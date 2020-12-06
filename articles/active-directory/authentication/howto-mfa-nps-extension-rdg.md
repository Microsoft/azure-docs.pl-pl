---
title: Integrowanie RDG z rozszerzeniem NPS usługi Azure AD MFA — Azure Active Directory
description: Zintegruj infrastrukturę Pulpit zdalny Gateway z usługą Azure AD MFA przy użyciu rozszerzenia serwera zasad sieciowych dla Microsoft Azure
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
ms.openlocfilehash: 20b0150c18f2c007ed104d34daacd49ab03131a7
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743381"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Integracja infrastruktury Pulpit zdalny Gateway przy użyciu rozszerzenia serwera zasad sieciowych (NPS) i usługi Azure AD

Ten artykuł zawiera szczegółowe informacje dotyczące integrowania infrastruktury Pulpit zdalny Gateway z usługą Azure AD Multi-Factor Authentication (MFA) przy użyciu rozszerzenia serwera zasad sieciowych (NPS) dla Microsoft Azure.

Rozszerzenie serwera zasad sieciowych (NPS) dla systemu Azure pozwala klientom na ochronę Usługa telefonujących użytkowników zdalnego uwierzytelniania (RADIUS) uwierzytelniania klientów przy użyciu usługi Azure based [Multi-Factor Authentication (MFA)](./concept-mfa-howitworks.md). To rozwiązanie zapewnia dwuetapową weryfikację w celu dodania drugiej warstwy zabezpieczeń do logowania i transakcji użytkownika.

Ten artykuł zawiera instrukcje krok po kroku dotyczące integrowania infrastruktury NPS z usługą Azure AD MFA przy użyciu rozszerzenia serwera NPS dla platformy Azure. Dzięki temu użytkownicy próbujący zalogować się do bramy Pulpit zdalny mogą przeprowadzić bezpieczną weryfikację.

> [!NOTE]
> Ten artykuł nie powinien być używany z wdrożeniami serwera MFA i powinien być używany tylko z wdrożeniami usługi Azure AD MFA (opartymi na chmurze).

Usługi zasad sieciowych i dostępu sieciowego umożliwiają organizacjom wykonywanie następujących czynności:

* Zdefiniuj centralne lokalizacje zarządzania i kontroli żądań sieci, określając, kto może nawiązać połączenie, jakie pory dnia są dozwolone, czas trwania połączeń oraz poziom zabezpieczeń, które muszą być używane przez klientów do nawiązywania połączeń itd. Zamiast określać te zasady na każdym serwerze bramy sieci VPN lub Pulpit zdalny (RD), te zasady można określić raz w centralnej lokalizacji. Protokół RADIUS zapewnia scentralizowane uwierzytelnianie, autoryzację i ewidencjonowanie aktywności (AAA).
* Ustanów i Wymuszaj zasady kondycji klienta ochrony dostępu do sieci (NAP), które określają, czy urządzenia mają nieograniczony lub ograniczony dostęp do zasobów sieciowych.
* Określ metodę wymuszania uwierzytelniania i autoryzacji w celu uzyskania dostępu do punktów dostępu bezprzewodowego i przełączników sieci Ethernet z obsługą 802.1 x.

Zazwyczaj organizacje wykorzystują serwer NPS (RADIUS) do uproszczenia i scentralizowania zarządzania zasadami sieci VPN. Jednak w wielu organizacjach serwer zasad sieciowych jest również używany do uproszczenia i scentralizowania zarządzania zasadami autoryzacji połączeń usług pulpitu zdalnego (RD CAP).

Organizacje mogą także zintegrować serwer zasad sieciowych z usługą Azure AD MFA w celu zwiększenia bezpieczeństwa i zapewnienia wysokiego poziomu zgodności. Pozwala to zagwarantować, że użytkownicy będą mogli przeprowadzić weryfikację dwuetapową, aby zalogować się do bramy Pulpit zdalny. Aby użytkownicy mieli dostęp, muszą podać kombinację nazwy użytkownika/hasła oraz informacje, które użytkownik ma w swojej kontroli. Te informacje muszą być zaufane i nie być łatwo duplikowane, takie jak numer telefonu komórkowego, numer stacjonarny, aplikacja na urządzeniu przenośnym itd. RDG obecnie obsługuje połączenia telefoniczne i powiadomienia wypychane z metod aplikacji Microsoft Authenticator dla funkcji 2FA. Aby uzyskać więcej informacji na temat obsługiwanych metod uwierzytelniania, zobacz sekcję [Określanie metod uwierzytelniania, których użytkownicy mogą używać](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use).

Przed udostępnieniem rozszerzenia serwera NPS dla platformy Azure Klienci, którzy chcą wdrożyć weryfikację dwuetapową dla zintegrowanych usług NPS i Azure AD MFA, musiały skonfigurować i zachować oddzielny serwer MFA w środowisku lokalnym, zgodnie z opisem w temacie [pulpit zdalny Gateway i azure serwer Multi-Factor Authentication za pomocą usługi RADIUS](howto-mfaserver-nps-rdg.md).

