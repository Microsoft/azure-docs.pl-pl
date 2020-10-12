---
title: Analizowanie dzienników witryn sieci Web przy użyciu bibliotek języka Python w platformie Spark — Azure
description: Ten Notes pokazuje, jak analizować dane dzienników przy użyciu biblioteki niestandardowej z platformą Spark w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 12/27/2019
ms.openlocfilehash: 1094235f5bc5cc25cf6d8f3762dc242503952de6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86083801"
---
# <a name="analyze-website-logs-using-a-custom-python-library-with-apache-spark-cluster-on-hdinsight"></a>Analizowanie dzienników witryn sieci Web przy użyciu niestandardowej biblioteki języka Python z klastrem Apache Spark w usłudze HDInsight

Ten Notes pokazuje, jak analizować dane dzienników przy użyciu biblioteki niestandardowej z Apache Spark w usłudze HDInsight. Używana biblioteka niestandardowa to biblioteka języka Python o nazwie **iislogparser.py**.

## <a name="prerequisites"></a>Wymagania wstępne

Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Zapisz dane pierwotne jako RDD

W tej sekcji używamy notesu [Jupyter](https://jupyter.org) skojarzonego z klastrem Apache Spark w usłudze HDInsight w celu uruchamiania zadań przetwarzających pierwotne dane przykładowe i zapisując je jako tabelę programu Hive. Przykładowe dane to plik CSV (hvac.csv) domyślnie dostępny dla wszystkich klastrów.

Po zapisaniu danych jako tabeli Apache Hive w następnej sekcji będziemy łączyć się z tabelą programu Hive przy użyciu narzędzi analizy biznesowej, takich jak Power BI i Tableau.

1. W przeglądarce sieci Web przejdź do `https://CLUSTERNAME.azurehdinsight.net/jupyter` lokalizacji, gdzie `CLUSTERNAME` jest nazwą klastra.

1. Utwórz nowy notes. Wybierz pozycję **Nowy**, a następnie **PySpark**.

    ![Tworzenie nowego notesu Apache Jupyter](./media/apache-spark-custom-library-website-log-analysis/hdinsight-create-jupyter-notebook.png "Tworzenie nowego notesu Jupyter")

1. Zostanie utworzony i otwarty nowy notes o nazwie Untitled.pynb. Wybierz nazwę notesu u góry, a następnie wprowadź przyjazną nazwę.

    ![Wprowadzanie nazwy notesu](./media/apache-spark-custom-library-website-log-analysis/hdinsight-name-jupyter-notebook.png "Wprowadzanie nazwy notesu")

1. Ponieważ utworzono Notes przy użyciu jądra PySpark, nie trzeba jawnie tworzyć kontekstów. Konteksty Spark i Hive zostaną automatycznie utworzone po uruchomieniu pierwszej komórki kodu. Możesz rozpocząć od zaimportowania typów, które są wymagane dla tego scenariusza. Wklej następujący fragment kodu do pustej komórki, a następnie naciśnij klawisze **SHIFT + ENTER**.

    ```pyspark
    from pyspark.sql import Row
    from pyspark.sql.types import *
    ```

1. Utwórz RDD za pomocą przykładowych danych dziennika, które są już dostępne w klastrze. Możesz uzyskać dostęp do danych na domyślnym koncie magazynu skojarzonym z klastrem w lokalizacji `\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log` . Wykonaj następujący kod:

    ```pyspark
    logs = sc.textFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')
    ```

1. Pobierz przykładowy zestaw dzienników, aby sprawdzić, czy poprzedni krok został ukończony pomyślnie.

    ```pyspark
    logs.take(5)
    ```

    Powinny pojawić się dane wyjściowe podobne do następującego tekstu:

    ```output
    [u'#Software: Microsoft Internet Information Services 8.0',
    u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
    u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']
    ```

## <a name="analyze-log-data-using-a-custom-python-library"></a>Analizowanie danych dzienników przy użyciu niestandardowej biblioteki języka Python

1. W danych wyjściowych powyżej pierwsze dwa wiersze zawierają informacje nagłówka, a każdy pozostały wiersz jest zgodny ze schematem opisanym w tym nagłówku. Analizowanie takich dzienników może być skomplikowane. Dlatego używamy niestandardowej biblioteki języka Python (**iislogparser.py**), która znacznie ułatwia analizowanie takich dzienników. Domyślnie ta biblioteka jest dołączona do klastra Spark w usłudze HDInsight pod adresem `/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py` .

    Jednak ta biblioteka nie jest w tej bibliotece, `PYTHONPATH` dlatego nie można jej używać przy użyciu instrukcji import, takiej jak `import iislogparser` . Aby można było korzystać z tej biblioteki, musimy ją rozpowszechnić do wszystkich węzłów procesu roboczego. Uruchom Poniższy fragment kodu.

    ```pyspark
    sc.addPyFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')
    ```

1. `iislogparser` zapewnia funkcję `parse_log_line` , która zwraca `None` , jeśli wiersz dziennika jest wierszem nagłówka i zwraca wystąpienie `LogLine` klasy, jeśli napotka wiersz dziennika. Użyj `LogLine` klasy, aby wyodrębnić tylko wiersze dziennika z RDD:

    ```pyspark
    def parse_line(l):
        import iislogparser
        return iislogparser.parse_log_line(l)
    logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()
    ```

1. Pobierz kilka wyodrębnionych wierszy dziennika, aby sprawdzić, czy krok zakończył się pomyślnie.

    ```pyspark
    logLines.take(2)
    ```

   Dane wyjściowe powinny wyglądać podobnie do następującego tekstu:

    ```output
    [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
    2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]
    ```

1. `LogLine`Klasa z kolei ma kilka przydatnych metod, takich jak `is_error()` , które zwracają, czy wpis dziennika ma kod błędu. Użyj tej klasy, aby obliczyć liczbę błędów w wyodrębnionych wierszach dziennika, a następnie Rejestruj wszystkie błędy w innym pliku.

    ```pyspark
    errors = logLines.filter(lambda p: p.is_error())
    numLines = logLines.count()
    numErrors = errors.count()
    print 'There are', numErrors, 'errors and', numLines, 'log entries'
    errors.map(lambda p: str(p)).saveAsTextFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')
    ```

    Dane wyjściowe powinny być stanu `There are 30 errors and 646 log entries` .

1. Możesz również użyć **matplotlib** do konstruowania wizualizacji danych. Na przykład jeśli chcesz odizolować przyczynę żądań, które są uruchamiane przez długi czas, możesz chcieć znaleźć pliki, które najprawdopodobniej przekroczą średnią. Poniższy fragment kodu pobiera 25 najpopularniejszych zasobów, które miały najwięcej czasu na przekazanie żądania.

    ```pyspark
    def avgTimeTakenByKey(rdd):
        return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                    .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))

    avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])
    ```

   Powinny pojawić się dane wyjściowe podobne do następującego tekstu:

    ```output
    [(u'/blogposts/mvc4/step13.png', 197.5),
    (u'/blogposts/mvc2/step10.jpg', 179.5),
    (u'/blogposts/extractusercontrol/step5.png', 170.0),
    (u'/blogposts/mvc4/step8.png', 159.0),
    (u'/blogposts/mvcrouting/step22.jpg', 155.0),
    (u'/blogposts/mvcrouting/step3.jpg', 152.0),
    (u'/blogposts/linqsproc1/step16.jpg', 138.75),
    (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
    (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
    (u'/blogposts/nested/step2.jpg', 126.0),
    (u'/blogposts/adminpack/step1.png', 124.0),
    (u'/BlogPosts/datalistpaging/step2.png', 118.0),
    (u'/blogposts/mvc4/step35.png', 117.0),
    (u'/blogposts/mvcrouting/step2.jpg', 116.5),
    (u'/blogposts/aboutme/basketball.jpg', 109.0),
    (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
    (u'/blogposts/mvc4/step12.png', 106.0),
    (u'/blogposts/linq8/step0.jpg', 105.5),
    (u'/blogposts/mvc2/step18.jpg', 104.0),
    (u'/blogposts/mvc2/step11.jpg', 104.0),
    (u'/blogposts/mvcrouting/step1.jpg', 104.0),
    (u'/blogposts/extractusercontrol/step1.png', 103.0),
    (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
    (u'/blogposts/mvcrouting/step21.jpg', 101.0),
    (u'/blogposts/mvc4/step1.png', 98.0)]
    ```

1. Te informacje można również przedstawić w formie kreślenia. Jako pierwszy krok tworzenia wykresu, pozwól nam najpierw utworzyć tymczasową tabelę tabeli **AverageTime**. W tabeli są pogrupowane dzienniki według czasu, aby sprawdzić, czy w danym momencie wystąpił nietypowe opóźnienia.

    ```pyspark
    avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
    schema = StructType([StructField('Minutes', IntegerType(), True),
                        StructField('Time', FloatType(), True)])

    avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
    avgTimeTakenByMinuteDF.registerTempTable('AverageTime')
    ```

1. Następnie można uruchomić następujące zapytanie SQL, aby pobrać wszystkie rekordy z tabeli **AverageTime** .

    ```pyspark
    %%sql -o averagetime
    SELECT * FROM AverageTime
    ```

   Magiczna Metoda polega na `%%sql` `-o averagetime` tym, że dane wyjściowe zapytania są utrwalane lokalnie na serwerze Jupyter (zazwyczaj węzła głównego klastra). Dane wyjściowe są utrwalane jako ramka [dataPandas](https://pandas.pydata.org/) o określonej nazwie **averagetime**.

   Powinny pojawić się dane wyjściowe podobne do poniższej:

   ![dane wyjściowe zapytania SQL usługi HDInsight Jupyter](./media/apache-spark-custom-library-website-log-analysis/hdinsight-jupyter-sql-qyery-output.png "Dane wyjściowe zapytania SQL")

   Aby uzyskać więcej informacji na temat `%%sql` Magic, zobacz [Parametry obsługiwane za pomocą Magic%% SQL](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic).

1. Teraz możesz użyć matplotlib, biblioteki używanej do konstruowania wizualizacji danych, aby utworzyć wykres. Ponieważ wykres musi być tworzony na podstawie lokalnie utrwalonej ramki Dataframe **averagetime** , fragment kodu musi rozpoczynać się od `%%local` Magic. Dzięki temu kod jest uruchamiany lokalnie na serwerze Jupyter.

    ```pyspark
    %%local
    %matplotlib inline
    import matplotlib.pyplot as plt

    plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
    plt.xlabel('Time (min)')
    plt.ylabel('Average time taken for request (ms)')
    ```

   Powinny pojawić się dane wyjściowe podobne do poniższej:

   ![Wykres analizy dzienników sieci Web platformy Apache Spark](./media/apache-spark-custom-library-website-log-analysis/hdinsight-apache-spark-web-log-analysis-plot.png "Matplotlib dane wyjściowe")

1. Po zakończeniu działania aplikacji należy zamknąć Notes, aby zwolnić zasoby. W tym celu w menu **File** (Plik) w notesie wybierz pozycję **Close and Halt** (Zamknij i zatrzymaj). Ta akcja spowoduje zamknięcie i zamknięcie notesu.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami:

* [Przegląd: platforma Apache Spark w usłudze Azure HDInsight](apache-spark-overview.md)
* [Korzystanie z zewnętrznych pakietów z notesami Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalacja oprogramowania Jupyter na komputerze i nawiązywanie połączenia z klastrem Spark w usłudze HDInsight](apache-spark-jupyter-notebook-install-locally.md)
