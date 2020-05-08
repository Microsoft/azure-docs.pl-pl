---
title: Jak przeprowadzić inspekcję Azure Cosmos DB operacji na płaszczyźnie kontroli
description: Dowiedz się, jak przeprowadzać inspekcję operacji płaszczyzny kontroli, takich jak Dodawanie regionów, przepływność aktualizacji, tryb failover w regionie, dodawanie sieci wirtualnej itp. w Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/23/2020
ms.author: sngun
ms.openlocfilehash: a5df7866f7897109dbd7a0ea8a52b857ab671875
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735355"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Jak przeprowadzić inspekcję Azure Cosmos DB operacji na płaszczyźnie kontroli

Płaszczyzna kontroli w Azure Cosmos DB to usługa RESTful, która umożliwia wykonywanie różnorodnych operacji na koncie usługi Azure Cosmos. Udostępnia on publiczny model zasobów (na przykład: baza danych, konto) i różne operacje wykonywane przez użytkowników końcowych w celu wykonywania akcji w modelu zasobów. Operacje płaszczyzny kontroli obejmują zmiany w ramach konta lub kontenera usługi Azure Cosmos. Na przykład operacje takie jak tworzenie konta usługi Azure Cosmos, Dodawanie regionu, przepływność aktualizacji, tryb failover w regionie, dodawanie sieci wirtualnej itp. to niektóre operacje płaszczyzny kontroli. W tym artykule wyjaśniono, jak przeprowadzić inspekcję operacji płaszczyzny kontroli w Azure Cosmos DB. Operacje płaszczyzny kontroli można uruchomić na kontach usługi Azure Cosmos przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell lub Azure Portal, a w przypadku kontenerów Użyj interfejsu wiersza polecenia platformy Azure lub programu PowerShell.

Poniżej przedstawiono kilka przykładowych scenariuszy, w których Inspekcja operacji na płaszczyźnie kontroli jest przydatna:

* Chcesz otrzymywać alert, gdy reguły zapory dla konta usługi Azure Cosmos są modyfikowane. Alert jest wymagany, aby znaleźć nieautoryzowane modyfikacje reguł, które regulują bezpieczeństwo sieci Twojego konta usługi Azure Cosmos i podejmują szybką akcję.

* Chcesz otrzymywać alert, jeśli nowy region zostanie dodany lub usunięty z konta usługi Azure Cosmos. Dodanie lub usunięcie regionów ma wpływ na wymagania dotyczące rozliczeń i niepodległości danych. Ten alert pomoże Ci wykryć przypadkowe dodanie lub usunięcie regionu na koncie.

* Chcesz uzyskać więcej szczegółów z dzienników diagnostycznych dotyczących zmian. Na przykład Sieć wirtualna została zmieniona.

## <a name="disable-key-based-metadata-write-access"></a>Wyłącz dostęp do zapisu metadanych opartych na kluczach

