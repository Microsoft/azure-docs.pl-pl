---
title: Konfigurowalne okresy istnienia tokenów
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak ustawiać okresy istnienia tokenów wystawionych przez platformę tożsamości firmy Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 09/29/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: a9bf992a65914afb8fa800041b57ad9f44ba4fa0
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91595619"
---
# <a name="configurable-token-lifetimes-in-microsoft-identity-platform-preview"></a>Konfigurowalne okresy istnienia tokenów na platformie tożsamości firmy Microsoft (wersja zapoznawcza)

Możesz określić okres istnienia tokenu wystawionego przez platformę tożsamości firmy Microsoft. Okresy istnienia tokenów można ustawić dla wszystkich aplikacji w organizacji, dla aplikacji wielodostępnych (dla wielu organizacji) lub dla określonej jednostki usługi w organizacji. Obecnie nie obsługujemy konfigurowania okresów istnienia tokenu dla [podmiotów usługi zarządzania tożsamościami zarządzanymi](../managed-identities-azure-resources/overview.md).

> [!IMPORTANT]
> Po przesłuchaniu od klientów w wersji zapoznawczej wdrożono [funkcje zarządzania sesjami uwierzytelniania](../conditional-access/howto-conditional-access-session-lifetime.md) w usłudze Azure AD dostęp warunkowy. Ta nowa funkcja służy do konfigurowania okresów istnienia tokenu odświeżania przez ustawienie częstotliwości logowania. Po 30 maja 2020 żadna Nowa dzierżawa nie będzie mogła korzystać z konfigurowalnych zasad okresu istnienia tokenu do konfigurowania tokenów sesji i odświeżania. Wycofanie zostanie wykonane w ciągu kilku miesięcy od tego momentu, co oznacza, że zaprzestanie przestrzegania istniejącej sesji i zasad odświeżania tokenów. Nadal można skonfigurować okresy istnienia tokenu dostępu po zakończeniu działania.

W usłudze Azure AD obiekt zasad reprezentuje zestaw reguł, które są wymuszane dla poszczególnych aplikacji lub we wszystkich aplikacjach w organizacji. Każdy typ zasad ma unikatową strukturę z zestawem właściwości, które są stosowane do obiektów, do których są przypisane.

Zasady można wyznaczyć jako zasady domyślne dla swojej organizacji. Zasady są stosowane do wszystkich aplikacji w organizacji, o ile nie zostały zastąpione przez zasady o wyższym priorytecie. Można również przypisać zasady do określonych aplikacji. Kolejność priorytetów różni się w zależności od typu zasad.

Przykładowo Przeczytaj [Przykłady sposobu konfigurowania okresów istnienia tokenu](configure-token-lifetimes.md).

> [!NOTE]
> Zasady okresowego istnienia tokenu dotyczą tylko klientów mobilnych i komputerów stacjonarnych, które uzyskują dostęp do usług SharePoint Online i OneDrive dla firm i nie mają zastosowania do sesji przeglądarki sieci Web.
> Aby zarządzać okresem istnienia sesji przeglądarki sieci Web dla usług SharePoint Online i OneDrive dla firm, użyj funkcji [okres istnienia sesji dostępu warunkowego](../conditional-access/howto-conditional-access-session-lifetime.md) . Zapoznaj się z [blogiem usługi SharePoint Online](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) , aby dowiedzieć się więcej o konfigurowaniu limitów czasu bezczynności sesji.

## <a name="token-types"></a>Typy tokenów

Można ustawić zasady okresu istnienia tokenu dla tokenów odświeżania, tokenów dostępu, tokenów SAML, tokenów sesji i tokenów identyfikatorów.

### <a name="access-tokens"></a>Tokeny dostępu

