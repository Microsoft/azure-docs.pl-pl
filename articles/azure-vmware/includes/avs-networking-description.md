---
title: Sieć i łączność rozwiązań VMWare platformy Azure
description: Opis sieci i łączności rozwiązań VMWare platformy Azure.
ms.topic: include
ms.date: 09/21/2020
ms.openlocfilehash: 6c45043ebbbc5099f512a1d746e48d2a8a065e6f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316900"
---
<!-- Used in introduction.md and concepts-networking.md -->

Rozwiązanie VMware dla platformy Azure oferuje środowisko chmury prywatnej dostępne ze środowisk i zasobów lokalnych i opartych na platformie Azure. Usługi, takie jak Azure ExpressRoute i połączenia sieci VPN, zapewniają łączność. Te usługi wymagają określonych zakresów adresów sieciowych i portów zapory do włączenia usług.

Podczas wdrażania chmury prywatnej można utworzyć sieci prywatne do zarządzania, aprowizacji i vMotion. Za pomocą tych sieci prywatnych można uzyskiwać dostęp do Menedżera vCenter i NSX-T oraz maszyn wirtualnych vMotion lub wdrożenia.  ExpressRoute Global Reach służy do łączenia chmur prywatnych z środowiskami lokalnymi. Połączenie wymaga sieci wirtualnej z obwodem usługi ExpressRoute w ramach subskrypcji.


>[!NOTE]
>Dostęp do Internetu i usług platformy Azure są obsługiwane i udostępniane do korzystania z maszyn wirtualnych w sieciach produkcyjnych podczas wdrażania chmury prywatnej.  Domyślnie dostęp do Internetu jest wyłączony dla nowych chmur prywatnych i w dowolnym momencie może być włączony lub wyłączony.