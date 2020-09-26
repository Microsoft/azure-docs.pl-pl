---
title: Korzystanie z usługi Azure Multi-Factor Authentication z usługą NPS Azure Active Directory
description: Dowiedz się, jak korzystać z funkcji Multi-Factor Authentication platformy Azure przy użyciu istniejącej infrastruktury uwierzytelniania serwera zasad sieciowych (NPS)
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 08/31/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: f7214668fc63ec8bb2e0c23d264c1aa01c9148ba
ms.sourcegitcommit: 5dbea4631b46d9dde345f14a9b601d980df84897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91368416"
---
# <a name="integrate-your-existing-network-policy-server-nps-infrastructure-with-azure-multi-factor-authentication"></a>Integrowanie istniejącej infrastruktury serwera zasad sieciowych (NPS) z usługą Azure Multi-Factor Authentication

Rozszerzenie serwera zasad sieciowych (NPS) dla platformy Azure Multi-Factor Authentication dodaje do infrastruktury uwierzytelniania możliwości usługi MFA oparte na chmurze, korzystając z istniejących serwerów. Za pomocą rozszerzenia serwera NPS można dodać połączenie telefoniczne, wiadomość tekstową lub weryfikację aplikacji telefonicznej do istniejącego przepływu uwierzytelniania bez konieczności instalowania, konfigurowania i konserwowania nowych serwerów.

Rozszerzenie serwera NPS pełni rolę karty między usługą RADIUS a chmurową Multi-Factor Authentication Azure, aby zapewnić drugi czynnik uwierzytelniania dla federacyjnych lub synchronizowanych użytkowników.

## <a name="how-the-nps-extension-works"></a>Jak działa rozszerzenie serwera NPS

W przypadku korzystania z rozszerzenia serwera NPS dla usługi Azure Multi-Factor Authentication przepływ uwierzytelniania obejmuje następujące składniki:

1. **Serwer nas/VPN** odbiera żądania od klientów sieci VPN i konwertuje je na żądania usługi RADIUS do serwerów NPS.
2. **Serwer NPS** nawiązuje połączenie z usługą Active Directory Domain Services (AD DS) w celu przeprowadzenia podstawowego uwierzytelniania dla żądań RADIUS, a po pomyślnym przekazanie żądania do dowolnych zainstalowanych rozszerzeń.  
3. **Rozszerzenie serwera NPS** wyzwala żądanie do usługi Azure Multi-Factor Authentication na potrzeby uwierzytelniania pomocniczego. Gdy rozszerzenie odbierze odpowiedź, a jeśli wyzwanie usługi MFA powiedzie się, kończy żądanie uwierzytelnienia, dostarczając serwer zasad sieciowych z tokenami zabezpieczającymi, które zawierają żądanie MFA wystawione przez usługę Azure STS.
4. **Usługa Azure MFA** komunikuje się z usługą Azure Active Directory (Azure AD), aby pobrać szczegóły użytkownika i wykonuje uwierzytelnianie pomocnicze przy użyciu metody weryfikacji skonfigurowanej dla użytkownika.

Na poniższym diagramie przedstawiono przepływ żądań uwierzytelniania wysokiego poziomu:

![Diagram przepływu uwierzytelniania dla użytkowników uwierzytelnianych za pośrednictwem serwera sieci VPN do serwera NPS i rozszerzenia serwera NPS usługi Azure Multi-Factor Authentication](./media/howto-mfa-nps-extension/auth-flow.png)

### <a name="radius-protocol-behavior-and-the-nps-extension"></a>Zachowanie protokołu usługi RADIUS i rozszerzenie serwera NPS

AS RADIUS jest protokołem UDP, nadawca zakłada utratę pakietów i czeka na odpowiedź. Po upływie określonego czasu połączenie może przekroczyć limit czasu. Jeśli tak, pakiet jest ponownie wysyłany, ponieważ nadawca zakłada, że pakiet nie dociera do miejsca docelowego. W scenariuszu uwierzytelniania w tym artykule serwery sieci VPN wysyłają żądanie i oczekują na odpowiedź. Jeśli upłynął limit czasu połączenia, serwer sieci VPN wyśle żądanie ponownie.

![Diagram przepływu pakietów i żądań protokołu UDP usługi RADIUS po upływie limitu czasu odpowiedzi z serwera NPS](./media/howto-mfa-nps-extension/radius-flow.png)

Serwer NPS może nie odpowiadać na oryginalne żądanie serwera sieci VPN, zanim nastąpi przełączenie w czasie, gdy nadal trwa przetwarzanie żądania MFA. Użytkownik mógł nie odpowiedzieć na monit usługi MFA, więc rozszerzenie zasad sieciowych Azure Multi-Factor Authentication oczekuje na ukończenie tego zdarzenia. W takiej sytuacji serwer zasad sieciowych identyfikuje dodatkowe żądania serwera sieci VPN jako żądanie duplikatu. Serwer NPS odrzuca te zduplikowane żądania serwera sieci VPN.

![Diagram serwera NPS odrzucający zduplikowane żądania z serwera RADIUS](./media/howto-mfa-nps-extension/discard-duplicate-requests.png)

