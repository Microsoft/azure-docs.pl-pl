---
title: 'Samouczek: używanie języka R w kontekście obliczeń Spark w usłudze Azure HDInsight'
description: Samouczek — wprowadzenie do oprogramowania R i Spark w klastrze usług Machine Learning Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: 73ca0d089ab758fb13e69d341337139d79194cc5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "71121935"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>Samouczek: używanie języka R w kontekście obliczeń Spark w usłudze Azure HDInsight

Ten samouczek zawiera instrukcje krok po kroku dotyczące korzystania z funkcji R w programie Apache Spark uruchomionym w klastrze usługi Azure HDInsight Machine Learning Services.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Pobieranie przykładowych danych do magazynu lokalnego
> * Kopiuj dane do magazynu domyślnego
> * Konfigurowanie zestawu danych
> * Tworzenie źródeł danych
> * Tworzenie kontekstu obliczeniowego dla platformy Spark
> * Dopasuj model liniowy
> * Używanie złożonych plików XDF
> * Konwertuj XDF do formatu CSV

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster usług Machine Learning usługi Azure HDInsight. Przejdź do [obszaru tworzenie klastrów Apache Hadoop przy użyciu Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) i dla **typu klastra**wybierz pozycję usługi w usłudze **ml**.

## <a name="connect-to-rstudio-server"></a>Łączenie z programem RStudio Server

Serwer RStudio jest uruchamiany w węźle brzegowym klastra. Przejdź do następującej witryny (gdzie *ClusterName* w adresie URL jest nazwą utworzonego klastra usług Machine Learning HDInsight):

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

Gdy logujesz się po raz pierwszy, uwierzytelniasz dwa razy. W pierwszym monicie uwierzytelniania Podaj nazwę użytkownika i hasło administratora klastra (wartość domyślna to *admin*). W drugim monicie o uwierzytelnieniu Podaj nazwę użytkownika i hasło SSH (wartość domyślna to *sshuser*). Kolejne logowania wymagają tylko poświadczeń SSH.

## <a name="download-the-sample-data-to-local-storage"></a>Pobieranie przykładowych danych do magazynu lokalnego

*Zestaw danych na czas linii lotniczych 2012* obejmuje 12 plików rozdzielonych przecinkami, które zawierają szczegóły dotyczące przybycia i wyjazdu lotów dla wszystkich lotów komercyjnych w Stanach Zjednoczonych na rok 2012. Ten zestaw danych jest duży i zawiera ponad 6 000 000 obserwacji.

1. Zainicjuj kilka zmiennych środowiskowych. W konsoli serwera RStudio wprowadź następujący kod:

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

1. W okienku po prawej stronie wybierz kartę **środowisko** . Zmienne są wyświetlane w obszarze **wartości**.

    ![Konsola sieci Web HDInsight R Studio](./media/ml-services-tutorial-spark-compute/hdinsight-rstudio-image.png)

1. Utwórz katalog lokalny i Pobierz przykładowe dane. W RStudio wprowadź następujący kod:

    ```R
    # Create local directory
    dir.create(localDir)
    
    # Download data to the tmp folder(local)
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(localDir, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(localDir, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(localDir, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(localDir, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(localDir, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(localDir, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(localDir, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(localDir, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(localDir, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(localDir, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(localDir, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(localDir, "airOT201212.csv"))
    ```

    Pobieranie powinno zakończyć się około 9,5 minut.

## <a name="copy-the-data-to-default-storage"></a>Kopiuj dane do magazynu domyślnego

W lokalizacji rozproszony system plików Hadoop (HDFS) jest określona `airDataDir` zmienna. W RStudio wprowadź następujący kod:

```R
# Set directory in bigDataDirRoot to load the data into
airDataDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

# Create directory (default storage)
rxHadoopMakeDir(airDataDir)

# Copy data from local storage to default storage
rxHadoopCopyFromLocal(localDir, bigDataDirRoot)
    
# Optional. Verify files
rxHadoopListFiles(airDataDir)
```

Krok powinien być zakończony przez około 10 sekund.

## <a name="set-up-a-dataset"></a>Konfigurowanie zestawu danych

1. Utwórz obiekt systemu plików, który używa wartości domyślnych. W RStudio wprowadź następujący kod:

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

