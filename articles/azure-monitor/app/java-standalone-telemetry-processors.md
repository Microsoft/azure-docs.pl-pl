---
title: Procesory telemetrii (wersja zapoznawcza) — Azure Monitor Application Insights Java
description: Procesory telemetryczne dla Azure Monitor Application Insights Java
ms.topic: conceptual
ms.date: 10/29/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 316243e130345ce1b009e065cf9d8250c501ee84
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96601037"
---
# <a name="telemetry-processors-preview-for-azure-monitor-application-insights-java"></a>Procesory telemetrii (wersja zapoznawcza) dla Azure Monitor Application Insights Java

> [!NOTE]
> Ta funkcja jest nadal dostępna w wersji zapoznawczej.

Agent Java 3,0 dla Application Insights ma teraz możliwości przetwarzania danych telemetrycznych przed wyeksportowaniem danych.

### <a name="some-use-cases"></a>Niektóre przypadki użycia:
 * Maskowanie danych poufnych
 * Warunkowe dodawanie wymiarów niestandardowych
 * Aktualizowanie nazwy telemetrii używanej do agregacji i wyświetlania

### <a name="supported-processors"></a>Obsługiwane procesory:
 * Procesor atrybutów
 * Procesor obejmujący

## <a name="to-get-started"></a>Aby rozpocząć

Utwórz plik konfiguracji o nazwie `applicationinsights.json` i umieść go w tym samym katalogu, co `applicationinsights-agent-***.jar` , przy użyciu poniższego szablonu.

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

## <a name="includeexclude-spans"></a>Zakresy dołączania/wykluczania

Procesor atrybutu i procesor obejmują opcję dostarczania zestawu właściwości zakresu do dopasowania, aby określić, czy zakres ma być dołączany czy wykluczony z procesora. Aby skonfigurować tę opcję, w obszarze `include` i/lub `exclude` co najmniej jeden `matchType` musi być `spanNames` `attributes` wymagany. Konfiguracja dołączania/wykluczania jest obsługiwana w celu uzyskania więcej niż jednego określonego warunku. Aby nastąpiło dopasowanie, wszystkie określone warunki muszą mieć wartość true. 

**Pole wymagane**: 
* `matchType` kontroluje sposób `spanNames` interpretowania elementów w i `attributes` tablicach. Możliwe wartości to `regexp` lub `strict`. 

**Pola opcjonalne**: 
* `spanNames` musi odpowiadać co najmniej jednemu z elementów. 
* `attributes` Określa listę atrybutów do dopasowania. Wszystkie te atrybuty muszą być dokładnie zgodne, aby nastąpiło dopasowanie.

> [!NOTE]
> Jeśli obie `include` i `exclude` są określone, `include` właściwości są sprawdzane przed `exclude` właściwościami.

#### <a name="sample-usage"></a>Przykładowe zastosowanie

Poniżej pokazano, jak określić zestaw właściwości zakresu, aby wskazać, które zakresy obejmują ten procesor. `include`Właściwości mówią, które powinny być uwzględnione, a właściwości w `exclude` dalszej odfiltrować zakresy, które nie powinny być przetwarzane.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "spanNames": [
            "svcA",
            "svcB"
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
  }
}
```

W powyższej konfiguracji są stosowane następujące zakresy zgodne z właściwościami i akcjami procesora:

* Nazwa Span1: "svcB" atrybutów: {env: production, test_request: 123, credit_card: 1234, redact_trace: "false"}

* Nazwa Span2: "svcA" atrybutów: {env: przemieszczanie, test_request: false, redact_trace: true}

Poniższe zakresy nie pasują do właściwości include i nie są stosowane:

* Nazwa Span3: "svcB" atrybuty: {env: Product, test_request: true, credit_card: 1234, redact_trace: false}

* Nazwa Span4: "svcC" atrybutów: {env: dev, test_request: false}

## <a name="attribute-processor"></a>Procesor atrybutów 

Procesor atrybutów modyfikuje atrybuty zakresu. Opcjonalnie obsługuje możliwość dołączania/wykluczania zakresów.
Wykonuje listę akcji, które są wykonywane w kolejności określonej w pliku konfiguracji. Obsługiwane są następujące akcje:

* `insert` : Wstawia nowy atrybut w zakresach, w których klucz jeszcze nie istnieje
* `update` : Aktualizuje atrybut w zakresie, w którym znajduje się klucz
* `delete` : Usuwa atrybut z zakresu
* `hash`   : Hashs (SHA1) istniejąca wartość atrybutu

Dla akcji `insert` i `update`
* `key` jest wymagana
* jedno `value` lub `fromAttribute` jest wymagane
* Ciąg `action` jest wymagany.

Dla `delete` akcji,
* `key` jest wymagana
* `action`: `delete` jest wymagana.

Dla `hash` akcji,
* `key` jest wymagana
* `action` : `hash` jest wymagana.

Listę akcji można utworzyć w celu utworzenia rozbudowanych scenariuszy, takich jak atrybut wypełniania wstecz, kopiowanie wartości do nowego klucza, redagowanie informacji poufnych.

#### <a name="sample-usage"></a>Przykładowe zastosowanie

Poniższy przykład ilustruje Wstawianie kluczy/wartości do zakresów:

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "attribute1",
            "value": "value1",
            "action": "insert"
          },
          {
            "key": "key1",
            "fromAttribute": "anotherkey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

Poniższy przykład demonstruje Konfigurowanie procesora do aktualizowania tylko istniejących kluczy w atrybucie:

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "piiattribute",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "credit_card",
            "action": "delete"
          },
          {
            "key": "user.email",
            "action": "hash"
          }
        ]
      }
    ]
  }
}
```

