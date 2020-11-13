---
title: Jak używać Micrometer z usługą Azure Application Insights Java SDK
description: Przewodnik krok po kroku dotyczący używania Micrometer Application Insights z rozruchem wiosny i niesprężynowymi aplikacjami rozruchowymi.
ms.topic: conceptual
author: lgayhardt
ms.custom: devx-track-java
ms.author: lagayhar
ms.date: 11/01/2018
ms.openlocfilehash: 30bd45cee962395ad6c6b87e393ea0f1b7329908
ms.sourcegitcommit: 9706bee6962f673f14c2dc9366fde59012549649
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94616845"
---
# <a name="how-to-use-micrometer-with-azure-application-insights-java-sdk-not-recommended"></a>Jak używać Micrometer z usługą Azure Application Insights Java SDK (niezalecane)

> [!IMPORTANT]
> Zalecanym podejściem do monitorowania aplikacji Java jest użycie autoinstrumentacji bez zmiany kodu. Funkcja telemetrii Micrometer jest zbierana z Application Insightsem programu Java 3,0 Agent — postępuj zgodnie z wytycznymi dla [Application Insights programu java 3,0 Agent](./java-in-process-agent.md).

> [!NOTE]
> Application Insights SDK języka Java nie obsługuje wiosny webstrumień — zamiast tego użyj [Application Insights Java 3,0 Agent](./java-in-process-agent.md) . 
>
> Zarówno webstrumień, jak i Micrometer są obsługiwane w programie [Application Insights Java 3,0 Agent](./java-on-premises.md) , który nie wymaga Instrumentacji. 

Micrometer do monitorowania aplikacji miaruje metryki dla kodu aplikacji opartego na JVM i umożliwia eksportowanie danych do ulubionych systemów monitorowania. W tym artykule opisano, jak używać Micrometer z Application Insights zarówno dla aplikacji rozruchowych z systemem sprężyny, jak i bez sprężyny.

