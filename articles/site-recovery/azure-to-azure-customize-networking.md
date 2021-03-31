---
title: Dostosowywanie konfiguracji sieci dla maszyny wirtualnej trybu failover | Microsoft Docs
description: Zawiera omówienie dostosowywania konfiguracji sieci dla maszyny wirtualnej trybu failover w replikacji maszyn wirtualnych platformy Azure przy użyciu Azure Site Recovery.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: sideeksh
ms.openlocfilehash: f63021275574e294fa372357d6e62724f5efe0f7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98541194"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>Dostosowywanie konfiguracji sieci dla docelowej maszyny wirtualnej platformy Azure

Ten artykuł zawiera wskazówki dotyczące dostosowywania konfiguracji sieci na docelowej maszynie wirtualnej platformy Azure w przypadku replikowania i odzyskiwania maszyn wirtualnych platformy Azure z jednego regionu do innego przy użyciu [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Przed rozpoczęciem

Dowiedz się, jak Site Recovery zapewnia odzyskiwanie po awarii w [tym scenariuszu](azure-to-azure-architecture.md).

## <a name="supported-networking-resources"></a>Obsługiwane zasoby sieciowe

Podczas replikowania maszyn wirtualnych platformy Azure można dostarczyć następujące konfiguracje zasobów kluczy dla maszyny wirtualnej trybu failover:

- [Wewnętrzny moduł równoważenia obciążenia](../load-balancer/load-balancer-overview.md)
- [Publiczny adres IP](../virtual-network/public-ip-addresses.md)
- [Grupa zabezpieczeń sieci](../virtual-network/manage-network-security-group.md) dla podsieci i karty sieciowej

## <a name="prerequisites"></a>Wymagania wstępne

- Upewnij się, że konfiguracje po stronie odzyskiwania są planowane z wyprzedzeniem.
- Utwórz z góry zasoby sieciowe. Podaj je jako dane wejściowe, aby usługa Azure Site Recovery mogła przestrzegać tych ustawień i upewnić się, że maszyna wirtualna trybu failover będzie zgodna z tymi ustawieniami.

## <a name="customize-failover-and-test-failover-networking-configurations"></a>Dostosuj tryb failover i przetestuj konfiguracje sieci trybu failover

1. Przejdź do **pozycji zreplikowane elementy**. 
2. Wybierz żądaną maszynę wirtualną platformy Azure.
3. Wybierz pozycję **obliczenia i sieć** , a następnie wybierz pozycję **Edytuj**. Należy zauważyć, że ustawienia konfiguracji karty sieciowej zawierają odpowiednie zasoby w źródle. 

     ![Dostosowywanie konfiguracji sieci trybu failover](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Wybierz testową sieć wirtualną w trybie failover.
5. Sieć trybu failover jest **wybierana** obok karty sieciowej, która ma zostać skonfigurowana. W następnym bloku, który zostanie otwarty, wybierz odpowiednie wstępnie utworzone zasoby w lokalizacji testowej pracy w trybie failover i w trybie failover.

    ![Edytowanie konfiguracji karty sieciowej](media/azure-to-azure-customize-networking/nic-drilldown.png) 

6. Wybierz przycisk **OK**.

Site Recovery będzie teraz przestrzegać tych ustawień i upewnić się, że maszyna wirtualna w trybie failover jest połączona z wybranym zasobem za pośrednictwem odpowiedniej karty sieciowej.

Gdy Wyzwalasz test pracy w trybie failover za pośrednictwem planu odzyskiwania, zawsze będzie ona zwracać do sieci wirtualnej platformy Azure. Ta sieć wirtualna zostanie użyta na potrzeby testowego przełączania do trybu failover dla maszyn, na których nie zostały wstępnie skonfigurowane ustawienia testowej pracy w trybie failover.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="unable-to-view-or-select-a-resource"></a>Nie można wyświetlić lub wybrać zasobu

Jeśli nie możesz wybrać lub wyświetlić zasobu sieci, przejdź przez następujące testy i warunki:

- Pole docelowe dla zasobu sieciowego jest włączone tylko wtedy, gdy źródłowa maszyna wirtualna ma odpowiednie dane wejściowe. Jest to oparte na zasadzie, która w przypadku scenariusza odzyskiwania po awarii powinna mieć być dokładna lub skalowana w dół wersja źródła.
- Dla każdego zasobu sieciowego niektóre filtry są stosowane na liście rozwijanej, aby zapewnić, że maszyna wirtualna trybu failover może dołączyć do wybranego zasobu i utrzymywać niezawodność trybu failover. Te filtry są oparte na tych samych warunkach sieciowych, które zostały zweryfikowane podczas konfigurowania źródłowej maszyny wirtualnej.

Walidacje wewnętrznego modułu równoważenia obciążenia:

- Subskrypcja i region modułu równoważenia obciążenia oraz docelowej maszyny wirtualnej powinny być takie same.
- Sieć wirtualna skojarzona z wewnętrznym modułem równoważenia obciążenia i jej docelową MASZYNę wirtualną powinna być taka sama.
- Jednostka SKU publicznego adresu IP maszyny wirtualnej i jednostka SKU wewnętrznego modułu równoważenia obciążenia muszą być takie same.
- Jeśli docelowa maszyna wirtualna jest skonfigurowana do umieszczania w strefie dostępności, sprawdź, czy moduł równoważenia obciążenia jest nadmiarowy strefy lub nie jest częścią żadnej strefy dostępności. (Podstawowe usługi równoważenia obciążenia SKU nie obsługują stref i nie będą wyświetlane na liście rozwijanej w tym przypadku).
- Upewnij się, że wewnętrzny moduł równoważenia obciążenia ma wstępnie utworzoną pulę zaplecza i konfigurację frontonu.

Publiczny adres IP:

- Subskrypcja i region publicznego adresu IP i docelowej maszyny wirtualnej powinny być takie same.
- Jednostka SKU publicznego adresu IP maszyny wirtualnej i jednostka SKU wewnętrznego modułu równoważenia obciążenia muszą być takie same.

Sieciowa Grupa zabezpieczeń:
- Subskrypcja i region sieciowej grupy zabezpieczeń i docelowej maszyny wirtualnej powinny być takie same.


> [!WARNING]
> Jeśli docelowa maszyna wirtualna jest skojarzona z zestawem dostępności, należy skojarzyć publiczny adres IP i wewnętrzny moduł równoważenia obciążenia z tą samą jednostką SKU z tym publicznym adresem IP i wewnętrznym modułem równoważenia obciążenia w zestawie dostępności. Jeśli tego nie zrobisz, praca w trybie failover może zakończyć się niepowodzeniem.
