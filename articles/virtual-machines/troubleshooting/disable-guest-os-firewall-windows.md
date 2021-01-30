---
title: Wyłączanie zapory systemu operacyjnego gościa na maszynie wirtualnej platformy Azure | Microsoft Docs
description: Zapoznaj się z metodą obejścia problemu w sytuacjach, w których Zapora systemu operacyjnego gościa filtruje częściowy lub kompletny ruch do maszyny wirtualnej.
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 11/22/2018
ms.author: delhan
ms.openlocfilehash: 74d06d3d4aaa0d76b80257d2148fb62f71c3fdb0
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99093199"
---
# <a name="disable-the-guest-os-firewall-in-azure-vm"></a>Wyłączanie zapory systemu operacyjnego gościa na maszynie wirtualnej platformy Azure

Ten artykuł zawiera informacje dotyczące sytuacji, w których można podejrzewać, że Zapora systemu operacyjnego gościa filtruje częściowy lub kompletny ruch do maszyny wirtualnej. Taka sytuacja może wystąpić, jeśli zostały celowo wprowadzone zmiany w zaporze, która spowodowała niepowodzenie połączeń RDP.

## <a name="solution"></a>Rozwiązanie

Proces opisany w tym artykule jest przeznaczony do użycia jako obejście, aby można było skupić się na rozwiązywaniu rzeczywistego problemu, co umożliwia poprawne skonfigurowanie reguł zapory. Najlepszym rozwiązaniem firmy Microsoft jest włączenie składnika Zapora systemu Windows. Sposób konfigurowania reguł zapory zależy od poziomu dostępu do wymaganej maszyny wirtualnej.

### <a name="online-solutions"></a>Rozwiązania online 

Jeśli maszyna wirtualna jest w trybie online i jest dostępna na innej maszynie wirtualnej w tej samej sieci wirtualnej, można wykonać te środki zaradcze przy użyciu innej maszyny wirtualnej.

#### <a name="mitigation-1-custom-script-extension-or-run-command-feature"></a>Ograniczenie 1: niestandardowe rozszerzenie skryptu lub funkcja uruchamiania polecenia

Jeśli korzystasz z działającego agenta platformy Azure, możesz użyć [niestandardowego rozszerzenia skryptu](../extensions/custom-script-windows.md) lub funkcji [Run Commands](../windows/run-command.md) (tylko Menedżer zasobów VM), aby zdalnie uruchamiać następujące skrypty.

> [!Note]
> * Jeśli Zapora jest ustawiona lokalnie, uruchom następujący skrypt:
>   ```
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\Standardprofile' -name "EnableFirewall" -Value 0 
>   Restart-Service -Name mpssvc
>   ```
> * Jeśli Zapora jest ustawiona za pomocą zasad Active Directory, możesz użyć następującego skryptu, aby uzyskać tymczasowy dostęp. 
>   ```
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile' -name "EnableFirewall" -Value 0
>   Set-ItemProperty -Path 'HKLM:\SOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile' -name "EnableFirewall" -Value 0
>   Restart-Service -Name mpssvc
>   ```
>   Jednak zaraz po ponownym zastosowaniu zasad zostanie rozpoczęta Sesja zdalna. Nieodwracalna poprawka tego problemu polega na zmodyfikowaniu zasad, które są stosowane na tym komputerze.

#### <a name="mitigation-2-remote-powershell"></a>Środki zaradcze 2: zdalne środowisko PowerShell

1.  Połącz się z MASZYNą wirtualną znajdującą się w tej samej sieci wirtualnej co maszyna wirtualna, której nie można połączyć przy użyciu połączenia RDP.

2.  Otwórz okno konsoli programu PowerShell.

3.  Uruchom następujące polecenia:

    ```powershell
    Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
    netsh advfirewall set allprofiles state off
    Restart-Service -Name mpssvc 
    exit
    ```

> [!Note]
> Jeśli Zapora jest ustawiona za pomocą obiektu zasady grupy, ta metoda może nie zadziałać, ponieważ to polecenie zmienia tylko lokalne wpisy rejestru. Jeśli zasady są stosowane, ta zmiana zostanie zastąpiona. 

#### <a name="mitigation-3-pstools-commands"></a>Środki zaradcze 3: program PsTools polecenia

1.  Na maszynie wirtualnej Rozwiązywanie problemów Pobierz [program PsTools](/sysinternals/downloads/pstools).

2.  Otwórz wystąpienie programu CMD, a następnie uzyskaj dostęp do maszyny wirtualnej za pomocą jej adresu DIP.

