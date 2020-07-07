---
title: 'Azure ExpressRoute: wymagania wstępne'
description: Ta strona zawiera listę wymagań, które należy spełnić przed zamówieniem obwodu usługi Azure ExpressRoute. Zawiera listę kontrolną.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: cherylmc
ms.openlocfilehash: a72eba9bde0745e66bdf8e7efd8eaec7d6a0b186
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "74083357"
---
# <a name="expressroute-prerequisites--checklist"></a>Wymagania wstępne usługi ExpressRoute i lista kontrolna
Aby połączyć się z usługami w chmurze firmy Microsoft za pomocą usługi ExpressRoute, należy sprawdzić, czy zostały spełnione wymagania wymienione w poniższych sekcjach.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Konto platformy Azure
* Prawidłowe i aktywne konto platformy Microsoft Azure. To konto jest wymagane do skonfigurowania obwodu usługi ExpressRoute. Obwody usługi ExpressRoute to zasoby w ramach subskrypcji platformy Azure. Subskrypcja platformy Azure jest wymagana, nawet jeśli łączność jest ograniczona do usług w chmurze firmy Microsoft, takich jak Office 365.
* Aktywna subskrypcja usługi Office 365 (w przypadku korzystania z usług Office 365). Więcej informacji znajduje się w tym artykule w sekcji dotyczącej określonych wymagań dotyczących usługi Office 365.

## <a name="connectivity-provider"></a>Dostawca połączenia

* W celu połączenia z chmurą firmy Microsoft można współpracować z [partnerem połączenia usługi ExpressRoute](expressroute-locations.md#partners). Połączenie między siecią lokalną a firmą Microsoft można skonfigurować na [trzy sposoby](expressroute-introduction.md).
* Jeśli dostawca nie jest partnerem połączenia usługi ExpressRoute, można nadal połączyć się z chmurą firmy Microsoft za pośrednictwem [dostawcy serwera Exchange w chmurze](expressroute-locations.md#connectivity-through-exchange-providers).

## <a name="network-requirements"></a>Wymagania dotyczące sieci
* **Nadmiarowość w poszczególnych lokalizacjach komunikacji równorzędnej**: Firma Microsoft wymaga skonfigurowania nadmiarowych sesji protokołu BGP między routerami firmy Microsoft i routerami komunikacji równorzędnej w każdym obwodzie usługi ExpressRoute (nawet jeśli istnieje tylko [jedno połączenie fizyczne z wymianą w chmurze](expressroute-faqs.md#onep2plink)).
* **Nadmiarowość odzyskiwania po awarii**: Firma Microsoft zdecydowanie zaleca skonfigurowanie co najmniej dwóch obwodów usługi ExpressRoute w różnych lokalizacjach komunikacji równorzędnej w celu uniknięcia Single Point of Failure.
* **Routing**: w zależności od sposobu połączenia z chmurą firmy Microsoft użytkownik lub jego dostawca musi skonfigurować sesje protokołu BGP oraz nimi zarządzać na potrzeby [domen routingu](expressroute-circuit-peerings.md). Niektórzy dostawcy połączenia Ethernet lub dostawcy usług serwera Exchange w chmurze mogą oferować zarządzanie przy użyciu protokołu BGP w ramach usługi dodatkowej.
* **Translator adresów sieciowych**: firma Microsoft akceptuje tylko publiczne adresy IP za pośrednictwem komunikacji równorzędnej firmy Microsoft. W przypadku korzystania z prywatnych adresów IP w sieci lokalnej użytkownik lub jego dostawca muszą przełożyć prywatne adresy IP na publiczne [przy użyciu translatora adresów sieciowych](expressroute-nat.md).
* **Technologia QoS**: program Skype dla firm oferuje różne usługi (np. połączenia głosowe, wideo, usługi tekstowe), które wymagają zróżnicowanej obsługi w technologii QoS. Użytkownik i jego dostawca powinni postępować zgodnie z [wymaganiami technologii QoS](expressroute-qos.md).
* **Bezpieczeństwo sieci**: podczas łączenia z usługą Microsoft Cloud za pośrednictwem usługi ExpressRoute miej na uwadze [bezpieczeństwo sieci](../best-practices-network-security.md).

## <a name="office-365"></a>Office 365
Jeśli zamierzasz włączyć usługę Office 365 w ramach usługi ExpressRoute, przejrzyj następujące dokumenty, aby uzyskać więcej informacji o wymaganiach dotyczących usługi Office 365.

* [Omówienie usługi ExpressRoute dla usługi Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Routing za pomocą usługi ExpressRoute dla usługi Office 365](https://support.office.com/article/Routing-with-ExpressRoute-for-Office-365-e1da26c6-2d39-4379-af6f-4da213218408)
* [Wysoka dostępność i tryb failover w usłudze ExpressRoute](https://aka.ms/erhighavailability)
* [Adresy URL i zakresy adresów IP usługi Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
* [Planowanie sieci i dostrajanie wydajności dla usługi Office 365](https://support.office.com/article/Network-planning-and-performance-tuning-for-Office-365-e5f1228c-da3c-4654-bf16-d163daee8848)
* [Narzędzia i kalkulatory przepustowości sieci](https://support.office.com/article/Network-and-migration-planning-for-Office-365-f5ee6c33-bcd7-4b0b-b0f8-dc1d9fb8d132)
* [Integracja usługi Office 365 ze środowiskiem lokalnym](https://support.office.com/article/Office-365-integration-with-on-premises-environments-263faf8d-aa21-428b-aed3-2021837a4b65)
* [Usługa ExpressRoute w usłudze Office 365 — szkoleniowe filmy wideo dla zaawansowanych](https://channel9.msdn.com/series/aer/)

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać więcej informacji na temat ExpressRoute, zobacz [często zadawane pytania](expressroute-faqs.md)dotyczące usługi ExpressRoute.
* Znajdź dostawcę połączenia usługi ExpressRoute. Zobacz artykuł [ExpressRoute partners and peering locations](expressroute-locations.md) (Partnerzy i lokalizacje komunikacji równorzędnej usługi ExpressRoute).
* Zapoznaj się z wymaganiami dotyczącymi [routingu](expressroute-routing.md), [translatora adresów sieciowych](expressroute-nat.md) i [technologii QoS](expressroute-qos.md).
* Skonfiguruj połączenie usługi ExpressRoute.
  * [Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)](expressroute-howto-circuit-arm.md)
  * [Configure routing (Konfigurowanie routingu)](expressroute-howto-routing-arm.md)
  * [Link a VNet to an ExpressRoute circuit (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute)](expressroute-howto-linkvnet-arm.md)
