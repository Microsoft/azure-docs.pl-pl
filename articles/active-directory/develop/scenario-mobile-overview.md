---
title: Tworzenie aplikacji mobilnej, która wywołuje interfejsy API sieci Web | Azure
titleSuffix: Microsoft identity platform | Azure
description: Dowiedz się, jak utworzyć aplikację mobilną wywołującą interfejsy API sieci Web (omówienie)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.reviewer: brandwe
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 1f90f7f23fbdf10b91d8dfc7cd00cca83cd32fbc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80882577"
---
# <a name="scenario-mobile-application-that-calls-web-apis"></a>Scenariusz: aplikacja mobilna, która wywołuje interfejsy API sieci Web

Dowiedz się, jak utworzyć aplikację mobilną, która wywołuje interfejsy API sieci Web.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Wprowadzenie

Utwórz pierwszą aplikację mobilną i wypróbuj Przewodnik Szybki Start.

> [!div class="nextstepaction"]
> [Szybki Start: uzyskiwanie tokenu i wywoływanie Microsoft Graph interfejsu API z poziomu aplikacji systemu Android](./quickstart-v2-android.md)
>
> [Szybki Start: uzyskiwanie tokenu i wywoływanie Microsoft Graph interfejsu API z poziomu aplikacji systemu iOS](./quickstart-v2-ios.md)
>
> [Szybki Start: uzyskiwanie tokenu i wywoływanie Microsoft Graph interfejsu API z aplikacji platformy Xamarin dla systemu iOS lub Android](https://github.com/Azure-Samples/active-directory-xamarin-native-v2)

## <a name="overview"></a>Omówienie

Spersonalizowane, bezproblemowe środowisko użytkownika jest niezbędne dla aplikacji mobilnych.  Platforma tożsamości firmy Microsoft umożliwia deweloperom mobilnym tworzenie tego środowiska dla użytkowników systemów iOS i Android. Aplikacja może się zalogować Azure Active Directory użytkowników (Azure AD), użytkowników indywidualnych konto Microsoft i użytkowników Azure AD B2C. Może również uzyskać tokeny do wywoływania internetowego interfejsu API w ich imieniu. Aby zaimplementować te przepływy, użyjemy biblioteki Microsoft Authentication Library (MSAL). MSAL implementuje [przepływ kodu autoryzacji standardu OAuth 2.0](v2-oauth2-auth-code-flow.md)w branży.

![Aplikacje demona](./media/scenarios/mobile-app.svg)

Zagadnienia dotyczące aplikacji mobilnych:

- **Środowisko użytkownika jest kluczem**: zezwól użytkownikom na wyświetlanie wartości aplikacji przed zaproszeniem do logowania. Żądaj tylko wymaganych uprawnień.
- **Obsługa wszystkich konfiguracji użytkowników**: wielu użytkowników pracujących na urządzeniach przenośnych musi być zgodna z zasadami dostępu warunkowego i zasadami zgodności urządzeń. Upewnij się, że te kluczowe scenariusze są obsługiwane.
- Zaimplementuj Logowanie jednokrotne **(SSO)**: za pomocą usługi MSAL i platformy tożsamości firmy Microsoft można włączyć logowanie jednokrotne za pośrednictwem przeglądarki lub Microsoft Authenticator (i Intune — portal firmy w systemie Android).
- **Zaimplementuj tryb udostępnionego urządzenia**: umożliwia korzystanie z aplikacji w scenariuszach współużytkowanych urządzeń, na przykład szpitalach, produkcyjnych, detalicznych i finansowych. [Przeczytaj więcej na temat obsługi trybu udostępnionego urządzenia](msal-shared-devices.md).

## <a name="specifics"></a>Szczegółowych informacji

Podczas tworzenia aplikacji mobilnej na platformie Microsoft Identity należy pamiętać o następujących kwestiach:

- W zależności od platformy niektóre Interakcje użytkownika mogą być wymagane podczas pierwszego logowania użytkowników. Na przykład system iOS wymaga, aby aplikacje pokazywały Interakcje użytkownika, gdy po raz pierwszy korzystają z logowania jednokrotnego za pomocą usługi Microsoft Authenticator (i Intune — Portal firmy w systemie Android).
- W systemach iOS i Android MSAL może używać zewnętrznej przeglądarki do logowania użytkowników. Zewnętrzna przeglądarka może pojawić się na wierzchu swojej aplikacji.
- Nigdy nie używaj wpisu tajnego w aplikacji mobilnej. W tych aplikacjach wpisy tajne są dostępne dla wszystkich użytkowników.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Rejestracja aplikacji](scenario-mobile-app-registration.md)
