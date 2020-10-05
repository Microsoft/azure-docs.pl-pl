---
title: Używanie języka Java i JDBC z Azure SQL Database
description: Dowiedz się, jak używać języka Java i JDBC z Azure SQL Database.
services: sql-database
author: jdubois
ms.author: judubois
ms.service: sql-database
ms.subservice: development
ms.topic: quickstart
ms.devlang: java
ms.date: 06/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: 829a106a643c10626a65855152375c349cd76f9a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87833593"
---
# <a name="use-java-and-jdbc-with--azure-sql-database"></a>Używanie języka Java i JDBC z Azure SQL Database

W tym temacie przedstawiono Tworzenie przykładowej aplikacji, która używa języka Java i [JDBC](https://en.wikipedia.org/wiki/Java_Database_Connectivity) do przechowywania i pobierania informacji w [Azure SQL Database](https://docs.microsoft.com/azure/sql-database/).

JDBC jest standardowym interfejsem API języka Java do łączenia z tradycyjnymi relacyjnymi bazami danych.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure. Jeśli go nie masz, [Uzyskaj bezpłatną wersję próbną](https://azure.microsoft.com/free/).
- [Azure Cloud Shell](/azure/cloud-shell/quickstart) lub [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Zalecamy Azure Cloud Shell, aby zalogować się automatycznie i mieć dostęp do wszystkich potrzebnych narzędzi.
- Obsługiwany [zestaw Java Development Kit](https://aka.ms/azure-jdks), wersja 8 (uwzględniony w Azure Cloud Shell).
- Narzędzie kompilacji [Apache Maven](https://maven.apache.org/) .

## <a name="prepare-the-working-environment"></a>Przygotowanie środowiska roboczego

Zamierzamy używać zmiennych środowiskowych, aby ograniczyć liczbę błędów i ułatwić dostosowanie poniższej konfiguracji do konkretnych potrzeb.

Skonfiguruj te zmienne środowiskowe za pomocą następujących poleceń:

```bash
AZ_RESOURCE_GROUP=database-workshop
AZ_DATABASE_NAME=<YOUR_DATABASE_NAME>
AZ_LOCATION=<YOUR_AZURE_REGION>
AZ_SQL_SERVER_USERNAME=demo
AZ_SQL_SERVER_PASSWORD=<YOUR_AZURE_SQL_PASSWORD>
AZ_LOCAL_IP_ADDRESS=<YOUR_LOCAL_IP_ADDRESS>
```

Zastąp symbole zastępcze następującymi wartościami, które są używane w tym artykule:

- `<YOUR_DATABASE_NAME>`: Nazwa serwera Azure SQL Database. Powinna być unikatowa w ramach platformy Azure.
- `<YOUR_AZURE_REGION>`: Region świadczenia usługi Azure, który będzie używany. Można używać `eastus` Domyślnie, ale zalecamy skonfigurowanie regionu bliżej miejsca, w którym się znajdują. Aby uzyskać pełną listę dostępnych regionów, wprowadź `az account list-locations` .
- `<AZ_SQL_SERVER_PASSWORD>`: Hasło serwera Azure SQL Database. To hasło powinno zawierać co najmniej osiem znaków. Znaki powinny pochodzić z trzech z następujących kategorii: wielkie litery angielskie, małe litery angielskie, cyfry (0-9) i znaki inne niż alfanumeryczne (!, $, #,% itd.).
- `<YOUR_LOCAL_IP_ADDRESS>`: Adres IP komputera lokalnego, z którego będzie uruchamiana aplikacja Java. Jednym z wygody jest wskazanie, że Twoja przeglądarka ma być [whatismyip.Akamai.com](http://whatismyip.akamai.com/).

Następnie utwórz grupę zasobów przy użyciu następującego polecenia:

```azurecli
az group create \
    --name $AZ_RESOURCE_GROUP \
    --location $AZ_LOCATION \
  	| jq
```

> [!NOTE]
> Używamy `jq` Narzędzia do wyświetlania danych JSON i zwiększania czytelności. To narzędzie jest instalowane domyślnie na [Azure Cloud Shell](https://shell.azure.com/). Jeśli nie lubisz tego narzędzia, możesz bezpiecznie usunąć `| jq` część wszystkich poleceń, które będą używane.

## <a name="create-an-azure-sql-database-instance"></a>Tworzenie wystąpienia Azure SQL Database

W pierwszej kolejności tworzymy serwer Azure SQL Database zarządzany.

> [!NOTE]
> Więcej szczegółowych informacji na temat tworzenia serwerów Azure SQL Database można znaleźć w [przewodniku szybki start: tworzenie Azure SQL Database pojedynczej bazy danych](/azure/sql-database/sql-database-single-database-get-started).

W [Azure Cloud Shell](https://shell.azure.com/)Uruchom następujące polecenie:

```azurecli
az sql server create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME \
    --location $AZ_LOCATION \
    --admin-user $AZ_SQL_SERVER_USERNAME \
    --admin-password $AZ_SQL_SERVER_PASSWORD \
  	| jq
```

To polecenie tworzy serwer Azure SQL Database.

### <a name="configure-a-firewall-rule-for-your-azure-sql-database-server"></a>Konfigurowanie reguły zapory dla serwera Azure SQL Database

Wystąpienia Azure SQL Database są zabezpieczone domyślnie. Mają zaporę, która nie zezwala na żadne połączenie przychodzące. Aby można było korzystać z bazy danych programu, należy dodać regułę zapory zezwalającą na dostęp lokalnego adresu IP do serwera bazy danych.

Ze względu na to, że lokalny adres IP został skonfigurowany na początku tego artykułu, możesz otworzyć zaporę serwera, uruchamiając następujące polecenie:

```azurecli
az sql server firewall-rule create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name $AZ_DATABASE_NAME-database-allow-local-ip \
    --server $AZ_DATABASE_NAME \
    --start-ip-address $AZ_LOCAL_IP_ADDRESS \
    --end-ip-address $AZ_LOCAL_IP_ADDRESS \
  	| jq
```

### <a name="configure-a-azure-sql-database"></a>Konfigurowanie bazy danych Azure SQL Database

Utworzony wcześniej serwer Azure SQL Database jest pusty. Nie ma żadnej bazy danych, której można używać z aplikacją Java. Utwórz nową bazę danych o nazwie `demo` , uruchamiając następujące polecenie:

```azurecli
az sql db create \
    --resource-group $AZ_RESOURCE_GROUP \
    --name demo \
    --server $AZ_DATABASE_NAME \
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
            <groupId>com.microsoft.sqlserver</groupId>
            <artifactId>mssql-jdbc</artifactId>
            <version>7.4.1.jre8</version>
        </dependency>
    </dependencies>
</project>
```

Ten plik to [Apache Maven](https://maven.apache.org/) , który konfiguruje nasz projekt do użycia:

- Java 8
- Najnowszy SQL Server sterownika dla języka Java

### <a name="prepare-a-configuration-file-to-connect-to-azure-sql-database"></a>Przygotuj plik konfiguracji do nawiązania połączenia z usługą Azure SQL Database

Utwórz plik *src/Main/sources/Application. Properties* i Dodaj:

```properties
url=jdbc:sqlserver://$AZ_DATABASE_NAME.database.windows.net:1433;database=demo;encrypt=true;trustServerCertificate=false;hostNameInCertificate=*.database.windows.net;loginTimeout=30;
user=demo@$AZ_DATABASE_NAME
password=$AZ_SQL_SERVER_PASSWORD
```

- Zastąp dwie `$AZ_DATABASE_NAME` zmienne wartością skonfigurowaną na początku tego artykułu.
- Zastąp `$AZ_SQL_SERVER_PASSWORD` zmienną wartością skonfigurowaną na początku tego artykułu.

### <a name="create-an-sql-file-to-generate-the-database-schema"></a>Utwórz plik SQL, aby wygenerować schemat bazy danych

Użyjemy *src/Main/sources `schema.sql` * , aby utworzyć schemat bazy danych. Utwórz ten plik z następującą zawartością:

```sql
DROP TABLE IF EXISTS todo;
CREATE TABLE todo (id INT PRIMARY KEY, description VARCHAR(255), details VARCHAR(4096), done BIT);
```

## <a name="code-the-application"></a>Kod aplikacji

### <a name="connect-to-the-database"></a>Łączenie z bazą danych

Następnie Dodaj kod Java, który będzie używać JDBC do przechowywania i pobierania danych z bazy danych SQL Azure.

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

Ten kod Java będzie używać *aplikacji. właściwości* i utworzonych wcześniej plików *Schema. SQL* w celu nawiązania połączenia z bazą danych SQL Server i utworzenia schematu, w którym będą przechowywane nasze dane.

W tym pliku można zobaczyć, że dodałeś metody do wstawiania, odczytywania, aktualizowania i usuwania danych: kodują te metody w pozostałej części tego artykułu i będziesz mieć możliwość usuwania komentarzy do nich po sobie nawzajem.

> [!NOTE]
> Poświadczenia bazy danych są przechowywane we właściwościach *użytkownika* i *hasło* pliku *Application. Properties* . Te poświadczenia są używane podczas wykonywania `DriverManager.getConnection(properties.getProperty("url"), properties);` , ponieważ plik właściwości jest przesyłany jako argument.

Możesz teraz wykonać tę klasę główną przy użyciu ulubionego narzędzia:

- Korzystając ze środowiska IDE, należy kliknąć prawym przyciskiem myszy klasę *DemoApplication* i wykonać ją.
- Za pomocą Maven można uruchomić aplikację, wykonując: `mvn exec:java -Dexec.mainClass="com.example.demo.DemoApplication"` .

Aplikacja powinna połączyć się z Azure SQL Database, utworzyć schemat bazy danych, a następnie zamknąć połączenie, tak jak powinno się zobaczyć w dziennikach konsoli:

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

### <a name="insert-data-into-azure-sql-database"></a>Wstawianie danych do usługi Azure SQL Database

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

### <a name="reading-data-from-azure-sql-database"></a>Odczytywanie danych z usługi Azure SQL Database

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

### <a name="updating-data-in-azure-sql-database"></a>Aktualizowanie danych w Azure SQL Database

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

### <a name="deleting-data-in-azure-sql-database"></a>Usuwanie danych w usłudze Azure SQL Database

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

## <a name="conclusion-and-resources-clean-up"></a>Usuwanie wniosków i zasobów

Gratulacje! Utworzono aplikację Java, która używa JDBC do przechowywania i pobierania danych z usługi Azure SQL Database.

Aby wyczyścić wszystkie zasoby używane w ramach tego przewodnika Szybki Start, Usuń grupę zasobów przy użyciu następującego polecenia:

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>Następne kroki

- [Projektuj swoją pierwszą bazę danych w Azure SQL Database](design-first-database-tutorial.md)  
- [Sterownik JDBC firmy Microsoft dla SQL Server](https://github.com/microsoft/mssql-jdbc)  
- [Zgłaszanie problemów/zadawanie pytań](https://github.com/microsoft/mssql-jdbc/issues)  
