---
title: Korzystanie z MapReduce i Zwinięciea przy użyciu Apache Hadoop w usłudze HDInsight — Azure
description: Dowiedz się, jak zdalnie uruchamiać zadania MapReduce z Apache Hadoop w usłudze HDInsight przy użyciu programu zwinięcie.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/13/2020
ms.openlocfilehash: e90dc2c7220caf5bd72b7086adc275934652e150
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939699"
---
# <a name="run-mapreduce-jobs-with-apache-hadoop-on-hdinsight-using-rest"></a>Uruchamianie zadań MapReduce za pomocą Apache Hadoop w usłudze HDInsight przy użyciu usługi REST

Dowiedz się, jak używać interfejsu API REST Apache Hive WebHCat do uruchamiania zadań MapReduce na Apache Hadoop w klastrze usługi HDInsight. Zwinięcie służy do zademonstrowania, w jaki sposób można korzystać z usługi HDInsight przy użyciu nieprzetworzonych żądań HTTP do uruchamiania zadań MapReduce.

> [!NOTE]  
> Jeśli masz już doświadczenie w korzystaniu z serwerów Hadoop opartych na systemie Linux, ale dopiero zaczynasz korzystać z usługi HDInsight, zapoznaj się z [informacjami o Apache Hadoop opartych na systemie Linux w](../hdinsight-hadoop-linux-information.md) dokumencie usługi HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Hadoop w usłudze HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md).

