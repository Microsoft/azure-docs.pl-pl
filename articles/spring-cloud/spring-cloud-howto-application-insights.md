---
title: Jak używać Application Insights agenta In-Process Java w chmurze Azure wiosennej
description: Jak monitorować aplikacje i mikrousługi przy użyciu Application Insights In-Process Java w chmurze platformy Azure.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 12/04/2020
ms.custom: devx-track-java
ms.openlocfilehash: c4871c3de8028eec1b6184c1d03ac2180b50f57d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98881354"
---
# <a name="application-insights-java-in-process-agent-in-azure-spring-cloud-preview"></a>Application Insights agenta In-Process języka Java w chmurze Azure wiosennej (wersja zapoznawcza)

W tym dokumencie wyjaśniono, jak monitorować aplikacje i mikrousługi przy użyciu Application Insights agenta Java w chmurze Azure wiosennej. 

Za pomocą tej funkcji można:

* Przeszukaj dane śledzenia przy użyciu różnych filtrów.
* Wyświetl mapę zależności mikrousług.
* Sprawdź wydajność żądania.
* Monitoruj metryki na żywo w czasie rzeczywistym.
* Sprawdź niepowodzenia żądań.
* Sprawdź metryki aplikacji.

Application Insights zapewnić wiele zauważalnych perspektyw, w tym:

* Mapa aplikacji
* Wydajność
* Błędy
* Metryki
* Metryki na żywo
* Dostępność

## <a name="enable-java-in-process-agent-for-application-insights"></a>Włącz agenta In-Process Java dla Application Insights

Włącz funkcję w wersji zapoznawczej programu Java In-Process Agent, wykonując poniższą procedurę.

1. Przejdź do strony Przegląd usługi w wystąpieniu usługi.
2. Kliknij pozycję **Application Insights** wpis w bloku monitorowanie.
3. Kliknij przycisk **włącz Application Insights** , aby włączyć integrację **Application Insights** .
4. Wybierz istniejące wystąpienie Application Insights lub Utwórz nowe.
5. Z pisklętami **Włącz agenta w procesie Java** , aby włączyć funkcję agenta w procesie języka Java w wersji zapoznawczej. W tym miejscu można również dostosować częstotliwość próbkowania od 0 do 100.
6.  Kliknij przycisk **Zapisz**, aby zapisać zmianę.

## <a name="portal"></a>Portal

1. Przejdź do **usługi | Strona przegląd** i wybierz **Application Insights** w sekcji **monitorowanie** . 
2. Kliknij pozycję **włącz Application Insights** , aby włączyć Application Insights w chmurze Azure wiosennej.
3. Kliknij pozycję **Włącz agenta w procesie Java** , aby włączyć funkcję IPA w wersji zapoznawczej języka Java. Po włączeniu funkcji IPA Preview można skonfigurować jedną opcjonalną częstotliwość próbkowania (domyślnie 10,0%).

  [![IPA 0](media/spring-cloud-application-insights/insights-process-agent-0.png)](media/spring-cloud-application-insights/insights-process-agent-0.png)

## <a name="using-the-application-insights-feature"></a>Korzystanie z funkcji Application Insights

Po włączeniu funkcji **Application Insights** można:

W lewym okienku nawigacji kliknij **Application Insights** , aby przejść do strony **Przegląd** Application Insights. 

* Kliknij pozycję **Mapa aplikacji** , aby zobaczyć stan wywołań między aplikacjami.

  [![IPA 2](media/spring-cloud-application-insights/insights-process-agent-2-map.png)](media/spring-cloud-application-insights/insights-process-agent-2-map.png)

* Kliknij link między klientami a usługą, `petclinic` Aby zobaczyć więcej szczegółów, takich jak zapytanie z bazy danych SQL.

* W okienku nawigacji po lewej stronie kliknij pozycję **wydajność** , aby wyświetlić dane wydajności wszystkich operacji aplikacji, a także zależności i role.

  [![IPA 4](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)](media/spring-cloud-application-insights/insights-process-agent-4-performance.png)

* W okienku nawigacji po lewej stronie kliknij pozycję **niepowodzenia** , aby sprawdzić, czy aplikacje nie są oczekiwane.

  [![IPA 6](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)](media/spring-cloud-application-insights/insights-process-agent-6-failures.png)

* W okienku nawigacji po lewej stronie kliknij pozycję **metryki** , a następnie wybierz obszar nazw, a jeśli istnieją, zostaną wyświetlone zarówno metryki rozruchowe wiosny, jak i metryki niestandardowe.

  [![IPA 7](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)](media/spring-cloud-application-insights/insights-process-agent-5-metrics.png)

* W okienku nawigacji po lewej stronie kliknij pozycję **metryki na żywo** , aby wyświetlić metryki w czasie rzeczywistym dla różnych wymiarów.

  [![IPA 8](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)](media/spring-cloud-application-insights/petclinic-microservices-live-metrics.jpg)

* W okienku nawigacji po lewej stronie kliknij pozycję **dostępność** , aby monitorować dostępność i czas odpowiedzi aplikacji sieci Web, tworząc [testy dostępności w Application Insights](../azure-monitor/app/monitor-web-app-availability.md).

  [![IPA 9](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)](media/spring-cloud-application-insights/petclinic-microservices-availability.jpg)

## <a name="arm-template"></a>Szablon ARM
Aby użyć szablonu Azure Resource Manager, Skopiuj następującą zawartość do programu `azuredeploy.json` .

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
Zastosuj szablon ARM przy użyciu polecenia CLI:

* Dla istniejącego wystąpienia chmury Azure wiosną:

```azurecli
az spring-cloud app-insights update [--app-insights/--app-insights-key] "assignedName" [--sampling-rate] "samplingRate" –name "assignedName" –resource-group "resourceGroupName"
```
* Dla nowo utworzonego wystąpienia chmury Azure wiosną:

```azurecli
az spring-cloud create/update [--app-insights]/[--app-insights-key] "assignedName" --disable-app-insights false --enable-java-agent true --name "assignedName" –resource-group "resourceGroupName"
```
* Aby wyłączyć funkcję App-Insight:

```azurecli
az spring-cloud app-insights update --disable –name "assignedName" –resource-group "resourceGroupName"

```

## <a name="see-also"></a>Zobacz też
* [Korzystanie z rozproszonego śledzenia w chmurze Azure wiosennej](spring-cloud-tutorial-distributed-tracing.md)
* [Analizowanie dzienników i metryk](diagnostic-services.md)
* [Przesyłanie strumieniowe dzienników w czasie rzeczywistym](spring-cloud-howto-log-streaming.md)