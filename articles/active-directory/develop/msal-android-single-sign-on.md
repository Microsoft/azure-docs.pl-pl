---
title: Jak włączyć logowanie jednokrotne dla wielu aplikacji w systemie Android przy użyciu usługi MSAL | Azure
titleSuffix: Microsoft identity platform
description: Jak używać biblioteki Microsoft Authentication Library (MSAL) dla systemu Android, aby umożliwić Logowanie jednokrotne w aplikacjach.
services: active-directory
author: hamiltonha
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: how-to
ms.date: 10/15/2020
ms.author: hahamil
ms.reviewer: marsma
ms.openlocfilehash: f3d4ec8db89e9bebfdcd594e842a6c19d3d66d54
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100104097"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-msal"></a>Instrukcje: Włączanie logowania jednokrotnego dla aplikacji w systemie Android przy użyciu MSAL

Logowanie jednokrotne (SSO) umożliwia użytkownikom wprowadzanie poświadczeń tylko raz i ich automatyczne działanie w wielu aplikacjach.

[Platforma tożsamości firmy Microsoft](./index.yml) i Biblioteka Microsoft Authentication Library (MSAL) umożliwiają włączenie logowania jednokrotnego w ramach własnego pakietu aplikacji. Za pomocą funkcji brokera i aplikacji uwierzytelniania można rozłożyć Logowanie jednokrotne za pośrednictwem całego urządzenia.

W tym instruktażu dowiesz się, jak skonfigurować zestawy SDK używane przez aplikację w celu zapewnienia logowania jednokrotnego do klientów.

## <a name="prerequisites"></a>Wymagania wstępne

Ta procedura polega na tym, że wiesz, jak:

