---
title: Korzystanie z usługi Azure Table Storage lub interfejs API tabel Azure Cosmos DB w Node.js
description: Przechowuj dane strukturalne w chmurze przy użyciu usługi Azure Table Storage lub interfejs API tabel Azure Cosmos DB w Node.js.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: nodejs
ms.topic: sample
ms.date: 07/23/2020
author: sakash279
ms.author: akshanka
ms.custom: devx-track-js
ms.openlocfilehash: 43ac175e2c1caa39bfe88a7c1a5f42318db343fb
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92477287"
---
# <a name="how-to-use-azure-table-storage-or-the-azure-cosmos-db-table-api-from-nodejs"></a>Jak korzystać z usługi Azure Table Storage lub interfejsu API tabel usługi Azure Cosmos DB przy użyciu platformy Node.js

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

W tym artykule przedstawiono sposób tworzenia tabel, przechowywania danych i wykonywania operacji CRUD na danych. Wybierz Azure Cosmos DB Table service platformy Azure lub interfejs API tabel. Przykłady są zapisywane w Node.js.

## <a name="create-an-azure-service-account"></a>Tworzenie konta usługi Azure

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

**Utwórz konto usługi Azure Storage**

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

**Tworzenie konta interfejsu API tabel usługi Azure Cosmos DB**

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="configure-your-application-to-access-azure-storage-or-the-azure-cosmos-db-table-api"></a>Konfigurowanie aplikacji w celu uzyskiwania dostępu do usługi Azure Storage lub interfejsu API tabel usługi Azure Cosmos DB

Aby użyć usługi Azure Storage lub Azure Cosmos DB, należy skorzystać z zestawu Azure Storage SDK dla platformy Node.js, który zawiera zestaw wygodnych bibliotek służących do komunikacji z usługami Storage REST.

### <a name="use-node-package-manager-npm-to-install-the-package"></a>Instalowanie menedżera NPM (Node Package Manager)

1. Użyj interfejsu wiersza polecenia, takiego jak **PowerShell** (Windows), **Terminal** (Mac) lub **Bash** (Unix), i przejdź do folderu, w którym utworzono aplikację.
2. Wpisz ciąg **npm install azure-storage** w oknie polecenia. Dane wyjściowe polecenia są podobne do poniższego przykładu.

   ```bash
    azure-storage@0.5.0 node_modules\azure-storage
    +-- extend@1.2.1
    +-- xmlbuilder@0.4.3
    +-- mime@1.2.11
    +-- node-uuid@1.4.3
    +-- validator@3.22.2
    +-- underscore@1.4.4
    +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
    +-- xml2js@0.2.7 (sax@0.5.2)
    +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
   ```

3. Możesz ręcznie uruchomić polecenie **ls**, aby sprawdzić, czy utworzono folder **node_modules**. Wewnątrz tego folderu znajduje się pakiet **azure-storage** zawierający biblioteki wymagane do uzyskiwania dostępu do magazynu.

### <a name="import-the-package"></a>Importowanie pakietu

Dodaj następujący kod na początku pliku **server.js** w aplikacji:

```javascript
var azure = require('azure-storage');
```

## <a name="add-your-connection-string"></a>Dodaj parametry połączenia

Możesz połączyć się z kontem usługi Azure Storage lub kontem Azure Cosmos DB interfejs API tabel. Pobierz parametry połączenia na podstawie typu konta, którego używasz.

### <a name="add-an-azure-storage-connection"></a>Dodawanie połączenia z usługą Azure Storage

Moduł platformy Azure odczytuje zmienne środowiskowe AZURE_STORAGE_ACCOUNT i AZURE_STORAGE_ACCESS_KEY lub AZURE_STORAGE_CONNECTION_STRING, aby uzyskać informacje wymagane do nawiązania połączenia z kontem usługi Azure Storage. Jeśli te zmienne środowiskowe nie są ustawione, podczas wywoływania należy określić informacje o koncie `TableService` . Na przykład poniższy kod tworzy `TableService` obiekt:

```javascript
var tableSvc = azure.createTableService('myaccount', 'myaccesskey');
```

