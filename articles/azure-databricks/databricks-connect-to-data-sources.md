---
title: 'Łączenie się z różnymi źródłami danych z Azure Databricks '
description: Dowiedz się, jak nawiązać połączenie z usługą Azure SQL Database, Azure Data Lake Store, BLOB Storage, Cosmos DB, Event Hubs i Azure SQL Data Warehouse z Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: 2b6d234c3845c220fd63c24db5bdbdae0f7c63fd
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85255079"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Łączenie ze źródłami danych z Azure Databricks

Ten artykuł zawiera linki do wszystkich różnych źródeł danych na platformie Azure, które mogą być połączone z Azure Databricks. Postępuj zgodnie z przykładami w tych linkach, aby wyodrębnić dane ze źródeł danych platformy Azure (na przykład Azure Blob Storage, Azure Event Hubs itp.) do klastra Azure Databricks i uruchamiać na nich zadania analityczne. 

## <a name="prerequisites"></a>Wymagania wstępne

* Musisz mieć obszar roboczy Azure Databricks i klaster Spark. Postępuj zgodnie z instrukcjami w punkcie wprowadzenie [do Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Źródła danych dla Azure Databricks

Poniższa lista zawiera źródła danych na platformie Azure, których można używać z Azure Databricks. Aby uzyskać pełną listę źródeł danych, które mogą być używane z Azure Databricks, zobacz [źródła danych dla Azure Databricks](/azure/databricks/data/data-sources/index).

- [Azure SQL Database](/azure/databricks/data/data-sources/sql-databases)

    Ten link udostępnia interfejs API Dataframe służący do łączenia się z SQL Database przy użyciu JDBC i kontrolowania równoległości odczytów za pośrednictwem interfejsu JDBC. W tym temacie przedstawiono szczegółowe przykłady użycia interfejsu API Scala z skróconymi przykładami języka Python i Spark SQL na końcu.
- [Azure Data Lake Storage](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)

    Ten link zawiera przykłady użycia jednostki usługi Azure Active Directory do uwierzytelniania przy użyciu Azure Data Lake Storage. Zawiera również instrukcje dotyczące sposobu uzyskiwania dostępu do danych w Azure Data Lake Storage z Azure Databricks.

- [Azure Blob Storage](/azure/databricks/data/data-sources/azure/azure-storage)

    Ten link zawiera przykłady dotyczące bezpośredniego dostępu do usługi Azure Blob Storage z Azure Databricks przy użyciu klucza dostępu lub SAS dla danego kontenera. Link zawiera również informacje dotyczące uzyskiwania dostępu do usługi Azure Blob Storage z Azure Databricks przy użyciu interfejsu API RDD.

- [Azure Cosmos DB](/azure/databricks/data/data-sources/azure/cosmosdb-connector)

    Ten link zawiera instrukcje dotyczące korzystania z [Azure Cosmos DB łącznika Spark](https://github.com/Azure/azure-cosmosdb-spark) z Azure Databricks w celu uzyskania dostępu do danych w Azure Cosmos DB.

- [Azure Event Hubs](/azure/event-hubs/event-hubs-spark-connector)

    Ten link zawiera instrukcje dotyczące korzystania z [łącznika usługi azure Event Hubs Spark](https://github.com/Azure/azure-event-hubs-spark) z Azure Databricks w celu uzyskania dostępu do danych w usłudze Azure Event Hubs.

- [Azure SQL Data Warehouse](/azure/synapse-analytics/sql-data-warehouse/)

    Ten link zawiera instrukcje dotyczące korzystania z łącznika Azure SQL Data Warehouse w celu nawiązania połączenia z Azure Databricks.
    

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o źródłach, z których można importować dane do Azure Databricks, zobacz [źródła danych dla Azure Databricks](/azure/databricks/data/data-sources/index).


