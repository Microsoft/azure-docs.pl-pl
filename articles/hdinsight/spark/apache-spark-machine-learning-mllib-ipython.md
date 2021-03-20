---
title: Przykład uczenia maszynowego przy użyciu platformy Spark MLlib w usłudze HDInsight — Azure
description: Dowiedz się, jak za pomocą usługi Spark MLlib utworzyć aplikację Machine Learning, która analizuje zestaw danych przy użyciu klasyfikacji w ramach regresji logistycznej.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020, devx-track-python
ms.date: 04/27/2020
ms.openlocfilehash: d2054058bb00b0801aa1c3694c73b6a2edb46c80
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98930053"
---
# <a name="use-apache-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Użyj Apache Spark MLlib, aby skompilować aplikację uczenia maszynowego i analizować zestaw danych

Dowiedz się, jak za pomocą programu Apache Spark MLlib utworzyć aplikację uczenia maszynowego. Aplikacja będzie przeprowadzać analizę predykcyjną w otwartym zestawie danych. W przypadku wbudowanych bibliotek uczenia maszynowego platformy Spark ten przykład używa *klasyfikacji* za pośrednictwem regresji logistycznej.

MLlib to podstawowa Biblioteka platformy Spark, która udostępnia wiele narzędzi użytecznych w przypadku zadań uczenia maszynowego, takich jak:

* Klasyfikacja
* Regresja
* Klastrowanie
* Modelowanie
* Wieloskładnikowa dekompozycja wartości (SVD) i podstawowa analiza składników (PPW)
* Testowanie hipotez i Obliczanie statystyk przykładowych

## <a name="understand-classification-and-logistic-regression"></a>Zrozumienie klasyfikacji i regresji logistycznej

*Klasyfikacja*, popularne zadanie uczenia maszynowego, to proces sortowania danych wejściowych do kategorii. Jest to zadanie algorytmu klasyfikacji, aby ustalić sposób przypisywania "etykiet" do danych wejściowych dostarczanych przez użytkownika. Można na przykład traktować algorytm uczenia maszynowego, który akceptuje informacje o zapasach jako dane wejściowe. Następnie dzieli zapas na dwie kategorie: zasoby, które należy sprzedać i zasoby, które powinny być przechowywane.

