---
title: Przykłady procesorów telemetrii — Azure Monitor Application Insights dla języka Java
description: Przykłady ilustrujące procesory telemetryczne w Azure Monitor Application Insights dla języka Java
ms.topic: conceptual
ms.date: 12/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 9b29c9611359c97c4097ad0b90ee2673bb28f37c
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98696316"
---
# <a name="telemetry-processors-examples---azure-monitor-application-insights-for-java"></a>Przykłady procesorów telemetrii — Azure Monitor Application Insights dla języka Java

## <a name="includeexclude-samples"></a>Przykłady dołączania/wykluczania

### <a name="1-include-spans"></a>1. Uwzględnij zakresy

Poniższy przykład ilustruje zakres dla tego procesora atrybutów. Wszystkie inne zakresy, które nie pasują do właściwości, nie są przetwarzane przez ten procesor.

Poniżej przedstawiono warunki, które należy spełnić w celu dopasowania:
* Nazwa zakresu musi być równa "span" lub "spanB" 

Poniżej znajdują się zakresy zgodne z właściwościami include i są stosowane akcje procesora.
* Nazwa Span1: "span" atrybutów: {env: dev, test_request: 123, credit_card: 1234}
* Nazwa Span2: "spanB" atrybutów: {env: dev, test_request: false}
* Nazwa Span3: "span" atrybutów: {env: 1, test_request: dev, credit_card: 1234}

Poniższy zakres nie jest zgodny z właściwościami include i nie są stosowane akcje procesora.
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

### <a name="2-exclude-spans"></a>2. Wyklucz zakresy

Poniżej pokazano, z wyłączeniem zakresów dla tego procesora atrybutów. Wszystkie zakresy, które pasują do właściwości, nie są przetwarzane przez ten procesor.

Poniżej przedstawiono warunki, które należy spełnić w celu dopasowania:
* Nazwa zakresu musi być równa "span" lub "spanB" 

Poniżej znajdują się zakresy zgodne z właściwościami Exclude i nie są stosowane akcje procesora.
* Nazwa Span1: "span" atrybutów: {env: dev, test_request: 123, credit_card: 1234}
* Nazwa Span2: "spanB" atrybutów: {env: dev, test_request: false}
* Nazwa Span3: "span" atrybutów: {env: 1, test_request: dev, credit_card: 1234}

Poniższy zakres nie jest zgodny z właściwościami wykluczania i są stosowane akcje procesora.
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

### <a name="3-excludemulti-spans"></a>3. ExcludeMulti zakresy

Poniżej pokazano, z wyłączeniem zakresów dla tego procesora atrybutów. Wszystkie zakresy, które pasują do właściwości, nie są przetwarzane przez ten procesor.

Poniżej przedstawiono warunki, które należy spełnić w celu dopasowania:
* Atrybut ("ENV", "dev") musi znajdować się w zakresie dla dopasowania.
* Pod warunkiem, że istnieje atrybut z kluczem "test_request" w zakresie, w którym występuje dopasowanie.

Poniżej znajdują się zakresy zgodne z właściwościami Exclude i nie są stosowane akcje procesora.
* Nazwa Span1: "spanB" atrybutów: {env: dev, test_request: 123, credit_card: 1234}
* Nazwa Span2: "span" atrybutów: {env: dev, test_request: false}

Poniższy zakres nie jest zgodny z właściwościami wykluczania i są stosowane akcje procesora.
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

### <a name="4-selective-processing"></a>4. przetwarzanie selektywne

Poniżej pokazano, jak określić zestaw właściwości zakresu, aby wskazać, które zakresy obejmują ten procesor. `include`Właściwości mówią, które powinny być uwzględnione, a właściwości w `exclude` dalszej odfiltrować zakresy, które nie powinny być przetwarzane.

W poniższej konfiguracji są stosowane następujące zakresy zgodne z właściwościami i akcjami procesora:

* Nazwa Span1: "spanB" atrybutów: {env: production, test_request: 123, credit_card: 1234, redact_trace: "false"}
* Nazwa Span2: "span" atrybutów: {env: przemieszczanie, test_request: false, redact_trace: true}

Poniższe zakresy nie pasują do właściwości include i nie są stosowane:
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

Poniższy kod wstawia nowy atrybut {"Attribute1": "attributeValue1"} do zakresu, w którym klucz "Attribute1" nie istnieje.

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

Poniższe elementy używają wartości z atrybutu "anotherkey", aby wstawić nowy atrybut {"newKey": "value from Attribute" anotherkey "} do zakresu, w którym klucz" newKey "nie istnieje. Jeśli atrybut "anotherkey" nie istnieje, nowy atrybut nie zostanie wstawiony do zakresu.

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

Następujący element aktualizuje atrybut {"DB. Secret": "redagowane"} i aktualizuje atrybut "Boo" przy użyciu wartości z atrybutu "foo". Zakresy bez atrybutu "Boo" nie zmienią się.

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

Poniższy ilustruje usuwanie atrybutu z kluczem "credit_card".

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

Poniżej przedstawiono wartość skrótu istniejące wartości atrybutu.

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

Poniższy przykład ilustruje użycie wyrażenia regularnego do tworzenia nowych atrybutów na podstawie wartości innego atrybutu.
Na przykład w przypadku protokołu HTTP. URL = ' http://example.com/path?queryParam1=value1 , queryParam2 = wartość2 ' zostaną wstawione następujące atrybuty:
* httpProtocol: http
* httpDomain: example.com
* httpPath: ścieżka
* httpQueryParams: queryParam1 = wartość1, queryParam2 = wartość2
* wartość http. URL nie zmienia się.

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


## <a name="span-processor-samples"></a>Span przykłady procesora

### <a name="name-a-span"></a>Nazwij zakres

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

### <a name="extract-attributes-from-span-name-with-include-and-exclude"></a>Wyodrębnij atrybuty z nazwy zakresu przy użyciu dołączania i wykluczania

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