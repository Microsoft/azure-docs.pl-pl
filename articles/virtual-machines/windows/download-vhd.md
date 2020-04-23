---
title: Pobieranie dysku VHD systemu Windows z platformy Azure
description: Pobierz dysk VHD systemu Windows przy użyciu witryny Azure portal.
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: article
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: e87f2436f69abed4c0d0ee415226f99b52f2e804
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085369"
---
# <a name="download-a-windows-vhd-from-azure"></a>Pobieranie dysku VHD systemu Windows z platformy Azure

W tym artykule dowiesz się, jak pobrać plik wirtualnego dysku twardego systemu Windows (VHD) z platformy Azure przy użyciu witryny Azure portal.

## <a name="optional-generalize-the-vm"></a>Opcjonalnie: Uogólnianie maszyny Wirtualnej

Jeśli chcesz użyć dysku wirtualnego jako [obrazu](tutorial-custom-images.md) do tworzenia innych maszyn wirtualnych, należy użyć [Sysprep](https://docs.microsoft.com/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) do uogólniania systemu operacyjnego. 

Aby użyć dysku wirtualnego jako obrazu do tworzenia innych maszyn wirtualnych, uogólniaj maszynę wirtualną.

1. Jeśli jeszcze tego nie zrobiono, zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. [Połącz się z maszyną wirtualną](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
3. Na maszynie wirtualnej otwórz okno wiersza polecenia jako administrator.
4. Zmień katalog na *%windir%\system32\sysprep* i uruchom program sysprep.exe.
5. W oknie dialogowym Narzędzie przygotowania systemu wybierz pozycję **Enter System Out-of-Box Experience (OOBE)** i upewnij się, że wybrano **uogólnienie.**
6. W obszarze Opcje zamykania wybierz pozycję **Zamknięcie**, a następnie kliknij przycisk **OK**. 


## <a name="stop-the-vm"></a>Zatrzymywanie maszyny wirtualnej

Nie można pobrać dysku VHD z platformy Azure, jeśli jest on dołączony do uruchomionej maszyny Wirtualnej. Aby pobrać dysk VHD, musisz zatrzymać maszynę wirtualną. 

1. W menu Centrum w witrynie Azure portal kliknij polecenie **Maszyny wirtualne**.
1. Wybierz maszynę wirtualną z listy.
1. Na bloku maszyny Wirtualnej kliknij przycisk **Zatrzymaj**.


## <a name="generate-download-url"></a>Generowanie adresu URL pobierania

Aby pobrać plik VHD, musisz wygenerować adres URL [sygnatury dostępu współdzielonego (SAS).](../../storage/common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) Po wygenerowaniu adresu URL do adresu URL jest przypisywany czas wygaśnięcia.

1. Na stronie maszyny Wirtualnej kliknij pozycję **Dyski** w menu po lewej stronie.
1. Wybierz dysk systemu operacyjnego dla maszyny Wirtualnej.
1. Na stronie dysku wybierz pozycję **Eksportuj dysk** z lewego menu.
1. Domyślny czas wygaśnięcia adresu URL wynosi *3600* sekund. Zwiększ tę liczbę do **36000** dla dysków z systemem operacyjnym Windows.
1. Kliknij **pozycję Generuj adres URL**.

> [!NOTE]
> Czas wygaśnięcia jest wydłuża się od wartości domyślnej, aby zapewnić wystarczająco dużo czasu na pobranie dużego pliku VHD dla systemu operacyjnego Windows Server. Można oczekiwać, że plik VHD zawierający system operacyjny Windows Server potrwa kilka godzin w zależności od połączenia. Jeśli pobierasz dysk VHD dla dysku danych, wystarczy czas domyślny. 
> 
> 

## <a name="download-vhd"></a>Pobieranie VHD

1. Pod wygenerowanym adresem URL kliknij pozycję Pobierz plik VHD.
1. Może być konieczne **kliknięcie przycisku Zapisz** w przeglądarce, aby rozpocząć pobieranie. Domyślną nazwą pliku VHD jest *abcd*.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [przekazać plik VHD na platformę Azure](upload-generalized-managed.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
- [Tworzenie dysków zarządzanych z dysków niezarządzanych na koncie magazynu](attach-disk-ps.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
- [Zarządzanie dyskami platformy Azure za pomocą programu PowerShell](tutorial-manage-data-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

