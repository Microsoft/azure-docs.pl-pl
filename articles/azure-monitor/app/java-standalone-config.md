---
title: Monitorowanie aplikacji Java w dowolnym miejscu — usługa Azure Monitor Application Insights
description: Bezkodowe monitorowanie wydajności aplikacji dla aplikacji Java działających w dowolnym środowisku bez instrumentacji aplikacji. Znajdź główną przyczynę problemów d przy użyciu rozproszonego śledzenia i mapy aplikacji.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 5d930d349a2ab1efbd7a61904874bf6bdb411889
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641890"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>Opcje konfiguracji — agent autonomiczny Java dla usługi Azure Monitor Application Insights



## <a name="connection-string-and-role-name"></a>Ciąg połączenia i nazwa roli

```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
    "preview": {
      "roleName": "my cloud role name"
    }
  }
}
```

Wymagany jest ciąg połączenia, a nazwa roli jest ważna za każdym razem, gdy wysyłasz dane z różnych aplikacji do tego samego zasobu usługi Application Insights.

Więcej informacji i dodatkowe opcje konfiguracji znajdziesz poniżej, aby uzyskać więcej informacji.

## <a name="configuration-file-path"></a>Ścieżka pliku konfiguracji

Domyślnie usługa Application Insights Java 3.0 Preview oczekuje, że plik konfiguracyjny zostanie nazwany `ApplicationInsights.json`i będzie znajdować się w tym samym katalogu co `applicationinsights-agent-3.0.0-PREVIEW.jar`.

Można określić własną ścieżkę pliku konfiguracyjnego za pomocą

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE`zmienną środowiskową, lub
* `applicationinsights.configurationFile`Właściwość systemu Java

Jeśli określisz ścieżkę względną, zostanie ona rozwiązana `applicationinsights-agent-3.0.0-PREVIEW.jar` względem katalogu, w którym się znajduje.

## <a name="connection-string"></a>Parametry połączenia

Jest to wymagane. Parametry połączenia można znaleźć w zasobie usługi Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Ciąg połączenia usługi Application Insights":::

Ciąg połączenia można również ustawić przy `APPLICATIONINSIGHTS_CONNECTION_STRING`użyciu zmiennej środowiskowej .

## <a name="cloud-role-name"></a>Nazwa roli w chmurze

Nazwa roli w chmurze jest używana do etykietowania składnika na mapie aplikacji.

Jeśli chcesz ustawić nazwę roli w chmurze:

```json
{
  "instrumentationSettings": {
    "preview": {   
      "roleName": "my cloud role name"
    }
  }
}
```

Jeśli nazwa roli w chmurze nie jest ustawiona, nazwa zasobu usługi Application Insights będzie używana do etykietowania składnika na mapie aplikacji.

Nazwę roli w chmurze można również `APPLICATIONINSIGHTS_ROLE_NAME`ustawić przy użyciu zmiennej środowiskowej .

## <a name="cloud-role-instance"></a>Wystąpienie roli w chmurze

Wystąpienie roli w chmurze domyślnie ma nazwę komputera.

Jeśli chcesz ustawić wystąpienie roli w chmurze na coś innego, a nie nazwę komputera:

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleInstance": "my cloud role instance"
    }
  }
}
```

Można również ustawić wystąpienie roli w `APPLICATIONINSIGHTS_ROLE_INSTANCE`chmurze przy użyciu zmiennej środowiskowej .

## <a name="application-log-capture"></a>Przechwytywanie dziennika aplikacji

Aplikacja Insights Java 3.0 Preview automatycznie przechwytuje rejestrowanie aplikacji za pośrednictwem Log4j, Logback i java.util.logging.

Domyślnie przechwytuje wszystkie rejestrowania `WARN` wykonywane na poziomie lub powyżej.

