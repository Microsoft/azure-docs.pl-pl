---
title: Limity rozwiązań VMware platformy Azure
description: Ograniczenia rozwiązań VMware platformy Azure.
ms.topic: include
ms.date: 03/16/2021
ms.openlocfilehash: 0e2359d951f5348b69e95ab7fa046981b2b7b32d
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103622145"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

W poniższej tabeli opisano maksymalne limity dla rozwiązania VMware platformy Azure.

| **Zasób** | **Granice** |
| :-- | :-- |
| Klastry na chmurę prywatną | 12 |
| Minimalna liczba węzłów na klaster | 3 |
| Maksymalna liczba węzłów na klaster | 16 |
| Węzły na chmurę prywatną | 64 |
| Program vCenter na chmurę prywatną | 1  |
| Pary witryn HCX | 3 z Advanced Edition, 10 i Enterprise Edition |
| Automatyczna synchronizacja ExpressRoute maksymalna połączona SDDCs | 4 |
| Automatyczna synchronizacja ExpressRoute portspeed | 10 Gb/s<br />Używana Brama sieci wirtualnej określa rzeczywistą przepustowość. Aby uzyskać więcej informacji, zobacz [Informacje o bramach sieci wirtualnej ExpressRoute](../../expressroute/expressroute-about-virtual-network-gateways.md) | 
| Publiczne adresy IP uwidocznione za pośrednictwem vWAN | 100 |
| limity pojemności sieci vSAN | 75% całkowitego użycia (Zachowaj 25% dostępne dla umowy SLA)  |

W przypadku innych ograniczeń określonych przez program VMware Użyj [maksymalnego narzędzia do konfiguracji programu VMware](https://configmax.vmware.com/).
