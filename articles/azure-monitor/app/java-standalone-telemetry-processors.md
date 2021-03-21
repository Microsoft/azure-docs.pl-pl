---
title: Procesory telemetrii (wersja zapoznawcza) — Azure Monitor Application Insights dla środowiska Java
description: Dowiedz się więcej o konfigurowaniu procesorów telemetrycznych w Azure Monitor Application Insights dla środowiska Java.
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 35e53454e5b2c6265082bbedb4a8b60e82df7191
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734574"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>Procesory telemetrii (wersja zapoznawcza) — Azure Monitor Application Insights dla środowiska Java

> [!NOTE]
> Funkcja procesorów telemetrycznych jest w wersji zapoznawczej.

Agent Java 3,0 dla Application Insights może przetwarzać dane telemetryczne przed wyeksportowaniem danych.

Poniżej przedstawiono niektóre przypadki użycia dla procesorów telemetrii:
 * Utwórz poufne dane.
 * Warunkowo Dodaj niestandardowe wymiary.
 * Zaktualizuj nazwę zakresu, która jest używana do agregowania podobnej telemetrii w Azure Portal.
 * Porzuć atrybuty zakresu, aby kontrolować koszty pozyskiwania.

## <a name="terminology"></a>Terminologia

Przed poznaniem procesorów telemetrycznych należy zrozumieć *zakres* tego okresu. Zakres jest ogólnym terminem dla:

* Żądanie przychodzące.
* Zależność wychodząca (na przykład połączenie zdalne z inną usługą).
* Zależność w procesie (na przykład pracy wykonywanej przez podskładniki usługi).

W przypadku procesorów telemetrii te składniki są ważne:

* Nazwa
* Atrybuty

Nazwa zakresu jest głównym ekranem dla żądań i zależności w Azure Portal. Atrybuty span reprezentują zarówno standardowe, jak i niestandardowe właściwości danego żądania lub zależności.

## <a name="telemetry-processor-types"></a>Typy procesorów telemetrii

Obecnie dwa typy procesorów telemetrii to procesory atrybutów i zakresy procesorów.

Procesor atrybutu może wstawiać, aktualizować, usuwać i wyznaczać atrybuty.
Może również użyć wyrażenia regularnego, aby wyodrębnić jeden lub więcej nowych atrybutów z istniejącego atrybutu.

Procesor span może zaktualizować nazwę telemetrii.
Może również użyć wyrażenia regularnego, aby wyodrębnić jeden lub więcej nowych atrybutów z nazwy zakresu.

> [!NOTE]
> Obecnie procesory telemetrii przetwarzają tylko atrybuty typu String. Nie przetwarzają one atrybutów typu Boolean ani Number.

## <a name="getting-started"></a>Wprowadzenie

