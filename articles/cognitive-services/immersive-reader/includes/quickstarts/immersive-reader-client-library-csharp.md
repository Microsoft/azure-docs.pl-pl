---
title: Czytnik immersyjny — Biblioteka klienta C# — Szybki Start
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start utworzysz aplikację internetową od podstaw i dodasz funkcję interfejsu API czytnika immersyjny.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: include
ms.date: 09/14/2020
ms.author: nitinme
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: cef5aaae58797e38745b3f5164c171581a005562
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94371842"
---
[Czytnik immersyjny](https://www.onenote.com/learningtools) jest specjalnie zaprojektowanym narzędziem, które implementuje sprawdzone techniki w celu zwiększenia czytelności dla nowych czytelników, nauk językowych i osób, które mają różnice szkoleniowe, takie jak Dyslexia. Możesz użyć czytnika immersyjny w aplikacjach, aby odizolować tekst, aby zwiększyć fokus, wyświetlić obrazy dla często używanych wyrazów, wyróżnić części mowy, odczytywać zaznaczone fragmenty tekstu na głos, tłumaczyć słowa i frazy w czasie rzeczywistym i nie tylko.

W tym przewodniku szybki start utworzysz aplikację internetową od podstaw i zintegrujesz czytnik immersyjny przy użyciu biblioteki klienta czytnika immersyjny. Pełny przykład pracy tego przewodnika Szybki Start jest dostępny [w witrynie GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services)
* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads)
* Zasób czytnika immersyjny skonfigurowany do Azure Active Directory uwierzytelniania. Postępuj zgodnie z [tymi instrukcjami](../../how-to-create-immersive-reader.md) , aby rozpocząć konfigurację. W przypadku konfigurowania przykładowych właściwości projektu konieczne będzie utworzenie niektórych wartości. Zapisz dane wyjściowe sesji w pliku tekstowym do użycia w przyszłości.

## <a name="create-a-web-app-project"></a>Tworzenie projektu aplikacji internetowej

Utwórz nowy projekt w programie Visual Studio przy użyciu szablonu aplikacji sieci Web ASP.NET Core z wbudowanym modelem-View-Controller i ASP.NET Core 2,1. Nadaj projektowi nazwę "QuickstartSampleWebApp".

