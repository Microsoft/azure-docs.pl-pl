---
title: Zachowanie interakcyjnego monitu o żądanie (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak dostosować zachowanie monitu w wywołaniach interaktywnych przy użyciu biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL.js).
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 85f5269c3c2616070b826ecc38735c31617db537
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85477553"
---
# <a name="prompt-behavior-in-msaljs-interactive-requests"></a>Zachowanie monitu w MSAL.js interaktywnych żądań

Gdy użytkownik ustanowił aktywną sesję usługi Azure AD z wieloma kontami użytkowników, na stronie logowania usługi Azure AD zostanie domyślnie wyświetlony monit o wybranie konta przed kontynuowaniem logowania. Jeśli istnieje tylko jedna uwierzytelniona sesja z usługą Azure AD, użytkownicy nie będą widzieć środowiska wyboru konta.

Biblioteka MSAL.js (począwszy od wersji v 0.2.4) nie wysyła parametru monitu podczas żądań interaktywnych ( `loginRedirect` , `loginPopup` `acquireTokenRedirect` i `acquireTokenPopup` ), a tym samym nie wymusza żadnego zachowania monitu. W przypadku żądań tokenu dyskretnego za pomocą `acquireTokenSilent` metody MSAL.js przekazuje parametr monitu ustawiony na `none` .

Na podstawie scenariusza aplikacji można kontrolować zachowanie monitu dla żądań interaktywnych przez ustawienie parametru monitu w parametrach żądania przesłanych do metod. Na przykład jeśli chcesz wywołać środowisko wybierania konta:

```javascript
var request = {
    scopes: ["user.read"],
    prompt: 'select_account',
}

userAgentApplication.loginRedirect(request);
```


Podczas uwierzytelniania za pomocą usługi Azure AD można przekazywać następujące wartości monitu:

**Logowanie:** Ta wartość spowoduje wymuszenie wprowadzenia przez użytkownika poświadczeń w żądaniu uwierzytelniania.

**select_account:** Ta wartość umożliwi użytkownikowi wybranie wszystkich kont w sesji.

**zgoda:** Ta wartość spowoduje wyzwolenie dialogu uwierzytelniania OAuth, który umożliwia użytkownikom Przyznawanie uprawnień do aplikacji.

**Brak:** Ta wartość zapewni, że użytkownik nie zobaczy żadnego interakcyjnego monitu. Zaleca się, aby nie przekazywać tej wartości do metod interaktywnych w MSAL.js, ponieważ mogą one mieć nieoczekiwane zachowania. Zamiast tego należy użyć `acquireTokenSilent` metody w celu uzyskania wywołań dyskretnych.

## <a name="next-steps"></a>Następne kroki

Przeczytaj więcej na temat `prompt` parametru w [niejawnym protokole OAuth 2,0](v2-oauth2-implicit-grant-flow.md) , który jest używany przez bibliotekę MSAL.js.
