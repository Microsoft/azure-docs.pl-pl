---
title: Klaster SAP ASCS/SCS w usłudze WSFC przy użyciu udziału plików na platformie Azure | Microsoft Docs
description: Dowiedz się, jak Klastrować wystąpienie SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udziału plików na platformie Azure.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/15/2021
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a51f874d09aebfcb2c0b73e0b484f68042d1bb6d
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103496205"
---
# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-file-share-in-azure"></a>Klastrowanie wystąpienia SAP ASCS/SCS w klastrze trybu failover systemu Windows przy użyciu udziału plików na platformie Azure

> ![Logo Windows.][Logo_Windows] Windows
>

Klaster pracy awaryjnej systemu Windows Server to podstawa instalacji oprogramowania SAP ASCS/SCS o wysokiej dostępności w systemie Windows.

Klaster trybu failover jest grupą serwerów niezależnych od 1 + n (węzłów), które współpracują ze sobą w celu zwiększenia dostępności aplikacji i usług. Jeśli wystąpi awaria węzła, klaster trybu failover systemu Windows Server oblicza liczbę błędów, które mogą wystąpić i nadal utrzymuje klaster w dobrej kondycji, aby zapewnić aplikacje i usługi. Możesz wybrać inny tryb kworum, aby osiągnąć klaster trybu failover.

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem wykonywania zadań opisanych w tym artykule zapoznaj się z tym artykułem:

* [Architektura Azure Virtual Machines wysoka dostępność i scenariusze dla oprogramowania SAP NetWeaver][sap-high-availability-architecture-scenarios]

> [!IMPORTANT]
> Klastrowanie wystąpień SAP ASCS/SCS za pomocą udziału plików jest obsługiwane w przypadku oprogramowania SAP NetWeaver 7,40 (i nowszych) przy użyciu jądra SAP 7,49 (i nowszych).
>


## <a name="windows-server-failover-clustering-in-azure"></a>Klaster trybu failover z systemem Windows Server na platformie Azure

W porównaniu z wdrożeniami w chmurze bez systemu operacyjnego usługa Azure Virtual Machines wymaga dodatkowych kroków w celu skonfigurowania klastra trybu failover systemu Windows Server. Podczas tworzenia klastra należy ustawić kilka adresów IP i nazw hostów wirtualnych dla wystąpienia SAP ASCS/SCS.

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Rozpoznawanie nazw na platformie Azure i nazwa hosta wirtualnego klastra

Platforma Azure Cloud Platform nie oferuje opcji konfigurowania wirtualnych adresów IP, takich jak zmiennoprzecinkowe adresy IP. Do skonfigurowania wirtualnego adresu IP w celu uzyskania dostępu do zasobu klastra w chmurze potrzebne jest alternatywne rozwiązanie. 

Usługa Azure Load Balancer udostępnia *wewnętrzny moduł równoważenia obciążenia* dla platformy Azure. W przypadku wewnętrznego modułu równoważenia obciążenia klienci docierają do klastra za pośrednictwem wirtualnego adresu IP klastra. 

Wdróż wewnętrzny moduł równoważenia obciążenia w grupie zasobów zawierającej węzły klastra. Następnie należy skonfigurować wszystkie wymagane reguły przekazywania portów przy użyciu portów sondy wewnętrznego modułu równoważenia obciążenia. Klienci mogą łączyć się za pośrednictwem nazwy hosta wirtualnego. Serwer DNS rozpoznaje adres IP klastra. Wewnętrzny moduł równoważenia obciążenia obsługuje przekazywanie portów do aktywnego węzła klastra.

![Rysunek 1. Konfiguracja klastra trybu failover z systemem Windows Server na platformie Azure bez dysku udostępnionego][sap-ha-guide-figure-1001]

_**Rysunek 1.** Konfiguracja klastra trybu failover systemu Windows Server na platformie Azure bez dysku udostępnionego_

## <a name="sap-ascsscs-ha-with-file-share"></a>SAP ASCS/SCS HA z udziałem plików

System SAP opracował nowe podejście i alternatywę dla udostępnionych dysków klastra, na potrzeby klastrowania wystąpienia SAP ASCS/SCS w klastrze trybu failover systemu Windows. Zamiast korzystać z udostępnionych dysków klastra, można użyć udziału plików SMB do wdrożenia plików hosta globalnego SAP.

