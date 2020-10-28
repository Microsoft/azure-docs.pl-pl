---
title: Szybki start — eksplorowanie kosztów platformy Azure za pomocą analizy kosztów
description: Ten przewodnik Szybki start ułatwia eksplorowanie i analizowanie kosztów organizacyjnych platformy Azure za pomocą funkcji analizy kosztów.
author: bandersmsft
ms.author: banders
ms.date: 10/26/2020
ms.topic: quickstart
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.custom: contentperfq2
ms.openlocfilehash: 31a95d8c02ee540fe6b52088159f04535c39ea93
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92676835"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Szybki start: Eksplorowanie i analizowanie kosztów za pomocą analizy kosztów

Aby poprawnie kontrolować i optymalizować koszty platformy Azure, trzeba zrozumieć, skąd pochodzą koszty w organizacji. Warto również wiedzieć, ile kosztują usługi oraz które środowiska i systemy obsługują. Widoczność pełnego zakresu kosztów to kluczowy element wymagany do dokładnego zrozumienia wzorca wydatków organizacji. Za pomocą wzorców wydatków możesz wymuszać mechanizmy kontroli kosztów, takie jak budżety.

Ten przewodnik Szybki start ułatwia eksplorowanie i analizowanie kosztów organizacyjnych przy użyciu funkcji analizy kosztów. Możesz wyświetlić zagregowane koszty według organizacji, aby dowiedzieć się, gdzie występują koszty w miarę upływu czasu, i zidentyfikować trendy wydatków. Zakumulowane koszty w czasie można wyświetlać w celu oszacowania miesięcznych, kwartalnych, a nawet rocznych trendów kosztów w odniesieniu do budżetu. Budżet pomaga działać zgodnie z ograniczeniami finansowymi. Budżet jest również używany do wyświetlania kosztów dziennych lub miesięcznych w celu odizolowania nieprawidłowości wydatków. Ponadto można pobrać dane bieżącego raportu do dalszej analizy lub do użycia w systemie zewnętrznym.

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

- Przeglądanie kosztów w obrębie analizy kosztów
- Dostosowywanie widoków kosztów
- Pobieranie danych analizy kosztów

## <a name="prerequisites"></a>Wymagania wstępne

Analiza kosztów obsługuje różne typy kont platformy Azure. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](understand-cost-mgt-data.md). Aby wyświetlić dane kosztów, potrzebujesz przynajmniej dostępu do odczytu dla Twojego konta platformy Azure.

Aby uzyskać informacje na temat przypisywania dostępu do danych usługi Azure Cost Management, zobacz [Przypisywanie dostępu do danych](./assign-access-acm-data.md).

Jeśli masz nową subskrypcję, nie możesz od razu korzystać z funkcji usługi Cost Management. Aby można było korzystać ze wszystkich funkcji usługi Cost Management, może upłynąć do 48 godzin.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

- Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="review-costs-in-cost-analysis"></a>Przeglądanie kosztów w obrębie analizy kosztów

Aby sprawdzić swoje koszty w analizie kosztów, otwórz zakres w witrynie Azure Portal, a następnie wybierz pozycję **Analiza kosztów** w menu. Na przykład przejdź do obszaru **Subskrypcje** , wybierz subskrypcję z listy, a następnie wybierz pozycję **Analiza kosztów** w menu. Użyj kapsułki **Zakres** , aby przełączyć na inny zakres w analizie kosztów.

Wybrany zakres będzie używany w całej usłudze Cost Management w celu zapewnienia konsolidacji danych i kontrolowania dostępu do informacji o kosztach. Gdy używasz zakresów, nie wybierasz równocześnie wielu z nich. W zamian wybierasz większy zakres obejmujący inne zakresy, a następnie filtrujesz zawartość do zagnieżdżonych zakresów, których potrzebujesz. Zrozumienie tej metody jest istotne, ponieważ niektóre osoby mogą nie mieć dostępu do pojedynczego zakresu nadrzędnego, który obejmuje wiele zakresów zagnieżdżonych.

