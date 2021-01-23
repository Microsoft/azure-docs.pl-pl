---
title: Przygotowywanie wirtualnego dysku twardego systemu Windows do przekazania do platformy Azure
description: Dowiedz się, jak przygotować plik VHD lub VHDX systemu Windows w celu przekazania go do platformy Azure
author: glimoli
manager: dcscontentpm
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 09/02/2020
ms.author: genli
ms.openlocfilehash: e409211c167f7b29128faf9fdfc02aa5c0a7d0e3
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98736258"
---
# <a name="prepare-a-windows-vhd-or-vhdx-to-upload-to-azure"></a>Przygotowywanie dysku VHD lub VHDX systemu Windows do przekazania na platformę Azure

Przed przekazaniem maszyny wirtualnej z systemem Windows z lokalizacji lokalnej do platformy Azure należy przygotować wirtualny dysk twardy (VHD lub VHDX). Platforma Azure obsługuje maszyny wirtualne generacji 1 i 2, które są w formacie pliku VHD i mają dysk o stałym rozmiarze. Maksymalny dozwolony rozmiar wirtualnego dysku twardego systemu operacyjnego na maszynie wirtualnej generacji 1 to 2 TB.

Plik VHDX można skonwertować na dysk VHD, konwertując dynamicznie powiększający dysk na dysk o stałym rozmiarze, ale nie można zmienić generacji maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz temat [Tworzenie maszyny wirtualnej generacji 1 lub 2 w funkcji Hyper-V?](/windows-server/virtualization/hyper-v/plan/Should-I-create-a-generation-1-or-2-virtual-machine-in-Hyper-V) i [Obsługa maszyn wirtualnych 2. generacji na platformie Azure](../generation-2.md).

