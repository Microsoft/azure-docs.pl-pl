---
title: Jak używać agenta Application Insights Java In-Process w programie Azure Spring Cloud
description: Jak monitorować aplikacje i mikrousługi przy użyciu Application Insights Java In-Process Agent w Azure Spring Cloud.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: b704d2cf8e2cc8e6cf5d8049290379dd45e26737
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870341"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud-preview"></a>Application Insights Java In-Process Agent w Azure Spring Cloud (wersja zapoznawcza)

W tym dokumencie wyjaśniono, jak monitorować aplikacje i mikrousługi przy użyciu Application Insights Java w Azure Spring Cloud. 

Ta funkcja umożliwia:

* Wyszukiwanie danych śledzenia przy użyciu różnych filtrów.
* Wyświetlanie mapy zależności mikrousług.
* Sprawdź wydajność żądań.
* Monitorowanie metryk na żywo w czasie rzeczywistym.
* Sprawdź błędy żądań.
* Sprawdź metryki aplikacji.

Application Insights zapewniają wiele zauważalnych perspektyw, w tym:

* Mapa aplikacji
* Wydajność
* Błędy
* Metryki
* Metryki na żywo
* Dostępność

## <a name="enable-java-in-process-agent-for-application-insights"></a>Włączanie agenta java In-Process dla Application Insights

Włącz funkcję java In-Process Agent w wersji zapoznawczej, korzystając z poniższej procedury.

1. Przejdź do strony przeglądu usługi wystąpienia usługi.
2. Kliknij **Application Insights** w bloku monitorowania.
3. Kliknij **przycisk Włącz Application Insights,** aby włączyć **Application Insights** integracji.
4. Wybierz istniejące wystąpienie Application Insights lub utwórz nowe.
5. Włączanie **agenta przetwarzania java w** celu włączenia funkcji agenta w procesie w wersji zapoznawczej java. W tym miejscu możesz również dostosować częstotliwość próbkowania z 0 do 100.
6.  Kliknij przycisk **Zapisz**, aby zapisać zmianę.

## <a name="portal"></a>Portal

1. Przejdź do **usługi | Na** stronie Przegląd wybierz **Application Insights** w **sekcji** Monitorowanie. 
2. Kliknij **pozycję Włącz Application Insights,** aby Application Insights w Azure Spring Cloud.
3. Kliknij **pozycję Włącz agenta przetwarzania w języku Java,** aby włączyć funkcję Java IPA w wersji zapoznawczej. Po włączeniu funkcji IPA w wersji zapoznawczej można skonfigurować jedną opcjonalną częstotliwość próbkowania (domyślnie 10,0%).

  [![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

## <a name="using-the-application-insights-feature"></a>Korzystanie z Application Insights funkcji

Po włączeniu **Application Insights** funkcji można:

W lewym okienku nawigacji kliknij pozycję **Application Insights,** aby przejść do **strony Przegląd** Application Insights. 

* Kliknij **pozycję Mapa aplikacji,** aby wyświetlić stan wywołań między aplikacjami.

  [![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* Kliknij link customers-service i aby `petclinic` wyświetlić więcej szczegółów, takich jak zapytanie z bazy danych SQL.

* W okienku nawigacji po lewej stronie kliknij **pozycję** Wydajność, aby wyświetlić dane wydajności wszystkich operacji aplikacji, a także zależności i role.

  [![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* W okienku nawigacji po lewej stronie kliknij pozycję **Błędy,** aby sprawdzić, czy w aplikacjach wystąpił nieoczekiwany błąd.

  [![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* W okienku nawigacji  po lewej stronie kliknij pozycję Metryki i wybierz przestrzeń nazw. Zostaną Spring Boot metryki niestandardowe, jeśli takie są.

  [![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* W okienku nawigacji po lewej stronie kliknij pozycję **Metryki na** żywo, aby wyświetlić metryki czasu rzeczywistego dla różnych wymiarów.

  [![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* W okienku nawigacji po lewej stronie kliknij pozycję **Dostępność,** aby monitorować dostępność i czas odpowiedzi aplikacji internetowych przez utworzenie testów dostępności w [Application Insights](../azure-monitor/app/monitor-web-app-availability.md).

  [![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="arm-template"></a>Szablon ARM
Aby użyć szablonu Azure Resource Manager, skopiuj następującą zawartość do pliku `azuredeploy.json` .

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.AppPlatform/Spring",
            "name": "customize this",
            "apiVersion": "2020-07-01",
            "location": "[resourceGroup().location]",
            "resources": [
                {
                    "type": "monitoringSettings",
                    "apiVersion": "2020-11-01-preview",
                    "name": "default",
                    "properties": {
                        "appInsightsInstrumentationKey": "00000000-0000-0000-0000-000000000000",
                        "appInsightsSamplingRate": 88.0
                    },
                    "dependsOn": [
                        "[resourceId('Microsoft.AppPlatform/Spring', 'customize this')]"
                    ]
                }
            ],
            "properties": {}
        }
    ]
}
```

## <a name="cli"></a>Interfejs wiersza polecenia
Zastosuj szablon arm za pomocą polecenia interfejsu wiersza polecenia:

* W przypadku istniejącego Azure Spring Cloud wystąpienia:

```azurecli
az spring-cloud app-insights update [--app-insights/--app-insights-key] "assignedName" [--sampling-rate] "samplingRate" â€“name "assignedName" â€“resource-group "resourceGroupName"
```
* W przypadku nowo utworzonego Azure Spring Cloud wystąpienia:

```azurecli
az spring-cloud create/update [--app-insights]/[--app-insights-key] "assignedName" --disable-app-insights false --enable-java-agent true --name "assignedName" â€“resource-group "resourceGroupName"
```
* Aby wyłączyć usługę app-insight:

```azurecli
az spring-cloud app-insights update --disable â€“name "assignedName" â€“resource-group "resourceGroupName"

```

## <a name="see-also"></a>Zobacz też
* [Używanie śledzenia rozproszonego z Azure Spring Cloud](spring-cloud-howto-distributed-tracing.md)
* [Analizowanie dzienników i metryk](diagnostic-services.md)
* [Przesyłanie strumieniowe dzienników w czasie rzeczywistym](spring-cloud-howto-log-streaming.md)
