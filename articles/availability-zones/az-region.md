---
title: Usługi platformy Azure obsługujące strefy dostępności
description: Aby tworzyć odporne aplikacje o wysokiej Strefy dostępności na platformie Azure, Strefy dostępności fizycznie oddzielone lokalizacje, których można użyć do uruchamiania zasobów.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: fd4443e4ef6bcecacc03ce190bbf438288c9ec60
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364060"
---
# <a name="azure-services-that-support-availability-zones"></a>Usługi platformy Azure obsługujące strefy dostępności

Microsoft Azure globalna jest projektowana i konstruowana w każdej warstwie w celu zapewnienia klientom najwyższego poziomu nadmiarowości i odporności. Infrastruktura platformy Azure składa się z obszarów geograficznych, regionów i Strefy dostępności, które ograniczają promień awarii i w związku z tym ograniczają potencjalny wpływ na aplikacje i dane klientów. Konstrukcja Strefy dostępności platformy Azure została opracowana w celu zapewnienia oprogramowania i rozwiązania sieciowego w celu ochrony przed awariami centrum danych i zapewnienia klientom zwiększonej wysokiej dostępności.

Strefy dostępności to unikatowe fizyczne lokalizacje w regionie świadczenia usługi Azure. Każda strefa składa się z co najmniej jednego centrum danych z niezależnym zasilaniem, chłodzeniem i siecią. Fizyczne rozdzielenie sieci Strefy dostępności w obrębie regionu ogranicza wpływ na aplikacje i dane z awarii stref, takich jak powódź na dużą skalę, duże burze i burze oraz inne zdarzenia, które mogą zakłócać dostęp do lokacji, bezpieczny fragment, dłuższy czas pracy narzędzi i dostępność zasobów. Strefy dostępności i skojarzone z nimi centra danych są zaprojektowane w taki sposób, że w przypadku naruszenia zabezpieczeń jednej strefy usługi, pojemność i dostępność są obsługiwane przez inne Strefy dostępności w regionie.

Wszystkie usługi zarządzania platformą Azure zostały tak zaprojektowane, aby były odporne na awarie na poziomie regionu. W spektrum awarii co najmniej jedna awaria strefy dostępności w regionie ma mniejszy promień awarii w porównaniu z awarią całego regionu. Platforma Azure może odzyskać dane po awarii usług zarządzania na poziomie strefy w regionie. Platforma Azure przeprowadza krytyczną konserwację jednej strefy na raz w regionie, aby zapobiec awariom wpływających na zasoby klientów Strefy dostępności w obrębie regionu.


![koncepcyjny widok regionu świadczenia usługi Azure z 3 strefami](./media/az-region/azure-region-availability-zones.png)


Usługi platformy Azure Strefy dostępności się na trzy **kategorie:** strefowe, **strefowo nadmiarowe** i **poza regionalną.** Obciążenia klientów można podzielić na kategorie, aby wykorzystać dowolny z tych scenariuszy architektury w celu zapewnienia wydajności i trwałości aplikacji.

- **Usługi strefowe** — zasób można wdrożyć w określonej, samodzielnie wybranej strefie dostępności, aby osiągnąć bardziej rygorystyczne wymagania dotyczące opóźnień lub wydajności.  Odporność jest samodzielnie projektowana przez replikowanie aplikacji i danych do co najmniej jednej strefy w regionie.  Zasoby można przypiąć do określonej strefy. Na przykład maszyny wirtualne, dyski zarządzane lub standardowe adresy IP można przypiąć do określonej strefy, co pozwala zwiększyć odporność dzięki rozłożeniu co najmniej jednego wystąpienia zasobów w różnych strefach.

- **Usługi strefowo nadmiarowe** — platforma Azure replikuje zasoby i dane między strefami.  Firma Microsoft zarządza dostarczaniem wysokiej dostępności, ponieważ platforma Azure automatycznie replikuje i dystrybuuje wystąpienia w regionie.  Na przykład ZRS replikuje dane w trzech strefach, aby awaria strefy nie wpływała na ha ha danych. 

- **Usługi inne niż regionalne** — usługi są zawsze dostępne w lokalizacjach geograficznych platformy Azure i są odporne na błędy w całej strefie, a także na błędy w całym regionie. 


