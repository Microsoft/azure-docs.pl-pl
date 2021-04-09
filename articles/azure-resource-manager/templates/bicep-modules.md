---
title: Moduły Bicep
description: Opisuje sposób definiowania modułu i korzystania z nich oraz używania zakresów modułów.
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 7a680e8aa0fa4d5ef9cac7f9e7ba07a3aa4ee1e2
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105611739"
---
# <a name="use-bicep-modules-preview"></a>Korzystanie z modułów Bicep (wersja zapoznawcza)

Bicep umożliwia rozbicie złożonego rozwiązania na moduły. Moduł Bicep to zestaw co najmniej jednego zasobu, który ma zostać wdrożony razem. Moduły z nieskomplikowanymi szczegółami deklaracji zasobów nieprzetworzonych, które mogą zwiększyć czytelność. Można ponownie użyć tych modułów i udostępnić je innym osobom. W połączeniu z [specyfikacją szablonu](./template-specs.md)tworzy sposób tworzenia i ponownego użycia kodu. Aby zapoznać się z samouczkiem, zobacz [Samouczek: Dodawanie modułów Bicep](./bicep-tutorial-add-modules.md).

## <a name="define-modules"></a>Definiowanie modułów

Każdy plik Bicep może być używany jako moduł. Moduł udostępnia tylko parametry i dane wyjściowe jako kontrakt do innych plików Bicep. Parametry i dane wyjściowe są opcjonalne.

Następujący plik Bicep można wdrożyć bezpośrednio, aby utworzyć konto magazynu lub użyć go jako modułu.  W następnej sekcji pokazano, jak korzystać z modułów:

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_RAGRS'
  'Standard_ZRS'
  'Premium_LRS'
  'Premium_ZRS'
  'Standard_GZRS'
  'Standard_RAGZRS'
])
param storageSKU string = 'Standard_LRS'
param location string

var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'

resource stg 'Microsoft.Storage/storageAccounts@2019-04-01' = {
  name: uniqueStorageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

Dane wyjściowe są używane do przekazywania wartości do nadrzędnych plików Bicep.

## <a name="consume-modules"></a>Korzystanie z modułów

Użyj słowa kluczowego _modułu_ , aby korzystać z modułu. Następujący plik Bicep wdraża zasób zdefiniowany w pliku modułu, do którego się odwołuje:

```bicep
@minLength(3)
@maxLength(11)
param namePrefix string
param location string = resourceGroup().location

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

- **module**: słowo kluczowe.
- **Nazwa symboliczna** (stgModule): identyfikator modułu.
- **plik modułu**: ścieżka do modułu w tym przykładzie jest określona za pomocą ścieżki względnej (./storageAccount.Bicep). Wszystkie ścieżki w Bicep muszą być określone przy użyciu separatora katalogu ukośnika (/), aby zapewnić spójną międzyplatformę kompilacji. Znak ukośnika odwrotnego () systemu Windows \\ nie jest obsługiwany.
- Właściwość **_name_** (storageDeploy) jest wymagana podczas konsumowania modułu. Gdy Bicep generuje szablon IL, to pole jest używane jako nazwa zagnieżdżonego zasobu wdrożenia, który jest generowany dla modułu:

    ```json
    ...
    ...
    "resources": [
      {
        "type": "Microsoft.Resources/deployments",
        "apiVersion": "2020-10-01",
        "name": "storageDeploy",
        "properties": {
          ...
        }
      }
    ]
    ...
    ```

Aby uzyskać wartość wyjściową z modułu, należy pobrać wartość właściwości z składnią, na przykład: `stgModule.outputs.storageEndpoint` gdzie `stgModule` jest identyfikatorem modułu.

## <a name="configure-module-scopes"></a>Skonfiguruj zakresy modułów

Podczas deklarowania modułu można podać właściwość _zakresu_ , aby ustawić zakres, w którym ma zostać wdrożony moduł:

```bicep
module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: resourceGroup('someOtherRg') // pass in a scope to a different resourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}
```

Właściwość _SCOPE_ może zostać pominięta, gdy zakres docelowy modułu i zakres docelowy elementu nadrzędnego są takie same. Gdy właściwość Scope nie zostanie podana, moduł jest wdrażany w zakresie docelowym elementu nadrzędnego.

Następujący plik Bicep pokazuje, jak utworzyć grupę zasobów i wdrożyć moduł w grupie zasobów:

```bicep
// set the target scope for this file
targetScope = 'subscription'

@minLength(3)
@maxLength(11)
param namePrefix string

param location string = deployment().location

var resourceGroupName = '${namePrefix}rg'
resource myResourceGroup 'Microsoft.Resources/resourceGroups@2020-01-01' = {
  name: resourceGroupName
  location: location
  scope: subscription()
}

module stgModule './storageAccount.bicep' = {
  name: 'storageDeploy'
  scope: myResourceGroup
  params: {
    storagePrefix: namePrefix
    location: location
  }
}

output storageEndpoint object = stgModule.outputs.storageEndpoint
```

## <a name="next-steps"></a>Następne kroki

- Aby przejść przez samouczek, zobacz [Samouczek: Dodawanie modułów Bicep](./bicep-tutorial-add-modules.md).
