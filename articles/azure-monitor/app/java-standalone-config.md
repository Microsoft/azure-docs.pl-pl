---
title: Opcje konfiguracji — Azure Monitor Application Insights dla języka Java
description: Jak skonfigurować środowisko Azure Monitor Application Insights dla języka Java
ms.topic: conceptual
ms.date: 11/04/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: b78aaa659598e6eb58841c5cef0c209daaced5e0
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811980"
---
# <a name="configuration-options---azure-monitor-application-insights-for-java"></a>Opcje konfiguracji — Azure Monitor Application Insights dla języka Java

> [!WARNING]
> **W przypadku uaktualniania z wersji 3.0 (wersja zapoznawcza)**
>
> Przejrzyj dokładnie wszystkie poniższe opcje konfiguracji, ponieważ struktura JSON została całkowicie zmieniona, oprócz samej nazwy pliku, która zawierała tylko małe litery.

## <a name="connection-string-and-role-name"></a>Connection string and role name (Ciąg połączenia i nazwa roli)

Do rozpoczęcia pracy potrzebne są najpopularniejsze ustawienia parametrów połączenia i nazwy roli:

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  }
}
```

Wymagane są ciągi połączenia, a nazwa roli jest ważna za każdym razem, gdy wysyłasz dane z różnych aplikacji do tego samego Application Insights zasobów.

Więcej szczegółów i dodatkowe opcje konfiguracji można znaleźć poniżej.

## <a name="configuration-file-path"></a>Ścieżka pliku konfiguracji

Domyślnie program Application Insights Java 3.0 oczekuje, że plik konfiguracji będzie mieć nazwę i będzie się znajdował w tym samym katalogu co `applicationinsights.json` `applicationinsights-agent-3.0.3.jar` program .

Możesz określić własną ścieżkę pliku konfiguracji przy użyciu jednej z tych opcji

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` zmienna środowiskowa, lub
* `applicationinsights.configuration.file` Właściwość systemowa języka Java

Jeśli określisz ścieżkę względną, zostanie ona rozpoznana względem katalogu, w `applicationinsights-agent-3.0.3.jar` którym się znajduje.

## <a name="connection-string"></a>Parametry połączenia

Wymagane są ciągi połączenia. Możesz znaleźć swoje parametrów połączenia w zasobie Application Insights zasobów:

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights parametrów połączenia":::


