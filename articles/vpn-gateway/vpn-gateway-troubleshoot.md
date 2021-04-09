---
title: 'VPN Gateway platformy Azure: Rozwiązywanie problemów z konfiguracjami i połączeniami'
description: Ten artykuł ułatwia łączenie się z artykułami w celu rozwiązywania problemów z konfiguracją VPN Gateway, połączeniem i sprawdzaniem przepływności.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: troubleshooting
ms.date: 01/31/2018
ms.author: cherylmc
ms.openlocfilehash: a74109594eec87dddf637542f6d85091ed49231b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104720257"
---
# <a name="troubleshoot-vpn-gateway"></a>Rozwiązywanie problemów VPN Gateway

Połączenia VPN Gateway mogą się nie powieść z różnych powodów. Ten artykuł zawiera linki umożliwiające rozpoczęcie pracy z narzędziem rozwiązywania problemów. Aby zapoznać się z pełną listą, zobacz artykuły zawarte w spisie treści w obszarze **Rozwiązywanie problemów** po lewej stronie tej strony.

## <a name="troubleshooting-scenarios-and-solutions"></a>Scenariusze rozwiązywania problemów i rozwiązania

* [Sprawdzanie przepustowości sieci VPN do sieci wirtualnej](vpn-gateway-validate-throughput-to-vnet.md)<br>Połączenie bramy sieci VPN umożliwia nawiązanie bezpiecznej łączności między Virtual Networkami w ramach platformy Azure i lokalnej infrastruktury INFORMATYCZNej. W tym artykule pokazano, jak zweryfikować przepływność sieci z zasobów lokalnych do maszyny wirtualnej platformy Azure. Zawiera również wskazówki dotyczące rozwiązywania problemów.

* [Ustawienia sieci VPN i zapory](vpn-gateway-third-party-settings.md)<br>Ten artykuł zawiera kilka sugerowanych rozwiązań dla urządzeń sieci VPN lub zapory innych firm, które są używane z usługą VPN Gateway. Dostawca urządzenia zapewnia pomoc techniczną dla urządzeń sieci VPN lub zapory innych firm.

* [Połączenia punkt-lokacja](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md)<br>W tym artykule wymieniono typowe problemy z połączeniem punkt-lokacja, które mogą wystąpić. Omówiono w nim również potencjalne przyczyny i rozwiązania tych problemów.

* [Połączenia typu lokacja-lokacja](vpn-gateway-troubleshoot-site-to-site-cannot-connect.md)<br>Po skonfigurowaniu połączenia sieci VPN typu lokacja-lokacja między siecią lokalną a siecią wirtualną platformy Azure połączenie sieci VPN nagle przestanie działać i nie można go ponownie połączyć. W tym artykule opisano kroki rozwiązywania problemów, które ułatwiają rozwiązanie tego problemu.

* [Rozwiązywanie problemów z usługą Azure VPN Gateway przy użyciu dzienników diagnostycznych](troubleshoot-vpn-with-azure-diagnostics.md)<br>Korzystając z dzienników diagnostycznych, można rozwiązywać problemy z wieloma zdarzeniami związanymi z bramą sieci VPN, w tym aktywnością konfiguracji, łącznością tunelu sieci VPN, rejestrowaniem protokołu IPsec i wymianą tras BGP, w punkcie 

## <a name="next-steps"></a>Następne kroki

Za pomocą tych kroków można także [sprawdzić poprawność połączeń sieci wirtualnych i VPN](https://support.microsoft.com/help/4032151/configuring-and-validating-vnet-or-vpn-connections).
