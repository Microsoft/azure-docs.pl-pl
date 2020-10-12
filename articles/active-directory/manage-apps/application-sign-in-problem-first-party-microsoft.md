---
title: Problemy z logowaniem do aplikacji firmy Microsoft | Microsoft Docs
description: Rozwiązywanie typowych problemów występujących podczas logowania się do aplikacji firmy Microsoft korzystających z usługi Azure AD (np. Microsoft 365).
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/10/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8942a55d880132313e1cdac6bfc025e0b153b410
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90707955"
---
# <a name="problems-signing-in-to-a-microsoft-application"></a>Problemy z logowaniem do aplikacji firmy Microsoft

Aplikacje firmy Microsoft (takie jak Exchange, SharePoint, Yammer itp.) są przypisywane i zarządzane nieco inaczej niż aplikacje SaaS innej firmy lub inne aplikacje, które integrują się z usługą Azure AD w celu logowania jednokrotnego.

Istnieją trzy główne metody uzyskiwania dostępu do aplikacji opublikowanej przez firmę Microsoft.

-   W przypadku aplikacji w Microsoft 365 lub innych płatnych pakietów użytkownicy uzyskują dostęp za pośrednictwem **przypisania licencji** bezpośrednio do konta użytkownika lub za pośrednictwem grupy przy użyciu naszej możliwości przypisania licencji opartej na grupach.

-   W przypadku aplikacji, które firma Microsoft lub osoba trzecia bezpłatnie publikuje dla każdego z nich, użytkownicy mogą uzyskać dostęp za pomocą **zgody użytkownika**. Oznacza to, że logują się do aplikacji przy użyciu konta służbowego usługi Azure AD i zezwalają na dostęp do pewnego ograniczonego zestawu danych na swoim koncie.

-   W przypadku aplikacji, które firma Microsoft lub inna osoba publikuje bezpłatnie dla wszystkich użytkowników, może również uzyskać dostęp za pomocą **zgody administratora**. Oznacza to, że administrator ustalił, że aplikacja może być używana przez wszystkich użytkowników w organizacji, aby zalogować się do aplikacji przy użyciu konta administratora globalnego i udzielić dostępu wszystkim osobom w organizacji.

