---
title: Program CMD i program PowerShell na maszynach wirtualnych z systemem Windows Azure | Microsoft Docs
description: Jak używać poleceń CMD i PowerShell w konsoli SAC na maszynach wirtualnych platformy Azure z systemem Windows
services: virtual-machines-windows
documentationcenter: ''
author: alsin
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 08/14/2018
ms.author: alsin
ms.openlocfilehash: ef533b3566ac557b57f1435a2a9b2dbe26896993
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91306892"
---
# <a name="windows-commands---cmd-and-powershell"></a>Polecenia systemu Windows — CMD i PowerShell

Ta sekcja zawiera przykładowe polecenia służące do wykonywania typowych zadań w scenariuszach, w których może być konieczne użycie konsoli SAC do uzyskiwania dostępu do maszyny wirtualnej z systemem Windows, na przykład w przypadku konieczności rozwiązywania problemów z błędami połączeń RDP.

Konsola SAC została uwzględniona we wszystkich wersjach systemu Windows od systemu Windows Server 2003, ale jest domyślnie wyłączona. Konsola SAC korzysta ze `sacdrv.sys` sterownika jądra, `Special Administration Console Helper` usługi ( `sacsvr` ) i `sacsess.exe` procesu. Aby uzyskać więcej informacji, zobacz [Narzędzia i ustawienia usług zarządzania awaryjnego](/previous-versions/windows/it-pro/windows-server-2003/cc787940(v%3dws.10)).

Konsola SAC umożliwia nawiązanie połączenia z uruchomionym systemem operacyjnym za pośrednictwem portu szeregowego. Po uruchomieniu programu CMD z konsoli SAC `sacsess.exe` uruchamia się `cmd.exe` w URUCHOMIONYM systemie operacyjnym. Możesz zobaczyć, że w Menedżerze zadań, jeśli istnieje połączenie RDP z maszyną wirtualną w tym samym czasie, gdy użytkownik jest połączony z konsolą SAC za pośrednictwem funkcji konsoli szeregowej. Dostęp do programu CMD za pośrednictwem konsoli SAC jest taki sam, jak w `cmd.exe` przypadku połączenia za pośrednictwem protokołu RDP. Wszystkie te same polecenia i narzędzia są dostępne, w tym możliwość uruchamiania programu PowerShell z tego wystąpienia. Jest to główna różnica między konsolą SAC a środowiskiem odzyskiwania systemu Windows (WinRE) w tej konsoli SAC umożliwia zarządzanie systemem operacyjnym, w którym WinRE jest uruchamiany w innym, minimalnym systemie operacyjnym. Chociaż maszyny wirtualne platformy Azure nie obsługują możliwości uzyskiwania dostępu do środowiska WinRE, za pomocą konsoli szeregowej można zarządzać maszynami wirtualnymi platformy Azure za pośrednictwem konsoli SAC.

Ponieważ konsola SAC jest ograniczona do 80x24ego buforu ekranu bez przewijania z powrotem, Dodaj `| more` do poleceń, aby wyświetlić dane wyjściowe po jednej stronie w danym momencie. Użyj `<spacebar>` , aby zobaczyć następną stronę lub `<enter>` zobaczyć następny wiersz.

`SHIFT+INSERT` jest skrótem wklejania okna konsoli szeregowej.

Ze względu na ograniczony bufor ekranu, dłuższe polecenia mogą być łatwiejsze do wpisania w edytorze tekstu lokalnego, a następnie wklejone do konsoli SAC.

## <a name="view-and-edit-windows-registry-settings-using-cmd"></a>Wyświetlanie i edytowanie ustawień rejestru systemu Windows za pomocą polecenia CMD
### <a name="verify-rdp-is-enabled"></a>Sprawdź, czy włączono protokół RDP
`reg query "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections`

`reg query "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections`

Drugi klucz (w obszarze \Policies) będzie istnieć tylko w przypadku skonfigurowania odpowiedniego ustawienia zasad grupy.

### <a name="enable-rdp"></a>Włącz protokół RDP
`reg add "HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server" /v fDenyTSConnections /t REG_DWORD /d 0`

`reg add "HKLM\SOFTWARE\Policies\Microsoft\Windows NT\Terminal Services" /v fDenyTSConnections /t REG_DWORD /d 0`

Drugi klucz (w obszarze \Policies) byłby wymagany tylko wtedy, gdy skonfigurowano odpowiednie ustawienie zasad grupy. Wartość zostanie ponownie zapisywana przy następnym odświeżeniu zasad grupy, jeśli jest skonfigurowana w zasadach grupy.

