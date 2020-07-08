---
title: Zarejestruj aplikację sieci Web, która wywołuje interfejsy API sieci Web — Microsoft Identity platform | Azure
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
ms.openlocfilehash: 8cb7d86bd419563363779c499962c81f0c59e3b6
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80881880"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>Aplikacja sieci Web, która wywołuje interfejsy API sieci Web: Rejestracja aplikacji

Aplikacja sieci Web, która wywołuje interfejsy API sieci Web, ma taką samą rejestrację jak aplikacja sieci Web, która podpisuje użytkowników w programie. Tak więc postępuj zgodnie z instrukcjami w [aplikacji sieci Web, które logują się w użytkownikach: rejestrowanie aplikacji](scenario-web-app-sign-user-app-registration.md).

Jednak ponieważ aplikacja sieci Web teraz wywołuje również interfejsy API sieci Web, jest to poufna aplikacja kliencka. Dlatego jest wymagana dodatkowa Rejestracja. Aplikacja musi udostępniać poświadczenia klienta lub wpisy *tajne*przy użyciu platformy tożsamości firmy Microsoft.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>Uprawnienia aplikacji

Interfejsy API wywołań aplikacji sieci Web w imieniu zalogowanego użytkownika. Aby to zrobić, muszą one żądać *delegowania uprawnień*. Aby uzyskać szczegółowe informacje, zobacz [Dodawanie uprawnień dostępu do interfejsów API sieci Web](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Aplikacja sieci Web, która wywołuje interfejsy API sieci Web: Konfiguracja kodu](scenario-web-app-call-api-app-configuration.md)
