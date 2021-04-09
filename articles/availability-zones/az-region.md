---
title: Usługi platformy Azure obsługujące strefy dostępności
description: Aby utworzyć aplikacje o wysokiej dostępności i odporności na platformie Azure, Strefy dostępności zapewnić fizycznie oddzielne lokalizacje, za pomocą których można uruchamiać zasoby.
author: prsandhu
ms.service: azure
ms.topic: conceptual
ms.date: 03/30/2021
ms.author: prsandhu
ms.reviewer: cynthn
ms.custom: fasttrack-edit, mvc, references_regions
ms.openlocfilehash: a81f3a3c51ffd0e1e0937c077a07fcbde16f0513
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961306"
---
# <a name="azure-services-that-support-availability-zones"></a>Usługi platformy Azure obsługujące strefy dostępności

Infrastruktura globalna Microsoft Azure jest zaprojektowana i zbudowana w każdej warstwie, aby zapewnić klientom najwyższy poziom nadmiarowości i odporność. Infrastruktura platformy Azure składa się z lokalizacje geograficzne, regionów i Strefy dostępności, które ograniczają promień awarii i w związku z tym ograniczają potencjalny wpływ na aplikacje i dane klientów. Konstrukcja Strefy dostępności platformy Azure została opracowana w celu udostępnienia oprogramowania i sieci w celu ochrony przed awariami centrów danych oraz zapewnienia zwiększonej wysokiej dostępności dla naszych klientów.

Strefy dostępności to unikatowe fizyczne lokalizacje w regionie świadczenia usługi Azure. Każda strefa składa się z jednego lub kilku centrów danych z niezależną mocą, chłodzeniem i siecią. Fizyczne rozdzielenie Strefy dostępności w regionie ogranicza wpływ na aplikacje i dane z awarii strefy, takie jak zalewanie na dużą skalę, główne burze i przedziały, a także inne zdarzenia, które mogą zakłócać dostęp do lokacji, bezpieczny pasaż, wydłużyć czas pracy narzędzi i dostępność zasobów. Strefy dostępności i powiązane z nimi centra danych zostały zaprojektowane w taki sposób, że w przypadku naruszenia zabezpieczeń jednej strefy usługi, wydajność i dostępność są obsługiwane przez inne Strefy dostępności w regionie.

Wszystkie usługi zarządzania systemu Azure są zaprojektowane tak, aby były odporne na awarie na poziomie regionu. W spektrum błędów co najmniej jedna awaria strefy dostępności w obrębie regionu ma mniejszą wartość promienia błędu w porównaniu z awarią całego regionu. Platforma Azure może wykonać odzyskiwanie z niepowodzeń usług zarządzania w obrębie strefy. Platforma Azure wykonuje krytyczną konserwację jedną strefę w danym momencie w regionie, aby zapobiec wszelkim awariom wpływającym na zasoby klienta wdrożone w ramach Strefy dostępności w regionie.


![Widok koncepcyjny regionu platformy Azure z 3 strefami](./media/az-region/azure-region-availability-zones.png)


Usługi platformy Azure wspierające Strefy dostępności można podzielić na trzy **Kategorie: usługi** **strefowo nadmiarowe** i **nieregionalne** . Obciążenia klientów mogą być kategoryzowane w celu wykorzystania dowolnego z tych scenariuszy architektury w celu spełnienia wymagań dotyczących wydajności i trwałości aplikacji.

- **Usługi strefowe** — zasób można wdrożyć w określonej, samodzielnej strefie dostępności, aby osiągnąć bardziej rygorystyczne wymagania dotyczące opóźnień lub wydajności.  Odporność jest samodzielna przez replikowanie aplikacji i danych do co najmniej jednej strefy w regionie.  Zasoby mogą być przypięte do określonej strefy. Na przykład maszyny wirtualne, dyski zarządzane lub standardowe adresy IP mogą być przypięte do określonej strefy, co umożliwia zwiększenie odporności przez posiadanie jednego lub większej liczby wystąpień zasobów między strefami.