Aby rozwiązać problem, Zacznij od [ogólnych obszarów problemów z dostępem do aplikacji, aby wziąć pod uwagę](#general-problem-areas-with-application-access-to-consider) , a następnie Przeczytaj Przewodnik: kroki rozwiązywania problemów z dostępem do aplikacji firmy Microsoft w celu uzyskania szczegółowych informacji.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Ogólne obszary problemów z dostępem do aplikacji do rozważenia

Poniżej znajduje się lista ogólnych obszarów problemów, do których można przejść w przypadku, gdy masz pomysł na rozpoczęcie, ale zalecamy zapoznanie się z przewodnikiem, aby szybko rozpocząć pracę: Przewodnik: kroki rozwiązywania problemów z dostępem do aplikacji firmy Microsoft.

-   [Problemy związane z kontem użytkownika](#problems-with-the-users-account)

-   [Problemy z grupami](#problems-with-groups)

-   [Problemy z zasadami dostępu warunkowego](#problems-with-conditional-access-policies)

-   [Problemy z zgodą aplikacji](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Kroki rozwiązywania problemów z dostępem do aplikacji firmy Microsoft

Poniżej przedstawiono niektóre typowe problemy, które osób, gdy użytkownicy nie mogą zalogować się do aplikacji firmy Microsoft.

- Ogólne problemy do sprawdzenia w pierwszej kolejności

  * Upewnij się, że użytkownik loguje się do **poprawnego adresu URL** , a nie adresu URL aplikacji lokalnej.

  * Upewnij się, że konto użytkownika **nie jest zablokowane.**

  * Upewnij się, że **konto użytkownika istnieje** w Azure Active Directory. [Sprawdź, czy konto użytkownika istnieje w Azure Active Directory](#problems-with-the-users-account)

  * Upewnij się, że konto użytkownika jest **włączone** do logowania. [Sprawdź stan konta użytkownika](#problems-with-the-users-account)

  * Upewnij się, że **hasło użytkownika nie wygasło lub zostało zapomniane.** [Zresetuj hasło użytkownika](#reset-a-users-password) lub Włącz samoobsługowe [Resetowanie hasła](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

  * Upewnij się, że **Multi-Factor Authentication** nie blokuje dostępu użytkownika. [Sprawdź stan usługi uwierzytelniania wieloskładnikowego użytkownika](#check-a-users-multi-factor-authentication-status) lub [Sprawdź informacje kontaktowe uwierzytelniania użytkownika](#check-a-users-authentication-contact-info)

  * Upewnij się, że **zasady dostępu warunkowego** lub zasady **ochrony tożsamości** nie blokują dostępu użytkownika. [Sprawdzanie określonych zasad dostępu warunkowego](#problems-with-conditional-access-policies) lub [sprawdzanie zasad dostępu warunkowego określonych aplikacji](#check-a-specific-applications-conditional-access-policy) lub [wyłączanie określonych zasad dostępu warunkowego](#disable-a-specific-conditional-access-policy)

  * Upewnij się, że **informacje kontaktowe uwierzytelniania** użytkownika są aktualne, aby zezwolić na wymuszanie zasad dostępu Multi-Factor Authentication lub warunkowego. [Sprawdź stan usługi uwierzytelniania wieloskładnikowego użytkownika](#check-a-users-multi-factor-authentication-status) lub [Sprawdź informacje kontaktowe uwierzytelniania użytkownika](#check-a-users-authentication-contact-info)

- W przypadku aplikacji **firmy Microsoft** , **które wymagają licencji** (na przykład usługi Office 365), poniżej przedstawiono niektóre konkretne problemy, które należy sprawdzić po ustaleniu ogólnych problemów powyżej:

  * Upewnij się, że użytkownik lub ma **przypisaną licencję.** [Sprawdzanie przypisanych licencji użytkownika](#check-a-users-assigned-licenses) lub [Sprawdzanie przypisanych licencji grupy](#check-a-groups-assigned-licenses)

  * Jeśli licencja jest **przypisana do** **grupy statycznej**, należy się upewnić, że **użytkownik jest członkiem** tej grupy. [Sprawdź członkostwo w grupach użytkowników](#check-a-users-group-memberships)

  * Jeśli licencja jest **przypisana do** **grupy dynamicznej**, upewnij się, że **reguła grupy dynamicznej jest ustawiona poprawnie**. [Sprawdź kryteria członkostwa w grupie dynamicznej](#check-a-dynamic-groups-membership-criteria)

  * Jeśli licencja jest **przypisana do** **grupy dynamicznej**, należy się upewnić, że grupa dynamiczna **zakończyła przetwarzanie** członkostwa i że **użytkownik jest członkiem** (może to potrwać trochę czasu). [Sprawdź członkostwo w grupach użytkowników](#check-a-users-group-memberships)

  *  Po upewnieniu się, że licencja jest przypisana, upewnij się, że licencja **nie wygasła**.

  *  Upewnij się, że licencja dotyczy **aplikacji, do której** uzyskuje dostęp.

- W przypadku aplikacji **firmy Microsoft** **, które nie wymagają licencji**, Oto kilka innych rzeczy do sprawdzenia:

  * Jeśli aplikacja żąda **uprawnień na poziomie użytkownika** (na przykład "uzyskaj dostęp do skrzynki pocztowej tego użytkownika"), upewnij się, że użytkownik zalogował się do aplikacji i wykonał **operację wyrażania zgody na poziomie użytkownika** , aby umożliwić aplikacji dostęp do swoich danych.

  * Jeśli aplikacja żąda **uprawnień na poziomie administratora** (na przykład "dostęp do skrzynek pocztowych wszystkich użytkowników"), należy się upewnić, że administrator globalny wykonał **operację zgody na poziomie administratora w imieniu wszystkich użytkowników** w organizacji.

## <a name="problems-with-the-users-account"></a>Problemy związane z kontem użytkownika

Dostęp do aplikacji można zablokować z powodu problemu z użytkownikiem przypisanym do aplikacji. Poniżej przedstawiono kilka sposobów rozwiązywania problemów z użytkownikami i ich ustawień konta:

-   [Sprawdź, czy konto użytkownika istnieje w Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Sprawdź stan konta użytkownika](#check-a-users-account-status)

-   [Resetowanie hasła użytkownika](#reset-a-users-password)

-   [Włączanie samoobsługowego resetowania hasła](#enable-self-service-password-reset)

-   [Sprawdź stan usługi uwierzytelniania wieloskładnikowego użytkownika](#check-a-users-multi-factor-authentication-status)

-   [Sprawdź informacje kontaktowe uwierzytelniania użytkownika](#check-a-users-authentication-contact-info)

-   [Sprawdź członkostwo w grupach użytkowników](#check-a-users-group-memberships)

-   [Sprawdź przypisane licencje użytkownika](#check-a-users-assigned-licenses)

-   [Przypisywanie użytkownikowi licencji](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Sprawdź, czy konto użytkownika istnieje w Azure Active Directory

Aby sprawdzić, czy konto użytkownika jest obecne, wykonaj następujące czynności:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  w menu nawigacji kliknij pozycję **Użytkownicy i grupy** .

5.  Kliknij pozycję **Wszyscy użytkownicy**.

6.  **Wyszukaj** interesującego Cię użytkownika i **kliknij wiersz,** który chcesz wybrać.

7.  Sprawdź właściwości obiektu użytkownika, aby upewnić się, że będą wyglądały zgodnie z oczekiwaniami, i nie ma żadnych danych.

### <a name="check-a-users-account-status"></a>Sprawdź stan konta użytkownika

Aby sprawdzić stan konta użytkownika, wykonaj następujące kroki:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  w menu nawigacji kliknij pozycję **Użytkownicy i grupy** .

5.  Kliknij pozycję **Wszyscy użytkownicy**.

6.  **Wyszukaj** interesującego Cię użytkownika i **kliknij wiersz,** który chcesz wybrać.

7.  Kliknij pozycję **profil**.

8.  W obszarze **Ustawienia** upewnij się, że opcja **Zablokuj logowanie** jest ustawiona na wartość **nie**.

### <a name="reset-a-users-password"></a>Resetowanie hasła użytkownika

Aby zresetować hasło użytkownika, wykonaj następujące kroki:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  w menu nawigacji kliknij pozycję **Użytkownicy i grupy** .

5.  Kliknij pozycję **Wszyscy użytkownicy**.

6.  **Wyszukaj** interesującego Cię użytkownika i **kliknij wiersz,** który chcesz wybrać.

7.  Kliknij przycisk **resetowania hasła** w górnej części okienka użytkownika.

8.  Kliknij przycisk **zresetuj hasło** w wyświetlonym okienku **Resetowanie hasła** .

9.  Skopiuj **hasło tymczasowe** lub **wprowadź nowe hasło** dla użytkownika.

10. Poinformuj użytkownika o tym nowym haśle, aby móc zmienić to hasło podczas kolejnego logowania do Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Włączanie samoobsługowego resetowania hasła

Aby włączyć funkcję samoobsługowego resetowania hasła, wykonaj następujące kroki wdrażania:

-   [Zezwalaj użytkownikom na Resetowanie swoich haseł Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

-   [Zezwól użytkownikom na Resetowanie lub zmienianie Active Directory haseł lokalnych](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>Sprawdź stan usługi uwierzytelniania wieloskładnikowego użytkownika

Aby sprawdzić stan usługi uwierzytelniania wieloskładnikowego użytkownika, wykonaj następujące kroki:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. w menu nawigacji kliknij pozycję **Użytkownicy i grupy** .

5. Kliknij pozycję **Wszyscy użytkownicy**.

6. Kliknij przycisk **Multi-Factor Authentication** w górnej części okienka.

7. Po załadowaniu **portalu administracyjnego Multi-Factor Authentication** upewnij się, że jesteś na karcie **Użytkownicy** .

8. Znajdź użytkownika na liście użytkowników, wyszukując, filtrując lub sortując.

9. Wybierz użytkownika z listy użytkowników i **Włącz**, **Wyłącz**lub **wymuszj** uwierzytelnianie wieloskładnikowe zgodnie z potrzebami.

   * **Uwaga**: Jeśli użytkownik jest w stanie **wymuszonym** , można ustawić je jako czasowo **wyłączone** , aby umożliwić im powrót do konta. Po ponownym uruchomieniu programu można zmienić jego stan na ponownie **włączony** , aby wymagać od nich ponownego zarejestrowania swoich informacji kontaktowych podczas kolejnego logowania. Alternatywnie możesz wykonać kroki opisane w [informacji kontaktowych dotyczących uwierzytelniania użytkownika](#check-a-users-authentication-contact-info) , aby sprawdzić lub ustawić dla nich te dane.

### <a name="check-a-users-authentication-contact-info"></a>Sprawdź informacje kontaktowe uwierzytelniania użytkownika

Aby sprawdzić informacje kontaktowe uwierzytelniania użytkownika używane do uwierzytelniania wieloskładnikowego, dostępu warunkowego, ochrony tożsamości i resetowania hasła, wykonaj następujące kroki:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  w menu nawigacji kliknij pozycję **Użytkownicy i grupy** .

5.  Kliknij pozycję **Wszyscy użytkownicy**.

6.  **Wyszukaj** interesującego Cię użytkownika i **kliknij wiersz,** który chcesz wybrać.

7.  Kliknij pozycję **profil**.

8.  Przewiń w dół do **informacji kontaktowych dotyczących uwierzytelniania**.

9.  **Przejrzyj** dane zarejestrowane dla użytkownika i zaktualizuj je w razie potrzeby.

### <a name="check-a-users-group-memberships"></a>Sprawdź członkostwo w grupach użytkowników

Aby sprawdzić członkostwa w grupach użytkowników, wykonaj następujące kroki:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  w menu nawigacji kliknij pozycję **Użytkownicy i grupy** .

5.  Kliknij pozycję **Wszyscy użytkownicy**.

6.  **Wyszukaj** interesującego Cię użytkownika i **kliknij wiersz,** który chcesz wybrać.

7.  Kliknij pozycję **grupy** , aby zobaczyć grupy, do których należy użytkownik.

### <a name="check-a-users-assigned-licenses"></a>Sprawdź przypisane licencje użytkownika

Aby sprawdzić przypisane licencje użytkownika, wykonaj następujące kroki:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  w menu nawigacji kliknij pozycję **Użytkownicy i grupy** .

5.  Kliknij pozycję **Wszyscy użytkownicy**.

6.  **Wyszukaj** interesującego Cię użytkownika i **kliknij wiersz,** który chcesz wybrać.

7.  Kliknij pozycję **licencje** , aby zobaczyć, które licencje aktualnie przypisane do użytkownika.

### <a name="assign-a-user-a-license"></a>Przypisywanie użytkownikowi licencji 

Aby przypisać licencję do użytkownika, wykonaj następujące kroki:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  w menu nawigacji kliknij pozycję **Użytkownicy i grupy** .

5.  Kliknij pozycję **Wszyscy użytkownicy**.

6.  **Wyszukaj** interesującego Cię użytkownika i **kliknij wiersz,** który chcesz wybrać.

7.  Kliknij pozycję **licencje** , aby zobaczyć, które licencje aktualnie przypisane do użytkownika.

8.  Kliknij przycisk **Przypisz** .

9.  Wybierz **co najmniej jeden produkt** z listy dostępnych produktów.

10. **Opcjonalnie** kliknij element **Opcje przypisania** , aby szczegółowo przypisać produkty. Po zakończeniu kliknij przycisk **OK** .

11. Kliknij przycisk **Przypisz** , aby przypisać te licencje temu użytkownikowi.

## <a name="problems-with-groups"></a>Problemy z grupami

Dostęp do aplikacji można zablokować z powodu problemu z grupą, która jest przypisana do aplikacji. Poniżej przedstawiono kilka sposobów rozwiązywania problemów z grupami i członkostwem w grupach:

-   [Sprawdź członkostwo w grupie](#check-a-groups-membership)

-   [Sprawdź kryteria członkostwa w grupie dynamicznej](#check-a-dynamic-groups-membership-criteria)

-   [Sprawdź przypisane licencje grupy](#check-a-groups-assigned-licenses)

-   [Przetwórz ponownie licencje grupy](#reprocess-a-groups-licenses)

-   [Przypisywanie grupy do licencji](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Sprawdź członkostwo w grupie

Aby sprawdzić członkostwo grupy, wykonaj następujące kroki:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  w menu nawigacji kliknij pozycję **Użytkownicy i grupy** .

5.  Kliknij pozycję **wszystkie grupy**.

6.  **Wyszukaj** interesującą Cię grupę i **kliknij wiersz,** aby wybrać.

7.  Kliknij pozycję **Członkowie** , aby przejrzeć listę użytkowników przypisanych do tej grupy.

### <a name="check-a-dynamic-groups-membership-criteria"></a>Sprawdź kryteria członkostwa w grupie dynamicznej 

Aby sprawdzić kryteria członkostwa w grupie dynamicznej, wykonaj następujące kroki:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  w menu nawigacji kliknij pozycję **Użytkownicy i grupy** .

5.  Kliknij pozycję **wszystkie grupy**.

6.  **Wyszukaj** interesującą Cię grupę i **kliknij wiersz,** aby wybrać.

7.  Kliknij pozycję **dynamiczne reguły członkostwa.**

8.  Zapoznaj się z **prostą** lub **zaawansowaną** regułą zdefiniowaną dla tej grupy i upewnij się, że użytkownik, który ma być członkiem tej grupy, spełnia te kryteria.

### <a name="check-a-groups-assigned-licenses"></a>Sprawdź przypisane licencje grupy

Aby sprawdzić przypisane licencje grupy, wykonaj następujące kroki:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  w menu nawigacji kliknij pozycję **Użytkownicy i grupy** .

5.  Kliknij pozycję **wszystkie grupy**.

6.  **Wyszukaj** interesującą Cię grupę i **kliknij wiersz,** aby wybrać.

7.  Kliknij pozycję **licencje** , aby sprawdzić, które licencje zostały przypisane do grupy.

### <a name="reprocess-a-groups-licenses"></a>Przetwórz ponownie licencje grupy

Aby ponownie przetworzyć przypisane licencje grupy, wykonaj następujące czynności:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. w menu nawigacji kliknij pozycję **Użytkownicy i grupy** .

5. Kliknij pozycję **wszystkie grupy**.

6. **Wyszukaj** interesującą Cię grupę i **kliknij wiersz,** aby wybrać.

7. Kliknij pozycję **licencje** , aby sprawdzić, które licencje zostały przypisane do grupy.

8. Kliknij przycisk **przetwórz** ponownie, aby upewnić się, że licencje przypisane do członków tej grupy są aktualne. Może to zająć dużo czasu, w zależności od rozmiaru i złożoności grupy.

   >[!NOTE]
   >Aby to przyspieszyć, należy rozważyć tymczasowe przypisanie licencji bezpośrednio do użytkownika. [Przypisz użytkownikowi licencję](#problems-with-application-consent).
   >
   >

### <a name="assign-a-group-a-license"></a>Przypisywanie grupy do licencji

Aby przypisać licencję do grupy, wykonaj następujące kroki:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. w menu nawigacji kliknij pozycję **Użytkownicy i grupy** .

5. Kliknij pozycję **wszystkie grupy**.

6. **Wyszukaj** interesującą Cię grupę i **kliknij wiersz,** aby wybrać.

7. Kliknij pozycję **licencje** , aby sprawdzić, które licencje zostały przypisane do grupy.

8. Kliknij przycisk **Przypisz** .

9. Wybierz **co najmniej jeden produkt** z listy dostępnych produktów.

10. **Opcjonalnie** kliknij element **Opcje przypisania** , aby szczegółowo przypisać produkty. Po zakończeniu kliknij przycisk **OK** .

11. Kliknij przycisk **Przypisz** , aby przypisać te licencje do tej grupy. Może to zająć dużo czasu, w zależności od rozmiaru i złożoności grupy.

    >[!NOTE]
    >Aby to przyspieszyć, należy rozważyć tymczasowe przypisanie licencji bezpośrednio do użytkownika. [Przypisz użytkownikowi licencję](#problems-with-application-consent).
    > 
    >

## <a name="problems-with-conditional-access-policies"></a>Problemy z zasadami dostępu warunkowego

### <a name="check-a-specific-conditional-access-policy"></a>Sprawdzanie określonych zasad dostępu warunkowego

Aby sprawdzić lub zweryfikować pojedyncze zasady dostępu warunkowego:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3. Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4. Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji.

5. Kliknij element nawigacji **dostępu warunkowego** .

6. Kliknij odpowiednie zasady, które chcesz sprawdzić.

7. Sprawdź, czy nie ma określonych warunków, przypisań lub innych ustawień, które mogą blokować dostęp użytkowników.

   >[!NOTE]
   >Możesz tymczasowo wyłączyć te zasady, aby upewnić się, że nie wpłynie to na logowanie. Aby to zrobić, ustaw przełącznik **Włącz zasady** na wartość **nie** , a następnie kliknij przycisk **Zapisz** .
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Sprawdzanie zasad dostępu warunkowego określonych aplikacji

Aby sprawdzić lub sprawdzić poprawność aktualnie skonfigurowanych zasad dostępu warunkowego dla jednej aplikacji:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji.

5.  Kliknij pozycję **wszystkie aplikacje**.

6.  Wyszukaj szukaną aplikację lub użytkownik próbuje zalogować się na podstawie nazwy wyświetlanej aplikacji lub identyfikatora aplikacji.

     >[!NOTE]
     >Jeśli nie widzisz szukanej aplikacji, kliknij przycisk **Filtr** i rozwiń zakres listy do **wszystkich aplikacji**. Jeśli chcesz zobaczyć więcej kolumn, kliknij przycisk **kolumny** , aby dodać dodatkowe szczegóły dotyczące aplikacji.
     >
     >

7.  Kliknij element nawigacji **dostępu warunkowego** .

8.  Kliknij odpowiednie zasady, które chcesz sprawdzić.

9.  Sprawdź, czy nie ma określonych warunków, przypisań lub innych ustawień, które mogą blokować dostęp użytkowników.

     >[!NOTE]
     >Możesz tymczasowo wyłączyć te zasady, aby upewnić się, że nie wpłynie to na logowanie. Aby to zrobić, ustaw przełącznik **Włącz zasady** na wartość **nie** , a następnie kliknij przycisk **Zapisz** .
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Wyłącz określone zasady dostępu warunkowego

Aby sprawdzić lub zweryfikować pojedyncze zasady dostępu warunkowego:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz **rozszerzenie Azure Active Directory** , klikając pozycję **wszystkie usługi** w górnej części menu nawigacji po lewej stronie.

3.  Wpisz ciąg **"Azure Active Directory**" w polu wyszukiwania filtru i wybierz element **Azure Active Directory** .

4.  Kliknij pozycję **aplikacje przedsiębiorstwa** w menu nawigacji.

5.  Kliknij element nawigacji **dostępu warunkowego** .

6.  Kliknij odpowiednie zasady, które chcesz sprawdzić.

7.  Wyłącz zasady, ustawiając przełącznik **Włącz zasady** na wartość **nie** , a następnie kliknij przycisk **Zapisz** .

## <a name="problems-with-application-consent"></a>Problemy z zgodą aplikacji

Dostęp do aplikacji można zablokować, ponieważ nie nastąpiła odpowiednia operacja zgody na uprawnienia. Poniżej przedstawiono kilka sposobów rozwiązywania problemów z zgodą aplikacji i ich rozwiązania:

-   [Wykonywanie operacji wyrażania zgody na poziomie użytkownika](#perform-a-user-level-consent-operation)

-   [Wykonywanie operacji zgody na poziomie administratora dla dowolnej aplikacji](#perform-administrator-level-consent-operation-for-any-application)

-   [Wykonaj zgodę na poziomie administratora dla aplikacji z jedną dzierżawą](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Wykonywanie zgody na poziomie administratora dla aplikacji z wieloma dzierżawcami](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Wykonywanie operacji wyrażania zgody na poziomie użytkownika

-   Dla każdej aplikacji z obsługą połączenia typu Open ID, która żąda uprawnień, przechodzenie do ekranu logowania aplikacji wykonuje na poziomie użytkownika zgodę na aplikację zalogowanego użytkownika.

-   Jeśli chcesz to zrobić programowo, zobacz [żądanie zgody poszczególnych użytkowników](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#requesting-individual-user-consent).

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Wykonywanie operacji zgody na poziomie administratora dla dowolnej aplikacji

-   W przypadku **aplikacji utworzonych przy użyciu modelu aplikacji V1**można wymusić zgodę na poziom tego administratora, dodając "**? monit = \_ zgoda administratora**" na końcu adresu URL logowania aplikacji.

-   Dla **każdej aplikacji opracowanej przy użyciu modelu aplikacji w wersji 2**można wymusić tę zgodę na poziomie administratora, postępując zgodnie z instrukcjami w sekcji **żądanie uprawnień z administratora katalogu** w [punkcie końcowym zgody administratora](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Wykonaj zgodę na poziomie administratora dla aplikacji z jedną dzierżawą

-   W przypadku **aplikacji z jedną dzierżawą** , które żądają uprawnień (takich jak te, które są opracowywane lub są używane w organizacji), można wykonać operacji **zgody na poziomie administratora** w imieniu wszystkich użytkowników, logując się jako Administrator globalny i klikając przycisk **Udziel uprawnień** w górnej części **rejestru aplikacji — &gt; wszystkie aplikacje — &gt; Wybierz okienko &gt; uprawnienia wymagane dla aplikacji** .

-   Dla **każdej aplikacji opracowanej przy użyciu modelu aplikacji w wersji 1 lub 2**można wymusić zgodę na dostęp na poziomie administratora, postępując zgodnie z instrukcjami w sekcji **żądanie uprawnień z administratora katalogu** w [punkcie końcowym zgody administratora](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Wykonywanie zgody na poziomie administratora dla aplikacji z wieloma dzierżawcami

-   W przypadku **aplikacji wielodostępnych** , które żądają uprawnień (takich jak aplikacja innej firmy lub firma Microsoft, opracowuje), można wykonać operację **wyrażania zgody na poziomie administratora** . Zaloguj się jako Administrator globalny i kliknij przycisk **Udziel uprawnień** w obszarze **aplikacje dla przedsiębiorstw — &gt; wszystkie aplikacje — Wybierz okienko &gt; &gt; uprawnienia aplikacji** (dostępne wkrótce).

-   Możesz również wymusić tę zgodę na poziomie administratora, postępując zgodnie z instrukcjami podanymi w sekcji **żądanie uprawnień z administratora katalogu** w [punkcie końcowym zgody administratora](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint).

## <a name="next-steps"></a>Następne kroki
[Korzystanie z punktu końcowego zgody administratora](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes#using-the-admin-consent-endpoint)

