---
title: Samouczek — Tworzenie średniej stosu na maszynie wirtualnej z systemem Linux na platformie Azure
description: Z tego samouczka dowiesz się, jak utworzyć stos MEAN (MongoDB, Express, AngularJS i Node.js) na maszynie wirtualnej z systemem Linux na platformie Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/08/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 9e0ed3454f11907c5f183f08fd2ec51db3384225
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80154325"
---
# <a name="tutorial-create-a-mongodb-express-angularjs-and-nodejs-mean-stack-on-a-linux-virtual-machine-in-azure"></a>Samouczek: tworzenie stosu MEAN (MongoDB, Express, AngularJS i Node.js) na maszynie wirtualnej z systemem Linux na platformie Azure

W tym samouczku pokazano, jak zaimplementować stos MEAN (MongoDB, Express, AngularJS i Node.js) na maszynie wirtualnej z systemem Linux na platformie Azure. Utworzony stos MEAN umożliwia dodawanie i usuwanie książek w bazie danych oraz wyświetlanie ich listy. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej z systemem Linux
> * Instalowanie środowiska Node.js
> * Instalowanie bazy danych MongoDB i konfigurowanie serwera
> * Instalowanie platformy Express i konfigurowanie tras do serwera
> * Uzyskiwanie dostępu do tras przy użyciu AngularJS
> * Uruchamianie aplikacji

W tym samouczku jest używany interfejs wiersza polecenia w [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview), który jest stale aktualizowany do najnowszej wersji. Aby otworzyć Cloud Shell, wybierz opcję **Wypróbuj** z góry dowolnego bloku kodu.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).


## <a name="create-a-linux-vm"></a>Tworzenie maszyny wirtualnej z systemem Linux

Utwórz nową grupę zasobów za pomocą polecenia [az group create](https://docs.microsoft.com/cli/azure/group) i utwórz maszynę wirtualną z systemem Linux za pomocą polecenia [az vm create](https://docs.microsoft.com/cli/azure/vm). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

W poniższym przykładzie użyto interfejsu wiersza polecenia platformy Azure, aby utworzyć grupę zasobów o nazwie *myResourceGroupMEAN* w lokalizacji *eastus*. Tworzona jest maszyna wirtualna o nazwie *myVM* z kluczami SSH, jeśli jeszcze nie istnieją w domyślnej lokalizacji kluczy. Aby użyć konkretnego zestawu kluczy, użyj opcji --ssh-key-value.

```azurecli-interactive
az group create --name myResourceGroupMEAN --location eastus
az vm create \
    --resource-group myResourceGroupMEAN \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password 'Azure12345678!' \
    --generate-ssh-keys
az vm open-port --port 3300 --resource-group myResourceGroupMEAN --name myVM
```

Po utworzeniu maszyny wirtualnej w interfejsie wiersza polecenia platformy Azure zostanie wyświetlona informacja podobna do następującej: 

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroupMEAN/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.72.77.9",
  "resourceGroup": "myResourceGroupMEAN"
}
```
Zwróć uwagę na element `publicIpAddress`. Ten adres jest używany na potrzeby uzyskiwania dostępu do maszyny wirtualnej.

Użyj następującego polecenia, aby utworzyć sesję SSH z maszyną wirtualną. Upewnij się, że używasz prawidłowego publicznego adresu IP. W powyższym przykładzie adres IP to 13.72.77.9.

```bash
ssh azureuser@13.72.77.9
```

## <a name="install-nodejs"></a>Instalowanie środowiska Node.js

[Node.js](https://nodejs.org/en/) to środowisko uruchomieniowe JavaScript oparte na aparacie JavaScript V8 przeglądarki Chrome. W tym samouczku środowisko Node.js jest używane do konfigurowania tras platformy Express i kontrolerów AngularJS.

Na maszynie wirtualnej, korzystając z powłoki bash otwartej przy użyciu protokołu SSH, zainstaluj środowisko Node.js.

```bash
sudo apt-get install -y nodejs
```

## <a name="install-mongodb-and-set-up-the-server"></a>Instalowanie bazy danych MongoDB i konfigurowanie serwera
[Baza danych MongoDB](https://www.mongodb.com) przechowuje dane w elastycznych dokumentach w notacji JSON. Pola w bazie danych mogą się różnić w zależności od dokumentu, a struktura danych może zmienić się z upływem czasu. W przypadku naszej przykładowej aplikacji dodajemy do bazy danych MongoDB rekordy książek zawierające nazwę, numer ISBN, autora i liczbę stron. 

1. Na maszynie wirtualnej, korzystając z powłoki bash otwartej przy użyciu protokołu SSH, ustaw klucz bazy danych MongoDB.

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
    echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
    ```

