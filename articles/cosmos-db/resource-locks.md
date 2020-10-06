---
title: Zapobiegaj usunięciu lub zmianie zasobów Azure Cosmos DB
description: Użyj blokad zasobów platformy Azure, aby uniemożliwić usunięcie lub zmianę zasobów Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/06/2020
ms.author: mjbrown
ms.openlocfilehash: b23fb22a99705e1bf37de7a7982513ff692d8f6a
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91771053"
---
# <a name="prevent-azure-cosmos-db-resources-from-being-deleted-or-changed"></a>Zapobiegaj usunięciu lub zmianie zasobów Azure Cosmos DB

Administrator może chcieć zablokować konto usługi Azure Cosmos, bazę danych lub kontener, aby zapobiec przypadkowemu usunięciu lub zmodyfikowaniu zasobów krytycznych przez innych użytkowników w organizacji. Poziom blokady można ustawić na wartość CanNotDelete lub ReadOnly.

- **CanNotDelete** oznacza, że autoryzowani użytkownicy nadal mogą odczytywać i modyfikować zasób, ale nie mogą usunąć tego zasobu.
- **ReadOnly** oznacza, że autoryzowani użytkownicy mogą odczytywać zasoby, ale nie mogą usuwać ani aktualizować zasobu. Zastosowanie tej blokady jest podobne do ograniczenia wszystkich autoryzowanych użytkowników do uprawnień udzielonych przez rolę czytelnika.

## <a name="how-locks-are-applied"></a>Jak są stosowane blokady

W przypadku zastosowania blokady w zakresie nadrzędnym wszystkie zasoby w tym zakresie dziedziczą tę samą blokadę. Nawet zasoby dodawane później dziedziczą blokadę z elementu nadrzędnego. Pierwszeństwo ma najbardziej restrykcyjną blokadę dziedziczenia.

W przeciwieństwie do kontroli dostępu opartej na rolach blokady zarządzania są używane do stosowania ograniczenia do wszystkich użytkowników i ról. Aby dowiedzieć się więcej na temat RBAC dla Azure Cosmos DB Zobacz, [Kontrola dostępu oparta na rolach w Azure Cosmos DB](role-based-access-control.md).

Blokady usługi Resource Manager dotyczą tylko operacji wykonywanych na płaszczyźnie zarządzania, która składa się z operacji wysyłanych do witryny https://management.azure.com. Blokady nie ograniczają sposobu wykonywania własnych funkcji przez zasoby. Zmiany zasobów są ograniczone, ale operacje zasobów nie są ograniczone. Na przykład blokada tylko do odczytu w kontenerze usługi Azure Cosmos uniemożliwia usunięcie lub zmodyfikowanie kontenera. Nie uniemożliwia to tworzenia, aktualizowania ani usuwania danych w kontenerze. Transakcje danych są dozwolone, ponieważ te operacje nie są wysyłane do witryny https://management.azure.com.

## <a name="manage-locks"></a>Zarządzanie blokadami

> [!WARNING]
> Blokady zasobów nie działają w przypadku zmian wprowadzonych przez użytkowników uzyskujących dostęp do Azure Cosmos DB przy użyciu kluczy konta, chyba że konto usługi Azure Cosmos jest najpierw zablokowane przez włączenie właściwości disableKeyBasedMetadataWriteAccess. Należy zachować ostrożność przed włączeniem tej właściwości, aby upewnić się, że nie spowoduje to przerwania istniejących aplikacji, które wprowadzają zmiany w zasobach przy użyciu dowolnego zestawu SDK, Azure Portal lub narzędzi innych firm, które łączą się za pomocą kluczy konta i modyfikują zasoby, takie jak zmiana przepływności, aktualizowanie zasad indeksu itp. Aby dowiedzieć się więcej i zapoznać się z listą kontrolną, aby upewnić się, że aplikacje będą nadal działać, zobacz temat [zapobieganie zmianom Azure Cosmos DB zestawów SDK](role-based-access-control.md#prevent-sdk-changes)

### <a name="powershell"></a>PowerShell

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "my-cosmos-account"
$lockName = "$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
Update-AzCosmosDBAccount -ResourceGroupName $resourceGroupName -Name $accountName -DisableKeyBasedMetadataWriteAccess true

# Create a Delete Lock on an Azure Cosmos account resource and all child resources
New-AzResourceLock `
    -ApiVersion "2020-04-01" `
    -ResourceType "Microsoft.DocumentDB/databaseAccounts" `
    -ResourceGroupName $resourceGroupName `
    -ResourceName $accountName `
    -LockName $lockName `
    -LockLevel "CanNotDelete" # CanNotDelete or ReadOnly
```

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

```bash
resourceGroupName='myResourceGroup'
accountName='my-cosmos-account'
$lockName="$accountName-Lock"

# First, update the account to prevent changes by anything that connects via account keys
az cosmosdb update  --name $accountName --resource-group $resourceGroupName  --disable-key-based-metadata-write-access true

# Create a Delete Lock on an Azure Cosmos account resource
az lock create --name $lockName \
    --resource-group $resourceGroupName \
    --resource-type Microsoft.DocumentDB/databaseAccount \
    --lock-type 'CanNotDelete' # CanNotDelete or ReadOnly \
    --resource $accountName
```

### <a name="template"></a>Szablon

W przypadku zastosowania blokady do zasobu Azure Cosmos DB należy użyć następujących formatów:

- Nazwij `{resourceName}/Microsoft.Authorization/{lockName}`
- Wprowadź `{resourceProviderNamespace}/{resourceType}/providers/locks`

> [!IMPORTANT]
> Podczas modyfikowania istniejącego konta usługi Azure Cosmos Pamiętaj o uwzględnieniu innych właściwości konta i zasobów podrzędnych, gdy redploying z tą właściwością. Nie Wdrażaj tego szablonu, ponieważ spowoduje to zresetowanie wszystkich właściwości konta.

```json
"resources": [
    {
        "type": "Microsoft.DocumentDB/databaseAccounts",
        "name": "[variables('accountName')]",
        "apiVersion": "2020-04-01",
        "kind": "GlobalDocumentDB",
        "location": "[parameters('location')]",
        "properties": {
            "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
            "locations": "[variables('locations')]",
            "databaseAccountOfferType": "Standard",
            "enableAutomaticFailover": "[parameters('automaticFailover')]",
            "disableKeyBasedMetadataWriteAccess": true
        }
    },
    {
        "type": "Microsoft.DocumentDB/databaseAccounts/providers/locks",
        "apiVersion": "2020-04-01",
        "name": "[concat(variables('accountName'), '/Microsoft.Authorization/siteLock')]",
        "dependsOn": [
        "[resourceId('Microsoft.DocumentDB/databaseAccounts', variables('accountName'))]"
        ],
        "properties": {
        "level": "CanNotDelete",
        "notes": "Cosmos account should not be deleted."
        }
    }
]
```

## <a name="next-steps"></a>Następne kroki

- [Omówienie blokad Azure Resource Manager](../azure-resource-manager/management/lock-resources.md)
