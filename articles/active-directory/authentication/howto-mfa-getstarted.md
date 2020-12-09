---
title: Zagadnienia dotyczące wdrażania Multi-Factor Authentication usługi Azure AD
description: Poznaj zagadnienia dotyczące wdrażania i strategię pomyślnej implementacji usługi Azure AD Multi-Factor Authentication
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9ee81abd7cd0268a7cbd6b16aa6065ec7b54bef
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96861310"
---
# <a name="plan-an-azure-ad-multi-factor-authentication-deployment"></a>Planowanie wdrożenia usługi Azure AD Multi-Factor Authentication

Osoby nawiązują połączenie z zasobami organizacji w coraz bardziej skomplikowanych scenariuszach. Osoby łączące się z firmowymi urządzeniami należącymi do organizacji, osobistymi i publicznymi w sieci firmowej przy użyciu inteligentnych telefonów, tabletów, komputerów i laptopów, często na wielu platformach. W tym zawsze połączonym świecie z wieloma urządzeniami i wieloma platformami zabezpieczenia kont użytkowników są ważniejsze niż kiedykolwiek wcześniej. Hasła, niezależnie od ich złożoności, użycia między urządzeniami, sieciami i platformami, nie są już wystarczające, aby zapewnić bezpieczeństwo konta użytkownika, zwłaszcza wtedy, gdy użytkownicy będą mogli ponownie używać haseł na kontach. Zaawansowana phishing i inne ataki inżynieryjne mogą spowodować, że nazwy użytkowników i hasła są ogłaszane i sprzedawane w ramach ciemnej sieci Web.

[Usługa Azure AD Multi-Factor Authentication (MFA)](concept-mfa-howitworks.md) pomaga w zabezpieczeniu dostępu do danych i aplikacji. Zapewnia dodatkową warstwę zabezpieczeń przy użyciu drugiej formy uwierzytelniania. Organizacje mogą korzystać z [dostępu warunkowego](../conditional-access/overview.md) , aby rozwiązanie spełniało ich konkretne potrzeby.

W tym przewodniku wdrażania pokazano, jak zaplanować i przetestować wdrożenie usługi Azure AD Multi-Factor Authentication.

Aby szybko zobaczyć Multi-Factor Authentication usługi Azure AD w działaniu, a następnie wróć do zrozumienia dodatkowych zagadnień związanych z wdrażaniem:

> [!div class="nextstepaction"]
> [Włączanie uwierzytelniania wieloskładnikowego w usłudze Azure AD](tutorial-enable-azure-mfa.md)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem wdrażania usługi Azure AD Multi-Factor Authentication istnieją wstępnie wymagane elementy, które należy wziąć pod uwagę.

