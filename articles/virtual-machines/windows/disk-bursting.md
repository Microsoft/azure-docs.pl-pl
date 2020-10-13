---
title: Zarządzanie dyskami zarządzanymi
description: Dowiedz się więcej na temat obciążeń dysków dla usługi Azure Virtual Machines na potrzeby maszyn wirtualnych
author: albecker1
ms.author: albecker
ms.date: 09/22/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 547834542b605b226ebffd68e05296ee847dc6de
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974332"
---
# <a name="disk-bursting"></a>Rozszerzanie możliwości dysków
[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting.md)]

## <a name="virtual-machine-level-bursting"></a>Rozerwanie na poziomie maszyny wirtualnej
Obsługa serii na poziomie maszyny wirtualnej jest włączona we wszystkich regionach w chmurze publicznej w przypadku następujących obsługiwanych rozmiarów: 
- [Seria Lsv2](../lsv2-series.md)

W regionie zachodnie stany USA dostępne są również maszyny wirtualne o następujących rozmiarach:
- [Seria Dsv3](../dv3-dsv3-series.md)
- [Seria Esv3](../ev3-esv3-series.md)

W przypadku maszyn wirtualnych, które go obsługują, funkcja przenoszenia jest domyślnie włączona.

## <a name="disk-level-bursting"></a>Rozerwanie na poziomie dysku
Rozbicie jest również dostępne w [dysków SSD Premium](../disks-types.md#premium-ssd) dla rozmiaru dysku P20 i mniejszej we wszystkich regionach. Funkcja przenoszenia dysków jest domyślnie włączona w nowych wdrożeniach obsługiwanych rozmiarów dysków. Istniejące rozmiary dysków, jeśli obsługują rozbicie dysku, mogą umożliwić Przechodzenie przez jedną z następujących metod: 
- **Uruchom ponownie maszynę wirtualną** 
- **Odłączanie i ponowne dołączanie dysku**


[!INCLUDE [managed-disks-bursting](../../../includes/managed-disks-bursting-2.md)]