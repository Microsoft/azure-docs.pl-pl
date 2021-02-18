---
title: Zwiększ niezawodność aplikacji za pomocą usługi Advisor
description: Użyj Azure Advisor, aby zapewnić i poprawić niezawodność wdrożeń na platformie Azure o kluczowym znaczeniu dla firmy.
ms.topic: article
ms.date: 09/27/2020
ms.openlocfilehash: c96b47f1ee145129f4c14c6646f93abeb8a5aac9
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579979"
---
# <a name="improve-the-reliability-of-your-application-by-using-azure-advisor"></a>Zwiększ niezawodność aplikacji przy użyciu Azure Advisor

Azure Advisor pomaga zapewnić i poprawić ciągłość aplikacji o znaczeniu krytycznym dla firmy. Zalecenia dotyczące niezawodności można uzyskać, korzystając z karty **niezawodność** na pulpicie nawigacyjnym usługi Advisor.

## <a name="check-the-version-of-your-check-point-network-virtual-appliance-image"></a>Sprawdź wersję obrazu urządzenia wirtualnego w sieci

Program Advisor może określić, czy na maszynie wirtualnej jest uruchomiona wersja obrazu programu Test Point, która została uznana za utratę łączności sieciowej podczas operacji obsługi platformy. Zalecenie klasyfikatora pomoże Ci uaktualnić do nowszej wersji obrazu, który rozwiązuje ten problem. To sprawdzenie zapewni ciągłość działania dzięki lepszej łączności sieciowej.

## <a name="ensure-application-gateway-fault-tolerance"></a>Upewnij się, że Tolerancja błędów w usłudze Application Gateway

To zalecenie zapewnia ciągłość działania aplikacji o kluczowym znaczeniu, które są obsługiwane przez bramy aplikacji. Doradca identyfikuje wystąpienia bramy aplikacji, które nie są skonfigurowane pod kątem odporności na uszkodzenia. Następnie sugeruje akcje korygowania, które można wykonać. Klasyfikator identyfikuje średnią lub dużą bramę aplikacji z jednym wystąpieniem i zaleca dodanie co najmniej jednego wystąpienia. Identyfikuje ona także aplikacje małych wystąpień i wiele wystąpień oraz zaleca migrowanie ich do średnich lub dużych jednostek SKU. Doradca zaleca te działania, aby upewnić się, że wystąpienia usługi Application Gateway zostały skonfigurowane tak, aby spełniały bieżące wymagania SLA dotyczące tych zasobów.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>Ochrona danych maszyny wirtualnej przed przypadkowym usunięciem

Konfiguracja kopii zapasowej maszyny wirtualnej zapewnia dostępność danych o kluczowym znaczeniu dla firmy i zapewnia ochronę przed przypadkowym usunięciem lub uszkodzeniem. Doradca identyfikuje maszyny wirtualne, których kopia zapasowa nie jest włączona i zaleca włączenie tworzenia kopii zapasowych. 

## <a name="ensure-you-have-access-to-azure-experts-when-you-need-it"></a>Upewnij się, że masz dostęp do ekspertów platformy Azure, gdy ich potrzebujesz

W przypadku korzystania z obciążenia krytycznego dla firmy ważne jest, aby mieć dostęp do pomocy technicznej, gdy będzie potrzebna. Doradca identyfikuje potencjalne subskrypcje o krytycznym znaczeniu dla firmy, które nie mają wsparcia technicznego zawartego w planach pomocy technicznej. Zalecamy uaktualnienie do opcji, która obejmuje pomoc techniczną.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-problems-affect-you"></a>Utwórz alerty Azure Service Health, aby otrzymywać powiadomienia o problemach z platformą Azure

