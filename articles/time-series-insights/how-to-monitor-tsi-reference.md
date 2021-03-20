---
title: Informacje o monitorowaniu Azure Time Series Insights danych | Microsoft Docs
description: Dokumentacja dotycząca monitorowania Azure Time Series Insights.
author: deepakpalled
ms.author: lyhughes
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: bfd0d04313f0b519b4013a43e29d88400c73ea31
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591385"
---
# <a name="monitoring-azure-time-series-insights-data-reference"></a>Informacje o monitorowaniu Azure Time Series Insights danych

Dowiedz się więcej na temat danych i zasobów zebranych przez Azure Monitor ze środowiska Azure Time Series Insights. Aby uzyskać szczegółowe informacje na temat zbierania i analizowania danych monitorowania, zobacz [Time Series Insights monitorowania]( ./how-to-monitor-tsi.md) .

## <a name="metrics"></a>Metryki

W tej sekcji wymieniono wszystkie automatycznie zbierane metryki platformy zebrane dla Azure Time Series Insights. Aby uzyskać listę wszystkich Azure Monitor metryki pomocy technicznej (w tym Azure Time Series Insights), zobacz temat [Azure monitor Supported Metrics](../azure-monitor/essentials/metrics-supported.md). Dostawcą zasobów dla tych metryk są [Microsoft. TimeSeriesInsights/Environments/eventsources](../azure-monitor/essentials/metrics-supported.md#microsofttimeseriesinsightsenvironmentseventsources) i [Microsoft. TimeSeriesInsights/](../azure-monitor/essentials/metrics-supported.md#microsofttimeseriesinsightsenvironments)Environments.


### <a name="ingress"></a>Ruch przychodzący
 
|Metric|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|
|---|---|---|---|---|
|IngressReceivedBytes|Bajty odebrane z ruchu przychodzącego|Bajty|Łącznie|Liczba bajtów odczytanych ze źródła zdarzenia|
|IngressReceivedInvalidMessages|Odebrano nieprawidłowe komunikaty dotyczące transferu danych przychodzących|Liczba|Łącznie|Liczba nieprawidłowych komunikatów odczytanych ze źródła zdarzeń|
|IngressReceivedMessages|Odebrane komunikaty transferu danych przychodzących|Liczba|Łącznie|Liczba komunikatów odczytanych ze źródła zdarzeń|
|IngressReceivedMessagesCountLag|Opóźnienie liczby odebranych komunikatów przychodzących|Liczba|Średnia|Różnica między numerem sekwencyjnym ostatnio zarejestrowanego komunikatu w partycji źródłowej zdarzenia a sekwencją sekwencji komunikatów przetwarzanych w ramach ruchu przychodzącego|
|IngressReceivedMessagesTimeLag|Zwłoka czasu odbierania komunikatów przychodzących|Sekundy|Maksimum|Różnica między czasem, w którym wiadomość jest przetworzona do kolejki w źródle zdarzeń i czasie, w którym jest przetwarzany w danych wejściowych|
|IngressStoredBytes|Bajty przechowywane w ruchu przychodzącym|Bajty|Łącznie|Łączny rozmiar zdarzeń pomyślnie przetworzonych i dostępnych dla zapytania|
|IngressStoredEvents|Zdarzenia związane z transferem danych przychodzących|Liczba|Łącznie|Liczba zdarzeń spłaszczonych pomyślnie przetworzonych i dostępnych dla zapytania|

### <a name="storage"></a>Storage

|Metric|Nazwa wyświetlana metryki|Jednostka|Typ agregacji|Opis|
|---|---|---|---|---|
|WarmStorageMaxProperties|Maksymalna liczba właściwości magazynu ciepłego|Liczba|Maksimum|Maksymalna liczba właściwości używanych przez środowisko dla jednostki SKU S1/S2 i Maksymalna liczba właściwości dozwolona przez magazyn ciepły dla jednostki SKU|
|WarmStorageUsedProperties|Właściwości używanej pamięci masowej |Liczba|Maksimum|Liczba właściwości używanych przez środowisko dla jednostki SKU S1/S2 i liczby właściwości używanych przez magazyn ciepły dla jednostek SKU|

## <a name="resource-logs"></a>Dzienniki zasobów

Ta sekcja zawiera listę typów dzienników zasobów, które można zbierać dla środowiska Azure Time Series Insightsowego.

| Kategoria | Nazwa wyświetlana | Opis |
|----- |----- |----- |
| Ruch przychodzący | TSIIngress | Kategoria ruchu przychodzącego śledzi błędy występujące w potoku transferu danych przychodzących. Ta kategoria zawiera błędy występujące podczas otrzymywania zdarzeń (takich jak awarie w celu nawiązania połączenia ze źródłem zdarzenia) i przetwarzania zdarzeń (takich jak błędy podczas analizowania ładunku zdarzenia). |

## <a name="schemas"></a>Schematy
Następujące schematy są używane przez Azure Time Series Insights

### <a name="tsiingress-table"></a>Tabela TSIIngress

| Właściwość | Opis |
|----- |----- |
| TimeGenerated | Czas (UTC), w którym to zdarzenie jest generowane. |
| Lokalizacja | Lokalizacja zasobu. |
| Kategoria | Kategoria zdarzenia dziennika. |
| OperationName | Nazwa operacji zdarzenia. |
| CorrelationId | Identyfikator korelacji żądania. |
| Poziom | Poziom ważności zdarzenia. |
| ResultDescription | Opis wyniku operacji, taki jak "Odebrano błąd zabroniony". |
| Komunikat | Komunikat skojarzony z błędem. Zawiera szczegółowe informacje o błędach i sposobach łagodzenia błędu. |
| ErrorCode | Kod skojarzony z błędem |
| EventSourcetype | Typ źródła zdarzenia. Może to być centrum zdarzeń lub Centrum IoT Hub. |
| EventSourceProperties | Kolekcja właściwości specyficznych dla źródła zdarzenia. Zawiera szczegółowe informacje, takie jak grupa odbiorców i nazwa klucza dostępu. |
