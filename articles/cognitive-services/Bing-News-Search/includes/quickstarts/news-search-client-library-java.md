---
title: wyszukiwanie wiadomości Bing Java Client Library — Szybki Start
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 2edf5634b56fdfa6e1ed7dad9ee443ca588db583
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87375835"
---
Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć wyszukiwanie wiadomości za pomocą biblioteki klienta wyszukiwanie wiadomości Bing dla środowiska Java. Chociaż wyszukiwanie wiadomości Bing ma interfejs API REST zgodny z większością języków programowania, Biblioteka klienta zapewnia łatwy sposób integracji usługi z aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingNewsSearch).

## <a name="prerequisites"></a>Wymagania wstępne

Zainstaluj zależności biblioteki klienta wyszukiwanie wiadomości Bing przy użyciu programu Maven, Gradle lub innego systemu zarządzania zależnościami. Plik POM systemu Maven wymaga następującej deklaracji:

```xml
    <dependencies>
    <dependency>
        <groupId>com.microsoft.azure.cognitiveservices</groupId>
        <artifactId>azure-cognitiveservices-newssearch</artifactId>
        <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
    </dependencies>
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Tworzenie i inicjowanie projektu

Utwórz nowy projekt w języku Java w ulubionym środowisku IDE lub edytorze i zaimportuj poniższe biblioteki.

```java
import com.microsoft.azure.cognitiveservices.newssearch.*;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.NewsInner;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.NewsSearchAPIImpl;
import com.microsoft.azure.cognitiveservices.newssearch.implementation.TrendingTopicsInner;
import com.microsoft.rest.credentials.ServiceClientCredentials;
import okhttp3.Interceptor;
import okhttp3.OkHttpClient;
import okhttp3.Request;
import okhttp3.Response;
import java.io.IOException;
```

## <a name="create-a-search-client-and-store-credentials"></a>Tworzenie klienta wyszukiwania i przechowywanie poświadczeń

1. Utwórz metodę o nazwie `getClient()`, która zwraca nowego klienta wyszukiwania `NewsSearchAPIImpl`. Dodaj swój punkt końcowy jako pierwszy parametr dla nowego obiektu `NewsSearchAPIImpl` oraz nowy obiekt `ServiceClientCredentials` do przechowywania poświadczeń. 

    ```java
    public static NewsSearchAPIImpl getClient(final String subscriptionKey) {
        return new NewsSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                });
    }
    ```

2. Aby utworzyć obiekt `ServiceClientCredentials`, zastąp funkcję `applyCredentialsFilter()`. Przekaż `OkHttpClient.Builder` do metody i użyj metody konstruktora, `addNetworkInterceptor()` Aby utworzyć swoje poświadczenia dla wywołania biblioteki klienta.

    ```java
    new ServiceClientCredentials() {
        @Override
        public void applyCredentialsFilter(OkHttpClient.Builder builder) {
            builder.addNetworkInterceptor(
                    new Interceptor() {
                        @Override
                        public Response intercept(Chain chain) throws IOException {
                            Request request = null;
                            Request original = chain.request();
                            // Request customization: add request headers.
                            Request.Builder requestBuilder = original.newBuilder()
                                    .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
                            request = requestBuilder.build();
                            return chain.proceed(request);
                        }
                    });
        }
    });
    ```

## <a name="send-and-receive-a-search-request"></a>Wysyłanie i odbieranie żądania wyszukiwania

1. Utwórz metodę, która wywołuje funkcję `getClient()` i wysyła żądanie wyszukiwania do usługi wyszukiwania wiadomości Bing. Przefiltruj wyszukiwanie za pomocą parametrów *rynek* i *liczba*, a następnie wypisz informacje o pierwszym wyniku dotyczącym wiadomości: nazwę, adres URL, datę publikacji, opis, nazwę dostawcy i łączną liczbę szacowanych dopasowań dla Twojego wyszukiwania.

    ```java
    public static void newsSearch(String subscriptionKey)
    {
        NewsSearchAPIImpl client = getClient(subscriptionKey);
        String searchTerm = "Quantum Computing";
    
        NewsInner newsResults = client.searchs().list(searchTerm, null, null, null,
                null, null, 100, null, "en-us",
                null, null, null, null, null,
                null, null);
    
        if (newsResults.value().size() > 0)
        {
            NewsArticle firstNewsResult = newsResults.value().get(0);
    
            System.out.println(String.format("TotalEstimatedMatches value: %d", newsResults.totalEstimatedMatches()));
            System.out.println(String.format("News result count: %d", newsResults.value().size()));
            System.out.println(String.format("First news name: %s", firstNewsResult.name()));
            System.out.println(String.format("First news url: %s", firstNewsResult.url()));
            System.out.println(String.format("First news description: %s", firstNewsResult.description()));
            System.out.println(String.format("First news published time: %s", firstNewsResult.datePublished()));
            System.out.println(String.format("First news provider: %s", firstNewsResult.provider().get(0).name()));
        }
        else
        {
            System.out.println("Couldn't find news results!");
        }
    
    }
    
    ```

2. Dodaj swoją metodę wyszukiwania do metody `main()`, aby wykonać kod.

    ```java 
    public static void main(String[] args) {
        String subscriptionKey = "YOUR-SUBSCRIPTION-KEY";
        NewsSearchSDK.newsSearch(subscriptionKey);
    }
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](../../tutorial-bing-news-search-single-page-app.md)
