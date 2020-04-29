---
title: Opóźnienie połączenia użytkownika pulpitu wirtualnego systemu Windows — Azure
description: Opóźnienie połączenia dla użytkowników pulpitu wirtualnego systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a4210947d771768943775a3e62c2558fa2883bd5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79128180"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Określanie opóźnienia połączenia użytkownika w programie Virtual Desktop systemu Windows

Pulpit wirtualny systemu Windows jest dostępny globalnie. Administratorzy mogą tworzyć maszyny wirtualne w dowolnym regionie świadczenia usługi Azure. Opóźnienie połączenia będzie się różnić w zależności od lokalizacji użytkowników i maszyn wirtualnych. Usługi pulpitu wirtualnego systemu Windows będą stale przełączane do nowych lokalizacje geograficzne w celu poprawy opóźnienia. 
 
[Narzędzie szacowania środowiska pulpitu wirtualnego systemu Windows](https://azure.microsoft.com/services/virtual-desktop/assessment/) może pomóc w ustaleniu najlepszej lokalizacji w celu zoptymalizowania opóźnień maszyn wirtualnych. Zalecamy używanie narzędzia co dwa do trzech miesięcy, aby upewnić się, że optymalna lokalizacja nie została zmieniona jako Windows Virtual Desktop w nowe obszary. 

## <a name="azure-traffic-manager"></a>Azure Traffic Manager

Pulpit wirtualny systemu Windows używa Traffic Manager platformy Azure, który sprawdza lokalizację serwera DNS użytkownika w celu znalezienia najbliższego wystąpienia usługi pulpitu wirtualnego systemu Windows. Zaleca się, aby administratorzy przeglądali lokalizację serwera DNS użytkownika przed wybraniem lokalizacji maszyn wirtualnych.

## <a name="next-steps"></a>Następne kroki

- Aby sprawdzić najlepszą lokalizację optymalnego opóźnienia, zobacz [narzędzie szacowania pulpitu wirtualnego systemu Windows](https://azure.microsoft.com/services/virtual-desktop/assessment/).
- Aby zapoznać się z planami cenowymi, zobacz [Cennik usługi pulpit wirtualny systemu Windows](https://azure.microsoft.com/pricing/details/virtual-desktop/).
- Aby rozpocząć pracę z wdrożeniem pulpitu wirtualnego systemu Windows, zapoznaj się z [naszym samouczkiem](tenant-setup-azure-active-directory.md).