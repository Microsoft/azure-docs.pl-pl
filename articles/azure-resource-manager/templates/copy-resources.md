---
title: Wdróż wiele wystąpień zasobów
description: Użyj operacji kopiowania i tablic w szablonie Azure Resource Manager (szablon ARM), aby wdrożyć wiele razy typ zasobu.
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 3af676cce544c125e441857f06556b9ff7eee697
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385716"
---
# <a name="resource-iteration-in-arm-templates"></a>Iteracja zasobów w szablonach ARM

W tym artykule pokazano, jak utworzyć więcej niż jedno wystąpienie zasobu w szablonie Azure Resource Manager (szablon ARM). Przez dodanie pętli Copy do sekcji Resources szablonu można dynamicznie ustawić liczbę zasobów do wdrożenia. Należy również unikać powtarzania składni szablonu.

Możesz również użyć pętli kopiowania z [właściwościami](copy-properties.md), [zmiennymi](copy-variables.md)i [wyjściami](copy-outputs.md).

Jeśli musisz określić, czy zasób został wdrożony w ogóle, zobacz [warunek elementu](conditional-resource-deployment.md).

## <a name="syntax"></a>Składnia

# <a name="json"></a>[JSON](#tab/json)

Dodaj `copy` element do sekcji Resources szablonu, aby wdrożyć wiele wystąpień zasobu. `copy`Element ma następujący format ogólny:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

`name`Właściwość jest dowolną wartością, która identyfikuje pętlę. `count`Właściwość określa liczbę iteracji dla typu zasobu.

Użyj `mode` właściwości i, `batchSize` Aby określić, czy zasoby są wdrażane równolegle czy w kolejności. Te właściwości są opisane w [numerach seryjnych lub równoległych](#serial-or-parallel).

# <a name="bicep"></a>[Bicep](#tab/bicep)

Za pomocą pętli można zadeklarować wiele zasobów według:

- Iteracja w tablicy:

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
    <resource-properties>
  }
  ```

- Iterowanie elementów tablicy

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for (<item>, <index>) in <collection>: {
    <resource-properties>
  }
  ```

- Używanie indeksu pętli

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <index> in range(<start>, <stop>): {
    <resource-properties>
  }
  ```

---

## <a name="copy-limits"></a>Limity kopiowania

Liczba nie może przekraczać 800.

Liczba nie może być liczbą ujemną. Może to być zero, jeśli szablon zostanie wdrożony przy użyciu najnowszej wersji interfejsu wiersza polecenia platformy Azure, programu PowerShell lub API REST. W tym celu należy użyć:

- Azure PowerShell **2,6** lub nowszy
- Interfejs wiersza polecenia platformy Azure **2.0.74** lub nowszy
- Interfejs API REST w wersji **2019-05-10** lub nowszej
- [Połączone wdrożenia](linked-templates.md) muszą używać interfejsu API w wersji **2019-05-10** lub nowszej dla typu zasobu wdrożenia

We wcześniejszych wersjach programu PowerShell, interfejsu wiersza polecenia i interfejsie API REST nie są obsługiwane wartości zerowe.

Należy zachować ostrożność przy użyciu [wdrożenia w trybie pełnym](deployment-modes.md) z pętlą kopiowania. W przypadku ponownego wdrożenia z trybem kompletnym do grupy zasobów wszystkie zasoby, które nie są określone w szablonie po usunięciu pętli kopiowania, zostaną usunięte.

## <a name="resource-iteration"></a>Iteracja zasobu

Poniższy przykład tworzy liczbę kont magazynu określonych w `storageCount` parametrze.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageCount": {
      "type": "int",
      "defaultValue": 2
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[parameters('storageCount')]"
      }
    }
  ]
}
```

Należy zauważyć, że nazwa każdego zasobu zawiera `copyIndex()` funkcję, która zwraca bieżącą iterację w pętli. Funkcja `copyIndex()` rozpoczyna liczenie od zera. Tak więc, Poniższy przykład:

```json
"name": "[concat('storage', copyIndex())]",
```

Tworzy następujące nazwy:

- storage0
- storage1
- storage2.

