---
title: Microsoft Graph powiązania Azure Functions
description: Dowiedz się, jak używać wyzwalaczy Microsoft Graph i powiązań w programie Azure Functions.
author: craigshoemaker
ms.topic: reference
ms.date: 12/20/2017
ms.author: cshoe
ms.openlocfilehash: ef746186717f3eb6bb93263c7f86c26606f65ea9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84322519"
---
# <a name="microsoft-graph-bindings-for-azure-functions"></a>Microsoft Graph powiązania Azure Functions

W tym artykule opisano sposób konfigurowania i pracy z Microsoft Graph wyzwalaczami i powiązaniami w programie Azure Functions. Za pomocą tych danych można używać Azure Functions do pracy z danymi, szczegółowymi informacjami i zdarzeniami z [Microsoft Graph](https://developer.microsoft.com/graph).

Rozszerzenie Microsoft Graph zapewnia następujące powiązania:
- [Powiązanie danych wejściowych tokenu uwierzytelniania](#token-input) pozwala na interakcję z dowolnym interfejsem API Microsoft Graph.
- [Powiązanie danych wejściowych tabeli programu Excel](#excel-input) umożliwia odczytywanie danych z programu Excel.
- [Powiązanie danych wyjściowych tabeli programu Excel](#excel-output) pozwala modyfikować dane programu Excel.
- [Powiązanie danych wejściowych pliku w usłudze OneDrive](#onedrive-input) umożliwia odczytywanie plików z usługi OneDrive.
- [Powiązanie danych wyjściowych w usłudze OneDrive](#onedrive-output) umożliwia zapis w plikach w usłudze OneDrive.
- [Powiązanie danych wyjściowych wiadomości programu Outlook](#outlook-output) umożliwia wysyłanie wiadomości e-mail za pośrednictwem programu Outlook.
- Kolekcja [wyzwalaczy i powiązań Microsoft Graph elementu webhook](#webhooks) umożliwia reagowanie na zdarzenia z Microsoft Graph.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

> [!Note]
> Microsoft Graph powiązania są obecnie dostępne w wersji zapoznawczej dla Azure Functions wersja 2. x i nowsza. Nie są one obsługiwane w funkcjach w wersji 1. x.

## <a name="packages"></a>Pakiety

Powiązanie danych wejściowych tokenu uwierzytelniania jest dostępne w pakiecie NuGet [Microsoft. Azure. WebJobs. Extensions. AuthTokens](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.AuthTokens/) . Inne powiązania Microsoft Graph są dostępne w pakiecie [Microsoft. Azure. WebJobs. Extensions. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.MicrosoftGraph/) . Kod źródłowy pakietów znajduje się w repozytorium [Azure-Functions-microsoftgraph-Extension](https://github.com/Azure/azure-functions-microsoftgraph-extension/) GitHub.

[!INCLUDE [functions-package-v2](../../includes/functions-package-v2.md)]

## <a name="setting-up-the-extensions"></a>Konfigurowanie rozszerzeń

Powiązania Microsoft Graph są dostępne za poorednictwem _rozszerzeń powiązań_. Rozszerzenia powiązań są opcjonalnymi składnikami środowiska uruchomieniowego Azure Functions. W tej sekcji przedstawiono sposób konfigurowania rozszerzeń Microsoft Graph i tokenów uwierzytelniania.

### <a name="enabling-functions-20"></a>Włączanie funkcji 2,0

Rozszerzenia powiązań są dostępne tylko dla Azure Functions 2,0. 

Aby uzyskać informacje na temat sposobu ustawiania aplikacji funkcji do korzystania z wersji 2,0 środowiska uruchomieniowego usługi Functions, zobacz jak dowiedzieć [się, jak kierować wersje środowiska uruchomieniowego Azure Functions](set-runtime-version.md).

### <a name="installing-the-extension"></a>Instalowanie rozszerzenia

Aby zainstalować rozszerzenie z Azure Portal, przejdź do szablonu lub powiązania, które odwołuje się do niego. Utwórz nową funkcję, a następnie na ekranie Wybieranie szablonu Wybierz scenariusz "Microsoft Graph". Wybierz jeden z szablonów z tego scenariusza. Alternatywnie możesz przejść do karty "Integracja" istniejącej funkcji i wybrać jedno z powiązań uwzględnionych w tym artykule.

W obu przypadkach zostanie wyświetlone ostrzeżenie, które określa rozszerzenie, które ma zostać zainstalowane. Kliknij przycisk **Instaluj** , aby uzyskać rozszerzenie. Każde rozszerzenie należy zainstalować tylko raz dla aplikacji funkcji. 

> [!Note] 
> Proces instalacji w portalu może potrwać do 10 minut od planu zużycia.

Jeśli używasz programu Visual Studio, możesz pobrać rozszerzenia, instalując [pakiety NuGet wymienione wcześniej w tym artykule](#packages).

### <a name="configuring-authentication--authorization"></a>Konfigurowanie uwierzytelniania/autoryzacji

Powiązania opisane w tym artykule wymagają użycia tożsamości. Dzięki temu Microsoft Graph wymuszać uprawnienia i kontrolować interakcje. Tożsamość może być użytkownikiem, który uzyskuje dostęp do aplikacji lub samej aplikacji. Aby skonfigurować tę tożsamość, skonfiguruj [App Service uwierzytelnianie/autoryzacja](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) przy użyciu Azure Active Directory. Należy również zażądać wszelkich uprawnień zasobów wymaganych przez funkcje.

> [!Note] 
> Rozszerzenie Microsoft Graph obsługuje tylko uwierzytelnianie w usłudze Azure AD. Użytkownicy muszą zalogować się przy użyciu konta służbowego.

Jeśli używasz Azure Portal, zobaczysz ostrzeżenie poniżej monitu o zainstalowanie rozszerzenia. Ostrzeżenie wyświetla komunikat z prośbą o skonfigurowanie App Service uwierzytelniania/autoryzacji i zażądanie wszelkich uprawnień wymaganych przez szablon lub powiązanie. Kliknij pozycję **Konfiguruj usługę Azure AD teraz** lub **Dodaj uprawnienia teraz** , odpowiednio do potrzeb.



<a name="token-input"></a>
## <a name="auth-token"></a>Token uwierzytelniania

Powiązanie danych wejściowych tokenu uwierzytelniania pobiera token usługi Azure AD dla danego zasobu i udostępnia go kodowi jako ciąg. Może to być dowolny zasób, dla którego aplikacja ma uprawnienia. 

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#auth-token---example)
* [Atrybuty](#auth-token---attributes)
* [Konfiguracja](#auth-token---configuration)
* [Użycie](#auth-token---usage)

### <a name="auth-token---example"></a>Token uwierzytelniania — przykład

Zobacz przykład specyficzny dla języka:

* [Skrypt języka C# (csx)](#auth-token---c-script-example)
* [JavaScript](#auth-token---javascript-example)

#### <a name="auth-token---c-script-example"></a>Token uwierzytelniania — przykład skryptu w języku C#

Poniższy przykład pobiera informacje o profilu użytkownika.

*function.jsw* pliku definiuje wyzwalacz http z powiązaniem danych wejściowych tokenu:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kod skryptu C# używa tokenu do wywołania protokołu HTTP do Microsoft Graph i zwraca wynik:

```csharp
using System.Net; 
using System.Net.Http; 
using System.Net.Http.Headers;
using Microsoft.Extensions.Logging; 

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string graphToken, ILogger log)
{
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", graphToken);
    return await client.GetAsync("https://graph.microsoft.com/v1.0/me/");
}
```

#### <a name="auth-token---javascript-example"></a>Token uwierzytelniania — przykład JavaScript

Poniższy przykład pobiera informacje o profilu użytkownika.

*function.jsw* pliku definiuje wyzwalacz http z powiązaniem danych wejściowych tokenu:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "token",
      "direction": "in",
      "name": "graphToken",
      "resource": "https://graph.microsoft.com",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kod JavaScript używa tokenu do wywołania protokołu HTTP do Microsoft Graph i zwraca wynik.

```js
const rp = require('request-promise');

module.exports = function (context, req) {
    let token = "Bearer " + context.bindings.graphToken;

    let options = {
        uri: 'https://graph.microsoft.com/v1.0/me/',
        headers: {
            'Authorization': token
        }
    };
    
    rp(options)
        .then(function(profile) {
            context.res = {
                body: profile
            };
            context.done();
        })
        .catch(function(err) {
            context.res = {
                status: 500,
                body: err
            };
            context.done();
        });
};
```

### <a name="auth-token---attributes"></a>Token uwierzytelniania — atrybuty

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)Użyj atrybutu [tokenu](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/TokenBinding/TokenAttribute.cs) .

### <a name="auth-token---configuration"></a>Token uwierzytelniania — konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `Token` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Nazwij**| nie dotyczy |Wymagane — nazwa zmiennej używana w kodzie funkcji dla tokenu uwierzytelniania. Zobacz [Używanie powiązania danych wejściowych tokenu uwierzytelniania z kodu](#token-input-code).|
|**Wprowadź**| nie dotyczy |Wymagane — musi być ustawiony na wartość `token` .|
|**wskazywa**| nie dotyczy |Wymagane — musi być ustawiony na wartość `in` .|
|**Identity**|**Tożsamość**|Wymagane — tożsamość, która zostanie użyta do wykonania akcji. Może być jedną z następujących wartości:<ul><li><code>userFromRequest</code>-Prawidłowy tylko z [wyzwalaczem http]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code>-Używa tożsamości wcześniej zalogowanego użytkownika o określonym IDENTYFIKATORze. Zobacz <code>userId</code> Właściwość.</li><li><code>userFromToken</code>-Używa tożsamości reprezentowanej przez określony token. Zobacz <code>userToken</code> Właściwość.</li><li><code>clientCredentials</code>— Używa tożsamości aplikacji funkcji.</li></ul>|
|**Nazwa**|**Nazwa**  |Wymagana, jeśli i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromId` . Identyfikator podmiotu zabezpieczeń użytkownika skojarzony z wcześniej zalogowanym użytkownikiem.|
|**userToken**|**UserToken**|Wymagana, jeśli i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromToken` . Token ważny dla aplikacji funkcji. |
|**Zasób**|**zasoby**|Wymagane — adres URL zasobu usługi Azure AD, dla którego jest żądany token.|

<a name="token-input-code"></a>
### <a name="auth-token---usage"></a>Token uwierzytelniania — użycie

Samo powiązanie nie wymaga żadnych uprawnień usługi Azure AD, ale w zależności od tego, jak token jest używany, może być konieczne zażądanie dodatkowych uprawnień. Sprawdź wymagania zasobu, do którego zamierzasz uzyskać dostęp za pomocą tokenu.

Token jest zawsze wyświetlany w kodzie jako ciąg.

> [!Note]
> Podczas tworzenia lokalnie przy użyciu dowolnego `userFromId` `userFromToken` lub `userFromRequest` opcji, wymagany token może być [uzyskiwany ręcznie](https://github.com/Azure/azure-functions-microsoftgraph-extension/issues/54#issuecomment-392865857) i określony w `X-MS-TOKEN-AAD-ID-TOKEN` nagłówku żądania z aplikacji klienckiej wywołującej.


<a name="excel-input"></a>
## <a name="excel-input"></a>Dane wejściowe programu Excel

Powiązanie danych wejściowych tabeli programu Excel odczytuje zawartość tabeli programu Excel przechowywanej w usłudze OneDrive.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#excel-input---example)
* [Atrybuty](#excel-input---attributes)
* [Konfiguracja](#excel-input---configuration)
* [Użycie](#excel-input---usage)

### <a name="excel-input---example"></a>Dane wejściowe programu Excel — przykład

Zobacz przykład specyficzny dla języka:

* [Skrypt języka C# (csx)](#excel-input---c-script-example)
* [JavaScript](#excel-input---javascript-example)

#### <a name="excel-input---c-script-example"></a>Dane wejściowe programu Excel — przykład skryptu

Następujący *function.jsw* pliku definiuje wyzwalacz http z powiązaniem wejściowym programu Excel:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Poniższy kod skryptu C# odczytuje zawartość określonej tabeli i zwraca je użytkownikowi:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Microsoft.Extensions.Logging;

public static IActionResult Run(HttpRequest req, string[][] excelTableData, ILogger log)
{
    return new OkObjectResult(excelTableData);
}
```

#### <a name="excel-input---javascript-example"></a>Dane wejściowe programu Excel — przykład JavaScript

Następujący *function.jsw* pliku definiuje wyzwalacz http z powiązaniem wejściowym programu Excel:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "excel",
      "direction": "in",
      "name": "excelTableData",
      "path": "{query.workbook}",
      "identity": "UserFromRequest",
      "tableName": "{query.table}"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Poniższy kod JavaScript odczytuje zawartość określonej tabeli i zwraca je do użytkownika.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.excelTableData
    };
    context.done();
};
```

### <a name="excel-input---attributes"></a>Dane wejściowe programu Excel

W [bibliotekach klas języka C#](functions-dotnet-class-library.md), Użyj atrybutu [programu Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) .

### <a name="excel-input---configuration"></a>Dane wejściowe programu Excel — konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `Excel` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Nazwij**| nie dotyczy |Wymagane — nazwa zmiennej używana w kodzie funkcji dla tabeli programu Excel. Zobacz [Używanie powiązania danych wejściowych tabeli programu Excel z kodu](#excel-input-code).|
|**Wprowadź**| nie dotyczy |Wymagane — musi być ustawiony na wartość `excel` .|
|**wskazywa**| nie dotyczy |Wymagane — musi być ustawiony na wartość `in` .|
|**Identity**|**Tożsamość**|Wymagane — tożsamość, która zostanie użyta do wykonania akcji. Może być jedną z następujących wartości:<ul><li><code>userFromRequest</code>-Prawidłowy tylko z [wyzwalaczem http]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code>-Używa tożsamości wcześniej zalogowanego użytkownika o określonym IDENTYFIKATORze. Zobacz <code>userId</code> Właściwość.</li><li><code>userFromToken</code>-Używa tożsamości reprezentowanej przez określony token. Zobacz <code>userToken</code> Właściwość.</li><li><code>clientCredentials</code>— Używa tożsamości aplikacji funkcji.</li></ul>|
|**Nazwa**|**Nazwa**  |Wymagana, jeśli i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromId` . Identyfikator podmiotu zabezpieczeń użytkownika skojarzony z wcześniej zalogowanym użytkownikiem.|
|**userToken**|**UserToken**|Wymagana, jeśli i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromToken` . Token ważny dla aplikacji funkcji. |
|**ścieżka**|**Ścieżka**|Wymagane — ścieżka w usłudze OneDrive do skoroszytu programu Excel.|
|**arkuszname**|**Arkuszname**|Arkusz, w którym znajduje się tabela.|
|**tableName**|**TableName**|Nazwa tabeli. Jeśli nie zostanie określony, zostanie użyta zawartość arkusza.|

<a name="excel-input-code"></a>
### <a name="excel-input---usage"></a>Dane wejściowe programu Excel — użycie

To powiązanie wymaga następujących uprawnień usługi Azure AD:

|Zasób|Uprawnienie|
|--------|--------|
|Microsoft Graph|Odczytuj pliki użytkownika|

Powiązanie uwidacznia następujące typy do funkcji .NET Functions:
- ciąg [] []
- Microsoft. Graph. skoroszyty
- Niestandardowe typy obiektów (przy użyciu powiązania modelu strukturalnego)










<a name="excel-output"></a>
## <a name="excel-output"></a>Dane wyjściowe programu Excel

Powiązanie danych wyjściowych programu Excel modyfikuje zawartość tabeli programu Excel przechowywanej w usłudze OneDrive.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#excel-output---example)
* [Atrybuty](#excel-output---attributes)
* [Konfiguracja](#excel-output---configuration)
* [Użycie](#excel-output---usage)

### <a name="excel-output---example"></a>Dane wyjściowe programu Excel — przykład

Zobacz przykład specyficzny dla języka:

* [Skrypt języka C# (csx)](#excel-output---c-script-example)
* [JavaScript](#excel-output---javascript-example)

#### <a name="excel-output---c-script-example"></a>Dane wyjściowe programu Excel — przykład skryptu w języku C#

Poniższy przykład dodaje wiersze do tabeli programu Excel.

*function.jsw* pliku definiuje wyzwalacz http z powiązaniem wyjściowym programu Excel:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kod skryptu C# dodaje nowy wiersz do tabeli (przyjęto, że jest jednokolumnowa) na podstawie danych wejściowych z ciągu zapytania:

```csharp
using System.Net;
using System.Text;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, IAsyncCollector<object> newExcelRow, ILogger log)
{
    string input = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await newExcelRow.AddAsync(new {
        Text = input
        // Add other properties for additional columns here
    });
    return;
}
```

#### <a name="excel-output---javascript-example"></a>Dane wyjściowe programu Excel — przykład JavaScript

Poniższy przykład dodaje wiersze do tabeli programu Excel.

*function.jsw* pliku definiuje wyzwalacz http z powiązaniem wyjściowym programu Excel:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "newExcelRow",
      "type": "excel",
      "direction": "out",
      "identity": "userFromRequest",
      "updateType": "append",
      "path": "{query.workbook}",
      "tableName": "{query.table}"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Poniższy kod JavaScript dodaje nowy wiersz do tabeli (przyjęto, że jest to jedna kolumna) na podstawie danych wejściowych z ciągu zapytania.

```js
module.exports = function (context, req) {
    context.bindings.newExcelRow = {
        text: req.query.text
        // Add other properties for additional columns here
    }
    context.done();
};
```

### <a name="excel-output---attributes"></a>Dane wyjściowe programu Excel — atrybuty

W [bibliotekach klas języka C#](functions-dotnet-class-library.md), Użyj atrybutu [programu Excel](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/ExcelAttribute.cs) .

### <a name="excel-output---configuration"></a>Dane wyjściowe programu Excel — konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `Excel` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Nazwij**| nie dotyczy |Wymagane — nazwa zmiennej używana w kodzie funkcji dla tokenu uwierzytelniania. Zobacz [Używanie powiązania danych wyjściowych tabeli programu Excel z kodu](#excel-output-code).|
|**Wprowadź**| nie dotyczy |Wymagane — musi być ustawiony na wartość `excel` .|
|**wskazywa**| nie dotyczy |Wymagane — musi być ustawiony na wartość `out` .|
|**Identity**|**Tożsamość**|Wymagane — tożsamość, która zostanie użyta do wykonania akcji. Może być jedną z następujących wartości:<ul><li><code>userFromRequest</code>-Prawidłowy tylko z [wyzwalaczem http]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code>-Używa tożsamości wcześniej zalogowanego użytkownika o określonym IDENTYFIKATORze. Zobacz <code>userId</code> Właściwość.</li><li><code>userFromToken</code>-Używa tożsamości reprezentowanej przez określony token. Zobacz <code>userToken</code> Właściwość.</li><li><code>clientCredentials</code>— Używa tożsamości aplikacji funkcji.</li></ul>|
|**Nazwa** |**Nazwa** |Wymagana, jeśli i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromId` . Identyfikator podmiotu zabezpieczeń użytkownika skojarzony z wcześniej zalogowanym użytkownikiem.|
|**userToken**|**UserToken**|Wymagana, jeśli i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromToken` . Token ważny dla aplikacji funkcji. |
|**ścieżka**|**Ścieżka**|Wymagane — ścieżka w usłudze OneDrive do skoroszytu programu Excel.|
|**arkuszname**|**Arkuszname**|Arkusz, w którym znajduje się tabela.|
|**tableName**|**TableName**|Nazwa tabeli. Jeśli nie zostanie określony, zostanie użyta zawartość arkusza.|
|**Typ aktualizacji**|**Typ aktualizacji**|Wymagane — typ zmiany do wprowadzenia do tabeli. Może być jedną z następujących wartości:<ul><li><code>update</code>— Zastępuje zawartość tabeli w usłudze OneDrive.</li><li><code>append</code>-Dodaje ładunek do końca tabeli w usłudze OneDrive, tworząc nowe wiersze.</li></ul>|

<a name="excel-output-code"></a>
### <a name="excel-output---usage"></a>Dane wyjściowe programu Excel — użycie

To powiązanie wymaga następujących uprawnień usługi Azure AD:

|Zasób|Uprawnienie|
|--------|--------|
|Microsoft Graph|Uzyskaj pełny dostęp do plików użytkownika|

Powiązanie uwidacznia następujące typy do funkcji .NET Functions:
- ciąg [] []
- Newtonsoft.Js. LINQ. JObject
- Microsoft. Graph. skoroszyty
- Niestandardowe typy obiektów (przy użyciu powiązania modelu strukturalnego)





<a name="onedrive-input"></a>
## <a name="file-input"></a>Dane wejściowe pliku

Powiązanie danych wejściowych pliku usługi OneDrive odczytuje zawartość pliku przechowywanego w usłudze OneDrive.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#file-input---example)
* [Atrybuty](#file-input---attributes)
* [Konfiguracja](#file-input---configuration)
* [Użycie](#file-input---usage)

### <a name="file-input---example"></a>Dane wejściowe pliku — przykład

Zobacz przykład specyficzny dla języka:

* [Skrypt języka C# (csx)](#file-input---c-script-example)
* [JavaScript](#file-input---javascript-example)

#### <a name="file-input---c-script-example"></a>Wejściowy plik — przykład skryptu w języku C#

Poniższy przykład odczytuje plik, który jest przechowywany w usłudze OneDrive.

*function.jsw* pliku definiuje wyzwalacz http z powiązaniem wejściowym pliku w usłudze OneDrive:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kod skryptu C# odczytuje plik określony w ciągu zapytania i rejestruje jego długość:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequestMessage req, Stream myOneDriveFile, ILogger log)
{
    log.LogInformation(myOneDriveFile.Length.ToString());
}
```

#### <a name="file-input---javascript-example"></a>Dane wejściowe pliku — przykład JavaScript

Poniższy przykład odczytuje plik, który jest przechowywany w usłudze OneDrive.

*function.jsw* pliku definiuje wyzwalacz http z powiązaniem wejściowym pliku w usłudze OneDrive:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "in",
      "path": "{query.filename}",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Poniższy kod JavaScript odczytuje plik określony w ciągu zapytania i zwraca jego długość.

```js
module.exports = function (context, req) {
    context.res = {
        body: context.bindings.myOneDriveFile.length
    };
    context.done();
};
```

### <a name="file-input---attributes"></a>Wprowadzanie plików — atrybuty

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)Użyj atrybutu [usługi OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) .

### <a name="file-input---configuration"></a>Dane wejściowe pliku — konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `OneDrive` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Nazwij**| nie dotyczy |Wymagane — nazwa zmiennej używana w kodzie funkcji dla pliku. Zobacz [Używanie powiązania danych wejściowych pliku w usłudze OneDrive z kodu](#onedrive-input-code).|
|**Wprowadź**| nie dotyczy |Wymagane — musi być ustawiony na wartość `onedrive` .|
|**wskazywa**| nie dotyczy |Wymagane — musi być ustawiony na wartość `in` .|
|**Identity**|**Tożsamość**|Wymagane — tożsamość, która zostanie użyta do wykonania akcji. Może być jedną z następujących wartości:<ul><li><code>userFromRequest</code>-Prawidłowy tylko z [wyzwalaczem http]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code>-Używa tożsamości wcześniej zalogowanego użytkownika o określonym IDENTYFIKATORze. Zobacz <code>userId</code> Właściwość.</li><li><code>userFromToken</code>-Używa tożsamości reprezentowanej przez określony token. Zobacz <code>userToken</code> Właściwość.</li><li><code>clientCredentials</code>— Używa tożsamości aplikacji funkcji.</li></ul>|
|**Nazwa**|**Nazwa**  |Wymagana, jeśli i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromId` . Identyfikator podmiotu zabezpieczeń użytkownika skojarzony z wcześniej zalogowanym użytkownikiem.|
|**userToken**|**UserToken**|Wymagana, jeśli i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromToken` . Token ważny dla aplikacji funkcji. |
|**ścieżka**|**Ścieżka**|Wymagane — ścieżka do pliku w usłudze OneDrive.|

<a name="onedrive-input-code"></a>
### <a name="file-input---usage"></a>Wprowadzanie plików — użycie

To powiązanie wymaga następujących uprawnień usługi Azure AD:

|Zasób|Uprawnienie|
|--------|--------|
|Microsoft Graph|Odczytuj pliki użytkownika|

Powiązanie uwidacznia następujące typy do funkcji .NET Functions:
- Byte []
- Strumień
- ciąg
- Microsoft. Graph. DriveItem






<a name="onedrive-output"></a>
## <a name="file-output"></a>Dane wyjściowe pliku

Powiązanie danych wyjściowych pliku usługi OneDrive modyfikuje zawartość pliku przechowywanego w usłudze OneDrive.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#file-output---example)
* [Atrybuty](#file-output---attributes)
* [Konfiguracja](#file-output---configuration)
* [Użycie](#file-output---usage)

### <a name="file-output---example"></a>Dane wyjściowe pliku — przykład

Zobacz przykład specyficzny dla języka:

* [Skrypt języka C# (csx)](#file-output---c-script-example)
* [JavaScript](#file-output---javascript-example)

#### <a name="file-output---c-script-example"></a>Wyjście pliku — przykład skryptu w języku C#

Poniższy przykład zapisuje do pliku, który jest przechowywany w usłudze OneDrive.

*function.jsw* pliku definiuje wyzwalacz http z powiązaniem wyjściowym usługi OneDrive:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kod skryptu C# Pobiera tekst z ciągu zapytania i zapisuje go w pliku tekstowym (FunctionsTest.txt zgodnie z definicją w powyższym przykładzie) w katalogu głównym usługi OneDrive obiektu wywołującego:

```csharp
using System.Net;
using System.Text;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, ILogger log, Stream myOneDriveFile)
{
    string data = req.Query
        .FirstOrDefault(q => string.Compare(q.Key, "text", true) == 0)
        .Value;
    await myOneDriveFile.WriteAsync(Encoding.UTF8.GetBytes(data), 0, data.Length);
    myOneDriveFile.Close();
    return;
}
```

#### <a name="file-output---javascript-example"></a>Wyjście pliku — przykład JavaScript

Poniższy przykład zapisuje do pliku, który jest przechowywany w usłudze OneDrive.

*function.jsw* pliku definiuje wyzwalacz http z powiązaniem wyjściowym usługi OneDrive:

```json
{
  "bindings": [
    {
      "authLevel": "anonymous",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "myOneDriveFile",
      "type": "onedrive",
      "direction": "out",
      "path": "FunctionsTest.txt",
      "identity": "userFromRequest"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kod JavaScript Pobiera tekst z ciągu zapytania i zapisuje go w pliku tekstowym (FunctionsTest.txt zgodnie z definicją w konfiguracji powyżej) w katalogu głównym usługi OneDrive obiektu wywołującego.

```js
module.exports = function (context, req) {
    context.bindings.myOneDriveFile = req.query.text;
    context.done();
};
```

### <a name="file-output---attributes"></a>Dane wyjściowe pliku — atrybuty

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)Użyj atrybutu [usługi OneDrive](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OneDriveAttribute.cs) .

### <a name="file-output---configuration"></a>Wyjście pliku — konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `OneDrive` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Nazwij**| nie dotyczy |Wymagane — nazwa zmiennej używana w kodzie funkcji dla pliku. Zobacz [Używanie powiązania danych wyjściowych pliku usługi OneDrive z kodu](#onedrive-output-code).|
|**Wprowadź**| nie dotyczy |Wymagane — musi być ustawiony na wartość `onedrive` .|
|**wskazywa**| nie dotyczy |Wymagane — musi być ustawiony na wartość `out` .|
|**Identity**|**Tożsamość**|Wymagane — tożsamość, która zostanie użyta do wykonania akcji. Może być jedną z następujących wartości:<ul><li><code>userFromRequest</code>-Prawidłowy tylko z [wyzwalaczem http]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code>-Używa tożsamości wcześniej zalogowanego użytkownika o określonym IDENTYFIKATORze. Zobacz <code>userId</code> Właściwość.</li><li><code>userFromToken</code>-Używa tożsamości reprezentowanej przez określony token. Zobacz <code>userToken</code> Właściwość.</li><li><code>clientCredentials</code>— Używa tożsamości aplikacji funkcji.</li></ul>|
|**Nazwa** |**Nazwa** |Wymagana, jeśli i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromId` . Identyfikator podmiotu zabezpieczeń użytkownika skojarzony z wcześniej zalogowanym użytkownikiem.|
|**userToken**|**UserToken**|Wymagana, jeśli i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromToken` . Token ważny dla aplikacji funkcji. |
|**ścieżka**|**Ścieżka**|Wymagane — ścieżka do pliku w usłudze OneDrive.|

<a name="onedrive-output-code"></a>
#### <a name="file-output---usage"></a>Wyjście pliku — użycie

To powiązanie wymaga następujących uprawnień usługi Azure AD:

|Zasób|Uprawnienie|
|--------|--------|
|Microsoft Graph|Uzyskaj pełny dostęp do plików użytkownika|

Powiązanie uwidacznia następujące typy do funkcji .NET Functions:
- Byte []
- Strumień
- ciąg
- Microsoft. Graph. DriveItem





<a name="outlook-output"></a>
## <a name="outlook-output"></a>Dane wyjściowe programu Outlook

Powiązanie danych wyjściowych wiadomości programu Outlook wysyła wiadomość e-mail za pomocą programu Outlook.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#outlook-output---example)
* [Atrybuty](#outlook-output---attributes)
* [Konfiguracja](#outlook-output---configuration)
* [Użycie](#outlook-output---usage)

### <a name="outlook-output---example"></a>Dane wyjściowe programu Outlook — przykład

Zobacz przykład specyficzny dla języka:

* [Skrypt języka C# (csx)](#outlook-output---c-script-example)
* [JavaScript](#outlook-output---javascript-example)

#### <a name="outlook-output---c-script-example"></a>Dane wyjściowe programu Outlook — przykład skryptu w języku C#

Poniższy przykład wysyła wiadomość e-mail za pośrednictwem programu Outlook.

*function.jsw* pliku definiuje wyzwalacz http z powiązaniem wyjściowym wiadomości programu Outlook:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

Kod skryptu C# wysyła wiadomość z wywołującego do odbiorcy określonego w ciągu zapytania:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static void Run(HttpRequest req, out Message message, ILogger log)
{ 
    string emailAddress = req.Query["to"];
    message = new Message(){
        subject = "Greetings",
        body = "Sent from Azure Functions",
        recipient = new Recipient() {
            address = emailAddress
        }
    };
}

public class Message {
    public String subject {get; set;}
    public String body {get; set;}
    public Recipient recipient {get; set;}
}

public class Recipient {
    public String address {get; set;}
    public String name {get; set;}
}
```

#### <a name="outlook-output---javascript-example"></a>Dane wyjściowe programu Outlook — przykład JavaScript

Poniższy przykład wysyła wiadomość e-mail za pośrednictwem programu Outlook.

*function.jsw* pliku definiuje wyzwalacz http z powiązaniem wyjściowym wiadomości programu Outlook:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "message",
      "type": "outlook",
      "direction": "out",
      "identity": "userFromRequest"
    }
  ],
  "disabled": false
}
```

Kod JavaScript wysyła wiadomość z wywołującego do odbiorcy określonego w ciągu zapytania:

```js
module.exports = function (context, req) {
    context.bindings.message = {
        subject: "Greetings",
        body: "Sent from Azure Functions with JavaScript",
        recipient: {
            address: req.query.to 
        } 
    };
    context.done();
};
```

### <a name="outlook-output---attributes"></a>Dane wyjściowe programu Outlook — atrybuty

W [bibliotekach klas języka C#](functions-dotnet-class-library.md)Użyj atrybutu [programu Outlook](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/OutlookAttribute.cs) .

### <a name="outlook-output---configuration"></a>Dane wyjściowe programu Outlook — konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `Outlook` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Nazwij**| nie dotyczy |Wymagane — nazwa zmiennej używana w kodzie funkcji dla wiadomości e-mail. Zobacz [Używanie powiązania danych wyjściowych wiadomości programu Outlook z kodu](#outlook-output-code).|
|**Wprowadź**| nie dotyczy |Wymagane — musi być ustawiony na wartość `outlook` .|
|**wskazywa**| nie dotyczy |Wymagane — musi być ustawiony na wartość `out` .|
|**Identity**|**Tożsamość**|Wymagane — tożsamość, która zostanie użyta do wykonania akcji. Może być jedną z następujących wartości:<ul><li><code>userFromRequest</code>-Prawidłowy tylko z [wyzwalaczem http]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code>-Używa tożsamości wcześniej zalogowanego użytkownika o określonym IDENTYFIKATORze. Zobacz <code>userId</code> Właściwość.</li><li><code>userFromToken</code>-Używa tożsamości reprezentowanej przez określony token. Zobacz <code>userToken</code> Właściwość.</li><li><code>clientCredentials</code>— Używa tożsamości aplikacji funkcji.</li></ul>|
|**Nazwa**|**Nazwa**  |Wymagana, jeśli i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromId` . Identyfikator podmiotu zabezpieczeń użytkownika skojarzony z wcześniej zalogowanym użytkownikiem.|
|**userToken**|**UserToken**|Wymagana, jeśli i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromToken` . Token ważny dla aplikacji funkcji. |

<a name="outlook-output-code"></a>
### <a name="outlook-output---usage"></a>Dane wyjściowe programu Outlook — użycie

To powiązanie wymaga następujących uprawnień usługi Azure AD:

|Zasób|Uprawnienie|
|--------|--------|
|Microsoft Graph|Wyślij wiadomość jako użytkownika|

Powiązanie uwidacznia następujące typy do funkcji .NET Functions:
- Microsoft. Graph. Message
- Newtonsoft.Js. LINQ. JObject
- ciąg
- Niestandardowe typy obiektów (przy użyciu powiązania modelu strukturalnego)






## <a name="webhooks"></a>Elementy webhook

Elementy webhook umożliwiają reagowanie na zdarzenia w Microsoft Graph. Aby obsługiwać elementy webhook, funkcje są konieczne do tworzenia, odświeżania i reagowania na _subskrypcje elementu webhook_. Kompletne rozwiązanie elementu webhook wymaga połączenia następujących powiązań:
- [Wyzwalacz Microsoft Graph elementu webhook](#webhook-trigger) umożliwia reagowanie na przychodzące elementy webhook.
- [Powiązanie danych wejściowych subskrypcji elementu webhook](#webhook-input) umożliwia wyświetlenie listy istniejących subskrypcji i ich opcjonalne odświeżenie. Microsoft Graph
- [Microsoft Graph powiązanie danych wyjściowych subskrypcji elementu webhook](#webhook-output) umożliwia tworzenie lub usuwanie subskrypcji elementu webhook.

Powiązania same nie wymagają żadnych uprawnień usługi Azure AD, ale musisz zażądać uprawnień odpowiednich dla typu zasobu, do którego chcesz reagować. Aby uzyskać listę uprawnień wymaganych dla każdego typu zasobu, zobacz [uprawnienia do subskrypcji](https://docs.microsoft.com/graph/api/subscription-post-subscriptions?view=graph-rest-1.0).

Aby uzyskać więcej informacji na temat elementów webhook, zobacz [Praca z elementami webhook w Microsoft Graph].





## <a name="webhook-trigger"></a>Wyzwalacz elementu webhook

Wyzwalacz Microsoft Graph elementu webhook umożliwia funkcji reagowanie na przychodzące elementy webhook z Microsoft Graph. Każde wystąpienie tego wyzwalacza może reagować na jeden typ zasobu programu Microsoft Graph.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#webhook-trigger---example)
* [Atrybuty](#webhook-trigger---attributes)
* [Konfiguracja](#webhook-trigger---configuration)
* [Użycie](#webhook-trigger---usage)

### <a name="webhook-trigger---example"></a>Wyzwalacz elementu webhook — przykład

Zobacz przykład specyficzny dla języka:

* [Skrypt języka C# (csx)](#webhook-trigger---c-script-example)
* [JavaScript](#webhook-trigger---javascript-example)

#### <a name="webhook-trigger---c-script-example"></a>Wyzwalacz elementu webhook — przykład skryptu w języku C#

W poniższym przykładzie przedstawiono obsługę elementów webhook dla przychodzących komunikatów programu Outlook. Aby użyć wyzwalacza elementu webhook, możesz [utworzyć subskrypcję](#webhook-output---example) [, aby uniemożliwić](#webhook-subscription-refresh) jej wygaśnięcie.

*function.jsw* pliku definiuje wyzwalacz elementu webhook:

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

Kod skryptu C# reaguje na przychodzące wiadomości e-mail i rejestruje treść nadawców wysyłanych przez odbiorcę oraz zawierający "Azure Functions" w temacie:

```csharp
#r "Microsoft.Graph"
using Microsoft.Graph;
using System.Net;
using Microsoft.Extensions.Logging;

public static async Task Run(Message msg, ILogger log)  
{
    log.LogInformation("Microsoft Graph webhook trigger function processed a request.");

    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if (msg.Subject.Contains("Azure Functions") && msg.From.Equals(msg.Sender)) {
        log.LogInformation($"Processed email: {msg.BodyPreview}");
    }
}
```

#### <a name="webhook-trigger---javascript-example"></a>Wyzwalacz elementu webhook — przykład JavaScript

W poniższym przykładzie przedstawiono obsługę elementów webhook dla przychodzących komunikatów programu Outlook. Aby użyć wyzwalacza elementu webhook, możesz [utworzyć subskrypcję](#webhook-output---example) [, aby uniemożliwić](#webhook-subscription-refresh) jej wygaśnięcie.

*function.jsw* pliku definiuje wyzwalacz elementu webhook:

```json
{
  "bindings": [
    {
      "name": "msg",
      "type": "GraphWebhookTrigger",
      "direction": "in",
      "resourceType": "#Microsoft.Graph.Message"
    }
  ],
  "disabled": false
}
```

Kod JavaScript reaguje na przychodzące wiadomości e-mail i rejestruje treść nadawców wysyłanych przez odbiorcę oraz zawierający "Azure Functions" w temacie:

```js
module.exports = function (context) {
    context.log("Microsoft Graph webhook trigger function processed a request.");
    const msg = context.bindings.msg
    // Testable by sending oneself an email with the subject "Azure Functions" and some text body
    if((msg.subject.indexOf("Azure Functions") > -1) && (msg.from === msg.sender) ) {
      context.log(`Processed email: ${msg.bodyPreview}`);
    }
    context.done();
};
```

### <a name="webhook-trigger---attributes"></a>Wyzwalacz elementu webhook — atrybuty

W [bibliotekach klas języka C#](functions-dotnet-class-library.md), Użyj atrybutu [GraphWebhookTrigger](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookTriggerAttribute.cs) .

### <a name="webhook-trigger---configuration"></a>Wyzwalacz elementu webhook — konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `GraphWebhookTrigger` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Nazwij**| nie dotyczy |Wymagane — nazwa zmiennej używana w kodzie funkcji dla wiadomości e-mail. Zobacz [Używanie powiązania danych wyjściowych wiadomości programu Outlook z kodu](#outlook-output-code).|
|**Wprowadź**| nie dotyczy |Wymagane — musi być ustawiony na wartość `graphWebhook` .|
|**wskazywa**| nie dotyczy |Wymagane — musi być ustawiony na wartość `trigger` .|
|**resourceType**|**ResourceType**|Wymagane — zasób grafu, dla którego ta funkcja powinna reagować na elementy webhook. Może być jedną z następujących wartości:<ul><li><code>#Microsoft.Graph.Message</code>-zmiany wprowadzone w komunikatach programu Outlook.</li><li><code>#Microsoft.Graph.DriveItem</code>-zmiany wprowadzone do elementów głównych usługi OneDrive.</li><li><code>#Microsoft.Graph.Contact</code>— zmiany kontaktów osobistych w programie Outlook.</li><li><code>#Microsoft.Graph.Event</code>— zmiany wprowadzone do elementów kalendarza programu Outlook.</li></ul>|

> [!Note]
> Aplikacja funkcji może mieć tylko jedną funkcję, która jest zarejestrowana dla danej `resourceType` wartości.

### <a name="webhook-trigger---usage"></a>Wyzwalacz elementu webhook — użycie

Powiązanie uwidacznia następujące typy do funkcji .NET Functions:
- Microsoft Graph typy zestawu SDK odpowiednie dla typu zasobu, takie jak `Microsoft.Graph.Message` lub `Microsoft.Graph.DriveItem` .
- Niestandardowe typy obiektów (przy użyciu powiązania modelu strukturalnego)




<a name="webhook-input"></a>
## <a name="webhook-input"></a>Wejście elementu webhook

Powiązanie danych wejściowych elementu webhook Microsoft Graph umożliwia pobranie listy subskrypcji zarządzanych przez tę aplikację funkcji. Powiązanie odczytuje z magazynu aplikacji funkcji, więc nie odzwierciedla innych subskrypcji utworzonych spoza aplikacji.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#webhook-input---example)
* [Atrybuty](#webhook-input---attributes)
* [Konfiguracja](#webhook-input---configuration)
* [Użycie](#webhook-input---usage)

### <a name="webhook-input---example"></a>Wejście elementu webhook — przykład

Zobacz przykład specyficzny dla języka:

* [Skrypt języka C# (csx)](#webhook-input---c-script-example)
* [JavaScript](#webhook-input---javascript-example)

#### <a name="webhook-input---c-script-example"></a>Dane wejściowe elementu webhook — przykład skryptu w języku C#

Poniższy przykład pobiera wszystkie subskrypcje dla użytkownika wywołującego i usuwa je.

*function.jsw* pliku definiuje wyzwalacz http z powiązaniem wejściowym subskrypcji i powiązaniem danych wyjściowych subskrypcji korzystającej z akcji usuwania:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kod skryptu C# pobiera subskrypcje i usuwa je:

```csharp
using System.Net;
using Microsoft.Extensions.Logging;

public static async Task Run(HttpRequest req, string[] existingSubscriptions, IAsyncCollector<string> subscriptionsToDelete, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");
    foreach (var subscription in existingSubscriptions)
    {
        log.LogInformation($"Deleting subscription {subscription}");
        await subscriptionsToDelete.AddAsync(subscription);
    }
}
```

#### <a name="webhook-input---javascript-example"></a>Dane wejściowe elementu webhook — przykład JavaScript

Poniższy przykład pobiera wszystkie subskrypcje dla użytkownika wywołującego i usuwa je.

*function.jsw* pliku definiuje wyzwalacz http z powiązaniem wejściowym subskrypcji i powiązaniem danych wyjściowych subskrypcji korzystającej z akcji usuwania:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in",
      "filter": "userFromRequest"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToDelete",
      "direction": "out",
      "action": "delete",
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "res",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kod JavaScript pobiera subskrypcje i usuwa je:

```js
module.exports = function (context, req) {
    const existing = context.bindings.existingSubscriptions;
    var toDelete = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Deleting subscription ${existing[i]}`);
        todelete.push(existing[i]);
    }
    context.bindings.subscriptionsToDelete = toDelete;
    context.done();
};
```

### <a name="webhook-input---attributes"></a>Wejście elementu webhook — atrybuty

W [bibliotekach klas języka C#](functions-dotnet-class-library.md), Użyj atrybutu [GraphWebhookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs) .

### <a name="webhook-input---configuration"></a>Dane wejściowe elementu webhook — konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `GraphWebhookSubscription` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Nazwij**| nie dotyczy |Wymagane — nazwa zmiennej używana w kodzie funkcji dla wiadomości e-mail. Zobacz [Używanie powiązania danych wyjściowych wiadomości programu Outlook z kodu](#outlook-output-code).|
|**Wprowadź**| nie dotyczy |Wymagane — musi być ustawiony na wartość `graphWebhookSubscription` .|
|**wskazywa**| nie dotyczy |Wymagane — musi być ustawiony na wartość `in` .|
|**filtru**|**Filtr**| Jeśli jest ustawiona na `userFromRequest` , powiązanie będzie pobierać tylko subskrypcje należące do użytkownika wywołującego (prawidłowe tylko przy użyciu [wyzwalacza http]).| 

### <a name="webhook-input---usage"></a>Dane wejściowe elementu webhook

Powiązanie uwidacznia następujące typy do funkcji .NET Functions:
- ciąg []
- Niestandardowe tablice typów obiektów
- Newtonsoft.Js. LINQ. JObject []
- Microsoft. Graph. Subscription []





## <a name="webhook-output"></a>Wyjście elementu webhook

Powiązanie danych wyjściowych subskrypcji elementu webhook umożliwia tworzenie, usuwanie i odświeżanie subskrypcji elementu webhook w Microsoft Graph.

Ta sekcja zawiera następujące podsekcje:

* [Przykład](#webhook-output---example)
* [Atrybuty](#webhook-output---attributes)
* [Konfiguracja](#webhook-output---configuration)
* [Użycie](#webhook-output---usage)

### <a name="webhook-output---example"></a>Dane wyjściowe elementu webhook — przykład

Zobacz przykład specyficzny dla języka:

* [Skrypt języka C# (csx)](#webhook-output---c-script-example)
* [JavaScript](#webhook-output---javascript-example)

#### <a name="webhook-output---c-script-example"></a>Wyjście elementu webhook — przykład skryptu w języku C#

Poniższy przykład tworzy subskrypcję. Możesz [odświeżyć subskrypcję](#webhook-subscription-refresh) , aby uniemożliwić jej wygaśnięcie.

*function.jsw* pliku definiuje wyzwalacz http z powiązaniem danych wyjściowych subskrypcji przy użyciu akcji Create:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kod skryptu w języku C# rejestruje element webhook, który będzie powiadamiał tę aplikację funkcji, gdy wywołujący użytkownik odbiera komunikat programu Outlook:

```csharp
using System;
using System.Net;
using Microsoft.Extensions.Logging;

public static HttpResponseMessage run(HttpRequestMessage req, out string clientState, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
    clientState = Guid.NewGuid().ToString();
    return new HttpResponseMessage(HttpStatusCode.OK);
}
```

#### <a name="webhook-output---javascript-example"></a>Dane wyjściowe elementu webhook — przykład JavaScript

Poniższy przykład tworzy subskrypcję. Możesz [odświeżyć subskrypcję](#webhook-subscription-refresh) , aby uniemożliwić jej wygaśnięcie.

*function.jsw* pliku definiuje wyzwalacz http z powiązaniem danych wyjściowych subskrypcji przy użyciu akcji Create:

```json
{
  "bindings": [
    {
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "clientState",
      "direction": "out",
      "action": "create",
      "subscriptionResource": "me/mailFolders('Inbox')/messages",
      "changeTypes": [
        "created"
      ],
      "identity": "userFromRequest"
    },
    {
      "type": "http",
      "name": "$return",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

Kod JavaScript rejestruje element webhook, który będzie powiadamiał tę aplikację funkcji, gdy wywołujący użytkownik odbiera komunikat programu Outlook:

```js
const uuidv4 = require('uuid/v4');

module.exports = function (context, req) {
    context.bindings.clientState = uuidv4();
    context.done();
};
```

### <a name="webhook-output---attributes"></a>Elementy wyjściowe elementu webhook — atrybuty

W [bibliotekach klas języka C#](functions-dotnet-class-library.md), Użyj atrybutu [GraphWebhookSubscription](https://github.com/Azure/azure-functions-microsoftgraph-extension/blob/master/src/MicrosoftGraphBinding/Bindings/GraphWebhookSubscriptionAttribute.cs) .

### <a name="webhook-output---configuration"></a>Wyjście elementu webhook — konfiguracja

W poniższej tabeli objaśniono właściwości konfiguracji powiązań, które zostały ustawione w *function.js* pliku i `GraphWebhookSubscription` atrybutu.

|function.jswłaściwości | Właściwość atrybutu |Opis|
|---------|---------|----------------------|
|**Nazwij**| nie dotyczy |Wymagane — nazwa zmiennej używana w kodzie funkcji dla wiadomości e-mail. Zobacz [Używanie powiązania danych wyjściowych wiadomości programu Outlook z kodu](#outlook-output-code).|
|**Wprowadź**| nie dotyczy |Wymagane — musi być ustawiony na wartość `graphWebhookSubscription` .|
|**wskazywa**| nie dotyczy |Wymagane — musi być ustawiony na wartość `out` .|
|**Identity**|**Tożsamość**|Wymagane — tożsamość, która zostanie użyta do wykonania akcji. Może być jedną z następujących wartości:<ul><li><code>userFromRequest</code>-Prawidłowy tylko z [wyzwalaczem http]. Używa tożsamości użytkownika wywołującego.</li><li><code>userFromId</code>-Używa tożsamości wcześniej zalogowanego użytkownika o określonym IDENTYFIKATORze. Zobacz <code>userId</code> Właściwość.</li><li><code>userFromToken</code>-Używa tożsamości reprezentowanej przez określony token. Zobacz <code>userToken</code> Właściwość.</li><li><code>clientCredentials</code>— Używa tożsamości aplikacji funkcji.</li></ul>|
|**Nazwa**|**Nazwa**  |Wymagana, jeśli i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromId` . Identyfikator podmiotu zabezpieczeń użytkownika skojarzony z wcześniej zalogowanym użytkownikiem.|
|**userToken**|**UserToken**|Wymagana, jeśli i tylko wtedy, gdy _tożsamość_ jest ustawiona na `userFromToken` . Token ważny dla aplikacji funkcji. |
|**transakcji**|**Akcja**|Wymagane — określa akcję, która ma zostać wykonana przez powiązanie. Może być jedną z następujących wartości:<ul><li><code>create</code>— Rejestruje nową subskrypcję.</li><li><code>delete</code>-Usuwa określoną subskrypcję.</li><li><code>refresh</code>-Odświeża określoną subskrypcję, aby uniemożliwić jej wygaśnięcie.</li></ul>|
|**subscriptionResource**|**SubscriptionResource**|Wymagana, jeśli i tylko wtedy, gdy _Akcja_ jest ustawiona na `create` . Określa zasób Microsoft Graph, który będzie monitorowany pod kątem zmian. Zobacz [Praca z elementami webhook w Microsoft Graph]. |
|**changeType**|**ChangeType**|Wymagana, jeśli i tylko wtedy, gdy _Akcja_ jest ustawiona na `create` . Wskazuje typ zmiany w subskrybowanym zasobie, który zgłosi powiadomienie. Obsługiwane są następujące wartości: `created` , `updated` , `deleted` . Wiele wartości można łączyć za pomocą listy rozdzielanej przecinkami.|

### <a name="webhook-output---usage"></a>Dane wyjściowe elementu webhook — użycie

Powiązanie uwidacznia następujące typy do funkcji .NET Functions:
- ciąg
- Microsoft. Graph. Subscription




<a name="webhook-examples"></a>
## <a name="webhook-subscription-refresh"></a>Odświeżanie subskrypcji elementu webhook

Istnieją dwa podejścia do odświeżania subskrypcji:

- Użyj tożsamości aplikacji, aby obsłużyć wszystkie subskrypcje. Będzie to wymagało zgody od administratora Azure Active Directory. Mogą one być używane przez wszystkie języki obsługiwane przez Azure Functions.
- Użyj tożsamości skojarzonej z każdą subskrypcją przez ręczne powiązanie każdego identyfikatora użytkownika. To wymaga pewnego niestandardowego kodu do wykonania powiązania. Może być używany tylko przez funkcje platformy .NET.

Ta sekcja zawiera przykład dla każdej z tych metod:

* [Przykład tożsamości aplikacji](#webhook-subscription-refresh---app-identity-example)
* [Przykład tożsamości użytkownika](#webhook-subscription-refresh---user-identity-example)

### <a name="webhook-subscription-refresh---app-identity-example"></a>Odświeżanie subskrypcji elementu webhook — przykład tożsamości aplikacji

Zobacz przykład specyficzny dla języka:

* [Skrypt języka C# (csx)](#app-identity-refresh---c-script-example)
* JavaScript

### <a name="app-identity-refresh---c-script-example"></a>Odświeżanie tożsamości aplikacji — przykład skryptu w języku C#

W poniższym przykładzie zostanie użyta tożsamość aplikacji do odświeżenia subskrypcji.

*function.json* definiuje wyzwalacz czasomierza z powiązaniem wejściowym subskrypcji i powiązaniem wyjściowym subskrypcji:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

Kod skryptu C# odświeża subskrypcje:

```csharp
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, string[] existingSubscriptions, ICollector<string> subscriptionsToRefresh, ILogger log)
{
    // This template uses application permissions and requires consent from an Azure Active Directory admin.
    // See https://go.microsoft.com/fwlink/?linkid=858780
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
      log.LogInformation($"Refreshing subscription {subscription}");
      subscriptionsToRefresh.Add(subscription);
    }
}
```

### <a name="app-identity-refresh---c-script-example"></a>Odświeżanie tożsamości aplikacji — przykład skryptu w języku C#

W poniższym przykładzie zostanie użyta tożsamość aplikacji do odświeżenia subskrypcji.

*function.json* definiuje wyzwalacz czasomierza z powiązaniem wejściowym subskrypcji i powiązaniem wyjściowym subskrypcji:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "subscriptionsToRefresh",
      "direction": "out",
      "action": "refresh",
      "identity": "clientCredentials"
    }
  ],
  "disabled": false
}
```

Kod JavaScript odświeża subskrypcje:

```js
// This template uses application permissions and requires consent from an Azure Active Directory admin.
// See https://go.microsoft.com/fwlink/?linkid=858780

module.exports = function (context) {
    const existing = context.bindings.existingSubscriptions;
    var toRefresh = [];
    for (var i = 0; i < existing.length; i++) {
        context.log(`Refreshing subscription ${existing[i]}`);
        toRefresh.push(existing[i]);
    }
    context.bindings.subscriptionsToRefresh = toRefresh;
    context.done();
};
```

### <a name="webhook-subscription-refresh---user-identity-example"></a>Odświeżanie subskrypcji elementu webhook — przykład tożsamości użytkownika

W poniższym przykładzie zostanie użyta tożsamość użytkownika w celu odświeżenia subskrypcji.

*function.jsw* pliku definiuje wyzwalacz czasomierza i odkłada powiązania danych wejściowych subskrypcji do kodu funkcji:

```json
{
  "bindings": [
    {
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in",
      "schedule": "0 * * */2 * *"
    },
    {
      "type": "graphWebhookSubscription",
      "name": "existingSubscriptions",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

Kod skryptu w języku C# odświeża subskrypcje i tworzy powiązanie danych wyjściowych w kodzie przy użyciu tożsamości poszczególnych użytkowników:

```csharp
using System;
using Microsoft.Extensions.Logging;

public static async Task Run(TimerInfo myTimer, UserSubscription[] existingSubscriptions, IBinder binder, ILogger log)
{
  log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    foreach (var subscription in existingSubscriptions)
    {
        // binding in code to allow dynamic identity
        using (var subscriptionsToRefresh = await binder.BindAsync<IAsyncCollector<string>>(
            new GraphWebhookSubscriptionAttribute() {
                Action = "refresh",
                Identity = "userFromId",
                UserId = subscription.UserId
            }
        ))
        {
            log.LogInformation($"Refreshing subscription {subscription}");
            await subscriptionsToRefresh.AddAsync(subscription);
        }

    }
}

public class UserSubscription {
    public string UserId {get; set;}
    public string Id {get; set;}
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wyzwalaczach i powiązaniach usługi Azure Functions](functions-triggers-bindings.md)

[Wyzwalacz HTTP]: functions-bindings-http-webhook.md
[Praca z elementami webhook w Microsoft Graph]: https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/webhooks
