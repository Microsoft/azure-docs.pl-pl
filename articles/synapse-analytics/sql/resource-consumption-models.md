---
title: Zużycie zasobów SQL usługi Synapse Analytics
description: Dowiedz się więcej o modelach użycia SQL Synapse w usłudze Azure Synapse Analytics.
services: synapse analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.openlocfilehash: e078893b3bbe0ef5661cd87bad62b320f78ceb5d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424860"
---
# <a name="azure-synapse-analytics-sql-resource-consumption"></a>Zużycie zasobów SQL usługi Azure Synapse Analytics

W tym artykule opisano modele zużycia zasobów synapse SQL (wersja zapoznawcza).

## <a name="sql-on-demand"></a>SQL na żądanie

SQL na żądanie to usługa płatności za zapytanie, która nie wymaga wybierania odpowiedniego rozmiaru. System automatycznie dostosowuje się do twoich wymagań, zwalniając cię od zarządzania infrastrukturą i wybierając odpowiedni rozmiar dla twojego rozwiązania.

## <a name="sql-pool---data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>Pula SQL — jednostki magazynowania danych (DWU) i jednostki hurtowni danych obliczeniowych (cDWU)

Zalecenia dotyczące wyboru idealnej liczby jednostek magazynu danych (DWU) w celu optymalizacji ceny i wydajności oraz sposobu zmiany liczby jednostek.

### <a name="what-are-data-warehouse-units"></a>Co to są jednostki magazynu danych

A Synapse puli SQL reprezentuje zbiór zasobów analitycznych, które są aprowizacji. Zasoby analityczne są definiowane jako połączenie procesora CPU, pamięci i we/wy. Te trzy zasoby są powiązane z jednostkami skali obliczeniowej o nazwie Jednostki hurtowni danych (DWU). Jednostka DWU to abstrakcyjna, znormalizowana miara zasobów obliczeniowych i wydajności. Zmiana poziomu usług zmienia liczbę jednostek DU dostępnych dla systemu, co z kolei dostosowuje wydajność i koszt systemu.

Aby uzyskać wyższą wydajność, można zwiększyć liczbę jednostek magazynu danych. Aby zmniejszyć wydajność, zmniejsz jednostki magazynu danych. Koszty magazynu i mocy obliczeniowej są rozliczane osobno, więc zmiana liczby jednostek magazynu danych nie ma wpływu na koszty magazynu.

Wydajność jednostek magazynu danych jest oparta na tych metrykach obciążenia magazynu danych:

- Jak szybko standardowe zapytanie hurtowni danych może skanować dużą liczbę wierszy, a następnie wykonywać agregację złożoną. Ta operacja jest intensywnie we/wy i procesora CPU.
- Jak szybko magazyn danych może pozyskiwania danych z obiektów blob usługi Azure Storage lub usługi Azure Data Lake. Ta operacja jest intensywnie sieciowa i obciążana procesorem.
- Jak szybko [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) polecenie T-SQL może kopiować tabelę. Ta operacja polega na odczytywaniu danych z magazynu, dystrybucji ich w węzłach urządzenia i ponownym zapisaniu do magazynu. Ta operacja jest procesora CPU, We/Wy i sieci intensywnie.

Zwiększenie DWUs:

- Liniowo zmienia wydajność systemu dla skanowania, agregacji i instrukcji CTAS
- Zwiększa liczbę czytników i modułów zapisu dla operacji ładowania PolyBase
- Zwiększa maksymalną liczbę równoczesnych zapytań i gniazd współbieżności.

### <a name="service-level-objective"></a>Cel poziomu usług

Cel poziomu usług (SLO) to ustawienie skalowalności, które określa poziom kosztów i wydajności magazynu danych. Poziomy usług dla Gen2 są mierzone w jednostkach magazynu danych obliczeniowych (cDWU), na przykład DW2000c. Poziomy usług Gen1 są mierzone w jednostkach DW, na przykład DW2000.

Cel poziomu usług (SLO) to ustawienie skalowalności, które określa poziom kosztów i wydajności magazynu danych. Poziomy usług dla puli SQL Gen2 są mierzone w jednostkach magazynu danych (DWU), na przykład DW2000c.

