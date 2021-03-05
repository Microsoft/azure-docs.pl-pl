---
title: 'Azure AD Connect: uwierzytelnianie w chmurze za pomocą wdrożenia przemieszczanego | Microsoft Docs'
description: W tym artykule wyjaśniono, jak przeprowadzić migrację z uwierzytelniania federacyjnego do uwierzytelniania w chmurze przy użyciu wdrożenia etapowego.
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/03/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2038587aebe8fe98bd1adcccef6b895ec7370656
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102176862"
---
# <a name="migrate-to-cloud-authentication-using-staged-rollout-preview"></a>Migrowanie do uwierzytelniania w chmurze przy użyciu wdrożenia etapowego (wersja zapoznawcza)

Wdrażanie etapowe pozwala na selektywne testowanie grup użytkowników z możliwościami uwierzytelniania w chmurze, takimi jak usługa Azure AD Multi-Factor Authentication (MFA), dostęp warunkowy, Ochrona tożsamości na potrzeby przecieków poświadczeń, zarządzania tożsamościami i innych, przed wycinaniem domen.  W tym artykule omówiono sposób tworzenia przełącznika. Przed rozpoczęciem wdrożenia przemieszczanego należy jednak wziąć pod uwagę konsekwencje, jeśli co najmniej jeden z następujących warunków jest spełniony:
    
-  Obecnie używasz lokalnego serwera Multi-Factor Authentication. 
-  Do uwierzytelniania są używane karty inteligentne. 
-  Bieżący serwer oferuje pewne funkcje tylko federacyjne.

Przed podjęciem próby wykonania tej funkcji zalecamy zapoznanie się z naszym przewodnikiem dotyczącym wybierania właściwej metody uwierzytelniania. Aby uzyskać więcej informacji, zobacz tabelę "Porównanie metod" w obszarze [Wybierz właściwą metodę uwierzytelniania dla Azure Active Directory rozwiązanie do tworzenia tożsamości hybrydowej](./choose-ad-authn.md#comparing-methods).

Aby zapoznać się z omówieniem tej funkcji, zobacz "Azure Active Directory: co to jest wdrażanie etapowe?". plików

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE3inQJ]



## <a name="prerequisites"></a>Wymagania wstępne

-   Masz dzierżawę usługi Azure Active Directory (Azure AD) z domenami federacyjnymi.

-   Podjęto decyzję o przejściu do jednej z dwóch opcji:
    - **Opcja A**  -  *Synchronizacja skrótów haseł (synchronizacja)*  +  *bezproblemowe logowanie jednokrotne (SSO)*.  Aby uzyskać więcej informacji, zobacz [co to jest synchronizacja skrótów haseł](whatis-phs.md) i [co to jest bezproblemowe logowanie jednokrotne](how-to-connect-sso.md)
    - **Opcja B**  -  *uwierzytelnianie przekazywane*  +  *bezproblemowe logowanie jednokrotne*.  Aby uzyskać więcej informacji, zobacz [co to jest uwierzytelnianie przekazywane](how-to-connect-pta.md)  
    
    Chociaż *bezproblemowe logowanie jednokrotne* jest opcjonalne, zalecamy włączenie dla użytkowników, którzy uruchamiają komputery przyłączone do domeny z wewnątrz sieci firmowej.

-   Skonfigurowano wszystkie odpowiednie zasady oznaczania i dostępu warunkowego dla użytkowników, którzy są migrowani do uwierzytelniania w chmurze.

-   Jeśli planujesz korzystanie z usługi Azure AD Multi-Factor Authentication, zalecamy użycie [połączonej rejestracji do samoobsługowego resetowania hasła (SSPR) i Multi-Factor Authentication](../authentication/concept-registration-mfa-sspr-combined.md) , aby użytkownicy mogli rejestrować swoje metody uwierzytelniania. Uwaga — w przypadku używania SSPR do resetowania hasła lub zmiany hasła przy użyciu strony Moje profile podczas wdrażania etapowego Azure AD Connect musi synchronizować nowy skrót hasła, co może potrwać do 2 minut po zresetowaniu.

-   Aby można było korzystać z funkcji wdrażania etapowego, musisz być administratorem globalnym w Twojej dzierżawie.

