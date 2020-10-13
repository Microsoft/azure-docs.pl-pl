---
title: 'Azure ExpressRoute: wymagania wstępne'
description: Ta strona zawiera listę wymagań, które należy spełnić przed zamówieniem obwodu usługi Azure ExpressRoute. Zawiera listę kontrolną.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 87c8a7523222afd3ae4daeb8fb5824025e693cd1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89569632"
---
# <a name="expressroute-prerequisites--checklist"></a>Wymagania wstępne usługi ExpressRoute i lista kontrolna
Aby połączyć się z usługami w chmurze firmy Microsoft za pomocą usługi ExpressRoute, należy sprawdzić, czy zostały spełnione wymagania wymienione w poniższych sekcjach.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Konto platformy Azure
* Prawidłowe i aktywne konto platformy Microsoft Azure. To konto jest wymagane do skonfigurowania obwodu usługi ExpressRoute. Obwody usługi ExpressRoute to zasoby w ramach subskrypcji platformy Azure. Subskrypcja platformy Azure jest wymagana, nawet jeśli łączność jest ograniczona do usług w chmurze firmy Microsoft innych niż Azure, takich jak Microsoft 365.
* Aktywna subskrypcja Microsoft 365 (w przypadku korzystania z usług Microsoft 365 Services). Aby uzyskać więcej informacji, zapoznaj się z sekcją Microsoft 365 specyficzne wymagania tego artykułu.

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

## <a name="microsoft-365"></a>Microsoft 365
Jeśli planujesz włączyć Microsoft 365 w ExpressRoute, przejrzyj następujące dokumenty, aby uzyskać więcej informacji na temat wymagań dotyczących Microsoft 365.

* [Usługa Azure ExpressRoute dla Microsoft 365](/microsoft-365/enterprise/azure-expressroute)
* [Routing z ExpressRoute dla Microsoft 365](/microsoft-365/enterprise/routing-with-expressroute)
* [Wysoka dostępność i tryb failover w usłudze ExpressRoute](https://aka.ms/erhighavailability)
* [Adresy URL Microsoft 365 i zakresy adresów IP](/microsoft-365/enterprise/urls-and-ip-address-ranges)
* [Planowanie sieci i dostrajanie wydajności dla Microsoft 365](/microsoft-365/enterprise/network-planning-and-performance)
* [Planowanie sieci i migracji dla Microsoft 365](/microsoft-365/enterprise/network-and-migration-planning)
* [Integracja Microsoft 365 ze środowiskami lokalnymi](/microsoft-365/enterprise/microsoft-365-integration)
* [Usługa ExpressRoute w usłudze Office 365 — szkoleniowe filmy wideo dla zaawansowanych](https://channel9.msdn.com/series/aer/)

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać więcej informacji na temat ExpressRoute, zobacz [często zadawane pytania](expressroute-faqs.md)dotyczące usługi ExpressRoute.
* Znajdź dostawcę połączenia usługi ExpressRoute. Zobacz artykuł [ExpressRoute partners and peering locations](expressroute-locations.md) (Partnerzy i lokalizacje komunikacji równorzędnej usługi ExpressRoute).
* Zapoznaj się z wymaganiami dotyczącymi [routingu](expressroute-routing.md), [translatora adresów sieciowych](expressroute-nat.md) i [technologii QoS](expressroute-qos.md).
* Skonfiguruj połączenie usługi ExpressRoute.
  * [Create an ExpressRoute circuit (Tworzenie obwodu usługi ExpressRoute)](expressroute-howto-circuit-arm.md)
  * [Configure routing (Konfigurowanie routingu)](expressroute-howto-routing-arm.md)
  * [Link a VNet to an ExpressRoute circuit (Łączenie sieci wirtualnej z obwodem usługi ExpressRoute)](expressroute-howto-linkvnet-arm.md)
