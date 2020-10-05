---
title: 'Szybki Start: sprawdzanie pisowni za pomocą interfejsu API REST i środowiska Java — sprawdzanie pisowni Bing'
titleSuffix: Azure Cognitive Services
description: Zacznij korzystać z interfejsu API REST sprawdzanie pisowni Bing i języka Java, aby sprawdzić pisownię i gramatykę.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-spell-check
ms.topic: quickstart
ms.date: 05/21/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 02e83d2a18958e23f412af7b9685ddd1ab400aac
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91316634"
---
# <a name="quickstart-check-spelling-with-the-bing-spell-check-rest-api-and-java"></a>Szybki Start: sprawdzanie pisowni za pomocą interfejsu API REST sprawdzanie pisowni Bing i środowiska Java

Użyj tego przewodnika Szybki start, aby wykonać pierwsze wywołanie interfejsu API REST sprawdzania pisowni Bing. Ta prosta aplikacja w języku Java wysyła żądanie do interfejsu API i zwraca listę sugerowanych poprawek. 

Mimo że aplikacja jest zapisywana w języku Java, interfejs API jest usługą sieci Web RESTful zgodną z większością języków programowania. Kod źródłowy tej aplikacji jest dostępny w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingSpellCheck.java).

## <a name="prerequisites"></a>Wymagania wstępne

* Zestaw Java Development Kit (JDK) w wersji 7 lub nowszej.

* Zaimportuj plik [gson-2.8.5. jar](https://libraries.io/maven/com.google.code.gson%3Agson) lub najnowszą wersję [gson](https://github.com/google/gson) . Aby wykonać polecenie w wierszu polecenia, należy dodać `.jar` do folderu Java z klasą Main.

[!INCLUDE [cognitive-services-bing-spell-check-signup-requirements](../../../../includes/cognitive-services-bing-spell-check-signup-requirements.md)]

## <a name="create-and-initialize-an-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy projekt Java w ulubionym środowisku IDE lub edytorze z wybraną nazwą klasy, a następnie zaimportuj następujące pakiety:

    ```java
    import java.io.*;
    import java.net.*;
    import com.google.gson.*;
    import javax.net.ssl.HttpsURLConnection;
    ```

2. Utwórz zmienne dla hosta punktu końcowego interfejsu API, ścieżki i klucza subskrypcji. Następnie utwórz zmienne dla rynku, tekst, który chcesz sprawdzić pisownię, oraz ciąg dla trybu sprawdzania pisowni. Możesz użyć globalnego punktu końcowego w poniższym kodzie lub użyć punktu końcowego [niestandardowej domeny](../../../cognitive-services/cognitive-services-custom-subdomains.md) podrzędnej wyświetlanego w Azure Portal dla zasobu.

    ```java
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/spellcheck";

    static String key = "<ENTER-KEY-HERE>";

    static String mkt = "en-US";
    static String mode = "proof";
    static String text = "Hollo, wrld!";
    ```

## <a name="create-and-send-an-api-request"></a>Tworzenie i wysyłanie żądania interfejsu API

1. Utwórz funkcję o nazwie `check()`, która utworzy i wyśle żądanie interfejsu API. W ramach tej funkcji Dodaj kod określony w następnych krokach. Utwórz ciąg dla parametrów żądania:

   1. Przypisz kod rynkowy do `mkt` parametru `=` operatorem. 

   1. Dodaj `mode` parametr z `&` operatorem, a następnie przypisz tryb sprawdzania pisowni. 

   ```java
   public static void check () throws Exception {
       String params = "?mkt=" + mkt + "&mode=" + mode;
      // add the rest of the code snippets here (except prettify() and main())...
   }
   ```

2. Utwórz adres URL, łącząc parametry hosta, ścieżki i parametrów punktu końcowego. Utwórz nowy `HttpsURLConnection` obiekt.

    ```java
    URL url = new URL(host + path + params);
    HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
    ```

3. Otwórz połączenie z adresem URL. Ustaw metodę żądania na `POST` i Dodaj parametry żądania. Pamiętaj, aby dodać klucz subskrypcji do `Ocp-Apim-Subscription-Key` nagłówka.

    ```java
    connection.setRequestMethod("POST");
    connection.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
    connection.setRequestProperty("Ocp-Apim-Subscription-Key", key);
    connection.setDoOutput(true);
    ```

4. Utwórz nowy `DataOutputStream` obiekt i Wyślij żądanie do interfejsu API.

    ```java
        DataOutputStream wr = new DataOutputStream(connection.getOutputStream());
        wr.writeBytes("text=" + text);
        wr.flush();
        wr.close();
    ```

## <a name="format-and-read-the-api-response"></a>Formatowanie i odczytywanie odpowiedzi interfejsu API

1. Dodaj `prettify()` metodę do klasy, która sformatuje kod JSON, aby uzyskać bardziej czytelny wynik.

    ``` java
    // This function prettifies the json response.
    public static String prettify(String json_text) {
        JsonParser parser = new JsonParser();
        JsonElement json = parser.parse(json_text);
        Gson gson = new GsonBuilder().setPrettyPrinting().create();
        return gson.toJson(json);
    }
    ```

1. Utwórz obiekt `BufferedReader` i odczytaj odpowiedź z interfejsu API. Wyświetl ją na konsoli.
    
    ```java
    BufferedReader in = new BufferedReader(
    new InputStreamReader(connection.getInputStream()));
    String line;
    while ((line = in.readLine()) != null) {
        System.out.println(prettify(line));
    }
    in.close();
    ```

## <a name="call-the-api"></a>Wywoływanie interfejsu API

W funkcji Main aplikacji należy wywołać `check()` metodę utworzoną wcześniej.
```java
        public static void main(String[] args) {
            try {
                check();
            }
            catch (Exception e) {
                System.out.println (e);
            }
        }
```

## <a name="run-the-application"></a>Uruchamianie aplikacji

Skompiluj i Uruchom projekt. Jeśli używasz wiersza polecenia, użyj następujących poleceń, aby skompilować i uruchomić aplikację:

1. Kompiluj aplikację:

   ```bash
   javac -classpath .;gson-2.2.2.jar\* <CLASS_NAME>.java
   ```

2. Uruchom aplikację:

   ```bash
   java -cp .;gson-2.2.2.jar\* <CLASS_NAME>
   ```

## <a name="example-json-response"></a>Przykładowa odpowiedź JSON

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON, jak pokazano w następującym przykładzie:

```json
{
   "_type": "SpellCheck",
   "flaggedTokens": [
      {
         "offset": 0,
         "token": "Hollo",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "Hello",
               "score": 0.9115257530801
            },
            {
               "suggestion": "Hollow",
               "score": 0.858039839213461
            },
            {
               "suggestion": "Hallo",
               "score": 0.597385084464481
            }
         ]
      },
      {
         "offset": 7,
         "token": "wrld",
         "type": "UnknownToken",
         "suggestions": [
            {
               "suggestion": "world",
               "score": 0.9115257530801
            }
         ]
      }
   ]
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](../tutorials/spellcheck.md)

- [Czym jest interfejs API sprawdzania pisowni Bing?](../overview.md)
- [Dokumentacja wersji 7 interfejsu API sprawdzanie pisowni Bing](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-spell-check-api-v7-reference)
