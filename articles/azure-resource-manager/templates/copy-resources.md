---
title: Wdrażanie wielu wystąpień zasobów
description: Użyj operacji kopiowania i tablic w szablonie Azure Resource Manager (szablon usługi ARM), aby wielokrotnie wdrożyć typ zasobu.
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 5ddb0cabf0acae1ffe9b9e77e6defa70f9cbd61b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479971"
---
# <a name="resource-iteration-in-arm-templates"></a>Iteracja zasobów w szablonach arm

W tym artykule pokazano, jak utworzyć więcej niż jedno wystąpienie zasobu w szablonie usługi Azure Resource Manager (szablon usługi ARM). Dodając pętlę kopiowania do sekcji zasobów szablonu, możesz dynamicznie ustawić liczbę zasobów do wdrożenia. Należy również unikać powtarzania składni szablonu.

Możesz również użyć pętli kopiowania z [właściwościami](copy-properties.md), [zmiennymi](copy-variables.md)i [wyjściami](copy-outputs.md).

Jeśli musisz określić, czy zasób jest w ogóle wdrażany, zobacz [warunek element](conditional-resource-deployment.md).

## <a name="syntax"></a>Składnia

# <a name="json"></a>[JSON](#tab/json)

Dodaj `copy` element do sekcji resources szablonu, aby wdrożyć wiele wystąpień zasobu. Element `copy` ma następujący ogólny format:

```json
"copy": {
  "name": "<name-of-loop>",
  "count": <number-of-iterations>,
  "mode": "serial" <or> "parallel",
  "batchSize": <number-to-deploy-serially>
}
```

Właściwość `name` jest dowolną wartością, która identyfikuje pętlę. Właściwość `count` określa liczbę iteracji dla typu zasobu.

Użyj właściwości `mode` i `batchSize` , aby określić, czy zasoby są wdrażane równolegle, czy w sekwencji. Te właściwości są opisane w serial [lub parallel](#serial-or-parallel).

# <a name="bicep"></a>[Bicep](#tab/bicep)

Pętle mogą być używane do deklarowania wielu zasobów przez:

- Iterowanie po tablicy:

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
    <resource-properties>
  }]
  ```

- Iterowanie po elementach tablicy

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for (<item>, <index>) in <collection>: {
    <resource-properties>
  }]
  ```

- Używanie indeksu pętli

  ```bicep
  @batchSize(<number>)
  resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <index> in range(<start>, <stop>): {
    <resource-properties>
  }]
  ```

---

## <a name="copy-limits"></a>Limity kopiowania

Ta liczba nie może przekraczać 800.

Liczba nie może być liczbą ujemną. W przypadku wdrażania szablonu przy użyciu najnowszej wersji interfejsu wiersza polecenia platformy Azure, programu PowerShell lub interfejsu API REST może być zerowa. W szczególności należy użyć:

- Azure PowerShell **2.6** lub nowszy
- Interfejs wiersza polecenia platformy Azure **w wersji 2.0.74** lub nowszej
- Interfejs API REST **w wersji 2019-05-10** lub nowszej
- [Połączone wdrożenia muszą](linked-templates.md) używać interfejsu API w wersji **2019-05-10 lub** nowszej dla typu zasobu wdrożenia

Wcześniejsze wersje programu PowerShell, interfejsu wiersza polecenia i interfejsu API REST nie obsługują zera dla liczby.

Należy zachować ostrożność podczas [wdrażania w trybie kompletnym z](deployment-modes.md) pętlą kopiowania. W przypadku ponownego wykonania z trybem ukończenia do grupy zasobów wszystkie zasoby, które nie zostały określone w szablonie po rozwiązaniu pętli kopiowania, zostaną usunięte.

## <a name="resource-iteration"></a>Iteracja zasobu

Poniższy przykład tworzy liczbę kont magazynu określoną w `storageCount` parametrze .

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

Zwróć uwagę, że nazwa każdego zasobu zawiera funkcję `copyIndex()` , która zwraca bieżącą iterację w pętli. Funkcja `copyIndex()` rozpoczyna liczenie od zera. W poniższym przykładzie:

```json
"name": "[concat('storage', copyIndex())]",
```

Tworzy następujące nazwy:

- magazyn0
- storage1
- storage2.

Aby przesunięć wartość indeksu, można przekazać wartość w `copyIndex()` funkcji . Liczba iteracji jest nadal określona w elemencie kopiowania, ale wartość jest `copyIndex` przesunięta o określoną wartość. Tak więc w poniższym przykładzie:

```json
"name": "[concat('storage', copyIndex(1))]",
```

Tworzy następujące nazwy:

- magazyn1
- magazyn2
- magazyn3

Operacja kopiowania jest przydatna podczas pracy z tablicami, ponieważ można iterować po poszczególnych elementach tablicy. Użyj funkcji w tablicy, aby określić liczbę iteracji i pobrać `length` `copyIndex` bieżący indeks w tablicy.

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

Zwróć uwagę, że `i` indeks jest używany podczas tworzenia nazwy zasobu konta magazynu.

---

Poniższy przykład tworzy jedno konto magazynu dla każdej nazwy podanej w parametrze .

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

