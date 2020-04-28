---
title: Często zadawane pytania dotyczące usługi Azure Monitor dla maszyn wirtualnych | Dokumenty firmy Microsoft
description: Usługa Azure Monitor dla maszyn wirtualnych to rozwiązanie na platformie Azure, które łączy monitorowanie kondycji i wydajności systemu operacyjnego maszyny Wirtualnej platformy Azure, a także automatycznie wykrywa składniki aplikacji i zależności z innymi zasobami i mapuje komunikację między nimi. Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące wersji ga.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/31/2020
ms.openlocfilehash: 3d250ef1aba979be04a44acaf31a3d685f162e37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283892"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>Często zadawane pytania dotyczące usługi Azure Monitor dla maszyn wirtualnych (GA)
Ten często zadawane pytania dotyczące ogólnej dostępności obejmuje zmiany wprowadzone w IV kwartale 2019 r. i I kwartale 2020 r., gdy przygotowywaliśmy się do GA.

## <a name="updates-for-azure-monitor-for-vms"></a>Aktualizacje dla usługi Azure Monitor dla maszyn wirtualnych
W styczniu 2020 r. firma Microsoft wydała nową wersję usługi Azure Monitor dla maszyn wirtualnych przed ogłoszeniem ga. Klienci obsługujący usługę Azure Monitor dla maszyn wirtualnych otrzymają teraz wersję GA, ale obecni klienci korzystający z wersji usługi Azure Monitor dla maszyn wirtualnych z q4 2019 i wcześniejszych zostaną poproszeni o uaktualnienie. To często zadawane pytania zawiera wskazówki dotyczące wykonywania uaktualnienia na dużą skalę, jeśli masz duże wdrożenia w wielu obszarach roboczych.


Dzięki temu uaktualnieniu dane wydajności usługi Azure Monitor dla maszyn wirtualnych są przechowywane w tej samej tabeli *InsightsMetrics* co [usługa Azure Monitor dla kontenerów,](container-insights-overview.md)co ułatwia wykonywanie zapytań o dwa zestawy danych. Ponadto można przechowywać bardziej zróżnicowane zestawy danych, których nie mogliśmy przechowywać w poprzednio używanej tabeli. 

Nasze widoki wydajności są teraz przy użyciu danych, które przechowujemy w *insightsmetrics* tabeli.  Jeśli nie został jeszcze uaktualniony do korzystania z najnowszego rozwiązania VMInsights w obszarze roboczym, wykresy nie będą już wyświetlać informacji.  Możesz uaktualnić naszą stronę **Wprowadzenie,** jak opisano poniżej.


## <a name="what-is-changing"></a>Co się zmienia?
Firma We have released a new solution, named VMInsights, który zawiera dodatkowe możliwości zbierania danych wraz z nową lokalizacją do przechowywania tych danych w obszarze roboczym usługi Log Analytics. 

W przeszłości włączyliśmy rozwiązanie ServiceMap w obszarze roboczym i skonfigurowaliśmy liczniki wydajności w obszarze roboczym usługi Log Analytics, aby wysłać dane do tabeli *Perf.* To nowe rozwiązanie wysyła dane do tabeli o nazwie *InsightsMetrics,* która jest również używana przez usługę Azure Monitor dla kontenerów. Ten schemat tabeli umożliwia nam przechowywanie dodatkowych metryk i zestawów danych usługi, które nie są zgodne z formatem tabeli *Perf.*

Zaktualizowaliśmy nasze wykresy wydajności, aby używać danych, które przechowujemy w tabeli *InsightsMetrics.* Możesz uaktualnić, aby korzystać z tabeli *InsightsMetrics* z naszej strony **Wprowadzenie,** jak opisano poniżej.


## <a name="how-do-i-upgrade"></a>Jak uaktualnić?
Gdy obszar roboczy usługi Log Analytics zostanie uaktualniony do najnowszej wersji usługi Azure Monitor do maszyn wirtualnych, uaktualni agenta zależności na każdej z maszyn wirtualnych dołączonych do tego obszaru roboczego. Każda maszyna wirtualna wymagająca uaktualnienia zostanie zidentyfikowana na karcie **Wprowadzenie** w usłudze Azure Monitor dla maszyn wirtualnych w witrynie Azure portal. Po wybraniu uaktualnienia maszyny Wirtualnej, uaktualni obszar roboczy dla tej maszyny wirtualnej wraz z innymi maszynami wirtualnymi dołączonymi do tego obszaru roboczego. Można wybrać jedną maszynę wirtualną lub wiele maszyn wirtualnych, grup zasobów lub subskrypcji. 

