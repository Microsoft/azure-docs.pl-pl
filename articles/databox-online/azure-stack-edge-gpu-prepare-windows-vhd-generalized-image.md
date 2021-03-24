---
title: Tworzenie obrazów maszyn wirtualnych z uogólnionego obrazu wirtualnego dysku twardego systemu Windows dla urządzenia z systemem Azure Stack EDGE Pro GPU
description: Opisuje, jak obrazy maszyn wirtualnych z uogólnionych obrazów rozpoczynają się od wirtualnego dysku twardego systemu Windows lub dysku VHDX. Ten uogólniony obraz służy do tworzenia obrazów maszyn wirtualnych, które mają być używane z maszynami wirtualnymi wdrożonymi na urządzeniu z systemem Azure Stack Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/18/2021
ms.author: alkohli
ms.openlocfilehash: 978099accd57d15c750a27f77b2e220f569a2dd0
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "104962612"
---
# <a name="use-generalized-image-from-windows-vhd-to-create-a-vm-image-for-your-azure-stack-edge-pro-device"></a>Użyj uogólnionego obrazu z wirtualnego dysku twardego systemu Windows, aby utworzyć obraz maszyny wirtualnej dla urządzenia Azure Stack Edge

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Aby wdrażać maszyny wirtualne na urządzeniu Azure Stack EDGE Pro, musisz mieć możliwość tworzenia niestandardowych obrazów maszyn wirtualnych, których można użyć do tworzenia maszyn wirtualnych. W tym artykule opisano kroki wymagane do przygotowania pliku VHD lub VHDX systemu Windows w celu utworzenia uogólnionego obrazu. Ten uogólniony obraz jest następnie używany do utworzenia obrazu maszyny wirtualnej dla urządzenia Azure Stack Edge. 

## <a name="about-preparing-windows-vhd"></a>Informacje o przygotowaniu wirtualnego dysku twardego systemu Windows

Plik VHD lub VHDX systemu Windows może służyć do tworzenia *uogólnionego* obrazu lub *wyspecjalizowanego* obrazu. Poniższa tabela zawiera podsumowanie najważniejszych różnic między *uogólnionymi* i *wyspecjalizowanymi* obrazami.


|Typ obrazu  |Uogólniony  |Wyspecjalizowany  |
|---------|---------|---------|
|Cel     |Wdrożone w dowolnym systemie         | Przeznaczony dla określonego systemu        |
|Instalacja po rozruchu     | Instalacja jest wymagana podczas pierwszego rozruchu maszyny wirtualnej.          | Instalacja nie jest wymagana. <br> Platforma włącza maszynę wirtualną.        |
|Konfigurowanie     |Nazwa hosta, użytkownik administracyjny i inne wymagane ustawienia specyficzne dla maszyny wirtualnej.         |Całkowicie wstępnie skonfigurowane.         |
|Używany, gdy     |Tworzenie wielu nowych maszyn wirtualnych na podstawie tego samego obrazu.         |Migrowanie konkretnej maszyny lub przywracanie maszyny wirtualnej z poprzedniej kopii zapasowej.         |


W tym artykule opisano kroki wymagane do wdrożenia z uogólnionego obrazu. Aby wdrożyć z wyspecjalizowanego obrazu, zobacz [Korzystanie z wyspecjalizowanego wirtualnego dysku twardego systemu Windows](azure-stack-edge-placeholder.md) dla urządzenia.

> [!IMPORTANT]
> Ta procedura nie obejmuje przypadków, w których źródłowy wirtualny dysk twardy jest skonfigurowany z niestandardowymi konfiguracjami i ustawieniami. Na przykład może być wymagane wykonanie dodatkowych czynności w celu uogólnienia dysku VHD zawierającego niestandardowe reguły zapory lub ustawienia serwera proxy. Aby uzyskać więcej informacji na temat dodatkowych akcji, zobacz [Przygotowywanie wirtualnego dysku twardego systemu Windows do przekazania do platformy azure Virtual Machines](../virtual-machines/windows/prepare-for-upload-vhd-image.md).


## <a name="vm-image-workflow"></a>Przepływ pracy obrazu maszyny wirtualnej

Przepływ pracy wysokiego poziomu służący do przygotowania wirtualnego dysku twardego systemu Windows do użycia jako uogólniony obraz zawiera następujące czynności:

