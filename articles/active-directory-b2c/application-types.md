---
title: Typy aplikacji obsługiwane przez Azure AD B2C
titleSuffix: Azure AD B2C
description: Dowiedz się więcej o typach aplikacji, których można używać z Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 07897823a3ba3b83e240e8e8dc005ea13b036fce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94952050"
---
# <a name="application-types-that-can-be-used-in-active-directory-b2c"></a>Typy aplikacji, które mogą być używane w Active Directory B2C
 
Azure Active Directory B2C (Azure AD B2C) obsługuje uwierzytelnianie dla różnych nowoczesnych architektur aplikacji. Wszystkie one są oparte na standardowych protokołach branżowych [OAuth 2.0](protocols-overview.md) lub [OpenID Connect](protocols-overview.md). W tym artykule opisano typy aplikacji, które można kompilować, niezależnie od preferowanego języka lub platformy. Ułatwia on także zrozumienie ogólnych scenariuszy przed rozpoczęciem tworzenia aplikacji.

Każda aplikacja, która używa Azure AD B2C musi być zarejestrowana w [dzierżawie Azure AD B2C](tutorial-create-tenant.md) przy użyciu [Azure Portal](https://portal.azure.com/). Proces rejestracji aplikacji zbiera i przypisuje wartości, takie jak:

* **Identyfikator aplikacji** , który jednoznacznie identyfikuje aplikację.
* **Adres URL odpowiedzi** , który może służyć do kierowania odpowiedzi z powrotem do aplikacji.

Każde żądanie wysyłane do Azure AD B2C określa **przepływ użytkownika** (wbudowane zasady) lub **zasady niestandardowe** kontrolujące zachowanie Azure AD B2C. Oba typy zasad umożliwiają tworzenie wysoce dostosowywalnego zestawu środowisk użytkownika.

Interakcja każdej aplikacji jest realizowana według następującego ogólnego schematu:

1. Aplikacja kieruje użytkownika do punktu końcowego v 2.0 w celu wykonania [zasad](user-flow-overview.md).
2. Użytkownik wypełnia zasady zgodnie z definicją zasad.
3. Aplikacja otrzymuje token zabezpieczający z punktu końcowego v 2.0.
4. Aplikacja używa tokenu zabezpieczającego do uzyskiwania dostępu do chronionych informacji lub chronionego zasobu.
5. Serwer zasobów weryfikuje token zabezpieczający, aby sprawdzić możliwość przyznania dostępu.
6. Aplikacja okresowo odświeża token zabezpieczający.

Te kroki mogą się nieco różnić w zależności od typu kompilowanej aplikacji.

## <a name="web-applications"></a>Aplikacje internetowe

W przypadku aplikacji sieci Web (w tym .NET, PHP, Java, Ruby, Python i Node.js), które są hostowane na serwerze i dostępne za pomocą przeglądarki, Azure AD B2C obsługuje [OpenID Connect Connect](protocols-overview.md) dla wszystkich środowisk użytkownika. W Azure AD B2C implementacji OpenID Connect Connect aplikacja sieci Web inicjuje środowisko użytkownika przez wystawienie żądań uwierzytelniania do usługi Azure AD. Wynikiem żądania jest token `id_token`. Ten token zabezpieczający reprezentuje tożsamość użytkownika. Zawiera także informacje o użytkowniku w formie oświadczeń:

```json
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Dowiedz się więcej o typach tokenów i oświadczeń dostępnych dla aplikacji w [odniesieniu do tokenów Azure AD B2C](tokens-overview.md).

W aplikacji sieci Web każde wykonanie [zasad](user-flow-overview.md) wykonuje następujące czynności:

1. Użytkownik przegląda aplikację sieci Web.
2. Aplikacja sieci Web przekierowuje użytkownika do Azure AD B2C wskazującą, że zasady zostały wykonane.
3. Użytkownik ukończy zasady.
4. Azure AD B2C zwraca `id_token` do przeglądarki.
5. `id_token`Jest on ogłaszany w identyfikatorze URI przekierowania.
6. `id_token`Została sprawdzona weryfikacja i ustawiono plik cookie sesji.
7. Do użytkownika zostanie zwrócona bezpieczna strona.

Sprawdzenie tokenu `id_token` przy użyciu publicznego klucza podpisywania otrzymanego z usługi Azure AD jest wystarczające do zweryfikowania tożsamości użytkownika. Ten proces ustawia również plik cookie sesji, który może służyć do identyfikowania użytkownika na kolejnych żądaniach strony.

Aby zapoznać się z tym scenariuszem w działaniu, wypróbuj jeden z przykładów kodu logowania aplikacji sieci Web w [sekcji wprowadzenie](overview.md).

Oprócz ułatwienia prostego logowania aplikacja serwera sieci Web może również potrzebować dostępu do usługi sieci Web zaplecza. W takim przypadku aplikacja sieci Web może wykonywać nieco inne [OpenID Connect połączenia](openid-connect.md) i uzyskiwać tokeny przy użyciu kodów autoryzacji i odświeżania tokenów. Ten scenariusz jest opisany w [sekcji dotyczącej interfejsów API sieci Web](#web-apis) poniżej.

## <a name="single-page-applications"></a>Aplikacje jednostronicowe
Wiele nowoczesnych aplikacji sieci Web jest skompilowanych jako aplikacje jednostronicowe po stronie klienta ("aplikacji jednostronicowych"). Deweloperzy zapisują je przy użyciu języka JavaScript lub środowiska SPA, takiego jak kątowy, Vue i reagują. Aplikacje te działają w przeglądarce internetowej i mają inne cechy uwierzytelniania niż tradycyjne aplikacje sieci Web po stronie serwera.

Azure AD B2C dostępne są **dwie** opcje umożliwiające jednostronicowe aplikacje logowania użytkowników i uzyskiwanie tokenów w celu uzyskania dostępu do usług zaplecza lub interfejsów API sieci Web:

### <a name="authorization-code-flow-with-pkce"></a>Przepływ kodu autoryzacji (z PKCE)
- [Przepływ kodu autoryzacji OAuth 2,0 (z PKCE)](./authorization-code-flow.md). Przepływ kodu autoryzacji umożliwia aplikacji wymianę kodu autoryzacji dla tokenów **identyfikatora** , aby reprezentować tokeny uwierzytelnionego użytkownika i **dostępu** , które są konieczne do wywołania chronionych interfejsów API. Ponadto zwraca tokeny **odświeżania** , które zapewniają długoterminowy dostęp do zasobów w imieniu użytkowników, bez konieczności interakcji z tymi użytkownikami. 

Jest to **zalecane** podejście. Tokeny odświeżania o ograniczonym okresie istnienia ułatwiają aplikacji dostosowanie do [nowoczesnej ochrony plików cookie w przeglądarce](../active-directory/develop/reference-third-party-cookies-spas.md), takich jak Safari itp.

Aby skorzystać z tego przepływu, aplikacja może używać biblioteki uwierzytelniania, która obsługuje tę funkcję, na przykład [MSAL.js 2. x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser).

<!-- ![Single-page applications-auth](./media/tutorial-single-page-app/spa-app-auth.svg) -->
![Aplikacje jednostronicowe — uwierzytelnianie](./media/tutorial-single-page-app/active-directory-oauth-code-spa.png)

### <a name="implicit-grant-flow"></a>Niejawny przepływ dotacji
- [Niejawny przepływ OAuth 2,0](implicit-flow-single-page-application.md). Niektóre struktury, takie jak [MSAL.js 1. x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core), obsługują tylko niejawny przepływ. Przepływ niejawnego przydzielenia umożliwia aplikacji uzyskanie tokenów dotyczących **identyfikatorów** i **dostępu** . W przeciwieństwie do przepływu kodu autoryzacji niejawny przepływ przyznania nie zwraca **tokenu odświeżania**. 

Ten przepływ uwierzytelniania nie obejmuje scenariuszy aplikacji, które wykorzystują Międzyplatformowe struktury JavaScript, takie jak elektron i reagowanie na nie. Te scenariusze wymagają dalszych możliwości interakcji z natywnymi platformami.

## <a name="web-apis"></a>Interfejsy API sieci Web

Azure AD B2C można użyć do zabezpieczenia usług sieci Web, takich jak RESTful internetowy interfejs API aplikacji. Interfejsy API sieci Web mogą zabezpieczać swoje dane za pomocą protokołu OAuth 2.0, uwierzytelniając żądania przychodzące HTTP przy użyciu tokenów. Element wywołujący interfejs API sieci Web dołącza token w nagłówku autoryzacji żądania HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
``` 

Interfejs API sieci Web może następnie użyć tego tokenu do zweryfikowania tożsamości elementu wywołującego interfejs API oraz do wyodrębnienia informacji o elemencie wywołującym z oświadczeń zakodowanych w tokenie. Aby uzyskać więcej informacji o typach tokenów i oświadczeń dostępnych dla aplikacji, zobacz [informacje o tokenach usługi Azure AD B2C](tokens-overview.md).

Internetowy interfejs API może odbierać tokeny z wielu typów klientów, takich jak aplikacje sieci Web, aplikacje klasyczne i mobilne, aplikacje jednostronicowe, demony po stronie serwera i inne interfejsy API sieci Web. Oto przykład kompletnego przepływu dla aplikacji sieci Web, która wywołuje interfejs API sieci Web:

1. Aplikacja sieci Web wykonuje zasady, a użytkownik kończy pracę użytkownika.
2. Azure AD B2C zwraca (OpenID Connect Connect) `id_token` i kod autoryzacji do przeglądarki.
3. Przeglądarka publikuje `id_token` kod autoryzacji i w identyfikatorze URI przekierowania.
4. Serwer sieci Web sprawdza poprawność `id_token` i ustawia plik cookie sesji.
5. Serwer sieci Web prosi Azure AD B2C o `access_token` podanie kodu autoryzacji, identyfikatora klienta aplikacji i poświadczeń klienta.
6. `access_token`I `refresh_token` są zwracane do serwera sieci Web.
7. Interfejs API sieci Web jest wywoływany przy użyciu `access_token` w nagłówku autoryzacji.
8. Internetowy interfejs API sprawdza poprawność tokenu.
9. Zabezpieczone dane są zwracane do aplikacji sieci Web.

Aby dowiedzieć się więcej o kodach autoryzacji, tokenach odświeżania i krokach uzyskiwania tokenów, zapoznaj się z informacjami na temat [protokołu OAuth 2.0](authorization-code-flow.md).

Aby dowiedzieć się, jak zabezpieczyć interfejs API sieci Web przy użyciu usługi Azure AD B2C, zapoznaj się z samouczkami dotyczącymi interfejsu API sieci Web w [sekcji Wprowadzenie](overview.md).

## <a name="mobile-and-native-applications"></a>Aplikacje mobilne i natywne

Aplikacje, które są zainstalowane na urządzeniach, takich jak aplikacje mobilne i klasyczne, często muszą uzyskiwać dostęp do usług zaplecza lub interfejsów API sieci Web w imieniu użytkowników. Możesz dodać dostosowane środowiska zarządzania tożsamościami do natywnych aplikacji i bezpiecznie wywoływać usługi zaplecza, używając Azure AD B2C i [przepływu kodu autoryzacji OAuth 2,0](authorization-code-flow.md).

W tym przepływie aplikacja wykonuje [zasady](user-flow-overview.md) i odbiera `authorization_code` z usługi Azure AD po zakończeniu zasad przez użytkownika. `authorization_code`Reprezentuje uprawnienie aplikacji do wywoływania usług zaplecza w imieniu użytkownika, który jest aktualnie zalogowany. Aplikacja może następnie wymienić `authorization_code` w tle dla `access_token` i `refresh_token` .  Aplikacja może używać usługi `access_token` do uwierzytelniania w interfejsie API sieci Web zaplecza w żądaniach HTTP. Może również użyć tokenu `refresh_token` do pobrania nowego tokenu `access_token`, gdy wygaśnie stary.

## <a name="current-limitations"></a>Bieżące ograniczenia

### <a name="unsupported-application-types"></a>Nieobsługiwane typy aplikacji

#### <a name="daemonsserver-side-applications"></a>Demony/aplikacje po stronie serwera

Aplikacje zawierające długotrwałe procesy lub działające bez obecności użytkownika potrzebują również metody uzyskiwania dostępu do zabezpieczonych zasobów, takich jak interfejsy API sieci Web. Te aplikacje mogą uwierzytelniać i uzyskiwać tokeny przy użyciu tożsamości aplikacji (a nie delegowanej tożsamości użytkownika) oraz za pomocą przepływu poświadczeń klienta OAuth 2,0. Przepływ poświadczeń klienta nie jest taki sam, jak w przypadku usługi w imieniu i w imieniu, nie powinien być używany do uwierzytelniania między serwerami.

Mimo że przesyłanie poświadczeń klienta OAuth 2,0 nie jest obecnie obsługiwane bezpośrednio przez usługę uwierzytelniania Azure AD B2C, można skonfigurować przepływ poświadczeń klienta przy użyciu usługi Azure AD i punktu końcowego Microsoft Identity platform/token dla aplikacji w dzierżawie Azure AD B2C. Dzierżawa Azure AD B2C udostępnia pewne funkcje z dzierżawami przedsiębiorstwa usługi Azure AD.

Aby skonfigurować przepływ poświadczeń klienta, zobacz [Azure Active Directory v 2.0 i przepływ poświadczeń klienta OAuth 2,0](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md). Pomyślne uwierzytelnienie powoduje otrzymanie tokenu sformatowanego w taki sposób, aby mógł być używany przez usługę Azure AD zgodnie z opisem w temacie [odwołanie do tokenu usługi Azure AD](../active-directory/develop/id-tokens.md).

Instrukcje dotyczące rejestrowania aplikacji zarządzania programu znajdują się w temacie [zarządzanie Azure AD B2C przy użyciu Microsoft Graph](microsoft-graph-get-started.md).

#### <a name="web-api-chains-on-behalf-of-flow"></a>Łańcuchy interfejsu API sieci Web (przepływ „w imieniu”)

Wiele architektur obejmuje interfejs API sieci Web, który musi wywołać inny podrzędny interfejs API sieci Web, przy czym oba interfejsy są zabezpieczane przez usługę Azure AD B2C. Ten scenariusz jest typowy w natywnych klientach z zapleczem interfejsu API sieci Web i wywołuje usługi online firmy Microsoft, takiej jak Microsoft Graph API.

Ten scenariusz obejmujący łańcuch interfejsów API sieci Web może być obsługiwany przy użyciu przyznania poświadczeń elementu nośnego OAuth 2.0 JWT, określanego również jako przepływ „w imieniu”.  Jednak przepływ „w imieniu” nie jest obecnie wdrażany w usłudze Azure AD B2C.

### <a name="faulted-apps"></a>Uszkodzone aplikacje

Nie należy edytować Azure AD B2C aplikacji w następujący sposób:

- W innych portalach zarządzania aplikacjami, takich jak [portal rejestracji aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade).
- Przy użyciu interfejs API programu Graph lub programu PowerShell.

Jeśli edytujesz aplikację Azure AD B2C poza Azure Portal, stanie się ona błędną aplikacją i nie będzie można jej używać w Azure AD B2C. Usuń aplikację i utwórz ją ponownie.

Aby usunąć aplikację, przejdź do [portalu rejestracji aplikacji](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade) i Usuń aplikację. Aby aplikacja była widoczna, musisz być jej właścicielem (a nie tylko administratorem dzierżawy).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat wbudowanych zasad oferowanych przez [przepływy użytkowników w Azure Active Directory B2C](user-flow-overview.md).