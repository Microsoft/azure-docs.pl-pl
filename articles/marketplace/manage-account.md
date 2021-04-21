---
title: Jak zarządzać kontem komercyjnej platformy handlowej w programie Microsoft Partner Center — Azure Marketplace
description: Dowiedz się, jak zarządzać kontem platformy handlowej w witrynie Microsoft Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: varsha-sarah
ms.author: vavargh
ms.date: 04/07/2021
ms.openlocfilehash: 6b721e7acb7907743c0696aff6c11ad59f5ceba9
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107812574"
---
# <a name="manage-your-commercial-marketplace-account-in-partner-center"></a>Zarządzanie kontem komercyjnej platformy handlowej w Partner Center

**Odpowiednie role**

- Właściciel
- Menedżer

Po utworzeniu konta usługi Partner Center do zarządzania kontem [](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) i [ofertami](./create-account.md)można użyć pulpitu nawigacyjnego platformy handlowej.

## <a name="access-your-account-settings"></a>Uzyskiwanie dostępu do ustawień konta

Jeśli jeszcze tego nie zrobiono, Użytkownik (lub administrator organizacji) powinien uzyskać dostęp do ustawień konta dla Partner Center konta. [](https://partner.microsoft.com/dashboard/account/management)

1. Zaloguj się do [pulpitu](https://partner.microsoft.com/dashboard/commercial-marketplace) nawigacyjnego platformy handlowej w Partner Center przy użyciu konta, do którego chcesz uzyskać dostęp. Jeśli należysz do wielu kont i zalogowano się przy użyciu innego konta, możesz przełączyć [konta](switch-accounts.md).
1. W prawym górnym rogu wybierz pozycję **Ustawienia** (ikona koła zębatego), a następnie wybierz **pozycję Ustawienia konta.**

    [![Zrzut ekranu przedstawiający menu ustawień konta w Partner Center. ](./media/manage-accounts/settings-account.png)](./media/manage-accounts/settings-account.png#lightbox)

1. W **obszarze Ustawienia konta** wybierz pozycję **Prawne.** Następnie wybierz **kartę Deweloper,** aby wyświetlić szczegóły powiązane z Twoim kontem platformy handlowej.

    [![Zrzut ekranu przedstawiający kartę dewelopera na stronie prawnej w ustawieniach konta. ](./media/manage-accounts/developer-tab.png)](./media/manage-accounts/developer-tab.png#lightbox)

### <a name="account-settings-page"></a>Strona Ustawień konta

Po wybraniu opcji **Ustawienia** i **rozwinięciu opcji Ustawienia konta** domyślny widok to Informacje **prawne.** Ta strona może mieć maksymalnie trzy karty, w zależności od programów zarejestrowanych w: _Partner_, _Reseller_ i _Developer_.

Karta **Partner** dla partnerów zarejestrowanych w programie MPN obejmuje:

- Wszystkie prawne informacje biznesowe, takie jak zarejestrowana nazwa prawna i adres firmy
- Podstawowa osoba kontaktowa
- Lokalizacje biznesowe.

Karta **Reseller** (Sprzedawca) dla partnerów wykonujących działalność w programie CSP obejmuje:

- Podstawowe informacje kontaktowe
- Profil pomocy technicznej klienta
- Informacje o programie

Karta **Deweloper** dla partnerów zarejestrowanych w dowolnym programie dewelopera zawiera następujące informacje:

- **Szczegóły konta:** Typ konta i Stan konta
- **Identyfikatory wydawcy:** identyfikator sprzedawcy, identyfikator użytkownika, identyfikator wydawcy, dzierżawy usługi Azure AD i inne
- **Informacje kontaktowe:** nazwa wyświetlana wydawcy, osoba kontaktowa sprzedawcy (imię i nazwisko, adres e-mail, numer telefonu i adres) oraz osoba zatwierdzająca w firmie (imię i nazwisko, adres e-mail, telefon)

### <a name="account-settings---developer-tab"></a>Ustawienia konta — karta Deweloper

Poniższe informacje opisują informacje na karcie Deweloper.

#### <a name="account-details"></a>Szczegóły konta

W _sekcji Szczegóły_ konta  na karcie Deweloper można wyświetlić podstawowe informacje, takie jak typ  konta **(firma** lub osoba indywidualna) i stan weryfikacji twojego konta. Podczas procesu weryfikacji konta te ustawienia będą wyświetlać każdy wymagany krok, w tym weryfikację wiadomości e-mail, weryfikację zatrudnienia i weryfikację firmy.

#### <a name="publisher-ids"></a>Identyfikatory wydawcy

W sekcji Identyfikatory wydawcy możesz zobaczyć identyfikator **firmy Symantec** (jeśli **dotyczy),** identyfikator sprzedawcy, identyfikator **użytkownika,** **identyfikator MPN** i dzierżawy usługi **Azure AD.** Te wartości są przypisywane przez firmę Microsoft w celu unikatowego identyfikowania konta dewelopera i nie można ich edytować.

Jeśli nie masz identyfikatora firmy Symantec, możesz wybrać link, aby go zażądać.

### <a name="contact-info"></a>Informacje kontaktowe

W  sekcji Informacje kontaktowe można wyświetlić nazwę wyświetlaną wydawcy, informacje kontaktowe sprzedawcy **(nazwę** kontaktu, adres e-mail, numer telefonu i adres sprzedawcy firmy) oraz osobę zatwierdzającą w firmie **(imię** i nazwisko, adres e-mail i numer telefonu osoby z uprawnieniami do zatwierdzania decyzji dla firmy).

Możesz również wybrać link **Aktualizuj,** aby zmienić informacje kontaktowe, takie jak nazwa wyświetlana wydawcy i adres e-mail.

### <a name="account-settings-identifiers"></a>Identyfikatory ustawień konta

W **obszarze Ustawienia konta** Profil  >  **organizacji** wybierz pozycję **Identyfikatory,** aby wyświetlić następujące informacje:

- **Identyfikatory MPN:** wszystkie identyfikatory MPN skojarzone z Twoim kontem
- **CSP:** identyfikatory MPN skojarzone z programem CSP dla tego konta.
- **Wydawca:** Identyfikatory sprzedawców skojarzone z Twoim kontem
- **Identyfikatory GUID śledzenia:** wszelkie identyfikatory GUID śledzenia skojarzone z Twoim kontem

#### <a name="tracking-guids"></a>Identyfikatory GUID śledzenia

Globalnie unikatowe identyfikatory (GUID) to unikatowe numery referencyjne (z 32 cyframi szesnastkowymi), których można użyć do śledzenia użycia platformy Azure.

Aby utworzyć identyfikatory GUID do śledzenia, należy użyć generatora identyfikatorów GUID. Zespół usługi Azure Storage utworzył formularz generatora [identyfikatorów GUID,](https://aka.ms/StoragePartners) który wysyła pocztą e-mail identyfikator GUID o prawidłowym formacie i może być ponownie używany w różnych systemach śledzenia.

Zalecamy utworzenie unikatowego identyfikatora GUID dla każdej oferty i kanału dystrybucji dla każdego produktu. Jeśli nie chcesz dzielić raportowania, możesz użyć jednego identyfikatora GUID dla wielu kanałów dystrybucji produktu.

Jeśli wdrażasz produkt przy użyciu szablonu i jest on dostępny zarówno w witrynie Azure Marketplace, jak i w witrynie GitHub, możesz utworzyć i zarejestrować dwa różne identyfikatory GUID:

- Produkt A w Azure Marketplace
- Produkt A w witrynie GitHub

Raportowanie jest wykonywane przez wartość partnera (Microsoft identyfikator partnera) i identyfikatory GUID. Identyfikatory GUID można również śledzić na bardziej szczegółowym poziomie, dostosowywując je do poszczególnych planów w ramach oferty.

Aby uzyskać więcej informacji, zobacz Śledzenie użycia [klientów platformy Azure za pomocą identyfikatorów GUID — często zadawane pytania.](azure-partner-customer-usage-attribution.md#faq)

### <a name="agreements"></a>Umowy

Strona  Umowy umożliwia wyświetlenie listy autoryzowanych umów publikowania. Te umowy są wyświetlane na liście według nazwy i numeru wersji, w tym daty jej zaakceptowania i nazwy użytkownika, który zaakceptował umowę.

Aby uzyskać dostęp do strony Umowy:

1. Zaloguj się do [Partner Center](https://partner.microsoft.com/dashboard/home).
1. W prawym górnym rogu wybierz pozycję **Ustawienia**  >  **Ustawienia Ustawienia konta.**
1. W **obszarze Ustawienia konta** wybierz pozycję **Umowy**.

**Wymagane akcje** mogą pojawić się w górnej części tej strony, jeśli istnieją aktualizacje umowy, które wymagają Twojej uwagi. Aby zaakceptować zaktualizowaną umowę, najpierw przeczytaj wersję połączonej umowy, a następnie wybierz **pozycję Zaakceptuj umowę.**

## <a name="set-up-a-payout-profile"></a>Konfigurowanie profilu wypłat

Profil wypłaty to konto bankowe, do którego są wysyłane wpływy ze sprzedaży. To konto bankowe musi znajdować się w tym samym kraju lub regionie, w którym Partner Center konto. Aby uzyskać więcej informacji na temat profilu wypłaty, zobacz Tworzenie profilów wypłat i profilów podatkowych zachęt [oraz zarządzanie](/partner-center/incentives-create-and-manage-your-payout-and-tax-profiles) nimi w programie Partner Center oraz Konfigurowanie konta wypłaty [i formularzy podatkowych.](/partner-center/set-up-your-payout-account)

Aby skonfigurować profil wypłaty:

1. Przejdź do strony [przeglądu komercyjnej platformy handlowej w](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) Partner Center.
2. W sekcji **Profil** obok opcji **Profil** wypłaty wybierz pozycję **Aktualizuj**.
3. **Wybierz formę płatności:** Konto bankowe lub PayPal.
4. **Dodaj informacje** o płatności: może to obejmować wybór typu konta (sprawdzenie lub oszczędności), wprowadzenie nazwy właściciela konta, numeru konta i numeru routingu, adresu rozliczeniowego, numeru telefonu lub adresu e-mail w systemie PayPal. Aby uzyskać więcej informacji na temat korzystania z usługi PayPal jako formy płatności na koncie i dowiedzieć się, czy jest ona obsługiwana na rynku lub w Twoim regionie, zobacz Informacje o systemie [PayPal.](/windows/uwp/publish/setting-up-your-payout-account-and-tax-forms#paypal-info)

> [!IMPORTANT]
> Zmiana konta wypłaty może opóźnić płatności nawet o jeden cykl płatności. To opóźnienie występuje, ponieważ musimy zweryfikować zmianę konta, tak jak podczas pierwszej konfiguracji konta wypłaty. Po zweryfikowaniu konta nadal otrzymasz pełną kwotę. Wszelkie płatności za bieżący cykl płatności zostaną dodane do następnego.  

## <a name="tax-profile"></a>Profil podatkowy

Sprawdź bieżący stan profilu podatkowego, potwierdzając, że jest wyświetlany prawidłowy **typ** jednostki i informacje o **certyfikacie** podatkowym. Wybierz **pozycję Edytuj,** aby zaktualizować lub wypełnić wymagane formularze.

Aby ustalić stan podatkowy, musisz określić kraj lub region, w którym znajduje się obywatel i obywatel, oraz wypełnić odpowiednie formularze podatkowe skojarzone z danym kraju lub regionem.

Niezależnie od kraju lub regionu, w którym znajduje się kraj lub region, musisz wypełnić formularze podatkowe, Stany Zjednoczone sprzedaż jakichkolwiek ofert za pośrednictwem firmy Microsoft. Partnerzy spełniający określone Stany Zjednoczone dotyczące rezydencji muszą wypełnić formularz IRS W-9. Inni partnerzy spoza Stany Zjednoczone muszą wypełnić formularz IRS W-8. Te formularze można wypełniać online podczas wypełniania profilu podatkowego.

Numer identyfikacyjny Stany Zjednoczone indywidualnego (ITIN) nie jest wymagany do otrzymywania płatności od firmy Microsoft ani do otrzymywania korzyści podatkowych.

Formularze podatkowe można wypełnić i przesłać w formie elektronicznej w Partner Center. W większości przypadków nie trzeba drukować ani wysyłać żadnych formularzy pocztą e-mail.

Różne kraje i regiony mają różne wymagania podatkowe. Dokładna kwota, którą należy zapłacić w podatkach, zależy od krajów i regionów, w których sprzedajesz swoje oferty. Firma Microsoft remituje sprzedaż i korzysta z podatku w Twoim imieniu w niektórych krajach i regionach. Te kraje i regiony zostaną zidentyfikowane w procesie tworzenia oferty. W innych krajach i regionach, w zależności od tego, gdzie jest zarejestrowana rejestracja, może być konieczne remitowanie sprzedaży i użycie podatku dla sprzedaży bezpośrednio do lokalnego urzędu skarbowego. Ponadto osłaniane przychody ze sprzedaży mogą być rozliczane jako dochód. Zdecydowanie zachęcamy do skontaktowania się z odpowiednim urzędem w danym kraju lub regionie, co może pomóc w ustaleniu odpowiednich informacji podatkowych dla transakcji sprzedaży firmy Microsoft.

Aby uzyskać więcej informacji na temat profilu podatkowego, zobacz Create [and manage incentives payout and tax profiles in Partner Center](/partner-center/incentives-create-and-manage-your-payout-and-tax-profiles) (Tworzenie profilów wypłat i profilów podatkowych w programie Partner Center) oraz Set up your payout account and tax forms (Konfigurowanie konta wypłaty i [formularzy podatkowych).](/partner-center/set-up-your-payout-account)

### <a name="withholding-rates"></a>Stawki potrącone

Informacje, które przesyłasz w formularzach podatkowych, określają odpowiednią stawkę potrącenia podatku. Stawka potrącania ma zastosowanie tylko do sprzedaży w Stany Zjednoczone; sprzedaż dokonana w lokalizacjach innych niż USA nie podlega wstrzymaniu. Stawki potrącania różnią się, ale w przypadku większości deweloperów rejestrujących się poza Stany Zjednoczone stawka domyślna wynosi 30%. Możesz zmniejszyć tę stawkę, jeśli twój kraj lub region wyraził zgodę na podatek dochodowy w Stany Zjednoczone.

### <a name="tax-treaty-benefits"></a>Korzyści z podatku

Jeśli jesteś poza Stany Zjednoczone, możesz skorzystać z korzyści podatkowych. Korzyści te różnią się w zależności od kraju/regionu i mogą pozwolić na zmniejszenie kwoty podatków wstrzymywanych przez firmę Microsoft. Korzyści podatkowe można uzyskać, wypełniając część II formularza W-8BEN. Zalecamy komunikowanie się z odpowiednimi zasobami w twoim kraju lub regionie, aby określić, czy te korzyści mają zastosowanie do Ciebie.

[Dowiedz się więcej o szczegółach podatkowych dla deweloperów aplikacji/gier dla systemu Windows Azure Marketplace wydawców.](/windows/uwp/publish/tax-details-for-paid-apps)

### <a name="payout-hold-status"></a>Stan wstrzymania wypłat

Domyślnie firma Microsoft wysyła płatności co miesiąc. Możesz jednak opcjonalnie zablokować wypłaty, co uniemożliwi wysyłanie płatności na Konto. Jeśli zdecydujesz się trzymać wypłaty, będziemy nadal rejestrować wszelkie zarobki i podać szczegółowe informacje w **podsumowaniu wypłaty.** Jednak nie wyślemy żadnych płatności na Twoje konto, dopóki nie usuniesz wstrzymania.

**Aby trzymać płatności:**

1. Przejdź do **ustawień konta**. 
1. W lewym okienku sieci rozwiń pozycję **Wypłata i podatek,** a następnie **wybierz pozycję Profile wypłat i podatkowe.**
1. Wybierz program, dla którego chcesz przechowywać płatności, a następnie zaznacz pole **wyboru Wstrzymaj** moją płatność.

Stan wstrzymania wypłaty można zmienić w dowolnym momencie, ale należy pamiętać, że decyzja będzie mieć wpływ na następną miesięczną wypłatę. Jeśli na przykład chcesz trzymać kwietniowe wypłaty, upewnij się, że stan wstrzymywania wypłaty został ustawiony **na Wł.** przed końcem marca.

Po ustawieniu stanu wstrzymania **wypłaty** na Wł. wszystkie wypłaty będą wstrzymane do momentu przełączenia suwaka z powrotem do **opcji Wyłączone.** Gdy to zrobisz, zostaniesz uwzględniony w następnym miesięcznym cyklu wypłaty (pod warunkiem, że zostały spełnione odpowiednie progi płatności). Jeśli na przykład wypłaty zostały wstrzymane, ale chcesz, aby wypłata była generowana w czerwcu, pamiętaj, aby przed końcem maja przełączyć stan wstrzymania wypłaty na Wyłączone. 

> [!NOTE]
> Wybór **stanu Wstrzymanie wypłat ma** zastosowanie do wszystkich źródeł przychodów, które są opłacane za pośrednictwem usługi Microsoft Partner Center, w tym Azure Marketplace, Microsoft AppSource, Microsoft Store, reklam itp.).  Nie można wybrać różnych stanów przechowywania dla każdego źródła przychodów.

## <a name="devices"></a>Urządzenia

Ustawienia zarządzania urządzeniami dotyczą tylko publikowania na platformie uniwersalnej systemu Windows (UWP). [Dowiedz się więcej](/windows/uwp/publish/manage-account-settings-and-profile#additional-settings-and-info).

## <a name="create-a-billing-profile"></a>Tworzenie profilu rozliczeniowego

Jeśli publikujesz ofertę [usługi Dynamics 365 for Customer Engagement & Power Apps](dynamics-365-customer-engage-offer-setup.md) lub Dynamics [365 for Operations,](./partner-center-portal/create-new-operations-offer.md) musisz wypełnić *swój profil rozliczeniowy.*

Adres rozliczeniowy jest wstępnie wypełniany z jednostki prawnej i można go później zaktualizować. Pola TAX i VAT ID są wymagane w niektórych krajach i opcjonalne dla innych. Nie można edytować nazwy kraju/regionu ani nazwy firmy.

1. Przejdź do **ustawień konta**.
1. Następnie w lewym okienku sieciowym rozwiń pozycję **Profil organizacji** i wybierz pozycję **Profil rozliczeniowy.**


## <a name="multi-user-account-management"></a>Zarządzanie kontami wielu użytkowników

Partner Center korzysta [Azure Active Directory](/azure/active-directory/fundamentals/active-directory-whatis) (Azure AD) do uzyskiwania dostępu do kont wielu użytkowników i zarządzania nimi. Usługa Azure AD organizacji jest automatycznie skojarzona Partner Center kontem usługi w ramach procesu rejestracji.

## <a name="next-steps"></a>Następne kroki

- [Dodawanie użytkowników i zarządzanie nimi](add-manage-users.md)
