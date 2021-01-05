---
title: Wersje oprogramowania VMware
description: Obsługiwane wersje oprogramowania VMware dla rozwiązań VMware platformy Azure.
ms.topic: include
ms.date: 12/29/2020
ms.openlocfilehash: c6ba2904bab6c6f44001cafed1bd4cbdeb786373
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97825077"
---
<!-- Used in faq.md and concepts-private-clouds-clusters.md -->


Bieżące wersje oprogramowania VMware używane w klastrach chmur prywatnych rozwiązania Azure VMware są następujące:

| Oprogramowanie              |    Wersja   |
| :---                  |     :---:    |
| VCSA/vSphere/ESXi |    6,7 U3    | 
| ESXi                  |    6,7 U3    | 
| Sieci vSAN                  |    6,7 U3    |
| NSX-T                 |      2.5     |


>[!NOTE]
>NSX-T jest jedyną obsługiwaną wersją programu NSX.

W przypadku każdego nowego klastra w chmurze prywatnej wersja oprogramowania jest zgodna z aktualnie uruchomionymi usługami. W przypadku każdej nowej chmury prywatnej w ramach subskrypcji zostanie zainstalowana najnowsza wersja stosu oprogramowania. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące wersji oprogramowania VMware](https://docs.vmware.com/en/VMware-HCX/services/user-guide/GUID-54E5293B-8707-4D29-BFE8-EE63539CC49B.html).

Uaktualnienie pakietu oprogramowania w chmurze prywatnej zachowuje oprogramowanie w ramach jednej wersji najnowszego pakietu oprogramowania z programu VMware. Wersje oprogramowania w chmurze prywatnej mogą różnić się od najnowszych wersji poszczególnych składników oprogramowania (ESXi, NSX-T, vCenter, sieci vSAN). Ogólne zasady uaktualniania i procesy można znaleźć dla oprogramowania platformy Azure VMware Solution platform opisanego w artykule [aktualizacje i uaktualnienia w chmurze prywatnej](../concepts-upgrades.md).

