---
title: 'Szybki Start: łączenie Node.js aplikacji MongoDB z Azure Cosmos DB'
description: W tym przewodniku Szybki start pokazano, jak połączyć istniejącą aplikację MongoDB utworzoną na platformie Node.js z usługą Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/21/2019
ms.custom: seo-javascript-september2019, seo-javascript-october2019, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 5c0de1ccf4b6d2db44b1e315e73a84dd712b3f6c
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94564025"
---
# <a name="quickstart-migrate-an-existing-mongodb-nodejs-web-app-to-azure-cosmos-db"></a>Szybki Start: Migrowanie istniejącej aplikacji sieci Web MongoDB Node.js do Azure Cosmos DB 
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

W tym przewodniku szybki start utworzysz Azure Cosmos DB konta interfejsu API usługi Mongo DB i zarządzasz nim przy użyciu Azure Cloud Shell i z ŚREDNIą aplikacją (MongoDB, Express, skośną i Node.js) sklonowaną z usługi GitHub. Azure Cosmos DB to wielomodelowa usługa bazy danych, która pozwala szybko tworzyć i wysyłać zapytania dotyczące dokumentów, tabel, kluczy i wartościowych baz danych przy użyciu dystrybucji globalnej i możliwości skalowania w poziomie.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Lub [Wypróbuj bezpłatnie Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure. Można również użyć [emulatora Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) z parametrami połączenia `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true` .

- [Node.js](https://nodejs.org/)i praktyczną wiedzę na temat Node.js.

- Usługi [git](https://git-scm.com/downloads).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ten artykuł wymaga wersji 2,0 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.


## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Uruchom następujące polecenia w celu sklonowania przykładowego repozytorium. To przykładowe repozytorium zawiera domyślną aplikację [MEAN.js](https://meanjs.org/).

1. Otwórz wiersz polecenia, utwórz nowy folder o nazwie git-samples, a następnie zamknij wiersz polecenia.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Otwórz okno terminala usługi Git, np. git bash, i użyj polecenia `cd`, aby przejść do nowego folderu instalacji aplikacji przykładowej.

    ```bash
    cd "C:\git-samples"
    ```

3. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze. 

    ```bash
    git clone https://github.com/prashanthmadi/mean
    ```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Ta aplikacja MongoDB napisywana w Node.js nawiązuje połączenie z bazą danych Azure Cosmos DB, która obsługuje klienta MongoDB. Innymi słowy, jest niewidoczny dla aplikacji, że dane są przechowywane w bazie danych Azure Cosmos DB.

Zainstaluj wymagane pakiety i uruchom aplikację.

```bash
cd mean
npm install
npm start
```
Aplikacja podejmie próbę połączenia się ze źródłem bazy danych MongoDB, która zakończy się niepowodzeniem. Zamknij aplikację w przypadku zwrócenia przez dane wyjściowe błędu „[MongoError: connect ECONNREFUSED 127.0.0.1:27017]”.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Jeśli używasz zainstalowanego interfejsu wiersza polecenia platformy Azure, zaloguj się do subskrypcji platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index#az-login) i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie. Ten krok możesz pominąć, jeśli używasz powłoki Azure Cloud Shell.

```azurecli
az login 
``` 
   
## <a name="add-the-azure-cosmos-db-module"></a>Dodawanie modułu Azure Cosmos DB

Jeśli używasz zainstalowanego interfejsu wiersza polecenia platformy Azure, sprawdź, czy składnik `cosmosdb` jest już zainstalowany, uruchamiając polecenie `az`. Jeśli `cosmosdb` znajduje się na liście podstawowych poleceń, przejdź do następnego polecenia. Ten krok możesz pominąć, jeśli używasz powłoki Azure Cloud Shell.

Jeśli `cosmosdb` nie znajduje się na liście podstawowych poleceń, zainstaluj ponownie [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz [grupę zasobów](../azure-resource-manager/management/overview.md) za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure, takich jak aplikacje internetowe, bazy danych i konta magazynu, oraz zarządzania nimi. 

Poniższy przykład obejmuje tworzenie grupy zasobów w regionie Europa Zachodnia. Wybierz unikatową nazwę grupy zasobów.

Jeśli używasz Azure Cloud Shell, wybierz opcję **Wypróbuj** , postępuj zgodnie z monitami Onscreen, aby zalogować się, a następnie skopiuj polecenie do wiersza polecenia.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

## <a name="create-an-azure-cosmos-db-account"></a>Tworzenie konta usługi Azure Cosmos DB

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

## <a name="connect-your-nodejs-application-to-the-database"></a>Połączenie aplikacji Node.js z bazą danych

W tym kroku połączysz przykładową aplikację MEAN.js z właśnie utworzonym kontem bazy danych Azure Cosmos DB. 

<a name="devconfig"></a>
## <a name="configure-the-connection-string-in-your-nodejs-application"></a>Konfigurowanie parametrów połączenia w aplikacji Node.js

W repozytorium MEAN.js otwórz plik `config/env/local-development.js`.

Zastąp zawartość tego pliku następującym kodem. Należy również zastąpić dwa symbole zastępcze `<cosmosdb-name>` nazwą konta usługi Cosmos.

```javascript
'use strict';

module.exports = {
  db: {
    uri: 'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean-dev?ssl=true&sslverifycertificate=false'
  }
};
```

## <a name="retrieve-the-key"></a>Pobieranie klucza

Aby połączyć się z bazą danych usługi Cosmos, niezbędny jest klucz bazy danych. Użyj polecenia [AZ cosmosdb Keys list](/cli/azure/cosmosdb/keys#az-cosmosdb-keys-list) , aby pobrać klucz podstawowy.

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

W interfejsie wiersza polecenia platformy Azure zostaną wyświetlone informacje podobne do następującego przykładu. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

Skopiuj wartość `primaryMasterKey`. Wklej ją zamiast `<primary_master_key>` w `local-development.js`.

Zapisz zmiany.

### <a name="run-the-application-again"></a>Uruchom ponownie aplikację.

Uruchom ponownie polecenie `npm start`. 

```bash
npm start
```

Komunikat na konsoli powinien stwierdzać, że środowisko programistyczne jest uruchomione i gotowe do pracy. 

Przejdź do programu `http://localhost:3000` w przeglądarce. Wybierz pozycję Utwórz **konto** w górnym menu i spróbuj utworzyć dwóch fikcyjnych użytkowników. 

Przykładowa aplikacja MEAN.js przechowuje dane użytkowników w bazie danych. Jeśli wszystko przebiega poprawnie i aplikacja MEAN.js automatycznie zaloguje się do utworzonego użytkownika, oznacza to, że połączenie z usługą Azure Cosmos DB działa. 

:::image type="content" source="./media/create-mongodb-nodejs/mongodb-connect-success.png" alt-text="Pomyślne połączenie MEAN.js z MongoDB":::

## <a name="view-data-in-data-explorer"></a>Wyświetlanie danych w Eksploratorze danych

Dane przechowywane w bazie danych usługi Cosmos są dostępne do wyświetlania i wykonywania zapytań w witrynie Azure Portal.

Aby wyświetlać dane użytkownika utworzone w poprzednim kroku, a także pracować z nimi i wykonywać na nich zapytania, zaloguj się do witryny [Azure Portal](https://portal.azure.com) w przeglądarce sieci Web.

W górnym polu wyszukiwania wprowadź **Azure Cosmos DB**. Po otwarciu bloku konta usługi Cosmos wybierz swoje konto usługi Cosmos. Na lewym pasku nawigacyjnym wybierz pozycję **Eksplorator danych**. Rozwiń kolekcję w okienku Kolekcje. Następnie możesz wyświetlić dokumenty w kolekcji, wysłać zapytanie dotyczące danych, a nawet tworzyć i uruchamiać procedury składowane, wyzwalacze i funkcje definiowane przez użytkownika (UDF). 

:::image type="content" source="./media/create-mongodb-nodejs/cosmosdb-connect-mongodb-data-explorer.png" alt-text="Eksplorator danych w witrynie Azure Portal":::


## <a name="deploy-the-nodejs-application-to-azure"></a>Wdrażanie aplikacji Node.js na platformie Azure

W tym kroku należy wdrożyć aplikację w języku Node.js w usłudze Cosmos DB.

Można było zauważyć, że plik konfiguracji, który został zmodyfikowany wcześniej, jest przeznaczony dla środowiska programistycznego (`/config/env/local-development.js`). Po wdrożeniu aplikacji w usłudze App Service domyślnie będzie ona uruchamiana w środowisku produkcyjnym. Teraz musisz wprowadzić tę samą zmianę w odpowiednim pliku konfiguracji.

W repozytorium MEAN.js otwórz plik `config/env/production.js`.

W obiekcie `db` zastąp wartość `uri` tak jak pokazano w poniższym przykładzie. Pamiętaj, aby zamienić symbole zastępcze tak jak poprzednio.

```javascript
'mongodb://<cosmosdb-name>:<primary_master_key>@<cosmosdb-name>.documents.azure.com:10255/mean?ssl=true&sslverifycertificate=false',
```

> [!NOTE] 
> Ta `ssl=true` opcja jest ważna ze względu na wymagania Cosmos DB. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące parametrów połączenia](connect-mongodb-account.md#connection-string-requirements).
>
>

Na terminalu zatwierdź wszystkie zmiany w środowisku Git. Możesz skopiować oba polecenia, aby uruchomić je razem.

```bash
git add .
git commit -m "configured MongoDB connection string"
```
## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia konta interfejsu API usługi Azure Cosmos DB MongoDB przy użyciu Azure Cloud Shell i tworzenia i uruchamiania aplikacji MEAN.js w celu dodania użytkowników do konta. Teraz możesz zaimportować dodatkowe dane do swojego konta usługi Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Importowanie danych z bazy danych MongoDB do usługi Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%252fazure%252fcosmos-db%252ftoc.json%253ftoc%253d%252fazure%252fcosmos-db%252ftoc.json)
