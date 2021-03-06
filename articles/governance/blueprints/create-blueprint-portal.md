---
title: Создание схемы Azure на портале
description: Схемы Azure позволяют создавать, определять и развертывать артефакты.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: quickstart
ms.service: blueprints
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: 6b7ca276f3273faa485d08633061f882493f72f7
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647278"
---
# <a name="define-and-assign-an-azure-blueprint-in-the-portal"></a>Определение и назначение схемы Azure на портале

Понимание того, как создавать и назначать схемы в Azure, позволяет организации определять стандартные согласованные модели и разрабатывать быстро развертываемые конфигурации для повторного использования на основе шаблонов Resource Manager, политик, правил безопасности и других элементов. В этом руководстве вы научитесь использовать службу Azure Blueprint для выполнения некоторых общих задач, связанных с созданием, публикацией и назначением схемы в вашей организации, таких как:

> [!div class="checklist"]
> - создание схемы и добавление различных поддерживаемых артефактов;
> - внесение изменений в существующую схему на стадии **черновика**;
> - обозначение схемы как готовой к назначению с помощью **публикации**;
> - назначение схемы существующей подписке;
> - проверка состояния и хода выполнения назначенной схемы;
> - удаление схемы, назначенной подписке.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free), прежде чем начинать работу.

## <a name="create-a-blueprint"></a>Создание схемы

Первым этапом при определении стандартной модели соответствия требованиям является формирование схемы на основе доступных ресурсов. В этом примере вы создадите схему с именем MyBlueprint с целью настройки назначений ролей и политик для подписки, добавите группу ресурсов, а затем создадите для нее шаблон Resource Manager и назначение ролей в новой группе ресурсов.

1. Запустите службу Azure Blueprint на портале Azure. Для этого щелкните **Все службы**, а затем найдите и выберите пункт **Политика** в левой панели. На странице **Политика** нажмите кнопку **Схемы**.

1. Выберите **Определения схем** на странице слева и щелкните кнопку **+ Создать схему** в верхней части страницы.

   - Либо нажмите кнопку **Создать** на странице **Приступая к работе**, чтобы перейти непосредственно к созданию схемы.

   ![Создание схемы](./media/create-blueprint-portal/create-blueprint-button.png)

1. Укажите **Название схемы**, например "MyBlueprint" (буквы и цифры — до 48 знаков, но без пробелов и специальных символов) для проекта, но оставьте **описание схемы** пустым.  В поле **Расположение определения** нажмите кнопку с многоточием справа, выберите [группу управления](../management-groups/overview.md), где вы хотите сохранить проект, и нажмите кнопку **Выбрать**.

   > [!NOTE]
   > Определения схем можно сохранить только в группы управления. Чтобы создать свою первую группу управления, выполните [эти действия](../management-groups/create.md).

1. Убедитесь, что информация верна (поля **Название схемы** и **Расположение определения** нельзя будет изменить позже) и нажмите кнопку **Далее: артефакты** в нижней части страницы или вкладку **Артефакты** в верхней части страницы.

1. Добавьте назначение роли в подписке: щелкните левой кнопкой мыши на строке **+ Добавить артефакт..**  в группе **Подписки**, и в правой части браузера откроется окно "Добавление артефактов". Выберите "Назначение ролей" для _Типа артефакта_. В разделе _Роли_выберите "Участник" и оставьте в поле _Добавить пользователя, приложение или группу_ оставьте флажок, означающий **динамический параметр**. Нажмите кнопку **Добавить**, чтобы добавить артефакт в схему.

   ![Артефакт — назначение ролей](./media/create-blueprint-portal/add-role-assignment.png)

   > [!NOTE]
   > Большинство _артефактов_ поддерживают параметры. Параметр, которому присвоено значение во время создания схемы, является **статическим параметром**. Если параметр назначается во время назначения схемы, это **динамический параметр**. Дополнительные сведения см. в описании [параметров схемы](./concepts/parameters.md).

