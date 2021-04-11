---
title: Zarządzanie użyciem i kosztami dla usługi Azure Application Insights | Microsoft Docs
description: Zarządzanie woluminami telemetrii i monitorowanie kosztów w Application Insights.
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: DaleKoetke
ms.author: dalek
ms.date: 3/30/2021
ms.reviewer: lagayhar
ms.openlocfilehash: e048e788e674e90a62b15784c590c07e5d36b816
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106078404"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Zarządzanie użyciem i kosztami usługi Application Insights

> [!NOTE]
> W tym artykule opisano sposób zrozumienia i kontrolowania kosztów Application Insights.  W powiązanym artykule, [monitorowaniu użycia i szacowanych kosztów](..//usage-estimated-costs.md) opisano sposób wyświetlania użycia i szacowane koszty w wielu funkcjach monitorowania platformy Azure dla różnych modeli cen.

Application Insights zaprojektowano w celu uzyskania wszystkiego, czego potrzebujesz, aby monitorować dostępność, wydajność i użycie aplikacji sieci Web, niezależnie od tego, czy są one hostowane na platformie Azure, czy lokalnie. Application Insights obsługuje popularne języki i struktury, takie jak .NET, Java i Node.js, i integruje się z procesami DevOps i narzędziami, takimi jak Azure DevOps, JIRA i usługi PagerDuty. Ważne jest, aby zrozumieć, co określa koszty monitorowania aplikacji. W tym artykule opisano, jakie dyski są monitorowane przez aplikację oraz jak można je aktywnie monitorować i kontrolować.

Jeśli masz pytania dotyczące sposobu działania cen dla Application Insights, możesz ogłosić pytanie na naszej stronie pytań i odpowiedzi [Microsoft&pytań](/answers/topics/azure-monitor.html)i odpowiedzi.

## <a name="pricing-model"></a>Model cen

Cennik [usługi Azure Application Insights][start] to model z **płatnością zgodnie z rzeczywistym** użyciem na podstawie ilości danych pozyskiwanych i opcjonalnie do dłuższego przechowywania danych. Każdy zasób Application Insights jest rozliczany jako osobna usługa i przyczynia się do rozliczenia za subskrypcję platformy Azure. Ilość danych jest mierzona jako rozmiar nieskompresowanego pakietu danych JSON, który jest odbierany przez Application Insights z aplikacji. Ilość danych jest mierzona w GB (10 ^ 9 bajtów). Za korzystanie z [Live Metrics Stream](./live-stream.md)nie jest naliczana opłata za ilość danych.

[Wieloetapowe testy sieci Web](./availability-multistep.md) wiążą się z dodatkowymi opłatami. Wieloetapowe testy sieci Web to testy sieci Web, które wykonują sekwencję akcji. Nie ma oddzielnej opłaty za *testy ping* dla pojedynczej strony. Dane telemetryczne z testów ping i testów wieloetapowych są rozliczone tak samo jak inne dane telemetryczne z aplikacji.

Opcja Application Insights [włączania alertów w niestandardowych wymiarach metryk](./pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) można również generować w dodatkowych kosztach, ponieważ może to spowodować utworzenie dodatkowych metryk przed agregacją. [Dowiedz się więcej](./pre-aggregated-metrics-log-metrics.md) o metrykach opartych na dzienniku i wstępnie zagregowanych informacjach w Application Insights i o [cenach](https://azure.microsoft.com/pricing/details/monitor/) Azure monitor metryk niestandardowych.

### <a name="workspace-based-application-insights"></a>Application Insights oparte na obszarze roboczym

W przypadku zasobów Application Insights, które wysyłają dane do Log Analytics obszaru roboczego, nazywanych [zasobami Application Insights opartymi na obszarze roboczym](create-workspace-resource.md), rozliczanie za pozyskiwanie i przechowywanie danych odbywa się w obszarze roboczym, w którym znajdują się dane Application Insights. Dzięki temu klienci mogą korzystać ze wszystkich opcji [modelu cen](../logs/manage-cost-storage.md#pricing-model) log Analytics, który obejmuje rezerwacje pojemności, a nie tylko płatność zgodnie z rzeczywistym użyciem. Log Analytics również zawiera więcej opcji przechowywania danych, w tym [przechowywanie według typu danych](../logs/manage-cost-storage.md#retention-by-data-type). Application Insights typy danych w obszarze roboczym otrzymują 90 dni przechowywania bez opłat. Użycie testów sieci Web i włączenie alertów w niestandardowych wymiarach metryk jest nadal zgłaszane przez Application Insights. Dowiedz się, jak śledzić koszty pozyskiwania i przechowywania danych w Log Analytics przy użyciu [i szacowanych kosztów](../logs/manage-cost-storage.md#understand-your-usage-and-estimate-costs), [Azure Cost Management + rozliczeń](../logs/manage-cost-storage.md#viewing-log-analytics-usage-on-your-azure-bill) i [log Analytics zapytań](#data-volume-for-workspace-based-application-insights-resources). 

## <a name="estimating-the-costs-to-manage-your-application"></a>Szacowanie kosztów zarządzania aplikacją

Jeśli jeszcze nie korzystasz z Application Insights, możesz użyć [kalkulatora cen Azure monitor](https://azure.microsoft.com/pricing/calculator/?service=monitor) , aby oszacować koszt używania Application Insights. Zacznij od wprowadzenia "Azure Monitor" w polu wyszukiwania i kliknięcia kafelka Azure Monitor wyników. Przewiń w dół stronę do Azure Monitor i wybierz pozycję Application Insights z listy rozwijanej Typ.  W tym miejscu możesz wprowadzić liczbę GB danych, które mają być zbierane miesięcznie, a więc pytanie, ile danych będzie Application Insights zbierać monitorowanie aplikacji.

Istnieją dwa podejścia do rozwiązania tego zagadnienia: użycie domyślnego monitorowania i próbkowania adaptacyjnego, które jest dostępne w zestawie SDK ASP.NET, lub oszacowanie potencjalnego pozyskiwania danych na podstawie tego, co inni klienci będą mogli zobaczyć.

### <a name="data-collection-when-using-sampling"></a>Zbieranie danych przy użyciu próbkowania

Przy [pobieraniu próbkowania](sampling.md#adaptive-sampling)zestawu SDK ASP.NET wolumin danych jest dostosowywany automatycznie, aby zachować w ramach określonego maksymalnego natężenia ruchu dla domyślnego monitorowania Application Insights. Jeśli aplikacja tworzy niską ilość danych telemetrycznych, na przykład podczas debugowania lub z powodu niskiego użycia, elementy nie zostaną porzucone przez procesor próbkowania, o ile ilość poniżej skonfigurowanych zdarzeń na sekundę. W przypadku aplikacji o dużym nasileniu z domyślnym progiem pięciu zdarzeń na sekundę próbkowanie adaptacyjne ograniczy liczbę codziennych zdarzeń do 432 000. Przy użyciu typowego średniego rozmiaru zdarzenia o wartości 1 KB odpowiada to 13,4 GB danych telemetrycznych na każdy węzeł obsługujący aplikację, ponieważ próbkowanie jest wykonywane lokalnie dla każdego węzła.

> [!NOTE]
> Rozmiar danych dziennika Azure Monitor jest obliczany w GB (1 GB = 10 ^ 9 bajtów).

W przypadku zestawów SDK, które nie obsługują próbkowania adaptacyjnego, można zastosować [próbkowanie](./sampling.md#ingestion-sampling)pozyskiwania, które próbkuje, gdy dane są odbierane przez Application Insights na podstawie procentowej ilości danych do zatrzymywania, lub [próbkowania o stałym rozmiarze dla witryn sieci web ASP.NET, ASP.NET Core i Java](sampling.md#fixed-rate-sampling) , aby zmniejszyć ruch wysyłany z serwera i przeglądarek sieci Web.

### <a name="learn-from-what-similar-customers-collect"></a>Dowiedz się, co zbiera podobne odbiorcy

W kalkulatorze cen usługi Azure Monitoring dla Application Insights, jeśli włączysz funkcję "oszacowanie ilości danych opartych na działaniu aplikacji", możesz dostarczyć dane wejściowe dotyczące aplikacji (żądania miesięcznie i wyświetlenia stron miesięcznie), a następnie Kalkulator wskaże medianę i 90% ilość danych zbieranych przez podobne aplikacje. Te aplikacje obejmują zakres konfiguracji Application Insights (np. niektóre z [próbkami](./sampling.md)domyślnymi, niektóre nie mają próbek itp.), dlatego nadal masz kontrolę, aby zmniejszyć ilość danych, które są znacznie mniejsze niż poziom średni przy użyciu próbkowania. Jest to jednak punkt początkowy, który umożliwia zapoznanie się z innymi, podobnym klientom.

## <a name="understand-your-usage-and-estimate-costs"></a>Zapoznaj się z użyciem i szacunkowymi kosztami

Application Insights ułatwia zrozumienie, jakie koszty są prawdopodobnie oparte na najnowszych wzorcach użycia. Aby rozpocząć, w Azure Portal, dla zasobu Application Insights, przejdź do strony **użycie i szacowane koszty** :

![Wybierz Cennik](./media/pricing/pricing-001.png)

A. Przejrzyj ilość danych w danym miesiącu. Obejmuje to wszystkie dane, które zostały odebrane i zachowane (po każdym [próbkowaniu](./sampling.md)) z serwera i aplikacji klienckich oraz z testów dostępności.  
B. Dla [wieloetapowych testów sieci Web](./availability-multistep.md)dokonywana jest oddzielna opłata. (Nie obejmuje to prostych testów dostępności, które są uwzględnione w opłacie ilości danych).  
C. Wyświetlanie trendów ilości danych w ostatnim miesiącu.  
D. Włącz [próbkowanie](./sampling.md)pozyskiwania danych.
E. Ustaw dzienny limit ilości danych.  

(Należy pamiętać, że wszystkie ceny wyświetlane na zrzutach ekranu w tym artykule są tylko na przykład. W przypadku bieżących cen w walucie i regionie zobacz [cennik Application Insights][pricing].)

Aby dokładniej zbadać użycie Application Insights, Otwórz stronę **metryki** , Dodaj metrykę o nazwie "punkt danych", a następnie wybierz opcję *Zastosuj dzielenie* , aby podzielić dane przez "typ elementu telemetrii".

Opłaty za Application Insights są dodawane do rachunku na korzystanie z platformy Azure. Szczegóły rachunku na korzystanie z platformy Azure można znaleźć w sekcji **Cost Management + rozliczenia** Azure Portal lub w [portalu rozliczeń systemu Azure](https://account.windowsazure.com/Subscriptions).  [Poniżej znajdują](#viewing-application-insights-usage-on-your-azure-bill) się szczegółowe informacje dotyczące korzystania z tego programu w celu Application Insights. 

![W menu po lewej stronie wybierz pozycję rozliczenia](./media/pricing/02-billing.png)

### <a name="using-data-volume-metrics"></a>Korzystanie z metryk ilości danych
<a id="understanding-ingested-data-volume"></a>

Aby dowiedzieć się więcej na temat woluminów danych, wybierz **metryki** dla zasobu Application Insights, Dodaj nowy wykres. Dla metryki wykresu w obszarze **metryki oparte na dzienniku** wybierz pozycję **wolumin punktu danych**. Kliknij przycisk **Zastosuj podział** i wybierz pozycję Grupuj według **`Telemetryitem` typu**.

![Używanie metryk do przeglądania ilości danych](./media/pricing/10-billing.png)

### <a name="queries-to-understand-data-volume-details"></a>Zapytania, aby zrozumieć szczegóły ilości danych

Istnieją dwa podejścia do badania woluminów danych dla Application Insights. Pierwsze używa zagregowanych informacji w `systemEvents` tabeli, a druga używa `_BilledSize` właściwości, która jest dostępna dla każdego pozyskanego zdarzenia. `systemEvents` nie będzie zawierać informacji o rozmiarze danych w przypadku [aplikacji opartych na obszarze roboczym](#data-volume-for-workspace-based-application-insights-resources).

#### <a name="using-aggregated-data-volume-information"></a>Korzystanie z zagregowanych informacji o woluminie danych

Na przykład można użyć `systemEvents` tabeli, aby zobaczyć ilość danych pozyskanych w ciągu ostatnich 24 godzin przy użyciu zapytania:

```kusto
systemEvents
| where timestamp >= ago(24h)
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes)
```

Lub aby wyświetlić wykres ilości danych (w bajtach) według typu danych z ostatnich 30 dni, można użyć:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| extend BillingTelemetrySizeInBytes = todouble(measurements["BillingTelemetrySize"])
| summarize sum(BillingTelemetrySizeInBytes) by BillingTelemetryType, bin(timestamp, 1d) | render barchart  
```

Zwróć uwagę, że to zapytanie może być używane w [alercie dziennika platformy Azure](../alerts/alerts-unified-log.md) w celu skonfigurowania alertów dotyczących woluminów danych.  

Aby dowiedzieć się więcej o zmianach danych telemetrycznych, można pobrać liczbę zdarzeń według typu przy użyciu zapytania:

```kusto
systemEvents
| where timestamp >= startofday(ago(30d))
| where type == "Billing"
| extend BillingTelemetryType = tostring(dimensions["BillingTelemetryType"])
| summarize count() by BillingTelemetryType, bin(timestamp, 1d)
| render barchart  
```

#### <a name="using-data-size-per-event-information"></a>Używanie rozmiaru danych na informacje o zdarzeniu

Aby dowiedzieć się więcej na temat źródła woluminów danych, można użyć `_BilledSize` właściwości, która jest obecna w każdym pozyskanym zdarzeniu.

Na przykład, aby sprawdzić, które operacje generują najwięcej ilości danych w ciągu ostatnich 30 dni, można zsumować `_BilledSize` wszystkie zdarzenia zależności:

```kusto
dependencies
| where timestamp >= startofday(ago(30d))
| summarize sum(_BilledSize) by operation_Name
| render barchart  
```

#### <a name="data-volume-for-workspace-based-application-insights-resources"></a>Wolumin danych dla Application Insights zasobów opartych na obszarze roboczym

Aby przyjrzeć się trendom ilości danych dla wszystkich [zasobów Application Insights opartych na obszarze roboczym](create-workspace-resource.md) w obszarze roboczym przez ostatni tydzień, przejdź do obszaru roboczego log Analytics i uruchom zapytanie:

```kusto
union (AppAvailabilityResults),
      (AppBrowserTimings),
      (AppDependencies),
      (AppExceptions),
      (AppEvents),
      (AppMetrics),
      (AppPageViews),
      (AppPerformanceCounters),
      (AppRequests),
      (AppSystemEvents),
      (AppTraces)
| where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now())
| summarize sum(_BilledSize) by _ResourceId, bin(TimeGenerated, 1d)
| render areachart
```

Aby wykonać zapytanie o trendy ilości danych według typu dla określonego zasobu Application Insights opartego na obszarze roboczym, w Log Analytics Użyj obszaru roboczego:

```kusto
union (AppAvailabilityResults),
      (AppBrowserTimings),
      (AppDependencies),
      (AppExceptions),
      (AppEvents),
      (AppMetrics),
      (AppPageViews),
      (AppPerformanceCounters),
      (AppRequests),
      (AppSystemEvents),
      (AppTraces)
| where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now())
| where _ResourceId contains "<myAppInsightsResourceName>"
| summarize sum(_BilledSize) by Type, bin(TimeGenerated, 1d)
| render areachart
```

## <a name="viewing-application-insights-usage-on-your-azure-bill"></a>Wyświetlanie Application Insights użycia na rachunku na platformie Azure

Platforma Azure oferuje bardzo przydatne funkcje w [Azure Cost Management i centrum rozliczeń](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=/azure/billing/TOC.json) . Na przykład funkcja "analiza kosztów" umożliwia wyświetlanie wydatków dotyczących zasobów platformy Azure. Dodawanie filtru według typu zasobu (do Microsoft. Insights/Components for Application Insights) umożliwi śledzenie wydatków. Następnie dla opcji "Grupuj według" Wybierz "kategoria licznika" lub "licznik".  W przypadku zasobów Application Insights w bieżących planach cenowych, większość użycia będzie wyświetlana jako Log Analytics dla kategorii miernika, ponieważ istnieje pojedyncze zaplecze dzienników dla wszystkich składników Azure Monitor. 

Aby lepiej zrozumieć użycie, można [pobrać informacje o użyciu z witryny Azure Portal](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal).
W pobranym arkuszu kalkulacyjnym można zobaczyć użycie na zasób platformy Azure dziennie. W tym arkuszu kalkulacyjnym programu Excel można sprawdzić użycie zasobów Application Insights przez pierwsze filtrowanie w kolumnie "kategoria licznika", aby pokazać "Application Insights" i "Log Analytics", a następnie dodać filtr w kolumnie "identyfikator wystąpienia", która jest "zawiera Microsoft. Insights/Components".  Większość Application Insights użycie jest raportowane na miernikach z kategorią licznika Log Analytics, ponieważ istnieje pojedyncze zaplecze dzienników dla wszystkich składników Azure Monitor.  Tylko zasoby Application Insights ze starszych warstw cenowych i wieloetapowych testów sieci Web są zgłaszane z kategorią miernika Application Insights.  Użycie jest wyświetlane w kolumnie "zużyta ilość", a jednostka dla każdego wpisu jest pokazywana w kolumnie "jednostka miary".  Temat dotyczący [interpretacji rachunku za platformę Microsoft Azure](../../cost-management-billing/understand/review-individual-bill.md) zawiera więcej szczegółowych informacji.

## <a name="managing-your-data-volume"></a>Zarządzanie ilością danych

Ilość wysyłanych danych może być zarządzana przy użyciu następujących technik:

* **Próbkowanie**: możesz użyć próbkowania, aby zmniejszyć ilość danych telemetrycznych wysyłanych z serwera i aplikacji klienckich przy minimalnym zniekształceniu metryk. Próbkowanie to podstawowe narzędzie, za pomocą którego można dostroić ilość wysyłanych danych. Dowiedz się więcej o [funkcjach pobierania próbek](./sampling.md).

* **Ogranicz wywołania AJAX**: można [ograniczyć liczbę wywołań AJAX, które mogą być zgłaszane](./javascript.md#configuration) w każdym widoku strony, lub wyłączyć raportowanie AJAX. Należy pamiętać, że wyłączenie wywołań AJAX spowoduje wyłączenie [korelacji JavaScript](./javascript.md#enable-correlation).

* **Wyłącz niepotrzebne moduły**: [Edytuj ApplicationInsights.config](./configuration-with-applicationinsights-config.md) , aby wyłączyć niepotrzebne moduły kolekcji. Na przykład można zdecydować, że liczniki wydajności lub dane zależności są nieistotne.

* **Metryki przed agregacją**: w przypadku umieszczenia wywołań TrackMetric w aplikacji można zredukować ruch przy użyciu przeciążenia, które akceptuje Obliczanie średniej i odchylenia standardowego partii pomiarów. Lub można użyć [wstępnie agregowanego pakietu](https://www.myget.org/gallery/applicationinsights-sdk-labs).
 
* **Dzienny limit**: podczas tworzenia zasobu Application Insights w Azure Portal dzienny limit jest ustawiany na 100 GB/dzień. Podczas tworzenia zasobu Application Insights w programie Visual Studio wartość domyślna to małe (tylko 32,3 MB/dzień). Domyślna wartość limitu dziennego jest ustawiana na ułatwienia testowania. Przed wdrożeniem aplikacji w środowisku produkcyjnym należy zamierzać, że użytkownik będzie zgłaszał dzienny limit. 

    Maksymalny limit wynosi 1 000 GB/dzień, chyba że zostanie zażądana wyższa wartość maksymalna dla aplikacji o dużym ruchu.
    
    Ostrzegawcze wiadomości e-mail dotyczące dziennego limitu są wysyłane do konta, które są członkami tych ról dla Application Insights zasobu: "serviceadmin", "AccountAdmin", "Administrator", "Owner".

    Podczas ustawiania dziennego limitu należy zachować ostrożność. Zamiarem powinna być *nigdy nie trafiać dziennego limitu*. Po osiągnięciu dziennego limitu utracisz dane z pozostałej części dnia i nie będzie można monitorować aplikacji. Aby zmienić dzienny limit, użyj opcji **dzienny limit ilości** . Możesz uzyskać dostęp do tej opcji w okienku **użycie i szacowane koszty** (jest to opisane w dalszej części artykułu).
    
    Usunięto ograniczenie dla niektórych typów subskrypcji, które mają środki kredytowe, których nie można użyć dla Application Insights. Wcześniej, jeśli subskrypcja ma limit wydatków, w oknie dialogowym dzienne zakończenie znajdują się instrukcje usuwania limitu wydatków i włączania codziennych limitów ponad 32,3 MB/dzień.
    
* **Ograniczanie** przepływności: ograniczanie szybkości danych do 32 000 zdarzeń na sekundę, średnia na 1 minutę na klucz Instrumentacji. Ilość danych wysyłanych przez aplikację jest szacowana co minutę. W przypadku przekroczenia stawki za sekundę obliczonej w ciągu minuty serwer odrzuca niektóre żądania. Zestaw SDK buforuje dane, a następnie próbuje ponownie wysłać ją. Rozłożenie przeskoku przez kilka minut. Jeśli aplikacja stale wysyła dane przy użyciu stawki ograniczenia, niektóre dane zostaną usunięte. (Zestawy SDK ASP.NET, Java i JavaScript próbują ponownie wysłać dane w ten sposób; inne zestawy SDK mogą po prostu porzucić dane ograniczające ograniczenia). W przypadku wystąpienia ograniczenia zostanie wyświetlone ostrzeżenie informujące o tym, że wystąpiło.

## <a name="manage-your-maximum-daily-data-volume"></a>Zarządzanie maksymalnym dziennym woluminem danych

Możesz użyć dziennego limitu ilości, aby ograniczyć zbierane dane. Jeśli jednak zostanie osiągnięty limit, utracisz wszystkie dane telemetryczne wysyłane z aplikacji dla pozostałej części dnia. *Nie zaleca* się, aby aplikacja osiągnęła dzienny limit. Nie można śledzić kondycji i wydajności aplikacji po osiągnięciu dziennego limitu.

Zamiast używać dziennego limitu ilości, użyj [próbkowania](./sampling.md) , aby dostosować ilość danych do żądanego poziomu. Następnie użyj dziennego limitu tylko jako "ostatniej", jeśli aplikacja nieoczekiwanie zacznie wysyłać znacznie wyższe ilości danych telemetrycznych.

### <a name="identify-what-daily-data-limit-to-define"></a>Określ dzienny limit danych do zdefiniowania

Przejrzyj Application Insights użycie i szacowane koszty, aby zrozumieć trend pozyskiwania danych i co to jest dzienny limit ilości do zdefiniowania. Należy wziąć pod uwagę opiekę, ponieważ nie będzie można monitorować zasobów po osiągnięciu limitu.

### <a name="set-the-daily-cap"></a>Ustaw limit dzienny

Aby zmienić dzienny limit, w sekcji **Konfigurowanie** zasobu Application Insights, na stronie **użycie i szacowane koszty** wybierz pozycję  **dzienny limit**.

![Dostosuj dzienny limit ilości danych telemetrycznych](./media/pricing/pricing-003.png)

Aby [zmienić dzienny limit za pośrednictwem Azure Resource Manager](./powershell.md), właściwość do zmiany jest `dailyQuota` .  Za pomocą Azure Resource Manager można również ustawić `dailyQuotaResetTime` i dzienny limit `warningThreshold` .

### <a name="create-alerts-for-the-daily-cap"></a>Tworzenie alertów dla dziennego limitu

Application Insights dzienny tworzy zdarzenie w dzienniku aktywności platformy Azure, gdy pozyskiwane woluminy danych osiągnie poziom ostrzeżeń lub dzienny limit.  Alert można [utworzyć na podstawie tych zdarzeń dziennika aktywności](../alerts/alerts-activity-log.md#create-with-the-azure-portal). Nazwy sygnałów dla tych zdarzeń to:

* Osiągnięto próg ostrzeżenia dziennego limitu Application Insights składnika

* Osiągnięto dzienny limit Application Insights składnika

## <a name="sampling"></a>Próbkowanie
[próbkowanie](./sampling.md) to metoda zmniejszania szybkości, z jaką dane telemetryczne są wysyłane do aplikacji, przy zachowaniu możliwości wyszukiwania pokrewnych zdarzeń podczas wyszukiwania diagnostycznego. Należy również zachować poprawne liczby zdarzeń.

Próbkowanie jest efektywnym sposobem zredukowania opłat i pozostawania w ramach miesięcznego limitu przydziału. Algorytm próbkowania zachowuje powiązane elementy telemetrii, dlatego na przykład podczas korzystania z wyszukiwania można znaleźć żądanie dotyczące konkretnego wyjątku. Algorytm zachowuje również poprawne liczby, aby zobaczyć poprawne wartości w Eksploratorze metryk dla stawek żądań, stawek wyjątków i innych liczników.

Istnieje kilka rodzajów próbkowania.

* [Próbkowanie adaptacyjne](./sampling.md) jest wartością domyślną dla zestawu SDK ASP.NET. Próbkowanie adaptacyjne automatycznie dostosowuje się do ilości danych telemetrycznych wysyłanych przez aplikację. Działa automatycznie w zestawie SDK w aplikacji sieci Web, dzięki czemu ruch telemetrii w sieci jest zmniejszany. 
* *Próbkowanie* do pozyskiwania jest alternatywą, która działa w punkcie, w którym dane telemetryczne z aplikacji stają się usługą Application Insights. Pobieranie próbek nie ma wpływu na ilość danych telemetrycznych wysyłanych z aplikacji, ale zmniejsza wolumin, który jest zachowywany przez usługę. Możesz użyć próbkowania pozyskiwania, aby zmniejszyć przydział, który jest używany przez dane telemetryczne z przeglądarek i innych zestawów SDK.

Aby ustawić próbkowanie pozyskiwania, przejdź do okienka  **Cennik** :

![W okienku przydział i Cennik wybierz kafelek przykłady, a następnie wybierz ułamek próbkowania](./media/pricing/pricing-004.png)

> [!WARNING]
> Okienko **próbkowanie danych** kontroluje tylko wartość próbkowania pozyskiwania. Nie odzwierciedla ona częstotliwości próbkowania, która jest stosowana przez zestaw SDK Application Insights w aplikacji. Jeśli w zestawie SDK zostało już próbkowane przychodzące dane telemetryczne, pobieranie próbek pozyskiwania nie jest stosowane.
>

Aby wykryć rzeczywistą częstotliwość próbkowania bez względu na to, gdzie została zastosowana, użyj [zapytania analizy](../logs/log-query-overview.md). Zapytanie wygląda następująco:

```kusto
requests | where timestamp > ago(1d)
| summarize 100/avg(itemCount) by bin(timestamp, 1h)
| render areachart
```

W każdym zachowanym rekordzie `itemCount` wskazuje liczbę oryginalnych rekordów, które reprezentuje. Jest równa 1 + liczba poprzednich odrzuconych rekordów.

## <a name="change-the-data-retention-period"></a>Change the data retention period (Zmienianie okresu przechowywania danych)

Domyślne przechowywanie dla zasobów Application Insights to 90 dni. Dla każdego zasobu Application Insights można wybrać różne okresy przechowywania. Pełny zestaw dostępnych okresów przechowywania to 30, 60, 90, 120, 180, 270, 365, 550 lub 730. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/monitor/) na temat cen, aby uzyskać dłuższe przechowywanie danych. 

Aby zmienić przechowywanie, z zasobu Application Insights przejdź na stronę **użycie i szacowane koszty** i wybierz opcję **przechowywania danych** :

![Zrzut ekranu pokazujący lokalizację zmiany okresu przechowywania danych.](./media/pricing/pricing-005.png)

Gdy przechowywanie zostanie obniżone, istnieje kilka okresów prolongaty przed usunięciem najstarszych danych.

Przechowywanie można również [ustawić za pomocą programu PowerShell programowo](powershell.md#set-the-data-retention) przy użyciu `retentionInDays` parametru. W przypadku ustawienia przechowywania danych na 30 dni można wyzwolić natychmiastowe przeczyszczanie starszych danych przy użyciu `immediatePurgeDataOn30Days` parametru, co może być przydatne w scenariuszach związanych ze zgodnością. Ta funkcja przeczyszczania jest dostępna tylko za pośrednictwem Azure Resource Manager i powinna być używana z najwyższą starannością. Dzienny czas resetowania ilości danych można skonfigurować przy użyciu Azure Resource Manager, aby ustawić `dailyQuotaResetTime` parametr.

## <a name="data-transfer-charges-using-application-insights"></a>Opłaty za transfer danych przy użyciu Application Insights

Wysyłanie danych do Application Insights może spowodować naliczenie opłat za przepustowość danych. Zgodnie z opisem na [stronie cennika przepustowości platformy Azure](https://azure.microsoft.com/pricing/details/bandwidth/)transfer danych między usługami platformy Azure znajdującymi się w dwóch regionach jest naliczany jako wychodzący transfer danych. Transfer danych przychodzących jest bezpłatny. Ta opłata jest jednak bardzo mała (kilka%) w porównaniu z kosztami Application Insights pozyskiwania danych dziennika. W związku z tym, kontrolując koszty Log Analytics muszą skupić się na pozyskiwanym woluminie danych i mamy wskazówki ułatwiające zrozumienie tego w [tym miejscu](#managing-your-data-volume).

## <a name="limits-summary"></a>Podsumowanie limitów

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Wyłącz dzienne wiadomości e-mail

Aby wyłączyć dzienne wiadomości e-mail z limitem ilości, w sekcji **Konfiguracja** zasobu Application Insights, w okienku **użycie i szacowane koszty** wybierz pozycję  **dzienny limit**. Istnieją ustawienia do wysyłania wiadomości e-mail po osiągnięciu limitu, a także czas osiągnięcia dodanego poziomu ostrzegawczego. Jeśli chcesz wyłączyć wszystkie wiadomości e-mail dotyczące woluminów dziennych, usuń zaznaczenie obu pól.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Warstwa cenowa starszej wersji przedsiębiorstwa (na węzeł)

W przypadku wczesnych użytkowników platformy Azure Application Insights nadal istnieją dwie warstwy cenowe: Basic i Enterprise. Podstawowa warstwa cenowa jest taka sama jak opisana powyżej i jest warstwą domyślną. Obejmuje ona wszystkie funkcje warstwy korporacyjnej bez dodatkowych kosztów. Podstawowa warstwa jest rozliczana głównie na ilość danych, które zostały pozyskane.

Te starsze warstwy cenowe zostały zmienione. Warstwa cenowa przedsiębiorstwa jest teraz wywoływana **dla każdego węzła** , a podstawowa warstwa cenowa jest teraz wywoływana **na GB**. Te nowe nazwy są używane poniżej i w Azure Portal.  

Warstwa na węzeł (dawniej Enterprise) ma opłatą za węzeł, a każdy węzeł otrzymuje dzienny limit danych. W warstwie cenowej na węzeł opłaty są naliczane za dane pozyskane powyżej uwzględnionego przydziału. Jeśli używasz pakietu Operations Management Suite, wybierz warstwę na węzeł. W kwietniu [2018 wprowadziliśmy nowy](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) model cen dla monitorowania platformy Azure. Ten model przyjmuje prosty model "płatność zgodnie z rzeczywistym użyciem" w całym portfolio usług monitorowania. Dowiedz się więcej o [nowym modelu cen](..//usage-estimated-costs.md).

W przypadku bieżących cen w walucie i regionie zapoznaj się z tematem [Application Insights cenniku](https://azure.microsoft.com/pricing/details/application-insights/).

### <a name="understanding-billed-usage-on-the-legacy-enterprise-per-node-tier"></a>Informacje o rozliczeniu użycia w starszej warstwie przedsiębiorstwa (na węzeł) 

Zgodnie z bardziej szczegółowymi informacjami, Starsza warstwa przedsiębiorstwa (na węzeł) łączy użycie spośród wszystkich Application Insights zasobów w ramach subskrypcji, aby obliczyć liczbę węzłów i nadwyżkowe dane. Ze względu na ten proces złożony, **użycie wszystkich zasobów Application Insights w subskrypcji jest raportowane tylko dla jednego z zasobów**.  Pozwala to na uzgadnianie [rozliczanego użycia](#viewing-application-insights-usage-on-your-azure-bill) z użyciem obserwowania dla każdego Application Insights zasobów bardzo skomplikowane. 

> [!WARNING]
> Ze względu na złożoność śledzenia i interpretacji użycia zasobów Application Insights w starszej warstwie przedsiębiorstwa (na węzeł) zdecydowanie zalecamy korzystanie z bieżącej warstwy cenowej płatność zgodnie z rzeczywistym użyciem. 

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>Uprawnienia do subskrypcji dla warstwy węzła i pakietu Operations Management Suite

Klienci, którzy kupują Pakiet Operations Management Suite E1 i E2, mogą uzyskać Application Insights na węzeł jako dodatkowy składnik bez dodatkowych kosztów zgodnie z [dotychczas ogłoszonymi](/archive/blogs/msoms/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription). Każda jednostka pakietu Operations Management Suite E1 i E2 obejmuje uprawnienie do jednego węzła Application Insights na warstwę węzła. Każdy węzeł Application Insights obejmuje nawet 200 MB danych pobieranych dziennie (oddzielenie od Log Analytics pozyskiwania danych), z 90m przechowywaniem danych bez dodatkowych kosztów. Warstwa została opisana bardziej szczegółowo w dalszej części artykułu.

Ponieważ ta warstwa ma zastosowanie tylko do klientów z subskrypcją pakietu Operations Management Suite, klienci, którzy nie mają subskrypcji pakietu Operations Management Suite, nie widzą opcji wyboru tej warstwy.

> [!NOTE]
> Aby upewnić się, że te uprawnienia uzyskasz, Application Insights zasoby muszą znajdować się w warstwie cenowej węzła. To uprawnienie ma zastosowanie tylko do węzłów. Application Insights zasobów w warstwie na GB nie ma żadnej korzyści. To uprawnienie nie jest widoczne w szacowanych kosztach widocznych w okienku **użycie i szacowany koszt** . Ponadto Jeśli przeniesiesz subskrypcję do nowego modelu cen monitorowania platformy Azure w kwietniu 2018, dostępna jest tylko warstwa za GB. Nie zaleca się przeniesienia subskrypcji do nowego modelu cen monitorowania platformy Azure, jeśli masz subskrypcję pakietu Operations Management Suite.

### <a name="how-the-per-node-tier-works"></a>Jak działa warstwa na węzeł

* Płacisz za każdy węzeł, który wysyła dane telemetryczne dla wszystkich aplikacji w warstwie na węzeł.
  * *Węzeł* jest maszyną fizyczną lub wirtualną lub wystąpieniem roli "platforma jako usługa", które hostuje aplikację.
  * Komputery deweloperskie, przeglądarki klienta i urządzenia przenośne nie są liczone jako węzły.
  * Jeśli aplikacja zawiera kilka składników, które wysyłają dane telemetryczne, takie jak usługa sieci Web i proces roboczy zaplecza, składniki są zliczane osobno.
  * Dane [Live Metrics Stream](./live-stream.md) nie są uwzględniane w celach cenowych. Opłaty są naliczane za każdy węzeł, a nie za aplikację. Jeśli masz pięć węzłów, które wysyłają dane telemetryczne dla 12 aplikacji, opłata dotyczy pięciu węzłów.
* Mimo że opłaty są naliczane miesięcznie, opłata jest naliczana tylko za godzinę, w której węzeł wysyła dane telemetryczne z aplikacji. Opłata godzinowa to płatna opłata miesięczna podzielona przez 744 (liczba godzin w ciągu 31 dni).
* Przydział ilości danych 200 MB dziennie jest podawany dla każdego wykrytego węzła (z dokładnością co godzinę). Nieużywane alokacje danych nie są przenoszone z jednego dnia do następnego.
  * W przypadku wybrania warstwy cenowej na węzeł Każda subskrypcja pobiera dzienny limit danych na podstawie liczby węzłów, które wysyłają dane telemetryczne do Application Insights zasobów w ramach tej subskrypcji. Dlatego jeśli masz pięć węzłów, które wysyłają dane codziennie, będziesz mieć przynależność do puli o pojemności 1 GB do wszystkich zasobów Application Insights w tej subskrypcji. Nie ma znaczenia, czy niektóre węzły wysyłają więcej danych niż inne węzły, ponieważ zawarte dane są współużytkowane przez wszystkie węzły. Jeśli w danym dniu Application Insights zasoby otrzymają więcej danych niż jest uwzględniona w dziennej alokacji danych dla tej subskrypcji, zostaną naliczone opłaty za użycie nadwyżkowe za GB danych. 
  * Dzienny limit danych jest obliczany jako liczba godzin w ciągu dnia (przy użyciu czasu UTC), przez który każdy węzeł wysyła dane telemetryczne podzielone przez 24 pomnożone przez 200 MB. Tak więc, jeśli masz cztery węzły, które wysyłają dane telemetryczne przez 15 godzin w ciągu dnia, to w przypadku tego dnia uwzględniane są ((4 &#215; 15)/24) &#215; 200 MB = 500 MB. W cenie 2,30 USD za GB za nadwyżkę danych opłata wynosi 1,15 USD, jeśli węzły wysyłają 1 GB danych dziennie.
  * Dzienna dieta dla warstwy węzła nie jest współdzielona z aplikacjami, dla których wybrano warstwę za GB. Nieużywany dodatek nie jest przenoszony od bieżącego dnia.

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Przykłady sposobu określania odrębnej liczby węzłów

| Scenariusz                               | Łączna liczba węzłów dziennych |
|:---------------------------------------|:----------------:|
| 1 aplikacja używająca 3 wystąpień Azure App Service i 1 serwera wirtualnego | 4 |
| 3 aplikacje działające na 2 maszynach wirtualnych; zasoby Application Insights dla tych aplikacji znajdują się w tej samej subskrypcji i w warstwie na węzeł | 2 | 
| 4 aplikacje, których zasoby usługi Application Insights znajdują się w tej samej subskrypcji; Każda aplikacja uruchamia 2 wystąpienia w godzinach od 16 godzin szczytu i 4 wystąpienia w ciągu 8 godzin szczytu | 13,33 |
| Usługi w chmurze z 1 rolą proces roboczy i 1 rolą sieci Web, każdy uruchomiony 2 wystąpienia | 4 | 
| Klaster programu Azure Service Fabric w systemie 5 z systemem 50 mikrousług; Każda mikrousługa z uruchomioną 3 wystąpieniami | 5|

* Precyzyjne zliczanie węzłów zależy od tego, który Application Insights używany przez aplikację. 
  * W zestawie SDK w wersji 2,2 i nowszych zarówno [zestaw Application Insights Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) i [zestaw SDK sieci Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) są raportowane przez każdego hosta aplikacji jako węzeł. Przykłady to nazwa komputera dla serwerów fizycznych i hostów maszyn wirtualnych lub nazwa wystąpienia usług Cloud Services.  Jedyny wyjątek to aplikacja, która korzysta tylko z [platformy .NET Core](https://dotnet.github.io/) i zestawu SDK Application Insights Core. W takim przypadku tylko jeden węzeł jest raportowany dla wszystkich hostów, ponieważ nazwa hosta jest niedostępna.
  * W przypadku wcześniejszych wersji zestawu SDK zestaw SDK [sieci Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) działa jak nowsze wersje zestawu SDK, ale [podstawowy zestaw SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) raportuje tylko jeden węzeł, niezależnie od liczby hostów aplikacji.
  * Jeśli aplikacja używa zestawu SDK do ustawiania **roleInstance** na wartość niestandardową, domyślnie ta sama wartość jest używana do określenia liczby węzłów.
  * W przypadku korzystania z nowej wersji zestawu SDK z aplikacją uruchomioną z poziomu komputerów klienckich lub urządzeń przenośnych liczba węzłów może zwracać liczbę, która jest duża (z powodu dużej liczby komputerów klienckich lub urządzeń przenośnych).

## <a name="automation"></a>Automation

Można napisać skrypt do ustawienia warstwy cenowej za pomocą usługi Azure Resource Management. [Dowiedz się, jak](powershell.md#price).

## <a name="next-steps"></a>Następne kroki

* [sond](./sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ./app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/