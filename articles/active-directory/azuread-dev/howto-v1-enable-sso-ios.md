---
title: Jak włączyć logowanie jednokrotne dla wielu aplikacji w systemie iOS przy użyciu biblioteki ADAL | Microsoft Docs
description: Jak korzystać z funkcji zestawu ADAL SDK, aby umożliwić Logowanie jednokrotne w aplikacjach.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: ios
ms.devlang: objective-c
ms.topic: how-to
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: 08b018082c753b9524cb12a72d637fe5458d9114
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85383703"
---
# <a name="how-to-enable-cross-app-sso-on-ios-using-adal"></a>Instrukcje: Włączanie logowania jednokrotnego dla aplikacji w systemie iOS przy użyciu biblioteki ADAL

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Logowanie jednokrotne (SSO) umożliwia użytkownikom wprowadzanie poświadczeń tylko raz i ich automatyczne działanie w aplikacjach i na różnych platformach, które mogą być używane przez inne aplikacje (takie jak konta Microsoft lub konta służbowego Microsoft 365) bez względu na wydawcę.

Platforma tożsamości firmy Microsoft wraz z zestawami SDK ułatwia włączenie logowania jednokrotnego w ramach własnego pakietu aplikacji lub z możliwościami brokera i aplikacjami uwierzytelniania na całym urządzeniu.

W tym instruktażu dowiesz się, jak skonfigurować zestaw SDK w aplikacji w celu zapewnienia logowania jednokrotnego dla klientów.

Ten sposób zastosowania:

* Azure Active Directory (Azure Active Directory)
* Azure Active Directory B2C
* Azure Active Directory B2B
* Azure Active Directory dostępu warunkowego

## <a name="prerequisites"></a>Wymagania wstępne

Ta procedura polega na założeniu, że wiesz, jak:

* Inicjowanie obsługi administracyjnej aplikacji przy użyciu starszej wersji portalu usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Rejestrowanie aplikacji](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
* Zintegruj swoją aplikację z [zestawem SDK usługi Azure AD systemu iOS](https://github.com/AzureAD/azure-activedirectory-library-for-objc).

## <a name="single-sign-on-concepts"></a>Pojęcia dotyczące logowania jednokrotnego

### <a name="identity-brokers"></a>Brokerzy tożsamości

Firma Microsoft udostępnia aplikacje dla każdej platformy mobilnej, która umożliwia mostkowanie poświadczeń między aplikacjami od różnych dostawców i ulepszonych funkcji, które wymagają jednego bezpiecznego miejsca, z którego można weryfikować poświadczenia. Są one nazywane **brokerami**.

W systemach iOS i Android brokerzy są dostarczani za pomocą aplikacji do pobrania, które klienci instalują niezależnie lub wypychani do urządzenia przez firmę, która zarządza niektórymi lub wszystkimi urządzeniami dla swoich pracowników. Brokerzy obsługują zarządzanie zabezpieczeniami tylko dla niektórych aplikacji lub całego urządzenia w oparciu o konfigurację administratora IT. W systemie Windows ta funkcja jest zapewniana przez selektor konta wbudowany w system operacyjny, który jest dobrze znany jako Broker uwierzytelniania w sieci Web.

### <a name="patterns-for-logging-in-on-mobile-devices"></a>Wzorce logowania na urządzeniach przenośnych

Dostęp do poświadczeń na urządzeniach jest zgodny z dwoma podstawowymi wzorcami:

* Nieobsługiwane nazwy logowania z obsługą brokera
* Obsługiwane nazwy logowania obsługiwane przez brokera

#### <a name="non-broker-assisted-logins"></a>Nieobsługiwane nazwy logowania z obsługą brokera

Logowania nieobsługiwane przez brokera to środowiska logowania, które są używane w połączeniu z aplikacją i używają lokalnego magazynu na urządzeniu dla tej aplikacji. Ten magazyn może być współużytkowany przez aplikacje, ale poświadczenia są ściśle powiązane z aplikacją lub pakietem aplikacji przy użyciu tego poświadczenia. Najprawdopodobniej wystąpił to w wielu aplikacjach mobilnych po wprowadzeniu nazwy użytkownika i hasła w samej aplikacji.

Te identyfikatory logowania mają następujące zalety:

* Środowisko użytkownika istnieje w całości w aplikacji.
* Poświadczenia mogą być współużytkowane przez aplikacje, które są podpisane za pomocą tego samego certyfikatu, zapewniając Logowanie jednokrotne do Twojego zestawu aplikacji.
* Przed zalogowaniem i po zalogowaniu do aplikacji należy określić, czy jest ona dostępna.

Te identyfikatory logowania mają następujące wady:

* Użytkownicy nie mogą korzystać z logowania jednokrotnego we wszystkich aplikacjach korzystających z tożsamości firmy Microsoft, które zostały skonfigurowane przez aplikację.
* Aplikacja nie może być używana z bardziej zaawansowanymi funkcjami biznesowymi, takimi jak dostęp warunkowy, lub korzystać z pakietu produktów usługi Intune.
* Aplikacja nie może obsługiwać uwierzytelniania opartego na certyfikatach dla użytkowników firmowych.

Poniżej przedstawiono reprezentację sposobu, w jaki zestawy SDK współpracują z udostępnionym magazynem aplikacji, aby umożliwić Logowanie jednokrotne:

```
+------------+ +------------+  +-------------+
|            | |            |  |             |
|   App 1    | |   App 2    |  |   App 3     |
|            | |            |  |             |
|            | |            |  |             |
+------------+ +------------+  +-------------+
| ADAL SDK  |  |  ADAL SDK  |  |  ADAL SDK   |
+------------+-+------------+--+-------------+
|                                            |
|            App Shared Storage              |
+--------------------------------------------+
```

#### <a name="broker-assisted-logins"></a>Obsługiwane nazwy logowania obsługiwane przez brokera

Obsługiwane przez brokera identyfikatory logowania to środowiska logowania, które występują w aplikacji brokera i używają magazynu i zabezpieczeń brokera do udostępniania poświadczeń dla wszystkich aplikacji na urządzeniu, które stosują platformę tożsamości. Oznacza to, że aplikacje korzystają z brokera w celu podpisywania użytkowników w programie. W systemach iOS i Android te brokery są udostępniane za pomocą aplikacji do pobrania, które klienci instalują niezależnie lub wypychani do urządzenia przez firmę zarządzającą urządzeniem dla użytkownika. Przykładem tego typu aplikacji jest aplikacja Microsoft Authenticator w systemie iOS. W systemie Windows ta funkcja jest udostępniana przez funkcję wyboru konta wbudowaną w system operacyjny, znaną technicznie jako brokera uwierzytelniania w sieci Web.

Środowisko to zależy od platformy i czasami może być zakłócone użytkownikom, jeśli nie są prawidłowo zarządzane. Najprawdopodobniej znasz ten wzorzec, jeśli masz zainstalowaną aplikację Facebook i używasz połączenia Facebook z innej aplikacji. Platforma tożsamości używa tego samego wzorca.

W przypadku systemu iOS to prowadzi do animacji "Transition", w której aplikacja jest wysyłana do tła, podczas gdy Microsoft Authenticator aplikacje pojawiają się na pierwszym planie, aby użytkownik mógł wybrać konto, za pomocą którego chcesz się zalogować.

W przypadku systemu Android i Windows wybór konta jest wyświetlany na górze aplikacji, co jest mniej uciążliwe dla użytkownika.

#### <a name="how-the-broker-gets-invoked"></a>Jak zostanie wywołana Broker

Jeśli na urządzeniu jest zainstalowany zgodny Broker, taki jak aplikacja Microsoft Authenticator, zestawy SDK automatycznie wykonują zadania wywołania brokera, gdy użytkownik wskaże zalogowanie się przy użyciu dowolnego konta z platformy tożsamości. To konto może należeć do osobistego konta Microsoft, konta służbowego lub konta, które można udostępnić i hostować na platformie Azure przy użyciu naszych produktów B2C i B2B.

#### <a name="how-we-ensure-the-application-is-valid"></a>Jak upewnić się, że aplikacja jest prawidłowa

Konieczność zapewnienia tożsamości aplikacji, która wywołuje brokera, ma kluczowe znaczenie dla zabezpieczeń zapewnianych przez dane logowania obsługiwane przez brokera. Żadne z systemów iOS i Android wymuszają unikatowe identyfikatory, które są prawidłowe tylko dla danej aplikacji, więc złośliwe aplikacje mogą "fałszować" Identyfikator legalnej aplikacji i otrzymać tokeny przeznaczone dla legalnej aplikacji. Aby upewnić się, że zawsze komunikujemy się z właściwą aplikacją w środowisku uruchomieniowym, poprosimy o udostępnienie niestandardowego redirectURI podczas rejestrowania aplikacji w firmie Microsoft. Jak deweloperzy powinni skierować ten identyfikator URI przekierowania jest szczegółowo opisany poniżej. Ten niestandardowy redirectURI zawiera identyfikator pakietu aplikacji i jest niepowtarzalny dla aplikacji w sklepie Apple App Store. Gdy aplikacja wywołuje brokera, Broker prosi system operacyjny iOS o podanie identyfikatora pakietu, który wywołał brokera. Broker dostarcza ten identyfikator pakietu firmie Microsoft w wywołaniu naszego systemu tożsamości. Jeśli identyfikator pakietu aplikacji nie jest zgodny z IDENTYFIKATORem pakietu dostarczonym przez dewelopera podczas rejestracji, odmówimy dostępu do tokenów dla zasobu, którego żąda aplikacja. Ta kontrola zapewnia, że tylko aplikacja zarejestrowana przez dewelopera otrzymuje tokeny.

**Deweloper ma możliwość wyboru, czy zestaw SDK wywołuje brokera, czy używa nieobsługiwanego przez brokera przepływu.** Jeśli jednak deweloper zdecyduje się nie używać przepływu z obsługą brokera, utraci korzyści wynikające z używania poświadczeń logowania jednokrotnego, które użytkownik mógł już dodać na urządzeniu, i uniemożliwia ich używanie z funkcjami biznesowymi firmy Microsoft zapewnia swoim klientom, takie jak dostęp warunkowy, możliwości zarządzania usługą Intune i uwierzytelnianie oparte na certyfikatach.

Te identyfikatory logowania mają następujące zalety:

* Środowisko logowania jednokrotnego dla wszystkich swoich aplikacji niezależnie od dostawcy.
* Aplikacja może korzystać z bardziej zaawansowanych funkcji, takich jak dostęp warunkowy lub korzystać z pakietu usługi Intune.
* Aplikacja może obsługiwać uwierzytelnianie oparte na certyfikatach dla użytkowników firmowych.
* Znacznie bezpieczniejsze środowisko logowania jako tożsamość aplikacji i użytkownik jest weryfikowany przez aplikację brokera z dodatkowymi algorytmami i szyfrowaniem zabezpieczeń.

Te identyfikatory logowania mają następujące wady:

* W systemie iOS użytkownik zostanie przechodzący ze środowiska aplikacji, podczas gdy wybrane są poświadczenia.
* Utrata możliwości zarządzania logowaniem dla klientów w aplikacji.

Poniżej przedstawiono reprezentację sposobu, w jaki zestawy SDK współpracują z aplikacjami brokera, aby włączyć logowanie jednokrotne:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    Else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
| Azure SDK  | | Azure SDK  |   | Azure SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+
```

## <a name="enabling-cross-app-sso-using-adal"></a>Włączanie logowania jednokrotnego dla wielu aplikacji przy użyciu biblioteki ADAL

Tutaj korzystamy z zestawu ADAL iOS SDK, aby:

* Włącz logowanie jednokrotne z użyciem nieobsługiwanego brokera dla pakietu aplikacji
* Włącz obsługę logowania jednokrotnego dla brokera

### <a name="turning-on-sso-for-non-broker-assisted-sso"></a>Włączanie logowania jednokrotnego na potrzeby logowania jednokrotnego za pomocą usługi non-Broker

W przypadku nieobsługiwanego przez brokera logowania jednokrotnego w aplikacjach zestaw SDK zarządza znaczną złożonością logowania jednokrotnego. Obejmuje to znalezienie odpowiedniego użytkownika w pamięci podręcznej i utrzymywanie listy zalogowanych użytkowników do wykonywania zapytań.

Aby włączyć logowanie jednokrotne dla aplikacji, musisz wykonać następujące czynności:

1. Upewnij się, że wszystkie aplikacje używają tego samego identyfikatora klienta lub identyfikatora aplikacji.
2. Upewnij się, że wszystkie aplikacje mają ten sam certyfikat podpisywania od firmy Apple, aby można było udostępnić łańcuchy kluczy.
3. Zażądaj tego samego uprawnienia łańcucha kluczy dla każdej aplikacji.
4. Poinformuj zestawy SDK dotyczące udostępnionego łańcucha kluczy, którego chcesz używać.

#### <a name="using-the-same-client-id--application-id-for-all-the-applications-in-your-suite-of-apps"></a>Używanie tego samego identyfikatora klienta/identyfikatora aplikacji dla wszystkich aplikacji w twoim zestawie aplikacji

Aby platforma tożsamości mogła mieć pewność, że może udostępniać tokeny w aplikacjach, każda aplikacja będzie musiała mieć ten sam identyfikator klienta lub identyfikator aplikacji. Jest to unikatowy identyfikator, który został dostarczony podczas rejestracji pierwszej aplikacji w portalu.

Identyfikatory URI przekierowania pozwalają identyfikować różne aplikacje do usługi tożsamości firmy Microsoft, jeśli używają tego samego identyfikatora aplikacji. Każda aplikacja może mieć wiele identyfikatorów URI przekierowania zarejestrowanych w portalu dołączania. Każda aplikacja w Twoim pakiecie będzie miała inny identyfikator URI przekierowania. Przykład wyglądu poniżej:

Identyfikator URI przekierowania APP1: `x-msauth-mytestiosapp://com.myapp.mytestapp`

Identyfikator URI przekierowania APP2: `x-msauth-mytestiosapp://com.myapp.mytestapp2`

Identyfikator URI przekierowania APP3: `x-msauth-mytestiosapp://com.myapp.mytestapp3`

....

Są one zagnieżdżane w ramach tego samego identyfikatora klienta/identyfikatora aplikacji i zostały wyszukane w oparciu o identyfikator URI przekierowania w konfiguracji zestawu SDK.

```
+-------------------+
|                   |
|  Client ID        |
+---------+---------+
          |
          |           +-----------------------------------+
          |           |  App 1 Redirect URI               |
          +----------^+                                   |
          |           +-----------------------------------+
          |
          |           +-----------------------------------+
          +----------^+  App 2 Redirect URI               |
          |           |                                   |
          |           +-----------------------------------+
          |
          +----------^+-----------------------------------+
                      |  App 3 Redirect URI               |
                      |                                   |
                      +-----------------------------------+

```

Poniżej wyjaśniono format tych identyfikatorów URI przekierowania. Możesz użyć dowolnego identyfikatora URI przekierowania, chyba że chcesz obsługiwać brokera. w takim przypadku muszą one wyglądać podobnie jak powyżej.

#### <a name="create-keychain-sharing-between-applications"></a>Tworzenie udostępniania łańcucha kluczy między aplikacjami

Włączenie udostępniania łańcucha kluczy wykracza poza zakres tego dokumentu i jest objęte przez firmę Apple przy [dodawaniu funkcji](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html). Ważne jest, aby zdecydować, jak ma być wywoływana łańcucha kluczy, i dodać tę możliwość do wszystkich aplikacji.

Po poprawnym skonfigurowaniu uprawnień powinien zostać wyświetlony plik w katalogu projektu zatytułowany, który wygląda następująco `entitlements.plist` :

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN" "https://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>keychain-access-groups</key>
    <array>
        <string>$(AppIdentifierPrefix)com.myapp.mytestapp</string>
        <string>$(AppIdentifierPrefix)com.myapp.mycache</string>
    </array>
</dict>
</plist>
```

Po włączeniu uprawnień łańcucha kluczy w każdej aplikacji i przygotowaniu do korzystania z logowania jednokrotnego poinformuj zestaw SDK o tożsamości dla łańcucha kluczy, używając następującego ustawienia w ustawieniach `ADAuthenticationSettings` :

```
defaultKeychainSharingGroup=@"com.myapp.mycache";
```

> [!WARNING]
> Po udostępnieniu łańcucha kluczy w aplikacjach każda aplikacja może usuwać użytkowników lub gorszyć usuwanie wszystkich tokenów w aplikacji. Jest to szczególnie katastrofalne, jeśli masz aplikacje, które opierają się na tokenach do wykonywania zadań w tle. Udostępnianie łańcucha kluczy oznacza, że należy zachować ostrożność w każdej operacji usuwania za pośrednictwem zestawów SDK tożsamości.

Gotowe. Zestaw SDK będzie teraz udostępniać poświadczenia dla wszystkich aplikacji. Lista użytkowników będzie również współużytkowana przez wystąpienia aplikacji.

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Włączanie logowania jednokrotnego dla brokera z pomocą usługi logowania jednokrotnego

Możliwość używania dowolnego brokera zainstalowanego na urządzeniu przez aplikację jest **domyślnie wyłączona**. Aby można było używać aplikacji z brokerem, należy wykonać dodatkową konfigurację i dodać do aplikacji kod.

Poniżej przedstawiono kroki, które należy wykonać:

1. Włącz tryb brokera w wywołaniu kodu aplikacji MS SDK.
2. Ustanów nowy identyfikator URI przekierowania i zapewnij zarówno rejestrację aplikacji, jak i aplikacji.
3. Rejestrowanie schematu adresu URL.
4. Dodaj uprawnienie do pliku info. plist.

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Krok 1. Włączanie trybu brokera w aplikacji

Możliwość korzystania z brokera przez aplikację jest włączana podczas tworzenia "kontekstu" lub początkowej konfiguracji obiektu uwierzytelniania. W tym celu należy ustawić typ poświadczeń w kodzie:

```
/*! See the ADCredentialsType enumeration definition for details */
@propertyADCredentialsType credentialsType;
```
`AD_CREDENTIALS_AUTO`Ustawienie umożliwi zestawowi SDK próba wywołania do brokera, `AD_CREDENTIALS_EMBEDDED` uniemożliwiając wywołanie zestawu SDK do brokera.

#### <a name="step-2-registering-a-url-scheme"></a>Krok 2. rejestrowanie schematu adresu URL

Platforma tożsamości używa adresów URL do wywołania brokera, a następnie powrotu kontroli z powrotem do aplikacji. Aby zakończyć tę rundę, potrzebujesz schematu adresu URL zarejestrowanego dla aplikacji, o której będzie wiadomo platforma tożsamości. Może to być oprócz innych schematów aplikacji, które zostały wcześniej zarejestrowane w aplikacji.

> [!WARNING]
> Zalecamy, aby schemat adresu URL był dość unikatowy, aby zminimalizować prawdopodobieństwo użycia innej aplikacji przy użyciu tego samego schematu adresu URL. Firma Apple nie wymusza unikatowości schematów adresów URL zarejestrowanych w sklepie App Store.

Poniżej przedstawiono przykład sposobu wyświetlania w konfiguracji projektu. Można to także zrobić również w XCode:

```
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.myapp.mytestapp</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>x-msauth-mytestiosapp</string>
        </array>
    </dict>
</array>
```

#### <a name="step-3-establish-a-new-redirect-uri-with-your-url-scheme"></a>Krok 3. nawiązanie nowego identyfikatora URI przekierowania ze schematem adresu URL

Aby upewnić się, że zawsze zwracamy tokeny poświadczeń do poprawnej aplikacji, musimy upewnić się, że dzwonimy z powrotem do aplikacji w taki sposób, aby można było zweryfikować system operacyjny iOS. System operacyjny iOS zgłasza do aplikacji Microsoft Broker identyfikator pakietu aplikacji, który je wywołuje. Nie może to być sfałszowane przez nieautoryzowaną aplikację. W związku z tym korzystamy z niego wraz z identyfikatorem URI naszej aplikacji brokera, aby upewnić się, że tokeny są zwracane do odpowiedniej aplikacji. Wymagamy ustanowienia tego unikatowego identyfikatora URI przekierowania zarówno w aplikacji, jak i jako identyfikatora URI przekierowania w naszym portalu dla deweloperów.

Identyfikator URI przekierowania musi mieć poprawną formę:

`<app-scheme>://<your.bundle.id>`

przykład: *x-msauth-mytestiosapp://com.MyApp.mytestapp*

Ten identyfikator URI przekierowania musi być określony w rejestracji aplikacji przy użyciu [Azure Portal](https://portal.azure.com/). Aby uzyskać więcej informacji na temat rejestracji aplikacji w usłudze Azure AD, zobacz [Integrowanie z Azure Active Directory](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

##### <a name="step-3a-add-a-redirect-uri-in-your-app-and-dev-portal-to-support-certificate-based-authentication"></a>Krok 3a: Dodawanie identyfikatora URI przekierowania w aplikacji i portalu deweloperów do obsługi uwierzytelniania opartego na certyfikatach

Aby można było obsługiwać uwierzytelnianie oparte na certyfikatach, należy zarejestrować drugi "msauth" w aplikacji, a [Azure Portal](https://portal.azure.com/) do obsługi uwierzytelniania certyfikatu, jeśli chcesz dodać tę obsługę w aplikacji.

`msauth://code/<broker-redirect-uri-in-url-encoded-form>`

np.: *msauth://Code/x-msauth-mytestiosapp%3A%2F%2Fcom.MyApp.mytestapp*

#### <a name="step-4-add-a-configuration-parameter-to-your-app"></a>Krok 4. Dodawanie parametru konfiguracji do aplikacji

Użycie biblioteki ADAL — canOpenURL: Aby sprawdzić, czy na urządzeniu zainstalowano brokera. W systemie iOS 9 na platformie Apple zablokowano, które schematy mogą wykonywać zapytania dotyczące aplikacji. Musisz dodać "msauth" do sekcji LSApplicationQueriesSchemes `info.plist file` .

```
    <key>LSApplicationQueriesSchemes</key>
    <array>
        <string>msauth</string>
    </array>

```

### <a name="youve-configured-sso"></a>Skonfigurowano Logowanie jednokrotne.

Teraz zestaw SDK tożsamości automatycznie będzie udostępniać poświadczenia w aplikacjach i wywoływać brokera, jeśli jest obecny na urządzeniu.

## <a name="next-steps"></a>Następne kroki

* Informacje o [protokole SAML logowania](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) jednokrotnego
