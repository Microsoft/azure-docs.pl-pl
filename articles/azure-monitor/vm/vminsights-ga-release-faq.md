---
title: Informacje o usłudze VM Insights (GA) — często zadawane pytania | Microsoft Docs
description: Usługa VM Insights to rozwiązanie na platformie Azure, które łączy monitorowanie kondycji i wydajności systemu operacyjnego maszyny wirtualnej platformy Azure, a także automatycznie odnajduje składniki aplikacji i zależności z innymi zasobami oraz mapuje komunikację między nimi. W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące wersji GA.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/31/2020
ms.openlocfilehash: fbef73bfe8058110277b200b8c4091fcde110c04
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102031881"
---
# <a name="vm-insights-generally-available-ga-frequently-asked-questions"></a>Usługi VM Insights są ogólnie dostępne (GA) często zadawanych pytań
Ta ogólna dostępność często zadawanych pytań dotyczy zmian wprowadzonych w kwartale 2019 i Q1 2020 w miarę przygotowywania do ogólnego użytku.

## <a name="updates-for-vm-insights"></a>Aktualizacje usługi VM Insights
W styczniu 2020 opublikowano nową wersję usługi VM Insights z wyprzedzeniem. Klienci, którzy włączą usługi VM Insights, otrzymają teraz wersję GA, ale istniejący klienci korzystający z wersji usługi VM Insights z 4 kwartale 2019 i wcześniejszym będą monitowani o uaktualnienie. Te często zadawane pytania oferują wskazówki dotyczące przeprowadzania uaktualnienia w odpowiedniej skali, jeśli masz duże wdrożenia w wielu obszarach roboczych.


W przypadku tego uaktualnienia dane wydajności usługi VM Insights są przechowywane w tej samej tabeli *InsightsMetrics* , co w przypadku usługi [Container Insights](../containers/container-insights-overview.md), co ułatwia wykonywanie zapytań dotyczących dwóch zestawów danych. Ponadto można przechowywać bardziej zróżnicowane zestawy danych, które nie mogły być przechowywane w tabeli, która została wcześniej użyta. 

Nasze widoki wydajności używają teraz danych przechowywanych w tabeli *InsightsMetrics* .  Jeśli jeszcze nie uaktualniono do korzystania z najnowszego rozwiązania VMInsights w obszarze roboczym, na wykresach nie będą już wyświetlane informacje.  Możesz przeprowadzić uaktualnienie **z naszej strony wprowadzenie,** zgodnie z poniższym opisem.


## <a name="what-is-changing"></a>Co się zmieni?
Opublikowano nowe rozwiązanie o nazwie VMInsights, które zawiera więcej możliwości zbierania danych oraz nową lokalizację do przechowywania tych danych w obszarze roboczym Log Analytics. 

W przeszłości włączono rozwiązanie ServiceMap w obszarze roboczym i skonfigurujesz liczniki wydajności w obszarze roboczym Log Analytics, aby wysłać dane do tabeli *wydajności* . To nowe rozwiązanie wysyła dane do tabeli o nazwie *InsightsMetrics* , która jest również używana przez usługi Container Insights. Ten schemat tabeli pozwala nam przechowywać więcej metryk i zestawów danych usług, które nie są zgodne z formatem tabeli *wydajności* .

Zaktualizowaliśmy wykresy wydajnościowe, aby użyć przechowywanych danych w tabeli *InsightsMetrics* . Możesz przeprowadzić uaktualnienie, aby użyć tabeli *InsightsMetrics* **na naszej stronie wprowadzenie,** zgodnie z poniższym opisem.


## <a name="how-do-i-upgrade"></a>Jak mogę uaktualnić?
Gdy obszar roboczy Log Analytics zostanie uaktualniony do najnowszej wersji Azure Monitor do maszyn wirtualnych, zostanie uaktualniony Agent zależności na każdej z maszyn wirtualnych dołączonych do tego obszaru roboczego. Każda maszyna wirtualna wymagająca uaktualnienia zostanie zidentyfikowana na karcie **wprowadzenie** w szczegółowe informacje o maszynie wirtualnej w Azure Portal. W przypadku wybrania opcji uaktualnienia maszyny wirtualnej program przeprowadzi uaktualnienie obszaru roboczego dla tej maszyny wirtualnej wraz z innymi maszynami wirtualnymi dołączonymi do tego obszaru roboczego. Można wybrać pojedynczą maszynę wirtualną lub wiele maszyn wirtualnych, grup zasobów lub subskrypcji. 

