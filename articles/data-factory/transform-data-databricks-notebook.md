---
title: Przekształcanie danych za pomocą notesu databricks
description: Dowiedz się, jak przetwarzać lub przekształcać dane, uruchamiając notes Databricks.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: nabhishek
ms.author: abnarain
manager: shwang
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 6d3c9f0df0d834ffe75d0b56e3c80a432c27ea38
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419021"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Przekształcanie danych przez uruchamianie notesu databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Aktywność notesu usługi Azure Databricks w [potoku usługi Data Factory](concepts-pipelines-activities.md) uruchamia notes Databricks w obszarze roboczym usługi Azure Databricks. W tym artykule opiera się na [działaniach](transform-data.md) transformacji danych, który przedstawia ogólny przegląd transformacji danych i obsługiwanych działań transformacji.Usługa Azure Databricks to zarządzana platforma do uruchamiania platformy Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Definicja aktywności notesu databricks

Oto przykładowa definicja JSON aktywności notesu Databricks:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            },
            "libraries": [
                {
                "jar": "dbfs:/docs/library.jar"
                }
            ]
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Właściwości aktywności notesu databricks

W poniższej tabeli opisano właściwości JSON używane w definicji JSON:

|Właściwość|Opis|Wymagany|
|---|---|---|
|name|Nazwa działania w potoku.|Tak|
|description|Tekst opisujący działanie.|Nie|
|type|W przypadku aktywności notesu databricks typem działania jest DatabricksNotebook.|Tak|
|linkedServiceName|Nazwa usługi połączonej Databricks, na której działa notes Databricks. Aby dowiedzieć się więcej o tej połączonej usłudze, zobacz Artykuł dotyczący  [powiązanych usług obliczeń.](compute-linked-services.md)|Tak|
|ścieżka notesu|Ścieżka bezwzględna notesu do uruchomienia w obszarze roboczym Databricks. Ta ścieżka musi zaczynać się od ukośnika.|Tak|
|baseParametry|Tablica par klucz-wartość. Parametry podstawowe mogą być używane dla każdego uruchomienia działania. Jeśli notes przyjmuje parametr, który nie jest określony, zostanie użyta wartość domyślna z notesu. Więcej informacji na temat parametrów można znaleźć w [notesach databricks](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Nie|
|biblioteki|Lista bibliotek, które mają być zainstalowane w klastrze, który wykona zadanie. Może to być \<tablica ciągów,>.|Nie|


## <a name="supported-libraries-for-databricks-activities"></a>Obsługiwane biblioteki dla działań Databricks

W powyższej definicji aktywności Databricks można określić następujące typy bibliotek: *jar,* *egg*, *whl*, *maven*, *pypi*, *cran*.

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
            "whl": "dbfs:/mnt/libraries/mlflow-0.0.1.dev0-py2-none-any.whl"
        },
        {
            "whl": "dbfs:/mnt/libraries/wheel-libraries.wheelhouse.zip"
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

Aby uzyskać więcej informacji, zobacz [Dokumentację Databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) dla typów bibliotek.

## <a name="passing-parameters-between-notebooks-and-data-factory"></a>Przekazywanie parametrów między notesami a fabryką danych

Parametry fabryki danych można przekazać do notesów przy użyciu *właściwości baseParameters* w aktywności databricks. 

W niektórych przypadkach może być konieczne przekazanie niektórych wartości z notebooka z powrotem do fabryki danych, które mogą być używane do sterowania przepływem (kontrole warunkowe) w fabryce danych lub być używane przez działania niższego rzędu (limit rozmiaru wynosi 2 MB). 

1. W notesie można wywołać [dbutils.notebook.exit("returnValue")](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-workflows.html#notebook-workflows-exit) i odpowiednie "returnValue" zostaną zwrócone do fabryki danych.

2. Dane wyjściowe można wykorzystać w fabryce `'@activity('databricks notebook activity name').output.runOutput'`danych przy użyciu wyrażenia, takiego jak . 

   > [!IMPORTANT]
   > W przypadku przekazywania obiektu JSON można pobrać wartości, dołączając nazwy właściwości. Przykład: `'@activity('databricks notebook activity name').output.runOutput.PropertyName'`

## <a name="how-to-upload-a-library-in-databricks"></a>Jak przesłać bibliotekę w Databricks

#### <a name="using-databricks-workspace-ui"></a>[Korzystanie z interfejsu użytkownika obszaru roboczego Databricks](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Aby uzyskać ścieżkę dbfs biblioteki dodanej przy użyciu interfejsu użytkownika, można użyć [interfejsu wiersza polecenia Databricks (instalacja).](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli) 

Zazwyczaj biblioteki Jar są przechowywane w dbfs:/FileStore/jars podczas korzystania z interfejsu użytkownika. Można wymienić wszystko za pośrednictwem interfejsu wiersza polecenia: *databricks fs ls dbfs:/FileStore/jars*.



#### <a name="copy-library-using-databricks-cli"></a>[Kopiowanie biblioteki przy użyciu interfejsu WIERSZA POLECENIA Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Przykład: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*
