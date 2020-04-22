---
title: Zarządzanie użyciem i kosztami dzienników usługi Azure Monitor | Dokumenty firmy Microsoft
description: Dowiedz się, jak zmienić plan cenowy i zarządzać zasadami dotyczącymi ilości danych i przechowywania dla obszaru roboczego usługi Log Analytics w usłudze Azure Monitor.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 9a7d0530c4f03138fad3e4aaa473d54e1cfd5b0a
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686561"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Zarządzanie użyciem i kosztami za pomocą dzienników usługi Azure Monitor

> [!NOTE]
> W tym artykule opisano, jak zrozumieć i kontrolować koszty dzienników usługi Azure Monitor. Powiązany [artykuł, Monitorowanie użycia i szacowane koszty](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) opisano sposób wyświetlania użycia i szacowanych kosztów w wielu funkcjach monitorowania platformy Azure dla różnych modeli cenowych. Wszystkie ceny i koszty przedstawione w tym artykule służą wyłącznie celom. 

Dzienniki usługi Azure Monitor są przeznaczone do skalowania i obsługi zbierania, indeksowania i przechowywania ogromnych ilości danych dziennie z dowolnego źródła w przedsiębiorstwie lub wdrożonych na platformie Azure.  Chociaż może to być podstawowym czynnikiem w organizacji, efektywność kosztowa jest ostatecznie podstawowym czynnikiem. W tym celu należy zrozumieć, że koszt obszaru roboczego usługi Log Analytics nie jest oparty tylko na ilości zebranych danych, ale jest również zależny od wybranego planu i czasu, przez jaki wybrano przechowywanie danych generowanych z połączonych źródeł.  

W tym artykule sprawdzamy, jak można aktywnie monitorować liczbę pojądanych danych i wzrost magazynu oraz zdefiniować limity kontroli kosztów związanych z nimi. 

## <a name="pricing-model"></a>Model cen

Domyślna cena dla usługi Log Analytics to model **płatności zgodnie z rzeczywistym użyciem** oparty na ilości danych pochłoniętych i opcjonalnie dla dłuższego przechowywania danych. Ilość danych jest mierzona jako rozmiar danych, które będą przechowywane. Każdy obszar roboczy usługi Log Analytics jest naliczany jako oddzielna usługa i przyczynia się do rachunku za subskrypcję platformy Azure. Ilość połkuwania danych może być znaczna w zależności od następujących czynników: 

  - Liczba włączonych rozwiązań do zarządzania i ich konfiguracja (np. 
  - Liczba monitorowanych maszyn wirtualnych
  - Typ danych zebranych z każdej monitorowanej maszyny wirtualnej 
  
Oprócz modelu płatności zgodnie z rzeczywistym i rzeczywistym, usługa Log Analytics ma warstwy **rezerwacji pojemności,** które umożliwiają zaoszczędzenie aż 25% w porównaniu z ceną płatności zgodnie z rzeczywistym i wyjazdem. Cena rezerwacji pojemności umożliwia zakup rezerwacji od 100 GB/dzień. Wszelkie użycie powyżej poziomu rezerwacji będzie rozliczane według stawki płatności zgodnie z rzeczywistym użyciem. Poziomy rezerwacji zdolności produkcyjnych mają 31-dniowy okres zobowiązań. W okresie zobowiązań można zmienić poziom rezerwacji zdolności produkcyjnych wyższego poziomu (który uruchomi ponownie 31-dniowy okres zobowiązań), ale nie można wrócić do płatności zgodnie z rzeczywistym przejściem lub do niższej warstwy rezerwacji zdolności produkcyjnych do czasu zakończenia okresu zobowiązań. Rozliczanie warstw rezerwacja zdolności produkcyjnych odbywa się codziennie. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/monitor/) o cenach płatności zgodnie z rzeczywistym użyciem i rezerwacji pojemności. 

