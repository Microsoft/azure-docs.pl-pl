---
title: Korzystanie z Azure Cosmos DB interfejs API tabel i usługi Azure Table Storage przy użyciu języka Python
description: Przechowuj dane strukturalne w chmurze przy użyciu usługi Azure Table Storage lub interfejs API tabel Azure Cosmos DB przy użyciu języka Python.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: python
ms.topic: sample
ms.date: 07/23/2020
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.custom: devx-track-python
ms.openlocfilehash: 341615bf8fe231eafa606411948ad014399b8261
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93079550"
---
# <a name="get-started-with-azure-table-storage-and-the-azure-cosmos-db-table-api-using-python"></a>Rozpoczynanie pracy z usługą Azure Table Storage oraz interfejsem API tabel usługi Azure Cosmos DB przy użyciu języka Python
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

Magazyn tabel Azure i Azure Cosmos DB to usługi, które przechowują dane strukturalne NoSQL w chmurze, zapewniając Magazyn kluczy/atrybutów z projektem bez schematu. Ponieważ usługi Table Storage i Azure Cosmos DB nie mają schematu, można łatwo dostosować dane do zmieniających się potrzeb aplikacji. Dostęp do danych w usłudze Table Storage i interfejsu API tabel jest szybki i ekonomiczny dla wielu typów aplikacji. zwykle jest to tańsze niż tradycyjny kod SQL dla podobnych ilości danych.

Możesz użyć magazynu tabel lub Azure Cosmos DB do przechowywania elastycznych zestawów danych, takich jak dane użytkowników dla aplikacji sieci Web, książek adresowych, informacji o urządzeniu lub innych typów metadanych wymaganych przez usługę. W tabeli można przechowywać dowolną liczbę jednostek, a konto magazynu może zawierać dowolną liczbę tabel w granicach pojemności konta magazynu.

### <a name="about-this-sample"></a>Informacje o tym przykładzie

