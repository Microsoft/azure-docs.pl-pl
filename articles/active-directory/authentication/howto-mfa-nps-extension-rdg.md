---
title: Integrowanie usługi RDG z rozszerzeniem serwera NPS usługi Azure AD MFA — Azure Active Directory
description: Integrowanie infrastruktury Pulpit zdalny Gateway z usługą Azure AD MFA przy użyciu rozszerzenia serwera zasad sieciowych dla Microsoft Azure
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
ms.openlocfilehash: 6f50792ec45570f7e90893a97150ea26b63ebf9c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829840"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Integrowanie infrastruktury Pulpit zdalny Gateway przy użyciu rozszerzenia serwera zasad sieciowych (NPS) i usługi Azure AD

Ten artykuł zawiera szczegółowe informacje dotyczące integracji infrastruktury usługi Pulpit zdalny Gateway z usługą Azure AD Multi-Factor Authentication (MFA) przy użyciu rozszerzenia serwera zasad sieciowych (NPS) na Microsoft Azure.

Rozszerzenie serwera zasad sieciowych (NPS) dla platformy Azure umożliwia klientom ochronę uwierzytelniania klienta usługi Usługa telefonujących użytkowników zdalnego uwierzytelniania (RADIUS) przy użyciu usługi [Multi-Factor Authentication (MFA)](./concept-mfa-howitworks.md)opartej na chmurze platformy Azure. To rozwiązanie zapewnia weryfikację dwuetapową w celu dodania drugiej warstwy zabezpieczeń do operacji logowania i transakcji użytkownika.

Ten artykuł zawiera instrukcje krok po kroku dotyczące integrowania infrastruktury serwera NPS z usługą Azure AD MFA przy użyciu rozszerzenia serwera NPS dla platformy Azure. Umożliwia to bezpieczną weryfikację dla użytkowników próbujących zalogować się do Pulpit zdalny Gateway.

> [!NOTE]
> Ten artykuł nie powinien być używany z wdrożeniami serwera MFA i powinien być używany tylko z wdrożeniami usługi Azure AD MFA (opartymi na chmurze).

Usługi zasad sieciowych i dostępu sieciowego (NPS) dają organizacjom możliwość wykonywania następujących czynności:

* Zdefiniuj centralne lokalizacje na potrzeby zarządzania żądaniami sieciowymi i kontrolowania ich, określając, kto może nawiązać połączenie, jaka jest liczba dozwolonych godzin połączeń, czas trwania połączeń oraz poziom zabezpieczeń, z których klienci muszą się łączyć, i tak dalej. Zamiast określać te zasady na każdym serwerze bramy Pulpit zdalny sieci VPN lub bramie usług pulpitu zdalnego, można je określić raz w centralnej lokalizacji. Protokół RADIUS zapewnia scentralizowane uwierzytelnianie, autoryzację i ewidencjonowanie wartości (AAA).
* Ustanów i wymusz zasady kondycji klienta ochrony dostępu do sieci (NAP), które określają, czy na urządzeniach jest udzielany nieograniczony, czy ograniczony dostęp do zasobów sieciowych.
* Zapewnienie metody wymuszania uwierzytelniania i autoryzacji dostępu do punktów dostępu bezprzewodowego i przełączników Ethernet z możliwością standardu 802.1x.

Zazwyczaj organizacje używają serwera NPS (RADIUS) w celu uproszczenia i scentralizować zarządzanie zasadami sieci VPN. Jednak wiele organizacji również za pomocą serwera ZASAD, aby uprościć i scentralizować zarządzanie zasadami autoryzacji połączeń usług pulpitu zdalnego (RD CAP).

Organizacje mogą również zintegrować serwer NPS z usługą Azure AD MFA w celu zwiększenia bezpieczeństwa i zapewnienia wysokiego poziomu zgodności. Pomaga to zapewnić, że użytkownicy ustanowią weryfikację dwuetapową w celu zalogowania się do Pulpit zdalny Gateway. Aby użytkownikom udzielono dostępu, muszą podać kombinację nazwy użytkownika i hasła wraz z informacjami, które użytkownik ma pod kontrolą. Te informacje muszą być zaufane i nie mogą być łatwo zduplikowane, takie jak numer telefonu komórkowego, numer telefonu stacjonarnego, aplikacja na urządzeniu przenośnym itp. RdG obecnie obsługuje połączenia telefoniczne i powiadomienia wypychane z metod aplikacji Microsoft Authenticator dla 2FA. Aby uzyskać więcej informacji na temat obsługiwanych metod uwierzytelniania, zobacz sekcję [Określanie metod uwierzytelniania, których użytkownicy mogą używać.](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use)

Przed dostępnością rozszerzenia serwera NPS dla platformy Azure klienci, którzy chcieli zaimplementować weryfikację dwuetapową dla zintegrowanych środowisk NPS i Azure AD MFA, musieli skonfigurować i utrzymywać oddzielny serwer MFA w środowisku lokalnym, zgodnie z dokumentacją w usłudze Pulpit zdalny Gateway i usłudze Azure Serwer Multi-Factor Authentication przy użyciu [usługi RADIUS.](howto-mfaserver-nps-rdg.md)

