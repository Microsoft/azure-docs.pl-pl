---
title: Architektury referencyjne Azure DDoS Protection
description: Poznaj architekturę referencyjną usługi Azure DDoS Protection.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: b74ebf332790fd9a08840c8c76d99e2b014dac43
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103083"
---
# <a name="ddos-protection-reference-architectures"></a>Architektury referencyjne DDoS Protection

Standard DDoS Protection jest przeznaczony [dla usług, które są wdrażane w sieci wirtualnej](../virtual-network/virtual-network-for-azure-services.md). W przypadku innych usług stosowana jest domyślna usługa DDoS Protection podstawowa. Poniższe architektury referencyjne są uporządkowane według scenariuszy, a wzorce architektury pogrupowane razem.

## <a name="virtual-machine-windowslinux-workloads"></a>Obciążenia maszyn wirtualnych (Windows/Linux)

### <a name="application-running-on-load-balanced-vms"></a>Aplikacja działająca na maszynach wirtualnych z równoważeniem obciążenia

Ta architektura referencyjna przedstawia zestaw sprawdzonych rozwiązań dotyczących uruchamiania wielu maszyn wirtualnych z systemem Windows w zestawie skalowania za modułem równoważenia obciążenia, aby zwiększyć dostępność i skalowalność. Ta architektura może być używana w przypadku każdego bezstanowego obciążenia, takiego jak serwer sieci Web.

![Diagram architektury referencyjnej dla aplikacji uruchomionej na maszynach wirtualnych z równoważeniem obciążenia](./media/ddos-best-practices/image-9.png)

W tej architekturze obciążenie jest rozkładane na wiele wystąpień maszyny wirtualnej. Istnieje jeden publiczny adres IP, a ruch internetowy jest dystrybuowany do maszyn wirtualnych za pośrednictwem modułu równoważenia obciążenia. Standard DDoS Protection jest włączony w sieci wirtualnej modułu równoważenia obciążenia platformy Azure (Internet), z którym jest skojarzony publiczny adres IP.

Moduł równoważenia obciążenia dystrybuuje przychodzące żądania internetowe do wystąpień maszyn wirtualnych. Zestawy skalowania maszyn wirtualnych umożliwiają ręczne skalowanie lub wyewidencjonowywanie liczby maszyn wirtualnych lub automatyczne na podstawie wstępnie zdefiniowanych reguł. Jest to ważne, jeśli zasób jest w trakcie ataku DDoS. Aby uzyskać więcej informacji na temat architektury referencyjnej, zobacz [ten artykuł](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm).

### <a name="application-running-on-windows-n-tier"></a>Aplikacja działająca w systemie Windows N-warstwowa

Istnieje wiele sposobów implementowania architektury n-warstwowej. Na poniższym diagramie przedstawiono typową aplikację sieci Web z trzema warstwami. Ta architektura jest oparta na artykule [Uruchamianie maszyn wirtualnych ze zrównoważonym obciążeniem w celu zapewnienia skalowalności i dostępności](/azure/architecture/reference-architectures/virtual-machines-windows/multi-vm). Warstwy internetowa i biznesowa używają maszyn wirtualnych z równoważeniem obciążenia.

![Diagram architektury referencyjnej dla aplikacji działającej w systemie Windows N-warstwowej](./media/ddos-best-practices/image-10.png)

W tej architekturze w sieci wirtualnej jest włączony DDoS Protection Standard. Wszystkie publiczne adresy IP w sieci wirtualnej uzyskają ochronę DDoSą dla warstwy 3 i 4. W przypadku ochrony warstwy 7 Wdróż Application Gateway w jednostce SKU WAF. Aby uzyskać więcej informacji na temat architektury referencyjnej, zobacz [ten artykuł](/azure/architecture/reference-architectures/virtual-machines-windows/n-tier).

> [!NOTE]
> Scenariusze, w których Pojedyncza maszyna wirtualna działa za publicznym adresem IP nie jest obsługiwana.

### <a name="paas-web-application"></a>Aplikacja internetowa PaaS

