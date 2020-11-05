---
title: Samouczek — monitorowanie opublikowanych interfejsów API na platformie Azure API Management | Microsoft Docs
description: Wykonaj kroki tego samouczka, aby dowiedzieć się, jak używać metryk, alertów, dzienników aktywności i dzienników zasobów do monitorowania interfejsów API w usłudze Azure API Management.
services: api-management
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: apimpm
ms.openlocfilehash: 2317e61111c3ad328e8f112e7d9567f3f5d47990
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93379419"
---
# <a name="tutorial-monitor-published-apis"></a>Samouczek: monitorowanie opublikowanych interfejsów API

Za pomocą Azure Monitor można wizualizować, wykonywać zapytania, kierować, archiwizować i wykonywać akcje dotyczące metryk lub dzienników pochodzących z usługi Azure API Management.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wyświetlanie metryk interfejsu API 
> * Konfigurowanie reguły alertu 
> * Wyświetlanie dzienników aktywności
> * Włączanie i wyświetlanie dzienników zasobów

## <a name="prerequisites"></a>Wymagania wstępne

+ Poznaj [terminologię dotyczącą usługi Azure API Management](api-management-terminology.md).
+ Wykonaj następujące kroki szybkiego startu: [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
+ Ponadto wykonaj zadania z następującego samouczka: [Importowanie i publikowanie pierwszego interfejsu API](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>Wyświetlanie metryk interfejsów API

API Management emituje [metryki](../azure-monitor/platform/data-platform-metrics.md) co minutę, dzięki czemu możesz niemal w czasie rzeczywistym uwidocznić stan i kondycję interfejsów API. Poniżej przedstawiono dwa najczęściej używane metryki. Aby uzyskać listę wszystkich dostępnych metryk, zobacz temat [obsługiwane metryki](../azure-monitor/platform/metrics-supported.md#microsoftapimanagementservice).

* **Pojemność** — ułatwia podejmowanie decyzji o uaktualnianiu/obniżeniu wydajności usług APIM. Metryka jest emitowana co minutę i odzwierciedla pojemność bramy w momencie raportowania. Wartość metryki należy do zakresu od 0 do 100 i jest obliczana w oparciu o zasoby bramy, takie jak wykorzystanie procesora i pamięci.
* **Żądania** — pomaga analizować ruch interfejsu API za pośrednictwem usług API Management. Metryka jest emitowana na minutę i zgłasza liczbę żądań bramy z wymiarami, w tym kodami odpowiedzi, lokalizacjami, nazwą hosta i błędami. 

> [!IMPORTANT]
> Następujące metryki zostały wycofane z maja 2019 i zostaną wycofane w sierpniu 2023: całkowita liczba żądań bramy, pomyślne żądania bramy, nieautoryzowane żądania bramy, Nieudane żądania bramy, inne żądania bramy. Przeprowadź migrację do metryki żądań, która zapewnia równoważną funkcjonalność.

:::image type="content" source="media/api-management-howto-use-azure-monitor/apim-monitor-metrics.png" alt-text="Zrzut ekranu metryk w API Management przegląd":::

Aby uzyskać dostęp do metryk:

1. W [Azure Portal](https://portal.azure.com)przejdź do wystąpienia API Management. Na stronie **Przegląd** zapoznaj się z kluczowymi metrykami dotyczącymi interfejsów API.
1. Aby szczegółowo zbadać metryki, wybierz pozycję **metryki** z menu w dolnej części strony.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-metrics-blade.png" alt-text="Zrzut ekranu przedstawiający element metryk w menu monitorowanie":::

1. Z listy rozwijanej wybierz interesujące Cię metryki. Na przykład **żądania**. 
1. Wykres przedstawia łączną liczbę wywołań interfejsu API.
1. Wykres może być filtrowany przy użyciu wymiarów metryk **żądań** . Na przykład wybierz pozycję **Dodaj filtr** , wybierz **kategorię kod odpowiedzi zaplecza** , wprowadź 500 jako wartość. Teraz wykres pokazuje liczbę żądań, które zakończyły się niepowodzeniem w zapleczu interfejsu API.   

## <a name="set-up-an-alert-rule"></a>Konfigurowanie reguły alertu 

[Alerty](../azure-monitor/platform/alerts-metric-overview.md) można odbierać w oparciu o metryki i dzienniki aktywności. Azure Monitor umożliwia [skonfigurowanie alertu](../azure-monitor/platform/alerts-metric.md) w celu wykonania następujących czynności podczas wyzwalania:

* Wysyłanie powiadomienia e-mail
* Wywołanie elementu webhook
* Wywołanie aplikacji logiki platformy Azure

Aby skonfigurować przykładową regułę alertu na podstawie metryki żądania:

1. W [Azure Portal](https://portal.azure.com)przejdź do wystąpienia API Management.
1. Wybierz pozycję **alerty** na pasku menu w dolnej części strony.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/alert-menu-item.png" alt-text="Zrzut ekranu opcji alertów w menu monitorowanie":::

1. Wybierz pozycję **+ Nowa reguła alertu**.
1. W oknie **Tworzenie reguły alertu** **Wybierz pozycję warunek**.
1. W oknie **Konfigurowanie logiki sygnału** :
    1. W polu **Typ sygnału** wybierz pozycję **metryki**.
    1. W polu **Nazwa sygnału** wybierz pozycję **żądania**.
    1. W obszarze **Podziel według wymiarów** w polu **Nazwa wymiaru** wybierz **kategorię kod odpowiedzi bramy**.
    1. W obszarze **wartości wymiaru** wybierz pozycję **4xx** dla błędów klienta, takich jak nieautoryzowane lub nieprawidłowe żądania.
    1. W obszarze **logika alertu** Określ próg, po którym alert powinien zostać wyzwolony, a następnie wybierz pozycję **gotowe**.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/threshold.png" alt-text="Zrzut ekranu przedstawiający Konfigurowanie logiki sygnałów systemu Windows":::

1. Wybierz istniejącą grupę akcji lub Utwórz nową. W poniższym przykładzie zostanie utworzona nowa grupa akcji. Wiadomość e-mail z powiadomieniem zostanie wysłana do admin@contoso.com . 

    :::image type="content" source="media/api-management-howto-use-azure-monitor/action-details.png" alt-text="Zrzut ekranu przedstawiający powiadomienia dla nowej grupy akcji":::

1. Wprowadź nazwę i opis reguły alertu, a następnie wybierz poziom ważności. 
1. Wybierz pozycję **Utwórz regułę alertu**.
1. Teraz Przetestuj regułę alertu, wywołując interfejs API konferencji bez klucza interfejsu API. Na przykład:

    ```bash
    curl GET https://apim-hello-world.azure-api.net/conference/speakers HTTP/1.1 
    ```

    Alert zostanie wyzwolony w oparciu o okres próbny, a wiadomość e-mail zostanie wysłana do admin@contoso.com . 

    Alerty są również wyświetlane na stronie **alerty** dla wystąpienia API Management.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/portal-alerts.png" alt-text="Zrzut ekranu alertów w portalu":::

## <a name="activity-logs"></a>Dzienniki aktywności

Dzienniki aktywności udostępniają szczegółowe dane operacji wykonywanych w stosunku do usług API Management. Korzystając z tych dzienników, można określić rodzaj, użytkownika i czas każdej operacji zapisu (PUT, POST, DELETE) wykonanej względem usług API Management.

> [!NOTE]
> Dzienniki aktywności nie obejmują operacji odczytu (GET) ani operacji wykonywanych w witrynie Azure Portal albo przy użyciu oryginalnych interfejsów API zarządzania.

Dostęp do dzienników aktywności można uzyskać w usłudze API Management, a dostęp do wszystkich zasobów platformy Azure — w usłudze Azure Monitor. 

:::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs.png" alt-text="Zrzut ekranu przedstawiający dziennik aktywności w portalu":::

Aby wyświetlić dziennik aktywności:

1. W [Azure Portal](https://portal.azure.com)przejdź do wystąpienia API Management.

1. Wybierz pozycję **Dziennik aktywności**.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs-blade.png" alt-text="Zrzut ekranu przedstawiający element dziennika aktywności w menu monitorowanie":::
1. Wybierz żądany zakres filtrowania, a następnie **Zastosuj**.

## <a name="resource-logs"></a>Dzienniki zasobów

Dzienniki zasobów zawierają bogate informacje o operacjach i błędach, które są ważne do celów inspekcji, a także do rozwiązywania problemów. Dzienniki zasobów różnią się od dzienników aktywności. Dziennik aktywności zawiera szczegółowe informacje o operacjach wykonywanych na zasobach platformy Azure. Dzienniki zasobów zapewniają wgląd w operacje wykonywane przez zasób.

Aby skonfigurować dzienniki zasobów:

1. W [Azure Portal](https://portal.azure.com)przejdź do wystąpienia API Management.
2. Wybierz pozycję **Ustawienia diagnostyczne**.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-diagnostic-logs-blade.png" alt-text="Zrzut ekranu przedstawiający element ustawień diagnostycznych w menu monitorowanie":::

1. Wybierz pozycję **+ Dodaj ustawienie diagnostyczne**.
1. Wybierz dzienniki lub metryki, które chcesz zebrać.

   Dzienniki zasobów oraz metryki można archiwizować na koncie magazynu, przesyłać strumieniowo do centrum zdarzeń lub wysyłać do obszaru roboczego Log Analytics. 

Aby uzyskać więcej informacji, zobacz [Tworzenie ustawień diagnostycznych w celu wysyłania dzienników platformy i metryk do różnych miejsc docelowych](../azure-monitor/platform/diagnostic-settings.md).

## <a name="view-diagnostic-data-in-azure-monitor"></a>Wyświetlanie danych diagnostycznych w Azure Monitor

Włączenie kolekcji GatewayLogs lub metryk w obszarze roboczym Log Analytics może potrwać kilka minut, aby dane były widoczne w Azure Monitor. Aby wyświetlić dane:

1. W [Azure Portal](https://portal.azure.com)przejdź do wystąpienia API Management.
1. Wybierz pozycję **dzienniki** z menu w dolnej części strony.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/logs-menu-item.png" alt-text="Zrzut ekranu przedstawiający element Logs w menu monitorowanie":::

Uruchom zapytania, aby wyświetlić dane. Dostępne są kilka [przykładowych zapytań](../azure-monitor/log-query/saved-queries.md) lub własne. Na przykład następujące zapytanie pobiera ostatnie 24 godziny danych z tabeli GatewayLogs:

```kusto
ApiManagementGatewayLogs
| where TimeGenerated > ago(1d) 
```

Aby uzyskać więcej informacji o korzystaniu z dzienników zasobów dla API Management, zobacz:

* [Rozpocznij pracę z Azure Monitor Log Analytics](../azure-monitor/log-query/get-started-portal.md)lub wypróbuj [środowisko demonstracyjne log Analytics](https://portal.loganalytics.io/demo).

* [Przegląd zapytań dzienników w Azure monitor](../azure-monitor/log-query/log-query-overview.md).

Poniższy kod JSON wskazuje przykładowy wpis w GatewayLogs dla pomyślnego żądania interfejsu API. Aby uzyskać szczegółowe informacje, zobacz [Informacje o schemacie](gateway-log-schema-reference.md). 

```json
{
    "Level": 4,
    "isRequestSuccess": true,
    "time": "2020-10-14T17:xx:xx.xx",
    "operationName": "Microsoft.ApiManagement/GatewayLogs",
    "category": "GatewayLogs",
    "durationMs": 152,
    "callerIpAddress": "xx.xx.xxx.xx",
    "correlationId": "3f06647e-xxxx-xxxx-xxxx-530eb9f15261",
    "location": "East US",
    "properties": {
        "method": "GET",
        "url": "https://apim-hello-world.azure-api.net/conference/speakers",
        "backendResponseCode": 200,
        "responseCode": 200,
        "responseSize": 41583,
        "cache": "none",
        "backendTime": 87,
        "requestSize": 526,
        "apiId": "demo-conference-api",
        "operationId": "GetSpeakers",
        "apimSubscriptionId": "master",
        "clientTime": 65,
        "clientProtocol": "HTTP/1.1",
        "backendProtocol": "HTTP/1.1",
        "apiRevision": "1",
        "clientTlsVersion": "1.2",
        "backendMethod": "GET",
        "backendUrl": "https://conferenceapi.azurewebsites.net/speakers"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group>/PROVIDERS/MICROSOFT.APIMANAGEMENT/SERVICE/APIM-HELLO-WORLD"
}
```

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wyświetlanie metryk interfejsu API
> * Konfigurowanie reguły alertu 
> * Wyświetlanie dzienników aktywności
> * Włączanie i wyświetlanie dzienników zasobów


Przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Śledzenie wywołań](api-management-howto-api-inspector.md)
