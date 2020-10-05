---
title: wyszukiwanie wideo Bing Java Client Library — Szybki Start
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/19/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 2b3d4993406f150b2983d4d820f7d070b5de1e96
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "87375718"
---
Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć wyszukiwanie wiadomości za pomocą biblioteki klienta wyszukiwanie wideo Bing dla środowiska Java. Chociaż wyszukiwanie wideo Bing ma interfejs API REST zgodny z większością języków programowania, Biblioteka klienta zapewnia łatwy sposób integracji usługi z aplikacjami. Kod źródłowy dla tego przykładu można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVideoSearch), z dodatkowymi adnotacjami i funkcjami.

## <a name="prerequisites"></a>Wymagania wstępne

* [Zestaw Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html)

* [Biblioteka Gson](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-video-search-signup-requirements](~/includes/cognitive-services-bing-video-search-signup-requirements.md)]

Zainstaluj zależności biblioteki klienta wyszukiwanie wideo Bing przy użyciu programu Maven, Gradle lub innego systemu zarządzania zależnościami. Plik POM systemu Maven wymaga następującej deklaracji:

```xml
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.cognitiveservices</groupId>
      <artifactId>azure-cognitiveservices-videosearch</artifactId>
      <version>0.0.1-beta-SNAPSHOT</version>
    </dependency>
  </dependencies> 
```

## <a name="create-and-initialize-a-project"></a>Tworzenie i inicjowanie projektu


Utwórz nowy projekt w języku Java w ulubionym środowisku IDE lub edytorze i zaimportuj poniższe biblioteki.

```java
    import com.microsoft.azure.cognitiveservices.videosearch.*;
    import com.microsoft.azure.cognitiveservices.videosearch.VideoObject;
    import com.microsoft.rest.credentials.ServiceClientCredentials;
    import okhttp3.Interceptor;
    import okhttp3.OkHttpClient;
    import okhttp3.Request;
    import okhttp3.Response;
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.List; 
```

## <a name="create-a-search-client"></a>Tworzenie klienta wyszukiwania

1. Zaimplementuj klienta `VideoSearchAPIImpl`, który wymaga punktu końcowego interfejsu API i wystąpienia klasy `ServiceClientCredentials`.

    ```java
    public static VideoSearchAPIImpl getClient(final String subscriptionKey) {
        return new VideoSearchAPIImpl("https://api.cognitive.microsoft.com/bing/v7.0/",
                new ServiceClientCredentials() {
                //...
                }
    )};
    ```

    Aby zaimplementować klasę `ServiceClientCredentials`, wykonaj następujące kroki:

    1. Przesłoń funkcję `applyCredentialsFilter()`, używając obiektu `OkHttpClient.Builder` jako parametru. 
        
        ```java
        //...
        new ServiceClientCredentials() {
                @Override
                public void applyCredentialsFilter(OkHttpClient.Builder builder) {
                //...
                }
        //...
        ```
    
    2. W ramach funkcji `applyCredentialsFilter()` wywołaj funkcję `builder.addNetworkInterceptor()`. Utwórz nowy obiekt `Interceptor` i przesłoń jego metodę `intercept()` tak, aby przyjmowała obiekt interceptora `Chain`.

        ```java
        //...
        builder.addNetworkInterceptor(
            new Interceptor() {
                @Override
                public Response intercept(Chain chain) throws IOException {
                //...    
                }
            });
        ///...
        ```

    3. W funkcji `intercept` utwórz zmienne dla żądania. Użyj funkcji `Request.Builder()`, aby utworzyć żądanie. Dodaj klucz subskrypcji do nagłówka `Ocp-Apim-Subscription-Key` i zwróć funkcję `chain.proceed()` w obiekcie żądania.
            
        ```java
        //...
        public Response intercept(Chain chain) throws IOException {
            Request request = null;
            Request original = chain.request();
            Request.Builder requestBuilder = original.newBuilder()
                    .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            request = requestBuilder.build();
            return chain.proceed(request);
        }
        //...
        ```

## <a name="send-a-search-request-and-receive-the-response"></a>Wyślij żądanie wyszukiwania i odbierz odpowiedź 

1. Utwórz funkcję o nazwie `VideoSearch()`, która przyjmujące Twój klucz subskrypcji w postaci ciągu. Utwórz wystąpienie utworzonego wcześniej klienta wyszukiwania.
    
    ```java
    public static void VideoSearch(String subscriptionKey){
        VideoSearchAPIImpl client = VideoSDK.getClient(subscriptionKey);
        //...
    }
    ```
2. W ramach funkcji `VideoSearch()` wyślij żądanie wyszukiwania za pomocą klienta, używając elementu `SwiftKey` jako terminu wyszukiwania. Jeśli interfejs API wyszukiwania wideo zwrócił wynik, pobierz pierwszy wynik i wyświetl jego identyfikator, nazwę i adres URL wraz z całkowitą liczbę zwróconych filmów wideo. 
    
    ```java
    VideosInner videoResults = client.searchs().list("SwiftKey");

    if (videoResults == null){
        System.out.println("Didn't see any video result data..");
    }
    else{
        if (videoResults.value().size() > 0){
            VideoObject firstVideoResult = videoResults.value().get(0);

            System.out.println(String.format("Video result count: %d", videoResults.value().size()));
            System.out.println(String.format("First video id: %s", firstVideoResult.videoId()));
            System.out.println(String.format("First video name: %s", firstVideoResult.name()));
            System.out.println(String.format("First video url: %s", firstVideoResult.contentUrl()));
        }
        else{
            System.out.println("Couldn't find video results!");
        }
    }
    ```

3. Wywołaj metodę wyszukiwania z poziomu głównej metody.

    ```java
    public static void main(String[] args) {
        VideoSDK.VideoSearch("YOUR-SUBSCRIPTION-KEY");
    }
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie internetowej aplikacji jednostronicowej](../../tutorial-bing-video-search-single-page-app.md)

## <a name="see-also"></a>Zobacz też 

* [Co to jest interfejs API wyszukiwania wideo Bing?](../../overview.md)
* [Przykłady zestawu SDK programu .NET dla usług poznawczych](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
