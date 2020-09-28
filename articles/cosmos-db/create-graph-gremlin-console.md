---
title: 'Zapytanie z interfejsem API usługi Azure Cosmos DB Gremlin przy użyciu konsoli TinkerPop Gremlin: samouczek'
description: Przewodnik Szybki start usługi Azure Cosmos DB opisujący tworzenie wierzchołków, krawędzi i zapytań za pomocą interfejsu API języka Gremlin w usłudze Azure Cosmos DB.
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: quickstart
ms.date: 07/10/2020
ms.author: jasonh
ms.openlocfilehash: ca1ca258296f5ac8f1fb7120d2965ccacf74b5d5
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/28/2020
ms.locfileid: "91409395"
---
# <a name="quickstart-create-query-and-traverse-an-azure-cosmos-db-graph-database-using-the-gremlin-console"></a>Szybki start: Tworzenie elementów, wykonywanie zapytań i przechodzenie przez grafową bazę danych usługi Azure Cosmos DB przy użyciu konsoli Gremlin

> [!div class="op_single_selector"]
> * [Konsola Gremlin](create-graph-gremlin-console.md)
> * [.NET](create-graph-dotnet.md)
> * [Java](create-graph-java.md)
> * [Node.js](create-graph-nodejs.md)
> * [Python](create-graph-python.md)
> * [PHP](create-graph-php.md)
>  

Azure Cosmos DB to rozproszona globalnie, wielomodelowa usługa bazy danych firmy Microsoft. Dzięki wykorzystaniu dystrybucji globalnej i możliwości skalowania poziomego opartego na usłudze Azure Cosmos DB, można szybko tworzyć i za pomocą zapytań badać bazy danych dokumentów, par klucz/wartość i grafów. 

