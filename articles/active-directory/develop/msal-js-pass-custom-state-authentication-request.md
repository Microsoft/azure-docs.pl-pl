---
title: Przekaż stan niestandardowy w żądaniach uwierzytelniania (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak przekazać niestandardową wartość parametru stanu w żądaniu uwierzytelniania przy użyciu biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL.js).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 01/16/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 5c1fad96ea6e3b75b3afdfd4a4d3baac43308541
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/11/2021
ms.locfileid: "98063675"
---
# <a name="pass-custom-state-in-authentication-requests-using-msaljs"></a>Przekazywanie stanu niestandardowego w żądaniach uwierzytelniania przy użyciu MSAL.js

Parametr *State* , zdefiniowany przez OAuth 2,0, jest uwzględniony w żądaniu uwierzytelniania i jest również zwracany w odpowiedzi tokenu, aby zapobiec atakom na fałszerstwo żądań między lokacjami. Domyślnie Biblioteka uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL.js) przekazuje losowo wygenerowaną unikatową wartość parametru *stanu* w żądaniach uwierzytelniania.

Parametru stanu można także użyć do kodowania informacji o stanie aplikacji przed przekierowaniem. Można przekazać stan użytkownika w aplikacji, na przykład stronę lub widok, jako dane wejściowe tego parametru. Biblioteka MSAL.js umożliwia przekazanie stanu niestandardowego jako parametru stanu w `Request` obiekcie:

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
> Jeśli chcesz pominąć buforowany token i przejść do serwera, przekaż wartość Boolean do `forceRefresh` obiektu AuthenticationParameters używanego do wykonywania żądania logowania/tokenu.
> `forceRefresh` nie powinno być używane domyślnie z powodu wpływu na wydajność aplikacji.
> Korzystanie z pamięci podręcznej umożliwi użytkownikom lepsze środowisko pracy.
> Pomijanie pamięci podręcznej powinno być używane tylko w scenariuszach, w których wiadomo, że aktualnie buforowane dane nie mają aktualnych informacji.
> Takie jak narzędzie administracyjne, które dodaje role do użytkownika, który musi uzyskać nowy token ze zaktualizowanymi rolami.

Na przykład:

```javascript
let loginRequest = {
    scopes: ["user.read", "user.write"],
    state: "page_url"
}

myMSALObj.loginPopup(loginRequest);
```

Przesłany stan jest dołączany do unikatowego identyfikatora GUID ustawionego przez MSAL.js podczas wysyłania żądania. Po zwróceniu odpowiedzi MSAL.js sprawdza pod kątem zgodności stanu, a następnie zwraca niestandardową przekazana stan w `Response` obiekcie jako `accountState` .

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

Aby dowiedzieć się więcej, Przeczytaj o [kompilowaniu aplikacji jednostronicowej (Spa)](scenario-spa-overview.md) przy użyciu MSAL.js.