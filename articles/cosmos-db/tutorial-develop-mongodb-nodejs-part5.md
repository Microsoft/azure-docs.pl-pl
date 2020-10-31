---
title: Połącz aplikację kątową z interfejsem API Azure Cosmos DB dla MongoDB za pomocą Mongoose
description: W tym samouczku opisano sposób tworzenia aplikacji w środowisku Node.js przy użyciu platformy Angular i programu Express do zarządzania danymi przechowywanymi w usłudze Cosmos DB. W tej części wtyczka Mongoose zostanie użyta do nawiązania połączenia z usługą Azure Cosmos DB.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: a3097fa539f460ef5e8ffe73598fa5d55516717e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097808"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---use-mongoose-to-connect-to-cosmos-db"></a>Tworzenie aplikacji Angular przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB — łączenie z usługą Cosmos DB za pomocą wtyczki Mongoose
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

Ten wieloczęściowy samouczek pokazuje, jak utworzyć aplikację Node.js za pomocą programu Express i platformy Angular, a następnie podłączyć ją do [konta usługi Cosmos skonfigurowanego przy użyciu interfejsu API usługi Cosmos DB dla bazy danych MongoDB](mongodb-introduction.md). Ten artykuł stanowi 5. część samouczka i opiera się na [części 4.](tutorial-develop-mongodb-nodejs-part4.md)

W tej części samouczka zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Łączenie z usługą Cosmos DB przy użyciu wtyczki Mongoose.
> * Uzyskiwanie parametrów połączenia z usługą Cosmos DB.
> * Tworzenie modelu elementów hero.
> * Tworzenie usługi hero w celu pobrania danych elementów hero.
> * Uruchom aplikację lokalnie.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem [utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

* Przed rozpoczęciem tego samouczka wykonaj kroki przedstawione w [części 4](tutorial-develop-mongodb-nodejs-part4.md).

* Ten samouczek wymaga uruchomienia interfejsu wiersza polecenia platformy Azure lokalnie. Musi być zainstalowany wiersz polecenia platformy Azure w wersji 2.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja interfejsu wiersza polecenia lub jego uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0](/cli/azure/install-azure-cli).

* Ten samouczek przedstawia instrukcje tworzenia aplikacji krok po kroku. Jeśli chcesz pobrać gotowy projekt, ukończoną aplikację znajdziesz w [repozytorium angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) w witrynie GitHub.

## <a name="use-mongoose-to-connect"></a>Łączenie przy użyciu wtyczki Mongoose

Wtyczka Mongoose to biblioteka modelowania danych obiektu (ODM) dla bazy danych MongoDB i środowiska Node.js. Wtyczki Mongoose można użyć, aby nawiązać połączenie z kontem usługi Azure Cosmos DB. Aby zainstalować wtyczkę Mongoose i połączyć się z usługą Azure Cosmos DB, wykonaj następujące kroki:

1. Zainstaluj moduł npm mongoose, czyli interfejs API używany do komunikowania się z bazą danych MongoDB.

    ```bash
    npm i mongoose --save
    ```

1. W folderze **server** utwórz plik o nazwie **mongo.js** . Do tego pliku dodasz szczegóły połączenia z kontem usługi Azure Cosmos DB.

