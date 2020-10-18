---
title: Przegląd zawsze dostępnych grup dostępności SQL Server
description: W tym artykule wprowadzono SQL Server zawsze włączonymi grupami dostępności na platformie Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.custom: seo-lt-2019, devx-track-azurecli
ms.openlocfilehash: 26d4080e20fb8d00ec4d276e56e09170001d2b8e
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2020
ms.locfileid: "92166543"
---
# <a name="always-on-availability-group-on-sql-server-on-azure-vms"></a>Zawsze włączona Grupa dostępności na SQL Server na maszynach wirtualnych platformy Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W tym artykule wprowadzono zawsze włączone grupy dostępności dla SQL Server na platformie Azure Virtual Machines. 

## <a name="overview"></a>Omówienie

Zawsze włączone grupy dostępności na platformie Azure Virtual Machines są podobne do [zawsze lokalnych grup dostępności](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server). Jednak ze względu na to, że maszyny wirtualne są hostowane na platformie Azure, istnieje kilka dodatkowych zagadnień, takich jak nadmiarowość maszyn wirtualnych i routing ruchu w sieci platformy Azure. 

Na poniższym diagramie przedstawiono grupę dostępności dla SQL Server na maszynach wirtualnych platformy Azure:

![Grupa dostępności](./media/availability-group-overview/00-EndstateSampleNoELB.png)


## <a name="vm-redundancy"></a>Nadmiarowość maszyny wirtualnej 

