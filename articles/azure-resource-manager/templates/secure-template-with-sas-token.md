---
title: Bezpieczne wdrażanie szablonu z tokenem SAS
description: Wdróż zasoby na platformie Azure przy użyciu szablonu Azure Resource Manager, który jest chroniony przez token sygnatury dostępu współdzielonego. Pokazuje Azure PowerShell i interfejs wiersza polecenia platformy Azure.
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 42eaae316d4fd0575102323933f849a3058228a6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80156399"
---
# <a name="deploy-private-arm-template-with-sas-token"></a>Wdróż prywatny szablon ARM z tokenem SAS

Gdy szablon Azure Resource Manager (ARM) znajduje się na koncie magazynu, możesz ograniczyć dostęp do szablonu, aby uniknąć ujawniania go publicznie. Dostęp do bezpiecznego szablonu można uzyskać, tworząc token sygnatury dostępu współdzielonego (SAS) dla szablonu i dostarczając ten token podczas wdrażania. W tym artykule wyjaśniono, jak używać Azure PowerShell lub interfejsu wiersza polecenia platformy Azure do wdrożenia szablonu z tokenem SAS.

## <a name="create-storage-account-with-secured-container"></a>Utwórz konto magazynu z bezpiecznym kontenerem

Poniższy skrypt tworzy konto magazynu i kontener z wyłączonym dostępem publicznym.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location "Central US"
New-AzStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name} `
  -Type Standard_LRS `
  -Location "Central US"
Set-AzCurrentStorageAccount `
  -ResourceGroupName ExampleGroup `
  -Name {your-unique-name}
New-AzStorageContainer `
  -Name templates `
  -Permission Off
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az group create \
  --name "ExampleGroup" \
  --location "Central US"
az storage account create \
    --resource-group ExampleGroup \
    --location "Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
```

---

## <a name="upload-template-to-storage-account"></a>Przekaż szablon do konta magazynu

Teraz możesz przystąpić do przekazywania szablonu do konta magazynu. Podaj ścieżkę do szablonu, którego chcesz użyć.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
Set-AzStorageBlobContent `
  -Container templates `
  -File c:\Templates\azuredeploy.json
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az storage blob upload \
    --container-name templates \
    --file azuredeploy.json \
    --name azuredeploy.json \
    --connection-string $connection
```

---

## <a name="provide-sas-token-during-deployment"></a>Podaj token sygnatury dostępu współdzielonego podczas wdrażania

Aby wdrożyć szablon prywatny na koncie magazynu, wygeneruj token sygnatury dostępu współdzielonego i umieść go w identyfikatorze URI dla szablonu. Ustaw czas wygaśnięcia, aby zapewnić wystarczającą ilość czasu na ukończenie wdrożenia.

> [!IMPORTANT]
> Obiekt BLOB zawierający szablon jest dostępny tylko dla właściciela konta. Jednak podczas tworzenia tokenu sygnatury dostępu współdzielonego dla obiektu BLOB obiekt BLOB jest dostępny dla wszystkich użytkowników o tym identyfikatorze URI. Jeśli inny użytkownik przechwytuje identyfikator URI, ten użytkownik będzie mógł uzyskać dostęp do szablonu. Token SAS to dobry sposób ograniczania dostępu do szablonów, ale nie należy uwzględniać poufnych danych, takich jak hasła bezpośrednio w szablonie.
>

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken `
  -Container templates `
  -Blob azuredeploy.json `
  -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri $templateuri
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ExampleGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name azuredeploy.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name azuredeploy.json \
    --output tsv \
    --connection-string $connection)
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

---

Aby zapoznać się z przykładem użycia tokenu sygnatury dostępu współdzielonego z połączonymi szablonami, zobacz [Używanie połączonych szablonów z Azure Resource Manager](linked-templates.md).


## <a name="next-steps"></a>Następne kroki
* Aby zapoznać się z wprowadzeniem do wdrażania szablonów, zobacz [wdrażanie zasobów przy użyciu szablonów ARM i Azure PowerShell](deploy-powershell.md).
* Aby zdefiniować parametry w szablonie, zobacz [Tworzenie szablonów](template-syntax.md#parameters).