-   Aby umożliwić *bezproblemowe logowanie jednokrotne* w określonym lesie Active Directory, musisz być administratorem domeny.

-  W przypadku wdrażania hybrydowej usługi Azure AD lub funkcji Azure AD Join należy przeprowadzić uaktualnienie do wersji Windows 10 1903 Update.


## <a name="supported-scenarios"></a>Obsługiwane scenariusze

Poniższe scenariusze są obsługiwane w przypadku wdrażania etapowego. Funkcja działa tylko dla:

- Użytkownicy, którzy są obsługiwani do usługi Azure AD za pomocą Azure AD Connect. Nie dotyczy to użytkowników korzystających tylko z chmury.

- Ruch związany z logowaniem użytkowników w przeglądarkach i nowoczesnych klientach *uwierzytelniania* . Aplikacje lub usługi w chmurze korzystające ze starszego uwierzytelniania będą powracać do przepływów uwierzytelniania federacyjnego. Przykładem może być Usługa Exchange Online z wyłączoną funkcją nowoczesnego uwierzytelniania lub program Outlook 2010, który nie obsługuje nowoczesnego uwierzytelniania.

- Rozmiar grupy jest obecnie ograniczony do 50 000 użytkowników.  Jeśli masz grupy, które są większe niż 50 000 użytkowników, zaleca się poddzielenie tej grupy na wiele grup na potrzeby wdrożenia etapowego.

- Dołączanie hybrydowe systemu Windows 10 lub usługa Azure AD Dołącz do podstawowego tokenu odświeżania bez wglądu w dane do serwera federacyjnego dla systemu Windows 10 w wersji 1903 i nowszej, gdy nazwa UPN użytkownika jest w trakcie routingu, a sufiks domeny jest weryfikowany w usłudze Azure AD.

## <a name="unsupported-scenarios"></a>Nieobsługiwane scenariusze

Następujące scenariusze nie są obsługiwane w przypadku wdrażania etapowego:

- Starsze uwierzytelnianie, takie jak POP3 i SMTP, nie jest obsługiwane.

- Niektóre aplikacje wysyłają parametr zapytania "domain_hint" do usługi Azure AD podczas uwierzytelniania. Te przepływy będą kontynuowane, a użytkownicy włączeni do wdrożenia przemieszczania będą nadal używać Federacji do uwierzytelniania.

<!-- -->

- Administratorzy mogą wdrożyć uwierzytelnianie w chmurze przy użyciu grup zabezpieczeń. Aby uniknąć opóźnienia synchronizacji w przypadku używania lokalnych grup zabezpieczeń Active Directory, zalecamy korzystanie z grup zabezpieczeń w chmurze. Mają zastosowanie następujące warunki:

    - Można użyć maksymalnie 10 grup na funkcję. Oznacza to, że można użyć 10 grup dla każdej *synchronizacji skrótów haseł*, *uwierzytelniania przekazywanego* i *BEZproblemowego logowania jednokrotnego*.
    - Zagnieżdżone grupy *nie są obsługiwane*. Ten zakres ma również zastosowanie do publicznej wersji zapoznawczej.
    - Grupy dynamiczne *nie są obsługiwane* w przypadku wdrażania etapowego.
    - Obiekty Contact wewnątrz grupy blokują Dodawanie grupy.

- Przy pierwszym dodawaniu grupy zabezpieczeń do wdrożenia przemieszczanego można ograniczyć do 200 użytkowników, aby uniknąć przekroczenia limitu czasu środowiska. Po dodaniu grupy można do niej dodać kilku użytkowników bezpośrednio, zgodnie z potrzebami.

- Podczas wdrażania etapowego, gdy EnforceCloudPasswordPolicyForPasswordSyncedUsers jest włączona, zasady wygasania haseł są ustawiane na 90 dni bez opcji dostosowywania. 

- Dołączanie hybrydowe systemu Windows 10 lub Azure AD Join — pozyskiwanie podstawowego tokenu odświeżania dla systemu Windows 10 w wersji starszej niż 1903. Ten scenariusz powróci do WS-Trust punktu końcowego serwera federacyjnego, nawet jeśli logowanie użytkownika jest w zakresie wdrożenia etapowego.