1. Skopiuj następujący kod do pliku **mongo.js** . Ten kod zapewnia następujące funkcje:

   * Wymaga wtyczki Mongoose.
   * Przesłania obietnicę Mongo w celu użycia podstawowej obietnicy wbudowanej w wersji ES6/ES2015 i nowszych.
   * Wywołuje plik env, który umożliwia skonfigurowanie pewnych elementów w oparciu o to, czy pracujesz na etapie przygotowywania, produkcji, czy programowania. Utworzysz ten plik w następnej sekcji.
   * Zawiera parametry połączenia bazy danych MongoDB, które są ustawiane w pliku env.
   * Tworzy funkcję connect, która wywołuje wtyczkę Mongoose.

     ```javascript
     const mongoose = require('mongoose');
     /**
     * Set to Node.js native promises
     * Per https://mongoosejs.com/docs/promises.html
     */
     mongoose.Promise = global.Promise;

     const env = require('./env/environment');

     // eslint-disable-next-line max-len
     const mongoUri = `mongodb://${env.accountName}:${env.key}@${env.accountName}.documents.azure.com:${env.port}/${env.databaseName}?ssl=true`;

     function connect() {
     mongoose.set('debug', true);
     return mongoose.connect(mongoUri, { useMongoClient: true });
     }

     module.exports = {
     connect,
     mongoose
     };
     ```
    
1. W okienku Eksploratora w obszarze **server** utwórz folder o nazwie **environment** . W folderze **environment** utwórz plik o nazwie **environment.js** .

1. Z pliku mongo.js musimy uwzględnić wartości parametrów `dbName`, `key` i `cosmosPort`. Skopiuj następujący kod do pliku **environment.js** :

    ```javascript
    // TODO: replace if yours are different
    module.exports = {
      accountName: 'your-cosmosdb-account-name-goes-here',
      databaseName: 'admin', 
      key: 'your-key-goes-here',
      port: 10255
    };
    ```

## <a name="get-the-connection-string"></a>Pobieranie parametrów połączenia

Aby połączyć swoją aplikację z usługą Azure Cosmos DB, musisz zaktualizować ustawienia konfiguracji dla aplikacji. Wykonaj następujące kroki, aby zaktualizować ustawienia: 

1. W witrynie Azure Portal uzyskaj numer portu, nazwę konta usługi Azure Cosmos DB i wartości klucza podstawowego dla konta usługi Azure Cosmos DB.

1. W pliku **environment.js** zmień wartość właściwości `port` na 10255. 

    ```javascript
    const port = 10255;
    ```

1. W pliku **environment.js** zmień wartość właściwości `accountName` na nazwę konta usługi Azure Cosmos DB, które zostało utworzone w [części 4.](tutorial-develop-mongodb-nodejs-part4.md) tego samouczka. 

1. Pobierz klucz podstawowy konta usługi Azure Cosmos DB, używając następującego polecenia interfejsu wiersza polecenia w oknie terminala: 

    ```azurecli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    \<cosmosdb-name> jest nazwą konta Azure Cosmos DB utworzonego w [części 4](tutorial-develop-mongodb-nodejs-part4.md) samouczka.

1. Skopiuj klucz podstawowy do pliku **environment.js** jako wartość `key`.

Teraz Twoja aplikacja ma wszystkie informacje niezbędne do łączenia się z usługą Azure Cosmos DB. 

## <a name="create-a-hero-model"></a>Tworzenie modelu elementów hero

Następnie należy zdefiniować schemat danych, które mają być przechowywane w usłudze Azure Cosmos DB, definiując plik modelu. Wykonaj następujące kroki, aby utworzyć _model elementów hero_ definiujący schemat danych:

1. W okienku Eksploratora w folderze **server** utwórz plik o nazwie **hero.model.js** .

1. Skopiuj poniższy kod do pliku **hero.model.js** . Ten kod zapewnia następujące funkcje:

   * Wymaga wtyczki Mongoose.
   * Tworzy nowy schemat z identyfikatorem, nazwą i powiedzeniem.
   * Tworzy model przy użyciu schematu.
   * Eksportuje model. 
   * Nadaje kolekcji nazwę **Heroes** (zamiast **Heros** , która jest nazwą domyślną kolekcji zgodną z regułami wtyczki Mongoose dotyczącymi nazw w liczbie mnogiej).

   ```javascript
   const mongoose = require('mongoose');

   const Schema = mongoose.Schema;

   const heroSchema = new Schema(
     {
       id: { type: Number, required: true, unique: true },
       name: String,
       saying: String
     },
     {
       collection: 'Heroes'
     }
   );

   const Hero = mongoose.model('Hero', heroSchema);

   module.exports = Hero;
   ```

## <a name="create-a-hero-service"></a>Tworzenie usługi hero

Po utworzeniu modelu elementów hero należy zdefiniować usługę do odczytywania danych i wykonywania operacji generowania listy, tworzenia, usuwania i aktualizacji. Wykonaj następujące kroki, aby utworzyć _usługę hero_ , która wykonuje zapytanie o dane w usłudze Azure Cosmos DB:

1. W okienku Eksploratora w folderze **server** utwórz plik o nazwie **hero.service.js** .