Dostępność rozszerzenia serwera NPS dla platformy Azure umożliwia teraz organizacjom wybór wdrożenia lokalnego rozwiązania MFA lub opartego na chmurze rozwiązania MFA w celu zabezpieczenia uwierzytelniania klienta usługi RADIUS.

## <a name="authentication-flow"></a>Przepływ uwierzytelniania

Aby użytkownicy mogli udzielić dostępu do zasobów sieciowych za pomocą bramy Pulpit zdalny, muszą spełniać warunki określone w jednej z zasad autoryzacji połączeń usług pulpitu zdalnego (RD CAP) i jedną zasadą autoryzacji zasobów usług pulpitu zdalnego (RD RAP). W obszarze RD CAP Określ, kto ma uprawnienia do nawiązywania połączeń z bramami usług pulpitu zdalnego. RD RAPs Określ zasoby sieciowe, takie jak Pulpity zdalne lub aplikacje zdalne, z którymi użytkownik może się łączyć za pomocą bramy usług pulpitu zdalnego.

Bramę usług pulpitu zdalnego można skonfigurować do korzystania z centralnego magazynu zasad dla usług RD CAP. Zasady RD RAP nie mogą używać centralnych zasad, ponieważ są przetwarzane w bramie usług pulpitu zdalnego. Przykładem bramy usług pulpitu zdalnego skonfigurowanej do korzystania z centralnego magazynu zasad dla usług RD CAP jest Klient RADIUS na innym serwerze NPS, który służy jako centralny magazyn zasad.

Gdy rozszerzenie serwera NPS dla platformy Azure jest zintegrowane z bramą serwera NPS i Pulpit zdalny, przepływ pomyślnego uwierzytelnienia jest następujący:

1. Serwer bramy Pulpit zdalny odbiera żądanie uwierzytelnienia od użytkownika pulpitu zdalnego w celu nawiązania połączenia z zasobem, na przykład sesji Pulpit zdalny. Działając jako klient usługi RADIUS, Pulpit zdalny serwer bramy konwertuje żądanie na komunikat Access-Request usługi RADIUS i wysyła komunikat do serwera RADIUS (NPS), na którym zainstalowano rozszerzenie serwera NPS.
1. Kombinacja nazwy użytkownika i hasła jest weryfikowana w Active Directory i użytkownik jest uwierzytelniany.
1. Jeśli spełnione są wszystkie warunki określone w żądaniu połączenia serwera NPS i zasady sieciowe (na przykład ograniczenia dotyczące czasu lub członkostwa w grupie), rozszerzenie serwera zasad sieciowych wyzwala żądanie uwierzytelniania pomocniczego za pomocą usługi Azure AD MFA.
1. Usługa Azure AD MFA komunikuje się z usługą Azure AD, pobiera szczegóły użytkownika i wykonuje uwierzytelnianie pomocnicze przy użyciu obsługiwanych metod.
1. Po pomyślnym wykonaniu żądania MFA usługa Azure AD MFA komunikuje wynik do rozszerzenia serwera NPS.
1. Serwer NPS, na którym rozszerzenie jest zainstalowane, wysyła komunikat Access-Accept usługi RADIUS dla zasad RD CAP do serwera bramy Pulpit zdalny.
1. Użytkownik uzyskuje dostęp do żądanego zasobu sieciowego za pomocą bramy usług pulpitu zdalnego.

## <a name="prerequisites"></a>Wymagania wstępne

Ta sekcja zawiera szczegółowe informacje dotyczące wymagań wstępnych przed integracją usługi Azure AD MFA z bramą Pulpit zdalny. Przed rozpoczęciem należy mieć na miejsce następujące wymagania wstępne.  

* Infrastruktura Usługi pulpitu zdalnego (RDS)
* Licencja usługi Azure AD MFA
* Oprogramowanie systemu Windows Server
* Rola Usługi zasad sieciowych i dostępu sieciowego (NPS)
* Azure Active Directory zsynchronizowane z lokalnym Active Directory
* Identyfikator GUID Azure Active Directory

### <a name="remote-desktop-services-rds-infrastructure"></a>Infrastruktura Usługi pulpitu zdalnego (RDS)

Musisz mieć działającą infrastrukturę Usługi pulpitu zdalnego (RDS). Jeśli tego nie zrobisz, możesz szybko utworzyć tę infrastrukturę na platformie Azure przy użyciu następującego szablonu szybkiego startu: [Utwórz wdrożenie kolekcji sesji pulpit zdalny](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment).

