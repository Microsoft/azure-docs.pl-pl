---
title: Tworzenie pliku parametrów
description: Tworzenie pliku parametrów do przekazywania wartości podczas wdrażania Azure Resource Manager szablonu
ms.topic: conceptual
ms.date: 04/15/2021
ms.openlocfilehash: ddeaed94396aa662b795ae5701aa367ba13d869b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531215"
---
# <a name="create-resource-manager-parameter-file"></a>Tworzenie Resource Manager parametrów

Zamiast przekazywania parametrów jako wartości w tekście w skrypcie, można użyć pliku JSON zawierającego wartości parametrów. W tym artykule pokazano, jak utworzyć plik parametrów, który będzie używać szablonu JSON lub pliku Bicep.

## <a name="parameter-file"></a>Plik parametrów

Plik parametrów używa następującego formatu:

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

Zwróć uwagę, że w pliku parametrów wartości parametrów są przechowywane jako zwykły tekst. To podejście działa w przypadku wartości, które nie są poufne, takich jak sku zasobów. Zwykły tekst nie działa w przypadku wartości poufnych, takich jak hasła. Jeśli musisz przekazać parametr zawierający wartość wrażliwą, przechowaj wartość w magazynie kluczy. Następnie odwołaj się do magazynu kluczy w pliku parametrów. Wartość wrażliwa jest bezpiecznie pobierana podczas wdrażania.

Poniższy plik parametrów zawiera wartość w postaci zwykłego tekstu i wartość wrażliwą przechowywaną w magazynie kluczy.

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

Aby uzyskać więcej informacji na temat używania wartości z magazynu kluczy, zobacz Use Azure Key Vault to pass secure parameter value during deployment (Używanie programu Azure Key Vault do przekazania wartości bezpiecznego [parametru podczas wdrażania).](key-vault-parameter.md)

## <a name="define-parameter-values"></a>Definiowanie wartości parametrów

Aby określić sposób definiowania nazw parametrów i wartości, otwórz szablon JSON lub Bicep. Przyjrzyj się sekcji parameters szablonu. W poniższych przykładach przedstawiono parametry z szablonów JSON i Bicep.

