---
title: Przesyłanie zadań do klastra Spark w usłudze Azure HDInsight za pomocą usługi Livy Spark
description: Dowiedz się, jak Apache Spark używać interfejsu API REST do zdalnego przesyłania zadań platformy Spark do klastra usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: e5ed8fd2eba175a170c12c032e7c6ecf6a926b64
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86084617"
---
# <a name="use-apache-spark-rest-api-to-submit-remote-jobs-to-an-hdinsight-spark-cluster"></a>Przesyłanie zadań zdalnych do klastra usługi HDInsight Spark przy użyciu interfejsu API REST platformy Apache Spark

Dowiedz się, jak korzystać z usługi [Apache usługi Livy](https://livy.incubator.apache.org/), interfejsu API REST Apache Spark, który jest używany do przesyłania zadań zdalnych do klastra Azure HDInsight Spark. Aby uzyskać szczegółową dokumentację, zobacz [Apache usługi Livy](https://livy.incubator.apache.org/docs/latest/rest-api.html).

Można użyć usługi Livy do uruchamiania interakcyjnych powłok Spark lub przesyłania zadań wsadowych do uruchamiania na platformie Spark. W tym artykule omówiono użycie usługi Livy do przesyłania zadań wsadowych. Fragmenty kodu w tym artykule służą do wykonywania wywołań interfejsu API REST do punktu końcowego usługi Livy Spark.

## <a name="prerequisites"></a>Wymagania wstępne

Klaster Apache Spark w usłudze HDInsight. Aby uzyskać instrukcje, zobacz [Tworzenie klastra platformy Apache Spark w usłudze Azure HDInsight](apache-spark-jupyter-spark-sql.md).

## <a name="submit-an-apache-livy-spark-batch-job"></a>Prześlij zadanie wsadowe Apache usługi Livy Spark

Przed przesłaniem zadania wsadowego należy przekazać aplikację jar w magazynie klastra skojarzonym z klastrem. Możesz to zrobić za pomocą narzędzia wiersza polecenia [AzCopy](../../storage/common/storage-use-azcopy.md). Istnieją różne klientów, których można użyć do przekazywania danych. Więcej informacji na ten temat można znaleźć w artykule [Przekazywanie danych dla zadań Apache Hadoop w usłudze HDInsight](../hdinsight-upload-data.md).

```cmd
curl -k --user "admin:password" -v -H "Content-Type: application/json" -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches' -H "X-Requested-By: admin"
```

### <a name="examples"></a>Przykłady

* Jeśli plik JAR znajduje się w magazynie klastra (WASBS)

    ```cmd  
    curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

* Jeśli chcesz przekazać nazwę pliku JAR i nazwę klasy jako część pliku wejściowego (w tym przykładzie input.txt)

    ```cmd
    curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

## <a name="get-information-on-livy-spark-batches-running-on-the-cluster"></a>Uzyskaj informacje na temat usługi Livy Spark uruchomionych w klastrze

Składnia:

```cmd
curl -k --user "admin:password" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"
```

### <a name="examples"></a>Przykłady

* Jeśli chcesz pobrać wszystkie usługi Livy Spark uruchomione w klastrze:

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"
    ```

* Jeśli chcesz pobrać konkretną partię z danym IDENTYFIKATORem partii

    ```cmd
    curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"
    ```

## <a name="delete-a-livy-spark-batch-job"></a>Usuwanie zadania wsadowego usługi Livy Spark

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"
```

### <a name="example"></a>Przykład

Usuwanie zadania wsadowego z IDENTYFIKATORem partii `5` .

```cmd
curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/5"
```

## <a name="livy-spark-and-high-availability"></a>Usługi Livy Spark i wysoka dostępność

Usługi Livy zapewnia wysoką dostępność dla zadań platformy Spark uruchomionych w klastrze. Oto kilka przykładów.

* Jeśli usługa usługi Livy zostanie wyłączona po przesłaniu zadania zdalnie do klastra Spark, zadanie będzie nadal uruchamiane w tle. Po utworzeniu kopii zapasowej usługi Livy przywraca stan zadania i raportuje go z powrotem.
* Notesy Jupyter dla usługi HDInsight są obsługiwane przez usługi Livy w zapleczu. Jeśli w notesie uruchomiono zadanie platformy Spark, a usługa usługi Livy zostanie ponownie uruchomiona, Notes nadal uruchamia komórki kodu.

## <a name="show-me-an-example"></a>Pokaż przykład

W tej sekcji przedstawiono przykłady użycia usługi Livy Spark do przesyłania zadań wsadowych, monitorowania postępu zadania i usuwania go. Używana w tym przykładzie aplikacja została opracowana w artykule [Tworzenie autonomicznej aplikacji Scala i uruchamianie jej w klastrze usługi HDInsight Spark](apache-spark-create-standalone-application.md). W tym miejscu przyjęto następujące kroki:

* Skopiowano już za pośrednictwem aplikacji jar do konta magazynu skojarzonego z klastrem.
* Zainstalowano na komputerze, na którym próbujesz wykonać te kroki.

Wykonaj następujące czynności:

1. Aby ułatwić sobie korzystanie z programu, Ustaw zmienne środowiskowe. Ten przykład jest oparty na środowisku systemu Windows, Popraw zmienne odpowiednio do potrzeb środowiska. Zamień `CLUSTERNAME` i `PASSWORD` z odpowiednimi wartościami.

    ```cmd
    set clustername=CLUSTERNAME
    set password=PASSWORD
    ```

1. Sprawdź, czy w klastrze działa usługi Livy Spark. Można to zrobić, pobierając listę uruchomionych partii. Jeśli uruchamiasz zadanie przy użyciu usługi Livy po raz pierwszy, dane wyjściowe powinny zwracać zero.

    ```cmd
    curl -k --user "admin:%password%" -v -X GET "https://%clustername%.azurehdinsight.net/livy/batches"
    ```

    Powinno zostać wyświetlone dane wyjściowe podobne do następującego fragmentu kodu:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:47:53 GMT
    < Content-Length: 34
    <
    {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Zwróć uwagę, jak ostatni wiersz w danych wyjściowych wskazuje **całkowitą wartość: 0**, która sugeruje brak uruchomionych partii.

1. Pozwól nam teraz przesłać zadanie wsadowe. Poniższy fragment kodu używa pliku wejściowego (input.txt) do przekazywania nazwy jar i nazwy klasy jako parametrów. Wykonanie tych kroków z komputera z systemem Windows przy użyciu pliku wejściowego jest zalecanym rozwiązaniem.

    ```cmd
    curl -k --user "admin:%password%" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://%clustername%.azurehdinsight.net/livy/batches" -H "X-Requested-By: admin"
    ```

    Parametry w pliku **input.txt** są zdefiniowane w następujący sposób:

    ```text
    { "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }
    ```

    Powinny pojawić się dane wyjściowe podobne do następującego fragmentu kodu:

    ```output
    < HTTP/1.1 201 Created
    < Content-Type: application/json; charset=UTF-8
    < Location: /0
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:51:30 GMT
    < Content-Length: 36
    <
    {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Zwróć uwagę, jak ostatni wiersz danych wyjściowych mówi o **stanie: uruchamianie**. Informacja o **identyfikatorze: 0**. Tutaj, **0** to identyfikator wsadu.

1. Teraz można pobrać stan tej konkretnej partii przy użyciu identyfikatora partii.

    ```cmd
    curl -k --user "admin:%password%" -v -X GET "https://%clustername%.azurehdinsight.net/livy/batches/0"
    ```

    Powinny pojawić się dane wyjściowe podobne do następującego fragmentu kodu:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Fri, 20 Nov 2015 23:54:42 GMT
    < Content-Length: 509
    <
    {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://myspar.lpel.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Dane wyjściowe przedstawiają teraz **stan: sukces**, który sugeruje, że zadanie zostało pomyślnie ukończone.

1. Jeśli chcesz, możesz teraz usunąć partię.

    ```cmd
    curl -k --user "admin:%password%" -v -X DELETE "https://%clustername%.azurehdinsight.net/livy/batches/0"
    ```

    Powinny pojawić się dane wyjściowe podobne do następującego fragmentu kodu:

    ```output
    < HTTP/1.1 200 OK
    < Content-Type: application/json; charset=UTF-8
    < Server: Microsoft-IIS/8.5
    < X-Powered-By: ARR/2.5
    < X-Powered-By: ASP.NET
    < Date: Sat, 21 Nov 2015 18:51:54 GMT
    < Content-Length: 17
    <
    {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact
    ```

    Ostatni wiersz danych wyjściowych wskazuje, że partia została pomyślnie usunięta. Usuwanie zadania, gdy jest ono uruchomione, również przerywa zadanie. Usunięcie zadania, które zakończyło się pomyślnie lub w inny sposób, spowoduje całkowite usunięcie informacji o zadaniu.

## <a name="updates-to-livy-configuration-starting-with-hdinsight-35-version"></a>Aktualizacje konfiguracji usługi Livy, począwszy od wersji 3,5 usługi HDInsight

Klastry usługi HDInsight 3,5 i nowsze — domyślnie Wyłącz używanie lokalnych ścieżek plików do uzyskiwania dostępu do przykładowych plików danych lub Jars. Zachęcamy do korzystania ze `wasbs://` ścieżki zamiast uzyskiwać dostęp do Jars lub przykładowych plików danych z klastra.

## <a name="submitting-livy-jobs-for-a-cluster-within-an-azure-virtual-network"></a>Przesyłanie zadań usługi Livy dla klastra w sieci wirtualnej platformy Azure

Jeśli łączysz się z klastrem usługi HDInsight Spark z poziomu Virtual Network platformy Azure, możesz połączyć się bezpośrednio z usługą usługi Livy w klastrze. W takim przypadku adresem URL dla punktu końcowego usługi Livy jest `http://<IP address of the headnode>:8998/batches` . W tym miejscu **8998** jest port, na którym usługi Livy działa w klastrze węzła głównego. Aby uzyskać więcej informacji na temat uzyskiwania dostępu do usług na portach innych niż publiczne, zobacz [porty używane przez usługi Apache Hadoop w usłudze HDInsight](../hdinsight-hadoop-port-settings-for-services.md).

## <a name="next-steps"></a>Następne kroki

* [Dokumentacja interfejsu API REST usługi Apache usługi Livy](https://livy.incubator.apache.org/docs/latest/rest-api.html)
* [Zarządzanie zasobami klastra Apache Spark w usłudze Azure HDInsight](apache-spark-resource-manager.md)
* [Śledzenie i debugowanie zadań uruchamianych w klastrze Apache Spark w usłudze HDInsight](apache-spark-job-debugging.md)