```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Możesz również ustawić ciąg połączenia przy użyciu zmiennej środowiskowej (która będzie miała pierwszeństwo przed ciągami połączenia określonymi `APPLICATIONINSIGHTS_CONNECTION_STRING` w konfiguracji JSON).

Ustawienie parametrów połączenia spowoduje wyłączenie agenta języka Java.

## <a name="cloud-role-name"></a>Nazwa roli w chmurze

Nazwa roli w chmurze jest używana do oznaczania składnika na mapie aplikacji.

Jeśli chcesz ustawić nazwę roli w chmurze:

```json
{
  "role": {   
    "name": "my cloud role name"
  }
}
```

Jeśli nie ustawiono nazwy roli w chmurze, Application Insights nazwa zasobu będzie używana do oznaczania składnika na mapie aplikacji.

Możesz również ustawić nazwę roli w chmurze przy użyciu zmiennej środowiskowej (która będzie miała pierwszeństwo przed nazwą roli w chmurze określoną `APPLICATIONINSIGHTS_ROLE_NAME` w konfiguracji JSON).

## <a name="cloud-role-instance"></a>Wystąpienie roli w chmurze

Domyślnie wystąpienie roli w chmurze ma nazwę maszyny.

Jeśli chcesz ustawić wystąpienie roli w chmurze na coś innego, a nie nazwę komputera:

```json
{
  "role": {
    "name": "my cloud role name",
    "instance": "my cloud role instance"
  }
}
```

Możesz również ustawić wystąpienie roli w chmurze przy użyciu zmiennej środowiskowej (która będzie miała pierwszeństwo przed wystąpieniem roli w chmurze określonym `APPLICATIONINSIGHTS_ROLE_INSTANCE` w konfiguracji JSON).

## <a name="sampling"></a>Próbkowanie

Próbkowanie jest przydatne, jeśli chcesz obniżyć koszty.
Próbkowanie jest wykonywane jako funkcja na identyfikatorze operacji (nazywanym również identyfikatorem śledzenia), dzięki czemu ten sam identyfikator operacji zawsze będzie skutkować taką samą decyzją próbkowania. Dzięki temu nie będzie można pobrać części transakcji rozproszonej próbkowane w podczas gdy inne jego części są próbkowane.

Jeśli na przykład ustawisz próbkowanie na 10%, zobaczysz tylko 10% transakcji, ale każda z tych 10% będzie mieć pełne szczegóły transakcji.

Oto przykład sposobu ustawienia próbkowania w celu przechwycenia około **1/3** wszystkich transakcji — upewnij się, że ustawiono częstotliwość próbkowania poprawną dla Twojego przypadku użycia:

```json
{
  "sampling": {
    "percentage": 33.333
  }
}
```

Możesz również ustawić wartość procentową próbkowania przy użyciu zmiennej środowiskowej (która będzie miała pierwszeństwo przed procentem próbkowania określonym `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE` w konfiguracji JSON).

> [!NOTE]
> Dla wartości procentowej próbkowania wybierz wartość procentową, która jest bliska 100/N, gdzie N jest liczbą całkowitą. Obecnie próbkowanie nie obsługuje innych wartości.

## <a name="sampling-overrides-preview"></a>Przesłonięcia próbkowania (wersja zapoznawcza)

Ta funkcja jest dostępna w wersji zapoznawczej, począwszy od wersji 3.0.3.

Przesłonięcia próbkowania umożliwiają zastąpienie [domyślnego procentu próbkowania,](#sampling)na przykład:
* Ustaw wartość procentową próbkowania na 0 (lub niewielką wartość) dla hałaśliwych kontroli kondycji.
* Ustaw wartość procentową próbkowania na 0 (lub niewielką wartość) dla hałaśliwych wywołań zależności.
* Ustaw wartość procentową próbkowania na 100 dla ważnego typu żądania (np. ), nawet jeśli próbkowanie domyślne jest skonfigurowane `/login` na wartość niższą.

Aby uzyskać więcej informacji, zapoznaj się z dokumentacją [przesłonięcia próbkowania.](./java-standalone-sampling-overrides.md)

## <a name="jmx-metrics"></a>Metryki JMX

Jeśli chcesz zebrać dodatkowe metryki JMX:

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

`name` to nazwa metryki, która zostanie przypisana do tej metryki JMX (może to być wszystko).

`objectName` to [nazwa obiektu](https://docs.oracle.com/javase/8/docs/api/javax/management/ObjectName.html) JMX MBean, który chcesz zebrać.

`attribute` to nazwa atrybutu wewnątrz zestawu JMX MBean, który chcesz zebrać.

Obsługiwane są wartości liczbowe i logiczne metryk JMX. Metryki logiczne JMX są mapowane na wartość `0` false i `1` wartość true.

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

`${...}` Może służyć do odczytu wartości z określonej zmiennej środowiskowej podczas uruchamiania.

> [!NOTE]
> Począwszy od wersji 3.0.2, jeśli dodasz wymiar niestandardowy o nazwie , wartość będzie przechowywana w kolumnie w tabeli dzienników Application Insights, a nie jako wymiar `service.version` `application_Version` niestandardowy.

## <a name="telemetry-processors-preview"></a>Procesory telemetrii (wersja zapoznawcza)

Ta funkcja jest dostępna w wersji zapoznawczej.

Umożliwia konfigurowanie reguł, które będą stosowane do żądań, zależności i danych telemetrycznych śledzenia, na przykład:
 * Maskowanie poufnych danych
 * Warunkowe dodawanie wymiarów niestandardowych
 * Zaktualizuj nazwę zakresu, która jest używana do agregowania podobnych danych telemetrycznych w Azure Portal.
 * Porzuć określone atrybuty span, aby kontrolować koszty pozyskiwania.

Aby uzyskać więcej informacji, zapoznaj się z [dokumentacją procesora telemetrii.](./java-standalone-telemetry-processors.md)

> [!NOTE]
> Jeśli chcesz usunąć określone (całe) zakresy w celu kontrolowania kosztu pozyskiwania, zobacz [przesłonięcia próbkowania](./java-standalone-sampling-overrides.md).

## <a name="auto-collected-logging"></a>Automatyczne zbieranie rejestrowania

Log4j, Logback i java.util.logging są automatycznie instrumentowane, a rejestrowanie wykonywane za pośrednictwem tych platform rejestrowania jest zbierane automatycznie.

Rejestrowanie jest przechwytywane tylko wtedy, gdy najpierw spełnia poziom skonfigurowany dla struktury rejestrowania, a po drugie, również spełnia poziom skonfigurowany dla Application Insights.

Jeśli na przykład twoja platforma rejestrowania jest skonfigurowana do rejestrowania (i powyżej) z pakietu , Application Insights jest skonfigurowana do przechwytywania (i powyżej), program Application Insights przechwyci tylko `WARN` `com.example` `INFO` `WARN` (i powyżej) z pakietu `com.example` .

Domyślny poziom skonfigurowany dla Application Insights to `INFO` . Jeśli chcesz zmienić ten poziom:

```json
{
  "instrumentation": {
    "logging": {
      "level": "WARN"
    }
  }
}
```

Poziom można również ustawić przy użyciu zmiennej środowiskowej (która będzie miała pierwszeństwo przed poziomem określonym `APPLICATIONINSIGHTS_INSTRUMENTATION_LOGGING_LEVEL` w konfiguracji JSON).

Są to prawidłowe wartości, które można określić w pliku i jak odpowiadają one poziomom rejestrowania w `level` `applicationinsights.json` różnych platformach rejestrowania:

| poziom             | Log4j  | Logback (Logback) | LIP     |
|-------------------|--------|---------|---------|
| WYŁ.               | WYŁ.    | WYŁ.     | WYŁ.     |
| Śmiertelne             | Śmiertelne  | BŁĄD   | Ciężkie  |
| BŁĄD (lub POWAŻNY) | BŁĄD  | BŁĄD   | Ciężkie  |
| OSTRZEGAJ (lub OSTRZEŻENIE) | Ostrzec   | Ostrzec    | OSTRZEŻENIE |
| Informacji              | Informacji   | Informacji    | Informacji    |
| Config            | DEBUGUJ  | DEBUGUJ   | Config  |
| DEBUG (lub FINE)   | DEBUGUJ  | DEBUGUJ   | Dobrze    |
| Drobniejsze             | DEBUGUJ  | DEBUGUJ   | Drobniejsze   |
| TRACE (LUB ŚLEDZENIA) | TRACE  | TRACE   | Najlepszych  |
| ALL               | ALL    | ALL     | ALL     |

> [!NOTE]
> Jeśli obiekt wyjątku zostanie przekazany do rejestratora, komunikat dziennika (i szczegóły obiektu wyjątku) będzie wyświetlany w Azure Portal tabeli, a nie `exceptions` `traces` tabeli.

## <a name="auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics"></a>Automatycznie zbierane metryki mikrometrów (w tym metryki Spring Boot metryki metryk)

Jeśli aplikacja używa [parametru Micrometer,](https://micrometer.io)metryki, które są wysyłane do rejestru globalnego Micrometer, są zbierane automatycznie.

Ponadto jeśli aplikacja używa [parametru Spring Boot,](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html)metryki skonfigurowane za pomocą Spring Boot szybki są również zbierane automatycznie.

Aby wyłączyć automatyczne zbieranie metryk mikrometrycznych (w tym metryk Spring Boot metryki metryk:

> [!NOTE]
> Metryki niestandardowe są rozliczane oddzielnie i mogą generować dodatkowe koszty. Sprawdź szczegółowe informacje [o cenach.](https://azure.microsoft.com/pricing/details/monitor/) Aby wyłączyć metryki Micrometer i Spring Metric, dodaj do pliku konfiguracji poniżej konfigurację.

```json
{
  "instrumentation": {
    "micrometer": {
      "enabled": false
    }
  }
}
```

## <a name="auto-collected-azure-sdk-telemetry-preview"></a>Automatycznie zbierane dane telemetryczne zestawu Azure SDK (wersja zapoznawcza)

Wiele najnowszych bibliotek zestawu Azure SDK emituje dane telemetryczne (zobacz [pełną listę](./java-in-process-agent.md#azure-sdks-preview)).

Od wersji Application Insights Java 3.0.3 można włączyć przechwytywanie tej telemetrii.

Jeśli chcesz włączyć tę funkcję:

```json
{
  "preview": {
    "instrumentation": {
      "azureSdk": {
        "enabled": true
      }
    }
  }
}
```

Tę funkcję można również włączyć przy użyciu zmiennej środowiskowej `APPLICATIONINSIGHTS_PREVIEW_INSTRUMENTATION_AZURE_SDK_ENABLED`
(która będzie miała pierwszeństwo przed włączoną określoną w konfiguracji JSON).

## <a name="suppressing-specific-auto-collected-telemetry"></a>Pomijanie określonych automatycznie zbieranych danych telemetrycznych

Począwszy od wersji 3.0.3, określone automatycznie zbierane dane telemetryczne mogą być pomijane przy użyciu tych opcji konfiguracji:

```json
{
  "instrumentation": {
    "cassandra": {
      "enabled": false
    },
    "jdbc": {
      "enabled": false
    },
    "jms": {
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
    },
    "springScheduling": {
      "enabled": false
    }
  }
}
```

Możesz również pominąć te instrumentacje przy użyciu tych zmiennych środowiskowych:

* `APPLICATIONINSIGHTS_INSTRUMENTATION_CASSANDRA_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_JDBC_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_JMS_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_KAFKA_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_MICROMETER_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_MONGO_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_REDIS_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_SPRING_SCHEDULING_ENABLED`

(która będzie miała pierwszeństwo przed włączoną określoną w konfiguracji JSON).

> UWAGA Jeśli szukasz bardziej szczegółowej kontroli, np. w celu pominięcia niektórych wywołań redis, ale nie wszystkich wywołań redis, zobacz przesłonięcia [próbkowania](./java-standalone-sampling-overrides.md).

## <a name="heartbeat"></a>Puls

Domyślnie program Application Insights Java 3.0 wysyła metrykę pulsu co 15 minut. Jeśli używasz metryki pulsu do wyzwalania alertów, możesz zwiększyć częstotliwość tego pulsu:

```json
{
  "heartbeat": {
    "intervalSeconds": 60
  }
}
```

> [!NOTE]
> Nie można zwiększyć interwału do dłuższego niż 15 minut, ponieważ dane pulsu są również używane do śledzenia Application Insights danych.

## <a name="http-proxy"></a>HTTP Proxy

Jeśli aplikacja znajduje się za zaporą i nie może połączyć się bezpośrednio z usługą Application Insights (zobacz Adresy IP używane przez usługę [Application Insights),](./ip-addresses.md)możesz skonfigurować środowisko Application Insights Java 3.0 do używania serwera proxy HTTP:

```json
{
  "proxy": {
    "host": "myproxy",
    "port": 8080
  }
}
```

Application Insights Java 3.0 również respektuje globalne `-Dhttps.proxyHost` i jeśli są one `-Dhttps.proxyPort` ustawione.

## <a name="metric-interval"></a>Interwał metryki

Ta funkcja jest dostępna w wersji zapoznawczej.

Domyślnie metryki są przechwytywane co 60 sekund.

Począwszy od wersji 3.0.3, można zmienić ten interwał:

```json
{
  "preview": {
    "metricIntervalSeconds": 300
  }
}
```

To ustawienie dotyczy wszystkich tych metryk:

* Domyślne liczniki wydajności, np. procesor i pamięć
* Domyślne metryki niestandardowe, np. chronometraż wyrzucania elementów bezużytecznych
* Skonfigurowane metryki JMX[(patrz powyżej](#jmx-metrics))
* Metryki mikrometry[(patrz powyżej](#auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics))


[//]: # "UWAGA Obsługa usługi OpenTelemetry jest w prywatnej wersji zapoznawczej, dopóki interfejs API OpenTelemetry nie osiągnie wersji 1.0"

[//]: # "Obsługa ## wersji interfejsu API OpenTelemetry w wersjach 1.0"

[//]: # "Obsługa interfejsu API OpenTelemetry w wersjach wcześniejszych niż 1.0 jest opt-in, ponieważ interfejs API OpenTelemetry nie jest jeszcze stabilny"
[//]: # "i dlatego każda wersja agenta obsługuje tylko określone wersje interfejsu API OpenTelemetry starsze niż 1.0"
[//]: # "(to ograniczenie nie będzie stosowane po zwolnieniu interfejsu API OpenTelemetry 1.0)."

[//]: # "'''json"
[//]: # "{"
[//]: # "  \"wersja \" zapoznawcza: {"
[//]: # "    \"openTelemetryApiSupport \" : true"
[//]: # "  }"
[//]: # "}"
[//]: # "```"

