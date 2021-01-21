---
title: Umowy EA platformy Azure i poprawki
description: W tym artykule wyjaśniono, w jaki sposób umowy EA platformy Azure i ich poprawki wpływają na Twoje korzystanie z witryny Azure EA Portal.
author: bandersmsft
ms.author: banders
ms.date: 01/19/2021
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: enterprise
ms.reviewer: boalcsva
ms.openlocfilehash: c2c3636e98d67616826b03cca9657b806c5bd653
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98598003"
---
# <a name="azure-ea-agreements-and-amendments"></a>Umowy EA platformy Azure i poprawki

W tym artykule opisano możliwy wpływ umów EA platformy Azure i ich poprawek na sposób, w jaki uzyskujesz dostęp do usług platformy Azure, korzystasz z nich i za nie płacisz.

## <a name="enrollment-provisioning-status"></a>Stan aprowizacji rejestracji

Data rozpoczęcia nowej przedpłaty za platformę Azure (wcześniej nazywanej zobowiązaniem pieniężnym) zależy od daty przetworzenia jej w regionalnym centrum operacyjnym. Ponieważ przedpłaty za platformę Azure wynikające z zamówień składanych za pośrednictwem witryny Azure EA Portal są przetwarzane w strefie czasowej UTC, może wystąpić pewne opóźnienie, jeśli Twoja przedpłata za platformę Azure wynikająca z zamówień zakupów została przetworzona w innym regionie. Data rozpoczęcia obsługi w zamówieniu wskazuje datę rozpoczęcia przedpłaty za platformę Azure. Data rozpoczęcia obsługi odpowiada momentowi wyświetlenia przedpłaty za platformę Azure w witrynie Azure EA Portal.

