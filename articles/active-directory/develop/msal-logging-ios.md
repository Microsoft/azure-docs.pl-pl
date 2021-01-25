---
title: Rejestrowanie błędów i wyjątków w MSAL dla systemu iOS/macOS
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak rejestrować błędy i wyjątki w MSAL dla systemu iOS/macOS
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
ms.openlocfilehash: ee3837b75d586238e7ca6ac85434cc56f592929d
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763490"
---
# <a name="logging-in-msal-for-iosmacos"></a>Rejestrowanie w bibliotece MSAL dla systemu iOS/macOS

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="objective-c"></a>[Objective-C](#tab/objc)

## <a name="msal-for-ios-and-macos-logging-objc"></a>MSAL dla systemu iOS i rejestrowania macOS — ObjC

Ustaw wywołanie zwrotne, aby przechwycić MSAL rejestrowanie i dołączyć je do rejestrowania własnych aplikacji. Podpis dla wywołania zwrotnego wygląda następująco:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Na przykład:

```objc
[MSALGlobalConfig.loggerConfig setLogCallback:^(MSALLogLevel level, NSString *message, BOOL containsPII)
    {
        if (!containsPII)
        {
#if DEBUG
            // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
            NSLog(@"MSAL log: %@", message);
#endif
        }
    }];
```

### <a name="personal-data"></a>Dane osobowe

Domyślnie MSAL nie przechwytują ani nie rejestruje żadnych danych osobowych. Biblioteka umożliwia deweloperom aplikacji włączenie tej właściwości w klasie MSALLogger. Po włączeniu `pii.Enabled` aplikacja jest odpowiedzialna za bezpieczne obsługiwanie bardzo poufnych danych i spełnianie wymagań prawnych.

```objc
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = YES;

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = NO;
```

### <a name="logging-levels"></a>Poziomy rejestrowania

Aby ustawić poziom rejestrowania podczas rejestrowania przy użyciu MSAL dla systemów iOS i macOS, użyj jednej z następujących wartości:

|Poziom  |Opis |
|---------|---------|
| `MSALLogLevelNothing`| Wyłącz wszystkie rejestrowanie |
| `MSALLogLevelError` | Poziom domyślny, drukuje informacje tylko w przypadku wystąpienia błędów |
| `MSALLogLevelWarning` | Ostrzeżenia |
| `MSALLogLevelInfo` |  Punkty wejścia biblioteki, z parametrami i różnymi operacjami łańcucha kluczy |
|`MSALLogLevelVerbose`     |  Śledzenie interfejsu API |

Na przykład:

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>Format komunikatu dziennika

Część komunikatów dziennika MSAL jest w formacie `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Na przykład:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Przekazywanie identyfikatorów korelacji i sygnatur czasowych jest przydatne w przypadku śledzenia problemów. W komunikacie dziennika są dostępne informacje o sygnaturze czasowej i IDENTYFIKATORze korelacji. Jedyną niezawodnym miejscem, w którym można je pobrać, jest MSAL komunikaty rejestrowania.

## <a name="swift"></a>[Swift](#tab/swift)

## <a name="msal-for-ios-and-macos-logging-swift"></a>MSAL dla systemu iOS i rejestrowania macOS — SWIFT

Ustaw wywołanie zwrotne, aby przechwycić MSAL rejestrowanie i dołączyć je do rejestrowania własnych aplikacji. Sygnatura (reprezentowana w zamierzeniu-C) dla wywołania zwrotnego wygląda następująco:

```objc
/*!
    The LogCallback block for the MSAL logger
 
    @param  level           The level of the log message
    @param  message         The message being logged
    @param  containsPII     If the message might contain Personally Identifiable Information (PII)
                            this will be true. Log messages possibly containing PII will not be
                            sent to the callback unless PIllLoggingEnabled is set to YES on the
                            logger.

 */
typedef void (^MSALLogCallback)(MSALLogLevel level, NSString *message, BOOL containsPII);
```

Na przykład:

```swift
MSALGlobalConfig.loggerConfig.setLogCallback { (level, message, containsPII) in
    if let message = message, !containsPII
    {
#if DEBUG
    // IMPORTANT: MSAL logs may contain sensitive information. Never output MSAL logs with NSLog, or print, directly unless you're running your application in debug mode. If you're writing MSAL logs to file, you must store the file securely.
    print("MSAL log: \(message)")
#endif
    }
}
```

### <a name="personal-data"></a>Dane osobowe

Domyślnie MSAL nie przechwytują ani nie rejestruje żadnych danych osobowych. Biblioteka umożliwia deweloperom aplikacji włączenie tej właściwości w klasie MSALLogger. Po włączeniu `pii.Enabled` aplikacja jest odpowiedzialna za bezpieczne obsługiwanie bardzo poufnych danych i spełnianie wymagań prawnych.

```swift
// By default, the `MSALLogger` doesn't capture any PII

// PII will be logged
MSALGlobalConfig.loggerConfig.piiEnabled = true

// PII will NOT be logged
MSALGlobalConfig.loggerConfig.piiEnabled = false
```

### <a name="logging-levels"></a>Poziomy rejestrowania

Aby ustawić poziom rejestrowania podczas rejestrowania przy użyciu MSAL dla systemów iOS i macOS, użyj jednej z następujących wartości:

|Poziom  |Opis |
|---------|---------|
| `MSALLogLevelNothing`| Wyłącz wszystkie rejestrowanie |
| `MSALLogLevelError` | Poziom domyślny, drukuje informacje tylko w przypadku wystąpienia błędów |
| `MSALLogLevelWarning` | Ostrzeżenia |
| `MSALLogLevelInfo` |  Punkty wejścia biblioteki, z parametrami i różnymi operacjami łańcucha kluczy |
|`MSALLogLevelVerbose`     |  Śledzenie interfejsu API |

Na przykład:

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Format komunikatu dziennika

Część komunikatów dziennika MSAL jest w formacie `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Na przykład:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Przekazywanie identyfikatorów korelacji i sygnatur czasowych jest przydatne w przypadku śledzenia problemów. W komunikacie dziennika są dostępne informacje o sygnaturze czasowej i IDENTYFIKATORze korelacji. Jedyną niezawodnym miejscem, w którym można je pobrać, jest MSAL komunikaty rejestrowania.

---

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej przykładów kodu, zapoznaj się z przykładami [kodu platformy tożsamości firmy Microsoft](sample-v2-code.md).