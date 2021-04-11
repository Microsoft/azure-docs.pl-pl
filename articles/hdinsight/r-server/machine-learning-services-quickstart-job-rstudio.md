---
title: 'Szybki Start: RStudio Server & ML Services for R — Azure HDInsight'
description: W ramach przewodnika Szybki Start wykonujesz skrypt języka R w klastrze usług ML w usłudze Azure HDInsight przy użyciu serwera RStudio.
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/19/2019
ms.custom: mvc
ms.openlocfilehash: 8abd485bff885868d7865cf273cb161974106013
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104862837"
---
# <a name="quickstart-execute-an-r-script-on-an-ml-services-cluster-in-azure-hdinsight-using-rstudio-server"></a>Szybki Start: wykonywanie skryptu języka R w klastrze usług ML w usłudze Azure HDInsight przy użyciu serwera RStudio

Usługi ML w usłudze Azure HDInsight umożliwiają używanie skryptów języka R Apache Spark i Apache Hadoop MapReduce do uruchamiania obliczeń rozproszonych. Usługa ML kontroluje sposób wykonywania wywołań przez ustawienie kontekstu obliczeniowego. Węzeł brzegowy klastra zapewnia wygodne miejsce do łączenia się z klastrem i uruchamiania skryptów języka R. Węzeł brzegowy umożliwia uruchamianie równoległych funkcji rozproszonych kolekcję funkcji revoscaler na różnych rdzeniach serwera węzła brzegowego. Można je również uruchamiać w węzłach klastra przy użyciu mapy usługi Hadoop w usłudze kolekcję funkcji revoscaler, zmniejszając lub Apache Spark konteksty obliczeniowe.

W tym przewodniku szybki start dowiesz się, jak uruchomić skrypt języka R z serwerem RStudio, który demonstruje użycie platformy Spark w przypadku obliczeń rozproszonych języka R. Zdefiniujesz kontekst obliczeniowy do wykonywania obliczeń lokalnie w węźle brzegowym i ponownie dystrybuowany w węzłach klastra usługi HDInsight.

## <a name="prerequisite"></a>Wymaganie wstępne

Klaster usługi ML w usłudze HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) i wybierz pozycję **usługi ml** dla **typu klastra**.

## <a name="connect-to-rstudio-server"></a>Łączenie z programem RStudio Server

Serwer RStudio jest uruchamiany w węźle brzegowym klastra. Przejdź do następującego adresu URL, gdzie `CLUSTERNAME` to nazwa utworzonego klastra usług ml:

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

Gdy logujesz się po raz pierwszy, musisz uwierzytelnić się dwa razy. W przypadku pierwszego monitu o uwierzytelnienie Podaj nazwę logowania administratora klastra i hasło, wartość domyślna to `admin` . W przypadku drugiego monitu uwierzytelniania Podaj nazwę logowania SSH i hasło, wartość domyślna to `sshuser` . Kolejne logowania wymagają tylko poświadczeń SSH.

Po nawiązaniu połączenia ekran powinien przypominać następujący zrzut ekranu:

:::image type="content" source="./media/ml-services-quickstart-job-rstudio/connect-to-r-studio1.png" alt-text="Przeglądy konsoli sieci Web programu R Studio" border="true":::

## <a name="use-a-compute-context"></a>Używanie kontekstu obliczeniowego

1. Z serwera RStudio należy użyć następującego kodu do załadowania przykładowych danych do domyślnego magazynu dla usługi HDInsight:

    ```RStudio
    # Set the HDFS (WASB) location of example data
     bigDataDirRoot <- "/example/data"
    
     # create a local folder for storing data temporarily
     source <- "/tmp/AirOnTimeCSV2012"
     dir.create(source)
    
     # Download data to the tmp folder
     remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
     download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
     download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
     download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
     download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
     download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
     download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
     download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
     download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
     download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
     download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
     download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
     download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))
    
     # Set directory in bigDataDirRoot to load the data into
     inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")
    
     # Make the directory
     rxHadoopMakeDir(inputDir)
    
     # Copy the data from source to input
     rxHadoopCopyFromLocal(source, bigDataDirRoot)
    ```

    Wykonanie tego kroku może potrwać około 8 minut.

