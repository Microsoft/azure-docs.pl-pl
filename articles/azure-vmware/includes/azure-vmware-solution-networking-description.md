---
title: Sieć i łączność rozwiązań VMware platformy Azure
description: Opis sieci i łączności rozwiązań VMware platformy Azure.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 47b5fab8430f4e462f1e4a0e8cb6ddb69ec38c02
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92924982"
---
<!-- Used in introduction.md and concepts-networking.md -->

Rozwiązanie VMware dla platformy Azure oferuje środowisko chmury prywatnej dostępne ze środowisk i zasobów lokalnych i opartych na platformie Azure. Usługi, takie jak Azure ExpressRoute i połączenia sieci VPN, zapewniają łączność. Te usługi wymagają określonych zakresów adresów sieciowych i portów zapory do włączenia usług.

Podczas wdrażania chmury prywatnej można utworzyć sieci prywatne do zarządzania, aprowizacji i vMotion. Za pomocą tych sieci prywatnych można uzyskiwać dostęp do Menedżera vCenter i NSX-T oraz maszyn wirtualnych vMotion lub wdrożenia.  ExpressRoute Global Reach służy do łączenia chmur prywatnych z środowiskami lokalnymi. Połączenie wymaga sieci wirtualnej z obwodem usługi ExpressRoute w ramach subskrypcji.

Zasoby, takie jak serwery sieci Web i maszyny wirtualne, są dostępne dla Internetu za pomocą funkcji publicznego adresu IP sieci wirtualnej platformy Azure.  Domyślnie dostęp do Internetu jest wyłączony dla nowych chmur prywatnych. Aby uzyskać więcej informacji, zobacz [jak korzystać z funkcji publicznego adresu IP w rozwiązaniu Azure VMware](../public-ip-usage.md).