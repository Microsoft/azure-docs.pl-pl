---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 39750a86ccf781a10109e299e27a55a03173acb6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98901086"
---
Migawki przyrostowe są kopiami zapasowymi punktu w czasie dla dysków zarządzanych, które w razie potrzeby składają się tylko z zmian od ostatniej migawki. Po przywróceniu dysku z migawki przyrostowej system rekonstruuje pełny dysk, który reprezentuje punkt w czasie kopii zapasowej dysku podczas tworzenia przyrostowej migawki. Ta nowa możliwość dla migawek dysków zarządzanych potencjalnie pozwala im zwiększyć koszty, ponieważ, o ile nie zostanie wybrana opcja, nie trzeba przechowywać całego dysku przy każdej pojedynczej migawce. Podobnie jak w przypadku pełnych migawek, przyrostowe migawki mogą służyć do tworzenia pełnego dysku zarządzanego lub pełnej migawki.

Istnieje kilka różnic między migawką przyrostową i pełną migawką. Migawki przyrostowe zawsze korzystają ze standardowego magazynu HDD, niezależnie od typu magazynu dysku, natomiast pełne migawki mogą korzystać z dysków SSD w warstwie Premium. Jeśli używasz pełnych migawek na Premium Storage do skalowania wdrożeń maszyn wirtualnych, zalecamy używanie obrazów niestandardowych w ramach magazynu w warstwie Standardowa w [galerii obrazów udostępnionych](../articles/virtual-machines/shared-image-galleries.md). Pomożemy Ci w osiągnięciu bardziej ogromnej skali z niższym kosztem. Ponadto migawki przyrostowe potencjalnie oferują lepszą niezawodność za pomocą [magazynu Strefowo nadmiarowego](../articles/storage/common/storage-redundancy.md) (ZRS). Jeśli ZRS jest dostępny w wybranym regionie, migawka przyrostowa będzie używać ZRS automatycznie. Jeśli ZRS nie jest dostępny w regionie, migawka domyślnie będzie [magazynem lokalnie nadmiarowy](../articles/storage/common/storage-redundancy.md) (LRS). Można zastąpić to zachowanie i wybrać jeden z nich ręcznie, ale nie jest to zalecane.

Migawki przyrostowe oferują również funkcję różnicową, która jest dostępna tylko w przypadku dysków zarządzanych. Umożliwiają one uzyskanie zmian między dwiema przyrostowymi migawkami tych samych dysków zarządzanych, w dół do poziomu bloku. Można użyć tej funkcji, aby zmniejszyć rozmiary danych podczas kopiowania migawek między regionami.  Na przykład możesz pobrać pierwszą migawkę przyrostową jako podstawowy obiekt BLOB w innym regionie. W przypadku kolejnych migawek przyrostowych można skopiować tylko zmiany od ostatniej migawki do podstawowego obiektu BLOB. Po skopiowaniu zmian można wykonać migawki na podstawowym obiekcie blob, który reprezentuje punkt w czasie, aby utworzyć kopię zapasową dysku w innym regionie. Dysk można przywrócić z podstawowego obiektu BLOB lub z migawki w podstawowym obiekcie BLOB w innym regionie.

:::image type="content" source="media/virtual-machines-disks-incremental-snapshots-description/incremental-snapshot-diagram.png" alt-text="Diagram przedstawiający migawki przyrostowe skopiowane między regionami. Migawki tworzą różne wywołania interfejsu API do momentu ostatecznego tworzenia stronicowych obiektów BLOB dla każdej migawki.":::

Dla migawek przyrostowych są naliczane opłaty tylko za użyty rozmiar. Używany rozmiar migawek można znaleźć, przeglądając [raport dotyczący użycia platformy Azure](../articles/cost-management-billing/understand/review-individual-bill.md). Na przykład jeśli używany rozmiar danych migawki to 10 GiB, Raport **dziennego** użycia będzie zawierał 10 GIB/(31 dni) = 0,3226 jako ilość zużytą.