1. Добавьте назначение политики в подписке: щелкните левой кнопкой мыши строку **+ Добавить артефакт...**  непосредственно под разделом **Подписки**. Выберите "Назначение политик" для _Типа артефакта_. Измените _тип_ на "Встроенные" и в поле _Поиск_ введите "tag". Выйдите из поля _Поиск_, чтобы была выполнена фильтрация. Выберите поле "Применить тег и его значение по умолчанию группам ресурсов", щелкнув по нему. Нажмите кнопку **Добавить**, чтобы добавить артефакт в схему.

1. Щелкните строку назначения политики "Применить тег и его значение по умолчанию группам ресурсов". Откроется окно, где можно задать параметры артефакта как часть определения схемы. В нем можно задать параметры для всех назначений (**статические параметры**), основанные на этой схеме, а не во время назначения (**динамические параметры**). В этом примере требуется использовать **динамические параметры** во время назначения схемы, поэтому оставьте значения по умолчанию и нажмите кнопку **Отменить**.

1. Добавьте группу ресурсов в подписке: щелкните левой кнопкой мыши по строке **+ Добавить артефакт...** под разделом **Подписка**. Выберите "Группа ресурсов" для _Типа артефакта_. Оставьте поля _Имя группы ресурсов_ и _Расположение_ пустыми, но убедитесь, что установлен флажок для каждого свойства, чтобы сделать их **динамическими параметрами**. Нажмите кнопку **Добавить**, чтобы добавить артефакт в схему.

