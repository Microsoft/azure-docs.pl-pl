---
title: Przygotowywanie wirtualnego dysku twardego systemu Windows do przekazania do platformy Azure
description: Dowiedz się, jak przygotować plik VHD lub VHDX systemu Windows w celu przekazania go do platformy Azure
author: glimoli
manager: dcscontentpm
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 05/11/2019
ms.author: genli
ms.openlocfilehash: 1f8f595fd0656a35d54012ece64e826357e03bb9
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82098683"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Przygotowywanie dysku VHD lub VHDX systemu Windows do przekazania na platformę Azure

Przed przekazaniem maszyny wirtualnej z systemem Windows z lokalizacji lokalnej do platformy Azure należy przygotować wirtualny dysk twardy (VHD lub VHDX). Platforma Azure obsługuje maszyny wirtualne generacji 1 i 2, które są w formacie pliku VHD i mają dysk o stałym rozmiarze. Maksymalny dozwolony rozmiar wirtualnego dysku twardego to 2 TB.

Na maszynie wirtualnej generacji 1 można skonwertować system plików VHDX na dysk VHD. Możesz również skonwertować dynamicznie powiększający dysk na dysk o stałym rozmiarze. Ale nie można zmienić generacji maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz temat [jak utworzyć maszynę wirtualną generacji 1 lub 2 w funkcji Hyper-V?](https://technet.microsoft.com/windows-server-docs/compute/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) i [obsłudze platformy Azure dla maszyn wirtualnych 2. generacji (wersja zapoznawcza)](generation-2.md).

Aby uzyskać informacje o zasadach pomocy technicznej dla maszyn wirtualnych platformy Azure, zobacz [Microsoft Server Software Support for Azure](https://support.microsoft.com/help/2721672/microsoft-server-software-support-for-microsoft-azure-virtual-machines)Virtual Machines.

> [!NOTE]
> Instrukcje zawarte w tym artykule dotyczą:
>1. 64-bitowa wersja systemu Windows Server 2008 R2 i nowszych systemów operacyjnych Windows Server. Aby uzyskać informacje o uruchamianiu 32-bitowego systemu operacyjnego na platformie Azure, zobacz [obsługa 32-bitowych systemów operacyjnych na maszynach wirtualnych platformy Azure](https://support.microsoft.com/help/4021388/support-for-32-bit-operating-systems-in-azure-virtual-machines).
>2. Jeśli do migracji obciążenia, takiego jak Azure Site Recovery lub Azure Migrate, zostanie użyte dowolne narzędzie do odzyskiwania po awarii, ten proces jest nadal wymagany, a następnie następuje w systemie operacyjnym gościa w celu przygotowania obrazu przed migracją.

## <a name="system-file-checker-sfc-command"></a>Narzędzie System File Checker (SFC) — polecenie

### <a name="run-windows-system-file-checker-utility-run-sfc-scannow-on-os-prior-to-generalization-step-of-creating-customer-os-image"></a>Uruchom narzędzie sprawdzania plików systemu Windows (Uruchom sfc/scannow) w systemie operacyjnym przed etapem uogólniania tworzenia obrazu systemu operacyjnego klienta

Narzędzie System File Checker (SFC) służy do weryfikowania i zastępowania plików systemu Windows.

Aby uruchomić polecenie SFC:

1. Otwórz wiersz polecenia CMD z podwyższonym poziomem uprawnień jako administrator.
1. Wpisz `sfc /scannow` i wybierz **Enter**.

    ![ narzędzie sprawdzania plików systemowych](media/prepare-for-upload-vhd-image/system-file-checker.png)


Po zakończeniu skanowania programu SFC spróbuj zainstalować aktualizacje systemu Windows i ponownie uruchomić komputer.

## <a name="convert-the-virtual-disk-to-a-fixed-size-and-to-vhd"></a>Konwertowanie dysku wirtualnego na stały rozmiar i na dysk VHD

Jeśli musisz skonwertować dysk wirtualny do wymaganego formatu dla platformy Azure, użyj jednej z metod opisanych w tej sekcji:

1. Przed uruchomieniem procesu konwersji dysku wirtualnego wykonaj kopię zapasową maszyny wirtualnej.

1. Upewnij się, że wirtualny dysk twardy systemu Windows działa poprawnie na serwerze lokalnym. Przed podjęciem próby konwersji lub przekazania na platformę Azure Usuń wszystkie błędy w samej maszynie wirtualnej.

1. W odniesieniu do rozmiaru wirtualnego dysku twardego:

   1. Wszystkie wirtualne dyski twarde na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1 MB. Podczas konwertowania z dysku surowego na dysk VHD należy upewnić się, że rozmiar dysku surowego jest wielokrotnością 1 MB przed konwersją. Ułamki megabajtów będą powodowały błędy podczas tworzenia obrazów na podstawie przekazanego wirtualnego dysku twardego.

   2. Maksymalny dozwolony rozmiar wirtualnego dysku twardego systemu operacyjnego to 2 TB.


Po konwersji dysku utwórz maszynę wirtualną, która używa tego dysku. Uruchom i zaloguj się na maszynie wirtualnej, aby zakończyć przygotowywanie jej do przekazania.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Konwertowanie dysku za pomocą Menedżera funkcji Hyper-V 
1. Otwórz Menedżera funkcji Hyper-V i wybierz komputer lokalny po lewej stronie. W menu znajdującym się nad listą komputerów wybierz **Akcja** > **Edytuj dysk**.
2. Na stronie **lokalizowanie wirtualnego dysku twardego** wybierz swój dysk wirtualny.
3. Na stronie **Wybierz akcję** wybierz pozycję **Konwertuj** > **dalej**.
4. Jeśli musisz skonwertować z dysku VHDX, wybierz pozycję **VHD** > **dalej**.
5. Jeśli trzeba skonwertować dynamicznie powiększający się dysk, wybierz pozycję **stały rozmiar** > **dalej**.
6. Znajdź i wybierz ścieżkę, w której ma zostać zapisany nowy plik VHD.
7. Wybierz pozycję **Zakończ**.

> [!NOTE]
> Użyj sesji programu PowerShell z podwyższonym poziomem uprawnień, aby uruchomić polecenia z tego artykułu.

### <a name="use-powershell-to-convert-the-disk"></a>Konwertowanie dysku przy użyciu programu PowerShell 
Dysk wirtualny można skonwertować przy użyciu polecenia [convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) w programie Windows PowerShell. Wybierz opcję **Uruchom jako administrator** podczas uruchamiania programu PowerShell. 

Poniższe przykładowe polecenie konwertuje dysk z dysku VHDX na dysk VHD. Polecenie umożliwia również Konwertowanie dysku z dynamicznie powiększanego dysku na dysk o stałym rozmiarze.

```Powershell
Convert-VHD –Path c:\test\MY-VM.vhdx –DestinationPath c:\test\MY-NEW-VM.vhd -VHDType Fixed
```

W tym poleceniu Zastąp wartość `-Path` ścieżką do wirtualnego dysku twardego, który chcesz skonwertować. Zastąp wartość `-DestinationPath` nową ścieżką i nazwą konwertowanego dysku.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Konwertuj z formatu dysku VMware VMDK
Jeśli masz obraz maszyny wirtualnej z systemem Windows w [formacie VMDK](https://en.wikipedia.org/wiki/VMDK), użyj [konwertera maszyny wirtualnej firmy Microsoft](https://www.microsoft.com/download/details.aspx?id=42497) , aby przekonwertować go na format VHD. Aby uzyskać więcej informacji, zobacz [jak skonwertować dysk VHD programu VMware VMDK na funkcję Hyper-V](https://blogs.msdn.com/b/timomta/archive/2015/06/11/how-to-convert-a-vmware-vmdk-to-hyper-v-vhd.aspx).

## <a name="set-windows-configurations-for-azure"></a>Ustawianie konfiguracji systemu Windows na platformie Azure

> [!NOTE]
> Platforma Azure instaluje plik ISO na dysku DVD-ROM po utworzeniu maszyny wirtualnej z systemem Windows na podstawie uogólnionego obrazu.
> Z tego powodu dysk DVD-ROM musi być włączony w systemie operacyjnym w uogólnionym obrazie. Jeśli jest wyłączona, maszyna wirtualna z systemem Windows zostanie zablokowana.

Na maszynie wirtualnej, która ma zostać przekazana na platformę Azure, uruchom następujące polecenia w [oknie wiersza polecenia z podwyższonym poziomem uprawnień](https://technet.microsoft.com/library/cc947813.aspx):

1. Usuń każdą statyczną trasę trwałą w tabeli routingu:
   
   * Aby wyświetlić tabelę tras, uruchom `route print` polecenie w wierszu polecenia.
   * Sprawdź `Persistence Routes` sekcje. Jeśli istnieje trasa trwała, użyj `route delete` polecenia, aby je usunąć.
2. Usuń serwer proxy WinHTTP:
   
    ```PowerShell
    netsh winhttp reset proxy
    ```

    Jeśli maszyna wirtualna musi współpracować z określonym serwerem proxy, Dodaj wyjątek serwera proxy do adresu IP platformy Azure ([168.63.129.16](https://blogs.msdn.microsoft.com/mast/2015/05/18/what-is-the-ip-address-168-63-129-16/
)), aby maszyna wirtualna mogła nawiązać połączenie z platformą Azure:
    ```
    $proxyAddress="<your proxy server>"
    $proxyBypassList="<your list of bypasses>;168.63.129.16"

    netsh winhttp set proxy $proxyAddress $proxyBypassList
    ```

3. Ustaw [`Onlineall`](https://technet.microsoft.com/library/gg252636.aspx)następujące zasady sieci San:
   
    ```PowerShell
    diskpart 
    ```
    W otwartym oknie wiersza polecenia wpisz następujące polecenia:

     ```DISKPART
    san policy=onlineall
    exit   
    ```

4. Ustawianie czasu uniwersalnego czasu koordynowanego (UTC) dla systemu Windows. Należy również ustawić typ uruchamiania usługi czas systemu Windows (`w32time`) na: `Automatic`
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation' -Name "RealTimeIsUniversal" -Value 1 -Type DWord -Force

    Set-Service -Name w32time -StartupType Automatic
    ```
5. Ustaw profil zasilacza na wysoką wydajność:

    ```PowerShell
    powercfg /setactive SCHEME_MIN
    ```
6. Upewnij się, że zmienne `TEMP` środowiskowe i `TMP` są ustawione na wartości domyślne:

    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TEMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name "TMP" -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
    ```

## <a name="check-the-windows-services"></a>Sprawdzanie usług systemu Windows
Upewnij się, że dla każdej z poniższych usług systemu Windows ustawiono domyślne wartości systemu Windows. Te usługi są minimalne, które muszą zostać skonfigurowane w celu zapewnienia łączności z maszyną wirtualną. Aby zresetować ustawienia uruchamiania, uruchom następujące polecenia:
   
```PowerShell
Get-Service -Name bfe | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name dhcp | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name dnscache | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name IKEEXT | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name iphlpsvc | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name netlogon | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name netman | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name nsi | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name TermService | Where-Object { $_.StartType -ne 'Manual' } | Set-Service -StartupType 'Manual'
Get-Service -Name MpsSvc | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
Get-Service -Name RemoteRegistry | Where-Object { $_.StartType -ne 'Automatic' } | Set-Service -StartupType 'Automatic'
```
## <a name="update-remote-desktop-registry-settings"></a>Aktualizowanie ustawień rejestru zdalnego pulpitu
Upewnij się, że następujące ustawienia są poprawnie skonfigurowane dla dostępu zdalnego:

>[!NOTE] 
>Po uruchomieniu `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <object name> -Value <value>`programu może zostać wyświetlony komunikat o błędzie. Możesz bezpiecznie zignorować ten komunikat. Oznacza tylko, że domena nie wypychanie tej konfiguracji za pośrednictwem obiektu zasady grupy.

1. Remote Desktop Protocol (RDP) jest włączony:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDenyTSConnections" -Value 0 -Type DWord -Force
    ```
   
2. Port RDP został prawidłowo skonfigurowany. Domyślnym portem jest 3389:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "PortNumber" -Value 3389 -Type DWord -Force
    ```
    Podczas wdrażania maszyny wirtualnej reguły domyślne są tworzone na podstawie portu 3389. Jeśli chcesz zmienić numer portu, zrób to po wdrożeniu maszyny wirtualnej na platformie Azure.

3. Odbiornik nasłuchuje w każdym interfejsie sieciowym:
   
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "LanAdapter" -Value 0 -Type DWord -Force
   ```
4. Skonfiguruj tryb uwierzytelniania na poziomie sieci (NLA) dla połączeń RDP:
   
    ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "UserAuthentication" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SecurityLayer" -Value 1 -Type DWord -Force

    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "fAllowSecProtocolNegotiation" -Value 1 -Type DWord -Force
     ```

5. Ustaw wartość w polu Keep-Alive:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveEnable" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "KeepAliveInterval" -Value 1  -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "KeepAliveTimeout" -Value 1 -Type DWord -Force
    ```
6. Połącz ponownie
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name "fDisableAutoReconnect" -Value 0 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fInheritReconnectSame" -Value 1 -Type DWord -Force
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "fReconnectSame" -Value 0 -Type DWord -Force
    ```
7. Ogranicz liczbę jednoczesnych połączeń:
    
    ```PowerShell
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name "MaxInstanceCount" -Value 4294967295 -Type DWord -Force
    ```
8. Usuń wszystkie certyfikaty z podpisem własnym powiązane z odbiornikiem RDP:
    
    ```PowerShell
    if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains "SSLCertificateSHA1Hash")
    {
        Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SSLCertificateSHA1Hash" -Force
    }
    ```
    Ten kod umożliwia nawiązanie połączenia na początku podczas wdrażania maszyny wirtualnej. Jeśli chcesz przejrzeć to później, możesz to zrobić po wdrożeniu maszyny wirtualnej na platformie Azure.

9. Jeśli maszyna wirtualna będzie częścią domeny, sprawdź następujące zasady, aby upewnić się, że poprzednie ustawienia nie są przywrócone. 
    
    | Cel                                     | Zasady                                                                                                                                                       | Wartość                                                                                    |
    |------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|
    | Włączono protokół RDP                           | Computer komputera\Zasady\Ustawienia Settings\Administrative Templates\Components\Remote Desktop Zdalnego\host sesji pulpitu Host\Connections         | Zezwalaj użytkownikom na zdalne nawiązywanie połączenia przy użyciu Pulpit zdalny                                  |
    | Zasady grupy NLA                         | Settings\Administrative Templates\Components\Remote Desktop Zdalnego\host sesji pulpitu Host\Security                                                    | Wymagaj uwierzytelniania użytkownika na potrzeby dostępu zdalnego przy użyciu usługi NLA |
    | Ustawienia utrzymywania aktywności                      | Komputer komputera\Zasady\Ustawienia Settings\Administrative administracyjne \ składniki systemu \ pulpit Zdalnego\host sesji pulpitu Host\Connections | Konfigurowanie interwału połączenia Keep-Alive                                                 |
    | Ustawienia ponownego połączenia                       | Komputer komputera\Zasady\Ustawienia Settings\Administrative administracyjne \ składniki systemu \ pulpit Zdalnego\host sesji pulpitu Host\Connections | Połącz automatycznie ponownie                                                                   |
    | Ograniczona liczba ustawień połączenia | Komputer komputera\Zasady\Ustawienia Settings\Administrative administracyjne \ składniki systemu \ pulpit Zdalnego\host sesji pulpitu Host\Connections | Ogranicz liczbę połączeń                                                              |

## <a name="configure-windows-firewall-rules"></a>Konfigurowanie reguł zapory systemu Windows
1. Włącz Zaporę systemu Windows na trzech profilach (domena, standardowa i publiczna):

   ```PowerShell
    Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled True
   ```

2. Uruchom następujące polecenie w programie PowerShell, aby umożliwić usłudze WinRM przez trzy profile zapory (domena, prywatny i publiczny), a następnie Włącz usługę zdalną programu PowerShell:
   
   ```PowerShell
    Enable-PSRemoting -Force

    Set-NetFirewallRule -DisplayName "Windows Remote Management (HTTP-In)" -Enabled True
   ```
3. Włącz następujące reguły zapory, aby zezwolić na ruch RDP:

   ```PowerShell
    Set-NetFirewallRule -DisplayGroup "Remote Desktop" -Enabled True
   ```   
4. Włącz regułę udostępniania plików i drukarek, aby maszyna wirtualna mogła odpowiedzieć na polecenie ping w sieci wirtualnej:

   ```PowerShell
   Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -Enabled True
   ``` 
5. Utwórz regułę dla sieci platformy Azure:

   ```PowerShell
    New-NetFirewallRule -DisplayName "AzurePlatform" -Direction Inbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow -EdgeTraversalPolicy Allow
    New-NetFirewallRule -DisplayName "AzurePlatform" -Direction Outbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow
   ``` 
6. Jeśli maszyna wirtualna będzie częścią domeny, sprawdź następujące zasady usługi Azure AD, aby upewnić się, że poprzednie ustawienia nie zostaną przywrócone. 

    | Cel                                 | Zasady                                                                                                                                                  | Wartość                                   |
    |--------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------|
    | Włączanie profilów zapory systemu Windows | Computer komputera\Zasady\Ustawienia Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows firewall   | Ochrona wszystkich połączeń sieciowych         |
    | Włącz protokół RDP                           | Computer komputera\Zasady\Ustawienia Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows firewall   | Zezwalaj na wyjątki Pulpit zdalny dla ruchu przychodzącego |
    |                                      | Computer komputera\Zasady\Ustawienia Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows firewall | Zezwalaj na wyjątki Pulpit zdalny dla ruchu przychodzącego |
    | Włącz protokół ICMP-v4                       | Computer komputera\Zasady\Ustawienia Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows firewall   | Zezwalaj na wyjątki protokołu ICMP                   |
    |                                      | Computer komputera\Zasady\Ustawienia Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows firewall | Zezwalaj na wyjątki protokołu ICMP                   |

## <a name="verify-the-vm"></a>Weryfikowanie maszyny wirtualnej 

Upewnij się, że maszyna wirtualna jest w dobrej kondycji, bezpieczna i RDP: 

1. Aby upewnić się, że dysk jest w dobrej kondycji i jest spójny, sprawdź, czy dysk przy następnym ponownym uruchomieniu maszyny wirtualnej:

    ```PowerShell
    Chkdsk /f
    ```
    Upewnij się, że raport zawiera dysk czyste i o dobrej kondycji.

2. Ustaw ustawienia Dane konfiguracji rozruchu (BCD). 

    > [!NOTE]
    > Użyj okna programu PowerShell z podwyższonym poziomem uprawnień, aby uruchomić te polecenia.
   
   ```powershell
    bcdedit /set "{bootmgr}" integrityservices enable
    bcdedit /set "{default}" device partition=C:
    bcdedit /set "{default}" integrityservices enable
    bcdedit /set "{default}" recoveryenabled Off
    bcdedit /set "{default}" osdevice partition=C:
    bcdedit /set "{default}" bootstatuspolicy IgnoreAllFailures

    #Enable Serial Console Feature
    bcdedit /set "{bootmgr}" displaybootmenu yes
    bcdedit /set "{bootmgr}" timeout 5
    bcdedit /set "{bootmgr}" bootems yes
    bcdedit /ems "{current}" ON
    bcdedit /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```
3. Dziennik zrzutu może być przydatny podczas rozwiązywania problemów z awarią systemu Windows. Włącz zbieranie dzienników zrzutów:

    ```powershell
    # Set up the guest OS to collect a kernel dump on an OS crash event
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name CrashDumpEnabled -Type DWord -Force -Value 2
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name DumpFile -Type ExpandString -Force -Value "%SystemRoot%\MEMORY.DMP"
    Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name NMICrashDump -Type DWord -Force -Value 1

    # Set up the guest OS to collect user mode dumps on a service crash event
    $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
    if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
    New-ItemProperty -Path $key -Name DumpFolder -Type ExpandString -Force -Value "c:\CrashDumps"
    New-ItemProperty -Path $key -Name CrashCount -Type DWord -Force -Value 10
    New-ItemProperty -Path $key -Name DumpType -Type DWord -Force -Value 2
    Set-Service -Name WerSvc -StartupType Manual
    ```
4. Sprawdź, czy repozytorium Instrumentacja zarządzania Windows (WMI) jest spójne:

    ```PowerShell
    winmgmt /verifyrepository
    ```
    Jeśli repozytorium jest uszkodzone, zobacz [WMI: uszkodzenie repozytorium](https://blogs.technet.microsoft.com/askperf/2014/08/08/wmi-repository-corruption-or-not).

5. Upewnij się, że żadna inna aplikacja nie korzysta z portu 3389. Ten port jest używany dla usługi RDP na platformie Azure. Aby sprawdzić, które porty są używane na maszynie wirtualnej, `netstat -anob`Uruchom polecenie:

    ```PowerShell
    netstat -anob
    ```

6. Aby przekazać wirtualny dysk twardy z systemem Windows, który jest kontrolerem domeny:

   * Postępuj zgodnie z [tymi dodatkowymi krokami](https://support.microsoft.com/kb/2904015) , aby przygotować dysk.

   * Upewnij się, że znasz hasło trybu przywracania usług katalogowych (DSRM) w przypadku, gdy musisz uruchomić maszynę wirtualną w trybie DSRM w pewnym momencie. Aby uzyskać więcej informacji, zobacz [Ustawianie hasła DSRM](https://technet.microsoft.com/library/cc754363(v=ws.11).aspx).

7. Upewnij się, że znasz wbudowane konto administratora i hasło. Może zajść potrzeba zresetowania bieżącego hasła administratora lokalnego i upewnienia się, że można użyć tego konta do logowania się do systemu Windows za pomocą połączenia RDP. To uprawnienie dostępu jest kontrolowane przez obiekt zasady grupy "Zezwalaj na logowanie za pomocą Usługi pulpitu zdalnego". Wyświetl ten obiekt w Edytor lokalnych zasad grupy tutaj:

    Komputer Komputera\ustawienia systemu Windows\Ustawienia zabezpieczeń \ zasady Lokalne\przypisanie prawa

8. Sprawdź następujące zasady usługi Azure AD, aby upewnić się, że nie blokujesz dostępu do protokołu RDP za pośrednictwem protokołu RDP lub sieci:

    - Komputer Komputera\ustawienia systemu Windows\Ustawienia zabezpieczeń \ zasady Lokalne\przypisanie Rights odmowa dostęp do tego komputera z sieci

    - Komputer Komputera\ustawienia systemu Windows\Ustawienia zabezpieczeń \ zasady Lokalne\przypisanie Rights odmowa logowanie za Usługi pulpitu zdalnego


9. Sprawdź następujące zasady usługi Azure AD, aby upewnić się, że nie są usuwane żadne z wymaganych kont dostępu:

   - Komputer Komputera\ustawienia systemu Windows\Ustawienia zabezpieczeń \ zasady Lokalne\przypisanie, Assignment\Access ten komputer z sieci

   Zasady powinny być wymienione w następujących grupach:

   - Administratorzy

   - Operatorzy kopii zapasowych

   - Wszyscy

   - Użytkownicy

10. Uruchom ponownie maszynę wirtualną, aby upewnić się, że system Windows jest nadal w dobrej kondycji i można go połączyć za pomocą połączenia RDP. W tym momencie możesz chcieć utworzyć maszynę wirtualną w lokalnej funkcji Hyper-V, aby upewnić się, że maszyna wirtualna jest uruchamiana w całości. Następnie przetestuj go, aby upewnić się, że można uzyskać dostęp do maszyny wirtualnej za pomocą protokołu RDP.

11. Usuń wszystkie filtry interfejsu dodatkowego sterownika transportu (TDI). Na przykład Usuń oprogramowanie, które analizuje pakiety TCP lub dodatkowe zapory. Jeśli chcesz przejrzeć to później, możesz to zrobić po wdrożeniu maszyny wirtualnej na platformie Azure.

12. Odinstaluj wszelkie inne oprogramowanie lub sterowniki innej firmy, które są powiązane ze składnikami fizycznymi lub innymi technologiami wirtualizacji.

### <a name="install-windows-updates"></a>Zainstaluj aktualizacje systemu Windows
W idealnym przypadku należy zachować aktualizację komputera na *poziomie poprawki*. Jeśli to nie jest możliwe, upewnij się, że są zainstalowane następujące aktualizacje. Aby uzyskać najnowsze aktualizacje, zobacz strony historii systemu Windows: [Windows 10 i Windows server 2019](https://support.microsoft.com/help/4000825), [Windows 8.1 i Windows Server 2012 R2](https://support.microsoft.com/help/4009470) oraz [Windows 7 z dodatkiem SP1 i Windows Server 2008 R2 SP1](https://support.microsoft.com/help/4009469).

| Składnik               | plików binarnych         | Windows 7 z dodatkiem SP1, Windows Server 2008 R2 z dodatkiem SP1 | Windows 8, Windows Server 2012               | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 | V1703 systemu Windows 10    | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
|-------------------------|----------------|-------------------------------------------|---------------------------------------------|------------------------------------|---------------------------------------------------------|----------------------------|-------------------------------------------------|-------------------------------------------------|
| Magazyn                 | dysk. sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061         | -                                                       | -                          | -                                               | -                                               |
|                         | Storport. sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.332             | -                                               | -                                               |
|                         | NTFS. sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | Iologmsg. dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Classpnp. sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614         | 10.0.14393.953 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Volsnap. sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384         | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | partmgr. sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 — KB3000850         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgr. sys     |                                           |                                             |                                    |                                                         | 10.0.15063.0               | -                                               | -                                               |
|                         | Volmgrx. sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | 10.0.15063.0               | -                                               | -                                               |
|                         | Msiscsi. sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726         | 10.0.14393.1066 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | MSDSM. sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726         | -                                                       | -                          | -                                               | -                                               |
|                         | MPIO. sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726         | 10.0.14393.1198 - KB4022715                             | -                          | -                                               | -                                               |
|                         | vmstorfl. sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650         | 10.0.14393.2007 - KB4345418                             | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                      | -                                               |
|                         | Fveapi. dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614         | 10.0.14393.576 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Fveapibase. dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614         | 10.0.14393.206 - KB4022715                              | -                          | -                                               | -                                               |
| Sieć                 | netvsc. sys     | -                                         | -                                           | -                                  | 10.0.14393.1198 - KB4022715                             | 10.0.15063.250 - KB4020001 | -                                               | -                                               |
|                         | Mrxsmb10. sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726         | 10.0.14393.479 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | Mrxsmb20. sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | Mrxsmb. sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726         | 10.0.14393.953 - KB4022715                              | 10.0.15063.0               | -                                               | -                                               |
|                         | tcpip. sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.447             | -                                               | -                                               |
|                         | http. sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726         | 10.0.14393.251 - KB4022715                              | 10.0.15063.483             | -                                               | -                                               |
|                         | w pliku. sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.138             | -                                               | -                                               |
| Podstawowe                    | ntoskrnl. exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726         | 10.0.14393.1358 - KB4022715                             | 10.0.15063.483             | -                                               | -                                               |
| Usługi pulpitu zdalnego | rdpcorets. dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726         | 10.0.14393.1198 - KB4022715                             | 10.0.15063.0               | -                                               | -                                               |
|                         | termsrv. dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 — KB3000850         | 10.0.14393.0 — KB4022715                                | 10.0.15063.0               | -                                               | -                                               |
|                         | TermDD. sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726         | 10.0.14393.594 - KB4022715                              | -                          | -                                               | -                                               |
|                         | Rdpdd. dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
|                         | Rdpwd. sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                  | -                                                       | -                          | -                                               | -                                               |
| Zabezpieczenia                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                          | KB4012606                                               | KB4012606                  | -                                               | -                                               |
|                         |                |                                           | KB4012216                                   |                                    | KB4013198                                               | KB4013198                  | -                                               | -                                               |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                          | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         |                |                                           | KB4012217                                   |                                    | KB4013429                                               | KB4013429                  | -                                               | -                                               |
|                         | CVE-2018-0886  | KB4103718               | KB4103730                | KB4103725       | KB4103723                                               | KB4103731                  | KB4103727                                       | KB4103721                                       |
|                         |                | KB4103712          | KB4103726          | KB4103715|                                                         |                            |                                                 |                                                 |
       
> [!NOTE]
> Aby uniknąć przypadkowego ponownego uruchomienia podczas aprowizacji maszyny wirtualnej, zalecamy upewnienie się, że wszystkie instalacje Windows Update zostały zakończone i że nie ma żadnych oczekujących aktualizacji. Jednym z nich jest zainstalowanie wszystkich możliwych aktualizacji systemu Windows i ponowne uruchomienie komputera przed uruchomieniem polecenia Sysprep.

### <a name="determine-when-to-use-sysprep"></a>Ustalanie, kiedy należy używać narzędzia Sysprep<a id="step23"></a>    

Narzędzie przygotowywania systemu (Sysprep) to proces, który można uruchomić w celu zresetowania instalacji systemu Windows. Program Sysprep udostępnia "środowisko pracy", usuwając wszystkie dane osobowe i instalując kilka składników. 

Zazwyczaj program Sysprep jest uruchamiany w celu utworzenia szablonu, na podstawie którego można wdrożyć kilka innych maszyn wirtualnych z określoną konfiguracją. Szablon jest nazywany *uogólnionym obrazem*.

Jeśli chcesz utworzyć tylko jedną maszynę wirtualną z jednego dysku, nie musisz używać narzędzia Sysprep. Zamiast tego można utworzyć maszynę wirtualną na podstawie *wyspecjalizowanego obrazu*. Aby uzyskać informacje dotyczące sposobu tworzenia maszyny wirtualnej na podstawie wyspecjalizowanego dysku, zobacz:

- [Tworzenie maszyny wirtualnej na podstawie wyspecjalizowanego dysku](create-vm-specialized.md)
- [Tworzenie maszyny wirtualnej na podstawie wyspecjalizowanego dysku VHD](https://docs.microsoft.com/azure/virtual-machines/windows/create-vm-specialized-portal?branch=master)

Jeśli chcesz utworzyć uogólniony obraz, musisz uruchomić program Sysprep. Aby uzyskać więcej informacji, zobacz [jak używać narzędzia Sysprep: wprowadzenie](https://technet.microsoft.com/library/bb457073.aspx). 

Nie każda rola lub aplikacja zainstalowana na komputerze z systemem Windows obsługuje uogólnione obrazy. Dlatego przed wykonaniem tej procedury upewnij się, że program Sysprep obsługuje rolę komputera. Aby uzyskać więcej informacji, zobacz [Obsługa programu Sysprep dla ról serwera](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).

### <a name="generalize-a-vhd"></a>Uogólnianie wirtualnego dysku twardego

>[!NOTE]
> Po uruchomieniu wykonaj `sysprep.exe` następujące kroki, Wyłącz maszynę wirtualną. Nie włączaj jej ponownie, dopóki nie utworzysz obrazu na platformie Azure.

1. Zaloguj się do maszyny wirtualnej z systemem Windows.
1. Uruchom **wiersz polecenia** jako administrator. 
1. Zmień katalog na `%windir%\system32\sysprep`. Następnie należy uruchomić polecenie `sysprep.exe`.
1. W oknie dialogowym **Narzędzie przygotowywania systemu** wybierz pozycję **Włącz systemowy tryb OOBE** i upewnij się, że pole wyboru **Uogólnij** jest zaznaczone.

    ![Narzędzie przygotowywania systemu](media/prepare-for-upload-vhd-image/syspre.png)
1. W obszarze **Opcje zamykania**wybierz pozycję **Zamknij**.
1. Kliknij przycisk **OK**.
1. Po zakończeniu działania narzędzia Sysprep Zamknij maszynę wirtualną. Nie używaj **ponownego uruchamiania** , aby zamknąć maszynę wirtualną.

Teraz dysk VHD jest gotowy do przekazania. Aby uzyskać więcej informacji na temat sposobu tworzenia maszyny wirtualnej na podstawie uogólnionego dysku, zobacz [przekazywanie uogólnionego wirtualnego dysku twardego i używanie go do tworzenia nowej maszyny wirtualnej na platformie Azure](sa-upload-generalized.md).


>[!NOTE]
> Niestandardowy plik *Unattend. XML* nie jest obsługiwany. Chociaż obsługujemy `additionalUnattendContent` właściwość, która zapewnia tylko ograniczoną obsługę dodawania opcji [Instalatora Microsoft-Windows-Shell-Setup](https://docs.microsoft.com/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) do pliku *Unattend. XML* , który jest wykorzystywany przez agenta aprowizacji platformy Azure. Aby dodać FirstLogonCommands i LogonCommands, można użyć, na przykład [additionalUnattendContent](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent?view=azure-dotnet) . Aby uzyskać więcej informacji, zobacz [AdditionalUnattendContent FirstLogonCommands example](https://github.com/Azure/azure-quickstart-templates/issues/1407).


## <a name="complete-the-recommended-configurations"></a>Wykonaj zalecane konfiguracje
Następujące ustawienia nie wpływają na przekazywanie wirtualnego dysku twardego. Zdecydowanie zaleca się jednak ich skonfigurowanie.

* Zainstaluj [agenta maszyny wirtualnej platformy Azure](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Następnie można włączyć rozszerzenia maszyn wirtualnych. Rozszerzenia maszyn wirtualnych implementują większość krytycznych funkcji, których można chcieć używać z maszynami wirtualnymi. Wymagane są rozszerzenia, na przykład w celu resetowania haseł lub konfigurowania protokołu RDP. Aby uzyskać więcej informacji, zobacz [Omówienie agenta maszyny wirtualnej platformy Azure](../extensions/agent-windows.md).
* Po utworzeniu maszyny wirtualnej na platformie Azure Zalecamy umieszczenie pliku stronicowania na *woluminie dysków* czasowych, aby zwiększyć wydajność. Umieszczanie plików można skonfigurować w następujący sposób:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name "PagingFiles" -Value "D:\pagefile.sys" -Type MultiString -Force
   ```
  Jeśli dysk danych jest dołączony do maszyny wirtualnej, litera woluminu dysku czasowego jest zwykle *D*. To oznaczenie może różnić się w zależności od ustawień i liczby dostępnych dysków.
  * Zalecamy wyłączenie blokowania skryptów, które mogą być dostarczane przez oprogramowanie antywirusowe. Mogą one zakłócać i blokować skrypty agenta aprowizacji systemu Windows wykonywane podczas wdrażania nowej maszyny wirtualnej z obrazu.
  
## <a name="next-steps"></a>Następne kroki
* [Przekazywanie obrazu maszyny wirtualnej z systemem Windows na platformę Azure w celu wdrożenia Menedżer zasobów](upload-generalized-managed.md)
* [Rozwiązywanie problemów z aktywacją maszyn wirtualnych systemu Windows Azure](troubleshoot-activation-problems.md)

