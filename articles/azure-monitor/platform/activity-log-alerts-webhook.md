---
title: Informacje o schemacie elementu webhook używanym w alertach dziennika aktywności
description: Informacje o schemacie JSON, który jest publikowany w adresie URL elementu webhook po aktywowaniu alertu dziennika aktywności.
ms.topic: conceptual
ms.date: 03/31/2017
ms.subservice: alerts
ms.openlocfilehash: a73ab12d1729acba132aeffd4104ca7846ecb9e8
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901440"
---
# <a name="webhooks-for-azure-activity-log-alerts"></a>Elementy webhook dla alertów dziennika aktywności platformy Azure
W ramach definicji grupy akcji można skonfigurować punkty końcowe elementu webhook, aby otrzymywać powiadomienia o alertach dziennika aktywności. Za pomocą elementów webhook można kierować te powiadomienia do innych systemów na potrzeby akcji wykonywanych po przetworzeniu lub w niestandardowym. W tym artykule przedstawiono sposób, w jaki zostanie wyświetlony ładunek dla wpisu HTTP dla elementu webhook.

Aby uzyskać więcej informacji na temat alertów dziennika aktywności, zobacz jak [utworzyć alerty dziennika aktywności platformy Azure](activity-log-alerts.md).

Aby uzyskać informacje na temat grup akcji, zobacz How to [Create Action Groups](./action-groups.md).

> [!NOTE]
> Można również użyć [typowego schematu alertu](./alerts-common-schema.md), który umożliwia korzystanie z jednego rozszerzalnego i ujednoliconego ładunku alertów dla wszystkich usług alertów w Azure monitor, dla integracji elementów webhook. [Dowiedz się więcej na temat typowych definicji schematu alertów.](./alerts-common-schema-definitions.md)


## <a name="authenticate-the-webhook"></a>Uwierzytelnianie elementu webhook
Element webhook może opcjonalnie użyć autoryzacji opartej na tokenach na potrzeby uwierzytelniania. Identyfikator URI elementu webhook jest zapisywany z IDENTYFIKATORem tokenu, na przykład `https://mysamplealert/webcallback?tokenid=sometokenid&someparameter=somevalue` .

## <a name="payload-schema"></a>Schemat ładunku
Ładunek JSON zawarty w operacji POST różni się w zależności od pola Data. Context. activityLog. eventSource.

> [!NOTE]
> Obecnie opis, który jest częścią zdarzenia dziennika aktywności, jest kopiowany do wygenerowanej właściwości **"Opis alertu"** .
>
> Aby można było wyrównać ładunek dziennika aktywności z innymi typami alertów, od 1 kwietnia 2021 Właściwość wywołanego alertu będzie **zawierać opis reguły** alertu.
>
> W przygotowaniu do tej zmiany utworzyliśmy nową właściwość **"Opis zdarzenia dziennika aktywności"** w dzienniku aktywności. Ta nowa właściwość zostanie wypełniona właściwością **"Description"** , która jest już dostępna do użycia. Oznacza to, że nowe pole **"Opis zdarzenia dziennika aktywności"** będzie zawierać opis, który jest częścią zdarzenia dziennika aktywności.
>
> Zapoznaj się z regułami alertów, regułami akcji, elementami webhook, aplikacją logiki lub innymi konfiguracjami, w których można użyć właściwości **"Description"** z wywoływanego alertu i zastąp go właściwością **"Opis zdarzenia dziennika aktywności"** .
>
> Jeśli warunek (w regułach akcji, elementach webhook, aplikacja logiki lub inne konfiguracje) jest obecnie oparty na właściwości **"Description"** dla alertów dziennika aktywności, może być konieczne zmodyfikowanie go na podstawie właściwości **"Opis zdarzenia dziennika aktywności"** .
>
> Aby wypełnić nową właściwość **"Description"** , można dodać opis w definicji reguły alertu.