Dostępność rozszerzenia serwera NPS dla platformy Azure umożliwia teraz organizacjom wdrożenie lokalnego rozwiązania MFA lub opartego na chmurze rozwiązania MFA w celu zabezpieczenia uwierzytelniania klienta usługi RADIUS.

## <a name="authentication-flow"></a>Przepływ uwierzytelniania

Aby użytkownicy mieli dostęp do zasobów sieciowych za pośrednictwem bramy usługi Pulpit zdalny, muszą spełniać warunki określone w jednej zasadach autoryzacji połączeń usług pulpitu zdalnego (RD CAP) i jednej zasadach autoryzacji zasobów usług pulpitu zdalnego (RD RAP). Rd CAPs określić, kto jest autoryzowany do łączenia się z bramami usług pulpitu zdalnego. Rd RD RAPs określić zasobów sieciowych, takich jak pulpity zdalne lub aplikacje zdalne, które użytkownik może połączyć się za pośrednictwem bramy usług pulpitu zdalnego.

Brama usług pulpitu zdalnego można skonfigurować do używania centralnego magazynu zasad dla rd rd CAPs. RPO usług pulpitu zdalnego nie może używać zasad centralnych, ponieważ są przetwarzane w bramie usług pulpitu zdalnego. Przykładem bramy usług pulpitu zdalnego skonfigurowanego do używania centralnego magazynu zasad dla dostępu klienta usług pulpitu zdalnego jest klient RADIUS do innego serwera NPS, który służy jako centralny magazyn zasad.

Jeśli rozszerzenie serwera NPS dla platformy Azure jest zintegrowane z usługą NPS i bramą Pulpit zdalny Gateway, pomyślny przepływ uwierzytelniania jest następujący:

1. Serwer Pulpit zdalny Gateway odbiera żądanie uwierzytelnienia od użytkownika pulpitu zdalnego w celu nawiązania połączenia z zasobem, takim jak Pulpit zdalny sesji. Działający jako klient RADIUS serwer usługi Pulpit zdalny Gateway konwertuje żądanie na komunikat usługi Access-Request usługi RADIUS i wysyła komunikat do serwera RADIUS (NPS), na którym zainstalowano rozszerzenie serwera NPS.
1. Kombinacja nazwy użytkownika i hasła jest weryfikowana w usłudze Active Directory, a użytkownik jest uwierzytelniany.
1. Jeśli są spełnione wszystkie warunki określone w żądaniu połączenia serwera NPS i zasadach sieciowych (na przykład ograniczenia członkostwa w godzinach dnia lub grupy), rozszerzenie serwera NPS wyzwala żądanie dodatkowego uwierzytelniania za pomocą usługi Azure AD MFA.
1. Usługa Azure AD MFA komunikuje się z usługą Azure AD, pobiera szczegóły użytkownika i wykonuje uwierzytelnianie pomocnicze przy użyciu obsługiwanych metod.
1. Po sukcesie uwierzytelniania wieloskładnikowego uwierzytelniania wieloskładnikowego usługa Azure AD MFA komunikuje wynik z rozszerzeniem serwera NPS.
1. Serwer NPS, na którym zainstalowano rozszerzenie, wysyła komunikat Access-Accept RADIUS dla zasad RD CAP do serwera Pulpit zdalny Gateway.
1. Użytkownikowi udzielono dostępu do żądanego zasobu sieciowego za pośrednictwem bramy usług pulpitu zdalnego.

## <a name="prerequisites"></a>Wymagania wstępne

W tej sekcji szczegółowo o wymaganiach wstępnych, które należy spełnić przed zintegrowaniem usługi Azure AD MFA z Pulpit zdalny Gateway. Przed rozpoczęciem należy spełnić następujące wymagania wstępne.  

* Usługi pulpitu zdalnego (RDS)
* Licencja usługi Azure AD MFA
* Oprogramowanie systemu Windows Server
* Rola usług zasad sieciowych i dostępu sieciowego (NPS)
* Azure Active Directory zsynchronizowane z lokalna usługa Active Directory
* Azure Active Directory identyfikatora GUID

### <a name="remote-desktop-services-rds-infrastructure"></a>Usługi pulpitu zdalnego infrastruktury usług pulpitu zdalnego

Musisz mieć w miejscu Usługi pulpitu zdalnego (RDS). Jeśli tego nie zrobisz, możesz szybko utworzyć tę infrastrukturę na platformie Azure przy użyciu następującego szablonu szybkiego startu: Tworzenie Pulpit zdalny [kolekcji sesji.](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment)

Jeśli chcesz szybko ręcznie utworzyć lokalną infrastrukturę usług pulpitu zdalnego na potrzeby testowania, wykonaj kroki, aby ją wdrożyć.
**Dowiedz się więcej:** [Wdrażanie usług pulpitu zdalnego za pomocą przewodnika Szybki start platformy Azure](/windows-server/remote/remote-desktop-services/rds-in-azure) i podstawowego wdrożenia infrastruktury usług [pulpitu zdalnego.](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)

### <a name="azure-ad-mfa-license"></a>Licencja usługi Azure AD MFA

