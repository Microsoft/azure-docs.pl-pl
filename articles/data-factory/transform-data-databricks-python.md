---
title: Przekształcanie danych za pomocą języka Databricks Python
description: Dowiedz się, jak przetwarzać lub przekształcać dane, uruchamiając program Databricks Python.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/15/2018
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: anandsub
ms.openlocfilehash: e102b14d8471a19564f66edc27cc328c2a789c98
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414594"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Przekształcanie danych przez uruchamianie działania języka Python w usłudze Azure Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


Działanie usługi Azure Databricks Python w [potoku fabryki danych](concepts-pipelines-activities.md) uruchamia plik Języka Python w klastrze usługi Azure Databricks. W tym artykule opiera się na [działaniach](transform-data.md) transformacji danych, który przedstawia ogólny przegląd transformacji danych i obsługiwanych działań transformacji.Usługa Azure Databricks to zarządzana platforma do uruchamiania platformy Apache Spark.

Poniższy klip wideo zawiera jedenastominutowe wprowadzenie i demonstrację tej funkcji:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Definicja aktywności języka Databricks Python

Oto przykładowa definicja JSON działania Języka Pytona Databricks:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksSparkPython",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "pythonFile": "dbfs:/docs/pi.py",
            "parameters": [
                "10"
            ],
            "libraries": [
                {
                    "pypi": {
                        "package": "tensorflow"
                    }
                }
            ]
        }
    }
}
```

## <a name="databricks-python-activity-properties"></a>Właściwości działania języka Databricks Python

W poniższej tabeli opisano właściwości JSON używane w definicji JSON:

|Właściwość|Opis|Wymagany|
|---|---|---|
|name|Nazwa działania w potoku.|Tak|
|description|Tekst opisujący działanie.|Nie|
|type|W przypadku działania języka Databricks Python typem działania jest DatabricksSparkPython.|Tak|
|linkedServiceName|Nazwa usługi połączonej Databricks, na której działa działanie Języka Python. Aby dowiedzieć się więcej o tej połączonej usłudze, zobacz Artykuł dotyczący  [powiązanych usług obliczeń.](compute-linked-services.md)|Tak|
|pythonFile|Identyfikator URI pliku języka Python do wykonania. Obsługiwane są tylko ścieżki DBFS.|Tak|
|parameters|Parametry wiersza polecenia, które zostaną przekazane do pliku Pythona. Jest to tablica ciągów.|Nie|
|biblioteki|Lista bibliotek, które mają być zainstalowane w klastrze, który wykona zadanie. Może to być tablica <ciągu,> obiektu|Nie|

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

Zazwyczaj biblioteki Jar są przechowywane w dbfs:/FileStore/jars podczas korzystania z interfejsu użytkownika. Można wymienić wszystko przez CLI: *databricks fs ls dbfs:/FileStore/jars* 



#### <a name="copy-library-using-databricks-cli"></a>[Kopiowanie biblioteki przy użyciu interfejsu WIERSZA POLECENIA Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Przykład: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*