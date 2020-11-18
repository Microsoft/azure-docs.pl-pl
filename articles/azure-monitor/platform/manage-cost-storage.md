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
ms.date: 11/16/2020
ms.author: bwren
ms.subservice: ''
ms.openlocfilehash: 71a4fba177f5bbbaf9f8d991222b071d0da66d4d
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660393"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Zarządzanie użyciem i kosztami za pomocą dzienników usługi Azure Monitor    

> [!NOTE]
> W tym artykule opisano sposób zrozumienia i kontrolowania kosztów Azure Monitor dzienników. W powiązanym artykule, [monitorowaniu użycia i szacowanych kosztów](usage-estimated-costs.md) opisano sposób wyświetlania użycia i szacowane koszty w wielu funkcjach monitorowania platformy Azure dla różnych modeli cen. Wszystkie ceny i koszty pokazane w tym artykule są wyłącznie na przykład. 

Dzienniki Azure Monitor są przeznaczone do skalowania i obsługi zbierania, indeksowania i przechowywania dużych ilości danych dziennie z dowolnego źródła w przedsiębiorstwie lub wdrożonego na platformie Azure.  Chociaż może to być podstawowy sterownik dla Twojej organizacji, kosztem wydajności jest ostatecznie podstawowy sterownik. W tym celu należy zrozumieć, że koszt obszaru roboczego Log Analytics nie jest oparty tylko na ilości zbieranych danych, zależy również od wybranego planu i od czasu wybrania do przechowywania danych generowanych przez połączone źródła.  

W tym artykule opisano, jak można aktywnie monitorować pozyskiwane ilości danych i przyrosty magazynu oraz definiować ograniczenia kontroli nad tymi kosztami. 

## <a name="pricing-model"></a>Model cen

Domyślna cena dla Log Analytics jest modelem **płatności zgodnie z rzeczywistym** użyciem na podstawie ilości danych pozyskiwanych i opcjonalnie do dłuższego przechowywania danych. Ilość danych jest mierzona jako rozmiar danych, które będą przechowywane w GB (10 ^ 9 bajtów). Każdy obszar roboczy Log Analytics jest rozliczany jako osobna usługa i przyczynia się do rozliczenia za subskrypcję platformy Azure. Ilość pozyskiwania danych może być znaczna w zależności od następujących czynników: 

  - Liczba włączonych rozwiązań zarządzania i ich konfiguracja
  - Liczba monitorowanych maszyn wirtualnych
  - Typ danych zbieranych z każdej monitorowanej maszyny wirtualnej 
  
Oprócz modelu "płatność zgodnie z rzeczywistym użyciem" Log Analytics ma warstwy **rezerwacji pojemności** , które umożliwiają oszczędności o 25% w porównaniu z ceną płatność zgodnie z rzeczywistym użyciem. Cennik rezerwacji zdolności produkcyjnych umożliwia zakupienie rezerwacji rozpoczynającej się o 100 GB/dzień. Każde użycie powyżej poziomu rezerwacji będzie rozliczane według stawki płatności zgodnie z rzeczywistym użyciem. Warstwy rezerwacji pojemności mają 31-dniowy okres zobowiązania. W okresie obowiązywania zobowiązania można zmienić warstwę rezerwacji wyższego poziomu (co spowoduje ponowne uruchomienie 31-dniowego okresu zobowiązań), ale nie będzie można wrócić do warstwy rezerwacji zgodnie z rzeczywistym użyciem lub do niższej wersji, dopóki upłynie okres obowiązywania zobowiązania. Opłaty za warstwy rezerwacji pojemności są wykonywane codziennie. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/monitor/) na temat log Analytics płatność zgodnie z rzeczywistym użyciem i Cennik rezerwacji zdolności produkcyjnych. 

