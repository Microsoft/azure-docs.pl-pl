---
title: Przegląd grup dostępności
description: W tym artykule wprowadzono SQL Server grup dostępności na maszynach wirtualnych platformy Azure.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 601eebb1-fc2c-4f5b-9c05-0e6ffd0e5334
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/13/2017
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 62dce0204f77ab65473fc1735015e41f483dddb1
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84049134"
---
# <a name="introducing-sql-server-availability-groups-on-azure-virtual-machines"></a>Wprowadzenie SQL Server grup dostępności na maszynach wirtualnych platformy Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

W tym artykule wprowadzono SQL Server grup dostępności na platformie Azure Virtual Machines. 

Zawsze włączone grupy dostępności na platformie Azure Virtual Machines są podobne do zawsze dostępnych grup dostępności w środowisku lokalnym. Aby uzyskać więcej informacji, zobacz [Always On Availability groups (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx). 

Diagram ilustruje części kompletnej SQL Server grupy dostępności na platformie Azure Virtual Machines.

![Grupa dostępności](./media/availability-group-overview/00-EndstateSampleNoELB.png)

Kluczową różnicą dla grupy dostępności na platformie Azure Virtual Machines jest to, że maszyny wirtualne platformy Azure wymagają [modułu równoważenia obciążenia](../../../load-balancer/load-balancer-overview.md). Moduł równoważenia obciążenia przechowuje adresy IP dla odbiornika grupy dostępności. Jeśli masz więcej niż jedną grupę dostępności, każda grupa wymaga odbiornika. Jeden moduł równoważenia obciążenia może obsługiwać wiele odbiorników.

Ponadto w klastrze trybu failover gościa maszyny wirtualnej Azure IaaS zalecamy pojedyncze karty sieciowe na serwer (węzeł klastra) i jedną podsieć. Sieć platformy Azure ma fizyczną nadmiarowość, co sprawia, że dodatkowe karty sieciowe i podsieci są niepotrzebne w klastrze gościa maszyny wirtualnej IaaS platformy Azure. Mimo że raport z weryfikacji klastra wyświetli ostrzeżenie, że węzły są dostępne tylko w ramach jednej sieci, to ostrzeżenie można zignorować w klastrach trybu failover gościa maszyny wirtualnej IaaS platformy Azure. 

Aby zwiększyć nadmiarowość i wysoką dostępność, SQL Server maszyny wirtualne powinny znajdować się w tym samym [zestawie dostępności](availability-group-manually-configure-prerequisites-tutorial.md#create-availability-sets)lub w różnych [strefach dostępności](/azure/availability-zones/az-overview). 

|  | Wersja systemu Windows Server | Wersja SQL Server | SQL Server Edition | Konfiguracja kworum usługi WSFC | Odzyskiwanie po awarii z obsługą wieloregionu | Obsługa wielopodsieci | Obsługa istniejącej usługi AD | Odzyskiwanie po awarii z wielostrefowym regionem | Obsługa funkcji ROZKŁ-AG bez domeny usługi AD | Obsługa funkcji ROZKŁ-AG bez klastrów |  
| :------ | :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----| :-----|
| [INTERFEJS WIERSZA POLECENIA MASZYNY WIRTUALNEJ SQL](availability-group-az-cli-configure.md) | 2016 | 2017 </br>2016   | Enterprise | Monitor w chmurze | Nie | Yes | Tak | Yes | Nie | Nie |
| [Szablony szybkiego startu](availability-group-quickstart-template-configure.md) | 2016 | 2017</br>2016  | Enterprise | Monitor w chmurze | Nie | Yes | Tak | Yes | Nie | Nie |
| [Szablon portalu](availability-group-azure-marketplace-template-configure.md) | 2016 </br>2012 R2 | 2016</br>2014 | Enterprise | Udział plików | Nie | Nie | Nie | Nie | Nie | Nie |
| [Ręczne](availability-group-manually-configure-prerequisites-tutorial.md) | Wszystko | Wszystko | Wszystko | Wszystko | Tak | Tak | Tak | Tak | Tak | Tak |
| &nbsp; | &nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |&nbsp; |

Gdy wszystko będzie gotowe do tworzenia grupy dostępności SQL Server na platformie Azure Virtual Machines, zapoznaj się z tymi samouczkami.

## <a name="manually-with-azure-cli"></a>Ręczne przy użyciu interfejsu wiersza polecenia platformy Azure
Aby skonfigurować i wdrożyć grupę dostępności przy użyciu interfejsu wiersza polecenia platformy Azure, jest to zalecana opcja, co jest najlepszym rozwiązaniem w zakresie uproszczenia i przyspieszenia wdrożenia. Korzystając z interfejsu wiersza polecenia platformy Azure, można utworzyć klaster trybu failover systemu Windows, przyłączać SQL Server maszyny wirtualne do klastra, a także utworzyć odbiornik i wewnętrzne Load Balancer, w ciągu 30 minut. Ta opcja nadal wymaga ręcznego utworzenia grupy dostępności, ale automatyzuje wszystkie inne niezbędne czynności konfiguracyjne. 

Aby uzyskać więcej informacji, zobacz [Używanie interfejsu wiersza polecenia usługi Azure SQL VM do konfigurowania zawsze włączonych grup dostępności dla SQL Server na maszynie wirtualnej platformy Azure](availability-group-az-cli-configure.md). 

## <a name="automatically-with-azure-quickstart-templates"></a>Automatycznie dzięki szablonom szybkiego startu platformy Azure
Szablony szybkiego startu platformy Azure używają dostawcy zasobów maszyny wirtualnej SQL do wdrożenia klastra trybu failover systemu Windows, dołączania do niego SQL Server maszyn wirtualnych, tworzenia odbiornika i konfigurowania wewnętrznego Load Balancer. Ta opcja nadal wymaga ręcznego utworzenia grupy dostępności i wewnętrznego Load Balancer (ILB), ale automatyzuje i upraszcza wszystkie inne niezbędne czynności konfiguracyjne (w tym konfiguracja ILB). 

Aby uzyskać więcej informacji, zobacz [Korzystanie z szablonu szybkiego startu platformy Azure w celu skonfigurowania zawsze włączone grupy dostępności dla SQL Server na maszynie wirtualnej platformy Azure](availability-group-quickstart-template-configure.md).


## <a name="automatically-with-an-azure-portal-template"></a>Automatycznie przy użyciu szablonu Azure Portal

[Automatycznie Konfiguruj grupę dostępności na maszynie wirtualnej platformy Azure Menedżer zasobów](availability-group-azure-marketplace-template-configure.md)


## <a name="manually-in-azure-portal"></a>Ręcznie w Azure Portal

Możesz również samodzielnie tworzyć maszyny wirtualne bez szablonu. Najpierw należy spełnić wymagania wstępne, a następnie utworzyć grupę dostępności. Zobacz następujące tematy: 

- [Skonfiguruj wymagania wstępne dla SQL Server zawsze włączonymi grupami dostępności na platformie Azure Virtual Machines](availability-group-manually-configure-prerequisites-tutorial.md)

- [Utwórz zawsze włączona Grupa dostępności, aby zwiększyć dostępność i odzyskiwanie po awarii](availability-group-manually-configure-tutorial.md)

## <a name="next-steps"></a>Następne kroki

[SQL Server Skonfiguruj zawsze dostępną grupę dostępności na platformie Azure Virtual Machines w różnych regionach](availability-group-manually-configure-multiple-regions.md)
