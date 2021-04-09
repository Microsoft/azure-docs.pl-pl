---
title: MSAL dla systemu iOS & macOS różnice | Azure
titleSuffix: Microsoft identity platform
description: Opisuje różnice użycia biblioteki uwierzytelniania firmy Microsoft (MSAL) między systemami iOS i macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: oldalton
ms.custom: aaddev
ms.openlocfilehash: 59e4111b6dda386777e820c9be16ace9ff01135c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98064919"
---
# <a name="microsoft-authentication-library-for-ios-and-macos-differences"></a>Biblioteka uwierzytelniania firmy Microsoft dla systemów iOS i macOS — różnice

W tym artykule opisano różnice w działaniu między biblioteką uwierzytelniania firmy Microsoft (MSAL) dla systemów iOS i macOS.

> [!NOTE]
> Na komputerze Mac MSAL obsługuje tylko aplikacje macOS.

## <a name="general-differences"></a>Ogólne różnice

MSAL for macOS to podzbiór funkcji dostępnych dla systemu iOS.

MSAL dla macOS nie obsługuje:

- różne typy przeglądarek, takie jak `ASWebAuthenticationSession` , `SFAuthenticationSession` , `SFSafariViewController` .
- uwierzytelnianie przez brokera za pośrednictwem aplikacji Microsoft Authenticator nie jest obsługiwane w przypadku macOS.

Udostępnianie łańcucha kluczy między aplikacjami z tego samego wydawcy jest bardziej ograniczone na macOS 10,14 i starszych. Użyj [list kontroli dostępu](https://developer.apple.com/documentation/security/keychain_services/access_control_lists?language=objc) , aby określić ścieżki do aplikacji, które powinny korzystać z łańcucha kluczy. Użytkownik może zobaczyć dodatkowe wiersze łańcucha kluczy.

W przypadku macOS 10.15 +, zachowanie MSAL jest takie samo jak w przypadku systemów iOS i macOS. MSAL używa [grup dostępu pęku kluczy](https://developer.apple.com/documentation/security/keychain_services/keychain_items/sharing_access_to_keychain_items_among_a_collection_of_apps?language=objc) do udostępniania łańcucha kluczy. 

### <a name="conditional-access-authentication-differences"></a>Różnice dotyczące uwierzytelniania dostępu warunkowego

W przypadku scenariuszy dostępu warunkowego w przypadku korzystania z usługi MSAL dla systemu iOS będą wyświetlane mniejsze monity użytkownika. Wynika to z faktu, że system iOS używa aplikacji brokera (Microsoft Authenticator), która wyklucza konieczność monitowania użytkownika w niektórych przypadkach.

### <a name="project-setup-differences"></a>Różnice w konfiguracji projektu

**macOS**

- Podczas konfigurowania projektu w programie macOS upewnij się, że aplikacja jest podpisana przy użyciu prawidłowego certyfikatu programistycznego lub produkcyjnego. MSAL nadal działa w trybie bez znaku, ale zadziała inaczej w odniesieniu do trwałości pamięci podręcznej. Aplikacja powinna być uruchamiana tylko bez znaku na potrzeby debugowania. Jeśli aplikacja jest dystrybuowana bez znaku, będzie:
1. W dniu 10,14 i wcześniejszych MSAL wyświetli monit o podanie hasła pęku kluczy przy każdym ponownym uruchomieniu aplikacji.
2. W programie 10.15 + MSAL monituje użytkownika o podanie poświadczeń dla każdego przejęcia tokenu. 

- aplikacje macOS nie muszą implementować wywołania AppDelegate.

**iOS**

- Aby skonfigurować projekt do obsługi przepływu brokera uwierzytelniania, należy wykonać dodatkowe czynności. Kroki są wywoływane w samouczku.
- projekty systemu iOS muszą rejestrować niestandardowe schematy w info. plist. Nie jest to wymagane w macOS.
