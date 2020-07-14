---
title: Tworzenie obszaru roboczego przy użyciu szablonu Azure Resource Manager
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć nowy obszar roboczy Azure Machine Learning przy użyciu szablonu Azure Resource Manager.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: larryfr
author: Blackmist
ms.date: 07/09/2020
ms.custom: seoapril2019
ms.openlocfilehash: 4ba48e5beb8ce4b4ae126dd23acbe0dec650f655
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232155"
---
# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Użyj szablonu Azure Resource Manager, aby utworzyć obszar roboczy dla Azure Machine Learning

[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

W tym artykule przedstawiono kilka sposobów tworzenia obszaru roboczego Azure Machine Learning przy użyciu szablonów Azure Resource Manager. Szablon Menedżer zasobów ułatwia tworzenie zasobów jako jednej, skoordynowanej operacji. Szablon to dokument JSON, który definiuje zasoby, które są zbędne dla wdrożenia. Może również określać parametry wdrożenia. Parametry są używane do udostępniania wartości wejściowych przy użyciu szablonu.

Aby uzyskać więcej informacji, zobacz [wdrażanie aplikacji przy użyciu szablonu Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Jeśli go nie masz, wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree).

* Aby użyć szablonu z interfejsu wiersza polecenia, musisz mieć [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) lub [interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="workspace-resource-manager-template"></a>Szablon Menedżer zasobów obszaru roboczego

Szablon Azure Resource Manager używany w tym dokumencie znajduje się w katalogu [201-Machine-Learning-Advanced](https://github.com/Azure/azure-quickstart-templates/blob/master/201-machine-learning-advanced/azuredeploy.json) w usłudze Azure Start templates repozytorium GitHub.

Ten szablon umożliwia utworzenie następujących usług platformy Azure:

* Konto usługi Azure Storage
* W usłudze Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Obszar roboczy usługi Azure Machine Learning

Grupa zasobów to kontener, w którym znajdują się usługi. Różne usługi są wymagane przez obszar roboczy Azure Machine Learning.

Przykładowy szablon ma dwa **wymagane** parametry:

* **Lokalizacja** , w której zostaną utworzone zasoby.

    Szablon będzie korzystać z lokalizacji wybranej dla większości zasobów. Wyjątkiem jest usługa Application Insights, która nie jest dostępna we wszystkich lokalizacjach, w których znajdują się inne usługi. W przypadku wybrania lokalizacji, w której jest ona niedostępna, usługa zostanie utworzona w lokalizacji Południowo-środkowe stany USA.

* **Obszar roboczyname**, który jest przyjazną nazwą obszaru roboczego Azure Machine Learning.

    > [!NOTE]
    > W nazwie obszaru roboczego nie jest rozróżniana wielkość liter.

    Nazwy innych usług są generowane losowo.

> [!TIP]
> Chociaż szablon skojarzony z tym dokumentem tworzy nowy Azure Container Registry, można również utworzyć nowy obszar roboczy bez tworzenia rejestru kontenerów. Jeden zostanie utworzony podczas wykonywania operacji wymagającej rejestru kontenerów. Na przykład szkolenie lub wdrożenie modelu.
>
> Możesz również odwoływać się do istniejącego rejestru kontenerów lub konta magazynu w szablonie Azure Resource Manager, zamiast tworzyć nowe. Jednak używany rejestr kontenerów musi mieć włączone __konto administratora__ . Aby uzyskać informacje na temat włączania konta administratora, zobacz [konto administratora](/azure/container-registry/container-registry-authentication#admin-account).

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

Aby uzyskać więcej informacji na temat szablonów, zobacz następujące artykuły:

* [Tworzenie szablonów Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)
* [Wdrażanie aplikacji za pomocą szablonów Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md)
* [Typy zasobów Microsoft. MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

## <a name="deploy-template"></a>Wdrażanie szablonu

Aby wdrożyć szablon, należy utworzyć grupę zasobów.

Zapoznaj się z sekcją [Azure Portal](#use-the-azure-portal) , jeśli wolisz używać graficznego interfejsu użytkownika.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

```azurecli
az group create --name "examplegroup" --location "eastus"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroup -Name "examplegroup" -Location "eastus"
```

---

Po pomyślnym utworzeniu grupy zasobów Wdróż szablon przy użyciu następującego polecenia:

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" location="eastus"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus"
```

---

Domyślnie wszystkie zasoby utworzone w ramach szablonu są nowe. Istnieje również możliwość korzystania z istniejących zasobów. Podając dodatkowe parametry szablonu, można użyć istniejących zasobów. Na przykład jeśli chcesz użyć istniejącego konta magazynu, ustaw wartość **storageAccountOption** na **istniejąca** i podaj nazwę konta magazynu w parametrze **storageAccountName** .

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      storageAccountOption="existing" \
      storageAccountName="existingstorageaccountname"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -storageAccountOption "existing" `
  -storageAccountName "existingstorageaccountname"
```

---

## <a name="deploy-an-encrypted-workspace"></a>Wdrażanie zaszyfrowanego obszaru roboczego

Poniższy przykładowy szablon pokazuje, jak utworzyć obszar roboczy z trzema ustawieniami:

* Włącz ustawienia wysokiej poufności dla obszaru roboczego
* Włącz szyfrowanie dla obszaru roboczego
* Używa istniejącego Azure Key Vault do pobierania kluczy zarządzanych przez klienta

Aby uzyskać więcej informacji, zobacz [szyfrowanie w spoczynku](concept-enterprise-security.md#encryption-at-rest).

> [!IMPORTANT]
> Przed rozpoczęciem korzystania z tego szablonu należy spełnić pewne wymagania dotyczące subskrypcji:
>
> * Aplikacja __Azure Machine Learning__ musi być __współautorem__ subskrypcji platformy Azure.
> * Musisz mieć istniejące Azure Key Vault, które zawierają klucz szyfrowania.
> * Musisz mieć zasady dostępu w Azure Key Vault, które będą udzielać dostępu do __Azure Cosmos DB__ aplikacji __Get__, __zawijania__i __odwinięcia__ .
> * Azure Key Vault musi znajdować się w tym samym regionie, w którym planujesz utworzyć obszar roboczy Azure Machine Learning.

__Aby dodać aplikację Azure Machine Learning jako współautor__, użyj następujących poleceń:

1. Zaloguj się do konta platformy Azure i uzyskaj swój identyfikator subskrypcji. Ta subskrypcja musi być taka sama, która zawiera obszar roboczy Azure Machine Learning.  

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

    ```azurecli
    az account list --query '[].[name,id]' --output tsv
    ```

    > [!TIP]
    > Aby wybrać inną subskrypcję, użyj `az account set -s <subscription name or ID>` polecenia i określ nazwę lub Identyfikator subskrypcji do przełączenia. Aby uzyskać więcej informacji na temat wyboru subskrypcji, zobacz [Korzystanie z wielu subskrypcji platformy Azure](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest). 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Get-AzSubscription
    ```

    > [!TIP]
    > Aby wybrać inną subskrypcję, użyj `Az-SetContext -SubscriptionId <subscription ID>` polecenia i określ nazwę lub Identyfikator subskrypcji do przełączenia. Aby uzyskać więcej informacji na temat wyboru subskrypcji, zobacz [Korzystanie z wielu subskrypcji platformy Azure](https://docs.microsoft.com/powershell/azure/manage-subscriptions-azureps?view=azps-4.3.0).

    ---

1. Aby uzyskać identyfikator obiektu aplikacji Azure Machine Learning, użyj następującego polecenia. Wartość może być różna dla każdej subskrypcji platformy Azure:

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

    ```azurecli
    az ad sp list --display-name "Azure Machine Learning" --query '[].[appDisplayName,objectId]' --output tsv
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Get-AzADServicePrincipal --DisplayName "Azure Machine Learning" | select-object DisplayName, Id
    ```

    ---
    To polecenie zwraca identyfikator obiektu, który jest identyfikatorem GUID.

1. Aby dodać identyfikator obiektu jako współautor do subskrypcji, użyj następującego polecenia. Zamień na `<object-ID>` Identyfikator obiektu nazwy głównej usługi. Zamień na `<subscription-ID>` nazwę lub Identyfikator subskrypcji platformy Azure:

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

    ```azurecli
    az role assignment create --role 'Contributor' --assignee-object-id <object-ID> --subscription <subscription-ID>
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    New-AzRoleAssignment --ObjectId <object-ID> --RoleDefinitionName "Contributor" -Scope /subscriptions/<subscription-ID>
    ```

    ---

1. Aby wygenerować klucz w istniejącym Azure Key Vault, użyj jednego z następujących poleceń. Zamień `<keyvault-name>` na nazwę magazynu kluczy. Zamień na `<key-name>` nazwę, która ma zostać użyta dla klucza:

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

    ```azurecli
    az keyvault key create --vault-name <keyvault-name> --name <key-name> --protection software
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Add-AzKeyVaultKey -VaultName <keyvault-name> -Name <key-name> -Destination 'Software'
    ```
    --- 

__Aby dodać zasady dostępu do magazynu kluczy, użyj następujących poleceń__:

1. Aby uzyskać identyfikator obiektu aplikacji Azure Cosmos DB, użyj następującego polecenia. Wartość może być różna dla każdej subskrypcji platformy Azure:

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

    ```azurecli
    az ad sp list --display-name "Azure Cosmos DB" --query '[].[appDisplayName,objectId]' --output tsv
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Get-AzADServicePrincipal --DisplayName "Azure Cosmos DB" | select-object DisplayName, Id
    ```
    ---

    To polecenie zwraca identyfikator obiektu, który jest identyfikatorem GUID. Zapisz go później

1. Aby ustawić zasady, użyj następującego polecenia. Zamień `<keyvault-name>` na nazwę istniejącego Azure Key Vault. Zamień na `<object-ID>` Identyfikator GUID z poprzedniego kroku:

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

    ```azurecli
    az keyvault set-policy --name <keyvault-name> --object-id <object-ID> --key-permissions get unwrapKey wrapKey
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)
    
    ```azurepowershell
    Set-AzKeyVaultAccessPolicy -VaultName <keyvault-name> -ObjectId <object-ID> -PermissionsToKeys get, unwrapKey, wrapKey
    ```
    ---    

__Aby uzyskać wartości__ dla `cmk_keyvault` (identyfikatora Key Vault) i `resource_cmk_uri` parametrów (identyfikator URI klucza) wymaganych przez ten szablon, wykonaj następujące czynności:

1. Aby uzyskać identyfikator Key Vault, użyj następującego polecenia:

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

    ```azurecli
    az keyvault show --name <keyvault-name> --query 'id' --output tsv
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Get-AzureRMKeyVault -VaultName '<keyvault-name>'
    ```
    ---

    To polecenie zwraca wartość podobną do `/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>` .

1. Aby uzyskać wartość identyfikatora URI dla klucza zarządzanego przez klienta, użyj następującego polecenia:

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

    ```azurecli
    az keyvault key show --vault-name <keyvault-name> --name <key-name> --query 'key.kid' --output tsv
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Get-AzureKeyVaultKey -VaultName '<keyvault-name>' -KeyName '<key-name>'
    ```
    ---

    To polecenie zwraca wartość podobną do `https://mykeyvault.vault.azure.net/keys/mykey/{guid}` .

> [!IMPORTANT]
> Po utworzeniu obszaru roboczego nie można zmienić ustawień poufnych danych, szyfrowania, identyfikatora magazynu kluczy ani identyfikatorów kluczy. Aby zmienić te wartości, należy utworzyć nowy obszar roboczy przy użyciu nowych wartości.

Po pomyślnym wykonaniu powyższych kroków Wdróż szablon tak jak zwykle. Aby umożliwić korzystanie z kluczy zarządzanych przez klienta, ustaw następujące parametry:

* **encryption_status** do **włączenia**.
* **cmk_keyvault** `cmk_keyvault` wartość uzyskaną w poprzednich krokach.
* **resource_cmk_uri** `resource_cmk_uri` wartość uzyskaną w poprzednich krokach.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      encryption_status="Enabled" \
      cmk_keyvault="/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>" \
      resource_cmk_uri="https://mykeyvault.vault.azure.net/keys/mykey/{guid}" \
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -encryption_status "Enabled" `
  -cmk_keyvault "/subscriptions/{subscription-guid}/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<keyvault-name>" `
  -resource_cmk_uri "https://mykeyvault.vault.azure.net/keys/mykey/{guid}"
```
---

W przypadku korzystania z klucza zarządzanego przez klienta Azure Machine Learning tworzy pomocniczą grupę zasobów zawierającą wystąpienie Cosmos DB. Aby uzyskać więcej informacji, zobacz [szyfrowanie w Cosmos DB REST](concept-enterprise-security.md#encryption-at-rest).

Dodatkową konfiguracją, którą można podać dla danych, jest ustawienie dla parametru **confidential_data** **wartości true**. W tym celu program wykonuje następujące czynności:

* Program uruchamia szyfrowanie lokalnego dysku zapasowego dla Azure Machine Learning klastrów obliczeniowych, co zapewnia, że nie utworzono żadnych wcześniejszych klastrów w ramach subskrypcji. Jeśli wcześniej utworzono klaster w ramach subskrypcji, należy otworzyć bilet pomocy technicznej, aby umożliwić szyfrowanie dysku zapasowego dla klastrów obliczeniowych.
* Czyści lokalny dysk magazynujący między przebiegami.
* Bezpiecznie przekazuje poświadczenia konta magazynu, rejestru kontenerów i konta SSH z warstwy wykonywania do klastrów obliczeniowych, używając magazynu kluczy.
* Umożliwia filtrowanie adresów IP, aby upewnić się, że źródłowe pule usługi Batch nie mogą być wywoływane przez żadną zewnętrzną usługę inną niż AzureMachineLearningService.

  Aby uzyskać więcej informacji, zobacz [szyfrowanie w spoczynku](concept-enterprise-security.md#encryption-at-rest).

## <a name="deploy-workspace-behind-a-virtual-network"></a>Wdróż obszar roboczy za siecią wirtualną

Ustawiając `vnetOption` wartość parametru na `new` lub `existing` , można utworzyć zasoby używane przez obszar roboczy za siecią wirtualną.

> [!IMPORTANT]
> W przypadku rejestru kontenerów obsługiwana jest tylko jednostka SKU "Premium".

> [!IMPORTANT]
> Application Insights nie obsługuje wdrażania za siecią wirtualną.

### <a name="only-deploy-workspace-behind-private-endpoint"></a>Wdrażaj tylko obszar roboczy za prywatnym punktem końcowym

Jeśli skojarzone zasoby nie znajdują się za siecią wirtualną, można ustawić parametr **privateEndpointType** na `AutoAproval` lub `ManualApproval` wdrożyć obszar roboczy za prywatnym punktem końcowym.

> [!IMPORTANT]
> Wdrożenie jest prawidłowe tylko w regionach, które obsługują prywatne punkty końcowe.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -privateEndpointType "AutoApproval"
```

---

### <a name="use-a-new-virtual-network"></a>Użyj nowej sieci wirtualnej

Aby wdrożyć zasób za nową siecią wirtualną, należy ustawić **vnetOption** na **Nowy** wraz z ustawieniami sieci wirtualnej dla odpowiedniego zasobu. Poniższe wdrożenie przedstawia sposób wdrożenia obszaru roboczego z zasobem konta magazynu za nową siecią wirtualną.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      storageAccountBehindVNet="true"
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -storageAccountBehindVNet "true"
  -privateEndpointType "AutoApproval"
```

---

Alternatywnie można wdrożyć wiele lub wszystkie zasoby zależne za siecią wirtualną.

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      storageAccountBehindVNet="true" \
      keyVaultBehindVNet="true" \
      containerRegistryBehindVNet="true" \
      containerRegistryOption="new" \
      containerRegistrySku="Premium"
      privateEndpointType="AutoApproval"
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -storageAccountBehindVNet "true"
  -keyVaultBehindVNet "true" `
  -containerRegistryBehindVNet "true" `
  -containerRegistryOption "new" `
  -containerRegistrySku "Premium"
  -privateEndpointType "AutoApproval"
```

---

<!-- Workspaces need a private endpoint when associated resources are behind a virtual network to work properly. To set up a private endpoint for the workspace with a new virtual network:

> [!IMPORTANT]
> The deployment is only valid in regions which support private endpoints.

# [Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="new" \
      vnetName="examplevnet" \
      privateEndpointType="AutoApproval"
```

# [Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "new" `
  -vnetName "examplevnet" `
  -privateEndpointType "AutoApproval"
```

--- -->

### <a name="use-an-existing-virtual-network--resources"></a>Użyj istniejącej sieci wirtualnej & zasobów

Aby wdrożyć obszar roboczy z istniejącymi skojarzonymi zasobami, należy ustawić parametr **vnetOption** na **istniejący** wraz z parametrami podsieci. Należy jednak utworzyć punkty końcowe usługi w sieci wirtualnej dla każdego z zasobów **przed** wdrożeniem. Podobnie jak w przypadku nowych wdrożeń sieci wirtualnej, można mieć jeden lub wszystkie zasoby związane z siecią wirtualną.

> [!IMPORTANT]
> Podsieć powinna mieć `Microsoft.Storage` punkt końcowy usługi

> [!IMPORTANT]
> Podsieci nie umożliwiają tworzenia prywatnych punktów końcowych. Wyłącz prywatny punkt końcowy, aby włączyć podsieć.

1. Włącz punkty końcowe usługi dla zasobów.

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

    ```azurecli
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.Storage"
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.KeyVault"
    az network vnet subnet update --resource-group "examplegroup" --vnet-name "examplevnet" --name "examplesubnet" --service-endpoints "Microsoft.ContainerRegistry"
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)

    ```azurepowershell
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.Storage" | Set-AzVirtualNetwork
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.KeyVault" | Set-AzVirtualNetwork
    Get-AzVirtualNetwork -ResourceGroupName "examplegroup" -Name "examplevnet" | Set-AzVirtualNetworkSubnetConfig -Name "examplesubnet" -AddressPrefix "<subnet prefix>" -ServiceEndpoint "Microsoft.ContainerRegistry" | Set-AzVirtualNetwork
    ```

    ---

1. Wdrażanie obszaru roboczego

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azcli)

    ```azurecli
    az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="existing" \
      vnetName="examplevnet" \
      vnetResourceGroupName="examplegroup" \
      storageAccountBehindVNet="true" \
      keyVaultBehindVNet="true" \
      containerRegistryBehindVNet="true" \
      containerRegistryOption="new" \
      containerRegistrySku="Premium" \
      subnetName="examplesubnet" \
      subnetOption="existing"
      privateEndpointType="AutoApproval"
    ```

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azpowershell)
    ```azurepowershell
    New-AzResourceGroupDeployment `
      -Name "exampledeployment" `
      -ResourceGroupName "examplegroup" `
      -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
      -workspaceName "exampleworkspace" `
      -location "eastus" `
      -vnetOption "existing" `
      -vnetName "examplevnet" `
      -vnetResourceGroupName "examplegroup" `
      -storageAccountBehindVNet "true"
      -keyVaultBehindVNet "true" `
      -containerRegistryBehindVNet "true" `
      -containerRegistryOption "new" `
      -containerRegistrySku "Premium" `
      -subnetName "examplesubnet" `
      -subnetOption "existing"
      -privateEndpointType "AutoApproval"
    ```
    ---

<!-- Workspaces need a private endpoint when associated resources are behind a virtual network to work properly. To set up a private endpoint for the workspace with an existing virtual network:

> [!IMPORTANT]
> The deployment is only valid in regions which support private endpoints.

# [Azure CLI](#tab/azcli)

```azurecli
az deployment group create \
    --name "exampledeployment" \
    --resource-group "examplegroup" \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" \
    --parameters workspaceName="exampleworkspace" \
      location="eastus" \
      vnetOption="existing" \
      vnetName="examplevnet" \
      vnetResourceGroupName="rg" \
      privateEndpointType="AutoApproval" \
      subnetName="subnet" \
      subnetOption="existing"
```

# [Azure PowerShell](#tab/azpowershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name "exampledeployment" `
  -ResourceGroupName "examplegroup" `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-machine-learning-advanced/azuredeploy.json" `
  -workspaceName "exampleworkspace" `
  -location "eastus" `
  -vnetOption "existing" `
  -vnetName "examplevnet" `
  -vnetResourceGroupName "rg"
  -privateEndpointType "AutoApproval"
  -subnetName "subnet"
  -subnetOption "existing"
```

--- -->

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. Wykonaj kroki opisane w sekcji [wdrażanie zasobów z szablonu niestandardowego](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Po nadejściu ekranu __Wybierz szablon__ wybierz szablon **201-Machine-Learning-Advanced** z listy rozwijanej.
1. Wybierz __pozycję Wybierz szablon__ , aby użyć szablonu. Podaj następujące wymagane informacje i inne parametry w zależności od scenariusza wdrażania.

   * Subskrypcja: wybierz subskrypcję platformy Azure, która ma być używana dla tych zasobów.
   * Grupa zasobów: wybierz lub Utwórz grupę zasobów zawierającą usługi.
   * Region: Wybierz region świadczenia usługi Azure, w którym zostaną utworzone zasoby.
   * Nazwa obszaru roboczego: Nazwa do użycia dla obszaru roboczego Azure Machine Learning, który zostanie utworzony. Nazwa obszaru roboczego musi zawierać od 3 do 33 znaków. Może zawierać tylko znaki alfanumeryczne i znak "-".
   * Lokalizacja: Wybierz lokalizację, w której zostaną utworzone zasoby.
1. Wybierz pozycję __Przeglądanie + tworzenie__.
1. Na ekranie __Recenzja + tworzenie__ Zaakceptuj wymienione warunki i postanowienia, a następnie wybierz pozycję __Utwórz__.

Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów z szablonu niestandardowego](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="resource-provider-errors"></a>Błędy dostawcy zasobów

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Azure Key Vault zasad dostępu i szablonów Azure Resource Manager

Gdy używasz szablonu Azure Resource Manager do tworzenia obszaru roboczego i skojarzonych zasobów (w tym Azure Key Vault), wiele razy. Przykładowo wielokrotne użycie szablonu z tymi samymi parametrami w ramach potoku ciągłej integracji i wdrażania.

Większość operacji tworzenia zasobów za pomocą szablonów to idempotentne, ale Key Vault czyści zasady dostępu za każdym razem, gdy szablon jest używany. Wyczyszczenie zasad dostępu powoduje przerwanie dostępu do Key Vault wszystkich istniejących obszarów roboczych, które go używają. Na przykład funkcje zatrzymywania/tworzenia maszyny wirtualnej Azure Notebooks mogą zakończyć się niepowodzeniem.  

Aby uniknąć tego problemu, zalecamy zastosowanie jednej z następujących metod:

* Nie Wdrażaj szablonu więcej niż raz dla tych samych parametrów. Lub Usuń istniejące zasoby przed użyciem szablonu, aby utworzyć je ponownie.

* Przejrzyj zasady dostępu Key Vault a następnie użyj tych zasad, aby ustawić `accessPolicies` Właściwość szablonu. Aby wyświetlić zasady dostępu, użyj następującego polecenia platformy Azure:

    ```azurecli
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    Aby uzyskać więcej informacji na temat korzystania z `accessPolicies` sekcji szablonu, zobacz [odwołanie do obiektu AccessPolicyEntry](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry).

* Sprawdź, czy zasób Key Vault już istnieje. Jeśli tak, nie należy go ponownie tworzyć za pomocą szablonu. Na przykład, aby użyć istniejącego Key Vault zamiast tworzenia nowego, wprowadź następujące zmiany w szablonie:

    * **Dodaj** parametr, który akceptuje identyfikator istniejącego zasobu Key Vault:

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **Usuń** sekcję, która tworzy zasób Key Vault:

        ```json
        {
          "type": "Microsoft.KeyVault/vaults",
          "apiVersion": "2018-02-14",
          "name": "[variables('keyVaultName')]",
          "location": "[parameters('location')]",
          "properties": {
            "tenantId": "[variables('tenantId')]",
            "sku": {
              "name": "standard",
              "family": "A"
            },
            "accessPolicies": [
            ]
          }
        },
        ```

    * **Usuń** `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` wiersz z `dependsOn` sekcji obszaru roboczego. **Zmień** również `keyVault` wpis w `properties` sekcji obszaru roboczego, aby odwołać się do `keyVaultId` parametru:

        ```json
        {
          "type": "Microsoft.MachineLearningServices/workspaces",
          "apiVersion": "2019-11-01",
          "name": "[parameters('workspaceName')]",
          "location": "[parameters('location')]",
          "dependsOn": [
            "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
            "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]"
          ],
          "identity": {
            "type": "systemAssigned"
          },
          "sku": {
            "tier": "[parameters('sku')]",
            "name": "[parameters('sku')]"
          },
          "properties": {
            "friendlyName": "[parameters('workspaceName')]",
            "keyVault": "[parameters('keyVaultId')]",
            "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
            "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]"
          }
        }
        ```

    Po wprowadzeniu tych zmian możesz określić identyfikator istniejącego zasobu Key Vault podczas uruchamiania szablonu. Następnie szablon ponownie użyje Key Vault, ustawiając `keyVault` Właściwość obszaru roboczego na jego identyfikator.

    Aby uzyskać identyfikator Key Vault, można odwoływać się do danych wyjściowych oryginalnego szablonu lub użyć interfejsu wiersza polecenia platformy Azure. Poniższe polecenie stanowi przykład użycia interfejsu wiersza polecenia platformy Azure w celu pobrania identyfikatora zasobu Key Vault:

    ```azurecli
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    To polecenie zwraca wartość podobną do następującego tekstu:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>Następne kroki

* [Wdrażanie zasobów za pomocą szablonów Menedżer zasobów i Menedżer zasobów interfejsu API REST](../azure-resource-manager/templates/deploy-rest.md).
* [Tworzenie i wdrażanie grup zasobów platformy Azure za pomocą programu Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
