---
title: Monitoruj aplikacje Java w dowolnym miejscu — Azure Monitor Application Insights
description: Monitorowanie wydajności aplikacji bezkodowej dla aplikacji Java działających w dowolnym środowisku bez Instrumentacji aplikacji. Znajdź główną przyczynę problemów d przy użyciu śledzenia rozproszonego i mapy aplikacji.
ms.topic: conceptual
ms.date: 04/16/2020
ms.custom: devx-track-java
ms.openlocfilehash: 9b90f8b9336111438b4b832d557d448470959255
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91537661"
---
# <a name="configuration-options---java-standalone-agent-for-azure-monitor-application-insights"></a>Opcje konfiguracji — autonomiczny Agent Java dla Azure Monitor Application Insights



## <a name="connection-string-and-role-name"></a>Parametry połączenia i nazwa roli

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

Parametry połączenia są wymagane, a nazwa roli jest ważna za każdym razem, gdy wysyłasz dane z różnych aplikacji do tego samego Application Insightsgo zasobu.

Aby uzyskać więcej informacji, Zobacz więcej szczegółów i dodatkowe opcje konfiguracji.

## <a name="configuration-file-path"></a>Ścieżka pliku konfiguracji

Domyślnie program Application Insights Java 3,0 Preview oczekuje, że plik konfiguracji jest nazwany `ApplicationInsights.json` i znajduje się w tym samym katalogu, co `applicationinsights-agent-3.0.0-PREVIEW.5.jar` .

