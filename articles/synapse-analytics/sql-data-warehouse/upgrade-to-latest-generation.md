---
title: Uaktualnij do najnowszej generacji
description: Uaktualnij dedykowaną pulę SQL usługi Azure Synapse Analytics do najnowszej generacji architektury sprzętu i magazynu platformy Azure.
services: synapse-analytics
author: mlee3gsd
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/19/2019
ms.author: martinle
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: b025b26d505f99b3bc92e995fde9184a4cc26a4d
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93309586"
---
# <a name="optimize-performance-by-upgrading-dedicated-sql-pool-in-azure-synapse-analytics"></a>Optymalizowanie wydajności przez uaktualnienie dedykowanej puli SQL w usłudze Azure Synapse Analytics

Uaktualnij dedykowaną pulę SQL do najnowszej generacji architektury sprzętu i magazynu platformy Azure.

## <a name="why-upgrade"></a>Dlaczego warto przeprowadzić uaktualnienie?

Teraz można bezproblemowo przeprowadzić uaktualnienie do dedykowanej warstwy usługi obliczeniowej Gen2 w puli SQL w Azure Portal dla [obsługiwanych regionów](gen2-migration-schedule.md#automated-schedule-and-region-availability-table). Jeśli region nie obsługuje samodzielnego uaktualniania, możesz przeprowadzić uaktualnienie do obsługiwanego regionu lub poczekać na udostępnienie samodzielnego uaktualnienia w Twoim regionie. Uaktualnij teraz, aby korzystać z najnowszej generacji sprzętu platformy Azure i rozszerzonej architektury magazynu, w tym szybszej wydajności, wyższej skalowalności i nieograniczonego magazynu kolumnowego.

> [!VIDEO https://www.youtube.com/embed/9B2F0gLoyss]

> [!IMPORTANT]
> To uaktualnienie dotyczy w [obsługiwanych regionach](gen2-migration-schedule.md#automated-schedule-and-region-availability-table)dedykowanych pul SQL w warstwie Gen1.

## <a name="before-you-begin"></a>Przed rozpoczęciem

1. Sprawdź, czy [region](gen2-migration-schedule.md#automated-schedule-and-region-availability-table) jest obsługiwany na potrzeby migracji GEN1 do GEN2. Zanotuj daty automatycznej migracji. Aby uniknąć konfliktów z procesem zautomatyzowanym, zaplanuj migrację ręczną przed datą rozpoczęcia procesu automatycznego.
2. Jeśli jesteś w regionie, który nie jest jeszcze obsługiwany, Kontynuuj sprawdzanie, czy region ma zostać dodany lub [uaktualniony przy użyciu polecenia Przywróć](#upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal) do obsługiwanego regionu.
3. Jeśli region jest obsługiwany, [Uaktualnij go za pomocą Azure Portal](#upgrade-in-a-supported-region-using-the-azure-portal)
4. **Wybierz sugerowany poziom wydajności** dla dedykowanej puli SQL na podstawie bieżącego poziomu wydajności w warstwie Gen1 zoptymalizowanej pod kątem obliczeń przy użyciu poniższego mapowania:

   | Warstwa zoptymalizowana pod kątem obliczeń Gen1 | Warstwa zoptymalizowana pod kątem obliczeń Gen2 |
   | :-------------------------: | :-------------------------: |
   |            DW100            |           DW100c            |
   |            WARTOŚCI DW200            |           DW200c            |
   |            DW300            |           DW300c            |
   |            DW400            |           DW400c            |
   |            DW500            |           DW500c.            |
   |            WARTOŚCI DW600            |           DW500c.            |
   |           WARTOŚCI DW1000            |           DW1000c           |
   |           DW1200            |           DW1000c           |
   |           DW1500            |           DW1500c           |
   |           DW2000            |           DW2000c           |
   |           DW3000            |           DW3000c           |
   |           DW6000            |           DW6000c           |

> [!NOTE]
> Sugerowane poziomy wydajności nie są konwersją bezpośrednią. Na przykład zalecamy przechodzenie z wartości DW600 do DW500c.

## <a name="upgrade-in-a-supported-region-using-the-azure-portal"></a>Uaktualnij program w obsługiwanym regionie przy użyciu Azure Portal

- Migracja z Gen1 do Gen2 za pośrednictwem Azure Portal jest trwała. Nie istnieje proces powrotu do Gen1.
- Aby przeprowadzić migrację do Gen2, musi być uruchomiona dedykowana Pula SQL

### <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

- Zaloguj się do [portalu Azure](https://portal.azure.com/).
- Upewnij się, że dedykowana Pula SQL jest uruchomiona — musi być migrowana do Gen2

### <a name="powershell-upgrade-commands"></a>Polecenia uaktualniania programu PowerShell

1. Jeśli przeprowadzono wstrzymanie dedykowanej puli SQL w warstwie Gen1, która ma zostać uaktualniona, [Wznów dedykowaną pulę SQL](pause-and-resume-compute-portal.md).

2. Przygotowanie przez kilka minut przestoju.

3. Zidentyfikuj wszystkie odwołania do kodu na potrzeby obliczeń zoptymalizowanych pod kątem wydajności Gen1 i zmodyfikuj je na równoważnym poziomie wydajności zoptymalizowanym pod kątem obliczeń Gen2. Poniżej znajdują się dwa przykłady, w których należy zaktualizować odwołania do kodu przed uaktualnieniem:

   Oryginalne polecenie programu Gen1 PowerShell:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300"
   ```

   Zmodyfikowano:

   ```powershell
   Set-AzSqlDatabase -ResourceGroupName "myResourceGroup" -DatabaseName "mySampleDataWarehouse" -ServerName "mynewserver-20171113" -RequestedServiceObjectiveName "DW300c"
   ```

   > [!NOTE]
   > -RequestedServiceObjectiveName "DW300" został zmieniony na-RequestedServiceObjectiveName "DW300 **c** "
   >

   Oryginalne Gen1 polecenia T-SQL:

   ```SQL
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300') ;
   ```

   Zmodyfikowano:

   ```sql
   ALTER DATABASE mySampleDataWarehouse MODIFY (SERVICE_OBJECTIVE = 'DW300c') ;
   ```

   > [!NOTE]
   > SERVICE_OBJECTIVE = "DW300" została zmieniona na SERVICE_OBJECTIVE = "DW300 **c** "

## <a name="start-the-upgrade"></a>Rozpocznij uaktualnianie

1. Przejdź do puli programu SQL zoptymalizowanej pod kątem obliczeń Gen1 w Azure Portal. Jeśli przeprowadzono wstrzymanie dedykowanej puli SQL w warstwie Gen1, która ma zostać uaktualniona, [Wznów dedykowaną pulę SQL](pause-and-resume-compute-portal.md).
2. Wybierz pozycję **Uaktualnij do karty Gen2** na karcie zadania: ![ Upgrade_1](./media/upgrade-to-latest-generation/upgrade-to-gen2-1.png)

   > [!NOTE]
   > Jeśli nie widzisz karty **uaktualnienie do Gen2** na karcie zadania, typ subskrypcji jest ograniczony w bieżącym regionie.
   > [Prześlij bilet pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md) , aby uzyskać zatwierdzenie subskrypcji.

3. Upewnij się, że obciążenie zostało zakończone i przestanie działać w stanie spoczynku przed uaktualnieniem. Będziesz mieć czas przestoju przez kilka minut, zanim dedykowana Pula SQL będzie powracać do trybu online jako dedykowana Pula usługi Gen2 zoptymalizowana pod kątem obliczeń. **Wybierz pozycję Uaktualnij** :

   ![Upgrade_2](./media/upgrade-to-latest-generation/upgrade-to-gen2-2.png)

4. **Monitoruj uaktualnienie** , sprawdzając stan w Azure Portal:

   ![Upgrade3](./media/upgrade-to-latest-generation/upgrade-to-gen2-3.png)

   Pierwszy krok procesu uaktualniania przechodzi przez operację skalowania ("uaktualnienie do trybu offline"), w którym wszystkie sesje zostaną zabite, a połączenia zostaną usunięte.

   Drugim krokiem procesu uaktualniania jest migracja danych ("Uaktualnianie w trybie online"). Migracja danych to proces w tle Trickle online. Ten proces wolno przenosi dane kolumnowy ze starej architektury magazynu do nowej architektury magazynu przy użyciu lokalnej pamięci podręcznej SSD. W tym czasie dedykowana Pula SQL będzie w trybie online na potrzeby wykonywania zapytań i ładowania. Dane będą dostępne do zbadania, niezależnie od tego, czy zostały zmigrowane, czy nie. Migracja danych odbywa się przy różnych stawkach, w zależności od rozmiaru danych, poziomu wydajności i liczby segmentów magazynu kolumn.

5. **Zalecenie opcjonalne:** Po zakończeniu operacji skalowania można przyspieszyć proces w tle migracji danych. Aby wymusić przenoszenie danych, należy uruchomić polecenie [ALTER index Rebuild](sql-data-warehouse-tables-index.md) na wszystkich głównych tabelach magazynu kolumn, które zostaną zbadane przy użyciu większego poziomu SLO i klasy zasobów. Ta operacja jest **w trybie offline** w porównaniu z procesem Trickle w tle, co może potrwać kilka godzin w zależności od liczby i rozmiarów tabel. Jednak po zakończeniu migracja danych będzie znacznie szybsza ze względu na nową architekturę magazynu rozszerzonego o wysokiej jakości RowGroups.

> [!NOTE]
> Instrukcja ALTER index Rebuild jest operacją w trybie offline, a tabele nie będą dostępne do momentu zakończenia odbudowy.

Poniższe zapytanie generuje wymagane polecenia ALTER index Rebuild, aby przyspieszyć migrację danych:

```sql
SELECT 'ALTER INDEX [' + idx.NAME + '] ON ['
       + Schema_name(tbl.schema_id) + '].['
       + Object_name(idx.object_id) + '] REBUILD ' + ( CASE
                                                         WHEN (
                                                     (SELECT Count(*)
                                                      FROM   sys.partitions
                                                             part2
                                                      WHERE  part2.index_id
                                                             = idx.index_id
                                                             AND
                                                     idx.object_id =
                                                     part2.object_id)
                                                     > 1 ) THEN
              ' PARTITION = '
              + Cast(part.partition_number AS NVARCHAR(256))
              ELSE ''
                                                       END ) + '; SELECT ''[' +
              idx.NAME + '] ON [' + Schema_name(tbl.schema_id) + '].[' +
              Object_name(idx.object_id) + '] ' + (
              CASE
                WHEN ( (SELECT Count(*)
                        FROM   sys.partitions
                               part2
                        WHERE
                     part2.index_id =
                     idx.index_id
                     AND idx.object_id
                         = part2.object_id) > 1 ) THEN
              ' PARTITION = '
              + Cast(part.partition_number AS NVARCHAR(256))
              + ' completed'';'
              ELSE ' completed'';'
                                                    END )
FROM   sys.indexes idx
       INNER JOIN sys.tables tbl
               ON idx.object_id = tbl.object_id
       LEFT OUTER JOIN sys.partitions part
                    ON idx.index_id = part.index_id
                       AND idx.object_id = part.object_id
WHERE  idx.type_desc = 'CLUSTERED COLUMNSTORE';
```

## <a name="upgrade-from-an-azure-geographical-region-using-restore-through-the-azure-portal"></a>Uaktualnianie z regionu geograficznego platformy Azure przy użyciu funkcji przywracania przez Azure Portal

## <a name="create-a-user-defined-restore-point-using-the-azure-portal"></a>Utwórz punkt przywracania zdefiniowany przez użytkownika przy użyciu Azure Portal

1. Zaloguj się do [portalu Azure](https://portal.azure.com/).

2. Przejdź do dedykowanej puli SQL, dla której chcesz utworzyć punkt przywracania.

3. W górnej części sekcji Przegląd wybierz pozycję **+ nowy punkt przywracania**.

    ![Nowy punkt przywracania](./media/upgrade-to-latest-generation/creating_restore_point_0.png)

4. Określ nazwę punktu przywracania.

    ![Nazwa punktu przywracania](./media/upgrade-to-latest-generation/creating_restore_point_1.png)

## <a name="restore-an-active-or-paused-database-using-the-azure-portal"></a>Przywracanie aktywnej lub wstrzymanej bazy danych przy użyciu Azure Portal

1. Zaloguj się do [portalu Azure](https://portal.azure.com/).
2. Przejdź do dedykowanej puli SQL, z której chcesz wykonać przywracanie.
3. W górnej części sekcji Przegląd wybierz pozycję **Przywróć**.

    ![ Omówienie przywracania kopii zapasowych](./media/upgrade-to-latest-generation/restoring_0.png)

4. Wybierz **punkty przywracania automatycznego** lub **punkty przywracania zdefiniowane przez użytkownika**. Dla punktów przywracania zdefiniowanych przez użytkownika **Wybierz punkt przywracania zdefiniowany przez użytkownika** lub **Utwórz nowy punkt przywracania zdefiniowany przez użytkownika**. Na serwerze wybierz pozycję **Utwórz nowy** i wybierz serwer w Gen2 obsługiwanym regionie geograficznym.

    ![Automatyczne punkty przywracania](./media/upgrade-to-latest-generation/restoring_1.png)

## <a name="restore-from-an-azure-geographical-region-using-powershell"></a>Przywracanie z regionu geograficznego platformy Azure przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby odzyskać bazę danych, należy użyć polecenia cmdlet [Restore-AzSqlDatabase](/powershell/module/az.sql/restore-azsqldatabase?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) .

> [!NOTE]
> Można wykonać przywracanie geograficzne do Gen2! W tym celu należy określić Gen2 serviceobiektywname (np. wartości DW1000 **c** ) jako opcjonalny parametr.

1. Otwórz program Windows PowerShell.
2. Połącz się z kontem platformy Azure i Wyświetl listę wszystkich subskrypcji skojarzonych z Twoim kontem.
3. Wybierz subskrypcję zawierającą bazę danych, która ma zostać przywrócona.
4. Pobierz bazę danych, którą chcesz odzyskać.
5. Utwórz żądanie odzyskiwania dla bazy danych, określając Gen2 servicecelname.
6. Sprawdź stan bazy danych z przywróconą geograficzną.

```Powershell
Connect-AzAccount
Get-AzSubscription
Select-AzSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID -ServiceObjectiveName "<YourTargetServiceLevel>" -RequestedServiceObjectiveName "DW300c"

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

> [!NOTE]
> Aby skonfigurować bazę danych po zakończeniu przywracania, zobacz [Konfigurowanie bazy danych po odzyskaniu](../../azure-sql/database/disaster-recovery-guidance.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#configure-your-database-after-recovery).

Odzyskana baza danych będzie TDE, jeśli źródłowa baza danych jest włączona.

Jeśli występują problemy z dedykowaną pulą SQL, Utwórz [żądanie pomocy technicznej](sql-data-warehouse-get-started-create-support-ticket.md) i odwołuje się do "Gen2 upgrade" jako możliwej przyczyny.

## <a name="next-steps"></a>Następne kroki

Uaktualniona dedykowana Pula SQL jest w trybie online. Aby skorzystać z rozszerzonej architektury, zobacz [klasy zasobów dla zarządzania obciążeniami](resource-classes-for-workload-management.md).
