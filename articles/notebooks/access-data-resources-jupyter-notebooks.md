---
title: Dostęp do danych w notesach Jupyter Azure Notebooks — wersja zapoznawcza
description: Dowiedz się, jak uzyskać dostęp do plików, interfejsów API REST, baz danych i różnych zasobów usługi Azure Storage z notesu Jupyter.
ms.topic: how-to
ms.date: 12/04/2018
ms.custom: devx-track-python
ms.openlocfilehash: a833ff914c1ee53f024147371977ac1caa3800dc
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842876"
---
# <a name="access-cloud-data-in-a-notebook"></a>Dostęp do danych z chmury w notesie

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Wykonanie interesującej pracy w notesie Jupyter wymaga danych. Dane, faktycznie, to Lifeblood notesów.

Można na pewno [zaimportować pliki danych do projektu](work-with-project-data-files.md), nawet używając poleceń takich jak `curl` z notesu, aby bezpośrednio pobrać plik. Jest to jednak konieczne, aby pracować z znacznie bardziej rozbudowanymi danymi, które są dostępne w źródłach niezwiązanych z plikami, takimi jak interfejsy API REST, relacyjne bazy danych i magazyn w chmurze, takie jak tabele platformy Azure.

W tym artykule krótko opisano te różne opcje. Ze względu na to, że dostęp do danych jest najlepiej widoczny w akcji, można znaleźć kod możliwy do uruchomienia w [Azure Notebooks próbkach — dostęp do danych](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb).

## <a name="rest-apis"></a>Interfejsy API REST

Ogólnie mówiąc, olbrzymia ilość danych dostępnych w Internecie jest dostępna nie za pośrednictwem plików, ale za pośrednictwem interfejsów API REST. Na szczęście, ponieważ komórka notesu może zawierać dowolny kod, który lubisz, można użyć kodu do wysyłania żądań i odbierania danych JSON. Następnie można przekonwertować ten kod JSON na dowolny format, który ma być używany, taki jak Pandas Dataframe.

Aby uzyskać dostęp do danych przy użyciu interfejsu API REST, należy użyć tego samego kodu w komórkach kodu notesu, które są używane w dowolnej innej aplikacji. Ogólna struktura przy użyciu biblioteki Requests jest następująca:

```python
import pandas
import requests

# New York City taxi data for 2014
data_url = 'https://data.cityofnewyork.us/resource/gkne-dk5s.json'

# General data request; include other API keys and credentials as needed in the data argument
response = requests.get(data_url, data={"limit": "20"})

if response.status_code == 200:
    dataframe_rest2 = pandas.DataFrame.from_records(response.json())
    print(dataframe_rest2)
```

## <a name="azure-sql-database-and-sql-managed-instance"></a>Azure SQL Database i wystąpienie zarządzane SQL

Można uzyskać dostęp do baz danych w SQL Database lub wystąpieniu zarządzanym SQL z pomocą bibliotek moduł pyodbc lub pymssql.

[Używanie języka Python do wykonywania zapytań w bazie danych Azure SQL Database](../azure-sql/database/connect-query-python.md) zawiera instrukcje dotyczące tworzenia bazy danych w SQL Database zawierającej dane AdventureWorks i pokazujące, jak wykonywać zapytania dotyczące tych danych. Ten sam kod jest przedstawiony w notesie przykładowym dla tego artykułu.

## <a name="azure-storage"></a>Azure Storage

Usługa Azure Storage oferuje kilka różnych typów magazynu nierelacyjnego, w zależności od typu danych i sposobu dostępu do niego:

- Table Storage: zapewnia niski koszt magazynowania magazynu o dużej pojemności dla danych tabelarycznych, na przykład zebranych dzienników czujników, dzienników diagnostycznych itd.
- BLOB Storage: zapewnia magazyn podobne do plików dla dowolnego typu danych.

W przykładowym notesie przedstawiono pracę z tabelami i obiektami BLOB, w tym używanie sygnatury dostępu współdzielonego w celu zezwalania na dostęp tylko do odczytu do obiektów BLOB.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB zapewnia w pełni indeksowany Magazyn NoSQL dla dokumentów JSON. Poniższe artykuły zawierają różne sposoby pracy z Cosmos DBami z poziomu języka Python:

- [Tworzenie aplikacji interfejsu API SQL przy użyciu języka Python](../cosmos-db/create-sql-api-python.md)
- [Tworzenie aplikacji do kolby z interfejsem API Azure Cosmos DB dla MongoDB](../cosmos-db/create-mongodb-flask.md)
- [Tworzenie bazy danych grafu przy użyciu języka Python i interfejsu API Gremlin](../cosmos-db/create-graph-python.md)
- [tworzenie aplikacji Cassandra przy użyciu języka Python i usługi Azure Cosmos DB](../cosmos-db/create-cassandra-python.md)
- [tworzenie aplikacji interfejsu API tabel przy użyciu języka Python i usługi Azure Cosmos DB](../cosmos-db/table-storage-how-to-use-python.md)

Podczas pracy z Cosmos DB można użyć biblioteki [Azure-cosmosdb-Table](https://pypi.org/project/azure-cosmosdb-table/) .

## <a name="other-azure-databases"></a>Inne bazy danych platformy Azure

Platforma Azure udostępnia wiele innych typów baz danych, których można użyć. W poniższych artykułach przedstawiono wskazówki dotyczące uzyskiwania dostępu do tych baz danych z języka Python:

- [Usługa Azure Database for PostgreSQL: nawiązywanie połączeń z danymi i wykonywanie na nich zapytań za pomocą języka Python](../postgresql/connect-python.md)
- [Szybki start: korzystanie z usługi Azure Redis Cache w połączeniu z językiem Python](../azure-cache-for-redis/cache-python-get-started.md)
- [Usługa Azure Database for MySQL: nawiązywanie połączeń z danymi i wykonywanie na nich zapytań za pomocą języka Python](../mysql/connect-python.md)
- [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)
  - [Kreator kopiowania dla Azure Data Factory](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>Następne kroki

- [Instrukcje: współdziałanie z plikami danych projektu](work-with-project-data-files.md)