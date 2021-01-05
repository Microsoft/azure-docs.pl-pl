---
title: Przykład wykresu metryki Azure Monitor
description: Dowiedz się więcej na temat wizualizacji danych Azure Monitor.
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 01/29/2019
ms.author: vitalyg
ms.subservice: metrics
ms.openlocfilehash: 05dadfe88ed64aea8066b02298ba158a44a03c6f
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/24/2020
ms.locfileid: "97760187"
---
# <a name="metric-chart-examples"></a>Przykłady wykresu metryki 

Platforma Azure oferuje [tysiące metryk](./metrics-supported.md), z których wiele ma wymiary. Przy użyciu [filtrów wymiarów](./metrics-charts.md), stosowania [dzielenia](./metrics-charts.md), kontroli typu wykresu i dostosowywania ustawień wykresu można tworzyć zaawansowane widoki diagnostyczne i pulpity nawigacyjne, które zapewniają wgląd w kondycję infrastruktury i aplikacji. W tym artykule przedstawiono przykłady wykresów, które można skompilować przy użyciu [Eksplorator metryk](./metrics-charts.md) i wyjaśniono kroki niezbędne do skonfigurowania każdego z tych wykresów.

Chcesz udostępnić przykłady doskonałych wykresów? Współtworzenie tej strony w witrynie GitHub i udostępnianie własnych przykładów wykresów!

## <a name="website-cpu-utilization-by-server-instances"></a>Użycie procesora przez witryny sieci Web według wystąpień serwera

Ten wykres pokazuje, czy procesor dla App Service znajdował się w akceptowalnym zakresie i dzieli go na wystąpienie, aby określić, czy ładowanie zostało prawidłowo rozproszone. Na wykresie można zobaczyć, że aplikacja była uruchomiona w jednym wystąpieniu serwera przed 6 AM, a następnie skalowana przez dodanie kolejnego wystąpienia.

![Wykres liniowy średniej wartości procentowej procesora według wystąpienia serwera](./media/metrics-charts/cpu-by-instance.png)

### <a name="how-to-configure-this-chart"></a>Jak skonfigurować ten wykres?

Wybierz zasób App Service i Znajdź metrykę **procentową procesora CPU** . Następnie kliknij przycisk **Zastosuj podział** i wybierz wymiar **wystąpienia** .

## <a name="application-availability-by-region"></a>Dostępność aplikacji według regionów

Wyświetl dostępność aplikacji według regionów, aby zidentyfikować lokalizacje geograficzne, w których występują problemy. Ten wykres pokazuje metrykę dostępności Application Insights. Zobaczysz, że monitorowana aplikacja nie ma problemu z dostępnością z wschodniego centrum danych USA, ale występuje problem z częściową dostępnością z regionu zachodnie stany USA, a Azja Wschodnia.

![Wykres średniej dostępności według lokalizacji](./media/metrics-charts/availability-by-location.png)

### <a name="how-to-configure-this-chart"></a>Jak skonfigurować ten wykres?

Najpierw należy włączyć monitorowanie [dostępności Application Insights](../app/monitor-web-app-availability.md) dla witryny sieci Web. Po wybraniu tej opcji zaznacz zasób Application Insights i wybierz metrykę dostępności. Zastosuj podział w wymiarze **lokalizacji przebiegu** .

## <a name="volume-of-failed-storage-account-transactions-by-api-name"></a>Liczba zakończonych niepowodzeniem transakcji konta magazynu według nazwy interfejsu API

Zasób konta magazynu ma nadmierną ilość nieudanych transakcji. Metryki transakcji można użyć do zidentyfikowania, który interfejs API jest odpowiedzialny za nadmiarową awarię. Zwróć uwagę, że następujący wykres został skonfigurowany z tym samym wymiarem (nazwą interfejsu API) w ramach dzielenia i filtrowania według typu odpowiedzi zakończonej niepowodzeniem:

![Wykres słupkowy transakcji interfejsu API](./media/metrics-charts/split-and-filter-example.png)

### <a name="how-to-configure-this-chart"></a>Jak skonfigurować ten wykres?

W selektorze metryki wybierz konto magazynu i metrykę **transakcji** . Przełącz typ wykresu na **Wykres słupkowy**. Kliknij przycisk **Zastosuj podział** i wybierz pozycję **nazwa interfejsu API** wymiaru. Następnie ponownie kliknij przycisk **Dodaj filtr** i wybierz wymiar **nazwy interfejsu API** . W oknie dialogowym Filtr Wybierz interfejsy API, które mają być wykreślone na wykresie.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o Azure Monitor [skoroszytach](./workbooks-overview.md)
* Dowiedz się więcej o [Eksploratorze metryk](metrics-charts.md)

