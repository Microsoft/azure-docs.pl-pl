---
title: Punkty końcowe usługi sieci wirtualnej — Azure Database for MariaDB
description: Opisuje, jak działają punkty końcowe usługi sieci wirtualnej dla serwera Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/17/2020
ms.openlocfilehash: 0863ccda9f292f3da9f7064a78ba700ab5962eb2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96014639"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-mariadb"></a>Używanie reguł i punktów końcowych usługi dla sieci wirtualnej w przypadku usługi Azure Database for MariaDB

*Reguły sieci wirtualnej* to jedna funkcja zabezpieczeń zapory, która kontroluje, czy serwer Azure Database for MariaDB akceptuje komunikację wysyłaną z określonych podsieci w sieciach wirtualnych. W tym artykule wyjaśniono, dlaczego funkcja reguły sieci wirtualnej jest czasami najlepszą opcją bezpiecznego zezwalania na komunikację z serwerem Azure Database for MariaDB.

Aby utworzyć regułę sieci wirtualnej, musi być to [Sieć wirtualna][vm-virtual-network-overview] (VNET) i [punkt końcowy usługi sieci wirtualnej][vm-virtual-network-service-endpoints-overview-649d] , aby uzyskać odwołanie do reguły. Na poniższej ilustracji przedstawiono sposób działania punktu końcowego usługi Virtual Network z Azure Database for MariaDB:

![Przykład działania punktu końcowego usługi sieci wirtualnej](media/concepts-data-access-security-vnet/vnet-concept.png)

> [!NOTE]
> Ta funkcja jest dostępna we wszystkich regionach platformy Azure, w których wdrożono Azure Database for MariaDB dla serwerów Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

Możesz również rozważyć użycie [prywatnego linku](concepts-data-access-security-private-link.md) dla połączeń. Link prywatny zapewnia prywatny adres IP w sieci wirtualnej dla serwera Azure Database for MariaDB.

<a name="anch-terminology-and-description-82f"></a>

## <a name="terminology-and-description"></a>Terminologia i opis

**Sieć wirtualna:** Możesz mieć sieci wirtualne skojarzone z subskrypcją platformy Azure.

**Podsieć:** Sieć wirtualna zawiera **podsieci**. Wszystkie maszyny wirtualne platformy Azure, które są przypisane do podsieci. Jedna podsieć może zawierać wiele maszyn wirtualnych lub innych węzłów obliczeniowych. Węzły obliczeniowe znajdujące się poza siecią wirtualną nie mogą uzyskać dostępu do sieci wirtualnej, chyba że skonfigurowano zabezpieczenia, aby zezwolić na dostęp.

**Virtual Network punkt końcowy usługi:** [Punkt końcowy usługi Virtual Network][vm-virtual-network-service-endpoints-overview-649d] jest podsiecią, której wartości właściwości zawierają co najmniej jedną formalną nazwę typu usługi platformy Azure. W tym artykule interesuje Cię nazwę typu **Microsoft. SQL**, która odnosi się do usługi platformy Azure o nazwie SQL Database. Ten tag usługi dotyczy również usług Azure Database for MariaDB, MySQL i PostgreSQL. Należy pamiętać, że podczas stosowania znacznika usługi **Microsoft. SQL** do punktu końcowego usługi sieci wirtualnej zostanie skonfigurowany ruch punktu końcowego usługi dla wszystkich serwerów Azure SQL Database, Azure Database for MariaDB, Azure Database for MySQL i Azure Database for PostgreSQL w podsieci.

**Reguła sieci wirtualnej:** Reguła sieci wirtualnej dla serwera Azure Database for MariaDB to podsieć wymieniona na liście kontroli dostępu (ACL) serwera Azure Database for MariaDB. Aby znajdować się na liście ACL dla serwera Azure Database for MariaDB, podsieć musi zawierać nazwę typu **Microsoft. SQL** .

Reguła sieci wirtualnej instruuje serwer Azure Database for MariaDB, aby akceptował komunikację z każdego węzła znajdującego się w podsieci.







<a name="anch-benefits-of-a-vnet-rule-68b"></a>

## <a name="benefits-of-a-virtual-network-rule"></a>Zalety reguły sieci wirtualnej

Do momentu podjęcia działania maszyny wirtualne w podsieciach nie mogą komunikować się z serwerem Azure Database for MariaDB. Jedną z akcji, która ustanawia komunikację, jest utworzenie reguły sieci wirtualnej. Uzasadnienie wyboru podejścia reguły sieci wirtualnej wymaga dyskusji porównującej i kontrastowej obejmującej konkurencyjne opcje zabezpieczeń oferowane przez zaporę.

### <a name="a-allow-access-to-azure-services"></a>A. Zezwalanie na dostęp usługom platformy Azure

