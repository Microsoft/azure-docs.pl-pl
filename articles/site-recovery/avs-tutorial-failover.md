---
title: Praca w trybie failover maszyn wirtualnych rozwiązania Azure VMware na platformie Azure przy użyciu Site Recovery
description: Dowiedz się, jak przełączać maszyny wirtualne rozwiązań VMware platformy Azure do trybu failover na platformie Azure w Azure Site Recovery
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: 60c268ba837540eda86a4cbaf6e0ab1c425d90b4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91814567"
---
# <a name="fail-over--azure-vmware-solution-vms"></a>Maszyny wirtualne rozwiązania Azure VMware w trybie failover

W tym artykule opisano, jak przełączyć maszynę wirtualną rozwiązania VMware platformy Azure na platformę Azure przy użyciu [Azure Site Recovery](site-recovery-overview.md).

Jest to piąty samouczek w serii, który pokazuje, jak skonfigurować odzyskiwanie po awarii na platformie Azure dla maszyn wirtualnych rozwiązań VMware platformy Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]

> * Sprawdź, czy właściwości maszyny wirtualnej rozwiązania VMware platformy Azure są zgodne z wymaganiami platformy Azure.
> * Przełączenie określonych maszyn wirtualnych do trybu failover na platformie Azure.

> [!NOTE]
> Samouczki pokazują najprostszą ścieżkę wdrożenia dla scenariusza. Korzystają one z opcji domyślnych, tam gdzie to możliwe, i nie wyświetlają wszystkich możliwych ustawień i ścieżek. Aby uzyskać szczegółowe informacje na temat pracy awaryjnej, zobacz [maszyny wirtualne](site-recovery-failover.md)w trybie failover.

