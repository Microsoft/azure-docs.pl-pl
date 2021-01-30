---
title: Włączanie lub wyłączanie reguły zapory w systemie operacyjnym gościa na maszynie wirtualnej platformy Azure | Microsoft Docs
description: Dowiedz się, jak włączyć lub wyłączyć reguły zapory systemu operacyjnego gościa na zdalnej maszynie wirtualnej platformy Azure za pomocą usług online lub offline.
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
ms.openlocfilehash: 9ee27f429dbfd1e550a45bbc26413a1c259c4fbe
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99092372"
---
# <a name="enable-or-disable-a-firewall-rule-on-an-azure-vm-guest-os"></a>Włączanie lub wyłączanie reguły zapory w systemie operacyjnym gościa maszyny wirtualnej platformy Azure

Ten artykuł zawiera informacje dotyczące rozwiązywania problemów z sytuacją, w której można podejrzewać, że Zapora systemu operacyjnego gościa filtruje częściowy ruch na maszynie wirtualnej. Może to być przydatne z następujących powodów:

*   Jeśli zmiana została celowo wykonana w zaporze, która spowodowała niepowodzenie połączeń RDP, użycie funkcji niestandardowego rozszerzenia skryptu może rozwiązać ten problem.

*   Wyłączenie wszystkich profilów zapory jest bardziej foolproof sposobem rozwiązywania problemów niż ustawienie reguły zapory dotyczącej protokołu RDP.

## <a name="solution"></a>Rozwiązanie

Sposób konfigurowania reguł zapory zależy od poziomu dostępu do wymaganej maszyny wirtualnej. W poniższych przykładach użyto reguł RDP. Jednak te same metody można zastosować do dowolnego innego rodzaju ruchu, wskazując prawidłowy klucz rejestru.

### <a name="online-troubleshooting"></a>Rozwiązywanie problemów online 

#### <a name="mitigation-1-custom-script-extension"></a>Środki zaradcze 1: niestandardowe rozszerzenie skryptu

1.  Utwórz skrypt przy użyciu poniższego szablonu.

    *   Aby włączyć regułę:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Aby wyłączyć regułę:
        ```cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

2.  Przekaż ten skrypt do Azure Portal przy użyciu funkcji [niestandardowego rozszerzenia skryptu](../extensions/custom-script-windows.md) . 

#### <a name="mitigation-2-remote-powershell"></a>Środki zaradcze 2: zdalne środowisko PowerShell

Jeśli maszyna wirtualna jest w trybie online i jest dostępna na innej maszynie wirtualnej w tej samej sieci wirtualnej, możesz zastosować środki zaradcze przy użyciu innej maszyny wirtualnej.

1.  Na maszynie wirtualnej Rozwiązywanie problemów Otwórz okno konsoli programu PowerShell.

2.  Uruchom następujące polecenia, zgodnie z potrzebami.

    *   Aby włączyć regułę:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Enable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

    *   Aby wyłączyć regułę:
        ```powershell
        Enter-PSSession (New-PSSession -ComputerName "<HOSTNAME>" -Credential (Get-Credential) -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck)) 
        Disable-NetFirewallRule -DisplayName  "RemoteDesktop-UserMode-In-TCP"
        exit
        ```

#### <a name="mitigation-3-pstools-commands"></a>Środki zaradcze 3: program PsTools polecenia

Jeśli maszyna wirtualna jest w trybie online i jest dostępna na innej maszynie wirtualnej w tej samej sieci wirtualnej, możesz zastosować środki zaradcze przy użyciu innej maszyny wirtualnej.

1.  Na maszynie wirtualnej Rozwiązywanie problemów Pobierz [program PsTools](/sysinternals/downloads/pstools).

2.  Otwórz wystąpienie programu CMD i uzyskaj dostęp do maszyny wirtualnej za pomocą wewnętrznego adresu IP (DIP). 

    * Aby włączyć regułę:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=yes
        ```

    *   Aby wyłączyć regułę:
        ```cmd
        psexec \\<DIP> -u <username> cmd
        netsh advfirewall firewall set rule dir=in name="Remote Desktop - User Mode (TCP-In)" new enable=no
        ```

#### <a name="mitigation-4-remote-registry"></a>Łagodzenie 4: Rejestr zdalny

