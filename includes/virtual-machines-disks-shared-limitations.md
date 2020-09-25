---
title: dołączanie pliku
description: dołączanie pliku
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/22/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 11901e0b68fed9a6a83287d50896bf2afc51fa37
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91279568"
---
Włączenie dysków udostępnionych jest możliwe tylko dla podzbioru typów dysków. Obecnie tylko Ultra disks i Premium dysków SSD mogą włączać dyski udostępnione. Każdy dysk zarządzany z włączonymi dyskami udostępnionymi podlega następującym ograniczeniom zorganizowanym według typu dysku:

### <a name="ultra-disks"></a>Dyski w warstwie Ultra

Niezwykle dyski mają własną listę ograniczeń, niezwiązanych z dyskami udostępnionymi. Ograniczenia dotyczące programu Ultra Disk można znaleźć w tematach [Korzystanie z usługi Azure Ultra disks](../articles/virtual-machines/disks-enable-ultra-ssd.md).

Podczas udostępniania Ultra disks obowiązują następujące dodatkowe ograniczenia:

- Obecnie ograniczona do Azure Resource Manager lub obsługi zestawu SDK. 
- Tylko dyski podstawowe mogą być używane z niektórymi wersjami klastra trybu failover systemu Windows Server, aby uzyskać szczegółowe informacje, zobacz [wymagania sprzętowe klastra trybu failover i opcje magazynu](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).

Udostępnione Ultra disks są dostępne we wszystkich regionach, które domyślnie obsługują usługi Ultra disks i nie wymagają rejestrowania się w celu korzystania z nich.

### <a name="premium-ssds"></a>Dyski SSD w warstwie Premium

- Obecnie obsługiwane tylko w [podzbiorze regionów](#regional-availability).
- Obecnie ograniczona do Azure Resource Manager lub obsługi zestawu SDK. 
- Można ją włączyć tylko na dyskach danych, a nie na dyskach systemu operacyjnego.
- Buforowanie hosta **tylko do odczytu** nie jest dostępne dla dysków SSD Premium z `maxShares>1` .
- Nie można uzyskać dostępu do dysków w warstwie Premium dysków SSD z `maxShares>1` .
- W przypadku używania zestawów dostępności i zestawów skalowania maszyn wirtualnych z dyskami udostępnionymi platformy Azure [wyrównanie domeny błędów magazynu](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set) z domeną błędów maszyny wirtualnej nie jest wymuszane dla udostępnionego dysku danych.
- W przypadku korzystania z [grup umieszczania w sąsiedztwie (PPG)](../articles/virtual-machines/windows/proximity-placement-groups.md)wszystkie maszyny wirtualne, które udostępniają dysk, muszą być częścią tego samego PPGu.
- Tylko dyski podstawowe mogą być używane z niektórymi wersjami klastra trybu failover systemu Windows Server, aby uzyskać szczegółowe informacje, zobacz [wymagania sprzętowe klastra trybu failover i opcje magazynu](https://docs.microsoft.com/windows-server/failover-clustering/clustering-requirements).
- Obsługa Azure Backup i Azure Site Recovery nie jest jeszcze dostępna.

#### <a name="regional-availability"></a>Dostępność regionalna

Udostępnione dysków SSD Premium są obsługiwane tylko w następujących regionach:

- East US
- Wschodnie stany USA 2
- Zachodnie stany USA
- Zachodnie stany USA 2
- Zachodnio-środkowe stany USA
- South Central US
- Północno-środkowe stany USA
- Środkowe stany USA
- West Europe
- Europa Północna
- Korea Środkowa
- Kanada Środkowa
- Kanada Wschodnia
- Japonia Wschodnia
- Japonia Zachodnia
- Azja Południowo-Wschodnia
- Azja Wschodnia
- US Gov Wirginia
- US Gov Arizona