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
ms.openlocfilehash: be8208c7b2b67610f8d579b1bc0094bf09b3033e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87026292"
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

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu dla aplikacji sieci Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnetcore)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu dla aplikacji sieci Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=aspnet)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu dla aplikacji sieci Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=java)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Uzyskiwanie tokenu dla aplikacji sieci Web](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-app-call-api-acquire-token?tabs=python)

---
