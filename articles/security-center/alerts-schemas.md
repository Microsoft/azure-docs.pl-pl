---
title: Schematy alertów Azure Security Center
description: W tym artykule opisano różne schematy używane przez Azure Security Center na potrzeby alertów zabezpieczeń.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/19/2020
ms.author: memildin
ms.openlocfilehash: 55f8d37d435aa8adeb4d97246ce7b2c7811140be
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558002"
---
# <a name="security-alerts-schemas"></a>Schematy alertów zabezpieczeń

Jeśli subskrypcja ma włączoną usługę Azure Defender, otrzymasz alerty zabezpieczeń, gdy Security Center wykryje zagrożenia dla swoich zasobów.

Te alerty zabezpieczeń można wyświetlić na stronach Azure Security Center **ochrony przed zagrożeniami** lub za poorednictwem narzędzi zewnętrznych, takich jak:

- [Wskaźnik platformy Azure](../sentinel/index.yml) — natywny Siem w chmurze firmy Microsoft. Łącznik wskaźnikowego otrzymuje alerty z Azure Security Center i wysyła je do [obszaru roboczego log Analytics](../azure-monitor/logs/quick-create-workspace.md) dla platformy Azure.
- Rozwiązań Siem innych firm — Wysyłaj dane do [usługi Azure Event Hubs](../event-hubs/index.yml). Następnie Zintegruj dane centrum zdarzeń z SIEMem innej firmy. Dowiedz się więcej w temacie [alerty przesyłania strumieniowego do rozwiązania do zarządzania usługami Siem, o i IT](export-to-siem.md).
- [Interfejs API REST](/rest/api/securitycenter/) — Jeśli używasz interfejsu API REST do uzyskiwania dostępu do alertów, zobacz [dokumentację interfejsu API alertów online](/rest/api/securitycenter/alerts).

Jeśli używasz jakichkolwiek metod programistycznych do korzystania z alertów, potrzebujesz poprawnego schematu, aby znaleźć pola, które są dla Ciebie istotne. Ponadto, Jeśli eksportujesz do centrum zdarzeń lub próbujesz wyzwolić automatyzację przepływu pracy za pomocą ogólnych łączników HTTP, użyj schematów, aby prawidłowo przeanalizować obiekty JSON.

>[!IMPORTANT]
> Schemat jest nieco różny dla każdego z tych scenariuszy, dlatego upewnij się, że wybrano odpowiednią kartę poniżej.


## <a name="the-schemas"></a>Schematy 


### <a name="workflow-automation-and-continuous-export-to-event-hub"></a>[Automatyzacja przepływu pracy i eksport ciągły do centrum zdarzeń](#tab/schema-continuousexport)

### <a name="sample-json-for-alerts-sent-to-logic-apps-event-hub-and-third-party-siems"></a>Przykładowy kod JSON dla alertów wysyłanych do Logic Apps, centrum zdarzeń i rozwiązań Siem innych firm

Poniżej znajdziesz schemat zdarzeń alertów przesłanych do:

- Wystąpienia aplikacji logiki platformy Azure, które zostały skonfigurowane w automatyzacji przepływu pracy Security Center
- Centrum zdarzeń platformy Azure korzystające z funkcji ciągłego eksportu Security Center

Aby uzyskać więcej informacji na temat funkcji automatyzacji przepływu pracy, zobacz [Automatyzowanie odpowiedzi na wyzwalacze Security Center](workflow-automation.md).

Aby uzyskać więcej informacji na temat eksportu ciągłego, zobacz [ciągły eksport danych Security Center](continuous-export.md).

[!INCLUDE [Workflow schema](../../includes/security-center-alerts-schema-workflow-automation.md)]




### <a name="azure-sentinel-and-log-analytics-workspaces"></a>[Obszary robocze Azure — wskaźnik i Log Analytics](#tab/schema-sentinel)

Łącznik wskaźnikowego otrzymuje alerty z Azure Security Center i wysyła je do obszaru roboczego Log Analytics dla platformy Azure. 

