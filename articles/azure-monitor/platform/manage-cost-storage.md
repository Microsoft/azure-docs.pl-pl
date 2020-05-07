---
title: Zarządzanie użyciem i kosztami dzienników Azure Monitor
description: Dowiedz się, jak zmienić plan cenowy i zarządzać ilością danych i zasadami przechowywania dla obszaru roboczego Log Analytics w Azure Monitor.
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
ms.date: 05/04/2020
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 601f1c224d6e1d756c27dc2478951682ce6bb4fd
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82854767"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Zarządzanie użyciem i kosztami za pomocą dzienników Azure Monitor

> [!NOTE]
> W tym artykule opisano sposób zrozumienia i kontrolowania kosztów Azure Monitor dzienników. W powiązanym artykule, [monitorowaniu użycia i szacowanych kosztów](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) opisano sposób wyświetlania użycia i szacowane koszty w wielu funkcjach monitorowania platformy Azure dla różnych modeli cen. Wszystkie ceny i koszty pokazane w tym artykule są wyłącznie na przykład. 

Dzienniki Azure Monitor są przeznaczone do skalowania i obsługi zbierania, indeksowania i przechowywania dużych ilości danych dziennie z dowolnego źródła w przedsiębiorstwie lub wdrożonego na platformie Azure.  Chociaż może to być podstawowy sterownik dla Twojej organizacji, kosztem wydajności jest ostatecznie podstawowy sterownik. W tym celu należy zrozumieć, że koszt obszaru roboczego Log Analytics nie jest oparty tylko na ilości zbieranych danych, zależy również od wybranego planu i od czasu wybrania do przechowywania danych generowanych przez połączone źródła.  

W tym artykule opisano, jak można aktywnie monitorować pozyskiwane ilości danych i przyrosty magazynu oraz definiować ograniczenia kontroli nad tymi kosztami. 

## <a name="pricing-model"></a>Model cen

Domyślna cena dla Log Analytics jest modelem **płatności zgodnie z rzeczywistym** użyciem na podstawie ilości danych pozyskiwanych i opcjonalnie do dłuższego przechowywania danych. Ilość danych jest mierzona jako rozmiar danych, które będą przechowywane. Każdy obszar roboczy Log Analytics jest rozliczany jako osobna usługa i przyczynia się do rozliczenia za subskrypcję platformy Azure. Ilość pozyskiwania danych może być znaczna w zależności od następujących czynników: 

  - Liczba włączonych rozwiązań zarządzania i ich konfiguracja
  - Liczba monitorowanych maszyn wirtualnych
  - Typ danych zbieranych z każdej monitorowanej maszyny wirtualnej 
  
Oprócz modelu "płatność zgodnie z rzeczywistym użyciem" Log Analytics ma warstwy **rezerwacji pojemności** , które umożliwiają oszczędności o 25% w porównaniu z ceną płatność zgodnie z rzeczywistym użyciem. Cennik rezerwacji zdolności produkcyjnych umożliwia zakupienie rezerwacji rozpoczynającej się o 100 GB/dzień. Każde użycie powyżej poziomu rezerwacji będzie rozliczane według stawki płatności zgodnie z rzeczywistym użyciem. Warstwy rezerwacji pojemności mają 31-dniowy okres zobowiązania. W okresie obowiązywania zobowiązania można zmienić warstwę rezerwacji wyższego poziomu (co spowoduje ponowne uruchomienie 31-dniowego okresu zobowiązań), ale nie będzie można wrócić do warstwy rezerwacji zgodnie z rzeczywistym użyciem lub do niższej wersji, dopóki upłynie okres obowiązywania zobowiązania. Opłaty za warstwy rezerwacji pojemności są wykonywane codziennie. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/monitor/) na temat log Analytics płatność zgodnie z rzeczywistym użyciem i Cennik rezerwacji zdolności produkcyjnych. 

