---
title: Przekształcanie danych z użyciem jar
description: Dowiedz się, jak przetwarzać lub przekształcać dane, uruchamiając jar w obrębie Azure Data Factory potoku.
ms.service: data-factory
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
ms.date: 02/10/2021
ms.openlocfilehash: ccfe8fbf330e1c7f6f415b64a1f18d93a084a0ba
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100374018"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Przekształcanie danych przez uruchomienie działania jar w Azure Databricks

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Działanie Azure Databricks jar w [potoku Data Factory](concepts-pipelines-activities.md) uruchamia plik JAR platformy Spark w klastrze Azure Databricks. W tym artykule przedstawiono artykuł [działania przekształcania danych](transform-data.md) , który zawiera ogólne omówienie transformacji danych i obsługiwanych działań transformacji. Azure Databricks to zarządzana platforma do uruchamiania Apache Spark.

Poniższy klip wideo zawiera jedenastominutowe wprowadzenie i demonstrację tej funkcji:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Definicja działania jar

Oto przykładowa definicja JSON aktywności elementu danych jar:

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Właściwości działania jar

W poniższej tabeli opisano właściwości JSON używane w definicji JSON:

|Właściwość|Opis|Wymagane|
|:--|---|:-:|
|name|Nazwa działania w potoku.|Tak|
|description (opis)|Tekst opisujący działanie działania.|Nie|
|typ|W przypadku działania jar dla datakostki typ działania to DatabricksSparkJar.|Tak|
|linkedServiceName|Nazwa połączonej usługi datakostki, w której działa działanie jar. Aby dowiedzieć się więcej o tej połączonej usłudze, zobacz artykuł dotyczący [połączonych usług obliczeniowych](compute-linked-services.md) .|Tak|
|mainClassName|Pełna nazwa klasy zawierającej metodę Main, która ma zostać wykonana. Ta klasa musi być zawarta w formacie JAR udostępnionym jako biblioteka. Plik JAR może zawierać wiele klas. Każda klasa może zawierać metodę Main.|Tak|
|parameters|Parametry, które zostaną przesłane do metody Main. Ta właściwość jest tablicą ciągów.|Nie|
|biblioteki|Lista bibliotek do zainstalowania w klastrze, w którym będą wykonywane zadania. Może to być tablica <String, Object>|Tak (co najmniej jeden zawierający metodę mainClassName)|

> [!NOTE]
> **Znany problem** — w przypadku używania tego samego [interaktywnego klastra](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks) do uruchamiania współbieżnych elementów danych jar (bez ponownego uruchomienia klastra) występuje znany problem w kostkach danych, gdzie w parametrach pierwszego działania będą używane również następujące działania. Z tego powodu nieprawidłowe parametry są przesyłane do kolejnych zadań. Aby uniknąć tego problemu, użyj [klastra zadań](compute-linked-services.md#example---using-new-job-cluster-in-databricks) .

## <a name="supported-libraries-for-databricks-activities"></a>Biblioteki obsługiwane dla działań datakostek

W poprzedniej definicji działania elementów danych określono następujące typy bibliotek: `jar` , `egg` ,, `maven` `pypi` , `cran` .

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

Aby uzyskać więcej informacji, zobacz [dokumentację](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary) dotyczącą typów bibliotek.

## <a name="how-to-upload-a-library-in-databricks"></a>Jak przekazać bibliotekę w kostkach

### <a name="you-can-use-the-workspace-ui"></a>Można użyć interfejsu użytkownika obszaru roboczego:

1. [Korzystanie z interfejsu użytkownika obszaru roboczego datakosteks](/azure/databricks/libraries/#create-a-library)

2. Aby uzyskać ścieżkę dBfs biblioteki dodanej przy użyciu interfejsu użytkownika, można użyć [wiersza polecenia datakosteks](/azure/databricks/dev-tools/cli/#install-the-cli).

   Zazwyczaj biblioteki jar są przechowywane w obszarze dBfs:/FileStore/Jars przy użyciu interfejsu użytkownika. Wszystkie te listę można wyświetlić za pomocą interfejsu wiersza polecenia: *datakosteks FS LS dBfs:/FileStore/Job-Jars*

### <a name="or-you-can-use-the-databricks-cli"></a>Można też użyć interfejsu wiersza polecenia datakosteks:

1. Wykonaj [kopię biblioteki przy użyciu interfejsu wiersza polecenia datakosteks](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs)

2. Korzystanie z interfejsu wiersza polecenia datakosteks [(kroki instalacji)](/azure/databricks/dev-tools/cli/#install-the-cli)

   Na przykład, aby skopiować plik JAR do dBfs: `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`

## <a name="next-steps"></a>Następne kroki

W przypadku wprowadzenia i pokazania tej funkcji Obejrzyj [film wideo](https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player).
