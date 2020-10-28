---
title: Uruchamianie SQL Server VM na dedykowanym hoście platformy Azure
description: Dowiedz się, jak uruchomić maszynę wirtualną SQL Server na dedykowanym hoście platformy Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/12/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: bb80abc35aedcdf0b46cefa279e477739cf1df6b
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92789798"
---
# <a name="run-sql-server-vm-on-an-azure-dedicated-host"></a>Uruchamianie SQL Server VM na dedykowanym hoście platformy Azure 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W tym artykule szczegółowo opisano, jak używać maszyny wirtualnej SQL Server z [dedykowanym hostem platformy Azure](../../../virtual-machines/dedicated-hosts.md). Dodatkowe informacje na temat dedykowanego hosta platformy Azure można znaleźć w wpisie w blogu [wprowadzającym dedykowany Host platformy Azure](https://azure.microsoft.com/blog/introducing-azure-dedicated-host/). 

## <a name="overview"></a>Omówienie
[Dedykowany Host platformy Azure](../../../virtual-machines/dedicated-hosts.md) to usługa, która zapewnia serwerom fizycznym możliwość hostowania co najmniej jednej maszyny wirtualnej — dedykowanej dla jednej subskrypcji platformy Azure. Dedykowane hosty to te same serwery fizyczne, które są używane w centrach danych firmy Microsoft, dostępne jako zasoby. Można udostępnić dedykowane hosty w obrębie regionu, strefy dostępności i domeny błędów. Następnie można umieścić maszyny wirtualne bezpośrednio na hostach, w których konfiguracja najlepiej odpowiada Twoim potrzebom.

## <a name="limitations"></a>Ograniczenia

- Nie wszystkie serie maszyn wirtualnych są obsługiwane na dedykowanych hostach, a dostępność serii maszyn wirtualnych zależy od regionu. Aby uzyskać więcej informacji, zobacz [Omówienie dedykowanych hostów platformy Azure](../../../virtual-machines/dedicated-hosts.md).

## <a name="licensing"></a>Licencjonowanie

Możesz wybrać jedną z dwóch różnych opcji licencjonowania, gdy umieścisz SQL Server maszynę wirtualną na dedykowanym hoście platformy Azure. 

  - **Licencjonowanie maszyn wirtualnych SQL** : jest to istniejąca opcja licencjonowania, w przypadku której płacisz osobno za każdą licencję na SQL Server maszynę wirtualną. 
  - **Dedykowane Licencjonowanie hosta** : nowy model licencjonowania dostępny dla dedykowanego hosta platformy Azure, w którym SQL Server licencje są powiązane i płatne na poziomie hosta. 


Opcje na poziomie hosta dotyczące korzystania z istniejących licencji SQL Server: 
  - Korzyść użycia hybrydowego platformy Azure SQL Server Enterprise Edition (AHB)
    - Dostępne dla klientów z pakietem SA lub subskrypcją.
    - Licencjonowanie wszystkich dostępnych rdzeni fizycznych i korzystanie z nieograniczonej wirtualizacji (maksymalnie procesorów wirtualnych vCPU obsługiwane przez hosta).
        - Aby uzyskać więcej informacji na temat stosowania AHB do dedykowanego hosta platformy Azure, zobacz [korzyść użycia hybrydowego platformy Azure często zadawane pytania](https://azure.microsoft.com/pricing/hybrid-benefit/faq/). 
  - Licencje SQL Server nabyte przed 1 października
      - Wersja SQL Server Enterprise ma opcje licencji na poziomie hosta i przez maszynę wirtualną. 
      - Wersja SQL Server Standard ma tylko opcję licencji na maszynę wirtualną. 
          - Aby uzyskać szczegółowe informacje, zobacz [warunki produktu firmy Microsoft](https://www.microsoft.com/licensing/product-licensing/products). 
  - Jeśli nie wybrano SQL Server dedykowanej opcji na poziomie hosta, możesz wybrać pozycję SQL Server AHB na poziomie poszczególnych maszyn wirtualnych, tak jak w przypadku maszyn wirtualnych z wieloma dzierżawcami.



## <a name="provisioning"></a>Inicjowanie obsługi  
Inicjowanie obsługi maszyny wirtualnej SQL Server na dedykowanym hoście nie różni się od żadnej innej maszyny wirtualnej platformy Azure. Można to zrobić za pomocą [Azure PowerShell](../../../virtual-machines/windows/dedicated-hosts-powershell.md), [Azure Portal](../../../virtual-machines/dedicated-hosts-portal.md)i [interfejsu wiersza polecenia platformy Azure](../../../virtual-machines/linux/dedicated-hosts-cli.md).

Proces dodawania istniejącej maszyny wirtualnej SQL Server do dedykowanego hosta wymaga przestoju, ale nie wpłynie na dane i nie będzie miał utraty danych. Niemniej jednak przed przeniesieniem należy utworzyć kopię zapasową wszystkich baz danych, w tym systemowych baz danych.

## <a name="virtualization"></a>Wirtualizacja 

Jedną z zalet dedykowanego hosta jest nieograniczona wirtualizacja. Na przykład możesz mieć licencje na 64 rdzeni wirtualnych, ale można skonfigurować hosta tak, aby miał 128 rdzeni wirtualnych, więc otrzymujesz podwójny rdzeni wirtualnych, ale płacisz tylko połowę tego, co SQL Server licencji. 

Ponieważ jest to Twój host, kwalifikujesz się do ustawienia wirtualizacji o współczynniku 1:2. 

## <a name="faq"></a>Często zadawane pytania

**P: jak działa Korzyść użycia hybrydowego platformy Azure dla licencji systemu Windows Server/SQL Server na dedykowanym hoście platformy Azure?**

Odp.: klienci mogą korzystać z wartości swoich istniejących licencji systemu Windows Server i SQL Server z pakietem Software Assurance lub uprawniających licencji subskrypcyjnych, aby uregulować obniżoną stawkę za dedykowanego hosta platformy Azure przy użyciu Korzyść użycia hybrydowego platformy Azure. Klienci korzystający z systemu Windows Server Datacenter i SQL Server Enterprise Edition uzyskują nieograniczoną wirtualizację (w zależności od liczby maszyn wirtualnych z systemem Windows Server można wdrożyć na hoście z uwzględnieniem fizycznej pojemności serwera bazowego), gdy uzyskują licencję na cały Host i używają Korzyść użycia hybrydowego platformy Azure.  Wszystkie obciążenia systemu Windows Server i SQL Server na dedykowanym hoście platformy Azure są również uprawnione do rozszerzonych aktualizacji zabezpieczeń dla systemów Windows Server i SQL Server 2008/R2 bez dodatkowych opłat. 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz następujące artykuły: 

* [Omówienie SQL Server na maszynie wirtualnej z systemem Windows](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Często zadawane pytania dotyczące SQL Server na maszynie wirtualnej z systemem Windows](frequently-asked-questions-faq.md)
* [Wskazówki dotyczące cen dla SQL Server na maszynie wirtualnej z systemem Windows](pricing-guidance.md)
* [Informacje o wersji SQL Server na maszynie wirtualnej z systemem Windows](doc-changes-updates-release-notes.md)