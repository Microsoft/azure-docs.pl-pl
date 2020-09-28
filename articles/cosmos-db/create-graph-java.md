---
title: Tworzenie bazy danych grafu przy użyciu języka Java w Azure Cosmos DB
description: Przykładowy kod Java, którego można używać do nawiązywania połączeń z danymi grafu i wykonywania zapytań względem nich w usłudze Azure Cosmos DB za pomocą języka Gremlin.
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.devlang: java
ms.topic: quickstart
ms.date: 03/26/2019
ms.author: jasonh
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: ad00fcc0c7b871210b29400821808b6729d953f6
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2020
ms.locfileid: "91409412"
---
# <a name="quickstart-build-a-graph-database-with-the-java-sdk-and-the-azure-cosmos-db-gremlin-api"></a>Szybki Start: Tworzenie bazy danych grafu przy użyciu zestawu Java SDK i interfejsu API Azure Cosmos DB Gremlin

> [!div class="op_single_selector"]
> * [Konsola Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

W tym przewodniku szybki start utworzysz konto interfejsu API Azure Cosmos DB Gremlin (Graph) z Azure Portal i zarządzasz nim, a następnie dodasz dane przy użyciu aplikacji Java sklonowanej z usługi GitHub. Azure Cosmos DB to wielomodelowa usługa bazy danych, która pozwala szybko tworzyć i wysyłać zapytania dotyczące dokumentów, tabel, kluczy i wartościowych baz danych przy użyciu dystrybucji globalnej i możliwości skalowania w poziomie.

## <a name="prerequisites"></a>Wymagania wstępne
- Konto platformy Azure z aktywną subskrypcją. [Utwórz je bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- [Zestaw Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). Wskaż `JAVA_HOME` zmienną środowiskową do folderu, w którym zainstalowano JDK.
- [Archiwum binarne Maven](https://maven.apache.org/download.cgi). 
- Usługi [git](https://www.git-scm.com/downloads). 

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

Przed utworzeniem bazy danych grafów musisz utworzyć konto bazy danych Gremlin (Graph) z użyciem usługi Azure Cosmos DB.

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Dodawanie wykresu

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="clone-the-sample-application"></a>Klonowanie przykładowej aplikacji

Teraz przejdźmy do pracy z kodem. Sklonujemy aplikację interfejsu API języka Gremlin z usługi GitHub, ustawimy parametry połączenia i uruchomimy ją. Zobaczysz, jak łatwo jest pracować programowo z danymi.  

1. Otwórz wiersz polecenia, utwórz nowy folder o nazwie git-samples, a następnie zamknij wiersz polecenia.

    ```bash
    md "C:\git-samples"
    ```

2. Otwórz okno terminala usługi Git, na przykład git bash, i użyj polecenia `cd`, aby przejść do folderu instalacji aplikacji przykładowej.  

    ```bash
    cd "C:\git-samples"
    ```

3. Uruchom następujące polecenie w celu sklonowania przykładowego repozytorium. To polecenie tworzy kopię aplikacji przykładowej na komputerze. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-graph-java-getting-started.git
    ```

## <a name="review-the-code"></a>Przeglądanie kodu

Ta czynność jest opcjonalna. Jeśli chcesz się dowiedzieć, jak zasoby bazy danych są tworzone w kodzie, możesz przejrzeć poniższe fragmenty kodu. W przeciwnym razie możesz od razu przejść do sekcji [Aktualizacja parametrów połączenia](#update-your-connection-information).

Wszystkie poniższe fragmenty kodu pochodzą z pliku *C:\git-samples\azure-Cosmos-DB-Graph-Java-getting-started\src\GetStarted\Program.Java* .

Ta Aplikacja konsolowa języka Java korzysta z bazy danych [interfejsu API Gremlin](graph-introduction.md) z sterownikiem OSS [Apache TinkerPop](https://tinkerpop.apache.org/) . 

- Gremlin `Client` jest inicjowana z konfiguracji w pliku *C:\git-samples\azure-Cosmos-DB-Graph-Java-getting-started\src\remote.YAML* .

    ```java
    cluster = Cluster.build(new File("src/remote.yaml")).create();
    ...
    client = cluster.connect();
    ```

- Wykonywanie serii kroków języka Gremlin przy użyciu metody `client.submit`.

    ```java
    ResultSet results = client.submit(gremlin);

    CompletableFuture<List<Result>> completableFutureResults = results.all();
    List<Result> resultList = completableFutureResults.get();

    for (Result result : resultList) {
        System.out.println(result.toString());
    }
    ```

## <a name="update-your-connection-information"></a>Aktualizowanie danych połączenia

Teraz wróć do witryny Azure Portal, aby uzyskać informacje o połączeniu i skopiować je do aplikacji. Te ustawienia umożliwiają aplikacji komunikację z hostowaną bazą danych.

1. Na koncie Azure Cosmos DB w [Azure Portal](https://portal.azure.com/)wybierz pozycję **klucze**. 

    Skopiuj pierwszą część wartości identyfikatora URI.

    :::image type="content" source="./media/create-graph-java/copy-access-key-azure-portal.png" alt-text="Wyświetlanie i kopiowanie klucza dostępu w witrynie Azure Portal, strona Klucze":::

2. Otwórz plik *src/Remote. YAML* i wklej unikatową wartość identyfikatora `$name$` w programie `hosts: [$name$.graphs.azure.com]` .

    Wiersz 1 elementu *Remote. YAML* powinien teraz wyglądać podobnie do 

    `hosts: [test-graph.graphs.azure.com]`

3. Zmień element `graphs` na `gremlin.cosmosdb` w wartości `endpoint`. (Jeśli konto bazy danych programu Graph zostało utworzone przed 20 grudnia 2017 r., nie wprowadzaj żadnych zmian wartości punktu końcowego i przejdź do następnego kroku).

    Wartość punktu końcowego powinna wyglądać następująco:

    `"endpoint": "https://testgraphacct.gremlin.cosmosdb.azure.com:443/"`

4. W witrynie Azure Portal użyj przycisku kopiowania, aby skopiować KLUCZ PODSTAWOWY, i wklej go za pośrednictwem elementu `$masterKey$` w `password: $masterKey$`.

    Wiersz 4 elementu *Remote. YAML* powinien teraz wyglądać podobnie do 

    `password: 2Ggkr662ifxz2Mg==`

5. Zmień wiersz 3 elementu *Remote. YAML* z

    `username: /dbs/$database$/colls/$collection$`

    na wartość 

    `username: /dbs/sample-database/colls/sample-graph`

    W przypadku użycia unikatowej nazwy dla przykładowej bazy danych lub grafu odpowiednio zaktualizuj wartości.

6. Zapisz plik *Remote. YAML* .

## <a name="run-the-console-app"></a>Uruchamianie aplikacji konsolowej

1. W oknie terminalu usługi Git za pomocą polecenia `cd` przejdź do folderu azure-cosmos-db-graph-java-getting-started.

    ```git
    cd "C:\git-samples\azure-cosmos-db-graph-java-getting-started"
    ```

2. W oknie terminala usługi Git użyj następującego polecenia, aby zainstalować wymagane pakiety języka Java.

   ```git
   mvn package
   ```

3. W oknie terminala usługi Git użyj następującego polecenia, aby uruchomić aplikację języka Java.
    
    ```git
    mvn exec:java -D exec.mainClass=GetStarted.Program
    ```

    W oknie terminalu zostaną wyświetlone wierzchołki dodawane do grafu. 
    
    Jeśli występują błędy przekroczenia limitu czasu, sprawdź, czy zaktualizowano poprawnie informacje o połączeniu w sekcji [Aktualizowanie danych połączenia](#update-your-connection-information), a także spróbuj ponownie uruchomić ostatnie polecenie. 
    
    Po zatrzymaniu programu wybierz pozycję ENTER, a następnie przejdź z powrotem do Azure Portal w przeglądarce internetowej. 

<a id="add-sample-data"></a>
## <a name="review-and-add-sample-data"></a>Przeglądanie i dodawanie przykładowych danych

Teraz możesz wrócić do Eksploratora danych i zobaczyć wierzchołki dodane do grafu, a także dodać kolejne punkty danych.

1. Na koncie Azure Cosmos DB w Azure Portal wybierz pozycję **Eksplorator danych**, rozwiń węzeł **przykład-Graph**, wybierz pozycję **Graph**, a następnie wybierz pozycję **Zastosuj filtr**. 

   :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-expanded.png" alt-text="Tworzenie nowych dokumentów w Eksploratorze danych w witrynie Azure Portal":::

2. Na liście **Wyniki** zwróć uwagę na nowych użytkowników dodanych do grafu. Wybierz użytkownika **ben** i zwróć uwagę, że jest on połączony z użytkownikiem robin. Możesz przenosić wierzchołki, przeciągając je i upuszczając, zmieniać powiększenie przy użyciu kółka myszy oraz powiększać rozmiar grafu przy użyciu podwójnej strzałki. 

   :::image type="content" source="./media/create-graph-java/azure-cosmosdb-graph-explorer-new.png" alt-text="Nowe wierzchołki grafu w Eksploratorze danych w witrynie Azure Portal":::

3. Dodajmy kilku nowych użytkowników. Wybierz pozycję **nowy wierzchołek** , aby dodać dane do grafu.

   :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-new-vertex.png" alt-text="Tworzenie nowych dokumentów w Eksploratorze danych w witrynie Azure Portal":::

4. W polu etykiety wprowadź *osobę*.

5. Wybierz pozycję **Dodaj właściwość** , aby dodać każdą z następujących właściwości. Zauważ, że możesz utworzyć unikatowe właściwości dla każdej osoby w grafie. Tylko klucz id jest wymagany.

    key|wartość|Uwagi
    ----|----|----
    identyfikator|ashley|Unikatowy identyfikator wierzchołka. Jeśli nie określono identyfikatora, zostanie on wygenerowany.
    płeć|kobieta| 
    techniczne | java | 

    > [!NOTE]
    > W tym przewodniku Szybki start tworzona jest kolekcja niepartycjonowana. Niemniej jednak, jeśli utworzysz kolekcję partycjonowaną poprzez określenie klucza partycji podczas tworzenia kolekcji, musisz uwzględnić klucz partycji jako klucz w każdym nowym wierzchołku. 

6. Wybierz pozycję **OK**. Może być konieczne rozszerzenie ekranu w celu wyświetlenia przycisku **OK** u dołu ekranu.

7. Ponownie wybierz **nowy wierzchołek** i Dodaj dodatkowego nowego użytkownika. 

8. Wprowadź etykietę *osoba*.

9. Wybierz pozycję **Dodaj właściwość** , aby dodać każdą z następujących właściwości:

    key|wartość|Uwagi
    ----|----|----
    identyfikator|rakesh|Unikatowy identyfikator wierzchołka. Jeśli nie określono identyfikatora, zostanie on wygenerowany.
    płeć|mężczyzna| 
    szkoła|MIT| 

10. Wybierz pozycję **OK**. 

11. Wybierz przycisk **Zastosuj filtr** z domyślnym `g.V()` filtrem, aby wyświetlić wszystkie wartości na grafie. Wszyscy użytkownicy będą teraz wyświetlani na liście **Wyniki**. 

    W miarę dodawania większej ilości danych można używać filtrów do ograniczania wyników. Domyślnie Eksplorator danych korzysta z zapytania `g.V()` w celu pobrania wszystkich wierzchołków grafu. Można je zmienić na inne [zapytanie o graf](tutorial-query-graph.md), takie jak`g.V().count()`, aby zwrócić liczbę wszystkich wierzchołków grafu w formacie JSON. W przypadku zmiany filtru Zmień filtr z powrotem na `g.V()` i wybierz pozycję **Zastosuj filtr** , aby ponownie wyświetlić wszystkie wyniki.

12. Teraz możesz połączyć użytkowników rakesh i ashley. Upewnij się, że na liście **wyników** została wybrana opcja **Ashley** , a następnie wybierz pozycję :::image type="content" source="./media/create-graph-java/edit-pencil-button.png" alt-text="Zmień obiekt docelowy wierzchołka w grafie"::: obok **elementów docelowych** znajdujących się w prawej dolnej części. Może być konieczne rozszerzenie okna w celu wyświetlenia przycisku.

    :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-edit-target.png" alt-text="Zmiana celu wierzchołka w grafie — Azure CosmosDB":::

13. W polu **Target** wpisz *Rakesh*, a w polu **etykieta krawędzi** wpisz *wie*, a następnie zaznacz pole wyboru.

    :::image type="content" source="./media/create-graph-java/azure-cosmosdb-data-explorer-set-target.png" alt-text="Dodawanie połączenia w Eksplorator danych — Azure CosmosDB":::

14. Teraz wybierz użytkownika **rakesh** z listy wyników, aby zobaczyć, że użytkownicy ashley i rakesh są połączeni. 

    :::image type="content" source="./media/create-graph-java/azure-cosmosdb-graph-explorer.png" alt-text="Dwa wierzchołki połączone w Eksplorator danych — Azure CosmosDB":::

Na tym kończy się część tego samouczka poświęcona tworzeniu zasobów. Możesz dodać do grafu kolejne wierzchołki, zmodyfikować istniejące wierzchołki lub zmienić zapytania. Teraz przejrzyjmy metryki udostępniane przez usługę Azure Cosmos DB, a następnie wyczyśćmy zasoby. 

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób tworzenia konta Azure Cosmos DB, tworzenia wykresu przy użyciu Eksplorator danych i uruchamiania aplikacji Java, która dodaje dane do grafu. Teraz możesz tworzyć bardziej złożone zapytania i implementować zaawansowaną logikę przechodzenia grafu za pomocą języka Gremlin. 

> [!div class="nextstepaction"]
> [Wykonywanie zapytań przy użyciu języka Gremlin](tutorial-query-graph.md)

