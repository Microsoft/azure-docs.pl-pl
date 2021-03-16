---
title: Logowanie użytkowników z aplikacji sieci Web | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć aplikację internetową, która loguje się do użytkowników (omówienie)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: dbd4923dd2843ab2c043da69f1b72efd8e1eb701
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573051"
---
# <a name="scenario-web-app-that-signs-in-users"></a>Scenariusz: aplikacja sieci Web, która loguje użytkowników

Dowiedz się, co musisz zrobić, aby utworzyć aplikację sieci Web korzystającą z platformy tożsamości firmy Microsoft do logowania użytkowników.

## <a name="getting-started"></a>Wprowadzenie

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Jeśli chcesz utworzyć swoją pierwszą aplikację sieci Web przenośną (ASP.NET Core), która loguje użytkowników, wykonaj następujące czynności:

[Szybki Start: ASP.NET Core aplikacji sieci Web, która loguje użytkowników](quickstart-v2-aspnet-core-webapp.md)

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Jeśli chcesz zrozumieć, jak dodać logowanie do istniejącej aplikacji sieci Web ASP.NET, spróbuj wykonać następujące czynności:

[Szybki Start: ASP.NET Web App, który loguje użytkowników](quickstart-v2-aspnet-webapp.md)

# <a name="java"></a>[Java](#tab/java)

Jeśli jesteś deweloperem języka Java, spróbuj wykonać następujące czynności:

[Szybki Start: Dodawanie logowania do aplikacji sieci Web w języku Java przez firmę Microsoft](quickstart-v2-java-webapp.md)

# <a name="python"></a>[Python](#tab/python)

Jeśli tworzysz przy użyciu języka Python, wypróbuj następujący Przewodnik Szybki Start:

[Szybki Start: Dodawanie logowania z firmą Microsoft do aplikacji sieci Web w języku Python](quickstart-v2-python-webapp.md)

---

## <a name="overview"></a>Omówienie

Należy dodać uwierzytelnianie do aplikacji sieci Web, aby umożliwić użytkownikom logowanie się. Dodanie uwierzytelniania umożliwia aplikacji sieci Web dostęp do ograniczonych informacji o profilach w celu dostosowania środowiska dla użytkowników.

Aplikacje sieci Web uwierzytelniają użytkownika w przeglądarce internetowej. W tym scenariuszu aplikacja sieci Web kieruje przeglądarkę użytkownika do podpisania ich w usłudze Azure Active Directory (Azure AD). Usługa Azure AD zwraca odpowiedź logowania za pomocą przeglądarki użytkownika, która zawiera oświadczenia dotyczące użytkownika w tokenie zabezpieczającym. Podpisywanie użytkowników korzysta z protokołu [Open ID Connect](./v2-protocols-oidc.md) Standard, uproszczonego przy użyciu [bibliotek](scenario-web-app-sign-user-app-configuration.md#microsoft-libraries-supporting-web-apps)pośredniczących.

![Znaki aplikacji sieci Web w użytkownikach](./media/scenario-webapp/scenario-webapp-signs-in-users.svg)

W drugiej fazie można umożliwić aplikacji wywoływanie interfejsów API sieci Web w imieniu zalogowanego użytkownika. Ta kolejna faza jest innym scenariuszem, który można znaleźć w [aplikacji sieci Web, która wywołuje interfejsy API sieci Web](scenario-web-app-call-api-overview.md).

> [!NOTE]
> Dodawanie logowania do aplikacji sieci Web polega na tym, jak chronić aplikację sieci Web i sprawdzać token użytkownika, który jest biblioteką  **oprogramowania pośredniczącego** . W przypadku platformy .NET ten scenariusz nie wymaga jeszcze biblioteki uwierzytelniania firmy Microsoft (MSAL), która ma na celu uzyskanie tokenu do wywołania chronionych interfejsów API. Biblioteki uwierzytelniania zostaną wprowadzone w scenariuszu kontynuacji, gdy aplikacja sieci Web wymaga wywołania interfejsów API sieci Web.

## <a name="specifics"></a>Szczegółowych informacji

- Podczas rejestracji aplikacji należy podać jeden lub kilka (Jeśli aplikacja jest wdrażana w kilku lokalizacjach) identyfikatorów URI odpowiedzi. W niektórych przypadkach (ASP.NET i ASP.NET Core) należy włączyć token identyfikatora. Na koniec należy skonfigurować identyfikator URI, aby aplikacja działała w celu wylogowania użytkowników.
- W kodzie aplikacji należy podać uprawnienia do logowania pełnomocników aplikacji sieci Web. Możesz chcieć dostosować sprawdzanie poprawności tokenów (w szczególności w scenariuszach partnerskich).
- Aplikacje sieci Web obsługują wszystkie typy kont. Aby uzyskać więcej informacji, zobacz [obsługiwane typy kont](v2-supported-account-types.md).

## <a name="recommended-reading"></a>Zalecany odczyt

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Następne kroki

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Przejdź do następnego artykułu w tym scenariuszu — [Rejestracja aplikacji](./scenario-web-app-sign-user-app-registration.md?tabs=aspnetcore).

# <a name="aspnet"></a>[ASP.NET](#tab/aspnet)

Przejdź do następnego artykułu w tym scenariuszu — [Rejestracja aplikacji](./scenario-web-app-sign-user-app-registration.md?tabs=aspnet).

# <a name="java"></a>[Java](#tab/java)

Przejdź do następnego artykułu w tym scenariuszu — [Rejestracja aplikacji](./scenario-web-app-sign-user-app-registration.md?tabs=java).

# <a name="python"></a>[Python](#tab/python)

Przejdź do następnego artykułu w tym scenariuszu — [Rejestracja aplikacji](./scenario-web-app-sign-user-app-registration.md?tabs=python).

---