W przypadku przeglądania dzienników serwera NPS te dodatkowe żądania mogą zostać odrzucone. Takie zachowanie jest zaprojektowane w celu ochrony użytkownika końcowego przed uzyskaniem wielu żądań w ramach pojedynczej próby uwierzytelnienia. Odrzucone żądania w dzienniku zdarzeń serwera NPS nie wskazują na problem z serwerem NPS lub rozszerzeniem serwera zasad sieciowych platformy Azure Multi-Factor Authentication.

Aby zminimalizować odrzucone żądania, zalecamy skonfigurowanie serwerów sieci VPN z limitem czasu równym co najmniej 60 sekund. W razie potrzeby lub w celu zmniejszenia liczby odrzuconych żądań w dziennikach zdarzeń można zwiększyć wartość limitu czasu serwera sieci VPN do 90 lub 120 sekund.

Ze względu na to zachowanie protokołu UDP serwer NPS może odebrać zduplikowane żądanie i wysyłać kolejne monity usługi MFA, nawet wtedy, gdy użytkownik już odpowiedział na żądanie początkowe. Aby uniknąć tego warunku czasowego, rozszerzenie serwera zasad sieciowych platformy Azure Multi-Factor Authentication nadal filtruje i odrzuca zduplikowane żądania przez maksymalnie 10 sekund po wysłaniu pomyślnej odpowiedzi do serwera sieci VPN.

![Diagram serwera NPS kontynuuje odrzucanie zduplikowanych żądań z serwera sieci VPN przez dziesięć sekund od zwrócenia pomyślnej odpowiedzi](./media/howto-mfa-nps-extension/delay-after-successful-authentication.png)

Ponownie można zobaczyć odrzucone żądania w dziennikach zdarzeń serwera zasad sieciowych, nawet jeśli monit Multi-Factor Authentication platformy Azure zakończył się pomyślnie. Jest to oczekiwane zachowanie i nie wskazuje na problem z serwerem NPS lub rozszerzeniem zasad sieciowych platformy Azure Multi-Factor Authentication.

## <a name="plan-your-deployment"></a>Planowanie wdrożenia

Rozszerzenie NPS automatycznie obsługuje nadmiarowość, dlatego nie jest potrzebna specjalna konfiguracja.

W razie potrzeby można utworzyć dowolną liczbę serwerów NPS z obsługą Multi-Factor Authentication platformy Azure. W przypadku instalowania wielu serwerów należy użyć różnicowego certyfikatu klienta dla każdej z nich. Tworzenie certyfikatu dla każdego serwera oznacza, że każdy certyfikat można zaktualizować osobno i nie martw się o przestoje na wszystkich serwerach.

Serwery sieci VPN rozsyłają żądania uwierzytelniania, dlatego muszą mieć świadomość nowych serwerów NPS z obsługą platformy Azure Multi-Factor Authentication.

## <a name="prerequisites"></a>Wymagania wstępne

Rozszerzenie serwera NPS jest przeznaczone do pracy z istniejącą infrastrukturą. Przed rozpoczęciem upewnij się, że zostały spełnione następujące wymagania wstępne.

### <a name="licenses"></a>Licencje

Rozszerzenie serwera NPS dla usługi Azure Multi-Factor Authentication jest dostępne dla klientów z [licencjami na usługę azure Multi-Factor Authentication](multi-factor-authentication.md). Licencje oparte na użyciu dla systemu Azure Multi-Factor Authentication, na przykład na użytkownika lub licencje uwierzytelniania, nie są zgodne z rozszerzeniem serwera NPS.

### <a name="software"></a>Oprogramowanie

System Windows Server 2012 lub nowszy.

### <a name="libraries"></a>Biblioteki

Należy ręcznie zainstalować następującą bibliotekę:

