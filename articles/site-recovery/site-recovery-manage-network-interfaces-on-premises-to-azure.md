---
title: Zarządzanie kartami sieciowymi lokalnego odzyskiwania po awarii przy użyciu Azure Site Recovery
description: Opisuje sposób zarządzania interfejsami sieciowymi na potrzeby lokalnego odzyskiwania po awarii na platformie Azure przy użyciu Azure Site Recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: harshacs
ms.openlocfilehash: 4dad7f76edf34782131c7c844978763cda53acc7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90068119"
---
# <a name="manage-vm-network-interfaces-for-on-premises-disaster-recovery-to-azure"></a>Zarządzanie interfejsami sieciowymi maszyny wirtualnej na potrzeby odzyskiwania po awarii ze środowiska lokalnego na platformę Azure

Maszyna wirtualna na platformie Azure musi mieć dołączony co najmniej jeden interfejs sieciowy. Może mieć dołączoną liczbę interfejsów sieciowych, ponieważ rozmiar maszyny wirtualnej obsługuje.

Domyślnie pierwszy interfejs sieciowy dołączony do maszyny wirtualnej platformy Azure jest zdefiniowany jako podstawowy interfejs sieciowy. Wszystkie inne interfejsy sieciowe w maszynie wirtualnej są dodatkowymi interfejsami sieciowymi. Domyślnie cały ruch wychodzący z maszyny wirtualnej jest wysyłany przy użyciu adresu IP przypisanego do podstawowej konfiguracji adresu IP podstawowego interfejsu sieciowego.

W środowisku lokalnym maszyny wirtualne lub serwery mogą mieć wiele interfejsów sieciowych dla różnych sieci w środowisku. Różne sieci są zwykle używane do wykonywania określonych operacji, takich jak uaktualnienia, konserwacja i dostęp do Internetu. W przypadku migrowania lub przejścia w tryb failover na platformę Azure z środowiska lokalnego należy pamiętać, że interfejsy sieciowe na tej samej maszynie wirtualnej muszą być połączone z tą samą siecią wirtualną.

Domyślnie Azure Site Recovery tworzy jako wiele interfejsów sieciowych na maszynie wirtualnej platformy Azure, które są podłączone do serwera lokalnego. Można uniknąć tworzenia nadmiarowych interfejsów sieciowych podczas migracji lub przejścia w tryb failover, edytując ustawienia interfejsu sieciowego w ustawieniach dla zreplikowanej maszyny wirtualnej.

## <a name="select-the-target-network"></a>Wybierz sieć docelową

W przypadku maszyn wirtualnych VMware i fizycznych oraz dla funkcji Hyper-V (bez System Center Virtual Machine Manager) można określić docelową sieć wirtualną dla poszczególnych maszyn wirtualnych. W przypadku maszyn wirtualnych funkcji Hyper-V zarządzanych przy użyciu Virtual Machine Manager należy użyć [mapowania sieci](./hyper-v-vmm-network-mapping.md) do mapowania sieci maszyn wirtualnych na źródłowym serwerze Virtual Machine Manager i docelowych sieci platformy Azure.

1. W obszarze **zreplikowane elementy** w magazynie Recovery Services wybierz dowolny replikowany element, aby uzyskać dostęp do ustawień dla tego zreplikowanego elementu.

2. Wybierz kartę **obliczenia i sieć** , aby uzyskać dostęp do ustawień sieci dla zreplikowanego elementu.

3. W obszarze **właściwości sieci**wybierz sieć wirtualną z listy dostępnych interfejsów sieciowych.

    ![Ustawienia sieciowe](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/compute-and-network.png)

Modyfikacja sieci docelowej ma wpływ na wszystkie interfejsy sieciowe dla tej konkretnej maszyny wirtualnej.

W przypadku chmur Virtual Machine Manager modyfikowanie mapowania sieci ma wpływ na wszystkie maszyny wirtualne i ich interfejsy sieciowe.

## <a name="select-the-target-interface-type"></a>Wybierz typ interfejsu docelowego

W sekcji **interfejsy sieciowe** okienka **obliczenia i sieć** można wyświetlać i edytować ustawienia interfejsu sieciowego. Możesz również określić docelowy typ interfejsu sieciowego.

- **Podstawowy** interfejs sieciowy jest wymagany do przejścia w tryb failover.
- Wszystkie inne wybrane interfejsy sieciowe (jeśli istnieją) są **dodatkowymi** interfejsami sieciowymi.
- Wybierz pozycję nie **Używaj** , aby wykluczyć interfejs sieciowy z tworzenia w trybie failover.

Domyślnie podczas włączania replikacji Site Recovery wybiera wszystkie wykryte interfejsy sieciowe na serwerze lokalnym. Oznacza jedną jako **podstawową** i wszystkie inne jako **pomocnicze**. Wszystkie kolejne interfejsy dodane na serwerze lokalnym są oznaczane jako **nieużywane** domyślnie. Gdy dodajesz więcej interfejsów sieciowych, upewnij się, że wybrany jest prawidłowy rozmiar docelowy maszyny wirtualnej platformy Azure, aby uwzględnić wszystkie wymagane interfejsy sieciowe.

## <a name="modify-network-interface-settings"></a>Modyfikowanie ustawień interfejsu sieciowego

Można zmodyfikować podsieć i adres IP dla interfejsów sieciowych zreplikowanego elementu. Jeśli nie określono adresu IP, Site Recovery przypisze następnego dostępnego adresu IP z podsieci do interfejsu sieciowego w trybie failover.

1. Wybierz dowolny dostępny interfejs sieciowy, aby otworzyć ustawienia interfejsu sieciowego.

2. Wybierz żądaną podsieć z listy dostępnych podsieci.

3. Wprowadź żądany adres IP (zgodnie z potrzebami).

    ![Ustawienia interfejsu sieciowego](./media/site-recovery-manage-network-interfaces-on-premises-to-azure/network-interface-settings.png)

4. Wybierz **przycisk OK** , aby zakończyć edycję i wrócić do okienka **obliczenia i sieć** .

5. Powtórz kroki 1-4 dla innych interfejsów sieciowych.

6. Wybierz pozycję **Zapisz** , aby zapisać wszystkie zmiany.

## <a name="next-steps"></a>Następne kroki
  [Dowiedz się więcej](../virtual-network/virtual-network-network-interface-vm.md) o interfejsach sieciowych dla maszyn wirtualnych platformy Azure.
