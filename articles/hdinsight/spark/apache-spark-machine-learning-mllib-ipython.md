---
title: Przykład uczenia maszynowego z spark MLlib na hdinsight — Azure
description: Dowiedz się, jak używać platformy Spark MLlib do tworzenia aplikacji uczenia maszynowego, która analizuje zestaw danych przy użyciu klasyfikacji za pomocą regresji logistycznej.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 04/16/2020
ms.openlocfilehash: 26695df299ba5d0f50c8f271b5da99284a8d6764
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81531137"
---
# <a name="use-apache-spark-mllib-to-build-a-machine-learning-application-and-analyze-a-dataset"></a>Tworzenie aplikacji uczenia maszynowego i analizowanie zestawu danych za pomocą aplikacji Apache Spark MLlib

Dowiedz się, jak utworzyć aplikację uczenia maszynowego za pomocą aplikacji Apache Spark [MLlib.](https://spark.apache.org/mllib/) Aplikacja wykona analizę predykcyjną na otwartym zestawie danych. Z wbudowanych bibliotek uczenia maszynowego platformy Spark w tym przykładzie użyto *klasyfikacji* za pomocą regresji logistycznej.

MLlib to podstawowa biblioteka platformy Spark, która udostępnia wiele narzędzi przydatnych do zadań uczenia maszynowego, takich jak:

* Klasyfikacja
* Regresja
* Klastrowanie
* Modelowanie
* Rozkład wartości pojedynczej (SVD) i analiza głównego komponentu (PCA)
* Badanie hipotez i obliczanie przykładowych statystyk

## <a name="understand-classification-and-logistic-regression"></a>Zrozumienie klasyfikacji i regresji logistycznej

*Klasyfikacja*, popularne zadanie uczenia maszynowego, to proces sortowania danych wejściowych na kategorie. Zadaniem algorytmu klasyfikacji jest ustalenie, jak przypisać "etykiety" do danych wejściowych, które podasz. Na przykład można pomyśleć o algorytmie uczenia maszynowego, który akceptuje informacje giełdowe jako dane wejściowe. Następnie dzieli akcje na dwie kategorie: akcje, które należy sprzedać i akcje, które należy zachować.

Regresja logistyczna jest algorytmem używanym do klasyfikacji. Interfejs API regresji logistycznej platformy Spark jest przydatny w *klasyfikacji binarnej*lub klasyfikacji danych wejściowych do jednej z dwóch grup. Aby uzyskać więcej informacji na temat regresji logistycznych, zobacz [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

Podsumowując, proces regresji logistycznej tworzy *funkcję logistyczną*. Funkcja służy do przewidywania prawdopodobieństwa, że wektor wejściowy należy do jednej lub drugiej grupy.  

## <a name="predictive-analysis-example-on-food-inspection-data"></a>Przykład analizy predykcyjnej na temat danych z inspekcji żywności

W tym przykładzie używasz spark do przeprowadzenia analizy predykcyjnej danych inspekcji żywności (**Food_Inspections1.csv**). Dane uzyskane za pośrednictwem [portalu danych City of Chicago](https://data.cityofchicago.org/). Ten zestaw danych zawiera informacje o inspekcjach zakładów spożywczych, które zostały przeprowadzone w Chicago. W tym informacje o każdym zakładzie, stwierdzeniu naruszeń (jeśli występują) i wynikach kontroli. Plik danych CSV jest już dostępny na koncie magazynu skojarzonym z klastrem pod adresem **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

W poniższych krokach opracowywać model, aby zobaczyć, co trzeba, aby przejść lub nie inspekcji żywności.

## <a name="create-an-apache-spark-mllib-machine-learning-app"></a>Tworzenie aplikacji do uczenia maszynowego Apache Spark MLlib

1. Utwórz notes Jupyter przy użyciu jądra PySpark. Aby uzyskać instrukcje, zobacz [Tworzenie notesu Jupyter](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook).

2. Importuj typy wymagane dla tej aplikacji. Skopiuj i wklej następujący kod do pustej komórki, a następnie naciśnij **klawisze SHIFT + ENTER**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    ```

    Ze względu na jądro PySpark nie trzeba tworzyć żadnych kontekstów jawnie. Konteksty Spark i Hive są tworzone automatycznie po uruchomieniu pierwszej komórki kodu.

## <a name="construct-the-input-dataframe"></a>Konstruowanie wejściowej ramki danych

Użyj kontekstu Iskra, aby wyciągnąć nieprzetworzone dane CSV do pamięci jako tekst nieustrukturyzowany. Następnie użyj biblioteki CSV języka Python, aby przeanalizować każdy wiersz danych.

1. Uruchom następujące wiersze, aby utworzyć odporny rozproszony zestaw danych (RDD) przez importowanie i analizowanie danych wejściowych.

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

2. Uruchom następujący kod, aby pobrać jeden wiersz z RDD, dzięki czemu można spojrzeć schematu danych:

    ```PySpark
    inspections.take(1)
    ```

    Dane wyjściowe wyglądają następująco:

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

    Dane wyjściowe daje wyobrażenie o schemacie pliku wejściowego. Zawiera nazwę każdego zakładu i rodzaj zakładu. Również adres, dane z inspekcji i lokalizacja, między innymi.

3. Uruchom następujący kod, aby utworzyć ramkę danych *(df)* i tabelę tymczasową *(CountResults)* z kilkoma kolumnami, które są przydatne w analizie predykcyjnej. `sqlContext`służy do przekształcenia danych strukturalnych.

    ```PySpark
    schema = StructType([
    StructField("id", IntegerType(), False),
    StructField("name", StringType(), False),
    StructField("results", StringType(), False),
    StructField("violations", StringType(), True)])

    df = spark.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
    df.registerTempTable('CountResults')
    ```

    Cztery kolumny zainteresowania w ramce danych to **identyfikator,** **nazwa,** **wyniki**i **naruszenia.**

4. Uruchom następujący kod, aby uzyskać małą próbkę danych:

    ```PySpark
    df.show(5)
    ```

    Dane wyjściowe wyglądają następująco:

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

Zacznijmy, aby zorientować się, co zawiera zestaw danych. 

1. Uruchom następujący kod, aby wyświetlić różne wartości w kolumnie **wyników:**

    ```PySpark
    df.select('results').distinct().show()
    ```

    Dane wyjściowe wyglądają następująco:

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

2. Uruchom następujący kod, aby wizualizować rozkład tych wyników:

    ```PySpark
    %%sql -o countResultsdf
    SELECT COUNT(results) AS cnt, results FROM CountResults GROUP BY results
    ```

    Magia, `%%sql` `-o countResultsdf` po której następuje zapewnia, że dane wyjściowe kwerendy jest zachowywane lokalnie na serwerze Jupyter (zazwyczaj headnode klastra). Dane wyjściowe są zachowywane jako [pandas](https://pandas.pydata.org/) dataframe o określonej nazwie **countResultsdf**. Aby uzyskać więcej `%%sql` informacji na temat magii i innych magii dostępnych w jądrze PySpark, zobacz [Jądra dostępne w notebookach Jupyter z klastrami Apache Spark HDInsight](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

    Dane wyjściowe wyglądają następująco:

    ![Dane wyjściowe kwerendy SQL](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-query-output.png "Dane wyjściowe kwerendy SQL")

3. Do utworzenia wykresu można również użyć [matplotlib](https://en.wikipedia.org/wiki/Matplotlib), biblioteki używanej do konstruowania wizualizacji danych. Ponieważ wykres musi być utworzony z lokalnie utrwalonej elementu dataframe **countResultsdf,** fragment kodu musi zaczynać się od `%%local` magii. Ta akcja gwarantuje, że kod jest uruchamiany lokalnie na serwerze Jupyter.

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

    Aby przewidzieć wynik inspekcji żywności, należy opracować model oparty na naruszeniach. Ponieważ regresja logistyczna jest metodą klasyfikacji binarnej, warto podzielić dane wynikowe na dwie kategorie: **Niepowodzenie** i **Przekaż:**

   - Przekazać
       - Przekazać
       - Pass w / warunki
   - Niepowodzenie
       - Niepowodzenie
   - Odrzuć
       - Firma nie zlokalizowana
       - Z działalności

     Dane z innymi wynikami ("Business Not Located" lub "Out of Business") nie są przydatne i mimo to stanowią niewielki procent wyników.

4. Uruchom następujący kod, aby przekonwertować istniejącą ramkę danych(`df`) na nową ramkę danych, w której każda inspekcja jest reprezentowana jako para naruszeń etykiet. W takim przypadku etykieta `0.0` reprezentuje błąd, `1.0` etykieta reprezentuje sukces, `-1.0` a etykieta reprezentuje niektóre wyniki oprócz tych dwóch wyników.

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

5. Uruchom następujący kod, aby wyświetlić jeden wiersz danych oznaczonych etykietą:

    ```PySpark
    labeledData.take(1)
    ```

    Dane wyjściowe wyglądają następująco:

    ```
    [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]
    ```

## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Tworzenie modelu regresji logistycznej na podstawie wejściowej ramki danych

Ostatnim zadaniem jest przekonwertowanie oznaczonych danych. Przekonwertować dane na format, który może być analizowany przez regresję logistyczną. Dane wejściowe do algorytmu regresji logistycznej wymagają zestawu *par wektorowych funkcji etykiet.* Gdzie "wektor funkcji" jest wektorem liczb, które reprezentują punkt wejściowy. Tak, trzeba przekonwertować "naruszenia" kolumny, która jest częściowo ustrukturyzowana i zawiera wiele komentarzy w wolnym tekście. Konwertuj kolumnę na tablicę liczb rzeczywistych, które maszyna może łatwo zrozumieć.

Jednym ze standardowych metod uczenia maszynowego do przetwarzania języka naturalnego jest przypisanie każdemu odrębnemu wyrazowi "indeksu". Następnie przekaż wektor do algorytmu uczenia maszynowego. W taki sposób, że wartość każdego indeksu zawiera względną częstotliwość tego wyrazu w ciągu tekstowym.

MLlib zapewnia łatwy sposób, aby wykonać tę operację. Najpierw "tokenize" każdy ciąg naruszeń, aby uzyskać poszczególne słowa w każdym ciągu. Następnie należy `HashingTF` użyć do konwersji każdego zestawu tokenów do wektora funkcji, które następnie mogą być przekazywane do algorytmu regresji logistycznej do konstruowania modelu. Wszystkie te kroki można wykonać w kolejności za pomocą "potoku".

```PySpark
tokenizer = Tokenizer(inputCol="violations", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(labeledData)
```

## <a name="evaluate-the-model-using-another-dataset"></a>Ocenianie modelu przy użyciu innego zestawu danych

Model został utworzony wcześniej, aby *przewidzieć,* jakie będą wyniki nowych inspekcji. Prognozy są oparte na naruszeniach, które zostały zaobserwowane. Przeszkoliłeś ten model w zestawie danych **Food_Inspections1.csv**. Do *oceny* siły tego modelu na nowych danych można użyć drugiego zestawu danych **Food_Inspections2.csv.** Ten drugi zestaw danych (**Food_Inspections2.csv**) znajduje się w domyślnym kontenerze magazynu skojarzonym z klastrem.

1. Uruchom następujący kod, aby utworzyć nową ramkę danych, **predictionsDf,** która zawiera przewidywanie generowane przez model. Fragment kodu tworzy również tabelę tymczasową o nazwie **Prognozy** na podstawie dataframe.

    ```PySpark
    testData = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                .map(csvParse) \
                .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
    testDf = spark.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
    predictionsDf = model.transform(testDf)
    predictionsDf.registerTempTable('Predictions')
    predictionsDf.columns
    ```

    Powinien zostać wyświetlony wynik podobny do następującego tekstu:

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

1. Spójrz na jedną z prognoz. Uruchom ten fragment kodu:

    ```PySpark
    predictionsDf.take(1)
    ```

   Istnieje przewidywanie dla pierwszego wpisu w zestawie danych testowych.

1. Metoda `model.transform()` stosuje tę samą transformację do wszystkich nowych danych z tego samego schematu i dojść do przewidywania, jak klasyfikować dane. Możesz zrobić kilka statystyk, aby zorientować się, jak przewidywanie było:

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

    Za pomocą regresji logistycznej z Spark daje model relacji między opisami naruszeń w języku angielskim. I czy dany biznes przejdzie lub nie kontroli żywności.

## <a name="create-a-visual-representation-of-the-prediction"></a>Tworzenie wizualnej reprezentacji prognozowania

Teraz można skonstruować wizualizację końcową, aby pomóc w uzasadnieniu wyników tego testu.

1. Można rozpocząć od wyodrębnienia różnych prognoz i wyników z **przewidywanie** tabeli tymczasowej utworzone wcześniej. Poniższe kwerendy oddzielają dane wyjściowe jako *true_positive,* *false_positive,* *true_negative*i *false_negative*. W poniższych zapytaniach można wyłączyć wizualizację przy użyciu, `-q` a `-o`także zapisać dane wyjściowe (za `%%local` pomocą ) jako ramki danych, które mogą być następnie używane z magii.

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

1. Na koniec użyj następującego fragmentu kodu, aby wygenerować wykres za pomocą **matplotlib**.

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

    ![Wyniki wyjściowe aplikacji uczenia maszynowego platformy Spark — wykres kołowy procentowy nieudanych inspekcji żywności.](./media/apache-spark-machine-learning-mllib-ipython/spark-machine-learning-result-output-2.png "Wyniki rezultatu uczenia maszynowego platformy Spark")

    Na tym wykresie "pozytywny" wynik odnosi się do nieudanej kontroli żywności, podczas gdy wynik negatywny odnosi się do uchwalonej inspekcji.

## <a name="shut-down-the-notebook"></a>Zamykanie notesu

Po zakończeniu uruchamiania aplikacji należy zamknąć notes, aby zwolnić zasoby. W tym celu w menu **File** (Plik) w notesie wybierz pozycję **Close and Halt** (Zamknij i zatrzymaj). Ta akcja powoduje zatrzymanie i zamknięcie notesu.

## <a name="next-steps"></a>Następne kroki

* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenariusze

* [Apache Spark z analizą danych BI: Interaktywna analiza danych przy użyciu platformy Spark w funkcji HDInsight za pomocą narzędzi analizy biznesowej](apache-spark-use-bi-tools.md)
* [Apache Spark z uczeniem maszynowym: użyj platformy Spark w hdinsight do analizowania temperatury budynku przy użyciu danych HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Analiza dziennika strony internetowej przy użyciu platformy Apache Spark w hdinsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Tworzenie i uruchamianie aplikacji

* [Tworzenie autonomicznych aplikacji przy użyciu języka Scala](apache-spark-create-standalone-application.md)
* [Zdalne uruchamianie zadań w klastrze Apache Spark przy użyciu programu Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Narzędzia i rozszerzenia

* [Tworzenie i przesyłanie aplikacji Spark Scala przy użyciu dodatku HDInsight Tools Plugin for IntelliJ IDEA](apache-spark-intellij-tool-plugin.md)
* [Użyj wtyczki HDInsight Tools dla IntelliJ IDEA do zdalnego debugowania aplikacji Apache Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Używanie notebooków Apache Zeppelin z klastrem Apache Spark w programie HDInsight](apache-spark-zeppelin-notebook.md)
* [Jądra dostępne dla notebooka Jupyter w klastrze Apache Spark dla usługi HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Zarządzanie zasobami

* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