### <a name="add-an-azure-cosmos-db-connection"></a>Dodawanie połączenia z usługą Azure Cosmos DB

Aby dodać połączenie Azure Cosmos DB, Utwórz `TableService` obiekt i określ nazwę konta, klucz podstawowy i punkt końcowy. Można skopiować te wartości z **Settings**  >  **parametrów połączenia** ustawienia w Azure Portal dla konta Cosmos DB. Przykład:

```javascript
var tableSvc = azure.createTableService('myaccount', 'myprimarykey', 'myendpoint');
```

## <a name="create-a-table"></a>Tworzenie tabeli

Poniższy kod tworzy `TableService` obiekt i używa go do utworzenia nowej tabeli.

```javascript
var tableSvc = azure.createTableService();
```

Wywołanie `createTableIfNotExists` tworzenia nowej tabeli o określonej nazwie, jeśli jeszcze nie istnieje. W poniższym przykładzie jest tworzona nowa tabela o nazwie „mytable”, jeśli jeszcze nie istnieje:

```javascript
tableSvc.createTableIfNotExists('mytable', function(error, result, response){
  if(!error){
    // Table exists or created
  }
});
```

Element `result.created` ma wartość `true` w przypadku tworzenia nowej tabeli lub wartość `false`, jeśli tabela już istnieje. Element `response` zawiera informacje dotyczące żądania.

### <a name="apply-filters"></a>Stosowanie filtrów

Można zastosować opcjonalne filtrowanie do operacji wykonywanych przy użyciu `TableService` . Operacje filtrowania mogą obejmować rejestrowanie, automatyczne ponawianie prób itd. Filtry są obiektami implementującymi metodę z podpisem:

```javascript
function handle (requestOptions, next)
```

Po zakończeniu przetwarzania wstępnego opcji żądań metoda musi wywołać element **next**, przekazując wywołanie zwrotne z następującą sygnaturą:

```javascript
function (returnObject, finalCallback, next)
```

W przypadku tego wywołania zwrotnego i po przetworzeniu `returnObject` (odpowiedzi z żądania do serwera) wywołanie zwrotne musi zostać wywołane, `next` Jeśli istnieje, aby kontynuować przetwarzanie innych filtrów, lub po prostu wywołać w `finalCallback` przeciwnym razie zakończenie wywołania usługi.

Dwa filtry, które implementują logikę ponowień, są dołączone do zestawu Azure SDK dla Node.js `ExponentialRetryPolicyFilter` i `LinearRetryPolicyFilter` . Poniższy element tworzy `TableService` obiekt, który używa `ExponentialRetryPolicyFilter` :

```javascript
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var tableSvc = azure.createTableService().withFilter(retryOperations);
```

## <a name="add-an-entity-to-a-table"></a>Dodawanie jednostki do tabeli

Aby dodać jednostkę, najpierw utwórz obiekt, który definiuje właściwości jednostki. Wszystkie jednostki muszą zawierać elementy **PartitionKey** i **RowKey**, które są unikatowymi identyfikatorami jednostki.

* **PartitionKey** — określa partycję, w której jest przechowywana jednostka.
* **RowKey** — unikatowo identyfikuje jednostkę w partycji.

Klucze **PartitionKey** i **RowKey** muszą być wartościami ciągów. Aby uzyskać więcej informacji, zobacz [Understanding the Table Service Data Model (Omówienie modelu danych usługi Table Service)](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model).

Poniżej znajduje się przykład definiowania jednostki. **DueDate** jest definiowana jako typ `Edm.DateTime` . Określenie typu jest opcjonalne — w przypadku rezygnacji typy zostaną wywnioskowane.

```javascript
var task = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'},
  description: {'_':'take out the trash'},
  dueDate: {'_':new Date(2015, 6, 20), '$':'Edm.DateTime'}
};
```

> [!NOTE]
> Istnieje również `Timestamp` pole dla każdego rekordu, który jest ustawiany przez platformę Azure podczas wstawiania lub aktualizowania jednostki.

Można również użyć `entityGenerator` do tworzenia jednostek. Poniższy przykład tworzy tę samą jednostkę zadania przy użyciu `entityGenerator` .

