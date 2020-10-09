---
title: Łączenie usługi Azure Virtual Network z usługą CloudSimple przy użyciu rozwiązania ExpressRoute — Azure VMware przez CloudSimple
description: Zawiera opis sposobu uzyskiwania informacji o komunikacji równorzędnej dla połączenia między usługą Azure Virtual Network a środowiskiem CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6b20ee4e04a4443529ecceca8c6fc2206f7df39d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77563992"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>Łączenie sieci wirtualnej platformy Azure z usługą CloudSimple przy użyciu usługi ExpressRoute

Sieć prywatną chmury można zwiększyć do sieci wirtualnej platformy Azure i zasobów platformy Azure. Połączenie ExpressRoute umożliwia dostęp do zasobów uruchomionych w ramach subskrypcji platformy Azure z chmury prywatnej.

## <a name="request-authorization-key"></a>Żądaj klucza autoryzacji

Klucz autoryzacji jest wymagany dla połączenia ExpressRoute między chmurą prywatną i siecią wirtualną platformy Azure. Aby uzyskać klucz, należy skorzystać z <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">pomocy technicznej</a>.  W żądaniu Skorzystaj z następujących informacji:

* Typ problemu: **techniczne**
* Subskrypcja: **Wybierz subskrypcję, w której wdrożono usługę CloudSimple**
* Usługa: **rozwiązanie VMware według CloudSimple**
* Typ problemu: **żądanie obsługi**
* Podtyp problemu: **klucz autoryzacji dla połączenia sieci wirtualnej platformy Azure**
* Podmiot: **żądanie klucza autoryzacji dla połączenia sieci wirtualnej platformy Azure**

## <a name="get-peering-information-from-cloudsimple-portal"></a>Pobierz informacje o komunikacji równorzędnej z portalu CloudSimple

Aby skonfigurować połączenie, należy nawiązać połączenie między usługą Azure Virtual Network i środowiskiem CloudSimple.  W ramach procedury należy podać identyfikator URI obwodu równorzędnego i klucz autoryzacji. Uzyskaj identyfikator URI i klucz autoryzacji z [portalu CloudSimple](access-cloudsimple-portal.md).  Wybierz pozycję **Sieć** w menu po stronie, a następnie wybierz pozycję **połączenie sieciowe platformy Azure**. Lub wybierz pozycję **konto** w menu po stronie, a następnie wybierz pozycję **połączenie sieciowe platformy Azure**.

Kopiuj identyfikator URI obwodu równorzędnego oraz klucz autoryzacji dla każdego z regionów przy użyciu ikony *kopiowania* . Dla każdego regionu CloudSimple, który chcesz połączyć:

1. Kliknij przycisk **Kopiuj** , aby skopiować identyfikator URI. Wklej ją do pliku, który może być dostępny do dodania do Azure Portal.  
2. Kliknij przycisk **Kopiuj** , aby skopiować klucz autoryzacji i wkleić go do pliku.

Skopiuj klucz autoryzacji i identyfikator URI obwodu równorzędnego, który jest w stanie **dostępny** .  **Użycie** stanu wskazuje, że klucz został już użyty do utworzenia połączenia sieci wirtualnej.

![Strona połączenia Virtual Network](media/virtual-network-connection.png)

Aby uzyskać szczegółowe informacje na temat konfigurowania linku sieci wirtualnej platformy Azure do usługi CloudSimple, zobacz [łączenie środowiska chmury prywatnej CloudSimple z siecią wirtualną platformy Azure przy użyciu usługi ExpressRoute](azure-expressroute-connection.md).

## <a name="next-steps"></a>Następne kroki

* [Połączenie sieci wirtualnej platformy Azure z chmurą prywatną](azure-expressroute-connection.md)
* [Nawiązywanie połączenia z siecią lokalną za pomocą usługi Azure ExpressRoute](on-premises-connection.md)
