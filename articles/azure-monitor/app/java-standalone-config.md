---
title: Opcje konfiguracji — Azure Monitor Application Insights dla języka Java
description: Jak skonfigurować Application Insights Azure Monitor dla języka Java
ms.topic: conceptual
ms.date: 11/04/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 4ed3b3d60be0e5e4bedcb604ce021f6a64002120
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201254"
---
# <a name="configuration-options---azure-monitor-application-insights-for-java"></a>Opcje konfiguracji — Azure Monitor Application Insights dla języka Java

> [!WARNING]
> **W przypadku uaktualniania programu z wersji zapoznawczej 3,0**
>
> Dokładnie Przejrzyj wszystkie opcje konfiguracji, ponieważ struktura JSON została całkowicie zmieniona, oprócz samej nazwy pliku, która wystąpiła tylko małymi literami.

## <a name="connection-string-and-role-name"></a>Parametry połączenia i nazwa roli

Parametry połączenia i nazwa roli to najczęściej używane ustawienia, które należy wykonać, aby rozpocząć:

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  }
}
```

Parametry połączenia są wymagane, a nazwa roli jest ważna za każdym razem, gdy wysyłasz dane z różnych aplikacji do tego samego Application Insightsgo zasobu.

Poniżej znajdziesz więcej szczegółów i dodatkowe opcje konfiguracji.

## <a name="configuration-file-path"></a>Ścieżka pliku konfiguracji

Domyślnie Application Insights Java 3,0 oczekuje, że plik konfiguracji jest nazwany `applicationinsights.json` i znajduje się w tym samym katalogu co `applicationinsights-agent-3.0.2.jar` .

Ścieżkę do pliku konfiguracji można określić przy użyciu opcji

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` Zmienna środowiskowa lub
* `applicationinsights.configuration.file` Właściwość systemu Java

W przypadku określenia ścieżki względnej zostanie ona rozwiązany względem katalogu, w którym `applicationinsights-agent-3.0.2.jar` znajduje się lokalizacja.

## <a name="connection-string"></a>Parametry połączenia

Parametry połączenia są wymagane. Parametry połączenia można znaleźć w zasobie Application Insights:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights parametry połączenia":::


