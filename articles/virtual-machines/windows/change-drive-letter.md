---
title: 'Uczynienie dysku D: maszyny Wirtualnej dyskiem danych '
description: 'W tym artykule opisano sposób zmieniania liter dysków dla maszyny Wirtualnej systemu Windows, tak aby można było używać dysku D: jako dysku danych.'
services: virtual-machines-windows
author: cynthn
ms.service: virtual-machines-windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: article
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: 15df3178f2860fa066a82cb1429e0c1a6e5c2b08
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083426"
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Używanie dysku D: jako dysku danych na maszynie Wirtualnej systemu Windows
Jeśli aplikacja musi używać dysku D do przechowywania danych, postępuj zgodnie z tymi instrukcjami, aby użyć innej litery dysku dla dysku tymczasowego. Nigdy nie używaj dysku tymczasowego do przechowywania danych, które należy zachować.

Jeśli zmieniasz rozmiar lub **zatrzymasz (cofnięto alokację)** maszyny wirtualnej, może to spowodować umieszczenie maszyny wirtualnej w nowym hipernadzorcy. Zdarzenie planowanej lub nieplanowanej konserwacji może również wywołać to umieszczenie. W tym scenariuszu dysk tymczasowy zostanie ponownie przypisany do pierwszej dostępnej litery dysku. Jeśli masz aplikację, która w szczególności wymaga dysku D:, należy wykonać następujące kroki, aby tymczasowo przenieść pagefile.sys, dołączyć nowy dysk danych i przypisać mu literę D, a następnie przenieść pagefile.sys z powrotem do tymczasowego dysku. Po zakończeniu platformy Azure nie będzie odbierze D: jeśli maszyna wirtualna przenosi się do innego funkcji hypervisor.

Aby uzyskać więcej informacji o tym, jak platforma Azure korzysta z dysku tymczasowego, zobacz [Opis dysku tymczasowego na maszynach wirtualnych platformy Microsoft Azure](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

## <a name="attach-the-data-disk"></a>Dołączanie dysku danych
Najpierw należy dołączyć dysk danych do maszyny wirtualnej. Aby to zrobić za pomocą portalu, zobacz [Jak dołączyć zarządzany dysk danych w witrynie Azure portal](attach-managed-disk-portal.md).

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Tymczasowe przenoszenie pliku pagefile.sys do dysku C
1. Nawiąż połączenie z maszyną wirtualną. 
2. Kliknij prawym przyciskiem myszy menu **Start** i wybierz polecenie **System**.
3. W menu po lewej stronie wybierz pozycję **Zaawansowane ustawienia systemowe**.
4. W sekcji **Wydajność** wybierz pozycję **Ustawienia**.
5. Wybierz kartę **Zaawansowane**.
6. W sekcji **Pamięć wirtualna** wybierz pozycję **Zmień**.
7. Wybierz dysk **C,** a następnie kliknij pozycję **Rozmiar zarządzany przez system,** a następnie kliknij przycisk **Ustaw**.
8. Wybierz dysk **D,** a następnie kliknij pozycję **Brak pliku stronicowania,** a następnie kliknij przycisk **Ustaw**.
9. Kliknij przycisk Zastosuj. Zostanie otrzymasz ostrzeżenie, że komputer musi zostać ponownie uruchomiony, aby zmiany miały wpływ.
10. Uruchom ponownie maszynę wirtualną.

## <a name="change-the-drive-letters"></a>Zmienianie liter dysków
1. Po ponownym uruchomieniu maszyny Wirtualnej zaloguj się ponownie na maszynie wirtualnej.
2. Kliknij menu **Start** i wpisz **diskmgmt.msc** i naciśnij klawisz Enter. Rozpocznie się zarządzanie dyskami.
3. Kliknij prawym przyciskiem myszy **na D**, dysk magazynu tymczasowego i wybierz pozycję **Zmień literę dysku i ścieżki**.
4. W obszarze Litera dysku wybierz nowy dysk, taki jak **T,** a następnie kliknij przycisk **OK**. 
5. Kliknij prawym przyciskiem myszy dysk danych i wybierz polecenie **Zmień literę dysku i ścieżki**.
6. W obszarze Litera dysku wybierz dysk **D,** a następnie kliknij przycisk **OK**. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Przenoszenie pliku pagefile.sys z powrotem na tymczasowy dysk pamięci masowej
1. Kliknij prawym przyciskiem myszy menu **Start** i wybierz polecenie **System**
2. W menu po lewej stronie wybierz pozycję **Zaawansowane ustawienia systemowe**.
3. W sekcji **Wydajność** wybierz pozycję **Ustawienia**.
4. Wybierz kartę **Zaawansowane**.
5. W sekcji **Pamięć wirtualna** wybierz pozycję **Zmień**.
6. Zaznacz dysk systemu operacyjnego **C** i kliknij pozycję **Brak pliku stronicowania,** a następnie kliknij przycisk **Ustaw**.
7. Wybierz dysk magazynu tymczasowego **T,** a następnie kliknij pozycję **Rozmiar zarządzany przez system,** a następnie kliknij przycisk **Ustaw**.
8. Kliknij przycisk **Zastosuj**. Zostanie otrzymasz ostrzeżenie, że komputer musi zostać ponownie uruchomiony, aby zmiany miały wpływ.
9. Uruchom ponownie maszynę wirtualną.

## <a name="next-steps"></a>Następne kroki
* Można zwiększyć ilość miejsca dostępnego dla maszyny wirtualnej, [dołączając dodatkowy dysk danych](attach-managed-disk-portal.md).

