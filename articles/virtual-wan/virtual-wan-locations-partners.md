---
title: Partnerzy i lokalizacje wirtualnych sieci WAN platformy Azure | Microsoft Docs
description: Ten artykuł zawiera listę partnerów i lokalizacji centrów wirtualnych sieci WAN platformy Azure.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to find a Virtual WAN partner
ms.openlocfilehash: e8dcb188dc5aad59738e3eab4169bf9dbd3679ec
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91449346"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Wirtualne partnerzy sieci WAN i wirtualne lokalizacje koncentratora

Ten artykuł zawiera informacje na temat regionów i partnerów obsługiwanych przez wirtualną sieć WAN na potrzeby łączności z koncentratorem wirtualnym.

Azure Virtual WAN to usługa sieciowa zapewniająca zoptymalizowaną i zautomatyzowaną łączność między oddziałami za pośrednictwem platformy Azure. Usługa Virtual WAN umożliwia łączenie urządzeń w oddziałach z platformą Azure i konfigurowanie ich komunikacji. Można to zrobić ręcznie lub za pomocą urządzeń dostawcy za pośrednictwem wirtualnego partnera sieci WAN. Korzystanie z urządzeń partnerskich umożliwia łatwe korzystanie z programu, uproszczenie łączności i zarządzanie konfiguracją.

Połączenie z urządzeniem lokalnym jest nawiązywane w zautomatyzowany sposób z koncentratorem wirtualnym. Koncentratorem wirtualnym jest sieć wirtualna zarządzana przez firmę Microsoft. Koncentrator zawiera różne punkty końcowe usług, umożliwiające łączność z siecią lokalną (zasobem vpnsite). Możesz mieć tylko jeden koncentrator na region.

## <a name="branch-ipsec-connectivity-automation-from-partners"></a><a name="automation"></a>Automatyzacja połączeń IPSec z gałęzią od partnerów

Urządzenia łączące się z wirtualną siecią WAN platformy Azure mają wbudowaną automatyzację, aby nawiązać połączenie. Jest to zazwyczaj skonfigurowane w interfejsie użytkownika zarządzania urządzeniami (lub równoważne), które konfiguruje zarządzanie łącznością i konfiguracją między urządzeniem oddziału sieci VPN a punktem końcowym sieci VPN usługi Azure Virtual Hub (Brama sieci VPN).

Następująca Automatyzacja wysokiego poziomu jest skonfigurowana w konsoli urządzenia/w centrum zarządzania:

* Odpowiednie uprawnienia dla urządzenia umożliwiającego dostęp do grupy zasobów wirtualnej sieci WAN platformy Azure
* Przekazywanie urządzenia oddziału do wirtualnej sieci WAN platformy Azure
* Automatyczne pobieranie informacji o łączności z platformą Azure
* Konfiguracja lokalnego urządzenia rozgałęzienia 

Niektórzy partnerzy łączności mogą zwiększyć automatyzację, aby uwzględnić tworzenie sieci wirtualnej i VPN Gateway usługi Azure Virtual Hub. Jeśli chcesz dowiedzieć się więcej o automatyzacji, zobacz [wskazówki dotyczące automatyzacji dla partnerów wirtualnych sieci WAN](virtual-wan-configure-automation-providers.md).

## <a name="branch-ipsec-connectivity-partners"></a><a name="partners"></a>Partnerzy łączności protokołu IPSec dla gałęzi

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

Następujący partnerzy są styczeń na naszym planie w najbliższej przyszłości: 128 technologie, Arista, Cisco Systems (Viptela), F5 Networks, Oracle SD-WAN i SharpLink.

## <a name="partners-with-integrated-virtual-hub-offerings"></a>Partnerzy ze zintegrowanymi ofertami koncentratora wirtualnego
Oprócz zautomatyzowanej łączności z pakietem IPSec w biurze oddziału niektórzy Partnerzy oferują **wirtualne urządzenia sieciowe (urządzeń WUS)** , które można zintegrować bezpośrednio z wirtualnym KONCENTRATOREM sieci WAN platformy Azure.  Dzięki temu klienci mogą zakończyć połączenia gałęzi do zgodnego urządzenia innej firmy w koncentratorze wirtualnym.  

Partnerzy, którzy oferują urządzenie WUS w wirtualnym koncentratorze sieci WAN, muszą:

* Zaimplementowano automatyzację łączności protokołu IPSec na urządzeniu oddziału i masz wbudowaną urządzenie WUS swoją ofertę do wirtualnego centrum sieci WAN platformy Azure.
* Masz dostęp do istniejącej oferty sieciowego urządzenia wirtualnego w portalu Azure Marketplace.

Jeśli jesteś partnerem i masz pytania dotyczące zarządzanego urządzenie WUS w ramach oferty koncentratora wirtualnego, skontaktuj się z nami, wysyłając wiadomość e-mail na adres vwannvaonboarding@microsoft.com

## <a name="integrated-virtual-hub-nva-partners"></a>Partnerzy urządzenie WUS zintegrowanego koncentratora wirtualnego
Ci partnerzy mają oferty **aplikacji zarządzanych** , które są teraz dostępne do wdrożenia w wirtualnym KONCENTRATORZE sieci WAN.

|Partnerzy|Konfiguracja/Przewodnik dotyczący wdrażania|
|---|---|
|[Barracuda Networks](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overviewus/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overview)| [Podręcznik wdrażania Barracuda CloudGen sieci WAN](https://campus.barracuda.com/product/cloudgenwan/doc/91980640/deployment/)|
|[VWAN routera usług w chmurze (CSR) firmy Cisco](https://aka.ms/ciscoMarketPlaceOffer)| Aby wdrożyć sieć WAN firmy Cisco Cloud Services (CSR) w koncentratorze wirtualnym, Wyślij wiadomość e-mail do vwan_public_preview@cisco.com |

Następujący partnerzy są styczeń, aby urządzenie WUS w centrum wirtualne w najbliższej przyszłości: Citrix, Verse Networks i VeloCloud.

## <a name="locations"></a><a name="locations"></a>Lokalizacje

[!INCLUDE [regions](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz temat [często zadawane pytania dotyczące wirtualnej sieci WAN](virtual-wan-faq.md).

* Aby uzyskać więcej informacji na temat automatyzowania łączności z usługą Azure Virtual WAN, zobacz [wskazówki dotyczące automatyzacji dla partnerów wirtualnych sieci WAN](virtual-wan-configure-automation-providers.md).