## <a name="manage-windows-services-using-cmd"></a>Zarządzanie usługami systemu Windows przy użyciu polecenia CMD

### <a name="view-service-state"></a>Wyświetl stan usługi
`sc query termservice`
###  <a name="view-service-logon-account"></a>Wyświetl konto logowania do usługi
`sc qc termservice`
### <a name="set-service-logon-account"></a>Ustaw konto logowania do usługi
`sc config termservice obj= "NT Authority\NetworkService"`

Po znaku równości jest wymagane miejsce.
### <a name="set-service-start-type"></a>Ustaw typ uruchomienia usługi
`sc config termservice start= demand`

Po znaku równości jest wymagane miejsce. Możliwe wartości początkowe to include `boot` , `system` ,,, `auto` `demand` `disabled` , `delayed-auto` .
### <a name="set-service-dependencies"></a>Ustawianie zależności usługi
`sc config termservice depend= RPCSS`

Po znaku równości jest wymagane miejsce.
### <a name="start-service"></a>Uruchom usługę
`net start termservice`

lub

`sc start termservice`
### <a name="stop-service"></a>Zatrzymaj usługę
`net stop termservice`

lub

`sc stop termservice`
## <a name="manage-networking-features-using-cmd"></a>Zarządzanie funkcjami sieciowymi za pomocą polecenia CMD
### <a name="show-nic-properties"></a>Pokaż właściwości karty sieciowej
`netsh interface show interface`
### <a name="show-ip-properties"></a>Pokaż właściwości IP
`netsh interface ip show config`
### <a name="show-ipsec-configuration"></a>Pokaż konfigurację protokołu IPSec
`netsh nap client show configuration`
### <a name="enable-nic"></a>Włącz kartę sieciową
`netsh interface set interface name="<interface name>" admin=enabled`
### <a name="set-nic-to-use-dhcp"></a>Ustawianie karty sieciowej do korzystania z protokołu DHCP
`netsh interface ip set address name="<interface name>" source=dhcp`

Aby uzyskać więcej informacji na temat `netsh` , [kliknij tutaj](/windows-server/networking/technologies/netsh/netsh-contexts).

Maszyny wirtualne platformy Azure należy zawsze skonfigurować w systemie operacyjnym gościa, aby można było uzyskać adres IP przy użyciu protokołu DHCP. Ustawienie statycznego adresu IP platformy Azure nadal używa protokołu DHCP do nadawania statycznego adresu IP do maszyny wirtualnej.
### <a name="ping"></a>Ping
`ping 8.8.8.8`
### <a name="port-ping"></a>Port ping
Instalowanie klienta Telnet

`dism /online /Enable-Feature /FeatureName:TelnetClient`

Testowanie łączności

`telnet bing.com 80`

Aby usunąć klienta Telnet

`dism /online /Disable-Feature /FeatureName:TelnetClient`

Jeśli domyślnie jest ograniczone do metod dostępnych w systemie Windows, program PowerShell może być lepszym rozwiązaniem do testowania łączności z portem. Przykłady można znaleźć w sekcji programu PowerShell poniżej.
### <a name="test-dns-name-resolution"></a>Testowanie rozpoznawania nazw DNS
`nslookup bing.com`
### <a name="show-windows-firewall-rule"></a>Pokaż regułę zapory systemu Windows
`netsh advfirewall firewall show rule name="Remote Desktop - User Mode (TCP-In)"`
### <a name="disable-windows-firewall"></a>Wyłącz zaporę systemu Windows
`netsh advfirewall set allprofiles state off`

Tego polecenia można użyć podczas rozwiązywania problemów z tymczasową regułą zapory systemu Windows. Zostanie on włączony przy następnym ponownym uruchomieniu lub po włączeniu przy użyciu poniższego polecenia. Nie należy zatrzymywać usługi Zapora systemu Windows (MPSSVC) ani podstawowego aparatu filtrowania (BFE) jako sposobu wyłączania zapory systemu Windows. Zatrzymywanie MPSSVC lub BFE spowoduje zablokowanie wszystkich połączeń.
### <a name="enable-windows-firewall"></a>Włączanie Zapory systemu Windows
`netsh advfirewall set allprofiles state on`
## <a name="manage-users-and-groups-using-cmd"></a>Zarządzanie użytkownikami i grupami przy użyciu polecenia CMD
### <a name="create-local-user-account"></a>Utwórz konto użytkownika lokalnego
`net user /add <username> <password>`
### <a name="add-local-user-to-local-group"></a>Dodaj użytkownika lokalnego do grupy lokalnej
`net localgroup Administrators <username> /add`
### <a name="verify-user-account-is-enabled"></a>Sprawdź, czy konto użytkownika jest włączone
`net user <username> | find /i "active"`

