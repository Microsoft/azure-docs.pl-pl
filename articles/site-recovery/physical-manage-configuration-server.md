---
title: Zarządzanie serwerem konfiguracji serwerów fizycznych w Azure Site Recovery
description: W tym artykule opisano sposób zarządzania serwerem konfiguracji Azure Site Recovery na potrzeby odzyskiwania po awarii serwera fizycznego na platformie Azure.
services: site-recovery
author: mayurigupta13
ms.service: site-recovery
ms.topic: article
ms.date: 02/28/2019
ms.author: mayg
ms.openlocfilehash: ff612b7c052ead5658ea4bbfafd7aace51ba3c02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86132488"
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>Zarządzanie serwerem konfiguracji na potrzeby odzyskiwania po awarii serwera fizycznego

Lokalny serwer konfiguracji należy skonfigurować w przypadku korzystania z usługi [Azure Site Recovery](site-recovery-overview.md) na potrzeby odzyskiwania po awarii serwerów fizycznych na platformę Azure. Serwer konfiguracji koordynuje komunikację między maszynami lokalnymi i platformą Azure oraz zarządza replikacją danych. Ten artykuł zawiera podsumowanie typowych zadań związanych z zarządzaniem serwerem konfiguracji po jego wdrożeniu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Tabela zawiera podsumowanie wymagań wstępnych dotyczących wdrażania lokalnego komputera serwera konfiguracji.

| **Składnik** | **Wymaganie** |
| --- |---|
| Rdzenie procesora CPU| 8 |
| Pamięć RAM | 16 GB|
| Liczba dysków | 3, w tym dysk systemu operacyjnego, dysk pamięci podręcznej serwera przetwarzania i dysk przechowywania na potrzeby powrotu po awarii |
| Wolne miejsce na dysku (pamięć podręczna serwera przetwarzania) | 600 GB
| Wolne miejsce na dysku (dysk przechowywania) | 600 GB|
| System operacyjny  | Windows Server 2012 z dodatkiem R2 <br> Windows Server 2016 |
| Ustawienia regionalne systemu operacyjnego | Angielski (Stany Zjednoczone)|
| Wersja programu VMware vSphere PowerCLI | Niewymagane|
| Role systemu Windows Server | Nie należy włączać tych ról: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
| Zasady grupy| Nie włączaj tych zasad grupy: <br> -Uniemożliwianie dostępu do wiersza polecenia <br> — Uniemożliwianie dostępu do narzędzi edycji rejestru <br> — Logika zaufania dla plików załączników <br> -Włącz wykonywanie skryptu <br> [Dowiedz się więcej](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))|
| IIS | -Brak istniejącej domyślnej witryny sieci Web <br> -Włącz  [uwierzytelnianie anonimowe](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> -Włącz ustawienie [FastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10))  <br> — Żadna istniejąca witryna sieci Web/aplikacja nasłuchu na porcie 443<br>|
| Typ karty sieciowej | VMXNET3 (po wdrożeniu jako maszyny wirtualnej VMware) |
| Typ adresu IP | Static |
| Dostęp do Internetu | Serwer musi mieć dostęp do tych adresów URL: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - `https://management.azure.com` <br> -*. services.visualstudio.com <br> - https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi (niewymagane w przypadku serwerów przetwarzania skalowalnego w poziomie) <br> - time.nist.gov <br> - time.windows.com |
| Porty | 443 (organizowanie kanału sterowania)<br>9443 (transport danych)|

## <a name="download-the-latest-installation-file"></a>Pobierz najnowszy plik instalacyjny

