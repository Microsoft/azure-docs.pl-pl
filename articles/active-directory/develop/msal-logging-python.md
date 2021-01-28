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
ms.openlocfilehash: 560caa7e29ce12b58e151a1362aaf2c662646f13
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954804"
---
# <a name="logging-in-msal-for-python"></a>Rejestrowanie w bibliotece MSAL dla języka Python

[!INCLUDE [MSAL logging introduction](../../../includes/active-directory-develop-error-logging-introduction.md)]

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

Aby uzyskać więcej informacji na temat rejestrowania w języku Python, zobacz Rejestrowanie w języku Python  [: jak to zrobić](https://docs.python.org/3/howto/logging.html#logging-basic-tutorial).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej przykładów kodu, zapoznaj się z przykładami [kodu platformy tożsamości firmy Microsoft](sample-v2-code.md).

---