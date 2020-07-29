---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.custom: devx-track-java
ms.author: erhopf
ms.openlocfilehash: baf74969bdec495fad201a5f489e93edfabf53b6
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87375241"
---
[!INCLUDE [Prerequisites](prerequisites-java.md)]

[!INCLUDE [Set up and use environment variables](setup-env-variables.md)]

## <a name="initialize-a-project-with-gradle"></a>Inicjowanie projektu za pomocą narzędzia Gradle

Zaczniemy od utworzenia katalogu roboczego dla tego projektu. W wierszu polecenia (lub terminalu) uruchom następujące polecenie:

```console
mkdir transliterate-sample
cd transliterate-sample
```

Następnie zainicjujesz projekt narzędzia Gradle. To polecenie spowoduje utworzenie niezbędnych plików kompilacji dla narzędzia Gradle, a w szczególności najważniejszego pliku `build.gradle.kts`, który jest używany czasie wykonywania do utworzenia i skonfigurowania aplikacji. Uruchom następujące polecenie w katalogu roboczym:

```console
gradle init --type basic
```

Po wyświetleniu monitu wybierz pozycję **Język DSL**, a następnie **Kotlin**.

## <a name="configure-the-build-file"></a>Konfigurowanie pliku kompilacji

Znajdź plik `build.gradle.kts` i otwórz go za pomocą ulubionego środowiska IDE lub edytora tekstów. Następnie skopiuj go do tej konfiguracji kompilacji:

```
plugins {
    java
    application
}
application {
    mainClassName = "Transliterate"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("com.squareup.okhttp:okhttp:2.5.0")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Weź pod uwagę, że ten przykład zależy od bibliotek OkHttp (obsługa żądań HTTP) i Gson (obsługa i analizowanie kodu JSON). Jeśli chcesz dowiedzieć się więcej na temat konfiguracji kompilacji, zobacz [Creating New Gradle Builds (Tworzenie nowych kompilacji narzędzia Gradle)](https://guides.gradle.org/creating-new-gradle-builds/).

## <a name="create-a-java-file"></a>Tworzenie pliku języka Java

Utworzymy folder dla przykładowej aplikacji. W katalogu roboczym uruchom polecenie:

```console
mkdir -p src\main\java
```

Następnie utwórz w tym folderze plik o nazwie `Transliterate.java`.

## <a name="import-required-libraries"></a>Importowanie wymaganych bibliotek

Otwórz plik `Transliterate.java` i dodaj następujące instrukcje import:

```java
import java.io.*;
import java.net.*;
import java.util.*;
import com.google.gson.*;
import com.squareup.okhttp.*;
```


## <a name="define-variables"></a>Definiowanie zmiennych

Najpierw należy utworzyć klasę publiczną dla projektu:

```java
public class Transliterate {
  // All project code goes here...
}
```

Dodaj następujące wiersze do klasy `Transliterate`. Najpierw klucz subskrypcji i punkt końcowy są odczytywane ze zmiennych środowiskowych. Następnie należy zauważyć, że wraz z `api-version` , dwa dodatkowe parametry zostały dołączane do `url` . Te parametry są używane do ustawiania języka wejściowego oraz skryptów na potrzeby transliteracji. W tym przykładzie ustawiono język japoński (`jpan`) i łaciński (`latn`). 

```java
private static String subscriptionKey = System.getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY");
private static String endpoint = System.getenv("TRANSLATOR_TEXT_ENDPOINT");
String url = endpoint + "/transliterate?api-version=3.0&language=ja&fromScript=jpan&toScript=latn";
```
Jeśli używasz subskrypcji usługi Cognitive Services, musisz także uwzględnić `Ocp-Apim-Subscription-Region` w parametrach żądania. [Dowiedz się więcej o uwierzytelnianiu w ramach subskrypcji wielu usług](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-client-and-build-a-request"></a>Tworzenie klienta i budowanie żądania

Dodaj następujący wiersz do klasy `Transliterate` w celu utworzenia wystąpienia klasy `OkHttpClient`:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Następnie utworzymy żądanie POST. Możesz dowolnie zmienić tekst do transliteracji.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"こんにちは\"\n}]");
    Request request = new Request.Builder()
            .url(url).post(body)
            .addHeader("Ocp-Apim-Subscription-Key", subscriptionKey)
            .addHeader("Content-type", "application/json").build();
    Response response = client.newCall(request).execute();
    return response.body().string();
}
```

## <a name="create-a-function-to-parse-the-response"></a>Tworzenie funkcji analizowania odpowiedzi

Ta prosta funkcja analizuje i prettifies odpowiedź JSON z usługi Translator.

```java
// This function prettifies the json response.
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonElement json = parser.parse(json_text);
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="put-it-all-together"></a>Zebranie wszystkich elementów

Ostatnim krokiem jest wykonanie żądania i pobranie odpowiedzi. Dodaj następujące wiersze do projektu:

```java
public static void main(String[] args) {
    try {
        Transliterate transliterateRequest = new Transliterate();
        String response = transliterateRequest.Post();
        System.out.println(prettify(response));
    } catch (Exception e) {
        System.out.println(e);
    }
}
```

## <a name="run-the-sample-app"></a>Uruchamianie przykładowej aplikacji

To wszystko. Teraz możesz uruchomić przykładową aplikację. Z wiersza polecenia (lub sesji terminalu) przejdź do katalogu głównego używanego katalogu roboczego, a następnie uruchom polecenie:

```console
gradle build
```

Po zakończeniu kompilacji uruchom:

```console
gradle run
```

## <a name="sample-response"></a>Przykładowa odpowiedź

```json
[
  {
    "text": "konnichiwa",
    "script": "latn"
  }
]
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z dokumentacją interfejsu API, aby zrozumieć wszystko, co możesz zrobić z translatorem.

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
