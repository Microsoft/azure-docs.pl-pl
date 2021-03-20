---
title: Obsługa błędów i wyjątków w bibliotece MSAL dla języka Python
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak obsługiwać błędy i wyjątki, wyzwania dotyczące oświadczeń dostępu warunkowego i ponawianie prób w MSAL dla aplikacji języka Python.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, rayluo
ms.custom: aaddev
ms.openlocfilehash: 3046787393d38ed60b54236f33acc065db90321d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98761095"
---
# <a name="handle-errors-and-exceptions-in-msal-for-python"></a>Obsługa błędów i wyjątków w bibliotece MSAL dla języka Python

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-python"></a>Obsługa błędów w MSAL dla języka Python

W programie MSAL for Python większość błędów jest przekazywanych jako wartość zwrotna z wywołania interfejsu API. Ten błąd jest reprezentowany jako słownik zawierający odpowiedź JSON z platformy tożsamości firmy Microsoft.

* Pomyślna odpowiedź zawiera `"access_token"` klucz. Format odpowiedzi jest definiowany przez protokół OAuth2. Aby uzyskać więcej informacji, zobacz [5,1 Pomyślne odpowiedzi](https://tools.ietf.org/html/rfc6749#section-5.1)
* Odpowiedź na błąd zawiera `"error"` i zwykle `"error_description"` . Format odpowiedzi jest definiowany przez protokół OAuth2. Aby uzyskać więcej informacji, zobacz [5,2 odpowiedzi na błędy](https://tools.ietf.org/html/rfc6749#section-5.2)

Po zwróceniu błędu `"error_description"` klucz zawiera komunikat z możliwością odczytu przez człowieka, który z kolei zwykle zawiera kod błędu platformy tożsamości firmy Microsoft. Aby uzyskać szczegółowe informacje o różnych kodach błędów, zobacz [kody błędów uwierzytelniania i autoryzacji](./reference-aadsts-error-codes.md).

W programie MSAL for Python wyjątki są rzadko, ponieważ większość błędów jest obsługiwana przez zwrócenie wartości błędu. `ValueError`Wyjątek jest zgłaszany tylko wtedy, gdy występuje problem z próbą użycia biblioteki, na przykład gdy parametry interfejsu API są źle sformułowane.

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Następne kroki

Rozważ włączenie [rejestrowania w MSAL dla języka Python](msal-logging-python.md) , aby ułatwić diagnozowanie i Debugowanie problemów.
