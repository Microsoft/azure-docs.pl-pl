---
title: Przenoszenie aplikacji demona, która wywołuje interfejsy API sieci Web w środowisku produkcyjnym | Azure
titleSuffix: Microsoft identity platform
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
ms.openlocfilehash: d903f04055d1607ee782bd502d99a8fd9cde87ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578451"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Aplikacja demona, która wywołuje interfejsy API sieci Web — Przenieś do środowiska produkcyjnego

Teraz, gdy wiesz już, jak uzyskać i używać tokenu dla wywołania Service to Service, Dowiedz się, jak przenieść aplikację do środowiska produkcyjnego.

## <a name="deployment---multitenant-daemon-apps"></a>Wdrażanie — aplikacje demona wielodostępna

Jeśli jesteś niezależnym dostawcą oprogramowania i utworzysz aplikację demona, która może działać w kilku dzierżawcach, upewnij się, że administrator dzierżawy:

- Inicjuje obsługę nazwy głównej usługi dla aplikacji.
- Przyznaje zgodę na aplikację.

Należy wyjaśnić swoim klientom sposób wykonywania tych operacji. Aby uzyskać więcej informacji, zobacz [żądanie zgody dla całej dzierżawy](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="code-samples"></a>Przykłady kodu

# <a name="net"></a>[.NET](#tab/dotnet)

- Dokumentacja referencyjna dla:
  - Tworzenie wystąpienia [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder).
  - Wywoływanie [AcquireTokenForClient](/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder).
- Inne przykłady/samouczki:
  - [program Microsoft-Identity-platform-Console DAEMON](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) oferuje niewielką aplikację konsolową demona programu .NET Core, która wyświetla użytkowników dzierżawcy Microsoft Graph.

    ![Topologia aplikacji demona Przykładowa](media/scenario-daemon-app/daemon-app-sample.svg)

    Ten sam przykład ilustruje także odmianę certyfikatów:

    ![Topologia aplikacji demona Przykładowa — certyfikaty](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [Microsoft-Identity-platform-ASPNET-webapp-demos](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) ASP.NET MVC aplikacji sieci Web, która synchronizuje dane z Microsoft Graph przy użyciu tożsamości aplikacji, a nie w imieniu użytkownika. Ten przykład ilustruje również proces wyrażania zgody administratora.

    ![topology](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="java"></a>[Java](#tab/java)

Wypróbuj Szybki Start [token i Wywołaj interfejs API Microsoft Graph z aplikacji konsolowej Java przy użyciu tożsamości aplikacji](quickstart-v2-java-daemon.md).

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

- Aby uzyskać więcej informacji, zobacz:
  - Informacje o [konfiguracji](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/configuration.md)
  - Tworzenie wystąpienia [ConfidentialClientApplication](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md)
  - [Często zadawane pytania](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/faq.md)
- Inne przykłady/samouczki:
  - [Przykład demona konsoli Node MSAL](https://github.com/Azure-Samples/ms-identity-javascript-nodejs-console)

# <a name="python"></a>[Python](#tab/python)

Wypróbuj Szybki Start [token i Wywołaj interfejs API Microsoft Graph z aplikacji konsolowej języka Python przy użyciu tożsamości aplikacji](quickstart-v2-python-daemon.md).

---

## <a name="next-steps"></a>Następne kroki

Oto kilka linków, które pomogą Ci dowiedzieć się więcej:

# <a name="net"></a>[.NET](#tab/dotnet)

Wypróbuj Szybki Start [token i Wywołaj interfejs API Microsoft Graph z aplikacji konsolowej platformy .NET Core przy użyciu tożsamości aplikacji](quickstart-v2-netcore-daemon.md).

# <a name="java"></a>[Java](#tab/java)

Wypróbuj Szybki Start [token i Wywołaj interfejs API Microsoft Graph z aplikacji konsolowej Java przy użyciu tożsamości aplikacji](quickstart-v2-java-daemon.md).

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Wypróbuj Szybki Start [token i Wywołaj interfejs API Microsoft Graph z aplikacji konsolowej Node.js przy użyciu tożsamości aplikacji](quickstart-v2-nodejs-console.md).

# <a name="python"></a>[Python](#tab/python)

Wypróbuj Szybki Start [token i Wywołaj interfejs API Microsoft Graph z aplikacji konsolowej języka Python przy użyciu tożsamości aplikacji](quickstart-v2-python-daemon.md).

---
