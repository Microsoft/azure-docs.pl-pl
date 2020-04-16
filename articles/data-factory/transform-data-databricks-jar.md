---
title: Przekształcanie danych za pomocą databricks jar
description: Dowiedz się, jak przetwarzać lub przekształcać dane, uruchamiając plik Databricks Jar.
services: data-factory
documentationcenter: ''
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
manager: shwang
ms.date: 03/15/2018
ms.openlocfilehash: 6b010000a674e351051c664dd5eeacd40e802439
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414607"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Przekształcanie danych przez uruchamianie działania jar w usłudze Azure Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Działanie usługi Azure Databricks Jar w [potoku fabryki danych](concepts-pipelines-activities.md) uruchamia jar platformy Spark w klastrze usługi Azure Databricks. W tym artykule opiera się na [działaniach](transform-data.md) transformacji danych, który przedstawia ogólny przegląd transformacji danych i obsługiwanych działań transformacji.Usługa Azure Databricks to zarządzana platforma do uruchamiania platformy Apache Spark.

Poniższy klip wideo zawiera jedenastominutowe wprowadzenie i demonstrację tej funkcji:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Databricks Jar definicja działania

Oto przykładowa definicja JSON działania Databricks Jar:

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

## <a name="databricks-jar-activity-properties"></a>Właściwości aktywności Databricks Jar

W poniższej tabeli opisano właściwości JSON używane w definicji JSON:

|Właściwość|Opis|Wymagany|
|:--|---|:-:|
|name|Nazwa działania w potoku.|Tak|
|description|Tekst opisujący działanie.|Nie|
|type|W przypadku działania Databricks Jar typem działania jest DatabricksSparkJar.|Tak|
|linkedServiceName|Nazwa usługi połączonej databricks, na której działa działanie Jar. Aby dowiedzieć się więcej o tej połączonej usłudze, zobacz Artykuł dotyczący  [powiązanych usług obliczeń.](compute-linked-services.md)|Tak|
|mainClassName (Nazwa główna)|Pełna nazwa klasy zawierającej metodę główną, która ma zostać wykonana. Ta klasa musi być zawarta w JAR dostarczone jako biblioteka.|Tak|
|parameters|Parametry, które zostaną przekazane do metody głównej.  Jest to tablica ciągów.|Nie|
|biblioteki|Lista bibliotek, które mają być zainstalowane w klastrze, który wykona zadanie. Może to być tablica <ciągu,> obiektu|Tak (co najmniej jeden zawierający metodę mainClassName)|

> [!NOTE]
> **Znany problem** — podczas korzystania z tego samego [klastra interaktywnego](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks) do uruchamiania równoczesnych działań Databricks Jar (bez ponownego uruchamiania klastra), istnieje znany problem w Databricks, gdzie w parametrach 1-cia działania będą używane przez następujące działania, jak również. W związku z tym wynikające z niepoprawne parametry są przekazywane do kolejnych zadań. Aby złagodzić ten problem, użyj zamiast tego [klastra zadań.](compute-linked-services.md#example---using-new-job-cluster-in-databricks) 

## <a name="supported-libraries-for-databricks-activities"></a>Obsługiwane biblioteki dla działań databricks

W powyższej definicji aktywności Databricks można określić te typy bibliotek: *jar*, *jajko*, *maven*, *pypi*, *cran*.

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

Aby uzyskać więcej informacji, zapoznaj się [z dokumentacją databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) dla typów bibliotek.

## <a name="how-to-upload-a-library-in-databricks"></a>Jak przesłać bibliotekę w Databricks

#### <a name="using-databricks-workspace-ui"></a>[Korzystanie z interfejsu użytkownika obszaru roboczego Databricks](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Aby uzyskać ścieżkę dbfs biblioteki dodanej przy użyciu interfejsu użytkownika, można użyć [Databricks CLI (instalacja)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Zazwyczaj biblioteki Jar są przechowywane w dbfs:/FileStore/jars podczas korzystania z interfejsu użytkownika. Można wyświetlić wszystkie za pośrednictwem interfejsu wiersza polecenia: *databricks fs ls dbfs:/FileStore/job-jars* 



#### <a name="copy-library-using-databricks-cli"></a>[Kopiowanie biblioteki przy użyciu interfejsu WIERSZA POLECENIA Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)
Użyj Databricks CLI [(kroki instalacji)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Przykład - kopiowanie JAR do dbfs: *dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar*
