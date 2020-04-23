---
title: Wdrażanie dedykowanych hostów platformy Azure przy użyciu portalu
description: Wdrażanie maszyn wirtualnych do dedykowanych hostów za pomocą portalu.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 53a9cd33f7bd2223be1c9f199b7a6d10d876c322
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082938"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>Wdrażanie maszyn wirtualnych na dedykowanych hostach za pomocą portalu

W tym artykule opisano, jak utworzyć [dedykowany host](dedicated-hosts.md) platformy Azure do obsługi maszyn wirtualnych (maszyn wirtualnych). 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

1. W lewym górnym rogu okna witryny Azure Portal wybierz pozycję **Utwórz zasób**.
1. Na stronie **Nowy** w obszarze **Popularne**wybierz pozycję **Centrum danych systemu Windows Server 2016**.
1. Na karcie **Podstawy** w obszarze **Szczegóły projektu**upewnij się, że jest wybrana poprawna subskrypcja, a następnie wybierz *myDedicatedHostsRG* jako **grupę Zasobów**. 
1. W obszarze **Szczegóły wystąpienia** wpisz *myVM* w polu **Nazwa maszyny wirtualnej** i wybierz *Wschodnie stany USA* w polu **Lokalizacja **.
1. W **obszarze Opcje dostępności** wybierz **strefę Dostępność**, wybierz *1* z listy rozwijanej.
1. Dla rozmiaru wybierz pozycję **Zmień rozmiar**. Na liście dostępnych rozmiarów wybierz jeden z serii Esv3, takich jak **Standard E2s v3**. Może być konieczne wyczyszczenie filtru, aby wyświetlić wszystkie dostępne rozmiary.
1. W obszarze **Konto administratora** podaj nazwę użytkownika, taką jak *azureuser*, oraz hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](faq.md#what-are-the-password-requirements-when-creating-a-vm).
1. W obszarze **Reguły portów przychodzących**wybierz pozycję **Zezwalaj na wybrane porty,** a następnie wybierz z listy rozwijanej pozycję **RDP (3389).**
1. U góry strony wybierz kartę **Zaawansowane,** a następnie w sekcji **Host** wybierz **grupę** *myHostGroup* for Host i *myHost* dla **hosta**. 
    ![Wybieranie grupy hostów i hosta](./media/dedicated-hosts-portal/advanced.png)
1. Pozostaw pozostałe wartości domyślne, a następnie wybierz przycisk **Przejrzyj + utwórz** znajdujący się u dołu strony.
1. Po wyświetleniu komunikatu, że weryfikacja została przekazana, wybierz pozycję **Utwórz**.

## <a name="add-an-existing-vm"></a>Dodawanie istniejącej maszyny wirtualnej 

Do dedykowanego hosta można dodać istniejącą maszynę wirtualną, ale najpierw maszyna wirtualna musi być zatrzymana\przydziałem. Przed przeniesieniem maszyny Wirtualnej na dedykowany host upewnij się, że konfiguracja maszyny Wirtualnej jest obsługiwana:

- Rozmiar maszyny Wirtualnej musi znajdować się w tej samej rodzinie rozmiarów co dedykowany host. Na przykład jeśli dedykowany host jest DSv3, rozmiar maszyny Wirtualnej może być Standard_D4s_v3, ale nie może być Standard_A4_v2. 
- Maszyna wirtualna musi znajdować się w tym samym regionie co dedykowany host.
- Maszyna wirtualna nie może być częścią grupy miejsc docelowych zbliżeniowych. Usuń maszynę wirtualną z grupy miejsc docelowych zbliżeniowych przed przeniesieniem jej do dedykowanego hosta. Aby uzyskać więcej informacji, zobacz [Przenoszenie maszyny Wirtualnej z grupy miejsc docelowych zbliżeniowych](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group)
- Maszyna wirtualna nie może być w zestawie dostępności.
- Jeśli maszyna wirtualna znajduje się w strefie dostępności, musi być tą samą strefą dostępności co grupa hosta. Ustawienia strefy dostępności dla maszyny Wirtualnej i grupy hostów muszą być zgodne.

Przenieś maszynę wirtualną na dedykowany host za pomocą [portalu](https://portal.azure.com).

1. Otwórz stronę maszyny Wirtualnej.
1. Wybierz **pozycję Zatrzymaj,** aby zatrzymać\zdeterminowaćlokalizuj maszynę wirtualną.
1. Z lewego menu **wybierz polecenie Konfiguracja.**
1. Wybierz grupę hostów i hosta z menu rozwijanych.
1. Po zakończeniu wybierz pozycję **Zapisz** u góry strony.
1. Po dodaniu maszyny Wirtualnej do hosta wybierz **opcję Przegląd** z lewego menu.
1. U góry strony wybierz pozycję **Start,** aby ponownie uruchomić maszynę wirtualną.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, zobacz [przegląd dedykowanych hostów.](dedicated-hosts.md) 

- Istnieje przykładowy szablon, znaleziono [tutaj,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)który używa zarówno stref i domen błędów dla maksymalnej odporności w regionie.

- Można również wdrożyć dedykowanego hosta przy użyciu [programu Azure PowerShell](dedicated-hosts-powershell.md).
