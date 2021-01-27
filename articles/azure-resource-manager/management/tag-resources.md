---
title: Dodawanie tagów do zasobów, grup zasobów i subskrypcji dla organizacji logicznej
description: Pokazuje, jak zastosować Tagi do organizowania zasobów platformy Azure na potrzeby rozliczeń i zarządzania nimi.
ms.topic: conceptual
ms.date: 01/04/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: fb93673b643fd13efe9ffea148c5fb1d072f9e05
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896227"
---
# <a name="use-tags-to-organize-your-azure-resources-and-management-hierarchy"></a>Używanie tagów do organizowania zasobów platformy Azure i hierarchii zarządzania

Możesz zastosować znaczniki do zasobów platformy Azure, grup zasobów i subskrypcji, aby logicznie zorganizować je w taksonomię. Każdy tag składa się z nazwy i pary wartości. Na przykład można zastosować nazwę „Środowisko” i wartość „Produkcyjne” do wszystkich zasobów w środowisku produkcyjnym.

Aby zapoznać się z zaleceniami dotyczącymi sposobu implementowania strategii tagowania, zobacz [Przewodnik po nazewnictwu i znakowaniu zasobów](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json).

> [!IMPORTANT]
> W nazwach tagów nie jest rozróżniana wielkość liter w operacjach. Tag o nazwie znacznika, niezależnie od wielkości liter, jest aktualizowany lub pobierany. Jednak dostawca zasobów może zachować wielkość liter podaną dla nazwy tagu. Zostanie wyświetlona wielkość liter w raportach kosztów.
> 
> W wartościach tagów jest rozróżniana wielkość liter.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="required-access"></a>Wymagany dostęp

Istnieją dwa sposoby uzyskania wymaganego dostępu do tagów zasobów.

