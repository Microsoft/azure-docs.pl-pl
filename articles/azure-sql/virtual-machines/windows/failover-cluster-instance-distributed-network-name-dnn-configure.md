---
title: Konfigurowanie DNN dla wystąpienia klastra trybu failover
description: Dowiedz się, jak skonfigurować nazwę sieci rozproszonej (DNN) w celu kierowania ruchu do SQL Server w wystąpieniu klastra trybu failover maszyny wirtualnej platformy Azure (FCI).
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 8549592ace00e712929ebc76045a32531b9db659
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97358320"
---
# <a name="configure-a-dnn-for-failover-cluster-instance"></a>Konfigurowanie DNN dla wystąpienia klastra trybu failover
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Na platformie Azure Virtual Machines nazwa sieci rozproszonej (DNN) kieruje ruch do odpowiedniego zasobu klastra. Zapewnia łatwiejszy sposób nawiązywania połączenia z wystąpieniem klastra trybu failover SQL Server (FCI) niż nazwa sieci wirtualnej (VNN), bez konieczności Azure Load Balancer. 

W tym artykule przedstawiono sposób konfigurowania zasobu DNN w celu kierowania ruchu do wystąpienia klastra trybu failover z SQL Server na maszynach wirtualnych platformy Azure w celu zapewnienia wysokiej dostępności i odzyskiwania po awarii (HADR cluster). 

Funkcja DNN jest obecnie dostępna tylko na SQL Server 2019 ZASTOSUJESZ pakietu CU2 i nowszych w systemie Windows Server 2016 lub nowszym. 

W przypadku alternatywnej opcji łączności należy rozważyć [nazwę sieci wirtualnej i Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) zamiast tego. 

## <a name="overview"></a>Omówienie

Nazwa sieci rozproszonej (DNN) zastępuje nazwę sieci wirtualnej (VNN) jako punkt połączenia, gdy jest używany z [wystąpieniem klastra trybu failover z funkcją Always On na SQL Server maszynach wirtualnych](failover-cluster-instance-overview.md). Wyklucza to konieczność przesyłania Azure Load Balancer ruchem skierowanym do VNN, upraszczając wdrażanie, konserwację i ulepszanie trybu failover. 

W przypadku wdrożenia FCI nadal istnieje VNN, ale klient nawiązuje połączenie z nazwą DNS DNN zamiast nazwą VNN. 

## <a name="prerequisites"></a>Wymagania wstępne 

Przed wykonaniem kroków opisanych w tym artykule należy dysponować:

