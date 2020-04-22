---
title: Tworzenie obszaru roboczego za pomocą szablonu usługi Azure Resource Manager
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć nowy obszar roboczy usługi Azure Machine Learning za pomocą szablonu usługi Azure Resource Manager.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2020
ms.custom: seoapril2019
ms.openlocfilehash: b802a9c9df7e7f0c44ea66ee0061efb517b80050
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682757"
---
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]
<br>

# <a name="use-an-azure-resource-manager-template-to-create-a-workspace-for-azure-machine-learning"></a>Tworzenie obszaru roboczego usługi Azure Machine Learning za pomocą szablonu usługi Azure Resource Manager

W tym artykule dowiesz się kilka sposobów tworzenia obszaru roboczego usługi Azure Machine Learning przy użyciu szablonów usługi Azure Resource Manager. Szablon Menedżera zasobów ułatwia tworzenie zasobów jako pojedynczej, skoordynowanej operacji. Szablon jest dokumentem JSON, który definiuje zasoby, które są potrzebne do wdrożenia. Może również określić parametry wdrażania. Parametry są używane do dostarczania wartości wejściowych podczas korzystania z szablonu.

Aby uzyskać więcej informacji, zobacz [Wdrażanie aplikacji za pomocą szablonu usługi Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Jeśli go nie masz, wypróbuj [bezpłatną lub płatną wersję usługi Azure Machine Learning](https://aka.ms/AMLFree).

* Aby użyć szablonu z interfejsu wiersza polecenia, potrzebujesz [programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview?view=azps-1.2.0) lub [interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="resource-manager-template"></a>Szablon usługi Resource Manager

Do utworzenia obszaru roboczego usługi Azure Machine Learning i skojarzonych zasobów platformy Azure można użyć następującego szablonu usługi Resource Manager:

[!code-json[create-azure-machine-learning-service-workspace](~/quickstart-templates/101-machine-learning-create/azuredeploy.json)]

Ten szablon tworzy następujące usługi platformy Azure:

* Grupa zasobów platformy Azure
* Konto usługi Azure Storage
* W usłudze Azure Key Vault
* Azure Application Insights
* Azure Container Registry
* Obszar roboczy usługi Azure Machine Learning

Grupa zasobów jest kontenerem, który przechowuje usługi. Różne usługi są wymagane przez obszar roboczy usługi Azure Machine Learning.

Przykładowy szablon ma dwa parametry:

* **Lokalizacja,** w której zostanie utworzona grupa zasobów i usługi.

    Szablon będzie używany do lokalizacji wybranej dla większości zasobów. Wyjątkiem jest usługa usługi Application Insights, która nie jest dostępna we wszystkich lokalizacjach, w których znajdują się inne usługi. Jeśli wybierzesz lokalizację, w której nie jest dostępna, usługa zostanie utworzona w lokalizacji południowo-środkowe stany USA.

* **Nazwa obszaru roboczego**, która jest przyjazną nazwą obszaru roboczego usługi Azure Machine Learning.

    > [!NOTE]
    > Nazwa obszaru roboczego jest niewrażliwa na wielkości liter.

    Nazwy innych usług są generowane losowo.

> [!TIP]
> Podczas gdy szablon skojarzony z tym dokumentem tworzy nowy rejestr kontenerów platformy Azure, można również utworzyć nowy obszar roboczy bez tworzenia rejestru kontenerów. Jeden zostanie utworzony podczas wykonywania operacji, która wymaga rejestru kontenerów. Na przykład szkolenia lub wdrażania modelu.
>
> Można również odwołać się do istniejącego rejestru kontenerów lub konta magazynu w szablonie usługi Azure Resource Manager, zamiast tworzyć nowe.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

Aby uzyskać więcej informacji na temat szablonów, zobacz następujące artykuły:

* [Szablony usługi Azure Resource Manager autora](../azure-resource-manager/templates/template-syntax.md)
* [Wdrażanie aplikacji za pomocą szablonów usługi Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md)
* [Typy zasobów usługi Microsoft.MachineLearningServices](https://docs.microsoft.com/azure/templates/microsoft.machinelearningservices/allversions)

### <a name="advanced-template"></a>Szablon zaawansowany

Poniższy przykładowy szablon pokazuje, jak utworzyć obszar roboczy z trzema ustawieniami:

* Włączanie ustawień wysokiej poufności dla obszaru roboczego
* Włączanie szyfrowania obszaru roboczego
* Używa istniejącego magazynu azure key vault do pobierania kluczy zarządzanych przez klienta

Aby uzyskać więcej informacji, zobacz [Szyfrowanie w spoczynku](concept-enterprise-security.md#encryption-at-rest).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "workspaceName": {
      "type": "string",
      "metadata": {
        "description": "Specifies the name of the Azure Machine Learning workspace."
      }
    },
    "location": {
      "type": "string",
      "defaultValue": "southcentralus",
      "allowedValues": [
        "eastus",
        "eastus2",
        "southcentralus",
        "southeastasia",
        "westcentralus",
        "westeurope",
        "westus2"
      ],
      "metadata": {
        "description": "Specifies the location for all resources."
      }
    },
    "sku":{
      "type": "string",
      "defaultValue": "basic",
      "allowedValues": [
        "basic",
        "enterprise"
      ],
      "metadata": {
        "description": "Specifies the sku, also referred to as 'edition' of the Azure Machine Learning workspace."
      }
    },
    "high_confidentiality":{
      "type": "string",
      "defaultValue": "false",
      "allowedValues": [
        "false",
        "true"
      ],
      "metadata": {
        "description": "Specifies that the Azure Machine Learning workspace holds highly confidential data."
      }
    },
    "encryption_status":{
      "type": "string",
      "defaultValue": "Disabled",
      "allowedValues": [
        "Enabled",
        "Disabled"
      ],
      "metadata": {
        "description": "Specifies if the Azure Machine Learning workspace should be encrypted with the customer managed key."
      }
    },
    "cmk_keyvault":{
      "type": "string",
      "metadata": {
        "description": "Specifies the customer managed keyvault Resource Manager ID."
      }
    },
    "resource_cmk_uri":{
      "type": "string",
      "metadata": {
        "description": "Specifies the customer managed keyvault key uri."
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat('sa',uniqueString(resourceGroup().id))]",
    "storageAccountType": "Standard_LRS",
    "keyVaultName": "[concat('kv',uniqueString(resourceGroup().id))]",
    "tenantId": "[subscription().tenantId]",
    "applicationInsightsName": "[concat('ai',uniqueString(resourceGroup().id))]",
    "containerRegistryName": "[concat('cr',uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2018-07-01",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "[variables('storageAccountType')]"
      },
      "kind": "StorageV2",
      "properties": {
        "encryption": {
          "services": {
            "blob": {
              "enabled": true
            },
            "file": {
              "enabled": true
            }
          },
          "keySource": "Microsoft.Storage"
        },
        "supportsHttpsTrafficOnly": true
      }
    },
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
        "accessPolicies": []
      }
    },
    {
      "type": "Microsoft.Insights/components",
      "apiVersion": "2015-05-01",
      "name": "[variables('applicationInsightsName')]",
      "location": "[if(or(equals(parameters('location'),'eastus2'),equals(parameters('location'),'westcentralus')),'southcentralus',parameters('location'))]",
      "kind": "web",
      "properties": {
        "Application_Type": "web"
      }
    },
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2017-10-01",
      "name": "[variables('containerRegistryName')]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "adminUserEnabled": true
      }
    },
    {
      "type": "Microsoft.MachineLearningServices/workspaces",
      "apiVersion": "2020-01-01",
      "name": "[parameters('workspaceName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",
        "[resourceId('Microsoft.Insights/components', variables('applicationInsightsName'))]",
        "[resourceId('Microsoft.ContainerRegistry/registries', variables('containerRegistryName'))]"
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
        "keyVault": "[resourceId('Microsoft.KeyVault/vaults',variables('keyVaultName'))]",
        "applicationInsights": "[resourceId('Microsoft.Insights/components',variables('applicationInsightsName'))]",
        "containerRegistry": "[resourceId('Microsoft.ContainerRegistry/registries',variables('containerRegistryName'))]",
        "storageAccount": "[resourceId('Microsoft.Storage/storageAccounts/',variables('storageAccountName'))]",
         "encryption": {
                "status": "[parameters('encryption_status')]",
                "keyVaultProperties": {
                    "keyVaultArmId": "[parameters('cmk_keyvault')]",
                    "keyIdentifier": "[parameters('resource_cmk_uri')]"
                  }
            },
        "hbiWorkspace": "[parameters('high_confidentiality')]"
      }
    }
  ]
}
```

Aby uzyskać identyfikator magazynu kluczy i identyfikator klucza wymaganego przez ten szablon, można użyć interfejsu wiersza polecenia platformy Azure. Następujące polecenie otrzymuje identyfikator magazynu kluczy:

```azurecli-interactive
az keyvault show --name mykeyvault --resource-group myresourcegroup --query "id"
```

To polecenie zwraca wartość `"/subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault"`podobną do .

Aby uzyskać identyfikator URI dla klucza zarządzanego przez klienta, użyj następującego polecenia:

```azurecli-interactive
az keyvault key show --vault-name mykeyvault --name mykey --query "key.kid"
```

To polecenie zwraca wartość `"https://mykeyvault.vault.azure.net/keys/mykey/{guid}"`podobną do .

> [!IMPORTANT]
> Po utworzeniu obszaru roboczego nie można zmienić ustawień poufnych danych, szyfrowania, identyfikatora magazynu kluczy ani identyfikatorów kluczy. Aby zmienić te wartości, należy utworzyć nowy obszar roboczy przy użyciu nowych wartości.

## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. Wykonaj kroki opisane w [programze Wdrażanie zasobów z szablonu niestandardowego](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal#deploy-resources-from-custom-template). Po dotarciu do ekranu __Edytuj szablon__ wklej szablon z tego dokumentu.
1. Wybierz __pozycję Zapisz,__ aby użyć szablonu. Podaj następujące informacje i zaakceptuj wymienione warunki:

   * Subskrypcja: Wybierz subskrypcję platformy Azure do użycia dla tych zasobów.
   * Grupa zasobów: wybierz lub utwórz grupę zasobów zawierającą usługi.
   * Nazwa obszaru roboczego: nazwa używana dla obszaru roboczego usługi Azure Machine Learning, który zostanie utworzony. Nazwa obszaru roboczego musi zawierać od 3 do 33 znaków. Może zawierać tylko znaki alfanumeryczne i '-'.
   * Lokalizacja: wybierz lokalizację, w której będą tworzone zasoby.

Aby uzyskać więcej informacji, zobacz [Wdrażanie zasobów z szablonu niestandardowego](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template).

## <a name="use-azure-powershell"></a>Korzystanie z programu Azure PowerShell

W tym przykładzie przyjęto założenie, że `azuredeploy.json` szablon został zapisany w pliku o nazwie w bieżącym katalogu:

```powershell
New-AzResourceGroup -Name examplegroup -Location "East US"
new-azresourcegroupdeployment -name exampledeployment `
  -resourcegroupname examplegroup -location "East US" `
  -templatefile .\azuredeploy.json -workspaceName "exampleworkspace" -sku "basic"