Jeśli chcesz zmienić ten próg:

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "logging": {
          "threshold": "ERROR"
        }
      }
    }
  }
}
```

Są to `threshold` prawidłowe wartości, które `ApplicationInsights.json` można określić w pliku i jak odpowiadają one poziomowi rejestrowania w różnych strukturach rejestrowania:

| `threshold`  | Log4j  | Logowanie | LIP     |
|--------------|--------|---------|---------|
| WYŁ.          | WYŁ.    | WYŁ.     | WYŁ.     |
| Śmiertelne        | Śmiertelne  | BŁĄD   | Ciężkie  |
| BŁĄD/POWAŻNY | BŁĄD  | BŁĄD   | Ciężkie  |
| OSTRZEGAJ/OSTRZEGAJ | Ostrzec   | Ostrzec    | OSTRZEŻENIE |
| Informacji         | Informacji   | Informacji    | Informacji    |
| Config       | DEBUGUJ  | DEBUGUJ   | Config  |
| DEBUGOWANIE/GRZYWNA   | DEBUGUJ  | DEBUGUJ   | Dobrze    |
| Drobniejsze        | DEBUGUJ  | DEBUGUJ   | Drobniejsze   |
| ŚLEDZENIE/NAJLEPSZE | TRACE  | TRACE   | Najlepszych  |
| ALL          | ALL    | ALL     | ALL     |

## <a name="jmx-metrics"></a>Metryki JMX

Jeśli masz jakieś metryki JMX, które cię interesują w przechwytywaniu:

```json
{
  "instrumentationSettings": {
    "preview": {
        "jmxMetrics": [
        {
          "objectName": "java.lang:type=ClassLoading",
          "attribute": "LoadedClassCount",
          "display": "Loaded Class Count"
        },
        {
          "objectName": "java.lang:type=MemoryPool,name=Code Cache",
          "attribute": "Usage.used",
          "display": "Code Cache Used"
        }
      ]
    }
  }
}
```

## <a name="micrometer"></a>Mikrometr

Domyślnie, jeśli aplikacja używa [Micrometer,](https://micrometer.io)Application Insights 3.0 (począwszy od Preview.2) teraz dodaje się do globalnego rejestru Micrometer i przechwytuje metryki micrometer.

Jeśli chcesz wyłączyć tę funkcję:

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "micrometer": {
          "enabled": false
        }
      }
    }
  }
}
```

## <a name="heartbeat"></a>Puls

Domyślnie usługa Application Insights Java 3.0 Preview wysyła metrykę pulsu raz na 15 minut. Jeśli używasz metryki pulsu do wyzwalania alertów, możesz zwiększyć częstotliwość tego bicia serca:

```json
{
  "instrumentationSettings": {
    "preview": {
        "heartbeat": {
            "intervalSeconds": 60
        }
    }
  }
}
```

> [!NOTE]
> Nie można zmniejszyć częstotliwości tego pulsu, ponieważ dane pulsu są również używane do śledzenia użycia usługi Application Insights.

## <a name="sampling"></a>Próbkowanie

Próbkowanie jest przydatne, jeśli trzeba zmniejszyć koszty.
Próbkowanie jest wykonywane jako funkcja identyfikatora operacji (znanego również jako identyfikator śledzenia), tak aby ten sam identyfikator operacji zawsze skutkował tą samą decyzją o pobieraniu próbek. Gwarantuje to, że nie będzie pobierać części transakcji rozproszonej próbkowane, podczas gdy inne części są próbkowane.

Na przykład jeśli ustawisz próbkowanie na 10%, zobaczysz tylko 10% transakcji, ale każdy z tych 10% będzie miał pełne szczegóły transakcji end-to-end.

Oto przykład, jak ustawić próbkowanie na **10% wszystkich transakcji** — upewnij się, że ustawiono częstotliwość próbkowania, która jest poprawna dla przypadku użycia:

```json
{
  "instrumentationSettings": {
    "preview": {
        "sampling": {
            "fixedRate": {
                "percentage": 10
            }
          }
        }
    }
}
```

## <a name="http-proxy"></a>HTTP Proxy

Jeśli aplikacja znajduje się za zaporą i nie może połączyć się bezpośrednio z usługą Application Insights (zobacz [adresy IP używane przez aplikację Application Insights),](https://docs.microsoft.com/azure/azure-monitor/app/ip-addresses)można skonfigurować aplikację Application Insights Java 3.0 Preview do używania serwera proxy HTTP:

```json
{
  "instrumentationSettings": {
    "preview": {
      "httpProxy": {
        "host": "myproxy",
        "port": 8080
      }
    }
  }
}
```

## <a name="self-diagnostics"></a>Autodiagnostyka

"Autodiagnostyka" odnosi się do wewnętrznego rejestrowania z aplikacji Application Insights Java 3.0 Preview.

Może to być przydatne w przypadku wykrywania i diagnozowania problemów z samym usługą Application Insights.

Domyślnie loguje się do `warn`konsoli z poziomem, odpowiadającym tej konfiguracji:

```json
{
  "instrumentationSettings": {
    "preview": {
        "selfDiagnostics": {
            "destination": "console",
            "level": "WARN"
        }
    }
  }
}
```

Prawidłowe `OFF`poziomy `ERROR` `WARN`to `INFO` `DEBUG`, `TRACE`, , , i .

Jeśli chcesz zalogować się do pliku zamiast rejestrowania do konsoli:

```json
{
  "instrumentationSettings": {
    "preview": {
        "selfDiagnostics": {
            "destination": "file",
            "directory": "/var/log/applicationinsights",
            "level": "WARN",
            "maxSizeMB": 10
        }    
    }
  }
}
```

Podczas korzystania z rejestrowania plików, po trafieniu `maxSizeMB`pliku, będzie on akcesji, zachowując tylko ostatnio ukończony plik dziennika oprócz bieżącego pliku dziennika.
