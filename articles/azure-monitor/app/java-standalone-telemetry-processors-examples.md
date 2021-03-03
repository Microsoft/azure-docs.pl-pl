---
title: Przykłady procesora telemetrii — Azure Monitor Application Insights dla środowiska Java
description: Poznaj przykłady pokazujące procesory telemetryczne w Azure Monitor Application Insights dla środowiska Java.
ms.topic: conceptual
ms.date: 12/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 0978bd669855d264ed6dfa5eeddc45ad499aa2a5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101734591"
---
# <a name="telemetry-processor-examples---azure-monitor-application-insights-for-java"></a>Przykłady procesora telemetrii — Azure Monitor Application Insights dla środowiska Java

W tym artykule przedstawiono przykłady procesorów telemetrycznych w programie Application Insights for Java. Znajdziesz przykłady dla konfiguracji dołączania i wykluczania. Znajdziesz również przykłady dla procesorów atrybutów i procesorów.
## <a name="include-and-exclude-samples"></a>Przykłady dołączania i wykluczania

W tej sekcji zobaczysz, jak dołączać i wykluczać zakresy. Zobaczysz również, jak wykluczyć wiele zakresów i zastosować przetwarzanie selektywne.
### <a name="include-spans"></a>Uwzględnij zakresy

W tej sekcji pokazano, jak uwzględnić zakresy dla procesora atrybutów. Zakresy, które nie są zgodne z właściwościami, nie są przetwarzane przez procesor.

Dopasowanie wymaga, aby nazwa zakresu była równa `spanA` lub `spanB` . 

Te zakresy są zgodne z właściwościami dołączania i są stosowane akcje procesora:
* Nazwa Span1: "span" atrybutów: {env: dev, test_request: 123, credit_card: 1234}
* Nazwa Span2: "spanB" atrybutów: {env: dev, test_request: false}
* Nazwa Span3: "span" atrybutów: {env: 1, test_request: dev, credit_card: 1234}

Ten zakres nie jest zgodny z właściwościami include i nie są stosowane akcje procesora:
* Nazwa Span4: "spanC" atrybutów: {env: dev, test_request: false}

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
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="exclude-spans"></a>Wyklucz zakresy

W tej sekcji pokazano, jak wykluczyć zakresy dla procesora atrybutu. Zakresy, które pasują do właściwości, nie są przetwarzane przez ten procesor.

Dopasowanie wymaga, aby nazwa zakresu była równa `spanA` lub `spanB` .

Poniższe zakresy są zgodne z właściwościami wykluczania i nie są stosowane akcje procesora:
* Nazwa Span1: "span" atrybutów: {env: dev, test_request: 123, credit_card: 1234}
* Nazwa Span2: "spanB" atrybutów: {env: dev, test_request: false}
* Nazwa Span3: "span" atrybutów: {env: 1, test_request: dev, credit_card: 1234}

