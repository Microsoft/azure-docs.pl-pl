---
title: Informacje Media Services monitorowania
description: Ważne materiały referencyjne potrzebne podczas monitorowania Media Services
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femila
ms.topic: reference
ms.service: media-services
ms.custom: subject-monitoring
ms.date: 04/21/2021
ms.openlocfilehash: 3fd7b8013ec67d718f308ccd1b72a6f90012e02e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873059"
---
# <a name="monitoring-media-services-data-reference"></a>Informacje Media Services monitorowania

W tym artykule omykamy dane, które są przydatne do monitorowania Media Services. Aby uzyskać więcej informacji na temat wszystkich metryk platformy obsługiwanych w programie Azure Monitor, zapoznaj się z tematem [Obsługiwane metryki](../../../azure-monitor/essentials/metrics-supported.md)za pomocą Azure Monitor .

## <a name="metrics"></a>Metryki

Metryki są zbierane w regularnych odstępach czasu niezależnie od tego, czy wartość się zmienia. Są one przydatne w przypadku alertów, ponieważ mogą być często próbkowane, a alerty mogą być szybko wyzwani przy użyciu stosunkowo prostej logiki.


Media Services obsługuje metryki monitorowania dla następujących zasobów:

|Typ metryki | Dostawca zasobów/przestrzeń nazw typu<br/> i link do poszczególnych metryk |
|-------|-----|
| Media Services ogólne | [Ogólne](/azure/azure-monitor/essentials/metrics-supported#microsoftmediamediaservices) |
| Wydarzenia na żywo | [Microsoft.Media/mediaservices/liveEvents](/azure/azure-monitor/essentials/metrics-supported#microsoftmediamediaservicesliveevents) 
| Punkty końcowe przesyłania strumieniowego | [Microsoft.Media/mediaservices/streamingEndpoints,](/azure/azure-monitor/essentials/metrics-supported#microsoftmediamediaservicesstreamingendpoints)które są istotne dla interfejsu [API REST punktów końcowych przesyłania strumieniowego.](/rest/api/media/streamingendpoints) 


Należy również przejrzeć [limity przydziału i limity konta.](../limits-quotas-constraints-reference.md)


## <a name="metric-dimensions"></a>Wymiary metryki

Aby uzyskać więcej informacji na temat wymiarów metryk, zobacz [Metryki wielowymiarowe](../../../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

Usługi Media Services mają następujące wymiary metryk.  Są one własne, oparte na metrykach, które obsługują.  Aby uzyskać [więcej informacji, zobacz linki](#metrics) metryk powyżej.   
- Format OutputFormat
- HttpStatusCode 
- ErrorCode 
- Nazwa ścieżki 

## <a name="resource-logs"></a>Dzienniki zasobów

Dzienniki zasobów zawierają rozbudowane i częste dane dotyczące działania zasobu platformy Azure. Aby uzyskać więcej informacji, zobacz Jak zbierać i korzystać [z danych dzienników z zasobów platformy Azure.](../../../azure-monitor/essentials/platform-logs-overview.md)

Media Services obsługuje następujące dzienniki zasobów: [Microsoft.Media/mediaservices](/azure/azure-monitor/essentials/resource-logs-categories#microsoftmediamediaservices)

## <a name="schemas"></a>Schematy

Aby uzyskać szczegółowy opis schematu dzienników diagnostycznych najwyższego poziomu, zobacz Obsługiwane usługi, schematy i kategorie [dzienników diagnostycznych platformy Azure.](../../../azure-monitor/essentials/resource-logs-schema.md)

## <a name="key-delivery-log-schema-properties"></a>Właściwości schematu dziennika dostarczania kluczy

Te właściwości są specyficzne dla schematu dziennika dostarczania kluczy.

|Nazwa|Opis|
|---|---|
|keyId|Identyfikator żądanego klucza.|
|Keytype|Może to być jedna z następujących wartości: "Clear" (bez szyfrowania), "FairPlay", "PlayReady" lub "Widevine".|
|policyName|Nazwa Azure Resource Manager zasad.|
|Tokentype|Typ tokenu.|
|statusMessage|Komunikat o stanie.|

### <a name="example"></a>Przykład

Właściwości schematu żądań dostarczania kluczy.

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

>[!NOTE]
> Widevine to usługa dostarczana przez firmę Google Inc. z zastrzeżeniem warunków użytkowania usługi i zasad ochrony prywatności firmy Google, Inc.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]
