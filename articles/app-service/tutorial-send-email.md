---
title: 'Samouczek: wysyłanie wiadomości e-mail przy użyciu Logic Apps'
description: Dowiedz się, jak wywoływać procesy biznesowe z App Service aplikacji. Wysyłaj wiadomości e-mail, tweety i wpisy w serwisie Facebook, dodawaj je do list adresowych i nie tylko.
ms.topic: tutorial
ms.date: 04/08/2020
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 6b15c09162146de7aef8abfcdba40627eb77621c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830938"
---
# <a name="tutorial-send-email-and-invoke-other-business-processes-from-app-service"></a>Samouczek: wysyłanie wiadomości e-mail i wywoływanie innych procesów biznesowych z App Service

Z tego samouczka dowiesz się, jak zintegrować aplikację App Service z procesami biznesowymi. Jest to typowe dla scenariuszy aplikacji internetowych, takich jak:

- Wysyłanie wiadomości e-mail z potwierdzeniem transakcji
- Dodawanie użytkownika do grupy serwisu Facebook
- Połącz się z systemami innych firm, np. SAP, Salesforce itp.
- Wymiana standardowych komunikatów B2B

W tym samouczku będziesz wysyłać wiadomości e-mail za pomocą usługi Gmail z aplikacji App Service przy użyciu [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Istnieją inne sposoby wysyłania wiadomości e-mail z aplikacji internetowej, takie jak konfiguracja SMTP dostarczana przez platformę językową. Jednak Logic Apps znacznie więcej mocy w aplikacji App Service bez dodawania złożoności do kodu. Logic Apps udostępnia prosty interfejs konfiguracji dla najpopularniejszych integracji biznesowych, a aplikacja może je wywołać w dowolnym momencie za pomocą żądania HTTP.

## <a name="prerequisite"></a>Wymaganie wstępne

Wd wdrażaj aplikację przy użyciu wybranej struktury językowej, aby App Service. Aby wykonać kroki samouczka w celu wdrożenia przykładowej aplikacji, zobacz poniżej:

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

[Samouczek: Tworzenie aplikacji ASP.NET na platformie Azure przy użyciu usługi SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

[Samouczek: tworzenie aplikacji ASP.NET Core i SQL Database w Azure App Service](tutorial-dotnetcore-sqldb-app.md)

### <a name="nodejs"></a>[Node.js](#tab/node)

[Samouczek: tworzenie aplikacji Node.js MongoDB na platformie Azure](tutorial-nodejs-mongodb-app.md)

### <a name="php"></a>[PHP](#tab/php)

[Samouczek: tworzenie aplikacji PHP i MySQL na platformie Azure](tutorial-php-mysql-app.md)

### <a name="python"></a>[Python](#tab/python)

[Samouczek: uruchamianie aplikacji internetowej Python (Django) przy użyciu bazy danych PostgreSQL w Azure App Service](tutorial-python-postgresql-app.md)

### <a name="ruby"></a>[Ruby](#tab/ruby)

[Tworzenie aplikacji w języku Ruby i korzystającej z bazy danych Postgres w usłudze Azure App Service w systemie Linux](tutorial-ruby-postgres-app.md)

---

## <a name="create-the-logic-app"></a>Tworzenie aplikacji logiki

1. W [Azure Portal](https://portal.azure.com)utwórz pustą aplikację logiki, zgodnie z instrukcjami [w tece Create your logic app (Tworzenie aplikacji logiki).](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app) Gdy zostanie wyświetlony Logic Apps **Designer**, wróć do tego samouczka.
1. Na stronie powitalnego Logic Apps Designer wybierz pozycję Po otrzymaniu żądania **HTTP** w obszarze **Rozpocznij od typowego wyzwalacza**.

    ![Zrzut ekranu przedstawiający stronę powitaną projektanta Logic Apps z wyróżniona lewą stroną.](./media/tutorial-send-email/receive-http-request.png)
1. W oknie dialogowym **Po otrzymaniu żądania HTTP** wybierz pozycję Użyj **przykładowego ładunku do wygenerowania schematu**.

    ![Zrzut ekranu przedstawiający okno dialogowe When an H T T P request (Kiedy żądanie H T T P) i czy wybrano opcję Use sample payload to generate schema opion (Użyj przykładowego ładunku do wygenerowania schematu). ](./media/tutorial-send-email/generate-schema-with-payload.png)

1. Skopiuj następujący przykładowy kod JSON do pola tekstowego i wybierz pozycję **Gotowe.**

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

    Schemat jest teraz generowany dla danych żądania, które chcesz. W praktyce możesz po prostu przechwycić rzeczywiste dane żądania wygenerowane przez kod aplikacji i pozwolić platformie Azure wygenerować schemat JSON. 
1. W górnej części ekranu Logic Apps Projektant wybierz pozycję **Zapisz.** 

    Teraz możesz zobaczyć adres URL wyzwalacza żądania HTTP. Wybierz ikonę kopiowania, aby skopiować ją do późniejszego użycia.

    ![Zrzut ekranu przedstawiający ikonę kopiowania w celu skopiowania pliku R L wyzwalacza żądania H T T P.](./media/tutorial-send-email/http-request-url.png)

    Ta definicja żądania HTTP jest wyzwalaczem dla wszystkiego, co chcesz zrobić w tej aplikacji logiki, na przykład gmail lub cokolwiek innego. Później wywołasz ten adres URL w aplikacji App Service aplikacji. Aby uzyskać więcej informacji na temat wyzwalacza żądania, zobacz informacje [dotyczące żądania/odpowiedzi HTTP](../connectors/connectors-native-reqres.md).

1. W dolnej części projektanta kliknij pozycję **Nowy krok,** wpisz **Gmail** w polu wyszukiwania akcji, a następnie znajdź i wybierz pozycję Wyślij wiadomość e-mail **(V2).**
    
    > [!TIP]
    > Możesz wyszukiwać inne typy integracji, takie jak SendGrid, MailChimp, Microsoft 365 i SalesForce. Aby uzyskać więcej informacji, [zobacz Logic Apps dokumentacji](../logic-apps/index.yml).

1. W **oknie dialogowym Gmail** wybierz pozycję **Zaloguj** się i zaloguj się do konta Gmail, z którego chcesz wysłać wiadomość e-mail.

    ![Zrzut ekranu przedstawiający okno dialogowe Usługi Gmail służące do logowania się do konta Gmail, z którego chcesz wysyłać wiadomości e-mail.](./media/tutorial-send-email/gmail-sign-in.png)

1. Po zalogowaniu kliknij pole tekstowe **Do,** aby automatycznie otworzyć okno dialogowe zawartości dynamicznej.

1. Obok akcji **Po otrzymaniu żądania HTTP** wybierz pozycję Zobacz **więcej**.

    ![Zrzut ekranu przedstawiający przycisk Zobacz więcej obok akcji Po otrzymaniu żądania H T T P.](./media/tutorial-send-email/expand-dynamic-content.png)

    Powinny zostać teraz wyświetlonych trzy właściwości z przykładowych danych JSON, których używaliśmy wcześniej. W tym kroku użyjemy tych właściwości z żądania HTTP do utworzenia wiadomości e-mail.
1. Ponieważ wybierasz wartość pola  Do, wybierz adres **e-mail**. Jeśli chcesz, wyłącz okno dialogowe zawartości dynamicznej, klikając **pozycję Dodaj zawartość dynamiczną.**

    ![Zrzut ekranu przedstawiający opcję wiadomości e-mail i wyróżnioną opcję Dodaj dynamiczną kontrę.](./media/tutorial-send-email/hide-dynamic-content.png)

1. Na liście **rozwijanej** Dodaj nowy parametr wybierz pozycję **Temat** i **Treść.**

1. Kliknij pole tekstowe **Temat** i w ten sam sposób wybierz **zadanie**. Mając kursor nadal w polu **Temat,** wpisz *utworzone .* 

1. Kliknij pozycję **Body (Treść)** i w taki sam sposób wybierz **pozycję due (termin).** Przenieś kursor na lewo od pozycji **Due (Termin)** i wpisz This work item is due on (Ten *element pracy jest spowodowany terminem*).

    > [!TIP]
    > Jeśli chcesz edytować zawartość HTML bezpośrednio w  treści wiadomości e-mail, wybierz pozycję Widok kodu w górnej części Logic Apps Projektanta. Upewnij się, że kod zawartości dynamicznej jest zachowywany (na przykład `@{triggerBody()?['due']}` )
    >
    > ![Zrzut ekranu przedstawiający widok kodu do wyświetlania zawartości H T M L bezpośrednio w treści wiadomości e-mail.](./media/tutorial-send-email/edit-rich-html-email.png) 

1. Następnie dodaj asynchroniczną odpowiedź HTTP do wyzwalacza HTTP. Między wyzwalaczem HTTP a akcją Gmail kliknij **+** znak i wybierz pozycję Dodaj **gałąź równoległą**.

    ![Zrzut ekranu przedstawiający wyróżnione opcje + znak i Dodaj gałąź równoległą.](./media/tutorial-send-email/add-http-response.png)

1. W polu wyszukiwania wyszukaj odpowiedź **,** a następnie wybierz **akcję** Odpowiedź.

    ![Zrzut ekranu przedstawiający pasek wyszukiwania i wyróżnione akcje Odpowiedź.](./media/tutorial-send-email/choose-response-action.png)

    Domyślnie akcja odpowiedzi wysyła błąd HTTP 200. Jest to wystarczające dla tego samouczka. Aby uzyskać więcej informacji, zobacz [http request/response reference (Odwołanie do żądania/odpowiedzi HTTP).](../connectors/connectors-native-reqres.md)

1. W górnej części ekranu Logic Apps Projektant wybierz ponownie **pozycję Zapisz.** 

## <a name="add-http-request-code-to-app"></a>Dodawanie kodu żądania HTTP do aplikacji

Upewnij się, że skopiowano adres URL wyzwalacza żądania HTTP z wcześniejszego źródła. Ponieważ zawiera on poufne informacje, najlepszym rozwiązaniem jest niewłokanie ich bezpośrednio w kodzie. W App Service można odwoływać się do niego jako zmiennej środowiskowej, używając ustawień aplikacji. 

W [Cloud Shell](https://shell.azure.com)utwórz ustawienie aplikacji za pomocą następującego polecenia (zastąp *\<app-name>* wartości , i *\<resource-group-name>* *\<logic-app-url>* ):

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings LOGIC_APP_URL="<your-logic-app-url>"
```

W kodzie udostępnij standardowy wpis HTTP w adresie URL przy użyciu dowolnego języka klienta HTTP, który jest dostępny dla struktury językowej, z następującą konfiguracją:

- Treść żądania zawiera ten sam format JSON, który został dostarczony do aplikacji logiki:

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

- Żądanie zawiera nagłówek `Content-Type: application/json` . 
- Aby zoptymalizować wydajność, wyślij żądanie asynchronicznie, jeśli to możliwe.

Kliknij kartę preferowany język/platformę poniżej, aby zobaczyć przykład.

### <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

W ASP.NET możesz wysłać wpis HTTP przy użyciu klasy [System.Net.Http.HttpClient.](/dotnet/api/system.net.http.httpclient) Na przykład:

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

Jeśli [testujesz](app-service-web-tutorial-dotnet-sqldatabase.md)ten kod w przykładowej aplikacji z artykułu Samouczek: tworzenie aplikacji usługi ASP.NET na platformie Azure [](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/blob/master/DotNetAppSqlDb/Controllers/TodosController.cs#L52-L63)za pomocą usługi SQL Database, możesz użyć go do wysłania wiadomości e-mail z potwierdzeniem w akcji Utwórz po dodaniu `Todo` elementu. Aby użyć powyższego kodu asynchronicznego, przekonwertuj akcję Utwórz na asynchroniczną.

### <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

W ASP.NET Core możesz wysłać wpis HTTP przy użyciu klasy [System.Net.Http.HttpClient.](/dotnet/api/system.net.http.httpclient) Na przykład:

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
> Ten kod został napisany dla uproszczenia pokazu. W praktyce nie należy ować wystąpienia `HttpClient` obiektu dla każdego żądania. Postępuj zgodnie ze wskazówkami z [tematu Use IHttpClientFactory to implement resilient HTTP requests (Implementowanie odpornych żądań HTTP za pomocą IHttpClientFactory).](/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests)

Jeśli testujesz ten kod w przykładowej aplikacji dla pozycji Samouczek: tworzenie aplikacji ASP.NET Core i SQL Database w programie [Azure App Service,](tutorial-dotnetcore-sqldb-app.md)możesz użyć go do wysłania wiadomości e-mail z potwierdzeniem w akcji Utwórz po dodaniu [](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial/blob/master/Controllers/TodosController.cs#L56-L65) `Todo` elementu.

### <a name="nodejs"></a>[Node.js](#tab/node)

W Node.js można łatwo wysłać wpis HTTP przy użyciu pakietu npm, takiego jak [axios](https://www.npmjs.com/package/axios). Na przykład:

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

Jeśli testujesz ten kod w przykładowej aplikacji dla artykułu [Samouczek:](tutorial-nodejs-mongodb-app.md)tworzenie aplikacji usług Node.js i MongoDB na platformie Azure, możesz użyć go do wysłania wiadomości e-mail z potwierdzeniem w funkcji [create](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L14-L27)po pomyślnym zapisaniu [artykułu.](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L24)

### <a name="php"></a>[PHP](#tab/php)

W języku PHP możesz łatwo wysłać wpis HTTP za pomocą [guszła](http://docs.guzzlephp.org/en/stable/index.html). Na przykład:

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

Jeśli testujesz ten kod w przykładowej aplikacji z samouczka: tworzenie aplikacji PHP i [MySQL](tutorial-php-mysql-app.md)na platformie Azure, możesz użyć go do wysłania wiadomości e-mail z potwierdzeniem w funkcji [Route::p ost](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48)tuż przed instrukcji return.

### <a name="python"></a>[Python](#tab/python)

W języku Python możesz łatwo wysłać wpis HTTP z [żądaniami](https://pypi.org/project/requests/). Na przykład:

```python
# Requires pip install requests && pip freeze > requirements.txt
import requests
import os
...
payload = {
    "email": "a-valid@emailaddress.com",
    "due": "4/1/2020",
    "task": "My new task!"
}
response = requests.post(os.environ['LOGIC_APP_URL'], data = payload)
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

Jeśli testujesz ten kod w przykładowej aplikacji dla samouczka: uruchamianie aplikacji internetowej Python (Django) przy użyciu bazy danych [PostgreSQL](tutorial-python-postgresql-app.md)w języku Azure App Service, możesz użyć go do wysłania wiadomości e-mail z potwierdzeniem w funkcji [Route::p ost](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48)bezpośrednio przed instrukcji return.

### <a name="ruby"></a>[Ruby](#tab/ruby)

W ruby możesz łatwo wysłać wpis HTTP za pomocą programu JSONClient. Na przykład:

```ruby
clnt = JSONClient.new
body = { 
    'email' => 'a-valid@emailaddress.com',
    'due' => '4/1/2020',
    'task' => "My new task!"
}

connection = clnt.post_async(ENV['LOGIC_APP_URL'], body)
```

Jeśli [testujesz](tutorial-ruby-postgres-app.md)ten kod w przykładowej aplikacji dla kompilowania aplikacji Ruby i Postgres w programie [](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L26-L38) Azure App Service dla systemu Linux, możesz użyć go do wysłania wiadomości e-mail z potwierdzeniem w akcji tworzenia, gdy działanie zakończy się [ @task.save pomyślnie.](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L30)

---

## <a name="more-resources"></a>Więcej zasobów

[Samouczek: hostowanie interfejsu API RESTful z mechanizmem CORS w usłudze Azure App Service](app-service-web-tutorial-rest-api.md)  
[Odwołanie do żądania/odpowiedzi HTTP dla Logic Apps](../connectors/connectors-native-reqres.md)  
[Szybki start: tworzenie pierwszego przepływu pracy przy użyciu Azure Logic Apps — Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)