![Nowy projekt — C #](../../media/quickstart-csharp/1-createproject.png)

![Konfiguruj nowy projekt — C #](../../media/quickstart-csharp/2-configureproject.png)

![Nowa aplikacja sieci Web ASP.NET Core — C #](../../media/quickstart-csharp/3-createmvc.png)

## <a name="set-up-authentication"></a>Konfigurowanie uwierzytelniania

### <a name="configure-authentication-values"></a>Konfigurowanie wartości uwierzytelniania

Kliknij prawym przyciskiem myszy projekt w _Eksplorator rozwiązań_ i wybierz polecenie **Zarządzaj kluczami tajnymi użytkownika**. Spowoduje to otwarcie pliku o nazwie _secrets.jsna_. Ten plik nie jest sprawdzany w kontroli źródła. Dowiedz się więcej [tutaj](/aspnet/core/security/app-secrets?preserve-view=true&tabs=windows&view=aspnetcore-3.1). Zastąp zawartość _secrets.js_ przy użyciu poniższego, podając wartości podanych podczas tworzenia zasobu czytnika immersyjny.

```json
{
  "TenantId": "YOUR_TENANT_ID",
  "ClientId": "YOUR_CLIENT_ID",
  "ClientSecret": "YOUR_CLIENT_SECRET",
  "Subdomain": "YOUR_SUBDOMAIN"
}
```

### <a name="install-active-directory-nuget-package"></a>Zainstaluj Active Directory pakiet NuGet

Poniższy kod używa obiektów z pakietu NuGet **Microsoft. IdentityModel. clients. ActiveDirectory** , dlatego należy dodać odwołanie do tego pakietu w projekcie.

Otwórz konsolę Menedżera pakietów NuGet z **menu Narzędzia — > Menedżer pakietów NuGet — > konsolę Menedżera pakietów** i uruchom następujące polecenie:

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.2.0
```

### <a name="update-the-controller-to-acquire-the-token"></a>Aktualizowanie kontrolera w celu uzyskania tokenu 

Otwórz _Controllers\HomeController.cs_ i Dodaj następujący kod po instrukcjach _using_ w górnej części pliku.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Teraz skonfigurujemy kontroler do uzyskiwania wartości usługi Azure AD z _secrets.js_. W górnej części klasy _HomeController_ , ```public class HomeController : Controller {``` Dodaj następujący kod.

```csharp
private readonly string TenantId;     // Azure subscription TenantId
private readonly string ClientId;     // Azure AD ApplicationId
private readonly string ClientSecret; // Azure AD Application Service Principal password
private readonly string Subdomain;    // Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')

public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
{
    TenantId = configuration["TenantId"];
    ClientId = configuration["ClientId"];
    ClientSecret = configuration["ClientSecret"];
    Subdomain = configuration["Subdomain"];

    if (string.IsNullOrWhiteSpace(TenantId))
    {
        throw new ArgumentNullException("TenantId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientId))
    {
        throw new ArgumentNullException("ClientId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientSecret))
    {
        throw new ArgumentNullException("ClientSecret is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(Subdomain))
    {
        throw new ArgumentNullException("Subdomain is null! Did you add that info to secrets.json?");
    }
}

/// <summary>
/// Get an Azure AD authentication token
/// </summary>
private async Task<string> GetTokenAsync()
{
    string authority = $"https://login.windows.net/{TenantId}";
    const string resource = "https://cognitiveservices.azure.com/";

    AuthenticationContext authContext = new AuthenticationContext(authority);
    ClientCredential clientCredential = new ClientCredential(ClientId, ClientSecret);

    AuthenticationResult authResult = await authContext.AcquireTokenAsync(resource, clientCredential);

    return authResult.AccessToken;
}

[HttpGet]
public async Task<JsonResult> GetTokenAndSubdomain()
{
    try
    {
        string tokenResult = await GetTokenAsync();

        return new JsonResult(new { token = tokenResult, subdomain = Subdomain });
    }
    catch (Exception e)
    {
        string message = "Unable to acquire Azure AD token. Check the debugger for more information.";
        Debug.WriteLine(message, e);
        return new JsonResult(new { error = message });
    }
}
```

## <a name="add-sample-content"></a>Dodaj przykładową zawartość
Najpierw Otwórz _Views\Shared\Layout.cshtml_. Przed wierszem ```</head>``` Dodaj następujący kod:

```html
@RenderSection("Styles", required: false)
```

Teraz dodamy przykładową zawartość do tej aplikacji sieci Web. Otwórz _Views\Home\Index.cshtml_ i Zastąp wszystkie automatycznie wygenerowany kod tym przykładem:

```html
@{
    ViewData["Title"] = "Immersive Reader C# Quickstart";
}

@section Styles {
    <style type="text/css">
        .immersive-reader-button {
            background-color: white;
            margin-top: 5px;
            border: 1px solid black;
            float: right;
        }
    </style>
}

<div class="container">
    <button class="immersive-reader-button" data-button-style="iconAndText" data-locale="en"></button>

    <h1 id="ir-title">About Immersive Reader</h1>
    <div id="ir-content" lang="en-us">
        <p>
            Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences.
            The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader
            <ul>
                <li>
                    Shows content in a minimal reading view
                </li>
                <li>
                    Displays pictures of commonly used words
                </li>
                <li>
                    Highlights nouns, verbs, adjectives, and adverbs
                </li>
                <li>
                    Reads your content out loud to you
                </li>
                <li>
                    Translates your content into another language
                </li>
                <li>
                    Breaks down words into syllables
                </li>
            </ul>
        </p>
        <h3>
            The Immersive Reader is available in many languages.
        </h3>
        <p lang="es-es">
            El Lector inmersivo está disponible en varios idiomas.
        </p>
        <p lang="zh-cn">
            沉浸式阅读器支持许多语言
        </p>
        <p lang="de-de">
            Der plastische Reader ist in vielen Sprachen verfügbar.
        </p>
        <p lang="ar-eg" dir="rtl" style="text-align:right">
            يتوفر \"القارئ الشامل\" في العديد من اللغات.
        </p>
    </div>
</div>
```

Zwróć uwagę, że cały tekst ma atrybut **lang** , który opisuje Języki tekstu. Ten atrybut pomaga czytnikom immersyjny zapewnić odpowiednie funkcje języka i gramatyki.

## <a name="add-javascript-to-handle-launching-immersive-reader"></a>Dodaj kod JavaScript do obsługi uruchamiania czytnika immersyjny

Biblioteka czytników immersyjny udostępnia funkcje, takie jak uruchamianie czytnika Immersyjnygo i renderowanie przycisków czytnika szczegółowego. Dowiedz się więcej [tutaj](../../reference.md).

W dolnej części _Views\Home\Index.cshtml_ Dodaj następujący kod:

```html
@section Scripts
{
    <script src="https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js"></script>
    <script>
        function getTokenAndSubdomainAsync() {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "@Url.Action("GetTokenAndSubdomain", "Home")",
                    type: "GET",
                    success: function (data) {
                        if (data.error) {
                            reject(data.error);
                        } else {
                            resolve(data);
                        }
                    },
                    error: function (err) {
                        reject(err);
                    }
                });
            });
        }
    
        $(".immersive-reader-button").click(function () {
            handleLaunchImmersiveReader();
        });
    
        function handleLaunchImmersiveReader() {
            getTokenAndSubdomainAsync()
                .then(function (response) {
                    const token = response["token"];
                    const subdomain = response["subdomain"];
    
                    // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#chunk
                    const data = {
                        title: $("#ir-title").text(),
                        chunks: [{
                            content: $("#ir-content").html(),
                            mimeType: "text/html"
                        }]
                    };
    
                    // Learn more about options https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#options
                    const options = {
                        "onExit": exitCallback,
                        "uiZIndex": 2000
                    };
    
                    ImmersiveReader.launchAsync(token, subdomain, data, options)
                        .catch(function (error) {
                            alert("Error in launching the Immersive Reader. Check the console.");
                            console.log(error);
                        });
                })
                .catch(function (error) {
                    alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                    console.log(error);
                });
        }
    
        function exitCallback() {
            console.log("This is the callback function. It is executed when the Immersive Reader closes.");
        }
    </script>
}
```

## <a name="build-and-run-the-app"></a>Skompiluj i uruchom aplikację

Na pasku menu wybierz kolejno opcje **debuguj > Rozpocznij debugowanie** lub naciśnij klawisz **F5** , aby uruchomić aplikację.

W przeglądarce powinny zostać wyświetlone następujące tematy:

![Przykładowa aplikacja — C #](../../media/quickstart-csharp/4-buildapp.png)

## <a name="launch-the-immersive-reader"></a>Uruchom czytnik immersyjny

Po kliknięciu przycisku "czytnik immersyjny" zobaczysz czytnik immersyjny uruchamiany z zawartością na stronie.

![Czytnik immersyjny-C #](../../media/quickstart-csharp/5-viewimmersivereader.png)

## <a name="next-steps"></a>Następne kroki

* Poznaj [zestaw SDK czytnika immersyjny](https://github.com/microsoft/immersive-reader-sdk) i [Kompendium zestawu SDK czytnika immersyjny](../../reference.md)