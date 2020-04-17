---
title: Tworzenie i szkolenie modelu predykcyjnego w R
titleSuffix: Azure SQL Database Machine Learning Services (preview)
description: Utwórz prosty model predykcyjny w języku R przy użyciu usług azure sql database machine learning services (wersja zapoznawcza), a następnie przewidzieć wynik przy użyciu nowych danych.
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
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460158"
---
# <a name="quickstart-create-and-train-a-predictive-model-in-r-with-azure-sql-database-machine-learning-services-preview"></a>Szybki start: tworzenie i szkolenie modelu predykcyjnego w języku R za pomocą usług Azure SQL Database Machine Learning Services (wersja zapoznawcza)

W tym przewodniku Szybki start można utworzyć i wyszkolić model predykcyjny przy użyciu języka R, zapisać model do tabeli w bazie danych, a następnie użyć modelu do przewidywania wartości z nowych danych przy użyciu usług uczenia maszynowego (z R) w bazie danych SQL azure.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Baza danych SQL platformy Azure](sql-database-single-database-get-started.md) z [regułą zapory na poziomie serwera](sql-database-server-level-firewall-rule.md)
- [Usługi uczenia maszynowego](sql-database-machine-learning-services-overview.md) z włączoną funkcją języka R.
- [Sql Server Management Studio](/sql/ssms/sql-server-management-studio-ssms) (SSMS)

W tym przykładzie użyto prostego modelu regresji do przewidywania drogi hamowania samochodu na podstawie prędkości przy użyciu zestawu danych **samochodów** dołączonego do języka R.

> [!TIP]
> Wiele zestawów danych są dołączone do środowiska wykonawczego R, aby uzyskać `library(help="datasets")` listę zainstalowanych zestawów danych, wpisz z wiersza polecenia R.

## <a name="create-and-train-a-predictive-model"></a>Tworzenie i szkolenie modelu predykcyjnego

Dane prędkości samochodu w zestawie **danych samochodów** zawiera dwie kolumny, zarówno numeryczne: **dist** i **prędkości**. Dane obejmują wiele obserwacji zatrzymania przy różnych prędkościach. Na podstawie tych danych utworzysz model regresji liniowej opisujący relację między prędkością samochodu a odległością wymaganą do zatrzymania samochodu.

Wymagania modelu liniowego są proste:
- Zdefiniuj formułę opisującą relację między zależną *zmienną prędkością* a niezależną *zmienną odległością*.
- Podaj dane wejściowe do użycia w uczenia modelu.

