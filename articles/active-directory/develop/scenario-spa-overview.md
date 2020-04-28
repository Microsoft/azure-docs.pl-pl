---
title: Scenariusz jednostronicowej aplikacji JavaScript — platforma tożsamości firmy Microsoft | Azure
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
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 3ead0ea58c6860519f027eb6a7450df37396bd89
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80885178"
---
# <a name="scenario-single-page-application"></a>Scenariusz: aplikacja jednostronicowa

Dowiedz się wszystkiego, co musisz zrobić, aby skompilować aplikację jednostronicową (SPA).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Wprowadzenie

Możesz utworzyć swoją pierwszą aplikację, postępując zgodnie z przewodnikiem Autostart JavaScript:

> [!div class="nextstepaction"]
> [Szybki Start: aplikacja jednostronicowa](./quickstart-v2-javascript.md)

## <a name="overview"></a>Omówienie

Wiele nowoczesnych aplikacji sieci Web jest skompilowanych jako aplikacje jednostronicowe po stronie klienta. Deweloperzy zapisują je za pomocą języka JavaScript lub środowiska SPA, takiego jak kątowy, Vue. js i reaguje. js. Aplikacje te działają w przeglądarce internetowej i mają inne cechy uwierzytelniania niż tradycyjne aplikacje sieci Web po stronie serwera. 

Platforma tożsamości firmy Microsoft umożliwia aplikacjom jednostronicowym Logowanie użytkowników i uzyskiwanie tokenów w celu uzyskania dostępu do usług zaplecza lub interfejsów API sieci Web przy użyciu [niejawnego przepływu OAuth 2,0](./v2-oauth2-implicit-grant-flow.md). Niejawny przepływ umożliwia aplikacji uzyskanie tokenów identyfikatora reprezentujących uwierzytelnionego użytkownika, a także dostęp do tokenów wymaganych do wywołania chronionych interfejsów API.

![Aplikacje jednostronicowe](./media/scenarios/spa-app.svg)

Ten przepływ uwierzytelniania nie obejmuje scenariuszy aplikacji, które wykorzystują Międzyplatformowe struktury JavaScript, takie jak elektron i reagowanie na nie. Wymagają one dalszych możliwości interakcji z natywnymi platformami.

## <a name="specifics"></a>Szczegółowych informacji

Aby włączyć ten scenariusz dla aplikacji, potrzebne są:

* Rejestrowanie aplikacji w usłudze Azure Active Directory (Azure AD). Ta rejestracja obejmuje włączenie niejawnego przepływu i ustawienie identyfikatora URI przekierowania, do którego zwracane są tokeny.
* Konfiguracja aplikacji z zarejestrowanymi właściwościami aplikacji, takimi jak identyfikator aplikacji.
* Użycie biblioteki uwierzytelniania firmy Microsoft (MSAL) do przeprowadzenia przepływu uwierzytelniania w celu zalogowania się i uzyskania tokenów.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Rejestracja aplikacji](scenario-spa-app-registration.md)
