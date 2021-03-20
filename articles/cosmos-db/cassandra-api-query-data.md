---
title: 'Samouczek: wykonywanie zapytań względem danych z poziomu konta interfejsu API Cassandra w usłudze Azure Cosmos DB'
description: W tym samouczku przedstawiono sposób wykonywania zapytań względem danych użytkownika z poziomu konta interfejsu API Cassandra w usłudze Azure Cosmos DB przy użyciu aplikacji języka Java.
ms.service: cosmos-db
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.subservice: cosmosdb-cassandra
ms.topic: tutorial
ms.date: 09/24/2018
ms.openlocfilehash: 693984ceaef1a71ebfbe4122c18a45a563c9c101
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93100443"
---
# <a name="tutorial-query-data-from-a-cassandra-api-account-in-azure-cosmos-db"></a>Samouczek: wykonywanie zapytań względem danych z poziomu konta interfejsu API Cassandra w usłudze Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Jako deweloper być może masz aplikacje, które używają par klucz-wartość. Możesz przechowywać dane kluczy/wartości i tworzyć zapytania względem nich przy użyciu konta interfejsu API Cassandra w usłudze Azure Cosmos DB. W tym samouczku przedstawiono sposób wykonywania zapytań względem danych użytkownika z poziomu konta interfejsu API Cassandra w usłudze Azure Cosmos DB przy użyciu aplikacji języka Java. Aplikacja języka Java używa [sterownika Java](https://github.com/datastax/java-driver) i wykonuje zapytania względem danych użytkownika, takie jak identyfikator użytkownika, nazwa użytkownika i miasto użytkownika. 

Ten samouczek obejmuje następujące zadania:

> [!div class="checklist"]
> * Wykonywanie zapytań względem danych z tabeli bazy danych Cassandra
> * Uruchamianie aplikacji

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Wymagania wstępne

* Ten artykuł należy do wieloczęściowego samouczka. Przed rozpoczęciem upewnij się, że zostały wykonane poprzednie kroki w celu utworzenia konta interfejs API Cassandra, miejsca, tabeli i [załadowania przykładowych danych do tabeli](cassandra-api-load-data.md). 

## <a name="query-data"></a>Zapytania o dane

Przeprowadź poniższe czynności, aby wykonać zapytania względem danych z poziomu konta interfejsu API Cassandra:

1. Otwórz plik `UserRepository.java` w folderze `src\main\java\com\azure\cosmosdb\cassandra`. Dodaj poniższy blok kodu. Ten kod umożliwia zastosowanie trzech metod: 

   * Wykonywanie zapytań względem wszystkich użytkowników w bazie danych
   * Wykonywanie zapytań względem określonego użytkownika przefiltrowanego według identyfikatora
   * Usuwanie tabeli

   ```java
   /**
   * Select all rows from user table
   */
   public void selectAllUsers() {

     final String query = "SELECT * FROM uprofile.user";
     List<Row> rows = session.execute(query).all();

     for (Row row : rows) {
        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
     }
   }

   /**
   * Select a row from user table
   *
   * @param id user_id
   */
   public void selectUser(int id) {
      final String query = "SELECT * FROM uprofile.user where user_id = 3";
      Row row = session.execute(query).one();

      LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
   }

   /**
   * Delete user table.
   */
   public void deleteTable() {
     final String query = "DROP TABLE IF EXISTS uprofile.user";
     session.execute(query);
   }
   ```

2. Otwórz plik `UserProfile.java` w folderze `src\main\java\com\azure\cosmosdb\cassandra`. Ta klasa zawiera metodę główną, która wywołuje zdefiniowane wcześniej metody wstawiania danych createKeyspace i createTable. Teraz dodaj poniższy kod, który wykonuje zapytanie dotyczące wszystkich użytkowników lub określonego użytkownika:

   ```java
   LOGGER.info("Select all users");
   repository.selectAllUsers();

   LOGGER.info("Select a user by id (3)");
   repository.selectUser(3);

   LOGGER.info("Delete the users profile table");
   repository.deleteTable();
   ```

## <a name="run-the-java-app"></a>Uruchamianie aplikację języka Java
1. Otwórz wiersz polecenia lub okno terminala. Wklej poniższy blok kodu. 

   Ten kod zmienia katalog (polecenie cd) na ścieżkę folderu, w której utworzono projekt. Następnie jest uruchamiane polecenie `mvn clean install` w celu wygenerowania pliku `cosmosdb-cassandra-examples.jar` w folderze docelowym. Na koniec kod uruchamia aplikację języka Java.

   ```bash
   cd "cassandra-demo"
   
   mvn clean install
   
   java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
   ```

2. Teraz w witrynie Azure Portal otwórz **Eksplorator danych** i upewnij się, że tabela użytkowników została usunięta.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Możesz usunąć grupę zasobów, konto usługi Azure Cosmos oraz wszystkie powiązane zasoby, gdy nie będą już potrzebne. Aby to zrobić, wybierz grupę zasobów maszyny wirtualnej, wybierz pozycję **Usuń**, a następnie potwierdź nazwę grupy zasobów, którą chcesz usunąć.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób wykonywania zapytań o dane z poziomu konta interfejsu API Cassandra usługi Azure Cosmos DB. Teraz możesz przejść do następnego artykułu:

> [!div class="nextstepaction"]
> [Migrowanie danych do konta interfejsu API Cassandra](cassandra-import-data.md)


