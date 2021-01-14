---
title: Użyj maszyny wirtualnej rozwiązywania problemów z systemem Linux w Azure Portal | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z maszyną wirtualną z systemem Linux, łącząc dysk systemu operacyjnego z MASZYNą wirtualną odzyskiwania przy użyciu Azure Portal
services: virtual-machines-linux
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: ''
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/19/2019
ms.author: genli
ms.openlocfilehash: affdee6871649102ef7881fb0f540eba6ab450ca
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/14/2021
ms.locfileid: "98200961"
---
# <a name="troubleshoot-a-linux-vm-by-attaching-the-os-disk-to-a-recovery-vm-using-the-azure-portal"></a>Rozwiązywanie problemów z maszyną wirtualną z systemem Linux przez dołączenie dysku systemu operacyjnego do maszyny wirtualnej odzyskiwania przy użyciu Azure Portal
Jeśli maszyna wirtualna z systemem Linux napotyka błąd rozruchu lub dysku, może być konieczne wykonanie kroków rozwiązywania problemów na wirtualnym dysku twardym. Typowym przykładem może być nieprawidłowy wpis w programie `/etc/fstab` , który uniemożliwia pomyślne uruchomienie maszyny wirtualnej. W tym artykule szczegółowo opisano sposób używania Azure Portal do łączenia wirtualnego dysku twardego z inną maszyną wirtualną z systemem Linux w celu usunięcia błędów, a następnie ponownego utworzenia oryginalnej maszyny wirtualnej.

## <a name="recovery-process-overview"></a>Omówienie procesu odzyskiwania
Proces rozwiązywania problemów jest następujący:

1. Zatrzymaj zaatakowaną maszynę wirtualną.
1. Utwórz migawkę dysku systemu operacyjnego maszyny wirtualnej.
1. Utwórz wirtualny dysk twardy na podstawie migawki.
1. Dołącz i Zainstaluj wirtualny dysk twardy na innej maszynie wirtualnej z systemem Linux w celu rozwiązywania problemów.
1. Nawiąż połączenie z maszyną wirtualną rozwiązywania problemów. Edytuj pliki lub Uruchom dowolne narzędzia, aby rozwiązać problemy dotyczące oryginalnego wirtualnego dysku twardego.
1. Odłącz wirtualny dysk twardy od maszyny wirtualnej rozwiązywania problemów.
1. Zamień dysk systemu operacyjnego dla maszyny wirtualnej.

> [!NOTE]
> Ten artykuł nie ma zastosowania do maszyny wirtualnej z dyskiem niezarządzanym.

## <a name="determine-boot-issues"></a>Określanie problemów z rozruchem
Sprawdź zrzut ekranu diagnostyki rozruchu i maszyny wirtualnej, aby określić, dlaczego nie można prawidłowo uruchomić maszyny wirtualnej. Typowym przykładem może być nieprawidłowy wpis w programie `/etc/fstab` lub dysk twardy, który jest usuwany lub przenoszony.

Wybierz maszynę wirtualną w portalu, a następnie przewiń w dół do sekcji **Pomoc techniczna i rozwiązywanie problemów** . Kliknij pozycję **Diagnostyka rozruchu** , aby wyświetlić komunikaty konsoli przesyłane strumieniowo z maszyny wirtualnej. Przejrzyj dzienniki konsoli, aby sprawdzić, czy można ustalić przyczynę wystąpienia problemu z maszyną wirtualną. W poniższym przykładzie pokazano zablokowaną maszynę wirtualną w trybie konserwacji, która wymaga interakcji ręcznej:

![Wyświetlanie dzienników konsoli diagnostyki rozruchu maszyny wirtualnej](./media/troubleshoot-recovery-disks-portal-linux/boot-diagnostics-error.png)

Możesz również kliknąć **zrzut ekranu** w górnej części dziennika diagnostyki rozruchu, aby pobrać przechwytywanie zrzutu ekranu maszyny wirtualnej.

