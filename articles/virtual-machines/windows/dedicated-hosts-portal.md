---
title: Wdrażanie dedykowanych hostów platformy Azure przy użyciu portalu
description: Wdrażaj maszyny wirtualne na dedykowanych hostach przy użyciu portalu.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 03/10/2020
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: a4ce9d360e226a9934c30b749ea0284f5ace9cf0
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284595"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>Wdrażanie maszyn wirtualnych na dedykowanych hostach przy użyciu portalu

W tym artykule opisano sposób tworzenia [dedykowanego hosta](dedicated-hosts.md) platformy Azure do hostowania maszyn wirtualnych. 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

1. W lewym górnym rogu okna witryny Azure Portal wybierz pozycję **Utwórz zasób**.
1. Na stronie **Nowy** w obszarze **popularne**wybierz pozycję **Windows Server 2016 Datacenter**.
1. Na karcie **podstawy** w obszarze **szczegóły projektu**upewnij się, że wybrano poprawną subskrypcję, a następnie wybierz pozycję *myDedicatedHostsRG* jako **grupę zasobów**. 
1. W obszarze **Szczegóły wystąpienia** wpisz *myVM* w polu **Nazwa maszyny wirtualnej** i wybierz *Wschodnie stany USA* w polu **Lokalizacja **.
1. W obszarze **Opcje dostępności** wybierz pozycję **strefa dostępności**, a następnie z listy rozwijanej wybierz pozycję *1* .
1. Dla rozmiaru wybierz pozycję **Zmień rozmiar**. Na liście dostępnych rozmiarów wybierz jedną z serii Esv3, na przykład **standardowa E2s v3**. Może być konieczne wyczyszczenie filtru, aby wyświetlić wszystkie dostępne rozmiary.
1. W obszarze **Konto administratora** podaj nazwę użytkownika, taką jak *azureuser*, oraz hasło. Hasło musi mieć co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](faq.md#what-are-the-password-requirements-when-creating-a-vm).
1. W obszarze **reguły portów ruchu przychodzącego**wybierz opcję **Zezwalaj na wybrane porty** , a następnie wybierz pozycję **RDP (3389)** z listy rozwijanej.
1. W górnej części strony wybierz kartę **Zaawansowane** i w sekcji **host** wybierz pozycję Moja *host* dla **grupy hostów** *i hosta* dla **hosta**. 
    ![Wybierz grupę hostów i hosta](./media/dedicated-hosts-portal/advanced.png)
1. Pozostaw pozostałe wartości domyślne, a następnie wybierz przycisk **Przejrzyj + utwórz** znajdujący się u dołu strony.
1. Gdy zobaczysz komunikat, że Walidacja zakończyła się pomyślnie, wybierz pozycję **Utwórz**.

## <a name="add-an-existing-vm"></a>Dodawanie istniejącej maszyny wirtualnej 

Możesz dodać istniejącą maszynę wirtualną do dedykowanego hosta, ale najpierw należy Stop\Deallocated. maszynę wirtualną Przed przeniesieniem maszyny wirtualnej do dedykowanego hosta upewnij się, że konfiguracja maszyny wirtualnej jest obsługiwana:

- Rozmiar maszyny wirtualnej musi znajdować się w tej samej rodzinie rozmiarów co dedykowany host. Na przykład jeśli dedykowany host to DSv3, rozmiar maszyny wirtualnej może być Standard_D4s_v3, ale nie Standard_A4_v2. 
- Maszyna wirtualna musi znajdować się w tym samym regionie co dedykowany host.
- Maszyna wirtualna nie może być częścią grupy umieszczania sąsiedztwa. Usuń maszynę wirtualną z grupy położenia zbliżeniowe przed przeniesieniem jej do dedykowanego hosta. Aby uzyskać więcej informacji, zobacz [Przenoszenie maszyny wirtualnej z grupy umieszczania sąsiedztwa](./proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)
- Maszyna wirtualna nie może znajdować się w zestawie dostępności.
- Jeśli maszyna wirtualna znajduje się w strefie dostępności, musi to być ta sama strefa dostępności co grupa hostów. Ustawienia strefy dostępności dla maszyny wirtualnej i grupy hostów muszą być zgodne.

Przenieś maszynę wirtualną na dedykowanego hosta przy użyciu [portalu](https://portal.azure.com).

1. Otwórz stronę dla maszyny wirtualnej.
1. Wybierz pozycję **Zatrzymaj** , aby stop\deallocate maszynę wirtualną.
1. Z menu po lewej stronie wybierz pozycję **Konfiguracja** .
1. Wybierz grupę hostów i hosta z menu rozwijanego.
1. Gdy skończysz, wybierz pozycję **Zapisz** w górnej części strony.
1. Po dodaniu maszyny wirtualnej do hosta z menu po lewej stronie wybierz pozycję **Przegląd** .
1. W górnej części strony wybierz pozycję **Rozpocznij** , aby ponownie uruchomić maszynę wirtualną.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji, zobacz Omówienie [dedykowanych hostów](dedicated-hosts.md) . 

- Istnieje przykładowy szablon, który znajduje się w [tym miejscu](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), który używa stref i domen błędów w celu uzyskania maksymalnej odporności w regionie.

- Możesz również wdrożyć dedykowanego hosta przy użyciu [Azure PowerShell](dedicated-hosts-powershell.md).