| Scenariusz | Wymaganie wstępne |
| --- | --- |
| Środowisko tożsamości **tylko w chmurze** z nowoczesnego uwierzytelniania | **Brak dodatkowych zadań wymaganych wstępnie** |
| Scenariusze tożsamości **hybrydowej** | Wdrożono [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) i tożsamości użytkowników są synchronizowane lub federacyjne z Active Directory Domain Services lokalnymi z Azure Active Directory. |
| Lokalne starsze aplikacje opublikowane na potrzeby dostępu do chmury | [Serwer proxy aplikacji](../manage-apps/application-proxy.md) usługi Azure AD został wdrożony. |
| Korzystanie z usługi Azure AD MFA z uwierzytelnianiem usługi RADIUS | [Serwer zasad sieciowych](howto-mfa-nps-extension.md) został wdrożony. |
| Użytkownicy mają Microsoft Office 2010 lub starszy lub Apple mail dla systemu iOS 11 lub starszego | Uaktualnij do [wersji Microsoft Office 2013 lub nowszej](https://support.microsoft.com/help/4041439/modern-authentication-configuration-requirements-for-transition-from-o) oraz Apple mail dla systemu iOS 12 lub nowszego. Dostęp warunkowy nie jest obsługiwany przez starsze protokoły uwierzytelniania. |

## <a name="plan-user-rollout"></a>Planowanie wdrożenia użytkownika

Plan wdrożenia usługi MFA powinien obejmować wdrożenie pilotażowe, a następnie etapy wdrażania, które znajdują się w ramach zdolności produkcyjnych. Rozpocznij wdrażanie, stosując zasady dostępu warunkowego do małej grupy użytkowników pilotażowych. Po dokonaniu oceny wpływu na użytkowników pilotażowych, używany proces i zachowania rejestracji można dodać więcej grup do zasad lub dodać więcej użytkowników do istniejących grup.

### <a name="user-communications"></a>Komunikacja użytkowników

Ważne jest, aby informować użytkowników, w planowaną komunikację, o nadchodzących zmianach, wymaganiach dotyczących rejestracji usługi Azure AD MFA oraz o wszelkich niezbędnych akcjach użytkownika. Zalecamy komunikację z przedstawicielami firmy w organizacji, takimi jak komunikacja, zarządzanie zmianami lub działy kadr.

Firma Microsoft udostępnia [Szablony komunikacji](https://aka.ms/mfatemplates) i [dokumentację użytkowników końcowych](../user-help/security-info-setup-signin.md) , co ułatwia przygotowanie komunikacji. Możesz wysyłać użytkowników do [https://myprofile.microsoft.com](https://myprofile.microsoft.com) rejestracji bezpośrednio, wybierając linki do **informacji zabezpieczających** na tej stronie.

## <a name="deployment-considerations"></a>Zagadnienia dotyczące wdrażania

Usługa Azure AD Multi-Factor Authentication jest wdrażana przez wymuszanie zasad z dostępem warunkowym. Zasady dostępu warunkowego mogą wymagać od użytkowników przeprowadzenia uwierzytelniania wieloskładnikowego w przypadku spełnienia określonych kryteriów, takich jak:

* Wszyscy użytkownicy, określony użytkownik, członek grupy lub przypisana rola
* Dostęp do określonej aplikacji w chmurze
* Platforma urządzeń
* Stan urządzenia
* Lokalizacja sieciowa lub adres IP zlokalizowany geograficznie
* Aplikacje klienckie
* Ryzyko związane z logowaniem (wymaga ochrony tożsamości)
* Zgodne urządzenie
* Urządzenie dołączone do hybrydowej usługi Azure AD
* Zatwierdzona aplikacja kliencka

Korzystaj z dostosowywalnych plakatów i szablonów wiadomości e-mail w pakietach [wdrożeniowych](https://www.microsoft.com/download/details.aspx?id=57600&WT.mc_id=rss_alldownloads_all) usługi MFA, aby wdrożyć uwierzytelnianie wieloskładnikowe w organizacji.

## <a name="enable-multi-factor-authentication-with-conditional-access"></a>Włączanie Multi-Factor Authentication przy użyciu dostępu warunkowego

Zasady dostępu warunkowego wymuszają rejestrację, co wymaga od użytkowników niezarejestrowanej rejestracji przy pierwszym logowaniu, ważnym zagadnieniem dotyczącym zabezpieczeń.

[Azure AD Identity Protection](../identity-protection/howto-identity-protection-configure-risk-policies.md) współtworzy zarówno zasady rejestracji, jak i zautomatyzowane zasady wykrywania i korygowania zagrożeń w historii Multi-Factor Authentication usługi Azure AD. Zasady mogą być tworzone w celu wymuszenia zmiany hasła w przypadku naruszenia złamanej tożsamości lub wymagania usługi MFA, gdy logowanie jest uznawane za ryzykowne przez następujące [zdarzenia](../identity-protection/overview-identity-protection.md):

* Ujawnione poświadczenia
* Logowania z anonimowych adresów IP
* Niemożliwa podróż do nietypowych lokalizacji
* Logowania z nieznanych lokalizacji
* Logowania z zainfekowanych urządzeń
* Logowania z adresów IP z podejrzanymi działaniami

Niektóre wykrycia ryzyka wykryte przez Azure Active Directory Identity Protection są wykonywane w czasie rzeczywistym, a niektóre wymagają przetwarzania w trybie offline. Administratorzy mogą Zablokowani użytkownicy, którzy wykazują ryzykowne zachowania i korygują się ręcznie, wymagają zmiany hasła lub wymagają uwierzytelniania wieloskładnikowego w ramach zasad dostępu warunkowego.

## <a name="define-network-locations"></a>Definiowanie lokalizacji sieciowych

Zalecamy, aby organizacje używały dostępu warunkowego do definiowania sieci przy użyciu [nazwanych lokalizacji](../conditional-access/location-condition.md#named-locations). Jeśli Twoja organizacja korzysta z usługi Identity Protection, rozważ użycie zasad opartych na ryzyku zamiast nazwanych lokalizacji.

### <a name="configuring-a-named-location"></a>Konfigurowanie lokalizacji nazwanej

1. Otwórz **Azure Active Directory** w Azure Portal
2. Wybierz **zabezpieczenia**
3. W obszarze **Zarządzanie** wybierz pozycję **nazwane lokalizacje** .
4. Wybierz **nową lokalizację**
5. W polu **Nazwa** Podaj opisową nazwę
6. Wybierz, czy chcesz definiować lokalizację przy użyciu *zakresów adresów IP* , *krajów/regionów*
   1. W przypadku korzystania z *zakresów adresów IP*
      1. Zdecyduj, czy chcesz *oznaczyć jako zaufaną lokalizację*. Logowanie z zaufanej lokalizacji obniża ryzyko użytkownika związane z logowaniem. Oznacz tę lokalizację jako zaufaną, Jeśli wiesz, że wprowadzone zakresy adresów IP są ustalone i wiarygodne w organizacji.
      2. Określ zakresy adresów IP
   2. Jeśli są używane *kraje/regiony*
      1. Rozwiń menu rozwijane i wybierz kraje lub regiony, które chcesz zdefiniować dla tej nazwanej lokalizacji.
      2. Zdecyduj, czy mają być *uwzględniane nieznane obszary*. Nieznane obszary to adresy IP, których nie można zamapować na kraj/region.
7. Wybierz pozycję **Utwórz**

## <a name="plan-authentication-methods"></a>Zaplanuj metody uwierzytelniania

Administratorzy mogą wybrać [metody uwierzytelniania](../authentication/concept-authentication-methods.md) , które mają być dostępne dla użytkowników. Ważne jest, aby zezwalać na więcej niż jedną metodę uwierzytelniania, dzięki czemu użytkownicy mają dostępną metodę tworzenia kopii zapasowych w przypadku, gdy ich podstawowa metoda jest niedostępna. Następujące metody są dostępne dla administratorów do włączenia:

> [!TIP]
> Firma Microsoft zaleca używanie Microsoft Authenticator (aplikacji mobilnej) jako podstawowej metody usługi Azure AD Multi-Factor Authentication w celu zapewnienia bezpieczniejszego i udoskonalonego środowiska użytkownika. Aplikacja Microsoft Authenticator również [spełnia wymagania](https://azure.microsoft.com/resources/microsoft-nist/) krajowe Institute of Standards and Technology Authenticator Assurance. 

### <a name="notification-through-mobile-app"></a>Powiadomienie za poorednictwem aplikacji mobilnej

Powiadomienie wypychane jest wysyłane do aplikacji Microsoft Authenticator na urządzeniu przenośnym. Użytkownik wyświetli powiadomienie i wybierze opcję **Zatwierdź** , aby ukończyć weryfikację. Powiadomienia wypychane za pomocą aplikacji mobilnej zapewniają najmniej niepożądane opcje dla użytkowników. Są one również najbardziej niezawodną i bezpieczną opcją, ponieważ korzystają z połączenia danych, a nie z telefonem.

> [!NOTE]
> Jeśli Twoja organizacja ma pracę w podróży lub podróżuje z Chinami, **powiadomienie za pomocą metody aplikacji mobilnej** na **urządzeniach z systemem Android** nie działa w tym kraju/regionie. Dla tych użytkowników należy udostępnić alternatywne metody.

### <a name="verification-code-from-mobile-app"></a>Kod weryfikacyjny z aplikacji mobilnej

Aplikacja mobilna, taka jak aplikacja Microsoft Authenticator, generuje nowy kod weryfikacyjny OATH co 30 sekund. Użytkownik wprowadza kod weryfikacyjny do interfejsu logowania. Opcja aplikacji mobilnej może być używana bez względu na to, czy telefon ma sygnał danych lub sieć komórkową.

### <a name="call-to-phone"></a>Wywołanie telefonu

Automatyczne połączenie głosowe jest umieszczane dla użytkownika. Użytkownik odbierze połączenie i naciśnie klawisz **#** na klawiaturze telefonu, aby zatwierdzić swoje uwierzytelnienie. Wywołanie do telefonu to świetna metoda tworzenia kopii zapasowej dotycząca powiadomień lub kodu weryfikacyjnego z aplikacji mobilnej.

### <a name="text-message-to-phone"></a>Wiadomość SMS na telefon

Wiadomość tekstowa zawierająca kod weryfikacyjny jest wysyłana do użytkownika, użytkownik otrzymuje monit o wprowadzenie kodu weryfikacyjnego w interfejsie logowania.

### <a name="choose-verification-options"></a>Wybierz opcje weryfikacji

1. Przejdź do **Azure Active Directory**, **użytkowników**, **Multi-Factor Authentication**.

   ![Uzyskiwanie dostępu do portalu Multi-Factor Authentication z poziomu bloku użytkownicy usługi Azure AD w Azure Portal](media/howto-mfa-getstarted/users-mfa.png)

1. Na nowej karcie, która otwiera okno dialogowe Przejdź do **ustawień usługi**.
1. W obszarze **Opcje weryfikacji** Sprawdź wszystkie pola dla dostępnych dla użytkowników metod.

   ![Konfigurowanie metod weryfikacji na karcie Ustawienia usługi Multi-Factor Authentication](media/howto-mfa-getstarted/mfa-servicesettings-verificationoptions.png)

1. Kliknij pozycję **Zapisz**.
1. Zamknij kartę **Ustawienia usługi** .

## <a name="plan-registration-policy"></a>Planowanie zasad rejestracji

Administratorzy muszą określić, w jaki sposób użytkownicy będą rejestrować swoje metody. Organizacje powinny [włączyć nowe, połączone środowisko rejestracji](howto-registration-mfa-sspr-combined.md) usługi Azure AD MFA i samoobsługowego resetowania hasła (SSPR). SSPR umożliwia użytkownikom Resetowanie hasła w bezpieczny sposób przy użyciu tych samych metod, które są używane do uwierzytelniania wieloskładnikowego. Zalecamy zastosowanie tej złożonej rejestracji, ponieważ jest to doskonałe środowisko dla użytkowników, z możliwością rejestrowania jednokrotnego dla obu usług. Włączenie tych samych metod dla SSPR i usługi Azure AD MFA umożliwi użytkownikom zarejestrowanie się w celu korzystania z obu tych funkcji.

### <a name="registration-with-identity-protection"></a>Rejestracja przy użyciu programu Identity Protection

Jeśli Twoja organizacja korzysta z Azure Active Directory Identity Protection, [Skonfiguruj zasady rejestracji usługi MFA](../identity-protection/howto-identity-protection-configure-mfa-policy.md) , aby monitować użytkowników o zarejestrowanie przy następnym logowaniu.

### <a name="registration-without-identity-protection"></a>Rejestracja bez ochrony tożsamości

Jeśli w organizacji nie ma licencji umożliwiających ochronę tożsamości, użytkownicy są monitowani o zarejestrowanie się przy następnym zalogowaniu do usługi MFA. Użytkownicy nie mogą być zarejestrowani w usłudze MFA, jeśli nie korzystają z aplikacji chronionych za pomocą usługi MFA. Ważne jest, aby wszyscy użytkownicy zarejestrowali się w taki sposób, że niewłaściwymi aktorami nie może odgadnąć hasła użytkownika i zarejestrować się w usłudze MFA w ich imieniu, efektywnie przyjmując kontrolę nad tym kontem.

#### <a name="enforcing-registration"></a>Wymuszanie rejestracji

Korzystając z następujących kroków, zasady dostępu warunkowego mogą zmusić użytkowników do zarejestrowania się w usłudze Multi-Factor Authentication

1. Utwórz grupę, Dodaj wszystkich użytkowników, którzy nie są obecnie zarejestrowani.
2. Korzystając z dostępu warunkowego, Wymuś uwierzytelnianie wieloskładnikowe dla tej grupy, aby uzyskać dostęp do wszystkich zasobów.
3. Okresowo należy ponownie oszacować członkostwo w grupie i usunąć użytkowników, którzy zostali zarejestrowani z grupy.

Możesz zidentyfikować zarejestrowanych i niezarejestrowanych użytkowników usługi Azure AD MFA za pomocą poleceń programu PowerShell, które są zależne od [modułu MSOnline PowerShell](/powershell/azure/active-directory/install-msonlinev1).

#### <a name="identify-registered-users"></a>Identyfikowanie zarejestrowanych użytkowników

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods -ne $null} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

#### <a name="identify-non-registered-users"></a>Zidentyfikuj niezarejestrowanych użytkowników

```PowerShell
Get-MsolUser -All | where {$_.StrongAuthenticationMethods.Count -eq 0} | Select-Object -Property UserPrincipalName | Sort-Object userprincipalname 
```

### <a name="convert-users-from-per-user-mfa-to-conditional-access-based-mfa"></a>Konwertowanie użytkowników z usługi MFA na użytkownika na podstawie dostępu warunkowego

Jeśli użytkownicy zostali włączeni przy użyciu włączonego użytkownika i wymuszania usługi Azure AD Multi-Factor Authentication następujące środowisko PowerShell może pomóc w dokonaniu konwersji do Multi-Factor Authentication usługi Azure AD opartej na dostępie warunkowym.

Uruchom to środowisko PowerShell w oknie ISE lub Zapisz jako `.PS1` plik do uruchomienia lokalnego.

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
> Ostatnio zmieniono zachowanie i skrypt programu PowerShell powyżej. Wcześniej skrypt zapisany z metod MFA, wyłączył uwierzytelnianie MFA i przywrócił metody. Nie jest to już konieczne, ponieważ domyślne zachowanie funkcji Disable nie czyści metod.

## <a name="plan-conditional-access-policies"></a>Planowanie zasad dostępu warunkowego

Aby zaplanować strategię zasad dostępu warunkowego, która określi, kiedy są wymagane usługi MFA i inne kontrolki, zapoznaj się ze [wspólnymi zasadami dostępu warunkowego](../conditional-access/concept-conditional-access-policy-common.md).

Ważne jest, aby zapobiec przypadkowemu zablokowaniu dzierżawy usługi Azure AD. Można ograniczyć wpływ tego nieumyślnego braku dostępu administracyjnego przez [utworzenie dwóch lub więcej kont dostępu awaryjnego w dzierżawie](../roles/security-emergency-access.md) i wykluczenie ich z zasad dostępu warunkowego.

### <a name="create-conditional-access-policy"></a>Tworzenie zasad dostępu warunkowego

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu konta administratora globalnego.
1. Przejdź do **Azure Active Directory**  >  **Security**  >  **dostępu warunkowego** zabezpieczeń.
1. Wybierz pozycję **nowe zasady**.
   ![Tworzenie zasad dostępu warunkowego w celu włączenia uwierzytelniania wieloskładnikowego dla użytkowników Azure Portal w grupie pilotażowej](media/howto-mfa-getstarted/conditionalaccess-newpolicy.png)
1. Podaj opisową nazwę zasad.
1. W obszarze **Użytkownicy i grupy**:
   * Na karcie **Dołącz** wybierz przycisk radiowy **Wszyscy użytkownicy**
   * Na karcie **Wyklucz** , zaznacz pole wyboru **Użytkownicy i grupy** , a następnie wybierz swoje konta dostępu awaryjnego.
   * Kliknij pozycję **Gotowe**.
1. W obszarze **aplikacje w chmurze** wybierz przycisk radiowy **wszystkie aplikacje w chmurze** .
   * Opcjonalnie: na karcie **Wyklucz** wybierz pozycję aplikacje w chmurze, dla których Twoja organizacja nie wymaga uwierzytelniania wieloskładnikowego.
   * Kliknij pozycję **Gotowe**.
1. W sekcji **warunki** :
   * Opcjonalnie: Jeśli włączono usługę Azure Identity Protection, możesz zdecydować się na ocenę ryzyka związanego z logowaniem w ramach zasad.
   * Opcjonalnie: Jeśli skonfigurowano Zaufane lokalizacje lub nazwane lokalizacje, można określić, czy te lokalizacje mają zostać dołączone lub wykluczone z zasad.
1. W obszarze **Grant (Udziel**) Upewnij się, że wybrano przycisk radiowy **Udziel dostępu** .
    * Zaznacz pole wyboru **Wymagaj uwierzytelniania wieloskładnikowego**.
    * Kliknij pozycję **Wybierz**.
1. Pomiń sekcję **sesji** .
1. Ustaw przełącznik **Włącz zasady** na wartość **włączone**.
1. Kliknij pozycję **Utwórz**.

## <a name="plan-integration-with-on-premises-systems"></a>Planowanie integracji z systemami lokalnymi

Niektóre starsze i lokalne aplikacje, które nie są uwierzytelniane bezpośrednio w usłudze Azure AD, wymagają dodatkowych kroków do korzystania z usługi MFA, w tym:

* Starsze aplikacje lokalne, które będą musiały korzystać z serwera proxy aplikacji.
* Lokalne aplikacje usługi RADIUS, które będą musiały korzystać z karty usługi MFA z serwerem NPS.
* Lokalne AD FS aplikacje, które będą musiały korzystać z karty usługi MFA z AD FS 2016 lub nowszym.

Aplikacje, które uwierzytelniają się bezpośrednio w usłudze Azure AD i mają nowoczesne uwierzytelnianie (przy użyciu protokołu WS-Direct, SAML, OAuth, OpenID Connect Connect), mogą bezpośrednio korzystać z zasad dostępu warunkowego.

### <a name="use-azure-ad-mfa-with-azure-ad-application-proxy"></a>Korzystanie z usługi Azure AD MFA z usługą Azure serwer proxy aplikacji usługi Azure AD

Aplikacje znajdujące się w środowisku lokalnym można publikować w dzierżawie usługi Azure AD za pomocą [usługi azure serwer proxy aplikacji usługi Azure AD](../manage-apps/application-proxy.md) i korzystać z usługi azure AD Multi-Factor Authentication, jeśli są skonfigurowane do korzystania z uwierzytelniania wstępnego usługi Azure AD.

Te aplikacje podlegają zasadom dostępu warunkowego, które wymuszają Multi-Factor Authentication usługi Azure AD, podobnie jak w przypadku dowolnej innej aplikacji zintegrowanej z usługą Azure AD.

Podobnie, jeśli usługa Azure AD Multi-Factor Authentication jest wymuszana dla wszystkich logowań użytkowników, lokalne aplikacje opublikowane przy użyciu usługi Azure serwer proxy aplikacji usługi Azure AD będą chronione.

### <a name="integrating-azure-ad-multi-factor-authentication-with-network-policy-server"></a>Integrowanie usługi Azure AD Multi-Factor Authentication z serwerem zasad sieciowych

Rozszerzenie serwera zasad sieciowych (NPS) dla usługi Azure AD MFA dodaje do infrastruktury uwierzytelniania funkcje MFA oparte na chmurze, korzystając z istniejących serwerów. Za pomocą rozszerzenia serwera NPS możesz dodać połączenie telefoniczne, wiadomość SMS lub weryfikację aplikacji telefonicznej do istniejącego przepływu uwierzytelniania. Ta integracja ma następujące ograniczenia:

* W przypadku protokołu CHAPv2 obsługiwane są tylko powiadomienia wypychane aplikacji uwierzytelniania i rozmowy głosowej.
* Nie można zastosować zasad dostępu warunkowego.

Rozszerzenie serwera NPS pełni rolę karty między usługą RADIUS a bramą Azure AD MFA w celu zapewnienia drugiego czynnika uwierzytelniania w celu ochrony [sieci VPN](howto-mfa-nps-extension-vpn.md), [połączeń bramy pulpit zdalny](howto-mfa-nps-extension-rdg.md)lub innych aplikacji z obsługą usługi RADIUS. Użytkownicy rejestrujący usługi Azure AD MFA w tym środowisku będą zobowiązani do wszystkich prób uwierzytelnienia, ale brak zasad dostępu warunkowego oznacza, że usługa MFA jest zawsze wymagana.

#### <a name="implementing-your-nps-server"></a>Implementowanie serwera NPS

Jeśli masz już wdrożone wystąpienie serwera zasad sieciowych i używasz go, dowiesz się, jak [zintegrować istniejącą infrastrukturę NPS z usługą Azure AD Multi-Factor Authentication](howto-mfa-nps-extension.md). Jeśli konfigurujesz serwer zasad sieciowych po raz pierwszy, zapoznaj się z instrukcjami, aby uzyskać instrukcje dotyczące [serwera zasad sieciowych (NPS)](/windows-server/networking/technologies/nps/nps-top) . Wskazówki dotyczące rozwiązywania problemów można znaleźć w artykule [Rozwiązywanie komunikatów o błędach z rozszerzenia serwera NPS dla usługi Azure AD Multi-Factor Authentication](howto-mfa-nps-extension-errors.md).

#### <a name="prepare-nps-for-users-that-arent-enrolled-for-mfa"></a>Przygotuj serwer zasad sieciowych dla użytkowników, którzy nie są zarejestrowani na potrzeby uwierzytelniania wieloskładnikowego

Wybierz, co się stanie, gdy użytkownicy, którzy nie są zarejestrowani przy użyciu usługi MFA, spróbują przeprowadzić uwierzytelnianie. Aby kontrolować zachowanie funkcji, użyj ustawienia rejestru `REQUIRE_USER_MATCH` w ścieżce rejestru `HKLM\Software\Microsoft\AzureMFA` . To ustawienie ma jedną opcję konfiguracji.

| Klucz | Wartość | Domyślny |
| --- | --- | --- |
| `REQUIRE_USER_MATCH` | PRAWDA/FAŁSZ | Nie ustawiono (odpowiednik wartości TRUE) |

Celem tego ustawienia jest określenie, co należy zrobić, gdy użytkownik nie jest zarejestrowany na potrzeby usługi MFA. Efekty zmiany tego ustawienia są wymienione w poniższej tabeli.

| Ustawienia | Stan usługi MFA użytkownika | Efekty |
| --- | --- | --- |
| Klucz nie istnieje | Nie zarejestrowano | Żądanie MFA nie powiodło się |
| Wartość ustawiona na true/nie ustawiona | Nie zarejestrowano | Żądanie MFA nie powiodło się |
| Klucz ustawiony na wartość false | Nie zarejestrowano | Uwierzytelnianie bez usługi MFA |
| Klucz ma wartość false lub prawda | Zarejestrowano | Uwierzytelnianie za pomocą usługi MFA |

### <a name="integrate-with-active-directory-federation-services"></a>Integracja z usługą Active Directory Federation Services

Jeśli Twoja organizacja jest federacyjnym usługą Azure AD, możesz użyć [usługi Azure ad Multi-Factor Authentication, aby zabezpieczyć zasoby AD FS](multi-factor-authentication-get-started-adfs.md), zarówno lokalnie, jak i w chmurze. Usługa Azure AD MFA pozwala skrócić hasła i zapewnić bezpieczniejszy sposób uwierzytelniania. Począwszy od systemu Windows Server 2016, można teraz skonfigurować usługę Azure AD MFA na potrzeby uwierzytelniania podstawowego.

W przeciwieństwie do AD FS w systemie Windows Server 2012 R2, karta usługi Azure AD MFA AD FS 2016 integruje się bezpośrednio z usługą Azure AD i nie wymaga lokalnego serwera usługi Azure MFA. Karta Azure AD MFA jest wbudowana w system Windows Server 2016 i nie ma potrzeby dodatkowej instalacji.

W przypadku korzystania z usługi Azure AD MFA z AD FS 2016, a aplikacja docelowa podlega zasadom dostępu warunkowego, istnieją dodatkowe zagadnienia:

* Dostęp warunkowy jest dostępny, gdy aplikacja jest jednostką uzależnioną w usłudze Azure AD, federacyjna z AD FS 2016 lub nowszą.
* Dostęp warunkowy nie jest dostępny, gdy aplikacja jest jednostką uzależnioną do AD FS 2016 lub AD FS 2019 i jest zarządzana lub federacyjna z AD FS 2016 lub AD FS 2019.
* Dostęp warunkowy jest również niedostępny, gdy AD FS 2016 lub AD FS 2019 jest skonfigurowany do korzystania z usługi Azure AD MFA jako metody uwierzytelniania podstawowego.

#### <a name="ad-fs-logging"></a>Rejestrowanie AD FS

Standard AD FS 2016 i 2019 logowanie zarówno w dzienniku zabezpieczeń systemu Windows, jak i w dzienniku administratora AD FS, zawiera informacje o żądaniach uwierzytelniania i ich powodzeniu lub niepowodzeniu. Dane dziennika zdarzeń w ramach tych zdarzeń wskazują, czy usługa Azure AD MFA została użyta. Na przykład zdarzenie AD FS inspekcji o IDENTYFIKATORze 1200 może zawierać:

```
<MfaPerformed>true</MfaPerformed>
<MfaMethod>MFA</MfaMethod>
```

#### <a name="renew-and-manage-certificates"></a>Odnawianie certyfikatów i zarządzanie nimi

Na każdym serwerze AD FS, na komputerze lokalnym w moim sklepie, będzie istnieć certyfikat usługi Azure AD MFA z podpisem własnym zatytułowany OU = Microsoft AD FS Azure MFA, który zawiera datę wygaśnięcia certyfikatu. Sprawdź okres ważności tego certyfikatu na każdym serwerze AD FS, aby określić datę wygaśnięcia.

Jeśli okres ważności certyfikatów zbliża się do wygaśnięcia, [Wygeneruj i Sprawdź nowy certyfikat usługi MFA na każdym serwerze AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-and-azure-mfa#configure-the-ad-fs-servers).

Poniższe wskazówki zawierają szczegółowe informacje na temat zarządzania certyfikatami usługi Azure AD MFA na serwerach AD FS. W przypadku konfigurowania AD FS za pomocą usługi Azure AD MFA certyfikaty wygenerowane za pośrednictwem `New-AdfsAzureMfaTenantCertificate` polecenia cmdlet programu PowerShell są prawidłowe przez dwa lata. Odnów i zainstaluj odnowione certyfikaty przed wygaśnięciem, aby ovoid przerwy w działaniu usługi MFA.

## <a name="implement-your-plan"></a>Implementowanie planu

Po zaplanowaniu rozwiązania możesz wdrożyć, wykonując poniższe kroki:

1. Spełnienie wszelkich niezbędnych wymagań wstępnych
   1. Wdrażanie [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) dla dowolnych scenariuszy hybrydowych
   1. Wdróż [serwer proxy aplikacji usługi Azure AD platformy Azure](../manage-apps/application-proxy.md) dla wszystkich aplikacji lokalnych opublikowanych na potrzeby dostępu do chmury
   1. Wdróż [serwer NPS](/windows-server/networking/technologies/nps/nps-top) pod kątem dowolnego uwierzytelniania usługi RADIUS
   1. Upewnij się, że użytkownicy przeprowadzili uaktualnienie do obsługiwanych wersji Microsoft Office z włączonym nowoczesnym uwierzytelnianiem
1. Skonfiguruj wybrane [metody uwierzytelniania](#choose-verification-options)
1. Definiowanie [nazwanych lokalizacji sieciowych](../conditional-access/location-condition.md#named-locations)
1. Wybierz grupy, aby rozpocząć wdrażanie MFA.
1. Konfigurowanie [zasad dostępu warunkowego](#create-conditional-access-policy)
1. Konfigurowanie zasad rejestracji usługi MFA
   1. [Połączone usługi MFA i SSPR](howto-registration-mfa-sspr-combined.md)
   1. Z usługą [Identity Protection](../identity-protection/howto-identity-protection-configure-mfa-policy.md)
1. Wysyłanie komunikacji użytkowników i uzyskiwanie użytkownikom rejestracji [https://aka.ms/mfasetup](https://aka.ms/mfasetup)
1. [Śledź użytkowników zarejestrowanych](#identify-non-registered-users)

> [!TIP]
> Użytkownicy w chmurze dla instytucji rządowych mogą rejestrować się w [https://aka.ms/GovtMFASetup](https://aka.ms/GovtMFASetup)

## <a name="manage-your-solution"></a>Zarządzanie rozwiązaniem

Raporty usługi Azure AD MFA

Usługa Azure AD Multi-Factor Authentication udostępnia raporty za pomocą Azure Portal:

| Raport | Lokalizacja | Opis |
| --- | --- | --- |
| Alerty użycia i oszustw | Logowanie za pomocą usługi Azure AD > | Zawiera informacje dotyczące ogólnego użycia, podsumowania użytkowników i szczegółów użytkownika; a także historia alertów o oszustwie przesłanych w określonym zakresie dat. |

## <a name="troubleshoot-mfa-issues"></a>Rozwiązywanie problemów z usługą MFA

Poznaj rozwiązania typowych problemów z usługą Azure AD MFA w artykule dotyczącym [rozwiązywania problemów z usługą Azure ad Multi-Factor Authentication](https://support.microsoft.com/help/2937344/troubleshooting-azure-multi-factor-authentication-issues) w centrum pomoc techniczna firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

Aby wyświetlić Multi-Factor Authentication usługi Azure AD w działaniu, wykonaj następujące czynności:

> [!div class="nextstepaction"]
> [Włączanie uwierzytelniania wieloskładnikowego w usłudze Azure AD](tutorial-enable-azure-mfa.md)
