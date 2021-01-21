---
title: Procesory telemetrii (wersja zapoznawcza) — Azure Monitor Application Insights dla środowiska Java
description: Jak skonfigurować procesory telemetrii w Azure Monitor Application Insights dla języka Java
ms.topic: conceptual
ms.date: 10/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: c0745dd4069c64292fbcaef666d843ae2d25f7b3
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632584"
---
# <a name="telemetry-processors-preview---azure-monitor-application-insights-for-java"></a>Procesory telemetrii (wersja zapoznawcza) — Azure Monitor Application Insights dla środowiska Java

> [!NOTE]
> Ta funkcja jest nadal dostępna w wersji zapoznawczej.

Agent Java 3,0 dla Application Insights ma teraz możliwości przetwarzania danych telemetrycznych przed wyeksportowaniem danych.

Poniżej przedstawiono niektóre przypadki użycia procesorów telemetrycznych:
 * Maskowanie danych poufnych
 * Warunkowe dodawanie wymiarów niestandardowych
 * Zaktualizuj nazwę używaną do agregacji i wyświetlania w Azure Portal
 * Porzuć atrybuty zakresu, aby kontrolować koszt pozyskiwania

## <a name="terminology"></a>Terminologia

Zanim przejdziemy do procesorów telemetrii, ważne jest, aby zrozumieć, do czego odnosi się okres.

Zakres jest ogólnym terminem dla którejkolwiek z tych trzech rzeczy:

* Żądanie przychodzące
* Zależność wychodząca (np. zdalne wywołanie do innej usługi)
* Zależność w procesie (np. prace wykonywane przez podskładniki usługi)

Na potrzeby procesorów telemetrii ważne składniki zakresu są następujące:

* Nazwa
* Atrybuty

Nazwa zakresu jest głównym ekranem używanym do żądań i zależności w Azure Portal.

Atrybuty span reprezentują standardowe i niestandardowe właściwości danego żądania lub zależności.

## <a name="telemetry-processor-types"></a>Typy procesorów telemetrii

Obecnie istnieją dwa typy procesorów telemetrii.

#### <a name="attribute-processor"></a>Procesor atrybutów

Procesor atrybutu umożliwia wstawianie, aktualizowanie, usuwanie i mieszanie atrybutów.
Może również wyodrębnić (za pośrednictwem wyrażenia regularnego) jeden lub więcej nowych atrybutów z istniejącego atrybutu.

#### <a name="span-processor"></a>Procesor obejmujący

Procesor zakresu ma możliwość zaktualizowania nazwy telemetrii.
Może również wyodrębnić (za pośrednictwem wyrażenia regularnego) jeden lub więcej nowych atrybutów z nazwy zakresu.

> [!NOTE]
> Należy zauważyć, że aktualnie procesory telemetryczne przetwarzają tylko atrybuty typu String i nie przetwarzają atrybutów typu Boolean ani Number.

## <a name="getting-started"></a>Wprowadzenie

Utwórz plik konfiguracji o nazwie `applicationinsights.json` i umieść go w tym samym katalogu, co `applicationinsights-agent-*.jar` , przy użyciu poniższego szablonu.

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

## <a name="includeexclude-criteria"></a>Kryteria dołączania/wykluczania

Zarówno procesory atrybutów, jak i procesory zakresów obsługują opcjonalne `include` i `exclude` kryteria.
Procesor zostanie zastosowany tylko do tych zakresów, które pasują do jego `include` kryteriów (jeśli zostały podane) _i_ nie są zgodne z `exclude` kryteriami (jeśli zostały podane).

Aby skonfigurować tę opcję, w obszarze `include` i/lub `exclude` co najmniej jeden `matchType` musi być `spanNames` `attributes` wymagany.
Konfiguracja dołączania/wykluczania jest obsługiwana w celu uzyskania więcej niż jednego określonego warunku.
Aby nastąpiło dopasowanie, wszystkie określone warunki muszą mieć wartość true. 

**Pole wymagane**: 
* `matchType` kontroluje sposób `spanNames` interpretowania elementów w i `attributes` tablicach. Możliwe wartości to `regexp` lub `strict`. 

**Pola opcjonalne**: 
* `spanNames` musi odpowiadać co najmniej jednemu z elementów. 
* `attributes` Określa listę atrybutów do dopasowania. Wszystkie te atrybuty muszą być dokładnie zgodne, aby nastąpiło dopasowanie.

> [!NOTE]
> Jeśli obie `include` i `exclude` są określone, `include` właściwości są sprawdzane przed `exclude` właściwościami.

#### <a name="sample-usage"></a>Przykładowe użycie

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
Aby uzyskać więcej informacji, zapoznaj się z dokumentacją dotyczącą [procesora telemetrii](./java-standalone-telemetry-processors-examples.md) .

## <a name="attribute-processor"></a>Procesor atrybutów

Procesor atrybutów modyfikuje atrybuty zakresu. Opcjonalnie obsługuje możliwość dołączania/wykluczania zakresów. Wykonuje listę akcji, które są wykonywane w kolejności określonej w pliku konfiguracji. Obsługiwane są następujące akcje:

### `insert`

Wstawia nowy atrybut w zakresach, w których klucz jeszcze nie istnieje.   

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
W przypadku `insert` akcji wymagane są następujące elementy
  * `key`
  * jeden z `value` lub `fromAttribute`
  * `action`:`insert`

### `update`

Aktualizuje atrybut w zakresach, w którym znajduje się klucz

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
W przypadku `update` akcji wymagane są następujące elementy
  * `key`
  * jeden z `value` lub `fromAttribute`
  * `action`:`update`


