---
title: Azure Premium Storage — projektowanie pod kątem wysokiej wydajności
description: Projektuj aplikacje o wysokiej wydajności przy użyciu dysków zarządzanych w usłudze Azure Premium SSD. Premium Storage oferuje wysoką wydajność, obsługę dysków o niskich opóźnieniach dla obciążeń intensywnie korzystających z operacji we/wy działających na platformie Azure Virtual Machines.
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 06/27/2017
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 46c917a2d27f5efaa1e902db0d5da5375578b8b8
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86526043"
---
# <a name="azure-premium-storage-design-for-high-performance"></a>Azure Premium Storage: projektowanie pod kątem wysokiej wydajności
[!INCLUDE [virtual-machines-common-premium-storage-introduction](../../../includes/virtual-machines-common-premium-storage-introduction.md)]

> [!NOTE]
> Czasami problem z wydajnością dysku to w rzeczywistości wąskie gardło sieci. W takich sytuacjach należy zoptymalizować [wydajność sieci](../../virtual-network/virtual-network-optimize-network-bandwidth.md).
>
> Jeśli chcesz przeprowadzić test porównawczy dysku, zapoznaj się z artykułem dotyczącym [tworzenia wzorców na dysku](disks-benchmarks.md).
>
> Jeśli maszyna wirtualna obsługuje przyspieszone sieci, należy upewnić się, że jest ona włączona. Jeśli nie jest włączona, można ją włączyć dla już wdrożonych maszyn wirtualnych w [systemach Windows](../../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) i [Linux](../../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

Przed rozpoczęciem, jeśli dopiero zaczynasz Premium Storage, najpierw Przeczytaj artykuł [Wybieranie typu dysku platformy Azure dla maszyn wirtualnych IaaS](disks-types.md) i [elementów docelowych skalowalności dla kont usługi BLOB Storage na stronie Premium](../../storage/blobs/scalability-targets-premium-page-blobs.md).

[!INCLUDE [virtual-machines-common-premium-storage-performance.md](../../../includes/virtual-machines-common-premium-storage-performance.md)]

Jeśli chcesz przeprowadzić test porównawczy dysku, zapoznaj się z artykułem dotyczącym [tworzenia wzorców na dysku](disks-benchmarks.md).

Dowiedz się więcej na temat dostępnych typów dysków: [Wybierz typ dysku](disks-types.md)  

Aby uzyskać SQL Server użytkowników, przeczytaj artykuły dotyczące najlepszych rozwiązań w zakresie wydajności dla SQL Server:

* [Najlepsze rozwiązania dotyczące wydajności SQL Server na platformie Azure Virtual Machines](../../azure-sql/virtual-machines/windows/performance-guidelines-best-practices.md)
* [Usługa Azure Premium Storage zapewnia najwyższą wydajność SQL Server na maszynie wirtualnej platformy Azure](https://cloudblogs.microsoft.com/sqlserver/2015/04/23/azure-premium-storage-provides-highest-performance-for-sql-server-in-azure-vm/)
