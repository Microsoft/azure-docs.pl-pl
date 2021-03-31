---
title: Konfigurowanie ustawień Autostart dla maszyny wirtualnej w Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, jak skonfigurować ustawienia Autostart dla maszyn wirtualnych w środowisku laboratoryjnym. To ustawienie umożliwia automatyczne uruchamianie maszyn wirtualnych w środowisku laboratoryjnym zgodnie z harmonogramem.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 83e7b0836273a59eaaf66471bd0cb42d63ccf1c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91328484"
---
# <a name="auto-startup-lab-virtual-machines"></a>Automatycznie uruchamiaj maszyny wirtualne w laboratorium  
Azure DevTest Labs pozwala skonfigurować maszyny wirtualne w laboratorium do automatycznego uruchamiania i zamykania na podstawie harmonogramu. Aby uzyskać informacje na temat konfigurowania ustawień automatycznego zamykania, zobacz [Zarządzanie zasadami Autozamykania dla laboratorium w Azure DevTest Labs](devtest-lab-auto-shutdown.md). 

W przeciwieństwie do automatycznego zamykania, gdzie wszystkie maszyny wirtualne są uwzględniane po włączeniu zasad, zasady autostartu wymagają, aby użytkownik laboratorium jawnie wybierał maszynę wirtualną i dostosowała się do tego harmonogramu. Dzięki temu nie będziesz w łatwy sposób uruchamiać się w sytuacji, w której niepożądane maszyny wirtualne są przypadkowo uruchamiane i powodują nieoczekiwane wydatki.

W tym artykule opisano sposób konfigurowania zasad autostartu dla laboratorium.

## <a name="configure-autostart-settings-for-a-lab"></a>Konfigurowanie ustawień Autostart dla laboratorium 
1. Przejdź do strony głównej dla laboratorium. 
2. W menu po lewej stronie wybierz pozycję **Konfiguracja i zasady** . 

    ![Zrzut ekranu pokazujący menu "Konfiguracja i zasady" w laboratorium DevTest.](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. Na stronie **Konfiguracja i zasady** wykonaj następujące czynności:
    
    1. Wybierz opcję **włączone** , aby **zezwolić maszynom wirtualnym na planowanie automatycznego uruchamiania** , aby włączyć funkcję Autostart dla tego laboratorium. 
    2. Wybierz godzinę rozpoczęcia (na przykład 8:00:00 AM) dla pola **rozpoczęcie harmonogramu** . 
    3. Wybierz **strefę czasową** do użycia. 
    4. Wybierz **dni tygodnia** , w których maszyny wirtualne muszą być uruchamiane automatycznie. 
    5. Następnie wybierz pozycję **Zapisz** na pasku narzędzi, aby zapisać ustawienia. 

        ![Ustawienia autostartu](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > Te zasady nie automatycznie stosują automatycznego uruchamiania do wszystkich maszyn wirtualnych w laboratorium. Aby **wybrać** poszczególne maszyny wirtualne, przejdź do strony maszyny wirtualnej i Włącz **funkcję Autostart** dla tej maszyny wirtualnej.

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>Włączanie autostartu dla maszyny wirtualnej w laboratorium
Poniższa procedura zawiera kroki umożliwiające rezygnację z maszyny wirtualnej do zasad Autostart laboratorium. 

1. Na stronie głównej laboratorium wybierz **maszynę wirtualną** z listy **moje maszyny wirtualne** . 

    ![Menu Konfiguracja i zasady](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. Na stronie **maszyna wirtualna** wybierz pozycję **Autostart** w menu po lewej stronie lub na liście **harmonogramów** . 

    ![Wybierz menu Autostart](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. Na stronie **Autostart** wybierz opcję **włączone** dla opcji **Zezwalaj na planowanie automatycznego uruchamiania dla tej maszyny wirtualnej** .

    ![Włącz Autostart dla maszyny wirtualnej](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. Następnie wybierz pozycję **Zapisz** na pasku narzędzi, aby zapisać ustawienie. 


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o zasadach automatycznego zamykania konfiguracji laboratorium, zobacz [Zarządzanie zasadami Autozamykania dla laboratorium w Azure DevTest Labs](devtest-lab-auto-shutdown.md)
