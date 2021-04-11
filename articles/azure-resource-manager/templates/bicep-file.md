---
title: Struktura i składnia pliku Bicep
description: Opisuje strukturę i właściwości pliku Bicep przy użyciu składni deklaratywnej.
ms.topic: conceptual
ms.date: 03/31/2021
ms.openlocfilehash: 09993ae9c08f53144de8e94e6555ad93bec681f6
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168692"
---
# <a name="understand-the-structure-and-syntax-of-bicep-files"></a>Zrozumienie struktury i składni plików Bicep

W tym artykule opisano strukturę pliku Bicep. Przedstawia on różne sekcje pliku i właściwości, które są dostępne w tych sekcjach.

Ten artykuł jest przeznaczony dla użytkowników, którzy mają pewną wiedzę z plikami Bicep. Zawiera szczegółowe informacje na temat struktury szablonu. Aby zapoznać się z samouczkiem krok po kroku, który przeprowadzi Cię przez proces tworzenia pliku Bicep, zobacz [Samouczek: Tworzenie i wdrażanie pierwszego pliku Azure Resource Manager Bicep](bicep-tutorial-create-first-bicep.md).

## <a name="template-format"></a>Template format (Format szablonu)

Plik Bicep ma następujące elementy. Elementy mogą być wyświetlane w dowolnej kolejności.

```bicep
targetScope = '<scope>'

@<decorator>(<argument>)
param <parameter-name> <parameter-data-type> = <default-value>

var <variable-name> = <variable-value>

resource <resource-symbolic-name> '<resource-type>@<api-version>' = {
  <resource-properties>
}

// conditional deployment
resource <resource-symbolic-name> '<resource-type>@<api-version>' = if (<condition-to-deploy>) {
  <resource-properties>
}

// iterative deployment
@<decorator>(<argument>)
resource <resource-symbolic-name> '<resource-type>@<api-version>' = [for <item> in <collection>: {
  <resource-properties>
}]

module <module-symbolic-name> '<path-to-file>' = {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// conditional deployment
module <module-symbolic-name> '<path-to-file>' = if (<condition-to-deploy>) {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}

// iterative deployment
module <module-symbolic-name> '<path-to-file>' = [for <item> in <collection>: {
  name: '<linked-deployment-name>'
  params: {
    <parameter-names-and-values>
  }
}]

output <output-name> <output-data-type> = <output-value>
```

W poniższym przykładzie przedstawiono implementację tych elementów.

```bicep
@minLength(3)
@maxLength(11)
param storagePrefix string

param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location

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

module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}

output storageEndpoint object = stg.properties.primaryEndpoints
```

## <a name="target-scope"></a>Zakres docelowy

Domyślnie zakres docelowy jest ustawiony na `resourceGroup` . W przypadku wdrażania na poziomie grupy zasobów nie trzeba ustawiać zakresu docelowego w pliku Bicep.

Dozwolone wartości to:

* Grupa zasobów **— wartość** domyślna używana na potrzeby [wdrożeń grup zasobu](deploy-to-resource-group.md).
* **subskrypcja** — używana na potrzeby [wdrożeń subskrypcji](deploy-to-subscription.md).
* Grupa **zarządzania** — służy do [wdrażania grup zarządzania](deploy-to-management-group.md).
* **dzierżawca** — używany do [wdrożeń dzierżawców](deploy-to-tenant.md).

## <a name="parameters"></a>Parametry

Użyj parametrów dla wartości, które muszą być różne dla różnych wdrożeń. Można zdefiniować wartość domyślną dla parametru, który jest używany, jeśli podczas wdrażania nie zostanie podana żadna wartość.

Na przykład można dodać parametr SKU, aby określić różne rozmiary zasobu. Można użyć funkcji szablonu do tworzenia wartości domyślnej, na przykład do pobierania lokalizacji grupy zasobów.

```bicep
param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location
```

