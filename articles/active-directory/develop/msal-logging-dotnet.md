---
title: Rejestrowanie błędów i wyjątków w MSAL.NET
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak rejestrować błędy i wyjątki w MSAL.NET
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/25/2021
ms.author: marsma
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 47576265c9b1a20f801231abe2fb37a929d5a27c
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763538"
---
# <a name="logging-in-msalnet"></a>Rejestrowanie na platformie MSAL.NET

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msalnet"></a>Konfigurowanie rejestrowania w usłudze MSAL.NET

W MSAL 3. x rejestrowanie jest ustawione na aplikację przy tworzeniu aplikacji przy użyciu `.WithLogging` modyfikatora konstruktora. Ta metoda pobiera parametry opcjonalne:

- `Level` umożliwia wybór żądanego poziomu rejestrowania. Ustawienie na błędy spowoduje tylko błędy
- `PiiLoggingEnabled` umożliwia rejestrowanie danych osobistych i organizacji, jeśli ustawiono wartość true. Domyślnie to ustawienie ma wartość FAŁSZ, aby aplikacja nie rejestrował danych osobowych.
- `LogCallback` jest ustawiony na delegata, który wykonuje rejestrowanie. Jeśli `PiiLoggingEnabled` ma wartość true, ta metoda otrzyma komunikaty dwa razy: jeden z `containsPii` parametrem jest równa false, a komunikat bez danych osobowych, a drugi parametr ma wartość `containsPii` true, a komunikat może zawierać dane osobowe. W niektórych przypadkach (gdy wiadomość nie zawiera danych osobowych), komunikat będzie taki sam.
- `DefaultLoggingEnabled` Włącza rejestrowanie domyślne dla platformy. Domyślnie jest to wartość false. Jeśli ustawisz ją na wartość true, używa ona śledzenia zdarzeń w aplikacjach Desktop/platformy UWP, NSLog w systemach iOS i Logcat w systemie Android.

```csharp
class Program
 {
  private static void Log(LogLevel level, string message, bool containsPii)
  {
     if (containsPii)
     {
        Console.ForegroundColor = ConsoleColor.Red;
     }
     Console.WriteLine($"{level} {message}");
     Console.ResetColor();
  }

  static void Main(string[] args)
  {
    var scopes = new string[] { "User.Read" };

    var application = PublicClientApplicationBuilder.Create("<clientID>")
                      .WithLogging(Log, LogLevel.Info, true)
                      .Build();

    AuthenticationResult result = application.AcquireTokenInteractive(scopes)
                                             .ExecuteAsync().Result;
  }
 }
 ```

> [!TIP]
 > Zobacz [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) , aby poznać przykłady rejestrowania MSAL.NET i nie tylko.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej przykładów kodu, zapoznaj się z przykładami [kodu platformy tożsamości firmy Microsoft)](sample-v2-code.md).