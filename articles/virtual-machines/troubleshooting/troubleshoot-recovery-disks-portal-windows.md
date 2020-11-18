---
title: Rozwiązywanie problemów z maszyną wirtualną z systemem Windows w Azure Portal | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z maszynami wirtualnymi systemu Windows na platformie Azure, łącząc dysk systemu operacyjnego z MASZYNą wirtualną odzyskiwania za pomocą Azure Portal.
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/19/2018
ms.author: genli
ms.openlocfilehash: 04adf3903cd925f4507e94347251c762a576ff93
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94735232"
---
# <a name="troubleshoot-a-windows-vm-by-attaching-the-os-disk-to-a-recovery-vm-through-the-azure-portal"></a>Rozwiązywanie problemów z maszyną wirtualną z systemem Windows przez dołączenie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania za pomocą Azure Portal
Jeśli maszyna wirtualna z systemem Windows na platformie Azure napotyka błąd uruchamiania lub dysku, może być konieczne wykonanie kroków rozwiązywania problemów na wirtualnym dysku twardym (VHD). Typowym przykładem jest niepowodzenie aktualizacji aplikacji, która uniemożliwia pomyślne uruchomienie maszyny wirtualnej. W tym artykule szczegółowo opisano, jak za pomocą Azure Portal połączyć swój wirtualny dysk twardy z inną maszyną wirtualną z systemem Windows w celu usunięcia błędów, a następnie ponownie utworzyć oryginalną maszynę wirtualną. 

## <a name="recovery-process-overview"></a>Omówienie procesu odzyskiwania
Proces rozwiązywania problemów jest następujący:

1. Zatrzymaj zaatakowaną maszynę wirtualną.
1. Utwórz migawkę dysku systemu operacyjnego dla maszyny wirtualnej.
1. Utwórz wirtualny dysk twardy na podstawie migawki.
1. Dołącz i Zainstaluj wirtualny dysk twardy na innej maszynie wirtualnej z systemem Windows w celu rozwiązywania problemów.
1. Nawiąż połączenie z maszyną wirtualną rozwiązywania problemów. Edytuj pliki lub Uruchom dowolne narzędzia, aby rozwiązać problemy dotyczące oryginalnego wirtualnego dysku twardego.
1. Odłącz wirtualny dysk twardy od maszyny wirtualnej rozwiązywania problemów.
1. Zamień dysk systemu operacyjnego dla maszyny wirtualnej.

> [!NOTE]
> Ten artykuł nie dotyczy maszyn wirtualnych z dyskami niezarządzanymi.

