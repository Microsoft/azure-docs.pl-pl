---
title: Rejestrowanie błędów i wyjątków w programie MSAL for Java
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak rejestrować błędy i wyjątki w programie MSAL for Java
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
ms.openlocfilehash: d3fa13a6751b2d8acf1fc99aecbf174f1823fb0b
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954922"
---
# <a name="logging-in-msal-for-java"></a>Rejestrowanie w bibliotece MSAL dla języka Java

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

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

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej przykładów kodu, zapoznaj się z przykładami [kodu platformy tożsamości firmy Microsoft](sample-v2-code.md).