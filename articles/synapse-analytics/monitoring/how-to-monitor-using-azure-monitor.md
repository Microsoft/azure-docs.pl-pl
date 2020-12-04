---
title: Jak monitorować Synapse Analytics przy użyciu Azure Monitor
description: Dowiedz się, jak monitorować obszar roboczy Synapse Analytics przy użyciu Azure Monitor metryk, alertów i dzienników
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: monitoring
ms.date: 11/30/2020
ms.author: mahi
ms.reviewer: mahi
ms.openlocfilehash: 9032fcaf35265c791913f5b69fb0972bada6885f
ms.sourcegitcommit: c4246c2b986c6f53b20b94d4e75ccc49ec768a9a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2020
ms.locfileid: "96602465"
---
# <a name="use-azure-monitor-with-your-azure-synapse-analytics-workspace"></a>Używanie Azure Monitor z obszarem roboczym usługi Azure Synapse Analytics

Aplikacje w chmurze są złożone i mają wiele ruchomych części. Monitory zapewniają dane, aby zapewnić, że aplikacje będą działać w dobrej kondycji. Monitory pomagają również uniknąć potencjalnych problemów i rozwiązywać problemy z poprzednimi. Dane monitorowania umożliwiają uzyskanie szczegółowych informacji o aplikacjach. Ta wiedza ułatwia zwiększenie wydajności aplikacji i łatwość utrzymania. Pomaga również zautomatyzować akcje, które w przeciwnym razie wymagają ręcznej interwencji.

Azure Monitor udostępnia metryki, alerty i dzienniki podstawowej infrastruktury dla większości usług platformy Azure. Dzienniki diagnostyczne platformy Azure są emitowane przez zasób i zapewniają rozbudowane, częste dane dotyczące operacji tego zasobu. Usługa Azure Synapse Analytics umożliwia zapisywanie dzienników diagnostycznych w Azure Monitor.

Aby uzyskać więcej informacji, zobacz temat [Azure Monitor — przegląd](../../azure-monitor/overview.md).

## <a name="metrics"></a>Metryki

Dzięki monitorowi możesz uzyskać wgląd w wydajność i kondycję obciążeń platformy Azure. Najważniejszym typem danych monitorowania jest Metryka, która jest również nazywana licznikiem wydajności. Metryki są emitowane przez większość zasobów platformy Azure. Monitor oferuje kilka sposobów konfigurowania i używania tych metryk do monitorowania i rozwiązywania problemów.

Aby uzyskać dostęp do tych metryk, wykonaj instrukcje podane w temacie [Azure monitor Data Platform](../../azure-monitor/platform/data-platform.md).

### <a name="workspace-level-metrics"></a>Metryki na poziomie obszaru roboczego

Poniżej przedstawiono niektóre metryki emitowane przez obszary robocze:

| **Metryka**                           | **Kategoria metryki, nazwa wyświetlana**                  | **Jednostka** | **Typy agregacji** | **Opis**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| IntegrationActivityRunsEnded         | Integracja, Metryka uruchamiania działań                     | Liczba    | Sum (wartość domyślna), liczba                | Całkowita liczba uruchomień działań, które wystąpiły/zakończyły się w oknie 1-minutowym. </br></br> Użyj wymiaru wynikowego tej metryki, aby przefiltrować według stanu zakończonego powodzeniem, niepowodzeniem lub anulowane.|
| IntegrationPipelineRunsEnded         | Integracja, Metryka przebiegu potoku                     | Liczba    | Sum (wartość domyślna), liczba                | Całkowita liczba uruchomień potoków, które wystąpiły/zakończyły się w oknie 1-minutowym. </br></br> Użyj wymiaru wynikowego tej metryki, aby przefiltrować według stanu zakończonego powodzeniem, niepowodzeniem lub anulowane. |
| IntegrationTriggerRunsEnded          | Integracja, Metryka uruchomienia wyzwalacza                      | Liczba    | Sum (wartość domyślna), liczba                | Całkowita liczba uruchomień wyzwalacza, które wystąpiły/zakończyły się w oknie 1-minutowym. </br></br> Użyj wymiaru wynikowego tej metryki, aby przefiltrować według stanu zakończonego powodzeniem, niepowodzeniem lub anulowane. |
| BuiltinSqlPoolDataProcessedBytes     | Wbudowana Pula SQL, przetworzonych danych (w bajtach) | Byte | Sum (wartość domyślna) | Ilość danych przetworzonych przez wbudowaną bezserwerową pulę SQL. |
| BuiltinSqlPoolLoginAttempts          | Wbudowana Pula SQL, próby logowania | Liczba | Sum (wartość domyślna) | Liczba prób logowania dla wbudowanej puli SQL bezserwerowej. |
| BuiltinSqlPoolDataRequestsEnded      | Wbudowana Pula SQL, żądania zakończone (bajty) | Liczba | Sum (wartość domyślna) | Liczba zakończonych żądań SQL dla wbudowanej puli SQL bezserwerowej. </br></br> Użyj wymiaru wynikowego tej metryki do filtrowania według stanu końcowego. |

