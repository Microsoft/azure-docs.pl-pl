---
title: Zarejestruj aplikację sieci Web, która wywołuje interfejsy API sieci Web | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak zarejestrować aplikację sieci Web, która wywołuje interfejsy API sieci Web
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
ms.openlocfilehash: bb9a1ca6c2c81e3b0d5dbeff06f4de012446cf79
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98756317"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>Aplikacja sieci Web, która wywołuje interfejsy API sieci Web: Rejestracja aplikacji

Aplikacja sieci Web, która wywołuje interfejsy API sieci Web, ma taką samą rejestrację jak aplikacja sieci Web, która podpisuje użytkowników w programie. Tak więc postępuj zgodnie z instrukcjami w [aplikacji sieci Web, które logują się w użytkownikach: rejestrowanie aplikacji](scenario-web-app-sign-user-app-registration.md).

Jednak ponieważ aplikacja sieci Web teraz wywołuje również interfejsy API sieci Web, jest to poufna aplikacja kliencka. Dlatego jest wymagana dodatkowa Rejestracja. Aplikacja musi udostępniać poświadczenia klienta lub wpisy *tajne* przy użyciu platformy tożsamości firmy Microsoft.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Uprawnienia aplikacji

Interfejsy API wywołań aplikacji sieci Web w imieniu zalogowanego użytkownika. Aby to zrobić, muszą one żądać *delegowania uprawnień*. Aby uzyskać szczegółowe informacje, zobacz [Dodawanie uprawnień w celu uzyskania dostępu do internetowego interfejsu API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api).

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu w tym scenariuszu, [Konfiguracja kodu](scenario-web-app-call-api-app-configuration.md).
