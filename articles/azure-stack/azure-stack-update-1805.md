---
title: Обновление 1805 для Azure Stack | Документация Майкрософт
description: Узнайте, что нового в обновлении 1805 для интегрированных систем Azure Stack, о каких проблемах известно и где можно скачать обновление.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: sethm
ms.reviewer: justini
ms.openlocfilehash: b6cb095736bbf41d53359bf7d07206703de5335c
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48870012"
---
# <a name="azure-stack-1805-update"></a>Обновление 1805 для Azure Stack

*Область применения: интегрированные системы Azure Stack*

В этой статье представлены улучшения и исправления в пакете обновления 1805, известные проблемы версии и сведения о том, где можно скачать обновление. Известные проблемы можно разделить на проблемы, которые непосредственно относятся к процессу обновления и проблемы со сборкой (после установки).

> [!IMPORTANT]        
> Этот пакет обновления предназначен только для интегрированных систем Azure Stack. Не применяйте этот пакет обновления к Пакету средств разработки Azure Stack.

## <a name="build-reference"></a>Указание сборки    
Номер сборки обновления 1805 для Azure Stack — **1.1805.1.47**.  

> [!TIP]  
> На основе отзывов клиентов обновлена схема версии, используемая в Microsoft Azure Stack.  Начиная с этого обновления, 1805, новая схема лучше представляет текущую версию облака.  
> 
> Теперь схема версии — *Version.YearYearMonthMonth.MinorVersion.BuildNumber*, в которой второй и третий набор представляют собой версию и выпуск. Например, 1805.1 представляет собой *окончательную первоначальную версию* (RTM) 1805.  


### <a name="new-features"></a>новые функции;
Это обновление включает в себя следующие улучшения и исправления для Azure Stack.
<!-- 2297790 - IS, ASDK --> 
- **Теперь Azure Stack включает клиент *Syslog* в** качестве *предварительной версии функции*. Этот клиент позволяет переадресовывать журналы аудита и безопасности, связанные с инфраструктурой Azure Stack, на сервер Syslog или в программное обеспечение управления информационной безопасностью и событиями безопасности (SIEM), внешнее по отношению к Azure Stack. В настоящее время клиент Syslog поддерживает только подключения без проверки подлинности по протоколу UDP через порт 514 по умолчанию. Полезные данные каждого сообщения Syslog представлены в общем формате событий (CEF). 

  Чтобы настроить клиент Syslog, воспользуйтесь командлетом **Set-SyslogServer**, представленным в привилегированной конечной точке. 

  В этой предварительной версии можно увидеть следующие три предупреждения. При представлении в Azure Stack эти предупреждения включают *описания* и руководство по *исправлению*. 
  - Заголовок. Целостность кода отключена  
  - Заголовок.Целостность кода в режиме аудита 
  - Заголовок. Создана учетная запись пользователя

  Пока эта функция находится в режиме предварительной версии, не стоит использовать ее в рабочих средах.   




### <a name="fixed-issues"></a>Исправленные проблемы

