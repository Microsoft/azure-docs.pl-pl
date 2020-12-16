---
title: Logowanie w aplikacjach MSAL | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o rejestrowaniu w aplikacjach Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/11/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: b53a12db9203121d12a69c10aaa81bceab5c1754
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97584259"
---
# <a name="logging-in-msal-applications"></a>Logowanie w aplikacjach MSAL

Aplikacje Microsoft Authentication Library (MSAL) generują komunikaty dziennika, które mogą pomóc zdiagnozować problemy. Aplikacja może skonfigurować rejestrowanie z kilkoma wierszami kodu i mieć kontrolę niestandardową na poziomie szczegółowości oraz o tym, czy dane osobiste i organizacyjne są rejestrowane. Zalecamy utworzenie wywołania zwrotnego rejestrowania MSAL i udostępnienie użytkownikom przesyłania dzienników w przypadku problemów z uwierzytelnianiem.

## <a name="logging-levels"></a>Poziomy rejestrowania

MSAL zapewnia kilka poziomów rejestrowania szczegółów:

- Błąd: wskazuje, że wystąpił problem i został wygenerowany błąd. Służy do debugowania i identyfikowania problemów.
- Ostrzeżenie: niekoniecznie Wystąpił błąd lub błąd, ale są one przeznaczone do diagnostyki i lokalizowania problemów.
- Informacja: MSAL będzie rejestrować zdarzenia przeznaczone do celów informacyjnych niekoniecznie do debugowania.
- Verbose: domyślne. MSAL rejestruje szczegółowe informacje o zachowaniu biblioteki.

## <a name="personal-and-organizational-data"></a>Dane osobowe i organizacyjne

Domyślnie Rejestrator MSAL nie przechwytuje żadnych wysoce poufnych danych osobistych lub organizacji. Biblioteka zawiera opcję włączenia rejestrowania danych osobowych i organizacji w przypadku podjęcia decyzji.

Aby uzyskać szczegółowe informacje o rejestrowaniu MSAL w określonym języku, wybierz kartę pasującą do języka:

## <a name="net"></a>[.NET](#tab/dotnet)

## <a name="logging-in-msalnet"></a>Rejestrowanie na platformie MSAL.NET

 > [!NOTE]
 > Zobacz [MSAL.NET wiki](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki) , aby poznać przykłady rejestrowania MSAL.NET i nie tylko.

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

## <a name="android"></a>[Android](#tab/android)

## <a name="logging-in-msal-for-android-using-java"></a>Logowanie w MSAL dla systemu Android przy użyciu języka Java

Włącz logowanie przy tworzeniu aplikacji przez utworzenie wywołania zwrotnego rejestrowania. Wywołanie zwrotne przyjmuje następujące parametry:

- `tag` jest ciągiem przesłanym do wywołania zwrotnego przez bibliotekę. Jest ona skojarzona z wpisem dziennika i może służyć do sortowania komunikatów rejestrowania.
- `logLevel` umożliwia wybór żądanego poziomu rejestrowania. Obsługiwane poziomy dzienników to: `Error` , `Warning` , `Info` i `Verbose` .
- `message` jest zawartością wpisu dziennika.
- `containsPII` Określa, czy komunikaty zawierające dane osobowe lub dane organizacji są rejestrowane. Domyślnie to ustawienie ma wartość FAŁSZ, aby aplikacja nie rejestrował danych osobowych. Jeśli `containsPII` jest `true` , ta metoda otrzyma komunikaty dwa razy: jeden z `containsPII` parametrem ustawionym na, `false` `message` bez danych osobowych, a drugi raz z `containsPii` parametrem ustawionym na `true` , a komunikat może zawierać dane osobowe. W niektórych przypadkach (gdy wiadomość nie zawiera danych osobowych), komunikat będzie taki sam.

```java
private StringBuilder mLogs;

mLogs = new StringBuilder();
Logger.getInstance().setExternalLogger(new ILoggerCallback()
{
   @Override
   public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII)
   {
      mLogs.append(message).append('\n');
   }
});
```

Domyślnie Rejestrator MSAL nie będzie przechwytywać informacji osobistych ani informacji o organizacji.
Aby włączyć rejestrowanie informacji osobistych lub informacji o organizacji:

```java
Logger.getInstance().setEnablePII(true);
```

Aby wyłączyć rejestrowanie danych osobowych i danych organizacji:

```java
Logger.getInstance().setEnablePII(false);
```

Domyślnie logowanie do Logcat jest wyłączone. Aby włączyć:

```java
Logger.getInstance().setEnableLogcatLog(true);
```

## <a name="javascript"></a>[JavaScript](#tab/javascript)

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

## <a name="objective-c"></a>[Obiektowy C](#tab/objc)

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

Przykład:

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

Przykład:

```objc
MSALGlobalConfig.loggerConfig.logLevel = MSALLogLevelVerbose;
 ```

 ### <a name="log-message-format"></a>Format komunikatu dziennika

