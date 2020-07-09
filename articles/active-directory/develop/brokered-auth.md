---
title: Uwierzytelnianie obsługiwane przez brokera w systemie Android | Azure
titlesuffix: Microsoft identity platform
description: Omówienie uwierzytelniania obsługiwanego przez brokera & autoryzacji dla systemu Android na platformie tożsamości firmy Microsoft
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman, hahamil, brianmel
ms.openlocfilehash: a734589178438fd65d9a2d156fd91fc82807f578
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76697901"
---
# <a name="brokered-authentication-in-android"></a>Uwierzytelnianie obsługiwane przez brokera w systemie Android

Musisz użyć jednego z brokerów uwierzytelniania firmy Microsoft, aby wziąć udział w rejestracji jednokrotnej na całym urządzeniu (SSO) i spełnić zasady dostępu warunkowego do organizacji. Integracja z brokerem zapewnia następujące korzyści:

- Logowanie jednokrotne urządzenia
- Dostęp warunkowy dla:
  - Ochrona aplikacji usługi Intune
  - Rejestracja urządzenia (Workplace Join)
  - Zarządzanie urządzeniami przenośnymi
- Zarządzanie kontami w całym urządzeniu
  -  za pomocą konta Android AccountManager & ustawienia konta
  - "Konto służbowe" — typ konta niestandardowego

W systemie Android Broker uwierzytelniania firmy Microsoft jest składnikiem zawartym w [aplikacji Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) i [Intune — portal firmy](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)

> [!TIP]
> Tylko jedna aplikacja, która hostuje brokera, będzie aktywna jako Broker w danym momencie. Która aplikacja jest aktywna jako Broker jest określona przez kolejność instalacji na urządzeniu. Pierwszy, który ma zostać zainstalowany, lub ostatni obecny na urządzeniu, jest aktywnym brokerem.

Na poniższym diagramie przedstawiono relację między aplikacją, biblioteką uwierzytelniania firmy Microsoft (MSAL) i brokerami uwierzytelniania firmy Microsoft.

