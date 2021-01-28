---
title: 'Samouczek: Usługa HDInsight Apache Storm do magazynu — Azure/Data Lake'
description: Samouczek — informacje na temat używania Apache Storm do zapisywania do magazynu zgodnego z systemem plików HDFS dla usługi Azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/24/2019
ms.openlocfilehash: 4e648c57be699620e669ce7db0845dad2b876095
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98932562"
---
# <a name="tutorial-write-to-apache-hadoop-hdfs-from-apache-storm-on-azure-hdinsight"></a>Samouczek: zapisywanie do Apache Hadoop HDFS z Apache Storm w usłudze Azure HDInsight

W tym samouczku pokazano, jak za pomocą Apache Storm zapisywać dane do magazynu zgodnego z systemem plików HDFS używanego przez Apache Storm w usłudze HDInsight. Usługa HDInsight może używać usługi Azure Storage i Azure Data Lake Storage jako magazynu zgodnego z systemem plików HDFS. Burza udostępnia składnik [HdfsBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/hdfs/bolt/HdfsBolt.html) , który zapisuje dane w systemie plików HDFS. Ten dokument zawiera informacje na temat zapisywania do dowolnego typu magazynu z HdfsBolt.

Przykładowa topologia używana w tym dokumencie opiera się na składnikach, które są dołączone do burzy w usłudze HDInsight. Może wymagać modyfikacji do pracy z Azure Data Lake Storage, gdy jest używany z innymi klastrami Apache Storm.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie klastra z akcją skryptu
> * Kompilowanie i pakowanie topologii
> * Wdrażanie i uruchamianie topologii
> * Wyświetlanie danych wyjściowych
> * Zatrzymywanie topologii

## <a name="prerequisites"></a>Wymagania wstępne

* [Java developer Kit (JDK) w wersji 8](/azure/developer/java/fundamentals/java-jdk-long-term-support)

