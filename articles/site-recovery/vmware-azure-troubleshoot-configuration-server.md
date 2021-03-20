---
title: Rozwiązywanie problemów z serwerem konfiguracji podczas odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformie Azure przy użyciu Azure Site Recovery | Microsoft Docs
description: Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z wdrażaniem serwera konfiguracji na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformie Azure przy użyciu Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2019
ms.author: ramamill
ms.openlocfilehash: b5fd014732fd4cdfaa52f971b5e4d2c74db580d2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92371957"
---
# <a name="troubleshoot-configuration-server-issues"></a>Rozwiązywanie problemów z serwerem konfiguracji

Ten artykuł pomaga w rozwiązywaniu problemów podczas wdrażania serwera konfiguracji [Azure Site Recovery](site-recovery-overview.md) i zarządzania nim. Serwer konfiguracji działa jako serwer zarządzania. Użyj serwera konfiguracji, aby skonfigurować odzyskiwanie po awarii lokalnych maszyn wirtualnych VMware i serwerów fizycznych na platformie Azure przy użyciu Site Recovery. W poniższych sekcjach omówiono najczęstsze błędy, które mogą wystąpić podczas dodawania nowego serwera konfiguracji i zarządzania serwerem konfiguracji.

## <a name="registration-failures"></a>Błędy rejestracji

Maszyna źródłowa rejestruje się na serwerze konfiguracji podczas instalacji agenta mobilności. Błędy można debugować w tym kroku, wykonując następujące wytyczne:

