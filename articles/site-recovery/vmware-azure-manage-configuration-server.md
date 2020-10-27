---
title: Zarządzanie serwerem konfiguracji na potrzeby odzyskiwania po awarii za pomocą Azure Site Recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: ramamill
ms.openlocfilehash: 2f1edc14efdeaf70bf4c2acc0e31e1517753ed3e
ms.sourcegitcommit: d767156543e16e816fc8a0c3777f033d649ffd3c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/26/2020
ms.locfileid: "92546351"
---
# <a name="manage-the-configuration-server-for-vmware-vmphysical-server-disaster-recovery"></a>Zarządzanie serwerem konfiguracji na potrzeby odzyskiwania po awarii maszyny wirtualnej VMware/serwera fizycznego

Lokalny serwer konfiguracji można skonfigurować w przypadku korzystania z [Azure Site Recovery](site-recovery-overview.md) na potrzeby odzyskiwania po awarii maszyn wirtualnych VMware i serwerów fizycznych na platformie Azure. Serwer konfiguracji koordynuje komunikację między lokalnym programem VMware i platformą Azure i zarządza replikacją danych. Ten artykuł zawiera podsumowanie typowych zadań związanych z zarządzaniem serwerem konfiguracji po jego wdrożeniu.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-windows-license"></a>Aktualizowanie licencji systemu Windows

Licencja świadczona z szablonem OVF to licencja ewaluacyjna ważna przez 180 dni. W przypadku nieprzerwanego użycia należy aktywować system Windows przy użyciu licencji z licencją. Aktualizację licencji można wykonać za pomocą klucza autonomicznego lub klucza standardowego usługi KMS. Wskazówki są dostępne w [wierszu polecenia narzędzia DISM systemu Windows do uruchamiania systemu operacyjnego](/windows-hardware/manufacture/desktop/dism-windows-edition-servicing-command-line-options). Aby uzyskać klucze, zapoznaj się z tematem [Konfiguracja klienta usługi KMS](/windows-server/get-started/kmsclientkeys).

## <a name="access-configuration-server"></a>Serwer konfiguracji dostępu

Dostęp do serwera konfiguracji można uzyskać w następujący sposób:

* Zaloguj się do maszyny wirtualnej, na której ją wdrożono, i zacznij **Azure Site Recovery Configuration Manager** ze skrótu na pulpicie.
* Alternatywnie możesz uzyskać dostęp do serwera konfiguracji zdalnie z https:// *ConfigurationServerName* /: 44315/. Zaloguj się przy użyciu poświadczeń administratora.

## <a name="modify-vmware-server-settings"></a>Modyfikowanie ustawień serwera VMware

