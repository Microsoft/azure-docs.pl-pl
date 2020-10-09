---
title: Zarejestruj aplikacje demona, które wywołują interfejsy API sieci Web — Microsoft Identity platform | Azure
description: Dowiedz się, jak utworzyć aplikację demona, która wywołuje interfejsy API sieci Web — Rejestracja aplikacji
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 508101ad615dd96559b1c68a61be7c08772545db
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80885484"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Aplikacja demona, która wywołuje interfejsy API sieci Web — Rejestracja aplikacji

Poniżej przedstawiono informacje o tym, co należy wiedzieć podczas rejestrowania aplikacji.

## <a name="supported-account-types"></a>Obsługiwane typy kont

Aplikacje demonów mają sens tylko w dzierżawach usługi Azure AD. Dlatego podczas tworzenia aplikacji należy wybrać jedną z następujących opcji:

- **Tylko konta w tym katalogu organizacji**. Ten wybór jest najbardziej powszechny, ponieważ aplikacje demonów są zwykle zapisywane przez deweloperów branżowych.
- **Konta w dowolnym katalogu organizacyjnym**. Możesz wybrać tę opcję, jeśli jesteś dostawcą niezależnego dostawcy oprogramowania. Aby można było zatwierdzić administratorów dzierżawy klientów.

## <a name="authentication---no-reply-uri-needed"></a>Uwierzytelnianie — nie jest wymagany identyfikator URI odpowiedzi

W przypadku, gdy poufna aplikacja kliencka używa *tylko* przepływu poświadczeń klienta, identyfikator URI odpowiedzi nie musi być zarejestrowany. Nie jest to konieczne w przypadku konfiguracji lub konstrukcji aplikacji. Przepływ poświadczeń klienta nie używa tego programu.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>Uprawnienia interfejsu API — uprawnienia aplikacji i zgoda administratora

Aplikacja demona może żądać tylko uprawnień aplikacji do interfejsów API (nie delegowanych uprawnień). Na stronie **uprawnienia interfejsu API** do rejestracji aplikacji, po wybraniu opcji **Dodaj uprawnienie** i wybraniu rodziny interfejsów API wybierz pozycję **uprawnienia aplikacji**, a następnie wybierz swoje uprawnienia.

![Uprawnienia aplikacji i zgoda administratora](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> Internetowy interfejs API, który ma zostać wywołany, musi definiować *uprawnienia aplikacji (Role aplikacji)*, a nie delegowane uprawnienia. Aby uzyskać szczegółowe informacje na temat sposobu ujawniania takiego interfejsu API, zobacz [chroniony internetowy interfejs API: Rejestracja aplikacji — gdy interfejs API sieci Web jest wywoływany przez aplikację demona](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).

Aplikacje demonów wymagają, aby administrator dzierżawy wstępnie wyraził zgodę na aplikację wywołującą internetowy interfejs API. Administratorzy dzierżawy podają tę zgodę na tej samej stronie **uprawnień interfejsu API** , wybierając pozycję **Udziel zgody administratora na *naszą organizację* **

Jeśli jesteś niezależnym dostawcą oprogramowania, Kompilując aplikację wielodostępną, należy zapoznać się z sekcją [wdrażanie w przypadku aplikacji wielodostępnych demona](scenario-daemon-production.md#deployment---multitenant-daemon-apps).

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Aplikacja demona — konfiguracja kodu aplikacji](./scenario-daemon-app-configuration.md)