## <a name="support-for-enterprise-customers"></a>Pomoc techniczna dla klientów korporacyjnych

 Dla niektórych klientów jest dostępna [Oferta planu pomocy technicznej dla umów Enterprise Agreement](https://azure.microsoft.com/offers/enterprise-agreement-support/) platformy Azure.

## <a name="enrollment-status"></a>Stan rejestracji

Rejestracja może mieć jedną z następujących wartości stanu. Każda wartość określa, w jaki sposób można używać rejestracji i uzyskiwać do niej dostęp. Stan rejestracji określa etap, na którym znajduje się rejestracja. Informuje na przykład, czy trzeba aktywować rejestrację przed jej użyciem. Może też informować o upłynięciu okresu początkowego i rozpoczęciu naliczania opłat za nadwyżkę użycia.

**Oczekująca** — administrator rejestracji musi zalogować się w witrynie Azure EA Portal. Po zalogowaniu rejestracja przejdzie w stan **Aktywna**.

**Aktywna** — rejestracja jest dostępna i można jej używać. W witrynie Azure EA Portal można tworzyć konta i subskrypcje. Rejestracja pozostanie aktywna do daty zakończenia umowy Enterprise Agreement.

**Przedłużenie na czas nieokreślony** — ten stan występuje po terminie zakończenia umowy Enterprise Agreement. Przed upływem terminu zakończenia umowy Enterprise Agreement związanej z rejestracją administrator rejestracji powinien:

- Odnowienie rejestracji przez dodanie kolejnej przedpłaty za platformę Azure
- Przenieść istniejącą rejestrację do nowej rejestracji.
- Przeprowadzić migrację do programu subskrypcji online firmy Microsoft (MOSP).
- Potwierdzić wyłączenie wszystkich usług skojarzonych z rejestracją.

**Wygasły** — rejestracja EA wygasa w dniu zakończenia umowy Enterprise Agreement. Klient z umową EA zrezygnował z przedłużenia umowy i wszystkie usługi zostały wyłączone.

Od 1 sierpnia 2019 r. nowe formularze rezygnacji nie są akceptowane w przypadku komercyjnych klientów platformy Azure. Zamiast tego wszystkie rejestracje są przedłużane na czas nieokreślony. Jeśli chcesz zakończyć korzystanie z usług platformy Azure, zamknij subskrypcję w witrynie [Azure Portal](https://portal.azure.com). Innym sposobem jest przesłanie żądania zakończenia przez Twojego partnera. Klienci z umowami dla instytucji rządowych nie zostaną obciążeni żadną opłatą.

**Przeniesiona** — ten stan jest stosowany do rejestracji, z których konta i usługi zostały przeniesione do nowej rejestracji. Rejestracje nie są przenoszone automatycznie w przypadku wygenerowania nowego numeru rejestracji podczas odnawiania. Aby nastąpiło automatyczne przeniesienie, należy uwzględnić poprzedni numer rejestracji w żądaniu odnowienia klienta.

## <a name="partner-markup"></a>Marża partnera

Marża partnera dostępna w witrynie Azure EA Portal zapewnia lepsze raportowanie kosztów dla klientów. W witrynie Azure EA Portal można wyświetlić użycie oraz ceny skonfigurowane przez partnerów dla ich klientów.

Funkcja narzutu umożliwia administratorom partnerów dodanie określonego procentowo narzutu do umów Enterprise Agreement zawieranych za ich pośrednictwem. Narzut procentowy jest stosowany do wszystkich informacji o usługach firmy Microsoft w witrynie Azure EA Portal, takich jak stawki mierników, przedpłata za platformę Azure i zamówienia. Po opublikowaniu narzutu przez partnera klient zobaczy koszty platformy Azure w witrynie Azure EA Portal. Na przykład w podsumowaniu użycia,cennikach i pobranych raportach użycia.

Od września 2019 r. partnerzy mogą dodać marżę w dowolnym czasie w danym okresie. Nie muszą czekać do kolejnej rocznicy umowy w celu dodania marży.

Firma Microsoft nie będzie mieć dostępu do podanego narzutu ani skojarzonych cen w żadnym celu, chyba że zostanie jawnie autoryzowana przez partnera kanału.

### <a name="how-the-calculation-works"></a>W jaki sposób są wykonywane obliczenia

Dostawca rozwiązań licencjonowania udostępnia pojedynczą procentową liczbę w witrynie EA Portal.    Wszystkie informacje handlowe dostępne w portalu zostaną podniesione o wartość procentową określoną przez dostawcę LSP. Przykład:

- Klient podpisuje umowę EA z przedpłatą za platformę Azure w kwocie 100 000 USD.
- Stawka miernika dla usługi A wynosi 10 USD/godzinę.
- W witrynie EA Portal dostawca rozwiązań licencjonowania ustawia narzut w wysokości 10%.
- W poniższym przykładzie przedstawiono sposób, w jaki klient zobaczy informacje handlowe:
    - Saldo środków pieniężnych: 110 000 USD.
    - Stawka miernika dla usługi A: 11 USD/godzinę.
    - Informacje o użyciu/hostingu usługi A za 100 godzin: 1100 USD.
    - Saldo środków pieniężnych dostępne dla klienta po odliczeniu użycia usługi A: 108 900 USD.

### <a name="when-to-use-a-markup"></a>Kiedy korzystać z narzutu

Ta funkcja jest przydatna w przypadku ustawiania tej samej wartości procentowej narzutu na WSZYSTKIE transakcje handlowe zawierane w ramach umowy EA. Oznacza to, że narzut powinien zostać ustawiony na informacje dotyczące przedpłaty za platformę Azure, stawki miernika, informacje o zamówieniach itd.

Nie używaj funkcji narzutu, jeśli:
- Korzystasz z różnych stawek na przedpłatę za platformę Azure i stawki miernika.
- Korzystasz z różnych stawek dla różnych mierników.

Jeśli korzystasz z różnych stawek dla różnych mierników, zalecamy utworzenie niestandardowego rozwiązania bazującego na kluczu interfejsu API, który po wprowadzeniu przez klienta spowoduje ściągnięcie danych dotyczących zużycia i udostępnienie raportów.

### <a name="other-important-information"></a>Inne istotne informacje

Ta funkcja jest przeznaczona do oceny kosztów platformy Azure dla klienta końcowego. Dostawca rozwiązań licencjonowania jest odpowiedzialny za wszystkie transakcje finansowe z klientem w ramach umowy EA.

Pamiętaj, aby przejrzeć informacje handlowe — informacje o saldzie środków pieniężnych, cennik itp. przed opublikowaniem cen z narzutem dla klientów końcowych.

### <a name="how-to-add-a-price-markup"></a>Jak dodać narzut na cenę

**Krok pierwszy: Dodawanie narzutu na cenę**

1. W witrynie Enterprise Portal kliknij pozycję **Raporty** w obszarze nawigacji po lewej.
1. W obszarze _Podsumowanie użycia_ kliknij niebieskie słowo **Narzut**.
1. Wprowadź wartość procentową narzutu (od -100 do 100), a następnie kliknij pozycję **Podgląd**.


**Krok drugi: Przegląd i sprawdzanie poprawności**

Przejrzyj ceny z narzutem w obszarze _Podsumowanie użycia_ dla terminu przedpłaty w widoku klienta. Cena firmy Microsoft będzie nadal dostępna w widoku partnera. Widoki można przełączać za pomocą przełącznika „osoby” narzutu partnera w prawym górnym rogu.

1. Przejrzyj ceny w arkuszu cen.
1. Przed opublikowaniem można wprowadzać zmiany, wybierając pozycję **Edytuj** na karcie _Wyświetl podsumowanie użycia > Widok klienta_. 
   
Ta sama wartość procentowa narzutu zostanie naliczona zarówno dla cen usług, jak i salda przedpłat. Nie używaj tej funkcji, jeśli masz różne wartości procentowe dla salda środków pieniężnych i stawek mierników lub różne wartości procentowe dla różnych usług.

**Krok trzeci: Publikowanie**

Po przejrzeniu i sprawdzeniu poprawności cen kliknij pozycję **Publikuj**.
  
Cennik z narzutem będzie dostępny dla administratorów przedsiębiorstw natychmiast po wybraniu opcji publikowania. Nie można wprowadzać zmian w narzucie. Jeśli chcesz coś zmodyfikować, musisz wyłączyć funkcję narzutu i zacząć od pierwszego kroku.

### <a name="which-enrollments-have-a-markup-enabled"></a>Które rejestracje mają włączoną funkcję narzutu?

Aby sprawdzić, czy rejestracja ma opublikowany narzut, kliknij pozycję **Zarządzaj** na lewym pasku nawigacji, a następnie kliknij kartę **Rejestracja**. Zaznacz pole wyboru rejestracji, aby sprawdzić i wyświetlić stan narzutu w obszarze _Szczegóły rejestracji_. Spowoduje to wyświetlenie bieżącego stanu funkcji narzutu dla tej umowy EA. Możliwe wartości to Wyłączone, Podgląd i Opublikowane.

### <a name="how-can-the-customer-download-usage-estimates"></a>Jak klient może pobrać szacunkowe wartości użycia?

Po opublikowaniu narzutu partnera klient pośredni będzie miał dostęp do tworzonych co miesiąc plików CSV zawierających informacje o saldzie i opłatach oraz do plików CSV zawierających szczegóły użycia. Pliki ze szczegółami użycia będą obejmować stawki zasobu i rozszerzony koszt.

### <a name="how-can-i-as-partner-apply-markup-to-existing-ea-customers-that-was-earlier-with-another-partner"></a>Jak mogę jako partner stosować narzut dla istniejących klientów z umową EA, którzy byli wcześniej obsługiwani przez innego partnera?
Partnerzy mogą korzystać z funkcji narzutu (w witrynie Azure EA) po przetworzeniu zmiany partnera handlowego. Nie trzeba czekać do następnego cyklu rocznego.


## <a name="resource-prepayment-and-requesting-quota-increases"></a>Zwiększanie przedpłaty dostarczenia zasobów i limitu przydziału

**System wymusza następujące domyślne przydziały dla każdej subskrypcji:**

| **Zasób** | **Domyślny limit przydziału** | **Komentarze** |
| --- | --- | --- |
| Wystąpienia obliczeniowe platformy Microsoft Azure | 20 współbieżnych małych wystąpień obliczeniowych lub równoważna ich liczba w przypadku innych rozmiarów wystąpień obliczeniowych. | W poniższej tabeli przedstawiono sposób obliczania równoważnej liczby małych wystąpień:<ul><li> Bardzo małe — odpowiednik 1 małego wystąpienia </li><li> Małe — odpowiednik 1 małego wystąpienia </li><li> Średnie — odpowiednik 2 małych wystąpień </li><li> Duże — odpowiednik 4 małych wystąpień </li><li> Bardzo duże — odpowiednik 8 małych wystąpień </li> </ul>|
| Wystąpienia obliczeniowe platformy Microsoft Azure — maszyny wirtualne w wersji 2 | EA: 350 rdzeni | Ogólnie dostępne maszyny wirtualne IaaS w wersji 2:<ul><li> Rodzina A0\_A7 — 350 rdzeni </li><li> Rodzina B\_A0\_A4 — 350 rdzeni </li><li> Rodzina A8\_A9 — 350 rdzeni </li><li> Rodzina DF — 350 rdzeni</li><li> GF — 350 rdzeni </li></ul>|
| Usługi hostowane na platformie Microsoft Azure | 6 hostowanych usług | Tego limitu usług hostowanych nie można zwiększyć powyżej sześciu w przypadku pojedynczej subskrypcji. Jeśli potrzebujesz dodatkowych usług hostowanych, dodaj kolejne subskrypcje. |
| Microsoft Azure Storage | 5 kont magazynu, każde o maksymalnym rozmiarze 100 TB. | Można zwiększyć liczbę kont magazynu do maksymalnie 20 na subskrypcję. Jeśli potrzebujesz dodatkowych kont magazynu, dodaj kolejne subskrypcje. |
| Usługi SQL Azure | 149 baz danych dowolnego typu (tj. Web Edition lub Business Edition). |   |
| Kontrola dostępu | 50 przestrzeni nazw na konto. 100 milionów transakcji kontroli dostępu na miesiąc |   |
| Service Bus | 50 przestrzeni nazw na konto. 40 połączeń usługi Service Bus | Klienci, którzy kupują połączenia usługi Service Bus za pośrednictwem pakietów połączeń, będą mieć limity przydziału równe punktowi środkowemu między zakupionym pakietem połączeń a następną najwyższą wartością pakietu połączeń. Klienci, którzy wybiorą pakiet 500, będą mieli limit przydziału równy 750. |

## <a name="resource-prepayment"></a>Przedpłata za zasoby

Firma Microsoft będzie zapewniać usługi co najmniej na poziomie powiązanego użycia, które obejmują zakupioną przedpłatę miesięczną (przedpłata za świadczenie usług), ale każde zwiększenie poziomu użycia zasobów usługi (np. zwiększenie liczby uruchomionych wystąpień obliczeniowych lub ilości używanego miejsca) jest zależne od dostępności tych zasobów usługi.

Opisane powyżej przydziały nie są przedpłatą za usługi. W celu określenia liczby jednoczesnych małych wystąpień obliczeniowych (lub ich odpowiednika) zapewnianych w ramach przedpłaty za świadczenie usług zatwierdzona liczba godzin małego wystąpienia obliczeniowego w miesiącu jest dzielona przez liczbę godzin w najkrótszym miesiącu roku (tj. lutym — 672 godziny).

## <a name="requesting-a-quota-increase"></a>Żądanie zwiększenia limitu przydziału

W dowolnym momencie możesz poprosić o zwiększenie limitu przydziału przez przesłanie [żądania online](https://g.microsoftonline.com/0WAEP00en/6). Aby przetworzyć żądanie, podaj następujące informacje:

- Konto Microsoft lub konto służbowe skojarzone z właścicielem konta Twojej subskrypcji. Jest to adres e-mail używany do logowania się w witrynie Microsoft Azure Portal w celu zarządzania subskrypcjami. Upewnij się, że jest to konto skojarzone z rejestracją w ramach umowy EA.
- Zasoby, dla których chcesz zwiększyć limit przydziału, i ich ilość.
- Identyfikator subskrypcji portalu deweloperów platformy Azure skojarzony z Twoją usługą.
  - Aby uzyskać informacje na temat sposobu uzyskiwania identyfikatora subskrypcji, [skontaktuj się z pomocą techniczną](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="plan-skus"></a>Jednostki SKU planu

Jednostki SKU planu oferują możliwość zakupu zestawu zintegrowanych usług po obniżonej stawce. Jednostki SKU planu są zaprojektowane tak, aby uzupełniać się wzajemnie w ramach dalszych zintegrowanych ofert i tworzyć zestaw w celu zwiększenia oszczędności.

Przykładem może być subskrypcja pakietu Operations Management Suite (OMS). Pakiet OMS oferuje prosty sposób uzyskania dostępu do pełnego zestawu opartych na chmurze funkcji zarządzania, takich jak analiza, konfiguracja, automatyzacja, zabezpieczenia, tworzenie kopii zapasowych i odzyskiwanie po awarii. Subskrypcje pakietu OMS obejmują prawa do składników programu System Center w celu zapewnienia kompletnego rozwiązania dla środowisk chmury hybrydowej.

Administratorzy przedsiębiorstwa mogą nadawać właścicielom kont prawa do aprowizowania wcześniej zakupionych jednostek SKU planu w witrynie Enterprise Portal, wykonując następujące czynności:

### <a name="view-the-price-sheet-to-check-included-quantity"></a>Wyświetlanie arkusza cen w celu sprawdzenia uwzględnionej ilości

1. Zaloguj się jako administrator przedsiębiorstwa.
1. Kliknij pozycję **Raporty** w obszarze nawigacji po lewej.
1. Kliknij kartę **Arkusz cen**.
1. Kliknij ikonę „Pobierz” w prawym górnym rogu.
1. Znajdź odpowiednie numery części jednostek SKU planu, korzystając z filtru w kolumnie „Uwzględniona ilość”, i wybierz wartości większe niż „0”.

### <a name="existingnew-account-owners-to-create-new-subscriptions"></a>Tworzenie nowych subskrypcji przez istniejących lub nowych właścicieli kont

**Krok pierwszy: Logowanie się do konta**
1. W witrynie EA Portal wybierz kartę **Zarządzanie** i przejdź do pozycji **Subskrypcja** w górnym menu.
1. Sprawdź fakt zalogowania jako właściciel konta.
1. Kliknij pozycję **+Dodaj subskrypcję**.
1. Kliknij pozycję **Kup**.

Przy pierwszym dodawaniu subskrypcji do konta konieczne będzie podanie swoich informacji kontaktowych. Podczas dodawania kolejnych subskrypcji Twoje informacje kontaktowe będą automatycznie dodawane.

Przy pierwszym dodawaniu subskrypcji do konta zostanie wyświetlona prośba o zaakceptowanie umowy MOSA oraz planu taryfowego. Te sekcje NIE mają zastosowania do klientów posiadających umowę Enterprise Agreement, ale są obecnie niezbędne do aprowizacji Twojej subskrypcji. Poprawka do rejestracji umowy Microsoft Azure Enterprise zastępuje powyższe elementy, a Twoja relacja umowna nie ulegnie zmianie. Zaznacz pole wyboru oznaczające zaakceptowanie warunków.

**Krok drugi: Aktualizowanie nazwy subskrypcji**

Wszystkie nowe subskrypcje zostaną dodane z domyślną nazwą subskrypcji „Microsoft Azure Enterprise”. Ważne jest, aby zaktualizować nazwę subskrypcji w celu odróżnienia jej od innych subskrypcji w ramach rejestracji przedsiębiorstwa i upewnić się, że można ją rozpoznać w raportach na poziomie przedsiębiorstwa.

Kliknij pozycję **Subskrypcje**, wybierz utworzoną subskrypcję, a następnie kliknij pozycję **Edytuj szczegóły subskrypcji**.

Zaktualizuj nazwę subskrypcji i administratora usługi, a następnie kliknij znacznik wyboru. Ta nazwa subskrypcji będzie wyświetlana w raportach i będzie też nazwą projektu skojarzonego z subskrypcją w portalu dla deweloperów.
Propagowanie nowych subskrypcji na liście subskrypcji może zająć do 24 godzin.

Tylko właściciele kont mogą wyświetlać subskrypcje i zarządzać nimi.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

**Właściciel konta ma stan oczekiwania**

Gdy nowi właściciele konta są po raz pierwszy dodawani do rejestracji, ich stan jest wyświetlany jako „oczekujące”. Po otrzymaniu powitalnej wiadomości e-mail z informacjami o aktywacji właściciel konta może się zalogować, aby aktywować swoje konto. Aktywacja spowoduje zaktualizowanie stanu konta z „oczekujące” na „aktywne”.

**Naliczanie opłat za użycie po zakupieniu jednostek SKU planu**

Ten scenariusz występuje, gdy klient wdrożył usługi, korzystając z niewłaściwego numeru rejestracji, lub wybrał niewłaściwe usługi.

Aby się upewnić, czy wdrożenie nastąpiło w ramach właściwej rejestracji, możesz sprawdzić informacje o uwzględnionych jednostkach za pośrednictwem arkusza cen. Zaloguj się jako administrator przedsiębiorstwa i kliknij pozycję **Raporty** na lewym pasku nawigacyjnym, a następnie wybierz kartę **Arkusz cen**. Kliknij ikonę pobierania w prawym górnym rogu i znajdź odpowiednie numery części jednostek SKU planu, korzystając z filtru w kolumnie „Uwzględniona ilość”, i wybierz wartości większe niż „0”.

Upewnij się, że plan pakietu OMS jest wyświetlany w arkuszu cen w obszarze uwzględnionych jednostek. Jeśli w rejestracji nie ma uwzględnionych jednostek dla planu pakietu OMS, być może Twój plan znajduje się w innej rejestracji. Skontaktuj się z pomocą techniczną witryny Azure Enterprise Portal pod adresem [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

Jeśli uwzględnione jednostki usług w arkuszu cen nie pasują do wdrożonych elementów, np. Przeanalizowane dane (warstwa Premium) usługi Operational Insights i Przeanalizowane dane (warstwa Standardowa) usługi Operational Insights, oznacza to, że być może wdrożono usługi, które nie są objęte planem. Skontaktuj się z pomocą techniczną witryny Azure Enterprise Portal pod adresem [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport), aby uzyskać dalszą pomoc.

**Aprowizowane usługi w ramach jednostek SKU planu znajdują się w niewłaściwej rejestracji**

Jeśli masz wiele rejestracji i wdrożono usługi, korzystając z niewłaściwego numeru rejestracji, który nie ma planu pakietu OMS, skontaktuj się z pomocą techniczną witryny Azure Enterprise Portal pod adresem [https://aka.ms/AzureEntSupport](https://aka.ms/AzureEntSupport).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak rozpocząć korzystanie z witryny Azure EA Portal, zobacz [Wprowadzenie do witryny Azure EA Portal](ea-portal-get-started.md).
- Administratorzy witryny Azure EA Portal powinni przeczytać artykuł [Administracja w witrynie Azure EA Portal](ea-portal-administration.md), aby poznać typowe zadania administracyjne.