We wszystkich warstwach cenowych wolumin danych jest obliczany na podstawie reprezentacji ciągu danych, gdy są one przygotowywane do przechowywania. W obliczaniu rozmiaru zdarzenia nie uwzględniono kilku właściwości wspólnych dla `_ResourceId` `_ItemId`wszystkich `_IsBillable` `_BilledSize` [typów danych,](https://docs.microsoft.com/azure/azure-monitor/platform/log-standard-properties) w tym , i .

Należy również pamiętać, że niektóre rozwiązania, takie jak [Usługa Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/) i usługa Azure [Sentinel,](https://azure.microsoft.com/pricing/details/azure-sentinel/)mają własny model cenowy. 

## <a name="estimating-the-costs-to-manage-your-environment"></a>Szacowanie kosztów zarządzania środowiskiem 

Jeśli nie używasz jeszcze dzienników usługi Azure Monitor, możesz użyć [kalkulatora cen usługi Azure Monitor,](https://azure.microsoft.com/pricing/calculator/?service=monitor) aby oszacować koszt korzystania z usługi Log Analytics. Zacznij od wprowadzenia "Azure Monitor" w polu wyszukiwania i kliknięcia powstałego kafelka usługi Azure Monitor. Przewiń stronę w dół do usługi Azure Monitor i wybierz usługę Log Analytics z listy rozwijanej Typ.  W tym miejscu można wprowadzić liczbę maszyn wirtualnych i GB danych, które mają być zbierane z każdej maszyny Wirtualnej. Zazwyczaj 1 do 3 GB miesiąca danych jest pozyskiwania z typowej maszyny Wirtualnej platformy Azure. Jeśli już oceniasz dzienniki usługi Azure Monitor, możesz użyć statystyk danych z własnego środowiska. Poniżej opisano, jak określić [liczbę monitorowanych maszyn wirtualnych](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) i [ilość danych, które pochłonął obszar roboczy.](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume) 

## <a name="understand-your-usage-and-estimate-costs"></a>Zrozumienie sposobu użytkowania i szacowania kosztów

Jeśli używasz teraz dzienników usługi Azure Monitor, łatwo zrozumieć, jakie koszty mogą być oparte na ostatnich wzorców użycia. Aby to zrobić, użyj **użycia usługi Log Analytics i szacowanych kosztów** do przeglądania i analizowania użycia danych. Pokazuje to, ile danych jest zbierane przez każde rozwiązanie, ile danych jest zachowywane i szacowane koszty na podstawie ilości danych pojął i wszelkie dodatkowe przechowywania poza uwzględnioną kwotę.

![Użycie i szacunkowe koszty](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Aby bardziej szczegółowo eksplorować dane, kliknij ikonę w prawym górnym rogu jednego z wykresów na stronie **Zużycie i Koszty szacunkowe.** Teraz możesz pracować z tym zapytaniem, aby zapoznać się z bardziej szczegółowe informacje na temat użycia.  

![Widok dzienników](media/manage-cost-storage/logs.png)

Na stronie **Użycie i Koszty szacunkowe** można przejrzeć ilość danych za miesiąc. Obejmuje to wszystkie dane odebrane i zachowane w obszarze roboczym usługi Log Analytics.  Kliknij **szczegóły użycia** od góry strony, aby wyświetlić pulpit nawigacyjny użycia z informacjami o trendach ilości danych według źródła, komputerów i oferty. Aby wyświetlić i ustawić dzienny limit lub zmodyfikować okres przechowywania, kliknij pozycję **Zarządzanie woluminami danych**.
 
Opłaty usługi Log Analytics są dodawane do rachunku platformy Azure. Szczegółowe informacje o rachunku za usługę Azure można zobaczyć w sekcji Rozliczenia w witrynie Azure portal lub w [portalu rozliczeń platformy Azure.](https://account.windowsazure.com/Subscriptions)  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Wyświetlanie użycia usługi Log Analytics na rachunku platformy Azure 

Platforma Azure oferuje wiele przydatnych funkcji w centrum [Azure Cost Management + Billing](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) Hub. Na przykład funkcja "Analiza kosztów" umożliwia wyświetlanie wydatków na zasoby platformy Azure. Dodanie filtru według typu zasobu (do microsoft.operationalinsights/workspace for Log Analytics) umożliwia śledzenie wydatków.

Więcej zrozumienia użycia można uzyskać, [pobierając użycie z witryny Azure portal.](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal) W pobranym arkuszu kalkulacyjnym możesz zobaczyć użycie na zasób platformy Azure (np. obszar roboczy usługi Log Analytics) dziennie. W tym arkuszu kalkulacyjnym programu Excel użycie z obszarów roboczych usługi Log Analytics można znaleźć, najpierw filtrując w kolumnie "Kategoria miernika", aby wyświetlić "Statystyki i analizy" (używane przez niektóre starsze warstwy cenowe) i "Analiza dzienników", a następnie dodając filtr w kolumnie "Identyfikator wystąpienia", która jest "zawiera obszar roboczy". Użycie jest wyświetlane w kolumnie "Ilość zużyta", a jednostka dla każdego wpisu jest wyświetlana w kolumnie "Jednostka miary".  Więcej informacji można znaleźć, aby ułatwić [zrozumienie rachunku za pomocą platformy Microsoft Azure.](https://docs.microsoft.com/azure/billing/billing-understand-your-bill) 

## <a name="changing-pricing-tier"></a>Zmiana warstwy cenowej

Aby zmienić warstwę cenową usługi Log Analytics w obszarze roboczym, 

1. W witrynie Azure portal otwórz **usage i szacowane koszty** z obszaru roboczego, gdzie zobaczysz listę każdej z warstw cen dostępnych dla tego obszaru roboczego.

2. Przejrzyj szacowane koszty dla każdej z warstw cenowych. Oszacowanie to jest oparte na ostatnich 31 dniach użycia, więc ten kosztorys opiera się na ostatnich 31 dniach jest reprezentatywne dla typowego użycia. W poniższym przykładzie można zobaczyć, jak na podstawie wzorców danych z ostatnich 31 dni ten obszar roboczy będzie kosztować mniej w warstwie Płatności zgodnie z rzeczywistym krokiem (#1) w porównaniu do warstwy rezerwacji pojemności 100 GB/dzień (#2).  

    ![Warstwy cenowe](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. Po zapoznaniu się z szacowanymi kosztami na podstawie ostatnich 31 dni użytkowania, jeśli zdecydujesz się zmienić warstwę cenową, kliknij przycisk **Wybierz**.  

Można również [ustawić warstwę cenową za pośrednictwem usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) przy użyciu parametru `sku` (w`pricingTier` szablonie Usługi Azure Resource Manager). 

## <a name="legacy-pricing-tiers"></a>Starsze warstwy cenowe

Subskrypcje, które miały w nim zasób usługi Log Analytics lub usługa Application Insights przed 2 kwietnia 2018 r. lub są połączone z umową Enterprise Agreement, która rozpoczęła się przed 1 lutego 2019 r., będą nadal miały dostęp do korzystania ze starszych warstw cenowych: **Bezpłatna**, **Autonomiczna (na GB)** i **Na węzeł (OMS)**.  Obszary robocze w warstwie Bezpłatne ceny będą miały codzienne pozyskiwania danych ograniczone do 500 MB (z wyjątkiem typów danych zabezpieczeń zebranych przez usługę Azure Security Center), a przechowywanie danych jest ograniczone do 7 dni. Bezpłatna warstwa cenowa jest przeznaczona tylko do celów oceny. Obszary robocze w warstwach cenowych Autonomiczne lub Na węzeł mają przechowywanie konfigurowalne przez użytkownika od 30 do 730 dni.

Warstwa cenowa na węzeł pobiera opłaty za monitorowaną maszynę wirtualną (węzeł) na ziarnistość godzinną. Dla każdego monitorowanego węzła obszar roboczy jest przydzielany 500 MB danych dziennie, które nie są rozliczane. Ta alokacja jest agregowana na poziomie obszaru roboczego. Dane pochłonięta powyżej agregacji dziennej alokacji danych są rozliczane za GB jako przeciążenie danych. Należy zauważyć, że na rachunku usługa będzie **usługi Insight i Analytics** dla usługi Log Analytics użycia, jeśli obszar roboczy znajduje się w warstwie cenowej na węzeł. 

> [!TIP]
> Jeśli obszar roboczy ma dostęp do warstwy cenowej **Na węzeł,** ale zastanawiasz się, czy będzie to kosztować mniej w warstwie Płatności zgodnie z rzeczywistym użyciem, możesz [użyć poniższej kwerendy,](#evaluating-the-legacy-per-node-pricing-tier) aby łatwo uzyskać rekomendację. 

Obszary robocze utworzone przed kwietniem 2016 r. mogą również uzyskać dostęp do oryginalnych warstw cenowych **Standard** i **Premium,** które mają stałe przechowywanie danych odpowiednio na 30 i 365 dni. Nowych obszarów roboczych nie można utworzyć w warstwach cenowych **Standardowa** lub **Premium,** a jeśli obszar roboczy zostanie przeniesiony z tych warstw, nie można go przenieść z powrotem. 

Więcej szczegółów na temat ograniczeń warstwy cenowej można znaleźć [tutaj](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces).

> [!NOTE]
> Aby użyć uprawnień pochodzących z zakupu pakietu OMS E1 Suite, pakietu OMS E2 Suite lub dodatku OMS dla centrum systemowego, wybierz warstwę cenową Log Analytics *per node.*

## <a name="change-the-data-retention-period"></a>Change the data retention period (Zmienianie okresu przechowywania danych)

W poniższych krokach opisano sposób konfigurowania czasu, przez jaki dane dziennika są przechowywane w obszarze roboczym. Przechowywanie danych można skonfigurować od 30 do 730 dni (2 lata) dla wszystkich obszarów roboczych, chyba że używają starszej warstwy cenowej Bezpłatna. 

### <a name="default-retention"></a>Domyślne przechowywanie

Aby ustawić domyślne przechowywanie obszaru roboczego, 
 
1. W witrynie Azure portal z obszaru roboczego wybierz **pozycję Użycie i szacowane koszty** z lewego okienka.
2. Na stronie **Użycie i szacunkowe koszty** kliknij pozycję **Zarządzanie ilością danych** w górnej części strony.
3. W okienku przesuń suwak, aby zwiększyć lub zmniejszyć liczbę dni, a następnie kliknij przycisk **OK**.  Jeśli korzystasz z warstwy *bezpłatnej,* nie będzie można zmodyfikować okresu przechowywania danych i musisz uaktualnić do warstwy płatnej, aby kontrolować to ustawienie.

    ![Zmienianie ustawienia przechowywania danych obszaru roboczego](media/manage-cost-storage/manage-cost-change-retention-01.png)

Po obniżeniu retencji istnieje kilkudniowy okres prolongaty przed usunięciem najstarszych danych. 
    
Retencji można również [ustawić za pośrednictwem usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) przy użyciu parametru. `retentionInDays` Ponadto jeśli ustawisz przechowywanie danych na 30 dni, można wyzwolić natychmiastowe przeczyszczanie starszych danych przy użyciu parametru, `immediatePurgeDataOn30Days` co może być przydatne w scenariuszach związanych ze zgodnością. Ta funkcja jest dostępna tylko za pośrednictwem usługi Azure Resource Manager. 

Dwa typy `Usage` danych `AzureActivity` — i —— są domyślnie przechowywane przez 90 dni i nie ma żadnych opłat za to 90-dniowe przechowywanie. Te typy danych są również wolne od opłat za pojmowanie danych. 



### <a name="retention-by-data-type"></a>Przechowywanie według typu danych

Istnieje również możliwość określenia różnych ustawień przechowywania dla poszczególnych typów danych od 30 do 730 dni (z wyjątkiem obszarów roboczych w starszej warstwie bezpłatnej ceny). Każdy typ danych jest zasobem podrzędnym obszaru roboczego. Na przykład tabelę SecurityEvent można rozwiązać w [usłudze Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) w następujący sposób:

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Należy zauważyć, że typ danych (tabela) jest rozróżniana wielkość liter.  Aby uzyskać bieżące ustawienia przechowywania typu danych określonego typu danych (w tym przykładzie SecurityEvent), należy użyć:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

Aby uzyskać bieżące ustawienia przechowywania typu danych dla wszystkich typów danych w obszarze roboczym, po prostu pomiń określony typ danych, na przykład:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Aby ustawić przechowywanie określonego typu danych (w tym przykładzie SecurityEvent) na 730 dni,

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

Prawidłowe `retentionInDays` wartości dla są od 30 do 730.

Typów `Usage` `AzureActivity` danych i nie można ustawić z zachowaniem niestandardowego przechowywania. Będą one miały maksymalnie domyślnego przechowywania obszaru roboczego lub 90 dni. 

Doskonałym narzędziem do łączenia się bezpośrednio z usługą Azure Resource Manager w celu ustawienia przechowywania według typu danych jest narzędzie OSS [ARMclient](https://github.com/projectkudu/ARMClient).  Dowiedz się więcej o ARMclient z artykułów [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) i [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).  Oto przykład użycia ARMClient, ustawienie danych SecurityEvent do przechowywania 730 dni:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> Ustawienie przechowywania poszczególnych typów danych może służyć do zmniejszenia kosztów przechowywania danych.  W przypadku danych zebranych od października 2019 r. (po wydaniu tej funkcji) ograniczenie przechowywania niektórych typów danych może z czasem zmniejszyć koszty przechowywania.  W przypadku danych zebranych wcześniej ustawienie niższego przechowywania dla pojedynczego typu nie wpłynie na koszty przechowywania.  

## <a name="manage-your-maximum-daily-data-volume"></a>Zarządzanie maksymalną dzienną ilością danych

Możesz skonfigurować dzienny limit i ograniczyć dzienne pobłażanie dla swojego obszaru roboczego, ale należy uważać, ponieważ twoim celem nie powinno być osiągnięcie dziennego limitu.  W przeciwnym razie utracisz dane przez pozostałą część dnia, co może mieć wpływ na inne usługi i rozwiązania platformy Azure, których funkcjonalność może zależeć od aktualnych danych dostępnych w obszarze roboczym.  W rezultacie zdolność do obserwowania i odbierania alertów, gdy warunki zdrowotne zasobów wspierających usługi IT są zagrożone.  Dzienny limit jest przeznaczony do wykorzystania jako sposób zarządzania nieoczekiwanym wzrostem ilości danych z zarządzanych zasobów i pozostawanie w granicach limitu lub gdy chcesz ograniczyć nieplanowane opłaty dla obszaru roboczego.  

Wkrótce po osiągnięciu dziennego limitu zbieranie typów danych podlegania rozliczaniu zatrzymuje się na resztę dnia. (Opóźnienie związane z zastosowaniem dziennego pułapu może oznaczać, że limit nie jest stosowany tak dokładnie, jak określony dzienny poziom limitu). W górnej części strony dla wybranego obszaru roboczego usługi Log Analytics pojawia się baner ostrzegawczy, a zdarzenie operacji jest wysyłane do tabeli *Operacja* w kategorii **LogManagement.** Wznowienie zbierania danych po upływie czasu resetowania zdefiniowanego *w dziennym limicie zostanie ustawiony na .* Zaleca się zdefiniowanie reguły alertu na podstawie tego zdarzenia operacji, skonfigurowanej do powiadamiania o osiągnięciu dziennego limitu danych. 

> [!WARNING]
> Dzienny limit nie zatrzymuje zbierania danych z usługi Azure Security Center, z wyjątkiem obszarów roboczych, w których usługa Azure Security Center została zainstalowana przed 19 czerwca 2017 r. 

### <a name="identify-what-daily-data-limit-to-define"></a>Określ, jaki dzienny limit danych należy zdefiniować

Przejrzyj [użycie usługi Log Analytics i szacowane koszty,](usage-estimated-costs.md) aby zrozumieć trend pozyskiwania danych i jaki jest dzienny limit woluminu do zdefiniowania. Należy rozważyć z ostrożnością, ponieważ nie będzie w stanie monitorować swoje zasoby po osiągnięciu limitu. 

### <a name="set-the-daily-cap"></a>Ustawianie dziennego limitu

W poniższych krokach opisano sposób konfigurowania limitu do zarządzania ilością danych, które obszar roboczy usługi Log Analytics będzie pozyskiwania dziennie.  

1. W obszarze roboczym wybierz pozycję **Użycie i szacunkowe koszty** w lewym okienku.
2. Na stronie **Użycie i koszty szacunkowe** dla wybranego obszaru roboczego kliknij pozycję **Zarządzanie woluminami danych** u góry strony. 
3. Dzienny limit jest domyślnie **wyłączony,** kliknij **przycisk ON,** aby go włączyć, a następnie ustaw limit głośności danych w GB/dzień.

    ![Usługa Log Analytics konfiguruje limit danych](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Alert, gdy daily cap osiągnął

Chociaż prezentujemy wizualną wskazówkę w witrynie Azure portal, gdy próg limitu danych jest spełniony, to zachowanie nie musi być zgodne z tym, jak zarządzać problemami operacyjnymi wymagającymi natychmiastowej uwagi.  Aby otrzymać powiadomienie alertu, można utworzyć nową regułę alertów w usłudze Azure Monitor.  Aby dowiedzieć się więcej, [zobacz, jak tworzyć, wyświetlać alerty i zarządzać nimi](alerts-metric.md).

Aby rozpocząć, oto zalecane ustawienia alertu:

- Cel: wybierz zasób usługi Log Analytics
- Kryteria: 
   - Nazwa sygnału: Niestandardowe wyszukiwanie w dzienniku
   - Zapytanie wyszukiwania: Operacja | gdzie Detail ma "OverQuota"
   - Na podstawie: Liczba wyników
   - Stan: Większy niż
   - Próg: 0
   - Okres: 5 (minuty)
   - Częstotliwość: 5 (minuty)
- Nazwa reguły alertu: osiągnięto dzienny limit danych
- Ważność: Ostrzeżenie (Sev 1)

Po zdefiniowaniu alertu i osiągnięciu limitu alert jest wyzwalany i wykonuje odpowiedź zdefiniowaną w grupie akcji. Może powiadamiać zespół za pośrednictwem poczty e-mail i wiadomości tekstowych lub zautomatyzować działania za pomocą zestawów webhook, programów runbook automatyzacji lub [integracji z zewnętrznym rozwiązaniem ITSM.](itsmc-overview.md#create-itsm-work-items-from-azure-alerts) 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Rozwiązywanie problemów związanych z użyciem przekraczającym oczekiwania

Większe użycie jest spowodowane przez jedną lub obie z następujących przyczyn:
- Więcej węzłów niż oczekiwano wysyła dane do obszaru roboczego usługi Log Analytics
- Więcej danych niż oczekiwano wysyłanych do obszaru roboczego usługi Log Analytics (być może z powodu rozpoczęcia korzystania z nowego rozwiązania lub zmiany konfiguracji do istniejącego rozwiązania)

## <a name="understanding-nodes-sending-data"></a>Opis węzłów wysyłających dane

Aby zrozumieć liczbę węzłów zgłaszających bicie serca od agenta każdego dnia w ostatnim miesiącu,

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
Pobierz liczbę węzłów wysyłających dane w ciągu ostatnich 24 godzin użyć kwerendy: 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

Aby uzyskać listę węzłów wysyłających dowolne dane (i ilość danych wysyłanych przez każdy z nich) można użyć zapytania obserwowanego:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

> [!TIP]
> Użyj `union withsource = tt *` tych kwerend oszczędnie, ponieważ skanowanie w różnych typach danych są kosztowne do wykonania. Ta kwerenda zastępuje stary sposób wykonywania zapytań o informacje na komputerze typem danych Użycia.  

## <a name="understanding-ingested-data-volume"></a>Opis pogoń za ilością danych

Na **stronie Użycie i koszty szacunkowe** wykres *pozyskiwania danych na rozwiązanie* pokazuje całkowitą ilość wysłanych danych i ilość wysyłanych przez każde rozwiązanie. Dzięki temu można określić trendy, takie jak to, czy ogólne użycie danych (lub użycie przez określone rozwiązanie) rośnie, pozostaje stały lub maleje. 

### <a name="data-volume-for-specific-events"></a>Ilość danych dla określonych zdarzeń

Aby przyjrzeć się rozmiarowi przyjmowanych danych dla określonego zestawu zdarzeń, można `Event`zbadać określoną tabelę (w tym przykładzie), a następnie ograniczyć kwerendę do zdarzeń będących przedmiotem zainteresowania (w tym przykładzie zdarzenia o identyfikatorze 5145 lub 5156):

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

Należy zauważyć, `where IsBillable = true` że klauzula odfiltrowuje typy danych z niektórych rozwiązań, dla których nie ma opłaty za pojące. 

### <a name="data-volume-by-solution"></a>Ilość danych wg rozwiązania

Kwerenda używana do wyświetlania rozliczanego woluminu danych według rozwiązania w ciągu ostatniego miesiąca (z wyłączeniem ostatniego częściowego dnia) jest następująca:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

Klauzula `TimeGenerated` z jest tylko w celu zapewnienia, że środowisko kwerendy w witrynie Azure portal będzie patrzeć wstecz poza domyślne 24 godzin. Podczas korzystania z użycia `StartTime` `EndTime` typu danych i reprezentują przedziały czasu, dla których są prezentowane wyniki. 

### <a name="data-volume-by-type"></a>Objętość danych według typu

Można przejść do szczegółów, aby zobaczyć trendy danych według typu danych:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

Lub aby zobaczyć tabelę według rozwiązania i wpisz dla ostatniego miesiąca,

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>Wolumin danych według komputera

Typ `Usage` danych nie zawiera informacji na poziomie completer. Aby zobaczyć **rozmiar** pogoń za pomocą danych `_BilledSize` na komputer, użyj [właściwości](log-standard-properties.md#_billedsize), która zapewnia rozmiar w bajtach:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

`_IsBillable` [Właściwość](log-standard-properties.md#_isbillable) określa, czy pogoń za dane będą naliczane opłaty.

Aby wyświetlić **liczbę** zdarzeń rozliczanych po spożyciu na komputerze, użyj 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  | sort by eventCount nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Wolumin danych według zasobu, grupy zasobów lub subskrypcji platformy Azure

W przypadku danych z węzłów hostowanych na platformie Azure można uzyskać **rozmiar** pojąconych danych __na komputer__, użyj [właściwości](log-standard-properties.md#_resourceid)_ResourceId , która zapewnia pełną ścieżkę do zasobu:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

W przypadku danych z węzłów hostowanych na platformie Azure można uzyskać **rozmiar** pojąłchanych danych __na subskrypcję platformy Azure__, `_ResourceId` przeanalizować właściwość jako:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize BillableDataBytes = sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

Zmiana `subscriptionId` `resourceGroup` na spowoduje wyświetlenie naliczanego naliczonego naliczonego wolumenu danych przez grupę zasobów platformy Azure. 

> [!WARNING]
> Niektóre pola typu dane użycia, podczas gdy nadal w schemacie, zostały przestarzałe i ich wartości nie są już wypełniane. Są to **pola komputer,** a także pola związane z pozyskiwaniam (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** i **AverageProcessingTimeMs**.

### <a name="querying-for-common-data-types"></a>Wykonywanie zapytań dotyczących typowych typów danych

Aby głębiej zagłębić się w źródło danych dla określonego typu danych, oto kilka przydatnych przykładowych zapytań:

+ Rozwiązanie **zabezpieczające**
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ Rozwiązanie do **zarządzania dziennikami**
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ Typ danych **Perf**
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ Typ danych **Event**
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ Typ danych **Syslog**
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ Typ danych **AzureDiagnostics**
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

### <a name="tips-for-reducing-data-volume"></a>Wskazówki dotyczące zmniejszania ilości danych

Oto kilka sugestii dotyczących zmniejszenia ilości zebranych dzienników:

| Źródło dużego woluminu danych | Jak zmniejszyć wolumin danych |
| -------------------------- | ------------------------- |
| Zdarzenia zabezpieczeń            | Wybierz [pospolite lub minimalne zdarzenia zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier). <br> Zmień zasady inspekcji zabezpieczeń w celu zbierania tylko potrzebnych zdarzeń. W szczególności zastanów się nad koniecznością zbierania następujących zdarzeń: <br> - [inspekcja platformy filtrowania](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [inspekcja rejestru](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [inspekcja systemu plików](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [inspekcja obiektu jądra](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [inspekcja manipulowania dojściem](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - audyt magazynu wymiennego |
| Liczniki wydajności       | Zmień [konfigurację licznika wydajności](data-sources-performance-counters.md) w następujący sposób: <br> — Zmniejsz częstotliwość gromadzenia <br> — Zmniejsz liczbę liczników wydajności |
| Dzienniki zdarzeń                 | Zmień [konfigurację dziennika zdarzeń](data-sources-windows-events.md) w następujący sposób: <br> — Zmniejsz liczbę gromadzonych danych dzienników zdarzeń <br> — Zbieraj wyłącznie zdarzenia o wymaganym poziomie. Na przykład nie zbieraj zdarzeń na poziomie *Informacje*. |
| Dziennik systemu                     | Zmień [konfigurację dziennika systemu](data-sources-syslog.md) w następujący sposób: <br> — Zmniejsz liczbę urządzeń, z których zbierane są dane <br> — Zbieraj wyłącznie zdarzenia o wymaganym poziomie. Na przykład nie zbieraj zdarzeń na poziomie *Informacje* i *Debugowanie*. |
| AzureDiagnostics           | Zmień kolekcję dziennika zasobów, aby: <br> — zmniejszyć liczbę dzienników zasobów wysyłanych do usługi Log Analytics, <br> — zbierać tylko wymagane dzienniki. |
| Dane rozwiązań z komputerów, które nie wymagają rozwiązania | Kierowanie [rozwiązania](../insights/solution-targeting.md) służy do zbierania danych tylko z wymaganych grup komputerów. |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>Uzyskiwanie węzłów rozliczanych w warstwie cenowej Na węzeł

Aby uzyskać listę komputerów, które będą rozliczane jako węzły, jeśli obszar roboczy znajduje się w starszej warstwie cenowej Na węzeł, poszukaj węzłów, które wysyłają **rozliczane typy danych** (niektóre typy danych są bezpłatne). Aby to zrobić, `_IsBillable` należy użyć [właściwości](log-standard-properties.md#_isbillable) i użyj lewego pola w pełni kwalifikowanej nazwy domeny. Spowoduje to zwraca liczbę komputerów z naliczonymi danymi na godzinę (co jest szczegółowością, w której węzły są liczone i rozliczane):

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>Obliczanie liczby węzłów zabezpieczeń i automatyzacji

Jeśli korzystasz z warstwy cenowej "Na węzeł (OMS)", naliczana jest opłata na podstawie liczby używanych węzłów i rozwiązań, liczba węzłów usługi Insights i Analytics, za które są naliczane opłaty, zostanie wyświetlona w tabeli na stronie **Użycie i Koszt szacowany.**  

Aby wyświetlić liczbę różnych węzłów zabezpieczeń, można użyć kwerendy:

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

Aby wyświetlić liczbę różnych węzłów automatyzacji, użyj kwerendy:

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>Ocena starszej warstwy cenowej Na węzeł

Decyzja o tym, czy obszary robocze z dostępem do starszej warstwy cenowej **na węzeł** są lepiej sygnalizowania w tej warstwie lub w bieżącej warstwie **Płatności zgodnie z rzeczywistym użyciem** lub **Rezerwacji pojemności,** jest często trudna do oceny dla klientów.  Obejmuje to zrozumienie kompromisu między kosztem stałym na monitorowany węzeł w warstwie cenowej Na węzeł a jego uwzględniony przydział danych 500 MB/węzeł/dzień i koszt tylko płacenia za pojone dane w warstwie Płatności zgodnie z rzeczywistym użyciem (na GB). 

Aby ułatwić tę ocenę, następujące zapytanie może służyć do zalecenia dla warstwy cen optymalne na podstawie wzorców użycia obszaru roboczego.  Ta kwerenda analizuje monitorowane węzły i dane ponajęte do obszaru roboczego w ciągu ostatnich 7 dni i dla każdego dnia ocenia, która warstwa cenowa byłaby optymalna. Aby użyć kwerendy, należy określić, czy obszar roboczy `workspaceHasSecurityCenter` `true` używa `false`usługi Azure Security Center, ustawiając lub , a następnie (opcjonalnie) aktualizując ceny za węzeł i na GB, które otrzymuje organizacja. 

```kusto
// Set these parameters before running query
let workspaceHasSecurityCenter = true;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your price per node / month 
let PerGBPrice = 2.30; // Enter your price per GB 
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
union withsource = tt * 
| where TimeGenerated >= startofday(now(-7d)) and TimeGenerated < startofday(now())
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join (
    Heartbeat 
    | where TimeGenerated >= startofday(now(-7d)) and TimeGenerated < startofday(now())
    | where Computer != ""
    | summarize ASCnodesPerHour = dcount(Computer) by bin(TimeGenerated, 1h) 
    | extend ASCnodesPerHour = iff(workspaceHasSecurityCenter, ASCnodesPerHour, 0)
    | summarize ASCnodesPerDay = sum(ASCnodesPerHour)/24.  by day=bin(TimeGenerated, 1d)   
) on day
| join (
    Usage 
    | where TimeGenerated > ago(8d)
    | where StartTime >= startofday(now(-7d)) and EndTime < startofday(now())
    | where IsBillable == true
    | extend NonSecurityData = iff(DataType !in (SecurityDataTypes), Quantity, 0.)
    | extend SecurityData = iff(DataType in (SecurityDataTypes), Quantity, 0.)
    | summarize DataGB=sum(Quantity)/1000., NonSecurityDataGB=sum(NonSecurityData)/1000., SecurityDataGB=sum(SecurityData)/1000. by day=bin(StartTime, 1d)  
) on day
| extend AvgGbPerNode =  NonSecurityDataGB / nodesPerDay
| extend PerGBDailyCost = iff(workspaceHasSecurityCenter,
             (NonSecurityDataGB + max_of(SecurityDataGB - 0.5*ASCnodesPerDay, 0.)) * PerGBPrice,
             DataGB * PerGBPrice)
| extend OverageGB = iff(workspaceHasSecurityCenter, 
             max_of(DataGB - 0.5*nodesPerDay - 0.5*ASCnodesPerDay, 0.), 
             max_of(DataGB - 0.5*nodesPerDay, 0.))
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerGBPrice
| extend Recommendation = iff(PerNodeDailyCost < PerGBDailyCost, "Per Node tier", 
             iff(NonSecurityDataGB > 85., "Capacity Reservation tier", "Pay-as-you-go (Per GB) tier"))
| project day, nodesPerDay, ASCnodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, Recommendation | sort by day asc
| project day, Recommendation // Comment this line to see details
| sort by day asc
```

Ta kwerenda nie jest dokładną replikacją sposobu obliczania użycia, ale będzie działać w celu zapewnienia zaleceń warstwy cenowej w większości przypadków.  

## <a name="create-an-alert-when-data-collection-is-high"></a>Tworzenie alertu, gdy zbieranie danych jest wysokie

W tej sekcji opisano sposób tworzenia alertu w sytuacji, gdy:
- Ilość danych przekracza określoną wartość.
- Przewiduje się, że ilość danych przekroczy określoną wartość.

Alerty platformy Azure obsługują [alerty dziennika](alerts-unified-log.md), które korzystają z zapytań wyszukiwania. 

Poniższe zapytanie daje rezultat, jeśli w ciągu ostatnich 24 godzin zebrano więcej niż 100 GB danych:

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type 
| where DataGB > 100
```

Następujące zapytanie używa prostej formuły umożliwiającej przewidywanie, kiedy w ciągu jednego dnia zostanie wysłanych więcej niż 100 GB danych: 

```kusto
union withsource = $table Usage 
| where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true 
| extend Type = $table 
| summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type 
| where EstimatedGB > 100
```

Aby utworzyć alerty dotyczące innego woluminu danych, zmień w zapytaniach wartość 100 na liczbę gigabajtów, po przekroczeniu której ma zostać wyświetlony alert.

Wykonaj kroki opisane w sekcji dotyczącej [tworzenia nowego alertu dziennika](alerts-metric.md), aby otrzymywać powiadomienia w sytuacji, gdy ilość zebranych danych jest większa niż oczekiwano.

Podczas tworzenia alertu dla pierwszego zapytania odnoszącego się do przypadku, gdy w ciągu 24 godzin występuje więcej niż 100 GB danych, ustaw wartości elementów:  

- **Zdefiniuj warunek alertu** — określ obszar roboczy usługi Log Analytics jako element docelowy zasobu.
- **Kryteria alertu** — określ następujące informacje:
   - **Nazwa sygnału** — wybierz pozycję **Przeszukiwanie dzienników niestandardowych**
   - **Zapytanie wyszukiwania** na `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize DataGB = sum((Quantity / 1000.)) by Type | where DataGB > 100`
   - **Alert logiki****opiera się na** *liczbie wyników*, a **warunek** jest *większy niż ***próg ** wynoszący *0*
   - **Okres** o długości *1440* minut i **częstotliwość alertów** o wartości *60* minut, ponieważ dane użycia są aktualizowane tylko raz na godzinę.
- **Zdefiniuj szczegóły alertu** — określ następujące informacje:
   - **Nazwa** na *Data volume greater than 100 GB in 24 hours* (Wolumin danych większy niż 100 GB w ciągu 24 godzin)
   - **Ważność** na *Ostrzeżenie*

Określ istniejącą [grupę akcji](action-groups.md) lub utwórz nową, tak aby otrzymywać powiadomienie, gdy alert dziennika spełni kryteria.

Podczas tworzenia alertu dla drugiego zapytania dotyczącego przypadku, w którym przewiduje się, że w ciągu 24 godzin wystąpi więcej niż 100 GB danych, ustaw wartości elementów:

- **Zdefiniuj warunek alertu** — określ obszar roboczy usługi Log Analytics jako element docelowy zasobu.
- **Kryteria alertu** — określ następujące informacje:
   - **Nazwa sygnału** — wybierz pozycję **Przeszukiwanie dzienników niestandardowych**
   - **Zapytanie wyszukiwania** na `union withsource = $table Usage | where QuantityUnit == "MBytes" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | extend Type = $table | summarize EstimatedGB = sum(((Quantity * 8) / 1000.)) by Type | where EstimatedGB > 100`
   - **Alert logiki****opiera się na** *liczbie wyników*, a **warunek** jest *większy niż ***próg ** wynoszący *0*
   - **Okres** o długości *180* minut i **częstotliwość alertów** o wartości *60* minut, ponieważ dane użycia są aktualizowane tylko raz na godzinę.
- **Zdefiniuj szczegóły alertu** — określ następujące informacje:
   - **Nazwa** na *Data volume expected to greater than 100 GB in 24 hours* (Oczekiwany wolumin danych większy niż 100 GB w ciągu 24 godzin)
   - **Ważność** na *Ostrzeżenie*

Określ istniejącą [grupę akcji](action-groups.md) lub utwórz nową, tak aby otrzymywać powiadomienie, gdy alert dziennika spełni kryteria.

Po otrzymaniu alertu wykonaj kroki przedstawione w poniższej sekcji, aby rozwiązać problemy związane z większym niż oczekiwano użyciem.

## <a name="data-transfer-charges-using-log-analytics"></a>Opłaty za transfer danych za pomocą usługi Log Analytics

Wysyłanie danych do usługi Log Analytics może naliczać opłaty za przepustowość danych. Zgodnie z opisem na [stronie cennik przepustowość platformy Azure](https://azure.microsoft.com/pricing/details/bandwidth/)transfer danych między usługami platformy Azure znajdującymi się w dwóch regionach naliczany jako transfer danych wychodzących według normalnej stawki. Transfer danych przychodzących jest bezpłatny. Jednak opłata ta jest bardzo mała (niewiele %) w porównaniu z kosztami pozyskiwania danych usługi Log Analytics. W związku z tym kontrolowanie kosztów usługi Log Analytics musi koncentrować się na ilości połkniętych danych, a my mamy wskazówki, które pomogą zrozumieć, że [tutaj](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume).   


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Rozwiązywanie problemów z tym, dlaczego usługa Log Analytics nie zbiera już danych

Jeśli korzystasz ze starszej warstwy cen bezpłatnych i wysłałeś więcej niż 500 MB danych w ciągu dnia, zbieranie danych zostanie zatrzymane przez resztę dnia. Osiągnięcie dziennego limitu jest częstym powodem, dla którego usługa Log Analytics przestaje zbierać dane lub danych wydaje się brakować.  Usługa Log Analytics tworzy zdarzenie typu Operacja po uruchomieniu i zatrzymaniu zbierania danych. Uruchom następującą kwerendę w wyszukiwaniu, aby sprawdzić, czy osiągasz dzienny limit i brakujące dane: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Po zatrzymaniu zbierania danych, OperationStatus jest **ostrzeżenie**. Po uruchomieniu zbierania danych OperationStatus jest **powodem**. W poniższej tabeli opisano przyczyny zatrzymania zbierania danych i sugerowaną akcję wznowienia zbierania danych:  

|Zatrzymanie odbioru przyczyn| Rozwiązanie| 
|-----------------------|---------|
|Dzienny limit starszego bezpłatnego poziomu cenowego osiągnięty |Poczekaj do następnego dnia, aby kolekcja została automatycznie ponownie uruchomiona lub zmień na płatną warstwę cenową.|
|Osiągnięto dzienny limit przestrzeni roboczej|Poczekaj na automatyczne ponowne uruchomienie kolekcji lub zwiększ dzienny limit głośności danych opisany w zarządzaniu maksymalnym dziennym woluminem danych. Dzienny czas resetowania limitu jest wyświetlany na stronie **Zarządzanie woluminami danych.** |
|Subskrypcja platformy Azure jest w stanie zawieszenia z powodu:<br> Bezpłatna wersja próbna zakończona<br> Przepustka platformy Azure wygasła<br> Osiągnięto miesięczny limit wydatków (na przykład w przypadku subskrypcji MSDN lub Visual Studio)|Konwersja na płatną subskrypcję<br> Usuń limit lub poczekaj, aż limit się zresetuje|

Aby otrzymywać powiadomienia po zatrzymaniu zbierania danych, należy wykonać czynności opisane w *funkcji Utwórz alert o dziennym korcie danych,* aby otrzymywać powiadomienia o zatrzymaniu zbierania danych. Czynności opisane w [temacie Tworzenie grupy akcji umożliwia](action-groups.md) skonfigurowanie akcji wiadomości e-mail, elementu webhook lub elementu runbook dla reguły alertu. 

## <a name="limits-summary"></a>Podsumowanie limitów

Istnieją pewne dodatkowe limity analizy dzienników, z których niektóre zależą od warstwy cenowej usługi Log Analytics. Są one udokumentowane [tutaj](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces).


## <a name="next-steps"></a>Następne kroki

- Zobacz [Wyszukiwanie dzienników w dziennikach monitora platformy Azure,](../log-query/log-query-overview.md) aby dowiedzieć się, jak używać języka wyszukiwania. Możesz użyć zapytań wyszukiwania w celu przeprowadzenia dodatkowej analizy danych użycia.
- Wykonaj kroki opisane w sekcji dotyczącej [tworzenia nowego alertu dziennika](alerts-metric.md), aby otrzymywać powiadomienie, gdy kryteria wyszukiwania zostaną spełnione.
- Kierowanie [rozwiązania](../insights/solution-targeting.md) służy do zbierania danych tylko z wymaganych grup komputerów.
- Aby skonfigurować zasadę skutecznego zbierania zdarzeń, zapoznaj się z [zasadami filtrowania usługi Azure Security Center](../../security-center/security-center-enable-data-collection.md).
- Zmień [konfigurację licznika wydajności](data-sources-performance-counters.md).
- Aby zmodyfikować ustawienia kolekcji zdarzeń, przejrzyj [konfigurację dziennika zdarzeń](data-sources-windows-events.md).
- Aby zmodyfikować ustawienia kolekcji syslog, przejrzyj [konfigurację syslogu](data-sources-syslog.md).