- Inicjowanie obsługi administracyjnej aplikacji przy użyciu Azure Portal. Aby uzyskać więcej informacji na temat tego tematu, zobacz instrukcje dotyczące tworzenia aplikacji w [samouczku systemu Android](./tutorial-v2-android.md#create-a-project)
- Zintegruj swoją aplikację z [biblioteką uwierzytelniania firmy Microsoft dla systemu Android](https://github.com/AzureAD/microsoft-authentication-library-for-android).

## <a name="methods-for-single-sign-on"></a>Metody logowania jednokrotnego

Istnieją dwa sposoby dla aplikacji korzystających z MSAL dla systemu Android w celu uzyskania rejestracji jednokrotnej:

* Za pośrednictwem [aplikacji brokera](#sso-through-brokered-authentication)
* Za pomocą [przeglądarki systemu](#sso-through-system-browser)


   Zalecane jest używanie aplikacji brokera do świadczeń, takich jak logowanie jednokrotne, zarządzanie kontami i dostęp warunkowy na całej urządzeniu. Jednak wymaga to od użytkowników pobrania dodatkowych aplikacji.

## <a name="sso-through-brokered-authentication"></a>Logowanie jednokrotne za pośrednictwem uwierzytelniania przez brokera

Zalecamy użycie jednego z brokerów uwierzytelniania firmy Microsoft do wzięcia udziału w usłudze logowania jednokrotnego (SSO) na całym urządzeniu oraz w celu spełnienia zasad dostępu warunkowego do organizacji. Integracja z brokerem zapewnia następujące korzyści:

- Logowanie jednokrotne urządzenia
- Dostęp warunkowy dla:
  - Ochrona aplikacji usługi Intune
  - Rejestracja urządzenia (Workplace Join)
  - Zarządzanie urządzeniami przenośnymi
- Zarządzanie kontami w całym urządzeniu
  -  za pomocą konta Android AccountManager & ustawienia konta
  - "Konto służbowe" — typ konta niestandardowego

W systemie Android brokera uwierzytelniania firmy Microsoft jest składnikiem zawartym w [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) i [Intune — portal firmy](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) aplikacji.

Na poniższym diagramie przedstawiono relację między aplikacją, biblioteką uwierzytelniania firmy Microsoft (MSAL) i brokerami uwierzytelniania firmy Microsoft.

![Diagram przedstawiający sposób odnoszący się do MSAL, aplikacji brokera i Menedżera kont systemu Android.](./media/brokered-auth/brokered-deployment-diagram.png)

### <a name="installing-apps-that-host-a-broker"></a>Instalowanie aplikacji obsługujących brokera

Aplikacje hostingowe brokera mogą być instalowane przez właściciela urządzenia ze sklepu z aplikacjami (zwykle Sklep Google Play) w dowolnym momencie. Niektóre interfejsy API (zasoby) są jednak chronione przez zasady dostępu warunkowego, które wymagają urządzeń:

- Zarejestrowane (dołączono do miejsca pracy) i/lub
- Zarejestrowano w zarządzaniu urządzeniami lub
- Zarejestrowane w Intune App Protection

Jeśli na urządzeniu nie ma jeszcze zainstalowanej aplikacji brokera, MSAL nakazuje użytkownikowi zainstalowanie go zaraz po próbie uzyskania tokenu interaktywnie. Następnie aplikacja będzie musiała prowadzić użytkownika przez kroki, aby zapewnić zgodność urządzenia z wymaganymi zasadami.

### <a name="effects-of-installing-and-uninstalling-a-broker"></a>Efekty instalacji i dezinstalacji brokera

#### <a name="when-a-broker-is-installed"></a>Po zainstalowaniu brokera

Gdy na urządzeniu jest zainstalowany Broker, wszystkie kolejne żądania tokenu interakcyjnego (wywołania do `acquireToken()` ) są obsługiwane przez brokera, a nie lokalnie przez MSAL. Wszystkie Stany logowania jednokrotnego, wcześniej dostępne dla MSAL, nie są dostępne dla brokera. W związku z tym użytkownik będzie musiał ponownie przeprowadzić uwierzytelnienie lub wybrać konto z istniejącej listy kont znanych urządzeniu.

Zainstalowanie brokera nie wymaga ponownego zalogowania użytkownika. Tylko wtedy, gdy użytkownik musi rozpoznać `MsalUiRequiredException` żądanie, zostanie przejdzie do brokera. `MsalUiRequiredException` mogą być zgłaszane z kilku powodów i muszą zostać rozwiązane interaktywnie. Na przykład:

- Użytkownik zmienił hasło skojarzone z kontem.
- Konto użytkownika nie spełnia już zasad dostępu warunkowego.
- Użytkownik odwołał swoją zgodę na skojarzenie aplikacji ze swoim kontem.

**Wiele brokerów** — Jeśli na urządzeniu zainstalowano wiele brokerów, najpierw jest on zawsze aktywnym brokerem. Tylko jeden Broker może być aktywny na urządzeniu.

#### <a name="when-a-broker-is-uninstalled"></a>Po odinstalowaniu brokera

Jeśli jest zainstalowana tylko jedna aplikacja hostingu brokera i zostanie usunięta, użytkownik będzie musiał zalogować się ponownie. Odinstalowanie aktywnego brokera spowoduje usunięcie konta i skojarzonych z nim tokenów.

Jeśli Intune — Portal firmy jest zainstalowana i działa jako aktywny Broker, a Microsoft Authenticator również jest zainstalowana, wówczas jeśli Intune — Portal firmy (Active Broker) zostanie odinstalowany, użytkownik będzie musiał zalogować się ponownie. Po ponownym zalogowaniu aplikacja Microsoft Authenticator przechodzi do aktywnego brokera.

### <a name="integrating-with-a-broker"></a>Integrowanie z brokerem

#### <a name="generate-a-redirect-uri-for-a-broker"></a>Generowanie identyfikatora URI przekierowania dla brokera

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

Po wygenerowaniu skrótu podpisu za pomocą *Narzędzia* klucza Użyj Azure Portal, aby wygenerować identyfikator URI przekierowania:

1. Zaloguj się do <a href="https://portal.azure.com/" target="_blank">Azure Portal</a> i wybierz swoją aplikację dla systemu Android w **rejestracje aplikacji**.
1. Wybierz pozycję **uwierzytelnianie**  >  **Dodaj platformę**  >  **Android**.
1. W otwartym okienku **Konfiguracja aplikacji systemu Android** wprowadź **skrót sygnatury** wygenerowany wcześniej i **nazwę pakietu**.
1. Wybierz przycisk **Konfiguruj** .

Azure Portal generuje identyfikator URI przekierowania dla Ciebie i wyświetla go w polu **URI przekierowania** okienka **konfiguracji systemu Android** .

Aby uzyskać więcej informacji na temat podpisywania aplikacji, zobacz [Podpisz swoją aplikację](https://developer.android.com/studio/publish/app-signing) w podręczniku użytkownika Android Studio.

> [!IMPORTANT]
> Użyj Twojego produkcyjnego klucza podpisywania dla wersji produkcyjnej aplikacji.

#### <a name="configure-msal-to-use-a-broker"></a>Konfigurowanie MSAL do korzystania z brokera

Aby korzystać z brokera w aplikacji, należy zaświadczać, że skonfigurowano przekierowanie brokera. Na przykład Uwzględnij identyfikator URI przekierowania z włączoną obsługą brokera i wskaż, że został on zarejestrowany przez dołączenie następujących ustawień w pliku konfiguracji MSAL:

```json
"redirect_uri" : "<yourbrokerredirecturi>",
"broker_redirect_uri_registered": true
```

#### <a name="broker-related-exceptions"></a>Wyjątki związane z brokerem

MSAL komunikuje się z brokerem na dwa sposoby:

- Usługa powiązana z brokerem
- Konto systemu Android

MSAL najpierw używa usługi powiązanej z brokerem, ponieważ wywołanie tej usługi nie wymaga żadnych uprawnień systemu Android. Jeśli wiązanie do powiązanej usługi zakończy się niepowodzeniem, MSAL będzie używać interfejsu API konta systemu Android. MSAL tylko wtedy, gdy Twoja aplikacja ma już przyznane `"READ_CONTACTS"` uprawnienia.

Jeśli zostanie wyświetlony `MsalClientException` Kod błędu `"BROKER_BIND_FAILURE"` , dostępne są dwie opcje:

- Poproszenie użytkownika o wyłączenie optymalizacji zużycia baterii dla aplikacji Microsoft Authenticator i Intune — Portal firmy.
- Poproszenie użytkownika o przyznanie `"READ_CONTACTS"` uprawnienia

### <a name="verify-broker-integration"></a>Weryfikuj integrację brokera

Może nie być od razu jasne, że integracja z brokerem działa, ale można wykonać następujące czynności, aby sprawdzić:

1. Na urządzeniu z systemem Android Ukończ żądanie przy użyciu brokera.
1. W ustawieniach na urządzeniu z systemem Android Wyszukaj nowo utworzone konto odpowiadające kontu uwierzytelnionemu przez użytkownika. Konto powinno być kontem typu *służbowego*.

Jeśli chcesz powtórzyć test, możesz usunąć konto z ustawień.

## <a name="sso-through-system-browser"></a>Logowanie jednokrotne za pomocą przeglądarki systemu

Aplikacje dla systemu Android mogą korzystać z niestandardowych kart programu WebView, przeglądarki systemowej lub Chrome na potrzeby obsługi użytkowników uwierzytelniania. Jeśli aplikacja nie korzysta z uwierzytelniania obsługiwanego przez brokera, w celu uzyskania rejestracji jednokrotnej należy użyć przeglądarki systemowej, a nie natywnego widoku WebView.

### <a name="authorization-agents"></a>Agenci autoryzacji

Wybór konkretnej strategii dla agentów autoryzacji jest opcjonalny i reprezentuje dodatkowe funkcje, które można dostosować. Większość aplikacji będzie używać ustawień domyślnych MSAL (zobacz [Opis pliku konfiguracji systemu Android MSAL](msal-configuration.md) , aby zobaczyć różne ustawienia domyślne).

MSAL obsługuje autoryzację przy użyciu programu `WebView` lub przeglądarki systemowej.  Na poniższym obrazie pokazano, w jaki sposób wygląda korzystanie z programu `WebView` lub przeglądarki systemu z CustomTabs lub bez CustomTabs:

![Przykłady logowania MSAL](./media/authorization-agents/sign-in-ui.jpg)

### <a name="single-sign-on-implications"></a>Konsekwencje logowania jednokrotnego

Domyślnie aplikacje zintegrowane z usługą MSAL używają niestandardowych kart przeglądarki systemu do autoryzacji. W przeciwieństwie do widoków sieci Web, karty niestandardowe udostępniają plik cookie jar z domyślną przeglądarką systemową, co pozwala na mniejszą liczbę logowań z aplikacjami internetowymi lub innymi natywnymi, które zostały zintegrowane z kartami niestandardowymi.

Jeśli aplikacja korzysta z `WebView` strategii bez integrowania pomocy technicznej Microsoft Authenticator lub Portal firmy do swojej aplikacji, użytkownicy nie będą mogli korzystać z funkcji logowania jednokrotnego na urządzeniu ani między natywnymi aplikacjami i aplikacjami sieci Web.

Jeśli aplikacja używa MSAL z brokerem, takim jak Microsoft Authenticator lub Intune — Portal firmy, użytkownicy mogą korzystać z logowania jednokrotnego w aplikacjach, jeśli mają aktywne Logowanie przy użyciu jednej z aplikacji.

### <a name="webview"></a>WebView

Aby użyć widoku webapp w aplikacji, należy umieścić następujący wiersz w pliku JSON konfiguracji aplikacji, który jest przesyłany do MSAL:

```json
"authorization_user_agent" : "WEBVIEW"
```

W przypadku używania w aplikacji `WebView` użytkownik loguje się bezpośrednio do aplikacji. Tokeny są przechowywane w piaskownicy aplikacji i nie są dostępne poza plikiem jar plików cookie aplikacji. W związku z tym użytkownik nie może korzystać z logowania jednokrotnego w aplikacjach, chyba że aplikacje integrują się z wystawcą uwierzytelnienia lub Portal firmy.

Program `WebView` udostępnia jednak możliwość dostosowania wyglądu i sposobu działania interfejsu użytkownika logowania. Aby dowiedzieć się więcej o tym, jak to zrobić, zobacz temat [WebViews dla systemu Android](https://developer.android.com/reference/android/webkit/WebView) .

### <a name="default-browser-plus-custom-tabs"></a>Domyślna przeglądarka i karty niestandardowe

Domyślnie MSAL używa przeglądarki i strategii [kart niestandardowych](https://developer.chrome.com/multidevice/android/customtabs) . Można jawnie wskazać tę strategię, aby uniemożliwić wprowadzanie zmian w przyszłych wersjach przy `DEFAULT` użyciu następującej konfiguracji JSON w pliku konfiguracji niestandardowej:

```json
"authorization_user_agent" : "BROWSER"
```

Użyj tej metody, aby zapewnić obsługę logowania jednokrotnego za pomocą przeglądarki urządzenia. MSAL korzysta z udostępnionego pliku cookie jar, który umożliwia innym natywnym aplikacjom lub aplikacjom sieci Web uzyskanie rejestracji jednokrotnej na urządzeniu przy użyciu plików cookie sesji utrwalania ustawionych przez MSAL.

### <a name="browser-selection-heuristic"></a>Algorytm heurystyczny wyboru przeglądarki

Ponieważ nie jest możliwe, aby MSAL określony pakiet przeglądarki był używany w każdej z szerokiej macierzy telefonów z systemem Android, MSAL implementuje algorytm heurystyczny wyboru przeglądarki, który próbuje zapewnić najlepszą obsługę logowania jednokrotnego dla wielu urządzeń.

MSAL przede wszystkim pobiera domyślną przeglądarkę z Menedżera pakietów i sprawdza, czy znajduje się ona na przetestowanej liście bezpiecznych przeglądarek. Jeśli tak nie jest, MSAL powracanie do korzystania z widoku WebView zamiast uruchamiania innej przeglądarki innej niż domyślna z listy bezpiecznych. Domyślna przeglądarka zostanie wybrana bez względu na to, czy obsługuje ona karty niestandardowe. Jeśli przeglądarka obsługuje niestandardowe karty, MSAL uruchomi kartę niestandardową. karty niestandardowe mają wygląd i czują się bliżej aplikacji `WebView` i umożliwiają podstawowe dostosowanie interfejsu użytkownika. Aby dowiedzieć się więcej, zobacz [niestandardowe karty w systemie Android](https://developer.chrome.com/multidevice/android/customtabs) .

Jeśli na urządzeniu nie ma żadnych pakietów przeglądarki, MSAL korzysta z aplikacji `WebView` . Jeśli ustawienie domyślne urządzenia nie zostanie zmienione, należy uruchomić tę samą przeglądarkę dla każdego logowania, aby zapewnić obsługę logowania jednokrotnego.

#### <a name="tested-browsers"></a>Przetestowane przeglądarki

Następujące przeglądarki zostały przetestowane w celu sprawdzenia, czy poprawne przekierowanie do `"redirect_uri"` określonego w pliku konfiguracji:

| Urządzenie | Wbudowana przeglądarka | Chrome | Opera  | Microsoft Edge | Przeglądarka UC | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (interfejs API 17) | chodzenia | chodzenia |nie dotyczy |nie dotyczy |nie dotyczy |nie dotyczy |
| Samsung S7 (interfejs API 25) | Przekaż<sup>1</sup> | chodzenia | chodzenia | chodzenia | udało |chodzenia |
| Huawei (interfejs API 26) |Pass<sup>2</sup> | chodzenia | udało | chodzenia | chodzenia |chodzenia |
| Vivo (interfejs API 26) |chodzenia|chodzenia|chodzenia|chodzenia|chodzenia|udało|
| Piksel 2 (interfejs API 26) |chodzenia | chodzenia | chodzenia | chodzenia | udało |chodzenia |
| Oppo | chodzenia | nie dotyczy<sup>3</sup>|nie dotyczy  |nie dotyczy |nie dotyczy | nie dotyczy|
| OnePlus (interfejs API 25) |chodzenia | chodzenia | chodzenia | chodzenia | udało |chodzenia |
| Nexus (interfejs API 28) |chodzenia | chodzenia | chodzenia | chodzenia | udało |chodzenia |
|MI | chodzenia | chodzenia | chodzenia | chodzenia | udało |chodzenia |

<sup>1</sup> Wbudowana przeglądarka Samsung to Samsung Internet.<br/>
<sup>2</sup> Wbudowana przeglądarka Huawei jest przeglądarką Huawei.<br/>
<sup>3</sup> Domyślnej przeglądarki nie można zmienić w ustawieniu urządzenia OPPO.

## <a name="next-steps"></a>Następne kroki

[Tryb udostępnionego urządzenia dla urządzeń z systemem Android](msal-android-shared-devices.md) umożliwia skonfigurowanie urządzenia z systemem Android w taki sposób, aby można je było łatwo udostępnić wielu pracownikom.