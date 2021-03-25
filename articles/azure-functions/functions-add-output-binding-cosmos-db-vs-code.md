---
title: Łączenie Azure Functions z Azure Cosmos DB przy użyciu Visual Studio Code
description: Dowiedz się, jak połączyć Azure Functions z kontem Azure Cosmos DB, dodając powiązanie danych wyjściowych do projektu Visual Studio Code.
author: ThomasWeiss
ms.date: 03/23/2021
ms.topic: quickstart
ms.author: thweiss
zone_pivot_groups: programming-languages-set-functions-temp
ms.openlocfilehash: 0a0c63ee54699185bcd02104b1a3f4d0070ea808
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023252"
---
# <a name="connect-azure-functions-to-azure-cosmos-db-using-visual-studio-code"></a>Łączenie Azure Functions z Azure Cosmos DB przy użyciu Visual Studio Code

[!INCLUDE [functions-add-storage-binding-intro](../../includes/functions-add-storage-binding-intro.md)]

W tym artykule pokazano, jak za pomocą Visual Studio Code połączyć się [Azure Cosmos DB](../cosmos-db/introduction.md) z funkcją utworzoną w poprzednim artykule Szybki Start. Powiązanie danych wyjściowych dodawane do tej funkcji zapisuje dane z żądania HTTP do dokumentu JSON przechowywanego w kontenerze Azure Cosmos DB. 

