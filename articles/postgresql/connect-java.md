---
title: 'Szybki Start: używanie języka Java i JDBC z Azure Database for PostgreSQL'
description: W tym przewodniku szybki start dowiesz się, jak używać języka Java i JDBC z Azure Database for PostgreSQL.
author: jdubois
ms.author: judubois
ms.service: postgresql
ms.custom: mvc, devcenter, devx-track-azurecli
ms.topic: quickstart
ms.devlang: java
ms.date: 08/17/2020
ms.openlocfilehash: 42547338c0f5f2f3105833b12e499d40b6209b05
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96184709"
---
# <a name="quickstart-use-java-and-jdbc-with-azure-database-for-postgresql"></a>Szybki Start: używanie języka Java i JDBC z Azure Database for PostgreSQL

W tym temacie przedstawiono Tworzenie przykładowej aplikacji, która używa języka Java i [JDBC](https://en.wikipedia.org/wiki/Java_Database_Connectivity) do przechowywania i pobierania informacji w [Azure Database for PostgreSQL](./index.yml).

JDBC jest standardowym interfejsem API języka Java do łączenia z tradycyjnymi relacyjnymi bazami danych.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Jeśli go nie masz, [Uzyskaj bezpłatną wersję próbną](https://azure.microsoft.com/free/).
- [Azure Cloud Shell](../cloud-shell/quickstart.md) lub [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Zalecamy Azure Cloud Shell, aby zalogować się automatycznie i mieć dostęp do wszystkich potrzebnych narzędzi.
- Obsługiwany [zestaw Java Development Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), wersja 8 (uwzględniony w Azure Cloud Shell).
- Narzędzie kompilacji [Apache Maven](https://maven.apache.org/) .

## <a name="prepare-the-working-environment"></a>Przygotowywanie środowiska roboczego

Zamierzamy używać zmiennych środowiskowych, aby ograniczyć liczbę błędów i ułatwić dostosowanie poniższej konfiguracji do konkretnych potrzeb.

Skonfiguruj te zmienne środowiskowe za pomocą następujących poleceń:

```bash
AZ_RESOURCE_GROUP=database-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_POSTGRESQL_USERNAME=demo
AZ_POSTGRESQL_PASSWORD=<YOUR_POSTGRESQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

Zastąp symbole zastępcze następującymi wartościami, które są używane w tym artykule:

- `<YOUR_DATABASE_NAME>`: Nazwa serwera PostgreSQL. Powinna być ona unikatowa w obrębie platformy Azure.
- `<YOUR_AZURE_REGION>`: Region świadczenia usługi Azure, który będzie używany. Możesz domyślnie zastosować region `eastus`, ale zalecamy skonfigurowanie regionu bliżej Twojego miejsca zamieszkania. Aby uzyskać pełną listę dostępnych regionów, wprowadź `az account list-locations` .
- `<YOUR_POSTGRESQL_PASSWORD>`: Hasło serwera bazy danych PostgreSQL. To hasło powinno zawierać co najmniej osiem znaków. Znaki muszą pochodzić z trzech spośród następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry (0–9) i znaki inne niż alfanumeryczne (!, $, #,% itd.).
- `<YOUR_LOCAL_IP_ADDRESS>`: Adres IP komputera lokalnego, z którego będzie uruchamiana aplikacja Java. Jednym z wygody jest wskazanie, że Twoja przeglądarka ma być [whatismyip.Akamai.com](http://whatismyip.akamai.com/).

Następnie utwórz grupę zasobów za pomocą następującego polecenia:

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
  	| jq
```

> [!NOTE]
> Używamy `jq` Narzędzia do wyświetlania danych JSON i zwiększania czytelności. To narzędzie jest instalowane domyślnie na [Azure Cloud Shell](https://shell.azure.com/). Jeśli nie lubisz tego narzędzia, możesz bezpiecznie usunąć część `| jq` ze wszystkich poleceń.

## <a name="create-an-azure-database-for-postgresql-instance"></a>Tworzenie wystąpienia Azure Database for PostgreSQL

Pierwszym krokiem jest utworzenie zarządzanego serwera PostgreSQL.

> [!NOTE]
> Więcej szczegółowych informacji na temat tworzenia serwerów PostgreSQL można znaleźć w temacie [Tworzenie serwera Azure Database for PostgreSQL przy użyciu Azure Portal](./quickstart-create-server-database-portal.md).

W [Azure Cloud Shell](https://shell.azure.com/)Uruchom następujące polecenie:

```azurecli
az postgres server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --sku-name B_Gen5_1 \
    --storage-size 5120 \
    --admin-user $AZ_POSTGRESQL_USERNAME \
    --admin-password $AZ_POSTGRESQL_PASSWORD \
  	| jq
```

To polecenie tworzy mały serwer PostgreSQL.

### <a name="configure-a-firewall-rule-for-your-postgresql-server"></a>Konfigurowanie reguły zapory dla serwera PostgreSQL

Wystąpienia Azure Database for PostgreSQL są zabezpieczone domyślnie. Ma ona zaporę, która nie zezwala na żadne połączenie przychodzące. Aby można było korzystać z bazy danych programu, należy dodać regułę zapory zezwalającą na dostęp lokalnego adresu IP do serwera bazy danych.

Ponieważ lokalny adres IP został skonfigurowany na początku tego artykułu, możesz otworzyć zaporę serwera, uruchamiając następujące polecenie:

```azurecli
az postgres server firewall-rule create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME-database-allow-local-ip \
    --server $AZ_DATABASE_NAME \
    --start-ip-address $AZ_LOCAL_IP_ADDRESS \
    --end-ip-address $AZ_LOCAL_IP_ADDRESS \
  	| jq
```

### <a name="configure-a-postgresql-database"></a>Konfigurowanie bazy danych PostgreSQL

Utworzony wcześniej serwer PostgreSQL jest pusty. Nie ma żadnej bazy danych, której można używać z aplikacją Java. Utwórz nową bazę danych o nazwie przy `demo` użyciu następującego polecenia:

```azurecli
az postgres db create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name demo \
    --server-name $AZ_DATABASE_NAME \
  	| jq
```

### <a name="create-a-new-java-project"></a>Utwórz nowy projekt Java

Korzystając z ulubionego środowiska IDE, Utwórz nowy projekt Java i Dodaj `pom.xml` plik w jego katalogu głównym:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>com.example</groupId>
    <artifactId>demo</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>demo</name>

    <properties>
        <java.version>1.8</java.version>
        <maven.compiler.source>1.8</maven.compiler.source>
        <maven.compiler.target>1.8</maven.compiler.target>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.postgresql</groupId>
            <artifactId>postgresql</artifactId>
            <version>42.2.12</version>
        </dependency>
    </dependencies>
</project>
```

Ten plik to [Apache Maven](https://maven.apache.org/) , który konfiguruje nasz projekt do użycia:

- Java 8
- Ostatni sterownik PostgreSQL dla języka Java

### <a name="prepare-a-configuration-file-to-connect-to-azure-database-for-postgresql"></a>Przygotuj plik konfiguracji, aby połączyć się z Azure Database for PostgreSQL

Utwórz plik *src/Main/sources/Application. Properties* i Dodaj:

```properties
url=jdbc:postgresql://$AZ_DATABASE_NAME.postgres.database.azure.com:5432/demo?ssl=true&sslmode=require
user=demo@$AZ_DATABASE_NAME
password=$AZ_POSTGRESQL_PASSWORD
```

- Zastąp dwie `$AZ_DATABASE_NAME` zmienne wartością skonfigurowaną na początku tego artykułu.
- Zastąp `$AZ_POSTGRESQL_PASSWORD` zmienną wartością skonfigurowaną na początku tego artykułu.

> [!NOTE]
> Dołączymy `?ssl=true&sslmode=require` do właściwości konfiguracji `url` , aby poinformować sterownik JDBC o użyciu protokołu TLS ([Transport Layer Security](https://en.wikipedia.org/wiki/Transport_Layer_Security)) podczas nawiązywania połączenia z bazą danych. Jest wymagane użycie protokołu TLS z Azure Database for PostgreSQL i jest dobrym rozwiązaniem w zakresie zabezpieczeń.

### <a name="create-an-sql-file-to-generate-the-database-schema"></a>Utwórz plik SQL, aby wygenerować schemat bazy danych

Użyjemy *src/Main/sources `schema.sql`* , aby utworzyć schemat bazy danych. Utwórz ten plik z następującą zawartością:

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id SERIAL PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BOOLEAN);
```

## <a name="code-the-application"></a>Kodowanie aplikacji

### <a name="connect-to-the-database"></a>Łączenie z bazą danych

Następnie Dodaj kod języka Java, który będzie używać JDBC do przechowywania i pobierania danych z serwera PostgreSQL.

Utwórz plik *src/Main/Java/DemoApplication. Java* zawierający następujące polecenie:

```java
package com.example.demo;

import java.sql.*;
import java.util.*;
import java.util.logging.Logger;

public class DemoApplication {

    private static final Logger log;

    static {
        System.setProperty("java.util.logging.SimpleFormatter.format", "[%4$-7s] %5$s %n");
        log =Logger.getLogger(DemoApplication.class.getName());
    }

    public static void main(String[] args) throws Exception {
        log.info("Loading application properties");
        Properties properties = new Properties();
        properties.load(DemoApplication.class.getClassLoader().getResourceAsStream("application.properties"));

        log.info("Connecting to the database");
        Connection connection = DriverManager.getConnection(properties.getProperty("url"), properties);
        log.info("Database connection test: " + connection.getCatalog());

        log.info("Create database schema");
        Scanner scanner = new Scanner(DemoApplication.class.getClassLoader().getResourceAsStream("schema.sql"));
        Statement statement = connection.createStatement();
        while (scanner.hasNextLine()) {
            statement.execute(scanner.nextLine());
        }

        /*
        Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
        insertData(todo, connection);
        todo = readData(connection);
        todo.setDetails("congratulations, you have updated data!");
        updateData(todo, connection);
        deleteData(todo, connection);
        */

        log.info("Closing database connection");
        connection.close();
    }
}
```

Ten kod Java będzie używać *aplikacji. właściwości* i utworzonych wcześniej plików *Schema. SQL* w celu nawiązania połączenia z serwerem PostgreSQL i utworzenia schematu, w którym będą przechowywane nasze dane.

W tym pliku można zobaczyć, że dodałeś metody do wstawiania, odczytywania, aktualizowania i usuwania danych: kodują te metody w pozostałej części tego artykułu i będziesz mieć możliwość usuwania komentarzy do nich po sobie nawzajem.

> [!NOTE]
> Poświadczenia bazy danych są przechowywane we właściwościach *użytkownika* i *hasło* pliku *Application. Properties* . Te poświadczenia są używane podczas wykonywania `DriverManager.getConnection(properties.getProperty("url"), properties);` , ponieważ plik właściwości jest przesyłany jako argument.

Możesz teraz wykonać tę klasę główną przy użyciu ulubionego narzędzia:

- Korzystając ze środowiska IDE, należy kliknąć prawym przyciskiem myszy klasę *DemoApplication* i wykonać ją.
- Za pomocą Maven można uruchomić aplikację, wykonując: `mvn exec:java -Dexec.mainClass="com.example.demo.DemoApplication"` .

Aplikacja powinna połączyć się z Azure Database for PostgreSQL, utworzyć schemat bazy danych, a następnie zamknąć połączenie, tak jak powinno się zobaczyć w dziennikach konsoli:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Closing database connection 
```

### <a name="create-a-domain-class"></a>Utwórz klasę domeny

Utwórz nową `Todo` klasę języka Java, obok `DemoApplication` klasy, i Dodaj następujący kod:

```java
package com.example.demo;

public class Todo {

    private Long id;
    private String description;
    private String details;
    private boolean done;

    public Todo() {
    }

    public Todo(Long id, String description, String details, boolean done) {
        this.id = id;
        this.description = description;
        this.details = details;
        this.done = done;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }

    public String getDetails() {
        return details;
    }

    public void setDetails(String details) {
        this.details = details;
    }

    public boolean isDone() {
        return done;
    }

    public void setDone(boolean done) {
        this.done = done;
    }

    @Override
    public String toString() {
        return "Todo{" +
                "id=" + id +
                ", description='" + description + '\'' +
                ", details='" + details + '\'' +
                ", done=" + done +
                '}';
    }
}
```

Ta klasa jest modelem domeny mapowanym w `todo` tabeli, która została utworzona podczas wykonywania skryptu *Schema. SQL* .

### <a name="insert-data-into-azure-database-for-postgresql"></a>Wstaw dane do Azure Database for PostgreSQL

W pliku *src/Main/Java/DemoApplication. Java* po metodzie Main Dodaj następującą metodę, aby wstawić dane do bazy danych:

```java
private static void insertData(Todo todo, Connection connection) throws SQLException {
    log.info("Insert data");
    PreparedStatement insertStatement = connection
            .prepareStatement("INSERT INTO todo (id, description, details, done) VALUES (?, ?, ?, ?);");

    insertStatement.setLong(1, todo.getId());
    insertStatement.setString(2, todo.getDescription());
    insertStatement.setString(3, todo.getDetails());
    insertStatement.setBoolean(4, todo.isDone());
    insertStatement.executeUpdate();
}
```

Teraz można odkomentować dwa następujące wiersze w `main` metodzie:

```java
Todo todo = new Todo(1L, "configuration", "congratulations, you have set up JDBC correctly!", true);
insertData(todo, connection);
```

Wykonanie klasy głównej powinno teraz generować następujące dane wyjściowe:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Closing database connection
```

### <a name="reading-data-from-azure-database-for-postgresql"></a>Odczytywanie danych z Azure Database for PostgreSQL

Zapoznaj się z poprzednio wstawionymi danymi, aby sprawdzić, czy kod działa poprawnie.

W pliku *src/Main/Java/DemoApplication. Java* po `insertData` metodzie Dodaj następującą metodę w celu odczytania danych z bazy danych:

```java
private static Todo readData(Connection connection) throws SQLException {
    log.info("Read data");
    PreparedStatement readStatement = connection.prepareStatement("SELECT * FROM todo;");
    ResultSet resultSet = readStatement.executeQuery();
    if (!resultSet.next()) {
        log.info("There is no data in the database!");
        return null;
    }
    Todo todo = new Todo();
    todo.setId(resultSet.getLong("id"));
    todo.setDescription(resultSet.getString("description"));
    todo.setDetails(resultSet.getString("details"));
    todo.setDone(resultSet.getBoolean("done"));
    log.info("Data read from the database: " + todo.toString());
    return todo;
}
```

Teraz można usunąć komentarz z następującego wiersza w `main` metodzie:

```java
todo = readData(connection);
```

Wykonanie klasy głównej powinno teraz generować następujące dane wyjściowe:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Closing database connection 
```

### <a name="updating-data-in-azure-database-for-postgresql"></a>Aktualizowanie danych w Azure Database for PostgreSQL

Zaktualizujmy wcześniej wstawione dane.

Mimo że w pliku *src/Main/Java/DemoApplication. Java* , po `readData` metodzie Dodaj następującą metodę, aby zaktualizować dane w bazie danych:

```java
private static void updateData(Todo todo, Connection connection) throws SQLException {
    log.info("Update data");
    PreparedStatement updateStatement = connection
            .prepareStatement("UPDATE todo SET description = ?, details = ?, done = ? WHERE id = ?;");

    updateStatement.setString(1, todo.getDescription());
    updateStatement.setString(2, todo.getDetails());
    updateStatement.setBoolean(3, todo.isDone());
    updateStatement.setLong(4, todo.getId());
    updateStatement.executeUpdate();
    readData(connection);
}
```

Teraz można odkomentować dwa następujące wiersze w `main` metodzie:

```java
todo.setDetails("congratulations, you have updated data!");
updateData(todo, connection);
```

Wykonanie klasy głównej powinno teraz generować następujące dane wyjściowe:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Update data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true} 
[INFO   ] Closing database connection 
```

### <a name="deleting-data-in-azure-database-for-postgresql"></a>Usuwanie danych w Azure Database for PostgreSQL

Na koniec usuńmy wcześniej wstawione dane.

Mimo że w pliku *src/Main/Java/DemoApplication. Java* , po `updateData` metodzie Dodaj następującą metodę, aby usunąć dane w bazie danych:

```java
private static void deleteData(Todo todo, Connection connection) throws SQLException {
    log.info("Delete data");
    PreparedStatement deleteStatement = connection.prepareStatement("DELETE FROM todo WHERE id = ?;");
    deleteStatement.setLong(1, todo.getId());
    deleteStatement.executeUpdate();
    readData(connection);
}
```

Teraz można usunąć komentarz z następującego wiersza w `main` metodzie:

```java
deleteData(todo, connection);
```

Wykonanie klasy głównej powinno teraz generować następujące dane wyjściowe:

```
[INFO   ] Loading application properties 
[INFO   ] Connecting to the database 
[INFO   ] Database connection test: demo 
[INFO   ] Create database schema 
[INFO   ] Insert data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have set up JDBC correctly!', done=true} 
[INFO   ] Update data 
[INFO   ] Read data 
[INFO   ] Data read from the database: Todo{id=1, description='configuration', details='congratulations, you have updated data!', done=true} 
[INFO   ] Delete data 
[INFO   ] Read data 
[INFO   ] There is no data in the database! 
[INFO   ] Closing database connection 
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gratulacje! Utworzono aplikację Java, która używa JDBC do przechowywania i pobierania danych z Azure Database for PostgreSQL.

Aby wyczyścić wszystkie zasoby używane w ramach tego przewodnika Szybki Start, Usuń grupę zasobów przy użyciu następującego polecenia:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Migrowanie bazy danych przy użyciu funkcji eksportowania i importowania](./howto-migrate-using-export-and-import.md)
