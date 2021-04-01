---
title: 'Samouczek: aplikacja Java do ładowania przykładowych danych do tabeli interfejs API Cassandra w Azure Cosmos DB'
description: W tym samouczku pokazano, jak załadować przykładowe dane użytkownika do tabeli na koncie interfejsu API Cassandra w usłudze Azure Cosmos DB przy użyciu aplikacji języka Java.
author: kanshiG
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: ad3dfc8c86d22e58377ebfbdb94b2beb004ded56
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93092470"
---
# <a name="tutorial-load-sample-data-into-a-cassandra-api-table-in-azure-cosmos-db"></a>Samouczek: ładowanie przykładowych danych do tabeli interfejsu API Cassandra w usłudze Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Jako deweloper być może masz aplikacje, które używają par klucz-wartość. Możesz przechowywać dane kluczy/wartości i zarządzać nimi przy użyciu konta interfejsu API Cassandra w usłudze Azure Cosmos DB. W tym samouczku pokazano, jak załadować przykładowe dane użytkownika do tabeli na koncie interfejsu API Cassandra w usłudze Azure Cosmos DB przy użyciu aplikacji języka Java. Aplikacja języka Java używa [sterownika języka Java](https://github.com/datastax/java-driver) i ładuje dane użytkownika, takie jak identyfikator, nazwa i miasto użytkownika. 

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Ładowanie danych do tabeli Cassandra
> * Uruchamianie aplikacji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

* Ten artykuł należy do wieloczęściowego samouczka. Przed rozpoczęciem pracy z tym dokumentem upewnij się, że [utworzono konto interfejs API Cassandra, przestrzeń kluczy i tabelę](create-cassandra-api-account-java.md).   

## <a name="load-data-into-the-table"></a>Ładowanie danych do tabel

Wykonaj poniższe czynności, aby załadować dane do tabeli interfejsu API Cassandra:

1. Otwórz plik „UserRepository.java” w folderze „src\main\java\com\azure\cosmosdb\cassandra” i dołącz kod wstawiający pola user_id, user_name i user_bcity do tabeli:

   ```java
   /**
   * Insert a row into user table
   *
   * @param id   user_id
   * @param name user_name
   * @param city user_bcity
   */
   public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
   }

   /**
   * Create a PrepareStatement to insert a row to user table
   *
   * @return PreparedStatement
   */
   public PreparedStatement prepareInsertStatement() {
      final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
   return session.prepare(insertStatement);
   }
   ```
 
2. Otwórz plik „UserProfile.java” w folderze „src\main\java\com\azure\cosmosdb\cassandra”. Ta klasa zawiera metodę main wywołującą metody createKeyspace i createTable zdefiniowane wcześniej. Teraz dodaj następujący kod, aby wstawić przykładowe dane do tabeli interfejsu API Cassandra.

   ```java
   //Insert rows into user table
   PreparedStatement preparedStatement = repository.prepareInsertStatement();
     repository.insertUser(preparedStatement, 1, "JohnH", "Seattle");
     repository.insertUser(preparedStatement, 2, "EricK", "Spokane");
     repository.insertUser(preparedStatement, 3, "MatthewP", "Tacoma");
     repository.insertUser(preparedStatement, 4, "DavidA", "Renton");
     repository.insertUser(preparedStatement, 5, "PeterS", "Everett");
   ```

## <a name="run-the-app"></a>Uruchamianie aplikacji

Otwórz wiersz polecenia lub okno terminalu i zmień ścieżkę na folder, w którym utworzono projekt. Uruchom polecenie „mvn clean install”, aby wygenerować plik cosmosdb-cassandra-examples.jar w folderze docelowym, a następnie uruchom aplikację. 

```bash
cd "cassandra-demo"

mvn clean install

java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
```

Teraz możesz otworzyć Eksploratora danych w witrynie Azure Portal, aby upewnić się, że informacje użytkownika zostały dodane do tabeli.
    
## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób ładowania przykładowych danych do konta interfejsu API Cassandra w usłudze Azure Cosmos DB. Teraz możesz przejść do następnego artykułu:

> [!div class="nextstepaction"]
> [Odpytywanie danych z konta interfejsu API Cassandra](cassandra-api-query-data.md)