- SQL Server 2019 w ZASTOSUJESZ pakietu CU2 lub nowszym w systemie Windows Server 2016 lub nowszym
- Zdecyduj, że nazwa sieci rozproszonej jest odpowiednią [opcją łączności dla rozwiązania HADR Cluster](hadr-cluster-best-practices.md#connectivity).
- Skonfigurowano [wystąpienia klastra trybu failover](failover-cluster-instance-overview.md). 
- Zainstalowano najnowszą wersję programu [PowerShell](/powershell/azure/install-az-ps). 

## <a name="create-dnn-resource"></a>Utwórz zasób DNN 

Zasób DNN jest tworzony w tej samej grupie klastra co SQL Server FCI. Użyj programu PowerShell, aby utworzyć zasób DNN w grupie klastra FCI. 

Następujące polecenie programu PowerShell dodaje zasób DNN do grupy klastrów FCI SQL Server z nazwą zasobu `<dnnResourceName>` . Nazwa zasobu służy do unikatowego identyfikowania zasobu. Użyj jednego z nich, który ma sens i jest unikatowy w całym klastrze. Typem zasobu musi być `Distributed Network Name` . 

`-Group`Wartość musi być nazwą grupy klastra, która odpowiada SQL Server FCI, gdzie chcesz dodać nazwę sieci rozproszonej. W przypadku wystąpienia domyślnego typowym formatem jest `SQL Server (MSSQLSERVER)` . 


```powershell
Add-ClusterResource -Name <dnnResourceName> `
-ResourceType "Distributed Network Name" -Group "<WSFC role of SQL server instance>"
```

Na przykład, aby utworzyć zasób DNN `dnn-demo` dla domyślnego SQL Server FCI, użyj następującego polecenia programu PowerShell:

```powershell
Add-ClusterResource -Name dnn-demo `
-ResourceType "Distributed Network Name" -Group "SQL Server (MSSQLSERVER)"

```

## <a name="set-cluster-dnn-dns-name"></a>Ustaw nazwę DNS DNN klastra

W klastrze Ustaw nazwę DNS dla zasobu DNN. Klaster używa tej wartości do kierowania ruchu do węzła, w którym aktualnie znajduje się SQL Server FCI. 

Klienci używają nazwy DNS do łączenia się z SQL Server FCI. Możesz wybrać unikatową wartość. Lub, jeśli masz już istniejące FCI i nie chcesz aktualizować parametrów połączenia klienta, możesz skonfigurować DNN do korzystania z bieżącego VNN, które są już używane przez klientów. Aby to zrobić, należy [zmienić nazwę VNN](#rename-the-vnn) przed ustawieniem DNN w systemie DNS.

Użyj tego polecenia, aby ustawić nazwę DNS dla DNN: 

```powershell
Get-ClusterResource -Name <dnnResourceName> | `
Set-ClusterParameter -Name DnsName -Value <DNSName>
```

`DNSName`Wartość jest używana przez klientów do łączenia się z SQL Server FCI. Na przykład w celu nawiązania połączenia z klientami `FCIDNN` Użyj następującego polecenia programu PowerShell:

```powershell
Get-ClusterResource -Name dnn-demo | `
Set-ClusterParameter -Name DnsName -Value FCIDNN
```

Klienci będą teraz wprowadzać `FCIDNN` do parametrów połączenia podczas nawiązywania połączenia z SQL Server FCI. 

   > [!WARNING]
   > Nie usuwaj bieżącej nazwy sieci wirtualnej (VNN), ponieważ jest to niezbędny składnik infrastruktury FCI. 


### <a name="rename-the-vnn"></a>Zmień nazwę VNN 

Jeśli masz istniejącą nazwę sieci wirtualnej i chcesz, aby klienci nadal używali tej wartości do łączenia się z SQL Server FCI, musisz zmienić nazwę bieżącego VNN na wartość symbolu zastępczego. Po zmianie nazwy bieżącego VNN można ustawić wartość Nazwa DNS dla DNN na VNN. 

Niektóre ograniczenia dotyczą zmiany nazwy VNN. Aby uzyskać więcej informacji, zobacz [zmiana nazwy FCI](/sql/sql-server/failover-clusters/install/rename-a-sql-server-failover-cluster-instance).

Jeśli korzystanie z bieżącego VNN nie jest konieczne dla Twojej firmy, Pomiń tę sekcję. Po zmianie nazwy VNN wybierz pozycję [cluster DNN DNS](#set-cluster-dnn-dns-name). 

   
## <a name="set-dnn-resource-online"></a>Ustaw zasób DNN w trybie online

Gdy zasób DNN jest odpowiednio nazwany i ustawisz wartość nazwy DNS w klastrze, użyj programu PowerShell, aby ustawić zasób DNN w trybie online w klastrze: 

```powershell
Start-ClusterResource -Name <dnnResourceName>
```

Na przykład aby uruchomić zasób DNN `dnn-demo` , użyj następującego polecenia programu PowerShell: 

```powershell
Start-ClusterResource -Name dnn-demo
```

## <a name="configure-possible-owners"></a>Konfigurowanie możliwych właścicieli

Domyślnie klaster wiąże nazwę DNS DNN z wszystkimi węzłami w klastrze. Jednak węzły w klastrze, które nie są częścią SQL Server FCI, powinny być wykluczone z listy możliwych właścicieli DNN. 

Aby zaktualizować możliwych właścicieli, wykonaj następujące kroki:

1. Przejdź do zasobu DNN w Menedżer klastra trybu failover. 
1. Kliknij prawym przyciskiem myszy zasób DNN i wybierz pozycję **Właściwości**. 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/fci-dnn-properties.png" alt-text="Menu skrótów dla zasobu DNN z wyróżnionym poleceniem właściwości.":::

1. Usuń zaznaczenie pola wyboru dla wszystkich węzłów, które nie uczestniczą w wystąpieniu klastra trybu failover. Lista możliwych właścicieli zasobu DNN powinna być zgodna z listą możliwych właścicieli zasobu wystąpienia SQL Server. Na przykład przy założeniu, że DATA3 nie uczestniczy w FCI, na poniższej ilustracji przedstawiono przykład usunięcia DATA3 z listy możliwych właścicieli dla zasobu DNN: 

   :::image type="content" source="media/hadr-distributed-network-name-dnn-configure/clear-check-for-nodes-not-in-fci.png" alt-text="Wyczyść pole wyboru obok węzłów, które nie uczestniczą w FCI dla możliwych właścicieli zasobu DNN":::

1. Wybierz przycisk **OK**, aby zapisać ustawienia. 


## <a name="restart-sql-server-instance"></a>Uruchom ponownie wystąpienie SQL Server 

Użyj Menedżer klastra trybu failover, aby ponownie uruchomić wystąpienie SQL Server. Wykonaj następujące kroki:

1. Przejdź do zasobu SQL Server w Menedżer klastra trybu failover.
1. Kliknij prawym przyciskiem myszy zasób SQL Server i przełącz go w tryb offline. 
1. Gdy wszystkie skojarzone zasoby są w trybie offline, kliknij prawym przyciskiem myszy zasób SQL Server i ponownie przełącz go do trybu online. 

## <a name="update-connection-string"></a>Zaktualizuj parametry połączenia

Aby zapewnić szybką łączność po przejściu w tryb failover, należy dodać `MultiSubnetFailover=True` do parametrów połączenia, jeśli wersja klienta SQL jest wcześniejsza niż 4.6.1. 

Ponadto jeśli DNN nie używa oryginalnych VNN, klienci SQL, którzy łączą się z SQL Server FCI, będą musieli zaktualizować parametry połączenia do nazwy DNS DNN. Aby uniknąć tego wymagania, można zaktualizować wartość nazwy DNS jako nazwę VNN. Ale musisz najpierw [zastąpić istniejący VNN symbolem zastępczym](#rename-the-vnn) . 

## <a name="test-failover"></a>Testowanie pracy w trybie failover


Przetestuj tryb failover zasobu klastra, aby sprawdzić poprawność funkcjonalności klastra. 


Aby przetestować tryb failover, wykonaj następujące kroki: 

1. Połącz się z jednym z SQL Server węzłów klastra przy użyciu protokołu RDP.
1. Otwórz **Menedżer klastra trybu failover**. Wybierz pozycję **role**. Zwróć uwagę na to, który węzeł jest właścicielem SQL Server roli FCI.
1. Kliknij prawym przyciskiem myszy SQL Server rolę FCI. 
1. Wybierz pozycję **Przenieś**, a następnie wybierz pozycję **najlepszy możliwy węzeł**.

**Menedżer klastra trybu failover** pokazuje rolę i jej zasoby przechodzą do trybu offline. Zasoby zostaną następnie przeniesione i przywrócone do trybu online w innym węźle.

## <a name="test-connectivity"></a>Testowanie łączności

Aby przetestować łączność, zaloguj się do innej maszyny wirtualnej w tej samej sieci wirtualnej. Otwórz **SQL Server Management Studio** i Połącz się z SQL Server FCI przy użyciu nazwy DNS DNN.

Jeśli zachodzi taka potrzeba, można [pobrać SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="avoid-ip-conflict"></a>Unikaj konfliktu adresów IP

Jest to opcjonalny krok, aby zapobiec przypisaniu wirtualnego adresu IP (VIP) używanego przez zasób FCI do innego zasobu na platformie Azure jako duplikatu. 

Mimo że klienci używają teraz DNN do nawiązywania połączenia z SQL Server FCI, nazwa sieci wirtualnej (VNN) i wirtualny adres IP nie mogą zostać usunięte, ponieważ są to niezbędne składniki infrastruktury FCI. Jednak ponieważ nie istnieje już moduł równoważenia obciążenia z zachowaniem wirtualnego adresu IP na platformie Azure, istnieje ryzyko, że do innego zasobu w sieci wirtualnej zostanie przypisany ten sam adres IP, co wirtualny adres IP używany przez FCI. Może to potencjalnie prowadzić do zduplikowanego problemu z konfliktem adresów IP. 

Skonfiguruj adres IP lub dedykowaną kartę sieciową w celu zarezerwowania adresu. 

### <a name="apipa-address"></a>Adres APIPA

Aby uniknąć używania zduplikowanych adresów IP, należy skonfigurować adres APIPA (nazywany również adresem lokalnym linku). W tym celu uruchom następujące polecenie:

```powershell
Get-ClusterResource "virtual IP address" | Set-ClusterParameter 
    –Multiple @{"Address”=”169.254.1.1”;”SubnetMask”=”255.255.0.0”;"OverrideAddressMatch"=1;”EnableDhcp”=0}
```

W tym poleceniu "wirtualny adres IP" jest nazwą klastrowanego adresu VIP, a "169.254.1.1" jest adresem APIPA wybranym dla adresu VIP. Wybierz adres najlepiej pasujący do Twojej firmy. Ustaw `OverrideAddressMatch=1` , aby zezwalać na adres IP w dowolnych sieciach, w tym w przestrzeni adresowej APIPA. 

### <a name="dedicated-network-adapter"></a>Dedykowana karta sieciowa

Alternatywnie można skonfigurować kartę sieciową na platformie Azure w celu zarezerwowania adresu IP używanego przez zasób wirtualnego adresu IP. Jednak spowoduje to użycie adresu w przestrzeni adresowej podsieci i wiąże się z dodatkowymi kosztami zagwarantowania, że karta sieciowa nie jest używana do żadnych innych celów.

## <a name="limitations"></a>Ograniczenia

- Obecnie DNN z FCI jest obsługiwana tylko dla SQL Server 2019 ZASTOSUJESZ pakietu CU2 i nowszych w systemie Windows Server 2016 lub nowszym. 
- Podczas pracy z innymi funkcjami SQL Server i FCI za pomocą DNN może wystąpić więcej zagadnień. Aby uzyskać więcej informacji, zobacz [FCI with DNN współdziałanie](failover-cluster-instance-dnn-interoperability.md). 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat SQL Server funkcji HADR Cluster na platformie Azure, zobacz [grupy dostępności](availability-group-overview.md) i [wystąpienie klastra trybu failover](failover-cluster-instance-overview.md). Możesz również poznać [najlepsze rozwiązania](hadr-cluster-best-practices.md) dotyczące konfigurowania środowiska pod kątem wysokiej dostępności i odzyskiwania po awarii. 


