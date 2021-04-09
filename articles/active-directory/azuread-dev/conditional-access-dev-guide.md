---
title: Wskazówki dla deweloperów dotyczące dostępu warunkowego usługi Azure AD
description: Wskazówki dla deweloperów i scenariusze dotyczące dostępu warunkowego usługi Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 02/28/2019
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: conceptual
ms.workload: identity
ROBOTS: NOINDEX
ms.openlocfilehash: b08b07ebcd6e45e95e23dff8686148889e9d4b39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96173923"
---
# <a name="developer-guidance-for-the-azure-active-directory-conditional-access-feature"></a>Wskazówki dla deweloperów dotyczące funkcji dostępu warunkowego Azure Active Directory

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

> [!NOTE]
> Aby uzyskać informacje na temat wersji platformy tożsamości firmy Microsoft w tym artykule, zobacz [wskazówki dla deweloperów dotyczące Azure Active Directory dostępu warunkowego](../develop/v2-conditional-access-dev-guide.md).

Funkcja dostępu warunkowego w usłudze Azure Active Directory (Azure AD) oferuje jeden z kilku sposobów zabezpieczania aplikacji oraz ochrony usługi. Dostęp warunkowy umożliwia deweloperom i klientom korporacyjnym ochronę usług na wiele sposobów, takich jak:

* Uwierzytelnianie wieloskładnikowe
* Zezwalanie na dostęp do określonych usług tylko zarejestrowanym urządzeniom usługi Intune
* Ograniczanie lokalizacji użytkowników i zakresów adresów IP

Aby uzyskać więcej informacji na temat pełnych możliwości dostępu warunkowego, zobacz [co to jest dostęp warunkowy](../conditional-access/overview.md).

W przypadku deweloperów tworzących aplikacje dla usługi Azure AD w tym artykule przedstawiono sposób korzystania z dostępu warunkowego, a także informacje o wpływie dostępu do zasobów, do których nie ma kontroli, które mogą mieć zastosowane zasady dostępu warunkowego. W tym artykule omówiono także skutki dostępu warunkowego w ramach przepływu, aplikacji sieci Web, uzyskiwania dostępu do Microsoft Graph i wywoływania interfejsów API.

Założono znajomość [pojedynczych i wielodostępnych](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json) aplikacji oraz [wspólnych wzorców uwierzytelniania](v1-authentication-scenarios.md) .

## <a name="how-does-conditional-access-impact-an-app"></a>Jak dostęp warunkowy wpływa na aplikację?

### <a name="app-types-impacted"></a>Typy aplikacji, których dotyczy problem

W większości typowych przypadków dostęp warunkowy nie zmienia zachowania aplikacji ani nie wymaga żadnych zmian od dewelopera. Tylko w niektórych przypadkach, gdy aplikacja pośrednio lub dyskretnie żąda tokenu dla usługi, aplikacja wymaga zmiany kodu w celu obsługi dostępu warunkowego "wyzwania". Może być tak proste, jak wykonywanie interakcyjnego żądania logowania.

W szczególnych przypadkach następujące scenariusze wymagają, aby kod obsługiwał dostęp warunkowy "wyzwania":

* Aplikacje wykonujące przepływ w imieniu użytkownika
* Aplikacje uzyskujące dostęp do wielu usług/zasobów
* Aplikacje jednostronicowe korzystające z ADAL.js
* Web Apps wywoływania zasobu

Zasady dostępu warunkowego można zastosować do aplikacji, ale można je również zastosować do internetowego interfejsu API, do którego aplikacja uzyskuje dostęp. Aby dowiedzieć się więcej o konfigurowaniu zasad dostępu warunkowego, zobacz [typowe zasady dostępu warunkowego](../conditional-access/concept-conditional-access-policy-common.md).

W zależności od scenariusza Klient korporacyjny może w dowolnym momencie zastosować i usunąć zasady dostępu warunkowego. Aby aplikacja kontynuowała działanie w przypadku zastosowania nowych zasad, należy zaimplementować obsługę "wyzwania". Poniższe przykłady ilustrują obsługę wyzwania.

### <a name="conditional-access-examples"></a>Przykłady dostępu warunkowego

Niektóre scenariusze wymagają zmiany kodu w celu obsługi dostępu warunkowego, a inne pracują zgodnie z oczekiwaniami. Poniżej przedstawiono kilka scenariuszy korzystających z dostępu warunkowego do uwierzytelniania wieloskładnikowego, który zapewnia wgląd w różnice.

