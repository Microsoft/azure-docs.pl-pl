---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/30/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 22a1a4b99717df32a40ea69ebb65a3a8e14ee2b4
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102511043"
---
Włączenie dysków udostępnionych jest możliwe tylko dla podzbioru typów dysków. Obecnie tylko Ultra disks i Premium dysków SSD mogą włączać dyski udostępnione. Każdy dysk zarządzany z włączonymi dyskami udostępnionymi podlega następującym ograniczeniom zorganizowanym według typu dysku:

### <a name="ultra-disks"></a>Dyski w warstwie Ultra

Niezwykle dyski mają własną listę ograniczeń, niezwiązanych z dyskami udostępnionymi. Ograniczenia dotyczące programu Ultra Disk można znaleźć w tematach [Korzystanie z usługi Azure Ultra disks](../articles/virtual-machines/disks-enable-ultra-ssd.md).

Podczas udostępniania Ultra disks obowiązują następujące dodatkowe ograniczenia:

- Obecnie ograniczona do Azure Resource Manager lub obsługi zestawu SDK. 
- Tylko dyski podstawowe mogą być używane z niektórymi wersjami klastra trybu failover systemu Windows Server, aby uzyskać szczegółowe informacje, zobacz [wymagania sprzętowe klastra trybu failover i opcje magazynu](/windows-server/failover-clustering/clustering-requirements).

Udostępnione Ultra disks są dostępne we wszystkich regionach, które domyślnie obsługują usługi Ultra disks i nie wymagają rejestrowania się w celu korzystania z nich.

### <a name="premium-ssds"></a>Dyski SSD w warstwie Premium

- Obecnie ograniczona do Azure Resource Manager lub obsługi zestawu SDK. 
- Można ją włączyć tylko na dyskach danych, a nie na dyskach systemu operacyjnego.
- Buforowanie hosta **tylko do odczytu** nie jest dostępne dla dysków SSD Premium z `maxShares>1` .
- Nie można uzyskać dostępu do dysków w warstwie Premium dysków SSD z `maxShares>1` .
- W przypadku używania zestawów dostępności i zestawów skalowania maszyn wirtualnych z dyskami udostępnionymi platformy Azure [wyrównanie domeny błędów magazynu](../articles/virtual-machines/availability.md) z domeną błędów maszyny wirtualnej nie jest wymuszane dla udostępnionego dysku danych.
- W przypadku korzystania z [grup umieszczania w sąsiedztwie (PPG)](../articles/virtual-machines/windows/proximity-placement-groups.md)wszystkie maszyny wirtualne, które udostępniają dysk, muszą być częścią tego samego PPGu.
- Tylko dyski podstawowe mogą być używane z niektórymi wersjami klastra trybu failover systemu Windows Server, aby uzyskać szczegółowe informacje, zobacz [wymagania sprzętowe klastra trybu failover i opcje magazynu](/windows-server/failover-clustering/clustering-requirements).
- Obsługa Azure Site Recovery nie jest jeszcze dostępna.
- Azure Backup jest dostępna za pomocą [usługi Kopia zapasowa Azure Disk (wersja zapoznawcza)](../articles/backup/disk-backup-overview.md).

#### <a name="regional-availability"></a>Dostępność w regionach

Udostępnione dysków SSD Premium są dostępne we wszystkich regionach, w których są dostępne dyski zarządzane.