::: zone pivot="programming-language-csharp"
Przed rozpoczęciem należy wykonać kroki [przewodnika Szybki Start: Tworzenie funkcji języka C# na platformie Azure przy użyciu Visual Studio Code](create-first-function-vs-code-csharp.md). Jeśli przed końcem tego artykułu zostały już wyczyszczone zasoby, wykonaj kroki ponownie, aby utworzyć aplikację funkcji i powiązane zasoby na platformie Azure.
::: zone-end
::: zone pivot="programming-language-javascript"  
Przed rozpoczęciem należy ukończyć [Przewodnik Szybki Start: Tworzenie funkcji JavaScript na platformie Azure przy użyciu Visual Studio Code](create-first-function-vs-code-node.md). Jeśli przed końcem tego artykułu zostały już wyczyszczone zasoby, wykonaj kroki ponownie, aby utworzyć aplikację funkcji i powiązane zasoby na platformie Azure.  
::: zone-end

## <a name="configure-your-environment"></a>Konfigurowanie środowiska

Przed rozpoczęciem upewnij się, że Zainstaluj [rozszerzenie bazy danych platformy Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb) dla Visual Studio Code.

## <a name="create-your-azure-cosmos-db-account"></a>Utwórz konto Azure Cosmos DB

> [!IMPORTANT]
> [Azure Cosmos DB bezserwerowe](../cosmos-db/serverless.md) jest teraz dostępna w wersji zapoznawczej. Ten tryb oparty na zużyciu ułatwia Azure Cosmos DB silnej opcji obciążeń bezserwerowych. Aby użyć Azure Cosmos DB w trybie bezserwerowym, wybierz opcję **Bezserwerowa** w **trybie pojemności** podczas tworzenia konta.

1. W nowym oknie przeglądarki zaloguj się do witryny [Azure Portal](https://portal.azure.com/).

2. Kliknij kolejno pozycje **Utwórz zasób** > **Bazy danych** > **Azure Cosmos DB**.
   
    :::image type="content" source="../../includes/media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png" alt-text="Okienko Bazy danych w witrynie Azure Portal" border="true":::

3. Na stronie **Tworzenie konta Azure Cosmos DB** wprowadź ustawienia dla nowego konta Azure Cosmos DB. 
 
    Ustawienie|Wartość|Opis
    ---|---|---
    Subskrypcja|*Twoja subskrypcja*|Wybierz subskrypcję platformy Azure, w której utworzono aplikacja funkcji w [poprzednim artykule](./create-first-function-vs-code-csharp.md).
    Grupa zasobów|*Twoja grupa zasobów*|Wybierz grupę zasobów, w której utworzono aplikacja funkcji w [poprzednim artykule](./create-first-function-vs-code-csharp.md).
    Nazwa konta|*Wprowadź unikatową nazwę*|Wprowadź unikatową nazwę do identyfikacji konta usługi Azure Cosmos DB.<br><br>Nazwa konta może zawierać tylko małe litery, cyfry i łączniki (-) i musi mieć długość od 3 do 31 znaków.
    Interfejs API|Core (SQL)|Wybierz pozycję **rdzeń (SQL)** , aby utworzyć bazę danych dokumentów, którą można badać przy użyciu składni SQL. [Dowiedz się więcej o interfejsie API SQL Azure Cosmos DB](../cosmos-db/introduction.md).|
    Lokalizacja|*Wybierz region znajdujący się najbliżej Twojej lokalizacji*|Wybierz lokalizację geograficzną, w której będzie hostowane konto usługi Azure Cosmos DB. Użyj lokalizacji najbliższej użytkownikowi lub użytkownikom, aby uzyskać najszybszy dostęp do danych.
    Tryb wydajności|Przepływność bezserwerowa lub obsługa administracyjna|Wybierz opcję **Bezserwerowa** , aby utworzyć konto w trybie [bezserwerowym](../cosmos-db/serverless.md) . Wybierz opcję **zainicjowana przepływność** , aby utworzyć konto w trybie [przepływności aprowizacji](../cosmos-db/set-throughput.md) .<br><br>Wybierz opcję **bezserwerowe** , jeśli zaczynasz pracę z Azure Cosmos DB.

4. Kliknij pozycję **Przejrzyj i utwórz**. Możesz pominąć sekcje **Sieć** i **Tagi**. 

5. Przejrzyj informacje podsumowujące i kliknij pozycję **Utwórz**. 

6. Poczekaj na utworzenie nowego Azure Cosmos DB, a następnie wybierz pozycję **Przejdź do zasobu**.

    :::image type="content" source="../cosmos-db/media/create-cosmosdb-resources-portal/azure-cosmos-db-account-deployment-successful.png" alt-text="Tworzenie konta Azure Cosmos DB zostało zakończone" border="true":::

## <a name="create-an-azure-cosmos-db-database-and-container"></a>Tworzenie Azure Cosmos DB bazy danych i kontenera

Na koncie usługi Azure Cosmos DB wybierz pozycję **Eksplorator danych**, a następnie pozycję **nowy kontener**. Utwórz nową bazę danych o nazwie *My-Database*, nowy kontener o nazwie *My-Container* i wybierz `/id` jako [klucz partycji](../cosmos-db/partitioning-overview.md).

:::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/create-container.png" alt-text="Tworzenie nowego kontenera Azure Cosmos DB z Azure Portal" border="true":::

## <a name="update-your-function-app-settings"></a>Aktualizowanie ustawień aplikacji funkcji

W [poprzednim artykule szybki start](./create-first-function-vs-code-csharp.md)utworzono aplikację funkcji na platformie Azure. W tym artykule opisano aktualizowanie aplikacja funkcji w celu zapisywania dokumentów JSON w kontenerze Azure Cosmos DB utworzonym powyżej. Aby nawiązać połączenie z kontem Azure Cosmos DB, musisz dodać jego parametry połączenia do ustawień aplikacji. Następnie można pobrać nowe ustawienie do local.settings.jsw pliku, aby można było nawiązać połączenie z kontem Azure Cosmos DB w przypadku uruchamiania lokalnego.

1. W Visual Studio Code Znajdź właśnie utworzone konto Azure Cosmos DB. Kliknij prawym przyciskiem myszy jego nazwę, a następnie wybierz polecenie **Kopiuj parametry połączenia**.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/copy-connection-string.png" alt-text="Kopiowanie parametrów połączenia Azure Cosmos DB" border="true":::

1. Naciśnij klawisz <kbd>F1</kbd> , aby otworzyć paletę poleceń, a następnie wyszukaj i uruchom polecenie `Azure Functions: Add New Setting...` .

1. Wybierz aplikację funkcji utworzoną w poprzednim artykule. Podaj następujące informacje po wyświetleniu monitów:

    + **Wprowadź nową nazwę ustawienia aplikacji**: typ `CosmosDbConnectionString` .

    + **Wprowadź wartość dla "CosmosDbConnectionString"**: wklej parametry połączenia konta Azure Cosmos DB, jak wcześniej skopiowane.

1. Ponownie naciśnij klawisz <kbd>F1</kbd> , aby otworzyć paletę poleceń, a następnie wyszukaj i uruchom polecenie `Azure Functions: Download Remote Settings...` . 

1. Wybierz aplikację funkcji utworzoną w poprzednim artykule. Wybierz opcję **tak, aby** zastąpić istniejące ustawienia lokalne. 

## <a name="register-binding-extensions"></a>Rejestrowanie rozszerzeń do wiązania

Ponieważ używane jest Azure Cosmos DB powiązanie danych wyjściowych, przed uruchomieniem projektu należy zainstalować odpowiednie rozszerzenie powiązań. 

::: zone pivot="programming-language-csharp"

Z wyjątkiem wyzwalaczy HTTP i Timer, powiązania są implementowane jako pakiety rozszerzeń. Uruchom następujące polecenie [dotnet Add Package](/dotnet/core/tools/dotnet-add-package) w oknie terminalu, aby dodać pakiet rozszerzenia magazynu do projektu.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.CosmosDB
```

::: zone-end

::: zone pivot="programming-language-javascript"

Twój projekt został skonfigurowany do używania [zestawów rozszerzeń](functions-bindings-register.md#extension-bundles), które automatycznie instalują wstępnie zdefiniowany zestaw pakietów rozszerzenia. 

Użycie pakietów rozszerzeń jest włączone w host.jsw pliku w katalogu głównym projektu, który jest wyświetlany w następujący sposób:

:::code language="json" source="~/functions-quickstart-java/functions-add-output-binding-storage-queue/host.json":::

::: zone-end

Teraz można dodać powiązanie danych wyjściowych Azure Cosmos DB do projektu.

## <a name="add-an-output-binding"></a>Dodawanie powiązania danych wyjściowych

W funkcjach każdy typ powiązania wymaga `direction` , `type` i unikatowy `name` do zdefiniowania w function.jsna pliku. Sposób definiowania tych atrybutów zależy od języka aplikacji funkcji.

::: zone pivot="programming-language-csharp"

W projekcie biblioteki klas języka C# powiązania są zdefiniowane jako atrybuty powiązania w metodzie funkcji. *function.jsw* pliku wymaganym przez funkcje są generowane automatycznie na podstawie tych atrybutów.

Otwórz plik projektu *HttpExample. cs* i Dodaj następujący parametr do `Run` definicji metody:

```csharp
[CosmosDB(
    databaseName: "my-database",
    collectionName: "my-container",
    ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
```

`documentsOut`Parametr jest <T> typem IAsyncCollector, który reprezentuje kolekcję dokumentów JSON, która zostanie zapisywana do kontenera Azure Cosmos DB po zakończeniu działania funkcji. Określone atrybuty określa nazwę kontenera i nazwę jego nadrzędnej bazy danych. Parametry połączenia dla konta Azure Cosmos DB są ustawiane przez `ConnectionStringSettingAttribute` .

Definicja metody Run powinna teraz wyglądać następująco:  

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
```

::: zone-end

::: zone pivot="programming-language-javascript"

Atrybuty powiązań są definiowane bezpośrednio w function.jspliku. W zależności od typu powiązania mogą być wymagane dodatkowe właściwości. [Azure Cosmos DB konfiguracja wyjściowa](./functions-bindings-cosmosdb-v2-output.md#configuration) zawiera opis pól wymaganych do Azure Cosmos DB powiązania danych wyjściowych. Rozszerzenie ułatwia dodawanie powiązań do function.jspliku. 

Aby utworzyć powiązanie, kliknij prawym przyciskiem myszy (Ctrl + kliknięcie na macOS) `function.json` plik w folderze HttpTrigger i wybierz polecenie **Dodaj powiązanie...**. Postępuj zgodnie z monitami, aby zdefiniować następujące właściwości powiązań dla nowego powiązania:

| Monit | Wartość | Opis |
| -------- | ----- | ----------- |
| **Wybierz kierunek powiązania** | `out` | Powiązanie jest powiązaniem wyjściowym. |
| **Wybieranie powiązania z kierunkiem "out"** | `Azure Cosmos DB` | Powiązanie jest powiązaniem Azure Cosmos DB. |
| **Nazwa używana do identyfikowania tego powiązania w kodzie** | `outputDocument` | Nazwa, która identyfikuje parametr powiązania przywoływany w kodzie. |
| **Baza danych Cosmos DB, w której będą zapisywane dane** | `my-database` | Nazwa bazy danych Azure Cosmos DB zawierającej kontener docelowy. |
| **Kolekcja baz danych, w której będą zapisywane dane** | `my-container` | Nazwa kontenera Azure Cosmos DB, w którym będą zapisywane dokumenty JSON. |
| **W przypadku wartości true tworzy bazę danych i kolekcję usługi Cosmos DB** | `false` | Docelowa baza danych i kontener już istnieją. |
| **Wybierz ustawienie z "local.setting.json"** | `CosmosDbConnectionString` | Nazwa ustawienia aplikacji, które zawiera parametry połączenia dla konta Azure Cosmos DB. |
| **Klucz partycji (opcjonalny)** | *Pozostaw puste* | Wymagane tylko wtedy, gdy powiązanie danych wyjściowych tworzy kontener. |
| **Przepływność kolekcji (opcjonalna)** | *Pozostaw puste* | Wymagane tylko wtedy, gdy powiązanie danych wyjściowych tworzy kontener. |

Powiązanie jest dodawane do `bindings` tablicy w function.jsna, który powinien wyglądać następująco:

```json
{
    "type": "cosmosDB",
    "direction": "out",
    "name": "outputDocument",
    "databaseName": "my-database",
    "collectionName": "my-container",
    "createIfNotExists": "false",
    "connectionStringSetting": "CosmosDbConnectionString"
}
```

::: zone-end

## <a name="add-code-that-uses-the-output-binding"></a>Dodawanie kodu korzystającego z powiązania danych wyjściowych

::: zone pivot="programming-language-csharp"  

Dodaj kod, który używa `documentsOut` obiektu powiązania danych wyjściowych do utworzenia dokumentu JSON. Dodaj ten kod przed zwróceniem metody.

```csharp
if (!string.IsNullOrEmpty(name))
{
    // Add a JSON document to the output container.
    await documentsOut.AddAsync(new
    {
        // create a random ID
        id = System.Guid.NewGuid().ToString(),
        name = name
    });
}
```

W tym momencie funkcja powinna wyglądać w następujący sposób:

```csharp
[FunctionName("HttpExample")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
    [CosmosDB(
        databaseName: "my-database",
        collectionName: "my-container",
        ConnectionStringSetting = "CosmosDbConnectionString")]IAsyncCollector<dynamic> documentsOut,
    ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    if (!string.IsNullOrEmpty(name))
    {
        // Add a JSON document to the output container.
        await documentsOut.AddAsync(new
        {
            // create a random ID
            id = System.Guid.NewGuid().ToString(),
            name = name
        });
    }

    string responseMessage = string.IsNullOrEmpty(name)
        ? "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response."
        : $"Hello, {name}. This HTTP triggered function executed successfully.";

    return new OkObjectResult(responseMessage);
}
```

::: zone-end

::: zone pivot="programming-language-javascript"  

Dodaj kod, który używa `outputDocument` obiektu powiązania danych wyjściowych w `context.bindings` celu utworzenia dokumentu JSON. Dodaj ten kod przed `context.res` instrukcją.

```javascript
if (name) {
    context.bindings.outputDocument = JSON.stringify({
        // create a random ID
        id: new Date().toISOString() + Math.random().toString().substr(2,8),
        name: name
    });
}
```

W tym momencie funkcja powinna wyglądać w następujący sposób:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    const name = (req.query.name || (req.body && req.body.name));
    const responseMessage = name
        ? "Hello, " + name + ". This HTTP triggered function executed successfully."
        : "This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.";

    if (name) {
        context.bindings.outputDocument = JSON.stringify({
            // create a random ID
            id: new Date().toISOString() + Math.random().toString().substr(2,8),
            name: name
        });
    }

    context.res = {
        // status: 200, /* Defaults to 200 */
        body: responseMessage
    };
}
```

::: zone-end  

## <a name="run-the-function-locally"></a>Lokalne uruchamianie funkcji

1. Tak jak w poprzednim artykule, naciśnij klawisz <kbd>F5</kbd> , aby uruchomić program Aplikacja funkcji i podstawowe narzędzia. 

1. Po uruchomieniu podstawowych narzędzi przejdź do obszaru **Azure: Functions** . W obszarze **funkcje** rozwiń pozycję funkcje **projektu lokalnego**  >  . Kliknij prawym przyciskiem myszy (Ctrl-kliknij na Mac) `HttpExample` funkcję i wybierz pozycję **Wykonaj funkcję teraz...**.

    :::image type="content" source="../../includes/media/functions-run-function-test-local-vs-code/execute-function-now.png" alt-text="Wykonaj funkcję teraz z Visual Studio Code":::

1. W obszarze **wprowadź treść żądania** zobaczysz wartość treść komunikatu żądania `{ "name": "Azure" }` . Naciśnij klawisz ENTER, aby wysłać ten komunikat żądania do funkcji.  
 
1. Po zwróceniu odpowiedzi naciśnij <kbd>klawisze CTRL + C</kbd> , aby zatrzymać podstawowe narzędzia.

### <a name="verify-that-a-json-document-has-been-created"></a>Sprawdź, czy dokument JSON został utworzony

1. Na Azure Portal Wróć do swojego konta Azure Cosmos DB i wybierz pozycję **Eksplorator danych**.

1. Rozwiń bazę danych i kontener, a następnie wybierz pozycję **elementy** , aby wyświetlić dokumenty utworzone w kontenerze.

1. Sprawdź, czy nowy dokument JSON został utworzony przez powiązanie danych wyjściowych.

    :::image type="content" source="./media/functions-add-output-binding-cosmos-db-vs-code/verify-output.png" alt-text="Weryfikowanie, czy nowy dokument został utworzony w kontenerze Azure Cosmos DB" border="true":::

## <a name="redeploy-and-verify-the-updated-app"></a>Wdróż ponownie i sprawdź zaktualizowaną aplikację

1. W Visual Studio Code naciśnij klawisz F1, aby otworzyć paletę poleceń. W palecie poleceń Wyszukaj i wybierz `Azure Functions: Deploy to function app...` .

1. Wybierz aplikację funkcji utworzoną w pierwszym artykule. Ponieważ wdrażasz ponownie projekt w tej samej aplikacji, wybierz pozycję **Wdróż** , aby odrzucić ostrzeżenie o zastąpieniu plików.

1. Po zakończeniu wdrażania możesz ponownie użyć funkcji **Wykonaj teraz...** , aby wyzwolić funkcję na platformie Azure.

1. Ponownie [Sprawdź dokumenty utworzone w kontenerze Azure Cosmos DB](#verify-that-a-json-document-has-been-created) , aby sprawdzić, czy powiązanie danych wyjściowych ponownie generuje nowy dokument JSON.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Na platformie Azure *zasoby* odnoszą się do aplikacji funkcji, funkcji, kont magazynu i tak dalej. Są one pogrupowane w *grupy zasobów*, a wszystkie elementy w grupie można usunąć, usuwając grupę.

Aby ukończyć te przewodniki Szybki start, zostały utworzone zasoby. Za te zasoby może zostać naliczona opłata — zależy to od Twojego [stanu konta](https://azure.microsoft.com/account/) i [cennika usług](https://azure.microsoft.com/pricing/). Jeśli nie potrzebujesz już tych zasobów, oto jak możesz je usunąć:

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]

## <a name="next-steps"></a>Następne kroki

Funkcja wyzwalana przez protokół HTTP została zaktualizowana w celu zapisywania dokumentów JSON w kontenerze Azure Cosmos DB. Teraz można dowiedzieć się więcej na temat opracowywania funkcji przy użyciu Visual Studio Code:

+ [Opracowywanie Azure Functions przy użyciu Visual Studio Code](functions-develop-vs-code.md)

+ [Azure Functions wyzwalacze i powiązania](functions-triggers-bindings.md).
::: zone pivot="programming-language-csharp"  
+ [Przykłady kompletnych projektów funkcji w języku C#](/samples/browse/?products=azure-functions&languages=csharp).

+ [Dokumentacja dla deweloperów Azure Functions C#](functions-dotnet-class-library.md)  
::: zone-end 
::: zone pivot="programming-language-javascript"  
+ [Przykłady kompletnych projektów funkcji w języku JavaScript](/samples/browse/?products=azure-functions&languages=javascript).

+ [Przewodnik dla deweloperów Azure Functions JavaScript](functions-reference-node.md)  
::: zone-end  