## <a name="take-a-snapshot-of-the-os-disk"></a>Utwórz migawkę dysku systemu operacyjnego
Migawka to pełna kopia tylko do odczytu wirtualnego dysku twardego (VHD). Zalecamy wyczyszczenie maszyny wirtualnej przed wykonaniem migawki, aby wyczyścić wszystkie procesy, które są w toku. Aby wykonać migawkę dysku systemu operacyjnego, wykonaj następujące kroki:

1. Przejdź do [Azure Portal](https://portal.azure.com). Wybierz pozycję **maszyny wirtualne** z paska bocznego, a następnie wybierz maszynę wirtualną, która ma problem.
1. W okienku po lewej stronie wybierz pozycję **dyski**, a następnie wybierz nazwę dysku systemu operacyjnego.
    ![Obraz przedstawiający nazwę dysku systemu operacyjnego](./media/troubleshoot-recovery-disks-portal-windows/select-osdisk.png)
1. Na stronie **Przegląd** dysku systemu operacyjnego, a następnie wybierz pozycję **Utwórz migawkę**.
1. Utwórz migawkę w tej samej lokalizacji, w której znajduje się dysk systemu operacyjnego.

## <a name="create-a-disk-from-the-snapshot"></a>Utwórz dysk na podstawie migawki
Aby utworzyć dysk na podstawie migawki, wykonaj następujące kroki:

1. Wybierz **Cloud Shell** z Azure Portal.

    ![Obraz informacji o otwartych Cloud Shell](./media/troubleshoot-recovery-disks-portal-windows/cloud-shell.png)
1. Uruchom następujące polecenia programu PowerShell, aby utworzyć dysk zarządzany na podstawie migawki. Te przykładowe nazwy należy zastąpić odpowiednimi nazwami.

    ```powershell
    #Provide the name of your resource group
    $resourceGroupName ='myResourceGroup'
    
    #Provide the name of the snapshot that will be used to create Managed Disks
    $snapshotName = 'mySnapshot' 
    
    #Provide the name of theManaged Disk
    $diskName = 'newOSDisk'
    
    #Provide the size of the disks in GB. It should be greater than the VHD file size. In this sample, the size of the snapshot is 127 GB. So we set the disk size to 128 GB.
    $diskSize = '128'
    
    #Provide the storage type for Managed Disk. Premium_LRS or Standard_LRS.
    $storageType = 'Standard_LRS'
    
    #Provide the Azure region (e.g. westus) where Managed Disks will be located.
    #This location should be same as the snapshot location
    #Get all the Azure location using command below:
    #Get-AzLocation
    $location = 'westus'
    
    $snapshot = Get-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName 
     
    $diskConfig = New-AzDiskConfig -AccountType $storageType -Location $location -CreateOption Copy -SourceResourceId $snapshot.Id
     
    New-AzDisk -Disk $diskConfig -ResourceGroupName $resourceGroupName -DiskName $diskName
    ```
3. Jeśli polecenia zostaną wykonane pomyślnie, zobaczysz nowy dysk w podanej grupie zasobów.

## <a name="attach-disk-to-another-vm"></a>Dołącz dysk do innej maszyny wirtualnej
W następnych kilku krokach do rozwiązywania problemów służy inna maszyna wirtualna. Po dołączeniu dysku do maszyny wirtualnej rozwiązywania problemów można przeglądać i edytować zawartość tego dysku. Ten proces umożliwia poprawienie wszystkich błędów konfiguracji lub przejrzenie dodatkowych plików dziennika aplikacji lub systemu. Aby dołączyć dysk do innej maszyny wirtualnej, wykonaj następujące kroki:

1. Wybierz grupę zasobów z portalu, a następnie wybierz maszynę wirtualną rozwiązywania problemów. Wybierz pozycję **dyski**, wybierz pozycję **Edytuj**, a następnie kliknij pozycję **Dodaj dysk danych**:

    ![Dołącz istniejący dysk w portalu](./media/troubleshoot-recovery-disks-portal-windows/attach-existing-disk.png)

2. Z listy **dyski danych** wybierz dysk systemu operacyjnego zidentyfikowanej maszyny wirtualnej. Jeśli nie widzisz dysku systemu operacyjnego, upewnij się, że Rozwiązywanie problemów z maszyną wirtualną i dyskiem systemu operacyjnego znajduje się w tym samym regionie (lokalizacja). 
3. Wybierz pozycję **Zapisz** , aby zastosować zmiany.

## <a name="mount-the-attached-data-disk"></a>Instalowanie dołączonego dysku z danymi

> [!NOTE]
> W poniższych przykładach szczegółowo opisano kroki wymagane na maszynie wirtualnej Ubuntu. Jeśli używasz innego dystrybucji systemu Linux, takiego jak Red Hat Enterprise Linux lub SUSE, lokalizacje i polecenia w pliku dziennika `mount` mogą być nieco inne. Zapoznaj się z dokumentacją dotyczącą określonych dystrybucji, aby uzyskać odpowiednie zmiany w poleceniach.

1. Użyj odpowiednich poświadczeń protokołu SSH do rozwiązywania problemów z maszyną wirtualną. Jeśli ten dysk jest pierwszym dyskiem danych dołączonym do maszyny wirtualnej rozwiązywania problemów, prawdopodobnie jest on połączony z `/dev/sdc` . Użyj `dmseg` , aby wyświetlić listę dołączonych dysków:

    ```bash
    dmesg | grep SCSI
    ```
    Dane wyjściowe są podobne do poniższego przykładu:

    ```bash
    [    0.294784] SCSI subsystem initialized
    [    0.573458] Block layer SCSI generic (bsg) driver version 0.4 loaded (major 252)
    [    7.110271] sd 2:0:0:0: [sda] Attached SCSI disk
    [    8.079653] sd 3:0:1:0: [sdb] Attached SCSI disk
    [ 1828.162306] sd 5:0:0:0: [sdc] Attached SCSI disk
    ```

    W poprzednim przykładzie dysk systemu operacyjnego jest na dysku, `/dev/sda` a dysk tymczasowy podany dla każdej maszyny wirtualnej ma wartość `/dev/sdb` . Jeśli masz wiele dysków danych, powinny one znajdować się w `/dev/sdd` , `/dev/sde` i tak dalej.

2. Utwórz katalog, aby zainstalować istniejący wirtualny dysk twardy. Poniższy przykład tworzy katalog o nazwie `troubleshootingdisk` :

    ```bash
    sudo mkdir /mnt/troubleshootingdisk
    ```

3. Jeśli masz wiele partycji na istniejącym wirtualnym dysku twardym, zainstaluj wymaganą partycję. W poniższym przykładzie jest instalowana Pierwsza partycja podstawowa w `/dev/sdc1` :

    ```bash
    sudo mount /dev/sdc1 /mnt/troubleshootingdisk
    ```

    > [!NOTE]
    > Najlepszym rozwiązaniem jest zainstalowanie dysków z danymi na maszynach wirtualnych na platformie Azure przy użyciu uniwersalnego identyfikatora UUID dla wirtualnego dysku twardego. W przypadku tego krótkiego scenariusza rozwiązywania problemów zainstalowanie wirtualnego dysku twardego przy użyciu identyfikatora UUID nie jest konieczne. Jednak w normalnych warunkach Edycja `/etc/fstab` w celu zainstalowania wirtualnych dysków twardych przy użyciu nazwy urządzenia zamiast identyfikatora UUID może spowodować niepowodzenie rozruchu maszyny wirtualnej.


## <a name="fix-issues-on-original-virtual-hard-disk"></a>Rozwiązywanie problemów dotyczących oryginalnego wirtualnego dysku twardego
Po zainstalowaniu istniejącego wirtualnego dysku twardego można wykonać wszelkie czynności konserwacyjne i rozwiązywania problemów zgodnie z wymaganiami. Po usunięciu problemów wykonaj następujące kroki.

## <a name="unmount-and-detach-original-virtual-hard-disk"></a>Odinstalowywanie i odłączanie oryginalnego wirtualnego dysku twardego
Po rozwiązaniu błędów Odłącz istniejący wirtualny dysk twardy z maszyny wirtualnej rozwiązywania problemów. Nie można użyć wirtualnego dysku twardego z żadną inną maszyną wirtualną, dopóki dzierżawa nie dołączy wirtualnego dysku twardego do maszyny wirtualnej rozwiązywania problemów.

1. Odinstaluj istniejący wirtualny dysk twardy z sesji SSH do rozwiązywania problemów z maszyną wirtualną. Najpierw Zmień katalog nadrzędny dla punktu instalacji:

    ```bash
    cd /
    ```

    Odinstaluj już istniejący wirtualny dysk twardy. Poniższy przykład Odinstalowuje urządzenie w `/dev/sdc1` :

    ```bash
    sudo umount /dev/sdc1
    ```

2. Teraz Odłącz wirtualny dysk twardy od maszyny wirtualnej. Wybierz maszynę wirtualną w portalu, a następnie kliknij pozycję **dyski**. Wybierz istniejący wirtualny dysk twardy, a następnie kliknij przycisk **Odłącz**:

    ![Odłącz istniejący wirtualny dysk twardy](./media/troubleshoot-recovery-disks-portal-windows/detach-disk.png)

    Poczekaj, aż maszyna wirtualna pomyślnie odłączy dysk danych przed kontynuowaniem.

## <a name="swap-the-os-disk-for-the-vm"></a>Wymiana dysku systemu operacyjnego dla maszyny wirtualnej

Azure Portal teraz obsługuje zmianę dysku systemu operacyjnego maszyny wirtualnej. W tym celu wykonaj następujące kroki:

1. Przejdź do [Azure Portal](https://portal.azure.com). Wybierz pozycję **maszyny wirtualne** z paska bocznego, a następnie wybierz maszynę wirtualną, która ma problem.
1. W okienku po lewej stronie wybierz pozycję **dyski**, a następnie wybierz pozycję **Zamień dysk systemu operacyjnego**.
        ![Obraz dotyczący wymiany dysku systemu operacyjnego w Azure Portal](./media/troubleshoot-recovery-disks-portal-windows/swap-os-ui.png)

1. Wybierz nowy dysk, który został naprawiony, a następnie wpisz nazwę maszyny wirtualnej, aby potwierdzić zmianę. Jeśli dysk nie jest wyświetlany na liście, odczekaj 10% minut po odłączeniu dysku od maszyny wirtualnej rozwiązywania problemów. Upewnij się również, że dysk znajduje się w tej samej lokalizacji co maszyna wirtualna.
1. Wybierz przycisk OK.

## <a name="next-steps"></a>Następne kroki
Jeśli masz problemy z nawiązywaniem połączenia z maszyną wirtualną, zobacz [Rozwiązywanie problemów z połączeniami SSH z maszyną wirtualną platformy Azure](troubleshoot-ssh-connection.md). Problemy z uzyskiwaniem dostępu do aplikacji uruchomionych na maszynie wirtualnej można znaleźć [w temacie Rozwiązywanie problemów z łącznością aplikacji na maszynie wirtualnej z systemem Linux](./troubleshoot-app-connection.md?toc=/azure/virtual-machines/linux/toc.json).

Aby uzyskać więcej informacji o korzystaniu z Menedżer zasobów, zobacz [Azure Resource Manager omówienie](../../azure-resource-manager/management/overview.md).
