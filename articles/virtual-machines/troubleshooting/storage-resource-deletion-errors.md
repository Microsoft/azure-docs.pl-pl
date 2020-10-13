---
title: Rozwiązywanie problemów z błędami usuwania zasobów magazynu na maszynach wirtualnych systemu Linux na platformie Azure | Microsoft Docs
description: Dowiedz się, jak rozwiązywać problemy z maszynami wirtualnymi z systemem Linux podczas usuwania zasobów magazynu zawierających dołączone wirtualne dyski twarde.
keywords: ''
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 8d727bc8bdc8f015504baa57f9596b3bacac9712
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91651637"
---
# <a name="troubleshoot-storage-resource-deletion-errors"></a>Rozwiązywanie problemów z błędami usuwania zasobów magazynu

W niektórych scenariuszach może wystąpić jeden z następujących błędów podczas próby usunięcia konta usługi Azure Storage, kontenera lub obiektu BLOB w ramach wdrożenia Azure Resource Manager:

> **Nie można usunąć konta magazynu "StorageAccountName". Błąd: nie można usunąć konta magazynu, ponieważ jego artefakty są w użyciu.**
> 
> **Nie można usunąć z # kontenerów: <br> VHD: obecnie istnieje dzierżawa w kontenerze i w żądaniu nie określono identyfikatora dzierżawy.**
> 
> **Nie można usunąć # z # BLOB: <br> blobname. VHD: obecnie istnieje dzierżawa obiektu BLOB, a w żądaniu nie określono identyfikatora dzierżawy.**

Wirtualne dyski twarde używane na maszynach wirtualnych platformy Azure to pliki VHD przechowywane jako stronicowe obiekty blob na koncie magazynu w warstwie Standardowa lub Premium na platformie Azure. Aby uzyskać więcej informacji na temat dysków platformy Azure, zobacz [wprowadzenie do usługi Managed disks](../managed-disks-overview.md).

Platforma Azure uniemożliwia usunięcie dysku dołączonego do maszyny wirtualnej w celu zapobieżenia uszkodzeniu. Zapobiega to również usunięciu kontenerów i kont magazynu, które mają stronicowy obiekt BLOB dołączony do maszyny wirtualnej. 