- **Usługi strefowo nadmiarowe** — platforma Azure replikuje zasób i dane między strefami.  Firma Microsoft zarządza dostarczaniem wysokiej dostępności, ponieważ platforma Azure automatycznie replikuje i dystrybuuje wystąpienia w regionie.  ZRS, na przykład replikuje dane w trzech strefach, aby awaria strefy nie miała wpływu na HA danych. 

- **Usługi inne niż regionalne** — usługi są zawsze dostępne w usłudze Azure lokalizacje geograficzne i są odporne na awarie na poziomie strefy, a także awarie całego regionu. 


Aby zapewnić kompleksową ciągłość biznesową na platformie Azure, skompiluj architekturę aplikacji przy użyciu kombinacji Strefy dostępności z parami regionów platformy Azure. Można synchronicznie replikować aplikacje i dane za pomocą Strefy dostępności w regionie świadczenia usługi Azure, aby zapewnić wysoką dostępność i asynchroniczną replikację w regionach platformy Azure w celu ochrony przed odzyskiwaniem po awarii. Aby dowiedzieć się więcej, zobacz [Kompilowanie rozwiązań o wysokiej dostępności przy użyciu strefy dostępności](/azure/architecture/high-availability/building-solutions-for-high-availability). 

## <a name="azure-services-supporting-availability-zones"></a>Usługi platformy Azure obsługujące Strefy dostępności

 - Nie ma na liście starszej generacji maszyn wirtualnych. Aby uzyskać więcej informacji, zobacz [poprzednie generacja rozmiarów maszyn wirtualnych](../virtual-machines/sizes-previous-gen.md).
 - Jak wspomniano w [regionach i strefy dostępności na platformie Azure](az-overview.md), niektóre usługi nie są regionalne. Te usługi nie są zależne od określonego regionu świadczenia usługi Azure, ponieważ są one odporne na przerwy w działaniu strefy, a także przerwy w działaniu całego regionu.  Listę usług innych niż regionalne można znaleźć w [obszarze produkty dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/).


## <a name="azure-regions-with-availability-zones"></a>Regiony platformy Azure z Strefy dostępności


| Ameryki           | Europa               | Afryka              | Azja i Pacyfik   |
|--------------------|----------------------|---------------------|----------------|
|                    |                      |                     |                |
| Brazylia Południowa       | Francja Środkowa       | Północna Republika Południowej Afryki * | Australia Wschodnia |
| Kanada Środkowa     | Niemcy Środkowo-Zachodnie |                     | Japonia Wschodnia     |
| Central US         | Europa Północna         |                     | Korea środkowa * |
| East US            | Południowe Zjednoczone Królestwo             |                     | Southeast Asia |
| Wschodnie stany USA 2          | West Europe          |                     |                |
| Południowo-środkowe stany USA |                      |                     |                |
| US Gov Wirginia    |                      |                     |                |
| Zachodnie stany USA 2        |                      |                     |                |
| Zachodnie stany USA 3 *       |                      |                     |                |

