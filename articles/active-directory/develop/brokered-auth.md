---
title: Uwierzytelnianie obsługiwane przez brokera w systemie Android | Azure
titleSuffix: Microsoft identity platform
description: Omówienie uwierzytelniania obsługiwanego przez brokera & autoryzacji dla systemu Android na platformie tożsamości firmy Microsoft
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2020
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman, hahamil, brianmel
ms.openlocfilehash: 5042bfad2cfe06c7c368c6b476aa1b02d67bcc9c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91760758"
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

W systemie Android Broker uwierzytelniania firmy Microsoft jest składnikiem zawartym w [Microsoft Authenticator aplikacji](https://play.google.com/store/apps/details?id=com.azure.authenticator) i [Intune — portal firmy](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal).

Na poniższym diagramie przedstawiono relację między aplikacją, biblioteką uwierzytelniania firmy Microsoft (MSAL) i brokerami uwierzytelniania firmy Microsoft.

![Diagram przedstawiający sposób odnoszący się do MSAL, aplikacji brokera i Menedżera kont systemu Android.](./media/brokered-auth/brokered-deployment-diagram.png)

## <a name="installing-apps-that-host-a-broker"></a>Instalowanie aplikacji obsługujących brokera

Aplikacje hostingowe brokera mogą być instalowane przez właściciela urządzenia ze sklepu z aplikacjami (zwykle Sklep Google Play) w dowolnym momencie. Niektóre interfejsy API (zasoby) są jednak chronione przez zasady dostępu warunkowego, które wymagają urządzeń:

- Zarejestrowane (dołączono do miejsca pracy) i/lub
- Zarejestrowano w zarządzaniu urządzeniami lub
- Zarejestrowane w Intune App Protection

Jeśli na urządzeniu nie ma jeszcze zainstalowanej aplikacji brokera, MSAL nakazuje użytkownikowi zainstalowanie go zaraz po próbie uzyskania tokenu interaktywnie. Następnie aplikacja będzie musiała prowadzić użytkownika przez kroki, aby zapewnić zgodność urządzenia z wymaganymi zasadami.

## <a name="effects-of-installing-and-uninstalling-a-broker"></a>Efekty instalacji i dezinstalacji brokera

### <a name="when-a-broker-is-installed"></a>Po zainstalowaniu brokera

Gdy na urządzeniu jest zainstalowany Broker, wszystkie kolejne żądania tokenu interakcyjnego (wywołania do `acquireToken()` ) są obsługiwane przez brokera, a nie lokalnie przez MSAL. Wszystkie Stany logowania jednokrotnego, wcześniej dostępne dla MSAL, nie są dostępne dla brokera. W związku z tym użytkownik będzie musiał ponownie przeprowadzić uwierzytelnienie lub wybrać konto z istniejącej listy kont znanych urządzeniu.

Zainstalowanie brokera nie wymaga ponownego zalogowania użytkownika. Tylko wtedy, gdy użytkownik musi rozpoznać `MsalUiRequiredException` żądanie, zostanie przejdzie do brokera. `MsalUiRequiredException` mogą być zgłaszane z kilku powodów i muszą zostać rozwiązane interaktywnie. Na przykład:

- Użytkownik zmienił hasło skojarzone z kontem.
- Konto użytkownika nie spełnia już zasad dostępu warunkowego.
- Użytkownik odwołał swoją zgodę na skojarzenie aplikacji ze swoim kontem.

#### <a name="multiple-brokers"></a>Wiele brokerów

Jeśli na urządzeniu zainstalowano wiele brokerów, najpierw jest on zawsze aktywnym brokerem. Tylko jeden Broker może być aktywny na urządzeniu.

### <a name="when-a-broker-is-uninstalled"></a>Po odinstalowaniu brokera

Jeśli jest zainstalowana tylko jedna aplikacja hostingu brokera i zostanie usunięta, użytkownik będzie musiał zalogować się ponownie. Odinstalowanie aktywnego brokera spowoduje usunięcie konta i skojarzonych z nim tokenów.

Jeśli Intune — Portal firmy jest zainstalowana i działa jako aktywny Broker, a Microsoft Authenticator również jest zainstalowana, wówczas jeśli Intune — Portal firmy (Active Broker) zostanie odinstalowany, użytkownik będzie musiał zalogować się ponownie. Po ponownym zalogowaniu aplikacja Microsoft Authenticator przechodzi do aktywnego brokera.

## <a name="integrating-with-a-broker"></a>Integrowanie z brokerem

### <a name="generating-a-redirect-uri-for-a-broker"></a>Generowanie identyfikatora URI przekierowania dla brokera

Należy zarejestrować identyfikator URI przekierowania, który jest zgodny z brokerem. Identyfikator URI przekierowania dla brokera powinien zawierać nazwę pakietu aplikacji i reprezentację w formacie base64 aplikacji.

Format identyfikatora URI przekierowania to: `msauth://<yourpackagename>/<base64urlencodedsignature>`

Za pomocą [Narzędzia](https://manpages.debian.org/buster/openjdk-11-jre-headless/keytool.1.en.html) klucza można wygenerować skrót sygnatury kodowany algorytmem Base64 przy użyciu kluczy podpisywania aplikacji, a następnie użyć Azure Portal do WYGENEROWANIA identyfikatora URI przekierowania przy użyciu tego skrótu.

Linux i macOS:

```bash
keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
```

W systemie Windows:

```powershell
keytool -exportcert -alias androiddebugkey -keystore %HOMEPATH%\.android\debug.keystore | openssl sha1 -binary | openssl base64
```

Po wygenerowaniu skrótu podpisu za pomocą *Narzędzia*klucza Użyj Azure Portal, aby wygenerować identyfikator URI przekierowania:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i wybierz swoją aplikację dla systemu Android w **rejestracje aplikacji**.
1. Wybierz pozycję **uwierzytelnianie**  >  **Dodaj platformę**  >  **Android**.
1. W otwartym okienku **Konfiguracja aplikacji systemu Android** wprowadź **skrót sygnatury** wygenerowany wcześniej i **nazwę pakietu**.
1. Wybierz przycisk **Konfiguruj** .

Azure Portal generuje identyfikator URI przekierowania dla Ciebie i wyświetla go w polu **URI przekierowania** okienka **konfiguracji systemu Android** .

Aby uzyskać więcej informacji na temat podpisywania aplikacji, zobacz [Podpisz swoją aplikację](https://developer.android.com/studio/publish/app-signing) w podręczniku użytkownika Android Studio.

> [!IMPORTANT]
> Użyj Twojego produkcyjnego klucza podpisywania dla wersji produkcyjnej aplikacji.

### <a name="configure-msal-to-use-a-broker"></a>Konfigurowanie MSAL do korzystania z brokera

Aby korzystać z brokera w aplikacji, należy zaświadczać, że skonfigurowano przekierowanie brokera. Na przykład Uwzględnij identyfikator URI przekierowania z włączoną obsługą brokera i wskaż, że został on zarejestrowany przez dołączenie następujących ustawień w pliku konfiguracji MSAL:

```json
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

### <a name="broker-related-exceptions"></a>Wyjątki związane z brokerem

MSAL komunikuje się z brokerem na dwa sposoby:

- Usługa powiązana z brokerem
- Konto systemu Android

MSAL najpierw używa usługi powiązanej z brokerem, ponieważ wywołanie tej usługi nie wymaga żadnych uprawnień systemu Android. Jeśli wiązanie do powiązanej usługi zakończy się niepowodzeniem, MSAL będzie używać interfejsu API konta systemu Android. MSAL tylko wtedy, gdy Twoja aplikacja ma już przyznane `"READ_CONTACTS"` uprawnienia.

Jeśli zostanie wyświetlony `MsalClientException` Kod błędu `"BROKER_BIND_FAILURE"` , dostępne są dwie opcje:

- Poproszenie użytkownika o wyłączenie optymalizacji zużycia baterii dla aplikacji Microsoft Authenticator i Intune — Portal firmy.
- Poproszenie użytkownika o przyznanie `"READ_CONTACTS"` uprawnienia

## <a name="verifying-broker-integration"></a>Weryfikowanie integracji brokera

Może nie być od razu jasne, że integracja z brokerem działa, ale można wykonać następujące czynności, aby sprawdzić:

1. Na urządzeniu z systemem Android Ukończ żądanie przy użyciu brokera.
1. W ustawieniach na urządzeniu z systemem Android Wyszukaj nowo utworzone konto odpowiadające kontu uwierzytelnionemu przez użytkownika. Konto powinno być kontem typu *służbowego*.

Jeśli chcesz powtórzyć test, możesz usunąć konto z ustawień.

## <a name="next-steps"></a>Następne kroki

[Tryb udostępnionego urządzenia dla urządzeń z systemem Android](msal-android-shared-devices.md) umożliwia skonfigurowanie urządzenia z systemem Android w taki sposób, aby można je było łatwo udostępnić wielu pracownikom.