Użyj następującego polecenia, aby uaktualnić obszar roboczy przy użyciu programu PowerShell:

```PowerShell
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName <resource-group-name> -WorkspaceName <workspace-name> -IntelligencePackName "VMInsights" -Enabled $True
```

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>Co należy zrobić o licznikach wydajności w obszarze mój obszar roboczy, jeśli zainstaluję rozwiązanie VMInsights?

Poprzednia Metoda włączania liczników wydajności usługi VM Insights w obszarze roboczym. Bieżąca wersja zapisuje te dane w tabeli o nazwie `InsightsMetrics` . Te liczniki wydajności można wyłączyć w obszarze roboczym, jeśli nie trzeba już ich używać. 

>[!NOTE]
>Jeśli istnieją reguły alertów odwołujące się do tych liczników w `Perf` tabeli, należy je zaktualizować w celu odwoływania się do nowych danych przechowywanych w `InsightsMetrics` tabeli. Zapoznaj się z naszą dokumentacją dotyczącą przykładowych zapytań dzienników, których można użyć w odniesieniu do tej tabeli.
>

Jeśli zdecydujesz się na włączenie włączonych liczników wydajności, naliczanie opłat za dane pozyskane i przechowywane w `Perf` tabeli na podstawie [log Analytics cennika [( https://azure.microsoft.com/pricing/details/monitor/) .

## <a name="how-will-this-change-affect-my-alert-rules"></a>Jak ta zmiana wpłynie na moje reguły alertów?

Jeśli utworzono [alerty dziennika](../alerts/alerts-unified-log.md) , które wysyłają zapytania do `Perf` tabeli docelowej liczników wydajności, które zostały włączone w obszarze roboczym, należy zaktualizować te reguły, aby odwoływać się do `InsightsMetrics` tabeli. Te wskazówki dotyczą również wszelkich reguł przeszukiwania dzienników przy użyciu `ServiceMapComputer_CL` i `ServiceMapProcess_CL` , ponieważ te zestawy danych są przenoszone do `VMComputer` i `VMProcess` tabele.

Będziemy aktualizować te często zadawane pytania i naszą dokumentację, aby uwzględnić przykładowe reguły alertów wyszukiwania w dzienniku dla zbieranych danych.

## <a name="how-will-this-change-affect-my-bill"></a>Jak ta zmiana wpłynie na mój rachunek?

Opłaty są naliczane w oparciu o dane pozyskane i zachowane w obszarze roboczym Log Analytics.

Gromadzone dane wydajności na poziomie komputera są takie same, jak w przypadku danych przechowywanych w tabeli, o podobnym rozmiarze `Perf` .

## <a name="what-if-i-only-want-to-use-service-map"></a>Co zrobić, jeśli chcę używać tylko Service Map?

Jest to dokładne. Podczas wyświetlania szczegółowych informacji o nadchodzącej aktualizacji zobaczysz, że w Azure Portal zostanie wyświetlony komunikat z instrukcjami. Po wydaniu zostanie wyświetlony monit z prośbą o aktualizację do nowej wersji. Jeśli wolisz korzystać tylko z funkcji [Maps](vminsights-maps.md) , możesz zrezygnować z uaktualnienia i nadal korzystać z funkcji Maps w usłudze VM Insights i rozwiązania Service map, do którego uzyskano dostęp z obszaru roboczego lub kafelka pulpitu nawigacyjnego.

Jeśli wybrano opcję ręcznego włączania liczników wydajności w obszarze roboczym, można zobaczyć dane na niektórych z naszych wykresów wydajności wyświetlanych w Azure Monitor. Po wydaniu nowego rozwiązania będziemy aktualizować nasze wykresy wydajności, aby wykonywać zapytania dotyczące danych przechowywanych w `InsightsMetrics` tabeli. Jeśli chcesz zobaczyć dane z tej tabeli na tych wykresach, musisz przeprowadzić uaktualnienie do nowej wersji usługi VM Insights.

Zmiany dotyczące przenoszenia danych z programu `ServiceMapComputer_CL` i wpłyną `ServiceMapProcess_CL` na Service map i szczegółowe informacje dotyczące maszyn wirtualnych, dlatego należy zaplanować tę aktualizację.

W przypadku wybrania opcji Nie uaktualniaj do rozwiązania **VMInsights** będziemy nadal podawać starsze wersje naszych skoroszytów wydajności, które odwołują się do danych w `Perf` tabeli.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>Czy Service Map zestawy danych będą również przechowywane w InsightsMetrics?

Zestawy danych nie będą duplikowane, jeśli używasz obu rozwiązań. Obie oferty współdzielą zbiory danych, które będą przechowywane w `VMComputer` (dawniej ServiceMapComputer_CL), `VMProcess` (dawniej ServiceMapProcess_CL), `VMConnection` i `VMBoundPort` tabele do przechowywania zebranych zestawów danych mapy.  

W `InsightsMetrics` tabeli będą przechowywane zestawy danych maszyn wirtualnych, procesów i usług, które są zbierane i będą wypełniane tylko w przypadku korzystania z usługi VM Insights i rozwiązania VM Insights. Rozwiązanie Service Map nie będzie zbierać ani przechowywać danych w `InsightsMetrics` tabeli.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>Czy przy użyciu rozwiązań Service Map i VMInsights w obszarze mój obszar roboczy zostaną naliczone podwójne opłaty?

Nie, te dwa rozwiązania współdzielą zestawy danych mapy, które są przechowywane w `VMComputer` (dawniej ServiceMapComputer_CL), `VMProcess` (dawniej ServiceMapProcess_CL), `VMConnection` i `VMBoundPort` . Jeśli w obszarze roboczym znajdują się oba rozwiązania, nie będziesz mieć podwójnej opłaty za te dane.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>Jeśli usunę rozwiązanie Service Map lub VMInsights, usuniemy moje dane?

Nie, te dwa rozwiązania współdzielą zestawy danych mapy, które są przechowywane w `VMComputer` (dawniej ServiceMapComputer_CL), `VMProcess` (dawniej ServiceMapProcess_CL), `VMConnection` i `VMBoundPort` . W przypadku usunięcia jednego z tych rozwiązań te zestawy danych będą inejść, że nadal istnieje rozwiązanie, które korzysta z danych i pozostaje w obszarze roboczym Log Analytics. Musisz usunąć oba rozwiązania z obszaru roboczego, aby można było usunąć z niego dane.

## <a name="health-feature-is-in-limited-public-preview"></a>Funkcja kondycji jest w ograniczonej publicznej wersji zapoznawczej

Otrzymamy wiele doskonałych opinii od klientów dotyczących zestawu funkcji kondycji maszyn wirtualnych. Asignificant interesuje tę funkcję i ekscytację nad jej potencjalną obsługą przepływów pracy monitorowania. Planujemy wprowadzić serię zmian w celu dodania funkcji i rozwiązania otrzymanej opinii. 

Aby zminimalizować wpływ tych zmian na nowych klientów, przeniesionomy tę funkcję do **ograniczonej publicznej wersji zapoznawczej**. Ta aktualizacja miała miejsce w październiku 2019.

Planujemy ponowne uruchomienie tej funkcji kondycji w 2020, po uzyskaniu szczegółowych informacji o maszynie wirtualnej.

## <a name="how-do-existing-customers-access-the-health-feature"></a>Jak istniejący klienci uzyskują dostęp do funkcji kondycji?

Istniejący klienci korzystający z funkcji kondycji nadal będą mieć do niej dostęp, ale nie będzie oferowany nowym klientom.  

Aby uzyskać dostęp do tej funkcji, można dodać następującą flagę funkcji `feature.vmhealth=true` do adresu URL Azure Portal [https://portal.azure.com](https://portal.azure.com) . Przykład `https://portal.azure.com/?feature.vmhealth=true` .

Możesz również użyć tego krótkiego adresu URL, który ustawia flagę funkcji automatycznie: [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview) .

Jako istniejący klient można nadal korzystać z funkcji kondycji na maszynach wirtualnych, które są podłączone do istniejącej konfiguracji obszaru roboczego z funkcjonalnością kondycji.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>Teraz korzystam z kondycji maszyny wirtualnej z jednym środowiskiem i chcę wdrożyć je w nowym

Jeśli jesteś istniejącym klientem korzystającym z funkcji kondycji i chcesz go użyć do nowego wdrożenia, skontaktuj się z nami, vminsights@microsoft.com Aby uzyskać instrukcje.

## <a name="next-steps"></a>Następne kroki

Aby poznać wymagania i metody, które ułatwiają monitorowanie maszyn wirtualnych, zobacz [wdrażanie usługi VM Insights](./vminsights-enable-overview.md).
