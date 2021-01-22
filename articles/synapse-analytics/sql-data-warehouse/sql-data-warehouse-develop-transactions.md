---
title: Korzystanie z transakcji w puli SQL usługi Azure Synapse Analytics
description: Ten artykuł zawiera wskazówki dotyczące implementowania transakcji i opracowywania rozwiązań w puli SQL Synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 03/22/2019
ms.author: xiaoyul
ms.custom: azure-synapse
ms.reviewer: igorstan
ms.openlocfilehash: 8144c588d4b6794cadc0577bf63dabc2cc3e0efd
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98677290"
---
# <a name="use-transactions-in-a-sql-pool-in-azure-synapse"></a>Używanie transakcji w puli SQL w usłudze Azure Synapse 

Ten artykuł zawiera wskazówki dotyczące implementowania transakcji i opracowywania rozwiązań w puli SQL.

## <a name="what-to-expect"></a>Czego oczekiwać

Zgodnie z oczekiwaniami Pula SQL obsługuje transakcje w ramach obciążenia magazynu danych. Aby jednak zapewnić, że Pula SQL jest utrzymywana na dużą skalę, niektóre funkcje są ograniczone w porównaniu do SQL Server. W tym artykule przedstawiono różnice.

## <a name="transaction-isolation-levels"></a>Poziomy izolacji transakcji

Pula SQL implementuje transakcje KWASowe. Poziom izolacji obsługi transakcyjnej jest domyślnie ODCZYTYWANy jako niezatwierdzony.  Można ją zmienić w celu odczytania ZATWIERDZONEj izolacji migawek przez READ_COMMITTED_SNAPSHOT włączenie opcji bazy danych dla puli SQL dla użytkownika w przypadku nawiązania połączenia z bazą danych Master.  

Po włączeniu wszystkie transakcje w tej bazie danych są wykonywane w ramach przekroczenia izolacji ZATWIERDZONEj migawki i ustawienie Odczytaj niezatwierdzone na poziomie sesji nie zostanie uznane. Sprawdź [Opcje ALTER DATABASE SET (Transact-SQL)](/sql/t-sql/statements/alter-database-transact-sql-set-options?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) , aby uzyskać szczegółowe informacje.

## <a name="transaction-size"></a>Rozmiar transakcji

Ograniczenie pojedynczej transakcji modyfikacji danych jest ograniczone. Limit jest stosowany do dystrybucji. W związku z tym łączne alokacje można obliczyć przez pomnożenie limitu przez liczbę dystrybucji.

Aby przybliżyć maksymalną liczbę wierszy w transakcji, Podziel limit dystrybucji o łączny rozmiar każdego wiersza. W przypadku kolumn o zmiennej długości należy wziąć pod uwagę średnią długość kolumny zamiast używać maksymalnego rozmiaru.

W poniższej tabeli zostały wykonane dwa założenia:

* Wystąpił parzysty rozkład danych
* Średnia długość wiersza to 250 bajtów.

## <a name="gen2"></a>Gen2

| [DWU](./sql-data-warehouse-overview-what-is.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) | Limit na dystrybucję (GB) | Liczba dystrybucji | Maksymalny rozmiar transakcji (GB) | Liczba wierszy na dystrybucję | Maksymalna liczba wierszy na transakcję |
| --- | --- | --- | --- | --- | --- |
| DW100c |1 |60 |60 |4 000 000 |240 000 000 |
| DW200c |1.5 |60 |90 |6 000 000 |360 000 000 |
| DW300c |2.25 |60 |135 |9 000 000 |540 000 000 |
| DW400c |3 |60 |180 |12 000 000 |720 000 000 |
| DW500c. |3,75 |60 |225 |15 000 000 |900 000 000 |
| DW1000c |7,5 |60 |450 |30 000 000 |1 800 000 000 |
| DW1500c |11,25 |60 |675 |45 000 000 |2 700 000 000 |
| DW2000c |15 |60 |900 |60 000 000 |3 600 000 000 |
| DW2500c |18,75 |60 |1125 |75 000 000 |4 500 000 000 |
| DW3000c |22,5 |60 |1 350 |90 000 000 |5 400 000 000 |
| DW5000c |37,5 |60 |2 250 |150 000 000 |9 000 000 000 |
| DW6000c |45 |60 |2 700 |180 000 000 |10 800 000 000 |
| DW7500c |56,25 |60 |3 375 |225 000 000 |13 500 000 000 |
| DW10000c |75 |60 |4500 |300 000 000 |18 000 000 000 |
| DW15000c |112,5 |60 |6750 |450 000 000 |27 000 000 000 |
| DW30000c |225 |60 |13 500 |900 000 000 |54 000 000 000 |

## <a name="gen1"></a>Gen1

| [DWU](./sql-data-warehouse-overview-what-is.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) | Limit na dystrybucję (GB) | Liczba dystrybucji | Maksymalny rozmiar transakcji (GB) | Liczba wierszy na dystrybucję | Maksymalna liczba wierszy na transakcję |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4 000 000 |240 000 000 |
| WARTOŚCI DW200 |1.5 |60 |90 |6 000 000 |360 000 000 |
| DW300 |2.25 |60 |135 |9 000 000 |540 000 000 |
| DW400 |3 |60 |180 |12 000 000 |720 000 000 |
| DW500 |3,75 |60 |225 |15 000 000 |900 000 000 |
| WARTOŚCI DW600 |4.5 |60 |270 |18 000 000 |1 080 000 000 |
| WARTOŚCI DW1000 |7,5 |60 |450 |30 000 000 |1 800 000 000 |
| DW1200 |9 |60 |540 |36 000 000 |2 160 000 000 |
| DW1500 |11,25 |60 |675 |45 000 000 |2 700 000 000 |
| DW2000 |15 |60 |900 |60 000 000 |3 600 000 000 |
| DW3000 |22,5 |60 |1 350 |90 000 000 |5 400 000 000 |
| DW6000 |45 |60 |2 700 |180 000 000 |10 800 000 000 |