1. Ponieważ oryginalne pliki CSV mają raczej nieporęczny nazwy zmiennych, należy podać listę *colInfo* , aby ułatwić zarządzanie nimi. W RStudio wprowadź następujący kod:

    ```R
    airlineColInfo <- list(
         MONTH = list(newName = "Month", type = "integer"),
        DAY_OF_WEEK = list(newName = "DayOfWeek", type = "factor",
            levels = as.character(1:7),
            newLevels = c("Mon", "Tues", "Wed", "Thur", "Fri", "Sat",
                          "Sun")),
        UNIQUE_CARRIER = list(newName = "UniqueCarrier", type =
                                "factor"),
        ORIGIN = list(newName = "Origin", type = "factor"),
        DEST = list(newName = "Dest", type = "factor"),
        CRS_DEP_TIME = list(newName = "CRSDepTime", type = "integer"),
        DEP_TIME = list(newName = "DepTime", type = "integer"),
        DEP_DELAY = list(newName = "DepDelay", type = "integer"),
        DEP_DELAY_NEW = list(newName = "DepDelayMinutes", type =
                             "integer"),
        DEP_DEL15 = list(newName = "DepDel15", type = "logical"),
        DEP_DELAY_GROUP = list(newName = "DepDelayGroups", type =
                               "factor",
           levels = as.character(-2:12),
           newLevels = c("< -15", "-15 to -1","0 to 14", "15 to 29",
                         "30 to 44", "45 to 59", "60 to 74",
                         "75 to 89", "90 to 104", "105 to 119",
                         "120 to 134", "135 to 149", "150 to 164",
                         "165 to 179", ">= 180")),
        ARR_DELAY = list(newName = "ArrDelay", type = "integer"),
        ARR_DELAY_NEW = list(newName = "ArrDelayMinutes", type =
                             "integer"),  
        ARR_DEL15 = list(newName = "ArrDel15", type = "logical"),
        AIR_TIME = list(newName = "AirTime", type =  "integer"),
        DISTANCE = list(newName = "Distance", type = "integer"),
        DISTANCE_GROUP = list(newName = "DistanceGroup", type =
                             "factor",
         levels = as.character(1:11),
         newLevels = c("< 250", "250-499", "500-749", "750-999",
             "1000-1249", "1250-1499", "1500-1749", "1750-1999",
             "2000-2249", "2250-2499", ">= 2500")))
    
    varNames <- names(airlineColInfo)
    ```

## <a name="create-data-sources"></a>Tworzenie źródeł danych

W kontekście obliczeniowym platformy Spark można utworzyć źródła danych, korzystając z następujących funkcji:

|Funkcja | Opis |
|---------|-------------|
|`RxTextData` | Rozdzielane przecinkami źródło danych tekstowych. |
|`RxXdfData` | Dane w formacie pliku danych XDF. W programie kolekcję funkcji revoscaler format pliku XDF jest modyfikowany dla usługi Hadoop w celu przechowywania danych w złożonym zestawie plików, a nie w pojedynczym pliku. |
|`RxHiveData` | Generuje obiekt źródła danych programu Hive.|
|`RxParquetData` | Generuje obiekt źródła danych Parquet.|
|`RxOrcData` | Generuje obiekt źródła danych ORC.|

Utwórz obiekt [RxTextData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxtextdata) przy użyciu plików skopiowanych do systemu plików HDFS. W RStudio wprowadź następujący kod:

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-a-compute-context-for-spark"></a>Tworzenie kontekstu obliczeniowego dla platformy Spark

Aby załadować dane i uruchomić analizy w węzłach procesu roboczego, należy ustawić kontekst obliczeń w skrypcie na [obliczeniowego rxspark](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxspark). W tym kontekście funkcja R automatycznie dystrybuuje obciążenie dla wszystkich węzłów procesu roboczego bez wbudowanego wymagania dotyczącego zarządzania zadaniami lub kolejką. Kontekst obliczeń platformy Spark jest ustanawiany za pośrednictwem `RxSpark` `rxSparkConnect()` programu lub w celu utworzenia kontekstu obliczeniowego platformy Spark i służy `rxSparkDisconnect()` do powrotu do lokalnego kontekstu obliczeniowego. W RStudio wprowadź następujący kod:

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>Dopasuj model liniowy

