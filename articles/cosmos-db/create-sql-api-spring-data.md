---
title: Szybki Start — Użyj danych wiosennych Azure Cosmos DB v3, aby utworzyć bazę danych dokumentów przy użyciu Azure Cosmos DB
description: Ten przewodnik Szybki Start przedstawia dane wiosenne Azure Cosmos DB przykład kodu v3, których można użyć do nawiązania połączenia z interfejsem API SQL Azure Cosmos DB i wykonywania względem niego zapytań
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: f9d82c78ec0f5cd1b86d3fa885f89a25ec6e3672
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91349114"
---
# <a name="quickstart-build-a-spring-data-azure-cosmos-db-v3-app-to-manage-azure-cosmos-db-sql-api-data"></a>Szybki Start: Tworzenie danych wiosennych Azure Cosmos DB aplikacji V3 w celu zarządzania Azure Cosmos DB danymi interfejsu API SQL


> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK 4](create-sql-api-java.md)
> * [Wiosenne dane v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

W tym przewodniku szybki start utworzysz konto Azure Cosmos DB interfejsu API SQL z Azure Portal i zarządzasz nim, a następnie korzystasz z aplikacji wiosennej, Azure Cosmos DB z usługi GitHub. Najpierw utwórz konto Azure Cosmos DB interfejsu API SQL przy użyciu Azure Portal, a następnie Utwórz aplikację z rozruchem wiosny za pomocą łącznika danych sprężyny Azure Cosmos DB v3, a następnie Dodaj zasoby do konta Cosmos DB przy użyciu aplikacji do rozruchu ze sprężyną. Azure Cosmos DB to wielomodelowa usługa bazy danych, która pozwala szybko tworzyć i wysyłać zapytania dotyczące dokumentów, tabel, kluczy i wartościowych baz danych przy użyciu dystrybucji globalnej i możliwości skalowania w poziomie.

> [!IMPORTANT]  
> Te informacje o wersji dotyczą wersji 3 danych wiosennych Azure Cosmos DB. [Informacje o wersji 2](sql-api-sdk-java-spring-v2.md)można znaleźć tutaj. 
>
> Dane sprężynowe Azure Cosmos DB obsługują tylko interfejs API SQL.
>
> Zobacz następujące artykuły, aby uzyskać informacje na temat sprężyny danych na innych Azure Cosmos DB interfejsów API:
> * [Sprężynowe dane dla Apache Cassandra z Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [MongoDB danych wiosennych z Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Gremlin danych wiosennych z Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Lub [Wypróbuj bezpłatnie Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure. Można również użyć [emulatora Azure Cosmos DB](https://aka.ms/cosmosdb-emulator) z identyfikatorem URI `https://localhost:8081` i kluczem `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` .
- [Zestaw Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Wskaż `JAVA_HOME` zmienną środowiskową do folderu, w którym zainstalowano JDK.
- [Archiwum binarne Maven](https://maven.apache.org/download.cgi). W systemie Ubuntu Uruchom polecenie, `apt-get install maven` Aby zainstalować Maven.
- Usługi [git](https://www.git-scm.com/downloads). W systemie Ubuntu Uruchom polecenie, `sudo apt-get install git` Aby zainstalować usługę git.

## <a name="introductory-notes"></a>Uwagi wprowadzające

*Struktura konta Cosmos DB.* Niezależnie od interfejsu API lub języka programowania, *konto* Cosmos DB zawiera zero lub więcej *baz danych*, *baza danych* (DB) zawiera zero lub więcej *kontenerów*, a *kontener* zawiera zero lub więcej elementów, jak pokazano na poniższym diagramie:

:::image type="content" source="./media/databases-containers-items/cosmos-entities.png" alt-text="Jednostki kont usługi Azure Cosmos" border="false":::

Więcej informacji na temat baz danych, kontenerów i elementów można znaleźć [tutaj.](databases-containers-items.md) Niektóre ważne właściwości są definiowane na poziomie kontenera, między nimi *przepływność* i *klucz partycji*. 

Zainicjowana przepływność jest mierzona w jednostkach żądania (*jednostek ru*), które mają cenę pieniężną i są istotnym czynnikiem w kosztu eksploatacyjnym konta. Zainicjowaną przepływność można wybrać w poziomie szczegółowości poszczególnych kontenerów lub stopnia szczegółowości poszczególnych baz danych, jednak Specyfikacja przepływności na poziomie kontenera jest zazwyczaj preferowana. Więcej informacji o aprowizacji przepływności można znaleźć [tutaj.](set-throughput.md)

Gdy elementy są wstawiane do kontenera Cosmos DB, baza danych rozszerza się w poziomie, dodając więcej magazynu i obliczeń do obsługi żądań. Pojemność magazynu i mocy obliczeniowej są dodawane w odrębnych jednostkach nazywanych *partycjami*, a użytkownik musi wybrać jedno pole w dokumentach, aby być kluczem partycji, który mapuje każdy dokument na partycję. Zarządzanie takimi partycjami polega na tym, że każda partycja ma przypisany surowy wycink z zakresu wartości klucza partycji; w związku z tym zaleca się wybranie klucza partycji, który jest relatywnie losowy lub równomiernie dystrybuowany. W przeciwnym razie niektóre partycje będą widzieć znacznie więcej żądań (*gorąca partycja*), a inne partycje zobaczą znacznie mniejszą liczbę żądań (*zimna partycja*) i należy to uniknąć. Więcej informacji na temat partycjonowania można znaleźć [tutaj](partitioning-overview.md).

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
git clone https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos
```

## <a name="review-the-code"></a>Przeglądanie kodu

Ta czynność jest opcjonalna. Jeśli chcesz się dowiedzieć, jak zasoby bazy danych są tworzone w kodzie, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz od razu przejść do sekcji [Uruchamianie aplikacji](#run-the-app). 

### <a name="application-configuration-file"></a>Plik konfiguracji aplikacji

W tym miejscu przedstawiono sposób, w jaki sprężyny rozruchowe i sprężynowe rozszerzają środowisko użytkownika — proces ustanawiania klienta Cosmos i łączenia się z zasobami Cosmos jest teraz konfiguracją, a nie kodem. Podczas uruchamiania aplikacji wiosny sprężyny obsługują wszystkie te standardowe przy użyciu ustawień w **aplikacji. Properties**:

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

Po utworzeniu konta Azure Cosmos DB, bazy danych i kontenera po prostu wypełnianie w pliku konfiguracyjnym i sprężynowe dane rozruchowe/sprężynowe będą automatycznie wykonywać następujące czynności: (1) utworzyć bazowe wystąpienie zestawu Java SDK `CosmosClient` z identyfikatorem URI i kluczem oraz (2) połączyć się z bazą danych i kontenerem. Wszystko jest ustawione — **nie ma więcej kodu zarządzania zasobami!**

### <a name="java-source"></a>Źródło Java

Wartość danych wiosennych — Dodawanie jest również dostępne z prostego, czystego, ustandaryzowanego i niezależnego od platformy interfejsu do obsługi magazynów danych. Poniżej przedstawiono przykłady CRUD i zapytań na potrzeby Azure Cosmos DB manipulowania przykładowymi dokumentami z sprężyną Azure Cosmos DB danych.

* Tworzenie i aktualizowanie elementów przy użyciu `save` metody.

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]
   
* Point-odczytuje przy użyciu pochodnej metody zapytania zdefiniowanej w repozytorium. `findByIdAndLastName`Wykonuje odczyt punktów dla `UserRepository` . Pola wymienione w nazwie metody powodują, że dane sprężynowe mają być wykonywane do odczytu punktów zdefiniowanego przez `id` i `lastName` pola:

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Read)]

* Usuwanie elementu przy użyciu `deleteAll` :

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

* Zapytanie pochodne oparte na nazwie metody repozytorium. Dane sprężynowe implementują `UserRepository` `findByFirstName` metodę jako zapytanie SQL SDK języka Java dla `firstName` tego pola (nie można zaimplementować tego zapytania jako odczytu punktu):

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

## <a name="run-the-app"></a>Uruchamianie aplikacji

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i uruchomić aplikację, korzystając z informacji o punkcie końcowym. Umożliwia to aplikacji komunikację z hostowaną bazą danych.

1. W oknie terminalu usługi git wpisz polecenie `cd`, aby przejść do folderu z przykładowym kodem.

    ```bash
    cd azure-spring-data-cosmos-java-sql-api-getting-started/azure-spring-data-cosmos-java-getting-started/
    ```

2. W oknie terminalu usługi git Użyj następującego polecenia, aby zainstalować wymagane pakiety danych sprężyny Azure Cosmos DB.

    ```bash
    mvn clean package
    ```

3. W oknie terminalu usługi git Użyj następującego polecenia, aby uruchomić dane wiosenne Azure Cosmos DB aplikacji:

    ```bash
    mvn spring-boot:run
    ```
    
4. Aplikacja ładuje **aplikację. właściwości** i łączy zasoby na koncie Azure Cosmos DB.
5. Aplikacja wykona CRUD operacje opisane powyżej.
6. Aplikacja wykona zapytanie pochodne.
7. Aplikacja nie usuwa zasobów. Przełącz się z powrotem do portalu, aby [wyczyścić zasoby](#clean-up-resources) z Twojego konta, jeśli chcesz uniknąć naliczania opłat.

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia konta interfejsu API SQL Azure Cosmos DB, tworzenia bazy danych dokumentów i kontenera przy użyciu Eksplorator danych i uruchamiania aplikacji ze sprężyną danych w celu programistycznego wykonywania tych samych czynności. Teraz możesz zaimportować dodatkowe dane do konta Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Importowanie danych do usługi Azure Cosmos DB](import-data.md)
