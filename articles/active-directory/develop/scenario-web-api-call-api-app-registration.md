---
title: Rejestrowanie internetowego interfejsu API, który wywołuje interfejsy API sieci Web — Microsoft Identity platform | Azure
description: Dowiedz się, jak utworzyć internetowy interfejs API, który wywołuje podrzędne interfejsy API sieci Web (Rejestracja aplikacji).
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
ms.openlocfilehash: c0b009d6d53a117aa11d0629fb649b2dd55559af
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/03/2020
ms.locfileid: "89438198"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Internetowy interfejs API, który wywołuje interfejsy API sieci Web: Rejestracja aplikacji

Internetowy interfejs API, który wywołuje podrzędne interfejsy API sieci Web, ma taką samą rejestrację jak chroniony internetowy interfejs API. W związku z tym należy postępować zgodnie z instrukcjami w temacie [Protected Web API: Rejestracja aplikacji](scenario-protected-web-api-app-registration.md).

Ponieważ aplikacja sieci Web wywołuje teraz interfejsy API sieci Web, jest to poufna aplikacja kliencka. Dlatego wymagane są dodatkowe informacje o rejestracji: aplikacja musi udostępniać wpisy tajne (poświadczenia klienta) za pomocą platformy tożsamości firmy Microsoft.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Uprawnienia aplikacji

Usługi Web Apps wywołania interfejsów API w imieniu użytkowników, dla których odebrano token okaziciela. Aplikacje sieci Web muszą zażądać uprawnień delegowanych. Aby uzyskać więcej informacji, zobacz [Dodawanie uprawnień w celu uzyskania dostępu do internetowego interfejsu API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Internetowy interfejs API, który wywołuje interfejsy API sieci Web: Konfiguracja kodu](scenario-web-api-call-api-app-configuration.md)
