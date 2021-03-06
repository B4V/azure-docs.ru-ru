---
title: Анализ событий экземпляра установки Azure Digital Twins | Документация Майкрософт
description: В этом руководстве вы узнаете, как визуализировать и анализировать события из пространств Azure Digital Twins с помощью Аналитики временных рядов Azure.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 1366cafe5d2c526e86905c108b9c7b865aac8f69
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323466"
---
# <a name="tutorial-visualize-and-analyze-events-from-your-azure-digital-twins-spaces-using-time-series-insights"></a>Руководство по визуализации и анализу событий пространств Azure Digital Twins с помощью Аналитики временных рядов

После развертывания экземпляра Azure Digital Twins, подготовки пространств и реализации пользовательской функции для мониторинга определенных условий вы можете визуализировать события и данные, поступающие из ваших пространств, чтобы найти тенденции и аномалии. 

В [первом руководстве](tutorial-facilities-setup.md) вы настроили пространственный граф воображаемого здания с комнатой, где присутствуют датчики движения, углекислого газа и температуры. Во [втором руководстве](tutorial-facilities-udf.md) вы подготовили граф и определяемую пользователем функцию. Эта функция отслеживает значения датчиков и активирует уведомления для правильных условий, таких как пустое помещение, нормальные уровни температуры и углекислого газа. В этом руководстве показано, как интегрировать уведомления и данные, поступающие из экземпляра Digital Twins, используя Аналитику временных рядов Azure. Затем вы можете визуализировать значения датчиков за период времени и определить такие тенденции: какая комната чаще всего используется, какое время дня самое загруженное и т. д. Кроме того, можно обнаружить такие аномалии: какие комнаты кажутся более душными и жаркими или присутствие в здании области, которая постоянно отправляет высокие значения температуры, что указывает на неисправное кондиционирование воздуха.

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Потоковая передача данных с помощью Центров событий
> * Анализ с помощью Аналитики временных рядов

## <a name="prerequisites"></a>Предварительные требования