1. Aby skojarzyć inny serwer VMware z serwerem konfiguracji, po [zalogowaniu](#access-configuration-server)wybierz pozycję **Dodaj vCenter Server/vSphere ESXi serwer** .
2. Wprowadź szczegóły, a następnie wybierz przycisk **OK** .

## <a name="modify-credentials-for-automatic-discovery"></a>Modyfikowanie poświadczeń na potrzeby automatycznego odnajdywania

1. Aby zaktualizować poświadczenia używane do nawiązywania połączenia z serwerem VMware w celu automatycznego odnajdywania maszyn wirtualnych VMware, po [zalogowaniu](#access-configuration-server)wybierz konto i kliknij przycisk **Edytuj** .
2. Wprowadź nowe poświadczenia, a następnie wybierz przycisk **OK** .

    ![Modyfikowanie oprogramowania VMware](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)

Poświadczenia można także modyfikować za pomocą CSPSConfigtool.exe.

1. Zaloguj się do serwera konfiguracji i uruchom CSPSConfigtool.exe
2. Wybierz konto, które chcesz zmodyfikować, a następnie kliknij przycisk **Edytuj** .
3. Wprowadź zmodyfikowane poświadczenia i kliknij przycisk **OK** .

## <a name="modify-credentials-for-mobility-service-installation"></a>Modyfikuj poświadczenia instalacji usługi mobilności

Zmodyfikuj poświadczenia używane do automatycznej instalacji usługi mobilności na maszynach wirtualnych VMware, które są włączone na potrzeby replikacji.

1. Po [zalogowaniu](#access-configuration-server)wybierz pozycję **Zarządzaj poświadczeniami maszyny wirtualnej**
2. Wybierz konto, które chcesz zmodyfikować, a następnie kliknij pozycję **Edytuj** .
3. Wprowadź nowe poświadczenia, a następnie wybierz przycisk **OK** .

    ![Modyfikowanie poświadczeń usługi mobilności](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

Możesz również zmodyfikować poświadczenia za CSPSConfigtool.exe.

1. Zaloguj się do serwera konfiguracji i uruchom CSPSConfigtool.exe
2. Wybierz konto, które chcesz zmodyfikować, a następnie kliknij pozycję **Edytuj** .
3. Wprowadź nowe poświadczenia i kliknij przycisk **OK** .

## <a name="add-credentials-for-mobility-service-installation"></a>Dodawanie poświadczeń do instalacji usługi mobilności

Jeśli pominięto Dodawanie poświadczeń podczas OVF wdrażania serwera konfiguracji,

1. Po [zalogowaniu](#access-configuration-server)wybierz pozycję **Zarządzaj poświadczeniami maszyny wirtualnej** .
2. Kliknij pozycję **Dodaj poświadczenia maszyny wirtualnej** .
    ![Zrzut ekranu przedstawia okienko zarządzanie poświadczeniami maszyny wirtualnej za pomocą linku Dodawanie poświadczeń maszyny wirtualnej.](media/vmware-azure-manage-configuration-server/add-mobility-credentials.png)
3. Wprowadź nowe poświadczenia i kliknij przycisk **Dodaj** .

Możesz również dodać poświadczenia za CSPSConfigtool.exe.

1. Zaloguj się do serwera konfiguracji i uruchom CSPSConfigtool.exe
2. Kliknij przycisk **Dodaj** , wprowadź nowe poświadczenia i kliknij przycisk **OK** .

## <a name="modify-proxy-settings"></a>Modyfikowanie ustawień serwera proxy

Zmodyfikuj ustawienia serwera proxy używane przez maszynę serwera konfiguracji na potrzeby dostępu do Internetu na platformie Azure. W przypadku maszyny serwera przetwarzania oprócz domyślnego serwera przetwarzania uruchomionego na komputerze serwera konfiguracji należy zmodyfikować ustawienia na obu komputerach.

1. Po [zalogowaniu](#access-configuration-server) się do serwera konfiguracji wybierz pozycję **Zarządzaj łącznością** .
2. Zaktualizuj wartości serwera proxy. Następnie wybierz pozycję **Zapisz** , aby zaktualizować ustawienia.

## <a name="add-a-network-adapter"></a>Dodaj kartę sieciową

Szablon Open Virtualization Format (OVF) wdraża maszynę wirtualną serwera konfiguracji z jedną kartą sieciową.

- [Do maszyny wirtualnej można dodać dodatkową kartę](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter), ale należy ją dodać przed zarejestrowaniem serwera konfiguracji w magazynie.
- Aby dodać adapter po zarejestrowaniu serwera konfiguracji w magazynie, Dodaj kartę we właściwościach maszyny wirtualnej. Następnie należy [ponownie zarejestrować](#reregister-a-configuration-server-in-the-same-vault) serwer w magazynie.

## <a name="how-to-renew-ssl-certificates"></a>Jak odnowić certyfikaty SSL

Serwer konfiguracji ma wbudowany serwer sieci Web, który organizuje działania agentów mobilności na wszystkich chronionych maszynach, na serwerach procesów nieskompilowanych/skalowalnych w poziomie oraz podłączonych do nich głównych serwerach docelowych. Serwer sieci Web używa certyfikatu SSL do uwierzytelniania klientów. Certyfikat wygasa po upływie trzech lat i może zostać odnowiony w dowolnym momencie.

### <a name="check-expiry"></a>Sprawdź ważność

Data wygaśnięcia jest wyświetlana w obszarze **kondycja serwera konfiguracji** . W przypadku wdrożeń serwera konfiguracji przed 2016 maja wartość wygaśnięcia certyfikatu została ustawiona na jeden rok. Jeśli masz certyfikat, który wkrótce wygaśnie, wystąpią następujące sytuacje:

- Gdy data wygaśnięcia wynosi dwa miesiące lub mniej, usługa zaczyna wysyłać powiadomienia w portalu i pocztą e-mail (Jeśli subskrybujesz powiadomienia Site Recovery).
- Na stronie zasobów magazynu zostanie wyświetlony transparent powiadomienia. Aby uzyskać więcej informacji, wybierz transparent.
- Jeśli zobaczysz przycisk **Uaktualnij teraz** , oznacza to, że niektóre składniki w środowisku nie zostały uaktualnione do wersji 9.4. xxxx. x lub wyższej. Uaktualnij składniki przed odnowieniem certyfikatu. Nie można odnowić w starszych wersjach.

### <a name="if-certificates-are-yet-to-expire"></a>Jeśli certyfikaty jeszcze nie wygasną

1. Aby odnowić w magazynie, Otwórz **Site Recovery**  >  **serwer konfiguracji** infrastruktury. Wybierz wymagany serwer konfiguracji.
2. Upewnij się, że wszystkie składniki serwerów przetwarzania skalowalnego w poziomie, główne serwery docelowe i agenci mobilności na wszystkich chronionych maszynach znajdują się w najnowszych wersjach i są w stanie połączonym.
3. Teraz wybierz pozycję **Odnów certyfikaty** .
4. Uważnie postępuj zgodnie z instrukcjami na tej stronie, a następnie kliknij przycisk OK, aby odnowić certyfikaty na wybranym serwerze konfiguracji i skojarzone składniki.

### <a name="if-certificates-have-already-expired"></a>Jeśli certyfikaty już wygasły

1. Po wygaśnięciu **nie można odnowić certyfikatów z Azure Portal** . Przed kontynuowaniem upewnij się, że wszystkie składniki skalowalne w poziomie serwery procesów, główne serwery docelowe i agenci mobilności na wszystkich chronionych komputerach znajdują się w najnowszych wersjach i są w stanie połączonym.
2. **Wykonaj tę procedurę tylko wtedy, gdy certyfikaty już wygasły.** Zaloguj się do serwera konfiguracji, przejdź do dysku C > dane programu > Site Recovery > Home > svsystems > bin i uruchom narzędzie wykonawcze "Narzędzie renewcerts" jako administrator.
3. W oknie wykonywania programu PowerShell są wydawane punkty obecności i wyzwalane są odnowienia certyfikatów. Może to potrwać maksymalnie 15 minut. Nie zamykaj okna do momentu zakończenia odnawiania.

:::image type="content" source="media/vmware-azure-manage-configuration-server/renew-certificates.png" alt-text="RenewCertificates":::

## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Ponowne zarejestrowanie serwera konfiguracji w tym samym magazynie

Jeśli zachodzi taka potrzeba, można ponownie zarejestrować serwer konfiguracji w tym samym magazynie. Jeśli istnieje dodatkowy serwer przetwarzania, oprócz domyślnego serwera przetwarzania uruchomionego na komputerze serwera konfiguracji, należy ponownie zarejestrować obie maszyny.


1. W magazynie Otwórz pozycję **Zarządzaj**  >  **Site Recovery Infrastructure**  >  **serwerami konfiguracji** infrastruktury Site Recovery.
2. W obszarze **serwery** wybierz pozycję **Pobierz klucz rejestracji** , aby pobrać plik poświadczeń magazynu.
3. Zaloguj się na komputerze serwera konfiguracji.
4. W **%ProgramData%\ASR\home\svsystems\bin** Otwórz **cspsconfigtool.exe** .
5. Na karcie **rejestracja w magazynie** wybierz pozycję **Przeglądaj** , a następnie Znajdź pobrany plik poświadczeń magazynu.
6. W razie konieczności Podaj szczegóły serwera proxy. Następnie wybierz pozycję **Zarejestruj** .
7. Otwórz okno poleceń administracyjnych programu PowerShell i uruchom następujące polecenie:
   ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

    >[!NOTE]
    >W celu **ściągnięcia najnowszych certyfikatów** z serwera konfiguracji do skalowalnego w poziomie serwera przetwarzania wykonaj polecenie *" \<Installation Drive\Microsoft Azure Site Recovery\agent\cdpcli.exe> " — registermt*

8. Na koniec Uruchom ponownie program usługą obengine, wykonując następujące polecenie.
   ```
        net stop obengine
        net start obengine
   ```


## <a name="register-a-configuration-server-with-a-different-vault"></a>Rejestrowanie serwera konfiguracji z innym magazynem

> [!WARNING]
> Poniższy krok odkojarzy serwer konfiguracji z bieżącego magazynu, a replikacja wszystkich chronionych maszyn wirtualnych na serwerze konfiguracji jest zatrzymana.

1. Zaloguj się do serwera konfiguracji.
2. Otwórz okno poleceń administracyjnych programu PowerShell i uruchom następujące polecenie:

    ```
    reg delete "HKLM\Software\Microsoft\Azure Site Recovery\Registration"
    net stop dra
    ```
3. Uruchom Portal Server Appliance przeglądarki konfiguracji przy użyciu skrótu na pulpicie.
4. Wykonaj kroki rejestracji podobne do nowej [rejestracji](vmware-azure-tutorial.md#register-the-configuration-server)serwera konfiguracji.

## <a name="upgrade-the-configuration-server"></a>Uaktualnij serwer konfiguracji

Aby zaktualizować serwer konfiguracji, należy uruchomić pakiet zbiorczy aktualizacji. Aktualizacje mogą być stosowane do wersji N-4. Na przykład:

- Jeśli uruchomisz 9,7, 9,8, 9,9 lub 9,10, możesz przeprowadzić uaktualnienie bezpośrednio do wersji 9,11.
- Jeśli zostanie uruchomiony system 9,6 lub starszy i chcesz przeprowadzić uaktualnienie do wersji 9,11, musisz najpierw przeprowadzić uaktualnienie na wersję 9,7. przed 9,11.

Aby uzyskać szczegółowe wskazówki dotyczące pomocy technicznej dotyczącej składników Azure Site Recovery, zobacz [tutaj](./service-updates-how-to.md#support-statement-for-azure-site-recovery).
Linki do pakietów zbiorczych aktualizacji na potrzeby uaktualniania do wszystkich wersji serwera konfiguracji są dostępne [tutaj](./service-updates-how-to.md#links-to-currently-supported-update-rollups).

> [!IMPORTANT]
> W przypadku każdej nowej wersji "N" wydanego składnika Azure Site Recovery wszystkie wersje poniżej "N-4" są uznawane za nieobsługiwane. Zawsze zaleca się przeprowadzenie uaktualnienia do najnowszej wersji.</br>
> Aby uzyskać szczegółowe wskazówki dotyczące pomocy technicznej dotyczącej składników Azure Site Recovery, zobacz [tutaj](./service-updates-how-to.md#support-statement-for-azure-site-recovery).

Uaktualnij serwer w następujący sposób:

1. W magazynie przejdź do pozycji **Zarządzanie**  >  **Site Recovery infrastruktura**  >  **serwery konfiguracji** .
2. Jeśli dostępna jest aktualizacja, w kolumnie > **wersja agenta** zostanie wyświetlony link.
    ![Aktualizowanie](./media/vmware-azure-manage-configuration-server/update2.png)
3. Pobierz plik instalatora aktualizacji na serwer konfiguracji.

    ![Zrzut ekranu pokazujący, gdzie kliknąć, aby pobrać plik instalatora aktualizacji.](./media/vmware-azure-manage-configuration-server/update1.png)

4. Kliknij dwukrotnie, aby uruchomić Instalatora.
5. Instalator wykryje bieżącą wersję uruchomioną na komputerze. Kliknij przycisk **tak** , aby rozpocząć uaktualnianie.
6. Po zakończeniu uaktualniania serwer sprawdza poprawność.

    ![Zrzut ekranu przedstawiający ukończoną konfigurację weryfikacji serwera.](./media/vmware-azure-manage-configuration-server/update3.png)

7. Kliknij przycisk **Zakończ** , aby zamknąć Instalatora.
8. Aby uaktualnić pozostałe składniki Site Recovery, zapoznaj się z naszymi [wskazówkami dotyczącymi uaktualniania](./service-updates-how-to.md#vmware-vmphysical-server-disaster-recovery-to-azure).

## <a name="upgrade-configuration-serverprocess-server-from-the-command-line"></a>Uaktualnianie serwera konfiguracji/serwera przetwarzania z wiersza polecenia

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

|Nazwa parametru| Typ | Opis| Wartości|
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
MySQLRootPassword = "Password>"
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

## <a name="delete-or-unregister-a-configuration-server"></a>Usuwanie lub Wyrejestrowywanie serwera konfiguracji

1. [Wyłącz ochronę](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) dla wszystkich maszyn wirtualnych na serwerze konfiguracji.
2. Usuń [skojarzenie](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) i [usunąć](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) wszystkie zasady replikacji z serwera konfiguracji.
3. [Usuń](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) wszystkie hosty vCenter/vSphere, które są skojarzone z serwerem konfiguracji.
4. W magazynie Otwórz **Site Recovery**  >  **serwery konfiguracji** infrastruktury.
5. Wybierz serwer konfiguracji, który chcesz usunąć. Następnie na stronie **szczegóły** wybierz pozycję **Usuń** .

    ![Usuń serwer konfiguracji](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)


### <a name="delete-with-powershell"></a>Usuwanie przy użyciu programu PowerShell

Opcjonalnie można usunąć serwer konfiguracji za pomocą programu PowerShell.

1. [Zainstaluj](/powershell/azure/install-Az-ps) moduł Azure PowerShell.
2. Zaloguj się do konta platformy Azure za pomocą tego polecenia:

    `Connect-AzAccount`
3. Wybierz subskrypcję magazynu.

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Ustaw kontekst magazynu.

    ```
    $vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Pobierz serwer konfiguracji.

    `$fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Usuń serwer konfiguracji.

    `Remove-AzSiteRecoveryFabric -Fabric $fabric [-Force]`

> [!NOTE]
> Aby wymusić usunięcie serwera konfiguracji, można użyć opcji **-Force** w Remove-AzSiteRecoveryFabric.

## <a name="generate-configuration-server-passphrase"></a>Generowanie hasła serwera konfiguracji

1. Zaloguj się do serwera konfiguracji, a następnie otwórz okno wiersza polecenia jako administrator.
2. Aby zmienić katalog na folder bin, wykonaj polecenie **CD%ProgramData%\ASR\home\svsystems\bin**
3. Aby wygenerować plik hasła, wykonaj **genpassphrase.exe-v > MobSvc. hasło** .
4. Hasło zostanie zapisane w pliku znajdującym się w lokalizacji **%ProgramData%\ASR\home\svsystems\bin\MobSvc.Passphrase** .

## <a name="refresh-configuration-server"></a>Odśwież serwer konfiguracji

1. W Azure Portal przejdź do **magazynu Recovery Services**  >  **Zarządzanie**  >  **Site Recovery infrastruktury**  >  **dla programu VMware &**  >  **serwery konfiguracji** komputerów fizycznych
2. Kliknij serwer konfiguracji, który chcesz odświeżyć.
3. W bloku ze szczegółowymi informacjami o wybranym serwerze konfiguracji kliknij pozycję **więcej**  >  **Odśwież serwer** .
4. Monitoruj postęp zadania w obszarze Recovery Services monitorowania **magazynu**  >  **Monitoring**  >  **Site Recovery zadania** .

## <a name="failback-requirements"></a>Wymagania dotyczące powrotu po awarii

Podczas ponownego włączania ochrony i powrotu po awarii lokalny serwer konfiguracji musi być uruchomiony i w stanie połączonym. W przypadku pomyślnego powrotu po awarii maszyny wirtualnej w bazie danych serwera konfiguracji musi istnieć niepowodzenie.

Upewnij się, że wykonywane są regularne zaplanowane kopie zapasowe serwera konfiguracji. Jeśli wystąpi awaria i serwer konfiguracji zostanie utracony, należy najpierw przywrócić serwer konfiguracji z kopii zapasowej i upewnić się, że przywrócony serwer konfiguracji ma ten sam adres IP, z którym został zarejestrowany w magazynie. Powrót po awarii nie będzie działał, jeśli dla przywróconego serwera konfiguracji zostanie użyty inny adres IP.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z samouczkami dotyczącymi konfigurowania odzyskiwania po awarii [maszyn wirtualnych VMware](vmware-azure-tutorial.md) na platformie Azure.