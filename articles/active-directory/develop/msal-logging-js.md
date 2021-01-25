---
title: Rejestrowanie błędów i wyjątków w MSAL.js
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak rejestrować błędy i wyjątki w MSAL.js
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
ms.openlocfilehash: 8604a38bc310cc884c2b5e99efe7a47ae5e787d7
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763495"
---
# <a name="logging-in-msaljs"></a>Rejestrowanie w bibliotece MSAL.js

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="configure-logging-in-msaljs"></a>Konfigurowanie rejestrowania w MSAL.js

Włącz rejestrowanie w MSAL.js (JavaScript) przez przekazanie obiektu rejestratora podczas konfiguracji w celu utworzenia `UserAgentApplication` wystąpienia. Ten obiekt rejestratora ma następujące właściwości:

- `localCallback`: wystąpienie wywołania zwrotnego, które może zostać dostarczone przez dewelopera do korzystania z dzienników i publikowania ich w niestandardowy sposób. Zaimplementuj metodę localCallback w zależności od tego, jak chcesz przekierować dzienniki.
- `level` (opcjonalnie): konfigurowalny poziom rejestrowania. Obsługiwane poziomy dzienników to: `Error` , `Warning` , `Info` i `Verbose` . Wartość domyślna to `Info`.
- `piiLoggingEnabled` (opcjonalnie): w przypadku ustawienia wartości true program rejestruje dane osobiste i organizacyjne. Domyślnie jest to wartość false, aby aplikacja nie rejestrował danych osobowych. Osobiste dzienniki danych nigdy nie są zapisywane w domyślnych danych wyjściowych, takich jak Console, Logcat lub NSLog.
- `correlationId` (opcjonalnie): unikatowy identyfikator używany do mapowania żądania z odpowiedzią na potrzeby debugowania. Wartość domyślna to RFC4122 w wersji 4 GUID (128 bitów).

```javascript
function loggerCallback(logLevel, message, containsPii) {
   console.log(message);
}

var msalConfig = {
    auth: {
        clientId: "<Enter your client id>",
    },
    system: {
        logger: new Msal.Logger(
            loggerCallback , {
                level: Msal.LogLevel.Verbose,
                piiLoggingEnabled: false,
                correlationId: '1234'
            }
        )
    }
}

var UserAgentApplication = new Msal.UserAgentApplication(msalConfig);
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej przykładów kodu, zapoznaj się z przykładami [kodu platformy tożsamości firmy Microsoft)](sample-v2-code.md).