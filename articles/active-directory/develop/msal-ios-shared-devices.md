---
title: Tryb udostępnionego urządzenia dla urządzeń z systemem iOS
titleSuffix: Microsoft identity platform | Azure
description: Dowiedz się, jak włączyć tryb udostępnionego urządzenia, aby umożliwić pracownikom teraźniejszości współużytkowanie urządzenia z systemem iOS
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: 3353d87291492563acbd3a85bbae266c711377f2
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105611807"
---
# <a name="shared-device-mode-for-ios-devices"></a>Tryb udostępnionego urządzenia dla urządzeń z systemem iOS

>[!IMPORTANT]
> Ta funkcja [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Procesy robocze teraźniejszości, takie jak detaliczne jednostki sprzedaży, członkowie załóg lotów i pracownicy usług polowych, często wykorzystują udostępnione urządzenie przenośne do wykonywania swoich zadań. Te współużytkowane urządzenia mogą stwarzać zagrożenie bezpieczeństwa, jeśli użytkownicy współużytkują swoje hasła lub numery PIN, celowo lub nie, aby uzyskać dostęp do danych klientów i firmowych na udostępnionym urządzeniu.

Tryb udostępnionego urządzenia pozwala na łatwiejsze i bezpieczne udostępnianie urządzeń z systemem iOS 13 lub nowszym. Pracownicy mogą szybko logować się i uzyskiwać dostęp do informacji o klientach. Po ich przeniesieniu lub zadaniu można wylogować się z urządzenia, aby było ono natychmiast gotowe do użycia przez kolejnego pracownika.

Tryb udostępnionego urządzenia zapewnia również zarządzanie tożsamością przez firmę Microsoft.

Ta funkcja korzysta z [aplikacji Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) do zarządzania użytkownikami na urządzeniu oraz do dystrybuowania [wtyczki Microsoft Enterprise SSO dla urządzeń firmy Apple](apple-sso-plugin.md).

## <a name="create-a-shared-device-mode-app"></a>Tworzenie aplikacji trybu udostępnionego urządzenia

Aby utworzyć aplikację trybu udostępnionego urządzenia, deweloperzy i Administratorzy urządzeń w chmurze współpracują ze sobą:

1. **Deweloperzy aplikacji** piszą aplikację jednokontową (aplikacje z wieloma kontami nie są obsługiwane w trybie udostępnionego urządzenia) i zapisują kod do obsługi takich elementów jak udostępnione urządzenie do wylogowania.

1. **Administratorzy urządzeń** przygotują urządzenie do udostępnienia przy użyciu dostawcy zarządzania urządzeniami przenośnymi (MDM), takiego jak Microsoft Intune, aby zarządzać urządzeniami w swojej organizacji. MDM wysyła do urządzeń Microsoft Authenticator aplikację i włącza "tryb współużytkowany" dla każdego urządzenia za pomocą aktualizacji profilu na urządzeniu. To ustawienie trybu udostępnionego powoduje zmianę zachowania obsługiwanych aplikacji na urządzeniu. Ta konfiguracja od dostawcy MDM ustawia tryb udostępnionego urządzenia dla urządzenia i włącza [wtyczkę logowania JEDNOkrotnego w przedsiębiorstwie firmy Microsoft dla urządzeń firmy Apple](apple-sso-plugin.md) , która jest wymagana do trybu udostępnionego urządzenia.

1. [**Wymagane w publicznej wersji zapoznawczej**] Użytkownik z rolą [administratora urządzenia w chmurze](../roles/permissions-reference.md#cloud-device-administrator) musi uruchomić [aplikację Microsoft Authenticator](../user-help/user-help-auth-app-overview.md) i dołączyć swoje urządzenie do organizacji.

    Aby skonfigurować członkostwo ról organizacyjnych w Azure Portal: **Azure Active Directory**  >  **role i Administratorzy**  >  **administrator urządzeń w chmurze**

Poniższe sekcje ułatwiają aktualizację aplikacji do obsługi trybu udostępnionego urządzenia.

## <a name="use-intune-to-enable-shared-device-mode--sso-extension"></a>Korzystanie z usługi Intune w celu włączenia trybu udostępnionego urządzenia & rozszerzenia SSO

> [!NOTE]
> Poniższy krok jest wymagany tylko w przypadku publicznej wersji zapoznawczej.

Urządzenie musi być skonfigurowane do obsługi trybu udostępnionego urządzenia. Musi mieć zainstalowany system iOS 13 + i być zarejestrowany w systemie MDM. Konfiguracja zarządzania urządzeniami przenośnymi musi również włączyć [wtyczkę logowania JEDNOkrotnego w przedsiębiorstwie firmy Microsoft dla urządzeń firmy Apple](apple-sso-plugin.md). Aby dowiedzieć się więcej na temat rozszerzeń rejestracji jednokrotnej, zobacz [film wideo firmy Apple](https://developer.apple.com/videos/play/tech-talks/301/).

1. W portalu konfiguracji usługi Intune poinformuj urządzenie, aby włączyć [wtyczkę logowania JEDNOkrotnego w przedsiębiorstwie firmy Microsoft dla urządzeń firmy Apple](apple-sso-plugin.md) z następującą konfiguracją:

    - **Typ**: redirect
    - **Identyfikator rozszerzenia**: com. Microsoft. azureauthenticator. ssoextension
    - **Identyfikator zespołu**: (to pole nie jest wymagane w przypadku systemu iOS)
    - **Adresy URL**:   
        - `https://login.microsoftonline.com`
        - `https://login.microsoft.com`
        - `https://sts.windows.net`
        - `https://login.partner.microsoftonline.cn`
        - `https://login.chinacloudapi.cn`
        - `https://login.microsoftonline.de`
        - `https://login.microsoftonline.us`
        - `https://login.usgovcloudapi.net`
        - `https://login-us.microsoftonline.com`
    - **Dodatkowe dane do skonfigurowania**:
      - Klucz: sharedDeviceMode
      - Typ: wartość logiczna
      - Wartość: prawda

    Aby uzyskać więcej informacji na temat konfigurowania usługi Intune, zapoznaj się z [dokumentacją konfiguracyjną usługi Intune](/intune/configuration/ios-device-features-settings).

1. Następnie skonfiguruj rozwiązanie MDM w celu wypchnięcia aplikacji Microsoft Authenticator na urządzenie przy użyciu profilu MDM.

    Ustaw następujące opcje konfiguracji, aby włączyć tryb udostępnionego urządzenia:

    - Konfiguracja 1:
      - Klucz: sharedDeviceMode
      - Typ: wartość logiczna
      - Wartość: prawda

## <a name="modify-your-ios-application-to-support-shared-device-mode"></a>Modyfikowanie aplikacji systemu iOS w celu obsługi trybu udostępnionego urządzenia

Użytkownicy zależą od użytkownika, aby upewnić się, że ich dane nie są wyciekiem innemu użytkownikowi. Poniższe sekcje zawierają przydatne sygnały wskazujące na aplikację, że wprowadzono zmianę i powinny być obsługiwane.

Użytkownik jest odpowiedzialny za sprawdzanie stanu użytkownika na urządzeniu za każdym razem, gdy aplikacja jest używana, a następnie czyszczenie danych poprzedniego użytkownika. Obejmuje to również ponowne załadowanie z tła w ramach wielozadań.

Po zmianie użytkownika należy upewnić się, że zarówno dane poprzedniego użytkownika są wyczyszczone, jak i wszystkie dane w pamięci podręcznej wyświetlane w aplikacji zostaną usunięte. Zdecydowanie zalecamy, aby Twoja firma prowadziła proces przeglądu zabezpieczeń po zaktualizowaniu aplikacji do obsługi trybu udostępnionego urządzenia.

### <a name="detect-shared-device-mode"></a>Wykrywanie trybu udostępnionego urządzenia

Wykrywanie trybu udostępnionego urządzenia jest ważne dla aplikacji. W przypadku korzystania z aplikacji na udostępnionym urządzeniu wiele aplikacji będzie wymagało zmiany środowiska użytkownika. Na przykład aplikacja może mieć funkcję "Rejestracja", która nie jest odpowiednia dla procesu roboczego teraźniejszości, ponieważ prawdopodobnie ma już konto. Możesz również chcieć dodać dodatkowe zabezpieczenia do obsługi danych aplikacji, jeśli jest ona w trybie udostępniania.

Użyj `getDeviceInformationWithParameters:completionBlock:` interfejsu API w programie, `MSALPublicClientApplication` Aby określić, czy aplikacja jest uruchomiona na urządzeniu w trybie udostępnionego urządzenia.

Poniższe fragmenty kodu pokazują przykłady użycia `getDeviceInformationWithParameters:completionBlock:` interfejsu API.

#### <a name="swift"></a>Swift

```swift
application.getDeviceInformation(with: nil, completionBlock: { (deviceInformation, error) in

    guard let deviceInfo = deviceInformation else {
        return
    }

    let isSharedDevice = deviceInfo.deviceMode == .shared
    // Change your app UX if needed
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
[application getDeviceInformationWithParameters:nil
                                completionBlock:^(MSALDeviceInformation * _Nullable deviceInformation, NSError * _Nullable error)
{
    if (!deviceInformation)
    {
        return;
    }

    BOOL isSharedDevice = deviceInformation.deviceMode == MSALDeviceModeShared;
    // Change your app UX if needed
}];
```

### <a name="get-the-signed-in-user-and-determine-if-a-user-has-changed-on-the-device"></a>Uzyskiwanie zalogowanego użytkownika i określanie, czy użytkownik zmienił się na urządzeniu

Kolejną ważną częścią obsługi trybu udostępnionego urządzenia jest określenie stanu użytkownika na urządzeniu i wyczyszczenie danych aplikacji w przypadku zmiany użytkownika lub braku żadnego użytkownika na urządzeniu. Użytkownik jest odpowiedzialny za zapewnienie, że dane nie są wyciekiem innemu użytkownikowi.

Za pomocą `getCurrentAccountWithParameters:completionBlock:` interfejsu API można wysyłać zapytania dotyczące aktualnie zalogowanego konta na urządzeniu.

#### <a name="swift"></a>Swift

```swift
let msalParameters = MSALParameters()
msalParameters.completionBlockQueue = DispatchQueue.main

application.getCurrentAccount(with: msalParameters, completionBlock: { (currentAccount, previousAccount, error) in

    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALParameters *parameters = [MSALParameters new];
parameters.completionBlockQueue = dispatch_get_main_queue();

[application getCurrentAccountWithParameters:parameters
                             completionBlock:^(MSALAccount * _Nullable account, MSALAccount * _Nullable previousAccount, NSError * _Nullable error)
{
    // currentAccount is the currently signed in account
    // previousAccount is the previously signed in account if any
}];
```

### <a name="globally-sign-in-a-user"></a>Globalne Logowanie użytkownika

Gdy urządzenie jest skonfigurowane jako urządzenie udostępnione, aplikacja może wywołać `acquireTokenWithParameters:completionBlock:` interfejs API w celu zalogowania się do konta. Konto będzie dostępne globalnie dla wszystkich kwalifikujących się aplikacji na urządzeniu po napisaniu pierwszej aplikacji na koncie.

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALInteractiveTokenParameters *parameters = [[MSALInteractiveTokenParameters alloc] initWithScopes:@[@"api://myapi/scope"] webviewParameters:[self msalTestWebViewParameters]];

parameters.loginHint = self.loginHintTextField.text;

[application acquireTokenWithParameters:parameters completionBlock:completionBlock];
```

### <a name="globally-sign-out-a-user"></a>Globalne wylogowywanie użytkownika

Poniższy kod usuwa konto zalogowane i czyści buforowane tokeny spoza aplikacji, ale również z urządzenia znajdującego się w trybie udostępnionego urządzenia. Nie jest jednak możliwe wyczyszczenie *danych* z aplikacji. Należy wyczyścić dane z aplikacji, a także wyczyścić wszystkie dane w pamięci podręcznej, które aplikacja może wyświetlać użytkownikowi.

#### <a name="clear-browser-state"></a>Wyczyść stan przeglądarki

> [!NOTE]
> Poniższy krok jest wymagany tylko w przypadku publicznej wersji zapoznawczej.

W tej publicznej wersji zapoznawczej [Wtyczka usługi logowania jednokrotnego w przedsiębiorstwie firmy Microsoft dla urządzeń Apple](apple-sso-plugin.md) czyści stan tylko dla aplikacji. Stan nie jest czyszczony w przeglądarce Safari. Zalecamy ręczne czyszczenie sesji przeglądarki, aby upewnić się, że żadne ślady stanu użytkownika nie zostały pozostawione. Możesz użyć opcjonalnej `signoutFromBrowser` Właściwości pokazanej poniżej, aby wyczyścić wszystkie pliki cookie. Spowoduje to, że przeglądarka zostanie krótko uruchomiona na urządzeniu.

#### <a name="swift"></a>Swift

```swift
let account = .... /* account retrieved above */

let signoutParameters = MSALSignoutParameters(webviewParameters: self.webViewParamaters!)
signoutParameters.signoutFromBrowser = true // Only needed for Public Preview.

application.signout(with: account, signoutParameters: signoutParameters, completionBlock: {(success, error) in

    if let error = error {
        // Signout failed
        return
    }

    // Sign out completed successfully
})
```

#### <a name="objective-c"></a>Objective-C

```objective-c
MSALAccount *account = ... /* account retrieved above */;

MSALSignoutParameters *signoutParameters = [[MSALSignoutParameters alloc] initWithWebviewParameters:webViewParameters];
signoutParameters.signoutFromBrowser = YES; // Only needed for Public Preview.

[application signoutWithAccount:account signoutParameters:signoutParameters completionBlock:^(BOOL success, NSError * _Nullable error)
{
    if (!success)
    {
        // Signout failed
        return;
    }

    // Sign out completed successfully
}];
```

## <a name="next-steps"></a>Następne kroki

Aby zobaczyć, jak działa tryb udostępnionego urządzenia, Poniższy przykład kodu w usłudze GitHub zawiera przykład uruchamiania aplikacji Worker teraźniejszości na urządzeniu z systemem iOS w trybie udostępnionego urządzenia:

[Przykład interfejsu API SWIFT Microsoft Graph MSAL systemu iOS](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc)