Jeśli maszyna wirtualna jest w trybie online i można uzyskać do niej dostęp z innej maszyny wirtualnej w tej samej sieci wirtualnej, możesz użyć [rejestru zdalnego](https://www.betaarchive.com/wiki/index.php?title=Microsoft_KB_Archive/314837) na drugiej maszynie wirtualnej.

1.  Na maszynie wirtualnej Rozwiązywanie problemów Uruchom Edytor rejestru (regedit.exe), a następnie wybierz kolejno pozycje **plik**  >  **Połącz z rejestrem sieciowym**.

2.  Otwórz gałąź \System *maszyny docelowej*, a następnie określ następujące wartości:

    * Aby włączyć regułę, Otwórz następującą wartość rejestru:
    
        \SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-Usermode-in-TCP *maszyny docelowej*
    
        Następnie zmień wartość **Active = false** na **Active = true** w ciągu:

        `v2.22|Action=Allow|Active=TRUE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=\@FirewallAPI.dll,-28775|Desc=\@FirewallAPI.dll,-28756|EmbedCtxt=\@FirewallAPI.dll,-28752|`
    
    * Aby wyłączyć regułę, Otwórz następującą wartość rejestru:
    
        \SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-Usermode-in-TCP *maszyny docelowej*

        Następnie zmień wartość **Active = true** na **Active = false**:
        
        `v2.22|Action=Allow|Active=FALSE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=\@FirewallAPI.dll,-28775|Desc=\@FirewallAPI.dll,-28756|EmbedCtxt=\@FirewallAPI.dll,-28752|`

3.  Uruchom ponownie maszynę wirtualną, aby zastosować zmiany.

### <a name="offline-troubleshooting"></a>Rozwiązywanie problemów w trybie offline 

Jeśli nie możesz uzyskać dostępu do maszyny wirtualnej za pomocą żadnej metody, korzystanie z niestandardowego rozszerzenia skryptu zakończy się niepowodzeniem, a użytkownik będzie musiał pracować w trybie OFFLINE, pracując bezpośrednio na dysku systemowym.

Przed wykonaniem tych kroków należy wykonać migawkę dysku systemowego, której dotyczy dana maszyna wirtualna, jako kopię zapasową. Aby uzyskać więcej informacji, zobacz [migawka dysku](../windows/snapshot-copy-managed-disk.md).

1.  [Dołącz dysk systemowy do maszyny wirtualnej odzyskiwania](troubleshoot-recovery-disks-portal-windows.md).

2.  Uruchom Pulpit zdalny połączenie z maszyną wirtualną odzyskiwania.

3.  Upewnij się, że dysk jest oflagowany jako **online** w konsoli Zarządzanie dyskami. Należy pamiętać, że litera dysku przypisana do dołączonego dysku systemowego.

4.  Przed wprowadzeniem jakichkolwiek zmian Utwórz kopię folderu \Windows\System32\Config w przypadku, gdy konieczne jest wycofanie zmian.

5.  Na maszynie wirtualnej Rozwiązywanie problemów Uruchom Edytor rejestru (regedit.exe).

6.  Zaznacz klucz **HKEY_LOCAL_MACHINE** , a następnie wybierz pozycję **plik**  >  **Załaduj gałąź** z menu.

    ![Regedit](./media/enable-or-disable-firewall-rule-guest-os/load-registry-hive.png)

7.  Zlokalizuj i Otwórz plik \windows\system32\config\SYSTEM. 

    > [!Note]
    > Zostanie wyświetlony monit o podanie nazwy. Wprowadź **BROKENSYSTEM**, a następnie rozwiń węzeł **HKEY_LOCAL_MACHINE**. Zobaczysz teraz dodatkowy klucz o nazwie **BROKENSYSTEM**. W ramach tego problemu instalujemy te gałęzie problemów jako **BROKENSYSTEM**.

8.  Wprowadź następujące zmiany w gałęzi BROKENSYSTEM:

    1.  Sprawdź, który klucz rejestru **ControlSet** jest URUCHAMIANY przez maszynę wirtualną. Zobaczysz swój numer klucza w HKLM\BROKENSYSTEM\Select\Current.

    2.  Aby włączyć regułę, Otwórz następującą wartość rejestru:
    
        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP
        
        Następnie zmień wartość **Active = false** na **Active = true**.
        
        `v2.22|Action=Allow|Active=TRUE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=\@FirewallAPI.dll,-28775|Desc=\@FirewallAPI.dll,-28756|EmbedCtxt=\@FirewallAPI.dll,-28752|`

    3.  Aby wyłączyć regułę, Otwórz następujący klucz rejestru:

        HKLM\BROKENSYSTEM\ControlSet00X\Services\SharedAccess\Parameters\FirewallPolicy\FirewallRules\RemoteDesktop-UserMode-In-TCP

        Następnie zmień wartość **Active = true na wartość** **Active = false**.
        
        `v2.22|Action=Allow|Active=FALSE|Dir=In|Protocol=6|Profile=Domain|Profile=Private|Profile=Public|LPort=3389|App=%SystemRoot%\system32\svchost.exe|Svc=termservice|Name=\@FirewallAPI.dll,-28775|Desc=\@FirewallAPI.dll,-28756|EmbedCtxt=\@FirewallAPI.dll,-28752|`

9.  Podświetl pozycję **BROKENSYSTEM**, a następnie wybierz pozycję **plik**  >  **Zwolnij gałąź** z menu.

10. [Odłącz dysk systemowy i Utwórz ponownie maszynę wirtualną](troubleshoot-recovery-disks-portal-windows.md).

11. Sprawdź, czy problem został rozwiązany.