```

Aby uzyskać więcej informacji, zobacz [Wdrażanie zasobów za pomocą szablonów Usługi Resource Manager i szablonu](../azure-resource-manager/templates/deploy-powershell.md) prywatnego menedżera zasobów usługi Azure PowerShell i [wdrażanie go za pomocą tokena Sygnatury dostępu Współdzielonego i programu Azure PowerShell](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="use-the-azure-cli"></a>Używanie interfejsu wiersza polecenia platformy Azure

W tym przykładzie przyjęto założenie, że `azuredeploy.json` szablon został zapisany w pliku o nazwie w bieżącym katalogu:

```azurecli-interactive
az group create --name examplegroup --location "East US"
az group deployment create \
  --name exampledeployment \
  --resource-group examplegroup \
  --template-file azuredeploy.json \
  --parameters workspaceName=exampleworkspace location=eastus sku=basic
```

Aby uzyskać więcej informacji, zobacz [Wdrażanie zasobów za pomocą szablonów Menedżera zasobów i szablonu interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/deploy-cli.md) oraz [wdrażania prywatnego szablonu Menedżera zasobów z tokenem Sygnatury dostępu Współdzielonego i interfejsu wiersza polecenia platformy Azure](../azure-resource-manager/templates/secure-template-with-sas-token.md).

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="resource-provider-errors"></a>Błędy dostawcy zasobów

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="azure-key-vault-access-policy-and-azure-resource-manager-templates"></a>Zasady dostępu usługi Azure Key Vault i szablony usługi Azure Resource Manager

Podczas korzystania z szablonu Usługi Azure Resource Manager do tworzenia obszaru roboczego i skojarzonych zasobów (w tym usługi Azure Key Vault), wiele razy. Na przykład przy użyciu szablonu wiele razy z tych samych parametrów w ramach potoku ciągłej integracji i wdrażania.

Większość operacji tworzenia zasobów za pośrednictwem szablonów są idempotentne, ale Usługa Key Vault czyści zasady dostępu za każdym razem, gdy szablon jest używany. Wyczyszczenie zasad dostępu przerywa dostęp do magazynu kluczy dla każdego istniejącego obszaru roboczego, który go używa. Na przykład zatrzymaj/utwórz funkcje maszyny wirtualnej notesów platformy Azure może zakończyć się niepowodzeniem.  

Aby uniknąć tego problemu, zaleca się jedno z następujących podejść:

* Nie należy wdrażać szablonu więcej niż jeden raz dla tych samych parametrów. Lub usuń istniejące zasoby przed użyciem szablonu, aby je odtworzyć.

* Sprawdź zasady dostępu usługi Key Vault, a `accessPolicies` następnie użyj tych zasad, aby ustawić właściwość szablonu. Aby wyświetlić zasady dostępu, użyj następującego polecenia interfejsu wiersza polecenia platformy Azure:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query properties.accessPolicies
    ```

    Aby uzyskać więcej `accessPolicies` informacji na temat korzystania z sekcji szablonu, zobacz [odwołanie do obiektu AccessPolicyEntry](https://docs.microsoft.com/azure/templates/Microsoft.KeyVault/2018-02-14/vaults#AccessPolicyEntry).

* Sprawdź, czy zasób Magazynu kluczy już istnieje. Jeśli tak, nie należy ponownie utworzyć go za pośrednictwem szablonu. Na przykład, aby użyć istniejącego magazynu kluczy zamiast tworzyć nowy, należy wprowadzić następujące zmiany w szablonie:

    * **Dodaj** parametr, który akceptuje identyfikator istniejącego zasobu usługi Key Vault:

        ```json
        "keyVaultId":{
          "type": "string",
          "metadata": {
            "description": "Specify the existing Key Vault ID."
          }
        }
      ```

    * **Usuń** sekcję, która tworzy zasób Usługi Key Vault:

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

    * **Usuń** `"[resourceId('Microsoft.KeyVault/vaults', variables('keyVaultName'))]",` wiersz z `dependsOn` sekcji obszaru roboczego. **Zmień** również `keyVault` wpis `properties` w sekcji obszaru roboczego, aby odwoływać się do parametru: `keyVaultId`

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

    Po tych zmianach można określić identyfikator istniejącego zasobu usługi Key Vault podczas uruchamiania szablonu. Następnie szablon ponownie użyć magazynu kluczy, ustawiając `keyVault` właściwość obszaru roboczego na jego identyfikator.

    Aby uzyskać identyfikator magazynu kluczy, można odwołać się do danych wyjściowych oryginalnego uruchomienia szablonu lub użyć interfejsu wiersza polecenia platformy Azure. Następujące polecenie jest przykładem użycia interfejsu wiersza polecenia platformy Azure w celu uzyskania identyfikatora zasobu usługi Key Vault:

    ```azurecli-interactive
    az keyvault show --name mykeyvault --resource-group myresourcegroup --query id
    ```

    To polecenie zwraca wartość podobną do następującego tekstu:

    ```text
    /subscriptions/{subscription-guid}/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault
    ```

## <a name="next-steps"></a>Następne kroki

* [Wdrażanie zasobów za pomocą szablonów Menedżera zasobów i interfejsu API REST Menedżera zasobów](../azure-resource-manager/templates/deploy-rest.md).
* [Tworzenie i wdrażanie grup zasobów platformy Azure za pośrednictwem programu Visual Studio](../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