## <a name="using-spring-boot-15x"></a>Korzystanie z usługi sprężynowego rozruchu 1,5 x
Dodaj następujące zależności do pliku pom.xml lub Build. Gradle: 
* [Application Insights sprężyny rozruchu — Starter](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter) 2.5.0 lub nowszego
* Micrometer Azure Registry 1.1.0 lub nowszy
* [Micrometer sprężyny ze starszą](https://micrometer.io/docs/ref/spring/1.5) 1.1.0 lub wyższą (to spowoduje Przeportowanie kodu autokonfiguracji w strukturze sprężynowej).
* [Zasób ApplicationInsights](./create-new-resource.md)

Kroki

1. Zaktualizuj plik pom.xml aplikacji do rozruchu ze sprężyną i Dodaj do niej następujące zależności:

    ```XML
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-spring-boot-starter</artifactId>
        <version>2.5.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-spring-legacy</artifactId>
        <version>1.1.0</version>
    </dependency>

    <dependency>
        <groupId>io.micrometer</groupId>
        <artifactId>micrometer-registry-azure-monitor</artifactId>
        <version>1.1.0</version>
    </dependency>

    ```
2. Zaktualizuj plik Application. Properties lub YML za pomocą klucza Instrumentacji Application Insights przy użyciu następującej właściwości:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
1. Kompilowanie aplikacji i uruchamianie jej
2. Powyższe funkcje powinny pomóc Ci w rozpoczęciu pracy z wstępnie zagregowanymi metrykami, które są zbierane do Azure Monitor. Szczegółowe informacje na temat dostosowywania Application Insights sprężynowego rozruchu Starter można znaleźć w [pliku Readme w witrynie GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/spring/azure-spring-boot-starter/README.md).

## <a name="using-spring-2x"></a>Korzystanie z sprężyny 2. x

Dodaj następujące zależności do pliku pom.xml lub Build. Gradle:

* Application Insights sprężynowego rozruchu — Starter 2.1.2 lub nowszego
* Azure-sprężynowe-Boot-Metrics-restarters 2.0.7 lub nowszy
* [Zasób Application Insights](./create-new-resource.md)

Kroki:

1. Zaktualizuj plik pom.xml aplikacji rozruchowej wiosennej i Dodaj do niej następującą zależność:

    ```XML
    <dependency> 
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-spring-boot-metrics-starter</artifactId>
          <version>2.0.7</version>
    </dependency>
    ```
1. Zaktualizuj plik Application. Properties lub YML za pomocą klucza Instrumentacji Application Insights przy użyciu następującej właściwości:

     `azure.application-insights.instrumentation-key=<your-instrumentation-key-here>`
3. Kompilowanie aplikacji i uruchamianie jej
4. Powyższe funkcje powinny być wykonywane z użyciem wstępnie zagregowanych metryk, które są zbierane do Azure Monitor. Szczegółowe informacje na temat dostosowywania Application Insights sprężynowego rozruchu Starter można znaleźć w [pliku Readme w witrynie GitHub](https://github.com/Microsoft/azure-spring-boot/releases/latest).

Metryki domyślne:

*    Automatycznie skonfigurowane metryki dla metryk Tomcat, JVM, Logback, metryk Log4J, metryk czasowych, metryk procesora, FileDescriptorMetrics.
*    Na przykład jeśli Netflix Hystrix jest obecny w ścieżce klasy, będziemy również otrzymywać te metryki. 
*    Następujące metryki mogą być dostępne poprzez dodanie odpowiednich ziaren. 
        - CacheMetrics (CaffeineCache, EhCache2, GuavaCache, HazelcastCache, JCache)     
        - DataBaseTableMetrics 
        - HibernateMetrics 
        - JettyMetrics 
        - Metryki OkHttp3 
        - Metryki Kafka 

 

Jak wyłączyć automatyczne zbieranie metryk: 
 
- Metryki JVM: 
    - Management. Metrics. Binders. JVM. Enabled = false 
- Metryki Logback: 
    - Management. Metrics. Binders. logback. Enabled = false
- Metryki czasu przestoju: 
    - Management. Metrics. Binders. czas działania. Enabled = false 
- Metryki procesora:
    -  Management. Metrics. Binders. Processor. Enabled = false 
- FileDescriptorMetrics:
    - Management. Metrics. Binders. Files. Enabled = false 
- Hystrix metryki w przypadku biblioteki na ścieżce klasy: 
    - Management. Metrics. Binders. Hystrix. Enabled = false 
- AspectJ metryki w przypadku biblioteki na ścieżce klasy: 
    - wiosenn. AOP. Enabled = false 

> [!NOTE]
> Określ powyższe właściwości w pliku Application. Properties lub Application. yml aplikacji do rozruchowego sprężyny

## <a name="use-micrometer-with-non-spring-boot-web-applications"></a>Korzystanie z Micrometer z niesprężynnymi aplikacjami sieci Web

Dodaj następujące zależności do pliku pom.xml lub Build. Gradle:

* Application Insights Web 2.5.0 lub nowsza
* Micrometer Azure Registry 1.1.0 lub nowszy
* [Zasób Application Insights](./create-new-resource.md)

Kroki:

1. Dodaj następujące zależności w pliku pom.xml lub Build. Gradle:

    ```XML
        <dependency>
            <groupId>io.micrometer</groupId>
            <artifactId>micrometer-registry-azure-monitor</artifactId>
            <version>1.1.0</version>
        </dependency>
        
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>applicationinsights-web-auto</artifactId>
            <version>2.5.0</version>
        </dependency>
     ```

2. Umieść `ApplicationInsights.xml` plik w folderze Resources:

    ```XML
    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">
    
       <!-- The key from the portal: -->
       <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>
    
       <!-- HTTP request component (not required for bare API) -->
       <TelemetryModules>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
       </TelemetryModules>
    
       <!-- Events correlation (not required for bare API) -->
       <!-- These initializers add context data to each event -->
       <TelemetryInitializers>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
          <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
       </TelemetryInitializers>
    
    </ApplicationInsights>
    ```

3. Przykładowa Klasa serwletu (emituje metrykę czasomierza):

    ```Java
        @WebServlet("/hello")
        public class TimedDemo extends HttpServlet {
    
          private static final long serialVersionUID = -4751096228274971485L;
    
          @Override
          @Timed(value = "hello.world")
          protected void doGet(HttpServletRequest request, HttpServletResponse response)
              throws ServletException, IOException {
    
            response.getWriter().println("Hello World!");
            MeterRegistry registry = (MeterRegistry) getServletContext().getAttribute("AzureMonitorMeterRegistry");
    
        //create new Timer metric
            Timer sampleTimer = registry.timer("timer");
            Stream<Integer> infiniteStream = Stream.iterate(0, i -> i+1);
            infiniteStream.limit(10).forEach(integer -> {
              try {
                Thread.sleep(1000);
                sampleTimer.record(integer, TimeUnit.MILLISECONDS);
              } catch (Exception e) {}
               });
          }
          @Override
          public void init() throws ServletException {
            System.out.println("Servlet " + this.getServletName() + " has started");
          }
          @Override
          public void destroy() {
            System.out.println("Servlet " + this.getServletName() + " has stopped");
          }
    
        }
    
    ```

4. Przykładowa Klasa konfiguracji:

    ```Java
         @WebListener
         public class MeterRegistryConfiguration implements ServletContextListener {
     
           @Override
           public void contextInitialized(ServletContextEvent servletContextEvent) {
     
         // Create AzureMonitorMeterRegistry
           private final AzureMonitorConfig config = new AzureMonitorConfig() {
             @Override
             public String get(String key) {
                 return null;
             }
            @Override
               public Duration step() {
                 return Duration.ofSeconds(60);}
     
             @Override
             public boolean enabled() {
                 return false;
             }
         };
     
      MeterRegistry azureMeterRegistry = AzureMonitorMeterRegistry.builder(config);
     
             //set the config to be used elsewhere
             servletContextEvent.getServletContext().setAttribute("AzureMonitorMeterRegistry", azureMeterRegistry);
     
           }
     
           @Override
           public void contextDestroyed(ServletContextEvent servletContextEvent) {
     
           }
         }
    ```

Aby dowiedzieć się więcej o metrykach, zapoznaj się z [dokumentacją Micrometer](https://micrometer.io/docs/).

Inny przykładowy kod dotyczący tworzenia różnych typów metryk można znaleźć w[oficjalnym repozytorium GitHub Micrometer](https://github.com/micrometer-metrics/micrometer/tree/master/samples/micrometer-samples-core/src/main/java/io/micrometer/core/samples).

## <a name="how-to-bind-additional-metrics-collection"></a>Jak powiązać dodatkową kolekcję metryk

### <a name="springbootspring"></a>SpringBoot/Sprężyna

Utwórz element ziarna odpowiedniej kategorii metryk. Załóżmy na przykład, że potrzebujemy metryk pamięci podręcznej Guava:

```Java
    @Bean
    GuavaCacheMetrics guavaCacheMetrics() {
        Return new GuavaCacheMetrics();
    }
```
Istnieje kilka metryk, które nie są domyślnie włączone, ale można je powiązać w powyższy sposób. Pełną listę można znaleźć w [oficjalnym repozytorium usługi GitHub Micrometer](https://github.com/micrometer-metrics/micrometer/tree/master/micrometer-core/src/main/java/io/micrometer/core/instrument/binder ).

### <a name="non-spring-apps"></a>Aplikacje niesprężynowe
Dodaj następujący kod powiązania do pliku konfiguracji:
```Java 
    New GuavaCacheMetrics().bind(registry);
```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat Micrometer, zapoznaj się z oficjalną [dokumentacją Micrometer](https://micrometer.io/docs).
* Aby uzyskać informacje na temat sprężyny na platformie Azure, zapoznaj się z oficjalną [wiosną w dokumentacji platformy Azure](/java/azure/spring-framework/?view=azure-java-stable).