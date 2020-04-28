---
title: Zmiana rozmiaru maszyny wirtualnej w laboratorium w Azure DevTest Labs
description: Dowiedz się, jak zmienić rozmiar maszyny wirtualnej (VM) w Azure DevTest Labs na podstawie potrzeb dotyczących wydajności procesora, sieci lub dysku.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: bf7c425766a97aaa1d143133f04502a0aa3c36cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76756181"
---
# <a name="resize-a-vm-in-a-lab-in-azure-devtest-labs"></a>Zmiana rozmiaru maszyny wirtualnej w laboratorium w Azure DevTest Labs
Jedną z ważnych funkcji usługi Azure Virtual Machines jest możliwość zmiany rozmiaru maszyny wirtualnej (VM) w zależności od potrzeb dotyczących wydajności procesora CPU, sieci lub dysku. Azure DevTest Labs teraz obsługuje tę funkcję dla maszyn wirtualnych w laboratorium. Funkcja zmiany rozmiaru jest zgodna z zasadami laboratorium dla dozwolonych rozmiarów maszyn wirtualnych w laboratorium. Oznacza to, że można zmienić rozmiar maszyny wirtualnej tylko na Dozwolone rozmiary w laboratorium. 


## <a name="steps-to-resize-a-vm-in-a-lab"></a>Procedura zmiany rozmiaru maszyny wirtualnej w laboratorium 
Aby zmienić rozmiar maszyny wirtualnej w laboratorium w Azure DevTest Labs, wykonaj następujące czynności: 

> [!NOTE]
> Jeśli masz połączenie z maszyną wirtualną za pośrednictwem sesji pulpitu zdalnego, Zapisz swoją służbę i odłącz ją od maszyny wirtualnej przed zmianą jej rozmiarów.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **DevTest Labs** .
3. Z listy laboratoriów wybierz laboratorium obejmujące maszynę wirtualną, której rozmiar chcesz zmienić.  
4. W lewym panelu wybierz pozycję **My Virtual Machines**. 
5. Z listy maszyn wirtualnych wybierz maszynę wirtualną.
6. Jeśli maszyna wirtualna jest uruchomiona, wybierz pozycję **Zatrzymaj** na pasku narzędzi. Sprawdź stan operacji w oknie **powiadomienia** . Zaczekaj, aż maszyna wirtualna zostanie zatrzymana, a następnie zamknij okno **powiadomienia** . 

    ![Zatrzymywanie maszyny wirtualnej](media/devtest-lab-resize-vm/stop-vm.png)
1. Na stronie maszyna wirtualna dla maszyny wirtualnej wybierz pozycję **rozmiar** w obszarze **Ustawienia** w menu po lewej stronie.

    ![Menu Rozmiar](media/devtest-lab-resize-vm/size-menu.png)
1. W oknie **Wybieranie rozmiaru** Przeglądaj i wybierz rozmiar maszyny wirtualnej, a następnie kliknij przycisk **Wybierz**.     
1. Sprawdź stan operacji zmiany rozmiaru w oknie **powiadomienia** .

    ![Zmień rozmiar stanu](media/devtest-lab-resize-vm/resize-status.png)
10. Po pomyślnym zakończeniu operacji zmiany rozmiaru Zamknij okno **powiadomienia** . 
11. Wybierz pozycję **Przegląd** w menu po lewej stronie, a następnie wybierz pozycję **Uruchom ponownie** na pasku narzędzi, aby ponownie uruchomić maszynę wirtualną. 

## <a name="next-steps"></a>Następne kroki
Aby uzyskać szczegółowe informacje na temat funkcji zmiany rozmiaru obsługiwanej przez usługi Azure Virtual Machines, zobacz [zmiana rozmiaru maszyn wirtualnych](https://azure.microsoft.com/blog/resize-virtual-machines/).


