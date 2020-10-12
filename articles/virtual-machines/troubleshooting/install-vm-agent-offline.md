---
title: Zainstaluj agenta maszyny wirtualnej platformy Azure w trybie offline | Microsoft Docs
description: Dowiedz się, jak zainstalować agenta maszyny wirtualnej platformy Azure w trybie offline.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 07/06/2020
ms.author: genli
ms.openlocfilehash: 456aa225fa8eed47ca794c54e61b77a30c93fa9a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85983231"
---
# <a name="install-the-azure-virtual-machine-agent-in-offline-mode"></a>Zainstaluj agenta maszyny wirtualnej platformy Azure w trybie offline 

Agent maszyny wirtualnej platformy Azure (Agent VM) oferuje przydatne funkcje, takie jak resetowanie hasła administratora lokalnego i wypychanie skryptów. W tym artykule opisano sposób instalowania agenta maszyny wirtualnej dla maszyny wirtualnej z systemem Windows w trybie offline. 

## <a name="when-to-use-the-vm-agent-in-offline-mode"></a>Kiedy używać agenta maszyny wirtualnej w trybie offline

Zainstaluj agenta maszyny wirtualnej w trybie offline w następujących scenariuszach:

- Wdrożona maszyna wirtualna platformy Azure nie ma zainstalowanego agenta maszyny wirtualnej lub Agent nie działa.
- Nie pamiętasz hasła administratora maszyny wirtualnej lub nie masz dostępu do maszyny wirtualnej.

## <a name="how-to-install-the-vm-agent-in-offline-mode"></a>Jak zainstalować agenta maszyny wirtualnej w trybie offline

Wykonaj następujące kroki, aby zainstalować agenta maszyny wirtualnej w trybie offline.

### <a name="step-1-attach-the-os-disk-of-the-vm-to-another-vm-as-a-data-disk"></a>Krok 1. dołączanie dysku systemu operacyjnego maszyny wirtualnej do innej maszyny wirtualnej jako dysku z danymi

1. Utwórz migawkę dysku systemu operacyjnego z zaatakowaną maszyną wirtualną, Stwórz dysk na podstawie migawki, a następnie Dołącz dysk do maszyny wirtualnej rozwiązywania problemów. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z maszyną wirtualną z systemem Windows przez dołączenie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania przy użyciu Azure Portal](troubleshoot-recovery-disks-portal-windows.md). W przypadku klasycznej maszyny wirtualnej Usuń maszynę wirtualną i Zachowaj dysk systemu operacyjnego, a następnie Dołącz dysk systemu operacyjnego do maszyny wirtualnej Rozwiązywanie problemów.

2.  Połącz się z maszyną wirtualną narzędzia do rozwiązywania problemów. Otwórz **Computer management**przystawkę Zarządzanie  >  **dyskami**Zarządzanie komputerem. Upewnij się, że dysk systemu operacyjnego jest w trybie online i że litery dysku są przypisane do partycji dysku.

### <a name="step-2-modify-the-os-disk-to-install-the-azure-vm-agent"></a>Krok 2. Modyfikowanie dysku systemu operacyjnego w celu zainstalowania agenta maszyny wirtualnej platformy Azure

1.  Utwórz połączenie pulpitu zdalnego z maszyną wirtualną narzędzia do rozwiązywania problemów.

2.  Na maszynie wirtualnej narzędzia do rozwiązywania problemów przejdź do dołączonego dysku systemu operacyjnego, Otwórz folder \Windows\System32\Config. Skopiuj wszystkie pliki w tym folderze jako kopię zapasową, w przypadku gdy wymagane jest wycofanie.

3.  Uruchom **Edytor rejestru** (regedit.exe).

4.  Wybierz klucz **HKEY_LOCAL_MACHINE** . Z menu wybierz pozycję **File**  >  **Załaduj plik Hive**:

    ![Ładowanie gałęzi](./media/install-vm-agent-offline/load-hive.png)

5.  Przejdź do folderu \windows\system32\config\SYSTEM na dysku systemu operacyjnego, który został podłączony. W polu Nazwa gałęzi wpisz **BROKENSYSTEM**. W kluczu **HKEY_LOCAL_MACHINE** zostanie wyświetlona nowa gałąź rejestru.

6.  Przejdź do folderu \windows\system32\config\SOFTWARE na dysku systemu operacyjnego, który został podłączony. W polu Nazwa oprogramowania Hive wprowadź **BROKENSOFTWARE**.

7. Jeśli na dołączonym dysku systemu operacyjnego zainstalowano agenta maszyny wirtualnej, wykonaj kopię zapasową bieżącej konfiguracji. Jeśli nie zainstalowano agenta maszyny wirtualnej, przejdź do następnego kroku.
      
    1. Zmień nazwę folderu \windowsazure na \windowsazure.old.

    2. Wyeksportuj następujące rejestry:
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE\BROKENSYSTEM\ControlSet001\Services\RdAgent

