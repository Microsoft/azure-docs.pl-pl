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
ms.openlocfilehash: b75e0c1e53f1e00579de73897197cdd2f14d79af
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81455414"
---
# <a name="virtual-network-service-endpoints"></a>Punkty końcowe usługi dla sieci wirtualnej

Punkty końcowe usługi sieci wirtualnej (VNet) rozszerzają prywatną przestrzeń adresową sieci wirtualnej. Punkty końcowe również rozszerzyć tożsamość sieci wirtualnej do usług platformy Azure za pomocą bezpośredniego połączenia. Punkty końcowe umożliwiają zabezpieczanie krytycznych zasobów usługi platformy Azure tylko do sieci wirtualnych. Ruch z sieci wirtualnej do usługi platformy Azure zawsze pozostaje w sieci szkieletowej platformy Microsoft Azure.

Ta funkcja jest dostępna dla następujących usług i regionów platformy Azure. Zasób *firmy Microsoft.\* * jest w nawiasie. Włącz ten zasób od strony podsieci podczas konfigurowania punktów końcowych usługi dla usługi:

**Ogólnie dostępne**

- **[Usługa Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** *(Microsoft.Storage):* ogólnie dostępne we wszystkich regionach platformy Azure.
- **[Usługa Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.Sql):* ogólnie dostępne we wszystkich regionach platformy Azure.
- **[Usługa Azure SQL Data Warehouse](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.Sql):* ogólnie dostępne we wszystkich regionach platformy Azure.
- **[Usługa Azure Database for PostgreSQL server](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.Sql):* Ogólnie dostępne w regionach platformy Azure, w których usługa bazy danych jest dostępna.
- **[Usługa Azure Database for MySQL server](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.Sql):* Ogólnie dostępne w regionach platformy Azure, w których usługa bazy danych jest dostępna.
- **[Usługa Azure Database for MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** *(Microsoft.Sql):* ogólnie dostępne w regionach platformy Azure, w których usługa bazy danych jest dostępna.
- **[Usługa Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.AzureCosmosDB):* ogólnie dostępne we wszystkich regionach platformy Azure.
- **[Usługa Azure Key Vault](../key-vault/general/overview-vnet-service-endpoints.md)** *(Microsoft.KeyVault):* ogólnie dostępne we wszystkich regionach platformy Azure.
- **[Usługa Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.ServiceBus):* ogólnie dostępne we wszystkich regionach platformy Azure.
- **[Usługi Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.EventHub):* ogólnie dostępne we wszystkich regionach platformy Azure.
- **[Usługa Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.AzureActiveDirectory):* Ogólnie dostępne we wszystkich regionach platformy Azure, w których dostępna jest usługa ADLS Gen1.
- **[Usługa Azure App Service:](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)** ogólnie dostępne we wszystkich regionach platformy Azure, w których usługa app jest dostępna.

**Publiczna wersja zapoznawcza**

- **[Usługa Azure Container Registry](../container-registry/container-registry-vnet.md)** *(Microsoft.ContainerRegistry):* Podgląd dostępny we wszystkich regionach platformy Azure, w których jest dostępny rejestr kontenerów platformy Azure.

Najbardziej aktualne powiadomienia można znaleźć na stronie [aktualizacji usługi Azure Virtual Network](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="key-benefits"></a>Najważniejsze korzyści

Punkty końcowe usługi oferują następujące korzyści:

- **Ulepszone zabezpieczenia zasobów usługi platformy Azure:** prywatne przestrzenie adresowe sieci wirtualnej mogą się nakładać. Nie można używać nakładających się spacji, aby jednoznacznie zidentyfikować ruch pochodzący z sieci wirtualnej. Punkty końcowe usługi zapewniają możliwość zabezpieczania zasobów usługi platformy Azure do sieci wirtualnej, rozszerzając tożsamość sieci wirtualnej na usługę. Po włączeniu punktów końcowych usługi w sieci wirtualnej można dodać regułę sieci wirtualnej w celu zabezpieczenia zasobów usługi platformy Azure do sieci wirtualnej. Dodanie reguły zapewnia większe bezpieczeństwo, całkowicie usuwając publiczny dostęp do Internetu do zasobów i zezwalając na ruch tylko z sieci wirtualnej.
- **Optymalne routing dla ruchu usługi platformy Azure z sieci wirtualnej:** obecnie wszystkie trasy w sieci wirtualnej, które wymuszają ruch internetowy do urządzeń lokalnych i/lub wirtualnych, również wymuszają ruch usługi platformy Azure do korzystania z tej samej trasy co ruch internetowy. Punkty końcowe usługi zapewniają optymalny routing ruchu platformy Azure. 

  Punkty końcowe zawsze pobierają ruch bezpośrednio z sieci wirtualnej do usługi w sieci szkieletowej platformy Microsoft Azure. Zachowywanie ruchu w sieci szkieletowej platformy Azure umożliwia kontynuowanie inspekcji i monitorowania wychodzącego ruchu internetowego z sieci wirtualnych za pośrednictwem tunelowania wymuszanego, bez wywierania wpływu na ruch usługi. Aby uzyskać więcej informacji na temat tras zdefiniowanych przez użytkownika i tunelowania wymuszonego, zobacz [Routing ruchu sieci wirtualnej platformy Azure](virtual-networks-udr-overview.md).
- **Prosta konfiguracja i mniejsze koszty ogólne zarządzania**: nie potrzebujesz już zastrzeżonych publicznych adresów IP w swoich sieciach wirtualnych, aby zabezpieczać zasoby platformy Azure za pośrednictwem zapory adresów IP. Do skonfigurowania punktów końcowych usługi nie jest wymagane translacja adresów sieciowych ani urządzenia bramy. Punkty końcowe usługi można skonfigurować za pomocą prostego kliknięcia podsieci. Nie ma żadnych dodatkowych narzutów na utrzymanie punktów końcowych.

## <a name="limitations"></a>Ograniczenia

- Funkcja jest dostępna tylko dla sieci wirtualnych wdrożonych za pomocą modelu wdrażania przy użyciu usługi Azure Resource Manager.
- Punkty końcowe są włączone w podsieciach skonfigurowanych w sieciach wirtualnych platformy Azure. Punktów końcowych nie można używać dla ruchu z pomieszczeń do usług platformy Azure. Aby uzyskać więcej informacji, zobacz [Bezpieczny dostęp do usługi platformy Azure z lokalnego](#secure-azure-services-to-virtual-networks)
- W przypadku usługi Azure SQL punkt końcowy usługi dotyczy tylko ruchu usługi platformy Azure w regionie sieci wirtualnej. W przypadku usługi Azure Storage punkty końcowe obejmują również sparowane regiony, w których można wdrożyć sieć wirtualną w celu obsługi ruchu magazynu geograficznego dostępu do odczytu (RA-GRS) i magazynu geograficznego (GRS). Aby uzyskać więcej informacji, zobacz [Sparowane regiony platformy Azure](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions).
- W przypadku usługi Azure Data Lake Storage (ADLS) Gen 1 funkcja integracji sieci wirtualnej jest dostępna tylko dla sieci wirtualnych w tym samym regionie. Należy również zauważyć, że integracja sieci wirtualnej dla usługi ADLS Gen1 używa zabezpieczeń punktu końcowego usługi sieci wirtualnej między siecią wirtualną a usługą Azure Active Directory (Azure AD) do generowania dodatkowych oświadczeń zabezpieczeń w tokenie dostępu. Te oświadczenia są następnie używane do uwierzytelniania sieci wirtualnej na koncie usługi Data Lake Storage Gen1 i uzyskania dostępu. Tag *Microsoft.AzureActiveDirectory* wymieniony w obszarze usługi obsługujące punkty końcowe usługi jest używany tylko do obsługi punktów końcowych usługi do ADLS Gen 1. Usługa Azure AD nie obsługuje punktów końcowych usługi natywnie. Aby uzyskać więcej informacji na temat integracji sieci wirtualnej usługi Azure Data Lake Store gen 1, zobacz [Zabezpieczenia sieci w usłudze Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="secure-azure-services-to-virtual-networks"></a>Bezpieczne usługi platformy Azure dla sieci wirtualnych

- Punkt końcowy usługi dla sieci wirtualnej zapewnia tożsamość sieci wirtualnej w usłudze platformy Azure. Po włączeniu punktów końcowych usługi w sieci wirtualnej można dodać regułę sieci wirtualnej w celu zabezpieczenia zasobów usługi platformy Azure do sieci wirtualnej.
- Obecnie ruch usługi platformy Azure z sieci wirtualnej używa publicznych adresów IP jako źródłowych adresów IP. Dzięki punktom końcowym usługi ruch usługi jest przełączany na używanie prywatnych adresów sieci wirtualnej jako źródłowych adresów IP w przypadku uzyskiwania dostępu do usługi platformy Azure z sieci wirtualnej. To przełączenie umożliwia dostęp do usług bez konieczności stosowania zastrzeżonych publicznych adresów IP w zaporach adresów IP.

   >[!NOTE]
   > Dzięki punktom końcowym usługi źródłowe adresy IP maszyn wirtualnych w podsieci dla ruchu w usłudze mogą przełączyć się z używania publicznych adresów IPv4 na używanie prywatnych adresów IPv4. Istniejące reguły zapory usługi platformy Azure używające publicznych adresów IP platformy Azure przestaną działać po tym przełączeniu. Przed rozpoczęciem konfigurowania punktów końcowych usługi sprawdź, czy reguły zapory usługi platformy Azure zezwalają na ten przełącznik. Może również wystąpić tymczasowe przerwanie ruchu usługi z tej podsieci podczas konfigurowania punktów końcowych usługi. 
 
## <a name="secure-azure-service-access-from-on-premises"></a>Bezpieczny dostęp do usługi platformy Azure z lokalnego

  Domyślnie zasoby usługi platformy Azure zabezpieczone do sieci wirtualnych nie są osiągalne z sieci lokalnych. Jeśli chcesz zezwolić na ruch z lokalnego, należy również zezwolić na publiczne (zazwyczaj, NAT) adresy IP z lokalnego lub usługi ExpressRoute. Te adresy IP można dodać za pośrednictwem konfiguracji zapory IP dla zasobów usługi platformy Azure.

  Usługa ExpressRoute: jeśli używasz [usługi ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) do publicznego komunikacji równorzędnej lub komunikacji równorzędnej firmy Microsoft z twojego lokalu, musisz zidentyfikować używane adresy IP NAT. W przypadku komunikacji równorzędnej publicznej każdy obwód usługi ExpressRoute domyślnie używa dwóch adresów IP NAT stosowanych do ruchu usługi platformy Azure, gdy ruch wejdą w szkielet sieci Microsoft Azure. W przypadku komunikacji równorzędnej firmy Microsoft adresy IP NAT są dostarczane przez klienta lub dostarczane przez usługodawcę.Aby umożliwić dostęp do zasobów usługi, musisz zezwolić na te publiczne adresy IP w ustawieniu zapory adresu IP zasobu.Aby znaleźć adresy IP obwodów usługi ExpressRoute publicznej komunikacji równorzędnej, [otwórz bilet pomocy technicznej przy użyciu usługi ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) w witrynie Azure Portal. Aby uzyskać więcej informacji na temat translatora nat dla usługi ExpressRoute public i microsoft peering, zobacz [Wymagania usługi ExpressRoute NAT](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering).

![Zabezpieczanie usług platformy Azure w sieciach wirtualnych](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Konfigurowanie

- Konfigurowanie punktów końcowych usługi w podsieci w sieci wirtualnej. Punkty końcowe współpracują z wystąpieniami obliczeniowymi dowolnego typu uruchomionymi w danej podsieci.
- Można skonfigurować wiele punktów końcowych usługi dla wszystkich obsługiwanych usług platformy Azure (na przykład usługi Azure Storage lub Azure SQL Database) w podsieci.
- W przypadku usługi Azure SQL Database sieci wirtualne muszą znajdować się w tym samym regionie, co zasób usługi platformy Azure. Jeśli korzystasz z magazynów GRS i RA-GRS w usłudze Azure Storage, konto podstawowe powinno znajdować się w tym samym regionie, co sieć wirtualna. W przypadku wszystkich innych usług można zabezpieczyć zasoby usługi platformy Azure do sieci wirtualnych w dowolnym regionie. 
- Sieć wirtualna, w której konfigurowany jest punkt końcowy, może należeć do tej samej subskrypcji, co zasób usługi platformy Azure, lub innej. Aby uzyskać więcej informacji na temat uprawnień wymaganych do konfigurowania punktów końcowych i zabezpieczania usług platformy Azure, zobacz [Aprowizowanie](#provisioning).
- W przypadku obsługiwanych usług można zabezpieczyć nowe lub istniejące zasoby w sieciach wirtualnych za pomocą punktów końcowych usługi.

### <a name="considerations"></a>Zagadnienia do rozważenia

- Po włączeniu punktu końcowego usługi źródłowe adresy IP maszyn wirtualnych w przełączniku podsieci. Źródłowe adresy IP przełączają się od używania publicznych adresów IPv4 do używania ich prywatnego adresu IPv4 podczas komunikowania się z usługą z tej podsieci. Wszystkie otwarte połączenia TCP z usługą są zamykane podczas tego przełączania. Upewnij się, że żadne krytyczne zadania nie działają podczas włączania lub wyłączania punktu końcowego w usłudze dla podsieci. Upewnij się również, czy aplikacje mogą automatycznie połączyć się z usługami platformy Azure po przełączeniu adresu IP.

  Przełączenie adresu IP ma wpływ tylko na ruch usługi z sieci wirtualnej. Nie ma wpływu na inny ruch adresowany do lub z publicznych adresów IPv4 przypisanych do maszyn wirtualnych. Jeśli masz istniejące reguły zapory używające publicznych adresów IP platformy Azure dla usług platformy Azure, te reguły przestają działać po przełączeniu na użycie prywatnych adresów sieci wirtualnej.
- Dzięki punktom końcowym usługi wpisy DNS dla usług platformy Azure pozostają aktualne i nadal będą rozwiązywać publiczne adresy IP przypisane do usługi Azure.

- Sieciowe grupy zabezpieczeń z punktami końcowymi usługi:
  - Domyślnie sieciowe sieciowe umożliwiają wychodzący ruch internetowy, a także zezwalają na ruch z sieci wirtualnej do usług platformy Azure. Ten ruch nadal działa z punktami końcowymi usługi, jak jest. 
  - Jeśli chcesz odmówić całego ruchu wychodzącego internetu i zezwolić tylko na ruch do określonych usług platformy Azure, możesz to zrobić przy użyciu [tagów usługi](security-overview.md#service-tags) w sieciowej grupowej. Obsługiwane usługi platformy Azure można określić jako miejsce docelowe w regułach sieciowej sieciowej sieciowej, a platforma Azure zapewnia również obsługę adresów IP leżących u podstaw każdego tagu. Aby uzyskać więcej informacji, zobacz [Azure Service tags for NSGs (Tagi usług platformy Azure dla sieciowych grup zabezpieczeń)](security-overview.md#service-tags). 

### <a name="scenarios"></a>Scenariusze

- **Równorzędne lub połączone sieci wirtualne albo wiele sieci wirtualnych**: aby zabezpieczyć usługi platformy Azure w wielu podsieciach sieci wirtualnej lub wielu sieciach wirtualnych, możesz niezależnie włączyć punkty końcowe usługi w każdej z tych podsieci i zabezpieczyć zasoby usługi platformy Azure we wszystkich tych podsieciach.
- **Filtrowanie ruchu wychodzącego z sieci wirtualnej do usług platformy Azure:** Jeśli chcesz sprawdzić lub filtrować ruch wysyłany do usługi platformy Azure z sieci wirtualnej, możesz wdrożyć wirtualne urządzenie sieciowe w sieci wirtualnej. Następnie możesz zastosować punkty końcowe usługi do podsieci, w której wdrożono sieciowe urządzenie wirtualne, i zabezpieczyć zasób usługi platformy Azure tylko w tej podsieci. W tym scenariuszu może być przydatne, jeśli chcesz użyć filtrowania urządzeń wirtualnych sieci, aby ograniczyć dostęp do usługi platformy Azure z sieci wirtualnej tylko do określonych zasobów platformy Azure. Aby uzyskać więcej informacji, zobacz [ruch wychodzący z sieciowych urządzeń wirtualnych](/azure/architecture/reference-architectures/dmz/nva-ha).
- **Zabezpieczanie zasobów platformy Azure w usługach wdrożonych bezpośrednio w sieciach wirtualnych:** można bezpośrednio wdrożyć różne usługi platformy Azure w określonych podsieciach w sieci wirtualnej. Zasoby usługi platformy Azure można zabezpieczyć w podsieciach [usługi zarządzanej](virtual-network-for-azure-services.md), konfigurując punkt końcowy usługi w podsieci usługi zarządzanej.
- **Ruch dysku z maszyny wirtualnej platformy Azure:** ruch na dysku maszyny wirtualnej dla dysków zarządzanych i niezarządzanych nie ma wpływu na zmiany routingu punktów końcowych usługi dla usługi Azure Storage. Ten ruch obejmuje diskIO, a także montaż i odinstalowanie. Można ograniczyć dostęp REST do stronicowych obiektów blob, aby wybrać sieci za pośrednictwem punktów końcowych usługi i [reguł sieci usługi Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 

### <a name="logging-and-troubleshooting"></a>Rejestrowanie i rozwiązywanie problemów

Po skonfigurowaniu punktów końcowych usługi do określonej usługi sprawdź, czy trasa punktu końcowego usługi jest obowiązuje przez: 
 
- Weryfikowanie źródłowego adresu IP każdego żądania obsługi w diagnostyce usługi. Wszystkie nowe żądania z punktami końcowymi usługi pokazują pozycję „źródłowy adres IP” dla żądania jako prywatny adres sieci wirtualnej przypisany do klienta wysyłającego żądanie z sieci wirtualnej. Bez punktu końcowego ten adres będzie publicznym adresem IP platformy Azure.
- Wyświetlanie skutecznych tras w dowolnym interfejsie sieciowym w podsieci. Trasa do usługi:
  - Pokazuje dokładniej określoną domyślną trasę do zakresu prefiksów adresów danej usługi.
  - Ma element nextHopType o wartości *VirtualNetworkServiceEndpoint*.
  - Wskazuje, że w yszła bardziej bezpośrednie połączenie z usługą w porównaniu z wszelkimi drogami tunelowania

>[!NOTE]
> Trasy punktu końcowego usługi zastępują trasy BGP lub UDR dla dopasowania prefiksu adresu usługi platformy Azure. Aby uzyskać więcej informacji, zobacz [rozwiązywanie problemów z efektywnymi trasami](diagnose-network-routing-problem.md).

## <a name="provisioning"></a>Inicjowanie obsługi

Punkty końcowe usługi mogą być konfigurowane w sieciach wirtualnych niezależnie przez użytkownika z dostępem do zapisu do sieci wirtualnej. Aby zabezpieczyć zasoby usługi platformy Azure do sieci wirtualnej, użytkownik musi mieć uprawnienia do *Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action* dla dodanych podsieci. Wbudowane role administratora usługi domyślnie zawierają to uprawnienie. Uprawnienie można zmodyfikować, tworząc role niestandardowe.

Aby uzyskać więcej informacji na temat ról wbudowanych, zobacz [Wbudowane role zasobów platformy Azure.](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Aby uzyskać więcej informacji na temat przypisywania określonych uprawnień do ról niestandardowych, zobacz [Role niestandardowe dla zasobów platformy Azure](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Sieci wirtualne i zasoby usług platformy Azure mogą należeć do tej samej lub różnych subskrypcji. Jeśli zasoby sieci wirtualnej i usługi platformy Azure należą do różnych subskrypcji, zasoby muszą znajdować się w tej samej dzierżawie usługi Active Directory (AD). 

## <a name="pricing-and-limits"></a>Ceny i limity

Nie ma żadnych dodatkowych opłat za korzystanie z punktów końcowych usługi. Bieżący model cenowy dla usług platformy Azure (usługa Azure Storage, usługa Azure SQL Database itp.) ma zastosowanie w stanie obecnym.

Nie ma limitu całkowitej liczby punktów końcowych usługi w sieci wirtualnej.

Niektóre usługi platformy Azure, takie jak konta usługi Azure Storage, mogą wymuszać limity liczby podsieci używanych do zabezpieczania zasobu. Szczegółowe informacje można znaleźć w dokumentacji różnych usług w sekcji [Następne kroki.](#next-steps)

## <a name="vnet-service-endpoint-policies"></a>Zasady punktu końcowego usługi sieci wirtualnej 

Zasady punktu końcowego usługi sieci wirtualnej umożliwiają filtrowanie ruchu sieci wirtualnej do usług platformy Azure. Ten filtr zezwala tylko na określone zasoby usługi platformy Azure za pomocą punktów końcowych usługi. Zasady punktu końcowego usługi zapewniają szczegółową kontrolę dostępu w przypadku ruchu w sieci wirtualnej do usług platformy Azure. Aby uzyskać więcej informacji, zobacz [Zasady punktu końcowego usługi sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview).

## <a name="faqs"></a>Często zadawane pytania

Aby zapoznać się z często zadawanymi pytaniami, zobacz Często zadawane pytania [dotyczące punktu końcowego usługi sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints).

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie punktów końcowych usługi dla sieci wirtualnej](tutorial-restrict-network-access-to-resources.md)
- [Zabezpieczanie konta usługi Azure Storage w sieci wirtualnej](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Zabezpieczanie bazy danych SQL w usłudze Azure w sieci wirtualnej](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Zabezpieczanie magazynu danych SQL usługi Azure w sieci wirtualnej](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Integracja usług platformy Azure w sieciach wirtualnych](virtual-network-for-azure-services.md)
- [Zasady punktu końcowego usługi sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)
- [Szablon usługi Azure Resource Manager](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)