Najnowsza wersja pliku instalacyjnego serwera konfiguracji jest dostępna w portalu Site Recovery. Ponadto można je pobrać bezpośrednio z [Centrum pobierania Microsoft](https://aka.ms/unifiedsetup).

1. Zaloguj się do Azure Portal i przejdź do swojego magazynu Recovery Services.
2. Przejdź do **Site Recovery**  >  **serwerów konfiguracji** infrastruktury (w obszarze dla maszyn fizycznych VMware &).
3. Kliknij przycisk **+ serwery** .
4. Na stronie **Dodawanie serwera** kliknij przycisk Pobierz, aby pobrać klucz rejestracji. Ten klucz jest potrzebny podczas instalacji serwera konfiguracji w celu zarejestrowania go w usłudze Azure Site Recovery.
5. Kliknij link **pobierz Microsoft Azure Site Recovery Unified Setup** , aby pobrać najnowszą wersję serwera konfiguracji.

   ![Strona pobierania](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>Instalowanie i rejestrowanie serwera

1. Uruchom plik instalacyjny ujednoliconego instalatora.
2. W obszarze **przed rozpoczęciem**wybierz pozycję **Zainstaluj serwer konfiguracji i serwer przetwarzania**.

    ![Przed rozpoczęciem](./media/physical-manage-configuration-server/combined-wiz1.png)

3. W obszarze **Licencja na oprogramowanie innej firmy** kliknij pozycję **Akceptuję**, aby pobrać i zainstalować program MySQL.
4. W obszarze **Ustawienia internetowe**Określ, jak dostawca działający na serwerze konfiguracji łączy się z Azure Site Recovery przez Internet. Upewnij się, że adresy URL są dozwolone.

    - Jeśli chcesz nawiązać połączenie z serwerem proxy, który jest aktualnie skonfigurowany na komputerze, wybierz pozycję **Połącz z Azure Site Recovery przy użyciu serwera proxy**.
    - Jeśli chcesz, aby dostawca łączył się bezpośrednio, wybierz opcję **Połącz bezpośrednio z Azure Site Recovery bez serwera proxy**.
    - Jeśli istniejący serwer proxy wymaga uwierzytelniania lub jeśli chcesz użyć niestandardowego serwera proxy dla połączenia dostawcy, wybierz opcję **Połącz przy użyciu niestandardowych ustawień serwera proxy**, a następnie określ adres, port i poświadczenia.
     ![Zapora](./media/physical-manage-configuration-server/combined-wiz4.png)
6. W kroku **Sprawdzanie wymagań wstępnych** Instalator przeprowadza kontrolę w celu upewnienia się, że można uruchomić instalację. Jeśli zostanie wyświetlone ostrzeżenie dotyczące **kontroli synchronizacji czasu globalnego**, sprawdź, czy czas zegara systemowego (ustawienia **Data i godzina**) jest taki sam jak dla strefy czasowej.

    ![Wymagania wstępne](./media/physical-manage-configuration-server/combined-wiz5.png)
7. W obszarze **Konfiguracja programu MySQL** utwórz poświadczenia do logowania się do zainstalowanego wystąpienia serwera programu MySQL.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. W obszarze **Szczegóły środowiska** wybierz, czy zamierzasz replikować maszyny wirtualne programu VMware. Jeśli jesteś, Instalator sprawdzi, czy zainstalowano program PowerCLI 6,0.
9. W obszarze **Lokalizacja instalacji** wybierz, gdzie mają zostać zainstalowane pliki binarne i gdzie ma być przechowywana pamięć podręczną. Na wybranym dysku musi być co najmniej 5 GB dostępnego miejsca, ale zalecamy dysk pamięci podręcznej z co najmniej 600 GB wolnego miejsca.

    ![Lokalizacja instalacji](./media/physical-manage-configuration-server/combined-wiz8.png)
10. W **obszarze Wybór sieci**najpierw wybierz kartę interfejsu sieciowego używaną przez serwer przetwarzania na potrzeby odnajdywania i instalacji wypychanej usługi mobilności na maszynach źródłowych, a następnie wybierz kartę sieciową używaną przez serwer konfiguracji do łączności z platformą Azure. Port 9443 jest domyślnym portem używanym do wysyłania i odbierania ruchu związanego z replikacją, ale możesz zmienić ten numer portu tak, aby odpowiadał wymaganiom Twojego środowiska. Oprócz portu 9443 otwieramy też port 443, który jest używany przez serwer sieci Web do organizowania operacji replikacji. Nie należy używać portu 443 do wysyłania lub otrzymywania ruchu związanego z replikacją.

    ![Wybór sieci](./media/physical-manage-configuration-server/combined-wiz9.png)


11. W obszarze **Podsumowanie** przejrzyj informacje i kliknij przycisk **Zainstaluj**. Po zakończeniu instalacji generowane jest hasło. Będzie ono potrzebne po włączeniu replikacji, dlatego skopiuj je i przechowuj w bezpiecznym miejscu.


Po zakończeniu rejestracji serwer jest wyświetlany w bloku **Ustawienia**  >  **serwery** w magazynie.


## <a name="install-from-the-command-line"></a>Instalowanie z wiersza polecenia

Uruchom plik instalacyjny w następujący sposób:

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Przykładowe zastosowanie
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>Parametry

|Nazwa parametru| Type | Opis| Wartości|
|-|-|-|-|
| /ServerMode|Wymagane|Określa, czy mają zostać zainstalowane oba serwery, konfiguracji i przetwarzania, czy tylko serwer przetwarzania|CS<br>PS|
|/InstallLocation|Wymagane|Folder, w którym są instalowane składniki| Dowolny folder na komputerze|
|/MySQLCredsFilePath|Wymagane|Ścieżka pliku, w której są przechowywane poświadczenia serwera MySQL|Plik powinien mieć format określony poniżej|
|/VaultCredsFilePath|Wymagane|Ścieżka pliku poświadczeń magazynu|Prawidłowa ścieżka pliku|
|/EnvType|Wymagane|Typ środowiska, który ma być chroniony |VMware<br>NonVMware|
|/PSIP|Wymagane|Adres IP karty sieciowej do użytku podczas przesyłania danych replikacji| Dowolny prawidłowy adres IP|
|/CSIP|Wymagane|Adres IP karty sieciowej, na której nasłuchuje serwer konfiguracji| Dowolny prawidłowy adres IP|
|/PassphraseFilePath|Wymagane|Pełna ścieżka do lokalizacji pliku hasła|Prawidłowa ścieżka pliku|
|/BypassProxy|Opcjonalne|Określa, że serwer konfiguracji łączy się z platformą Azure bez serwera proxy|Należy uzyskać tę wartość z Venu|
|/ProxySettingsFilePath|Opcjonalne|Ustawienia serwera proxy (domyślny serwer proxy wymaga uwierzytelniania lub niestandardowy serwer proxy)|Plik powinien mieć format określony poniżej|
|DataTransferSecurePort|Opcjonalne|Numer portu dla protokołu PSIP do użytku z danymi replikacji| Prawidłowy numer portu (wartość domyślna to 9433)|
|/SkipSpaceCheck|Opcjonalne|Pomiń sprawdzanie miejsca dla dysku pamięci podręcznej| |
|/AcceptThirdpartyEULA|Wymagane|Flaga implikuje akceptację umowy licencyjnej innego producenta| |
|/ShowThirdpartyEULA|Opcjonalne|Wyświetla umowę licencyjną innej firmy. Jeśli zostanie podany w danych wejściowych, wszystkie inne parametry są ignorowane| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Utwórz plik wejściowy dla MYSQLCredsFilePath

Parametr MySQLCredsFilePath pobiera plik jako dane wejściowe. Utwórz plik przy użyciu następującego formatu i przekaż go jako parametr wejściowy MySQLCredsFilePath.
```ini
[MySQLCredentials]
MySQLRootPassword = "Password"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>Utwórz plik wejściowy dla ProxySettingsFilePath
Parametr ProxySettingsFilePath pobiera plik jako dane wejściowe. Utwórz plik przy użyciu następującego formatu i przekaż go jako parametr wejściowy ProxySettingsFilePath.

```ini
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modify-proxy-settings"></a>Modyfikowanie ustawień serwera proxy

Ustawienia serwera proxy dla komputera serwera konfiguracji można modyfikować w następujący sposób:

1. Zaloguj się do serwera konfiguracji.
2. Uruchom cspsconfigtool.exe przy użyciu skrótu na pulpicie.
3. Kliknij kartę **rejestracja w magazynie** .
4. Pobierz nowy plik rejestracji magazynu z portalu i podaj go jako dane wejściowe do narzędzia.

   ![Rejestr-konfiguracja-serwer](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Podaj szczegóły nowego serwera proxy, a następnie kliknij przycisk **zarejestruj** .
6. Otwórz okno poleceń administracyjnych programu PowerShell.
7. Uruchom następujące polecenie:

   ```powershell
   $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
   net stop obengine
   net start obengine
   ```

   > [!WARNING]
   > W przypadku dołączenia dodatkowych serwerów przetwarzania do serwera konfiguracji należy [poprawić ustawienia serwera proxy na wszystkich serwerach procesów skalowalnych w poziomie](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server) w danym wdrożeniu.

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Ponowne rejestrowanie serwera konfiguracji z tym samym magazynem
1. Zaloguj się do serwera konfiguracji.
2. Uruchom cspsconfigtool.exe przy użyciu skrótu na pulpicie.
3. Kliknij kartę **rejestracja w magazynie** .
4. Pobierz nowy plik rejestracji z portalu i podaj go jako dane wejściowe do narzędzia.
      ![Rejestr-konfiguracja-serwer](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Podaj szczegóły serwera proxy, a następnie kliknij przycisk **zarejestruj** .  
6. Otwórz okno poleceń administracyjnych programu PowerShell.
7. Uruchom następujące polecenie

    ```powershell
    $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
    net stop obengine
    net start obengine
    ```

   > [!WARNING]
   > Jeśli masz wiele serwerów przetwarzania, musisz [je ponownie zarejestrować](vmware-azure-manage-process-server.md#reregister-a-process-server).

## <a name="register-a-configuration-server-with-a-different-vault"></a>Rejestrowanie serwera konfiguracji z innym magazynem

> [!WARNING]
> Poniższy krok odkojarzy serwer konfiguracji z bieżącego magazynu, a replikacja wszystkich chronionych maszyn wirtualnych na serwerze konfiguracji jest zatrzymana.

1. Zaloguj się na serwerze konfiguracji
2. w wierszu polecenia administratora uruchom polecenie:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Uruchom cspsconfigtool.exe przy użyciu skrótu na pulpicie.
4. Kliknij kartę **rejestracja w magazynie** .
5. Pobierz nowy plik rejestracji z portalu i podaj go jako dane wejściowe do narzędzia.
6. Podaj szczegóły serwera proxy, a następnie kliknij przycisk **zarejestruj** .  
7. Otwórz okno poleceń administracyjnych programu PowerShell.
8. Uruchom następujące polecenie
    ```powershell
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Uaktualnianie serwera konfiguracji

Aby zaktualizować serwer konfiguracji, należy uruchomić pakiet zbiorczy aktualizacji. Aktualizacje mogą być stosowane do wersji N-4. Na przykład:

- Jeśli korzystasz z 9,7, 9,8, 9,9 lub 9,10, możesz przeprowadzić uaktualnienie bezpośrednio do wersji 9,11.
- Jeśli korzystasz z systemu 9,6 lub starszego, a chcesz uaktualnić do wersji 9,11, musisz najpierw przeprowadzić uaktualnienie do programu wersja 9,7. przed 9,11.

Linki do pakietów zbiorczych aktualizacji na potrzeby uaktualniania do wszystkich wersji serwera konfiguracji są dostępne na [stronie aktualizacji typu wiki](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Uaktualnij serwer w następujący sposób:

1. Pobierz plik instalatora aktualizacji na serwer konfiguracji.
2. Kliknij dwukrotnie, aby uruchomić Instalatora.
3. Instalator wykryje bieżącą wersję uruchomioną na komputerze.
4. Kliknij przycisk **OK** , aby potwierdzić i uruchomić uaktualnienie. 


## <a name="delete-or-unregister-a-configuration-server"></a>Usuwanie lub Wyrejestrowywanie serwera konfiguracji

> [!WARNING]
> Przed rozpoczęciem likwidowania serwera konfiguracji należy upewnić się, że zostały wymienione poniżej.
> 1. [Wyłącz ochronę](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) dla wszystkich maszyn wirtualnych na tym serwerze konfiguracji.
> 2. Usuń [skojarzenie](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) i [usunąć](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) wszystkie zasady replikacji z serwera konfiguracji.
> 3. [Usuń](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) wszystkie serwery vCenter/vSphere, które są skojarzone z serwerem konfiguracji.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Usuń serwer konfiguracji z Azure Portal
1. W Azure Portal przejdź do **Site Recovery**  >  **serwery konfiguracji** infrastruktury z menu magazyn.
2. Kliknij serwer konfiguracji, który chcesz zlikwidować.
3. Na stronie Szczegóły serwera konfiguracji kliknij przycisk **Usuń** .
4. Kliknij przycisk **tak** , aby potwierdzić usunięcie serwera.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>Odinstaluj serwer konfiguracji wraz z jego zależnościami
> [!TIP]
>   Jeśli planujesz ponownie użyć serwera konfiguracji z Azure Site Recovery, możesz przejść bezpośrednio do kroku 4

1. Zaloguj się do serwera konfiguracji jako administrator.
2. Otwórz Panel sterowania > program > Odinstaluj programy
3. Odinstaluj programy w następującej kolejności:
   * Agent Microsoft Azure Recovery Services
   * Microsoft Azure Site Recovery usługa mobilności/główny serwer docelowy
   * Dostawca Site Recovery Microsoft Azure
   * Microsoft Azure Site Recovery serwera konfiguracji/serwera przetwarzania
   * Microsoft Azure Site Recovery konfiguracji zależności serwera
   * Serwer MySQL 5,5
4. Uruchom następujące polecenie z wiersza polecenia i administratora.
   ```
   reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
   ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Usuwanie lub Wyrejestrowywanie serwera konfiguracji (program PowerShell)

1. [Zainstaluj](/powershell/azure/install-Az-ps) program Moduł Azure PowerShell
2. Zaloguj się do konta platformy Azure przy użyciu polecenia
    
    `Connect-AzAccount`
3. Wybierz subskrypcję, w ramach której jest obecny magazyn

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Teraz Skonfiguruj kontekst magazynu
    
    ```powershell
    $Vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $Vault
    ```
4. Pobierz serwer konfiguracji

    `$Fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Usuń serwer konfiguracji

    `Remove-AzSiteRecoveryFabric -Fabric $Fabric [-Force]`

> [!NOTE]
> Opcja **-Force** w Remove-AzSiteRecoveryFabric może służyć do wymuszenia usunięcia/usunięcia serwera konfiguracji.

## <a name="renew-tlsssl-certificates"></a>Odnawianie certyfikatów TLS/SSL
Serwer konfiguracji ma wbudowany serwer sieci Web, który organizuje działania usługi mobilności, serwery przetwarzania i połączone z nim główne serwery docelowe. Serwer sieci Web używa certyfikatu TLS/SSL do uwierzytelniania klientów. Certyfikat wygasa po upływie trzech lat i może zostać odnowiony w dowolnym momencie.

### <a name="check-expiry"></a>Sprawdź ważność

W przypadku wdrożeń serwera konfiguracji przed 2016 maja wartość wygaśnięcia certyfikatu została ustawiona na jeden rok. Jeśli masz certyfikat do wygaśnięcia, wystąpią następujące sytuacje:

- Gdy data wygaśnięcia wynosi dwa miesiące lub mniej, usługa zaczyna wysyłać powiadomienia w portalu i pocztą e-mail (Jeśli subskrybujesz powiadomienia Azure Site Recovery).
- Na stronie zasobów magazynu zostanie wyświetlony transparent powiadomienia. Kliknij transparent, aby uzyskać więcej szczegółów.
- Jeśli zobaczysz przycisk **Uaktualnij teraz** , oznacza to, że w danym środowisku istnieją pewne składniki, które nie zostały uaktualnione do wersji 9.4. x lub wyższej. Uaktualnij składniki przed odnowieniem certyfikatu. Nie można odnowić w starszych wersjach.

### <a name="renew-the-certificate"></a>Odnów certyfikat

1. W magazynie Otwórz **Site Recovery**  >  **serwer konfiguracji**infrastruktury i kliknij wymagany serwer konfiguracji.
2. Data wygaśnięcia jest wyświetlana w obszarze **kondycja serwera konfiguracji**
3. Kliknij pozycję **Odnów certyfikaty**. 




## <a name="common-issues"></a>Typowe problemy
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z samouczkami dotyczącymi konfigurowania odzyskiwania po awarii [serwerów fizycznych](./physical-azure-disaster-recovery.md) na platformie Azure.
