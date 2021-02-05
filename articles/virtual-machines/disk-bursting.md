---
title: Zarządzanie dyskami zarządzanymi
description: Dowiedz się więcej na temat obciążeń dysków dla dysków platformy Azure i maszyn wirtualnych platformy Azure.
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: dcdbf94e547581cb9ff885ac5896467abdf316ae
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576197"
---
# <a name="managed-disk-bursting"></a>Zarządzanie dyskami zarządzanymi
[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Rozerwanie na poziomie maszyny wirtualnej
Rozbicie na poziomie maszyny wirtualnej jest włączone w następującej serii maszyn wirtualnych we wszystkich regionach, w których są obsługiwane:
- [Seria Lsv2](lsv2-series.md)
- [Seria Dsv3](dv3-dsv3-series.md)
- [Seria Esv3](ev3-esv3-series.md)

W przypadku maszyn wirtualnych, które go obsługują, funkcja przenoszenia jest domyślnie włączona.

## <a name="disk-level-bursting"></a>Rozerwanie na poziomie dysku
Rozbicie jest również dostępne w [dysków SSD Premium](disks-types.md#premium-ssd) w przypadku dysków o rozmiarach P20 i mniejszych we wszystkich regionach w chmurach publicznych, administracji publicznej i chińskiej platformy Azure. W przypadku wszystkich nowych i istniejących wdrożeń rozmiarów dysków, które go obsługują, funkcja przenoszenia dysków jest domyślnie włączona. 

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]
