---
title: Rejestrowanie błędów i wyjątków w MSAL dla języka Python
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak rejestrować błędy i wyjątki w programie MSAL for Python
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
ms.openlocfilehash: 1d52b017f94785f5fb25a25f127ae52d96e97d8b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578757"
---
# <a name="logging-in-msal-for-python"></a>Rejestrowanie w bibliotece MSAL dla języka Python

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

## <a name="msal-for-python-logging"></a>MSAL na potrzeby rejestrowania w języku Python

Logowanie w programie MSAL for Python wykorzystuje [moduł rejestrowania w standardowej bibliotece języka Python](https://docs.python.org/3/library/logging.html). Rejestrowanie MSAL można skonfigurować w następujący sposób (i zobaczyć w działaniu w [username_password_sample](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.0.0/sample/username_password_sample.py#L31L32)):

### <a name="enable-debug-logging-for-all-modules"></a>Włącz rejestrowanie debugowania dla wszystkich modułów

Domyślnie rejestrowanie w dowolnym skrypcie języka Python jest wyłączone. Jeśli chcesz włączyć pełne rejestrowanie dla **wszystkich** modułów języka Python w skrypcie, użyj `logging.basicConfig` z poziomu `logging.DEBUG` :

```python
import logging

logging.basicConfig(level=logging.DEBUG)
```

Spowoduje to wydrukowanie wszystkich komunikatów dziennika, które są przekazywane do modułu rejestrowania, do wyjścia standardowego.

### <a name="configure-msal-logging-level"></a>Konfigurowanie poziomu rejestrowania MSAL

Można skonfigurować poziom rejestrowania dla dostawcy dziennika języka Python przy użyciu `logging.getLogger()` metody z nazwą rejestratora `"msal"` :

```python
import logging

logging.getLogger("msal").setLevel(logging.WARN)
```

### <a name="configure-msal-logging-with-azure-app-insights"></a>Konfigurowanie rejestrowania MSAL za pomocą usługi Azure App Insights

Dzienniki języka Python są przekazywane do procedury obsługi dziennika, która domyślnie jest `StreamHandler` . Aby wysłać dzienniki MSAL do Application Insights z kluczem instrumentacji, użyj `AzureLogHandler` dostarczonej przez `opencensus-ext-azure` bibliotekę biblioteki.

Aby zainstalować program, `opencensus-ext-azure` Dodaj `opencensus-ext-azure` pakiet z PyPI do zależności lub instalacji PIP:

```console
pip install opencensus-ext-azure
```

Następnie Zmień domyślną procedurę obsługi `"msal"` dostawcy dziennika na wystąpienie z `AzureLogHandler` zestawem kluczy Instrumentacji w `APP_INSIGHTS_KEY` zmiennej środowiskowej:

```python
import logging
import os

from opencensus.ext.azure.log_exporter import AzureLogHandler

APP_INSIGHTS_KEY = os.getenv('APP_INSIGHTS_KEY')

logging.getLogger("msal").addHandler(AzureLogHandler(connection_string='InstrumentationKey={0}'.format(APP_INSIGHTS_KEY))
```

### <a name="personal-and-organizational-data-in-python"></a>Dane osobowe i organizacyjne w języku Python

MSAL dla języka Python nie rejestruje danych osobowych ani danych organizacji. Nie ma właściwości umożliwiającej włączenie lub wyłączenie rejestrowania danych osobowych lub organizacji.

Możesz użyć standardowego rejestrowania w języku Python, aby zalogować się, ale użytkownik jest odpowiedzialny za bezpieczne obsługiwanie danych poufnych i spełnienie wymagań prawnych.

Aby uzyskać więcej informacji na temat rejestrowania w języku Python, zobacz Rejestrowanie w języku Python  [: jak to zrobić](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej przykładów kodu, zapoznaj się z przykładami [kodu platformy tożsamości firmy Microsoft](sample-v2-code.md).