2. Zaktualizuj menedżera pakietów przy użyciu klucza.
  
    ```bash
    sudo apt-get update
    ```

3. Zainstaluj bazę danych MongoDB.

    ```bash
    sudo apt-get install -y mongodb
    ```

4. Uruchom serwer.

    ```bash
    sudo service mongodb start
    ```

5. Należy również zainstalować pakiet [analizatora treści](https://www.npmjs.com/package/body-parser-json), który pomoże nam przetworzyć dane JSON przekazywane w żądaniach do serwera.

    Zainstaluj menedżera pakietów npm.

    ```bash
    sudo apt-get install npm
    ```

    Zainstaluj pakiet analizatora treści.
    
    ```bash
    sudo npm install body-parser
    ```

6. Utwórz folder o nazwie *Books* i dodaj do niego plik o nazwie *server.js* zawierający konfigurację serwera internetowego.

    ```javascript
    var express = require('express');
    var bodyParser = require('body-parser');
    var app = express();
    app.use(express.static(__dirname + '/public'));
    app.use(bodyParser.json());
    require('./apps/routes')(app);
    app.set('port', 3300);
    app.listen(app.get('port'), function() {
        console.log('Server up: http://localhost:' + app.get('port'));
    });
    ```

## <a name="install-express-and-set-up-routes-to-the-server"></a>Instalowanie platformy Express i konfigurowanie tras do serwera

[Express](https://expressjs.com) to minimalna i elastyczna platforma aplikacji internetowych Node.js, która oferuje funkcje dla aplikacji internetowych i mobilnych. W tym samouczku platforma Express jest używana do przekazywania informacji o książkach do i z bazy danych MongoDB. Program [Mongoose](https://mongoosejs.com) zapewnia proste, oparte na schemacie rozwiązanie do modelowania danych aplikacji. W tym samouczku oprogramowanie Mongoose jest używane do zapewnienia schematu książki dla bazy danych.

1. Zainstaluj platformę Express i program Mongoose.

    ```bash
    sudo npm install express mongoose
    ```

2. W folderze *Books* utwórz folder o nazwie *apps* i dodaj plik o nazwie *routes.js* ze zdefiniowanymi trasami platformy Express.

    ```javascript
    var Book = require('./models/book');
    module.exports = function(app) {
      app.get('/book', function(req, res) {
        Book.find({}, function(err, result) {
          if ( err ) throw err;
          res.json(result);
        });
      }); 
      app.post('/book', function(req, res) {
        var book = new Book( {
          name:req.body.name,
          isbn:req.body.isbn,
          author:req.body.author,
          pages:req.body.pages
        });
        book.save(function(err, result) {
          if ( err ) throw err;
          res.json( {
            message:"Successfully added book",
            book:result
          });
        });
      });
      app.delete("/book/:isbn", function(req, res) {
        Book.findOneAndRemove(req.query, function(err, result) {
          if ( err ) throw err;
          res.json( {
            message: "Successfully deleted the book",
            book: result
          });
        });
      });
      var path = require('path');
      app.get('*', function(req, res) {
        res.sendfile(path.join(__dirname + '/public', 'index.html'));
      });
    };
    ```

3. W folderze *apps* utwórz folder o nazwie *models* i dodaj plik o nazwie *book.js* ze zdefiniowaną konfiguracją modelu książki.  

    ```javascript
    var mongoose = require('mongoose');
    var dbHost = 'mongodb://localhost:27017/test';
    mongoose.connect(dbHost);
    mongoose.connection;
    mongoose.set('debug', true);
    var bookSchema = mongoose.Schema( {
      name: String,
      isbn: {type: String, index: true},
      author: String,
      pages: Number
    });
    var Book = mongoose.model('Book', bookSchema);
    module.exports = mongoose.model('Book', bookSchema); 
    ```

## <a name="access-the-routes-with-angularjs"></a>Uzyskiwanie dostępu do tras przy użyciu AngularJS

[AngularJS](https://angularjs.org) udostępnia platformę internetową do tworzenia dynamicznych widoków w aplikacjach internetowych. W tym samouczku moduł AngularJS jest używany do połączenia naszej strony internetowej z platformą Express i wykonywania akcji na bazie danych książek.

1. Zmień katalog kopii zapasowej na *Books* (`cd ../..`), a następnie utwórz folder o nazwie *Public* i Dodaj plik o nazwie *Script. js* ze zdefiniowaną konfiguracją kontrolera.

    ```javascript
    var app = angular.module('myApp', []);
    app.controller('myCtrl', function($scope, $http) {
      $http( {
        method: 'GET',
        url: '/book'
      }).then(function successCallback(response) {
        $scope.books = response.data;
      }, function errorCallback(response) {
        console.log('Error: ' + response);
      });
      $scope.del_book = function(book) {
        $http( {
          method: 'DELETE',
          url: '/book/:isbn',
          params: {'isbn': book.isbn}
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
      $scope.add_book = function() {
        var body = '{ "name": "' + $scope.Name + 
        '", "isbn": "' + $scope.Isbn +
        '", "author": "' + $scope.Author + 
        '", "pages": "' + $scope.Pages + '" }';
        $http({
          method: 'POST',
          url: '/book',
          data: body
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
    });
    ```
    
2. W folderze *public* utwórz plik o nazwie *index.html* ze zdefiniowaną stroną internetową.

    ```html
    <!doctype html>
    <html ng-app="myApp" ng-controller="myCtrl">
      <head>
        <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
        <script src="script.js"></script>
      </head>
      <body>
        <div>
          <table>
            <tr>
              <td>Name:</td> 
              <td><input type="text" ng-model="Name"></td>
            </tr>
            <tr>
              <td>Isbn:</td>
              <td><input type="text" ng-model="Isbn"></td>
            </tr>
            <tr>
              <td>Author:</td> 
              <td><input type="text" ng-model="Author"></td>
            </tr>
            <tr>
              <td>Pages:</td>
              <td><input type="number" ng-model="Pages"></td>
            </tr>
          </table>
          <button ng-click="add_book()">Add</button>
        </div>
        <hr>
        <div>
          <table>
            <tr>
              <th>Name</th>
              <th>Isbn</th>
              <th>Author</th>
              <th>Pages</th>
            </tr>
            <tr ng-repeat="book in books">
              <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
              <td>{{book.name}}</td>
              <td>{{book.isbn}}</td>
              <td>{{book.author}}</td>
              <td>{{book.pages}}</td>
            </tr>
          </table>
        </div>
      </body>
    </html>
    ```

##  <a name="run-the-application"></a>Uruchamianie aplikacji

1. Zmień katalog kopii zapasowej na *Books* (`cd ..`) i uruchom serwer, uruchamiając następujące polecenie:

    ```bash
    nodejs server.js
    ```

2. Otwórz w przeglądarce internetowej adres zarejestrowany dla maszyny wirtualnej. Na przykład *http:\//13.72.77.9:3300*. Powinna zostać wyświetlona zawartość podobna do tej strony:

    ![Rekord książki](media/tutorial-mean/meanstack-init.png)

3. Wprowadź dane w polach tekstowych i kliknij pozycję **Dodaj**. Przykład:

    ![Dodawanie rekordu książki](media/tutorial-mean/meanstack-add.png)

4. Po odświeżeniu strony powinna zostać wyświetlona zawartość podobna do tej strony:

    ![Wyświetlanie listy rekordów książek](media/tutorial-mean/meanstack-list.png)

5. Możesz kliknąć przycisk **Usuń** i usunąć rekord książki z bazy danych.

## <a name="next-steps"></a>Następne kroki

W ramach tego samouczka utworzono aplikację internetową, która śledzi rekordy książek przy użyciu stosu MEAN na maszynie wirtualnej z systemem Linux. W tym samouczku omówiono:

> [!div class="checklist"]
> * Tworzenie maszyny wirtualnej z systemem Linux
> * Instalowanie środowiska Node.js
> * Instalowanie bazy danych MongoDB i konfigurowanie serwera
> * Instalowanie platformy Express i konfigurowanie tras do serwera
> * Uzyskiwanie dostępu do tras przy użyciu AngularJS
> * Uruchamianie aplikacji

Przejdź do następnego samouczka, aby dowiedzieć się, jak zabezpieczyć serwery sieci Web przy użyciu certyfikatów TLS.

> [!div class="nextstepaction"]
> [Zabezpieczanie serwera sieci Web za pomocą protokołu TLS](tutorial-secure-web-server.md)
