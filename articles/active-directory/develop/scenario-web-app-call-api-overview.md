---
title: Tworzenie aplikacji sieci Web, która wywołuje interfejsy API sieci Web — Microsoft Identity platform | Azure
description: Dowiedz się, jak utworzyć aplikację sieci Web, która wywołuje interfejsy API sieci Web (omówienie)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5af9e34baf6115e801fbfe35e6e3895e48b360e7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80881727"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>Scenariusz: aplikacja sieci Web, która wywołuje interfejsy API sieci Web

Dowiedz się, jak utworzyć aplikację sieci Web, która podpisuje użytkowników na platformie tożsamości firmy Microsoft, a następnie wywołuje interfejsy API sieci Web w imieniu zalogowanego użytkownika.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

W tym scenariuszu przyjęto założenie, że został już przetworzony następujący scenariusz:

> [!div class="nextstepaction"]
> [Aplikacja internetowa z możliwością logowania użytkowników](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Omówienie

Należy dodać uwierzytelnianie do aplikacji sieci Web, aby umożliwić użytkownikom logowanie się i wywoływanie internetowego interfejsu API w imieniu zalogowanego użytkownika.

![Aplikacja internetowa, która wywołuje internetowe interfejsy API](./media/scenario-webapp/web-app.svg)

Aplikacje sieci Web, które wywołują interfejsy API sieci Web, są poufnymi aplikacjami klienckimi.
Dlatego rejestrujemy wpis tajny (hasło aplikacji lub certyfikat) z Azure Active Directory (Azure AD). Ten wpis tajny jest przesyłany podczas wywołania usługi Azure AD w celu uzyskania tokenu.

## <a name="specifics"></a>Szczegółowych informacji

> [!NOTE]
> Dodawanie logowania do aplikacji sieci Web polega na ochronie samej aplikacji sieci Web. Ochronę uzyskuje się za pomocą bibliotek *oprogramowania pośredniczącego* , a nie biblioteki uwierzytelniania firmy Microsoft (MSAL). W poprzednim scenariuszu [aplikacja sieci Web, która](scenario-web-app-sign-user-overview.md)jest zadawana użytkownikom, ponosi ten temat.
>
> W tym scenariuszu opisano sposób wywoływania interfejsów API sieci Web z aplikacji sieci Web. Musisz uzyskać tokeny dostępu dla tych interfejsów API sieci Web. Aby uzyskać te tokeny, należy użyć bibliotek MSAL.

Opracowywanie tego scenariusza obejmuje następujące zadania:

- Podczas [rejestracji aplikacji](scenario-web-app-call-api-app-registration.md)należy podać identyfikator URI odpowiedzi, klucz tajny lub certyfikat, który ma być współużytkowany z usługą Azure AD. Jeśli aplikacja zostanie wdrożona w kilku lokalizacjach, należy podać te informacje dla każdej lokalizacji.
- [Konfiguracja aplikacji](scenario-web-app-call-api-app-configuration.md) musi udostępniać poświadczenia klienta, które zostały udostępnione usłudze Azure AD podczas rejestracji aplikacji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Aplikacja sieci Web, która wywołuje interfejsy API sieci Web: Rejestracja aplikacji](scenario-web-app-call-api-app-registration.md)
