---
title: Używanie transakcji w puli SQL Synapse
description: Ten artykuł zawiera wskazówki dotyczące implementowania transakcji i tworzenia rozwiązań w puli sql synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 03/22/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 558b16fc348728c507af1fa0260a67ccacefed0f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416146"
---
# <a name="use-transactions-in-synapse-sql-pool"></a>Używanie transakcji w puli SQL Synapse

Ten artykuł zawiera wskazówki dotyczące implementowania transakcji i tworzenia rozwiązań w puli SQL.

## <a name="what-to-expect"></a>Czego się spodziewać

Zgodnie z oczekiwaniami pula SQL obsługuje transakcje w ramach obciążenia magazynu danych. Jednak aby upewnić się, że pula SQL jest utrzymywana na dużą skalę, niektóre funkcje są ograniczone w porównaniu do programu SQL Server. W tym artykule przedstawiono różnice.

## <a name="transaction-isolation-levels"></a>Poziomy izolacji transakcji

Pula SQL implementuje transakcje ACID. Poziom izolacji obsługi transakcyjnej jest domyślnie do odczytu niezatwierdzone.  Można go zmienić na ODCZYT COMMITTED MIGAWKI IZOLACJI, włączając opcję READ_COMMITTED_SNAPSHOT bazy danych dla bazy danych użytkownika po połączeniu z główną bazą danych.  

Po włączeniu wszystkie transakcje w tej bazie danych są wykonywane w obszarze IZOLACJA MIGAWKI ODCZYTU POPEŁNIONE I ustawienie ODCZYTU NIEZATWIERDZONE na poziomie sesji nie będą honorowane. Szczegółowe informacje można [znaleźć w opcji ZESTAWU ALTER DATABASE SET (Transact-SQL).](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)

## <a name="transaction-size"></a>Rozmiar transakcji

Pojedyncza transakcja modyfikacji danych jest ograniczona rozmiarem. Limit jest stosowany dla rozkładu. W związku z tym całkowita alokacja może być obliczana przez pomnożenie limitu przez liczbę dystrybucji.

Aby przybliżyć maksymalną liczbę wierszy w transakcji, należy podzielić limit dystrybucji przez całkowity rozmiar każdego wiersza. W przypadku kolumn o zmiennej długości należy rozważyć zastosowanie średniej długości kolumny, a nie przy użyciu maksymalnego rozmiaru.

W poniższej tabeli przyjęto dwa założenia:

* Doszło do równomiernego rozkładu danych
* Średnia długość wiersza wynosi 250 bajtów

## <a name="gen2"></a>Gen2

