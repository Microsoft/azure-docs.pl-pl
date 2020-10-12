---
title: Wskazówki dotyczące planowania Azure NetApp Files sieci | Microsoft Docs
description: Opisuje wskazówki, które mogą pomóc w zaprojektowaniu efektywnej architektury sieci przy użyciu Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: ram-kakani
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: ramakk
ms.openlocfilehash: a8d81acc0fcb4afa0f981fca3fd099296a0361df
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89569496"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Wytyczne dotyczące planowania sieci usługi Azure NetApp Files

Planowanie architektury sieci to kluczowy element projektowania dowolnej infrastruktury aplikacji. Ten artykuł pomaga w projektowaniu efektywnej architektury sieci na potrzeby obciążeń, aby korzystać z zaawansowanych możliwości Azure NetApp Files.

Woluminy Azure NetApp Files są zaprojektowane w celu zapełnienia w podsieć specjalnego przeznaczenia o nazwie [delegowana podsieć](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet) w ramach Virtual Network platformy Azure. Z tego względu można uzyskać dostęp do woluminów bezpośrednio z sieci wirtualnej, z sieci wirtualnych komunikacji równorzędnej w tym samym regionie lub lokalnie za pośrednictwem bramy Virtual Network (ExpressRoute lub VPN Gateway) w razie potrzeby. Podsieć jest przeznaczona dla Azure NetApp Files i nie ma łączności z innymi usługami platformy Azure ani z Internetem.

## <a name="considerations"></a>Zagadnienia do rozważenia  

Należy pamiętać o kilku kwestiach związanych z planowaniem Azure NetApp Files sieci.

### <a name="constraints"></a>Ograniczenia

Poniższe funkcje nie są obecnie obsługiwane w przypadku Azure NetApp Files: 

* Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) zastosowane do delegowanej podsieci
* Trasy zdefiniowane przez użytkownika (UDR) zastosowane do delegowanej podsieci
* Zasady platformy Azure (na przykład niestandardowe zasady nazewnictwa) w interfejsie Azure NetApp Files
* Usługi równoważenia obciążenia dla ruchu Azure NetApp Filesowego
* Azure Virtual WAN 
* Strefy nadmiarowe Virtual Network bramy (jednostki SKU bramy za pomocą AZ) 
* Aktywne/aktywne Virtual Network GWs 
* Sieć wirtualna z podwójnym stosem (IPv4 i IPv6)

Następujące ograniczenia dotyczące sieci mają zastosowanie do Azure NetApp Files:

* Liczba adresów IP używanych w sieci wirtualnej z Azure NetApp Files (w tym sieci wirtualnych komunikacji równorzędnej) nie może przekraczać 1000. Pracujemy nad zwiększeniem tego limitu, aby spełnić wymagania dotyczące skalowania klientów. 
* W każdej sieci wirtualnej Azure Virtual Network (VNet) można delegować tylko jedną podsieć do usługi Azure NetApp Files.


### <a name="supported-network-topologies"></a>Obsługiwane topologie sieci

W poniższej tabeli opisano topologie sieci obsługiwane przez Azure NetApp Files.  Opisano w nim również obejścia dla nieobsługiwanych topologii. 

|    Topologie    |    Jest obsługiwana    |     Obejście    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    Łączność z woluminem w lokalnej sieci wirtualnej    |    Tak    |         |
|    Łączność z woluminem w równorzędnej sieci wirtualnej (ten sam region)    |    Tak    |         |
|    Łączność z woluminem w równorzędnej sieci wirtualnej (między regionem lub globalną komunikację równorzędną)    |    Nie    |    Brak    |
|    Łączność z woluminem za pośrednictwem bramy ExpressRoute    |    Tak    |         |
|    Łączność z miejsca lokalnego do woluminu w sieci wirtualnej szprych za pośrednictwem bramy ExpressRoute i komunikacji równorzędnej sieci wirtualnej z tranzytem bramy    |    Tak    |        |
|    Łączność między lokacjami lokalnymi a woluminem w sieci wirtualnej szprych za pośrednictwem bramy sieci VPN    |    Tak    |         |
|    Łączność między lokacjami lokalnymi a woluminem w sieci wirtualnej szprych za pośrednictwem bramy VPN i komunikacji równorzędnej sieci wirtualnej z tranzytem bramy    |    Tak    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Sieć wirtualna dla woluminów Azure NetApp Files

