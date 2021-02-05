---
title: Rejestrowanie aplikacji klasycznych, które wywołują interfejsy API sieci Web | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak utworzyć aplikację klasyczną wywołującą interfejsy API sieci Web (Rejestracja aplikacji)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 4031e43b3ec6f6f451fbc4888cc482249042690b
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582727"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>Aplikacja klasyczna, która wywołuje interfejsy API sieci Web: Rejestracja aplikacji

W tym artykule omówiono specyficzne dla rejestracji aplikacji dla aplikacji klasycznych.

## <a name="supported-account-types"></a>Obsługiwane typy kont

Typy kont obsługiwane w aplikacji klasycznej zależą od środowiska, które chcesz wyrównać. Z powodu tej relacji obsługiwane typy kont zależą od przepływów, które mają być używane.

### <a name="audience-for-interactive-token-acquisition"></a>Odbiorcy na potrzeby pozyskiwania tokenów interaktywnych

Jeśli aplikacja klasyczna korzysta z uwierzytelniania interakcyjnego, użytkownicy mogą logować się z dowolnego [typu konta](quickstart-register-app.md).

### <a name="audience-for-desktop-app-silent-flows"></a>Odbiorcy dla przepływów dyskretnych aplikacji klasycznych

- Aby można było używać zintegrowanego uwierzytelniania systemu Windows lub nazwy użytkownika i hasła, aplikacja musi zalogować użytkowników w Twojej dzierżawie, na przykład jeśli jesteś deweloperem biznesowym (LOB). Lub, w Azure Active Directory organizacji, aplikacja musi zalogować użytkowników w Twojej dzierżawie, jeśli jest to scenariusz niezależnego dostawcy oprogramowania. Te przepływy uwierzytelniania nie są obsługiwane w przypadku kont osobistych firmy Microsoft.
- Jeśli logujesz się do użytkowników przy użyciu tożsamości społecznościowych, które przechodzą przez Urząd i zasady firmy B2C (Business-to-Handle), możesz używać tylko uwierzytelniania interakcyjnego i nazwy użytkownika.

## <a name="redirect-uris"></a>Identyfikatory URI przekierowania

Identyfikatory URI przekierowania do użycia w aplikacji klasycznej zależą od przepływu, którego chcesz użyć.

- Jeśli używasz uwierzytelniania interakcyjnego lub przepływu kodu urządzenia, użyj `https://login.microsoftonline.com/common/oauth2/nativeclient` . Aby osiągnąć tę konfigurację, wybierz odpowiedni adres URL w sekcji **uwierzytelnianie** dla swojej aplikacji.

  > [!IMPORTANT]
  > Używanie `https://login.microsoftonline.com/common/oauth2/nativeclient` jako najlepszych rozwiązań w zakresie zabezpieczeń zaleca się użycie identyfikatora URI przekierowania.  Jeśli nie określono identyfikatora URI przekierowania, MSAL.NET `urn:ietf:wg:oauth:2.0:oob` domyślnie używa, które nie jest zalecane.  Ta wartość domyślna zostanie zaktualizowana jako istotna zmiana w następnej wersji.

- W przypadku tworzenia natywnej aplikacji "cel-C" lub "Swift" dla usługi macOS należy zarejestrować identyfikator URI przekierowania na podstawie identyfikatora pakietu aplikacji w następującym formacie: `msauth.<your.app.bundle.id>://auth` . Zastąp `<your.app.bundle.id>` identyfikatorem pakietu aplikacji.
- Jeśli aplikacja używa tylko zintegrowanego uwierzytelniania systemu Windows lub nazwy użytkownika i hasła, nie trzeba rejestrować identyfikatora URI przekierowania dla aplikacji. Te przepływy umożliwiają przeprowadzenie rundy w punkcie końcowym Microsoft Identity platform v 2.0. Aplikacja nie zostanie wywołana ponownie na żadnym konkretnym identyfikatorze URI.
- Aby rozróżnić [przepływ kodu urządzenia](scenario-desktop-acquire-token.md#device-code-flow), [zintegrowane uwierzytelnianie systemu Windows](scenario-desktop-acquire-token.md#integrated-windows-authentication)oraz [nazwę użytkownika i hasło](scenario-desktop-acquire-token.md#username-and-password) z poufnej aplikacji klienckiej przy użyciu przepływu poświadczeń klienta używanego w [aplikacjach demonów](scenario-daemon-overview.md), żadna z tych elementów nie wymaga identyfikatora URI przekierowania, skonfiguruj ją jako publiczną aplikację kliencką. Aby osiągnąć tę konfigurację:

    1. W <a href="https://portal.azure.com/" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> Azure Portal</a>wybierz aplikację w **rejestracje aplikacji**, a następnie wybierz pozycję **uwierzytelnianie**.
    1. W obszarze **Zaawansowane ustawienia**  >  **Zezwalaj na przepływy klientów publicznych**  >  **Włącz następujące przepływy mobilne i klasyczne:** wybierz pozycję **tak**.

        :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Włącz publiczne ustawienie klienta w okienku uwierzytelnianie w Azure Portal":::

## <a name="api-permissions"></a>Uprawnienia aplikacji

Aplikacje klasyczne wywoływanie interfejsów API dla zalogowanego użytkownika. Muszą oni zażądać uprawnień delegowanych. Nie mogą żądać uprawnień aplikacji, które są obsługiwane tylko w [aplikacjach demona](scenario-daemon-overview.md).

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu w tym scenariuszu — [Konfiguracja kodu aplikacji](scenario-desktop-app-configuration.md).
