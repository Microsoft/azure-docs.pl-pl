---
title: Media Services metryki i dzienników diagnostycznych z Azure Monitor
titleSuffix: Azure Media Services
description: Informacje na temat monitorowania Azure Media Services metryk i dzienników diagnostycznych za pośrednictwem Azure Monitor.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2020
ms.author: inhenkel
ms.openlocfilehash: 33aed32c30f298fd3432f4cebcc28b9c20974545
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93309064"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs-with-azure-monitor"></a>Monitorowanie metryk Media Services i dzienników diagnostycznych za pomocą Azure Monitor

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[Azure monitor](../../azure-monitor/overview.md) umożliwia monitorowanie metryk i dzienników diagnostycznych, które ułatwiają zrozumienie sposobu działania aplikacji. Wszystkie dane zbierane przez usługę Azure Monitor pasują do jednego z dwóch podstawowych typów: metryk i dzienników. Można monitorować Media Services dzienników diagnostycznych oraz tworzyć alerty i powiadomienia dotyczące zebranych metryk i dzienników. Możesz wizualizować i analizować dane metryk przy użyciu [Eksploratora metryk](../../azure-monitor/platform/metrics-getting-started.md). Możesz wysyłać dzienniki do usługi [Azure Storage](https://azure.microsoft.com/services/storage/), przesyłać strumieniowo do [usługi Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), eksportować je do [log Analytics](https://azure.microsoft.com/services/log-analytics/)lub korzystać z usług innych firm.

Aby zapoznać się z szczegółowym omówieniem, zobacz [Azure monitor metryki](../../azure-monitor/platform/data-platform.md) i [Azure monitor dzienników diagnostycznych](../../azure-monitor/platform/platform-logs-overview.md).

W tym temacie omówiono obsługiwane [Media Services metryki](#media-services-metrics) i [dzienniki diagnostyczne Media Services](#media-services-diagnostic-logs).

## <a name="media-services-metrics"></a>Metryki Media Services

Metryki są zbierane w regularnych odstępach czasu, niezależnie od tego, czy wartość jest zmieniana. Są one przydatne w przypadku alertów, ponieważ mogą być próbkowane często, a alert może być uruchamiany szybko z stosunkowo prostą logiką. Aby uzyskać informacje na temat tworzenia alertów metryk, zobacz [Tworzenie i wyświetlanie alertów metryk oraz zarządzanie nimi za pomocą Azure monitor](../../azure-monitor/platform/alerts-metric.md).

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

Należy również sprawdzić [przydziały i limity kont](limits-quotas-constraints.md).

### <a name="streaming-endpoint"></a>Punkt końcowy przesyłania strumieniowego

Następujące metryki [punktów końcowych przesyłania strumieniowego](/rest/api/media/streamingendpoints) Media Services są obsługiwane:

|Nazwa metryki|Nazwa wyświetlana|Opis|
|---|---|---|
|Żądania|Żądania|Zapewnia łączną liczbę żądań HTTP obsłużonych przez punkt końcowy przesyłania strumieniowego.|
|Ruch wychodzący|Ruch wychodzący|Całkowita liczba bajtów wychodzących na minutę na punkt końcowy przesyłania strumieniowego.|
|SuccessE2ELatency|Pomyślne zakończenie oczekiwania|Czas trwania od momentu, gdy punkt końcowy przesyłania strumieniowego otrzymał żądanie do momentu wysłania ostatniego bajtu odpowiedzi.|
|Użycie procesora| Użycie procesora dla punktów końcowych przesyłania strumieniowego w warstwie Premium. Te dane nie są dostępne dla standardowych punktów końcowych przesyłania strumieniowego. |
|Przepustowość ruchu wychodzącego | Przepustowość w bitach na sekundę.|

### <a name="metrics-are-useful"></a>Metryki są przydatne

Poniżej przedstawiono przykłady sposobu, w jaki monitorowanie Media Services metryki mogą pomóc zrozumieć sposób działania aplikacji. Oto kilka pytań, na które można rozwiązać Media Services metryki:

* Jak mogę monitorować mój standardowy punkt końcowy przesyłania strumieniowego, aby sprawdzić, czy przekroczono limity?
* Jak mogę sprawdzić, czy mam wystarczającą liczbę jednostek skalowania punktów końcowych przesyłania strumieniowego w warstwie Premium?
* Jak ustawić alert, aby dowiedzieć się, kiedy skalować punkty końcowe przesyłania strumieniowego?
* Jak mogę ustawić alert, aby sprawdzić, czy osiągnięto maksymalną liczbę wyjściową skonfigurowaną na koncie?
* Jak można zobaczyć podział żądań zakończonych niepowodzeniem i przyczynę niepowodzenia?
* Jak mogę sprawdzić, ile żądań HLS lub PAUZy jest pobieranych z Pakowarki?
* Jak mogę ustawić alert, aby sprawdzić, czy osiągnięto wartość progową liczby nieudanych żądań?

Współbieżność jest istotna dla liczby punktów końcowych przesyłania strumieniowego używanych w ramach jednego konta w czasie. Należy pamiętać o relacji między liczbą współbieżnych strumieni z złożonymi parametrami publikowania, takimi jak dynamiczne Pakowanie na wiele protokołów, wiele szyfrowania DRM itp. Każdy dodatkowy opublikowany strumień na żywo zwiększa przepustowość procesora i wyjścia w punkcie końcowym przesyłania strumieniowego. Z tego względu należy użyć Azure Monitor, aby dokładnie monitorować wykorzystanie punktu końcowego przesyłania strumieniowego (procesor i ruch wychodzący), aby upewnić się, że jest odpowiednio skalowane (lub podzielić ruchu między wieloma punktami końcowymi przesyłania strumieniowego w przypadku bardzo dużej współbieżności).

### <a name="example"></a>Przykład

Zobacz [, jak monitorować Media Services metryki](media-services-metrics-howto.md).

## <a name="media-services-diagnostic-logs"></a>Media Services dzienników diagnostycznych

Dzienniki diagnostyczne zapewniają rozbudowane i częste dane dotyczące operacji zasobu platformy Azure. Aby uzyskać więcej informacji, zobacz [jak zbierać i zużywać dane dzienników z zasobów platformy Azure](../../azure-monitor/platform/platform-logs-overview.md).

Media Services obsługuje następujące dzienniki diagnostyczne:

* Dostarczanie klucza

### <a name="key-delivery"></a>Dostarczanie klucza

|Nazwa|Opis|
|---|---|
|Żądanie usługi dostarczania kluczy|Dzienniki pokazujące informacje o żądaniu usługi dostarczania kluczy. Aby uzyskać więcej informacji, zobacz [schematy](media-services-diagnostic-logs-schema.md).|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>Dlaczego warto używać dzienników diagnostycznych?

Niektóre elementy, które można przeanalizować za pomocą dzienników diagnostycznych dostarczania kluczy, to:

* Sprawdź liczbę licencji dostarczonych przez typ DRM.
* Zobacz liczbę licencji dostarczonych przez zasady.
* Zobacz błędy według typów DRM lub zasad.
* Sprawdź liczbę nieautoryzowanych żądań licencji od klientów.

### <a name="example"></a>Przykład

Zobacz [monitorowanie dzienników diagnostycznych usługi Media Service](media-services-diagnostic-logs-howto.md).

## <a name="next-steps"></a>Następne kroki

* [Jak zbierać i zużywać dane dzienników z zasobów platformy Azure](../../azure-monitor/platform/platform-logs-overview.md)
* [Tworzenie i wyświetlanie alertów metryk oraz zarządzanie nimi w usłudze Azure Monitor](../../azure-monitor/platform/alerts-metric.md)
* [Jak monitorować metryki Media Services](media-services-metrics-howto.md)
* [Monitorowanie dzienników diagnostycznych usługi Media Service](media-services-diagnostic-logs-howto.md)
