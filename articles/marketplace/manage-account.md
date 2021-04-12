---
title: Jak zarządzać komercyjnym kontem witryny Marketplace w centrum partnerskim firmy Microsoft — Azure Marketplace
description: Dowiedz się, jak zarządzać komercyjnym kontem witryny Marketplace w centrum partnerskim firmy Microsoft.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: varsha-sarah
ms.author: vavargh
ms.date: 04/07/2021
ms.openlocfilehash: c76d9d06425405cf7f43e089cb9c2995e30410ee
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108471"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>Zarządzanie kontem komercyjnej witryny Marketplace w centrum partnerskim

**Odpowiednie role**

- Właściciel
- Menedżer

Po [utworzeniu konta Centrum partnerskiego](./create-account.md)możesz użyć [pulpitu nawigacyjnego portalu Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) , aby zarządzać kontem i ofertami.

## <a name="access-your-account-settings"></a>Dostęp do ustawień konta

Jeśli jeszcze tego nie zrobiono, użytkownik (lub administrator organizacji) powinien uzyskać dostęp do [ustawień konta](https://partner.microsoft.com/dashboard/account/management) Centrum partnerskiego.

1. Zaloguj się do [komercyjnego pulpitu nawigacyjnego portalu Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace) w centrum partnerskim przy użyciu konta, do którego chcesz uzyskać dostęp. Jeśli jesteś częścią wielu kont i zarejestrowano się przy użyciu innego, możesz [przełączyć konta](switch-accounts.md).
1. W prawym górnym rogu wybierz pozycję **Ustawienia** (ikona koła zębatego), a następnie wybierz pozycję **Ustawienia konta**.

    [![Zrzut ekranu przedstawiający menu Ustawienia konta w centrum partnerskim. ](./media/manage-accounts/settings-account.png)](./media/manage-accounts/settings-account.png#lightbox)

1. W obszarze **Ustawienia konta** wybierz pozycję **Legal**. Następnie wybierz kartę **deweloper** , aby wyświetlić szczegóły dotyczące Twojego konta komercyjnego Marketplace.

    [![Zrzut ekranu przedstawiający kartę Deweloper na stronie Informacje prawne w obszarze Ustawienia konta. ](./media/manage-accounts/developer-tab.png)](./media/manage-accounts/developer-tab.png#lightbox)

### <a name="account-settings-page"></a>Strona ustawień konta

Po wybraniu opcji **Ustawienia** i rozszerzeniu **ustawień konta** domyślny widok to **Informacje prawne**. Ta strona może zawierać maksymalnie trzy karty, w zależności od programów zarejestrowanych w programie: _partner_, _odsprzedawca_ i _deweloper_.

Karta **partner** dla partnerów zarejestrowanych w usłudze MPN obejmuje następujące:

- Wszystkie prawne informacje biznesowe, takie jak zarejestrowana legalna nazwa i adres firmy
- Kontakt podstawowy
- Lokalizacje biznesowe.

Karta **odsprzedawca** dla partnerów wykonujących działalność w ramach programu CSP obejmuje:

- Podstawowe informacje kontaktowe
- Profil obsługi klienta
- Informacje o programie

Karta **deweloper** dla partnerów zarejestrowanych w dowolnym programie dewelopera ma następujące informacje:

- **Szczegóły konta**: typ konta i stan konta
- **Identyfikatory wydawcy**: Identyfikator sprzedawcy, identyfikator użytkownika, identyfikator wydawcy, dzierżawy usługi Azure AD i inne
- **Informacje kontaktowe**: Nazwa wyświetlana wydawcy, kontakt ze sprzedającym (nazwisko, adres e-mail, telefon i adres) i osoba zatwierdzająca w firmie (nazwisko, adres e-mail, telefon)

### <a name="account-settings---developer-tab"></a>Ustawienia konta — karta deweloper

Poniższe informacje opisują informacje na karcie Deweloper.

#### <a name="account-details"></a>Szczegóły konta

W sekcji _szczegóły konta_ na karcie _deweloper_ można zobaczyć podstawowe informacje, takie jak **Typ konta** (firma lub osoba indywidualna) oraz **stan weryfikacji** konta. W ramach procesu weryfikacji konta zostaną wyświetlone wszystkie wymagane kroki, w tym Weryfikacja poczty e-mail, weryfikacja zatrudnienia i weryfikacja firmy.

#### <a name="publisher-ids"></a>Identyfikatory wydawcy

W sekcji identyfikatorów wydawcy można zobaczyć **Identyfikator firmy Symantec** (jeśli dotyczy), **Identyfikator sprzedawcy**, identyfikator **użytkownika**, **identyfikator MPN** i **dzierżawy usługi Azure AD**. Te wartości są przypisywane przez firmę Microsoft w celu unikatowego identyfikowania Twojego konta dewelopera i nie można ich edytować.

Jeśli nie masz identyfikatora firmy Symantec, możesz wybrać link, aby go zażądać.

### <a name="contact-info"></a>Informacje kontaktowe

W sekcji _informacje kontaktowe_ zobaczysz **nazwę wyświetlaną wydawcy**, **informacje kontaktowe sprzedającego** (imię i nazwisko, adres e-mail, numer telefonu i adresy sprzedawcy firmy) oraz **osoby zatwierdzające przez firmę** (nazwisko, adres e-mail i numer telefonu osoby odpowiedzialnej za upoważnienie do zatwierdzania decyzji dla firmy).

Możesz również wybrać link **Aktualizuj** , aby zmienić informacje kontaktowe, takie jak nazwa wyświetlana wydawcy i adres e-mail.

### <a name="account-settings-identifiers"></a>Identyfikatory ustawień konta

W obszarze **Ustawienia konta**  >  **profil organizacji** wybierz pozycję **identyfikatory** , aby wyświetlić następujące informacje:

- **Identyfikatory MPN**: wszystkie identyfikatory MPN skojarzone z Twoim kontem
- **Dostawca usług kryptograficznych**: identyfikatory MPN skojarzone z programem CSP dla tego konta.
- **Wydawca**: identyfikatory sprzedawcy skojarzone z Twoim kontem
- **Identyfikatory GUID śledzenia**: wszystkie identyfikatory GUID śledzenia skojarzone z Twoim kontem

#### <a name="tracking-guids"></a>Identyfikatory GUID śledzenia

Unikatowe identyfikatory globalne (GUIDs) są unikatowymi numerami odwołania (z 32 cyframi szesnastkowymi), które mogą być używane do śledzenia użycia platformy Azure.

Aby utworzyć identyfikatory GUID do śledzenia, należy użyć generatora GUID. Zespół usługi Azure Storage utworzył [formularz generatora GUID](https://aka.ms/StoragePartners) , który wyśle wiadomość E-mail na identyfikator GUID prawidłowego formatu i może być ponownie używany w różnych systemach śledzenia.

Zalecamy utworzenie unikatowego identyfikatora GUID dla każdej oferty i kanału dystrybucji dla każdego produktu. Można wybrać opcję użycia jednego identyfikatora GUID dla wielu kanałów dystrybucji produktu, jeśli nie chcesz, aby raportowanie było podzielone.

Jeśli produkt zostanie wdrożony przy użyciu szablonu, który jest dostępny zarówno w portalu Azure Marketplace, jak i w witrynie GitHub, można utworzyć i zarejestrować dwa różne identyfikatory GUID:

- Produkt A w portalu Azure Marketplace
- Produkt A w serwisie GitHub

Raportowanie jest wykonywane przez wartość partnera (identyfikator partnera firmy Microsoft) i identyfikatory GUID. Możesz także śledzić identyfikatory GUID na bardziej szczegółowym poziomie wyrównanym do każdego planu w ramach oferty.

Aby uzyskać więcej informacji, zobacz [Śledzenie użycia klientów platformy Azure z identyfikatorami GUID często zadawanych pytań](azure-partner-customer-usage-attribution.md#faq)i odpowiedzi.

### <a name="agreements"></a>Umowy

Strona **umowy** umożliwia wyświetlenie listy zatwierdzonych umów dotyczących publikacji. Umowy te są wyświetlane zgodnie z nazwą i numerem wersji, włącznie z datą zaakceptowania i nazwą użytkownika, który zaakceptował umowę.

Aby uzyskać dostęp do strony umów:

1. Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/home).
1. W prawym górnym rogu wybierz pozycję **Ustawienia**  >  **Ustawienia konta**.
1. W obszarze **Ustawienia konta** wybierz pozycję **umowy**.

**Wymagane akcje** mogą pojawić się u góry tej strony, jeśli istnieją aktualizacje umów, które wymagają Twojej uwagi. Aby zaakceptować zaktualizowaną umowę, najpierw Przeczytaj połączoną wersję umowy, a następnie wybierz pozycję **Zaakceptuj umowę**.

## <a name="set-up-a-payout-profile"></a>Konfigurowanie profilu wypłaty

Profil wypłaty to konto bankowe, do którego są wysyłane transakcje z sprzedaży. To konto bankowe musi znajdować się w tym samym kraju lub regionie, w którym zostało zarejestrowane konto Centrum partnerskiego. Aby uzyskać więcej informacji na temat profilu wypłaty, zobacz [Tworzenie i zarządzanie wypłatami zachęt i profilami podatków w centrum partnerskim](/partner-center/incentives-create-and-manage-your-payout-and-tax-profiles) oraz [Konfigurowanie konta wypłaty i formularzy podatkowych](/partner-center/set-up-your-payout-account).

Aby skonfigurować profil wypłaty:

1. Przejdź do [strony Przegląd komercyjnego portalu Marketplace](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) w centrum partnerskim.
2. W sekcji **profil** obok pozycji **profil wypłaty** wybierz pozycję **Aktualizuj**.
3. **Wybierz formę płatności**: konto bankowe lub PayPal.
4. **Dodawanie informacji o płatności**: może to obejmować wybranie typu konta (sprawdzanie lub oszczędności), wprowadzenie nazwy, numeru konta i numeru rozliczeniowego, adres rozliczeniowy, numer telefonu lub adres E-mail w systemie PayPal. Aby uzyskać więcej informacji na temat korzystania z systemu PayPal jako formy płatności konta i dowiedzieć się, czy jest ona obsługiwana na Twoim rynku lub regionie, zobacz [Informacje o systemie PayPal](/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info).

> [!IMPORTANT]
> Zmiana konta wypłaty może opóźniać płatności o jeden cykl płatności. To opóźnienie występuje, ponieważ musimy zweryfikować zmianę konta, tak jak w przypadku pierwszej konfiguracji konta wypłaty. Po zweryfikowaniu konta będziesz nadal otrzymywać opłaty za pełną kwotę. Wszelkie płatności z tytułu bieżącego cyklu płatności zostaną dodane do kolejnego.  

## <a name="tax-profile"></a>Profil podatkowy

Przejrzyj bieżący stan profilu podatkowego, potwierdź, że jest wyświetlany prawidłowy **Typ jednostki** i **Informacje o certyfikacie podatkowym** . Wybierz pozycję **Edytuj** , aby zaktualizować lub ukończyć wszystkie wymagane formularze.

Aby ustalić swój stan podatkowy, należy określić swój kraj lub region zamieszkania i obywatelstwa oraz wypełnić odpowiednie formularze podatkowe związane z Twoim krajem lub regionem.

Niezależnie od kraju lub regionu zamieszkania lub obywatelstwa, musisz wypełnić Stany Zjednoczone formularzy podatkowych, aby sprzedać oferty w firmie Microsoft. Partnerzy, którzy spełniają określone wymagania dotyczące Stany Zjednoczone miejsca zamieszkania, muszą wypełnić formularz urzędu skarbowego W postaci od-9. Inni partnerzy spoza Stany Zjednoczone muszą wypełnić formularz urzędu skarbowego W trybie do 8. Możesz wypełnić te formularze w trybie online, gdy dokończysz swój profil podatkowy.

Stany Zjednoczone indywidualny numer identyfikacyjny podatnika (lub ITIN) nie jest wymagany do otrzymywania płatności od firmy Microsoft ani do roszczeń wynikających z Traktatu podatkowego.

W centrum partnerskim można dokończyć i przesyłać formularze podatkowe elektronicznie. w większości przypadków nie ma potrzeby drukowania ani wysyłania żadnych formularzy.

Różne kraje i regiony mają różne wymagania podatkowe. Dokładna kwota, która musi zostać zapłacona podatkiem, zależy od krajów i regionów, w których sprzedawane są oferty. Firma Microsoft dokonuje sprzedaży i używa podatków w Twoim imieniu w niektórych krajach i regionach. Te kraje i regiony zostaną zidentyfikowane w procesie tworzenia oferty. W innych krajach i regionach, w zależności od tego, gdzie się rejestrujesz, może być konieczne przekazanie sprzedaży i użycie podatku za sprzedaż bezpośrednio do urzędu opodatkowania lokalnego. Ponadto otrzymane przychody mogą być opodatkowane jako dochód. Zdecydowanie zachęcamy do skontaktowania się z odpowiednim urzędem dla danego kraju lub regionu, który może pomóc w ustaleniu odpowiednich informacji podatkowych dla transakcji sprzedaży firmy Microsoft.

Aby uzyskać więcej informacji na temat profilu podatkowego, zobacz [Tworzenie i zarządzanie wypłatami zachęt i profilami podatków w centrum partnerskim](/partner-center/incentives-create-and-manage-your-payout-and-tax-profiles) oraz [Konfigurowanie konta wypłaty i formularzy podatkowych](/partner-center/set-up-your-payout-account).

### <a name="withholding-rates"></a>Stawki za potrącenie

Informacje przesyłane w formularzach podatkowych określają odpowiednią stawkę za potrącenie podatku. Wskaźnik potrącenia ma zastosowanie tylko do sprzedaży wprowadzonej do Stany Zjednoczone; sprzedaż dokonywana w lokalizacjach innych niż Stany USA nie podlega potrąceniu. Stawki zaliczania są różne, ale dla większości deweloperów rejestrowanych poza Stany Zjednoczone Domyślna stawka wynosi 30%. Istnieje możliwość zredukowania tej stawki, jeśli kraj lub region wyraził zgodę na Traktat dotyczący podatku dochodowego z Stany Zjednoczone.

### <a name="tax-treaty-benefits"></a>Korzyści z Traktatu podatkowego

Jeśli nie masz Stany Zjednoczone, możesz skorzystać z korzyści z traktatów podatkowych. Te korzyści różnią się w zależności od kraju/regionu do kraju/regionu i mogą pomóc w zmniejszeniu liczby podatków rozliczeń przez firmę Microsoft. Korzyści z Traktatu podatkowego można zgłaszać, wypełniając część II formularza W 8BEN. Zalecamy komunikację z odpowiednimi zasobami w Twoim kraju lub regionie, aby określić, czy te korzyści dotyczą użytkownika.

[Dowiedz się więcej o szczegółach dotyczących podatku dla deweloperów aplikacji/gier systemu Windows i wydawców portalu Azure Marketplace](/windows/uwp/publish/tax-details-for-paid-apps).

### <a name="payout-hold-status"></a>Stan wstrzymania

Domyślnie firma Microsoft wysyła płatności miesięcznie. Można jednak opcjonalnie wprowadzić wypłaty, co uniemożliwi wysyłanie płatności do konta. Jeśli zdecydujesz się na wstrzymanie wypłatów, będziemy nadal rejestrować wszelkie dochody, które uzyskasz, i podaj szczegóły w **podsumowaniu wypłaty**. Nie będziemy jednak wysyłać żadnych płatności do konta do momentu usunięcia blokady.

**Aby wstrzymać płatności**:

1. Przejdź do pozycji **Ustawienia konta**. 
1. Po lewej stronie rozwiń pozycję **wypłata i podatek** , a następnie wybierz pozycję **wypłaty i profile podatkowe**.
1. Wybierz program, dla którego chcesz przeprowadzić płatności, a następnie zaznacz pole wyboru **Zablokuj moją płatność** .

W każdej chwili możesz zmienić status wypłaty, ale należy pamiętać, że decyzja wpłynie na następną miesięczną wypłatę. Na przykład jeśli chcesz wstrzymać wypłatę z kwietnia, pamiętaj, aby przed końcem marca ustawić stan wstrzymania na wartość **włączone** .

Po ustawieniu wypłaty stan Wstrzymaj na **wł**. wszystkie wypłaty zostaną wstrzymane do momentu przełączenia suwaka do trybu **wyłączone**. Gdy to zrobisz, zostanie uwzględniony w następnym cyklu miesięcznej wypłaty (w przypadku spełnienia wszelkich odpowiednich progów płatności). Na przykład jeśli wystąpiły wypłaty w dniu wstrzymania, ale chcesz wystawić wypłatę w czerwcu, upewnij się, że stan wstrzymania wypłaty jest **wyłączony** przed końcem maja.

> [!NOTE]
> **Wypłata** z tytułu stanu ma zastosowanie do **wszystkich** źródeł przychodów, które są płatne za pomocą Centrum partnerskiego firmy Microsoft, w tym witryny Azure Marketplace, Microsoft AppSource, Microsoft Store, reklamy itp.). Dla każdego źródła przychodu nie można wybrać różnych stanów wstrzymania.

## <a name="devices"></a>Urządzenia

Ustawienia zarządzania urządzeniami dotyczą tylko publikowania na platformie uniwersalnej systemu Windows (platformy UWP). [Dowiedz się więcej](/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

## <a name="create-a-billing-profile"></a>Tworzenie profilu rozliczeń

Jeśli publikujesz [Dynamics 365 do zaangażowania klienta & aplikacje zaawansowane](./partner-center-portal/create-new-customer-engagement-offer.md) lub [Dynamics 365 for Operations](./partner-center-portal/create-new-operations-offer.md) , musisz ukończyć *profil rozliczeń*.

Adres rozliczeniowy jest wstępnie wypełniony przez firmę prawną i można go później zaktualizować. Pola podatek i Identyfikator VAT są wymagane przez niektóre kraje i opcjonalne dla innych. Nie można edytować nazwy kraju/regionu i nazwy firmy.

1. Przejdź do pozycji **Ustawienia konta**.
1. Następnie w lewym okienku rozwijanym rozwiń pozycję **profil organizacji** i wybierz pozycję **profil rozliczeń**.


## <a name="multi-user-account-management"></a>Zarządzanie kontami przez wiele użytkowników

Centrum partnerskie używa [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) do uzyskiwania dostępu do konta i zarządzania przez wiele użytkowników. Usługa Azure AD Twojej organizacji jest automatycznie skojarzona z kontem Centrum partnerskiego w ramach procesu rejestracji.

## <a name="next-steps"></a>Następne kroki

- [Dodawanie użytkowników i zarządzanie nimi](add-manage-users.md)