Klienci używają tokenów dostępu w celu uzyskiwania dostępu do chronionego zasobu. Tokenu dostępu można używać tylko dla konkretnej kombinacji użytkownika, klienta i zasobu. Tokeny dostępu nie mogą zostać odwołane i są ważne do czasu ich wygaśnięcia. Złośliwy aktor, który uzyskał token dostępu, może korzystać z niego na potrzeby zakresu jego okresu istnienia. Dostosowanie okresu istnienia tokena dostępu jest kompromisem między zwiększeniem wydajności systemu i zwiększeniem czasu, przez jaki klient zachowuje dostęp po wyłączeniu konta użytkownika. Zwiększona wydajność systemu jest osiągana przez zredukowanie liczby prób uzyskania przez klienta nowego tokenu dostępu.  Wartość domyślna to 1 godzina — po 1 godzinie klient musi używać tokenu odświeżania do (zazwyczaj dyskretnie) uzyskać nowy token odświeżania i token dostępu. 

### <a name="saml-tokens"></a>Tokeny języka SAML

Tokeny języka SAML są używane przez wiele aplikacji SAAS opartych na sieci Web i są uzyskiwane Azure Active Directory przy użyciu punktu końcowego protokołu SAML2. Są one również używane przez aplikacje korzystające z protokołu WS-Federation. Domyślny okres istnienia tokenu to 1 godzina. W perspektywie aplikacji okres ważności tokenu jest określany przez wartość NotOnOrAfter `<conditions …>` elementu w tokenie. Po zakończeniu okresu ważności tokenu klient musi zainicjować nowe żądanie uwierzytelniania, które będzie często spełnione bez interakcyjnego logowania w wyniku tokenu sesji logowania jednokrotnego (SSO).

Wartość NotOnOrAfter można zmienić przy użyciu `AccessTokenLifetime` parametru w `TokenLifetimePolicy` . Zostanie ona ustawiona na okres istnienia skonfigurowany w ramach zasad, jeśli istnieje, oraz współczynnik pochylenia zegara wynoszący pięć minut.

`<SubjectConfirmationData>`Konfiguracja okresu istnienia tokenu nie ma wpływ na potwierdzenie podmiotu NotOnOrAfter określone w elemencie. 

### <a name="refresh-tokens"></a>Odśwież tokeny