## <a name="self-diagnostics"></a>Samodzielna diagnostyka

"Samodzielna diagnostyka" odnosi się do rejestrowania wewnętrznego z Application Insights Java 3.0.

Ta funkcja może być przydatna do wyszukiwania i diagnozowania problemów z Application Insights się.

Domyślnie dzienniki Application Insights Java 3.0 na poziomie pliku i konsoli, co `INFO` `applicationinsights.log` odpowiada tej konfiguracji:

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

`destination` Może to być jeden z `file` , `console` lub `file+console` .

`level` może być jednym z `OFF` , , , , , lub `ERROR` `WARN` `INFO` `DEBUG` `TRACE` .

`path` może być ścieżką bezwzględną lub względną. Ścieżki względne są rozpoznawane względem katalogu, w `applicationinsights-agent-3.0.3.jar` którym się znajduje.

`maxSizeMb` to maksymalny rozmiar pliku dziennika przed jego przewęchem.

`maxHistory` to liczba przewróconych plików dziennika, które są zachowywane (oprócz bieżącego pliku dziennika).

Począwszy od wersji 3.0.2, można również ustawić samodzielną diagnostykę przy użyciu zmiennej środowiskowej (która będzie miała pierwszeństwo przed poziomem samodzielnej diagnostyki określonym w konfiguracji `level` `APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL` JSON).

## <a name="an-example"></a>Przykład

Jest to tylko przykład, aby pokazać, jak wygląda plik konfiguracji z wieloma składnikami.
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
