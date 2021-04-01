---
title: Dostęp do rozwiązań VMware platformy Azure przez CloudSimple z zasobów lokalnych
titleSuffix: Azure VMware Solution by CloudSimple
description: Uzyskiwanie dostępu do rozwiązania VMware platformy Azure przez CloudSimple z sieci lokalnej przez zaporę
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6ff057d99e29c7c6fe30e77f38a0bff265dbe7bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86998899"
---
# <a name="accessing-your-cloudsimple-private-cloud-environment-and-applications-from-on-premises"></a>Uzyskiwanie dostępu do środowiska chmury prywatnej CloudSimple i aplikacji z poziomu lokalnego

Połączenie można skonfigurować z sieci lokalnej do CloudSimple przy użyciu usługi Azure ExpressRoute lub sieci VPN typu lokacja-lokacja.  Uzyskaj dostęp do CloudSimple prywatnej chmury vCenter oraz wszelkich obciążeń, które są uruchamiane w chmurze prywatnej przy użyciu połączenia.  Można kontrolować, które porty są otwierane w ramach połączenia, za pomocą zapory w sieci lokalnej.  W tym artykule omówiono niektóre typowe wymagania dotyczące portów aplikacji.  W przypadku innych aplikacji zapoznaj się z dokumentacją aplikacji dotyczącą wymagań portów.

## <a name="ports-required-for-accessing-vcenter"></a>Porty wymagane do uzyskania dostępu do programu vCenter

Aby można było uzyskać dostęp do Menedżera usługi vCenter i NSX-T chmury prywatnej, porty zdefiniowane w poniższej tabeli muszą być otwarte na zaporze lokalnej.  

| Port       | Element źródłowy                           | Element docelowy                      | Przeznaczenie                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | Lokalne serwery DNS          | Serwery DNS w chmurze prywatnej        | Wymagany do przekazywania wyszukiwania DNS *AZ.cloudsimple.IO* do serwerów DNS w chmurze prywatnej z sieci lokalnej.       |
| 53 (UDP)   | Serwery DNS w chmurze prywatnej        | Lokalne serwery DNS          | Wymagane w celu przeprowadzenia przeszukiwania nazw domen lokalnych z usługi vCenter w chmurze prywatnej do lokalnych serwerów DNS. |
| 80 (TCP)   | Sieć lokalna              | Sieć zarządzania chmurą prywatną | Wymagane do przekierowywania adresu URL programu vCenter z *protokołu HTTP* do *protokołu HTTPS*.                                                           |
| 443 (TCP)  | Sieć lokalna              | Sieć zarządzania chmurą prywatną | Wymagane w celu uzyskania dostępu do Menedżera vCenter i NSX-T z sieci lokalnej.                                             |
| 8000 (TCP) | Sieć lokalna              | Sieć zarządzania chmurą prywatną | Wymagany do vMotion maszyn wirtualnych ze swojej firmy w chmurze prywatnej.                                            |
| 8000 (TCP) | Sieć zarządzania chmurą prywatną | Sieć lokalna              | Wymagane dla vMotion maszyn wirtualnych z chmury prywatnej do lokalnego.                                            |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>Porty wymagane do korzystania z lokalnej usługi Active Directory jako źródła tożsamości

Aby skonfigurować lokalną usługi Active Directory jako źródło tożsamości w programie vCenter w chmurze prywatnej, należy otworzyć porty zdefiniowane w tabeli.  Zobacz [Używanie usługi Azure AD jako dostawcy tożsamości dla programu vCenter w chmurze prywatnej CloudSimple](./azure-ad.md) na potrzeby czynności konfiguracyjnych.

| Port         | Element źródłowy                           | Element docelowy                                         | Przeznaczenie                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | Serwery DNS w chmurze prywatnej        | Lokalne serwery DNS                             | Wymagane w celu przeprowadzenia przekazywania DNS nazwy lokalnych domen usługi Active Directory z chmury prywatnej vCenter do lokalnych serwerów DNS.          |
| 389 (TCP/UDP) | Sieć zarządzania chmurą prywatną | Lokalne kontrolery domeny usługi Active Directory     | Wymagany do komunikacji z usługą LDAP z prywatnego serwera vCenter Cloud do kontrolerów domeny usługi Active Directory na potrzeby uwierzytelniania użytkowników.                |
| 636 (TCP)     | Sieć zarządzania chmurą prywatną | Lokalne kontrolery domeny usługi Active Directory     | Wymagane w celu komunikacji z kontrolerami domeny usługi Active Directory w celu uwierzytelniania użytkowników przy użyciu protokołu Secure LDAP (LDAPs) z prywatnego serwera vCenter Cloud. |
| 3268 (TCP)    | Sieć zarządzania chmurą prywatną | Lokalne serwery wykazu globalnego usługi Active Directory | Wymagane na potrzeby komunikacji z protokołem LDAP w wdrożeniach z wielodomenowym kontrolerem.                                                                        |
| 3269 (TCP)    | Sieć zarządzania chmurą prywatną | Lokalne serwery wykazu globalnego usługi Active Directory | Wymagane w przypadku komunikacji LDAPs w wdrożeniach z wielodomenowym kontrolerem.                                                                       |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>Typowe porty wymagane do uzyskania dostępu do obciążeń maszyn wirtualnych

Dostęp do obciążeń maszyny wirtualne uruchomione w chmurze prywatnej wymagają otwarcia portów na zaporze lokalnej.  W poniższej tabeli przedstawiono niektóre typowe porty wymagane i ich przeznaczenia.  Wymagania dotyczące portów specyficzne dla aplikacji można znaleźć w dokumentacji aplikacji.

| Port         | Element źródłowy                         | Element docelowy                          | Przeznaczenie                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | Sieć lokalna            | Sieć obciążenia chmury prywatnej       | Bezpieczny dostęp powłoki do maszyn wirtualnych z systemem Linux działających w chmurze prywatnej.              |
| 3389 (TCP)    | Sieć lokalna            | Sieć obciążenia chmury prywatnej       | Pulpit zdalny do maszyn wirtualnych z systemem Windows uruchomionych w chmurze prywatnej.                 |
| 80 (TCP)      | Sieć lokalna            | Sieć obciążenia chmury prywatnej       | Uzyskaj dostęp do dowolnych serwerów sieci Web wdrożonych na maszynach wirtualnych działających w chmurze prywatnej.        |
| 443 (TCP)     | Sieć lokalna            | Sieć obciążenia chmury prywatnej       | Uzyskaj dostęp do dowolnych bezpiecznych serwerów sieci Web wdrożonych na maszynach wirtualnych działających w chmurze prywatnej. |
| 389 (TCP/UDP) | Sieć obciążenia chmury prywatnej | Lokalna sieć usługi Active Directory | Przyłączanie maszyn wirtualnych obciążeń systemu Windows do lokalnej domeny usługi Active Directory.       |
| 53 (UDP)      | Sieć obciążenia chmury prywatnej | Sieć lokalna                  | Dostęp usługi DNS do obciążeń maszyn wirtualnych do lokalnych serwerów DNS.         |

## <a name="next-steps"></a>Następne kroki

* [Tworzenie sieci VLAN i podsieci oraz zarządzanie nimi](./create-vlan-subnet.md)
* [Nawiązywanie połączenia z siecią lokalną za pomocą usługi Azure ExpressRoute](./on-premises-connection.md)
* [Konfigurowanie sieci VPN typu lokacja-lokacja z poziomu lokalnego](./vpn-gateway.md)