Jeśli chcesz zwrócić wartości z wdrożonych zasobów, możesz użyć funkcji [kopiowania w sekcji danych wyjściowych](copy-outputs.md).

## <a name="serial-or-parallel"></a>Szeregowe lub równoległe

Domyślnie program Resource Manager zasoby równolegle. Nie stosuje żadnego limitu do liczby zasobów wdrożonych równolegle poza łącznym limitem 800 zasobów w szablonie. Kolejność ich tworzenia nie jest gwarantowana.

Można jednak określić, że zasoby są wdrażane kolejno. Na przykład podczas aktualizowania środowiska produkcyjnego możesz chcieć rozsyłać aktualizacje tak, aby tylko pewna liczba została zaktualizowana w dowolnym momencie.

Aby na przykład wdrażać szeregowo konta magazynu po dwa na raz, użyj:

# <a name="json"></a>[JSON](#tab/json)

Aby wdrożyć szeregowo więcej niż jedno wystąpienie zasobu, ustaw na wartość szeregowa i na liczbę wystąpień do `mode` wdrożenia w  `batchSize` czasie. W trybie szeregowym Resource Manager zależność od wcześniejszych wystąpień w pętli, więc nie uruchamia jednej partii przed zakończeniem poprzedniej partii.

Wartość elementu `batchSize` nie może przekraczać wartości dla w `count` elemencie kopiowania.

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

Właściwość `mode` akceptuje również wartość **parallel**, która jest wartością domyślną.

# <a name="bicep"></a>[Bicep](#tab/bicep)

Aby wdrożyć szeregowo więcej niż jedno wystąpienie zasobu, ustaw dekorator na liczbę wystąpień do wdrożenia `batchSize` [](./bicep-file.md#resource-and-module-decorators) w tym samym czasie. W trybie szeregowym Resource Manager zależność od wcześniejszych wystąpień w pętli, więc nie uruchamia jednej partii, dopóki poprzednia partia nie zostanie ukończona.

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

Nie można użyć pętli kopiowania dla zasobu podrzędnego. Aby utworzyć więcej niż jedno wystąpienie zasobu, które zwykle definiuje się jako zagnieżdżone w innym zasobie, należy zamiast tego utworzyć ten zasób jako zasób najwyższego poziomu. Relację z zasobem nadrzędnym definiuje się za pomocą właściwości typu i nazwy.

Załóżmy na przykład, że zazwyczaj definiujesz zestaw danych jako zasób podrzędny w fabryce danych.

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

Aby utworzyć więcej niż jeden zestaw danych, przenieś go poza fabrykę danych. Zestaw danych musi być na tym samym poziomie co fabryka danych, ale nadal jest zasobem podrzędnym fabryki danych. Relacja między zestawem danych i fabryką danych jest zachowywana za pomocą właściwości typu i nazwy. Ponieważ nie można już wywnioskować typu na podstawie jego pozycji w szablonie, należy podać w pełni kwalifikowany typ w formacie: `{resource-provider-namespace}/{parent-resource-type}/{child-resource-type}` .

Aby ustanowić relację nadrzędny/podrzędny z wystąpieniem fabryki danych, podaj nazwę zestawu danych, który zawiera nazwę zasobu nadrzędnego. Użyj formatu: `{parent-resource-name}/{child-resource-name}`.

W poniższym przykładzie pokazano implementację:

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
|[Kopiowanie magazynu](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystorage.json) |Wdraża więcej niż jedno konto magazynu z numerem indeksu w nazwie. |
|[Magazyn kopii szeregowej](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/serialcopystorage.json) |Wdraża kilka kont magazynu po jednym na raz. Nazwa zawiera numer indeksu. |
|[Kopiowanie magazynu z tablicą](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/multipleinstance/copystoragewitharray.json) |Wdraża kilka kont magazynu. Nazwa zawiera wartość z tablicy. |

## <a name="next-steps"></a>Następne kroki

- Aby ustawić zależności od zasobów utworzonych w pętli kopiowania, zobacz Define the order for deploying resources in ARM templates (Definiowanie kolejności [wdrażania zasobów w szablonach usługi ARM).](define-resource-dependency.md)
- Aby przejść przez samouczek, zobacz [Samouczek: tworzenie wielu wystąpień zasobów za pomocą szablonów usługi ARM.](template-tutorial-create-multiple-instances.md)
- Aby uzyskać Microsoft Learn, który obejmuje kopię zasobów, zobacz [Manage complex cloud deployments by using advanced ARM template features](/learn/modules/manage-deployments-advanced-arm-template-features/)(Zarządzanie złożonymi wdrożeniami w chmurze przy użyciu zaawansowanych funkcji szablonu usługi ARM).
- W przypadku innych zastosowań pętli kopiowania zobacz:
  - [Iteracja właściwości w szablonach arm](copy-properties.md)
  - [Iteracja zmiennej w szablonach arm](copy-variables.md)
  - [Iteracja danych wyjściowych w szablonach arm](copy-outputs.md)
- Aby uzyskać informacje o używaniu kopiowania z zagnieżdżonych szablonów, zobacz [Using copy](linked-templates.md#using-copy).