```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Parametry połączenia można również ustawić przy użyciu zmiennej środowiskowej `APPLICATIONINSIGHTS_CONNECTION_STRING` (która będzie mieć pierwszeństwo, jeśli parametry połączenia są również określone w konfiguracji JSON).

Ustawienie parametrów połączenia spowoduje wyłączenie agenta Java.

## <a name="cloud-role-name"></a>Nazwa roli w chmurze

Nazwa roli w chmurze służy do etykietowania składnika na mapie aplikacji.

Jeśli chcesz ustawić nazwę roli w chmurze:

```json
{
  "role": {   
    "name": "my cloud role name"
  }
}
```

Jeśli nazwa roli chmury nie jest ustawiona, nazwa zasobu Application Insights zostanie użyta do etykietowania składnika na mapie aplikacji.

Możesz również ustawić nazwę roli w chmurze przy użyciu zmiennej środowiskowej `APPLICATIONINSIGHTS_ROLE_NAME` (która będzie mieć pierwszeństwo, jeśli nazwa roli chmury zostanie również określona w konfiguracji JSON).

## <a name="cloud-role-instance"></a>Wystąpienie roli w chmurze

Nazwa maszyny jest wartością domyślną wystąpienia roli chmury.

Jeśli chcesz ustawić inną rolę w chmurze, a nie nazwę komputera:

```json
{
  "role": {
    "name": "my cloud role name",
    "instance": "my cloud role instance"
  }
}
```

Można również ustawić wystąpienie roli w chmurze przy użyciu zmiennej środowiskowej `APPLICATIONINSIGHTS_ROLE_INSTANCE` (która będzie mieć pierwszeństwo, jeśli wystąpienie roli chmury zostanie również określone w konfiguracji JSON).

## <a name="sampling"></a>Próbkowanie

Próbkowanie jest przydatne, jeśli zachodzi potrzeba obniżenia kosztów.
Próbkowanie jest wykonywane jako funkcja w IDENTYFIKATORze operacji (znanym także jako identyfikator śledzenia), dzięki czemu ten sam Identyfikator operacji będzie zawsze powodował tę samą decyzję o próbkowaniu. Dzięki temu nie będzie można pobrać części transakcji rozproszonej próbkowanej w czasie, gdy inne części tego elementu są próbkowane.

Jeśli na przykład ustawisz próbkowanie do 10%, zobaczysz tylko 10% transakcji, ale każdy z nich będzie miał pełne szczegółowe informacje o transakcjach.

Oto przykład sposobu ustawiania próbkowania w celu przechwycenia około **1/3 wszystkich transakcji** — upewnij się, że ustawiono częstotliwość próbkowania poprawną dla przypadku użycia:

```json
{
  "sampling": {
    "percentage": 33.333
  }
}
```

Można również ustawić wartość procentową próbkowania przy użyciu zmiennej środowiskowej `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` (która następnie ma pierwszeństwo, jeśli procent próbkowania jest również określony w konfiguracji JSON).

> [!NOTE]
> W polu procent próbkowania wybierz wartość procentową zbliżoną do 100/N, gdzie N jest liczbą całkowitą. Obecnie próbkowanie nie obsługuje innych wartości.

## <a name="jmx-metrics"></a>Metryki JMX

Jeśli chcesz zebrać pewne dodatkowe metryki JMX:

```json
{
  "jmxMetrics": [
    {
      "name": "JVM uptime (millis)",
      "objectName": "java.lang:type=Runtime",
      "attribute": "Uptime"
    },
    {
      "name": "MetaSpace Used",
      "objectName": "java.lang:type=MemoryPool,name=Metaspace",
      "attribute": "Usage.used"
    }
  ]
}
```

`name` to nazwa metryki, która zostanie przypisana do tej metryki JMX (może być dowolna).

`objectName` jest [nazwą obiektu](https://docs.oracle.com/javase/8/docs/api/javax/management/ObjectName.html) komponentu JMX, który chcesz zebrać.

`attribute` to nazwa atrybutu w JMX MBean, który ma zostać zebrany.

Obsługiwane są wartości liczbowe i logiczne metryki JMX. Metryki logiczne JMX są mapowane na `0` wartość false, a `1` dla wartości true.

## <a name="custom-dimensions"></a>Wymiary niestandardowe

Jeśli chcesz dodać wymiary niestandardowe do wszystkich danych telemetrycznych:

```json
{
  "customDimensions": {
    "mytag": "my value",
    "anothertag": "${ANOTHER_VALUE}"
  }
}
```

`${...}` można go użyć do odczytu wartości z określonej zmiennej środowiskowej podczas uruchamiania.

> [!NOTE]
> Rozpoczynając od wersji 3.0.2, jeśli dodasz niestandardowy wymiar o nazwie `service.version` , wartość zostanie zapisana w `application_Version` kolumnie w tabeli dzienników Application Insights, a nie jako wymiar niestandardowy.

## <a name="telemetry-processors-preview"></a>Procesory telemetrii (wersja zapoznawcza)

Ta funkcja jest dostępna w wersji zapoznawczej.

Umożliwia konfigurowanie reguł, które będą stosowane do żądania, zależności i danych telemetrycznych śledzenia, na przykład:
 * Maskowanie danych poufnych
 * Warunkowe dodawanie wymiarów niestandardowych
 * Aktualizowanie nazwy telemetrii używanej do agregacji i wyświetlania

Aby uzyskać więcej informacji, zapoznaj się z dokumentacją dotyczącą [procesora telemetrii](./java-standalone-telemetry-processors.md) .

## <a name="auto-collected-logging"></a>Rejestrowanie z autozbieraniem

Log4J, Logback i Java. util. Logging są autoinstrumentami, a rejestrowanie wykonywane za pośrednictwem tych platform rejestrowania jest zbierane z autogromadzeniem.

Rejestrowanie jest przechwytywane tylko wtedy, gdy najpierw spełni skonfigurowany próg dla struktur rejestrowania, a druga również spełnia wartość Application Insights skonfigurowany próg.

Domyślny próg Application Insights wynosi `INFO` . Jeśli chcesz zmienić ten poziom:

```json
{
  "instrumentation": {
    "logging": {
      "level": "WARN"
    }
  }
}
```

Możesz również ustawić poziom przy użyciu zmiennej środowiskowej `APPLICATIONINSIGHTS_INSTRUMENTATION_LOGGING_LEVEL` (która będzie mieć pierwszeństwo, jeśli poziom zostanie również określony w konfiguracji JSON).

Są to prawidłowe `level` wartości, które można określić w `applicationinsights.json` pliku oraz jak są one zgodne z poziomami rejestrowania w różnych strukturach rejestrowania:

| poziom             | Log4J  | Logback | LIP     |
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

> [!NOTE]
> Jeśli obiekt wyjątku zostanie przekazana do rejestratora, komunikat dziennika (i szczegóły obiektu wyjątku) będzie wyświetlany w Azure Portal w `exceptions` tabeli zamiast `traces` tabeli.

## <a name="auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics"></a>Zbierane metryki Micrometer (w tym metryki uruchamiającego rozruch z sprężyną)

Jeśli aplikacja używa [Micrometer](https://micrometer.io), metryki, które są wysyłane do rejestru globalnego Micrometer, są zbierane na podstawie autodostrajania.

Ponadto, jeśli aplikacja korzysta z [uruchamiającego uruchamianie sprężynowe](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html), metryki skonfigurowane przez siłownik rozruchu sprężyny są również zbierane domyślnie.

Aby wyłączyć autozbieranie metryk Micrometer (w tym metryki uruchamiającego uruchamianie sprężynowe):

> [!NOTE]
> Metryki niestandardowe są rozliczane osobno i mogą generować dodatkowe koszty. Sprawdź szczegółowe [Informacje o cenach](https://azure.microsoft.com/pricing/details/monitor/). Aby wyłączyć metryki siłownika Micrometer i sprężyny, Dodaj poniższą konfigurację do pliku konfiguracji.

```json
{
  "instrumentation": {
    "micrometer": {
      "enabled": false
    }
  }
}
```

## <a name="suppressing-specific-auto-collected-telemetry"></a>Pomijanie określonych autozbieranych danych telemetrycznych

Rozpoczynając od wersji 3.0.2, można pominąć określoną funkcję automatycznej zebranej telemetrii przy użyciu następujących opcji konfiguracji:

```json
{
  "instrumentation": {
    "cassandra": {
      "enabled": false
    },
    "jdbc": {
      "enabled": false
    },
    "kafka": {
      "enabled": false
    },
    "micrometer": {
      "enabled": false
    },
    "mongo": {
      "enabled": false
    },
    "redis": {
      "enabled": false
    }
  }
}
```

## <a name="heartbeat"></a>Puls

Domyślnie Application Insights Java 3,0 wysyła metrykę pulsu co 15 minut. Jeśli używasz metryki pulsu do wyzwalania alertów, możesz zwiększyć częstotliwość tego pulsu:

```json
{
  "heartbeat": {
    "intervalSeconds": 60
  }
}
```

> [!NOTE]
> Nie można zwiększyć interwału do dłużej niż 15 minut, ponieważ dane pulsu są również używane do śledzenia użycia Application Insights.

## <a name="http-proxy"></a>Serwer proxy HTTP

Jeśli aplikacja znajduje się za zaporą i nie może połączyć się bezpośrednio z Application Insights (zobacz [adresy IP używane przez Application Insights](./ip-addresses.md)), można skonfigurować Application Insights Java 3,0 do korzystania z serwera proxy http:

```json
{
  "proxy": {
    "host": "myproxy",
    "port": 8080
  }
}
```

Application Insights Java 3,0 odnoszą się również do globalnych `-Dhttps.proxyHost` i, `-Dhttps.proxyPort` Jeśli są ustawione.

## <a name="metric-interval"></a>Interwał metryk

Ta funkcja jest dostępna w wersji zapoznawczej.

Domyślnie metryki są przechwytywane co 60 sekund.

Począwszy od wersji 3.0.3-BETA można zmienić ten interwał:

```json
{
  "preview": {
    "metricIntervalSeconds": 300
  }
}
```

To ustawienie ma zastosowanie do wszystkich tych metryk:

* Domyślne liczniki wydajności, np. procesor CPU i pamięć
* Domyślne metryki niestandardowe, np. czas wyrzucania elementów bezużytecznych
* Skonfigurowane metryki JMX ([patrz powyżej](#jmx-metrics))
* Metryki Micrometer ([patrz powyżej](#auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics))


[//]: # "Uwaga Obsługa OpenTelemetry jest w prywatnej wersji zapoznawczej do momentu, gdy interfejs API OpenTelemetry osiągnie 1,0"

[//]: # "Obsługa # # w przypadku wersji pre-1,0 interfejsu API OpenTelemetry"

[//]: # "Obsługa wersji pre-1,0 interfejsu API OpenTelemetry jest zgodą, ponieważ interfejs API OpenTelemetry nie jest jeszcze stabilny"
[//]: # "w związku z tym każda wersja agenta obsługuje tylko specjalne wersje 1,0 interfejsu API OpenTelemetry"
[//]: # "(to ograniczenie nie zostanie zastosowane po wydaniu interfejsu API OpenTelemetry 1,0)."

[//]: # "plik JSON "" ""
[//]: # "{"
[//]: # "  \"wersja zapoznawcza \" : {"
[//]: # "    \"openTelemetryApiSupport \" : true"
[//]: # "  }"
[//]: # "}"
[//]: # "```"