Aby rozpocząć, Utwórz plik konfiguracji o nazwie *applicationinsights.json*. Zapisz ją w tym samym katalogu, co *ApplicationInsights-Agent- \* . jar*. Użyj następującego szablonu.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        ...
      },
      {
        "type": "attribute",
        ...
      },
      {
        "type": "span",
        ...
      }
    ]
  }
}
```

## <a name="include-criteria-and-exclude-criteria"></a>Kryteria dołączania i wykluczania kryteriów

Zarówno procesory atrybutów, jak i procesory zakresów obsługują opcjonalne `include` i `exclude` kryteria.
Procesor jest stosowany tylko do zakresów, które pasują do jego `include` kryteriów (jeśli jest dostępny) _i_ nie są zgodne z `exclude` kryteriami (jeśli jest dostępny).

Aby skonfigurować tę opcję, w obszarze `include` lub `exclude` (lub obu) Określ co najmniej jeden `matchType` i albo `spanNames` `attributes` .
Konfiguracja dołączania/wykluczania pozwala na więcej niż jeden określony warunek.
Aby wyniki były zgodne, wszystkie określone warunki muszą mieć wartość true. 

* **Pole wymagane**: `matchType` określa sposób `spanNames` interpretowania elementów w tablicach i `attributes` tablicach. Możliwe wartości to `regexp` i `strict` . 

* **Pola opcjonalne**: 
    * `spanNames` musi odpowiadać co najmniej jednemu z elementów. 
    * `attributes` Określa listę atrybutów do dopasowania. Wszystkie te atrybuty muszą dokładnie pasować do wyniku dopasowania.
    
> [!NOTE]
> Jeśli obie `include` i `exclude` są określone, `include` właściwości są sprawdzane przed `exclude` sprawdzeniem właściwości.

### <a name="sample-usage"></a>Przykładowe zastosowanie

```json
"processors": [
  {
    "type": "attribute",
    "include": {
      "matchType": "strict",
      "spanNames": [
        "spanA",
        "spanB"
      ]
    },
    "exclude": {
      "matchType": "strict",
      "attributes": [
        {
          "key": "redact_trace",
          "value": "false"
        }
      ]
    },
    "actions": [
      {
        "key": "credit_card",
        "action": "delete"
      },
      {
        "key": "duplicate_key",
        "action": "delete"
      }
    ]
  }
]
```
Aby uzyskać więcej informacji, zobacz [przykłady procesora telemetrii](./java-standalone-telemetry-processors-examples.md).

## <a name="attribute-processor"></a>Procesor atrybutów

Procesor atrybutu modyfikuje atrybuty zakresu. Może obsługiwać możliwość dołączania lub wykluczania zakresów. Wykonuje listę akcji, które są wykonywane w kolejności, w jakiej określa plik konfiguracji. Procesor obsługuje następujące akcje:

- `insert`
- `update`
- `delete`
- `hash`
- `extract`
### `insert`

`insert`Akcja wstawia nowy atrybut w zakresach, w których klucz jeszcze nie istnieje.   

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "value1",
        "action": "insert"
      }
    ]
  }
]
```
`insert`Akcja wymaga następujących ustawień:
* `key`
* Albo `value``fromAttribute`
* `action`: `insert`

### `update`

`update`Akcja aktualizuje atrybut w zakresach, w których klucz już istnieje.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "value": "newValue",
        "action": "update"
      }
    ]
  }
]
```
`update`Akcja wymaga następujących ustawień:
* `key`
* Albo `value``fromAttribute`
* `action`: `update`


### `delete` 

`delete`Akcja usuwa atrybut z zakresu.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "delete"
      }
    ]
  }
]
```
`delete`Akcja wymaga następujących ustawień:
* `key`
* `action`: `delete`

### `hash`

