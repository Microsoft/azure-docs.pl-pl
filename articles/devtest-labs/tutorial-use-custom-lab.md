---
title: Uzyskiwanie dostępu do laboratorium w usłudze Azure DevTest Labs | Microsoft Docs
description: W tym samouczku będziesz uzyskiwać dostęp do laboratorium tworzonego przy użyciu usługi Azure DevTest Labs, przejmować maszyny wirtualne, korzystać z nich, a następnie cofać ich przejęcie.
ms.topic: tutorial
ms.date: 06/26/2020
ms.author: spelluru
ms.openlocfilehash: 96d41b74b3c143feb64fc9c602de68182787c08b
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107379472"
---
# <a name="tutorial-access-a-lab-in-azure-devtest-labs"></a>Samouczek: uzyskiwanie dostępu do laboratorium w usłudze Azure DevTest Labs
W tym samouczku będziesz używać laboratorium, które zostało utworzone w temacie [Samouczek: tworzenie laboratorium w usłudze Azure DevTest Labs](tutorial-create-custom-lab.md).

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Przejmowanie maszyny wirtualnej w laboratorium
> * Łączenie z maszyną wirtualną
> * Cofanie przejęcia maszyny wirtualnej

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="access-the-lab"></a>Uzyskiwanie dostępu do laboratorium

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **Wszystkie zasoby** w menu po lewej stronie. 
3. Wybierz typ zasobu **DevTest Labs**. 
4. Wybierz laboratorium. 

    ![Wybieranie laboratorium](./media/tutorial-use-custom-lab/search-for-select-custom-lab.png)

## <a name="claim-a-vm"></a>Przejmowanie maszyny wirtualnej

1. Na liście **Maszyny wirtualne możliwe do przejęcia** wybierz pozycję **...** (wielokropek) i wybierz pozycję **Przejmij maszynę**.

    ![Przejmowanie maszyny wirtualnej](./media/tutorial-use-custom-lab/claim-virtual-machine.png)
1. Upewnij się, że maszyna wirtualna jest widoczna na liście **Moje maszyny wirtualne**.

    ![Moje maszyny wirtualne](./media/tutorial-use-custom-lab/my-virtual-machines.png)

## <a name="connect-to-the-vm"></a>Łączenie z maszyną wirtualną

1. Wybierz maszynę wirtualną z listy. Zostanie wyświetlona **strona maszyny wirtualnej** dla Twojej maszyny wirtualnej. Wybierz pozycję **Połącz** na pasku narzędzi.

    ![Nawiązywanie połączenia z maszyną wirtualną](./media/tutorial-use-custom-lab/connect-button.png)
2. Zapisz pobrany plik **RDP** na dysku twardym i użyj go do nawiązania połączenia z maszyną wirtualną. Podaj nazwę użytkownika i hasło zastosowane podczas tworzenia maszyny wirtualnej w poprzedniej sekcji. 

    Aby można było nawiązać połączenie z maszyną wirtualną z systemem Linux, musi być włączony dostęp do maszyny wirtualnej za pomocą protokołu SSH i/lub RDP. Kroki procedury nawiązywania połączenia z maszyną wirtualną z systemem Linux zawiera artykuł [Instalowanie i konfigurowanie pulpitu zdalnego w celu nawiązania połączenia z maszyna wirtualną z systemem Linux na platformie Azure](../virtual-machines/linux/use-remote-desktop.md). 

    > [!NOTE]
    > Istnieją inne sposoby, aby uzyskać dostęp do strony Maszyna wirtualna dla maszyny wirtualnej. Oto niektóre z nich: 
    > 
    > 1. Wyszukaj wszystkie maszyny wirtualne w subskrypcji. Wybierz maszynę wirtualną z listy maszyn wirtualnych, aby uzyskać dostęp do **strony Maszyna wirtualna.**
    > 2. Przejdź do **strony Grupa zasobów** dla grupy zasobów. Następnie wybierz maszynę wirtualną z listy zasobów w grupie zasobów, aby uzyskać dostęp do **strony Maszyna wirtualna.** 
    >
    > Nie używaj przycisku **Połącz** na pasku narzędzi na stronie **Maszyna** wirtualna, do których można uzyskać dostęp przy użyciu tych opcji. Zamiast tego przejdź do strony **Maszyna wirtualna** ze strony **usługi DevTest Labs,** jak pokazano w tym artykule, a następnie użyj **przycisku** Połącz na pasku narzędzi.


## <a name="unclaim-the-vm"></a>Cofanie przejęcia maszyny wirtualnej
Po zakończeniu korzystania z maszyny wirtualnej cofnij jej przejęcie, wykonując następujące czynności: 

1. Na stronie maszyny wirtualnej wybierz pozycję **Cofnij przejęcie** na pasku narzędzi. 

    ![Cofanie przejęcia maszyny wirtualnej](./media/tutorial-use-custom-lab/unclaim-vm-menu.png)
1. Maszyna wirtualna jest zamykana przed cofnięciem przejęcia. Stan tej operacji można zobaczyć w powiadomieniach.  
3. Przejdź z powrotem do strony laboratorium DevTest Lab, klikając nazwę laboratorium w menu linków do stron nadrzędnych, znajdującym się u góry. 
    
    ![Przechodzenie z powrotem do laboratorium](./media/tutorial-use-custom-lab/breadcrumb-to-lab.png)
1. Upewnij się, że maszyna wirtualna jest widoczna na liście **Maszyny wirtualne możliwe do przejęcia** w dolnej części.

    
## <a name="next-steps"></a>Następne kroki
W tym samouczku pokazano, jak uzyskiwać dostęp do laboratorium, które zostało utworzone za pomocą usługi Azure DevTest Labs, i korzystać z niego. Aby uzyskać więcej informacji na temat uzyskiwania dostępu do maszyn wirtualnych i używania ich w laboratorium, zobacz 

> [!div class="nextstepaction"]
> [Instrukcje: korzystanie z maszyn wirtualnych w laboratorium](devtest-lab-add-vm.md)

