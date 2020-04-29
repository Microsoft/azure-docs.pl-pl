---
title: Tworzenie konta dewelopera firmy Microsoft | Portal Azure Marketplace
description: Wymagania i kroki związane z tworzeniem konta dewelopera Microsoft.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: 161abde1ef0dfd86842fb56afe699fa632e0d9ba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280375"
---
<a name="create-a-microsoft-developer-account"></a>Tworzenie konta dewelopera firmy Microsoft
====================================

W tym artykule opisano, jak stać się zatwierdzonym deweloperem firmy Microsoft na potrzeby publikowania w witrynie Azure Marketplace.

## <a name="create-a-microsoft-account"></a>Tworzenie konta Microsoft

Aby rozpocząć proces publikowania, musisz ukończyć rejestrację **Centrum deweloperów Microsoft** . Użyjesz tego samego zarejestrowanego konta w **[Portal Cloud partner](https://cloudpartner.azure.com/)** , aby rozpocząć proces publikowania.

### <a name="general-account-guidelines"></a>Ogólne wytyczne dotyczące kont

Zalecamy, aby masz tylko jeden konto Microsoft dla ofert portalu Azure Marketplace. To konto nie powinno być specyficzne dla usług ani ofert.

Adres tworzący nazwę użytkownika powinien znajdować się w domenie i kontrolowany przez Twój zespół IT. Wszystkie działania związane z publikowaniem należy wykonać za pomocą tego konta.

>[!WARNING]
>Takie słowa, jak "Azure" i "Microsoft", nie są obsługiwane w przypadku rejestracji konto Microsoft. Unikaj używania tych słów w celu zakończenia procesu tworzenia i rejestracji konta.

### <a name="company-account-guidelines"></a>Wytyczne dotyczące konta firmowego

Postępuj zgodnie z tymi wskazówkami, jeśli więcej niż jedna osoba będzie musiała uzyskać dostęp do konta, logując się przy użyciu konto Microsoft, które otworzyło konto.

>[!IMPORTANT]
>Aby umożliwić wielu użytkownikom dostęp do konta Centrum deweloperów, zalecamy używanie Azure Active Directory do przypisywania ról do poszczególnych użytkowników. Mogą uzyskać dostęp do konta, logując się przy użyciu poszczególnych poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Zarządzanie użytkownikami konta](https://docs.microsoft.com/windows/uwp/publish/manage-account-users).

-   Utwórz konto Microsoft przy użyciu adresu e-mail, który należy do domeny firmowej\', ale nie do pojedynczej osoby. Na przykład WindowsApps\@fabrikam.com.
-   Ogranicz dostęp do tego konto Microsoft do najmniejszej możliwej liczby deweloperów.
-   Skonfiguruj firmową listę dystrybucyjną poczty e-mail, która zawiera wszystkich użytkowników, którzy muszą uzyskać dostęp do konta dewelopera, a następnie Dodaj ten adres e-mail do informacji zabezpieczających. Dzięki temu wszyscy pracownicy na liście mogą odbierać kody zabezpieczeń w razie potrzeby i zarządzać informacjami o zabezpieczeniach konto Microsoft. Jeśli skonfigurowanie listy dystrybucyjnej nie jest możliwe, właściciel indywidualnego konta e-mail musi być dostępny, aby uzyskać dostęp do kodu zabezpieczeń i udostępnić go po wyświetleniu monitu (na przykład gdy nowe informacje zabezpieczające zostaną dodane do konta lub w przypadku, gdy wymagane jest uzyskanie dostępu z nowego urządzenia).
-   Dodaj numer telefonu firmy, który nie wymaga rozszerzenia i jest dostępny dla głównych członków zespołu.
-   Ogólnie rzecz biorąc Deweloperzy używają zaufanych urządzeń do logowania się do firmowego konta dewelopera. Wszyscy członkowie zespołu kluczy powinni mieć dostęp do tych zaufanych urządzeń. Pozwoli to zmniejszyć potrzebę wysyłania kodów zabezpieczeń podczas uzyskiwania dostępu do konta.
-   Jeśli musisz zezwolić na dostęp do konta z niezaufanego komputera, Ogranicz dostęp do maksymalnie pięciu deweloperów. W idealnym przypadku deweloperzy powinni uzyskiwać dostęp do konta z maszyn, które współużytkują tę samą lokalizację geograficzną i sieciową.
-   Często Przejrzyj [informacje zabezpieczające firmy](https://account.live.com/proofs/Manage) , aby upewnić się, że jest ona aktualna.

>[!IMPORTANT]
>Twoje konto dewelopera powinno być dostępne głównie z zaufanych komputerów. Jest to ważne, ponieważ istnieje limit liczby wygenerowanych kodów dla konta na tydzień. Zapewnia również najbardziej bezproblemowe środowisko logowania.
>
>Aby uzyskać więcej informacji, zobacz [dodatkowe wskazówki i zabezpieczenia dotyczące konta dewelopera](https://msdn.microsoft.com/windows/uwp/publish/opening-a-developer-account#additional-guidelines-for-company-accounts).

### <a name="to-create-a-microsoft-account"></a>Aby utworzyć konto Microsoft

1.  Otwórz nową sesję przeglądania w trybie InPrivate programu Chrome incognito lub Internet Explorer, aby upewnić się, że użytkownik nie jest zalogowany na istniejącym koncie.
2.  Zarejestrowanie poczty e-mail (zgodnie z poprzednimi wskazówkami) jako konto Microsoft za pomocą tego [linku](https://signup.live.com/signup.aspx). Wykonaj następujące instrukcje dotyczące rejestracji:

    - Podczas rejestrowania konta jako konto Microsoft należy podać prawidłowy numer telefonu dla systemu, aby przesłać kod weryfikacyjny konta jako wiadomość tekstową lub automatyczne wywołanie.
    - Podczas rejestrowania konta jako konto Microsoft należy podać prawidłowy identyfikator e-mail w celu uzyskania zautomatyzowanej wiadomości e-mail w celu weryfikacji konta.
    - Sprawdź, czy adres e-mail został wysłany do listy dystrybucyjnej.

    Teraz możesz przystąpić do korzystania z nowego konto Microsoft w centrum deweloperów firmy Microsoft.

## <a name="register-your-account-in-microsoft-developer-center"></a>Zarejestruj swoje konto w centrum deweloperów firmy Microsoft

Centrum deweloperów firmy Microsoft służy do rejestrowania informacji o firmie jeden raz. Rejestratorze musi być prawidłowym przedstawicielem firmy i musi podać swoje dane osobowe w celu weryfikacji tożsamości. Osoba rejestrująca musi używać konto Microsoft udostępnionej firmie, **a to samo konto musi być używane w Portal Cloud partner.** Upewnij się, że firma nie ma jeszcze konta Centrum deweloperów firmy Microsoft przed podjęciem próby jego utworzenia. W trakcie procesu będziemy zbierać informacje o adresie firmy, informacje o koncie bankowym i informacje podatkowe. Są one zazwyczaj uzyskiwane z kontaktów finansowych lub firmowych.

>[!IMPORTANT]
>Aby postępować zgodnie z różnymi etapami tworzenia i wdrażania oferty, należy wykonać następujące składniki profilu dewelopera.

| Profil dewelopera     | Aby rozpocząć pracę w wersji roboczej    | Przygotowanie       | Publikowanie szablonu bezpłatnego i rozwiązania   | Publikuj komercyjne   |
|---------------------- |----------------   |-----------    |-------------------------------------  |---------------------  |
| Rejestracja w firmie  | Musi mieć         | Musi mieć     | Musi mieć                             | Musi mieć             |
| Identyfikator profilu podatkowego        | Optional          | Optional      | Optional                              | Musi mieć             |
| Konto bankowe          | Optional          | Optional      | Optional                              | Musi mieć             |

>[!NOTE]
>Korzystanie z własnej licencji (BYOL) jest obsługiwane tylko dla maszyn wirtualnych i jest uważana za bezpłatną ofertę.

### <a name="register-your-company-account"></a>Rejestrowanie konta firmowego

1. Otwórz nową sesję przeglądania InPrivate lub incognito przeglądarki Internet Explorer, aby upewnić się, że użytkownik nie jest zalogowany na koncie osobistym.

2. Przejdź do [Centrum deweloperów systemu Windows](https://dev.windows.com/registration?accountprogram=azure) , aby zarejestrować siebie jako sprzedającego. Przed kontynuowaniem Przeczytaj zapoznaj się z poniższą uwagą.

   ![Weryfikacja konto Microsoft](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-verify.jpg)

    >[!IMPORTANT]
    >Upewnij się, że identyfikator poczty e-mail lub lista dystrybucyjna (lista dystrybucyjna jest zalecana do usunięcia zależności od osób), które będą używane do rejestracji w centrum deweloperów, są najpierw zarejestrowane jako konto Microsoft. Jeśli nie, zarejestruj się za pomocą tego linku. Ponadto nie można używać żadnych identyfikatorów poczty e-mail w domenie firmowej firmy Microsoft na potrzeby rejestracji w centrum deweloperów.

   ![Logowanie do centrum deweloperów](./media/cloud-partner-portal-create-dev-center-registration/seller-dashboard-login.jpg)

3. Uruchom Kreatora "Pomóż nam chronić Twoje konto", aby zweryfikować swoją tożsamość przy użyciu numeru telefonu lub adresu e-mail.

4. W oknie Informacje o koncie rejestracji wybierz z listy rozwijanej **kraj/region konta** , a następnie wybierz przycisk **dalej**.

   ![Wybierz kraj/region](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_04.png)

    >[!WARNING]
    >"Sprzedawca" z krajów/regionów: aby sprzedawać swoje usługi w portalu Azure Marketplace, zarejestrowana jednostka musi być z jednego z zatwierdzonych krajów/regionów, które są wyświetlane na liście rozwijanej. To ograniczenie dotyczy wypłaty i przyczyn podatkowych. Aby uzyskać więcej informacji, zobacz zasady uczestnictwa w portalu Marketplace.

5. Wybierz pozycję **firma** jako "typ konta", a następnie wybierz pozycję **dalej**.

    >[!IMPORTANT]
    >Aby lepiej zrozumieć typy kont i zdecydować, który typ jest najlepszy dla Ciebie, Wyświetl typy kont stron, lokalizacje i opłaty widoczne w następnym przechwyceniu ekranu.

    ![Typy kont dla sprzedawcy](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_05.png)

6. Wprowadź **nazwę wyświetlaną wydawcy**. Jest to zazwyczaj nazwa firmy.

    >[!NOTE]
    >Nazwa wyświetlana wydawcy wprowadzona w centrum deweloperów nie jest wyświetlana w portalu Azure Marketplace po wyświetleniu oferty. Jednak te informacje są konieczne do zakończenia procesu rejestracji.

7. Wprowadź **informacje kontaktowe** dla weryfikacji konta.

    >[!IMPORTANT]
    >Musisz podać dokładne informacje kontaktowe, ponieważ zostaną one użyte w procesie weryfikacyjnym, aby Twoja firma mogła zostać zatwierdzona w centrum deweloperów.

8. Wprowadź informacje kontaktowe **osoby zatwierdzającej w firmie**. Osoba zatwierdzająca w firmie jest osobą, która może sprawdzić, czy masz uprawnienia do tworzenia konta w centrum deweloperów w imieniu organizacji. Po podaniu tych informacji wybierz pozycję **dalej** , aby przejść do **sekcji płatność**.

    ![Określ osobę zatwierdzającą w firmie](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_08.png)

9. Wprowadź informacje o płatności dla Twojego konta. Jeśli masz kod promocyjny, który obejmuje koszt rejestracji, możesz wprowadzić go tutaj. W przeciwnym razie podaj informacje o karcie kredytowej (lub w systemie PayPal na obsługiwanych rynkach). Wybierz pozycję **dalej** , aby przejść do ostatniego **przeglądu**.

   ![Rejestracja płatności](./media/cloud-partner-portal-create-dev-center-registration/imgRegisterCo_09.png)

10. Przejrzyj informacje o koncie i upewnij się, że wszystkie elementy są poprawne. Przeczytaj i zaakceptuj warunki i postanowienia [umowy wydawcy Microsoft Azure Marketplace](https://go.microsoft.com/fwlink/?LinkID=699560). Zaznacz pole wyboru, aby wskazać, że masz uprawnienia do odczytu i zaakceptowania tych warunków.

11. Wybierz pozycję **Zakończ** , aby potwierdzić rejestrację. Na adres e-mail zostanie wysłany komunikat z potwierdzeniem.

12. Jeśli planujesz tylko publikowanie bezpłatnych ofert, wybierz pozycję [Przejdź do Portal Cloud partner](https://cloudpartner.azure.com/) i przejdź do pozycji "Zarejestruj konto w portalu Cloud Partner" w tym artykule.

### <a name="commercial-offers"></a>Oferty komercyjne

Jeśli planujesz publikowanie ofert komercyjnych, takich jak oferta maszyn wirtualnych przy użyciu modelu rozliczania godzinowego, musisz podać informacje podatkowe i bankowe. W tym celu zaloguj się do konta Centrum deweloperów i wybierz pozycję **Zaktualizuj informacje o koncie**. Postępuj zgodnie z instrukcjami w następnej sekcji "Dodawanie informacji bankowych i podatkowych".

>[!IMPORTANT]
>Nie będzie można wypchnąć komercyjnej oferty do produkcji bez podawania informacji o koncie bankowym i podatku.

Jeśli wolisz później zaktualizować dane bankowe i podatkowe, możesz przejść do "Zarejestruj konto w portalu Cloud Partner" w tym artykule.

>[!NOTE]
>Firma Microsoft zaleca, aby jak najszybciej zapewnić informacje o koncie bankowym i podatku, ponieważ trwa sprawdzanie poprawności informacji podatkowych.

### <a name="add-banking-and-tax-information"></a>Dodawanie informacji bankowych i podatkowych

Aby opublikować komercyjne oferty zakupu, należy dodać wypłatę i informacje podatkowe i przesłać je do walidacji w centrum deweloperów.

**Aby podać informacje o banku**

1.  Zaloguj się do [Centrum deweloperów firmy Microsoft](https://dev.windows.com/registration?accountprogram=azure) przy użyciu konto Microsoft.
2.  Wybierz pozycję **konto wypłaty** w menu po lewej stronie, w obszarze **Wybierz formę płatności**wybierz pozycję **konto bankowe** lub **PayPal**.

    >[!NOTE]
    >Jeśli masz komercyjne oferty, które klienci kupują w portalu Marketplace, jest to konto, na którym zostanie wyświetlona wypłata za te zakupy.
3.  Wprowadź informacje o płatności, a następnie wybierz pozycję **Zapisz**.

    >[!IMPORTANT]
    >Jeśli musisz zaktualizować lub zmienić konto wypłaty, wykonaj powyższe kroki, aby zastąpić bieżące informacje nowymi informacjami.
    >
    >Zmiana konta wypłaty może opóźniać płatności o jeden cykl płatności. To opóźnienie występuje, ponieważ musimy zweryfikować zmianę konta, tak jak podczas pierwszej konfiguracji konta wypłaty. Po zweryfikowaniu konta będziesz nadal otrzymywać opłaty za pełną kwotę. Wszelkie płatności z tytułu bieżącego cyklu płatności zostaną dodane do kolejnego.

4.  Wybierz pozycję **Dalej**.

**Aby podać informacje podatkowe**

1.  Zaloguj się do [Centrum deweloperów firmy Microsoft](https://dev.windows.com/registration?accountprogram=azure) , korzystając z konto Microsoft (jeśli to konieczne).
2.  Z menu po lewej stronie wybierz pozycję **profil podatkowy**.
3.  Na stronie **Konfigurowanie formularza podatkowego** :
    - Wybierz kraj lub region, w którym znajduje się stały miejsce w miejscu.
    - Wybierz kraj lub region, w którym posiadasz obywatelstwo podstawowe.
    - Wybierz pozycję **Dalej**.
4.  Wprowadź szczegóły podatkowe, a następnie wybierz pozycję **dalej**.

>[!WARNING]
>Nie będzie możliwe wypchnięcie do ofert komercyjnych w środowisku produkcyjnym bez podawania informacji o koncie bankowym i podatku w Twoim koncie Microsoft Developer Center.

### <a name="developer-center-registration-issues"></a>Problemy z rejestracją Centrum deweloperów

Jeśli masz problemy z rejestracją Centrum deweloperów, wykonaj następujące kroki, aby otworzyć bilet pomocy technicznej.

1.  Przejdź do [linku do pomocy technicznej](https://developer.microsoft.com/windows/support).
2.  W obszarze **kontakt z nami**wybierz pozycję **Prześlij zdarzenie**.
    ![Otwórz bilet](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_02.png)
3.  W obszarze **typ problemu**wybierz pozycję "pomoc z Centrum deweloperów" i dla **kategorii**wybierz pozycję "publikowanie aplikacji i zarządzanie nimi". Wybierz pozycję **Rozpocznij pocztę e-mail**.

    ![Identyfikowanie typu problemu](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_03.png)

4.  Zostanie nadana Strona logowania. Zaloguj się przy użyciu dowolnych konto Microsoft. Jeśli nie masz konto Microsoft, [Utwórz je](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1). \

5.  Podaj szczegółowe informacje o problemie i wybierz pozycję **Prześlij** , aby wysłać bilet.

    ![Prześlij bilet](./media/cloud-partner-portal-create-dev-center-registration/imgAddTax_05.png)

## <a name="register-your-account-in-the-cloud-partner-portal"></a>Zarejestruj swoje konto w portalu Cloud partner

Użyj [Portal Cloud partner](https://cloudpartner.azure.com/) , aby opublikować oferty i zarządzać nimi.

1.  Otwórz nową sesję przeglądania w trybie InPrivate programu Chrome incognito lub Internet Explorer, aby upewnić się, że użytkownik nie jest zalogowany na koncie osobistym.
2.  Przejdź do [Portal Cloud partner](https://cloudpartner.azure.com/).
3.  Jeśli jesteś nowym użytkownikiem i zalogujesz się do [Portal Cloud partner](https://cloudpartner.azure.com/) po raz pierwszy, musisz zalogować się przy użyciu tego samego identyfikatora poczty e-mail, który został zarejestrowany w ramach konta Centrum deweloperów. Dzięki temu konto Centrum deweloperów i konto portalu partnerów w chmurze są połączone ze sobą.

Później możesz dodać innych członków firmy, którzy pracują nad aplikacją. Możesz je jako współautorzy lub właściciele w portalu Cloud partner, wykonując czynności opisane w następnej sekcji.

Jeśli użytkownik jest dodawany jako współautor/właściciel w portalu portalu Cloud partner, można zalogować się przy użyciu własnego konta.

>[!TIP]
>Zasady uczestnictwa są opisane w witrynie sieci Web systemu Azure.

## <a name="manage-users-as-owners-or-contributors-in-the-cloud-partner-portal"></a>Zarządzanie użytkownikami jako właściciele lub współautorzy w portalu Cloud partner

[Kroki umożliwiające zarządzanie użytkownikami w portalu Cloud partner](./cloud-partner-portal-manage-users.md)


## <a name="next-steps"></a>Następne kroki

Teraz, gdy Twoje konto zostało utworzone i zarejestrowane, możesz rozpocząć proces publikowania w portalu Azure Marketplace.