Dowolny z następujących elementów:
  * Windows PowerShell lub,
  * [Zwinięcie](https://curl.haxx.se/) z [JQ](https://stedolan.github.io/jq/)

## <a name="run-a-mapreduce-job"></a>Uruchamianie zadania MapReduce

> [!NOTE]  
> W przypadku korzystania z zwinięcie lub innej komunikacji REST z WebHCat należy uwierzytelnić żądania, podając nazwę użytkownika i hasło administratora klastra usługi HDInsight. Należy użyć nazwy klastra jako części identyfikatora URI, który jest używany do wysyłania żądań do serwera.
>
> Interfejs API REST jest zabezpieczony przy użyciu [podstawowego uwierzytelniania dostępu](https://en.wikipedia.org/wiki/Basic_access_authentication). Należy zawsze tworzyć żądania przy użyciu protokołu HTTPS, aby upewnić się, że poświadczenia są bezpiecznie wysyłane do serwera.

### <a name="curl"></a>Narzędzie Curl

1. Aby ułatwić sobie korzystanie z programu, Ustaw zmienne poniżej. Ten przykład jest oparty na środowisku systemu Windows, należy poprawić w miarę potrzeb w danym środowisku.

    ```cmd
    set CLUSTERNAME=
    set PASSWORD=
    ```

1. W wierszu polecenia wpisz następujące polecenie, aby sprawdzić możliwość nawiązania połączenia z klastrem usługi HDInsight:

    ```bash
    curl -u admin:%PASSWORD% -G https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/status
    ```

    W tym poleceniu są używane następujące parametry:

   * **-u**: wskazuje nazwę użytkownika i hasło używane do uwierzytelniania żądania
   * **-G**: wskazuje, że ta operacja jest żądaniem Get

   Początek identyfikatora URI, `https://CLUSTERNAME.azurehdinsight.net/templeton/v1` , jest taki sam dla wszystkich żądań.

    Otrzymasz odpowiedź podobną do następującej:

    ```json
    {"version":"v1","status":"ok"}
    ```

1. Aby przesłać zadanie MapReduce, użyj następującego polecenia. W razie potrzeby zmodyfikuj ścieżkę do **JQ** .

    ```cmd
    curl -u admin:%PASSWORD% -d user.name=admin ^
    -d jar=/example/jars/hadoop-mapreduce-examples.jar ^
    -d class=wordcount -d arg=/example/data/gutenberg/davinci.txt -d arg=/example/data/output ^
    https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/mapreduce/jar | ^
    C:\HDI\jq-win64.exe .id
    ```

    Koniec identyfikatora URI (/MapReduce/jar) informuje WebHCat, że to żądanie uruchamia zadanie MapReduce z klasy w pliku JAR. W tym poleceniu są używane następujące parametry:

   * **-d**: `-G` nie jest używany, dlatego żądanie jest domyślnie ustawiane jako metoda post. `-d` Określa wartości danych, które są wysyłane wraz z żądaniem.
     * **User.Name**: użytkownik, który uruchamia polecenie
     * **jar**: Lokalizacja pliku JAR, który zawiera klasę do uruchomienia
     * **Klasa**: Klasa, która zawiera logikę MapReduce
     * **ARG**: argumenty, które mają zostać przekazane do zadania MapReduce. W takim przypadku wejściowy plik tekstowy i katalog, które są używane na potrzeby danych wyjściowych

    To polecenie powinno zwrócić identyfikator zadania, którego można użyć do sprawdzenia stanu zadania: `job_1415651640909_0026` .

1. Aby sprawdzić stan zadania, użyj następującego polecenia. Zastąp wartość wartością `JOBID` **rzeczywistą** zwróconą w poprzednim kroku. W razie konieczności Popraw lokalizację **JQ** .

    ```cmd
    set JOBID=job_1415651640909_0026

    curl -G -u admin:%PASSWORD% -d user.name=admin https://%CLUSTERNAME%.azurehdinsight.net/templeton/v1/jobs/%JOBID% | ^
    C:\HDI\jq-win64.exe .status.state
    ```

### <a name="powershell"></a>PowerShell

1. Aby ułatwić sobie korzystanie z programu, Ustaw zmienne poniżej. Zastąp wartość `CLUSTERNAME` rzeczywistą nazwą klastra. Wykonaj polecenie i wprowadź hasło logowania klastra po wyświetleniu monitu.

    ```powershell
    $clusterName="CLUSTERNAME"
    $creds = Get-Credential -UserName admin -Message "Enter the cluster login password"
    ```

1. Użyj następującego polecenia, aby sprawdzić, czy można nawiązać połączenie z klastrem usługi HDInsight:

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clustername.azurehdinsight.net/templeton/v1/status" `
        -Credential $creds `
        -UseBasicParsing
    $resp.Content
    ```

    Otrzymasz odpowiedź podobną do następującej:

    ```json
    {"version":"v1","status":"ok"}
    ```

1. Aby przesłać zadanie MapReduce, użyj następującego polecenia:

    ```powershell
    $reqParams = @{}
    $reqParams."user.name" = "admin"
    $reqParams.jar = "/example/jars/hadoop-mapreduce-examples.jar"
    $reqParams.class = "wordcount"
    $reqParams.arg = @()
    $reqParams.arg += "/example/data/gutenberg/davinci.txt"
    $reqparams.arg += "/example/data/output"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/mapreduce/jar" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    Koniec identyfikatora URI (/MapReduce/jar) informuje WebHCat, że to żądanie uruchamia zadanie MapReduce z klasy w pliku JAR. W tym poleceniu są używane następujące parametry:

    * **User.Name**: użytkownik, który uruchamia polecenie
    * **jar**: Lokalizacja pliku JAR, który zawiera klasę do uruchomienia
    * **Klasa**: Klasa, która zawiera logikę MapReduce
    * **ARG**: argumenty, które mają zostać przekazane do zadania MapReduce. W takim przypadku wejściowy plik tekstowy i katalog, które są używane na potrzeby danych wyjściowych

   To polecenie powinno zwrócić identyfikator zadania, którego można użyć do sprawdzenia stanu zadania: `job_1415651640909_0026` .

1. Aby sprawdzić stan zadania, użyj następującego polecenia:

    ```powershell
    $reqParams=@{"user.name"="admin"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobID" `
       -Credential $creds `
       -Body $reqParams `
       -UseBasicParsing

    # ConvertFrom-JSON can't handle duplicate names with different case
    # So change one to prevent the error
    $fixDup=$resp.Content.Replace("jobID","job_ID")
    (ConvertFrom-Json $fixDup).status.state
    ```

### <a name="both-methods"></a>Obie metody

1. Jeśli zadanie zostało zakończone, zwróconym stanem jest `SUCCEEDED` .

1. Gdy stan zadania został zmieniony na `SUCCEEDED` , można pobrać wyniki zadania z usługi Azure Blob Storage. `statusdir`Parametr, który jest przesyłany przy użyciu zapytania, zawiera lokalizację pliku wyjściowego. W tym przykładzie lokalizacja to `/example/curl` . Ten adres przechowuje dane wyjściowe zadania w domyślnym magazynie klastrów w lokalizacji `/example/curl` .

Możesz wyświetlić i pobrać te pliki przy użyciu [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Aby uzyskać więcej informacji na temat korzystania z interfejsu wiersza polecenia platformy Azure do pracy z usługą Azure Blob Storage, zobacz [Szybki Start: Tworzenie, pobieranie i wyświetlanie listy obiektów BLOB za pomocą interfejsu wiersza polecenia platformy Azure](../../storage/blobs/storage-quickstart-blobs-cli.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje o innych sposobach pracy z usługą Hadoop w usłudze HDInsight:

* [Używanie MapReduce z usługą Apache Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md)
* [Używanie Apache Hive z Apache Hadoop w usłudze HDInsight](hdinsight-use-hive.md)

Aby uzyskać więcej informacji o interfejsie REST używanym w tym artykule, zobacz [odwołanie WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference).
