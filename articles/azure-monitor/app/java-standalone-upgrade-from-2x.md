---
title: Uaktualnianie z wersji 2. x Azure Monitor Application Insights Java
description: Uaktualnianie z Azure Monitor Application Insights Java 2. x
ms.topic: conceptual
ms.date: 11/25/2020
ms.openlocfilehash: d1d09c09afbabd40a32cbb80f1901112c37ac3da
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96355139"
---
# <a name="upgrading-from-application-insights-java-sdk-2x"></a>Uaktualnianie z Application Insights Java SDK 2. x

Jeśli używasz już Application Insights Java SDK 2. x w aplikacji, nie musisz go usunąć.
Agent Java 3,0 wykryje go i przechwytuje i skorelowanie wszelkich niestandardowych danych telemetrycznych wysyłanych za pośrednictwem zestawu Java SDK 2. x, pomijając automatyczne kolekcje wykonywane przez zestaw Java SDK 2. x, aby zapobiec zduplikowanym telemetrii.

Jeśli używasz agenta Application Insights 2. x, musisz usunąć `-javaagent:` argument JVM, który wskazuje na agenta 2. x.

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers i TelemetryProcessors

Zestaw Java SDK 2. x TelemetryInitializers i TelemetryProcessors nie zostanie uruchomiony w przypadku korzystania z agenta 3,0.
Wiele przypadków użycia, które wcześniej wymagały, można rozwiązać w 3,0 przez skonfigurowanie [niestandardowych wymiarów](./java-standalone-config.md#custom-dimensions) lub skonfigurowanie [procesorów telemetrii](./java-standalone-telemetry-processors.md).

## <a name="multiple-applications-in-a-single-jvm"></a>Wiele aplikacji w jednym JVM

Obecnie 3,0 obsługuje tylko pojedyncze [Parametry połączenia i nazwę roli](./java-standalone-config.md#connection-string-and-role-name) na uruchomiony proces. W szczególności nie jest jeszcze dostępna wiele Tomcat Web Apps w tym samym wdrożeniu Tomcat przy użyciu różnych parametrów połączenia lub różnych nazw ról.

## <a name="http-request-telemetry-names"></a>Nazwy telemetrii żądania HTTP

Nazwy telemetrii żądań HTTP w 3,0 zostały zmienione w celu ogólnego udostępnienia lepszego zagregowanego widoku w portalu Application Insights U/X.

Jednak w przypadku niektórych aplikacji można nadal preferować Zagregowany widok w U/X, który został dostarczony przez poprzednie nazwy telemetrii, w takim przypadku można użyć funkcji w wersji zapoznawczej procesorów telemetrycznych w 3,0, aby powrócić do poprzednich nazw.

### <a name="to-prefix-the-telemetry-name-with-the-http-method-get-post-etc"></a>Aby prefiksować nazwę telemetrii za pomocą metody http ( `GET` , `POST` itp.):

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "attributes": [
            { "key": "http.method", "value": "" }
          ],
          "spanNames": [ "^/" ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "^(?<tempName>.*)$" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.method", "tempName" ],
          "separator": " "
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempName" }
          ]
        },
        "actions": [
          { "key": "tempName", "action": "delete" }
        ]
      }
    ]
  }
}
```

### <a name="to-set-the-telemetry-name-to-the-full-url-path"></a>Aby ustawić nazwę telemetrii na pełną ścieżkę URL

```
{
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.method" },
            { "key": "http.url" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.url" ]
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.method" },
            { "key": "http.url" }
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [ "https?://[^/]+(?<tempPath>/[^?]*)" ]
          }
        }
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "name": {
          "fromAttributes": [ "tempPath" ]
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "actions": [
          { "key": "tempPath", "action": "delete" }
        ]
      }
    ]
  }
}
```
