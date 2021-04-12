---
title: Kontrola konserwacji maszyn wirtualnych platformy Azure przy użyciu Azure Portal
description: Dowiedz się, jak kontrolować, kiedy konserwacja jest stosowana do maszyn wirtualnych platformy Azure przy użyciu kontroli konserwacji i Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance-control
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: 0dea30723a3ebe1598d6304ac3c98bfe1b55b466
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102563017"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-portal"></a>Kontrolowanie aktualizacji z kontrolą konserwacji i Azure Portal

Kontrola konserwacji pozwala określić, kiedy mają być stosowane aktualizacje odizolowanych maszyn wirtualnych i hostów dedykowanych platformy Azure. W tym temacie omówiono opcje Azure Portal sterowania konserwacją. Aby uzyskać więcej informacji na temat korzyści z używania kontroli konserwacji, jej ograniczeń i innych opcji zarządzania, zobacz [Zarządzanie aktualizacjami platformy przy użyciu kontroli konserwacji](maintenance-control.md).

## <a name="create-a-maintenance-configuration"></a>Utworzenie konfiguracji konserwacji

1. Zaloguj się w witrynie Azure Portal.

1. Wyszukaj **konfiguracje konserwacji**.

   ![Zrzut ekranu przedstawiający sposób otwierania konfiguracji konserwacji](media/virtual-machines-maintenance-control-portal/maintenance-configurations-search.png)

1. Kliknij pozycję **Dodaj**.

   ![Zrzut ekranu przedstawiający sposób dodawania konfiguracji konserwacji](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add.png)

1. Wybierz subskrypcję i grupę zasobów, podaj nazwę konfiguracji i wybierz region. Kliknij przycisk **Dalej**.

   ![Zrzut ekranu przedstawiający podstawowe informacje o konfiguracji konserwacji](media/virtual-machines-maintenance-control-portal/maintenance-configurations-basics.png)

1. Dodaj Tagi i wartości. Kliknij przycisk **Dalej**.

   ![Zrzut ekranu przedstawiający sposób dodawania tagów do konfiguracji konserwacji](media/virtual-machines-maintenance-control-portal/maintenance-configurations-tags.png)

1. Przejrzyj podsumowanie. Kliknij pozycję **Utwórz**.

   ![Zrzut ekranu przedstawiający sposób tworzenia konfiguracji konserwacji](media/virtual-machines-maintenance-control-portal/maintenance-configurations-create.png)

1. Po zakończeniu wdrażania kliknij pozycję **Przejdź do zasobu**.

   ![Zrzut ekranu przedstawiający ukończenie wdrożenia konfiguracji konserwacji](media/virtual-machines-maintenance-control-portal/maintenance-configurations-deployment-complete.png)

## <a name="assign-the-configuration"></a>Przypisz konfigurację

Na stronie Szczegóły konfiguracji konserwacji kliknij pozycję przypisania, a następnie kliknij pozycję **Przypisz zasób**. 

![Zrzut ekranu przedstawiający sposób przypisywania zasobu](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add-assignment.png)

Wybierz zasoby, do których ma zostać przypisana konfiguracja konserwacji, a następnie kliknij przycisk **OK**. Kolumna **Typ** wskazuje, czy zasób jest IZOLOWANą maszyną wirtualną czy dedykowanym hostem platformy Azure. Aby można było przypisać konfigurację, maszyna wirtualna musi być uruchomiona. Jeśli próbujesz przypisać konfigurację do zatrzymanej maszyny wirtualnej, wystąpi błąd. 

<!---Shantanu to add details about the error case--->

![Zrzut ekranu przedstawiający sposób wybierania zasobu](media/virtual-machines-maintenance-control-portal/maintenance-configurations-select-resource.png)

## <a name="check-configuration"></a>Sprawdź konfigurację

Można sprawdzić, czy konfiguracja została zastosowana prawidłowo, lub sprawdzić konfigurację konserwacji, która jest aktualnie przypisana przy użyciu **konfiguracji konserwacji**. Kolumna **Type** wskazuje, czy konfiguracja jest przypisana do izolowanej maszyny wirtualnej, czy dedykowanego hosta platformy Azure. 

![Zrzut ekranu przedstawiający sposób sprawdzania konfiguracji konserwacji](media/virtual-machines-maintenance-control-portal/maintenance-configurations-host-type.png)

Możesz również sprawdzić konfigurację określonej maszyny wirtualnej na jej stronie właściwości. Kliknij pozycję **konserwacja** , aby wyświetlić konfigurację przypisaną do tej maszyny wirtualnej.

![Zrzut ekranu przedstawiający sposób sprawdzania konserwacji dla hosta](media/virtual-machines-maintenance-control-portal/maintenance-configurations-check-config.png)

## <a name="check-for-pending-updates"></a>Wyszukaj oczekujące aktualizacje

Istnieją również dwa sposoby sprawdzenia, czy aktualizacje oczekują na konfigurację konserwacji. W obszarze **Konfiguracja konserwacji** na stronie Szczegóły konfiguracji kliknij pozycję **przypisania** i sprawdź **stan konserwacji**.

![Zrzut ekranu przedstawiający sposób sprawdzania oczekujących aktualizacji](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending.png)

Można także sprawdzić określony host przy użyciu **Virtual Machines** lub właściwości dedykowanego hosta. 

![Zrzut ekranu pokazujący wyróżniony stan konserwacji.](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending-vm.png)

## <a name="apply-updates"></a>Stosowanie aktualizacji

Oczekujące aktualizacje można zastosować na żądanie przy użyciu **Virtual Machines**. Na stronie Szczegóły maszyny wirtualnej kliknij pozycję **konserwacja** , a następnie kliknij pozycję **Zastosuj konserwację teraz**.

![Zrzut ekranu przedstawiający sposób stosowania oczekujących aktualizacji](media/virtual-machines-maintenance-control-portal/maintenance-configurations-apply-updates-now.png)

## <a name="check-the-status-of-applying-updates"></a>Sprawdź stan zastosowania aktualizacji 

Możesz sprawdzić postęp aktualizacji konfiguracji w konfiguracjach **konserwacji** lub przy użyciu **Virtual Machines**. Na stronie Szczegóły maszyny wirtualnej kliknij pozycję **konserwacja**. W poniższym przykładzie **stan konserwacji** wskazuje, że aktualizacja jest **oczekująca**.

![Zrzut ekranu przedstawiający sposób sprawdzania stanu oczekujących aktualizacji](media/virtual-machines-maintenance-control-portal/maintenance-configurations-status.png)

## <a name="delete-a-maintenance-configuration"></a>Usuń konfigurację konserwacji

Aby usunąć konfigurację, Otwórz szczegóły konfiguracji i kliknij przycisk **Usuń**.

![Zrzut ekranu pokazujący sposób usuwania konfiguracji.](media/virtual-machines-maintenance-control-portal/maintenance-configurations-delete.png)


## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zobacz temat [konserwacja i aktualizacje](maintenance-and-updates.md).
