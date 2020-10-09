---
title: Rozwiązywanie problemów z wadliwą maszyną wirtualną platformy Azure przy użyciu wirtualizacji zagnieżdżonej na platformie Azure | Microsoft Docs
description: Jak rozwiązywać problemy z maszyną wirtualną platformy Azure przy użyciu wirtualizacji zagnieżdżonej na platformie Azure
services: virtual-machines-windows
documentationcenter: ''
author: glimoli
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/19/2019
ms.author: genli
ms.openlocfilehash: e1acfc3216ccfaeac035f1ff31e82c7b67c17daf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "76119622"
---
# <a name="troubleshoot-a-faulty-azure-vm-by-using-nested-virtualization-in-azure"></a>Rozwiązywanie problemów z wadliwą maszyną wirtualną platformy Azure przy użyciu wirtualizacji zagnieżdżonej na platformie Azure

W tym artykule pokazano, jak utworzyć zagnieżdżone środowisko wirtualizacji w Microsoft Azure, aby można było zainstalować dysk uszkodzonej maszyny wirtualnej na hoście funkcji Hyper-V (maszyna ratownicza) na potrzeby rozwiązywania problemów.

## <a name="prerequisites"></a>Wymagania wstępne

Aby można było zainstalować wadliwą maszynę wirtualną, maszyna ratownicza musi używać tego samego typu konta magazynu (warstwy Standardowa lub Premium) jako uszkodzonej maszyny wirtualnej.

## <a name="step-1-create-a-rescue-vm-and-install-hyper-v-role"></a>Krok 1. Tworzenie ratowniczej maszyny wirtualnej i Instalowanie roli funkcji Hyper-V

1.  Utwórz nową ratowniczą maszynę wirtualną:

    -  System operacyjny: Windows Server 2016 Datacenter

    -  Rozmiar: Każda seria v3 z co najmniej dwoma rdzeniami obsługującymi wirtualizację zagnieżdżoną. Aby uzyskać więcej informacji, zobacz [wprowadzenie nowych rozmiarów maszyn wirtualnych Dv3 i EV3](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/).

    -  Ta sama lokalizacja, konto magazynu i Grupa zasobów działa jako uszkodzona maszyna wirtualna.

    -  Wybierz ten sam typ magazynu co wadliwa maszyna wirtualna (standardowa lub Premium).

2.  Po utworzeniu łodziowej maszyny wirtualnej do ratowniczej maszyny wirtualnej.

3.  W Menedżer serwera wybierz pozycję **Zarządzaj**  >  **Dodaj role i funkcje**.

4.  W sekcji **Typ instalacji** wybierz pozycję **Instalacja oparta na rolach lub oparta na funkcjach**.

5.  Upewnij się, że w sekcji **Wybierz serwer docelowy** została wybrana maszyna wirtualna.

6.  Wybierz **rolę funkcji Hyper-V**  >  **Dodaj funkcje**.

7.  W sekcji **funkcje** wybierz pozycję **dalej** .

8.  Jeśli przełącznik wirtualny jest dostępny, zaznacz go. W przeciwnym razie wybierz pozycję **dalej**.

9.  W sekcji **migracja** wybierz pozycję **dalej** .

10. W sekcji **magazyny domyślne** wybierz pozycję **dalej**.

11. Zaznacz pole wyboru w celu automatycznego ponownego uruchomienia serwera, jeśli jest to wymagane.

12. Wybierz pozycję **Zainstaluj**.

13. Zezwól serwerowi na zainstalowanie roli funkcji Hyper-V. Trwa to kilka minut, a serwer zostanie automatycznie uruchomiony ponownie.

## <a name="step-2-create-the-faulty-vm-on-the-rescue-vms-hyper-v-server"></a>Krok 2. utworzenie uszkodzonej maszyny wirtualnej na serwerze funkcji Hyper-V z systemem ratowniczej maszyny wirtualnej

