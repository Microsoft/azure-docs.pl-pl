---
title: Dokumentacja dla deweloperów Java dla Azure Functions
description: Dowiedz się, jak opracowywać funkcje przy użyciu języka Java.
ms.topic: conceptual
ms.date: 09/14/2018
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 1ffbd760ae75605d75652b29d379420d6946aa8f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96326458"
---
# <a name="azure-functions-java-developer-guide"></a>Przewodnik dewelopera Azure Functions Java

Ten przewodnik zawiera szczegółowe informacje ułatwiające pomyślne rozwinięcie Azure Functions przy użyciu języka Java.

Jeśli jesteś deweloperem języka Java, jeśli jesteś nowym do Azure Functions, rozważ pierwsze czytanie jednego z następujących artykułów:

| Wprowadzenie | Pojęcia| 
| -- | -- |  
| <ul><li>[Funkcja języka Java używająca Visual Studio Code](./create-first-function-vs-code-java.md)</li><li>[Funkcja Java/Maven z terminalem/wierszem polecenia](./create-first-function-cli-java.md)</li><li>[Funkcja języka Java używająca Gradle](functions-create-first-java-gradle.md)</li><li>[Funkcja języka Java wykorzystująca przezaćmienie](functions-create-maven-eclipse.md)</li><li>[Funkcja języka Java używająca POMYSŁu IntelliJ](functions-create-maven-intellij.md)</li></ul> | <ul><li>[Przewodnik dla deweloperów](functions-reference.md)</li><li>[Opcje hostingu](functions-scale.md)</li><li>[&nbsp;Zagadnienia dotyczące wydajności](functions-best-practices.md)</li></ul> |

## <a name="java-function-basics"></a>Podstawowe funkcje języka Java

Funkcja języka Java to `public` Metoda, która jest zadekoracyjna adnotacją `@FunctionName` . Ta metoda definiuje wpis dla funkcji języka Java i musi być unikatowa w konkretnym pakiecie. Pakiet może zawierać wiele klas z wieloma metodami publicznymi z adnotacją `@FunctionName` . Pojedynczy pakiet jest wdrażany w aplikacji funkcji na platformie Azure. W przypadku uruchamiania na platformie Azure aplikacja funkcji udostępnia kontekst wdrażania, wykonywania i zarządzania dla poszczególnych funkcji języka Java.

## <a name="programming-model"></a>Model programowania 

Pojęcia związane z [wyzwalaczami i powiązaniami](functions-triggers-bindings.md) mają podstawowe znaczenie dla Azure Functions. Wyzwalacze rozpoczynają wykonywanie kodu. Powiązania umożliwiają przekazywanie danych do i zwracanie danych z funkcji bez konieczności pisania niestandardowego kodu dostępu do danych.

## <a name="create-java-functions"></a>Tworzenie funkcji języka Java

Aby ułatwić tworzenie funkcji języka Java, dostępne są narzędzia oparte na Maven i Archetypes, które używają wstępnie zdefiniowanych szablonów języka Java, które ułatwiają tworzenie projektów z określonym wyzwalaczem funkcji.    

### <a name="maven-based-tooling"></a>Narzędzia oparte na Maven

Następujące środowiska deweloperskie zawierają Azure Functions narzędzia, które umożliwiają tworzenie projektów funkcji języka Java: 

+ [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions)
+ [Eclipse](functions-create-maven-eclipse.md)
+ [IntelliJ](functions-create-maven-intellij.md)

Powyższe linki artykułu pokazują, jak utworzyć swoje pierwsze funkcje przy użyciu wybranego środowiska IDE. 

### <a name="project-scaffolding"></a>Tworzenie szkieletu projektu

