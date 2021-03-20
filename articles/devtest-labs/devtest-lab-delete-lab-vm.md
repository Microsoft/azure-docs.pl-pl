---
title: Usuwanie laboratorium lub maszyny wirtualnej w laboratorium w Azure DevTest Labs
description: W tym artykule opisano sposób usuwania laboratorium lub usuwania maszyny wirtualnej w laboratorium przy użyciu Azure Portal (Azure DevTest Labs).
ms.topic: article
ms.date: 01/24/2020
ms.openlocfilehash: 495fb98f3da41a47d316dd64554ba616ede0af47
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85481225"
---
# <a name="delete-a-lab-or-vm-in-a-lab-in-azure-devtest-labs"></a>Usuwanie laboratorium lub maszyny wirtualnej w laboratorium w Azure DevTest Labs
W tym artykule opisano sposób usuwania laboratorium lub maszyny wirtualnej w laboratorium.

## <a name="delete-a-lab"></a>Usuwanie laboratorium
Po usunięciu wystąpienia usługi DevTest Labs z grupy zasobów usługa DevTest Labs wykonuje następujące czynności: 

- Wszystkie zasoby, które zostały utworzone automatycznie podczas tworzenia laboratorium, są automatycznie usuwane. Sama Grupa zasobów nie jest usuwana. Jeśli wszystkie zasoby zostały utworzone ręcznie przez tę grupę zasobów, usługa nie usunie ich. 
- Wszystkie maszyny wirtualne w laboratorium i w grupach zasobów skojarzonych z tymi maszynami wirtualnymi zostaną automatycznie usunięte. Podczas tworzenia maszyny wirtualnej w laboratorium Usługa tworzy zasoby (dysk, interfejs sieciowy, publiczny adres IP itp.) dla maszyny wirtualnej w oddzielnej grupie zasobów. Jeśli jednak ręcznie utworzysz wszystkie dodatkowe zasoby w tych grupach zasobów, usługa DevTest Labs nie usunie tych zasobów i grupy zasobów. 

Aby usunąć laboratorium, wykonaj następujące czynności: 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **wszystkie zasoby** z menu po lewej stronie, wybierz pozycję **DevTest Labs** dla typu usługi i wybierz laboratorium.

    ![Wybierz laboratorium](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Na stronie **Lab DevTest** kliknij pozycję **Usuń** na pasku narzędzi. 

    ![Przycisk Usuń](media/devtest-lab-delete-lab-vm/delete-button.png)
4. Na stronie **potwierdzenie** wprowadź **nazwę** laboratorium i wybierz pozycję **Usuń**. 

    ![Confirm](media/devtest-lab-delete-lab-vm/confirm-delete.png)
5. Aby wyświetlić stan operacji, wybierz pozycję **powiadomienia** ikona (dzwonek). 

    ![Powiadomienia](media/devtest-lab-delete-lab-vm/delete-status.png)

 
## <a name="delete-a-vm-in-a-lab"></a>Usuwanie maszyny wirtualnej w laboratorium
Jeśli usuniesz maszynę wirtualną w laboratorium, niektóre zasoby (nie wszystkie), które zostały utworzone podczas tworzenia laboratorium, są usuwane. Następujące zasoby nie zostały usunięte: 

-   Magazyn kluczy w głównej grupie zasobów
-   Zestaw dostępności, moduł równoważenia obciążenia, publiczny adres IP w grupie zasobów maszyny wirtualnej. Te zasoby są współużytkowane przez wiele maszyn wirtualnych w grupie zasobów. 

Maszyny wirtualne, interfejs sieciowy i dysk skojarzony z maszyną wirtualną są usuwane. 

Aby usunąć maszynę wirtualną w laboratorium, wykonaj następujące czynności: 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **wszystkie zasoby** z menu po lewej stronie, wybierz pozycję **DevTest Labs** dla typu usługi i wybierz laboratorium.

    ![Wybierz laboratorium](media/devtest-lab-delete-lab-vm/select-lab.png)
3. Wybierz **... (wielokropek)** dla maszyny wirtualnej na liście maszyn wirtualnych, a następnie wybierz pozycję **Usuń**. 

    ![Usuń maszynę wirtualną w menu](media/devtest-lab-delete-lab-vm/delete-vm-menu-in-list.png)
4. W oknie dialogowym **potwierdzenia** wybierz **przycisk OK**. 
5. Aby wyświetlić stan operacji, wybierz pozycję **powiadomienia** ikona (dzwonek). 

Aby usunąć MASZYNę wirtualną ze **strony maszyny wirtualnej**, wybierz pozycję **Usuń** z paska narzędzi, jak pokazano na poniższej ilustracji:

![Usuń maszynę wirtualną ze strony maszyny wirtualnej](media/devtest-lab-delete-lab-vm/delete-from-vm-page.png) 


## <a name="next-steps"></a>Następne kroki
Jeśli chcesz utworzyć laboratorium, zobacz następujące artykuły: 

- [Tworzenie laboratorium](devtest-lab-create-lab.md)
- [Dodawanie maszyny wirtualnej do laboratorium](devtest-lab-add-vm.md)