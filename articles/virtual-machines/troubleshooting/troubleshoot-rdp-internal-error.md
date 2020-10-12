---
title: Wystąpił błąd wewnętrzny podczas nawiązywania połączenia RDP z platformą Azure Virtual Machines | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy wewnętrzne protokołu RDP w Microsoft Azure. | Microsoft Docs
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/22/2018
ms.author: genli
ms.openlocfilehash: 7cbb67a215d44759b2b503929c37cb50ea94709c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90069768"
---
#  <a name="an-internal-error-occurs-when-you-try-to-connect-to-an-azure-vm-through-remote-desktop"></a>An internal error occurs when you try to connect to an Azure VM through Remote Desktop (Podczas próby połączenia z maszyną wirtualną platformy Azure za pośrednictwem pulpitu zdalnego występuje błąd wewnętrzny)

W tym artykule opisano błąd, który może wystąpić podczas próby nawiązania połączenia z maszyną wirtualną (VM) w Microsoft Azure.


## <a name="symptoms"></a>Objawy

Nie można nawiązać połączenia z maszyną wirtualną platformy Azure przy użyciu protokołu Remote Desktop Protocol (RDP). Połączenie zostanie zablokowane w sekcji **Konfigurowanie zdalne** lub zostanie wyświetlony następujący komunikat o błędzie:

- Wewnętrzny błąd protokołu RDP
- Wystąpił błąd wewnętrzny
- Ten komputer nie może być połączony z komputerem zdalnym. Spróbuj ponownie nawiązać połączenie. Jeśli problem będzie się powtarzać, skontaktuj się z właścicielem komputera zdalnego lub administratorem sieci


## <a name="cause"></a>Przyczyna

Ten problem może wystąpić z następujących powodów:

- Być może zaatakowano maszynę wirtualną.
- Nie można uzyskać dostępu do lokalnych kluczy szyfrowania RSA.
- Protokół TLS jest wyłączony.
- Certyfikat jest uszkodzony lub wygasł.

## <a name="solution"></a>Rozwiązanie

Aby rozwiązać ten problem, wykonaj kroki opisane w poniższych sekcjach. Przed rozpoczęciem Utwórz migawkę dysku systemu operacyjnego z zaatakowaną maszyną wirtualną jako kopię zapasową. Aby uzyskać więcej informacji, zobacz [migawka dysku](../windows/snapshot-copy-managed-disk.md).

### <a name="check-rdp-security"></a>Sprawdź zabezpieczenia protokołu RDP

Najpierw sprawdź, czy sieciowa Grupa zabezpieczeń dla portu RDP 3389 jest niezabezpieczona (Otwórz). Jeśli jest ona niezabezpieczona i jest wyświetlana \* jako źródłowy adres IP dla ruchu przychodzącego, Ogranicz port RDP do adresu IP specifc użytkownika, a następnie przetestuj dostęp do protokołu RDP. Jeśli to się nie powiedzie, wykonaj kroki opisane w następnej sekcji.

### <a name="use-serial-control"></a>Użyj kontrolki szeregowej