### <a name="dedicated-sql-pool-metrics"></a>Metryki dedykowanej puli SQL

Poniżej przedstawiono niektóre metryki emitowane przez dedykowane pule SQL:

| **Metryka**                           | **Nazwa wyświetlana**                  | **Jednostka** | **Typy agregacji** | **Opis**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| DWULimit                            | Limit jednostek dwu                       | Liczba   | Max (wartość domyślna), min., średnia | Skonfigurowany rozmiar puli SQL |
| DWUUsed                             | JEDNOSTEK dwu używane                        | Liczba   | Max (wartość domyślna), min., średnia | Reprezentuje reprezentację wysokiego poziomu użycia w puli SQL. Mierzone przez limit jednostek dwu * jednostek dwu procent |
| DWUUsedPercent                      | JEDNOSTEK dwu procent użycia             | Procent | Max (wartość domyślna), min., średnia | Reprezentuje reprezentację wysokiego poziomu użycia w puli SQL. Mierzone przez przejęcie wartości procentowej procentu procesora CPU i operacji we/wy danych |
| ConnectionsBlockedByFirewall        | Połączenia zablokowane przez zaporę | Liczba   | Sum (wartość domyślna)   | Liczba połączeń blokowanych przez reguły zapory. Odwiedzaj ponownie zasady kontroli dostępu dla puli SQL i monitoruj te połączenia, jeśli liczba jest wysoka |
| AdaptiveCacheHitPercent             | Odsetek trafień adaptacyjnej pamięci podręcznej   | Procent | Max (wartość domyślna), min., średnia | Mierzy, jak dobre obciążenia korzystają z adaptacyjnej pamięci podręcznej. Użyj tej metryki z metryką procentu trafień w pamięci podręcznej, aby określić, czy skalować w celu uzyskania dodatkowej pojemności czy ponownie uruchamiać obciążenia, aby podręczna |
| AdaptiveCacheUsedPercent            | Procent użycia adaptacyjnej pamięci podręcznej  | Procent | Max (wartość domyślna), min., średnia | Mierzy, jak dobre obciążenia korzystają z adaptacyjnej pamięci podręcznej. Użyj tej metryki z metryką procentową wykorzystania pamięci podręcznej, aby określić, czy skalowanie ma być skalowane w celu uzyskania dodatkowej pojemności czy ponownego uruchomienia obciążeń w celu uzyskania |
| LocalTempDBUsedPercent              | Procent użycia lokalnej bazy danych tempdb    | Procent | Max (wartość domyślna), min., średnia | Użycie lokalnej bazy danych tempdb we wszystkich węzłach obliczeniowych — wartości są emitowane co pięć minut. |
| MemoryUsedPercent                   | Procent wykorzystania pamięci          | Procent | Max (wartość domyślna), min., średnia | Użycie pamięci we wszystkich węzłach w puli SQL |
| CPUPercent                          | Procent użycia procesora CPU             | Procent | Max (wartość domyślna), min., średnia | Użycie procesora CPU we wszystkich węzłach w puli SQL |
| Połączenia                         | Połączenia                     | Liczba   | Sum (wartość domyślna)  | Łączna Liczba logowań do puli SQL |
| ActiveQueries                      | Aktywne zapytania                 | Liczba   | Sum (wartość domyślna)   | Aktywne zapytania. Użycie tej metryki nie zostało odfiltrowane i rozdzielenie spowoduje wyświetlenie wszystkich aktywnych zapytań uruchomionych w systemie. |
| QueuedQueries                      | Zakolejkowane zapytania                  | Liczba   | Sum (wartość domyślna)   | Skumulowana liczba żądań umieszczonych w kolejce po osiągnięciu maksymalnego limitu współbieżności |
| WLGActiveQueries                   | Aktywne zapytania grupy obciążenia   | Liczba   | Sum (wartość domyślna)   | Aktywne zapytania w grupie obciążenia. Użycie tej metryki nie zostało odfiltrowane i rozdzielenie spowoduje wyświetlenie wszystkich aktywnych zapytań uruchomionych w systemie. |
| WLGActiveQueriesTimeouts           | Limity czasu zapytania grupy obciążeń   | Liczba   | Sum (wartość domyślna)   | Zapytania dotyczące grupy obciążeń, które przekroczyły limit czasu. Limity czasu zapytania zgłoszone przez tę metrykę są wykonywane dopiero po rozpoczęciu wykonywania zapytania (nie obejmują czasu oczekiwania z powodu blokady lub oczekiwania na zasoby) |
| WLGQueuedQueries                   | Zakolejkowane zapytania grupy obciążenia   | Liczba   | Sum (wartość domyślna)   | Skumulowana liczba żądań umieszczonych w kolejce po osiągnięciu maksymalnego limitu współbieżności |
| WLGAllocationBySystemPercent        | Alokacja grupy obciążeń według wartości procentowej systemu | Procent | Max (wartość domyślna), min., średnia, suma | Procent alokacji zasobów względem całego systemu |
| WLGAllocationByEffectiveCapResourcePercent   | Alokacja grupy obciążeń według maksymalnego procentu zasobów | Procent | Max (wartość domyślna), min., średnia | Wyświetla procentową alokację zasobów względem procentu zasobów obowiązujących dla grupy obciążenia. Ta Metryka zapewnia efektywne wykorzystanie grupy obciążeń |
| WLGEffectiveCapResourcePercent      | Procent zasobów obowiązujących dla limitu  | Procent | Max (wartość domyślna), min., średnia | Procent zasobów obowiązujących dla grupy obciążenia. Jeśli istnieją inne grupy obciążeń z min_percentage_resource > 0, effective_cap_percentage_resource jest obniżane proporcjonalnie |
| WLGEffectiveMinResourcePercent      | Efektywny minimalny procent zasobów  | Procent | Max (wartość domyślna), min., średnia, suma | Ustawienie efektywnej minimalnej wartości procentowej zasobu może uwzględniać ustawienia poziomu usług i grupy obciążeń. Efektywne min_percentage_resource można zwiększyć na niższych poziomach usługi |