Limit rozmiaru transakcji jest stosowany dla transakcji lub operacji. Nie jest on stosowany we wszystkich współbieżnych transakcjach. W związku z tym każda transakcja może zapisywać tę ilość danych w dzienniku.

Aby zoptymalizować i zminimalizować ilość danych zapisywana w dzienniku, zapoznaj się z artykułem [najlepsze rozwiązania](sql-data-warehouse-develop-best-practices-transactions.md) w zakresie transakcji.

> [!WARNING]
> Maksymalny rozmiar transakcji można osiągnąć tylko dla skrótów lub ROUND_ROBIN rozproszonych tabel, w których rozproszenie danych jest parzyste. Jeśli transakcja zapisuje dane w sposób skośny do dystrybucji, to limit jest prawdopodobnie osiągnięty przed maksymalnym rozmiarem transakcji.
> <!--REPLICATED_TABLE-->

## <a name="transaction-state"></a>Stan transakcji

Pula SQL używa funkcji XACT_STATE (), aby zgłosić nieudaną transakcję przy użyciu wartości-2. Ta wartość oznacza, że transakcja zakończyła się niepowodzeniem i jest oznaczona wyłącznie do wycofania.

> [!NOTE]
> Użycie-2 przez funkcję XACT_STATE, aby zauważyć, że nieudana transakcja reprezentuje inne zachowanie, aby SQL Server. SQL Server używa wartości-1 do reprezentowania transakcji niezatwierdzonej. SQL Server może tolerować błędy wewnątrz transakcji, bez konieczności oznaczania jej jako niezatwierdzonej. Na przykład spowodują `SELECT 1/0` wystąpienie błędu, ale nie wymusić transakcji w stanie niezatwierdzonym.

SQL Server również zezwala na odczyty w transakcji niezatwierdzonej. Jednak Pula SQL nie zezwala na to. Jeśli wystąpi błąd w transakcji puli SQL, zostanie automatycznie wprowadzony stan-2 i nie będzie można wykonać żadnych dalszych instrukcji SELECT, dopóki instrukcja nie zostanie wycofana.

W związku z tym ważne jest, aby sprawdzić, czy kod aplikacji, aby sprawdzić, czy używa XACT_STATE (), ponieważ może być konieczne wprowadzenie modyfikacji kodu.

Na przykład w SQL Server może zostać wyświetlona transakcja, która wygląda następująco:

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

Poprzedni kod daje następujący komunikat o błędzie:

Msg 111233, poziom 16, stan 1, wiersz 1 111233; Bieżąca transakcja została przerwana, a wszystkie oczekujące zmiany zostały wycofane. Przyczyną tego problemu jest to, że transakcja w stanie tylko wycofania nie jest jawnie wycofywana przed instrukcją DDL, DML lub SELECT.

Nie otrzymasz danych wyjściowych funkcji ERROR_ *.

W puli SQL kod musi być nieco zmieniony:

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

Oczekiwane zachowanie jest teraz obserwowane. Błąd w transakcji jest zarządzany, a ERROR_ * funkcje zapewniają wartości zgodnie z oczekiwaniami.

Wszystko, co zostało zmienione, polega na tym, że WYCOFANie transakcji musiało wystąpić przed odczytaniem informacji o błędzie w bloku CATCH.

## <a name="error_line-function"></a>Error_Line () — funkcja

Warto również zauważyć, że Pula SQL nie implementuje ani nie obsługuje funkcji ERROR_LINE (). Jeśli masz ten kod, musisz go usunąć, aby był zgodny z pulą SQL.

Zamiast zaimplementować równoważne funkcje, użyj etykiet zapytań w kodzie. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [etykiet](sql-data-warehouse-develop-label.md) .

## <a name="using-throw-and-raiserror"></a>Korzystanie z funkcji THROW i RAISERROR

THROW to bardziej nowoczesny implementacja do wywoływania wyjątków w puli SQL, ale również jest obsługiwana wartość RAISERROR. Istnieje jednak kilka różnic, które warto zwrócić uwagę.

* Liczby komunikatów o błędach zdefiniowane przez użytkownika nie mogą znajdować się w zakresie 100 000 – 150 000 dla THROW
* Komunikaty o błędach instrukcji RAISERROR są stałe o 50 000
* Użycie widoku sys. messages nie jest obsługiwane

## <a name="limitations"></a>Ograniczenia

Pula SQL zawiera kilka innych ograniczeń odnoszących się do transakcji.

Są one następujące:

* Brak transakcji rozproszonych
* Brak dozwolonych transakcji zagnieżdżonych
* Brak dozwolonych punktów zapisu
* Brak nazwanych transakcji
* Brak oznaczonych transakcji
* Brak obsługi języka DDL, takiego jak CREATE TABLE wewnątrz transakcji zdefiniowanej przez użytkownika

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat optymalizowania transakcji, zobacz [najlepsze rozwiązania](sql-data-warehouse-develop-best-practices-transactions.md)w zakresie transakcji. Aby dowiedzieć się więcej o najlepszych rozwiązaniach dotyczących puli SQL, zobacz [najlepsze rozwiązania dotyczące puli SQL](sql-data-warehouse-best-practices.md).