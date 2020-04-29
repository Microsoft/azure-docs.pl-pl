---
title: Azure VMware Solutions według CloudSimple — Bezpieczna chmura prywatna
description: Opisuje sposób zabezpieczania rozwiązań VMware platformy Azure według chmury prywatnej CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4541874a9e8fc4111e5c65d02f07535c4d14f9f1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77565982"
---
# <a name="how-to-secure-your-private-cloud-environment"></a>Jak zabezpieczyć środowisko chmury prywatnej

Zdefiniowanie kontroli dostępu opartej na rolach (RBAC) dla usługi CloudSimple, portalu CloudSimple i chmury prywatnej na platformie Azure.  Użytkownicy, grupy i role do uzyskiwania dostępu do programu vCenter w chmurze prywatnej są określane przy użyciu logowania jednokrotnego w oprogramowaniu VMware.  

## <a name="rbac-for-cloudsimple-service"></a>RBAC dla usługi CloudSimple

Tworzenie usługi CloudSimple wymaga roli **właściciela** lub **współautora** w ramach subskrypcji platformy Azure.  Domyślnie wszyscy właściciele i Współautorzy mogą tworzyć usługi CloudSimple i uzyskiwać dostęp do portalu CloudSimple na potrzeby tworzenia chmur prywatnych i zarządzania nimi.  Na region można utworzyć tylko jedną usługę CloudSimple.  Aby ograniczyć dostęp do określonych administratorów, wykonaj poniższą procedurę.

1. Utwórz usługę CloudSimple w nowej **grupie zasobów** na Azure Portal
2. Określ wartość RBAC dla grupy zasobów.
3. Kup węzły i Użyj tej samej grupy zasobów co usługa CloudSimple

Tylko użytkownicy posiadający uprawnienia **właściciela** lub **współautora** w grupie zasobów będą widzieć usługę CloudSimple i uruchamiać Portal CloudSimple.

Aby uzyskać więcej informacji na temat RBAC, zobacz [co to jest kontrola dostępu oparta na rolach (RBAC) dla zasobów platformy Azure](../role-based-access-control/overview.md).

## <a name="rbac-for-private-cloud-vcenter"></a>RBAC dla chmury prywatnej — vCenter

Podczas tworzenia chmury `CloudOwner@cloudsimple.local` prywatnej jest tworzony domyślny użytkownik w DOMENIE vCenter SSO.  Użytkownik CloudOwner ma uprawnienia do zarządzania programem vCenter. Do programu vCenter SSO są dodawane dodatkowe źródła tożsamości w celu udzielenia dostępu innym użytkownikom.  Wstępnie zdefiniowane role i grupy są konfigurowane w programie vCenter, który może służyć do dodawania kolejnych użytkowników.

### <a name="add-new-users-to-vcenter"></a>Dodawanie nowych użytkowników do programu vCenter

1. [Eskalować uprawnienia](escalate-private-cloud-privileges.md) do **CloudOwner\@Cloudsimple. Local** User w chmurze prywatnej.
2. Zaloguj się do programu vCenter przy użyciu **CloudOwner\@cloudsimple. Local**
3. [Dodaj użytkowników korzystających z logowania](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-72BFF98C-C530-4C50-BF31-B5779D2A4BBB.html)jednokrotnego w programie vCenter.
4. Dodawanie użytkowników do [grup programu vCenter Logowanie jednokrotne](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

Aby uzyskać więcej informacji na temat wstępnie zdefiniowanych ról i grup, zobacz [model uprawnień chmury prywatnej CloudSimple firmy VMware vCenter](learn-private-cloud-permissions.md) .

### <a name="add-new-identity-sources"></a>Dodaj nowe źródła tożsamości

Możesz dodać dodatkowych dostawców tożsamości dla domeny vCenter SSO w chmurze prywatnej.  Dostawcy tożsamości udostępniają uwierzytelnianie i grupy vCenter logowania jednokrotnego zapewniają autoryzację dla użytkowników.

* [Użyj Active Directory jako dostawcy tożsamości](set-vcenter-identity.md) w usłudze vCenter w chmurze prywatnej.
* [Korzystanie z usługi Azure AD jako dostawcy tożsamości](azure-ad.md) w usłudze vCenter w chmurze prywatnej

1. [Eskalować uprawnienia](escalate-private-cloud-privileges.md) do **CloudOwner\@Cloudsimple. Local** User w chmurze prywatnej.
2. Zaloguj się do programu vCenter przy użyciu **CloudOwner\@cloudsimple. Local**
3. Dodaj użytkowników z dostawcy tożsamości do [grup usługi vCenter Logowanie jednokrotne](https://docs.vmware.com/en/VMware-vSphere/5.5/com.vmware.vsphere.security.doc/GUID-CDEA6F32-7581-4615-8572-E0B44C11D80D.html).

## <a name="secure-network-on-your-private-cloud-environment"></a>Zabezpieczanie sieci w środowisku chmury prywatnej

Zabezpieczenia sieci środowiska chmury prywatnej są kontrolowane przez zabezpieczenie dostępu do sieci i sterowanie ruchem sieciowym między zasobami.

### <a name="access-to-private-cloud-resources"></a>Dostęp do zasobów chmury prywatnej

Dostęp do chmury prywatnej i dostępu do zasobów odbywa się za pośrednictwem bezpiecznego połączenia sieciowego:

* **[Połączenie ExpressRoute](on-premises-connection.md)**. ExpressRoute zapewnia bezpieczne połączenie o wysokiej przepustowości i małych opóźnieniach ze środowiska lokalnego.  Użycie połączenia umożliwia lokalnym usługom, sieciom i użytkownikom uzyskiwanie dostępu do aplikacji vCenter w chmurze prywatnej.
* **[Brama sieci VPN typu lokacja-lokacja](vpn-gateway.md)**. Połączenie sieci VPN typu lokacja-lokacja umożliwia dostęp do zasobów chmury prywatnej ze źródeł lokalnych za pośrednictwem bezpiecznego tunelu.  Należy określić, które sieci lokalne mogą wysyłać i odbierać ruch sieciowy w chmurze prywatnej.
* **[Brama sieci VPN typu punkt-lokacja](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)**. Użyj połączenia sieci VPN typu punkt-lokacja, aby uzyskać szybki dostęp zdalny do programu vCenter w chmurze prywatnej.

### <a name="control-network-traffic-in-private-cloud"></a>Sterowanie ruchem sieciowym w chmurze prywatnej

Tabele i reguły zapory kontrolują ruch sieciowy w chmurze prywatnej.  Tabela zapory pozwala kontrolować ruch sieciowy między siecią źródłową lub adresem IP a siecią docelową lub adresem IP na podstawie kombinacji reguł zdefiniowanych w tabeli.

1. Utwórz [tabelę zapory](firewall.md#add-a-new-firewall-table).
2. [Dodaj reguły](firewall.md#create-a-firewall-rule) do tabeli zapory.
3. [Dołącz tabelę zapory do sieci VLAN/podsieci](firewall.md#attach-vlans-subnet).
