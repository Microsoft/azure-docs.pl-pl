---
title: Zagadnienia dotyczące wdrażania uwierzytelniania wieloskładnikowego platformy Azure
description: Dowiedz się więcej o zagadnieniach dotyczących wdrażania i strategii pomyślnej implementacji uwierzytelniania wieloskładnikowego platformy Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 845a202faccbbe0a604560ac57ae30f87344b95a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81451129"
---
# <a name="plan-an-azure-multi-factor-authentication-deployment"></a>Planowanie wdrożenia uwierzytelniania wieloskładnikowego platformy Azure

Ludzie łączą się z zasobami organizacyjnymi w coraz bardziej skomplikowanych scenariuszach. Użytkownicy łączą się z urządzeniami należącymi do organizacji, osobistymi i publicznymi w sieci firmowej i poza nią za pomocą smartfonów, tabletów, komputerów i laptopów, często na wielu platformach. W tym zawsze połączonym, wielo-i wieloplatformowym świecie bezpieczeństwo kont użytkowników jest ważniejsze niż kiedykolwiek. Hasła, bez względu na ich złożoność, używane na różnych urządzeniach, w sieciach i platformach nie są już wystarczające, aby zapewnić bezpieczeństwo konta użytkownika, zwłaszcza gdy użytkownicy mają tendencję do ponownego użycia haseł na różnych kontach. Zaawansowane ataki phishingowe i inne ataki na inżynierię społeczną mogą spowodować publikowanie i sprzedawanie nazw użytkowników i haseł w ciemnej sieci.

[Uwierzytelnianie wieloskładnikowe usługi Azure (MFA)](concept-mfa-howitworks.md) pomaga chronić dostęp do danych i aplikacji. Zapewnia dodatkową warstwę zabezpieczeń przy użyciu drugiej formy uwierzytelniania. Organizacje mogą używać [dostępu warunkowego,](../conditional-access/overview.md) aby dopasować rozwiązanie do ich specyficznych potrzeb.

W tym przewodniku wdrażania pokazano, jak zaplanować, a następnie przetestować wdrożenie uwierzytelniania wieloskładnikowego platformy Azure.

Aby szybko wyświetlić uwierzytelnianie wieloskładnikowe platformy Azure w działaniu, a następnie wrócić do zapoznania się z dodatkowymi zagadnieniami związanymi z wdrażaniem:

> [!div class="nextstepaction"]
> [Włączanie usługi Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem wdrażania uwierzytelniania wieloskładnikowego platformy Azure istnieją elementy wstępne, które należy wziąć pod uwagę.

| Scenariusz | Wymagania wstępne |
| --- | --- |
| Środowisko tożsamości **tylko w chmurze** z nowoczesnym uwierzytelnianiem | **Brak dodatkowych zadań wymaganych** |
| **Scenariusze** tożsamości hybrydowej | [Usługa Azure AD Connect](../hybrid/whatis-hybrid-identity.md) jest wdrażana, a tożsamości użytkowników są synchronizowane lub sfederowane z lokalnymi usługami domenowymi Active Directory z usługą Azure Active Directory. |
| Starsze aplikacje lokalne opublikowane w celu uzyskania dostępu do chmury | Zostanie wdrożony [serwer proxy aplikacji](../manage-apps/application-proxy.md) usługi Azure AD. |
| Korzystanie z usługi Azure MFA z uwierzytelnianiem RADIUS | Wdrożono [serwer zasad sieciowych (NPS).](howto-mfa-nps-extension.md) |
| Użytkownicy mają pakiet Microsoft Office 2010 lub wcześniejszy lub apple mail dla systemu iOS 11 lub wcześniejszego | Uaktualnienie do [pakietu Microsoft Office 2013 lub nowszego](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) oraz poczty Apple dla systemu iOS 12 lub nowszego. Dostęp warunkowy nie jest obsługiwany przez starsze protokoły uwierzytelniania. |

## <a name="plan-user-rollout"></a>Planowanie wdrażania użytkownika

Plan wdrożenia usługi MFA powinien obejmować wdrożenie pilotażowe, a następnie fale wdrażania, które znajdują się w ramach zdolności obsługi technicznej. Rozpocznij wdrażanie, stosując zasady dostępu warunkowego do niewielkiej grupy użytkowników pilotażowych. Po ocenie wpływu na użytkowników pilotażowych, proces używanych i zachowania rejestracji, można dodać więcej grup do zasad lub dodać więcej użytkowników do istniejących grup.

### <a name="user-communications"></a>Komunikacja z użytkownikiem

Ważne jest, aby poinformować użytkowników, w planowanej komunikacji, o nadchodzących zmianach, wymagania dotyczące rejestracji usługi Azure MFA i wszelkie niezbędne akcje użytkownika. Zalecamy, aby komunikacja była rozwijana w porozumieniu z przedstawicielami organizacji, takimi jak działy komunikacji, zarządzania zmianami lub zasoby ludzkie.

Firma Microsoft udostępnia [szablony komunikacji](https://aka.ms/mfatemplates) i [dokumentację użytkownika końcowego,](../user-help/security-info-setup-signin.md) aby ułatwić opracowywanie komunikatów. Do tej strony [https://myprofile.microsoft.com](https://myprofile.microsoft.com) można wysyłać użytkowników do rejestracji bezpośrednio, wybierając łącza **Informacje zabezpieczające** na tej stronie.

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

Uwierzytelnianie wieloskładnikowe platformy Azure jest wdrażane przez wymuszanie zasad za pomocą dostępu warunkowego. [Zasady dostępu warunkowego](../conditional-access/overview.md) mogą wymagać od użytkowników wykonywania uwierzytelniania wieloskładnikowego, gdy spełnione są określone kryteria, takie jak:

* Wszyscy użytkownicy, określony użytkownik, członek grupy lub przypisana rola
* Dostęp do określonej aplikacji w chmurze
* Platforma urządzeń
* Stan urządzenia
* Lokalizacja sieciowa lub geograficznie zlokalizowany adres IP
* Aplikacje klienckie
* Ryzyko logowania (wymaga ochrony tożsamości)
* Zgodne urządzenie
* Hybrydowe urządzenie przyłączone do usługi Azure AD
* Zatwierdzona aplikacja kliencka

Użyj konfigurowalnych plakatów i szablonów wiadomości [e-mail w materiałach do wdrażania uwierzytelniania wieloskładnikowego,](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all) aby wdrożyć uwierzytelnianie wieloskładnikowe w organizacji.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Włączanie uwierzytelniania wieloskładnikowego z dostępem warunkowym

Zasady dostępu warunkowego wymuszają rejestrację, wymagając od niezarejestrowanych użytkowników ukończenia rejestracji przy pierwszym logowaniu, co jest ważnym czynnikiem bezpieczeństwa.

[Usługa Azure AD Identity Protection](../identity-protection/howto-configure-risk-policies.md) współtworzy zarówno zasady rejestracji dotyczące i automatyczne zasady wykrywania i korygowania ryzyka w wątku uwierzytelniania wieloskładnikowego platformy Azure. Zasady mogą być tworzone w celu wymuszenia zmiany hasła, gdy istnieje zagrożenie tożsamości naruszone lub wymagają mfa, gdy logowanie jest uważane za ryzykowne przez następujące [zdarzenia:](../reports-monitoring/concept-risk-events.md)

* Wyciekające poświadczenia
* Logowania z anonimowych adresów IP
* Niemożliwa podróż do nietypowych lokalizacji
* Logowania z nieznanych lokalizacji
* Logowania z zainfekowanych urządzeń
* Logowania z adresów IP z podejrzanymi działaniami

Niektóre z wykrywania ryzyka wykrytych przez usługę Azure Active Directory Identity Protection występują w czasie rzeczywistym, a niektóre wymagają przetwarzania w trybie offline. Administratorzy mogą zablokować użytkowników, którzy wykazują ryzykowne zachowania i ręcznie korygują, wymagają zmiany hasła lub wymagają uwierzytelniania wieloskładnikowego w ramach swoich zasad dostępu warunkowego.

## <a name="define-network-locations"></a>Definiowanie lokalizacji sieciowych

Zaleca się, aby organizacje używały dostępu warunkowego do definiowania swojej sieci przy użyciu [nazwanych lokalizacji](../conditional-access/location-condition.md#named-locations). Jeśli twoja organizacja korzysta z ochrony tożsamości, rozważ użycie zasad opartych na ryzyku zamiast nazwanych lokalizacji.

### <a name="configuring-a-named-location"></a>Konfigurowanie nazwanej lokalizacji

1. Otwieranie **usługi Azure Active Directory** w witrynie Azure portal
2. wybierz pozycję **Zabezpieczenia**.
3. W obszarze **Zarządzanie**wybierz pozycję **Nazwane lokalizacje**
4. Wybierz **nową lokalizację**
5. W polu **Nazwa** podaj opisową nazwę
6. Określ, czy lokalizacja jest definiowana przy użyciu *zakresów adresów IP,* czy *krajów/regionów*
   1. W przypadku korzystania z *zakresów adresów IP*
      1. Zdecyduj, czy *oznaczyć jako zaufaną lokalizację*. Logowanie z zaufanej lokalizacji obniża ryzyko użytkownika związane z logowaniem. Oznacz tę lokalizację jako zaufaną tylko wtedy, gdy wiadomo, że wprowadzone zakresy adresów IP są ustanowione i wiarygodne w organizacji.
      2. Określanie zakresów adresów IP
   2. Jeśli korzystasz z *krajów/regionów*
      1. Rozwiń menu rozwijane i wybierz kraje lub regiony, które chcesz zdefiniować dla tej nazwanej lokalizacji.
      2. Zdecyduj, czy *chcesz uwzględnić nieznane obszary*. Nieznane obszary to adresy IP, których nie można mapować na kraj/region.
7. Wybierz **pozycję Utwórz**

## <a name="plan-authentication-methods"></a>Planowanie metod uwierzytelniania

Administratorzy mogą wybrać [metody uwierzytelniania,](../authentication/concept-authentication-methods.md) które mają udostępnić użytkownikom. Ważne jest, aby zezwolić na więcej niż jedną metodę uwierzytelniania, dzięki czemu użytkownicy mają dostępną metodę tworzenia kopii zapasowej w przypadku, gdy ich podstawowa metoda jest niedostępna. Administratorzy mogą włączyć następujące metody:

### <a name="notification-through-mobile-app"></a>Powiadomienie za pośrednictwem aplikacji mobilnej

Powiadomienie wypychane jest wysyłane do aplikacji Microsoft Authenticator na urządzeniu przenośnym. Użytkownik wyświetla powiadomienie i wybiera **przycisk Zatwierdź,** aby zakończyć weryfikację. Powiadomienia wypychane za pośrednictwem aplikacji mobilnej zapewniają najmniej inwazyjną opcję dla użytkowników. Są one również najbardziej niezawodną i bezpieczną opcją, ponieważ używają połączenia danych, a nie telefonii.

> [!NOTE]
> Jeśli twoja organizacja ma pracowników pracujących w Chinach lub podróżujących do Chin, metoda **powiadamiania za pośrednictwem aplikacji mobilnej** na urządzeniach z **Androidem** nie działa w tym kraju. Alternatywne metody powinny być dostępne dla tych użytkowników.

### <a name="verification-code-from-mobile-app"></a>Kod weryfikacyjny z aplikacji mobilnej

Aplikacja mobilna, taka jak aplikacja Microsoft Authenticator, generuje nowy kod weryfikacyjny OATH co 30 sekund. Użytkownik wprowadza kod weryfikacyjny do interfejsu logowania. Z opcji aplikacji mobilnej można korzystać niezależnie od tego, czy telefon ma sygnał transmisji danych lub sieci komórkowej.

### <a name="call-to-phone"></a>Zadzwoń na telefon

Do użytkownika zostanie nawiększene automatyczne połączenie głosowe. Użytkownik odbiera połączenie i naciska **#** na klawiaturę telefonu, aby zatwierdzić ich uwierzytelnianie. Połączenie telefoniczne to świetna metoda tworzenia kopii zapasowych do powiadomień lub kodu weryfikacyjnego z aplikacji mobilnej.

### <a name="text-message-to-phone"></a>Wiadomość SMS na telefon

Wiadomość tekstowa zawierająca kod weryfikacyjny jest wysyłana do użytkownika, użytkownik jest monitowany o wprowadzenie kodu weryfikacyjnego do interfejsu logowania.

### <a name="choose-verification-options"></a>Wybieranie opcji weryfikacji

1. Przejdź do **usługi Azure Active Directory**, **użytkownicy**, **uwierzytelnianie wieloskładnikowe**.

   ![Uzyskiwanie dostępu do portalu uwierzytelniania wieloskładnikowego z bloku Użytkownicy usługi Azure AD w witrynie Azure portal](media/howto-mfa-getstarted/users-mfa.png)

1. Na nowej karcie, która otwiera przeglądanie **ustawień usługi**.
1. W obszarze **Opcje weryfikacji**sprawdź wszystkie pola wyboru metod dostępnych dla użytkowników.

   ![Konfigurowanie metod weryfikacji na karcie Ustawienia usługi uwierzytelniania wieloskładnikowego](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. Kliknij **zapisz**.
1. Zamknij kartę **ustawień usługi.**

## <a name="plan-registration-policy"></a>Zasady rejestracji planu

Administratorzy muszą określić, w jaki sposób użytkownicy będą rejestrować swoje metody. Organizacje powinny [włączyć nowe środowisko połączonej rejestracji](howto-registration-mfa-sspr-combined.md) usługi Azure MFA i samoobsługowego resetowania haseł (SSPR). Funkcja SSPR umożliwia użytkownikom resetowanie hasła w bezpieczny sposób przy użyciu tych samych metod, których używają do uwierzytelniania wieloskładnikowego. Zalecamy tę połączoną rejestrację, ponieważ jest to doskonałe środowisko dla użytkowników, z możliwością rejestracji raz dla obu usług. Włączenie tych samych metod dla samowoluuszu SSPR i usługi Azure MFA umożliwi użytkownikom zarejestrowanych do korzystania z obu funkcji.

### <a name="registration-with-identity-protection"></a>Rejestracja w ochronie tożsamości

Jeśli Twoja organizacja korzysta z usługi Azure Active Directory Identity Protection, [skonfiguruj zasady rejestracji usługi MFA,](../identity-protection/howto-mfa-policy.md) aby monitować użytkowników o zarejestrowanie się przy następnym loguchaniu interakcyjnie.

### <a name="registration-without-identity-protection"></a>Rejestracja bez ochrony tożsamości

Jeśli organizacja nie ma licencji, które włączają ochronę tożsamości, użytkownicy są monitowani o zarejestrowanie się przy następnym wymaganiu usługi MFA podczas logowania. Użytkownicy mogą nie być zarejestrowani dla usługi MFA, jeśli nie używają aplikacji chronionych za pomocą usługi MFA. Ważne jest, aby wszyscy użytkownicy zarejestrowali się tak, że źli aktorzy nie mogą odgadnąć hasła użytkownika i zarejestrować się w urzędzie obsługi w ich imieniu, skutecznie przejmując kontrolę nad kontem.

#### <a name="enforcing-registration"></a>Wymuszanie rejestracji

Wykonując następujące kroki, zasady dostępu warunkowego mogą zmusić użytkowników do zarejestrowania się w celu uwierzytelniania wieloskładnikowego

1. Utwórz grupę, dodaj wszystkich użytkowników, którzy nie są aktualnie zarejestrowani.
2. Korzystając z dostępu warunkowego, wymuszaj uwierzytelnianie wieloskładnikowe dla tej grupy, aby uzyskać dostęp do wszystkich zasobów.
3. Okresowo ponownie ocenić członkostwo w grupie i usunąć użytkowników, którzy zarejestrowali się z grupy.

Za pomocą poleceń programu PowerShell, które opierają się na [module MSOnline PowerShell,](https://docs.microsoft.com/powershell/azure/active-directory/install-msonlinev1?view=azureadps-1.0)można identyfikować zarejestrowanych i niezarejestrowanych użytkowników usługi Azure MFA.

#### <a name="identify-registered-users"></a>Identyfikowanie zarejestrowanych użytkowników

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Identyfikowanie niezarejestrowanych użytkowników

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Konwertowanie użytkowników z usługi MFA na użytkownika na uwierzytelnianie wieloskładnikowe oparte na dostępie warunkowym

Jeśli użytkownicy zostali włączeni przy użyciu uwierzytelniania wieloskładnikowego platformy Azure z włączoną i wymuszoną usługą Azure, następujący program PowerShell może pomóc w konwersji na uwierzytelnianie wieloskładnikowe azure na podstawie dostępu warunkowego.

Uruchom ten program PowerShell w oknie `.PS1` ISE lub zapisz jako plik do uruchomienia lokalnie.

```PowerShell
# Sets the MFA requirement state
function Set-MfaState {

    [CmdletBinding()]
    param(
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $ObjectId,
        [Parameter(ValueFromPipelineByPropertyName=$True)]
        $UserPrincipalName,
        [ValidateSet("Disabled","Enabled","Enforced")]
        $State
    )

    Process {
        Write-Verbose ("Setting MFA state for user '{0}' to '{1}'." -f $ObjectId, $State)
        $Requirements = @()
        if ($State -ne "Disabled") {
            $Requirement =
                [Microsoft.Online.Administration.StrongAuthenticationRequirement]::new()
            $Requirement.RelyingParty = "*"
            $Requirement.State = $State
            $Requirements += $Requirement
        }

        Set-MsolUser -ObjectId $ObjectId -UserPrincipalName $UserPrincipalName `
                     -StrongAuthenticationRequirements $Requirements
    }
}

# Disable MFA for all users
Get-MsolUser -All | Set-MfaState -State Disabled
```

> [!NOTE]
> Niedawno zmieniliśmy zachowanie i skrypt programu PowerShell powyżej odpowiednio. Wcześniej skrypt zapisane poza metody uwierzytelniania Wieloskładnikowego, wyłączone mfa i przywrócone metody. Nie jest to już konieczne teraz, gdy domyślne zachowanie wyłączenia nie wyczyści ć metod.

## <a name="plan-conditional-access-policies"></a>Planowanie zasad dostępu warunkowego

Aby zaplanować strategię zasad dostępu warunkowego, która określi, kiedy jest wymagane uwierzytelnianie usługi MFA i inne formanty, zapoznaj się z [komunikatem Co to jest dostęp warunkowy w usłudze Azure Active Directory?](../conditional-access/overview.md).

Ważne jest, aby zapobiec nieumyślnie zablokowane z dzierżawy usługi Azure AD. Można zmniejszyć wpływ tego niezamierzonego braku dostępu [administracyjnego, tworząc dwa lub więcej kont dostępu awaryjnego w dzierżawie](../users-groups-roles/directory-emergency-access.md) i wykluczając je z zasad dostępu warunkowego.

### <a name="create-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com) przy użyciu konta administratora globalnego.
1. Przejdź do **usługi Azure Active Directory** > **Security** > **Conditional Access**.
1. Wybierz **pozycję Nowa zasada**.
   ![Tworzenie zasad dostępu warunkowego w celu włączenia usługi MfA dla użytkowników portalu Azure w grupie pilotażowej](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. Podaj znaczącą nazwę zasad.
1. W obszarze **Użytkownicy i grupy:**
   * Na karcie **Dołączanie** wybierz przycisk opcji **Wszyscy użytkownicy**
   * Na karcie **Wykluczanie** zaznacz pole wyboru **Użytkownicy i grupy** i wybierz swoje konta dostępu awaryjnego.
   * Kliknij przycisk **Gotowe**.
1. W obszarze **Aplikacje w chmurze**wybierz przycisk opcji **Wszystkie aplikacje w chmurze.**
   * OPCJONALNIE: Na karcie **Wyklucz** wybierz aplikacje w chmurze, dla których twoja organizacja nie wymaga uwierzytelniania Wieloskładnikowego.
   * Kliknij przycisk **Gotowe**.
1. Sekcja **Warunki:**
   * OPCJONALNIE: Jeśli włączono usługę Azure Identity Protection, możesz ocenić ryzyko logowania jako część zasad.
   * OPCJONALNIE: Jeśli skonfigurowano zaufane lokalizacje lub nazwane lokalizacje, można określić, aby uwzględnić lub wykluczyć te lokalizacje z zasad.
1. W obszarze **Grant**upewnij się, że jest wybrany przycisk opcji **Grant access.**
    * Zaznacz pole wyboru **Wymagaj uwierzytelniania wieloskładnikowego**.
    * Kliknij **pozycję Wybierz**.
1. Pomiń **sekcję Sesja.**
1. Ustaw włącz przełącznik **zasad** na **Włączone**.
1. Kliknij przycisk **Utwórz**.

## <a name="plan-integration-with-on-premises-systems"></a>Planowanie integracji z systemami lokalnymi

Niektóre starsze i lokalne aplikacje, które nie uwierzytelniają się bezpośrednio za pomocą usługi Azure AD, wymagają dodatkowych kroków w celu użycia usługi MFA, w tym:

* Starsze aplikacje lokalne, które będą musiały używać serwera proxy aplikacji.
* Lokalne aplikacje RADIUS, które będą musiały używać karty usługi MFA z serwerem NPS.
* Lokalne aplikacje AD FS, które będą musiały używać karty usługi MFA z usługą AD FS 2016 lub nowszą.

Aplikacje uwierzytelniają się bezpośrednio za pomocą usługi Azure AD i mają nowoczesne uwierzytelnianie (WS-Fed, SAML, OAuth, OpenID Connect) mogą bezpośrednio korzystać z zasad dostępu warunkowego.

### <a name="use-azure-mfa-with-azure-ad-application-proxy"></a>Korzystanie z usługi Azure MFA z serwerem proxy aplikacji usługi Azure AD

Aplikacje zamieszkałe lokalnie mogą być publikowane w dzierżawie usługi Azure AD za pośrednictwem [serwera proxy aplikacji usługi Azure AD](../manage-apps/application-proxy.md) i mogą korzystać z uwierzytelniania wieloskładnikowego platformy Azure, jeśli są skonfigurowane do korzystania z uwierzytelniania wstępnego usługi Azure AD.

Te aplikacje podlegają zasadom dostępu warunkowego, które wymuszają uwierzytelnianie wieloskładnikowe platformy Azure, podobnie jak każda inna aplikacja zintegrowana z usługą Azure AD.

Podobnie jeśli uwierzytelnianie wieloskładnikowe platformy Azure jest wymuszane dla wszystkich logowania użytkowników, aplikacje lokalne opublikowane za pomocą serwera proxy aplikacji usługi Azure AD będą chronione.

### <a name="integrating-azure-multi-factor-authentication-with-network-policy-server"></a>Integracja uwierzytelniania wieloskładnikowego platformy Azure z serwerem zasad sieciowych

Rozszerzenie serwera zasad sieciowych (NPS) dla usługi Azure MFA dodaje funkcje usługi MFA oparte na chmurze do infrastruktury uwierzytelniania przy użyciu istniejących serwerów. Dzięki rozszerzeniu NPS możesz dodać połączenie telefoniczne, wiadomość TEKSTOWĄ lub weryfikację aplikacji telefonicznej do istniejącego przepływu uwierzytelniania. Ta integracja ma następujące ograniczenia:

* W protokole CHAPv2 obsługiwane są tylko powiadomienia wypychane aplikacji uwierzytelnianych i połączenia głosowe.
* Nie można zastosować zasad dostępu warunkowego.

Rozszerzenie SERWERA NPS działa jako karta między usługą RADIUS a chmurowym programem Azure MFA w celu zapewnienia drugiego czynnika uwierzytelniania w celu ochrony połączeń [sieci VPN,](howto-mfa-nps-extension-vpn.md) [bramy usług pulpitu zdalnego](howto-mfa-nps-extension-rdg.md)lub innych aplikacji obsługujących usługę RADIUS. Użytkownicy, którzy zarejestrują się dla usługi Azure MFA w tym środowisku będą kwestionowane dla wszystkich prób uwierzytelniania, brak zasad dostępu warunkowego oznacza, że usługa MFA jest zawsze wymagane.

#### <a name="implementing-your-nps-server"></a>Implementowanie serwera NPS

Jeśli masz już wdrożone i używane wystąpienie serwera NPS, odwołaj się [do integracji istniejącej infrastruktury NPS z uwierzytelnianiem wieloskładnikowym platformy Azure.](howto-mfa-nps-extension.md) Jeśli konfigurujesz serwer NPS po raz pierwszy, zapoznaj się z instrukcjami na [stronie Network Policy Server (NPS).](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) Wskazówki dotyczące rozwiązywania problemów można znaleźć w artykule [Rozwiązywanie komunikatów o błędach z rozszerzenia NPS dla uwierzytelniania wieloskładnikowego platformy Azure](howto-mfa-nps-extension-errors.md).

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>Przygotowanie serwera NPS dla użytkowników, którzy nie są zarejestrowani w usłudze MFA

Wybierz, co się dzieje, gdy użytkownicy, którzy nie są zarejestrowani z mfa spróbować uwierzytelnić. Użyj ustawienia `REQUIRE_USER_MATCH` rejestru w `HKLM\Software\Microsoft\AzureMFA` ścieżce rejestru, aby kontrolować zachowanie funkcji. To ustawienie ma jedną opcję konfiguracji.

| Klucz | Wartość | Domyślne |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | PRAWDA / FAŁSZ | Nie ustawiono (odpowiednik TRUE) |

Celem tego ustawienia jest określenie, co należy zrobić, gdy użytkownik nie jest zarejestrowany dla usługi MFA. Skutki zmiany tego ustawienia są wymienione w poniższej tabeli.

| Ustawienia | Stan usługi MFA użytkownika | Efekty |
| --- | --- | --- |
| Klucz nie istnieje | Nie zarejestrowano się | Wyzwanie pomocy makrofinansowej nie powiodło się |
| Wartość ustawiona na True / nie ustawiona | Nie zarejestrowano się | Wyzwanie pomocy makrofinansowej nie powiodło się |
| Klawisz ustawiony na False | Nie zarejestrowano się | Uwierzytelnianie bez usługi MFA |
| Klawisz ustawiony na False lub True | Zarejestrowano | Musi uwierzytelnić się za pomocą usługi MFA |

### <a name="integrate-with-active-directory-federation-services"></a>Integracja z usługami federacjowymi Active Directory

Jeśli twoja organizacja jest sfederowana z usługą Azure AD, można użyć [uwierzytelniania wieloskładnikowego platformy Azure do zabezpieczania zasobów usług AD FS](multi-factor-authentication-get-started-adfs.md), zarówno lokalnie, jak i w chmurze. Usługa Azure MFA umożliwia zmniejszenie haseł i zapewnienie bezpieczniejszego sposobu uwierzytelniania. Począwszy od systemu Windows Server 2016, można teraz skonfigurować uwierzytelnianie Wieloskładnikowe Azure dla uwierzytelniania podstawowego.

W przeciwieństwie do usług AD FS w systemie Windows Server 2012 R2 karta usługi AD FS 2016 usługi Azure MFA integruje się bezpośrednio z usługą Azure AD i nie wymaga lokalnego serwera usługi Azure MFA. Karta usługi Azure MFA jest wbudowana w system Windows Server 2016 i nie ma potrzeby dodatkowej instalacji.

Podczas korzystania z usługi Azure MFA z usług ad fs 2016 i aplikacji docelowej podlega zasadom dostępu warunkowego, istnieją dodatkowe zagadnienia:

* Dostęp warunkowy jest dostępny, gdy aplikacja jest stroną uzależnioną do usługi Azure AD, federacyjne z usług ad fs 2016 lub nowsze.
* Dostęp warunkowy nie jest dostępny, gdy aplikacja jest stroną uzależnioną usług AD FS 2016 lub AD FS 2019 i jest zarządzana lub sfederowana za pomocą usług AD FS 2016 lub AD FS 2019.
* Dostęp warunkowy jest również niedostępny, gdy usługi AD FS 2016 lub AD FS 2019 są skonfigurowane do używania usługi Azure MFA jako podstawowej metody uwierzytelniania.

#### <a name="ad-fs-logging"></a>Rejestrowanie chŁB usługi AD

Standardowe rejestrowanie usług AD FS 2016 i 2019 zarówno w dzienniku zabezpieczeń systemu Windows, jak i w dzienniku administratora usług AD FS zawiera informacje o żądaniach uwierzytelniania i ich powodach lub niepowodzeniu. Dane dziennika zdarzeń w tych zdarzeniach będą wskazywać, czy usługa Azure MFA została użyta. Na przykład identyfikator zdarzenia inspekcji usług AD FS 1200 może zawierać:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Odnawianie certyfikatów i zarządzanie nimi

Na każdym serwerze usług AD FS na komputerze lokalnym mój sklep będzie podpisany samodzielnie certyfikat usługi Azure MFA o nazwie OU=Microsoft AD FS Azure MFA, który zawiera datę wygaśnięcia certyfikatu. Sprawdź okres ważności tego certyfikatu na każdym serwerze usług AD FS, aby określić datę wygaśnięcia.

Jeśli okres ważności certyfikatów zbliża się do wygaśnięcia, [wygeneruj i zweryfikuj nowy certyfikat usługi MFA na każdym serwerze usług AD FS](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

Poniższe wskazówki dotyczące zarządzania certyfikatami usługi Azure MFA na serwerach usług AD FS. Podczas konfigurowania usług AD FS za pomocą usługi `New-AdfsAzureMfaTenantCertificate` Azure MFA certyfikaty generowane za pośrednictwem polecenia cmdlet programu PowerShell są ważne przez dwa lata. Odnów i zainstaluj odnowione certyfikaty przed wygaśnięciem do ovoid zakłóceń w usłudze MFA.

## <a name="implement-your-plan"></a>Zaimplementuj swój plan

Teraz, po zaplanowaniu rozwiązania, możesz zaimplementować, wykonując poniższe czynności:

1. Spełnij wszelkie niezbędne wymagania
   1. Wdrażanie [usługi Azure AD Connect](../hybrid/whatis-hybrid-identity.md) w dowolnych scenariuszach hybrydowych
   1. Wdrażanie [serwera proxy aplikacji usługi Azure AD](../manage-apps/application-proxy.md) dla dowolnej aplikacji lokalnych opublikowanych w celu uzyskania dostępu do chmury
   1. Wdrażanie [serwera NPS](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top) dla dowolnego uwierzytelniania USŁUGI RADIUS
   1. Upewnij się, że użytkownicy uaktualnili do obsługiwanych wersji pakietu Microsoft Office z włączonym nowoczesnym uwierzytelnianiem
1. Konfigurowanie wybranych [metod uwierzytelniania](#choose-verification-options)
1. Definiowanie [nazwanych lokalizacji sieciowych](../conditional-access/location-condition.md#named-locations)
1. Wybierz grupy, aby rozpocząć wdrażanie usługi MFA.
1. Konfigurowanie [zasad dostępu warunkowego](#create-conditional-access-policy)
1. Konfigurowanie zasad rejestracji usługi MFA
   1. [Połączone mfa i sspr](howto-registration-mfa-sspr-combined.md)
   1. Z [ochroną tożsamości](../identity-protection/howto-mfa-policy.md)
1. Wysyłaj komunikaty z użytkownikami i przychyń do rejestracji w[https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [Śledzenie, kto jest zarejestrowany](#identify-non-registered-users)

> [!TIP]
> Użytkownicy chmury administracji administracji administracji administracji administracji administracji administracji 19[https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup)

## <a name="manage-your-solution"></a>Zarządzanie rozwiązaniem

Raporty dotyczące usługi Azure MFA

Uwierzytelnianie wieloskładnikowe platformy Azure udostępnia raporty za pośrednictwem witryny Azure portal:

| Raport | Lokalizacja | Opis |
| --- | --- | --- |
| Powiadomienia o użytkowaniu i oszustwach | Logowania > usługi Azure AD | Zawiera informacje o ogólnym użyciu, podsumowaniu użytkownika i szczegółach użytkownika; jak również historię wpisów o nadużyciach finansowych złożonych w określonym zakresie dat. |

## <a name="troubleshoot-mfa-issues"></a>Rozwiązywanie problemów z usługią mfa

Rozwiązania typowych problemów z usługą Azure MFA można znaleźć w [artykule Rozwiązywanie problemów z uwierzytelnianiem wieloskładnikowym platformy Azure](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) w Centrum pomocy technicznej firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

Aby wyświetlić usługę Azure Multi-Factor Authentication w akcji, wykonaj następujący samouczek:

> [!div class="nextstepaction"]
> [Włączanie usługi Azure Multi-Factor Authentication](tutorial-enable-azure-mfa.md)
