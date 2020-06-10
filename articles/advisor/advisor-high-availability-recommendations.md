---
title: Zwiększ dostępność aplikacji dzięki Azure Advisor
description: Użyj Azure Advisor, aby zwiększyć wysoką dostępność wdrożeń platformy Azure.
ms.topic: article
ms.date: 01/29/2019
ms.openlocfilehash: bed092a51b5a4aba1dfa64c17f5ed3d6f72212da
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84658467"
---
# <a name="improve-availability-of-your-application-with-azure-advisor"></a>Zwiększ dostępność aplikacji dzięki Azure Advisor

Azure Advisor pomaga zapewnić i poprawić ciągłość aplikacji o znaczeniu krytycznym dla firmy. Zalecenia dotyczące wysokiej dostępności można uzyskać, korzystając z karty **wysokiej dostępności** na pulpicie nawigacyjnym usługi Advisor.

## <a name="ensure-virtual-machine-fault-tolerance"></a>Zapewnianie odporności na uszkodzenia maszyny wirtualnej

Aby zapewnić nadmiarowość aplikacji, zalecamy grupowanie co najmniej dwóch maszyn wirtualnych w zestawie dostępności. Doradca identyfikuje maszyny wirtualne, które nie są częścią zestawu dostępności i zaleca ich przeniesienie do zestawu dostępności. Ta konfiguracja gwarantuje, że podczas planowanego lub nieplanowanego zdarzenia konserwacji jest dostępna co najmniej jedna maszyna wirtualna i będzie ona zgodna z umową SLA maszyny wirtualnej platformy Azure. Można utworzyć zestaw dostępności dla maszyny wirtualnej lub dodać maszynę wirtualną do istniejącego zestawu dostępności.

> [!NOTE]
> Jeśli wybierzesz opcję utworzenia zestawu dostępności, musisz dodać co najmniej jedną maszynę wirtualną do niej. Zalecamy grupowanie co najmniej dwóch maszyn wirtualnych w zestawie dostępności, aby upewnić się, że podczas przestoju jest dostępny przynajmniej jeden komputer.

## <a name="ensure-availability-set-fault-tolerance"></a>Zapewnianie odporności na uszkodzenia zestawu dostępności

Aby zapewnić nadmiarowość aplikacji, zalecamy grupowanie co najmniej dwóch maszyn wirtualnych w zestawie dostępności. Doradca identyfikuje zestawy dostępności, które zawierają pojedynczą maszynę wirtualną i zaleca dodanie do niej co najmniej jednej maszyny wirtualnej.Ta konfiguracja gwarantuje, że podczas planowanego lub nieplanowanego zdarzenia konserwacji jest dostępna co najmniej jedna maszyna wirtualna i będzie ona zgodna z umową SLA maszyny wirtualnej platformy Azure.Można utworzyć maszynę wirtualną lub dodać istniejącą maszynę wirtualną do zestawu dostępności.  

## <a name="use-managed-disks-to-improve-data-reliability"></a>Korzystanie z funkcji Dyski zarządzane w celu zwiększania niezawodności danych

Maszyny wirtualne, które znajdują się w zestawie dostępności z dyskami, które korzystają z kont magazynu lub jednostek skalowania magazynu, nie są odporne na awarie pojedynczej jednostki skalowania magazynu podczas przestoju. Usługa Advisor zidentyfikuje te zestawy dostępności i zaleca migrację do usługi Azure Managed Disks. Dzięki temu dyski różnych maszyn wirtualnych w zestawie dostępności są wystarczająco izolowane, aby uniknąć single point of failure. 

## <a name="known-issue-with-check-point-network-virtual-appliance-image-version"></a>Znany problem dotyczący wersji obrazu Check Point wirtualnego urządzenia sieciowego

Doradca może określić, czy na maszynie wirtualnej może być uruchomiona wersja obrazu programu Check Point, która została uznana za utratę łączności sieciowej w przypadku operacji obsługi platformy. Zalecenie doradcy pomoże Ci uaktualnić do nowszej wersji obrazu, który rozwiązuje ten problem. Zapewni to ciągłość działania dzięki lepszej łączności sieciowej.

## <a name="ensure-application-gateway-fault-tolerance"></a>Upewnij się, że Tolerancja błędów w usłudze Application Gateway

