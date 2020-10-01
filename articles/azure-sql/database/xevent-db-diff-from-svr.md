---
title: Zdarzenia rozszerzone
description: Opisuje zdarzenia rozszerzone (XEvents) w Azure SQL Database oraz sposób różnicowania sesji zdarzeń w przypadku sesji zdarzeń w Microsoft SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: reference
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: c8f73c0789cd0211deeb66af5c7300a81d7b1be0
ms.sourcegitcommit: 4bebbf664e69361f13cfe83020b2e87ed4dc8fa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91619818"
---
# <a name="extended-events-in-azure-sql-database"></a>Zdarzenia rozszerzone w Azure SQL Database 
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

Zestaw funkcji rozszerzonych zdarzeń w Azure SQL Database jest niezawodnym podzbiorem funkcji SQL Server i wystąpienia zarządzanego usługi Azure SQL.

*XEvents* to nieformalny pseudonim, który jest czasami używany dla "rozszerzonych zdarzeń" w blogach i innych nieformalnych lokalizacjach.

Dodatkowe informacje na temat zdarzeń rozszerzonych są dostępne pod adresem:

- [Szybki start: rozszerzone zdarzenia w SQL Server](/sql/relational-databases/extended-events/quick-start-extended-events-in-sql-server)
- [Zdarzenia rozszerzone](/sql/relational-databases/extended-events/extended-events)

## <a name="prerequisites"></a>Wymagania wstępne

W tym temacie założono, że masz już pewną wiedzę na temat:

- [Azure SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Zdarzenia rozszerzone](/sql/relational-databases/extended-events/extended-events)

- Obszerna dokumentacja dotycząca zdarzeń rozszerzonych ma zastosowanie do SQL Server, Azure SQL Database i wystąpienia zarządzanego usługi Azure SQL.

