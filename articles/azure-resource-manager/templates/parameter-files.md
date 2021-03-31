---
title: Tworzenie pliku parametrów
description: Utwórz plik parametrów do przekazywania wartości podczas wdrażania szablonu Azure Resource Manager
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 2b6d942b21594fa608127bb8f403e72295671005
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89276664"
---
# <a name="create-resource-manager-parameter-file"></a>Utwórz plik parametrów Menedżer zasobów

Zamiast przekazywania parametrów jako wartości śródwierszowych w skrypcie prostszym może się okazać użycie pliku JSON zawierającego wartości parametrów. W tym artykule pokazano, jak utworzyć plik parametrów.

## <a name="parameter-file"></a>Plik parametrów

Plik parametrów ma następujący format:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "value": "<second-value>"
    }
  }
}
```

Zwróć uwagę, że wartości parametrów są przechowywane jako zwykły tekst w pliku parametrów. Ta metoda działa w przypadku wartości, które nie są poufne, na przykład określania jednostki SKU dla zasobu. Nie działa w przypadku poufnych wartości, takich jak hasła. Jeśli konieczne jest przekazanie wartości poufnej jako parametru, należy zapisać wartość w magazynie kluczy i odwołać się do magazynu kluczy w pliku parametrów. Wartość poufne jest bezpiecznie pobierana podczas wdrażania.

Następujący plik parametrów zawiera wartość zwykłego tekstu i wartość przechowywaną w magazynie kluczy.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "reference": {
        "keyVault": {
          "id": "<resource-id-key-vault>"
        },
        "secretName": "<secret-name>"
      }
    }
  }
}
```

Aby uzyskać więcej informacji o korzystaniu z wartości z magazynu kluczy, zobacz [używanie Azure Key Vault do przekazywania zabezpieczonej wartości parametrów podczas wdrażania](key-vault-parameter.md).

## <a name="define-parameter-values"></a>Definiowanie wartości parametrów

Aby ustalić sposób definiowania wartości parametrów, Otwórz wdrożony szablon. Zapoznaj się z sekcją parametry szablonu. W poniższym przykładzie przedstawiono parametry szablonu.

```json
"parameters": {
  "storagePrefix": {
    "type": "string",
    "maxLength": 11
  },
  "storageAccountType": {
    "type": "string",
    "defaultValue": "Standard_LRS",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS",
    "Standard_ZRS",
    "Premium_LRS"
    ]
  }
}
```

Pierwsze szczegóły do powiadomienia to nazwa każdego parametru. Wartości w pliku parametrów muszą być zgodne z nazwami.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
    },
    "storageAccountType": {
    }
  }
}
```

Zwróć uwagę na typ parametru. Wartości w pliku parametrów muszą mieć takie same typy. Dla tego szablonu można podać oba parametry jako ciągi.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": ""
    },
    "storageAccountType": {
      "value": ""
    }
  }
}
```

Następnie poszukaj wartości domyślnej. Jeśli parametr ma wartość domyślną, można podać wartość, ale nie jest to konieczne.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "" // This value must be provided.
    },
    "storageAccountType": {
      "value": "" // This value is optional. Template will use default value if not provided.
    }
  }
}
```

Na koniec zapoznaj się z dozwolonymi wartościami i wszelkimi ograniczeniami, takimi jak Maksymalna długość. Informują one zakres wartości, które można podać dla parametru.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "storage"
    },
    "storageAccountType": {
      "value": "Standard_ZRS"
    }
  }
}
```

Plik parametrów może zawierać tylko wartości parametrów, które są zdefiniowane w szablonie. Jeśli plik parametrów zawiera dodatkowe parametry, które nie pasują do parametrów w szablonie, zostanie wyświetlony komunikat o błędzie.

## <a name="parameter-type-formats"></a>Formaty typów parametrów