We wszystkich warstwach cenowych rozmiar danych zdarzenia jest obliczany na podstawie ciągu reprezentującego właściwości, które są przechowywane w Log Analytics dla tego zdarzenia, niezależnie od tego, czy dane są wysyłane z agenta, czy dodawane podczas procesu pozyskiwania. Obejmuje to wszystkie [pola niestandardowe](custom-fields.md) , które są dodawane jako dane są zbierane, a następnie przechowywane w log Analytics. Niektóre właściwości wspólne dla wszystkich typów danych, w tym niektóre [log Analytics właściwości standardowe](./log-standard-columns.md), są wykluczone w obliczaniu rozmiaru zdarzenia. Obejmuje to `_ResourceId` , `_ItemId` , `_IsBillable` `_BilledSize` i `Type` . Wszystkie inne właściwości przechowywane w Log Analytics są uwzględniane podczas obliczania rozmiaru zdarzenia. Niektóre typy danych są bezpłatne od opłat za pozyskiwanie danych, na przykład na platformie Azure, pulsu i typach użycia. Aby określić, czy zdarzenie zostało wykluczone z rozliczeń na potrzeby pozyskiwania danych, można użyć `_IsBillable` właściwości, jak pokazano [poniżej](#data-volume-for-specific-events). Użycie jest raportowane w GB (1,0 E9 bajtów). 

Należy również pamiętać, że niektóre rozwiązania, takie jak [Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/), [wskaźnik platformy Azure](https://azure.microsoft.com/pricing/details/azure-sentinel/) i [Zarządzanie konfiguracją](https://azure.microsoft.com/pricing/details/automation/) mają własne modele cen. 

### <a name="log-analytics-dedicated-clusters"></a>Log Analytics dedykowanych klastrów

Log Analytics dedykowane klastry to kolekcje obszarów roboczych w jednym zarządzanym klastrze Eksplorator danych platformy Azure, które obsługują zaawansowane scenariusze, takie jak [klucze zarządzane przez klienta](customer-managed-keys.md).  Log Analytics dedykowane klastry używają modelu cenowego rezerwacji pojemności, który musi być skonfigurowany na co najmniej 1000 GB/dzień. Ten poziom wydajności ma rabat w wysokości 25% w porównaniu z cennikiem z opcją płatność zgodnie z rzeczywistym użyciem. Każde użycie powyżej poziomu rezerwacji będzie rozliczane według stawki płatności zgodnie z rzeczywistym użyciem. Rezerwacja pojemności klastra obejmuje 31-dniowy okres zobowiązania po zwiększeniu poziomu rezerwacji. W okresie obowiązywania zobowiązania nie można zmniejszyć poziomu rezerwacji pojemności, ale można go zwiększyć w dowolnym momencie. Gdy obszary robocze są skojarzone z klastrem, rozliczanie danych dla tych obszarów roboczych odbywa się na poziomie klastra przy użyciu skonfigurowanego poziomu rezerwacji pojemności. Dowiedz się więcej na temat [tworzenia klastrów log Analytics](customer-managed-keys.md#create-cluster) i [kojarzenia z nią obszarów roboczych](customer-managed-keys.md#link-workspace-to-cluster). Informacje o cenach rezerwacji zdolności produkcyjnych są dostępne na [stronie cennika Azure monitor]( https://azure.microsoft.com/pricing/details/monitor/).  

Poziom rezerwacji pojemności klastra jest konfigurowany za pomocą programu programistycznego za pomocą Azure Resource Manager przy użyciu `Capacity` parametru w obszarze `Sku` . Wartość `Capacity` jest określona w jednostkach GB i może mieć wartości 1000 GB/dzień lub więcej w przyrostach wynoszących 100 GB/dzień. Jest to szczegółowo opisany w [Azure monitor kluczu zarządzanym przez klienta](customer-managed-keys.md#create-cluster). Jeśli klaster wymaga rezerwacji powyżej 2000 GB/dzień, skontaktuj się z nami pod adresem [LAIngestionRate@microsoft.com](mailto:LAIngestionRate@microsoft.com) .

Istnieją dwa tryby rozliczania użycia w klastrze. Można je określić przy użyciu `billingType` parametru podczas [konfigurowania klastra](customer-managed-keys.md#customer-managed-key-operations). Dwa tryby są: 

1. **Klaster**: w tym przypadku (co jest ustawieniem domyślnym) rozliczanie danych pozyskiwanych odbywa się na poziomie klastra. Pobrane ilości danych z każdego obszaru roboczego skojarzonego z klastrem są agregowane w celu obliczenia dziennego rachunku dla klastra. Należy pamiętać, że alokacje na węzeł [Azure Security Center](../../security-center/index.yml) są stosowane na poziomie obszaru roboczego przed agregacją zagregowanych danych we wszystkich obszarach roboczych w klastrze. 

2. **Obszary robocze**: koszty rezerwacji pojemności dla klastra są przydzielone proporcjonalnie do obszarów roboczych w klastrze (po rozpoczęciu obsługi alokacji dla każdego węzła z [Azure Security Center](../../security-center/index.yml) dla każdego obszaru roboczego). Jeśli całkowita ilość danych pozyskanych w obszarze roboczym dla danego dnia jest mniejsza niż rezerwacja pojemności, w każdym obszarze roboczym zostanie naliczona opłata za pobrane dane z zastosowaniem stawki za ilość zarezerwowaną za GB, rozliczenie ich na część rezerwacji pojemności, a niewykorzystana część rezerwacji pojemności jest rozliczana z zasobem klastra. Jeśli całkowita ilość danych pozyskanych w obszarze roboczym dla danego dnia jest większa niż rezerwacja pojemności, w każdym obszarze roboczym jest naliczana część rezerwacji zdolności produkcyjnych na podstawie jej ułamka za dane pobranego dnia i każdego obszaru roboczego dla części pozyskanych danych powyżej rezerwacji pojemności. Nie są naliczane opłaty za zasób klastra, jeśli całkowita ilość danych pozyskana do obszaru roboczego na dzień przekracza rezerwację pojemności.

W opcjach rozliczania klastra przechowywanie danych jest rozliczane w poszczególnych obszarach roboczych. Należy pamiętać, że podczas tworzenia klastra rozliczenia są rozliczane, niezależnie od tego, czy obszary robocze zostały skojarzone z klastrem. Należy również pamiętać, że obszary robocze skojarzone z klastrem nie mają już warstwy cenowej.

## <a name="estimating-the-costs-to-manage-your-environment"></a>Szacowanie kosztów związanych z zarządzaniem środowiskiem 

Jeśli jeszcze nie korzystasz z dzienników Azure Monitor, możesz użyć [kalkulatora cen Azure monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) , aby oszacować koszt używania log Analytics. Zacznij od wprowadzenia "Azure Monitor" w polu wyszukiwania i kliknięcia kafelka Azure Monitor wyników. Przewiń w dół stronę do Azure Monitor i wybierz pozycję Log Analytics z listy rozwijanej Typ.  W tym miejscu możesz wprowadzić liczbę maszyn wirtualnych i GB danych, które mają być zbierane z każdej maszyny wirtualnej. Zwykle od 1 do 3 GB danych miesiąca jest pobierana z typowej maszyny wirtualnej platformy Azure. Jeśli już już oceniasz dzienniki Azure Monitor, możesz użyć statystyk danych z własnego środowiska. Poniżej znajdują się informacje na temat określania [liczby monitorowanych maszyn wirtualnych](#understanding-nodes-sending-data) i [ilości danych pobieranych przez obszar roboczy](#understanding-ingested-data-volume). 

## <a name="understand-your-usage-and-estimate-costs"></a>Zapoznaj się z użyciem i szacunkowymi kosztami

Jeśli używasz dzienników Azure Monitor teraz, możesz łatwo zrozumieć, jakie koszty są prawdopodobnie oparte na ostatnich wzorcach użycia. W tym celu należy użyć  **log Analytics użycia i szacowanych kosztów** , aby przejrzeć i analizować użycie danych. Pokazuje to ilość danych zbieranych przez każde rozwiązanie, ilość przechowywanych danych i oszacowanie kosztów na podstawie ilości danych pozyskiwanych i dodatkowego okresu przechowywania poza uwzględnioną ilością.

![Użycie i szacunkowe koszty](media/manage-cost-storage/usage-estimated-cost-dashboard-01.png)

Aby eksplorować dane w bardziej szczegółowy sposób, kliknij ikonę w prawym górnym rogu jednego z wykresów na stronie **użycie i szacowane koszty** . Teraz możesz korzystać z tego zapytania, aby poznać więcej szczegółów dotyczących użycia.  

![Widok dzienników](media/manage-cost-storage/logs.png)

Na stronie **użycie i szacowane koszty** możesz sprawdzić ilość danych w danym miesiącu. Obejmuje to wszystkie dane do rozliczenia otrzymane i zachowane w obszarze roboczym Log Analytics.  
 
Opłaty za Log Analytics są dodawane do rachunku na korzystanie z platformy Azure. Szczegóły rachunku na korzystanie z platformy Azure można znaleźć w sekcji rozliczenia Azure Portal lub [Azure Billing Portal](https://account.windowsazure.com/Subscriptions).  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>Wyświetlanie Log Analytics użycia na rachunku na platformie Azure 

Platforma Azure oferuje bardzo przydatne funkcje w [Azure Cost Management i centrum rozliczeń](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=%252fazure%252fbilling%252fTOC.json) . Na przykład funkcja "analiza kosztów" umożliwia wyświetlanie wydatków dotyczących zasobów platformy Azure. Najpierw Dodaj filtr "typ zasobu" (do Microsoft. operationalinsights/Workspace dla Log Analytics i Microsoft. operationalinsights/obszar roboczy dla klastrów Log Analytics) umożliwi śledzenie wydatków Log Analytics. Następnie dla opcji "Grupuj według" Wybierz "kategoria licznika" lub "licznik".  Należy zauważyć, że inne usługi, takie jak Azure Security Center i Azure — wskaźnik użytkowania, również rozliczają swoje użycie względem zasobów Log Analytics obszaru roboczego. Aby wyświetlić mapowanie do nazwy usługi, można wybrać widok tabeli zamiast wykresu. 

Aby lepiej zrozumieć użycie, można [pobrać informacje o użyciu z witryny Azure Portal](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal). Pobrany arkusz kalkulacyjny zawiera dane o użyciu poszczególnych zasobów platformy Azure (np. obszaru roboczego usługi Log Analytics) na dzień. W tym arkuszu kalkulacyjnym programu Excel można znaleźć użycie z obszarów roboczych Log Analytics, wybierając najpierw filtrowanie w kolumnie "kategoria licznika", aby pokazać "Log Analytics", "szczegółowe informacje i analiza" (używane przez niektóre starsze warstwy cenowe) i "Azure Monitor" (używane przez warstwy cenowe rezerwacji zdolności produkcyjnych), a następnie Dodawanie filtru w kolumnie "identyfikator wystąpienia", która ma wartość "zawiera obszar roboczy" lub "zawiera klaster" (ten ostatni, aby uwzględnić użycie klastra Log Analytics). Użycie jest wyświetlane w kolumnie "zużyta ilość", a jednostka dla każdego wpisu jest pokazywana w kolumnie "jednostka miary".  Temat dotyczący [interpretacji rachunku za platformę Microsoft Azure](../../cost-management-billing/understand/review-individual-bill.md) zawiera więcej szczegółowych informacji. 

## <a name="changing-pricing-tier"></a>Zmienianie warstwy cenowej

Aby zmienić warstwę cenową Log Analytics w obszarze roboczym, 

1. W Azure Portal Otwórz **i szacowane koszty** w obszarze roboczym, w którym zostanie wyświetlona lista wszystkich warstw cenowych dostępnych dla tego obszaru roboczego.

2. Przejrzyj szacowane koszty dla każdej warstwy cenowej. To oszacowanie dotyczy ostatnich 31 dni użytkowania, więc oszacowanie kosztów polega na wykorzystaniu ostatnich 31 dni reprezentatywnych dla typowego użycia. W poniższym przykładzie można zobaczyć, jak, na podstawie wzorców danych z ostatnich 31 dni, ten obszar roboczy będzie tańszy w warstwie płatność zgodnie z rzeczywistym użyciem (#1) w porównaniu z warstwą rezerwacji (#2) 100 GB/dzień.  

    ![Warstwy cenowe](media/manage-cost-storage/pricing-tier-estimated-costs.png)

3. Po przejrzeniu szacowanych kosztów na podstawie ostatnich 31 dni użytkowania, jeśli zdecydujesz się zmienić warstwę cenową, kliknij pozycję **Wybierz**.  

Możesz również [ustawić warstwę cenową za pośrednictwem Azure Resource Manager](../samples/resource-manager-workspace.md) przy użyciu `sku` parametru ( `pricingTier` w szablonie Azure Resource Manager). 

## <a name="legacy-pricing-tiers"></a>Starsze warstwy cenowe

Subskrypcje, w których wystąpiły obszary robocze Log Analytics lub Application Insights zasobów, przed 2 kwietnia 2018 lub połączone z Umowa Enterprise, które zostały uruchomione przed 1 lutego 2019, nadal będą miały dostęp do korzystania ze starszych warstw cenowych: **bezpłatna**, **autonomiczna (za GB)** i **na węzeł (OMS)**.  Obszary robocze w warstwie cenowej bezpłatna będą mieć dzienne pozyskiwanie danych ograniczone do 500 MB (z wyjątkiem typów danych zabezpieczeń zbieranych przez [Azure Security Center](../../security-center/index.yml)), a przechowywanie danych jest ograniczone do 7 dni. Warstwa cenowa bezpłatna jest przeznaczona tylko do celów ewaluacyjnych. Obszary robocze w warstwach autonomicznych lub na węzeł są dostępne przez użytkownika w sposób konfigurowalny od 30 do 730 dni.

Użycie w ramach autonomicznej warstwy cenowej jest rozliczane przez pozyskiwany wolumin danych. Jest on raportowany w usłudze **log Analytics** i miernik ma nazwę "dane analizowane". 

Opłaty za warstwę cenową na węzeł na monitorowaną maszynę wirtualną (węzeł) mają stopień szczegółowości godzin. Dla każdego monitorowanego węzła obszar roboczy ma przydzieloną 500 MB danych dziennie, które nie są rozliczane. Ta alokacja jest obliczana z dokładnością co godzinę i jest agregowana na poziomie obszaru roboczego każdego dnia. Dane pozyskane powyżej zagregowanego dziennego przydziału danych są rozliczane za GB jako nadwyżkowe dane. Należy pamiętać, że na rachunku usługa zostanie **Insight and Analytics** do log Analytics użycie, jeśli obszar roboczy znajduje się w warstwie cenowej węzła na węzeł. Użycie jest raportowane dla trzech liczników:

1. Węzeł: jest to użycie dla liczby monitorowanych węzłów (maszyn wirtualnych) w jednostkach węzła * miesiące.
2. Nadwyżka danych na węzeł: jest to liczba GB danych pobieranych z przekroczeniem zagregowanej alokacji danych.
3. Dane uwzględnione na węzeł: to ilość danych uzyskanych w ramach zagregowanego przydziału danych. Ten licznik jest również używany, gdy obszar roboczy znajduje się w obszarze wszystkie warstwy cenowe, aby pokazać ilość danych objętych przez Azure Security Center.

> [!TIP]
> Jeśli Twój obszar roboczy ma dostęp do warstwy cenowej **węzła** , ale zastanawiasz się, czy w warstwie płatność zgodnie z rzeczywistym użyciem jest to tańsze, możesz [użyć poniższego zapytania](#evaluating-the-legacy-per-node-pricing-tier) , aby łatwo uzyskać zalecenie. 

Obszary robocze utworzone przed kwietnia 2016 mogą również uzyskiwać dostęp do oryginalnych warstw cenowych **Standard** i **Premium** , które mają odpowiednio stałe okresy 30 i 365. Nie można tworzyć nowych obszarów roboczych w warstwach cenowych **standardowa** lub **Premium** , a jeśli obszar roboczy jest przenoszony z tych warstw, nie można go przenieść z powrotem. Liczniki pozyskiwania danych dla tych starszych warstw są nazywane "danymi analizowanymi".

Istnieją także pewne zachowania między użyciem starszych warstw Log Analytics i sposobu rozliczania użycia dla [Azure Security Center](../../security-center/index.yml). 

1. Jeśli obszar roboczy znajduje się w starszej wersji warstwy Standardowa lub Premium, Azure Security Center będzie rozliczany tylko w przypadku Log Analytics pozyskiwania danych, a nie na węzeł.
2. Jeśli obszar roboczy znajduje się w starszej warstwie węzła, Azure Security Center będzie rozliczany przy użyciu bieżącego [Azure Security Center modelu cenowego opartego na węźle](https://azure.microsoft.com/pricing/details/security-center/). 
3. W przypadku innych warstw cenowych (w tym rezerwacji pojemności), jeśli Azure Security Center została włączona przed 19 czerwca 2017, Azure Security Center zostanie naliczona tylko za pozyskiwanie danych Log Analytics. W przeciwnym razie Azure Security Center będzie rozliczany przy użyciu bieżącego Azure Security Center modelu cen opartego na węzłach.

Dodatkowe szczegóły ograniczeń warstwy cenowej są dostępne w [ramach subskrypcji platformy Azure i ograniczeń usługi, przydziałów i ograniczeń](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces).

Żadna ze starszych warstw cenowych nie ma opartych na regionie cen.  

> [!NOTE]
> Aby użyć uprawnień pochodzących z zakupu pakietu OMS E1, pakietu OMS E2 lub Add-On OMS dla programu System Center, wybierz warstwę cenową Log Analytics *na węzeł* .

## <a name="log-analytics-and-security-center"></a>Log Analytics i Security Center

Rozliczanie [Azure Security Center](https://docs.microsoft.com/azure/security-center/) jest ściśle powiązane z log Analytics rozliczeniami. Security Center zapewnia alokację 500 MB/węzeł/dzień względem zestawu [typów danych zabezpieczeń](https://docs.microsoft.com/azure/azure-monitor/reference/tables/tables-category#security) (WindowsEvent, SecurityAlert, SecurityBaseline, SecurityBaselineSummary, SecurityDetection, SecurityEvent, WindowsFirewall, MaliciousIPCommunication, LinuxAuditLog, SysmonEvent, ProtectionStatus) i typów danych Update i UpdateSummary, gdy rozwiązanie Update Management nie jest uruchomione w obszarze roboczym lub jest włączone Określanie elementu docelowego rozwiązania. Jeśli obszar roboczy znajduje się w starszej warstwie cenowej węzła, alokacje Security Center i Log Analytics są łączone i stosowane wspólnie do wszystkich danych pozyskanych z rozliczeniami.  

## <a name="change-the-data-retention-period"></a>Change the data retention period (Zmienianie okresu przechowywania danych)

W poniższych krokach opisano sposób konfigurowania czasu przechowywania danych dziennika w obszarze roboczym. Przechowywanie danych można skonfigurować od 30 do 730 dni (2 lata) dla wszystkich obszarów roboczych, chyba że korzystają z starszej warstwy cenowej bezpłatna. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/monitor/) na temat cen, aby uzyskać dłuższe przechowywanie danych. 

### <a name="default-retention"></a>Domyślne przechowywanie

Aby ustawić domyślne przechowywanie dla obszaru roboczego, 
 
1. W Azure Portal w obszarze roboczym wybierz pozycję **użycie i szacowane koszty** w okienku po lewej stronie.
2. Na stronie **Użycie i szacunkowe koszty** kliknij pozycję **Przechowywanie danych** w górnej części strony.
3. W okienku za pomocą suwaka zwiększ lub zmniejsz liczbę dni, a następnie kliknij przycisk **OK**.  W przypadku korzystania z warstwy *bezpłatna* nie będzie można modyfikować okresu przechowywania danych i należy przeprowadzić uaktualnienie do warstwy płatnej w celu kontrolowania tego ustawienia.

    ![Zmień ustawienie przechowywania danych obszaru roboczego](media/manage-cost-storage/manage-cost-change-retention-01.png)

W przypadku obniżenia poziomu przechowywania istnieje kilka okresów prolongaty przed usunięciem danych starszych niż nowe ustawienie przechowywania. 

Przechowywanie można również [ustawić za pośrednictwem Azure Resource Manager](../samples/resource-manager-workspace.md) przy użyciu `retentionInDays` parametru. Po ustawieniu przechowywania danych na 30 dni można wyzwolić natychmiastowe przeczyszczanie starszych danych przy użyciu `immediatePurgeDataOn30Days` parametru (eliminując okres prolongaty kilku dni). Może to być przydatne w scenariuszach związanych ze zgodnością, w których bezpośrednie usuwanie danych jest konieczne. Ta funkcja natychmiastowego przeczyszczania jest dostępna tylko za pośrednictwem Azure Resource Manager. 

Obszary robocze z 30-dniowym przechowywaniem mogą faktycznie zachować dane przez 31 dni. Jeśli jest to konieczne, aby dane były przechowywane przez 30 dni, użyj Azure Resource Manager, aby ustawić przechowywanie na 30 dni i z `immediatePurgeDataOn30Days` parametrem.  

Dwa typy danych-- `Usage` i `AzureActivity` --są domyślnie przechowywane przez co najmniej 90 dni i nie są naliczane opłaty za korzystanie z tego 90go okresu przechowywania. Jeśli okres przechowywania obszaru roboczego zostanie zwiększony powyżej 90 dni, zachowywane są również te typy danych.  Te typy danych są również wolne od opłat za pozyskiwanie danych. 

Typy danych z zasobów Application Insights opartych na obszarze roboczym (,,,,,,,, `AppAvailabilityResults` `AppBrowserTimings` `AppDependencies` `AppExceptions` `AppEvents` `AppMetrics` `AppPageViews` `AppPerformanceCounters` `AppRequests` `AppSystemEvents` i `AppTraces` ) również są przechowywane domyślnie przez 90 dni i nie są naliczane opłaty za korzystanie z tego 90ego okresu przechowywania. Ich przechowywanie można dostosować przy użyciu funkcji przechowywania danych według typu. 

Należy zauważyć, że [interfejs API przeczyszczania](/rest/api/loganalytics/workspacepurge/purge) log Analytics nie wpływa na rozliczenie na przechowywanie i jest przeznaczony do użycia w bardzo ograniczonych przypadkach. Aby zmniejszyć rachunek przechowywania, należy zmniejszyć okres przechowywania dla obszaru roboczego lub dla konkretnych typów danych. 

### <a name="retention-by-data-type"></a>Przechowywanie według typu danych

Można również określić różne ustawienia przechowywania dla poszczególnych typów danych od 30 do 730 dni (z wyjątkiem obszarów roboczych w starszej warstwie cenowej bezpłatna). Każdy typ danych jest zasobem podrzędnym obszaru roboczego. Na przykład można [rozAzure Resource Manager](../../azure-resource-manager/management/overview.md) tabeli SecurityEvent jako:

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

Należy pamiętać, że typ danych (tabela) uwzględnia wielkość liter.  Aby pobrać bieżące ustawienia przechowywania typu danych dla określonego typu danych (w tym przykładzie SecurityEvent), użyj:

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

> [!NOTE]
> Przechowywanie jest zwracane tylko dla typu danych, jeśli przechowywanie zostało jawnie ustawione dla.  Typy danych, które nie mają jawnie ustawionego przechowywania (i w ten sposób dziedziczą przechowywanie obszaru roboczego) nie zwracają niczego z tego wywołania. 

Aby pobrać bieżące ustawienia przechowywania typu danych dla wszystkich typów danych w obszarze roboczym, dla których ustawiono przechowywanie ich typów danych, wystarczy pominąć określony typ danych, na przykład:

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

`Usage` `AzureActivity` Nie można ustawić typów danych i przechowywania niestandardowego. Zajmie to maksymalny domyślny okres przechowywania obszaru roboczego lub 90 dni. 

Doskonałym narzędziem do nawiązywania bezpośredniego połączenia z Azure Resource Manager, aby ustawić przechowywanie według typu danych, jest narzędzie OSS [ARMclient](https://github.com/projectkudu/ARMClient).  Dowiedz się więcej na temat ARMclient z artykułów przez [David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) i [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/).  Oto przykład użycia ARMClient, ustawiając dane SecurityEvent na 730 dni:

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> Ustawienie przechowywania poszczególnych typów danych może służyć do zmniejszania kosztów przechowywania danych.  W przypadku danych zbieranych od października 2019 (gdy ta funkcja została wydana) zmniejszenie ilości danych w czasie przechowywania może zmniejszyć koszt przechowywania w miarę upływu czasu.  W przypadku danych zbieranych wcześniej ustawienie mniejszego okresu przechowywania dla danego typu nie wpłynie na koszty przechowywania.  

## <a name="manage-your-maximum-daily-data-volume"></a>Zarządzanie maksymalnym dziennym woluminem danych

Można skonfigurować dzienny limit i ograniczyć dzienne pozyskiwanie obszaru roboczego, ale należy zachować ostrożność, ponieważ cel nie powinien być osiągnięty w dziennym limicie.  W przeciwnym razie utracisz dane przez pozostałą część dnia, co może mieć wpływ na inne usługi i rozwiązania platformy Azure, których funkcjonalność może zależeć od aktualnych danych dostępnych w obszarze roboczym.  Ma to wpływ na możliwość obserwowania i otrzymywania alertów dotyczących kondycji zasobów obsługujących usługi IT.  Dzienny limit jest przeznaczony do użycia jako sposób zarządzania **nieoczekiwanym wzrostem** ilości danych z zarządzanych zasobów i pozostania w limicie lub w przypadku ograniczenia nieplanowanych opłat dla obszaru roboczego. Nie powinno się ustawiać takiego dziennego limitu, który będzie osiągany każdego dnia w obszarze roboczym.

W każdym obszarze roboczym dzienny limit został zastosowany w innej godzinie dnia. Godzina resetowania jest wyświetlana na stronie **dzienne zakończenie** (patrz poniżej). Nie można skonfigurować tej godziny resetowania. 

Wkrótce po osiągnięciu dziennego limitu w pozostałej części dnia zostanie zatrzymana Kolekcja typów danych do rozliczenia. Opóźnienie związane z zastosowaniem dziennego limitu oznacza, że zakończenie nie jest stosowane w dokładnie określonym poziomie dziennego limitu. Transparent ostrzegawczy pojawia się w górnej części strony dla wybranego obszaru roboczego Log Analytics, a zdarzenie operacji jest wysyłane do tabeli *operacji* w kategorii **LogManagement** . Zbieranie danych zostanie wznowione po upływie czasu resetowania określonego w obszarze *dzienny limit*. Zalecamy zdefiniowanie reguły alertu na podstawie tego zdarzenia operacji skonfigurowanego do powiadamiania o osiągnięciu dziennego limitu ilości danych (patrz [poniżej](#alert-when-daily-cap-reached)). 

> [!NOTE]
> Dzienny limit nie może zatrzymać zbierania danych zgodnie z dokładnością określonego poziomu, a niektóre nadmierne dane są oczekiwane, zwłaszcza jeśli obszar roboczy otrzymuje duże ilości danych. Zapoznaj się z [poniższymi](#view-the-effect-of-the-daily-cap) tematami zapytania, które ułatwia badanie dziennego zachowania Cap. 

> [!WARNING]
> Dzienny limit nie zatrzymuje zbierania danych z usługi Azure Sentinal lub Azure Security Center, z wyjątkiem obszarów roboczych, w których Azure Security Center został zainstalowany przed 19 czerwca 2017. 

### <a name="identify-what-daily-data-limit-to-define"></a>Określ dzienny limit danych do zdefiniowania

Przejrzyj [log Analytics użycie i szacowane koszty](usage-estimated-costs.md) , aby zrozumieć trend pozyskiwania danych i co to jest dzienny limit ilości do zdefiniowania. Należy wziąć pod uwagę, że od czasu wygrania Twoje zasoby będą mogły być monitorowane po osiągnięciu limitu. 

### <a name="set-the-daily-cap"></a>Ustaw limit dzienny

W poniższych krokach opisano sposób konfigurowania limitu zarządzania ilością danych w obszarze roboczym Log Analytics na dzień.  

1. W obszarze roboczym wybierz pozycję **Użycie i szacunkowe koszty** w lewym okienku.
2. Na stronie **użycie i szacowane koszty** dla wybranego obszaru roboczego kliknij pozycję **koniec danych** w górnej części strony. 
3. Dzienny limit jest domyślnie **wyłączony** ? Kliknij **przycisk Włącz,** aby go włączyć, a następnie ustaw limit ilości danych na GB/dzień.

    ![Log Analytics skonfigurować limit danych](media/manage-cost-storage/set-daily-volume-cap-01.png)
    
Dzienny limit można skonfigurować za pośrednictwem ARM, ustawiając `dailyQuotaGb` parametr w obszarze w obszarze `WorkspaceCapping` [roboczym — Tworzenie lub aktualizowanie](/rest/api/loganalytics/workspaces/createorupdate#workspacecapping). 

### <a name="view-the-effect-of-the-daily-cap"></a>Wyświetlanie efektu dziennego limitu

Aby wyświetlić efekt dziennego limitu, ważne jest, aby uwzględnić typy danych zabezpieczeń, które nie są uwzględnione w codziennym Cap, oraz godzinę resetowania dla obszaru roboczego. Godzina resetowania dziennego limitu jest widoczna na stronie **dziennego limitu** .  Poniższe zapytanie może służyć do śledzenia woluminów danych, które są zależne od dziennego limitu resetowania Cap. W tym przykładzie godzina resetowania obszaru roboczego to 14:00.  Musisz zaktualizować ten obszar roboczy.

```kusto
let DailyCapResetHour=14;
Usage
| where Type !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend TimeGenerated=datetime_add("hour",-1*DailyCapResetHour,TimeGenerated)
| where TimeGenerated > startofday(ago(31d))
| where IsBillable
| summarize IngestedGbBetweenDailyCapResets=sum(_BilledSize)/1000. by day=bin(TimeGenerated, 1d) | render areachart  
```

### <a name="alert-when-daily-cap-reached"></a>Zgłoś alert po osiągnięciu dziennego limitu

Podczas prezentowania wizualnej wskazówki w Azure Portal po spełnieniu progu limitu danych takie zachowanie nie musi być dostosowane do sposobu zarządzania problemami operacyjnymi wymagającymi natychmiastowej uwagi.  Aby otrzymać powiadomienie o alercie, można utworzyć nową regułę alertu w Azure Monitor.  Aby dowiedzieć się więcej, zobacz [jak tworzyć, wyświetlać i zarządzać alertami](alerts-metric.md).

Aby rozpocząć, poniżej przedstawiono zalecane ustawienia alertu dotyczącego `Operation` tabeli przy użyciu `_LogOperation` funkcji. 

- Cel: wybierz zasób Log Analytics
- Określonych 
   - Nazwa sygnału: niestandardowe wyszukiwanie w dzienniku
   - Zapytanie wyszukiwania: `_LogOperation | where Category == "Ingestion" | where Operation == "Ingestion rate" | where Level == "Warning"`
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
find where TimeGenerated > ago(24h) project Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

Aby uzyskać listę węzłów wysyłających dowolne dane (i ilość danych wysyłanych przez poszczególne), można użyć poniższego zapytania:

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

### <a name="nodes-billed-by-the-legacy-per-node-pricing-tier"></a>Węzły rozliczane według warstwy cenowej starszej dla węzła

[Warstwa cenowa starszej wersji na węzeł](#legacy-pricing-tiers) jest rozliczana dla węzłów z dokładnością co godzinę, a także nie zlicza węzłów wysyłających zestaw danych zabezpieczeń. Dzienna liczba węzłów będzie bliska następującej kwerendzie:

```kusto
find where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now()) project Computer, _IsBillable, Type, TimeGenerated
| where Type !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| where _IsBillable == true
| summarize billableNodesPerHour=dcount(computerName) by bin(TimeGenerated, 1h)
| summarize billableNodesPerDay = sum(billableNodesPerHour)/24., billableNodeMonthsPerDay = sum(billableNodesPerHour)/24./31.  by day=bin(TimeGenerated, 1d)
| sort by day asc
```

Liczba jednostek na rachunku znajduje się w jednostkach węzła * miesiące, które są reprezentowane przez `billableNodeMonthsPerDay` zapytanie. Jeśli w obszarze roboczym jest zainstalowane rozwiązanie Update Management, Dodaj typy danych Update i UpdateSummary do listy w klauzuli WHERE w powyższej kwerendzie. Na koniec istnieje pewna dodatkowa złożoność w rzeczywistym algorytmie rozliczania, gdy jest używane rozwiązanie określania wartości docelowej, które nie jest reprezentowane w powyższym zapytaniu. 


> [!TIP]
> Te `find` zapytania są oszczędnie zależą od tego, jak skanowanie między typami danych jest [czasochłonne](../log-query/query-optimization.md#query-performance-pane) . Jeśli wyniki **dla komputera** nie są potrzebne, należy wykonać zapytanie dotyczące typu danych użycia (patrz poniżej).

## <a name="understanding-ingested-data-volume"></a>Zrozumienie ilości pozyskiwanych danych

Na stronie **użycie i szacowane koszty** pozyskiwanie *danych na wykres rozwiązań* pokazuje łączną ilość wysłanych danych i ilość wysyłanych przez każde rozwiązanie. Dzięki temu można określić trendy, takie jak to, czy ogólne użycie danych (lub użycie określonego rozwiązania) rośnie, pozostało stałe lub zmniejszane. 

### <a name="data-volume-for-specific-events"></a>Wolumin danych dla określonych zdarzeń

Aby przyjrzeć się ilości pobieranych danych dla określonego zestawu zdarzeń, można wykonać zapytanie dotyczące konkretnej tabeli (w tym przykładzie), `Event` a następnie ograniczyć zapytanie do interesujących Cię zdarzeń (w tym przykładzie zdarzenia o identyfikatorze 5145 lub 5156):

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

Należy zauważyć, że klauzula `where _IsBillable = true` filtruje typy danych z niektórych rozwiązań, dla których nie jest naliczana opłata za pozyskiwanie. [Dowiedz się więcej](./log-standard-columns.md#_isbillable) o programie `_IsBillable` .

### <a name="data-volume-by-solution"></a>Ilość danych wg rozwiązania

Zapytanie używane do wyświetlania ilości danych do rozliczenia według rozwiązania w ciągu ostatniego miesiąca (z wyjątkiem ostatniego częściowego dnia) to:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

Klauzula WITH `TimeGenerated` służy tylko do upewnienia się, że obsługa zapytań w Azure Portal będzie wyglądać poza domyślnymi 24 godzinami. W przypadku użycia typu danych użycia i reprezentowania przedziałów `StartTime` `EndTime` czasu, dla których są wyświetlane wyniki. 

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

`Usage`Typ danych nie zawiera informacji na poziomie komputera. Aby wyświetlić **rozmiar** pobieranych danych na komputer, użyj `_BilledSize` [Właściwości](./log-standard-columns.md#_billedsize), która zapewnia rozmiar w bajtach:

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, _IsBillable, Computer
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName 
| sort by BillableDataBytes nulls last
```

`_IsBillable` [Właściwość](./log-standard-columns.md#_isbillable) określa, czy pozyskane dane będą naliczane opłaty. 

Aby zobaczyć **liczbę** zdarzeń rozliczanych pobieranych na komputer, użyj 

```kusto
find where TimeGenerated > ago(24h) project _IsBillable, Computer
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  
| sort by eventCount nulls last
```

> [!TIP]
> Te `find` zapytania są oszczędnie zależą od tego, jak skanowanie między typami danych jest [czasochłonne](../log-query/query-optimization.md#query-performance-pane) . Jeśli wyniki **dla komputera** nie są potrzebne, należy wykonać zapytanie dotyczące typu danych użycia.

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Ilość danych według zasobu platformy Azure, grupy zasobów lub subskrypcji

W przypadku danych z węzłów hostowanych na platformie Azure można uzyskać **rozmiar** pobieranych danych __na komputer__, używając [Właściwości](./log-standard-columns.md#_resourceid)_ResourceId, która zapewnia pełną ścieżkę do zasobu:

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by BillableDataBytes nulls last
```

W przypadku danych z węzłów hostowanych na platformie Azure możesz uzyskać **rozmiar** pozyskanych danych __na subskrypcję platformy Azure__, pobrać identyfikator subskrypcji `_ResourceId` właściwości jako:

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId
| extend subscriptionId = tostring(split(_ResourceId, "/")[2]) 
| summarize BillableDataBytes = sum(BillableDataBytes) by subscriptionId | sort by BillableDataBytes nulls last
```

Podobnie, aby pobrać ilość danych według grupy zasobów:

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId
| extend resourceGroup = tostring(split(_ResourceId, "/")[4] )
| summarize BillableDataBytes = sum(BillableDataBytes) by resourceGroup | sort by BillableDataBytes nulls last
```

Możesz również przeanalizować `_ResourceId` bardziej szczegółowo, jeśli jest to konieczne.

```Kusto
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
```

> [!TIP]
> Te `find` zapytania są oszczędnie zależą od tego, jak skanowanie między typami danych jest [czasochłonne](../log-query/query-optimization.md#query-performance-pane) . Jeśli nie potrzebujesz wyników na subskrypcję, grupę zasobów lub nazwę zasobu, a następnie wykonaj zapytanie dotyczące typu danych użycia.

> [!WARNING]
> Niektóre pola typu danych użycia, ale nadal w schemacie, są przestarzałe i ich wartości nie będą już wypełnione. Są to **komputery** , a także pola związane z pozyskiwaniem (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** i **AverageProcessingTimeMs**.


### <a name="querying-for-common-data-types"></a>Wykonywanie zapytań dotyczących typowych typów danych

Aby Dig bardziej szczegółowo źródło danych dla określonego typu danych, Oto kilka przydatnych zapytań przykładowych:

+ Zasoby **Application Insights oparte na obszarze roboczym**
  - Dowiedz się więcej na temat [zarządzania użyciem i kosztami Application Insights](../app/pricing.md#data-volume-for-workspace-based-application-insights-resources)
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

## <a name="tips-for-reducing-data-volume"></a>Wskazówki dotyczące zmniejszania ilości danych

Niektóre sugestie dotyczące zmniejszenia ilości zbieranych dzienników obejmują:

| Źródło dużego woluminu danych | Jak zmniejszyć wolumin danych |
| -------------------------- | ------------------------- |
| Analizy kontenerów         | [Skonfiguruj usługi Container Insights](../insights/container-insights-cost.md#controlling-ingestion-to-reduce-cost) , aby zbierać tylko wymagane dane. |
| Zdarzenia zabezpieczeń            | Wybierz [pospolite lub minimalne zdarzenia zabezpieczeń](../../security-center/security-center-enable-data-collection.md#data-collection-tier). <br> Zmień zasady inspekcji zabezpieczeń w celu zbierania tylko potrzebnych zdarzeń. W szczególności zastanów się nad koniecznością zbierania następujących zdarzeń: <br> - [inspekcja platformy filtrowania](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772749(v=ws.10)) <br> - [inspekcja rejestru](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [inspekcja systemu plików](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [inspekcja obiektu jądra](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [inspekcja manipulowania dojściem](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> Inspekcja magazynu wymiennego |
| Liczniki wydajności       | Zmień [konfigurację licznika wydajności](data-sources-performance-counters.md) w następujący sposób: <br> — Zmniejsz częstotliwość gromadzenia <br> — Zmniejsz liczbę liczników wydajności |
| Dzienniki zdarzeń                 | Zmień [konfigurację dziennika zdarzeń](data-sources-windows-events.md) w następujący sposób: <br> — Zmniejsz liczbę gromadzonych danych dzienników zdarzeń <br> — Zbieraj wyłącznie zdarzenia o wymaganym poziomie. Na przykład nie zbieraj zdarzeń na poziomie *Informacje*. |
| Dziennik systemu                     | Zmień [konfigurację dziennika systemu](data-sources-syslog.md) w następujący sposób: <br> — Zmniejsz liczbę urządzeń, z których zbierane są dane <br> — Zbieraj wyłącznie zdarzenia o wymaganym poziomie. Na przykład nie zbieraj zdarzeń na poziomie *Informacje* i *Debugowanie*. |
| AzureDiagnostics           | Zmień kolekcję dziennika zasobów, aby: <br> — zmniejszyć liczbę dzienników zasobów wysyłanych do usługi Log Analytics, <br> — zbierać tylko wymagane dzienniki. |
| Dane rozwiązań z komputerów, które nie wymagają rozwiązania | Użyj funkcji [określania celu rozwiązania](../insights/solution-targeting.md) , aby zbierać dane tylko z wymaganych grup komputerów. |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>Pobieranie węzłów jako rozliczane w warstwie cenowej na węzeł

Aby uzyskać listę komputerów, które będą rozliczane jako węzły w przypadku, gdy obszar roboczy znajduje się w starszej warstwie cenowej węzła, poszukaj węzłów, które wysyłają **opłaty za typy danych** (niektóre typy danych są bezpłatne). W tym celu należy użyć `_IsBillable` [Właściwości](./log-standard-columns.md#_isbillable) i użyć pola z lewej strony w w pełni kwalifikowanej nazwy domeny. To zwraca liczbę komputerów z danymi rozliczanymi za godzinę (czyli stopnia szczegółowości, w których węzły są zliczane i są rozliczane):

```kusto
find where TimeGenerated > ago(24h) project Computer, TimeGenerated
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

Aby ułatwić tę ocenę, można użyć następującego zapytania, aby zastosować zalecenie dotyczące optymalnej warstwy cenowej na podstawie wzorców użytkowania obszaru roboczego.  To zapytanie przegląda monitorowane węzły i dane pozyskane w obszarze roboczym w ciągu ostatnich 7 dni, a dla każdego dnia szacuje się, która warstwa cenowa byłaby optymalna. Aby użyć zapytania, należy określić

1. czy obszar roboczy używa Azure Security Center przez ustawienie `workspaceHasSecurityCenter` na `true` lub `false` , 
2. Zaktualizuj ceny, jeśli masz określone zniżki i
3. Określ liczbę dni, przez którą ma zostać wyszukane i przeanalizowanie według ustawienia `daysToEvaluate` . Jest to przydatne, jeśli zapytanie trwa zbyt długo, próbując przeglądać dane przez 7 dni. 

Oto zapytanie o rekomendacje warstwy cenowej:

```kusto
// Set these parameters before running query
let workspaceHasSecurityCenter = true;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your montly price per monitored nodes
let PerNodeOveragePrice = 2.30; // Enter your price per GB for data overage in the Per Node pricing tier
let PerGBPrice = 2.30; // Enter your price per GB in the Pay-as-you-go pricing tier
let daysToEvaluate = 7; // Enter number of previous days look at (reduce if the query is taking too long)
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
let StartDate = startofday(datetime_add("Day",-1*daysToEvaluate,now()));
let EndDate = startofday(now());
union * 
| where TimeGenerated >= StartDate and TimeGenerated < EndDate
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join kind=leftouter (
    Heartbeat 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where Computer != ""
    | summarize ASCnodesPerHour = dcount(Computer) by bin(TimeGenerated, 1h) 
    | extend ASCnodesPerHour = iff(workspaceHasSecurityCenter, ASCnodesPerHour, 0)
    | summarize ASCnodesPerDay = sum(ASCnodesPerHour)/24.  by day=bin(TimeGenerated, 1d)   
) on day
| join (
    Usage 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
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
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerNodeOveragePrice
| extend Recommendation = iff(PerNodeDailyCost < PerGBDailyCost, "Per Node tier", 
             iff(NonSecurityDataGB > 85., "Capacity Reservation tier", "Pay-as-you-go (Per GB) tier"))
| project day, nodesPerDay, ASCnodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, Recommendation | sort by day asc
//| project day, Recommendation // Comment this line to see details
| sort by day asc
```

To zapytanie nie jest dokładną replikacją sposobu obliczania użycia, ale będzie działała w większości przypadków w celu zapewnienia zaleceń dotyczących warstwy cenowej.  

> [!NOTE]
> Aby użyć uprawnień pochodzących z zakupu pakietu OMS E1, pakietu OMS E2 lub Add-On OMS dla programu System Center, wybierz warstwę cenową Log Analytics *na węzeł* .

## <a name="create-an-alert-when-data-collection-is-high"></a>Utwórz alert, gdy zbieranie danych jest wysokie

W tej sekcji opisano sposób tworzenia alertu, który ilość danych w ostatnich 24 godzinach przekroczyła określoną ilość przy użyciu [alertów dziennika](alerts-unified-log.md)Azure monitor. 

Aby alertować, jeśli ilość danych do rozliczenia w ostatnich 24 godzinach była większa niż 50 GB, wykonaj następujące kroki: 

- **Zdefiniuj warunek alertu** — określ obszar roboczy usługi Log Analytics jako element docelowy zasobu.
- **Kryteria alertu** — określ następujące informacje:
   - **Nazwa sygnału** — wybierz pozycję **Przeszukiwanie dzienników niestandardowych**
   - **Wyszukaj zapytanie** do `Usage | where IsBillable | summarize DataGB = sum(Quantity / 1000.) | where DataGB > 50` . Jeśli chcesz użyć innego 
   - **Alert logiki****opiera się na** *liczbie wyników*, a **warunek** jest *większy niż***próg** wynoszący *0*
   - **Okres** wynoszący *1440* minut i **częstotliwość alertów** do co *1440* minut, które mają być uruchamiane raz dziennie.
- **Zdefiniuj szczegóły alertu** — określ następujące informacje:
   - **Nazwa** z *ilością danych do rozliczenia większa niż 50 GB w ciągu 24 godzin*
   - **Ważność** na *Ostrzeżenie*

Określ istniejącą [grupę akcji](action-groups.md) lub utwórz nową, tak aby otrzymywać powiadomienie, gdy alert dziennika spełni kryteria.

Po otrzymaniu alertu wykonaj kroki opisane w powyższych sekcjach, aby rozwiązać problem, dlaczego użycie jest wyższe niż oczekiwano.

## <a name="data-transfer-charges-using-log-analytics"></a>Opłaty za transfer danych przy użyciu Log Analytics

Wysyłanie danych do Log Analytics może spowodować naliczenie opłat za przepustowość danych. Zgodnie z opisem na [stronie cennika przepustowości platformy Azure](https://azure.microsoft.com/pricing/details/bandwidth/)transfer danych między usługami platformy Azure znajdującymi się w dwóch regionach jest naliczany jako wychodzący transfer danych. Transfer danych przychodzących jest bezpłatny. Ta opłata jest jednak bardzo mała (kilka%) w porównaniu z kosztami pozyskiwania danych Log Analytics. W związku z tym, kontrolując koszty Log Analytics muszą skupić się na pozyskiwanym [woluminie danych](#understanding-ingested-data-volume). 


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Rozwiązywanie problemów dlaczego Log Analytics nie zbiera już danych

Jeśli korzystasz z starszej warstwy cenowej bezpłatnej i w ciągu dnia wyślesz więcej niż 500 MB danych, zbieranie danych zostanie zatrzymane w pozostałej części dnia. Osiągnięcie dziennego limitu jest typowym powodem, Log Analytics przestaje zbierać dane lub wydaje się, że brakuje danych.  Log Analytics tworzy zdarzenie typu operacja podczas uruchamiania i zatrzymywania zbierania danych. Uruchom następujące zapytanie w obszarze wyszukiwania, aby sprawdzić, czy osiągnięto limit dzienny i brakujące dane: 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

Jeśli zbieranie danych jest zatrzymane, stan OperationStatus ma wartość **Ostrzeżenie**. Jeśli zbieranie danych zostało rozpoczęte, stan OperationStatus ma wartość **Powodzenie**. W poniższej tabeli opisano przyczyny zatrzymania zbierania danych oraz sugerowaną akcję wznowienia zbierania danych:  

|Zakończenie zbierania danych o przyczynie| Rozwiązanie| 
|-----------------------|---------|
|Osiągnięto dzienny limit Twojego obszaru roboczego|Poczekaj na automatyczne ponowne uruchomienie kolekcji lub Zwiększ dzienny limit ilości danych opisany w temacie Zarządzanie maksymalnym dziennym woluminem danych. Dzienny czas resetowania zostanie wyświetlony na stronie **dzienne zakończenie** . |
| W Twoim obszarze roboczym osiągnięto [współczynnik głośności](../service-limits.md#log-analytics-workspaces) pozyskiwania danych | Domyślny limit szybkości pozyskiwania danych wysyłanych z zasobów platformy Azure przy użyciu ustawień diagnostycznych wynosi około 6 GB/min na obszar roboczy. Jest to przybliżona wartość, ponieważ dokładny rozmiar może się różnić w zależności od typu danych, długości dziennika i jego poziomu kompresji. Ten limit nie dotyczy danych wysyłanych z agentów ani interfejsu API modułu zbierającego dane. Jeśli dane wysyłane są szybciej do jednego obszaru roboczego, niektóre z nich mogą zostać utracone, a w czasie, w którym limit jest przekroczony, co sześć godzin jest wysyłane zdarzenie do tabeli Operacja w obszarze roboczym. Jeśli ilość pozyskiwanych danych stale przekracza limit szybkości lub spodziewasz się osiągnąć ten limit wkrótce, możesz zażądać zwiększenia limitu dla obszaru roboczego, wysyłając wiadomość e-mail na adres LAIngestionRate@microsoft.com lub wniosek o pomoc techniczną. Zdarzenie, które wskazuje na limit szybkości pozyskiwania danych, można znaleźć przy użyciu zapytania `Operation | where OperationCategory == "Ingestion" | where Detail startswith "The rate of data crossed the threshold"`. |
|Osiągnięto dzienny limit starszych bezpłatnych warstw cenowych |Poczekaj na automatyczne ponowne uruchomienie kolekcji lub Zmień ją na płatną warstwę cenową.|
|Subskrypcja platformy Azure jest w stanie wstrzymania z powodu:<br> Bezpłatna wersja próbna została zakończona<br> Upłynął okres ważności platformy Azure<br> Osiągnięto miesięczny limit wydatków (na przykład w subskrypcji MSDN lub Visual Studio)|Konwersja na płatną subskrypcję<br> Usuń limit lub zaczekaj na zresetowanie limitu|

Aby otrzymywać powiadomienia, gdy zbieranie danych zostanie zatrzymane, wykonaj kroki opisane w temacie Tworzenie alertu *dziennego zakończenia danych* , aby otrzymywać powiadomienia o zatrzymaniu zbierania danych. Wykonaj kroki opisane w sekcji [Tworzenie grupy akcji](action-groups.md) , aby skonfigurować akcję poczty e-mail, elementu webhook lub elementu Runbook dla reguły alertu. 

## <a name="limits-summary"></a>Podsumowanie limitów

Istnieją pewne dodatkowe limity Log Analytics, które są zależne od warstwy cenowej Log Analytics. Są one udokumentowane w [ramach subskrypcji platformy Azure i ograniczeń usługi, przydziałów i ograniczeń](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces).


## <a name="next-steps"></a>Następne kroki

- Zobacz [Wyszukiwanie w dzienniku w](../log-query/log-query-overview.md) dziennikach Azure monitor, aby dowiedzieć się, jak używać języka wyszukiwania. Możesz użyć zapytań wyszukiwania w celu przeprowadzenia dodatkowej analizy danych użycia.
- Wykonaj kroki opisane w sekcji dotyczącej [tworzenia nowego alertu dziennika](alerts-metric.md), aby otrzymywać powiadomienie, gdy kryteria wyszukiwania zostaną spełnione.
- Użyj funkcji [określania celu rozwiązania](../insights/solution-targeting.md) , aby zbierać dane tylko z wymaganych grup komputerów.
- Aby skonfigurować efektywne zasady zbierania zdarzeń, przejrzyj [zasady filtrowania Azure Security Center](../../security-center/security-center-enable-data-collection.md).
- Zmień [konfigurację licznika wydajności](data-sources-performance-counters.md).
- Aby zmodyfikować ustawienia zbierania zdarzeń, przejrzyj [konfigurację dziennika zdarzeń](data-sources-windows-events.md).
- Aby zmodyfikować ustawienia kolekcji dziennika systemowego, zapoznaj się z tematem [Konfiguracja dziennika](data-sources-syslog.md)systemu.