Wymagana jest licencja usługi Azure AD MFA, która jest dostępna za pośrednictwem Azure AD — wersja Premium lub innych pakietów, które go zawierają. Licencje oparte na użyciu dla usługi Azure AD MFA, takie jak licencje na użytkownika lub na uwierzytelnianie, nie są zgodne z rozszerzeniem serwera NPS. Aby uzyskać więcej informacji, zobacz How to get Azure AD Multi-Factor Authentication (Jak uzyskać [usługę Azure AD Multi-Factor Authentication).](concept-mfa-licensing.md) Do celów testowych możesz użyć subskrypcji wersji próbnej.

### <a name="windows-server-software"></a>Oprogramowanie systemu Windows Server

Rozszerzenie serwera NPS wymaga systemu Windows Server 2008 R2 z dodatkiem SP1 lub więcej z zainstalowaną usługą roli serwera NPS. Wszystkie kroki w tej sekcji zostały wykonane przy użyciu systemu Windows Server 2016.

### <a name="network-policy-and-access-services-nps-role"></a>Rola usług zasad sieciowych i dostępu sieciowego (NPS)

Usługa roli serwera NPS udostępnia funkcje serwera RADIUS i klienta, a także usługę kondycji zasad dostępu do sieci. Ta rola musi być zainstalowana na co najmniej dwóch komputerach w infrastrukturze: bramie Pulpit zdalny i innym serwerze członkowskim lub kontrolerze domeny. Domyślnie rola jest już obecna na komputerze skonfigurowanym jako Pulpit zdalny Gateway.  Należy również zainstalować rolę serwera NPS na co najmniej na innym komputerze, takim jak kontroler domeny lub serwer członkowski.

Aby uzyskać informacje na temat instalowania usługi roli serwera NPS systemu Windows Server 2012 lub starszego, zobacz [Instalowanie serwera zasad kondycji ochrony sieci](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd296890(v=ws.10)). Opis najlepszych rozwiązań dla serwera NPS, w tym zalecenia dotyczące instalowania serwera NPS na kontrolerze domeny, zobacz [Najlepsze rozwiązania dotyczące serwera NPS.](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771746(v=ws.10))

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory zsynchronizowane z lokalna usługa Active Directory

Aby korzystać z rozszerzenia serwera NPS, użytkownicy lokalni muszą być zsynchronizowani z usługą Azure AD i włączoną usługę MFA. W tej sekcji założono, że użytkownicy lokalni są synchronizowane z usługą Azure AD przy użyciu programu AD Connect. Aby uzyskać informacje na temat programu Azure AD Connect, [zobacz Integrowanie](../hybrid/whatis-hybrid-identity.md)katalogów lokalnych z Azure Active Directory .

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory IDENTYFIKATORA GUID

Aby zainstalować rozszerzenie serwera NPS, musisz znać identyfikator GUID usługi Azure AD. Poniżej przedstawiono instrukcje dotyczące znajdowania identyfikatora GUID usługi Azure AD.

## <a name="configure-multi-factor-authentication"></a>Konfigurowanie uwierzytelniania wieloskładnikowego

Ta sekcja zawiera instrukcje dotyczące integrowania usługi Azure AD MFA z Pulpit zdalny Gateway. Jako administrator musisz skonfigurować usługę Azure AD MFA, aby użytkownicy mogą samodzielnie rejestrować swoje wieloskładnikowe urządzenia lub aplikacje.

Postępuj zgodnie z instrukcjami z tematu Wprowadzenie do usługi [Azure AD Multi-Factor Authentication](howto-mfa-getstarted.md) w chmurze, aby włączyć usługę MFA dla użytkowników usługi Azure AD.

### <a name="configure-accounts-for-two-step-verification"></a>Konfigurowanie kont do weryfikacji dwuetapowej

Po włączeniu usługi MFA dla konta nie można zalogować się do zasobów zarządzanych przez zasady uwierzytelniania wieloskładnikowego, dopóki zaufane urządzenie nie zostanie pomyślnie skonfigurowane do użycia na potrzeby drugiego współczynnika uwierzytelniania i nie zostanie uwierzytelnione przy użyciu weryfikacji dwuetapowej.

Postępuj zgodnie z instrukcjami z tematu Co usługa [Azure AD Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user-first-time.md) oznacza dla mnie?, aby zrozumieć i prawidłowo skonfigurować urządzenia do uwierzytelniania wieloskładnikowego przy użyciu konta użytkownika.

> [!IMPORTANT]
> Zachowanie logowania dla usługi Pulpit zdalny Gateway nie umożliwia wprowadzenia kodu weryfikacyjnego przy użyciu usługi Azure AD Multi-Factor Authentication. Konto użytkownika musi być skonfigurowane do weryfikacji przez telefon lub do aplikacji Microsoft Authenticator z powiadomieniami push.
>
> Jeśli dla użytkownika nie skonfigurowano weryfikacji telefonu ani aplikacji Microsoft Authenticator z powiadomieniami wypychania, użytkownik nie będzie mógł ukończyć zadania uwierzytelniania wieloskładnikowego usługi Azure AD i zalogować się do usługi Pulpit zdalny Gateway.
>
> Metoda tekstowa SMS nie działa z usługą Pulpit zdalny Gateway, ponieważ nie umożliwia wprowadzenia kodu weryfikacyjnego.

## <a name="install-and-configure-nps-extension"></a>Instalowanie i konfigurowanie rozszerzenia serwera NPS

