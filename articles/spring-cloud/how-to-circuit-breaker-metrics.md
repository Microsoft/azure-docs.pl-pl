---
title: Zbieraj metryki wyłącznika Resilience4J chmury z chmurą Micrometer
description: Jak zbierać metryki wyłącznika usługi Cloud Resilience4J z chmurą Micrometer w chmurze wiosennej platformy Azure.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: devx-track-java
ms.openlocfilehash: 0b24e8e07b4038d6def9945b7c347bb81ae5378b
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107258184"
---
# <a name="collect-spring-cloud-resilience4j-circuit-breaker-metrics-with-micrometer-preview"></a>Zbieraj metryki wyłącznika Resilience4J chmury z chmurą z Micrometer (wersja zapoznawcza)

W tym dokumencie wyjaśniono, jak zbierać metryki wyłącznika usługi Cloud Resilience4j Application Insights z chmurą w języku Java. Za pomocą tej funkcji można monitorować metryki wyłącznika usługi resilience4j z Application Insights z Micrometer.

Aby zobaczyć, jak działa, korzystamy z [demonstracji obwodowej z chmurą](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) .

## <a name="prerequisites"></a>Wymagania wstępne

* Włącz agenta In-Process Java z poziomu [agenta In-Process Java dla przewodnika Application Insights](./spring-cloud-howto-application-insights.md#enable-java-in-process-agent-for-application-insights). 

* Włącz zbieranie wymiarów dla metryk resilience4j z poziomu [przewodnika Application Insights](../azure-monitor/app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation).

* Zainstaluj narzędzia Git, Maven i Java, jeśli nie są jeszcze używane przez komputer deweloperski.

## <a name="build-and-deploy-apps"></a>Kompilowanie i wdrażanie aplikacji

Poniższa procedura umożliwia kompilację i wdrożenie aplikacji.

1. Klonowanie i kompilowanie repozytorium demonstracyjnego.

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

3. Wdrażaj aplikacje.

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
> * Uwzględnij wymaganą zależność dla Resilience4j:
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
> * Kod klienta musi używać interfejsu API programu `CircuitBreakerFactory` , który jest implementowany jako `bean` automatycznie tworzony po dołączeniu wyłącznika chmurowego sprężyny Starter. Aby uzyskać szczegółowe informacje, zobacz Sprężyna wyłącznika [chmurowego](https://spring.io/projects/spring-cloud-circuitbreaker#overview).
>
> * Poniższe 2 zależności powodują konflikt z pakietami resilient4j powyżej.  Upewnij się, że klient nie uwzględni ich.
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
> Przejdź do adresu URL dostarczonego przez aplikacje bramy i uzyskaj dostęp do punktu końcowego z poziomu [demona z chmurą w chmurze](https://github.com/spring-cloud-samples/spring-cloud-circuitbreaker-demo) w następujący sposób:
>
>   ```console
>   /get
>   /get/delay/{seconds}
>   /get/fluxdelay/{seconds}
>   ```

## <a name="locate-resilence4j-metrics-from-portal"></a>Lokalizowanie metryk Resilence4j z portalu

1. Wybierz blok **Application Insights** w portalu Azure wiosny Cloud, a następnie kliknij pozycję **Application Insights**.

   [![resilience4J 0](media/spring-cloud-resilience4j/resilience4J-0.png)](media/spring-cloud-resilience4j/resilience4J-0.PNG)

2. Na stronie **Application Insights** wybierz pozycję **metryki** .  Wybierz pozycję **Azure. ApplicationInsights** z **przestrzeni nazw metryk**.  Należy również wybrać metryki **resilience4j_circuitbreaker_buffered_calls** ze **średnią**.

   [![resilience4J 1](media/spring-cloud-resilience4j/resilience4J-1.png)](media/spring-cloud-resilience4j/resilience4J-1.PNG)

3. Wybierz pozycję **resilience4j_circuitbreaker_calls** metryki i **średnią**.

   [![resilience4J 2](media/spring-cloud-resilience4j/resilience4J-2.png)](media/spring-cloud-resilience4j/resilience4J-2.PNG)

4. Wybierz pozycję **resilience4j_circuitbreaker_calls**  metryki i **średnią**.  Kliknij pozycję **Dodaj filtr**, a następnie wybierz pozycję Nazwa jako **createNewAccount**.

   [![resilience4J 3](media/spring-cloud-resilience4j/resilience4J-3.png)](media/spring-cloud-resilience4j/resilience4J-3.PNG)

5. Wybierz pozycję **resilience4j_circuitbreaker_calls**  metryki i **średnią**.  Następnie kliknij przycisk **Zastosuj podział** i wybierz pozycję **rodzaj**.

   [![resilience4J 4](media/spring-cloud-resilience4j/resilience4J-4.png)](media/spring-cloud-resilience4j/resilience4J-4.PNG)

6. Wybierz **resilience4j_circuitbreaker_calls**, "**resilience4j_circuitbreaker_buffered_calls** i metryki **resilience4j_circuitbreaker_slow_calls** ze **średnią**.

   [![resilience4J 5](media/spring-cloud-resilience4j/resilience4j-5.png)](media/spring-cloud-resilience4j/resilience4j-5.PNG)

## <a name="see-also"></a>Zobacz też

* [Application Insights](spring-cloud-howto-application-insights.md)
* [Rozproszone śledzenie](spring-cloud-howto-distributed-tracing.md)
* [Pulpit nawigacyjny wyłącznika](spring-cloud-tutorial-circuit-breaker.md)