> [!TIP]
> Jeśli potrzebujesz odświeżacza w modelach liniowych, wypróbuj ten samouczek opisujący proces dopasowywania modelu za pomocą rxLinMod: [Dopasowanie modeli liniowych](https://docs.microsoft.com/machine-learning-server/r/how-to-revoscaler-linear-model)

W poniższych krokach skonfigurujesz dane szkoleniowe, utworzysz model regresji, przeszkolisz go przy użyciu danych szkoleniowych, a następnie zapiszesz model w tabeli SQL.

1. Otwórz program **SQL Server Management Studio** i nawiąż połączenie z usługą SQL Database.

   Jeśli potrzebujesz pomocy w nawiązywania połączenia, zobacz [Szybki start: Łączenie i wykonywanie zapytań o bazę danych SQL i wykonywanie zapytań o kwerendę .](sql-database-connect-query-ssms.md)

1. Utwórz tabelę **CarSpeed,** aby zapisać dane szkoleniowe.

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

1. Utwórz model regresji przy użyciu programu `rxLinMod`. 

   Aby utworzyć model, należy zdefiniować formułę wewnątrz kodu R, a następnie przekazać dane **szkoleniowe CarSpeed** jako parametr wejściowy.

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

1. Utwórz tabelę, w której można przechowywać model, dzięki czemu można go użyć później do prognozowania. 

   Dane wyjściowe pakietu R, który tworzy model jest zwykle **obiekt binarny**, więc tabela musi mieć kolumnę **VARBINARY(max)** typu.

    ```sql
    CREATE TABLE dbo.stopping_distance_models (
        model_name VARCHAR(30) NOT NULL DEFAULT('default model') PRIMARY KEY
        , model VARBINARY(max) NOT NULL
        );
    ```

1. Teraz wywołaj procedurę składowaną, wygeneruj model i zapisz go w tabeli.

   ```sql
   INSERT INTO dbo.stopping_distance_models (model)
   EXECUTE generate_linear_model;
   ```

   Należy pamiętać, że jeśli uruchomisz ten kod po raz drugi, otrzymasz następujący błąd:

   ```text
   Violation of PRIMARY KEY constraint...Cannot insert duplicate key in object bo.stopping_distance_models
   ```

   Jedną z opcji, aby uniknąć tego błędu jest zaktualizowanie nazwy dla każdego nowego modelu. Możesz na przykład zmienić nazwę na bardziej opisową i uwzględnić typ modelu, datę jego utworzenia i tak dalej.

   ```sql
   UPDATE dbo.stopping_distance_models
   SET model_name = 'rxLinMod ' + FORMAT(GETDATE(), 'yyyy.MM.HH.mm', 'en-gb')
   WHERE model_name = 'default model'
   ```

## <a name="view-the-table-of-coefficients"></a>Wyświetlanie tabeli współczynników

Ogólnie rzecz biorąc, dane wyjściowe języka R z procedury składowanej [sp_execute_external_script](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-execute-external-script-transact-sql) są ograniczone do jednej ramki danych. Jednak można zwracać dane wyjściowe innych typów, na przykład wartości skalarne, oprócz ramki danych.

Załóżmy na przykład, że chcesz trenować model, ale natychmiast wyświetlić tabelę współczynników z modelu. W tym celu należy utworzyć tabelę współczynników jako główny zestaw wyników i wyprowadzić przeszkolony model w zmiennej SQL. Można natychmiast ponownie użyć modelu, wywołując zmienną lub można zapisać model do tabeli, jak pokazano poniżej.

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

## <a name="score-new-data-using-the-trained-model"></a>Zdobywaj nowe dane przy użyciu wyszkolonego modelu

*Scoring* jest terminem używanym w analizie danych do oznaczania generowania prognoz, prawdopodobieństw lub innych wartości na podstawie nowych danych podawanych do wyszkolonego modelu. Użyjesz modelu utworzonego w poprzedniej sekcji, aby uzyskać punkty prognoz względem nowych danych.

Zwróć uwagę, że oryginalne dane szkoleniowe zatrzymują się na prędkości 25 mil na godzinę. To dlatego, że oryginalne dane były oparte na doświadczeniu z 1920 roku! Można się zastanawiać, jak długo zajęłoby samochód z 1920 roku, aby zatrzymać, jeśli może dostać się dzieje tak szybko, jak 60 mph lub nawet 100 mph? Aby odpowiedzieć na to pytanie, można podać kilka nowych wartości prędkości do modelu.

1. Utwórz tabelę z nowymi danymi prędkości.

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

2. Przewiduj drogę hamowania na podstawie tych nowych wartości prędkości.

   Ponieważ model jest oparty na algorytm **rxLinMod** pod warunkiem, jako część pakietu **RevoScaleR, wywołanie** `predict` [rxPredict](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxpredict) funkcji, a nie ogólnej funkcji R.

   W tym przykładzie skryptu:
   - Używa instrukcji SELECT, aby uzyskać pojedynczy model z tabeli
   - Przekazuje go jako parametr wejściowy
   - Wywołuje `unserialize` funkcję w modelu
   - Stosuje `rxPredict` funkcję z odpowiednimi argumentami do modelu
   - Dostarcza nowe dane wejściowe

   > [!TIP]
   > Aby uzyskać punktację w czasie rzeczywistym, zobacz [funkcje serializacji](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxserializemodel) dostarczane przez RevoScaleR.

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
> W tym przykładzie `str` skryptu funkcja jest dodawana podczas fazy testowania, aby sprawdzić schemat danych zwracanych z języka R. Instrukcję można usunąć później.
>
> Nazwy kolumn użytych w skrypcie języka R niekoniecznie są przekazywane do danych wyjściowych procedury składowanej. W tym miejscu klauzula WITH RESULTS definiuje niektóre nowe nazwy kolumn.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usług Azure SQL Database Machine Learning Services z R (wersja zapoznawcza), zobacz następujące artykuły.

- [Usługi azure SQL Database Machine Learning services z r (wersja zapoznawcza)](sql-database-machine-learning-services-overview.md)
- [Tworzenie i uruchamianie prostych skryptów języka R w usługach azure SQL Database Machine Learning Services (wersja zapoznawcza)](sql-database-quickstart-r-create-script.md)
- [Pisanie zaawansowanych funkcji języka R w bazie danych SQL usługi Azure przy użyciu usług uczenia maszynowego (wersja zapoznawcza)](sql-database-machine-learning-services-functions.md)
- [Praca z danymi języka R i SQL w usługach azure SQL Database Machine Learning Services (wersja zapoznawcza)](sql-database-machine-learning-services-data-issues.md)
