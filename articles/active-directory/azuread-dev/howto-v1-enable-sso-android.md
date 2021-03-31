---
title: Jak włączyć logowanie jednokrotne dla wielu aplikacji w systemie Android przy użyciu biblioteki ADAL | Microsoft Docs
description: Jak korzystać z funkcji zestawu ADAL SDK, aby umożliwić Logowanie jednokrotne w aplikacjach.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: how-to
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: brandwe, jmprieur
ms.custom: aaddev, devx-track-java
ROBOTS: NOINDEX
ms.openlocfilehash: 9d1faf7aed5cf4c4975925c7a56d169dc99a7bd5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87313595"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>Instrukcje: Włączanie logowania jednokrotnego dla aplikacji w systemie Android przy użyciu biblioteki ADAL

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Logowanie jednokrotne (SSO) umożliwia użytkownikom wprowadzanie poświadczeń tylko raz i ich automatyczne działanie w aplikacjach i na różnych platformach, które mogą być używane przez inne aplikacje (takie jak konta Microsoft lub konta służbowego Microsoft 365) bez względu na wydawcę.

Platforma tożsamości firmy Microsoft wraz z zestawami SDK ułatwia włączenie logowania jednokrotnego w ramach własnego pakietu aplikacji lub z możliwościami brokera i aplikacjami uwierzytelniania na całym urządzeniu.

W tym instruktażu dowiesz się, jak skonfigurować zestaw SDK w aplikacji w celu zapewnienia logowania jednokrotnego dla klientów.

## <a name="prerequisites"></a>Wymagania wstępne

Ta procedura polega na założeniu, że wiesz, jak:

