---
title: Wdróż lokalną ochronę hasłem usługi Azure AD
description: Dowiedz się, jak planować i wdrażać ochronę hasłem w usłudze Azure AD w środowisku lokalnym Active Directory Domain Services
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 03/05/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: b773fb887d3663a2af2e340912e378c7fccaba4a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89003545"
---
# <a name="plan-and-deploy-on-premises-azure-active-directory-password-protection"></a>Planowanie i wdrażanie lokalnej Azure Active Directory ochrony hasłem

Użytkownicy często tworzą hasła używające zwykłych słów lokalnych, takich jak szkoła, zespół sportowy lub osoba sławę. Hasła te są łatwe do odgadnięcia i są słabe przed atakami opartymi na słownikach. Aby wymusić silne hasła w organizacji, usługa Ochrona hasłem w usłudze Azure Active Directory (Azure AD) zapewnia globalną i niestandardową listę wykluczonych haseł. Żądanie zmiany hasła nie powiedzie się, jeśli na liście wykluczone hasła znajduje się dopasowanie.

Aby chronić lokalne środowisko Active Directory Domain Services (AD DS), możesz zainstalować i skonfigurować ochronę hasłem usługi Azure AD, aby współpracowały z Premium kontrolerem domeny. W tym artykule opisano sposób instalowania i rejestrowania usługi proxy ochrony haseł w usłudze Azure AD oraz agenta DC ochrony hasłem usługi Azure AD w środowisku lokalnym.

Aby uzyskać więcej informacji na temat sposobu działania ochrony haseł usługi Azure AD w środowisku lokalnym, zobacz [Jak wymusić ochronę hasłem usługi Azure AD dla systemu Windows Server Active Directory](concept-password-ban-bad-on-premises.md).

## <a name="deployment-strategy"></a>Strategia wdrażania

Na poniższym diagramie przedstawiono sposób, w jaki podstawowe składniki ochrony hasłem usługi Azure AD współpracują ze sobą w środowisku lokalnym Active Directory:

