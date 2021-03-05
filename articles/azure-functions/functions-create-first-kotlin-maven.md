---
title: Tworzenie funkcji Kotlin w Azure Functions przy użyciu Maven
description: Utwórz i Opublikuj aplikację funkcji wyzwalanej przez protokół HTTP w celu Azure Functions z Kotlin i Maven.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 721fbaf1a3b2418677d07e8ac39fa733164c1459
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102179565"
---
# <a name="quickstart-create-your-first-function-with-kotlin-and-maven"></a>Szybki Start: Tworzenie pierwszej funkcji za pomocą Kotlin i Maven

W tym artykule opisano, jak za pomocą narzędzia wiersza polecenia Maven utworzyć i opublikować projekt funkcji Kotlin w celu Azure Functions. Gdy wszystko będzie gotowe, Twój kod funkcji zostanie uruchomiony w [planie zużycia](consumption-plan.md) na platformie Azure i może być wyzwalany za pomocą żądania HTTP.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby opracowywać funkcje przy użyciu Kotlin, musisz mieć zainstalowane następujące elementy:

- Zestaw [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), wersja 8
- Narzędzie [Apache Maven](https://maven.apache.org), wersja 3.0 lub nowsza
- [Interfejs wiersza polecenia platformy Azure](/cli/azure)
- [Azure Functions Core Tools](./functions-run-local.md#v2) w wersji 2.6.666 lub nowszej

> [!IMPORTANT]
> Aby wykonać wszystkie czynności opisane w tym przewodniku Szybki start, dla zmiennej środowiskowej JAVA_HOME należy ustawić lokalizację instalacji zestawu JDK.

## <a name="generate-a-new-azure-functions-project"></a>Generuj nowy projekt Azure Functions

W pustym folderze Uruchom następujące polecenie, aby wygenerować projekt Azure Functions z [Maven Archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

# <a name="bash"></a>[bash](#tab/bash)
```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```

> [!NOTE]
> Jeśli występują problemy z uruchomieniem polecenia, należy zapoznać się z `maven-archetype-plugin` używaną wersją. Ponieważ uruchamiasz polecenie w pustym katalogu bez `.pom` pliku, może być podjęta próba użycia wtyczki starszej wersji od, `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` Jeśli uaktualniono Maven ze starszej wersji. Jeśli tak, spróbuj usunąć `maven-archetype-plugin` katalog i ponownie uruchomić polecenie.

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)
```powershell
mvn archetype:generate `
    "-DarchetypeGroupId=com.microsoft.azure" `
    "-DarchetypeArtifactId=azure-functions-kotlin-archetype"
```

# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```
---

Maven prosi o podanie wartości niezbędnych do zakończenia generowania projektu. Aby uzyskać informacje o wartościach _groupId_, _artifactId_ i _version_, zobacz materiały referencyjne [Konwencja nazewnictwa narzędzia Maven](https://maven.apache.org/guides/mini/guide-naming-conventions.html). Wartość _appName_ musi być unikatowa w obrębie platformy Azure, dlatego narzędzie Maven generuje domyślną nazwę aplikacji na podstawie wprowadzonej wcześniej wartości _artifactId_. Wartość _PackageName_ określa pakiet Kotlin dla wygenerowanego kodu funkcji.

Poniższe identyfikatory `com.fabrikam.functions` i `fabrikam-functions` są używane jako przykład i poprawiają czytelność dalszych kroków w tym przewodniku Szybki start. Zalecamy dostarczenie własnych wartości Maven w tym kroku.

<pre>
[INFO] Parameter: groupId, Value: com.fabrikam.function
[INFO] Parameter: artifactId, Value: fabrikam-function
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.fabrikam.function
[INFO] Parameter: packageInPathFormat, Value: com/fabrikam/function
[INFO] Parameter: appName, Value: fabrikam-function-20190524171507291
[INFO] Parameter: resourceGroup, Value: java-functions-group
[INFO] Parameter: package, Value: com.fabrikam.function
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: groupId, Value: com.fabrikam.function
[INFO] Parameter: appRegion, Value: westus
[INFO] Parameter: artifactId, Value: fabrikam-function
</pre>

Narzędzie Maven utworzy pliki projektu w nowym folderze o nazwie podanej we właściwości _artifactId_. W tym przykładzie jest to `fabrikam-functions`. Gotowy do uruchomienia kod wygenerowany w projekcie jest prostą funkcją [wyzwalaną przez protokół HTTP](./functions-bindings-http-webhook.md), która zwraca treść żądania:

```kotlin
class Function {

    /**
     * This function listens at endpoint "/api/HttpTrigger-Java". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/HttpTrigger-Java&code={your function key}
     * 2. curl "{your host}/api/HttpTrigger-Java?name=HTTP%20Query&code={your function key}"
     * Function Key is not needed when running locally, it is used to invoke function deployed to Azure.
     * More details: https://aka.ms/functions_authorization_keys
     */
    @FunctionName("HttpTrigger-Java")
    fun run(
            @HttpTrigger(
                    name = "req",
                    methods = [HttpMethod.GET, HttpMethod.POST],
                    authLevel = AuthorizationLevel.FUNCTION) request: HttpRequestMessage<Optional<String>>,
            context: ExecutionContext): HttpResponseMessage {

        context.logger.info("HTTP trigger processed a ${request.httpMethod.name} request.")

        val query = request.queryParameters["name"]
        val name = request.body.orElse(query)

        name?.let {
            return request
                    .createResponseBuilder(HttpStatus.OK)
                    .body("Hello, $name!")
                    .build()
        }

        return request
                .createResponseBuilder(HttpStatus.BAD_REQUEST)
                .body("Please pass a name on the query string or in the request body")
                .build()
    }
}
```

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

Zmień katalog na nowo utworzony folder projektu, a następnie skompiluj i uruchom funkcję za pomocą narzędzia Maven:

```
cd fabrikam-function
mvn clean package
mvn azure-functions:run
```

> [!NOTE]
> Jeśli wystąpi wyjątek `javax.xml.bind.JAXBException` w przypadku używania platformy Java 9, zobacz obejście w serwisie [GitHub](https://github.com/jOOQ/jOOQ/issues/6477).

Te dane wyjściowe są wyświetlane, gdy funkcja jest uruchomiona lokalnie w systemie i jest gotowa do odpowiadania na żądania HTTP:

<pre>
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpTrigger-Java: [GET,POST] http://localhost:7071/api/HttpTrigger-Java
</pre>

Wyzwól funkcję z poziomu wiersza polecenia, używając programu curl w nowym oknie terminalu:

```
curl -w '\n' -d LocalFunction http://localhost:7071/api/HttpTrigger-Java
```

<pre>
Hello LocalFunction!
</pre>

Aby zatrzymać wykonywanie kodu funkcji, użyj polecenia `Ctrl-C` w oknie terminala.

## <a name="deploy-the-function-to-azure"></a>Wdrażanie funkcji na platformie Azure

Proces wdrażania w usłudze Azure Functions korzysta z poświadczeń konta z interfejsu wiersza polecenia platformy Azure. Przed kontynuowaniem [Zaloguj się przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli) .

```azurecli
az login
```

Wdróż swój kod w nowej aplikacji funkcji przy użyciu `azure-functions:deploy` elementu docelowego Maven.

> [!NOTE]
> Jeśli używasz Visual Studio Code do wdrożenia aplikacji funkcji, pamiętaj, aby wybrać niebezpłatną subskrypcję lub wystąpił błąd. Możesz obejrzeć swoją subskrypcję po lewej stronie IDE.

```
mvn azure-functions:deploy
```

Po zakończeniu wdrażania zobaczysz adres URL, którego można użyć w celu uzyskania dostępu do aplikacji funkcji:

<pre>
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
</pre>

Przetestuj działanie aplikacji funkcji na platformie Azure, używając programu `cURL`. Musisz zmienić adres URL z poniższego przykładu tak, aby był zgodny z adresem URL Twojej wdrożonej aplikacji funkcji uzyskanym w poprzednim kroku.

> [!NOTE]
> Upewnij się, że ustawiono **prawa dostępu** do programu `Anonymous` . Po wybraniu domyślnego poziomu programu `Function` wymagane jest zaprezentowanie [klucza funkcji](functions-bindings-http-webhook-trigger.md#authorization-keys) w żądaniach, aby uzyskać dostęp do punktu końcowego funkcji.

```
curl -w '\n' https://fabrikam-function-20170920120101928.azurewebsites.net/api/HttpTrigger-Java -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>Wprowadzanie zmian i ponowne wdrażanie

Edytuj `src/main.../Function.java` plik źródłowy w wygenerowanym projekcie, aby zmienić tekst zwracany przez aplikację funkcji. Zmień ten wiersz:

```kotlin
return request
        .createResponseBuilder(HttpStatus.OK)
        .body("Hello, $name!")
        .build()
```

Do następującego kodu:

```kotlin
return request
        .createResponseBuilder(HttpStatus.OK)
        .body("Hi, $name!")
        .build()
```

Zapisz zmiany i wdróż ponownie, uruchamiając polecenie `azure-functions:deploy` z poziomu terminalu, tak jak poprzednio. Aplikacja funkcji zostanie zaktualizowana, a żądanie:

```
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

Zobaczysz zaktualizowane dane wyjściowe:

<pre>
Hi, AzureFunctionsTest
</pre>


## <a name="reference-bindings"></a>Powiązania odwołań

Aby można było współpracować z wyzwalaczami [Azure Functions i powiązaniami](functions-triggers-bindings.md) innymi niż wyzwalacz http i wyzwalaczem Timer, należy zainstalować rozszerzenia powiązań. Chociaż nie jest to wymagane w tym artykule, musisz wiedzieć, jak włączyć rozszerzenia podczas pracy z innymi typami powiązań.

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

## <a name="next-steps"></a>Następne kroki

Utworzono aplikację funkcji Kotlin z prostym wyzwalaczem HTTP i wdrożono ją w Azure Functions.

- Zapoznaj się z [przewodnikiem dewelopera Azure Functions Java](functions-reference-java.md) , aby uzyskać więcej informacji na temat opracowywania funkcji Java i Kotlin.
- Dodaj do swojego projektu kolejne funkcje z różnymi wyzwalaczami, używając elementu docelowego `azure-functions:add` narzędzia Maven.
- Pisanie i debugowanie funkcji lokalnie za pomocą oprogramowania [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) i [Eclipse](functions-create-maven-eclipse.md). 
- Debuguj funkcje wdrożone na platformie Azure przy użyciu programu Visual Studio Code. Zobacz dokumentację [bezserwerowych aplikacji Java](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud) programu Visual Studio Code, aby uzyskać instrukcje.