1. Utwórz informacje o danych i zdefiniuj dwa źródła danych. Wprowadź następujący kod w RStudio:

    ```RStudio
    # Define the HDFS (WASB) file system
     hdfsFS <- RxHdfsFileSystem()
    
     # Create info list for the airline data
     airlineColInfo <- list(
          DAY_OF_WEEK = list(type = "factor"),
          ORIGIN = list(type = "factor"),
          DEST = list(type = "factor"),
          DEP_TIME = list(type = "integer"),
          ARR_DEL15 = list(type = "logical"))
    
     # get all the column names
     varNames <- names(airlineColInfo)
    
     # Define the text data source in hdfs
     airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)
    
     # Define the text data source in local system
     airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)
    
     # formula to use
     formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"
    ```

1. Uruchom regresję logistyczną na danych za pomocą **lokalnego** kontekstu obliczeniowego. Wprowadź następujący kod w RStudio:

    ```RStudio
    # Set a local compute context
     rxSetComputeContext("local")
    
     # Run a logistic regression
     system.time(
        modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
     )
    
     # Display a summary
     summary(modelLocal)
    ```

    Obliczenia powinny zakończyć się w około 7 minutach. Powinny pojawić się dane wyjściowe kończące się wierszami podobnymi do następującego fragmentu kodu:

    ```output
    Data: airOnTimeDataLocal (RxTextData Data Source)
     File name: /tmp/AirOnTimeCSV2012
     Dependent variable(s): ARR_DEL15
     Total independent variables: 634 (Including number dropped: 3)
     Number of valid observations: 6005381
     Number of missing observations: 91381
     -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)
    
     Coefficients:
                      Estimate Std. Error z value Pr(>|z|)
      (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
      ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
      ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
      ......
      DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
      DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
      DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
      DEST=BPT         Dropped    Dropped Dropped  Dropped
    
      ---
    
      Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
      Condition number of final variance-covariance matrix: 11904202
      Number of iterations: 7
    ```

1. Uruchom tę samą regresję logistyczną za pomocą kontekstu **Spark** . Dzięki kontekstowi aparatu Spark przetwarzanie jest dystrybuowane do wszystkich węzłów procesu roboczego w klastrze usługi HDInsight. Wprowadź następujący kod w RStudio:

    ```RStudio
    # Define the Spark compute context
     mySparkCluster <- RxSpark()
    
     # Set the compute context
     rxSetComputeContext(mySparkCluster)
    
     # Run a logistic regression
     system.time(  
        modelSpark <- rxLogit(formula, data = airOnTimeData)
     )
    
     # Display a summary
     summary(modelSpark)
    ```

    Obliczenia powinny zakończyć się około 5 minut.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu przewodnika Szybki Start możesz chcieć usunąć klaster. Dzięki usłudze HDInsight dane są przechowywane w usłudze Azure Storage, więc można bezpiecznie usunąć klaster, gdy nie jest używany. Opłaty za klaster usługi HDInsight są naliczane nawet wtedy, gdy nie jest używany. Ponieważ opłaty za klaster są wielokrotnie większe niż opłaty za magazyn, ze względów ekonomicznych warto usuwać klastry, gdy nie są używane.

Aby usunąć klaster, zobacz [usuwanie klastra usługi HDInsight przy użyciu przeglądarki, programu PowerShell lub interfejsu wiersza polecenia platformy Azure](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób uruchamiania skryptu języka R z serwerem RStudio, który pokazano przy użyciu platformy Spark dla rozproszonych obliczeń języka R.  Przejdź do następnego artykułu, aby dowiedzieć się, jakie opcje są dostępne, aby określić, czy i jak wykonywanie jest równoległe między rdzeniami węzła krawędzi lub klastra usługi HDInsight.

> [!div class="nextstepaction"]
>[Opcje kontekstu obliczeniowego dla usług ML w usłudze HDInsight](./r-server-compute-contexts.md)

> [!NOTE]
> Ta strona zawiera opis funkcji oprogramowania RStudio. Microsoft Azure HDInsight nie jest powiązany z RStudio, Inc.