- [Pakiet redystrybucyjny języka Visual C++ dla programu Visual Studio 2015](https://www.microsoft.com/download/details.aspx?id=48145)

Następujące biblioteki są instalowane automatycznie z rozszerzeniem.

- [Visual C++ pakiety redystrybucyjne dla Visual Studio 2013 (x64)](https://www.microsoft.com/download/details.aspx?id=40784)
- [Moduł Microsoft Azure Active Directory dla Windows PowerShell wersja 1.1.166.0](https://www.powershellgallery.com/packages/MSOnline/1.1.166.0)

Moduł Microsoft Azure Active Directory dla Windows PowerShell jest również instalowany za pomocą skryptu konfiguracji, który jest uruchamiany w ramach procesu instalacji, jeśli jeszcze nie istnieje. Nie ma potrzeby instalowania tego modułu przed czasem, gdy nie został jeszcze zainstalowany.

### <a name="azure-active-directory"></a>Azure Active Directory

Każdy użytkownik korzystający z rozszerzenia serwera NPS musi być synchronizowany z usługą Azure AD przy użyciu Azure AD Connect i musi być zarejestrowany dla usługi MFA.

Po zainstalowaniu rozszerzenia wymagany jest *Identyfikator dzierżawy* i poświadczenia administratora dla dzierżawy usługi Azure AD. Aby uzyskać identyfikator dzierżawy, wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny dzierżawy platformy Azure.
1. Wyszukaj i wybierz **Azure Active Directory**.
1. Na stronie **Przegląd** są wyświetlane *Informacje o dzierżawie* . Wybierz ikonę **kopiowania** obok *identyfikatora dzierżawy*, jak pokazano na poniższym przykładzie zrzutu ekranu:

   ![Pobieranie identyfikatora dzierżawy z Azure Portal](./media/howto-mfa-nps-extension/azure-active-directory-tenant-id-portal.png)

### <a name="network-requirements"></a>Wymagania dotyczące sieci

Serwer NPS musi być w stanie komunikować się z następującymi adresami URL za pośrednictwem portów 80 i 443:

* *https: \/ /adnotifications.windowsazure.com*
* *https: \/ /login.microsoftonline.com*
* *https: \/ /Credentials.Azure.com*

Ponadto do ukończenia [instalacji karty przy użyciu dostarczonego skryptu programu PowerShell](#run-the-powershell-script)jest wymagane połączenie z następującymi adresami URL:

* *https: \/ /login.microsoftonline.com*
* *https: \/ /provisioningapi.microsoftonline.com*
* *https: \/ /aadcdn.msauth.NET*

## <a name="prepare-your-environment"></a>Przygotowywanie środowiska

Przed zainstalowaniem rozszerzenia serwera NPS Przygotuj środowisko do obsługi ruchu związanego z uwierzytelnianiem.

### <a name="enable-the-nps-role-on-a-domain-joined-server"></a>Włączanie roli serwera NPS na serwerze przyłączonym do domeny

Serwer NPS nawiązuje połączenie z usługą Azure AD i uwierzytelnia żądania MFA. Wybierz jeden serwer dla tej roli. Zalecamy wybranie serwera, który nie obsługuje żądań z innych usług, ponieważ rozszerzenie zasad sieciowych zgłasza błędy dla żądań, które nie są RADIUS. Serwer NPS musi być skonfigurowany jako podstawowy i pomocniczy serwer uwierzytelniania dla danego środowiska. Serwer proxy nie może żądania usługi RADIUS na inny serwer.

1. Na serwerze Otwórz **Menedżer serwera**. Wybierz **Kreatora dodawania ról i funkcji** z menu *szybkiego startu* .
2. Dla typu instalacji wybierz pozycję **Instalacja oparta na rolach lub oparta na funkcjach**.
3. Wybierz rolę serwera **usług zasad sieciowych i dostępu sieciowego** . Okno może się pojawić w celu poinformowania o dodatkowych wymaganych funkcjach umożliwiających uruchomienie tej roli.
4. Kontynuuj pracę kreatora aż do strony *potwierdzenia* . Gdy wszystko będzie gotowe, wybierz pozycję **Zainstaluj**.

Zainstalowanie roli serwera zasad sieciowych może potrwać kilka minut. Po zakończeniu przejdź do poniższych sekcji, aby skonfigurować ten serwer do obsługi przychodzących żądań RADIUS z rozwiązania sieci VPN.

### <a name="configure-your-vpn-solution-to-communicate-with-the-nps-server"></a>Skonfiguruj rozwiązanie sieci VPN do komunikacji z serwerem NPS

W zależności od używanego rozwiązania sieci VPN kroki konfigurowania zasad uwierzytelniania usługi RADIUS różnią się. Skonfiguruj zasady sieci VPN tak, aby wskazywały serwer NPS usługi RADIUS.

### <a name="sync-domain-users-to-the-cloud"></a>Synchronizuj użytkowników domeny z chmurą

Ten krok można już zakończyć w dzierżawie, ale warto sprawdzić, czy Azure AD Connect ostatnio synchronizować bazy danych.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator.
2. Wybierz **Azure Active Directory**  >  **Azure AD Connect**
3. Sprawdź, czy stan synchronizacji jest **włączony** i czy Ostatnia synchronizacja była krótsza niż godzina temu.

Jeśli chcesz rozpocząć pracę z nową rundą synchronizacji, zobacz [Azure AD Connect Sync: Scheduler](../hybrid/how-to-connect-sync-feature-scheduler.md#start-the-scheduler).

### <a name="determine-which-authentication-methods-your-users-can-use"></a>Określanie metod uwierzytelniania, których użytkownicy mogą używać

Istnieją dwa czynniki wpływające na to, które metody uwierzytelniania są dostępne z wdrożeniem rozszerzenia serwera NPS:

1. Algorytm szyfrowania hasła używany przez klienta usługi RADIUS (serwer sieci VPN, serwera usługi lub innego) i serwery NPS.
   - **Protokół PAP** obsługuje wszystkie metody uwierzytelniania w usłudze Azure Multi-Factor Authentication w chmurze: połączenie telefoniczne, jednokierunkowa wiadomość tekstowa, powiadomienia aplikacji mobilnej, tokeny sprzętowe Oath i kod weryfikacyjny aplikacji mobilnej.
   - **CHAPv2** i **Protokół EAP** obsługują połączenia telefoniczne i powiadomienia aplikacji mobilnej.

      > [!NOTE]
      > Podczas wdrażania rozszerzenia serwera NPS należy użyć tych czynników do obliczenia, które metody są dostępne dla użytkowników. Jeśli Klient RADIUS obsługuje protokół PAP, ale środowisko użytkownika klienta nie ma pól wejściowych dla kodu weryfikacyjnego, połączenie telefoniczne i powiadomienie aplikacji mobilnej są dwoma obsługiwanymi opcjami.
      >
      > Ponadto, jeśli środowisko użytkownika klienta sieci VPN obsługuje pola wejściowe i skonfigurowano zasady dostępu do sieci, uwierzytelnianie może się powieść. Jednak żaden z atrybutów RADIUS skonfigurowanych w zasadach sieciowych nie zostanie zastosowany do urządzenia dostępu do sieci, takiego jak serwer RRAS ani klient sieci VPN. W związku z tym klient VPN może mieć większy dostęp niż pożądany lub nie ma dostępu.

2. Metody wejściowe, które może obsłużyć aplikacja kliencka (sieć VPN, serwer sieci lub inny). Czy na przykład klient sieci VPN ma pewne środki, aby zezwolić użytkownikowi na wpisywanie kodu weryfikacyjnego z aplikacji tekstowej lub mobilnej?

Nieobsługiwane [metody uwierzytelniania można wyłączyć](howto-mfa-mfasettings.md#verification-methods) na platformie Azure.

### <a name="register-users-for-mfa"></a>Rejestrowanie użytkowników usługi MFA

Przed wdrożeniem i użyciem rozszerzenia serwera NPS należy zarejestrować użytkowników wymaganych do przeprowadzenia Multi-Factor Authentication Azure dla usługi MFA. Aby przetestować rozszerzenie podczas jego wdrażania, potrzebne jest również co najmniej jedno konto testowe, które jest w pełni zarejestrowane dla Multi-Factor Authentication platformy Azure.

Jeśli musisz utworzyć i skonfigurować konto testowe, wykonaj następujące czynności:

1. Zaloguj się do programu [https://aka.ms/mfasetup](https://aka.ms/mfasetup) przy użyciu konta testowego.
2. Postępuj zgodnie z monitami, aby skonfigurować metodę weryfikacji.
3. W Azure Portal jako administrator należy [utworzyć zasady dostępu warunkowego](howto-mfa-getstarted.md#create-conditional-access-policy) , aby wymagać uwierzytelniania wieloskładnikowego dla konta testowego.

> [!IMPORTANT]
>
> Upewnij się, że użytkownicy zostali pomyślnie zarejestrowani w usłudze Azure Multi-Factor Authentication. Jeśli użytkownicy zostali wcześniej zarejestrowani do samoobsługowego resetowania hasła (SSPR), *StrongAuthenticationMethods* jest włączona dla swojego konta. Usługa Azure Multi-Factor Authentication jest wymuszana, gdy jest skonfigurowany *StrongAuthenticationMethods* , nawet jeśli użytkownik zarejestrował się tylko na SSPR.
>
> Można włączyć łączną rejestrację zabezpieczeń, która pozwala konfigurować SSPR i Multi-Factor Authentication platformy Azure w tym samym czasie. Aby uzyskać więcej informacji, zobacz [Włączanie rejestrowania połączonych informacji o zabezpieczeniach w Azure Active Directory](howto-registration-mfa-sspr-combined.md).
>
> Możesz również [wymusić, aby użytkownicy mogli ponownie rejestrować metody uwierzytelniania](howto-mfa-userdevicesettings.md#manage-user-authentication-options) , jeśli wcześniej WŁĄCZYLI tylko SSPR.

## <a name="install-the-nps-extension"></a>Instalowanie rozszerzenia serwera NPS

> [!IMPORTANT]
> Zainstaluj rozszerzenie serwera NPS na innym serwerze niż punkt dostępu sieci VPN.

### <a name="download-and-install-the-nps-extension-for-azure-mfa"></a>Pobieranie i Instalowanie rozszerzenia serwera NPS dla usługi Azure MFA

Aby pobrać i zainstalować rozszerzenie serwera NPS, wykonaj następujące czynności:

1. [Pobierz rozszerzenie serwera NPS](https://aka.ms/npsmfa) z centrum pobierania Microsoft.
1. Skopiuj plik binarny do serwera zasad sieciowych, który chcesz skonfigurować.
1. Uruchom *setup.exe* i postępuj zgodnie z instrukcjami instalacji. Jeśli wystąpią błędy, upewnij się, że [biblioteki z sekcji wymagania wstępne](#libraries) zostały zainstalowane pomyślnie.

#### <a name="upgrade-the-nps-extension"></a>Uaktualnianie rozszerzenia serwera NPS

Jeśli później zostanie uaktualniona istniejąca instalacja rozszerzenia serwera NPS, aby uniknąć ponownego uruchomienia serwera bazowego, wykonaj następujące czynności:

1. Odinstaluj istniejącą wersję.
1. Uruchom Nowy Instalator.
1. Uruchom ponownie usługę *serwera zasad sieciowych (IAS)* .

### <a name="run-the-powershell-script"></a>Uruchamianie skryptu programu PowerShell

Instalator tworzy skrypt programu PowerShell w lokalizacji `C:\Program Files\Microsoft\AzureMfa\Config` (gdzie `C:\` jest używany dysk instalacyjny). Ten skrypt programu PowerShell wykonuje następujące akcje przy każdym uruchomieniu:

* Tworzy certyfikat z podpisem własnym.
* Kojarzy klucz publiczny certyfikatu z jednostką usługi w usłudze Azure AD.
* Przechowuje certyfikat w magazynie certyfikatów komputera lokalnego.
* Przyznaje dostęp do prywatnego klucza certyfikatu użytkownikowi sieci.
* Uruchamia ponownie usługę serwera NPS.

Jeśli nie chcesz używać własnych certyfikatów (zamiast certyfikatów z podpisem własnym, które generuje skrypt programu PowerShell), uruchom skrypt programu PowerShell, aby zakończyć instalację rozszerzenia serwera NPS. W przypadku zainstalowania rozszerzenia na wielu serwerach, każdy serwer powinien mieć własny certyfikat.

Aby zapewnić możliwość równoważenia obciążenia lub nadmiarowości, Powtórz te kroki na dodatkowych serwerach NPS zgodnie z potrzebami:

1. Otwórz wiersz polecenia programu Windows PowerShell jako administrator.
1. Zmień katalogi na miejsce, w którym Instalator utworzył skrypt programu PowerShell:

   ```powershell
   cd "C:\Program Files\Microsoft\AzureMfa\Config"
   ```

1. Uruchom skrypt programu PowerShell utworzony przez Instalatora.

   > [!IMPORTANT]
   > W przypadku klientów korzystających z Azure Government lub z platformy Azure w Chinach firmy 21Vianet najpierw Edytuj `Connect-MsolService` polecenia cmdlet w skrypcie *AzureMfaNpsExtnConfigSetup.ps1* , aby uwzględnić parametry *AzureEnvironment* dla wymaganej chmury. Na przykład określ polecenie *-AzureEnvironment USGovernment* lub *-AzureEnvironment AzureChinaCloud*.
   >
   > Aby uzyskać więcej informacji, zobacz temat informacje o [parametrach Connect-MsolService](/powershell/module/msonline/connect-msolservice#parameters).

   ```powershell
   .\AzureMfaNpsExtnConfigSetup.ps1
   ```

1. Po wyświetleniu monitu zaloguj się do usługi Azure AD jako administrator.
1. Program PowerShell zapyta o identyfikator dzierżawy. Użyj identyfikatora *GUID,* który został skopiowany z Azure Portal w sekcji wymagania wstępne.
1. Po zakończeniu skryptu zostanie wyświetlony komunikat o powodzeniu.  

Jeśli poprzedni certyfikat komputera wygasł i został wygenerowany nowy certyfikat, należy usunąć wszystkie wygasłe certyfikaty. Wygaśnięcie certyfikatów może spowodować problemy z uruchamianiem rozszerzenia serwera NPS.

> [!NOTE]
> Jeśli używasz własnych certyfikatów zamiast generować certyfikaty przy użyciu skryptu programu PowerShell, upewnij się, że są one wyrównane do konwencji nazewnictwa NPS. Nazwa podmiotu musi mieć wartość **CN = \<TenantID\> , OU = Microsoft NPS Extension**.

### <a name="microsoft-azure-government-or-azure-china-21vianet-additional-steps"></a>Microsoft Azure Government lub Azure Chiny — dodatkowe kroki

W przypadku klientów, którzy korzystają z chmurowych usług 21Vianet Azure Government lub Azure Chiny, na każdym serwerze NPS wymagane są następujące dodatkowe czynności konfiguracyjne.

> [!IMPORTANT]
> Te ustawienia rejestru należy skonfigurować tylko w przypadku Azure Government lub platformy Azure w Chinach.

1. Jeśli jesteś klientem korzystającym Azure Government z usługi Microsoft Azure (Chiny), Otwórz **Edytor rejestru** na serwerze NPS.
1. Przejdź do adresu `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\AzureMfa`.
1. W przypadku klientów Azure Government ustaw następujące wartości klucza:

    | Klucz rejestru       | Wartość |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.us   |
    | STS_URL            | https://login.microsoftonline.us/ |

1. W przypadku klientów korzystających z platformy Azure w Chinach, ustaw następujące wartości klucza:

    | Klucz rejestru       | Wartość |
    |--------------------|-----------------------------------|
    | AZURE_MFA_HOSTNAME | adnotifications.windowsazure.cn   |
    | STS_URL            | https://login.chinacloudapi.cn/   |

1. Powtórz dwa poprzednie kroki, aby ustawić wartości klucza rejestru dla każdego serwera NPS.
1. Uruchom ponownie usługę NPS dla każdego serwera NPS.

    Przy minimalnym wpływie Przełącz każdy serwer zasad sieciowych z pojedynczego obrotu równoważenia obciążenia sieciowego i zaczekaj, aż wszystkie połączenia mają być opróżniane.

### <a name="certificate-rollover"></a>Przerzucanie certyfikatów

W przypadku wydania *1.0.1.32* rozszerzenia serwera NPS jest teraz obsługiwane odczytywanie wielu certyfikatów. Ta funkcja ułatwia usprawnienie aktualizacji certyfikatów przed ich wygaśnięciem. Jeśli w organizacji jest uruchomiona Poprzednia wersja rozszerzenia serwera NPS, Uaktualnij do wersji *1.0.1.32* lub nowszej.

Certyfikaty utworzone przez `AzureMfaNpsExtnConfigSetup.ps1` skrypt są ważne przez 2 lata. Monitoruj certyfikaty do wygaśnięcia. Certyfikaty rozszerzenia serwera NPS są umieszczane w magazynie certyfikatów *komputera lokalnego* w obszarze *osobistym* i są *wystawiane dla* identyfikatora dzierżawy dostarczonego do skryptu instalacji.

Gdy certyfikat zbliża się do daty wygaśnięcia, należy utworzyć nowy certyfikat w celu jego zastąpienia.  Ten proces jest realizowany przez ponowne uruchomienie `AzureMfaNpsExtnConfigSetup.ps1` i utrzymywanie tego samego identyfikatora dzierżawy po wyświetleniu monitu. Ten proces należy powtórzyć na każdym serwerze NPS w środowisku.

## <a name="configure-your-nps-extension"></a>Konfigurowanie rozszerzenia serwera NPS

Po przygotowaniu środowiska i rozszerzeniu serwera NPS zainstalowanej na wymaganych serwerach można skonfigurować rozszerzenie.

Ta sekcja zawiera zagadnienia dotyczące projektowania i sugestie dotyczące pomyślnych wdrożeń rozszerzenia serwera NPS.

### <a name="configuration-limitations"></a>Ograniczenia konfiguracji

- Rozszerzenie serwera NPS dla usługi Azure Multi-Factor Authentication nie zawiera narzędzi do migrowania użytkowników i ustawień z serwera MFA do chmury. Z tego powodu sugerujemy użycie rozszerzenia dla nowych wdrożeń, a nie istniejącego wdrożenia. Jeśli używasz rozszerzenia w istniejącym wdrożeniu, użytkownicy muszą ponownie wykonać próbę, aby wypełnić szczegóły usługi MFA w chmurze.  
- Rozszerzenie serwera NPS używa nazwy UPN z lokalnego środowiska AD DS, aby identyfikować użytkownika na platformie Azure Multi-Factor Authentication na potrzeby uwierzytelniania pomocniczego. Rozszerzenie można skonfigurować tak, aby używało innego identyfikatora, takiego jak alternatywny identyfikator logowania lub niestandardowe pole AD DS inne niż nazwa UPN. Aby uzyskać więcej informacji, zapoznaj się z artykułem [Zaawansowane opcje konfiguracji rozszerzenia serwera NPS dla Multi-Factor Authentication](howto-mfa-nps-extension-advanced.md).
- Nie wszystkie protokoły szyfrowania obsługują wszystkie metody weryfikacji.
   - **Protokół PAP** obsługuje połączenie telefoniczne, jednokierunkową wiadomość tekstową, powiadomienie aplikacji mobilnej i kod weryfikacyjny aplikacji mobilnej
   - **CHAPv2** i obsługa **protokołu EAP** oraz powiadomienia aplikacji mobilnej

### <a name="control-radius-clients-that-require-mfa"></a>Sterowanie klientami usługi RADIUS, którzy wymagają uwierzytelniania wieloskładnikowego

Po włączeniu uwierzytelniania wieloskładnikowego dla klienta usługi RADIUS przy użyciu rozszerzenia serwera NPS wszystkie uwierzytelnienia tego klienta są wymagane do przeprowadzenia uwierzytelniania MFA. Aby włączyć uwierzytelnianie wieloskładnikowe dla niektórych klientów usługi RADIUS, ale nie dla innych, można skonfigurować dwa serwery zasad sieciowych i zainstalować rozszerzenie tylko dla jednego z nich.

Skonfiguruj klientów usługi RADIUS, którzy mają wymagać uwierzytelniania wieloskładnikowego, aby wysyłać żądania do serwera NPS skonfigurowanego z rozszerzeniem, a inni klienci usługi RADIUS na serwerze NPS nie zostali skonfigurowani przy użyciu rozszerzenia.

### <a name="prepare-for-users-that-arent-enrolled-for-mfa"></a>Przygotuj dla użytkowników, którzy nie są zarejestrowani na potrzeby uwierzytelniania wieloskładnikowego

Jeśli masz użytkowników, którzy nie zostali zarejestrowani na potrzeby uwierzytelniania wieloskładnikowego, możesz określić, co się dzieje podczas próby uwierzytelnienia. Aby kontrolować to zachowanie, użyj ustawienia *REQUIRE_USER_MATCH* w ścieżce rejestru *HKLM\Software\Microsoft\AzureMFA*. To ustawienie ma jedną opcję konfiguracji:

| Klucz | Wartość | Domyślny |
| --- | ----- | ------- |
| REQUIRE_USER_MATCH | PRAWDA/FAŁSZ | Nie ustawiono (odpowiednik wartości TRUE) |

To ustawienie określa, jakie czynności należy wykonać, gdy użytkownik nie jest zarejestrowany na potrzeby usługi MFA. Jeśli klucz nie istnieje, nie jest ustawiony lub jest ustawiony na *wartość true*, a użytkownik nie jest zarejestrowany, rozszerzenie usługi MFA zakończy się niepowodzeniem.

Gdy klucz jest ustawiony na *wartość false* , a użytkownik nie jest zarejestrowany, uwierzytelnianie jest wykonywane bez wykonywania usługi MFA. Jeśli użytkownik jest zarejestrowany w ramach usługi MFA, musi uwierzytelnić się za pomocą usługi MFA, nawet jeśli *REQUIRE_USER_MATCH* jest ustawiona na *wartość false*.

Możesz utworzyć ten klucz i ustawić dla niego *wartość false* , gdy użytkownicy zostaną dołączeni i mogą nie zostać zarejestrowani na platformie Azure jeszcze Multi-Factor Authentication. Jednak ponieważ ustawienie klucza zezwala użytkownikom, którzy nie są zarejestrowani na potrzeby uwierzytelniania wieloskładnikowego, należy usunąć ten klucz przed przejściem do środowiska produkcyjnego.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="nps-extension-health-check-script"></a>Skrypt sprawdzania kondycji rozszerzenia serwera NPS

Następujący skrypt jest dostępny do wykonywania podstawowych kroków kontroli kondycji podczas rozwiązywania problemów z rozszerzeniem serwera NPS.

[MFA_NPS_Troubleshooter.ps1](/samples/azure-samples/azure-mfa-nps-extension-health-check/azure-mfa-nps-extension-health-check/)

### <a name="how-do-i-verify-that-the-client-cert-is-installed-as-expected"></a>Jak mogę sprawdzić, czy certyfikat klienta został zainstalowany zgodnie z oczekiwaniami?

Wyszukaj certyfikat z podpisem własnym utworzony przez Instalatora w magazynie certyfikatów i sprawdź, czy klucz prywatny ma uprawnienia przyznane *usłudze sieciowej*użytkownika. Certyfikat ma nazwę podmiotu **CN \<tenantid\> , OU = Microsoft NPS Extension**

Certyfikaty z podpisem własnym wygenerowane przez `AzureMfaNpsExtnConfigSetup.ps1` skrypt mają okres istnienia ważności wynoszący dwa lata. Podczas sprawdzania, czy certyfikat jest zainstalowany, należy również sprawdzić, czy certyfikat nie wygasł.

### <a name="how-can-i-verify-that-my-client-certificate-is-associated-to-my-tenant-in-azure-ad"></a>Jak sprawdzić, czy mój certyfikat klienta jest skojarzony z moją dzierżawą w usłudze Azure AD?

Otwórz wiersz polecenia programu PowerShell i uruchom następujące polecenia:

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1
```

Te polecenia służą do drukowania wszystkich certyfikatów kojarzenia dzierżawcy z wystąpieniem rozszerzenia serwera NPS w sesji programu PowerShell. Poszukaj certyfikatu, eksportując certyfikat klienta jako plik *X. 509 z kodowaniem Base-64 (. cer)* bez klucza prywatnego i porównaj go z listą z programu PowerShell.

Następujące polecenie spowoduje utworzenie pliku o nazwie *npscertificate* w katalogu głównym dysku *C:* w formacie *. cer*.

```powershell
import-module MSOnline
Connect-MsolService
Get-MsolServicePrincipalCredential -AppPrincipalId "981f26a1-7f43-403b-a875-f8b09b8cd720" -ReturnKeyValues 1 | select -ExpandProperty "value" | out-file c:\npscertificate.cer
```

Po uruchomieniu tego polecenia przejdź do katalogu głównego dysku *C:* , Znajdź plik i kliknij go dwukrotnie. Przejdź do szczegółów i przewiń w dół do pozycji "odcisk palca". Porównaj odcisk palca certyfikatu zainstalowanego na serwerze z tym serwerem. Odciski palców certyfikatu powinny być zgodne.

*Prawidłowymi* i *prawidłowymi* sygnaturami czasowymi, które znajdują się w formularzu czytelnym dla człowieka, można użyć do odfiltrowania oczywistych nieprawidłowych wartości, jeśli polecenie zwróci więcej niż jeden certyfikat.

### <a name="why-cannot-i-sign-in"></a>Dlaczego nie mogę się zalogować?

Sprawdź, czy hasło nie wygasło. Rozszerzenie serwera NPS nie obsługuje zmieniania haseł w ramach przepływu pracy logowania. Skontaktuj się z działem IT swojej organizacji, aby uzyskać dalszą pomoc.

### <a name="why-are-my-requests-failing-with-adal-token-error"></a>Dlaczego moje żądania kończą się niepowodzeniem z powodu błędu tokenu ADAL?

Ten błąd może być spowodowany jedną z kilku przyczyn. Aby rozwiązać problem, wykonaj następujące kroki:

1. Uruchom ponownie serwer zasad sieciowych.
2. Sprawdź, czy certyfikat klienta został zainstalowany zgodnie z oczekiwaniami.
3. Sprawdź, czy certyfikat jest skojarzony z dzierżawcą w usłudze Azure AD.
4. Upewnij się, że adres `https://login.microsoftonline.com/` jest dostępny z serwera, na którym działa rozszerzenie.

### <a name="why-does-authentication-fail-with-an-error-in-http-logs-stating-that-the-user-is-not-found"></a>Dlaczego uwierzytelnianie kończy się niepowodzeniem z powodu błędu w dziennikach HTTP informujących o tym, że użytkownik nie został znaleziony?

Sprawdź, czy program AD Connect jest uruchomiony i czy użytkownik jest obecny zarówno w lokalnym środowisku AD DS, jak i w usłudze Azure AD.

### <a name="why-do-i-see-http-connect-errors-in-logs-with-all-my-authentications-failing"></a>Dlaczego widzę błędy połączenia HTTP w dziennikach ze wszystkimi moimi uwierzytelnianiem kończy się niepowodzeniem?

Upewnij się, że adres https://adnotifications.windowsazure.com jest osiągalny z serwera, na którym działa rozszerzenie NPS.

### <a name="why-is-authentication-not-working-despite-a-valid-certificate-being-present"></a>Dlaczego uwierzytelnianie nie działa, pomimo obecności ważnego certyfikatu?

Jeśli poprzedni certyfikat komputera wygasł i został wygenerowany nowy certyfikat, Usuń wszystkie wygasłe certyfikaty. Wygasłe certyfikaty mogą powodować problemy z uruchamianiem rozszerzenia serwera NPS.

Aby sprawdzić, czy masz prawidłowy certyfikat, sprawdź *Magazyn certyfikatów konta komputera* lokalnego przy użyciu programu MMC i upewnij się, że certyfikat nie przeszedł jego daty wygaśnięcia. Aby wygenerować nowy prawidłowy certyfikat, należy ponownie uruchomić kroki z polecenia [Uruchom skrypt Instalatora programu PowerShell](#run-the-powershell-script).

### <a name="why-do-i-see-discarded-requests-in-the-nps-server-logs"></a>Dlaczego widzę żądania odrzucone w dziennikach serwera NPS?

Serwer sieci VPN może wysyłać powtórzone żądania do serwera NPS, jeśli wartość limitu czasu jest zbyt niska. Serwer NPS wykrywa te zduplikowane żądania i odrzuca je. To zachowanie jest zgodne z projektem i nie wskazuje na problem z serwerem NPS lub rozszerzeniem serwera NPS usługi Azure Multi-Factor Authentication.

Aby uzyskać więcej informacji na temat tego, dlaczego są wyświetlane odrzucone pakiety w dziennikach serwera NPS, zobacz [zachowanie protokołu RADIUS i rozszerzenie serwera NPS](#radius-protocol-behavior-and-the-nps-extension) na początku tego artykułu.

## <a name="managing-the-tlsssl-protocols-and-cipher-suites"></a>Zarządzanie protokołami TLS/SSL i zestawami szyfrowania

Zaleca się, aby starsze i słabsze mechanizmy szyfrowania były wyłączone lub usunięte, chyba że jest to wymagane przez organizację. Informacje o tym, jak wykonać to zadanie, można znaleźć w artykule [Zarządzanie protokołami SSL/TLS i mechanizmami szyfrowania dla AD FS](/windows-server/identity/ad-fs/operations/manage-ssl-protocols-in-ad-fs)

### <a name="additional-troubleshooting"></a>Dodatkowe Rozwiązywanie problemów

Dodatkowe wskazówki dotyczące rozwiązywania problemów i możliwe rozwiązania można znaleźć w artykule, [rozwiązując komunikaty o błędach z rozszerzenia serwera NPS dla platformy Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md).

## <a name="next-steps"></a>Następne kroki

- [Omówienie i konfiguracja serwera zasad sieciowych w systemie Windows Server](/windows-server/networking/technologies/nps/nps-top)

- Skonfiguruj Alternatywne identyfikatory logowania lub skonfiguruj listę wyjątków dla adresów IP, które nie powinny przeprowadzać weryfikacji dwuetapowej w [zaawansowanych opcjach konfiguracji dla rozszerzenia serwera NPS dla Multi-Factor Authentication](howto-mfa-nps-extension-advanced.md)

- Dowiedz się, jak zintegrować [pulpit zdalny bramę](howto-mfa-nps-extension-rdg.md) i [serwery sieci VPN](howto-mfa-nps-extension-vpn.md) przy użyciu rozszerzenia serwera NPS

- [Resolve error messages from the NPS extension for Azure Multi-Factor Authentication](howto-mfa-nps-extension-errors.md) (Rozstrzyganie komunikatów o błędach z rozszerzenia serwera NPS dotyczących usługi Azure Multi-Factor Authentication)