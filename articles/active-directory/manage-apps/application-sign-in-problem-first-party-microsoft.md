---
title: Problemy z logowaniem do aplikacji firmy Microsoft | Microsoft Docs
description: Rozwiązywanie typowych problemów napotykanych podczas logowania się do aplikacji firmy Microsoft przy użyciu usługi Azure AD (takich jak Microsoft 365).
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/10/2018
ms.author: iangithinji
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c56e356a9bacc6479d3a3a33be905457c26e732e
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378180"
---
# <a name="problems-signing-in-to-a-microsoft-application"></a>Problemy z logowaniem do aplikacji firmy Microsoft

Aplikacje firmy Microsoft (takie jak Exchange, SharePoint, Yammer itp.) są przypisywane i zarządzane nieco inaczej niż aplikacje SaaS innych firm lub inne aplikacje integrowane z usługą Azure AD w celu logowania jednokrotnego.

Istnieją trzy główne sposoby uzyskiwania dostępu przez użytkownika do aplikacji opublikowanej przez firmę Microsoft.

-   W przypadku aplikacji w Microsoft 365 lub innych płatnych pakietów  użytkownicy mają dostęp za pośrednictwem przypisania licencji bezpośrednio do konta użytkownika lub za pośrednictwem grupy przy użyciu funkcji przypisywania licencji opartej na grupach.

-   W przypadku aplikacji publikowanych bezpłatnie przez firmę Microsoft lub inne osoby użytkownicy mogą uzyskać dostęp za pośrednictwem **zgody użytkownika.** Oznacza to, że logują się do aplikacji przy użyciu konta służbowego usługi Azure AD i umożliwiają jej dostęp do pewnego ograniczonego zestawu danych na koncie.

-   W przypadku aplikacji publikowanych bezpłatnie przez firmę Microsoft lub inne osoby, użytkownikom można również przyznać dostęp za pośrednictwem **zgody administratora.** Oznacza to, że administrator ustalił, że aplikacja może być używana przez wszystkich użytkowników w organizacji, dlatego loguje się do aplikacji przy użyciu konta administratora globalnego i udziela dostępu wszystkim użytkownikom w organizacji.