Zalecamy skonfigurowanie alertów Azure Service Health, aby otrzymywać powiadomienia o problemach z usługą platformy Azure. [Azure Service Health](https://azure.microsoft.com/features/service-health/) to bezpłatna usługa, która zapewnia spersonalizowane wskazówki i pomoc techniczną, gdy występuje problem z usługą platformy Azure. Klasyfikator identyfikuje subskrypcje, dla których nie skonfigurowano alertów, i zaleca ich skonfigurowanie.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>Skonfiguruj Traffic Manager punkty końcowe pod kątem odporności

Profile Traffic Manager platformy Azure z więcej niż jednym punktem końcowym mają wyższą dostępność w przypadku niepowodzenia danego punktu końcowego. Umieszczenie punktów końcowych w różnych regionach dodatkowo zwiększa niezawodność usługi. Klasyfikator identyfikuje profile usługi Traffic Manager, w których istnieje tylko jeden punkt końcowy i zaleca dodanie co najmniej jednego punktu końcowego w innym regionie.

Jeśli wszystkie punkty końcowe w profilu Traffic Manager skonfigurowanym dla routingu bliskości znajdują się w tym samym regionie, użytkownicy z innych regionów mogą napotkać opóźnienia połączeń. Dodanie lub przeniesienie punktu końcowego do innego regionu poprawi ogólną wydajność i zapewni lepszą dostępność, jeśli wszystkie punkty końcowe w jednym regionie zakończą się niepowodzeniem. Usługa Advisor identyfikuje Traffic Manager profile skonfigurowane dla routingu bliskości, w których wszystkie punkty końcowe znajdują się w tym samym regionie. Zalecamy dodanie lub przeniesienie punktu końcowego do innego regionu platformy Azure.

Jeśli profil Traffic Manager jest skonfigurowany pod kątem routingu geograficznego, ruch jest kierowany do punktów końcowych w oparciu o zdefiniowane regiony. W przypadku niepowodzenia regionu nie ma wstępnie zdefiniowanego trybu failover. Jeśli masz punkt końcowy, w którym jest skonfigurowane grupowanie regionalne **(na całym świecie)**, możesz uniknąć porzucenia ruchu i zwiększyć dostępność usługi. Usługa Advisor identyfikuje profil Traffic Manager skonfigurowany pod kątem routingu geograficznego, w którym nie ma punktu końcowego skonfigurowanego do grupowania regionalnego jako **wszystkie (na świecie)**. Zalecamy zmianę konfiguracji, aby utworzyć punkt końcowy **(World)**.

## <a name="use-soft-delete-on-your-azure-storage-account-to-save-and-recover-data-after-accidental-overwrite-or-deletion"></a>Użyj nietrwałego usunięcia na koncie usługi Azure Storage, aby zapisać i odzyskać dane po przypadkowym zastąpieniu lub usunięciu

Włącz [usuwanie nietrwałe](../storage/blobs/soft-delete-blob-overview.md) na koncie magazynu, aby usunięte obiekty blob przechodzą do stanu nietrwałego usunięcia. Gdy dane są zastępowane, generowana jest migawka usuwania nietrwałego w celu zapisania stanu zastąpionych danych. Użycie funkcji usuwania nietrwałego umożliwia odzyskiwanie po przypadkowym usunięciu lub zastępowaniem. Usługa Advisor identyfikuje konta usługi Azure Storage, dla których nie włączono usuwania nietrwałego i sugeruje włączenie go.

## <a name="configure-your-vpn-gateway-to-active-active-for-connection-resiliency"></a>Skonfiguruj bramę sieci VPN do usługi Active-Active na potrzeby odporności połączeń

W konfiguracji aktywne-aktywne oba wystąpienia bramy sieci VPN ustanawiają tunele sieci VPN S2S do lokalnego urządzenia sieci VPN. Gdy planowane zdarzenie konserwacji lub nieplanowane zdarzenie następuje do jednego wystąpienia bramy, ruch jest automatycznie przełączany do innego aktywnego tunelu IPsec. Azure Advisor identyfikuje bramy sieci VPN, które nie są skonfigurowane jako aktywne-aktywne i sugerują skonfigurowanie ich pod kątem wysokiej dostępności.

## <a name="use-production-vpn-gateways-to-run-your-production-workloads"></a>Używanie bram produkcyjnych sieci VPN do uruchamiania obciążeń produkcyjnych

Azure Advisor sprawdza wszystkie bramy sieci VPN korzystające z podstawowej jednostki SKU i zaleca użycie produkcyjnej jednostki SKU. Podstawowa jednostka SKU została zaprojektowana na potrzeby tworzenia i testowania. Oferta produkcyjnych jednostek SKU:
- Więcej tuneli. 
- Obsługa protokołu BGP. 
- Opcje konfiguracji Active-Active. 
- Niestandardowe zasady protokołu IPSec/IKE. 
- Wyższa stabilność i dostępność.

## <a name="ensure-virtual-machine-fault-tolerance-temporarily-disabled"></a>Zapewnianie odporności na uszkodzenia maszyny wirtualnej (tymczasowo wyłączone)

W celu zapewnienia nadmiarowości aplikacji zalecamy grupowanie co najmniej dwóch maszyn wirtualnych w zestawie dostępności. Doradca identyfikuje maszyny wirtualne, które nie są częścią zestawu dostępności i zaleca ich przeniesienie do jednej z nich. Ta konfiguracja gwarantuje, że podczas planowanej lub nieplanowanej konserwacji jest dostępna co najmniej jedna maszyna wirtualna i będzie ona zgodna z umową SLA maszyny wirtualnej platformy Azure. Można utworzyć zestaw dostępności dla maszyny wirtualnej lub dodać maszynę wirtualną do istniejącego zestawu dostępności.

> [!NOTE]
> Jeśli wybierzesz opcję utworzenia zestawu dostępności, musisz dodać co najmniej jedną maszynę wirtualną do niej. Zalecamy grupowanie co najmniej dwóch maszyn wirtualnych w zestawie dostępności, aby upewnić się, że podczas przestoju jest dostępny przynajmniej jeden komputer.

## <a name="ensure-availability-set-fault-tolerance-temporarily-disabled"></a>Upewnij się, że odporność na błędy zestawu dostępności (tymczasowo wyłączona)

W celu zapewnienia nadmiarowości aplikacji zalecamy grupowanie co najmniej dwóch maszyn wirtualnych w zestawie dostępności. Doradca identyfikuje zestawy dostępności, które zawierają pojedynczą maszynę wirtualną i zaleca dodanie do niej co najmniej jednej maszyny wirtualnej. Ta konfiguracja gwarantuje, że podczas planowanej lub nieplanowanej konserwacji jest dostępna co najmniej jedna maszyna wirtualna i będzie ona zgodna z umową SLA maszyny wirtualnej platformy Azure. Można utworzyć maszynę wirtualną lub dodać istniejącą maszynę wirtualną do zestawu dostępności.  

## <a name="use-managed-disks-to-improve-data-reliability-temporarily-disabled"></a>Korzystanie z dysków zarządzanych w celu zwiększenia niezawodności danych (tymczasowo wyłączone)

Maszyny wirtualne znajdujące się w zestawie dostępności z dyskami, które współużytkują konta magazynu lub jednostki skalowania magazynu, nie są odporne na awarie dla pojedynczych jednostek skalowania magazynu podczas przestoju. Usługa Advisor identyfikuje te zestawy dostępności i zaleca migrację do usługi Azure Managed Disks. Ta migracja zapewni wystarczającą izolację dysków maszyn wirtualnych w zestawie dostępności, aby uniknąć single point of failure. 

## <a name="repair-invalid-log-alert-rules"></a>Napraw nieprawidłowe reguły alertów dziennika

Azure Advisor wykrywa reguły alertów dziennika, które mają nieprawidłowe zapytania określone w sekcji warunku. Reguły alertów dziennika Azure Monitor uruchamiają zapytania z określoną częstotliwością i wyzwalają alerty na podstawie wyników. Zapytania mogą stać się nieprawidłowe w miarę upływu czasu ze względu na zmiany w przywoływanych zasobach, tabelach lub poleceniach. Usługa Advisor zaleca poprawki dla zapytań dotyczących alertów, aby zapobiec automatycznemu wyłączaniu reguł oraz w celu zapewnienia pokrycia monitorowania. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z regułami alertów](../azure-monitor/alerts/alerts-troubleshoot-log.md#query-used-in-a-log-alert-isnt-valid)

## <a name="configure-consistent-indexing-mode-on-your-azure-cosmos-db-collection"></a>Skonfiguruj spójny tryb indeksowania w kolekcji Azure Cosmos DB

Konfigurowanie kontenerów Azure Cosmos DB z trybem indeksowania z opóźnieniem może mieć wpływ na świeżość wyników zapytania. Klasyfikator wykrywa w ten sposób kontenery skonfigurowane w ten sposób i zaleca się przełączanie na tryb spójny. [Dowiedz się więcej na temat zasad indeksowania w Azure Cosmos DB.](../cosmos-db/how-to-manage-indexing-policy.md)

## <a name="configure-your-azure-cosmos-db-containers-with-a-partition-key"></a>Konfigurowanie kontenerów usługi Azure Cosmos DB przy użyciu klucza partycji

Azure Advisor identyfikuje Azure Cosmos DB kolekcje niepartycjonowane, które zbliżają się do przydzielonego limitu przydziału magazynu. Zaleca się przeprowadzenie migracji tych kolekcji do nowych kolekcji z definicją klucza partycji, dzięki czemu mogą one być automatycznie skalowane przez usługę. [Dowiedz się więcej na temat wybierania klucza partycji.](../cosmos-db/partitioning-overview.md)

## <a name="upgrade-your-azure-cosmos-db-net-sdk-to-the-latest-version-from-nuget"></a>Uaktualnij zestaw SDK Azure Cosmos DB .NET do najnowszej wersji z narzędzia NuGet

Azure Advisor identyfikuje konta Azure Cosmos DB, które używają starych wersji zestawu .NET SDK. Zaleca się przeprowadzenie uaktualnienia do najnowszej wersji z narzędzia NuGet w celu uzyskania najnowszych poprawek, ulepszeń wydajności i możliwości funkcji. [Dowiedz się więcej na temat Azure Cosmos DB .NET SDK.](../cosmos-db/sql-api-sdk-dotnet-standard.md)

## <a name="upgrade-your-azure-cosmos-db-java-sdk-to-the-latest-version-from-maven"></a>Uaktualnianie zestawu Java SDK dla usługi Azure Cosmos DB do najnowszej wersji z programu Maven

Azure Advisor identyfikuje konta Azure Cosmos DB, które używają starych wersji zestawu Java SDK. Zaleca się przeprowadzenie uaktualnienia do najnowszej wersji z Maven w celu uzyskania najnowszych poprawek, ulepszeń wydajności i możliwości funkcji. [Dowiedz się więcej na temat Azure Cosmos DB Java SDK.](../cosmos-db/sql-api-sdk-java-v4.md)

## <a name="upgrade-your-azure-cosmos-db-spark-connector-to-the-latest-version-from-maven"></a>Uaktualnij łącznik Azure Cosmos DB Spark do najnowszej wersji z Maven

Azure Advisor identyfikuje konta Azure Cosmos DB, które używają starych wersji łącznika Azure Cosmos DB Spark. Zaleca się przeprowadzenie uaktualnienia do najnowszej wersji z Maven w celu uzyskania najnowszych poprawek, ulepszeń wydajności i możliwości funkcji. [Dowiedz się więcej na temat łącznika Spark Azure Cosmos DB.](../cosmos-db/spark-connector.md)

## <a name="consider-moving-to-kafka-21-on-hdinsight-40"></a>Rozważ przejście do Kafka 2,1 w usłudze HDInsight 4,0

Od 1 lipca 2020 nie będzie można tworzyć nowych klastrów Kafka przy użyciu Kafka 1,1 w usłudze Azure HDInsight 4,0. Istniejące klastry będą działać w obecnym stanie bez pomocy technicznej firmy Microsoft. Rozważ przeniesienie do Kafka 2,1 w usłudze HDInsight 4,0 do 30 czerwca 2020, aby uniknąć potencjalnych przerw w działaniu systemu lub pomocy technicznej.

## <a name="consider-upgrading-older-spark-versions-in-hdinsight-spark-clusters"></a>Rozważ uaktualnienie starszych wersji platformy Spark w klastrach usługi HDInsight Spark

Od 1 lipca 2020 nie będzie można tworzyć nowych klastrów Spark przy użyciu platformy Spark 2,1 lub 2,2 w usłudze HDInsight 3,6. Nie będzie można tworzyć nowych klastrów Spark przy użyciu platformy Spark 2,3 w usłudze HDInsight 4,0. Istniejące klastry będą działać w obecnym stanie bez pomocy technicznej firmy Microsoft. 

## <a name="enable-virtual-machine-replication"></a>Włącz replikację maszyn wirtualnych
Maszyny wirtualne, które nie mają włączonej replikacji w innym regionie, nie są odporne na awarie regionalne. Replikowanie maszyn wirtualnych zmniejsza niekorzystny wpływ na działalność biznesową w trakcie awarii regionu platformy Azure. Program Advisor wykrywa maszyny wirtualne, na których replikacja nie jest włączona i zaleca włączenie tej funkcji. Po włączeniu replikacji, jeśli wystąpi awaria, można szybko przenieść maszyny wirtualne do zdalnego regionu platformy Azure. [Dowiedz się więcej o replikacji maszyny wirtualnej.](../site-recovery/azure-to-azure-quickstart.md)

## <a name="upgrade-to-the-latest-version-of-the-azure-connected-machine-agent"></a>Uaktualnianie do najnowszej wersji agenta usługi Azure Connected Machine
[Agent maszyny połączonej z platformą Azure](../azure-arc/servers/manage-agent.md) jest regularnie aktualizowany z poprawkami błędów, ulepszeniami stabilności i nowymi funkcjami. Zidentyfikowano zasoby, które nie działają w najnowszej wersji agenta maszynowego. zalecenie doradcy zasugeruje, aby uaktualnić agenta do najnowszej wersji w celu uzyskania najlepszego środowiska usługi Azure Arc.

## <a name="do-not-override-hostname-to-ensure-website-integrity"></a>Nie należy zastępować nazwy hosta w celu zapewnienia integralności witryny internetowej
Doradca zaleca się uniknięcie przesłaniania nazwy hosta podczas konfigurowania Application Gateway. Posiadanie innej domeny na frontonie usługi Application Gateway niż ta, która jest używana do uzyskiwania dostępu do zaplecza, może potencjalnie prowadzić do uszkodzenia plików cookie lub przekierowań adresów URL. Należy pamiętać, że może to nie mieć miejsca we wszystkich sytuacjach i że niektóre kategorie zapleczy (np. interfejsy API REST) są na to mniej wrażliwe. Upewnij się, że zaplecze może to obsłużyć lub zaktualizuj konfigurację usługi Application Gateway, tak aby nazwa hosta nie wymagała zastąpienia pod kątem zaplecza. W przypadku używania z App Service dołączać niestandardową nazwę domeny do aplikacji sieci Web i unikać używania *nazwy hosta. azurewebsites.NET do zaplecza.* [Dowiedz się więcej o domenie niestandardowej](../application-gateway/troubleshoot-app-service-redirection-app-service-url.md).

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Jak uzyskać dostęp do rekomendacji o wysokiej dostępności w usłudze Advisor

1. Zaloguj się do [Azure Portal](https://portal.azure.com), a następnie otwórz program [Advisor](https://aka.ms/azureadvisordashboard).

2.  Na pulpicie nawigacyjnym usługi Advisor wybierz kartę **wysoka dostępność** .

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zaleceń klasyfikatora, zobacz:
* [Wprowadzenie do usługi Advisor](advisor-overview.md)
* [Wprowadzenie do usługi Advisor](advisor-get-started.md)
* [Ocena klasyfikatora](azure-advisor-score.md)
* [Zalecenia usługi Advisor dotyczące kosztów](advisor-cost-recommendations.md)
* [Zalecenia dotyczące wydajności usługi Advisor](advisor-performance-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń usługi Advisor](advisor-security-recommendations.md)
* [Zalecenia dotyczące doskonałości operacyjnej klasyfikatora](advisor-operational-excellence-recommendations.md)