1. Użyj funkcji [rxLinMod](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod) , aby dopasować model liniowy przy użyciu `airDS` źródła danych. W RStudio wprowadź następujący kod:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    Ten krok należy wykonać w 2 – 3 minutach.

1. Przejrzyj wyniki. W RStudio wprowadź następujący kod:

    ```R
    summary(delayArr)
    ```

    Powinny zostać wyświetlone następujące wyniki:

    ```output
    Call:
    rxLinMod(formula = ArrDelay ~ DayOfWeek, data = airDS, cube = TRUE)
    
    Cube Linear Regression Results for: ArrDelay ~ DayOfWeek
    Data: airDataXdf (RxXdfData Data Source)
    File name: /tutorial/data/AirOnTimeCSV2012
    Dependent variable(s): ArrDelay
    Total independent variables: 7 
    Number of valid observations: 6005381
    Number of missing observations: 91381 
     
    Coefficients:
                   Estimate Std. Error t value Pr(>|t|)     | Counts
    DayOfWeek=Mon   3.54210    0.03736   94.80 2.22e-16 *** | 901592
    DayOfWeek=Tues  1.80696    0.03835   47.12 2.22e-16 *** | 855805
    DayOfWeek=Wed   2.19424    0.03807   57.64 2.22e-16 *** | 868505
    DayOfWeek=Thur  4.65502    0.03757  123.90 2.22e-16 *** | 891674
    DayOfWeek=Fri   5.64402    0.03747  150.62 2.22e-16 *** | 896495
    DayOfWeek=Sat   0.91008    0.04144   21.96 2.22e-16 *** | 732944
    DayOfWeek=Sun   2.82780    0.03829   73.84 2.22e-16 *** | 858366
    ---
    Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
    Residual standard error: 35.48 on 6005374 degrees of freedom
    Multiple R-squared: 0.001827 (as if intercept included)
    Adjusted R-squared: 0.001826 
    F-statistic:  1832 on 6 and 6005374 DF,  p-value: < 2.2e-16 
    Condition number: 1 
    ```

    Wyniki wskazują, że przetworzono wszystkie dane, 6 000 000 obserwacje przy użyciu wszystkich plików CSV w określonym katalogu. Ze względu na to, że masz określony `cube = TRUE` współczynnik dla każdego dnia tygodnia (a nie przechwycenia).

## <a name="use-composite-xdf-files"></a>Używanie złożonych plików XDF

Jak widać, można analizować pliki CSV bezpośrednio przy użyciu języka R w usłudze Hadoop. Jednak analizę można wykonać szybciej, jeśli dane są przechowywane w bardziej wydajny sposób. Format pliku XDF języka R jest wydajny, ale jest nieco modyfikowany dla systemu plików HDFS, dzięki czemu pojedyncze pliki pozostają w jednym bloku systemu plików HDFS. (Rozmiar bloku systemu plików HDFS różni się od instalacji do instalacji, ale ma zazwyczaj 64 MB lub 128 MB). 

W przypadku tworzenia zestawu złożonych plików XDF przy użyciu usługi [rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport) w usłudze Hadoop należy określić `RxTextData` Źródło danych, takie jak `AirDS` dane i `RxXdfData` Źródło danych z systemem plików w systemie HDFS jako argumentem pliku. Można następnie użyć `RxXdfData` obiektu jako argumentu danych w kolejnych analizach języka R.

1. Zdefiniuj `RxXdfData` obiekt. W RStudio wprowadź następujący kod:

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. Ustaw rozmiar bloku 250000 wierszy i określ, że wszystkie dane są odczytywane. W RStudio wprowadź następujący kod:

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. Zaimportuj dane przy użyciu programu `rxImport` . W RStudio wprowadź następujący kod:

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    Ten krok należy wykonać w ciągu kilku minut.

1. Ponowne oszacowanie tego samego modelu liniowego przy użyciu nowego, szybszego źródła danych. W RStudio wprowadź następujący kod:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    Krok powinien być wykonany w czasie krótszym niż minutę.