3.  Uruchom następujące polecenia:

    ```cmd
    psexec \\<DIP> -u <username> cmd
    netsh advfirewall set allprofiles state off
    psservice restart mpssvc
    ```

#### <a name="mitigation-4-remote-registry"></a>Łagodzenie 4: Rejestr zdalny 

Wykonaj następujące kroki, aby użyć [rejestru zdalnego](https://www.betaarchive.com/wiki/index.php?title=Microsoft_KB_Archive/314837).

1.  Na maszynie wirtualnej Rozwiązywanie problemów Uruchom Edytor rejestru, a następnie przejdź do **pliku**  >  **Connect Network Registry**.

2.  Otwórz gałąź \System *maszyny docelowej* i określ następujące wartości:

    ```
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile\EnableFirewall           -->        0 
    <TARGET MACHINE>\SYSTEM\CurrentControlSet\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile\EnableFirewall         -->        0
    ```

3.  Należy ponownie uruchomić usługę. Ponieważ nie można tego zrobić za pomocą zdalnego rejestru, należy użyć konsoli usług zdalnych.

4.  Otwórz wystąpienie programu **Services. msc**.

5.  Kliknij pozycję **usługi (lokalne)**.

6.  Wybierz pozycję **Połącz z innym komputerem**.

7.  Wprowadź **prywatny adres IP (DIP)** maszyny wirtualnej problemu.

8.  Uruchom ponownie lokalne zasady zapory.

9.  Spróbuj ponownie nawiązać połączenie z maszyną wirtualną za pośrednictwem protokołu RDP z komputera lokalnego.

### <a name="offline-solutions"></a>Rozwiązania w trybie offline 

Jeśli masz sytuację, w której nie można nawiązać połączenia z maszyną wirtualną za pomocą żadnej metody, rozszerzenie niestandardowego skryptu zakończy się niepowodzeniem i będzie trzeba pracować w trybie OFFLINE, pracując bezpośrednio na dysku systemowym. W tym celu wykonaj następujące kroki:

1.  [Dołącz dysk systemowy do maszyny wirtualnej odzyskiwania](troubleshoot-recovery-disks-portal-windows.md).

2.  Uruchom Pulpit zdalny połączenie z maszyną wirtualną odzyskiwania.

3.  Upewnij się, że dysk jest oflagowany jako online w konsoli Zarządzanie dyskami. Zanotuj literę dysku przypisaną do dołączonego dysku systemowego.

4.  Przed wprowadzeniem jakichkolwiek zmian Utwórz kopię folderu \Windows\System32\Config w przypadku, gdy konieczne jest wycofanie zmian.

5.  Na maszynie wirtualnej Rozwiązywanie problemów Uruchom Edytor rejestru (regedit.exe). 

6.  W ramach tej procedury rozwiązywania problemów instalujemy gałęzie jako BROKENSYSTEM i BROKENSOFTWARE.

7.  Zaznacz klucz HKEY_LOCAL_MACHINE, a następnie wybierz pozycję plik > Załaduj gałąź z menu.

8.  Zlokalizuj plik \windows\system32\config\SYSTEM na dołączonym dysku systemowym.

9.  Otwórz wystąpienie programu PowerShell z podwyższonym poziomem uprawnień, a następnie uruchom następujące polecenia:

    ```cmd
    # Load the hives - If your attached disk is not F, replace the letter assignment here
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM
    reg load HKLM\BROKENSOFTWARE f:\windows\system32\config\SOFTWARE 
    # Disable the firewall on the local policy
    $ControlSet = (get-ItemProperty -Path 'HKLM:\BROKENSYSTEM\Select' -name "Current").Current
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'BROKENSYSTEM\ControlSet00'+$ControlSet+'\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # To ensure the firewall is not set through AD policy, check if the following registry entries exist and if they do, then check if the following entries exist:
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\DomainProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\PublicProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    $key = 'HKLM:\BROKENSOFTWARE\Policies\Microsoft\WindowsFirewall\StandardProfile'
    Set-ItemProperty -Path $key -name 'EnableFirewall' -Value 0 -Type Dword -force
    # Unload the hives
    reg unload HKLM\BROKENSYSTEM
    reg unload HKLM\BROKENSOFTWARE
    ```

10. [Odłącz dysk systemowy i Utwórz ponownie maszynę wirtualną](troubleshoot-recovery-disks-portal-windows.md).

11. Sprawdź, czy problem został rozwiązany.