Użyj konsoli szeregowej lub [napraw maszynę wirtualną w trybie offline](#repair-the-vm-offline) , dołączając dysk systemu operacyjnego maszyny wirtualnej do maszyny wirtualnej odzyskiwania.

Aby rozpocząć, nawiąż połączenie z [konsolą szeregową i Otwórz wystąpienie programu PowerShell](./serial-console-windows.md#use-cmd-or-powershell-in-serial-console
). Jeśli konsola szeregowa nie jest włączona na maszynie wirtualnej, przejdź do sekcji [Naprawa maszyny wirtualnej w trybie offline](#repair-the-vm-offline) .

#### <a name="step-1-check-the-rdp-port"></a>Krok: 1 Sprawdź port RDP

1. W wystąpieniu programu PowerShell Użyj polecenia [netstat](/windows-server/administration/windows-commands/netstat) , aby sprawdzić, czy port 3389 jest używany przez inne aplikacje:

    ```powershell
    Netstat -anob |more
    ```

2. Jeśli Termservice.exe korzysta z portu 3389, przejdź do kroku 2. Jeśli inna usługa lub aplikacja inna niż Termservice.exe korzysta z portu 3389, wykonaj następujące czynności:

    1. Zatrzymaj usługę dla aplikacji korzystającej z usługi 3389:

        ```powershell
        Stop-Service -Name <ServiceName> -Force
        ```

    2. Uruchom usługę terminalową:

        ```powershell
        Start-Service -Name Termservice
        ```

2. Jeśli nie można zatrzymać aplikacji lub jeśli ta metoda nie dotyczy użytkownika, zmień port dla protokołu RDP:

    1. Zmień port:

        ```powershell
        Set-ItemProperty -Path 'HKLM\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -name PortNumber -value <Hexportnumber>

        Stop-Service -Name Termservice -Force

        Start-Service -Name Termservice
        ```

    2. Ustaw zaporę dla nowego portu:

        ```powershell
        Set-NetFirewallRule -Name "RemoteDesktop-UserMode-In-TCP" -LocalPort <NEW PORT (decimal)>
        ```

    3. [Zaktualizuj grupę zabezpieczeń sieci dla nowego portu](../../virtual-network/security-overview.md) w Azure Portal port RDP.

#### <a name="step-2-set-correct-permissions-on-the-rdp-self-signed-certificate"></a>Krok 2. Ustawianie prawidłowych uprawnień dla certyfikatu z podpisem własnym RDP

1. W wystąpieniu programu PowerShell uruchom następujące polecenia, aby odnowić certyfikat z podpisem własnym RDP:

    ```powershell
    Import-Module PKI

    Set-Location Cert:\LocalMachine 

    $RdpCertThumbprint = 'Cert:\LocalMachine\Remote Desktop\'+((Get-ChildItem -Path 'Cert:\LocalMachine\Remote Desktop\').thumbprint) 

    Remove-Item -Path $RdpCertThumbprint

    Stop-Service -Name "SessionEnv"

    Start-Service -Name "SessionEnv"
    ```

2. Jeśli nie można odnowić certyfikatu za pomocą tej metody, spróbuj odnowić certyfikat z podpisem własnym protokołu RDP zdalnie:

    1. Z działającej maszyny wirtualnej, która ma łączność z maszyną wirtualną, na której występują problemy, wpisz **MMC** w polu **Uruchom** , aby otworzyć program Microsoft Management Console.
    2. W menu **plik** wybierz pozycję **Dodaj/Usuń przystawkę**, wybierz pozycję **Certyfikaty**, a następnie wybierz pozycję **Dodaj**.
    3. Wybierz pozycję **konta komputerów**, wybierz **inny komputer**, a następnie Dodaj adres IP maszyny wirtualnej problemu.
    4. Przejdź do folderu **Remote Desktop\Certificates** , kliknij prawym przyciskiem myszy certyfikat, a następnie wybierz polecenie **Usuń**.
    5. W wystąpieniu programu PowerShell z poziomu konsoli szeregowej Uruchom ponownie usługę konfiguracji Pulpit zdalny:

        ```powershell
        Stop-Service -Name "SessionEnv"

        Start-Service -Name "SessionEnv"
        ```

3. Zresetuj uprawnienie do folderu MachineKeys.

    ```powershell
    remove-module psreadline icacls

    md c:\temp

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt 

    takeown /f "C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

    icacls C:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt 

    Restart-Service TermService -Force
    ```

4. Uruchom ponownie maszynę wirtualną, a następnie spróbuj uruchomić Pulpit zdalny połączenie z maszyną wirtualną. Jeśli błąd nadal występuje, przejdź do następnego kroku.

#### <a name="step-3-enable-all-supported-tls-versions"></a>Krok 3. włączenie wszystkich obsługiwanych wersji protokołu TLS

Klient RDP domyślnie używa protokołu TLS 1,0. Można go jednak zmienić na TLS 1,1, który stał się nowym standardem. Jeśli na maszynie wirtualnej jest wyłączone szyfrowanie TLS 1,1, połączenie zakończy się niepowodzeniem.

1. W wystąpieniu polecenia CMD Włącz protokół TLS:

    ```console
    reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    reg add "HKLM\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
    ```

2. Aby zapobiec zastąpieniu zmian przez zasady usługi AD, Zatrzymaj aktualizację zasad grupy tymczasowo:

    ```console
    REG add "HKLM\SYSTEM\CurrentControlSet\Services\gpsvc" /v Start /t REG_DWORD /d 4 /f
    ```

3. Uruchom ponownie maszynę wirtualną, aby zmiany zaczęły obowiązywać. Jeśli problem został rozwiązany, uruchom następujące polecenie, aby ponownie włączyć zasady grupy:

    ```console
    sc config gpsvc start= auto sc start gpsvc

    gpupdate /force
    ```

    Jeśli zmiana zostanie wycofana, oznacza to, że w domenie firmy znajdują się zasady Active Directory. Należy zmienić te zasady, aby uniknąć ponownego wystąpienia tego problemu.

### <a name="repair-the-vm-offline"></a>Naprawianie maszyny wirtualnej w trybie offline

#### <a name="attach-the-os-disk-to-a-recovery-vm"></a>Dołączanie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania

1. [Dołącz dysk systemu operacyjnego do maszyny wirtualnej odzyskiwania](./troubleshoot-recovery-disks-portal-windows.md).
2. Po dołączeniu dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania upewnij się, że dysk jest oznaczony jako **online** w konsoli Zarządzanie dyskami. Zanotuj literę dysku przypisaną do dołączonego dysku systemu operacyjnego.
3. Uruchom Pulpit zdalny połączenie z maszyną wirtualną odzyskiwania.

#### <a name="enable-dump-log-and-serial-console"></a>Włącz dziennik zrzutów i konsolę seryjną

Aby włączyć dziennik zrzutów i konsolę szeregową, uruchom następujący skrypt.

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (**Uruchom jako administrator**).
2. Uruchom poniższy skrypt:

    W tym skrypcie Załóżmy, że litera dysku przypisana do dołączonego dysku systemu operacyjnego to F. Zastąp tę literę dysku odpowiednią wartością dla maszyny wirtualnej.

    ```console
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REM Enable Serial Console
    bcdedit /store F:\boot\bcd /set {bootmgr} displaybootmenu yes
    bcdedit /store F:\boot\bcd /set {bootmgr} timeout 5
    bcdedit /store F:\boot\bcd /set {bootmgr} bootems yes
    bcdedit /store F:\boot\bcd /ems {<BOOT LOADER IDENTIFIER>} ON
    bcdedit /store F:\boot\bcd /emssettings EMSPORT:1 EMSBAUDRATE:115200

    REM Suggested configuration to enable OS Dump
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v CrashDumpEnabled /t REG_DWORD /d 1 /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v DumpFile /t REG_EXPAND_SZ /d "%SystemRoot%\MEMORY.DMP" /f
    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\CrashControl" /v NMICrashDump /t REG_DWORD /d 1 /f

    reg unload HKLM\BROKENSYSTEM
    ```

#### <a name="reset-the-permission-for-machinekeys-folder"></a>Zresetuj uprawnienie do folderu MachineKeys

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (**Uruchom jako administrator**).
2. Uruchom poniższy skrypt. W tym skrypcie Załóżmy, że litera dysku przypisana do dołączonego dysku systemu operacyjnego to F. Zastąp tę literę dysku odpowiednią wartością dla maszyny wirtualnej.

    ```console
    Md F:\temp

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\BeforeScript_permissions.txt

    takeown /f "F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys" /a /r

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\System:(F)"

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "NT AUTHORITY\NETWORK SERVICE:(R)"

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c /grant "BUILTIN\Administrators:(F)"

    icacls F:\ProgramData\Microsoft\Crypto\RSA\MachineKeys /t /c > c:\temp\AfterScript_permissions.txt
    ```

#### <a name="enable-all-supported-tls-versions"></a>Włącz wszystkie obsługiwane wersje protokołu TLS

1. Otwórz sesję wiersza polecenia z podwyższonym poziomem uprawnień (**Uruchom jako administrator**), a następnie uruchom następujące polecenia. W poniższym skrypcie założono, że litera sterownika jest przypisana do dołączonego dysku systemu operacyjnego, to F. Zastąp tę literę dysku odpowiednią wartością dla maszyny wirtualnej.
2. Sprawdź, który protokół TLS jest włączony:

    ```console
    reg load HKLM\BROKENSYSTEM F:\windows\system32\config\SYSTEM.hiv

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWO
    ```

3. Jeśli klucz nie istnieje lub jego wartość wynosi **0**, Włącz protokół, uruchamiając następujące skrypty:

    ```console
    REM Enable TLS 1.0, TLS 1.1 and TLS 1.2

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.0\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.1\Server" /v Enabled /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server" /v Enabled /t REG_DWORD /d 1 /f
    ```

4. Włącz uwierzytelnianie NLA:

    ```console
    REM Enable NLA

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet001\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v SecurityLayer /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v UserAuthentication /t REG_DWORD /d 1 /f

    REG ADD "HKLM\BROKENSYSTEM\ControlSet002\Control\Terminal Server\WinStations\RDP-Tcp" /v fAllowSecProtocolNegotiation /t REG_DWORD /d 1 /f reg unload HKLM\BROKENSYSTEM
    ```

5. [Odłącz dysk systemu operacyjnego i Utwórz ponownie maszynę wirtualną](./troubleshoot-recovery-disks-portal-windows.md), a następnie sprawdź, czy problem został rozwiązany.
