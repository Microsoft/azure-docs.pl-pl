---
title: Przekaż stan niestandardowy w żądaniach uwierzytelniania (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak przekazać niestandardową wartość parametru stanu w żądaniu uwierzytelniania przy użyciu biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL. js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/16/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 1c05956f83ad3a6491627be8916fac2c8be2b7ff
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77084934"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Przekazywanie stanu niestandardowego w żądaniach uwierzytelniania przy użyciu MSAL. js

Parametr *State* , zdefiniowany przez OAuth 2,0, jest uwzględniony w żądaniu uwierzytelniania i jest również zwracany w odpowiedzi tokenu, aby zapobiec atakom na fałszerstwo żądań między lokacjami. Domyślnie Biblioteka uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL. js) przekazuje losowo wygenerowane unikatowe wartości parametru *stanu* w żądaniach uwierzytelniania.

Parametru stanu można także użyć do kodowania informacji o stanie aplikacji przed przekierowaniem. Można przekazać stan użytkownika w aplikacji, na przykład stronę lub widok, jako dane wejściowe tego parametru. Biblioteka MSAL. js umożliwia przekazanie stanu niestandardowego jako parametru stanu w `Request` obiekcie:

```javascript
// Request type
export type AuthenticationParameters = {
    scopes?: Array<string>;
    extraScopesToConsent?: Array<string>;
    prompt?: string;
    extraQueryParameters?: QPDict;
    claimsRequest?: string;
    authority?: string;
    state?: string;
    correlationId?: string;
    account?: Account;
    sid?: string;
    loginHint?: string;
    forceRefresh?: boolean;
};
```

> [!Note]
> Jeśli chcesz pominąć buforowany token i przejść do serwera, przekaż wartość Boolean `forceRefresh` do obiektu AuthenticationParameters używanego do wykonywania żądania logowania/tokenu.
> `forceRefresh`nie powinno być używane domyślnie z powodu wpływu na wydajność aplikacji.
> Korzystanie z pamięci podręcznej umożliwi użytkownikom lepsze środowisko pracy.
> Pomijanie pamięci podręcznej powinno być używane tylko w scenariuszach, w których wiadomo, że aktualnie buforowane dane nie mają aktualnych informacji.
> Takie jak narzędzie administracyjne, które dodaje role do użytkownika, który musi uzyskać nowy token ze zaktualizowanymi rolami.

Przykład:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

Przesłany stan jest dołączany do unikatowego identyfikatora GUID ustawionego przez MSAL. js podczas wysyłania żądania. Po zwróceniu odpowiedzi MSAL. js sprawdza zgodność stanu, a następnie zwraca niestandardową przekazaną w `Response` obiekcie jako. `accountState`

```javascript
export type AuthResponse = {
    uniqueId: string;
    tenantId: string;
    tokenType: string;
    idToken: IdToken;
    accessToken: string;
    scopes: Array<string>;
    expiresOn: Date;
    account: Account;
    accountState: string;
};
```

Aby dowiedzieć się więcej, Przeczytaj o [kompilowaniu aplikacji jednostronicowej (Spa)](scenario-spa-overview.md) przy użyciu MSAL. js.