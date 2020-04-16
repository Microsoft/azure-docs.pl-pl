---
title: Przekształcanie danych przy użyciu działania platformy Spark
description: Dowiedz się, jak przekształcać dane, uruchamiając programy Platformy Spark z potoku fabryki danych platformy Azure przy użyciu działania platformy Spark.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 05/31/2018
ms.openlocfilehash: c39575e8ea60a091124c633f8958ec36e8a61885
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418851"
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Przekształcanie danych przy użyciu aktywności platformy Spark w usłudze Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-spark.md)
> * [Bieżąca wersja](transform-data-using-spark.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Działanie platformy Spark w [potoku](concepts-pipelines-activities.md) usługi Data Factory wykonuje program Spark na [własną](compute-linked-services.md#azure-hdinsight-linked-service) lub [na żądanie](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) klastra HDInsight. W tym artykule opiera się na [działaniach transformacji danych,](transform-data.md) który przedstawia ogólny przegląd transformacji danych i obsługiwanych działań transformacji. Korzystając z usługi połączonej platformy Spark na żądanie, usługa Data Factory automatycznie tworzy klaster platformy Spark, aby przetwarzać dane, a następnie usuwa klaster po zakończeniu przetwarzania. 


## <a name="spark-activity-properties"></a>Właściwości aktywności iskrowej
Oto przykładowa definicja JSON działania platformy Spark:    

```json
{
    "name": "Spark Activity",
    "description": "Description",
    "type": "HDInsightSpark",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "sparkJobLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "rootPath": "adfspark",
        "entryFilePath": "test.py",
        "sparkConfig": {
            "ConfigItem1": "Value"
        },
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ]
    }
}
```

W poniższej tabeli opisano właściwości JSON używane w definicji JSON:

| Właściwość              | Opis                              | Wymagany |
| --------------------- | ---------------------------------------- | -------- |
| name                  | Nazwa działania w potoku.    | Tak      |
| description           | Tekst opisujący działanie.  | Nie       |
| type                  | W przypadku działania platformy Spark typem działania jest HDInsightSpark. | Tak      |
| linkedServiceName     | Nazwa usługi połączonej hdinsight Spark, na której działa program Spark. Aby dowiedzieć się więcej o tej połączonej usłudze, zobacz Artykuł [dotyczący powiązanych usług obliczeń.](compute-linked-services.md) | Tak      |
| Usługa SparkJobLinked | Usługa połączona usługi Azure Storage, która przechowuje plik zadania platformy Spark, zależności i dzienniki.  Jeśli nie określisz wartości dla tej właściwości, używany jest magazyn skojarzony z klastrem HDInsight. Wartość tej właściwości może być tylko usługą linked Usługi Azure Storage. | Nie       |
| ścieżka rootPath              | Kontener i folder obiektów Blob platformy Azure zawierający plik Spark. W nazwie pliku rozróżniana jest wielkość liter. Szczegółowe informacje na temat struktury tego folderu można znaleźć w sekcji struktury folderów (następna sekcja). | Tak      |
| wpisFilePath         | Ścieżka względna do folderu głównego kodu/pakietu Platformy Spark. Plik wpisowy musi być plikiem Pythona lub plikiem jar. | Tak      |
| Classname             | Główna klasa aplikacji Java/Spark      | Nie       |
| Argumenty             | Lista argumentów wiersza polecenia programu Spark. | Nie       |
| proxyUżytnik             | Konto użytkownika do personifikacji w celu wykonania programu Spark | Nie       |
| sparkConfig (niem.           | Określ wartości właściwości konfiguracji platformy Spark wymienione w temacie: [Konfiguracja platformy Spark - Właściwości aplikacji](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Nie       |
| getDebugInfo          | Określa, kiedy pliki dziennika platformy Spark są kopiowane do magazynu platformy Azure używane przez klaster HDInsight (lub) określone przez sparkJobLinkedService. Dozwolone wartości: Brak, Zawsze lub Błąd. Wartość domyślna: None. | Nie       |

## <a name="folder-structure"></a>Struktura folderów
Zadania spark są bardziej rozszerzalne niż zadania Pig/Hive. W przypadku zadań platformy Spark można podać wiele zależności, takich jak pakiety jar (umieszczone w java CLASSPATH), pliki python (umieszczone na PYTHONPATH) i inne pliki.

Utwórz następującą strukturę folderów w magazynie obiektów Blob platformy Azure, do którego odwołuje się usługa połączona HDInsight. Następnie należy przesłać pliki zależne do odpowiednich folderów podrzędnych w folderze głównym reprezentowanym przez **entryFilePath**. Na przykład przekaż pliki języka Python do podfolderu pyFiles i plików jar do podfolderu słoików folderu głównego. W czasie wykonywania usługa Data Factory oczekuje następującej struktury folderów w magazynie obiektów Blob platformy Azure:     

| Ścieżka                  | Opis                              | Wymagany | Typ   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.`(korzeń)            | Ścieżka główna zadania Platformy Spark w połączonej usłudze magazynu | Tak      | Folder |
| &lt;zdefiniowane przez użytkownika&gt; | Ścieżka wskazująca plik wejścia zadania Spark | Tak      | Plik   |
| ./słoiki                | Wszystkie pliki w tym folderze są przesyłane i umieszczane na ścieżce klasy java klastra | Nie       | Folder |
| ./pyFiles             | Wszystkie pliki w tym folderze są przesyłane i umieszczane na PYTHONPATH klastra | Nie       | Folder |
| ./pliki               | Wszystkie pliki w tym folderze są przesyłane i umieszczane w katalogu roboczym executora | Nie       | Folder |
| ./archiwum            | Wszystkie pliki w tym folderze są nieskompresowane | Nie       | Folder |
| ./dzienniki                | Folder zawierający dzienniki z klastra Platformy Spark. | Nie       | Folder |

Oto przykład magazynu zawierającego dwa pliki zadań platformy Spark w usłudze Azure Blob Storage, do których odwołuje się usługa połączona HDInsight.

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły, które wyjaśniają, jak przekształcać dane w inny sposób: 

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Aktywność gałęzi](transform-data-using-hadoop-hive.md)
* [Aktywność świń](transform-data-using-hadoop-pig.md)
* [Działanie mapreduce](transform-data-using-hadoop-map-reduce.md)
* [Aktywność w serwisie Hadoop Streaming](transform-data-using-hadoop-streaming.md)
* [Aktywność iskierki](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie wsadowe uczenia maszynowego](transform-data-using-machine-learning.md)
* [Działanie procedury składowanej](transform-data-using-stored-procedure.md)