W okienku zabezpieczenia połączenia jest dostępny przycisk **włączania/WYłączania** , który ma etykietę **Zezwalaj na dostęp do usług platformy Azure**. Ustawienie **on** umożliwia komunikację ze wszystkimi adresami IP platformy Azure i wszystkimi podsieciami platformy Azure. Te adresy IP lub podsieci platformy Azure mogą nie należeć do użytkownika. To **ustawienie jest** prawdopodobnie dłużej otwierane, niż chcesz, aby baza danych Azure Database for MariaDB. Funkcja reguły sieci wirtualnej oferuje znacznie bardziej precyzyjną kontrolę.

### <a name="b-ip-rules"></a>B. Reguły adresów IP

Zapora Azure Database for MariaDB pozwala określić zakresy adresów IP, z których komunikacja jest akceptowana na serwerze Azure Database for MariaDB. To podejście jest odpowiednie dla stabilnych adresów IP, które są poza siecią prywatną platformy Azure. Jednak wiele węzłów wewnątrz sieci prywatnej platformy Azure jest skonfigurowanych przy użyciu *dynamicznych* adresów IP. Dynamiczne adresy IP mogą ulec zmianie, na przykład po ponownym uruchomieniu maszyny wirtualnej. Folly do określenia dynamicznego adresu IP w regule zapory w środowisku produkcyjnym.

Możesz odzyskać opcję IP, uzyskując *statyczny* adres IP dla maszyny wirtualnej. Aby uzyskać szczegółowe informacje, zobacz [Konfigurowanie prywatnych adresów IP dla maszyny wirtualnej przy użyciu Azure Portal][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w].

Jednak podejście ze statycznym adresem IP może być trudne do zarządzania i jest kosztowne, gdy jest wykonywane w odpowiedniej skali. Reguły sieci wirtualnej są łatwiejsze do ustanowienia i zarządzania.


<a name="anch-details-about-vnet-rules-38q"></a>

## <a name="details-about-virtual-network-rules"></a>Szczegóły dotyczące reguł sieci wirtualnej

W tej sekcji opisano kilka szczegółowych informacji o regułach sieci wirtualnej.

### <a name="only-one-geographic-region"></a>Tylko jeden region geograficzny

Każdy punkt końcowy usługi Virtual Network ma zastosowanie tylko do jednego regionu platformy Azure. Punkt końcowy nie umożliwia innym regionom akceptowania komunikacji z podsieci.

Każda reguła sieci wirtualnej jest ograniczona do regionu, do którego odnosi się podstawowy punkt końcowy.

### <a name="server-level-not-database-level"></a>Poziom serwera, nie na poziomie bazy danych

Każda reguła sieci wirtualnej ma zastosowanie do całego serwera Azure Database for MariaDB, a nie tylko do jednej konkretnej bazy danych na serwerze. Innymi słowy reguła sieci wirtualnej jest stosowana na poziomie serwera, a nie na poziomie bazy danych.

### <a name="security-administration-roles"></a>Role administrowania zabezpieczeniami

Istnieje separacja ról zabezpieczeń w administrowaniu punktami końcowymi usługi Virtual Network. Wymagana jest akcja z każdej z następujących ról:

- **Administrator sieci:** &nbsp; Włącz punkt końcowy.
- **Administrator bazy danych:** &nbsp; Zaktualizuj listę kontroli dostępu (ACL), aby dodać daną podsieć do serwera Azure Database for MariaDB.

*Alternatywa dla usługi Azure RBAC:*

Role administratora sieci i administratora bazy danych mają więcej możliwości niż jest to konieczne do zarządzania regułami sieci wirtualnej. Wymagany jest tylko podzestaw ich możliwości.

Możesz skorzystać z funkcji [kontroli dostępu opartej na rolach (Azure RBAC)][rbac-what-is-813s] na platformie Azure, aby utworzyć pojedynczą rolę niestandardową, która ma tylko niezbędny podzbiór funkcji. Rola niestandardowa może być używana zamiast obejmuje administratora sieci lub administratora bazy danych. Powierzchnia obszaru ekspozycji zabezpieczeń jest niższa w przypadku dodania użytkownika do roli niestandardowej, a nie dodania użytkownika do innych dwóch głównych ról administratora.

> [!NOTE]
> W niektórych przypadkach Azure Database for MariaDB i podsieć wirtualna znajdują się w różnych subskrypcjach. W takich przypadkach należy zapewnić następujące konfiguracje:
> - Obie subskrypcje muszą znajdować się w tej samej dzierżawie Azure Active Directory.
> - Użytkownik ma wymagane uprawnienia do inicjowania operacji, takich jak Włączanie punktów końcowych usługi i Dodawanie podsieci wirtualnej do danego serwera.
> - Upewnij się, że w subskrypcji jest zarejestrowany dostawca zasobów **Microsoft. SQL** i **Microsoft. DBforMariaDB** . Aby uzyskać więcej informacji, zobacz temat [Resource-Manager-Registration][resource-manager-portal]

## <a name="limitations"></a>Ograniczenia

W przypadku Azure Database for MariaDB funkcja reguł sieci wirtualnej ma następujące ograniczenia:

- Aplikację sieci Web można zamapować na prywatny adres IP w sieci wirtualnej/podsieci. Nawet jeśli punkty końcowe usługi są włączone w danej sieci wirtualnej/podsieci, połączenia z aplikacji sieci Web na serwerze będą mieć publiczne Źródło IP platformy Azure, a nie Źródło sieci wirtualnej/podsieci. Aby włączyć łączność z aplikacji sieci Web na serwerze z regułami zapory VNet, należy zezwolić usługom platformy Azure na dostęp do serwera na serwerze.

- W zaporze dla Azure Database for MariaDB Każda reguła sieci wirtualnej odwołuje się do podsieci. Wszystkie te podsieci, do których istnieją odwołania, muszą być hostowane w tym samym regionie geograficznym, który hostuje Azure Database for MariaDB.

- Każdy serwer Azure Database for MariaDB może mieć maksymalnie 128 wpisów listy ACL dla danej sieci wirtualnej.

- Reguły sieci wirtualnej mają zastosowanie tylko do Azure Resource Manager sieci wirtualnych; i nie do [klasycznych sieci modeli wdrażania][resource-manager-deployment-model-568f] .

- Włączenie punktów końcowych usługi sieci wirtualnej do Azure Database for MariaDB przy użyciu znacznika usługi **Microsoft. SQL** włącza również punkty końcowe dla wszystkich usług Azure Database: Azure Database for MariaDB, Azure Database for MySQL, Azure Database for PostgreSQL, Azure SQL Database i Azure Synapse Analytics (dawniej SQL Data Warehouse).

- Obsługa punktów końcowych usługi sieci wirtualnej jest obsługiwana tylko w przypadku serwerów Ogólnego przeznaczenia i zoptymalizowanych pod kątem pamięci.

- Jeśli w podsieci jest włączona funkcja **Microsoft. SQL** , oznacza to, że do nawiązania połączenia mają być używane tylko reguły sieci wirtualnej. [Reguły zapory inne niż sieci wirtualnej](concepts-firewall-rules.md) w tej podsieci nie będą działały.

- Na zaporze zakresy adresów IP dotyczą następujących elementów sieciowych, ale reguły sieci wirtualnej nie są:
    - [Wirtualna sieć prywatna (VPN) typu lokacja-lokacja (S2S)][vpn-gateway-indexmd-608y]
    - Lokalna za pośrednictwem [ExpressRoute][expressroute-indexmd-744v]

## <a name="expressroute"></a>ExpressRoute

Jeśli sieć jest połączona z siecią platformy Azure za pomocą [ExpressRoute][expressroute-indexmd-744v], każdy obwód jest skonfigurowany z dwoma publicznymi adresami IP w przeglądarce Microsoft Edge. Te dwa adresy IP są używane do nawiązywania połączenia z usługami firmy Microsoft, takimi jak usługa Azure Storage, przy użyciu publicznej komunikacji równorzędnej Azure.

Aby umożliwić komunikację z obwodu do Azure Database for MariaDB, należy utworzyć reguły sieci IP dla publicznych adresów IP obwodów. Aby znaleźć publiczne adresy IP obwodu usługi ExpressRoute, Otwórz bilet pomocy technicznej z ExpressRoute przy użyciu Azure Portal.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Dodawanie reguły zapory sieci wirtualnej do serwera bez włączania punktów końcowych usługi sieci wirtualnej

Tylko ustawienie reguły zapory sieci wirtualnej nie pomaga zabezpieczyć serwera w sieci wirtualnej. Aby zabezpieczenia zaczęły obowiązywać, należy również włączyć punkty końcowe usługi **sieci** wirtualnej. Po włączeniu punktów końcowych usługi **w** usłudze Sieć wirtualna jest przestojem do momentu zakończenia przejścia z **trybu do trybu** **.** Jest to szczególnie prawdziwe w kontekście dużych sieci wirtualnych. Możesz użyć flagi **IgnoreMissingServiceEndpoint** , aby zmniejszyć lub wyeliminować przestoje podczas przejścia.

Flagę **IgnoreMissingServiceEndpoint** można ustawić za pomocą interfejsu wiersza polecenia platformy Azure lub portalu.

## <a name="related-articles"></a>Pokrewne artykuły:
- [Sieci wirtualne platformy Azure][vm-virtual-network-overview]
- [Punkty końcowe usługi dla sieci wirtualnej platformy Azure][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>Następne kroki
Artykuły dotyczące tworzenia reguł sieci wirtualnej można znaleźć w temacie:
- [Tworzenie reguł Azure Database for MariaDB sieci wirtualnej i zarządzanie nimi przy użyciu Azure Portal](howto-manage-vnet-portal.md)
 
<!--
- [Create and manage Azure Database for MariaDB VNet rules using Azure CLI](howto-manage-vnet-using-cli.md)
-->

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-deployment-model-568f]: ../azure-resource-manager/management/deployment-models.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../role-based-access-control/overview.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml

[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md