1. Przekonwertuj źródłowy plik VHD lub VHDX na dysk VHD o stałym rozmiarze.
1. Utwórz maszynę wirtualną w funkcji Hyper-V przy użyciu stałego dysku VHD.
1. Nawiąż połączenie z maszyną wirtualną funkcji Hyper-V.
1. Uogólnij wirtualny dysk twardy przy użyciu narzędzia *Sysprep* . 
1. Skopiuj uogólniony obraz do magazynu obiektów BLOB.
1. Korzystaj z uogólnionego obrazu, aby wdrażać maszyny wirtualne na urządzeniu. Aby uzyskać więcej informacji, zobacz jak [wdrożyć maszynę wirtualną za pośrednictwem Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md) lub [wdrożyć maszynę wirtualną za pomocą programu PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md).


## <a name="prerequisites"></a>Wymagania wstępne

Przed przystąpieniem do przygotowania wirtualnego dysku twardego systemu Windows do użycia jako uogólnionego obrazu na Azure Stack Edge upewnij się, że:

- Masz dysk VHD lub VHDX zawierający obsługiwaną wersję systemu Windows. Zobacz [obsługiwane systemy operacyjne gościa]() dla Twojego Azure Stack EDGE Pro. 
- Masz dostęp do klienta systemu Windows z zainstalowanym menedżerem funkcji Hyper-V. 
- Masz dostęp do konta usługi Azure Blob Storage do przechowywania wirtualnego dysku twardego po jego przygotowaniu.

## <a name="prepare-a-generalized-windows-image-from-vhd"></a>Przygotowywanie uogólnionego obrazu systemu Windows z dysku VHD

## <a name="convert-to-a-fixed-vhd"></a>Konwertowanie na stały dysk VHD 

Aby można było tworzyć obrazy maszyn wirtualnych, konieczne będzie posiadanie dysków VHD o stałym rozmiarze. Konieczne będzie przekonwertowanie źródłowego wirtualnego dysku twardego systemu Windows lub dysku VHDX na stały dysk VHD. Wykonaj następujące kroki:

1. Otwórz Menedżera funkcji Hyper-V na swoim komputerze klienckim. Przejdź do pozycji **Edytuj dysk**.

    ![Otwórz Menedżera funkcji Hyper-V](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-1.png)

1. Na stronie **zanim rozpoczniesz** wybierz pozycję **dalej>**.

1. Na stronie **lokalizowanie wirtualnego dysku twardego** przejdź do lokalizacji źródłowego wirtualnego dysku twardego systemu Windows lub dysku VHDX, który chcesz skonwertować. Wybierz pozycję **dalej>**.

    ![Strona lokalizowanie wirtualnego dysku twardego](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-2.png)

1. Na stronie **Wybierz akcję** wybierz pozycję **Konwertuj** i wybierz pozycję **dalej>**.

    ![Wybierz stronę akcji](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-3.png)

1. Na stronie **Wybierz format dysku** wybierz pozycję Format dysku **VHD** , a następnie wybierz przycisk **dalej>**.

   ![Strona wybierania formatu dysku](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-4.png)


1. Na stronie **Wybieranie typu dysku** wybierz pozycję **stały rozmiar** i wybierz pozycję **dalej>**.

   ![Strona wybierania typu dysku](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-5.png)


1. Na stronie **Konfigurowanie dysku** przejdź do lokalizacji i określ nazwę dysku VHD o stałym rozmiarze. Wybierz pozycję **dalej>**.

   ![Strona Konfigurowanie dysku](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/convert-fixed-vhd-6.png)


1. Przejrzyj podsumowanie i wybierz pozycję **Zakończ**. Konwersja dysku VHD lub VHDX trwa kilka minut. Czas konwersji zależy od rozmiaru dysku źródłowego. 

<!--
1. Run PowerShell on your Windows client.
1. Run the following command:

    ```powershell
    Convert-VHD -Path <source VHD path> -DestinationPath <destination-path.vhd> -VHDType Fixed 
    ```
-->
Ten stały dysk VHD zostanie użyty dla wszystkich kolejnych kroków opisanych w tym artykule.


## <a name="create-a-hyper-v-vm-from-fixed-vhd"></a>Tworzenie maszyny wirtualnej funkcji Hyper-V na podstawie stałego dysku VHD

1. W **Menedżerze funkcji Hyper-V** w okienku zakres kliknij prawym przyciskiem myszy węzeł systemu, aby otworzyć menu kontekstowe, a następnie wybierz pozycję **Nowa**  >  **maszyna wirtualna**.

    ![Wybierz nową maszynę wirtualną w okienku zakres](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-1.png)

