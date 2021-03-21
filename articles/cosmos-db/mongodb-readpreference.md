---
title: Użyj preferencji Odczytaj z interfejsem API Azure Cosmos DB dla MongoDB
description: Dowiedz się, jak używać preferencji odczytu MongoDB z interfejsem API Azure Cosmos DB dla MongoDB
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 02/26/2019
ms.custom: devx-track-js
ms.openlocfilehash: 3c78ad6605e927015d35df12cadf0347dd0337cf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96349048"
---
# <a name="how-to-globally-distribute-reads-using-azure-cosmos-dbs-api-for-mongodb"></a>Jak globalnie dystrybuować odczyty przy użyciu interfejsu API Azure Cosmos DB dla MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

W tym artykule pokazano, jak globalnie dystrybuować operacje odczytu z ustawieniami [preferencji odczytu MongoDB](https://docs.mongodb.com/manual/core/read-preference/) przy użyciu interfejsu API Azure Cosmos DB dla MongoDB.

## <a name="prerequisites"></a>Wymagania wstępne 
Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Zapoznaj się z tym artykułem [szybkiego startu](tutorial-global-distribution-mongodb.md) , aby uzyskać instrukcje dotyczące korzystania z Azure Portal, aby skonfigurować konto Cosmos z dystrybucją globalną, a następnie nawiązać z nim połączenie.

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Otwórz okno terminalu usługi Git, na przykład git bash, i za pomocą polecenia `cd` przejdź do katalogu roboczego.  

Uruchom następujące polecenia w celu sklonowania przykładowego repozytorium. W zależności od używanej platformy należy użyć jednego z następujących przykładowych repozytoriów:

1. [Przykładowa aplikacja .NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [Przykładowa aplikacja NodeJS]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Przykładowa aplikacja Mongoose](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Przykładowa aplikacja Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [Przykładowa aplikacja SpringBoot](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

W zależności od używanej platformy Zainstaluj wymagane pakiety i uruchom aplikację. Aby zainstalować zależności, należy skorzystać z pliku Readme zawartego w przykładowym repozytorium aplikacji. Na przykład w aplikacji przykładowej NodeJS Użyj następujących poleceń, aby zainstalować wymagane pakiety i uruchomić aplikację.

```bash
cd mean
npm install
node index.js
```
Aplikacja próbuje nawiązać połączenie ze źródłem MongoDB i nie powiedzie się, ponieważ parametry połączenia są nieprawidłowe. Postępuj zgodnie z instrukcjami w pliku Readme, aby zaktualizować parametry połączenia `url` . Zaktualizuj również `readFromRegion` region do odczytu na koncie Cosmos. Następujące instrukcje pochodzą z przykładu NodeJS:

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos account's values. 
```

Po wykonaniu tych kroków Przykładowa aplikacja zostanie uruchomiona i wygeneruje następujące dane wyjściowe:

```
connected!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromSecondaryfunc query completed!
```

## <a name="read-using-read-preference-mode"></a>Przeczytaj przy użyciu trybu preferencji odczytu

Protokół MongoDB udostępnia następujące tryby preferencji odczytu, które mają być używane przez klientów:

1. GŁÓWNYM
2. PRIMARY_PREFERRED
3. DODATKOWYCH
4. SECONDARY_PREFERRED
5. ZNAJDUJĄC

Szczegółowe informacje o zachowaniu każdego z tych trybów preferencji odczytu można znaleźć w dokumentacji dotyczącej [zachowania preferencji szczegółowe MongoDB](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior) . W Cosmos DB, podstawowe mapowania do zapisu region i dodatkowe mapy w regionie ODCZYTYWANia.

W oparciu o typowe scenariusze zalecamy użycie następujących ustawień:

1. Jeśli wymagane są **odczyty o małym opóźnieniu** , użyj **najbliższego** trybu preferencji odczyt. To ustawienie powoduje kierowanie operacji odczytu do najbliższego dostępnego regionu. Należy pamiętać, że jeśli najbliższy region jest regionem zapisu, te operacje są kierowane do tego regionu.
2. Jeśli wymagana jest **wysoka dostępność i rozkład geograficzny operacji odczytu** (opóźnienie nie jest ograniczeniem), użyj **podstawowego preferowanego** lub **pomocniczego preferowanego** trybu preferencji odczytu. To ustawienie umożliwia kierowanie odpowiednio operacji odczytu do dostępnego regionu zapisu lub odczytu. Jeśli region nie jest dostępny, żądania są kierowane do następnego dostępnego regionu zgodnie z zachowaniem preferencji odczyt.

Poniższy fragment kodu z przykładowej aplikacji pokazuje, jak skonfigurować najbliższy priorytet odczytu w NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

Podobnie Poniższy fragment kodu przedstawia sposób konfigurowania preferencji odczytu SECONDARY_PREFERRED w NodeJS:

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

Preferencję odczytu można także ustawić, przekazując `readPreference` jako parametr w opcjach identyfikatora URI parametrów połączenia:

```javascript
const MongoClient = require('mongodb').MongoClient;
const assert = require('assert');

// Connection URL
const url = 'mongodb://localhost:27017?ssl=true&replicaSet=globaldb&readPreference=nearest';

// Database Name
const dbName = 'myproject';

// Use connect method to connect to the Server
MongoClient.connect(url, function(err, client) {
  console.log("Connected correctly to server");

  const db = client.db(dbName);

  client.close();
});
```

Zapoznaj się z odpowiednimi przykładowymi repozytoriami aplikacji dla innych platform, takich jak [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) i [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

## <a name="read-using-tags"></a>Przeczytaj przy użyciu tagów

Oprócz trybu preferencji odczyt protokół MongoDB umożliwia używanie tagów do bezpośredniej operacji odczytu. W interfejsie API Cosmos DB dla MongoDB `region` tag jest domyślnie uwzględniany jako część `isMaster` odpowiedzi:

```json
"tags": {
         "region": "West US"
      }
```

W związku z tym MongoClient może użyć `region` znacznika wraz z nazwą regionu, aby skierować operacje odczytu do określonych regionów. W przypadku kont Cosmos nazwy regionów można znaleźć w Azure Portal po lewej stronie obszarze **ustawienia — >dane repliki globalnie**. To ustawienie jest przydatne w celu uzyskania **izolacji odczytu** w przypadku, gdy aplikacja kliencka ma kierować operacje odczytu tylko do określonego regionu. To ustawienie jest idealne dla scenariuszy typu non-produkcja/Analytics, które są uruchamiane w tle i nie są krytycznymi usługami produkcyjnymi.

Poniższy fragment kodu z przykładowej aplikacji pokazuje, jak skonfigurować preferencję odczytu przy użyciu tagów w NodeJS:

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

Zapoznaj się z odpowiednimi przykładowymi repozytoriami aplikacji dla innych platform, takich jak [.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference) i [Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference).

W tym artykule wyjaśniono, jak globalnie dystrybuować operacje odczytu przy użyciu preferencji odczytu z interfejsem API Azure Cosmos DB dla MongoDB.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń wszystkie zasoby utworzone w tym artykule w Azure Portal, wykonując następujące czynności:

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal kliknij pozycję **Grupy zasobów**, a następnie kliknij nazwę utworzonego zasobu. 
2. Na stronie grupy zasobów kliknij pozycję **Usuń**, wpisz w polu tekstowym nazwę zasobu do usunięcia, a następnie kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

* [Importowanie danych z bazy danych MongoDB do usługi Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
* [Skonfiguruj globalnie rozproszoną bazę danych z interfejsem API Azure Cosmos DB dla MongoDB](tutorial-global-distribution-mongodb.md)
* [Opracowywanie lokalnie za pomocą emulatora Azure Cosmos DB](local-emulator.md)