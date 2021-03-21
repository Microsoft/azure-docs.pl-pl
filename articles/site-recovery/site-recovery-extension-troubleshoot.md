---
title: Rozwiązywanie problemów z rozszerzeniem maszyny wirtualnej platformy Azure na potrzeby odzyskiwania po awarii przy użyciu Azure Site Recovery
description: Rozwiązywanie problemów z rozszerzeniem maszyny wirtualnej platformy Azure na potrzeby odzyskiwania po awarii przy użyciu Azure Site Recovery.
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.openlocfilehash: c1915d108bf9465d3e5b8d6a55053b583ee4f580
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96009708"
---
# <a name="troubleshoot-azure-vm-extension-issues"></a>Rozwiązywanie problemów z rozszerzeniem maszyny wirtualnej platformy Azure

W tym artykule opisano kroki rozwiązywania problemów, które mogą pomóc w rozwiązywaniu Azure Site Recovery błędów związanych z agentem i rozszerzeniem maszyny wirtualnej.

## <a name="low-system-resources"></a>Niskie zasoby systemowe

Ten problem występuje, gdy system ma małą ilość dostępnej pamięci i nie może przydzielić pamięci na potrzeby instalacji usługi mobilności. Upewnij się, że wystarczająca ilość pamięci została zwolniona w celu pomyślnego zakończenia instalacji.

## <a name="azure-site-recovery-extension-time-out"></a>Limit czasu rozszerzenia Azure Site Recovery  

Komunikat o błędzie: "Przekroczono limit czasu wykonywania zadania podczas śledzenia operacji rozszerzenia do uruchomienia"<br>
Kod błędu: "151076"

 Azure Site Recovery zainstalowaniu na maszynie wirtualnej rozszerzenia w ramach zadania Włącz ochronę. Wszelkie poniższe warunki mogą uniemożliwić wyzwolenie ochrony i spowodować niepowodzenie zadania. Wykonaj następujące kroki rozwiązywania problemów, a następnie ponów próbę wykonania operacji:

- [Agent jest zainstalowany na maszynie wirtualnej, ale nie odpowiada (dla maszyn wirtualnych z systemem Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Aktualizacja lub załadowanie rozszerzenia Site Recovery nie powiodło się](#the-site-recovery-extension-fails-to-update-or-load)

Komunikat o błędzie: "Poprzednia operacja rozszerzenia Site Recovery trwa dłużej niż oczekiwano".<br>
Kod błędu: "150066"

- [Agent jest zainstalowany na maszynie wirtualnej, ale nie odpowiada (dla maszyn wirtualnych z systemem Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)
- [Stan rozszerzenia Site Recovery jest nieprawidłowy](#the-site-recovery-extension-fails-to-update-or-load)

## <a name="protection-fails-because-the-vm-agent-is-unresponsive"></a>Ochrona nie powiedzie się, ponieważ agent maszyny wirtualnej nie odpowiada

Komunikat o błędzie: "Przekroczono limit czasu wykonywania zadania podczas śledzenia operacji rozszerzenia do uruchomienia".<br>
Kod błędu: "151099"

Ten błąd może wystąpić, jeśli Agent gościa platformy Azure w maszynie wirtualnej nie jest w stanie gotowości.

Stan agenta gościa platformy Azure można sprawdzić w [Azure Portal](https://portal.azure.com/). Przejdź do maszyny wirtualnej, którą próbujesz chronić, i sprawdź stan w obszarze właściwości ustawień **maszyny wirtualnej**  >    >    >  **stan agenta**. W większości przypadków stan agenta jest gotowy po ponownym uruchomieniu maszyny wirtualnej. Jeśli jednak nie możesz ponownie uruchomić komputera lub problem nadal występuje, wykonaj następujące kroki rozwiązywania problemów:

- [Agent jest zainstalowany na maszynie wirtualnej, ale nie odpowiada (dla maszyn wirtualnych z systemem Windows)](#the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms)
- [Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)


Komunikat o błędzie: "Przekroczono limit czasu wykonywania zadania podczas śledzenia operacji rozszerzenia do uruchomienia".<br>
Kod błędu: "151095"

Ten błąd występuje, gdy wersja agenta na komputerze z systemem Linux jest nieaktualna. Wykonaj następujący krok rozwiązywania problemów:

- [Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)](#the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms)  

## <a name="causes-and-solutions"></a>Przyczyny i rozwiązania

### <a name="the-agent-is-installed-in-the-vm-but-its-unresponsive-for-windows-vms"></a><a name="the-agent-installed-in-the-vm-but-unresponsive-for-windows-vms"></a>Agent jest zainstalowany na maszynie wirtualnej, ale nie odpowiada (dla maszyn wirtualnych z systemem Windows)

#### <a name="solution"></a>Rozwiązanie
Agent maszyny wirtualnej mógł zostać uszkodzony lub usługa mogła zostać zatrzymana. Ponowne zainstalowanie agenta maszyny wirtualnej pomoże uzyskać najnowszą wersję. Pomaga również w ponownym uruchomieniu komunikacji z usługą.

1. Ustal, czy usługa agenta gościa systemu Windows Azure jest uruchomiona w usługach VM Services (Services. msc). Uruchom ponownie usługę agenta gościa systemu Windows Azure.    
1. Jeśli usługa agenta gościa platformy Microsoft Azure nie jest widoczna w obszarze usługi, Otwórz Panel sterowania. Przejdź do **apletu programy i funkcje** , aby sprawdzić, czy zainstalowano usługę agenta gościa systemu Windows.
1. Jeśli w aplecie **programy i funkcje** zostanie wyświetlony Agent gościa platformy Microsoft Azure, Odinstaluj agenta gościa platformy Microsoft Azure.
1. Pobierz i zainstaluj [najnowszą wersję pliku msi agenta](https://go.microsoft.com/fwlink/?LinkID=394789&clcid=0x409). Aby ukończyć instalację, musisz mieć uprawnienia administratora.
1. Sprawdź, czy usługa agenta gościa systemu Windows Azure jest wyświetlana w obszarze usługi.
1. Uruchom ponownie zadanie ochrony.

Sprawdź również, czy na maszynie wirtualnej [zainstalowano Microsoft .NET 4,5](/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) . Do komunikacji agenta maszyny wirtualnej z usługą wymagany jest program .NET 4,5.

### <a name="the-agent-installed-in-the-vm-is-out-of-date-for-linux-vms"></a>Agent zainstalowany na maszynie wirtualnej jest nieaktualny (dla maszyn wirtualnych z systemem Linux)

#### <a name="solution"></a>Rozwiązanie
Większość błędów związanych z agentami lub rozszerzeniami dla maszyn wirtualnych z systemem Linux jest spowodowana przez problemy, które mają wpływ na nieaktualny Agent maszyny wirtualnej. Aby rozwiązać ten problem, postępuj zgodnie z następującymi ogólnymi wskazówkami:

1. Postępuj zgodnie z instrukcjami dotyczącymi [aktualizowania agenta maszyny wirtualnej z systemem Linux](../virtual-machines/extensions/update-linux-agent.md).

   > [!NOTE]
   > *Zdecydowanie zalecamy* , aby zaktualizować agenta tylko za pomocą repozytorium dystrybucji. Nie zalecamy pobierania kodu agenta bezpośrednio z usługi GitHub i aktualizowania go. Jeśli najnowszy Agent dystrybucji nie jest dostępny, skontaktuj się z pomocą techniczną, aby uzyskać instrukcje dotyczące sposobu jej instalacji. Aby sprawdzić najnowszego agenta, przejdź do strony [agenta systemu Linux platformy Microsoft Azure](https://github.com/Azure/WALinuxAgent/releases) w repozytorium GitHub.

1. Upewnij się, że na maszynie wirtualnej jest uruchomiony agent platformy Azure, uruchamiając następujące polecenie: `ps -e`

   Jeśli proces nie jest uruchomiony, uruchom go ponownie przy użyciu następujących poleceń:

   - Dla Ubuntu: `service walinuxagent start`
   - W przypadku innych dystrybucji: `service waagent start`

1. [Skonfiguruj agenta automatycznego ponownego uruchamiania](https://github.com/Azure/WALinuxAgent/wiki/Known-Issues#mitigate_agent_crash).
1. Włącz ochronę maszyny wirtualnej.

### <a name="the-site-recovery-extension-fails-to-update-or-load"></a>Aktualizacja lub załadowanie rozszerzenia Site Recovery nie powiodło się

Stan rozszerzenia jest wyświetlany jako "puste", "nobieżniing" lub "transitioning".

#### <a name="solution"></a>Rozwiązanie

Odinstaluj rozszerzenie i ponownie uruchom operację.

Aby odinstalować rozszerzenie:

1. W [Azure Portal](https://portal.azure.com/)przejdź do maszyny wirtualnej, na której występuje błąd kopii zapasowej.
1. Wybierz pozycję **Ustawienia**.
1. Wybierz pozycję **Rozszerzenia**.
1. Wybierz **Site Recovery rozszerzenie**.
1. Wybierz pozycję **Odinstaluj**.

W przypadku maszyny wirtualnej z systemem Linux, jeśli rozszerzenie VMSnapshot nie jest wyświetlane w Azure Portal, [zaktualizuj agenta systemu Linux platformy Azure](../virtual-machines/extensions/update-linux-agent.md). Następnie uruchom ochronę.

Wykonanie tych kroków spowoduje ponowne zainstalowanie rozszerzenia podczas ochrony.
