---
title: Rozmiary maszyn wirtualnych platformy Azure — magazyn | Microsoft Docs
description: Wyświetla różne rozmiary zoptymalizowane pod kątem magazynowania dostępne dla maszyn wirtualnych na platformie Azure. Wyświetla listę informacji o liczbie procesorów wirtualnych vCPU, dyskach danych i kartach sieciowych, a także przepływności magazynu i przepustowości sieci dla rozmiarów w tej serii.
services: virtual-machines
documentationcenter: ''
author: sasha-melamed
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jonbeck
ms.openlocfilehash: 5a611dc288fc18a14f6000689f9f9b49d4b3feb8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77913340"
---
# <a name="storage-optimized-virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych zoptymalizowane pod kątem magazynu

Rozmiary maszyn wirtualnych zoptymalizowane pod kątem magazynu oferują wysoką przepływność dysku i operacje we/wy, a doskonale nadają się do obsługi danych Big Data, bazy danych SQL, NoSQL, magazynowania danych i dużych transakcyjnych baz danych.  Przykłady obejmują Cassandra, MongoDB, Cloudera i Redis. Ten artykuł zawiera informacje o liczbie procesorów wirtualnych vCPU, dyskach danych i kartach sieciowych oraz o przepływności lokalnego magazynu i przepustowości sieci dla każdego zoptymalizowanego rozmiaru.

Funkcja [serii Lsv2](lsv2-series.md) ma wysoką przepływność, małe opóźnienia, bezpośrednio mapowane lokalne magazyny interfejsu NVMe działające na [procesorze AMD EPYC<sup>TM</sup> 7551](https://www.amd.com/en/products/epyc-7000-series) i wszystkie podstawowe usprawnienia 2.55 GHz oraz maksymalne zwiększenie wydajności 3,0 GHz. Maszyny wirtualne serii Lsv2 mają rozmiary od 8 do 80 vCPU w jednoczesnej konfiguracji wielowątkowej.  Istnieje 8 GiB pamięci na vCPU i jeden dysk 1.92 TB interfejsu NVMe o pojemności M. 2 na 8 procesorów wirtualnych vCPU, z maksymalnie 19,2 TB (10X 1.92 TB) dostępnej w L80s v2.

## <a name="other-sizes"></a>Inne rozmiary

- [Zastosowania ogólne](sizes-general.md)
- [Optymalizacja pod kątem obliczeń](sizes-compute.md)
- [Optymalizacja pod kątem pamięci](sizes-memory.md)
- [Optymalizacja pod kątem procesora GPU](sizes-gpu.md)
- [Obliczenia o wysokiej wydajności](sizes-hpc.md)
- [Poprzednie generacje](sizes-previous-gen.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak [usługa Azure COMPUTE units (ACU)](acu.md) może pomóc w porównaniu wydajności obliczeniowej w ramach jednostek SKU platformy Azure.

Dowiedz się, jak zoptymalizować wydajność maszyn wirtualnych z serii Lsv2 dla [systemu Windows](windows/storage-performance.md) lub [Linux](linux/storage-performance.md).
