---
title: Wdrażanie zasobów za pomocą interfejsu API REST i szablonu
description: Użyj Azure Resource Manager i Menedżer zasobów interfejsu API REST do wdrażania zasobów na platformie Azure. Zasoby są zdefiniowane w szablonie usługi Resource Manager.
ms.topic: conceptual
ms.date: 06/04/2019
ms.openlocfilehash: 9cdb7b668e5170917b41ef49639bd9a17e538766
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80153237"
---
# <a name="deploy-resources-with-arm-templates-and-resource-manager-rest-api"></a>Wdrażanie zasobów za pomocą szablonów ARM i interfejsu API REST Menedżer zasobów

W tym artykule wyjaśniono, jak używać Menedżer zasobów interfejsu API REST z szablonami Azure Resource Manager (ARM) do wdrażania zasobów na platformie Azure.

Możesz dołączyć szablon do treści żądania lub połączyć się z plikiem. Plik może być plikiem lokalnym lub zewnętrznym, który jest dostępny za pośrednictwem identyfikatora URI. Jeśli szablon znajduje się na koncie magazynu, możesz ograniczyć dostęp do szablonu i udostępnić token sygnatury dostępu współdzielonego (SAS) podczas wdrażania.

## <a name="deployment-scope"></a>Zakres wdrożenia

Wdrożenie można określić w grupie zarządzania, subskrypcji platformy Azure lub grupie zasobów. W większości przypadków wdrożenie zostanie ukierunkowane na grupę zasobów. Użyj grup zarządzania lub wdrożeń subskrypcji, aby zastosować zasady i przypisania ról w określonym zakresie. W celu utworzenia grupy zasobów i wdrożenia do niej zasobów należy również użyć wdrożeń subskrypcji. W zależności od zakresu wdrożenia używane są inne polecenia.

Aby wdrożyć w **grupie zasobów**, użyj [wdrożeń — Utwórz](/rest/api/resources/deployments/createorupdate). Żądanie jest wysyłane do:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Aby wdrożyć w **ramach subskrypcji**, użyj [wdrożeń — Utwórz zakres subskrypcji](/rest/api/resources/deployments/createorupdateatsubscriptionscope). Żądanie jest wysyłane do:

```HTTP
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Aby uzyskać więcej informacji o wdrożeniach na poziomie subskrypcji, zobacz [Tworzenie grup zasobów i zasobów na poziomie subskrypcji](deploy-to-subscription.md).

Aby wdrożyć w **grupie zarządzania**, należy użyć [wdrożenia — tworzenie w zakresie grupy zarządzania](/rest/api/resources/deployments/createorupdateatmanagementgroupscope). Żądanie jest wysyłane do:

```HTTP
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-05-01
```

Aby uzyskać więcej informacji o wdrożeniach na poziomie grupy zarządzania, zobacz [Tworzenie zasobów na poziomie grupy zarządzania](deploy-to-management-group.md).

W przykładach w tym artykule są używane wdrożenia grup zasobów.

## <a name="deploy-with-the-rest-api"></a>Wdrażanie przy użyciu interfejsu API REST

1. Ustaw [wspólne parametry i nagłówki](/rest/api/azure/), w tym tokeny uwierzytelniania.

1. Jeśli nie masz istniejącej grupy zasobów, Utwórz grupę zasobów. Podaj identyfikator subskrypcji, nazwę nowej grupy zasobów i lokalizację potrzebną dla Twojego rozwiązania. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy zasobów](/rest/api/resources/resourcegroups/createorupdate).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2019-05-01
   ```

   Z treścią żądania, taką jak:

   ```json
   {
    "location": "West US",
    "tags": {
      "tagname1": "tagvalue1"
    }
   }
   ```

1. Sprawdź poprawność wdrożenia przed jego wykonaniem, uruchamiając operację [walidacji wdrożenia szablonu](/rest/api/resources/deployments/validate) . Podczas testowania wdrożenia podaj parametry dokładnie tak, jak podczas wykonywania wdrożenia (pokazane w następnym kroku).

1. Aby wdrożyć szablon, podaj identyfikator subskrypcji, nazwę grupy zasobów, nazwę wdrożenia w identyfikatorze URI żądania.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-05-01
   ```

   W treści żądania Podaj link do szablonu i pliku parametrów. Aby uzyskać więcej informacji na temat pliku parametrów, zobacz [Tworzenie pliku parametrów Menedżer zasobów](parameter-files.md).

   Zauważ, że **tryb** jest ustawiony na wartość **przyrostowa**. Aby przeprowadzić kompletne wdrożenie, ustaw **tryb** na **ukończone**. Należy zachować ostrożność w przypadku korzystania z trybu kompletnego, ponieważ można przypadkowo usunąć zasoby, które nie znajdują się w szablonie.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental"
    }
   }
   ```

    Jeśli chcesz rejestrować zawartość odpowiedzi, zażądać zawartości lub oba te elementy, Dołącz **debugSetting** do żądania.

   ```json
   {
    "properties": {
      "templateLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
        "contentVersion": "1.0.0.0"
      },
      "parametersLink": {
        "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
        "contentVersion": "1.0.0.0"
      },
      "mode": "Incremental",
      "debugSetting": {
        "detailLevel": "requestContent, responseContent"
      }
    }
   }
   ```

    Konto magazynu można skonfigurować tak, aby korzystało z tokenu sygnatury dostępu współdzielonego (SAS). Aby uzyskać więcej informacji, zobacz [delegowanie dostępu za pomocą sygnatury dostępu współdzielonego](/rest/api/storageservices/delegating-access-with-a-shared-access-signature).

    Jeśli musisz podać wartość poufną dla parametru (na przykład hasła), Dodaj tę wartość do magazynu kluczy. Pobierz Magazyn kluczy podczas wdrażania, jak pokazano w poprzednim przykładzie. Aby uzyskać więcej informacji, zobacz [Przekazywanie bezpiecznych wartości podczas wdrażania](key-vault-parameter.md).

1. Zamiast łączyć się z plikami szablonu i parametrów, można je uwzględnić w treści żądania. Poniższy przykład pokazuje treść żądania z szablonem i parametrem wbudowanym:

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
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
          },
          "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
              "description": "Location for all resources."
            }
          }
        },
        "variables": {
          "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
        },
        "resources": [
          {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-02-01",
            "name": "[variables('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
              "name": "[parameters('storageAccountType')]"
            },
            "kind": "StorageV2",
            "properties": {}
          }
        ],
        "outputs": {
          "storageAccountName": {
            "type": "string",
            "value": "[variables('storageAccountName')]"
          }
        }
      },
      "parameters": {
        "location": {
          "value": "eastus2"
        }
      }
    }
   }
   ```

1. Aby uzyskać stan wdrożenia szablonu, należy użyć [wdrożeń — Get](/rest/api/resources/deployments/get).

   ```HTTP
   GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2018-05-01
   ```

## <a name="next-steps"></a>Następne kroki

- Aby wrócić do pomyślnego wdrożenia, gdy wystąpi błąd, zobacz [wycofywanie po pomyślnym wdrożeniu](rollback-on-error.md).
- Aby określić sposób obsługi zasobów, które istnieją w grupie zasobów, ale nie są zdefiniowane w szablonie, zobacz [Azure Resource Manager trybami wdrożenia](deployment-modes.md).
- Aby dowiedzieć się więcej o obsłudze asynchronicznych operacji REST, zobacz [śledzenie asynchronicznych operacji na platformie Azure](../management/async-operations.md).
- Aby dowiedzieć się więcej na temat szablonów, zobacz [Omówienie struktury i składni szablonów ARM](template-syntax.md).