Maszyny wirtualne platformy Azure utworzone na podstawie uogólnionego obrazu będą miały nazwę konta administratora lokalnego o nazwie określonej podczas aprowizacji maszyny wirtualnej. W związku z tym zwykle nie będzie `Administrator` .
### <a name="enable-user-account"></a>Włącz konto użytkownika
`net user <username> /active:yes`
### <a name="view-user-account-properties"></a>Wyświetl właściwości konta użytkownika
`net user <username>`

Przykładowe wiersze zainteresowania z konta administratora lokalnego:

`Account active Yes`

`Account expires Never`

`Password expires Never`

`Workstations allowed All`

`Logon hours allowed All`

`Local Group Memberships *Administrators`

### <a name="view-local-groups"></a>Wyświetlanie grup lokalnych
`net localgroup`
## <a name="manage-the-windows-event-log-using-cmd"></a>Zarządzanie dziennikiem zdarzeń systemu Windows przy użyciu polecenia CMD
### <a name="query-event-log-errors"></a>Rejestruj błędy dziennika zdarzeń
`wevtutil qe system /c:10 /f:text /q:"Event[System[Level=2]]" | more`

Zmień `/c:10` żądaną liczbę zdarzeń do zwrócenia lub przenieś ją, aby zwracała wszystkie zdarzenia zgodne z filtrem.
### <a name="query-event-log-by-event-id"></a>Zapytanie dziennika zdarzeń według identyfikatora zdarzenia
`wevtutil qe system /c:1 /f:text /q:"Event[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Zapytanie dziennika zdarzeń według identyfikatora zdarzenia i dostawcy
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Zbadaj dziennik zdarzeń według identyfikatora zdarzenia i dostawcy w ciągu ostatnich 24 godzin
`wevtutil qe system /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Użyj `604800000` , aby odszukać 7 dni zamiast 24 godzin.
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Zbadaj dziennik zdarzeń według identyfikatora zdarzenia, dostawcy i EventData w ciągu ostatnich 7 dni
`wevtutil qe security /c:1 /f:text /q:"Event[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications-using-cmd"></a>Wyświetlanie lub usuwanie zainstalowanych aplikacji przy użyciu polecenia CMD
### <a name="list-installed-applications"></a>Wyświetlanie listy zainstalowanych aplikacji
`wmic product get Name,InstallDate | sort /r | more`

`sort /r`Sortuje Malejąco według daty instalacji, aby ułatwić sprawdzenie, co zostało niedawno zainstalowane. Użyj polecenia `<spacebar>` , aby przejść do następnej strony danych wyjściowych lub `<enter>` Aby uzyskać więcej niż jeden wiersz.
### <a name="uninstall-an-application"></a>Odinstalowywanie aplikacji
`wmic path win32_product where name="<name>" call uninstall`

Zamień na `<name>` nazwę zwracaną w powyższym poleceniu dla aplikacji, którą chcesz usunąć.

## <a name="file-system-management-using-cmd"></a>Zarządzanie systemem plików przy użyciu polecenia CMD
### <a name="get-file-version"></a>Pobierz wersję pliku
`wmic datafile where "drive='C:' and path='\\windows\\system32\\drivers\\' and filename like 'netvsc%'" get version /format:list`

Ten przykład zwraca wersję pliku sterownika wirtualnej karty sieciowej, który jest netvsc.sys, netvsc63.sys lub netvsc60.sys w zależności od wersji systemu Windows.
### <a name="scan-for-system-file-corruption"></a>Skanuj pod kątem uszkodzenia pliku systemowego
`sfc /scannow`

Zobacz też [Naprawa obrazu systemu Windows](/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="scan-for-system-file-corruption"></a>Skanuj pod kątem uszkodzenia pliku systemowego
`dism /online /cleanup-image /scanhealth`

Zobacz też [Naprawa obrazu systemu Windows](/windows-hardware/manufacture/desktop/repair-a-windows-image).
### <a name="export-file-permissions-to-text-file"></a>Eksportowanie uprawnień pliku do pliku tekstowego
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /t /c > %temp%\MachineKeys_permissions_before.txt`
### <a name="save-file-permissions-to-acl-file"></a>Zapisz uprawnienia do pliku listy ACL
`icacls %programdata%\Microsoft\Crypto\RSA\MachineKeys /save %temp%\MachineKeys_permissions_before.aclfile /t`
### <a name="restore-file-permissions-from-acl-file"></a>Przywróć uprawnienia do pliku z pliku listy ACL
`icacls %programdata%\Microsoft\Crypto\RSA /save %temp%\MachineKeys_permissions_before.aclfile /t`

