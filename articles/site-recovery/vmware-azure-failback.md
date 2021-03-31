---
title: Powrót po awarii maszyn wirtualnych VMware/serwerów fizycznych z platformy Azure za pomocą Azure Site Recovery
description: Informacje dotyczące powrotu po awarii do lokacji lokalnej po przejściu do trybu failover na platformie Azure, podczas odzyskiwania danych z maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.date: 01/15/2019
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: aed015b67aa36e7678b31d7f2f047cb1e77c6a3d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004198"
---
# <a name="fail-back-vmware-vms-to-on-premises-site"></a>Powrót po awarii maszyn wirtualnych VMware do lokacji lokalnej

W tym artykule opisano sposób powrotu po awarii maszyn wirtualnych platformy Azure do lokacji lokalnej, po przejściu do [trybu failover](site-recovery-failover.md) lokalnych maszyn wirtualnych na platformie Azure z [Azure Site Recovery](site-recovery-overview.md). Po powrocie po awarii do stanu lokalnego należy włączyć replikację, aby lokalne maszyny wirtualne uruchomiły replikację do platformy Azure.

## <a name="before-you-start"></a>Przed rozpoczęciem

1. Dowiedz się więcej o [powrotu po awarii programu VMware](failover-failback-overview.md#vmwarephysical-reprotectionfailback). 
2. Upewnij się, że została sprawdzona i została wykonana procedura [przygotowania do powrotu po awarii](vmware-azure-prepare-failback.md)oraz że wszystkie wymagane składniki zostały wdrożone. Składniki obejmują serwer przetwarzania na platformie Azure, lokalny główny serwer docelowy oraz połączenie sieci VPN typu lokacja-lokacja (lub prywatna Komunikacja równorzędna ExpressRoute) na potrzeby powrotu po awarii.
3. Upewnij się, że zostały spełnione [wymagania](vmware-azure-reprotect.md#before-you-begin) dotyczące ponownej ochrony i powrotu po awarii oraz że [włączono ochronę](vmware-azure-reprotect.md#enable-reprotection) maszyn wirtualnych platformy Azure, dzięki czemu są one replikowane z platformy Azure do lokacji lokalnej. Maszyny wirtualne muszą znajdować się w stanie replikowanym, aby można było wrócić do trybu failover.




## <a name="run-a-failover-to-fail-back"></a>Uruchamianie trybu failover w celu powrotu po awarii

1. Upewnij się, że maszyny wirtualne platformy Azure są ponownie chronione i replikowane do lokacji lokalnej.
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




## <a name="reprotect-from-on-premises-to-azure"></a>Ponowne włączanie ochrony z poziomu lokalnego na platformę Azure

Po potwierdzeniu powrotu po awarii maszyny wirtualne platformy Azure zostaną usunięte. Maszyna wirtualna znajduje się w lokacji lokalnej, ale nie jest chroniona. Aby ponownie rozpocząć replikowanie maszyn wirtualnych do platformy Azure w następujący sposób:

1. W magazynie > **zreplikowane elementy** wybierz pozycję zaplecze maszyny wirtualne, a następnie wybierz pozycję **Włącz ponownie ochronę**.
2. Określ serwer przetwarzania, który jest używany do wysyłania danych z powrotem do platformy Azure.
3. Wybierz **przycisk OK** , aby rozpocząć zadanie ponownego włączania ochrony.

> [!NOTE]
> Po rozpoczęciu lokalnej maszyny wirtualnej ponowne zarejestrowanie na serwerze konfiguracji przez agenta może potrwać do 15 minut. W tym czasie ponowna ochrona nie powiedzie się i zwróci komunikat o błędzie z informacją, że Agent nie jest zainstalowany. W takim przypadku poczekaj kilka minut i ponownie Włącz ochronę.

## <a name="next-steps"></a>Następne kroki

Po zakończeniu zadania ponownego włączania ochrony lokalna maszyna wirtualna jest replikowana na platformę Azure. W razie potrzeby można [uruchomić kolejną pracę w trybie failover](site-recovery-failover.md) na platformie Azure.