1. Przejrzyj wyniki. Wyniki powinny być takie same jak w przypadku plików CSV. W RStudio wprowadź następujący kod:

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>Konwertuj XDF do formatu CSV

### <a name="in-a-spark-context"></a>W kontekście platformy Spark

Jeśli pliki CSV zostały przekonwertowane na format pliku XDF w celu zwiększenia wydajności podczas wykonywania analiz, ale teraz chcesz przekonwertować dane z powrotem do woluminu CSV, możesz to zrobić za pomocą [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep).

Aby utworzyć folder plików CSV, najpierw Utwórz `RxTextData` Obiekt przy użyciu nazwy katalogu jako argumentu pliku. Ten obiekt reprezentuje folder, w którym mają zostać utworzone pliki CSV. Ten katalog jest tworzony podczas uruchamiania programu `rxDataStep` . Następnie wskaż ten `RxTextData` obiekt w `outFile` argumencie `rxDataStep` . Każdy utworzony wolumin CSV ma nazwę na podstawie nazwy katalogu i po nim liczby.

Załóżmy, że chcesz napisać folder plików CSV w systemie HDFS z `airDataXdf` XDF złożonego po wykonaniu regresji logistycznej i przewidywania, tak aby nowe pliki CSV zawierały przewidywane wartości i reszty. W RStudio wprowadź następujący kod:

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

Ten krok należy wykonać w około 2,5 minutach.

`rxDataStep`Zapisano jeden plik CSV dla każdego pliku XDFD w wejściowym pliku XDF. Jest to domyślne zachowanie podczas zapisywania plików CSV z złożonych plików XDF do systemu HDFS, gdy kontekst obliczeń jest ustawiony na `RxSpark` .

### <a name="in-a-local-context"></a>W kontekście lokalnym

Alternatywnie, po zakończeniu wykonywania analiz można przełączyć kontekst obliczeń z powrotem do programu, `local` Aby korzystać z dwóch argumentów w programie `RxTextData` , co zapewnia nieco większą kontrolę podczas zapisywania plików CSV w systemie HDFS: `createFileSet` i `rowsPerOutFile` . Gdy ustawisz `createFileSet` `TRUE` opcję, folder plików CSV zostanie zapisany w katalogu, który określisz. Po ustawieniu `createFileSet` na wartość `FALSE` zostanie zapisany pojedynczy plik CSV. Można ustawić drugi argument, `rowsPerOutFile` do liczby całkowitej, aby wskazać liczbę wierszy do zapisu w każdym pliku CSV, gdy `createFileSet` jest `TRUE` .

W RStudio wprowadź następujący kod:

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

Ten krok należy wykonać w około 10 minut.

W przypadku korzystania z `RxSpark` kontekstu obliczeniowego `createFileSet` wartość domyślna to `TRUE` i `rowsPerOutFile` nie ma żadnego wpływu. W związku z tym, jeśli chcesz utworzyć pojedynczy wolumin CSV lub dostosować liczbę wierszy na plik, wykonaj `rxDataStep` w `local` kontekście obliczeniowym (dane nadal mogą znajdować się w systemie plików HDFS).

## <a name="final-steps"></a>Kroki końcowe

1. Wyczyść dane. W RStudio wprowadź następujący kod:

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. Zatrzymaj zdalną aplikację Spark. W RStudio wprowadź następujący kod:

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. Zakończ sesję języka R. W RStudio wprowadź następujący kod:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po ukończeniu tego samouczka możesz chcieć usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, dzięki czemu można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty są naliczone również za klaster usługi HDInsight, nawet wtedy, gdy nie jest używany. Ze względu na to, że opłaty za klaster są dużo razy większe niż opłaty za magazyn, to ekonomiczne znaczenie do usuwania klastrów, gdy nie są używane.

Aby usunąć klaster, zobacz [usuwanie klastra usługi HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia platformy Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób korzystania z funkcji R w Apache Spark uruchomionych w klastrze usługi HDInsight Machine Learning. Aby uzyskać więcej informacji zobacz następujące artykuły:

* [Opcje kontekstu obliczeniowego dla klastra usług Machine Learning usługi Azure HDInsight](r-server-compute-contexts.md)
* [Funkcje języka R dla platformy Spark w usłudze Hadoop](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)