Ta sekcja zawiera instrukcje dotyczące konfigurowania infrastruktury usług pulpitu zdalnego do używania usługi Azure AD MFA do uwierzytelniania klienta za pomocą Pulpit zdalny Gateway.

### <a name="acquire-azure-active-directory-tenant-id"></a>Uzyskaj Azure Active Directory dzierżawy

W ramach konfiguracji rozszerzenia serwera NPS należy podać poświadczenia administratora i identyfikator usługi Azure AD dla dzierżawy usługi Azure AD. Aby uzyskać identyfikator dzierżawy, wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) jako administrator globalny dzierżawy platformy Azure.
1. W menu Azure Portal wybierz pozycję **Azure Active Directory** lub wyszukaj i **wybierz Azure Active Directory** na dowolnej stronie.
1. Na stronie **Przegląd** są wyświetlane *informacje o* dzierżawie. Obok identyfikatora *dzierżawy* wybierz ikonę **Kopiuj,** jak pokazano na poniższym przykładowym zrzucie ekranu:

   ![Pobieranie identyfikatora dzierżawy z Azure Portal](./media/howto-mfa-nps-extension-rdg/azure-active-directory-tenant-id-portal.png)

### <a name="install-the-nps-extension"></a>Instalowanie rozszerzenia serwera NPS

Zainstaluj rozszerzenie serwera NPS na serwerze z zainstalowaną rolą usług zasad sieciowych i dostępu sieciowego (NPS). Ta funkcja działa jako serwer RADIUS w twoim projekcie.

> [!IMPORTANT]
> Nie instaluj rozszerzenia serwera NPS na serwerze Pulpit zdalny Gateway (RDG). Serwer RDG nie używa protokołu RADIUS ze swoim klientem, dlatego rozszerzenie nie może interpretować i wykonywać uwierzytelniania wieloskładnikowego.
>
> Gdy serwer RDG i serwer NPS z rozszerzeniem serwera NPS są różne serwery, RDG używa serwera NPS wewnętrznie do komunikowania się z innymi serwerami NPS i używa protokołu RADIUS jako protokołu do poprawnej komunikacji.