Ścieżka używana podczas korzystania z programu `/restore` musi być folderem nadrzędnym folderu określonego podczas korzystania z programu `/save` . W tym przykładzie `\RSA` jest elementem nadrzędnym `\MachineKeys` folderu określonego w `/save` powyższym przykładzie.
### <a name="take-ntfs-ownership-of-a-folder"></a>Przełącz własność systemu plików NTFS do folderu
`takeown /f %programdata%\Microsoft\Crypto\RSA\MachineKeys /a /r`
### <a name="grant-ntfs-permissions-to-a-folder-recursively"></a>Przyznawanie uprawnień NTFS do folderu rekursywnie
`icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"`
## <a name="manage-devices"></a>Zarządzanie urządzeniami
### <a name="remove-non-present-pnp-devices"></a>Usuwanie nieobecnych urządzeń PNP
`%windir%\System32\RUNDLL32.exe %windir%\System32\pnpclean.dll,RunDLL_PnpClean /Devices /Maxclean`
## <a name="manage-group-policy"></a>Zarządzanie zasadami grupy
### <a name="force-group-policy-update"></a>Wymuś aktualizację zasad grupy
`gpupdate /force /wait:-1`
## <a name="miscellaneous-tasks-using-cmd"></a>Różne zadania przy użyciu polecenia CMD
### <a name="show-os-version"></a>Pokaż wersję systemu operacyjnego
`ver`

lub

`wmic os get caption,version,buildnumber /format:list`

lub

`systeminfo  find /i "os name"`

`systeminfo | findstr /i /r "os.*version.*build"`
### <a name="view-os-install-date"></a>Wyświetl datę instalacji systemu operacyjnego
`systeminfo | find /i "original"`

lub

`wmic os get installdate`
### <a name="view-last-boot-time"></a>Wyświetl czas ostatniego rozruchu
`systeminfo | find /i "system boot time"`
### <a name="view-time-zone"></a>Wyświetl strefę czasową
`systeminfo | find /i "time zone"`

lub

`wmic timezone get caption,standardname /format:list`
### <a name="restart-windows"></a>Ponowne uruchamianie systemu Windows
`shutdown /r /t 0`

Dodanie `/f` spowoduje wymuszenie zamknięcia uruchomionych aplikacji bez ostrzeżenia użytkowników.
### <a name="detect-safe-mode-boot"></a>Wykrywaj rozruch w trybie awaryjnym
`bcdedit /enum | find /i "safeboot"`

## <a name="windows-commands---powershell"></a>Polecenia systemu Windows — PowerShell

Aby uruchomić program PowerShell w konsoli SAC, po dojściu do wiersza polecenia CMD wpisz:

`powershell <enter>`

> [!CAUTION]
> Przed uruchomieniem innych poleceń programu PowerShell Usuń moduł PSReadLine z sesji programu PowerShell. Występuje znany problem polegający na tym, że dodatkowe znaki można wprowadzać w tekście wklejanym ze schowka, jeśli PSReadLine jest uruchomiony w sesji programu PowerShell w konsoli SAC.

Najpierw sprawdź, czy PSReadLine jest załadowany. Jest ona domyślnie ładowana w systemie Windows Server 2016, Windows 10 i nowszych wersjach systemu Windows. Będzie ona dostępna tylko w starszych wersjach systemu Windows, jeśli została zainstalowana ręcznie.

Jeśli to polecenie powróci do monitu bez danych wyjściowych, moduł nie został załadowany i można nadal używać sesji programu PowerShell w konsoli SAC jako normalne.

`get-module psreadline`

Jeśli powyższe polecenie zwróci wersję modułu PSReadLine, uruchom następujące polecenie, aby je zwolnić. To polecenie nie usuwa ani nie Odinstalowuje modułu, zwalnia go tylko z bieżącej sesji programu PowerShell.

`remove-module psreadline`

## <a name="view-and-edit-windows-registry-settings-using-powershell"></a>Wyświetlanie i edytowanie ustawień rejestru systemu Windows przy użyciu programu PowerShell
### <a name="verify-rdp-is-enabled"></a>Sprawdź, czy włączono protokół RDP
`get-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections'`

`get-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections'`

