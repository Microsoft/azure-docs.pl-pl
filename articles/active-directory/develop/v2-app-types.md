---
title: Typy aplikacji dla platformy tożsamości firmy Microsoft | Azure
description: Typy aplikacji i scenariuszy obsługiwane przez platformę tożsamości firmy Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/13/2020
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev, fasttrack-edit, contperf-fy21q2
ms.openlocfilehash: 7ec309f016e73642262399bd75e7b5146bc5e497
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98752775"
---
# <a name="application-types-for-the-microsoft-identity-platform"></a>Typy aplikacji dla platformy tożsamości firmy Microsoft

Platforma tożsamości firmy Microsoft obsługuje uwierzytelnianie dla różnorodnych architektur nowoczesnych aplikacji, z których wszystkie są oparte na standardach branżowych [OAuth 2,0 lub OpenID Connect Connect](active-directory-v2-protocols.md). W tym artykule opisano typy aplikacji, które można skompilować przy użyciu platformy tożsamości firmy Microsoft, niezależnie od preferowanego języka lub platformy. Te informacje ułatwiają zrozumienie scenariuszy wysokiego poziomu przed rozpoczęciem pracy z kodem w [scenariuszach aplikacji](authentication-flows-app-scenarios.md#application-scenarios).

## <a name="the-basics"></a>Podstawy

Należy zarejestrować każdą aplikację korzystającą z platformy tożsamości firmy Microsoft w [Rejestracje aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908)Azure Portal. Proces rejestracji aplikacji zbiera i przypisuje te wartości dla aplikacji:

* **Identyfikator aplikacji (klienta)** , który jednoznacznie identyfikuje aplikację
* **Identyfikator URI przekierowania** , za pomocą którego można kierować odpowiedzi z powrotem do aplikacji
* Kilka innych wartości specyficznych dla scenariusza, takich jak obsługiwane typy kont

Aby uzyskać szczegółowe informacje, Dowiedz się, jak [zarejestrować aplikację](quickstart-register-app.md).

Po zarejestrowaniu aplikacji aplikacja komunikuje się z platformą tożsamości firmy Microsoft przez wysyłanie żądań do punktu końcowego. Udostępniamy struktury i biblioteki typu open source, które obsługują szczegóły tych żądań. Istnieje również możliwość zaimplementowania logiki uwierzytelniania samodzielnie przez tworzenie żądań do tych punktów końcowych:

```HTTP
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Aplikacje jednostronicowe (JavaScript)

Wiele nowoczesnych aplikacji ma fronton aplikacji jednostronicowej oparty głównie na języku JavaScript, często z platformą, taką jak kątowy, reagowanie lub Vue. Platforma tożsamości firmy Microsoft obsługuje te aplikacje przy użyciu protokołu [OpenID Connect Connect](v2-protocols-oidc.md) na potrzeby uwierzytelniania oraz [niejawnego przepływu](v2-oauth2-implicit-grant-flow.md) uwierzytelniania OAuth 2,0 lub nowszego [kodu autoryzacji uwierzytelniania OAuth 2,0 + PKCE przepływu](v2-oauth2-auth-code-flow.md) na potrzeby autoryzacji (patrz poniżej).

Poniższy diagram przepływu przedstawia przyznanie kodu autoryzacji OAuth 2,0 (wraz ze szczegółami dotyczącymi pominiętych PKCE), w którym aplikacja otrzymuje kod z punktu końcowego platformy tożsamości firmy Microsoft i realizuje `authorize` go w celu uzyskania tokenów i tokenów odświeżania przy użyciu żądań sieci Web między lokacjami. Token odświeżania wygasa co 24 godziny, a aplikacja musi zażądać innego kodu. Oprócz tokenu dostępu, `id_token` który reprezentuje zalogowanego użytkownika do aplikacji klienckiej, zazwyczaj żąda również za pomocą tego samego przepływu i/lub oddzielnego żądania połączenia OpenID Connect (nie pokazano tutaj).

:::image type="content" source="media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.svg" alt-text="Diagram przedstawiający przepływ kodu autoryzacji OAuth 2 między aplikacją jednostronicową a punktem końcowym usługi tokenu zabezpieczającego." border="false":::

Aby zapoznać się z tym scenariuszem w działaniu, zapoznaj się z [samouczkiem: Logowanie użytkowników i wywoływanie interfejsu API Microsoft Graph z poziomu skryptu JavaScript Spa przy użyciu przepływu kodu uwierzytelniania](tutorial-v2-javascript-auth-code.md).

### <a name="authorization-code-flow-vs-implicit-flow"></a>Przepływ kodu autoryzacji a niejawny przepływ

W przypadku większości historii uwierzytelniania OAuth 2,0 zalecanym sposobem [jest](v2-oauth2-implicit-grant-flow.md) skompilowanie aplikacji jednostronicowych. Wraz z usunięciem [plików cookie innych firm](reference-third-party-cookies-spas.md) i [zwróceniem uwagi](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14) na problemy związane z bezpieczeństwem w odniesieniu do niejawnego przepływu został przeniesiony do przepływu kodu autoryzacji dla aplikacji jednostronicowych.

Aby zapewnić zgodność aplikacji w przeglądarce Safari i innych przeglądarek świadomych zachowania poufności, nie zalecamy już korzystania z niejawnego przepływu i zamiast tego zaleca się przepływ kodu autoryzacji.

## <a name="web-apps"></a>Aplikacje internetowe

W przypadku aplikacji sieci Web (.NET, PHP, Java, Ruby, Python, Node), do których użytkownik uzyskuje dostęp za pomocą przeglądarki, można użyć programu [OpenID Connect Connect](active-directory-v2-protocols.md) do logowania użytkownika. W programie OpenID Connect Connect aplikacja sieci Web otrzymuje token ID. Token identyfikatora to token zabezpieczający, który weryfikuje tożsamość użytkownika i zawiera informacje o użytkowniku w formie oświadczeń:

```JSON
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Więcej informacji o różnych typach tokenów używanych na platformie tożsamości firmy Microsoft można znaleźć w dokumentacji dotyczącej [tokenów dostępu](access-tokens.md) i [id_token](id-tokens.md)

W aplikacjach serwera sieci Web przepływ uwierzytelniania logowania wykonuje następujące czynności wysokiego poziomu:

![Pokazuje przepływ uwierzytelniania aplikacji sieci Web](./media/v2-app-types/convergence-scenarios-webapp.svg)

Tożsamość użytkownika można zapewnić, sprawdzając token identyfikatora przy użyciu publicznego klucza podpisywania otrzymanego z platformy tożsamości firmy Microsoft. Ustawiono plik cookie sesji, który może służyć do identyfikowania użytkownika na kolejnych żądaniach strony.

Aby wyświetlić ten scenariusz w działaniu, wypróbuj przykłady kodu w [aplikacji sieci Web, która jest w scenariuszu użytkownika](scenario-web-app-sign-user-overview.md).

Oprócz prostej logowania aplikacja serwera sieci Web może potrzebować dostępu do innej usługi sieci Web, takiej jak interfejs API REST. W takim przypadku aplikacja serwera sieci Web jest zaangażowana w połączony przepływ OpenID Connect Connect i OAuth 2,0 przy użyciu [przepływu kodu autoryzacji oauth 2,0](v2-oauth2-auth-code-flow.md). Aby uzyskać więcej informacji na temat tego scenariusza, zapoznaj [się z artykułem wprowadzenie do usługi Web Apps i interfejsów API sieci Web](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-WebAPI-OpenIDConnect-DotNet).

## <a name="web-apis"></a>Interfejsy API sieci Web

Platformy tożsamości firmy Microsoft można używać do zabezpieczania usług sieci Web, takich jak internetowy interfejs API aplikacji RESTful. Interfejsy API sieci Web można zaimplementować na wielu platformach i w różnych językach. Można je również zaimplementować przy użyciu wyzwalaczy HTTP w Azure Functions. Zamiast tokenów identyfikatorów i plików cookie sesji, internetowy interfejs API używa tokenu dostępu OAuth 2,0 do zabezpieczania danych i uwierzytelniania żądań przychodzących. Obiekt wywołujący interfejs API sieci Web dołącza token dostępu w nagłówku autoryzacji żądania HTTP, tak jak to:

```HTTP
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Internetowy interfejs API używa tokenu dostępu do weryfikowania tożsamości obiektu wywołującego interfejsu API i wyodrębniania informacji o wywołującym z oświadczeń, które są zakodowane w tokenie dostępu. Więcej informacji o różnych typach tokenów używanych na platformie tożsamości firmy Microsoft można znaleźć w dokumentacji dotyczącej [tokenów dostępu](access-tokens.md) i [id_token](id-tokens.md) .

Internetowy interfejs API może zapewnić użytkownikom możliwość wyboru lub rezygnacji z określonych funkcji lub danych przez ujawnienie uprawnień, nazywanych również [zakresami](v2-permissions-and-consent.md). Aby aplikacja wywołująca uzyskała uprawnienie do zakresu, użytkownik musi wyrazić zgodę na zakres w przepływie. Platforma tożsamości firmy Microsoft prosi użytkownika o zgodę, a następnie rejestruje uprawnienia we wszystkich tokenach dostępu odbieranych przez internetowy interfejs API. Internetowy interfejs API sprawdza poprawność tokenów dostępu odbieranych na każdym wywołaniu i wykonuje sprawdzanie autoryzacji.

Internetowy interfejs API może odbierać tokeny dostępu z wszystkich typów aplikacji, w tym aplikacji serwera sieci Web, aplikacji klasycznych i mobilnych, aplikacji jednostronicowych, demonów po stronie serwera, a nawet innych interfejsów API sieci Web. Przepływ wysokiego poziomu dla interfejsu API sieci Web wygląda następująco:

![Pokazuje przepływ uwierzytelniania interfejsu API sieci Web](./media/v2-app-types/convergence-scenarios-webapi.svg)

Aby dowiedzieć się, jak zabezpieczyć interfejs API sieci Web przy użyciu tokenów dostępu OAuth2, zapoznaj się z przykładami kodu interfejsu API sieci Web w [scenariuszu chronionego interfejsu API sieci Web](scenario-protected-web-api-overview.md).

W wielu przypadkach interfejsy API sieci Web muszą również wykonywać żądania wychodzące do innych podrzędnych interfejsów API sieci Web zabezpieczonych przez platformę tożsamości firmy Microsoft. W tym celu interfejsy API sieci Web mogą korzystać z usługi **Flow,** co umożliwia interfejsowi API sieci Web wymianę przychodzącego tokenu dostępu dla innego tokenu dostępu, który będzie używany w żądaniach wychodzących. Aby uzyskać więcej informacji, zobacz [Microsoft Identity platform i OAuth 2,0 w imieniu usługi Flow](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Aplikacje mobilne i natywne

Aplikacje zainstalowane na urządzeniu, takie jak aplikacje mobilne i klasyczne, często muszą uzyskiwać dostęp do usług zaplecza lub interfejsów API sieci Web, które przechowują dane i wykonują funkcje w imieniu użytkownika. Te aplikacje mogą dodawać logowanie i autoryzację do usług zaplecza przy użyciu [przepływu kodu autoryzacji OAuth 2,0](v2-oauth2-auth-code-flow.md).

W tym przepływie aplikacja otrzymuje kod autoryzacji z platformy tożsamości firmy Microsoft po zalogowaniu się użytkownika. Kod autoryzacji reprezentuje uprawnienia aplikacji do wywoływania usług zaplecza w imieniu zalogowanego użytkownika. Aplikacja może wymienić kod autoryzacji w tle dla tokenu dostępu OAuth 2,0 i tokenu odświeżania. Aplikacja może używać tokenu dostępu do uwierzytelniania do interfejsów API sieci Web w żądaniach HTTP i używać tokenu odświeżania do uzyskiwania nowych tokenów dostępu po wygaśnięciu starszych tokenów dostępu.

![Pokazuje przepływ uwierzytelniania aplikacji natywnych](./media/v2-app-types/convergence-scenarios-native.svg)

> [!NOTE]
> Jeśli aplikacja używa domyślnego programu System WebView, zapoznaj się z informacjami o funkcji "Potwierdź moje logowanie" i kodzie błędu AADSTS50199 w temacie [uwierzytelnianie i kody błędów autoryzacji usługi Azure AD](reference-aadsts-error-codes.md).

## <a name="daemons-and-server-side-apps"></a>Demony i aplikacje po stronie serwera

Aplikacje, które mają długotrwałe procesy lub działają bez interakcji z użytkownikiem, muszą również mieć dostęp do zabezpieczonych zasobów, takich jak interfejsy API sieci Web. Te aplikacje mogą uwierzytelniać i uzyskiwać tokeny przy użyciu tożsamości aplikacji, a nie delegowanej tożsamości użytkownika, za pomocą przepływu poświadczeń klienta OAuth 2,0. Tożsamość aplikacji można udowodnić przy użyciu klucza tajnego klienta lub certyfikatu. Aby uzyskać więcej informacji, zobacz [aplikacje konsolowe demona .NET Core przy użyciu platformy tożsamości firmy Microsoft](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2).

W tym przepływie aplikacja współdziała bezpośrednio z `/token` punktem końcowym w celu uzyskania dostępu:

![Pokazuje przepływ uwierzytelniania aplikacji demona](./media/v2-app-types/convergence-scenarios-daemon.svg)

Aby skompilować aplikację demona, zapoznaj się z [dokumentacją dotyczącą poświadczeń klienta](v2-oauth2-client-creds-grant-flow.md)lub wypróbuj [przykładową aplikację platformy .NET](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już typy aplikacji obsługiwanych przez platformę tożsamości firmy Microsoft, Dowiedz się więcej na temat [uwierzytelniania OAuth 2,0 i OpenID Connect](active-directory-v2-protocols.md) , aby uzyskać informacje o składnikach protokołu używanych w różnych scenariuszach.
