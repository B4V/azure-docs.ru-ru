---
title: Добавление тегов к ресурсам Azure для их логической организации | Документация Майкрософт
description: Здесь описано, как применить теги, чтобы организовать ресурсы Azure для выставления счетов и управления.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 003a78e5-2ff8-4685-93b4-e94d6fb8ed5b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: AzurePortal
ms.devlang: na
ms.topic: conceptual
ms.date: 09/26/2018
ms.author: tomfitz
ms.openlocfilehash: 82a48c902b72b5aca546c45032a13e831533f9ee
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2018
ms.locfileid: "47393474"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Использование тегов для организации ресурсов в Azure

[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

Чтобы применить теги к ресурсам, у пользователя должен быть доступ на запись к этому типу ресурсов. Чтобы применить теги ко всем типам ресурсов, используйте роль [участника](../role-based-access-control/built-in-roles.md#contributor). Чтобы применить теги только к одному типу ресурсов, используйте роль участника для этого ресурса. Например, чтобы применить теги к виртуальным машинам, используйте [Участник виртуальных машин](../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="powershell"></a>PowerShell

Для работы примеров в этой статье требуется Azure PowerShell 6.0 или более поздней версии. Если у вас установлена более старая версия, чем 6.0, [обновите ее](/powershell/azure/install-azurerm-ps).

Чтобы просмотреть существующие теги для *группы ресурсов*, используйте этот командлет:

```powershell
(Get-AzureRmResourceGroup -Name examplegroup).Tags
```

Этот скрипт вернет ответ в следующем формате:

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

Чтобы просмотреть существующие теги для *ресурса с указанным идентификатором ресурса*, используйте:

```powershell
(Get-AzureRmResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

Или чтобы просмотреть существующие теги для *ресурса с указанным именем и группой ресурсов*, используйте:

```powershell
(Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Чтобы получить *группы ресурсов с определенным тегом*, используйте:

```powershell
(Get-AzureRmResourceGroup -Tag @{ Dept="Finance" }).ResourceGroupName
```

Чтобы получить *ресурсы с определенным тегом*, используйте:

```powershell
(Get-AzureRmResource -Tag @{ Dept="Finance"}).Name
```

Чтобы получить *ресурсы с определенным тегом*, используйте:

```powershell
(Get-AzureRmResource -TagName Dept).Name
```

Каждый раз, когда вы добавляете теги к ресурсу или группе ресурсов, вы перезаписываете существующие теги в этом ресурсе или группе. Поэтому необходимо использовать другой подход, исходя из того, имеются ли теги в ресурсе или в группе ресурсов.

Чтобы добавить теги в *группу ресурсов без тегов*, используйте этот командлет:

```powershell
Set-AzureRmResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

Чтобы добавить теги в *группу ресурсов с существующими тегами*, извлеките их, добавьте новый тег и повторно примените теги:

```powershell
$tags = (Get-AzureRmResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzureRmResourceGroup -Tag $tags -Name examplegroup
```

Чтобы добавить теги в *ресурс без тегов*, используйте этот командлет:

```powershell
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzureRmResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

Чтобы добавить теги в *ресурс с существующими тегами*, используйте:

```powershell
$r = Get-AzureRmResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.Tags.Add("Status", "Approved") 
Set-AzureRmResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

Чтобы добавить все теги из группы ресурсов к ресурсам в этой группе, *не сохраняя существующие теги ресурсов*, используйте следующий сценарий.

```powershell
$groups = Get-AzureRmResourceGroup
foreach ($g in $groups)
{
    Get-AzureRmResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzureRmResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

Чтобы добавить все теги из группы ресурсов к ресурсам в этой группе, *сохранив существующие теги ресурсов*, используйте приведенный ниже сценарий.

```powershell
$group = Get-AzureRmResourceGroup "examplegroup"
if ($group.Tags -ne $null) {
    $resources = Get-AzureRmResource -ResourceGroupName $group.ResourceGroupName
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzureRmResource -ResourceId $r.ResourceId).Tags
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }
            Set-AzureRmResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzureRmResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Чтобы удалить все теги, передайте пустую хэш-таблицу:

```powershell
Set-AzureRmResourceGroup -Tag @{} -Name examplegroup
```

## <a name="azure-cli"></a>Инфраструктура CLI Azure

Чтобы просмотреть существующие теги для *группы ресурсов*, используйте этот командлет:

```azurecli
az group show -n examplegroup --query tags
```

Этот скрипт вернет ответ в следующем формате:

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

Чтобы просмотреть существующие теги для *ресурса с указанным именем, типом и группой ресурсов*, используйте следующую команду:

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

При циклическом переборе коллекции ресурсов может потребоваться отобразить ресурс с помощью идентификатора ресурса. Полный пример показан далее в этой статье. Чтобы просмотреть существующие теги для *ресурса с указанным идентификатором ресурса*, используйте следующую команду:

```azurecli
az resource show --id <resource-id> --query tags
```

Чтобы получить группы ресурсов с определенным тегом, используйте команду `az group list`:

```azurecli
az group list --tag Dept=IT
```

Чтобы получить все ресурсы с определенным тегом и значением, используйте команду `az resource list`:

```azurecli
az resource list --tag Dept=Finance
```

Каждый раз, когда вы добавляете теги к ресурсу или группе ресурсов, вы перезаписываете существующие теги в этом ресурсе или группе. Поэтому необходимо использовать другой подход, исходя из того, имеются ли теги в ресурсе или в группе ресурсов.

Чтобы добавить теги в *группу ресурсов без тегов*, используйте этот командлет:

```azurecli
az group update -n examplegroup --set tags.Environment=Test tags.Dept=IT
```

Чтобы добавить теги в *ресурс без тегов*, используйте этот командлет:

```azurecli
az resource tag --tags Dept=IT Environment=Test -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Чтобы добавить теги в ресурс, который уже содержит теги, извлеките существующие теги, переформатируйте это значение и еще раз добавьте существующие и новые теги: 

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Чтобы добавить все теги из группы ресурсов к ресурсам в этой группе, *не сохраняя существующие теги ресурсов*, используйте следующий сценарий.

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    az resource tag --tags $t --id $resid
  done
done
```

Чтобы добавить все теги из группы ресурсов к ресурсам в этой группе и *сохранить существующие теги ресурсов*, используйте следующий сценарий:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    jsonrtag=$(az resource show --id $resid --query tags)
    rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
    az resource tag --tags $t$rt --id $resid
  done
done
```

## <a name="templates"></a>Шаблоны

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="portal"></a>Microsoft Azure

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST API

Портал Azure и PowerShell используют [интерфейс REST API диспетчера ресурсов Resource Manager](https://docs.microsoft.com/rest/api/resources/). Если вам нужно интегрировать теги в другую среду, их можно получить с помощью метода **GET** по идентификатору ресурса и обновить набор тегов с помощью вызова метода **PATCH**.

## <a name="tags-and-billing"></a>Теги и выставление счетов

С помощью тегов можно группировать данные о выставлении счетов. Например, если вы используете несколько виртуальных машин для различных организаций, то с помощью тегов можно сгруппировать сведения об использовании по месту возникновения затрат. Кроме того, теги можно использовать для классификации затрат по среде выполнения (например, сведения о выставленных счетах за виртуальные машины, запущенные в рабочей среде).

Сведения о тегах можно получить с помощью [интерфейсов API использования ресурсов Azure и RateCard](../billing/billing-usage-rate-card-overview.md) или файла сведений об использовании с разделителями-запятыми (CSV-файла). Этот файл можно скачать на [портале учетных записей Azure](https://account.windowsazure.com/) или на [портале Azure Enterprise Portal](https://ea.azure.com). Дополнительные сведения о программном доступе к данным для выставления счетов см. в статье [Получение ценных сведений о потреблении ресурсов Microsoft Azure](../billing/billing-usage-rate-card-overview.md). Подробнее об операциях REST API см. в [справочнике по REST API для выставления счетов Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c).

В скачанном CSV-файле со сведениями об использовании служб, поддерживающих теги выставления счетов, эти теги отображаются в столбце **Tags**. Дополнительные сведения см. в статье [Расшифровка счета за использование Microsoft Azure](../billing/billing-understand-your-bill.md).

![См. сведения об использовании тегов при выставлении счетов.](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>Дополнительная информация

* В подписку можно добавить ограничения и соглашения, используя настраиваемые политики. Некоторые политики могут требовать, чтобы для всех ресурсов было задано значение определенного тега. Дополнительные сведения см. в статье [Что такое служба "Политика Azure"](../azure-policy/azure-policy-introduction.md).
* Общие сведения об использовании Azure PowerShell для развертывания ресурсов см. в статье [Управление ресурсами с помощью Azure PowerShell и Resource Manager](powershell-azure-resource-manager.md).
* Общие сведения об использовании Azure CLI для развертывания ресурсов см. в статье [Управление ресурсами и группами ресурсов Azure с помощью интерфейса командной строки Azure](xplat-cli-azure-resource-manager.md).
* Общие сведения об использовании портала см. в статье [Управление ресурсами Azure через портал](resource-group-portal.md).  
* Инструкции по использованию Resource Manager для эффективного управления подписками в организациях см. в статье [Корпоративный каркас Azure: рекомендуемая система управления подписками](/azure/architecture/cloud-adoption-guide/subscription-governance).