* Tworzysz aplikację systemu iOS z jedną dzierżawą i stosujesz zasady dostępu warunkowego. Aplikacja loguje się do użytkownika i nie żąda dostępu do interfejsu API. Po zalogowaniu się użytkownika zasady są automatycznie wywoływane, a użytkownik musi przeprowadzić uwierzytelnianie wieloskładnikowe (MFA). 
* Tworzysz aplikację natywną, która używa usługi warstwy środkowej do uzyskiwania dostępu do podrzędnego interfejsu API. Klient korporacyjny w firmie korzystającej z tej aplikacji stosuje zasady do podrzędnego interfejsu API. Po zalogowaniu się użytkownika końcowego aplikacja natywna zażąda dostępu do warstwy środkowej i wysyła token. Warstwa środkowa wykonuje przepływ w imieniu użytkownika, aby zażądać dostępu do podrzędnego interfejsu API. W tym momencie oświadczenia "wyzwanie" są prezentowane w warstwie środkowej. Warstwa środkowa wysyła wyzwanie z powrotem do aplikacji natywnej, która musi być zgodna z zasadami dostępu warunkowego.

#### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph ma specjalne uwagi dotyczące kompilowania aplikacji w środowiskach dostępu warunkowego. Ogólnie rzecz biorąc, Mechanics dostępu warunkowego zachowuje się tak samo, ale zasady widoczne dla użytkowników będą oparte na danych źródłowych żądanych przez aplikację z grafu. 

W każdym przypadku wszystkie zakresy Microsoft Graph reprezentują niektóre zestawy danych, dla których można zastosować zasady. Ponieważ zasady dostępu warunkowego są przypisane do określonych zestawów danych, usługa Azure AD będzie wymuszać zasady dostępu warunkowego na podstawie danych grafu, a nie samego grafu.

Na przykład jeśli aplikacja żąda następujących zakresów Microsoft Graph,

```
scopes="Bookings.Read.All Mail.Read"
```

Aplikacja może oczekiwać, że użytkownicy będą spełniać wszystkie zasady ustawione w ramach rezerwacji i programu Exchange. Niektóre zakresy mogą być mapowane na wiele zestawów danych, jeśli udzielą dostępu. 

### <a name="complying-with-a-conditional-access-policy"></a>Zgodność z zasadami dostępu warunkowego

W przypadku kilku różnych topologii aplikacji zasady dostępu warunkowego są oceniane podczas ustanawiania sesji. Ponieważ zasady dostępu warunkowego działają na poziomie szczegółowości aplikacji i usług, punkt, w którym jest wywoływany, zależy w dużym stopniu od scenariusza, który próbujesz wykonać.

Gdy aplikacja próbuje uzyskać dostęp do usługi za pomocą zasad dostępu warunkowego, może wystąpić problem z dostępem warunkowym. To żądanie jest zakodowane w `claims` parametrze, który znajduje się w odpowiedzi z usługi Azure AD. Oto przykład tego parametru wyzwania: 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Deweloperzy mogą podjąć to wyzwanie i dołączyć go do nowego żądania do usługi Azure AD. Przekazanie tego stanu powoduje, że użytkownik końcowy będzie monitowany o wykonanie jakiejkolwiek akcji niezbędnej do zapewnienia zgodności z zasadami dostępu warunkowego. W poniższych scenariuszach wyjaśniono konkretne błędy i sposób wyodrębniania parametru.

## <a name="scenarios"></a>Scenariusze

### <a name="prerequisites"></a>Wymagania wstępne

Dostęp warunkowy usługi Azure AD jest funkcją objętą [Azure AD — wersja Premium](../fundamentals/active-directory-whatis.md). Więcej informacji o wymaganiach licencyjnych można znaleźć w raporcie dotyczącym [użycia bez licencji](../reports-monitoring/overview-reports.md). Deweloperzy mogą dołączać do [sieci Microsoft Developer Network](/), która obejmuje bezpłatną subskrypcję pakietu Enterprise Mobility Suite, która obejmuje Azure AD — wersja Premium.

### <a name="considerations-for-specific-scenarios"></a>Zagadnienia dotyczące konkretnych scenariuszy

Poniższe informacje dotyczą tylko tych scenariuszy dostępu warunkowego:

* Aplikacje wykonujące przepływ w imieniu użytkownika
* Aplikacje uzyskujące dostęp do wielu usług/zasobów
* Aplikacje jednostronicowe korzystające z ADAL.js

W poniższych sekcjach omówiono typowe scenariusze, które są bardziej skomplikowane. Podstawową zasadą działania są zasady dostępu warunkowego, które są oceniane w momencie żądania tokenu dla usługi, która ma zastosowane zasady dostępu warunkowego.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Scenariusz: aplikacja wykonująca przepływ w imieniu