### `delete` 

Usuwa atrybut z zakresu

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
W przypadku `delete` akcji wymagane są następujące elementy
  * `key`
  * `action`: `delete`

### `hash`

Skróty (SHA1) istniejące wartości atrybutów

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
W przypadku `hash` akcji wymagane są następujące elementy
* `key`
* `action` : `hash`

### `extract`

> [!NOTE]
> Ta funkcja jest tylko w 3.0.2 i nowszych

Wyodrębnianie wartości przy użyciu reguły wyrażenia regularnego z klucza wejściowego do kluczy docelowych określonych w regule. Jeśli klucz docelowy już istnieje, zostanie zastąpiony. Zachowuje się podobnie do ustawienia [procesor span](#extract-attributes-from-span-name) `toAttributes` z istniejącym atrybutem jako źródło.

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
W przypadku `extract` akcji wymagane są następujące elementy
* `key`
* `pattern`
* `action` : `extract`

Aby uzyskać więcej informacji, zapoznaj się z dokumentacją dotyczącą [procesora telemetrii](./java-standalone-telemetry-processors-examples.md) .

## <a name="span-processor"></a>Procesor obejmujący

Procesor span modyfikuje nazwę lub atrybuty zakresu na podstawie nazwy zakresu. Opcjonalnie obsługuje możliwość dołączania/wykluczania zakresów.

### <a name="name-a-span"></a>Nazwij zakres

Następujące ustawienie jest wymagane jako część sekcji Name:

* `fromAttributes`: Wartość atrybutu dla kluczy jest używana do tworzenia nowej nazwy w kolejności określonej w konfiguracji. Wszystkie klucze atrybutów należy określić w zakresie, w jakim procesor ma zmienić jego nazwę.

Można opcjonalnie skonfigurować następujące ustawienie:

* `separator`: Ciąg, który jest określony, będzie używany do dzielenia wartości
> [!NOTE]
> Jeśli zmiana nazwy jest zależna od atrybutów, które są modyfikowane przez procesor atrybutów, upewnij się, że procesor zakresu jest określony po procesor atrybutów w specyfikacji potoku.

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

### <a name="extract-attributes-from-span-name"></a>Wyodrębnij atrybuty z nazwy zakresu

Przyjmuje listę wyrażeń regularnych w celu dopasowania nazwy zakresu względem i wyodrębnienia atrybutów z niej na podstawie podwyrażeń. Musi być określony w `toAttributes` sekcji.

Wymagane są następujące ustawienia:

`rules` : Lista reguł służących do wyodrębniania wartości atrybutów z nazwy zakresu. Wartości w nazwie zakresu są zamieniane na nazwy wyodrębnionych atrybutów. Każda reguła na liście jest ciągiem wzorca wyrażenia regularnego. Nazwa zakresu jest sprawdzana pod kątem wyrażenia regularnego. Jeśli wyrażenie regularne jest zgodne, wszystkie nazwane Podwyrażenie wyrażenia regularnego są wyodrębniane jako atrybuty i dodawane do zakresu. Każda nazwa podwyrażenia to nazwa atrybutu i Podwyrażenie dopasowane do części staną się wartością atrybutu. Dopasowana część w nazwie zakresu jest zastępowana przez wyodrębnioną nazwę atrybutu. Jeśli atrybuty już istnieją w zasięgu, zostaną nadpisywane. Ten proces jest powtarzany dla wszystkich reguł w kolejności, w jakiej zostały określone. Każda kolejna reguła działa w odniesieniu do nazwy zakresu, która jest danymi wyjściowymi po przetworzeniu poprzedniej reguły.

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

## <a name="list-of-attributes"></a>Lista atrybutów

Poniżej wymieniono niektóre typowe atrybuty span, które mogą być używane w procesorach telemetrycznych.

### <a name="http-spans"></a>Zakresy HTTP

| Atrybut  | Typ | Opis | 
|---|---|---|
| `http.method` | ciąg | Metoda żądania HTTP.|
| `http.url` | ciąg | Pełny adres URL żądania HTTP w formularzu `scheme://host[:port]/path?query[#fragment]` . Zwykle fragment nie jest przesyłany za pośrednictwem protokołu HTTP, ale jeśli jest znany, należy go uwzględnić.|
| `http.status_code` | liczba | [Kod stanu odpowiedzi HTTP](https://tools.ietf.org/html/rfc7231#section-6).|
| `http.flavor` | ciąg | Rodzaj używanego protokołu HTTP |
| `http.user_agent` | ciąg | Wartość nagłówka [użytkownika http-agenta](https://tools.ietf.org/html/rfc7231#section-5.5.3) wysyłanego przez klienta. |

### <a name="jdbc-spans"></a>Zakresy JDBC

| Atrybut  | Typ | Opis  |
|---|---|---|
| `db.system` | ciąg | Identyfikator używanego produktu zarządzania bazami danych (DBMS). |
| `db.connection_string` | ciąg | Parametry połączenia używane do nawiązania połączenia z bazą danych. Zaleca się usunięcie osadzonych poświadczeń.|
| `db.user` | ciąg | Nazwa użytkownika służąca do uzyskiwania dostępu do bazy danych. |
| `db.name` | ciąg | Ten atrybut służy do raportowania nazwy bazy danych, do której uzyskuje się dostęp. Dla poleceń, które przełączają bazę danych, należy ustawić dla docelowej bazy danych (nawet jeśli polecenie nie powiedzie się).|
| `db.statement` | ciąg | Wykonanie instrukcji bazy danych.|
