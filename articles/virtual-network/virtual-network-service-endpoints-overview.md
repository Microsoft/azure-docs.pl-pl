---
title: Punkty końcowe usługi dla sieci wirtualnej platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak włączyć bezpośredni dostęp do zasobów platformy Azure z sieci wirtualnej przy użyciu punktów końcowych usługi.
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2019
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: 5e97a35a272684a99e1ca21cbec9c8f3edef253c
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097426"
---
# <a name="virtual-network-service-endpoints"></a>Punkty końcowe usługi dla sieci wirtualnej

Punkt końcowy usługi Virtual Network (VNet) zapewnia bezpieczną i bezpośrednią łączność z usługami platformy Azure za pośrednictwem zoptymalizowanej trasy za pośrednictwem sieci szkieletowej platformy Azure. Punkty końcowe umożliwiają zabezpieczanie krytycznych zasobów usługi platformy Azure tylko do sieci wirtualnych. Punkty końcowe usługi umożliwiają używanie prywatnych adresów IP w sieci wirtualnej w celu uzyskania dostępu do punktu końcowego usługi platformy Azure bez konieczności publicznego adresu IP w sieci wirtualnej.

Ta funkcja jest dostępna dla następujących regionów i usług platformy Azure. *Microsoft. \** Resource znajduje się w nawiasach. Włącz ten zasób ze strony podsieci podczas konfigurowania punktów końcowych usługi dla usługi:

**Ogólnie dostępne**

- **[Usługa Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (*Microsoft. Storage*): ogólnie dostępna we wszystkich regionach świadczenia usługi Azure.
- **[Azure SQL Database](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): ogólnie dostępna we wszystkich regionach świadczenia usługi Azure.
- **[Azure Synapse Analytics](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): ogólnie dostępna we wszystkich regionach świadczenia usługi Azure dla dedykowanych pul SQL (dawniej SQL DW).
- **[Serwer Azure Database for PostgreSQL](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): ogólnie dostępny w regionach platformy Azure, w których usługa bazy danych jest dostępna.
- **[Serwer Azure Database for MySQL](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. SQL*): ogólnie dostępny w regionach platformy Azure, w których usługa bazy danych jest dostępna.
- **[Azure Database for MariaDB](../mariadb/concepts-data-access-security-vnet.md)** (*Microsoft. SQL*): ogólnie dostępna w regionach platformy Azure, w których usługa bazy danych jest dostępna.
- **[Azure Cosmos DB](../cosmos-db/how-to-configure-vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. AzureCosmosDB*): ogólnie dostępna we wszystkich regionach świadczenia usługi Azure.
- **[Azure Key Vault](../key-vault/general/overview-vnet-service-endpoints.md)** (*Microsoft. datamagazyn*): ogólnie dostępna we wszystkich regionach świadczenia usługi Azure.
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. ServiceBus*): ogólnie dostępna we wszystkich regionach świadczenia usługi Azure.
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. EventHub*): ogólnie dostępna we wszystkich regionach świadczenia usługi Azure.
- **[Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft. usługi azureactivedirectory*): ogólnie dostępna we wszystkich regionach świadczenia usługi Azure, w których usługa ADLS Gen1 jest dostępna.
- **[Azure App Service](../app-service/app-service-ip-restrictions.md)** (*Microsoft. Web*): ogólnie dostępna we wszystkich regionach świadczenia usługi Azure, w których usługa App Service jest dostępna.
- **[Azure Cognitive Services](../cognitive-services/cognitive-services-virtual-networks.md?tabs=portal)** (*Microsoft. CognitiveServices*): ogólnie dostępna we wszystkich regionach świadczenia usługi Azure, w których są dostępne.

**Publiczna wersja zapoznawcza**

- **[Azure Container Registry](../container-registry/container-registry-vnet.md)** (*Microsoft. ContainerRegistry*): wersja zapoznawcza dostępna w ograniczonej liczbie regionów świadczenia usługi Azure, w której Azure Container Registry jest dostępna.

