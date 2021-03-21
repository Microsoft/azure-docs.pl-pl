---
title: Wersje oprogramowania VMware
description: Obsługiwane wersje oprogramowania VMware dla rozwiązań VMware platformy Azure.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 9a1781d163e67c2f9b6a4f756a616e59a969696f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462522"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


Wersje oprogramowania VMware używane w nowych wdrożeniach klastrów prywatnych chmur rozwiązań platformy Azure są następujące:

| Oprogramowanie              |    Wersja   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6,7 U3    | 
| ESXi                  |    6,7 U3    | 
| Sieci vSAN                  |    6,7 U3    |
| NSX-T <br />**Uwaga:** NSX-T jest jedyną obsługiwaną wersją programu NSX.               |      2.5     |


Nowe klastry dodane do istniejącej chmury prywatnej są stosowane w aktualnie uruchomionej wersji oprogramowania. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące wersji oprogramowania VMware](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

Uaktualnienie pakietu oprogramowania w chmurze prywatnej zachowuje oprogramowanie w ramach jednej wersji najnowszego pakietu oprogramowania z programu VMware. Wersje oprogramowania w chmurze prywatnej mogą różnić się od najnowszych wersji poszczególnych składników oprogramowania (ESXi, NSX-T, vCenter, sieci vSAN). Ogólne zasady uaktualniania i procesy można znaleźć dla oprogramowania platformy Azure VMware Solution platform opisanego w artykule [aktualizacje i uaktualnienia w chmurze prywatnej](../concepts-upgrades.md).