Użyj następującego polecenia, aby uaktualnić obszar roboczy przy użyciu programu PowerShell:

```PowerShell
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName <resource-group-name> -WorkspaceName <workspace-name> -IntelligencePackName "VMInsights" -Enabled $True
```

## <a name="what-should-i-do-about-the-performance-counters-in-my-workspace-if-i-install-the-vminsights-solution"></a>Co należy zrobić z licznikami wydajności w moim obszarze roboczym, jeśli instaluję rozwiązanie VMInsights?

Poprzednia metoda włączania usługi Azure Monitor dla maszyn wirtualnych używane liczniki wydajności w obszarze roboczym. Bieżąca wersja przechowuje te `InsightsMetrics`dane w tabeli o nazwie . Możesz wyłączyć te liczniki wydajności w obszarze roboczym, jeśli nie trzeba już ich używać. 

>[!NOTE]
>Jeśli masz reguły alertów, które `Perf` odwołują się do tych liczników `InsightsMetrics` w tabeli, należy zaktualizować je, aby odwoływać się do nowych danych przechowywanych w tabeli. Zapoznaj się z naszą dokumentacją na przykład zapytania dziennika, których można użyć, które odnoszą się do tej tabeli.
>

Jeśli zdecydujesz się zachować włączone liczniki wydajności, zostanie naliczona naliczona naliczona naliczona naliczona naliczona i przechowywana w `Perf` tabeli na podstawie [Cennik usługi Log Analytics[(https://azure.microsoft.com/pricing/details/monitor/).

## <a name="how-will-this-change-affect-my-alert-rules"></a>Jak ta zmiana wpłynie na moje reguły alertów?

Jeśli utworzono [alerty dziennika,](../platform/alerts-unified-log.md) które kwerendy `Perf` tabeli docelowe liczniki wydajności, które zostały włączone `InsightsMetrics` w obszarze roboczym, należy zaktualizować te reguły, aby odwoływać się do tabeli zamiast. Te wskazówki dotyczą również wszelkich `ServiceMapComputer_CL` reguł `ServiceMapProcess_CL`wyszukiwania dzienników przy `VMComputer` użyciu `VMProcess` i , ponieważ te zestawy danych są przewożenia i tabel.

Zaktualizujemy to często zadawane pytania i naszą dokumentację, aby uwzględnić reguły alertów wyszukiwania dziennika dla zestawów danych, które zbieramy.

## <a name="how-will-this-affect-my-bill"></a>Jak to wpłynie na mój rachunek?

Rozliczenia są nadal oparte na danych pochłoniętych i zachowanych w obszarze roboczym usługi Log Analytics.

Dane wydajności na poziomie maszyny, które zbieramy, są takie same, `Perf` mają podobną wielkość do danych przechowywanych w tabeli i będą kosztować mniej więcej tyle samo.

## <a name="what-if-i-only-want-to-use-service-map"></a>Co zrobić, jeśli chcę używać tylko mapy usług?

To jest w porządku. Podczas wyświetlania usługi Azure Monitor dla maszyn wirtualnych zostanie wyświetlony monity w witrynie Azure Portal dotyczące nadchodzącej aktualizacji. Po zwolnieniu zostanie wyświetlony monit z prośbą o aktualizację do nowej wersji. Jeśli wolisz używać tylko funkcji [Mapy,](vminsights-maps.md) możesz zrezygnować z uaktualniania i nadal używać funkcji Mapy w usłudze Azure Monitor dla maszyn wirtualnych i rozwiązania mapy usług dostępnego z kafelka obszaru roboczego lub pulpitu nawigacyjnego.

Jeśli zdecydujesz się ręcznie włączyć liczniki wydajności w obszarze roboczym, możesz zobaczyć dane na niektórych naszych wykresach wydajności oglądanych z usługi Azure Monitor. Po wydaniu nowego rozwiązania zaktualizujemy nasze wykresy `InsightsMetrics` wydajności, aby zbadać dane przechowywane w tabeli. Jeśli chcesz zobaczyć dane z tej tabeli na tych wykresach, należy uaktualnić do nowej wersji usługi Azure Monitor dla maszyn wirtualnych.

Zmiany, aby przenieść `ServiceMapProcess_CL` dane i `ServiceMapComputer_CL` będą miały wpływ zarówno mapy usług i usługi Azure Monitor dla maszyn wirtualnych, więc nadal trzeba zaplanować dla tej aktualizacji.

Jeśli zdecydujesz się nie uaktualnić do rozwiązania **VMInsights,** firma We will continue to provide `Perf` legacy versions of our performance workbooks that refer to data in the table.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>Czy zestawy danych mapy usług będą również przechowywane w aplikacji InsightsMetrics?

Zestawy danych nie zostaną zduplikowane, jeśli używasz obu rozwiązań. Obie oferty współużytkują zestawy `VMComputer` danych, które będą przechowywane `VMProcess` w (dawniej ServiceMapComputer_CL), `VMConnection`(dawniej ServiceMapProcess_CL) oraz `VMBoundPort` tabelach do przechowywania gromadzonych przez nas zestawów danych map.  

Tabela `InsightsMetrics` będzie przechowywać zestawy danych maszyn wirtualnych, procesów i usług, które zbieramy i będą wypełniane tylko wtedy, gdy używasz usługi Azure Monitor dla maszyn wirtualnych i rozwiązania VM Insights. Rozwiązanie mapy usług nie będzie zbierać `InsightsMetrics` ani przechowywać danych w tabeli.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-in-my-workspace"></a>Czy zostanie naliczona podwójna opłata, jeśli w moim obszarze roboczym są dostępne rozwiązania Mapy usług i VMInsights?

Nie, te dwa rozwiązania współużytkują `VMComputer` zestawy danych map, które `VMProcess` przechowujemy w (dawniej ServiceMapComputer_CL), (dawniej ServiceMapProcess_CL), `VMConnection`i `VMBoundPort`. Nie zostanie naliczona podwójna opłata za te dane, jeśli oba rozwiązania są dostępne w obszarze roboczym.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data"></a>Jeśli usunę mapę usługi lub rozwiązanie VMInsights, spowoduje to usunięcie moich danych?

Nie, te dwa rozwiązania współużytkują `VMComputer` zestawy danych map, które `VMProcess` przechowujemy w (dawniej ServiceMapComputer_CL), (dawniej ServiceMapProcess_CL), `VMConnection`i `VMBoundPort`. Jeśli usuniesz jedno z rozwiązań, te zestawy danych zauważą, że nadal istnieje rozwiązanie, które używa danych i pozostaje w obszarze roboczym usługi Log Analytics. Należy usunąć oba rozwiązania z obszaru roboczego, aby dane zostały z niego usunięte.

## <a name="health-feature-is-in-limited-public-preview"></a>Funkcja kondycji znajduje się w ograniczonej publicznej wersji zapoznawczej

Otrzymaliśmy wiele wspaniałych opinii od klientów na temat naszego zestawu funkcji VM Health. Istnieje duże zainteresowanie wokół tej funkcji i emocje nad jej potencjał do obsługi przepływów pracy monitorowania. Planujemy wprowadzić szereg zmian, aby dodać funkcjonalność i odnieść się do otrzymanych opinii. 

Aby zminimalizować wpływ tych zmian na nowych klientów, przenieśliśmy tę funkcję do **ograniczonej publicznej wersji zapoznawczej.** Ta aktualizacja miała miejsce w październiku 2019.

Planujemy ponownie uruchomić tę funkcję kondycji w 2020 r., po tym jak usługa Azure Monitor dla maszyn wirtualnych jest w ga.

## <a name="how-do-existing-customers-access-the-health-feature"></a>W jaki sposób obecni klienci uzyskują dostęp do funkcji Zdrowie?

Obecni klienci korzystający z funkcji Kondycji będą nadal mieli do niej dostęp, ale nie będą ona oferowana nowym klientom.  

Aby uzyskać dostęp do tej funkcji, `feature.vmhealth=true` można dodać [https://portal.azure.com](https://portal.azure.com)następującą flagę funkcji do adresu URL witryny Azure portal . Przykład `https://portal.azure.com/?feature.vmhealth=true`.

Możesz również użyć tego krótkiego adresu URL, [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview)który automatycznie ustawia flagę obiektu: .

Jako istniejący klient możesz nadal korzystać z funkcji Kondycja na maszynach wirtualnych, które są połączone z istniejącą konfiguracją obszaru roboczego z funkcją kondycji.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-to-a-new-one"></a>Używam teraz maszyny Wirtualnej Zdrowie z jednym środowiskiem i chciałbym wdrożyć ją w nowym

Jeśli jesteś istniejącym klientem, który korzysta z funkcji Zdrowie i chcesz użyć jej vminsights@microsoft.com do nowego wdrożenia, skontaktuj się z nami, aby uzyskać instrukcje.

## <a name="next-steps"></a>Następne kroki

Aby zrozumieć wymagania i metody, które pomagają monitorować maszyny wirtualne, przejrzyj [wdrażanie usługi Azure Monitor dla maszyn wirtualnych.](vminsights-enable-overview.md)
