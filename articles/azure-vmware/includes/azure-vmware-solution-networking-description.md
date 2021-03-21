---
title: Sieć i łączność rozwiązań VMware platformy Azure
description: Opis sieci i łączności rozwiązań VMware platformy Azure.
ms.topic: include
ms.date: 03/13/2021
ms.openlocfilehash: 96dd93f1db5dc3ddcbb883313e19c6aed8a256da
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462590"
---
<!-- Used in introduction.md and concepts-networking.md -->

Rozwiązanie VMware dla platformy Azure oferuje środowisko chmury prywatnej dostępne z zasobów lokalnych i opartych na platformie Azure. Takie usługi jak Azure ExpressRoute, połączenia sieci VPN lub Azure Virtual WAN zapewniają łączność. Te usługi wymagają określonych zakresów adresów sieciowych i portów zapory do włączenia usług.

Podczas wdrażania chmury prywatnej można utworzyć sieci prywatne do zarządzania, aprowizacji i vMotion. Za pomocą tych sieci prywatnych można uzyskiwać dostęp do Menedżera vCenter i NSX-T oraz maszyn wirtualnych vMotion lub wdrożenia.  

ExpressRoute Global Reach służy do łączenia chmur prywatnych z środowiskami lokalnymi. Połączenie wymaga sieci wirtualnej z obwodem usługi ExpressRoute w ramach subskrypcji.

Maszyny wirtualne wdrożone w chmurze prywatnej są dostępne dla Internetu za pośrednictwem funkcji publicznego adresu IP sieci wirtualnej platformy Azure.  Domyślnie dostęp do Internetu jest wyłączony dla nowych chmur prywatnych. Aby uzyskać więcej informacji, zobacz [jak korzystać z funkcji publicznego adresu IP w rozwiązaniu Azure VMware](../public-ip-usage.md).