### <a name="apache-spark-pool-metrics"></a>Metryki puli Apache Spark

Poniżej przedstawiono niektóre metryki emitowane przez pule Apache Spark:

| **Metryka**                           | **Kategoria metryki, nazwa wyświetlana**                  | **Jednostka** | **Typy agregacji** | **Opis**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| BigDataPoolApplicationsEnded  | Zakończono Apache Spark aplikacje  | Liczba | Sum (wartość domyślna) | Liczba zakończonych aplikacji puli Apache Spark |
| BigDataPoolAllocatedCores     | Liczba rdzeni wirtualnych przypisywanych do puli Apache Spark                 | Liczba | Max (wartość domyślna), min., średnia | Przydzielono rdzeni wirtualnych dla puli Apache Spark |
| BigDataPoolAllocatedMemory    | Ilość pamięci (GB) przydzieloną do puli Apache Spark            | Liczba | Max (wartość domyślna), min., średnia | Przydzieloną pamięć dla puli Apache Spark (GB) |
| BigDataPoolApplicationsActive | Aktywne Apache Spark aplikacje | Liczba | Max (wartość domyślna), min., średnia | Liczba aktywnych aplikacji puli Apache Spark |

## <a name="alerts"></a>Alerty

Zaloguj się do Azure Portal i wybierz pozycję **Monitoruj**  >  **alerty** , aby utworzyć alerty.

### <a name="create-alerts"></a>Tworzenie alertów

1. Wybierz pozycję **+ Nowa reguła alertu** , aby utworzyć nowy Alert.

1. Zdefiniuj **warunek alertu** , aby określić, kiedy alert powinien zostać wywołany.

    > [!NOTE]
    > Upewnij się, że wybrano opcję **wszystkie** na liście rozwijanej **Filtruj według typu zasobu** .

1. Zdefiniuj **szczegóły alertu** , aby bardziej szczegółowo określić sposób skonfigurowania alertu.