W tej sekcji opisano pojęcia, które ułatwiają planowanie sieci wirtualnych.

### <a name="azure-virtual-networks"></a>Sieci wirtualne platformy Azure

Przed zainicjowaniem obsługi woluminu Azure NetApp Files należy utworzyć sieć wirtualną platformy Azure lub użyć jej, która już istnieje w subskrypcji. Sieć wirtualna definiuje granicę sieci dla woluminu.  Aby uzyskać więcej informacji na temat tworzenia sieci wirtualnych, zobacz [dokumentację usługi Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

### <a name="subnets"></a>Podsieci

Podsieci segmentuje sieć wirtualną w oddzielnych przestrzeniach adresowych, które są używane przez zasoby platformy Azure.  Woluminy Azure NetApp Files są zawarte w podsieci w specjalnym przeznaczeniu nazywanym [delegowaną podsiecią](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet). 

Delegowanie podsieci daje jawne uprawnienia do usługi Azure NetApp Files do tworzenia zasobów specyficznych dla usługi w podsieci.  Używa unikatowego identyfikatora podczas wdrażania usługi. W takim przypadku tworzony jest interfejs sieciowy, aby umożliwić łączność z Azure NetApp Files.

Jeśli używasz nowej sieci wirtualnej, możesz utworzyć podsieć i delegować podsieć do Azure NetApp Files, wykonując instrukcje podane w temacie [delegowanie podsieci do Azure NetApp Files](azure-netapp-files-delegate-subnet.md). Istnieje również możliwość delegowania istniejącej pustej podsieci, która nie została jeszcze delegowana do innych usług.

Jeśli sieć wirtualna jest połączona z inną siecią wirtualną, nie można rozszerzyć przestrzeni adresowej sieci wirtualnej. Z tego powodu należy utworzyć nową delegowaną podsieć w obszarze adresów sieci wirtualnej. Jeśli musisz rozszerzyć przestrzeń adresową, musisz usunąć komunikację równorzędną sieci wirtualnej przed zwiększeniem przestrzeni adresowej.

### <a name="udrs-and-nsgs"></a>UDR i sieciowych grup zabezpieczeń

Trasy zdefiniowane przez użytkownika (UDR) i sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) nie są obsługiwane w delegowanych podsieciach dla Azure NetApp Files. Można jednak zastosować UDR i sieciowych grup zabezpieczeń do innych podsieci, nawet w ramach tej samej sieci wirtualnej co podsieć delegowana do Azure NetApp Files.

* UDR następnie zdefiniuj przepływy ruchu z innych podsieci do delegowanej podsieci Azure NetApp Files. Pomaga to zapewnić, że jest to wyrównane do przepływu ruchu z powrotem z Azure NetApp Files do innych podsieci przy użyciu tras systemowych.  
* Sieciowych grup zabezpieczeń następnie zezwól lub Odmów ruchu do i z delegowanej podsieci Azure NetApp Files. 

## <a name="azure-native-environments"></a>Środowiska natywne platformy Azure

Na poniższym diagramie przedstawiono środowisko platformy Azure w języku macierzystym:

![Środowisko sieci natywnej platformy Azure](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>Lokalna sieć wirtualna

Podstawowym scenariuszem jest utworzenie lub nawiązanie połączenia z woluminem Azure NetApp Files z maszyny wirtualnej (VM) w tej samej sieci wirtualnej. W przypadku sieci wirtualnej 2 na powyższym diagramie wolumin 1 jest tworzony w delegowanej podsieci i można go zainstalować na maszynie wirtualnej 1 w podsieci domyślnej.

### <a name="vnet-peering"></a>Komunikacja równorzędna sieci wirtualnych

Jeśli masz dodatkowe sieci wirtualnych w tym samym regionie, które potrzebują dostępu do zasobów każdego z nich, sieci wirtualnych można połączyć za pomocą [komunikacji równorzędnej sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) , aby zapewnić bezpieczną łączność za pośrednictwem infrastruktury platformy Azure. 

Rozważ użycie sieci VNet 2 i sieci wirtualnej 3 na powyższym diagramie. Jeśli maszyna wirtualna 1 musi nawiązać połączenie z maszyną wirtualną 2 lub 2, lub jeśli maszyna wirtualna 2 musi nawiązać połączenie z maszyną wirtualną 1 lub wolumin 1, należy włączyć komunikację równorzędną sieci wirtualnej między siecią wirtualną 2 i siecią wirtualną 3. 

Ponadto Rozważmy scenariusz, w którym sieć wirtualna 1 jest połączona z siecią VNet 2, a sieć wirtualna 2 jest połączona z siecią VNet 3 w tym samym regionie. Zasoby z sieci wirtualnej 1 mogą łączyć się z zasobami na platformie sieci wirtualnej 2, ale nie mogą łączyć się z zasobami w VNet 3, chyba że sieć wirtualna 1 i Sieć wirtualna 3 są połączone za pomocą komunikacji równorzędnej. 

Na powyższym diagramie, chociaż maszyna wirtualna 3 może połączyć się z woluminem 1, maszyna wirtualna 4 nie może nawiązać połączenia z woluminem 2.  Przyczyną tego jest to, że sieci wirtualnych szprych nie są połączone za pomocą komunikacji równorzędnej, a _Routing tranzytowy nie jest obsługiwany w przypadku komunikacji równorzędnej sieci_wirtualnych.

## <a name="hybrid-environments"></a>Środowiska hybrydowe

Na poniższym diagramie przedstawiono środowisko hybrydowe: 

![Środowisko sieci hybrydowej](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

W scenariuszu hybrydowym aplikacje z lokalnych centrów danych potrzebują dostępu do zasobów na platformie Azure.  W takim przypadku należy rozłożyć centrum danych na platformę Azure lub korzystać z usług natywnych platformy Azure lub odzyskiwania po awarii. Aby uzyskać informacje na temat sposobu łączenia wielu zasobów lokalnych z zasobami na platformie Azure za pośrednictwem sieci VPN typu lokacja-lokacja lub ExpressRoute, zobacz [VPN Gateway temat Opcje planowania](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) .

W hybrydowej topologii gwiazdy koncentratora Sieć wirtualna na platformie Azure działa jako centralny punkt łączności z siecią lokalną. Szprychy są sieci wirtualnych za pomocą komunikacji równorzędnej z centrum i mogą być używane do izolowania obciążeń.

W zależności od konfiguracji można połączyć zasoby lokalne z zasobami w centrum i szprych.

W topologii zilustrowanej powyżej, Sieć lokalna jest połączona z centralną siecią wirtualną na platformie Azure, a w tym samym regionie istnieje 2 szprychy sieci wirtualnych z siecią wirtualną koncentratora.  W tym scenariuszu opcje łączności obsługiwane dla woluminów Azure NetApp Files są następujące:

* Zasoby lokalne VM 1 i VM 2 mogą łączyć się z woluminem 1 w centrum za pośrednictwem sieci VPN lub obwodu ExpressRoute. 
* Lokalne zasoby maszyn wirtualnych 1 i VM 2 mogą łączyć się z woluminem 2 lub 3 za pośrednictwem sieci VPN typu lokacja-lokacja i regionalnej sieci równorzędnej.
* Maszyna wirtualna 3 w koncentratorze sieci wirtualnej może nawiązać połączenie z woluminem 2 w sieci VNET sieci wirtualnej 1 i 3 w szprychy/2.
* W przypadku maszyny wirtualnej 4 z sieci wirtualnej szprych 1 i VM 5 z szprychy sieci wirtualnej 2 można nawiązać połączenie z woluminem 1 w sieci wirtualnej centrum.
* Maszyna wirtualna 4 w sieci wirtualnej szprych 1 nie może nawiązać połączenia z woluminem 3 w sieci wirtualnej szprych 2. Ponadto maszyna wirtualna 5 w VNet2 szprych nie może nawiązać połączenia z woluminem 2 w sieci wirtualnej szprych 1. Dzieje się tak, ponieważ sieci wirtualnych szprych nie są równorzędne i _Routing tranzytowy nie jest obsługiwany za pośrednictwem komunikacji równorzędnej sieci wirtualnej_.
* W powyższej architekturze, jeśli istnieje Brama w sieci wirtualnej szprychy, połączenie z woluminem ANF z Premium łączenia się za pośrednictwem bramy w centrum zostanie utracone. Zgodnie z projektem, preferencja zostanie nadana bramie w sieci wirtualnej szprychy, więc tylko komputery łączące się za pośrednictwem tej bramy mogą łączyć się z woluminem ANF.

## <a name="next-steps"></a>Następne kroki

[Delegowanie podsieci do usługi Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
