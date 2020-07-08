---
title: Problem z logowaniem do witryny sieci Web panelu dostępu | Microsoft Docs
description: Wskazówki dotyczące rozwiązywania problemów, które mogą wystąpić podczas próby zalogowania się w celu korzystania z panelu dostępu
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
ms.date: 07/11/2017
ms.author: kenwith
ms.reviwer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f2ad4db231c616b3022ecafc62b12d6d81b67fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84760817"
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>Problem z logowaniem do witryny sieci Web panelu dostępu

Panel dostępu jest portalem opartym na sieci Web, który umożliwia użytkownikowi, który ma konto służbowe w usłudze Azure Active Directory (Azure AD) wyświetlanie i uruchamianie aplikacji opartych na chmurze, do których administrator usługi Azure AD udzielił im dostępu. Użytkownik, który ma wersje usługi Azure AD, może również korzystać z funkcji samoobsługowego zarządzania grupami i aplikacjami za pomocą panelu dostępu. Panel dostępu jest oddzielony od Azure Portal i nie wymaga, aby użytkownicy mieli subskrypcję platformy Azure.

Użytkownicy mogą zalogować się do panelu dostępu, jeśli mają konto służbowe w usłudze Azure AD.

-   Użytkownicy mogą być uwierzytelniani bezpośrednio przez usługę Azure AD.

-   Użytkownicy mogą być uwierzytelniani przy użyciu Active Directory Federation Services (AD FS).

-   Użytkownicy mogą uwierzytelniać się za pomocą systemu Windows Server Active Directory.

Jeśli użytkownik ma subskrypcję platformy Azure lub pakietu Office 365 i korzysta z niej Azure Portal lub aplikacji pakietu Office 365, będzie można bezproblemowo korzystać z panelu dostępu bez konieczności ponownego logowania. Użytkownicy, którzy nie są uwierzytelniani, są monitowani o zalogowanie się przy użyciu nazwy użytkownika i hasła do konta w usłudze Azure AD. Jeśli organizacja ma skonfigurowane Federacji, wpisz odpowiednią nazwę użytkownika.

## <a name="general-issues-to-check-first"></a>Ogólne problemy do sprawdzenia w pierwszej kolejności 

-   Upewnij się, że użytkownik loguje się do **poprawnego adresu URL**:<https://myapps.microsoft.com>

-   Upewnij się, że przeglądarka użytkownika dodała adres URL do swoich **zaufanych witryn**

-   Upewnij się, że konto użytkownika jest **włączone** do logowania.

-   Upewnij się, że konto użytkownika **nie jest zablokowane.**

-   Upewnij się, że **hasło użytkownika nie wygasło lub zostało zapomniane.**

-   Upewnij się, że **Multi-Factor Authentication** nie blokuje dostępu użytkownika.

-   Upewnij się, że **zasady dostępu warunkowego** lub zasady **ochrony tożsamości** nie blokują dostępu użytkownika.

-   Upewnij się, że **informacje kontaktowe uwierzytelniania** użytkownika są aktualne, aby zezwolić na wymuszanie zasad dostępu Multi-Factor Authentication lub warunkowego.

-   Upewnij się, że wyczyścisz również pliki cookie w przeglądarce i spróbujesz ponownie zalogować.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Wymagania dotyczące przeglądarki dla panelu dostępu

Panel dostępu wymaga przeglądarki obsługującej język JavaScript i ma włączoną obsługę CSS. Aby korzystać z logowania jednokrotnego opartego na hasłach (SSO) w panelu dostępu, rozszerzenie panelu dostępu musi być zainstalowane w przeglądarce użytkownika. To rozszerzenie jest pobierane automatycznie, gdy użytkownik wybierze aplikację, która jest skonfigurowana dla logowania jednokrotnego opartego na hasłach.

W przypadku logowania jednokrotnego opartego na haśle przeglądarki użytkownika końcowego mogą:

-   Internet Explorer 8, 9, 10, 11 — w systemie Windows 7 lub nowszym

-   Microsoft Edge w systemie Windows 10 w wersji rocznicowej lub nowszej 

-   Chrome — w systemie Windows 7 lub nowszym oraz w systemie MacOS X lub nowszym

-   Firefox 26,0 lub nowszy — w systemie Windows XP z dodatkiem SP2 lub nowszym oraz na Mac OS X 10,6 lub nowszym


## <a name="problems-with-the-users-account"></a>Problemy związane z kontem użytkownika

Dostęp do panelu dostępu można zablokować z powodu problemu z kontem użytkownika. Poniżej przedstawiono kilka sposobów rozwiązywania problemów z użytkownikami i ich ustawień konta:

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

   >[!NOTE]
   >Jeśli użytkownik jest w stanie **wymuszonym** , można ustawić je jako czasowo **wyłączone** , aby umożliwić im powrót do konta. Po ponownym uruchomieniu programu można zmienić jego stan na ponownie **włączony** , aby wymagać od nich ponownego zarejestrowania informacji kontaktowych podczas kolejnego logowania. Alternatywnie możesz wykonać kroki opisane w [informacji kontaktowych dotyczących uwierzytelniania użytkownika](#check-a-users-authentication-contact-info) , aby sprawdzić lub ustawić dla nich te dane.
   >
   >

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

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Jeśli te kroki rozwiązywania problemów nie rozwiążą problemu

Otwórz bilet pomocy technicznej z następującymi informacjami, jeśli są dostępne:

-   Identyfikator błędu korelacji

-   Nazwa UPN (adres e-mail użytkownika)

-   Identyfikator dzierżawy

-   Typ przeglądarki

-   Strefa czasowa i czas/przedział czasu w trakcie wystąpienia błędu

-   Ślady programu Fiddler

## <a name="next-steps"></a>Następne kroki
[Zapewnianie logowania jednokrotnego do aplikacji za pomocą serwera proxy aplikacji](application-proxy-configure-single-sign-on-with-kcd.md)
