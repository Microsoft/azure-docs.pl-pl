---
title: Wdrażanie zasobów za pomocą interfejsu API REST i szablonu
description: Użyj Azure Resource Manager i Menedżer zasobów interfejsu API REST do wdrażania zasobów na platformie Azure. Zasoby są zdefiniowane w szablonie usługi Resource Manager.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 77192aff9ed4fe33269b5e11891c30e15bc312dd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98028968"
---
# <a name="deploy-resources-with-arm-templates-and-azure-resource-manager-rest-api"></a>Wdrażanie zasobów za pomocą szablonów ARM i interfejsu API REST Azure Resource Manager

W tym artykule wyjaśniono, jak używać interfejsu API REST Azure Resource Manager z szablonami Azure Resource Manager (szablony usługi ARM) do wdrażania zasobów na platformie Azure.

Możesz dołączyć szablon do treści żądania lub połączyć się z plikiem. Plik może być plikiem lokalnym lub zewnętrznym, który jest dostępny za pośrednictwem identyfikatora URI. Jeśli szablon znajduje się na koncie magazynu, możesz ograniczyć dostęp do szablonu i udostępnić token sygnatury dostępu współdzielonego (SAS) podczas wdrażania.

## <a name="deployment-scope"></a>Zakres wdrożenia

Wdrożenie można określić w grupie zasobów, subskrypcji platformy Azure, grupie zarządzania lub dzierżawie. W zależności od zakresu wdrożenia używane są inne polecenia.

- Aby wdrożyć w **grupie zasobów**, użyj [wdrożeń — Utwórz](/rest/api/resources/deployments/createorupdate). Żądanie jest wysyłane do:

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-06-01
  ```

- Aby wdrożyć w **ramach subskrypcji**, użyj [wdrożeń — Utwórz zakres subskrypcji](/rest/api/resources/deployments/createorupdateatsubscriptionscope). Żądanie jest wysyłane do:

  ```HTTP
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-06-01
  ```

  Aby uzyskać więcej informacji o wdrożeniach na poziomie subskrypcji, zobacz [Tworzenie grup zasobów i zasobów na poziomie subskrypcji](deploy-to-subscription.md).

- Aby wdrożyć w **grupie zarządzania**, należy użyć [wdrożenia — tworzenie w zakresie grupy zarządzania](/rest/api/resources/deployments/createorupdateatmanagementgroupscope). Żądanie jest wysyłane do:

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{groupId}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-06-01
  ```

  Aby uzyskać więcej informacji o wdrożeniach na poziomie grupy zarządzania, zobacz [Tworzenie zasobów na poziomie grupy zarządzania](deploy-to-management-group.md).

- Aby wdrożyć w **dzierżawie**, użyj [wdrożeń — Utwórz lub zaktualizuj w zakresie dzierżawy](/rest/api/resources/deployments/createorupdateattenantscope). Żądanie jest wysyłane do:

  ```HTTP
  PUT https://management.azure.com/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2020-06-01
  ```

  Aby uzyskać więcej informacji na temat wdrożeń na poziomie dzierżawy, zobacz [Tworzenie zasobów na poziomie dzierżawy](deploy-to-tenant.md).

W przykładach w tym artykule są używane wdrożenia grup zasobów.

## <a name="deploy-with-the-rest-api"></a>Wdrażanie przy użyciu interfejsu API REST

1. Ustaw [wspólne parametry i nagłówki](/rest/api/azure/), w tym tokeny uwierzytelniania.

1. Jeśli wdrażasz w grupie zasobów, która nie istnieje, Utwórz grupę zasobów. Podaj identyfikator subskrypcji, nazwę nowej grupy zasobów i lokalizację potrzebną dla Twojego rozwiązania. Aby uzyskać więcej informacji, zobacz [Tworzenie grupy zasobów](/rest/api/resources/resourcegroups/createorupdate).

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2020-06-01
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

1. Przed wdrożeniem szablonu można wyświetlić podgląd zmian wprowadzonych przez szablon w danym środowisku. Użyj [operacji działania warunkowego](template-deploy-what-if.md) , aby sprawdzić, czy szablon wprowadza zmiany, których oczekujesz. Co-jeśli również sprawdza poprawność szablonu pod kątem błędów.

