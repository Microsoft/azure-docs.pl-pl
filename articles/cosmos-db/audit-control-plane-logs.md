---
title: Jak przeprowadzić inspekcję Azure Cosmos DB operacji na płaszczyźnie kontroli
description: Dowiedz się, jak przeprowadzać inspekcję operacji płaszczyzny kontroli, takich jak Dodawanie regionów, przepływność aktualizacji, tryb failover w regionie, dodawanie sieci wirtualnej itp. w Azure Cosmos DB
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/05/2020
ms.author: sngun
ms.openlocfilehash: 6f3e408343fc75d6587d1a67a0179edf13d56e36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101658252"
---
# <a name="how-to-audit-azure-cosmos-db-control-plane-operations"></a>Jak przeprowadzić inspekcję Azure Cosmos DB operacji na płaszczyźnie kontroli
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Płaszczyzna kontroli w Azure Cosmos DB to usługa RESTful, która umożliwia wykonywanie różnorodnych operacji na koncie usługi Azure Cosmos. Udostępnia on publiczny model zasobów (na przykład: baza danych, konto) i różne operacje wykonywane przez użytkowników końcowych w celu wykonywania akcji w modelu zasobów. Operacje płaszczyzny kontroli obejmują zmiany w ramach konta lub kontenera usługi Azure Cosmos. Na przykład operacje takie jak tworzenie konta usługi Azure Cosmos, Dodawanie regionu, przepływność aktualizacji, tryb failover w regionie, dodawanie sieci wirtualnej itp. to niektóre operacje płaszczyzny kontroli. W tym artykule wyjaśniono, jak przeprowadzić inspekcję operacji płaszczyzny kontroli w Azure Cosmos DB. Operacje płaszczyzny kontroli można uruchomić na kontach usługi Azure Cosmos przy użyciu interfejsu wiersza polecenia platformy Azure, programu PowerShell lub Azure Portal, a w przypadku kontenerów Użyj interfejsu wiersza polecenia platformy Azure lub programu PowerShell.

Poniżej przedstawiono kilka przykładowych scenariuszy, w których Inspekcja operacji na płaszczyźnie kontroli jest przydatna:

* Chcesz otrzymywać alert, gdy reguły zapory dla konta usługi Azure Cosmos są modyfikowane. Alert jest wymagany, aby znaleźć nieautoryzowane modyfikacje reguł, które regulują bezpieczeństwo sieci Twojego konta usługi Azure Cosmos i podejmują szybką akcję.

* Chcesz otrzymywać alert, jeśli nowy region zostanie dodany lub usunięty z konta usługi Azure Cosmos. Dodanie lub usunięcie regionów ma wpływ na wymagania dotyczące rozliczeń i niepodległości danych. Ten alert pomoże Ci wykryć przypadkowe dodanie lub usunięcie regionu na koncie.

* Chcesz uzyskać więcej szczegółów z dzienników diagnostycznych dotyczących zmian. Na przykład Sieć wirtualna została zmieniona.

## <a name="disable-key-based-metadata-write-access"></a>Wyłącz dostęp do zapisu metadanych opartych na kluczach

