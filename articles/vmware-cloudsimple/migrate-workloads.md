---
title: Rozwiązanie VMware firmy Azure przez CloudSimple — Migrowanie maszyn wirtualnych obciążeń do chmury prywatnej
description: Opisuje sposób migrowania maszyn wirtualnych z lokalnego programu vCenter do CloudSimple prywatnej chmury vCenter
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 851305775d14ed371440434b2fbe9334ec0a21b9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97899324"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-private-cloud-vcenter-environment"></a>Migrowanie maszyn wirtualnych obciążeń z lokalnego środowiska vCenter do chmury prywatnej

Aby migrować maszyny wirtualne z lokalnego centrum danych do prywatnej chmury CloudSimple, dostępne są różne opcje.  Chmura prywatna zapewnia natywny dostęp do programu VMware vCenter, a narzędzia obsługiwane przez program VMware mogą służyć do migracji obciążeń. W tym artykule opisano niektóre opcje migracji programu vCenter.

## <a name="prerequisites"></a>Wymagania wstępne

Migracja maszyn wirtualnych i danych ze środowiska lokalnego centrum dane wymaga połączenia z siecią z centrów prywatnych.  Aby nawiązać połączenie sieciowe, użyj jednej z następujących metod:

* [Połączenie sieci VPN typu lokacja-lokacja](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway) między środowiskiem lokalnym i chmurą prywatną.
* ExpressRoute Global Reach połączenie między lokalnym obwodem usługi ExpressRoute i obwodem CloudSimple ExpressRoute.

Ścieżka sieciowa z lokalnego środowiska vCenter do chmury prywatnej musi być dostępna do migracji maszyn wirtualnych przy użyciu vMotion.  Sieć vMotion na lokalnym serwerze vCenter musi mieć możliwości routingu.  Sprawdź, czy zapora zezwala na cały ruch vMotion między lokalnym programem vCenter i prywatnym chmurą w chmurze. (W chmurze prywatnej Routing w sieci vMotion jest domyślnie skonfigurowany).

## <a name="migrate-isos-and-templates"></a>Migrowanie obrazów ISO i szablonów

Aby tworzyć nowe maszyny wirtualne w chmurze prywatnej, użyj szablonów obrazów ISO i maszyn wirtualnych.  Aby przekazać obrazów ISO i szablony do chmury prywatnej programu vCenter i udostępnić je, użyj następującej metody.

1. Przekaż plik ISO do chmury prywatnej vCenter z interfejsu użytkownika vCenter.
2. [Publikowanie biblioteki zawartości](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html) w chmurze prywatnej vCenter:

    1. Opublikuj lokalną bibliotekę zawartości.
    2. Utwórz nową bibliotekę zawartości na serwerze vCenter w chmurze prywatnej.
    3. Zasubskrybuj opublikowaną lokalną bibliotekę zawartości.
    4. Zsynchronizuj bibliotekę zawartości, aby uzyskać dostęp do subskrybowanej zawartości.

## <a name="migrate-vms-using-powercli"></a>Migrowanie maszyn wirtualnych przy użyciu PowerCLI

Aby przeprowadzić migrację maszyn wirtualnych z lokalnego programu vCenter do chmury prywatnej programu vCenter, użyj programu VMware PowerCLI lub narzędzia migracji obciążenia Cross vCenter dostępnego w oprogramowaniu VMware Labs.  Poniższy przykładowy skrypt przedstawia polecenia migracji PowerCLI.

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> Aby użyć nazw docelowego hosta programu vCenter i ESXi, skonfiguruj przekazywanie DNS z poziomu lokalnego do chmury prywatnej.

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>Migrowanie maszyn wirtualnych przy użyciu sieci VPN NSX warstwy 2

Ta opcja umożliwia migrację na żywo obciążeń z lokalnego środowiska VMware do chmury prywatnej na platformie Azure.  W tej rozciągniętej sieci warstwy 2, podsieć lokalna będzie dostępna w chmurze prywatnej.  Po migracji nowe przypisanie adresu IP nie jest wymagane dla maszyn wirtualnych.

[Migrowanie obciążeń przy użyciu rozproszonych sieci warstwy 2](migration-layer-2-vpn.md) opisuje sposób użycia sieci VPN warstwy 2 do rozciągnięcia sieci warstwy 2 ze środowiska lokalnego do chmury prywatnej.

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>Migrowanie maszyn wirtualnych przy użyciu narzędzi kopii zapasowej i odzyskiwania po awarii

Migrację maszyn wirtualnych do chmury prywatnej można wykonać przy użyciu narzędzi do tworzenia kopii zapasowych i przywracania danych.  Użyj chmury prywatnej jako celu do przywracania z kopii zapasowych utworzonych za pomocą narzędzia innej firmy.  Chmurę prywatną można również użyć jako elementu docelowego do odzyskiwania po awarii przy użyciu oprogramowania VMware SRM lub narzędzia innej firmy.

Aby uzyskać więcej informacji na temat korzystania z tych narzędzi, zobacz następujące tematy:

* [Tworzenie kopii zapasowych maszyn wirtualnych obciążeń w chmurze prywatnej CloudSimple przy użyciu Veeam B&R](backup-workloads-veeam.md)
* [Skonfiguruj chmurę prywatną CloudSimple jako lokację odzyskiwania po awarii dla lokalnych obciążeń programu VMware](disaster-recovery-zerto.md)
