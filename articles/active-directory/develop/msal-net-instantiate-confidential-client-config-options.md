---
title: Tworzenie wystąpienia poufnej aplikacji klienckiej (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć wystąpienie poufnej aplikacji klienckiej z opcjami konfiguracji przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: d477c419bb677a6b8f24a3aae26c403e47cc96cb
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99583946"
---
# <a name="instantiate-a-confidential-client-application-with-configuration-options-using-msalnet"></a>Tworzenie wystąpienia poufnej aplikacji klienckiej z opcjami konfiguracji przy użyciu MSAL.NET

W tym artykule opisano sposób tworzenia wystąpienia [poufnej aplikacji klienckiej](msal-client-applications.md) przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .net (MSAL.NET).  Aplikacja tworzy wystąpienie z opcjami konfiguracji zdefiniowanymi w pliku ustawień.

Przed zainicjowaniem aplikacji należy najpierw ją [zarejestrować](quickstart-register-app.md) , aby można było zintegrować aplikację z platformą tożsamości firmy Microsoft. Po zarejestrowaniu mogą być potrzebne następujące informacje (które można znaleźć w Azure Portal):

- Identyfikator klienta (ciąg reprezentujący identyfikator GUID)
- Adres URL dostawcy tożsamości (nazywany wystąpieniem) i odbiorcy logowania dla aplikacji. Te dwa parametry są określane zbiorczo jako urząd.
- Identyfikator dzierżawy, jeśli piszesz aplikację biznesową wyłącznie dla Twojej organizacji (nazywaną również aplikacją z jedną dzierżawą).
- Wpis tajny aplikacji (ciąg tajny klienta) lub certyfikat (typu X509Certificate2), jeśli jest to poufna aplikacja kliencka.
- W przypadku aplikacji sieci Web, a czasami dla publicznych aplikacji klienckich (w szczególności gdy aplikacja wymaga użycia brokera), należy również ustawić redirectUri, w którym dostawca tożsamości będzie kontaktować się z aplikacją przy użyciu tokenów zabezpieczających.

## <a name="configure-the-application-from-the-config-file"></a>Skonfiguruj aplikację z pliku konfiguracji
Nazwa właściwości opcji w MSAL.NET jest zgodna z nazwą właściwości `AzureADOptions` w ASP.NET Core, więc nie trzeba pisać kodu łączenia.

Konfiguracja aplikacji ASP.NET Core jest opisana w *appsettings.js* pliku:

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "Domain": "[Enter the domain of your tenant, e.g. contoso.onmicrosoft.com]",
    "TenantId": "[Enter 'common', or 'organizations' or the Tenant Id (Obtained from the Azure portal. Select 'Endpoints' from the 'App registrations' blade and use the GUID in any of the URLs), e.g. da41245a5-11b3-996c-00a8-4d99re19f292]",
    "ClientId": "[Enter the Client Id (Application ID obtained from the Azure portal), e.g. ba74781c2-53c2-442a-97c2-3d60re42f403]",
    "CallbackPath": "/signin-oidc",
    "SignedOutCallbackPath ": "/signout-callback-oidc",

    "ClientSecret": "[Copy the client secret added to the app from the Azure portal]"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

Począwszy od MSAL.NET v3. x, możesz skonfigurować poufną aplikację kliencką z pliku konfiguracyjnego.

W klasie, w której chcesz skonfigurować i utworzyć wystąpienie aplikacji, zadeklaruj `ConfidentialClientApplicationOptions` obiekt.  Powiąż konfigurację odczytaną ze źródła (w tym appconfig.jsw pliku) z wystąpieniem opcji aplikacji przy użyciu `IConfigurationRoot.Bind()` metody z [Microsoft.Extensions.Configwersja. Pakiet NuGet spinacza](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Binder):

```csharp
using Microsoft.Identity.Client;

private ConfidentialClientApplicationOptions _applicationOptions;
_applicationOptions = new ConfidentialClientApplicationOptions();
configuration.Bind("AzureAD", _applicationOptions);
```

Dzięki temu zawartość sekcji "AzureAD" *appsettings.jsw* pliku będzie powiązana z odpowiednimi właściwościami `ConfidentialClientApplicationOptions` obiektu.  Następnie Skompiluj `ConfidentialClientApplication` obiekt:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
        .Build();
```

## <a name="add-runtime-configuration"></a>Dodaj konfigurację środowiska uruchomieniowego
W poufnej aplikacji klienckiej zwykle masz pamięć podręczną na użytkownika. W związku z tym konieczne będzie uzyskanie pamięci podręcznej skojarzonej z użytkownikiem i informowanie konstruktora aplikacji o tym, który ma być używany. W ten sam sposób może istnieć dynamicznie obliczany identyfikator URI przekierowania. W takim przypadku kod jest następujący:

```csharp
IConfidentialClientApplication app;
var request = httpContext.Request;
var currentUri = UriHelper.BuildAbsolute(request.Scheme, request.Host, request.PathBase, _azureAdOptions.CallbackPath ?? string.Empty);
app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(_applicationOptions)
       .WithRedirectUri(currentUri)
       .Build();
TokenCache userTokenCache = _tokenCacheProvider.SerializeCache(app.UserTokenCache,httpContext, claimsPrincipal);
```