Aby uzyskać informacje o zasadach pomocy technicznej dla maszyn wirtualnych platformy Azure, zobacz [Microsoft Server Software Support for Azure](https://support.microsoft.com/help/2721672/)Virtual Machines.

> [!NOTE]
> Instrukcje zawarte w tym artykule dotyczą:
>
> - 64-bitowa wersja systemu Windows Server 2008 R2 i nowszych systemów operacyjnych Windows Server. Aby uzyskać informacje o uruchamianiu 32-bitowego systemu operacyjnego na platformie Azure, zobacz [obsługa 32-bitowych systemów operacyjnych na maszynach wirtualnych platformy Azure](https://support.microsoft.com/help/4021388/).
> - Jeśli do migracji obciążenia, takiego jak Azure Site Recovery lub Azure Migrate, zostanie użyte dowolne narzędzie do odzyskiwania po awarii, ten proces jest nadal wymagany w systemie operacyjnym gościa w celu przygotowania obrazu przed rozpoczęciem migracji.

## <a name="system-file-checker"></a> narzędzie sprawdzania plików systemowych

### <a name="run-windows-system-file-checker-utility-before-generalization-of-os-image"></a>Uruchom narzędzie sprawdzania plików systemu Windows przed uogólnieniem obrazu systemu operacyjnego

Program System File Checker (SFC) służy do weryfikowania i zastępowania plików systemu Windows.

> [!IMPORTANT]
> Użyj sesji programu PowerShell z podwyższonym poziomem uprawnień, aby uruchomić przykłady w tym artykule.

Uruchom polecenie SFC:

```powershell
sfc.exe /scannow
```

```Output
Beginning system scan.  This process will take some time.

Beginning verification phase of system scan.
Verification 100% complete.

Windows Resource Protection did not find any integrity violations.
```

Po zakończeniu skanowania programu SFC Zainstaluj aktualizacje systemu Windows i uruchom ponownie komputer.

## <a name="set-windows-configurations-for-azure"></a>Ustawianie konfiguracji systemu Windows na platformie Azure

> [!NOTE]
> Platforma Azure instaluje plik ISO na dysku DVD-ROM po utworzeniu maszyny wirtualnej z systemem Windows na podstawie uogólnionego obrazu. Z tego powodu dysk DVD-ROM musi być włączony w systemie operacyjnym w uogólnionym obrazie. Jeśli jest wyłączona, maszyna wirtualna z systemem Windows zostanie zablokowana w środowisku OOBE (out-of-Box Experience).

1. Usuń wszystkie statyczne trasy trwałe w tabeli routingu:

   - Aby wyświetlić tabelę routingu, uruchom polecenie `route.exe print` .
   - Zapoznaj się z sekcją **trasy trwałości** . Jeśli istnieje trasa trwała, użyj polecenia, `route.exe delete` aby je usunąć.

1. Usuń serwer proxy WinHTTP:

   ```powershell
   netsh.exe winhttp reset proxy
   ```

    Jeśli maszyna wirtualna musi pracować z określonym serwerem proxy, Dodaj wyjątek serwera proxy dla adresu IP platformy Azure ([168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)), aby maszyna wirtualna mogła nawiązać połączenie z platformą Azure:

    ```
    $proxyAddress='<your proxy server>'
    $proxyBypassList='<your list of bypasses>;168.63.129.16'
    netsh.exe winhttp set proxy $proxyAddress $proxyBypassList
    ```

1. Otwórz narzędzie DiskPart:

   ```powershell
   diskpart.exe
   ```

   Ustaw następujące zasady sieci SAN [`Onlineall`](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/gg252636(v=ws.11)) :

   ```DiskPart
   DISKPART> san policy=onlineall
   DISKPART> exit
   ```

1. Ustawianie czasu uniwersalnego czasu koordynowanego (UTC) dla systemu Windows. Ponadto ustaw typ uruchomienia usługi czas systemu Windows **W32Time** na **automatyczny**:

   ```powershell
   Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Control\TimeZoneInformation -Name RealTimeIsUniversal -Value 1 -Type DWord -Force
   Set-Service -Name w32time -StartupType Automatic
   ```

1. Ustaw profil zasilacza na wysoką wydajność:

   ```powershell
   powercfg.exe /setactive SCHEME_MIN
   ```

1. Upewnij się, że zmienne środowiskowe **temp** i **tmp** są ustawione na wartości domyślne:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name TEMP -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Environment' -Name TMP -Value "%SystemRoot%\TEMP" -Type ExpandString -Force
   ```

## <a name="check-the-windows-services"></a>Sprawdzanie usług systemu Windows

Upewnij się, że dla każdej z poniższych usług systemu Windows ustawiono wartość domyślną systemu Windows. Te usługi są minimalne, które muszą być skonfigurowane w celu zapewnienia łączności maszyn wirtualnych. Aby ustawić ustawienia uruchamiania, uruchom następujący przykład:

```powershell
Get-Service -Name BFE, Dhcp, Dnscache, IKEEXT, iphlpsvc, nsi, mpssvc, RemoteRegistry |
  Where-Object StartType -ne Automatic |
    Set-Service -StartupType Automatic

Get-Service -Name Netlogon, Netman, TermService |
  Where-Object StartType -ne Manual |
    Set-Service -StartupType Manual
```

## <a name="update-remote-desktop-registry-settings"></a>Aktualizowanie ustawień rejestru usług pulpitu zdalnego

Upewnij się, że następujące ustawienia są poprawnie skonfigurowane dla dostępu zdalnego:

> [!NOTE]
> Jeśli podczas uruchamiania zostanie wyświetlony komunikat o błędzie `Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services -Name <string> -Value <object>` , możesz go bezpiecznie zignorować. Oznacza to, że domena nie ustawia tej konfiguracji za pośrednictwem obiektu zasady grupy.

1. Remote Desktop Protocol (RDP) jest włączony:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server' -Name fDenyTSConnections -Value 0 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name fDenyTSConnections -Value 0 -Type DWord -Force
   ```

1. Port RDP jest prawidłowo skonfigurowany przy użyciu domyślnego portu 3389:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name PortNumber -Value 3389 -Type DWord -Force
   ```

   Podczas wdrażania maszyny wirtualnej są tworzone reguły domyślne dla portu 3389. Aby zmienić numer portu, zrób to po wdrożeniu maszyny wirtualnej na platformie Azure.

1. Odbiornik nasłuchuje na każdym interfejsie sieciowym:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name LanAdapter -Value 0 -Type DWord -Force
   ```

1. Skonfiguruj tryb uwierzytelniania na poziomie sieci (NLA) dla połączeń RDP:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name UserAuthentication -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name SecurityLayer -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name fAllowSecProtocolNegotiation -Value 1 -Type DWord -Force
   ```

1. Ustaw wartość w polu Keep-Alive:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name KeepAliveEnable -Value 1  -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name KeepAliveInterval -Value 1  -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name KeepAliveTimeout -Value 1 -Type DWord -Force
   ```

1. Ustaw opcje ponownego połączenia:

   ```PowerShell
   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services' -Name fDisableAutoReconnect -Value 0 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name fInheritReconnectSame -Value 1 -Type DWord -Force
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name fReconnectSame -Value 0 -Type DWord -Force
   ```

1. Ogranicz liczbę jednoczesnych połączeń:

   ```powershell
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\Winstations\RDP-Tcp' -Name MaxInstanceCount -Value 4294967295 -Type DWord -Force
   ```

1. Usuń wszystkie certyfikaty z podpisem własnym powiązane z odbiornikiem RDP:

   ```powershell
   if ((Get-Item -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp').Property -contains 'SSLCertificateSHA1Hash')
   {
       Remove-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name SSLCertificateSHA1Hash -Force
   }
   ```

   Ten kod gwarantuje, że podczas wdrażania maszyny wirtualnej można nawiązać połączenie. Te ustawienia można także sprawdzić po wdrożeniu maszyny wirtualnej na platformie Azure.

1. Jeśli maszyna wirtualna jest częścią domeny, sprawdź następujące zasady, aby upewnić się, że poprzednie ustawienia nie zostaną przywrócone.

    |                 Cel                  |                                                                            Zasady                                                                            |                           Wartość                            |
    | ------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------- |
    | Włączono protokół RDP                        | Computer komputera\Zasady\Ustawienia Settings\Administrative Templates\Components\Remote Desktop Zdalnego\host sesji pulpitu Host\Connections         | Zezwalaj użytkownikom na zdalne nawiązywanie połączenia przy użyciu Pulpit zdalny    |
    | Zasady grupy NLA                      | Settings\Administrative Templates\Components\Remote Desktop Zdalnego\host sesji pulpitu Host\Security                                                    | Wymagaj uwierzytelniania użytkownika na potrzeby dostępu zdalnego przy użyciu usługi NLA |
    | Ustawienia utrzymywania aktywności                   | Komputer komputera\Zasady\Ustawienia Settings\Administrative administracyjne \ składniki systemu \ pulpit Zdalnego\host sesji pulpitu Host\Connections | Konfigurowanie interwału połączenia Keep-Alive                   |
    | Ustawienia ponownego połączenia                    | Komputer komputera\Zasady\Ustawienia Settings\Administrative administracyjne \ składniki systemu \ pulpit Zdalnego\host sesji pulpitu Host\Connections | Połącz automatycznie ponownie                                    |
    | Ograniczona liczba ustawień połączenia | Komputer komputera\Zasady\Ustawienia Settings\Administrative administracyjne \ składniki systemu \ pulpit Zdalnego\host sesji pulpitu Host\Connections | Ogranicz liczbę połączeń                                |

## <a name="configure-windows-firewall-rules"></a>Konfigurowanie reguł zapory systemu Windows

1. Włącz Zaporę systemu Windows na trzech profilach (domena, standardowa i publiczna):

   ```powershell
   Set-NetFirewallProfile -Profile Domain, Public, Private -Enabled True
   ```

1. Uruchom Poniższy przykład, aby zezwolić usłudze WinRM przez trzy profile zapory (domena, prywatny i publiczny), a następnie Włącz usługę zdalną programu PowerShell:

   ```powershell
   Enable-PSRemoting -Force
   Set-NetFirewallRule -DisplayName 'Windows Remote Management (HTTP-In)' -Enabled True
   ```

1. Włącz następujące reguły zapory, aby zezwolić na ruch RDP:

   ```powershell
   Set-NetFirewallRule -DisplayGroup 'Remote Desktop' -Enabled True
   ```

1. Włącz regułę udostępniania plików i drukarek, aby maszyna wirtualna mogła reagować na żądania ping w sieci wirtualnej:

   ```powershell
   Set-NetFirewallRule -DisplayName 'File and Printer Sharing (Echo Request - ICMPv4-In)' -Enabled True
   ```

1. Utwórz regułę dla sieci platformy Azure:

   ```powershell
   New-NetFirewallRule -DisplayName AzurePlatform -Direction Inbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow -EdgeTraversalPolicy Allow
   New-NetFirewallRule -DisplayName AzurePlatform -Direction Outbound -RemoteAddress 168.63.129.16 -Profile Any -Action Allow
   ```

1. Jeśli maszyna wirtualna jest częścią domeny, sprawdź następujące zasady usługi Azure AD, aby upewnić się, że poprzednie ustawienia nie zostaną przywrócone.

    |                 Cel                 |                                                                         Zasady                                                                          |                  Wartość                  |
    | ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------- |
    | Włączanie profilów zapory systemu Windows | Computer komputera\Zasady\Ustawienia Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows firewall   | Ochrona wszystkich połączeń sieciowych         |
    | Włącz protokół RDP                           | Computer komputera\Zasady\Ustawienia Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows firewall   | Zezwalaj na wyjątki Pulpit zdalny dla ruchu przychodzącego |
    |                                      | Computer komputera\Zasady\Ustawienia Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows firewall | Zezwalaj na wyjątki Pulpit zdalny dla ruchu przychodzącego |
    | Włącz protokół ICMP-v4                       | Computer komputera\Zasady\Ustawienia Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Domain Profile\Windows firewall   | Zezwalaj na wyjątki protokołu ICMP                   |
    |                                      | Computer komputera\Zasady\Ustawienia Settings\Administrative Templates\Network\Network Connection\Windows Firewall\Standard Profile\Windows firewall | Zezwalaj na wyjątki protokołu ICMP                   |

## <a name="verify-the-vm"></a>Weryfikowanie maszyny wirtualnej

Upewnij się, że maszyna wirtualna jest w dobrej kondycji, bezpieczna i RDP:

1. Aby upewnić się, że dysk jest w dobrej kondycji i jest spójny, sprawdź, czy dysk przy następnym ponownym uruchomieniu maszyny wirtualnej:

   ```powershell
   chkdsk.exe /f
   ```

   Upewnij się, że raport zawiera dysk czyste i o dobrej kondycji.

1. Ustaw ustawienia Dane konfiguracji rozruchu (BCD).

   ```powershell
   bcdedit.exe /set "{bootmgr}" integrityservices enable
   bcdedit.exe /set "{default}" device partition=C:
   bcdedit.exe /set "{default}" integrityservices enable
   bcdedit.exe /set "{default}" recoveryenabled Off
   bcdedit.exe /set "{default}" osdevice partition=C:
   bcdedit.exe /set "{default}" bootstatuspolicy IgnoreAllFailures

   #Enable Serial Console Feature
   bcdedit.exe /set "{bootmgr}" displaybootmenu yes
   bcdedit.exe /set "{bootmgr}" timeout 5
   bcdedit.exe /set "{bootmgr}" bootems yes
   bcdedit.exe /ems "{current}" ON
   bcdedit.exe /emssettings EMSPORT:1 EMSBAUDRATE:115200
   ```

1. Dziennik zrzutu może być przydatny podczas rozwiązywania problemów z awarią systemu Windows. Włącz zbieranie dzienników zrzutów:

   ```powershell
   # Set up the guest OS to collect a kernel dump on an OS crash event
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name CrashDumpEnabled -Type DWord -Force -Value 2
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name DumpFile -Type ExpandString -Force -Value "%SystemRoot%\MEMORY.DMP"
   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\CrashControl' -Name NMICrashDump -Type DWord -Force -Value 1

   # Set up the guest OS to collect user mode dumps on a service crash event
   $key = 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting\LocalDumps'
   if ((Test-Path -Path $key) -eq $false) {(New-Item -Path 'HKLM:\SOFTWARE\Microsoft\Windows\Windows Error Reporting' -Name LocalDumps)}
   New-ItemProperty -Path $key -Name DumpFolder -Type ExpandString -Force -Value 'C:\CrashDumps'
   New-ItemProperty -Path $key -Name CrashCount -Type DWord -Force -Value 10
   New-ItemProperty -Path $key -Name DumpType -Type DWord -Force -Value 2
   Set-Service -Name WerSvc -StartupType Manual
   ```

1. Sprawdź, czy repozytorium Instrumentacja zarządzania Windows (WMI) jest spójne:

   ```powershell
   winmgmt.exe /verifyrepository
   ```

   Jeśli repozytorium jest uszkodzone, zobacz [WMI: uszkodzenie repozytorium](https://techcommunity.microsoft.com/t5/ask-the-performance-team/wmi-repository-corruption-or-not/ba-p/375484).

1. Upewnij się, że żadna inna aplikacja nie korzysta z portu 3389. Ten port jest używany dla usługi RDP na platformie Azure. Aby sprawdzić, które porty są używane na maszynie wirtualnej, uruchom polecenie `netstat.exe -anob` :

   ```powershell
   netstat.exe -anob
   ```

1. Aby przekazać wirtualny dysk twardy z systemem Windows, który jest kontrolerem domeny:

   - Postępuj zgodnie z [tymi dodatkowymi krokami](https://support.microsoft.com/kb/2904015) , aby przygotować dysk.

   - Upewnij się, że znasz hasło trybu przywracania usług katalogowych (DSRM), jeśli kiedykolwiek trzeba uruchomić maszynę wirtualną w trybie DSRM. Aby uzyskać więcej informacji, zobacz [Ustawianie hasła DSRM](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/cc754363(v=ws.11)).

1. Upewnij się, że znasz wbudowane konto administratora i hasło. Może zajść potrzeba zresetowania bieżącego hasła administratora lokalnego i upewnienia się, że można użyć tego konta do logowania się do systemu Windows za pomocą połączenia RDP. To uprawnienie dostępu jest kontrolowane przez obiekt zasady grupy "Zezwalaj na logowanie za pomocą Usługi pulpitu zdalnego". Wyświetl ten obiekt w Edytor lokalnych zasad grupy:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment`

1. Sprawdź następujące zasady usługi Azure AD, aby upewnić się, że nie blokuje dostępu do protokołu RDP:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights
      Assignment\Deny access to this computer from the network`

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights
      Assignment\Deny log on through Remote Desktop Services`

1. Sprawdź następujące zasady usługi Azure AD, aby upewnić się, że nie usuwają żadnego z wymaganych kont dostępu:

   - `Computer Configuration\Windows Settings\Security Settings\Local Policies\User Rights Assignment\Access this computer from the network`

   Zasady powinny być wymienione w następujących grupach:

   - Administratorzy

   - Operatorzy kopii zapasowych

   - Wszyscy

   - Użytkownicy

1. Uruchom ponownie maszynę wirtualną, aby upewnić się, że system Windows jest nadal w dobrej kondycji i można go połączyć za pomocą połączenia RDP. Na tym etapie warto utworzyć maszynę wirtualną na lokalnym serwerze funkcji Hyper-V, aby upewnić się, że maszyna wirtualna jest uruchamiana w całości. Następnie przetestuj go, aby upewnić się, że można uzyskać dostęp do maszyny wirtualnej za pomocą protokołu RDP.

1. Usuń wszystkie filtry interfejsu dodatkowego sterownika transportu (TDI). Na przykład Usuń oprogramowanie, które analizuje pakiety TCP lub dodatkowe zapory.

1. Odinstaluj wszelkie inne oprogramowanie lub sterowniki innej firmy, które są powiązane ze składnikami fizycznymi lub innymi technologiami wirtualizacji.

### <a name="install-windows-updates"></a>Zainstaluj aktualizacje systemu Windows

W idealnym przypadku należy zachować uaktualnienie komputera do *poziomu poprawki*, jeśli nie jest to możliwe, należy się upewnić, że są zainstalowane następujące aktualizacje. Aby uzyskać najnowsze aktualizacje, zobacz strony historii systemu Windows: [Windows 10, Windows server 2019](https://support.microsoft.com/help/4000825), [Windows 8.1 i Windows Server 2012 R2](https://support.microsoft.com/help/4009470) oraz [Windows 7 z dodatkiem SP1 i Windows Server 2008 R2 SP1](https://support.microsoft.com/help/4009469).

<br />

|        Składnik        |     Binarne     | Windows 7 z dodatkiem SP1, Windows Server 2008 R2 z dodatkiem SP1 |       Windows 8, Windows Server 2012        | Windows 8.1, Windows Server 2012 R2 | Windows 10 v1607, Windows Server 2016 v1607 |      V1703 systemu Windows 10      | Windows 10 v1709, Windows Server 2016 v1709 | Windows 10 v1803, Windows Server 2016 v1803 |
| ----------------------- | -------------- | ----------------------------------------- | ------------------------------------------- | ----------------------------------- | ------------------------------------------- | -------------------------- | ------------------------------------------- | ------------------------------------------- |
| Magazyn                 | disk.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17638 / 6.2.9200.21757 - KB3137061 | 6.3.9600.18203 - KB3137061          | -                                           | -                          | -                                           | -                                           |
|                         | storport.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17188 / 6.2.9200.21306 - KB3018489 | 6.3.9600.18573 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.332             | -                                           | -                                           |
|                         | ntfs.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17623 / 6.2.9200.21743 - KB3121255 | 6.3.9600.18654 - KB4022726          | 10.0.14393.1198 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | Iologmsg.dll   | 6.1.7601.23403 - KB3125574                | 6.2.9200.16384 - KB2995387                  | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | Classpnp.sys   | 6.1.7601.23403 - KB3125574                | 6.2.9200.17061 / 6.2.9200.21180 - KB2995387 | 6.3.9600.18334 - KB3172614          | 10.0.14393.953 - KB4022715                  | -                          | -                                           | -                                           |
|                         | Volsnap.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17047 / 6.2.9200.21165 - KB2975331 | 6.3.9600.18265 - KB3145384          | -                                           | 10.0.15063.0               | -                                           | -                                           |
|                         | partmgr.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.16681 - KB2877114                  | 6.3.9600.17401 — KB3000850          | 10.0.14393.953 - KB4022715                  | 10.0.15063.0               | -                                           | -                                           |
|                         | volmgr.sys     |                                           |                                             |                                     |                                             | 10.0.15063.0               | -                                           | -                                           |
|                         | Volmgrx.sys    | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | 10.0.15063.0               | -                                           | -                                           |
|                         | Msiscsi.sys    | 6.1.7601.23403 - KB3125574                | 6.2.9200.21006 - KB2955163                  | 6.3.9600.18624 - KB4022726          | 10.0.14393.1066 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | Msdsm.sys      | 6.1.7601.23403 - KB3125574                | 6.2.9200.21474 - KB3046101                  | 6.3.9600.18592 - KB4022726          | -                                           | -                          | -                                           | -                                           |
|                         | Mpio.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.21190 - KB3046101                  | 6.3.9600.18616 - KB4022726          | 10.0.14393.1198 - KB4022715                 | -                          | -                                           | -                                           |
|                         | vmstorfl.sys   | 6.3.9600.18907 - KB4072650                | 6.3.9600.18080 - KB3063109                  | 6.3.9600.18907 - KB4072650          | 10.0.14393.2007 - KB4345418                 | 10.0.15063.850 - KB4345419 | 10.0.16299.371 - KB4345420                  | -                                           |
|                         | Fveapi.dll     | 6.1.7601.23311 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.18294 - KB3172614          | 10.0.14393.576 - KB4022715                  | -                          | -                                           | -                                           |
|                         | Fveapibase.dll | 6.1.7601.23403 - KB3125574                | 6.2.9200.20930 - KB2930244                  | 6.3.9600.17415 - KB3172614          | 10.0.14393.206 - KB4022715                  | -                          | -                                           | -                                           |
| Sieć                 | netvsc.sys     | -                                         | -                                           | -                                   | 10.0.14393.1198 - KB4022715                 | 10.0.15063.250 - KB4020001 | -                                           | -                                           |
|                         | mrxsmb10.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.22108 - KB4022724                  | 6.3.9600.18603 - KB4022726          | 10.0.14393.479 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | mrxsmb20.sys   | 6.1.7601.23816 - KB4022722                | 6.2.9200.21548 - KB4022724                  | 6.3.9600.18586 - KB4022726          | 10.0.14393.953 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | mrxsmb.sys     | 6.1.7601.23816 - KB4022722                | 6.2.9200.22074 - KB4022724                  | 6.3.9600.18586 - KB4022726          | 10.0.14393.953 - KB4022715                  | 10.0.15063.0               | -                                           | -                                           |
|                         | tcpip.sys      | 6.1.7601.23761 - KB4022722                | 6.2.9200.22070 - KB4022724                  | 6.3.9600.18478 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.447             | -                                           | -                                           |
|                         | http.sys       | 6.1.7601.23403 - KB3125574                | 6.2.9200.17285 - KB3042553                  | 6.3.9600.18574 - KB4022726          | 10.0.14393.251 - KB4022715                  | 10.0.15063.483             | -                                           | -                                           |
|                         | vmswitch.sys   | 6.1.7601.23727 - KB4022719                | 6.2.9200.22117 - KB4022724                  | 6.3.9600.18654 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.138             | -                                           | -                                           |
| Core                    | ntoskrnl.exe   | 6.1.7601.23807 - KB4022719                | 6.2.9200.22170 - KB4022718                  | 6.3.9600.18696 - KB4022726          | 10.0.14393.1358 - KB4022715                 | 10.0.15063.483             | -                                           | -                                           |
| Usługi pulpitu zdalnego | rdpcorets.dll  | 6.2.9200.21506 - KB4022719                | 6.2.9200.22104 - KB4022724                  | 6.3.9600.18619 - KB4022726          | 10.0.14393.1198 - KB4022715                 | 10.0.15063.0               | -                                           | -                                           |
|                         | termsrv.dll    | 6.1.7601.23403 - KB3125574                | 6.2.9200.17048 - KB2973501                  | 6.3.9600.17415 — KB3000850          | 10.0.14393.0 — KB4022715                    | 10.0.15063.0               | -                                           | -                                           |
|                         | termdd.sys     | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | win32k.sys     | 6.1.7601.23807 - KB4022719                | 6.2.9200.22168 - KB4022718                  | 6.3.9600.18698 - KB4022726          | 10.0.14393.594 - KB4022715                  | -                          | -                                           | -                                           |
|                         | rdpdd.dll      | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
|                         | rdpwd.sys      | 6.1.7601.23403 - KB3125574                | -                                           | -                                   | -                                           | -                          | -                                           | -                                           |
| Zabezpieczenia                | MS17-010       | KB4012212                                 | KB4012213                                   | KB4012213                           | KB4012606                                   | KB4012606                  | -                                           | -                                           |
|                         |                |                                           | KB4012216                                   |                                     | KB4013198                                   | KB4013198                  | -                                           | -                                           |
|                         |                | KB4012215                                 | KB4012214                                   | KB4012216                           | KB4013429                                   | KB4013429                  | -                                           | -                                           |
|                         |                |                                           | KB4012217                                   |                                     | KB4013429                                   | KB4013429                  | -                                           | -                                           |
|                         | CVE-2018-0886  | KB4103718                                 | KB4103730                                   | KB4103725                           | KB4103723                                   | KB4103731                  | KB4103727                                   | KB4103721                                   |
|                         |                | KB4103712                                 | KB4103726                                   | KB4103715                           |                                             |                            |                                             |                                             |

> [!NOTE]
> Aby uniknąć przypadkowego ponownego uruchomienia podczas aprowizacji maszyny wirtualnej, zalecamy upewnienie się, że wszystkie instalacje Windows Update zostały zakończone i że nie ma żadnych oczekujących aktualizacji. Jednym z nich jest zainstalowanie wszystkich możliwych aktualizacji systemu Windows i ponowne uruchomienie komputera przed uruchomieniem `sysprep.exe` polecenia.

## <a name="determine-when-to-use-sysprep"></a>Ustalanie, kiedy należy używać narzędzia Sysprep

Narzędzie przygotowywania systemu ( `sysprep.exe` ) to proces, który można uruchomić w celu zresetowania instalacji systemu Windows.
Program Sysprep udostępnia "środowisko pracy", usuwając wszystkie dane osobowe i instalując kilka składników.

Zwykle uruchamia `sysprep.exe` się, aby utworzyć szablon, na podstawie którego można wdrożyć kilka innych maszyn wirtualnych z określoną konfiguracją. Szablon jest nazywany *uogólnionym obrazem*.

Aby utworzyć tylko jedną maszynę wirtualną na podstawie jednego dysku, nie trzeba używać narzędzia Sysprep. Zamiast tego można utworzyć maszynę wirtualną na podstawie *wyspecjalizowanego obrazu*. Aby uzyskać informacje dotyczące sposobu tworzenia maszyny wirtualnej na podstawie wyspecjalizowanego dysku, zobacz:

- [Tworzenie maszyny wirtualnej na podstawie wyspecjalizowanego dysku](create-vm-specialized.md)
- [Tworzenie maszyny wirtualnej na podstawie wyspecjalizowanego dysku VHD](./create-vm-specialized-portal.md)

Aby utworzyć uogólniony obraz, należy uruchomić program Sysprep. Aby uzyskać więcej informacji, zobacz [jak używać narzędzia Sysprep: wprowadzenie](/previous-versions/windows/it-pro/windows-xp/bb457073(v=technet.10)).

Nie każda rola lub aplikacja zainstalowana na komputerze z systemem Windows obsługuje uogólnione obrazy. Przed użyciem tej procedury upewnij się, że program Sysprep obsługuje rolę komputera. Aby uzyskać więcej informacji, zobacz [Obsługa programu Sysprep dla ról serwera](/windows-hardware/manufacture/desktop/sysprep-support-for-server-roles).

W szczególności program Sysprep wymaga całkowicie odszyfrowania dysków przed wykonaniem. Jeśli na maszynie wirtualnej włączono szyfrowanie, wyłącz je przed uruchomieniem narzędzia Sysprep.


### <a name="generalize-a-vhd"></a>Uogólnianie wirtualnego dysku twardego

>[!NOTE]
> Po uruchomieniu wykonaj `sysprep.exe` następujące kroki, Wyłącz maszynę wirtualną. Nie włączaj jej ponownie, dopóki nie utworzysz obrazu na platformie Azure.

1. Zaloguj się do maszyny wirtualnej z systemem Windows.
1. Uruchom sesję programu PowerShell jako administrator.
1. Usuń katalog Panther (C:\Windows\Panther).
1. Zmień katalog na `%windir%\system32\sysprep` . Następnie należy uruchomić polecenie `sysprep.exe`.
1. W oknie dialogowym **Narzędzie przygotowywania systemu** wybierz opcję **Wprowadź system out-of-Box Experience (OOBE)** i upewnij się, że jest zaznaczone pole wyboru **generalize** .

    ![Narzędzie przygotowywania systemu](media/prepare-for-upload-vhd-image/syspre.png)
1. W obszarze **Opcje zamykania** wybierz pozycję **Zamknij**.
1. Wybierz przycisk **OK**.
1. Po zakończeniu działania narzędzia Sysprep Zamknij maszynę wirtualną. Nie używaj **ponownego uruchamiania** , aby zamknąć maszynę wirtualną.

Teraz dysk VHD jest gotowy do przekazania. Aby uzyskać więcej informacji na temat sposobu tworzenia maszyny wirtualnej na podstawie uogólnionego dysku, zobacz [przekazywanie uogólnionego wirtualnego dysku twardego i używanie go do tworzenia nowej maszyny wirtualnej na platformie Azure](/previous-versions/azure/virtual-machines/windows/sa-upload-generalized).

>[!NOTE]
> Niestandardowy plik *unattend.xml* nie jest obsługiwany. Chociaż obsługujemy Właściwość **additionalUnattendContent** , która zapewnia tylko ograniczoną obsługę dodawania opcji [instalacji Microsoft-Windows-Shell-setup](/windows-hardware/customize/desktop/unattend/microsoft-windows-shell-setup) do pliku *unattend.xml* , którego używa Agent aprowizacji platformy Azure. Aby dodać FirstLogonCommands i LogonCommands, można użyć, na przykład [additionalUnattendContent](/dotnet/api/microsoft.azure.management.compute.models.additionalunattendcontent) . Aby uzyskać więcej informacji, zobacz [AdditionalUnattendContent FirstLogonCommands example](https://github.com/Azure/azure-quickstart-templates/issues/1407).

## <a name="convert-the-virtual-disk-to-a-fixed-size-vhd"></a>Konwertowanie dysku wirtualnego na wirtualny dysk twardy o stałym rozmiarze

Użyj jednej z metod opisanych w tej sekcji, aby przekonwertować dysk wirtualny i zmienić jego rozmiar na wymagany format dla platformy Azure:

1. Przed uruchomieniem konwersji lub zmiany rozmiaru dysku wirtualnego wykonaj kopię zapasową maszyny wirtualnej.

1. Upewnij się, że wirtualny dysk twardy systemu Windows działa poprawnie na serwerze lokalnym. Przed podjęciem próby konwersji lub przekazania na platformę Azure Usuń wszystkie błędy w samej maszynie wirtualnej.

1. Przekonwertuj dysk wirtualny na typ fixed.

1. Zmień rozmiar dysku wirtualnego, aby spełniał wymagania platformy Azure:

   1. Dyski na platformie Azure muszą mieć rozmiar wirtualny wyrównany do 1 MiB. Jeśli wirtualny dysk twardy jest częścią 1 MiB, należy zmienić rozmiar dysku na wielokrotność 1 MiB. Dyski, które są częścią bazy MiB, powodują błędy podczas tworzenia obrazów na podstawie przekazanego wirtualnego dysku twardego. Aby sprawdzić rozmiar, można użyć polecenia cmdlet [Get-VHD](/powershell/module/hyper-v/get-vhd) programu PowerShell do wyświetlania "size", który musi być wielokrotnością 1 MIB na platformie Azure, i "rozmiar pliku", który będzie równy "size" i 512 bajtów dla stopki dysku VHD.
   
   1. Maksymalny dozwolony rozmiar wirtualnego dysku twardego systemu operacyjnego z maszyną wirtualną generacji 1 to 2 048 GiB (2 TiB), 
   1. Maksymalny rozmiar dysku danych to 32 767 GiB (32 TiB).

> [!NOTE]
> - W przypadku przygotowywania dysku systemu operacyjnego Windows po konwersji na dysk stały i zmiany rozmiaru w razie potrzeby należy utworzyć maszynę wirtualną używającą dysku. Uruchom i zaloguj się do maszyny wirtualnej i przejdź do sekcji w tym artykule, aby zakończyć przygotowywanie jej do przekazania.  
> - Jeśli przygotowywany jest dysk z danymi, można go zatrzymać z tą sekcją i kontynuować przekazywanie dysku.

### <a name="use-hyper-v-manager-to-convert-the-disk"></a>Konwertowanie dysku za pomocą Menedżera funkcji Hyper-V

1. Otwórz Menedżera funkcji Hyper-V i wybierz komputer lokalny po lewej stronie. W menu znajdującym się nad listą komputerów wybierz **Akcja**  >  **Edytuj dysk**.
1. Na stronie **lokalizowanie wirtualnego dysku twardego** wybierz swój dysk wirtualny.
1. Na stronie **Wybierz akcję** wybierz pozycję **Konwertuj**  >  **dalej**.
1. Aby przekonwertować z formatu VHDX, wybierz pozycję **VHD**  >  **dalej**.
1. Aby skonwertować dynamicznie powiększający dysk, wybierz pozycję **stały rozmiar**  >  **dalej**.
1. Znajdź i wybierz ścieżkę, w której ma zostać zapisany nowy plik VHD.
1. Wybierz pozycję **Zakończ**.

### <a name="use-powershell-to-convert-the-disk"></a>Konwertowanie dysku przy użyciu programu PowerShell

Dysk wirtualny można skonwertować przy użyciu polecenia cmdlet [convert-VHD](/powershell/module/hyper-v/convert-vhd) w programie PowerShell. Jeśli potrzebujesz informacji na temat instalowania tego polecenia cmdlet [, zobacz Instalowanie roli funkcji Hyper-V](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

Poniższy przykład konwertuje dysk z dysku VHDX na dysk VHD. Konwertuje także dysk z dynamicznie powiększanego dysku na dysk o stałym rozmiarze.

```powershell
Convert-VHD -Path C:\test\MyVM.vhdx -DestinationPath C:\test\MyNewVM.vhd -VHDType Fixed
```

W tym przykładzie Zastąp wartość **Path ścieżką** do wirtualnego dysku twardego, który chcesz skonwertować. Zastąp wartość **DestinationPath** nową ścieżką i nazwą konwertowanego dysku.

### <a name="use-hyper-v-manager-to-resize-the-disk"></a>Zmienianie rozmiaru dysku za pomocą Menedżera funkcji Hyper-V

1. Otwórz Menedżera funkcji Hyper-V i wybierz komputer lokalny po lewej stronie. W menu znajdującym się nad listą komputerów wybierz **Akcja**  >  **Edytuj dysk**.
1. Na stronie **lokalizowanie wirtualnego dysku twardego** wybierz swój dysk wirtualny.
1. Na stronie **Wybierz akcję** wybierz pozycję **Rozwiń**  >  **dalej**.
1. Na stronie **lokalizowanie wirtualnego dysku twardego** wprowadź nowy rozmiar w GIB > **dalej**.
1. Wybierz pozycję **Zakończ**.

### <a name="use-powershell-to-resize-the-disk"></a>Zmienianie rozmiaru dysku przy użyciu programu PowerShell

Można zmienić rozmiar dysku wirtualnego za pomocą polecenia cmdlet [Zmień rozmiar pliku VHD](/powershell/module/hyper-v/resize-vhd) w programie PowerShell. Jeśli potrzebujesz informacji na temat instalowania tego polecenia cmdlet [, zobacz Instalowanie roli funkcji Hyper-V](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

Poniższy przykład zmienia rozmiar dysku z 100,5 MiB na 101 MiB, aby spełnić wymagania dotyczące wyrównania na platformie Azure.

```powershell
Resize-VHD -Path C:\test\MyNewVM.vhd -SizeBytes 105906176
```

W tym przykładzie Zastąp wartość **Path ścieżką** do wirtualnego dysku twardego, którego rozmiar chcesz zmienić. Zastąp wartość **SizeBytes** nowym rozmiarem w bajtach dla dysku.

### <a name="convert-from-vmware-vmdk-disk-format"></a>Konwertuj z formatu dysku VMware VMDK

Jeśli masz obraz maszyny wirtualnej z systemem Windows w [formacie VMDK](https://en.wikipedia.org/wiki/VMDK), możesz użyć [Azure Migrate](../../migrate/server-migrate-overview.md) , aby przekonwertować dysk VMDK i przekazać go na platformę Azure.

## <a name="complete-the-recommended-configurations"></a>Wykonaj zalecane konfiguracje

Następujące ustawienia nie wpływają na przekazywanie wirtualnego dysku twardego. Zdecydowanie zaleca się jednak ich skonfigurowanie.

- Zainstaluj [agenta maszyny wirtualnej platformy Azure](https://go.microsoft.com/fwlink/?LinkID=394789). Następnie można włączyć rozszerzenia maszyn wirtualnych. Rozszerzenia maszyn wirtualnych implementują większość krytycznych funkcji, których można chcieć używać z maszynami wirtualnymi. Wymagane są rozszerzenia, na przykład w celu resetowania haseł lub konfigurowania protokołu RDP. Aby uzyskać więcej informacji, zobacz [Omówienie agenta maszyny wirtualnej platformy Azure](../extensions/agent-windows.md).
- Po utworzeniu maszyny wirtualnej na platformie Azure Zalecamy umieszczenie pliku stronicowania na *woluminie dysków* czasowych, aby zwiększyć wydajność. Umieszczanie plików można skonfigurować w następujący sposób:

  ```powershell
  Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Control\Session Manager\Memory Management' -Name PagingFiles -Value 'D:\pagefile.sys' -Type MultiString -Force
  ```

  Jeśli dysk danych jest dołączony do maszyny wirtualnej, litera woluminu dysku czasowego jest zwykle *D*. To oznaczenie może różnić się w zależności od ustawień i liczby dostępnych dysków.

  - Zalecamy wyłączenie blokowania skryptów, które mogą być dostarczane przez oprogramowanie antywirusowe. Mogą one zakłócać i blokować skrypty agenta aprowizacji systemu Windows wykonywane podczas wdrażania nowej maszyny wirtualnej z obrazu.

## <a name="next-steps"></a>Następne kroki

- [Przekazywanie obrazu maszyny wirtualnej z systemem Windows na platformę Azure w celu wdrożenia Menedżer zasobów](upload-generalized-managed.md)
- [Rozwiązywanie problemów z aktywacją maszyn wirtualnych systemu Windows Azure](../troubleshooting/troubleshoot-activation-problems.md)