```javascript
var entGen = azure.TableUtilities.entityGenerator;
var task = {
  PartitionKey: entGen.String('hometasks'),
  RowKey: entGen.String('1'),
  description: entGen.String('take out the trash'),
  dueDate: entGen.DateTime(new Date(Date.UTC(2015, 6, 20))),
};
```

Aby dodać jednostkę do tabeli, przekaż obiekt Entity do `insertEntity` metody.

```javascript
tableSvc.insertEntity('mytable',task, function (error, result, response) {
  if(!error){
    // Entity inserted
  }
});
```

Jeśli operacja zakończy się pomyślnie, element `result` będzie zawierać tag [ETag](https://en.wikipedia.org/wiki/HTTP_ETag) wstawionego rekordu, a element `response` będzie zawierać informacje na temat operacji.

Przykładowa odpowiedź:

```javascript
{ '.metadata': { etag: 'W/"datetime\'2015-02-25T01%3A22%3A22.5Z\'"' } }
```

> [!NOTE]
> Domyślnie program nie `insertEntity` zwraca wstawionej jednostki jako części `response` informacji. Jeśli planujesz wykonywanie innych operacji w obrębie tej jednostki lub chcesz buforować informacje, może być przydatne ich zwracanie jako części elementu `result`. Można to zrobić, włączając `echoContent` następujące czynności:
>
> `tableSvc.insertEntity('mytable', task, {echoContent: true}, function (error, result, response) {...}`

## <a name="update-an-entity"></a>Aktualizowanie jednostki

Istnieje kilka metod aktualizowania istniejącej jednostki:

* `replaceEntity` -Aktualizuje istniejącą jednostkę, zastępując ją.
* `mergeEntity` -Aktualizuje istniejącą jednostkę, łącząc nowe wartości właściwości z istniejącą jednostką.
* `insertOrReplaceEntity` -Aktualizuje istniejącą jednostkę, zastępując ją. Jeśli żadna jednostka nie istnieje, zostanie wstawiona nowa jednostka.
* `insertOrMergeEntity` -Aktualizuje istniejącą jednostkę, łącząc nowe wartości właściwości z istniejącymi. Jeśli żadna jednostka nie istnieje, zostanie wstawiona nowa jednostka.

Poniższy przykład ilustruje aktualizowanie jednostki przy użyciu `replaceEntity` :

```javascript
tableSvc.replaceEntity('mytable', updatedTask, function(error, result, response){
  if(!error) {
    // Entity updated
  }
});
```

> [!NOTE]
> Domyślnie podczas aktualizowania jednostki nie odbywa się sprawdzenie, czy aktualizowane dane zostały wcześniej zmodyfikowane przez inny proces. Aby obsługiwać równoczesne aktualizacje:
>
> 1. Pobierz tag ETag aktualizowanego obiektu. Ten tag jest zwracany jako część elementu `response` dla dowolnej operacji powiązanej z jednostką i można go pobrać za pośrednictwem elementu `response['.metadata'].etag`.
> 2. Podczas wykonywania operacji aktualizowania jednostki dodaj informacje tagu ETag wcześniej pobrane do nowej jednostki. Przykład:
>
>       entity2['.metadata'].etag = currentEtag;
> 3. Wykonaj operację aktualizacji. Jeśli jednostka została zmodyfikowana od czasu pobrania wartości tagu ETag, takiej jak inne wystąpienie aplikacji, element `error` jest zwracany wraz z informacją o tym, że warunek aktualizacji określony w żądaniu nie został spełniony.
>
>

W przypadku `replaceEntity` i `mergeEntity` , jeśli aktualizowana jednostka nie istnieje, operacja aktualizacji zakończy się niepowodzeniem. w związku z tym, jeśli chcesz przechowywać jednostkę niezależnie od tego, czy już istnieje, użyj `insertOrReplaceEntity` lub `insertOrMergeEntity` .

Element `result` pomyślnie zakończonych operacji aktualizowania zawiera tag **Etag** zaktualizowanej jednostki.

## <a name="work-with-groups-of-entities"></a>Praca z grupami jednostek

Czasami warto przesłać jednocześnie wiele operacji w partii, aby zapewnić niepodzielne przetwarzanie przez serwer. Aby to osiągnąć, użyj `TableBatch` klasy do utworzenia partii, a następnie użyj `executeBatch` metody, `TableService` Aby wykonać operacje wsadowe.

 W poniższym przykładzie przedstawiono przesyłanie dwóch jednostek w partii:

```javascript
var task1 = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'},
  description: {'_':'Take out the trash'},
  dueDate: {'_':new Date(2015, 6, 20)}
};
var task2 = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '2'},
  description: {'_':'Wash the dishes'},
  dueDate: {'_':new Date(2015, 6, 20)}
};

var batch = new azure.TableBatch();

batch.insertEntity(task1, {echoContent: true});
batch.insertEntity(task2, {echoContent: true});

tableSvc.executeBatch('mytable', batch, function (error, result, response) {
  if(!error) {
    // Batch completed
  }
});
```

W przypadku operacji wsadowych zakończonych pomyślnie element `result` zawiera informacje dotyczące każdej operacji w partii.

### <a name="work-with-batched-operations"></a>Praca z operacjami wsadowymi

Operacje dodane do partii można sprawdzić, wyświetlając właściwość `operations`. Można także użyć następujących metod podczas pracy z operacjami:

* **clear** — usuwa wszystkie operacje z partii.
* **getOperations** — pobiera operację z partii.
* **hasOperations** — zwraca wartość true, jeśli partia zawiera operacje.
* **removeOperations** — usuwa operację.
* **size** — zwraca liczbę operacji w partii.

## <a name="retrieve-an-entity-by-key"></a>Pobieranie jednostek według klucza

Aby zwrócić określoną jednostkę na podstawie elementów **PartitionKey** i **RowKey**, użyj metody **retrieveEntity**.

```javascript
tableSvc.retrieveEntity('mytable', 'hometasks', '1', function(error, result, response){
  if(!error){
    // result contains the entity
  }
});
```

Po ukończeniu tej operacji element `result` zawiera jednostkę.

## <a name="query-a-set-of-entities"></a>Wykonywanie zapytania względem zestawu jednostek

Aby wykonać zapytanie dotyczące tabeli, należy użyć obiektu **TableQuery** w celu skompilowania wyrażenia zapytania przy użyciu następujących klauzul:

* **select** — pola do zwrócenia z zapytania.
* **where** — klauzula where.

  * **and** — warunek where `and`.
  * **or** — warunek where `or`.
* **top** — liczba elementów do pobrania.

W poniższym przykładzie jest tworzone zapytanie, które zwraca pięć pierwszych elementów z kluczem PartitionKey „hometasks”.

```javascript
var query = new azure.TableQuery()
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

Ponieważ klauzula **select** nie jest używana, są zwracane wszystkie pola. Aby wykonać zapytanie dotyczące tabeli, należy użyć elementu **queryEntities**. W poniższym przykładzie użyto tego zapytania w celu zwrócenia jednostek z tabeli „mytable”.

```javascript
tableSvc.queryEntities('mytable',query, null, function(error, result, response) {
  if(!error) {
    // query was successful
  }
});
```

W przypadku powodzenia element `result.entries` będzie zawierać tablicę jednostek pasujących do zapytania. Jeśli zapytanie nie mogło zwrócić wszystkich jednostek, element `result.continuationToken` ma wartość inną niż *null* i może być używany jako trzeci parametr elementu **queryEntities** do pobierania większej liczby wyników. W zapytaniu początkowym można użyć wartości *null* w przypadku trzeciego parametru.

### <a name="query-a-subset-of-entity-properties"></a>Tworzenie zapytania do podzbioru właściwości jednostki

Za pomocą zapytania wykonywanego względem tabeli można pobrać tylko kilka pól z jednostki.
Redukuje to przepustowość i może poprawiać wydajność zapytań, zwłaszcza w przypadku dużych jednostek. Użyj klauzuli **select** i przekaż nazwy pól do zwrócenia. Na przykład poniższe zapytanie zwraca tylko wartości pól **description** i **dueDate**.

```javascript
var query = new azure.TableQuery()
  .select(['description', 'dueDate'])
  .top(5)
  .where('PartitionKey eq ?', 'hometasks');
```

## <a name="delete-an-entity"></a>Usuwanie jednostki

Jednostkę można usunąć za pomocą kluczy partycji i wierszy. W tym przykładzie obiekt **task1** zawiera wartości **RowKey** i **PartitionKey** jednostki do usunięcia. Następnie obiekt jest przekazywany do metody **deleteEntity**.

```javascript
var task = {
  PartitionKey: {'_':'hometasks'},
  RowKey: {'_': '1'}
};

tableSvc.deleteEntity('mytable', task, function(error, response){
  if(!error) {
    // Entity deleted
  }
});
```

> [!NOTE]
> Rozważ użycie tagów ETag podczas usuwania elementów, aby upewnić się, że element nie został zmodyfikowany przez inny proces. Aby uzyskać informacje na temat używania tagów Etag, zobacz [Aktualizowanie jednostki](#update-an-entity).
>
>

## <a name="delete-a-table"></a>Usuwanie tabeli

Poniższy kod usuwa tabelę z konta magazynu.

```javascript
tableSvc.deleteTable('mytable', function(error, response){
    if(!error){
        // Table deleted
    }
});
```

Jeśli nie masz pewności, czy tabela istnieje, użyj metody **deleteTableIfExists**.

## <a name="use-continuation-tokens"></a>Korzystanie z tokenów kontynuacji

W przypadku wykonywania zapytań dotyczących tabel z dużą liczbą wyników należy poszukać tokenów kontynuacji. Mogą istnieć duże ilości danych dostępnych dla zapytania, o których możesz nie wiedzieć, jeśli nie skompilujesz rozpoznawania obecności tokenu kontynuacji.

Obiekt **results** zwracany w przypadku wykonywania zapytań dotyczących jednostek ustawia właściwość `continuationToken`, gdy taki token istnieje. Możesz go następnie użyć podczas wykonywania zapytania, aby kontynuować poruszanie się w obrębie jednostek tabeli i partycji.

Podczas wykonywania zapytania możesz określić parametr `continuationToken` między wystąpieniem obiektu zapytania i funkcją wywołania zwrotnego:

```javascript
var nextContinuationToken = null;
dc.table.queryEntities(tableName,
    query,
    nextContinuationToken,
    function (error, results) {
        if (error) throw error;

        // iterate through results.entries with results

        if (results.continuationToken) {
            nextContinuationToken = results.continuationToken;
        }

    });
```

Jeśli sprawdzasz obiekt `continuationToken`, znajdziesz właściwości, takie jak `nextPartitionKey`, `nextRowKey` i `targetLocation`, które mogą służyć do iterowania w obrębie wszystkich wyników.

Można również użyć `top` programu razem z programem, `continuationToken` Aby ustawić rozmiar strony.

## <a name="work-with-shared-access-signatures"></a>Praca z sygnaturami dostępu współdzielonego

Sygnatury dostępu współdzielonego (SAS) to bezpieczny sposób zapewnienia szczegółowego dostępu do tabel bez podawania kluczy ani nazwy konta usługi Storage. Sygnatury dostępu współdzielonego są często używane do udzielania ograniczonych praw dostępu do danych, takich jak zezwalanie aplikacji mobilnej na wykonywanie zapytań dotyczących rekordów.

Aplikacja zaufana, taka jak usługa oparta na chmurze, generuje sygnaturę dostępu współdzielonego przy użyciu elementu **generateSharedAccessSignature** obiektu **TableService** i udostępnia ją niezaufanej lub częściowo zaufanej aplikacji, takiej jak aplikacja mobilna. Sygnatura dostępu współdzielonego jest generowana przy użyciu zasad opisujących daty rozpoczęcia i zakończenia okresu, w którym ta sygnatura obowiązuje, a także poziom dostępu przyznany właścicielowi sygnatury dostępu współdzielonego.

W poniższym przykładzie są generowane nowe zasady dostępu współdzielonego, które umożliwią właścicielowi sygnatury dostępu współdzielonego wykonanie zapytania („r”) dotyczącego tabeli. Ich ważność wygaśnie po upływie 100 minut od utworzenia.

```javascript
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
    Start: startDate,
    Expiry: expiryDate
  },
};