- Dołączanie hybrydowe systemu Windows 10 lub usługa Azure AD Join — pozyskiwanie podstawowego tokenu odświeżania dla wszystkich wersji, gdy lokalna nazwa UPN użytkownika nie obsługuje routingu. Ten scenariusz powróci do WS-Trust punktu końcowego w trybie wdrażania etapowego, ale przestanie działać, gdy migracja etapowa zostanie zakończona, a Logowanie użytkownika nie będzie już polegać na serwerze federacyjnym.

  >[!NOTE]
  >Nadal musisz wprowadzić ostateczną uruchomienie produkcyjne z Federacji do uwierzytelniania w chmurze przy użyciu Azure AD Connect lub programu PowerShell. Wdrażanie etapowe nie przełącza domen z federacyjnego na zarządzane.  Aby uzyskać więcej informacji na temat uruchomienie produkcyjne domeny, zobacz [Migrowanie z Federacji do synchronizacji skrótów haseł](plan-migrate-adfs-password-hash-sync.md#step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso) i [Migrowanie z Federacji do uwierzytelniania przekazywanego](plan-migrate-adfs-password-hash-sync.md#step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso)
  
## <a name="get-started-with-staged-rollout"></a>Wprowadzenie do wdrożenia przemieszczanego

Aby przetestować logowanie do *synchronizacji skrótów haseł* przy użyciu wdrożenia etapowego, postępuj zgodnie z instrukcjami w następnej sekcji.

Aby uzyskać informacje o używaniu poleceń cmdlet programu PowerShell, zobacz temat [Azure AD 2,0 Preview](/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout).

## <a name="pre-work-for-password-hash-sync"></a>Pre-Work dla synchronizacji skrótów haseł

1. Włącz *synchronizację skrótów haseł* na stronie [funkcje opcjonalne](how-to-connect-install-custom.md#optional-features) w Azure AD Connect. 

   ![Zrzut ekranu strony "funkcje opcjonalne" w Azure Active Directory Connect](media/how-to-connect-staged-rollout/sr1.png)

1. Upewnij się, że cykl *synchronizacji pełnego skrótu hasła* został uruchomiony, aby wszystkie skróty haseł użytkowników zostały zsynchronizowane z usługą Azure AD. Aby sprawdzić stan *synchronizacji skrótów haseł*, można użyć diagnostyki programu PowerShell w obszarze [Rozwiązywanie problemów z synchronizacją skrótów haseł z synchronizacją Azure AD Connect](tshoot-connect-password-hash-synchronization.md).

   ![Zrzut ekranu przedstawiający dziennik rozwiązywania problemów z AADConnect](./media/how-to-connect-staged-rollout/sr2.png)

Jeśli chcesz przetestować logowanie za pomocą *uwierzytelniania przekazywanego* przy użyciu wdrożenia etapowego, włącz je, wykonując instrukcje przedpracy w następnej sekcji.

## <a name="pre-work-for-pass-through-authentication"></a>Przed rozpoczęciem uwierzytelniania przekazywanego

1. Zidentyfikuj serwer z systemem Windows Server 2012 R2 lub nowszym, w którym ma zostać uruchomiony agent *uwierzytelniania Pass-through* . 

   *Nie* wybieraj serwera Azure AD Connect. Upewnij się, że serwer jest przyłączony do domeny, umożliwia uwierzytelnienie wybranych użytkowników za pomocą Active Directory i może komunikować się z usługą Azure AD na portach wychodzących i adresach URL. Aby uzyskać więcej informacji, zobacz sekcję "krok 1: Sprawdzanie wymagań wstępnych" [przewodnika Szybki Start: usługa Azure AD bezproblemowe logowanie](how-to-connect-sso-quick-start.md)jednokrotne.

1. [Pobierz agenta uwierzytelniania Azure AD Connect](https://aka.ms/getauthagent)i zainstaluj go na serwerze. 

1. Aby włączyć [wysoką dostępność](how-to-connect-sso-quick-start.md), należy zainstalować dodatkowych agentów uwierzytelniania na innych serwerach.

1. Upewnij się, że [Ustawienia inteligentnego blokady](../authentication/howto-password-smart-lockout.md) zostały odpowiednio skonfigurowane. Pozwoli to zagwarantować, że konta Active Directory lokalnych użytkowników nie są blokowane przez złe uczestników.

Zalecamy włączenie *bezproblemowego logowania jednokrotnego* niezależnie od metody logowania (*Synchronizacja skrótów haseł* lub *uwierzytelnianie przekazywane*) wybieranego do wdrożenia etapowego. Aby włączyć *bezproblemowe logowanie jednokrotne*, postępuj zgodnie z instrukcjami przedpracy w następnej sekcji.

## <a name="pre-work-for-seamless-sso"></a>Przedpracuj przed bezproblemowym logowaniem jednokrotnym

Włącz *bezproblemową rejestrację jednokrotną* w lasach Active Directory przy użyciu programu PowerShell. Jeśli masz więcej niż jeden las Active Directory, włącz go osobno dla każdego lasu. *Bezproblemowe logowanie jednokrotne* jest wyzwalane tylko dla użytkowników wybranych do wdrożenia etapowego. Nie ma to wpływu na istniejącą konfigurację Federacji.

Włącz *bezproblemową rejestrację jednokrotną* , wykonując następujące czynności:

1. Zaloguj się do Azure AD Connect Server.

2. Przejdź do folderu *% ProgramFiles% \\ Microsoft Azure Active Directory Connect* .

3. Zaimportuj bezproblemowy moduł programu PowerShell dla *logowania jednokrotnego* , uruchamiając następujące polecenie: 

   `Import-Module .\AzureADSSO.psd1`

4. Uruchom program PowerShell jako administrator. W programie PowerShell Wywołaj polecenie `New-AzureADSSOAuthenticationContext` . To polecenie umożliwia otwarcie okienka, w którym można wprowadzić poświadczenia administratora globalnego dzierżawy.

5. Wywołanie `Get-AzureADSSOStatus | ConvertFrom-Json` . To polecenie wyświetla listę Active Directory lasów (Zobacz listę "domeny"), na której włączono tę funkcję. Domyślnie na poziomie dzierżawy jest ustawiona wartość false.

   ![Przykład danych wyjściowych programu Windows PowerShell](./media/how-to-connect-staged-rollout/sr3.png)

6. Wywołanie `$creds = Get-Credential` . W wierszu polecenia wprowadź poświadczenia administratora domeny dla zamierzonego lasu Active Directory.

7. Wywołanie `Enable-AzureADSSOForest -OnPremCredentials $creds` . To polecenie tworzy konto komputera AZUREADSSOACC z lokalnego kontrolera domeny dla lasu Active Directory, który jest wymagany do *bezproblemowego logowania jednokrotnego*.

8. *Bezproblemowe logowanie jednokrotne* wymaga, aby adresy URL znajdować się w strefie intranetowej. Aby wdrożyć te adresy URL przy użyciu zasad grupy, zobacz [Szybki Start: usługa Azure AD bezproblemowe logowanie](how-to-connect-sso-quick-start.md#step-3-roll-out-the-feature)jednokrotne.

9. Aby zapoznać się z kompletnym przewodnikiem, możesz również pobrać nasze [plany wdrożenia](https://aka.ms/SeamlessSSODPDownload) w celu *bezproblemowego logowania jednokrotnego*.

## <a name="enable-staged-rollout"></a>Włącz wdrażanie etapowe

Aby wdrożyć określoną funkcję (*uwierzytelnianie przekazywane*, *synchronizację skrótów haseł* lub *bezproblemowe logowanie jednokrotne*) do wybranego zestawu użytkowników w grupie, postępuj zgodnie z instrukcjami podanymi w następnych sekcjach.

### <a name="enable-a-staged-rollout-of-a-specific-feature-on-your-tenant"></a>Włącz etapowe wprowadzanie konkretnej funkcji w dzierżawie

Jedną z następujących opcji można wdrożyć:

- **Opcja A**  -  *Synchronizacja*  +  skrótów haseł *bezproblemowe logowanie jednokrotne*
- **Opcja B**  -  *uwierzytelnianie przekazywane*  +  *bezproblemowe logowanie jednokrotne*
- **Nieobsługiwane**  -  *Synchronizacja*  +  skrótów haseł *uwierzytelnianie przekazywane*  +  *bezproblemowe logowanie jednokrotne*

Wykonaj następujące czynności:

1. Aby uzyskać dostęp do środowiska wersji zapoznawczej, zaloguj się do [portalu usługi Azure AD](https://aka.ms/stagedrolloutux).

2. Wybierz łącze **Włącz wdrażanie etapowe dla konta zarządzanego użytkownika (wersja zapoznawcza)** .

   Na przykład jeśli chcesz włączyć *opcję A*, przesuń **skrót hasła** i **bezproblemowo** kontroluje Logowanie jednokrotne do **włączenia**, jak pokazano na poniższych ilustracjach.

   ![Strona Azure AD Connect](./media/how-to-connect-staged-rollout/sr4.png)

   ![Strona "Włączanie przemieszczonych funkcji wdrożenia (wersja zapoznawcza)"](./media/how-to-connect-staged-rollout/sr5.png)

3. Dodaj grupy do funkcji, aby włączyć *uwierzytelnianie przekazywane* i *bezproblemowe logowanie jednokrotne*. Aby uniknąć limitu czasu środowiska użytkownika, upewnij się, że grupy zabezpieczeń nie zawierają początkowo więcej niż 200 członków.

   ![Strona "Zarządzanie grupami do synchronizacji skrótów haseł (wersja zapoznawcza)"](./media/how-to-connect-staged-rollout/sr6.png)

   >[!NOTE]
   >Członkowie w grupie są automatycznie włączeni do wdrożenia etapowego. Grupy zagnieżdżone i dynamiczne nie są obsługiwane w przypadku wdrażania etapowego.
   >Podczas dodawania nowej grupy Użytkownicy w grupie (do 200 użytkowników dla nowej grupy) zostaną zaktualizowani w celu natychmiastowego używania uwierzytelniania zarządzanego. Edytowanie grupy (Dodawanie lub usuwanie użytkowników) może zająć do 24 godzin.
   >Bezproblemowe logowanie jednokrotne ma zastosowanie tylko wtedy, gdy użytkownicy znajdują się w grupie bezproblemowej rejestracji jednokrotnej, a także w grupie PTA lub PHS.

## <a name="auditing"></a>Inspekcja

Włączono zdarzenia inspekcji dla różnych akcji wykonywanych w celu wdrożenia etapowego:

- Zdarzenie inspekcji po włączeniu wdrożenia etapowego dla *synchronizacji skrótów haseł*, *uwierzytelniania przekazywanego* lub *BEZproblemowego logowania jednokrotnego*.

  >[!NOTE]
  >Zdarzenie inspekcji jest rejestrowane, gdy jest włączone *bezproblemowe logowanie jednokrotne* przy użyciu wdrożenia etapowego.

  ![Okienko "Tworzenie zasad wdrażania dla funkcji" — karta działania](./media/how-to-connect-staged-rollout/sr7.png)

  ![Okienko "Tworzenie zasad wdrażania dla funkcji" — zmodyfikowano kartę właściwości](./media/how-to-connect-staged-rollout/sr8.png)

- Zdarzenie inspekcji, gdy grupa jest dodawana do *synchronizacji skrótów haseł*, *uwierzytelniania przekazywanego* lub *BEZproblemowego logowania jednokrotnego*.

  >[!NOTE]
  >Zdarzenie inspekcji jest rejestrowane, gdy grupa zostanie dodana do *synchronizacji skrótów haseł* na potrzeby wdrożenia etapowego.

  ![Okienko "Dodawanie grupy do wdrożenia funkcji" — karta aktywność](./media/how-to-connect-staged-rollout/sr9.png)

  ![Okienko "Dodawanie grupy do wdrożenia funkcji" — zmodyfikowano kartę właściwości](./media/how-to-connect-staged-rollout/sr10.png)

- Zdarzenie inspekcji, gdy użytkownik, który został dodany do grupy, ma możliwość wdrożenia przemieszczania.

  ![Okienko "Dodawanie użytkownika do wdrożenia funkcji" — karta aktywność](media/how-to-connect-staged-rollout/sr11.png)

  ![Okienko "Dodawanie użytkownika do wdrożenia funkcji" — karta elementy docelowe](./media/how-to-connect-staged-rollout/sr12.png)

## <a name="validation"></a>Walidacja

Aby przetestować logowanie za pomocą *synchronizacji skrótów haseł* lub *uwierzytelniania przekazywanego* (Logowanie przy użyciu nazwy użytkownika i hasła), wykonaj następujące czynności:

1. W ekstranecie przejdź do [strony aplikacje](https://myapps.microsoft.com) w sesji przeglądarki prywatnej, a następnie wprowadź wartość USERPRINCIPALNAME (UPN) konta użytkownika wybranego do wdrożenia etapowego.

   Użytkownicy, którzy zostali domyślni do wdrożenia przemieszczanego, nie są przekierowywani do strony logowania federacyjnego. Zamiast tego są wyświetlane monity o zalogowanie się na stronie logowania z oznaczeniem dzierżawy usługi Azure AD.

1. Upewnij się, że logowanie powiodło się w [raporcie aktywności logowania usługi Azure AD](../reports-monitoring/concept-sign-ins.md) , filtrując element userPrincipalName.

Aby przetestować logowanie za pomocą *bezproblemowego logowania JEDNOkrotnego*:

1. W intranecie przejdź do [strony aplikacje](https://myapps.microsoft.com) w sesji przeglądarki prywatnej, a następnie wprowadź wartość USERPRINCIPALNAME (UPN) konta użytkownika wybranego do wdrożenia etapowego.

   Użytkownicy, których celem jest rozmieszczenie etapowe *bezproblemowego logowania jednokrotnego* , są prezentowane z "próbą zalogowania się..." komunikat przed zalogowaniem się w trybie dyskretnym.

1. Upewnij się, że logowanie powiodło się w [raporcie aktywności logowania usługi Azure AD](../reports-monitoring/concept-sign-ins.md) , filtrując element userPrincipalName.

   Aby śledzić logowania użytkowników, które nadal występują na Active Directory Federation Services (AD FS) dla wybranych użytkowników wdrożenia przemieszczanego, postępuj zgodnie z instrukcjami w [AD FS Rozwiązywanie problemów: zdarzenia i rejestrowanie](/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging#types-of-events). Sprawdź dokumentację dostawcy, jak to sprawdzić w przypadku dostawców federacyjnych innych firm.

## <a name="remove-a-user-from-staged-rollout"></a>Usuwanie użytkownika z wdrożenia przemieszczanego

Usunięcie użytkownika z grupy powoduje wyłączenie etapowego wdrażania dla tego użytkownika. Aby wyłączyć funkcję rozmieszczania przemieszczania, przesuń formant z powrotem do trybu **wyłączone**.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**P: Czy można korzystać z tej funkcji w środowisku produkcyjnym?**

Odp.: tak, możesz użyć tej funkcji w dzierżawie produkcyjnej, ale zalecamy, aby najpierw ją wypróbować w dzierżawie testowej.

**P: czy ta funkcja może służyć do utrzymania stałego "współistnienia", gdzie niektórzy użytkownicy używają uwierzytelniania federacyjnego, a inne korzystają z uwierzytelniania w chmurze?**

Odp.: nie, ta funkcja została zaprojektowana na potrzeby testowania uwierzytelniania w chmurze. Po pomyślnym przetestowaniu kilku grup użytkowników należy wyciąć je do uwierzytelniania w chmurze. Nie zalecamy użycia stałego stanu mieszanego, ponieważ takie podejście może prowadzić do nieoczekiwanych przepływów uwierzytelniania.

**P: Czy można użyć programu PowerShell, aby wykonać etapowe wdrażanie?**

Odp. Tak. Aby dowiedzieć się, jak używać programu PowerShell do wdrażania etapowego, zobacz [Azure AD Preview](/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout).

## <a name="next-steps"></a>Następne kroki
- [Azure AD 2,0 — wersja zapoznawcza](/powershell/module/azuread/?view=azureadps-2.0-preview#staged_rollout )
- [Zmień metodę logowania na synchronizację skrótów haseł](plan-migrate-adfs-password-hash-sync.md#step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso)
- [Zmień metodę logowania na uwierzytelnianie przekazywane](plan-migrate-adfs-password-hash-sync.md#step-3-change-the-sign-in-method-to-password-hash-synchronization-and-enable-seamless-sso)