Aby przesunięciu wartość indeksu, można przekazać wartość w `copyIndex()` funkcji. Liczba iteracji jest nadal określona w elemencie Copy, ale wartość `copyIndex` jest przesunięta przez określoną wartość. Tak więc, Poniższy przykład:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Tworzy następujące nazwy:

- storage1
- storage2
- storage3

Operacja kopiowania jest przydatna podczas pracy z tablicami, ponieważ można wykonać iterację każdego elementu w tablicy. Użyj `length` funkcji w tablicy, aby określić liczbę iteracji i `copyIndex` pobrać bieżący indeks tablicy.

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageCount int = 2

resource storage_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, storageCount): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

Zauważ, że indeks `i` jest używany podczas tworzenia nazwy zasobu konta magazynu.

---

Poniższy przykład tworzy jedno konto magazynu dla każdej nazwy podanej w parametrze.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
      "storageNames": {
          "type": "array",
          "defaultValue": [
            "contoso",
            "fabrikam",
            "coho"
          ]
      }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(parameters('storageNames')[copyIndex()], uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {},
      "copy": {
        "name": "storagecopy",
        "count": "[length(parameters('storageNames'))]"
      }
    }
  ],
  "outputs": {}
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
param storageNames array = [
  'contoso'
  'fabrikam'
  'coho'
]

