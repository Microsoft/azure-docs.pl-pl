---
title: Zmień wydajność usługi Azure Managed disks przy użyciu Azure Portal
description: Dowiedz się, jak zmienić warstwy wydajności dla nowych i istniejących dysków zarządzanych przy użyciu Azure Portal.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 01/05/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 625fb1e3dd0b433da6b60f995aa6b380c23ec9ce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97901037"
---
# <a name="change-your-performance-tier-using-the-azure-portal"></a>Zmień warstwę wydajności przy użyciu Azure Portal

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>Ograniczenia

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="getting-started"></a>Wprowadzenie

### <a name="new-disks"></a>Nowe dyski

Poniższe kroki pokazują, jak zmienić warstwę wydajności dysku podczas pierwszego tworzenia dysku:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Przejdź do maszyny wirtualnej, dla której chcesz utworzyć nowy dysk.
1. Po wybraniu nowego dysku, najpierw wybierz rozmiar dysku, którego potrzebujesz.
1. Po wybraniu rozmiaru wybierz inną warstwę wydajności, aby zmienić jej wydajność.
1. Wybierz **przycisk OK** , aby utworzyć dysk.

:::image type="content" source="media/disks-performance-tiers-portal/new-disk-change-performance-tier.png" alt-text="Zrzut ekranu przedstawiający blok tworzenia dysku jest wyróżniony, a lista rozwijana warstwy wydajności zostanie wyróżniona." lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::


## <a name="existing-disks"></a>Istniejące dyski

Poniższe kroki przedstawiają sposób zmiany warstwy wydajności istniejącego dysku:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Przejdź do maszyny wirtualnej zawierającej dysk, który chcesz zmienić.
1. Cofnij przydział maszyny wirtualnej lub Odłącz dysk.
1. Wybierz dysk
1. Wybierz pozycję **rozmiar i wydajność**.
1. Na liście rozwijanej **warstwa wydajności** wybierz warstwę inną niż bieżąca warstwa wydajności dysku.
1. Wybierz opcję **Zmień rozmiar**.

:::image type="content" source="media/disks-performance-tiers-portal/change-tier-existing-disk.png" alt-text="Zrzut ekranu przedstawiający blok rozmiar + wydajność, warstwa wydajności została wyróżniona." lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::

## <a name="next-steps"></a>Następne kroki

Jeśli potrzebujesz zmienić rozmiar dysku, aby wykorzystać wyższe warstwy wydajności, zobacz następujące artykuły:

- [Rozszerzanie wirtualnych dysków twardych na maszynę wirtualną z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure](linux/expand-disks.md)
- [Rozszerzanie dysku zarządzanego dołączonego do maszyny wirtualnej z systemem Windows](windows/expand-os-disk.md)