![Diagram wdrażania brokera](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>Instalowanie aplikacji obsługujących brokera

Aplikacje hostingowe brokera mogą być instalowane przez właściciela urządzenia ze sklepu z aplikacjami (zwykle Sklep Google Play) w dowolnym momencie. Niektóre interfejsy API (zasoby) są jednak chronione przez zasady dostępu warunkowego, które wymagają urządzeń:

- Zarejestrowane (dołączono do miejsca pracy) i/lub
- Zarejestrowano w zarządzaniu urządzeniami lub
- Zarejestrowane w Intune App Protection

Jeśli na urządzeniu nie ma jeszcze zainstalowanej aplikacji brokera, MSAL nakazuje użytkownikowi zainstalowanie go zaraz po próbie uzyskania tokenu interaktywnie. Następnie aplikacja będzie musiała prowadzić użytkownika przez kroki, aby zapewnić zgodność urządzenia z wymaganymi zasadami.

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>Efekty instalacji i dezinstalacji brokera

### <a name="when-a-broker-is-installed"></a>Po zainstalowaniu brokera

Gdy na urządzeniu jest zainstalowany Broker, wszystkie kolejne żądania tokenu interakcyjnego (wywołania do `acquireToken()` ) są obsługiwane przez brokera, a nie lokalnie przez MSAL. Wszystkie Stany logowania jednokrotnego, wcześniej dostępne dla MSAL, nie są dostępne dla brokera. W związku z tym użytkownik będzie musiał ponownie przeprowadzić uwierzytelnienie lub wybrać konto z istniejącej listy kont znanych urządzeniu.

Zainstalowanie brokera nie wymaga ponownego zalogowania użytkownika. Tylko wtedy, gdy użytkownik musi rozpoznać `MsalUiRequiredException` żądanie, zostanie przejdzie do brokera. `MsalUiRequiredException`jest zgłaszany z kilku powodów i musi zostać rozwiązany interaktywnie. Oto najczęstsze przyczyny:

- Użytkownik zmienił hasło skojarzone z kontem.
- Konto użytkownika nie spełnia już zasad dostępu warunkowego.
- Użytkownik odwołał swoją zgodę na skojarzenie aplikacji ze swoim kontem.

### <a name="when-a-broker-is-uninstalled"></a>Po odinstalowaniu brokera

Jeśli jest zainstalowana tylko jedna aplikacja hostingu brokera i zostanie usunięta, użytkownik będzie musiał zalogować się ponownie. Odinstalowanie aktywnego brokera spowoduje usunięcie konta i skojarzonych z nim tokenów.

Jeśli Intune — Portal firmy jest zainstalowana i działa jako aktywny Broker, a Microsoft Authenticator również jest zainstalowana, wówczas jeśli Intune — Portal firmy (Active Broker) zostanie odinstalowany, użytkownik będzie musiał zalogować się ponownie. Po ponownym zalogowaniu aplikacja Microsoft Authenticator przechodzi do aktywnego brokera.

## <a name="integrating-with-a-broker"></a>Integrowanie z brokerem

### <a name="generating-a-redirect-uri-for-a-broker"></a>Generowanie identyfikatora URI przekierowania dla brokera

Należy zarejestrować identyfikator URI przekierowania, który jest zgodny z brokerem. Identyfikator URI przekierowania dla brokera musi zawierać nazwę pakietu aplikacji, a także reprezentację sygnatury zakodowanej w formacie base64.

Format identyfikatora URI przekierowania to:`msauth://<yourpackagename>/<base64urlencodedsignature>`

Wygeneruj podpis kodowany przez adres URL w formacie base64 przy użyciu kluczy podpisywania aplikacji. Oto kilka przykładowych poleceń korzystających z kluczy podpisywania debugowania:

#### <a name="macos"></a>macOS

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

#### <a name="windows"></a>Windows

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Aby uzyskać informacje na temat podpisywania aplikacji, zobacz [Podpisz swoją aplikację](https://developer.android.com/studio/publish/app-signing) .

> [!IMPORTANT]
> Użyj Twojego produkcyjnego klucza podpisywania dla wersji produkcyjnej aplikacji.

### <a name="configure-msal-to-use-a-broker"></a>Konfigurowanie MSAL do korzystania z brokera

Aby korzystać z brokera w aplikacji, należy zaświadczać, że skonfigurowano przekierowanie brokera. Na przykład Uwzględnij identyfikator URI przekierowania z włączoną obsługą brokera--i wskaż, że został on zarejestrowany — przez uwzględnienie następujących danych w pliku konfiguracji MSAL:

```javascript
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

> [!TIP]
> Nowy interfejs użytkownika rejestracji aplikacji Azure Portal pomaga wygenerować identyfikator URI przekierowania brokera. Jeśli aplikacja została zarejestrowana przy użyciu starszego środowiska lub w portalu rejestracji aplikacji firmy Microsoft, może być konieczne wygenerowanie identyfikatora URI przekierowania i ręczne zaktualizowanie listy identyfikatorów URI przekierowania w portalu.

### <a name="broker-related-exceptions"></a>Wyjątki związane z brokerem

MSAL komunikuje się z brokerem na dwa sposoby:

- Usługa powiązana z brokerem
- Konto systemu Android

MSAL najpierw używa usługi powiązanej z brokerem, ponieważ wywołanie tej usługi nie wymaga żadnych uprawnień systemu Android. Jeśli wiązanie do powiązanej usługi zakończy się niepowodzeniem, MSAL będzie używać interfejsu API konta systemu Android. MSAL to zrobić tylko wtedy, gdy aplikacja ma już przyznane `"READ_CONTACTS"` uprawnienia.

Jeśli zostanie wyświetlony `MsalClientException` Kod błędu `"BROKER_BIND_FAILURE"` , dostępne są dwie opcje:

- Poproszenie użytkownika o wyłączenie optymalizacji zużycia baterii dla aplikacji Microsoft Authenticator i Intune — Portal firmy.
- Poproszenie użytkownika o przyznanie `"READ_CONTACTS"` uprawnienia