var tableSAS = tableSvc.generateSharedAccessSignature('mytable', sharedAccessPolicy);
var host = tableSvc.host;
```

Należy również pamiętać o podaniu informacji o hoście, ponieważ są one wymagane, gdy właściciel sygnatury dostępu współdzielonego próbuje uzyskać dostęp do tabeli.

Następnie aplikacja kliencka używa sygnatury dostępu współdzielonego z elementem **TableServiceWithSAS** w celu wykonywania operacji względem tabeli. W poniższym przykładzie następuje połączenie z tabelą i wykonanie zapytania. Zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatury dostępu współdzielonego (SAS)](../storage/common/storage-sas-overview.md) w formacie tableSAS.

```javascript
// Note in the following command, host is in the format: `https://<your_storage_account_name>.table.core.windows.net` and the tableSAS is in the format: `sv=2018-03-28&si=saspolicy&tn=mytable&sig=9aCzs76n0E7y5BpEi2GvsSv433BZa22leDOZXX%2BXXIU%3D`;

var sharedTableService = azure.createTableServiceWithSas(host, tableSAS);
var query = azure.TableQuery()
  .where('PartitionKey eq ?', 'hometasks');

sharedTableService.queryEntities(query, null, function(error, result, response) {
  if(!error) {
    // result contains the entities
  }
});
```

Ponieważ sygnatura dostępu współdzielonego została wygenerowana z dostępem tylko do zapytania, próba wstawienia, zaktualizowania lub usunięcia jednostek spowoduje zwrócenie błędu.

### <a name="access-control-lists"></a>Listy kontroli dostępu

Do ustawienia zasad dostępu powiązanych z sygnaturą dostępu współdzielonego można również użyć listy kontroli dostępu (ACL, Access Control List). Jest to przydatne, jeśli chcesz umożliwić wielu klientom dostęp do tabeli, ale uwzględnia różne zasady dostępu dla poszczególnych klientów.

Lista ACL jest implementowana przy użyciu tablicy zasad dostępu z identyfikatorem skojarzonym z poszczególnymi zasadami. W poniższym przykładzie zdefiniowano dwie zasady, odpowiednio dla użytkowników „user1” i „user2”:

```javascript
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.QUERY,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.TableUtilities.SharedAccessPermissions.ADD,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