Ścieżkę do pliku konfiguracji można określić przy użyciu opcji

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` Zmienna środowiskowa lub
* `applicationinsights.configurationFile` Właściwość systemu Java

W przypadku określenia ścieżki względnej zostanie ona rozwiązany względem katalogu, w którym `applicationinsights-agent-3.0.0-PREVIEW.5.jar` znajduje się lokalizacja.

## <a name="connection-string"></a>Parametry połączenia

Jest to wymagane. Parametry połączenia można znaleźć w zasobie Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights parametry połączenia":::


```json
{
  "instrumentationSettings": {
    "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
  }
}
```

Parametry połączenia można również ustawić przy użyciu zmiennej środowiskowej `APPLICATIONINSIGHTS_CONNECTION_STRING` .

Ustawienie parametrów połączenia spowoduje wyłączenie agenta Java.

## <a name="cloud-role-name"></a>Nazwa roli w chmurze

Nazwa roli w chmurze służy do etykietowania składnika na mapie aplikacji.

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

Jeśli nazwa roli chmury nie jest ustawiona, nazwa zasobu Application Insights zostanie użyta do etykietowania składnika na mapie aplikacji.

Możesz również ustawić nazwę roli w chmurze przy użyciu zmiennej środowiskowej `APPLICATIONINSIGHTS_ROLE_NAME` .

## <a name="cloud-role-instance"></a>Wystąpienie roli w chmurze

Nazwa maszyny jest wartością domyślną wystąpienia roli chmury.

Jeśli chcesz ustawić inną rolę w chmurze, a nie nazwę komputera:

```json
{
  "instrumentationSettings": {
    "preview": {
      "roleInstance": "my cloud role instance"
    }
  }
}
```

Możesz również ustawić wystąpienie roli w chmurze przy użyciu zmiennej środowiskowej `APPLICATIONINSIGHTS_ROLE_INSTANCE` .

## <a name="application-log-capture"></a>Przechwytywanie dziennika aplikacji

Application Insights Java 3,0 Preview automatycznie przechwytuje rejestrowanie aplikacji za pośrednictwem Log4J, Logback i Java. util. Logging.

Domyślnie zostanie przechwycone rejestrowanie wykonane na `INFO` poziomie lub wyższym.

Jeśli chcesz zmienić ten próg:

```json
{
  "instrumentationSettings": {
    "preview": {
      "instrumentation": {
        "logging": {
          "threshold": "WARN"
        }
      }
    }
  }
}
```

Można również ustawić próg rejestrowania przy użyciu zmiennej środowiskowej `APPLICATIONINSIGHTS_LOGGING_THRESHOLD` .

Są to prawidłowe `threshold` wartości, które można określić w `ApplicationInsights.json` pliku i jak są one odnoszące się do poziomów rejestrowania w różnych strukturach rejestrowania:

| wartość progowa   | Log4J  | Logback | LIP     |
|-------------------|--------|---------|---------|
| WYŁ.               | WYŁ.    | WYŁ.     | WYŁ.     |
| KRYTYCZN             | KRYTYCZN  | BŁĄD   | WAŻNY  |
| BŁĄD (lub poważny) | BŁĄD  | BŁĄD   | WAŻNY  |
| WARN (lub ostrzeżenie) | WYOWIETLON   | WYOWIETLON    | OSTRZEŻENIE |
| INFORMACJE              | INFORMACJE   | INFORMACJE    | INFORMACJE    |
| SYGNATUR            | DEBUGUJ  | DEBUGUJ   | SYGNATUR  |
| Debugowanie (lub szczegółowe)   | DEBUGUJ  | DEBUGUJ   | ZAKTUALIZOWA    |
| DOKŁADNIEJSZ             | DEBUGUJ  | DEBUGUJ   | DOKŁADNIEJSZ   |
| TRACE (lub FINEST) | TRACE  | TRACE   | FINEST  |
| ALL               | ALL    | ALL     | ALL     |

## <a name="jmx-metrics"></a>Metryki JMX

Jeśli masz pewne JMX metryki, które chcesz przechwytywać:

```json
{
  "instrumentationSettings": {
    "preview": {
      "jmxMetrics": [
        {
          "objectName": "java.lang:type=Runtime",
          "attribute": "Uptime",
          "display": "JVM uptime (millis)"
        },
        {
          "objectName": "java.lang:type=MemoryPool,name=Metaspace",
          "attribute": "Usage.used",
          "display": "MetaSpace Used"
        }
      ]
    }
  }
}
```

Metryki JMX można również ustawić przy użyciu zmiennej środowiskowej `APPLICATIONINSIGHTS_JMX_METRICS` .

Ta zmienna środowiskowa zawartości musi być danymi JSON pasującymi do powyższej struktury, np. `[{"objectName": "java.lang:type=Runtime", "attribute": "Uptime", "display": "JVM uptime (millis)"}, {"objectName": "java.lang:type=MemoryPool,name=Metaspace", "attribute": "Usage.used", "display": "MetaSpace Used"}]`

## <a name="micrometer-including-metrics-from-spring-boot-actuator"></a>Micrometer (w tym metryki z siłownika rozruchu sprężynowego)

Jeśli aplikacja używa [Micrometer](https://micrometer.io), Application Insights 3,0 (począwszy od wersji zapoznawczej. 2) teraz przechwytuje metryki wysyłane do rejestru globalnego Micrometer.

Jeśli aplikacja korzysta z [siłownika uruchomienia sprężynowego](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html), Application Insights 3,0 (począwszy od wersji zapoznawczej. 4) teraz przechwytuje metryki skonfigurowane przez siłownik rozruchu sprężynowego (który używa Micrometer, ale nie używa rejestru globalnego Micrometer).

Jeśli chcesz wyłączyć te funkcje:

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

Domyślnie program Application Insights Java 3,0 Preview wysyła metrykę pulsu co 15 minut. Jeśli używasz metryki pulsu do wyzwalania alertów, możesz zwiększyć częstotliwość tego pulsu:

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
> Nie można zmniejszyć częstotliwości tego pulsu, ponieważ dane pulsu są również używane do śledzenia użycia Application Insights.

## <a name="sampling"></a>Próbkowanie

Próbkowanie jest przydatne, jeśli zachodzi potrzeba obniżenia kosztów.
Próbkowanie jest wykonywane jako funkcja w IDENTYFIKATORze operacji (znanym także jako identyfikator śledzenia), dzięki czemu ten sam Identyfikator operacji będzie zawsze powodował tę samą decyzję o próbkowaniu. Dzięki temu nie będzie można pobrać części transakcji rozproszonej próbkowanej w czasie, gdy inne części tego elementu są próbkowane.

Jeśli na przykład ustawisz próbkowanie do 10%, zobaczysz tylko 10% transakcji, ale każdy z nich będzie miał pełne szczegółowe informacje o transakcjach.

Oto przykład sposobu ustawiania próbkowania do **10% wszystkich transakcji** — upewnij się, że ustawiono częstotliwość próbkowania poprawną dla przypadku użycia:

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

Możesz również ustawić procent próbkowania przy użyciu zmiennej środowiskowej `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` .

## <a name="http-proxy"></a>Serwer proxy HTTP

Jeśli aplikacja znajduje się za zaporą i nie może połączyć się bezpośrednio z Application Insights (zobacz [adresy IP używane przez Application Insights](./ip-addresses.md)), można skonfigurować Application Insights Java 3,0 Preview do korzystania z serwera proxy http:

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

## <a name="self-diagnostics"></a>Samodiagnostyka

"Samodiagnostyka" odnosi się do rejestrowania wewnętrznego z Application Insights Java 3,0 Preview.

Może to być przydatne w przypadku wykrywania trendów i diagnozowania problemów Application Insights samego siebie.

Domyślnie program loguje się do konsoli o poziomie `warn` odpowiadającym tej konfiguracji:

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

Prawidłowymi poziomami są `OFF` , `ERROR` , `WARN` ,, `INFO` `DEBUG` i `TRACE` .

Jeśli chcesz zalogować się do pliku zamiast rejestrowania w konsoli programu:

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

W przypadku korzystania z funkcji rejestrowania plików, gdy trafią pliki `maxSizeMB` , nastąpi Przerzucanie, a oprócz bieżącego pliku dziennika zostanie zachowany tylko ostatnio ukończony plik dziennika.
