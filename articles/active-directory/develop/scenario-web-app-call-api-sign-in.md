---
title: Usuwanie kont z pamięci podręcznej tokenu na wylogowaniu — platforma tożsamości firmy Microsoft | Azure
description: Dowiedz się, jak usunąć konto z pamięci podręcznej tokeny przy wylogowaniu
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b7f59f235f4baa270b36b01cc4532227ab23fbc8
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442535"
---
# <a name="a-web-app-that-calls-web-apis-remove-accounts-from-the-token-cache-on-global-sign-out"></a>Aplikacja sieci Web, która wywołuje interfejsy API sieci Web: usuwanie kont z pamięci podręcznej tokenów na globalnym wylogowaniu

Dowiesz się, jak dodać logowanie do aplikacji sieci Web w [aplikacji sieci Web, która loguje się w użytkownikach: Logowanie i](scenario-web-app-sign-user-sign-in.md)wylogowywanie.

Wylogowywanie jest inne dla aplikacji sieci Web, która wywołuje interfejsy API sieci Web. Gdy użytkownik wyloguje się z aplikacji lub z dowolnej aplikacji, należy usunąć tokeny skojarzone z tym użytkownikiem z pamięci podręcznej tokenów.

## <a name="intercept-the-callback-after-single-sign-out"></a>Przechwycenie wywołania zwrotnego po wylogowaniu jednokrotnym

Aby wyczyścić wpis w pamięci podręcznej token skojarzony z wylogowanym kontem, aplikacja może przechwycić `logout` zdarzenie After. Aplikacje sieci Web przechowują tokeny dostępu dla każdego użytkownika w pamięci podręcznej tokenów. Przechwycenie po `logout` wywołaniu zwrotnym może usunąć użytkownika z pamięci podręcznej.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Microsoft. Identity. Web zajmuje się wdrożeniem wylogowania. Aby uzyskać szczegółowe informacje, zobacz [Microsoft. Identity. Web Code Source](https://github.com/AzureAD/microsoft-identity-web/blob/c29f1a7950b940208440bebf0bcb524a7d6bee22/src/Microsoft.Identity.Web/WebAppExtensions/WebAppCallsWebApiAuthenticationBuilderExtensions.cs#L168-L176)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Przykład ASP.NET nie usuwa kont z pamięci podręcznej na globalnym wylogowaniu.

# <a name="java"></a>[Java](#tab/java)

Przykład Java nie usuwa kont z pamięci podręcznej na globalnym wylogowaniu.

# <a name="python"></a>[Python](#tab/python)

Przykład języka Python nie usuwa kont z pamięci podręcznej na globalnym wylogowaniu.

---

## <a name="next-steps"></a>Następne kroki

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Przejdź do następnego artykułu w tym scenariuszu, [Uzyskaj token dla aplikacji sieci Web](./scenario-web-app-call-api-acquire-token.md?tabs=aspnetcore).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Przejdź do następnego artykułu w tym scenariuszu, [Uzyskaj token dla aplikacji sieci Web](./scenario-web-app-call-api-acquire-token.md?tabs=aspnet).

# <a name="java"></a>[Java](#tab/java)

Przejdź do następnego artykułu w tym scenariuszu, [Uzyskaj token dla aplikacji sieci Web](./scenario-web-app-call-api-acquire-token.md?tabs=java).

# <a name="python"></a>[Python](#tab/python)

Przejdź do następnego artykułu w tym scenariuszu, [Uzyskaj token dla aplikacji sieci Web](./scenario-web-app-call-api-acquire-token.md?tabs=python).

---