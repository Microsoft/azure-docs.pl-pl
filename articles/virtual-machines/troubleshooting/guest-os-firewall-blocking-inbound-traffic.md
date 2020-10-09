---
title: Zapora systemu operacyjnego gościa maszyny wirtualnej platformy Azure blokuje ruch przychodzący | Microsoft Docs
description: Dowiedz się, jak rozwiązać problem z połączeniem z portalem Pulpit zdalny (RDP), że Zapora systemu operacyjnego gościa blokuje ruch przychodzący.
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
ms.openlocfilehash: 1b80fc997a4b3d2b472717b1ec2f379a4e958d8c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80422554"
---
# <a name="azure-vm-guest-os-firewall-is-blocking-inbound-traffic"></a>Zapora systemu operacyjnego gościa maszyny wirtualnej platformy Azure blokuje ruch przychodzący

W tym artykule omówiono sposób rozwiązywania problemu z portalem Pulpit zdalny (RDP), który występuje, gdy Zapora systemu operacyjnego gościa blokuje ruch przychodzący.

## <a name="symptoms"></a>Objawy

Nie można połączyć się z maszyną wirtualną platformy Azure przy użyciu połączenia RDP. W ramach diagnostyki rozruchu — > zrzut ekranu pokazuje, że system operacyjny jest w pełni ładowany na ekranie powitalnym (Ctrl + Alt + Del).

## <a name="cause"></a>Przyczyna

### <a name="cause-1"></a>Przyczyna 1

Reguła RDP nie została skonfigurowana w taki sposób, aby zezwalała na ruch RDP.

### <a name="cause-2"></a>Przyczyna 2

Profile zapory systemu gościa są skonfigurowane do blokowania wszystkich połączeń przychodzących, łącznie z ruchem RDP.

![Ustawienie zapory](./media/guest-os-firewall-blocking-inbound-traffic/firewall-advanced-setting.png)

## <a name="solution"></a>Rozwiązanie

Przed wykonaniem tych kroków należy wykonać migawkę dysku systemowego, której dotyczy dana maszyna wirtualna, jako kopię zapasową. Aby uzyskać więcej informacji, zobacz [migawka dysku](../windows/snapshot-copy-managed-disk.md).

Aby rozwiązać ten problem, należy użyć jednej z metod w temacie [jak używać narzędzi zdalnych do rozwiązywania problemów z maszyną wirtualną platformy Azure](remote-tools-troubleshoot-azure-vm-issues.md) w celu zdalnego nawiązywania połączenia z maszyną wirtualną, a następnie edytować reguły zapory systemu operacyjnego gościa w celu **zezwolenia na** ruch RDP.

### <a name="online-troubleshooting"></a>Rozwiązywanie problemów online