<!-- # - applicability -->
- Мы исправили проблему, не позволяющую [открыть новый запрос в службу поддержки из раскрывающегося списка](azure-stack-manage-portals.md#quick-access-to-help-and-support) на портале администрирования. Сейчас этот параметр работает должным образом. 

- **Различные исправления** производительности, стабильности, безопасности и операционной системы, используемой службой Azure Stack.


<!-- # Additional releases timed with this update    -->



## <a name="before-you-begin"></a>Перед началом работы    

### <a name="prerequisites"></a>Предварительные требования
- Прежде чем применить обновление 1805 для Azure Stack, установите пакет обновления 1804 для Azure Stack.  
- Установите последнюю доступную версию обновления или исправления для версии 1804.   
- Перед началом установки обновления 1805 запустите [Test-AzureStack](azure-stack-diagnostic-test.md) для проверки состояния Azure Stack и устраните все найденные операционные проблемы. Кроме того, просмотрите активные предупреждения и решите проблемы с теми, которые требуют действия. 

### <a name="known-issues-with-the-update-process"></a>Известные проблемы с процессом обновления   
- Во время установки обновления 1805 могут отображаться оповещения с заголовком *Error – Template for FaultType UserAccounts.New is missing* (Ошибка: отсутствует шаблон для FaultType UserAccounts.New).  Эти оповещения можно игнорировать. Они будут закрыты автоматически после завершения обновления до версии 1805.   

<!-- 2489559 - IS --> 
- Не пытайтесь создавать виртуальные машины во время установки этого обновления. Дополнительные сведения об управлении обновлениями в Azure Stack см. в [этой статье](azure-stack-updates.md#plan-for-updates).


### <a name="post-update-steps"></a>Действия после обновления
Когда обновление 1805 установится, установите все применимые исправления. Дополнительные сведения см. в статьях базы знаний по ссылке ниже, а также в статье о нашей [политике обслуживания](azure-stack-servicing-policy.md).  
 - [KB 4344102 — Исправление Azure Stack 1.1805.7.57](https://support.microsoft.com/help/4344102).


## <a name="known-issues-post-installation"></a>Известные проблемы (после установки)
Ниже перечислены известные проблемы после установки этой версии сборки.

### <a name="portal"></a>Microsoft Azure  

- Техническая документация Azure Stack посвящена последнему выпуску. Из-за изменений на портале между выпусками отображаемое содержимое при использовании порталов Azure Stack может отличаться от содержимого в документации. 

<!-- 2931230 – IS  ASDK --> 
- Планы, добавленные в подписку пользователя как дополнительные, невозможно удалить даже при удалении основного плана из подписки. Дополнительный план будет оставаться в подписке, пока подписки, которые ссылаются на него, не будут удалены. 

<!-- TBD - IS ASDK --> 
- С этой версией Azure Stack невозможно установить обновления драйверов с помощью пакета расширения OEM.  Для этой проблемы нет решения.

<!-- 2551834 - IS, ASDK --> 
- При выборе **Обзора** учетной записи на порталах администратора или пользователя, сведения области *"Основные компоненты"* не отображаются.  В области "Основные компоненты" отображаются сведения об учетной записи, такие как ее *группа ресурсов*, *расположение* и *идентификатор подписки*.  Кроме того, доступны другие параметры обзора, например *Службы* и *Мониторинг*, а также *Открыть в обозревателе* или *Удаление учетной записи хранения*. 

  Чтобы просмотреть недоступные сведения, используйте командлет [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0) PowerShell. 

<!-- 2551834 - IS, ASDK --> 
- При выборе **Тегов** учетной записи на порталах администратора или пользователя, сведения не загружаются и на отображаются.  

  Чтобы просмотреть недоступные сведения, используйте командлет [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0) PowerShell.


<!-- 2332636 - IS -->  
- При использовании AD FS для системы идентификации Azure Stack и обновлении до этой версии Azure Stack владелец подписки по умолчанию сбрасывается до встроенного пользователя **CloudAdmin**.  
  Решение. Чтобы устранить эту проблему после установки этого обновления, выполните шаг 3 из процедуры [Активация службы автоматизации для настройки отношений доверия с поставщиком утверждений в Azure Stack](azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-1), чтобы сбросить владельца подписки поставщика по умолчанию.   

<!-- TBD - IS ASDK --> 
- Некоторые типы административных подписок недоступны. При обновлении Azure Stack до этой версии два типа подписки, введенные с версией 1804, не отображаются в консоли. Это ожидаемое поведение. Доступные типы подписки: *Подписка для учета* и *Подписка для контроля потребления*. Эти типы подписок еще не готовы к использованию, но их уже можно увидеть в новых средах Azure Stack, начиная с версии 1804. Продолжайте использовать тип подписки *Поставщик по умолчанию*.  

<!-- 2403291 - IS ASDK --> 
- Возможно, вы не используете горизонтальную полосу прокрутки в нижней части административного и пользовательских порталов. Если не удается получить доступ к горизонтальной полосе прокрутки, с помощью строки навигации перейдите в предыдущую колонку портала, выбрав имя колонки, которую нужно просмотреть, в списке навигации в верхней левой части портала.
  ![Элемент навигации](media/azure-stack-update-1804/breadcrumb.png)

<!-- TBD - IS --> 
- Просмотр вычислений или ресурсов хранилища на портале администрирования может оказаться невозможным. Причиной является ошибка, которая произошла во время установки обновления. Из-за нее об обновлении было неверно сообщено (как об успешном). При возникновении этой ошибки обратитесь за помощью в службу поддержки корпорации Майкрософт.

<!-- TBD - IS --> 
- На портале может появиться пустая панель мониторинга. Чтобы восстановить панель мониторинга, щелкните значок шестеренки в правом верхнем углу портала и выберите **Восстановление параметров по умолчанию**.

<!-- TBD - IS ASDK --> 
- Удаление подписки пользователя приводит к появлению потерянных ресурсов. Чтобы избежать этого, следует сначала удалить ресурсы пользователя или всю группу ресурсов и лишь затем удалять подписки пользователя.

<!-- TBD - IS ASDK --> 
- Вы не можете просматривать разрешения для подписки на порталах Azure Stack. Используйте PowerShell, чтобы проверить разрешения.


### <a name="health-and-monitoring"></a>Работоспособность и мониторинг
<!-- 1264761 - IS ASDK -->  
- Вы можете получать предупреждения от компонента *Health controller* (Контроллер работоспособности), которые содержат следующие сведения:  
 
   Предупреждение № 1:
   - НАЗВАНИЕ. Роль инфраструктуры неработоспособна.
   - СЕРЬЕЗНОСТЬ. Предупреждение.
   - КОМПОНЕНТ. Контроллер работоспособности.
   - ОПИСАНИЕ. Контроллер работоспособности сканера пульса недоступен. Это может повлиять на отчеты о работоспособности и метрики.  

  Предупреждение №2:
   - НАЗВАНИЕ. Роль инфраструктуры неработоспособна.
   - СЕРЬЕЗНОСТЬ. Предупреждение.
   - КОМПОНЕНТ. Контроллер работоспособности.
   - ОПИСАНИЕ. Контроллер работоспособности сканера ошибок недоступен. Это может повлиять на отчеты о работоспособности и метрики.

  Оба предупреждения 1 и 2 можно спокойно проигнорировать, и они автоматически закроются через некоторое время. 

  Также может появиться следующее предупреждение, касающееся функции *Емкость*. Для этого предупреждения процент доступной памяти, указанный в описании, может меняться.  

  Предупреждение №3
   - НАЗВАНИЕ. Недостаточный объем памяти.
   - СЕРЬЕЗНОСТЬ. Критическая
   - КОМПОНЕНТ. Емкость
   - ОПИСАНИЕ. Регион потребляет более 80,00 % доступной памяти. При создании виртуальных машин с большими объемами памяти может произойти сбой.  

  В этой версии Azure Stack это предупреждение может срабатывать неправильно. Это предупреждение можно игнорировать, если виртуальные машины клиента продолжают успешно развертываться. 
  
  Предупреждение № 3 не закрывается автоматически. Если закрыть это предупреждение, Azure Stack создаст такое же предупреждение в течение 15 минут.  

<!-- 2368581 - IS. ASDK --> 
- Если оператор Azure Stack получает предупреждение о недостатке памяти, а при развертывании виртуальных машин клиента происходит сбой с *ошибкой создания виртуальной машины со структурой Microsoft Azure*, вероятно, для метки Azure Stack недостаточно памяти. Используйте [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822), чтобы разобраться с емкостью, доступной для рабочих нагрузок. 


### <a name="compute"></a>Службы вычислений
<!-- TBD - IS, ASDK --> 
- При выборе размера развертываемой виртуальной машины некоторые размеры виртуальных машин серии F не отображаются в селекторе размера во время создания. Следующие размеры виртуальных машин не отображаются в селекторе: *F8s_v2*, *F16s_v2*, *F32s_v2* и *F64s_v2*.  
  Чтобы обойти эту проблему, используйте один из следующих методов для развертывания виртуальной машины. В каждом методе необходимо указать размер виртуальной машины, который вы хотите использовать.

  - **Шаблон Azure Resource Manager.** При использовании шаблона задайте в нем значение *vmSize*, равное требуемому размеру виртуальной машины. Например, приведенная ниже запись используется для развертывания виртуальной машины размера *F32s_v2*:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **Azure CLI:** можно использовать команду [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) и указать размер виртуальной машины в качестве параметра. Пример: `--size "Standard_F32s_v2"`.

  - **PowerShell:** в PowerShell можно использовать командлет [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0) с параметром, который указывает размер виртуальной машины. Пример: `-VMSize "Standard_F32s_v2"`.


<!-- TBD - IS ASDK --> 
- Параметры масштабирования для масштабируемых наборов виртуальной машины на портале недоступны. В качестве обходного решения используйте [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Из-за различий между версиями PowerShell используйте параметр `-Name` вместо параметра `-VMScaleSetName`.

<!-- TBD - IS --> 
- При создании группы доступности на портале (**Создать** > **Вычисления** > **Группа доступности**) вы можете создать ее только с одним доменом сбоя и одним доменом обновления. В качестве обходного решения при создании виртуальной машины создайте группу доступности с помощью PowerShell, CLI или на портале.

<!-- TBD - IS ASDK --> 
- При создании виртуальных машин на пользовательском портале Azure Stack отображается неверное число дисков данных, которые можно подключить к виртуальной машине серии DS. К виртуальным машинам серии DS можно подключить такое же количество дисков данных, которое доступно в конфигурации Azure.

<!-- TBD - IS ASDK --> 
- При сбое создания образа виртуальной машины неисправный элемент, который вы не смогли удалить, может быть добавлен в колонку вычислений образов виртуальной машины.

  В качестве обходного пути создайте образ виртуальной машины с пустым VHD. Его можно создать с помощью Hyper-V (New-VHD -Path C:\dummy.vhd -Fixed -SizeBytes 1 GB). Этот процесс должен устранить проблему, которая не дает удалить неисправный элемент. Затем через 15 минут после создания фиктивного образа вы сможете удалить его.

  После этого вы можете попытаться повторно загрузить образ виртуальной машины, который ранее загрузить не удалось.

<!-- TBD - IS ASDK --> 
- Если подготовка расширения для развертывания виртуальной машины занимает слишком много времени, следует дождаться истечения времени ожидания, а не пытаться остановить процесс освобождения или удаления виртуальной машины.  

<!-- 1662991 IS ASDK --> 
- Диагностика виртуальных машин Linux не поддерживается в Azure Stack. При развертывании виртуальной машины Linux с включенной диагностикой оно завершается со сбоем. Развертывание также не выполняется, если базовые метрики виртуальной машины Linux включены через параметры диагностики.  


### <a name="networking"></a>Сеть
<!-- TBD - IS ASDK --> 
- Вы не можете создать определяемые пользователем маршруты на административном или пользовательском портале. В качестве обходного решения используйте [Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell).

<!-- 1766332 - IS ASDK --> 
- Если в разделе **Сеть** выбрать **Create VPN Gateway** (Создание VPN-шлюза) для настройки VPN-подключения, то в качестве типа VPN будет указан параметр **На основе политики**. Не выбирайте это подключение. В Azure Stack поддерживается только параметр **Route Based** (На основе маршрутов).

<!-- 2388980 - IS ASDK --> 
- После создания виртуальной машины и ее связывания с общедоступным IP-адресом вы не можете отменить эту связь. Будет выглядеть так, будто вам удалось его отменить, но ранее присвоенный общедоступный IP-адрес останется связанным с исходной виртуальной машиной.

  Сейчас для создания виртуальной машины необходимо использовать только новые общедоступные IP-адреса.

  Это происходит, даже если присвоить этот IP-адрес новой виртуальной машине (это часто называют *переключением виртуального IP-адреса*). Все последующие попытки подключиться через этот IP-адрес к новой виртуальной машине приведут к подключению к исходной виртуальной машине.

<!-- 2292271 - IS ASDK --> 
- Если повысить квоту для ресурсов сети, которые являются частью предложения и плана, связанного с подпиской клиента, то новое ограничение не применяется к этой подписке. Тем не менее оно распространяется на новые подписки, созданные после увеличения квоты.

  Чтобы обойти эту проблему, используйте план надстройки для повышения квоты сети, если план уже связан с подпиской. Чтобы получить дополнительные сведения, узнайте, как [сделать доступным план надстройки](azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

<!-- 2304134 IS ASDK --> 
- Вы не можете удалить подписку, с которой связаны ресурсы зоны DNS или таблиц маршрутов. Чтобы успешно удалить подписку, необходимо сначала удалить из подписки клиента ресурсы зоны DNS и таблицы маршрутов.


<!-- 1902460 - IS ASDK --> 
- Azure Stack поддерживает один *локальный сетевой шлюз* на IP-адрес. Это относится ко всем подпискам клиентов. После создания первого подключения к локальному сетевому шлюзу последующие попытки создать ресурс локального сетевого шлюза с тем же IP-адресом блокируются.

<!-- 16309153 - IS ASDK --> 
- В виртуальной сети, созданной с параметром DNS-сервера *Автоматический*, попытка изменить на пользовательский DNS-сервер завершается ошибкой. Обновленные параметры не передаются на виртуальные машины в этой виртуальной сети.

<!-- TBD - IS ASDK --> 
- Azure Stack не поддерживает добавление дополнительных сетевых интерфейсов к экземпляру виртуальной машины после ее развертывания. Если для виртуальной машины требуется несколько сетевых интерфейсов, их нужно определить во время развертывания.

<!-- 2096388 IS --> 
- Вы не можете использовать портал администрирования для обновления правил для сетевой группы безопасности.

    Обходной путь для службы приложений. Если вам нужно подключение с помощью удаленного рабочего стола к экземплярам контроллера, измените правила безопасности в группах безопасности сети с помощью PowerShell.  Ниже приведены примеры того, как *разрешить* конфигурацию, а затем восстановить для нее состояние *запрета*:  

    - *Разрешить:*

      ```powershell    
      Connect-AzureRmAccount -EnvironmentName AzureStackAdmin

      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

      ##This doesn’t work. Need to set properties again even in case of edit

      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  

      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

    - *Запретить:*

        ```powershell

        Connect-AzureRmAccount -EnvironmentName AzureStackAdmin

        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

        ##This doesn’t work. Need to set properties again even in case of edit

        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  

        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
        ```


### <a name="sql-and-mysql"></a>SQL и MySQL

<!-- TBD - IS --> 
- Создание элементов на серверах размещения SQL и MySQL, если его выполняет не поставщик ресурсов, не поддерживается и может привести к несоответствию состояний.  

<!-- IS, ASDK --> 
- При создании номера SKU для поставщиков ресурсов SQL и MySQL в именах **семейства** и **уровня** не поддерживаются специальные знаки, включая пробелы и точки.

<!-- TBD - IS --> 
> [!NOTE]  
> После обновления до Azure Stack 1805 вы можете продолжить использование ранее развернутых поставщиков ресурсов SQL и MySQL.  Рекомендуем обновлять SQL и MySQL при выходе нового выпуска. Применяйте обновления для поставщиков ресурсов SQL и MySQL последовательно (как и для Azure Stack). Например, если вы используете версию 1803, сначала примените версию 1804, а затем — 1805.      
>   
> Установка обновления 1805 не влияет на текущее использование поставщиков ресурсов SQL или MySQL пользователями.
> Независимо от версии используемых поставщиков ресурсов, данные пользователей в базах данных не затрагиваются и остаются доступными.    



### <a name="app-service"></a>Служба приложений
<!-- 2352906 - IS ASDK --> 
- Прежде чем создавать первую функцию Azure в подписке, пользователь должен зарегистрировать поставщик ресурсов хранилища.

<!-- 2489178 - IS ASDK --> 
- Чтобы масштабировать инфраструктуру (рабочие роли, роли управления, внешние роли), используйте PowerShell, как описано в заметках о выпуске служб вычислений.

<!-- TBD - IS ASDK --> 
- Сейчас службу приложений Azure можно развернуть только в *подписке поставщика по умолчанию*. 


### <a name="usage"></a>Использование  
<!-- TBD - IS ASDK --> 
- Вместо метки *TimeDate*, которая показывает время создания записи, в данных инструмента для отслеживания использования общедоступного IP-адреса отображается одно значение *EventDateTime* для каждой записи. В настоящее время эти данные непригодны для выполнения точного расчета и получения данных об использовании общедоступного IP-адреса.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Скачивание обновления
Пакет обновления 1805 для Azure Stack можно скачать [здесь](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>См. также
- Сведения об использовании привилегированной конечной точки (PEP) для отслеживания и возобновления обновлений см. в статье [Мониторинг обновлений в Azure Stack с помощью привилегированной конечной точки](azure-stack-monitor-update.md).
- [Общие сведения об управлении обновлениями в Azure Stack](azure-stack-updates.md).
- [Применение обновлений в Azure Stack](azure-stack-apply-updates.md).