We wszystkich warstwach cenowych wolumin danych jest obliczany na podstawie ciągu reprezentującego dane, które są przygotowane do przechowywania. Niektóre [właściwości wspólne dla wszystkich typów danych](https://docs.microsoft.com/azure/azure-monitor/platform/log-standard-properties) nie są uwzględniane podczas obliczania rozmiaru zdarzenia, w `_ResourceId`tym, `_ItemId` `_IsBillable` i. `_BilledSize`

Należy również pamiętać, że niektóre rozwiązania, takie jak [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/), [wskaźnik platformy Azure](https://azure.microsoft.com/pricing/details/azure-sentinel/) i [Zarządzanie konfiguracją](https://azure.microsoft.com/pricing/details/automation/) mają własne modele cen. 

### <a name="log-analytics-clusters"></a>Klastry Log Analytics

Klastry Log Analytics to kolekcje obszarów roboczych w jednym zarządzanym klastrze Eksplorator danych platformy Azure, które obsługują zaawansowane scenariusze, takie jak [klucze zarządzane przez klienta](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys).  Klastry Log Analytics obsługują tylko model cen rezerwacji pojemności, zaczynając od 1000 GB/dzień z rabatem w wysokości 25% w porównaniu z cennikiem z opcją płatność zgodnie z rzeczywistym użyciem. Każde użycie powyżej poziomu rezerwacji będzie rozliczane według stawki płatności zgodnie z rzeczywistym użyciem. Rezerwacja pojemności klastra obejmuje 31-dniowy okres zobowiązania po zwiększeniu poziomu rezerwacji. W okresie obowiązywania zobowiązania nie można zmniejszyć poziomu rezerwacji pojemności, ale można go zwiększyć w dowolnym momencie. Dowiedz się więcej na temat [tworzenia klastrów log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys#create-cluster-resource) i [kojarzenia z nią obszarów roboczych](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys#workspace-association-to-cluster-resource).  

Poziom rezerwacji pojemności klastra jest konfigurowany za pośrednictwem programowo z Azure Resource Manager przy `Capacity` użyciu parametru `Sku`w obszarze. Wartość `Capacity` jest określona w jednostkach GB i może mieć wartości 1000 GB/dzień lub więcej w przyrostach wynoszących 100 GB/dzień. [Tutaj opisano tutaj](https://docs.microsoft.com/azure/azure-monitor/platform/customer-managed-keys#create-cluster-resource). Jeśli klaster wymaga rezerwacji powyżej 2000 GB/dzień, skontaktuj się z nami [LAIngestionRate@microsoft.com](mailto:LAIngestionRate@microsoft.com)pod adresem.

Ponieważ rozliczanie danych pobieranych odbywa się na poziomie klastra, obszary robocze skojarzone z klastrem nie mają już warstwy cenowej. Pobrane ilości danych z każdego obszaru roboczego skojarzonego z klastrem są agregowane w celu obliczenia dziennego rachunku dla klastra. Należy pamiętać, że alokacje na węzeł [Azure Security Center](https://docs.microsoft.com/azure/security-center/) są stosowane na poziomie obszaru roboczego przed agregacją zagregowanych danych we wszystkich obszarach roboczych w klastrze. Przechowywanie danych jest nadal rozliczane na poziomie obszaru roboczego. Należy pamiętać, że podczas tworzenia klastra rozliczenia są rozliczane, niezależnie od tego, czy obszary robocze zostały skojarzone z klastrem. 

## <a name="estimating-the-costs-to-manage-your-environment"></a>Szacowanie kosztów związanych z zarządzaniem środowiskiem 

Jeśli jeszcze nie korzystasz z dzienników Azure Monitor, możesz użyć [kalkulatora cen Azure monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) , aby oszacować koszt używania log Analytics. Zacznij od wprowadzenia "Azure Monitor" w polu wyszukiwania i kliknięcia kafelka Azure Monitor wyników. Przewiń w dół stronę do Azure Monitor i wybierz pozycję Log Analytics z listy rozwijanej Typ.  W tym miejscu możesz wprowadzić liczbę maszyn wirtualnych i GB danych, które mają być zbierane z każdej maszyny wirtualnej. Zwykle od 1 do 3 GB danych miesiąca jest pobierana z typowej maszyny wirtualnej platformy Azure. Jeśli już już oceniasz dzienniki Azure Monitor, możesz użyć statystyk danych z własnego środowiska. Poniżej znajdują się informacje na temat określania [liczby monitorowanych maszyn wirtualnych](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-nodes-sending-data) i [ilości danych pobieranych przez obszar roboczy](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume). 

## <a name="understand-your-usage-and-estimate-costs"></a>Zapoznaj się z użyciem i szacunkowymi kosztami

Jeśli używasz dzienników Azure Monitor teraz, możesz łatwo zrozumieć, jakie koszty są prawdopodobnie oparte na ostatnich wzorcach użycia. W tym celu należy użyć **log Analytics użycia i szacowanych kosztów** , aby przejrzeć i analizować użycie danych. Pokazuje to ilość danych zbieranych przez każde rozwiązanie, ilość przechowywanych danych i oszacowanie kosztów na podstawie ilości danych pozyskiwanych i dodatkowego okresu przechowywania poza uwzględnioną ilością.

![Użycie i szacunkowe koszty](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Aby eksplorować dane w bardziej szczegółowy sposób, kliknij ikonę w prawym górnym rogu jednego z wykresów na stronie **użycie i szacowane koszty** . Teraz możesz korzystać z tego zapytania, aby poznać więcej szczegółów dotyczących użycia.  

![Widok dzienników](media/manage-cost-storage/logs.png)

Na stronie **użycie i szacowane koszty** możesz sprawdzić ilość danych w danym miesiącu. Obejmuje to wszystkie dane odebrane i zachowane w obszarze roboczym Log Analytics.  Kliknij pozycję **szczegóły użycia** w górnej części strony, aby wyświetlić pulpit nawigacyjny użycie z informacjami o trendach ilości danych według źródła, komputerów i oferty. Aby wyświetlić i ustawić dzienny limit lub zmodyfikować okres przechowywania, kliknij przycisk **Zarządzanie ilością danych**.
 
Opłaty za Log Analytics są dodawane do rachunku na korzystanie z platformy Azure. Szczegóły rachunku na korzystanie z platformy Azure można znaleźć w sekcji rozliczenia Azure Portal lub [Azure Billing Portal](https://account.windowsazure.com/Subscriptions).  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Wyświetlanie Log Analytics użycia na rachunku na platformie Azure 

Platforma Azure oferuje bardzo przydatne funkcje w [Azure Cost Management i centrum rozliczeń](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) . Na przykład funkcja "analiza kosztów" umożliwia wyświetlanie wydatków dotyczących zasobów platformy Azure. Dodanie filtru według typu zasobu (do firmy Microsoft. operationalinsights/Workspace for Log Analytics) umożliwi śledzenie wydatków.

Więcej informacji o użyciu można uzyskać, [pobierając użycie z Azure Portal](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal). W pobranym arkuszu kalkulacyjnym można zobaczyć użycie na zasób platformy Azure (np. Log Analytics obszar roboczy) dziennie. W tym arkuszu kalkulacyjnym programu Excel można znaleźć informacje o wykorzystaniu z obszarów roboczych Log Analytics, wybierając najpierw filtrowanie w kolumnie "kategoria licznika", aby pokazać "szczegółowe dane i analizy" (używane przez niektóre starsze warstwy cenowe) i "Log Analytics", a następnie dodać filtr w kolumnie "identyfikator wystąpienia", która jest "zawiera obszar roboczy". Użycie jest wyświetlane w kolumnie "zużyta ilość", a jednostka dla każdego wpisu jest pokazywana w kolumnie "jednostka miary".  Dostępne są więcej szczegółów, które ułatwiają [zrozumienie Microsoft Azure rachunku](https://docs.microsoft.com/azure/billing/billing-understand-your-bill). 

## <a name="changing-pricing-tier"></a>Zmienianie warstwy cenowej

Aby zmienić warstwę cenową Log Analytics w obszarze roboczym, 

1. W Azure Portal Otwórz **i szacowane koszty** w obszarze roboczym, w którym zostanie wyświetlona lista wszystkich warstw cenowych dostępnych dla tego obszaru roboczego.

2. Przejrzyj szacowane koszty dla każdej warstwy cenowej. To oszacowanie dotyczy ostatnich 31 dni użytkowania, więc oszacowanie kosztów polega na wykorzystaniu ostatnich 31 dni reprezentatywnych dla typowego użycia. W poniższym przykładzie można zobaczyć, jak, na podstawie wzorców danych z ostatnich 31 dni, ten obszar roboczy będzie tańszy w warstwie płatność zgodnie z rzeczywistym użyciem (#1) w porównaniu z warstwą rezerwacji (#2) 100 GB/dzień.  

    ![Warstwy cenowe](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. Po przejrzeniu szacowanych kosztów na podstawie ostatnich 31 dni użytkowania, jeśli zdecydujesz się zmienić warstwę cenową, kliknij pozycję **Wybierz**.  

Możesz również [ustawić warstwę cenową za pośrednictwem Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) przy `sku` użyciu parametru`pricingTier` (w szablonie Azure Resource Manager). 

## <a name="legacy-pricing-tiers"></a>Starsze warstwy cenowe

Subskrypcje, w których wystąpiły obszary robocze Log Analytics lub Application Insights zasobów, przed 2 kwietnia 2018 lub połączone z Umowa Enterprise, które zostały uruchomione przed 1 lutego 2019, nadal będą miały dostęp do korzystania ze starszych warstw cenowych: **bezpłatna**, **autonomiczna (za GB)** i **na węzeł (OMS)**.  Obszary robocze w warstwie cenowej bezpłatna będą mieć dzienne pozyskiwanie danych ograniczone do 500 MB (z wyjątkiem typów danych zabezpieczeń zbieranych przez Azure Security Center), a przechowywanie danych jest ograniczone do 7 dni. Warstwa cenowa bezpłatna jest przeznaczona tylko do celów ewaluacyjnych. Obszary robocze w warstwach autonomicznych lub na węzeł są dostępne przez użytkownika w sposób konfigurowalny od 30 do 730 dni.

Opłaty za warstwę cenową na węzeł na monitorowaną maszynę wirtualną (węzeł) mają stopień szczegółowości godzin. Dla każdego monitorowanego węzła obszar roboczy ma przydzieloną 500 MB danych dziennie, które nie są rozliczane. Ta alokacja jest agregowana na poziomie obszaru roboczego. Dane pozyskane powyżej zagregowanego dziennego przydziału danych są rozliczane za GB jako nadwyżkowe dane. Należy pamiętać, że na rachunku usługa zostanie **Insight and Analytics** do log Analytics użycie, jeśli obszar roboczy znajduje się w warstwie cenowej węzła na węzeł. 

> [!TIP]
> Jeśli Twój obszar roboczy ma dostęp do warstwy cenowej **węzła** , ale zastanawiasz się, czy w warstwie płatność zgodnie z rzeczywistym użyciem jest to tańsze, możesz [użyć poniższego zapytania](#evaluating-the-legacy-per-node-pricing-tier) , aby łatwo uzyskać zalecenie. 

Obszary robocze utworzone przed kwietnia 2016 mogą również uzyskiwać dostęp do oryginalnych warstw cenowych **Standard** i **Premium** , które mają odpowiednio stałe okresy 30 i 365. Nie można tworzyć nowych obszarów roboczych w warstwach cenowych **standardowa** lub **Premium** , a jeśli obszar roboczy jest przenoszony z tych warstw, nie można go przenieść z powrotem. 

Więcej szczegółów dotyczących ograniczeń warstwy cenowej można znaleźć [tutaj](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces).

> [!NOTE]
> Aby użyć uprawnień, które pochodzą z zakupu pakietu OMS E1, pakietu OMS E2 lub dodatku OMS dla programu System Center, wybierz warstwę cenową Log Analytics *na węzeł* .

## <a name="change-the-data-retention-period"></a>Change the data retention period (Zmienianie okresu przechowywania danych)

W poniższych krokach opisano sposób konfigurowania czasu przechowywania danych dziennika w obszarze roboczym. Przechowywanie danych można skonfigurować od 30 do 730 dni (2 lata) dla wszystkich obszarów roboczych, chyba że korzystają z starszej warstwy cenowej bezpłatna. 

### <a name="default-retention"></a>Domyślne przechowywanie

Aby ustawić domyślne przechowywanie dla obszaru roboczego, 
 
1. W Azure Portal w obszarze roboczym wybierz pozycję **użycie i szacowane koszty** w okienku po lewej stronie.
2. Na stronie **Użycie i szacunkowe koszty** kliknij pozycję **Zarządzanie ilością danych** w górnej części strony.
3. W okienku przesuń suwak, aby zwiększyć lub zmniejszyć liczbę dni, a następnie kliknij przycisk **OK**.  W przypadku korzystania z warstwy *bezpłatna* nie będzie można modyfikować okresu przechowywania danych i należy przeprowadzić uaktualnienie do warstwy płatnej w celu kontrolowania tego ustawienia.

    ![Zmień ustawienie przechowywania danych obszaru roboczego](media/manage-cost-storage/manage-cost-change-retention-01.png)

Gdy przechowywanie zostanie obniżone, istnieje kilka okresów prolongaty przed usunięciem najstarszych danych. 
    
Przechowywanie można również [ustawić za pośrednictwem Azure Resource Manager](https://docs.microsoft.com/azure/azure-monitor/platform/template-workspace-configuration#configure-a-log-analytics-workspace) przy użyciu `retentionInDays` parametru. Ponadto w przypadku ustawienia przechowywania danych na 30 dni można wyzwolić natychmiastowe przeczyszczanie starszych danych przy użyciu `immediatePurgeDataOn30Days` parametru, co może być przydatne w scenariuszach związanych ze zgodnością. Ta funkcja jest udostępniana tylko przez Azure Resource Manager. 

Dwa typy danych — `Usage` i `AzureActivity` --są domyślnie zachowywane przez 90 dni i nie są naliczane opłaty za korzystanie z tego 90go okresu przechowywania. Te typy danych są również wolne od opłat za pozyskiwanie danych. 



### <a name="retention-by-data-type"></a>Przechowywanie według typu danych

Można również określić różne ustawienia przechowywania dla poszczególnych typów danych od 30 do 730 dni (z wyjątkiem obszarów roboczych w starszej warstwie cenowej bezpłatna). Każdy typ danych jest zasobem podrzędnym obszaru roboczego. Na przykład można [rozAzure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) tabeli SecurityEvent jako:

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Należy pamiętać, że typ danych (tabela) uwzględnia wielkość liter.  Aby uzyskać bieżące ustawienia przechowywania typu danych dla określonego typu danych (w tym przykładzie SecurityEvent), użyj:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

Aby uzyskać bieżące ustawienia przechowywania typu danych dla wszystkich typów danych w obszarze roboczym, wystarczy pominąć określony typ danych, na przykład:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

Aby ustawić przechowywanie określonego typu danych (w tym przykładzie SecurityEvent) na 730 dni, wykonaj

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

Prawidłowe wartości `retentionInDays` to od 30 do 730.

Nie `Usage` można `AzureActivity` ustawić typów danych i przechowywania niestandardowego. Zajmie to maksymalny domyślny okres przechowywania obszaru roboczego lub 90 dni. 

Doskonałym narzędziem do nawiązywania bezpośredniego połączenia z Azure Resource Manager, aby ustawić przechowywanie według typu danych, jest narzędzie OSS [ARMclient](https://github.com/projectkudu/ARMClient).  Dowiedz się więcej na temat ARMclient z artykułów przez [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) i [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).  Oto przykład użycia ARMClient, ustawiając dane SecurityEvent na 730 dni:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> Ustawienie przechowywania poszczególnych typów danych może służyć do zmniejszania kosztów przechowywania danych.  W przypadku danych zbieranych od października 2019 (gdy ta funkcja została wydana) zmniejszenie ilości danych w czasie przechowywania może zmniejszyć koszt przechowywania w miarę upływu czasu.  W przypadku danych zbieranych wcześniej ustawienie mniejszego okresu przechowywania dla danego typu nie wpłynie na koszty przechowywania.  

## <a name="manage-your-maximum-daily-data-volume"></a>Zarządzanie maksymalnym dziennym woluminem danych

Można skonfigurować dzienny limit i ograniczyć dzienne pozyskiwanie obszaru roboczego, ale należy zachować ostrożność, ponieważ cel nie powinien być osiągnięty w dziennym limicie.  W przeciwnym razie utracisz dane przez pozostałą część dnia, co może mieć wpływ na inne usługi i rozwiązania platformy Azure, których funkcjonalność może zależeć od aktualnych danych dostępnych w obszarze roboczym.  W efekcie można obserwować i otrzymywać alerty w przypadku, gdy ma to wpływ na warunki kondycji zasobów obsługujących usługi IT.  Dzienny limit jest przeznaczony do użycia jako sposób zarządzania nieoczekiwanym wzrostem ilości danych z zarządzanych zasobów i pozostania w limicie lub w przypadku ograniczenia nieplanowanych opłat dla obszaru roboczego.  

Wkrótce po osiągnięciu dziennego limitu w pozostałej części dnia zostanie zatrzymana Kolekcja typów danych do rozliczenia. (Opóźnienie związane z zastosowaniem dziennego limitu może oznaczać, że zakończenie nie jest stosowane jako dokładnie określony poziom dziennego limitu). Transparent ostrzegawczy pojawia się w górnej części strony dla wybranego obszaru roboczego Log Analytics, a zdarzenie operacji jest wysyłane do tabeli *operacji* w kategorii **LogManagement** . Zbieranie danych zostanie wznowione po upływie czasu resetowania określonego w obszarze *dzienny limit*. Zalecamy zdefiniowanie reguły alertu na podstawie tego zdarzenia operacji skonfigurowanego do powiadamiania o osiągnięciu dziennego limitu ilości danych. 

> [!WARNING]
> Dzienny limit nie zatrzymuje zbierania danych z Azure Security Center, z wyjątkiem obszarów roboczych, w których Azure Security Center został zainstalowany przed 19 czerwca 2017. 

### <a name="identify-what-daily-data-limit-to-define"></a>Określ dzienny limit danych do zdefiniowania

Przejrzyj [log Analytics użycie i szacowane koszty](usage-estimated-costs.md) , aby zrozumieć trend pozyskiwania danych i co to jest dzienny limit ilości do zdefiniowania. Należy wziąć pod uwagę, że od czasu wygrania Twoje zasoby będą mogły być monitorowane po osiągnięciu limitu. 

### <a name="set-the-daily-cap"></a>Ustaw limit dzienny

W poniższych krokach opisano sposób konfigurowania limitu zarządzania ilością danych w obszarze roboczym Log Analytics na dzień.  

1. W obszarze roboczym wybierz pozycję **Użycie i szacunkowe koszty** w lewym okienku.
2. Na stronie **użycie i szacowane koszty** dla wybranego obszaru roboczego kliknij pozycję **Zarządzanie ilością danych** w górnej części strony. 
3. Dzienny limit jest domyślnie **wyłączony** ? Kliknij **przycisk Włącz,** aby go włączyć, a następnie ustaw limit ilości danych na GB/dzień.

    ![Log Analytics skonfigurować limit danych](media/manage-cost-storage/set-daily-volume-cap-01.png)

### <a name="alert-when-daily-cap-reached"></a>Zgłoś alert po osiągnięciu dziennego limitu

Podczas prezentowania wizualnej wskazówki w Azure Portal po spełnieniu progu limitu danych takie zachowanie nie musi być dostosowane do sposobu zarządzania problemami operacyjnymi wymagającymi natychmiastowej uwagi.  Aby otrzymać powiadomienie o alercie, można utworzyć nową regułę alertu w Azure Monitor.  Aby dowiedzieć się więcej, zobacz [jak tworzyć, wyświetlać i zarządzać alertami](alerts-metric.md).

Aby rozpocząć, poniżej przedstawiono zalecane ustawienia alertu:

- Cel: wybierz zasób Log Analytics
- Określonych 
   - Nazwa sygnału: niestandardowe wyszukiwanie w dzienniku
   - Zapytanie wyszukiwania: operacja | gdzie szczegóły ma "limit przydziału"
   - Na podstawie: liczba wyników
   - Warunek: większe niż
   - Próg: 0
   - Okres: 5 (minuty)
   - Częstotliwość: 5 (minuty)
- Nazwa reguły alertu: osiągnięto dzienny limit danych
- Ważność: ostrzeżenie (ważność 1)

Po zdefiniowaniu alertu i osiągnięciu limitu zostanie wyzwolony alert i zostanie określona odpowiedź zdefiniowana w grupie akcji. Może ona wysyłać powiadomienia do zespołu za pośrednictwem wiadomości e-mail i SMS albo automatyzować akcje przy użyciu elementów webhook, Runbook Automation lub [integracji z zewnętrznym rozwiązaniem narzędzia ITSM](itsmc-overview.md#create-itsm-work-items-from-azure-alerts). 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>Rozwiązywanie problemów związanych z użyciem przekraczającym oczekiwania

Większe użycie jest spowodowane przez jedną lub obie z następujących przyczyn:
- Więcej węzłów niż oczekiwano wysłania danych do obszaru roboczego Log Analytics
- Więcej danych niż oczekiwano podczas wysyłania do obszaru roboczego Log Analytics (prawdopodobnie z powodu rozpoczęcia korzystania z nowego rozwiązania lub zmiany konfiguracji w istniejącym rozwiązaniu)

## <a name="understanding-nodes-sending-data"></a>Informacje o węzłach wysyłających dane

Aby zrozumieć liczbę węzłów zgłaszających pulsy przez agenta każdego dnia w ostatnim miesiącu, użyj

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
Pobierz liczbę węzłów, które wysyłają dane w ciągu ostatnich 24 godzin, użyj zapytania: 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

Aby uzyskać listę węzłów wysyłających dowolne dane (i ilość danych wysyłanych przez poszczególne), można użyć poniższego zapytania:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

> [!TIP]
> Te zapytania `union withsource = tt *` są oszczędnie zależą od tego, jak skanowanie między typami danych jest [czasochłonne](https://docs.microsoft.com/azure/azure-monitor/log-query/query-optimization#query-performance-pane) . To zapytanie zastępuje stary sposób wykonywania zapytań dotyczących informacji dla poszczególnych komputerów za pomocą typu danych użycia.  

## <a name="understanding-ingested-data-volume"></a>Zrozumienie ilości pozyskiwanych danych

Na stronie **użycie i szacowane koszty** pozyskiwanie *danych na wykres rozwiązań* pokazuje łączną ilość wysłanych danych i ilość wysyłanych przez każde rozwiązanie. Dzięki temu można określić trendy, takie jak to, czy ogólne użycie danych (lub użycie określonego rozwiązania) rośnie, pozostało stałe lub zmniejszane. 

### <a name="data-volume-for-specific-events"></a>Wolumin danych dla określonych zdarzeń

Aby przyjrzeć się ilości pobieranych danych dla określonego zestawu zdarzeń, można wykonać zapytanie dotyczące konkretnej tabeli (w tym przykładzie `Event`), a następnie ograniczyć zapytanie do interesujących Cię zdarzeń (w tym przykładzie zdarzenia o identyfikatorze 5145 lub 5156):

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

Należy zauważyć, że `where IsBillable = true` klauzula filtruje typy danych z niektórych rozwiązań, dla których nie jest naliczana opłata za pozyskiwanie. 

### <a name="data-volume-by-solution"></a>Ilość danych wg rozwiązania

Zapytanie używane do wyświetlania ilości danych do rozliczenia według rozwiązania w ciągu ostatniego miesiąca (z wyjątkiem ostatniego częściowego dnia) to:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

Klauzula WITH `TimeGenerated` służy tylko do upewnienia się, że obsługa zapytań w Azure Portal będzie wyglądać poza domyślnymi 24 godzinami. W przypadku użycia typu danych użycia i `StartTime` `EndTime` reprezentowania przedziałów czasu, dla których są wyświetlane wyniki. 

### <a name="data-volume-by-type"></a>Ilość danych według typu

Możesz przejść do szczegółów, aby zobaczyć trendy dotyczące danych według typu danych:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

Lub, aby wyświetlić tabelę według rozwiązania i typu w ciągu ostatniego miesiąca,

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>Ilość danych według komputera

Typ `Usage` danych nie zawiera informacji na poziomie komputera. Aby wyświetlić **rozmiar** pobieranych danych na komputer, użyj `_BilledSize` [Właściwości](log-standard-properties.md#_billedsize), która zapewnia rozmiar w bajtach:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName | sort by Bytes nulls last
```

`_IsBillable` [Właściwość](log-standard-properties.md#_isbillable) określa, czy pozyskane dane będą naliczane opłaty.

Aby zobaczyć **liczbę** zdarzeń rozliczanych pobieranych na komputer, użyj 

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  | sort by eventCount nulls last
```

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Ilość danych według zasobu platformy Azure, grupy zasobów lub subskrypcji

W przypadku danych z węzłów hostowanych na platformie Azure można uzyskać **rozmiar** pobieranych danych __na komputer__, używając [Właściwości](log-standard-properties.md#_resourceid)_ResourceId, która zapewnia pełną ścieżkę do zasobu:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by Bytes nulls last
```

W przypadku danych z węzłów hostowanych na platformie Azure można uzyskać **rozmiar** pozyskanych danych __na subskrypcję platformy Azure__, przeanalizować `_ResourceId` właściwość jako:

```kusto
union withsource = tt * 
| where TimeGenerated > ago(24h)
| where _IsBillable == true 
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
| summarize BillableDataBytes = sum(_BilledSize) by subscriptionId | sort by Bytes nulls last
```

Zmiana `subscriptionId` na `resourceGroup` wartość spowoduje wyświetlenie ilości danych pozyskanych do rozliczenia przez grupę zasobów platformy Azure. 

> [!WARNING]
> Niektóre pola typu danych użycia, ale nadal w schemacie, są przestarzałe i ich wartości nie będą już wypełnione. Są to **komputery** , a także pola związane z pozyskiwaniem (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** i **AverageProcessingTimeMs**.

### <a name="querying-for-common-data-types"></a>Wykonywanie zapytań dotyczących typowych typów danych

Aby Dig bardziej szczegółowo źródło danych dla określonego typu danych, Oto kilka przydatnych zapytań przykładowych:

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

Niektóre sugestie dotyczące zmniejszenia ilości zbieranych dzienników obejmują:

| Źródło dużego woluminu danych | Jak zmniejszyć wolumin danych |
| -------------------------- | ------------------------- |
| Zdarzenia zabezpieczeń            | Wybierz [pospolite lub minimalne zdarzenia zabezpieczeń](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection#data-collection-tier). <br> Zmień zasady inspekcji zabezpieczeń w celu zbierania tylko potrzebnych zdarzeń. W szczególności zastanów się nad koniecznością zbierania następujących zdarzeń: <br> - [inspekcja platformy filtrowania](https://technet.microsoft.com/library/dd772749(WS.10).aspx) <br> - [inspekcja rejestru](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [inspekcja systemu plików](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [inspekcja obiektu jądra](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [inspekcja manipulowania dojściem](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> Inspekcja magazynu wymiennego |
| Liczniki wydajności       | Zmień [konfigurację licznika wydajności](data-sources-performance-counters.md) w następujący sposób: <br> — Zmniejsz częstotliwość gromadzenia <br> — Zmniejsz liczbę liczników wydajności |
| Dzienniki zdarzeń                 | Zmień [konfigurację dziennika zdarzeń](data-sources-windows-events.md) w następujący sposób: <br> — Zmniejsz liczbę gromadzonych danych dzienników zdarzeń <br> — Zbieraj wyłącznie zdarzenia o wymaganym poziomie. Na przykład nie zbieraj zdarzeń na poziomie *Informacje*. |
| Dziennik systemu                     | Zmień [konfigurację dziennika systemu](data-sources-syslog.md) w następujący sposób: <br> — Zmniejsz liczbę urządzeń, z których zbierane są dane <br> — Zbieraj wyłącznie zdarzenia o wymaganym poziomie. Na przykład nie zbieraj zdarzeń na poziomie *Informacje* i *Debugowanie*. |
| AzureDiagnostics           | Zmień kolekcję dziennika zasobów, aby: <br> — zmniejszyć liczbę dzienników zasobów wysyłanych do usługi Log Analytics, <br> — zbierać tylko wymagane dzienniki. |
| Dane rozwiązań z komputerów, które nie wymagają rozwiązania | Użyj funkcji [określania celu rozwiązania](../insights/solution-targeting.md) , aby zbierać dane tylko z wymaganych grup komputerów. |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>Pobieranie węzłów jako rozliczane w warstwie cenowej na węzeł

Aby uzyskać listę komputerów, które będą rozliczane jako węzły w przypadku, gdy obszar roboczy znajduje się w starszej warstwie cenowej węzła, poszukaj węzłów, które wysyłają **opłaty za typy danych** (niektóre typy danych są bezpłatne). W tym celu należy użyć `_IsBillable` [Właściwości](log-standard-properties.md#_isbillable) i użyć pola z lewej strony w w pełni kwalifikowanej nazwy domeny. To zwraca liczbę komputerów z danymi rozliczanymi za godzinę (czyli stopnia szczegółowości, w których węzły są zliczane i są rozliczane):

```kusto
union withsource = tt * 
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>Pobieranie zabezpieczeń i liczby węzłów automatyzacji

Jeśli korzystasz z warstwy cenowej "na węzeł (OMS)", opłaty są naliczane na podstawie liczby używanych węzłów i rozwiązań, liczby analiz i węzłów analizy, dla których są naliczane opłaty, zostaną wyświetlone w tabeli na stronie **użycie i szacowany koszt** .  

Aby wyświetlić liczbę różnych węzłów zabezpieczeń, można użyć zapytania:

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

Aby wyświetlić liczbę różnych węzłów automatyzacji, użyj zapytania:

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

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>Ocenianie starszej warstwy cenowej na węzeł

Decyzja o tym, czy obszary robocze z dostępem do starszej warstwy cenowej **na węzeł** są lepiej wyłączone w tej warstwie lub w bieżącej warstwie **płatność zgodnie z rzeczywistym** **użyciem, czy** jest często trudne do oceny dla klientów.  Obejmuje to informacje o handlu między stałymi kosztami dla monitorowanego węzła w warstwie cenowej węzła i dołączonym przydziale danych wynoszącym 500 MB/węzeł/dzień, a także kosztem płatności za pozyskiwane dane w warstwie płatności zgodnie z rzeczywistym użyciem (za GB). 

Aby ułatwić tę ocenę, można użyć następującego zapytania, aby zastosować zalecenie dotyczące optymalnej warstwy cenowej na podstawie wzorców użytkowania obszaru roboczego.  To zapytanie przegląda monitorowane węzły i dane pozyskane w obszarze roboczym w ciągu ostatnich 7 dni, a dla każdego dnia szacuje się, która warstwa cenowa byłaby optymalna. Aby użyć zapytania, należy określić, czy obszar roboczy używa Azure Security Center przez ustawienie `workspaceHasSecurityCenter` na `true` lub `false`, a następnie (opcjonalnie) Aktualizowanie za każdy węzeł i za GB cen, które odbiera organizacji. 

```kusto
// Set these parameters before running query
let workspaceHasSecurityCenter = true;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your montly price per monitored nodes
let PerGBPrice = 2.30; // Enter your price per GB 
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
union withsource = tt * 
| where TimeGenerated >= startofday(now(-7d)) and TimeGenerated < startofday(now())
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join kind=leftouter (
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

To zapytanie nie jest dokładną replikacją sposobu obliczania użycia, ale będzie działała w większości przypadków w celu zapewnienia zaleceń dotyczących warstwy cenowej.  

## <a name="create-an-alert-when-data-collection-is-high"></a>Utwórz alert, gdy zbieranie danych jest wysokie

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

## <a name="data-transfer-charges-using-log-analytics"></a>Opłaty za transfer danych przy użyciu Log Analytics

Wysyłanie danych do Log Analytics może spowodować naliczenie opłat za przepustowość danych. Zgodnie z opisem na [stronie cennika przepustowości platformy Azure](https://azure.microsoft.com/pricing/details/bandwidth/)transfer danych między usługami platformy Azure znajdującymi się w dwóch regionach jest naliczany jako wychodzący transfer danych. Transfer danych przychodzących jest bezpłatny. Ta opłata jest jednak bardzo mała (kilka%) w porównaniu z kosztami pozyskiwania danych Log Analytics. W związku z tym, kontrolując koszty Log Analytics muszą skupić się na pozyskiwanym woluminie danych i mamy wskazówki ułatwiające zrozumienie tego w [tym miejscu](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#understanding-ingested-data-volume).   


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Rozwiązywanie problemów dlaczego Log Analytics nie zbiera już danych

Jeśli korzystasz z starszej warstwy cenowej bezpłatnej i w ciągu dnia wyślesz więcej niż 500 MB danych, zbieranie danych zostanie zatrzymane w pozostałej części dnia. Osiągnięcie dziennego limitu jest typowym powodem, Log Analytics przestaje zbierać dane lub wydaje się, że brakuje danych.  Log Analytics tworzy zdarzenie typu operacja podczas uruchamiania i zatrzymywania zbierania danych. Uruchom następujące zapytanie w obszarze wyszukiwania, aby sprawdzić, czy osiągnięto limit dzienny i brakujące dane: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Gdy zbieranie danych zostanie zatrzymane, OperationStatus jest **ostrzeżeniem**. Po rozpoczęciu zbierania danych OperationStatus **powiodło**się. W poniższej tabeli opisano przyczyny zatrzymania zbierania danych oraz sugerowaną akcję wznowienia zbierania danych:  

|Zakończenie zbierania danych o przyczynie| Rozwiązanie| 
|-----------------------|---------|
|Osiągnięto dzienny limit starszych bezpłatnych warstw cenowych |Poczekaj na automatyczne ponowne uruchomienie kolekcji lub Zmień ją na płatną warstwę cenową.|
|Osiągnięto dzienny limit Twojego obszaru roboczego|Poczekaj na automatyczne ponowne uruchomienie kolekcji lub Zwiększ dzienny limit ilości danych opisany w temacie Zarządzanie maksymalnym dziennym woluminem danych. Dzienny czas resetowania zostanie wyświetlony na stronie **Zarządzanie ilością danych** . |
|Subskrypcja platformy Azure jest w stanie wstrzymania z powodu:<br> Bezpłatna wersja próbna została zakończona<br> Upłynął okres ważności platformy Azure<br> Osiągnięto miesięczny limit wydatków (na przykład w subskrypcji MSDN lub Visual Studio)|Konwersja na płatną subskrypcję<br> Usuń limit lub zaczekaj na zresetowanie limitu|

Aby otrzymywać powiadomienia, gdy zbieranie danych zostanie zatrzymane, wykonaj kroki opisane w temacie Tworzenie alertu *dziennego zakończenia danych* , aby otrzymywać powiadomienia o zatrzymaniu zbierania danych. Wykonaj kroki opisane w sekcji [Tworzenie grupy akcji](action-groups.md) , aby skonfigurować akcję poczty e-mail, elementu webhook lub elementu Runbook dla reguły alertu. 

## <a name="limits-summary"></a>Podsumowanie limitów

Istnieją pewne dodatkowe limity Log Analytics, które są zależne od warstwy cenowej Log Analytics. Opisano je [tutaj](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#log-analytics-workspaces).


## <a name="next-steps"></a>Następne kroki

- Zobacz [Wyszukiwanie w dzienniku w](../log-query/log-query-overview.md) dziennikach Azure monitor, aby dowiedzieć się, jak używać języka wyszukiwania. Możesz użyć zapytań wyszukiwania w celu przeprowadzenia dodatkowej analizy danych użycia.
- Wykonaj kroki opisane w sekcji dotyczącej [tworzenia nowego alertu dziennika](alerts-metric.md), aby otrzymywać powiadomienie, gdy kryteria wyszukiwania zostaną spełnione.
- Użyj funkcji [określania celu rozwiązania](../insights/solution-targeting.md) , aby zbierać dane tylko z wymaganych grup komputerów.
- Aby skonfigurować efektywne zasady zbierania zdarzeń, przejrzyj [zasady filtrowania Azure Security Center](../../security-center/security-center-enable-data-collection.md).
- Zmień [konfigurację licznika wydajności](data-sources-performance-counters.md).
- Aby zmodyfikować ustawienia zbierania zdarzeń, przejrzyj [konfigurację dziennika zdarzeń](data-sources-windows-events.md).
- Aby zmodyfikować ustawienia kolekcji dziennika systemowego, zapoznaj się z tematem [Konfiguracja dziennika](data-sources-syslog.md)systemu.
