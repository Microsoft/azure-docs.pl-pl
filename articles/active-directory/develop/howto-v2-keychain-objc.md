---
title: Konfigurowanie pęku kluczy
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak skonfigurować pęku kluczy, aby aplikacja mogła buforować tokeny w pęku kluczy.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: d94bf7ffe955c9ec9ee2a2e7f7c4dbaaa28df270
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77085853"
---
# <a name="configure-keychain"></a>Konfigurowanie pęku kluczy

Gdy [Biblioteka uwierzytelniania firmy Microsoft dla systemu iOS i macOS](msal-overview.md) (MSAL) w użytkowniku lub odświeża token, próbuje buforować tokeny w łańcuchu kluczy. Tokeny buforowania w łańcuchu kluczy umożliwiają MSAL zapewnianie dyskretnego logowania jednokrotnego między wieloma aplikacjami dystrybuowanymi przez ten sam program Apple Developer. Logowanie jednokrotne jest realizowane za pośrednictwem funkcji grup dostępu łańcucha kluczy. Aby uzyskać więcej informacji, zobacz [dokumentację elementów łańcucha kluczy](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc)firmy Apple.

W tym artykule opisano sposób konfigurowania uprawnień aplikacji, dzięki czemu MSAL może zapisywać buforowane tokeny w systemach iOS i macOS pęku kluczy.

## <a name="default-keychain-access-group"></a>Domyślna grupa dostępu łańcucha kluczy

### <a name="ios"></a>iOS

MSAL w systemie iOS domyślnie `com.microsoft.adalcache` używa grupy dostępu. Jest to grupa dostępu współdzielonego używana przez zestawy SDK MSAL i Azure AD Authentication Library (ADAL) i zapewnia najlepszą obsługę logowania jednokrotnego między wieloma aplikacjami z tego samego wydawcy.

W systemie iOS Dodaj grupę `com.microsoft.adalcache` pęku kluczy do uprawnienia do aplikacji w Xcode w obszarze **Ustawienia** > projektu**funkcje** > **udostępnianie łańcucha kluczy**

### <a name="macos"></a>macOS

MSAL na macOS domyślnie `com.microsoft.identity.universalstorage` używa grupy dostępu.

Ze względu na ograniczenia macOS pęku kluczy `access group` , MSAL nie jest bezpośrednio przetłumaczyć na atrybut grupy dostępu pęku kluczy (zobacz [KSecAttrAccessGroup](https://developer.apple.com/documentation/security/ksecattraccessgroup?language=objc)) na macOS 10,14 i wcześniejszych. Jednak zachowuje się podobnie z perspektywy rejestracji jednokrotnej, zapewniając, że wiele aplikacji dystrybuowanych przez tego samego deweloperów firmy Apple może mieć dyskretne Logowanie jednokrotne.

Na macOS 10,15 (macOS Catalina), MSAL używa atrybutu grupy dostępu pęku kluczy, aby uzyskać dyskretne Logowanie jednokrotne, podobnie jak w przypadku systemu iOS.

## <a name="custom-keychain-access-group"></a>Niestandardowa Grupa dostępu pęku kluczy

Jeśli chcesz użyć innej grupy dostępu łańcucha kluczy, możesz przekazać grupę niestandardową podczas tworzenia `MSALPublicClientApplicationConfig` przed utworzeniem `MSALPublicClientApplication`, w następujący sposób:

# <a name="objective-c"></a>[Obiektowy C](#tab/objc)

```objc
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"your-client-id"
                                                                                            redirectUri:@"your-redirect-uri"
                                                                                              authority:nil];
    
config.cacheConfig.keychainSharingGroup = @"custom-group";
    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:nil];
    
// Now call acquiretoken. 
// Tokens will be saved into the "custom-group" access group
// and only shared with other applications declaring the same access group
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
let config = MSALPublicClientApplicationConfig(clientId: "your-client-id",
                                            redirectUri: "your-redirect-uri",
                                              authority: nil)
config.cacheConfig.keychainSharingGroup = "custom-group"
        
do {
  let application = try MSALPublicClientApplication(configuration: config)
  // continue on with application          
} catch let error as NSError {
  // handle error here
}       
```

---

## <a name="disable-keychain-sharing"></a>Wyłącz udostępnianie łańcucha kluczy

Jeśli nie chcesz udostępniać stanu logowania jednokrotnego między wieloma aplikacjami lub korzystać z dowolnej grupy dostępu łańcucha kluczy, Wyłącz udostępnianie łańcucha kluczy, przekazując identyfikator pakietu aplikacji jako grupę łańcuchową:

# <a name="objective-c"></a>[Obiektowy C](#tab/objc)

```objc
config.cacheConfig.keychainSharingGroup = [[NSBundle mainBundle] bundleIdentifier];
```

# <a name="swift"></a>[Swift](#tab/swift)

```swift
if let bundleIdentifier = Bundle.main.bundleIdentifier {
    config.cacheConfig.keychainSharingGroup = bundleIdentifier
}
```

---

## <a name="handle--34018-error-failed-to-set-item-into-keychain"></a>Dojście — błąd 34018 (nie można ustawić elementu na pęku kluczy)

Błąd — 34018 zwykle oznacza, że pęku kluczy nie została poprawnie skonfigurowana. Upewnij się, że Grupa dostępu pęku kluczy, która została skonfigurowana w MSAL, jest zgodna z konfiguracją w uprawnieniach.

## <a name="ensure-your-application-is-properly-signed"></a>Upewnij się, że aplikacja jest prawidłowo podpisana

W systemie macOS aplikacje mogą być wykonywane bez podpisywania przez dewelopera. Chociaż większość funkcji MSAL będzie nadal działać, logowanie jednokrotne za pomocą dostępu do łańcucha kluczy wymaga podpisania aplikacji. Jeśli występuje wiele wierszy pęku kluczy, upewnij się, że podpis aplikacji jest prawidłowy.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat grup dostępu łańcucha kluczy w [dostępie do usługi Microsoft Sharing na potrzeby udostępniania elementów łańcucha kluczy w zbiorze aplikacji](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) .
