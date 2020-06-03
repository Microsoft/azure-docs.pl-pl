---
title: Zarządzanie dyskami zarządzanymi
description: Dowiedz się więcej na temat obciążeń dysków dla usługi Azure Virtual Machines na potrzeby maszyn wirtualnych
author: albecker1
ms.author: albecker
ms.date: 06/02/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: f92fae38d49c51dfe87a68b023ba779e89b0e0bc
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/02/2020
ms.locfileid: "84295460"
---
# <a name="disk-bursting"></a>Rozszerzanie możliwości dysków
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Rozerwanie na poziomie maszyny wirtualnej
Obsługa serii na poziomie maszyny wirtualnej jest włączona we wszystkich regionach w chmurze publicznej w przypadku następujących obsługiwanych rozmiarów: 
- [Seria Lsv2](../lsv2-series.md)

W przypadku maszyn wirtualnych, które go obsługują, funkcja przenoszenia jest domyślnie włączona.

## <a name="disk-level-bursting"></a>Rozerwanie na poziomie dysku
Rozbicie jest również dostępne w [dysków SSD Premium](disks-types.md#premium-ssd) w przypadku dysków o rozmiarach P20 i mniejszych we wszystkich regionach w chmurach publicznych, administracji publicznej i chińskiej platformy Azure. W przypadku wszystkich nowych i istniejących wdrożeń rozmiarów dysków, które go obsługują, funkcja przenoszenia dysków jest domyślnie włączona. 

[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]
