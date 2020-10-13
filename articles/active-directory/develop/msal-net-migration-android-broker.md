---
title: Migrowanie aplikacji platformy Xamarin Android przy użyciu brokerów do MSAL.NET
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak przeprowadzić migrację aplikacji platformy Xamarin dla systemu Android, które używają Microsoft Authenticator lub Intune — Portal firmy z ADAL.NET do MSAL.NET.
author: aiwang
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/31/2020
ms.author: aiwang
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 47902b29da1dfe20fb54d633a0559e6a337fd771
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89183635"
---
# <a name="migrate-android-applications-that-use-a-broker-from-adalnet-to-msalnet"></a>Migrowanie aplikacji systemu Android, które używają brokera z ADAL.NET do MSAL.NET

Jeśli masz obecnie aplikację platformy Xamarin dla systemu Android korzystającą Azure Active Directory z biblioteki ADAL.NET Authentication Library for .NET (MSAL.NET) i [brokera uwierzytelniania](brokered-auth.md), przeprowadzisz migrację do [biblioteki uwierzytelniania firmy Microsoft dla platformy .NET ](msal-overview.md) .

## <a name="prerequisites"></a>Wymagania wstępne

* Aplikacja platformy Xamarin dla systemu Android jest już zintegrowana z brokerem ([Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) lub [Intune — Portal firmy](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)) i ADAL.NET, które należy zmigrować do MSAL.NET.

## <a name="step-1-enable-the-broker"></a>Krok 1. Włączanie brokera

<table>
<tr><td>Bieżący kod biblioteki ADAL:</td><td>MSAL odpowiedni:</td></tr>
<tr><td>
W programie ADAL.NET obsługa brokera jest włączana na podstawie kontekstu uwierzytelniania.

Aby wywołać brokera, należy ustawić `useBroker` *wartość true* w `PlatformParameters` konstruktorze:

```CSharp
public PlatformParameters(
        Activity callerActivity,
        bool useBroker)
```

W kodzie modułu renderowania strony specyficznym dla platformy dla systemu Android Ustaw `useBroker` flagę na wartość true:

```CSharp
page.BrokerParameters = new PlatformParameters(
        this,
        true,
        PromptBehavior.SelectAccount);
```

Następnie Uwzględnij parametry w wywołaniu tokenu pozyskiwania:

```CSharp
AuthenticationResult result =
        await
            AuthContext.AcquireTokenAsync(
                Resource,
                ClientId,
                new Uri(RedirectURI),
                platformParameters)
                .ConfigureAwait(false);
```

</td><td>
W programie MSAL.NET obsługa brokera jest włączana dla poszczególnych PublicClientApplication.

Użyj `WithBroker()` parametru (domyślnie ustawionego na true) do brokera wywołań:

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid)
                .Build();
```

Następnie w wywołaniu AcquireToken:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="step-2-set-an-activity"></a>Krok 2. Ustawianie działania

W programie ADAL.NET przekazano działanie (zazwyczaj główne) jako część PlatformParameters, jak pokazano w [kroku 1: Włączanie brokera](#step-1-enable-the-broker).

MSAL.NET używa również działania, ale nie jest to wymagane w zwykłych użycia systemu Android bez użycia brokera. Aby użyć brokera, ustaw działanie na wysyłanie i odbieranie odpowiedzi z brokera.

<table>
<tr><td>Bieżący kod biblioteki ADAL:</td><td>MSAL odpowiedni:</td></tr>
<tr><td>
Działanie jest przesyłane do PlatformParameters na platformie dla systemu Android.

```CSharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```
</td><td>

W MSAL.NET, wykonaj dwie czynności, aby ustawić działanie dla systemu Android:

1. W programie ustaw wartość na na `MainActivity.cs` `App.RootViewController`  , `MainActivity` Aby upewnić się, że jest to działanie z wywołaniem do brokera.

    Jeśli ta wartość nie jest ustawiona poprawnie, może wystąpić następujący błąd: `"Activity_required_for_android_broker":"Activity is null, so MSAL.NET cannot invoke the Android broker. See https://aka.ms/Brokered-Authentication-for-Android"`

1. W wywołaniu AcquireTokenInteractive Użyj `.WithParentActivityOrWindow(App.RootViewController)` i przekaż odwołanie do działania, które będzie używane. W tym przykładzie zostanie użyte główne.

**Na przykład:**

W *App.cs*:

```CSharp
   public static object RootViewController { get; set; }
```

W *MainActivity.cs*:

```CSharp
   LoadApplication(new App());
   App.RootViewController = this;
```

W wywołaniu AcquireToken:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o zagadnieniach specyficznych dla systemu Android podczas korzystania z programu MSAL.NET z platformą Xamarin, zobacz [wymagania dotyczące konfiguracji i wskazówki dotyczące rozwiązywania problemów dla platformy Xamarin Android z](msal-net-xamarin-android-considerations.md)