---
title: Używanie Apache Hadoop Hive z zwinięciem w usłudze HDInsight — Azure
description: Dowiedz się, jak zdalnie przesyłać zadania Apache wieprz do usługi Azure HDInsight przy użyciu zawieszania.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/06/2020
ms.openlocfilehash: 956406ec5ac99be5973f1928bbb89db10e68b339
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96000503"
---
# <a name="run-apache-hive-queries-with-apache-hadoop-in-hdinsight-using-rest"></a>Uruchamianie zapytań Apache Hive z Apache Hadoop w usłudze HDInsight przy użyciu usługi REST

[!INCLUDE [hive-selector](../../../includes/hdinsight-selector-use-hive.md)]

Dowiedz się, jak używać interfejsu API REST WebHCat do uruchamiania zapytań Apache Hive z Apache Hadoop w klastrze usługi Azure HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Hadoop w usłudze HDInsight. Zobacz Rozpoczynanie [pracy z usługą HDInsight w systemie Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Klient REST. W tym dokumencie jest stosowane [wywołanie Invoke-WebRequest](/powershell/module/microsoft.powershell.utility/invoke-webrequest) w programie Windows PowerShell i [zwinięcie](https://curl.haxx.se/) w witrynie [bash](/windows/wsl/install-win10).

* Jeśli używasz bash, potrzebujesz również JQ, procesora JSON wiersza polecenia.  Zobacz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) .

## <a name="base-uri-for-rest-api"></a>Podstawowy identyfikator URI interfejsu API REST

Podstawowy Uniform Resource Identifier (URI) dla interfejsu API REST w usłudze HDInsight to `https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME` , gdzie `CLUSTERNAME` jest nazwą klastra.  Nazwy klastra w identyfikatorach URI są **rozróżniane wielkości** liter.  W czasie, gdy nazwa klastra w w pełni kwalifikowana nazwa domeny (FQDN) w ramach identyfikatora URI ( `CLUSTERNAME.azurehdinsight.net` ) nie uwzględnia wielkości liter, inne wystąpienia w identyfikatorze URI uwzględniają wielkość liter.

## <a name="authentication"></a>Authentication

W przypadku korzystania z zawieszeń lub innej komunikacji REST z WebHCat należy uwierzytelnić żądania, podając nazwę użytkownika i hasło administratora klastra usługi HDInsight. Interfejs API REST jest zabezpieczony za pomocą [uwierzytelniania podstawowego](https://en.wikipedia.org/wiki/Basic_access_authentication). Aby upewnić się, że poświadczenia są bezpiecznie wysyłane do serwera, należy zawsze wysyłać żądania przy użyciu bezpiecznego protokołu HTTP (HTTPS).

### <a name="setup-preserve-credentials"></a>Instalacja (zachowywanie poświadczeń)

Zachowaj poświadczenia, aby uniknąć ich przetworzenia w każdym przykładzie.  Nazwa klastra zostanie zachowana w osobnym kroku.

**A. bash**  
Edytuj Poniższy skrypt, zastępując go `PASSWORD` rzeczywistym hasłem.  Następnie wprowadź polecenie.

```bash
export password='PASSWORD'
```  

**B. PowerShell** Wykonaj Poniższy kod, a następnie wprowadź swoje poświadczenia w oknie podręcznym:

```powershell
$creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
```

### <a name="identify-correctly-cased-cluster-name"></a>Zidentyfikuj poprawną nazwę klastra z wielkością liter

Rzeczywista wielkość liter nazwy klastra może być inna niż oczekiwano, w zależności od sposobu utworzenia klastra.  Kroki opisane w tym miejscu spowodują wyświetlenie rzeczywistej wielkości liter, a następnie zapisanie jej w zmiennej dla wszystkich późniejszych przykładów.

Edytuj poniższe skrypty, aby zastąpić je `CLUSTERNAME` nazwą klastra. Następnie wprowadź polecenie. (Nazwa klastra nazwy FQDN nie jest rozróżniana wielkość liter).

```bash
export clusterName=$(curl -u admin:$password -sS -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
echo $clusterName
```  

```powershell
# Identify properly cased cluster name
$resp = Invoke-WebRequest -Uri "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters" `
    -Credential $creds -UseBasicParsing
$clusterName = (ConvertFrom-Json $resp.Content).items.Clusters.cluster_name;

# Show cluster name
$clusterName
```

## <a name="run-a-hive-query"></a>Uruchomienie zapytania programu Hive

1. Aby sprawdzić, czy można nawiązać połączenie z klastrem usługi HDInsight, użyj jednego z następujących poleceń:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/status
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/status" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    Otrzymujesz odpowiedź podobną do następującego tekstu:

    ```json
    {"status":"ok","version":"v1"}
    ```

    W tym poleceniu są używane następujące parametry:

    * `-u` — Nazwa użytkownika i hasło używane do uwierzytelniania żądania.
    * `-G` -Wskazuje, że to żądanie jest operacją pobierania.

1. Początek adresu URL, `https://$CLUSTERNAME.azurehdinsight.net/templeton/v1` , jest taki sam dla wszystkich żądań. Ścieżka wskazuje, `/status` że żądanie ma zwrócić stan WebHCat (znany również jako Templeton) dla serwera. Możesz również zażądać wersji programu Hive przy użyciu następującego polecenia:

    ```bash
    curl -u admin:$password -G https://$clusterName.azurehdinsight.net/templeton/v1/version/hive
    ```

    ```powershell
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/version/hive" `
       -Credential $creds `
       -UseBasicParsing
    $resp.Content
    ```

    To żądanie zwraca odpowiedź podobną do następującego tekstu:

    ```json
    {"module":"hive","version":"1.2.1000.2.6.5.3008-11"}
    ```

1. Aby utworzyć tabelę o nazwie **log4jLogs**, wykonaj następujące czynności:

    ```bash
    jobid=$(curl -s -u admin:$password -d user.name=admin -d execute="DROP+TABLE+log4jLogs;CREATE+EXTERNAL+TABLE+log4jLogs(t1+string,t2+string,t3+string,t4+string,t5+string,t6+string,t7+string)+ROW+FORMAT+DELIMITED+FIELDS+TERMINATED+BY+' '+STORED+AS+TEXTFILE+LOCATION+'/example/data/';SELECT+t4+AS+sev,COUNT(*)+AS+count+FROM+log4jLogs+WHERE+t4+=+'[ERROR]'+AND+INPUT__FILE__NAME+LIKE+'%25.log'+GROUP+BY+t4;" -d statusdir="/example/rest" https://$clusterName.azurehdinsight.net/templeton/v1/hive | jq -r .id)
    echo $jobid
    ```

    ```powershell
    $reqParams = @{"user.name"="admin";"execute"="DROP TABLE log4jLogs;CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED BY ' ' STORED AS TEXTFILE LOCATION '/example/data/;SELECT t4 AS sev,COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;";"statusdir"="/example/rest"}
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/templeton/v1/hive" `
       -Credential $creds `
       -Body $reqParams `
       -Method POST `
       -UseBasicParsing
    $jobID = (ConvertFrom-Json $resp.Content).id
    $jobID
    ```

    To żądanie używa metody POST, która wysyła dane w ramach żądania do interfejsu API REST. Następujące wartości danych są wysyłane z żądaniem:

     * `user.name` — Użytkownik, który uruchamia polecenie.
     * `execute` -Instrukcje HiveQL do wykonania.
     * `statusdir` -Katalog, w którym jest zapisywana wartość stanu tego zadania.

   Te instrukcje wykonują następujące czynności:

   * `DROP TABLE` -Jeśli tabela już istnieje, zostanie usunięta.
   * `CREATE EXTERNAL TABLE` -Tworzy nową tabelę "External" w gałęzi. Tabele zewnętrzne przechowują tylko definicję tabeli w programie Hive. Dane pozostaną w oryginalnej lokalizacji.

     > [!NOTE]  
     > Tabele zewnętrzne powinny być używane, gdy oczekuje się, że dane podstawowe mają być aktualizowane przez zewnętrzne źródło. Na przykład proces automatycznego przekazywania danych lub inna operacja MapReduce.
     >
     > Porzucenie tabeli **zewnętrznej nie powoduje usunięcia danych** , tylko definicji tabeli.

   * `ROW FORMAT` — Jak są formatowane dane. Pola w każdym dzienniku są oddzielone spacjami.
   * `STORED AS TEXTFILE LOCATION` — Miejsce, w którym dane są przechowywane (przykład/katalog danych) i są przechowywane jako tekst.
   * `SELECT` -Wybiera liczbę wszystkich wierszy, w których kolumna **T4** zawiera wartość **[Error]**. Ta instrukcja zwraca wartość **3** , ponieważ istnieją trzy wiersze, które zawierają tę wartość.

     > [!NOTE]  
     > Należy zauważyć, że odstępy między instrukcjami HiveQL są zastępowane `+` znakiem, gdy jest używany z zwinięciem. Ujęte w cudzysłów wartości zawierające spację, takie jak ogranicznik, nie powinny być zastępowane przez `+` .

      To polecenie zwraca identyfikator zadania, którego można użyć do sprawdzenia stanu zadania.

1. Aby sprawdzić stan zadania, użyj następującego polecenia:

    ```bash
    curl -u admin:$password -d user.name=admin -G https://$clusterName.azurehdinsight.net/templeton/v1/jobs/$jobid | jq .status.state
    ```

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

    Jeśli zadanie zostało zakończone, stan zostanie **zakończony pomyślnie**.

1. Po zmianie stanu zadania na **powodzenie** można pobrać wyniki zadania z usługi Azure Blob Storage. `statusdir`Parametr przesłany z zapytaniem zawiera lokalizację pliku wyjściowego; w tym przypadku `/example/rest` . Ten adres przechowuje dane wyjściowe w `example/curl` katalogu w domyślnym magazynie klastrów.

    Możesz wyświetlić i pobrać te pliki przy użyciu [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Aby uzyskać więcej informacji na temat korzystania z interfejsu wiersza polecenia platformy Azure z usługą Azure Storage, zobacz dokument [Używanie interfejsu wiersza polecenia platformy Azure z usługą Azure Storage](../../storage/blobs/storage-quickstart-blobs-cli.md) .

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat innych sposobów pracy z usługą Hadoop w usłudze HDInsight:

* [Używanie Apache Hive z Apache Hadoop w usłudze HDInsight](hdinsight-use-hive.md)
* [Używanie MapReduce z usługą Apache Hadoop w usłudze HDInsight](hdinsight-use-mapreduce.md)

Aby uzyskać więcej informacji na temat interfejsu API REST używanego w tym dokumencie, zobacz dokument [referencyjny WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat+Reference) .