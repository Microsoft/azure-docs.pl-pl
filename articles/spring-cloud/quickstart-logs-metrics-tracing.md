---
title: Szybki Start — monitorowanie aplikacji w chmurze platformy Azure z użyciem dzienników, metryk i śledzenia
description: Używaj przesyłania strumieniowego dzienników, usługi log Analytics, metryk i śledzenia, aby monitorować przykładowe aplikacje Piggymetrics w chmurze Azure wiosennej.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 75c50e6342a00ef37215d9e961503dd7ffb0d205
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104879339"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>Szybki Start: monitorowanie aplikacji w chmurze platformy Azure z użyciem dzienników, metryk i śledzenia

::: zone pivot="programming-language-csharp"
Korzystając z wbudowanej funkcji monitorowania w chmurze Azure wiosennej, można debugować i monitorować złożone problemy. Chmura sprężynowa platformy Azure integruje [śledzenie rozproszone](https://steeltoe.io/docs/3/tracing/distributed-tracing) Steeltoe z [Application Insights](../azure-monitor/app/app-insights-overview.md)platformy Azure. Ta integracja zapewnia zaawansowane dzienniki, metryki i funkcję śledzenia rozproszonego z Azure Portal.

W poniższych procedurach opisano sposób używania przesyłania strumieniowego dzienników, Log Analytics, metryk i śledzenia rozproszonego z przykładową aplikacją wdrożoną w poprzednich przewodnikach Szybki Start.

## <a name="prerequisites"></a>Wymagania wstępne

* Wykonaj poprzednie Przewodniki Szybki Start w tej serii:

  * [Zainicjuj obsługę usługi w chmurze Azure wiosennej](spring-cloud-quickstart-provision-service-instance.md).
  * [Skonfiguruj serwer konfiguracji chmury z platformą Azure](spring-cloud-quickstart-setup-config-server.md).
  * [Kompiluj i wdrażaj aplikacje](spring-cloud-quickstart-deploy-apps.md).

## <a name="logs"></a>Dzienniki

Istnieją dwa sposoby wyświetlania dzienników w chmurze Azure wiosennej: **przesyłanie strumieniowe** dzienników w czasie rzeczywistym na wystąpienie aplikacji lub **log Analytics** dla zagregowanych dzienników z zaawansowanymi możliwościami zapytań.

### <a name="log-streaming"></a>Przesyłanie strumieniowe dzienników

Można użyć przesyłania strumieniowego dzienników w interfejsie wiersza polecenia platformy Azure przy użyciu poniższe polecenie.

```azurecli
az spring-cloud app logs -n solar-system-weather -f
```

Zobaczysz dane wyjściowe podobne do poniższego przykładu:

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
> Użyj `az spring-cloud app logs -h` , aby poznać więcej parametrów i funkcję strumienia dzienników.

### <a name="log-analytics"></a>Log Analytics

1. W Azure Portal przejdź do **usługi |** Na stronie Omówienie i wybierz pozycję **dzienniki** w sekcji **monitorowanie** . Wybierz pozycję **Uruchom** na jednym z przykładowych zapytań dotyczących chmury wiosennej platformy Azure.

   [![Wpis ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png) usługi Logs Analytics](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Edytuj zapytanie, aby usunąć klauzule WHERE, które ograniczają wyświetlanie do dzienników ostrzeżeń i błędów.

1. Następnie wybierz pozycję, a zobaczysz pozycję `Run` dzienniki. Zobacz artykuł [Azure log Analytics docs](../azure-monitor/logs/get-started-queries.md) , aby uzyskać więcej wskazówek dotyczących pisania zapytań.

   [![Zapytanie usługi Logs Analytics — steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query-steeltoe.png#lightbox)

## <a name="metrics"></a>Metryki

1. W Azure Portal przejdź do **usługi |** Na stronie Przegląd wybierz pozycję **metryki** w sekcji **monitorowanie** . Dodaj pierwszą metrykę, wybierając jedną z metryk platformy .NET w obszarze **wydajność (.NET)** lub **żądanie (.NET)** na liście rozwijanej **Metryka** , a następnie pozycję `Avg` **agregacji** , aby wyświetlić oś czasu dla tej metryki.

   [![Wpis metryk — steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu-steeltoe.png#lightbox)
    
1. Kliknij przycisk **Dodaj filtr** na pasku narzędzi, wybierz opcję, `App=solar-system-weather` Aby zobaczyć użycie procesora tylko dla aplikacji **słonecznej System-Pogoda** .

   [![Używanie filtru w metrykach — steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter-steeltoe.png#lightbox)

1. Odrzuć filtr utworzony w poprzednim kroku, wybierz pozycję **Zastosuj podział**, a następnie wybierz pozycję `App` **wartości** , aby wyświetlić użycie procesora przez różne aplikacje.

   [![Zastosuj podział w metrykach — steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split-steeltoe.png#lightbox)

## <a name="distributed-tracing"></a>Rozproszone śledzenie

1. W Azure Portal przejdź do **usługi |** Na stronie Omówienie i wybierz opcję **śledzenie rozproszone** w sekcji **monitorowanie** . Następnie wybierz kartę **Wyświetl mapę aplikacji** po prawej stronie.

   [![Wpis śledzenia rozproszonego — steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Teraz można zobaczyć stan wywołań między aplikacjami. 

   [![Śledzenie rozproszone — Omówienie — steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview-steeltoe.png#lightbox)
    
1. Wybierz łącze między dostawcą **słonecznym system-Pogoda** i **globalnej-Pogoda-dostawca** , aby zobaczyć więcej szczegółów, takich jak Najwolniejsze wywołania przy użyciu metod http.

   [![Śledzenie rozproszone — steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call-steeltoe.png#lightbox)
    
1. Na koniec wybierz pozycję **Zbadaj wydajność** , aby poznać bardziej zaawansowaną analizę wydajności.

   [![Wydajność śledzenia rozproszonego — steeltoe ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance-steeltoe.png#lightbox)
::: zone-end

::: zone pivot="programming-language-java"
Korzystając z wbudowanej funkcji monitorowania w chmurze Azure wiosennej, można debugować i monitorować złożone problemy. Chmura ze sprężyną Azure integruje się z [chmurą Sleuth](https://spring.io/projects/spring-cloud-sleuth) z platformą [Application Insights](../azure-monitor/app/app-insights-overview.md)Azure. Ta integracja zapewnia zaawansowane dzienniki, metryki i funkcję śledzenia rozproszonego z Azure Portal. W poniższych procedurach opisano sposób używania przesyłania strumieniowego dzienników, Log Analytics, metryk i śledzenia rozproszonego ze wdrożonymi aplikacjami PiggyMetrics.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj poprzednie kroki: 

* [Udostępnianie wystąpienia chmury wiosennej platformy Azure](spring-cloud-quickstart-provision-service-instance.md)
* [Konfigurowanie serwera konfiguracji](spring-cloud-quickstart-setup-config-server.md)
* [Kompiluj i wdrażaj aplikacje](spring-cloud-quickstart-deploy-apps.md).

## <a name="logs"></a>Dzienniki

Istnieją dwa sposoby wyświetlania dzienników w chmurze Azure wiosennej: **przesyłanie strumieniowe** dzienników w czasie rzeczywistym na wystąpienie aplikacji lub **log Analytics** dla zagregowanych dzienników z zaawansowanymi możliwościami zapytań.

### <a name="log-streaming"></a>Przesyłanie strumieniowe dzienników

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/Azure-CLI)

Można użyć przesyłania strumieniowego dzienników w interfejsie wiersza polecenia platformy Azure przy użyciu poniższe polecenie.

```azurecli
az spring-cloud app logs -s <service instance name> -g <resource group name> -n gateway -f
```

Zostaną wyświetlone następujące dzienniki:

[![Przesyłanie strumieniowe dzienników z interfejsu wiersza polecenia ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png) platformy Azure](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png#lightbox)

> [!TIP]
> Użyj `az spring-cloud app logs -h` , aby poznać dodatkowe parametry i funkcje strumienia dzienników.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

Aby pobrać dzienniki przy użyciu Azure Toolkit for IntelliJ:

1. Wybierz opcję **Eksplorator platformy Azure**, a następnie pozycję **chmura Wiosenna**.

1. Kliknij prawym przyciskiem myszy działającą aplikację.

1. Z listy rozwijanej wybierz pozycję **dzienniki przesyłania strumieniowego** .

   ![Wybieranie dzienników przesyłania strumieniowego](media/spring-cloud-intellij-howto/streaming-logs.png)
    
1. Wybierz **wystąpienie**.

   ![Wybierz wystąpienie](media/spring-cloud-intellij-howto/select-instance.png)
    
1. Dziennik przesyłania strumieniowego będzie widoczny w oknie danych wyjściowych.

   ![Dane wyjściowe dziennika przesyłania strumieniowego](media/spring-cloud-intellij-howto/streaming-log-output.png)

---
### <a name="log-analytics"></a>Log Analytics

1. Przejdź do **usługi |** Na stronie Omówienie i wybierz pozycję **dzienniki** w sekcji **monitorowanie** . Kliknij przycisk **Uruchom** na jednym z przykładowych zapytań dotyczących chmury wiosennej platformy Azure. 

   [![Wpis ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png) usługi Logs Analytics](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Następnie zostaną wyświetlone odfiltrowane dzienniki. Zobacz artykuł [Azure log Analytics docs](../azure-monitor/logs/get-started-queries.md) , aby uzyskać więcej wskazówek dotyczących pisania zapytań.

   [![Zapytanie ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png) usługi Logs Analytics](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png#lightbox)

## <a name="metrics"></a>Metryki

1. Przejdź do **usługi |** Na stronie Przegląd wybierz pozycję **metryki** w sekcji **monitorowanie** . Dodaj pierwszą metrykę, wybierając `system.cpu.usage` opcję **Metryka** i `Avg` **agregację** , aby wyświetlić oś czasu dla ogólnego użycia procesora.

   [![Wpis ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png) metryk](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png#lightbox)
    
1. Kliknij przycisk **Dodaj filtr** na pasku narzędzi powyżej, zaznacz, `App=Gateway` Aby zobaczyć użycie procesora tylko dla aplikacji **bramy** .

   [![Użyj filtru w metrykach ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png#lightbox)

1. Odrzuć utworzony powyżej filtr, kliknij przycisk **Zastosuj podział** i `App` Wybierz **wartości** , aby zobaczyć użycie procesora przez różne aplikacje.

   [![Zastosuj podział w metrykach ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png#lightbox)

## <a name="distributed-tracing"></a>Rozproszone śledzenie

1. Przejdź do **usługi |** Na stronie Omówienie i wybierz opcję **śledzenie rozproszone** w sekcji **monitorowanie** . Następnie kliknij kartę **Wyświetl mapę aplikacji** po prawej stronie.

   [![Wpis ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png) śledzenia rozproszonego](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Teraz można zobaczyć stan wywołań między aplikacjami Piggymetrics. 

   [![Śledzenie rozproszone — Omówienie ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png#lightbox)
    
1. Kliknij link między **bramą** a **kontem usługi** , aby zobaczyć więcej szczegółów, takich jak Najwolniejsze wywołania przez metody http.

   [![Śledzenie ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png) rozproszone](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png#lightbox)
    
1. Na koniec kliknij pozycję **Zbadaj wydajność** , aby poznać bardziej zaawansowaną analizę wydajności.

   [![Wydajność ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png) śledzenia rozproszonego](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png#lightbox)

::: zone-end

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W tych przewodnikach szybki start utworzono zasoby platformy Azure, które będą nadal naliczane opłaty, jeśli pozostaną w ramach subskrypcji. Jeśli nie chcesz potrzebować tych zasobów w przyszłości, Usuń grupę zasobów za pomocą portalu lub uruchamiając następujące polecenie w Cloud Shell:

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

W poprzednim przewodniku szybki start określono również domyślną nazwę grupy zasobów. Jeśli nie planujesz kontynuować pracy z następnym przewodnikiem Szybki Start, wyczyść to ustawienie domyślne, uruchamiając następujące polecenie interfejsu wiersza polecenia:

```azurecli
az configure --defaults group=
```

## <a name="next-steps"></a>Następne kroki

Aby poznać więcej możliwości monitorowania chmury wiosennej platformy Azure, zobacz:

> [!div class="nextstepaction"]
> [Usługi diagnostyczne](diagnostic-services.md)
>
> [Rozproszone śledzenie](spring-cloud-howto-distributed-tracing.md)
>
> [Przesyłanie strumieniowe dzienników w czasie rzeczywistym](spring-cloud-howto-log-streaming.md)
