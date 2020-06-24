---
title: Dołączanie lub odłączanie dysku danych do maszyny wirtualnej w Azure DevTest Labs
description: Dołączanie lub odłączanie dysku danych do maszyny wirtualnej w Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 9616bf38-7db8-4915-a32a-e4f40a7a56ad
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2020
ms.author: spelluru
ms.openlocfilehash: 3f18425408e6526904db85eae1c3a4db41d11a58
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84896585"
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>Dołączanie lub odłączanie dysku danych do maszyny wirtualnej w Azure DevTest Labs
[Usługa Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) zarządza kontami magazynu skojarzonymi z dyskami danych maszyny wirtualnej. Użytkownik dołącza nowy dysk danych do maszyny wirtualnej, określa wymagany typ i rozmiar dysku, a platforma Azure automatycznie tworzy dysk i zarządza nim. Dysk danych może zostać następnie odłączony od maszyny wirtualnej i ponownie dołączony do tej samej maszyny wirtualnej lub dołączony do innej maszyny wirtualnej, która należy do tego samego użytkownika.

Ta funkcja jest przydatna do zarządzania magazynem lub oprogramowaniem poza każdą indywidualną maszyną wirtualną. Jeśli magazyn lub oprogramowanie już istnieje na dysku z danymi, można je łatwo dołączać, odłączać i dołączać do dowolnej maszyny wirtualnej, która należy do użytkownika, który jest właścicielem tego dysku danych.

## <a name="attach-a-data-disk"></a>Dołączanie dysku danych
Przed dołączeniem dysku danych do maszyny wirtualnej zapoznaj się z następującymi wskazówkami:

- Rozmiar maszyny wirtualnej kontroluje liczbę dysków z danymi, które można dołączyć. Aby uzyskać szczegółowe informacje, zobacz [rozmiary maszyn wirtualnych](https://docs.microsoft.com/azure/virtual-machines/windows/sizes).
- Dysk danych można dołączyć tylko do maszyny wirtualnej, na której jest uruchomiony program. Przed podjęciem próby dołączenia dysku danych upewnij się, że maszyna wirtualna jest uruchomiona.

### <a name="attach-a-new-disk"></a>Dołącz nowy dysk
Wykonaj następujące kroki, aby utworzyć i dołączyć nowy dysk danych zarządzanych do maszyny wirtualnej w Azure DevTest Labs.

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **DevTest Labs** .
1. Z listy laboratoriów wybierz odpowiednie laboratorium. 
1. Z listy **moich maszyn wirtualnych**wybierz DZIAŁAJĄCą maszynę wirtualną.
1. Z menu po lewej stronie wybierz pozycję **dyski**.
1. Wybierz pozycję **Dołącz nowy** , aby utworzyć nowy dysk danych i dołączyć go do maszyny wirtualnej.

    ![Dołącz nowy dysk z danymi do maszyny wirtualnej](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. Wypełnij okienko **Dołącz nowy dysk** , wprowadzając dane w polu Nazwa dysku, typ i rozmiar.

    ![Ukończ formularz "Dołączanie nowego dysku"](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. Wybierz przycisk **OK**.

Po kilku chwilach nowy dysk danych zostanie utworzony i dołączony do maszyny wirtualnej i wyświetlony na liście **dysków danych** dla tej maszyny wirtualnej.

### <a name="attach-an-existing-disk"></a>Dołączanie istniejącego dysku
Wykonaj następujące kroki, aby ponownie dołączyć istniejący dostępny dysk danych do uruchomionej maszyny wirtualnej. 

1. Wybierz działającą maszynę wirtualną, dla której chcesz ponownie dołączyć dysk danych.
1. Z menu po lewej stronie wybierz pozycję **dyski**.
1. Wybierz pozycję **Dołącz istniejący** , aby dołączyć dostępny dysk z danymi do maszyny wirtualnej.

    ![Dołącz istniejący dysk z danymi do maszyny wirtualnej](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing-button.png)

1. W okienku **Dołącz istniejący dysk** wybierz przycisk OK.

    ![Dołącz istniejący dysk z danymi do maszyny wirtualnej](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

Po kilku chwilach dysk danych jest dołączany do maszyny wirtualnej i pojawia się na liście **dysków danych** dla tej maszyny wirtualnej.

## <a name="detach-a-data-disk"></a>Odłączanie dysku danych
Gdy nie potrzebujesz już dysku danych dołączonego do maszyny wirtualnej, możesz go łatwo odłączyć. Odłączenie usuwa dysk z maszyny wirtualnej, ale utrzymuje go w magazynie do późniejszego użycia.

Jeśli chcesz ponownie użyć istniejących danych na dysku, możesz dołączyć go do tej samej maszyny wirtualnej lub do innej.

### <a name="detach-from-the-vms-management-pane"></a>Odłączanie od okienka zarządzania maszyny wirtualnej
1. Z listy maszyn wirtualnych wybierz MASZYNę wirtualną z dołączonym dyskiem danych.
1. Z menu po lewej stronie wybierz pozycję **dyski**.
1. Z listy **dysków danych**wybierz dysk danych, który chcesz odłączyć.

    ![Wybierz dyski danych dla maszyny wirtualnej](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-button.png) 
1. Wybierz opcję **Odłącz** od góry okienka szczegółów dysku.

    ![Odłączanie dysku danych](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. Wybierz pozycję **tak** , aby potwierdzić, że chcesz odłączyć dysk z danymi.

Dysk jest odłączony i jest dostępny do dołączenia do innej maszyny wirtualnej. 
### <a name="detach-from-the-labs-main-pane"></a>Odłącz od głównego okienka laboratorium
1. W okienku głównym laboratorium wybierz pozycję **Moje dyski danych**.
1. Kliknij prawym przyciskiem myszy dysk danych, który chcesz odłączyć, lub wybierz jego wielokropek (**...**), a następnie wybierz polecenie **Odłącz**.

    ![Odłączanie dysku danych](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. Wybierz pozycję **tak** , aby potwierdzić, że chcesz ją odłączyć.

   > [!NOTE]
   > Jeśli dysk danych został już odłączony, można go usunąć z listy dostępnych dysków danych, wybierając pozycję **Usuń**.
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>Uaktualnianie niezarządzanego dysku danych
Jeśli masz istniejącą maszynę wirtualną, która używa niezarządzanych dysków danych, możesz łatwo skonwertować maszynę wirtualną w taki sposób, aby korzystała z dysków zarządzanych. Ten proces powoduje przekonwertowanie dysku systemu operacyjnego i dołączonych dysków danych.

Aby uaktualnić niezarządzany dysk danych, wykonaj kroki opisane w tym artykule, aby [odłączyć dysk danych](#detach-a-data-disk) z niezarządzanej maszyny wirtualnej. Następnie ponownie [Podłącz dysk](#attach-an-existing-disk) do ZARZĄDZANEJ maszyny wirtualnej, aby automatycznie uaktualnić dysk danych z niezarządzanego do zarządzanego.

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak zarządzać dyskami danych dla maszyn wirtualnych z możliwością [domagania](devtest-lab-add-claimable-vm.md#unclaim-a-vm).