![Jak współpracują ze składnikami ochrony haseł usługi Azure AD](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Dobrym pomysłem jest sprawdzenie, jak działa oprogramowanie przed jego wdrożeniem. Aby uzyskać więcej informacji, zobacz [Omówienie pojęć związanych z ochroną hasłem usługi Azure AD](concept-password-ban-bad-on-premises.md).

Zalecamy rozpoczęcie wdrożeń w trybie *inspekcji* . Tryb inspekcji jest domyślnym ustawieniem początkowym, w którym można kontynuować ustawianie haseł. Hasła, które byłyby blokowane, są rejestrowane w dzienniku zdarzeń. Po wdrożeniu serwerów proxy i agentów kontrolera domeny w trybie inspekcji należy monitorować wpływ zasad haseł na użytkowników, gdy zasady zostaną wymuszone.

Na etapie inspekcji wiele organizacji stwierdzi, że mają zastosowanie następujące sytuacje:

* Muszą oni ulepszyć istniejące procesy operacyjne, aby używać bardziej bezpiecznych haseł.
* Użytkownicy często używają niezabezpieczonych haseł.
* Muszą oni informować użytkowników o nadchodzącej zmianie wymuszania zabezpieczeń, możliwym wpływie na nie i sposobach wybierania bezpiecznych haseł.

Istnieje również możliwość, że sprawdzanie poprawności hasła ma wpływ na istniejące Active Directory Automatyzacja wdrożenia kontrolera domeny. Zaleca się, aby podczas oceny okresu inspekcji, co najmniej jedna podwyższanie poziomu kontrolera domeny i jedno obniżenie kontrolera domeny było możliwe, aby pomóc w odwróceniu takich problemów. Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Ntdsutil.exe nie może ustawić słabego hasła trybu naprawy usług katalogowych](howto-password-ban-bad-on-premises-troubleshoot.md#ntdsutilexe-fails-to-set-a-weak-dsrm-password)
* [Podwyższanie poziomu repliki kontrolera domeny kończy się niepowodzeniem z powodu słabego hasła trybu naprawy usług katalogowych](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-replica-promotion-fails-because-of-a-weak-dsrm-password)
* [Obniżanie poziomu kontrolera domeny nie powiodło się z powodu słabego hasła administratora lokalnego](howto-password-ban-bad-on-premises-troubleshoot.md#domain-controller-demotion-fails-due-to-a-weak-local-administrator-password)

Po uruchomieniu funkcji w trybie inspekcji przez rozsądny okres można zmienić konfigurację z *inspekcji* w celu *wymuszenia* , aby wymagać bardziej bezpiecznych haseł. Dodatkowe monitorowanie w tym czasie jest dobrym pomysłem.

Należy pamiętać, że ochrona hasłem w usłudze Azure AD może tylko weryfikować hasła podczas operacji zmiany lub ustawiania hasła. Hasła, które zostały zaakceptowane i zapisane w Active Directory przed wdrożeniem ochrony hasłem usługi Azure AD, nigdy nie będą weryfikowane i będą nadal działać zgodnie z oczekiwaniami. Wraz z upływem czasu wszyscy użytkownicy i konta ostatecznie rozpoczną korzystanie z funkcji ochrony hasłem w usłudze Azure AD, ponieważ istniejące hasła wygasają normalnie. Konta skonfigurowane z opcją "hasło nigdy nie wygasa" są wykluczone z tej usługi.

### <a name="multiple-forest-considerations"></a>Zagadnienia dotyczące wielu lasów

Nie ma dodatkowych wymagań dotyczących wdrażania ochrony hasłem usługi Azure AD w wielu lasach.

Każdy Las jest skonfigurowany niezależnie, zgodnie z opisem w poniższej sekcji, aby [wdrożyć ochronę hasłem w usłudze Azure AD Premium](#download-required-software). Każdy serwer proxy ochrony haseł usługi Azure AD może obsługiwać tylko kontrolery domeny z lasu, do którego jest przyłączony.

Oprogramowanie ochrony hasłem usługi Azure AD w dowolnym lesie nie jest nieświadome oprogramowania ochrony hasła, które zostało wdrożone w innych lasach, niezależnie od konfiguracji zaufania Active Directory.

### <a name="read-only-domain-controller-considerations"></a>Zagadnienia dotyczące kontrolera domeny tylko do odczytu

Zdarzenia zmiany lub ustawienia hasła nie są przetwarzane i utrwalane na kontrolerach domeny tylko do odczytu (RODC). Zamiast tego są przekazywane do zapisywalnych kontrolerów domeny. Nie trzeba instalować oprogramowania agenta DC ochrony hasłem usługi Azure AD na kontrolerach RODC.

Ponadto nie jest obsługiwane uruchamianie usługi proxy ochrony hasłem w usłudze Azure AD na kontrolerze domeny tylko do odczytu.

### <a name="high-availability-considerations"></a>Zagadnienia dotyczące wysokiej dostępności

Główną troską o ochronę hasłem jest dostępność serwerów proxy ochrony hasłem usługi Azure AD, gdy kontrolery domen w lesie próbują pobrać nowe zasady lub inne dane z platformy Azure. Każdy agent DC ochrony hasłem usługi Azure AD używa prostego algorytmu okrężnej w stylu podczas wybierania serwera proxy do wywołania. Agent pomija serwery proxy, które nie odpowiadają.

W przypadku większości w pełni połączonych Active Directory wdrożeń, które mają dobrą replikację stanu katalogu i folderu SYSVOL, do zapewnienia dostępności wystarczą dwa serwery proxy ochrony hasłem usługi Azure AD. Ta konfiguracja powoduje czasowe pobieranie nowych zasad i innych danych. W razie potrzeby można wdrożyć dodatkowe serwery proxy ochrony hasłem usługi Azure AD.

Projektowanie oprogramowania agenta DC ochrony hasłem w usłudze Azure AD ogranicza typowe problemy, które są związane z wysoką dostępnością. Agent DC ochrony hasłem usługi Azure AD przechowuje lokalną pamięć podręczną ostatnio pobranych zasad haseł. Nawet jeśli wszystkie zarejestrowane serwery proxy staną się niedostępne, agenci DC ochrony hasłem usługi Azure AD kontynuują wymuszanie zasad haseł w pamięci podręcznej.

Rozsądna częstotliwość aktualizacji zasad haseł w dużych wdrożeniach to zwykle dni, nie więcej niż godz. W związku z tym krótkie przestoje serwerów proxy nie wpływają znacząco na ochronę hasłem usługi Azure AD.

## <a name="deployment-requirements"></a>Wymagania dotyczące wdrażania

Aby uzyskać informacje o licencjonowaniu, zobacz [wymagania licencyjne ochrony hasłem usługi Azure AD](concept-password-ban-bad.md#license-requirements).

Obowiązują następujące podstawowe wymagania:

* Wszystkie maszyny, w tym kontrolery domeny z zainstalowanymi składnikami ochrony haseł usługi Azure AD, muszą mieć zainstalowane środowisko uruchomieniowe języka uniwersalnego C.
    * Środowisko uruchomieniowe można uzyskać, upewniając się, że masz wszystkie aktualizacje Windows Update. Lub można ją pobrać w pakiecie aktualizacji specyficznym dla systemu operacyjnego. Aby uzyskać więcej informacji, zobacz [Aktualizacja dla środowiska uruchomieniowego uniwersalnego języka C w systemie Windows](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* Do zarejestrowania lasu Active Directory systemu Windows Server w usłudze Azure AD potrzebujesz konta z uprawnieniami administratora domeny Active Directory w domenie głównej lasu.
* Usługa dystrybucji kluczy musi być włączona na wszystkich kontrolerach domeny w domenie z systemem Windows Server 2012. Domyślnie ta usługa jest włączona za pośrednictwem uruchomienia wyzwalacza ręcznego.
* Połączenie sieciowe musi istnieć między co najmniej jednym kontrolerem domeny w każdej domenie i co najmniej jednym serwerem, który obsługuje usługę proxy dla ochrony hasłem usługi Azure AD. Ta łączność musi zezwalać kontrolerowi domeny na dostęp do portu mapowania punktów końcowych wywołań RPC 135 i portu serwera RPC w usłudze serwera proxy.
    * Domyślnie port serwera RPC to dynamiczny port RPC, ale można go skonfigurować do [korzystania z portu statycznego](#static).
* Wszystkie maszyny, na których zostanie zainstalowana usługa serwera proxy ochrony haseł usługi Azure AD, muszą mieć dostęp sieciowy do następujących punktów końcowych:

    |**Punkt końcowy**|**Cel**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Żądania uwierzytelniania|
    |`https://enterpriseregistration.windows.net`|Funkcja ochrony hasłem w usłudze Azure AD|

### <a name="azure-ad-password-protection-dc-agent"></a>Agent DC ochrony hasłem usługi Azure AD

Następujące wymagania dotyczą agenta DC ochrony hasłem usługi Azure AD:

* Na wszystkich maszynach, na których zostanie zainstalowane oprogramowanie agenta DC ochrony hasłem usługi Azure AD, musi działać system Windows Server 2012 lub nowszy.
    * Domena lub las Active Directory nie musi być na poziomie funkcjonalności domeny systemu Windows Server 2012 lub poziomu funkcjonalności lasu (FFL). Jak wspomniano w [zasadach projektowania](concept-password-ban-bad-on-premises.md#design-principles), nie ma minimalnych DFL lub FFL wymaganych do uruchomienia agenta lub oprogramowania serwera proxy.
* Na wszystkich maszynach z uruchomionym agentem usługi Azure AD Password Protection musi być zainstalowany program .NET 4,5.
* Wszystkie domeny Active Directory z uruchomioną usługą agenta DC ochrony hasłem usługi Azure AD muszą używać replikacji rozproszony system plików (DFSR) do replikacji folderu SYSVOL.
   * Jeśli domena nie korzysta już z usług DFSR, przed zainstalowaniem ochrony hasłem usługi Azure AD należy przeprowadzić migrację. Aby uzyskać więcej informacji, zobacz temat [Przewodnik migracji replikacji folderu SYSVOL: usługa FRS do replikacja systemu plików DFS](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd640019(v=ws.10))

    > [!WARNING]
    > Oprogramowanie agenta DC ochrony hasłem w usłudze Azure AD będzie obecnie instalowane na kontrolerach domeny w domenach, które nadal korzystają z usługi FRS (technologii poprzednika z DFSR) na potrzeby replikacji folderu SYSVOL, ale oprogramowanie nie będzie działało prawidłowo w tym środowisku.
    >
    > Dodatkowe negatywne efekty uboczne obejmują pojedyncze pliki, których replikacja nie powiodła się, a procedury przywracania folderu SYSVOL pojawiają się po awarii, ale w trybie dyskretnym nie można zreplikować wszystkich plików.
    >
    > Przeprowadź migrację domeny tak szybko, jak to możliwe, zarówno w przypadku związanych z nią korzyści, jak i Odblokuj wdrożenie ochrony hasłem usługi Azure AD. Przyszłe wersje oprogramowania zostaną automatycznie wyłączone po uruchomieniu w domenie, w której nadal jest używana usługa FRS.

### <a name="azure-ad-password-protection-proxy-service"></a>Usługa serwera proxy ochrony hasła usługi Azure AD

Poniższe wymagania dotyczą usługi proxy ochrony hasłem w usłudze Azure AD:

* Na wszystkich maszynach, na których zostanie zainstalowana usługa serwera proxy ochrony haseł usługi Azure AD, musi działać system Windows Server 2012 R2 lub nowszy.

    > [!NOTE]
    > Wdrożenie usługi serwera proxy ochrony hasłem w usłudze Azure AD jest wymaganym warunkiem wdrożenia ochrony hasłem usługi Azure AD, mimo że kontroler domeny może mieć bezpośrednią łączność z Internetem.

* Na wszystkich maszynach, na których zostanie zainstalowana usługa serwera proxy ochrony hasłem usługi Azure AD, musi być zainstalowany program .NET 4,7.
    * Środowisko .NET 4,7 powinno być już zainstalowane w w pełni zaktualizowanym systemie Windows Server. W razie potrzeby Pobierz i uruchom Instalatora, który znajduje [się w instalatorze offline .NET Framework 4,7 dla systemu Windows](https://support.microsoft.com/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* Wszystkie komputery, na których jest hostowana usługa serwera proxy ochrony haseł usługi Azure AD, muszą być skonfigurowane tak, aby zezwalać kontrolerom domeny na logowanie do usługi proxy. Ta możliwość jest kontrolowana przez przypisanie przywileju "uzyskaj dostęp do tego komputera z sieci".
* Wszystkie komputery, na których jest hostowana usługa serwera proxy ochrony haseł usługi Azure AD, muszą być skonfigurowane tak, aby zezwalały na ruch HTTP 1,2 wychodzący TLS
* Konto *administratora globalnego* do zarejestrowania usługi i lasu proxy ochrony haseł usługi Azure AD za pomocą usługi Azure AD.
* Dostęp do sieci musi być włączony dla zestawu portów i adresów URL określonych w [procedurach konfiguracji środowiska serwera proxy aplikacji](../manage-apps/application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).

### <a name="microsoft-azure-ad-connect-agent-updater-prerequisites"></a>Wymagania wstępne dotyczące Microsoft Azure AD Connect Agent Aktualizator

Usługa Aktualizator Connect Agent jest zainstalowana obok usługi serwera proxy ochrony hasłem w usłudze Azure AD. Microsoft Azure AD Aby usługa Microsoft Azure AD Connect Agent Aktualizator mogła działać, wymagana jest dodatkowa konfiguracja:

* Jeśli w środowisku używany jest serwer proxy HTTP, postępuj zgodnie z wytycznymi określonymi w [pracy z istniejącymi lokalnymi serwerami proxy](../manage-apps/application-proxy-configure-connectors-with-proxy-servers.md).
* Microsoft Azure AD Connect Agent Aktualizator wymaga również kroków TLS 1,2 określonych w [wymaganiach dotyczących protokołu TLS](../manage-apps/application-proxy-add-on-premises-application.md#tls-requirements).

> [!WARNING]
> Serwer proxy ochrony hasłem usługi Azure AD i usługa Azure serwer proxy aplikacji usługi Azure AD instalują różne wersje usługi Microsoft Azure AD Connect Agent Aktualizator, dlatego instrukcje odnoszą się do zawartości serwera proxy aplikacji. Te różne wersje są niezgodne, jeśli są zainstalowane obok siebie i w ten sposób uniemożliwią kontaktowanie się z platformą Azure w ramach aktualizacji oprogramowania przez agenta Aktualizator, dlatego nie należy instalować serwera proxy ochrony hasłem usługi Azure AD i serwera proxy aplikacji na tym samym komputerze.

## <a name="download-required-software"></a>Pobierz wymagane oprogramowanie

Istnieją dwa wymagane Instalatory dla lokalnego wdrożenia ochrony hasła usługi Azure AD:

* Agent DC ochrony hasłem usługi Azure AD (*AzureADPasswordProtectionDCAgentSetup.msi*)
* Serwer proxy ochrony hasłem usługi Azure AD (*AzureADPasswordProtectionProxySetup.exe*)

Pobierz oba Instalatory z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

## <a name="install-and-configure-the-proxy-service"></a>Instalowanie i Konfigurowanie usługi proxy

Usługa serwera proxy ochrony hasłem w usłudze Azure AD jest zazwyczaj na serwerze członkowskim w lokalnym środowisku AD DS. Po zainstalowaniu usługa serwera proxy ochrony hasłem w usłudze Azure AD komunikuje się z usługą Azure AD, aby zachować kopię list globalnych i zakazanych haseł klientów dla dzierżawy usługi Azure AD.

W następnej sekcji instalujesz agentów DC ochrony hasłem usługi Azure AD na kontrolerach domeny w środowisku lokalnym AD DS. Ci agenci DC komunikują się z usługą proxy, aby uzyskać najnowsze listy zakazanych haseł do użycia podczas przetwarzania zdarzeń zmiany hasła w domenie.

Wybierz co najmniej jeden serwer, na którym ma być Hostowana usługa serwera proxy ochrony hasła usługi Azure AD. Następujące zagadnienia dotyczą serwerów:

* Każda taka usługa może udostępniać tylko zasady haseł dla jednego lasu. Komputer hosta musi być przyłączony do domeny w tym lesie. Domeny główne i podrzędne są obsługiwane. Wymagana jest łączność sieciowa między co najmniej jednym KONTROLERem domeny w każdej domenie lasu i komputerem ochrony hasłem.
* Na kontrolerze domeny można uruchomić usługę proxy ochrony hasłem usługi Azure AD, ale ten kontroler domeny wymaga połączenia z Internetem. To połączenie może stanowić problem z zabezpieczeniami. Zalecamy tę konfigurację tylko do celów testowych.
* Zalecamy co najmniej dwa serwery proxy ochrony haseł usługi Azure AD, które zostały zanotowane w poprzedniej sekcji dotyczącej [zagadnień dotyczących wysokiej dostępności](#high-availability-considerations).
* Nie jest obsługiwane uruchamianie usługi proxy ochrony hasłem Azure AD na kontrolerze domeny tylko do odczytu.

Aby zainstalować usługę proxy ochrony hasłem w usłudze Azure AD, wykonaj następujące czynności:

1. Aby zainstalować usługę proxy ochrony hasłem usługi Azure AD, uruchom `AzureADPasswordProtectionProxySetup.exe` instalatora oprogramowania.

    Instalacja oprogramowania nie wymaga ponownego uruchomienia komputera i może być zautomatyzowany przy użyciu standardowych procedur MSI, jak w poniższym przykładzie:
    
    ```console
    AzureADPasswordProtectionProxySetup.exe /quiet
    ```
    
    > [!NOTE]
    > Przed zainstalowaniem pakietu musi być uruchomiona usługa Zapora systemu Windows, `AzureADPasswordProtectionProxySetup.exe` Aby uniknąć błędu instalacji.
    >
    > Jeśli Zapora systemu Windows jest skonfigurowana do uruchamiania, obejście to tymczasowe włączenie i uruchomienie usługi zapory podczas instalacji. Oprogramowanie serwera proxy nie ma określonej zależności od zapory systemu Windows po zakończeniu instalacji.
    >
    > Jeśli używasz zapory innej firmy, musi ona być skonfigurowana w celu spełnienia wymagań dotyczących wdrożenia. Obejmują one między innymi Zezwalanie na dostęp przychodzący do portu 135 i portu serwera proxy RPC. Aby uzyskać więcej informacji, zobacz poprzednią sekcję dotyczącą [wymagań dotyczących wdrożenia](#deployment-requirements).

1. Oprogramowanie serwera proxy ochrony hasłem usługi Azure AD zawiera nowy moduł programu PowerShell `AzureADPasswordProtection` . Poniższe kroki uruchamiają różne polecenia cmdlet z tego modułu programu PowerShell.

    Aby użyć tego modułu, Otwórz okno programu PowerShell jako administrator i zaimportuj nowy moduł w następujący sposób:
    
    ```powershell
    Import-Module AzureADPasswordProtection
    ```

1. Aby sprawdzić, czy usługa serwera proxy ochrony hasłem w usłudze Azure AD jest uruchomiona, użyj następującego polecenia programu PowerShell:

    ```powershell
    Get-Service AzureADPasswordProtectionProxy | fl
    ```

    Wynik powinien zawierać **stan** *uruchomiony*.

1. Usługa serwera proxy jest uruchomiona na komputerze, ale nie ma poświadczeń do komunikowania się z usługą Azure AD. Zarejestruj serwer proxy ochrony hasłem usługi Azure AD za pomocą usługi Azure AD przy użyciu `Register-AzureADPasswordProtectionProxy` polecenia cmdlet.

    To polecenie cmdlet wymaga poświadczeń administratora globalnego dla dzierżawy platformy Azure. Wymagane są również lokalne Active Directory uprawnienia administratora domeny w domenie głównej lasu. To polecenie cmdlet należy również uruchomić przy użyciu konta z uprawnieniami administratora lokalnego:

    Po pomyślnym wykonaniu tego polecenia dla usługi serwera proxy ochrony hasłem w usłudze Azure AD zostaną pomyślnie wykonane dodatkowe wywołania, ale nie są one potrzebne.

    `Register-AzureADPasswordProtectionProxy`Polecenie cmdlet obsługuje następujące trzy tryby uwierzytelniania. Pierwsze dwa tryby obsługują platformę Azure Multi-Factor Authentication, ale trzeci tryb nie jest.

    > [!TIP]
    > Podczas pierwszego uruchomienia tego polecenia cmdlet dla określonej dzierżawy platformy Azure może wystąpić zauważalne opóźnienie. O ile nie zgłoszono błędu, nie martw się o to opóźnienie.

     * Tryb uwierzytelniania interaktywnego:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Ten tryb nie działa w systemach operacyjnych Server Core. Zamiast tego należy użyć jednego z następujących trybów uwierzytelniania. Ponadto ten tryb może zakończyć się niepowodzeniem, jeśli konfiguracja zwiększonych zabezpieczeń programu Internet Explorer jest włączona. Obejście polega na wyłączeniu tej konfiguracji, zarejestrowaniu serwera proxy, a następnie jego ponownym włączeniu.

     * Tryb uwierzytelniania kodu urządzenia:

        ```powershell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Po wyświetleniu monitu, aby otworzyć przeglądarkę internetową i wprowadzić kod uwierzytelniania.

     * Tryb uwierzytelniania dyskretnego (opartego na hasłach):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Ten tryb kończy się niepowodzeniem, jeśli usługa Azure Multi-Factor Authentication jest wymagana dla Twojego konta. W takim przypadku należy użyć jednego z dwóch poprzednich trybów uwierzytelniania lub zamiast tego użyć innego konta, które nie wymaga uwierzytelniania MFA.
        >
        > Możesz również sprawdzić, czy uwierzytelnianie wieloskładnikowe jest wymagane, jeśli usługa Azure Device Registration (która jest używana w ramach okładki przez usługę Azure AD Password Protection) została skonfigurowana w taki sposób, aby globalnie wymagała uwierzytelniania wieloskładnikowego. Aby obejść to wymaganie, można użyć innego konta, które obsługuje uwierzytelnianie wieloskładnikowe z jednym z poprzednich dwóch trybów uwierzytelniania lub można również tymczasowo osłabić wymaganie usługi MFA rejestracji urządzeń Azure.
        >
        > Aby wprowadzić tę zmianę, Wyszukaj i wybierz **Azure Active Directory** w Azure Portal, a następnie wybierz pozycję **urządzenia > ustawienia urządzenia**. Ustaw **Wymagaj uwierzytelniania wieloskładnikowego, aby dołączyć urządzenia** do *nie*. Należy koniecznie zmienić konfigurację tego ustawienia z powrotem na *wartość tak* po zakończeniu rejestracji.
        >
        > Zalecamy, aby wymagania usługi MFA były pomijane wyłącznie w celach testowych.

    Obecnie nie musisz określać parametru *-ForestCredential* , który jest zarezerwowany dla przyszłych funkcji.

    Rejestracja usługi proxy ochrony hasłem w usłudze Azure AD jest niezbędna tylko raz w okresie istnienia usługi. Po wykonaniu tej czynności usługa serwera proxy ochrony hasłem w usłudze Azure AD automatycznie przeprowadzi wszelkie inne niezbędne konserwacje.

1. Teraz Zarejestruj Las Active Directory lokalnego z poświadczeniami niezbędnymi do komunikowania się z platformą Azure przy użyciu `Register-AzureADPasswordProtectionForest` polecenia cmdlet programu PowerShell.

    > [!NOTE]
    > Jeśli w środowisku zainstalowano wiele serwerów proxy ochrony haseł usługi Azure AD, nie ma znaczenia, który serwer proxy służy do zarejestrowania lasu.

    Polecenie cmdlet wymaga poświadczeń administratora globalnego dla dzierżawy platformy Azure. To polecenie cmdlet należy również uruchomić przy użyciu konta z uprawnieniami administratora lokalnego. Wymaga również lokalnego Active Directory uprawnień administratora przedsiębiorstwa. Ten krok jest uruchamiany raz na las.

    `Register-AzureADPasswordProtectionForest`Polecenie cmdlet obsługuje następujące trzy tryby uwierzytelniania. Pierwsze dwa tryby obsługują platformę Azure Multi-Factor Authentication, ale trzeci tryb nie jest.

    > [!TIP]
    > Podczas pierwszego uruchomienia tego polecenia cmdlet dla określonej dzierżawy platformy Azure może wystąpić zauważalne opóźnienie. O ile nie zgłoszono błędu, nie martw się o to opóźnienie.

     * Tryb uwierzytelniania interaktywnego:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```

        > [!NOTE]
        > Ten tryb nie będzie działać w systemach operacyjnych Server Core. Zamiast tego należy użyć jednego z następujących dwóch trybów uwierzytelniania. Ponadto ten tryb może zakończyć się niepowodzeniem, jeśli konfiguracja zwiększonych zabezpieczeń programu Internet Explorer jest włączona. Obejście polega na wyłączeniu tej konfiguracji, zarejestrowaniu lasu, a następnie jego ponownym włączeniu.  

     * Tryb uwierzytelniania kodu urządzenia:

        ```powershell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        ```

        Po wyświetleniu monitu, aby otworzyć przeglądarkę internetową i wprowadzić kod uwierzytelniania.

     * Tryb uwierzytelniania dyskretnego (opartego na hasłach):

        ```powershell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Ten tryb kończy się niepowodzeniem, jeśli usługa Azure Multi-Factor Authentication jest wymagana dla Twojego konta. W takim przypadku należy użyć jednego z dwóch poprzednich trybów uwierzytelniania lub zamiast tego użyć innego konta, które nie wymaga uwierzytelniania MFA.
        >
        > Możesz również sprawdzić, czy uwierzytelnianie wieloskładnikowe jest wymagane, jeśli usługa Azure Device Registration (która jest używana w ramach okładki przez usługę Azure AD Password Protection) została skonfigurowana w taki sposób, aby globalnie wymagała uwierzytelniania wieloskładnikowego. Aby obejść to wymaganie, można użyć innego konta, które obsługuje uwierzytelnianie wieloskładnikowe z jednym z poprzednich dwóch trybów uwierzytelniania lub można również tymczasowo osłabić wymaganie usługi MFA rejestracji urządzeń Azure.
        >
        > Aby wprowadzić tę zmianę, Wyszukaj i wybierz **Azure Active Directory** w Azure Portal, a następnie wybierz pozycję **urządzenia > ustawienia urządzenia**. Ustaw **Wymagaj uwierzytelniania wieloskładnikowego, aby dołączyć urządzenia** do *nie*. Należy koniecznie zmienić konfigurację tego ustawienia z powrotem na *wartość tak* po zakończeniu rejestracji.
        >
        > Zalecamy, aby wymagania usługi MFA były pomijane wyłącznie w celach testowych.

       Te przykłady zakończyły się powodzeniem tylko wtedy, gdy obecnie zalogowany użytkownik jest również Active Directory administratorem domeny dla domeny katalogu głównego. Jeśli tak nie jest, możesz podać alternatywne poświadczenia domeny za pomocą parametru *-ForestCredential* .

    Rejestracja lasu Active Directory jest konieczna tylko raz w okresie istnienia lasu. Po wykonaniu tej czynności Agenty usługi Azure AD Password Protection w lesie automatycznie wykonują wszelkie inne niezbędne konserwacje. Po `Register-AzureADPasswordProtectionForest` pomyślnym uruchomieniu dla lasu, dodatkowe wywołania polecenia cmdlet powiodą się, ale nie są potrzebne.
    
    W `Register-AzureADPasswordProtectionForest` celu pomyślnego uruchomienia co najmniej jednego kontrolera domeny z systemem Windows Server 2012 lub nowszym musi być dostępny w domenie serwera proxy ochrony hasłem usługi Azure AD. Przed wykonaniem tego kroku nie trzeba instalować oprogramowania agenta DC ochrony hasłem usługi Azure AD na żadnym z kontrolerów domeny.

### <a name="configure-the-proxy-service-to-communicate-through-an-http-proxy"></a>Konfigurowanie usługi proxy do komunikacji za pośrednictwem serwera proxy HTTP

Jeśli środowisko wymaga użycia określonego serwera proxy HTTP do komunikowania się z platformą Azure, wykonaj następujące kroki, aby skonfigurować usługę ochrony hasłem w usłudze Azure AD.

Utwórz plik *AzureADPasswordProtectionProxy.exe.config* w `%ProgramFiles%\Azure AD Password Protection Proxy\Service` folderze. Uwzględnij następującą zawartość:

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

Jeśli serwer proxy HTTP wymaga uwierzytelniania, Dodaj tag *UseDefaultCredentials* :

   ```xml
   <configuration>
      <system.net>
         <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy bypassonlocal="true"
            proxyaddress="http://yourhttpproxy.com:8080" />
         </defaultProxy>
      </system.net>
   </configuration>
   ```

W obu przypadkach Zastąp ciąg `http://yourhttpproxy.com:8080` adresem i portem określonego serwera proxy HTTP.

Jeśli serwer proxy HTTP jest skonfigurowany do korzystania z zasad autoryzacji, należy udzielić dostępu do konta komputera Active Directory maszyny, która obsługuje usługę serwera proxy na potrzeby ochrony hasłem.

Zalecamy zatrzymanie i ponowne uruchomienie usługi proxy ochrony hasłem Azure AD po utworzeniu lub zaktualizowaniu pliku *AzureADPasswordProtectionProxy.exe.config* .

Usługa serwera proxy nie obsługuje korzystania z określonych poświadczeń w celu nawiązania połączenia z serwerem proxy HTTP.

### <a name="configure-the-proxy-service-to-listen-on-a-specific-port"></a>Konfigurowanie usługi serwera proxy do nasłuchiwania na określonym porcie

Oprogramowanie agenta DC ochrony hasłem w usłudze Azure AD używa protokołu RPC przez TCP do komunikowania się z usługą proxy. Domyślnie usługa serwera proxy ochrony hasłem w usłudze Azure AD nasłuchuje na dowolnym dostępnym dynamicznym punkcie końcowym RPC. Usługę można skonfigurować do nasłuchiwania na konkretnym porcie TCP, jeśli jest to konieczne ze względu na topologię sieci lub wymagania dotyczące zapory w danym środowisku.

<a id="static" /></a>Aby skonfigurować usługę do uruchamiania w ramach portu statycznego, należy użyć `Set-AzureADPasswordProtectionProxyConfiguration` polecenia cmdlet w następujący sposób:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
```

> [!WARNING]
> Aby te zmiany zaczęły obowiązywać, należy zatrzymać i ponownie uruchomić usługę proxy ochrony hasłem usługi Azure AD.

Aby skonfigurować usługę do uruchamiania w ramach portu dynamicznego, Użyj tej samej procedury, ale ustaw *StaticPort* z powrotem na zero:

```powershell
Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
```

> [!WARNING]
> Aby te zmiany zaczęły obowiązywać, należy zatrzymać i ponownie uruchomić usługę proxy ochrony hasłem usługi Azure AD.

Usługa serwera proxy ochrony hasłem usługi Azure AD wymaga ręcznego ponownego uruchomienia po zmianie konfiguracji portu. Po wprowadzeniu tych zmian w konfiguracji nie trzeba ponownie uruchamiać usługi agenta DC ochrony hasłem w usłudze Azure AD na kontrolerach domeny.

Aby wykonać zapytanie o bieżącą konfigurację usługi, należy użyć `Get-AzureADPasswordProtectionProxyConfiguration` polecenia cmdlet, jak pokazano w poniższym przykładzie.

```powershell
Get-AzureADPasswordProtectionProxyConfiguration | fl
```

Następujące przykładowe dane wyjściowe pokazują, że usługa serwera proxy ochrony hasłem usługi Azure AD korzysta z portu dynamicznego:

```output
ServiceName : AzureADPasswordProtectionProxy
DisplayName : Azure AD password protection Proxy
StaticPort  : 0
```

## <a name="install-the-dc-agent-service"></a>Instalowanie usługi agenta DC

Aby zainstalować usługę agenta DC ochrony hasłem w usłudze Azure AD, uruchom `AzureADPasswordProtectionDCAgentSetup.msi` pakiet.

Instalację oprogramowania można zautomatyzować za pomocą standardowych procedur MSI, jak pokazano w następującym przykładzie:

```console
msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart
```

`/norestart`Flagę można pominąć, jeśli wolisz, aby Instalator automatycznie ponownie uruchomił maszynę.

Instalacja oprogramowania lub Dezinstalacja programu wymaga ponownego uruchomienia. To wymaganie wynika z faktu, że biblioteki DLL filtru haseł są ładowane lub zwalniane po ponownym uruchomieniu.

Instalacja usługi Premium Azure AD Password Protection została zakończona po zainstalowaniu oprogramowania Agent DC na kontrolerze domeny i ponownym uruchomieniu komputera. Nie jest wymagana żadna inna konfiguracja. Zdarzenia zmiany hasła dotyczące Premium kontrolerów domen używają skonfigurowanych list zakazanych haseł z usługi Azure AD.

Aby włączyć ochronę hasłem Premium usługi Azure AD z poziomu Azure Portal lub skonfigurować niestandardowe hasła zabronione, zobacz [Włączanie ochrony lokalnego hasła usługi Azure AD](howto-password-ban-bad-on-premises-operations.md).

> [!TIP]
> Agenta usługi Azure AD Password Protection można zainstalować na komputerze, który nie jest jeszcze kontrolerem domeny. W takim przypadku usługa zostanie uruchomiona i uruchomiona, ale pozostanie nieaktywna, dopóki komputer nie zostanie podwyższony do poziomu kontrolera domeny.

## <a name="upgrading-the-proxy-service"></a>Uaktualnianie usługi proxy

Usługa serwera proxy ochrony hasłem usługi Azure AD obsługuje automatyczne uaktualnianie. Automatyczne uaktualnianie używa usługi Microsoft Azure AD Connect Agent Aktualizator, która jest instalowana obok usługi proxy. Automatyczne uaktualnianie jest domyślnie włączone i może być włączane lub wyłączane przy użyciu `Set-AzureADPasswordProtectionProxyConfiguration` polecenia cmdlet.

Bieżące ustawienie można zbadać przy użyciu `Get-AzureADPasswordProtectionProxyConfiguration` polecenia cmdlet. Zalecamy włączenie opcji automatycznego uaktualniania.

`Get-AzureADPasswordProtectionProxy`Polecenie cmdlet może służyć do wykonywania zapytań dotyczących wersji oprogramowania wszystkich aktualnie zainstalowanych serwerów proxy ochrony haseł usługi Azure AD w lesie.

### <a name="manual-upgrade-process"></a>Proces uaktualniania ręcznego

Ręczne uaktualnienie jest realizowane przez uruchomienie najnowszej wersji `AzureADPasswordProtectionProxySetup.exe` instalatora oprogramowania. Najnowsza wersja oprogramowania jest dostępna w [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

Nie jest wymagane odinstalowanie bieżącej wersji usługi serwera proxy ochrony haseł usługi Azure AD — Instalator wykonuje uaktualnienie w miejscu. Podczas uaktualniania usługi proxy nie powinno być wymagane ponowne uruchomienie komputera. Uaktualnienie oprogramowania może być zautomatyzowane przy użyciu standardowych procedur MSI, takich jak `AzureADPasswordProtectionProxySetup.exe /quiet` .

## <a name="upgrading-the-dc-agent"></a>Uaktualnianie agenta DC

Gdy jest dostępna nowsza wersja oprogramowania agenta DC ochrony hasłem w usłudze Azure AD, uaktualnienie jest wykonywane przez uruchomienie najnowszej wersji `AzureADPasswordProtectionDCAgentSetup.msi` pakietu oprogramowania. Najnowsza wersja oprogramowania jest dostępna w [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=57071).

Nie jest wymagane, aby odinstalować bieżącą wersję oprogramowania agenta kontrolera domeny — Instalator wykonuje uaktualnienie w miejscu. Podczas uaktualniania oprogramowania agenta kontrolera domeny jest zawsze wymagane ponowne uruchomienie komputera — to wymaganie jest spowodowane przez podstawowe zachowanie systemu Windows.

Uaktualnienie oprogramowania może być zautomatyzowane przy użyciu standardowych procedur MSI, takich jak `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn /norestart` .

Możesz pominąć `/norestart` flagę, jeśli wolisz, aby Instalator automatycznie ponownie uruchomił maszynę.

`Get-AzureADPasswordProtectionDCAgent`Polecenie cmdlet może służyć do wykonywania zapytań dotyczących wersji oprogramowania wszystkich aktualnie zainstalowanych agentów usługi Azure AD Password Protection w lesie.

## <a name="next-steps"></a>Następne kroki

Po zainstalowaniu usług potrzebnych do ochrony hasłem usługi Azure AD na serwerach lokalnych [Włącz ochronę hasłem Premium usługi Azure AD w Azure Portal](howto-password-ban-bad-on-premises-operations.md) , aby ukończyć wdrożenie.