# <a name="json"></a>[JSON](#tab/json)

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

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageAccountType string = 'Standard_LRS'
```

---

W pliku parametrów pierwszymi szczegółami, które należy zauważyć, jest nazwa każdego parametru. Nazwy parametrów w pliku parametrów muszą być zgodne z nazwami parametrów w szablonie.

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

Zwróć uwagę na typ parametru . Typy parametrów w pliku parametrów muszą używać tych samych typów co szablon. W tym przykładzie oba typy parametrów są ciągami.

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

Sprawdź w szablonie parametry z wartością domyślną. Jeśli parametr ma wartość domyślną, możesz podać wartość w pliku parametrów, ale nie jest to wymagane. Wartość pliku parametrów zastępuje wartość domyślną szablonu.

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

Sprawdź dozwolone wartości szablonu i wszelkie ograniczenia, takie jak maksymalna długość. Te wartości określają zakres wartości, które można podać dla parametru. W tym przykładzie `storagePrefix` może mieć maksymalnie 11 znaków i `storageAccountType` musi określać dozwoloną wartość.

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

> [!NOTE]
> Plik parametrów może zawierać tylko wartości parametrów zdefiniowanych w szablonie. Jeśli plik parametrów zawiera dodatkowe parametry, które nie pasują do parametrów szablonu, zostanie wyświetlony błąd.

## <a name="parameter-type-formats"></a>Formaty typów parametrów

W poniższym przykładzie przedstawiono formaty różnych typów parametrów: ciąg, liczba całkowita, wartość logiczna, tablica i obiekt.

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

## <a name="deploy-template-with-parameter-file"></a>Wdrażanie szablonu za pomocą pliku parametrów

Z interfejsu wiersza polecenia platformy Azure przekaż lokalny plik parametrów przy użyciu polecenia `@` i nazwę pliku parametrów. Na przykład `@storage.parameters.json`.

```azurecli
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Aby uzyskać więcej informacji, zobacz [Deploy resources with ARM templates and Azure CLI (Wdrażanie zasobów za pomocą szablonów usługi ARM i interfejsu wiersza polecenia platformy Azure).](./deploy-cli.md#parameters) Do wdrożenia _plików bicep potrzebny_ jest interfejs wiersza polecenia platformy Azure w wersji 2.20 lub wyższej.

Z Azure PowerShell przekaż lokalny plik parametrów przy użyciu `TemplateParameterFile` parametru .

```azurepowershell
New-AzResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup `
  -TemplateFile C:\MyTemplates\storage.json `
  -TemplateParameterFile C:\MyTemplates\storage.parameters.json
```

Aby uzyskać więcej informacji, zobacz [Deploy resources with ARM templates and Azure PowerShell](./deploy-powershell.md#pass-parameter-values)(Wdrażanie zasobów za pomocą szablonów usługi ARM i Azure PowerShell ). Aby _wdrożyć pliki bicep,_ Azure PowerShell w wersji 5.6.0 lub wyższej.

> [!NOTE]
> Nie można użyć pliku parametrów z blokiem szablonu niestandardowego w portalu.

> [!TIP]
> Jeśli używasz projektu grupy zasobów [platformy Azure](create-visual-studio-deployment-project.md)w usłudze Visual Studio , upewnij się, że plik parametrów ma akcję kompilacji ustawioną na wartość **Zawartość.** 

## <a name="file-name"></a>Nazwa pliku

Ogólna konwencja nazewnictwa dla pliku parametrów obejmuje _parametry_ w nazwie szablonu. Jeśli na przykład szablon ma nazwęazuredeploy.js _,_ plik parametrów ma nazwęazuredeploy.parameters.js _na_. Ta konwencja nazewnictwa pomaga zobaczyć połączenie między szablonem i parametrami.

Aby wdrożyć w różnych środowiskach, należy utworzyć więcej niż jeden plik parametrów. Gdy nadasz plikom parametrów nazwę, zidentyfikuj ich użycie, takie jak tworzenie i produkcja. Na przykład użyjazuredeploy.parameters-dev.js _i_ _azuredeploy.parameters-prod.js,_ aby wdrożyć zasoby.

## <a name="parameter-precedence"></a>Pierwszeństwo parametrów

W tej samej operacji wdrażania można użyć parametrów w tekście i pliku parametrów lokalnych. Na przykład można określić niektóre wartości w lokalnym pliku parametrów i dodać inne wartości w tekście podczas wdrażania. W przypadku podania wartości parametru zarówno w lokalnym pliku parametrów, jak i w tekście, pierwszeństwo ma wartość w tekście.

Można użyć zewnętrznego pliku parametrów, podając w pliku wartość URI. W przypadku użycia zewnętrznego pliku parametrów nie można przekazać innych wartości w tekście ani z pliku lokalnego. Wszystkie parametry wbudowane są ignorowane. Podaj wszystkie wartości parametrów w pliku zewnętrznym.

## <a name="parameter-name-conflicts"></a>Konflikty nazw parametrów

Jeśli szablon zawiera parametr o takiej samej nazwie jak jeden z parametrów w poleceniu programu PowerShell, program PowerShell przedstawia parametr z szablonu z przyrostką `FromTemplate` . Na przykład parametr o nazwie w szablonie powoduje konflikt z `ResourceGroupName` `ResourceGroupName` parametrem polecenia cmdlet [New-AzResourceGroupDeployment.](/powershell/module/az.resources/new-azresourcegroupdeployment) Zostanie wyświetlony monit o podanie wartości dla `ResourceGroupNameFromTemplate` . Aby uniknąć tego nieporozumień, użyj nazw parametrów, które nie są używane w poleceniach wdrażania.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat definiowania parametrów w szablonie, zobacz [Parametry w szablonach usługi ARM.](template-parameters.md)
- Aby uzyskać więcej informacji na temat używania wartości z magazynu kluczy, zobacz Use Azure Key Vault to pass secure parameter value during deployment (Używanie usługi Azure Key Vault do przekazania wartości bezpiecznego [parametru podczas wdrażania.](key-vault-parameter.md)
