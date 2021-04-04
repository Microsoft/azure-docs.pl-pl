---
title: 'Samouczek: wysyłanie wiadomości e-mail z Logic Apps'
description: Dowiedz się, jak wywoływać procesy biznesowe z poziomu aplikacji App Service. Wysyłać wiadomości e-mail, tweety i wpisy w serwisie Facebook, dodawać do list wysyłkowych i wiele innych.
ms.topic: tutorial
ms.date: 04/08/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 8072a941cd89290af3e25cc63c4fccccce705df9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95014666"
---
# <a name="tutorial-send-email-and-invoke-other-business-processes-from-app-service"></a>Samouczek: wysyłanie wiadomości e-mail i wywoływanie innych procesów biznesowych z App Service

W tym samouczku dowiesz się, jak zintegrować aplikację App Service z procesami biznesowymi. Jest to typowe scenariusze dotyczące aplikacji sieci Web, takie jak:

- Wyślij wiadomość e-mail z potwierdzeniem dla transakcji
- Dodawanie użytkownika do grupy w usłudze Facebook
- Łączenie z systemami innych firm, takimi jak SAP, Salesforce itd.
- Program Exchange Standard — komunikaty B2B

W tym samouczku będziesz wysyłać wiadomości e-mail z użyciem usługi Gmail z aplikacji App Service przy użyciu [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Istnieją inne sposoby wysyłania wiadomości e-mail z aplikacji sieci Web, takiej jak konfiguracja SMTP udostępniona przez strukturę języka. Jednak Logic Apps zapewnia znacznie większą moc do aplikacji App Service bez konieczności zwiększania złożoności kodu. Logic Apps udostępnia prosty interfejs konfiguracji dla najpopularniejszych integracji z firmą, a aplikacja może wywoływać je w dowolnym momencie za pomocą żądania HTTP.

## <a name="prerequisite"></a>Wymaganie wstępne

Wdróż aplikację z wybranym przez Ciebie platformą języka, aby App Service. Aby zapoznać się z samouczkiem dotyczącym wdrażania przykładowej aplikacji, zobacz poniżej:

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

[Samouczek: Tworzenie aplikacji ASP.NET na platformie Azure przy użyciu usługi SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

[Samouczek: Tworzenie aplikacji ASP.NET Core i SQL Database w Azure App Service](tutorial-dotnetcore-sqldb-app.md)

### <a name="nodejs"></a>[Node.js](#tab/node)

[Samouczek: Tworzenie aplikacji Node.js i MongoDB na platformie Azure](tutorial-nodejs-mongodb-app.md)

### <a name="php"></a>[PHP](#tab/php)

[Samouczek: Tworzenie aplikacji PHP i MySQL na platformie Azure](tutorial-php-mysql-app.md)

### <a name="python"></a>[Python](#tab/python)

[Samouczek: uruchamianie aplikacji sieci Web języka Python (Django) za pomocą PostgreSQL w Azure App Service](tutorial-python-postgresql-app.md)

### <a name="ruby"></a>[Ruby](#tab/ruby)

[Tworzenie aplikacji w języku Ruby i korzystającej z bazy danych Postgres w usłudze Azure App Service w systemie Linux](tutorial-ruby-postgres-app.md)

---

## <a name="create-the-logic-app"></a>Tworzenie aplikacji logiki

1. W [Azure Portal](https://portal.azure.com)Utwórz pustą aplikację logiki, postępując zgodnie z instrukcjami w temacie [Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app). Gdy zobaczysz **projektanta Logic Apps**, Wróć do tego samouczka.
1. Na stronie powitalnej programu Logic Apps Designer wybierz opcję **gdy żądanie HTTP zostanie odebrane** w polu **Rozpocznij ze wspólnym wyzwalaczem**.

    ![Zrzut ekranu pokazujący stronę powitalną dla projektanta Logic Apps z wyświetloną żądaniem H T t P.](./media/tutorial-send-email/receive-http-request.png)
1. W oknie dialogowym dla **momentu odebrania żądania HTTP** wybierz pozycję **Użyj przykładowego ładunku do wygenerowania schematu**.

    ![Zrzut ekranu, na którym jest wyświetlane okno dialogowe żądanie H T t P, a w polu Użyj przykładowego ładunku do wygenerowania wybranego schematu opion. ](./media/tutorial-send-email/generate-schema-with-payload.png)

1. Skopiuj następujący przykładowy kod JSON do pola tekstowego i wybierz pozycję **gotowe**.

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

    Schemat jest teraz generowany dla żądanych danych żądania. W ramach tej usługi można po prostu przechwycić rzeczywiste dane żądania generowane przez kod aplikacji i umożliwić systemowi Azure generowanie schematu JSON. 
1. W górnej części projektanta Logic Apps wybierz pozycję **Zapisz**. 

    Teraz można sprawdzić adres URL wyzwalacza żądania HTTP. Wybierz ikonę kopiowania, aby skopiować ją do późniejszego użycia.

    ![Zrzut ekranu, który podświetla ikonę kopiowania, aby skopiować U R L wyzwalacza żądania H T t P.](./media/tutorial-send-email/http-request-url.png)

    Ta definicja żądania HTTP jest wyzwalaczem do wszystkich elementów, które chcesz wykonać w tej aplikacji logiki, będzie to Gmail lub jakakolwiek inna. Później ten adres URL zostanie wywołany w aplikacji App Service. Aby uzyskać więcej informacji na temat wyzwalacza żądania, zobacz [odwołanie do żądania HTTP/odpowiedzi](../connectors/connectors-native-reqres.md).

1. W dolnej części okna projektanta kliknij pozycję **nowy krok**, wpisz **Gmail** w polu wyszukiwania akcje i Znajdź i wybierz pozycję **Wyślij wiadomość e-mail (wersja 2)**.
    
    > [!TIP]
    > Można wyszukiwać inne typy integracji, takie jak SendGrid, MailChimp, Microsoft 365 i SalesForce. Aby uzyskać więcej informacji, zobacz [dokumentację Logic Apps](../logic-apps/index.yml).

1. W oknie dialogowym usługi **Gmail** wybierz pozycję **Zaloguj** się i zaloguj się na koncie usługi Gmail, z którego chcesz wysłać wiadomość e-mail.

    ![Zrzut ekranu przedstawiający okno dialogowe Gmail używane do logowania się do konta usługi Gmail, z którego chcesz wysłać wiadomość e-mail.](./media/tutorial-send-email/gmail-sign-in.png)

1. Po zalogowaniu kliknij w polu tekstowym **do** , a okno dialogowe zawartość dynamiczna zostanie automatycznie otwarte.

1. Obok akcji **po odebraniu żądania HTTP** wybierz pozycję **Zobacz więcej**.

    ![Zrzut ekranu pokazujący przycisk Zobacz więcej obok momentu odebrania żądania H T t P.](./media/tutorial-send-email/expand-dynamic-content.png)

    Teraz powinny zostać wyświetlone trzy właściwości z przykładowych danych JSON użytych wcześniej. W tym kroku użyjesz tych właściwości z żądania HTTP w celu utworzenia wiadomości e-mail.
1. Ponieważ wybierasz wartość pola **do** , wybierz pozycję **e-mail**. Jeśli chcesz, przełącz się do okna dialogowego zawartość dynamiczna, klikając pozycję **Dodaj zawartość dynamiczną**.

    ![Zrzut ekranu pokazujący opcję poczty e-mail i opcję Dodaj zawartość dynamiczną.](./media/tutorial-send-email/hide-dynamic-content.png)

1. Z listy rozwijanej **Dodaj nowy parametr** wybierz pozycję **temat** i **treść**.

1. Kliknij pole tekstowe **temat** , a w ten sam sposób wybierz **zadanie**. Po umieszczeniu kursora w polu **tematu** wpisz polecenie *utworzony*. 

1. Kliknij pozycję w **treści** i w ten sam sposób wybierz pozycję **termin**. Przesuń **kursor z lewej strony i wpisz** *ten element roboczy*.

    > [!TIP]
    > Jeśli chcesz edytować zawartość HTML bezpośrednio w treści wiadomości e-mail, wybierz pozycję **Widok kodu** w górnej części okna projektanta Logic Apps. Upewnij się, że zachowasz dynamiczny kod zawartości (na przykład `@{triggerBody()?['due']}` ).
    >
    > ![Zrzut ekranu pokazujący widok kodu służący do wyświetlania zawartości H T M w treści wiadomości e-mail.](./media/tutorial-send-email/edit-rich-html-email.png) 

1. Następnie Dodaj asynchroniczną odpowiedź HTTP do wyzwalacza HTTP. Między wyzwalaczem HTTP i akcją Gmail kliknij **+** znak i wybierz pozycję **Dodaj gałąź równoległą**.

    ![Zrzut ekranu, który pokazuje wyróżnioną opcję + i Dodaj gałąź równoległą.](./media/tutorial-send-email/add-http-response.png)

1. W polu wyszukiwania Wyszukaj **odpowiedź**, a następnie wybierz akcję **odpowiedź** .

    ![Zrzut ekranu, który pokazuje wyróżniony pasek wyszukiwania i akcję odpowiedzi.](./media/tutorial-send-email/choose-response-action.png)

    Domyślnie akcja odpowiedzi wysyła komunikat HTTP 200. Jest to wystarczające dla tego samouczka. Aby uzyskać więcej informacji, zobacz [informacje na temat żądania HTTP/odpowiedzi](../connectors/connectors-native-reqres.md).

1. W górnej części projektanta Logic Apps wybierz pozycję **Zapisz** ponownie. 

## <a name="add-http-request-code-to-app"></a>Dodawanie kodu żądania HTTP do aplikacji

Upewnij się, że został skopiowany adres URL wyzwalacza żądania HTTP ze starszej wersji. Ponieważ zawiera informacje poufne, najlepszym rozwiązaniem jest, aby nie umieścić go bezpośrednio w kodzie. Za pomocą App Service można odwołać się do niego jako zmienna środowiskowa, przy użyciu ustawień aplikacji. 

W [Cloud Shell](https://shell.azure.com)Utwórz ustawienie aplikacji przy użyciu następującego polecenia (Zastąp *\<app-name>* , *\<resource-group-name>* i *\<logic-app-url>* ):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings LOGIC_APP_URL="<your-logic-app-url>"
```

W kodzie wprowadź standardowe HTTP post na adres URL przy użyciu dowolnego języka klienta HTTP dostępnego dla struktury języka, z następującą konfiguracją:

- Treść żądania zawiera ten sam format JSON, który został dostarczony do aplikacji logiki:

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

- Żądanie zawiera nagłówek `Content-Type: application/json` . 
- Aby zoptymalizować wydajność, należy wysłać żądanie asynchronicznie, jeśli to możliwe.

Kliknij kartę preferowany język/struktura poniżej, aby zobaczyć przykład.

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

W ASP.NET można wysłać wpis HTTP przy użyciu klasy [System .NET. http. HttpClient](/dotnet/api/system.net.http.httpclient) . Na przykład:

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // requires using System.Configuration;
    ConfigurationManager.AppSettings["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

Jeśli testujesz ten kod w przykładowej aplikacji na potrzeby [samouczka: Tworzenie aplikacji ASP.NET na platformie Azure przy użyciu SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md), możesz użyć jej do wysłania potwierdzenia wiadomości e-mail w ramach [akcji tworzenia](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/blob/master/DotNetAppSqlDb/Controllers/TodosController.cs#L52-L63)po `Todo` dodaniu elementu. Aby użyć kodu asynchronicznego powyżej, przekonwertuj akcję tworzenia na asynchronicznie.

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

W ASP.NET Core można wysłać wpis HTTP przy użyciu klasy [System .NET. http. HttpClient](/dotnet/api/system.net.http.httpclient) . Na przykład:

```csharp
// requires using System.Net.Http;
var client = new HttpClient();
// requires using System.Text.Json;
var jsonData = JsonSerializer.Serialize(new
{
    email = "a-valid@emailaddress.com",
    due = "4/1/2020",
    task = "My new task!"
});

HttpResponseMessage result = await client.PostAsync(
    // Requires DI configuration to access app settings. See https://docs.microsoft.com/azure/app-service/configure-language-dotnetcore#access-environment-variables
    _configuration["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

> [!NOTE]
> Ten kod jest przeznaczony dla prostoty demonstracji. W programie nie można utworzyć wystąpienia `HttpClient` obiektu dla każdego żądania. Postępuj zgodnie ze wskazówkami w temacie [use IHttpClientFactory, aby zaimplementować odporne żądania HTTP](/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).

Jeśli testujesz ten kod w przykładowej aplikacji na potrzeby [samouczka: tworzenie ASP.NET Core i SQL Database aplikacji w Azure App Service](tutorial-dotnetcore-sqldb-app.md), możesz użyć jej do wysłania potwierdzenia wiadomości e-mail w ramach [akcji tworzenia](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial/blob/master/Controllers/TodosController.cs#L56-L65)po `Todo` dodaniu elementu.

### <a name="nodejs"></a>[Node.js](#tab/node)

W Node.js można łatwo wysyłać pocztę HTTP przy użyciu pakietu npm, takiego jak [Axios](https://www.npmjs.com/package/axios). Na przykład:

```javascript
// Requires npm install --save axios
const axios = require('axios');

var jsonData = {
        email: "a-valid@emailaddress.com",
        due: "4/1/2020",
        task: "My new task!"
};

(async function(data) {
    try {
        const response = await axios.post(process.env.LOGIC_APP_URL, jsonData);
        console.log(response.status);
    } catch (error) {
        console.log(error);
    }
})(jsonData);

```

Jeśli testujesz ten kod w przykładowej aplikacji na potrzeby [samouczka: Tworzenie aplikacji Node.js i MongoDB na platformie Azure](tutorial-nodejs-mongodb-app.md), możesz użyć jej do wysłania potwierdzenia wiadomości e-mail w [funkcji tworzenia](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L14-L27)po [pomyślnym zapisaniu artykułu](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L24).

### <a name="php"></a>[PHP](#tab/php)

W języku PHP można łatwo wysyłać pocztę HTTP przy użyciu [Guzzle](http://docs.guzzlephp.org/en/stable/index.html). Na przykład:

```php
// Requires composer require guzzlehttp/guzzle:~6.0
use GuzzleHttp\Client;
...
$client = new Client();
$options = [
    'json' => [ 
        'email' => 'a-valid@emailaddress.com',
        'due' => '4/1/2020',
        'task' => "My new task!"
    ]
];

$promise = $client-> postAsync(getenv($LOGIC_APP_URL), $options)->then( 
    function ($response) {
        return $response->getStatusCode();
    }, function ($exception) {
        return $exception->getResponse();
    }
);

$response = $promise->wait();
// Requires Laravel to run Log::info(). Check the documentation of your preferred framework for logging instructions.
Log::info(print_r($response, TRUE));
```

Jeśli testujesz ten kod w przykładowej aplikacji na potrzeby [samouczka: Tworzenie aplikacji PHP i MySQL na platformie Azure](tutorial-php-mysql-app.md), możesz użyć jej do wysłania potwierdzenia wiadomości e-mail w [funkcji Route::p Ost](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48), tuż przed instrukcją Return.

### <a name="python"></a>[Python](#tab/python)

W języku Python można łatwo wysyłać pocztę HTTP przy użyciu [żądań](https://pypi.org/project/requests/). Na przykład:

```python
# Requires pip install requests && pip freeze > requirements.txt
import requests
...
payload = {
    "email": "a-valid@emailaddress.com",
    "due": "4/1/2020",
    "task": "My new task!"
}
response = requests.post("https://prod-112.westeurope.logic.azure.com:443/workfl$
print(response.status_code)
```
<!-- ```python
# Requires pip install aiohttp && pip freeze > requirements.txt
import aiohttp
...
payload = {
        'email': 'a-valid@emailaddress.com',
        'due': '4/1/2020',
        'task': 'My new task!'
}
async with aiohttp.post('http://httpbin.org/post', data=json.dump(payload)) as resp:
    print(await resp.status())
``` -->

Jeśli testujesz ten kod w przykładowej aplikacji na potrzeby [samouczka: Uruchom aplikację sieci Web języka Python (Django) z PostgreSQL w Azure App Service](tutorial-python-postgresql-app.md), możesz użyć jej do wysłania potwierdzenia wiadomości e-mail w [funkcji Route::p Ost](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48), tuż przed instrukcją Return.

### <a name="ruby"></a>[Ruby](#tab/ruby)

W języku Ruby można łatwo wysyłać pocztę HTTP przy użyciu JSONClient. Na przykład:

```ruby
clnt = JSONClient.new
body = { 
    'email' => 'a-valid@emailaddress.com',
    'due' => '4/1/2020',
    'task' => "My new task!"
}

connection = clnt.post_async(ENV['LOGIC_APP_URL'], body)
```

Jeśli testujesz ten kod w przykładowej aplikacji na potrzeby [tworzenia aplikacji Ruby i Postgres w Azure App Service w systemie Linux](tutorial-ruby-postgres-app.md), możesz użyć jej do wysłania potwierdzenia wiadomości e-mail w ramach akcji [tworzenia](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L26-L38) , [gdy @task.save powiedzie się](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L30).

---

## <a name="more-resources"></a>Więcej zasobów

[Samouczek: hostowanie interfejsu API RESTful z mechanizmem CORS w usłudze Azure App Service](app-service-web-tutorial-rest-api.md)  
[Odwołanie żądania HTTP/odpowiedzi dla Logic Apps](../connectors/connectors-native-reqres.md)  
[Szybki Start: Tworzenie pierwszego przepływu pracy przy użyciu Azure Logic Apps-Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)