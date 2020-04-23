---
title: Konfigurowanie aplikacji klasycznych, które wywołują internetowe interfejsy API — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak skonfigurować kod aplikacji klasycznej, która wywołuje internetowe interfejsy API
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e0b43f7563c9dfac6374590f6b081197536fe31e
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869010"
---
# <a name="desktop-app-that-calls-web-apis-code-configuration"></a>Aplikacja klasyczna, która wywołuje internetowe interfejsy API: konfiguracja kodu

Po utworzeniu aplikacji dowiesz się, jak skonfigurować kod ze współrzędnymi aplikacji.

## <a name="microsoft-authentication-libraries"></a>Biblioteki uwierzytelniania firmy Microsoft

Następujące biblioteki uwierzytelniania firmy Microsoft (MSALs) obsługują aplikacje klasyczne.

  Biblioteka uwierzytelniania firmy Microsoft | Opis
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Obsługuje tworzenie aplikacji klasycznej na wielu platformach, takich jak Linux, Windows i macOS.
  ![Python](media/sample-v2-code/logo_python.png) <br/> MĘTÓW MSAL | Obsługuje tworzenie aplikacji klasycznej na wielu platformach.
  ![Java](media/sample-v2-code/logo_java.png) <br/> MSAL Java | Obsługuje tworzenie aplikacji klasycznej na wielu platformach.
  ![MSAL iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL iOS | Obsługuje aplikacje klasyczne, które działają tylko w systemie macOS.

## <a name="public-client-application"></a>Publiczna aplikacja klienta

Z punktu widzenia kodu aplikacje klasyczne są publicznymi aplikacjami klienckimi. Konfiguracja będzie się nieco różnić w zależności od tego, czy używasz uwierzytelniania interaktywnego, czy nie.

# <a name="net"></a>[.NET](#tab/dotnet)

Musisz budować i manipulować MSAL.NET `IPublicClientApplication`.

![IPublicClientApplication (Zastosowanie iPublicClientApplication)](media/scenarios/public-client-application.png)

### <a name="exclusively-by-code"></a>Wyłącznie przez kod

Poniższy kod zawiera wystąpienia publicznej aplikacji klienckiej i loguje się do użytkowników w chmurze publicznej platformy Microsoft Azure za pomocą konta służbowego lub szkolnego lub osobistego konta Microsoft.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

Jeśli zamierzasz użyć uwierzytelniania interaktywnego lub przepływu kodu `.WithRedirectUri` urządzenia, jak widać wcześniej, użyj modyfikatora.

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="use-configuration-files"></a>Korzystanie z plików konfiguracji

Poniższy kod wystąpienia publicznej aplikacji klienckiej z obiektu konfiguracji, który może być wypełniona programowo lub odczytu z pliku konfiguracji.

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
        .WithDefaultRedirectUri()
        .Build();
```

### <a name="more-elaborated-configuration"></a>Bardziej rozbudowana konfiguracja

Tworzenie aplikacji można opracować, dodając szereg modyfikatorów. Na przykład, jeśli chcesz, aby aplikacja była aplikacją wielodostępną w chmurze krajowej, na przykład rząd STANÓW Zjednoczonych pokazany w tym miejscu, możesz napisać:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithDefaultRedirectUri()
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment,
                         AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

MSAL.NET zawiera również modyfikator usług federacyjnych Active Directory 2019:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Na koniec, jeśli chcesz uzyskać tokeny dla dzierżawy B2C usługi Azure Active Directory (Azure AD), określ dzierżawę, jak pokazano w poniższym fragmentie kodu:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

### <a name="learn-more"></a>Dowiedz się więcej

Aby dowiedzieć się więcej o konfigurowaniu aplikacji MSAL.NET klasycznej:

- Aby uzyskać listę wszystkich modyfikatorów dostępnych w `PublicClientApplicationBuilder`programie , zobacz dokumentację referencyjną [PublicClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods).
- Aby uzyskać opis wszystkich opcji `PublicClientApplicationOptions`dostępnych w obszarze , zobacz [PublicClientApplicationOptions](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions) w dokumentacji referencyjnej.

### <a name="complete-example-with-configuration-options"></a>Kompletny przykład z opcjami konfiguracji

Wyobraź sobie aplikację konsoli .NET `appsettings.json` Core, która ma następujący plik konfiguracyjny:

```json
{
  "Authentication": {
    "AzureCloudInstance": "AzurePublic",
    "AadAuthorityAudience": "AzureAdMultipleOrgs",
    "ClientId": "ebe2ab4d-12b3-4446-8480-5c3828d04c50"
  },

  "WebAPI": {
    "MicrosoftGraphBaseEndpoint": "https://graph.microsoft.com"
  }
}
```

Masz mały kod do odczytania w tym pliku za pomocą . Struktura konfiguracji dostarczona przez NET:

```csharp
public class SampleConfiguration
{
 /// <summary>
 /// Authentication options
 /// </summary>
 public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

 /// <summary>
 /// Base URL for Microsoft Graph (it varies depending on whether the application runs
 /// in Microsoft Azure public clouds or national or sovereign clouds)
 /// </summary>
 public string MicrosoftGraphBaseEndpoint { get; set; }

 /// <summary>
 /// Reads the configuration from a JSON file
 /// </summary>
 /// <param name="path">Path to the configuration json file</param>
 /// <returns>SampleConfiguration as read from the json file</returns>
 public static SampleConfiguration ReadFromJsonFile(string path)
 {
  // .NET configuration
  IConfigurationRoot Configuration;
  var builder = new ConfigurationBuilder()
                    .SetBasePath(Directory.GetCurrentDirectory())
                    .AddJsonFile(path);
  Configuration = builder.Build();

  // Read the auth and graph endpoint configuration
  SampleConfiguration config = new SampleConfiguration()
  {
   PublicClientApplicationOptions = new PublicClientApplicationOptions()
  };
  Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
  config.MicrosoftGraphBaseEndpoint =
  Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
  return config;
 }
}
```

Teraz, aby utworzyć aplikację, napisz następujący kod:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .WithDefaultRedirectUri()
           .Build();
```

Przed wywołaniem `.Build()` metody można zastąpić konfigurację za pomocą `.WithXXX` wywołań metod, jak widać wcześniej.

# <a name="java"></a>[Java](#tab/java)

Oto klasa używana w przykładach rozwoju msal Java do konfigurowania przykładów: [TestData](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/public-client/TestData.java).

```Java
PublicClientApplication pca = PublicClientApplication.builder(CLIENT_ID)
        .authority(AUTHORITY)
        .build();
```

# <a name="python"></a>[Python](#tab/python)

```Python
config = json.load(open(sys.argv[1]))

app = msal.PublicClientApplication(
    config["client_id"], authority=config["authority"],
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.rtfd.io/en/latest/#msal.SerializableTokenCache
    )
```

# <a name="macos"></a>[Macos](#tab/macOS)

Poniższy kod zawiera wystąpienia publicznej aplikacji klienckiej i loguje się do użytkowników w chmurze publicznej platformy Microsoft Azure za pomocą konta służbowego lub szkolnego lub osobistego konta Microsoft.

### <a name="quick-configuration"></a>Szybka konfiguracja

Cel C:

```objc
NSError *msalError = nil;

MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift:
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

### <a name="more-elaborated-configuration"></a>Bardziej rozbudowana konfiguracja

Tworzenie aplikacji można opracować, dodając szereg modyfikatorów. Na przykład, jeśli chcesz, aby aplikacja była aplikacją wielodostępną w chmurze krajowej, na przykład rząd STANÓW Zjednoczonych pokazany w tym miejscu, możesz napisać:

Cel C:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];

MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];

NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

Swift:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)

let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```
---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu dla aplikacji klasycznej](scenario-desktop-acquire-token.md)