W poniższym przykładzie bieżąca lista ACL jest pobierana do tabeli **hometasks**, a następnie następuje dodanie nowych zasad za pomocą elementu **setTableAcl**. W przypadku takiego podejścia:

```javascript
var extend = require('extend');
tableSvc.getTableAcl('hometasks', function(error, result, response) {
if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    tableSvc.setTableAcl('hometasks', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

Po ustawieniu listy ACL można następnie utworzyć sygnaturę dostępu współdzielonego na podstawie identyfikatora zasad. W poniższym przykładzie jest tworzona nowa sygnatura dostępu współdzielonego dla użytkownika „user2”:

```javascript
tableSAS = tableSvc.generateSharedAccessSignature('hometasks', { Id: 'user2' });
```

## <a name="next-steps"></a>Następne kroki

Więcej informacji zawierają poniższe zasoby.

* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) jest bezpłatną aplikacją autonomiczną oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.
* Repozytorium zestawu [Azure Storage SDK dla platformy Node.js](https://github.com/Azure/azure-storage-node) w witrynie GitHub.
* [Platforma Azure dla deweloperów środowiska Node.js](/azure/developer/javascript/)
* [Tworzenie aplikacji internetowej Node.js na platformie Azure](../app-service/quickstart-nodejs.md)
* [Tworzenie i wdrażanie aplikacji Node.js do usługi w chmurze Azure](../cloud-services/cloud-services-nodejs-develop-deploy-app.md) (za pomocą programu Windows PowerShell)