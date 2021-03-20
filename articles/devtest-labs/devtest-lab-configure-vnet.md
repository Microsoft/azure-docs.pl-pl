---
title: Konfigurowanie sieci wirtualnej w Azure DevTest Labs | Microsoft Docs
description: Dowiedz się, jak skonfigurować istniejącą sieć wirtualną i podsieć, a następnie używać ich w maszynie wirtualnej z Azure DevTest Labs
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 21fe23b6be6fdc924c5502ff97f9c03a3f3e759d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87283558"
---
# <a name="configure-a-virtual-network-in-azure-devtest-labs"></a>Konfigurowanie sieci wirtualnej w Azure DevTest Labs
Jak wyjaśniono w artykule [Dodawanie maszyny wirtualnej do laboratorium](devtest-lab-add-vm.md), podczas tworzenia maszyny wirtualnej w laboratorium można określić skonfigurowaną sieć wirtualną. Na przykład może być konieczne uzyskanie dostępu do zasobów Corpnet z maszyn wirtualnych przy użyciu sieci wirtualnej, która została skonfigurowana za pomocą usługi ExpressRoute lub sieci VPN typu lokacja-lokacja.

W tym artykule wyjaśniono, jak dodać istniejącą sieć wirtualną do ustawień Virtual Network laboratorium, aby umożliwić wybór podczas tworzenia maszyn wirtualnych.

> [!NOTE]
> Aby dowiedzieć się więcej o kosztach związanych z usługą Virtual Network platformy Azure, zobacz [Cennik usługi azure Virtual Network](../virtual-network/virtual-networks-overview.md#pricing).

## <a name="configure-a-virtual-network-for-a-lab-using-the-azure-portal"></a>Skonfiguruj sieć wirtualną dla laboratorium przy użyciu Azure Portal
Poniższe kroki przeprowadzą Cię przez proces dodawania istniejącej sieci wirtualnej (i podsieci) do laboratorium, aby można było go użyć podczas tworzenia maszyny wirtualnej w tym samym laboratorium. 

1. Zaloguj się w witrynie [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **DevTest Labs** .
1. Z listy laboratoriów wybierz odpowiednie laboratorium. 
1. W okienku głównym laboratorium wybierz pozycję **Konfiguracja i zasady**.

    ![Uzyskiwanie dostępu do konfiguracji i zasad laboratorium](./media/devtest-lab-configure-vnet/policies-menu.png)
1. W sekcji **Zasoby zewnętrzne** wybierz pozycję **sieci wirtualne**. Zostanie wyświetlona lista sieci wirtualnych skonfigurowanych dla bieżącego laboratorium, a także domyślna Sieć wirtualna utworzona dla laboratorium. 
1. Wybierz pozycję **+ Dodaj**.
   
    ![Dodawanie istniejącej sieci wirtualnej do laboratorium](./media/devtest-lab-configure-vnet/lab-settings-vnet-add.png)
1. W okienku **Sieć wirtualna** wybierz pozycję **[Wybierz sieć wirtualną]**.
   
    ![Wybierz istniejącą sieć wirtualną](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet1.png)
1. W okienku **Wybierz sieć wirtualną** wybierz żądaną sieć wirtualną. Zostanie wyświetlona lista przedstawiająca wszystkie sieci wirtualne, które znajdują się w tym samym regionie w ramach subskrypcji jako laboratorium.
1. Po wybraniu sieci wirtualnej nastąpi powrót do okienka **Sieć wirtualna** . Wybierz podsieć na liście u dołu.

    ![Lista podsieci](./media/devtest-lab-configure-vnet/lab-settings-vnets-vnet2.png)
    
    Zostanie wyświetlone okienko podsieć laboratorium.

    ![Okienko podsieci laboratorium](./media/devtest-lab-configure-vnet/lab-subnet.png)
     
   - Określ **nazwę podsieci laboratorium**.
   - Aby zezwolić na używanie podsieci podczas tworzenia maszyny wirtualnej laboratorium, wybierz opcję **Użyj podczas tworzenia maszyny wirtualnej**.
   - Aby włączyć [współużytkowany publiczny adres IP](devtest-lab-shared-ip.md), wybierz opcję **Włącz udostępniony publiczny IP**.
   - Aby zezwolić na publiczne adresy IP w podsieci, wybierz opcję **Zezwalaj na tworzenie publicznego adresu IP**.
   - W polu **Maksymalna liczba maszyn wirtualnych na użytkownika** Określ maksymalną liczbę maszyn wirtualnych na użytkownika dla każdej podsieci. Jeśli chcesz mieć nieograniczoną liczbę maszyn wirtualnych, pozostaw to pole puste.
1. Wybierz **przycisk OK** , aby zamknąć okienko podsieć laboratorium.
1. Wybierz pozycję **Zapisz** , aby zamknąć okienko Sieć wirtualna.

Teraz, gdy sieć wirtualna jest skonfigurowana, można ją wybrać podczas tworzenia maszyny wirtualnej. Aby dowiedzieć się, jak utworzyć MASZYNę wirtualną i określić sieć wirtualną, zapoznaj się z artykułem [Dodaj maszynę wirtualną do laboratorium](devtest-lab-add-vm.md). 

[Dokumentacja Virtual Network](../virtual-network/index.yml) platformy Azure zawiera więcej informacji o sposobach korzystania z programu sieci wirtualnych, w tym o sposobie konfigurowania sieci wirtualnej i zarządzania nią oraz łączenia jej z siecią lokalną.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Następne kroki
Po dodaniu odpowiedniej sieci wirtualnej do laboratorium, następnym krokiem jest [dodanie maszyny wirtualnej do laboratorium](devtest-lab-add-vm.md).