Aby zwiększyć nadmiarowość i wysoką dostępność, SQL Server maszyny wirtualne powinny znajdować się w tym samym [zestawie dostępności](../../../virtual-machines/windows/tutorial-availability-sets.md#availability-set-overview)lub w różnych [strefach dostępności](/azure/availability-zones/az-overview).

Zestaw dostępności to grupa zasobów, które są skonfigurowane tak, aby nie były dwa tereny w tej samej strefie dostępności. Zapobiega to wpływowi wielu zasobów w grupie podczas wdrażania. 


## <a name="connectivity"></a>Łączność 

W tradycyjnym wdrożeniu lokalnym klienci nawiązują połączenie z odbiornikiem grupy dostępności przy użyciu nazwy sieci wirtualnej (VNN), a odbiornik kieruje ruch do odpowiedniej repliki SQL Server w grupie dostępności. Istnieje jednak dodatkowy wymóg kierowania ruchu w sieci platformy Azure. 

W SQL Server na maszynach wirtualnych platformy Azure Skonfiguruj [moduł równoważenia obciążenia](availability-group-vnn-azure-load-balancer-configure.md) , aby kierować ruchem do odbiornika grupy dostępności, lub jeśli korzystasz z SQL Server 2019 CU8 i nowszych, możesz skonfigurować [odbiornik nazwy sieci rozproszonej (DNN)](availability-group-distributed-network-name-dnn-listener-configure.md) w celu zastąpienia odbiornika tradycyjnej grupy dostępności VNN. 


### <a name="vnn-listener"></a>Odbiornik VNN 

Użyj [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) , aby skierować ruch z klienta do tradycyjnego odbiornika nazw sieci wirtualnej (VNN) w sieci platformy Azure. 

Moduł równoważenia obciążenia przechowuje adresy IP dla odbiornika VNN. Jeśli masz więcej niż jedną grupę dostępności, każda grupa wymaga odbiornika VNN. Jeden moduł równoważenia obciążenia może obsługiwać wiele odbiorników.

Aby rozpocząć, zobacz [Konfigurowanie modułu równoważenia obciążenia](availability-group-vnn-azure-load-balancer-configure.md). 

### <a name="dnn-listener"></a>Odbiornik DNN

SQL Server 2019 CU8 wprowadza obsługę odbiornika nazwy sieci rozproszonej (DNN). Odbiornik DNN zastępuje tradycyjną funkcję odbiornika grupy dostępności, negację modułu równoważenia obciążenia platformy Azure, aby kierować ruchem w sieci platformy Azure. 

Odbiornik DNN jest zalecanym rozwiązaniem łączności HADR Cluster na platformie Azure, ponieważ upraszcza wdrażanie, zmniejsza konserwację i koszty oraz zmniejsza czas pracy w trybie failover w przypadku awarii. 

Użyj odbiornika DNN, aby zastąpić istniejący odbiornik VNN lub użyć go w połączeniu z istniejącym odbiornikiem VNN, dzięki czemu Grupa dostępności ma dwa odrębne punkty połączenia — jeden przy użyciu nazwy odbiornika VNN (i portu, jeśli nie jest to ustawienie domyślne), a drugi przy użyciu nazwy i portu odbiornika DNN. Może to być przydatne w przypadku klientów, którzy chcą uniknąć opóźnień trybu failover modułu równoważenia obciążenia, ale nadal korzystają z funkcji SQL Server, które są zależne od odbiornika VNN, takich jak rozproszone grupy dostępności, Service Broker lub FILESTREAM. Aby dowiedzieć się więcej, zobacz [odbiornik DNN i współdziałanie funkcji SQL Server](availability-group-dnn-interoperability.md)

Aby rozpocząć, zobacz [Konfigurowanie odbiornika DNN](availability-group-distributed-network-name-dnn-listener-configure.md).


## <a name="deployment"></a>Wdrożenie 

Istnieje wiele opcji wdrażania grupy dostępności do SQL Server na maszynach wirtualnych platformy Azure, a w przypadku większej automatyzacji niż inne. 

Poniższa tabela zawiera porównanie dostępnych opcji: 

| |**[Azure Portal](availability-group-azure-portal-configure.md)**|**[Interfejs wiersza polecenia platformy Azure/PowerShell](availability-group-az-cli-configure.md)**|**[Szablony szybkiego startu](availability-group-quickstart-template-configure.md)**|**[Ręczne](availability-group-manually-configure-prerequisites-tutorial.md)** | 
|---------|---------|---------|--------- |---------|
|**Wersja programu SQL Server** |2016 + |2016 +|2016 +|2012 +|
|**Wydanie programu SQL Server** |Enterprise |Enterprise |Enterprise |Enterprise, standard|
|**Wersja systemu Windows Server**| 2016 + | 2016 + | 2016 + | Wszystko| 
|**Tworzy dla Ciebie klaster**|Tak|Tak | Tak |Nie|
|**Tworzy grupę dostępności dla Ciebie** |Tak |Nie|Nie|Nie|
|**Tworzy odbiornik i moduł równoważenia obciążenia niezależnie** |Nie|Nie|Nie|Tak|
|**Czy możliwe jest utworzenie odbiornika DNN przy użyciu tej metody?**|Nie|Nie|Nie|Tak|
|**Konfiguracja kworum usługi WSFC**n|Monitor w chmurze|Monitor w chmurze|Monitor w chmurze|Wszystko|
|**Odzyskiwanie po awarii z wieloma regionami** |Nie|Nie|Nie|Tak|
|**Obsługa wielosieciowa** |Tak|Tak|Tak|Tak|
|**Obsługa istniejącej usługi AD**|Tak|Tak|Tak|Tak|
|**Odzyskiwanie po awarii za pomocą wielostrefy w tym samym regionie**|Tak|Tak|Tak|Tak|
|**Distributed AG bez usługi AD**|Nie|Nie|Nie|Tak|
|**Dystrybucja AG bez klastra** |Nie|Nie|Nie|Tak|
||||||



## <a name="considerations"></a>Zagadnienia do rozważenia 

W klastrze trybu failover gościa maszyny wirtualnej IaaS platformy Azure zaleca się korzystanie z jednej karty sieciowej na serwerze (w węźle klastra) i pojedynczej podsieci. Sieć platformy Azure ma fizyczną nadmiarowość, co sprawia, że dodatkowe karty sieciowe i podsieci nie są potrzebne w klastrze gościa maszyny wirtualnej Azure IaaS. Mimo że raport z weryfikacji klastra wyświetli ostrzeżenie, że węzły są dostępne tylko w ramach jednej sieci, to ostrzeżenie można zignorować w klastrach trybu failover gościa maszyny wirtualnej IaaS platformy Azure. 

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [najlepszymi rozwiązaniami HADR Cluster](hadr-cluster-best-practices.md) , a następnie Rozpocznij pracę z wdrażaniem grupy dostępności przy użyciu [Azure Portal](availability-group-azure-portal-configure.md), [interfejsu wiersza polecenia platformy Azure/programu PowerShell](availability-group-az-cli-configure.md), [szablonów szybkiego startu](availability-group-quickstart-template-configure.md) lub [ręcznie](availability-group-manually-configure-prerequisites-tutorial.md).

Alternatywnie można wdrożyć [grupę dostępności bezklastrową](availability-group-clusterless-workgroup-configure.md) lub grupę dostępności w [wielu regionach](availability-group-manually-configure-multiple-regions.md). 