## <a name="take-a-snapshot-of-the-os-disk"></a>Utwórz migawkę dysku systemu operacyjnego
Migawka to pełna kopia tylko do odczytu wirtualnego dysku twardego. Zalecamy wyczyszczenie maszyny wirtualnej przed wykonaniem migawki, aby wyczyścić wszystkie procesy, które są w toku. Aby wykonać migawkę dysku systemu operacyjnego, wykonaj następujące kroki:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com). Wybierz pozycję **maszyny wirtualne** z paska bocznego, a następnie wybierz maszynę wirtualną, której dotyczy problem.
1. W okienku po lewej stronie wybierz pozycję **dyski**, a następnie wybierz nazwę dysku systemu operacyjnego.
    ![Zrzut ekranu przedstawiający nazwę dysku systemu operacyjnego w ustawieniach dysku.](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. Na stronie **Przegląd** dysku systemu operacyjnego wybierz pozycję **Utwórz migawkę**.
1. Utwórz migawkę w tej samej lokalizacji, w której znajduje się dysk systemu operacyjnego.

## <a name="create-a-disk-from-the-snapshot"></a>Utwórz dysk na podstawie migawki
Aby utworzyć dysk na podstawie migawki, wykonaj następujące kroki:

1. Wybierz **Cloud Shell** z Azure Portal.

    ![Zrzut ekranu pokazujący przycisk służący do otwierania Azure Cloud Shell.](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. Uruchom następujące polecenia programu PowerShell, aby utworzyć dysk zarządzany na podstawie migawki. Zastąp przykładowe nazwy odpowiednimi nazwami.

    ```powershell
    #Provide the name of your resource group.
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create managed disks.
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of the managed disk.
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in gigabytes. It should be greater than the VHD file size. In this example, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for the managed disk: Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (for example, westus) where the managed disks will be located.
    #This location should be the same as the snapshot location.
    #Get all the Azure locations by using this command:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. Jeśli polecenia zostaną wykonane pomyślnie, zobaczysz nowy dysk w podanej grupie zasobów.

## <a name="attach-the-disk-to-another-vm"></a>Dołącz dysk do innej maszyny wirtualnej
W następnych kilku krokach do rozwiązywania problemów służy inna maszyna wirtualna. Po dołączeniu dysku do maszyny wirtualnej rozwiązywania problemów można przeglądać i edytować zawartość tego dysku. Ten proces umożliwia poprawienie wszystkich błędów konfiguracji lub przejrzenie dodatkowych plików dziennika aplikacji lub systemu. Aby dołączyć dysk do innej maszyny wirtualnej, wykonaj następujące kroki:

1. Wybierz grupę zasobów z portalu, a następnie wybierz maszynę wirtualną rozwiązywania problemów. Wybierz pozycję **dyski**  >  **Edytuj**  >  **Dodaj dysk danych**.

    ![Zrzut ekranu pokazujący opcje dołączania istniejącego dysku w portalu.](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Z listy **dyski danych** wybierz dysk systemu operacyjnego zidentyfikowanej maszyny wirtualnej. Jeśli nie widzisz dysku systemu operacyjnego, upewnij się, że maszyna wirtualna do rozwiązywania problemów i dysk systemu operacyjnego znajdują się w tym samym regionie (lokalizacji). 
3. Wybierz pozycję **Zapisz** , aby zastosować zmiany.

## <a name="mount-the-attached-data-disk-to-the-vm"></a>Instalowanie dołączonego dysku z danymi na maszynie wirtualnej

1. Otwórz połączenie Pulpit zdalny z maszyną wirtualną rozwiązywania problemów. 
2. Na maszynie wirtualnej Rozwiązywanie problemów Otwórz **Menedżer serwera**, a następnie wybierz pozycję **usługi plików i magazynowania**. 

    ![Zrzut ekranu pokazujący Wybieranie usług plików i magazynowania w ramach Menedżer serwera.](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

3. Dysk danych zostanie automatycznie wykryty i dołączony. Aby wyświetlić listę podłączonych dysków, wybierz pozycję **dyski**. Możesz wybrać dysk danych, aby wyświetlić informacje o woluminie, w tym literę dysku. Poniższy przykład przedstawia dołączony dysk danych i użycie dysku **F**.

    ![Zrzut ekranu przedstawiający informacje o dysku i woluminie w Menedżer serwera.](./media/troubleshoot-recovery-disks-portal-windows/server-manager-disk-attached.png)

## <a name="fix-problems-on-the-original-virtual-hard-disk"></a>Rozwiązywanie problemów dotyczących oryginalnego wirtualnego dysku twardego
Po zainstalowaniu istniejącego wirtualnego dysku twardego można wykonać wszelkie czynności konserwacyjne i rozwiązywania problemów zgodnie z wymaganiami. Po rozwiązaniu wszystkich błędów wykonaj następujące czynności.

## <a name="unmount-and-detach-the-original-virtual-hard-disk"></a>Odinstalowywanie i odłączanie oryginalnego wirtualnego dysku twardego
Odłącz istniejący wirtualny dysk twardy z maszyny wirtualnej rozwiązywania problemów. Nie można użyć wirtualnego dysku twardego z żadną inną maszyną wirtualną do momentu wydania dzierżawy, która dołącza wirtualny dysk twardy do rozwiązywania problemów z maszyną wirtualną.

1. Z sesji Pulpit zdalny na maszynę wirtualną Otwórz pozycję **Menedżer serwera**, a następnie wybierz pozycję **usługi plików i magazynowania**.

    ![Zrzut ekranu pokazujący Wybieranie usług plików i magazynowania w Menedżer serwera.](./media/troubleshoot-recovery-disks-portal-windows/server-manager-select-storage.png)

2. Wybierz pozycję **dyski**, kliknij prawym przyciskiem myszy dysk danych, a następnie wybierz polecenie **Przełącz do trybu offline**.

    ![Zrzut ekranu przedstawiający Ustawianie dysku danych w trybie offline w Menedżer serwera.](./media/troubleshoot-recovery-disks-portal-windows/server-manager-set-disk-offline.png)

3. Odłącz wirtualny dysk twardy od maszyny wirtualnej. W Azure Portal wybierz maszynę wirtualną, a następnie wybierz pozycję **dyski**. 
4. Wybierz pozycję **Edytuj**, wybierz dołączony dysk systemu operacyjnego, a następnie wybierz pozycję **Usuń**.

    ![Zrzut ekranu pokazujący opcje odłączania istniejącego wirtualnego dysku twardego.](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Przed kontynuowaniem Zaczekaj, aż dysk danych zostanie pomyślnie usunięty z maszyny wirtualnej.

## <a name="swap-the-os-disk-for-the-vm"></a>Wymiana dysku systemu operacyjnego dla maszyny wirtualnej

Azure Portal teraz obsługuje zmianę dysku systemu operacyjnego maszyny wirtualnej. W tym celu wykonaj następujące czynności:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com). Wybierz pozycję **maszyny wirtualne** z paska bocznego, a następnie wybierz maszynę wirtualną, której dotyczy problem.
1. W okienku po lewej stronie wybierz pozycję **dyski**, a następnie wybierz pozycję **Zamień dysk systemu operacyjnego**.
   
    ![Zrzut ekranu pokazujący przycisk zamiany dysku systemu operacyjnego w Azure Portal.](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Wybierz nowy dysk, który został naprawiony, a następnie wprowadź nazwę maszyny wirtualnej, aby potwierdzić zmianę. Jeśli na liście nie widzisz dysku, poczekaj od 10 do 15 minut po odłączeniu dysku od maszyny wirtualnej rozwiązywania problemów. Upewnij się również, że dysk znajduje się w tej samej lokalizacji co maszyna wirtualna.
1. Wybierz przycisk **OK**.

## <a name="next-steps"></a>Następne kroki
Jeśli masz problemy z nawiązywaniem połączenia z maszyną wirtualną, zobacz [Rozwiązywanie problemów z połączeniami pulpit zdalny z maszyną wirtualną platformy Azure](troubleshoot-rdp-connection.md). Problemy z uzyskiwaniem dostępu do aplikacji uruchomionych na maszynie wirtualnej można znaleźć [w temacie Rozwiązywanie problemów z łącznością aplikacji na maszynie wirtualnej z systemem Windows](troubleshoot-app-connection.md).

Aby uzyskać więcej informacji o korzystaniu z Azure Resource Manager, zobacz [omówienie Azure Resource Manager](../../azure-resource-manager/management/overview.md).


