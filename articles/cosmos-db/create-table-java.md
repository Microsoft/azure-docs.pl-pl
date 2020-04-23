---
title: Tworzenie aplikacji — Usługa Azure Cosmos DB za pomocą interfejsu API tabel i języka Java
description: Ten przewodnik szybkiego startu przedstawia wykorzystanie interfejsu API tabeli usługi Azure Cosmos DB do tworzenia aplikacji przy użyciu witryny Azure Portal i języka Java
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: java
ms.topic: quickstart
ms.date: 04/10/2018
ms.author: sngun
ms.custom: seo-java-august2019, seo-java-september2019
ms.openlocfilehash: e3517804cb66a9f98351e4c68f4f7c4387cee8fe
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083805"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-table-api-data"></a>Szybki start: tworzenie aplikacji Java do zarządzania danymi interfejsu API tabeli usługi Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.js](create-table-nodejs.md)
> * [Python](create-table-python.md)
> 

W tym przewodniku Szybki start utworzysz konto interfejsu API tabeli usługi Azure Cosmos DB i użyj Eksploratora danych i aplikacji Java sklonowanej z usługi GitHub do tworzenia tabel i jednostek. Usługa Azure Cosmos DB to wielomodelowa usługa bazy danych, która umożliwia szybkie tworzenie i wykonywanie zapytań o bazy danych dokumentów, tabeli, wartości klucza i wykresów z możliwościami dystrybucji globalnej i skali poziomej.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz jeden za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Możesz [też bezpłatnie wypróbować usługę Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) bez subskrypcji platformy Azure. Można również użyć [emulatora bazy danych usługi Azure Cosmos](https://aka.ms/cosmosdb-emulator) z identyfikatorem URI `https://localhost:8081` i kluczem. `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`
- [Zestaw java development (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Skieruj `JAVA_HOME` zmienną środowiskową do folderu, w którym jest zainstalowany JDK.
- [Archiwum binarne Maven](https://maven.apache.org/download.cgi). 
- [Git](https://www.git-scm.com/downloads). 

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

> [!IMPORTANT] 
> Musisz utworzyć nowe konto interfejsu API tabeli, aby pracować z ogólnie dostępnymi zestawami SDK interfejsu API tabeli. Konta interfejsu API tabeli utworzone w okresie obowiązywania wersji zapoznawczej nie są obsługiwane przez ogólnie dostępne zestawy SDK.
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>Dodawanie tabeli

[!INCLUDE [cosmos-db-create-table](../../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>Dodawanie danych przykładowych

[!INCLUDE [cosmos-db-create-table-add-sample-data](../../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz sklonujemy aplikację Tabela z serwisu GitHub, ustawimy parametry połączenia i uruchomimy ją. Zobaczysz, jak łatwo jest pracować programowo z danymi.

1. Otwórz wiersz polecenia, utwórz nowy folder o nazwie git-samples, a następnie zamknij wiersz polecenia.

    ```bash
    md "C:\git-samples"
    ```

2. Otwórz okno terminala usługi Git, np. git bash, i użyj polecenia `cd`, aby przejść do nowego folderu instalacji aplikacji przykładowej.

    ```bash
    cd "C:\git-samples"
    ```

3. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze.

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-java-getting-started.git 
    ```

> ! [PORADA] Aby uzyskać bardziej szczegółowe wskazówki dotyczące podobnego kodu, zobacz przykładowy artykuł [interfejsu API tabeli usługi Cosmos DB.](table-storage-how-to-use-java.md) 

## <a name="update-your-connection-string"></a>Aktualizowanie parametrów połączenia

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o parametrach połączenia i skopiować je do aplikacji. Umożliwia to aplikacji komunikację z hostowaną bazą danych. 

1. Na swoim koncie usługi Azure Cosmos DB w [portalu Azure](https://portal.azure.com/)wybierz pozycję **Parametry połączenia**. 

   ![Wyświetlanie informacji o ciągu połączenia w okienku Ciąg połączenia](./media/create-table-java/cosmos-db-quickstart-connection-string.png)

2. Skopiuj PODSTAWOWE PARAMETRY POŁĄCZENIA przy użyciu przycisku kopiowania po prawej stronie.

3. Otwórz *plik config.properties* z folderu *C:\git-samples\storage-table-java-getting-started\src\main\resources* folderu. 

5. Oznacz jako komentarz wiersz pierwszy i usuń znaczniki komentarza dla wiersza drugiego. Pierwsze dwa wiersze powinny teraz wyglądać następująco.

    ```xml
    #StorageConnectionString = UseDevelopmentStorage=true
    StorageConnectionString = DefaultEndpointsProtocol=https;AccountName=[ACCOUNTNAME];AccountKey=[ACCOUNTKEY]
    ```

6. Wklej PODSTAWOWE PARAMETRY POŁĄCZENIA z portalu do wartości StorageConnectionString w wierszu 2. 

    > [!IMPORTANT]
    > Jeśli dany punkt końcowy korzysta z adresu documents.azure.com, oznacza to, że masz konto w wersji zapoznawczej i musisz utworzyć [nowe konto interfejsu API tabeli](#create-a-database-account), aby korzystać z dostępnego ogólnie zestawu SDK API tabeli.
    >

7. Zapisz plik *config.properties.*

Aplikacja została zaktualizowana i zawiera teraz wszystkie informacje potrzebne do nawiązania komunikacji z usługą Azure Cosmos DB. 

## <a name="run-the-app"></a>Uruchomienie aplikacji

1. W oknie terminala usługi Git za pomocą polecenia `cd` przejdź do folderu storage-table-java-getting-started.

    ```git
    cd "C:\git-samples\storage-table-java-getting-started"
    ```

2. W oknie terminala git uruchom następujące polecenia, aby uruchomić aplikację Java.

    ```git
    mvn compile exec:java 
    ```

    W oknie konsoli zostaną wyświetlone dane tabeli dodawane do nowej bazy danych tabel w usłudze Azure Cosmos DB.

    Teraz możesz wrócić do Eksploratora danych i zobaczyć, jak się pracuje z nowymi danymi, modyfikuje je i tworzy zapytania względem nich. 

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start przedstawiono sposób tworzenia konta usługi Azure Cosmos DB, tworzenia tabeli przy użyciu Eksploratora danych i uruchamiania aplikacji Java w celu dodania danych tabeli.  Teraz można tworzyć zapytania do danych przy użyciu interfejsu API tabel.  

> [!div class="nextstepaction"]
> [Importowanie danych tabeli do interfejsu API tabeli](table-import.md)
