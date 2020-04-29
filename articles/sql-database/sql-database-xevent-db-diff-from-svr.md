---
title: Zdarzenia rozszerzone
description: Opisuje zdarzenia rozszerzone (XEvents) w Azure SQL Database oraz sposób różnicowania sesji zdarzeń w przypadku sesji zdarzeń w Microsoft SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: cb4eb4474ad074a3e69dc146c97b48d54343595b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79213959"
---
# <a name="extended-events-in-sql-database"></a>Zdarzenia rozszerzone w SQL Database
[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

W tym temacie wyjaśniono, w jaki sposób implementacja rozszerzonych zdarzeń w Azure SQL Database jest nieco różna w porównaniu z rozszerzonymi zdarzeniami w Microsoft SQL Server.

- SQL Database V12 zyskał funkcję zdarzeń rozszerzonych w drugiej połowie kalendarza 2015.
- SQL Server ma rozszerzone zdarzenia od 2008.
- Zestaw funkcji rozszerzonych zdarzeń na SQL Database jest niezawodnym podzbiorem funkcji na SQL Server.

*XEvents* to nieformalny pseudonim, który jest czasami używany dla "rozszerzonych zdarzeń" w blogach i innych nieformalnych lokalizacjach.

Dodatkowe informacje na temat zdarzeń rozszerzonych, dla Azure SQL Database i Microsoft SQL Server, są dostępne w:

- [Szybki start: rozszerzone zdarzenia w SQL Server](https://msdn.microsoft.com/library/mt733217.aspx)
- [Zdarzenia rozszerzone](https://msdn.microsoft.com/library/bb630282.aspx)

## <a name="prerequisites"></a>Wymagania wstępne

W tym temacie założono, że masz już pewną wiedzę na temat:

- [Usługa Azure SQL Database](https://azure.microsoft.com/services/sql-database/).
- [Rozszerzone zdarzenia](https://msdn.microsoft.com/library/bb630282.aspx) w Microsoft SQL Server.

- Obszerna dokumentacja dotycząca zdarzeń rozszerzonych dotyczy zarówno SQL Server, jak i SQL Database.

Przed wybraniem pliku zdarzenia jako [obiektu docelowego](#AzureXEventsTargets)pomocne jest wcześniejsze narażenie na następujące elementy:

- [Usługa Azure Storage](https://azure.microsoft.com/services/storage/)


- PowerShell
    - [Używanie Azure PowerShell z usługą Azure Storage](../storage/common/storage-powershell-guide-full.md) — zawiera wyczerpujące informacje na temat programu PowerShell i usługi Azure Storage.

## <a name="code-samples"></a>Przykłady kodu

Tematy pokrewne zawierają dwa przykłady kodu:


- [Kod docelowy buforu pierścieniowego dla zdarzeń rozszerzonych w SQL Database](sql-database-xevent-code-ring-buffer.md)
    - Krótki prosty skrypt języka Transact-SQL.
    - Podkreślamy w temacie przykładowy kod, który po zakończeniu pracy z obiektem docelowym bufora pierścieniowego należy zwolnić jego zasoby przez wykonanie instrukcji ALTER-Drop `ALTER EVENT SESSION ... ON DATABASE DROP TARGET ...;` . Później można dodać kolejne wystąpienie buforu pierścienia przez `ALTER EVENT SESSION ... ON DATABASE ADD TARGET ...`.


- [Kod docelowy pliku zdarzeń dla zdarzeń rozszerzonych w SQL Database](sql-database-xevent-code-event-file.md)
    - Faza 1 to program PowerShell do utworzenia kontenera usługi Azure Storage.
    - Faza 2 to Transact-SQL, który używa kontenera usługi Azure Storage.

## <a name="transact-sql-differences"></a>Różnice w języku Transact-SQL


- Gdy wykonujesz polecenie [Utwórz sesję zdarzenia](https://msdn.microsoft.com/library/bb677289.aspx) na SQL Server, użyj klauzuli **on serwera** . Ale na SQL Database zamiast tego używasz klauzuli **on Database** .


- Klauzula for **Database** dotyczy również poleceń [ALTER Event Session](https://msdn.microsoft.com/library/bb630368.aspx) i [Drop Event](https://msdn.microsoft.com/library/bb630257.aspx) .


- Najlepszym rozwiązaniem jest dołączenie opcji sesji zdarzeń **STARTUP_STATE = on** w instrukcji **Create Event Session** lub **ALTER Event Session** .
    - Wartość **= on** obsługuje automatyczne ponowne uruchamianie po ponownym skonfigurowaniu logicznej bazy danych z powodu przejścia w tryb failover.

## <a name="new-catalog-views"></a>Nowe widoki wykazu

Funkcja zdarzeń rozszerzonych jest obsługiwana przez kilka [widoków wykazu](https://msdn.microsoft.com/library/ms174365.aspx). Widoki wykazu informują o *metadanych lub definicjach* sesji zdarzeń utworzonych przez użytkownika w bieżącej bazie danych. Widoki nie zwracają informacji o wystąpieniach aktywnych sesji zdarzeń.

| Nazwa<br/>Widok wykazu | Opis |
|:--- |:--- |
| **sys. database_event_session_actions** |Zwraca wiersz dla każdej akcji w każdym zdarzeniu sesji zdarzeń. |
| **sys. database_event_session_events** |Zwraca wiersz dla każdego zdarzenia w sesji zdarzeń. |
| **sys. database_event_session_fields** |Zwraca wiersz dla każdej kolumny dostosowania, która została jawnie ustawiona dla zdarzeń i elementów docelowych. |
| **sys. database_event_session_targets** |Zwraca wiersz dla każdego obiektu docelowego zdarzenia dla sesji zdarzeń. |
| **sys. database_event_sessions** |Zwraca wiersz dla każdej sesji zdarzeń w bazie danych SQL Database. |

W Microsoft SQL Server podobne widoki wykazu mają nazwy, które obejmują *. serwer\_ * zamiast *. baza danych\_*. Wzorzec nazwy jest podobny do wykazu **sys. server_event_%**.

## <a name="new-dynamic-management-views-dmvs"></a>Nowe dynamiczne widoki zarządzania [(widoków DMV)](https://msdn.microsoft.com/library/ms188754.aspx)

Azure SQL Database ma [dynamiczne widoki zarządzania (widoków DMV)](https://msdn.microsoft.com/library/bb677293.aspx) , które obsługują zdarzenia rozszerzone. Widoków DMV informacje o *aktywnych* sesjach zdarzeń.

| Nazwa DMV | Opis |
|:--- |:--- |
| **sys. dm_xe_database_session_event_actions** |Zwraca informacje o akcjach sesji zdarzeń. |
| **sys. dm_xe_database_session_events** |Zwraca informacje o zdarzeniach sesji. |
| **sys. dm_xe_database_session_object_columns** |Pokazuje wartości konfiguracji dla obiektów, które są powiązane z sesją. |
| **sys. dm_xe_database_session_targets** |Zwraca informacje o celach docelowych sesji. |
| **sys. dm_xe_database_sessions** |Zwraca wiersz dla każdej sesji zdarzeń, który jest objęty zakresem bieżącej bazy danych. |

W Microsoft SQL Server podobne widoki wykazu są nazwane bez części nazwy * \_bazy danych* , takiej jak:

- **sys. dm_xe_sessions**, a nie nazwa<br/>**sys. dm_xe_database_sessions**.

### <a name="dmvs-common-to-both"></a>Widoków DMV wspólne dla obu
W przypadku zdarzeń rozszerzonych istnieją dodatkowe widoków DMV, które są wspólne dla obu Azure SQL Database i Microsoft SQL Server:

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

## <a name="targets-for-your-sql-database-event-sessions"></a>Cele dla sesji zdarzeń SQL Database

Poniżej znajdują się elementy docelowe, które mogą przechwytywać wyniki z sesji zdarzeń na SQL Database:

- [Docelowy bufora pierścieniowego](https://msdn.microsoft.com/library/ff878182.aspx) — krótko przechowuje dane zdarzenia w pamięci.
- [Obiekt docelowy licznika zdarzeń](https://msdn.microsoft.com/library/ff878025.aspx) — zlicza wszystkie zdarzenia występujące podczas sesji zdarzeń rozszerzonych.
- [Docelowy plik zdarzeń](https://msdn.microsoft.com/library/ff878115.aspx) — zapisuje kompletne bufory do kontenera usługi Azure Storage.

Interfejs API [śledzenia zdarzeń systemu Windows (ETW)](https://msdn.microsoft.com/library/ms751538.aspx) nie jest dostępny dla zdarzeń rozszerzonych w SQL Database.

## <a name="restrictions"></a>Ograniczenia

Istnieje kilka różnic związanych z zabezpieczeniami befitting środowisko chmury SQL Database:

- Zdarzenia rozszerzone są oparte na modelu izolacji z jedną dzierżawą. Sesja zdarzeń w jednej bazie danych nie może uzyskać dostępu do danych ani zdarzeń z innej bazy danych.
- Nie można wydać instrukcji **Create Event Session** w kontekście bazy danych **Master** .

## <a name="permission-model"></a>Model uprawnień

Musisz mieć uprawnienie **Kontrola** w bazie danych, aby wystawić instrukcję **tworzenia sesji zdarzeń** . Właściciel bazy danych (dbo) ma uprawnienie **Kontrola** .

### <a name="storage-container-authorizations"></a>Autoryzacje kontenera magazynu

Token sygnatury dostępu współdzielonego generowany dla kontenera usługi Azure Storage musi określać **RWL** dla uprawnień. Wartość **RWL** zapewnia następujące uprawnienia:

- Odczyt
- Zapisywanie
- List

## <a name="performance-considerations"></a>Zagadnienia dotyczące wydajności

Istnieją scenariusze, w których intensywne wykorzystanie zdarzeń rozszerzonych może zbierać więcej aktywnych pamięci niż w dobrej kondycji dla całego systemu. W związku z tym system Azure SQL Database dynamicznie ustawia i dostosowuje limity ilości aktywnej pamięci, która może być kumulowana przez sesję zdarzeń. Wiele czynników umożliwia przechodzenie do dynamicznego obliczania.

Jeśli zostanie wyświetlony komunikat o błędzie informujący, że maksymalna ilość pamięci została wymuszona, niektóre akcje naprawcze, które można wykonać, to:

- Uruchom mniejszą liczbę współbieżnych sesji zdarzeń.
- Za pomocą instrukcji **Create** i **ALTER** dla sesji zdarzeń Zmniejsz ilość pamięci określonej w klauzuli **Max\_Memory** .

### <a name="network-latency"></a>Opóźnienie sieci

W **celu przechowywania danych w obiektach** BLOB usługi Azure Storage może wystąpić opóźnienie sieci lub błędy. Inne zdarzenia w SQL Database mogą być opóźnione podczas oczekiwania na zakończenie komunikacji sieciowej. To opóźnienie może spowolnić obciążenie.

- Aby zmniejszyć ryzyko związane z wydajnością, należy unikać ustawiania opcji **EVENT_RETENTION_MODE** , aby **NO_EVENT_LOSS** w definicjach sesji zdarzeń.

## <a name="related-links"></a>Powiązane linki

- [Używanie Azure PowerShell z usługą Azure Storage](../storage/common/storage-powershell-guide-full.md).
- [Polecenia cmdlet usługi Azure Storage](https://docs.microsoft.com/powershell/module/Azure.Storage)
- [Używanie Azure PowerShell z usługą Azure Storage](../storage/common/storage-powershell-guide-full.md) — zawiera wyczerpujące informacje na temat programu PowerShell i usługi Azure Storage.
- [Jak używać usługi BLOB Storage z platformy .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [CREATE CREDENTIAL (Transact-SQL)](https://msdn.microsoft.com/library/ms189522.aspx)
- [Utwórz sesję zdarzeń (Transact-SQL)](https://msdn.microsoft.com/library/bb677289.aspx)
- [Wpisy w blogu Jonathana Kehayias na temat zdarzeń rozszerzonych w Microsoft SQL Server](https://www.sqlskills.com/blogs/jonathan/category/extended-events/)


- Strona sieci Web *aktualizacji usługi* platformy Azure, zawężana przez parametr do Azure SQL Database:
    - [https://azure.microsoft.com/updates/?service=sql-database](https://azure.microsoft.com/updates/?service=sql-database)


Inne tematy przykładowe kodu dla zdarzeń rozszerzonych są dostępne na następujących łączach. Należy jednak regularnie sprawdzać dowolną próbkę, aby zobaczyć, czy Przykładowe elementy docelowe Microsoft SQL Server, a nie Azure SQL Database. Następnie możesz zdecydować, czy niewielkie zmiany są potrzebne do uruchomienia przykładu.

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
