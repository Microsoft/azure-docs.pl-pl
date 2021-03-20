---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 19927fd274cbb7337248fb2ae8cf9d882612d570
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92369248"
---
Usługa Azure Data Factory obsługuje następujące działania przekształcania, które można dodawać indywidualnie do potoków lub łączyć je z innymi działaniami.

| Działanie przekształcania danych | Środowisko obliczeniowe |
|:--- |:--- |
| [Hive](../articles/data-factory/v1/data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](../articles/data-factory/v1/data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](../articles/data-factory/v1/data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Połączenia strumieniowe usługi Hadoop](../articles/data-factory/v1/data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Spark](../articles/data-factory/v1/data-factory-spark.md) | HDInsight [Hadoop] |
| [Działania Azure Machine Learning Studio (klasyczne): wykonywanie wsadowe i aktualizowanie zasobu](../articles/data-factory/v1/data-factory-azure-ml-batch-execution-activity.md) |Maszyna wirtualna platformy Azure |
| [Procedura składowana](../articles/data-factory/v1/data-factory-stored-proc-activity.md) |Azure SQL, Azure Synapse Analytics lub SQL Server |
| [Data Lake Analytics U-SQL](../articles/data-factory/v1/data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](../articles/data-factory/v1/data-factory-use-custom-activities.md) |Usługa HDInsight [Hadoop] lub usługa Azure Batch |

> [!NOTE]
> Możesz użyć działania MapReduce, aby uruchomić programy platformy Spark w klastrze usługi HDInsight Spark. Zobacz [Wywoływanie programów platformy Spark z usługi Azure Data Factory](../articles/data-factory/v1/data-factory-spark.md).
> Możesz utworzyć niestandardowe działanie, aby uruchamiać skrypty w klastrze usługi HDInsight z zainstalowanym językiem R. Zobacz [Uruchamianie skryptów języka R przy użyciu usługi Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample).
> 
> 