[Dowiedz się więcej o](failover-failback-overview.md#types-of-failover) różnych typach trybu failover. Jeśli chcesz przejść do trybu failover wielu maszyn wirtualnych w planie odzyskiwania, zapoznaj się z [tym artykułem](site-recovery-failover.md).

## <a name="before-you-start"></a>Przed rozpoczęciem

Wykonaj poprzednie samouczki:

1. Upewnij się, że [skonfigurowano platformę Azure](avs-tutorial-prepare-azure.md) pod kątem odzyskiwania po awarii na platformie Azure.
2. Wykonaj następujące [kroki](avs-tutorial-prepare-avs.md) , aby przygotować wdrożenie rozwiązania Azure VMware do odzyskiwania po awarii na platformie Azure.
3. [Skonfiguruj](avs-tutorial-replication.md) odzyskiwanie po awarii dla maszyn wirtualnych rozwiązań VMware platformy Azure.
4. Uruchom [drążenie odzyskiwania po awarii](avs-tutorial-dr-drill-azure.md) , aby upewnić się, że wszystko działa zgodnie z oczekiwaniami.

## <a name="verify-vm-properties"></a>Sprawdzanie właściwości maszyn wirtualnych

Przed uruchomieniem trybu failover Sprawdź właściwości maszyny wirtualnej, aby upewnić się, że maszyny wirtualne spełniają [wymagania platformy Azure](vmware-physical-azure-support-matrix.md#replicated-machines).

Sprawdź właściwości w następujący sposób:

1. W obszarze **chronione elementy** wybierz pozycję **zreplikowane elementy**, a następnie wybierz maszynę wirtualną, którą chcesz zweryfikować.

2. Okienko **Zreplikowany element** zawiera podsumowanie informacji na temat maszyny wirtualnej, jej kondycję oraz najnowsze dostępne punkty odzyskiwania. Wybierz pozycję **Właściwości** , aby wyświetlić więcej szczegółów.

3. W obszarze **obliczenia i sieć** można modyfikować te właściwości zgodnie z wymaganiami:
    * Nazwa platformy Azure
    * Grupa zasobów
    * Rozmiar docelowy
    * [Zestaw dostępności](../virtual-machines/windows/tutorial-availability-sets.md)
    * Ustawienia dysku zarządzanego

4. Można wyświetlać i modyfikować ustawienia sieci, w tym:

    * Sieć i podsieć, w której zostanie umieszczona maszyna wirtualna platformy Azure po przejściu do trybu failover.
    * Adres IP, który zostanie przypisany do niego.

5. W obszarze **Dyski** można znaleźć informacje dotyczące systemu operacyjnego i dysków danych maszyny wirtualnej.

## <a name="run-a-failover-to-azure"></a>Przełączanie do trybu failover na platformie Azure

1. W obszarze **Ustawienia**  >  **zreplikowane elementy** wybierz maszynę wirtualną, która ma zostać przełączona w tryb failover, a następnie wybierz pozycję **Praca awaryjna**.
2. W obszarze **Tryb failover** wybierz **Punkt odzyskiwania**, do którego przełączenie w tryb failover ma zostać wykonane. Możesz użyć jednej z następujących opcji:
   * **Najnowszy**: ta opcja najpierw przetwarza wszystkie dane wysyłane do usługi Site Recovery. Zapewnia najniższy cel punktu odzyskiwania (RPO), ponieważ maszyna wirtualna platformy Azure utworzona po przejściu w tryb failover ma wszystkie dane, które zostały zreplikowane do Site Recovery podczas wyzwolenia trybu failover.
   * **Najnowsza przetworzony**: Ta opcja powoduje, że maszyna wirtualna nie jest w trybie failover do najnowszego punktu odzyskiwania przetworzonego przez Site Recovery. Ta opcja zapewnia niski RTO (cel czasu odzyskiwania), ponieważ nie trwa przetwarzanie nieprzetworzonych danych.
   * **Najnowsza spójna dla aplikacji**: Ta opcja nie powoduje przełączenia maszyny wirtualnej w tryb failover do najnowszego punktu odzyskiwania spójnego na poziomie aplikacji przetworzonego przez Site Recovery.
   * **Niestandardowe**: Ta opcja umożliwia określenie punktu odzyskiwania.

3. Wybierz opcję **Zamknij maszynę przed rozpoczęciem pracy w trybie failover** , aby podjąć próbę zamknięcia źródłowych maszyn wirtualnych przed wyzwoleniem trybu failover. Tryb failover kontynuuje działanie nawet wtedy, gdy wyłączenie nie powiedzie się. Postęp pracy w trybie failover można wykonać na stronie **zadań** .

W niektórych scenariuszach tryb failover wymaga dodatkowego przetwarzania, którego ukończenie trwa od 8 do 10 minut. Możesz zauważyć dłuższe czasy testowania pracy w trybie failover dla:

* Maszyny wirtualne VMware z uruchomioną wersją usługi mobilności starszą niż 9,8.
* Maszyny wirtualne VMware Linux.
* Maszyny wirtualne VMware, które nie mają włączonej usługi DHCP.
* Maszyny wirtualne VMware, które nie mają następujących sterowników rozruchowych: storvsc, VMBus, storflt, Intelide, ATAPI.

> [!WARNING]
> Nie Anuluj trybu failover w toku. Przed rozpoczęciem pracy w trybie failover zatrzymywana jest replikacja maszyny wirtualnej. Jeśli anulujesz tryb failover po rozpoczęciu przełączania, zostanie ono zatrzymane, ale maszyna wirtualna nie zostanie ponownie zreplikowana.

## <a name="connect-to-failed-over-vm"></a>Nawiązywanie połączenia z maszyną wirtualną w trybie failover

1. Jeśli chcesz nawiązać połączenie z maszynami wirtualnymi platformy Azure po przejściu w tryb failover przy użyciu usług Remote Desktop Protocol (RDP) i Secure Shell (SSH), [Sprawdź, czy zostały spełnione wymagania](failover-failback-overview.md#connect-to-azure-after-failover).
2. Po przejściu w tryb failover przejdź do maszyny wirtualnej i sprawdź poprawność, [łącząc](../virtual-machines/windows/connect-logon.md) się z nią.
3. Jeśli po przejściu w tryb failover ma zostać użyty inny punkt odzyskiwania, użyj **Zmień punkt odzyskiwania** . Po zatwierdzeniu przejścia w tryb failover w następnym kroku ta opcja nie będzie już dostępna.
4. Po sprawdzeniu poprawności wybierz pozycję **Zatwierdź** , aby zakończyć punkt odzyskiwania maszyny wirtualnej po przejściu do trybu failover.
5. Po zatwierdzeniu wszystkie pozostałe dostępne punkty odzyskiwania zostaną usunięte. Ten krok powoduje zakończenie pracy w trybie failover.

>[!TIP]
> Jeśli występują problemy z łącznością po przejściu do trybu failover, postępuj zgodnie z [przewodnikiem rozwiązywania problemów](site-recovery-failover-to-azure-troubleshoot.md).

## <a name="next-steps"></a>Następne kroki

Po przejściu w tryb failover ponownie Włącz ochronę maszyn wirtualnych platformy Azure w chmurze prywatnej rozwiązań VMware platformy Azure. Następnie po ponownym włączeniu ochrony maszyn wirtualnych i przeprowadzeniu replikacji do chmury prywatnej rozwiązania VMware firmy Azure powrót po awarii z platformy Azure kończy się niepowodzeniem.


> [!div class="nextstepaction"]
> Ponowne [Włączanie ochrony maszyn wirtualnych](avs-tutorial-reprotect.md) 
>  platformy Azure [Powrót po awarii z platformy Azure](avs-tutorial-failback.md)