Ta architektura referencyjna pokazuje, jak działa aplikacja Azure App Service w jednym regionie. Ta architektura pokazuje zestaw sprawdzonych rozwiązań dla aplikacji sieci Web, które używają [Azure App Service](https://azure.microsoft.com/documentation/services/app-service/) i [Azure SQL Database](https://azure.microsoft.com/documentation/services/sql-database/).
W przypadku scenariuszy trybu failover jest ustawiany region w stanie wstrzymania.

![Diagram architektury referencyjnej dla aplikacji sieci Web PaaS](./media/ddos-best-practices/image-11.png)

Usługa Azure Traffic Manager kieruje żądania przychodzące do Application Gateway w jednym z regionów. Podczas normalnych operacji usługa kieruje żądania do Application Gateway w aktywnym regionie. W przypadku niedostępności tego regionu Traffic Manager przełączenia w tryb failover w celu Application Gateway w regionie gotowości.

Cały ruch z Internetu przeznaczony do aplikacji sieci Web jest kierowany do [Application Gateway publicznego adresu IP](../application-gateway/application-gateway-web-app-overview.md) za pośrednictwem Traffic Manager. W tym scenariuszu sama usługa App Service (aplikacja sieci Web) nie jest bezpośrednio na zewnątrz i jest chroniona przez Application Gateway. 

Zalecamy skonfigurowanie Application Gateway jednostki SKU WAF (Zapobiegaj trybowi), aby chronić przed atakami warstwy 7 (HTTP/HTTPS/WebSocket). Ponadto aplikacje sieci Web są skonfigurowane tak, aby [akceptowały tylko ruch z Application Gateway](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/) adres IP.

Aby uzyskać więcej informacji na temat architektury referencyjnej, zobacz [ten artykuł](/azure/architecture/reference-architectures/app-service-web-app/multi-region).

## <a name="protecting-on-premises-resources"></a>Ochrona zasobów lokalnych

Możesz wykorzystać skalę, pojemność i wydajność standardu Azure DDoS Protection, aby chronić zasoby lokalne, udostępniając publiczny adres IP na platformie Azure i przekierowując ruch do źródła zaplecza do środowiska lokalnego.

![Ochrona zasobów Premium](./media/reference-architectures/ddos-on-prem.png)

Jeśli masz aplikację sieci Web, która odbiera ruch z Internetu, możesz hostować aplikację sieci Web za Application Gateway, a następnie chronić ją za pomocą WAF przed atakami sieci Web w warstwie 7, takimi jak iniekcja SQL i Slowloris. Źródła zaplecza aplikacji będą znajdować się w środowisku lokalnym, które są połączone za pośrednictwem sieci VPN. 

Zasoby zaplecza w środowisku lokalnym nie będą ujawnione w publicznej sieci Internet. Tylko publiczny adres IP AppGW/WAF jest narażony na połączenie internetowe, a nazwa DNS aplikacji jest mapowana na ten publiczny adres IP. 

Gdy usługa DDoS Protection Standard jest włączona w sieci wirtualnej, która zawiera AppGW/WAF, DDoS Protection Standard będzie chronić aplikację przez rozwiązanie problemu dotyczącego nieprawidłowego ruchu i kierowanie przypuszczalnie czystego ruchu do aplikacji. 

W tym [artykule](https://docs.microsoft.com/azure/azure-vmware/protect-azure-vmware-solution-with-application-gateway) pokazano, jak można używać standardu DDoS Protection w połączeniu z Application Gateway, aby chronić aplikację sieci Web działającą w rozwiązaniu VMware platformy Azure.

## <a name="mitigation-for-non-web-paas-services"></a>Środki zaradcze dla usług PaaS innych niż sieci Web

### <a name="hdinsight-on-azure"></a>HDInsight na platformie Azure

W tej architekturze referencyjnej przedstawiono Konfigurowanie DDoS Protection Standard dla [klastra usługi Azure HDInsight](../hdinsight/index.yml). Upewnij się, że klaster usługi HDInsight jest połączony z siecią wirtualną i że DDoS Protection jest włączona w sieci wirtualnej.

![Okienka "HDInsight" i "Ustawienia zaawansowane" z ustawieniami sieci wirtualnej](./media/ddos-best-practices/image-12.png)

![Wybór na potrzeby włączania DDoS Protection](./media/ddos-best-practices/image-13.png)

W tej architekturze ruch kierowany do klastra usługi HDInsight z Internetu jest kierowany do publicznego adresu IP skojarzonego z usługą równoważenia obciążenia bramy usługi HDInsight. Moduł równoważenia obciążenia bramy wysyła następnie bezpośrednio ruch do węzłów głównych lub węzłów procesu roboczego. Ponieważ w sieci wirtualnej usługi HDInsight włączono Standard DDoS Protection, wszystkie publiczne adresy IP w sieci wirtualnej uzyskają ochronę DDoSą dla warstwy 3 i 4. Ta architektura referencyjna może być łączona z architekturą referencyjną N-warstwową i wieloregionową.

Więcej informacji na temat tej architektury referencyjnej można znaleźć w dokumentacji usługi [Azure HDInsight przy użyciu usługi azure Virtual Network](../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .


> [!NOTE]
> Azure App Service Environment usługi PowerApps lub API Management w sieci wirtualnej z publicznym adresem IP nie są obsługiwane natywnie.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć plan ochrony DDoS](manage-ddos-protection.md).