resource storageNames_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for name in storageNames: {
  name: concat(name, uniqueString(resourceGroup().id))
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

---

Jeśli chcesz zwrócić wartości ze wdrożonych zasobów, możesz użyć opcji [Kopiuj w sekcji dane wyjściowe](copy-outputs.md).

## <a name="serial-or-parallel"></a>Numer seryjny lub równoległy

Domyślnie Menedżer zasobów tworzy zasoby równolegle. Nie ma żadnego limitu liczby zasobów wdrożonych równolegle, poza całkowitym limitem 800 zasobów w szablonie. Kolejność, w której są tworzone, nie jest gwarantowana.

Można jednak określić, że zasoby są wdrażane w sekwencji. Na przykład podczas aktualizowania środowiska produkcyjnego warto rozłożyć aktualizacje, aby w dowolnym momencie zaktualizować tylko określoną liczbę.

Na przykład aby przeprowadzić sekwencyjne wdrażanie kont magazynu dwa naraz, należy użyć:

# <a name="json"></a>[JSON](#tab/json)

Aby przeprowadzić szeregowo wdrożenie więcej niż jednego wystąpienia zasobu, należy ustawić `mode` wartość **serial** oraz `batchSize` liczbę wystąpień do wdrożenia w danym momencie. W trybie serial Menedżer zasobów tworzy zależność od wcześniejszych wystąpień w pętli, dlatego nie uruchamia jednej partii do momentu zakończenia poprzedniej partii.

Wartość parametru `batchSize` nie może przekroczyć wartości `count` elementu w elemencie Copy.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-04-01",
      "name": "[concat(copyIndex(),'storage', uniqueString(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "copy": {
        "name": "storagecopy",
        "count": 4,
        "mode": "serial",
        "batchSize": 2
      },
      "properties": {}
    }
  ],
  "outputs": {}
}
```

`mode`Właściwość akceptuje również **Parallel**, która jest wartością domyślną.

# <a name="bicep"></a>[Bicep](#tab/bicep)

Aby przeprowadzić szeregowo wdrożenie więcej niż jednego wystąpienia zasobu, należy ustawić `batchSize` [dekoratora](./bicep-file.md#resource-and-module-decorators) na liczbę wystąpień do wdrożenia w danym momencie. W trybie serial Menedżer zasobów tworzy zależność od wcześniejszych wystąpień w pętli, dlatego nie uruchamia jednej partii do momentu zakończenia poprzedniej partii.

```bicep
@batchSize(2)
resource storage_id 'Microsoft.Storage/storageAccounts@2019-04-01' = [for i in range(0, 4): {
  name: '${i}storage${uniqueString(resourceGroup().id)}'
  location: resourceGroup().location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  properties: {}
}]
```

---

## <a name="iteration-for-a-child-resource"></a>Iteracja zasobu podrzędnego

Nie można użyć pętli kopiowania dla zasobu podrzędnego. Aby utworzyć więcej niż jedno wystąpienie zasobu, który jest zwykle definiowany jako zagnieżdżony w innym zasobie, należy zamiast tego utworzyć ten zasób jako zasób najwyższego poziomu. Relację z zasobem nadrzędnym definiuje się przy użyciu właściwości typu i nazwy.

Załóżmy na przykład, że zwykle zdefiniujesz zestaw danych jako zasób podrzędny w fabryce danych.

```json
"resources": [
{
  "type": "Microsoft.DataFactory/factories",
  "name": "exampleDataFactory",
  ...
  "resources": [
    {
      "type": "datasets",
      "name": "exampleDataSet",
      "dependsOn": [
        "exampleDataFactory"
      ],
      ...
    }
  ]
```

Aby utworzyć więcej niż jeden zestaw danych, przenieś go poza fabrykę danych. Zestaw danych musi znajdować się na tym samym poziomie co Fabryka danych, ale nadal jest zasobem podrzędnym fabryki danych. Relację między zestawem danych a fabryką danych można zachować za pomocą właściwości Typ i nazwa. Ponieważ typ nie może być już wywnioskowany na podstawie jego pozycji w szablonie, należy podać w formacie w pełni kwalifikowany typ: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}` .

Aby ustanowić relację nadrzędną/podrzędną z wystąpieniem fabryki danych, podaj nazwę zestawu danych, który zawiera nazwę zasobu nadrzędnego. Użyj formatu: `{parent-resource-name}/{child-resource-name}`.

W poniższym przykładzie przedstawiono implementację:

# <a name="json"></a>[JSON](#tab/json)

```json
"resources": [
{
  "type": "Microsoft.DataFactory/factories",
  "name": "exampleDataFactory",
  ...
},
{
  "type": "Microsoft.DataFactory/factories/datasets",
  "name": "[concat('exampleDataFactory', '/', 'exampleDataSet', copyIndex())]",
  "dependsOn": [
    "exampleDataFactory"
  ],
  "copy": {
    "name": "datasetcopy",
    "count": "3"
  },
  ...
}]
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource dataFactoryName_resource 'Microsoft.DataFactory/factories@2018-06-01' = {
  name: "exampleDataFactory"
  ...
}

resource dataFactoryName_ArmtemplateTestDatasetIn 'Microsoft.DataFactory/factories/datasets@2018-06-01' = [for i in range(0, 3): {
  name: 'exampleDataFactory/exampleDataset${i}'
  ...
}
```

---

## <a name="example-templates"></a>Przykładowe szablony

W poniższych przykładach przedstawiono typowe scenariusze tworzenia więcej niż jednego wystąpienia zasobu lub właściwości.

|Template  |Opis  |
|---------|---------|
|[Kopiuj magazyn](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Wdraża więcej niż jedno konto magazynu o numerze indeksu w nazwie. |
|[Magazyn kopii seryjnych](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Wdraża kilka kont magazynu jeden w czasie. Nazwa zawiera numer indeksu. |
|[Kopiuj magazyn z tablicą](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Wdraża kilka kont magazynu. Nazwa zawiera wartość z tablicy. |

## <a name="next-steps"></a>Następne kroki

- Aby ustawić zależności dla zasobów utworzonych w pętli kopiowania, zobacz [Definiowanie kolejności wdrażania zasobów w usłudze ARM](define-resource-dependency.md).
- Aby przejść przez samouczek, zobacz [Samouczek: Tworzenie wielu wystąpień zasobów przy użyciu szablonów ARM](template-tutorial-create-multiple-instances.md).
- W przypadku modułu Microsoft Learn, który obejmuje kopiowanie zasobów, zobacz [zarządzanie złożonymi wdrożeniami w chmurze za pomocą zaawansowanych funkcji szablonów usługi ARM](/learn/modules/manage-deployments-advanced-arm-template-features/).
- Inne zastosowania pętli Copy można znaleźć w temacie:
  - [Iteracja właściwości w szablonach ARM](copy-properties.md)
  - [Iteracja zmiennej w szablonach ARM](copy-variables.md)
  - [Iteracja danych wyjściowych w szablonach ARM](copy-outputs.md)
- Aby uzyskać informacje na temat używania kopiowania z szablonami zagnieżdżonymi, zobacz [using Copy](linked-templates.md#using-copy).