1. Aby wdrożyć szablon, podaj identyfikator subskrypcji, nazwę grupy zasobów, nazwę wdrożenia w identyfikatorze URI żądania.

   ```HTTP
   PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2019-10-01
   ```

   W treści żądania Podaj link do szablonu i pliku parametrów. Aby uzyskać więcej informacji na temat pliku parametrów, zobacz [Tworzenie pliku parametrów usługi Resource Manager](parameter-files.md).

   Zauważ, że `mode` ustawiono wartość **przyrostowa**. Aby uruchomić całe wdrożenie, ustaw `mode` na **zakończone**. Należy zachować ostrożność w przypadku korzystania z trybu kompletnego, ponieważ można przypadkowo usunąć zasoby, które nie znajdują się w szablonie.

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

    Jeśli chcesz rejestrować zawartość odpowiedzi, zażądać zawartości lub oba te elementy, Dołącz `debugSetting` je do żądania.

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

    Konto magazynu można skonfigurować tak, aby korzystało z tokenu sygnatury dostępu współdzielonego (SAS). Aby uzyskać więcej informacji, zobacz [delegowanie dostępu za pomocą sygnatury dostępu współdzielonego](/rest/api/storageservices/delegate-access-with-shared-access-signature).

    Jeśli musisz podać wartość poufną dla parametru (na przykład hasła), Dodaj tę wartość do magazynu kluczy. Pobierz Magazyn kluczy podczas wdrażania, jak pokazano w poprzednim przykładzie. Aby uzyskać więcej informacji, zobacz [używanie Azure Key Vault do przekazywania zabezpieczonej wartości parametrów podczas wdrażania](key-vault-parameter.md).

1. Zamiast łączyć się z plikami szablonu i parametrów, można je uwzględnić w treści żądania. Poniższy przykład pokazuje treść żądania z szablonem i parametrem wbudowanym:

   ```json
   {
      "properties": {
      "mode": "Incremental",
      "template": {
        "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
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
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Resources/deployments/{deploymentName}?api-version=2019-10-01
   ```

## <a name="deployment-name"></a>Nazwa wdrożenia

Można nadać wdrożenie nazwę, taką jak `ExampleDeployment` .

Za każdym razem, gdy uruchamiasz wdrożenie, do historii wdrożenia grupy zasobów zostanie dodany wpis z nazwą wdrożenia. Jeśli zostanie uruchomione inne wdrożenie i nastąpi taka sama nazwa, wcześniejszy wpis zostanie zastąpiony bieżącym wdrożeniem. Jeśli chcesz zachować unikatowe wpisy w historii wdrożenia, nadaj każdemu wdrożeniu unikatową nazwę.

Aby utworzyć unikatową nazwę, można przypisać liczbę losową. Lub Dodaj wartość daty.

W przypadku uruchamiania współbieżnych wdrożeń w tej samej grupie zasobów o tej samej nazwie wdrożenia zostanie wykonane tylko ostatnie wdrożenie. Wszystkie wdrożenia o tej samej nazwie, które nie zostały zakończone, są zastępowane przez ostatnie wdrożenie. Jeśli na przykład zostanie uruchomione wdrożenie o nazwie, `newStorage` które wdraża konto magazynu o nazwie `storage1` , a w tym samym czasie zostanie uruchomione inne wdrożenie o nazwie, `newStorage` które wdraża konto magazynu o nazwie `storage2` , zostanie wdrożone tylko jedno konto magazynu. Utworzone konto magazynu ma nazwę `storage2` .

Jeśli jednak zostanie uruchomione wdrożenie o nazwie, `newStorage` które wdraża konto magazynu o nazwie `storage1` i natychmiast po jego zakończeniu uruchomisz inne wdrożenie o nazwie, `newStorage` które wdraża konto magazynu o nazwie `storage2` , masz dwa konta magazynu. Jeden z nich ma nazwę `storage1` , a drugi o nazwie `storage2` . Jednak w historii wdrożenia znajduje się tylko jeden wpis.

W przypadku określenia unikatowej nazwy dla każdego wdrożenia można uruchomić je współbieżnie bez konfliktu. W przypadku uruchomienia wdrożenia o nazwie, `newStorage1` które wdraża konto magazynu o nazwie `storage1` i w tym samym czasie, należy uruchomić inne wdrożenie o nazwie, `newStorage2` które wdraża konto magazynu o nazwie `storage2` , istnieją dwa konta magazynu i dwa wpisy w historii wdrażania.

Aby uniknąć konfliktów z jednoczesnymi wdrożeniami i zapewnić unikatowe wpisy w historii wdrożenia, należy nadać każdemu wdrożeniu unikatową nazwę.

## <a name="next-steps"></a>Następne kroki

- Aby wrócić do pomyślnego wdrożenia, gdy wystąpi błąd, zobacz [wycofywanie po pomyślnym wdrożeniu](rollback-on-error.md).
- Aby określić sposób obsługi zasobów, które istnieją w grupie zasobów, ale nie są zdefiniowane w szablonie, zobacz [Azure Resource Manager trybami wdrożenia](deployment-modes.md).
- Aby dowiedzieć się więcej o obsłudze asynchronicznych operacji REST, zobacz [śledzenie asynchronicznych operacji na platformie Azure](../management/async-operations.md).
- Aby dowiedzieć się więcej na temat szablonów, zobacz [Omówienie struktury i składni szablonów ARM](template-syntax.md).