1. Zdefiniuj **grupę akcji** , aby określić, kto powinien otrzymywać alerty (i jak).

## <a name="logs"></a>Dzienniki

### <a name="workspace-level-logs"></a>Dzienniki na poziomie obszaru roboczego

Poniżej przedstawiono dzienniki emitowane przez obszary robocze analiz usługi Azure Synapse:

| Nazwa tabeli Log Analytics | Nazwa kategorii dziennika                 | Opis |
|-------------------------------|-------------------------------------------------|-------------|
| SynapseGatewayApiRequests     | GatewayApiRequests             | Żądania interfejsu API bramy usługi Azure Synapse. |
| SynapseRbacOperations         | SynapseRbacOperations          | Operacje kontroli dostępu opartej na rolach (SRBAC) usługi Azure Synapse. |

### <a name="dedicated-sql-pool-logs"></a>Dedykowane dzienniki puli SQL

Poniżej przedstawiono dzienniki emitowane przez dedykowane pule SQL:

| Nazwa tabeli Log Analytics        | Nazwa kategorii dziennika             | Opis |
|----------------------|--------------------------------------|-------------|
| SynapseSqlPoolExecRequests  | ExecRequests | Informacje o żądaniach i zapytaniach SQL w dedykowanej puli SQL platformy Azure Synapse.
| SynapseSqlPoolDmsWorkers    | DmsWorkers   | Informacje o pracownikach kończących procedurę DMS w dedykowanej puli SQL platformy Azure Synapse.
| SynapseSqlPoolRequestSteps  | RequestSteps | Informacje o krokach żądania, które tworzą dane żądanie SQL/zapytanie w dedykowanej puli SQL platformy Azure Synapse.
| SynapseSqlPoolSqlRequests   | Sqlrequests  | Informacje dotyczące dystrybucji zapytań kroków żądań SQL/zapytań w dedykowanej puli SQL usługi Azure Synapse.
| SynapseSqlPoolWaits         | Czeka        | Informacje o stanie oczekiwania napotkane podczas wykonywania żądania SQL/zapytania w dedykowanej puli SQL Synapse platformy Azure, w tym blokad i czekają na kolejki transmisji.