Drugi klucz (w obszarze \Policies) będzie istnieć tylko w przypadku skonfigurowania odpowiedniego ustawienia zasad grupy.
### <a name="enable-rdp"></a>Włącz protokół RDP
`set-itemproperty -path 'hklm:\system\curRentcontrolset\control\terminal server' -name 'fdenytsconNections' 0 -type dword`

`set-itemproperty -path 'hklm:\software\policies\microsoft\windows nt\terminal services' -name 'fdenytsconNections' 0 -type dword`

Drugi klucz (w obszarze \Policies) byłby wymagany tylko wtedy, gdy skonfigurowano odpowiednie ustawienie zasad grupy. Wartość zostanie ponownie zapisywana przy następnym odświeżeniu zasad grupy, jeśli jest skonfigurowana w zasadach grupy.
## <a name="manage-windows-services-using-powershell"></a>Zarządzanie usługami systemu Windows przy użyciu programu PowerShell
### <a name="view-service-details"></a>Wyświetl szczegóły usługi
`get-wmiobject win32_service -filter "name='termservice'" |  format-list Name,DisplayName,State,StartMode,StartName,PathName,ServiceType,Status,ExitCode,ServiceSpecificExitCode,ProcessId`

`Get-Service` można go użyć, ale nie obejmuje konta logowania do usługi. `Get-WmiObject win32-service` wpływa.
### <a name="set-service-logon-account"></a>Ustaw konto logowania do usługi
`(get-wmiobject win32_service -filter "name='termservice'").Change($null,$null,$null,$null,$null,$false,'NT Authority\NetworkService')`

W przypadku korzystania z konta usługi innego niż `NT AUTHORITY\LocalService` , `NT AUTHORITY\NetworkService` lub należy `LocalSystem` określić hasło konta jako ostatni (ósmy) argument po nazwie konta.
### <a name="set-service-startup-type"></a>Ustaw typ uruchomienia usługi
`set-service termservice -startuptype Manual`

