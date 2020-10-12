---
title: Wysyłanie powiadomień Azure Service Health za pośrednictwem elementów webhook
description: Wysyłanie spersonalizowanych powiadomień o zdarzeniach dotyczących kondycji usługi do istniejącego systemu zarządzania problemami.
ms.topic: conceptual
ms.service: service-health
ms.date: 3/27/2018
ms.openlocfilehash: 05b0572c89a29fddc881f9977ee437d1319e6254
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86518932"
---
# <a name="use-a-webhook-to-configure-health-notifications-for-problem-management-systems"></a>Używanie elementu webhook do konfigurowania powiadomień o kondycji dla systemów zarządzania problemami

W tym artykule opisano sposób konfigurowania alertów Azure Service Health w celu wysyłania danych za poorednictwem elementów webhook do istniejącego systemu powiadomień.

Możesz skonfigurować alerty Service Health, aby powiadamiać użytkownika za pomocą wiadomości SMS lub wiadomości e-mail, gdy zdarzenie usługi platformy Azure ma wpływ na użytkownika.

Jednak prawdopodobnie masz już istniejący zewnętrzny system powiadomień, którego wolisz używać. W tym artykule opisano najważniejsze części ładunku elementu webhook. Zawiera opis sposobu tworzenia alertów niestandardowych w celu powiadomienia o wystąpieniu odpowiednich problemów z usługą.

Jeśli chcesz użyć wstępnie skonfigurowanej integracji, zobacz:
* [Konfigurowanie alertów za pomocą usługi ServiceNow](service-health-alert-webhook-servicenow.md)
* [Konfigurowanie alertów za pomocą usługi PagerDuty](service-health-alert-webhook-pagerduty.md)
* [Konfigurowanie alertów za pomocą OpsGenie](service-health-alert-webhook-opsgenie.md)

**Obejrzyj film wprowadzający:**

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUV]

## <a name="configure-a-custom-notification-by-using-the-service-health-webhook-payload"></a>Skonfiguruj niestandardowe powiadomienie przy użyciu Service Health ładunku elementu webhook
Aby skonfigurować własną niestandardową integrację elementów webhook, należy przeanalizować ładunek JSON, który jest wysyłany za pośrednictwem powiadomienia Service Health.

Zobacz [przykładowy](../azure-monitor/platform/activity-log-alerts-webhook.md) `ServiceHealth` ładunek elementu webhook.

Możesz potwierdzić, że jest to alert kondycji usługi, sprawdzając `context.eventSource == "ServiceHealth"` . Najbardziej odpowiednie właściwości są następujące:
- **Data. Context. activityLog. status**
- **Data. Context. activityLog. Level**
- **Data. Context. activityLog. Identyfikator subskrypcji**
- **Data. Context. activityLog. Properties. title**
- **Data. Context. activityLog. Properties. impactStartTime**
- **Data. Context. activityLog. Properties. Communication**
- **Data. Context. activityLog. Properties. impactedServices**
- **Data. Context. activityLog. Properties. trackingId**

## <a name="create-a-link-to-the-service-health-dashboard-for-an-incident"></a>Tworzenie linku do pulpitu nawigacyjnego Service Health dla zdarzenia
Możesz utworzyć bezpośredni link do pulpitu nawigacyjnego Service Health na pulpicie lub urządzeniu przenośnym, generując wyspecjalizowany adres URL. Użyj *trackingId* oraz pierwszych trzech i ostatnich trzech cyfr identyfikatora *subskrypcji* w tym formacie:

https <i></i> ://App.Azure.com/h/* &lt; trackingId &gt; * / * &lt; pierwsze trzy i ostatnie trzy cyfry identyfikatora subskrypcji &gt; *

Na przykład jeśli identyfikator *subskrypcji* to bba14129-e895-429b-8809-278e836ecdb3, a *trackingId* to 0DET-URB, adres URL Service Health to:

https <i></i> ://App.Azure.com/h/0DET-URB/bbadb3

## <a name="use-the-level-to-detect-the-severity-of-the-issue"></a>Użyj poziomu, aby wykryć ważność problemu
Od najmniejszej do najwyższej wagi Właściwość **Level** w ładunku może mieć wartość *informacyjną*, *Ostrzeżenie*, *błąd*lub *krytyczne*.

## <a name="parse-the-impacted-services-to-determine-the-incident-scope"></a>Analizowanie usług, których dotyczy problem, w celu określenia zakresu incydentu
Alerty Service Health mogą informować o problemach w wielu regionach i usługach. Aby uzyskać szczegółowe informacje, należy przeanalizować wartość `impactedServices` .

Zawartość znajdująca się wewnątrz to zmieniony ciąg [JSON](https://json.org/) , który w przypadku braku ucieczki zawiera inny obiekt JSON, który można regularnie analizować. Na przykład:

```json
{"data.context.activityLog.properties.impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"},{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"Alerts & Metrics\"},{\"ImpactedRegions\":[{\"RegionName\":\"Australia Southeast\"}],\"ServiceName\":\"App Service\"}]"}
```

Wartość  zostanie zmieniona na:

```json
[
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia East"
         },
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"Alerts & Metrics"
   },
   {
      "ImpactedRegions":[
         {
            "RegionName":"Australia Southeast"
         }
      ],
      "ServiceName":"App Service"
   }
]
```

Ten przykład przedstawia problemy dotyczące:
- "Alerty & metryki" w Australii wschodniej i Australii Południowo-Wschodnia.
- "App Service" w Australii Południowo-Wschodnia.

## <a name="test-your-webhook-integration-via-an-http-post-request"></a>Przetestuj integrację elementu webhook za pośrednictwem żądania HTTP POST

Wykonaj następujące kroki:

1. Utwórz ładunek kondycji usługi, który chcesz wysłać. Zapoznaj się z przykładowym ładunkiem elementu webhook kondycji usługi w [elementach webhook dla alertów dziennika aktywności platformy Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

1. Utwórz żądanie HTTP POST w następujący sposób:

    ```
    POST        https://your.webhook.endpoint

    HEADERS     Content-Type: application/json

    BODY        <service health payload>
    ```
   Powinna zostać wyświetlona odpowiedź "2XX-powodzenie".

1. Przejdź do [usługi PagerDuty](https://www.pagerduty.com/) , aby upewnić się, że integracja została pomyślnie skonfigurowana.

## <a name="next-steps"></a>Następne kroki
- Przejrzyj [schemat elementu webhook alertu dziennika aktywności](../azure-monitor/platform/activity-log-alerts-webhook.md). 
- Dowiedz się więcej o [powiadomieniach o kondycji usługi](./service-notifications.md).
- Dowiedz się więcej na temat [grup akcji](../azure-monitor/platform/action-groups.md).
