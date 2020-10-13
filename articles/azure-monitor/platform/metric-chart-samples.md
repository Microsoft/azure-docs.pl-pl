---
title: Przykład wykresu metryki Azure Monitor
description: Dowiedz się więcej na temat wizualizacji danych Azure Monitor.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 9b2ab664f319de07fd70bd1a22b1ba6d64ac208f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87320259"
---
# <a name="metric-chart-examples"></a>Przykłady wykresu metryki 

Platforma Azure oferuje [tysiące metryk](./metrics-supported.md), z których wiele ma wymiary. Przy użyciu [filtrów wymiarów](./metrics-charts.md), stosowania [dzielenia](./metrics-charts.md), kontroli typu wykresu i dostosowywania ustawień wykresu można tworzyć zaawansowane widoki diagnostyczne i pulpity nawigacyjne, które zapewniają wgląd w kondycję infrastruktury i aplikacji. W tym artykule przedstawiono przykłady wykresów, które można skompilować przy użyciu [Eksplorator metryk](./metrics-charts.md) i wyjaśniono kroki niezbędne do skonfigurowania każdego z tych wykresów.

Chcesz udostępnić przykłady doskonałych wykresów? Współtworzenie tej strony w witrynie GitHub i udostępnianie własnych przykładów wykresów!

## <a name="website-cpu-utilization-by-server-instances"></a>Użycie procesora przez witryny sieci Web według wystąpień serwera

Ten wykres pokazuje, czy procesor dla App Service znajdował się w akceptowalnym zakresie i dzieli go na wystąpienie, aby określić, czy ładowanie zostało prawidłowo rozproszone. Na wykresie można zobaczyć, że aplikacja była uruchomiona w jednym wystąpieniu serwera przed 6 AM, a następnie skalowana przez dodanie kolejnego wystąpienia.

![Wykres liniowy średniej wartości procentowej procesora według wystąpienia serwera](./media/metric-chart-samples/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>Jak skonfigurować ten wykres?

Wybierz zasób App Service i Znajdź metrykę **procentową procesora CPU** . Następnie kliknij przycisk **Zastosuj podział** i wybierz wymiar **wystąpienia** .

## <a name="application-availability-by-region"></a>Dostępność aplikacji według regionów

Wyświetl dostępność aplikacji według regionów, aby zidentyfikować lokalizacje geograficzne, w których występują problemy. Ten wykres pokazuje metrykę dostępności Application Insights. Zobaczysz, że monitorowana aplikacja nie ma problemu z dostępnością z wschodniego centrum danych USA, ale występuje problem z częściową dostępnością z regionu zachodnie stany USA, a Azja Wschodnia.

![Wykres średniej dostępności według lokalizacji](./media/metric-chart-samples/availability-run-location.png)

### <a name="how-to-configure-this-chart"></a>Jak skonfigurować ten wykres?

Najpierw należy włączyć monitorowanie [dostępności Application Insights](../app/monitor-web-app-availability.md) dla witryny sieci Web. Po wybraniu tej opcji zaznacz zasób Application Insights i wybierz metrykę dostępności. Zastosuj podział w wymiarze **lokalizacji przebiegu** .

## <a name="volume-of-storage-account-transactions-by-api-name"></a>Ilość transakcji konta magazynu według nazwy interfejsu API

Zasób konta magazynu ma nadmierną ilość transakcji. Metryki transakcji można użyć do określenia, który interfejs API jest odpowiedzialny za nadmierne obciążenie. Zwróć uwagę, że następujący wykres jest skonfigurowany z tym samym wymiarem (nazwą interfejsu API) w funkcji filtrowania i dzielenia, aby zawęzić widok tylko do wywołań interfejsu API w celu:

![Wykres słupkowy transakcji interfejsu API](./media/metric-chart-samples/transactions-by-api.png)

### <a name="how-to-configure-this-chart"></a>Jak skonfigurować ten wykres?

W selektorze metryki wybierz konto magazynu i metrykę **transakcji** . Przełącz typ wykresu na **Wykres słupkowy**. Kliknij przycisk **Zastosuj podział** i wybierz pozycję **nazwa interfejsu API**wymiaru. Następnie ponownie kliknij przycisk **Dodaj filtr** i wybierz wymiar **nazwy interfejsu API** . W oknie dialogowym Filtr Wybierz interfejsy API, które mają być wykreślone na wykresie.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o Azure Monitor [skoroszytach](./workbooks-overview.md)
* Dowiedz się więcej o [Eksploratorze metryk](metrics-charts.md)