В этом руководстве предполагается, что вы [настроили](tutorial-facilities-setup.md) и [подготовили](tutorial-facilities-udf.md) экземпляр Azure Digital Twins. Прежде чем продолжить, убедитесь, что у вас есть:
- [Учетная запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Работающий экземпляр Digital Twins.
- [Примеры Digital Twins на C#](https://github.com/Azure-Samples/digital-twins-samples-csharp), скачанные и извлеченные на ваш рабочий компьютер.
- [Пакет SDK для .NET Core версии 2.1.403 или более поздней](https://www.microsoft.com/net/download) на компьютере разработки. Он нужен для запуска примера. Выполните команду `dotnet --version`, чтобы проверить, установлена ли необходимая версия. 


## <a name="stream-data-using-event-hubs"></a>Потоковая передача данных с помощью Центров событий
Служба [Центры событий](../event-hubs/event-hubs-about.md) позволяет создать конвейер для потоковой передачи данных. В этом разделе показано, как создать концентратор событий в качестве соединителя между экземплярами Digital Twins и TSI.

### <a name="create-an-event-hub"></a>Создание концентратора событий

1. Войдите на [портал Azure](https://portal.azure.com).

1. В области навигации слева щелкните **Создать ресурс**. 

1. Найдите и выберите **Центры событий**. Нажмите кнопку **Создать**.

1. Введите **имя** для пространства имен Центров событий, выберите **ценовую категорию** *Стандартный*, укажите **подписку**, **группу ресурсов**, используемую экземпляром Digital Twins, и **расположение**. Нажмите кнопку **Создать**. 

1. По завершении перейдите к *развертыванию* пространства имен Центров событий и выберите пространство имен в разделе **Ресурс**.

    ![пространство имен концентратора событий;](./media/tutorial-facilities-analyze/open-event-hub-ns.png)


1. В области **Обзор** пространства имен Центров событий нажмите кнопку **Концентратор событий** вверху. 
    ![Концентратор событий](./media/tutorial-facilities-analyze/create-event-hub.png)

1. Введите **имя** концентратора событий, а затем щелкните **Создать**. Развернутый концентратор событий будет отображаться в области **Центры событий** пространства имен Центров событий с **состоянием** *Активно*. Щелкните этот концентратор событий, чтобы открыть область **Обзор**.

1. Нажмите кнопку **Группа потребителей** вверху и введите имя для группы потребителей, например *tsievents*. Нажмите кнопку **Создать**.
    ![Группа потребителей концентратора событий](./media/tutorial-facilities-analyze/event-hub-consumer-group.png)

   Созданная группа потребителей отобразится в списке в нижней части панели **Обзор** концентратора событий. 

1. Откройте область **Политика общего доступа** концентратора событий и нажмите кнопку **Добавить**. **Создайте** политику с именем *ManageSend*. Убедитесь, что все флажки установлены. 

    ![Строки подключения концентратора событий](./media/tutorial-facilities-analyze/event-hub-connection-strings.png)

1. Откройте созданную политику *ManageSend* и скопируйте значения **Строка подключения — первичный ключ** и **Строка подключения — вторичный ключ** во временный файл. Эти значения потребуются, чтобы создать конечную точку для концентратора событий в следующем разделе.

### <a name="create-endpoint-for-the-event-hub"></a>Создание конечной точки для концентратора событий

1. В окне командной строки убедитесь, что вы находитесь в папке **_occupancy quickstart\src** примера Digital Twins.

1. Откройте файл **_actions\createEndpoints.yaml_** в редакторе. Замените содержимое этого файла следующим:

    ```yaml
    - type: EventHub
      eventTypes:
      - SensorChange
      - SpaceChange
      - TopologyOperation
      - UdfCustom
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hubs_namespace
    - type: EventHub
      eventTypes:
      - DeviceMessage
      connectionString: Primary_connection_string_for_your_event_hub
      secondaryConnectionString: Secondary_connection_string_for_your_event_hub
      path: Name_of_your_Event_Hubs_namespace
    ```

1. Замените заполнители `Primary_connection_string_for_your_event_hub` значением **Строка подключения — первичный ключ** концентратора событий. Убедитесь, что формат строки подключения выглядит следующим образом:
```
Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey1GUID;EntityPath=nameOfYourEventHub
```

1. Замените заполнители `Secondary_connection_string_for_your_event_hub` значением **Строка подключения — вторичный ключ** концентратора событий. Убедитесь, что формат строки подключения выглядит следующим образом: 
```
Endpoint=sb://nameOfYourEventHubNamespace.servicebus.windows.net/;SharedAccessKeyName=ManageSend;SharedAccessKey=yourShareAccessKey2GUID;EntityPath=nameOfYourEventHub
```

1. Замените заполнители `Name_of_your_Event_Hubs_namespace` именем пространства имен Центров событий.

    > [!IMPORTANT]
    > Введите все значения без кавычек. Убедитесь, что в файле *YAML* после двоеточия есть по крайней мере один пробел. Вы также можете проверить содержимое файла *YAML* с помощью любого средства проверки YAML в Интернете, например [этого](https://onlineyamltools.com/validate-yaml).


1. Сохраните и закройте файл. Выполните следующую команду в окне командной строки и при появлении запроса войдите с помощью учетной записи Azure.

    ```cmd/sh
    dotnet run CreateEndpoints
    ```
   
   Эта команда создает две конечные точки для концентратора событий.

   ![Конечные точки для Центров событий](./media/tutorial-facilities-analyze/dotnet-create-endpoints.png)

## <a name="analyze-with-time-series-insights"></a>Анализ с помощью Аналитики временных рядов

1. В левой области навигации [портала Azure](https://portal.azure.com) щелкните **Создать ресурс**. 

1. Найдите новый ресурс **Аналитика временных рядов** и выберите его. Нажмите кнопку **Создать**.

1. Введите **имя** экземпляра Аналитики временных рядов, а затем выберите свою **подписку**. Выберите **группу ресурсов**, которую вы использовали для экземпляра Digital Twins, и укажите **расположение**. Нажмите кнопку **Создать**.

    ![Создание TSI](./media/tutorial-facilities-analyze/create-tsi.png)

1. Откройте развернутую среду Аналитики временных рядов, а затем откройте область **Источники событий**. Нажмите кнопку **Добавить** вверху, чтобы добавить группу потребителей.

1. В области **New event source** (Новый источник событий) введите **имя** и убедитесь, что другие значения выбраны правильно. Выберите *ManageSend* в качестве **имени политики концентратора событий**, а затем в качестве **группы потребителей концентратора событий** выберите *ту*, которую создали в предыдущем разделе. Нажмите кнопку **Создать**.

    ![Источник событий TSI](./media/tutorial-facilities-analyze/tsi-event-source.png)

1. Откройте область **Обзор** среды Аналитики временных рядов и нажмите кнопку **Перейти к среде** вверху. Если вы получаете *предупреждение о доступе к данным*, откройте область **Data Access Policies** (Политики доступа к данным) для экземпляра TSI, щелкните **Добавить**, выберите **Участник** в качестве роли и выберите соответствующего пользователя.

1. В результате нажатия кнопки **Перейти к среде** откроется [обозреватель службы "Аналитика временных рядов"](../time-series-insights/time-series-insights-explorer.md). Если никакие события не отображаются, перейдите к проекту **_device-connectivity_** примера Digital Twins и запустите `dotnet run`, чтобы имитировать события устройства.

1. После создания нескольких имитированных событий вернитесь к обозревателю службы "Аналитика временных рядов" и нажмите кнопку "Обновить" вверху. Вы увидите, как для имитированных данных датчиков создаются аналитические диаграммы. 

    ![Обозреватель TSI](./media/tutorial-facilities-analyze/tsi-explorer.png)

1. В обозревателе Аналитики временных рядов можно создавать диаграммы и тепловые карты различных событий и данных комнат, датчиков и других ресурсов. В левой части щелкните раскрывающиеся списки с названиями **Мера** и **Разделение по**, чтобы создать собственные визуализации. Например, выберите *События* как значение **меры** и *DigitalTwins-SensorHardwareId* как значение параметра **Разделение по**, чтобы создать тепловую карту для каждого датчика, как показано на следующем изображении:

    ![Обозреватель TSI](./media/tutorial-facilities-analyze/tsi-explorer-heatmap.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Если на этом этапе вы хотите прекратить изучение Azure Digital Twins, можно удалить ресурсы, созданные в этом руководстве:

1. На [портале Azure](http://portal.azure.com) в меню слева щелкните **Все ресурсы**, выберите группу ресурсов Digital Twins и **удалите** ее.
2. Если необходимо, можно также удалить примеры приложений на рабочем компьютере. 


## <a name="next-steps"></a>Дополнительная информация

Перейдите к следующей статье, чтобы получить дополнительные сведения о пространственных интеллектуальных графах и моделях объектов в Azure Digital Twins. 
> [!div class="nextstepaction"]
> [Understanding Digital Twins object models and spatial intelligence graph](concepts-objectmodel-spatialgraph.md) (Основные сведения об объектных моделях и пространственном интеллектуальном графе в Digital Twins)

