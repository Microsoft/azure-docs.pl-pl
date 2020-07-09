---
title: Chroniony internetowy interfejs API — Omówienie
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć chroniony internetowy interfejs API (omówienie).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: cf66757d28a3883664aaacd85baad9cc0dea6956
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81537206"
---
# <a name="scenario-protected-web-api"></a>Scenariusz: chroniony internetowy interfejs API

W tym scenariuszu dowiesz się, jak uwidocznić internetowy interfejs API. Dowiesz się również, jak chronić internetowy interfejs API, tak aby tylko uwierzytelnieni użytkownicy mieli do niego dostęp.

Aby korzystać z internetowego interfejsu API, należy włączyć użytkowników uwierzytelnionych zarówno przy użyciu kont służbowych, jak i na kontach osobistych firmy Microsoft.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Szczegółowych informacji

Poniżej przedstawiono szczegółowe informacje, które należy znać, aby chronić interfejsy API sieci Web:

- Rejestracja aplikacji musi uwidaczniać co najmniej jeden zakres. Wersja tokenu zaakceptowana przez internetowy interfejs API zależy od odbiorców logowania.
- Konfiguracja kodu dla internetowego interfejsu API musi weryfikować token używany podczas wywoływania internetowego interfejsu API.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Rejestrowanie aplikacji](scenario-protected-web-api-app-registration.md)