W poniższym przykładzie pokazano, jak przetwarzać zakresy, które mają nazwę zakresu, która pasuje do wzorców RegExp.
Ten procesor spowoduje usunięcie atrybutu "token" i spowoduje mieszanie atrybutu "Password" w zakresach, gdzie nazwa zakresu pasuje do "auth \* ". i gdzie nazwa zakresu nie jest zgodna z nazwą "login \* ".

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "auth.*"
          ]
        },
        "exclude": {
          "matchType": "regexp",
          "spanNames": [
            "login.*"
          ]
        },
        "actions": [
          {
            "key": "password",
            "value": "obfuscated",
            "action": "update"
          },
          {
            "key": "token",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

## <a name="span-processors"></a>Zakresy procesorów

Procesor span modyfikuje nazwę lub atrybuty zakresu na podstawie nazwy zakresu. Opcjonalnie obsługuje możliwość dołączania/wykluczania zakresów.

### <a name="name-a-span"></a>Nazwij zakres

Następujące ustawienie jest wymagane jako część sekcji Name:

* `fromAttributes`: Wartość atrybutu dla kluczy jest używana do tworzenia nowej nazwy w kolejności określonej w konfiguracji. Wszystkie klucze atrybutów należy określić w zakresie, w jakim procesor ma zmienić jego nazwę.

Można opcjonalnie skonfigurować następujące ustawienie:

* `separator`: Ciąg, który jest określony, będzie używany do dzielenia wartości
> [!NOTE]
> Jeśli zmiana nazwy jest zależna od atrybutów, które są modyfikowane przez procesor atrybutów, upewnij się, że procesor zakresu jest określony po procesor atrybutów w specyfikacji potoku.

#### <a name="sample-usage"></a>Przykładowe zastosowanie

W poniższym przykładzie określono wartości atrybutu "DB. svc", "Operation" i "ID" w postaci nowej nazwy zakresu, w tej kolejności, oddzielonej przez wartość "::".
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "fromAttributes": [
            "db.svc",
            "operation",
            "id"
          ],
          "separator": "::"
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-span-name"></a>Wyodrębnij atrybuty z nazwy zakresu

Przyjmuje listę wyrażeń regularnych w celu dopasowania nazwy zakresu względem i wyodrębnienia atrybutów z niej na podstawie podwyrażeń. Musi być określony w `toAttributes` sekcji.

Wymagane są następujące ustawienia:

`rules` : Lista reguł służących do wyodrębniania wartości atrybutów z nazwy zakresu. Wartości w nazwie zakresu są zamieniane na nazwy wyodrębnionych atrybutów. Każda reguła na liście jest ciągiem wzorca wyrażenia regularnego. Nazwa zakresu jest sprawdzana pod kątem wyrażenia regularnego. Jeśli wyrażenie regularne jest zgodne, wszystkie nazwane Podwyrażenie wyrażenia regularnego są wyodrębniane jako atrybuty i dodawane do zakresu. Każda nazwa podwyrażenia to nazwa atrybutu i Podwyrażenie dopasowane do części staną się wartością atrybutu. Dopasowana część w nazwie zakresu jest zastępowana przez wyodrębnioną nazwę atrybutu. Jeśli atrybuty już istnieją w zasięgu, zostaną nadpisywane. Ten proces jest powtarzany dla wszystkich reguł w kolejności, w jakiej zostały określone. Każda kolejna reguła działa w odniesieniu do nazwy zakresu, która jest danymi wyjściowymi po przetworzeniu poprzedniej reguły.

#### <a name="sample-usage"></a>Przykładowe zastosowanie

Załóżmy, że nazwa zakresu wejściowego to/API/V1/Document/12345678/Update. Zastosowanie następujących wyników w nazwie zakresu wyjściowego/api/v1/document/{documentId}/update spowoduje dodanie nowego atrybutu "element DocumentID" = "12345678" do zakresu.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "toAttributes": {
            "rules": [
              "^/api/v1/document/(?<documentId>.*)/update$"
            ]
          }
        }
      }
    ]
  }
}
```

Poniższy ilustruje zmianę nazwy zakresu na "{operation_website}" i dodanie atrybutu {Key: operation_website, Value: oldSpanName}, gdy zakres ma następujące właściwości:
- Nazwa zakresu zawiera znak "/" w dowolnym miejscu w ciągu.
- Nazwa zakresu nie jest "nie odgradzania/Change".
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "^(.*?)/(.*?)$"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "donot/change"
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [
              "(?<operation_website>.*?)$"
            ]
          }
        }
      }
    ]
  }
}
```