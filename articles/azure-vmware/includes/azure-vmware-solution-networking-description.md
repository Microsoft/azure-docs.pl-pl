---
title: Sieć i łączność rozwiązań VMWare platformy Azure
description: Opis sieci i łączności rozwiązań VMWare platformy Azure.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 1d43a8a49fc56a6a5b534a89fd1dfc2f5ae2d478
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91574464"
---
<!-- Used in introduction.md and concepts-networking.md -->

Rozwiązanie VMware dla platformy Azure oferuje środowisko chmury prywatnej dostępne ze środowisk i zasobów lokalnych i opartych na platformie Azure. Usługi, takie jak Azure ExpressRoute i połączenia sieci VPN, zapewniają łączność. Te usługi wymagają określonych zakresów adresów sieciowych i portów zapory do włączenia usług.

Podczas wdrażania chmury prywatnej można utworzyć sieci prywatne do zarządzania, aprowizacji i vMotion. Za pomocą tych sieci prywatnych można uzyskiwać dostęp do Menedżera vCenter i NSX-T oraz maszyn wirtualnych vMotion lub wdrożenia.  ExpressRoute Global Reach służy do łączenia chmur prywatnych z środowiskami lokalnymi. Połączenie wymaga sieci wirtualnej z obwodem usługi ExpressRoute w ramach subskrypcji.



>[!NOTE]
>Dostęp do Internetu i usług platformy Azure są obsługiwane i udostępniane do korzystania z maszyn wirtualnych w sieciach produkcyjnych podczas wdrażania chmury prywatnej.  Domyślnie dostęp do Internetu jest wyłączony dla nowych chmur prywatnych i w dowolnym momencie może być włączony lub wyłączony.