Proces usuwania konta magazynu, kontenera lub obiektu BLOB w przypadku otrzymania jednego z tych błędów jest następujący: 
1. Zidentyfikuj obiekty blob dołączone do maszyny wirtualnej
2. [Usuwanie maszyn wirtualnych z dołączonym **dyskiem systemu operacyjnego**](#step-2-delete-vm-to-detach-os-disk)
3. [Odłącz wszystkie **dyski danych** z pozostałych maszyn wirtualnych](#step-3-detach-data-disk-from-the-vm)

Ponów próbę usunięcia konta magazynu, kontenera lub obiektu BLOB po zakończeniu tych kroków.

## <a name="step-1-identify-blob-attached-to-a-vm"></a>Krok 1. identyfikowanie obiektu BLOB dołączonego do maszyny wirtualnej

### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>Scenariusz 1. Usuwanie obiektu BLOB — identyfikowanie dołączonej maszyny wirtualnej
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu Centrum wybierz pozycję **Wszystkie zasoby**. Przejdź do konta magazynu, a następnie wybierz pozycję **kontenery**w obszarze **usługi BLOB** i przejdź do obiektu BLOB, który ma zostać usunięty.
3. Jeśli jest **dzierżawiony** **stan dzierżawy** obiektu BLOB, kliknij prawym przyciskiem myszy i wybierz polecenie **Edytuj metadane** , aby otworzyć okienko metadanych obiektu BLOB. 

    ![Zrzut ekranu portalu z obiektami BLOB konta magazynu i kliknij prawym przyciskiem myszy > wyróżnione "Edytuj metadane"](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. W okienku Metadane obiektu BLOB zaznacz i Zapisz wartość **MicrosoftAzureCompute_VMName**. Ta wartość to nazwa maszyny wirtualnej, do której jest dołączony dysk VHD. (Zobacz **Ważne** , jeśli to pole nie istnieje)
5. W okienku Metadane obiektu BLOB zaznacz i Zapisz wartość **MicrosoftAzureCompute_DiskType**. Ta wartość określa, czy dołączony dysk jest systemem operacyjnym, czy dyskiem danych (zobacz **Ważne** , jeśli to pole nie istnieje). 

     ![Zrzut ekranu portalu z otwartym okienkiem magazynu "metadane obiektów BLOB"](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. Jeśli typ dysku BLOB to **OSDisk** , postępuj zgodnie z [krok 2: Usuń maszynę wirtualną, aby odłączyć dysk systemu operacyjnego](#step-2-delete-vm-to-detach-os-disk). W przeciwnym razie, jeśli typ dysku BLOB to **dysk** danych, wykonaj kroki opisane w [sekcji Krok 3. Odłączanie dysku z danymi z maszyny wirtualnej](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> Jeśli **MicrosoftAzureCompute_VMName** i **MicrosoftAzureCompute_DiskType** nie są wyświetlane w metadanych obiektu BLOB, oznacza to, że obiekt BLOB jest jawnie dzierżawiony i nie jest podłączony do maszyny wirtualnej. Dzierżawcy obiektów BLOB nie można usunąć bez wcześniejszego podzielenia dzierżawy. Aby przerwać dzierżawę, kliknij prawym przyciskiem myszy obiekt BLOB i wybierz pozycję **Przerwij dzierżawę**. Dzierżawione obiekty blob, które nie są dołączone do maszyny wirtualnej, uniemożliwiają usunięcie obiektu BLOB, ale nie uniemożliwiają usuwania kontenera lub konta magazynu.

### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>Scenariusz 2. Usuwanie kontenera — Zidentyfikuj wszystkie obiekty blob w kontenerze, które są dołączone do maszyn wirtualnych
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu Centrum wybierz pozycję **Wszystkie zasoby**. Przejdź do konta magazynu, w obszarze **Usługa BLOB** wybierz **kontenery**i Znajdź kontener do usunięcia.
3. Kliknij, aby otworzyć kontener, a zostanie wyświetlona lista obiektów BLOB znajdujących się w nim. Zidentyfikuj wszystkie obiekty blob z typem obiektu BLOB = **Page BLOB** i State Lease = **wydzierżawione** z tej listy. Postępuj zgodnie z scenariuszem 1, aby zidentyfikować maszynę wirtualną skojarzoną z każdym z tych obiektów BLOB.

    ![Zrzut ekranu portalu z wyróżnionymi obiektami BLOB konta magazynu i "stanem dzierżawy" i "dzierżawa"](./media/troubleshoot-vhds/utd-disks-sm.png)

4. Wykonaj [kroki krok 2](#step-2-delete-vm-to-detach-os-disk) i [3](#step-3-detach-data-disk-from-the-vm) , aby usunąć maszyny wirtualne z **OSDisk** i odłączyć **dysk**danych. 

### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>Scenariusz 3. Usuwanie konta magazynu — identyfikowanie wszystkich obiektów BLOB w ramach konta magazynu, które są dołączone do maszyn wirtualnych
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu Centrum wybierz pozycję **Wszystkie zasoby**. Przejdź do konta magazynu, a następnie wybierz pozycję **obiekty**BLOB w obszarze **usługi BLOB** .
3. W okienku **kontenery** Zidentyfikuj wszystkie kontenery, w których jest **wydzierżawiony** **stan dzierżawy** , i postępuj zgodnie z [scenariuszem 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) dla każdego kontenera **dzierżawy** .
4. Wykonaj [kroki krok 2](#step-2-delete-vm-to-detach-os-disk) i [3](#step-3-detach-data-disk-from-the-vm) , aby usunąć maszyny wirtualne z **OSDisk** i odłączyć **dysk**danych. 

## <a name="step-2-delete-vm-to-detach-os-disk"></a>Krok 2. usunięcie maszyny wirtualnej w celu odłączenia dysku systemu operacyjnego
W przypadku dysku z systemem operacyjnym należy usunąć maszynę wirtualną przed usunięciem dołączonego wirtualnego dysku twardego. Po ukończeniu tych kroków nie będzie wymagana żadna dodatkowa akcja dla dysków danych podłączonych do tej samej maszyny wirtualnej:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu centrum wybierz pozycję **Virtual Machines**.
3. Wybierz maszynę wirtualną, z którą jest dołączony dysk VHD.
4. Upewnij się, że nic nie jest aktywnie używane, a maszyna wirtualna nie jest już potrzebna.
5. W górnej części okienka **Szczegóły maszyny wirtualnej** wybierz pozycję **Usuń**, a następnie kliknij przycisk **tak** , aby potwierdzić.
6. Należy usunąć maszynę wirtualną, ale można zachować dysk VHD. Jednak wirtualny dysk twardy nie powinien już być dołączony do maszyny wirtualnej ani dzierżawy. Zwolnienie dzierżawy może potrwać kilka minut. Aby sprawdzić, czy dzierżawa została wydana, przejdź do lokalizacji obiektu BLOB, a w okienku **właściwości obiektu BLOB** **stan dzierżawy** powinien być **dostępny**.

## <a name="step-3-detach-data-disk-from-the-vm"></a>Krok 3. Odłączanie dysku danych z maszyny wirtualnej
Jeśli wirtualny dysk twardy znajduje się na dysku z danymi, odłącz wirtualny dysk twardy od maszyny wirtualnej w celu usunięcia dzierżawy:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. W menu centrum wybierz pozycję **Virtual Machines**.
3. Wybierz maszynę wirtualną, z którą jest dołączony dysk VHD.
4. Wybierz pozycję **dyski** w okienku **Szczegóły maszyny wirtualnej** .
5. Wybierz dysk z danymi do usunięcia, do którego jest dołączony dysk VHD. Możesz określić, który obiekt BLOB jest dołączony do dysku, sprawdzając adres URL wirtualnego dysku twardego.
6. Lokalizację obiektu BLOB można zweryfikować, klikając dysk, aby sprawdzić ścieżkę w polu **Identyfikator URI dysku VHD** .
7. Wybierz pozycję **Edytuj** w górnej części okienka **dysków** .
8. Kliknij przycisk **Odłącz ikonę** dysku z danymi, który ma zostać usunięty.

     ![Zrzut ekranu przedstawiający Portal z otwartym okienkiem magazynu "metadane obiektów BLOB" i wyróżnioną ikoną odłączania dysku z danymi do usunięcia.](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. Wybierz pozycję **Zapisz**. Dysk jest teraz odłączony od maszyny wirtualnej, a wirtualny dysk twardy nie jest już dzierżawiony. Zwolnienie dzierżawy może potrwać kilka minut. Aby sprawdzić, czy dzierżawa została wydana, przejdź do lokalizacji obiektu BLOB i w okienku **właściwości obiektu BLOB** , wartość **stanu dzierżawy** powinna być **odblokowana** lub **dostępna**.

[Storage deletion errors in Resource Manager deployment]: #storage-delete-errors-in-rm

