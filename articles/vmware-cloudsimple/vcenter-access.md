---
title: Rozwiązanie VMware firmy Azure według CloudSimple — dostęp do klienta vSphere
description: Opisuje, jak uzyskać dostęp do programu vCenter w chmurze prywatnej.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/30/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2e62042a84f7ac2615762e3d9b036e4340bd8c73
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899120"
---
# <a name="access-your-private-cloud-vcenter-portal"></a>Dostęp do portalu vCenter w chmurze prywatnej

Portal programu vCenter w chmurze prywatnej można uruchomić z portalu Azure Portal lub CloudSimple.  Portal vCenter umożliwia zarządzanie infrastrukturą VMware w chmurze prywatnej.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Należy nawiązać połączenie sieciowe, a rozpoznawanie nazw DNS musi być włączone, aby można było uzyskać dostęp do portalu vCenter.  Możesz nawiązać połączenie sieciowe z chmurą prywatną, korzystając z dowolnej z poniższych opcji.

* [Nawiązywanie połączenia z poziomu lokalnego z CloudSimple za pomocą ExpressRoute](on-premises-connection.md)
* [Skonfiguruj połączenie sieci VPN z chmurą prywatną CloudSimple](set-up-vpn.md)

Aby skonfigurować rozpoznawanie nazw DNS składników infrastruktury VMware chmury prywatnej, zobacz [Konfigurowanie systemu DNS do rozpoznawania nazw dla prywatnego dostępu vCenter w chmurze z lokalnej stacji roboczej](on-premises-dns-setup.md)

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="access-vcenter-from-azure-portal"></a>Dostęp do programu vCenter z Azure Portal

Portal programu vCenter w chmurze prywatnej można uruchomić na podstawie Azure Portal.

1. Wybierz pozycję **Wszystkie usługi**.

2. Wyszukaj **usługi CloudSimple Services**.

3. Wybierz usługę CloudSimple prywatnej chmury, z którą chcesz nawiązać połączenie.

4. Na stronie **Przegląd** kliknij pozycję **Wyświetl chmurę prywatną VMware.**

    ![Przegląd usługi CloudSimple](media/cloudsimple-service-overview.png)

5. Wybierz chmurę prywatną z listy chmur prywatnych, a następnie kliknij pozycję **Uruchom klienta vSphere**.

    ![Uruchom klienta vSphere](media/cloudsimple-service-launch-vsphere-client.png)

## <a name="access-vcenter-from-cloudsimple-portal"></a>Dostęp do programu vCenter z portalu CloudSimple

Portal programu vCenter w chmurze prywatnej można uruchomić z poziomu portalu CloudSimple.

1. Uzyskaj dostęp do [portalu CloudSimple](access-cloudsimple-portal.md).

2. Z **zasobów** Wybierz chmurę prywatną, do której chcesz uzyskać dostęp, a następnie kliknij pozycję **Uruchom klienta vSphere**.

    ![Uruchom vSphere klienta-zasoby](media/cloudsimple-portal-resources-launch-vcenter.png)

3. Możesz również uruchomić program vCenter Portal z poziomu ekranu podsumowania chmury prywatnej.

    ![Uruchom vSphere klienta — podsumowanie](media/cloudsimple-resources-summary-launch-vcenter.png)

## <a name="next-steps"></a>Następne kroki

* [Tworzenie sieci VLAN i podsieci dla chmur prywatnych oraz zarządzanie nimi](create-vlan-subnet.md)
* [Model uprawnień chmury prywatnej CloudSimple firmy VMware vCenter](learn-private-cloud-permissions.md)