To zalecenie zapewnia ciągłość działania aplikacji o kluczowym znaczeniu, które są obsługiwane przez bramy aplikacji. Doradca identyfikuje wystąpienia bramy aplikacji, które nie są skonfigurowane pod kątem odporności na uszkodzenia, i sugeruje akcje korygowania, które można wykonać. Klasyfikator identyfikuje średnią lub dużą bramę aplikacji z jednym wystąpieniem i zaleca dodanie co najmniej jednego wystąpienia. Identyfikuje ona również aplikacje małych aplikacji o pojedynczej lub wielu wystąpieniach i zaleca Migrowanie do średnich lub dużych jednostek SKU. Doradca zaleca te działania, aby upewnić się, że wystąpienia usługi Application Gateway zostały skonfigurowane tak, aby spełniały bieżące wymagania SLA dotyczące tych zasobów.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Ochrona danych maszyny wirtualnej przed przypadkowym usunięciem

Konfiguracja kopii zapasowej maszyny wirtualnej zapewnia dostępność danych o kluczowym znaczeniu dla firmy i zapewnia ochronę przed przypadkowym usunięciem lub uszkodzeniem. Program Advisor identyfikuje maszyny wirtualne, których kopia zapasowa nie jest włączona i zaleca włączenie tworzenia kopii zapasowych. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>Upewnij się, że masz dostęp do ekspertów w chmurze platformy Azure, gdy ich potrzebujesz

W przypadku wykonywania obciążeń o krytycznym znaczeniu dla firmy ważne jest, aby mieć dostęp do pomocy technicznej w razie potrzeby. Doradca identyfikuje potencjalne subskrypcje o krytycznym znaczeniu dla firmy, które nie mają wsparcia technicznego zawartego w planie pomocy technicznej i zalecają uaktualnienie do opcji, która obejmuje pomoc techniczną.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Utwórz alerty Azure Service Health, aby otrzymywać powiadomienia o problemach z platformą Azure

