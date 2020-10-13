---
title: Aktualizowanie aplikacji logiki & elementów Runbook dla migracji alertów
description: Dowiedz się, jak modyfikować elementy webhook, Logic Apps i Runbook, aby przygotować się do dobrowolnej migracji.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 03/19/2018
ms.subservice: alerts
ms.openlocfilehash: 9df5d702019063ffba6d79cc63370cd25a7242fd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91358788"
---
# <a name="prepare-your-logic-apps-and-runbooks-for-migration-of-classic-alert-rules"></a>Przygotowywanie aplikacji logiki i elementów Runbook do migracji reguł alertów klasycznych

> [!NOTE]
> Jak [wcześniej ogłoszone](monitoring-classic-retirement.md), klasyczne alerty w Azure monitor są wycofywane, mimo że nadal są ograniczone dla zasobów, które jeszcze nie obsługują nowych alertów. Data wycofania tych alertów została jeszcze przedłużona. Nowa data zostanie ogłoszona wkrótce.
>

Jeśli zdecydujesz się na dobrowolne Migrowanie reguł alertów klasycznych do nowych reguł alertów, pamiętaj, że istnieją pewne różnice między tymi dwoma systemami. W tym artykule opisano te różnice i sposób przygotowania do zmiany.

## <a name="api-changes"></a>Zmiany interfejsu API

Interfejsy API, które tworzą i zarządzają klasycznymi regułami alertów ( `microsoft.insights/alertrules` ), różnią się od interfejsów API, które tworzą nowe alerty metryk () i zarządzają nimi `microsoft.insights/metricalerts` . W przypadku programistycznego tworzenia i zarządzania klasycznymi regułami alertów należy zaktualizować skrypty wdrażania, aby współpracowały z nowymi interfejsami API.

Poniższa tabela zawiera odwołanie do interfejsów programistycznych zarówno dla klasycznego, jak i nowego alertu:

| Typ skryptu wdrożenia | Alerty klasyczne | Nowe alerty metryk |
| ---------------------- | -------------- | ----------------- |
|Interfejs API REST     | [Microsoft. Insights/alertrules](/rest/api/monitor/alertrules)         | [Microsoft. Insights/metricalerts](/rest/api/monitor/metricalerts)       |
|Interfejs wiersza polecenia platformy Azure     | [AZ monitor alert](/cli/azure/monitor/alert?view=azure-cli-latest)        | [AZ Monitoruj metryki alertu](/cli/azure/monitor/metrics/alert?view=azure-cli-latest)        |
|Program PowerShell      | [Odwołanie](/powershell/module/az.monitor/add-azmetricalertrule)       |  [Odwołanie](/powershell/module/az.monitor/add-azmetricalertrulev2)    |
| Szablon usługi Azure Resource Manager | [W przypadku alertów klasycznych](./alerts-enable-template.md)|[Nowe alerty metryki](./alerts-metric-create-templates.md)|

## <a name="notification-payload-changes"></a>Zmiany ładunku powiadomień

