---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 1c3996c3f40da496af0cd795d0873864667a1f19
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183236"
---
## <a name="use-the-azure-portal"></a>Korzystanie z witryny Azure Portal
1. Wybierz maszynę wirtualną, którą chcesz ponownie wdrożyć, a następnie wybierz przycisk *Wdróż* ponownie w bloku *Ustawienia* . Może być konieczne przewinięcie w dół, aby wyświetlić sekcję **Obsługa i rozwiązywanie problemów** , która zawiera przycisk "redeploy", jak w poniższym przykładzie:
   
    ![Blok maszyny wirtualnej platformy Azure](./media/virtual-machines-common-redeploy-to-new-node/vmoverview.png)
2. Aby potwierdzić operację, wybierz przycisk *Wdróż* ponownie:
   
    ![Ponowne wdrażanie bloku maszyny wirtualnej](./media/virtual-machines-common-redeploy-to-new-node/redeployvm.png)
3. **Stan** maszyny wirtualnej zmieni się na *zaktualizowanie* , ponieważ maszyna wirtualna przygotowuje się do ponownego wdrożenia, jak pokazano w następującym przykładzie:
   
    ![Aktualizowanie maszyny wirtualnej](./media/virtual-machines-common-redeploy-to-new-node/vmupdating.png)
4. **Stan** zmieni się na *rozpoczynając* od uruchomienia maszyny wirtualnej na nowym hoście platformy Azure, jak pokazano w następującym przykładzie:
   
    ![Uruchamianie maszyny wirtualnej](./media/virtual-machines-common-redeploy-to-new-node/vmstarting.png)
5. Po zakończeniu procesu rozruchu przez maszynę wirtualną **stan** zostanie zwrócony do *uruchomionego*, co oznacza, że maszyna wirtualna została pomyślnie wdrożona ponownie:
   
    ![Uruchomiona maszyna wirtualna](./media/virtual-machines-common-redeploy-to-new-node/vmrunning.png)