### <a name="common"></a>Wspólne

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Operation",
                "correlationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "eventSource": "Administrative",
                "eventTimestamp": "2017-03-29T15:43:08.0019532+00:00",
                "eventDataId": "8195a56a-85de-4663-943e-1a2bf401ad94",
                "level": "Informational",
                "operationName": "Microsoft.Insights/actionGroups/write",
                "operationId": "6ac88262-43be-4adf-a11c-bd2179852898",
                "status": "Started",
                "subStatus": "",
                "subscriptionId": "52c65f65-0518-4d37-9719-7dbbfc68c57a",
                "submissionTimestamp": "2017-03-29T15:43:20.3863637+00:00",
                ...
            }
        },
        "properties": {}
    }
}
```

### <a name="administrative"></a>Administracyjny

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "authorization": {
                    "action": "Microsoft.Insights/actionGroups/write",
                    "scope": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions"
                },
                "claims": "{...}",
                "caller": "me@contoso.com",
                "description": "",
                "httpRequest": "{...}",
                "resourceId": "/subscriptions/52c65f65-0518-4d37-9719-7dbbfc68c57b/resourceGroups/CONTOSO-TEST/providers/Microsoft.Insights/actionGroups/IncidentActions",
                "resourceGroupName": "CONTOSO-TEST",
                "resourceProviderName": "Microsoft.Insights",
                "resourceType": "Microsoft.Insights/actionGroups"
            }
        },
        "properties": {}
    }
}
```

### <a name="security"></a>Zabezpieczenia

