---
title: Łączenie aplikacji go z interfejsem API Azure Cosmos DB dla MongoDB
description: Ten przewodnik Szybki Start przedstawia sposób nawiązywania połączenia istniejącej aplikacji go z interfejsem API Azure Cosmos DB MongoDB.
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: go
ms.topic: quickstart
ms.date: 04/24/2020
ms.openlocfilehash: 92edfa148268db5a5458b2af4000bc9ffd9ecc83
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101659957"
---
# <a name="quickstart-connect-a-go-application-to-azure-cosmos-dbs-api-for-mongodb"></a>Szybki Start: łączenie aplikacji go z interfejsem API Azure Cosmos DB dla MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](./mongodb-introduction.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Azure Cosmos DB to wielomodelowa usługa bazy danych, która pozwala szybko tworzyć i wysyłać zapytania dotyczące dokumentów, tabel, kluczy i wartościowych baz danych przy użyciu dystrybucji globalnej i możliwości skalowania w poziomie. W tym przewodniku szybki start utworzysz konto Azure Cosmos DB i zarządzasz nim przy użyciu Azure Cloud Shell, Sklonuj istniejącą przykładową aplikację z usługi GitHub i skonfigurujesz ją do pracy z Azure Cosmos DBami. 

Przykładowa aplikacja jest narzędziem do zarządzania opartym na wierszu polecenia, które zostało `todo` zapisaniem. Interfejs API Azure Cosmos DB dla MongoDB jest [zgodny z protokołem telekomunikacyjnym MongoDB](./mongodb-introduction.md#wire-protocol-compatibility), dzięki czemu można połączyć się z nim przy użyciu dowolnego sterownika klienta MongoDB. Ta aplikacja korzysta ze [sterownika go dla MongoDB](https://github.com/mongodb/mongo-go-driver) w sposób niewidoczny dla aplikacji, w której dane są przechowywane w bazie danych Azure Cosmos DB.

## <a name="prerequisites"></a>Wymagania wstępne
- Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free). Lub [Wypróbuj bezpłatnie Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure. Można również użyć [emulatora Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) z parametrami połączenia `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` .
- Zainstalowano na komputerze i działająca wiedza na temat [języka go.](https://golang.org/)
- Usługi [git](https://git-scm.com/downloads).
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Uruchom następujące polecenia w celu sklonowania przykładowego repozytorium.

1. Otwórz wiersz polecenia, Utwórz nowy folder o nazwie `git-samples` , a następnie Zamknij wiersz polecenia.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Otwórz okno terminala usługi Git, np. git bash, i użyj polecenia `cd`, aby przejść do nowego folderu instalacji aplikacji przykładowej.

    ```bash
    cd "C:\git-samples"
    ```

3. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze. 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-go-mongodb-quickstart
    ```

## <a name="review-the-code"></a>Przeglądanie kodu

Ta czynność jest opcjonalna. Jeśli chcesz się dowiedzieć, jak działa aplikacja, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz przejść do [uruchamiania aplikacji](#run-the-application). Układ aplikacji jest następujący:

```bash
.
├── go.mod
├── go.sum
└── todo.go
```

Wszystkie poniższe fragmenty kodu pochodzą z pliku `todo.go`.

### <a name="connecting-the-go-app-to-azure-cosmos-db"></a>Łączenie aplikacji języka Go z usługą Cosmos Azure DB

[`clientOptions`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions) hermetyzuje parametry połączenia dla Azure Cosmos DB, które są przesyłane przy użyciu zmiennej środowiskowej (szczegóły w kolejnej sekcji). Połączenie jest inicjowane przy użyciu, [`mongo.NewClient`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#NewClient) do którego `clientOptions` wystąpienie zostanie przesłane. [ `Ping` Funkcja](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Client.Ping) jest wywoływana w celu potwierdzenia pomyślnej łączności (dotyczy to strategii niepowodzenia)

```go
    ctx, cancel := context.WithTimeout(context.Background(), time.Second*10)
    defer cancel()

    clientOptions := options.Client().ApplyURI(mongoDBConnectionString).SetDirect(true)
    
    c, err := mongo.NewClient(clientOptions)
    err = c.Connect(ctx)
    if err != nil {
        log.Fatalf("unable to initialize connection %v", err)
    }

    err = c.Ping(ctx, nil)
    if err != nil {
        log.Fatalf("unable to connect %v", err)
    }
```

> [!NOTE] 
> Użycie [`SetDirect(true)`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions.SetDirect) konfiguracji jest ważne, bez której zostanie wyświetlony następujący błąd łączności: `unable to connect connection(cdb-ms-prod-<azure-region>-cm1.documents.azure.com:10255[-4]) connection is closed`
>

### <a name="create-a-todo-item"></a>Utwórz `todo` element

Aby utworzyć `todo` , uzyskujemy dojście do [`mongo.Collection`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection) i Wywołaj [`InsertOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.InsertOne) funkcję. 

```go
func create(desc string) {
    c := connect()
    ctx := context.Background()
    defer c.Disconnect(ctx)

    todoCollection := c.Database(database).Collection(collection)
    r, err := todoCollection.InsertOne(ctx, Todo{Description: desc, Status: statusPending})
    if err != nil {
        log.Fatalf("failed to add todo %v", err)
    }
```

Przekazujemy `Todo` strukturę, która zawiera opis i stan (początkowo ustawiony na `pending` ).

```go
type Todo struct {
    ID          primitive.ObjectID `bson:"_id,omitempty"`
    Description string             `bson:"description"`
    Status      string             `bson:"status"`
}
```
### <a name="list-todo-items"></a>`todo`Elementy listy

Można wyświetlić listę zadań do wykonania na podstawie kryteriów. [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D)Jest tworzony w celu hermetyzacji kryteriów filtrowania

```go
func list(status string) {
    .....
    var filter interface{}
    switch status {
    case listAllCriteria:
        filter = bson.D{}
    case statusCompleted:
        filter = bson.D{{statusAttribute, statusCompleted}}
    case statusPending:
        filter = bson.D{{statusAttribute, statusPending}}
    default:
        log.Fatal("invalid criteria for listing todo(s)")
    }
```

[`Find`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.Find) służy do wyszukiwania dokumentów opartych na filtrze, a wynik jest konwertowany na wycinek `Todo`

```go
    todoCollection := c.Database(database).Collection(collection)
    rs, err := todoCollection.Find(ctx, filter)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
    var todos []Todo
    err = rs.All(ctx, &todos)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
```

Na koniec informacje są renderowane w formacie tabelarycznym

```go
    todoTable := [][]string{}

    for _, todo := range todos {
        s, _ := todo.ID.MarshalJSON()
        todoTable = append(todoTable, []string{string(s), todo.Description, todo.Status})
    }

    table := tablewriter.NewWriter(os.Stdout)
    table.SetHeader([]string{"ID", "Description", "Status"})

    for _, v := range todoTable {
        table.Append(v)
    }
    table.Render()
```

### <a name="update-a-todo-item"></a>Aktualizowanie `todo` elementu

`todo`Można ją zaktualizować na podstawie jej `_id` . [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D)Filtr jest tworzony na podstawie, `_id` a drugi jest tworzony dla zaktualizowanych informacji, co jest nowym stanem ( `completed` lub `pending` ) w tym przypadku. Na koniec [`UpdateOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.UpdateOne) Funkcja jest wywoływana z filtrem i zaktualizowanym dokumentem

```go
func update(todoid, newStatus string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
    filter := bson.D{{"_id", oid}}
    update := bson.D{{"$set", bson.D{{statusAttribute, newStatus}}}}
    _, err = todoCollection.UpdateOne(ctx, filter, update)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
```

### <a name="delete-a-todo"></a>Usuń element `todo`

Element `todo` jest usuwany na podstawie jego `_id` i jest hermetyzowany w postaci [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) wystąpienia. [`DeleteOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.DeleteOne) jest wywoływana w celu usunięcia dokumentu.

```go
func delete(todoid string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("invalid todo ID %v", err)
    }
    filter := bson.D{{"_id", oid}}
    _, err = todoCollection.DeleteOne(ctx, filter)
    if err != nil {
        log.Fatalf("failed to delete todo %v", err)
    }
}
```

## <a name="build-the-application"></a>Kompilowanie aplikacji

Przejdź do katalogu, w którym Sklonowano aplikację, i skompiluj ją (przy użyciu `go build` ).

```bash
cd monogdb-go-quickstart
go build -o todo
```

Aby upewnić się, że aplikacja została skompilowana prawidłowo.

```bash
./todo --help
```

## <a name="setup-azure-cosmos-db"></a>Azure Cosmos DB Instalatora

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten temat będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli musisz zainstalować lub uaktualnić program, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]. 

Jeśli używasz zainstalowanego interfejsu wiersza polecenia platformy Azure, zaloguj się do subskrypcji platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index#az-login) i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie. Ten krok możesz pominąć, jeśli używasz powłoki Azure Cloud Shell.

```azurecli
az login 
``` 
   
### <a name="add-the-azure-cosmos-db-module"></a>Dodawanie modułu Azure Cosmos DB

Jeśli używasz zainstalowanego interfejsu wiersza polecenia platformy Azure, sprawdź, czy składnik `cosmosdb` jest już zainstalowany, uruchamiając polecenie `az`. Jeśli `cosmosdb` znajduje się na liście podstawowych poleceń, przejdź do następnego polecenia. Ten krok możesz pominąć, jeśli używasz powłoki Azure Cloud Shell.

Jeśli `cosmosdb` nie znajduje się na liście podstawowych poleceń, zainstaluj ponownie [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów](../azure-resource-manager/management/overview.md) za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure, takich jak aplikacje internetowe, bazy danych i konta magazynu, oraz zarządzania nimi. 

Poniższy przykład obejmuje tworzenie grupy zasobów w regionie Europa Zachodnia. Wybierz unikatową nazwę grupy zasobów.

Jeśli używasz Azure Cloud Shell, wybierz opcję **Wypróbuj**, postępuj zgodnie z monitami Onscreen, aby zalogować się, a następnie skopiuj polecenie do wiersza polecenia.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

### <a name="create-an-azure-cosmos-db-account"></a>Tworzenie konta usługi Azure Cosmos DB

Utwórz konto usługi Cosmos za pomocą polecenia [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create).

W poniższym poleceniu w miejsce symbolu zastępczego `<cosmosdb-name>` wstaw swoją unikatową nazwę konta usługi Cosmos. Ta nazwa będzie służyć jako część Twojego punktu końcowego usługi Cosmos DB (`https://<cosmosdb-name>.documents.azure.com/`), dlatego musi być unikatowa w obrębie wszystkich kont usługi Cosmos na platformie Azure. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

Parametr `--kind MongoDB` umożliwia tworzenie połączeń klienckich MongoDB.

Po utworzeniu konta usługi Azure Cosmos DB w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone informacje podobne do następujących. 

> [!NOTE]
> W tym przykładzie format JSON jest używany jako format wyjściowy interfejsu wiersza polecenia platformy Azure. Jest to ustawienie domyślne. Aby użyć innego formatu wyjściowego, zobacz [Formaty danych wyjściowych dla poleceń interfejsu wiersza polecenia platformy Azure](/cli/azure/format-output-azure-cli). 

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

### <a name="retrieve-the-database-key"></a>Pobieranie klucza bazy danych

Aby połączyć się z bazą danych usługi Cosmos, niezbędny jest klucz bazy danych. Użyj polecenia [AZ cosmosdb Keys list](/cli/azure/cosmosdb/keys#az-cosmosdb-keys-list) , aby pobrać klucz podstawowy.

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

W interfejsie wiersza polecenia platformy Azure zostaną wyświetlone informacje podobne do następującego przykładu. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

## <a name="configure-the-application"></a>Konfigurowanie aplikacji 

<a name="devconfig"></a>
### <a name="export-the-connection-string-mongodb-database-and-collection-names-as-environment-variables"></a>Wyeksportuj parametry połączenia, bazę danych MongoDB i nazwy kolekcji jako zmienne środowiskowe. 

```bash
export MONGODB_CONNECTION_STRING="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> Ta `ssl=true` opcja jest ważna ze względu na wymagania Cosmos DB. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące parametrów połączenia](connect-mongodb-account.md#connection-string-requirements).
>

W polu `MONGODB_CONNECTION_STRING` zmienna środowiskowa Zastąp symbole zastępcze dla `<COSMOSDB_ACCOUNT_NAME>` i `<COSMOSDB_PASSWORD>`

1. `<COSMOSDB_ACCOUNT_NAME>`: Nazwa utworzonego konta Azure Cosmos DB
2. `<COSMOSDB_PASSWORD>`: Klucz bazy danych wyodrębniony w poprzednim kroku

```bash
export MONGODB_DATABASE=todo-db
export MONGODB_COLLECTION=todos
```

Możesz wybrać preferowane wartości dla `MONGODB_DATABASE` i `MONGODB_COLLECTION` pozostawić je.

## <a name="run-the-application"></a>Uruchamianie aplikacji

Aby utworzyć `todo`

```bash
./todo --create "Create an Azure Cosmos DB database account"
```

Jeśli to się powiedzie, powinny pojawić się dane wyjściowe z MongoDB `_id` nowo utworzonego dokumentu:

```bash
added todo ObjectID("5e9fd6befd2f076d1f03bd8a")
```

Utwórz inny `todo`

```bash
./todo --create "Get the MongoDB connection string using the Azure CLI"
```

Wyświetl wszystkie `todo` elementy s

```bash
./todo --list all
```

Powinny zostać wyświetlone te, które zostały właśnie dodane w formacie tabelarycznym, jako takie

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | pending   |
|                            | database account               |           |
| "5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

Aby zaktualizować stan elementu `todo` (np. Zmień go na `completed` stan), użyj `todo` identyfikatora

```bash
./todo --update 5e9fd6b1bcd2fa6bd267d4c4,completed
```

Wyświetl `todo` tylko zakończone

```bash
./todo --list completed
```

Powinien zostać wyświetlony właśnie zaktualizowany

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | completed |
|                            | database account               |           |
+----------------------------+--------------------------------+-----------+
```

### <a name="view-data-in-data-explorer"></a>Wyświetlanie danych w Eksploratorze danych

Dane przechowywane w Azure Cosmos DB są dostępne do wyświetlania i wykonywania zapytań w Azure Portal.

Aby wyświetlać dane użytkownika utworzone w poprzednim kroku, a także pracować z nimi i wykonywać na nich zapytania, zaloguj się do witryny [Azure Portal](https://portal.azure.com) w przeglądarce sieci Web.

W górnym polu wyszukiwania wprowadź **Azure Cosmos DB**. Po otwarciu bloku konta usługi Cosmos wybierz swoje konto usługi Cosmos. Na lewym pasku nawigacyjnym wybierz pozycję **Eksplorator danych**. Rozwiń kolekcję w okienku Kolekcje. Następnie możesz wyświetlić dokumenty w kolekcji, wysłać zapytanie dotyczące danych, a nawet tworzyć i uruchamiać procedury składowane, wyzwalacze i funkcje definiowane przez użytkownika (UDF). 

:::image type="content" source="./media/create-mongodb-go/go-cosmos-db-data-explorer.png" alt-text="Eksplorator danych z nowo utworzonym dokumentem":::


Usuń `todo` Identyfikator using

```bash
./todo --delete 5e9fd6b1bcd2fa6bd267d4c4,completed
```

Wyświetl listę `todo` s do potwierdzenia

```bash
./todo --list all
```

`todo`Właśnie usunięty nie powinien być obecny

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia konta interfejsu API usługi Azure Cosmos DB MongoDB przy użyciu Azure Cloud Shell i tworzenia i uruchamiania aplikacji w wierszu polecenia programu go w celu zarządzania usługą `todo` . Teraz możesz zaimportować dodatkowe dane do swojego konta usługi Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Importowanie danych z bazy danych MongoDB do usługi Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)