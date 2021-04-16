---
title: Struktura i składnia plików Bicep
description: Opisuje strukturę i właściwości pliku Bicep przy użyciu składni deklaratywnej.
ms.topic: conceptual
ms.date: 03/31/2021
ms.openlocfilehash: 1b8eddd388878be8f653f963ef967cf2c0af685f
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537862"
---
# <a name="understand-the-structure-and-syntax-of-bicep-files"></a>Opis struktury i składni plików Bicep

W tym artykule opisano strukturę pliku Bicep. Przedstawia on różne sekcje pliku i właściwości, które są dostępne w tych sekcjach.

Ten artykuł jest przeznaczony dla użytkowników, którzy są zaznajomieni z plikami Bicep. Zawiera on szczegółowe informacje o strukturze szablonu. Aby uzyskać samouczek krok po kroku, który przeprowadzi Cię przez proces tworzenia pliku Bicep, zobacz Samouczek: tworzenie i wdrażanie pierwszego pliku [Azure Resource Manager Bicep.](bicep-tutorial-create-first-bicep.md)

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

Poniższy przykład przedstawia implementację tych elementów.

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

Domyślnie zakres docelowy jest ustawiony na `resourceGroup` wartość . Jeśli wdrażasz na poziomie grupy zasobów, nie musisz ustawiać zakresu docelowego w pliku Bicep.

Dozwolone wartości to:

* **resourceGroup** — wartość domyślna używana w przypadku [wdrożeń grupy zasobów.](deploy-to-resource-group.md)
* **subskrypcja** — używana w przypadku [wdrożeń subskrypcji.](deploy-to-subscription.md)
* **managementGroup** — służy do [wdrożeń grup zarządzania.](deploy-to-management-group.md)
* **dzierżawa** — używana w przypadku [wdrożeń dzierżawy.](deploy-to-tenant.md)

## <a name="parameters"></a>Parametry

Użyj parametrów dla wartości, które muszą się różnić dla różnych wdrożeń. Można zdefiniować wartość domyślną dla parametru, który jest używany, jeśli podczas wdrażania nie podano żadnej wartości.

Możesz na przykład dodać parametr SKU, aby określić różne rozmiary zasobu. Za pomocą funkcji szablonu można utworzyć wartość domyślną, na przykład uzyskać lokalizację grupy zasobów.

```bicep
param storageSKU string = 'Standard_LRS'
param location string = resourceGroup().location
```

Aby uzyskać informacje o dostępnych typach danych, zobacz [Typy danych w szablonach](data-types.md).

Aby uzyskać więcej informacji, zobacz [Parametry w szablonach](template-parameters.md).

## <a name="parameter-decorators"></a>Dekoratory parametrów

Dla każdego parametru można dodać co najmniej jeden dekorator. Te dekoratory definiują wartości dozwolone dla parametru . W poniższym przykładzie określono jednostki SKU, które można wdrożyć za pośrednictwem pliku Bicep.

```bicep
@allowed([
  'Standard_LRS'
  'Standard_GRS'
  'Standard_ZRS'
  'Premium_LRS'
])
param storageSKU string = 'Standard_LRS'
```

W poniższej tabeli opisano dostępne dekoratory i sposób ich używania.