Jeśli chcesz szybko utworzyć lokalną infrastrukturę RDS na potrzeby testowania, wykonaj kroki, aby je wdrożyć.
**Dowiedz się więcej**: [Wdróż RDS z przewodnikiem Szybki Start platformy Azure](/windows-server/remote/remote-desktop-services/rds-in-azure) i [podstawowym wdrożeniem infrastruktury RDS](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

### <a name="azure-ad-mfa-license"></a>Licencja usługi Azure AD MFA

Wymagana jest licencją usługi Azure AD MFA, która jest dostępna za pomocą Azure AD — wersja Premium lub innych pakietów, które obejmują. Licencje oparte na użyciu usługi Azure AD MFA, takie jak na użytkownika lub licencje uwierzytelniania, są niezgodne z rozszerzeniem serwera NPS. Aby uzyskać więcej informacji, zobacz [jak uzyskać Multi-Factor Authentication usługi Azure AD](concept-mfa-licensing.md). Do celów testowych możesz użyć subskrypcji próbnej.

### <a name="windows-server-software"></a>Oprogramowanie systemu Windows Server

Rozszerzenie serwera NPS wymaga systemu Windows Server 2008 R2 z dodatkiem SP1 lub nowszego z zainstalowaną usługą roli serwera NPS. Wszystkie kroki przedstawione w tej sekcji zostały wykonane przy użyciu systemu Windows Server 2016.

### <a name="network-policy-and-access-services-nps-role"></a>Rola Usługi zasad sieciowych i dostępu sieciowego (NPS)

Usługa roli serwera NPS udostępnia funkcje serwera i klienta usługi RADIUS, a także usługę kondycji zasad dostępu do sieci. Ta rola musi być zainstalowana na co najmniej dwóch komputerach w infrastrukturze: bramy Pulpit zdalny i innego serwera członkowskiego lub kontrolera domeny. Domyślnie rola jest już obecna na komputerze skonfigurowanym jako brama Pulpit zdalny.  Rolę serwera NPS należy również zainstalować na co najmniej na innym komputerze, takim jak kontroler domeny lub serwer członkowski.

Aby uzyskać informacje na temat instalowania usługi roli serwera NPS systemu Windows Server 2012 lub starszej wersji, zobacz [Instalowanie serwera zasad dotyczących kondycji ochrony dostępu do sieci](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd296890(v=ws.10)). Aby zapoznać się z opisem najlepszych rozwiązań dotyczących serwera NPS, w tym zalecenia dotyczące instalowania serwera zasad sieciowych na kontrolerze domeny, zobacz [najlepsze rozwiązania dotyczące serwerów zasad sieciowych](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771746(v=ws.10)).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory zsynchronizowane z lokalnym Active Directory

Aby można było korzystać z rozszerzenia serwera NPS, użytkownicy lokalni muszą zostać zsynchronizowani z usługą Azure AD i włączyły uwierzytelnianie MFA. W tej sekcji założono, że użytkownicy lokalni są synchronizowani z usługą Azure AD przy użyciu programu AD Connect. Aby uzyskać informacje na temat programu Azure AD Connect, zobacz [integrowanie katalogów lokalnych z Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>Identyfikator GUID Azure Active Directory

Aby zainstalować rozszerzenie serwera NPS, należy znać identyfikator GUID usługi Azure AD. Instrukcje dotyczące znajdowania identyfikatora GUID usługi Azure AD są podane poniżej.

## <a name="configure-multi-factor-authentication"></a>Konfigurowanie Multi-Factor Authentication

Ta sekcja zawiera instrukcje dotyczące integrowania usługi Azure AD MFA z bramą Pulpit zdalny. Jako administrator musisz skonfigurować usługę Azure AD MFA, zanim użytkownicy będą mogli automatycznie rejestrować swoje urządzenia lub aplikacje wieloskładnikowe.

Wykonaj kroki opisane w temacie [wprowadzenie do usługi Azure ad Multi-Factor Authentication w chmurze,](howto-mfa-getstarted.md) aby włączyć usługę MFA dla użytkowników usługi Azure AD.

### <a name="configure-accounts-for-two-step-verification"></a>Konfigurowanie kont na potrzeby weryfikacji dwuetapowej

Po włączeniu konta usługi MFA nie można zalogować się do zasobów objętych zasadami usługi MFA do momentu pomyślnego skonfigurowania zaufanego urządzenia do użycia dla drugiego czynnika uwierzytelniania i uwierzytelnienia przy użyciu weryfikacji dwuetapowej.

Wykonaj kroki opisane w sekcji [co to jest usługa Azure AD Multi-Factor Authentication dla mnie?](../user-help/multi-factor-authentication-end-user-first-time.md) aby zrozumieć i prawidłowo skonfigurować urządzenia dla usługi MFA przy użyciu konta użytkownika.

> [!IMPORTANT]
> Zachowanie podczas logowania dla bramy Pulpit zdalny nie zapewnia opcji wprowadzania kodu weryfikacyjnego za pomocą usługi Azure AD Multi-Factor Authentication. Konto użytkownika musi być skonfigurowane pod kątem weryfikacji telefonu lub aplikacji Microsoft Authenticator z użyciem powiadomień wypychanych.
>
> Jeśli jedna z tych dwóch metod uwierzytelniania nie jest skonfigurowana dla użytkownika, nie będzie można ukończyć wyzwania usługi Azure AD Multi-Factor Authentication i zalogować się do bramy Pulpit zdalny.

## <a name="install-and-configure-nps-extension"></a>Instalowanie i Konfigurowanie rozszerzenia serwera NPS

Ta sekcja zawiera instrukcje dotyczące konfigurowania infrastruktury usług pulpitu zdalnego do korzystania z usługi Azure AD MFA na potrzeby uwierzytelniania klientów za pomocą bramy Pulpit zdalny.

### <a name="acquire-azure-active-directory-tenant-id"></a>Uzyskaj identyfikator dzierżawy Azure Active Directory

W ramach konfiguracji rozszerzenia serwera NPS należy podać poświadczenia administratora i identyfikator usługi Azure AD dla dzierżawy usługi Azure AD. Aby uzyskać identyfikator dzierżawy, wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako Administrator globalny dzierżawy platformy Azure.
1. W menu Azure Portal wybierz pozycję **Azure Active Directory** lub Wyszukaj i wybierz pozycję **Azure Active Directory** z dowolnej strony.
1. Na stronie **Przegląd** są wyświetlane *Informacje o dzierżawie* . Wybierz ikonę **kopiowania** obok *identyfikatora dzierżawy*, jak pokazano na poniższym przykładzie zrzutu ekranu:

   ![Pobieranie identyfikatora dzierżawy z Azure Portal](./media/howto-mfa-nps-extension-rdg/azure-active-directory-tenant-id-portal.png)

### <a name="install-the-nps-extension"></a>Instalowanie rozszerzenia serwera NPS

Zainstaluj rozszerzenie serwera NPS na serwerze z zainstalowaną rolą usług zasad sieciowych i dostępu sieciowego (NPS). Ta funkcja działa jako serwer RADIUS dla Twojego projektu.

> [!IMPORTANT]
> Nie instaluj rozszerzenia serwera NPS na serwerze bramy Pulpit zdalny (RDG). Serwer RDG nie używa protokołu RADIUS z klientem, więc rozszerzenie nie może zinterpretować i wykonać uwierzytelniania MFA.
>
> Gdy serwer RDG i serwer zasad sieciowych z rozszerzeniem serwera NPS są różnymi serwerami, RDG używa wewnętrznie serwera NPS do komunikowania się z innymi serwerami NPS i używa usługi RADIUS jako protokołu do poprawnego komunikowania się.

1. Pobierz [rozszerzenie serwera NPS](https://aka.ms/npsmfa).
1. Skopiuj plik wykonywalny Instalatora (NpsExtnForAzureMfaInstaller.exe) na serwer NPS.
1. Na serwerze NPS kliknij dwukrotnie **NpsExtnForAzureMfaInstaller.exe**. Jeśli zostanie wyświetlony monit, kliknij przycisk **Uruchom**.
1. W oknie dialogowym rozszerzenia serwera NPS dla Instalatora usługi Azure AD MFA Przejrzyj postanowienia licencyjne dotyczące oprogramowania, zaznacz opcję Akceptuję **warunki i postanowienia licencyjne**, a następnie kliknij przycisk **Instaluj**.
1. W oknie dialogowym rozszerzenia serwera NPS dla usługi Azure AD MFA kliknij przycisk **Zamknij**.

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Konfigurowanie certyfikatów do użycia z rozszerzeniem serwera NPS przy użyciu skryptu programu PowerShell

Następnie należy skonfigurować certyfikaty do użycia przez rozszerzenie serwera NPS w celu zapewnienia bezpiecznej komunikacji i zapewnienia bezpieczeństwa. Składniki serwera NPS obejmują skrypt programu Windows PowerShell, który służy do konfigurowania certyfikatu z podpisem własnym do użycia z serwerem zasad sieciowych.

Skrypt wykonuje następujące czynności:

* Tworzy certyfikat z podpisem własnym
* Kojarzy klucz publiczny certyfikatu z jednostką usługi w usłudze Azure AD
* Przechowuje certyfikat w magazynie komputera lokalnego
* Przyznaje dostęp do prywatnego klucza certyfikatu użytkownikowi sieci
* Uruchamia ponownie usługę serwera zasad sieciowych

Jeśli chcesz użyć własnych certyfikatów, musisz skojarzyć klucz publiczny certyfikatu z jednostką usługi w usłudze Azure AD i tak dalej.

Aby użyć skryptu, podaj rozszerzenie przy użyciu poświadczeń administratora usługi Azure AD oraz wcześniej skopiowanego identyfikatora dzierżawy usługi Azure AD. Uruchom skrypt na każdym serwerze NPS, na którym zainstalowano rozszerzenie serwera NPS. Następnie wykonaj poniższe czynności:

1. Otwórz wiersz poleceń administracyjnych programu Windows PowerShell.
1. W wierszu polecenia programu PowerShell wpisz `cd 'c:\Program Files\Microsoft\AzureMfa\Config'` polecenie, a następnie naciśnij klawisz **Enter**.
1. Wpisz `.\AzureMfaNpsExtnConfigSetup.ps1` polecenie, a następnie naciśnij klawisz **Enter**. Skrypt sprawdza, czy zainstalowano moduł Azure Active Directory PowerShell. Jeśli nie jest zainstalowany, skrypt instaluje moduł.

   ![Uruchamianie AzureMfaNpsExtnConfigSetup.ps1 w programie Azure AD PowerShell](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. Po zweryfikowaniu instalacji modułu PowerShell przez skrypt zostanie wyświetlone okno dialogowe Azure Active Directory module programu PowerShell. W oknie dialogowym wprowadź poświadczenia administratora usługi Azure AD i hasło, a następnie kliknij przycisk **Zaloguj**.

   ![Uwierzytelnianie w usłudze Azure AD w programie PowerShell](./media/howto-mfa-nps-extension-rdg/image5.png)

1. Po wyświetleniu monitu wklej *Identyfikator dzierżawy* , który został wcześniej skopiowany do schowka, a następnie naciśnij klawisz **Enter**.

   ![Umieszczanie identyfikatora dzierżawy w programie PowerShell](./media/howto-mfa-nps-extension-rdg/image6.png)

1. Skrypt tworzy certyfikat z podpisem własnym i wykonuje inne zmiany w konfiguracji. Dane wyjściowe powinny być podobne do poniższej ilustracji.

   ![Dane wyjściowe programu PowerShell zawierające certyfikat z podpisem własnym](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>Konfigurowanie składników NPS na Pulpit zdalny Gateway

W tej sekcji skonfigurujesz zasady autoryzacji połączeń bramy Pulpit zdalny i inne ustawienia usługi RADIUS.

Przepływ uwierzytelniania wymaga wymiany komunikatów usługi RADIUS między bramą Pulpit zdalny a serwerem NPS, na którym jest zainstalowane rozszerzenie serwera NPS. Oznacza to, że należy skonfigurować ustawienia klienta usługi RADIUS zarówno na bramie Pulpit zdalny, jak i na serwerze NPS, na którym zainstalowano rozszerzenie serwera NPS.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Konfigurowanie zasad autoryzacji połączeń bramy Pulpit zdalny w celu korzystania z magazynu centralnego

Pulpit zdalny zasady autoryzacji połączeń (RD CAP) określają wymagania dotyczące nawiązywania połączenia z serwerem bramy Pulpit zdalny. Ustawienia RD CAP mogą być przechowywane lokalnie (domyślnie) lub mogą być przechowywane w centralnym magazynie RD CAP, na którym działa serwer NPS. Aby skonfigurować integrację usługi Azure AD MFA z usługami RDS, należy określić użycie magazynu centralnego.

1. Na serwerze bramy usług pulpitu zdalnego Otwórz **Menedżer serwera**.
1. W menu kliknij pozycję **Narzędzia**, wskaż polecenie **usługi pulpitu zdalnego**, a następnie kliknij pozycję **Menedżer bramy pulpit zdalny**.
1. W Menedżerze bramy usług pulpitu zdalnego kliknij prawym przyciskiem myszy pozycję **\[ Nazwa serwera \] (lokalna)**, a następnie kliknij polecenie **Właściwości**.
1. W oknie dialogowym właściwości wybierz kartę **magazyn RD CAP** .
1. Na karcie Magazyn RD CAP wybierz pozycję **serwer centralny z uruchomionym serwerem zasad sieciowych**. 
1. W polu **Wprowadź nazwę lub adres IP dla serwera** , na którym działa serwer zasad sieciowych wpisz adres IP lub nazwę serwera serwera, na którym zainstalowano rozszerzenie serwera NPS.

   ![Wprowadź nazwę lub adres IP serwera NPS](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Kliknij pozycję **Dodaj**.
1. W oknie dialogowym **wspólny klucz tajny** wprowadź wspólny klucz tajny, a następnie kliknij przycisk **OK**. Upewnij się, że ten wspólny klucz tajny jest rejestrowany i bezpieczny.

   >[!NOTE]
   >Wspólny klucz tajny służy do ustanawiania relacji zaufania między serwerami i klientami usługi RADIUS. Utwórz długi i skomplikowany klucz tajny.
   >

   ![Tworzenie wspólnego klucza tajnego w celu ustanowienia zaufania](./media/howto-mfa-nps-extension-rdg/image11.png)

1. Kliknij przycisk **OK**, aby zamknąć okno dialogowe.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>Konfigurowanie wartości limitu czasu usługi RADIUS na serwerze zasad sieciowych Pulpit zdalny bramy

Aby zapewnić czas na zweryfikowanie poświadczeń użytkowników, przeprowadzenie weryfikacji dwuetapowej, odebranie odpowiedzi i odpowiedzi na komunikaty usługi RADIUS, należy dostosować wartość limitu czasu usługi RADIUS.

1. Na serwerze bramy usług pulpitu zdalnego Otwórz Menedżer serwera. W menu kliknij pozycję **Narzędzia**, a następnie kliknij pozycję **serwer zasad sieciowych**.
1. W konsoli **serwera NPS (lokalnego)** rozwiń węzeł **klienci i serwery usługi RADIUS**, a następnie wybierz pozycję **zdalny serwer RADIUS**.

   ![Konsola zarządzania serwerem zasad sieciowych pokazująca zdalny serwer usługi RADIUS](./media/howto-mfa-nps-extension-rdg/image12.png)

1. W okienku szczegółów kliknij dwukrotnie pozycję **Grupa serwerów bramy usług terminalowych**.

   >[!NOTE]
   >Ta grupa serwerów RADIUS została utworzona podczas konfigurowania serwera centralnego dla zasad serwera NPS. Brama usług pulpitu zdalnego przekazuje komunikaty RADIUS do tego serwera lub grupy serwerów, jeśli więcej niż jeden w grupie.
   >

1. W oknie dialogowym **Właściwości grupy serwerów bramy usług terminalowych** wybierz adres IP lub nazwę serwera NPS, który został skonfigurowany do przechowywania usług RD CAP, a następnie kliknij przycisk **Edytuj**.

   ![Wybierz adres IP lub nazwę serwera zasad sieciowych skonfigurowany wcześniej](./media/howto-mfa-nps-extension-rdg/image13.png)

1. W oknie dialogowym **Edytowanie serwera usługi RADIUS** wybierz kartę **równoważenie obciążenia** .
1. Na karcie **równoważenie obciążenia** w polu **Liczba sekund bez odpowiedzi zanim żądanie zostanie uznane za porzucone** , Zmień wartość domyślną z 3 na wartość z zakresu od 30 do 60 sekund.
1. W polu **Liczba sekund między żądaniami, gdy serwer jest zidentyfikowany jako niedostępny** , Zmień wartość domyślną 30 sekund na wartość, która jest równa lub większa niż wartość określona w poprzednim kroku.

   ![Edytowanie ustawień limitu czasu serwera usługi RADIUS na karcie Równoważenie obciążenia](./media/howto-mfa-nps-extension-rdg/image14.png)

1. Dwa razy kliknij przycisk **OK** , aby zamknąć okna dialogowe.

### <a name="verify-connection-request-policies"></a>Weryfikowanie zasad żądań połączeń

Domyślnie podczas konfigurowania bramy usług pulpitu zdalnego do używania centralnego magazynu zasad na potrzeby zasad autoryzacji połączeń Brama usług pulpitu zdalnego jest skonfigurowana do przesyłania dalej żądań CAP do serwera NPS. Serwer NPS z zainstalowanym rozszerzeniem usługi Azure AD MFA przetwarza żądanie dostępu usługi RADIUS. Poniższe kroki pokazują, jak sprawdzić domyślne zasady żądań połączeń.  

1. Na bramie usług pulpitu zdalnego, w konsoli serwera NPS (lokalnego) rozwiń węzeł **zasady** i wybierz pozycję **zasady żądań połączeń**.
1. Kliknij dwukrotnie pozycję **zasady autoryzacji bramy usług terminalowych**.
1. W oknie dialogowym **Właściwości zasad autoryzacji bramy usług terminalowych** kliknij kartę **Ustawienia** .
1. Na karcie **Ustawienia** w obszarze przekazywanie żądania połączenia kliknij pozycję **uwierzytelnianie**. Klient usługi RADIUS jest skonfigurowany do przesyłania dalej żądań uwierzytelniania.

   ![Skonfiguruj ustawienia uwierzytelniania określające grupę serwerów](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Kliknij przycisk **Anuluj**.

>[!NOTE]
> Aby uzyskać więcej informacji na temat tworzenia zasad żądań połączeń, zobacz artykuł [Konfiguracja zasad żądań połączeń](/windows-server/networking/technologies/nps/nps-crp-configure#add-a-connection-request-policy) jest taka sama. 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Skonfiguruj serwer zasad sieciowych na serwerze, na którym zainstalowano rozszerzenie serwera NPS

Serwer NPS, na którym jest zainstalowane rozszerzenie serwera NPS, musi mieć możliwość wymiany komunikatów usługi RADIUS z serwerem NPS na bramie Pulpit zdalny. Aby włączyć tę wymianę wiadomości, należy skonfigurować składniki NPS na serwerze, na którym jest zainstalowana usługa rozszerzenia serwera NPS.

### <a name="register-server-in-active-directory"></a>Zarejestruj serwer w Active Directory

Aby zapewnić prawidłowe działanie w tym scenariuszu, należy zarejestrować serwer NPS w Active Directory.

1. Na serwerze NPS Otwórz **Menedżer serwera**.
1. W Menedżerze serwera kliknij **narzędzia**, a następnie kliknij przycisk **serwer zasad sieciowych**.
1. W konsoli serwera zasad sieciowych kliknij prawym przyciskiem myszy serwer **NPS (lokalny)**, a następnie kliknij pozycję **zarejestruj serwer w Active Directory**.
1. Dwa razy kliknij przycisk **OK** .

   ![Rejestrowanie serwera NPS w Active Directory](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Pozostaw otwartą konsolę, aby uzyskać następną procedurę.

### <a name="create-and-configure-radius-client"></a>Tworzenie i Konfigurowanie klienta usługi RADIUS

Brama Pulpit zdalny musi być skonfigurowana jako Klient RADIUS serwera NPS.

1. Na serwerze NPS, na którym zainstalowano rozszerzenie serwera NPS, w konsoli **serwera NPS (lokalnej)** kliknij prawym przyciskiem myszy pozycję **klienci usługi RADIUS** i kliknij pozycję **Nowy**.

   ![Tworzenie nowego klienta usługi RADIUS w konsoli serwera NPS](./media/howto-mfa-nps-extension-rdg/image17.png)

1. W oknie dialogowym **Nowy klient usługi RADIUS** Podaj przyjazną nazwę, taką jak _brama_, oraz adres IP lub nazwę DNS serwera bramy pulpit zdalny.
1. W polu wspólny **klucz tajny** i **Potwierdź wspólne hasło** wprowadź to samo hasło, które zostało użyte wcześniej.

   ![Skonfiguruj przyjazną nazwę i adres IP lub DNS](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Kliknij przycisk **OK** , aby zamknąć okno dialogowe Nowy klient RADIUS.

### <a name="configure-network-policy"></a>Konfigurowanie zasad sieciowych

Należy odwołać się, że serwer NPS z rozszerzeniem usługi Azure AD MFA jest wskazanym centralnym magazynem zasad dla zasad autoryzacji połączeń (CAP). W związku z tym należy zaimplementować zakończenie na serwerze NPS w celu autoryzowania prawidłowych żądań połączeń.  

1. Na serwerze NPS Otwórz konsolę serwera NPS (lokalna), rozwiń węzeł **zasady**, a następnie kliknij pozycję **zasady sieciowe**.
1. Kliknij prawym przyciskiem myszy pozycję **połączenia z innymi serwerami dostępu**, a następnie kliknij pozycję **Duplikuj zasady**.

   ![Duplikowanie połączenia z innymi zasadami serwerów dostępu](./media/howto-mfa-nps-extension-rdg/image19.png)

1. Kliknij prawym przyciskiem myszy pozycję **kopia połączeń z innymi serwerami dostępu**, a następnie kliknij pozycję **Właściwości**.
1. W oknie dialogowym **kopia połączeń z innymi serwerami dostępu** w polu **Nazwa zasad** wprowadź odpowiednią nazwę, taką jak _RDG_CAP_. Sprawdź **włączone zasady** i wybierz pozycję **Udziel dostępu**. Opcjonalnie w obszarze **Typ serwera dostępu do sieci** wybierz pozycję **brama pulpit zdalny** lub pozostaw ją **nieokreślony**.

   ![Nazwij zasady, Włącz i Udziel dostępu](./media/howto-mfa-nps-extension-rdg/image21.png)

1. Kliknij kartę **ograniczenia** , a następnie zaznacz pole wyboru **Zezwalaj klientom na łączenie się bez negocjowania metody uwierzytelniania**.

   ![Modyfikowanie metod uwierzytelniania, aby umożliwić klientom nawiązywanie połączeń](./media/howto-mfa-nps-extension-rdg/image22.png)

1. Opcjonalnie kliknij kartę **warunki** i Dodaj warunki, które muszą zostać spełnione, aby połączenie było autoryzowane, na przykład z członkostwem w określonej grupie systemu Windows.

   ![Opcjonalnie określ warunki połączenia](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Kliknij przycisk **OK**. Po wyświetleniu monitu o wyświetlenie odpowiedniego tematu pomocy kliknij przycisk **nie**.
1. Upewnij się, że nowe zasady są na początku listy, że zasady są włączone i że mają dostęp.

   ![Przenieś zasady na początek listy](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Weryfikuj konfigurację

Aby sprawdzić konfigurację, należy zalogować się do bramy Pulpit zdalny przy użyciu odpowiedniego klienta RDP. Upewnij się, że korzystasz z konta, które jest dozwolone przez zasady autoryzacji połączeń i włączono usługę Azure AD MFA.

Jak pokazano na poniższej ilustracji, możesz użyć strony **Dostęp w sieci Web pulpit zdalny** .

![Testowanie w Pulpit zdalny Dostęp w sieci Web](./media/howto-mfa-nps-extension-rdg/image25.png)

Po pomyślnym wprowadzeniu poświadczeń uwierzytelniania podstawowego w oknie dialogowym Pulpit zdalny Połącz zostanie wyświetlony stan Inicjowanie połączenia zdalnego, jak pokazano poniżej. 

W przypadku pomyślnego uwierzytelnienia za pomocą pomocniczej metody uwierzytelniania, która została wcześniej skonfigurowana w usłudze Azure AD MFA, nawiązano połączenie z zasobem. Jeśli jednak uwierzytelnianie pomocnicze zakończy się niepowodzeniem, odmówiono dostępu do zasobu. 

![Podłączanie pulpitu zdalnego inicjowania połączenia zdalnego](./media/howto-mfa-nps-extension-rdg/image26.png)

W poniższym przykładzie aplikacja Authenticator w systemie Windows Phone jest używana do zapewnienia uwierzytelniania pomocniczego.

![Przykład Windows Phone aplikacji Authenticator, która wyświetla weryfikację](./media/howto-mfa-nps-extension-rdg/image27.png)

Po pomyślnym uwierzytelnieniu przy użyciu dodatkowej metody uwierzytelniania użytkownik jest zalogowany do bramy Pulpit zdalny jako normalny. Jednak ze względu na to, że wymagane jest użycie pomocniczej metody uwierzytelniania przy użyciu aplikacji mobilnej na zaufanym urządzeniu, proces logowania jest bezpieczniejszy niż w przeciwnym razie.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Wyświetlanie dzienników Podgląd zdarzeń dla zdarzeń pomyślnego logowania

Aby wyświetlić pomyślne zdarzenia logowania w dziennikach Podgląd zdarzeń systemu Windows, można wydać następujące polecenie programu Windows PowerShell służące do wykonywania zapytań dotyczących usług terminalowych systemu Windows i dzienników zabezpieczeń systemu Windows.

Aby zbadać pomyślne zdarzenia logowania w dziennikach operacyjnych bramy _(usługi Event Viewer\Applications i usług Logs\Microsoft\Windows\TerminalServices-Gateway\Operational)_, użyj następujących poleceń programu PowerShell:

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* To polecenie wyświetla zdarzenia systemu Windows, które pokazują, że zostały spełnione wymagania zasad autoryzacji zasobów (RD RAP) i udzielono im dostępu.

![Wyświetlanie zdarzeń przy użyciu programu PowerShell](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* To polecenie wyświetla zdarzenia, które są wyświetlane, gdy użytkownik przestrzega wymagań zasad autoryzacji połączeń.

![Wyświetlanie zasad autoryzacji połączeń przy użyciu programu PowerShell](./media/howto-mfa-nps-extension-rdg/image29.png)

Możesz również wyświetlić ten dziennik i filtr dla identyfikatorów zdarzeń, 300 i 200. Aby zbadać pomyślne zdarzenia logowania w dziennikach Podglądu zdarzeń zabezpieczeń, użyj następującego polecenia:

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* To polecenie można uruchomić na Central serwer NPS lub na serwerze bramy usług pulpitu zdalnego.

![Przykładowe zdarzenia pomyślnego logowania](./media/howto-mfa-nps-extension-rdg/image30.png)

Możesz również wyświetlić dziennik zabezpieczeń lub widok niestandardowy usług zasad sieciowych i dostępu sieciowego, jak pokazano poniżej:

![Usługi zasad sieciowych i dostępu sieciowego Podgląd zdarzeń](./media/howto-mfa-nps-extension-rdg/image31.png)

Na serwerze, na którym zainstalowano rozszerzenie serwera NPS dla usługi Azure AD MFA, można znaleźć Dzienniki aplikacji Podgląd zdarzeń specyficzne dla rozszerzenia w _Logs\Microsoft\AzureMfa aplikacji i usług_.

![Podgląd zdarzeń autoryzacji dzienników aplikacji](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Przewodnik rozwiązywania problemów

Jeśli konfiguracja nie działa zgodnie z oczekiwaniami, pierwsze miejsce do rozpoczęcia rozwiązywania problemów polega na sprawdzeniu, czy użytkownik jest skonfigurowany do korzystania z usługi Azure AD MFA. Użytkownik nawiązuje połączenie z [Azure Portal](https://portal.azure.com). Jeśli użytkownik zostanie poproszony o weryfikację pomocniczą i może się uwierzytelnić, można wyeliminować niepoprawną konfigurację usługi Azure AD MFA.

Jeśli usługa Azure AD MFA działa dla użytkowników, należy przejrzeć odpowiednie dzienniki zdarzeń. Obejmują one zdarzenia zabezpieczeń, operacyjne bramy i dzienniki usługi Azure AD MFA omówione w poprzedniej sekcji.

Poniżej znajduje się przykładowe dane wyjściowe dziennika zabezpieczeń przedstawiające nieudane zdarzenie logowania (Identyfikator zdarzenia 6273).

![Przykład zdarzenia nieudanego logowania](./media/howto-mfa-nps-extension-rdg/image33.png)

Poniżej znajduje się zdarzenie powiązane z dzienników AzureMFA:

![Przykład logowania do usługi Azure AD MFA w Podgląd zdarzeń](./media/howto-mfa-nps-extension-rdg/image34.png)

Aby skorzystać z zaawansowanych opcji rozwiązywania problemów, zapoznaj się z plikami dziennika w formacie bazy danych serwera NPS, w których zainstalowano usługę NPS. Te pliki dzienników są tworzone w folderze _%systemroot%\System32\Logs_ jako pliki tekstowe rozdzielane przecinkami.

Aby uzyskać opis tych plików dziennika, zobacz [Interpretowanie plików dziennika w formacie serwera NPS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771748(v=ws.10)). Wpisy w tych plikach dziennika mogą być trudne do zinterpretowania bez importowania ich do arkusza kalkulacyjnego lub bazy danych. Kilka analiz usługi IAS można znaleźć w trybie online, aby pomóc w interpretacji plików dziennika.

Na poniższym obrazie przedstawiono dane wyjściowe z jednej z takich [aplikacji "shareware"](https://www.deepsoftware.com/iasviewer)do pobrania.

![Przykładowy Parser usługi IAS dla aplikacji "shareware"](./media/howto-mfa-nps-extension-rdg/image35.png)

Na koniec w celu uzyskania dodatkowych opcji rozwiązywania problemów można użyć analizatora protokołów, takiego jak [Microsoft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide).

Poniższy obraz z programu Microsoft Message Analyzer pokazuje ruch sieciowy filtrowany w protokole RADIUS zawierającym nazwę użytkownika **CONTOSO\AliceC**.

![Program Microsoft Message Analyzer pokazujący filtrowany ruch](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Następne kroki

[Jak uzyskać Multi-Factor Authentication usługi Azure AD](concept-mfa-licensing.md)

[Brama usług pulpitu zdalnego i serwer Azure Multi-Factor Authentication korzystające z usługi RADIUS](howto-mfaserver-nps-rdg.md)

[Integrowanie katalogów lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md)