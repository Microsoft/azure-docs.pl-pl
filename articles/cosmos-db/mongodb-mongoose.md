---
title: Łączenie Node.js aplikacji Mongoose do Azure Cosmos DB
description: Dowiedz się, jak używać struktury Mongoose do przechowywania danych i zarządzania nimi w Azure Cosmos DB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18, devx-track-js
ms.openlocfilehash: 8958699ae279d2613f8dbadca802ee2137407e75
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442416"
---
# <a name="connect-a-nodejs-mongoose-application-to-azure-cosmos-db"></a>Łączenie Node.js aplikacji Mongoose do Azure Cosmos DB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

W tym samouczku pokazano, jak używać [platformy Mongoose](https://mongoosejs.com/) w przypadku przechowywania danych w Cosmos DB. Dla tego przewodnika korzystamy z interfejsu API Azure Cosmos DB dla MongoDB. Mongoose to platforma modelowania obiektów usługi MongoDB w środowisku Node.js. Udostępnia ona także proste, bazujące na schematach rozwiązanie do modelowania danych aplikacji.

Cosmos DB to globalnie dystrybuowana wielomodelowa usługa bazy danych firmy Microsoft. Dzięki dystrybucji globalnej i możliwości skalowania poziomego w usłudze Cosmos DB możesz szybko tworzyć i za pomocą zapytań badać bazy danych dokumentów, par klucz/wartość oraz grafów.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[Node.js](https://nodejs.org/) w wersji v 0.10.29 lub nowszej.

## <a name="create-a-cosmos-account"></a>Utwórz konto Cosmos

Utwórzmy konto Cosmos. Jeśli masz już konto, którego chcesz użyć, możesz przejść od razu do kroku Konfigurowanie aplikacji Node.js. Jeśli używasz emulatora Azure Cosmos DB, postępuj zgodnie z instrukcjami w [Azure Cosmos DB emulator](local-emulator.md) w celu skonfigurowania emulatora i Przeskocz do konfiguracji aplikacji Node.js.

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

### <a name="create-a-database"></a>Tworzenie bazy danych 
W tej aplikacji omówiono dwa sposoby tworzenia kolekcji w Azure Cosmos DB: 
- **Przechowywanie każdego modelu obiektów w oddzielnej kolekcji** : zalecamy [utworzenie bazy danych z dedykowaną przepływność](set-throughput.md#set-throughput-on-a-database). Korzystanie z tego modelu wydajności zapewnia lepszą wydajność.

    :::image type="content" source="./media/mongodb-mongoose/db-level-throughput.png" alt-text=" SamouczekNode.js — zrzut ekranu przedstawiający Azure Portal, pokazujący sposób tworzenia bazy danych w Eksplorator danych dla konta Azure Cosmos DB, do użycia z modułem węzła Mongoose":::

- **Przechowywanie wszystkich modeli obiektów w pojedynczej kolekcji Cosmos DB** : Jeśli wolisz przechowywać wszystkie modele w pojedynczej kolekcji, możesz po prostu utworzyć nową bazę danych bez wybierania opcji udostępniania przepływności. Użycie tego modelu wydajności spowoduje utworzenie każdej kolekcji z własną przepustowością dla każdego modelu obiektów.

Po utworzeniu bazy danych użyjesz nazwy w `COSMOSDB_DBNAME` zmiennej środowiskowej poniżej.

## <a name="set-up-your-nodejs-application"></a>Konfigurowanie aplikacji Node.js

>[!Note]
> Jeśli chcesz tylko prześledzić przykładowy kod bez konfigurowania samej aplikacji, sklonuj [przykład](https://github.com/Azure-Samples/Mongoose_CosmosDB) używany na potrzeby tego samouczka i skompiluj aplikację Mongoose napisaną w języku Node.js w usłudze Azure Cosmos DB.

1. Aby utworzyć aplikację Node.js w wybranym folderze, uruchom następujące polecenie w wierszu polecenia węzła.

    ```npm init```

    Po odpowiedzeniu na wyświetlone pytania projekt będzie gotowy.

2. Dodaj nowy plik do folderu i nadaj mu nazwę ```index.js```.
3. Zainstaluj wymagane pakiety przy użyciu jednej z opcji polecenia ```npm install```:
   * Mongoose: ```npm install mongoose@5 --save```

     > [!Note]
     > Poniższe przykładowe połączenie Mongoose jest oparte na Mongoose 5 +, które uległy zmianie od wcześniejszych wersji.
    
   * Dotenv (jeśli chcesz załadować wpisy tajne z pliku env): ```npm install dotenv --save```

     >[!Note]
     > Parametr ```--save``` powoduje dodanie zależności do pliku package.json.

4. Zaimportuj zależności w pliku index.js.

    ```JavaScript
   var mongoose = require('mongoose');
   var env = require('dotenv').config();   //Use the .env file to load the variables
    ```

5. Dodaj parametry połączenia usługi Cosmos DB i nazwę bazy danych Cosmos DB do pliku ```.env```. Zastąp symbole zastępcze {Cosmos-account-name} i {dbname} własną nazwą konta Cosmos i nazwą bazy danych bez symboli w nawiasach klamrowych.

    ```JavaScript
   # You can get the following connection details from the Azure portal. You can find the details on the Connection string pane of your Azure Cosmos account.

   COSMOSDB_USER = "<Azure Cosmos account's user name, usually the database account name>"
   COSMOSDB_PASSWORD = "<Azure Cosmos account password, this is one of the keys specified in your account>"
   COSMOSDB_DBNAME = "<Azure Cosmos database name>"
   COSMOSDB_HOST= "<Azure Cosmos Host name>"
   COSMOSDB_PORT=10255
    ```

6. Połącz się z Cosmos DB przy użyciu platformy mongoose, dodając następujący kod na końcu index.js.
    ```JavaScript
   mongoose.connect("mongodb://"+process.env.COSMOSDB_HOST+":"+process.env.COSMOSDB_PORT+"/"+process.env.COSMOSDB_DBNAME+"?ssl=true&replicaSet=globaldb", {
      auth: {
        user: process.env.COSMOSDB_USER,
        password: process.env.COSMOSDB_PASSWORD
      },
    useNewUrlParser: true,
    useUnifiedTopology: true,
    retryWrites: false
    })
    .then(() => console.log('Connection to CosmosDB successful'))
    .catch((err) => console.error(err));
    ```
    >[!Note]
    > W tym miejscu zmienne środowiskowe są ładowane przy użyciu metody process.env.{nazwa_zmiennej} i pakietu npm „dotenv”.

    Teraz, po nawiązaniu połączenia z usługą Azure Cosmos DB, możesz rozpocząć konfigurowanie modeli obiektów na platformie Mongoose.

## <a name="best-practices-for-using-mongoose-with-cosmos-db"></a>Najlepsze rozwiązania dotyczące używania Mongoose z Cosmos DB

Dla każdego tworzonego modelu Mongoose tworzy nową kolekcję. Jest to najlepsze rozwiązanie przy użyciu [opcji przepływności na poziomie bazy danych](set-throughput.md#set-throughput-on-a-database), która została wcześniej omówiona. Aby użyć pojedynczej kolekcji, należy użyć [rozróżniaczów](https://mongoosejs.com/docs/discriminators.html)Mongoose. Dyskryminatory to mechanizm dziedziczenia schematów. Umożliwiają one posiadanie wielu modeli z nakładającymi się schematami w jednej bazowej kolekcji usługi MongoDB.

Dzięki temu można przechowywać różne modele danych w tej samej kolekcji, a następnie, korzystając z klauzuli filtru w czasie przetwarzania zapytania, pobierać tylko potrzebne dane. Przejdźmy do poszczególnych modeli.

### <a name="one-collection-per-object-model"></a>Jedna kolekcja na model obiektów

W tej sekcji omówiono, jak to osiągnąć za pomocą interfejsu API Azure Cosmos DB MongoDB. Ta metoda jest zalecanym rozwiązaniem, ponieważ pozwala na kontrolę kosztów i pojemności. W związku z tym ilość jednostek żądania w bazie danych nie zależy od liczby modeli obiektów. Jest to domyślny model operacyjny dla mongoose, więc możesz zapoznać się z tym.

1. Otwórz ponownie plik ```index.js```.

1. Utwórz definicję schematu modelu „Family” (Rodzina).

    ```JavaScript
    const Family = mongoose.model('Family', new mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
            gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }));
    ```

1. Utwórz obiekt w modelu „Family” (Rodzina).

    ```JavaScript
    const family = new Family({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Buddy" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });
    ```

1. Na koniec Zapisz obiekt w Cosmos DB. Spowoduje to utworzenie kolekcji w obiekcie nadrzędnym.

    ```JavaScript
    family.save((err, saveFamily) => {
        console.log(JSON.stringify(saveFamily));
    });
    ```

1. Teraz utwórzmy jeszcze jeden schemat i obiekt. Tym razem utwórzmy te elementy dla modelu „Vacation Destinations” (Miejsca spędzania wakacji) zawierającego miejsca atrakcyjne dla rodziny.
   1. Podobnie jak wcześniej utwórz schemat.
      ```JavaScript
      const VacationDestinations = mongoose.model('VacationDestinations', new mongoose.Schema({
       name: String,
       country: String
      }));
      ```

   1. Utwórz przykładowy obiekt (do tego schematu można dodać wiele obiektów) i zapisz go.
      ```JavaScript
      const vacaySpot = new VacationDestinations({
       name: "Honolulu",
       country: "USA"
      });

      vacaySpot.save((err, saveVacay) => {
       console.log(JSON.stringify(saveVacay));
      });
      ```

1. Teraz, przechodząc do Azure Portal, zauważysz dwie kolekcje utworzone w Cosmos DB.

   :::image type="content" source="./media/mongodb-mongoose/mongo-mutliple-collections.png" alt-text=" SamouczekNode.js — zrzut ekranu przedstawiający Azure Portal, z uwzględnieniem konta Azure Cosmos DB z wyróżnionymi wieloma nazwami kolekcji — baza danych Node":::

1. Na koniec przeczytajmy dane z Cosmos DB. Ponieważ korzystamy z domyślnego modelu działania platformy Mongoose, operacje odczytywania są takie same jak inne operacje odczytywania na platformie Mongoose.

    ```JavaScript
    Family.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family: " + JSON.stringify(fam)));
    });
    ```

### <a name="using-mongoose-discriminators-to-store-data-in-a-single-collection"></a>Korzystanie z dyskryminatorów platformy Mongoose w celu umożliwienia przechowywania danych w pojedynczej kolekcji

W przypadku tej metody używamy [rozróżniaczów Mongoose](https://mongoosejs.com/docs/discriminators.html) , aby ułatwić optymalizację kosztów każdej kolekcji. Dyskryminatory umożliwiają zdefiniowanie różnicującego „klucza”, który umożliwi przechowywanie, różnicowanie i filtrowanie różnych modeli obiektów.

W tym miejscu utworzymy bazowy model obiektów, zdefiniujemy klucz różnicujący i dodamy modele „Family” i „VacationDestinations” jako rozszerzenia modelu bazowego.

1. Ustawmy podstawową konfigurację i zdefiniujmy klucz dyskryminatora.

    ```JavaScript
    const baseConfig = {
        discriminatorKey: "_type", //If you've got a lot of different data types, you could also consider setting up a secondary index here.
        collection: "alldata"   //Name of the Common Collection
    };
    ```

1. Następnie zdefiniujmy wspólny model obiektów.

    ```JavaScript
    const commonModel = mongoose.model('Common', new mongoose.Schema({}, baseConfig));
    ```

1. Teraz definiujemy model „Family”. Zauważ, że teraz używamy metody ```commonModel.discriminator``` zamiast ```mongoose.model```. Ponadto dodajemy również podstawową konfigurację do schematu mongoose. W tym przypadku kluczem dyskryminatora jest więc ```FamilyType```.

    ```JavaScript
    const Family_common = commonModel.discriminator('FamilyType', new     mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
           gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }, baseConfig));
    ```

1. Podobnie dodajmy kolejny schemat, tym razem dla modelu „VacationDestinations”. Tutaj kluczem dyskryminatora jest ```VacationDestinationsType```.

    ```JavaScript
    const Vacation_common = commonModel.discriminator('VacationDestinationsType', new mongoose.Schema({
        name: String,
        country: String
    }, baseConfig));
    ```

1. Na koniec utworzymy obiekty modelu i zapiszemy go.
   1. Dodajmy obiekty do modelu „Family”.
      ```JavaScript
      const family_common = new Family_common({
       lastName: "Volum",
       parents: [
           { firstName: "Thomas" },
           { firstName: "Mary Kay" }
       ],
       children: [
           { firstName: "Ryan", gender: "male", grade: 8 },
           { firstName: "Patrick", gender: "male", grade: 7 }
       ],
       pets: [
           { givenName: "Buddy" }
       ],
       address: { country: "USA", state: "WA", city: "Seattle" }
      });

      family_common.save((err, saveFamily) => {
       console.log("Saved: " + JSON.stringify(saveFamily));
      });
      ```

   1. Następnie dodajmy obiekty do modelu „VacationDestinations” i zapiszmy go.
      ```JavaScript
      const vacay_common = new Vacation_common({
       name: "Honolulu",
       country: "USA"
      });

      vacay_common.save((err, saveVacay) => {
       console.log("Saved: " + JSON.stringify(saveVacay));
      });
      ```

1. Jeśli teraz wrócisz do witryny Azure Portal, zauważysz, że znajduje się tam tylko jedna kolekcja o nazwie ```alldata```, która zawiera dane zarówno z modelu „Family”, jak i „VacationDestinations”.

   :::image type="content" source="./media/mongodb-mongoose/mongo-collections-alldata.png" alt-text=" SamouczekNode.js — zrzut ekranu przedstawiający Azure Portal, pokazujący konto Azure Cosmos DB z wyróżnioną nazwą kolekcji — baza danych Node":::

1. Zauważ również, że każdy obiekt ma jeszcze jeden atrybut o nazwie ```__type```, który pomaga rozróżnić te dwa różne modele obiektów.

1. Na koniec spróbujmy odczytać dane przechowywane w usłudze Azure Cosmos DB. Platforma Mongoose dba o odpowiednie filtrowanie danych na podstawie modelu. Podczas odczytywania danych nie trzeba robić niczego więcej. Wystarczy tylko określić model (w tym przypadku ```Family_common```), a platforma Mongoose zajmie się filtrowaniem według klucza „DiscriminatorKey”.

    ```JavaScript
    Family_common.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family (using discriminator): " + JSON.stringify(fam)));
    });
    ```

Jak widać, praca z dyskryminatorami platformy Mongoose jest prosta. Dlatego jeśli masz aplikację, która korzysta z platformy Mongoose Framework, ten samouczek jest sposobem na rozpoczęcie pracy aplikacji przy użyciu interfejsu API usługi Azure Cosmos dla MongoDB bez konieczności zbyt wielu zmian.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [korzystać z programu Studio 3T](mongodb-mongochef.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Dowiedz się, jak [korzystać z programu Robo 3T](mongodb-robomongo.md) za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.
- Eksploruj [przykłady](mongodb-samples.md) bazy danych MongoDB za pomocą interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB.

[dbleveltp]: ./media/mongodb-mongoose/db-level-throughput.png