`hash`Skróty akcji (SHA1) mają istniejącą wartość atrybutu.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "action": "hash"
      }
    ]
  }
]
```
`hash`Akcja wymaga następujących ustawień:
* `key`
* `action`: `hash`

### `extract`

> [!NOTE]
> `extract`Funkcja jest dostępna tylko w wersji 3.0.2 i nowszych.

`extract`Akcja wyodrębnia wartości przy użyciu reguły wyrażenia regularnego z klucza wejściowego do kluczy docelowych, które określa reguła. Jeśli klucz docelowy już istnieje, zostanie zastąpiony. Ta akcja zachowuje się jak ustawienie [procesor zakresu](#extract-attributes-from-the-span-name) `toAttributes` , gdzie istniejący atrybut jest źródłem.

```json
"processors": [
  {
    "type": "attribute",
    "actions": [
      {
        "key": "attribute1",
        "pattern": "<regular pattern with named matchers>",
        "action": "extract"
      }
    ]
  }
]
```
`extract`Akcja wymaga następujących ustawień:
* `key`
* `pattern`
* `action`: `extract`

Aby uzyskać więcej informacji, zobacz [przykłady procesora telemetrii](./java-standalone-telemetry-processors-examples.md).

## <a name="span-processor"></a>Procesor obejmujący

Procesor span modyfikuje nazwę lub atrybuty zakresu na podstawie nazwy zakresu. Może obsługiwać możliwość dołączania lub wykluczania zakresów.

### <a name="name-a-span"></a>Nazwij zakres

`name`Sekcja wymaga `fromAttributes` Ustawienia. Wartości z tych atrybutów są używane do tworzenia nowej nazwy połączonej w kolejności określonej przez konfigurację. Procesor zmieni nazwę zakresu tylko wtedy, gdy wszystkie te atrybuty są obecne w zakresie.

`separator`Ustawienie jest opcjonalne. To ustawienie jest ciągiem. Określono, aby podzielić wartości.
> [!NOTE]
> Jeśli zmiana nazwy jest zależna od procesora atrybutów do modyfikacji atrybutów, upewnij się, że procesor zakresu jest określony po procesor atrybutów w specyfikacji potoku.

```json
"processors": [
  {
    "type": "span",
    "name": {
      "fromAttributes": [
        "attributeKey1",
        "attributeKey2",
      ],
      "separator": "::"
    }
  }
] 
```

### <a name="extract-attributes-from-the-span-name"></a>Wyodrębnij atrybuty z nazwy zakresu

`toAttributes`Sekcja zawiera listę wyrażeń regularnych, które pasują do nazwy zakresu. Wyodrębnia atrybuty na podstawie podwyrażeń.

To `rules` ustawienie jest wymagane. To ustawienie wyświetla listę reguł, które są używane do wyodrębniania wartości atrybutów z nazwy zakresu. 

Wartości w nazwie zakresu są zamieniane na nazwy wyodrębnionych atrybutów. Każda reguła na liście jest ciągiem wzorca wyrażeń regularnych (regularnym). 

Poniżej przedstawiono sposób zamieniania wartości na nazwy wyodrębnionych atrybutów:

1. Nazwa zakresu jest sprawdzana pod kątem wyrażenia regularnego. 
1. Jeśli wyrażenie regularne jest zgodne, wszystkie nazwane Podwyrażenie wyrażenia regularnego są wyodrębniane jako atrybuty. 
1. Wyodrębnione atrybuty są dodawane do zakresu. 
1. Każda nazwa podwyrażenia jest nazwą atrybutu. 
1. Podwyrażenie dopasowane do części jest wartością atrybutu. 
1. Dopasowana część w nazwie zakresu jest zastępowana przez wyodrębnioną nazwę atrybutu. Jeśli atrybuty już istnieją w zasięgu, są zastępowane. 
 
Ten proces jest powtarzany dla wszystkich reguł w kolejności, w jakiej zostały określone. Każda kolejna reguła działa w odniesieniu do nazwy zakresu, która jest danymi wyjściowymi poprzedniej reguły.

```json
"processors": [
  {
    "type": "span",
    "name": {
      "toAttributes": {
        "rules": [
          "rule1",
          "rule2",
          "rule3"
        ]
      }
    }
  }
]

```

## <a name="common-span-attributes"></a>Wspólne atrybuty zakresu

W tej sekcji wymieniono niektóre typowe atrybuty span, które mogą być używane przez procesory telemetryczne.

### <a name="http-spans"></a>Zakresy HTTP

| Atrybut  | Typ | Opis | 
|---|---|---|
| `http.method` | ciąg | Metoda żądania HTTP.|
| `http.url` | ciąg | Pełny adres URL żądania HTTP w formularzu `scheme://host[:port]/path?query[#fragment]` . Fragment nie jest zazwyczaj przesyłany za pośrednictwem protokołu HTTP. Ale jeśli fragment jest znany, powinien być dołączony.|
| `http.status_code` | liczba | [Kod stanu odpowiedzi HTTP](https://tools.ietf.org/html/rfc7231#section-6).|
| `http.flavor` | ciąg | Typ protokołu HTTP. |
| `http.user_agent` | ciąg | Wartość nagłówka [użytkownika http-agenta](https://tools.ietf.org/html/rfc7231#section-5.5.3) wysyłanego przez klienta. |

### <a name="jdbc-spans"></a>Zakresy JDBC

| Atrybut  | Typ | Opis  |
|---|---|---|
| `db.system` | ciąg | Identyfikator używanego produktu zarządzania bazami danych (DBMS). |
| `db.connection_string` | ciąg | Parametry połączenia używane do nawiązania połączenia z bazą danych. Zaleca się usunięcie osadzonych poświadczeń.|
| `db.user` | ciąg | Nazwa użytkownika służąca do uzyskiwania dostępu do bazy danych. |
| `db.name` | ciąg | Ciąg służący do raportowania nazwy bazy danych, do której uzyskuje się dostęp. W przypadku poleceń, które przełączają bazę danych, ten ciąg powinien być ustawiony na docelową bazę danych, nawet jeśli polecenie nie powiedzie się.|
| `db.statement` | ciąg | Instrukcja bazy danych, która jest uruchamiana.|