`Set-service` akceptuje `Automatic` , `Manual` lub `Disabled` dla typu uruchomienia.
### <a name="set-service-dependencies"></a>Ustawianie zależności usługi
`Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\TermService' -Name DependOnService -Value @('RPCSS','TermDD')`
### <a name="start-service"></a>Uruchom usługę
`start-service termservice`
### <a name="stop-service"></a>Zatrzymaj usługę
`stop-service termservice`
## <a name="manage-networking-features-using-powershell"></a>Zarządzanie funkcjami sieciowymi przy użyciu programu PowerShell
### <a name="show-nic-properties"></a>Pokaż właściwości karty sieciowej
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} |  format-list status,name,ifdesc,macadDresS,driverversion,MediaConNectState,MediaDuplexState`

lub

`get-wmiobject win32_networkadapter -filter "servicename='netvsc'" |  format-list netenabled,name,macaddress`

`Get-NetAdapter` jest dostępny w 2012 +, do użycia w programie 2008R2 `Get-WmiObject` .
### <a name="show-ip-properties"></a>Pokaż właściwości IP
`get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'" |  format-list DNSHostName,IPAddress,DHCPEnabled,IPSubnet,DefaultIPGateway,MACAddress,DHCPServer,DNSServerSearchOrder`
### <a name="enable-nic"></a>Włącz kartę sieciową
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | enable-netadapter`

lub

`(get-wmiobject win32_networkadapter -filter "servicename='netvsc'").enable()`

`Get-NetAdapter` jest dostępny w 2012 +, do użycia w programie 2008R2 `Get-WmiObject` .
### <a name="set-nic-to-use-dhcp"></a>Ustawianie karty sieciowej do korzystania z protokołu DHCP
`get-netadapter | where {$_.ifdesc.startswith('Microsoft Hyper-V Network Adapter')} | Set-NetIPInterface -DHCP Enabled`

`(get-wmiobject Win32_NetworkAdapterConfiguration -filter "ServiceName='netvsc'").EnableDHCP()`

`Get-NetAdapter` jest dostępny w 2012 +. Do użycia w 2008R2 `Get-WmiObject` . Maszyny wirtualne platformy Azure należy zawsze skonfigurować w systemie operacyjnym gościa, aby można było uzyskać adres IP przy użyciu protokołu DHCP. Ustawienie statycznego adresu IP platformy Azure nadal używa protokołu DHCP do nadawania adresu IP do maszyny wirtualnej.
### <a name="ping"></a>Ping
`test-netconnection`

> [!NOTE]
> Write-Progress polecenie cmdlet może nie współpracować z tym poleceniem. Jako środek zaradczy, można uruchomić polecenie `$ProgressPreference = "SilentlyContinue"` w programie PowerShell, aby wyłączyć pasek postępu.

lub

`get-wmiobject Win32_PingStatus -Filter 'Address="8.8.8.8"' | format-table -autosize IPV4Address,ReplySize,ResponseTime`

`Test-Netconnection` bez żadnych parametrów spróbuje wysłać polecenie ping `internetbeacon.msedge.net` . Jest on dostępny w 2012 +. W przypadku 2008R2 Użyj `Get-WmiObject` jak w drugim przykładzie.
### <a name="port-ping"></a>Port ping
`test-netconnection -ComputerName bing.com -Port 80`

lub

`(new-object Net.Sockets.TcpClient).BeginConnect('bing.com','80',$null,$null).AsyncWaitHandle.WaitOne(300)`

`Test-NetConnection` jest dostępny w 2012 +. Do użycia w 2008R2 `Net.Sockets.TcpClient`
### <a name="test-dns-name-resolution"></a>Testowanie rozpoznawania nazw DNS
`resolve-dnsname bing.com`

lub

`[System.Net.Dns]::GetHostAddresses('bing.com')`

`Resolve-DnsName` jest dostępny w 2012 +. Do użycia w 2008R2 `System.Net.DNS` .
### <a name="show-windows-firewall-rule-by-name"></a>Pokaż regułę zapory systemu Windows według nazwy
`get-netfirewallrule -name RemoteDesktop-UserMode-In-TCP`
### <a name="show-windows-firewall-rule-by-port"></a>Pokaż regułę zapory systemu Windows według portu
`get-netfirewallportfilter | where {$_.localport -eq 3389} | foreach {Get-NetFirewallRule -Name $_.InstanceId} | format-list Name,Enabled,Profile,Direction,Action`

lub

`(new-object -ComObject hnetcfg.fwpolicy2).rules | where {$_.localports -eq 3389 -and $_.direction -eq 1} | format-table Name,Enabled`

`Get-NetFirewallPortFilter` jest dostępny w 2012 +. Dla 2008R2 Użyj `hnetcfg.fwpolicy2` obiektu com.
### <a name="disable-windows-firewall"></a>Wyłącz zaporę systemu Windows
`Set-NetFirewallProfile -Profile Domain,Public,Private -Enabled False`

`Set-NetFirewallProfile` jest dostępny w 2012 +. Do użycia 2008R2 `netsh advfirewall` jako odwołania w powyższej sekcji cmd.
## <a name="manage-users-and-groups-using-powershell"></a>Zarządzanie użytkownikami i grupami przy użyciu programu PowerShell
### <a name="create-local-user-account"></a>Utwórz konto użytkownika lokalnego
`new-localuser <name>`
### <a name="verify-user-account-is-enabled"></a>Sprawdź, czy konto użytkownika jest włączone
`(get-localuser | where {$_.SID -like "S-1-5-21-*-500"}).Enabled`

lub

`(get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'").Disabled`

`Get-LocalUser` jest dostępny w 2012 +. Do użycia w 2008R2 `Get-WmiObject` . W tym przykładzie pokazano wbudowane konto administratora lokalnego, które ma zawsze identyfikator SID `S-1-5-21-*-500` . Maszyny wirtualne platformy Azure utworzone na podstawie uogólnionego obrazu będą miały nazwę konta administratora lokalnego o nazwie określonej podczas aprowizacji maszyny wirtualnej. W związku z tym zwykle nie będzie `Administrator` .
### <a name="add-local-user-to-local-group"></a>Dodaj użytkownika lokalnego do grupy lokalnej
`add-localgroupmember -group Administrators -member <username>`
### <a name="enable-local-user-account"></a>Włącz konto użytkownika lokalnego
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | enable-localuser`

W tym przykładzie włączono wbudowane konto administratora lokalnego, które ma zawsze identyfikator SID `S-1-5-21-*-500` . Maszyny wirtualne platformy Azure utworzone na podstawie uogólnionego obrazu będą miały nazwę konta administratora lokalnego o nazwie określonej podczas aprowizacji maszyny wirtualnej. W związku z tym zwykle nie będzie `Administrator` .
### <a name="view-user-account-properties"></a>Wyświetl właściwości konta użytkownika
`get-localuser | where {$_.SID -like "S-1-5-21-*-500"} | format-list *`

lub

`get-wmiobject Win32_UserAccount -Namespace "root\cimv2" -Filter "SID like 'S-1-5-%-500'" |  format-list Name,Disabled,Status,Lockout,Description,SID`

`Get-LocalUser` jest dostępny w 2012 +. Do użycia w 2008R2 `Get-WmiObject` . W tym przykładzie pokazano wbudowane konto administratora lokalnego, które ma zawsze identyfikator SID `S-1-5-21-*-500` .
### <a name="view-local-groups"></a>Wyświetlanie grup lokalnych
`(get-localgroup).name | sort` `(get-wmiobject win32_group).Name | sort`

`Get-LocalUser` jest dostępny w 2012 +. Do użycia w 2008R2 `Get-WmiObject` .
## <a name="manage-the-windows-event-log-using-powershell"></a>Zarządzanie dziennikiem zdarzeń systemu Windows przy użyciu programu PowerShell
### <a name="query-event-log-errors"></a>Rejestruj błędy dziennika zdarzeń
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Level=2]]" | more`

Zmień `/c:10` żądaną liczbę zdarzeń do zwrócenia lub przenieś ją, aby zwracała wszystkie zdarzenia zgodne z filtrem.
### <a name="query-event-log-by-event-id"></a>Zapytanie dziennika zdarzeń według identyfikatora zdarzenia
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider"></a>Zapytanie dziennika zdarzeń według identyfikatora zdarzenia i dostawcy
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11]]" | more`
### <a name="query-event-log-by-event-id-and-provider-for-the-last-24-hours"></a>Zbadaj dziennik zdarzeń według identyfikatora zdarzenia i dostawcy w ciągu ostatnich 24 godzin
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Hyper-V-Netvsc'] and EventID=11 and TimeCreated[timediff(@SystemTime) <= 86400000]]]"`