Aby utworzyć przypadek wskaźnikowy lub zdarzenie przy użyciu alertów Security Center, będzie potrzebny schemat dla tych alertów przedstawionych poniżej. 

Aby uzyskać więcej informacji na temat platformy Azure, zobacz [dokumentację](../sentinel/index.yml).

[!INCLUDE [Sentinel and workspace schema](../../includes/security-center-alerts-schema-log-analytics-workspace.md)]




### <a name="azure-activity-log"></a>[Dziennik aktywności platformy Azure](#tab/schema-activitylog)

Azure Security Center inspekcji wygenerowały alerty zabezpieczeń jako zdarzenia w dzienniku aktywności platformy Azure.

Zdarzenia alertów zabezpieczeń można wyświetlić w dzienniku aktywności przez wyszukanie zdarzenia Aktywuj alert, jak pokazano poniżej:

[![Wyszukiwanie w dzienniku aktywności zdarzenia aktywacji alertu](media/alerts-schemas/sample-activity-log-alert.png)](media/alerts-schemas/sample-activity-log-alert.png#lightbox)


### <a name="sample-json-for-alerts-sent-to-azure-activity-log"></a>Przykładowy kod JSON dla alertów wysyłanych do dziennika aktywności platformy Azure

```json
{
    "channels": "Operation",
    "correlationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "description": "PREVIEW - Role binding to the cluster-admin role detected. Kubernetes audit log analysis detected a new binding to the cluster-admin role which gives administrator privileges.\r\nUnnecessary administrator privileges might cause privilege escalation in the cluster.",
    "eventDataId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "eventName": {
        "value": "PREVIEW - Role binding to the cluster-admin role detected",
        "localizedValue": "PREVIEW - Role binding to the cluster-admin role detected"
    },
    "category": {
        "value": "Security",
        "localizedValue": "Security"
    },
    "eventTimestamp": "2019-12-25T18:52:36.801035Z",
    "id": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/events/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff/ticks/637128967568010350",
    "level": "Informational",
    "operationId": "2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "operationName": {
        "value": "Microsoft.Security/locations/alerts/activate/action",
        "localizedValue": "Activate Alert"
    },
    "resourceGroupName": "RESOURCE_GROUP_NAME",
    "resourceProviderName": {
        "value": "Microsoft.Security",
        "localizedValue": "Microsoft.Security"
    },
    "resourceType": {
        "value": "Microsoft.Security/locations/alerts",
        "localizedValue": "Microsoft.Security/locations/alerts"
    },
    "resourceId": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RESOURCE_GROUP_NAME/providers/Microsoft.Security/locations/centralus/alerts/2518250008431989649_e7313e05-edf4-466d-adfd-35974921aeff",
    "status": {
        "value": "Active",
        "localizedValue": "Active"
    },
    "subStatus": {
        "value": "",
        "localizedValue": ""
    },
    "submissionTimestamp": "2019-12-25T19:14:03.5507487Z",
    "subscriptionId": "SUBSCRIPTION_ID",
    "properties": {
        "clusterRoleBindingName": "cluster-admin-binding",
        "subjectName": "for-binding-test",
        "subjectKind": "ServiceAccount",
        "username": "masterclient",
        "actionTaken": "Detected",
        "resourceType": "Kubernetes Service",
        "severity": "Low",
        "intent": "[\"Persistence\"]",
        "compromisedEntity": "ASC-IGNITE-DEMO",
        "remediationSteps": "[\"Review the user in the alert details. If cluster-admin is unnecessary for this user, consider granting lower privileges to the user.\"]",
        "attackedResourceType": "Kubernetes Service"
    },
    "relatedEvents": []
}
```

### <a name="the-data-model-of-the-schema"></a>Model danych schematu

|Pole|Opis|
|----|----|
|**dyplomatyczn**|Stała, "operacja"|
|**korelacj**|Identyfikator alertu Azure Security Center|
|**zharmonizowan**|Opis alertu|
|**eventDataId**|Zobacz identyfikator korelacji|
|**eventName**|Pola wartości i localizedValue zawierają nazwę wyświetlaną alertu|
|**kategorii**|Pola wartości i localizedValue są stałe-"Security"|
|**eventTimestamp**|Sygnatura czasowa UTC dla momentu wygenerowania alertu|
|**id**|W pełni kwalifikowany identyfikator alertu|
|**poziom**|Stała, "informacyjna"|
|**operationId**|Zobacz identyfikator korelacji|
|**operationName**|Wartością pola jest stała — "Microsoft. Security/Locations/Alerts/Activate/Action", a zlokalizowana wartość będzie "Activate Alert" (może być możliwe zlokalizowanie wartości nominalnej dla użytkownika)|
|**resourceGroupName**|Będzie zawierać nazwę grupy zasobów|
|**resourceProviderName**|Pola wartości i localizedValue są stałe — "Microsoft. Security"|
|**resourceType**|Pola wartości i localizedValue są stałe — "Microsoft. Security/Locations/Alerts"|
|**Identyfikator**|W pełni kwalifikowany identyfikator zasobu platformy Azure|
|**Stany**|Pola wartości i localizedValue są stałe — "aktywne"|
|**subStatus**|Wartości i podpola localizedValue są puste|
|**submissionTimestamp**|Sygnatura czasowa UTC zdarzenia przesłania do dziennika aktywności|
|**subscriptionId**|Identyfikator subskrypcji zasobu, którego bezpieczeństwo zostało naruszone|
|**aœciwoœci**|Zbiór w formacie JSON z dodatkowymi właściwościami odnoszącymi się do alertu. Mogą one zmieniać się z jednego alertu do drugiego, jednak następujące pola zostaną wyświetlone w obszarze wszystkie alerty:<br>-ważność: ważność ataku<br>-compromisedEntity: nazwa zasobu, którego bezpieczeństwo zostało naruszone<br>-remediationSteps: tablica kroków korygowania do wykonania<br>-Cel: zamierzenie łańcucha kasowania dla alertu. Możliwe intencje są udokumentowane w [tabeli zamiarów](alerts-reference.md#intentions)|
|**relatedEvents**|Stała — pusta tablica|
|||





### <a name="ms-graph-api"></a>[interfejs API programu Graph MS](#tab/schema-graphapi)

Microsoft Graph to brama do danych i analizy w Microsoft 365. Zapewnia jednolity model programowania, którego można użyć w celu uzyskania dostępu do ogromnej ilości danych w Microsoft 365, Windows 10 i Enterprise Mobility + Security. Skorzystaj z bogactwa danych w Microsoft Graph, aby tworzyć aplikacje dla organizacji i klientów, którzy współpracują z milionami użytkowników.

Schemat i reprezentację JSON alertów zabezpieczeń wysyłanych do programu MS Graph są dostępne w [dokumentacji Microsoft Graph](/graph/api/resources/alert).

---


## <a name="next-steps"></a>Następne kroki

W tym artykule opisano schematy, które są używane przez narzędzia do ochrony przed zagrożeniami Azure Security Center podczas wysyłania informacji o alertach zabezpieczeń.

Aby uzyskać więcej informacji na temat sposobów uzyskiwania dostępu do alertów zabezpieczeń spoza Security Center, zobacz następujące strony:

- [Wskaźnik platformy Azure](../sentinel/index.yml) — natywny Siem w chmurze firmy Microsoft
- [Azure Event Hubs](../event-hubs/index.yml) — w pełni zarządzana usługa pozyskiwania danych w czasie rzeczywistym firmy Microsoft
- [Ciągłe eksportowanie danych Security Center](continuous-export.md)
- [Log Analytics obszary robocze](../azure-monitor/logs/quick-create-workspace.md) — Azure monitor przechowuje dane dziennika w log Analytics obszarze roboczym, kontener zawierający dane i informacje o konfiguracji