Przed inspekcją operacji płaszczyzny kontroli w Azure Cosmos DB należy wyłączyć dostęp do zapisu metadanych opartych na kluczach na Twoim koncie. Gdy dostęp do zapisu metadanych opartych na kluczach jest wyłączony, klienci łączący się z kontem usługi Azure Cosmos za pomocą kluczy kont nie mogą uzyskać dostępu do konta. Aby wyłączyć dostęp do zapisu, można ustawić `disableKeyBasedMetadataWriteAccess` właściwość na wartość true. Po ustawieniu tej właściwości zmiany w dowolnym zasobie mogą wystąpić od użytkownika z poprawnym rolą kontroli dostępu opartej na rolach (RBAC). Aby dowiedzieć się więcej na temat sposobu ustawiania tej właściwości, zobacz artykuł [Zapobiegaj zmianom z zestawów SDK](role-based-access-control.md#preventing-changes-from-cosmos-sdk) . 

Gdy program `disableKeyBasedMetadataWriteAccess` jest włączony, jeśli klient oparty na zestawie SDK uruchomi operacje tworzenia lub aktualizowania, zwracany jest błąd *"operacja post" w zasobie "ContainerNameorDatabaseName" jest niedozwolona za pośrednictwem Azure Cosmos DB punktu końcowego* . Musisz włączyć dostęp do takich operacji dla Twojego konta lub wykonać operacje tworzenia/aktualizacji za pomocą Azure Resource Manager, interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell. Aby przełączyć z powrotem, ustaw disableKeyBasedMetadataWriteAccess na **false** przy użyciu interfejsu wiersza polecenia platformy Azure, zgodnie z opisem w artykule [zapobieganie zmianom z zestawu SDK Cosmos](role-based-access-control.md#preventing-changes-from-cosmos-sdk) . Pamiętaj, aby zmienić wartość `disableKeyBasedMetadataWriteAccess` na false zamiast true.

Podczas wyłączania dostępu do zapisu metadanych należy wziąć pod uwagę następujące kwestie:

* Należy oszacować i upewnić się, że aplikacje nie tworzą wywołań metadanych, które zmieniają powyższe zasoby (na przykład tworzenie kolekcji, aktualizowanie przepływności,...) przy użyciu zestawu SDK lub kluczy konta.

* Obecnie Azure Portal używa kluczy konta do operacji metadanych, a tym samym operacje te zostaną zablokowane. Alternatywnie można wykonać takie operacje przy użyciu interfejsu wiersza polecenia platformy Azure, zestawów SDK lub wdrożeń szablonów Menedżer zasobów.

## <a name="enable-diagnostic-logs-for-control-plane-operations"></a>Włączanie dzienników diagnostycznych dla operacji płaszczyzny kontroli

Dzienniki diagnostyczne można włączyć dla operacji płaszczyzny kontroli przy użyciu Azure Portal. Po włączeniu dzienniki diagnostyczne będą rejestrować operację jako parę zdarzeń Start i Complete z odpowiednimi szczegółami. Na przykład *RegionFailoverStart* i *RegionFailoverComplete* będą kończyć zdarzenie trybu failover regionu.

Wykonaj następujące kroki, aby włączyć rejestrowanie na działania płaszczyzny kontroli:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i przejdź do swojego konta usługi Azure Cosmos.

1. Otwórz okienko **ustawień diagnostycznych** i podaj **nazwę** dzienników do utworzenia.

1. W polu Typ dziennika wybierz pozycję **ControlPlaneRequests** , a następnie wybierz opcję **Wyślij do log Analytics** .

Możesz również przechowywać dzienniki na koncie magazynu lub w strumieniu do centrum zdarzeń. W tym artykule pokazano, jak wysyłać dzienniki do usługi log Analytics, a następnie wykonywać do nich zapytania. Po jego włączeniu trwa kilka minut, zanim dzienniki diagnostyczne zaczną obowiązywać. Wszystkie operacje płaszczyzny kontroli wykonywane po tym punkcie mogą być śledzone. Poniższy zrzut ekranu pokazuje, jak włączyć dzienniki płaszczyzny kontroli:

![Włącz rejestrowanie żądań płaszczyzny kontroli](./media/audit-control-plane-logs/enable-control-plane-requests-logs.png)

## <a name="view-the-control-plane-operations"></a>Wyświetlanie operacji płaszczyzny kontroli

Po włączeniu rejestrowania wykonaj następujące kroki, aby śledzić operacje dla określonego konta:

1. Zaloguj się do [Azure Portal](https://portal.azure.com).

1. Otwórz kartę **monitorowanie** w obszarze nawigacji po lewej stronie, a następnie wybierz okienko **dzienniki** . Otwiera interfejs użytkownika, w którym można łatwo uruchamiać zapytania o tym konkretnym koncie w zakresie. Uruchom następujące zapytanie, aby wyświetlić dzienniki płaszczyzny kontroli:

   ```kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="ControlPlaneRequests"
   | where TimeGenerated >= ago(1h)
   ```

Poniższe zrzuty ekranu przechwytują dzienniki po zmianie poziomu spójności dla konta usługi Azure Cosmos:

![Po dodaniu sieci wirtualnej są rejestrowane płaszczyzny kontroli](./media/audit-control-plane-logs/add-ip-filter-logs.png)

Następujące zrzuty ekranu przechwytują dzienniki podczas aktualizowania przepływności tabeli Cassandra:

![Gdy przepływność jest aktualizowana, dzienniki płaszczyzny kontroli](./media/audit-control-plane-logs/throughput-update-logs.png)

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>Zidentyfikuj tożsamość skojarzoną z określoną operacją

Aby przeprowadzić dalsze debugowanie, można zidentyfikować konkretną operację w **dzienniku aktywności** przy użyciu identyfikatora działania lub sygnatury czasowej operacji. Sygnatura czasowa jest używana dla niektórych klientów Menedżer zasobów, w których identyfikator działania nie został jawnie zakończony. Dziennik aktywności zawiera szczegółowe informacje o tożsamości, z którą została zainicjowana operacja. Poniższy zrzut ekranu przedstawia sposób używania identyfikatora działania i wyszukiwania skojarzonych z nim operacji w dzienniku aktywności:

![Użyj identyfikatora działania i Znajdź operacje](./media/audit-control-plane-logs/find-operations-with-activity-id.png)

## <a name="control-plane-operations-for-azure-cosmos-account"></a>Operacje płaszczyzny kontroli dla konta usługi Azure Cosmos

Poniżej przedstawiono operacje płaszczyzny kontroli dostępne na poziomie konta. Większość operacji jest śledzonych na poziomie konta. Te operacje są dostępne jako metryki w usłudze Azure Monitor:

* Dodano region
* Usunięto region
* Konto zostało usunięte
* Region w trybie failover
* Utworzono konto
* Sieć wirtualna została usunięta
* Zaktualizowano ustawienia sieci konta
* Zaktualizowano ustawienia replikacji konta
* Zaktualizowano klucze konta
* Zaktualizowano ustawienia kopii zapasowej konta
* Zaktualizowano ustawienia diagnostyczne konta

## <a name="control-plane-operations-for-database-or-containers"></a>Operacje płaszczyzny kontroli dla bazy danych lub kontenerów

Poniżej przedstawiono operacje płaszczyzny kontroli dostępne na poziomie bazy danych i kontenera. Te operacje są dostępne jako metryki w usłudze Azure Monitor:

* SQL Database zaktualizowane
* Zaktualizowano kontener SQL
* SQL Database zaktualizowana przepływność
* Zaktualizowano przepływność kontenera SQL
* SQL Database usunięte
* Usunięto kontener SQL
* Cassandra miejsce na dysku
* Zaktualizowano tabelę Cassandra
* Cassandra przepustowość obszaru kluczy
* Zaktualizowano przepływność tabeli Cassandra
* Cassandra usunięto przestrzeń kluczy
* Usunięto tabelę Cassandra
* Baza danych Gremlin została zaktualizowana
* Zaktualizowano Graf Gremlin
* Zaktualizowano przepływność bazy danych Gremlin
* Zaktualizowano przepływność grafu Gremlin
* Baza danych Gremlin została usunięta
* Wykres Gremlin został usunięty
* Baza danych Mongo została zaktualizowana
* Aktualizacja kolekcji Mongo
* Zaktualizowano przepływność bazy danych Mongo
* Zaktualizowano przepływność kolekcji Mongo
* Baza danych Mongo została usunięta
* Kolekcja Mongo została usunięta
* Zaktualizowano tabelę platformy Azure
* Zaktualizowano przepływność tabeli usługi Azure Table
* Usunięto tabelę platformy Azure

## <a name="diagnostic-log-operations"></a>Operacje dzienników diagnostycznych

Poniżej przedstawiono nazwy operacji w dziennikach diagnostycznych dla różnych operacji:

* RegionAddStart, RegionAddComplete
* RegionRemoveStart, RegionRemoveComplete
* AccountDeleteStart, AccountDeleteComplete
* RegionFailoverStart, RegionFailoverComplete
* AccountCreateStart, AccountCreateComplete
* AccountUpdateStart, AccountUpdateComplete
* VirtualNetworkDeleteStart, VirtualNetworkDeleteComplete
* DiagnosticLogUpdateStart, DiagnosticLogUpdateComplete

Dla operacji specyficznych dla interfejsu API operacja jest nazywana następującym formatem:

* Rodzaju interfejsu API + ApiKindResourceType + OperationType + Start/Complete
* Rodzaju interfejsu API + ApiKindResourceType + "przepływność" + OperationType + Start/Complete

**Przyklad** 

* CassandraKeyspacesUpdateStart, CassandraKeyspacesUpdateComplete
* CassandraKeyspacesThroughputUpdateStart, CassandraKeyspacesThroughputUpdateComplete
* SqlContainersUpdateStart, SqlContainersUpdateComplete

Właściwość *ResourceDetails* zawiera całą treść zasobu jako ładunek żądania i zawiera wszystkie właściwości żądane do aktualizacji

## <a name="diagnostic-log-queries-for-control-plane-operations"></a>Zapytania dziennika diagnostycznego dotyczące operacji płaszczyzny kontroli

Poniżej przedstawiono kilka przykładów pobierania dzienników diagnostycznych dla operacji płaszczyzny kontroli:

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where  OperationName startswith "SqlContainersUpdateStart"
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where  OperationName startswith "SqlContainersThroughputUpdateStart"
```

## <a name="next-steps"></a>Następne kroki

* [Eksploruj Azure Monitor dla Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [Monitoruj i Debuguj przy użyciu metryk w Azure Cosmos DB](use-metrics.md)
