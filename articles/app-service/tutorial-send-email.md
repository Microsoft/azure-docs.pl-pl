---
title: 'Samouczek: wysyłanie wiadomości e-mail za pomocą aplikacji logiki'
description: Dowiedz się, jak wywoływać procesy biznesowe z aplikacji usługi App Service. Wysyłaj e-maile, tweety i posty na Facebooku, dodawaj do list mailingowych i wiele więcej.
ms.topic: tutorial
ms.date: 04/08/2020
ms.custom: mvc
ms.openlocfilehash: 4073b49a134356943bd7da8d54bf574f2e0d5eea
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604848"
---
# <a name="tutorial-send-email-and-invoke-other-business-processes-from-app-service"></a>Samouczek: Wysyłanie wiadomości e-mail i wywoływanie innych procesów biznesowych z usługi App Service

W tym samouczku dowiesz się, jak zintegrować aplikację usługi App Service z procesami biznesowymi. Jest to typowe dla scenariuszy aplikacji sieci web, takich jak:

- Wyślij wiadomość e-mail z potwierdzeniem transakcji
- Dodawanie użytkownika do grupy na Facebooku
- Połącz się z systemami innych firm, takimi jak SAP, SalesForce itp.
- Wymiana standardowych komunikatów B2B

W tym samouczku wysyłasz wiadomości e-mail z Gmaila z aplikacji Usługi app service przy użyciu [usługi Azure Logic Apps](../logic-apps/logic-apps-overview.md). Istnieją inne sposoby wysyłania wiadomości e-mail z aplikacji sieci web, takie jak konfiguracja SMTP udostępniana przez strukturę języka. Jednak aplikacje logiki przynosi o wiele więcej mocy do aplikacji usługi app service bez dodawania złożoności do kodu. Logic Apps zapewnia prosty interfejs konfiguracji dla najbardziej popularnych integracji biznesowych, a aplikacja może wywołać je w dowolnym momencie z żądaniem HTTP.

## <a name="prerequisite"></a>Wymagania wstępne

Wdrażanie aplikacji z wybranym frameworkiem językowym w usłudze App Service. Aby wykonać samouczek, aby wdrożyć przykładową aplikację, zobacz poniżej:

# <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

[Samouczek: tworzenie aplikacji ASP.NET na platformie Azure przy użyciu usługi SQL Database](app-service-web-tutorial-dotnet-sqldatabase.md)

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

[Samouczek: Tworzenie aplikacji ASP.NET Core i SQL Database w usłudze Azure App Service](app-service-web-tutorial-dotnetcore-sqldb.md)

# <a name="nodejs"></a>[Node.js](#tab/node)

[Samouczek: Tworzenie aplikacji Node.js i MongoDB na platformie Azure](app-service-web-tutorial-nodejs-mongodb-app.md)

# <a name="php"></a>[PHP](#tab/php)

[Samouczek: Tworzenie aplikacji PHP i MySQL na platformie Azure](app-service-web-tutorial-php-mysql.md)

# <a name="python"></a>[Python](#tab/python)

[Samouczek: Uruchamianie aplikacji internetowej Python (Django) z postgreSql w usłudze Azure App Service](containers/tutorial-python-postgresql-app.md)

# <a name="ruby"></a>[Ruby](#tab/ruby)

[Tworzenie aplikacji w języku Ruby i korzystającej z bazy danych Postgres w usłudze Azure App Service w systemie Linux](containers/tutorial-ruby-postgres-app.md)

---

## <a name="create-the-logic-app"></a>Tworzenie aplikacji logiki

