---
title: Dziennik aplikacji ze strukturą dla chmury wiosennej platformy Azure | Microsoft Docs
description: W tym artykule wyjaśniono, jak generować i zbierać dane z strukturalnych dzienników aplikacji w chmurze Azure wiosennej.
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 844530c6d1650b5fddd27b10c775c4364a3f5147
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102124469"
---
# <a name="structured-application-log-for-azure-spring-cloud"></a>Dziennik aplikacji ze strukturą dla chmury wiosennej platformy Azure

W tym artykule wyjaśniono, jak generować i zbierać dane z strukturalnych dzienników aplikacji w chmurze Azure wiosennej. Dzięki prawidłowej konfiguracji chmura sprężynowa platformy Azure zapewnia przydatne wykonywanie zapytań i analiz dzienników aplikacji za pomocą Log Analytics.

## <a name="log-schema-requirements"></a>Wymagania schematu dziennika
Aby usprawnić pracę z zapytaniem dziennika, dziennik aplikacji musi być w formacie JSON i być zgodny ze schematem. Chmura sprężynowa platformy Azure używa tego schematu do analizowania aplikacji i strumienia do Log Analytics. 

**Wymagania schematu JSON:**

| Klucz JSON      | Typ wartości JSON|  Wymagane | Kolumna w Log Analytics| Opis |
| --------------| ------------|-----------|-----------------|--------------------------|
| sygnatura czasowa     | ciąg      |     Tak   | AppTimestamp    | Sygnatura czasowa w formacie UTC  |
| rejestratora        | ciąg      |     Nie    | Rejestratora          | rejestratora                   |
| poziom         | ciąg      |     Nie    | CustomLevel     | poziom dziennika                |
| wątek        | ciąg      |     Nie    | Thread          | wątek                   |
| message       | ciąg      |     Nie    | Komunikat         | komunikat dziennika              |
| Ślad stosu    | ciąg      |     Nie    | Ślad stosu      | ślad stosu wyjątków    |
| exceptionClass| ciąg      |     Nie    | ExceptionClass  | Nazwa klasy wyjątku     |
| MDC           | zagnieżdżony kod JSON |     Nie    |                 | mapowany kontekst diagnostyczny|
| MDC. traceId   | ciąg      |     Nie    | TraceId         |Identyfikator śledzenia dla śledzenia rozproszonego|
| MDC. spanId    | ciąg      |     Nie    | SpanId          |Identyfikator zakresu śledzenia rozproszonego |
|               |             |           |                 |                          |

* Pole "timestamp" jest wymagane i musi być w formacie UTC, wszystkie inne pola są opcjonalne.
* "traceId" i "spanId" w polu "MDC" są używane na potrzeby śledzenia.
* Rejestruj każdy rekord JSON w jednym wierszu. 

**Przykład rekordu dziennika** 
 ```
{"timestamp":"2021-01-08T09:23:51.280Z","logger":"com.example.demo.HelloController","level":"ERROR","thread":"http-nio-1456-exec-4","mdc":{"traceId":"c84f8a897041f634","spanId":"c84f8a897041f634"},"stackTrace":"java.lang.RuntimeException: get an exception\r\n\tat com.example.demo.HelloController.throwEx(HelloController.java:54)\r\n\","message":"Got an exception","exceptionClass":"RuntimeException"}
```

