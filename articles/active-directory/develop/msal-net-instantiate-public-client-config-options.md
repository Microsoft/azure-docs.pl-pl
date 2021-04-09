---
title: Tworzenie wystąpienia publicznej aplikacji klienckiej (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć wystąpienie publicznej aplikacji klienckiej z opcjami konfiguracji przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
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
ms.openlocfilehash: 3e2ffebf0b414d4b59178fe04fb109530365786b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98064712"
---
# <a name="instantiate-a-public-client-application-with-configuration-options-using-msalnet"></a>Tworzenie wystąpienia publicznej aplikacji klienckiej z opcjami konfiguracji przy użyciu MSAL.NET

W tym artykule opisano sposób tworzenia wystąpienia [publicznej aplikacji klienckiej](msal-client-applications.md) przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .net (MSAL.NET).  Aplikacja tworzy wystąpienie z opcjami konfiguracji zdefiniowanymi w pliku ustawień.

Przed zainicjowaniem aplikacji należy najpierw ją [zarejestrować](quickstart-register-app.md) , aby można było zintegrować aplikację z platformą tożsamości firmy Microsoft. Po zarejestrowaniu mogą być potrzebne następujące informacje (które można znaleźć w Azure Portal):

- Identyfikator klienta (ciąg reprezentujący identyfikator GUID)
- Adres URL dostawcy tożsamości (nazywany wystąpieniem) i odbiorcy logowania dla aplikacji. Te dwa parametry są określane zbiorczo jako urząd.
- Identyfikator dzierżawy, jeśli piszesz aplikację biznesową wyłącznie dla Twojej organizacji (nazywaną również aplikacją z jedną dzierżawą).
- W przypadku aplikacji sieci Web, a czasami dla publicznych aplikacji klienckich (w szczególności gdy aplikacja wymaga użycia brokera), należy również ustawić redirectUri, w którym dostawca tożsamości będzie kontaktować się z aplikacją przy użyciu tokenów zabezpieczających.


Aplikacja konsolowa platformy .NET Core może mieć następujące *appsettings.jsw* pliku konfiguracji:

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

Poniższy kod odczytuje ten plik za pomocą platformy .NET Configuration Framework:

```csharp
public class SampleConfiguration
{
    /// <summary>
    /// Authentication options
    /// </summary>
    public PublicClientApplicationOptions PublicClientApplicationOptions { get; set; }

    /// <summary>
    /// Base URL for Microsoft Graph (it varies depending on whether the application is ran
    /// in Microsoft Azure public clouds or national / sovereign clouds
    /// </summary>
    public string MicrosoftGraphBaseEndpoint { get; set; }

    /// <summary>
    /// Reads the configuration from a json file
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

        // Read the auth and graph endpoint config
        SampleConfiguration config = new SampleConfiguration()
        {
            PublicClientApplicationOptions = new PublicClientApplicationOptions()
        };
        Configuration.Bind("Authentication", config.PublicClientApplicationOptions);
        config.MicrosoftGraphBaseEndpoint = Configuration.GetValue<string>("WebAPI:MicrosoftGraphBaseEndpoint");
        return config;
    }
}
```

Poniższy kod tworzy aplikację przy użyciu konfiguracji z pliku ustawień:

```csharp
SampleConfiguration config = SampleConfiguration.ReadFromJsonFile("appsettings.json");
var app = PublicClientApplicationBuilder.CreateWithApplicationOptions(config.PublicClientApplicationOptions)
           .Build();
```

