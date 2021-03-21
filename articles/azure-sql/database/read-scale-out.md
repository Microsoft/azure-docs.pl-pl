---
title: Odczytaj zapytania w replikach
description: Usługa Azure SQL umożliwia korzystanie z pojemności replik tylko do odczytu dla obciążeń odczytu, nazywanych skalowaniem do odczytu.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: sstein
ms.date: 01/20/2021
ms.openlocfilehash: 5f9e7e1c96db2b60e41fe0ded69ea562cf8fcea6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98663989"
---
# <a name="use-read-only-replicas-to-offload-read-only-query-workloads"></a>Korzystanie z replik tylko do odczytu w celu odciążenia obciążeń zapytań tylko do odczytu
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

W ramach [architektury wysokiej dostępności](high-availability-sla.md#premium-and-business-critical-service-tier-locally-redundant-availability)każda pojedyncza baza danych, baza danych elastycznej puli i wystąpienie zarządzane w warstwie Premium i krytyczne dla działania firmy są automatycznie inicjowane przy użyciu podstawowej repliki odczytu i zapisu oraz kilku replik pomocniczych tylko do odczytu. Pomocnicze repliki są obsługiwane z tym samym rozmiarem obliczeniowym co replika podstawowa. Funkcja *Odczyt skalowalny* w poziomie umożliwia odciążanie obciążeń tylko do odczytu przy użyciu pojemności obliczeniowej jednej z replik tylko do odczytu, zamiast uruchamiania ich w replice do odczytu i zapisu. W ten sposób niektóre obciążenia tylko do odczytu mogą być odizolowane od obciążeń odczytu i zapisu i nie wpłyną na ich wydajność. Ta funkcja jest przeznaczona dla aplikacji, które zawierają logicznie oddzielone, tylko do odczytu obciążenia, takie jak analiza. W warstwach usług premium i Krytyczne dla działania firmy aplikacje mogą uzyskać korzyści z wydajności przy użyciu tej dodatkowej pojemności bez dodatkowych kosztów.

Funkcja *Odczytaj skalowalny* w poziomie jest również dostępna w warstwie usługi w ramach skalowania po utworzeniu co najmniej jednej repliki pomocniczej. Wiele replik pomocniczych może służyć do równoważenia obciążenia obciążeń przeznaczonych tylko do odczytu, które wymagają więcej zasobów niż dostępne w jednej replice pomocniczej.

Architektura usługi Basic, standard i Ogólnego przeznaczenia o wysokiej dostępności nie obejmuje żadnych replik. Funkcja *odczytu skalowalnego* w poziomie nie jest dostępna w tych warstwach usług.

Na poniższym diagramie przedstawiono tę funkcję.

![Repliki tylko do odczytu](./media/read-scale-out/business-critical-service-tier-read-scale-out.png)

Funkcja *Odczyt skalowalny* w poziomie jest domyślnie włączona w nowych bazach danych Premium, krytyczne dla działania firmy i w skali. W przypadku tworzenia i skalowania jedna replika pomocnicza jest tworzona domyślnie dla nowych baz danych. 

> [!NOTE]
> Skalowanie w poziomie do odczytu jest zawsze włączone w warstwie usług Krytyczne dla działania firmy wystąpienia zarządzanego.

Jeśli parametry połączenia SQL są skonfigurowane z programem `ApplicationIntent=ReadOnly` , aplikacja zostanie przekierowana do repliki tylko do odczytu tej bazy danych lub wystąpienia zarządzanego. Aby uzyskać informacje na temat sposobu użycia `ApplicationIntent` właściwości, zobacz [Określanie zamiaru aplikacji](/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#specifying-application-intent).

Jeśli chcesz upewnić się, że aplikacja nawiązuje połączenie z repliką podstawową, niezależnie od `ApplicationIntent` Ustawienia w parametrach połączenia SQL, należy jawnie wyłączyć skalowanie w poziomie podczas tworzenia bazy danych lub zmiany konfiguracji. Jeśli na przykład baza danych jest uaktualniana z warstwy Standardowa lub Ogólnego przeznaczenia do warstwy Premium, Krytyczne dla działania firmy lub do skalowania i chcesz upewnić się, że wszystkie połączenia nadal przechodzą do repliki podstawowej, wyłącz opcję Odczytaj w poziomie. Aby uzyskać szczegółowe informacje na temat sposobu jego wyłączania, zobacz [Włączanie i wyłączanie skalowania](#enable-and-disable-read-scale-out)w poziomie.

> [!NOTE]
> Funkcje magazynu zapytań i programu SQL Profiler nie są obsługiwane w przypadku replik tylko do odczytu. 

## <a name="data-consistency"></a>Spójność danych

Jedną z korzyści wynikających z replik jest to, że repliki są zawsze w stanie spójnym w transakcjach, ale w różnych momentach mogą występować niewielkie opóźnienia między różnymi replikami. Skalowanie w poziomie nie obsługuje spójności poziomu sesji. Oznacza to, że jeśli sesja tylko do odczytu zostanie nawiązane ponownie po błędzie połączenia spowodowanym przez niedostępną replikę, może zostać przekierowana do repliki, która nie jest zgodna z 100% z repliką odczytu i zapisu. Podobnie jeśli aplikacja zapisuje dane przy użyciu sesji odczytu i zapisu, a natychmiast odczytuje je przy użyciu sesji tylko do odczytu, możliwe jest, że najnowsze aktualizacje nie są natychmiast widoczne w replice. Opóźnienie jest spowodowane przez asynchroniczną operację ponownego wykonywania w dzienniku transakcji.

> [!NOTE]
> Opóźnienia replikacji w regionie są niskie, a ta sytuacja jest rzadki. Aby monitorować opóźnienie replikacji, zobacz [monitorowanie i rozwiązywanie problemów z repliką tylko do odczytu](#monitoring-and-troubleshooting-read-only-replicas).

## <a name="connect-to-a-read-only-replica"></a>Nawiązywanie połączenia z repliką tylko do odczytu

Po włączeniu opcji Odczyt skalowalny w poziomie dla bazy danych `ApplicationIntent` opcja w parametrach połączenia dostarczonych przez klienta wskazuje, czy połączenie jest kierowane do repliki zapisu, czy do repliki tylko do odczytu. Jeśli `ApplicationIntent` wartość to `ReadWrite` (wartość domyślna), połączenie zostanie skierowane do repliki do odczytu i zapisu. Ta funkcja jest taka sama jak w przypadku, gdy `ApplicationIntent` nie jest uwzględniona w parametrach połączenia. Jeśli `ApplicationIntent` wartość jest `ReadOnly` , połączenie jest kierowane do repliki tylko do odczytu.

Na przykład następujące parametry połączenia łączą klienta z repliką tylko do odczytu (zastępując elementy w nawiasach kątowych prawidłowymi wartościami dla środowiska i upuszczając nawiasy kątowe):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

Jeden z następujących parametrów połączenia łączy klienta z repliką odczytu i zapisu (zastępując elementy w nawiasach kątowych prawidłowymi wartościami dla danego środowiska i upuszczając nawiasy kątowe):

```sql
Server=tcp:<server>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadWrite;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;

Server=tcp:<server>.database.windows.net;Database=<mydatabase>;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="verify-that-a-connection-is-to-a-read-only-replica"></a>Weryfikowanie połączenia z repliką tylko do odczytu

Można sprawdzić, czy nawiązano połączenie z repliką tylko do odczytu, uruchamiając następujące zapytanie w kontekście bazy danych. Będzie on zwracać READ_ONLY, gdy nastąpi połączenie z repliką tylko do odczytu.

```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Updateability');
```

> [!NOTE]
> W warstwach usług premium i Krytyczne dla działania firmy w danym momencie dostępna jest tylko jedna z replik tylko do odczytu. Funkcja skalowania obsługuje wiele replik tylko do odczytu.

## <a name="monitoring-and-troubleshooting-read-only-replicas"></a>Monitorowanie i rozwiązywanie problemów z replikami tylko do odczytu

W przypadku połączenia z repliką tylko do odczytu dynamiczne widoki zarządzania (widoków DMV) odzwierciedlają stan repliki i mogą być wysyłane do celów monitorowania i rozwiązywania problemów. Aparat bazy danych udostępnia wiele widoków, które uwidaczniają szeroką gamę danych monitorowania. 

Najczęściej używane widoki to:

| Nazwa | Przeznaczenie |
|:---|:---|
|[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Zawiera metryki wykorzystania zasobów w ciągu ostatniej godziny, w tym użycie procesora CPU, danych we/wy i zapisu w dzienniku względem ograniczeń celu usługi.|
|[sys.dm_os_wait_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-wait-stats-transact-sql)| Zawiera zagregowane statystyki oczekiwania dla wystąpienia aparatu bazy danych. |
|[sys.dm_database_replica_states](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database)| Zawiera informacje o stanie kondycji repliki i statystyce synchronizacji. Rozmiar kolejki ponownego wykonywania i częstotliwość ponownego wykonywania służą jako wskaźniki opóźnienia danych w replice tylko do odczytu. |
|[sys.dm_os_performance_counters](/sql/relational-databases/system-dynamic-management-views/sys-dm-os-performance-counters-transact-sql)| Zapewnia liczniki wydajności aparatu bazy danych.|
|[sys.dm_exec_query_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-stats-transact-sql)| Zapewnia dane statystyczne wykonywania poszczególnych zapytań, takie jak Liczba wykonań, użyty czas procesora CPU itd.|
|[sys.dm_exec_query_plan ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-transact-sql)| Udostępnia buforowane plany zapytań. |
|[sys.dm_exec_sql_text ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-sql-text-transact-sql)| Zawiera tekst zapytania dla buforowanego planu zapytania.|
|[sys.dm_exec_query_profiles](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| Zapewnia postęp zapytania w czasie rzeczywistym w czasie wykonywania zapytań.|
|[sys.dm_exec_query_plan_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-exec-query-plan-stats-transact-sql)| Zawiera informacje o ostatnim znanym rzeczywistym planie wykonywania, w tym statystyki środowiska uruchomieniowego dla zapytania.|
|[sys.dm_io_virtual_file_stats ()](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql)| Zapewnia liczbę operacji we/wy na sekundę, przepływność i statystykę opóźnienia dla wszystkich plików bazy danych. |

> [!NOTE]
> `sys.resource_stats`I `sys.elastic_pool_resource_stats` widoków DMV w logicznej głównej bazie danych programu zwracają dane użycia zasobów repliki podstawowej.

### <a name="monitoring-read-only-replicas-with-extended-events"></a>Monitorowanie replik tylko do odczytu z rozszerzonymi zdarzeniami

Nie można utworzyć sesji zdarzeń rozszerzonych, gdy jest połączony z repliką tylko do odczytu. Jednakże w Azure SQL Database definicje sesji [zdarzeń rozszerzonych](xevent-db-diff-from-svr.md) w zakresie bazy danych utworzonych i zmienionych w replice podstawowej są replikowane do replik tylko do odczytu, w tym replik geograficznych, i przechwytywania zdarzeń w replikach tylko do odczytu.

Rozszerzona sesja zdarzeń w replice tylko do odczytu, która jest oparta na definicji sesji z repliki podstawowej, może zostać uruchomiona i zatrzymana niezależnie od repliki podstawowej. Gdy sesja zdarzeń rozszerzonych zostanie usunięta z repliki podstawowej, zostanie ona również porzucona dla wszystkich replik tylko do odczytu.

### <a name="transaction-isolation-level-on-read-only-replicas"></a>Poziom izolacji transakcji w replikach tylko do odczytu

Zapytania uruchamiane w replikach tylko do odczytu są zawsze mapowane na poziom izolacji transakcji [migawek](/dotnet/framework/data/adonet/sql/snapshot-isolation-in-sql-server) . Izolacja migawek używa wersji wierszy, aby uniknąć blokowania scenariuszy, w których czytelnicy blokują autorzy.

W rzadkich przypadkach, jeśli transakcja izolacji migawek uzyskuje dostęp do metadanych obiektów, które zostały zmodyfikowane w innej współbieżnej transakcji, może wystąpić błąd [3961](/sql/relational-databases/errors-events/mssqlserver-3961-database-engine-error), "transakcja izolacji migawki nie powiodła się w bazie danych"%. * ls ", ponieważ obiekt, do którego uzyskuje dostęp instrukcja, został zmodyfikowany przez instrukcję języka DDL w innej współbieżnej transakcji od momentu rozpoczęcia tej transakcji. Jest to niedozwolone, ponieważ metadane nie są poddane kontroli wersji. Współbieżna aktualizacja metadanych może prowadzić do niespójności, jeśli jest mieszana z izolacją migawki ".

### <a name="long-running-queries-on-read-only-replicas"></a>Długotrwałe zapytania dotyczące replik tylko do odczytu

Zapytania działające w replikach tylko do odczytu muszą uzyskiwać dostęp do metadanych dla obiektów, do których odwołuje się zapytanie (tabele, indeksy, statystyki itp.). W rzadkich przypadkach, jeśli obiekt metadanych jest modyfikowany w replice podstawowej, podczas gdy zapytanie przechowuje blokadę tego samego obiektu w replice tylko do odczytu, zapytanie może [blokować](/sql/database-engine/availability-groups/windows/troubleshoot-primary-changes-not-reflected-on-secondary#BKMK_REDOBLOCK) proces, który stosuje zmiany z repliki podstawowej do repliki tylko do odczytu. Jeśli takie zapytanie było uruchamiane przez dłuższy czas, spowodowałoby to, że replika tylko do odczytu będzie znacząco synchronizowana z repliką podstawową.

Jeśli długotrwałe zapytanie w replice tylko do odczytu powoduje, że ten rodzaj blokowania zostanie automatycznie zakończony. Sesja będzie otrzymywać Błąd 1219, "sesja została rozłączona z powodu operacji DDL o wysokim priorytecie" lub błędu 3947 "transakcja została przerwana, ponieważ pomocnicze obliczenie nie mogło przechwycić ponownie. Spróbuj ponownie wykonać transakcję ".

> [!NOTE]
> Jeśli wystąpi błąd 3961, 1219 lub 3947 podczas wykonywania zapytań dotyczących repliki tylko do odczytu, ponów próbę wykonania zapytania.

> [!TIP]
> W warstwach usług premium i Krytyczne dla działania firmy w przypadku połączenia z repliką tylko do odczytu `redo_queue_size` kolumny i `redo_rate` w [sys.dm_database_replica_states](/sql/relational-databases/system-dynamic-management-views/sys-dm-database-replica-states-azure-sql-database) DMV mogą służyć do monitorowania procesu synchronizacji danych, służącego jako wskaźniki opóźnienia danych w replice tylko do odczytu.
> 

## <a name="enable-and-disable-read-scale-out"></a>Włączanie i wyłączanie skalowania w poziomie odczytu

Skalowanie w poziomie do odczytu jest domyślnie włączone w warstwach usług premium, Krytyczne dla działania firmy i skalowania. Skalowanie w poziomie do odczytu nie może być włączone w warstwach usług podstawowa, standardowa lub Ogólnego przeznaczenia. Skalowanie w poziomie do odczytu jest automatycznie wyłączane w bazach danych w ramach skalowania skonfigurowanych z zerowymi replikami.

Można wyłączyć i ponownie włączyć skalowanie w poziomie dla pojedynczych baz danych i baz danych puli elastycznej w warstwach usług premium lub Krytyczne dla działania firmy przy użyciu poniższych metod.

> [!NOTE]
> W przypadku pojedynczych baz danych i baz danych puli elastycznej można wyłączyć funkcję odczytywania skalowalnego w poziomie w celu zapewnienia zgodności z poprzednimi wersjami. Nie można wyłączyć funkcji odczytu skalowalnego w poziomie dla Krytyczne dla działania firmy wystąpieniami zarządzanymi.

### <a name="azure-portal"></a>Azure Portal

Ustawienie skalowanie odczyt w poziomie można zarządzać w bloku **Konfiguruj** bazę danych.

### <a name="powershell"></a>PowerShell

> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. Moduł Azure Resource Manager będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020.  Argumenty poleceń polecenia AZ module i w modułach Azure Resource Manager są zasadniczo identyczne. Aby uzyskać więcej informacji o zgodności, zobacz [wprowadzenie do nowego Azure PowerShell AZ module](/powershell/azure/new-azureps-module-az).

Zarządzanie skalowaniem do odczytu w Azure PowerShell wymaga wydania z grudnia 2016 Azure PowerShell lub nowszego. Aby uzyskać najnowszą wersję programu PowerShell, zobacz [Azure PowerShell](/powershell/azure/install-az-ps).

Można wyłączyć lub ponownie włączyć funkcję odczytywania skalowania w Azure PowerShell przez wywołanie polecenia cmdlet [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) i przekazanie w odpowiedniej wartości ( `Enabled` lub `Disabled` ) `-ReadScale` parametru.

Aby wyłączyć odczyt skalowalny w poziomie dla istniejącej bazy danych (zastępując elementy w nawiasach kątowych prawidłowymi wartościami dla danego środowiska i upuszczając nawiasy kątowe):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled
```

Aby wyłączyć odczyt skalowalny w poziomie dla nowej bazy danych (zastępując elementy w nawiasach kątowych prawidłowymi wartościami dla środowiska i upuszczając nawiasy kątowe):

```powershell
New-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Disabled -Edition Premium
```

Aby ponownie włączyć funkcję odczytywania skalowalnego w poziomie w istniejącej bazie danych (zastępując elementy w nawiasach kątowych wartościami prawidłowymi dla danego środowiska i porzuceniem nawiasów ostrych):

```powershell
Set-AzSqlDatabase -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> -ReadScale Enabled
```

### <a name="rest-api"></a>Interfejs API REST

Aby utworzyć bazę danych z wyłączonym skalowaniem do odczytu lub zmienić ustawienia dla istniejącej bazy danych, należy użyć następującej metody z `readScale` właściwością ustawioną na `Enabled` lub `Disabled` , jak w poniższym przykładowym żądaniu.

```rest
Method: PUT
URL: https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{GroupName}/providers/Microsoft.Sql/servers/{ServerName}/databases/{DatabaseName}?api-version= 2014-04-01-preview
Body: {
   "properties": {
      "readScale":"Disabled"
   }
}
```

Aby uzyskać więcej informacji, zobacz [bazy danych — Tworzenie lub aktualizowanie](/rest/api/sql/databases/createorupdate).

## <a name="using-the-tempdb-database-on-a-read-only-replica"></a>Korzystanie z `tempdb` bazy danych w replice tylko do odczytu

`tempdb`Baza danych w replice podstawowej nie jest replikowana do replik tylko do odczytu. Każda replika ma własną `tempdb` bazę danych, która jest tworzona podczas tworzenia repliki. Dzięki temu `tempdb` można aktualizować i modyfikować podczas wykonywania zapytania. Jeśli obciążenie tylko do odczytu zależy od używania `tempdb` obiektów, należy utworzyć te obiekty jako część skryptu zapytania.

## <a name="using-read-scale-out-with-geo-replicated-databases"></a>Używanie skalowania w poziomie z bazami danych z replikacją geograficzną

Pomocnicze bazy danych z replikacją geograficzną mają tę samą architekturę o wysokiej dostępności, co w przypadku podstawowych baz danych. W przypadku nawiązywania połączenia z pomocniczą bazą danych z replikacją geograficzną z włączonym skalowaniem w poziomie odczyt sesje z systemem `ApplicationIntent=ReadOnly` będą kierowane do jednej z replik o wysokiej dostępności w taki sam sposób, w jaki są kierowane do podstawowej zapisywalnej bazy danych. Sesje bez programu `ApplicationIntent=ReadOnly` będą kierowane do podstawowej repliki pomocniczej replikacji geograficznej, która jest również tylko do odczytu. 

W ten sposób tworzenie repliki geograficznej zapewnia dwie repliki tylko do odczytu dla podstawowej bazy danych do odczytu i zapisu, co łącznie trzech replik tylko do odczytu. Każda dodatkowa replika geograficzna zapewnia kolejną parę replik tylko do odczytu. Repliki geograficzne można tworzyć w dowolnym regionie świadczenia usługi Azure, łącznie z regionem podstawowej bazy danych.

> [!NOTE]
> Nie ma żadnej automatycznej operacji okrężnej ani żadnego innego routingu z równoważeniem obciążenia między replikami pomocniczej bazy danych z replikacją geograficzną.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat SQL Database funkcji tworzenia i skalowania, zobacz temat [skalowanie warstwy usług](service-tier-hyperscale.md).