Aby rozwiązać problem, [](#general-problem-areas-with-application-access-to-consider) rozpocznij od tematu Ogólne obszary problemów z dostępem do aplikacji, które należy wziąć pod uwagę, a następnie zapoznaj się z tematem Przewodnik: kroki rozwiązywania problemów z dostępem do aplikacji firmy Microsoft, aby uzyskać szczegółowe informacje.

## <a name="general-problem-areas-with-application-access-to-consider"></a>Ogólne obszary problemów z dostępem do aplikacji, które należy wziąć pod uwagę

Poniżej znajduje się lista ogólnych obszarów problemów, do których można przejść, jeśli wiesz, od czego zacząć, ale zalecamy przeczytanie przewodnika, aby szybko rozpocząć: Przewodnik: Kroki rozwiązywania problemów z dostępem do aplikacji firmy Microsoft.

-   [Problemy z kontem użytkownika](#problems-with-the-users-account)

-   [Problemy z grupami](#problems-with-groups)

-   [Problemy z zasadami dostępu warunkowego](#problems-with-conditional-access-policies)

-   [Problemy ze zgodą aplikacji](#problems-with-application-consent)

## <a name="steps-to-troubleshoot-microsoft-application-access"></a>Kroki rozwiązywania problemów z dostępem do aplikacji firmy Microsoft

Poniżej przedstawiono niektóre typowe problemy, które mogą się dzieje, gdy użytkownicy nie mogą zalogować się do aplikacji firmy Microsoft.

- Ogólne problemy, które należy najpierw sprawdzić

  * Upewnij się, że użytkownik loguje się pod **prawidłowym adresem URL,** a nie adresem URL aplikacji lokalnej.

  * Upewnij się, że konto użytkownika nie **jest zablokowane.**

  * Upewnij **się, że konto użytkownika istnieje w** Azure Active Directory. [Sprawdź, czy konto użytkownika istnieje w Azure Active Directory](#problems-with-the-users-account)

  * Upewnij się, że dla konta użytkownika **włączono** logowanie. [Sprawdzanie stanu konta użytkownika](#problems-with-the-users-account)

  * Upewnij się, że hasło **użytkownika nie wygasło ani nie zostało zapomniane.** [Resetowanie hasła użytkownika lub](#reset-a-users-password) Włączanie [samoobsługowego resetowania hasła](../authentication/tutorial-enable-sspr.md)

  * Upewnij **się, że uwierzytelnianie wieloskładnikowe** nie blokuje dostępu użytkowników. [Sprawdź stan uwierzytelniania wieloskładnikowego użytkownika](#check-a-users-multi-factor-authentication-status) lub Sprawdź informacje kontaktowe [użytkownika dotyczące uwierzytelniania](#check-a-users-authentication-contact-info)

  * Upewnij się, **że zasady dostępu warunkowego** lub **zasady ochrony tożsamości** nie blokują dostępu użytkowników. [Sprawdź określone zasady dostępu warunkowego](#problems-with-conditional-access-policies) lub Sprawdź [zasady](#check-a-specific-applications-conditional-access-policy) dostępu warunkowego określonej aplikacji lub [Wyłącz określone zasady dostępu warunkowego](#disable-a-specific-conditional-access-policy)

  * Upewnij się, że **informacje** kontaktowe użytkownika dotyczące uwierzytelniania są aktualne, aby umożliwić wymuszanie zasad uwierzytelniania wieloskładnikowego lub dostępu warunkowego. [Sprawdź stan uwierzytelniania wieloskładnikowego użytkownika](#check-a-users-multi-factor-authentication-status) lub Sprawdź informacje kontaktowe [użytkownika dotyczące uwierzytelniania](#check-a-users-authentication-contact-info)

- W przypadku **aplikacji firmy** **Microsoft,** które wymagają licencji (takich jak Office365), poniżej znajdują się pewne konkretne problemy, które należy sprawdzić po wykluczyć powyższe ogólne problemy:

  * Upewnij się, że użytkownik lub ma **przypisaną licencję.** [Sprawdzanie przypisanych licencji użytkownika](#check-a-users-assigned-licenses) lub [Sprawdzanie przypisanych](#check-a-groups-assigned-licenses) licencji grupy

  * Jeśli licencja jest **przypisana do grupy** **statycznej,** upewnij się, że użytkownik **jest** członkiem tej grupy. [Sprawdzanie członkostwa użytkownika w grupach](#check-a-users-group-memberships)

  * Jeśli licencja jest **przypisana do grupy** **dynamicznej,** upewnij się, że reguła grupy dynamicznej **jest prawidłowo ustawiona.** [Sprawdzanie kryteriów członkostwa grupy dynamicznej](#check-a-dynamic-groups-membership-criteria)

  * Jeśli licencja jest **przypisana** do grupy dynamicznej,  upewnij się, że grupa dynamiczna zakończyła przetwarzanie członkostwa i że użytkownik jest członkiem **(może** to trochę potrwać).  [Sprawdzanie członkostwa użytkownika w grupach](#check-a-users-group-memberships)

  *  Po upewnianiu się, że licencja jest przypisana, upewnij się, że licencja **nie wygasła.**

  *  Upewnij się, że licencja **jest dla aplikacji,** do których uzyskuje dostęp.

- W przypadku aplikacji firmy **Microsoft,** **które nie wymagają licencji,** należy sprawdzić kilka innych rzeczy:

  * Jeśli aplikacja żąda uprawnień na poziomie użytkownika **(na** przykład "Uzyskaj dostęp do skrzynki pocztowej tego użytkownika"),  upewnij się, że użytkownik zalogował się do aplikacji i wykonał operację wyrażania zgody na poziomie użytkownika, aby pozwolić aplikacji na dostęp do jego danych.

  * Jeśli aplikacja żąda uprawnień na poziomie administratora **(na** przykład "Dostęp do skrzynek pocztowych wszystkich użytkowników"), upewnij się, że administrator globalny wykonał operację zgody na poziomie administratora w **imieniu** wszystkich użytkowników w organizacji.

## <a name="problems-with-the-users-account"></a>Problemy z kontem użytkownika

Dostęp do aplikacji może zostać zablokowany z powodu problemu z użytkownikiem przypisanym do aplikacji. Poniżej przedstawiono kilka sposobów rozwiązywania i rozwiązywania problemów z użytkownikami i ich ustawieniami konta:

-   [Sprawdź, czy konto użytkownika istnieje w Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [Sprawdzanie stanu konta użytkownika](#check-a-users-account-status)

-   [Resetowanie hasła użytkownika](#reset-a-users-password)

-   [Włączanie samoobsługowego resetowania hasła](#enable-self-service-password-reset)

-   [Sprawdzanie stanu uwierzytelniania wieloskładnikowego użytkownika](#check-a-users-multi-factor-authentication-status)

-   [Sprawdzanie informacji kontaktowych uwierzytelniania użytkownika](#check-a-users-authentication-contact-info)

-   [Sprawdzanie członkostwa użytkownika w grupach](#check-a-users-group-memberships)

-   [Sprawdzanie przypisanych licencji użytkownika](#check-a-users-assigned-licenses)

-   [Przypisywanie użytkownikowi licencji](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Sprawdź, czy konto użytkownika istnieje w Azure Active Directory

Aby sprawdzić, czy konto użytkownika jest obecne, wykonaj następujące kroki:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz rozszerzenie **Azure Active Directory,** klikając pozycję **Wszystkie usługi** w górnej części głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory"** w polu wyszukiwania filtru i wybierz **Azure Active Directory** element.

4.  Kliknij **pozycję Użytkownicy i grupy** w menu nawigacji.

5.  kliknij **pozycję Wszyscy użytkownicy.**

6.  **Wyszukaj** użytkownika, który Cię interesuje, a **następnie kliknij wiersz do** wybrania.

7.  Sprawdź właściwości obiektu użytkownika, aby upewnić się, że wyglądają one zgodnie z oczekiwaniami i nie brakuje żadnych danych.

### <a name="check-a-users-account-status"></a>Sprawdzanie stanu konta użytkownika

Aby sprawdzić stan konta użytkownika, wykonaj następujące kroki:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz rozszerzenie **Azure Active Directory,** klikając pozycję **Wszystkie usługi** w górnej części głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory"** w polu wyszukiwania filtru i wybierz **Azure Active Directory** element.

4.  Kliknij **pozycję Użytkownicy i grupy** w menu nawigacji.

5.  kliknij **pozycję Wszyscy użytkownicy.**

6.  **Wyszukaj** użytkownika, który Cię interesuje, a **następnie kliknij wiersz do** wybrania.

7.  kliknij **pozycję Profil.**

8.  W **obszarze Ustawienia** upewnij się, że ustawienie **Blokuj logowanie** ma wartość **Nie.**

### <a name="reset-a-users-password"></a>Resetowanie hasła użytkownika

Aby zresetować hasło użytkownika, wykonaj następujące kroki:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz rozszerzenie **Azure Active Directory,** klikając pozycję **Wszystkie usługi** w górnej części głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory"** w polu wyszukiwania filtru i wybierz **Azure Active Directory** element.

4.  Kliknij **pozycję Użytkownicy i grupy** w menu nawigacji.

5.  kliknij **pozycję Wszyscy użytkownicy.**

6.  **Wyszukaj** użytkownika, który Cię interesuje, **a następnie kliknij wiersz do** wybrania.

7.  Kliknij przycisk **Resetuj** hasło w górnej części okienka użytkownika.

8.  Kliknij przycisk **Resetuj** hasło w **wyświetlonym okienku** Resetowanie hasła.

9.  Skopiuj hasło **tymczasowe** lub **wprowadź nowe hasło** dla użytkownika.

10. Przekaż to nowe hasło użytkownikowi. Musi zmienić to hasło podczas następnego logowania, aby Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Włączanie samoobsługowego resetowania hasła

Aby włączyć samoobsługowe resetowanie hasła, wykonaj poniższe kroki wdrażania:

-   [Umożliwianie użytkownikom resetowania Azure Active Directory haseł](../authentication/tutorial-enable-sspr.md)

-   [Umożliwianie użytkownikom resetowania lub zmieniania haseł lokalnych usługi Active Directory](../authentication/tutorial-enable-sspr.md)

### <a name="check-a-users-multi-factor-authentication-status"></a>Sprawdzanie stanu uwierzytelniania wieloskładnikowego użytkownika

Aby sprawdzić stan uwierzytelniania wieloskładnikowego użytkownika, wykonaj następujące kroki:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz rozszerzenie **Azure Active Directory,** klikając pozycję **Wszystkie usługi** w górnej części głównego menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory"** w polu wyszukiwania filtru i wybierz **Azure Active Directory** element.

4. Kliknij **pozycję Użytkownicy i grupy** w menu nawigacji.

5. kliknij **pozycję Wszyscy użytkownicy.**

6. Kliknij przycisk **Multi-Factor Authentication** w górnej części okienka.

7. Po **zakończeniu ładowania portalu administracyjnego usługi Multi-Factor Authentication** upewnij się, że jesteś na **karcie** Użytkownicy.

8. Znajdź użytkownika na liście użytkowników, wyszukując, filtrując lub sortując.

9. Wybierz użytkownika z listy użytkowników i **włącz,** **wyłącz** lub  wymusz uwierzytelnianie wieloskładnikowe zgodnie z potrzebami.

   * **Uwaga:** jeśli użytkownik jest w stanie **Wymuszone,** możesz tymczasowo ustawić go na wartość **Wyłączone,** aby wrócić do swojego konta. Po powrocie możesz ponownie zmienić ich stan  na Włączone, aby wymagać od nich ponownego zarejestrowania swoich informacji kontaktowych podczas następnego logowania. Alternatywnie możesz wykonać kroki opisane w te tematu [Sprawdzanie](#check-a-users-authentication-contact-info) informacji kontaktowych dotyczących uwierzytelniania użytkownika, aby zweryfikować lub ustawić dla nich te dane.

### <a name="check-a-users-authentication-contact-info"></a>Sprawdzanie informacji kontaktowych uwierzytelniania użytkownika

Aby sprawdzić informacje kontaktowe użytkownika dotyczące uwierzytelniania używane do uwierzytelniania wieloskładnikowego, dostępu warunkowego, ochrony tożsamości i resetowania hasła, wykonaj następujące kroki:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz rozszerzenie **Azure Active Directory,** klikając pozycję **Wszystkie usługi** w górnej części głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory"** w polu wyszukiwania filtru i wybierz **Azure Active Directory** element.

4.  Kliknij **pozycję Użytkownicy i grupy** w menu nawigacji.

5.  kliknij **pozycję Wszyscy użytkownicy.**

6.  **Wyszukaj** użytkownika, który Cię interesuje, a **następnie kliknij wiersz do** wybrania.

7.  kliknij **pozycję Profil.**

8.  Przewiń w dół do **informacji kontaktowych uwierzytelniania.**

9.  **Przejrzyj** dane zarejestrowane dla użytkownika i zaktualizuj je zgodnie z potrzebami.

### <a name="check-a-users-group-memberships"></a>Sprawdzanie członkostwa użytkownika w grupach

Aby sprawdzić członkostwo użytkownika w grupach, wykonaj następujące kroki:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz rozszerzenie **Azure Active Directory,** klikając pozycję **Wszystkie usługi** w górnej części głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory"** w polu wyszukiwania filtru i wybierz **Azure Active Directory** element.

4.  Kliknij **pozycję Użytkownicy i grupy** w menu nawigacji.

5.  kliknij **pozycję Wszyscy użytkownicy.**

6.  **Wyszukaj** użytkownika, który Cię interesuje, a **następnie kliknij wiersz do** wybrania.

7.  Kliknij **pozycję** Grupy, aby zobaczyć, do których grup należy użytkownik.

### <a name="check-a-users-assigned-licenses"></a>Sprawdzanie przypisanych licencji użytkownika

Aby sprawdzić przypisane licencje użytkownika, wykonaj następujące kroki:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz rozszerzenie **Azure Active Directory,** klikając pozycję **Wszystkie usługi** w górnej części głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory"** w polu wyszukiwania filtru i wybierz **Azure Active Directory** element.

4.  Kliknij **pozycję Użytkownicy i grupy** w menu nawigacji.

5.  kliknij **pozycję Wszyscy użytkownicy.**

6.  **Wyszukaj** użytkownika, który Cię interesuje, a **następnie kliknij wiersz do** wybrania.

7.  kliknij **pozycję Licencje,** aby zobaczyć, które licencje aktualnie przypisano użytkownikowi.

### <a name="assign-a-user-a-license"></a>Przypisywanie użytkownikowi licencji 

Aby przypisać licencję do użytkownika, wykonaj następujące kroki:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz rozszerzenie **Azure Active Directory,** klikając pozycję **Wszystkie usługi** w górnej części głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory"** w polu wyszukiwania filtru i wybierz **Azure Active Directory** element.

4.  Kliknij **pozycję Użytkownicy i grupy** w menu nawigacji.

5.  kliknij **pozycję Wszyscy użytkownicy.**

6.  **Wyszukaj** użytkownika, który Cię interesuje, a **następnie kliknij wiersz do** wybrania.

7.  kliknij **pozycję Licencje,** aby zobaczyć, które licencje aktualnie przypisano użytkownikowi.

8.  kliknij przycisk **Przypisz.**

9.  Wybierz **co najmniej jeden produkt** z listy dostępnych produktów.

10. **Opcjonalnie** kliknij **element opcji przypisania,** aby dokładnie przypisać produkty. Po **zakończeniu** kliknij przycisk OK.

11. Kliknij przycisk **Przypisz,** aby przypisać te licencje do tego użytkownika.

## <a name="problems-with-groups"></a>Problemy z grupami

Dostęp do aplikacji może zostać zablokowany z powodu problemu z grupą przypisaną do aplikacji. Poniżej przedstawiono kilka sposobów rozwiązywania i rozwiązywania problemów z grupami i członkostwem w grupach:

-   [Sprawdzanie członkostwa w grupie](#check-a-groups-membership)

-   [Sprawdzanie kryteriów członkostwa grupy dynamicznej](#check-a-dynamic-groups-membership-criteria)

-   [Sprawdzanie przypisanych licencji grupy](#check-a-groups-assigned-licenses)

-   [Ponowne przetwarzanie licencji grupy](#reprocess-a-groups-licenses)

-   [Przypisywanie licencji grupie](#assign-a-group-a-license)

### <a name="check-a-groups-membership"></a>Sprawdzanie członkostwa w grupie

Aby sprawdzić członkostwo grupy, wykonaj następujące kroki:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz rozszerzenie **Azure Active Directory,** klikając pozycję **Wszystkie usługi** w górnej części głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory"** w polu wyszukiwania filtru i wybierz **Azure Active Directory** element.

4.  Kliknij **pozycję Użytkownicy i grupy** w menu nawigacji.

5.  kliknij **pozycję Wszystkie grupy.**

6.  **Wyszukaj** grupę, która Cię interesuje, a **następnie kliknij wiersz do** wybrania.

7.  kliknij **pozycję** Członkowie, aby przejrzeć listę użytkowników przypisanych do tej grupy.

### <a name="check-a-dynamic-groups-membership-criteria"></a>Sprawdzanie kryteriów członkostwa grupy dynamicznej 

Aby sprawdzić kryteria członkostwa grupy dynamicznej, wykonaj następujące kroki:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz rozszerzenie **Azure Active Directory,** klikając pozycję **Wszystkie usługi** w górnej części głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory"** w polu wyszukiwania filtru i wybierz **Azure Active Directory** element.

4.  Kliknij **pozycję Użytkownicy i grupy** w menu nawigacji.

5.  kliknij **pozycję Wszystkie grupy.**

6.  **Wyszukaj** grupę, która Cię interesuje, **a następnie kliknij wiersz do** wybrania.

7.  kliknij **pozycję Reguły członkostwa dynamicznego.**

8.  Przejrzyj **prostą lub** **zaawansowaną** regułę zdefiniowaną dla tej grupy i upewnij się, że użytkownik, który ma być członkiem tej grupy, spełnia te kryteria.

### <a name="check-a-groups-assigned-licenses"></a>Sprawdzanie przypisanych licencji grupy

Aby sprawdzić przypisane licencje grupy, wykonaj następujące kroki:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz rozszerzenie **Azure Active Directory,** klikając pozycję **Wszystkie usługi** w górnej części głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory"** w polu wyszukiwania filtru i wybierz **Azure Active Directory** element.

4.  Kliknij **pozycję Użytkownicy i grupy** w menu nawigacji.

5.  kliknij **pozycję Wszystkie grupy.**

6.  **Wyszukaj** grupę, która Cię interesuje, a **następnie kliknij wiersz do** wybrania.

7.  kliknij **pozycję Licencje,** aby zobaczyć, które licencje są aktualnie przypisane do grupy.

### <a name="reprocess-a-groups-licenses"></a>Ponowne przetwarzanie licencji grupy

Aby ponownie przetworzyć przypisane licencje grupy, wykonaj następujące kroki:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz rozszerzenie **Azure Active Directory,** klikając pozycję **Wszystkie usługi** w górnej części głównego menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory"** w polu wyszukiwania filtru i wybierz **Azure Active Directory** element.

4. Kliknij **pozycję Użytkownicy i grupy** w menu nawigacji.

5. kliknij **pozycję Wszystkie grupy.**

6. **Wyszukaj** grupę, która Cię **interesuje,** a następnie kliknij wiersz do wybrania.

7. Kliknij **pozycję Licencje,** aby zobaczyć, które licencje są aktualnie przypisane do grupy.

8. Kliknij przycisk **Przetworz** ponownie, aby upewnić się, że licencje przypisane do członków tej grupy są aktualne. Może to zająć dużo czasu, w zależności od rozmiaru i złożoności grupy.

   >[!NOTE]
   >Aby to zrobić szybciej, rozważ tymczasowe przypisanie licencji bezpośrednio do użytkownika. [Przypisz użytkownikowi licencję](#problems-with-application-consent).
   >
   >

### <a name="assign-a-group-a-license"></a>Przypisywanie licencji do grupy

Aby przypisać licencję do grupy, wykonaj następujące kroki:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz rozszerzenie **Azure Active Directory,** klikając pozycję **Wszystkie usługi** w górnej części głównego menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory"** w polu wyszukiwania filtru i wybierz **Azure Active Directory** element.

4. Kliknij **pozycję Użytkownicy i grupy** w menu nawigacji.

5. kliknij **pozycję Wszystkie grupy.**

6. **Wyszukaj** grupę, która Cię **interesuje,** a następnie kliknij wiersz do wybrania.

7. Kliknij **pozycję Licencje,** aby zobaczyć, które licencje są aktualnie przypisane do grupy.

8. kliknij przycisk **Przypisz.**

9. Wybierz **co najmniej jeden produkt** z listy dostępnych produktów.

10. **Opcjonalnie** kliknij **element opcji przypisania,** aby dokładnie przypisać produkty. Po **zakończeniu** kliknij przycisk OK.

11. Kliknij przycisk **Przypisz,** aby przypisać te licencje do tej grupy. Może to zająć dużo czasu, w zależności od rozmiaru i złożoności grupy.

    >[!NOTE]
    >Aby to zrobić szybciej, rozważ tymczasowe przypisanie licencji bezpośrednio do użytkownika. [Przypisz użytkownikowi licencję](#problems-with-application-consent).
    > 
    >

## <a name="problems-with-conditional-access-policies"></a>Problemy z zasadami dostępu warunkowego

### <a name="check-a-specific-conditional-access-policy"></a>Sprawdzanie określonych zasad dostępu warunkowego

Aby sprawdzić lub zweryfikować pojedyncze zasady dostępu warunkowego:

1. Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2. Otwórz rozszerzenie **Azure Active Directory,** klikając pozycję **Wszystkie usługi** w górnej części głównego menu nawigacji po lewej stronie.

3. Wpisz **"Azure Active Directory"** w polu wyszukiwania filtru i wybierz **Azure Active Directory** element.

4. Kliknij **pozycję Aplikacje dla** przedsiębiorstw w menu nawigacji.

5. kliknij element **nawigacji Dostęp warunkowy.**

6. kliknij zasady, które chcesz sprawdzić.

7. Sprawdź, czy nie ma określonych warunków, przypisań ani innych ustawień, które mogą blokować dostęp użytkowników.

   >[!NOTE]
   >Możesz tymczasowo wyłączyć te zasady, aby upewnić się, że nie mają one wpływu na logowania. W tym celu ustaw przełącznik **Włącz zasady** na wartość **Nie** i kliknij **przycisk** Zapisz.
   >
   >

### <a name="check-a-specific-applications-conditional-access-policy"></a>Sprawdzanie zasad dostępu warunkowego określonej aplikacji

Aby sprawdzić lub zweryfikować aktualnie skonfigurowane zasady dostępu warunkowego jednej aplikacji:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz rozszerzenie **Azure Active Directory,** klikając pozycję **Wszystkie usługi** w górnej części głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory"** w polu wyszukiwania filtru i wybierz **Azure Active Directory** element.

4.  Kliknij **pozycję Aplikacje dla przedsiębiorstw** w menu nawigacji.

5.  kliknij **pozycję Wszystkie aplikacje.**

6.  Wyszukaj aplikację, która Cię interesuje, lub użytkownik próbuje się zalogować, korzystając z nazwy wyświetlanej aplikacji lub identyfikatora aplikacji.

     >[!NOTE]
     >Jeśli nie widzisz szukanych aplikacji, kliknij przycisk  Filtruj i rozwiń zakres listy do listy **Wszystkie aplikacje.** Jeśli chcesz wyświetlić więcej kolumn, kliknij przycisk **Kolumny,** aby dodać dodatkowe szczegóły dla aplikacji.
     >
     >

7.  kliknij element **nawigacji Dostęp warunkowy.**

8.  kliknij zasady, które chcesz sprawdzić.

9.  Sprawdź, czy nie ma określonych warunków, przypisań ani innych ustawień, które mogą blokować dostęp użytkowników.

     >[!NOTE]
     >Możesz tymczasowo wyłączyć te zasady, aby upewnić się, że nie mają one wpływu na logowania. W tym celu ustaw przełącznik Włącz **zasady** na **nie** i kliknij **przycisk** Zapisz.
     >
     >

### <a name="disable-a-specific-conditional-access-policy"></a>Wyłączanie określonych zasad dostępu warunkowego

Aby sprawdzić lub zweryfikować pojedyncze zasady dostępu warunkowego:

1.  Otwórz [**Azure Portal**](https://portal.azure.com/) i zaloguj się jako **administrator globalny.**

2.  Otwórz rozszerzenie **Azure Active Directory,** klikając pozycję **Wszystkie usługi** w górnej części głównego menu nawigacji po lewej stronie.

3.  Wpisz **"Azure Active Directory"** w polu wyszukiwania filtru i wybierz **Azure Active Directory** element.

4.  Kliknij **pozycję Aplikacje dla przedsiębiorstw** w menu nawigacji.

5.  kliknij element **nawigacji Dostęp warunkowy.**

6.  kliknij zasady, które chcesz sprawdzić.

7.  Wyłącz zasady, ustawiając przełącznik **Włącz zasady** na **Nie** i klikając **przycisk** Zapisz.

## <a name="problems-with-application-consent"></a>Problemy ze zgodą aplikacji

Dostęp do aplikacji może zostać zablokowany, ponieważ nie nastąpiła operacja wyrażania zgody na odpowiednie uprawnienia. Poniżej przedstawiono kilka sposobów rozwiązywania problemów ze zgodą aplikacji:

-   [Wykonywanie operacji wyrażania zgody na poziomie użytkownika](#perform-a-user-level-consent-operation)

-   [Wykonywanie operacji wyrażania zgody na poziomie administratora dla dowolnej aplikacji](#perform-administrator-level-consent-operation-for-any-application)

-   [Wyrażanie zgody na poziomie administratora dla aplikacji z jedną dzierżawą](#perform-administrator-level-consent-for-a-single-tenant-application)

-   [Wyrażanie zgody na poziomie administratora dla aplikacji wielodostępnych](#perform-administrator-level-consent-for-a-multi-tenant-application)

### <a name="perform-a-user-level-consent-operation"></a>Wykonywanie operacji wyrażania zgody na poziomie użytkownika

-   W przypadku każdej aplikacji z obsługą programu Open ID Connect, która żąda uprawnień, przejście do ekranu logowania aplikacji wykonuje zgodę na poziomie użytkownika dla aplikacji dla zalogowaowego użytkownika.

-   Jeśli chcesz to zrobić programowo, zobacz Requesting individual user consent (Żądanie [zgody poszczególnych użytkowników).](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent)

### <a name="perform-administrator-level-consent-operation-for-any-application"></a>Wykonywanie operacji wyrażania zgody na poziomie administratora dla dowolnej aplikacji

-   Tylko w przypadku aplikacji opracowanych przy użyciu modelu aplikacji w wersji **1** można wymusić wyrażenie zgody na poziomie administratora, dodając znak **"?prompt=admin \_ consent"** na końcu adresu URL logowania aplikacji.

-   W przypadku dowolnej aplikacji opracowanej przy użyciu modelu aplikacji w wersji **2** można  wymusić wyrażenie zgody na poziomie administratora, zgodnie z instrukcjami w sekcji Żądanie uprawnień od administratora katalogu w sekcji Korzystanie z punktu końcowego zgody [administratora](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint).

### <a name="perform-administrator-level-consent-for-a-single-tenant-application"></a>Wyrażanie zgody na poziomie administratora dla aplikacji z jedną dzierżawą

-   W  przypadku aplikacji z jedną dzierżawą, które żądają uprawnień (takich  jak te, które opracowujesz lub których jesteś właścicielem w  organizacji), możesz wykonać operację wyrażania zgody na poziomie administracyjnym w imieniu wszystkich użytkowników, logując się jako administrator globalny i klikając przycisk Ułaskalij uprawnienia w górnej części okienka Rejestr aplikacji — Wszystkie aplikacje — Wybierz aplikację **&gt; &gt; — &gt; wymagane** uprawnienia.

-   W przypadku dowolnej aplikacji opracowanej przy użyciu modelu aplikacji w wersji 1 lub **2** można wymusić wyrażenie zgody [](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)na poziomie administratora, korzystając z instrukcji w sekcji Żądanie uprawnień od administratora katalogu w tesłudze w tesłudze. 

### <a name="perform-administrator-level-consent-for-a-multi-tenant-application"></a>Wyrażanie zgody na poziomie administratora dla aplikacji wielodostępnych

-   W **przypadku aplikacji wielodostępnych,** które żądają uprawnień (takich jak aplikacja, która jest rozwijana przez firmę Microsoft lub innej firmy), można wykonać operację wyrażania zgody **na poziomie** administracyjnym. Zaloguj się jako administrator globalny  i kliknij przycisk Ułań uprawnienia w obszarze Aplikacje dla przedsiębiorstw — Wszystkie aplikacje — Wybierz aplikację **&gt; &gt; — &gt; uprawnienia** (dostępne wkrótce).

-   Możesz również wymusić tę zgodę na poziomie administratora,  korzystając z instrukcji w sekcji Żądanie uprawnień od administratora katalogu w tesłudze przy użyciu punktu [końcowego zgody administratora](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint).

## <a name="next-steps"></a>Następne kroki
[Korzystanie z punktu końcowego zgody administratora](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint)