---
title: SQL Server Konfigurowanie zawsze włączonych grup dostępności w różnych regionach
description: W tym artykule wyjaśniono, jak skonfigurować grupę dostępności programu SQL Server zawsze włączona na maszynach wirtualnych platformy Azure z repliką w innym regionie.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 60bb5ac652a80b5ae52c91f91fa0c80440e9cc82
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2020
ms.locfileid: "97359085"
---
# <a name="configure-a-sql-server-always-on-availability-group-across-different-azure-regions"></a>SQL Server Skonfiguruj zawsze dostępną grupę dostępności w różnych regionach platformy Azure

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W tym artykule wyjaśniono, jak skonfigurować usługę SQL Server zawsze włączona replika grupy dostępności na maszynach wirtualnych platformy Azure w zdalnej lokalizacji platformy Azure. Ta konfiguracja służy do obsługi odzyskiwania po awarii.

Ten artykuł ma zastosowanie do usługi Azure Virtual Machines w trybie Menedżer zasobów.

Na poniższej ilustracji przedstawiono typowe wdrożenie grupy dostępności na maszynach wirtualnych platformy Azure:

   ![Diagram przedstawiający moduł równoważenia obciążenia platformy Azure i zestaw dostępności z opcją "klaster trybu failover systemu Windows Server" i "zawsze włączone grupy dostępności".](./media/availability-group-manually-configure-multiple-regions/00-availability-group-basic.png)

W tym wdrożeniu wszystkie maszyny wirtualne znajdują się w jednym regionie platformy Azure. Repliki grupy dostępności mogą mieć zatwierdzenie synchroniczne z automatycznym trybem failover w przypadku wersji SQL-1 i SQL-2. Aby skompilować tę architekturę, zobacz [szablon lub samouczek grupy dostępności](availability-group-overview.md).

Ta architektura jest narażona na przestoje w przypadku niedostępności regionu platformy Azure. Aby wyeliminować tę lukę w zabezpieczeniach, Dodaj replikę w innym regionie świadczenia usługi Azure. Na poniższym diagramie przedstawiono sposób, w jaki będzie wyglądać Nowa architektura:

   ![Grupa dostępności — DR](./media/availability-group-manually-configure-multiple-regions/00-availability-group-basic-dr.png)

Poprzedni diagram przedstawia nową maszynę wirtualną o nazwie SQL-3. SQL-3 znajduje się w innym regionie świadczenia usługi Azure. Do klastra trybu failover systemu Windows Server jest dodawana wartość SQL-3. Program SQL-3 może obsługiwać replikę grupy dostępności. Na koniec należy zauważyć, że region platformy Azure dla programu SQL-3 ma nowy moduł równoważenia obciążenia platformy Azure.

>[!NOTE]
> Zestaw dostępności platformy Azure jest wymagany, gdy więcej niż jedna maszyna wirtualna znajduje się w tym samym regionie. Jeśli w regionie znajduje się tylko jedna maszyna wirtualna, zestaw dostępności nie jest wymagany. Maszynę wirtualną można umieścić w zestawie dostępności w czasie jego tworzenia. Jeśli maszyna wirtualna jest już w zestawie dostępności, można później dodać maszynę wirtualną do dodatkowej repliki.

W tej architekturze replika w regionie zdalnym jest zwykle konfigurowana z trybem dostępności zatwierdzania asynchronicznego i trybem ręcznej pracy awaryjnej.

Gdy repliki grup dostępności znajdują się na maszynach wirtualnych platformy Azure w różnych regionach świadczenia usługi Azure, każdy region wymaga:

* Brama sieci wirtualnej
* Połączenie bramy sieci wirtualnej

Na poniższym diagramie przedstawiono, jak sieci komunikują się między centrami danych.

   ![Diagram pokazujący dwie sieci wirtualne w różnych regionach platformy Azure komunikujących się przy użyciu bram V P N.](./media/availability-group-manually-configure-multiple-regions/01-vpngateway-example.png)

>[!IMPORTANT]
>Ta architektura wiąże się z opłatami za dane wychodzące dla danych replikowanych między regionami platformy Azure. Zobacz [Cennik przepustowości](https://azure.microsoft.com/pricing/details/bandwidth/).  

## <a name="create-remote-replica"></a>Utwórz replikę zdalną

Aby utworzyć replikę w zdalnym centrum danych, wykonaj następujące czynności:

1. [Utwórz sieć wirtualną w nowym regionie](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network).

1. [Skonfiguruj połączenie między sieciami wirtualnymi przy użyciu Azure Portal](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).

   >[!NOTE]
   >W niektórych przypadkach może być konieczne użycie programu PowerShell do utworzenia połączenia między sieciami wirtualnymi. Jeśli na przykład używasz różnych kont platformy Azure, nie możesz skonfigurować połączenia w portalu. W takim przypadku zobacz [Konfigurowanie połączenia między sieciami wirtualnymi przy użyciu Azure Portal](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md).

1. [Utwórz kontroler domeny w nowym regionie](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100).

   Ten kontroler domeny zapewnia uwierzytelnianie, Jeśli kontroler domeny w lokacji głównej jest niedostępny.

1. [Utwórz SQL Server maszynę wirtualną w nowym regionie](create-sql-vm-portal.md).

1. [Utwórz moduł równoważenia obciążenia platformy Azure w sieci w nowym regionie](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer).

   Ten moduł równoważenia obciążenia musi:

   - Należy do tej samej sieci i podsieci co Nowa maszyna wirtualna.
   - Mieć statyczny adres IP dla odbiornika grupy dostępności.
   - Uwzględnij pulę zaplecza składającą się tylko z maszyn wirtualnych znajdujących się w tym samym regionie co moduł równoważenia obciążenia.
   - Użyj sondy portu TCP określonego dla adresu IP.
   - Mieć regułę równoważenia obciążenia specyficzną dla SQL Server w tym samym regionie.  
   - Usługa Load Balancer w warstwie Standardowa, jeśli maszyny wirtualne w puli zaplecza nie są częścią pojedynczego zestawu dostępności lub zestawu skalowania maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz [standardowe omówienie Azure Load Balancer](../../../load-balancer/load-balancer-overview.md).

1. [Dodaj funkcję Klaster trybu failover do nowej SQL Server](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms).

1. [Dołącz do domeny nowe SQL Server](availability-group-manually-configure-prerequisites-tutorial.md#joinDomain).

1. [Ustaw nowe konto usługi SQL Server, aby korzystało z konta domeny](availability-group-manually-configure-prerequisites-tutorial.md#setServiceAccount).

1. [Dodaj nowe SQL Server do klastra trybu failover z systemem Windows Server](availability-group-manually-configure-tutorial.md#addNode).

1. Dodawanie zasobu adresu IP do klastra.

   Zasób adresu IP można utworzyć w Menedżer klastra trybu failover. Wybierz nazwę klastra, a następnie kliknij prawym przyciskiem myszy nazwę klastra w obszarze **zasoby podstawowe klastra** i wybierz polecenie **Właściwości**: 

   ![Zrzut ekranu przedstawiający "Menedżer klastra trybu failover" z nazwą klastra, "nazwą serwera" i wybranym "właściwościami".](./media/availability-group-manually-configure-multiple-regions/cluster-name-properties.png)

   W oknie dialogowym **Właściwości** wybierz pozycję **Dodaj** w obszarze **adres IP**, a następnie Dodaj adres IP nazwy klastra z regionu sieci zdalnej. W oknie dialogowym **adres IP** wybierz **OK** , a następnie ponownie wybierz przycisk **OK** w oknie dialogowym **właściwości klastra** , aby zapisać nowy adres IP. 

   ![Dodaj adres IP klastra](./media/availability-group-manually-configure-multiple-regions/add-cluster-ip-address.png)


1. Dodaj adres IP jako zależność dla głównej nazwy klastra.

   Otwórz właściwości klastra, a następnie wybierz kartę **zależności** . Skonfiguruj zależność lub dla dwóch adresów IP: 

   ![Właściwości klastra](./media/availability-group-manually-configure-multiple-regions/cluster-ip-dependencies.png)

1. Dodaj zasób adresu IP do roli grupy dostępności w klastrze. 

   Kliknij prawym przyciskiem myszy rolę grupy dostępności w Menedżer klastra trybu failover, wybierz pozycję **Dodaj zasób**, **więcej zasobów** i wybierz pozycję **adres IP**.

   ![Utwórz adres IP](./media/availability-group-manually-configure-multiple-regions/20-add-ip-resource.png)

   Skonfiguruj ten adres IP w następujący sposób:

   - Użyj sieci z zdalnego centrum danych.
   - Przypisz adres IP z nowego modułu równoważenia obciążenia platformy Azure. 

1. Dodaj zasób adresu IP jako zależność dla klastra punktu dostępu klienta odbiornika (nazwy sieciowej).

   Poniższy zrzut ekranu przedstawia prawidłowo skonfigurowany zasób klastra adresów IP:

   ![Grupa dostępności](./media/availability-group-manually-configure-multiple-regions/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >Grupa zasobów klastra obejmuje oba adresy IP. Oba adresy IP są zależnościami od punktu dostępu klienta odbiornika. Użyj operatora **or** w konfiguracji zależności klastra.

1. [Ustaw parametry klastra w programie PowerShell](availability-group-manually-configure-tutorial.md#setparam).

   Uruchom skrypt programu PowerShell z nazwą sieci klastra, adresem IP i portem sondy skonfigurowanym w module równoważenia obciążenia w nowym regionie.

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = "<n.n.n.n>" # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

1. Na nowych SQL Server w SQL Server Configuration Manager [Włącz zawsze włączone grupy dostępności](/sql/database-engine/availability-groups/windows/enable-and-disable-always-on-availability-groups-sql-server).

1. [Otwórz porty zapory na nowym SQL Server](availability-group-manually-configure-prerequisites-tutorial.md#endpoint-firewall).

   Numery portów, które należy otworzyć, zależą od danego środowiska. Otwórz porty dla punktu końcowego dublowania i sondy kondycji modułu równoważenia obciążenia platformy Azure.


1. [Dodaj replikę do grupy dostępności na nowym SQL Server](/sql/database-engine/availability-groups/windows/use-the-add-replica-to-availability-group-wizard-sql-server-management-studio).

   W przypadku repliki w zdalnym regionie świadczenia usługi Azure ustaw ją na replikację asynchroniczną z ręcznym trybem failover.  

## <a name="set-connection-for-multiple-subnets"></a>Ustaw połączenie dla wielu podsieci

Replika w zdalnym centrum danych jest częścią grupy dostępności, ale znajduje się w innej podsieci. Jeśli replika zostanie repliką podstawową, mogą wystąpić limity czasu połączenia aplikacji. Takie zachowanie jest takie samo jak lokalna Grupa dostępności w ramach wdrożenia z obsługą kilku podsieci. Aby zezwolić na połączenia z aplikacji klienckich, zaktualizuj połączenie klienta lub skonfiguruj buforowanie rozpoznawania nazw dla zasobu Nazwa sieciowa klastra.

Najlepiej zaktualizować parametry połączenia klienta, aby ustawić `MultiSubnetFailover=Yes` . Zobacz [nawiązywanie połączenia z usługą MultiSubnetFailover](/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#Anchor_0).

Jeśli nie można zmodyfikować parametrów połączenia, można skonfigurować buforowanie rozpoznawania nazw. Zobacz [błąd limitu czasu i nie można nawiązać połączenia z odbiornikiem grupy dostępności funkcji AlwaysOn SQL Server 2012 w środowisku z obsługą kilku podsieci](https://support.microsoft.com/help/2792139/time-out-error-and-you-cannot-connect-to-a-sql-server-2012-alwayson-av).

## <a name="fail-over-to-remote-region"></a>Przechodzenie w tryb failover do zdalnego regionu

Aby przetestować łączność odbiornika z regionem zdalnym, można przełączyć replikę w tryb failover do regionu zdalnego. Gdy replika jest asynchroniczna, tryb failover jest narażony na potencjalną utratę danych. Aby przełączyć się do trybu failover bez utraty danych, Zmień tryb dostępności na synchroniczny i ustaw tryb pracy awaryjnej na automatyczny. Wykonaj następujące kroki:

1. W **Eksplorator obiektów** Połącz się z wystąpieniem SQL Server, które obsługuje replikę podstawową.
1. W obszarze **zawsze włączone grupy dostępności**, **grupy dostępności**, kliknij prawym przyciskiem myszy grupę dostępności, a następnie wybierz pozycję **Właściwości**.
1. Na stronie **Ogólne** w obszarze **repliki dostępności** Ustaw replikę pomocniczą w lokacji odzyskiwania w taki sposób, aby korzystała z **synchronicznego trybu dostępności zatwierdzania** i trybu **automatycznego** trybu failover.
1. Jeśli istnieje replika pomocnicza w tej samej lokacji co replika podstawowa w celu zapewnienia wysokiej dostępności, ustaw tę replikę na **asynchroniczne zatwierdzenie** i **ręcznie**.
1. Wybierz przycisk OK.
1. W **Eksplorator obiektów** kliknij prawym przyciskiem myszy grupę dostępności, a następnie wybierz pozycję **Pokaż pulpit nawigacyjny**.
1. Na pulpicie nawigacyjnym Sprawdź, czy replika w witrynie DR jest synchronizowana.
1. W **Eksplorator obiektów** kliknij prawym przyciskiem myszy grupę dostępności, a następnie wybierz pozycję **tryb failover...**. SQL Server Management Studios otwiera kreatora w celu przełączenia w tryb failover SQL Server.  
1. Wybierz pozycję **dalej**, a następnie wybierz wystąpienie SQL Server w witrynie Dr. Ponownie wybierz pozycję **dalej** .
1. Połącz się z wystąpieniem SQL Server w witrynie DR i wybierz pozycję **dalej**.
1. Na stronie **Podsumowanie** Sprawdź ustawienia i wybierz pozycję **Zakończ**.

Po przetestowaniu łączności należy przenieść replikę podstawową z powrotem do głównego centrum danych i ustawić tryb dostępności z powrotem na normalne ustawienia operacyjne. W poniższej tabeli przedstawiono normalne ustawienia operacyjne dla architektury opisanej w tym dokumencie:

| Lokalizacja | Wystąpienie serwera | Rola | Tryb dostępności | Tryb pracy awaryjnej
| ----- | ----- | ----- | ----- | -----
| Podstawowe centrum danych | SQL — 1 | Podstawowe | Synchronous | Automatyczny
| Podstawowe centrum danych | SQL — 2 | Pomocniczy | Synchronous | Automatyczny
| Pomocnicze lub zdalne centrum danych | SQL — 3 | Pomocniczy | Asynchroniczny | Ręcznie


### <a name="more-information-about-planned-and-forced-manual-failover"></a>Więcej informacji o planowanym i wymuszonym ręcznym przejściu w tryb failover

Aby uzyskać więcej informacji, zobacz następujące tematy:

- [Wykonaj zaplanowaną ręczną pracę awaryjną grupy dostępności (SQL Server)](/sql/database-engine/availability-groups/windows/perform-a-planned-manual-failover-of-an-availability-group-sql-server)
- [Wykonaj wymuszoną ręczną pracę awaryjną grupy dostępności (SQL Server)](/sql/database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server)

## <a name="next-steps"></a>Następne kroki

* [Zawsze włączone grupy dostępności](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)
* [Azure Virtual Machines](../../../virtual-machines/windows/index.yml)
* [Moduły równoważenia obciążenia platformy Azure](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer)
* [Zestawy dostępności platformy Azure](../../../virtual-machines/manage-availability.md)