1. Skopiuj poniższy kod do pliku **hero.service.js** . Ten kod zapewnia następujące funkcje:

   * Pobiera utworzony przez Ciebie model.
   * Nawiązuje połączenie z bazą danych.
   * Tworzy zmienną `docquery`, która używa metody `hero.find` do definiowania zapytania zwracającego wszystkie elementy hero.
   * Uruchamia zapytanie przy użyciu funkcji `docquery.exec` z obietnicą w celu uzyskania listy wszystkich elementów hero, gdzie stan odpowiedzi to 200. 
   * Wysyła z powrotem komunikat o błędzie, jeśli stan ma wartość 500.
   * Pobiera elementy hero, ponieważ używane są moduły. 

   ```javascript
   const Hero = require('./hero.model');

   require('./mongo').connect();

   function getHeroes() {
     const docquery = Hero.find({});
     docquery
       .exec()
       .then(heroes => {
         res.status(200).json(heroes);
       })
       .catch(error => {
         res.status(500).send(error);
         return;
       });
   }

   module.exports = {
     getHeroes
   };
   ```

## <a name="configure-routes"></a>Konfigurowanie tras

Następnie należy skonfigurować trasy, które będą obsługiwać adresy URL dla żądań pobierania, tworzenia, odczytu i usuwania. Metody routingu określają funkcje wywołań zwrotnych (nazywane również _funkcjami obsługi_ ). Te funkcje są wywoływane, gdy aplikacja otrzymuje żądanie dotyczące określonego punktu końcowego i metody HTTP. Wykonaj następujące kroki, aby dodać usługę hero oraz zdefiniować swoje trasy:

1. W programie Visual Studio Code w pliku **routes.js** wykomentuj funkcję `res.send`, która wysyła dane przykładowego elementu hero. Zamiast niej dodaj wiersz wywołujący funkcję `heroService.getHeroes`.

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

1. W pliku **routes.js** dodaj funkcję `require` dotyczącą usługi hero:

    ```javascript
    const heroService = require('./hero.service'); 
    ```

1. W pliku **hero.service.js** zaktualizuj w następujący sposób funkcję `getHeroes`, aby pobierała parametry `req` i `res`:

    ```javascript
    function getHeroes(req, res) {
    ```

Poświęćmy teraz chwilę na zapoznanie się z wcześniejszym kodem. Najpierw wchodzimy do pliku index.js, który konfiguruje serwer węzłów. Zwróć uwagę, że ten plik konfiguruje i definiuje Twoje trasy. Następnie plik routes.js komunikuje się z usługą hero i informuje ją o konieczności pobrania funkcji, takich jak **getHeroes** , oraz przekazania żądania i odpowiedzi. Plik hero.service.js pobiera model i nawiązuje połączenie z bazą danych Mongo. Następnie po wywołaniu wykonuje funkcję **getHeroes** i zwraca odpowiedź 200. 

## <a name="run-the-app"></a>Uruchamianie aplikacji

Następnie uruchom aplikację, wykonując poniższe kroki:

1. W programie Visual Studio Code zapisz wszystkie zmiany. Po lewej stronie wybierz przycisk **Debuguj** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png"::: , a następnie wybierz przycisk **Rozpocznij debugowanie** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png"::: .

1. Teraz przełącz się na przeglądarkę. Otwórz **Narzędzia deweloperskie** i **kartę Sieć** . Przejdź do `http://localhost:3000` , a zobaczysz naszą aplikację.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png" alt-text="Nowe konto usługi Azure Cosmos DB w witrynie Azure Portal":::

W aplikacji nie są jeszcze przechowywane żadne elementy hero. W następnej części tego samouczka dodamy funkcje umieszczania, wypychania i usuwania. Następnie możemy dodawać, aktualizować i usuwać Heroes z interfejsu użytkownika przy użyciu połączeń Mongoose z naszą bazą danych Azure Cosmos. 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Możesz usunąć grupę zasobów, konto usługi Azure Cosmos DB oraz wszystkie powiązane zasoby, gdy nie będą już potrzebne. Aby usunąć grupę zasobów, wykonaj następujące kroki:

 1. Przejdź do grupy zasobów, w której zostało utworzone konto usługi Azure Cosmos DB.
 1. Wybierz pozycję **Usuń grupę zasobów** .
 1. Potwierdź nazwę grupy zasobów do usunięcia i wybierz pozycję **Usuń** .

## <a name="next-steps"></a>Następne kroki

Przejdź do części 6 samouczka, aby dodać funkcje Post, Put i Delete do aplikacji:

> [!div class="nextstepaction"]
> [Część 6: Dodawanie funkcji post, PUT i DELETE do aplikacji](tutorial-develop-mongodb-nodejs-part6.md)