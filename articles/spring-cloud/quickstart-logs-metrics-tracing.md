---
title: Szybki start — monitorowanie Azure Spring Cloud za pomocą dzienników, metryk i śledzenia
description: Używanie przesyłania strumieniowego dzienników, analizy dzienników, metryk i śledzenia do monitorowania przykładowych aplikacji Piggymetrics na Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 907bf06323d13b2d26dec5003e4739f2ae9faf74
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378520"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>Szybki start: monitorowanie Azure Spring Cloud za pomocą dzienników, metryk i śledzenia

::: zone pivot="programming-language-csharp"
Wbudowana funkcja monitorowania w programie Azure Spring Cloud umożliwia debugowanie i monitorowanie złożonych problemów. Azure Spring Cloud jest zintegrowana ze śledzeniem [rozproszonym](https://steeltoe.io/docs/3/tracing/distributed-tracing) firmy Steeltoe z usługą Azure [Application Insights.](../azure-monitor/app/app-insights-overview.md) Ta integracja zapewnia zaawansowane dzienniki, metryki i możliwość śledzenia rozproszonego z Azure Portal.

Poniższe procedury wyjaśniają, jak używać przesyłania strumieniowego dzienników, usługi Log Analytics, metryk i śledzenia rozproszonego z przykładową aplikacją wdrożoną w poprzednich przewodnikach Szybki start.

## <a name="prerequisites"></a>Wymagania wstępne

* Ukończ poprzednie przewodniki Szybki start z tej serii:

  * [Aprowizuj Azure Spring Cloud usługi .](spring-cloud-quickstart-provision-service-instance.md)
  * [Skonfiguruj Azure Spring Cloud konfiguracji systemu](spring-cloud-quickstart-setup-config-server.md).
  * [Kompilowanie i wdrażanie aplikacji.](spring-cloud-quickstart-deploy-apps.md)

## <a name="logs"></a>Dzienniki

Istnieją dwa sposoby, aby wyświetlić dzienniki na **Azure Spring Cloud:** przesyłanie strumieniowe dzienników dzienników w czasie rzeczywistym na wystąpienie aplikacji lub usługa **Log Analytics** dla zagregowanych dzienników z zaawansowaną możliwością wykonywania zapytań.

### <a name="log-streaming"></a>Przesyłanie strumieniowe dzienników

Możesz użyć przesyłania strumieniowego dzienników w interfejsie wiersza polecenia platformy Azure za pomocą następującego polecenia.

```azurecli
az spring-cloud app logs -n solar-system-weather -f
```

Zostaną wyświetlony dane wyjściowe podobne do następujących:

```output
=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing action method Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather) - Validation state: Valid
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController[0]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Retrieved weather data from 4 planets
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]

=> ConnectionId:0HM2HOMHT82UK => RequestPath:/weatherforecast RequestId:0HM2HOMHT82UK:00000003, SpanId:|e8c1682e-46518cc0202c5fd9., TraceId:e8c1682e-46518cc0202c5fd9, ParentId: => Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.Controllers.WeatherForecastController.Get (Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather)
Executing ObjectResult, writing value of type 'System.Collections.Generic.KeyValuePair`2[[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e],[System.String, System.Private.CoreLib, Version=4.0.0.0, Culture=neutral, PublicKeyToken=7cec85d7bea7798e]][]'.
←[40m←[32minfo←[39m←[22m←[49m: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
```

> [!TIP]
> Użyj `az spring-cloud app logs -h` polecenia , aby poznać więcej parametrów i funkcjonalność strumienia dzienników.

### <a name="log-analytics"></a>Log Analytics

1. W Azure Portal przejdź do **| Na stronie** Przegląd wybierz **pozycję Dzienniki** w **sekcji** Monitorowanie. Wybierz **pozycję Uruchom** dla jednego z przykładowych zapytań dla Azure Spring Cloud.

   [![Wpis usługi Logs Analytics ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Edytuj zapytanie, aby usunąć klauzule Where, które ograniczają wyświetlanie do dzienników ostrzeżeń i błędów.

1. Następnie wybierz `Run` pozycję , aby wyświetlić dzienniki. Aby uzyskać [więcej wskazówek dotyczących pisania](../azure-monitor/logs/get-started-queries.md) zapytań, zobacz dokumenty usługi Azure Log Analytics.

   [![Zapytanie usługi Logs Analytics — Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png#lightbox)

1. Aby dowiedzieć się więcej na temat języka zapytań używanego w u usługi Log Analytics, zobacz [Azure Monitor zapytań dziennika.](/azure/data-explorer/kusto/query/) Aby wysyłać zapytania do wszystkich dzienników usługi Log Analytics ze scentralizowanego klienta, zapoznaj się [z Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/query-monitor-data).

## <a name="metrics"></a>Metryki

1. W Azure Portal przejdź do usługi **| Na** stronie Przegląd wybierz **pozycję Metryki** w **sekcji** Monitorowanie. Dodaj pierwszą metrykę, wybierając jedną z metryk .NET w obszarze Wydajność **(.NET)** lub Żądanie **(.NET)** na liście rozwijanej Metryka, a w obszarze Agregacja, aby wyświetlić oś czasu dla tej  `Avg` metryki. 

   [![Wpis Metryki — Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png#lightbox)
    
1. Kliknij **przycisk Dodaj filtr** na pasku narzędzi, wybierz pozycję , aby wyświetlić użycie procesora CPU tylko dla aplikacji z `App=solar-system-weather` **układem słonecznym i pogodą.**

   [![Używanie filtru w metrykach — Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png#lightbox)

1. Odrzuć filtr utworzony w poprzednim kroku, wybierz pozycję Zastosuj podział **i** wybierz wartość Wartości, aby zobaczyć użycie procesora CPU przez `App` różne aplikacje. 

   [![Stosowanie podziału w metrykach — Zesłoń ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png#lightbox)

## <a name="distributed-tracing"></a>Rozproszone śledzenie

1. W Azure Portal przejdź do usługi **| Na stronie** Przegląd wybierz pozycję **Śledzenie rozproszone** w **sekcji** Monitorowanie. Następnie wybierz **kartę Wyświetl mapę aplikacji** po prawej stronie.

   [![Wpis śledzenia rozproszonego — Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Teraz możesz zobaczyć stan wywołań między aplikacjami. 

   [![Omówienie śledzenia rozproszonego — Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png#lightbox)
    
1. Wybierz połączenie między **solar-system-weather i** **planet-weather-provider,** aby wyświetlić więcej szczegółów, takich jak najwolniejsze wywołania metod HTTP.

   [![Śledzenie rozproszone — Zesłone ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png#lightbox)
    
1. Na koniec wybierz pozycję **Zbadaj wydajność,** aby poznać bardziej zaawansowaną wbudowaną analizę wydajności.

   [![Wydajność śledzenia rozproszonego — Steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png#lightbox)
::: zone-end

::: zone pivot="programming-language-java"
Wbudowana funkcja monitorowania w programie Azure Spring Cloud umożliwia debugowanie i monitorowanie złożonych problemów. Azure Spring Cloud integruje [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) z usługą Azure [Application Insights.](../azure-monitor/app/app-insights-overview.md) Ta integracja zapewnia zaawansowane dzienniki, metryki i możliwość śledzenia rozproszonego z Azure Portal. Poniższe procedury wyjaśniają, jak używać przesyłania strumieniowego dzienników, usługi Log Analytics, metryk i śledzenia rozproszonego z wdrożonymi aplikacjami PiggyMetrics.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj poprzednie kroki: 

* [Aprowizuj wystąpienie Azure Spring Cloud](spring-cloud-quickstart-provision-service-instance.md)
* [Konfigurowanie serwera konfiguracji](spring-cloud-quickstart-setup-config-server.md)
* [Kompilowanie i wdrażanie aplikacji.](spring-cloud-quickstart-deploy-apps.md)

## <a name="logs"></a>Dzienniki

Istnieją dwa sposoby, aby wyświetlić dzienniki na **Azure Spring Cloud:** przesyłanie strumieniowe dzienników dzienników w czasie rzeczywistym na wystąpienie aplikacji lub usługa **Log Analytics** dla zagregowanych dzienników z zaawansowaną możliwością wykonywania zapytań.

### <a name="log-streaming"></a>Przesyłanie strumieniowe dzienników

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/Azure-CLI)

Możesz użyć przesyłania strumieniowego dzienników w interfejsie wiersza polecenia platformy Azure za pomocą następującego polecenia.

```azurecli
az spring-cloud app logs -s <service instance name> -g <resource group name> -n gateway -f
```

Zobaczysz dzienniki podobne do tych:

[![Przesyłanie strumieniowe dzienników z interfejsu wiersza polecenia platformy Azure ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png#lightbox)

> [!TIP]
> Użyj `az spring-cloud app logs -h` polecenia , aby poznać więcej parametrów i funkcji strumienia dzienników.

#### <a name="intellij"></a>[Intellij](#tab/IntelliJ)

Aby pobrać dzienniki przy użyciu Azure Toolkit for IntelliJ:

1. Wybierz **pozycję Azure Explorer**, a następnie **Spring Cloud**.

1. Kliknij prawym przyciskiem myszy uruchamianą aplikację.

1. Wybierz **pozycję Dzienniki przesyłania** strumieniowego z listy rozwijanej.

   ![Wybieranie dzienników przesyłania strumieniowego](media/spring-cloud-intellij-howto/streaming-logs.png)
    
1. Wybierz **pozycję Wystąpienie**.

   ![Wybieranie wystąpienia](media/spring-cloud-intellij-howto/select-instance.png)
    
1. Dziennik przesyłania strumieniowego będzie widoczny w oknie danych wyjściowych.

   ![Dane wyjściowe dziennika przesyłania strumieniowego](media/spring-cloud-intellij-howto/streaming-log-output.png)

---
### <a name="log-analytics"></a>Log Analytics

1. Przejdź do **usługi | Na stronie** Przegląd wybierz **pozycję Dzienniki** w **sekcji** Monitorowanie. Kliknij **przycisk Uruchom** dla jednego z przykładowych zapytań dla Azure Spring Cloud. 

   [![Wpis usługi Logs Analytics ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Następnie zobaczysz odfiltrowane dzienniki. Aby uzyskać [więcej wskazówek dotyczących pisania](../azure-monitor/logs/get-started-queries.md) zapytań, zobacz dokumenty usługi Azure Log Analytics.

   [![Zapytanie usługi Logs Analytics ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png#lightbox)

1. Aby dowiedzieć się więcej o języku zapytań używanym przez usługę Log Analytics, zobacz [Azure Monitor zapytań dziennika](/azure/data-explorer/kusto/query/). Aby wysyłać zapytania do wszystkich dzienników usługi Log Analytics ze scentralizowanego klienta, zapoznaj się [z Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/query-monitor-data).

## <a name="metrics"></a>Metryki

1. Przejdź do **usługi | Na** stronie Przegląd wybierz **pozycję Metryki** w **sekcji** Monitorowanie. Dodaj swoją pierwszą metrykę, wybierając pozycję `system.cpu.usage` **Metryka** i `Avg` **pozycję Agregacja,** aby wyświetlić oś czasu ogólnego użycia procesora CPU.

   [![Wpis metryk ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png#lightbox)
    
1. Kliknij **przycisk Dodaj filtr** na powyższym pasku narzędzi, wybierz pozycję , aby wyświetlić użycie procesora CPU tylko dla `App=Gateway` **aplikacji** bramy.

   [![Używanie filtru w metrykach ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png#lightbox)

1. Odrzuć utworzony powyżej filtr, kliknij pozycję **Zastosuj podział** i wybierz pozycję `App` **Wartości,** aby zobaczyć użycie procesora CPU przez różne aplikacje.

   [![Stosowanie podziału w metrykach ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png#lightbox)

## <a name="distributed-tracing"></a>Rozproszone śledzenie

1. Przejdź do **usługi | Na stronie** Przegląd wybierz pozycję **Śledzenie rozproszone** w **sekcji** Monitorowanie. Następnie kliknij **kartę Wyświetl mapę aplikacji** po prawej stronie.

   [![Wpis śledzenia rozproszonego ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Teraz możesz zobaczyć stan wywołań między aplikacjami Piggymetrics. 

   [![Omówienie śledzenia rozproszonego ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png#lightbox)
    
1. Kliknij połączenie między **bramą** i **usługą konta,** aby wyświetlić więcej szczegółów, takich jak najwolniejsze wywołania metod HTTP.

   [![Śledzenie rozproszone ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png#lightbox)
    
1. Na koniec kliknij pozycję **Zbadaj wydajność,** aby poznać bardziej zaawansowaną wbudowaną analizę wydajności.

   [![Wydajność śledzenia rozproszonego ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png#lightbox)

::: zone-end

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W tych przewodnikach Szybki start utworzono zasoby platformy Azure, które będą nadal naliczać opłaty, jeśli pozostaną w Twojej subskrypcji. Jeśli nie będziesz potrzebować tych zasobów w przyszłości, usuń grupę zasobów przy użyciu portalu lub uruchamiając następujące polecenie w Cloud Shell:

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

We wcześniejszym przewodniku Szybki start ustawisz również domyślną nazwę grupy zasobów. Jeśli nie zamierzasz kontynuować następnego przewodnika Szybki start, wyczyść to ustawienie domyślne, uruchamiając następujące polecenie interfejsu wiersza polecenia:

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>Następne kroki

Aby poznać więcej możliwości monitorowania Azure Spring Cloud, zobacz:

> [!div class="nextstepaction"]
> [Usługi diagnostyczne](diagnostic-services.md)
>
> [Rozproszone śledzenie](spring-cloud-howto-distributed-tracing.md)
>
> [Przesyłanie strumieniowe dzienników w czasie rzeczywistym](spring-cloud-howto-log-streaming.md)