1.  [Utwórz dysk migawki](troubleshoot-recovery-disks-portal-windows.md#take-a-snapshot-of-the-os-disk) dla dysku systemu operacyjnego maszyny wirtualnej, który ma problem, a następnie Dołącz dysk migawki do maszyny wirtualnej recuse.

2.  Pulpit zdalny na ratowniczą maszynę wirtualną.

3.  Otwórz przystawkę Zarządzanie dyskami (diskmgmt. msc). Upewnij się, że dysk uszkodzonej maszyny wirtualnej jest ustawiony na **tryb offline**.

4.  Otwórz Menedżera funkcji Hyper-V: w **Menedżer serwera**wybierz **rolę funkcji Hyper-v**. Kliknij prawym przyciskiem myszy serwer, a następnie wybierz **Menedżera funkcji Hyper-V**.

5.  W Menedżerze funkcji Hyper-V kliknij prawym przyciskiem myszy ratowniczą maszynę wirtualną, a następnie wybierz pozycję **Nowa**  >  **maszyna wirtualna**  >  **dalej**.

6.  Wpisz nazwę maszyny wirtualnej, a następnie wybierz przycisk **dalej**.

7.  Wybierz pozycję **generacja 1**.

8.  Ustaw pamięć początkową o 1024 MB lub więcej.

9. W razie potrzeby wybierz przełącznik sieci funkcji Hyper-V, który został utworzony. W przeciwnym razie przejdź do następnej strony.

10. Wybierz pozycję **Dołącz wirtualny dysk twardy później**.

    ![obraz przedstawiający możliwość późniejszego dołączenia wirtualnego dysku twardego](media/troubleshoot-vm-by-use-nested-virtualization/attach-disk-later.png)

11. Po utworzeniu maszyny wirtualnej wybierz pozycję **Zakończ** .

12. Kliknij prawym przyciskiem myszy utworzoną maszynę wirtualną, a następnie wybierz pozycję **Ustawienia**.

13. Wybierz pozycję **kontroler IDE 0**, wybierz pozycję **dysk twardy**, a następnie kliknij przycisk **Dodaj**.

    ![obraz przedstawiający Dodawanie nowego dysku twardego](media/troubleshoot-vm-by-use-nested-virtualization/create-new-drive.png)    

14. Na stronie **fizyczny dysk twardy**wybierz dysk USZKODZONEJ maszyny wirtualnej, która została podłączona do maszyny wirtualnej platformy Azure. Jeśli nie widzisz żadnych dysków na liście, sprawdź, czy dysk jest ustawiony do trybu offline przy użyciu przystawki Zarządzanie dyskami.

    ![obraz dotyczący instalowania dysku](media/troubleshoot-vm-by-use-nested-virtualization/mount-disk.png)  


15. Wybierz pozycję **Apply** (Zastosuj), a następnie wybierz przycisk **OK**.

16. Kliknij dwukrotnie maszynę wirtualną, a następnie ją Uruchom.

17. Teraz możesz współpracować z maszyną wirtualną jako lokalną maszynę wirtualną. Można wykonać dowolne kroki rozwiązywania problemów.

## <a name="step-3-replace-the-os-disk-used-by-the-faulty-vm"></a>Krok 3. zastępowanie dysku systemu operacyjnego używanego przez wadliwą maszynę wirtualną

1.  Po przejściu maszyny wirtualnej w tryb online Zamknij maszynę wirtualną w Menedżerze funkcji Hyper-V.

2.  [Odinstaluj i Odłącz dysk systemu operacyjnego](troubleshoot-recovery-disks-portal-windows.md#unmount-and-detach-original-virtual-hard-disk
).
3.  [Zastąp dysk systemu operacyjnego używany przez maszynę wirtualną za pomocą naprawionego dysku systemu operacyjnego](troubleshoot-recovery-disks-portal-windows.md#swap-the-os-disk-for-the-vm
).

## <a name="next-steps"></a>Następne kroki

Jeśli masz problemy z nawiązywaniem połączenia z maszyną wirtualną, zobacz [Rozwiązywanie problemów z połączeniami RDP z maszyną wirtualną platformy Azure](troubleshoot-rdp-connection.md). Problemy dotyczące uzyskiwania dostępu do aplikacji uruchomionych na maszynie wirtualnej można znaleźć [w temacie Rozwiązywanie problemów z łącznością aplikacji na maszynie wirtualnej z systemem Windows](troubleshoot-app-connection.md).