1. Na stronie **zanim rozpoczniesz** Kreatora nowej maszyny wirtualnej wybierz pozycję **dalej**.

1. Na stronie **Określanie nazwy i lokalizacji** Podaj **nazwę** i **lokalizację** maszyny wirtualnej. Wybierz opcję **Dalej**.

    ![Określ nazwę i lokalizację maszyny wirtualnej](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-2.png)

1. Na stronie **Określanie generacji** wybierz opcję **generacja 1** dla typu obrazu urządzenia VHD, a następnie wybierz przycisk **dalej**.    

    ![Określ generację](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-3.png)

1. Przypisz żądaną konfigurację pamięci i sieci.

1. Na stronie **Podłączanie wirtualnego dysku twardego** wybierz opcję **Użyj istniejącego wirtualnego dysku twardego**, określ lokalizację utworzonego wcześniej wirtualnego dysku twardego systemu Windows, a następnie wybierz przycisk **dalej**.

    ![Strona łączenie wirtualnego dysku twardego](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/create-virtual-machine-4.png)

1. Przejrzyj **Podsumowanie** , a następnie wybierz pozycję **Zakończ** , aby utworzyć maszynę wirtualną.

Tworzenie maszyny wirtualnej trwa kilka minut.
     

## <a name="connect-to-the-hyper-v-vm"></a>Nawiązywanie połączenia z maszyną wirtualną funkcji Hyper-V

Maszyna wirtualna jest wyświetlana na liście maszyn wirtualnych w systemie klienta. 


1. Wybierz maszynę wirtualną, a następnie kliknij prawym przyciskiem myszy i wybierz pozycję **Uruchom**. 

    ![Wybierz maszynę wirtualną i uruchom ją](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/connect-virtual-machine-2.png)

2. Na maszynie wirtualnej jest pokazywana wartość **uruchomiona**. Wybierz maszynę wirtualną, a następnie kliknij prawym przyciskiem myszy i wybierz pozycję **Połącz**.

    ![Łączenie z maszyną wirtualną](./media/azure-stack-edge-gpu-prepare-windows-vhd-generalized-image/connect-virtual-machine-4.png)

Po nawiązaniu połączenia z maszyną wirtualną Ukończ pracę Kreatora instalacji komputera, a następnie zaloguj się do maszyny wirtualnej.


## <a name="generalize-the-vhd"></a>Uogólnianie wirtualnego dysku twardego  

Użyj narzędzia *Sysprep* , aby UOGÓLNIĆ dysk VHD. 

1. Na maszynie wirtualnej Otwórz wiersz polecenia.
1. Uruchom następujące polecenie, aby uogólnić dysk VHD. 

    ```
    c:\windows\system32\sysprep\sysprep.exe /oobe /generalize /shutdown /mode:vm
    ```
    Aby uzyskać szczegółowe informacje, zobacz  [Sysprep (Przygotowanie systemu) — Omówienie](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview).
1.  Po zakończeniu wykonywania polecenia maszyna wirtualna zostanie zamknięta. **Nie uruchamiaj ponownie maszyny wirtualnej**.

## <a name="upload-the-vhd-to-azure-blob-storage"></a>Przekazywanie wirtualnego dysku twardego do usługi Azure Blob Storage

Wirtualny dysk twardy może być teraz używany do tworzenia uogólnionego obrazu na Azure Stack Edge. 

1. Przekaż dysk VHD do usługi Azure Blob Storage. Zapoznaj się ze szczegółowymi instrukcjami w sekcji [przekazywanie wirtualnego dysku twardego przy użyciu Eksplorator usługi Azure Storage](../devtest-labs/devtest-lab-upload-vhd-using-storage-explorer.md).
1. Po zakończeniu przekazywania możesz użyć przekazanego obrazu do tworzenia obrazów maszyn wirtualnych i maszyn wirtualnych. 

<!-- this should be added to deploy VM articles - If you experience any issues creating VMs from your new image, you can use VM console access to help troubleshoot. For information on console access, see [link].-->



## <a name="next-steps"></a>Następne kroki

W zależności od rodzaju wdrożenia można wybrać jedną z poniższych procedur.

- [Wdróż maszynę wirtualną na podstawie uogólnionego obrazu za pomocą Azure Portal](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)
- [Wdróż maszynę wirtualną na podstawie uogólnionego obrazu za pomocą Azure PowerShell](azure-stack-edge-gpu-deploy-virtual-machine-powershell.md)  
