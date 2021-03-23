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
ms.openlocfilehash: 263397aa2cd09ba24fa750131b76047801869a65
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104798939"
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

Określ identyfikator URI przekierowania dla aplikacji, [konfigurując ustawienia platformy](quickstart-register-app.md#add-a-redirect-uri) dla aplikacji w **rejestracje aplikacji** w Azure Portal.

- W przypadku aplikacji korzystających z uwierzytelniania interaktywnego:
  - Aplikacje korzystające z osadzonych przeglądarek: `https://login.microsoftonline.com/common/oauth2/nativeclient`
  - Aplikacje korzystające z przeglądarek systemu: `http://localhost`

  > [!IMPORTANT]
  > Ze względów bezpieczeństwa zaleca się jawne ustawienie `https://login.microsoftonline.com/common/oauth2/nativeclient` lub `http://localhost` jako identyfikator URI przekierowania. Niektóre biblioteki uwierzytelniania, takie jak MSAL.NET, używają wartości domyślnej `urn:ietf:wg:oauth:2.0:oob` , gdy nie określono innego identyfikatora URI przekierowania, co nie jest zalecane. Ta wartość domyślna zostanie zaktualizowana jako istotna zmiana w następnej wersji.

- W przypadku tworzenia natywnej aplikacji "cel-C" lub "Swift" dla usługi macOS należy zarejestrować identyfikator URI przekierowania na podstawie identyfikatora pakietu aplikacji w następującym formacie: `msauth.<your.app.bundle.id>://auth` . Zastąp `<your.app.bundle.id>` identyfikatorem pakietu aplikacji.
- Jeśli tworzysz aplikację Node.js elektronów, Użyj niestandardowego protokołu plików zamiast regularnego identyfikatora URI przekierowania w sieci Web (https://) w celu obsługi kroku przekierowania przepływu autoryzacji, na przykład `msal://redirect` . Niestandardowa nazwa protokołu plików nie powinna być oczywista i powinna być zgodna z sugestiami w [specyfikacji OAuth 2.0 dla aplikacji natywnych](https://tools.ietf.org/html/rfc8252#section-7.1).
- Jeśli aplikacja używa tylko zintegrowanego uwierzytelniania systemu Windows lub nazwy użytkownika i hasła, nie trzeba rejestrować identyfikatora URI przekierowania dla aplikacji. Te przepływy umożliwiają przeprowadzenie rundy w punkcie końcowym Microsoft Identity platform v 2.0. Aplikacja nie zostanie wywołana ponownie na żadnym konkretnym identyfikatorze URI.
- Aby rozróżnić [przepływ kodu urządzenia](scenario-desktop-acquire-token.md#device-code-flow), [zintegrowane uwierzytelnianie systemu Windows](scenario-desktop-acquire-token.md#integrated-windows-authentication)oraz [nazwę użytkownika i hasło](scenario-desktop-acquire-token.md#username-and-password) z poufnej aplikacji klienckiej przy użyciu przepływu poświadczeń klienta używanego w [aplikacjach demonów](scenario-daemon-overview.md), żadna z tych elementów nie wymaga identyfikatora URI przekierowania, skonfiguruj ją jako publiczną aplikację kliencką. Aby osiągnąć tę konfigurację:

    1. W <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>wybierz aplikację w **rejestracje aplikacji**, a następnie wybierz pozycję **uwierzytelnianie**.
    1. W obszarze **Zaawansowane ustawienia**  >  **Zezwalaj na przepływy klientów publicznych**  >  **Włącz następujące przepływy mobilne i klasyczne:** wybierz pozycję **tak**.

        :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Włącz publiczne ustawienie klienta w okienku uwierzytelnianie w Azure Portal":::

## <a name="api-permissions"></a>Uprawnienia aplikacji

Aplikacje klasyczne wywoływanie interfejsów API dla zalogowanego użytkownika. Muszą oni zażądać uprawnień delegowanych. Nie mogą żądać uprawnień aplikacji, które są obsługiwane tylko w [aplikacjach demona](scenario-daemon-overview.md).

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu w tym scenariuszu — [Konfiguracja kodu aplikacji](scenario-desktop-app-configuration.md).