* Pakiet [Apache Maven](https://maven.apache.org/download.cgi) został prawidłowo [zainstalowany](https://maven.apache.org/install.html) zgodnie z usługą Apache.  Maven to system kompilacji projektu dla projektów języka Java.

* Klient SSH. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* [Schemat identyfikatora URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme) magazynu podstawowego klastrów. Będzie to możliwe `wasb://` w przypadku usługi Azure Storage, `abfs://` Azure Data Lake Storage Gen2 lub `adl://` Azure Data Lake Storage Gen1. Jeśli w usłudze Azure Storage włączono opcję bezpiecznego transferu, identyfikator URI mógłby być `wasbs://` .  Zobacz również [bezpieczny transfer](../../storage/common/storage-require-secure-transfer.md).

### <a name="example-configuration"></a>Przykładowa konfiguracja

Następujący YAML to fragment z `resources/writetohdfs.yaml` pliku zawartego w przykładzie. Ten plik definiuje topologię burzy przy użyciu struktury [strumienia](https://storm.apache.org/releases/current/flux.html) dla Apache Storm.

```yaml
components:
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1000

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "MB"

  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# spout definitions
spouts:
  - id: "tick-spout"
    className: "com.microsoft.example.TickSpout"
    parallelism: 1


# bolt definitions
bolts:
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
```

Ta YAML definiuje następujące elementy:

* `syncPolicy`: Określa, kiedy pliki są synchronizowane/opróżniane do systemu plików. W tym przykładzie każde 1000 krotek.
* `fileNameFormat`: Określa ścieżkę i wzorzec nazwy pliku, który ma być używany podczas pisania plików. W tym przykładzie ścieżka jest zapewniana w czasie wykonywania przy użyciu filtru, a rozszerzenie pliku to `.txt` .
* `recordFormat`: Definiuje wewnętrzny format plików, które są zapisywane. W tym przykładzie pola są rozdzielane `|` znakami.
* `rotationPolicy`: Określa, kiedy obracać pliki. W tym przykładzie nie jest przeprowadzane obracanie.
* `hdfs-bolt`: Używa poprzednich składników jako parametrów konfiguracji dla `HdfsBolt` klasy.

Aby uzyskać więcej informacji na temat struktury strumienia, zobacz [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html) .

## <a name="configure-the-cluster"></a>Konfigurowanie klastra

Domyślnie burza w usłudze HDInsight nie obejmuje składników, które są `HdfsBolt` używane do komunikacji z usługą Azure Storage lub Data Lake Storage w ścieżce klas. Aby dodać te składniki do `extlib` katalogu na potrzeby burzy w klastrze, należy użyć następującej akcji skryptu:

| Właściwość | Wartość |
|---|---|
|Typ skryptu |-Niestandardowe|
|Identyfikator URI skryptu bash |`https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh`|
|Typy węzłów |Nimbus, Nadzorca|
|Parametry |Brak|

Aby uzyskać informacje na temat używania tego skryptu z klastrem, zobacz [Dostosowywanie klastrów usługi HDInsight za pomocą dokumentu akcje skryptu](./../hdinsight-hadoop-customize-cluster-linux.md) .

## <a name="build-and-package-the-topology"></a>Kompilowanie i pakowanie topologii

1. Pobierz przykładowy projekt z [https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store](https://github.com/Azure-Samples/hdinsight-storm-azure-data-lake-store) do środowiska deweloperskiego.

2. W wierszu polecenia, terminalu lub sesji powłoki zmień katalogi na katalog główny pobranego projektu. Aby skompilować i utworzyć pakiet topologii, użyj następującego polecenia:

    ```cmd
    mvn compile package
    ```

    Po zakończeniu kompilacji i pakowania istnieje nowy katalog o nazwie `target` , który zawiera plik o nazwie `StormToHdfs-1.0-SNAPSHOT.jar` . Ten plik zawiera skompilowaną topologię.

## <a name="deploy-and-run-the-topology"></a>Wdrażanie i uruchamianie topologii

1. Użyj następującego polecenia, aby skopiować topologię do klastra usługi HDInsight. Zamień `CLUSTERNAME` na nazwę klastra.

    ```cmd
    scp target\StormToHdfs-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:StormToHdfs-1.0-SNAPSHOT.jar
    ```

1. Po zakończeniu przekazywania Użyj następującego polecenia, aby nawiązać połączenie z klastrem usługi HDInsight przy użyciu protokołu SSH. Zamień `CLUSTERNAME` na nazwę klastra.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Po nawiązaniu połączenia użyj następującego polecenia, aby utworzyć plik o nazwie `dev.properties` :

    ```bash
    nano dev.properties
    ```

1. Użyj następującego tekstu jako zawartości `dev.properties` pliku. Popraw miarę potrzeb w zależności od [schematu identyfikatora URI](../hdinsight-hadoop-linux-information.md#URI-and-scheme).

    ```
    hdfs.write.dir: /stormdata/
    hdfs.url: wasbs:///
    ```

    Aby zapisać plik, użyj __kombinacji klawiszy Ctrl + X__, a następnie __Y__ i __klawisza ENTER__. Wartości w tym pliku ustawiają adres URL magazynu i nazwę katalogu, w którym są zapisywane dane.

1. Użyj następującego polecenia, aby uruchomić topologię:

    ```bash
    storm jar StormToHdfs-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writetohdfs.yaml --filter dev.properties
    ```

    To polecenie uruchamia topologię przy użyciu platformy strumieni przez przesłanie jej do węzła Nimbus klastra. Topologia jest definiowana przez `writetohdfs.yaml` plik znajdujący się w JAR. `dev.properties`Plik jest przesyłany jako filtr, a wartości zawarte w pliku są odczytywane przez topologię.

## <a name="view-output-data"></a>Wyświetlanie danych wyjściowych

Aby wyświetlić dane, użyj następującego polecenia:

  ```bash
  hdfs dfs -ls /stormdata/
  ```

Zostanie wyświetlona lista plików utworzonych przez tę topologię. Poniższa lista stanowi przykład danych zwracanych przez poprzednie polecenia:

```output
Found 23 items
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-0-1561407909895.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-1-1561407915577.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-10-1561407943327.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-11-1561407946312.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-12-1561407949320.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-13-1561407952662.txt
-rw-r--r--   1 storm supergroup    5242880 2019-06-24 20:25 /stormdata/hdfs-bolt-3-14-1561407955502.txt
```

## <a name="stop-the-topology"></a>Zatrzymywanie topologii

Topologie burzy są uruchamiane do czasu zatrzymania lub klaster zostanie usunięty. Aby zatrzymać topologię, użyj następującego polecenia:

```bash
storm kill hdfswriter
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby wyczyścić zasoby utworzone w tym samouczku, możesz usunąć grupę zasobów. Usunięcie grupy zasobów powoduje również usunięcie skojarzonego klastra usługi HDInsight i wszystkich innych zasobów skojarzonych z tą grupą zasobów.

Aby usunąć grupę zasobów za pomocą witryny Azure Portal:

1. W witrynie Azure Portal rozwiń menu po lewej stronie, aby otworzyć menu usług, a następnie wybierz pozycję __Grupy zasobów__, aby wyświetlić listę grup zasobów.
2. Znajdź grupę zasobów do usunięcia, a następnie kliknij prawym przyciskiem myszy przycisk __Więcej (...)__ po prawej stronie listy.
3. Wybierz pozycję __Usuń grupę zasobów__ i potwierdź.

## <a name="next-steps"></a>Następne kroki

W ramach tego samouczka nauczysz się używać Apache Storm do zapisywania danych do magazynu zgodnego z systemem plików HDFS używanego przez Apache Storm w usłudze HDInsight.

> [!div class="nextstepaction"]
> Odkryj inne [przykłady Apache Storm dla usługi HDInsight](apache-storm-example-topology.md)