Aby zapewnić kompleksową ciągłość działalności biznesowej na platformie Azure, skompilować architekturę aplikacji przy użyciu kombinacji Strefy dostępności z parami regionów platformy Azure. Aplikacje i dane można replikować synchronicznie przy użyciu usługi Strefy dostępności w regionie świadczenia usługi Azure w celu wysokiej dostępności i asynchronicznie replikować w regionach platformy Azure w celu ochrony odzyskiwania po awarii. Aby dowiedzieć się więcej, przeczytaj [tworzenie rozwiązań wysokiej dostępności przy użyciu Strefy dostępności](/azure/architecture/high-availability/building-solutions-for-high-availability). 

## <a name="azure-services-supporting-availability-zones"></a>Usługi platformy Azure Strefy dostępności

 - Maszyny wirtualne starszej generacji nie są wyświetlane na liście. Aby uzyskać więcej informacji, zobacz [Poprzednie generacje rozmiarów maszyn wirtualnych.](../virtual-machines/sizes-previous-gen.md)
 - Jak wspomniano na stronie [Regiony i Strefy dostępności platformie Azure,](az-overview.md)niektóre usługi nie są regionalne. Te usługi nie są zależne od konkretnego regionu świadczenia usługi Azure, ponieważ są odporne na błędy w całej strefie, a także na błędy w całym regionie.  Listę usług innych niż regionalne można znaleźć na stronie [Dostępne produkty według regionów.](https://azure.microsoft.com/global-infrastructure/services/)


## <a name="azure-regions-with-availability-zones"></a>Regiony platformy Azure z Strefy dostępności
 

| Ameryki           | Europa               | Afryka              | Azja i Pacyfik   |
|--------------------|----------------------|---------------------|----------------|
|                    |                      |                     |                |
| Brazylia Południowa       | Francja Środkowa       | Północna Republika Południowej Afryki* | Australia Wschodnia |
| Kanada Środkowa     | Niemcy Zachodnio-środkowe |                     | Indie Środkowe* |
| Central US         | Europa Północna         |                     | Japan East     |
| East US            | Południowe Zjednoczone Królestwo             |                     | Korea Środkowa* |
| Wschodnie stany USA 2          | West Europe          |                     | Southeast Asia |
| Południowo-środkowe usa |                      |                     |                |
| US Gov Wirginia    |                      |                     |                |
| Zachodnie stany USA 2        |                      |                     |                |
| Zachodnie stany USA 3*       |                      |                     |                |

\* Aby dowiedzieć się więcej o Strefy dostępności i pomocy technicznej dotyczącej dostępnych usług w tych regionach, skontaktuj się z przedstawicielem ds. sprzedaży lub klienta firmy Microsoft. Aby uzyskać informacje o nadchodzących regionach, które będą Strefy dostępności, zobacz [Obszary geograficzne platformy Azure.](https://azure.microsoft.com/en-us/global-infrastructure/geographies/)


## <a name="azure-services-supporting-availability-zones"></a>Usługi platformy Azure Strefy dostępności

- Maszyny wirtualne starszej generacji nie są wymienione poniżej. Aby uzyskać więcej informacji, zobacz [poprzednie generacje rozmiarów maszyn wirtualnych.](../virtual-machines/sizes-previous-gen.md)

- Niektóre usługi nie są regionalne. Aby uzyskać więcej informacji, zobacz Regiony i Strefy dostępności na platformie [Azure.](az-overview.md) Te usługi nie są zależne od konkretnego regionu świadczenia usługi Azure, dzięki czemu są odporne na błędy w całej strefie i w całym regionie.  Listę usług innych niż regionalne można znaleźć na stronie [Dostępne produkty według regionów.](https://azure.microsoft.com/global-infrastructure/services/)


### <a name="zone-resilient-services"></a>Usługi odporne na strefy 

:globe_with_meridians: Usługi inne niż regionalne — usługi są zawsze dostępne w lokalizacjach geograficznych platformy Azure i są odporne na błędy w całej strefie, a także na błędy całego regionu.

:large_blue_diamond: Odporność na błędy w całej strefie 

**Foundational Services**

|     Produkty                                                    | Odporność             |
|-----------------------------------------------------------------|:----------------------------:|
|     [Application Gateway (wersja 2)](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant)                                  | :large_blue_diamond:  |
|     [Azure Backup](https://docs.microsoft.com/azure/backup/backup-create-rs-vault#set-storage-redundancy)                                                | :large_blue_diamond:  |
|     [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/high-availability#availability-zone-support)                                           | :large_blue_diamond:  |
|     [Azure Data Lake Storage Gen 2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction)                             | :large_blue_diamond:  |
|     [Azure Express Route](https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute)                                       | :large_blue_diamond:  |
|     [Publiczny adres IP platformy Azure](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses)                                           | :large_blue_diamond:  |
|     Azure SQL Database[(warstwa Ogólnego przeznaczenia)](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla)                 | :large_blue_diamond:  |
|     Azure SQL Database([Warstwa & Krytyczne dla działania firmy Premium](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla))     | :large_blue_diamond:  |
|     [Disk Storage](https://docs.microsoft.com/azure/storage/common/storage-redundancy)                                                | :large_blue_diamond:  |
|     [Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-geo-dr#availability-zones)                                                  | :large_blue_diamond:  |
|     [Usługa Key Vault](https://docs.microsoft.com/azure/key-vault/general/disaster-recovery-guidance)                                                   | :large_blue_diamond:  |
|     [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)                                               | :large_blue_diamond:  |
|     [Service Bus](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-geo-dr#availability-zones)                                                 | :large_blue_diamond:  |
|     [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cross-availability-zones#:~:text=An%20Availability%20Zone%20is%20a%20unique%20physical%20location,zones.%20This%20will%20ensure%20high-availability%20of%20your%20applications)                                            | :large_blue_diamond:  |
|     [Konto magazynu](https://docs.microsoft.com/azure/storage/common/storage-redundancy)                                           | :large_blue_diamond:  |
|     Magazyn:   [warstwy magazynowania Gorąca Blob Storage Chłodna](https://docs.microsoft.com/azure/storage/common/storage-redundancy)                      | :large_blue_diamond:  |
|     Magazyn:   [Dyski zarządzane](https://docs.microsoft.com/azure/virtual-machines/managed-disks-overview)                                    | :large_blue_diamond:  |
|     [Virtual Machines zestawów skalowania](https://docs.microsoft.com/azure/virtual-machine-scale-sets/scripts/cli-sample-zone-redundant-scale-set)                               | :large_blue_diamond:  |
|     [Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                          | :large_blue_diamond:  |
|     Virtual Machines: Seria [Av2](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Virtual Machines: Seria [Bs](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: Seria [DSv2](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                             | :large_blue_diamond:  |
|     Virtual Machines: [seria DSv3](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                            | :large_blue_diamond:  |
|     Virtual Machines: seria [Dv2](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                             | :large_blue_diamond:  |
|     Virtual Machines: seria [Dv3](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Virtual Machines: [Seria ESv3](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                             | :large_blue_diamond:  |
|     Virtual Machines: Seria [Ev3](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Virtual Machines: Seria [F](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                | :large_blue_diamond:  |
|     Virtual Machines: [Seria FS](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [Shared Image Gallery](https://docs.microsoft.com/azure/virtual-machines/shared-image-galleries#make-your-images-highly-available) | :large_blue_diamond:  |
|     [Virtual Network](https://docs.microsoft.com/azure/vpn-gateway/create-zone-redundant-vnet-gateway)                                         | :large_blue_diamond:  |
|     [VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways)                                             | :large_blue_diamond:  |


**Usługi główne**


|     Produkty                                                    | Odporność             |
|-----------------------------------------------------------------|:----------------------------:|
|     [Środowiska usługi App Service](https://docs.microsoft.com/azure/app-service/environment/zone-redundancy)                                    | :large_blue_diamond:  |
|     [Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/overview)                      | :large_blue_diamond:  |
|     [Azure Bastion](https://docs.microsoft.com/azure/bastion/bastion-overview)                                               | :large_blue_diamond:  |
|     [Azure Cache for Redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-high-availability)                              | :large_blue_diamond:  |
|     [Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-performance-optimization#availability-zones)               | :large_blue_diamond:  |
|     Azure Cognitive Services: [analiza tekstu](https://docs.microsoft.com/azure/cognitive-services/text-analytics/)                    | :large_blue_diamond:  |
|     [Azure Data Explorer](https://docs.microsoft.com/azure/data-explorer/create-cluster-database-portal)                               | :large_blue_diamond:  |
|     Azure Database for MySQL — [serwer elastyczny](https://docs.microsoft.com/azure/mysql/flexible-server/concepts-high-availability)                  | :large_blue_diamond:  |
|     Azure Database for PostgreSQL — [serwer elastyczny](https://docs.microsoft.com/azure/postgresql/flexible-server/overview)             | :large_blue_diamond:  |
|     [Azure DDoS Protection](https://docs.microsoft.com/azure/ddos-protection/ddos-faq)                                       | :large_blue_diamond:  |
|     [Usługa Azure Disk Encryption](https://docs.microsoft.com/azure/virtual-machines/disks-redundancy)                                       | :large_blue_diamond:  |
|     [Azure Firewall](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default)                                              | :large_blue_diamond:  |
|     [Azure Firewall Manager](https://docs.microsoft.com/azure/firewall-manager/quick-firewall-policy)                                      | :large_blue_diamond:  |
|     [Azure Kubernetes Service (AKS)](https://docs.microsoft.com/azure/aks/availability-zones)                              | :large_blue_diamond:  |
|     [Link prywatny platformy Azure](https://docs.microsoft.com/azure/private-link/private-link-overview)                                          | :large_blue_diamond:  |
|     [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery)                                         | :large_blue_diamond:  |
|     Azure SQL: [Maszyna wirtualna](https://docs.microsoft.com/azure/azure-sql/database/high-availability-sla)                                  | :large_blue_diamond:  |
|     [Azure Web Application Firewall](https://docs.microsoft.com/azure/firewall/deploy-availability-zone-powershell#:~:text=For%20more%20information%20about%20Azure%20Firewall%20Availability%20Zones%2C,This%20creates%20a%20zone-redundant%20IP%20address%20by%20default)                              | :large_blue_diamond:  |
|     [Container Registry](https://docs.microsoft.com/azure/container-registry/zone-redundancy)                                          | :large_blue_diamond:  |
|     [Event Grid](https://docs.microsoft.com/azure/event-grid/overview)                                                  | :large_blue_diamond:  |
|     [Network Watcher](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions#service-availability-and-redundancy)                                             | :large_blue_diamond:  |
|     Network Watcher: [Analiza ruchu](https://docs.microsoft.com/azure/network-watcher/frequently-asked-questions#service-availability-and-redundancy)                          | :large_blue_diamond:  |
|     [Power BI Embedded](https://docs.microsoft.com/power-bi/admin/service-admin-failover#what-does-high-availability)                                           | :large_blue_diamond:  |
|     [Premium Blob Storage](https://docs.microsoft.com/azure/storage/blobs/storage-blob-performance-tiers#:~:text=Table%201%20%20%20%20Area%20%20,%20%20Currently%20supports%20only%20locally-redundan%20...%20)                                        | :large_blue_diamond:  |
|     Magazyn: [Azure Premium Files](https://docs.microsoft.com/azure/storage/files/storage-files-planning)                                | :large_blue_diamond:  |
|     Virtual Machines: [Azure Dedicated Host](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                     | :large_blue_diamond:  |
|     Virtual Machines: Seria [Ddsv4](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Virtual Machines: [seria Ddv4](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: Seria [Dsv4](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: seria [Dv4](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                | :large_blue_diamond:  |
|     Virtual Machines: seria [Edsv4](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                              | :large_blue_diamond:  |
|     Virtual Machines: [seria Edv4](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: Seria [Esv4](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: [Seria Ev4](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                | :large_blue_diamond:  |
|     Virtual Machines: Seria [Fsv2](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                               | :large_blue_diamond:  |
|     Virtual Machines: seria [M](https://docs.microsoft.com/azure/virtual-machines/windows/create-powershell-availability-zone)                                  | :large_blue_diamond:  |
|     [Wirtualna sieć WAN](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)                                                 | :large_blue_diamond:  |
|     Virtual WAN: [ExpressRoute](https://docs.microsoft.com/azure/virtual-wan/virtual-wan-about#how-are-availability-zones-and-resiliency-handled-in-virtual-wan)                                   | :large_blue_diamond:  |
|     Virtual WAN: [Punkt-lokacja VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways)                      | :large_blue_diamond:  |
|     Virtual WAN: [lokacja-lokacja — VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/about-zone-redundant-vnet-gateways)                       | :large_blue_diamond:  |


**Wyspecjalizowane usługi**

|     Produkty                                                    | Odporność             |
|-----------------------------------------------------------------|:----------------------------:|
|     Azure Red Hat OpenShift                                     | :large_blue_diamond:  |
|     Cognitive Services: Narzędzie do wykrywania anomalii                        | :large_blue_diamond:  |
|     Cognitive Services: Rozpoznawanie formularzy                         | :large_blue_diamond:  |
|     Magazyn: Ultra Disk                                         | :large_blue_diamond:  |


**Inne niż regionalne**

|     Produkty                                                    | Odporność             |
|-----------------------------------------------------------------|:----------------------------:|
|     Azure DNS                                                   | :globe_with_meridians: |
|     Azure Active Directory                                    | :globe_with_meridians: |
|     Azure Advanced Threat Protection                            | :globe_with_meridians: |
|     Azure Advisor                                               | :globe_with_meridians: |
|     Azure Blueprints                                            | :globe_with_meridians: |
|     Azure Bot Services                                          | :globe_with_meridians: |
|     Azure Front Door                                            | :globe_with_meridians: |
|     Azure Defender dla IoT                                    | :globe_with_meridians: |
|     Azure Front Door                                            | :globe_with_meridians: |
|     Azure Information Protection                              | :globe_with_meridians: |
|     Azure Lighthouse                                          | :globe_with_meridians: |
|     Azure Managed Applications                                | :globe_with_meridians: |
|     Azure Maps                                                  | :globe_with_meridians: |
|     Azure Performance Diagnostics                               | :globe_with_meridians: |
|     Azure Policy                                                | :globe_with_meridians: |
|     Azure Resource Graph                                      | :globe_with_meridians: |
|     Usługa Azure Sentinel                                              | :globe_with_meridians: |
|     Azure Stack                                                 | :globe_with_meridians: |
|     Azure Stack Edge                                          | :globe_with_meridians: |
|     Cloud Shell                                                 | :globe_with_meridians: |
|     Content Delivery Network                                    | :globe_with_meridians: |
|     Cost Management                                             | :globe_with_meridians: |
|     Skrytka klienta for Microsoft Azure                      | :globe_with_meridians: |
|     Intune                                                      | :globe_with_meridians: |
|     Microsoft Azure Peering Service                           | :globe_with_meridians: |
|     Microsoft Azure Portal                                    | :globe_with_meridians: |
|     Microsoft Cloud App Security                                | :globe_with_meridians: |
|     Microsoft Graph                                             | :globe_with_meridians: |
|     Security Center                                           | :globe_with_meridians: |
|     Traffic Manager                                           | :globe_with_meridians: |


## <a name="pricing-for-vms-in-availability-zones"></a>Cennik maszyn wirtualnych w Strefy dostępności

Strefy dostępności platformy Azure są dostępne w ramach subskrypcji platformy Azure. Dowiedz się więcej tutaj — [cennik przepustowości.](https://azure.microsoft.com/pricing/details/bandwidth/)


## <a name="get-started-with-availability-zones"></a>Wprowadzenie do Strefy dostępności

- [Tworzenie maszyny wirtualnej](../virtual-machines/windows/create-portal-availability-zone.md)
- [Dodawanie dysku zarządzanego przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Tworzenie strefowo nadmiarowego zestawu skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Równoważenie obciążenia maszyn wirtualnych w różnych strefach przy użyciu usługa Load Balancer w warstwie Standardowa frontonie strefowo nadmiarowego](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Równoważenie obciążenia maszyn wirtualnych w strefie przy użyciu usługa Load Balancer w warstwie Standardowa z frontonem strefowym](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Magazyn strefowo nadmiarowy](../storage/common/storage-redundancy.md)
- [SQL Database ogólnego przeznaczenia](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Geograficzne odzyskiwanie po awarii w usłudze Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Geograficzne odzyskiwanie po awarii w usłudze Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Tworzenie strefowo nadmiarowej bramy sieci wirtualnej](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Dodawanie regionu strefowo nadmiarowego dla Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Wprowadzenie Azure Cache for Redis Strefy dostępności](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Utwórz wystąpienie usługi Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md)
- [Tworzenie klastra Azure Kubernetes Service (AKS), który używa Strefy dostępności](../aks/availability-zones.md)


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Regiony i strefy dostępności na platformie Azure](az-overview.md)
