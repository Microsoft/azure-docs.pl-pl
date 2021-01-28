---
title: Rozwiązywanie problemów z systemem plików HDFS w usłudze Azure HDInsight
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące pracy z systemem plików HDFS i usługą Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 04/27/2020
ms.custom: seodec18
ms.openlocfilehash: 0be7805493e5acc41254c57ca912b5a2ecf02dae
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98931455"
---
# <a name="troubleshoot-apache-hadoop-hdfs-by-using-azure-hdinsight"></a>Rozwiązywanie problemów z systemem HDFS usługi Apache Hadoop za pomocą usługi Azure HDInsight

Poznaj najważniejsze problemy i rozwiązania podczas pracy z usługą Hadoop rozproszony system plików (HDFS). Aby zapoznać się z pełną listą poleceń, zobacz [Przewodnik po poleceniach](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HDFSCommands.html) [systemu plików HDFS i podręczniku](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html)dotyczącego powłoki.

## <a name="how-do-i-access-the-local-hdfs-from-inside-a-cluster"></a><a name="how-do-i-access-local-hdfs-from-inside-a-cluster"></a>Jak mogę uzyskać dostęp do lokalnego systemu plików HDFS z klastra?

### <a name="issue"></a>Problem

Dostęp do lokalnego systemu plików HDFS z poziomu wiersza polecenia i kodu aplikacji zamiast programu przy użyciu magazynu obiektów blob platformy Azure lub Azure Data Lake Storage z klastra usługi HDInsight.

### <a name="resolution-steps"></a>Kroki rozwiązania

1. W wierszu polecenia Użyj `hdfs dfs -D "fs.default.name=hdfs://mycluster/" ...` dosłownie, jak w poniższym poleceniu:

    ```output
    hdfs dfs -D "fs.default.name=hdfs://mycluster/" -ls /
    Found 3 items
    drwxr-xr-x   - hdiuser hdfs          0 2017-03-24 14:12 /EventCheckpoint-30-8-24-11102016-01
    drwx-wx-wx   - hive    hdfs          0 2016-11-10 18:42 /tmp
    drwx------   - hdiuser hdfs          0 2016-11-10 22:22 /user
    ```

2. Z kodu źródłowego Użyj identyfikatora URI `hdfs://mycluster/` dosłownie, tak jak w przypadku następującej przykładowej aplikacji:

    ```Java
    import java.io.IOException;
    import java.net.URI;
    import org.apache.commons.io.IOUtils;
    import org.apache.hadoop.conf.Configuration;
    import org.apache.hadoop.fs.*;

    public class JavaUnitTests {

        public static void main(String[] args) throws Exception {

            Configuration conf = new Configuration();
            String hdfsUri = "hdfs://mycluster/";
            conf.set("fs.defaultFS", hdfsUri);
            FileSystem fileSystem = FileSystem.get(URI.create(hdfsUri), conf);
            RemoteIterator<LocatedFileStatus> fileStatusIterator = fileSystem.listFiles(new Path("/tmp"), true);
            while(fileStatusIterator.hasNext()) {
                System.out.println(fileStatusIterator.next().getPath().toString());
            }
        }
    }
    ```

3. Uruchom skompilowany plik JAR (na przykład plik o nazwie `java-unit-tests-1.0.jar` ) w klastrze usługi HDInsight przy użyciu następującego polecenia:

    ```apache
    hadoop jar java-unit-tests-1.0.jar JavaUnitTests
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.info
    hdfs://mycluster/tmp/hive/hive/5d9cf301-2503-48c7-9963-923fb5ef79a7/inuse.lck
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.info
    hdfs://mycluster/tmp/hive/hive/a0be04ea-ae01-4cc4-b56d-f263baf2e314/inuse.lck
    ```

## <a name="storage-exception-for-write-on-blob"></a>Wyjątek magazynu do zapisu na obiekcie blob

### <a name="issue"></a>Problem

W przypadku używania `hadoop` `hdfs dfs` poleceń lub do zapisywania plików, które są ~ 12 GB lub większe w klastrze HBase, może wystąpić następujący błąd:

```error
ERROR azure.NativeAzureFileSystem: Encountered Storage Exception for write on Blob : example/test_large_file.bin._COPYING_ Exception details: null Error Code : RequestBodyTooLarge
copyFromLocal: java.io.IOException
        at com.microsoft.azure.storage.core.Utility.initIOException(Utility.java:661)
        at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:366)
        at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:350)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.Executors$RunnableAdapter.call(Executors.java:471)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
Caused by: com.microsoft.azure.storage.StorageException: The request body is too large and exceeds the maximum permissible limit.
        at com.microsoft.azure.storage.StorageException.translateException(StorageException.java:89)
        at com.microsoft.azure.storage.core.StorageRequest.materializeException(StorageRequest.java:307)
        at com.microsoft.azure.storage.core.ExecutionEngine.executeWithRetry(ExecutionEngine.java:182)
        at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlockInternal(CloudBlockBlob.java:816)
        at com.microsoft.azure.storage.blob.CloudBlockBlob.uploadBlock(CloudBlockBlob.java:788)
        at com.microsoft.azure.storage.blob.BlobOutputStream$1.call(BlobOutputStream.java:354)
        ... 7 more
```

### <a name="cause"></a>Przyczyna

HBase w klastrach usługi HDInsight domyślnie ma rozmiar bloku 256 KB podczas zapisywania w usłudze Azure Storage. Chociaż działa w przypadku interfejsów API HBase lub interfejsów API REST, powoduje to błąd podczas korzystania z `hadoop` `hdfs dfs` narzędzi wiersza polecenia lub.

### <a name="resolution"></a>Rozwiązanie

Użyj `fs.azure.write.request.size` , aby określić większy rozmiar bloku. Tę modyfikację można wykonać przy użyciu `-D` parametru. Następujące polecenie jest przykładem przy użyciu tego parametru z `hadoop` poleceniem:

```bash
hadoop -fs -D fs.azure.write.request.size=4194304 -copyFromLocal test_large_file.bin /example/data
```

Możesz również zwiększyć wartość `fs.azure.write.request.size` globalnie za pomocą platformy Apache Ambari. Poniższe kroki umożliwiają zmianę wartości w interfejsie użytkownika sieci Web Ambari:

1. W przeglądarce przejdź do interfejsu użytkownika sieci Web Ambari dla klastra. Adres URL to `https://CLUSTERNAME.azurehdinsight.net` , gdzie `CLUSTERNAME` jest nazwą klastra. Po wyświetleniu monitu wprowadź nazwę i hasło administratora klastra.
2. Z lewej strony ekranu wybierz pozycję **HDFS**, a następnie **Wybierz kartę konfiguracje** .
3. W polu **Filtr...** wprowadź wartość `fs.azure.write.request.size` .
4. Zmień wartość z 262144 (256 KB) na nową wartość. Na przykład 4194304 (4 MB).

    ![Obraz zmiany wartości za pomocą interfejsu użytkownika sieci Web Ambari](./media/hdinsight-troubleshoot-hdfs/hbase-change-block-write-size.png)

Aby uzyskać więcej informacji na temat korzystania z programu Ambari, zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="du"></a>du

[`-du`](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#du)Polecenie wyświetla rozmiary plików i katalogów znajdujących się w danym katalogu lub długość pliku w przypadku, gdy jest tylko plikiem.

`-s`Opcja generuje zagregowane podsumowanie długości plików, które są wyświetlane.  
`-h`Opcja formatuje rozmiary plików.

Przykład:

```bash
hdfs dfs -du -s -h hdfs://mycluster/
hdfs dfs -du -s -h hdfs://mycluster/tmp
```

## <a name="rm"></a>RM

Polecenie [-RM](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/FileSystemShell.html#rm) usuwa pliki określone jako argumenty.

Przykład:

```bash
hdfs dfs -rm hdfs://mycluster/tmp/testfile
```

## <a name="next-steps"></a>Następne kroki

[!INCLUDE [troubleshooting next steps](../../includes/hdinsight-troubleshooting-next-steps.md)]