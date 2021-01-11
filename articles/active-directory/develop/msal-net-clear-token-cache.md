---
title: Wyczyść pamięć podręczną tokenów (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak wyczyścić pamięć podręczną tokenów przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 05/07/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 3049a1213b8b92153fc0fce96b2dadace01a4ca8
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98064763"
---
# <a name="clear-the-token-cache-using-msalnet"></a>Wyczyść pamięć podręczną tokenów przy użyciu MSAL.NET

Gdy [uzyskujesz token dostępu](msal-acquire-cache-tokens.md) przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .net (MSAL.NET), token jest buforowany. Gdy aplikacja wymaga tokenu, powinna najpierw wywołać `AcquireTokenSilent` metodę, aby sprawdzić, czy akceptowalny token znajduje się w pamięci podręcznej. 

Czyszczenie pamięci podręcznej jest realizowane przez usunięcie kont z pamięci podręcznej. Nie spowoduje to usunięcia pliku cookie sesji, który znajduje się w przeglądarce.  Poniższy przykład tworzy wystąpienie publicznej aplikacji klienckiej, pobiera konta aplikacji i usuwa konta.

```csharp
private readonly IPublicClientApplication _app;
private static readonly string ClientId = ConfigurationManager.AppSettings["ida:ClientId"];
private static readonly string Authority = string.Format(CultureInfo.InvariantCulture, AadInstance, Tenant);

_app = PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(Authority)
                .Build();

var accounts = (await _app.GetAccountsAsync()).ToList();

// clear the cache
while (accounts.Any())
{
   await _app.RemoveAsync(accounts.First());
   accounts = (await _app.GetAccountsAsync()).ToList();
}

```

Aby dowiedzieć się więcej na temat uzyskiwania i buforowania tokenów, przeczytaj artykuł [pozyskiwanie tokenu dostępu](msal-acquire-cache-tokens.md).