8.  Użyj istniejących plików na maszynie wirtualnej narzędzia do rozwiązywania problemów jako repozytorium instalacji agenta maszyny wirtualnej. Wykonaj poniższe czynności:

    1. Na maszynie wirtualnej narzędzia do rozwiązywania problemów wyeksportuj następujące podklucze w formacie rejestru (reg): 
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\WindowsAzureGuestAgent
        - HKEY_LOCAL_MACHINE \SYSTEM\ControlSet001\Services\RdAgent

          ![Eksportowanie podkluczy rejestru](./media/install-vm-agent-offline/backup-reg.png)

    2. Edytuj pliki rejestru. W każdym pliku Zmień **system** wartości wpisu na **BROKENSYSTEM** (jak pokazano na poniższych obrazach) i Zapisz plik. Zapamiętaj **ImagePath** bieżącego agenta maszyny wirtualnej. Będziemy musieli skopiować odpowiedni folder do dołączonego dysku systemu operacyjnego. 

        ![Zmienianie wartości podklucza rejestru](./media/install-vm-agent-offline/change-reg.png)

    3. Zaimportuj pliki rejestru do repozytorium, dwukrotnie klikając każdy plik rejestru.

    4. Upewnij się, że następujące dwa podklucze zostały pomyślnie zaimportowane do **BROKENSYSTEM** Hive:
        - WindowsAzureGuestAgent
        - RdAgent

    5. Skopiuj folder instalacyjny bieżącego agenta maszyny wirtualnej na dołączony dysk systemu operacyjnego: 

        1.  Na dołączonym dysku systemu operacyjnego Utwórz folder o nazwie WindowsAzure w ścieżce katalogu głównego.

        2.  Przejdź do C:\WindowsAzure na maszynie wirtualnej narzędzia do rozwiązywania problemów, poszukaj folderu o nazwie C:\WindowsAzure\ GuestAgent_X. X. XXXX. XXX. Skopiuj folder GuestAgent o numerze najnowszej wersji z C:\WindowsAzure do folderu WindowsAzure na dołączonym dysku systemu operacyjnego. Jeśli nie masz pewności, który folder powinien być kopiowany, skopiuj wszystkie foldery GuestAgent. Na poniższej ilustracji przedstawiono przykład folderu GuestAgent, który jest kopiowany do dołączonego dysku systemu operacyjnego.

             ![Kopiuj folder GuestAgent](./media/install-vm-agent-offline/copy-files.png)

9.  Wybierz pozycję **BROKENSYSTEM**. Z menu wybierz pozycję **File**  >  **Zwolnij plik Hive**.

10.  Wybierz pozycję **BROKENSOFTWARE**. Z menu wybierz pozycję **File**  >  **Zwolnij plik Hive**.

11.  Odłącz dysk systemu operacyjnego, a następnie [Zmień dysk systemu operacyjnego dla maszyny wirtualnej, której to dotyczy](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm). Dla klasycznej maszyny wirtualnej Utwórz nową maszynę wirtualną przy użyciu naprawionego dysku systemu operacyjnego.

12.  Uzyskaj dostęp do maszyny wirtualnej. Zwróć uwagę, że RdAgent jest uruchomiona i dzienniki są generowane.

Jeśli maszyna wirtualna została utworzona przy użyciu modelu wdrażania Menedżer zasobów, wszystko jest gotowe.

### <a name="use-the-provisionguestagent-property-for-classic-vms"></a>Używanie właściwości parametru provisionguestagent dla klasycznych maszyn wirtualnych

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Jeśli maszyna wirtualna została utworzona przy użyciu modelu klasycznego, należy użyć modułu Azure PowerShell, aby zaktualizować właściwość **parametru provisionguestagent** . Właściwość informuje platformę Azure, że na maszynie wirtualnej jest zainstalowany agent maszyny wirtualnej.

Aby ustawić właściwość **parametru provisionguestagent** , uruchom następujące polecenia w Azure PowerShell:

   ```powershell
   $vm = Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   $vm.VM.ProvisionGuestAgent = $true
   Update-AzureVM –Name <VM name> –VM $vm.VM –ServiceName <cloud service name>
   ```

Następnie uruchom `Get-AzureVM` polecenie. Zauważ, że właściwość **GuestAgentStatus** jest teraz wypełniana danymi:

   ```powershell
   Get-AzureVM –ServiceName <cloud service name> –Name <VM name>
   GuestAgentStatus:Microsoft.WindowsAzure.Commands.ServiceManagement.Model.PersistentVMModel.GuestAgentStatus
   ```

## <a name="next-steps"></a>Następne kroki

- [Omówienie agenta maszyny wirtualnej platformy Azure](../extensions/agent-windows.md)
- [Rozszerzenia i funkcje maszyny wirtualnej dla systemu Windows](../extensions/features-windows.md)