- Możesz mieć dostęp do zapisu dla typu zasobu **Microsoft. resources/Tags** . Ten dostęp umożliwia oznaczanie dowolnego zasobu, nawet jeśli nie masz dostępu do samego zasobu. Rola [współautor znacznika](../../role-based-access-control/built-in-roles.md#tag-contributor) przyznaje ten dostęp. Obecnie rola współautor tagów nie może stosować tagów do zasobów lub grup zasobów za pomocą portalu. Można stosować Tagi do subskrypcji za pomocą portalu. Obsługuje wszystkie operacje tagów za poorednictwem programu PowerShell i interfejsu API REST.  

- Możesz mieć dostęp do zapisu do samego zasobu. Rola [współautor](../../role-based-access-control/built-in-roles.md#contributor) przyznaje wymagany dostęp do stosowania tagów do dowolnych jednostek. Aby zastosować Tagi tylko do jednego typu zasobu, należy użyć roli współautor dla tego zasobu. Aby na przykład zastosować Tagi do maszyn wirtualnych, użyj [współautora maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

## <a name="powershell"></a>PowerShell

### <a name="apply-tags"></a>Zastosuj Tagi

Azure PowerShell oferuje dwa polecenia do stosowania tagów- [New-AzTag](/powershell/module/az.resources/new-aztag) i [Update-AzTag](/powershell/module/az.resources/update-aztag). Musisz mieć moduł AZ. resources 1.12.0 lub nowszy. Możesz sprawdzić swoją wersję za pomocą programu `Get-Module Az.Resources` . Możesz zainstalować ten moduł lub [zainstalować Azure PowerShell](/powershell/azure/install-az-ps) 3.6.1 lub nowszy.

Polecenie **New-AzTag** zastępuje wszystkie Tagi dla zasobu, grupy zasobów lub subskrypcji. Podczas wywoływania polecenia, należy przekazać identyfikator zasobu jednostki, którą chcesz oznaczyć.

W poniższym przykładzie zastosowano zestaw tagów do konta magazynu:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
New-AzTag -ResourceId $resource.id -Tag $tags
```

Po zakończeniu wykonywania polecenia należy zauważyć, że zasób ma dwa Tagi.

```output
Properties :
        Name    Value
        ======  =======
        Dept    Finance
        Status  Normal
```

Jeśli ponownie uruchomisz polecenie, ale tym razem z różnymi tagami, Zauważ, że wcześniejsze znaczniki są usuwane.

```azurepowershell-interactive
$tags = @{"Team"="Compliance"; "Environment"="Production"}
New-AzTag -ResourceId $resource.id -Tag $tags
```

```output
Properties :
        Name         Value
        ===========  ==========
        Environment  Production
        Team         Compliance
```

Aby dodać tagi do zasobu, który ma już Tagi, użyj polecenie **Update-AzTag**. Ustaw parametr **-Operation** do **scalenia**.

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

Zwróć uwagę, że dwa nowe Tagi zostały dodane do dwóch istniejących tagów.

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Normal
        Dept         Finance
        Team         Compliance
        Environment  Production
```

Nazwy poszczególnych tagów mogą mieć tylko jedną wartość. Jeśli podano nową wartość dla tagu, stara wartość zostanie zamieniona nawet w przypadku użycia operacji scalania. Poniższy przykład zmienia tag stanu z normalny na zielony.

```azurepowershell-interactive
$tags = @{"Status"="Green"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Green
        Dept         Finance
        Team         Compliance
        Environment  Production
```

Po ustawieniu parametru **-Operation** do **zastępowania** istniejące Tagi są zastępowane nowym zestawem tagów.

```azurepowershell-interactive
$tags = @{"Project"="ECommerce"; "CostCenter"="00123"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Replace
```

Tylko nowe Tagi pozostają w zasobie.

```output
Properties :
        Name        Value
        ==========  =========
        CostCenter  00123
        Team        Web
        Project     ECommerce
```

Te same polecenia działają również z grupami zasobów lub subskrypcjami. Identyfikator dla grupy zasobów lub subskrypcji, który ma zostać oznakowany, jest przekazywany.

Aby dodać nowy zestaw tagów do grupy zasobów, użyj:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
New-AzTag -ResourceId $resourceGroup.ResourceId -tag $tags
```

Aby zaktualizować Tagi dla grupy zasobów, użyj:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Production"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Update-AzTag -ResourceId $resourceGroup.ResourceId -Tag $tags -Operation Merge
```

Aby dodać nowy zestaw tagów do subskrypcji, użyj:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Dev"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
New-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags
```

Aby zaktualizować Tagi dla subskrypcji, użyj:

```azurepowershell-interactive
$tags = @{"Team"="Web Apps"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Update-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags -Operation Merge
```

Może istnieć więcej niż jeden zasób o tej samej nazwie w grupie zasobów. W takim przypadku można ustawić każdy zasób przy użyciu następujących poleceń:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Update-AzTag -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Operation Merge }
```

### <a name="list-tags"></a>Tworzenie listy tagów

Aby uzyskać Tagi dla zasobu, grupy zasobów lub subskrypcji, użyj polecenia [Get-AzTag](/powershell/module/az.resources/get-aztag) i przekaż identyfikator zasobu dla jednostki.

Aby wyświetlić Tagi dla zasobu, użyj:

```azurepowershell-interactive
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
Get-AzTag -ResourceId $resource.id
```

Aby wyświetlić Tagi dla grupy zasobów, użyj:

```azurepowershell-interactive
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Get-AzTag -ResourceId $resourceGroup.ResourceId
```

Aby wyświetlić Tagi dla subskrypcji, użyj:

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Get-AzTag -ResourceId "/subscriptions/$subscription"
```

### <a name="list-by-tag"></a>Lista według tagu

Aby uzyskać zasoby z określoną nazwą i wartością tagu, użyj:

```azurepowershell-interactive
(Get-AzResource -Tag @{ "CostCenter"="00123"}).Name
```

Aby uzyskać zasoby z określoną nazwą tagu z dowolną wartością tagu, użyj:

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

Aby uzyskać grupy zasobów z określoną nazwą i wartością tagu, użyj:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "CostCenter"="00123" }).ResourceGroupName
```

### <a name="remove-tags"></a>Usuń Tagi

Aby usunąć określone Tagi, użyj opcji **Update-AzTag** i Set **-Operation** do **usunięcia**. Przekaż Tagi, które chcesz usunąć.

```azurepowershell-interactive
$removeTags = @{"Project"="ECommerce"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $removeTags -Operation Delete
```

Określone Tagi są usuwane.

```output
Properties :
        Name        Value
        ==========  =====
        CostCenter  00123
```

Aby usunąć wszystkie Tagi, użyj polecenia [Remove-AzTag](/powershell/module/az.resources/remove-aztag) .

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Remove-AzTag -ResourceId "/subscriptions/$subscription"
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

### <a name="apply-tags"></a>Zastosuj Tagi

Interfejs wiersza polecenia platformy Azure oferuje dwa polecenia do stosowania tagów- [AZ tag Create](/cli/azure/tag#az_tag_create) i [AZ tag Update](/cli/azure/tag#az_tag_update). Musisz mieć interfejs wiersza polecenia platformy Azure 2.10.0 lub nowszy. Możesz sprawdzić swoją wersję za pomocą programu `az version` . Aby zaktualizować lub zainstalować, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

Polecenie **AZ tag Create** zastępuje wszystkie znaczniki dla zasobu, grupy zasobów lub subskrypcji. Podczas wywoływania polecenia, należy przekazać identyfikator zasobu jednostki, którą chcesz oznaczyć.

W poniższym przykładzie zastosowano zestaw tagów do konta magazynu:

```azurecli-interactive
resource=$(az resource show -g demoGroup -n demoStorage --resource-type Microsoft.Storage/storageAccounts --query "id" --output tsv)
az tag create --resource-id $resource --tags Dept=Finance Status=Normal
```

Po zakończeniu wykonywania polecenia należy zauważyć, że zasób ma dwa Tagi.

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Status": "Normal"
  }
},
```

Jeśli ponownie uruchomisz polecenie, ale tym razem z różnymi tagami, Zauważ, że wcześniejsze znaczniki są usuwane.

```azurecli-interactive
az tag create --resource-id $resource --tags Team=Compliance Environment=Production
```

```output
"properties": {
  "tags": {
    "Environment": "Production",
    "Team": "Compliance"
  }
},
```

Aby dodać tagi do zasobu, który ma już Tagi, użyj `az tag update` . Ustaw `--operation` parametr na wartość `Merge` .

```azurecli-interactive
az tag update --resource-id $resource --operation Merge --tags Dept=Finance Status=Normal
```

Zwróć uwagę, że dwa nowe Tagi zostały dodane do dwóch istniejących tagów.

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Environment": "Production",
    "Status": "Normal",
    "Team": "Compliance"
  }
},
```

Nazwy poszczególnych tagów mogą mieć tylko jedną wartość. Jeśli podano nową wartość dla tagu, stara wartość zostanie zamieniona nawet w przypadku użycia operacji scalania. Poniższy przykład zmienia tag stanu z normalny na zielony.

```azurecli-interactive
az tag update --resource-id $resource --operation Merge --tags Status=Green
```

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Environment": "Production",
    "Status": "Green",
    "Team": "Compliance"
  }
},
```

Po ustawieniu `--operation` parametru na `Replace` , istniejące znaczniki są zastępowane nowym zestawem tagów.

```azurecli-interactive
az tag update --resource-id $resource --operation Replace --tags Project=ECommerce CostCenter=00123 Team=Web
```

Tylko nowe Tagi pozostają w zasobie.

```output
"properties": {
  "tags": {
    "CostCenter": "00123",
    "Project": "ECommerce",
    "Team": "Web"
  }
},
```

Te same polecenia działają również z grupami zasobów lub subskrypcjami. Identyfikator dla grupy zasobów lub subskrypcji, który ma zostać oznakowany, jest przekazywany.

Aby dodać nowy zestaw tagów do grupy zasobów, użyj:

```azurecli-interactive
group=$(az group show -n demoGroup --query id --output tsv)
az tag create --resource-id $group --tags Dept=Finance Status=Normal
```

Aby zaktualizować Tagi dla grupy zasobów, użyj:

```azurecli-interactive
az tag update --resource-id $group --operation Merge --tags CostCenter=00123 Environment=Production
```

Aby dodać nowy zestaw tagów do subskrypcji, użyj:

```azurecli-interactive
sub=$(az account show --subscription "Demo Subscription" --query id --output tsv)
az tag create --resource-id /subscriptions/$sub --tags CostCenter=00123 Environment=Dev
```

Aby zaktualizować Tagi dla subskrypcji, użyj:

```azurecli-interactive
az tag update --resource-id /subscriptions/$sub --operation Merge --tags Team="Web Apps"
```

### <a name="list-tags"></a>Tworzenie listy tagów

Aby uzyskać Tagi dla zasobu, grupy zasobów lub subskrypcji, użyj polecenia [AZ tag list](/cli/azure/tag#az_tag_list) i przekaż identyfikator zasobu dla jednostki.

Aby wyświetlić Tagi dla zasobu, użyj:

```azurecli-interactive
resource=$(az resource show -g demoGroup -n demoStorage --resource-type Microsoft.Storage/storageAccounts --query "id" --output tsv)
az tag list --resource-id $resource
```

Aby wyświetlić Tagi dla grupy zasobów, użyj:

```azurecli-interactive
group=$(az group show -n demoGroup --query id --output tsv)
az tag list --resource-id $group
```

Aby wyświetlić Tagi dla subskrypcji, użyj:

```azurecli-interactive
sub=$(az account show --subscription "Demo Subscription" --query id --output tsv)
az tag list --resource-id /subscriptions/$sub
```

### <a name="list-by-tag"></a>Lista według tagu

Aby uzyskać zasoby z określoną nazwą i wartością tagu, użyj:

```azurecli-interactive
az resource list --tag CostCenter=00123 --query [].name
```

Aby uzyskać zasoby z określoną nazwą tagu z dowolną wartością tagu, użyj:

```azurecli-interactive
az resource list --tag Team --query [].name
```

Aby uzyskać grupy zasobów z określoną nazwą i wartością tagu, użyj:

```azurecli-interactive
az group list --tag Dept=Finance
```

### <a name="remove-tags"></a>Usuń Tagi

Aby usunąć określone Tagi, użyj `az tag update` i ustaw `--operation` wartość `Delete` . Przekaż Tagi, które chcesz usunąć.

```azurecli-interactive
az tag update --resource-id $resource --operation Delete --tags Project=ECommerce Team=Web
```

Określone Tagi są usuwane.

```output
"properties": {
  "tags": {
    "CostCenter": "00123"
  }
},
```

Aby usunąć wszystkie Tagi, użyj polecenia [AZ tag Delete](/cli/azure/tag#az_tag_delete) .

```azurecli-interactive
az tag delete --resource-id $resource
```

### <a name="handling-spaces"></a>Obsługa spacji

Jeśli nazwy tagów lub wartości zawierają spacje, należy ująć je w podwójne cudzysłowy.

```azurecli-interactive
az tag update --resource-id $group --operation Merge --tags "Cost Center"=Finance-1222 Location="West US"
```

## <a name="arm-templates"></a>Szablony usługi ARM

Podczas wdrażania można oznaczać zasoby, grupy zasobów i subskrypcje, korzystając z szablonu Azure Resource Manager (szablon ARM).

> [!NOTE]
> Tagi stosowane przez szablon ARM zastępują wszelkie istniejące Tagi.

### <a name="apply-values"></a>Zastosuj wartości

Poniższy przykład wdraża konto magazynu z trzema tagami. Dwa Tagi ( `Dept` i `Environment` ) są ustawione na wartości literału. Jeden tag ( `LastDeployed` ) jest ustawiony na parametr, który jest wartością domyślną bieżącej daty.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "Finance",
                "Environment": "Production",
                "LastDeployed": "[parameters('utcShort')]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-an-object"></a>Zastosuj obiekt

Możesz zdefiniować parametr obiektu przechowującego kilka tagów i zastosować ten obiekt do elementu tagu. Takie podejście zapewnia większą elastyczność niż w poprzednim przykładzie, ponieważ obiekt może mieć różne właściwości. Każda właściwość obiektu będzie osobnym tagiem dla zasobu. Poniższy przykład zawiera parametr o nazwie `tagValues`, który został zastosowany do elementu tagu.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "tagValues": {
            "type": "object",
            "defaultValue": {
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tagValues')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-a-json-string"></a>Zastosuj ciąg JSON

Aby przechowywać wiele wartości w jednym tagu, zastosuj ciąg JSON reprezentujący te wartości. Cały ciąg JSON jest przechowywany jako jeden tag, który nie może zawierać więcej niż 256 znaków. Poniższy przykład zawiera pojedynczy tag o nazwie `CostCenter`, który zawiera kilka wartości z ciągu JSON:  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-from-resource-group"></a>Zastosuj Tagi z grupy zasobów

Aby zastosować Tagi z grupy zasobów do zasobu, użyj funkcji [resourceing ()](../templates/template-functions-resource.md#resourcegroup) . Podczas pobierania wartości tagu Użyj `tags[tag-name]` składni zamiast `tags.tag-name` składni, ponieważ niektóre znaki nie są prawidłowo analizowane w notacji kropkowej.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "[resourceGroup().tags['Dept']]",
                "Environment": "[resourceGroup().tags['Environment']]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-to-resource-groups-or-subscriptions"></a>Stosowanie tagów do grup zasobów lub subskrypcji

Możesz dodać tagi do grupy zasobów lub subskrypcji, wdrażając typ zasobu **Microsoft. resources/Tags** . Tagi są stosowane do docelowej grupy zasobów lub subskrypcji dla wdrożenia. Za każdym razem, gdy wdrażasz szablon, wszystkie znaczniki, które zostały wcześniej zastosowane, zostały zastąpione.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tagName": {
            "type": "string",
            "defaultValue": "TeamName"
        },
        "tagValue": {
            "type": "string",
            "defaultValue": "AppTeam1"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": {
                    "[parameters('tagName')]": "[parameters('tagValue')]"
                }
            }
        }
    ]
}
```

