---
title: Pozyskiwanie tokenu z pamięci podręcznej (MSAL.NET)
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak uzyskać token dostępu dyskretnie (z pamięci podręcznej tokenów) przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 94e4a4d5b80e246ce822e977deafe5c41c9ff4ad
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98064780"
---
# <a name="get-a-token-from-the-token-cache-using-msalnet"></a>Pobieranie tokenu z pamięci podręcznej tokenów przy użyciu MSAL.NET

Gdy uzyskujesz token dostępu przy użyciu biblioteki uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET), token jest buforowany. Gdy aplikacja wymaga tokenu, powinna najpierw wywołać `AcquireTokenSilent` metodę, aby sprawdzić, czy akceptowalny token znajduje się w pamięci podręcznej. W wielu przypadkach możliwe jest uzyskanie innego tokenu z większą liczbą zakresów na podstawie tokenu w pamięci podręcznej. Istnieje również możliwość odświeżenia tokenu, gdy zbliża się do wygaśnięcia (ponieważ pamięć podręczna tokenów zawiera również token odświeżania).

Zalecany wzorzec polega na wywołaniu metody jako `AcquireTokenSilent` pierwszej.  Jeśli `AcquireTokenSilent` to się nie powiedzie, uzyskaj token przy użyciu innych metod.

W poniższym przykładzie aplikacja najpierw próbuje uzyskać token z pamięci podręcznej tokenów.  Jeśli `MsalUiRequiredException` wystąpi wyjątek, aplikacja uzyskuje token interaktywnie. 

```csharp
AuthenticationResult result = null;
var accounts = await app.GetAccountsAsync();

try
{
 result = await app.AcquireTokenSilent(scopes, accounts.FirstOrDefault())
        .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
 // A MsalUiRequiredException happened on AcquireTokenSilent.
 // This indicates you need to call AcquireTokenInteractive to acquire a token
 System.Diagnostics.Debug.WriteLine($"MsalUiRequiredException: {ex.Message}");

 try
 {
    result = await app.AcquireTokenInteractive(scopes)
          .ExecuteAsync();
 }
 catch (MsalException msalex)
 {
    ResultText.Text = $"Error Acquiring Token:{System.Environment.NewLine}{msalex}";
 }
}
catch (Exception ex)
{
 ResultText.Text = $"Error Acquiring Token Silently:{System.Environment.NewLine}{ex}";
 return;
}

if (result != null)
{
 string accessToken = result.AccessToken;
 // Use the token
}
```
