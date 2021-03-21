---
title: Aplikacja Java z Azure Cosmos DB interfejs API Cassandra przy użyciu zestawu Java 3,0 SDK
description: W tym przewodniku szybki start pokazano, jak za pomocą interfejs API Cassandra Azure Cosmos DB utworzyć aplikację profilu za pomocą zestawu SDK Azure Portal i Java 3,0.
ms.service: cosmos-db
author: TheovanKraay
ms.author: thvankra
ms.subservice: cosmosdb-cassandra
ms.devlang: java
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: df7f857edc4c1296b6a6e1fae13d70aa4f178728
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93099777"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-cassandra-api-data-v3-driver"></a>Szybki Start: Tworzenie aplikacji Java do zarządzania danymi interfejs API Cassandra Azure Cosmos DB (sterownik v3)
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java v3](create-cassandra-java.md)
> * [Java v4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

W tym przewodniku szybki start utworzysz konto Azure Cosmos DB interfejs API Cassandra i używasz aplikacji Java Cassandra sklonowanej z usługi GitHub w celu utworzenia bazy danych Cassandra i kontenera przy użyciu [sterowników v3. x Apache Cassandra](https://github.com/datastax/java-driver/tree/3.x) dla języka Java. Azure Cosmos DB to wielomodelowa usługa bazy danych, która pozwala szybko tworzyć i wysyłać zapytania dotyczące dokumentów, tabel, kluczy i wartościowych baz danych przy użyciu dystrybucji globalnej i możliwości skalowania w poziomie.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Lub [Wypróbuj bezpłatnie Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure.
- [Zestaw Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Wskaż `JAVA_HOME` zmienną środowiskową do folderu, w którym zainstalowano JDK.
- [Archiwum binarne Maven](https://maven.apache.org/download.cgi). W systemie Ubuntu Uruchom polecenie, `apt-get install maven` Aby zainstalować Maven.
- Usługi [git](https://www.git-scm.com/downloads). W systemie Ubuntu Uruchom polecenie, `sudo apt-get install git` Aby zainstalować usługę git.

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

Przed utworzeniem bazy danych dokumentów musisz utworzyć konto bazy danych Cassandra z użyciem usługi Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz przejdźmy do pracy z kodem. Sklonujemy aplikację bazy danych Cassandra z serwisu GitHub, ustawimy parametry połączenia i uruchomimy ją. Zobaczysz, jak łatwo jest pracować programowo z danymi. 

1. Otwórz wiersz polecenia. Utwórz nowy folder o nazwie `git-samples`. Następnie zamknij wiersz polecenia.

    ```bash
    md "C:\git-samples"
    ```

2. Otwórz okno terminala usługi Git, np. git bash, i użyj polecenia `cd`, aby przejść do nowego folderu instalacji aplikacji przykładowej.

    ```bash
    cd "C:\git-samples"
    ```

3. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-java-getting-started.git
    ```

## <a name="review-the-code"></a>Przeglądanie kodu

Ta czynność jest opcjonalna. Jeśli chcesz dowiedzieć się, jak kod tworzy zasoby bazy danych, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz od razu przejść do sekcji [Aktualizacja parametrów połączenia](#update-your-connection-string). Wszystkie te fragmenty kodu pochodzą z pliku *src/Main/Java/com/Azure/cosmosdb/Cassandra/util/CassandraUtils. Java* .  

* Są ustawione opcje host, port, nazwa użytkownika, hasło i protokół TLS/SSL. Informacje o parametrach połączenia pochodzą ze strony parametrów połączenia w witrynie Azure Portal.

   ```java
   cluster = Cluster.builder().addContactPoint(cassandraHost).withPort(cassandraPort).withCredentials(cassandraUsername, cassandraPassword).withSSL(sslOptions).build();
   ```

* Obiekt `cluster` łączy się z interfejsem API bazy danych Cassandra w usłudze Azure Cosmos DB i zwraca sesję umożliwiającą uzyskanie dostępu.

    ```java
    return cluster.connect();
    ```

Poniższe fragmenty kodu pochodzą z pliku *src/Main/Java/com/Azure/cosmosdb/Cassandra/Repository/UserRepository. Java* .

* Utwórz nową przestrzeń kluczy.

    ```java
    public void createKeyspace() {
        final String query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {'class': 'SimpleStrategy', 'replication_factor': '3' } ";
        session.execute(query);
        LOGGER.info("Created keyspace 'uprofile'");
    }
    ```

* Utwórz nową tabelę.

   ```java
   public void createTable() {
        final String query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        session.execute(query);
        LOGGER.info("Created table 'user'");
   }
   ```

* Wstaw jednostki użytkowników przy użyciu obiektu przygotowanej instrukcji.

    ```java
    public PreparedStatement prepareInsertStatement() {
        final String insertStatement = "INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (?,?,?)";
        return session.prepare(insertStatement);
    }

    public void insertUser(PreparedStatement statement, int id, String name, String city) {
        BoundStatement boundStatement = new BoundStatement(statement);
        session.execute(boundStatement.bind(id, name, city));
    }
    ```

* Wyślij zapytanie w celu pobrania informacji o wszystkich użytkownikach.

    ```java
   public void selectAllUsers() {
        final String query = "SELECT * FROM uprofile.user";
        List<Row> rows = session.execute(query).all();

        for (Row row : rows) {
            LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
        }
    }
    ```

* Wyślij zapytanie w celu pobrania informacji o jednym użytkowniku.

    ```java
    public void selectUser(int id) {
        final String query = "SELECT * FROM uprofile.user where user_id = 3";
        Row row = session.execute(query).one();

        LOGGER.info("Obtained row: {} | {} | {} ", row.getInt("user_id"), row.getString("user_name"), row.getString("user_bcity"));
    }
    ```

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i skopiować je do aplikacji. Szczegóły parametrów połączenia umożliwiają aplikacji komunikowanie się z hostowaną bazą danych.

1. Na koncie Azure Cosmos DB w [Azure Portal](https://portal.azure.com/)wybierz pozycję **Parametry połączenia**. 

    :::image type="content" source="./media/create-cassandra-java/copy-username-connection-string-azure-portal.png" alt-text="Wyświetlanie i kopiowanie nazwy użytkownika z witryny Portal Azure, strona Parametry połączenia":::

2. Użyj :::image type="icon" source="./media/create-cassandra-java/copy-button-azure-portal.png"::: przycisku po prawej stronie ekranu, aby skopiować wartość punkt kontaktowy. 

3. Otwórz plik *config. Properties* z folderu *C:\git-samples\azure-cosmosdb-Cassandra-Java-getting-started\java-examples\src\main\resources* . 

3. Wklej wartość PUNKT KONTAKTOWY z portalu do lokalizacji `<Cassandra endpoint host>` w wierszu 2.

    Wiersz 2 *pliku config. Properties* powinien teraz wyglądać podobnie do 

    `cassandra_host=cosmos-db-quickstart.cassandra.cosmosdb.azure.com`

3. Wróć do portalu i skopiuj wartość Nazwa użytkownika. Wklej wartość NAZWA UŻYTKOWNIKA z portalu do lokalizacji `<cassandra endpoint username>` w wierszu 4.

    Wiersz 4 *pliku config. Properties* powinien teraz wyglądać podobnie do 

    `cassandra_username=cosmos-db-quickstart`

4. Wróć do portalu i skopiuj wartość hasła. Wklej wartość HASŁO z portalu do lokalizacji `<cassandra endpoint password>` w wierszu 5.

    Wiersz 5 *pliku config. Properties* powinien teraz wyglądać podobnie do 

    `cassandra_password=2Ggkr662ifxz2Mg...==`

5. Jeśli chcesz użyć określonego certyfikatu TLS/SSL w wierszu 6, Zastąp wartość `<SSL key store file location>` lokalizacją certyfikatu TLS/SSL. Jeśli wartość nie zostanie podana, będzie używany certyfikat JDK zainstalowany w lokalizacji <JAVA_HOME>/jre/lib/security/cacerts. 

6. Jeśli zmieniono wiersz 6 w taki sposób, aby używał określonego certyfikatu TLS/SSL, zaktualizuj wiersz 7, aby użyć hasła dla tego certyfikatu. 

7. Zapisz plik *config. Properties* .

## <a name="run-the-java-app"></a>Uruchamianie aplikację języka Java

1. W oknie terminalu usługi git wpisz polecenie `cd` i przejdź do folderu `azure-cosmosdb-cassandra-java-getting-started`.

    ```git
    cd "C:\git-samples\azure-cosmosdb-cassandra-java-getting-started"
    ```

2. W oknie terminalu usługi Git użyj poniższego polecenia, aby wygenerować plik `cosmosdb-cassandra-examples.jar`.

    ```git
    mvn clean install
    ```

3. W oknie terminalu usługi Git uruchom następujące polecenie, aby uruchomić aplikację języka Java.

    ```git
    java -cp target/cosmosdb-cassandra-examples.jar com.azure.cosmosdb.cassandra.examples.UserProfile
    ```

    W oknie terminalu zostaną wyświetlone powiadomienia o utworzeniu przestrzeni kluczy i tabeli. Następnie zostaną wybrani i zwróceni wszyscy użytkownicy w tabeli oraz pojawią się dane wyjściowe, po czym zostanie wybrany wiersz według identyfikatora i pojawi się wartość.  

    Naciśnij klawisze CTRL + C, aby zatrzymać wykonywanie programu i zamknąć okno konsoli.

4. W witrynie Azure Portal otwórz **Eksploratora danych**, aby wykonywać zapytania oraz modyfikować te nowe dane i pracować z nimi. 

    :::image type="content" source="./media/create-cassandra-java/view-data-explorer-java-app.png" alt-text="Wyświetlanie danych w Eksplorator danych — Azure Cosmos DB":::

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia konta Azure Cosmos DB przy użyciu interfejs API Cassandra i uruchamiania aplikacji Cassandra Java, która tworzy Cassandra bazę danych i kontener. Teraz możesz zaimportować dodatkowe dane do konta Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importowanie danych bazy danych Cassandra do usługi Azure Cosmos DB](cassandra-import-data.md)
