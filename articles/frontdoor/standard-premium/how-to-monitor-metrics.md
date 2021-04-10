---
title: Metryki monitorowania na platformie Azure — warstwa standardowa/Premium
description: W tym artykule opisano metryki monitorowania Standard/Premium na platformie Azure.
services: frontdoor
author: duau
manager: KumudD
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: yuajia
ms.openlocfilehash: 72388eb8006ff1b9628db5066dc63e6a0811f3d5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105557330"
---
# <a name="real-time-monitoring-in-azure-front-door-standardpremium"></a>Monitorowanie w czasie rzeczywistym na platformie Azure — warstwa standardowa/Premium

> [!Note]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? [Tutaj](../front-door-overview.md)Wyświetl.

Platforma Azure Front-Standard/Premium jest zintegrowana z Azure Monitor i ma 11 metryki, które ułatwiają monitorowanie z użyciem usługi Azure Front-Standard/Premium w czasie rzeczywistym w celu śledzenia, rozwiązywania problemów i debugowania.  

Miary platformy Azure z przodu Standard/Premium i wysyłają metryki w 60 sekund. Metryki mogą pojawić się w portalu dopiero po 3 minutach. Metryki mogą być wyświetlane na wykresach lub w wybranych siatkach i dostępne za pośrednictwem portalu, programu PowerShell, interfejsu wiersza polecenia i interfejsu API. Aby uzyskać więcej informacji, zobacz [Azure monitor metryki](../../azure-monitor/essentials/data-platform-metrics.md).  

Metryki domyślne są bezpłatne. Dodatkowe metryki można włączyć dla dodatkowego kosztu. 

Można skonfigurować alerty dla każdej metryki, takie jak próg dla 4XXErrorRate lub 5XXErrorRate. Gdy współczynnik błędów przekracza wartość progową, zostanie wyzwolony alert zgodnie z konfiguracją. Aby uzyskać więcej informacji, zobacz [Tworzenie i wyświetlanie alertów metryk i zarządzanie nimi przy użyciu Azure monitor](../../azure-monitor/alerts/alerts-metric.md). 

> [!IMPORTANT]
> Platforma Azure Front-Standard/Premium (wersja zapoznawcza) jest obecnie dostępna w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="metrics-supported-in-azure-front-door-standardpremium"></a>Metryki obsługiwane na platformie Azure Front-Standard/Premium

| Metryki  | Opis | Wymiary |
| ------------- | ------------- | ------------- |
| Współczynnik trafień bajtów | Wartość procentowa danych wyjściowych z pamięci podręcznej AFD, obliczona względem łącznego ruchu wychodzącego. </br> **Współczynnik trafień bajtów** = (ruch wychodzący z krawędzi od brzegu do punktu początkowego)/egress z krawędzi. </br> **Scenariusze wykluczone ze współczynnika trafień w bajtach**:</br> 1. jawnie konfigurujesz Brak pamięci podręcznej za pomocą aparatu reguł lub buforowania ciągu zapytania. </br> 2. jawnie Skonfiguruj dyrektywę kontroli pamięci podręcznej bez magazynu ani prywatnej pamięci podręcznej. </br>3. Współczynnik trafień bajtów może być niski, jeśli większość ruchu sieciowego jest przekazywana do źródła, a nie z pamięci podręcznej na podstawie konfiguracji lub scenariuszy. | Punkt końcowy |
| RequestCount | Liczba żądań klientów obsłużonych przez usługę CDN. | Punkt końcowy, kraj klienta, region klienta, stan HTTP, Grupa stanu HTTP |
| ResponseSize | Liczba żądań klientów obsłużonych przez AFD. |Punkt końcowy, kraj klienta, region klienta, stan HTTP, Grupa stanu HTTP |
| TotalLatency | Łączny czas od żądania klienta odebranego przez usługę CDN **do momentu wysłania ostatniego bajtu odpowiedzi z sieci CDN do klienta**. |Punkt końcowy, kraj klienta, region klienta, stan HTTP, Grupa stanu HTTP |
| RequestSize | Liczba bajtów wysłanych jako żądania od klientów do AFD. | Punkt końcowy, kraj klienta, region klienta, stan HTTP, Grupa stanu HTTP |
| 4XX% ErrorRate | Procent wszystkich żądań klientów, dla których kod stanu odpowiedzi to 4XX. | Punkt końcowy, kraj klienta, region klienta |
| 5XX% ErrorRate | Procent wszystkich żądań klientów, dla których kod stanu odpowiedzi to 5XX. | Punkt końcowy, kraj klienta, region klienta |
| OriginRequestCount  | Liczba żądań wysłanych z AFD do źródła | Punkt końcowy, źródło, stan HTTP, Grupa stanu HTTP |
| OriginLatency | Czas obliczony od momentu wysłania żądania przez AFD Edge do zaplecza do momentu odebrania przez AFD ostatniego bajtu odpowiedzi z zaplecza. | Punkt końcowy, Źródło |
| OriginHealth% | Procent pomyślnych sond kondycji od AFD do pochodzenia.| Źródło, Grupa pierwotna |
| Liczba żądań WAF | Dopasowane żądanie WAF. | Akcja, nazwa reguły, Nazwa zasad |

## <a name="access-metrics-in-azure-portal"></a>Dostęp do metryk w Azure Portal

1. Z menu Azure Portal wybierz pozycję **wszystkie zasoby**  >>  **\<your-AFD Standard/Premium (Preview) -profile>** .

2. W obszarze **monitorowanie** wybierz pozycję **metryki**:

3. W obszarze **metryki** wybierz metrykę do dodania:

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-1.png" alt-text="Zrzut ekranu przedstawiający stronę metryki." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-1-expanded.png":::

4. Wybierz pozycję **Dodaj filtr** , aby dodać filtr:

    :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-2.png" alt-text="Zrzut ekranu przedstawiający Dodawanie filtrów do metryk." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-2-expanded.png":::
    
5. Wybierz pozycję **Zastosuj podział** , aby podzielić dane o różne wymiary:

   :::image type="content" source="../media/how-to-monitoring-metrics/front-door-metrics-4.png" alt-text="Zrzut ekranu przedstawiający dodawanie wymiarów do metryk." lightbox="../media/how-to-monitoring-metrics/front-door-metrics-4-expanded.png":::

6. Wybierz pozycję **Nowy wykres** , aby dodać nowy wykres:

## <a name="configure-alerts-in-azure-portal"></a>Konfigurowanie alertów w Azure Portal

1. Skonfiguruj alerty na platformie Azure — Standard/Premium (wersja zapoznawcza), wybierając pozycję **monitorowanie**  >>  **alertów**.

1. Wybierz **nową regułę alertu** dla metryk wymienionych w sekcji metryki.

Opłata zostanie naliczona na podstawie Azure Monitor. Aby uzyskać więcej informacji na temat alertów, zobacz [Azure monitor alertów](../../azure-monitor/alerts/alerts-overview.md).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [raportach Standard/Premium platformy Azure](how-to-reports.md).
- Dowiedz się więcej o [dziennikach Standard/Premium platformy Azure](how-to-logs.md).