---
title: Przekształcanie danych za pomocą języka Python
description: Dowiedz się, jak przetwarzać lub przekształcać dane, uruchamiając działanie języka Python dla elementów datakostks w potoku Azure Data Factory.
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/15/2018
author: dcstwh
ms.author: weetok
ms.custom: devx-track-python
ms.openlocfilehash: 49dfe11ceb01471e3b5afadd30259dcd63e7b82a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100373950"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Przekształcanie danych przez uruchomienie działania języka Python w Azure Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Działanie języka Python Azure Databricks w [potoku Data Factory](concepts-pipelines-activities.md) uruchamia plik w języku Python w klastrze Azure Databricks. W tym artykule przedstawiono artykuł [działania przekształcania danych](transform-data.md) , który zawiera ogólne omówienie transformacji danych i obsługiwanych działań transformacji. Azure Databricks to zarządzana platforma do uruchamiania Apache Spark.

Poniższy klip wideo zawiera jedenastominutowe wprowadzenie i demonstrację tej funkcji:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Definicja działania w języku Python

Poniżej znajduje się przykładowa definicja JSON działania w języku Python:

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

## <a name="databricks-python-activity-properties"></a>Właściwości działania Python dla elementów datakostek

W poniższej tabeli opisano właściwości JSON używane w definicji JSON:

|Właściwość|Opis|Wymagane|
|---|---|---|
|name|Nazwa działania w potoku.|Tak|
|description (opis)|Tekst opisujący działanie działania.|Nie|
|typ|Dla działania języka Python dla elementów datakostks typem działania jest DatabricksSparkPython.|Tak|
|linkedServiceName|Nazwa połączonej usługi datakostki, w której działa działanie języka Python. Aby dowiedzieć się więcej o tej połączonej usłudze, zobacz artykuł dotyczący [połączonych usług obliczeniowych](compute-linked-services.md) .|Tak|
|pythonFile|Identyfikator URI pliku języka Python, który ma zostać wykonany. Obsługiwane są tylko ścieżki DBFS.|Tak|
|parameters|Parametry wiersza polecenia, które zostaną przesłane do pliku języka Python. To jest tablica ciągów.|Nie|
|biblioteki|Lista bibliotek do zainstalowania w klastrze, w którym będą wykonywane zadania. Może to być tablica <String, Object>|Nie|

## <a name="supported-libraries-for-databricks-activities"></a>Biblioteki obsługiwane dla działań datakostek

W powyższej definicji działań datacegły należy określić następujące typy biblioteki: *jar*, *jaja*, *Maven*, *PyPi*, *Cran*.

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

Aby uzyskać więcej szczegółowych informacji, zapoznaj się z [dokumentacją](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary) dla typów bibliotek.

## <a name="how-to-upload-a-library-in-databricks"></a>Jak przekazać bibliotekę w kostkach

### <a name="you-can-use-the-workspace-ui"></a>Można użyć interfejsu użytkownika obszaru roboczego:

1. [Korzystanie z interfejsu użytkownika obszaru roboczego datakosteks](/azure/databricks/libraries/#create-a-library)

2. Aby uzyskać ścieżkę dBfs biblioteki dodanej przy użyciu interfejsu użytkownika, można użyć [wiersza polecenia datakosteks](/azure/databricks/dev-tools/cli/#install-the-cli).

   Zazwyczaj biblioteki jar są przechowywane w obszarze dBfs:/FileStore/Jars przy użyciu interfejsu użytkownika. Wszystkie te listę można wyświetlić za pomocą interfejsu wiersza polecenia: *datakosteks FS LS dBfs:/FileStore/Job-Jars*

### <a name="or-you-can-use-the-databricks-cli"></a>Można też użyć interfejsu wiersza polecenia datakosteks:

1. Wykonaj [kopię biblioteki przy użyciu interfejsu wiersza polecenia datakosteks](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs)

2. Korzystanie z interfejsu wiersza polecenia datakosteks [(kroki instalacji)](/azure/databricks/dev-tools/cli/#install-the-cli)

   Na przykład, aby skopiować plik JAR do dBfs: `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`