1. Otwórz plik C:\ProgramData\ASR\home\svsystems\var\ configurator_register_host_static_info. log. (Folder ProgramData może być folderem ukrytym. Jeśli nie widzisz folderu ProgramData, w Eksploratorze plików na karcie **Widok** , w sekcji **Pokaż/Ukryj** , zaznacz pole wyboru **elementy ukryte** . Awarie mogą być spowodowane przez wiele problemów.

2. Wyszukaj ciąg **nie znaleziono poprawnego adresu IP**. Jeśli ciąg zostanie znaleziony:
   1. Sprawdź, czy żądany identyfikator hosta jest taki sam jak identyfikator hosta maszyny źródłowej.
   2. Sprawdź, czy maszyna źródłowa ma co najmniej jeden adres IP przypisany do fizycznej karty sieciowej. Aby rejestracja agenta z serwerem konfiguracji zakończyła się powodzeniem, maszyna źródłowa musi mieć co najmniej jeden prawidłowy adres IP v4 przypisany do fizycznej karty sieciowej.
   3. Uruchom jedno z następujących poleceń na maszynie źródłowej, aby pobrać wszystkie adresy IP maszyny źródłowej:
      - Dla systemu Windows: `> ipconfig /all`
      - Dla systemu Linux: `# ifconfig -a`

3. Jeśli nie znaleziono **poprawnego adresu IP** , Wyszukaj **przyczynę ciągu =>wartość null**. Ten błąd występuje, gdy maszyna źródłowa używa pustego hosta do zarejestrowania na serwerze konfiguracji. Jeśli ciąg zostanie znaleziony:
    - Po rozwiązaniu problemów postępuj zgodnie z instrukcjami w temacie [Rejestrowanie maszyny źródłowej na serwerze konfiguracji](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) , aby ponowić próbę rejestracji ręcznie.

4. Jeśli na maszynie źródłowej nie znaleziono przyczyny ciągu, **>wartość null** , Otwórz plik C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log. (Folder ProgramData może być folderem ukrytym. Jeśli nie widzisz folderu ProgramData, w Eksploratorze plików na karcie **Widok** , w sekcji **Pokaż/Ukryj** , zaznacz pole wyboru **elementy ukryte** . Awarie mogą być spowodowane przez wiele problemów. 

5. Wyszukaj **żądanie post ciągu: (7) — nie można nawiązać połączenia z serwerem**. Jeśli ciąg zostanie znaleziony:
    1. Rozwiąż problemy z siecią między maszyną źródłową a serwerem konfiguracji. Sprawdź, czy serwer konfiguracji jest dostępny z maszyny źródłowej przy użyciu narzędzi sieciowych, takich jak ping, traceroute lub przeglądarka sieci Web. Upewnij się, że maszyna źródłowa może nawiązać połączenie z serwerem konfiguracji za pomocą portu 443.
    2. Sprawdź, czy wszystkie reguły zapory na maszynie źródłowej blokują połączenie między maszyną źródłową a serwerem konfiguracji. Skontaktuj się z administratorami sieci, aby odblokować problemy z połączeniem.
    3. Upewnij się, że foldery wymienione w [Site Recovery wykluczenia folderów z programów antywirusowych](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) są wykluczone z oprogramowania antywirusowego.
    4. Po rozwiązaniu problemów z siecią ponów próbę rejestracji, postępując zgodnie ze wskazówkami w temacie [Rejestrowanie maszyny źródłowej na serwerze konfiguracji](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

6. Jeśli **żądanie post ciągu: (7) — nie można nawiązać połączenia z serwerem** , w tym samym pliku dziennika poszukaj **żądania ciągu: (60) — certyfikatu równorzędnego nie można uwierzytelnić przy użyciu określonych certyfikatów urzędu certyfikacji**. Ten błąd może wystąpić, ponieważ certyfikat serwera konfiguracji wygasł lub maszyna źródłowa nie obsługuje protokołów TLS 1,0 i nowszych. Może również wystąpić, Jeśli zapora blokuje komunikację TLS między maszyną źródłową a serwerem konfiguracji. Jeśli ciąg zostanie znaleziony: 
    1. Aby rozwiązać ten problem, Połącz się z adresem IP serwera konfiguracji przy użyciu przeglądarki sieci Web na maszynie źródłowej. Użyj identyfikatora URI https: \/ \/ adres IP serwera konfiguracji<\> : 443/. Upewnij się, że maszyna źródłowa może nawiązać połączenie z serwerem konfiguracji za pomocą portu 443.
    2. Sprawdź, czy wszystkie reguły zapory na maszynie źródłowej muszą być dodane lub usunięte, aby maszyna źródłowa mogła komunikować się z serwerem konfiguracji. Ze względu na rozmaite oprogramowanie zapory, które może być używane, nie można wyświetlić wszystkich wymaganych konfiguracji zapory. Skontaktuj się z administratorami sieci, aby odblokować problemy z połączeniem.
    3. Upewnij się, że foldery wymienione w [Site Recovery wykluczenia folderów z programów antywirusowych](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) są wykluczone z oprogramowania antywirusowego.  
    4. Po rozwiązaniu problemów ponów próbę rejestracji, postępując zgodnie z instrukcjami w temacie [Rejestrowanie maszyny źródłowej na serwerze konfiguracji](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

7. W systemie Linux jeśli wartość platformy w <INSTALLATION_DIR \> /etc/drscout.conf jest uszkodzona, rejestracja kończy się niepowodzeniem. Aby zidentyfikować ten problem, Otwórz plik/var/log/ua_install. log. Wyszukaj ciąg **przerwania konfiguracji, ponieważ VM_PLATFORM wartość jest równa null lub nie jest VMware/Azure**. Platforma powinna być ustawiona na platformę **VMware** lub **platformę Azure**. Jeśli plik drscout. conf jest uszkodzony, zalecamy [odinstalowanie agenta mobilności](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) , a następnie ponowne zainstalowanie agenta mobilności. Jeśli Dezinstalacja nie powiedzie się, wykonaj następujące czynności: a. Otwórz plik Installation_Directory/Uninstall.sh i Dodaj komentarz do wywołania funkcji **StopServices** .
    b. Otwórz plik Installation_Directory/VX/bin/Uninstall.sh i Dodaj komentarz do wywołania funkcji **stop_services** .
    c. Otwórz plik Installation_Directory/FX/Uninstall.sh i Dodaj komentarz do całej sekcji, która próbuje zatrzymać usługę FX.
    d. [Odinstaluj](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) agenta mobilności. Po pomyślnym odinstalowaniu systemu ponownie uruchom system, a następnie spróbuj ponownie zainstalować agenta mobilności.

8. Upewnij się, że uwierzytelnianie wieloskładnikowe nie jest włączone dla konta użytkownika. Azure Site Recovery nie obsługuje uwierzytelniania wieloskładnikowego dla konta użytkownika. Zarejestruj serwer konfiguracji bez konta użytkownika z włączonym uwierzytelnianiem wieloskładnikowym.  

## <a name="installation-failure-failed-to-load-accounts"></a>Niepowodzenie instalacji: nie można załadować kont

Ten błąd występuje, gdy usługa nie może odczytać danych z połączenia transportowego, gdy instaluje agenta mobilności i rejestruje się na serwerze konfiguracji. Aby rozwiązać ten problem, upewnij się, że na maszynie źródłowej jest włączony protokół TLS 1,0.

## <a name="vcenter-discovery-failures"></a>Błędy odnajdowania vCenter

Aby rozwiązać problemy z odnajdywaniem programu vCenter, Dodaj serwer vCenter do ustawień serwera proxy listy pomijania. 

- Pobierz narzędzie PsExec z tego [miejsca](/sysinternals/downloads/psexec) , aby uzyskać dostęp do zawartości użytkownika systemu.
- Otwórz program Internet Explorer w zawartości użytkownika systemu, uruchamiając następujący wiersz polecenia PsExec-s-i "użycie ścieżki%ProgramFiles%\Internet Explorer\iexplore.exe"
- Dodaj ustawienia serwera proxy w programie IE i uruchom ponownie usługę tmanssvc.
- Aby skonfigurować ustawienia serwera proxy DRA, uruchom polecenie CD C:\Program Files\Microsoft Azure Site Recovery Provider
- Następnie wykonaj DRCONFIGURATOR.EXE/Configure/AddBypassUrls [Dodaj adres IP/nazwę FQDN vCenter Server podanych podczas **konfigurowania vCenter Server/VSphere ESXi serwera** [konfiguracji](vmware-azure-deploy-configuration-server.md#configure-settings)]

## <a name="change-the-ip-address-of-the-configuration-server"></a>Zmień adres IP serwera konfiguracji

Zdecydowanie zalecamy, aby nie zmieniać adresu IP serwera konfiguracji. Upewnij się, że wszystkie adresy IP przypisane do serwera konfiguracji to statyczne adresy IP. Nie używaj adresów IP DHCP.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: token SAML jest nieprawidłowy

Aby uniknąć tego błędu, należy się upewnić, że czas zegara systemowego nie różni się od czasu lokalnego o więcej niż 15 minut. Uruchom ponownie instalatora, aby ukończyć rejestrację.

## <a name="failed-to-create-a-certificate"></a>Nie można utworzyć certyfikatu

Nie można utworzyć certyfikatu wymaganego do uwierzytelnienia Site Recovery. Uruchom ponownie Instalatora po upewnieniu się, że uruchamiasz Instalatora jako administrator lokalny.

## <a name="failure-to-activate-windows-license-from-server-standard-evaluation-to-server-standard"></a>Niepowodzenie aktywowania licencji systemu Windows na podstawie standardowej oceny serwera do wersji Standard serwera

1. W ramach wdrożenia serwera konfiguracji za poorednictwem OVF jest używana licencja ewaluacyjna, która jest ważna przez 180 dni. Tę licencję należy aktywować przed wygaśnięciem. W przeciwnym razie może to spowodować częste zamknięcie serwera konfiguracji i w efekcie przeszkodę na działania replikacji.
2. Jeśli nie możesz aktywować licencji systemu Windows, skontaktuj się z [zespołem pomocy technicznej systemu Windows](https://aka.ms/Windows_Support) , aby rozwiązać ten problem.

## <a name="register-source-machine-with-configuration-server"></a>Zarejestruj maszynę źródłową z serwerem konfiguracji

### <a name="if-the-source-machine-runs-windows"></a>Jeśli na maszynie źródłowej jest uruchomiony system Windows

Uruchom następujące polecenie na maszynie źródłowej:

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
```

Ustawienie | Szczegóły
--- | ---
Użycie | UnifiedAgentConfigurator.exe/CSEndPoint <adres IP serwera konfiguracji \> /PassphraseFilePath <hasło pliku\>
Dzienniki konfiguracji agenta | Znajduje się w obszarze%ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Obowiązkowy parametr. Określa adres IP serwera konfiguracji. Użyj dowolnych prawidłowych adresów IP.
/PassphraseFilePath |  Obowiązkowy. Lokalizacja hasła. Użyj dowolnej prawidłowej ścieżki UNC lub pliku lokalnego.

### <a name="if-the-source-machine-runs-linux"></a>Jeśli na maszynie źródłowej jest uruchomiony system Linux

Uruchom następujące polecenie na maszynie źródłowej:

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

Ustawienie | Szczegóły
--- | ---
Użycie | /usr/local/ASR/Vx/bin CD<br /><br /> UnifiedAgentConfigurator.sh-i <adres IP serwera konfiguracji \> — P <ścieżka pliku hasła\>
-i | Obowiązkowy parametr. Określa adres IP serwera konfiguracji. Użyj dowolnych prawidłowych adresów IP.
-P |  Obowiązkowy. Pełna ścieżka pliku, w którym zapisano hasło. Użyj dowolnego prawidłowego folderu.

## <a name="unable-to-configure-the-configuration-server"></a>Nie można skonfigurować serwera konfiguracji

W przypadku instalowania aplikacji innych niż serwer konfiguracji na maszynie wirtualnej może nie być możliwe skonfigurowanie głównego celu. 

Serwer konfiguracji musi być serwerem o pojedynczym przeznaczeniu i korzystać z niego jako serwer współużytkowany jest nieobsługiwany. 

Aby uzyskać więcej informacji, zobacz często zadawane pytania dotyczące konfiguracji na stronie [Wdrażanie serwera konfiguracji](vmware-azure-deploy-configuration-server.md#faqs). 

## <a name="remove-the-stale-entries-for-protected-items-from-the-configuration-server-database"></a>Usuń stare wpisy dla chronionych elementów z bazy danych serwera konfiguracji 

Aby usunąć przestarzałą chronioną maszynę na serwerze konfiguracji, wykonaj następujące czynności. 
 
1. Aby określić maszynę źródłową i adres IP nieodświeżonego wpisu: 

    1. Otwórz plik Cmdlines programu MYSQL w trybie administratora. 
    2. Wykonaj następujące polecenia. 
   
        ```
        mysql> use svsdb1;
        mysql> select id as hostid, name, ipaddress, ostype as operatingsystem, from_unixtime(lasthostupdatetime) as heartbeat from hosts where name!='InMageProfiler'\G;
        ```

        Spowoduje to zwrócenie listy zarejestrowanych maszyn wraz z ich adresami IP i ostatnim pulsem. Znajdź hosta, który ma nieodświeżone pary replikacji.

2. Otwórz wiersz polecenia z podwyższonym poziomem uprawnień i przejdź do C:\ProgramData\ASR\home\svsystems\bin. 
4. Aby usunąć szczegóły zarejestrowanych hostów i informacje o przestarzałych wpisach z serwera konfiguracji, uruchom następujące polecenie przy użyciu maszyny źródłowej i adresu IP nieodświeżonego wpisu. 
   
    `Syntax: Unregister-ASRComponent.pl -IPAddress <IP_ADDRESS_OF_MACHINE_TO_UNREGISTER> -Component <Source/ PS / MT>`
 
    Jeśli masz wpis serwera źródłowego "lokalnego-VM01" z adresem IP 10.0.0.4, zamiast tego użyj poniższego polecenia.
 
    `perl Unregister-ASRComponent.pl -IPAddress 10.0.0.4 -Component Source`
 
5. Uruchom ponownie następujące usługi na maszynie źródłowej w celu ponownego zarejestrowania się na serwerze konfiguracji. 
 
    - InMage Scout Application Service
    - Agent InMage Scout VX — wskaźnik kontrolny/wpis

## <a name="upgrade-fails-when-the-services-fail-to-stop"></a>Uaktualnienie nie powiedzie się, gdy usługi zakończą się niepowodzeniem

Uaktualnienie serwera konfiguracji kończy się niepowodzeniem, gdy pewne usługi nie są zatrzymane. 

Aby zidentyfikować ten problem, przejdź do C:\ProgramData\ASRSetupLogs\ CX_TP_InstallLogFile na serwerze konfiguracji. Jeśli znajdziesz następujące błędy, wykonaj poniższe kroki, aby rozwiązać ten problem: 

```output
2018-06-28 14:28:12.943   Successfully copied php.ini to C:\Temp from C:\thirdparty\php5nts
2018-06-28 14:28:12.943   svagents service status - SERVICE_RUNNING
2018-06-28 14:28:12.944   Stopping svagents service.
2018-06-28 14:31:32.949   Unable to stop svagents service.
2018-06-28 14:31:32.949   Stopping svagents service.
2018-06-28 14:34:52.960   Unable to stop svagents service.
2018-06-28 14:34:52.960   Stopping svagents service.
2018-06-28 14:38:12.971   Unable to stop svagents service.
2018-06-28 14:38:12.971   Rolling back the install changes.
2018-06-28 14:38:12.971   Upgrade has failed.
```

W celu rozwiązania tego problemu:

Ręcznie Zatrzymaj następujące usługi:

- cxprocessserver
- Agent InMage Scout VX — wskaźnik kontrolny/wpis 
- Agent Microsoft Azure Recovery Services, 
- Microsoft Azure Site Recovery usługi, 
- tmansvc
  
Aby zaktualizować serwer konfiguracji, uruchom ponownie [ujednoliconą konfigurację](service-updates-how-to.md#links-to-currently-supported-update-rollups) .

## <a name="azure-active-directory-application-creation-failure"></a>Niepowodzenie tworzenia aplikacji Azure Active Directory

Masz niewystarczające uprawnienia do tworzenia aplikacji w usłudze Azure Active Directory (AAD) przy użyciu szablonu " [Open Virtualization Application" (komórki jajowe)](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template
) .

Aby rozwiązać ten problem, zaloguj się do Azure Portal i wykonaj jedną z następujących czynności:

- Zażądaj roli Deweloper aplikacji w usłudze AAD. Aby uzyskać więcej informacji na temat roli Deweloper aplikacji, zobacz [uprawnienia roli administrator w Azure Active Directory](../active-directory/roles/permissions-reference.md).
- Sprawdź, czy **użytkownik może utworzyć** flagę aplikacji *w usłudze* AAD. Aby uzyskać więcej informacji, zobacz [jak: korzystanie z portalu do tworzenia aplikacji usługi Azure AD i nazwy głównej usługi, która może uzyskiwać dostęp do zasobów](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

## <a name="process-servermaster-target-are-unable-to-communicate-with-the-configuration-server"></a>Serwer przetwarzania/główny element docelowy nie może komunikować się z serwerem konfiguracji 

Moduły serwer przetwarzania (PS) i główny element docelowy (MT) nie mogą komunikować się z serwerem konfiguracji (CS) i ich stan jest pokazywany jako niepołączony w Azure Portal.

Zwykle jest to spowodowane błędem z portem 443. Wykonaj następujące kroki, aby odblokować port i ponownie włączyć komunikację z usługami CS.

**Weryfikowanie, czy Agent MARS jest wywoływany przez głównego agenta docelowego**

Aby sprawdzić, czy główny agent docelowy może utworzyć sesję TCP dla adresu IP serwera konfiguracji, należy poszukać śledzenia podobnego do poniższego w głównych dziennikach agenta docelowego:

TCP \<Replace IP with CS IP here> : 52739 \<Replace IP with CS IP here> : 443 SYN_SENT 

TCP 192.168.1.40:52739 192.168.1.40:443 SYN_SENT//Zastąp adres IP adresem IP CS tutaj

W przypadku znalezienia śladów podobnych do następujących w dziennikach MT, Agent MT zgłasza błędy na porcie 443:

```output
#~> (11-20-2018 20:31:51):   ERROR  2508 8408 313 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
#~> (11-20-2018 20:31:54):   ERROR  2508 8408 314 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
```
 
Ten błąd może wystąpić, gdy inne aplikacje korzystają również z portu 443 lub z powodu blokowania ustawienia zapory.

W celu rozwiązania tego problemu:

- Sprawdź, czy port 443 nie jest blokowany przez zaporę.
- Jeśli port jest nieosiągalny z powodu innej aplikacji używającej tego portu, Zatrzymaj i Odinstaluj aplikację.
  - Jeśli zatrzymywanie aplikacji nie jest możliwe, skonfiguruj nową czystą CS.
- Uruchom ponownie serwer konfiguracji.
- Uruchom ponownie usługę IIS.

### <a name="configuration-server-is-not-connected-due-to-incorrect-uuid-entries"></a>Serwer konfiguracji nie jest połączony z powodu nieprawidłowych wpisów identyfikatora UUID

Ten błąd może wystąpić, gdy w bazie danych istnieje wiele wpisów identyfikatora UUID wystąpienia serwera konfiguracji (CS). Problem często występuje podczas klonowania maszyny wirtualnej serwera konfiguracji.

W celu rozwiązania tego problemu:

1. Usuń nieodświeżoną/starą maszynę wirtualną CS z serwera vCenter. Aby uzyskać więcej informacji, zobacz  [usuwanie serwerów i wyłączanie ochrony](site-recovery-manage-registration-and-protection.md).
2. Zaloguj się do maszyny wirtualnej serwera konfiguracji i nawiąż połączenie z bazą danych MySQL svsdb1. 
3. Wykonaj następujące zapytanie:

    > [!IMPORTANT]
    >
    > Sprawdź, czy wprowadzasz szczegóły identyfikatora UUID sklonowanego serwera konfiguracji lub przestarzałego wpisu serwera konfiguracji, który nie jest już używany do ochrony maszyn wirtualnych. Wprowadzenie niepoprawnego identyfikatora UUID spowoduje utratę informacji dla wszystkich istniejących elementów chronionych.
   
    ```
        MySQL> use svsdb1;
        MySQL> delete from infrastructurevms where infrastructurevmid='<Stale CS VM UUID>';
        MySQL> commit; 
    ```
4. Odśwież stronę portalu.

## <a name="an-infinite-sign-in-loop-occurs-when-entering-your-credentials"></a>Podczas wprowadzania poświadczeń występuje nieskończona pętla logowania

Po wprowadzeniu prawidłowej nazwy użytkownika i hasła na serwerze konfiguracji OVF logowanie na platformie Azure nadal będzie monitować o podanie poprawnych poświadczeń.

Ten problem może wystąpić, gdy czas systemowy jest nieprawidłowy.

W celu rozwiązania tego problemu:

Ustaw prawidłowy czas na komputerze i ponów próbę logowania. 
