---
title: Scenariusz jednostronicowej aplikacji JavaScript
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć aplikację jednostronicową (omówienie scenariusza), korzystając z platformy tożsamości firmy Microsoft.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40, devx-track-js
ms.openlocfilehash: 47b8c8c074a5e0ce3ed73a2a9a4b06aa307cdff3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98756430"
---
# <a name="scenario-single-page-application"></a>Scenariusz: aplikacja jednostronicowa

Dowiedz się wszystkiego, co musisz zrobić, aby skompilować aplikację jednostronicową (SPA).

## <a name="getting-started"></a>Wprowadzenie

Jeśli jeszcze tego nie zrobiono, Utwórz swoją pierwszą aplikację, wykonując skrypt JavaScript SPA:

[Szybki Start: aplikacja jednostronicowa](./quickstart-v2-javascript.md)

## <a name="overview"></a>Omówienie

Wiele nowoczesnych aplikacji sieci Web jest skompilowanych jako aplikacje jednostronicowe po stronie klienta. Deweloperzy zapisują je przy użyciu języka JavaScript lub środowiska SPA, takiego jak kątowy, Vue i reagują. Aplikacje te działają w przeglądarce internetowej i mają inne cechy uwierzytelniania niż tradycyjne aplikacje sieci Web po stronie serwera.

Platforma tożsamości firmy Microsoft udostępnia **dwie** opcje umożliwiające użytkownikom logowanie jednostronicowe aplikacji oraz uzyskiwanie tokenów dostępu do usług zaplecza lub interfejsów API sieci Web:

- [Przepływ kodu autoryzacji OAuth 2,0 (z PKCE)](./v2-oauth2-auth-code-flow.md). Przepływ kodu autoryzacji umożliwia aplikacji wymianę kodu autoryzacji dla tokenów **identyfikatora** , aby reprezentować tokeny uwierzytelnionego użytkownika i **dostępu** , które są konieczne do wywołania chronionych interfejsów API. Ponadto zwraca tokeny **odświeżania** , które zapewniają długoterminowy dostęp do zasobów w imieniu użytkowników, bez konieczności interakcji z tymi użytkownikami. Jest to **zalecane** podejście.

![Aplikacje jednostronicowe — uwierzytelnianie](./media/scenarios/spa-app-auth.svg)

- [Niejawny przepływ OAuth 2,0](./v2-oauth2-implicit-grant-flow.md). Przepływ niejawnego przydzielenia umożliwia aplikacji uzyskanie tokenów dotyczących **identyfikatorów** i **dostępu** . W przeciwieństwie do przepływu kodu autoryzacji niejawny przepływ przyznania nie zwraca **tokenu odświeżania**.

![Aplikacje jednostronicowe — niejawne](./media/scenarios/spa-app.svg)

Ten przepływ uwierzytelniania nie obejmuje scenariuszy aplikacji, które wykorzystują Międzyplatformowe struktury JavaScript, takie jak elektron i reagowanie na nie. Wymagają one dalszych możliwości interakcji z natywnymi platformami.

## <a name="specifics"></a>Szczegółowych informacji

Aby włączyć ten scenariusz dla aplikacji, potrzebne są:

* Rejestrowanie aplikacji w usłudze Azure Active Directory (Azure AD). Kroki rejestracji różnią się między niejawnym przepływem uprawnień a przepływem kodu autoryzacji.
* Konfiguracja aplikacji z zarejestrowanymi właściwościami aplikacji, takimi jak identyfikator aplikacji.
* Użycie biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL.js) w celu przeprowadzenia przepływu uwierzytelniania w celu zalogowania się i uzyskania tokenów.

## <a name="recommended-reading"></a>Zalecany odczyt

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu w tym scenariuszu — [Rejestracja aplikacji](scenario-spa-app-registration.md).
