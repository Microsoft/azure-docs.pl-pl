---
title: Limity rozwiązań VMware platformy Azure
description: Ograniczenia rozwiązań VMware platformy Azure.
ms.topic: include
ms.date: 03/04/2021
ms.openlocfilehash: 99c27694a300284cfd99b8411306c90ad4d8a12e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193747"
---
<!-- Used in /azure/azure-resource-manager/management/azure-subscription-service-limits.md -->

W poniższej tabeli opisano maksymalne limity dla rozwiązania VMware platformy Azure.

| **Zasób** | **Granice** |
| :-- | :-- |
| Klastry na chmurę prywatną | 4 |
| Minimalna liczba węzłów na klaster | 3 |
| Maksymalna liczba węzłów na klaster | 16 |
| Węzły na chmurę prywatną | 64 |
| Program vCenter na chmurę prywatną | 1  |
| Pary witryn HCX | 3 z Advanced Edition, 10 i Enterprise Edition |
| Automatyczna synchronizacja ExpressRoute maksymalna połączona SDDCs | 4 |
| Automatyczna synchronizacja ExpressRoute portspeed | 10 Gb/s | 
| Publiczne adresy IP uwidocznione za pośrednictwem vWAN | 100 |
| limity pojemności sieci vSAN | 75% całkowitego użycia (Zachowaj 25% dostępne dla umowy SLA)  |

W przypadku innych ograniczeń określonych przez program VMware Użyj [maksymalnego narzędzia do konfiguracji programu VMware](https://configmax.vmware.com/).
