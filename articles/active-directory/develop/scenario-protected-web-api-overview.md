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
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: c9cc8e6fcc796cfc7a09cf7bc1431646a6603af0
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94442977"
---
# <a name="scenario-protected-web-api"></a>Scenariusz: chroniony internetowy interfejs API

W tym scenariuszu dowiesz się, jak uwidocznić internetowy interfejs API. Dowiesz się również, jak chronić internetowy interfejs API, tak aby tylko uwierzytelnieni użytkownicy mieli do niego dostęp.

Aby korzystać z internetowego interfejsu API, należy włączyć użytkowników uwierzytelnionych zarówno przy użyciu kont służbowych, jak i na kontach osobistych firmy Microsoft.

## <a name="specifics"></a>Szczegółowych informacji

Poniżej przedstawiono szczegółowe informacje, które należy znać, aby chronić interfejsy API sieci Web:

- Rejestracja aplikacji musi uwidaczniać co najmniej jeden *zakres* lub jedną *rolę aplikacji*.
  - Zakresy są udostępniane przez interfejsy API sieci Web, które są wywoływane w imieniu użytkownika.
  - Role aplikacji są udostępniane przez interfejsy API sieci Web wywoływane przez aplikacje demona (które wywołuje internetowy interfejs API w ich imieniu).
- W przypadku tworzenia nowej rejestracji aplikacji internetowego interfejsu API wybierz [wersję tokenu dostępu](reference-app-manifest.md#accesstokenacceptedversion-attribute) zaakceptowaną przez internetowy interfejs API `2` . W przypadku starszych interfejsów API sieci Web zaakceptowana wersja tokenu może być `null` , ale ta wartość ogranicza odbiorców logowania tylko do organizacji, a osobiste konta Microsoft (MSA) nie będą obsługiwane.
- Konfiguracja kodu dla internetowego interfejsu API musi weryfikować token używany podczas wywoływania internetowego interfejsu API.
- Kod w akcjach kontrolera musi sprawdzać poprawność ról lub zakresów w tokenie.

## <a name="recommended-reading"></a>Zalecany odczyt

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu w tym scenariuszu — [Rejestracja aplikacji](scenario-protected-web-api-app-registration.md).
