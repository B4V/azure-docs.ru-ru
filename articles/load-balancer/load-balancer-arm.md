---
title: Поддержка Azure Resource Manager для балансировщика нагрузки | Документация Майкрософт
description: Использование PowerShell для работы с подсистемой балансировки нагрузки в Azure Resource Manager. Использование шаблонов для подсистемы балансировки нагрузки
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: tysonn
ms.assetid: d0394f11-ee5a-4407-9d86-79c936297265
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 43db9db3842d05fa13c3be92df14b905d2ddfc17
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2018
ms.locfileid: "42142919"
---
# <a name="using-azure-resource-manager-support-with-azure-load-balancer"></a>Использование поддержки Azure Resource Manager с Azure Load Balancer



Azure Resource Manager представляет собой предпочтительную платформу управления для служб в Azure. Теперь Azure Load Balancer можно управлять с помощью интерфейсов API и инструментов на основе Azure Resource Manager.

## <a name="concepts"></a>Основные понятия

Azure Load Balancer с Resource Manager содержит следующие дочерние ресурсы.

* Интерфейсная IP-конфигурация: подсистема балансировки нагрузки может включать в себя один или несколько внешних IP-адресов, которые также называются виртуальными IP-адресами. Такие IP-адреса обеспечивают поступление трафика.
* Пул внутренних адресов: IP-адреса, связанные с сетевыми картами виртуальных машин, между которыми распределяется нагрузка.
* Правила балансировки нагрузки: свойство правила сопоставляет указанное сочетание внешнего IP-адреса и порта с набором серверных IP-адресов и портов. Отдельный балансировщик нагрузки может применять несколько правил балансировки нагрузки. Каждое правило представляет собой сочетание интерфейсных IP-адреса и порта и внутренних IP-адреса и порта, связанных с виртуальными машинами.
* Пробы: пробы позволяют отслеживать работоспособность экземпляров виртуальных машин. В случае сбоя пробы работоспособности экземпляр виртуальной машины автоматически перестает использоваться.
* Правила NAT для входящего трафика: правила NAT, определяющие входящий трафик, который проходит через внешний IP-адрес и передается серверному IP-адресу.

![](./media/load-balancer-arm/load-balancer-arm.png)

## <a name="quickstart-templates"></a>Шаблоны быстрого запуска

Диспетчер ресурсов Azure позволяет подготавливать приложения с помощью декларативного шаблона. В одном шаблоне можно развернуть несколько служб и их зависимые компоненты. Один шаблон используется для развертывания приложения на каждом этапе его жизненного цикла.

Шаблоны могут содержать определения виртуальных машин, виртуальных сетей, групп доступности, сетевых интерфейсов (сетевых карт), учетных записей хранения, балансировщиков нагрузки, групп безопасности сети и общедоступных IP-адресов. С помощью шаблонов можно создать все необходимое для сложного приложения. Файл шаблона можно вернуть в систему управления содержимым, чтобы обеспечить управление версиями и возможность совместной работы.

[Дополнительные сведения о шаблонах](../azure-resource-manager/resource-manager-template-walkthrough.md)

[Дополнительные сведения о сетевых ресурсах](../networking/networking-overview.md)

Шаблоны быстрого запуска, использующие Azure Load Balancer, доступны в [этом репозитории GitHub](https://github.com/Azure/azure-quickstart-templates). В нем размещен набор шаблонов, созданных сообществом.

Примеры шаблонов:

* [2 ВМ в подсистеме балансировки нагрузки и правила балансировки нагрузки](http://go.microsoft.com/fwlink/?LinkId=544799)
* [2 ВМ в виртуальной сети с внутренней подсистемой балансировки нагрузки и правилами балансировки нагрузки](http://go.microsoft.com/fwlink/?LinkId=544800)
* [2 ВМ в подсистеме балансировки нагрузки и правила преобразования сетевых адресов для балансировки нагрузки](http://go.microsoft.com/fwlink/?LinkId=544801)

## <a name="setting-up-azure-load-balancer-with-a-powershell-or-cli"></a>Настройка балансировки нагрузки Azure с помощью PowerShell или интерфейса командной строки

Приступая к работе с командлетами, программами командной строки и интерфейсами REST API на основе Azure Resource Manager

* [Сетевые командлеты Azure](https://docs.microsoft.com/powershell/module/azurerm.network#networking) можно использовать для создания подсистемы балансировки нагрузки.
* [Создание подсистемы балансировки нагрузки с помощью диспетчера ресурсов Azure](load-balancer-get-started-ilb-arm-ps.md)
* [Использование Azure CLI с диспетчером ресурсов Azure (ARM)](../xplat-cli-azure-resource-manager.md)
* [Интерфейсы API REST подсистемы балансировки нагрузки](https://msdn.microsoft.com/library/azure/mt163651.aspx)

## <a name="next-steps"></a>Дополнительная информация

[Создайте доступную из Интернета подсистему балансировки нагрузки](load-balancer-get-started-internet-arm-ps.md) и настройте тип [режима распределения](load-balancer-distribution-mode.md) для определенного поведения сетевого трафика балансировщика нагрузки.

Узнайте, как управлять [временем ожидания простоя TCP для балансировщика нагрузки](load-balancer-tcp-idle-timeout.md). Это важно в тех случаях, когда приложению требуется проверять активность подключений к серверам, управляемым балансировщиком нагрузки.
