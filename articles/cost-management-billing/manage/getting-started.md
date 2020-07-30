---
title: Zapobieganie nieoczekiwanym opłatom i analizowanie ich za pomocą strony Zarządzanie kosztami i rozliczenia platformy Azure
description: Dowiedz się, jak uniknąć nieoczekiwanych opłat na rachunku platformy Azure oraz korzystać z funkcji śledzenia kosztów i zarządzania nimi dla konta platformy Azure.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.topic: how-to
ms.date: 07/24/2020
ms.author: banders
ms.openlocfilehash: 1b63b0316b8314e4c5107ddd3f1d22c1d59cbb08
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87281807"
---
# <a name="prevent-unexpected-charges-with-azure-billing-and-cost-management"></a>Zapobieganie nieoczekiwanym opłatom w rozliczeniach platformy Azure i zarządzanie kosztami

Kiedy rejestrujesz się na platformie Azure, możesz zrobić kilka rzeczy, aby lepiej zrozumieć swoje wydatki:

- Poznaj szacowane koszty przed dodaniem usług przy użyciu [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/), arkusza cen platformy Azure lub podczas dodawania usług w witrynie Azure Portal.
- Monitoruj koszty za pomocą [budżetów](../costs/tutorial-acm-create-budgets.md), [alertów](../costs/cost-mgt-alerts-monitor-usage-spending.md) i [analizy kosztów](../costs/quick-acm-cost-analysis.md).
- Sprawdź opłaty na fakturze, porównując je z [plikami szczegółowego użycia](download-azure-invoice-daily-usage-date.md).
- Integruj dane dotyczące rozliczeń i kosztów z własnym systemem raportowania przy użyciu interfejsów API [rozliczeń](https://docs.microsoft.com/rest/api/billing/) i [użycia](https://docs.microsoft.com/rest/api/consumption/).
- Korzystaj z dodatkowych zasobów i narzędzi dla klientów z umową Enterprise (EA), należących do programu Cloud Solution Provider (CSP) i korzystających z Dostępu sponsorowanego Azure.
- [Przez 12 miesięcy korzystaj bezpłatnie z niektórych najpopularniejszych usług platformy Azure](create-free-services.md) dostępnych na [bezpłatnym koncie platformy Azure](https://azure.microsoft.com/free/). Oprócz zaleceń wymienionych poniżej, zobacz [Unikanie naliczania opłat za bezpłatne konto](avoid-charges-free-account.md).

Jeśli musisz anulować subskrypcję platformy Azure, zobacz [Anulowanie subskrypcji platformy Azure](cancel-azure-subscription.md).

## <a name="get-estimated-costs-before-adding-azure-services"></a>Uzyskiwanie szacowanych kosztów przed dodaniem usług platformy Azure

Użyj jednego z następujących narzędzi, aby oszacować koszt korzystania z usługi platformy Azure:
- Kalkulator cen platformy Azure
- Arkusz cen platformy Azure
- Azure Portal

Obrazy w poniższych sekcjach pokazują przykładowe ceny w dolarach amerykańskich.

### <a name="estimate-cost-online-using-the-pricing-calculator"></a>Szacowanie kosztów online za pomocą kalkulatora cen

Skorzystaj z [kalkulatora cen](https://azure.microsoft.com/pricing/calculator/), aby uzyskać szacowany miesięczny koszt usługi, którą chcesz dodać. Możesz zmienić walutę, aby uzyskać oszacowanie w walucie lokalnej.

![Zrzut ekranu przedstawiający menu kalkulatora cen](./media/getting-started/pricing-calc.png)

Szacowany koszt możesz sprawdzić dla każdej usługi platformy Azure oferowanej przez firmę Microsoft. Przykładowo na poniższym zrzucie ekranu widać, że szacowany koszt maszyny wirtualnej A1 z systemem Windows to 66,96 USD miesięcznie w godzinach obliczeniowych, jeśli będzie działać przez cały czas:

![Zrzut ekranu kalkulatora cen z szacowanym miesięcznym kosztem maszyny wirtualnej A1 z systemem Windows](./media/getting-started/pricing-calcvm.png)

Aby uzyskać więcej informacji na temat cen, zobacz [Cennik — często zadawane pytania](https://azure.microsoft.com/pricing/faq/). Jeśli chcesz skontaktować się ze sprzedawcą platformy Azure, zadzwoń na numer telefonu widoczny w górnej części strony często zadawanych pytań.

### <a name="view-and-download-azure-price-sheet"></a>Wyświetlanie i pobieranie arkusza cen platformy Azure

Jeśli masz dostęp do platformy Azure za pomocą umowy Enterprise (EA) lub umowy klienta firmy Microsoft (MCA), możesz wyświetlić i pobrać arkusz cen dla swojego konta platformy Azure. Arkusz cen to plik programu Excel, który zawiera ceny dla wszystkich usług platformy Azure. Aby uzyskać więcej informacji, zobacz [Wyświetlanie i pobieranie cennika platformy Azure](ea-pricing.md).

### <a name="review-estimated-costs-in-the-azure-portal"></a>Przeglądanie szacowanych kosztów w witrynie Azure Portal

Szacowany koszt miesięczny możesz wyświetlić podczas dodawania usługi w witrynie Azure Portal. Na przykład po wybraniu rozmiaru maszyny wirtualnej z systemem Windows zobaczysz szacowany miesięczny koszt dla godzin obliczeniowych:

![Przykład: maszyna wirtualna A1 z systemem Windows z widocznym szacowanym kosztem miesięcznym](./media/getting-started/vm-size-cost.png)

## <a name="monitor-costs-when-using-azure-services"></a>Monitorowanie kosztów podczas korzystania z usług platformy Azure
Koszty można monitorować przy użyciu następujących narzędzi:

- Ustawianie alertów dotyczących budżetu i kosztów
- Analiza kosztów

### <a name="track-costs-with-budgets-and-cost-alerts"></a>Śledzenie kosztów za pomocą alertów dotyczących budżetów i kosztów

W celu zarządzania kosztami można utworzyć [budżety](../costs/tutorial-acm-create-budgets.md) oraz [alerty](../costs/cost-mgt-alerts-monitor-usage-spending.md), które automatycznie powiadamiają Ciebie oraz Twoich interesariuszy o anomaliach w wydatkach i nadmiernych wydatkach.

### <a name="explore-and-analyze-costs-with-cost-analysis"></a><a name="costs"></a> Poznawanie i analizowanie kosztów za pomocą analizy kosztów

Po uruchomieniu usług platformy Azure regularnie sprawdzaj koszty, aby śledzić wydatki na platformę Azure. Analizy kosztów możesz używać, aby zrozumieć, skąd biorą się koszty związane z użyciem platformy Azure.

1. Przejdź na stronę [Zarządzanie kosztami i rozliczenia w witrynie Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Billing/ModernBillingMenuBlade).

2. Kliknij pozycję **Analiza kosztów** po lewej stronie ekranu, aby zobaczyć bieżące koszty podzielone według różnych kryteriów, takich jak usługa, lokalizacja i subskrypcja. Po dodaniu usługi lub dokonaniu zakupu poczekaj 24 godziny na wyświetlenie danych. Domyślnie analiza kosztów pokazuje koszt dla zakresu, w którym się znajdujesz. Na przykład na poniższym zrzucie ekranu jest wyświetlany koszt dla konta rozliczeniowego Contoso. Użyj kapsułki Zakres, aby przełączyć się na inny zakres w analizie kosztów. Aby uzyskać więcej informacji na temat zakresów, zobacz [Omówienie zakresów i praca z nimi](../costs/understand-work-scopes.md#scopes)

    ![Zrzut ekranu przedstawiający widok analizy kosztów w witrynie Azure Portal](./media/getting-started/cost-analysis.png)

4. Filtrować można według różnych właściwości, takich jak tagi, typ zasobu i przedział czasu. Kliknij pozycję **Dodaj filtr**, aby dodać filtr dla właściwości i wybrać wartości do odfiltrowania. Wybierz pozycję **Eksportuj**, aby wyeksportować widok do pliku z wartościami rozdzielanymi przecinkami (csv).

5. Ponadto możesz klikać etykiety wykresu, aby zobaczyć dzienną historię wydatków dla konkretnych etykiet. Na przykład: Na poniższym zrzucie ekranu klikanie maszyn wirtualnych powoduje wyświetlenie dziennego kosztu działania tych maszyn.

    ![Zrzut ekranu przedstawiający widok historii wydatków w witrynie Azure Portal](./media/getting-started/costhistory.png)

## <a name="optimize-and-reduce-costs"></a>Optymalizowanie i zmniejszanie kosztów
Jeśli nie znasz zasad zarządzania kosztami, przeczytaj temat [Jak zoptymalizować inwestycję w chmurę za pomocą usługi Azure Cost Management](../costs/cost-mgt-best-practices.md).

W witrynie Azure Portal możesz także zoptymalizować i zmniejszyć koszty związane z platformą Azure za pomocą automatycznego zamykania maszyn wirtualnych i rekomendacji usługi Advisor.

### <a name="consider-cost-cutting-features-like-auto-shutdown-for-vms"></a>Rozważanie funkcji obcinania kosztów, takich jak automatyczne zamykanie maszyn wirtualnych

W zależności od scenariusza można skonfigurować automatyczne zamykanie maszyn wirtualnych w witrynie Azure Portal. Aby uzyskać więcej informacji, zobacz [Automatyczne zamykanie maszyn wirtualnych przy użyciu usługi Azure Resource Manager](https://azure.microsoft.com/blog/announcing-auto-shutdown-for-vms-using-azure-resource-manager/).

![Zrzut ekranu przedstawiający opcję automatycznego zamykania w portalu](./media/getting-started/auto-shutdown.png)

Automatyczne zamykanie to nie to samo, co wyłączanie maszyny wirtualnej przy użyciu opcji zasilania. Automatyczne zamykanie powoduje zatrzymanie i cofnięcie przydziału maszyn wirtualnych w celu uniknięcia dodatkowych opłat za użycie. Aby uzyskać więcej informacji, zobacz często zadawane pytania dotyczące cen dla [maszyn wirtualnych z systemem Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) i [maszyn wirtualnych z systemem Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) o stanach maszyn wirtualnych.

Aby poznać więcej funkcji obcinania kosztów dla środowisk deweloperskich i testowych, zobacz usługę [Azure DevTest Labs](https://azure.microsoft.com/services/devtest-lab/).

### <a name="turn-on-and-review-azure-advisor-recommendations"></a>Włączanie i przeglądanie rekomendacji usługi Azure Advisor

Usługa [Azure Advisor](../../advisor/advisor-overview.md) pomaga obniżyć koszty, identyfikując zasoby o niskim użyciu. Wyszukaj usługę **Advisor** w witrynie Azure Portal:

![Zrzut ekranu przedstawiający przycisk Azure Advisor w witrynie Azure Portal](./media/getting-started/advisor-button.png)

Wybierz pozycję **Koszty** po lewej stronie. Zobaczysz praktyczne zalecenia na karcie **Koszty**:

![Zrzut ekranu przedstawiający przykład rekomendacji dotyczącej kosztów usługi Advisor](./media/getting-started/advisor-action.png)

Zapoznaj się z samouczkiem [Optymalizowanie kosztów na podstawie rekomendacji](../costs/tutorial-acm-opt-recommendations.md), aby dowiedzieć się, jak korzystać z rekomendacji dotyczących oszczędzania kosztów usługi Advisor.


## <a name="integrate-with-billing-and-consumption-apis"></a>Integrowanie z interfejsami API rozliczeń i użycia

Aby programowo uzyskać dane dotyczące rozliczeń i kosztów, użyj interfejsów API [rozliczeń](https://docs.microsoft.com/rest/api/billing/) i [użycia](https://docs.microsoft.com/rest/api/consumption/) na platformie Azure. Aby pobrać rozliczane użycie, użyj interfejsu API usługi RateCard wraz z interfejsem API użycia. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie szczegółowych informacji o użyciu zasobów platformy Microsoft Azure](usage-rate-card-overview.md).

## <a name="additional-resources-and-special-cases"></a><a name="other-offers"></a> Dodatkowe zasoby i przypadki specjalne

### <a name="ea-csp-and-sponsorship-customers"></a>Klienci z umową Enterprise Agreement, umową od dostawcy CSP i dostępem sponsorowanym
Skontaktuj się ze swoim menedżerem konta lub partnerem platformy Azure, aby rozpocząć.

| Oferta | Zasoby |
|-------------------------------|-----------------------------------------------------------------------------------|
| Umowa Enterprise Agreement (EA) | [Witryna EA Portal](https://ea.azure.com/), [dokumenty pomocy](https://ea.azure.com/helpdocs) i [raport usługi Power BI](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-enterprise/) |
| Dostawca rozwiązań w chmurze (CSP) | Porozmawiaj ze swoim dostawcą |
| Dostęp sponsorowany Azure | [Portal dostępu sponsorowanego](https://www.microsoftazuresponsorships.com/) |

Jeśli zarządzasz działem IT w dużej organizacji, zalecamy przeczytanie dokumentu [Szkielet platformy Azure dla przedsiębiorstw](/azure/architecture/cloud-adoption-guide/subscription-governance) i [oficjalnego dokumentu dla działu IT przedsiębiorstw](https://download.microsoft.com/download/F/F/F/FFF60E6C-DBA1-4214-BEFD-3130C340B138/Azure_Onboarding_Guide_for_IT_Organizations_EN_US.pdf) (plik PDF do pobrania, tylko w języku angielskim).

### <a name="enterprise-agreement-cost-views-in-the-azure-portal"></a><a name="EA"></a> Widoki kosztów dla umowy Enterprise Agreement w witrynie Azure Portal

Widoki kosztów dla przedsiębiorstw są obecnie w publicznej wersji zapoznawczej. Uwagi:

- Koszty subskrypcji są oparte na użyciu i nie obejmują kwot przedpłat, nadwyżek, uwzględnionych ilości, korekt i podatków. Rzeczywiste opłaty są obliczane na poziomie rejestracji.
- Kwoty podane w witrynie Azure Portal mogą różnić się od tego, co jest widoczne w witrynie Enterprise Portal. Aktualizacje wprowadzone w witrynie Enterprise Portal mogą być widoczne w witrynie Azure Portal po kilku minutach.
- Jeśli nie widzisz kosztów, może to być spowodowane jedną z następujących przyczyn:
    - Nie masz uprawnień na poziomie subskrypcji. Aby wyświetlić widoki kosztów przedsiębiorstwa, musisz być czytelnikiem rozliczeń, czytelnikiem, współautorem lub właścicielem na poziomie subskrypcji.
    - Jesteś właścicielem konta, ale administrator rejestracji wyłączył ustawienie „wyświetlania opłat przez właściciela konta”.  Skontaktuj się z administratorem rejestracji, aby uzyskać dostęp do kosztów.
    - Jesteś administratorem działu, ale administrator rejestracji wyłączył ustawienie **wyświetlania opłat przez administratora działu**.  Skontaktuj się z administratorem rejestracji, aby uzyskać dostęp.
    - Platformę Azure zakupiono za pośrednictwem partnera handlowego, który nie opublikował informacji o cenniku.  
- Po zaktualizowaniu ustawień związanych z dostępem do kosztów w witrynie Enterprise Portal zmiany są widoczne w witrynie Azure Portal z kilkuminutowym opóźnieniem.
- Limit wydatków i wskazówki dotyczące faktur nie dotyczą subskrypcji umowy EA.

### <a name="check-your-subscription-and-access"></a>Sprawdzanie subskrypcji i dostępu

Aby wyświetlić koszty, musisz mieć dostęp do informacji o kosztach lub rozliczeniach na poziomie konta lub subskrypcji. Dostęp jest różny w zależności od typu konta rozliczeniowego. Aby dowiedzieć się więcej na temat kont rozliczeniowych i sprawdzić typ takiego konta, zobacz [Wyświetlanie kont rozliczeniowych w witrynie Azure Portal](view-all-accounts.md).

Jeśli masz dostęp do platformy Azure za pomocą konta rozliczeniowego w programie Microsoft Online Service Program (MOSP), zobacz [Zarządzanie dostępem do informacji rozliczeniowych dla platformy Azure](manage-billing-access.md).

Jeśli masz dostęp do platformy Azure za pomocą konta rozliczeniowego umowy Enterprise Agreement (EA), zobacz [Omówienie ról administracyjnych dla umowy Azure Enterprise Agreement na platformie Azure](understand-ea-roles.md).

Jeśli masz dostęp do platformy Azure za pomocą konta rozliczeniowego umowy klienta firmy Microsoft (MCA), zobacz [Omówienie ról administracyjnych dla umowy klienta firmy Microsoft na platformie Azure](understand-mca-roles.md).

### <a name="request-a-service-level-agreement-credit-for-a-service-incident"></a>Żądanie środków umowy dotyczącej poziomu usług dla zdarzenia związanego z usługą

W umowie dotyczącej poziomu usług (SLA) opisano zobowiązania firmy Microsoft dotyczące czasu dostępności i połączeń. Zdarzenie związane z usługą jest zgłaszane, gdy usługi platformy Azure napotykają problem, który ma wpływ na dostępność i połączenia, co często jest określane mianem *awarii*. Jeśli nie uzyskamy i nie utrzymamy poziomów usług dla każdej usługi zgodnie z opisem w umowie SLA, możesz kwalifikować się do otrzymania środków na część miesięcznych opłat za korzystanie z usług.

Aby zażądać środków:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/). Jeśli masz wiele kont, upewnij się, że korzystasz z tego, na które miał wpływ przestój na platformie Azure.
2. Utwórz nowy wniosek o pomoc techniczną.
3. W kategorii **Typ problemu** wybierz pozycję **Rozliczenia**.
4. W obszarze **Typ problemu** wybierz pozycję **Żądanie zwrotu pieniędzy**.
5. Dodaj szczegółowe informacje, aby określić, że prosisz o środki z umowy SLA, podaj datę/godzinę/strefę czasową, a także usługi, których dotyczy problem (maszyny wirtualne, witryny internetowe itp.).
6. Sprawdź informacje kontaktowe i wybierz pozycję **Utwórz**, aby przesłać żądanie.

Progi umowy SLA różnią się w zależności od usługi. Na przykład warstwa Internetowa programu SQL jest objęta umową SLA na poziomie 99,9%, maszyny wirtualne mają umowę SLA na poziomie 99,95%, a w przypadku warstwy Standardowa programu SQL jest dostępna umowa SLA na poziomie 99,99%.

W przypadku niektórych usług obowiązują wymagania wstępne dotyczące umowy SLA. Na przykład maszyny wirtualne muszą mieć wdrożone co najmniej dwa wystąpienia w tym samym zestawie dostępności.

Aby uzyskać więcej informacji, zobacz [Umowy dotyczące poziomu usług](https://azure.microsoft.com/support/legal/sla/) oraz dokumentację [podsumowania umów SLA dla usług platformy Azure](https://azure.microsoft.com/support/legal/sla/summary/).

## <a name="analyze-unexpected-charges"></a>Analizowanie nieoczekiwanych opłat

Infrastruktura zasobów w chmurze utworzona dla organizacji jest prawdopodobnie złożona. Na platformie Azure danego typu zasobu mogą dotyczyć różne typy opłat. Zasoby platformy Azure mogą należeć do różnych zespołów w organizacji i mogą być objęte różnymi typami modeli rozliczeń, które stosują się do różnych zasobów. Aby lepiej zrozumieć opłaty, rozpocznij ich analizowanie przy użyciu jednej lub kilku strategii opisanych w poniższych sekcjach.

### <a name="review-your-invoice-and-identify-the-resource-that-is-responsible-for-the-charge"></a>Przeglądanie faktury i określanie zasobu odpowiedzialnego za opłatę

Sposób kupowania usług platformy Azure pomaga określić metodologię i narzędzia dostępne na potrzeby identyfikowania zasobu skojarzonego z opłatą. Aby określić metodologię, która dotyczy Ciebie, najpierw [określ typ oferty platformy Azure](../costs/understand-cost-mgt-data.md#determine-your-offer-type). Następnie określ swoją kategorię klienta na liście [obsługiwanych ofert platformy Azure](../costs/understand-cost-mgt-data.md#supported-microsoft-azure-offers).

Poniższe artykuły zawierają szczegółowe kroki przeglądania rachunku na podstawie typu klienta. W każdym artykule znajdują się instrukcje dotyczące pobierania pliku CSV zawierającego dane użycia i szczegóły kosztów dla danego okresu rozliczeniowego.

- [Proces przeglądania rachunku dla płatności zgodnie z rzeczywistym użyciem](../understand/review-individual-bill.md#compare-invoiced-charges-with-usage-file)
- [Proces przeglądania rachunku dla umowy Enterprise Agreement](../understand/review-enterprise-agreement-bill.md)
- [Proces przeglądania dla Umowy z Klientem Microsoft](../understand/review-customer-agreement-bill.md#analyze-your-azure-usage-charges)
- [Proces przeglądania dla umowy Microsoft Partner Agreement](../understand/review-partner-agreement-bill.md#analyze-your-azure-usage-charges)

Rachunek platformy Azure agreguje miesięczne opłaty dla _poszczególnych mierników_. Mierniki są używane do śledzenia użycia zasobu w czasie i obliczenia rachunku. Podczas tworzenia pojedynczego zasobu platformy Azure, takiego jak maszyna wirtualna, zostanie dla niego utworzone co najmniej jedno wystąpienie miernika.

Przefiltruj plik CSV użycia na podstawie wartości _MeterName_ znajdującej się na rachunku do analizy, aby zobaczyć wszystkie pozycje dotyczące miernika. Wartość _InstanceID_ dla pozycji odpowiada rzeczywistemu zasobowi platformy Azure, który wygenerował opłatę.

Po zidentyfikowaniu zasobu można użyć funkcji analizy kosztów w usłudze Azure Cost Management, aby dalej analizować koszty związane z zasobem. Aby dowiedzieć się więcej o korzystaniu z analizy kosztów, zobacz [Rozpoczęcie analizowania kosztów](../costs/quick-acm-cost-analysis.md).

### <a name="review-invoiced-charges-in-cost-analysis"></a>Przeglądanie zafakturowanych kosztów w analizie kosztów

Aby wyświetlić szczegóły faktury w witrynie Azure Portal, przejdź do analizy kosztów dla zakresu skojarzonego z analizowaną fakturą. Wybierz widok **Szczegóły faktury**. Szczegóły faktury przedstawiają opłaty, które są widoczne na fakturze.

[![Przykład pokazujący szczegóły faktury](./media/getting-started/invoice-details.png)](./media/getting-started/invoice-details.png#lightbox)

Przeglądając szczegóły faktury, możesz zidentyfikować usługę z nieoczekiwanymi kosztami i określić, które zasoby są bezpośrednio skojarzone z zasobem w analizie kosztów. Jeśli na przykład chcesz przeanalizować opłaty za usługę Virtual Machines, przejdź do widoku **Skumulowany koszt**. Następnie ustaw stopień szczegółowości na **Codziennie**, ustaw filtr opłat **Nazwa usługi: Virtual Machines** i pogrupuj opłaty według wartości **Zasób**.

[![Przykład przedstawiający skumulowane koszty maszyn wirtualnych](./media/getting-started/virtual-machines.png)](./media/getting-started/virtual-machines.png#lightbox)


### <a name="identify-spikes-in-cost-over-time"></a>Identyfikowanie skoków kosztów w czasie

Czasem możesz nie wiedzieć, jakie ostatnio poniesione koszty spowodowały zmiany w opłatach na rachunku. Aby poznać zmiany, możesz użyć analizy kosztów do [wyświetlenia dziennego lub miesięcznego podziału kosztów w czasie](../costs/cost-analysis-common-uses.md#view-costs-per-day-or-by-month). Po utworzeniu widoku pogrupuj opłaty według **usługi** lub **zasobu**, aby zidentyfikować zmiany. Możesz także zmienić widok na wykres **liniowy**, aby lepiej zwizualizować dane.

![Przykład przedstawiający koszty w ramach analizy kosztów](./media/getting-started/costs-over-time.png)

### <a name="determine-resource-pricing-and-understand-its-billing-model"></a>Określanie ceny zasobu i interpretacja jego modelu rozliczeń

Pojedynczy zasób może spowodować naliczenie opłat w ramach wielu produktów i usług platformy Azure. Aby dowiedzieć się więcej o cenach dla każdej usługi platformy Azure, zobacz [cennik platformy Azure według produktu](https://azure.microsoft.com/pricing/#product-pricing). Na przykład pojedyncza maszyna wirtualna utworzona na platformie Azure może mieć utworzone następujące mierniki do śledzenia jej użycia. Każdego może dotyczyć inna cena.

- Godziny obliczeniowe
- Godziny korzystania z adresu IP
- Transfer danych przychodzących
- Transfer danych wychodzących
- Dysk zarządzany w warstwie Standardowa
- Operacje na dyskach zarządzanych w warstwie Standardowa
- Standardowe we/wy — dysk
- Standardowe we/wy — odczyt dla blokowych obiektów blob
- Standardowe we/wy — zapis dla blokowych obiektów blob
- Standardowe we/wy — usuwanie dla blokowych obiektów blob

Po utworzeniu maszyny wirtualnej każdy miernik rozpoczyna emitowanie rekordów użycia. Użycie i cena miernika są śledzone w systemie pomiarów platformy Azure. Liczniki, które zostały użyte do obliczenia rachunku, są pokazywane w pliku CSV użycia.

### <a name="find-the-people-responsible-for-the-resource-and-engage-them"></a>Znajdowanie osób odpowiedzialnych za zasób i kontaktowanie się z nimi

Często zespół odpowiedzialny za dany zasób będzie wiedzieć o zmianach wprowadzonych dla zasobu. Skontaktowanie się z nim jest przydatne podczas określania opłat, które mogą się pojawić. Na przykład zespół będący właścicielem mógł niedawno utworzyć zasób, zaktualizować jego jednostkę SKU (zmieniając w związku z tym współczynnik zasobu) lub zwiększyć obciążenie zasobu z powodu zmian w kodzie. Kontynuuj czytanie kolejnych sekcji, aby uzyskać informacje na temat innych technik określania właściciela zasobu.

#### <a name="analyze-the-audit-logs-for-the-resource"></a>Analizowanie dzienników inspekcji dla zasobu

Jeśli masz uprawnienia do wyświetlania zasobu, powinno to także wystarczyć do uzyskania dostępu do jego dzienników inspekcji. Przejrzyj dzienniki, aby znaleźć użytkownika odpowiedzialnego za ostatnie zmiany w zasobie. Aby dowiedzieć się więcej, zobacz [Wyświetlanie i pobieranie zdarzeń dziennika aktywności platformy Azure](../../azure-monitor/platform/activity-log-view.md).

#### <a name="analyze-user-permissions-to-the-resources-parent-scope"></a>Analizowanie uprawnień użytkowników do nadrzędnego zakresu zasobu

Osoby z dostępem do zapisu do subskrypcji lub grupy zasobów zwykle mają informacje o utworzonych zasobach. Powinny być w stanie wyjaśnić przeznaczenie zasobu lub wskazać osobę, która je zna. Aby zidentyfikować osoby z uprawnieniami do zakresu subskrypcji, zobacz [Wyświetlanie przypisań ról](../../role-based-access-control/check-access.md#view-role-assignments). Podobnego procesu możesz użyć dla grup zasobów.

### <a name="get-help-to-identify-charges"></a>Uzyskiwanie pomocy w związku z identyfikowaniem opłat

Jeśli powyższe strategie są już Ci znane i nadal nie rozumiesz źródła opłaty lub potrzebujesz innej pomocy dotyczącej problemu z rozliczeniem, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o korzystaniu z [limitów wydatków](spending-limit.md), aby zapobiec nadmiernym kosztom.
- Zacznij [analizować swoje koszty związane z platformą Azure](../costs/quick-acm-cost-analysis.md).
