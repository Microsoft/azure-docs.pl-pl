---
title: Mapuj sieci wirtualne między dwoma regionami w Azure Site Recovery
description: Informacje na temat mapowania sieci wirtualnych między dwoma regionami świadczenia usługi Azure na potrzeby odzyskiwania po awarii maszyny wirtualnej platformy Azure przy użyciu Azure Site Recovery.
author: Harsha-CS
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: harshacs
ms.openlocfilehash: b5ae68dea228e834b2449152bd3ef357f2a74e83
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90069496"
---
# <a name="set-up-network-mapping-and-ip-addressing-for-vnets"></a>Konfigurowanie mapowania sieci i adresowania IP dla sieci wirtualnych VNet

W tym artykule opisano sposób mapowania dwóch wystąpień sieci wirtualnych platformy Azure (sieci wirtualnych) znajdujących się w różnych regionach platformy Azure oraz konfigurowania adresów IP między sieciami. Mapowanie sieci zapewnia domyślne zachowanie wyboru sieci docelowej na podstawie sieci źródłowej w momencie włączania replikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Przed zamapowaniem sieci należy mieć [platformę Azure sieci wirtualnych](../virtual-network/virtual-networks-overview.md) w źródłowym i docelowym regionie platformy Azure. 

## <a name="set-up-network-mapping-manually-optional"></a>Ręcznie skonfiguruj mapowanie sieci (opcjonalnie)

Mapuj sieci w następujący sposób:

1. W obszarze **infrastruktura Site Recovery**kliknij pozycję **+ mapowanie sieci**.

    ![ Tworzenie mapowania sieci](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)

3. W obszarze **Dodawanie mapowania sieci**wybierz lokalizację źródłową i docelową. W naszym przykładzie źródłowa maszyna wirtualna jest uruchomiona w regionie Azja Wschodnia i jest replikowana do regionu Azja Południowo-Wschodnia.

    ![Wybierz źródło i cel](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)
3. Teraz Utwórz mapowanie sieci w odwrotnym kierunku. W naszym przykładzie Źródło będzie teraz miało Azja Południowo-Wschodnia, a element docelowy zostanie Azja Wschodnia.

    ![Okienko Dodawanie mapowania sieci — wybierz lokalizację źródłową i docelową dla sieci docelowej](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-networks-when-you-enable-replication"></a>Mapuj sieci po włączeniu replikacji

Jeśli nie przygotowano mapowania sieci przed skonfigurowaniem odzyskiwania po awarii dla maszyn wirtualnych platformy Azure, podczas [konfigurowania i włączania replikacji](azure-to-azure-how-to-enable-replication.md)można określić sieć docelową. Wykonując następujące czynności:

- W oparciu o wybrany element docelowy Site Recovery automatycznie tworzy mapowania sieci z regionu źródłowego do docelowego i z obszaru docelowego do źródła.
- Domyślnie Site Recovery tworzy sieć w regionie docelowym, który jest identyczny z siecią źródłową. Site Recovery dodaje **-ASR** jako sufiks do nazwy sieci źródłowej. Można dostosować sieć docelową.
- Jeśli mapowanie sieci już nastąpiło dla sieci źródłowej, zamapowana Sieć docelowa zawsze będzie domyślnie w momencie włączania replikacji dla większej liczby maszyn wirtualnych. Możesz zmienić docelową sieć wirtualną, wybierając z listy rozwijanej opcję inne dostępne opcje. 
- Aby zmienić domyślną docelową sieć wirtualną dla nowych replikacji, należy zmodyfikować istniejące mapowanie sieci.
- Aby zmodyfikować mapowanie sieci z regionu A do regionu B, należy najpierw usunąć mapowanie sieci z regionu B do regionu A. Po usunięciu mapowania odwrotnego zmodyfikuj mapowanie sieci z regionu A do regionu B, a następnie utwórz odpowiednie mapowanie odwrotne.

>[!NOTE]
>* Modyfikacja mapowania sieci powoduje jedynie zmianę ustawień domyślnych dla nowych replikacji maszyn wirtualnych. Nie ma to wpływu na wybór docelowej sieci wirtualnej dla istniejących replikacji. 
>* Jeśli chcesz zmodyfikować sieć docelową dla istniejącej replikacji, przejdź do ustawień obliczenia i sieć replikowanego elementu.

## <a name="specify-a-subnet"></a>Określ podsieć

Podsieć docelowej maszyny wirtualnej jest wybierana na podstawie nazwy podsieci źródłowej maszyny wirtualnej.

- Jeśli w sieci docelowej jest dostępna podsieć o takiej samej nazwie jak źródłowa podsieć maszyny wirtualnej, ta podsieć jest ustawiana dla docelowej maszyny wirtualnej.
- Jeśli podsieć o tej samej nazwie nie istnieje w sieci docelowej, pierwsza podsieć w kolejności alfabetycznej jest ustawiana jako podsieć docelowa.
- Można zmodyfikować podsieć docelową w ustawieniach **obliczeń i sieci** dla maszyny wirtualnej.

    ![Okno właściwości obliczeń obliczeniowych i sieciowych](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="set-up-ip-addressing-for-target-vms"></a>Skonfiguruj adresowanie IP dla docelowych maszyn wirtualnych

Adres IP dla każdej karty sieciowej na docelowej maszynie wirtualnej jest konfigurowany w następujący sposób:

- **DHCP**: Jeśli karta sieciowa ŹRÓDŁOWEJ maszyny wirtualnej używa protokołu DHCP, karta sieciowa docelowej maszyny wirtualnej jest również ustawiona do korzystania z protokołu DHCP.
- **Statyczny adres IP**: Jeśli karta sieciowa ŹRÓDŁOWEJ maszyny wirtualnej używa statycznego adresowania IP, docelowa karta sieciowa maszyny wirtualnej będzie również używać statycznego adresu IP.


## <a name="ip-address-assignment-during-failover"></a>Przypisywanie adresów IP podczas przełączania do trybu failover

**Źródłowa i docelowa podsieć** | **Szczegóły**
--- | ---
Ta sama przestrzeń adresowa | Adres IP źródłowej karty sieciowej maszyny wirtualnej jest ustawiony jako docelowy adres IP karty sieciowej maszyny wirtualnej.<br/><br/> Jeśli adres nie jest dostępny, następny dostępny adres IP jest ustawiany jako element docelowy.
Inna przestrzeń adresowa | Następny dostępny adres IP w podsieci docelowej jest ustawiany jako docelowy adres karty sieciowej maszyny wirtualnej.



## <a name="ip-address-assignment-during-test-failover"></a>Przypisanie adresu IP podczas testowego przełączania do trybu failover

**Sieć docelowa** | **Szczegóły**
--- | ---
Sieć docelowa jest siecią wirtualną trybu failover | -Docelowy adres IP będzie statyczny przy użyciu tego samego adresu IP. <br/><br/>  — Jeśli ten sam adres IP jest już przypisany, adres IP jest następną dostępną na końcu zakresu podsieci. Na przykład: Jeśli źródłowy adres IP to 10.0.0.19, a sieć trybu failover używa zakresu 10.0.0.0/24, wówczas Następny adres IP przypisany do docelowej maszyny wirtualnej to 10.0.0.254.
Sieć docelowa nie jest siecią wirtualną trybu failover | -Docelowy adres IP będzie statyczny przy użyciu tego samego adresu IP.<br/><br/>  — Jeśli ten sam adres IP jest już przypisany, adres IP jest następną dostępną na końcu zakresu podsieci.<br/><br/> Na przykład: Jeśli źródłowy statyczny adres IP to 10.0.0.19, a tryb failover znajduje się w sieci, która nie jest siecią trybu failover, z zakresem 10.0.0.0/24, docelowy statyczny adres IP będzie 10.0.0.0.19, jeśli jest dostępny, a w przeciwnym razie będzie 10.0.0.254.

- Sieć wirtualna trybu failover jest siecią docelową wybraną podczas konfigurowania odzyskiwania po awarii.
- Zalecamy, aby zawsze używać sieci nieprodukcyjnej do testowania pracy w trybie failover.
- Docelowy adres IP można zmodyfikować w ustawieniach **obliczeń i sieci** maszyny wirtualnej.


## <a name="next-steps"></a>Następne kroki

- Przejrzyj [wskazówki dotyczące sieci](./azure-to-azure-about-networking.md) na potrzeby odzyskiwania po awarii maszyny wirtualnej platformy Azure.
- [Dowiedz się więcej](site-recovery-retain-ip-azure-vm-failover.md) na temat zachowywania adresów IP po przejściu do trybu failover.