Część komunikatów dziennika MSAL jest w formacie `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Przykład:

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

Przykład:

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

Przykład:

```swift
MSALGlobalConfig.loggerConfig.logLevel = .verbose
 ```

### <a name="log-message-format"></a>Format komunikatu dziennika

Część komunikatów dziennika MSAL jest w formacie `TID = <thread_id> MSAL <sdk_ver> <OS> <OS_ver> [timestamp - correlation_id] message`

Przykład:

`TID = 551563 MSAL 0.2.0 iOS Sim 12.0 [2018-09-24 00:36:38 - 36764181-EF53-4E4E-B3E5-16FE362CFC44] acquireToken returning with error: (MSALErrorDomain, -42400) User cancelled the authorization session.`

Przekazywanie identyfikatorów korelacji i sygnatur czasowych jest przydatne w przypadku śledzenia problemów. W komunikacie dziennika są dostępne informacje o sygnaturze czasowej i IDENTYFIKATORze korelacji. Jedyną niezawodnym miejscem, w którym można je pobrać, jest MSAL komunikaty rejestrowania.

## <a name="java"></a>[Java](#tab/java)

## <a name="msal-for-java-logging"></a>MSAL na potrzeby rejestrowania w języku Java

Program MSAL for Java umożliwia korzystanie z biblioteki rejestrowania, która jest już używana z aplikacją, pod warunkiem, że jest ona zgodna z SLF4J. MSAL for Java używa [prostego rejestrowania zaufana fasady for Java](http://www.slf4j.org/) (SLF4J) jako prostej elewacji lub abstrakcji dla różnych platform rejestrowania, takich jak [Java. util. Logging](https://docs.oracle.com/javase/7/docs/api/java/util/logging/package-summary.html), [Logback](http://logback.qos.ch/) i [Log4J](https://logging.apache.org/log4j/2.x/). SLF4J umożliwia użytkownikowi Podłączanie żądanej struktury rejestrowania w czasie wdrażania.

Na przykład, aby użyć Logback jako struktury rejestrowania w aplikacji, Dodaj zależność Logback do pliku Maven pliku pom dla aplikacji:

```xml
<dependency>
    <groupId>ch.qos.logback</groupId>
    <artifactId>logback-classic</artifactId>
    <version>1.2.3</version>
</dependency>
```

Następnie Dodaj plik konfiguracji Logback:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration debug="true">

</configuration>
```

SLF4J automatycznie wiąże się z Logback w czasie wdrażania. Dzienniki MSAL zostaną zapisane w konsoli programu.

Instrukcje dotyczące tworzenia powiązań z innymi platformami rejestrowania można znaleźć w [podręczniku SLF4J](http://www.slf4j.org/manual.html).

### <a name="personal-and-organization-information"></a>Informacje osobiste i organizacja

Domyślnie rejestrowanie MSAL nie przechwytuje ani nie rejestruje żadnych danych osobowych ani organizacji. W poniższym przykładzie rejestrowanie danych osobistych lub organizacji jest domyślnie wyłączone:

```java
    PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
            .authority(AUTHORITY)
            .build();
```

Włącz osobiste i organizacyjne rejestrowanie danych przez ustawienie `logPii()` w konstruktorze aplikacji klienta. Jeśli włączysz dane osobiste lub organizacyjne, Twoja aplikacja musi podjąć odpowiedzialność za bezpieczne obsługiwanie danych o wysokiej dostępności i spełniających wymagania prawne.

W poniższym przykładzie jest włączone rejestrowanie danych osobistych lub organizacji:

```java
PublicClientApplication app2 = PublicClientApplication.builder(PUBLIC_CLIENT_ID)
        .authority(AUTHORITY)
        .logPii(true)
        .build();
```

## <a name="python"></a>[Python](#tab/python)

## <a name="msal-for-python-logging"></a>MSAL na potrzeby rejestrowania w języku Python

Logowanie w MSAL Python korzysta ze standardowego mechanizmu rejestrowania języka Python, na przykład `logging.info("msg")` można skonfigurować rejestrowanie MSAL w następujący sposób (i zobaczyć w działaniu w [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)):

### <a name="enable-debug-logging-for-all-modules"></a>Włącz rejestrowanie debugowania dla wszystkich modułów

Domyślnie rejestrowanie w dowolnym skrypcie języka Python jest wyłączone. Jeśli chcesz włączyć rejestrowanie debugowania dla wszystkich modułów w całym skrypcie języka Python, użyj:

```python
logging.basicConfig(level=logging.DEBUG)
```

### <a name="silence-only-msal-logging"></a>Wyciszenie tylko MSAL rejestrowania

Aby wyciszyć tylko rejestrowanie biblioteki MSAL, jednocześnie włączając rejestrowanie debugowania we wszystkich innych modułach w skrypcie języka Python, wyłącz Rejestrator używany przez język MSAL Python:

```Python
logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="personal-and-organizational-data-in-python"></a>Dane osobowe i organizacyjne w języku Python

MSAL dla języka Python nie rejestruje danych osobowych ani danych organizacji. Nie ma właściwości umożliwiającej włączenie lub wyłączenie rejestrowania danych osobowych lub organizacji.

Możesz użyć standardowego rejestrowania w języku Python, aby zalogować się, ale użytkownik jest odpowiedzialny za bezpieczne obsługiwanie danych poufnych i spełnienie wymagań prawnych.

Aby uzyskać więcej informacji na temat rejestrowania w języku Python, zapoznaj się z artykułem rejestrowanie w języku Python  [porady](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial).

---
