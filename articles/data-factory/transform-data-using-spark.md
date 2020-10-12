---
title: Przekształcanie danych przy użyciu działania Spark
description: Dowiedz się, jak przekształcać dane, uruchamiając programy Spark z potoku usługi Azure Data Factory przy użyciu działania platformy Spark.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: bc8fd73b18e197c42e4750612320c1b15a6db020
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83849216"
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Przekształcanie danych przy użyciu działania Spark w Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-spark.md)
> * [Bieżąca wersja](transform-data-using-spark.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Działanie platformy Spark w [potoku](concepts-pipelines-activities.md) Data Factory wykonuje program platformy Spark we [własnym klastrze usługi](compute-linked-services.md#azure-hdinsight-linked-service) HDInsight lub [na żądanie](compute-linked-services.md#azure-hdinsight-on-demand-linked-service)  . W tym artykule przedstawiono artykuł [działania przekształcania danych](transform-data.md) , który zawiera ogólne omówienie transformacji danych i obsługiwanych działań transformacji. Jeśli używasz połączonej usługi Spark na żądanie, Data Factory automatycznie tworzy klaster Spark, aby przetwarzać dane w czasie, a następnie usunąć klaster po zakończeniu przetwarzania. 


## <a name="spark-activity-properties"></a>Właściwości działania platformy Spark
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

| Właściwość              | Opis                              | Wymagane |
| --------------------- | ---------------------------------------- | -------- |
| name                  | Nazwa działania w potoku.    | Tak      |
| description           | Tekst opisujący działanie działania.  | Nie       |
| typ                  | Dla działania platformy Spark typem działania jest HDInsightSpark. | Tak      |
| linkedServiceName     | Nazwa połączonej usługi HDInsight Spark, na której jest uruchamiany program Spark. Aby dowiedzieć się więcej o tej połączonej usłudze, zobacz artykuł dotyczący [połączonych usług obliczeniowych](compute-linked-services.md) . | Tak      |
| SparkJobLinkedService | Połączona usługa Azure Storage, która przechowuje plik zadania platformy Spark, zależności i dzienniki. W tym miejscu są obsługiwane tylko połączone usługi **[BLOB Storage platformy Azure](https://docs.microsoft.com/azure/data-factory/connector-azure-blob-storage)** i **[ADLS Gen2](https://docs.microsoft.com/azure/data-factory/connector-azure-data-lake-storage)** . Jeśli nie określisz wartości tej właściwości, zostanie użyty magazyn skojarzony z klastrem usługi HDInsight. Wartością tej właściwości może być tylko połączona usługa Azure Storage. | Nie       |
| Właściwość RootPath              | Kontener i folder obiektów blob platformy Azure, który zawiera plik Spark. W nazwie pliku rozróżniana jest wielkość liter. Szczegółowe informacje na temat struktury tego folderu można znaleźć w sekcji struktury folderów (w następnej sekcji). | Tak      |
| entryFilePath         | Ścieżka względna do folderu głównego kodu/pakietu platformy Spark. Plik wejściowy musi być plikiem w języku Python lub plikiem jar. | Tak      |
| Nazwą             | Główna Klasa środowiska Java/Spark aplikacji      | Nie       |
| argumentu             | Lista argumentów wiersza polecenia do programu Spark. | Nie       |
| proxyUser             | Konto użytkownika służące do personifikacji w celu wykonania programu Spark | Nie       |
| sparkConfig           | Określ wartości właściwości konfiguracji platformy Spark wymienione w temacie: [Konfiguracja platformy Spark — właściwości aplikacji](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Nie       |
| GetDebugInfo —          | Określa, kiedy pliki dziennika platformy Spark są kopiowane do magazynu platformy Azure używanego przez klaster usługi HDInsight (lub) określonego przez sparkJobLinkedService. Dozwolone wartości: brak, zawsze lub niepowodzenie. Wartość domyślna: None. | Nie       |

## <a name="folder-structure"></a>Struktura folderów
Zadania platformy Spark są bardziej rozszerzalne niż zadania dla trzody chlewnej/Hive. W przypadku zadań platformy Spark można podać wiele zależności, takich jak pakiety jar (umieszczone w ścieżce klas Java), pliki Python (umieszczone na PYTHONPATH) i inne pliki.

Utwórz następującą strukturę folderów w magazynie obiektów blob platformy Azure, do której odwołuje się połączona Usługa HDInsight. Następnie Przekaż pliki zależne do odpowiednich podfolderów w folderze głównym reprezentowane przez **entryFilePath**. Na przykład przekazanie plików Python do podfolderu pyFiles i plików jar do podfolderu Jars folderu głównego. W czasie wykonywania Usługa Data Factory oczekuje następującej struktury folderów w usłudze Azure Blob Storage:     

| Ścieżka                  | Opis                              | Wymagane | Typ   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.` pierwiastek            | Ścieżka katalogu głównego zadania platformy Spark w połączonej usłudze Storage | Tak      | Folder |
| &lt;zdefiniowane przez użytkownika &gt; | Ścieżka wskazująca plik wpisu zadania Spark | Tak      | Plik   |
| ./jars                | Wszystkie pliki w tym folderze są przekazywane i umieszczane na ścieżce klas Java klastra | Nie       | Folder |
| ./pyFiles             | Wszystkie pliki w tym folderze są przekazywane i umieszczane w PYTHONPATH klastra | Nie       | Folder |
| ./files               | Wszystkie pliki w tym folderze są przekazywane i umieszczane w katalogu roboczym wykonującym | Nie       | Folder |
| ./archives            | Wszystkie pliki w tym folderze są nieskompresowane | Nie       | Folder |
| ./logs                | Folder zawierający dzienniki z klastra Spark. | Nie       | Folder |

Oto przykład dla magazynu zawierającego dwa pliki zadań platformy Spark na platformie Azure Blob Storage do których odwołuje się połączona Usługa HDInsight.

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
Zapoznaj się z następującymi artykułami, które wyjaśniają sposób przekształcania danych w inny sposób: 

* [Działanie U-SQL](transform-data-using-data-lake-analytics.md)
* [Działanie Hive](transform-data-using-hadoop-hive.md)
* [Aktywność trzody chlewnej](transform-data-using-hadoop-pig.md)
* [Działanie MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działanie przesyłania strumieniowego Hadoop](transform-data-using-hadoop-streaming.md)
* [Działanie platformy Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie wykonywania wsadowego Machine Learning](transform-data-using-machine-learning.md)
* [Działanie procedury składowanej](transform-data-using-stored-procedure.md)
