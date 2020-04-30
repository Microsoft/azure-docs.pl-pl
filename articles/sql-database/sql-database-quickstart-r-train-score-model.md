---
title: Tworzenie i uczenie modelu predykcyjnego w języku R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Utwórz prosty model predykcyjny w języku R przy użyciu Azure SQL Database Machine Learning Services (wersja zapoznawcza), a następnie przeprzewidywania wyniku przy użyciu nowych danych.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: r
ms.topic: quickstart
author: garyericson
ms.author: garye
ms.reviewer: davidph
manager: cgronlun
ms.date: 04/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 3c88bdf141e7784837a89c8104574d97c93296dc
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81460158"
---
# <a name="quickstart-create-and-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Szybki Start: Tworzenie i uczenie modelu predykcyjnego w języku R z Azure SQL Database Machine Learning Services (wersja zapoznawcza)

W tym przewodniku szybki start utworzysz i nauczysz model predykcyjny przy użyciu języka R, Zapisz model w tabeli w bazie danych, a następnie użyj modelu do przewidywania wartości z nowych danych przy użyciu Machine Learning Services (R) w Azure SQL Database.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Baza danych Azure SQL Database](sql-database-single-database-get-started.md) z [regułą zapory na poziomie serwera](sql-database-server-level-firewall-rule.md)
- [Machine Learning Services](sql-database-machine-learning-services-overview.md) z włączonym językiem R.
- [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

W tym przykładzie użyto prostego modelu regresji do przewidywania odległości zatrzymywania samochodu w oparciu o szybkość przy użyciu zestawu danych **samochodów** zawartych w języku R.

> [!TIP]
> Wiele zestawów danych jest dołączonych do środowiska uruchomieniowego języka R, aby uzyskać listę zainstalowanych zestawów danych, `library(help="datasets")` wpisz z wiersza polecenia języka r.

## <a name="create-and-train-a-predictive-model"></a>Tworzenie i uczenie modelu predykcyjnego

Dane dotyczące szybkości samochodu w zestawie danych **samochodów** zawierają dwie **kolumny: wartość** numeryczna i **szybkość**. Dane obejmują wiele zatrzymywania obserwacji o różnych szybkościach. Z tych danych utworzysz model regresji liniowej, który opisuje relację między szybkością samochodu i odległością wymaganą do zatrzymania samochodu.

Wymagania modelu liniowego są proste:
- Zdefiniuj formułę opisującą relację między zależną *szybkością* zmiennej i niezależną *odległością*zmiennej.
- Podaj dane wejściowe do użycia w uczenia modelu.

> [!TIP]
> Jeśli potrzebujesz odświeżacza dla modeli liniowych, Wypróbuj ten samouczek, w którym opisano proces instalowania modelu przy użyciu rxLinMod: [Dopasowywanie modeli liniowych](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

W poniższych krokach skonfigurujesz dane szkoleniowe, tworzysz model regresji, wyszkolesz go przy użyciu danych szkoleniowych, a następnie zapiszesz model w tabeli SQL.

1. Otwórz program **SQL Server Management Studio** i nawiąż połączenie z usługą SQL Database.

   Jeśli potrzebujesz pomocy przy nawiązywaniu połączenia, zobacz [Szybki Start: użyj SQL Server Management Studio, aby nawiązać połączenie i wysłać zapytanie do bazy danych Azure SQL](sql-database-connect-query-ssms.md).

1. Utwórz tabelę **CarSpeed** , aby zapisać dane szkoleniowe.

    ```sql
    CREATE TABLE dbo.CarSpeed (
        speed INT NOT NULL
        , distance INT NOT NULL
        )
    GO
    
    INSERT INTO dbo.CarSpeed (
        speed
        , distance
        )
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'car_speed <- cars;'
        , @input_data_1 = N''
        , @output_data_1_name = N'car_speed'
    GO
    ```

1. Utwórz model regresji przy `rxLinMod`użyciu. 

   Aby skompilować model, należy zdefiniować formułę w kodzie R, a następnie przekazać dane szkoleniowe **CarSpeed** jako parametr wejściowy.

    ```sql
    DROP PROCEDURE IF EXISTS generate_linear_model;
    GO
    CREATE PROCEDURE generate_linear_model
    AS
    BEGIN
      EXECUTE sp_execute_external_script
      @language = N'R'
      , @script = N'
    lrmodel <- rxLinMod(formula = distance ~ speed, data = CarsData);
    trained_model <- data.frame(payload = as.raw(serialize(lrmodel, connection=NULL)));
    '
      , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
      , @input_data_1_name = N'CarsData'
      , @output_data_1_name = N'trained_model'
      WITH RESULT SETS ((model VARBINARY(max)));
    END;
    GO
    ```

     Pierwszy argument funkcji rxLinMod to parametr *formula*, który definiuje odległość jako zależną od prędkości. Dane wejściowe są przechowywane w zmiennej `CarsData`, która jest wypełniana przez zapytanie SQL.

1. Utwórz tabelę, w której przechowujesz model, aby można było go używać później do prognozowania. 

   Wyjście pakietu języka R, który tworzy model, jest zazwyczaj **obiektem binarnym**, dlatego tabela musi mieć kolumnę typu **varbinary (max)** .

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
        );
    ```

1. Teraz Wywołaj procedurę składowaną, wygeneruj model i Zapisz go w tabeli.

   ```sql
   INSERT INTO dbo.stopping_distance_models (model)
   EXECUTE generate_linear_model;
   ```

   Należy pamiętać, że jeśli uruchomisz ten kod po raz drugi, otrzymasz następujący błąd:

   ```text
   Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object bo.stopping_distance_models
   ```

   Jedną z opcji, aby uniknąć tego błędu, jest aktualizacja nazwy dla każdego nowego modelu. Możesz na przykład zmienić nazwę na bardziej opisową i uwzględnić typ modelu, datę jego utworzenia i tak dalej.

   ```sql
   UPDATE dbo.stopping_distance_models
   SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
   WHERE model_name = 'default model'
   ```

## <a name="view-the-table-of-coefficients"></a>Wyświetl tabelę współczynników

Ogólnie rzecz biorąc, dane wyjściowe języka R z procedury składowanej [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) są ograniczone do jednej ramki danych. Jednak można zwracać dane wyjściowe innych typów, na przykład wartości skalarne, oprócz ramki danych.

Załóżmy na przykład, że chcesz nauczyć model, ale natychmiast wyświetlić tabelę współczynników z modelu. W tym celu należy utworzyć tabelę współczynników jako główny zestaw wyników i wyprowadzić przeszkolony model w zmiennej SQL. Możesz natychmiast ponownie użyć modelu przez wywołanie zmiennej lub zapisać model w tabeli, jak pokazano poniżej.

```sql
DECLARE @model VARBINARY(max)
    , @modelname VARCHAR(30)

EXECUTE sp_execute_external_script @language = N'R'
    , @script = N'
speedmodel <- rxLinMod(distance ~ speed, CarsData)
modelbin <- serialize(speedmodel, NULL)
OutputDataSet <- data.frame(coefficients(speedmodel));
'
    , @input_data_1 = N'SELECT [speed], [distance] FROM CarSpeed'
    , @input_data_1_name = N'CarsData'
    , @params = N'@modelbin varbinary(max) OUTPUT'
    , @modelbin = @model OUTPUT
WITH RESULT SETS(([Coefficient] FLOAT NOT NULL));

-- Save the generated model
INSERT INTO dbo.stopping_distance_models (
    model_name
    , model
    )
VALUES (
    'latest model'
    , @model
    )
```

**Wyniki**

![Przeszkolony model z dodatkowymi danymi wyjściowymi](./media/sql-database-quickstart-r-train-score-model/r-train-model-with-additional-output.png)

## <a name="score-new-data-using-the-trained-model"></a>Ocena nowych danych przy użyciu przeszkolonego modelu

*Ocenianie* to termin używany w nauce danych do oznaczania generowania prognoz, prawdopodobieństwa lub innych wartości na podstawie nowych danych podawanych do przeszkolonego modelu. Model utworzony w poprzedniej sekcji służy do oceny prognoz dotyczących nowych danych.

Zwróć uwagę, że oryginalne dane szkoleniowe zatrzymują się na prędkości 25 mil na godzinę. To dlatego, że oryginalne dane były oparte na doświadczeniu z 1920 roku! Możesz się zastanawiać, jak długo zajmiemy się działaniem urządzenia przenośnego z 1920s, aby zatrzymać je tak szybko, jak 60 mph, a nawet 100 mph? Aby odpowiedzieć na to pytanie, możesz podać nowe wartości szybkości dla modelu.

1. Utwórz tabelę z nową szybkością danych.

   ```sql
    CREATE TABLE dbo.NewCarSpeed (
        speed INT NOT NULL
        , distance INT NULL
        )
    GO
    
    INSERT dbo.NewCarSpeed (speed)
    VALUES (40)
        , (50)
        , (60)
        , (70)
        , (80)
        , (90)
        , (100)
   ```

2. Przewidywanie odległości zatrzymywania od tych nowych wartości prędkości.

   Ponieważ model jest oparty na algorytmie **rxLinMod** dostarczonym jako część pakietu **kolekcję funkcji revoscaler** , należy wywołać funkcję [RxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict) zamiast ogólnej funkcji języka R `predict` .

   Ten przykładowy skrypt:
   - Używa instrukcji SELECT, aby uzyskać jeden model z tabeli
   - Przekazuje go jako parametr wejściowy
   - Wywołuje `unserialize` funkcję w modelu
   - Stosuje `rxPredict` funkcję z odpowiednimi argumentami do modelu.
   - Udostępnia nowe dane wejściowe

   > [!TIP]
   > W przypadku oceniania w czasie rzeczywistym zobacz [funkcje serializacji](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) udostępniane przez kolekcję funkcji revoscaler.

   ```sql
    DECLARE @speedmodel VARBINARY(max) = (
            SELECT model
            FROM dbo.stopping_distance_models
            WHERE model_name = 'latest model'
            );
    
    EXECUTE sp_execute_external_script @language = N'R'
        , @script = N'
    current_model <- unserialize(as.raw(speedmodel));
    new <- data.frame(NewCarData);
    predicted.distance <- rxPredict(current_model, new);
    str(predicted.distance);
    OutputDataSet <- cbind(new, ceiling(predicted.distance));
    '
        , @input_data_1 = N'SELECT speed FROM [dbo].[NewCarSpeed]'
        , @input_data_1_name = N'NewCarData'
        , @params = N'@speedmodel varbinary(max)'
        , @speedmodel = @speedmodel
    WITH RESULT SETS((
                new_speed INT
                , predicted_distance INT
                ));
   ```

   **Wyniki**

   ![Zestaw wyników dla przewidywania drogi zatrzymania](./media/sql-database-quickstart-r-train-score-model/r-predict-stopping-distance-resultset.png)

> [!NOTE]
> W tym przykładowym skrypcie `str` funkcja jest dodawana podczas fazy testowania, aby sprawdzić schemat danych zwracanych z języka R. Instrukcję można usunąć później.
>
> Nazwy kolumn użytych w skrypcie języka R niekoniecznie są przekazywane do danych wyjściowych procedury składowanej. W tym miejscu klauzula WITH RESULTS definiuje kilka nowych nazw kolumn.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na Azure SQL Database Machine Learning Services z językiem R (wersja zapoznawcza), zobacz następujące artykuły.

- [Azure SQL Database Machine Learning Services z językiem R (wersja zapoznawcza)](sql-database-machine-learning-services-overview.md)
- [Twórz i uruchamiaj proste skrypty języka R w Azure SQL Database Machine Learning Services (wersja zapoznawcza)](sql-database-quickstart-r-create-script.md)
- [Zapisuj zaawansowane funkcje języka R w Azure SQL Database przy użyciu Machine Learning Services (wersja zapoznawcza)](sql-database-machine-learning-services-functions.md)
- [Pracuj z danymi języka R i SQL w Azure SQL Database Machine Learning Services (wersja zapoznawcza)](sql-database-machine-learning-services-data-issues.md)
