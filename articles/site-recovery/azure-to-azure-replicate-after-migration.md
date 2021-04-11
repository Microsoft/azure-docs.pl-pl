---
title: Skonfiguruj odzyskiwanie po awarii po migracji na platformę Azure za pomocą Azure Site Recovery
description: W tym artykule opisano sposób przygotowania maszyn do skonfigurowania odzyskiwania po awarii między regionami platformy Azure po migracji na platformę Azure przy użyciu Azure Site Recovery.
services: site-recovery
ms.service: site-recovery
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 41fa4f5d042b5c284a58b0fd87486e2309b03053
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581733"
---
# <a name="set-up-disaster-recovery-for-azure-vms-after-migration-to-azure"></a>Konfigurowanie odzyskiwania po awarii maszyn wirtualnych platformy Azure po migracji na platformę Azure 


Postępuj zgodnie z tym artykułem, jeśli [maszyny lokalne zostały zmigrowane do maszyn wirtualnych platformy Azure](./migrate-tutorial-on-premises-azure.md) przy użyciu usługi [Site Recovery](site-recovery-overview.md) i chcesz teraz skonfigurować maszyny wirtualne do odzyskiwania po awarii w regionie pomocniczym platformy Azure. W tym artykule opisano, jak upewnić się, że Agent maszyny wirtualnej platformy Azure jest zainstalowany na zmigrowanych maszynach wirtualnych oraz jak usunąć usługę mobilności Site Recovery, która nie jest już wymagana po migracji.



## <a name="verify-migration"></a>Weryfikowanie migracji

Przed skonfigurowaniem odzyskiwania po awarii upewnij się, że migracja zakończyła się zgodnie z oczekiwaniami. Aby pomyślnie ukończyć migrację, po przejściu do trybu failover należy wybrać opcję **pełna migracja** dla każdej maszyny, która ma zostać zmigrowana. 

## <a name="verify-the-azure-vm-agent"></a>Weryfikowanie agenta maszyny wirtualnej platformy Azure

Dla każdej maszyny wirtualnej platformy Azure musi być zainstalowany [Agent maszyny wirtualnej platformy Azure](../virtual-machines/extensions/agent-windows.md) . Aby replikować maszyny wirtualne platformy Azure, Site Recovery instaluje rozszerzenie agenta.

- Jeśli na maszynie jest uruchomiona wersja 9.7.0.0 lub nowsza Site Recovery, Agent maszyny wirtualnej platformy Azure zostanie automatycznie zainstalowany przez usługę mobilności na maszynach wirtualnych z systemem Windows. We wcześniejszych wersjach usługi mobilności należy zainstalować agenta ręcznie.
- W przypadku maszyn wirtualnych z systemem Linux należy ręcznie zainstalować agenta maszyny wirtualnej platformy Azure. Należy zainstalować agenta maszyny wirtualnej platformy Azure tylko wtedy, gdy usługa mobilności zainstalowana na migrowanej maszynie ma wartość v 9.6 lub wcześniejszą.


### <a name="install-the-agent-on-windows-vms"></a>Instalowanie agenta na maszynach wirtualnych z systemem Windows

Jeśli używasz wersji usługi mobilności Site Recovery wcześniejszej niż 9.7.0.0, lub kilka innych potrzebnych do zainstalowania agenta ręcznie, wykonaj następujące czynności:  

1. Upewnij się, że masz uprawnienia administratora na maszynie wirtualnej.
2. Pobierz [instalatora agenta maszyny wirtualnej](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409).
3. Uruchom plik Instalatora.

#### <a name="validate-the-installation"></a>Sprawdzanie poprawności instalacji
Aby sprawdzić, czy Agent jest zainstalowany:

1. Na maszynie wirtualnej platformy Azure w folderze C:\WindowsAzure\Packages powinien zostać wyświetlony plik WaAppAgent.exe.
2. Kliknij prawym przyciskiem myszy plik, a następnie w oknie **Właściwości** wybierz kartę **szczegóły** .
3. Sprawdź, czy w polu **Wersja produktu** jest wyświetlana wartość 2.6.1198.718 lub wyższa.

[Dowiedz się więcej](../virtual-machines/extensions/agent-windows.md) o instalacji agenta dla systemu Windows.

### <a name="install-the-agent-on-linux-vms"></a>Instalowanie agenta na maszynach wirtualnych z systemem Linux

Zainstaluj agenta [maszyny wirtualnej z systemem Linux na platformie Azure](../virtual-machines/extensions/agent-linux.md) ręcznie w następujący sposób:

1. Upewnij się, że masz uprawnienia administratora na tym komputerze.
2. Zdecydowanie zalecamy zainstalowanie agenta maszyny wirtualnej z systemem Linux przy użyciu programu RPM lub pakietu DEB z repozytorium pakietu dystrybucji. Wszyscy [pozatwierdzeni dostawcy dystrybucji](../virtual-machines/linux/endorsed-distros.md) integrują pakiet agenta platformy Azure z systemem Linux z obrazami i repozytoriami.
    - Zdecydowanie zalecamy, aby zaktualizować agenta tylko za pomocą repozytorium dystrybucji.
    - Nie zalecamy instalowania agenta maszyny wirtualnej systemu Linux bezpośrednio z usługi GitHub i aktualizowania go.
    -  Jeśli najnowszy Agent dystrybucji nie jest dostępny, skontaktuj się z pomocą techniczną, aby uzyskać instrukcje dotyczące sposobu jej instalacji. 

#### <a name="validate-the-installation"></a>Sprawdzanie poprawności instalacji 

1. Uruchom to polecenie: **PS-e** , aby upewnić się, że Agent Azure działa na maszynie wirtualnej z systemem Linux.
2. Jeśli proces nie jest uruchomiony, uruchom go ponownie przy użyciu następujących poleceń:
    - Dla Ubuntu: **Usługa walinuxagent Start**
    - W przypadku innych dystrybucji: **waagent usługi Start**


## <a name="uninstall-the-mobility-service"></a>Odinstalowywanie usługi mobilności

1. Ręcznie odinstaluj usługę mobilności z maszyny wirtualnej platformy Azure przy użyciu jednej z poniższych metod. 
    - W przypadku systemu Windows w panelu sterowania > **Dodaj/Usuń programy**, odinstaluj **Microsoft Azure Site Recovery usługa mobilności/główny serwer docelowy**. W wierszu polecenia z podwyższonym poziomem uprawnień uruchom polecenie:
        ```
        MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
        ```
    - W przypadku systemu Linux Zaloguj się jako użytkownik główny. W terminalu przejdź do **/User/Local/ASR** i uruchom następujące polecenie:
        ```
        ./uninstall.sh -Y
        ```
2. Przed skonfigurowaniem replikacji należy ponownie uruchomić maszynę wirtualną.

## <a name="next-steps"></a>Następne kroki

[Zapoznaj się z tematem rozwiązywania problemów](site-recovery-extension-troubleshoot.md) dotyczących rozszerzenia Site Recovery w agencie maszyny wirtualnej platformy Azure.
[Szybka replikacja](azure-to-azure-quickstart.md) maszyny wirtualnej platformy Azure do regionu pomocniczego.
