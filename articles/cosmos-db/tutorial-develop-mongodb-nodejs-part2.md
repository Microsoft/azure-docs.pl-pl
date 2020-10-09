---
title: Tworzenie aplikacji Node.js Express za pomocą interfejsu API Azure Cosmos DB dla MongoDB (part2)
description: Część 2 z serii samouczków o tworzeniu aplikacji bazy danych MongoDB przy użyciu usługi Angular i języka Node dla usługi Azure Cosmos DB przy użyciu dokładnie tych samych interfejsów API, które były używane dla bazy danych MongoDB.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: a796ae32033dbc09b1f5b888ded9ea5e1f4c457e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86523489"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---create-a-nodejs-express-app"></a>Tworzenie aplikacji Angular przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB — tworzenie aplikacji Node.js Express

Ten wieloczęściowy samouczek pokazuje, jak utworzyć nową aplikację napisaną w środowisku Node.js za pomocą programu Express i platformy Angular, a następnie podłączyć ją do [konta usługi Cosmos skonfigurowanego przy użyciu interfejsu API usługi Cosmos DB dla bazy danych MongoDB](mongodb-introduction.md).

Część 2 samouczka jest oparta na [wprowadzeniu](tutorial-develop-mongodb-nodejs.md) i obejmuje następujące zadania:

> [!div class="checklist"]
> * Instalowanie interfejsu wiersza polecenia usługi Angular i języka TypeScript
> * Tworzenie nowego projektu przy użyciu usługi Angular
> * Tworzenie aplikacji przy użyciu platformy Express
> * Testowanie aplikacji w narzędziu Postman

## <a name="video-walkthrough"></a>Przewodnik wideo