## <a name="self-diagnostics"></a>Samodiagnostyka

"Samodiagnostyka" odnosi się do rejestrowania wewnętrznego z Application Insights Java 3,0.

Ta funkcja może być przydatna w przypadku wykrywania trendów i diagnozowania problemów Application Insights samego siebie.

Domyślnie program Application Insights dzienniki Java 3,0 na poziomie `INFO` zarówno do pliku, `applicationinsights.log` jak i konsoli programu, odpowiadającej tej konfiguracji:

```json
{
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```

`destination` może być jedną z `file` , `console` lub `file+console` .

`level` może być jedną z `OFF` , `ERROR` ,,, `WARN` `INFO` `DEBUG` lub `TRACE` .

`path` może być ścieżką bezwzględną lub względną. Ścieżki względne są rozwiązywane względem katalogu, gdzie się `applicationinsights-agent-3.0.2.jar` znajduje.

`maxSizeMb` jest maksymalnym rozmiarem pliku dziennika przed przekazaniem go.

`maxHistory` to liczba rzutowanych plików dziennika (oprócz bieżącego pliku dziennika).

Rozpoczynając od wersji 3.0.2, można również ustawić samodiagnostykę `level` przy użyciu zmiennej środowiskowej `APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL` (która będzie mieć pierwszeństwo, jeśli w `level` konfiguracji JSON zostanie także określona opcja samodiagnostyka).

## <a name="an-example"></a>Przykład

Jest to tylko przykład pokazujący, jak wygląda plik konfiguracji z wieloma składnikami.
Skonfiguruj określone opcje w zależności od potrzeb.

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  },
  "sampling": {
    "percentage": 100
  },
  "jmxMetrics": [
  ],
  "customDimensions": {
  },
  "instrumentation": {
    "logging": {
      "level": "INFO"
    },
    "micrometer": {
      "enabled": true
    }
  },
  "proxy": {
  },
  "preview": {
    "processors": [
    ]
  },
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```
