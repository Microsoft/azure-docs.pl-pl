---
title: Zbieranie Spring Cloud zbierania metryk wyłącznika Resilience4J za pomocą mikrometrów
description: Jak zbierać metryki Spring Cloud Resilience4J wyłącznika za pomocą mikrometrów w Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: fedebd9182c168b9b7c455d5f6726e66720e0a8b
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479163"
---
# <a name="collect-spring-cloud-resilience4j-circuit-breaker-metrics-with-micrometer-preview"></a>Zbierz Spring Cloud resilience4J wyłącznika za pomocą mikrometrów (wersja zapoznawcza)

W tym dokumencie wyjaśniono, jak Spring Cloud metryk wyłącznika Resilience4j za pomocą Application Insights java in-process agent. Ta funkcja umożliwia monitorowanie metryk wyłącznika resilience4j z Application Insights micrometer.

Aby pokazać, jak działa, używamy pokazu [spring-cloud-circuit-breaker-demo.](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo)

## <a name="prerequisites"></a>Wymagania wstępne

* Włącz agenta In-Process Java w [przewodniku Java In-Process Agent for Application Insights .](./spring-cloud-howto-application-insights.md#enable-java-in-process-agent-for-application-insights) 

* Włącz zbieranie wymiarów dla metryk resilience4j z Application Insights [.](../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation)

* Zainstaluj narzędzia git, Maven i Java, jeśli nie są jeszcze one w użyciu przez komputer dewelopera.

## <a name="build-and-deploy-apps"></a>Kompilowanie i wdrażanie aplikacji

Następująca procedura służy do kompilowania i wdrażania aplikacji.

1. Sklonuj i skompilowaj repozytorium demonstracyjne.

```bash
git clone https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo.git
cd spring-cloud-circuitbreaker-demo && mvn clean package -DskipTests
```

2. Tworzenie aplikacji z punktami końcowymi

```azurecli
az spring-cloud app create --name resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
az spring-cloud app create --name reactive-resilience4j --assign-endpoint \
    -s ${asc-service-name} -g ${asc-resource-group}
```

3. Wdrażanie aplikacji.

```azurecli
az spring-cloud app deploy -n resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-resilience4j/target/spring-cloud-circuitbreaker-demo-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
az spring-cloud app deploy -n reactive-resilience4j \
    --jar-path ./spring-cloud-circuitbreaker-demo-reactive-resilience4j/target/spring-cloud-circuitbreaker-demo-reactive-resilience4j-0.0.1.BUILD-SNAPSHOT.jar \
    -s ${service_name} -g ${resource_group}
```

> [!Note]
>
> * Uwzględnij wymaganą zależność dla resilience4j:
>
>   ```xml
>   <dependency>
>       <groupId>io.github.resilience4j</groupId>
>       <artifactId>resilience4j-micrometer</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-circuitbreaker-resilience4j</artifactId>
>   </dependency>
>   ```
> * Kod klienta musi używać interfejsu API programu , który jest implementowane jako automatycznie tworzony po dojechanie `CircuitBreakerFactory` `bean` początkowego Spring Cloud wyłącznika. Aby uzyskać szczegółowe [informacje, Spring Cloud Wyłącznik](https://spring.io/projects/spring-cloud-circuitbreaker#overview).
>
> * Następujące 2 zależności mają konflikty z pakietami resilient4j powyżej.  Upewnij się, że klient ich nie zawiera.
>
>   ```xml
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-sleuth</artifactId>
>   </dependency>
>   <dependency>
>       <groupId>org.springframework.cloud</groupId>
>       <artifactId>spring-cloud-starter-zipkin</artifactId>
>   </dependency>
>   ```
>
>
> Przejdź do adresu URL dostarczonego przez aplikacje bramy i uzyskaj dostęp do punktu końcowego z [witryny spring-cloud-circuit-breaker-demo](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) w następujący sposób:
>
>   ```console
>   /get
>   /get/delay/{seconds}
>   /get/fluxdelay/{seconds}
>   ```

## <a name="locate-resilence4j-metrics-from-portal"></a>Zlokalizuj metryki Resilence4j w portalu

1. Wybierz blok **Application Insights** w Azure Spring Cloud portal, a następnie kliknij **pozycję Application Insights**.

   [![resilience4J 0](media/spring-cloud-resilience4j/resilience4J-0.png)](media/spring-cloud-resilience4j/resilience4J-0.PNG)

2. Wybierz **pozycję Metryki** na **Application Insights** strony.  Wybierz **pozycję azure.applicationinsights w** obszarze Przestrzeń nazw **metryk.**  Wybierz również **metryki resilience4j_circuitbreaker_buffered_calls** za pomocą opcji **Średnia.**

   [![resilience4J 1](media/spring-cloud-resilience4j/resilience4J-1.png)](media/spring-cloud-resilience4j/resilience4J-1.PNG)

3. Wybierz **resilience4j_circuitbreaker_calls** metryki i pozycję **Średnia.**

   [![resilience4J 2](media/spring-cloud-resilience4j/resilience4J-2.png)](media/spring-cloud-resilience4j/resilience4J-2.PNG)

4. Wybierz **resilience4j_circuitbreaker_calls** metryki i pozycję **Średnia.**  Kliknij **pozycję Dodaj filtr,** a następnie wybierz pozycję nazwa **jako createNewAccount.**

   [![resilience4J 3](media/spring-cloud-resilience4j/resilience4J-3.png)](media/spring-cloud-resilience4j/resilience4J-3.PNG)

5. Wybierz **resilience4j_circuitbreaker_calls** metryki i pozycję **Średnia.**  Następnie kliknij **pozycję Zastosuj podział** i wybierz **rodzaj**.

   [![resilience4J 4](media/spring-cloud-resilience4j/resilience4J-4.png)](media/spring-cloud-resilience4j/resilience4J-4.PNG)

6. Wybierz **resilience4j_circuitbreaker_calls**, "**resilience4j_circuitbreaker_buffered_calls** i resilience4j_circuitbreaker_slow_calls **metryki** za pomocą opcji **Średnia.**

   [![resilience4J 5](media/spring-cloud-resilience4j/resilience4j-5.png)](media/spring-cloud-resilience4j/resilience4j-5.PNG)

## <a name="see-also"></a>Zobacz też

* [Application Insights](spring-cloud-howto-application-insights.md)
* [Rozproszone śledzenie](spring-cloud-howto-distributed-tracing.md)
* [Pulpit nawigacyjny wyłącznika](spring-cloud-tutorial-circuit-breaker.md)
