---
title: Jak zarządzać komercyjnym kontem witryny Marketplace w centrum partnerskim firmy Microsoft
description: Dowiedz się, jak zarządzać komercyjnym kontem witryny Marketplace w centrum partnerskim firmy Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: parthpandyaMSFT
ms.author: parthp
ms.date: 08/27/2020
ms.openlocfilehash: d794716fe1f4717acbabfd90643b72c682e475bc
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370597"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>Zarządzanie kontem komercyjnej witryny Marketplace w centrum partnerskim

Po [utworzeniu konta Centrum partnerskiego](./create-account.md)możesz użyć [pulpitu nawigacyjnego portalu Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) , aby zarządzać kontem i ofertami.

W tym artykule szczegółowemy, jak zarządzać kontem Centrum partnerskiego, w tym:

- [Dostęp do ustawień konta Centrum partnerskiego](#access-your-account-settings)
- [Znajdź identyfikator wydawcy, identyfikator firmy Symantec, identyfikator sprzedającego, identyfikator użytkownika, identyfikator MPN i dzierżawy usługi Azure AD](#account-details)
- [Aktualizowanie informacji kontaktowych](#contact-info)
- [Konfigurowanie identyfikatorów GUID śledzenia do monitorowania użycia klienta](#tracking-guids)
- [Zarządzanie użytkownikami](#manage-users)
- [Zarządzanie grupami](#manage-groups)
- [Zarządzanie aplikacjami usługi Azure AD](#manage-azure-ad-applications)
- [Definiowanie uprawnień i ról użytkowników](#define-user-roles-and-permissions)
- [Zarządzanie dzierżawami usługi Azure AD (konta służbowe)](#manage-tenants)
- [Zarządzaj umowami Centrum partnerskiego](#agreements)

## <a name="access-your-account-settings"></a>Dostęp do ustawień konta

Jeśli jeszcze tego nie zrobiono, użytkownik (lub administrator organizacji) powinien uzyskać dostęp do [ustawień konta](https://partner.microsoft.com/dashboard/account/management) Centrum partnerskiego, aby:
- Sprawdź stan weryfikacji konta firmowego
- Potwierdź identyfikator firmy Symantec, identyfikator sprzedającego, identyfikator Microsoft Partner Network (MPN), identyfikator wydawcy i informacje kontaktowe, w tym osobę zatwierdzającą w firmie i kontakt ze sprzedającym
- Utwórz konta użytkowników dla każdej osoby, która będzie korzystać z konta firmowego w centrum partnerskim

### <a name="open-developer-settings"></a>Otwórz ustawienia dewelopera

**Ustawienia konta** znajdują się w prawym górnym rogu [pulpitu nawigacyjnego portalu Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace) w centrum partnerskim. Wybierz ikonę koła zębatego (w prawym górnym rogu pulpitu nawigacyjnego), a następnie wybierz pozycję **Ustawienia dewelopera**.

![Menu Ustawienia konta w centrum partnerskim](./media/dashboard-developer-settings.png)

Wewnątrz **ustawień konta**będzie można wyświetlić:
- **Szczegóły konta**: typ konta i stan konta
- **Identyfikatory wydawcy**: Identyfikator sprzedawcy, identyfikator użytkownika, identyfikator wydawcy, dzierżawy usługi Azure AD itd.
- **Informacje kontaktowe**: Nazwa wyświetlana wydawcy, nazwa kontaktu sprzedającego, adres e-mail, numer telefonu i adres
- **Identyfikatory GUID śledzenia**: wszystkie identyfikatory GUID śledzenia są skojarzone z Twoim kontem

### <a name="account-details"></a>Szczegóły konta

W sekcji Szczegóły konta można zobaczyć podstawowe informacje, takie jak **Typ konta** (firma lub osoba indywidualna) oraz **stan weryfikacji** konta. W ramach procesu weryfikacji konta zostaną wyświetlone wszystkie wymagane kroki, w tym Weryfikacja poczty e-mail, weryfikacja zatrudnienia i weryfikacja firmy. W tym miejscu możesz również zaktualizować swój adres e-mail i ponownie wysłać weryfikację, jeśli jest to konieczne.

### <a name="publisher-ids"></a>Identyfikatory wydawcy

W sekcji identyfikatorów wydawcy można zobaczyć **Identyfikator firmy Symantec**, **Identyfikator sprzedającego**, **Identyfikator użytkownika**, **identyfikator MPN**i **dzierżawy usługi Azure AD**. Te wartości są przypisywane przez firmę Microsoft w celu unikatowego identyfikowania Twojego konta dewelopera i nie można ich edytować.

### <a name="contact-info"></a>Informacje kontaktowe

W sekcji informacje kontaktowe zobaczysz **nazwę wyświetlaną wydawcy**, **informacje kontaktowe sprzedającego** (imię i nazwisko, adres e-mail, numer telefonu i adresy sprzedawcy firmy) oraz **osoby zatwierdzające przez firmę** (nazwisko, adres e-mail i numer telefonu osoby odpowiedzialnej za upoważnienie do zatwierdzania decyzji dla firmy).

#### <a name="payout-account"></a>Konto wypłaty

Konto wypłaty to konto bankowe, do którego są wysyłane transakcje z sprzedaży. To konto bankowe musi znajdować się w tym samym kraju/regionie, w którym zostało zarejestrowane konto Centrum partnerskiego.

Aby skonfigurować konto wypłaty:

1. Przejdź do [strony Przegląd komercyjnego portalu Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) w centrum partnerskim.
2. W sekcji profil obok pozycji **profil wypłaty**wybierz pozycję **Aktualizuj**.
3. **Wybierz formę płatności**: konto bankowe lub PayPal.
4. **Dodawanie informacji o płatności**: może to obejmować wybranie typu konta (sprawdzanie lub oszczędności), wprowadzenie nazwy, numeru konta i numeru rozliczeniowego, adres rozliczeniowy, numer telefonu lub adres E-mail w systemie PayPal. * Aby uzyskać więcej informacji na temat korzystania z systemu PayPal jako formy płatności konta i dowiedzieć się, czy jest ona obsługiwana w regionie rynku, zobacz [Informacje o systemie PayPal](https://docs.microsoft.com/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> Zmiana konta wypłaty może opóźniać płatności o jeden cykl płatności. To opóźnienie występuje, ponieważ musimy zweryfikować zmianę konta, tak jak w przypadku pierwszej konfiguracji konta wypłaty. Po zweryfikowaniu konta będziesz nadal otrzymywać opłaty za pełną kwotę. Wszelkie płatności z tytułu bieżącego cyklu płatności zostaną dodane do kolejnego.  

#### <a name="tax-profile"></a>Profil podatkowy

Przejrzyj bieżący stan profilu podatkowego, potwierdź, że jest wyświetlany prawidłowy **Typ jednostki** i **Informacje o certyfikacie podatkowym** . Wybierz pozycję **Edytuj** , aby zaktualizować lub ukończyć wszystkie wymagane formularze.

Aby ustalić swój stan podatkowy, należy określić kraj/region zamieszkania i obywatelstwo oraz wypełnić odpowiednie formularze podatkowe związane z Twoim krajem/regionem.

Niezależnie od kraju/regionu zamieszkania lub obywatelstwa, musisz wypełnić Stany Zjednoczone formularzy podatkowych, aby sprzedać oferty w firmie Microsoft. Partnerzy, którzy spełniają określone wymagania dotyczące Stany Zjednoczone miejsca zamieszkania, muszą wypełnić formularz urzędu skarbowego W postaci od-9. Inni partnerzy spoza Stany Zjednoczone muszą wypełnić formularz urzędu skarbowego W trybie do 8. Możesz wypełnić te formularze w trybie online, gdy dokończysz swój profil podatkowy.

Stany Zjednoczone indywidualny numer identyfikacyjny podatnika (lub ITIN) nie jest wymagany do otrzymywania płatności od firmy Microsoft ani do roszczeń wynikających z Traktatu podatkowego.

W centrum partnerskim można dokończyć i przesyłać formularze podatkowe elektronicznie. w większości przypadków nie ma potrzeby drukowania ani wysyłania żadnych formularzy.

Różne kraje i regiony mają różne wymagania podatkowe. Dokładna kwota, która musi zostać zapłacona podatkiem, zależy od krajów i regionów, w których sprzedawane są oferty. Firma Microsoft dokonuje sprzedaży i używa podatków w Twoim imieniu w niektórych krajach/regionach. Te kraje/regiony zostaną zidentyfikowane w procesie tworzenia oferty. W innych krajach/regionach, w zależności od tego, gdzie się rejestrujesz, może być konieczne przekazanie sprzedaży i użycie podatku za sprzedaż bezpośrednio do urzędu opodatkowania lokalnego. Ponadto otrzymane przychody mogą być opodatkowane jako dochód. Zdecydowanie zachęcamy do skontaktowania się z odpowiednim urzędem dla danego kraju lub regionu, który może pomóc w ustaleniu odpowiednich informacji podatkowych dla transakcji sprzedaży firmy Microsoft.

##### <a name="withholding-rates"></a>Stawki za potrącenie

Informacje przesyłane w formularzach podatkowych określają odpowiednią stawkę za potrącenie podatku. Wskaźnik potrącenia ma zastosowanie tylko do sprzedaży wprowadzonej do Stany Zjednoczone; sprzedaż dokonywana w lokalizacjach innych niż Stany USA nie podlega potrąceniu. Stawki zaliczania są różne, ale dla większości deweloperów rejestrowanych poza Stany Zjednoczone Domyślna stawka wynosi 30%. Istnieje możliwość zmniejszenia tej stawki, jeśli kraj/region wyraził zgodę na Traktat akcyzowy z Stany Zjednoczone.

##### <a name="tax-treaty-benefits"></a>Korzyści z Traktatu podatkowego

Jeśli nie masz Stany Zjednoczone, możesz skorzystać z korzyści z traktatów podatkowych. Te korzyści różnią się w zależności od kraju/regionu do kraju/regionu i mogą pozwalać zmniejszyć liczbę podatków rozpuszczalnych przez firmę Microsoft. Korzyści z Traktatu podatkowego można zgłaszać, wypełniając część II formularza W 8BEN. Zalecamy komunikację z odpowiednimi zasobami w Twoim kraju lub regionie, aby określić, czy te korzyści dotyczą użytkownika.

[Dowiedz się więcej o szczegółach dotyczących podatku dla deweloperów aplikacji/gier systemu Windows i wydawców portalu Azure Marketplace](https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps).

#### <a name="payout-hold-status"></a>Stan wstrzymania

Domyślnie firma Microsoft wysyła płatności miesięcznie. Można jednak opcjonalnie wprowadzić wypłaty, co uniemożliwi wysyłanie płatności do konta. Jeśli zdecydujesz się na wstrzymanie wypłatów, będziemy nadal rejestrować wszelkie dochody, które uzyskasz, i podaj szczegóły w **podsumowaniu wypłaty**. Nie będziemy jednak wysyłać żadnych płatności do konta do momentu usunięcia blokady.

Aby wstrzymać płatności, przejdź do pozycji **Ustawienia konta**. W obszarze **szczegóły finansowe**, w sekcji **stan wstrzymania** , przełącz suwak na wartość **wł**. W każdej chwili możesz zmienić status wypłaty, ale należy pamiętać, że decyzja wpłynie na następną miesięczną wypłatę. Na przykład jeśli chcesz wstrzymać wypłatę z kwietnia, pamiętaj, aby przed końcem marca ustawić stan wstrzymania na wartość **włączone** .

Po ustawieniu wypłaty stan Wstrzymaj na **wł**. wszystkie wypłaty zostaną wstrzymane do momentu przełączenia suwaka do trybu **wyłączone**. Gdy to zrobisz, zostanie uwzględniony w następnym cyklu miesięcznej wypłaty (w przypadku spełnienia wszelkich odpowiednich progów płatności). Na przykład jeśli wystąpiły wypłaty w dniu wstrzymania, ale chcesz wystawić wypłatę w czerwcu, upewnij się, że stan wstrzymania wypłaty jest **wyłączony** przed końcem maja.

> [!NOTE]
> **Wypłata** została wybrana z tytułu stanu ma zastosowanie do **wszystkich** źródeł przychodów, które są płatne za pomocą Centrum partnerskiego firmy Microsoft, w tym Azure Marketplace, AppSource, Microsoft Store, reklamy itp.). Dla każdego źródła przychodu nie można wybrać różnych stanów wstrzymania.

### <a name="devices"></a>Urządzenia

Ustawienia zarządzania urządzeniami dotyczą tylko publikowania platformy UWP. [Dowiedz się więcej](https://docs.microsoft.com/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

### <a name="tracking-guids"></a>Identyfikatory GUID śledzenia

Unikatowe identyfikatory globalne (GUIDs) są unikatowymi numerami odwołania (z 32 cyframi szesnastkowymi), które mogą być używane do śledzenia użycia platformy Azure. 

Aby utworzyć identyfikatory GUID do śledzenia, należy użyć generatora GUID. Zespół usługi Azure Storage utworzył [formularz generatora GUID](https://aka.ms/StoragePartners) , który wyśle wiadomość E-mail na identyfikator GUID prawidłowego formatu i może być ponownie używany w różnych systemach śledzenia.

Zalecamy utworzenie unikatowego identyfikatora GUID dla każdej oferty i kanału dystrybucji dla każdego produktu. Można wybrać opcję użycia jednego identyfikatora GUID dla wielu kanałów dystrybucji produktu, jeśli nie chcesz, aby raportowanie było podzielone.

Jeśli produkt zostanie wdrożony przy użyciu szablonu, który jest dostępny zarówno w portalu Azure Marketplace, jak i w witrynie GitHub, można utworzyć i zarejestrować dwa różne identyfikatory GUID:

- Produkt A w portalu Azure Marketplace
- Produkt A w serwisie GitHub

Raportowanie jest wykonywane przez wartość partnera (identyfikator partnera firmy Microsoft) i identyfikatory GUID. Możesz także śledzić identyfikatory GUID na bardziej szczegółowym poziomie wyrównanym do każdego planu w ramach oferty.

Aby uzyskać więcej informacji, zobacz [Śledzenie użycia klientów platformy Azure z identyfikatorami GUID często zadawanych pytań](../azure-partner-customer-usage-attribution.md#faq)i odpowiedzi.

## <a name="create-a-billing-profile"></a>Tworzenie profilu rozliczeń

Jeśli publikujesz [Dynamics 365 do zaangażowania klienta & aplikacje zaawansowane](./create-new-customer-engagement-offer.md) lub [Dynamics 365 for Operations](./create-new-operations-offer.md) , musisz ukończyć **profil rozliczeń**.

Adres rozliczeniowy jest wstępnie wypełniony przez firmę prawną i można go później zaktualizować. Pola podatek i Identyfikator VAT są opcjonalne.  Nie można edytować nazwy kraju/regionu i nazwy firmy.

## <a name="multi-user-account-management"></a>Zarządzanie kontami przez wiele użytkowników

Centrum partnerskie używa [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) do uzyskiwania dostępu do konta i zarządzania przez wiele użytkowników. Usługa Azure AD Twojej organizacji jest automatycznie skojarzona z kontem Centrum partnerskiego w ramach procesu rejestracji.

## <a name="manage-users"></a>Zarządzanie użytkownikami

Sekcja **Użytkownicy** Centrum partnerskiego (w obszarze **Ustawienia konta**) umożliwia zarządzanie użytkownikami, grupami i aplikacjami usługi Azure AD, które mają dostęp do Twojego konta Centrum partnerskiego za pomocą usługi Azure AD. Twoje konto musi mieć uprawnienia na [**poziomie Menedżera**](#define-user-roles-and-permissions) dla [konta służbowego (dzierżawy usługi Azure AD)](./company-work-accounts.md) , w którym chcesz dodać lub edytować użytkowników. Aby zarządzać użytkownikami w ramach innego konta służbowego/dzierżawy, musisz wylogować się, a następnie ponownie zalogować się jako użytkownik z uprawnieniami **Menedżera** dla tego konta służbowego/dzierżawy.

Po zalogowaniu się przy użyciu konta służbowego (dzierżawy usługi Azure AD) można:

- [Dodawanie lub usuwanie użytkowników](#add-existing-users)
- [Zmiana hasła użytkownika](#change-a-user-password)
- [Dodawanie lub usuwanie grup](#manage-groups)
- [Dodawanie lub usuwanie aplikacji usługi Azure AD](#add-new-azure-ad-applications)
- [Zarządzanie kluczami dla aplikacji usługi Azure AD](#manage-keys-for-an-azure-ad-application)
- [Definiowanie uprawnień i ról użytkowników](#define-user-roles-and-permissions)

Pamiętaj, że wszyscy użytkownicy Centrum partnerskiego (w tym grupy i aplikacje usługi Azure AD) muszą mieć aktywne konto służbowe w [dzierżawie usługi Azure AD](#manage-tenants) , która jest skojarzona z kontem Centrum partnerskiego.

### <a name="add-existing-users"></a>Dodaj istniejących użytkowników

Aby dodać użytkowników do konta Centrum partnerskiego, które już istnieje na [koncie służbowym firmy (dzierżawy usługi Azure AD)](./company-work-accounts.md):

1. Przejdź do pozycji **Użytkownicy** (w obszarze **Ustawienia konta**) i wybierz pozycję **Dodaj użytkowników**.
2. Wybierz co najmniej jednego użytkownika z wyświetlonej listy. Możesz użyć pola wyszukiwania, aby wyszukać określonych użytkowników.
* Jeśli wybierzesz więcej niż jednego użytkownika do dodania do konta Centrum partnerskiego, musisz przypisać im tę samą rolę lub zestaw uprawnień niestandardowych. Aby dodać wielu użytkowników z różnymi rolami/uprawnieniami, Powtórz te kroki dla każdej roli lub zestawu uprawnień niestandardowych.
3. Po zakończeniu wybierania użytkowników wybierz pozycję **Dodaj wybrane**.
4. W sekcji **role** Określ role lub dostosowane uprawnienia dla wybranych użytkowników.
5. Wybierz pozycję **Zapisz**.

### <a name="create-new-users"></a>Utwórz nowych użytkowników

Aby utworzyć nowe konta użytkowników, musisz mieć konto z uprawnieniami [**administratora globalnego**](../../active-directory/roles/permissions-reference.md) .

1. Przejdź do pozycji **Użytkownicy** (w obszarze **Ustawienia konta**), wybierz pozycję **Dodaj użytkowników**, a następnie wybierz pozycję **Utwórz nowych użytkowników**.
1. Wprowadź imię i nazwisko oraz nazwę użytkownika dla każdego nowego użytkownika. 
1. Jeśli chcesz, aby nowy użytkownik miał konto administratora globalnego w katalogu organizacji, zaznacz pole wyboru nadaj **temu użytkownikowi uprawnienia administratora globalnego w usłudze Azure AD, mając pełną kontrolę nad wszystkimi zasobami katalogu**. Dzięki temu użytkownik będzie miał pełny dostęp do wszystkich funkcji administracyjnych w usłudze Azure AD Twojej firmy. Będą oni mogli dodawać użytkowników i zarządzać nimi w ramach konta służbowego organizacji (dzierżawy usługi Azure AD), ale nie w centrum partnerskim, chyba że zostanie przyznane konto odpowiedniej roli/uprawnień.
1. Jeśli zaznaczono pole wyboru **tego użytkownika jako administratora globalnego**, należy podać **wiadomość e-mail z odzyskiwaniem hasła** , aby użytkownik mógł odzyskać swoje hasło w razie potrzeby.
1. W sekcji **członkostwo w grupie** wybierz wszystkie grupy, do których ma należeć nowy użytkownik.
1. W sekcji **role** Określ role lub dostosowane uprawnienia dla użytkownika.
1. Wybierz pozycję **Zapisz**.

Utworzenie nowego użytkownika w centrum partnerskim spowoduje również utworzenie konta dla tego użytkownika w ramach konta służbowego (dzierżawy usługi Azure AD), z którym użytkownik jest zalogowany. Wprowadzenie zmian w nazwie użytkownika w centrum partnerskim spowoduje takie same zmiany w ramach konta służbowego organizacji (dzierżawy usługi Azure AD).

### <a name="invite-new-users-by-email"></a>Zaproś nowych użytkowników pocztą e-mail

Aby zapraszać użytkowników, którzy nie są obecnie częścią firmowego konta służbowego (Azure AD dzierżawcą) za pośrednictwem poczty e-mail, musisz mieć konto z uprawnieniami [**administratora globalnego**](../../active-directory/roles/permissions-reference.md) .

1. Przejdź do pozycji **Użytkownicy** (w obszarze **Ustawienia konta**), wybierz pozycję **Dodaj użytkowników**, a następnie wybierz opcję **Zapraszanie użytkowników za pośrednictwem poczty e-mail**.
2. Wprowadź co najmniej jeden adres e-mail (do 10), rozdzielony przecinkami lub średnikami.
3. W sekcji **role** Określ role lub dostosowane uprawnienia dla użytkownika.
4. Wybierz pozycję **Zapisz**.

Zaproszeni użytkownicy otrzymają wiadomość e-mail z zaproszeniem do wzięcia udziału w Twoim koncie Centrum partnerskiego. Nowe konto użytkownika Gość zostanie utworzone na koncie służbowym (dzierżawy usługi Azure AD). Każdy użytkownik będzie musiał zaakceptować zaproszenie przed uzyskaniem dostępu do konta.

Jeśli musisz ponownie wysłać zaproszenie, odwiedź stronę **Użytkownicy** i Znajdź zaproszenie na liście użytkowników, wybierz swój adres e-mail (lub tekst mówiący *Oczekujące zaproszenia*). Następnie w dolnej części strony wybierz pozycję **Wyślij ponownie zaproszenie**.

> [!NOTE]
> Jeśli Twoja organizacja używa [integracji katalogu](https://go.microsoft.com/fwlink/p/?LinkID=724033) do synchronizowania lokalnej usługi katalogowej z usługą Azure AD, nie będziesz w stanie tworzyć nowych użytkowników, grup ani aplikacji usługi Azure AD w centrum partnerskim. Użytkownik (lub inny administrator w katalogu lokalnym) będzie musiał je utworzyć bezpośrednio w katalogu lokalnym, aby można było je zobaczyć i dodać do Centrum partnerskiego.

### <a name="remove-a-user"></a>Usuwanie użytkownika

Aby usunąć użytkownika z konta służbowego (dzierżawy usługi Azure AD), przejdź do pozycji **Użytkownicy** (w obszarze **Ustawienia konta**), wybierz użytkownika, który chcesz usunąć, przy użyciu pola wyboru w prawej kolumnie, a następnie wybierz pozycję **Usuń** z dostępnych akcji. Zostanie wyświetlone okno podręczne, aby potwierdzić, że chcesz usunąć wybranych użytkowników.

### <a name="change-a-user-password"></a>Zmiana hasła użytkownika

Jeśli jeden z użytkowników musi zmienić hasło, może to zrobić, jeśli podczas tworzenia konta użytkownika podano **wiadomość e-mail z odzyskiwaniem haseł** . Możesz również zaktualizować hasło użytkownika, wykonując poniższe kroki. Aby zmienić hasło użytkownika na koncie służbowym firmy (dzierżawy usługi Azure AD), użytkownik musi być zalogowany na koncie z uprawnieniami [**administratora globalnego**](../../active-directory/roles/permissions-reference.md) . Spowoduje to zmianę hasła użytkownika w dzierżawie usługi Azure AD wraz z hasłem używanym do uzyskiwania dostępu do Centrum partnerskiego.

1. Na stronie **Użytkownicy** (w obszarze **Ustawienia konta**) wybierz nazwę konta użytkownika, które chcesz edytować.
2. Wybierz przycisk **resetowania hasła** w dolnej części strony.
3. Zostanie wyświetlona strona potwierdzenia z informacjami logowania użytkownika, w tym hasła tymczasowego. Pamiętaj, aby wydrukować lub skopiować te informacje i udostępnić je użytkownikowi, ponieważ nie będzie można uzyskać dostępu do hasła tymczasowego po opuszczeniu tej strony.

## <a name="manage-groups"></a>Zarządzanie grupami

Grupy umożliwiają jednoczesne kontrolowanie wielu ról użytkowników i uprawnień.

### <a name="add-an-existing-group"></a>Dodaj istniejącą grupę

Aby dodać grupę, która już istnieje w ramach konta służbowego organizacji (dzierżawy usługi Azure AD) do konta Centrum partnerskiego:

1. Na stronie **Użytkownicy** (w obszarze **Ustawienia konta**) wybierz pozycję **Dodaj grupy**.
2. Wybierz co najmniej jedną grupę z wyświetlonej listy. Możesz użyć pola wyszukiwania do wyszukiwania określonych grup.
W przypadku wybrania więcej niż jednej grupy do dodania do konta Centrum partnerskiego należy przypisać im tę samą rolę lub zestaw uprawnień niestandardowych. Aby dodać wiele grup z różnymi rolami/uprawnieniami, Powtórz te kroki dla każdej roli lub zestawu uprawnień niestandardowych.
3. Po zakończeniu wybierania grup wybierz pozycję **Dodaj wybrane**.
4. W sekcji **role** Określ role lub dostosowane uprawnienia dla wybranych grup. Wszyscy członkowie grupy będą mogli uzyskiwać dostęp do konta Centrum partnerskiego z uprawnieniami, które są stosowane do grupy, niezależnie od ról i uprawnień skojarzonych z ich indywidualnym kontem.
5. Wybierz pozycję **Zapisz**.

Po dodaniu istniejącej grupy Każdy użytkownik, który jest członkiem tej grupy, będzie mógł uzyskać dostęp do konta Centrum partnerskiego z uprawnieniami skojarzonymi z rolą przypisanej do grupy.

### <a name="add-a-new-group"></a>Dodawanie nowej grupy

Aby dodać nową markę do konta Centrum partnerskiego:

1. Na stronie **Użytkownicy** (w obszarze **Ustawienia konta**) wybierz pozycję **Dodaj grupy**.
2. Na następnej stronie wybierz pozycję **Nowa grupa**.
3. Wprowadź nazwę wyświetlaną dla nowej grupy.
4. Określ role lub dostosowane uprawnienia dla grupy. Wszyscy członkowie grupy będą mogli uzyskiwać dostęp do konta Centrum partnerskiego z uprawnieniami, które są stosowane w tym miejscu, niezależnie od ról/uprawnień skojarzonych z ich indywidualnym kontem.
5. Z wyświetlonej listy wybierz użytkowników dla nowej grupy. Możesz użyć pola wyszukiwania, aby wyszukać określonych użytkowników.
6. Po zakończeniu wybierania użytkowników wybierz pozycję **Dodaj wybrane** , aby dodać je do nowej grupy.
7. Wybierz pozycję **Zapisz**.

Nowa grupa zostanie utworzona w ramach konta służbowego organizacji (dzierżawy usługi Azure AD), a nie tylko na koncie Centrum partnerskiego.

### <a name="remove-a-group"></a>Usuwanie grupy

Aby usunąć grupę z konta służbowego (dzierżawy usługi Azure AD), przejdź do pozycji **Użytkownicy** (w obszarze **Ustawienia konta**), wybierz grupę, którą chcesz usunąć, przy użyciu pola wyboru w prawej kolumnie, a następnie wybierz pozycję **Usuń** z dostępnych akcji. Zostanie wyświetlone okno podręczne, aby potwierdzić, że chcesz usunąć wybrane grupy.

## <a name="manage-azure-ad-applications"></a>Zarządzanie aplikacjami usługi Azure AD

Możesz zezwolić aplikacjom lub usługom należącym do usługi Azure AD Twojej firmy na dostęp do konta Centrum partnerskiego.

### <a name="add-existing-azure-ad-applications"></a>Dodawanie istniejących aplikacji usługi Azure AD

Aby dodać aplikacje, które już istnieją w Azure Active Directory firmy:

1. Na stronie **Użytkownicy** (w obszarze **Ustawienia konta**) wybierz pozycję **Dodaj aplikacje usługi Azure AD**.
2. Wybierz co najmniej jedną aplikację usługi Azure AD z wyświetlonej listy. Możesz użyć pola wyszukiwania, aby wyszukać określone aplikacje usługi Azure AD. W przypadku wybrania więcej niż jednej aplikacji usługi Azure AD, która ma zostać dodana do konta Centrum partnerskiego, należy przypisać im tę samą rolę lub zestaw uprawnień niestandardowych. Aby dodać wiele aplikacji usługi Azure AD z różnymi rolami/uprawnieniami, Powtórz te kroki dla każdej roli lub zestawu uprawnień niestandardowych.
3. Po zakończeniu wybierania aplikacji usługi Azure AD wybierz pozycję **Dodaj wybrane**.
4. W sekcji **role** Określ role lub dostosowane uprawnienia dla wybranych aplikacji usługi Azure AD.
5. Wybierz pozycję **Zapisz**.

### <a name="add-new-azure-ad-applications"></a>Dodawanie nowych aplikacji usługi Azure AD

Jeśli chcesz udzielić Centrum partnerskiego dostępu do nowego konta aplikacji usługi Azure AD, możesz je utworzyć w sekcji **Użytkownicy** . Spowoduje to utworzenie nowego konta na koncie służbowym firmy (dzierżawy usługi Azure AD), a nie tylko na koncie Centrum partnerskiego. Jeśli korzystasz głównie z tej aplikacji usługi Azure AD na potrzeby uwierzytelniania w centrum partnerskim i nie potrzebujesz dostępu do nich bezpośrednio, możesz wprowadzić dowolny prawidłowy adres **URL odpowiedzi** i **Identyfikator URI identyfikatora aplikacji**, o ile te wartości nie są używane przez żadną inną aplikację usługi Azure AD w katalogu.

1. Na stronie **Użytkownicy** (w obszarze **Ustawienia konta**) wybierz pozycję **Dodaj aplikacje usługi Azure AD**.
2. Na następnej stronie wybierz pozycję **Nowa aplikacja usługi Azure AD**.
3. Wprowadź **adres URL odpowiedzi** dla nowej aplikacji usługi Azure AD. Jest to adres URL, pod którym użytkownicy mogą się zalogować i korzystać z aplikacji usługi Azure AD (czasami znanego również jako adres URL aplikacji lub adres URL Sign-On). **Adres URL odpowiedzi** nie może mieć więcej niż 256 znaków i musi być unikatowy w obrębie katalogu.
4. Wprowadź **Identyfikator URI aplikacji** dla nowej aplikacji usługi Azure AD. Jest to identyfikator logiczny dla aplikacji usługi Azure AD, który jest prezentowany podczas wysyłania żądania logowania jednokrotnego do usługi Azure AD. **Identyfikator URI identyfikatora aplikacji** musi być unikatowy dla każdej aplikacji usługi Azure AD w katalogu. Ten identyfikator nie może zawierać więcej niż 256 znaków. Aby uzyskać więcej informacji o IDENTYFIKATORze URI aplikacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](../../active-directory/develop/quickstart-modify-supported-accounts.md#change-the-application-registration-to-support-different-accounts)).
5. W sekcji **role** Określ role lub dostosowane uprawnienia dla aplikacji usługi Azure AD.
6. Wybierz pozycję **Zapisz**.

Po dodaniu lub utworzeniu aplikacji usługi Azure AD możesz powrócić do sekcji **Użytkownicy** i wybrać nazwę aplikacji, aby przejrzeć ustawienia aplikacji, w tym identyfikator dzierżawy, identyfikator klienta, adres URL odpowiedzi i identyfikator URI aplikacji.

### <a name="remove-an-azure-ad-application"></a>Usuwanie aplikacji usługi Azure AD

Aby usunąć aplikację z konta służbowego (dzierżawy usługi Azure AD), przejdź do pozycji **Użytkownicy** (w obszarze **Ustawienia konta**), wybierz aplikację, którą chcesz usunąć, przy użyciu pola wyboru w prawej kolumnie, a następnie wybierz pozycję **Usuń** z dostępnych akcji. Zostanie wyświetlone okno podręczne, aby potwierdzić, że chcesz usunąć wybrane aplikacje.

### <a name="manage-keys-for-an-azure-ad-application"></a>Zarządzanie kluczami dla aplikacji usługi Azure AD

Jeśli aplikacja usługi Azure AD odczytuje i zapisuje dane w Microsoft Azure AD, będzie potrzebny klucz. Możesz utworzyć klucze dla aplikacji usługi Azure AD, edytując jej informacje w centrum partnerskim. Możesz również usunąć klucze, które nie są już potrzebne.

1. Na stronie **Użytkownicy** (w obszarze **Ustawienia konta**) wybierz nazwę aplikacji usługi Azure AD. Zobaczysz wszystkie aktywne klucze dla aplikacji usługi Azure AD, w tym datę utworzenia klucza i czas wygaśnięcia 50.
2. Aby usunąć klucz, który nie jest już wymagany, wybierz pozycję **Usuń**.
3. Aby dodać nowy klucz, wybierz pozycję **Dodaj nowy klucz**.
4. Zobaczysz ekran pokazujący **Identyfikator klienta** i **wartości klucza**. Pamiętaj, aby wydrukować lub skopiować te informacje, ponieważ nie będzie można uzyskać do niej dostępu, gdy opuścisz Tę stronę.
5. Jeśli chcesz utworzyć więcej kluczy, wybierz pozycję **Dodaj inny klucz**.

## <a name="define-user-roles-and-permissions"></a>Definiowanie uprawnień i ról użytkowników

Użytkownikom firmy można przypisać następujące role i uprawnienia do komercyjnego programu Marketplace w centrum partnerskim:

- **Właściciel**
  - "Jest to osoba, która po raz pierwszy utworzyła konto i ma do niego pełny dostęp, w tym możliwość tworzenia i edytowania wszystkich użytkowników konta oraz zmiany wszystkich ustawień finansowych i kont. Konto ma zwykle tylko jednego właściciela ".
- **Manager**
  - Może uzyskać dostęp do wszystkich funkcji konto Microsoft oprócz ustawień podatków i wypłat
  - Może zarządzać użytkownikami, rolami i kontami służbowymi (dzierżawcami)
- **Deweloper**
  - Może zarządzać ofertami i publikować je
  - Może wyświetlać raporty wydawcy

> [!NOTE]
> W przypadku programu komercyjnego portalu Marketplace nie są używane role Administrator globalny, współautor biznesowa, współautor finansowy i rynek. Przypisanie tych ról do użytkowników nie ma żadnego wpływu. Tylko role Menedżer i deweloper udzielą uprawnień użytkownikom.

Aby uzyskać więcej informacji na temat zarządzania rolami i uprawnieniami w innych obszarach Centrum partnerskiego, takich jak Azure Active Directory (AD), dostawca rozwiązań w chmurze (CSP), dostawca panelu sterowania (CPV), użytkownicy-Goście lub Microsoft Partner Network (MPN), zobacz [Przypisywanie ról i uprawnień użytkowników w centrum partnerskim](https://docs.microsoft.com/partner-center/permissions-overview).

## <a name="manage-tenants"></a>Zarządzanie dzierżawcami

Dzierżawa usługi Azure Active Directory (AD), nazywana również "kontem służbowym" w tej dokumentacji, stanowi reprezentację organizacji skonfigurowanej w Azure Portal i pomaga zarządzać określonym wystąpieniem usług w chmurze firmy Microsoft dla użytkowników wewnętrznych i zewnętrznych. Jeśli Twoja organizacja subskrybuje usługę firmy Microsoft w chmurze, taką jak Azure, Microsoft Intune lub Microsoft 365, została dla Ciebie ustanowiona dzierżawa usługi Azure AD.

Można skonfigurować wiele dzierżawców do użycia z centrum partnerskim. Każdy użytkownik z rolą **Menedżera** na koncie Centrum partnerskiego będzie miał możliwość dodawania i usuwania dzierżaw usługi Azure AD z konta.  

### <a name="add-an-existing-tenant"></a>Dodaj istniejącą dzierżawę

Aby skojarzyć kolejną dzierżawę usługi Azure AD z kontem Centrum partnerskiego:

1. Na stronie **dzierżawców** (w obszarze **Ustawienia konta**) wybierz pozycję **Skojarz inną dzierżawę usługi Azure AD**.
2. Wprowadź swoje poświadczenia usługi Azure AD dla dzierżawy, którą chcesz skojarzyć.
3. Przejrzyj nazwę organizacji i domenę dzierżawy usługi Azure AD. Aby zakończyć skojarzenie, wybierz pozycję **Potwierdź**.

Jeśli skojarzenie zakończy się pomyślnie, będziesz gotowy do dodawania użytkowników konta i zarządzania nimi w sekcji **Użytkownicy** w centrum partnerskim.

### <a name="create-a-new-tenant"></a>Tworzenie nowej dzierżawy

Aby utworzyć nową dzierżawę usługi Azure AD przy użyciu konta Centrum partnerskiego:

1. Na stronie **dzierżawców** (w obszarze **Ustawienia konta**) wybierz pozycję **Utwórz nową dzierżawę usługi Azure AD**.
2. Wprowadź informacje o katalogu dla nowej usługi Azure AD:
    - **Nazwa domeny**: unikatowa nazwa, która będzie używana dla domeny usługi Azure AD wraz z ". onmicrosoft.com". Na przykład, jeśli wprowadzono "przykład", domena usługi Azure AD będzie "example.onmicrosoft.com".
    - **Kontaktowy adres e-mail**: w razie potrzeby można skontaktować się z Tobą w sprawie Twojego konta.
    - **Informacje o koncie użytkownika administratora globalnego**: imię i nazwisko, nazwisko, nazwę użytkownika i hasło, które mają być używane dla nowego konta administratora globalnego.
3. Wybierz pozycję **Utwórz** , aby potwierdzić nowe informacje o domenie i koncie.
4. Zaloguj się przy użyciu nowej nazwy użytkownika i hasła administratora globalnego usługi Azure AD, aby rozpocząć [Dodawanie użytkowników i zarządzanie nimi](#manage-users).

Aby uzyskać więcej informacji na temat tworzenia nowych dzierżawców w ramach Azure Portal, a nie za pośrednictwem portalu Centrum partnerskiego, zobacz artykuł [Tworzenie nowej dzierżawy w Azure Active Directory](../../active-directory/fundamentals/active-directory-access-create-new-tenant.md).

### <a name="remove-a-tenant"></a>Usuwanie dzierżawy

Aby usunąć dzierżawcę z konta Centrum partnerskiego, Znajdź jego nazwę na stronie **dzierżawców** (w obszarze **Ustawienia konta**), a następnie wybierz pozycję **Usuń**. Zostanie wyświetlony monit o potwierdzenie, że chcesz usunąć dzierżawcę. Po wykonaniu tej czynności żaden użytkownik w tej dzierżawie nie będzie mógł zalogować się do konta usługi Partner Center, a wszystkie uprawnienia skonfigurowane dla tych użytkowników zostaną usunięte.

Po usunięciu dzierżawy wszyscy użytkownicy, którzy zostali dodani do konta Centrum partnerskiego z tej dzierżawy, nie będą już mogli się zalogować na koncie.

> [!TIP]
> Dzierżawy nie można usunąć, jeśli użytkownik jest obecnie zalogowany do Centrum partnerskiego przy użyciu konta w tej samej dzierżawie. Aby usunąć dzierżawcę, musisz zalogować się do Centrum partnerskiego jako **Menedżera** dla innej dzierżawy skojarzonej z tym kontem. Jeśli istnieje tylko jedna dzierżawa skojarzona z kontem, dzierżawa może zostać usunięta tylko po zalogowaniu się przy użyciu konto Microsoft, który otworzył konto.

## <a name="agreements"></a>Umowy

Sekcja **umów** Centrum partnerskiego (w obszarze **Ustawienia konta**) umożliwia wyświetlenie listy zatwierdzonych umów dotyczących publikacji. Umowy te są wyświetlane zgodnie z nazwą i numerem wersji, włącznie z datą zaakceptowania i nazwą użytkownika, który zaakceptował umowę.

**Wymagane akcje** mogą pojawić się u góry tej strony, jeśli istnieją aktualizacje umów, które wymagają Twojej uwagi. Aby zaakceptować zaktualizowaną umowę, najpierw Przeczytaj połączoną wersję umowy, a następnie wybierz pozycję **Zaakceptuj umowę**.