Ten przykład pokazuje, jak zastosować [zestaw SDK tabel usługi Azure Cosmos DB dla języka Python](https://pypi.python.org/pypi/azure-cosmosdb-table/) w kilku typowych scenariuszach związanych z usługą Azure Table Storage. Nazwa zestawu SDK wskazuje, że powinien być używany z usługą Azure Cosmos DB, ale działa zarówno z usługą Azure Cosmos DB, jak i z usługą Azure Table Storage. Każda z nich ma po prostu unikatowy punkt końcowy. Scenariusze są tu przedstawione za pomocą przykładów w języku Python, które ilustrują sposób wykonywania następujących czynności:

* Tworzenie i usuwanie tabel
* Wstawianie jednostek i wykonywanie zapytań
* Modyfikowanie jednostek

Podczas pracy ze scenariuszami w tym przykładzie może być przydatna [dokumentacja interfejsu API zestawu SDK usługi Azure Cosmos DB dla języka Python](/python/api/overview/azure/cosmosdb?preserve-view=true&view=azure-python).

## <a name="prerequisites"></a>Wymagania wstępne

Do pomyślnego ukończenia pracy z przykładem wymagane są następujące elementy:

* Środowisko [Python](https://www.python.org/downloads/) w wersji 2.7, 3.3, 3.4, 3.5 lub 3.6
* [Azure Cosmos DB zestawu SDK tabeli dla języka Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Ten zestaw SDK łączy się zarówno z usługą Azure Table Storage, jak i z interfejsem API tabel usługi Azure Cosmos DB.
* [Konto usługi Azure Storage](../storage/common/storage-account-create.md) lub [Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/)

## <a name="create-an-azure-service-account"></a>Tworzenie konta usługi Azure

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Utwórz konto usługi Azure Storage**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Tworzenie konta interfejsu API tabel usługi Azure Cosmos DB**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="install-the-azure-cosmos-db-table-sdk-for-python"></a>Instalowanie zestawu SDK tabel usługi Azure Cosmos DB dla języka Python

Po utworzeniu konta usługi Azure Storage następnym krokiem jest zainstalowanie [zestawu SDK tabel usługi Microsoft Azure Cosmos DB dla języka Python](https://pypi.python.org/pypi/azure-cosmosdb-table/). Aby uzyskać więcej informacji na temat instalowania zestawu SDK, zapoznaj się z plikiem [README.rst](https://github.com/Azure/azure-cosmosdb-python/blob/master/azure-cosmosdb-table/README.rst) w repozytorium zestawu SDK usługi Azure Cosmos DB dla języka Python w witrynie GitHub.

## <a name="import-the-tableservice-and-entity-classes"></a>Importowanie klas TableService i Entity

W języku Python do pracy z jednostkami w usłudze Azure Table Storage są używane klasy [TableService][py_TableService] i [Entity][py_Entity]. Dodaj następujący kod na początku pliku Python, aby zaimportować obie te klasy:

```python
from azure.cosmosdb.table.tableservice import TableService
from azure.cosmosdb.table.models import Entity
```

## <a name="connect-to-azure-table-service"></a>Łączenie się z usługą Azure Table Storage

Aby nawiązać połączenie z usługą Azure Table Storage, utwórz obiekt [TableService][py_TableService] i przekaż nazwę konta oraz klucz konta usługi Azure Storage. Zastąp wartości `myaccount` i `mykey` nazwą i kluczem konta.

```python
table_service = TableService(account_name='myaccount', account_key='mykey')
```

## <a name="connect-to-azure-cosmos-db"></a>Łączenie z usługą Azure Cosmos DB

Aby nawiązać połączenie z usługą Azure Cosmos DB, skopiuj podstawowe parametry połączenia z witryny Azure Portal, a następnie użyj ich, aby utworzyć obiekt [TableService][py_TableService]:

```python
table_service = TableService(connection_string='DefaultEndpointsProtocol=https;AccountName=myaccount;AccountKey=mykey;TableEndpoint=myendpoint;')
```

## <a name="create-a-table"></a>Tworzenie tabeli

Wywołaj metodę[create_table][py_create_table], aby utworzyć tabelę.

```python
table_service.create_table('tasktable')
```

## <a name="add-an-entity-to-a-table"></a>Dodawanie jednostki do tabeli

Aby dodać jednostkę, musisz najpierw utworzyć obiekt, który reprezentuje jednostkę, a następnie przekazać ten obiekt do [metody TableService.insert_entity][py_TableService]. Obiekt jednostki może być słownikiem lub obiektem typu [Entity][py_Entity]. Definiuje on nazwy i wartości właściwości jednostki. Każda jednostka musi zawierać wymagane właściwości [PartitionKey i RowKey](#partitionkey-and-rowkey), oprócz innych właściwości zdefiniowanych dla jednostki.

W tym przykładzie tworzony jest obiekt słownika reprezentujący jednostkę, który jest następnie przekazywany do metody [insert_entity][py_insert_entity] w celu dodania jednostki do tabeli:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the trash', 'priority': 200}
table_service.insert_entity('tasktable', task)
```

W tym przykładzie tworzony jest obiekt [Entity][py_Entity], który następnie jest przekazywany do metody [insert_entity][py_insert_entity] w celu dodania jednostki do tabeli:

```python
task = Entity()
task.PartitionKey = 'tasksSeattle'
task.RowKey = '002'
task.description = 'Wash the car'
task.priority = 100
table_service.insert_entity('tasktable', task)
```

### <a name="partitionkey-and-rowkey"></a>Właściwości PartitionKey i RowKey

Należy pamiętać, że dla każdej jednostki konieczne jest określenie właściwości **PartitionKey** i **RowKey** . Są to unikatowe identyfikatory jednostek, które wspólnie tworzą klucz podstawowy jednostki. Zapytania dotyczące tych wartości są wykonywane znacznie szybciej niż zapytania dotyczące innych właściwości jednostki, ponieważ tylko te właściwości są indeksowane.

W usłudze Table Storage właściwość **PartitionKey** jest używana do inteligentnego rozłożenia jednostki tabeli pomiędzy węzłami magazynu. Obiekty z taką samą wartością **PartitionKey** są przechowywane w tym samym węźle. Właściwość **RowKey** to unikatowy identyfikator jednostki w ramach partycji, do której należy.

## <a name="update-an-entity"></a>Aktualizowanie jednostki

Aby zaktualizować wszystkie wartości właściwości jednostki, należy wywołać metodę [update_entity][py_update_entity]. W tym przykładzie pokazano, jak zastąpić istniejącą jednostkę zaktualizowaną wersją:

```python
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the garbage', 'priority': 250}
table_service.update_entity('tasktable', task)
```

Jeśli aktualizowana jednostka jeszcze nie istnieje, to aktualizacja zakończy się niepowodzeniem. Jeśli chcesz zapisać jednostkę bez względu na to, czy istnieje czy nie, użyj metody [insert_or_replace_entity][py_insert_or_replace_entity]. W poniższym przykładzie pierwsze wywołanie spowoduje zastąpienie istniejącej jednostki. Drugie wywołanie spowoduje wstawienie nowej jednostki, ponieważ w tabeli nie istnieje żadna jednostka z podanymi wartościami PartitionKey i RowKey.

```python
# Replace the entity created earlier
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '001',
        'description': 'Take out the garbage again', 'priority': 250}
table_service.insert_or_replace_entity('tasktable', task)

# Insert a new entity
task = {'PartitionKey': 'tasksSeattle', 'RowKey': '003',
        'description': 'Buy detergent', 'priority': 300}
table_service.insert_or_replace_entity('tasktable', task)
```

> [!TIP]
> Metoda [Update_entity][py_update_entity] zastępuje wszystkie właściwości i wartości istniejącej jednostki. Umożliwia także usunięcie właściwości z istniejącej jednostki. Możesz użyć metody [merge_entity][py_merge_entity], aby zaktualizować istniejącą jednostkę przy użyciu nowych lub zmodyfikowanych wartości właściwości, bez całkowitego zastępowania jednostki.

## <a name="modify-multiple-entities"></a>Modyfikowanie wielu jednostek

Aby zapewnić niepodzielne przetwarzanie żądania przez usługę Table Storage, warto przesłać jednocześnie wiele operacji w partii. Najpierw należy użyć klasy [TableBatch][py_TableBatch], aby dodać wiele operacji do jednej partii. Następnie należy wywołać metodę [TableService][py_TableService].[ commit_batch][py_commit_batch], aby przesłać niepodzielną partię operacji. Wszystkie jednostki, które mają być zmodyfikowane w ramach partii, muszą należeć do tej samej partycji.

W tym przykładzie dodano dwie jednostki do partii:

```python
from azure.cosmosdb.table.tablebatch import TableBatch
batch = TableBatch()
task004 = {'PartitionKey': 'tasksSeattle', 'RowKey': '004',
           'description': 'Go grocery shopping', 'priority': 400}
task005 = {'PartitionKey': 'tasksSeattle', 'RowKey': '005',
           'description': 'Clean the bathroom', 'priority': 100}
batch.insert_entity(task004)
batch.insert_entity(task005)
table_service.commit_batch('tasktable', batch)
```

Partii można również użyć, korzystając ze składni menedżera kontekstu:

```python
task006 = {'PartitionKey': 'tasksSeattle', 'RowKey': '006',
           'description': 'Go grocery shopping', 'priority': 400}
task007 = {'PartitionKey': 'tasksSeattle', 'RowKey': '007',
           'description': 'Clean the bathroom', 'priority': 100}

with table_service.batch('tasktable') as batch:
    batch.insert_entity(task006)
    batch.insert_entity(task007)
```

## <a name="query-for-an-entity"></a>Wykonywanie zapytania względem jednostki

Aby wykonać zapytanie dotyczące jednostki w tabeli, przekaż wartości PartitionKey i RowKey do metody [TableService][py_TableService].[get_entity][py_get_entity].

```python
task = table_service.get_entity('tasktable', 'tasksSeattle', '001')
print(task.description)
print(task.priority)
```

## <a name="query-a-set-of-entities"></a>Wykonywanie zapytania względem zestawu jednostek

Możesz wykonać zapytanie dotyczące zestawu jednostek, podając ciąg filtru przy użyciu parametru **filter** . W tym przykładzie znalezione zostaną wszystkie zadania w lokalizacji Seattle przy użyciu filtru wartości PartitionKey:

```python
tasks = table_service.query_entities(
    'tasktable', filter="PartitionKey eq 'tasksSeattle'")
for task in tasks:
    print(task.description)
    print(task.priority)
```

## <a name="query-a-subset-of-entity-properties"></a>Tworzenie zapytania do podzbioru właściwości jednostki

Możesz również ograniczyć zestaw właściwości zwracanych dla każdej jednostki w zapytaniu. Ta technika, zwana *projekcją* , zmniejsza użycie przepustowości i może poprawiać wydajność zapytań, zwłaszcza w przypadku dużych jednostek lub zestawów wyników. Użyj parametru **select** i wprowadź nazwy właściwości, które chcesz zwrócić do klienta.

Zapytanie w poniższym kodzie zwraca wyłącznie opisy jednostek w tabeli.

> [!NOTE]
> Poniższy fragment kodu działa tylko w usłudze Azure Storage. Emulator magazynu nie obsługuje tego programu.

```python
tasks = table_service.query_entities(
    'tasktable', filter="PartitionKey eq 'tasksSeattle'", select='description')
for task in tasks:
    print(task.description)
```

## <a name="delete-an-entity"></a>Usuwanie jednostki

Aby usunąć jednostkę, przekaż jej wartości **PartitionKey** i **RowKey** do metody [delete_entity][py_delete_entity].

```python
table_service.delete_entity('tasktable', 'tasksSeattle', '001')
```

## <a name="delete-a-table"></a>Usuwanie tabeli

Jeśli nie potrzebujesz już tabeli ani żadnych jednostek, które się w niej znajdują, wywołaj metodę [delete_table][py_delete_table], aby trwale usunąć tabelę z usługi Azure Storage.

```python
table_service.delete_table('tasktable')
```

## <a name="next-steps"></a>Następne kroki

* [FAQ — Develop with the Table API (Opracowywanie zawartości przy użyciu interfejsu API tabel — często zadawane pytania)](./faq.md)
* [Azure Cosmos DB SDK for Python API reference (Dokumentacja zestawu SDK usługi Azure Cosmos DB dla języka Python)](/python/api/overview/azure/cosmosdb?preserve-view=true&view=azure-python)
* [Centrum deweloperów języka Python](https://azure.microsoft.com/develop/python/)
* [Eksplorator usługi Microsoft Azure Storage](../vs-azure-tools-storage-manage-with-storage-explorer.md): bezpłatna, wieloplatformowa aplikacja umożliwiająca wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.
* [Working with Python in Visual Studio (Windows) (Korzystanie z języka Python w programie Visual Studio w systemie Windows)](/visualstudio/python/overview-of-python-tools-for-visual-studio)



[py_commit_batch]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?preserve-view=true&view=azure-python
[py_create_table]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?preserve-view=true&view=azure-python
[py_delete_entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?preserve-view=true&view=azure-python
[py_get_entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?preserve-view=true&view=azure-python
[py_insert_entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?preserve-view=true&view=azure-python
[py_insert_or_replace_entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?preserve-view=true&view=azure-python
[py_Entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.models.entity?preserve-view=true&view=azure-python
[py_merge_entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?preserve-view=true&view=azure-python
[py_update_entity]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?preserve-view=true&view=azure-python
[py_delete_table]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?preserve-view=true&view=azure-python
[py_TableService]: /python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?preserve-view=true&view=azure-python
[py_TableBatch]: https://docs.microsoft.com/python/api/azure-cosmosdb-table/azure.cosmosdb.table.tableservice.tableservice?view=azure-python&preserve-view=true