Obejrzyj klip wideo [Jak używać usługi Cost Management w witrynie Azure Portal](https://www.youtube.com/watch?v=mfxysF-kTFA), aby dowiedzieć się więcej o sposobach korzystania z analizy kosztów. Aby obejrzeć inne wideo, odwiedź [kanał usługi Cost Management w serwisie YouTube](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/mfxysF-kTFA]

Początkowy widok analizy kosztów zawiera poniższe obszary.

**Widok kosztów skumulowanych** : Przedstawia wstępnie zdefiniowaną konfigurację widoku analizy kosztów. Każdy widok zawiera ustawienia zakresu dat, stopnia szczegółowości, sposobu grupowania i filtrowania. Widok domyślny pokazuje skumulowane koszty bieżącego okresu rozliczeniowego, ale można go zmienić na inne wbudowane widoki.

**Rzeczywisty koszt** : Przedstawia sumę kosztów użycia i zakupów dla bieżącego miesiąca, które będą widoczne na fakturze, w miarę ich naliczania.

**Prognoza** : Przedstawia sumę prognozowanych kosztów w wybranym okresie.

**Budżet** : Przedstawia limit planowanych wydatków w wybranym zakresie, jeśli jest dostępny.

**Skumulowany stopień szczegółowości** : Przedstawia sumę zagregowanych kosztów dziennych od początku okresu rozliczeniowego. Po utworzeniu budżetu subskrypcji lub konta billingowego możesz szybko wyświetlić trend wydatków w odniesieniu do budżetu. Umieść kursor nad datą, aby wyświetlić skumulowany koszt w tym dniu.

**Wykresy przestawne (pierścieniowe)** : Są to dynamiczne elementy przestawne, które przedstawiają podział kosztów według typowego zestawu właściwości standardowych. Przedstawiają one koszty w bieżącym miesiącu, w kolejności od największych do najmniejszych. Wykresy przestawne można zmieniać w dowolnym momencie, wybierając inny element przestawny. Koszty są domyślnie dzielone na kategorie według usługi (kategorii miernika), lokalizacji (regionu) i zakresu podrzędnego. Na przykład konta rejestracji należą do kont rozliczeniowych, grupy zasobów należą do subskrypcji, a zasoby należą do grup zasobów.

![Początkowy widok analizy kosztów w witrynie Azure Portal](./media/quick-acm-cost-analysis/cost-analysis-01.png)

### <a name="understand-forecast"></a>Informacje o prognozie

Prognoza kosztów pokazuje szacowane koszty w wybranym okresie. Model jest oparty na modelu regresji szeregów czasowych. Do dokładnego przewidywania kosztów wymagane są dane dotyczące kosztów i użycia z co najmniej 10 ostatnich dni. W przypadku danego okresu model prognozy wymaga takiej samej ilości danych treningowych jak dla prognozowanego okresu. Na przykład prognoza na trzy miesiące wymaga danych użycia i kosztów z co najmniej trzech ostatnich miesięcy.

Model używa maksymalnie sześciu miesięcy danych treningowych w celu prognozowania kosztów na rok. Zmiana prognozy wymaga danych treningowych z co najmniej siedmiu dni. Prognoza jest oparta na znaczących zmianach, takich jak istotny wzrost lub spadek, we wzorcach kosztów i użycia. Prognoza nie generuje oddzielnych przewidywań dla każdego elementu we właściwościach **Grupuj według** . Zapewnia jedynie prognozę dla łącznych, skumulowanych kosztów. W przypadku korzystania z wielu walut model udostępnia prognozę kosztów tylko w USD.

## <a name="customize-cost-views"></a>Dostosowywanie widoków kosztów

Analiza kosztów ma cztery wbudowane widoki zoptymalizowane pod kątem najpopularniejszych celów:

Widok | Odpowiada na pytania
--- | ---
Skumulowany koszt | Ile wynoszą dotychczasowe wydatki w tym miesiącu? Czy wydatki zmieściły się w budżecie?
Dzienny koszt | Czy w ciągu ostatnich 30 dni wystąpiły jakiekolwiek wzrosty kosztów dziennych?
Koszt według usługi | Jak moje miesięczne użycie zmieniało się w okresie objętym przez trzy ostatnie faktury?
Koszt według zasobu | Które zasoby kosztowały dotychczas najwięcej w tym miesiącu?

![Selektor widoku przedstawiający przykładowy wybór dla tego miesiąca](./media/quick-acm-cost-analysis/view-selector.png)

W większości przypadków będziesz jednak potrzebować dokładniejszej analizy. Dostosowywanie rozpoczyna się od wybrania daty w górnej części strony.

Analiza kosztów domyślnie przedstawia dane z bieżącego miesiąca. Selektor daty służy do szybkiego przełączania typowych zakresów dat. Przykłady to: ostatnie siedem dni, ostatni miesiąc, bieżący rok lub niestandardowy zakres dat. Subskrypcje płatne zgodnie z rzeczywistym użyciem obejmują również zakresy dat oparte na okresie rozliczeniowym, który nie jest powiązany z miesiącem kalendarzowym, takie jak bieżący okres rozliczeniowy lub ostatnia faktura. Aby przejść odpowiednio do poprzedniego lub następnego okresu, skorzystaj z linków **< WSTECZ** i **DALEJ >** u góry menu. Na przykład kliknięcie linku **< WSTECZ** spowoduje przełączenie z okresu **Ostatnie 7 dni** do okresu **8–14 dni temu** lub **15–21 dni temu** .

![Selektor daty przedstawiający przykładowy wybór dla tego miesiąca](./media/quick-acm-cost-analysis/date-selector.png)

Analiza kosztów domyślnie przedstawia **skumulowane** koszty. Skumulowane koszty obejmują wszystkie koszty dla każdego dnia oraz poprzednich dni i umożliwiają tworzenie wciąż rosnącego widoku zagregowanych kosztów dziennych. Ten widok jest optymalizowany w celu pokazania trendów względem budżetu dla wybranego zakresu czasu.

Widok wykresu prognozy służy do identyfikowania potencjalnych naruszeń budżetu. Gdy istnieje możliwość naruszenia budżetu, przewidywane przekroczenie jest wyświetlane na czerwono. Na wykresie jest również widoczny symbol wskaźnika. Umieszczając wskaźnik myszy nad symbolem, można zobaczyć szacowaną datę naruszenia budżetu.

![Przykład pokazujący potencjalne naruszenie budżetu](./media/quick-acm-cost-analysis/budget-breach.png)

Istnieje również widok **dzienny** przedstawiający koszty danego dnia. Widok dzienny nie zawiera trendu wzrostu. Widok został zaprojektowany z myślą o wyświetlaniu nieprawidłowości, gdy koszt gwałtownie wzrasta lub spada z dnia na dzień. W przypadku wybrania budżetu widok dzienny pokazuje również szacowany budżet na dany dzień.

Jeśli koszty dzienne stale przekraczają szacowany budżet dzienny, można oczekiwać przekroczenia kwoty budżetu miesięcznego. Szacowany budżet dzienny to metoda ułatwiająca wizualizowanie budżetu na niższym poziomie. W przypadku wahań kosztów dziennych porównanie szacowanego budżetu dziennego z budżetem miesięcznym jest mniej dokładne.

Oto widok dzienny ostatnich wydatków z włączoną prognozą wydatków.
![Widok dzienny przedstawiający przykładowe koszty dzienne dla bieżącego miesiąca](./media/quick-acm-cost-analysis/daily-view.png)

Po wyłączeniu prognozy wydatków nie widać przewidywanych wydatków w przyszłości. Ponadto podczas przeglądania kosztów dla ostatnich okresów prognoza kosztów nie pokazuje kosztów.

Ogólnie rzecz biorąc, w ciągu 8–12 godzin można spodziewać się danych lub powiadomień dotyczących wykorzystanych zasobów.

**Grupuj według** typowych właściwości, aby dzielić koszty i identyfikować ich najważniejsze składniki. Na przykład aby grupować według tagów zasobów, wybierz klucz tagu, według którego chcesz grupować. Koszty zostaną podzielone według poszczególnych wartości tagu oraz dodatkowego segmentu dla zasobów, do których nie zastosowano tego tagu.

Większość zasobów platformy Azure obsługuje tagowanie. Jednak niektóre tagi nie są dostępne na stronie Zarządzanie kosztami i rozliczenia. Ponadto nie są obsługiwane tagi grup zasobów. Obsługa tagów dotyczy użycia zgłaszanego *po tym* , gdy tag został zastosowany do zasobu. Tagi nie są stosowane wstecznie dla celów zestawień kosztów.

Obejrzyj film wideo [How to review tag policies with Azure Cost Management](https://www.youtube.com/watch?v=nHQYcYGKuyw) (Jak przeglądać zasady tagów za pomocą usługi Azure Cost Management), aby dowiedzieć się więcej o korzystaniu z zasad tagów platformy Azure w celu zwiększenia widoczności danych dotyczących kosztów.

Oto widok kosztów usług platformy Azure dla bieżącego miesiąca.

![Skumulowany, zgrupowany widok dzienny przedstawiający przykładowe koszty usług platformy Azure w ostatnim miesiącu](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Domyślnie analiza kosztów pokazuje wszystkie koszty użycia i zakupów, które będą widoczne na fakturze, w miarę ich naliczania. Ta funkcja nosi również nazwę **rzeczywistego kosztu** . Wyświetlanie rzeczywistego kosztu idealnie nadaje się do uzgadniania faktury. Jednak skokowe wzrosty kosztu zakupów mogą być alarmujące, gdy pilnujesz anomalii w wydatkach i innych zmian kosztów. Aby wyrównać wzrosty wynikające z kosztów zakupu rezerwacji, przełącz się na **koszt zamortyzowany** .

![Zmiana kosztu rzeczywistego na zamortyzowany w celu wyświetlenia zakupów rezerwacji rozłożonych w całym okresie i przydzielonych do zasobów, które używały rezerwacji](./media/quick-acm-cost-analysis/metric-picker.png)

Koszt zamortyzowany dzieli zakupy rezerwacji na dzienne fragmenty i rozkłada je w czasie trwania terminu rezerwacji. Na przykład zamiast zakupu na kwotę 365 USD 1 stycznia, każdego dnia w okresie od 1 stycznia do 31 grudnia będzie wyświetlany zakup w wysokości 1,00 USD. Oprócz podstawowej amortyzacji te koszty są również ponownie przydzielane i kojarzone przy użyciu określonych zasobów, które używały rezerwacji. Jeśli na przykład opłata dzienna w wysokości 1,00 USD została podzielona między dwie maszyny wirtualne, każdego dnia będą widoczne dwie opłaty w wysokości 0,50 USD. Jeśli część rezerwacji nie została wykorzystana danego dnia, zobaczysz jedną opłatę 0,50 USD skojarzoną z odpowiednią maszyną wirtualną i drugą opłatę 0,50 USD typu `UnusedReservation`. Pamiętaj, że koszty niewykorzystanych rezerwacji są widoczne tylko w przypadku wyświetlania kosztu zamortyzowanego.

W związku ze zmianą sposobu reprezentowania kosztów trzeba pamiętać, że w widokach kosztów rzeczywistych i zamortyzowanych będą wyświetlane różne sumy. Ogólnie rzecz biorąc, podczas przeglądania kosztów zamortyzowanych suma kosztów w miesiącach z zakupem rezerwacji będzie się zmniejszała, a w miesiącach po zakupie rezerwacji — zwiększała. Amortyzacja jest dostępna tylko w przypadku zakupów rezerwacji i w tej chwili nie dotyczy zakupów w witrynie Azure Marketplace.

Na poniższej ilustracji pokazano nazwy grup zasobów. Możesz grupować według tagów, aby wyświetlić sumę kosztów każdego tagu, lub użyć widoku **Koszt według zasobu** , aby wyświetlić wszystkie tagi dla określonego zasobu.

![Pełne dane dla bieżącego widoku, w którym są wyświetlane nazwy grup zasobów](./media/quick-acm-cost-analysis/full-data-set.png)

Podczas grupowania kosztów według konkretnego atrybutu 10 największych składników kosztów jest pokazanych od najwyższego do najniższego. Jeśli jest ich więcej niż 10, pokazanych jest dziewięć największych składników kosztów oraz grupa **Inne** , która przedstawia wszystkie pozostałe grupy łącznie. W przypadku grupowania według tagów wyświetlana jest także grupa **Bez tagów** dla kosztów, w których nie zastosowano klucza tagu. Grupa **Bez tagów** jest zawsze wyświetlana jako ostatnia, nawet jeśli koszty bez tagów są wyższe niż koszty z tagami. Jeśli istnieje 10 lub więcej wartości tagów, koszty bez tagów będą częścią grupy **Inne** . Przejdź do widoku tabeli i zmień poziom szczegółowości na **Brak** , aby wyświetlić wszystkie wartości sklasyfikowane od najwyższego do najniższego kosztu.

Klasyczne maszyny wirtualne, sieci i zasoby magazynu nie udostępniają szczegółowych danych dotyczących rozliczeń. Są one scalane jako **Usługi klasyczne** podczas grupowania kosztów.

Wykresy przestawne w obszarze wykresu głównego przedstawiają różne sposoby grupowania, które dają szerszy obraz kosztów ogólnych dla wybranego przedziału czasu i filtrów. Wybierz właściwość lub tag, aby wyświetlić zagregowane koszty według dowolnego wymiaru.

![Przykład przedstawiający wykresy przestawne](./media/quick-acm-cost-analysis/pivot-charts.png)

Możesz wyświetlić pełny zestaw danych dla dowolnego widoku. Wszelkie zastosowane opcje lub filtry wpływają na prezentowane dane. Aby wyświetlić pełny zestaw danych, wybierz listę **typ wykresu** , a następnie wybierz widok **tabeli** .

![Dane dla bieżącego widoku w widoku tabeli](./media/quick-acm-cost-analysis/chart-type-table-view.png)

## <a name="saving-and-sharing-customized-views"></a>Zapisywanie i udostępnianie dostosowanych widoków

Dostosowane widoki można zapisywać i udostępniać innym osobom, przypinając analizę kosztów do pulpitu nawigacyjnego witryny Azure Portal lub kopiując link do analizy kosztów.

Obejrzyj klip wideo [Udostępnianie i zapisywanie widoków w usłudze Azure Cost Management](https://www.youtube.com/watch?v=kQkXXj-SmvQ), aby dowiedzieć się więcej o tym, jak udostępniać za pomocą portalu informacji o kosztach w organizacji. Aby obejrzeć inne wideo, odwiedź [kanał usługi Cost Management w serwisie YouTube](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/kQkXXj-SmvQ]

Aby przypiąć analizę kosztów, wybierz ikonę pinezki w prawym górnym rogu lub bezpośrednio po nagłówku <Subscription Name> | Analiza kosztów”. Przypięcie analizy kosztów spowoduje zapisanie tylko głównego wykresu lub widoku tabeli. Udostępnij pulpit nawigacyjny, aby umożliwić innym użytkownikom dostęp do kafelka. Pamiętaj, że spowoduje to tylko udostępnienie konfiguracji pulpitu nawigacyjnego i nie udzieli innym użytkownikom dostępu do danych źródłowych. Jeśli nie masz dostępu do kosztów, ale masz dostęp do udostępnionego pulpitu nawigacyjnego, zobaczysz komunikat „odmowa dostępu”.

Aby udostępnić link do analizy kosztów, wybierz pozycję **Udostępnij** w górnej części bloku. Zostanie wyświetlony niestandardowy adres URL, który otwiera ten konkretny widok dla określonego zakresu. Jeśli nie masz dostępu do kosztów i uzyskasz ten adres URL, zobaczysz komunikat „odmowa dostępu”.

## <a name="download-usage-data"></a>Pobieranie danych użycia

Czasami musisz pobrać dane do dalszej analizy, scalić je z własnymi danymi lub zintegrować ze swoimi systemami. Usługa Cost Management oferuje kilka różnych opcji. Na początek, jeśli potrzebujesz podsumowania wysokiego poziomu ad hoc, takiego jak to, które otrzymujesz w ramach analizy kosztów, utwórz potrzebny widok. Następnie pobierz go, wybierając pozycję **Eksportuj** , a następnie pozycję **Pobierz dane do pliku CSV** lub **Pobierz dane do programu Excel** . Pobranie danych do programu Excel zapewnia dodatkowy kontekst w widoku użytym do wygenerowania pobierania, taki jak zakres, konfiguracja zapytania, suma i data wygenerowania.

Jeśli potrzebujesz pełnego, niezagregowanego zestawu danych, pobierz go z konta rozliczeniowego. Następnie na liście usług w okienku nawigacji po lewej stronie portalu przejdź do pozycji **Zarządzanie kosztami i rozliczenia** . Wybierz konto rozliczeniowe, jeśli ma to zastosowanie. Przejdź do pozycji **Użycie + opłaty** , a następnie wybierz ikonę **Pobierz** dla żądanego okresu rozliczeniowego.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

- Jeśli masz przypięty dostosowany widok analizy kosztów, który nie jest już potrzebny, przejdź do pulpitu nawigacyjnego, do którego widok został przypięty, i usuń go.
- Jeśli masz pobrane pliki danych użycia, które nie są już potrzebne, pamiętaj o ich usunięciu.

## <a name="next-steps"></a>Następne kroki

Przejdź do pierwszego samouczka, aby dowiedzieć się, jak tworzyć budżety i nimi zarządzać.

> [!div class="nextstepaction"]
> [Tworzenie budżetów i zarządzanie nimi](tutorial-acm-create-budgets.md)