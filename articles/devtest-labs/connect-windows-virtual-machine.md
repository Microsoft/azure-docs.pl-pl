---
title: Łączenie się z maszynami wirtualnymi z systemem Windows w Azure DevTest Labs
description: Dowiedz się, jak nawiązać połączenie z maszyną wirtualną z systemem Windows w laboratorium (Azure DevTest Labs)
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: e1e786daa396548030976159d1b150caa4b24396
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86540721"
---
# <a name="connect-to-a-windows-vm-in-your-lab-azure-devtest-labs"></a>Nawiązywanie połączenia z maszyną wirtualną z systemem Windows w laboratorium (Azure DevTest Labs)
W tym artykule pokazano, jak nawiązać połączenie z maszyną wirtualną z systemem Windows w laboratorium. 

## <a name="connect-to-a-windows-vm"></a>Nawiązywanie połączenia z maszyną wirtualną z systemem Windows
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku wyszukiwania Wyszukaj i wybierz pozycję **DevTest Labs**. 

    :::image type="content" source="./media/connect-windows-virtual-machine/search-select.png" alt-text="Wyszukaj i wybierz pozycję DevTest Labs":::    
1. Z listy laboratoriów wybierz **laboratorium**.

    :::image type="content" source="./media/connect-windows-virtual-machine/select-lab.png" alt-text="Wyszukaj i wybierz pozycję DevTest Labs":::            
1. Na stronie głównej laboratorium wybierz maszynę wirtualną z systemem Windows z listy **moje maszyny wirtualne** . 

    :::image type="content" source="./media/connect-windows-virtual-machine/select-windows-vm.png" alt-text="Wyszukaj i wybierz pozycję DevTest Labs":::                
1. Na stronie **maszyna wirtualna** dla maszyny wirtualnej wybierz pozycję **Połącz** na pasku narzędzi. Jeśli maszyna wirtualna jest zatrzymana, wybierz pozycję **Rozpocznij** pierwszy, aby uruchomić maszynę wirtualną.

    :::image type="content" source="./media/connect-windows-virtual-machine/select-connect.png" alt-text="Wyszukaj i wybierz pozycję DevTest Labs":::                    
1. Jeśli używasz przeglądarki Edge, zobaczysz link do **pobranego pliku RDP** w dolnej części przeglądarki. 

    :::image type="content" source="./media/connect-windows-virtual-machine/rdp-download.png" alt-text="Wyszukaj i wybierz pozycję DevTest Labs":::                        
1. Otwórz plik RDP, a następnie wprowadź poświadczenia maszyny wirtualnej, które zostały wpisane podczas tworzenia maszyny wirtualnej. Należy teraz połączyć się z maszyną wirtualną z systemem Windows. 

## <a name="next-steps"></a>Następne kroki
[Instrukcje: Nawiązywanie połączenia z maszyną wirtualną z systemem Linux](connect-linux-virtual-machine.md)
