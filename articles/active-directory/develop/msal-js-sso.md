---
title: Logowanie jednokrotne (MSAL.js) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej na temat tworzenia funkcji logowania jednokrotnego przy użyciu biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL.js).
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 8080d4cf4c3f0091f7837b3fccead5474c42db55
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84690782"
---
# <a name="single-sign-on-with-msaljs"></a>Logowanie jednokrotne za pomocą biblioteki MSAL.js

Pojedyncze Sign-On (Logowanie jednokrotne) umożliwia użytkownikom wprowadzanie poświadczeń raz w celu zalogowania się i ustanowienie sesji, która może zostać ponownie użyta w wielu aplikacjach, bez konieczności ponownego uwierzytelniania. Zapewnia to bezproblemowe środowisko użytkownika i zmniejsza powtarzające się instrukcje dotyczące poświadczeń.

Usługa Azure AD udostępnia funkcje logowania jednokrotnego do aplikacji, ustawiając plik cookie sesji, gdy użytkownik uwierzytelnia się po raz pierwszy. Biblioteka MSAL.js umożliwia aplikacjom korzystanie z tego programu na kilka sposobów.

## <a name="sso-between-browser-tabs"></a>Logowanie jednokrotne między kartami przeglądarki

Gdy aplikacja zostanie otwarta na wielu kartach, a użytkownik najpierw zaloguje się na jednej karcie, użytkownik jest również zalogowany na innych kartach bez monitowania. MSAL.js buforu identyfikatora dla użytkownika w przeglądarce `localStorage` i podpisania użytkownika w aplikacji na innych otwartych kartach.

Domyślnie użycie MSAL.js, `sessionStorage` które nie zezwala na udostępnianie sesji między kartami. Aby uzyskać Logowanie jednokrotne między kartami, upewnij się, że ustawiono `cacheLocation` w MSAL.js, `localStorage` tak jak pokazano poniżej.

