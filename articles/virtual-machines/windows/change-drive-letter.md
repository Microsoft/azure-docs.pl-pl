---
title: 'Tworzenie dysku D: dysku danych maszyny wirtualnej '
description: 'Opisuje sposób zmiany liter dysku dla maszyny wirtualnej z systemem Windows, aby można było używać dysku D: jako dysku danych.'
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/02/2018
ms.author: cynthn
ms.openlocfilehash: 87aa1344b3fbe0d11c1c5cdfa8a56560d67eb54f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555554"
---
# <a name="use-the-d-drive-as-a-data-drive-on-a-windows-vm"></a>Używanie dysku D: jako dysku z danymi na maszynie wirtualnej z systemem Windows
Jeśli aplikacja musi używać dysku D do przechowywania danych, postępuj zgodnie z tymi instrukcjami, aby użyć innej litery dysku dla dysku tymczasowego. Nie należy używać dysku tymczasowego do przechowywania danych, które należy zachować.

Jeśli zmienisz rozmiar lub **zatrzymasz (Cofnij przydział)** maszyny wirtualnej, może to spowodować umieszczenie maszyny wirtualnej w nowej funkcji hypervisor. Planowane lub nieplanowane zdarzenie konserwacji może również spowodować wyzwolenie tego położenia. W tym scenariuszu dysk tymczasowy zostanie ponownie przypisany do pierwszej dostępnej litery dysku. Jeśli masz aplikację, która odpowiednio wymaga dysku D:, musisz wykonać następujące kroki, aby tymczasowo przenieść pagefile.sys, dołączyć nowy dysk danych i przypisać go do litery D, a następnie przenieść pagefile.sys z powrotem do dysku tymczasowego. Po zakończeniu platforma Azure nie przejdzie z powrotem D: Jeśli maszyna wirtualna przejdzie do innej funkcji hypervisor.

Aby uzyskać więcej informacji na temat sposobu korzystania z dysku tymczasowego przez platformę Azure, zobacz [opis dysku tymczasowego na Microsoft Azure Virtual Machines](/archive/blogs/mast/understanding-the-temporary-drive-on-windows-azure-virtual-machines)

## <a name="attach-the-data-disk"></a>Dołącz dysk z danymi
Najpierw należy dołączyć dysk danych do maszyny wirtualnej. Aby to zrobić przy użyciu portalu, zobacz [jak dołączyć dysk danych zarządzanych w Azure Portal](attach-managed-disk-portal.md).

## <a name="temporarily-move-pagefilesys-to-c-drive"></a>Tymczasowo Przenieś pagefile.sys do dysku C
1. Nawiąż połączenie z maszyną wirtualną. 
2. Kliknij prawym przyciskiem myszy menu **Start** i wybierz pozycję **system**.
3. W menu po lewej stronie wybierz pozycję **Zaawansowane ustawienia systemu**.
4. W sekcji **wydajność** wybierz pozycję **Ustawienia**.
5. Wybierz kartę **Zaawansowane**.
6. W sekcji **pamięć wirtualna** wybierz pozycję **Zmień**.
7. Wybierz dysk **C** , a następnie kliknij pozycję **rozmiar zarządzany przez system** , a następnie kliknij pozycję **Ustaw**.
8. Wybierz dysk **D** , a następnie kliknij pozycję **Brak pliku stronicowania** , a następnie kliknij pozycję **Ustaw**.
9. Kliknij pozycję Zastosuj. Zostanie wyświetlone ostrzeżenie, że należy ponownie uruchomić komputer, aby zmiany zostały zastosowane.
10. Uruchom ponownie maszynę wirtualną.

## <a name="change-the-drive-letters"></a>Zmień litery dysku
1. Po ponownym uruchomieniu maszyny wirtualnej Zaloguj się ponownie do maszyny wirtualnej.
2. Kliknij menu **Start** i wpisz **diskmgmt. msc** i naciśnij klawisz ENTER. Zarządzanie dyskami rozpocznie się.
3. Kliknij prawym przyciskiem myszy pozycję **D**, tymczasowy dysk magazynu i wybierz polecenie **Zmień literę dysku i ścieżki**.
4. W obszarze litera dysku wybierz nowy dysk, taki jak **T** , a następnie kliknij przycisk **OK**. 
5. Kliknij prawym przyciskiem myszy dysk danych i wybierz polecenie **Zmień literę dysku i ścieżki**.
6. W obszarze litera dysku wybierz pozycję dysk **D** , a następnie kliknij przycisk **OK**. 

## <a name="move-pagefilesys-back-to-the-temporary-storage-drive"></a>Przenieś pagefile.sys z powrotem do tymczasowego dysku magazynu
1. Kliknij prawym przyciskiem myszy menu **Start** i wybierz pozycję **system**
2. W menu po lewej stronie wybierz pozycję **Zaawansowane ustawienia systemu**.
3. W sekcji **wydajność** wybierz pozycję **Ustawienia**.
4. Wybierz kartę **Zaawansowane**.
5. W sekcji **pamięć wirtualna** wybierz pozycję **Zmień**.
6. Wybierz dysk systemu operacyjnego **C** i kliknij opcję **Brak pliku stronicowania** , a następnie kliknij przycisk **Ustaw**.
7. Wybierz dysk magazynu tymczasowego **T** , a następnie kliknij pozycję **rozmiar zarządzany przez system** , a następnie kliknij pozycję **Ustaw**.
8. Kliknij pozycję **Zastosuj**. Zostanie wyświetlone ostrzeżenie, że należy ponownie uruchomić komputer, aby zmiany zostały zastosowane.
9. Uruchom ponownie maszynę wirtualną.

## <a name="next-steps"></a>Następne kroki
* Można zwiększyć ilość miejsca dostępnego dla maszyny wirtualnej przez [dołączenie dodatkowego dysku z danymi](attach-managed-disk-portal.md).