Przed wybraniem pliku zdarzenia jako [obiektu docelowego](#AzureXEventsTargets)pomocne jest wcześniejsze narażenie na następujące elementy:

- [Usługa Azure Storage](https://azure.microsoft.com/services/storage/)

- [Azure PowerShell z usługą Azure Storage](/powershell/module/az.storage/)

## <a name="code-samples"></a>Przykłady kodu

Tematy pokrewne zawierają dwa przykłady kodu:

- [Kod docelowy buforu pierścieniowego dla zdarzeń rozszerzonych w Azure SQL Database](xevent-code-ring-buffer.md)

  - Krótki prosty skrypt języka Transact-SQL.
  - Podkreślamy w temacie przykładowy kod, który po zakończeniu pracy z obiektem docelowym bufora pierścieniowego należy zwolnić jego zasoby przez wykonanie instrukcji ALTER-Drop `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` . Później można dodać kolejne wystąpienie buforu pierścienia przez `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...` .

- [Kod docelowy pliku zdarzeń dla zdarzeń rozszerzonych w Azure SQL Database](xevent-code-event-file.md)

  - Faza 1 to program PowerShell do utworzenia kontenera usługi Azure Storage.
  - Faza 2 to Transact-SQL, który używa kontenera usługi Azure Storage.

## <a name="transact-sql-differences"></a>Różnice w języku Transact-SQL

- Gdy wykonujesz polecenie [Utwórz sesję zdarzenia](/sql/t-sql/statements/create-event-session-transact-sql) na SQL Server, użyj klauzuli **on serwera** . Ale na Azure SQL Database zamiast tego używasz klauzuli **on Database** .
- Klauzula for **Database** dotyczy również poleceń [ALTER Event Session](/sql/t-sql/statements/alter-event-session-transact-sql) i [Drop Event](/sql/t-sql/statements/drop-event-session-transact-sql) .

- Najlepszym rozwiązaniem jest dołączenie opcji sesji zdarzeń **STARTUP_STATE = on** w instrukcji **Create Event Session**  lub **ALTER Event Session** .
  - Wartość **= on** obsługuje automatyczne ponowne uruchamianie po ponownym skonfigurowaniu logicznej bazy danych z powodu przejścia w tryb failover.

## <a name="new-catalog-views"></a>Nowe widoki wykazu

Funkcja zdarzeń rozszerzonych jest obsługiwana przez kilka [widoków wykazu](https://msdn.microsoft.com/library/ms174365.aspx). Widoki wykazu informują o *metadanych lub definicjach* sesji zdarzeń utworzonych przez użytkownika w bieżącej bazie danych. Widoki nie zwracają informacji o wystąpieniach aktywnych sesji zdarzeń.

| Nazwa<br/>Widok wykazu | Opis |
|:--- |:--- |
| **sys. database_event_session_actions** |Zwraca wiersz dla każdej akcji w każdym zdarzeniu sesji zdarzeń. |
| **sys. database_event_session_events** |Zwraca wiersz dla każdego zdarzenia w sesji zdarzeń. |
| **sys. database_event_session_fields** |Zwraca wiersz dla każdej kolumny dostosowania, która została jawnie ustawiona dla zdarzeń i elementów docelowych. |
| **sys. database_event_session_targets** |Zwraca wiersz dla każdego obiektu docelowego zdarzenia dla sesji zdarzeń. |
| **sys. database_event_sessions** |Zwraca wiersz dla każdej sesji zdarzeń w bazie danych. |

W Microsoft SQL Server podobne widoki wykazu mają nazwy, które obejmują *. serwer \_ * zamiast *. baza danych \_ *. Wzorzec nazwy jest podobny do wykazu **sys. server_event_%**.

## <a name="new-dynamic-management-views-dmvs"></a>Nowe dynamiczne widoki zarządzania [(widoków DMV)](https://msdn.microsoft.com/library/ms188754.aspx)

Azure SQL Database ma [dynamiczne widoki zarządzania (widoków DMV)](https://msdn.microsoft.com/library/bb677293.aspx) , które obsługują zdarzenia rozszerzone. Widoków DMV informacje o *aktywnych* sesjach zdarzeń.

| Nazwa DMV | Opis |
|:--- |:--- |
| **sys. dm_xe_database_session_event_actions** |Zwraca informacje o akcjach sesji zdarzeń. |
| **sys. dm_xe_database_session_events** |Zwraca informacje o zdarzeniach sesji. |
| **sys. dm_xe_database_session_object_columns** |Pokazuje wartości konfiguracji dla obiektów, które są powiązane z sesją. |
| **sys. dm_xe_database_session_targets** |Zwraca informacje o celach docelowych sesji. |
| **sys. dm_xe_database_sessions** |Zwraca wiersz dla każdej sesji zdarzeń, który jest objęty zakresem bieżącej bazy danych. |

W Microsoft SQL Server podobne widoki wykazu są nazwane bez części nazwy * \_ bazy danych* , takiej jak:

- **sys. dm_xe_sessions**, a nie nazwa<br/>**sys. dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>Widoków DMV wspólne dla obu

W przypadku zdarzeń rozszerzonych istnieją dodatkowe widoków DMV, które są wspólne dla Azure SQL Database, wystąpienia zarządzanego usługi Azure SQL i Microsoft SQL Server:

- **sys. dm_xe_map_values**
- **sys. dm_xe_object_columns**
- **sys. dm_xe_objects**
- **sys. dm_xe_packages**

<a name="sqlfindseventsactionstargets" id="sqlfindseventsactionstargets"></a>

## <a name="find-the-available-extended-events-actions-and-targets"></a>Znajdź dostępne rozszerzone zdarzenia, akcje i cele

Można uruchomić prostą instrukcję SQL, aby uzyskać listę dostępnych zdarzeń, akcji **i celu.**

```sql
SELECT
        o.object_type,
        p.name         AS [package_name],
        o.name         AS [db_object_name],
        o.description  AS [db_obj_description]
    FROM
                   sys.dm_xe_objects  AS o
        INNER JOIN sys.dm_xe_packages AS p  ON p.guid = o.package_guid
    WHERE
        o.object_type in
            (
            'action',  'event',  'target'
            )
    ORDER BY
        o.object_type,
        p.name,
        o.name;
```

<a name="AzureXEventsTargets" id="AzureXEventsTargets"></a> &nbsp;

## <a name="targets-for-your-azure-sql-database-event-sessions"></a>Cele dla sesji zdarzeń Azure SQL Database

Poniżej znajdują się elementy docelowe, które mogą przechwytywać wyniki z sesji zdarzeń na Azure SQL Database:

- [Docelowy bufora pierścieniowego](https://msdn.microsoft.com/library/ff878182.aspx) — krótko przechowuje dane zdarzenia w pamięci.
- [Obiekt docelowy licznika zdarzeń](https://msdn.microsoft.com/library/ff878025.aspx) — zlicza wszystkie zdarzenia występujące podczas sesji zdarzeń rozszerzonych.
- [Docelowy plik zdarzeń](https://msdn.microsoft.com/library/ff878115.aspx) — zapisuje kompletne bufory do kontenera usługi Azure Storage.

Interfejs API [śledzenia zdarzeń systemu Windows (ETW)](https://msdn.microsoft.com/library/ms751538.aspx) nie jest dostępny dla zdarzeń rozszerzonych w Azure SQL Database.

## <a name="restrictions"></a>Ograniczenia

Istnieje kilka różnic związanych z zabezpieczeniami befitting środowisko chmury Azure SQL Database:

- Zdarzenia rozszerzone są oparte na modelu izolacji z jedną dzierżawą. Sesja zdarzeń w jednej bazie danych nie może uzyskać dostępu do danych ani zdarzeń z innej bazy danych.
- Nie można wydać instrukcji **Create Event Session** w kontekście bazy danych **Master** .

## <a name="permission-model"></a>Model uprawnień

Musisz mieć uprawnienie **Kontrola** w bazie danych, aby wystawić instrukcję **tworzenia sesji zdarzeń** . Właściciel bazy danych (dbo) ma uprawnienie **Kontrola** .

### <a name="storage-container-authorizations"></a>Autoryzacje kontenera magazynu

Token sygnatury dostępu współdzielonego generowany dla kontenera usługi Azure Storage musi określać **RWL** dla uprawnień. Wartość **RWL** zapewnia następujące uprawnienia:

- Odczyt
- Zapisywanie
- Lista

## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Istnieją scenariusze, w których intensywne wykorzystanie zdarzeń rozszerzonych może zbierać więcej aktywnych pamięci niż w dobrej kondycji dla całego systemu. W związku z tym Azure SQL Database dynamicznie ustawia i dostosowuje limity ilości aktywnej pamięci, która może być kumulowana przez sesję zdarzeń. Wiele czynników umożliwia przechodzenie do dynamicznego obliczania.

Jeśli zostanie wyświetlony komunikat o błędzie informujący, że maksymalna ilość pamięci została wymuszona, niektóre akcje naprawcze, które można wykonać, to:

- Uruchom mniejszą liczbę współbieżnych sesji zdarzeń.
- Za pomocą instrukcji **Create** i **ALTER** dla sesji zdarzeń Zmniejsz ilość pamięci określonej w klauzuli **Max \_ Memory** .

### <a name="network-latency"></a>Opóźnienie sieci

W **celu przechowywania danych w obiektach** BLOB usługi Azure Storage może wystąpić opóźnienie sieci lub błędy. Inne zdarzenia w Azure SQL Database mogą być opóźnione podczas oczekiwania na zakończenie komunikacji sieciowej. To opóźnienie może spowolnić obciążenie.

- Aby zmniejszyć ryzyko związane z wydajnością, należy unikać ustawiania opcji **EVENT_RETENTION_MODE** , aby **NO_EVENT_LOSS** w definicjach sesji zdarzeń.

## <a name="related-links"></a>Linki pokrewne

- [Używanie Azure PowerShell z usługą Azure Storage](/powershell/module/az.storage/).
- [Polecenia cmdlet usługi Azure Storage](https://docs.microsoft.com/powershell/module/Azure.Storage)
- [Używanie programu Azure PowerShell z usługą Azure Storage](/powershell/module/az.storage/)
- [Jak używać usługi BLOB Storage z platformy .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [CREATE CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx)
- [Utwórz sesję zdarzeń (Transact-SQL)](https://msdn.microsoft.com/library/bb677289.aspx)
- [Wpisy w blogu Jonathana Kehayias na temat zdarzeń rozszerzonych w Microsoft SQL Server](https://www.sqlskills.com/blogs/jonathan/category/extended-events/)
- Strona sieci Web *aktualizacji usługi* platformy Azure, zawężana przez parametr do Azure SQL Database:
  - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