> [!VIDEO https://www.youtube.com/embed/lIwJIYcGSUg]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tej części samouczka pamiętaj o obejrzeniu [filmu wideo z wprowadzeniem](tutorial-develop-mongodb-nodejs.md).

Dla tego samouczka wymagane są również następujące elementy: 
* Środowisko [Node.js](https://nodejs.org/) w wersji 8.4.0 lub nowszej.
* [Postman](https://www.getpostman.com/)
* Program [Visual Studio Code](https://code.visualstudio.com/) lub wybrany edytor kodu.

> [!TIP]
> Ten samouczek przedstawia instrukcje tworzenia aplikacji krok po kroku. Jeśli chcesz pobrać gotowy projekt, ukończoną aplikację znajdziesz w [repozytorium angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) w witrynie GitHub.

## <a name="install-the-angular-cli-and-typescript"></a>Instalowanie interfejsu wiersza polecenia usługi Angular i języka TypeScript

1. Otwórz okno wiersza polecenia systemu Windows lub okno terminala na komputerze Mac i zainstaluj interfejs wiersza polecenia usługi Angular.

    ```bash
    npm install -g @angular/cli
    ```

2. Zainstaluj język TypeScript, wprowadzając poniższe polecenie w wierszu polecenia. 

    ```bash
    npm install -g typescript
    ```

## <a name="use-the-angular-cli-to-create-a-new-project"></a>Tworzenie nowego projektu przy użyciu interfejsu wiersza polecenia usługi Angular

1. W wierszu polecenia przejdź do folderu, w którym chcesz utworzyć nowy projekt, a następnie uruchom poniższe polecenie. To polecenie tworzy nowy folder i projekt o nazwie angular-cosmosdb i instaluje składniki usługi Angular wymagane dla nowej aplikacji. Używa ono minimalnej instalacji (--minimal) i wskazuje, że projekt korzysta z rozwiązania Sass (składnia przypominająca język CSS ustawiana za pomocą flagi --style scss).

    ```bash
    ng new angular-cosmosdb --minimal --style scss
    ```

2. Po wykonaniu polecenia przejdź do katalogów w folderze src/client.

    ```bash
    cd angular-cosmosdb
    ```

3. Następnie otwórz folder w programie Visual Studio Code.

    ```bash
    code .
    ```

## <a name="build-the-app-using-the-express-framework"></a>Tworzenie aplikacji przy użyciu platformy Express

1. W programie Visual Studio Code w okienku **Eksplorator** kliknij prawym przyciskiem myszy folder **src**, kliknij pozycję **Nowy folder** i nadaj nowemu folderowi nazwę *server*.

2. W okienku **Eksplorator** kliknij prawym przyciskiem myszy folder **server**, kliknij pozycję **Nowy plik** i nadaj nowemu plikowi nazwę *index.js*.

3. Wróć do wiersza polecenia i użyj poniższego polecenia, aby zainstalować analizator treści. Ułatwi to naszej aplikacji analizowanie danych JSON, które są przekazywane za pośrednictwem interfejsów API.

    ```bash
    npm i express body-parser --save
    ```

4. W programie Visual Studio Code skopiuj poniższy kod do pliku index.js. Ten kod:
    * Odwołuje się do platformy Express
    * Wywołuje analizator treści na potrzeby odczytywania danych JSON w treści żądań
    * Używa wbudowanej funkcji o nazwie path
    * Ustawia zmienne główne, które ułatwiają wyszukiwanie lokalizacji kodu
    * Konfiguruje port
    * Zwiększa wydajność platformy Express
    * Przekazuje do aplikacji informacje na temat sposobu korzystania z oprogramowania pośredniczącego, które miało być używane do obsługi serwera
    * Obsługuje wszystkie zasoby znajdujące się w folderze dist, które będą zawartością statyczną
    * Obsługuje aplikację i plik index.html dla żądań GET nieznalezionych na serwerze (dla linków bezpośrednich)
    * Uruchamia serwer przy użyciu elementu app.listen
    * Używa funkcji Strzałka do rejestrowania, że port jest aktywny
    
   ```node
   const express = require('express');
   const bodyParser = require('body-parser');
   const path = require('path');
   const routes = require('./routes');

   const root = './';
   const port = process.env.PORT || '3000';
   const app = express();

   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(express.static(path.join(root, 'dist/angular-cosmosdb')));
   app.use('/api', routes);
   app.get('*', (req, res) => {
     res.sendFile('dist/angular-cosmosdb/index.html', {root});
   });

   app.listen(port, () => console.log(`API running on localhost:${port}`));
   ```

5. W programie Visual Studio Code w okienku **Eksplorator** kliknij prawym przyciskiem myszy folder **server**, a następnie kliknij pozycję **Nowy plik**. Nadaj nowemu plikowi nazwę *routes.js*. 

6. Skopiuj poniższy kod do pliku **routes.js**. Ten kod:
   * Odwołuje się do routera platformy Express
   * Pobiera elementy hero
   * Wysyła z powrotem dane JSON dla zdefiniowanego elementu hero

   ```node
   const express = require('express');
   const router = express.Router();

   router.get('/heroes', (req, res) => {
    res.send(200, [
       {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    ])
   });

   module.exports=router;
   ```

7. Zapisz wszystkie zmodyfikowane pliki. 

8. W Visual Studio Code kliknij przycisk **Debuguj** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/debug-button.png"::: , a następnie kliknij przycisk koła zębatego :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/gear-button.png"::: . Nowy plik launch.json zostanie otwarty w programie Visual Studio Code. 

8. W wierszu 11 pliku launch.json, zmień wartość `"${workspaceFolder}\\server"` na `"program": "${workspaceRoot}/src/server/index.js"` i zapisz plik.

9. Kliknij przycisk **Rozpocznij debugowanie** , :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/start-debugging-button.png"::: Aby uruchomić aplikację.

    Aplikacja powinna działać bez błędów.

## <a name="use-postman-to-test-the-app"></a>Testowanie aplikacji przy użyciu narzędzia Postman

1. Teraz otwórz narzędzie Postman i w polu GET wstaw wartość `http://localhost:3000/api/heroes`. 

2. Kliknij przycisk **Wyślij** przycisk i pobierz odpowiedź JSON z aplikacji. 

    Ta odpowiedź pokazuje, że aplikacja została uruchomiona i działa lokalnie. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part2/azure-cosmos-db-postman.png" alt-text="Narzędzie Postman przedstawiające żądanie i odpowiedź":::


## <a name="next-steps"></a>Następne kroki

W tej części samouczka zostały wykonane następujące zadania:

> [!div class="checklist"]
> * Utworzono projekt Node.js przy użyciu interfejsu wiersza polecenia usługi Angular
> * Przetestowano aplikację przy użyciu narzędzia Postman

Możesz przejść do następnej części samouczka, aby utworzyć interfejs użytkownika.

> [!div class="nextstepaction"]
> [tworzenie interfejsu użytkownika przy użyciu usługi Angular](tutorial-develop-mongodb-nodejs-part3.md)