Gdy klient uzyskuje token dostępu w celu uzyskania dostępu do chronionego zasobu, klient otrzymuje również token odświeżenia. Token odświeżania służy do uzyskiwania nowych par tokenów dostępu/odświeżania w przypadku wygaśnięcia bieżącego tokenu dostępu. Token odświeżania jest powiązany z kombinacją użytkownika i klienta. Token odświeżania można [odwołać w dowolnym momencie](access-tokens.md#token-revocation), a ważność tokenu jest sprawdzana za każdym razem, gdy token jest używany.  Tokeny odświeżania nie są odwoływane, gdy są używane do pobierania nowych tokenów dostępu — najlepszym rozwiązaniem jest jednak, aby bezpiecznie usunąć stary token podczas uzyskiwania nowego. 

Ważne jest, aby wprowadzić rozróżnienie między poufnymi klientami i klientami publicznymi, co ma wpływ na to, jak długo można używać tokenów odświeżania. Aby uzyskać więcej informacji na temat różnych typów klientów, zobacz [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Okresy istnienia tokenów z poufnymi tokenami odświeżania klienta
Poufne klienci są aplikacjami, które mogą bezpiecznie przechowywać hasło klienta (poufne). Mogą oni udowodnić, że żądania pochodzą z zabezpieczonej aplikacji klienckiej, a nie od złośliwego aktora. Na przykład aplikacja sieci Web to poufny klient, ponieważ może przechowywać klucz tajny klienta na serwerze sieci Web. Nie jest on narażony. Ponieważ te przepływy są bezpieczniejsze, domyślne okresy istnienia tokenów odświeżania wystawionych dla tych przepływów `until-revoked` nie mogą być zmieniane przy użyciu zasad i nie zostaną odwołane w przypadku resetowania hasła dobrowolnego.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Okresy istnienia tokenów z publicznymi tokenami odświeżania klienta

Klienci publiczni nie mogą bezpiecznie przechowywać hasła klienta (poufne). Na przykład aplikacja dla systemu iOS/Android nie może zasłaniać wpisu tajnego z właściciela zasobu, więc jest traktowana jako klient publiczny. Można ustawić zasady dla zasobów, aby zapobiec tokenom odświeżania od klientów publicznych starszych niż określony okres od uzyskania nowej pary tokenów dostępu/odświeżenia. W tym celu należy użyć [Właściwości maks. czas nieaktywności tokenu odświeżania](#refresh-token-max-inactive-time) ( `MaxInactiveTime` ). Można również użyć zasad, aby ustawić okres, po którym tokeny odświeżania nie są już akceptowane. W tym celu należy użyć właściwości maksymalnego wieku [tokenu odświeżania](#single-factor-session-token-max-age) lub [tokenu sesji wieloskładnikowej](#multi-factor-refresh-token-max-age) . Można dostosować okres istnienia tokenu odświeżania, aby określić, kiedy i jak często użytkownik musi ponownie wprowadzić poświadczenia, zamiast być w sposób dyskretny uwierzytelniany w przypadku korzystania z publicznej aplikacji klienckiej.

> [!NOTE]
> Właściwość Max Age to długość czasu, przez który można użyć pojedynczego tokenu. 

### <a name="id-tokens"></a>Tokeny identyfikatorów
Tokeny identyfikatorów są przesyłane do witryn sieci Web i natywnych klientów. Tokeny identyfikatorów zawierają informacje o profilu użytkownika. Token identyfikatora jest powiązany z określoną kombinacją użytkownika i klienta. Tokeny identyfikatorów są uznawane za ważne do momentu ich wygaśnięcia. Zwykle aplikacja sieci Web dopasowuje okres istnienia sesji użytkownika w aplikacji do okresu istnienia tokenu identyfikatora wydanego dla użytkownika. Możesz dostosować okres istnienia tokenu identyfikatora, aby określić, jak często aplikacja sieci Web wygaśnie w sesji aplikacji i jak często wymaga ponownego uwierzytelnienia użytkownika przy użyciu platformy tożsamości firmy Microsoft (dyskretnie lub interaktywnie).

### <a name="single-sign-on-session-tokens"></a>Tokeny sesji logowania jednokrotnego
Gdy użytkownik uwierzytelnia się za pomocą platformy tożsamości firmy Microsoft, sesja logowania jednokrotnego (SSO) jest ustanawiana z użyciem przeglądarki użytkownika i platformy tożsamości firmy Microsoft. Token logowania jednokrotnego w formie pliku cookie reprezentuje tę sesję. Token sesji logowania jednokrotnego nie jest powiązany z określonym zasobem/aplikacją kliencką. Tokeny sesji SSO można odwołać, a ich ważność jest sprawdzana za każdym razem, gdy są używane.

Platforma tożsamości firmy Microsoft używa dwóch rodzajów tokenów sesji SSO: trwałych i nietrwałych. Tokeny sesji trwałych są przechowywane jako trwałe pliki cookie w przeglądarce. Tokeny sesji nietrwałych są przechowywane jako pliki cookie sesji. (Pliki cookie sesji są niszczone po zamknięciu przeglądarki). Zwykle jest przechowywany token nietrwałych sesji. Jednak gdy użytkownik wybierze pole wyboru nie wylogowuj **mnie** podczas uwierzytelniania, jest przechowywany token sesji trwałej.

Tokeny sesji nietrwałych mają okres istnienia 24 godzin. Stałe tokeny mają okres istnienia wynoszący 90 dni. W czasie, gdy token sesji logowania jednokrotnego jest używany w okresie ważności, okres ważności jest rozszerzany o kolejne 24 godziny lub 90 dni, w zależności od typu tokenu. Jeśli token sesji SSO nie jest używany w jego okresie ważności, jest uznawany za wygasły i nie jest już akceptowany.

Można użyć zasad, aby ustawić czas po wydaniu pierwszego tokenu sesji, poza którym token sesji nie jest już akceptowany. (W tym celu należy użyć właściwości maksymalny wiek tokenu sesji). Możesz dostosować okres istnienia tokenu sesji, aby określić, kiedy i jak często użytkownik musi ponownie wprowadzić poświadczenia, zamiast być uwierzytelniany w trybie dyskretnym w przypadku korzystania z aplikacji sieci Web.

### <a name="token-lifetime-policy-properties"></a>Właściwości zasad okresu istnienia tokenu
Zasada okresu istnienia tokenu jest typem obiektu zasad, który zawiera reguły okresu istnienia tokenu. Użyj właściwości zasad do kontrolowania określonych okresów istnienia tokenu. Jeśli nie ustawiono żadnych zasad, system wymusza domyślną wartość okresu istnienia.

### <a name="configurable-token-lifetime-properties"></a>Konfigurowalne właściwości okresu istnienia tokenu
| Właściwość | Ciąg właściwości zasad | Mową | Domyślny | Minimum | Maksimum |
| --- | --- | --- | --- | --- | --- |
| Okres istnienia tokenu dostępu |AccessTokenLifetime<sup>2</sup> |Tokeny dostępu, tokeny identyfikatorów, tokeny SAML2 |1 godzina |10 minut |1 dzień |
| Maksymalny czas nieaktywności tokenu odświeżania |MaxInactiveTime |Odśwież tokeny |90 dni |10 minut |90 dni |
| Maksymalny wiek tokenu odświeżania pojedynczego czynnika |MaxAgeSingleFactor |Odśwież tokeny (dla wszystkich użytkowników) |Do odwołania |10 minut |Do odwołania<sup>1</sup> |
| Maksymalny wiek tokenu wieloskładnikowego odświeżania |MaxAgeMultiFactor |Odśwież tokeny (dla wszystkich użytkowników) | 180 dni |10 minut |Do odwołania<sup>1</sup> |
| Maksymalny wiek tokenu sesji pojedynczego czynnika |MaxAgeSessionSingleFactor |Tokeny sesji (trwałe i nietrwałe) |Do odwołania |10 minut |Do odwołania<sup>1</sup> |
| Maksymalny wiek tokenu sesji wieloskładnikowe |MaxAgeSessionMultiFactor |Tokeny sesji (trwałe i nietrwałe) | 180 dni |10 minut |Do odwołania<sup>1</sup> |

* <sup>1</sup>365 dni to maksymalna jawna długość, którą można ustawić dla tych atrybutów.
* <sup>2</sup> Aby zapewnić działanie klienta sieci Web Microsoft Teams, zaleca się pozostawienie AccessTokenLifetime do ponad 15 minut dla Microsoft Teams.

### <a name="exceptions"></a>Wyjątki
| Właściwość | Mową | Domyślny |
| --- | --- | --- |
| Maksymalny wiek tokenu odświeżania (wystawiony dla użytkowników federacyjnych, którzy mają niewystarczające informacje o odwołaniu<sup>1</sup>) |Odśwież tokeny (wystawione dla użytkowników federacyjnych, którzy mają niewystarczające informacje o odwołaniu<sup>1</sup>) |12 godzin |
| Maksymalny czas nieaktywności tokenu odświeżania (wystawiony dla klientów poufnych) |Odśwież tokeny (wystawione dla klientów poufnych) |90 dni |
| Maksymalny wiek tokenu odświeżania (wystawiony dla klientów poufnych) |Odśwież tokeny (wystawione dla klientów poufnych) |Do odwołania |

* <sup>1</sup> użytkownicy federacyjny, którzy mają niewystarczające informacje o odwołaniu, obejmują wszystkich użytkowników, którzy nie mają atrybutu "LastPasswordChangeTimestamp". Ci użytkownicy otrzymują ten krótki maksymalny wiek, ponieważ Azure Active Directory nie może zweryfikować, kiedy odwołać tokeny powiązane ze starym poświadczeniem (na przykład hasło, które zostało zmienione), i należy ponownie zaewidencjonować, aby upewnić się, że użytkownik i skojarzone tokeny są nadal w dobrym stanie. Aby ulepszyć to środowisko, Administratorzy dzierżaw muszą upewnić się, że synchronizują atrybut "LastPasswordChangeTimestamp" (można go ustawić dla obiektu użytkownika przy użyciu programu PowerShell lub za pośrednictwem AADSync).

### <a name="policy-evaluation-and-prioritization"></a>Ocena zasad i priorytetyzacja
Można utworzyć i przypisać zasady czasu istnienia tokenu do określonej aplikacji, organizacji, a także do podmiotów usługi. Do określonej aplikacji mogą być stosowane wiele zasad. Zasady okresu istnienia tokenu, które obowiązują, są zgodne z następującymi regułami:

* Jeśli zasady są jawnie przypisane do nazwy głównej usługi, jest wymuszane.
* Jeśli żadna zasada nie zostanie jawnie przypisana do jednostki usługi, wymuszana jest zasada jawnie przypisana do organizacji nadrzędnej jednostki usługi.
* Jeśli żadna zasada nie zostanie jawnie przypisana do jednostki usługi lub do organizacji, zasady przypisane do aplikacji są wymuszane.
* Jeśli żadna zasada nie została przypisana do jednostki usługi, organizacji lub obiektu aplikacji, wartości domyślne są wymuszane. (Zobacz tabelę w obszarze [Właściwości konfigurowalnego okresu istnienia tokenu](#configurable-token-lifetime-properties)).

Aby uzyskać więcej informacji na temat relacji między obiektami aplikacji i obiektami głównej usługi, zobacz temat [obiekty główne aplikacji i usługi w Azure Active Directory](app-objects-and-service-principals.md).

Ważność tokenu jest oceniana w czasie używania tokenu. Zasady z najwyższym priorytetem w aplikacji, do której uzyskuje się dostęp, zaczynają obowiązywać.

Wszystkie używane tu przedziały czasu są sformatowane zgodnie z obiektem [TimeSpan](/dotnet/api/system.timespan) języka C#-D. hh: mm: SS.  Tak więc 80 dni i 30 minut `80.00:30:00` .  Interlinię D można porzucić, jeśli zero, więc 90 minut `00:90:00` .  

> [!NOTE]
> Oto przykładowy scenariusz.
>
> Użytkownik chce uzyskać dostęp do dwóch aplikacji sieci Web: aplikacji sieci Web A i aplikacji sieci Web B.
> 
> Elementy
> * Obie aplikacje sieci Web znajdują się w tej samej organizacji nadrzędnej.
> * Zasady okresu istnienia tokenu 1 z tokenem sesji maksymalny wiek równy osiem godzin jest ustawiany jako domyślna organizacja nadrzędna.
> * Aplikacja sieci Web A to regularna aplikacja sieci Web, która nie jest połączona z żadną zasadą.
> * Aplikacja sieci Web B jest używana w przypadku procesów wysoce poufnych. Jej jednostka usługi jest połączona z zasadą okresu istnienia tokenu 2, która ma maksymalny wiek tokenu sesji wynoszący 30 minut.
>
> O godzinie 12:00 PM użytkownik uruchamia nową sesję przeglądarki i próbuje uzyskać dostęp do aplikacji sieci Web A. Użytkownik zostanie przekierowany do platformy tożsamości firmy Microsoft i zostanie poproszony o zalogowanie się. Spowoduje to utworzenie pliku cookie z tokenem sesji w przeglądarce. Użytkownik zostanie przekierowany z powrotem do aplikacji sieci Web A przy użyciu tokenu identyfikatora, który umożliwia użytkownikowi dostęp do aplikacji.
>
> W 12:15 PM użytkownik próbuje uzyskać dostęp do aplikacji sieci Web B. Przeglądarka przekierowuje do platformy tożsamości firmy Microsoft, która wykrywa plik cookie sesji. Nazwa główna usługi aplikacji sieci Web B jest połączona z zasadą okresu istnienia tokenu 2, ale jest również częścią organizacji nadrzędnej z domyślnymi zasadami ważności tokenu 1. Zasady ważności tokenu 2 obowiązują, ponieważ zasady połączone z jednostkami usługi mają wyższy priorytet niż domyślne zasady organizacji. Token sesji został pierwotnie wydany w ciągu ostatnich 30 minut, więc jest uznawany za ważny. Użytkownik zostanie przekierowany z powrotem do aplikacji sieci Web B z tokenem identyfikatora, który przyznaje im dostęp.
>
> O godzinie 1:00 PM użytkownik próbuje uzyskać dostęp do aplikacji sieci Web A. Użytkownik zostanie przekierowany do platformy tożsamości firmy Microsoft. Aplikacja sieci Web A nie jest połączona z żadną zasadą, ale ponieważ należy do organizacji z domyślnymi zasadami istnienia tokenu 1, ta zasada zacznie obowiązywać. Wykryto plik cookie sesji, który został pierwotnie wystawiony w ciągu ostatnich ośmiu godzin. Użytkownik jest dyskretnie przekierowywany do aplikacji sieci Web A z nowym tokenem ID. Użytkownik nie musi uwierzytelniać się.
>
> Natychmiast użytkownik próbuje uzyskać dostęp do aplikacji sieci Web B. Użytkownik zostanie przekierowany do platformy tożsamości firmy Microsoft. Zgodnie z wcześniejszymi zasadami istnienia tokenu obowiązuje zasada 2. Ponieważ token został wystawiony ponad 30 minut temu, użytkownik jest monitowany o ponowne wprowadzenie poświadczeń logowania. Zostanie wystawiony token nowej sesji i token ID. Użytkownik może następnie uzyskać dostęp do aplikacji sieci Web B.
>
>

## <a name="configurable-policy-property-details"></a>Szczegóły właściwości zasad konfigurowalnych
### <a name="access-token-lifetime"></a>Okres istnienia tokenu dostępu
**Ciąg:** AccessTokenLifetime

**Ma wpływ na:** Tokeny dostępu, tokeny identyfikatorów, tokeny SAML

**Podsumowanie:** Ta zasada kontroluje, jak długo tokeny dostępu i identyfikatorów dla tego zasobu są uznawane za ważne. Zmniejszenie właściwości okresu istnienia tokenu dostępu zmniejsza ryzyko związane z tokenem dostępu lub tokenem ID używanym przez złośliwy aktor przez dłuższy czas. (Tokeny te nie mogą zostać odwołane). W wyniku tego jest niekorzystnie wpływać na wydajność, ponieważ tokeny muszą być częściej zastępowane.

Aby zapoznać się z przykładem, zobacz [Tworzenie zasad logowania do sieci Web](configure-token-lifetimes.md#create-a-policy-for-web-sign-in).

### <a name="refresh-token-max-inactive-time"></a>Maksymalny czas nieaktywności tokenu odświeżania
**Ciąg:** MaxInactiveTime

**Ma wpływ na:** Odśwież tokeny

**Podsumowanie:** Ta zasada kontroluje, jak stara token odświeżania, zanim klient nie będzie mógł go już używać do pobierania nowej pary tokenów dostępu/odświeżania podczas próby uzyskania dostępu do tego zasobu. Ponieważ nowy token odświeżania jest zwykle zwracany, gdy używany jest token odświeżania, te zasady uniemożliwiają dostęp, jeśli klient próbuje uzyskać dostęp do dowolnego zasobu przy użyciu bieżącego tokenu odświeżania w określonym przedziale czasu.

Te zasady wymuszają, że użytkownicy, którzy nie zostali aktywni na kliencie w celu ponownego uwierzytelnienia w celu pobrania nowego tokenu odświeżania.

Właściwość maksymalny czas nieaktywności tokenu odświeżania musi być ustawiona na wartość niższą niż maksymalny wiek tokenu usługi Single-Factor i Maksymalna liczba właściwości wieku tokenu usługi wieloskładnikowej.

Aby zapoznać się z przykładem, zobacz [Tworzenie zasad dla aplikacji natywnej, która wywołuje interfejs API sieci Web](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api).

### <a name="single-factor-refresh-token-max-age"></a>Maksymalny wiek tokenu odświeżania pojedynczego czynnika
**Ciąg:** MaxAgeSingleFactor

**Ma wpływ na:** Odśwież tokeny

**Podsumowanie:** Ta zasada kontroluje, jak długo użytkownik może użyć tokenu odświeżania, aby uzyskać nową parę tokenów dostępu/odświeżenia po pomyślnym uwierzytelnieniu przy użyciu tylko jednego czynnika. Po uwierzytelnieniu i otrzymaniu nowego tokenu odświeżania użytkownik może użyć przepływu tokenu odświeżania przez określony czas. (Jest to prawdziwe, o ile bieżący token odświeżania nie jest odwołany i nie jest jeszcze nieużywany przez czas dłuższy niż nieaktywny). W tym momencie użytkownik jest zmuszony do ponownego uwierzytelnienia w celu otrzymania nowego tokenu odświeżania.

Skrócenie maksymalnego wieku zmusza użytkowników do częstego uwierzytelniania. Ponieważ uwierzytelnianie wieloskładnikowe jest uznawane za mniej bezpieczne niż w przypadku uwierzytelniania wieloskładnikowego, zalecamy ustawienie tej właściwości na wartość, która jest równa lub mniejsza niż Właściwość maksymalnego wieku tokena odświeżania.

Aby zapoznać się z przykładem, zobacz [Tworzenie zasad dla aplikacji natywnej, która wywołuje interfejs API sieci Web](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api).

### <a name="multi-factor-refresh-token-max-age"></a>Maksymalny wiek tokenu wieloskładnikowego odświeżania
**Ciąg:** MaxAgeMultiFactor

**Ma wpływ na:** Odśwież tokeny

**Podsumowanie:** Ta zasada kontroluje, jak długo użytkownik może użyć tokenu odświeżania, aby uzyskać nową parę tokenów dostępu/odświeżenia po pomyślnym uwierzytelnieniu przy użyciu wielu czynników. Po uwierzytelnieniu i otrzymaniu nowego tokenu odświeżania użytkownik może użyć przepływu tokenu odświeżania przez określony czas. (Wartość ta jest równa, o ile bieżący token odświeżania nie jest odwołany i nie jest używany dłużej niż czas nieaktywności). W tym momencie użytkownicy są zmuszeni do ponownego uwierzytelnienia w celu otrzymania nowego tokenu odświeżania.

Skrócenie maksymalnego wieku zmusza użytkowników do częstego uwierzytelniania. Ponieważ uwierzytelnianie wieloskładnikowe jest uznawane za mniej bezpieczne niż w przypadku uwierzytelniania wieloskładnikowego, zalecamy ustawienie tej właściwości na wartość, która jest równa lub większa niż Właściwość maksymalnego wieku tokena odświeżania.

Aby zapoznać się z przykładem, zobacz [Tworzenie zasad dla aplikacji natywnej, która wywołuje interfejs API sieci Web](configure-token-lifetimes.md#create-a-policy-for-a-native-app-that-calls-a-web-api).

### <a name="single-factor-session-token-max-age"></a>Maksymalny wiek tokenu sesji pojedynczego czynnika
**Ciąg:** MaxAgeSessionSingleFactor

**Ma wpływ na:** Tokeny sesji (trwałe i nietrwałe)

**Podsumowanie:** Ta zasada kontroluje, jak długo użytkownik może użyć tokenu sesji w celu uzyskania nowego identyfikatora i tokenu sesji po pomyślnym uwierzytelnieniu przy użyciu tylko jednego czynnika. Po uwierzytelnieniu i odebraniu nowego tokenu sesji użytkownik może użyć przepływu tokenu sesji przez określony czas. (Wartość ta jest równa, o ile token bieżącej sesji nie został odwołany i nie wygasł.) Po upływie określonego czasu użytkownik jest zmuszony do ponownego uwierzytelnienia w celu otrzymania nowego tokenu sesji.

Skrócenie maksymalnego wieku zmusza użytkowników do częstego uwierzytelniania. Ponieważ uwierzytelnianie wieloskładnikowe jest uznawane za mniej bezpieczne niż w przypadku uwierzytelniania wieloskładnikowego, zalecamy ustawienie tej właściwości na wartość, która jest równa lub mniejsza niż maksymalny wiek tokenu sesji wieloskładnikowej.

Aby zapoznać się z przykładem, zobacz [Tworzenie zasad logowania do sieci Web](configure-token-lifetimes.md#create-a-policy-for-web-sign-in).

### <a name="multi-factor-session-token-max-age"></a>Maksymalny wiek tokenu sesji wieloskładnikowe
**Ciąg:** MaxAgeSessionMultiFactor

**Ma wpływ na:** Tokeny sesji (trwałe i nietrwałe)

**Podsumowanie:** Ta zasada kontroluje, jak długo użytkownik może użyć tokenu sesji w celu uzyskania nowego identyfikatora i tokenu sesji po ostatnim pomyślnym uwierzytelnieniu przy użyciu wielu czynników. Po uwierzytelnieniu i odebraniu nowego tokenu sesji użytkownik może użyć przepływu tokenu sesji przez określony czas. (Wartość ta jest równa, o ile token bieżącej sesji nie został odwołany i nie wygasł.) Po upływie określonego czasu użytkownik jest zmuszony do ponownego uwierzytelnienia w celu otrzymania nowego tokenu sesji.

Skrócenie maksymalnego wieku zmusza użytkowników do częstego uwierzytelniania. Ponieważ uwierzytelnianie wieloskładnikowe jest uznawane za mniej bezpieczne niż w przypadku usługi uwierzytelniania wieloskładnikowego, zalecamy ustawienie tej właściwości na wartość, która jest równa lub większa niż Właściwość maksymalnego wieku tokenu sesji.

## <a name="cmdlet-reference"></a>Dokumentacja poleceń cmdlet

Są to polecenia cmdlet w [module Azure Active Directory PowerShell dla programu Graph Preview](/powershell/module/azuread/?view=azureadps-2.0-preview#service-principals&preserve-view=true&preserve-view=true).

### <a name="manage-policies"></a>Zarządzanie zasadami

Aby zarządzać zasadami, można użyć następujących poleceń cmdlet.

| Polecenie cmdlet | Opis | 
| --- | --- |
| [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Tworzy nowe zasady. |
| [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Pobiera wszystkie zasady usługi Azure AD lub określone zasady. |
| [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) | Pobiera wszystkie aplikacje i jednostki usługi, które są połączone z zasadami. |
| [Set-AzureADPolicy](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Aktualizuje istniejące zasady. |
| [Remove-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Usuwa określone zasady. |

### <a name="application-policies"></a>Zasady aplikacji
W przypadku zasad aplikacji można użyć następujących poleceń cmdlet.</br></br>

| Polecenie cmdlet | Opis | 
| --- | --- |
| [Add-AzureADApplicationPolicy](/powershell/module/azuread/add-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Łączy określone zasady z aplikacją. |
| [Get-AzureADApplicationPolicy](/powershell/module/azuread/get-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Pobiera zasady przypisane do aplikacji. |
| [Remove-AzureADApplicationPolicy](/powershell/module/azuread/remove-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Usuwa zasady z aplikacji. |

### <a name="service-principal-policies"></a>Zasady dotyczące nazwy głównej usługi
W przypadku zasad głównych usługi można użyć następujących poleceń cmdlet.

| Polecenie cmdlet | Opis | 
| --- | --- |
| [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Łączy określone zasady z jednostką usługi. |
| [Get-AzureADServicePrincipalPolicy](/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Pobiera wszystkie zasady połączone z określoną jednostką usługi.|
| [Remove-AzureADServicePrincipalPolicy](/powershell/module/azuread/remove-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Usuwa zasady z określonej nazwy głównej usługi.|

## <a name="license-requirements"></a>Wymagania licencyjne

Korzystanie z tej funkcji wymaga licencji na Azure AD — wersja Premium P1. Aby znaleźć odpowiednią licencję dla wymagań, zobacz [porównanie ogólnie dostępnych funkcji wersji bezpłatna i Premium](https://azure.microsoft.com/pricing/details/active-directory/).

Klienci z [licencjami Microsoft 365 Business](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) również mają dostęp do funkcji dostępu warunkowego.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, Przeczytaj [Przykłady sposobu konfigurowania okresów istnienia tokenu](configure-token-lifetimes.md).