| Dekorator | Zastosuj do | Argument | Opis |
| --------- | ---- | ----------- | ------- |
| Dozwolone | all | array | Dozwolone wartości parametru. Użyj tego dekoratora, aby upewnić się, że użytkownik poda prawidłowe wartości. |
| description (opis) | all | ciąg | Tekst objaśniacy sposób użycia parametru . Opis jest wyświetlany użytkownikom za pośrednictwem portalu. |
| Maxlength | tablica, ciąg | int | Maksymalna długość parametrów ciągu i tablicy. Wartość jest włącznie. |
| Maxvalue | int | int | Maksymalna wartość parametru liczby całkowitej. Ta wartość jest włącznie. |
| metadane | all | object | Właściwości niestandardowe do zastosowania do parametru. Może zawierać właściwość description, która jest równoważna dekoratorowi opisu. |
| Minlength | tablica, ciąg | int | Minimalna długość parametrów ciągu i tablicy. Wartość jest włącznie. |
| Minvalue | int | int | Minimalna wartość parametru liczby całkowitej. Ta wartość jest włącznie. |
| Bezpieczne | string, object | brak | Oznacza parametr jako bezpieczny. Wartość bezpiecznego parametru nie jest zapisywana w historii wdrażania i nie jest rejestrowana. Aby uzyskać więcej informacji, zobacz [Bezpieczne ciągi i obiekty](data-types.md#secure-strings-and-objects). |

## <a name="variables"></a>Zmienne

Użyj zmiennych dla wyrażeń złożonych, które są powtarzane w pliku Bicep. Można na przykład dodać zmienną dla nazwy zasobu, która jest konstruowana przez grupę wartości.

```bicep
var uniqueStorageName = '${storagePrefix}${uniqueString(resourceGroup().id)}'
```

Nie określasz typu [danych dla](data-types.md) zmiennej. Zamiast tego typ danych jest wywnioskować na wartości .

Aby uzyskać więcej informacji, zobacz [Zmienne w szablonach](template-variables.md).

## <a name="resource"></a>Zasób

Użyj słowa `resource` kluczowego , aby zdefiniować zasób do wdrożenia. Deklaracja zasobu zawiera symboliczną nazwę zasobu. Użyjesz tej nazwy symbolicznej w innych częściach pliku Bicep, jeśli musisz uzyskać wartość z zasobu.

Deklaracja zasobu zawiera również typ zasobu i wersję interfejsu API.

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

W deklaracji zasobu należy uwzględnić właściwości typu zasobu. Te właściwości są unikatowe dla każdego typu zasobu.

Aby uzyskać więcej informacji, zobacz [Resource declaration in templates (Deklaracja zasobu w szablonach).](resource-declaration.md)

Aby [warunkowo wdrożyć zasób](conditional-resource-deployment.md), dodaj `if` wyrażenie.

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

Aby [wdrożyć więcej niż](https://github.com/Azure/bicep/blob/main/docs/spec/loops.md) jedno wystąpienie typu zasobu, dodaj `for` wyrażenie. Wyrażenie może iterować po elementach członkowskich tablicy.

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

Użyj modułów, aby połączyć się z innymi plikami Bicep, które zawierają kod, którego chcesz użyć ponownie. Moduł zawiera co najmniej jeden za zasobów do wdrożenia. Te zasoby są wdrażane razem z innymi zasobami w pliku Bicep.

```bicep
module webModule './webApp.bicep' = {
  name: 'webDeploy'
  params: {
    skuName: 'S1'
    location: location
  }
}
```

Nazwa symboliczna umożliwia odwołanie się do modułu z innego miejsca w pliku. Na przykład można uzyskać wartość wyjściową z modułu przy użyciu nazwy symbolicznej i nazwy wartości wyjściowej.

Podobnie jak w przypadku zasobów, można warunkowo lub iteracyjnie wdrożyć moduł. Składnia jest taka sama dla modułów, jak dla zasobów.

Aby uzyskać więcej informacji, zobacz [Use Bicep modules (Korzystanie z modułów Bicep).](bicep-modules.md)

## <a name="resource-and-module-decorators"></a>Dekoratory zasobów i modułów

Do definicji zasobu lub modułu możesz dodać dekorator. Jedynym obsługiwanym dekoratorem jest `batchSize(int)` . Można go zastosować tylko do definicji zasobu lub modułu, która używa `for` wyrażenia.

Domyślnie zasoby są wdrażane równolegle. Nie znasz kolejności ich zakończenia. Podczas dodawania `batchSize` dekoratora wystąpienia są wdrażane szeregowo. Użyj argumentu integer, aby określić liczbę wystąpień do równoległego wdrożenia.

```bicep
@batchSize(3)
resource storageAccountResources 'Microsoft.Storage/storageAccounts@2019-06-01' = [for storageName in storageAccounts: {
  ...
}]
```

Aby uzyskać więcej informacji, zobacz [Serial or Parallel](copy-resources.md#serial-or-parallel).

## <a name="outputs"></a>Dane wyjściowe

Użyj danych wyjściowych, aby zwrócić wartość z wdrożenia. Zazwyczaj wartość z wdrożonego zasobu jest zwracana, gdy trzeba ponownie użyć tej wartości dla innej operacji.

```bicep
output storageEndpoint object = stg.properties.primaryEndpoints
```

Określ typ [danych dla](data-types.md) wartości wyjściowej.

Aby uzyskać więcej informacji, zobacz [Dane wyjściowe w szablonach](template-outputs.md).

## <a name="comments"></a>Komentarze

Do `//` stosowania w komentarzach jedno wierszowych `/* ... */` lub w komentarzach wielo wierszowych

W poniższym przykładzie pokazano komentarz jedno wierszowy.

```bicep
// This is your primary NIC.
resource nic1 'Microsoft.Network/networkInterfaces@2020-06-01' = {
   ...
}
```

W poniższym przykładzie pokazano komentarz wielo wierszowy.

```bicep
/*
  This template assumes the key vault already exists and
  is in same subscription and resource group as the deployment.
*/
param existingKeyVaultName string
```

## <a name="multi-line-strings"></a>Ciągi wielo wierszowe

Ciąg można rozbić na wiele wierszy. Użyj trzech znaków pojedynczego `'''` cudzysłowu, aby rozpocząć i zakończyć ciąg wielo wierszowy.

Znaki w ciągu wielo wierszowym są obsługiwane w postaci, w ile są. Znaki ucieczki nie są konieczne. Nie można uwzględnić w `'''` ciągu wielo wierszowym. Interpolacja ciągów nie jest obecnie obsługiwana.

Możesz uruchomić ciąg bezpośrednio po otwarciu lub `'''` dołączyć nowy wiersz. W obu przypadkach wynikowy ciąg nie zawiera nowego wiersza. W zależności od wierszy kończących się w pliku Bicep nowe wiersze są interpretowane jako `\r\n` lub `\n` .

W poniższym przykładzie pokazano ciąg wielo wierszowy.

```bicep
var stringVar = '''
this is multi-line
  string with formatting
  preserved.
'''
```

Poprzedni przykład jest odpowiednikiem następującego danych JSON.

```json
"variables": {
  "stringVar": "this is multi-line\r\n  string with formatting\r\n  preserved.\r\n"
}
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać wprowadzenie do aplikacji Bicep, zobacz [Co to jest bicep?](bicep-overview.md).
