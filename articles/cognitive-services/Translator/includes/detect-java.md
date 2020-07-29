---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.custom: devx-track-java
ms.author: erhopf
ms.openlocfilehash: 69dd5f5eb3a1cc0cda60702e9e653e23f6588f93
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87375123"
---
[!INCLUDE [Prerequisites](prerequisites-java.md)]

[!INCLUDE [Setup and use environment variables](setup-env-variables.md)]

## <a name="initialize-a-project-with-gradle"></a>Inicjowanie projektu za pomocą narzędzia Gradle

Zaczniemy od utworzenia katalogu roboczego dla tego projektu. W wierszu polecenia (lub terminalu) uruchom następujące polecenie:

```console
mkdir detect-sample
cd detect-sample
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
    mainClassName = "Detect"
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
mkdir -p src/main/java
```

Następnie utwórz w tym folderze plik o nazwie `Detect.java`.

## <a name="import-required-libraries"></a>Importowanie wymaganych bibliotek

Otwórz plik `Detect.java` i dodaj następujące instrukcje import:

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
public class Detect {
  // All project code goes here...
}
```

Dodaj następujące wiersze do klasy `Detect`. Zauważ, że klucz subskrypcji i punkt końcowy są odczytywane ze zmiennych środowiskowych:

```java
private static String subscriptionKey = System.getenv("TRANSLATOR_TEXT_SUBSCRIPTION_KEY");
private static String endpoint = System.getenv("TRANSLATOR_TEXT_ENDPOINT");
String url = endpoint + "/detect?api-version=3.0";
```

Jeśli używasz subskrypcji usługi Cognitive Services, musisz także uwzględnić `Ocp-Apim-Subscription-Region` w parametrach żądania. [Dowiedz się więcej o uwierzytelnianiu w ramach subskrypcji wielu usług](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication).

## <a name="create-a-client-and-build-a-request"></a>Tworzenie klienta i budowanie żądania

Dodaj następujący wiersz do klasy `Detect` w celu utworzenia wystąpienia klasy `OkHttpClient`:

```java
// Instantiates the OkHttpClient.
OkHttpClient client = new OkHttpClient();
```

Następnie utworzymy żądanie POST. Możesz zmienić tekst, którego język ma zostać wykryty.

```java
// This function performs a POST request.
public String Post() throws IOException {
    MediaType mediaType = MediaType.parse("application/json");
    RequestBody body = RequestBody.create(mediaType,
            "[{\n\t\"Text\": \"Salve mondo!\"\n}]");
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
        Detect detectRequest = new Detect();
        String response = detectRequest.Post();
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

Po uruchomieniu przykładu na terminalu powinny zostać wyświetlone następujące elementy:

> [!NOTE]
> Znajdź skrót kraju/regionu na [liście języków](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).

```json
[
  {
    "language": "it",
    "score": 1.0,
    "isTranslationSupported": true,
    "isTransliterationSupported": false,
    "alternatives": [
      {
        "language": "pt",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      },
      {
        "language": "en",
        "score": 1.0,
        "isTranslationSupported": true,
        "isTransliterationSupported": false
      }
    ]
  }
]
```

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z dokumentacją interfejsu API, aby zrozumieć wszystko, co możesz zrobić z translatorem.

> [!div class="nextstepaction"]
> [Dokumentacja interfejsu API](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference)
