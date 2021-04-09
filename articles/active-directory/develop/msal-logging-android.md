---
title: Rejestrowanie błędów i wyjątków w MSAL dla systemu Android.
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak rejestrować błędy i wyjątki w programie MSAL for Android.
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
ms.openlocfilehash: ce0929adbb2b0213cfd4ee61fe795a2d5f33c648
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98954888"
---
# <a name="logging-in-msal-for-android"></a>Rejestrowanie w bibliotece MSAL dla systemu Android

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

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

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej przykładów kodu, zapoznaj się z przykładami [kodu platformy tożsamości firmy Microsoft](sample-v2-code.md).