Zalecamy skonfigurowanie alertów Azure Service Health, aby otrzymywać powiadomienia o problemach z usługą platformy Azure. [Azure Service Health](https://azure.microsoft.com/features/service-health/) to bezpłatna usługa, która zapewnia spersonalizowane wskazówki i pomoc techniczną, gdy ma to wpływ na problem z usługą platformy Azure. Klasyfikator identyfikuje subskrypcje, dla których nie skonfigurowano alertów i zaleca ich utworzenie.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Skonfiguruj Traffic Manager punkty końcowe pod kątem odporności

Profile Traffic Manager z więcej niż jednym punktem końcowym o większej dostępności w przypadku niepowodzenia danego punktu końcowego. Umieszczenie punktów końcowych w różnych regionach dodatkowo zwiększa niezawodność usługi. Klasyfikator identyfikuje profile usługi Traffic Manager, w których istnieje tylko jeden punkt końcowy i zaleca dodanie co najmniej jednego punktu końcowego w innym regionie.

Jeśli wszystkie punkty końcowe w profilu Traffic Manager skonfigurowanym dla routingu bliskości znajdują się w tym samym regionie, użytkownicy z innych regionów mogą napotkać opóźnienia połączeń. Dodanie lub przeniesienie punktu końcowego do innego regionu poprawi ogólną wydajność i zapewni lepszą dostępność, jeśli wszystkie punkty końcowe w jednym regionie zakończą się niepowodzeniem. Usługa Advisor identyfikuje Traffic Manager profile skonfigurowane dla routingu bliskości, w których wszystkie punkty końcowe znajdują się w tym samym regionie. Zalecamy dodanie lub przeniesienie punktu końcowego do innego regionu platformy Azure.

Jeśli profil Traffic Manager jest skonfigurowany pod kątem routingu geograficznego, ruch jest kierowany do punktów końcowych w oparciu o zdefiniowane regiony. W przypadku niepowodzenia regionu nie ma wstępnie zdefiniowanego trybu failover. Punkt końcowy, w którym grupowanie regionalne jest skonfigurowany na wartość "wszystko (świat)", pozwala uniknąć porzucenia ruchu i zwiększyć dostępność usługi. Usługa Advisor identyfikuje profil Traffic Manager skonfigurowany pod kątem routingu geograficznego, w którym nie ma punktu końcowego skonfigurowanego jako "wszystko (świat)". Zalecamy zmianę konfiguracji, aby utworzyć punkt końcowy "wszystko (świat)".

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Użyj nietrwałego usunięcia na koncie usługi Azure Storage, aby zapisać i odzyskać dane po przypadkowym zastąpieniu lub usunięciu

Włącz [usuwanie nietrwałe](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) na koncie magazynu, aby usunięte obiekty blob przechodzą do stanu nietrwałego usunięcia. Gdy dane są zastępowane, generowana jest migawka usuwania nietrwałego w celu zapisania stanu zastąpionych danych. Użycie funkcji usuwania nietrwałego umożliwia odzyskanie w przypadku przypadkowych usunięć lub zastępowaniem. Usługa Advisor identyfikuje konta usługi Azure Storage, dla których nie włączono usuwania nietrwałego i sugeruje włączenie go.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>Skonfiguruj bramę sieci VPN do usługi Active-Active na potrzeby odporności połączeń

W konfiguracji Active-Active, oba wystąpienia bramy sieci VPN będą ustanawiać tunele sieci VPN S2S do lokalnego urządzenia sieci VPN. Gdy planowane zdarzenie konserwacji lub nieplanowane zdarzenie do jednego wystąpienia bramy, ruch zostanie przełączony do innego aktywnego tunelu IPsec automatycznie. Azure Advisor zidentyfikuje bramy sieci VPN, które nie są skonfigurowane jako aktywne-aktywne i sugerują skonfigurowanie ich pod kątem wysokiej dostępności.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Używanie bram produkcyjnych sieci VPN do uruchamiania obciążeń produkcyjnych

Azure Advisor sprawdza wszystkie bramy sieci VPN będące podstawową jednostką SKU i zaleca użycie produkcyjnej jednostki SKU. Podstawowa jednostka SKU jest przeznaczona do celów deweloperskich i testowych. Produkcyjne jednostki SKU oferują większą liczbę tuneli, obsługę protokołu BGP, opcje konfiguracji aktywne-aktywne, niestandardowe zasady IPSec/IKE i wyższą stabilność i dostępność.

## <a name="repair-invalid-log-alert-rules"></a>Napraw nieprawidłowe reguły alertów dziennika

Azure Advisor wykryje reguły alertów, które mają nieprawidłowe zapytania określone w sekcji warunku. Reguły alertów dziennika są tworzone w usłudze Azure Monitor i służą do uruchamiania zapytań analitycznych w określonych odstępach czasu. Wyniki zapytania określają, czy trzeba wyzwolić alert. Zapytania analityczne z upływem czasu mogą stać się nieprawidłowe z powodu zmian w zasobach, tabelach lub poleceniach, do których się odwołują. Usługa Advisor zaleca poprawienie zapytania w regule alertu, aby uniemożliwić jego ręczne pobranie i zapewnienie monitorowania zasobów na platformie Azure. [Dowiedz się więcej o rozwiązywaniu problemów z regułami alertów](https://aka.ms/aa_logalerts_queryrepair)

## <a name="configure-consistent-indexing-mode-on-your-cosmos-db-collection"></a>Skonfiguruj spójny tryb indeksowania w kolekcji Cosmos DB

Kontenery Azure Cosmos DB skonfigurowane z trybem indeksowania z opóźnieniem mogą mieć wpływ na świeżość wyników zapytania. Program Advisor wykrywa kontenery skonfigurowane w ten sposób i zaleca się przełączenie na tryb spójny. [Dowiedz się więcej na temat zasad indeksowania w Cosmos DB](https://aka.ms/cosmosdb/how-to-manage-indexing-policy)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Konfigurowanie kontenerów usługi Azure Cosmos DB przy użyciu klucza partycji

Azure Advisor zidentyfikuje Azure Cosmos DB kolekcje niepartycjonowane, które zbliżają się do przydzielonego limitu przydziału magazynu. Zalecamy Migrowanie tych kolekcji do nowych kolekcji z definicją klucza partycji, dzięki czemu mogą być automatycznie skalowane przez usługę. [Dowiedz się więcej na temat wybierania klucza partycji](https://aka.ms/cosmosdb/choose-partitionkey)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Uaktualnianie zestawu .NET SDK dla usługi Azure Cosmos DB do najnowszej wersji z narzędzia Nuget

Azure Advisor zidentyfikuje konta Azure Cosmos DB, które używają starych wersji zestawu .NET SDK, i zaleca się przeprowadzenie uaktualnienia do najnowszej wersji z programu NuGet w celu uzyskania najnowszych poprawek, ulepszeń wydajności i nowych możliwości funkcji. [Dowiedz się więcej na temat Cosmos DB .NET SDK](https://aka.ms/cosmosdb/sql-api-sdk-dotnet)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Uaktualnianie zestawu Java SDK dla usługi Azure Cosmos DB do najnowszej wersji z programu Maven

Azure Advisor będzie identyfikować konta Azure Cosmos DB, które używają starych wersji zestawu Java SDK i zalecać uaktualnienie do najnowszej wersji z Maven dla najnowszych poprawek, ulepszeń wydajności i nowych funkcji. [Dowiedz się więcej na temat Cosmos DB Java SDK](https://aka.ms/cosmosdb/sql-api-sdk-async-java)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Uaktualnianie łącznika platformy Spark dla usługi Azure Cosmos DB do najnowszej wersji z programu Maven

Azure Advisor zidentyfikuje konta Azure Cosmos DB, które używają starych wersji łącznika programu Cosmos DB Spark i zalecamy uaktualnienie do najnowszej wersji z Maven dla najnowszych poprawek, ulepszeń wydajności i nowych funkcji. [Dowiedz się więcej o łączniku Cosmos DB Spark](https://aka.ms/cosmosdb/spark-connector)

## <a name="upgrade-recommendation-for-deprecation-of-kafka-11-in-hdinsight-40-kafka-cluster"></a>Zalecenie dotyczące uaktualnienia dotyczące wycofania Kafka 1,1 w klastrze usługi HDInsight 4,0 Kafka

Od 1 lipca 2020 r. klienci nie będą mogli tworzyć nowych klastrów platformy Kafka za pomocą platformy Kafka 1.1 w usłudze HDInsight 4.0. Istniejące klastry będą działać w obecnym stanie bez pomocy technicznej firmy Microsoft. Rozważ przejście na platformę Kafka 2.1 w usłudze HDInsight 4.0 do 30 czerwca 2020 r., aby uniknąć potencjalnych przerw w działaniu systemu lub pomocy technicznej.

## <a name="upgrade-recommendation-for-deprecation-of-older-spark-versions-in-hdinsight-spark-cluster"></a>Zalecenie dotyczące uaktualnienia dla wycofania starszych wersji platformy Spark w klastrze usługi HDInsight Spark

Od 1 lipca 2020 r. klienci nie będą mogli tworzyć nowych klastrów platformy Spark za pomocą platformy Spark 2.1 i 2.2 w usłudze HDInsight 3.6 oraz platformy Spark 2.3 w usłudze HDInsight 4.0. Istniejące klastry będą działać w obecnym stanie bez pomocy technicznej firmy Microsoft. ",

## <a name="enable-virtual-machine-replication"></a>Włącz replikację maszyn wirtualnych
Maszyny wirtualne, które nie mają włączonej replikacji w innym regionie, nie są odporne na awarie regionalne. Replikowanie maszyn wirtualnych zmniejsza niekorzystny wpływ na działalność biznesową w czasie awarii regionu platformy Azure. Usługa Advisor wykrywa maszyny wirtualne, które nie mają włączonej replikacji i zaleca włączenie replikacji, tak aby w przypadku awarii można szybko przełączać maszyny wirtualne w odległym regionie platformy Azure. [Dowiedz się więcej o replikacji maszyny wirtualnej](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-quickstart)

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Jak uzyskać dostęp do rekomendacji o wysokiej dostępności w usłudze Advisor

1. Zaloguj się do [Azure Portal](https://portal.azure.com), a następnie otwórz program [Advisor](https://aka.ms/azureadvisordashboard).

2.  Na pulpicie nawigacyjnym usługi Advisor kliknij kartę **wysoka dostępność** .

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zaleceń klasyfikatora, zobacz:
* [Wprowadzenie do usługi Advisor](advisor-overview.md)
* [Wprowadzenie do usługi Advisor](advisor-get-started.md)
* [Zalecenia dotyczące kosztów usługi Advisor](advisor-cost-recommendations.md)
* [Zalecenia dotyczące wydajności usługi Advisor](advisor-performance-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń usługi Advisor](advisor-security-recommendations.md)
* [Zalecenia dotyczące doskonałości operacyjnej klasyfikatora](advisor-operational-excellence-recommendations.md)
