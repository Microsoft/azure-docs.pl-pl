---
title: Rozwiązanie VMware platformy Azure — przekazywanie DNS z chmury prywatnej do lokalnego
description: Zawiera opis sposobu włączania wyszukiwania zasobów lokalnych w chmurze prywatnej usługi CloudSimple
author: Ajayan1008
ms.author: v-hborys
ms.date: 02/29/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3481e21a93fbf5f658c5ca00513ea179c8f95400
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899205"
---
# <a name="enable-cloudsimple-private-cloud-dns-servers-to-forward-dns-lookup-of-on-premises-resources-to-your-dns-servers"></a>Włącz serwery DNS chmury prywatnej CloudSimple, aby przekazywać wyszukiwanie w systemie DNS zasobów lokalnych do serwerów DNS

Serwery DNS w chmurze prywatnej mogą przekazywać wyszukiwanie DNS dla wszystkich zasobów lokalnych do serwerów DNS.  Włączenie wyszukiwania umożliwia składnikom vSphere w chmurze prywatnej wyszukiwanie wszystkich usług działających w środowisku lokalnym i komunikowanie się z nimi przy użyciu w pełni kwalifikowanych nazw domen (FQDN).

## <a name="scenarios"></a>Scenariusze 

Przekazanie wyszukiwania DNS dla lokalnego serwera DNS umożliwia korzystanie z chmury prywatnej w następujących scenariuszach:

* Korzystanie z chmury prywatnej jako konfiguracji odzyskiwania po awarii dla lokalnego rozwiązania VMware
* Używaj Active Directory lokalnych jako źródła tożsamości dla chmury prywatnej vSphere
* Korzystanie z HCX do migrowania maszyn wirtualnych z lokalizacji lokalnej do chmury prywatnej

## <a name="before-you-begin"></a>Przed rozpoczęciem

Połączenie sieciowe musi znajdować się w sieci prywatnej chmury w sieci lokalnej na potrzeby przesyłania dalej DNS.  Połączenie sieciowe można skonfigurować przy użyciu:

* [Nawiązywanie połączenia z poziomu lokalnego z CloudSimple za pomocą ExpressRoute](on-premises-connection.md)
* [Konfigurowanie bramy sieci VPN typu lokacja-lokacja](./vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)

Aby przekazywanie DNS działało, należy otworzyć porty zapory dla tego połączenia.  Używane porty to port TCP 53 lub port UDP 53.

> [!NOTE]
> W przypadku korzystania z sieci VPN typu lokacja-lokacja należy dodać lokalną podsieć serwera DNS jako część prefiksów lokalnych.

## <a name="request-dns-forwarding-from-private-cloud-to-on-premises"></a>Żądaj przesyłania dalej DNS z chmury prywatnej do lokalnego

Aby włączyć przesyłanie dalej DNS z chmury prywatnej do lokalnego, Prześlij [żądanie pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), dostarczając poniższe informacje.

* Typ problemu: **techniczne**
* Subskrypcja: **subskrypcja, w ramach której wdrożono usługę CloudSimple**
* Usługa: **rozwiązanie VMware według CloudSimple**
* Typ problemu: **poradnik lub jak mogę...**
* Podtyp problemu: **potrzebna pomoc w połączeniu z NW**
* W okienku szczegółów Podaj nazwę domeny domeny lokalnej.
* Podaj listę lokalnych serwerów DNS, do których odnośnik będzie przekazywany z chmury prywatnej w okienku szczegółów.

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej o konfiguracji zapory lokalnej](on-premises-firewall-configuration.md)
* [Konfiguracja lokalnego serwera DNS](on-premises-dns-setup.md)