Połącz się z [konsolą szeregową, a następnie otwórz wystąpienie programu PowerShell](serial-console-windows.md#use-cmd-or-powershell-in-serial-console). Jeśli konsola szeregowa nie jest włączona na maszynie wirtualnej, przejdź do pozycji "[napraw maszynę wirtualną w trybie offline](troubleshoot-rdp-internal-error.md#repair-the-vm-offline).

#### <a name="mitigation-1"></a>Środki zaradcze 1

1.  Jeśli Agent platformy Azure jest zainstalowany i działa poprawnie na maszynie wirtualnej, można użyć opcji "Resetuj tylko konfigurację" w obszarze **Obsługa i rozwiązywanie problemów**  >  **resetowania hasła** w menu maszyny wirtualnej.

2.  Uruchomienie tej opcji odzyskiwania wykonuje następujące czynności:

    *   Włącza składnik RDP, jeśli jest wyłączony.

    *   Włącza wszystkie profile zapory systemu Windows.

    *   Upewnij się, że reguła RDP jest włączona w zaporze systemu Windows.

    *   Jeśli poprzednie kroki nie działają, ręcznie Zresetuj regułę zapory. W tym celu wykonaj zapytanie dotyczące wszystkich reguł zawierających nazwę "Pulpit zdalny", uruchamiając następujące polecenie:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(Name.*Remote Desktop)" -context 9,4 | more
        ```

        Jeśli port RDP został ustawiony na inny port inny niż 3389, należy znaleźć dowolną regułę niestandardową, która mogła zostać utworzona i ustawiona na ten port. Aby wykonać zapytanie dotyczące wszystkich reguł ruchu przychodzącego z portem niestandardowym, uruchom następujące polecenie:

        ```cmd
        netsh advfirewall firewall show rule dir=in name=all | select-string -pattern "(LocalPort.*<CUSTOM PORT>)" -context 9,4 | more
        ```

3.  Jeśli zobaczysz, że reguła jest wyłączona, włącz ją. Aby otworzyć całą grupę, taką jak wbudowana grupa Pulpit zdalny, uruchom następujące polecenie:

    ```cmd
    netsh advfirewall firewall set rule group="Remote Desktop" new enable=yes
    ```

    W przeciwnym razie, aby otworzyć określoną zasadę Pulpit zdalny (ruch przychodzący TCP), uruchom następujące polecenie:

    ```cmd
    netsh advfirewall firewall set rule name="<CUSTOM RULE NAME>" new enable=yes
    ```

4.  W celu rozwiązywania problemów można wyłączyć profile zapory:

    ```cmd
    netsh advfirewall set allprofiles state off
    ```

    Po poprawnym zakończeniu rozwiązywania problemów i ustawieniu zapory należy ponownie włączyć zaporę.

    > [!Note]
    > Nie musisz ponownie uruchamiać maszyny wirtualnej, aby zastosować te zmiany.

5.  Spróbuj nawiązać połączenie RDP, aby uzyskać dostęp do maszyny wirtualnej.

#### <a name="mitigation-2"></a>Środki zaradcze 2

1.  Zbadaj profile zapory, aby określić, czy dla zasad zapory dla ruchu przychodzącego ustawiono wartość *BlockInboundAlways*:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    ![Allprofiles](./media/guest-os-firewall-blocking-inbound-traffic/firewall-profiles.png)

    > [!Note]
    > Poniższe wskazówki dotyczą zasad zapory, w zależności od konfiguracji:
    >    * *BlockInbound*: cały ruch przychodzący zostanie zablokowany, chyba że masz regułę zezwalającą na ten ruch.
    >    * *BlockInboundAlways*: wszystkie reguły zapory zostaną zignorowane i cały ruch zostanie zablokowany.

2.  Edytuj *DefaultInboundAction* , aby skonfigurować te profile w taki sposób, aby **zezwalały** na ruch. Aby to zrobić, uruchom następujące polecenie:

    ```cmd
    netsh advfirewall set allprofiles firewallpolicy allowinbound,allowoutbound
    ```

3.  Ponownie Zbadaj profile, aby upewnić się, że zmiana została wprowadzona pomyślnie. Aby to zrobić, uruchom następujące polecenie:

    ```cmd
    netsh advfirewall show allprofiles | more
    ```

    > [!Note]
    > Nie jest konieczne ponowne uruchomienie maszyny wirtualnej w celu zastosowania zmian.

4.  Spróbuj ponownie uzyskać dostęp do maszyny wirtualnej za pomocą protokołu RDP.

### <a name="offline-mitigations"></a>Środki zaradcze w trybie offline

1.  [Dołącz dysk systemowy do maszyny wirtualnej odzyskiwania](troubleshoot-recovery-disks-portal-windows.md).

2.  Uruchom Pulpit zdalny połączenie z maszyną wirtualną odzyskiwania.

3.  Upewnij się, że dysk jest oflagowany jako **online** w konsoli Zarządzanie dyskami. Zanotuj literę dysku przypisaną do dołączonego dysku systemowego.

#### <a name="mitigation-1"></a>Środki zaradcze 1

Zobacz [jak Enable-Disable regułę zapory w systemie operacyjnym gościa](enable-disable-firewall-rule-guest-os.md).

#### <a name="mitigation-2"></a>Środki zaradcze 2

1.  [Dołącz dysk systemowy do maszyny wirtualnej odzyskiwania](troubleshoot-recovery-disks-portal-windows.md).

2.  Uruchom Pulpit zdalny połączenie z maszyną wirtualną odzyskiwania.

3.  Po dołączeniu dysku systemowego do maszyny wirtualnej odzyskiwania upewnij się, że dysk jest oznaczony jako **online** w konsoli Zarządzanie dyskami. Zanotuj literę dysku przypisaną do dołączonego dysku systemu operacyjnego.

4.  Otwórz wystąpienie CMD z podwyższonym poziomem uprawnień, a następnie uruchom następujący skrypt:

    ```cmd
    REM Backup the registry prior doing any change
    robocopy f:\windows\system32\config f:\windows\system32\config.BACK /MT

    REM Mount the hive
    reg load HKLM\BROKENSYSTEM f:\windows\system32\config\SYSTEM

    REM Delete the keys to block all inbound connection scenario
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet001\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\DomainProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\PublicProfile" /v DoNotAllowExceptions
    REG DELETE "HKLM\BROKENSYSTEM\ControlSet002\services\SharedAccess\Parameters\FirewallPolicy\StandardProfile" /v DoNotAllowExceptions

    REM Unmount the hive
    reg unload HKLM\BROKENSYSTEM
    ```

5.  [Odłącz dysk systemowy i Utwórz ponownie maszynę wirtualną](troubleshoot-recovery-disks-portal-windows.md).

6.  Sprawdź, czy problem został rozwiązany.