Aby zastosować Tagi do grupy zasobów, użyj programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Wdróż w grupie zasobów, do której chcesz oznaczyć tag.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Aby zastosować Tagi do subskrypcji, użyj programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Wdróż w subskrypcji, która ma być tagiem.

```azurepowershell-interactive
New-AzSubscriptionDeployment -name tagresourcegroup -Location westus2 -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment sub create --name tagresourcegroup --location westus2 --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Aby uzyskać więcej informacji na temat wdrożeń subskrypcji, zobacz [Tworzenie grup zasobów i zasobów na poziomie subskrypcji](../templates/deploy-to-subscription.md).

Poniższy szablon dodaje Tagi z obiektu do grupy zasobów lub subskrypcji.

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tags": {
            "type": "object",
            "defaultValue": {
                "TeamName": "AppTeam1",
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": "[parameters('tags')]"
            }
        }
    ]
}
```

## <a name="portal"></a>Portal

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>Interfejs API REST

Aby móc korzystać z tagów za pomocą interfejsu API REST platformy Azure, użyj:

* [Tagi — Utwórz lub zaktualizuj w zakresie](/rest/api/resources/tags/createorupdateatscope) (Put operacja)
* [Tagi — Aktualizuj w zakresie](/rest/api/resources/tags/updateatscope) (operacja patch)
* [Tagi — get w zakresie](/rest/api/resources/tags/getatscope) (pobieranie operacji)
* [Tagi — Usuń w zakresie](/rest/api/resources/tags/deleteatscope) (operacja usuwania)