Użyj `604800000` , aby odszukać 7 dni zamiast 24 godzin. |
### <a name="query-event-log-by-event-id-provider-and-eventdata-in-the-last-7-days"></a>Zbadaj dziennik zdarzeń według identyfikatora zdarzenia, dostawcy i EventData w ciągu ostatnich 7 dni
`get-winevent -logname system -maxevents 1 -filterxpath "*[System[Provider[@Name='Microsoft-Windows-Security-Auditing'] and EventID=4624 and TimeCreated[timediff(@SystemTime) <= 604800000]] and EventData[Data[@Name='TargetUserName']='<username>']]" | more`
## <a name="view-or-remove-installed-applications-using-powershell"></a>Wyświetlanie lub usuwanie zainstalowanych aplikacji przy użyciu programu PowerShell
### <a name="list-installed-software"></a>Wyświetl listę zainstalowanego oprogramowania
`get-wmiobject win32_product | select installdate,name | sort installdate -descending | more`
### <a name="uninstall-software"></a>Odinstaluj oprogramowanie
`(get-wmiobject win32_product -filter "Name='<name>'").Uninstall()`
## <a name="file-system-management-using-powershell"></a>Zarządzanie systemem plików przy użyciu programu PowerShell
### <a name="get-file-version"></a>Pobierz wersję pliku
`(get-childitem $env:windir\system32\drivers\netvsc*.sys).VersionInfo.FileVersion`

Ten przykład zwraca wersję pliku sterownika wirtualnej karty sieciowej o nazwie netvsc.sys, netvsc63.sys lub netvsc60.sys w zależności od wersji systemu Windows.
### <a name="download-and-extract-file"></a>Pobierz i wyodrębnij plik
`$path='c:\bin';md $path;cd $path;(new-object net.webclient).downloadfile( ('htTp:/'+'/download.sysinternals.com/files/SysinternalsSuite.zip'),"$path\SysinternalsSuite.zip");(new-object -com shelL.apPlication).namespace($path).CopyHere( (new-object -com shelL.apPlication).namespace("$path\SysinternalsSuite.zip").Items(),16)`

W tym przykładzie tworzony jest `c:\bin` folder, a następnie pobiera i wyodrębnia pakiet narzędzi Sysinternals do programu `c:\bin` .
## <a name="miscellaneous-tasks-using-powershell"></a>Różne zadania przy użyciu programu PowerShell
### <a name="show-os-version"></a>Pokaż wersję systemu operacyjnego
`get-wmiobject win32_operatingsystem | format-list caption,version,buildnumber`
### <a name="view-os-install-date"></a>Wyświetl datę instalacji systemu operacyjnego
`(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).installdate)`
### <a name="view-last-boot-time"></a>Wyświetl czas ostatniego rozruchu
`(get-wmiobject win32_operatingsystem).lastbootuptime`
### <a name="view-windows-uptime"></a>Wyświetl czas działania systemu Windows
`"{0:dd}:{0:hh}:{0:mm}:{0:ss}.{0:ff}" -f ((get-date)-(get-wmiobject win32_operatingsystem).converttodatetime((get-wmiobject win32_operatingsystem).lastbootuptime))`