```javascript
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

## <a name="sso-between-apps"></a>Logowanie jednokrotne między aplikacjami

Po uwierzytelnieniu użytkownika plik cookie sesji jest ustawiany w domenie usługi Azure AD w przeglądarce. MSAL.js opiera się na tym pliku cookie sesji w celu zapewnienia logowania jednokrotnego dla użytkownika między różnymi aplikacjami. MSAL.js także buforuje tokeny identyfikatorów i tokeny dostępu użytkownika w ramach magazynu przeglądarki na domenę aplikacji. W efekcie zachowanie logowania jednokrotnego różni się w zależności od sytuacji:  

### <a name="applications-on-the-same-domain"></a>Aplikacje w tej samej domenie

Gdy aplikacje są hostowane w tej samej domenie, użytkownik może zalogować się do aplikacji jeden raz, a następnie uwierzytelnić się w innych aplikacjach bez monitu. MSAL.js korzysta z tokenów w pamięci podręcznej dla użytkownika w domenie w celu zapewnienia logowania jednokrotnego.

### <a name="applications-on-different-domain"></a>Aplikacje w innej domenie

W przypadku, gdy aplikacje są hostowane w różnych domenach, MSAL.js w domenie B nie można uzyskać dostępu do tokenów znajdujących się w domenie A.

Oznacza to, że gdy użytkownicy zalogowani w domenie A przejdą do aplikacji w domenie B, zostaną przekierowani lub zostanie wyświetlony monit ze stroną usługi Azure AD. Ponieważ usługa Azure AD nadal zawiera plik cookie sesji użytkownika, użytkownik zostanie zalogowany i nie będzie musiał ponownie wprowadzić poświadczeń. Jeśli użytkownik ma wiele kont użytkowników w sesji z usługą Azure AD, użytkownik zostanie poproszony o wybranie odpowiedniego konta w celu zalogowania się.

### <a name="automatically-select-account-on-azure-ad"></a>Automatycznie wybierz konto w usłudze Azure AD

W niektórych przypadkach aplikacja ma dostęp do kontekstu uwierzytelniania użytkownika i chce uniknąć wyświetlania konta usługi Azure AD w przypadku zalogowania się z wieloma kontami.  Można to zrobić na kilka różnych sposobów:

**Identyfikator sesji (SID)**

Identyfikator sesji to [opcjonalne](active-directory-optional-claims.md) , które można skonfigurować w tokenach identyfikatorów. To zastrzeżenie umożliwia aplikacji identyfikację sesji usługi Azure AD użytkownika niezależną od nazwy konta użytkownika lub użytkownika. Można przekazać identyfikator SID w parametrach żądania do `acquireTokenSilent` wywołania. Pozwoli to usłudze Azure AD pominąć wybór konta. Identyfikator SID jest powiązany z plikiem cookie sesji i nie będzie miał kontekstów między przeglądarkami.

```javascript
var request = {
    scopes: ["user.read"],
    sid: sid
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

> [!Note]
> Identyfikatora SID można używać tylko z dyskretnymi żądaniami uwierzytelniania `acquireTokenSilent` wywoływanymi w MSAL.js.
Kroki konfigurowania opcjonalnych oświadczeń w manifeście aplikacji można znaleźć [tutaj](active-directory-optional-claims.md).

**Korzystanie z wskazówki logowania**

Jeśli nie masz skonfigurowanego lub nie chcesz ominąć monitu wyboru konta w ramach wywołań uwierzytelniania interakcyjnego, możesz to zrobić, podając `login_hint` parametry żądania i opcjonalnie `domain_hint` tak jak `extraQueryParameters` w metodzie MSAL.js Interactive ( `loginPopup` , `loginRedirect` `acquireTokenPopup` i `acquireTokenRedirect` ). Na przykład:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.loginRedirect(request);
```

Możesz uzyskać wartości dla login_hint i domain_hint, odczytując oświadczenia zwrócone w tokenie identyfikatora dla użytkownika.

* **loginHint** powinna być ustawiona na wartość `preferred_username` Claim w tokenie ID.

* **domain_hint** jest wymagana tylko w przypadku korzystania z urzędu/typowe. Warunek domeny jest określany na podstawie identyfikatora dzierżawy (TID).  Jeśli `tid` w tokenie ID jest to `9188040d-6c67-4c5b-b112-36a304b66dad` odbiorca. W przeciwnym razie jest to organizacja.

Przeczytaj [tutaj](v2-oauth2-implicit-grant-flow.md) , aby uzyskać więcej informacji na temat wartości wskazówki logowania i wskazówki dotyczącej domeny.

> [!Note]
> Nie można jednocześnie przekazać identyfikatorów SID i login_hint. Spowoduje to powstanie odpowiedzi na błąd.

## <a name="sso-without-msaljs-login"></a>Logowanie jednokrotne bez logowania MSAL.js

Zgodnie z projektem, MSAL.js wymaga, aby metoda logowania była wywoływana w celu ustanowienia kontekstu użytkownika przed uzyskaniem tokenów dla interfejsów API. Ponieważ metody logowania są interaktywne, użytkownik widzi monit.

Istnieją pewne przypadki, w których aplikacje mają dostęp do kontekstu lub tokenu identyfikatora uwierzytelnionego użytkownika przez uwierzytelnianie zainicjowane w innej aplikacji i chcą korzystać z rejestracji jednokrotnej w celu uzyskania tokenów bez wcześniejszego zalogowania się za pomocą MSAL.js.

Oto przykład: użytkownik jest zalogowany do nadrzędnej aplikacji sieci Web, która hostuje inną aplikację JavaScript działającą jako dodatek lub dodatek.

Środowisko logowania jednokrotnego w tym scenariuszu można uzyskać w następujący sposób:

Przekaż `sid` parametr if available (lub `login_hint` Opcjonalnie `domain_hint` ) jako parametry żądania do `acquireTokenSilent` wywołania MSAL.js w następujący sposób:

```javascript
var request = {
    scopes: ["user.read"],
    loginHint: preferred_username,
    extraQueryParameters: {domain_hint: 'organizations'}
}

userAgentApplication.acquireTokenSilent(request).then(function(response) {
        const token = response.accessToken
    }
).catch(function (error) {  
        //handle error
});
```

## <a name="sso-in-adaljs-to-msaljs-update"></a>Logowanie jednokrotne w ADAL.js do MSAL.js Update

MSAL.js zapewnia dostępność funkcji za pomocą ADAL.js scenariuszy uwierzytelniania usługi Azure AD. Aby zapewnić, że migracja z ADAL.js MSAL.js prosta i aby uniknąć monitowania użytkowników o ponowne zalogowanie się, biblioteka odczytuje token identyfikatora reprezentujący sesję użytkownika w pamięci podręcznej ADAL.js i bezproblemowo podpisuje użytkownika w MSAL.js.  

Aby skorzystać z zachowania logowania jednokrotnego (SSO) podczas aktualizacji z ADAL.js, należy upewnić się, że biblioteki są używane `localStorage` na potrzeby tokenów buforowania. Ustaw wartość `cacheLocation` na `localStorage` w konfiguracji MSAL.js i ADAL.js podczas inicjowania w następujący sposób:


```javascript
// In ADAL.js
window.config = {
   clientId: 'g075edef-0efa-453b-997b-de1337c29185',
   cacheLocation: 'localStorage'
};

var authContext = new AuthenticationContext(config);


// In latest MSAL.js version
const config = {
    auth: {
        clientId: "abcd-ef12-gh34-ikkl-ashdjhlhsdg"
    },
    cache: {
        cacheLocation: 'localStorage'
    }
}

const myMSALObj = new UserAgentApplication(config);
```

Po skonfigurowaniu tej MSAL.js będzie można odczytać zapisany w pamięci podręcznej stan uwierzytelnionego użytkownika w ADAL.js i użyć go w celu zapewnienia logowania jednokrotnego w MSAL.js.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat sesji logowania jednokrotnego [i okresu istnienia tokenu](active-directory-configurable-token-lifetimes.md) w usłudze Azure AD.