- Inicjowanie obsługi administracyjnej aplikacji przy użyciu starszej wersji portalu dla Azure Active Directory (Azure AD). Aby uzyskać więcej informacji, zobacz [Rejestrowanie aplikacji](../develop/quickstart-register-app.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)
- Zintegruj swoją aplikację z usługą [Azure AD Android SDK](https://github.com/AzureAD/azure-activedirectory-library-for-android).

## <a name="single-sign-on-concepts"></a>Pojęcia dotyczące logowania jednokrotnego

### <a name="identity-brokers"></a>Brokerzy tożsamości

Firma Microsoft udostępnia aplikacje dla każdej platformy mobilnej, która umożliwia mostkowanie poświadczeń między aplikacjami od różnych dostawców i ulepszonych funkcji, które wymagają jednego bezpiecznego miejsca, z którego można weryfikować poświadczenia. Są one nazywane **brokerami**.

W systemach iOS i Android brokerzy są dostarczani za pomocą aplikacji do pobrania, które klienci instalują niezależnie lub wypychani do urządzenia przez firmę, która zarządza niektórymi lub wszystkimi urządzeniami dla swoich pracowników. Brokerzy obsługują zarządzanie zabezpieczeniami tylko dla niektórych aplikacji lub całego urządzenia w oparciu o konfigurację administratora IT. W systemie Windows ta funkcja jest zapewniana przez selektor konta wbudowany w system operacyjny, który jest dobrze znany jako Broker uwierzytelniania w sieci Web.

#### <a name="broker-assisted-login"></a>Logowanie z pomocą brokera

Obsługiwane przez brokera identyfikatory logowania to środowiska logowania, które występują w aplikacji brokera i używają magazynu i zabezpieczeń brokera do udostępniania poświadczeń dla wszystkich aplikacji na urządzeniu, które stosują platformę tożsamości. Domniemanie, że aplikacje będą polegać na brokerze w celu zalogowania użytkowników. W systemach iOS i Android te brokery są udostępniane za pomocą aplikacji do pobrania, które klienci instalują niezależnie lub mogą być wypychane do urządzenia przez firmę zarządzającą urządzeniem dla użytkownika. Przykładem tego typu aplikacji jest aplikacja Microsoft Authenticator w systemie iOS. W systemie Windows ta funkcja jest zapewniana przez selektor konta wbudowany w system operacyjny, który jest dobrze znany jako Broker uwierzytelniania w sieci Web.
Środowisko to zależy od platformy i czasami może być zakłócone użytkownikom, jeśli nie są prawidłowo zarządzane. Najprawdopodobniej znasz ten wzorzec, jeśli masz zainstalowaną aplikację Facebook i używasz połączenia Facebook z innej aplikacji. Platforma tożsamości używa tego samego wzorca.

W systemie Android wybór konta jest wyświetlany na górze aplikacji, co jest mniej uciążliwe dla użytkownika.

#### <a name="how-the-broker-gets-invoked"></a>Jak zostanie wywołana Broker

Jeśli na urządzeniu jest zainstalowany zgodny Broker, taki jak aplikacja Microsoft Authenticator, zestawy SDK tożsamości automatycznie wykonują zadania wywołania brokera, gdy użytkownik wskaże zalogowanie się przy użyciu dowolnego konta z platformy tożsamości.

#### <a name="how-microsoft-ensures-the-application-is-valid"></a>Jak firma Microsoft gwarantuje, że aplikacja jest ważna

Konieczność upewnienia się, że tożsamość aplikacji wywołującej brokera jest kluczowym zabezpieczeniem dostępnym w nazwach logowania obsługiwanych przez brokera. Systemy iOS i Android nie wymuszają unikatowych identyfikatorów, które są prawidłowe tylko dla danej aplikacji, więc złośliwe aplikacje mogą "fałszować" Identyfikator legalnej aplikacji i otrzymać tokeny przeznaczone dla legalnej aplikacji. Aby zapewnić, że firma Microsoft zawsze komunikuje się z właściwą aplikacją w czasie wykonywania, deweloper jest proszony o dostarczenie niestandardowego redirectURI podczas rejestrowania aplikacji w firmie Microsoft. **Jak deweloperzy powinni skierować ten identyfikator URI przekierowania jest szczegółowo opisany poniżej.** Ten niestandardowy redirectURI zawiera odcisk palca certyfikatu aplikacji i jest niepowtarzalny dla aplikacji przez Sklep Google Play. Gdy aplikacja wywołuje brokera, Broker prosi system operacyjny Android o podanie odcisku palca certyfikatu, który wywołał brokera. Broker udostępnia odcisk palca tego certyfikatu firmie Microsoft w wywołaniu systemu tożsamości. Jeśli odcisk palca certyfikatu aplikacji nie jest zgodny z odciskiem palca certyfikatu przekazanego do nas przez dewelopera podczas rejestracji, dostęp zostaje odrzucony do tokenów dla zasobu, którego żąda aplikacja. Ta kontrola zapewnia, że tylko aplikacja zarejestrowana przez dewelopera otrzymuje tokeny.

Obsługiwane przez brokera Logowanie jednokrotne ma następujące zalety:

* Środowisko logowania jednokrotnego dla wszystkich swoich aplikacji niezależnie od dostawcy.
* Aplikacja może korzystać z bardziej zaawansowanych funkcji firmy, takich jak dostęp warunkowy i obsługa scenariuszy usługi Intune.
* Aplikacja może obsługiwać uwierzytelnianie oparte na certyfikatach dla użytkowników firmowych.
* Bezpieczniejsze środowisko logowania jako tożsamość aplikacji i użytkownik jest weryfikowany przez aplikację brokera z dodatkowymi algorytmami i szyfrowaniem zabezpieczeń.

Poniżej przedstawiono reprezentację sposobu, w jaki zestawy SDK współpracują z aplikacjami brokera, aby włączyć logowanie jednokrotne:

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
|  ADAL SDK  | |  ADAL SDK  |   |  ADAL SDK   |
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

### <a name="turning-on-sso-for-broker-assisted-sso"></a>Włączanie logowania jednokrotnego dla brokera z pomocą usługi logowania jednokrotnego

Możliwość używania dowolnego brokera zainstalowanego na urządzeniu przez aplikację jest domyślnie wyłączona. Aby można było używać aplikacji z brokerem, należy wykonać dodatkową konfigurację i dodać do aplikacji kod.

Poniżej przedstawiono kroki, które należy wykonać:

1. Włącz tryb brokera w wywołaniu kodu zestawu MS SDK w kodzie aplikacji
2. Ustanów nowy identyfikator URI przekierowania i określ, czy zarówno aplikacja, jak i Rejestracja aplikacji
3. Konfigurowanie odpowiednich uprawnień w manifeście systemu Android

#### <a name="step-1-enable-broker-mode-in-your-application"></a>Krok 1. Włączanie trybu brokera w aplikacji

Możliwość korzystania z brokera przez aplikację jest włączana podczas tworzenia "ustawień" lub początkowej konfiguracji wystąpienia uwierzytelniania. Aby to zrobić w aplikacji:

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>Krok 2. nawiązanie nowego identyfikatora URI przekierowania ze schematem adresu URL

Aby upewnić się, że odpowiednia aplikacja otrzymuje zwrócone tokeny poświadczeń, należy się upewnić, że wywołanie z powrotem do aplikacji jest możliwe w taki sposób, aby można było zweryfikować system operacyjny Android. System operacyjny Android używa skrótu certyfikatu w magazynie Google Play. Ten skrót certyfikatu nie może być sfałszowany przez nieautoryzowaną aplikację. Wraz z identyfikatorem URI aplikacji brokera firma Microsoft gwarantuje, że tokeny są zwracane do odpowiedniej aplikacji. W aplikacji musi być zarejestrowany unikatowy identyfikator URI przekierowania.

Identyfikator URI przekierowania musi mieć poprawną formę:

`msauth://packagename/Base64UrlencodedSignature`

np.: *msauth://com.example.UserApp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

Ten identyfikator URI przekierowania można zarejestrować w rejestracji aplikacji przy użyciu [Azure Portal](https://portal.azure.com/). Aby uzyskać więcej informacji na temat rejestracji aplikacji w usłudze Azure AD, zobacz [Integrowanie z Azure Active Directory](../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>Krok 3. Konfigurowanie odpowiednich uprawnień w aplikacji

Aplikacja brokera w systemie Android używa funkcji Menedżera kont systemu operacyjnego Android do zarządzania poświadczeniami w aplikacjach. Aby można było korzystać z brokera w systemie Android, manifest aplikacji musi mieć uprawnienia do używania kont konta. Te uprawnienia zostały szczegółowo omówione w [dokumentacji Google dla programu Account Manager](https://developer.android.com/reference/android/accounts/AccountManager.html)

W szczególności te uprawnienia są następujące:

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>Skonfigurowano Logowanie jednokrotne.

Teraz zestaw SDK tożsamości automatycznie będzie udostępniać poświadczenia w aplikacjach i wywoływać brokera, jeśli jest obecny na urządzeniu.

## <a name="next-steps"></a>Następne kroki

* Informacje o [protokole SAML logowania](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) jednokrotnego