1. W [witrynie Azure portal](https://portal.azure.com)utwórz pustą aplikację logiki, postępując zgodnie z instrukcjami w [aplikacji Tworzenie logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md#create-your-logic-app). Po wyświetleniu **projektanta aplikacji logiki**wróć do tego samouczka.
1. Na stronie powitania projektanta aplikacji logiki wybierz pozycję **Po odebraniu żądania HTTP** w obszarze Start ze wspólnym **wyzwalaczem**.

    ![](./media/tutorial-send-email/receive-http-request.png)
1. W oknie dialogowym **Po odebraniu żądania HTTP**wybierz pozycję Użyj **przykładowego ładunku do wygenerowania schematu**.

    ![](./media/tutorial-send-email/generate-schema-with-payload.png)

1. Skopiuj do pola tekstowego następujący przykładowy json i wybierz pozycję **Gotowe**.

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

    Schemat jest teraz generowany dla żądanych danych żądania. W praktyce można po prostu przechwycić rzeczywiste dane żądania kodu aplikacji generuje i niech platforma Azure wygenerować schemat JSON dla Ciebie. 
1. U góry projektanta aplikacji logiki wybierz pozycję **Zapisz**. 

    Teraz możesz zobaczyć adres URL wyzwalacza żądania HTTP. Wybierz ikonę kopiowania, aby skopiować ją do późniejszego użycia.

    ![](./media/tutorial-send-email/http-request-url.png)

    Ta definicja żądania HTTP jest wyzwalaczem wszystkiego, co chcesz zrobić w tej aplikacji logiki, czy to Gmail lub cokolwiek innego. Później wywołasz ten adres URL w aplikacji usługi App Service. Aby uzyskać więcej informacji na temat wyzwalacza żądania, zobacz [odwołanie do żądania/odpowiedzi HTTP](../connectors/connectors-native-reqres.md).

1. U dołu projektanta kliknij pozycję **Nowy krok**, wpisz **Gmaila** w polu wyszukiwania akcji i znajdź i wybierz **pozycję Wyślij wiadomość e-mail (V2).**
    
    > [!TIP]
    > Można wyszukiwać inne typy integracji, takie jak SendGrid, MailChimp, Office 365 i SalesForce. Aby uzyskać więcej informacji, zobacz [dokumentację aplikacji logiki](https://docs.microsoft.com/azure/logic-apps/).
1. W oknie dialogowym **Gmail** wybierz pozycję **Zaloguj się** i zaloguj się na konto Gmail, z którego chcesz wysłać wiadomość e-mail.

    ![](./media/tutorial-send-email/gmail-sign-in.png)

1. Po zalogowaniu kliknij pole tekstowe **Do,** a okno dialogowe zawartości dynamicznej zostanie automatycznie otwarte.

1. Obok akcji **Po odebraniu żądania HTTP** wybierz pozycję Zobacz **więcej**.

    ![](./media/tutorial-send-email/expand-dynamic-content.png)

    Teraz powinny być widoczne trzy właściwości z przykładowych danych JSON, które zostały użyte wcześniej. W tym kroku można użyć tych właściwości z żądania HTTP do konstruowania wiadomości e-mail.
1. Ponieważ wybierasz wartość pola **Do,** wybierz pozycję **Poczta e-mail**. Jeśli chcesz, wyłącz okno dialogowe zawartości dynamicznej, klikając pozycję **Dodaj zawartość dynamiczną**.

    ![](./media/tutorial-send-email/hide-dynamic-content.png)

1. Z listy rozwijanej **Dodaj nowy parametr** wybierz pozycję **Temat** i **treść**.

1. Kliknij pole **tekstowe Temat** i w ten sam sposób wybierz **pozycję Zadanie**. Gdy kursor jest nadal w polu **Temat,** wpisz *utworzony*plik . 

1. Kliknij w **treści**, i w ten sam sposób wybierz **należne**. Przesuń kursor w lewo **i** wpisz *Ten element roboczy jest włączony*.

    > [!TIP]
    > Jeśli chcesz edytować zawartość HTML bezpośrednio w treści wiadomości e-mail, wybierz **widok Kod** u góry okna Projektanta aplikacji logiki. Po prostu upewnij się, `@{triggerBody()?['due']}`że zachowujesz dynamiczny kod zawartości (na przykład)
    >
    > ![](./media/tutorial-send-email/edit-rich-html-email.png) 

1. Następnie dodaj asynchroniiową odpowiedź HTTP do wyzwalacza HTTP. Między wyzwalaczem HTTP a akcją Gmail kliknij **+** znak i wybierz pozycję Dodaj **odgałęźliwą gałąź**.

    ![](./media/tutorial-send-email/add-http-response.png)

1. W polu wyszukiwania wyszukaj **odpowiedź**, a następnie wybierz akcję **Odpowiedź.**

    ![](./media/tutorial-send-email/choose-response-action.png)

    Domyślnie akcja odpowiedzi wysyła HTTP 200. To wystarczy do tego samouczka. Aby uzyskać więcej informacji, zobacz [odwołanie do żądania/odpowiedzi HTTP](../connectors/connectors-native-reqres.md).

1. U góry projektanta aplikacji logiki wybierz pozycję **Zapisz** ponownie. 

## <a name="add-http-request-code-to-app"></a>Dodawanie kodu żądania HTTP do aplikacji

Upewnij się, że adres URL wyzwalacza żądania HTTP został skopiowany z wcześniejszego. Ponieważ zawiera poufne informacje, jest najlepszym rozwiązaniem, aby nie umieścić go bezpośrednio w kodzie. Za pomocą usługi App Service można odwoływać się do niej jako zmiennej środowiskowej, używając ustawień aplikacji. 

W [aplikacji Cloud Shell](https://shell.azure.com)utwórz ustawienie aplikacji za pomocą następującego polecenia (zastąp * \<>nazwę aplikacji, * * \<>nazwa grupy zasobów *i * \<>adres url aplikacji): *

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings LOGIC_APP_URL="<your-logic-app-url>"
```

W kodzie należy wprowadzić standardowy wpis HTTP do adresu URL przy użyciu dowolnego języka klienta HTTP, który jest dostępny dla struktury języka, z następującą konfiguracją:

- Treść żądania zawiera ten sam format JSON, który został dostarczony do aplikacji logiki:

    ```json
    {
        "task": "<description>",
        "due": "<date>",
        "email": "<email-address>"
    }
    ```

- Żądanie zawiera nagłówek `Content-Type: application/json`. 
- Aby zoptymalizować wydajność, wyślij żądanie asynchronicznie, jeśli to możliwe.

Kliknij na preferowany język / framework kartę poniżej, aby zobaczyć przykład.

# <a name="aspnet"></a>[ASP.NET](#tab/dotnet)

W ASP.NET można wysłać wpis HTTP z [klasy System.Net.http.httpClient.](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) Przykład:

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

Jeśli testujesz ten kod w przykładowej aplikacji dla [samouczka: Tworzenie aplikacji ASP.NET na platformie Azure za pomocą bazy danych SQL](app-service-web-tutorial-dotnet-sqldatabase.md), można go użyć do wysłania potwierdzenia wiadomości e-mail w akcji [Utwórz](https://github.com/Azure-Samples/dotnet-sqldb-tutorial/blob/master/DotNetAppSqlDb/Controllers/TodosController.cs#L52-L63), po `Todo` dodaniu elementu. Aby użyć kodu asynchroniowego powyżej, przekonwertuj akcję Utwórz na asynchroniza.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/dotnetcore)

W ASP.NET Core można wysłać wpis HTTP za pomocą klasy [System.Net.http.httpClient.](https://docs.microsoft.com/dotnet/api/system.net.http.httpclient) Przykład:

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
    // Requires DI configuration to access app settings. See https://docs.microsoft.com/azure/app-service/containers/configure-language-dotnetcore#access-environment-variables
    _configuration["LOGIC_APP_URL"],
    new StringContent(jsonData, Encoding.UTF8, "application/json"));
    
var statusCode = result.StatusCode.ToString();
```

> [!NOTE]
> Ten kod jest napisany dla uproszczenia demonstracji. W praktyce nie tworzenie wystąpienia `HttpClient` obiektu dla każdego żądania. Postępuj zgodnie ze wskazówkami dotyczącymi [używania ihttpclientfactory do implementowania odpornych żądań HTTP](https://docs.microsoft.com/dotnet/architecture/microservices/implement-resilient-applications/use-httpclientfactory-to-implement-resilient-http-requests).

Jeśli testujesz ten kod w przykładowej aplikacji dla [samouczka: Tworzenie ASP.NET aplikacji Core i SQL Database w usłudze Azure App Service,](app-service-web-tutorial-dotnetcore-sqldb.md)można go użyć do wysłania potwierdzenia wiadomości e-mail w [akcji Utwórz](https://github.com/Azure-Samples/dotnetcore-sqldb-tutorial/blob/master/Controllers/TodosController.cs#L56-L65)po `Todo` dodaniu elementu.

# <a name="nodejs"></a>[Node.js](#tab/node)

W Node.js, można łatwo wysłać post HTTP z pakietu npm jak [axios](https://www.npmjs.com/package/axios). Przykład:

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

Jeśli testujesz ten kod w przykładowej aplikacji dla [samouczka: Tworzenie aplikacji Node.js i MongoDB na platformie Azure,](app-service-web-tutorial-nodejs-mongodb-app.md)możesz go użyć do wysłania potwierdzenia e-mail w [funkcji tworzenia,](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L14-L27)po [pomyślnym zapisaniu artykułu.](https://github.com/Azure-Samples/meanjs/blob/master/modules/articles/server/controllers/articles.server.controller.js#L24)

# <a name="php"></a>[PHP](#tab/php)

W PHP, można łatwo wysłać post HTTP z [Guzzle](http://docs.guzzlephp.org/en/stable/index.html). Przykład:

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

Jeśli testujesz ten kod w przykładowej aplikacji dla [samouczka: Tworzenie aplikacji PHP i MySQL na platformie Azure,](app-service-web-tutorial-php-mysql.md)można go użyć do wysłania potwierdzenia wiadomości e-mail w [Route::post funkcji,](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48)tuż przed return instrukcji.

# <a name="python"></a>[Python](#tab/python)

W Pythonie możesz łatwo wysłać post HTTP z [żądaniami](https://pypi.org/project/requests/). Przykład:

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

Jeśli testujesz ten kod w przykładowej aplikacji dla [samouczka: Uruchom aplikację internetową Python (Django) z PostgreSQL w usłudze Azure App Service,](containers/tutorial-python-postgresql-app.md)możesz go użyć do wysłania potwierdzenia wiadomości e-mail w [funkcji Route::post](https://github.com/Azure-Samples/laravel-tasks/blob/master/routes/web.php#L30-L48), tuż przed instrukcją return.

# <a name="ruby"></a>[Ruby](#tab/ruby)

W Ruby, można łatwo wysłać post HTTP z [JSONClient](https://www.rubydoc.info/gems/httpclient/JSONClient). Przykład:

```ruby
clnt = JSONClient.new
body = { 
    'email' => 'a-valid@emailaddress.com',
    'due' => '4/1/2020',
    'task' => "My new task!"
}

connection = clnt.post_async(ENV['LOGIC_APP_URL'], body)
```

Jeśli testujesz ten kod w przykładowej aplikacji do [tworzenia aplikacji Ruby i Postgres w usłudze Azure App Service w systemie Linux,](containers/tutorial-ruby-postgres-app.md)możesz użyć go do wysłania potwierdzenia wiadomości e-mail w akcji [tworzenia,](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L26-L38) [gdy @task.save zakończy się pomyślnie.](https://github.com/Azure-Samples/rubyrails-tasks/blob/master/app/controllers/tasks_controller.rb#L30)

---

# <a name="more-resources"></a>Więcej zasobów

[Samouczek: hostowanie interfejsu API RESTful z mechanizmem CORS w usłudze Azure App Service](app-service-web-tutorial-rest-api.md)  
[Odwołanie do żądania/odpowiedzi HTTP dla aplikacji logiki](../connectors/connectors-native-reqres.md)  
[Szybki start: tworzenie pierwszego przepływu pracy przy użyciu usługi Azure Logic Apps — Witryna Azure portal](../logic-apps/quickstart-create-first-logic-app-workflow.md)