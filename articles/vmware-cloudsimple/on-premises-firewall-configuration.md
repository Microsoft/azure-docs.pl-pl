---
title: Dostęp do rozwiązania VMware platformy Azure przez CloudSimple z lokalnego
titleSuffix: Azure VMware Solution by CloudSimple
description: Uzyskiwanie dostępu do rozwiązania VMware platformy Azure przez CloudSimple z sieci lokalnej za pośrednictwem zapory
author: sharaths-cs
ms.author: dikamath
ms.date: 08/08/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 539665c4756a7dc87078922421b45a88404f58f1
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868140"
---
# <a name="accessing-your-cloudsimple-private-cloud-environment-and-applications-from-on-premises"></a>Uzyskiwanie dostępu do środowiska chmury CloudSimple private cloud i aplikacji z lokalnego

Połączenie można skonfigurować z sieci lokalnej do CloudSimple przy użyciu usługi Azure ExpressRoute lub sieci VPN lokacja lokacja.  Uzyskaj dostęp do usługi CloudSimple Private Cloud vCenter i wszystkich obciążeń uruchamianych w chmurze prywatnej przy użyciu połączenia.  Można kontrolować, jakie porty są otwierane w połączeniu za pomocą zapory w sieci lokalnej.  W tym artykule omówiono niektóre typowe wymagania portu aplikacji.  W przypadku innych aplikacji należy zapoznać się z dokumentacją aplikacji dotyczącą wymagań portów.

## <a name="ports-required-for-accessing-vcenter"></a>Porty wymagane do uzyskania dostępu do vCenter

Aby uzyskać dostęp do programu Private Cloud vCenter i menedżera NSX-T, porty zdefiniowane w poniższej tabeli muszą być otwierane na zaporze lokalnej.  

| Port       | Element źródłowy                           | Element docelowy                      | Przeznaczenie                                                                                                                |
|------------|----------------------------------|----------------------------------|------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)   | Lokalne serwery DNS          | Serwery DNS w chmurze prywatnej        | Wymagane do przekazywania wyszukiwania DNS *az.cloudsimple.io* do serwerów DNS w chmurze prywatnej z sieci lokalnej.       |
| 53 (UDP)   | Serwery DNS w chmurze prywatnej        | Lokalne serwery DNS          | Wymagane do przesyłania dalej wyszukiwania nazw domen lokalnych w systemie DNS z usługi Private Cloud vCenter na lokalne serwery DNS. |
| 80 (TCP)   | Sieć lokalna              | Sieć zarządzania chmurą prywatną | Wymagane do przekierowania adresu URL vCenter z *http* do *https*.                                                           |
| 443 (TCP)  | Sieć lokalna              | Sieć zarządzania chmurą prywatną | Wymagane do uzyskiwania dostępu do vCenter i NSX-T manager z sieci lokalnej.                                             |
| 8000 (TCP) | Sieć lokalna              | Sieć zarządzania chmurą prywatną | Wymagane dla vMotion maszyn wirtualnych z lokalnego do prywatnego chmury.                                            |
| 8000 (TCP) | Sieć zarządzania chmurą prywatną | Sieć lokalna              | Wymagane dla vMotion maszyn wirtualnych z private cloud do lokalnego.                                            |

## <a name="ports-required-for-using-on-premises-active-directory-as-an-identity-source"></a>Porty wymagane do używania lokalnej usługi active directory jako źródła tożsamości