Najbardziej aktualne powiadomienia można znaleźć na stronie [aktualizacji usługi Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="key-benefits"></a>Najważniejsze korzyści

Punkty końcowe usługi oferują następujące korzyści:

- **Ulepszone zabezpieczenia zasobów usługi platformy Azure**: prywatne przestrzenie adresowe sieci wirtualnej mogą nakładać się na siebie. Nie można używać nakładających się spacji do unikatowego identyfikowania ruchu pochodzącego z sieci wirtualnej. Punkty końcowe usługi zapewniają możliwość zabezpieczania zasobów usługi platformy Azure w sieci wirtualnej przez rozszerzenie tożsamości wirtualnej do usługi. Po włączeniu punktów końcowych usługi w sieci wirtualnej możesz dodać regułę sieci wirtualnej, aby zabezpieczyć zasoby usługi platformy Azure w sieci wirtualnej. Dodanie reguły zapewnia ulepszone zabezpieczenia przez całkowite usunięcie publicznego dostępu do Internetu do zasobów i umożliwienie ruchu tylko z sieci wirtualnej.
- **Optymalny Routing ruchu usług platformy Azure z sieci wirtualnej**: dzisiaj wszystkie trasy w sieci wirtualnej, które wymuszają ruch internetowy do urządzeń lokalnych i/lub wirtualnych, wymuszają również ruch usługi platformy Azure w taki sam sposób jak ruch internetowy. Punkty końcowe usługi zapewniają optymalny routing ruchu platformy Azure. 

  Punkty końcowe zawsze pobierają ruch bezpośrednio z sieci wirtualnej do usługi w sieci szkieletowej platformy Microsoft Azure. Zachowywanie ruchu w sieci szkieletowej platformy Azure umożliwia kontynuowanie inspekcji i monitorowania wychodzącego ruchu internetowego z sieci wirtualnych za pośrednictwem tunelowania wymuszanego, bez wywierania wpływu na ruch usługi. Aby uzyskać więcej informacji na temat tras zdefiniowanych przez użytkownika i tunelowania wymuszonego, zobacz [routing ruchu w sieci wirtualnej platformy Azure](virtual-networks-udr-overview.md).
- **Prosta konfiguracja i mniejsze koszty ogólne zarządzania**: nie potrzebujesz już zastrzeżonych publicznych adresów IP w swoich sieciach wirtualnych, aby zabezpieczać zasoby platformy Azure za pośrednictwem zapory adresów IP. Nie są wymagane żadne urządzenia translacji adresów sieciowych (NAT) ani Brama wymagana do skonfigurowania punktów końcowych usługi. Punkty końcowe usługi można skonfigurować za pomocą prostego kliknięcia w podsieci. Obsługa punktów końcowych nie wiąże się z dodatkowymi kosztami.

## <a name="limitations"></a>Ograniczenia

- Funkcja jest dostępna tylko dla sieci wirtualnych wdrożonych za pomocą modelu wdrażania przy użyciu usługi Azure Resource Manager.
- Punkty końcowe są włączone w podsieciach skonfigurowanych w sieciach wirtualnych platformy Azure. Punktów końcowych nie można używać dla ruchu pochodzącego z lokalizacji lokalnych do usług platformy Azure. Aby uzyskać więcej informacji, zobacz [bezpieczny dostęp do usługi platformy Azure z lokalnego](#secure-azure-services-to-virtual-networks)
- W przypadku usługi Azure SQL punkt końcowy usługi dotyczy tylko ruchu usługi platformy Azure w regionie sieci wirtualnej. W przypadku usługi Azure Storage punkty końcowe również przechodzą w celu uwzględnienia par regionów, w których wdrożono sieć wirtualną w celu obsługi ruchu Read-Access Geo-Redundant Storage (RA-GRS) i Geo-Redundant Storage (GRS). Aby uzyskać więcej informacji, zobacz [regiony sparowane na platformie Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
- W przypadku Azure Data Lake Storage (ADLS) gen 1 Funkcja integracji sieci wirtualnej jest dostępna tylko w sieciach wirtualnych w tym samym regionie. Należy również zauważyć, że integracja sieci wirtualnej dla ADLS Gen1 korzysta z zabezpieczeń punktu końcowego usługi sieci wirtualnej między siecią wirtualną a Azure Active Directory (Azure AD) w celu wygenerowania dodatkowych oświadczeń zabezpieczeń w tokenie dostępu. Te oświadczenia są następnie używane do uwierzytelniania sieci wirtualnej na koncie usługi Data Lake Storage Gen1 i uzyskania dostępu. Tag *Microsoft. usługi azureactivedirectory* wymieniony w obszarze usługi obsługujące punkty końcowe usługi jest używany tylko dla punktów końcowych usługi pomocniczej ADLS generacji 1. Usługa Azure AD nie obsługuje natywnych punktów końcowych usługi. Aby uzyskać więcej informacji na temat integracji z siecią wirtualną Azure Data Lake Store generacji 1, zobacz [zabezpieczenia sieci w programie Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="secure-azure-services-to-virtual-networks"></a>Zabezpieczanie usług platformy Azure w sieciach wirtualnych

- Punkt końcowy usługi dla sieci wirtualnej zapewnia tożsamość sieci wirtualnej w usłudze platformy Azure. Po włączeniu punktów końcowych usługi w sieci wirtualnej możesz dodać regułę sieci wirtualnej, aby zabezpieczyć zasoby usługi platformy Azure w sieci wirtualnej.
- Obecnie ruch usługi platformy Azure z sieci wirtualnej używa publicznych adresów IP jako źródłowych adresów IP. Dzięki punktom końcowym usługi ruch usługi jest przełączany na używanie prywatnych adresów sieci wirtualnej jako źródłowych adresów IP w przypadku uzyskiwania dostępu do usługi platformy Azure z sieci wirtualnej. To przełączenie umożliwia dostęp do usług bez konieczności stosowania zastrzeżonych publicznych adresów IP w zaporach adresów IP.

   >[!NOTE]
   > Dzięki punktom końcowym usługi źródłowe adresy IP maszyn wirtualnych w podsieci dla ruchu w usłudze mogą przełączyć się z używania publicznych adresów IPv4 na używanie prywatnych adresów IPv4. Istniejące reguły zapory usługi platformy Azure używające publicznych adresów IP platformy Azure przestaną działać po tym przełączeniu. Przed rozpoczęciem konfigurowania punktów końcowych usługi sprawdź, czy reguły zapory usługi platformy Azure zezwalają na ten przełącznik. Może również wystąpić tymczasowe przerwanie ruchu usługi z tej podsieci podczas konfigurowania punktów końcowych usługi. 
 
## <a name="secure-azure-service-access-from-on-premises"></a>Zabezpieczanie dostępu do usługi platformy Azure z poziomu lokalnego

  Domyślnie zasoby usługi platformy Azure zabezpieczone za pośrednictwem sieci wirtualnych nie są dostępne z sieci lokalnych. Jeśli chcesz zezwolić na ruch z lokalnego, musisz również zezwolić na publiczne (zazwyczaj NAT) adresy IP z lokalnego lub ExpressRoute. Te adresy IP można dodać za pomocą konfiguracji zapory adresów IP dla zasobów usługi platformy Azure.

  ExpressRoute: Jeśli korzystasz z usługi [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do publicznej komunikacji równorzędnej lub komunikacji równorzędnej firmy Microsoft z miejsca lokalnego, musisz zidentyfikować używane adresy IP translatora adresów sieciowych. W przypadku publicznej komunikacji równorzędnej każdy obwód usługi ExpressRoute domyślnie używa dwóch adresów IP translatora adresów sieciowych, które są stosowane do ruchu usług platformy Azure, gdy ruch przechodzi do Microsoft Azure sieci szkieletowej. W przypadku komunikacji równorzędnej firmy Microsoft adresy IP translatora adresów sieciowych są udostępniane przez dostawcę usług lub przez niego udostępniane. Aby umożliwić dostęp do zasobów usługi, musisz zezwolić na te publiczne adresy IP w ustawieniu zapory adresu IP zasobu. Aby znaleźć adresy IP obwodów usługi ExpressRoute publicznej komunikacji równorzędnej, [otwórz bilet pomocy technicznej przy użyciu usługi ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) w witrynie Azure Portal. Aby uzyskać więcej informacji na temat translatora adresów sieciowych dla usługi ExpressRoute Public i komunikacji równorzędnej firmy Microsoft, zobacz [wymagania dotyczące EXPRESSROUTE NAT](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

![Zabezpieczanie usług platformy Azure w sieciach wirtualnych](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Konfigurowanie

- Skonfiguruj punkty końcowe usługi w podsieci w sieci wirtualnej. Punkty końcowe współpracują z wystąpieniami obliczeniowymi dowolnego typu uruchomionymi w danej podsieci.
- W podsieci można skonfigurować wiele punktów końcowych usługi dla wszystkich obsługiwanych usług platformy Azure (na przykład Azure Storage lub Azure SQL Database).
- W przypadku usługi Azure SQL Database sieci wirtualne muszą znajdować się w tym samym regionie, co zasób usługi platformy Azure. Jeśli korzystasz z magazynów GRS i RA-GRS w usłudze Azure Storage, konto podstawowe powinno znajdować się w tym samym regionie, co sieć wirtualna. W przypadku wszystkich innych usług można zabezpieczyć zasoby usługi platformy Azure w sieciach wirtualnych w dowolnym regionie. 
- Sieć wirtualna, w której konfigurowany jest punkt końcowy, może należeć do tej samej subskrypcji, co zasób usługi platformy Azure, lub innej. Aby uzyskać więcej informacji na temat uprawnień wymaganych do konfigurowania punktów końcowych i zabezpieczania usług platformy Azure, zobacz [Aprowizowanie](#provisioning).
- W przypadku obsługiwanych usług można zabezpieczyć nowe lub istniejące zasoby w sieciach wirtualnych za pomocą punktów końcowych usługi.

### <a name="considerations"></a>Zagadnienia do rozważenia

- Po włączeniu punktu końcowego usługi źródłowe adresy IP przełączają się z używania publicznych adresów IPv4, aby używać ich prywatnego adresu IPv4 podczas komunikacji z usługą z tej podsieci. Wszystkie otwarte połączenia TCP z usługą są zamykane podczas tego przełączania. Upewnij się, że żadne krytyczne zadania nie działają podczas włączania lub wyłączania punktu końcowego w usłudze dla podsieci. Upewnij się również, czy aplikacje mogą automatycznie połączyć się z usługami platformy Azure po przełączeniu adresu IP.

  Przełączenie adresu IP ma wpływ tylko na ruch usługi z sieci wirtualnej. Nie ma to wpływu na żaden inny ruch kierowany do lub z publicznych adresów IPv4 przypisanych do maszyn wirtualnych. Jeśli masz istniejące reguły zapory używające publicznych adresów IP platformy Azure dla usług platformy Azure, te reguły przestają działać po przełączeniu na użycie prywatnych adresów sieci wirtualnej.
- W przypadku punktów końcowych usługi wpisy DNS dla usług platformy Azure pozostają dzisiaj i nadal są rozwiązywane z publicznymi adresami IP przypisanymi do usługi platformy Azure.

- Sieciowe grupy zabezpieczeń z punktami końcowymi usługi:
  - Domyślnie sieciowych grup zabezpieczeń zezwala na ruch wychodzący z Internetu, a także zezwala na ruch z sieci wirtualnej do usług platformy Azure. Ten ruch nadal współpracuje z punktami końcowymi usługi, ponieważ jest to. 
  - Jeśli chcesz odmówić całego ruchu wychodzącego z Internetu i zezwolić tylko na ruch do określonych usług platformy Azure, możesz to zrobić za pomocą [tagów usługi](./network-security-groups-overview.md#service-tags) w sieciowych grup zabezpieczeń. Obsługiwane usługi platformy Azure można określić jako miejsce docelowe w regułach sieciowej grupy zabezpieczeń, a platforma Azure zapewnia konserwację adresów IP poszczególnych tagów. Aby uzyskać więcej informacji, zobacz [Azure Service tags for NSGs (Tagi usług platformy Azure dla sieciowych grup zabezpieczeń)](./network-security-groups-overview.md#service-tags). 

### <a name="scenarios"></a>Scenariusze

- **Równorzędne lub połączone sieci wirtualne albo wiele sieci wirtualnych**: aby zabezpieczyć usługi platformy Azure w wielu podsieciach sieci wirtualnej lub wielu sieciach wirtualnych, możesz niezależnie włączyć punkty końcowe usługi w każdej z tych podsieci i zabezpieczyć zasoby usługi platformy Azure we wszystkich tych podsieciach.
- **Filtrowanie ruchu wychodzącego z sieci wirtualnej do usług platformy Azure**: Jeśli chcesz przeprowadzić inspekcję lub filtrowanie ruchu wysyłanego do usługi platformy Azure z sieci wirtualnej, możesz wdrożyć sieciowe urządzenie wirtualne w sieci wirtualnej. Następnie możesz zastosować punkty końcowe usługi do podsieci, w której wdrożono sieciowe urządzenie wirtualne, i zabezpieczyć zasób usługi platformy Azure tylko w tej podsieci. Ten scenariusz może być przydatny, jeśli chcesz użyć filtrowania wirtualnych urządzeń sieciowych w celu ograniczenia dostępu do usługi platformy Azure z sieci wirtualnej tylko do określonych zasobów platformy Azure. Aby uzyskać więcej informacji, zobacz [ruch wychodzący z sieciowych urządzeń wirtualnych](/azure/architecture/reference-architectures/dmz/nva-ha).
- **Zabezpieczanie zasobów platformy Azure w usługach wdrożonych bezpośrednio w sieciach wirtualnych**: można bezpośrednio wdrożyć różne usługi platformy Azure w określonych podsieciach w sieci wirtualnej. Zasoby usługi platformy Azure można zabezpieczyć w podsieciach [usługi zarządzanej](virtual-network-for-azure-services.md), konfigurując punkt końcowy usługi w podsieci usługi zarządzanej.
- **Ruch dyskowy z maszyny wirtualnej platformy Azure**: ruch dysku maszyny wirtualnej dla dysków zarządzanych i niezarządzanych nie ma wpływ na zmiany routingu punktów końcowych usługi Azure Storage. Ten ruch obejmuje diskIO oraz Instalowanie i odinstalowywanie. Możesz ograniczyć dostęp do obiektów BLOB do stron, aby wybierać sieci za pomocą punktów końcowych usługi i [reguł sieci usługi Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

### <a name="logging-and-troubleshooting"></a>Rejestrowanie i rozwiązywanie problemów

Po skonfigurowaniu punktów końcowych usługi do określonej usługi Sprawdź, czy trasa punktu końcowego usługi obowiązuje: 
 
- Weryfikowanie źródłowego adresu IP każdego żądania obsługi w diagnostyce usługi. Wszystkie nowe żądania z punktami końcowymi usługi pokazują pozycję „źródłowy adres IP” dla żądania jako prywatny adres sieci wirtualnej przypisany do klienta wysyłającego żądanie z sieci wirtualnej. Bez punktu końcowego ten adres będzie publicznym adresem IP platformy Azure.
- Wyświetlanie skutecznych tras w dowolnym interfejsie sieciowym w podsieci. Trasa do usługi:
  - Pokazuje dokładniej określoną domyślną trasę do zakresu prefiksów adresów danej usługi.
  - Ma element nextHopType o wartości *VirtualNetworkServiceEndpoint*.
  - Wskazuje, że bardziej bezpośrednie połączenie z usługą jest stosowane w porównaniu z trasami tunelowania wymuszonego

>[!NOTE]
> Trasy punktu końcowego usługi zastępują trasy BGP lub UDR dla dopasowania prefiksu adresu usługi platformy Azure. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z obowiązującymi trasami](diagnose-network-routing-problem.md).

## <a name="provisioning"></a>Inicjowanie obsługi

Punkty końcowe usługi można konfigurować w sieciach wirtualnych niezależnie od użytkownika z dostępem do zapisu w sieci wirtualnej. Aby zabezpieczyć zasoby usługi platformy Azure w sieci wirtualnej, użytkownik musi mieć uprawnienia do *witryny Microsoft. Network/virtualNetworks/Subnets/joinViaServiceEndpoint/Action* dla dodanych podsieci. Wbudowane role administratora usługi domyślnie obejmują to uprawnienie. Uprawnienia można modyfikować, tworząc role niestandardowe.

Aby uzyskać więcej informacji na temat ról wbudowanych, zobacz [role wbudowane platformy Azure](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Aby uzyskać więcej informacji o przypisywaniu określonych uprawnień do ról niestandardowych, zobacz [role niestandardowe platformy Azure](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Sieci wirtualne i zasoby usług platformy Azure mogą należeć do tej samej lub różnych subskrypcji. Jeśli zasoby sieci wirtualnej i usługi platformy Azure należą do różnych subskrypcji, zasoby muszą znajdować się w tej samej dzierżawie usługi Active Directory (AD). 

## <a name="pricing-and-limits"></a>Ceny i limity

Za korzystanie z punktów końcowych usługi nie są naliczane dodatkowe opłaty. Bieżący model cen usług platformy Azure (Azure Storage, Azure SQL Database itp.) dotyczy dzisiaj.

Nie ma limitu całkowitej liczby punktów końcowych usługi w sieci wirtualnej.

Niektóre usługi platformy Azure, takie jak konta usługi Azure Storage, mogą wymuszać limity liczby podsieci używanych do zabezpieczania zasobu. Szczegółowe informacje znajdują się w dokumentacji dotyczącej różnych usług w sekcji [następne kroki](#next-steps) .

## <a name="vnet-service-endpoint-policies"></a>Zasady punktu końcowego usługi sieci wirtualnej 

Zasady punktu końcowego usługi sieci wirtualnej umożliwiają filtrowanie ruchu w sieci wirtualnej do usług platformy Azure. Ten filtr umożliwia użycie tylko określonych zasobów usługi platformy Azure dla punktów końcowych usługi. Zasady punktu końcowego usługi zapewniają szczegółową kontrolę dostępu w przypadku ruchu w sieci wirtualnej do usług platformy Azure. Aby uzyskać więcej informacji, zobacz [Virtual Network zasad punktu końcowego usługi](./virtual-network-service-endpoint-policies-overview.md).

## <a name="faqs"></a>Często zadawane pytania

Często zadawane pytania można znaleźć w temacie [Virtual Network często zadawane pytania dotyczące punktu końcowego usługi](./virtual-networks-faq.md#virtual-network-service-endpoints).

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie punktów końcowych usługi sieci wirtualnej](tutorial-restrict-network-access-to-resources.md)
- [Zabezpieczanie konta usługi Azure Storage w sieci wirtualnej](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Zabezpieczanie Azure SQL Database w sieci wirtualnej](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Zabezpieczanie usługi Azure Synapse Analytics w sieci wirtualnej](../azure-sql/database/vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Integracja usług platformy Azure w sieciach wirtualnych](virtual-network-for-azure-services.md)
- [Zasady punktu końcowego usługi Virtual Network](./virtual-network-service-endpoint-policies-overview.md)
- [Szablon usługi Azure Resource Manager](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)