W poniższym przykładzie przedstawiono formaty różnych typów parametrów.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "exampleString": {
      "value": "test string"
    },
    "exampleInt": {
      "value": 4
    },
    "exampleBool": {
      "value": true
    },
    "exampleArray": {
      "value": [
        "value 1",
        "value 2"
      ]
    },
    "exampleObject": {
      "value": {
        "property1": "value1",
        "property2": "value2"
      }
    }
   }
}
```

## <a name="deploy-template-with-parameter-file"></a>Wdróż szablon z plikiem parametrów

Aby przekazać lokalny plik parametrów za pomocą interfejsu wiersza polecenia platformy Azure, użyj @ i nazwy pliku parametrów.

```azurecli
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów przy użyciu szablonów ARM i interfejsu wiersza polecenia platformy Azure](./deploy-cli.md#parameters).

Aby przekazać lokalny plik parametrów Azure PowerShell, użyj `TemplateParameterFile` parametru.

```azurepowershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -TemplateParameterFile c:\MyTemplates\storage.parameters.json
```

Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów przy użyciu szablonów ARM i Azure PowerShell](./deploy-powershell.md#pass-parameter-values)

> [!NOTE]
> Nie można użyć pliku parametrów z blokiem szablonu niestandardowego w portalu.

## <a name="file-name"></a>Nazwa pliku

Ogólną konwencją nazewnictwa pliku parametrów jest dodawanie **parametrów** do nazwy szablonu. Na przykład jeśli szablon ma nazwę **azuredeploy.jsna**, plik parametrów ma nazwę **azuredeploy.parameters.jsna**. Ta konwencja nazewnictwa ułatwia wyświetlanie połączenia między szablonem i parametrami.

Aby wdrożyć w różnych środowiskach, należy utworzyć więcej niż jeden plik parametrów. Podczas nadawania nazwy plikowi parametrów Dodaj sposób, aby zidentyfikować jego użycie. Na przykład użyj **azuredeploy.parameters-dev.jsna** i **azuredeploy.parameters-prod.jsna**

## <a name="parameter-precedence"></a>Pierwszeństwo parametrów

W tej samej operacji wdrażania można używać wbudowanych parametrów i pliku parametrów lokalnych. Na przykład można określić niektóre wartości w lokalnym pliku parametrów i dodać inne wartości w tekście podczas wdrażania. Jeśli podano wartości dla parametru zarówno w pliku parametrów lokalnych, jak i wewnętrznie, wartość śródwierszowa ma pierwszeństwo.

Można użyć zewnętrznego pliku parametrów, dostarczając identyfikator URI do pliku. W przypadku korzystania z zewnętrznego pliku parametrów nie można przekazać innych wartości wbudowanych lub z pliku lokalnego. Wszystkie wbudowane parametry są ignorowane. Podaj wszystkie wartości parametrów w pliku zewnętrznym.

## <a name="parameter-name-conflicts"></a>Konflikty nazw parametrów

Jeśli szablon zawiera parametr o takiej samej nazwie jak jeden z parametrów w poleceniu programu PowerShell, program PowerShell przedstawia parametr z szablonu przy użyciu przyrostka **FromTemplate**. Na przykład parametr o nazwie **ResourceGroupName** w szablonie powoduje konflikt z parametrem **ResourceGroupName** w poleceniu cmdlet [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) . Zostanie wyświetlony monit o podanie wartości dla **ResourceGroupNameFromTemplate**. Można uniknąć tego nieporozumień przy użyciu nazw parametrów, które nie są używane dla poleceń wdrażania.


## <a name="next-steps"></a>Następne kroki

- Aby zrozumieć, jak definiować parametry w szablonie, zobacz [Parametry w szablonach Azure Resource Manager](template-parameters.md).
- Aby uzyskać więcej informacji o korzystaniu z wartości z magazynu kluczy, zobacz [używanie Azure Key Vault do przekazywania zabezpieczonej wartości parametrów podczas wdrażania](key-vault-parameter.md).
- Aby uzyskać więcej informacji na temat parametrów, zobacz [Parametry w szablonach Azure Resource Manager](template-parameters.md).
