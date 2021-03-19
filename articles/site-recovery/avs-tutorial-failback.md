---
title: Powrót po awarii rozwiązania Azure VMware VMsfrom Azure za pomocą Azure Site Recovery
description: Dowiedz się, jak przeprowadzić powrót po awarii do chmury prywatnej rozwiązania VMware platformy Azure po przejściu do trybu failover na platformie Azure podczas odzyskiwania po awarii.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: harshacs
ms.custom: MVC
ms.openlocfilehash: fb14e647d3444f2f0d0cb86901f93582a18848f5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91814587"
---
# <a name="fail-back-vms-to-azure-vmware-solution-private-cloud"></a>Powrót po awarii maszyn wirtualnych do chmury prywatnej rozwiązania Azure VMware

W tym artykule opisano, jak przeprowadzić powrót po awarii maszyn wirtualnych platformy Azure do chmury prywatnej rozwiązań VMware platformy Azure, po przejściu do [trybu failover](avs-tutorial-failover.md) maszyn wirtualnych rozwiązań VMware platformy Azure na platformie Azure z [Azure Site Recovery](site-recovery-overview.md). Po powrocie po awarii należy włączyć replikację, aby rozpocząć replikację maszyn wirtualnych rozwiązania Azure VMware do platformy Azure.

## <a name="before-you-start"></a>Przed rozpoczęciem

1. Dowiedz się więcej o [powrotu po awarii programu VMware](failover-failback-overview.md#vmwarephysical-reprotectionfailback). 
2. Upewnij się, że została sprawdzona i została wykonana procedura [przygotowania do powrotu po awarii](vmware-azure-prepare-failback.md)oraz że wszystkie wymagane składniki zostały wdrożone. Składniki obejmują serwer przetwarzania na platformie Azure, główny serwer docelowy oraz połączenie sieci VPN typu lokacja-lokacja (lub prywatna Komunikacja równorzędna ExpressRoute) na potrzeby powrotu po awarii.
3. Upewnij się, że zostały spełnione [wymagania](avs-tutorial-reprotect.md#before-you-begin) dotyczące ponownych prób ochrony i powrotu po awarii oraz że [włączono ochronę](avs-tutorial-reprotect.md#enable-reprotection) maszyn wirtualnych platformy Azure, dzięki czemu są one replikowane z platformy Azure do chmury prywatnej rozwiązania Azure VMware. Maszyny wirtualne muszą znajdować się w stanie replikowanym, aby można było wrócić do trybu failover.




## <a name="run-a-failover-to-fail-back"></a>Uruchamianie trybu failover w celu powrotu po awarii

1. Upewnij się, że maszyny wirtualne platformy Azure są ponownie chronione i replikowane do chmury prywatnej rozwiązania Azure VMware.
    - Maszyna wirtualna musi mieć co najmniej jeden punkt odzyskiwania w celu powrotu po awarii.
    - W przypadku powrotu po awarii planu odzyskiwania wszystkie maszyny w planie powinny mieć co najmniej jeden punkt odzyskiwania.
2. W magazynie > **zreplikowane elementy** wybierz maszynę wirtualną. Kliknij prawym przyciskiem myszy maszynę wirtualną > **niezaplanowaną pracę w trybie failover**.
3. W obszarze **Potwierdź tryb failover** sprawdź kierunek trybu failover (z platformy Azure).
4. Wybierz punkt odzyskiwania, którego chcesz użyć podczas pracy w trybie failover.
    - Zalecamy korzystanie z **najnowszego** punktu odzyskiwania. Punkt spójności aplikacji jest za ostatnim punktem w czasie i powoduje utratę danych.
    - **Najnowsza** to punkt odzyskiwania spójny na poziomie awarii.
    - W przypadku **najnowszej wersji** maszyny wirtualnej przechodzi w tryb failover do najnowszego dostępnego punktu w czasie. Jeśli istnieje grupa replikacji dla spójności wielodostępnej w ramach planu odzyskiwania, każda maszyna wirtualna w grupie przejdzie w tryb failover do jego niezależnego ostatniego punktu w czasie.
    - Jeśli używasz punktu odzyskiwania spójnego na poziomie aplikacji, każda maszyna wirtualna przechodzi do ostatniego dostępnego punktu. Jeśli plan odzyskiwania ma grupę replikacji, każda grupa odzyska do swojego wspólnego dostępnego punktu odzyskiwania.
5. Rozpoczynanie pracy w trybie failover. Site Recovery wyłącza maszyny wirtualne platformy Azure.
6. Po zakończeniu pracy w trybie failover Sprawdź, czy wszystko działa zgodnie z oczekiwaniami. Sprawdź, czy maszyny wirtualne platformy Azure są zamknięte. 
7. Po zweryfikowaniu wszystkiego kliknij prawym przyciskiem myszy maszynę wirtualną > **zatwierdzenie**, aby zakończyć proces przełączania do trybu failover. Zatwierdzenie powoduje usunięcie maszyny wirtualnej platformy Azure, która została zakończona niepowodzeniem. 

> [!NOTE]
> W przypadku maszyn wirtualnych z systemem Windows Site Recovery wyłącza narzędzia VMware podczas pracy w trybie failover. Podczas powrotu po awarii maszyny wirtualnej z systemem Windows narzędzia VMware są włączane ponownie. 




## <a name="reprotect-from-azure-vmware-solution-to-azure"></a>Ponowne włączanie ochrony z rozwiązań VMware platformy Azure do platformy Azure

Po potwierdzeniu powrotu po awarii maszyny wirtualne platformy Azure zostaną usunięte. Maszyna wirtualna jest ponownie w chmurze prywatnej rozwiązania Azure VMware, ale nie jest chroniona. Aby ponownie rozpocząć replikowanie maszyn wirtualnych do platformy Azure w następujący sposób:

1. W magazynie > **zreplikowane elementy** wybierz pozycję zaplecze maszyny wirtualne, a następnie wybierz pozycję **Włącz ponownie ochronę**.
2. Określ serwer przetwarzania, który jest używany do wysyłania danych z powrotem do platformy Azure.
3. Wybierz **przycisk OK** , aby rozpocząć zadanie ponownego włączania ochrony.

> [!NOTE]
> Po rozpoczęciu maszyny wirtualnej rozwiązania Azure VMware do zarejestrowania agenta na serwerze konfiguracji może minąć do 15 minut. W tym czasie ponowna ochrona nie powiedzie się i zwróci komunikat o błędzie z informacją, że Agent nie jest zainstalowany. W takim przypadku poczekaj kilka minut i ponownie Włącz ochronę.

## <a name="next-steps"></a>Następne kroki

Po zakończeniu zadania ponownego włączania ochrony maszyna wirtualna rozwiązania Azure VMware jest replikowana na platformę Azure. W razie potrzeby można [uruchomić kolejną pracę w trybie failover](avs-tutorial-failover.md) na platformie Azure.

