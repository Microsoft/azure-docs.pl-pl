---
title: Tworzenie pliku parametrów
description: Tworzenie pliku parametrów do przekazywania wartości podczas wdrażania szablonu usługi Azure Resource Manager
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: a1a1f703594f8eaa572ea38ecef88b4cd6ba5a4b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682892"
---
# <a name="create-resource-manager-parameter-file"></a>Tworzenie pliku parametrów Menedżera zasobów

Zamiast przekazywania parametrów jako wartości wbudowanych w skrypcie, może być łatwiej użyć pliku JSON, który zawiera wartości parametrów. W tym artykule pokazano, jak utworzyć plik parametrów.

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

Należy zauważyć, że wartości parametrów są przechowywane jako zwykły tekst w pliku parametrów. To podejście działa dla wartości, które nie są poufne, takie jak określanie jednostki SKU dla zasobu. Nie działa dla wartości poufnych, takich jak hasła. Jeśli chcesz przekazać wartość wrażliwą jako parametr, należy zapisać wartość w magazynie kluczy i odwołać się do magazynu kluczy w pliku parametrów. Wartość poufnych jest bezpiecznie pobierana podczas wdrażania.

Następujący plik parametru zawiera wartość zwykłego tekstu i wartość, która jest przechowywana w magazynie kluczy.

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

Aby uzyskać więcej informacji na temat używania wartości z magazynu kluczy, zobacz Przekazywanie wartości bezpiecznego parametru podczas wdrażania za pomocą usługi Azure Key Vault do [przekazywania wartości bezpiecznego parametru.](key-vault-parameter.md)

## <a name="define-parameter-values"></a>Definiowanie wartości parametrów

Aby dowiedzieć się, jak zdefiniować wartości parametrów, otwórz wdrażany szablon. Spójrz na sekcję parametrów szablonu. W poniższym przykładzie przedstawiono parametry z szablonu.

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

Pierwszym szczegółem, który należy zauważyć, jest nazwa każdego parametru. Wartości w pliku parametrów muszą być zgodne z nazwami.

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

Zwróć uwagę na typ parametru. Wartości w pliku parametrów muszą mieć te same typy. Dla tego szablonu można podać oba parametry jako ciągi.

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

Następnie poszukaj wartości domyślnej. Jeśli parametr ma wartość domyślną, można podać wartość, ale nie trzeba.

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

Na koniec spójrz na dozwolone wartości i wszelkie ograniczenia, takie jak maksymalna długość. Informują one zakres wartości, które można podać dla parametru.

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

## <a name="file-name"></a>Nazwa pliku

Ogólną konwencją nazewnictwa pliku parametrów jest dodanie **parametrów** do nazwy szablonu. Na przykład jeśli szablon nosi nazwę **azuredeploy.json,** plik parametru nosi nazwę **azuredeploy.parameters.json**. Ta konwencja nazewnictwa ułatwia wyświetlenie połączenia między szablonem a parametrami.

Aby wdrożyć w różnych środowiskach, należy utworzyć więcej niż jeden plik parametrów. Podczas nazywania pliku parametrów dodaj sposób identyfikowania jego użycia. Na przykład użyj **azuredeploy.parameters-dev.json** i **azuredeploy.parameters-prod.json**


## <a name="parameter-precedence"></a>Pierwszeństwo parametru

W tej samej operacji wdrażania można użyć parametrów wbudowanych i lokalnego pliku parametrów. Na przykład można określić niektóre wartości w pliku parametrów lokalnych i dodać inne wartości w linii podczas wdrażania. Jeśli podasz wartości parametru zarówno w pliku parametru lokalnego, jak i w inline, wartość wbudowana ma pierwszeństwo.

Jednak podczas korzystania z pliku parametrów zewnętrznych, nie można przekazać inne wartości w linii lub z pliku lokalnego. Wszystkie parametry wbudowane są ignorowane. Podaj wszystkie wartości parametrów w pliku zewnętrznym.

## <a name="parameter-name-conflicts"></a>Konflikty nazw parametrów

Jeśli szablon zawiera parametr o takiej samej nazwie jak jeden z parametrów w poleceniu programu PowerShell, program PowerShell przedstawia parametr z szablonu z postfix **FromTemplate**. Na przykład parametr o nazwie **ResourceGroupName** w szablonie powoduje konflikt z parametrem **ResourceGroupName** w poleceniu cmdlet [New-AzResourceGroupDeployment.](/powershell/module/az.resources/new-azresourcegroupdeployment) Zostanie wyświetlony monit o podanie wartości **resourcegroupnamefromtemplate**. Można uniknąć tego zamieszania przy użyciu nazw parametrów, które nie są używane dla poleceń wdrażania.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak zdefiniować parametry w szablonie, zobacz [Parametry w szablonach usługi Azure Resource Manager](template-parameters.md).
- Aby uzyskać więcej informacji na temat używania wartości z magazynu kluczy, zobacz Przekazywanie wartości bezpiecznego parametru podczas wdrażania za pomocą usługi Azure Key Vault do [przekazywania wartości bezpiecznego parametru.](key-vault-parameter.md)
- Aby uzyskać więcej informacji o parametrach, zobacz [Parametry w szablonach usługi Azure Resource Manager](template-parameters.md).
