---
title: 'Samouczek: Tworzenie aplikacji Spark Machine Learning — Azure HDInsight'
description: Samouczek — instrukcje krok po kroku dotyczące tworzenia Apache Spark aplikacji Machine Learning w klastrach usługi HDInsight Spark przy użyciu Jupyter Notebook.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc, devx-track-python
ms.date: 04/07/2020
ms.openlocfilehash: 88a71eb5a8ca9a6f2bf1d17fc4eaf9780527d2cb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930542"
---
# <a name="tutorial-build-an-apache-spark-machine-learning-application-in-azure-hdinsight"></a>Samouczek: Tworzenie aplikacji Apache Spark Machine Learning w usłudze Azure HDInsight

W tym samouczku dowiesz się, jak utworzyć aplikację uczenia maszynowego platformy [Apache Spark](./apache-spark-overview.md) dla usługi Azure HDInsight za pomocą środowiska [Jupyter Notebook](https://jupyter.org/).

[MLlib](https://spark.apache.org/docs/latest/ml-guide.html) to biblioteka uczenia maszynowego platformy Spark, składająca się z typowych algorytmów uczenia i narzędzi. (Klasyfikacja, regresja, klastrowanie, filtrowanie do współpracy i zmniejszanie liczby wymiarów). Również bazowe elementy podstawowe optymalizacji.)

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:
> [!div class="checklist"]
> * Tworzenie aplikacji uczenia maszynowego platformy Apache Spark

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Spark w usłudze HDInsight. Zobacz [Tworzenie klastra Apache Spark](./apache-spark-jupyter-spark-sql-use-portal.md).

* Znajomość zagadnień dotyczących używania notesów Jupyter za pomocą platformy Spark w usłudze HDInsight. Aby uzyskać więcej informacji, zobacz [ładowanie danych i uruchamianie zapytań za pomocą Apache Spark w usłudze HDInsight](./apache-spark-load-data-run-query.md).

## <a name="understand-the-data-set"></a>Opis zestawu danych

Aplikacja używa przykładowych danych **HVAC.csv** , które są domyślnie dostępne we wszystkich klastrach. Plik znajduje się w lokalizacji `\HdiSamples\HdiSamples\SensorSampleData\hvac` . Dane dotyczą temperatury docelowej i temperatury rzeczywistej niektórych budynków, wyposażonych w instalacje grzewczo-wentylacyjne (HVAC). Kolumna **System** zawiera identyfikatory systemów, a kolumna **SystemAge** — liczbę lat użytkowania instalacji grzewczo-wentylacyjnej w danym budynku. Można przewidzieć, czy kompilacja będzie hotter czy chłodna na podstawie temperatury docelowej, danego identyfikatora systemu i wieku systemu.

![Migawka danych używanych na potrzeby przykładowego uczenia maszynowego Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png "Migawka danych używanych na potrzeby przykładowego uczenia maszynowego Spark")

## <a name="develop-a-spark-machine-learning-application-using-spark-mllib"></a>Tworzenie aplikacji uczenia maszynowego platformy Spark przy użyciu biblioteki MLLib platformy Spark

Ta aplikacja używa [potoku Spark ml](https://spark.apache.org/docs/2.2.0/ml-pipeline.html) do przeprowadzenia klasyfikacji dokumentu. Potoki ML zapewniają jednolity zestaw interfejsów API wysokiego poziomu zbudowanych na podstawie ramek danych. Ramki danych ułatwiają użytkownikom tworzenie i dostrajanie praktycznych potoków uczenia maszynowego. W potoku dokument jest dzielony na wyrazy, które są przekształcane w wektor elementów liczbowych. Na końcu jest tworzony model predykcyjny przy użyciu etykiet i wektorów elementów. Wykonaj następujące kroki, aby utworzyć aplikację.

1. Utwórz Jupyter Notebook przy użyciu jądra PySpark. Aby uzyskać instrukcje, zobacz [Tworzenie pliku Jupyter Notebook](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook-file).

1. Zaimportuj typy wymagane w tym scenariuszu. Wklej następujący fragment kodu do pustej komórki, a następnie naciśnij klawisze **SHIFT + ENTER**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row

    import os
    import sys
    from pyspark.sql.types import *

    from pyspark.mllib.classification import LogisticRegressionWithSGD
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array
    ```

1. Załaduj dane (hvac.csv) i je przeanalizuj, a następnie je wykorzystaj do nauczenia modelu.

    ```PySpark
    # Define a type called LabelDocument
    LabeledDocument = Row("BuildingID", "SystemInfo", "label")

    # Define a function that parses the raw CSV file and returns an object of type LabeledDocument
    def parseDocument(line):
        values = [str(x) for x in line.split(',')]
        if (values[3] > values[2]):
            hot = 1.0
        else:
            hot = 0.0

        textValue = str(values[4]) + " " + str(values[5])

        return LabeledDocument((values[6]), textValue, hot)

    # Load the raw HVAC.csv file, parse it using the function
    data = sc.textFile("/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
    training = documents.toDF()
    ```

    Fragment kodu zawiera definicję funkcji, która porównuje temperaturę rzeczywistą z temperaturą docelową. Jeśli temperatura rzeczywista jest większa, przyjmuje się, że w budynku jest ciepło, co odpowiada wartości **1.0**. W przeciwnym razie w budynku jest zimno, co odpowiada wartości **0.0**.

1. Skonfiguruj potok uczenia maszynowego platformy Spark, która składa się z trzech etapów: tokenizer, hashingTF i lr.

    ```PySpark
    tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    ```

    Aby uzyskać więcej informacji o potoku i jego działaniu, zobacz [Apache Spark machine learning pipeline](https://spark.apache.org/docs/latest/ml-pipeline.html) (Potok uczenia maszynowego platformy Apache Spark).

1. Dopasuj potok do dokumentu szkoleniowego.

    ```PySpark
    model = pipeline.fit(training)
    ```

1. Zweryfikuj dokument szkoleniowy w celu sprawdzenia postępu w opracowywaniu aplikacji.

    ```PySpark
    training.show()
    ```

    Dane wyjściowe są podobne do następujących:

    ```output
    +----------+----------+-----+
    |BuildingID|SystemInfo|label|
    +----------+----------+-----+
    |         4|     13 20|  0.0|
    |        17|      3 20|  0.0|
    |        18|     17 20|  1.0|
    |        15|      2 23|  0.0|
    |         3|      16 9|  1.0|
    |         4|     13 28|  0.0|
    |         2|     12 24|  0.0|
    |        16|     20 26|  1.0|
    |         9|      16 9|  1.0|
    |        12|       6 5|  0.0|
    |        15|     10 17|  1.0|
    |         7|      2 11|  0.0|
    |        15|      14 2|  1.0|
    |         6|       3 2|  0.0|
    |        20|     19 22|  0.0|
    |         8|     19 11|  0.0|
    |         6|      15 7|  0.0|
    |        13|      12 5|  0.0|
    |         4|      8 22|  0.0|
    |         7|      17 5|  0.0|
    +----------+----------+-----+
    ```

    Porównaj dane wyjściowe z nieprzetworzonymi danymi w pliku CSV. Na przykład pierwszy wiersz pliku CSV zawiera te dane:

    ![Migawka danych wyjściowych dla przykładu uczenia maszynowego Spark](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png "Migawka danych wyjściowych dla przykładu uczenia maszynowego Spark")

    Zwróć uwagę, że temperatura rzeczywista jest mniejsza niż temperatura docelowa, co świadczy o tym, że w budynku jest zimno. Wartość **etykiety** w pierwszym wierszu to **0,0**, co oznacza, że kompilacja nie jest gorąca.

1. Przygotuj zestaw danych do uruchomienia uczonego modelu. W tym celu należy przekazać identyfikator systemowy i wiek systemu (oznaczany jako **SystemInfo** w danych wyjściowych szkolenia). Model przewiduje, czy kompilacja o IDENTYFIKATORze systemu i wieku systemu będzie hotter (zgodnie z 1,0) czy chłodnicą (zgodnie z 0,0).

    ```PySpark
    # SystemInfo here is a combination of system ID followed by system age
    Document = Row("id", "SystemInfo")
    test = sc.parallelize([(1L, "20 25"),
                    (2L, "4 15"),
                    (3L, "16 9"),
                    (4L, "9 22"),
                    (5L, "17 10"),
                    (6L, "7 22")]) \
        .map(lambda x: Document(*x)).toDF()
    ```

1. Na koniec wygenerujemy prognozy na podstawie danych testowych.

    ```PySpark
    # Make predictions on test documents and print columns of interest
    prediction = model.transform(test)
    selected = prediction.select("SystemInfo", "prediction", "probability")
    for row in selected.collect():
        print row
    ```

    Dane wyjściowe są podobne do następujących:

    ```output  
    Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
    Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
    Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
    Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
    Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
    Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
    ```

   Obserwuj pierwszy wiersz w prognozie. W przypadku systemu HVAC o IDENTYFIKATORze 20 i wieku systemu 25 lat kompilacja jest gorąca (**przewidywania = 1.0**). Pierwsza wartość elementu DenseVector (0.49999) odpowiada prognozie 0.0, a druga wartość (0.5001) odpowiada prognozie 1.0. Chociaż w danych wyjściowych druga wartość jest tylko nieznacznie większa, model generuje wynik **prediction=1.0**.

1. Zamknij notes, aby zwolnić zasoby. W tym celu w menu **File** (Plik) w notesie wybierz pozycję **Close and Halt** (Zamknij i zatrzymaj). Ta akcja powoduje zatrzymanie i zamknięcie notesu.

## <a name="use-anaconda-scikit-learn-library-for-spark-machine-learning"></a>Uczenie maszynowe platformy Spark z użyciem biblioteki Anaconda scikit-learn

Klastry Apache Spark w usłudze HDInsight obejmują biblioteki Anaconda. Zawierają także bibliotekę **scikit-learn** do uczenia maszynowego. Biblioteka zawiera również różne zestawy danych, których można użyć do kompilowania przykładowych aplikacji bezpośrednio z Jupyter Notebook. Aby zapoznać się z przykładami dotyczącymi korzystania z biblioteki scikit-Dowiedz się, zobacz [https://scikit-learn.org/stable/auto_examples/index.html](https://scikit-learn.org/stable/auto_examples/index.html) .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń klaster, który został utworzony, wykonując następujące czynności:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. W polu **Wyszukaj** w górnej części wpisz **HDInsight**.

1. Wybierz pozycję **Klastry usługi HDInsight** w obszarze **Usługi**.

1. Na liście wyświetlonych klastrów usługi HDInsight wybierz pozycję **...** obok klastra utworzonego w ramach tego samouczka.

1. Wybierz pozycję **Usuń**. Wybierz pozycję **Tak**.

![Azure Portal usuwa klaster usługi HDInsight](./media/apache-spark-ipython-notebook-machine-learning/hdinsight-azure-portal-delete-cluster.png "Usuwanie klastra usługi HDInsight")

## <a name="next-steps"></a>Następne kroki

W ramach tego samouczka nauczysz się używać Jupyter Notebook do kompilowania aplikacji Apache Spark Machine Learning dla usługi Azure HDInsight. Przejdź do następnego samouczka, aby dowiedzieć się, jak używać środowiska IntelliJ IDEA na potrzeby zadań Spark.

> [!div class="nextstepaction"]
> [Tworzenie aplikacji Scala Maven przy użyciu IntelliJ](./apache-spark-create-standalone-application.md)
