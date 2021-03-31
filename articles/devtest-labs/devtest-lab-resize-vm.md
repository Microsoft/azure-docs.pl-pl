---
title: Zmiana rozmiaru maszyny wirtualnej w laboratorium w Azure DevTest Labs
description: Dowiedz się, jak zmienić rozmiar maszyny wirtualnej (VM) w Azure DevTest Labs na podstawie potrzeb dotyczących wydajności procesora, sieci lub dysku.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 592be0862bc33ead86fe43d37753fb72fde120c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85482024"
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