Zwraca czas działania jak `<days>:<hours>:<minutes>:<seconds>:<milliseconds>` na przykład `49:16:48:00.00` .
### <a name="restart-windows"></a>Ponowne uruchamianie systemu Windows
`restart-computer`

Dodanie `-force` spowoduje wymuszenie zamknięcia uruchomionych aplikacji bez ostrzeżenia użytkowników.
## <a name="instance-metadata"></a>Metadane wystąpienia

Metadane wystąpienia platformy Azure można badać z poziomu maszyny wirtualnej platformy Azure, aby wyświetlić szczegółowe informacje, takie jak osType, Location, vmSize, VM, Name, resourceGroupName, subskrypcji, privateIpAddress i publicIpAddress.

Wykonywanie zapytań dotyczących metadanych wystąpienia wymaga zdrowej łączności sieciowej gościa, ponieważ powoduje wywołanie REST za pośrednictwem hosta platformy Azure do usługi metadanych wystąpienia. Dlatego jeśli możesz wysyłać zapytania o metadane wystąpienia, oznacza to, że gość może komunikować się za pośrednictwem sieci z usługą hostowaną na platformie Azure.

Aby uzyskać więcej informacji, zobacz [Usługa metadanych wystąpienia platformy Azure](../windows/instance-metadata-service.md).

### <a name="instance-metadata"></a>Metadane wystąpienia
`$im = invoke-restmethod -headers @{"metadata"="true"} -uri http://169.254.169.254/metadata/instance?api-version=2017-08-01 -method get`

`$im | convertto-json`
### <a name="os-type-instance-metadata"></a>Typ systemu operacyjnego (metadane wystąpienia)
`$im.Compute.osType`
### <a name="location-instance-metadata"></a>Lokalizacja (metadane wystąpienia)
`$im.Compute.Location`
### <a name="size-instance-metadata"></a>Rozmiar (metadane wystąpienia)
`$im.Compute.vmSize`
### <a name="vm-id-instance-metadata"></a>Identyfikator maszyny wirtualnej (metadane wystąpienia)
`$im.Compute.vmId`
### <a name="vm-name-instance-metadata"></a>Nazwa maszyny wirtualnej (metadane wystąpienia)
`$im.Compute.name`
### <a name="resource-group-name-instance-metadata"></a>Nazwa grupy zasobów (metadane wystąpienia)
`$im.Compute.resourceGroupName`
### <a name="subscription-id-instance-metadata"></a>Identyfikator subskrypcji (metadane wystąpienia)
`$im.Compute.subscriptionId`
### <a name="tags-instance-metadata"></a>Tagi (metadane wystąpienia)
`$im.Compute.tags`
### <a name="placement-group-id-instance-metadata"></a>Identyfikator grupy umieszczania (metadane wystąpienia)
`$im.Compute.placementGroupId`
### <a name="platform-fault-domain-instance-metadata"></a>Domena błędów platformy (metadane wystąpienia)
`$im.Compute.platformFaultDomain`
### <a name="platform-update-domain-instance-metadata"></a>Domena aktualizacji platformy (metadane wystąpienia)
`$im.Compute.platformUpdateDomain`
### <a name="ipv4-private-ip-address-instance-metadata"></a>Prywatny adres IP IPv4 (metadane wystąpienia)
`$im.network.interface.ipv4.ipAddress.privateIpAddress`
### <a name="ipv4-public-ip-address-instance-metadata"></a>Publiczny adres IP IPv4 (metadane wystąpienia)
`$im.network.interface.ipv4.ipAddress.publicIpAddress`
### <a name="ipv4-subnet-address--prefix-instance-metadata"></a>Adres/prefiks podsieci IPv4 (metadane wystąpienia)
`$im.network.interface.ipv4.subnet.address`

`$im.network.interface.ipv4.subnet.prefix`
### <a name="ipv6-ip-address-instance-metadata"></a>Adres IP IPv6 (metadane wystąpienia)
`$im.network.interface.ipv6.ipAddress`
### <a name="mac-address-instance-metadata"></a>Adres MAC (metadane wystąpienia)
`$im.network.interface.macAddress`

## <a name="next-steps"></a>Następne kroki
* Strona dokumentacji głównej konsoli szeregowej systemu Windows znajduje się [tutaj](serial-console-windows.md).
* Konsola szeregowa jest również dostępna dla maszyn wirtualnych z [systemem Linux](serial-console-linux.md) .
* Dowiedz się więcej na temat [diagnostyki rozruchu](boot-diagnostics.md).