1. Pobierz [rozszerzenie serwera NPS](https://aka.ms/npsmfa).
1. Skopiuj plik wykonywalny instalatora (NpsExtnForAzureMfaInstaller.exe) na serwer NPS.
1. Na serwerze NPS kliknij dwukrotnie pozycję **NpsExtnForAzureMfaInstaller.exe**. Jeśli zostanie wyświetlony monit, kliknij pozycję **Uruchom**.
1. W oknie dialogowym Rozszerzenie serwera NPS dla instalatora usługi Azure AD MFA przejrzyj postanowienia licencyjne dotyczące oprogramowania, zaznacz pole wyboru Wyrażam zgodę na warunki i postanowienia **licencyjne,** a następnie kliknij przycisk **Zainstaluj**.
1. W oknie dialogowym Rozszerzenie serwera NPS dla instalatora usługi Azure AD MFA kliknij **przycisk Zamknij**.

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Konfigurowanie certyfikatów do użycia z rozszerzeniem serwera NPS przy użyciu skryptu programu PowerShell

Następnie należy skonfigurować certyfikaty do użycia przez rozszerzenie serwera NPS, aby zapewnić bezpieczną komunikację i gwarancję. Składniki serwera NPS zawierają skrypt Windows PowerShell, który konfiguruje certyfikat z podpisem własnym do użycia z serwerami NPS.

Skrypt wykonuje następujące akcje:

* Tworzy certyfikat z podpisem własnym
* Kojarzy klucz publiczny certyfikatu z jednostką usługi w usłudze Azure AD
* Przechowuje certyfikat w magazynie komputera lokalnego
* Udziela dostępu do klucza prywatnego certyfikatu użytkownikowi sieci
* Ponownie uruchamia usługę serwera zasad sieciowych

Jeśli chcesz użyć własnych certyfikatów, musisz skojarzyć klucz publiczny certyfikatu z jednostką usługi w usłudze Azure AD i tak dalej.

Aby użyć skryptu, podaj rozszerzenie przy użyciu skopiowanych wcześniej poświadczeń administratora usługi Azure AD i identyfikatora dzierżawy usługi Azure AD. Uruchom skrypt na każdym serwerze NPS, na którym zainstalowano rozszerzenie serwera NPS. Następnie wykonaj poniższe czynności:

1. Otwórz wiersz Windows PowerShell administracyjnej.
1. W wierszu polecenia programu PowerShell wpisz `cd 'c:\Program Files\Microsoft\AzureMfa\Config'` i naciśnij klawisz **ENTER.**
1. Wpisz `.\AzureMfaNpsExtnConfigSetup.ps1` , a następnie naciśnij klawisz **ENTER.** Skrypt sprawdza, czy zainstalowano moduł Azure Active Directory PowerShell. Jeśli moduł nie zostanie zainstalowany, skrypt zainstaluje go automatycznie.

   ![Uruchamianie AzureMfaNpsExtnConfigSetup.ps1 w programie Azure AD PowerShell](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. Gdy skrypt zweryfikuje instalację modułu programu PowerShell, zostanie wyświetlone okno dialogowe Azure Active Directory programu PowerShell. W oknie dialogowym wprowadź poświadczenia administratora usługi Azure AD i hasło, a następnie kliknij **pozycję Zaloguj się.**

   ![Uwierzytelnianie w usłudze Azure AD w programie PowerShell](./media/howto-mfa-nps-extension-rdg/image5.png)

1. Po wyświetleniu monitu wklej identyfikator *dzierżawy* skopiowany wcześniej do schowka i naciśnij klawisz **ENTER.**

   ![Wprowadzanie identyfikatora dzierżawy w programie PowerShell](./media/howto-mfa-nps-extension-rdg/image6.png)

1. Skrypt tworzy certyfikat z podpisem własnym i wykonuje inne zmiany konfiguracji. Dane wyjściowe powinny przypominać obraz przedstawiony poniżej.

   ![Dane wyjściowe programu PowerShell przedstawiające certyfikat z podpisem własnym](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>Konfigurowanie składników serwera NPS w Pulpit zdalny Gateway

W tej sekcji skonfigurujesz zasady autoryzacji Pulpit zdalny gateway i inne ustawienia usługi RADIUS.

Przepływ uwierzytelniania wymaga wymiany komunikatów RADIUS między bramą Pulpit zdalny a serwerem NPS, na którym zainstalowano rozszerzenie serwera NPS. Oznacza to, że należy skonfigurować ustawienia klienta usługi RADIUS zarówno na bramie Pulpit zdalny, jak i na serwerze NPS, na którym zainstalowano rozszerzenie serwera NPS.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Konfigurowanie Pulpit zdalny autoryzacji połączeń bramy do używania magazynu centralnego

Pulpit zdalny autoryzacji połączeń określają wymagania dotyczące nawiązywania połączenia z serwerem Pulpit zdalny Bramy usług pulpitu zdalnego. Rd CAP mogą być przechowywane lokalnie (ustawienie domyślne) lub mogą być przechowywane w centralnym RD CAP magazynu z uruchomionym serwerem NPS. Aby skonfigurować integrację usługi Azure AD MFA z usługą RDS, należy określić użycie magazynu centralnego.

1. Na serwerze bramy usług pulpitu zdalnego otwórz **Menedżer serwera**.
1. W menu kliknij pozycję **Narzędzia**, wskaż polecenie **Usługi pulpitu zdalnego**, a następnie kliknij pozycję **Pulpit zdalny Menedżera bramy.**
1. W Menedżerze bramy usług pulpitu zdalnego kliknij prawym przyciskiem myszy **\[ nazwę serwera \] (lokalnego)**, a następnie kliknij polecenie **Właściwości.**
1. W oknie dialogowym Właściwości wybierz **kartę RD CAP Store.**
1. Na karcie RD CAP Store (Sklep sieciowy) wybierz **pozycję Central server running NPS (Centralny serwer z uruchomionym serwerem ZASAD).** 
1. W **Wprowadź nazwę lub** adres IP serwera NPS wpisz adres IP lub nazwę serwera serwera, na którym zainstalowano rozszerzenie serwera NPS.

   ![Wprowadź nazwę lub adres IP serwera NPS](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Kliknij pozycję **Dodaj**.
1. W **oknie dialogowym Wspólny** klucz tajny wprowadź wspólny klucz tajny, a następnie kliknij przycisk **OK.** Upewnij się, że zanotuj ten wspólny klucz tajny i bezpiecznie przechowujesz rekord.

   >[!NOTE]
   >Wspólny klucz tajny służy do ustanawiania relacji zaufania między serwerami i klientami usługi RADIUS. Utwórz długi i złożony klucz tajny.
   >

   ![Tworzenie wspólnego tajnego klucz w celu ustanowienia zaufania](./media/howto-mfa-nps-extension-rdg/image11.png)

1. Kliknij przycisk **OK**, aby zamknąć okno dialogowe.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>Konfigurowanie wartości limitu czasu usługi RADIUS na Pulpit zdalny NPS bramy

Aby mieć pewność, że jest czas na zweryfikowanie poświadczeń użytkowników, przeprowadzenie weryfikacji dwuetapowej, odbieranie odpowiedzi i odpowiadanie na komunikaty usługi RADIUS, należy dostosować wartość limitu czasu usługi RADIUS.

1. Na serwerze bramy usług pulpitu zdalnego otwórz Menedżer serwera. W menu kliknij pozycję **Narzędzia**, a następnie kliknij **pozycję Serwer zasad sieciowych.**
1. W konsoli **serwera NPS (lokalnego) rozwiń** pozycję Klienci i serwery **RADIUS,** a następnie wybierz **pozycję Zdalny serwer RADIUS.**

   ![Konsola zarządzania serwerem zasad sieciowych z wyświetloną usługą Zdalny serwer RADIUS](./media/howto-mfa-nps-extension-rdg/image12.png)

1. W okienku szczegółów kliknij dwukrotnie **TS GATEWAY SERVER GROUP**.

   >[!NOTE]
   >Ta grupa serwerów RADIUS została utworzona podczas konfigurowania serwera centralnego dla zasad serwera ZASAD SIECIOWYCH. Brama usług pulpitu zdalnego przekazuje komunikaty radius do tego serwera lub grupy serwerów, jeśli więcej niż jeden w grupie.
   >

1. W **oknie dialogowym** Właściwości GRUPY SERWERÓW BRAMY USŁUG TERMINALOWYCH wybierz adres IP lub nazwę serwera NPS skonfigurowanego do przechowywania adresów IP usług pulpitu zdalnego, a następnie kliknij przycisk **Edytuj**.

   ![Wybierz adres IP lub nazwę serwera NPS skonfigurowanego wcześniej](./media/howto-mfa-nps-extension-rdg/image13.png)

1. W **oknie dialogowym Edytowanie serwera RADIUS** wybierz **kartę Równoważenie** obciążenia.
1. Na **karcie Równoważenie** obciążenia  w polu Liczba sekund bez odpowiedzi przed usunięciem żądania zmień wartość domyślną z 3 na wartość z zakresu od 30 do 60 sekund.
1. W polu **Liczba** sekund między żądaniami, gdy serwer zostanie zidentyfikowany jako niedostępny, zmień wartość domyślną 30 sekund na wartość, która jest równa lub większa niż wartość określona w poprzednim kroku.

   ![Edytowanie ustawień limitu czasu serwera Radius na karcie równoważenia obciążenia](./media/howto-mfa-nps-extension-rdg/image14.png)

1. Kliknij **przycisk OK** dwa razy, aby zamknąć okna dialogowe.

### <a name="verify-connection-request-policies"></a>Weryfikowanie zasad żądań połączeń

Domyślnie podczas konfigurowania bramy usług pulpitu zdalnego do używania centralnego magazynu zasad dla zasad autoryzacji połączeń brama usług pulpitu zdalnego jest skonfigurowana do przekazywania żądań cap do serwera NPS. Serwer NPS z zainstalowanym rozszerzeniem usługi Azure AD MFA przetwarza żądanie dostępu usługi RADIUS. Poniższe kroki pokazują, jak zweryfikować domyślne zasady żądania połączenia.  

1. W bramie usług pulpitu zdalnego w konsoli serwera NPS (lokalnego) rozwiń pozycję **Zasady** i wybierz **pozycję Zasady żądań połączeń.**
1. Kliknij dwukrotnie **pozycję TS GATEWAY AUTHORIZATION POLICY (ZASADY AUTORYZACJI BRAMY USŁUG TERMINALOWYCH).**
1. W **oknie dialogowym Właściwości ZASAD AUTORYZACJI BRAMY** USŁUG TS kliknij **kartę** Ustawienia.
1. Na **karcie Ustawienia** w obszarze Przekazywanie żądania połączenia kliknij pozycję **Uwierzytelnianie.** Klient radius jest skonfigurowany do przekazywania żądań uwierzytelniania.

   ![Konfigurowanie ustawień uwierzytelniania określających grupę serwerów](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Kliknij przycisk **Anuluj**.

>[!NOTE]
> Aby uzyskać więcej informacji na temat tworzenia zasad żądań połączeń, zobacz artykuł Konfigurowanie zasad [żądań](/windows-server/networking/technologies/nps/nps-crp-configure#add-a-connection-request-policy) połączeń w tym samym celu. 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Konfigurowanie serwera NPS na serwerze, na którym jest zainstalowane rozszerzenie serwera NPS

Serwer NPS, na którym zainstalowano rozszerzenie serwera NPS, musi mieć możliwość wymiany komunikatów usługi RADIUS z serwerem NPS na Pulpit zdalny Gateway. Aby włączyć tę wymianę komunikatów, należy skonfigurować składniki serwera NPS na serwerze, na którym zainstalowano usługę rozszerzenia serwera NPS.

### <a name="register-server-in-active-directory"></a>Rejestrowanie serwera w usłudze Active Directory

Aby serwer NPS działał prawidłowo w tym scenariuszu, musi być zarejestrowany w usłudze Active Directory.

1. Na serwerze NPS otwórz **Menedżer serwera**.
1. W Menedżerze serwera kliknij **narzędzia**, a następnie kliknij przycisk **serwer zasad sieciowych**.
1. W konsoli serwera zasad sieciowych kliknij prawym przyciskiem myszy **serwer NPS (lokalny),** a następnie kliknij polecenie **Zarejestruj serwer w usłudze Active Directory.**
1. Kliknij **przycisk OK** dwa razy.

   ![Rejestrowanie serwera NPS w usłudze Active Directory](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Pozostaw konsolę otwartą na następną procedurę.

### <a name="create-and-configure-radius-client"></a>Tworzenie i konfigurowanie klienta usługi RADIUS

Brama Pulpit zdalny musi być skonfigurowana jako klient usługi RADIUS dla serwera NPS.

1. Na serwerze NPS, na którym zainstalowano rozszerzenie serwera NPS, w konsoli serwera **NPS (lokalnego)** kliknij prawym przyciskiem myszy pozycję **Klienci RADIUS** i kliknij polecenie **Nowy.**

   ![Tworzenie nowego klienta RADIUS w konsoli serwera NPS](./media/howto-mfa-nps-extension-rdg/image17.png)

1. W **oknie dialogowym** Nowy klient RADIUS podaj przyjazną nazwę, taką jak Brama _,_ oraz adres IP lub nazwę DNS serwera Pulpit zdalny Bramy.
1. W **polach Wspólny klucz tajny** i **Potwierdź wspólny** wpis tajny wprowadź ten sam wpis tajny, który był używany wcześniej.

   ![Konfigurowanie przyjaznej nazwy i adresu IP lub DNS](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Kliknij **przycisk OK,** aby zamknąć okno dialogowe Nowy klient RADIUS.

### <a name="configure-network-policy"></a>Konfigurowanie zasad sieciowych

Pamiętaj, że serwer NPS z rozszerzeniem usługi Azure AD MFA jest wyznaczonym centralnym magazynem zasad dla zasad autoryzacji połączeń (CAP). W związku z tym należy zaimplementować cap na serwerze NPS do autoryzowania prawidłowych żądań połączeń.  

1. Na serwerze NPS otwórz konsolę serwera NPS (lokalnego), rozwiń **pozycję Zasady** i kliknij pozycję **Zasady sieciowe.**
1. Kliknij prawym przyciskiem **myszy pozycję Połączenia z innymi serwerami dostępu,** a następnie kliknij polecenie **Duplikuj zasady.**

   ![Duplikowanie połączenia z innymi zasadami serwerów dostępu](./media/howto-mfa-nps-extension-rdg/image19.png)

1. Kliknij prawym przyciskiem **myszy kopię połączeń z innymi serwerami dostępu,** a następnie kliknij polecenie **Właściwości.**
1. W **oknie dialogowym Kopiowanie połączeń z** innymi serwerami dostępu w polu Nazwa zasad wprowadź odpowiednią _nazwę,_ taką jak RDG_CAP . Zaznacz **pole wyboru Zasady włączone** i wybierz pozycję U **przyznaj dostęp.** Opcjonalnie w opcji **Typ serwera dostępu** do sieci wybierz pozycję Pulpit zdalny **Gateway** lub pozostaw opcję **Nieokreślony.**

   ![Nazywanie zasad, włączanie i udzielanie dostępu](./media/howto-mfa-nps-extension-rdg/image21.png)

1. Kliknij **kartę Ograniczenia** i zaznacz pole wyboru **Zezwalaj klientom na łączenie się bez negocjowania metody uwierzytelniania**.

   ![Modyfikowanie metod uwierzytelniania w celu umożliwienia klientom nawiązywania połączenia](./media/howto-mfa-nps-extension-rdg/image22.png)

1. Opcjonalnie kliknij **kartę Warunki** i dodaj warunki, które muszą zostać spełnione, aby można było autoryzować połączenie, na przykład członkostwo w określonej grupie systemu Windows.

   ![Opcjonalnie określ warunki połączenia](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Kliknij przycisk **OK**. Po wyświetleniu monitu o wyświetlenie odpowiedniego tematu pomocy kliknij pozycję **Nie.**
1. Upewnij się, że nowe zasady są na początku listy, że zasady są włączone i że udzielają dostępu.

   ![Przenoszenie zasad na górę listy](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Weryfikowanie konfiguracji

Aby zweryfikować konfigurację, musisz zalogować się do usługi Pulpit zdalny Gateway przy użyciu odpowiedniego klienta RDP. Pamiętaj, aby użyć konta dozwolonego przez zasady autoryzacji połączeń i włączonego dla usługi Azure AD MFA.

Jak po widać na poniższej ilustracji, możesz użyć **Pulpit zdalny Dostęp w sieci Web** strony.

![Testowanie w Pulpit zdalny Dostęp w sieci Web](./media/howto-mfa-nps-extension-rdg/image25.png)

Po pomyślnym wprowadzeniu poświadczeń dla uwierzytelniania podstawowego w oknie dialogowym Pulpit zdalny Connect wyświetlany jest stan Inicjowanie połączenia zdalnego, jak pokazano poniżej. 

Jeśli pomyślnie uwierzytelnisz się przy użyciu dodatkowej metody uwierzytelniania skonfigurowanej wcześniej w usłudze Azure AD MFA, nałączono połączenie z zasobem. Jeśli jednak uwierzytelnianie pomocnicze nie powiedzie się, odmówiono dostępu do zasobu. 

![Podłączanie pulpitu zdalnego inicjowanie połączenia zdalnego](./media/howto-mfa-nps-extension-rdg/image26.png)

W poniższym przykładzie aplikacja Authenticator na telefonie z systemem Windows jest używana do uwierzytelniania pomocniczego.

![Przykładowa Windows Phone Authenticator przedstawiająca weryfikację](./media/howto-mfa-nps-extension-rdg/image27.png)

Po pomyślnym uwierzytelnieniu przy użyciu dodatkowej metody uwierzytelniania użytkownik jest zalogowany do usługi Pulpit zdalny Gateway w zwykły sposób. Jednak ze względu na to, że wymagane jest użycie dodatkowej metody uwierzytelniania przy użyciu aplikacji mobilnej na zaufanym urządzeniu, proces logowania jest bezpieczniejszy niż w przeciwnym razie.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Wyświetlanie Podgląd zdarzeń zdarzeń pomyślnego logowania

Aby wyświetlić zdarzenia pomyślnego logowania w dziennikach usługi Windows Podgląd zdarzeń, możesz wydać następujące polecenie Windows PowerShell, aby odpytywać usługi Terminal Windows i dzienniki Zabezpieczenia Windows.

Aby odpytywać zdarzenia pomyślnego logowania w dziennikach operacyjnych bramy _(Podgląd zdarzeń\Applications and Services Logs\Microsoft\Windows\TerminalServices-Gateway\Operational),_ użyj następujących poleceń programu PowerShell:

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* To polecenie wyświetla zdarzenia systemu Windows, które pokazują użytkownik spełnił wymagania zasad autoryzacji zasobów (RD RAP) i udzielono dostępu.

![Wyświetlanie zdarzeń przy użyciu programu PowerShell](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* To polecenie wyświetla zdarzenia, które są wyświetlane, gdy użytkownik spełnił wymagania zasad autoryzacji połączeń.

![wyświetlanie zasad autoryzacji połączeń przy użyciu programu PowerShell](./media/howto-mfa-nps-extension-rdg/image29.png)

Możesz również wyświetlić ten dziennik i odfiltrować identyfikatory zdarzeń 300 i 200. Aby odpytywać zdarzenia pomyślnego logowania w dziennikach podglądu zdarzeń zabezpieczeń, użyj następującego polecenia:

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* To polecenie można uruchomić na centralnym serwerze zasad sieciowych lub serwerze bramy usług pulpitu zdalnego.

![Przykładowe pomyślne zdarzenia logowania](./media/howto-mfa-nps-extension-rdg/image30.png)

Można również wyświetlić dziennik zabezpieczeń lub widok niestandardowy usług zasad sieciowych i dostępu sieciowego, jak pokazano poniżej:

![Usługi zasad sieciowych i dostępu Podgląd zdarzeń](./media/howto-mfa-nps-extension-rdg/image31.png)

Na serwerze, na którym zainstalowano rozszerzenie serwera NPS dla usługi Azure AD MFA, dzienniki aplikacji usługi Podgląd zdarzeń specyficzne dla tego rozszerzenia można znaleźć w tece Dzienniki aplikacji i _usług\Microsoft\AzureMfa._

![Podgląd zdarzeń aplikacji AuthZ](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Przewodnik rozwiązywania problemów

Jeśli konfiguracja nie działa zgodnie z oczekiwaniami, najpierw należy rozpocząć rozwiązywanie problemów, aby sprawdzić, czy użytkownik jest skonfigurowany do korzystania z usługi Azure AD MFA. Użytkownik powinien nawiązać połączenie z [Azure Portal](https://portal.azure.com). Jeśli użytkownikom zostanie wyświetlony monit o dodatkową weryfikację i będzie można pomyślnie uwierzytelnić się, możesz wyeliminować nieprawidłową konfigurację usługi Azure AD MFA.

Jeśli usługa Azure AD MFA działa dla użytkowników, należy przejrzeć odpowiednie dzienniki zdarzeń. Obejmują one dzienniki zdarzeń zabezpieczeń, działania bramy i usługi Azure AD MFA omówione w poprzedniej sekcji.

Poniżej przedstawiono przykładowe dane wyjściowe dziennika zabezpieczeń pokazujące zdarzenie logowania, które zakończyło się niepowodzeniem (identyfikator zdarzenia 6273).

![Przykład zdarzenia nieudanego logowania](./media/howto-mfa-nps-extension-rdg/image33.png)

Poniżej znajduje się powiązane zdarzenie z dzienników AzureMFA:

![Przykładowy dziennik usługi Azure AD MFA w usłudze Podgląd zdarzeń](./media/howto-mfa-nps-extension-rdg/image34.png)

Aby wykonać zaawansowane opcje rozwiązywania problemów, zapoznaj się z plikami dziennika formatu bazy danych serwera NPS, w których jest zainstalowana usługa serwera NPS. Te pliki dziennika są tworzone w _folderze %SystemRoot%\System32\Logs_ jako pliki tekstowe rozdzielane przecinkami.

Opis tych plików dziennika można znaleźć w temacie [Interpret NPS Database Format Log Files (Interpretowanie](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc771748(v=ws.10))plików dziennika formatu bazy danych serwera NPS). Wpisy w tych plikach dziennika mogą być trudne do zinterpretowania bez importowania ich do arkusza kalkulacyjnego lub bazy danych. W trybie online można znaleźć kilka parserów IAS, które pomagają w interpretowaniu plików dziennika.

Na poniższej ilustracji przedstawiono dane wyjściowe jednej z takich aplikacji shareware do [pobrania.](https://www.deepsoftware.com/iasviewer)

![Przykładowy parser IAS aplikacji Shareware](./media/howto-mfa-nps-extension-rdg/image35.png)

Na koniec, aby uzyskać dodatkowe opcje rozwiązywania problemów, można użyć analizatora protokołu, takiego [jak Microsoft Message Analyzer](/message-analyzer/microsoft-message-analyzer-operating-guide).

Poniższy obraz z Microsoft Message Analyzer pokazuje ruch sieciowy filtrowany według protokołu RADIUS, który zawiera nazwę użytkownika **CONTOSO\AliceC.**

![Analizator komunikatów firmy Microsoft przedstawiający przefiltrowany ruch](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Następne kroki

[Jak uzyskać usługę Azure AD Multi-Factor Authentication](concept-mfa-licensing.md)

[Brama usług pulpitu zdalnego i serwer Azure Multi-Factor Authentication korzystające z usługi RADIUS](howto-mfaserver-nps-rdg.md)

[Integrowanie katalogów lokalnych z usługą Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
