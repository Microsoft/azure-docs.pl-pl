---
title: Informacje o monitorowaniu Media Services danych
description: Ważne materiały referencyjne potrzebne podczas monitorowania Media Services
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femila
ms.topic: reference
ms.service: media-services
ms.custom: subject-monitoring
ms.date: 03/17/2021
ms.openlocfilehash: 66fce608515d16c5418ddd18e00319a3cbf088f7
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609092"
---
# <a name="monitoring-media-services-data-reference"></a>Informacje o monitorowaniu Media Services danych

W tym artykule omówiono dane, które są przydatne do monitorowania Media Services. Aby uzyskać więcej informacji na temat wszystkich metryk platformy obsługiwanych w Azure Monitor, przejrzyj [obsługiwane metryki z Azure monitor](../../../azure-monitor/essentials/metrics-supported.md).

## <a name="media-services-metrics"></a>Metryki Media Services

Metryki są zbierane w regularnych odstępach czasu, niezależnie od tego, czy wartość jest zmieniana. Są one przydatne w przypadku alertów, ponieważ mogą być próbkowane często, a alert może być uruchamiany szybko z stosunkowo prostą logiką.

Media Services obsługuje metryki monitorowania dla następujących zasobów:

* Konto
* Punkt końcowy przesyłania strumieniowego

### <a name="account"></a>Konto

Można monitorować następujące metryki konta.

|Nazwa metryki|Nazwa wyświetlana|Opis|
|---|---|---|
|AssetCount|Liczba zasobów|Zasoby na Twoim koncie.|
|AssetQuota|Przydział zasobów|Przydział zasobów na koncie.|
|AssetQuotaUsedPercentage|Procent użycia przydziału zasobów|Wartość procentowa przydziału zasobów jest już używana.|
|ContentKeyPolicyCount|Liczba zasad dotyczących kluczy zawartości|Zasady kluczy zawartości na Twoim koncie.|
|ContentKeyPolicyQuota|Przydział zasad dotyczących kluczy zawartości|Przydział zasad kluczy zawartości na Twoim koncie.|
|ContentKeyPolicyQuotaUsedPercentage|Procent użycia przydziału zasad klucza zawartości|Wartość procentowa przydziału zasad klucza zawartości już użyta.|
|StreamingPolicyCount|Liczba zasad przesyłania strumieniowego|Zasady przesyłania strumieniowego na Twoim koncie.|
|StreamingPolicyQuota|Limit przydziału zasad przesyłania strumieniowego|Przekroczono limit przydziału zasad przesyłania strumieniowego na Twoim koncie.|
|StreamingPolicyQuotaUsedPercentage|Procent użycia limitu przydziału zasad przesyłania strumieniowego|Wartość procentowa przydziału zasad przesyłania strumieniowego już używana.|

Należy również sprawdzić [przydziały i limity kont](../limits-quotas-constraints.md).

### <a name="streaming-endpoint"></a>Punkt końcowy przesyłania strumieniowego

Następujące metryki [punktów końcowych przesyłania strumieniowego](/rest/api/media/streamingendpoints) Media Services są obsługiwane:

|Nazwa metryki|Nazwa wyświetlana|Opis|
|---|---|---|
|Żądania|Żądania|Zapewnia łączną liczbę żądań HTTP obsłużonych przez punkt końcowy przesyłania strumieniowego.|
|Ruch wychodzący|Ruch wychodzący|Całkowita liczba bajtów wychodzących na minutę na punkt końcowy przesyłania strumieniowego.|
|SuccessE2ELatency|Pomyślne zakończenie oczekiwania|Czas trwania od momentu, gdy punkt końcowy przesyłania strumieniowego otrzymał żądanie do momentu wysłania ostatniego bajtu odpowiedzi.|
|Użycie procesora| | Użycie procesora dla punktów końcowych przesyłania strumieniowego w warstwie Premium. Te dane nie są dostępne dla standardowych punktów końcowych przesyłania strumieniowego. |
|Przepustowość ruchu wychodzącego | | Przepustowość w bitach na sekundę.|

## <a name="metric-dimensions"></a>Wymiary metryk

Aby uzyskać więcej informacji na temat wymiarów metryk, zobacz [wielowymiarowe metryki](../../../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

<!--**PLACEHOLDER** for dimensions table.-->

## <a name="resource-logs"></a>Dzienniki zasobów

## <a name="media-services-diagnostic-logs"></a>Media Services dzienników diagnostycznych

Dzienniki diagnostyczne zapewniają rozbudowane i częste dane dotyczące operacji zasobu platformy Azure. Aby uzyskać więcej informacji, zobacz [jak zbierać i zużywać dane dzienników z zasobów platformy Azure](../../../azure-monitor/essentials/platform-logs-overview.md).

Media Services obsługuje następujące dzienniki diagnostyczne:

* Dostarczanie klucza

### <a name="key-delivery"></a>Dostarczanie klucza

|Nazwa|Opis|
|---|---|
|Żądanie usługi dostarczania kluczy|Dzienniki pokazujące informacje o żądaniu usługi dostarczania kluczy. Aby uzyskać więcej informacji, zobacz [schematy](monitor-media-services-data-reference.md).|

## <a name="schemas"></a>Schematy

Aby uzyskać szczegółowy opis schematu dzienników diagnostycznych najwyższego poziomu, zobacz [obsługiwane usługi, schematy i kategorie dla dzienników diagnostycznych platformy Azure](../../../azure-monitor/essentials/resource-logs-schema.md).

## <a name="key-delivery-log-schema-properties"></a>Właściwości schematu dziennika dostarczania kluczy

Te właściwości są specyficzne dla schematu dziennika dostarczania kluczy.

|Nazwa|Opis|
|---|---|
|keyId|Identyfikator żądanego klucza.|
|keyType|Może to być jedna z następujących wartości: "Clear" (bez szyfrowania), "FairPlay", "PlayReady" lub "Widevine".|
|policyName|Nazwa Azure Resource Manager zasad.|
|Obiektu TokenType|Typ tokenu.|
|statusMessage|Komunikat o stanie.|

### <a name="example"></a>Przykład

Właściwości schematu żądań dostarczenia klucza.

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
> Widevine to usługa świadczona przez firmę Google Inc. z zastrzeżeniem warunków użytkowania i zasad zachowania poufności informacji w firmie Google, Inc.

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]