> [!NOTE]
> Udział plików SMB jest alternatywnym rozwiązaniem do korzystania z udostępnionych dysków klastra na potrzeby klastrowania wystąpień SAP ASCS/SCS.  
>

Ta architektura jest specyficzna dla następujących sposobów:

* Usługi SAP Central (z własną strukturą plików i procesami dodawania komunikatów i kolejkowania) są niezależne od plików hosta globalnego SAP.
* Usługi SAP Central działają w wystąpieniu SAP ASCS/SCS.
* Wystąpienie SAP ASCS/SCS jest klastrowane i jest dostępne przy użyciu \<ASCS/SCS virtual host name\> nazwy hosta wirtualnego.
* Pliki globalne SAP są umieszczane w udziale plików SMB i są dostępne przy użyciu \<SAP global host\> nazwy hosta: \\ \\ &lt; Global Host SAP &gt; \sapmnt \\ &lt; SID &gt; \SYS \. ..
* Wystąpienie SAP ASCS/SCS jest zainstalowane na dysku lokalnym na obu węzłach klastra.
* \<ASCS/SCS virtual host name\>Nazwa sieci różni się od &lt; hosta globalnego SAP &gt; .

![Rysunek 2: architektura SAP ASCS/SCS z udziałem plików SMB][sap-ha-guide-figure-8004]

_**Rysunek 2.** Nowa architektura oprogramowania SAP ASCS/SCS z udziałem plików SMB_

Wymagania wstępne dotyczące udziału plików SMB:

* Protokół SMB 3,0 (lub nowszy).
* Możliwość ustawienia Active Directory list kontroli dostępu (ACL) dla Active Directory grup użytkowników i `computer$` obiektu komputera.
* Udział plików musi mieć włączoną obsługę HA:
    * Dyski używane do przechowywania plików nie mogą być single point of failure.
    * Przestój serwera lub maszyny wirtualnej nie powoduje przestoju w udziale plików.

\<SID\>Rola klastra SAP nie zawiera udostępnionych dysków klastra ani zasobu klastra ogólnego udziału plików.


![Rysunek 3. \< \> zasoby roli klastra usługi SAP SID dotyczące korzystania z udziału plików][sap-ha-guide-figure-8005]

_**Rysunek 3.** &lt; &gt; Zasoby roli klastra usługi SAP SID do korzystania z udziału plików_


## <a name="scale-out-file-shares-with-storage-spaces-direct-in-azure-as-an-sapmnt-file-share"></a>Udziały plików skalowalne w poziomie z Bezpośrednie miejsca do magazynowania na platformie Azure jako udział plików SAPMNT

Udziału plików skalowalnego w poziomie można używać do hostowania i ochrony plików hosta globalnego SAP. Udział plików skalowalny w poziomie oferuje również SAPMNT usługę udziału plików o wysokiej dostępności.

![Rysunek 4: udział plików skalowalny w poziomie używany do ochrony plików hosta globalnego SAP][sap-ha-guide-figure-8006]

_**Rysunek 4.** Udział plików skalowalny w poziomie używany do ochrony plików hosta globalnego SAP_

> [!IMPORTANT]
> Udziały plików skalowalne w poziomie są w pełni obsługiwane w chmurze Microsoft Azure i w środowiskach lokalnych.
>

Udział plików skalowalny w poziomie oferuje wysoką dostępność i skalowalny SAPMNT udział plików.

Bezpośrednie miejsca do magazynowania jest używany jako dysk udostępniony dla udziału plików skalowalnego w poziomie. Za pomocą Bezpośrednie miejsca do magazynowania można kompilować wysoce dostępny i skalowalny magazyn przy użyciu serwerów z magazynem lokalnym. Magazyn udostępniony, który jest używany w udziale plików skalowalnym w poziomie, taki jak dla plików hosta globalnego SAP, nie jest single point of failure.

Podczas wybierania Bezpośrednie miejsca do magazynowania należy wziąć pod uwagę następujące przypadki użycia:

- Maszyny wirtualne używane do budowania klastra Bezpośrednie miejsca do magazynowania muszą zostać wdrożone w zestawie dostępności platformy Azure.
- W przypadku odzyskiwania po awarii klastra Bezpośrednie miejsca do magazynowania można użyć [usługi Azure Site Recovery](../../../site-recovery/azure-to-azure-support-matrix.md#replicated-machines---storage).
- Nie jest obsługiwane rozciąganie klastra bezpośredniego miejsca do magazynowania między różnymi Strefy dostępności platformy Azure.

### <a name="sap-prerequisites-for-scale-out-file-shares-in-azure"></a>Wymagania wstępne dotyczące oprogramowania SAP dla udziałów plików skalowalnych w poziomie na platformie Azure

Aby można było użyć udziału plików skalowalnego w poziomie, system musi spełniać następujące wymagania:

* Co najmniej dwa węzły klastra dla udziału plików skalowalnego w poziomie.
* Każdy węzeł musi mieć co najmniej dwa dyski lokalne.
* Ze względu na wydajność należy użyć funkcji *odporności dublowania*:
    * Dwukierunkowa dublowanie dla udziału plików skalowalnego w poziomie z dwoma węzłami klastra.
    * Trójwymiarowe dublowanie dla udziału plików skalowalnego w poziomie z trzema (lub więcej) węzłami klastra.
* Zaleca się, aby w udziale plików skalowalnym w poziomie co najmniej trzy węzły klastra z funkcją dublowania.
    Ta konfiguracja zapewnia większą skalowalność i odporność magazynu niż konfiguracja udziału plików skalowalnego w poziomie z dwoma węzłami klastra i dublowaniem dwukierunkowym.
* Musisz użyć dysków Azure Premium.
* Zalecamy korzystanie z usługi Azure Managed Disks.
* Zalecamy formatowanie woluminów przy użyciu systemu plików ReFS.
    * Aby uzyskać więcej informacji, zobacz temat [SAP uwaga 1869038 — obsługa SAP dla systemu plików ReFs][1869038] oraz rozdział [plików][planning-volumes-s2d-choosing-filesystem] w artykule Planowanie woluminów w bezpośrednie miejsca do magazynowania.
    * Pamiętaj, aby zainstalować [aktualizację zbiorczą programu Microsoft KB4025334][kb4025334].
* Można użyć DS-Series lub DSv2-Series rozmiarów maszyn wirtualnych platformy Azure.
* Aby zapewnić dobrą wydajność sieci między maszynami wirtualnymi, które są niezbędne do Bezpośrednie miejsca do magazynowania synchronizacji dysków, użyj typu maszyny wirtualnej z co najmniej przepustowością sieci "High".
    Aby uzyskać więcej informacji, zobacz specyfikacje [serii DSv2][dv2-series] i [ds][ds-series] .
* Zalecamy zarezerwowanie pewnej nieprzypisanej pojemności w puli magazynów. Pozostawienie nieprzypisanej pojemności w puli magazynów powoduje, że miejsce na woluminy do naprawy "w miejscu" w przypadku awarii dysku. Zwiększa to bezpieczeństwo i wydajność danych.  Aby uzyskać więcej informacji, zobacz [Wybieranie rozmiaru woluminu][choosing-the-size-of-volumes-s2d].
* Nie trzeba konfigurować wewnętrznego modułu równoważenia obciążenia platformy Azure dla nazwy sieciowej udziału plików skalowalnego w poziomie, na przykład dla programu \<SAP global host\> . Jest to realizowane w przypadku \<ASCS/SCS virtual host name\> wystąpienia SAP ASCS/SCS lub systemu DBMS. Udział plików skalowalny w poziomie skaluje obciążenie we wszystkich węzłach klastra. \<SAP global host\> używa lokalnego adresu IP dla wszystkich węzłów klastra.


> [!IMPORTANT]
> Nie można zmienić nazwy udziału plików SAPMNT, który wskazuje na \<SAP global host\> . SAP obsługuje tylko nazwę udziału "sapmnt".
>
> Aby uzyskać więcej informacji, zobacz temat [SAP uwaga 2492395 — czy nazwa udziału sapmnt być zmieniona?][2492395]

### <a name="configure-sap-ascsscs-instances-and-a-scale-out-file-share-in-two-clusters"></a>Skonfiguruj wystąpienia SAP ASCS/SCS i udział plików skalowalny w poziomie w dwóch klastrach

Wystąpienia SAP ASCS/SCS należy wdrożyć w osobnym klastrze z własną \<SID\> rolą klastra SAP. W takim przypadku należy skonfigurować udział plików skalowalny w poziomie w innym klastrze przy użyciu innej roli klastra.


> [!IMPORTANT]
> Instalator musi spełniać następujące wymagania: wystąpienia SAP ASCS/SCS i udział SOFS muszą zostać wdrożone w oddzielnych klastrach.    
>
> [!IMPORTANT] 
> W tym scenariuszu wystąpienie SAP ASCS/SCS jest skonfigurowane do uzyskiwania dostępu do hosta globalnego SAP przy użyciu ścieżki UNC \\ \\ &lt; hosta globalnego SAP &gt; \sapmnt \\ &lt; SID &gt; \SYS\.
>

![Rysunek 5: wystąpienie SAP ASCS/SCS oraz udział plików skalowalny w poziomie wdrożony w dwóch klastrach][sap-ha-guide-figure-8007]

_**Rysunek 5.** Wystąpienie SAP ASCS/SCS i udział plików skalowalny w poziomie wdrożony w dwóch klastrach_

> [!IMPORTANT]
> W chmurze platformy Azure każdy klaster, który jest używany w przypadku udziałów plików SAP i skalowalnych w poziomie, musi zostać wdrożony we własnym zestawie dostępności platformy Azure lub w Strefy dostępności platformy Azure. Zapewnia to rozproszone umieszczanie maszyn wirtualnych klastra w ramach podstawowej infrastruktury platformy Azure. Wdrożenia stref dostępności są obsługiwane w tej technologii.
>

## <a name="generic-file-share-with-sios-datakeeper-as-cluster-shared-disks"></a>Ogólny udział plików z oprogramowanie SIOS DataKeeper jako udostępnionymi dyskami klastra


Ogólny udział plików jest kolejną opcją do osiągnięcia udziału plików o wysokiej dostępności.

W takim przypadku można użyć rozwiązania oprogramowanie SIOS innej firmy jako dysku udostępnionego klastra.

## <a name="next-steps"></a>Następne kroki

* [Przygotowanie infrastruktury platformy Azure dla oprogramowania SAP HA przy użyciu klastra trybu failover systemu Windows i udziału plików dla wystąpienia oprogramowania SAP ASCS/SCS][sap-high-availability-infrastructure-wsfc-file-share]
* [Instalowanie oprogramowania SAP NetWeaver HA w klastrze trybu failover systemu Windows i udziału plików dla wystąpienia SAP ASCS/SCS][sap-high-availability-installation-wsfc-shared-disk]
* [Wdrażanie serwera plików skalowalnego w poziomie z dwoma węzłami Bezpośrednie miejsca do magazynowania dla magazynu UPD na platformie Azure][deploy-sofs-s2d-in-azure]
* [Bezpośrednie miejsca do magazynowania w systemie Windows Server 2016][s2d-in-win-2016]
* [Głębokie szczegółowe: woluminy w Bezpośrednie miejsca do magazynowania][deep-dive-volumes-in-s2d]

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[2492395]:https://launchpad.support.sap.com/#/notes/2492395

[kb4025334]:https://support.microsoft.com/help/4025334/windows-10-update-kb4025334

[dv2-series]:../../dv2-dsv2-series.md
[ds-series]:https://docs.microsoft.com/azure/virtual-machines/windows/sizes-general

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md

[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-guide-wsfc-shared-disk]:sap-high-availability-guide-wsfc-shared-disk.md
[sap-high-availability-guide-wsfc-file-share]:sap-high-availability-guide-wsfc-file-share.md
[sap-ascs-high-availability-multi-sid-wsfc]:sap-ascs-high-availability-multi-sid-wsfc.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-infrastructure-wsfc-file-share]:sap-high-availability-infrastructure-wsfc-file-share.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md
[sap-hana-ha]:sap-hana-high-availability.md
[sap-suse-ascs-ha]:high-availability-guide-suse.md

[planning-volumes-s2d-choosing-filesystem]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-filesystem
[choosing-the-size-of-volumes-s2d]:https://docs.microsoft.com/windows-server/storage/storage-spaces/plan-volumes#choosing-the-size-of-volumes
[deploy-sofs-s2d-in-azure]:https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment
[s2d-in-win-2016]:https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview
[deep-dive-volumes-in-s2d]:https://blogs.technet.microsoft.com/filecab/2016/08/29/deep-dive-volumes-in-spaces-direct/

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (Konfiguracja wysokiej dostępności dla oprogramowania SAP)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/1002-wsfc-sios-on-azure-ilb.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md

[1869038]:https://launchpad.support.sap.com/#/notes/1869038 