1. Добавьте шаблон в группе ресурсов: сделайте щелчок левой кнопкой мыши по строке **+ Добавить артефакт...** непосредственно под записью **ResourceGroup**. Выберите "Шаблон Azure Resource Manager" для _Типа артефакта_, задайте _Отображаемое имя артефакта_ как "Учетная запись хранения" и оставьте _Описание_ пустым. На вкладке **Шаблон** в окне редактора вставьте следующий шаблон Resource Manager. После вставки шаблона щелкните вкладку **Параметры** и обратите внимание, что параметр шаблона **storageAccountType** и значение по умолчанию **Standard_LRS** были автоматически обнаружены и заполнены, но настроены как **динамический параметр**. Снимите флажок и обратите внимание на то, что раскрывающийся список содержит только значения, включенные в шаблон Resource Manager в разделе **allowedValues**. Установите флажок, чтобы вернуть **динамический параметр**. Нажмите кнопку **Добавить**, чтобы добавить артефакт в схему.

   > [!IMPORTANT]
   > При импорте шаблона убедитесь, что файл формата JSON и не содержит HTML. При ссылке на URL-адрес на сайте GitHub убедитесь, что выбрана **RAW** для получения чистых JSON-файлов, а не HTML-оболочка для отображения на сайте GitHub. Если импортированный шаблон не формата JSON, произойдет ошибка.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "storageAccountType": {
               "type": "string",
               "defaultValue": "Standard_LRS",
               "allowedValues": [
                   "Standard_LRS",
                   "Standard_GRS",
                   "Standard_ZRS",
                   "Premium_LRS"
               ],
               "metadata": {
                   "description": "Storage Account type"
               }
           }
       },
       "variables": {
           "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
       },
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts",
           "name": "[variables('storageAccountName')]",
           "apiVersion": "2016-01-01",
           "location": "[resourceGroup().location]",
           "sku": {
               "name": "[parameters('storageAccountType')]"
           },
           "kind": "Storage",
           "properties": {}
       }],
       "outputs": {
           "storageAccountName": {
               "type": "string",
               "value": "[variables('storageAccountName')]"
           }
       }
   }
   ```

   ![Артефакт — шаблон Resource Manager](./media/create-blueprint-portal/add-resource-manager-template.png)

1. Завершенная схема должны выглядеть следующим образом. Обратите внимание, что у каждого артефакта указано, что "заполнено _x_ параметров из _y_" в столбце _Параметры_. **Динамические параметры** будут устанавливаться при каждом назначении схемы.

   ![Завершенная схема](./media/create-blueprint-portal/completed-blueprint.png)

1. Теперь, когда были добавлены все планируемые артефакты, нажмите кнопку **Сохранить черновик** в нижней части страницы.

## <a name="edit-a-blueprint"></a>Редактирование схемы

В [Создании схемы](#create-a-blueprint) не было указано описание, и назначение ролей не было добавлено в новую группу ресурсов. Это можно исправить следующими действиями.

1. Выберите **Определения схем** на странице слева.

1. В списке схем щелкните правой кнопкой мыши на созданный ранее и выберите **Изменение схемы**.

1. В **Описании схемы** укажите некоторые сведения о схеме и артефакты, составляющие ее.  В этом случае введите нечто вроде: "Эта схема задает политику использования тегов и назначение ролей при подписке, создает группу ресурсов и развертывает шаблон ресурсов и назначение ролей этой группе ресурсов".

1. Нажмите кнопку **Далее: артефакты** в нижней части страницы или вкладку **Артефакты** в верхней части страницы.

1. Добавьте назначение роли группе ресурсов: щелкните левой кнопкой мыши по строке **+ Добавить артефакт...** непосредственно под записью **ResourceGroup**. Выберите "Назначение ролей" для _Типа артефакта_. В разделе _Роль_ выберите "Владелец" и снимите флажок для поля _Добавить пользователя, приложения или группу_ и найдите и выберите пользователя, приложение или группу для добавления. Это будет **статический параметр**, и он будет использоваться в каждом назначении в рамках этой схемы. Нажмите кнопку **Добавить**, чтобы добавить артефакт в схему.

   ![Артефакт — назначение ролей #2](./media/create-blueprint-portal/add-role-assignment-2.png)

1. Завершенная схема должны выглядеть следующим образом. Обратите внимание, что добавленное назначение ролей показывает **заполнено 1 из 1 параметров**, что значит, что это **статический параметр**.

   ![Завершенная схема #2](./media/create-blueprint-portal/completed-blueprint-2.png)

1. Нажмите кнопку **Сохранить черновик** после обновления.

## <a name="publish-a-blueprint"></a>Публикация схемы

Теперь, когда артефакты добавлены в схему, пора ее опубликовать.
Публикация схемы позволяет назначать ее подписке.

1. Выберите **Определения схем** на странице слева.

1. В списке схем выберите созданную вами ранее и выберите **Публиковать схему**.

1. В открывшемся диалоговом окне укажите **Версию** (буквы, цифры и дефисы с максимальной длиной 20 символов.) например "v1" и **Измените заметку** (необязательно), например "Первая публикация".

1. Щелкните **Публиковать** в нижней части страницы.

## <a name="assign-a-blueprint"></a>Назначение схемы

После публикации схемы ее можно назначить подписке. Назначьте созданную схему одной из подписок в иерархии группы управления.

1. Выберите **Определения схем** на странице слева.

1. В списке схем выберите созданную вами ранее (правым щелчком мыши) или щелкните левой кнопкой мыши по многоточию и выберите **Публиковать схему**.

1. На странице **Назначить схему** выберите подписки, на которые необходимо развернуть эту схему из раскрывающегося списка **Подписки**.

   > [!NOTE]
   > Назначения будут создаваться для каждой выбранной подписки, позволяя изменять назначения одной подписки в будущем без необходимости вносить изменения во все выбранные подписки.

1. Для **Присвоенного имени** укажите уникальное имя для этого назначения.

1. В **Расположении** выберите регион для управляемого удостоверения, в котором необходимо создать назначение. Azure Blueprint использует это управляемое удостоверение для развертывания всех артефактов в назначенную схему. Дополнительные сведения см. в статье [Управляемые удостоверения для ресурсов Azure](../../active-directory/managed-identities-azure-resources/overview.md).

1. Установите раскрывающийся список **Версии схемы определения** версий **Опубликовано** на запись "v1" (по умолчанию как наиболее недавно **опубликованная** версия).

1. Для **назначения блокировки** оставьте значение по умолчанию **Не блокировать**. Дополнительные сведения см. в разделе [Блокировка ресурсов схем](./concepts/resource-locking.md).

1. Для назначения роли на уровне подписки **[группы пользователей или имя приложения]: участник** найдите и выберите пользователя, приложение или группу.

1. Для назначения политик на уровне подписки задайте **Имя тега** как "CostCenter" и **значение тега** как "ContosoIT".

1. В "ResourceGroup" укажите **имя** как "Учетная запись хранения" и **Расположение** как "Восточная часть США 2" из раскрывающегося списка.

   > [!NOTE]
   > Каждый артефакт, который был добавлен в группе ресурсов во время определения схемы, отображается с отступом в соответствии с группой ресурсов или объектом, в который он развертывается. Для артефактов, которые не принимают параметры или не имеют параметров для определения во время назначения, будут перечислены только контекстные сведения.

1. В шаблоне Azure Resource Manager "Учетная запись хранения" выберите "Standard_GRS" для параметра **storageAccountType**.

1. Прочтите поле сведений в нижней части страницы и нажмите кнопку **Назначить**.

## <a name="track-deployment-of-a-blueprint"></a>Отследить развертывание схемы

Когда схема была назначена одной или нескольким подпискам, происходит следующее:

- схема добавляется на страницу **Назначенным схемам** к подписке, которой она назначена;
- начинается процесс развертывания всех артефактов, определенных в проекте.

Теперь, когда схема была назначена подписке, проверьте ход выполнения развертывания.

1. Выберите **Назначенные схемы** на странице слева.

1. В списке схем выберите созданную вами ранее схему и выберите **Просмотреть информацию о схеме**.

   ![Просмотр сведений о назначении](./media/create-blueprint-portal/view-assignment-details.png)

1. На странице **Сведения о развертывании** убедитесь, что все артефакты были успешно развернуты и что во время развертывания не произошло ошибок. Если возникли ошибки, см. в разделе [Устранение неполадок в рамках схемы](./troubleshoot/general.md) действия по определению проблем.

## <a name="unassign-a-blueprint"></a>Отмена назначения схемы

Если схемы больше не нужны или были заменены схемами с обновленными шаблонами, политиками и структурами, их можно удалить из подписки. При удалении схемы назначенные артефакты оставляются. Чтобы удалить назначение схемы, выполните следующие действия.

1. Выберите **Назначенные схемы** на странице слева.

1. В списке схем выберите схему, назначение которой необходимо отменить, и нажмите кнопку **Отменить назначение схемы** в верхней части страницы.

1. В сообщении с подтверждением нажмите кнопку **ОК**.

## <a name="delete-a-blueprint"></a>Удаление схемы

1. Выберите **Определения схем** на странице слева.

1. Щелкните правой кнопкой мыши на схеме, которую необходимо удалить, и щелкните **Удалить схему**, затем нажмите кнопку **Да** в диалоговом окне подтверждения.

> [!NOTE]
> Удаление схемы этим способом приведет к удалению всех **опубликованных версий** выбранной схемы. Чтобы удалить одну версию, откройте схему, щелкните на вкладке **Опубликованные версии**, выберите и щелкните версию, которую требуется удалить, а затем нажмите кнопку **Удалить эту версию**. Схему с назначениями нельзя удалить до тех пор, пока не удалены все назначения схемы.

## <a name="next-steps"></a>Дополнительная информация

- Ознакомьтесь с [жизненным циклом схемы](./concepts/lifecycle.md).
- Узнайте, как использовать [статические и динамические параметры](./concepts/parameters.md)
- Научитесь настраивать [последовательность схемы](./concepts/sequencing-order.md)
- Узнайте, как применять [блокировку ресурсов схемы](./concepts/resource-locking.md)
- Узнайте, как [обновлять существующие назначения](./how-to/update-existing-assignments.md)
- Устраняйте проблемы, возникающие во время назначения схемы, с помощью [общих инструкций по устранению неполадок](./troubleshoot/general.md)