Przed inspekcją operacji płaszczyzny kontroli w Azure Cosmos DB należy wyłączyć dostęp do zapisu metadanych opartych na kluczach na Twoim koncie. Gdy dostęp do zapisu metadanych opartych na kluczach jest wyłączony, klienci łączący się z kontem usługi Azure Cosmos za pomocą kluczy kont nie mogą uzyskać dostępu do konta. Aby wyłączyć dostęp do zapisu, można ustawić `disableKeyBasedMetadataWriteAccess` Właściwość na wartość true. Po ustawieniu tej właściwości zmiany w dowolnym zasobie mogą wystąpić od użytkownika mającego odpowiednią rolę i poświadczenia platformy Azure. Aby dowiedzieć się więcej na temat sposobu ustawiania tej właściwości, zobacz artykuł [Zapobiegaj zmianom z zestawów SDK](role-based-access-control.md#prevent-sdk-changes) . 

`disableKeyBasedMetadataWriteAccess`Gdy program jest włączony, jeśli klient oparty na zestawie SDK uruchomi operacje tworzenia lub aktualizowania, zwracany jest błąd *"operacja post" w zasobie "ContainerNameorDatabaseName" jest niedozwolona za pośrednictwem Azure Cosmos DB punktu końcowego* . Musisz włączyć dostęp do takich operacji dla Twojego konta lub wykonać operacje tworzenia/aktualizacji za pomocą Azure Resource Manager, interfejsu wiersza polecenia platformy Azure lub Azure PowerShell. Aby przełączyć z powrotem, ustaw disableKeyBasedMetadataWriteAccess na **false** przy użyciu interfejsu wiersza polecenia platformy Azure, zgodnie z opisem w artykule [zapobieganie zmianom z zestawu SDK Cosmos](role-based-access-control.md#prevent-sdk-changes) . Pamiętaj, aby zmienić wartość `disableKeyBasedMetadataWriteAccess` na false zamiast true.

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

:::image type="content" source="./media/audit-control-plane-logs/enable-control-plane-requests-logs.png" alt-text="Włącz rejestrowanie żądań płaszczyzny kontroli":::

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

:::image type="content" source="./media/audit-control-plane-logs/add-ip-filter-logs.png" alt-text="Po dodaniu sieci wirtualnej są rejestrowane płaszczyzny kontroli":::

Poniższe zrzuty ekranu przechwytują dzienniki, gdy jest tworzona przestrzeń kluczy lub tabela konta Cassandra oraz gdy przepływność zostanie zaktualizowana. Płaszczyzna kontroli rejestruje operacje tworzenia i aktualizowania bazy danych, a kontener jest rejestrowany oddzielnie, jak pokazano na poniższym zrzucie ekranu:

:::image type="content" source="./media/audit-control-plane-logs/throughput-update-logs.png" alt-text="Gdy przepływność jest aktualizowana, dzienniki płaszczyzny kontroli":::

## <a name="identify-the-identity-associated-to-a-specific-operation"></a>Zidentyfikuj tożsamość skojarzoną z określoną operacją

Aby przeprowadzić dalsze debugowanie, można zidentyfikować konkretną operację w **dzienniku aktywności** przy użyciu identyfikatora działania lub sygnatury czasowej operacji. Sygnatura czasowa jest używana dla niektórych klientów Menedżer zasobów, w których identyfikator działania nie został jawnie zakończony. Dziennik aktywności zawiera szczegółowe informacje o tożsamości, z którą została zainicjowana operacja. Poniższy zrzut ekranu przedstawia sposób używania identyfikatora działania i wyszukiwania skojarzonych z nim operacji w dzienniku aktywności:

:::image type="content" source="./media/audit-control-plane-logs/find-operations-with-activity-id.png" alt-text="Użyj identyfikatora działania i Znajdź operacje":::

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

* Utworzono SQL Database
* SQL Database zaktualizowane
* SQL Database zaktualizowana przepływność
* SQL Database usunięte
* Utworzono kontener SQL
* Zaktualizowano kontener SQL
* Zaktualizowano przepływność kontenera SQL
* Usunięto kontener SQL
* Utworzono przestrzeń kluczy Cassandra
* Cassandra miejsce na dysku
* Cassandra przepustowość obszaru kluczy
* Cassandra usunięto przestrzeń kluczy
* Utworzono tabelę Cassandra
* Zaktualizowano tabelę Cassandra
* Zaktualizowano przepływność tabeli Cassandra
* Usunięto tabelę Cassandra
* Utworzono bazę danych Gremlin
* Baza danych Gremlin została zaktualizowana
* Zaktualizowano przepływność bazy danych Gremlin
* Baza danych Gremlin została usunięta
* Utworzono Graf Gremlin
* Zaktualizowano Graf Gremlin
* Zaktualizowano przepływność grafu Gremlin
* Wykres Gremlin został usunięty
* Utworzono bazę danych Mongo
* Baza danych Mongo została zaktualizowana
* Zaktualizowano przepływność bazy danych Mongo
* Baza danych Mongo została usunięta
* Utworzono kolekcję Mongo
* Aktualizacja kolekcji Mongo
* Zaktualizowano przepływność kolekcji Mongo
* Kolekcja Mongo została usunięta
* Utworzono tabelę Azure
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

* Rodzaju interfejsu API + ApiKindResourceType + OperationType
* Rodzaju interfejsu API + ApiKindResourceType + "przepływność" + OperationType

**Przykład** 

* CassandraKeyspacesCreate
* CassandraKeyspacesUpdate
* CassandraKeyspacesThroughputUpdate
* SqlContainersUpdate

Właściwość *ResourceDetails* zawiera całą treść zasobu jako ładunek żądania i zawiera wszystkie właściwości żądane do aktualizacji

## <a name="diagnostic-log-queries-for-control-plane-operations"></a>Zapytania dziennika diagnostycznego dotyczące operacji płaszczyzny kontroli

Poniżej przedstawiono kilka przykładów pobierania dzienników diagnostycznych dla operacji płaszczyzny kontroli:

```kusto
AzureDiagnostics 
| where Category startswith "ControlPlane"
| where OperationName contains "Update"
| project httpstatusCode_s, statusCode_s, OperationName, resourceDetails_s, activityId_g
```

```kusto
AzureDiagnostics 
| where Category =="ControlPlaneRequests"
| where TimeGenerated >= todatetime('2020-05-14T17:37:09.563Z')
| project TimeGenerated, OperationName, apiKind_s, apiKindResourceType_s, operationType_s, resourceDetails_s
```

```kusto
AzureDiagnostics
| where Category == "ControlPlaneRequests"
| where OperationName startswith "SqlContainersUpdate"
```

```kusto
AzureDiagnostics
| where Category == "ControlPlaneRequests"
| where OperationName startswith "SqlContainersThroughputUpdate"
```

Zapytanie w celu pobrania activityId i obiektu wywołującego, który zainicjował operację usuwania kontenera:

```kusto
(AzureDiagnostics
| where Category == "ControlPlaneRequests"
| where OperationName == "SqlContainersDelete"
| where TimeGenerated >= todatetime('9/3/2020, 5:30:29.300 PM')
| summarize by activityId_g )
| join (
AzureActivity
| parse HTTPRequest with * "clientRequestId\": \"" activityId_g "\"" * 
| summarize by Caller, HTTPRequest, activityId_g)
on activityId_g
| project Caller, activityId_g
```

Zapytanie w celu pobrania aktualizacji indeksu lub czasu wygaśnięcia (TTL). Następnie można porównać dane wyjściowe tego zapytania z wcześniejszą aktualizacją, aby zobaczyć zmianę w indeksie lub czas wygaśnięcia.

```Kusto
AzureDiagnostics
| where Category =="ControlPlaneRequests"
| where  OperationName == "SqlContainersUpdate"
| project resourceDetails_s
```

**rozdzielczości**

```json
{id:skewed,indexingPolicy:{automatic:true,indexingMode:consistent,includedPaths:[{path:/*,indexes:[]}],excludedPaths:[{path:/_etag/?}],compositeIndexes:[],spatialIndexes:[]},partitionKey:{paths:[/pk],kind:Hash},defaultTtl:1000000,uniqueKeyPolicy:{uniqueKeys:[]},conflictResolutionPolicy:{mode:LastWriterWins,conflictResolutionPath:/_ts,conflictResolutionProcedure:}
```

## <a name="next-steps"></a>Następne kroki

* [Eksploruj Azure Monitor dla Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)
* [Monitoruj i Debuguj przy użyciu metryk w Azure Cosmos DB](use-metrics.md)