```json
{
    "schemaId":"Microsoft.Insights/activityLogs",
    "data":{"status":"Activated",
        "context":{
            "activityLog":{
                "channels":"Operation",
                "correlationId":"2518408115673929999",
                "description":"Failed SSH brute force attack. Failed brute force attacks were detected from the following attackers: [\"IP Address: 01.02.03.04\"].  Attackers were trying to access the host with the following user names: [\"root\"].",
                "eventSource":"Security",
                "eventTimestamp":"2017-06-25T19:00:32.607+00:00",
                "eventDataId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "level":"Informational",
                "operationName":"Microsoft.Security/locations/alerts/activate/action",
                "operationId":"Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "properties":{
                    "attackers":"[\"IP Address: 01.02.03.04\"]",
                    "numberOfFailedAuthenticationAttemptsToHost":"456",
                    "accountsUsedOnFailedSignInToHostAttempts":"[\"root\"]",
                    "wasSSHSessionInitiated":"No","endTimeUTC":"06/25/2017 19:59:39",
                    "actionTaken":"Detected",
                    "resourceType":"Virtual Machine",
                    "severity":"Medium",
                    "compromisedEntity":"LinuxVM1",
                    "remediationSteps":"[In case this is an Azure virtual machine, add the source IP to NSG block list for 24 hours (see https://azure.microsoft.com/documentation/articles/virtual-networks-nsg/)]",
                    "attackedResourceType":"Virtual Machine"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/Microsoft.Security/locations/centralus/alerts/Sec-07f2-4d74-aaf0-03d2f53d5a33",
                "resourceGroupName":"contoso",
                "resourceProviderName":"Microsoft.Security",
                "status":"Active",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-25T20:23:04.9743772+00:00",
                "resourceType":"MICROSOFT.SECURITY/LOCATIONS/ALERTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="recommendation"></a>Zalecenie

```json
{
    "schemaId":"Microsoft.Insights/activityLogs",
    "data":{
        "status":"Activated",
        "context":{
            "activityLog":{
                "channels":"Operation",
                "claims":"{\"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress\":\"Microsoft.Advisor\"}",
                "caller":"Microsoft.Advisor",
                "correlationId":"123b4c54-11bb-3d65-89f1-0678da7891bd",
                "description":"A new recommendation is available.",
                "eventSource":"Recommendation",
                "eventTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "httpRequest":"{\"clientIpAddress\":\"0.0.0.0\"}",
                "eventDataId":"1bf234ef-e45f-4567-8bba-fb9b0ee1dbcb",
                "level":"Informational",
                "operationName":"Microsoft.Advisor/recommendations/available/action",
                "properties":{
                    "recommendationSchemaVersion":"1.0",
                    "recommendationCategory":"HighAvailability",
                    "recommendationImpact":"Medium",
                    "recommendationName":"Enable Soft Delete to protect your blob data",
                    "recommendationResourceLink":"https://portal.azure.com/#blade/Microsoft_Azure_Expert/RecommendationListBlade/recommendationTypeId/12dbf883-5e4b-4f56-7da8-123b45c4b6e6",
                    "recommendationType":"12dbf883-5e4b-4f56-7da8-123b45c4b6e6"
                },
                "resourceId":"/subscriptions/12345-5645-123a-9867-123b45a6789/resourceGroups/contoso/providers/microsoft.storage/storageaccounts/contosoStore",
                "resourceGroupName":"CONTOSO",
                "resourceProviderName":"MICROSOFT.STORAGE",
                "status":"Active",
                "subStatus":"",
                "subscriptionId":"12345-5645-123a-9867-123b45a6789",
                "submissionTimestamp":"2017-06-29T13:52:33.2742943+00:00",
                "resourceType":"MICROSOFT.STORAGE/STORAGEACCOUNTS"
            }
        },
        "properties":{}
    }
}
```

### <a name="servicehealth"></a>Servicehealth

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
            "channels": "Admin",
            "correlationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "description": "Active: Virtual Machines - Australia East",
            "eventSource": "ServiceHealth",
            "eventTimestamp": "2017-10-18T23:49:25.3736084+00:00",
            "eventDataId": "6fa98c0f-334a-b066-1934-1a4b3d929856",
            "level": "Informational",
            "operationName": "Microsoft.ServiceHealth/incident/action",
            "operationId": "bbac944f-ddc0-4b4c-aa85-cc7dc5d5c1a6",
            "properties": {
                "title": "Virtual Machines - Australia East",
                "service": "Virtual Machines",
                "region": "Australia East",
                "communication": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "incidentType": "Incident",
                "trackingId": "0NIH-U2O",
                "impactStartTime": "2017-10-18T02:48:00.0000000Z",
                "impactedServices": "[{\"ImpactedRegions\":[{\"RegionName\":\"Australia East\"}],\"ServiceName\":\"Virtual Machines\"}]",
                "defaultLanguageTitle": "Virtual Machines - Australia East",
                "defaultLanguageContent": "Starting at 02:48 UTC on 18 Oct 2017 you have been identified as a customer using Virtual Machines in Australia East who may receive errors starting Dv2 Promo and DSv2 Promo Virtual Machines which are in a stopped &quot;deallocated&quot; or suspended state. Customers can still provision Dv1 and Dv2 series Virtual Machines or try deploying Virtual Machines in other regions, as a possible workaround. Engineers have identified a possible fix for the underlying cause, and are exploring implementation options. The next update will be provided as events warrant.",
                "stage": "Active",
                "communicationId": "636439673646212912",
                "version": "0.1.1"
            },
            "status": "Active",
            "subscriptionId": "45529734-0ed9-4895-a0df-44b59a5a07f9",
            "submissionTimestamp": "2017-10-18T23:49:28.7864349+00:00"
        }
    },
    "properties": {}
    }
}
```

Aby uzyskać szczegółowe informacje dotyczące schematu alertów dziennika aktywności powiadomień o kondycji usługi, zobacz [powiadomienia dotyczące kondycji usługi](../../service-health/service-notifications.md). Ponadto Dowiedz się, jak [skonfigurować powiadomienia elementu webhook usługi Service Health z istniejącymi rozwiązaniami do zarządzania problemami](../../service-health/service-health-alert-webhook-guide.md).

### <a name="resourcehealth"></a>ResourceHealth

```json
{
    "schemaId": "Microsoft.Insights/activityLogs",
    "data": {
        "status": "Activated",
        "context": {
            "activityLog": {
                "channels": "Admin, Operation",
                "correlationId": "a1be61fd-37ur-ba05-b827-cb874708babf",
                "eventSource": "ResourceHealth",
                "eventTimestamp": "2018-09-04T23:09:03.343+00:00",
                "eventDataId": "2b37e2d0-7bda-4de7-ur8c6-1447d02265b2",
                "level": "Informational",
                "operationName": "Microsoft.Resourcehealth/healthevent/Activated/action",
                "operationId": "2b37e2d0-7bda-489f-81c6-1447d02265b2",
                "properties": {
                    "title": "Virtual Machine health status changed to unavailable",
                    "details": "Virtual machine has experienced an unexpected event",
                    "currentHealthStatus": "Unavailable",
                    "previousHealthStatus": "Available",
                    "type": "Downtime",
                    "cause": "PlatformInitiated"
                },
                "resourceId": "/subscriptions/<subscription Id>/resourceGroups/<resource group>/providers/Microsoft.Compute/virtualMachines/<resource name>",
                "resourceGroupName": "<resource group>",
                "resourceProviderName": "Microsoft.Resourcehealth/healthevent/action",
                "status": "Active",
                "subscriptionId": "<subscription Id>",
                "submissionTimestamp": "2018-09-04T23:11:06.1607287+00:00",
                "resourceType": "Microsoft.Compute/virtualMachines"
            }
        }
    }
}
```

| Nazwa elementu | Opis |
| --- | --- |
| status |Używane na potrzeby alertów dotyczących metryk. Zawsze ustawione na "aktywowane" dla alertów dziennika aktywności. |
| kontekst |Kontekst zdarzenia. |
| resourceProviderName |Dostawca zasobów zasobu, którego to dotyczy. |
| warunektype |Zawsze "zdarzenie". |
| name |Nazwa reguły alertu. |
| identyfikator |Identyfikator zasobu alertu. |
| description |Opis alertu ustawiany podczas tworzenia alertu. |
| subscriptionId |Identyfikator subskrypcji platformy Azure. |
| sygnatura czasowa |Godzina, o której zdarzenie zostało wygenerowane przez usługę platformy Azure, która przetworzyła żądanie. |
| resourceId |Identyfikator zasobu zasobu, którego dotyczy problem. |
| resourceGroupName |Nazwa grupy zasobów dla zasobu, którego dotyczy problem. |
| properties |Zestaw `<Key, Value>` par (czyli `Dictionary<String, String>` ), który zawiera szczegółowe informacje o zdarzeniu. |
| event |Element, który zawiera metadane dotyczące zdarzenia. |
| autoryzacja |Właściwości kontroli dostępu opartej na rolach platformy Azure dla zdarzenia. Te właściwości zazwyczaj obejmują akcję, rolę i zakres. |
| category |Kategoria zdarzenia. Obsługiwane wartości to: administracyjne, alert, zabezpieczenia, servicehealth i rekomendacja. |
| Obiekt wywołujący |Adres e-mail użytkownika, który wykonał operację, oświadczenie nazwy UPN lub oświadczenie SPN na podstawie dostępności. Może mieć wartość null w przypadku niektórych wywołań systemowych. |
| correlationId |Zazwyczaj identyfikator GUID w formacie ciągu. Zdarzenia z identyfikatorem korelacji należy do tej samej większej akcji i zwykle współużytkują identyfikator korelacji. |
| eventDescription |Tekst statyczny opisu zdarzenia. |
| eventDataId |Unikatowy identyfikator zdarzenia. |
| eventSource |Nazwa usługi lub infrastruktury platformy Azure, która wygenerowała zdarzenie. |
| httpRequest |Żądanie zwykle obejmuje metodę identyfikatorem żądania klienta, clientIpAddress i HTTP (na przykład PUT). |
| poziom |Jedna z następujących wartości: krytyczne, błąd, ostrzeżenie i informacje. |
| operationId |Zazwyczaj identyfikator GUID współużytkowany przez zdarzenia odpowiadające pojedynczej operacji. |
| operationName |Nazwa operacji. |
| properties |Właściwości zdarzenia. |
| status |Ciąg. Stan operacji. Wspólne wartości obejmują rozpoczęte, w toku, zakończone powodzeniem, zakończone niepowodzeniem, aktywne i rozwiązane. |
| subStatus |Zwykle zawiera kod stanu HTTP odpowiadającego wywołania REST. Może również zawierać inne ciągi opisujące podstan. Typowe wartości Substatus to OK (kod stanu HTTP: 200), utworzony (kod stanu HTTP: 201), zaakceptowano (kod stanu HTTP: 202), brak zawartości (kod stanu HTTP: 204), niewłaściwe żądanie (kod stanu HTTP: 400), nie znaleziono (kod stanu http: 404), konflikt (kod stanu usługi http: 409), wewnętrzny błąd serwera (kod stanu http: 500), Usługa niedostępna (kod stanu http: 503) i limit czasu bramy : 504). |

Szczegółowe informacje o schemacie wszystkich innych alertów dziennika aktywności można znaleźć w temacie [Omówienie dziennika aktywności platformy Azure](./platform-logs-overview.md).

## <a name="next-steps"></a>Następne kroki
* [Dowiedz się więcej o dzienniku aktywności](./platform-logs-overview.md).
* [Wykonaj skrypty usługi Azure Automation (elementy Runbook) w alertach platformy Azure](https://go.microsoft.com/fwlink/?LinkId=627081).
* [Użyj aplikacji logiki, aby wysłać wiadomość SMS za pośrednictwem Twilio z alertu platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app). Ten przykład dotyczy alertów metryk, ale można go zmodyfikować do pracy z alertem dziennika aktywności.
* [Użyj aplikacji logiki do wysłania komunikatu o zapasach z alertu platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app). Ten przykład dotyczy alertów metryk, ale można go zmodyfikować do pracy z alertem dziennika aktywności.
* [Użyj aplikacji logiki, aby wysłać wiadomość do kolejki platformy Azure z poziomu alertu platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app). Ten przykład dotyczy alertów metryk, ale można go zmodyfikować do pracy z alertem dziennika aktywności.