Ten przewodnik Szybki Start przedstawia sposób Azure Cosmos DB tworzenia konta [interfejsu API Gremlin](graph-introduction.md) , bazy danych i grafu (kontenera) przy użyciu Azure Portal, a następnie używania [konsoli Gremlin](https://tinkerpop.apache.org/docs/current/reference/#gremlin-console) z platformy  [Apache TinkerPop](https://tinkerpop.apache.org) do pracy z danymi interfejsu API programu Gremlin. W tym samouczku utworzysz wierzchołki i krawędzie oraz wykonasz względem nich zapytania, zaktualizujesz właściwość wierzchołka, przejdziesz graf i usuniesz wierzchołek.

:::image type="content" source="./media/create-graph-gremlin-console/gremlin-console.png" alt-text="Usługa Azure Cosmos DB w konsoli Apache Gremlin":::

Konsola Gremlin jest oparta na języku Groovy/Java i działa w systemach Linux, Mac i Windows. Możesz ją pobrać z [witryny Apache TinkerPop](https://tinkerpop.apache.org/downloads.html).

## <a name="prerequisites"></a>Wymagania wstępne

Musisz dysponować subskrypcją platformy Azure, aby utworzyć konto usługi Azure Cosmos DB na potrzeby tego przewodnika Szybki start.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Należy również zainstalować [konsolę Gremlin](https://tinkerpop.apache.org/downloads.html). **Zalecana wersja to v 3.4.3** lub wcześniejsza. (Aby użyć konsoli Gremlin w systemie Windows, należy zainstalować [środowisko uruchomieniowe języka Java](https://www.oracle.com/technetwork/java/javase/overview/index.html)).

## <a name="create-a-database-account"></a>Tworzenie konta bazy danych

[!INCLUDE [cosmos-db-create-dbaccount-graph](../../includes/cosmos-db-create-dbaccount-graph.md)]

## <a name="add-a-graph"></a>Dodawanie wykresu

[!INCLUDE [cosmos-db-create-graph](../../includes/cosmos-db-create-graph.md)]

## <a name="connect-to-your-app-servicegraph"></a><a id="ConnectAppService"></a>Nawiązywanie połączenia z usługą App Service/grafem

1. Przed uruchomieniem konsoli Gremlin utwórz lub zmodyfikuj plik konfiguracji remote-secure.yaml w katalogu `apache-tinkerpop-gremlin-console-3.2.5/conf`.
2. Wypełnij ustawienia konfiguracji *host*, *port*, *username*, *password*, *connectionPool* i *serializer* zgodnie z poniższą tabelą:

    Ustawienie|Sugerowana wartość|Opis
    ---|---|---
    hosty|[*nazwa konta*. **Gremlin**. Cosmos.Azure.com]|Zobacz poniższy zrzut ekranu. Jest to wartość **identyfikatora URI Gremlin** na stronie Przegląd Azure Portal w nawiasach kwadratowych z końcowym: 443/usunięty. Uwaga: Pamiętaj, aby użyć wartości Gremlin, a **nie** identyfikatora URI kończącego się na [*account-name*. Documents.Azure.com], co prawdopodobnie spowoduje, że "host nie odpowiedział w odpowiednim czasie" podczas próby wykonania zapytań Gremlin w późniejszym czasie. 
    port|443|Ustaw wartość 443.
    nazwa użytkownika|*Twoja nazwa użytkownika*|Zasób w postaci `/dbs/<db>/colls/<coll>`, gdzie `<db>` jest nazwą bazy danych, a `<coll>` oznacza nazwę kolekcji.
    hasło|*Twój klucz podstawowy*| Zobacz drugi zrzut ekranu poniżej. To jest klucz podstawowy, który można pobrać ze strony Klucze w witrynie Azure Portal, z pola Klucz podstawowy. Aby skopiować wartość, użyj przycisku kopiowania po lewej stronie pola.
    connectionPool|{enableSsl: true}|Ustawienie puli połączeń dla protokołu TLS.
    serializer|{ className: org.apache.tinkerpop.gremlin.<br>Driver. SQL. GraphSONMessageSerializerV2d0,<br> config: { serializeResultToString: true }}|Ustaw tę wartość i usuń wszystkie podziały wiersza `\n` podczas wklejania w wartości.

   W przypadku wartości hosta skopiuj wartość **Identyfikator URI Gremlin** ze strony **Przegląd**:

   :::image type="content" source="./media/create-graph-gremlin-console/gremlin-uri.png" alt-text="Wyświetlanie i kopiowanie wartości Identyfikator URI Gremlin na stronie Przegląd w witrynie Azure Portal":::

   W przypadku wartości hasła skopiuj wartość **Klucz podstawowy** ze strony **Klucze**:

   :::image type="content" source="./media/create-graph-gremlin-console/keys.png" alt-text="Wyświetlanie i kopiowanie klucza podstawowego w witrynie Azure Portal na stronie Klucze":::

   Zdalny plik secure.yaml powinien wyglądać następująco:

   ```yaml
   hosts: [your_database_server.gremlin.cosmos.azure.com] 
   port: 443
   username: /dbs/your_database_account/colls/your_collection
   password: your_primary_key
   connectionPool: {
     enableSsl: true
   }
   serializer: { className: org.apache.tinkerpop.gremlin.driver.ser.GraphSONMessageSerializerV2d0, config: { serializeResultToString: true }}
   ```

   pamiętaj, aby wartość parametru hosta umieścić w nawiasach kwadratowych []. 

1. W terminalu uruchom `bin/gremlin.bat` lub `bin/gremlin.sh`, aby uruchomić [Konsolę Gremlin](https://tinkerpop.apache.org/docs/3.2.5/tutorials/getting-started/).

1. W terminalu uruchom `:remote connect tinkerpop.server conf/remote-secure.yaml` w celu nawiązania połączenia z usługą aplikacji.

    > [!TIP]
    > Jeśli zostanie wyświetlony błąd `No appenders could be found for logger`, upewnij się, że zaktualizowano wartość serializatora w pliku remote-secure.yaml, zgodnie z opisem w kroku 2. Jeśli konfiguracja jest poprawna, to ostrzeżenie można bezpiecznie zignorować, ponieważ nie ma to wpływu na korzystanie z konsoli programu. 

1. Następnie uruchom `:remote console`, aby przekierować wszystkie polecenia konsoli na serwer zdalny.

   > [!NOTE]
   > Jeśli nie uruchomisz polecenia `:remote console`, ale chcesz przekierować wszystkie polecenia konsoli na serwer zdalny, dodaj do polecenia prefiks `:>`, na przykład uruchom polecenie jako `:> g.V().count()`. Ten prefiks jest częścią polecenia i jest to ważne, gdy konsola Gremlin jest używana z usługą Azure Cosmos DB. Pominięcie tego prefiksu oznacza, że konsola ma wykonać polecenia lokalnie, często względem grafu w pamięci. Użycie tego prefiksu `:>` powoduje wykonanie polecenia zdalnego przez konsolę, w tym przypadku względem usługi Azure Cosmos DB (emulatora localhost lub wystąpienia platformy Azure).

Świetnie! Teraz, po zakończeniu konfigurowania, zacznijmy uruchamianie poleceń konsoli.

Wypróbujmy proste polecenie count(). Wpisz następujący tekst w wierszu polecenia konsoli:

```java
g.V().count()
```

## <a name="create-vertices-and-edges"></a>Tworzenie wierzchołków i krawędzi

Zacznijmy od dodania wierzchołków dla pięciu osób: *Thomas*, *Mary Kay*, *Robin*, *Ben* i *Jack*.

Dane wejściowe (Thomas):

```java
g.addV('person').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44).property('userid', 1).property('pk', 'pk')
```

Dane wyjściowe:

```bash
==>[id:796cdccc-2acd-4e58-a324-91d6f6f5ed6d,label:person,type:vertex,properties:[firstName:[[id:f02a749f-b67c-4016-850e-910242d68953,value:Thomas]],lastName:[[id:f5fa3126-8818-4fda-88b0-9bb55145ce5c,value:Andersen]],age:[[id:f6390f9c-e563-433e-acbf-25627628016e,value:44]],userid:[[id:796cdccc-2acd-4e58-a324-91d6f6f5ed6d|userid,value:1]]]]
```

Dane wejściowe (Mary Kay):

```java
g.addV('person').property('firstName', 'Mary Kay').property('lastName', 'Andersen').property('age', 39).property('userid', 2).property('pk', 'pk')

```

Dane wyjściowe:

```bash
==>[id:0ac9be25-a476-4a30-8da8-e79f0119ea5e,label:person,type:vertex,properties:[firstName:[[id:ea0604f8-14ee-4513-a48a-1734a1f28dc0,value:Mary Kay]],lastName:[[id:86d3bba5-fd60-4856-9396-c195ef7d7f4b,value:Andersen]],age:[[id:bc81b78d-30c4-4e03-8f40-50f72eb5f6da,value:39]],userid:[[id:0ac9be25-a476-4a30-8da8-e79f0119ea5e|userid,value:2]]]]

```

Dane wejściowe (Robin):

```java
g.addV('person').property('firstName', 'Robin').property('lastName', 'Wakefield').property('userid', 3).property('pk', 'pk')
```

Dane wyjściowe:

```bash
==>[id:8dc14d6a-8683-4a54-8d74-7eef1fb43a3e,label:person,type:vertex,properties:[firstName:[[id:ec65f078-7a43-4cbe-bc06-e50f2640dc4e,value:Robin]],lastName:[[id:a3937d07-0e88-45d3-a442-26fcdfb042ce,value:Wakefield]],userid:[[id:8dc14d6a-8683-4a54-8d74-7eef1fb43a3e|userid,value:3]]]]
```

Dane wejściowe (Ben):

```java
g.addV('person').property('firstName', 'Ben').property('lastName', 'Miller').property('userid', 4).property('pk', 'pk')

```

Dane wyjściowe:

```bash
==>[id:ee86b670-4d24-4966-9a39-30529284b66f,label:person,type:vertex,properties:[firstName:[[id:a632469b-30fc-4157-840c-b80260871e9a,value:Ben]],lastName:[[id:4a08d307-0719-47c6-84ae-1b0b06630928,value:Miller]],userid:[[id:ee86b670-4d24-4966-9a39-30529284b66f|userid,value:4]]]]
```

Dane wejściowe (Jack):

```java
g.addV('person').property('firstName', 'Jack').property('lastName', 'Connor').property('userid', 5).property('pk', 'pk')
```

Dane wyjściowe:

```bash
==>[id:4c835f2a-ea5b-43bb-9b6b-215488ad8469,label:person,type:vertex,properties:[firstName:[[id:4250824e-4b72-417f-af98-8034aa15559f,value:Jack]],lastName:[[id:44c1d5e1-a831-480a-bf94-5167d133549e,value:Connor]],userid:[[id:4c835f2a-ea5b-43bb-9b6b-215488ad8469|userid,value:5]]]]
```


Następnie dodajmy krawędzie dla relacji między tymi osobami.

Dane wejściowe (Thomas -> Mary Kay):

```java
g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Mary Kay'))
```

Dane wyjściowe:

```bash
==>[id:c12bf9fb-96a1-4cb7-a3f8-431e196e702f,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:0d1fa428-780c-49a5-bd3a-a68d96391d5c,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

Dane wejściowe (Thomas -> Robin):

```java
g.V().hasLabel('person').has('firstName', 'Thomas').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Robin'))
```

Dane wyjściowe:

```bash
==>[id:58319bdd-1d3e-4f17-a106-0ddf18719d15,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:3e324073-ccfc-4ae1-8675-d450858ca116,outV:1ce821c6-aa3d-4170-a0b7-d14d2a4d18c3]
```

Dane wejściowe (Robin -> Ben):

```java
g.V().hasLabel('person').has('firstName', 'Robin').addE('knows').to(g.V().hasLabel('person').has('firstName', 'Ben'))
```

Dane wyjściowe:

```bash
==>[id:889c4d3c-549e-4d35-bc21-a3d1bfa11e00,label:knows,type:edge,inVLabel:person,outVLabel:person,inV:40fd641d-546e-412a-abcc-58fe53891aab,outV:3e324073-ccfc-4ae1-8675-d450858ca116]
```

## <a name="update-a-vertex"></a>Aktualizowanie wierzchołka

Zaktualizujmy wierzchołek *Thomas*, podając nowy wiek: *45*.

Dane wejściowe:
```java
g.V().hasLabel('person').has('firstName', 'Thomas').property('age', 45)
```
Dane wyjściowe:

```bash
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

## <a name="query-your-graph"></a>Wykonywanie zapytania względem grafu

Teraz uruchommy różne zapytania względem grafu.

Najpierw wypróbujmy zapytanie z filtrem, aby zwrócić tylko te osoby, które mają więcej niż 40 lat.

Dane wejściowe (zapytanie filtru):

```java
g.V().hasLabel('person').has('age', gt(40))
```

Dane wyjściowe:

```bash
==>[id:ae36f938-210e-445a-92df-519f2b64c8ec,label:person,type:vertex,properties:[firstName:[[id:872090b6-6a77-456a-9a55-a59141d4ebc2,value:Thomas]],lastName:[[id:7ee7a39a-a414-4127-89b4-870bc4ef99f3,value:Andersen]],age:[[id:a2a75d5a-ae70-4095-806d-a35abcbfe71d,value:45]]]]
```

Następnie wyświetlmy imiona osób, które mają więcej niż 40 lat.

Dane wejściowe (zapytanie filtru i projekcji):

```java
g.V().hasLabel('person').has('age', gt(40)).values('firstName')
```

Dane wyjściowe:

```bash
==>Thomas
```

## <a name="traverse-your-graph"></a>Przechodzenie grafu

Przejdźmy graf w celu zwrócenia wszystkich znajomych Thomasa.

Dane wejściowe (znajomi Thomasa):

```java
g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person')
```

Dane wyjściowe: 

```bash
==>[id:f04bc00b-cb56-46c4-a3bb-a5870c42f7ff,label:person,type:vertex,properties:[firstName:[[id:14feedec-b070-444e-b544-62be15c7167c,value:Mary Kay]],lastName:[[id:107ab421-7208-45d4-b969-bbc54481992a,value:Andersen]],age:[[id:4b08d6e4-58f5-45df-8e69-6b790b692e0a,value:39]]]]
==>[id:91605c63-4988-4b60-9a30-5144719ae326,label:person,type:vertex,properties:[firstName:[[id:f760e0e6-652a-481a-92b0-1767d9bf372e,value:Robin]],lastName:[[id:352a4caa-bad6-47e3-a7dc-90ff342cf870,value:Wakefield]]]]
```

Następnie uzyskajmy kolejną warstwę wierzchołków. Przejdźmy graf w celu zwrócenia wszystkich osób będących znajomymi znajomych Thomasa.

Dane wejściowe (znajomi znajomych Thomasa):

```java
g.V().hasLabel('person').has('firstName', 'Thomas').outE('knows').inV().hasLabel('person').outE('knows').inV().hasLabel('person')
```
Dane wyjściowe:

```bash
==>[id:a801a0cb-ee85-44ee-a502-271685ef212e,label:person,type:vertex,properties:[firstName:[[id:b9489902-d29a-4673-8c09-c2b3fe7f8b94,value:Ben]],lastName:[[id:e084f933-9a4b-4dbc-8273-f0171265cf1d,value:Miller]]]]
```

## <a name="drop-a-vertex"></a>Usuwanie wierzchołka

Usuńmy teraz wierzchołek z bazy danych grafu.

Dane wejściowe (usunięcie wierzchołka Jacka):

```java
g.V().hasLabel('person').has('firstName', 'Jack').drop()
```

## <a name="clear-your-graph"></a>Czyszczenie grafu

Na koniec usuńmy z bazy danych wszystkie wierzchołki i krawędzie.

Dane wejściowe:

```java
g.E().drop()
g.V().drop()
```

Gratulacje! Pomyślnie ukończono samouczek interfejsu API języka Gremlin w usłudze Azure Cosmos DB!

## <a name="review-slas-in-the-azure-portal"></a>Przeglądanie umów SLA w witrynie Azure Portal

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wyjaśniono, jak utworzyć konto usługi Azure Cosmos DB, jak utworzyć graf za pomocą Eksploratora danych oraz jak utworzyć wierzchołki i krawędzie, a także jak przejść graf za pomocą konsoli Gremlin. Teraz możesz tworzyć bardziej złożone zapytania i implementować zaawansowaną logikę przechodzenia grafu za pomocą języka Gremlin. 

> [!div class="nextstepaction"]
> [Wykonywanie zapytań przy użyciu języka Gremlin](tutorial-query-graph.md)
