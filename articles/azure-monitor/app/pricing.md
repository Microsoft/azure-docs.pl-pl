---
title: Zarządzanie użyciem i kosztami usługi Azure Application Insights | Dokumenty firmy Microsoft
description: Zarządzanie woluminami telemetrii i monitorowanie kosztów w usłudze Application Insights.
ms.topic: conceptual
author: DaleKoetke
ms.author: dalek
ms.date: 11/27/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 0225484de06ae4e595f1dcbcdd520f4e0e4d53f5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405386"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Zarządzanie użyciem i kosztami usługi Application Insights

> [!NOTE]
> W tym artykule opisano, jak zrozumieć i kontrolować koszty usługi Application Insights.  Powiązany [artykuł, Monitorowanie użycia i szacowane koszty](https://docs.microsoft.com/azure/azure-monitor/platform/usage-estimated-costs) opisano sposób wyświetlania użycia i szacowanych kosztów w wielu funkcjach monitorowania platformy Azure dla różnych modeli cenowych.

Usługa Application Insights została zaprojektowana tak, aby uzyskać wszystko, czego potrzebujesz do monitorowania dostępności, wydajności i użycia aplikacji sieci web, niezależnie od tego, czy są one hostowane na platformie Azure, czy lokalnie. Usługa Application Insights obsługuje popularne języki i struktury, takie jak .NET, Java i Node.js, i integruje się z procesami i narzędziami DevOps, takimi jak Azure DevOps, Jira i PagerDuty. Ważne jest, aby zrozumieć, co określa koszty monitorowania aplikacji. W tym artykule przejrzymy, co napędza koszty monitorowania aplikacji i jak można aktywnie monitorować i kontrolować je.

Jeśli masz pytania dotyczące sposobu ustalania cen w aplikacji Insights, możesz zadać pytanie na naszym [forum.](https://social.msdn.microsoft.com/Forums/home?forum=ApplicationInsights&filter=alltypes&sort=lastpostdesc)

## <a name="pricing-model"></a>Model cen

Cennik [usługi Azure Application Insights][start] to model płatności zgodnie z rzeczywistym **użyciem** oparty na ilości danych pozyskiwania i opcjonalnie dla dłuższego przechowywania danych. Każdy zasób usługi Application Insights jest naliczany jako oddzielna usługa i przyczynia się do rachunku za subskrypcję platformy Azure. Ilość danych jest mierzona jako rozmiar nieskompresowanego pakietu danych JSON, który jest odbierany przez aplikację Application Insights z aplikacji. Korzystanie ze [strumienia metryk na żywo](../../azure-monitor/app/live-stream.md)nie jest obciążne żadnymi opłatami za ilość danych.

[Wieloetapowe testy internetowe](../../azure-monitor/app/availability-multistep.md) pobierają dodatkową opłatę. Wieloetapowe testy sieci web są testy sieci web, które wykonują sekwencję akcji. Nie ma osobnej opłaty za *testy pingu* na jednej stronie. Dane telemetryczne z testów ping i testów wieloetapowych są ładowane tak samo jak inne dane telemetryczne z aplikacji.

Usługa Application Insights opcja [Włącz alerty dotyczące niestandardowych wymiarów metryki](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics#custom-metrics-dimensions-and-pre-aggregation) może również generować dodatkowe koszty, ponieważ może to spowodować utworzenie dodatkowych metryk wstępnej agregacji. [Dowiedz się więcej](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics) o metrykach opartych na dziennikach i wstępnie zagregowanych w usłudze Application Insights oraz o [cenach](https://azure.microsoft.com/pricing/details/monitor/) metryk niestandardowych usługi Azure Monitor.

## <a name="estimating-the-costs-to-manage-your-application"></a>Szacowanie kosztów zarządzania aplikacją

Jeśli nie używasz jeszcze usługi Application Insights, możesz użyć [kalkulatora cen usługi Azure Monitor,](https://azure.microsoft.com/pricing/calculator/?service=monitor) aby oszacować koszt korzystania z usługi Application Insights. Zacznij od wprowadzenia "Azure Monitor" w polu wyszukiwania i kliknięcia powstałego kafelka usługi Azure Monitor. Przewiń stronę w dół do usługi Azure Monitor i wybierz usługę Application Insights z listy rozwijanej Typ.  W tym miejscu można wprowadzić liczbę GB danych, które mają być zbierane miesięcznie, więc pytanie brzmi, ile danych będzie zbierać usługi Application Insights monitorowania aplikacji.

Istnieją dwa podejścia, aby rozwiązać ten problem: użycie domyślnego monitorowania i próbkowania adaptacyjnego, które jest dostępne w ASP.NET SDK, lub oszacować prawdopodobnego pozyskiwania danych na podstawie tego, co widzieli inni podobni klienci.

### <a name="data-collection-when-using-sampling"></a>Gromadzenie danych podczas pobierania próbek

Dzięki [adaptacyjnemu próbkowaniu](sampling.md#adaptive-sampling)ASP.NET SDK ilość danych jest dostosowywana automatycznie, aby utrzymywać w określonym maksymalnym tempie ruchu dla domyślnego monitorowania usługi Application Insights. Jeśli aplikacja generuje małą ilość danych telemetrycznych, takich jak podczas debugowania lub z powodu niskiego użycia, elementy nie zostaną usunięte przez procesor próbkowania, tak długo, jak wolumin jest poniżej skonfigurowanych zdarzeń na poziom sekundy. W przypadku aplikacji o dużej objętości z domyślnym progiem pięciu zdarzeń na sekundę adaptacyjne próbkowanie ograniczy liczbę zdarzeń dziennych do 432 000. Przy użyciu typowego średniego rozmiaru zdarzenia 1 KB odpowiada to 13,4 GB danych telemetrycznych na 31-dniowy miesiąc na węzeł hostowania aplikacji (ponieważ próbkowanie odbywa się lokalnie dla każdego węzła). 

W przypadku zestawów SDK, które nie obsługują próbkowania adaptacyjnego, można zastosować [próbkowanie pozyskiwania](https://docs.microsoft.com/azure/azure-monitor/app/sampling#ingestion-sampling), które próbki po otrzymaniu danych przez usługę Application Insights są pobierane na podstawie procentu danych do zachowania lub [próbkowania o stałej szybkości dla witryn sieci Web ASP.NET, ASP.NET Core i Java w](sampling.md#fixed-rate-sampling) celu zmniejszenia ruchu wysyłanego z serwera www i przeglądarek internetowych

### <a name="learn-from-what-similar-customers-collect"></a>Dowiedz się, co zbierają podobni klienci

W kalkulatorze cen monitorowania platformy Azure dla usługi Application Insights, jeśli włączysz funkcję "Oszacuj ilość danych na podstawie działania aplikacji", możesz podać dane wejściowe dotyczące aplikacji (żądania miesięczne i widoki strony miesięcznie, w przypadku, gdy będziesz zbierać dane telemetryczne po stronie klienta), a następnie kalkulator poinformuje Cię o medianie i 90 percentylu ilości danych zebranych przez podobne aplikacje. Aplikacje te obejmują zakres konfiguracji usługi Application Insights (np. niektóre mają [domyślne próbkowanie,](../../azure-monitor/app/sampling.md)niektóre nie mają próbkowania itp.), więc nadal masz kontrolę, aby zmniejszyć ilość danych, które połykasz znacznie poniżej poziomu mediany za pomocą próbkowania. Ale jest to punkt wyjścia, aby zrozumieć, co inni, podobni klienci widzą.

## <a name="understand-your-usage-and-estimate-costs"></a>Zrozumienie sposobu użytkowania i szacowania kosztów

Usługa Application Insights ułatwia zrozumienie, jakie koszty mogą być oparte na najnowszych wzorcach użycia. Aby rozpocząć, w witrynie Azure portal dla zasobu usługi Application Insights przejdź do strony **Użycie i szacowane koszty:**

![Wybierz ceny](./media/pricing/pricing-001.png)

A. Przejrzyj ilość danych za miesiąc. Obejmuje to wszystkie dane, które są odbierane i przechowywane (po każdym [próbkowaniu)](../../azure-monitor/app/sampling.md)z serwera i aplikacji klienckich oraz z testów dostępności.  
B. Oddzielna opłata jest pobierana dla [wieloetapowych testów internetowych.](../../azure-monitor/app/availability-multistep.md) (Nie obejmuje to prostych testów dostępności, które są uwzględnione w oliczce za ilość danych).  
C. Wyświetlanie trendów wolumenu danych w ostatnim miesiącu.  
D. Włącz [próbkowanie](../../azure-monitor/app/sampling.md)pozyskiwania danych .
E. Ustaw dzienny limit głośności danych.  

(Należy pamiętać, że wszystkie ceny wyświetlane na zrzutach ekranu w tym artykule są tylko do celów przykładowych. Aby uzyskać aktualne ceny w walucie i regionie, zobacz [Ustalanie cen usługi Application Insights.)][pricing]

Aby dokładniej zbadać użycie usługi Application Insights, otwórz stronę **Metryki,** dodaj metrykę o nazwie "Wolumin punktu danych", a następnie wybierz opcję *Zastosuj podział,* aby podzielić dane przez "Typ elementu telemetrii".

Opłaty usługi Application Insights są dodawane do rachunku platformy Azure. Szczegółowe informacje o rachunku za usługę Azure można zobaczyć w sekcji **Rozliczenia** w witrynie Azure portal lub w [portalu rozliczeń platformy Azure.](https://account.windowsazure.com/Subscriptions)

![W menu po lewej stronie wybierz pozycję Rozliczenia](./media/pricing/02-billing.png)

### <a name="using-data-volume-metrics"></a>Korzystanie z danych o woluminie danych
<a id="understanding-ingested-data-volume"></a>

Aby dowiedzieć się więcej o woluminach danych, wybierz **pozycję Metryki** zasobu usługi Application Insights, dodaj nowy wykres. Aby uzyskać metrykę wykresu, w obszarze **Metryki oparte na dzienniku**wybierz **pozycję Objętość punktu danych**. Kliknij **pozycję Zastosuj podział**i wybierz grupę według ** `Telemetryitem` typu**.

![Przeglądanie woluminu danych za pomocą danych](./media/pricing/10-billing.png)

### <a name="queries-to-understand-data-volume-details"></a>Zapytania o zrozumienie szczegółów woluminu danych

Istnieją dwa podejścia do badania woluminów danych dla usługi Application Insights. Pierwszy używa zagregowanych informacji `systemEvents` w tabeli, a `_BilledSize` drugi używa właściwości, która jest dostępna dla każdego pochłoniętym zdarzeniem.

#### <a name="using-aggregated-data-volume-information"></a>Korzystanie z zagregowanych informacji o woluminie danych

Na przykład można użyć `systemEvents` tabeli, aby zobaczyć wolumin danych przynajmowany w ciągu ostatnich 24 godzin za pomocą kwerendy:

```kusto
systemEvents
| where timestamp >= ago(24h)
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

Aby wyświetlić wykres woluminu danych (w bajtach) według typu danych z ostatnich 30 dni, można użyć:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes) by BillingTelemetryType, bin(timestamp, 1d) | render barchart  
```

Należy zauważyć, że ta kwerenda może służyć w [dzienniku azure alert](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log) do konfigurowania alertów na woluminach danych.  

Aby dowiedzieć się więcej o zmianach danych telemetrycznych, możemy uzyskać liczbę zdarzeń według typu za pomocą kwerendy:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| summarize count() by BillingTelemetryType, bin(timestamp, 1d)
| render barchart  
```

#### <a name="using-data-size-per-event-information"></a>Korzystanie z rozmiaru danych na informacje o zdarzeniu

Aby dowiedzieć się więcej szczegółów na temat źródła `_BilledSize` woluminów danych, można użyć właściwości, która jest obecna w każdym zdarzeniu pozyskane.

Na przykład, aby sprawdzić, które operacje generują najwięcej danych w ciągu `_BilledSize` ostatnich 30 dni, możemy podsumować wszystkie zdarzenia zależności:

```kusto
dependencies
| where timestamp >= startofday(ago(30d))
| summarize sum(_BilledSize) by operation_Name
| render barchart  
```

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>Wyświetlanie użycia usługi Application Insights na rachunku platformy Azure

Platforma Azure oferuje wiele przydatnych funkcji w centrum [Azure Cost Management + Billing](https://docs.microsoft.com/azure/cost-management/quick-acm-cost-analysis?toc=/azure/billing/TOC.json) Hub. Na przykład funkcja "Analiza kosztów" umożliwia wyświetlanie wydatków na zasoby platformy Azure. Dodanie filtru według typu zasobu (do witryny microsoft.insights/components for Application Insights) umożliwia śledzenie wydatków.

Więcej zrozumienia użycia można uzyskać, [pobierając użycie z witryny Azure portal.](https://docs.microsoft.com/azure/billing/billing-download-azure-invoice-daily-usage-date#download-usage-in-azure-portal)
W pobranym arkuszu kalkulacyjnym możesz zobaczyć użycie na zasób platformy Azure dziennie. W tym arkuszu kalkulacyjnym programu Excel użycie zasobów usługi Application Insights można znaleźć, filtrując najpierw kolumnę "Kategoria miernika", aby wyświetlić "Usługa Application Insights" i "Log Analytics", a następnie dodając filtr w kolumnie "Identyfikator wystąpienia", która jest "zawiera microsoft.insights/components".  Większość użycia usługi Application Insights jest raportowana na licznikach z kategorią mierników usługi Log Analytics, ponieważ istnieje pojedyncza wiązka danych zaplecza dzienników dla wszystkich składników usługi Azure Monitor.  Tylko zasoby usługi Application Insights dotyczące starszych warstw cenowych i wieloetapowych testów sieci Web są zgłaszane za pomocą kategorii mierników usługi Application Insights.  Użycie jest wyświetlane w kolumnie "Ilość zużyta", a jednostka dla każdego wpisu jest wyświetlana w kolumnie "Jednostka miary".  Więcej informacji można znaleźć, aby ułatwić [zrozumienie rachunku za pomocą platformy Microsoft Azure.](https://docs.microsoft.com/azure/billing/billing-understand-your-bill)

## <a name="managing-your-data-volume"></a>Zarządzanie woluminem danych

Ilością wysyłanych danych można zarządzać za pomocą następujących technik:

* **Próbkowanie: Próbkowanie:** Można użyć próbkowania, aby zmniejszyć ilość danych telemetrycznych wysyłanych z serwera i aplikacji klienckich przy minimalnym zniekształceniu metryk. Próbkowanie jest podstawowym narzędziem, którego można użyć do dostrojenia ilości wysyłanych danych. Dowiedz się więcej o [funkcjach próbkowania](../../azure-monitor/app/sampling.md).

* **Ogranicz połączenia Ajax:** Można [ograniczyć liczbę połączeń Ajax, które mogą być zgłaszane](../../azure-monitor/app/javascript.md#configuration) w każdym widoku strony, lub wyłączyć raportowanie Ajax.

* **Wyłącz niepotrzebne moduły:** [Edytuj plik ApplicationInsights.config,](../../azure-monitor/app/configuration-with-applicationinsights-config.md) aby wyłączyć moduły kolekcji, których nie potrzebujesz. Na przykład można zdecydować, że liczniki wydajności lub dane zależności są nieistotne.

* **Wstępnie zagregowane dane:** Jeśli umieścisz wywołania TrackMetric w aplikacji, możesz zmniejszyć ruch przy użyciu przeciążenia, które akceptuje obliczanie średniego i standardowego odchylenia partii pomiarów. Można też użyć [pakietu wstępnego agregowania](https://www.myget.org/gallery/applicationinsights-sdk-labs).
 
* **Dzienny limit:** Podczas tworzenia zasobu usługi Application Insights w witrynie Azure portal dzienny limit jest ustawiony na 100 GB/dzień. Podczas tworzenia zasobu usługi Application Insights w programie Visual Studio wartość domyślna jest mała (tylko 32,3 MB/dzień). Domyślny limit dzienny jest ustawiony w celu ułatwienia testowania. Jest przeznaczony, że użytkownik podniesie dzienny limit przed wdrożeniem aplikacji w środowiskach produkcyjnych. 

    Maksymalny limit wynosi 1000 GB/dzień, chyba że zażądasz wyższego maksimum dla aplikacji o dużym natężeniu ruchu.
    
    Wiadomości e-mail z ostrzeżeniem o dziennym ograniczeniu są wysyłane do konta, które są członkami tych ról dla zasobu usługi Application Insights: "ServiceAdmin", "AccountAdmin", "CoAdmin", "Owner".

    Należy uważać, gdy ustawisz dzienny limit. Twoim zamiarem powinno być *nigdy nie trafić dzienny cap*. Jeśli natrzymasz dzienny limit, utracisz dane przez pozostałą część dnia i nie możesz monitorować aplikacji. Aby zmienić dzienny limit, użyj opcji **Dzienny limit głośności.** Dostęp do tej opcji można uzyskać w okienku **Użycie i szacowane koszty** (opisano to bardziej szczegółowo w dalszej części artykułu).
    
    Usunęliśmy ograniczenie dotyczące niektórych typów subskrypcji, które mają kredyt, który nie może być używany w usłudze Application Insights. Wcześniej, jeśli subskrypcja ma limit wydatków, codzienne okno dialogowe cap ma instrukcje, aby usunąć limit wydatków i umożliwić dzienny limit, który ma zostać podniesiony powyżej 32,3 MB/dzień.
    
* **Ograniczanie:** ograniczanie ogranicza szybkość transmisji danych do 32 000 zdarzeń na sekundę, średnio ponad 1 minutę na klucz instrumentacji. Ilość wysyłanych przez aplikację danych jest oceniana co minutę. Jeśli przekracza stawkę na sekundę uśrednioną w ciągu minuty, serwer odrzuca niektóre żądania. Zestaw SDK buforuje dane, a następnie próbuje ponownie go wyśmiać. Rozprzestrzenia się wzrost w ciągu kilku minut. Jeśli aplikacja konsekwentnie wysyła dane z szybkością większą niż ograniczanie przepustowości, niektóre dane zostaną usunięte. (zestawy SDK ASP.NET, Java i JavaScript próbują ponownie wysłać dane w ten sposób; inne zestawy SDK mogą po prostu upuścić ograniczone dane). W przypadku ograniczania przepustowości ostrzeżenie powiadomienie ostrzega, że to nastąpiło.

## <a name="manage-your-maximum-daily-data-volume"></a>Zarządzanie maksymalną dzienną ilością danych

Aby ograniczyć zbierane dane, można użyć dziennego limitu woluminu. Jednak jeśli limit jest spełniony, występuje utrata wszystkich danych telemetrycznych wysłanych z aplikacji na pozostałą część dnia. Nie jest *wskazane,* aby aplikacja uderzyła w dzienny limit. Nie można śledzić kondycji i wydajności aplikacji po osiągnięciu dziennego limitu.

Zamiast używać dziennego limitu woluminu, użyj [próbkowania,](../../azure-monitor/app/sampling.md) aby dostroić wolumin danych do żądanego poziomu. Następnie należy użyć dziennego limitu tylko jako "ostateczności" w przypadku, gdy aplikacja nieoczekiwanie zaczyna wysyłać znacznie większe ilości danych telemetrycznych.

### <a name="identify-what-daily-data-limit-to-define"></a>Określ, jaki dzienny limit danych należy zdefiniować

Przejrzyj użycie usługi Application Insights i szacowane koszty, aby zrozumieć trend pozyskiwania danych i jaki jest dzienny limit woluminu do zdefiniowania. Należy go rozważyć z ostrożnością, ponieważ nie będzie można monitorować swoje zasoby po osiągnięciu limitu.

### <a name="set-the-daily-cap"></a>Ustawianie dziennego limitu

Aby zmienić dzienny limit, w sekcji **Konfigurowanie** zasobu usługi Application Insights na stronie Zużycie i **szacowane koszty** wybierz opcję **Daily Cap**.

![Dostosowywanie dziennego limitu głośności telemetrii](./media/pricing/pricing-003.png)

Aby [zmienić dzienny limit za pośrednictwem usługi Azure Resource Manager,](../../azure-monitor/app/powershell.md)właściwość do zmiany jest `dailyQuota`.  Za pośrednictwem usługi Azure Resource `dailyQuotaResetTime` Manager można również `warningThreshold`ustawić i dzienny limit .

### <a name="create-alerts-for-the-daily-cap"></a>Tworzenie alertów dla daily cap

Codzienna nakładka usługi Application Insights tworzy zdarzenie w dzienniku aktywności platformy Azure, gdy pochłonięte ilości danych osiągną poziom ostrzeżenia lub dzienny limit.  Alert można [utworzyć na podstawie tych zdarzeń dziennika aktywności](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-activity-log#create-with-the-azure-portal). Nazwy sygnałów dla tych zdarzeń to:

* Osiągnięto próg ostrzeżenia o dziennym zaślepce składnika Usługi Application Insights

* Osiągnięto dzienny limit składnika usługi Application Insights

## <a name="sampling"></a>Próbkowanie
[Próbkowanie](../../azure-monitor/app/sampling.md) jest metodą zmniejszenia szybkości, z jaką dane telemetryczne są wysyłane do aplikacji, przy jednoczesnym zachowaniu możliwości znajdowania powiązanych zdarzeń podczas wyszukiwania diagnostycznego. Można również zachować poprawne liczby zdarzeń.

Pobieranie próbek jest skutecznym sposobem na obniżenie opłat i utrzymanie się w ramach miesięcznego limitu. Algorytm próbkowania zachowuje powiązane elementy telemetrii, więc, na przykład, gdy używasz wyszukiwania, można znaleźć żądanie związane z określonym wyjątkiem. Algorytm zachowuje również poprawne liczby, dzięki czemu są widoczne poprawne wartości w Eksploratorze metryk dla stawek żądań, stawek wyjątków i innych zliczeń.

Istnieje kilka form pobierania próbek.

* [Adaptacyjne próbkowanie](../../azure-monitor/app/sampling.md) jest ustawieniem domyślnym dla ASP.NET SDK. Adaptacyjne próbkowanie automatycznie dostosowuje się do ilości danych telemetrycznych wysyłanych przez aplikację. Działa automatycznie w SDK w aplikacji sieci web, dzięki czemu ruch telemetryczny w sieci jest zmniejszona. 
* *Próbkowanie pozyskiwania* jest alternatywą, która działa w miejscu, w którym dane telemetryczne z aplikacji wprowadza usługę usługi Application Insights. Próbkowanie pozyskiwania nie wpływa na ilość danych telemetrycznych wysyłanych z aplikacji, ale zmniejsza wolumin, który jest zachowywany przez usługę. Próbkowanie pozyskiwania służy do zmniejszenia przydziału, który jest używany przez dane telemetryczne z przeglądarek i innych SDK.

Aby ustawić próbkowanie pozyskiwania, przejdź do okienka **Cennik:**

![W okienku Przydział i cennik wybierz kafelek Przykłady, a następnie wybierz ułamek próbkowania](./media/pricing/pricing-004.png)

> [!WARNING]
> **Okienko próbkowania danych** kontroluje tylko wartość pobierania próbek pozyskiwania. Nie odzwierciedla częstotliwości próbkowania, która jest stosowana przez SDK usługi Application Insights w aplikacji. Jeśli przychodzące dane telemetryczne zostały już pobrane próbki w skuborze SDK, pobieranie próbkowania nie jest stosowane.
>

Aby odkryć rzeczywistą częstotliwość próbkowania, niezależnie od tego, gdzie została zastosowana, użyj [zapytania Analytics](analytics.md). Kwerenda wygląda następująco:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

W każdym zachowanym `itemCount` rekordzie wskazuje liczbę oryginalnych rekordów, które reprezentuje. Jest równa 1 + liczba poprzednich odrzuconych rekordów.

## <a name="change-the-data-retention-period"></a>Change the data retention period (Zmienianie okresu przechowywania danych)

Domyślne przechowywanie zasobów usługi Application Insights wynosi 90 dni. Dla każdego zasobu usługi Application Insights można wybrać różne okresy przechowywania. Pełny zestaw dostępnych okresów przechowywania to 30, 60, 90, 120, 180, 270, 365, 550 lub 730 dni.

Aby zmienić przechowywanie, z zasobu usługi Application Insights przejdź do strony **Użycie i Koszty szacunkowe** i wybierz opcję **Przechowywanie danych:**

![Dostosowywanie dziennego limitu głośności telemetrii](./media/pricing/pricing-005.png)

Po obniżeniu retencji istnieje kilkudniowy okres prolongaty przed usunięciem najstarszych danych.

Retencji można również [ustawić programowo przy użyciu programu PowerShell](powershell.md#set-the-data-retention) przy użyciu parametru. `retentionInDays` Jeśli ustawisz przechowywanie danych na 30 dni, można wyzwolić `immediatePurgeDataOn30Days` natychmiastowe przeczyszczanie starszych danych przy użyciu parametru, co może być przydatne w scenariuszach związanych ze zgodnością. Ta funkcja przeczyszczanie jest dostępna tylko za pośrednictwem usługi Azure Resource Manager i powinna być używana ze szczególną ostrożnością. Dzienny czas resetowania limitu woluminu danych można skonfigurować przy `dailyQuotaResetTime` użyciu usługi Azure Resource Manager, aby ustawić parametr.

## <a name="data-transfer-charges-using-application-insights"></a>Opłaty za transfer danych przy użyciu usługi Application Insights

Wysyłanie danych do usługi Application Insights może naliczać opłaty za przepustowość danych. Zgodnie z opisem na [stronie cennik przepustowość platformy Azure](https://azure.microsoft.com/pricing/details/bandwidth/)transfer danych między usługami platformy Azure znajdującymi się w dwóch regionach naliczany jako transfer danych wychodzących według normalnej stawki. Transfer danych przychodzących jest bezpłatny. Jednak opłata ta jest bardzo mała (niewiele %) w porównaniu z kosztami pozyskiwania danych dziennika usługi Application Insights. W związku z tym kontrolowanie kosztów usługi Log Analytics musi koncentrować się na ilości połkniętych danych, a my mamy wskazówki, które pomogą zrozumieć, że [tutaj](https://docs.microsoft.com/azure/azure-monitor/app/pricing#managing-your-data-volume).

## <a name="limits-summary"></a>Podsumowanie limitów

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Wyłączanie codziennych wiadomości e-mail z limitem

Aby wyłączyć wiadomości e-mail z dziennym limitem woluminów, w sekcji **Konfigurowanie** zasobu usługi Application Insights w okienku **Zużycie i szacowane koszty** wybierz pozycję Daily **Cap**. Istnieją ustawienia do wysyłania wiadomości e-mail po osiągnięciu limitu, a także po osiągnięciu regulowanego poziomu ostrzeżenia. Jeśli chcesz wyłączyć wszystkie codzienne e-maile związane z limitem, odznacz oba pola.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Warstwa cenowa Starsze przedsiębiorstwo (na węzeł)

W przypadku wczesnych użytkowników usługi Azure Application Insights nadal istnieją dwie możliwe warstwy cenowe: Podstawowe i Enterprise. Podstawowa warstwa cenowa jest taka sama, jak opisano powyżej i jest domyślną warstwą. Zawiera wszystkie funkcje warstwy Enterprise, bez dodatkowych kosztów. Warstwa Podstawowa rozlicza się przede wszystkim z ilością danych, które są pozyskiwania.

> [!NOTE]
> Zmieniono nazwę tych starszych warstw cenowych. Warstwa cenowa Enterprise jest teraz nazywana **na węzeł,** a podstawowa warstwa cenowa jest teraz nazywana **na GB**. Te nowe nazwy są używane poniżej i w witrynie Azure portal.  

Warstwa Na węzeł (dawniej Enterprise) ma opłatę za węzeł, a każdy węzeł otrzymuje dzienny limit danych. W warstwie cenowej Na węzeł są naliczane opłaty za dane pochłonięte powyżej uwzględnionego limitu. Jeśli używasz pakietu Operations Management Suite, należy wybrać warstwę Na węzeł.

Aby uzyskać aktualne ceny w walucie i regionie, zobacz [Ustalanie cen usługi Application Insights](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> W kwietniu 2018 [r. wprowadziliśmy](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) nowy model cenowy do monitorowania platformy Azure. Ten model przyjmuje prosty model "płatność zgodnie z rzeczywistym". Dowiedz się więcej o [nowym modelu cenowym,](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs)jak [ocenić wpływ przejścia na ten model](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#understanding-your-azure-monitor-costs) na podstawie wzorców użytkowania i jak zdecydować się na nowy [model](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#azure-monitor-pricing-model)

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>Uprawnienia do subskrypcji warstwy na węzeł i pakietu Operations Management Suite

Klienci, którzy zakupili pakiet Operations Management Suite E1 i E2, mogą uzyskać aplikację Insights per node jako dodatkowy składnik bez dodatkowych kosztów, jak [wcześniej ogłoszono.](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/) W szczególności każda jednostka operations management suite E1 i E2 zawiera uprawnienia do jednego węzła warstwy Usługi Application Insights na węzeł. Każdy węzeł usługi Application Insights zawiera do 200 MB danych pozyskiwania dziennie (niezależnie od pozyskiwania danych usługi Log Analytics), z 90-dniowym przechowywaniem danych bez dodatkowych kosztów. Warstwa jest opisana bardziej szczegółowo w dalszej części artykułu.

Ponieważ ta warstwa ma zastosowanie tylko do klientów z subskrypcją pakietu Operations Management Suite, klienci, którzy nie mają subskrypcji pakietu Operations Management Suite, nie widzą opcji wyboru tej warstwy.

> [!NOTE]
> Aby upewnić się, że masz to uprawnienie, zasoby usługi Application Insights muszą znajdować się w warstwie cenowej Na węzeł. To uprawnienie ma zastosowanie tylko jako węzły. Zasoby usługi Application Insights w warstwie Na GB nie przynoszą żadnych korzyści. To uprawnienie nie jest widoczne w szacowanych kosztach wyświetlanych w okienku **Użycie i szacowany koszt.** Ponadto jeśli przeniesiesz subskrypcję do nowego modelu cenowego monitorowania platformy Azure w kwietniu 2018 r., warstwa Na GB jest jedyną dostępną warstwą. Przeniesienie subskrypcji do nowego modelu cenowego monitorowania platformy Azure nie jest wskazane, jeśli masz subskrypcję pakietu Operations Management Suite.

### <a name="how-the-per-node-tier-works"></a>Jak działa warstwa Na węzeł

* Płacisz za każdy węzeł, który wysyła dane telemetryczne dla wszystkich aplikacji w warstwie Na węzeł.
  * *Węzeł* to maszyna serwera fizycznego lub wirtualnego lub wystąpienie roli platformy jako usługi, w którym znajduje się aplikacja.
  * Maszyny programistyczne, przeglądarki klienckie i urządzenia przenośne nie są liczone jako węzły.
  * Jeśli aplikacja ma kilka składników, które wysyłają dane telemetryczne, takie jak usługa sieci web i proces roboczy zaplecza, składniki są liczone oddzielnie.
  * Dane [strumienia metryk na żywo](../../azure-monitor/app/live-stream.md) nie są liczone do celów cenowych. W ramach subskrypcji opłaty są naliczane za węzeł, a nie za aplikację. Jeśli masz pięć węzłów, które wysyłają dane telemetryczne dla 12 aplikacji, opłata jest dla pięciu węzłów.
* Mimo że opłaty są podawane miesięcznie, opłaty są naliczane tylko za godzinę, w której węzeł wysyła dane telemetryczne z aplikacji. Opłata godzinowa to podana opłata miesięczna podzielona przez 744 (liczba godzin w miesiącu 31-dniowym).
* Alokacja woluminu danych 200 MB dziennie jest podana dla każdego wykrytego węzła (z ziarnistością co godzinę). Nieużywane przydzielanie danych nie jest przenoszone z dnia na dzień.
  * Jeśli wybierzesz warstwę cenową Na węzeł, każda subskrypcja pobiera dzienną ilość danych na podstawie liczby węzłów, które wysyłają dane telemetryczne do zasobów usługi Application Insights w tej subskrypcji. Tak jeśli masz pięć węzłów, które wysyłają dane przez cały dzień, będziesz mieć buforowany przydział 1 GB stosowane do wszystkich zasobów usługi Application Insights w tej subskrypcji. Nie ma znaczenia, czy niektóre węzły wysyłają więcej danych niż inne węzły, ponieważ uwzględnione dane są współużytkowane we wszystkich węzłach. Jeśli w danym dniu zasoby usługi Application Insights otrzymują więcej danych niż jest uwzględniony w dziennej alokacji danych dla tej subskrypcji, naliczane są opłaty za dane za GB. 
  * Dzienna ilość danych jest obliczana jako liczba godzin w ciągu dnia (przy użyciu czasu UTC), przez którą każdy węzeł wysyła dane telemetryczne podzielone przez 24 pomnożone przez 200 MB. Tak więc, jeśli masz cztery węzły, które wysyłają dane telemetryczne podczas 15 z 24 godzin w ciągu dnia, dołączone dane dla tego dnia będą ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB. W cenie 2.30 USD za GB za przeładowanie danych, opłata będzie 1.15 USD, jeśli węzły wysłać 1 GB danych tego dnia.
  * Dzienna dieta warstwy Na węzeł nie jest współużytkowana aplikacjom, dla których wybrano warstwę Na GB. Niewykorzystany zasiłek nie jest przenoszony z dnia na dzień.

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Przykłady określania liczby odrębnych węzłów

| Scenariusz                               | Całkowita dzienna liczba węzłów |
|:---------------------------------------|:----------------:|
| 1 aplikacja przy użyciu 3 wystąpień usługi Azure App Service i 1 serwera wirtualnego | 4 |
| 3 aplikacje uruchomione na 2 maszynach wirtualnych; zasoby usługi Application Insights dla tych aplikacji znajdują się w tej samej subskrypcji i w warstwie Na węzeł | 2 | 
| 4 aplikacje, których zasoby usługi Applications Insights znajdują się w tej samej subskrypcji; każda aplikacja działa 2 wystąpienia w 16 godzinach poza szczytem i 4 wystąpienia w 8 godzinach szczytu | 13.33 |
| Usługi w chmurze z 1 rolą procesu roboczego i 1 rolą sieci Web, z których każde działa z 2 wystąpieniami | 4 | 
| Klaster sieci szkieletowej usługi Azure 5 węzłów z uruchomionym 50 mikrousługami; każda mikrousługa z uruchomionym 3 wystąpieniami | 5|

* Dokładne zliczanie węzłów zależy od tego, którego SDK usługi Application Insights używa aplikacja. 
  * W wersji SDK 2.2 i nowszych zarówno podstawowy [moduł SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) aplikacji, jak i [sdk sieci Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) zgłaszają każdy host aplikacji jako węzeł. Przykładami są nazwa komputera dla hostów serwera fizycznego i maszyny Wirtualnej lub nazwa wystąpienia usług w chmurze.  Jedynym wyjątkiem jest aplikacja, która używa tylko [.NET Core](https://dotnet.github.io/) i Application Insights Core SDK. W takim przypadku tylko jeden węzeł jest zgłaszany dla wszystkich hostów, ponieważ nazwa hosta nie jest dostępna.
  * We wcześniejszych wersjach zestawu SDK [zestawu SDK składnika Web SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) zachowuje się jak nowsze wersje zestawu SDK, ale [podstawowy moduł SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) raportuje tylko jeden węzeł, niezależnie od liczby hostów aplikacji.
  * Jeśli aplikacja używa zestawu SDK do ustawiania **roliInstance** do wartości niestandardowej, domyślnie ta sama wartość jest używana do określania liczby węzłów.
  * Jeśli używasz nowej wersji zestawu SDK z aplikacją, która działa z komputerów klienckich lub urządzeń przenośnych, liczba węzłów może zwrócić dużą liczbę (ze względu na dużą liczbę komputerów klienckich lub urządzeń przenośnych).

## <a name="automation"></a>Automatyzacja

Można napisać skrypt, aby ustawić warstwę cenową przy użyciu usługi Azure Resource Management. [Dowiedz się, jak to zrobić](powershell.md#price).

## <a name="next-steps"></a>Następne kroki

* [Próbkowanie](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/