| [DWU](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Limit na dystrybucję (GB) | Liczba dystrybucji | MAKSYMALNY rozmiar transakcji (GB) | # Wiersze na dystrybucję | Maksymalna liczba wierszy na transakcję |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4,000,000 |240,000,000 |
| DW200c |1.5 |60 |90 |6 000 000 |360,000,000 |
| DW300c |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400c |3 |60 |180 |12,000,000 |720,000,000 |
| DW500c. |3,75 |60 |225 |15,000,000 |900,000,000 |
| DW1000c |7,5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1500c |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000c |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW2500c |18.75 |60 |1125 |75,000,000 |4,500,000,000 |
| DW3000c |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW5000c |37.5 |60 |2,250 |150,000,000 |9,000,000,000 |
| DW6000c |45 |60 |2,700 |180,000,000 |10,800,000,000 |
| DW7500c |56.25 |60 |3,375 |225,000,000 |13,500,000,000 |
| DW10000c |75 |60 |4500 |300 000 000 |18,000,000,000 |
| DW15000c |112.5 |60 |6750 |450,000,000 |27,000,000,000 |
| DW30000c |225 |60 |13,500 |900,000,000 |54,000,000,000 |

## <a name="gen1"></a>Gen1

| [DWU](../../sql-data-warehouse/sql-data-warehouse-overview-what-is.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Limit na dystrybucję (GB) | Liczba dystrybucji | MAKSYMALNY rozmiar transakcji (GB) | # Wiersze na dystrybucję | Maksymalna liczba wierszy na transakcję |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4,000,000 |240,000,000 |
| DW200 (dw200) |1.5 |60 |90 |6 000 000 |360,000,000 |
| DW300 (dw300) |2.25 |60 |135 |9,000,000 |540,000,000 |
| DW400 ( DW400 ) |3 |60 |180 |12,000,000 |720,000,000 |
| DW500 |3,75 |60 |225 |15,000,000 |900,000,000 |
| DW600 ( DW600 ) |4.5 |60 |270 |18,000,000 |1,080,000,000 |
| DW1000 |7,5 |60 |450 |30,000,000 |1,800,000,000 |
| DW1200 |9 |60 |540 |36,000,000 |2,160,000,000 |
| DW1500 |11.25 |60 |675 |45,000,000 |2,700,000,000 |
| DW2000 |15 |60 |900 |60,000,000 |3,600,000,000 |
| DW3000 |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW6000 |45 |60 |2,700 |180,000,000 |10,800,000,000 |

Limit rozmiaru transakcji jest stosowany dla każdej transakcji lub operacji. Nie jest stosowany we wszystkich równoczesnych transakcji. W związku z tym każda transakcja może zapisać tę ilość danych w dzienniku.

Aby zoptymalizować i zminimalizować ilość danych zapisanych w dzienniku, zapoznaj się z [transactions best practices](sql-data-warehouse-develop-best-practices-transactions.md) artykułu.

> [!WARNING]
> Maksymalny rozmiar transakcji można osiągnąć tylko dla hash lub ROUND_ROBIN tabel rozproszonych, gdzie rozprzestrzenianie danych jest równe. Jeśli transakcja zapisuje dane w sposób skośny do dystrybucji, a następnie limit prawdopodobnie zostanie osiągnięty przed maksymalny rozmiar transakcji.
> <!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>Stan transakcji

Pula SQL używa funkcji XACT_STATE() do zgłaszania nieudanej transakcji przy użyciu wartości -2. Ta wartość oznacza, że transakcja nie powiodła się i jest oznaczona tylko do wycofywania.

> [!NOTE]
> Użycie -2 przez funkcję XACT_STATE do oznaczania nieudanej transakcji reprezentuje inne zachowanie do programu SQL Server. SQL Server używa wartości -1 do reprezentowania transakcji nieprzywiązywalne. SQL Server może tolerować niektóre błędy wewnątrz transakcji bez konieczności być oznaczone jako nieprzymiotalne. Na przykład `SELECT 1/0` może spowodować błąd, ale nie wymusić transakcji w stanie nieprzywiązywalne.

SQL Server zezwala również odczyty w transakcji nieprzywiązywalne. Jednak pula SQL nie pozwala to zrobić. Jeśli wystąpi błąd wewnątrz transakcji puli SQL, automatycznie przejdzie do stanu -2 i nie będzie można dokonać żadnych dalszych instrukcji select, dopóki instrukcja nie zostanie wycofana.

W związku z tym należy sprawdzić, czy kod aplikacji, aby sprawdzić, czy używa XACT_STATE(), jak może być konieczne, aby wprowadzić modyfikacje kodu.

Na przykład w programie SQL Server może zostać wyświetlenie transakcji wygląda następująco:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Poprzedni kod zawiera następujący komunikat o błędzie:

msg 111233, poziom 16, stan 1, linia 1 111233; Bieżąca transakcja została przerwana, a wszelkie oczekujące zmiany zostały wycofane. Przyczyną tego problemu jest to, że transakcja w stanie tylko do wycofywania nie jest jawnie przywracane przed DDL, DML lub SELECT instrukcji.

Nie otrzymasz danych wyjściowych funkcji ERROR_*.

W puli SQL kod musi być nieznacznie zmieniony:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        IF @@TRANCOUNT > 0
        BEGIN
            ROLLBACK TRAN;
            PRINT 'ROLLBACK';
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Oczekiwane zachowanie jest teraz obserwowane. Błąd w transakcji jest zarządzany, a funkcje ERROR_* zapewniają wartości zgodnie z oczekiwaniami.

Wszystko, co się zmieniło jest to, że wycofywanie transakcji musiało się zdarzyć przed odczytem informacji o błędzie w bloku CATCH.

## <a name="error_line-function"></a>Error_Line() , funkcja

Warto również zauważyć, że pula SQL nie implementuje ani nie obsługuje funkcji ERROR_LINE(). Jeśli masz to w kodzie, należy usunąć go, aby były zgodne z puli SQL.

Zamiast tego użyj etykiet zapytań w kodzie, aby zaimplementować równoważne funkcje. Aby uzyskać więcej informacji, zobacz artykuł [LABEL.](sql-data-warehouse-develop-label.md)

## <a name="using-throw-and-raiserror"></a>Korzystanie z THROW i RAISERROR

THROW jest bardziej nowoczesna implementacja do podnoszenia wyjątków w puli SQL, ale RAISERROR jest również obsługiwany. Istnieje kilka różnic, które warto zwrócić uwagę na jednak.

* Numery komunikatów o błędach zdefiniowanych przez użytkownika nie mogą znajdować się w zakresie 100 000 - 150 000 dla throw
* Komunikaty o błędach RAISERROR są stałe na 50 000
* Korzystanie z pliku sys.messages nie jest obsługiwane

## <a name="limitations"></a>Ograniczenia

Pula SQL ma kilka innych ograniczeń, które odnoszą się do transakcji.

Są one następujące:

* Brak transakcji rozproszonych
* Nie dozwolona jest transakcja zagnieżdżona
* Nie dozwolona liczba punktów zapisu
* Brak nazwanych transakcji
* Brak oznaczonych transakcji
* Brak obsługi DDL, takich jak TWORZENIE TABELI wewnątrz transakcji zdefiniowanej przez użytkownika

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat optymalizacji transakcji, zobacz [Najważniejsze wskazówki dotyczące transakcji](sql-data-warehouse-develop-best-practices-transactions.md). Aby dowiedzieć się więcej o innych najlepszych rozwiązaniach dotyczących puli SQL, zobacz [Najważniejsze wskazówki dotyczące puli SQL](sql-data-warehouse-best-practices.md).
