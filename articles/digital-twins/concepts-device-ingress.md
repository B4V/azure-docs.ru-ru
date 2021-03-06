---
title: Возможность подключения устройств с помощью Azure Digital Twins и входящие данные телеметрии | Документация Майкрософт
description: Общие сведения о подключении устройства с помощью Azure Digital Twins
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 61d05a7e9936a0edd17c5528ce4f55233b6e7e0e
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323927"
---
# <a name="device-connectivity-and-telemetry-ingress"></a>Возможность подключения устройств и входящие данные телеметрии

Данные телеметрии, отправленные устройствами и датчиками, составляют основу любого решения Интернета вещей. Таким образом, представление этих разрозненных ресурсов и управление ими в контексте расположения является главной проблемой в разработке приложений Интернета вещей. Azure Digital Twins упрощает разработку решений Интернета вещей, объединяя устройства и датчики с пространственным интеллектуальным графом.

Для начала в корневой части пространственного графа необходимо создать ресурс `IoTHub`, позволяющий всем устройствам под корневым пространством отправлять сообщения. После создания Центра Интернета вещей и регистрации устройств с датчиками в экземпляре Digital Twins устройства могут начать отправку данных в службу Digital Twins с помощью [пакета SDK для устройств Azure IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-device-sdks).

Пошаговые инструкции по подключению устройств см. в статье [Tutorial: Deploy Azure Digital Twins and configure a spatial graph](tutorial-facilities-setup.md) (Руководство. Развертывание Azure Digital Twins и настройка пространственного графа). Вкратце можно выделить следующие шаги:

- Развертывание экземпляра Azure Digital Twins с [портала Azure](https://portal.azure.com).
- Создание пространств в графе.
- Создание ресурса `IoTHub` и назначение его пространству в графе.
- Создание устройств и датчиков в графе и назначение их пространствам, созданным ранее.
- Создание сопоставителя для фильтрации сообщений телеметрии на основе условий.
- Создание [**определяемой пользователем функции**](concepts-user-defined-functions.md) и назначение ее пространству в графе для пользовательской обработки сообщений телеметрии.
- Назначение роли, позволяющей определяемой пользователем функции получить доступ к данным графа.
- Получение строки подключения устройства Центра Интернета вещей из API управления Digital Twins.
- Настройка строки подключения на устройстве с помощью пакета SDK для устройств Azure IoT.

Ниже приведены сведения о получении строки подключения устройства Центра Интернета вещей из API управления Digital Twins, а также об использовании формата сообщения телеметрии Центра Интернета вещей для отправки данных телеметрии на основе датчиков. Служба Digital Twins требует, чтобы каждая часть данных телеметрии, которые она получает, была связана с датчиком внутри пространственного графа. Благодаря этому обеспечивается обработка и маршрутизация данных в соответствующем пространственном контексте.

## <a name="get-the-iot-hub-device-connection-string-from-the-management-api"></a>Получение строки подключения устройства Центра Интернета вещей из API управления

Выполните вызов GET в API устройства с использованием параметра `includes=ConnectionString`, чтобы получить строку подключения устройства Центра Интернета вещей. Вы можете отфильтровать строку по GUID или идентификатору оборудования, чтобы найти необходимое устройство:

```plaintext
https://yourManagementApiUrl/api/v1.0/devices/yourDeviceGuid?includes=ConnectionString
```

```plaintext
https://yourManagementApiUrl/api/v1.0/devices?hardwareIds=yourDeviceHardwareId&includes=ConnectionString
```

| Имя настраиваемого атрибута | Заменить на |
| --- | --- |
| `yourManagementApiUrl` | Полный URL-путь для вашего API управления |
| `yourDeviceGuid` | идентификатор устройства; |
| `yourDeviceHardwareId` | Идентификатор оборудования устройства |

В полезных данных ответа скопируйте свойство устройства `connectionString`, которое вы будете использовать при вызове пакета SDK для устройств Azure IoT для отправки данных в Azure Digital Twins.

## <a name="device-to-cloud-message"></a>Отправка сообщений с устройства в облако

Вы можете настроить формат сообщений и полезные данные устройства в соответствии с потребностями вашего решения. Вы можете использовать любой контракт данных, сериализованный в массив или поток байтов, который поддерживается [классом сообщений клиента устройств Azure IoT, Message(byte[] byteArray)](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.-ctor?view=azure-dotnet#Microsoft_Azure_Devices_Client_Message__ctor_System_Byte___). Сообщение может иметь пользовательский двоичный формат, пока вы декодируете контракт данных в соответствующей определяемой пользователем функции. Единственное требование для сообщения, отправляемого с устройства в облако, — поддерживать набор свойств (см. далее), чтобы обеспечить соответствующую маршрутизацию сообщения в механизм обработки.

### <a name="telemetry-properties"></a>Свойства телеметрии

Хотя содержимое полезных данных `Message` может состоять из произвольных данных размером до 256 КБ, к ожидаемому свойству [Message.Properties](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.message.properties?view=azure-dotnet) применяется несколько требований. Представленные ниже шаги отражают требуемые и дополнительные свойства, поддерживаемые системой:

| Имя свойства | Значение | Обязательно | ОПИСАНИЕ |
|---|---|---|---|
| DigitalTwins-Telemetry | 1.0 | Да | Постоянное значение, которое идентифицирует сообщение в системе |
| DigitalTwins-SensorHardwareId | `string(72)` | Да | Уникальный идентификатор датчика, отправляющего `Message`. Это значение должно соответствовать свойству `HardwareId` объекта, чтобы система могла его обработать. Например, `00FF0643BE88-CO2` |
| CreationTimeUtc | `string` | Нет | Строка даты в формате [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601), идентифицирующая время выборки полезных данных. Например, `2018-09-20T07:35:00.8587882-07:00` |
| CorrelationId | `string` | Нет | `uuid`, который может использоваться для трассировки событий в системе. Например, `cec16751-ab27-405d-8fe6-c68e1412ce1f`

### <a name="sending-your-message-to-digital-twins"></a>Отправка сообщения в Digital Twins

Для отправки сообщения в службу Digital Twins используйте вызов DeviceClient [SendEventAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventasync?view=azure-dotnet) или [SendEventBatchAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.deviceclient.sendeventbatchasync?view=azure-dotnet).

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о возможностях обработки данных Azure Digital Twins и определяемых пользователем функциях см. в статье [Data processing and user-defined functions](concepts-user-defined-functions.md) (Обработка данных и определяемые пользователем функции).

