---
title: 'Szybki Start: Tworzenie indeksu wyszukiwania w języku Java przy użyciu interfejsów API REST'
titleSuffix: Azure Cognitive Search
description: W tym przewodniku szybki start dla języka Java dowiesz się, jak utworzyć indeks, załadować dane i uruchamiać zapytania przy użyciu interfejsów API REST usługi Azure Wyszukiwanie poznawcze.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: java
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 09/25/2020
ms.custom: devx-track-java
ms.openlocfilehash: 6be511029221e1f7bd1e58ad111503a43ee157fb
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91400659"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-in-java-using-rest-apis"></a>Szybki Start: Tworzenie indeksu Wyszukiwanie poznawcze platformy Azure w języku Java przy użyciu interfejsów API REST
> [!div class="op_single_selector"]
> * [JavaScript](search-get-started-nodejs.md)
> * [C#](search-get-started-dotnet.md)
> * [Java](search-get-started-java.md)
> * [Portal](search-get-started-portal.md)
> * [Program PowerShell](./search-get-started-powershell.md)
> * [Python](search-get-started-python.md)
> * [Postman](search-get-started-postman.md)

Tworzenie aplikacji konsolowej Java, która tworzy, ładuje i wysyła zapytanie do indeksu wyszukiwania za pomocą [IntelliJ](https://www.jetbrains.com/idea/), [Java 11 SDK](/java/azure/jdk/?view=azure-java-stable)i [interfejsu API REST platformy Azure wyszukiwanie poznawcze](/rest/api/searchservice/). Ten artykuł zawiera instrukcje krok po kroku dotyczące tworzenia aplikacji. Alternatywnie można [pobrać i uruchomić kompletną aplikację](/samples/azure-samples/azure-search-java-samples/java-sample-quickstart/).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Do utworzenia i przetestowania tego przewodnika Szybki Start użyto następującego oprogramowania i usług:

+ [IntelliJ IDEA](https://www.jetbrains.com/idea/)

+ [Zestaw SDK języka Java 11](/java/azure/jdk/?view=azure-java-stable)

+ [Utwórz usługę Azure wyszukiwanie poznawcze](search-create-service-portal.md) lub [Znajdź istniejącą usługę](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) w ramach bieżącej subskrypcji. Możesz użyć bezpłatnej usługi dla tego przewodnika Szybki Start.

<a name="get-service-info"></a>

## <a name="get-a-key-and-url"></a>Pobierz klucz i adres URL

Wywołania usługi wymagają punktu końcowego adresu URL i klucza dostępu dla każdego żądania. Usługa wyszukiwania jest tworzona razem z usługą, więc jeśli do subskrypcji dodano Wyszukiwanie poznawcze platformy Azure, wykonaj następujące kroki, aby uzyskać niezbędne informacje:

1. [Zaloguj się do Azure Portal](https://portal.azure.com/)i na stronie **Przegląd** usługi wyszukiwania Uzyskaj adres URL. Przykładowy punkt końcowy może wyglądać podobnie jak `https://mydemo.search.windows.net`.

2. W obszarze **Ustawienia**  >  **klucze**Uzyskaj klucz administratora dla pełnych praw do usługi. Istnieją dwa wymienne klucze administratora zapewniające ciągłość działania w przypadku, gdy trzeba ją wycofać. W przypadku żądań dotyczących dodawania, modyfikowania i usuwania obiektów można użyć klucza podstawowego lub pomocniczego.

   Utwórz również klucz zapytania. Najlepszym rozwiązaniem jest wydawanie żądań zapytań z dostępem tylko do odczytu.

:::image type="content" source="media/search-get-started-nodejs/service-name-and-keys.png" alt-text="Pobieranie nazwy usługi i administratora oraz kluczy zapytań" border="false":::

Każde żądanie wysyłane do usługi wymaga klucza interfejsu API. Prawidłowy klucz ustanawia relację zaufania dla danego żądania między aplikacją wysyłającą żądanie i usługą, która je obsługuje.

## <a name="set-up-your-environment"></a>Konfigurowanie środowiska

Zacznij od otwarcia IntelliJ POMYSŁu i skonfigurowania nowego projektu.

### <a name="create-the-project"></a>Tworzenie projektu

1. Otwórz pomysł IntelliJ i wybierz pozycję **Utwórz nowy projekt**.
1. Wybierz pozycję **Maven**.
1. Na liście **zestaw SDK projektu** wybierz zestaw SDK języka Java 11.

    :::image type="content" source="media/search-get-started-java/java-quickstart-create-new-maven-project.png" alt-text="Tworzenie projektu Maven" border="false":::

1. Dla **identyfikatora GroupID** i **ArtifactId**wprowadź `AzureSearchQuickstart` .
1. Zaakceptuj pozostałe wartości domyślne, aby otworzyć projekt.

### <a name="specify-maven-dependencies"></a>Określ zależności Maven

1. Wybierz **File**pozycję  >  **Ustawienia**pliku.
1. W oknie **Ustawienia** wybierz pozycję **kompilacja, wykonanie,**  >  **narzędzia kompilacji**wdrożenia  >  **Maven**  >  **Importowanie**.
1. Zaznacz pole wyboru  **Importuj projekty Maven automatycznie** , a następnie kliknij przycisk **OK** , aby zamknąć okno. Wtyczki Maven i inne zależności będą teraz automatycznie synchronizowane podczas aktualizacji pliku pom.xml w następnym kroku.

    :::image type="content" source="media/search-get-started-java/java-quickstart-settings-import-maven-auto.png" alt-text="Maven opcji importowania w ustawieniach IntelliJ" border="false":::

1. Otwórz plik pom.xml i Zastąp jego zawartość następującymi szczegółami konfiguracji Maven. Obejmują one odwołania do [wtyczki exec Maven](https://www.mojohaus.org/exec-maven-plugin/) oraz interfejsu [API interfejsu JSON](https://javadoc.io/doc/org.glassfish/javax.json/1.0.2)

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <project xmlns="http://maven.apache.org/POM/4.0.0"
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
        <modelVersion>4.0.0</modelVersion>
    
        <groupId>AzureSearchQuickstart</groupId>
        <artifactId>AzureSearchQuickstart</artifactId>
        <version>1.0-SNAPSHOT</version>
        <build>
            <sourceDirectory>src</sourceDirectory>
            <plugins>
                <plugin>
                    <artifactId>maven-compiler-plugin</artifactId>
                    <version>3.1</version>
                    <configuration>
                        <source>11</source>
                        <target>11</target>
                    </configuration>
                </plugin>
                <plugin>
                    <groupId>org.codehaus.mojo</groupId>
                    <artifactId>exec-maven-plugin</artifactId>
                    <version>1.6.0</version>
                    <executions>
                        <execution>
                            <goals>
                                <goal>exec</goal>
                            </goals>
                        </execution>
                    </executions>
                    <configuration>
                        <mainClass>main.java.app.App</mainClass>
                        <cleanupDaemonThreads>false</cleanupDaemonThreads>
                    </configuration>
                </plugin>
            </plugins>
        </build>
        <dependencies>
            <dependency>
                <groupId>org.glassfish</groupId>
                <artifactId>javax.json</artifactId>
                <version>1.0.2</version>
            </dependency>
        </dependencies>   
    </project>
    ```

### <a name="set-up-the-project-structure"></a>Skonfiguruj strukturę projektu

1. Wybierz **File**pozycję  >  **Struktura projektu**pliku.
1. Wybierz pozycję **moduły**i rozwiń drzewo źródłowe, aby uzyskać dostęp do zawartości `src`  >   `main` folderu.
1. W `src`  >   `main`  >  `java` folderze Dodaj `app` `service` foldery i. Aby to zrobić, wybierz `java` folder, naciśnij klawisze ALT + INSERT, a następnie wprowadź nazwę folderu.
1. W `src`  >   `main`  > `resources` folderze Dodaj `app` `service` foldery i.

    Gdy skończysz, drzewo projektu powinno wyglądać jak na poniższej ilustracji.

    :::image type="content" source="media/search-get-started-java/java-quickstart-basic-code-tree.png" alt-text="Struktura katalogu projektu" border="false":::

1. Kliknij przycisk **OK**, aby zamknąć okno.

### <a name="add-azure-cognitive-search-service-information"></a>Dodawanie informacji o usłudze Wyszukiwanie poznawcze platformy Azure

1. W oknie **projekt** rozwiń drzewo źródłowe, aby uzyskać dostęp do `src`  >   `main`  > `resources`  >  `app` folderu, a następnie Dodaj `config.properties` plik. Aby to zrobić, wybierz `app` folder, naciśnij klawisze ALT + INSERT, wybierz pozycję **plik**, a następnie wprowadź nazwę pliku.

1. Skopiuj następujące ustawienia do nowego pliku i zastąp je `<YOUR-SEARCH-SERVICE-NAME>` `<YOUR-ADMIN-KEY>` `<YOUR-QUERY-KEY>` nazwą i kluczami usługi. Jeśli punkt końcowy usługi to `https://mydemo.search.windows.net` , nazwa usługi to "Moja demonstracja".

    ```java
        SearchServiceName=<YOUR-SEARCH-SERVICE-NAME>
        SearchServiceAdminKey=<YOUR-ADMIN-KEY>
        SearchServiceQueryKey=<YOUR-QUERY-KEY>
        IndexName=hotels-quickstart
        ApiVersion=2020-06-30
    ```

### <a name="add-the-main-method"></a>Dodaj metodę Main

1. W `src`  >   `main`  >  `java`  >  `app` folderze Dodaj `App` klasę. Aby to zrobić, wybierz `app` folder, naciśnij klawisze ALT + INSERT, wybierz opcję **Klasa Java**, a następnie wprowadź nazwę klasy.
1. Otwórz `App` klasę i Zastąp zawartość następującym kodem. Ten kod zawiera `main` metodę. 

    Kod niekomentowany odczytuje parametry usługi wyszukiwania i używa ich do utworzenia wystąpienia klienta usługi wyszukiwania. Kod klienta usługi Search zostanie dodany w następnej sekcji.

    Kod z komentarzem w tej klasie zostanie odoznaczony jako komentarz w dalszej części tego przewodnika Szybki Start.

    ```java
    package main.java.app;
    
    import main.java.service.SearchServiceClient;
    import java.io.IOException;
    import java.util.Properties;
    
    public class App {
    
        private static Properties loadPropertiesFromResource(String resourcePath) throws IOException {
            var inputStream = App.class.getResourceAsStream(resourcePath);
            var configProperties = new Properties();
            configProperties.load(inputStream);
            return configProperties;
        }
    
        public static void main(String[] args) {
            try {
                var config = loadPropertiesFromResource("/app/config.properties");
                var client = new SearchServiceClient(
                        config.getProperty("SearchServiceName"),
                        config.getProperty("SearchServiceAdminKey"),
                        config.getProperty("SearchServiceQueryKey"),
                        config.getProperty("ApiVersion"),
                        config.getProperty("IndexName")
                );
    
    
    //Uncomment the next 3 lines in the 1 - Create Index section of the quickstart
    //            if(client.indexExists()){ client.deleteIndex();}
    //            client.createIndex("/service/index.json");
    //            Thread.sleep(1000L); // wait a second to create the index
    
    //Uncomment the next 2 lines in the 2 - Load Documents section of the quickstart
    //            client.uploadDocuments("/service/hotels.json");
    //            Thread.sleep(2000L); // wait 2 seconds for data to upload
    
    //Uncomment the following 5 search queries in the 3 - Search an index section of the quickstart
    //            // Query 1
    //            client.logMessage("\n*QUERY 1****************************************************************");
    //            client.logMessage("Search for: Atlanta'");
    //            client.logMessage("Return: All fields'");
    //            client.searchPlus("Atlanta");
    //
    //            // Query 2
    //            client.logMessage("\n*QUERY 2****************************************************************");
    //            client.logMessage("Search for: Atlanta");
    //            client.logMessage("Return: HotelName, Tags, Address");
    //            SearchServiceClient.SearchOptions options2 = client.createSearchOptions();
    //            options2.select = "HotelName,Tags,Address";
    //            client.searchPlus("Atlanta", options2);
    //
    //            //Query 3
    //            client.logMessage("\n*QUERY 3****************************************************************");
    //            client.logMessage("Search for: wifi & restaurant");
    //            client.logMessage("Return: HotelName, Description, Tags");
    //            SearchServiceClient.SearchOptions options3 = client.createSearchOptions();
    //            options3.select = "HotelName,Description,Tags";
    //            client.searchPlus("wifi,restaurant", options3);
    //
    //            // Query 4 -filtered query
    //            client.logMessage("\n*QUERY 4****************************************************************");
    //            client.logMessage("Search for: all");
    //            client.logMessage("Filter: Ratings greater than 4");
    //            client.logMessage("Return: HotelName, Rating");
    //            SearchServiceClient.SearchOptions options4 = client.createSearchOptions();
    //            options4.filter="Rating%20gt%204";
    //            options4.select = "HotelName,Rating";
    //            client.searchPlus("*",options4);
    //
    //            // Query 5 - top 2 results, ordered by
    //            client.logMessage("\n*QUERY 5****************************************************************");
    //            client.logMessage("Search for: boutique");
    //            client.logMessage("Get: Top 2 results");
    //            client.logMessage("Order by: Rating in descending order");
    //            client.logMessage("Return: HotelId, HotelName, Category, Rating");
    //            SearchServiceClient.SearchOptions options5 = client.createSearchOptions();
    //            options5.top=2;
    //            options5.orderby = "Rating%20desc";
    //            options5.select = "HotelId,HotelName,Category,Rating";
    //            client.searchPlus("boutique", options5);
    
            } catch (Exception e) {
                System.err.println("Exception:" + e.getMessage());
                e.printStackTrace();
            }
        }
    }
    ```

### <a name="add-the-http-operations"></a>Dodawanie operacji HTTP

1. W `src`  >   `main`  >  `java`  >  `service` folderze Dodaj `SearchServiceClient` klasę. Aby to zrobić, wybierz `service` folder, naciśnij klawisze ALT + INSERT, wybierz opcję **Klasa Java**, a następnie wprowadź nazwę klasy.
1. Otwórz `SearchServiceClient` klasę i Zastąp zawartość następującym kodem. Ten kod zawiera operacje HTTP wymagane do korzystania z interfejsu API REST usługi Azure Wyszukiwanie poznawcze. Dodatkowe metody tworzenia indeksu, przekazywania dokumentów i wysyłania zapytań do indeksu zostaną dodane w dalszej części.

    ```java
    package main.java.service;

    import javax.json.Json;
    import javax.net.ssl.HttpsURLConnection;
    import java.io.IOException;
    import java.io.StringReader;
    import java.net.HttpURLConnection;
    import java.net.URI;
    import java.net.http.HttpClient;
    import java.net.http.HttpRequest;
    import java.net.http.HttpResponse;
    import java.nio.charset.StandardCharsets;
    import java.util.Formatter;
    import java.util.function.Consumer;
    
        /* This class is responsible for implementing HTTP operations for creating the index, uploading documents and searching the data*/
        public class SearchServiceClient {
            private final String _adminKey;
            private final String _queryKey;
            private final String _apiVersion;
            private final String _serviceName;
            private final String _indexName;
            private final static HttpClient client = HttpClient.newHttpClient();
    
        public SearchServiceClient(String serviceName, String adminKey, String queryKey, String apiVersion, String indexName) {
            this._serviceName = serviceName;
            this._adminKey = adminKey;
            this._queryKey = queryKey;
            this._apiVersion = apiVersion;
            this._indexName = indexName;
        }

        private static HttpResponse<String> sendRequest(HttpRequest request) throws IOException, InterruptedException {
            logMessage(String.format("%s: %s", request.method(), request.uri()));
            return client.send(request, HttpResponse.BodyHandlers.ofString());
        }

        private static URI buildURI(Consumer<Formatter> fmtFn)
                {
                    Formatter strFormatter = new Formatter();
                    fmtFn.accept(strFormatter);
                    String url = strFormatter.out().toString();
                    strFormatter.close();
                    return URI.create(url);
        }
    
        public static void logMessage(String message) {
            System.out.println(message);
        }
    
        public static boolean isSuccessResponse(HttpResponse<String> response) {
            try {
                int responseCode = response.statusCode();
    
                logMessage("\n Response code = " + responseCode);
    
                if (responseCode == HttpURLConnection.HTTP_OK || responseCode == HttpURLConnection.HTTP_ACCEPTED
                        || responseCode == HttpURLConnection.HTTP_NO_CONTENT || responseCode == HttpsURLConnection.HTTP_CREATED) {
                    return true;
                }
    
                // We got an error
                var msg = response.body();
                if (msg != null) {
                    logMessage(String.format("\n MESSAGE: %s", msg));
                }
    
            } catch (Exception e) {
                e.printStackTrace();
            }
    
            return false;
        }
    
        public static HttpRequest httpRequest(URI uri, String key, String method, String contents) {
            contents = contents == null ? "" : contents;
            var builder = HttpRequest.newBuilder();
            builder.uri(uri);
            builder.setHeader("content-type", "application/json");
            builder.setHeader("api-key", key);
    
            switch (method) {
                case "GET":
                    builder = builder.GET();
                    break;
                case "HEAD":
                    builder = builder.GET();
                    break;
                case "DELETE":
                    builder = builder.DELETE();
                    break;
                case "PUT":
                    builder = builder.PUT(HttpRequest.BodyPublishers.ofString(contents));
                    break;
                case "POST":
                    builder = builder.POST(HttpRequest.BodyPublishers.ofString(contents));
                    break;
                default:
                    throw new IllegalArgumentException(String.format("Can't create request for method '%s'", method));
            }
            return builder.build();
        }
    }
    
    ```

### <a name="build-the-project"></a>Kompilowanie projektu

1. Sprawdź, czy projekt ma następującą strukturę.

    :::image type="content" source="media/search-get-started-java/java-quickstart-basic-code-tree-plus-classes.png" alt-text="Struktura katalogów i klasy projektu" border="false":::

1. Otwórz okno narzędzia **Maven** i wykonaj ten Maven cel: `verify exec:java` 
 :::image type="content" source="media/search-get-started-java/java-quickstart-execute-maven-goal.png" alt-text="Wykonaj Maven cel: Verify exec: Java" border="false":::

Po zakończeniu przetwarzania Wyszukaj komunikat o POWODZENIU kompilacji, po którym następuje kod zakończenia równy zero (0).

## <a name="1---create-index"></a>1 — Tworzenie indeksu

Definicja indeksu hoteli zawiera proste pola i jedno pole złożone. Przykładami prostego pola są "Hotelname" lub "Description". Pole "Address" jest polem złożonym, ponieważ ma podpola, takie jak "ulica" i "miasto". W tym przewodniku szybki start Definicja indeksu jest określana za pomocą formatu JSON.

1. W oknie **projekt** rozwiń drzewo źródłowe, aby uzyskać dostęp do `src`  >   `main`  > `resources`  >  `service` folderu, a następnie Dodaj `index.json` plik. Aby to zrobić, wybierz `app` folder, naciśnij klawisze ALT + INSERT, wybierz pozycję **plik**, a następnie wprowadź nazwę pliku.

1. Otwórz `index.json` plik i Wstaw następującą definicję indeksu.

    ```json
    {
      "name": "hotels-quickstart",
      "fields": [
        {
          "name": "HotelId",
          "type": "Edm.String",
          "key": true,
          "filterable": true
        },
        {
          "name": "HotelName",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": true,
          "facetable": false
        },
        {
          "name": "Description",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": false,
          "facetable": false,
          "analyzer": "en.lucene"
        },
        {
          "name": "Description_fr",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "sortable": false,
          "facetable": false,
          "analyzer": "fr.lucene"
        },
        {
          "name": "Category",
          "type": "Edm.String",
          "searchable": true,
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Tags",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "filterable": true,
          "sortable": false,
          "facetable": true
        },
        {
          "name": "ParkingIncluded",
          "type": "Edm.Boolean",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "LastRenovationDate",
          "type": "Edm.DateTimeOffset",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Rating",
          "type": "Edm.Double",
          "filterable": true,
          "sortable": true,
          "facetable": true
        },
        {
          "name": "Address",
          "type": "Edm.ComplexType",
          "fields": [
            {
              "name": "StreetAddress",
              "type": "Edm.String",
              "filterable": false,
              "sortable": false,
              "facetable": false,
              "searchable": true
            },
            {
              "name": "City",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "StateProvince",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "PostalCode",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            },
            {
              "name": "Country",
              "type": "Edm.String",
              "searchable": true,
              "filterable": true,
              "sortable": true,
              "facetable": true
            }
          ]
        }
      ]
    }
    ```

    Nazwa indeksu będzie "Hotele-Szybki Start". Atrybuty pól indeksu określają, jak indeksowane dane mogą być przeszukiwane w aplikacji. Na przykład, `IsSearchable` atrybut musi być przypisany do każdego pola, które powinny być uwzględnione w wyszukiwaniu pełnotekstowym. Aby dowiedzieć się więcej na temat atrybutów, zobacz [kolekcje pól i atrybuty pól](search-what-is-an-index.md#fields-collection).
    
    `Description`Pole w tym indeksie używa opcjonalnej `analyzer` właściwości do przesłania domyślnego analizatora języka Lucene. W `Description_fr` polu jest używany francuski Analizator Lucene, `fr.lucene` ponieważ jest on przechowywany w języku francuskim. `Description`Używa opcjonalnego narzędzia Microsoft Language Analyzer en. Lucene. Aby dowiedzieć się więcej na temat analizatorów, zobacz [analizatory do przetwarzania tekstu na platformie Azure wyszukiwanie poznawcze](search-analyzers.md).

1. Dodaj poniższy kod do klasy `SearchServiceClient`. Te metody kompilują adresy URL usługi Azure Wyszukiwanie poznawcze REST, które tworzą i usuwają indeks, i określają, czy indeks istnieje. Metody również tworzą żądanie HTTP.

    ```java
    public boolean indexExists() throws IOException, InterruptedException {
        logMessage("\n Checking if index exists...");
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s/docs?api-version=%s&search=*",
                _serviceName,_indexName,_apiVersion));
        var request = httpRequest(uri, _adminKey, "HEAD", "");
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    
    public boolean deleteIndex() throws IOException, InterruptedException {
        logMessage("\n Deleting index...");
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        var request = httpRequest(uri, _adminKey, "DELETE", "*");
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    
    
    public boolean createIndex(String indexDefinitionFile) throws IOException, InterruptedException {
        logMessage("\n Creating index...");
        //Build the search service URL
        var uri = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        //Read in index definition file
        var inputStream = SearchServiceClient.class.getResourceAsStream(indexDefinitionFile);
        var indexDef = new String(inputStream.readAllBytes(), StandardCharsets.UTF_8);
        //Send HTTP PUT request to create the index in the search service
        var request = httpRequest(uri, _adminKey, "PUT", indexDef);
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    ```

1. Usuń komentarz z poniższego kodu w `App` klasie. Ten kod usuwa indeks "Hotele-szybki", jeśli istnieje, i tworzy nowy indeks na podstawie definicji indeksu w pliku "index.json". 

    Po żądaniu utworzenia indeksu zostanie wstawiona druga przerwa. To wstrzymanie zapewnia, że indeks zostanie utworzony przed przekazaniem dokumentów.

    ```java
        if (client.indexExists()) { client.deleteIndex();}
          client.createIndex("/service/index.json");
          Thread.sleep(1000L); // wait a second to create the index
    ```

1. Otwórz okno narzędzia **Maven** i wykonaj ten Maven cel: `verify exec:java`

    Po uruchomieniu kodu Poszukaj komunikatu "Tworzenie indeksu", po którym następuje kod odpowiedzi 201. Ten kod odpowiedzi potwierdza, że indeks został utworzony. Przebieg powinien kończyć się komunikatem o POWODZENIU kompilacji i kodem zakończenia równym zero (0).
    
## <a name="2---load-documents"></a>2 — ładowanie dokumentów

1. W oknie **projekt** rozwiń drzewo źródłowe, aby uzyskać dostęp do `src`  >   `main`  > `resources`  >  `service` folderu, a następnie Dodaj `hotels.json` plik. Aby to zrobić, wybierz `app` folder, naciśnij klawisze ALT + INSERT, wybierz pozycję  **plik**, a następnie wprowadź nazwę pliku.
1. Wstaw następujące dokumenty hotelu do pliku.

    ```json
    {
      "value": [
        {
          "@search.action": "upload",
          "HotelId": "1",
          "HotelName": "Secret Point Motel",
          "Description": "The hotel is ideally located on the main commercial artery of the city in the heart of New York. A few minutes away is Time's Square and the historic centre of the city, as well as other places of interest that make New York one of America's most attractive and cosmopolitan cities.",
          "Description_fr": "L'hôtel est idéalement situé sur la principale artère commerciale de la ville en plein cœur de New York. A quelques minutes se trouve la place du temps et le centre historique de la ville, ainsi que d'autres lieux d'intérêt qui font de New York l'une des villes les plus attractives et cosmopolites de l'Amérique.",
          "Category": "Boutique",
          "Tags": [ "pool", "air conditioning", "concierge" ],
          "ParkingIncluded": "false",
          "LastRenovationDate": "1970-01-18T00:00:00Z",
          "Rating": 3.60,
          "Address": {
            "StreetAddress": "677 5th Ave",
            "City": "New York",
            "StateProvince": "NY",
            "PostalCode": "10022",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "2",
          "HotelName": "Twin Dome Motel",
          "Description": "The hotel is situated in a  nineteenth century plaza, which has been expanded and renovated to the highest architectural standards to create a modern, functional and first-class hotel in which art and unique historical elements coexist with the most modern comforts.",
          "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
          "Category": "Boutique",
          "Tags": [ "pool", "free wifi", "concierge" ],
          "ParkingIncluded": "false",
          "LastRenovationDate": "1979-02-18T00:00:00Z",
          "Rating": 3.60,
          "Address": {
            "StreetAddress": "140 University Town Center Dr",
            "City": "Sarasota",
            "StateProvince": "FL",
            "PostalCode": "34243",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "3",
          "HotelName": "Triple Landscape Hotel",
          "Description": "The Hotel stands out for its gastronomic excellence under the management of William Dough, who advises on and oversees all of the Hotel’s restaurant services.",
          "Description_fr": "L'hôtel est situé dans une place du XIXe siècle, qui a été agrandie et rénovée aux plus hautes normes architecturales pour créer un hôtel moderne, fonctionnel et de première classe dans lequel l'art et les éléments historiques uniques coexistent avec le confort le plus moderne.",
          "Category": "Resort and Spa",
          "Tags": [ "air conditioning", "bar", "continental breakfast" ],
          "ParkingIncluded": "true",
          "LastRenovationDate": "2015-09-20T00:00:00Z",
          "Rating": 4.80,
          "Address": {
            "StreetAddress": "3393 Peachtree Rd",
            "City": "Atlanta",
            "StateProvince": "GA",
            "PostalCode": "30326",
            "Country": "USA"
          }
        },
        {
          "@search.action": "upload",
          "HotelId": "4",
          "HotelName": "Sublime Cliff Hotel",
          "Description": "Sublime Cliff Hotel is located in the heart of the historic center of Sublime in an extremely vibrant and lively area within short walking distance to the sites and landmarks of the city and is surrounded by the extraordinary beauty of churches, buildings, shops and monuments. Sublime Cliff is part of a lovingly restored 1800 palace.",
          "Description_fr": "Le sublime Cliff Hotel est situé au coeur du centre historique de sublime dans un quartier extrêmement animé et vivant, à courte distance de marche des sites et monuments de la ville et est entouré par l'extraordinaire beauté des églises, des bâtiments, des commerces et Monuments. Sublime Cliff fait partie d'un Palace 1800 restauré avec amour.",
          "Category": "Boutique",
          "Tags": [ "concierge", "view", "24-hour front desk service" ],
          "ParkingIncluded": "true",
          "LastRenovationDate": "1960-02-06T00:00:00Z",
          "Rating": 4.60,
          "Address": {
            "StreetAddress": "7400 San Pedro Ave",
            "City": "San Antonio",
            "StateProvince": "TX",
            "PostalCode": "78216",
            "Country": "USA"
          }
        }
      ]
    }
    ```

1. Wstaw następujący kod do `SearchServiceClient` klasy. Ten kod kompiluje adres URL usługi REST w celu przekazania dokumentów hotelu do indeksu, a następnie wysyła żądanie HTTP POST.

    ```java
    public boolean uploadDocuments(String documentsFile) throws IOException, InterruptedException {
        logMessage("\n Uploading documents...");
        //Build the search service URL
        var endpoint = buildURI(strFormatter -> strFormatter.format(
                "https://%s.search.windows.net/indexes/%s/docs/index?api-version=%s",
                _serviceName,_indexName,_apiVersion));
        //Read in the data to index
        var inputStream = SearchServiceClient.class.getResourceAsStream(documentsFile);
        var documents = new String(inputStream.readAllBytes(), StandardCharsets.UTF_8);
        //Send HTTP POST request to upload and index the data
        var request = httpRequest(endpoint, _adminKey, "POST", documents);
        var response = sendRequest(request);
        return isSuccessResponse(response);
    }
    ```

1. Usuń komentarz z poniższego kodu w `App` klasie. Ten kod przekazuje dokumenty z "hotels.json" do indeksu.

    ```java
    client.uploadDocuments("/service/hotels.json");
    Thread.sleep(2000L); // wait 2 seconds for data to upload
    ```

    Po wysłaniu żądania załadowania dokumentu zostanie wstawiona dwusekundowa przerwanie, aby upewnić się, że proces ładujący dokument został ukończony przed wykonaniem zapytania względem indeksu.

1. Otwórz okno narzędzia **Maven** i wykonaj ten Maven cel: `verify exec:java`

    Ze względu na to, że w poprzednim kroku został utworzony indeks "Hotele — Szybki Start", kod zostanie teraz usunięty i ponownie utworzony przed załadowaniem dokumentów hotelowych.

    Po uruchomieniu kodu Poszukaj komunikatu "przekazywanie dokumentów", po którym następuje kod odpowiedzi 200. Ten kod odpowiedzi potwierdza, że dokumenty zostały przekazane do indeksu. Przebieg powinien kończyć się komunikatem o POWODZENIU kompilacji i kodem zakończenia równym zero (0).

## <a name="3---search-an-index"></a>3 — Przeszukiwanie indeksu

Teraz, gdy załadowałeś dokumenty hoteli, możesz utworzyć zapytania wyszukiwania umożliwiające dostęp do danych hoteli.

1. Dodaj poniższy kod do klasy `SearchServiceClient`. Ten kod kompiluje adresy URL usługi Azure Wyszukiwanie poznawcze REST, aby przeszukać indeksowane dane i wydrukował wyniki wyszukiwania.

    `SearchOptions`Klasa i `createSearchOptions` Metoda pozwalają określić podzestaw dostępnych opcji zapytania interfejsu API REST platformy Azure wyszukiwanie poznawcze. Aby uzyskać więcej informacji na temat opcji zapytania interfejsu API REST, zobacz [Wyszukiwanie dokumentów (Azure wyszukiwanie POZNAWCZE REST API)](/rest/api/searchservice/search-documents).

    `SearchPlus`Metoda tworzy adres URL zapytania wyszukiwania, wykonuje żądanie wyszukiwania, a następnie drukuje wyniki do konsoli. 

    ```java
    public SearchOptions createSearchOptions() { return new SearchOptions();}

    //Defines available search parameters that can be set
    public static class SearchOptions {

        public String select = "";
        public String filter = "";
        public int top = 0;
        public String orderby= "";
    }

    //Concatenates search parameters to append to the search request
    private String createOptionsString(SearchOptions options)
    {
        String optionsString = "";
        if (options != null) {
            if (options.select != "")
                optionsString = optionsString + "&$select=" + options.select;
            if (options.filter != "")
                optionsString = optionsString + "&$filter=" + options.filter;
            if (options.top != 0)
                optionsString = optionsString + "&$top=" + options.top;
            if (options.orderby != "")
                optionsString = optionsString + "&$orderby=" +options.orderby;
        }
        return optionsString;
    }
    
    public void searchPlus(String queryString)
    {
        searchPlus( queryString, null);
    }
    
    public void searchPlus(String queryString, SearchOptions options) {
    
        try {
            String optionsString = createOptionsString(options);
            var uri = buildURI(strFormatter -> strFormatter.format(
                    "https://%s.search.windows.net/indexes/%s/docs?api-version=%s&search=%s%s",
                    _serviceName, _indexName, _apiVersion, queryString, optionsString));
            var request = httpRequest(uri, _queryKey, "GET", null);
            var response = sendRequest(request);
            var jsonReader = Json.createReader(new StringReader(response.body()));
            var jsonArray = jsonReader.readObject().getJsonArray("value");
            var resultsCount = jsonArray.size();
            logMessage("Results:\nCount: " + resultsCount);
            for (int i = 0; i <= resultsCount - 1; i++) {
                logMessage(jsonArray.get(i).toString());
            }
    
            jsonReader.close();
    
        }
        catch (Exception e) {
            e.printStackTrace();
        }
    
    }
    ```

1. W `App` klasie Usuń komentarz z poniższego kodu. Ten kod konfiguruje pięć różnych zapytań, w tym tekst wyszukiwania, parametry zapytania i pola danych do zwrócenia. 

    ```java
    // Query 1
    client.logMessage("\n*QUERY 1****************************************************************");
    client.logMessage("Search for: Atlanta");
    client.logMessage("Return: All fields'");
    client.searchPlus("Atlanta");

    // Query 2
    client.logMessage("\n*QUERY 2****************************************************************");
    client.logMessage("Search for: Atlanta");
    client.logMessage("Return: HotelName, Tags, Address");
    SearchServiceClient.SearchOptions options2 = client.createSearchOptions();
    options2.select = "HotelName,Tags,Address";
    client.searchPlus("Atlanta", options2);

    //Query 3
    client.logMessage("\n*QUERY 3****************************************************************");
    client.logMessage("Search for: wifi & restaurant");
    client.logMessage("Return: HotelName, Description, Tags");
    SearchServiceClient.SearchOptions options3 = client.createSearchOptions();
    options3.select = "HotelName,Description,Tags";
    client.searchPlus("wifi,restaurant", options3);

    // Query 4 -filtered query
    client.logMessage("\n*QUERY 4****************************************************************");
    client.logMessage("Search for: all");
    client.logMessage("Filter: Ratings greater than 4");
    client.logMessage("Return: HotelName, Rating");
    SearchServiceClient.SearchOptions options4 = client.createSearchOptions();
    options4.filter="Rating%20gt%204";
    options4.select = "HotelName,Rating";
    client.searchPlus("*",options4);

    // Query 5 - top 2 results, ordered by
    client.logMessage("\n*QUERY 5****************************************************************");
    client.logMessage("Search for: boutique");
    client.logMessage("Get: Top 2 results");
    client.logMessage("Order by: Rating in descending order");
    client.logMessage("Return: HotelId, HotelName, Category, Rating");
    SearchServiceClient.SearchOptions options5 = client.createSearchOptions();
    options5.top=2;
    options5.orderby = "Rating%20desc";
    options5.select = "HotelId,HotelName,Category,Rating";
    client.searchPlus("boutique", options5);
    ```



    Istnieją dwa [sposoby dopasowywania terminów do zapytania](search-query-overview.md#types-of-queries): wyszukiwanie pełnotekstowe i filtry. Zapytanie wyszukiwania pełnotekstowego wyszukuje co najmniej jeden termin w `IsSearchable` polach w indeksie. Filtr jest wyrażeniem logicznym, które jest oceniane względem `IsFilterable` pól w indeksie. Możesz użyć wyszukiwania pełnotekstowego i filtrów razem lub oddzielnie.

1. Otwórz okno narzędzia **Maven** i wykonaj ten Maven cel: `verify exec:java`

    Poszukaj podsumowania poszczególnych zapytań i ich wyników. Przebieg powinien zostać ukończony z komunikatem o POWODZENIU kompilacji i kodem zakończenia równym zero (0).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy Pracujesz w ramach własnej subskrypcji, na końcu projektu warto usunąć zasoby, które nie są już potrzebne. Uruchomione zasoby mogą generować koszty. Zasoby możesz usuwać pojedynczo lub możesz usunąć grupę zasobów, aby usunąć cały ich zestaw.

Zasoby można znaleźć w portalu i zarządzać nimi za pomocą linku **wszystkie zasoby** lub **grupy zasobów** w okienku nawigacji po lewej stronie.

Jeśli używasz bezpłatnej usługi, pamiętaj, że masz ograniczone do trzech indeksów, indeksatorów i źródeł danych. Możesz usunąć poszczególne elementy w portalu, aby zachować limit. 

## <a name="next-steps"></a>Następne kroki

Ten przewodnik Szybki Start dla języka Java działa przez serię zadań w celu utworzenia indeksu, załadowania go do dokumentów i uruchamiania zapytań. Jeśli masz doświadczenie z podstawowymi pojęciami, zalecamy wykonanie poniższych artykułów, które wyświetlają operacje indeksatora w usłudze REST.

> [!div class="nextstepaction"]
> [Operacje indeksatora](/rest/api/searchservice/indexer-operations)