Aby uzyskać dostęp do dostępnych typów danych, zobacz [typy danych w szablonach](data-types.md).

Aby uzyskać więcej informacji, zobacz [Parametry w szablonach](template-parameters.md).

## <a name="parameter-decorators"></a>Dekoratory parametru

Dla każdego parametru można dodać jeden lub więcej dekoratory. Te dekoratory definiują wartości, które są dozwolone dla parametru. W poniższym przykładzie określono jednostki SKU, które można wdrożyć za pomocą pliku Bicep.

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageSKU string = 'Standard_LRS'
```

W poniższej tabeli opisano dostępne dekoratory oraz sposób ich używania.

| Dekoratora | Zastosuj do | Argument | Opis |
| --------- | ---- | ----------- | ------- |
| występować | all | array | Dozwolone wartości parametru. Użyj tego dekoratora, aby upewnić się, że użytkownik poda poprawne wartości. |
| description (opis) | all | ciąg | Tekst objaśniający sposób użycia parametru. Opis jest wyświetlany użytkownikom w portalu. |
| maxLength | Tablica, ciąg | int | Maksymalna długość parametrów ciągów i tablic. Wartość jest włącznie. |
| maxValue | int | int | Maksymalna wartość parametru Integer. Ta wartość jest włącznie. |
| metadane | all | object | Właściwości niestandardowe do zastosowania do parametru. Może zawierać Właściwość Description, która jest równoważna z opisem dekoratora. |
| minLength | Tablica, ciąg | int | Minimalna długość parametrów ciągów i tablic. Wartość jest włącznie. |
| minValue | int | int | Minimalna wartość parametru Integer. Ta wartość jest włącznie. |
| zapewnienia | ciąg, obiekt | brak | Oznacza parametr jako bezpieczny. Wartość bezpiecznego parametru nie jest zapisywana w historii wdrożenia i nie jest zarejestrowana. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie ciągów i obiektów](data-types.md#secure-strings-and-objects). |

## <a name="variables"></a>Zmienne

Używaj zmiennych dla złożonych wyrażeń powtarzanych w pliku Bicep. Na przykład można dodać zmienną dla nazwy zasobu, która jest zbudowana przez połączenie kilku wartości jednocześnie.

```bicep
var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
```

Nie określasz [typu danych](data-types.md) dla zmiennej. Zamiast tego typ danych jest wywnioskowany na podstawie wartości.

Aby uzyskać więcej informacji, zobacz [zmienne w szablonach](template-variables.md).

## <a name="resource"></a>Zasób

Użyj `resource` słowa kluczowego, aby zdefiniować zasób do wdrożenia. Deklaracja zasobu zawiera nazwę symboliczną dla zasobu. Ta Nazwa symboliczna zostanie użyta w innych częściach pliku Bicep, jeśli konieczne jest uzyskanie wartości z zasobu.

Deklaracja zasobu obejmuje również typ zasobu i wersję interfejsu API.

```bicep
resource stg 'Microsoft.Storage/storageAccounts@2019-06-01' = {
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
```

W deklaracji zasobów należy uwzględnić właściwości dla typu zasobu. Te właściwości są unikatowe dla każdego typu zasobu.

Aby uzyskać więcej informacji, zobacz [Deklaracja zasobów w szablonach](resource-declaration.md).

Aby [warunkowo wdrożyć zasób](conditional-resource-deployment.md), Dodaj `if` wyrażenie.

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = if (newOrExisting == 'new') {
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
```

Aby [wdrożyć więcej niż jedno wystąpienie](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md) typu zasobu, Dodaj `for` wyrażenie. Wyrażenie może przekroczyć liczbę elementów członkowskich tablicy.

```bicep
resource sa 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  name: storageName
  location: location
  sku: {
    name: storageSKU
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
  }
}]
```

## <a name="modules"></a>Moduły

Używaj modułów do łączenia z innymi plikami Bicep, które zawierają kod, którego chcesz użyć ponownie. Moduł zawiera co najmniej jeden zasób do wdrożenia. Te zasoby są wdrażane wraz z innymi zasobami w pliku Bicep.

```bicep
module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}
```

Nazwa symboliczna pozwala na odwoływanie się do modułu z innych lokalizacji w pliku. Na przykład można uzyskać wartość wyjściową z modułu przy użyciu nazwy symbolicznej i nazwy wartości wyjściowej.

Podobnie jak zasoby, można warunkowo lub iteracyjnie wdrożyć moduł. Składnia jest taka sama dla modułów jak zasoby.

Aby uzyskać więcej informacji, zobacz [używanie modułów Bicep](bicep-modules.md).

## <a name="resource-and-module-decorators"></a>Dekoratory zasobów i modułów

Do definicji zasobu lub modułu można dodać dekoratora. Jedyne obsługiwane dekoratora to `batchSize(int)` . Można go zastosować tylko do definicji zasobu lub modułu korzystającej z `for` wyrażenia.

Domyślnie zasoby są wdrażane równolegle. Nie wiesz, w jakiej kolejności kończy. Po dodaniu `batchSize` dekoratora należy wdrożyć wystąpienia seryjnie. Użyj argumentu Integer, aby określić liczbę wystąpień do wdrożenia równolegle.

```bicep
@batchSize(3)
resource storageAccountResources 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  ...
}]
```

Aby uzyskać więcej informacji, zobacz [serial lub Parallel](copy-resources.md#serial-or-parallel).

## <a name="outputs"></a>Dane wyjściowe

Użyj danych wyjściowych do zwrócenia wartości ze wdrożenia. Zwykle zwracasz wartość ze wdrożonego zasobu, gdy musisz ponownie użyć tej wartości dla innej operacji.

```bicep
output storageEndpoint object = stg.properties.primaryEndpoints
```

Określ [Typ danych](data-types.md) dla wartości wyjściowej.

Aby uzyskać więcej informacji, zobacz dane [wyjściowe w szablonach](template-outputs.md).

## <a name="comments"></a>Komentarze

Korzystanie `//` z komentarzy jednowierszowych lub `/* ... */` dla komentarzy wielowierszowych

Poniższy przykład pokazuje Komentarz jednowierszowy.

```bicep
// This is your primary NIC.
resource nic1 'Microsoft.Network/networkInterfaces@2020-06-01' = {
   ...
}
```

Poniższy przykład pokazuje komentarz wielowierszowy.

```bicep
/*
  This template assumes the key vault already exists and
  is in same subscription and resource group as the deployment.
*/
param existingKeyVaultName string
```

## <a name="multi-line-strings"></a>Ciągi wielowierszowe

Możesz przerwać ciąg w wielu wierszach. `'''`Aby rozpocząć i zakończyć ciąg wielowierszowy, użyj trzech znaków pojedynczego cudzysłowu. 

Znaki w ciągu wielowierszowym są obsługiwane jako-is. Znaki ucieczki są zbędne. Nie można dołączyć `'''` do ciągu wielowierszowego. Interpolacja ciągów nie jest obecnie obsługiwana.

Możesz uruchomić ciąg bezpośrednio po otwarciu `'''` lub dołączyć nowy wiersz. W obu przypadkach ciąg otrzymany nie zawiera nowego wiersza. W zależności od końców wierszy w pliku Bicep, nowe wiersze są interpretowane jako `\r\n` lub `\n` .

W poniższym przykładzie pokazano ciąg wielowierszowy.

```bicep
var stringVar = '''
this is multi-line
  string with formatting
  preserved.
'''
```

Poprzedni przykład jest równoważny do poniższego kodu JSON.

```json
"variables": {
  "stringVar": "this is multi-line\r\n  string with formatting\r\n  preserved.\r\n"
}
```

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z wprowadzeniem do Bicep, zobacz [co to jest Bicep?](bicep-overview.md).