Ten zakres nie jest zgodny z właściwościami wykluczania i są stosowane akcje procesora:
* Nazwa Span4: "spanC" atrybutów: {env: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="exclude-spans-by-using-multiple-criteria"></a>Wyklucz zakresy przy użyciu wielu kryteriów

W tej sekcji pokazano, jak wykluczyć zakresy dla procesora atrybutu. Zakresy, które pasują do właściwości, nie są przetwarzane przez ten procesor.

Dopasowanie wymaga spełnienia następujących warunków:
* Atrybut (na przykład `env` lub `dev` ) musi znajdować się w zakresie.
* Zakres musi mieć atrybut, który ma klucz `test_request` .

Poniższe zakresy są zgodne z właściwościami wykluczania i nie są stosowane akcje procesora.
* Nazwa Span1: "spanB" atrybutów: {env: dev, test_request: 123, credit_card: 1234}
* Nazwa Span2: "span" atrybutów: {env: dev, test_request: false}

Poniższy zakres nie jest zgodny z właściwościami wykluczania i są stosowane akcje procesora:
* Nazwa Span3: "spanB" atrybutów: {env: 1, test_request: dev, credit_card: 1234}
* Nazwa Span4: "spanC" atrybutów: {env: dev, dev_request: false}


```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ],
          "attributes": [
            {
              "key": "env",
              "value": "dev"
            },
            {
              "key": "test_request"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="selective-processing"></a>Przetwarzanie selektywne

W tej sekcji pokazano, jak określić zbiór właściwości zakresu wskazujący, które zakresy obejmują ten procesor. Właściwości include wskazują, które zakresy powinny być przetwarzane. Właściwości wykluczania filtrują zakresy, które nie powinny być przetwarzane.

W poniższej konfiguracji te zakresy są zgodne z właściwościami i są stosowane akcje procesora:

* Nazwa Span1: "spanB" atrybutów: {env: production, test_request: 123, credit_card: 1234, redact_trace: "false"}
* Nazwa Span2: "span" atrybutów: {env: przemieszczanie, test_request: false, redact_trace: true}

Te zakresy nie pasują do właściwości include i nie są stosowane akcje procesora:
* Nazwa Span3: "spanB" atrybuty: {env: Product, test_request: true, credit_card: 1234, redact_trace: false}
* Nazwa Span4: "spanC" atrybutów: {env: dev, test_request: false}

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
  }
}
```
## <a name="attribute-processor-samples"></a>Przykłady procesora atrybutów

### <a name="insert"></a>Insert

Poniższy przykład wstawia nowy atrybut `{"attribute1": "attributeValue1"}` do zakresów, w których klucz `attribute1` nie istnieje.

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
            "value": "attributeValue1",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="insert-from-another-key"></a>Wstaw z innego klucza

Poniższy przykład używa wartości z atrybutu, `anotherkey` Aby wstawić nowy atrybut `{"newKey": "<value from attribute anotherkey>"}` do zakresu, w którym klucz `newKey` nie istnieje. Jeśli atrybut `anotherkey` nie istnieje, nowy atrybut nie zostanie wstawiony do zakresów.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "newKey",
            "fromAttribute": "anotherKey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="update"></a>Aktualizacja

Poniższy przykład aktualizuje atrybut do `{"db.secret": "redacted"}` . Aktualizuje atrybut przy `boo` użyciu wartości z atrybutu `foo` . Zakresy, które nie mają atrybutu, `boo` nie zmieniają się.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "db.secret",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "boo",
            "fromAttribute": "foo",
            "action": "update" 
          }
        ]
      }
    ]
  }
}
```

### <a name="delete"></a>Usuń

Poniższy przykład pokazuje, jak usunąć atrybut, który ma klucz `credit_card` .

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="hash"></a>Skrót

Poniższy przykład pokazuje, jak skrócić istniejące wartości atrybutów.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
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

### <a name="extract"></a>Wyodrębnianie

Poniższy przykład pokazuje, jak używać wyrażenia regularnego (regularnych) do tworzenia nowych atrybutów na podstawie wartości innego atrybutu.
Na przykład podano `http.url = http://example.com/path?queryParam1=value1,queryParam2=value2` następujące atrybuty:
* httpProtocol: `http`
* httpDomain: `example.com`
* httpPath: `path`
* httpQueryParams: `queryParam1=value1,queryParam2=value2`
* http. URL: *Brak* zmian

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "http.url",
            "pattern": "^(?<httpProtocol>.*):\\/\\/(?<httpDomain>.*)\\/(?<httpPath>.*)(\\?|\\&)(?<httpQueryParams>.*)",
            "action": "extract"
          }
        ]
      }
    ]
  }
}
```

Poniższy przykład pokazuje, jak przetwarzać zakresy, które mają nazwę zakresu, która pasuje do wzorców wyrażeń regularnych.
Ten procesor usuwa `token` atrybut. Zasłania `password` atrybut w zakresach, gdzie nazwa zakresu jest zgodna `auth.*` i gdzie nazwa zakresu nie jest zgodna `login.*` .

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


## <a name="span-processor-samples"></a>Span przykłady procesora

### <a name="name-a-span"></a>Nazwij zakres

W poniższym przykładzie określono wartości atrybutów `db.svc` , `operation` , i `id` . Tworzy nową nazwę zakresu przy użyciu tych atrybutów, w tej kolejności, oddzielonych wartością `::` .
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

### <a name="extract-attributes-from-a-span-name"></a>Wyodrębnij atrybuty z nazwy zakresu

Załóżmy, że nazwa zakresu wejściowego to `/api/v1/document/12345678/update` . Poniższy przykład powoduje, że nazwa zakresu danych wyjściowych `/api/v1/document/{documentId}/update` . Dodaje nowy atrybut `documentId=12345678` do zakresu.
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

### <a name="extract-attributes-from-a-span-name-by-using-include-and-exclude"></a>Wyodrębnij atrybuty z nazwy zakresu przy użyciu funkcji include i Exclude

Poniższy przykład pokazuje, jak zmienić nazwę zakresu na `{operation_website}` . Dodaje atrybut z kluczem `operation_website` i wartością, `{oldSpanName}` gdy zakres ma następujące właściwości:
- Nazwa zakresu zawiera `/` dowolne miejsce w ciągu.
- Nazwa zakresu nie jest `donot/change` .
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