## <a name="generate-schema-compliant-json-log"></a>Generuj dziennik JSON zgodny ze schematem  
W przypadku aplikacji wiosennych można wygenerować oczekiwany format dziennika JSON przy użyciu wspólnych [platform rejestrowania](https://docs.spring.io/spring-boot/docs/2.1.13.RELEASE/reference/html/boot-features-logging.html#boot-features-custom-log-configuration), takich jak [logback](http://logback.qos.ch/) i [log4j2](https://logging.apache.org/log4j/2.x/). 

### <a name="log-with-logback"></a>Rejestruj przy użyciu logback 
W przypadku korzystania z funkcji sprężynowego uruchamiania, domyślnie jest używana logback. W przypadku aplikacji logback Użyj [logstash-Encoder](https://github.com/logstash/logstash-logback-encoder) do generowania dziennika sformatowanego JSON. Ta metoda jest obsługiwana w przypadku rozruchu sprężynowego w wersji 2.1 +. 

Procedura:

1. Dodaj zależność logstash do pliku pom.xml. 

    ```json
    <dependency>
        <groupId>net.logstash.logback</groupId>
        <artifactId>logstash-logback-encoder</artifactId>
        <version>6.5</version>
    </dependency>
    ```
1. Zaktualizuj plik konfiguracji logback.xml, aby ustawić format JSON.
    ```json
    <configuration>
        <appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
            <encoder class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder">
            <providers>
                <timestamp>
                    <fieldName>timestamp</fieldName>
                    <timeZone>UTC</timeZone>
                </timestamp>
                <loggerName>
                    <fieldName>logger</fieldName>
                </loggerName>
                <logLevel>
                    <fieldName>level</fieldName>
                </logLevel>
                <threadName>
                    <fieldName>thread</fieldName>
                </threadName>
                <nestedField>
                    <fieldName>mdc</fieldName>
                    <providers>
                        <mdc/>
                    </providers>
                </nestedField>
                <stackTrace>
                    <fieldName>stackTrace</fieldName>
                </stackTrace>
                <message/>
                <throwableClassName>
                    <fieldName>exceptionClass</fieldName>
                </throwableClassName>
            </providers>
            </encoder>
        </appender>
        <root level="info">
            <appender-ref ref="stdout"/>
        </root>
    </configuration>
    ```

### <a name="log-with-log4j2"></a>Rejestruj przy użyciu log4j2 

W przypadku aplikacji log4j2 Użyj [szablonu JSON-Template-layout](https://logging.apache.org/log4j/2.x/manual/json-template-layout.html) do generowania dziennika sformatowanego JSON. Ta metoda jest obsługiwana w przypadku rozruchu sprężynowego w wersji 2.1 +.

Procedura:

1. Wyklucz `spring-boot-starter-logging` z `spring-boot-starter` , Dodaj `spring-boot-starter-log4j2` zależności `log4j-layout-template-json` w pliku pom.xml.

    ```xml
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
            <exclusions>
                    <exclusion>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-starter-logging</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-log4j2</artifactId>
        </dependency>
        <dependency>
            <groupId>org.apache.logging.log4j</groupId>
            <artifactId>log4j-layout-template-json</artifactId>
            <version>2.14.0</version>
        </dependency>
    ```

2. Przygotuj plik szablonu układu JSON jsonTemplate.jsna ścieżce klasy.

    ```json
        {
            "mdc": {
                "$resolver": "mdc"
            },
            "exceptionClass": {
                "$resolver": "exception",
                "field": "className"
            },
            "stackTrace": {
                "$resolver": "exception",
                "field": "stackTrace",
                "stringified": true
            },
            "message": {
                "$resolver": "message",
                "stringified": true
            },
            "thread": {
                "$resolver": "thread",
                "field": "name"
            },
            "timestamp": {
                "$resolver": "timestamp",
                "pattern": {
                    "format": "yyyy-MM-dd'T'HH:mm:ss.SSS'Z'",
                    "timeZone": "UTC"
                }
            },
            "level": {
                "$resolver": "level",
                "field": "name"
            },
            "logger": {
                "$resolver": "logger",
                "field": "name"
            }
        }
    ```

3. Użyj tego szablonu układu JSON w pliku konfiguracji log4j2.xml. 

    ```json
        <configuration>
            <appenders>
                <console name="Console" target="SYSTEM_OUT">
                <JsonTemplateLayout eventTemplateUri="classpath:jsonTemplate.json"/>
                </console>
            </appenders>
            <loggers>
                <root level="info">
                <appender-ref ref="Console"/>
                </root>
            </loggers>
        </configuration>
    ```

## <a name="analyze-the-logs-in-log-analytics"></a>Analizowanie dzienników w Log Analytics

Po poprawnym skonfigurowaniu aplikacji Dziennik konsoli aplikacji zostanie przesłany strumieniowo do Log Analytics. Struktura umożliwia wydajne wykonywanie zapytań w Log Analytics.

### <a name="check-log-structure-in-log-analytics"></a>Sprawdź strukturę dzienników w Log Analytics
Postępuj zgodnie z następującą procedurą:
1. Przejdź do strony Przegląd usługi w wystąpieniu usługi.
2. Kliknij pozycję `Logs` wpis w `Monitoring` sekcji.
3. Uruchom to zapytanie.

   ```
   AppPlatformLogsforSpring
   | where TimeGenerated > ago(1h)
   | project AppTimestamp, Logger, CustomLevel, Thread, Message, ExceptionClass, StackTrace, TraceId, SpanId
   ```

4. Dzienniki aplikacji zwracają jak pokazano na poniższej ilustracji:

![Pokaż dziennik JSON](media/spring-cloud-structured-app-log/json-log-query.png)

### <a name="show-log-entries-containing-errors"></a>Pokaż wpisy dziennika zawierające błędy

Aby przejrzeć wpisy dziennika z błędami, uruchom następujące zapytanie:

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h) and CustomLevel == "ERROR" 
| project AppTimestamp, Logger, ExceptionClass, StackTrace, Message, AppName 
| sort by AppTimestamp
```

Użyj tego zapytania, aby znaleźć błędy lub zmodyfikować terminy zapytania, aby znaleźć konkretną klasę wyjątku lub kod błędu. 

### <a name="show-log-entries-for-a-specific-traceid"></a>Pokaż wpisy dziennika dla określonego traceId
Aby przejrzeć wpisy dziennika dla określonego identyfikatora śledzenia "trace_id", uruchom następujące zapytanie:

```
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where TraceId == "trace_id" 
| project AppTimestamp, Logger, TraceId, SpanId, StackTrace, Message, AppName 
| sort by AppTimestamp
```

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej na temat zapytania dziennika, zobacz [Rozpoczynanie pracy z kwerendami dzienników w Azure monitor](../azure-monitor/logs/get-started-queries.md)