Format ładunku powiadomień jest nieco inny między [klasycznymi regułami alertów](alerts-webhooks.md) i [nowymi alertami metryki](alerts-metric-near-real-time.md#payload-schema). Jeśli masz akcje elementu webhook, aplikacji logiki lub elementu Runbook, które są wyzwalane przez klasyczne reguły alertów, musisz zaktualizować te punkty końcowe powiadomień, aby akceptowały format ładunku nowych alertów metryk.

Skorzystaj z poniższej tabeli, aby zmapować pola ładunku elementu webhook z klasycznego formatu do nowego formatu:

| Typ punktu końcowego powiadomienia | Alerty klasyczne | Nowe alerty metryk |
| -------------------------- | -------------- | ----------------- |
|Czy alert został aktywowany lub rozwiązany?    | **Stany**       | **Data. status** |
|Informacje kontekstowe dotyczące alertu     | **Context**        | **Data. Context**        |
|Sygnatura czasowa, o której alert został aktywowany lub rozwiązany     | **Context. timestamp**       | **Data. Context. timestamp**        |
| Identyfikator reguły alertu | **context.id** | **data.context.id** |
| Nazwa reguły alertu | **context.name** | **data.context.name** |
| Opis reguły alertu | **Context. Description** | **Data. Context. Description** |
| Warunek reguły alertu | **Context. Condition** | **Data. Context. Condition** |
| Nazwa metryki | **Context. Condition. metricname** | **Data. Context. Condition. allOf [0]. metricname** |
| Agregacja czasu (stopień agregowania metryki w oknie oceny)| **Context. Condition. timeAggregation** | **Context. Condition. timeAggregation** |
| Okres próbny | **Context. Condition. windowSize** | **Data. Context. Condition. windowSize** |
| Operator (jak zagregowana wartość metryki jest porównywana z progiem) | **Context. Condition. operator** | **Data. Context. Condition. operator** |
| Próg | **Context. Condition. Threshold** | **Data. Context. Condition. allOf [0]. próg** |
| Wartość metryki | **Context. Condition. metricValue** | **Data. Context. Condition. allOf [0]. metricValue** |
| Identyfikator subskrypcji | **Context. Identyfikator subskrypcji** | **Data. Context. Identyfikator subskrypcji** |
| Grupa zasobów zasobu, którego to dotyczy | **Context. resources** | **Data. Context. resources** |
| Nazwa zasobu, którego to dotyczy | **Context. resourceName** | **Data. Context. resourceName** |
| Typ zasobu, którego to dotyczy | **Context. ResourceType** | **Data. Context. ResourceType** |
| Identyfikator zasobu zasobu, którego to dotyczy | **Context. resourceId** | **Data. Context. resourceId** |
| Bezpośredni link do strony podsumowania zasobów portalu | **Context. portalLink** | **Data. Context. portalLink** |
| Pola ładunków niestandardowych do przesłania do elementu webhook lub aplikacji logiki | **aœciwoœci** | **Data. Properties** |

Ładunki są podobne, jak widać. W poniższej sekcji przedstawiono:

- Szczegółowe informacje o modyfikowaniu aplikacji logiki w celu pracy z nowym formatem.
- Przykład elementu Runbook, który analizuje ładunek powiadomienia dla nowych alertów.

## <a name="modify-a-logic-app-to-receive-a-metric-alert-notification"></a>Modyfikowanie aplikacji logiki w celu otrzymywania powiadomienia o alercie

Jeśli używasz usługi Logic Apps z alertami klasycznymi, musisz zmodyfikować kod aplikacji logiki, aby przeanalizować nowy ładunek alertów metryk. Wykonaj następujące kroki:

1. Utwórz nową aplikację logiki.

1. Użyj szablonu "Azure Monitor-Metrics Handler". Ten szablon ma wyzwalacz **żądania HTTP** z zdefiniowanym odpowiednim schematem.

    ![Zrzut ekranu przedstawia dwa przyciski, puste Aplikacje logiki i Azure Monitor — obsługa alertów metryk.](media/alerts-migration/logic-app-template.png "Szablon alertu metryki")

1. Dodaj akcję do hostowania logiki przetwarzania.

## <a name="use-an-automation-runbook-that-receives-a-metric-alert-notification"></a>Korzystanie z elementu Runbook usługi Automation, który odbiera powiadomienie o alercie

Poniższy przykład zawiera kod programu PowerShell do użycia w elemencie Runbook. Ten kod może analizować ładunki dla zarówno klasycznych reguł alertów metryk, jak i nowych reguł alertów dotyczących metryk.

```PowerShell
## Example PowerShell code to use in a runbook to handle parsing of both classic and new metric alerts.

[OutputType("PSAzureOperationResponse")]

param
(
    [Parameter (Mandatory=$false)]
    [object] $WebhookData
)

$ErrorActionPreference = "stop"

if ($WebhookData)
{
    # Get the data object from WebhookData.
    $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

    # Determine whether the alert triggering the runbook is a classic metric alert or a new metric alert (depends on the payload schema).
    $schemaId = $WebhookBody.schemaId
    Write-Verbose "schemaId: $schemaId" -Verbose
    if ($schemaId -eq "AzureMonitorMetricAlert") {

        # This is the new metric alert schema.
        $AlertContext = [object] ($WebhookBody.data).context
        $status = ($WebhookBody.data).status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.allOf[0].metricName
        $metricValue = $AlertContext.condition.allOf[0].metricValue
        $threshold = $AlertContext.condition.allOf[0].threshold
        $timeAggregation = $AlertContext.condition.allOf[0].timeAggregation
    }
    elseif ($schemaId -eq $null) {
        # This is the classic metric alert schema.
        $AlertContext = [object] $WebhookBody.context
        $status = $WebhookBody.status

        # Parse fields related to alert rule condition.
        $metricName = $AlertContext.condition.metricName
        $metricValue = $AlertContext.condition.metricValue
        $threshold = $AlertContext.condition.threshold
        $timeAggregation = $AlertContext.condition.timeAggregation
    }
    else {
        # The schema is neither a classic metric alert nor a new metric alert.
        Write-Error "The alert data schema - $schemaId - is not supported."
    }

    # Parse fields related to resource affected.
    $ResourceName = $AlertContext.resourceName
    $ResourceType = $AlertContext.resourceType
    $ResourceGroupName = $AlertContext.resourceGroupName
    $ResourceId = $AlertContext.resourceId
    $SubId = $AlertContext.subscriptionId

    ## Your logic to handle the alert here.
}
else {
    # Error
    Write-Error "This runbook is meant to be started from an Azure alert webhook only."
}

```

Pełny przykład elementu Runbook, który zatrzyma maszynę wirtualną w przypadku wyzwolenia alertu, znajduje się w [dokumentacji Azure Automation](../../automation/automation-create-alert-triggered-runbook.md).

## <a name="partner-integration-via-webhooks"></a>Integracja z partnerem za pośrednictwem elementów webhook

Większość [naszych partnerów, którzy integrują się z klasycznymi alertami,](./partners.md) już obsługują nowsze alerty metryki za pomocą ich integracji. Znane integracji, które już pracują z nowymi alertami metryk są następujące:

- [PagerDuty](https://www.pagerduty.com/docs/guides/azure-integration-guide/)
- [OpsGenie](https://docs.opsgenie.com/docs/microsoft-azure-integration)
- [Aplikacji signl4](https://www.signl4.com/blog/mobile-alert-notifications-azure-monitor/)

Jeśli używasz integracji partnera, która nie jest wymieniona w tym miejscu, potwierdź u dostawcy integracji, że integracja współpracuje z nowymi alertami metryk.

## <a name="next-steps"></a>Następne kroki

- [Jak korzystać z narzędzia do migracji](alerts-using-migration-tool.md)
- [Informacje o sposobie działania narzędzia do migracji](alerts-understand-migration.md)