## <a name="inherit-tags"></a>Dziedzicz Tagi

Tagi zastosowane do grupy zasobów lub subskrypcji nie są dziedziczone przez zasoby. Aby zastosować do zasobów Tagi z subskrypcji lub grupy zasobów, zobacz temat [Zasady platformy Azure — Tagi](tag-policies.md).

## <a name="tags-and-billing"></a>Znaczniki i rozliczanie

Tagów można użyć do grupowania danych dotyczących rozliczeń. Na przykład jeśli jest uruchomionych wiele maszyn wirtualnych różnych organizacji, możesz użyć tagów do grupowania użycia według centrum kosztu. Tagi umożliwiają również kategoryzowanie kosztów według środowiska uruchomieniowego, na przykład na potrzeby rozliczania użycia maszyn uruchomionych w środowisku produkcyjnym.

Informacje o tagach można pobrać, pobierając plik użycia, plik z wartościami rozdzielanymi przecinkami (CSV) dostępny w Azure Portal. Aby uzyskać więcej informacji, zobacz [pobieranie lub wyświetlanie faktury rozliczeń na platformie Azure oraz danych dziennego użycia](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md). Podczas pobierania pliku użycia z Centrum konta platformy Azure wybierz pozycję **wersja 2**. W przypadku usług, które obsługują Tagi z rozliczeniami, Tagi są wyświetlane w kolumnie **Tagi** .

