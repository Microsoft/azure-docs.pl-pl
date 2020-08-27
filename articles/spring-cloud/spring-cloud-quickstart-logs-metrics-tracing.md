---
title: Szybki Start — monitorowanie aplikacji w chmurze platformy Azure z użyciem dzienników, metryk i śledzenia
description: Używaj przesyłania strumieniowego dzienników, usługi log Analytics, metryk i śledzenia, aby monitorować przykładowe aplikacje Piggymetrics w chmurze Azure wiosennej.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: 3737c7ec9018bfb7f884eab89a987bcada724483
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951744"
---
# <a name="quickstart-monitoring-azure-spring-cloud-apps-with-logs-metrics-and-tracing"></a>Szybki Start: monitorowanie aplikacji w chmurze platformy Azure z użyciem dzienników, metryk i śledzenia

Wbudowana funkcja monitorowania w chmurze Azure wiosennej umożliwia łatwe debugowanie i monitorowanie złożonych problemów. Chmura ze sprężyną Azure integruje się z [chmurą Sleuth](https://spring.io/projects/spring-cloud-sleuth) z platformą [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)Azure. Ta integracja zapewnia zaawansowane dzienniki, metryki i funkcję śledzenia rozproszonego z Azure Portal. Poprowadzimy Cię przez Ciebie, jak korzystać z przesyłania strumieniowego dzienników, Log Analytics, metryk i rozproszonego śledzenia przy użyciu wdrożonych aplikacji PiggyMetrics.

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

Zobaczysz dzienniki, takie jak

   [![Przesyłanie strumieniowe dzienników z interfejsu wiersza polecenia ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png) platformy Azure](media/spring-cloud-quickstart-logs-metrics-tracing/logs-streaming-cli.png#lightbox)

>[!TIP]
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

1. Przejdź do **usługi | ** Na stronie Omówienie i wybierz pozycję **dzienniki** w sekcji **monitorowanie** . Kliknij przycisk **Uruchom** na jednym z przykładowych zapytań dotyczących chmury wiosennej platformy Azure. 

    [![Wpis ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png) usługi Logs Analytics](media/spring-cloud-quickstart-logs-metrics-tracing/logs-entry.png#lightbox)
    
1. Następnie zostaną wyświetlone odfiltrowane dzienniki. Zobacz artykuł [Azure log Analytics docs](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-queries) , aby uzyskać więcej wskazówek dotyczących pisania zapytań.

    [![Zapytanie ](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png) usługi Logs Analytics](media/spring-cloud-quickstart-logs-metrics-tracing/logs-query.png#lightbox)

## <a name="metrics"></a>Metryki
1. Przejdź do **usługi | ** Na stronie Przegląd wybierz pozycję **metryki** w sekcji **monitorowanie** . Dodaj pierwszą metrykę, wybierając `system.cpu.usage` opcję **Metryka** i `Avg` **agregację** , aby wyświetlić oś czasu dla ogólnego użycia procesora.

    [![Wpis ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png) metryk](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-basic-cpu.png#lightbox)
    
1. Kliknij przycisk **Dodaj filtr** na pasku narzędzi powyżej, zaznacz, `App=Gateway` Aby zobaczyć użycie procesora tylko dla aplikacji **bramy** .

    [![Użyj filtru w metrykach ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-filter.png#lightbox)

1. Odrzuć utworzony powyżej filtr, kliknij przycisk **Zastosuj podział** i `App` Wybierz **wartości** , aby zobaczyć użycie procesora przez różne aplikacje.

    [![Zastosuj podział w metrykach ](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png)](media/spring-cloud-quickstart-logs-metrics-tracing/metrics-split.png#lightbox)

## <a name="distributed-tracing"></a>Śledzenie rozproszone
1. Przejdź do **usługi | ** Na stronie Omówienie i wybierz opcję **śledzenie rozproszone** w sekcji **monitorowanie** . Następnie kliknij kartę **Wyświetl mapę aplikacji** po prawej stronie.

    [![Wpis ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png) śledzenia rozproszonego](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-entry.png#lightbox)

1. Teraz można zobaczyć stan wywołań między aplikacjami Piggymetrics. 

    [![Śledzenie rozproszone — Omówienie ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png)](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-overview.png#lightbox)
    
1. Kliknij link między **bramą** a **kontem usługi** , aby zobaczyć więcej szczegółów, takich jak Najwolniejsze wywołania przez metody http.

    [![Śledzenie ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png) rozproszone](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-call.png#lightbox)
    
1. Na koniec kliknij pozycję **Zbadaj wydajność** , aby poznać bardziej zaawansowaną analizę wydajności.

    [![Wydajność ](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png) śledzenia rozproszonego](media/spring-cloud-quickstart-logs-metrics-tracing/tracing-performance.png#lightbox)

## <a name="clean-up-resources"></a>Czyszczenie zasobów
W poprzednich krokach utworzono zasoby platformy Azure w grupie zasobów. Jeśli nie chcesz potrzebować tych zasobów w przyszłości, Usuń grupę zasobów z portalu lub uruchamiając następujące polecenie w Cloud Shell:
```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```
W poprzednich krokach należy również ustawić domyślną nazwę grupy zasobów. Aby wyczyścić to ustawienie domyślne, uruchom następujące polecenie w Cloud Shell:
```azurecli
az configure --defaults group=
```
## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat możliwości monitorowania w chmurze platformy Azure, zobacz:
> [!div class="nextstepaction"]
> [Usługi diagnostyczne](diagnostic-services.md) 
>  [Śledzenie rozproszone](spring-cloud-tutorial-distributed-tracing.md) 
>  [Rejestrowanie strumieni w czasie rzeczywistym](spring-cloud-howto-log-streaming.md)
