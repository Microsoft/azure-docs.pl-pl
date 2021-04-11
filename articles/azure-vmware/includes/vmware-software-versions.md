---
title: Wersje oprogramowania VMware
description: Obsługiwane wersje oprogramowania VMware dla rozwiązań VMware platformy Azure.
ms.topic: include
ms.date: 03/31/2021
ms.openlocfilehash: a6441b55bbc6a8f694c50bbf022a6a2ae52d60bf
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106098149"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


Wersje oprogramowania VMware używane w nowych wdrożeniach klastrów prywatnych chmur rozwiązań platformy Azure są następujące:

| Oprogramowanie              |    Wersja   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6,7 U3l    | 
| ESXi                  |    6,7 U3l    | 
| Sieci vSAN                  |    6,7 U3l    |
| NSX-T <br />**Uwaga:** NSX-T jest jedyną obsługiwaną wersją programu NSX.               |      3.1.1     |


Nowe klastry dodane do istniejącej chmury prywatnej są stosowane w aktualnie uruchomionej wersji oprogramowania. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące wersji oprogramowania VMware](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

Uaktualnienie pakietu oprogramowania w chmurze prywatnej zachowuje oprogramowanie w ramach jednej wersji najnowszego pakietu oprogramowania z programu VMware. Wersje oprogramowania w chmurze prywatnej mogą różnić się od najnowszych wersji poszczególnych składników oprogramowania (ESXi, NSX-T, vCenter, sieci vSAN). Ogólne zasady uaktualniania i procesy można znaleźć dla oprogramowania platformy Azure VMware Solution platform opisanego w artykule [aktualizacje i uaktualnienia w chmurze prywatnej](../concepts-upgrades.md).

