---
title: Управление протоколами и шифрами в службе управления API Azure | Документация Майкрософт
description: Сведения об управлении протоколами (TLS, SSL) и шифрами (DES) в службе управления API Azure.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 5d18b0265d2b1c114ed9ef326241ed531e015288
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2018
ms.locfileid: "49385145"
---
# <a name="manage-protocols-and-ciphers-in-azure-api-management"></a>Управление протоколами и шифрами в службе управления API Azure

Служба управления API Azure поддерживает несколько версий протокола TLS для сторон клиента и сервера, а также несколько версий шифра 3DES.

В этом руководстве показано, как управлять конфигурациями протоколов и шифров для экземпляра службы управления API Azure.

![Управление протоколами и шифрами в APIM](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers.png)

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить шаги в этой статье, необходимо иметь следующее:

* Экземпляр управления API.

## <a name="how-to-manage-tls-protocols-and-3des-cipher"></a>Как управлять протоколами TLS и шифрами 3DES

1. Перейдите к **экземпляру службы управления API** на портале Azure.
2. В меню выберите **SSL**.  
    ![Управление протоколами и шифрами в APIM — меню](./media/api-management-howto-manage-protocols-ciphers/api-management-menu.png)
3. Включите или отключите нужные протоколы или шифры.
4. Выберите команду **Сохранить**. Изменения будут применены в течение часа.  
    ![Управление протоколами и шифрами в APIM — команда "Сохранить"](./media/api-management-howto-manage-protocols-ciphers/api-management-protocols-ciphers-save.png)

## <a name="next-steps"></a>Дополнительная информация

* Дополнительные сведения о [протоколе TLS](https://docs.microsoft.com/dotnet/framework/network-programming/tls).
* См. другие [видео](https://azure.microsoft.com/documentation/videos/index/?services=api-management) об управлении API.