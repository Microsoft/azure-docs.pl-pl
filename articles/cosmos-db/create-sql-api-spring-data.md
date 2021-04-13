---
title: Szybki start — tworzenie bazy danych dokumentów Azure Cosmos DB w wersji 3 przy użyciu narzędzia Spring Data Azure Cosmos DB
description: Ten przewodnik Szybki start przedstawia przykładowy kod spring data Azure Cosmos DB w wersji 3, który umożliwia nawiązywanie połączeń z interfejsem API SQL Azure Cosmos DB zapytań
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 03/07/2021
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: a094ebb6b218027c195985312b023ccb8838f703
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365686"
---
# <a name="quickstart-build-a-spring-data-azure-cosmos-db-v3-app-to-manage-azure-cosmos-db-sql-api-data"></a>Szybki start: tworzenie aplikacji Spring Data Azure Cosmos DB v3 do zarządzania danymi Azure Cosmos DB API SQL
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK 4](create-sql-api-java.md)
> * [Spring Data 3](create-sql-api-spring-data.md)
> * [Łącznik platformy Spark w wersji 3](create-sql-api-spark.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

W tym przewodniku Szybki start utworzysz konto interfejsu API SQL usługi Azure Cosmos DB i zarządzasz nimi z usługi Azure Portal oraz za pomocą aplikacji Spring Data Azure Cosmos DB v3 sklonowanej z usługi GitHub. Najpierw należy utworzyć konto interfejsu API SQL usługi Azure Cosmos DB przy użyciu usługi Azure Portal, następnie utworzyć aplikację usługi Spring Boot przy użyciu łącznika Spring Data Azure Cosmos DB v3, a następnie dodać zasoby do konta usługi Cosmos DB przy użyciu aplikacji Spring Boot. Azure Cosmos DB to wielo modelowa usługa bazy danych, która umożliwia szybkie tworzenie dokumentów, tabel, klucz-wartość i grafowych baz danych z możliwościami globalnej dystrybucji i skalowania w poziomie.

> [!IMPORTANT]  
> Te informacje o wersji są dla wersji 3 programu Spring Data Azure Cosmos DB. Informacje o wersji [dla wersji 2](sql-api-sdk-java-spring-v2.md)można znaleźć tutaj. 
>
> Usługa Spring Data Azure Cosmos DB obsługuje tylko interfejs API SQL.
>
> Zapoznaj się z tymi artykułami, aby uzyskać informacje na temat danych spring data w innych Azure Cosmos DB API:
> * [Spring Data for Apache Cassandra z Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Baza danych MongoDB Spring Data z Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Spring Data Gremlin z Azure Cosmos DB](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) Możesz [też Azure Cosmos DB bezpłatnie bez](https://azure.microsoft.com/try/cosmosdb/) subskrypcji platformy Azure. Możesz również użyć emulatora [Azure Cosmos DB z](https://aka.ms/cosmosdb-emulator) URI `https://localhost:8081` i kluczem `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` .
- [Zestaw Java Development Kit (JDK) 8.](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk) Wskaż w `JAVA_HOME` zmiennej środowiskowej folder, w którym zainstalowano JDK.
- Archiwum [binarne maven.](https://maven.apache.org/download.cgi) W systemie Ubuntu uruchom program , `apt-get install maven` aby zainstalować program Maven.
- [Git](https://www.git-scm.com/downloads). W systemie Ubuntu uruchom program , `sudo apt-get install git` aby zainstalować program Git.

## <a name="introductory-notes"></a>Uwagi wprowadzające

*Struktura konta Cosmos DB konta.* Niezależnie od interfejsu API lub języka  programowania konto usługi Cosmos DB zawiera zero lub więcej baz *danych,* baza danych *(DB)* zawiera zero lub więcej kontenerów,  *a* kontener zawiera zero lub więcej elementów, jak pokazano na poniższym diagramie:

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Jednostki konta usługi Azure Cosmos" border="false":::

Więcej informacji na temat baz danych, kontenerów i elementów można uzyskać [tutaj.](account-databases-containers-items.md) Na poziomie kontenera zdefiniowano kilka ważnych właściwości, między innymi aprowizowanej *przepływności* i *klucza partycji.* 

Aprowizowana przepływność jest mierzona w jednostkach żądania *(JEDNOSTKI* ŻĄDAŃ), które mają cenę pieniężną i są znaczącym czynnikiem określającym koszt operacyjny konta. Aprowizowana przepływność można wybrać z poziomem szczegółowości 1 kontenera lub szczegółowością dla bazy danych, jednak zwykle preferowana jest specyfikacja przepływności na poziomie kontenera. Więcej informacji na temat aprowizowania przepływności można uzyskać [tutaj.](set-throughput.md)

Gdy elementy są wstawiane do kontenera Cosmos DB, baza danych zwiększa się w poziomie, dodając więcej magazynu i zasobów obliczeniowych do obsługi żądań. Pojemność magazynu i mocy obliczeniowej są dodawane w jednostkach dyskretnych nazywanych partycjami i należy wybrać jedno pole w dokumentach jako klucz partycji, który mapuje każdy dokument na partycję. Sposób zarządzania partycjami to przypisanie każdej partycji w przybliżeniu równego wycinka z zakresu wartości klucza partycji; Dlatego zaleca się wybranie klucza partycji, który jest względnie losowy lub równomiernie rozproszony. W przeciwnym razie w niektórych partycjachbędzie znacznie więcej żądań (gorąca partycja), podczas gdy w innych partycjach będzie znacznie mniej żądań *(partycja* zimna), a tego należy unikać. Więcej informacji na temat partycjonowania można uzyskać [tutaj.](partitioning-overview.md)

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

Przed utworzeniem bazy danych dokumentów należy utworzyć konto interfejsu API SQL za pomocą usługi Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>Dodawanie kontenera

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>Dodawanie danych przykładowych

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>Wysyłanie zapytań dotyczących danych

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz przejdźmy do pracy z kodem. Sklonujmy aplikację interfejsu API SQL z serwisu GitHub, ustawmy parametry połączenia i uruchommy ją. Zobaczysz, jak łatwo jest pracować programowo z danymi. 

Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze.

```bash
git clone https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started.git
```

## <a name="review-the-code"></a>Przeglądanie kodu

Ta czynność jest opcjonalna. Jeśli chcesz się dowiedzieć, jak zasoby bazy danych są tworzone w kodzie, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz od razu przejść do sekcji [Uruchamianie aplikacji](#run-the-app). 

### <a name="application-configuration-file"></a>Plik konfiguracji aplikacji

W tym miejscu pokazujemy, Spring Boot i spring data poprawiają środowisko użytkownika — proces ustanawiania klienta usługi Cosmos i nawiązywania połączenia z zasobami usługi Cosmos jest teraz konfiguracyjny, a nie kod. Podczas uruchamiania Spring Boot obsługuje wszystkie te typy przy użyciu ustawień w **właściwościach application.properties:**

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

Po utworzeniu konta usługi Azure Cosmos DB, bazy danych i kontenera wystarczy wypełnić pola puste w pliku konfiguracji, Spring Boot/Spring Data automatycznie wykonaj następujące czynności: (1) utwórz bazowe wystąpienie zestawu JAVA SDK z kluczem i (URI) oraz (2) połączysz się z bazą danych i `CosmosClient` kontenerem. Wszystko jest już ustawione — **koniec z kodem zarządzania zasobami.**

### <a name="java-source"></a>Źródło Java

Wartość dodana rozwiązania Spring Data pochodzi również z prostego, czystego, standardowego i niezależnego od platformy interfejsu do obsługi magazynów danych. Na podstawie powyższego przykładu Spring Data w serwisie GitHub powiązywały się z nimi przykłady crud i zapytań do manipulowania dokumentami Azure Cosmos DB za pomocą narzędzia Spring Data Azure Cosmos DB.

* Tworzenie i aktualizacje elementów przy użyciu `save` metody .

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]
   
* Odczyty typu point przy użyciu pochodnej metody zapytania zdefiniowanej w repozytorium. Wykonuje `findByIdAndLastName` odczyty punktu dla `UserRepository` . Pola wymienione w nazwie metody powodują, że źródło Danych Spring wykonuje odczyt punktu zdefiniowany przez `id` pola `lastName` i :

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Read)]

* Usuwanie elementu przy użyciu elementu `deleteAll` :

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

* Zapytanie pochodne oparte na nazwie metody repozytorium. Spring Data implementuje metodę jako zapytanie SQL zestawu Java SDK w polu (tego zapytania nie można zaimplementować `UserRepository` `findByFirstName` jako odczytu `firstName` punktu):

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

## <a name="run-the-app"></a>Uruchamianie aplikacji

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i uruchomić aplikację, korzystając z informacji o punkcie końcowym. Umożliwia to aplikacji komunikację z hostowaną bazą danych.

1. W oknie terminalu usługi git wpisz polecenie `cd`, aby przejść do folderu z przykładowym kodem.

    ```bash
    cd azure-spring-data-cosmos-java-sql-api-getting-started/azure-spring-data-cosmos-java-getting-started/
    ```

2. W oknie terminalu usługi Git użyj następującego polecenia, aby zainstalować wymagane pakiety Azure Cosmos DB Spring Data.

    ```bash
    mvn clean package
    ```

3. W oknie terminalu usługi Git użyj następującego polecenia, aby uruchomić aplikację Spring Data Azure Cosmos DB aplikacji:

    ```bash
    mvn spring-boot:run
    ```
    
4. Aplikacja ładuje **application.properties** i łączy zasoby na Twoim Azure Cosmos DB konta.
5. Aplikacja będzie wykonywać opisane powyżej operacje CRUD punktu.
6. Aplikacja wykona zapytanie pochodne.
7. Aplikacja nie usuwa zasobów. Przełącz się z powrotem do [portalu, aby wyczyścić zasoby](#clean-up-resources) z konta, jeśli chcesz uniknąć naliczenie opłat.

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wiesz już, jak utworzyć konto interfejsu API SQL usługi Azure Cosmos DB, utworzyć bazę danych dokumentów i kontener przy użyciu interfejsu Eksplorator danych oraz uruchomić aplikację Spring Data, aby zrobić to samo programowo. Teraz możesz zaimportować dodatkowe dane na swoje Azure Cosmos DB konto. 

> [!div class="nextstepaction"]
> [Importowanie danych do usługi Azure Cosmos DB](import-data.md)