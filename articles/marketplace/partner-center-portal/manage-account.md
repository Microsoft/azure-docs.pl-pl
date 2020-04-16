---
title: Jak zarządzać kontem portalu komercyjnego w centrum partnerów firmy Microsoft
description: Dowiedz się, jak zarządzać kontem komercyjnego portalu Marketplace w Centrum partnerów firmy Microsoft.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 256ea1a03cd22df443362623177f91893f28b664
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419157"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>Zarządzanie kontem w portalu marketplace komercyjnego w Centrum partnerskim

Po [utworzeniu konta Centrum partnerów](./create-account.md)możesz zarządzać kontem i ofertami za pomocą [komercyjnego pulpitu nawigacyjnego.](https://partner.microsoft.com/dashboard/commercial-marketplace/overview)

W tym artykule przyjrzymy się, jak zarządzać kontem Centrum partnerów, w tym jak:

- [Dostęp do ustawień konta Centrum partnerów](#access-your-account-settings)
- [Znajdowanie identyfikatora wydawcy, identyfikatora firmy Symantec, identyfikatora sprzedawcy, identyfikatora użytkownika, identyfikatora MPN i dzierżawy usługi Azure AD](#account-details)
- [Aktualizowanie informacji kontaktowych](#contact-info)
- [Konfigurowanie identyfikatorów GUID śledzenia do monitorowania użycia klienta](#tracking-guids)
- [Zarządzaj użytkownikami](#manage-users)
- [Zarządzanie grupami](#manage-groups)
- [Zarządzanie aplikacjami usługi Azure AD](#manage-azure-ad-applications)
- [Definiowanie uprawnień i ról użytkowników](#define-user-roles-and-permissions)
- [Zarządzanie dzierżawami usługi Azure AD (kontami roboczymi)](#manage-tenants)
- [Zarządzanie umowami z centrum partnerskiego](#agreements)

## <a name="access-your-account-settings"></a>Dostęp do ustawień konta

Jeśli jeszcze tego nie zrobiłeś, Ty (lub administrator organizacji) powinieneś uzyskać dostęp do [ustawień konta](https://partner.microsoft.com/dashboard/account/management) Centrum partnerów, aby:
- Sprawdzanie statusu weryfikacji konta firmy
- Potwierdź swój identyfikator firmy Symantec, identyfikator sprzedawcy, identyfikator sieci partnerów firmy Microsoft (MPN), identyfikator wydawcy i informacje kontaktowe, w tym osobę zatwierdzaącą i kontakt ze sprzedawcą
- Tworzenie kont użytkowników dla wszystkich osób, które będą korzystać z Twojego konta firmowego w Centrum partnerów

### <a name="open-developer-settings"></a>Otwieranie ustawień dewelopera

**Ustawienia konta** znajdują się w prawym górnym rogu [pulpitu nawigacyjnego rynku komercyjnego](https://partner.microsoft.com/dashboard/commercial-marketplace) w Centrum partnerów. Wybierz ikonę koła zębatego (w prawym górnym rogu pulpitu nawigacyjnego), a następnie wybierz **pozycję Ustawienia dewelopera**.

![Menu Ustawienia konta w Centrum partnerów](./media/dashboard-developer-settings.png)

Wewnątrz **ustawień konta,** będzie można wyświetlić:
- **Szczegóły konta**: Typ konta i stan konta
- **Identyfikatory wydawców: identyfikator sprzedawcy,** identyfikator użytkownika, identyfikator wydawcy, dzierżawy usługi Azure AD itp.
- **Informacje kontaktowe**: Nazwa wyświetlana wydawcy, nazwa kontaktu sprzedającego, adres e-mail, telefon i adres
- **Śledzenie identyfikatorów GUID:** wszelkie identyfikatory GUID śledzenia kojarzą się z Twoim kontem

### <a name="account-details"></a>Szczegóły konta

W sekcji Szczegóły konta możesz zobaczyć podstawowe informacje, takie jak **typ konta** (firma lub osoba) oraz **stan weryfikacji** konta. Podczas procesu weryfikacji konta ustawienia te będą wyświetlać każdy wymagany krok, w tym weryfikację poczty e-mail, weryfikację zatrudnienia i weryfikację biznesową. Możesz również zaktualizować swój adres e-mail tutaj i wysłać ponownie weryfikację, jeśli zajdzie taka potrzeba.

### <a name="publisher-ids"></a>Identyfikatory wydawców

W sekcji Identyfikatory wydawców możesz zobaczyć swój **identyfikator Symantec,** identyfikator **sprzedawcy,** **identyfikator użytkownika,** **identyfikator MPN**i **dzierżawę usługi Azure AD.** Te wartości są przypisywane przez firmę Microsoft do jednoznacznej identyfikacji konta dewelopera i nie można ich edytować.

### <a name="contact-info"></a>Informacje kontaktowe

W sekcji Informacje kontaktowe możesz wyświetlić **nazwę wyświetlaną wydawcy,** **informacje kontaktowe sprzedającego** (imię i nazwisko kontaktu, adres e-mail, numer telefonu i adres sprzedawcy firmy) oraz **osobę zatwierdzaącą firmę** (imię i nazwisko, adres e-mail i numer telefonu osoby posiadającej uprawnienia do zatwierdzania decyzji dla firmy).

#### <a name="payout-account"></a>Konto wypłat

Konto wypłat to konto bankowe, na które wysyłane są wpływy ze sprzedaży. To konto bankowe musi znajdować się w tym samym kraju, w którym zarejestrowano konto Centrum partnerów.

Aby skonfigurować konto wypłat, musisz **skojarzyć swoje konto Microsoft:**
1. Przejdź do [strony przeglądu rynku komercyjnego](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) w Centrum partnerów.
2. W sekcji Profil wybierz pozycję **Skojarz swoje konto Microsoft**.
3. Po wyświetleniu monitu zaloguj się za pomocą konta Microsoft (MSA). To konto nie może być już skojarzone z innym kontem Centrum partnerów.
4. Aby ukończyć konfigurację konta wypłat, wyloguj się z Centrum partnerów, a następnie zaloguj się z powrotem za pomocą konta Microsoft (a nie konta służbowego).

Teraz, gdy konto Microsoft jest skojarzone, aby dodać konto wypłat, musisz:
- **Wybierz metodę płatności**: Konto bankowe lub PayPal
- **Dodaj informacje o płatności:** Może to obejmować wybór typu konta (sprawdzanie lub oszczędności), wprowadzenie nazwy właściciela konta, numeru konta i numeru rozliczeniowego, adresu rozliczeniowego, numeru telefonu lub adresu e-mail PayPal. *Aby uzyskać więcej informacji na temat korzystania z systemu PayPal jako metody płatności na koncie i dowiedzieć się, czy jest on obsługiwany w regionie rynku, zobacz [informacje o systemie PayPal](https://docs.microsoft.com/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> Zmiana konta wypłat może opóźnić płatności nawet o jeden cykl płatności. To opóźnienie występuje, ponieważ musimy zweryfikować zmianę konta, tak jak robimy to przy pierwszym skonfigurowaniu konta wypłat. Po zweryfikowaniu konta nadal otrzymasz zapłatę za pełną kwotę; wszelkie płatności należne za bieżący cykl płatności zostaną dodane do następnego.  

#### <a name="tax-profile"></a>Profil podatkowy

Przejrzyj bieżący status profilu podatkowego, potwierdzając prawidłowy **typ jednostki** i **informacje o certyfikacie podatkowym.** Wybierz **edytuj,** aby zaktualizować lub wypełnić wszystkie wymagane formularze.

Aby ustalić swój status podatkowy, musisz określić swój kraj zamieszkania i obywatelstwo oraz wypełnić odpowiednie formularze podatkowe związane z Twoim krajem/regionem.

Niezależnie od kraju zamieszkania lub obywatelstwa, musisz wypełnić formularze podatkowe Stanów Zjednoczonych, aby sprzedawać wszelkie oferty za pośrednictwem firmy Microsoft. Partnerzy, którzy spełniają określone wymagania dotyczące pobytu w Stanach Zjednoczonych, muszą wypełnić formularz IRS W-9. Inni partnerzy spoza Stanów Zjednoczonych muszą wypełnić formularz IRS W-8. Formularze te można wypełniać online podczas wypełniania profilu podatkowego.

Indywidualny numer identyfikacyjny podatnika (ITIN) stanów Zjednoczonych nie jest wymagany do otrzymywania płatności od firmy Microsoft ani do ubiegania się o korzyści wynikające z umowy podatkowej.

Formularze podatkowe można wypełniać i przesyłać drogą elektroniczną w Centrum partnerskim; w większości przypadków nie trzeba drukować i wysyłać żadnych formularzy.

Różne kraje i regiony mają różne wymogi podatkowe. Dokładna kwota, którą musisz zapłacić w podatkach, zależy od krajów i regionów, w których sprzedajesz swoje oferty. Firma Microsoft przekazuje sprzedaż i stosuje podatek w imieniu użytkownika w niektórych krajach. Kraje te zostaną zidentyfikowane w trakcie wystawiania oferty. W innych krajach, w zależności od miejsca rejestracji, może być konieczne przełęczenie podatku od sprzedaży bezpośrednio do lokalnego organu podatkowego. Ponadto uzyskane przychody ze sprzedaży mogą podlegać opodatkowaniu jako przychód. Zdecydowanie zachęcamy do skontaktowania się z odpowiednim organem dla danego kraju lub regionu, który może pomóc w określeniu właściwych informacji podatkowych dotyczących transakcji sprzedaży firmy Microsoft.

##### <a name="withholding-rates"></a>Stawki u źródła
Informacje przesyłane w formularzach podatkowych określają odpowiednią stawkę potrąconą zaliczką na podatek. Stawka potrącona z potrącenia dotyczy tylko sprzedaży, którą dokonujesz w Stanach Zjednoczonych; sprzedaży w placówkach spoza USA nie podlegają potrąceniu. Stawki u źródła są różne, ale w przypadku większości programistów rejestrujących się poza Stanami Zjednoczonymi domyślny wskaźnik wynosi 30%. Masz możliwość obniżenia tej stawki, jeśli twój kraj zgodził się na umowę o podatku dochodowym ze Stanami Zjednoczonymi.

##### <a name="tax-treaty-benefits"></a>Korzyści z umowy podatkowej
Jeśli jesteś poza Stanami Zjednoczonymi, możesz skorzystać z korzyści wynikających z umowy podatkowej. Korzyści te różnią się w zależności od kraju i mogą umożliwiać zmniejszenie kwoty podatków, które firma Microsoft potrącana jest z podatku. Możesz ubiegać się o korzyści z umowy podatkowej, wypełniając część II formularza W-8BEN. Zalecamy komunikowanie się z odpowiednimi zasobami w twoim kraju lub regionie, aby ustalić, czy te korzyści mają zastosowanie do Ciebie.

[Dowiedz się więcej o szczegółach podatkowych dla deweloperów aplikacji/gier systemu Windows i wydawców portalu Azure Marketplace.](https://docs.microsoft.com/windows/uwp/publish/tax-details-for-paid-apps)

#### <a name="payout-hold-status"></a>Status wstrzymania wypłaty

Domyślnie firma Microsoft wysyła płatności co miesiąc. Masz jednak możliwość wstrzymania wypłat, co uniemożliwi wysyłanie płatności na Twoje konto. Jeśli zdecydujesz się zawiesić wypłaty, będziemy nadal rejestrować wszelkie przychody, które zarabiasz i podać szczegóły w **podsumowaniu wypłat.** Nie będziemy jednak wysyłać żadnych płatności na Twoje konto, dopóki nie usuniesz blokady. 

Aby wstrzymać płatności, przejdź do **pozycji Ustawienia konta**. W **obszarze Szczegóły finansowe**w sekcji Stan **wstrzymania wypłaty** przełącz suwak na **Włączone**. Możesz zmienić status blokady wypłaty w dowolnym momencie, ale pamiętaj, że Twoja decyzja wpłynie na następną miesięczną wypłatę. Na przykład, jeśli chcesz zatrzymać kwietniową wypłatę, upewnij się, że ustawisz status wstrzymania wypłaty **na On** przed końcem marca.

Po ustawieniu statusu wstrzymania wypłaty **na On**wszystkie wypłaty zostaną wstrzymane, dopóki nie przełączysz suwaka z powrotem na **Wyłączone.** Gdy to zrobisz, zostaniesz uwzględniony podczas następnego miesięcznego cyklu wypłat (pod warunkiem, że zostaną spełnione wszystkie obowiązujące progi płatności). Na przykład, jeśli masz wstrzymane wypłaty, ale chcesz mieć wypłatę wygenerowaną w czerwcu, upewnij się, że przełącz stan wstrzymania wypłaty na **Wyłączone** przed końcem maja.

> [!NOTE]
> Wybór **statusu wstrzymania wypłat** dotyczy **wszystkich** źródeł przychodów, które są wypłacane za pośrednictwem Centrum partnerów firmy Microsoft, w tym usługi Azure Marketplace, AppSource, Microsoft Store, reklam itp.). Nie można wybrać różnych stanów wstrzymania dla każdego źródła przychodów.

### <a name="devices"></a>Urządzenia

Ustawienia zarządzania urządzeniami dotyczą tylko publikowania platformy uniwersalnej systemu Windows. [Dowiedz się więcej](https://docs.microsoft.com/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

### <a name="tracking-guids"></a>Śledzenie identyfikatorów GUID

Globalnie unikatowe identyfikatory (GUID) są unikatowe numery referencyjne (z 32 cyfr szesnastkowych), które mogą służyć do śledzenia użycia platformy Azure. 

Aby utworzyć identyfikatory GUID do śledzenia, należy użyć generatora identyfikatorów GUID. Zespół usługi Azure Storage utworzył [formularz generatora identyfikatorów GUID,](https://aka.ms/StoragePartners) który będzie wysyłał ci identyfikator GUID o poprawnym formacie i można go ponownie wyhodować w różnych systemach śledzenia.

Zalecamy utworzenie unikatowego identyfikatora GUID dla każdej oferty i kanału dystrybucji dla każdego produktu. Możesz wybrać jeden identyfikator GUID dla wielu kanałów dystrybucji produktu, jeśli nie chcesz, aby raportowanie było dzielone.

Jeśli wdrożysz produkt przy użyciu szablonu i jest on dostępny zarówno w portalu Azure Marketplace, jak i w usłudze GitHub, można utworzyć i zarejestrować dwa różne identyfikatory GUIDS:

*    Produkt A w portalu Azure Marketplace
*    Produkt A w serwisie GitHub

Raportowanie odbywa się za pomocą wartości partnera (Microsoft Partner ID) i identyfikatorów GUID. Można również śledzić identyfikatory GUID na bardziej szczegółowym poziomie, dostosowując się do każdego planu w ramach oferty.

Aby uzyskać więcej informacji, zobacz [Śledzenie użycia klienta platformy Azure z guids często zadawane pytania](https://docs.microsoft.com/azure/marketplace/azure-partner-customer-usage-attribution#faq).

## <a name="create-a-billing-profile"></a>Tworzenie profilu rozliczeniowego

Jeśli publikujesz ofertę [Dynamics 365 for Customer Engagement & Power Apps](./create-new-customer-engagement-offer.md) lub [Dynamics 365 for Operations,](./create-new-operations-offer.md) musisz ukończyć **profil rozliczeniowy**.

Adres rozliczeniowy jest wstępnie wypełniony od firmy i można zaktualizować ten adres później. Pola NIP i identyfikator VAT są opcjonalne.  Nie można edytować nazwy kraju i nazwy firmy.

## <a name="multi-user-account-management"></a>Zarządzanie kontem wielu użytkowników

Centrum partnerskie wykorzystuje [usługę Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) do uzyskiwania dostępu do kont i zarządzania kontem dla wielu użytkowników. Usługa Azure AD organizacji jest automatycznie skojarzona z kontem Centrum partnerów w ramach procesu rejestracji.

## <a name="manage-users"></a>Zarządzanie użytkownikami

Sekcja **Użytkownicy** w Centrum partnerów (w obszarze **Ustawienia konta)** umożliwia zarządzanie użytkownikami, grupami i aplikacjami usługi Azure AD, które mają dostęp do konta Centrum partnerów za pomocą usługi Azure AD. Twoje konto musi mieć uprawnienia [**na poziomie menedżera**](#define-user-roles-and-permissions) dla konta [służbowego (dzierżawy usługi Azure AD),](./company-work-accounts.md) na którym chcesz dodawać lub edytować użytkowników. Aby zarządzać użytkownikami w ramach innego konta służbowego / dzierżawy, musisz się wylogować, a następnie zalogować się z powrotem jako użytkownik z uprawnieniami **menedżera** na tym koncie roboczym / dzierżawie.

Po zalogowaniu się za pomocą konta służbowego (dzierżawy usługi Azure AD) można:

- [Dodawanie lub usuwanie użytkowników](#add-existing-users)
- [Zmienianie hasła użytkownika](#change-a-user-password)
- [Dodawanie lub usuwanie grup](#manage-groups)
- [Dodawanie lub usuwanie aplikacji usługi Azure AD](#add-new-azure-ad-applications)
- [Zarządzanie kluczami dla aplikacji usługi Azure AD](#manage-keys-for-an-azure-ad-application)
- [Definiowanie uprawnień i ról użytkowników](#define-user-roles-and-permissions)

Należy pamiętać, że wszyscy użytkownicy Centrum partnerów (w tym grupy i aplikacje usługi Azure AD) muszą mieć aktywne konto służbowe w [dzierżawie usługi Azure AD](#manage-tenants) skojarzonej z kontem Centrum partnerów.

### <a name="add-existing-users"></a>Dodawanie istniejących użytkowników

Aby dodać użytkowników do konta Centrum partnerów, którzy już istnieją na [koncie służbowym firmy (dzierżawa usługi Azure AD):](./company-work-accounts.md)

1. Przejdź do **pozycji Użytkownicy** (w obszarze **Ustawienia konta)** i wybierz pozycję **Dodaj użytkowników**.
2. Wybierz jednego lub więcej użytkowników z wyświetlona lista. Za pomocą pola wyszukiwania można wyszukiwać konkretnych użytkowników.
*Jeśli wybierzesz więcej niż jednego użytkownika do dodania do konta Centrum partnerów, musisz przypisać mu tę samą rolę lub zestaw uprawnień niestandardowych. Aby dodać wielu użytkowników z różnymi rolami/uprawnieniami, powtórz te kroki dla każdej roli lub zestawu uprawnień niestandardowych.
3. Po zakończeniu wybierania użytkowników kliknij przycisk **Dodaj zaznaczone**.
4. W sekcji **Role** określ role lub dostosowane uprawnienia dla wybranych użytkowników.
5. Wybierz pozycję **Zapisz**.

### <a name="create-new-users"></a>Tworzenie nowych użytkowników

Aby utworzyć zupełnie nowe konta użytkowników, musisz mieć konto z uprawnieniami [**administratora globalnego.**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) 

1. Przejdź do **pozycji Użytkownicy** (w obszarze **Ustawienia konta**) wybierz pozycję **Dodaj użytkowników**, a następnie wybierz pozycję **Utwórz nowych użytkowników**.
1. Wprowadź imię, nazwisko i nazwę użytkownika dla każdego nowego użytkownika. 
1. Jeśli chcesz, aby nowy użytkownik miał konto administratora globalnego w katalogu organizacji, zaznacz pole wyboru Niech ten użytkownik stanie się **administratorem globalnym w usłudze Azure AD, z pełną kontrolą nad wszystkimi zasobami katalogu.** Zapewni to użytkownikowi pełny dostęp do wszystkich funkcji administracyjnych w firmie Azure AD. Będą mogli dodawać użytkowników na koncie służbowym organizacji (dzierżawa usługi Azure AD) i zarządzać nimi, ale nie w Centrum partnerów, chyba że przyznasz kontu odpowiednią rolę/uprawnienia.
1. Jeśli zaznaczono to pole wyboru **Aby ten użytkownik był administratorem globalnym,** musisz podać wiadomość **e-mail odzyskiwania hasła,** aby użytkownik mógł odzyskać hasło, jeśli to konieczne.
1. W sekcji **Członkostwo grupy** wybierz wszystkie grupy, do których ma należeć nowy użytkownik.
1. W sekcji **Role** określ role lub dostosowane uprawnienia użytkownika.
1. Wybierz pozycję **Zapisz**.

Utworzenie nowego użytkownika w Centrum partnerów spowoduje również utworzenie konta dla tego użytkownika na koncie roboczym (dzierżawy usługi Azure AD), do którego użytkownik jest zalogowany. Wprowadzenie zmian w nazwie użytkownika w Centrum partnerskim spowoduje wprowadzenie tych samych zmian na koncie służbowym organizacji (dzierżawy usługi Azure AD).

### <a name="invite-new-users-by-email"></a>Zapraszanie nowych użytkowników pocztą e-mail

Aby zaprosić użytkowników, którzy nie są obecnie częścią konta służbowego firmy (dzierżawy usługi Azure AD) za pośrednictwem poczty e-mail, musisz mieć konto z uprawnieniami [**administratora globalnego.**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

1. Przejdź do **pozycji Użytkownicy** (w obszarze **Ustawienia konta**) wybierz pozycję **Dodaj użytkowników**, a następnie wybierz pozycję **Zaproś użytkowników pocztą e-mail.**
2. Wprowadź jeden lub więcej adresów e-mail (do dziesięciu), oddzielonych przecinkami lub średnikami.
3. W sekcji **Role** określ role lub dostosowane uprawnienia użytkownika.
4. Wybierz pozycję **Zapisz**.

Zaproszeni użytkownicy otrzymają wiadomość e-mail z zaproszeniem do dołączenia do Twojego konta Centrum partnerów. Nowe konto gość-użytkownik zostanie utworzone na koncie służbowym (dzierżawa usługi Azure AD). Każdy użytkownik musi zaakceptować zaproszenie, zanim będzie mógł uzyskać dostęp do Twojego konta.

Jeśli chcesz ponownie wysłać zaproszenie, odwiedź stronę **Użytkownicy,** znajdź zaproszenie na liście użytkowników, wybierz ich adres e-mail (lub tekst z *oczekującym zaproszeniem).* Następnie u dołu strony wybierz pozycję **Wyślij ponownie zaproszenie**.

> [!NOTE]
> Jeśli twoja organizacja używa [integracji katalogów](https://go.microsoft.com/fwlink/p/?LinkID=724033) do synchronizowania lokalnej usługi katalogowej z usługą Azure AD, nie będzie można tworzyć nowych użytkowników, grup ani aplikacji usługi Azure AD w Centrum partnerskim. Użytkownik (lub inny administrator w katalogu lokalnym) będzie musiał utworzyć je bezpośrednio w katalogu lokalnym, zanim będzie można je zobaczyć i dodać w Centrum partnerów.

### <a name="remove-a-user"></a>Usuwanie użytkownika

Aby usunąć użytkownika z konta służbowego (dzierżawy usługi Azure AD), przejdź do **Użytkownicy** (w obszarze **Ustawienia konta**), wybierz użytkownika, który chcesz usunąć za pomocą pola wyboru w prawej kolumnie, a następnie wybierz pozycję **Usuń** z dostępnych akcji. Pojawi się wyskakujące okno, aby potwierdzić, że chcesz usunąć wybranych użytkowników.

### <a name="change-a-user-password"></a>Zmienianie hasła użytkownika

Jeśli jeden z użytkowników musi zmienić swoje hasło, mogą to zrobić sami, jeśli podczas tworzenia konta użytkownika podano **wiadomość e-mail odzyskiwania hasła.** Hasło użytkownika można również zaktualizować, wykonując poniższe czynności. Aby zmienić hasło użytkownika na firmowym koncie służbowym (dzierżawa usługi Azure AD), musisz zalogować się na konto z uprawnieniami [**administratora globalnego.**](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles) Należy zauważyć, że spowoduje to zmianę hasła użytkownika w dzierżawie usługi Azure AD wraz z hasłem używanym do uzyskiwania dostępu do Centrum partnerów.

1. Na stronie **Użytkownicy** (w obszarze **Ustawienia konta)** wybierz nazwę konta użytkownika, które chcesz edytować.
2. Wybierz przycisk **Resetuj hasło** u dołu strony.
3. Pojawi się strona potwierdzenia z informacjami logowania użytkownika, w tym tymczasowym hasłem. Pamiętaj, aby wydrukować lub skopiować te informacje i przekazać go użytkownikowi, ponieważ nie będziesz mieć dostępu do tymczasowego hasła po opuszczeniu tej strony.

## <a name="manage-groups"></a>Zarządzanie grupami

Grupy umożliwiają sterowanie wieloma rolami i uprawnieniami użytkowników razem.

### <a name="add-an-existing-group"></a>Dodawanie istniejącej grupy

Aby dodać grupę, która już istnieje na koncie służbowym organizacji (dzierżawa usługi Azure AD) do konta Centrum partnerów:

1. Na stronie **Użytkownicy** (w obszarze **Ustawienia konta**) wybierz pozycję **Dodaj grupy**.
2. Wybierz jedną lub więcej grup z wyświetlona lista. Za pomocą pola wyszukiwania można wyszukiwać określone grupy.
Jeśli wybierzesz więcej niż jedną grupę do dodania do konta Centrum partnerów, musisz przypisać im tę samą rolę lub zestaw uprawnień niestandardowych. Aby dodać wiele grup z różnymi rolami/uprawnieniami, powtórz te kroki dla każdej roli lub zestawu uprawnień niestandardowych.
3. Po zakończeniu wybierania grup kliknij pozycję **Dodaj zaznaczone**.
4. W sekcji **Role** określ role lub dostosowane uprawnienia dla wybranych grup. Wszyscy członkowie grupy będą mogli uzyskać dostęp do twojego konta Centrum partnerów z uprawnieniami stosowanymi do grupy, niezależnie od ról i uprawnień skojarzonych z ich indywidualnym kontem.
5. Wybierz pozycję **Zapisz**.

Po dodaniu istniejącej grupy każdy użytkownik, który jest członkiem tej grupy, będzie mógł uzyskać dostęp do konta Centrum partnerów z uprawnieniami skojarzonymi z przypisaną rolą grupy.

### <a name="add-a-new-group"></a>Dodawanie nowej grupy

Aby dodać zupełnie nową grupę do konta Centrum partnerów:

1. Na stronie **Użytkownicy** (w obszarze **Ustawienia konta**) wybierz pozycję **Dodaj grupy**.
2. Na następnej stronie wybierz pozycję **Nowa grupa**.
3. Wprowadź nazwę wyświetlaną nowej grupy.
4. Określ role lub dostosowane uprawnienia dla grupy. Wszyscy członkowie grupy będą mogli uzyskać dostęp do twojego konta Centrum partnerów z uprawnieniami, które stosujesz w tym miejscu, niezależnie od ról/uprawnień skojarzonych z ich indywidualnym kontem.
5. Wybierz użytkowników dla nowej grupy z wyświetlona lista. Za pomocą pola wyszukiwania można wyszukiwać konkretnych użytkowników.
6. Po zakończeniu wybierania użytkowników kliknij przycisk **Dodaj zaznaczone,** aby dodać ich do nowej grupy.
7. Wybierz pozycję **Zapisz**.

Należy zauważyć, że ta nowa grupa zostanie utworzona na koncie służbowym organizacji (dzierżawa usługi Azure AD), a nie tylko na koncie Centrum partnerów.

### <a name="remove-a-group"></a>Usuwanie grupy

Aby usunąć grupę z konta służbowego (dzierżawy usługi Azure AD), przejdź do **Użytkownicy** (w obszarze **Ustawienia konta**), wybierz grupę, którą chcesz usunąć, używając pola wyboru w prawej kolumnie, a następnie wybierz pozycję **Usuń** z dostępnych akcji. Pojawi się wyskakujące okno, aby potwierdzić, że chcesz usunąć wybrane grupy.

## <a name="manage-azure-ad-applications"></a>Zarządzanie aplikacjami usługi Azure AD

Możesz zezwolić aplikacjom lub usługom, które są częścią usługi Azure AD twojej firmy, na dostęp do konta Centrum partnerów.

### <a name="add-existing-azure-ad-applications"></a>Dodawanie istniejących aplikacji usługi Azure AD

Aby dodać aplikacje, które już istnieją w firmowej usłudze Azure Active Directory:

1. Na stronie **Użytkownicy** (w obszarze **Ustawienia konta**) wybierz pozycję **Dodaj aplikacje usługi Azure AD**.
2. Wybierz jedną lub więcej aplikacji usługi Azure AD z wyświetlona lista. Za pomocą pola wyszukiwania można wyszukiwać określone aplikacje usługi Azure AD. Jeśli wybierzesz więcej niż jedną aplikację usługi Azure AD do dodania do konta Centrum partnerów, musisz przypisać im tę samą rolę lub zestaw uprawnień niestandardowych. Aby dodać wiele aplikacji usługi Azure AD z różnymi rolami/uprawnieniami, powtórz te kroki dla każdej roli lub zestawu uprawnień niestandardowych.
3. Po wybraniu aplikacji usługi Azure AD kliknij pozycję **Dodaj zaznaczone**.
4. W sekcji **Role** określ role lub dostosowane uprawnienia dla wybranych aplikacji usługi Azure AD.
5. Wybierz pozycję **Zapisz**.

### <a name="add-new-azure-ad-applications"></a>Dodawanie nowych aplikacji usługi Azure AD

Jeśli chcesz udzielić Centrum partnerów dostępu do zupełnie nowego konta aplikacji usługi Azure AD, możesz je utworzyć w sekcji **Użytkownicy.** Należy zauważyć, że spowoduje to utworzenie nowego konta na firmowym koncie służbowym (dzierżawa usługi Azure AD), a nie tylko na koncie Centrum partnerów. Jeśli używasz tej aplikacji usługi Azure AD do uwierzytelniania Centrum partnerów i nie potrzebujesz użytkowników, aby uzyskać do niej bezpośredni dostęp, możesz wprowadzić dowolny prawidłowy adres **URL** odpowiedzi i **identyfikator URI identyfikatora aplikacji,** o ile te wartości nie są używane przez żadną inną aplikację usługi Azure AD w katalogu.

1. Na stronie **Użytkownicy** (w obszarze **Ustawienia konta**) wybierz pozycję **Dodaj aplikacje usługi Azure AD**.
2. Na następnej stronie wybierz pozycję **Nowa aplikacja usługi Azure AD**.
3. Wprowadź **adres URL odpowiedzi** dla nowej aplikacji usługi Azure AD. Jest to adres URL, w którym użytkownicy mogą logować się i używać aplikacji usługi Azure AD (czasami nazywany także adresem URL aplikacji lub adresu URL logowania). **Adres URL odpowiedzi** nie może być dłuższy niż 256 znaków i musi być unikatowy w katalogu.
4. Wprowadź identyfikator **URI identyfikatora aplikacji** dla nowej aplikacji usługi Azure AD. Jest to logiczny identyfikator aplikacji usługi Azure AD, który jest prezentowany, gdy żądanie pojedynczego logowania jest wysyłane do usługi Azure AD. Należy zauważyć, że **identyfikator URI identyfikatora aplikacji** musi być unikatowy dla każdej aplikacji usługi Azure AD w katalogu. Ten identyfikator nie może być dłuższy niż 256 znaków. Aby uzyskać więcej informacji na temat identyfikatora URI identyfikatora aplikacji, zobacz [Integrowanie aplikacji z usługą Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-modify-supported-accounts#change-the-application-registration-to-support-different-accounts).
5. W sekcji **Role** określ role lub dostosowane uprawnienia dla aplikacji usługi Azure AD.
6. Wybierz pozycję **Zapisz**.

Po dodaniu lub utworzeniu aplikacji usługi Azure AD można powrócić do sekcji **Użytkownicy** i wybrać nazwę aplikacji do przejrzenia ustawień aplikacji, w tym identyfikator dzierżawy, identyfikator klienta, adres URL odpowiedzi i identyfikator URI identyfikatora aplikacji.

### <a name="remove-an-azure-ad-application"></a>Usuwanie aplikacji usługi Azure AD

Aby usunąć aplikację z konta służbowego (dzierżawy usługi Azure AD), przejdź do **Użytkownicy** (w obszarze **Ustawienia konta**), wybierz aplikację, którą chcesz usunąć za pomocą pola wyboru w prawej kolumnie, a następnie wybierz pozycję **Usuń** z dostępnych akcji. Pojawi się wyskakujące okno potwierdzające, że chcesz usunąć wybrane aplikacje.

### <a name="manage-keys-for-an-azure-ad-application"></a>Zarządzanie kluczami dla aplikacji usługi Azure AD

Jeśli aplikacja usługi Azure AD odczytuje i zapisuje dane w usłudze Microsoft Azure AD, będzie potrzebować klucza. Klucze dla aplikacji usługi Azure AD można utworzyć, edytując jej informacje w Centrum partnerów. Można również usunąć klucze, które nie są już potrzebne.

1. Na stronie **Użytkownicy** (w obszarze **Ustawienia konta**) wybierz nazwę aplikacji usługi Azure AD. Zobaczysz wszystkie aktywne klucze dla aplikacji usługi Azure AD, w tym datę, w której klucz został utworzony i kiedy wygaśnie. 
2. Aby usunąć klucz, który nie jest już potrzebny, wybierz pozycję **Usuń**.
3. Aby dodać nowy klucz, wybierz pozycję **Dodaj nowy klawisz**.
4. Zostanie wyświetlony ekran z identyfikatorem **klienta** i **wartościami klucza.** Pamiętaj, aby wydrukować lub skopiować te informacje, ponieważ nie będziesz mieć do nich dostępu ponownie po opuszczeniu tej strony.
5. Jeśli chcesz utworzyć więcej klawiszy, wybierz pozycję **Dodaj inny klawisz**.

## <a name="define-user-roles-and-permissions"></a>Definiowanie uprawnień i ról użytkowników

Użytkownikom firmy można przypisać następujące role i uprawnienia do programu Commercial Marketplace w Centrum partnerów:

- **Manager**
  - Może uzyskać dostęp do wszystkich funkcji konta Microsoft z wyjątkiem ustawień podatku i wypłat
  - Może zarządzać użytkownikami, rolami i kontami służbowymi (dzierżawcy)
- **Developer**
  - Może zarządzać ofertami i publikować je
  - Może wyświetlać niektóre raporty wydawców

> [!NOTE]
> W przypadku programu Marketplace komercyjnego nie są używane role Administrator globalny, Współpracownik biznesowy, Współautor finansowy i Marketer. Przypisywanie tych ról użytkownikom nie ma wpływu. Tylko role Menedżera i dewelopera przyznają uprawnienia użytkownikom.

Aby uzyskać więcej informacji na temat zarządzania rolami i uprawnieniami w innych obszarach Centrum partnerskiego, takich jak usługa Azure Active Directory (AD), dostawca rozwiązań w chmurze (CSP), dostawca panelu sterowania (CPV), użytkownicy-gość lub sieć partnerów firmy Microsoft (MPN), zobacz [Przypisywanie ról i uprawnień użytkowników w Centrum partnerów](https://docs.microsoft.com/partner-center/permissions-overview).

## <a name="manage-tenants"></a>Zarządzanie dzierżawcami

Dzierżawy usługi Azure Active Directory (AD), zwany również "konto służbowe" w całej tej dokumentacji, jest reprezentacją organizacji skonfigurowanej w witrynie Azure portal i pomaga zarządzać określonym wystąpieniem usług w chmurze firmy Microsoft dla użytkowników wewnętrznych i zewnętrznych. Jeśli Twoja organizacja zasubskrybowała usługę w chmurze firmy Microsoft, taką jak Azure, Microsoft Intune lub Office 365, ustanowiono dla Ciebie dzierżawę usługi Azure AD.

Można skonfigurować wielu dzierżaw do użycia z Centrum partnerów. Każdy użytkownik z rolą **menedżera** na koncie Centrum partnerów będzie miał możliwość dodania i usunięcia dzierżaw usługi Azure AD z konta.  

### <a name="add-an-existing-tenant"></a>Dodawanie istniejącej dzierżawy

Aby skojarzyć inną dzierżawę usługi Azure AD z kontem Centrum partnerów:

1. Na stronie **Dzierżawcy** (w obszarze **Ustawienia konta)** wybierz pozycję **Skojarz inną dzierżawę usługi Azure AD**.
2. Wprowadź poświadczenia usługi Azure AD dla dzierżawy, którą chcesz skojarzyć.
3. Przejrzyj nazwę organizacji i domeny dzierżawy usługi Azure AD. Aby zakończyć skojarzenie, wybierz pozycję **Potwierdź**.

Jeśli skojarzenie zakończy się pomyślnie, będzie można przystąpić do dodawania użytkowników kont i zarządzania nimi w sekcji **Użytkownicy** w Centrum partnerów.

### <a name="create-a-new-tenant"></a>Tworzenie nowej dzierżawy

Aby utworzyć nową dzierżawę usługi Azure AD za pomocą konta Centrum partnerów:

1. Na stronie **Dzierżawcy** (w obszarze **Ustawienia konta)** wybierz pozycję **Utwórz nową dzierżawę usługi Azure AD**.
2. Wprowadź informacje o katalogu dla nowej usługi Azure AD:
    - **Nazwa domeny:** unikatowa nazwa, której użyjemy w twojej domenie usługi Azure AD wraz z ".onmicrosoft.com". Na przykład jeśli wprowadzono "przykład", twoja domena usługi Azure AD będzie "example.onmicrosoft.com".
    - **Kontaktowy adres e-mail**: Adres e-mail, na który możemy skontaktować się z Tobą w sprawie Twojego konta, jeśli to konieczne.
    - **Informacje o koncie użytkownika administratora globalnego**: Imię, nazwisko, nazwa użytkownika i hasło, których chcesz użyć dla nowego konta administratora globalnego.
3. Wybierz **pozycję Utwórz,** aby potwierdzić nowe informacje o domenie i koncie.
4. Zaloguj się przy użyciu nowej nazwy użytkownika i hasła administratora globalnego usługi Azure AD, aby rozpocząć [dodawanie użytkowników i zarządzanie nimi.](#manage-users)

Aby uzyskać więcej informacji na temat tworzenia nowych dzierżaw w witrynie Azure portal, a nie za pośrednictwem portalu Centrum partnerów, zobacz artykuł [Tworzenie nowej dzierżawy w usłudze Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-access-create-new-tenant)

### <a name="remove-a-tenant"></a>Usuwanie dzierżawy

Aby usunąć dzierżawę z konta Centrum **partnerów,** znajdź jej nazwę na stronie Dzierżawcy (w **ustawieniach konta),** a następnie wybierz pozycję **Usuń**. Zostanie wyświetlony monit o potwierdzenie, że chcesz usunąć dzierżawę. Gdy to zrobisz, żaden użytkownik w tej dzierżawie nie będzie mógł zalogować się do konta Centrum partnerów, a wszelkie uprawnienia skonfigurowane dla tych użytkowników zostaną usunięte.

Po usunięciu dzierżawy wszyscy użytkownicy, którzy zostali dodani do konta Centrum partnerów z tej dzierżawy, nie będą już mogli zalogować się do konta.

> [!TIP]
> Nie można usunąć dzierżawy, jeśli jesteś aktualnie zalogowany do Centrum partnerów przy użyciu konta w tej samej dzierżawie. Aby usunąć dzierżawę, musisz zalogować się do Centrum partnerów jako **Menedżer** dla innej dzierżawy skojarzonej z kontem. Jeśli z kontem jest skojarzona tylko jedna dzierżawa, dzierżawa ta może zostać usunięta tylko po zalogowaniu się za pomocą konta Microsoft, które otworzyło konto.

## <a name="agreements"></a>Umowy

Sekcja **Umowy** w Centrum partnerów (w obszarze **Ustawienia konta)** umożliwia wyświetlenie listy autoryzowanych umów wydawniczych. Umowy te są wyświetlane zgodnie z nazwą i numerem wersji, w tym datą jej przyjęcia i nazwą użytkownika, który zaakceptował umowę.

**Potrzebne działania** mogą pojawić się w górnej części tej strony, jeśli istnieją aktualizacje umowy, które wymagają twojej uwagi. Aby zaakceptować zaktualizowaną umowę, najpierw przeczytaj połączona wersja umowy, a następnie wybierz pozycję **Zaakceptuj umowę**.
