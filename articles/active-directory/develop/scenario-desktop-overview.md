---
title: Tworzenie aplikacji klasycznej, która wywołuje interfejsy API sieci Web | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć aplikację klasyczną wywołującą interfejsy API sieci Web (omówienie)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: ea6ecf456bbcea01bf4c1eef5377d918bf0918fd
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104798956"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Scenariusz: aplikacja klasyczna, która wywołuje interfejsy API sieci Web

Dowiedz się wszystkiego, co jest potrzebne do utworzenia aplikacji klasycznej, która wywołuje interfejsy API sieci Web.

## <a name="get-started"></a>Rozpoczęcie pracy

Jeśli jeszcze tego nie zrobiono, Utwórz swoją pierwszą aplikację, wykonując Przewodnik Szybki Start:

- [Szybki start: uzyskiwanie tokenu i wywoływanie interfejsu API programu Microsoft Graph z poziomu aplikacji klasycznej w systemie Windows](./quickstart-v2-windows-desktop.md)
- [Szybki Start: uzyskiwanie tokenu i wywoływanie Microsoft Graph interfejsu API z poziomu aplikacji platformy UWP](./quickstart-v2-uwp.md)
- [Szybki Start: uzyskiwanie tokenu i wywoływanie Microsoft Graph interfejsu API z natywnej aplikacji macOS](./quickstart-v2-ios.md)
- [Szybki Start: uzyskiwanie tokenu i wywoływanie Microsoft Graph interfejsu API z aplikacji Node.js & elektronów](./quickstart-v2-nodejs-desktop.md)

## <a name="overview"></a>Omówienie

Napisz aplikację klasyczną i chcesz zalogować użytkowników do aplikacji i wywoływać interfejsy API sieci Web, takie jak Microsoft Graph, inne interfejsy API firmy Microsoft lub własny internetowy interfejs API. Istnieje kilka rozwiązań:

- Możesz użyć pozyskiwania tokenów interaktywnych:

  - Jeśli aplikacja klasyczna obsługuje kontrolki graficzne, na przykład jeśli jest to aplikacja Windows. form, aplikacja WPF lub aplikacja natywna macOS.
  - Lub, jeśli jest to aplikacja .NET Core i wyrażasz zgodę na interakcję uwierzytelniania z usługą Azure Active Directory (Azure AD) w przeglądarce systemowej.
  - Lub, jeśli jest to Node.jsa aplikacja elektronowa, która jest uruchamiana w wystąpieniu chromu.

- W przypadku aplikacji hostowanych w systemie Windows jest również możliwe, aby aplikacje działające na komputerach przyłączonych do domeny systemu Windows lub z usługą Azure AD przyłączone w trybie dyskretnym przy użyciu zintegrowanego uwierzytelniania systemu Windows.
- Na koniec, chociaż nie jest to zalecane, możesz użyć nazwy użytkownika i hasła w publicznych aplikacjach klienckich. Nadal jest to konieczne w niektórych scenariuszach, takich jak DevOps. Użycie nakłada ograniczenia dotyczące aplikacji. Na przykład nie może zalogować użytkownika, który musi przeprowadzić [uwierzytelnianie wieloskładnikowe](../authentication/concept-mfa-howitworks.md) (dostęp warunkowy). Ponadto aplikacja nie będzie korzystać z logowania jednokrotnego (SSO).

  Jest to również sprzeczne z zasadami nowoczesnego uwierzytelniania i są dostępne tylko dla starszych przyczyn.

  ![Aplikacja klasyczna](media/scenarios/desktop-app.svg)

- Jeśli nastąpi zapisanie przenośnego narzędzia wiersza polecenia, prawdopodobnie aplikacji .NET Core działającej w systemie Linux lub Mac, a po zaakceptowaniu tego uwierzytelniania zostanie przekazane do przeglądarki systemowej, można użyć uwierzytelniania interakcyjnego. Platforma .NET Core nie udostępnia [przeglądarki sieci Web](https://aka.ms/msal-net-uses-web-browser), więc uwierzytelnianie odbywa się w przeglądarce systemowej. W przeciwnym razie najlepszą opcją w tym przypadku jest użycie przepływu kodu urządzenia. Ten przepływ jest również używany w przypadku aplikacji bez przeglądarki, takich jak aplikacje IoT.

  ![Aplikacja bezprzeglądarki](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Szczegółowych informacji

Aplikacje klasyczne mają pewne różne wartości. Są one zależne od tego, czy aplikacja korzysta z uwierzytelniania interaktywnego, czy nie.

## <a name="recommended-reading"></a>Zalecany odczyt

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu w tym scenariuszu — [Rejestracja aplikacji](scenario-desktop-app-registration.md).