W tym scenariuszu przeglądamy przypadek, w którym aplikacja natywna wywołuje usługę sieci Web/interfejs API. Z kolei ta usługa wykonuje przepływ "w imieniu" w celu wywołania usługi podrzędnej. W naszym przypadku zastosowano zasady dostępu warunkowego do usługi podrzędnej (Web API 2) i używasz aplikacji natywnej, a nie aplikacji serwera/demona. 

![Aplikacja wykonująca Diagram przepływu w imieniu](./media/conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

Początkowe żądanie tokenu dla interfejsu Web API 1 nie monituje użytkownika końcowego o uwierzytelnianie wieloskładnikowe, ponieważ interfejs API sieci Web 1 może nie zawsze trafiać do podrzędnego interfejsu API. Gdy interfejs API sieci Web 1 próbuje zażądać tokenu w imieniu użytkownika dla interfejsu Web API 2, żądanie nie powiedzie się, ponieważ użytkownik nie zalogował się przy użyciu uwierzytelniania wieloskładnikowego.

Usługa Azure AD zwraca odpowiedź HTTP z interesującymi się danymi:

> [!NOTE]
> W tym wystąpieniu jest opis błędu usługi uwierzytelniania wieloskładnikowego, ale istnieje szeroki zakres `interaction_required` możliwych odnoszących się do dostępu warunkowego.

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

W interfejsie Web API 1 przechwytuje błąd `error=interaction_required` i wysyłamy `claims` wyzwanie do aplikacji klasycznej. W tym momencie aplikacja klasyczna może utworzyć nowe `acquireToken()` wywołanie i dołączyć `claims` żądanie jako dodatkowy parametr ciągu zapytania. To nowe żądanie wymaga od użytkownika przeprowadzenia uwierzytelniania wieloskładnikowego, a następnie wysłania tego nowego tokenu z powrotem do interfejsu Web API 1 i zakończenia przepływu w imieniu użytkownika.

Aby wypróbować ten scenariusz, zapoznaj się z naszym [przykładem kodu platformy .NET](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Przedstawiono w nim sposób przekazywania wyzwania oświadczeń z interfejsu API sieci Web 1 do aplikacji natywnej i konstruowania nowego żądania w aplikacji klienckiej.

## <a name="scenario-app-accessing-multiple-services"></a>Scenariusz: aplikacja uzyskuje dostęp do wielu usług

W tym scenariuszu w przypadku, gdy aplikacja sieci Web uzyskuje dostęp do dwóch usług, z których jest przypisana zasada dostępu warunkowego. W zależności od logiki aplikacji może istnieć ścieżka, w której aplikacja nie wymaga dostępu do obu usług sieci Web. W tym scenariuszu kolejność, w której zażądano tokenu, odgrywa ważną rolę w środowisku użytkownika końcowego.

Załóżmy, że mamy usługi sieci Web a i B, a usługa sieci Web B ma zastosowane nasze zasady dostępu warunkowego. Gdy początkowe żądanie uwierzytelniania interaktywnego wymaga zgody dla obu usług, zasady dostępu warunkowego nie są wymagane we wszystkich przypadkach. Jeśli aplikacja żąda tokenu usługi sieci Web B, zasady są wywoływane, a kolejne żądania dotyczące usługi sieci Web A także powiedzą się w następujący sposób.

![Aplikacja z dostępem do diagramu przepływu wielu usług](./media/conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

Alternatywnie, jeśli aplikacja początkowo żąda tokenu usługi sieci Web A, użytkownik końcowy nie wywołuje zasad dostępu warunkowego. Dzięki temu deweloper aplikacji może kontrolować środowisko użytkownika końcowego i nie wymuszać wywoływania zasad dostępu warunkowego we wszystkich przypadkach. W przypadku, gdy aplikacja następnie żąda tokenu usługi sieci Web B, jest to trudne. W tym momencie użytkownik końcowy musi przestrzegać zasad dostępu warunkowego. Gdy aplikacja próbuje się `acquireToken` , może wygenerować następujący błąd (zilustrowany na poniższym diagramie):

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![Aplikacja uzyskującego dostęp do wielu usług żądających nowego tokenu](./media/conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Jeśli aplikacja korzysta z biblioteki ADAL, błąd uzyskiwania tokenu jest zawsze powtarzany interaktywnie. W przypadku wystąpienia tego żądania interaktywnego użytkownik końcowy ma możliwość zapewnienia zgodności z dostępem warunkowym. Jest to prawdziwe, chyba że żądanie jest `AcquireTokenSilentAsync` lub `PromptBehavior.Never` w takim przypadku aplikacja musi wykonać interaktywne ```AcquireToken``` żądanie, aby dać użytkownikowi końcowemu możliwość zapewnienia zgodności z zasadami.

## <a name="scenario-single-page-app-spa-using-adaljs"></a>Scenariusz: aplikacja jednostronicowa (SPA) używająca ADAL.js

W tym scenariuszu w przypadku aplikacji jednostronicowej (SPA) korzystamy z usługi ADAL.js do wywoływania internetowego interfejsu API chronionego przez funkcję dostępu warunkowego. Jest to prosta architektura, ale ma pewne wszystkie szczegóły, które należy wziąć pod uwagę podczas tworzenia dookoła dostępu warunkowego.

W ADAL.js istnieje kilka funkcji, które uzyskują tokeny: `login()` , `acquireToken(...)` , `acquireTokenPopup(…)` i `acquireTokenRedirect(…)` .

* `login()` uzyskuje token identyfikatora za pomocą interakcyjnego żądania logowania, ale nie uzyskuje tokenów dostępu dla żadnej usługi (łącznie z chronionym interfejsem API usługi Dostęp warunkowy).
* `acquireToken(…)` można następnie użyć do dyskretnego uzyskania tokenu dostępu, co oznacza, że w żadnym wypadku nie jest wyświetlany interfejs użytkownika.
* `acquireTokenPopup(…)` i `acquireTokenRedirect(…)` są one używane do interaktywnego żądania tokenu dla zasobu, co oznacza, że zawsze wyświetla interfejs użytkownika logowania.

Gdy aplikacja wymaga tokenu dostępu do wywoływania internetowego interfejsu API, próbuje `acquireToken(…)` . Jeśli sesja tokenu wygasła lub musi być zgodna z zasadami dostępu warunkowego, funkcja *acquireToken* kończy się niepowodzeniem, a aplikacja używa `acquireTokenPopup()` lub `acquireTokenRedirect()` .

![Aplikacja jednostronicowa korzystająca z diagramu przepływu ADAL](./media/conditional-access-dev-guide/spa-using-adal-scenario.png)

Skorzystajmy z przykładu w naszym scenariuszu dostępu warunkowego. Użytkownik końcowy został wyładowany w lokacji i nie ma sesji. Wykonujemy `login()` wywołanie, uzyskując token identyfikatora bez uwierzytelniania wieloskładnikowego. Następnie użytkownik trafi przycisk, który wymaga aplikacji do żądania danych z internetowego interfejsu API. Aplikacja próbuje wykonać wywołanie, `acquireToken()` ale kończy się niepowodzeniem, ponieważ użytkownik nie wykonał jeszcze uwierzytelniania wieloskładnikowego i musi być zgodny z zasadami dostępu warunkowego.

Usługa Azure AD odsyła do następującej odpowiedzi HTTP:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

Nasza aplikacja musi przechwycić `error=interaction_required` . Aplikacja może następnie korzystać z `acquireTokenPopup()` lub `acquireTokenRedirect()` w tym samym zasobie. Użytkownik musi przeprowadzić uwierzytelnianie wieloskładnikowe. Po zakończeniu uwierzytelniania wieloskładnikowego przez użytkownika aplikacja zostanie wystawiona jako nowy token dostępu dla żądanego zasobu.

Aby wypróbować ten scenariusz, zapoznaj się [z naszym Przykładem Spa](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca). Ten przykładowy kod korzysta z zasad dostępu warunkowego i internetowego interfejsu API, który został zarejestrowany wcześniej przy użyciu protokołu JS SPA, aby przedstawić ten scenariusz. Pokazuje, jak prawidłowo obsługiwać wyzwanie oświadczeń i uzyskać token dostępu, który może być używany przez internetowy interfejs API. Alternatywnie można wyewidencjonować [ przykład kodu ogólnegoAngular.js](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) , aby uzyskać wskazówki dotyczące KĄTOWego Spa

## <a name="see-also"></a>Zobacz też

* Aby dowiedzieć się więcej o możliwościach, zobacz [dostęp warunkowy w Azure Active Directory](../conditional-access/overview.md).
* Aby uzyskać więcej przykładów kodu usługi Azure AD, zobacz [repozytorium kodu](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory)w witrynie GitHub.
* Aby uzyskać więcej informacji na temat zestawu SDK ADAL i uzyskać dostęp do dokumentacji referencyjnej, zobacz [Przewodnik po bibliotece](active-directory-authentication-libraries.md).
* Aby dowiedzieć się więcej o scenariuszach obejmujących wiele dzierżawców, zobacz [Jak logować użytkowników przy użyciu wzorca wielodostępności](../develop/howto-convert-app-to-be-multi-tenant.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json).