Jeśli wolisz opracowywanie wiersza polecenia z terminalu, najprostszym sposobem tworzenia szkieletów projektów funkcji opartych na języku Java jest użycie `Apache Maven` Archetypes. Środowisko Java Maven Archetype for Azure Functions jest publikowane w następującej grupie _GroupID_:_artifactId_: [com. Microsoft. Azure: Azure-Functions-Archetype](https://search.maven.org/artifact/com.microsoft.azure/azure-functions-archetype/). 

Następujące polecenie generuje nowy projekt funkcji Java przy użyciu tego Archetype:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-archetype
```

---

Aby rozpocząć korzystanie z tego Archetype, zobacz [Przewodnik Szybki Start dla języka Java](./create-first-function-cli-java.md).

## <a name="folder-structure"></a>Struktura folderów

Oto struktura folderów projektu Java Azure Functions:

```
FunctionsProject
 | - src
 | | - main
 | | | - java
 | | | | - FunctionApp
 | | | | | - MyFirstFunction.java
 | | | | | - MySecondFunction.java
 | - target
 | | - azure-functions
 | | | - FunctionApp
 | | | | - FunctionApp.jar
 | | | | - host.json
 | | | | - MyFirstFunction
 | | | | | - function.json
 | | | | - MySecondFunction
 | | | | | - function.json
 | | | | - bin
 | | | | - lib
 | - pom.xml
```

Aby skonfigurować aplikację funkcji, można użyć udostępnionej [host.js](functions-host-json.md) pliku. Każda funkcja ma własny plik kodu (. Java) i plik konfiguracji powiązania (function.json).

W projekcie można umieścić więcej niż jedną funkcję. Unikaj umieszczania funkcji w osobnych jarsach. `FunctionApp`Katalog docelowy jest wdrażany w aplikacji funkcji na platformie Azure.

## <a name="triggers-and-annotations"></a>Wyzwalacze i adnotacje

 Funkcje są wywoływane przez wyzwalacz, taki jak żądanie HTTP, czasomierz lub aktualizacja danych. Funkcja musi przetworzyć ten wyzwalacz i wszelkie inne dane wejściowe, aby utworzyć co najmniej jedno wyjście.

Użyj adnotacji języka Java zawartych w pakiecie [com. Microsoft. Azure. Functions. Annotation. *](/java/api/com.microsoft.azure.functions.annotation) , aby powiązać dane wejściowe i wyjściowe z metodami. Aby uzyskać więcej informacji, zobacz [dokumenty referencyjne języka Java](/java/api/com.microsoft.azure.functions.annotation).

> [!IMPORTANT] 
> Należy skonfigurować konto usługi Azure Storage w [local.settings.jsna](./functions-run-local.md#local-settings-file) potrzeby lokalnego uruchamiania usługi Azure Blob Storage, Azure queue storage lub Azure Table Storage.

Przykład:

```java
public class Function {
    public String echo(@HttpTrigger(name = "req", 
      methods = {HttpMethod.POST},  authLevel = AuthorizationLevel.ANONYMOUS) 
        String req, ExecutionContext context) {
        return String.format(req);
    }
}
```

Poniżej przedstawiono wygenerowane `function.json` przez [platformę Azure-Functions-Maven-plugin](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-maven-plugin):

```json
{
  "scriptFile": "azure-functions-example.jar",
  "entryPoint": "com.example.Function.echo",
  "bindings": [
    {
      "type": "httpTrigger",
      "name": "req",
      "direction": "in",
      "authLevel": "anonymous",
      "methods": [ "post" ]
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ]
}

```

## <a name="java-versions"></a>Wersje Java

Wersja języka Java używana podczas tworzenia aplikacji funkcji, na której działają funkcje na platformie Azure, jest określona w pliku pom.xml. Maven Archetype aktualnie generuje pom.xml dla języka Java 8, który można zmienić przed opublikowaniem. Wersja języka Java w pom.xml powinna być zgodna z wersją, w której aplikacja została opracowana lokalnie i przetestowana. 

### <a name="supported-versions"></a>Obsługiwane wersje

W poniższej tabeli przedstawiono bieżące obsługiwane wersje języka Java dla każdej wersji głównej środowiska uruchomieniowego funkcji, według systemu operacyjnego:

| Wersja funkcji | Wersje Java (Windows) | Wersje Java (Linux) |
| ----- | ----- | --- |
| wersji | 11 <br/>8 | 11 <br/>8 |
| 2.x | 8 | n/d |

O ile nie zostanie określona wersja języka Java dla danego wdrożenia, Maven Archetype domyślnie język Java 8 podczas wdrażania na platformie Azure.

### <a name="specify-the-deployment-version"></a>Określ wersję wdrożenia

Za pomocą parametru można kontrolować wersję języka Java wskazywaną przez Maven Archetype `-DjavaVersion` . Wartością tego parametru może być `8` lub `11` . 

Maven Archetype generuje pom.xml, który jest przeznaczony dla określonej wersji języka Java. Następujące elementy w pom.xml wskazują wersję języka Java do użycia:

| Element |  Wartość Java 8 | Wartość Java 11 | Opis |
| ---- | ---- | ---- | --- |
| **`Java.version`** | 1.8 | 11 | Wersja języka Java używana przez Maven-kompilator-plugin. |
| **`JavaVersion`** | 8 | 11 | Wersja języka Java hostowana przez aplikację funkcji na platformie Azure. |

W poniższych przykładach przedstawiono ustawienia dla języka Java 8 w odpowiednich sekcjach pom.xml pliku:

#### `Java.version`
:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="12-19" highlight="14":::

#### `JavaVersion`
:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="77-85" highlight="80":::

> [!IMPORTANT]
> Zmienna środowiskowa JAVA_HOME musi być poprawnie ustawiona dla katalogu JDK, który jest używany podczas kompilowania kodu przy użyciu Maven. Upewnij się, że wersja JDK jest co najmniej równa wartości `Java.version` Ustawienia. 

### <a name="specify-the-deployment-os"></a>Określ system operacyjny wdrożenia

Maven umożliwia również określenie systemu operacyjnego, w którym aplikacja funkcji działa na platformie Azure. Użyj `os` elementu, aby wybrać system operacyjny. 

| Element |  Windows | Linux | Docker |
| ---- | ---- | ---- | --- |
| **`os`** | windows | System | Docker |

Poniższy przykład przedstawia ustawienia systemu operacyjnego w `runtime` sekcji pliku pom.xml:

:::code language="xml" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/pom.xml" range="77-85" highlight="79":::
 
## <a name="jdk-runtime-availability-and-support"></a>Dostępność i obsługa środowiska uruchomieniowego JDK 

W przypadku lokalnego opracowywania aplikacji funkcji Java Pobierz odpowiednie oprogramowanie [Azul Zulu Enterprise for Azure](https://assets.azul.com/files/Zulu-for-Azure-FAQ.pdf) Java zestawy JDK z [systemów Azul](https://www.azul.com/downloads/azure-only/zulu/)i używaj go. Azure Functions używa środowiska uruchomieniowego języka Java JDK Azul podczas wdrażania aplikacji funkcji w chmurze.

[Pomoc techniczna systemu Azure](https://azure.microsoft.com/support/) dotycząca problemów z aplikacjami zestawy JDK i funkcji jest dostępna z [zakwalifikowanym planem pomocy technicznej](https://azure.microsoft.com/support/plans/).

## <a name="customize-jvm"></a>Dostosuj JVM

Funkcje umożliwiają dostosowanie maszyny wirtualnej Java (JVM) używanej do uruchamiania funkcji języka Java. [Następujące opcje JVM](https://github.com/Azure/azure-functions-java-worker/blob/master/worker.config.json#L7) są używane domyślnie:

* `-XX:+TieredCompilation`
* `-XX:TieredStopAtLevel=1`
* `-noverify` 
* `-Djava.net.preferIPv4Stack=true`
* `-jar`

W ustawieniu aplikacji o nazwie można podać dodatkowe argumenty `JAVA_OPTS` . Możesz dodać ustawienia aplikacji do aplikacji funkcji wdrożonej na platformie Azure w Azure Portal lub interfejsie wiersza polecenia platformy Azure.

> [!IMPORTANT]  
> W planie zużycia należy również dodać ustawienie WEBSITE_USE_PLACEHOLDER z wartością 0, aby dostosowanie działało. To ustawienie powoduje zwiększenie czasu zimnego uruchamiania funkcji języka Java.

### <a name="azure-portal"></a>Azure Portal

W [Azure Portal](https://portal.azure.com)Użyj [karty Ustawienia aplikacji](functions-how-to-use-azure-function-app-settings.md#settings) , aby dodać `JAVA_OPTS` ustawienie.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Możesz użyć polecenia [AZ functionapp config AppSettings Set](/cli/azure/functionapp/config/appsettings) , aby ustawić `JAVA_OPTS` , jak w poniższym przykładzie:

# <a name="consumption-plan"></a>[Plan Zużycie](#tab/consumption/bash)

```azurecli-interactive
az functionapp config appsettings set \
    --settings "JAVA_OPTS=-Djava.awt.headless=true" \
    "WEBSITE_USE_PLACEHOLDER=0" \
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

# <a name="consumption-plan"></a>[Plan Zużycie](#tab/consumption/cmd)

```azurecli-interactive
az functionapp config appsettings set ^
    --settings "JAVA_OPTS=-Djava.awt.headless=true" ^
    "WEBSITE_USE_PLACEHOLDER=0" ^
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

# <a name="dedicated-plan--premium-plan"></a>[Plan dedykowany/plan Premium](#tab/dedicated+premium/bash)

```azurecli-interactive
az functionapp config appsettings set \
    --settings "JAVA_OPTS=-Djava.awt.headless=true" \
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

# <a name="dedicated-plan--premium-plan"></a>[Plan dedykowany/plan Premium](#tab/dedicated+premium/cmd)

```azurecli-interactive
az functionapp config appsettings set ^
    --settings "JAVA_OPTS=-Djava.awt.headless=true" ^
    --name <APP_NAME> --resource-group <RESOURCE_GROUP>
```

---

Ten przykład włącza tryb bezobsługowy. Zastąp ciąg `<APP_NAME>` nazwą aplikacji funkcji i `<RESOURCE_GROUP>` grupą zasobów. 

## <a name="third-party-libraries"></a>Biblioteki innych firm 

Azure Functions obsługuje korzystanie z bibliotek innych firm. Domyślnie wszystkie zależności określone w pliku projektu są automatycznie umieszczane w `pom.xml` tym [`mvn package`](https://github.com/Microsoft/azure-maven-plugins/blob/master/azure-functions-maven-plugin/README.md#azure-functionspackage) celu. W przypadku bibliotek nieokreślonych jako zależności w `pom.xml` pliku umieść je w `lib` katalogu w katalogu głównym funkcji. Zależności umieszczane w `lib` katalogu są dodawane do modułu ładującego klasy systemu w czasie wykonywania.

`com.microsoft.azure.functions:azure-functions-java-library`Zależność jest domyślnie dostępna w ścieżce klasy i nie musi być uwzględniona w `lib` katalogu. Ponadto [platforma Azure-Functions-Java-Worker](https://github.com/Azure/azure-functions-java-worker) dodaje zależności wymienione [tutaj](https://github.com/Azure/azure-functions-java-worker/wiki/Azure-Java-Functions-Worker-Dependencies) do ścieżki klas.

## <a name="data-type-support"></a>Obsługa typu danych

Można używać zwykłych obiektów języka Java (Pojo), typów zdefiniowanych w `azure-functions-java-library` lub typów danych pierwotnych, takich jak String i Integer, do powiązania danych wejściowych lub wyjściowych.

### <a name="pojos"></a>Pojo

Do konwertowania danych wejściowych na POJO, [Azure-Functions-Java-Worker](https://github.com/Azure/azure-functions-java-worker) używa biblioteki [gson](https://github.com/google/gson) . Typy POJO używane jako dane wejściowe funkcji powinny być `public` .

### <a name="binary-data"></a>Dane binarne

Powiąż binarne dane wejściowe lub wyjściowe z `byte[]` , ustawiając `dataType` pole w function.jsna `binary` :

```java
   @FunctionName("BlobTrigger")
    @StorageAccount("AzureWebJobsStorage")
     public void blobTrigger(
        @BlobTrigger(name = "content", path = "myblob/{fileName}", dataType = "binary") byte[] content,
        @BindingName("fileName") String fileName,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Blob trigger function processed a blob.\n Name: " + fileName + "\n Size: " + content.length + " Bytes");
    }
```

Jeśli oczekujesz wartości null, użyj `Optional<T>` .

## <a name="bindings"></a>Powiązania

Powiązania danych wejściowych i wyjściowych zapewniają deklaratywny sposób nawiązywania połączenia z danymi z poziomu kodu. Funkcja może mieć wiele powiązań wejściowych i wyjściowych.

### <a name="input-binding-example"></a>Przykład powiązania danych wejściowych

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("echo")
    public static String echo(
        @HttpTrigger(name = "req", methods = { HttpMethod.PUT }, authLevel = AuthorizationLevel.ANONYMOUS, route = "items/{id}") String inputReq,
        @TableInput(name = "item", tableName = "items", partitionKey = "Example", rowKey = "{id}", connection = "AzureWebJobsStorage") TestInputData inputData,
        @TableOutput(name = "myOutputTable", tableName = "Person", connection = "AzureWebJobsStorage") OutputBinding<Person> testOutputData
    ) {
        testOutputData.setValue(new Person(httpbody + "Partition", httpbody + "Row", httpbody + "Name"));
        return "Hello, " + inputReq + " and " + inputData.getKey() + ".";
    }

    public static class TestInputData {
        public String getKey() { return this.RowKey; }
        private String RowKey;
    }
    public static class Person {
        public String PartitionKey;
        public String RowKey;
        public String Name;

        public Person(String p, String r, String n) {
            this.PartitionKey = p;
            this.RowKey = r;
            this.Name = n;
        }
    }
}
```

Wywołaj tę funkcję przy użyciu żądania HTTP. 
- Ładunek żądania HTTP jest przenoszona jako `String` argument dla argumentu `inputReq` .
- Jeden wpis jest pobierany z magazynu tabel i jest przenoszona `TestInputData` do argumentu `inputData` .

Aby otrzymać wsadowe dane wejściowe, można powiązać z `String[]` , `POJO[]` , `List<String>` , lub `List<POJO>` .

```java
@FunctionName("ProcessIotMessages")
    public void processIotMessages(
        @EventHubTrigger(name = "message", eventHubName = "%AzureWebJobsEventHubPath%", connection = "AzureWebJobsEventHubSender", cardinality = Cardinality.MANY) List<TestEventData> messages,
        final ExecutionContext context)
    {
        context.getLogger().info("Java Event Hub trigger received messages. Batch size: " + messages.size());
    }
    
    public class TestEventData {
    public String id;
}

```

Ta funkcja jest wyzwalana za każdym razem, gdy w skonfigurowanym centrum zdarzeń są nowe dane. Ponieważ `cardinality` jest ustawiona na `MANY` , funkcja otrzymuje partie komunikatów z centrum zdarzeń. `EventData` z centrum zdarzeń zostanie przekonwertowane na `TestEventData` potrzeby wykonywania funkcji.

### <a name="output-binding-example"></a>Przykład powiązania danych wyjściowych

Można powiązać powiązanie danych wyjściowych z wartością zwracaną przy użyciu `$return` . 

```java
package com.example;

import com.microsoft.azure.functions.annotation.*;

public class Function {
    @FunctionName("copy")
    @StorageAccount("AzureWebJobsStorage")
    @BlobOutput(name = "$return", path = "samples-output-java/{name}")
    public static String copy(@BlobTrigger(name = "blob", path = "samples-input-java/{name}") String content) {
        return content;
    }
}
```

Jeśli istnieje wiele powiązań wyjściowych, użyj wartości zwracanej tylko dla jednego z nich.

Aby wysłać wiele wartości wyjściowych, użyj `OutputBinding<T>` zdefiniowanej w `azure-functions-java-library` pakiecie. 

```java
@FunctionName("QueueOutputPOJOList")
    public HttpResponseMessage QueueOutputPOJOList(@HttpTrigger(name = "req", methods = { HttpMethod.GET,
            HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<Optional<String>> request,
            @QueueOutput(name = "itemsOut", queueName = "test-output-java-pojo", connection = "AzureWebJobsStorage") OutputBinding<List<TestData>> itemsOut, 
            final ExecutionContext context) {
        context.getLogger().info("Java HTTP trigger processed a request.");
       
        String query = request.getQueryParameters().get("queueMessageId");
        String queueMessageId = request.getBody().orElse(query);
        itemsOut.setValue(new ArrayList<TestData>());
        if (queueMessageId != null) {
            TestData testData1 = new TestData();
            testData1.id = "msg1"+queueMessageId;
            TestData testData2 = new TestData();
            testData2.id = "msg2"+queueMessageId;

            itemsOut.getValue().add(testData1);
            itemsOut.getValue().add(testData2);

            return request.createResponseBuilder(HttpStatus.OK).body("Hello, " + queueMessageId).build();
        } else {
            return request.createResponseBuilder(HttpStatus.INTERNAL_SERVER_ERROR)
                    .body("Did not find expected items in CosmosDB input list").build();
        }
    }

     public static class TestData {
        public String id;
    }
```

Wywołujesz tę funkcję na wywołaniu HttpRequest. Zapisuje wiele wartości do magazynu kolejek.

## <a name="httprequestmessage-and-httpresponsemessage"></a>HttpRequestMessage i HttpResponseMessage

 Są one zdefiniowane w `azure-functions-java-library` . Są to typy pomocnika do pracy z funkcjami HttpTrigger.

| Typ wyspecjalizowany      |       Cel        | Typowy sposób użycia                  |
| --------------------- | :-----------------: | ------------------------------ |
| `HttpRequestMessage<T>`  |    Wyzwalacz HTTP     | Pobiera metodę, nagłówki lub zapytania |
| `HttpResponseMessage` | Powiązanie danych wyjściowych HTTP | Zwraca stan inny niż 200   |

## <a name="metadata"></a>Metadane

Niektóre wyzwalacze wysyłają [metadane wyzwalacza](./functions-triggers-bindings.md) wraz z danymi wejściowymi. Możesz użyć adnotacji `@BindingName` do powiązania z metadanymi wyzwalacza.


```Java
package com.example;

import java.util.Optional;
import com.microsoft.azure.functions.annotation.*;


public class Function {
    @FunctionName("metadata")
    public static String metadata(
        @HttpTrigger(name = "req", methods = { HttpMethod.GET, HttpMethod.POST }, authLevel = AuthorizationLevel.ANONYMOUS) Optional<String> body,
        @BindingName("name") String queryValue
    ) {
        return body.orElse(queryValue);
    }
}
```
W poprzednim przykładzie, `queryValue` jest powiązany z parametrem ciągu zapytania `name` w adresie URL żądania HTTP, `http://{example.host}/api/metadata?name=test` . Oto inny przykład przedstawiający sposób powiązania `Id` z metadanymi wyzwalacza kolejki.

```java
 @FunctionName("QueueTriggerMetadata")
    public void QueueTriggerMetadata(
        @QueueTrigger(name = "message", queueName = "test-input-java-metadata", connection = "AzureWebJobsStorage") String message,@BindingName("Id") String metadataId,
        @QueueOutput(name = "output", queueName = "test-output-java-metadata", connection = "AzureWebJobsStorage") OutputBinding<TestData> output,
        final ExecutionContext context
    ) {
        context.getLogger().info("Java Queue trigger function processed a message: " + message + " with metadaId:" + metadataId );
        TestData testData = new TestData();
        testData.id = metadataId;
        output.setValue(testData);
    }
```

> [!NOTE]
> Nazwa podana w adnotacji musi być zgodna z właściwością metadanych.

## <a name="execution-context"></a>Kontekst wykonywania

`ExecutionContext`zdefiniowany w `azure-functions-java-library` , zawiera metody pomocnika do komunikowania się ze środowiskiem uruchomieniowym funkcji. Aby uzyskać więcej informacji, zobacz [artykuł dotyczący odwołania kontekście wykonywania](/java/api/com.microsoft.azure.functions.executioncontext).

### <a name="logger"></a>Rejestratora

Użycie `getLogger` , zdefiniowane w `ExecutionContext` , do pisania dzienników z kodu funkcji.

Przykład:

```java

import com.microsoft.azure.functions.*;
import com.microsoft.azure.functions.annotation.*;

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        if (req.isEmpty()) {
            context.getLogger().warning("Empty request body received by function " + context.getFunctionName() + " with invocation " + context.getInvocationId());
        }
        return String.format(req);
    }
}
```

## <a name="view-logs-and-trace"></a>Wyświetlanie dzienników i śledzenia

Interfejsu wiersza polecenia platformy Azure można użyć do przesyłania strumieniowego rejestrowania w języku Java stdout i stderr, a także do rejestrowania innych aplikacji. 

Poniżej przedstawiono sposób konfigurowania aplikacji funkcji do zapisywania rejestrowania aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli-interactive
az webapp log config --name functionname --resource-group myResourceGroup --application-logging true
```

---

Aby przesłać strumieniowo dane wyjściowe rejestrowania dla aplikacji funkcji przy użyciu interfejsu wiersza polecenia platformy Azure, Otwórz nowy wiersz poleceń, bash lub sesję terminala, a następnie wprowadź następujące polecenie:

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

---

Polecenie [AZ webapp log tail](/cli/azure/webapp/log) zawiera opcje umożliwiające filtrowanie danych wyjściowych przy użyciu `--provider` opcji. 

Aby pobrać pliki dzienników jako pojedynczy plik ZIP przy użyciu interfejsu wiersza polecenia platformy Azure, Otwórz nowy wiersz poleceń, bash lub sesję terminala, a następnie wprowadź następujące polecenie:

```azurecli-interactive
az webapp log download --resource-group resourcegroupname --name functionappname
```

Przed uruchomieniem tego polecenia należy włączyć rejestrowanie systemu plików w Azure Portal lub interfejsie wiersza polecenia platformy Azure.

## <a name="environment-variables"></a>Zmienne środowiskowe

W funkcjach, [Ustawienia aplikacji](functions-app-settings.md), takie jak parametry połączenia usługi, są ujawniane jako zmienne środowiskowe podczas wykonywania. Możesz uzyskać dostęp do tych ustawień przy użyciu, `System.getenv("AzureWebJobsStorage")` .

Poniższy przykład pobiera [ustawienie aplikacji](functions-how-to-use-azure-function-app-settings.md#settings)z kluczem o nazwie `myAppSetting` :

```java

public class Function {
    public String echo(@HttpTrigger(name = "req", methods = {HttpMethod.POST}, authLevel = AuthorizationLevel.ANONYMOUS) String req, ExecutionContext context) {
        context.getLogger().info("My app setting value: "+ System.getenv("myAppSetting"));
        return String.format(req);
    }
}

```

> [!NOTE]
> Wartość element appSetting FUNCTIONS_EXTENSION_VERSION powinna wynosić ~ 2 lub ~ 3 dla zoptymalizowanego zimnego środowiska uruchomieniowego.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat programowania w języku Java Azure Functions, zobacz następujące zasoby:

* [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
* [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
* [Azure Functions wyzwalacze i powiązania](functions-triggers-bindings.md)
* Lokalne programowanie i debugowanie za pomocą [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md)i [zaćmienie](functions-create-maven-eclipse.md)
* [Zdalne debugowanie funkcji Java przy użyciu Visual Studio Code](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud)
* [Wtyczka Maven dla Azure Functions](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-functions-maven-plugin/README.md) 
* Usprawnij Tworzenie funkcji za pomocą `azure-functions:add` celu i przygotuj katalog przemieszczania dla [wdrożenia pliku zip](deployment-zip-push.md).
