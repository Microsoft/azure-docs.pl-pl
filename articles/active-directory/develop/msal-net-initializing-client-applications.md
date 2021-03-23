---
title: Inicjowanie aplikacji klienckich MSAL.NET | Azure
titleSuffix: Microsoft identity platform
description: Informacje na temat inicjowania publicznego klienta i poufnych aplikacji klienckich przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/18/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 7ff61811e8b736f8f6d104a253cfe5dc5e76c428
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771366"
---
# <a name="initialize-client-applications-using-msalnet"></a>Inicjuj aplikacje klienckie przy użyciu MSAL.NET
W tym artykule opisano sposób inicjowania publicznego klienta i poufnych aplikacji klienckich przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).  Aby dowiedzieć się więcej na temat typów aplikacji klienckich, zobacz [klienta publicznego i poufne aplikacje klienckie](msal-client-applications.md).

W przypadku MSAL.NET 3. x zalecanym sposobem tworzenia wystąpienia aplikacji jest użycie konstruktorów aplikacji: `PublicClientApplicationBuilder` i `ConfidentialClientApplicationBuilder` . Oferują one zaawansowany mechanizm konfigurowania aplikacji zarówno z kodu, jak i pliku konfiguracji, a nawet przez mieszanie obu metod.

[Dokumentacja](/dotnet/api/microsoft.identity.client)  |  interfejsu API [Pakiet w pakiecie NuGet](https://www.nuget.org/packages/Microsoft.Identity.Client/)  |  [Kod](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)  |  źródłowy biblioteki [Przykłady kodu](sample-v2-code.md)

## <a name="prerequisites"></a>Wymagania wstępne
Przed zainicjowaniem aplikacji należy najpierw [ją zarejestrować](quickstart-register-app.md) , aby można było zintegrować aplikację z platformą tożsamości firmy Microsoft.  Po zarejestrowaniu mogą być potrzebne następujące informacje (które można znaleźć w Azure Portal):

- Identyfikator klienta (ciąg reprezentujący identyfikator GUID)
- Adres URL dostawcy tożsamości (nazywany wystąpieniem) i odbiorcy logowania dla aplikacji. Te dwa parametry są określane zbiorczo jako urząd.
- Identyfikator dzierżawy, jeśli piszesz aplikację biznesową wyłącznie dla Twojej organizacji (nazywaną również aplikacją z jedną dzierżawą).
- Wpis tajny aplikacji (ciąg tajny klienta) lub certyfikat (typu X509Certificate2), jeśli jest to poufna aplikacja kliencka.
- W przypadku aplikacji sieci Web, a czasami dla publicznych aplikacji klienckich (w szczególności gdy aplikacja wymaga użycia brokera), należy również ustawić redirectUri, w którym dostawca tożsamości będzie kontaktować się z aplikacją przy użyciu tokenów zabezpieczających.

## <a name="ways-to-initialize-applications"></a>Sposoby inicjowania aplikacji
Istnieje wiele różnych sposobów tworzenia wystąpienia aplikacji klienckich.

### <a name="initializing-a-public-client-application-from-code"></a>Inicjowanie publicznej aplikacji klienckiej przy użyciu kodu

Poniższy kod tworzy wystąpienie publicznej aplikacji klienckiej, logowania użytkowników w Microsoft Azure chmurze publicznej, z kontami służbowymi lub osobistymi kontami Microsoft.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>Inicjowanie poufnej aplikacji klienckiej z kodu

W ten sam sposób Poniższy kod tworzy wystąpienie aplikacji poufnej (aplikacji sieci Web znajdującej się w lokalizacji `https://myapp.azurewebsites.net` ) obsługującej tokeny użytkowników w Microsoft Azure chmurze publicznej, z kontami służbowymi lub osobistymi kontami Microsoft. Aplikacja jest identyfikowana przy użyciu dostawcy tożsamości, udostępniając klucz tajny klienta:

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

Ponieważ w środowisku produkcyjnym zamiast korzystania z klucza tajnego klienta możesz chcieć udostępnić certyfikat w usłudze Azure AD. Następnie można wykonać następujące czynności:

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>Inicjowanie publicznej aplikacji klienckiej z poziomu opcji konfiguracji

Poniższy kod tworzy wystąpienie publicznej aplikacji klienckiej z obiektu konfiguracji, który może być wypełniany programowo lub odczytywany z pliku konfiguracji:

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>Inicjowanie poufnej aplikacji klienckiej z poziomu opcji konfiguracji

Ten sam rodzaj wzorca ma zastosowanie do poufnych aplikacji klienckich. Możesz również dodać inne parametry za pomocą `.WithXXX` modyfikatorów (w tym przypadku certyfikatu).

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>Modyfikatory konstruktora

W fragmentach kodu przy użyciu konstruktorów aplikacji, `.With` można zastosować szereg metod jako modyfikatory (na przykład `.WithCertificate` i `.WithRedirectUri` ). 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>Modyfikatory wspólne dla publicznych i poufnych aplikacji klienckich

Modyfikatory, które można ustawić dla klienta publicznego lub poufnego konstruktora aplikacji klienta, to:

|Modyfikator | Opis|
|--------- | --------- |
|`.WithAuthority()` 7 zastąpień | Ustawia domyślny urząd certyfikacji usługi Azure AD, z możliwością wyboru chmury platformy Azure, odbiorców, dzierżawy (identyfikatora dzierżawy lub nazwy domeny) lub bezpośredniego przekazywania identyfikatora URI urzędu certyfikacji.|
|`.WithAdfsAuthority(string)` | Ustawia domyślny urząd aplikacji jako urząd usług ADFS.|
|`.WithB2CAuthority(string)` | Ustawia domyślny urząd aplikacji jako urząd Azure AD B2C.|
|`.WithClientId(string)` | Zastępuje identyfikator klienta.|
|`.WithComponent(string)` | Ustawia nazwę biblioteki przy użyciu MSAL.NET (ze względów telemetrii). |
|`.WithDebugLoggingCallback()` | Jeśli zostanie wywołana, aplikacja będzie wywoływała `Debug.Write` po prostu włączenie śladów debugowania. Zobacz [Rejestrowanie](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) , aby uzyskać więcej informacji.|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | Ustaw dodatkowe parametry zapytania na poziomie aplikacji, które będą wysyłane we wszystkich żądaniach uwierzytelniania. Jest to możliwe do zastąpienia na każdym poziomie metody pozyskiwania tokenu (z tymi samymi `.WithExtraQueryParameters pattern` ).|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | Umożliwia zaawansowane scenariusze, takie jak Konfigurowanie dla serwera proxy HTTP, lub wymuszenie użycia określonego HttpClient (na przykład w ASP.NET Core aplikacje sieci Web/interfejsy API).|
|`.WithLogging()` | Jeśli zostanie wywołana, aplikacja wywoła wywołanie zwrotne za pomocą śladów debugowania. Zobacz [Rejestrowanie](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) , aby uzyskać więcej informacji.|
|`.WithRedirectUri(string redirectUri)` | Zastępuje domyślny identyfikator URI przekierowania. W przypadku publicznych aplikacji klienckich jest to przydatne w scenariuszach obejmujących brokera.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | Ustawia delegata używanego do wysyłania telemetrii.|
|`.WithTenantId(string tenantId)` | Zastępuje identyfikator dzierżawy lub opis dzierżawy.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Modyfikatory specyficzne dla aplikacji platformy Xamarin. iOS

Modyfikatory, które można ustawić w publicznym konstruktorze aplikacji klienta na platformie Xamarin. iOS, to:

|Modyfikator | Opis|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Tylko Xamarin. iOS**: ustawia grupę zabezpieczeń łańcuch kluczy systemu iOS (dla trwałości pamięci podręcznej).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>Modyfikatory specyficzne dla poufnych aplikacji klienckich

Modyfikatory, które można ustawić dla poufnego konstruktora aplikacji klienta, to:

|Modyfikator | Opis|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Ustawia certyfikat identyfikujący aplikację w usłudze Azure AD.|
|`.WithClientSecret(string clientSecret)` | Ustawia klucz tajny klienta (hasło aplikacji) identyfikujący aplikację w usłudze Azure AD.|

Te Modyfikatory wykluczają się wzajemnie. W przypadku podania obydwu MSAL zgłosi znaczący wyjątek.

### <a name="example-of-usage-of-modifiers"></a>Przykład użycia modyfikatorów

Załóżmy, że Twoja aplikacja jest aplikacją biznesową, która jest tylko dla Twojej organizacji.  Następnie możesz napisać:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

W takim przypadku Programowanie dla chmur narodowych jest teraz uproszczone. Jeśli aplikacja ma być aplikacją wielodostępną w chmurze krajowej, można napisać na przykład:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

Istnieje również przesłonięcie usług AD FS (ADFS 2019 nie jest obecnie obsługiwane):
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Jeśli jesteś deweloperem Azure AD B2C, możesz określić swoją dzierżawę w następujący sposób:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

## <a name="next-steps"></a>Następne kroki

Po zainicjowaniu aplikacji klienckiej następnym zadaniem jest dodanie obsługi logowania użytkownika, autoryzowania dostępu do interfejsu API lub obu tych funkcji.

Nasza dokumentacja scenariusza aplikacji zawiera wskazówki dotyczące podpisywania użytkownika i uzyskiwania tokenu dostępu w celu uzyskania dostępu do interfejsu API w imieniu tego użytkownika:

- [Aplikacja internetowa, która loguje użytkowników: Logowanie i wylogowywanie](scenario-web-app-sign-user-sign-in.md)
- [Aplikacja sieci Web, która wywołuje interfejsy API sieci Web: uzyskiwanie tokenu](scenario-web-app-call-api-acquire-token.md)