Aby skonfigurować lokalną usługę active directory jako źródło tożsamości w usłudze Private Cloud vCenter, należy otworzyć porty zdefiniowane w tabeli.  Zobacz [Korzystanie z usługi Azure AD jako dostawcy tożsamości dla vCenter w chmurze prywatnej CloudSimple](https://docs.microsoft.com/azure/vmware-cloudsimple/azure-ad/) dla kroków konfiguracji.

| Port         | Element źródłowy                           | Element docelowy                                         | Przeznaczenie                                                                                                                                          |
|--------------|----------------------------------|-----------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------|
| 53 (UDP)      | Serwery DNS w chmurze prywatnej        | Lokalne serwery DNS                             | Wymagane do przesyłania dalej wyszukiwania dns lokalnych nazw domen usługi active directory z private cloud vCenter do lokalnych serwerów DNS.          |
| 389 (TCP/UDP) | Sieć zarządzania chmurą prywatną | Lokalne kontrolery domeny usługi active directory     | Wymagane do komunikacji LDAP z serwera vCenter private cloud do kontrolerów domeny usługi Active Directory do uwierzytelniania użytkowników.                |
| 636 (TCP)     | Sieć zarządzania chmurą prywatną | Lokalne kontrolery domeny usługi active directory     | Wymagane do bezpiecznej komunikacji LDAP (LDAP) z serwera vCenter private cloud do kontrolerów domeny usługi Active Directory w celu uwierzytelniania użytkowników. |
| 3268 (TCP)    | Sieć zarządzania chmurą prywatną | Lokalne serwery wykazu globalnego usługi active directory | Wymagane do komunikacji LDAP we wdrożeniach kontrolera wielu domen.                                                                        |
| 3269 (TCP)    | Sieć zarządzania chmurą prywatną | Lokalne serwery wykazu globalnego usługi active directory | Wymagane do komunikacji LDAPS we wdrożeniach kontrolera wielu domen.                                                                       |                                           |

## <a name="common-ports-required-for-accessing-workload-virtual-machines"></a>Typowe porty wymagane do uzyskiwania dostępu do maszyn wirtualnych obciążenia

Maszyny wirtualne z obciążeniem dostępu działające w chmurze prywatnej wymagają otwarcia portów w zaporze lokalnej.  W poniższej tabeli przedstawiono niektóre typowe porty wymagane i ich przeznaczenie.  W przypadku wymagań dotyczących portów specyficznych dla aplikacji należy zapoznać się z dokumentacją aplikacji.

| Port         | Element źródłowy                         | Element docelowy                          | Przeznaczenie                                                                              |
|--------------|--------------------------------|--------------------------------------|--------------------------------------------------------------------------------------|
| 22 (TCP)      | Sieć lokalna            | Sieć obciążeń chmury prywatnej       | Bezpieczny dostęp powłoki do maszyn wirtualnych systemu Linux działających w chmurze prywatnej.              |
| 3389 (TCP)    | Sieć lokalna            | Sieć obciążeń chmury prywatnej       | Pulpit zdalny do maszyn wirtualnych systemu Windows uruchomionych w chmurze prywatnej.                 |
| 80 (TCP)      | Sieć lokalna            | Sieć obciążeń chmury prywatnej       | Uzyskaj dostęp do wszystkich serwerów sieci web wdrożonych na maszynach wirtualnych uruchomionych w chmurze prywatnej.        |
| 443 (TCP)     | Sieć lokalna            | Sieć obciążeń chmury prywatnej       | Uzyskaj dostęp do wszystkich bezpiecznych serwerów sieci web wdrożonych na maszynach wirtualnych działających w chmurze prywatnej. |
| 389 (TCP/UDP) | Sieć obciążeń chmury prywatnej | Lokalna sieć usługi active directory | Dołącz maszyny wirtualne obciążenia systemu Windows do lokalnej domeny usługi active directory.       |
| 53 (UDP)      | Sieć obciążeń chmury prywatnej | Sieć lokalna                  | Dostęp do usługi DNS dla maszyn wirtualnych obciążenia do lokalnych serwerów DNS.         |

## <a name="next-steps"></a>Następne kroki

* [Tworzenie sieci VLAN i podsieci oraz zarządzanie nimi](https://docs.microsoft.com/azure/vmware-cloudsimple/create-vlan-subnet/)
* [Łączenie się z siecią lokalną przy użyciu usługi Azure ExpressRoute](https://docs.microsoft.com/azure/vmware-cloudsimple/on-premises-connection/)
* [Konfigurowanie sieci VPN między lokacjami z lokalnego](https://docs.microsoft.com/azure/vmware-cloudsimple/vpn-gateway/)