Aby uzyskać informacje na temat operacji interfejsu API REST, zobacz [Dokumentacja interfejsu API REST rozliczeń platformy Azure](/rest/api/billing/).

## <a name="limitations"></a>Ograniczenia

Tagi mają następujące ograniczenia:

* Nie wszystkie typy zasobów obsługują Tagi. Aby określić, czy można zastosować tag do typu zasobu, zobacz [obsługa tagów dla zasobów platformy Azure](tag-support.md).
* Dla każdego zasobu, grupy zasobów i subskrypcji może być maksymalnie 50 par nazwa/wartość tagu. Jeśli musisz zastosować więcej tagów niż maksymalna dozwolona liczba, użyj ciągu JSON dla wartości tagu. Ciąg JSON może zawierać wiele wartości, które są stosowane do jednej nazwy tagu. Grupa zasobów lub subskrypcja może zawierać wiele zasobów, dla których każda z nich ma 50 par nazwa/wartość.
* Nazwa tagu może zawierać maksymalnie 512 znaków, a wartość tagu jest ograniczona do 256 znaków. W przypadku kont magazynu nazwa tagu jest ograniczona do 128 znaków, a wartość tagu jest ograniczona do 256 znaków.
* Nie można zastosować tagów do zasobów klasycznych, takich jak Cloud Services.
* Nazwy tagów nie mogą zawierać następujących znaków:,,,,, `<` `>` `%` `&` `\` `?` , `/`

   > [!NOTE]
   > Obecnie strefy Azure DNS i usługi Traffic Manager również nie zezwalają na używanie spacji w tagu.
   >
   > Drzwi frontonu platformy Azure nie obsługują używania `#` w nazwie tagu.
   >
   > Azure Automation i Azure CDN obsługują tylko 15 tagów w zasobach.

## <a name="next-steps"></a>Następne kroki

* Nie wszystkie typy zasobów obsługują Tagi. Aby określić, czy można zastosować tag do typu zasobu, zobacz [obsługa tagów dla zasobów platformy Azure](tag-support.md).
* Aby zapoznać się z zaleceniami dotyczącymi sposobu implementowania strategii tagowania, zobacz [Przewodnik po nazewnictwu i znakowaniu zasobów](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json).
