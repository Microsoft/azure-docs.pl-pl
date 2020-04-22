---
title: Typy aplikacji dla platformy tożsamości firmy Microsoft | Azure
description: Typy aplikacji i scenariuszy obsługiwane przez punkt końcowy platformy tożsamości firmy Microsoft (wersja 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur
ms.custom: aaddev
ms.openlocfilehash: def92071496716f90b24158a50e4a5233e93c994
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677985"
---
# <a name="application-types-for-microsoft-identity-platform"></a>Typy aplikacji dla platformy tożsamości firmy Microsoft

Punkt końcowy platformy tożsamości firmy Microsoft (w wersji 2.0) obsługuje uwierzytelnianie dla różnych nowoczesnych architektur aplikacji, wszystkie oparte na standardowych protokołach [OAuth 2.0 lub OpenID Connect](active-directory-v2-protocols.md). W tym artykule opisano typy aplikacji, które można utworzyć przy użyciu platformy tożsamości firmy Microsoft, niezależnie od preferowanego języka lub platformy. Informacje te mają na celu ułatwienie zrozumienia scenariuszy wysokiego poziomu przed [rozpoczęciem pracy z kodem](v2-overview.md#getting-started).

## <a name="the-basics"></a>Podstawy

Należy zarejestrować każdą aplikację, która korzysta z punktu końcowego platformy tożsamości firmy Microsoft w nowym [portalu rejestracji aplikacji](https://go.microsoft.com/fwlink/?linkid=2083908). Proces rejestracji aplikacji zbiera i przypisuje te wartości dla aplikacji:

* **Identyfikator aplikacji (klienta),** który jednoznacznie identyfikuje aplikację
* Identyfikator **URI przekierowania,** którego można użyć do bezpośredniego reagowania z powrotem do aplikacji
* Kilka innych wartości specyficznych dla scenariusza, takich jak obsługiwane typy kont

Aby uzyskać szczegółowe informacje, dowiedz się, jak [zarejestrować aplikację](quickstart-register-app.md).

Po zarejestrowaniu aplikacji aplikacja komunikuje się z platformą tożsamości firmy Microsoft, wysyłając żądania do punktu końcowego. Zapewniamy platform open source i biblioteki, które obsługują szczegóły tych żądań. Istnieje również możliwość samodzielnego zaimplementowania logiki uwierzytelniania przez utworzenie żądań do tych punktów końcowych:

```HTTP
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

## <a name="single-page-apps-javascript"></a>Aplikacje jednostronicowe (JavaScript)

Wiele nowoczesnych aplikacji ma jednostronicowy fronton aplikacji, który jest napisany głównie w języku JavaScript. Często jest napisany przy użyciu struktury, takich jak Angular, React lub Vue. Punkt końcowy platformy tożsamości firmy Microsoft obsługuje te aplikacje przy użyciu [przepływu niejawnego OAuth 2.0](v2-oauth2-implicit-grant-flow.md).

W tym przepływie aplikacja odbiera tokeny bezpośrednio z platformy tożsamości firmy Microsoft autoryzować punkt końcowy, bez wymiany serwera na serwer. Cała logika uwierzytelniania i obsługa sesji odbywa się w całości w kliencie JavaScript, bez dodatkowych przekierowań strony.

![Pokazuje niejawny przepływ uwierzytelniania](./media/v2-app-types/convergence-scenarios-implicit.svg)

Aby zobaczyć ten scenariusz w akcji, spróbuj jednego z przykładów kodu aplikacji jednostronicowej w sekcji [Wprowadzenie do platformy tożsamości firmy Microsoft.](v2-overview.md#getting-started)

## <a name="web-apps"></a>Aplikacje internetowe

W przypadku aplikacji internetowych (.NET, PHP, Java, Ruby, Python, Node), do których użytkownik uzyskuje dostęp za pośrednictwem przeglądarki, można użyć [OpenID Connect](active-directory-v2-protocols.md) do logowania użytkownika. W OpenID Connect aplikacja internetowa odbiera token identyfikatora. Token identyfikatora to token zabezpieczający, który weryfikuje tożsamość użytkownika i zawiera informacje o użytkowniku w postaci oświadczeń:

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

Dalsze szczegóły dotyczące różnych typów tokenów używanych w punkcie końcowym platformy tożsamości firmy Microsoft są dostępne w odwołaniu do [tokenu dostępu](access-tokens.md) i [id_token odwołania](id-tokens.md)

W aplikacjach serwera sieci web przepływ uwierzytelniania logowania wykonuje następujące kroki wysokiego poziomu:

![Pokazuje przepływ uwierzytelniania aplikacji sieci Web](./media/v2-app-types/convergence-scenarios-webapp.svg)

Można zapewnić tożsamość użytkownika, sprawdzając token identyfikatora za pomocą publicznego klucza podpisywania, który jest odbierany z punktu końcowego platformy tożsamości firmy Microsoft. Plik cookie sesji jest ustawiony, który może służyć do identyfikacji użytkownika w kolejnych żądaniach strony.

Aby zobaczyć ten scenariusz w akcji, spróbuj jednego z przykładów kodu logowania aplikacji sieci web w sekcji [Wprowadzenie do platformy tożsamości firmy Microsoft.](v2-overview.md#getting-started)

Oprócz prostego logowania aplikacja serwera sieci web może wymagać dostępu do innej usługi sieci web, takiej jak interfejs API REST. W takim przypadku aplikacja serwera sieci web włącza się w połączony przepływ OpenID Connect i OAuth 2.0, używając [przepływu kodu autoryzacyjnego OAuth 2.0](active-directory-v2-protocols.md). Aby uzyskać więcej informacji na temat tego scenariusza, przeczytaj o [rozpoczynaniu pracy z aplikacjami sieci Web i interfejsami API sieci Web](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>Interfejsy API sieci Web

Punktu końcowego platformy tożsamości firmy Microsoft można używać do zabezpieczania usług sieci web, takich jak interfejs API sieci Web restful aplikacji. Interfejsy API sieci Web można zaimplementować na wielu platformach i językach. Można je również zaimplementować przy użyciu wyzwalaczy HTTP w usłudze Azure Functions. Zamiast tokenów identyfikatorów i plików cookie sesji internetowy interfejs API używa tokenu dostępu OAuth 2.0 do zabezpieczania swoich danych i uwierzytelniania żądań przychodzących. Wywołujący internetowy interfejs API dołącza token dostępu w nagłówku autoryzacji żądania HTTP, w ten sposób:

```HTTP
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Internetowy interfejs API używa tokenu dostępu do weryfikacji tożsamości obiektu wywołującego interfejsu API i wyodrębniania informacji o wywołującym z oświadczeń zakodowanych w tokenie dostępu. Dalsze szczegóły dotyczące różnych typów tokenów używanych w punkcie końcowym platformy tożsamości firmy Microsoft są dostępne w odwołaniu do [tokenu dostępu](access-tokens.md) i [id_token](id-tokens.md) odwołania.

Internetowy interfejs API może dać użytkownikom możliwość wyrażenia zgody na określoną funkcjonalność lub dane lub rezygnację z nich, ujawniając uprawnienia, znane również jako [zakresy.](v2-permissions-and-consent.md) Aby aplikacja wywołująca uzyskała uprawnienia do zakresu, użytkownik musi wyrazić zgodę na zakres podczas przepływu. Punkt końcowy platformy tożsamości firmy Microsoft prosi użytkownika o uprawnienia, a następnie rejestruje uprawnienia we wszystkich tokenach dostępu odbieranych przez internetowy interfejs API. Internetowy interfejs API sprawdza poprawność tokenów dostępu, które otrzymuje przy każdym wywołaniu i przeprowadza kontrole autoryzacji.

Internetowy interfejs API może odbierać tokeny dostępu ze wszystkich typów aplikacji, w tym aplikacji serwera sieci Web, aplikacji komputerowych i mobilnych, aplikacji jednostronicowych, demonów po stronie serwera, a nawet innych interfejsów API sieci Web. Przepływ wysokiego poziomu dla internetowego interfejsu API wygląda następująco:

![Pokazuje przepływ uwierzytelniania interfejsu API w sieci Web](./media/v2-app-types/convergence-scenarios-webapi.svg)

Aby dowiedzieć się, jak zabezpieczyć internetowy interfejs API przy użyciu tokenów dostępu OAuth2, zapoznaj się z przykładami kodu internetowego interfejsu API w sekcji [Wprowadzenie do platformy tożsamości firmy Microsoft.](v2-overview.md#getting-started)

W wielu przypadkach interfejsy API sieci web muszą również wywrzeć żądania wychodzące do innych podrzędnych interfejsów API sieci web zabezpieczonych przez platformę tożsamości firmy Microsoft. W tym celu interfejsy API sieci web mogą korzystać z przepływu **w imieniu,** który umożliwia interfejsowi API sieci web do wymiany tokenu dostępu przychodzącego dla innego tokenu dostępu, który ma być używany w żądaniach wychodzących. Aby uzyskać więcej informacji, zobacz [Microsoft identity platform i OAuth 2.0 On-Behalf-Of flow](v2-oauth2-on-behalf-of-flow.md).

## <a name="mobile-and-native-apps"></a>Aplikacje mobilne i natywne

Aplikacje zainstalowane na urządzeniu, takie jak aplikacje mobilne i klasyczne, często wymagają dostępu do usług zaplecza lub internetowych interfejsów API, które przechowują dane i wykonują funkcje w imieniu użytkownika. Te aplikacje mogą dodawać logowanie i autoryzację do usług zaplecza przy użyciu [przepływu kodu autoryzacji OAuth 2.0](v2-oauth2-auth-code-flow.md).

W tym przepływie aplikacja otrzymuje kod autoryzacji z punktu końcowego platformy tożsamości firmy Microsoft, gdy użytkownik się zaloguje. Kod autoryzacji reprezentuje uprawnienie aplikacji do wywoływania usług zaplecza w imieniu zalogowanego użytkownika. Aplikacja może wymieniać kod autoryzacji w tle dla tokenu dostępu OAuth 2.0 i tokenu odświeżania. Aplikacja może używać tokenu dostępu do uwierzytelniania do internetowych interfejsów API w żądaniach HTTP i użyć tokenu odświeżania, aby uzyskać nowe tokeny dostępu po wygaśnięciu starszych tokenów dostępu.

![Pokazuje przepływ uwierzytelniania aplikacji natywnej](./media/v2-app-types/convergence-scenarios-native.svg)

## <a name="daemons-and-server-side-apps"></a>Demony i aplikacje po stronie serwera

Aplikacje, które mają długotrwałe procesy lub działają bez interakcji z użytkownikiem, również potrzebują sposobu dostępu do zabezpieczonych zasobów, takich jak internetowe interfejsy API. Te aplikacje mogą uwierzytelniać i pobieranie tokenów przy użyciu tożsamości aplikacji, a nie tożsamości delegowanej użytkownika, z przepływem poświadczeń klienta OAuth 2.0. Możesz udowodnić tożsamość aplikacji przy użyciu klucza tajnego klienta lub certyfikatu. Aby uzyskać więcej informacji, zobacz [.NET Core daemon console application using Microsoft identity platform](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2).

W tym przepływie aplikacja współdziała `/token` bezpośrednio z punktem końcowym w celu uzyskania dostępu:

![Pokazuje przepływ uwierzytelniania aplikacji demona](./media/v2-app-types/convergence-scenarios-daemon.svg)

Aby utworzyć aplikację demona, zobacz [dokumentację poświadczeń klienta](v2-oauth2-client-creds-grant-flow.md)lub spróbuj [użyć przykładowej aplikacji .NET](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
