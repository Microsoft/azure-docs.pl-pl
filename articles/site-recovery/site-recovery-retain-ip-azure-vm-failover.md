---
title: Zachowaj adresy IP po przejściu do trybu failover maszyny wirtualnej platformy Azure za pomocą Azure Site Recovery
description: Zawiera opis sposobu zachowywania adresów IP po przełączeniu w tryb failover maszyn wirtualnych platformy Azure na potrzeby odzyskiwania po awarii do regionu pomocniczego z Azure Site Recovery
ms.service: site-recovery
ms.date: 4/9/2019
author: mayurigupta13
ms.topic: conceptual
ms.author: mayg
ms.openlocfilehash: 650fb7f0877a98ef53ed3868550f9c084ecb5885
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84710205"
---
# <a name="retain-ip-addresses-during-failover"></a>Zachowaj adresy IP podczas trybu failover

[Azure Site Recovery](site-recovery-overview.md) umożliwia odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure przez replikowanie maszyn wirtualnych do innego regionu platformy Azure, przechodzenie w tryb failover, jeśli wystąpi awaria, i powrót po awarii do regionu podstawowego, gdy elementy zostaną przywrócone do normalnego.

Podczas pracy w trybie failover możesz chcieć zachować adresowanie IP w regionie docelowym identycznym z regionem źródłowym:

- Domyślnie po włączeniu odzyskiwania po awarii dla maszyn wirtualnych platformy Azure Site Recovery tworzy zasoby docelowe na podstawie ustawień zasobów źródłowych. W przypadku maszyn wirtualnych platformy Azure skonfigurowanych ze statycznymi adresami IP Site Recovery próbuje zainicjować ten sam adres IP dla docelowej maszyny wirtualnej, jeśli nie jest używany. Aby uzyskać pełne wyjaśnienie, jak Site Recovery obsługuje adresowanie, [zapoznaj się z tym artykułem](azure-to-azure-network-mapping.md#set-up-ip-addressing-for-target-vms).
- W przypadku prostych aplikacji wystarcza konfiguracja domyślna. W przypadku bardziej złożonych aplikacji może być konieczne udostępnienie dodatkowego zasobu, aby upewnić się, że połączenie działa zgodnie z oczekiwaniami po przejściu do trybu failover.


W tym artykule przedstawiono kilka przykładów dotyczących zachowywania adresów IP w bardziej złożonych przykładowych scenariuszach. Przykłady obejmują:

- Praca w trybie failover dla firmy ze wszystkimi zasobami działającymi na platformie Azure
- Praca w trybie failover dla firmy z wdrożeniem hybrydowym oraz zasoby działające lokalnie i na platformie Azure

## <a name="resources-in-azure-full-failover"></a>Zasoby na platformie Azure: pełna praca awaryjna

Firma A ma wszystkie aplikacje działające na platformie Azure.

### <a name="before-failover"></a>Przed przejściem w tryb failover

Poniżej przedstawiono architekturę przed przejściem w tryb failover.

- Firma A ma identyczne sieci i podsieci w źródłowych i docelowych regionach platformy Azure.
- Aby skrócić cel czasu odzyskiwania (RTO), firma używa węzłów repliki dla SQL Server zawsze włączone, kontrolery domeny itd. Te węzły replik znajdują się w innej sieci wirtualnej w regionie docelowym, dzięki czemu mogą nawiązywać połączenia sieci VPN między lokacjami źródłowymi i docelowymi. Nie jest to możliwe, jeśli ta sama przestrzeń adresów IP jest używana w źródle i miejscu docelowym.  
- Przed przejściem w tryb failover architektura sieci jest następująca:
    - Region podstawowy to Azja Wschodnia platformy Azure
        - Azja Wschodnia ma sieć wirtualną (**źródłową sieć wirtualną**) z przestrzenią adresową 10.1.0.0/16.
        - Azja Wschodnia ma podział obciążeń na trzy podsieci w sieci wirtualnej:
            - **Podsieć 1**: 10.1.1.0/24
            - **Podsieć 2**: 10.1.2.0/24
            - **Podsieć 3**: 10.1.3.0/24
    - Region pomocniczy (docelowy) to Azja Południowo-Wschodnia platformy Azure
        - Azja Południowo-Wschodnia ma sieć wirtualną odzyskiwania (**Sieć wirtualna**), która jest identyczna z **źródłową siecią wirtualną**.
        - Azja Południowo-Wschodnia ma dodatkową sieć wirtualną (**Sieć wirtualna platformy Azure**) z przestrzenią adresową 10.2.0.0/16.
        - Sieć **wirtualna platformy Azure** zawiera podsieci (**podsieć 4**) z przestrzenią adresową 10.2.4.0/24.
        - Węzły repliki dla SQL Server zawsze włączone, kontroler domeny itp. znajdują się w **podsieci 4**.
    - **Źródłowa sieć wirtualna** i **Sieć wirtualna platformy Azure** są połączone z połączeniem sieci VPN typu lokacja-lokacja.
    - Sieć wirtualna **odzyskiwania** nie jest połączona z żadną inną siecią wirtualną.
    - **Firma A** przypisuje/weryfikuje docelowe adresy IP dla zreplikowanych elementów. Docelowy adres IP jest taki sam jak źródłowy adres IP dla każdej maszyny wirtualnej.

![Zasoby na platformie Azure przed ukończeniem trybu failover](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Po przejściu w tryb failover

Jeśli wystąpi awaria regionalna, firma A może przełączyć wszystkie zasoby na region docelowy.

- Przed przejściem w tryb failover firma A może organizować przechodzenie w tryb failover i automatycznie ustanawiać połączenia po przejściu w tryb failover między siecią **wirtualną odzyskiwania** i siecią **wirtualną platformy Azure**. Jest to zilustrowane na poniższym diagramie.
- W zależności od wymagań aplikacji połączenia między dwiema sieci wirtualnych (siecią**wirtualną odzyskiwania** i siecią **wirtualną platformy Azure**) w regionie docelowym można ustalić przed, w trakcie (w ramach pośredniego kroku) lub po przejściu w tryb failover.
  - Firma może użyć [planów odzyskiwania](site-recovery-create-recovery-plans.md) , aby określić, kiedy będą nawiązywane połączenia.
  - Mogą łączyć się między sieci wirtualnych za pomocą komunikacji równorzędnej sieci wirtualnych lub VPN typu lokacja-lokacja.
      - W przypadku wirtualnych sieci równorzędnych nie jest używana brama sieci VPN i występują inne ograniczenia.
      - [Ceny](https://azure.microsoft.com/pricing/details/virtual-network) wirtualnych sieci równorzędnych są obliczane inaczej niż [ceny](https://azure.microsoft.com/pricing/details/vpn-gateway)za sieć wirtualna-sieć wirtualna VPN Gateway. W przypadku pracy awaryjnej zaleca się używanie tej samej metody połączenia co sieci źródłowe, w tym typu połączenia, aby zminimalizować nieprzewidywalne zdarzenia sieciowe.

    ![Zasoby na platformie Azure — pełny tryb failover](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-full-region-failover2.png)



## <a name="resources-in-azure-isolated-app-failover"></a>Zasoby na platformie Azure: tryb failover izolowanej aplikacji

Może zajść potrzeba przełączenia w tryb failover na poziomie aplikacji. Na przykład w celu przełączenia w tryb failover konkretnej aplikacji lub warstwy aplikacji znajdującej się w dedykowanej podsieci.

- W tym scenariuszu, chociaż można zachować adresowanie IP, nie jest to ogólnie zalecane, ponieważ zwiększa to ryzyko niespójności łączności. W tej samej sieci wirtualnej platformy Azure zostanie również utracona łączność z podsiecią.
- Lepszym sposobem przełączenia w tryb failover aplikacji na poziomie podsieci jest użycie różnych docelowych adresów IP w celu przełączenia w tryb failover (Jeśli potrzebna jest łączność z innymi podsieciami w źródłowej podsieci wirtualnej) lub odizolowanie każdej aplikacji w swojej dedykowanej wirtualnej lokalizacji w regionie źródłowym. Z drugiej strony można ustanowić łączność między sieciami w regionie źródłowym i emulować to samo zachowanie po przełączeniu w tryb failover w regionie docelowym.  

W tym przykładzie firma A umieszcza aplikacje w regionie źródłowym w dedykowanej sieci wirtualnych i ustanawia łączność między tymi sieci wirtualnych. Dzięki temu projektowi mogą działać izolowane aplikacje w trybie failover i zachować źródłowe prywatne adresy IP w sieci docelowej.

### <a name="before-failover"></a>Przed przejściem w tryb failover

Przed przejściem w tryb failover architektura jest następująca:

- Maszyny wirtualne aplikacji są hostowane w podstawowym regionie usługi Azure Azja Wschodnia:
    - **APP1** Maszyny wirtualne znajdują się w sieci wirtualnej **VNET 1**: 10.1.0.0/16.
    - **APP2** Maszyny wirtualne znajdują się w sieci wirtualnej **VNET 2**: 10.2.0.0/16.
    - **Źródłowa sieć wirtualna 1** ma dwie podsieci.
    - **Źródłowa sieć wirtualna 2** ma dwie podsieci.
- Region pomocniczy (docelowy) to Południowo-Wschodnia Azja — Azja Południowo-Wschodnia ma sieci wirtualnych odzyskiwania (replikacja sieci**wirtualnej 1** i **Sieć wirtualna 2**), które są identyczne jak **źródłowa sieć wirtualna 1** i **źródłowa sieć wirtualna 2**.
        - Usługa **Recovery VNET 1** i **Sieć wirtualna odzyskiwania 2** każda z nich ma dwie podsieci, które pasują do podsieci w **źródłowej sieci wirtualnej 1** , a **źródłowa sieć wirtualna 2** — Azja Południowo-Wschodnia ma dodatkową sieć wirtualną (**Sieć wirtualna platformy Azure**) z przestrzenią adresową 10.3.0.0/16.
        - Sieć **wirtualna platformy Azure** zawiera podsieci (**podsieć 4**) z przestrzenią adresową 10.3.4.0/24.
        -Węzły repliki dla SQL Server zawsze włączone, kontroler domeny itp. znajdują się w **podsieci 4**.
- Istnieje wiele połączeń sieci VPN typu lokacja-lokacja: 
    - **Źródłowa sieć wirtualna 1** i **Sieć wirtualna platformy Azure**
    - **Źródłowa sieć wirtualna 2** i **Sieć wirtualna platformy Azure**
    - **Źródłowa sieć wirtualna 1** i **źródłowa sieć wirtualna 2** są połączone z siecią VPN typu lokacja-lokacja
- **Sieć wirtualna odzyskiwania 1** i **Sieć wirtualna odzyskiwania 2** nie są połączone z żadnymi innymi sieci wirtualnych.
- **Firma A** konfiguruje bramy sieci VPN przy użyciu sieci **wirtualnej odzyskiwania 1** i sieci **wirtualnej 2**, aby zmniejszyć RTO.  
- **VNet1 odzyskiwania** i **odzyskiwanie VNet2** nie są połączone z żadną inną siecią wirtualną.
- Aby skrócić cel czasu odzyskiwania (RTO), bramy sieci VPN są konfigurowane na potrzeby **odzyskiwania VNet1** i **odzyskiwania VNet2** przed przełączeniem w tryb failover.

    ![Zasoby na platformie Azure przed przejściem do trybu failover aplikacji](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-before-failover2.png)

### <a name="after-failover"></a>Po przejściu w tryb failover

W przypadku awarii lub problemu mającego wpływ na pojedynczą aplikację (w programie * * źródłowa sieć wirtualna 2 w naszym przykładzie) firma A może odzyskać zaatakowaną aplikację w następujący sposób:


- Rozłączaj połączenia sieci VPN między **źródłem VNet1** a **źródłem VNet2**i między **źródłem VNet2** i siecią **wirtualną platformy Azure** .
- Ustanów połączenia sieci VPN między elementami **Source VNet1** i **Recovery VNet2**, a następnie między **VNet2 Recovery** i sieci **wirtualnej platformy Azure**.
- Przełączenie w tryb failover maszyn wirtualnych w **źródłowym VNet2** do **VNet2 odzyskiwania**.

![Zasoby w trybie failover aplikacji platformy Azure](./media/site-recovery-retain-ip-azure-vm-failover/azure-to-azure-connectivity-isolated-application-after-failover2.png)


- Ten przykład można rozszerzyć w celu uwzględnienia większej liczby aplikacji i połączeń sieciowych. Zalecenie jest zgodne z modelem połączenia podobnym do tego, gdy jest to możliwe, w przypadku przejścia w tryb failover z lokalizacji źródłowej do docelowej.
- Bramy sieci VPN używają publicznych adresów IP i przeskoków bramy do nawiązywania połączeń. Jeśli nie chcesz używać publicznych adresów IP lub chcesz uniknąć dodatkowych przeskoków, możesz użyć [komunikacji równorzędnej Azure VNET](../virtual-network/virtual-network-peering-overview.md) z równorzędnymi sieciami wirtualnymi w [obsługiwanych regionach platformy Azure](../virtual-network/virtual-network-manage-peering.md#cross-region).

## <a name="hybrid-resources-full-failover"></a>Zasoby hybrydowe: pełna praca awaryjna

W tym scenariuszu **firma B** wykonuje hybrydową działalność biznesową, która jest częścią infrastruktury aplikacji działającej na platformie Azure i resztą działającą lokalnie. 

### <a name="before-failover"></a>Przed przejściem w tryb failover

Oto, jak wygląda architektura sieci przed przejściem w tryb failover.

- Maszyny wirtualne aplikacji są hostowane w usłudze Azure Azja Wschodnia.
- Azja Wschodnia ma sieć wirtualną (**źródłową sieć wirtualną**) z przestrzenią adresową 10.1.0.0/16.
  - Azja Wschodnia ma podział obciążeń na trzy podsieci w **źródłowej sieci wirtualnej**:
    - **Podsieć 1**: 10.1.1.0/24
    - **Podsieć 2**: 10.1.2.0/24
    - **Podsieć 3**: 10.1.3.0/24 przy użyciu sieci wirtualnej platformy Azure z przestrzenią adresową 10.1.0.0/16. Ta sieć wirtualna jest nazywana **źródłową siecią wirtualną**
      - Region pomocniczy (docelowy) to Azja Południowo-Wschodnia platformy Azure:
  - Azja Południowo-Wschodnia ma sieć wirtualną odzyskiwania (**Sieć wirtualna**), która jest identyczna z **źródłową siecią wirtualną**.
- Maszyny wirtualne w Azja Wschodnia są połączone z lokalnym centrum danych za pomocą usługi Azure ExpressRoute lub sieci VPN typu lokacja-lokacja.
- Aby ograniczyć RTO, firma B Inicjuje obsługę bram w sieci wirtualnej odzyskiwania na platformie Azure Południowo-Wschodnia przed przejściem do trybu failover.
- Firma B przypisuje/weryfikuje docelowe adresy IP dla replikowanych maszyn wirtualnych. Docelowy adres IP jest taki sam jak źródłowy adres IP dla każdej maszyny wirtualnej.


![Łączność między lokalizacjami lokalnymi i platformą Azure przed przejściem w tryb failover](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-before-failover2.png)

### <a name="after-failover"></a>Po przejściu w tryb failover


Jeśli wystąpi awaria regionalna, firma B może przełączyć wszystkie zasoby do trybu failover w regionie docelowym.

- Po przejściu do trybu failover firma B może organizować przechodzenie w tryb failover i automatycznie ustanawiać połączenia po przejściu w tryb failover między siecią **wirtualną odzyskiwania** i siecią **wirtualną platformy Azure**.
- W zależności od wymagań aplikacji połączenia między dwiema sieci wirtualnych (siecią**wirtualną odzyskiwania** i siecią **wirtualną platformy Azure**) w regionie docelowym można ustalić przed, w trakcie (w ramach pośredniego kroku) lub po przejściu w tryb failover. Firma może użyć [planów odzyskiwania](site-recovery-create-recovery-plans.md) , aby określić, kiedy będą nawiązywane połączenia.
- Oryginalne połączenie między usługą Azure Azja Wschodnia i lokalnym centrum danych powinno zostać rozłączone przed nawiązaniem połączenia między Południowo-Wschodnia i lokalnym centrum danych platformy Azure.
- Routing lokalny zostanie ponownie skonfigurowany w celu wskazywania regionu docelowego i bram po przejściu w tryb failover.

![Łączność między lokalizacjami lokalnymi i platformą Azure po przejściu w tryb failover](./media/site-recovery-retain-ip-azure-vm-failover/on-premises-to-azure-connectivity-after-failover2.png)

## <a name="hybrid-resources-isolated-app-failover"></a>Zasoby hybrydowe: tryb failover izolowanej aplikacji

Firma B nie może przełączyć izolowanych aplikacji na poziomie podsieci. Wynika to z faktu, że przestrzeń adresowa w sieci wirtualnych źródłowej i odzyskiwania jest taka sama, a oryginalne źródło połączenia lokalnego jest aktywne.

 - W przypadku usługi o odporności na aplikacje B należy umieścić każdą aplikację we własnej dedykowanej sieci wirtualnej platformy Azure.
 - W przypadku każdej aplikacji w oddzielnej sieci wirtualnej firma B może przełączać aplikacje izolowane w tryb failover i kierować połączenia ze źródłem do regionu docelowego.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [planach odzyskiwania](site-recovery-create-recovery-plans.md).
