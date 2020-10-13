---
title: Przenoszenie aplikacji demona, która wywołuje interfejsy API sieci Web w środowisku produkcyjnym Microsoft Identity platform | Azure
description: Dowiedz się, jak przenieść aplikację demona, która wywołuje interfejsy API sieci Web w środowisku produkcyjnym
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 72e6d0a8677f7a8175223b80541c99026a9eb16f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88119168"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Aplikacja demona, która wywołuje interfejsy API sieci Web — Przenieś do środowiska produkcyjnego

Teraz, gdy wiesz już, jak uzyskać i używać tokenu dla wywołania Service to Service, Dowiedz się, jak przenieść aplikację do środowiska produkcyjnego.

## <a name="deployment---multitenant-daemon-apps"></a>Wdrażanie — aplikacje demona wielodostępna

Jeśli jesteś niezależnym dostawcą oprogramowania i utworzysz aplikację demona, która może być uruchamiana w kilku dzierżawcach, należy upewnić się, że administrator dzierżawy:

- Inicjuje obsługę nazwy głównej usługi dla aplikacji.
- Przyznaje zgodę na aplikację.

Należy wyjaśnić swoim klientom sposób wykonywania tych operacji. Aby uzyskać więcej informacji, zobacz [żądanie zgody dla całej dzierżawy](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Następne kroki

Oto kilka linków, które pomogą Ci dowiedzieć się więcej:

# <a name="net"></a>[.NET](#tab/dotnet)

- Szybki Start: [Uzyskiwanie tokenu i wywoływanie Microsoft Graph interfejsu API z poziomu aplikacji konsolowej przy użyciu tożsamości aplikacji](./quickstart-v2-netcore-daemon.md).
- Dokumentacja referencyjna dla:
  - Tworzenie wystąpienia [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder).
  - Wywoływanie [AcquireTokenForClient](/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder).
- Inne przykłady/samouczki:
  - [program Microsoft-Identity-platform-Console DAEMON](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) oferuje prostą aplikację konsolową demona .NET Core, która wyświetla użytkowników dzierżawcy Microsoft Graph.

    ![Topologia aplikacji demona Przykładowa](media/scenario-daemon-app/daemon-app-sample.svg)

    Ten sam przykład ilustruje także odmianę certyfikatów:

    ![Topologia aplikacji demona Przykładowa — certyfikaty](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [Microsoft-Identity-platform-ASPNET-webapp-demos](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) ASP.NET MVC aplikacji sieci Web, która synchronizuje dane z Microsoft Graph przy użyciu tożsamości aplikacji, a nie w imieniu użytkownika. Ten przykład ilustruje również proces wyrażania zgody administratora.

    ![topology](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="python"></a>[Python](#tab/python)

Wypróbuj Szybki Start [token i Wywołaj interfejs API Microsoft Graph z aplikacji konsolowej języka Python przy użyciu tożsamości aplikacji](./quickstart-v2-python-daemon.md).

# <a name="java"></a>[Java](#tab/java)

MSAL Java jest obecnie w publicznej wersji zapoznawczej. Aby uzyskać więcej informacji, zobacz [MSAL Java dev Samples](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples).

---