Regresja logistyczna to algorytm używany do klasyfikacji. Interfejs API regresji logistycznej platformy Spark jest przydatny w przypadku *klasyfikacji binarnej* lub klasyfikowania danych wejściowych do jednej z dwóch grup. Aby uzyskać więcej informacji na temat regresji logistycznej, zobacz [witrynę Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

Podsumowując, proces regresji logistycznej produkuje *funkcję logistyczną*. Funkcja służy do przewidywania prawdopodobieństwa, że wektor wejściowy należy do jednej grupy lub drugiej.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Przykład analizy predykcyjnej na danych inspekcji żywności

W tym przykładzie używasz platformy Spark do analizy predykcyjnej danych inspekcji żywności (**Food_Inspections1.csv**). Dane nabyte w [mieście portalu danych w Chicago](https://data.cityofchicago.org/). Ten zestaw danych zawiera informacje o inspekcjach związanych z działalnością żywnościową, które zostały przeprowadzone w Chicago. W tym informacje dotyczące każdego zakładu, wykryte naruszenia (jeśli istnieją) oraz wyniki inspekcji. Plik danych CSV jest już dostępny na koncie magazynu skojarzonym z klastrem w **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

W poniższych krokach opracowujesz model, aby zobaczyć, co jest potrzebne do przekazania lub niepowodzenia inspekcji żywności.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Tworzenie aplikacji Apache Spark MLlib Machine Learning

1. Utwórz Jupyter Notebook przy użyciu jądra PySpark. Aby uzyskać instrukcje, zobacz [Tworzenie pliku Jupyter Notebook](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook-file).

2. Zaimportuj typy wymagane dla tej aplikacji. Skopiuj i wklej następujący kod do pustej komórki, a następnie naciśnij klawisze **SHIFT + ENTER**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```

    Ze względu na jądro PySpark nie trzeba jawnie tworzyć kontekstów. Konteksty Spark i Hive są tworzone automatycznie po uruchomieniu pierwszej komórki kodu.

## <a name="construct-the-input-dataframe"></a>Konstruowanie wejściowej ramki danych

Użyj kontekstu Spark, aby ściągnąć nieprzetworzone dane CSV do pamięci jako tekst bez struktury. Następnie użyj biblioteki CSV języka Python, aby przeanalizować każdy wiersz danych.

1. Uruchom następujące wiersze, aby utworzyć rozproszony zestaw danych (RDD), importując i przeanalizować dane wejściowe.

    ```PySpark
    def csvParse(s):
        import csv
        from StringIO import StringIO
        sio = StringIO(s)
        value = csv.reader(sio).next()
        sio.close()
        return value

    inspections = sc.textFile('/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                    .map(csvParse)
    ```

2. Uruchom Poniższy kod, aby pobrać jeden wiersz z RDD, więc możesz zajrzeć do schematu danych:

    ```PySpark
    inspections.take(1)
    ```

    Dane wyjściowe są następujące:

    ```
    [['413707',
        'LUNA PARK INC',
        'LUNA PARK  DAY CARE',
        '2049789',
        "Children's Services Facility",
        'Risk 1 (High)',
        '3250 W FOSTER AVE ',
        'CHICAGO',
        'IL',
        '60625',
        '09/21/2010',
        'License-Task Force',
        'Fail',
        '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
        '41.97583445690982',
        '-87.7107455232781',
        '(41.97583445690982, -87.7107455232781)']]
    ```

    Dane wyjściowe dają pomysł na Schemat pliku wejściowego. Zawiera nazwę każdego zakładu i typ zakładu. Również adres, dane inspekcji i lokalizacja, między innymi.

3. Uruchom Poniższy kod, aby utworzyć element Dataframe (*DF*) i tabelę tymczasową (*CountResults*) z kilkoma kolumnami, które są przydatne do analizy predykcyjnej. `sqlContext` służy do przekształcania danych ze strukturą.

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])

    df = spark.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    Cztery kolumny zainteresowania w ramce Dataframe są **identyfikatorami**, **nazwami**, **wynikami** i **naruszeniami**.

4. Uruchom następujący kod, aby uzyskać mały przykład danych:

    ```PySpark
    df.show(5)
    ```

    Dane wyjściowe są następujące:

    ```
    +------+--------------------+-------+--------------------+
    |    id|                name|results|          violations|
    +------+--------------------+-------+--------------------+
    |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
    |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
    |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
    |413708|BENCHMARK HOSPITA...|   Pass|                    |
    |413722|           JJ BURGER|   Pass|                    |
    +------+--------------------+-------+--------------------+
    ```

## <a name="understand-the-data"></a>Zrozumienie danych

Zacznijmy od tego, co zawiera zestaw danych. 

1. Uruchom następujący kod, aby wyświetlić różne wartości w kolumnie **wyniki** :

    ```PySpark
    df.select('results').distinct().show()
    ```

    Dane wyjściowe są następujące:

    ```
    +--------------------+
    |             results|
    +--------------------+
    |                Fail|
    |Business Not Located|
    |                Pass|
    |  Pass w/ Conditions|
    |     Out of Business|
    +--------------------+
    ```

2. Uruchom następujący kod, aby wizualizować dystrybucję następujących wyników:

    ```PySpark
    %%sql -o countResultsdf
    SELECT COUNT(results) AS cnt, results FROM CountResults GROUP BY results
    ```

    Magiczna Metoda polega na `%%sql` `-o countResultsdf` tym, że dane wyjściowe zapytania są utrwalane lokalnie na serwerze Jupyter (zazwyczaj węzła głównego klastra). Dane wyjściowe są utrwalane jako ramka [dataPandas](https://pandas.pydata.org/) o określonej nazwie **countResultsdf**. Aby uzyskać więcej informacji na temat `%%sql` Magic i innych magicznych PySpark jądra, zobacz [jądra dostępne w notesach Jupyter z klastrami usługi HDInsight Apache Spark](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

    Dane wyjściowe są następujące:

    ![Dane wyjściowe zapytania SQL](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "Dane wyjściowe zapytania SQL")

3. Możesz również użyć matplotlib, biblioteki używanej do konstruowania wizualizacji danych, aby utworzyć wykres. Ponieważ wykres musi być tworzony na podstawie lokalnie utrwalonej ramki Dataframe **countResultsdf** , fragment kodu musi rozpoczynać się od `%%local` Magic. Ta akcja zapewnia, że kod jest uruchamiany lokalnie na serwerze Jupyter.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = countResultsdf['results']
    sizes = countResultsdf['cnt']
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    Aby przewidzieć wynik inspekcji żywności, należy opracować model na podstawie naruszeń. Ponieważ regresja logistyczna jest metodą klasyfikacji binarnej, warto grupować dane wynikowe w dwie kategorie: **Niepowodzenie** i **przekazywanie**:

   - Zdane
       - Zdane
       - Przekazuj/warunki
   - Niepowodzenie
       - Niepowodzenie
   - Odrzuć
       - Nie znaleziono firmy
       - Poza firmą

     Dane z innymi wynikami ("firma nie znajduje się" lub "poza firmą") nie są przydatne i składają się na niewielką część wyników.

4. Uruchom Poniższy kod, aby przekonwertować istniejący element Dataframe ( `df` ) do nowej ramki danych, w której każda inspekcja jest reprezentowana jako para naruszenia etykiet. W tym przypadku etykieta `0.0` reprezentuje błąd, etykieta `1.0` reprezentuje sukces, a etykieta `-1.0` przedstawia niektóre wyniki oprócz tych dwóch wyników.

    ```PySpark
    def labelForResults(s):
        if s == 'Fail':
            return 0.0
        elif s == 'Pass w/ Conditions' or s == 'Pass':
            return 1.0
        else:
            return -1.0
    label = UserDefinedFunction(labelForResults, DoubleType())
    labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')
    ```

5. Uruchom następujący kod, aby wyświetlić jeden wiersz danych z etykietą:

    ```PySpark
    labeledData.take(1)
    ```

    Dane wyjściowe są następujące:

    ```
    [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]
    ```

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Tworzenie modelu regresji logistycznej na podstawie wejściowej ramki danych

Ostatnim zadaniem jest Konwertowanie danych z etykietami. Przekonwertuj dane do formatu, który może być analizowany przez regresję logistyczną. Wejście do algorytmu regresji logistycznej wymaga zestawu *par wektorów funkcji etykiet*. Gdzie "wektor funkcji" jest wektorem liczb reprezentujących punkt wejściowy. W związku z tym należy przekonwertować kolumnę "naruszenia", która jest częściową strukturą i zawiera wiele komentarzy w tekście swobodnym. Przekonwertuj kolumnę na tablicę liczb rzeczywistych, którą maszyna może łatwo zrozumieć.

Jedną z standardowych podejścia do uczenia maszynowego do przetwarzania języka naturalnego jest przypisanie każdego wyrazu DISTINCT "index". Następnie Przekaż wektor do algorytmu uczenia maszynowego. W taki sposób, że każdy indeks zawiera względną częstotliwość tego wyrazu w ciągu tekstowym.

MLlib zapewnia łatwy sposób wykonania tej operacji. Najpierw "tokenize" każdy ciąg naruszeń, aby pobrać pojedyncze słowa w każdym ciągu. Następnie należy użyć funkcji, `HashingTF` Aby skonwertować każdy zestaw tokenów do wektora funkcji, który można następnie przesłać do algorytmu regresji logistycznej w celu skonstruowania modelu. Wszystkie te kroki są wykonywane w sekwencji przy użyciu "potoku".

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>Oceń model przy użyciu innego zestawu danych

Możesz użyć utworzonego wcześniej modelu, aby *przewidzieć* wyniki nowych inspekcji. Przewidywania opierają się na naruszeniach naruszeń. Ten model został przeszkolony na **Food_Inspections1.csv** zestawu danych. Aby *oszacować* siłę tego modelu dla nowych danych, można użyć drugiego zestawu danych **Food_Inspections2.csv**. Ten drugi zestaw danych (**Food_Inspections2.csv**) znajduje się w domyślnym kontenerze magazynu skojarzonym z klastrem.

1. Uruchom następujący kod, aby utworzyć nową ramkę danych, **predictionsDf** , która zawiera prognozę wygenerowaną przez model. Fragment kodu tworzy również tabelę tymczasową o nazwie **przewidywania** na podstawie ramki Dataframe.

    ```PySpark
    testData = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                .map(csvParse) \
                .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
    testDf = spark.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
    predictionsDf = model.transform(testDf)
    predictionsDf.registerTempTable('Predictions')
    predictionsDf.columns
    ```

    Powinny pojawić się dane wyjściowe podobne do następującego tekstu:

    ```
    ['id',
        'name',
        'results',
        'violations',
        'words',
        'features',
        'rawPrediction',
        'probability',
        'prediction']
    ```

1. Zapoznaj się z jednym z prognoz. Uruchom następujący fragment kodu:

    ```PySpark
    predictionsDf.take(1)
    ```

   Istnieje Prognoza dotycząca pierwszego wpisu w zestawie danych testowych.

1. `model.transform()`Metoda stosuje te same przekształcenia do nowych danych z tym samym schematem i dociera do prognozowania sposobu klasyfikowania danych. Aby uzyskać informacje na temat tego, jak przewidywania były następujące:

    ```PySpark
    numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR
                                            (prediction = 1 AND (results = 'Pass' OR
                                                                results = 'Pass w/ Conditions'))""").count()
    numInspections = predictionsDf.count()

    print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
    print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"
    ```

    Dane wyjściowe wyglądają następująco:

    ```
    There were 9315 inspections and there were 8087 successful predictions
    This is a 86.8169618894% success rate
    ```

    Użycie regresji logistycznej z platformą Spark zapewnia model relacji między opisami naruszeń w języku angielskim. I czy dana działalność powinna zostać przekazana lub przeprowadzona niepowodzeniem w przypadku inspekcji żywności.

## <a name="create-a-visual-representation-of-the-prediction"></a>Utwórz wizualną reprezentację przewidywania

Teraz możesz skonstruować ostateczną wizualizację, aby pomóc Ci w podaniu wyników testu.

1. Zacznij od wyodrębnienia różnych prognoz i **wyników z tabeli tymczasowej** utworzone wcześniej. Poniższe zapytania oddzielają dane wyjściowe jako *true_positive*, *false_positive*, *true_negative* i *false_negative*. W poniższych zapytaniach można wyłączyć wizualizację za pomocą programu `-q` , a także zapisać dane wyjściowe (przy użyciu `-o` ) jako ramki danych, które mogą być następnie używane z `%%local` magiczną.

    ```PySpark
    %%sql -q -o true_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_positive
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

    ```PySpark
    %%sql -q -o true_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'
    ```

    ```PySpark
    %%sql -q -o false_negative
    SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions')
    ```

1. Na koniec użyj poniższego fragmentu kodu, aby wygenerować wykres przy użyciu **matplotlib**.

    ```PySpark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    labels = ['True positive', 'False positive', 'True negative', 'False negative']
    sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
    colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
    plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
    plt.axis('equal')
    ```

    Powinny zostać wyświetlone następujące dane wyjściowe:

    ![Wyjście aplikacji Spark Machine Learning — wartość procentowa dla wykresu kołowego niepowodzenia inspekcji żywności.](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Dane wyjściowe wyniku uczenia maszynowego platformy Spark")

    Na tym wykresie wynik "pozytywny" odnosi się do nieudanej inspekcji żywności, natomiast wynik negatywny odnosi się do przeprowadzonej inspekcji.

## <a name="shut-down-the-notebook"></a>Zamknij Notes

Po zakończeniu działania aplikacji należy zamknąć Notes, aby zwolnić zasoby. W tym celu w menu **File** (Plik) w notesie wybierz pozycję **Close and Halt** (Zamknij i zatrzymaj). Ta akcja powoduje zatrzymanie i zamknięcie notesu.

## <a name="next-steps"></a>Następne kroki

* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)
* [Analiza dzienników witryny sieci Web przy użyciu Apache Spark w usłudze HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Microsoft Cognitive Toolkit model uczenia głębokiego za pomocą usługi Azure HDInsight](apache-spark-microsoft-cognitive-toolkit.md)