Aby uzyskać więcej informacji na temat tych dzienników, zobacz następujące informacje:
- [sys.dm_pdw_exec_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_request_steps](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-request-steps-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_dms_workers](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-dms-workers-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)
- [sys.dm_pdw_sql_requests](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-sql-requests-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

### <a name="apache-spark-pool-log"></a>Dziennik puli Apache Spark

Oto dziennik emitowany przez pule Apache Spark:

| Nazwa tabeli Log Analytics               | Nazwa kategorii dziennika              | Opis                 |
|-----------------------------|---------------------------------------|-----------------------------|
| SynapseBigDataPoolApplicationsEnded | BigDataPoolAppsEnded | Informacje o zakończonych aplikacjach Apache Spark |

### <a name="diagnostic-settings"></a>Ustawienia diagnostyczne

Użyj ustawień diagnostycznych, aby skonfigurować dzienniki diagnostyczne dla zasobów nieobliczeniowych. Ustawienia kontrolki zasobu mają następujące funkcje:

* Określają miejsce wysyłania dzienników diagnostycznych. Przykłady obejmują konto usługi Azure Storage, centrum zdarzeń platformy Azure lub dzienniki monitora.
* Określają, które kategorie dzienników są wysyłane.
* Określają, jak długo każda kategoria dziennika ma być przechowywana na koncie magazynu.
* Przechowywanie przez zero dni oznacza, że dzienniki są przechowywane w nieskończoność. W przeciwnym razie wartość może być dowolną liczbą dni od 1 do 2 147 483 647.
* Jeśli zasady przechowywania są ustawione, ale przechowywanie dzienników na koncie magazynu jest wyłączone, zasady przechowywania nie mają żadnego efektu. Ten stan może wystąpić na przykład wtedy, gdy wybrano opcję tylko Event Hubs lub monitorowanie dzienników.
* Zasady przechowywania są stosowane dziennie. Granica między dniami występuje o północy czasu uniwersalnego skoordynowanego (UTC). Po upływie dnia dzienniki są usuwane z dni, które wykraczają poza zasady przechowywania. Na przykład jeśli masz zasady przechowywania o jeden dzień, na początku dzisiaj dzienniki przed wczoraj zostaną usunięte.

Ustawienia diagnostyczne Azure Monitor umożliwiają kierowanie dzienników diagnostycznych do analizy wielu różnych elementów docelowych.

* **Konto magazynu**: Zapisz dzienniki diagnostyczne na koncie magazynu na potrzeby inspekcji lub inspekcji ręcznej. Możesz użyć ustawień diagnostycznych, aby określić czas przechowywania w dniach.
* **Centrum zdarzeń**: przesyłanie strumieniowe dzienników do Event Hubs platformy Azure. Dzienniki stają się danymi wejściowymi do usługi partnerskiej/rozwiązania do analizy niestandardowej, takiego jak Power BI.
* **Log Analytics obszar roboczy**: Analizuj dzienniki przy użyciu log Analytics. Integracja z usługą Azure Synapse z Log Analytics jest przydatna w następujących scenariuszach:
  * Chcesz pisać złożone zapytania na rozbudowanym zestawie metryk, które są publikowane przez usługę Azure Synapse do Log Analytics. Możesz tworzyć niestandardowe alerty dla tych zapytań za pośrednictwem Azure Monitor.
  * Chcesz monitorować między obszarami roboczymi. Dane można kierować z wielu obszarów roboczych do jednego obszaru roboczego Log Analytics.

Można również użyć konta magazynu lub przestrzeni nazw centrum zdarzeń, która nie znajduje się w subskrypcji zasobu, który emituje dzienniki. Użytkownik, który konfiguruje ustawienie, musi mieć dostęp do obu subskrypcji przy użyciu odpowiedniej kontroli dostępu opartej na rolach (Azure RBAC).

#### <a name="configure-diagnostic-settings"></a>Konfigurowanie ustawień diagnostycznych

Utwórz lub Dodaj ustawienia diagnostyczne dla obszaru roboczego, dedykowanej puli SQL lub puli Apache Spark.

1. W portalu przejdź do pozycji monitorowanie. Wybierz kolejno pozycje **Ustawienia**  >  **Ustawienia diagnostyczne**.

1. Wybierz obszar roboczy Synapse, dedykowaną pulę SQL lub pulę Apache Spark, dla których chcesz utworzyć ustawienie diagnostyczne.

1. Jeśli w wybranym obszarze roboczym nie ma żadnych ustawień diagnostycznych, zostanie wyświetlony monit o utworzenie ustawienia. Wybierz pozycję **Włącz diagnostykę**.

   W przypadku istniejących ustawień diagnostycznych w obszarze roboczym zostanie wyświetlona lista ustawień, które zostały już skonfigurowane dla zasobu. Wybierz pozycję **Dodaj ustawienia diagnostyczne**.

1. Podaj nazwę ustawienia, wybierz pozycję **Wyślij do log Analytics**, a następnie wybierz obszar roboczy z **log Analytics obszarze roboczym**.

    > [!NOTE]
    > Ponieważ tabela dzienników platformy Azure nie może mieć więcej niż 500 kolumn, **zdecydowanie** zalecamy wybranie _trybu określonego dla zasobu_. Aby uzyskać więcej informacji, zobacz [log Analytics znane ograniczenia](../../azure-monitor/platform/resource-logs.md#column-limit-in-azurediagnostics).

1. Wybierz pozycję **Zapisz**.

Po kilku chwilach nowe ustawienie zostanie wyświetlone na liście ustawień dla obszaru roboczego, dedykowanej puli SQL lub puli Apache Spark. Dzienniki diagnostyczne są przesyłane strumieniowo do tego obszaru roboczego, gdy tylko zostaną wygenerowane nowe dane zdarzeń. Gdy zdarzenie jest emitowane i pojawia się w Log Analytics, może upłynąć do 15 minut.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat monitorowania uruchomień potoków, zobacz temat [monitorowanie przebiegów w artykule Synapse Studio](how-to-monitor-pipeline-runs.md) . 

Aby uzyskać więcej informacji na temat monitorowania Apache Spark aplikacji, zobacz artykuł [monitorowanie Apache Spark aplikacji w programie Synapse Studio](apache-spark-applications.md) .

Aby uzyskać więcej informacji na temat monitorowania żądań SQL, zobacz artykuł [monitorowanie żądań SQL w programie Synapse Studio](how-to-monitor-sql-requests.md) .