\* Aby dowiedzieć się więcej na temat pomocy technicznej dotyczącej usług Strefy dostępności i dostępnych w tych regionach, skontaktuj się z przedstawicielem firmy Microsoft ds. sprzedaży lub klienta. W przypadku nadchodzących regionów, które będą obsługiwać Strefy dostępności, zobacz [Azure lokalizacje geograficzne](https://azure.microsoft.com/en-us/global-infrastructure/geographies/).


## <a name="azure-services-supporting-availability-zones"></a>Usługi platformy Azure obsługujące Strefy dostępności

- Starsze maszyny wirtualne generacji nie są wymienione poniżej. Aby uzyskać więcej informacji, zobacz [poprzednie generacja rozmiarów maszyn wirtualnych](../virtual-machines/sizes-previous-gen.md).

- Niektóre usługi nie są regionalne. Aby uzyskać więcej informacji, zobacz [regiony i strefy dostępności na platformie Azure](az-overview.md) . Te usługi nie są zależne od określonego regionu świadczenia usługi Azure, dzięki czemu mogą odporne na przerwy w działaniu strefy i w całym regionie.  Listę usług innych niż regionalne można znaleźć w [obszarze produkty dostępne według regionów](https://azure.microsoft.com/global-infrastructure/services/).


### <a name="zone-resilient-services"></a>Usługi odporne na strefy 

: globe_with_meridians: usługi inne niż regionalne — usługi są zawsze dostępne z usługi Azure lokalizacje geograficzne i są odporne na awarie na poziomie strefy, a także awarie całego regionu.

: large_blue_diamond: odporne na awarie w całej strefie 

**Usługi podstawowe**

|     Produkty                                                    | Odporność             |
|-----------------------------------------------------------------|:----------------------------:|
|     Konto magazynu                                           | : large_blue_diamond:  |
|     Application Gateway (wersja 2)                                  | : large_blue_diamond:  |
|     Azure Backup                                                | : large_blue_diamond:  |
|     Azure Cosmos DB                                           | : large_blue_diamond:  |
|     Azure Data Lake Storage Gen 2                             | : large_blue_diamond:  |
|     Trasa Azure Express                                       | : large_blue_diamond:  |
|     Publiczny adres IP platformy Azure                                           | : large_blue_diamond:  |
|     Azure SQL Database (warstwa Ogólnego przeznaczenia)                 | : large_blue_diamond:  |
|     Azure SQL Database (warstwa warstwy & Premium Krytyczne dla działania firmy)     | : large_blue_diamond:  |
|     Disk Storage                                                | : large_blue_diamond:  |
|     Event Hubs                                                  | : large_blue_diamond:  |
|     Key Vault                                                   | : large_blue_diamond:  |
|     Load Balancer                                               | : large_blue_diamond:  |
|     Service Bus                                                 | : large_blue_diamond:  |
|     Service Fabric                                            | : large_blue_diamond:  |
|     Magazyn: gorąca/chłodna Blob Storage warstw                      | : large_blue_diamond:  |
|     Magazyn: Managed Disks                                    | : large_blue_diamond:  |
|     Virtual Machines zestawy skalowania                               | : large_blue_diamond:  |
|     Virtual Machines                                          | : large_blue_diamond:  |
|     Virtual Machines: Av2-Series                              | : large_blue_diamond:  |
|     Virtual Machines: Bs-Series                               | : large_blue_diamond:  |
|     Virtual Machines: DSv2-Series                             | : large_blue_diamond:  |
|     Virtual Machines: DSv3-Series                             | : large_blue_diamond:  |
|     Virtual Machines: Dv2-Series                              | : large_blue_diamond:  |
|     Virtual Machines: Dv3-Series                              | : large_blue_diamond:  |
|     Virtual Machines: ESv3-Series                             | : large_blue_diamond:  |
|     Virtual Machines: Ev3-Series                              | : large_blue_diamond:  |
|     Virtual Machines: Seria F                                | : large_blue_diamond:  |
|     Virtual Machines: FS-Series                               | : large_blue_diamond:  |
|     Virtual Network                                           | : large_blue_diamond:  |
|     VPN Gateway                                                 | : large_blue_diamond:  |


**Podstawowe usługi**

| Produkty                                        | Odporność |
|-------------------------------------------------|:------------:|
| Środowiska usługi App Service                        |      : large_blue_diamond:  |
| Azure Active Directory Domain Services          |      : large_blue_diamond:  |
| Azure Bastion                                   |      : large_blue_diamond:  |
| Azure Cache for Redis                           |      : large_blue_diamond:  |
| Azure Cognitive Services: analiza tekstu        |      : large_blue_diamond:  |
| Azure Data Explorer                             |      : large_blue_diamond:  |
| Azure Database for MySQL — elastyczny serwer      |      : large_blue_diamond:  |
| Azure Database for PostgreSQL — elastyczny serwer |      : large_blue_diamond:  |
| Azure DDoS Protection                           |      : large_blue_diamond:  |
| Usługa Azure Disk Encryption                           |      : large_blue_diamond:  |
| Azure Firewall                                  |      : large_blue_diamond:  |
| Azure Firewall Manager                          |      : large_blue_diamond:  |
| Azure Kubernetes Service (AKS)                  |      : large_blue_diamond:  |
| Link prywatny platformy Azure                              |      : large_blue_diamond:  |
| Azure Red Hat OpenShift                         |      : large_blue_diamond:  |
| Azure Site Recovery                             |      : large_blue_diamond:  |
| Azure SQL: maszyna wirtualna                      |      : large_blue_diamond:  |
| Azure Search                                    |      : large_blue_diamond:  |
| Azure Web Application Firewall                  |      : large_blue_diamond:  |
| Container Registry                              |      : large_blue_diamond:  |
| Event Grid                                      |      : large_blue_diamond:  |
| Network Watcher                                 |      : large_blue_diamond:  |
| Network Watcher: Analiza ruchu              |      : large_blue_diamond:  |
| Power BI Embedded                               |      : large_blue_diamond:  |
| Blob Storage Premium                            |      : large_blue_diamond:  |
| Magazyn: pliki Azure Premium                    |      : large_blue_diamond:  |
| Virtual Machines: dedykowany Host platformy Azure          |      : large_blue_diamond:  |
| Virtual Machines: Ddsv4-Series                  |      : large_blue_diamond:  |
| Virtual Machines: Ddv4-Series                   |      : large_blue_diamond:  |
| Virtual Machines: Dsv4-Series                   |      : large_blue_diamond:  |
| Virtual Machines: Dv4-Series                    |      : large_blue_diamond:  |
| Virtual Machines: Edsv4-Series                  |      : large_blue_diamond:  |
| Virtual Machines: Edv4-Series                   |      : large_blue_diamond:  |
| Virtual Machines: Esv4-Series                   |      : large_blue_diamond:  |
| Virtual Machines: Ev4-Series                    |      : large_blue_diamond:  |
| Virtual Machines: Fsv2-Series                   |      : large_blue_diamond:  |
| Virtual Machines: Seria M                      |      : large_blue_diamond:  |
| Wirtualna sieć WAN                                     |      : large_blue_diamond:  |
| Wirtualna sieć WAN: ExpressRoute                       |      : large_blue_diamond:  |
| Wirtualna sieć WAN: VPN Gateway punkt-lokacja          |      : large_blue_diamond:  |
| Wirtualna sieć WAN: VPN Gateway lokacja-lokacja           |      : large_blue_diamond:  |


**Inne niż regionalne**

|     Produkty                                  |     Odporność    |
|-----------------------------------------------|:-------------------:|
|     Azure DNS                                 |     : globe_with_meridians:             |
|     Azure Active Directory                  |     : globe_with_meridians:             |
|     Azure Advanced Threat Protection          |     : globe_with_meridians:             |
|     Azure Advisor                             |     : globe_with_meridians:             |
|     Azure Blueprints                          |     : globe_with_meridians:             |
|     Azure Bot Services                        |     : globe_with_meridians:             |
|     Azure Front Door                          |     : globe_with_meridians:             |
|     Usługa Azure Defender dla IoT                  |     : globe_with_meridians:             |
|     Azure Front Door                           |     : globe_with_meridians:             |
|     Azure Information Protection            |     : globe_with_meridians:             |
|     Azure Lighthouse                        |     : globe_with_meridians:             |
|     Azure Managed Applications              |     : globe_with_meridians:             |
|     Azure Maps                                |     : globe_with_meridians:             |
|     Azure Policy                              |     : globe_with_meridians:             |
|     Wykres zasobów platformy Azure                    |     : globe_with_meridians:             |
|     Usługa Azure Sentinel                            |     : globe_with_meridians:             |
|     Azure Stack                               |     : globe_with_meridians:             |
|     Azure Stack krawędź                        |     : globe_with_meridians:             |
|     Cloud Shell                               |     : globe_with_meridians:             |
|     Content Delivery Network                  |     : globe_with_meridians:             |
|     Cost Management                           |     : globe_with_meridians:             |
|     Skrytka klienta Microsoft Azure    |     : globe_with_meridians:             |
|     Intune                                    |     : globe_with_meridians:             |
|     Microsoft Azure usługi komunikacji równorzędnej         |     : globe_with_meridians:             |
|     Microsoft Azure Portal                  |     : globe_with_meridians:             |
|     Microsoft Cloud App Security              |     : globe_with_meridians:             |
|     Microsoft Graph                           |     : globe_with_meridians:             |
|     Security Center                         |     : globe_with_meridians:             |
|     Traffic Manager                         |     : globe_with_meridians:             |


## <a name="pricing-for-vms-in-availability-zones"></a>Cennik dla maszyn wirtualnych w Strefy dostępności

Nie ma dodatkowych opłat za maszyny wirtualne wdrożone w strefie dostępności. Aby uzyskać więcej informacji, zapoznaj się ze [stroną cennika przepustowości](https://azure.microsoft.com/pricing/details/bandwidth/).


## <a name="get-started-with-availability-zones"></a>Wprowadzenie do Strefy dostępności

- [Tworzenie maszyny wirtualnej](../virtual-machines/windows/create-portal-availability-zone.md)
- [Dodawanie dysku zarządzanego przy użyciu programu PowerShell](../virtual-machines/windows/attach-disk-ps.md#add-an-empty-data-disk-to-a-virtual-machine)
- [Utwórz strefowo nadmiarowy zestaw skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md)
- [Równoważenie obciążenia maszyn wirtualnych między strefami przy użyciu usługa Load Balancer w warstwie Standardowa ze strefowo nadmiarowy fronton](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Równoważenie obciążenia maszyn wirtualnych w strefie przy użyciu usługa Load Balancer w warstwie Standardowa z strefą frontonu](../load-balancer/quickstart-load-balancer-standard-public-cli.md?tabs=option-1-create-load-balancer-standard)
- [Magazyn strefowo nadmiarowy](../storage/common/storage-redundancy.md)
- [SQL Database warstwa ogólnego przeznaczenia](../azure-sql/database/high-availability-sla.md#general-purpose-service-tier-zone-redundant-availability-preview)
- [Geograficzne odzyskiwanie po awarii w usłudze Event Hubs](../event-hubs/event-hubs-geo-dr.md#availability-zones)
- [Geograficzne odzyskiwanie po awarii w usłudze Service Bus](../service-bus-messaging/service-bus-geo-dr.md#availability-zones)
- [Tworzenie strefowo nadmiarowej bramy sieci wirtualnej](../vpn-gateway/create-zone-redundant-vnet-gateway.md)
- [Dodaj region nadmiarowy strefy dla Azure Cosmos DB](../cosmos-db/high-availability.md#availability-zone-support)
- [Wprowadzenie usługi Azure cache for Redis Strefy dostępności](https://gist.github.com/JonCole/92c669ea482bbb7996f6428fb6c3eb97#file-redisazgettingstarted-md)
- [Utwórz wystąpienie usługi Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md)
- [Utwórz klaster usługi Azure Kubernetes Service (AKS), który używa Strefy dostępności](../aks/availability-zones.md)


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Regiony i strefy dostępności na platformie Azure](az-overview.md)
