---
title: Dodawanie tagów do zasobów, grup zasobów i subskrypcji dla organizacji logicznej
description: Pokazuje, jak zastosować Tagi do organizowania zasobów platformy Azure na potrzeby rozliczeń i zarządzania nimi.
ms.topic: conceptual
ms.date: 07/27/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3ffcb4a0f2f5dc64b165fcdec03f7c3ced258cc1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90086763"
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

Aby zastosować Tagi do zasobu, musisz mieć dostęp do zapisu dla typu zasobu **Microsoft. resources/Tags** . Rola [współautor tagów](../../role-based-access-control/built-in-roles.md#tag-contributor) umożliwia stosowanie tagów do jednostki bez uzyskiwania dostępu do samej jednostki. Obecnie rola współautor tagów nie może stosować tagów do zasobów lub grup zasobów za pomocą portalu. Można stosować Tagi do subskrypcji za pomocą portalu. Obsługuje wszystkie operacje tagów za poorednictwem programu PowerShell i interfejsu API REST.  

Rola [współautor](../../role-based-access-control/built-in-roles.md#contributor) przyznaje również wymagany dostęp do stosowania tagów do dowolnej jednostki. Aby zastosować Tagi tylko do jednego typu zasobu, należy użyć roli współautor dla tego zasobu. Aby na przykład zastosować Tagi do maszyn wirtualnych, użyj [współautora maszyny wirtualnej](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

## <a name="powershell"></a>Program PowerShell

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

Po ustawieniu parametru **-Operation** do **zastępowania**istniejące Tagi są zastępowane nowym zestawem tagów.

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

Dodając Tagi do grupy zasobów lub zasobu, można zastąpić istniejące Tagi lub dołączyć nowe tagi do istniejących tagów.

Aby zastąpić Tagi w zasobie, użyj:

```azurecli-interactive
az resource tag --tags 'Dept=IT' 'Environment=Test' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Aby dołączyć tag do istniejących tagów w zasobie, użyj:

```azurecli-interactive
az resource update --set tags.'Status'='Approved' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Aby zastąpić istniejące Tagi w grupie zasobów, użyj:

```azurecli-interactive
az group update -n examplegroup --tags 'Environment=Test' 'Dept=IT'
```

Aby dołączyć tag do istniejących tagów w grupie zasobów, użyj:

```azurecli-interactive
az group update -n examplegroup --set tags.'Status'='Approved'
```

Obecnie w interfejsie wiersza polecenia platformy Azure nie jest dostępne polecenie do stosowania tagów do subskrypcji. Można jednak użyć interfejsu wiersza polecenia, aby wdrożyć szablon ARM, który stosuje Tagi do subskrypcji. Zobacz [stosowanie tagów do grup zasobów lub subskrypcji](#apply-tags-to-resource-groups-or-subscriptions).

### <a name="list-tags"></a>Tworzenie listy tagów

Aby wyświetlić istniejące Tagi dla zasobu, użyj:

```azurecli-interactive
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Aby wyświetlić istniejące tagi dla grupy zasobów, użyj:

```azurecli-interactive
az group show -n examplegroup --query tags
```

Ten skrypt zwraca następujący format:

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

### <a name="list-by-tag"></a>Lista według tagu

Aby uzyskać wszystkie zasoby, które mają określony tag i wartość, użyj `az resource list` :

```azurecli-interactive
az resource list --tag Dept=Finance
```

Aby uzyskać grupy zasobów, które mają określony tag, użyj `az group list` :

```azurecli-interactive
az group list --tag Dept=IT
```

### <a name="handling-spaces"></a>Obsługa spacji

Jeśli nazwy tagów lub wartości zawierają spacje, należy wykonać kilka dodatkowych kroków. 

`--tags`Parametry w interfejsie wiersza polecenia platformy Azure mogą akceptować ciąg, który składa się z tablicy ciągów. Poniższy przykład zastępuje znaczniki w grupie zasobów, w której Tagi zawierają spacje i łącznik: 

```azurecli-interactive
TAGS=("Cost Center=Finance-1222" "Location=West US")
az group update --name examplegroup --tags "${TAGS[@]}"
```

Możesz użyć tej samej składni podczas tworzenia lub aktualizowania grupy zasobów lub zasobów przy użyciu `--tags` parametru.

Aby zaktualizować Tagi przy użyciu `--set` parametru, należy przekazać klucz i wartość jako ciąg. Poniższy przykład dołącza jeden tag do grupy zasobów:

```azurecli-interactive
TAG="Cost Center='Account-56'"
az group update --name examplegroup --set tags."$TAG"
```

W tym przypadku wartość tagu jest oznaczona pojedynczymi cudzysłowami, ponieważ wartość ma łącznik.

Może być również konieczne zastosowanie tagów do wielu zasobów. Poniższy przykład stosuje wszystkie Tagi z grupy zasobów do jej zasobów, gdy Tagi mogą zawierać spacje:

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '{}"' | sed 's/: /=/g' | sed "s/\"/'/g" | sed 's/, /,/g' | sed 's/ *$//g' | sed 's/^ *//g')
origIFS=$IFS
IFS=','
read -a tagarr <<< "$tags"
resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  az resource tag --tags "${tagarr[@]}" --id $id
done
IFS=$origIFS
```

## <a name="templates"></a>Szablony

Podczas wdrażania za pomocą szablonu Menedżer zasobów można oznaczać zasoby, grupy zasobów i subskrypcje.

### <a name="apply-values"></a>Zastosuj wartości

Poniższy przykład wdraża konto magazynu z trzema tagami. Dwa Tagi ( `Dept` i `Environment` ) są ustawione na wartości literału. Jeden tag ( `LastDeployed` ) jest ustawiony na parametr, który jest wartością domyślną bieżącej daty.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
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

Informacje o tagach można uzyskać za pomocą [interfejsów API kart użycia zasobów platformy Azure i karty rate](../../cost-management-billing/manage/usage-rate-card-overview.md) lub pliku z wartościami rozdzielanymi przecinkami (CSV). Plik użycia można pobrać z Azure Portal. Aby uzyskać więcej informacji, zobacz [pobieranie lub wyświetlanie faktury rozliczeń na platformie Azure oraz danych dziennego użycia](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md). Podczas pobierania pliku użycia z Centrum konta platformy Azure wybierz pozycję **wersja 2**. W przypadku usług, które obsługują Tagi z rozliczeniami, Tagi są wyświetlane w kolumnie **Tagi** .

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