> [!NOTE]
> Usługa Azure SQL Data Warehouse Gen2 niedawno dodała dodatkowe możliwości skalowania do obsługi warstw obliczeniowych już od 100 cDWU. Istniejące magazyny danych obecnie w gen1, które wymagają niższych warstw obliczeniowych można teraz uaktualnić do Gen2 w regionach, które są obecnie dostępne bez dodatkowych kosztów.  Jeśli region nie jest jeszcze obsługiwany, nadal można uaktualnić do obsługiwanego regionu. Aby uzyskać więcej informacji, zobacz [Uaktualnienie do gen2](../sql-data-warehouse/upgrade-to-latest-generation.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

W języku T-SQL ustawienie SERVICE_OBJECTIVE określa poziom usługi i warstwę wydajności dla puli SQL.

```sql
CREATE DATABASE mySQLDW
(Edition = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

### <a name="performance-tiers-and-data-warehouse-units"></a>Warstwy wydajności i jednostki hurtowni danych

Każda warstwa wydajności używa nieco innej jednostki miary dla swoich jednostek magazynu danych. Różnica ta jest odzwierciedlona na fakturze, ponieważ jednostka skali bezpośrednio przekłada się na rozliczenia.

- Magazyny danych gen1 są mierzone w jednostkach hurtowni danych (DWU).
- Magazyny danych Gen2 są mierzone w jednostkach hurtowni danych obliczeniowych (cDWU).

Zarówno jednostki DWU, jak i jednostki cDWU obsługują skalowanie obliczeń w górę lub w dół oraz wstrzymywanie obliczeń, gdy nie trzeba używać magazynu danych. Wszystkie te operacje są na żądanie. Gen2 używa lokalnej pamięci podręcznej opartej na dysku w węzłach obliczeniowych w celu zwiększenia wydajności. Podczas skalowania lub wstrzymywania systemu pamięć podręczna jest unieważniona, a zatem okres ocieplenia pamięci podręcznej jest wymagany przed osiągnięciem optymalnej wydajności.  

W miarę zwiększania jednostek magazynu danych zwiększa się liniowo zasoby obliczeniowe. Gen2 zapewnia najlepszą wydajność zapytań i najwyższą skalę. Systemy Gen2 również w pełni wykorzystują pamięć podręczną.

#### <a name="capacity-limits"></a>Limity pojemności

Każdy serwer SQL (na przykład myserver.database.windows.net) ma przydział [jednostki DTU (Database Transaction Unit),](../../sql-database/sql-database-service-tiers-dtu.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) który umożliwia określoną liczbę jednostek magazynu danych. Aby uzyskać więcej informacji, zobacz [limity pojemności zarządzania obciążeniem](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#workload-management).

### <a name="how-many-data-warehouse-units-do-i-need"></a>Ile jednostek magazynu danych potrzebuję

Idealna liczba jednostek magazynu danych zależy w dużej mierze od obciążenia i ilości danych załadowanych do systemu.

Kroki, aby znaleźć najlepszy DWU dla obciążenia:

1. Rozpocznij od wybrania mniejszego DWU.
2. Monitoruj wydajność aplikacji podczas testowania wczytywania danych do systemu, obserwując liczbę jednostek DU wybranych w porównaniu z obserwowaną wydajnością.
3. Określ wszelkie dodatkowe wymagania dotyczące okresowych okresów szczytowej aktywności. Obciążenia, które wykazują znaczne szczyty i koryta w aktywności może być konieczne często skalowane.

Pula SQL to system skalowania w poziomie, który może aprowizować ogromne ilości danych obliczeniowych i zapytań. Aby wyświetlić jego prawdziwe możliwości skalowania, zwłaszcza w większych jednostek DWU, zaleca się skalowanie zestawu danych podczas skalowania, aby upewnić się, że masz wystarczającą ilość danych do źródła procesorów. Do testowania skali zaleca się użycie co najmniej 1 TB.

> [!NOTE]
>
> Wydajność kwerendy zwiększa się tylko z większą równoległością, jeśli pracę można podzielić między węzłami obliczeniowymi. Jeśli okaże się, że skalowanie nie zmienia wydajności, może być konieczne dostrojenie projektu tabeli i/lub zapytań. Aby uzyskać wskazówki dotyczące dostrajania zapytań, zobacz [Zarządzanie kwerendami użytkowników](../overview-cheat-sheet.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="permissions"></a>Uprawnienia

Zmiana jednostek magazynu danych wymaga uprawnień opisanych w [alter database](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

Wbudowane role dla zasobów platformy Azure, takich jak SQL DB Contributor i SQL Server Contributor, mogą zmieniać ustawienia DWU.

#### <a name="view-current-dwu-settings"></a>Wyświetlanie bieżących ustawień DWU

Aby wyświetlić bieżące ustawienie DWU:

1. Otwórz Eksploratora obiektów programu SQL Server w programie Visual Studio.
2. Połącz się z główną bazą danych skojarzoną z logicznym serwerem bazy danych SQL.
3. Wybierz z dynamicznego widoku zarządzania sys.database_service_objectives. Oto przykład:

```sql
SELECT  db.name [Database]
,        ds.edition [Edition]
,        ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

### <a name="change-data-warehouse-units"></a>Zmienianie jednostek magazynu danych

#### <a name="azure-portal"></a>Azure Portal

Aby zmienić jednostki DWU:

1. Otwórz [witrynę Azure portal](https://portal.azure.com), otwórz bazę danych i kliknij przycisk **Skaluj**.

2. W obszarze **Skala**przesuń suwak w lewo lub w prawo, aby zmienić ustawienie DWU.

3. Kliknij pozycję **Zapisz**. Zostanie wyświetlony komunikat z potwierdzeniem. Kliknij pozycję **tak**, aby potwierdzić, lub **nie**, aby anulować.

#### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby zmienić jednostki dwu, należy użyć polecenia cmdlet programu PowerShell [Set-AzSqlDatabase.](/powershell/module/az.sql/set-azsqldatabase?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) W poniższym przykładzie ustawia cel poziomu usług na DW1000 dla bazy danych MySQLDW, która jest hostowana na serwerze MyServer.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000c"
```

Aby uzyskać więcej informacji, zobacz [polecenia cmdlet programu PowerShell dla magazynu danych SQL](../sql-data-warehouse/sql-data-warehouse-reference-powershell-cmdlets.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

#### <a name="t-sql"></a>T-SQL

Za pomocą języka T-SQL można wyświetlać bieżące urządzenia DWUsettings, zmieniać ustawienia i sprawdzać postęp.

Aby zmienić jednostki DWU:

1. Połącz się z główną bazą danych skojarzoną z serwerem logicznej bazy danych SQL.
2. Użyj instrukcji [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) TSQL. W poniższym przykładzie ustawia cel poziomu usługi dw1000c dla bazy danych MySQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000c')
;
```

#### <a name="rest-apis"></a>Interfejsy API REST

Aby zmienić jednostki DWU, użyj interfejsu API [tworzenie lub aktualizowanie interfejsu](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) REST bazy danych. W poniższym przykładzie ustawia cel poziomu usług na DW1000c dla bazy danych MySQLDW, która jest hostowana na serwerze MyServer. Serwer znajduje się w grupie zasobów platformy Azure o nazwie ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

Aby uzyskać więcej przykładów interfejsu API REST, zobacz [interfejsy API REST dla usługi SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-manage-compute-rest-api.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

### <a name="check-status-of-dwu-changes"></a>Sprawdź stan zmian DWU

Dwu zmiany może potrwać kilka minut, aby zakończyć. Jeśli skalowanie jest skalowanie automatycznie, należy rozważyć implementowanie logiki, aby upewnić się, że niektóre operacje zostały zakończone przed kontynuowaniem innej akcji.

Sprawdzanie stanu bazy danych za pomocą różnych punktów końcowych umożliwia poprawne zaimplementowanie automatyzacji. Portal zapewnia powiadomienie po zakończeniu operacji i bieżącego stanu baz danych, ale nie zezwala na programowe sprawdzanie stanu.

Nie można sprawdzić stanu bazy danych dla operacji skalowania w poziomie za pomocą witryny Azure Portal.

Aby sprawdzić stan zmian DWU:

1. Połącz się z główną bazą danych skojarzoną z serwerem logicznej bazy danych SQL.
2. Prześlij następującą kwerendę, aby sprawdzić stan bazy danych.

```sql
SELECT    *
FROM      sys.databases
;
```

1. Prześlij następującą kwerendę, aby sprawdzić stan operacji

```sql
SELECT    *
FROM      sys.dm_operation_status
WHERE     resource_type_desc = 'Database'
AND       major_resource_id = 'MySQLDW'
;
```

Ten dmv zwraca informacje o różnych operacji zarządzania w puli SQL, takich jak operacja i stan operacji, który jest IN_PROGRESS lub zakończone.

### <a name="the-scaling-workflow"></a>Przepływ pracy skalowania

Po uruchomieniu operacji skalowania system najpierw zabija wszystkie otwarte sesje, cofanie wszystkich otwartych transakcji w celu zapewnienia spójnego stanu. W przypadku operacji skalowania skalowanie występuje tylko po zakończeniu wycofywania transakcyjnego.  

- W przypadku operacji skalowania w górę system odłącza wszystkie węzły obliczeniowe, apowizoruje dodatkowe węzły obliczeniowe, a następnie ponownie dołącza do warstwy magazynu.
- W przypadku operacji skalowania w dół system odłącza wszystkie węzły obliczeniowe, a następnie ponownie dołącza tylko potrzebne węzły do warstwy magazynu.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat zarządzania wydajnością, zobacz [Klasy zasobów dotyczące zarządzania obciążeniami](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) oraz [limitów pamięci i współbieżności](../sql-data-warehouse/memory-concurrency-limits.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).
