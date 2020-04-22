---
title: Konfigurowalne okresy istnienia tokenu usługi Azure AD
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak ustawić okres istnienia tokenów wystawionych przez usługę Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/17/2020
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: f4138c4ae24ae599d4058c9fd06c33b69657fe38
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680069"
---
# <a name="configurable-token-lifetimes-in-azure-active-directory-preview"></a>Okres istnienia tokenu konfigurowalny w usłudze Azure Active Directory (wersja zapoznawcza)

Możliwe jest określenie okresu istnienia tokenu wystawionego przez usługę Azure Active Directory (Azure AD). Okresy istnienia tokenów można ustawić dla wszystkich aplikacji w organizacji, dla aplikacji wielodostępnych (dla wielu organizacji) lub dla określonej jednostki usługi w organizacji.

> [!IMPORTANT]
> Po wysłuchaniu klientów podczas wersji zapoznawczej zaimplementowaliśmy [funkcje zarządzania sesjami uwierzytelniania](https://go.microsoft.com/fwlink/?linkid=2083106) w usłudze Azure AD Dostęp warunkowy. Za pomocą tej nowej funkcji można skonfigurować okres istnienia tokenu odświeżania, ustawiając częstotliwość logowania. Po 1 maja 2020 r. nie będzie można użyć zasad Okres istnienia tokenu configurowalny do konfigurowania tokenów sesji i odświeżania. Nadal można skonfigurować okresy istnienia tokenu dostępu po umorzyniu.

W usłudze Azure AD obiekt zasad reprezentuje zestaw reguł, które są wymuszane w poszczególnych aplikacjach lub we wszystkich aplikacjach w organizacji. Każdy typ zasad ma unikatową strukturę z zestawem właściwości, które są stosowane do obiektów, do których są przypisane.

Zasady można wyznaczyć jako domyślną zasadę w organizacji. Zasady są stosowane do dowolnej aplikacji w organizacji, o ile nie jest zastępowana przez zasady o wyższym priorytecie. Można również przypisać zasady do określonych aplikacji. Kolejność priorytetów zależy od typu zasad.

> [!NOTE]
> Zasady okresu istnienia tokenu konfigurowalne dotyczą tylko klientów mobilnych i komputerów stacjonarnych, którzy uzyskują dostęp do zasobów usługi SharePoint Online i OneDrive dla Firm i nie mają zastosowania do sesji przeglądarki sieci Web.
> Aby zarządzać okresem istnienia sesji przeglądarki sieci Web dla usługi SharePoint Online i OneDrive dla Firm, użyj funkcji [okresu istnienia sesji dostępu warunkowego.](../conditional-access/howto-conditional-access-session-lifetime.md) Więcej informacji na temat konfigurowania limitów czasu bezczynnej sesji można znaleźć w [blogu usługi SharePoint Online.](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208)

## <a name="token-types"></a>Typy tokenów

Można ustawić zasady okresu istnienia tokenu dla tokenów odświeżania, tokenów dostępu, tokenów SAML, tokenów sesji i tokenów identyfikatorów.

### <a name="access-tokens"></a>Tokeny dostępu

Klienci używają tokenów dostępu, aby uzyskać dostęp do chronionego zasobu. Token dostępu może służyć tylko dla określonej kombinacji użytkownika, klienta i zasobu. Tokeny dostępu nie mogą zostać odwołane i są ważne do czasu ich wygaśnięcia. Złośliwy aktor, który uzyskał token dostępu, może go używać w zakresie jego istnienia. Dostosowanie okresu istnienia tokenu dostępu jest kompromisem między poprawą wydajności systemu a zwiększeniem czasu, przez który klient zachowuje dostęp po wyłączeniu konta użytkownika. Zwiększoną wydajność systemu uzyskuje się poprzez zmniejszenie liczby razy klient musi uzyskać nowy token dostępu.  Wartość domyślna to 1 godzina — po godzinie 1 klient musi użyć tokenu odświeżania, aby (zwykle po cichu) uzyskać nowy token odświeżania i token dostępu. 

### <a name="saml-tokens"></a>Tokeny języka SAML

Tokeny SAML są używane przez wiele aplikacji SAAS opartych na sieci Web i są uzyskiwane przy użyciu punktu końcowego protokołu SAML2 usługi Azure Active Directory. Są one również używane przez aplikacje przy użyciu WS-Federation. Domyślny okres istnienia tokenu wynosi 1 godzinę. Z punktu widzenia aplikacji okres ważności tokenu jest określony przez NotOnOrAfter `<conditions …>` wartość elementu w tokenie. Po zakończeniu okresu ważności tokenu klient musi zainicjować nowe żądanie uwierzytelnienia, które często będzie spełnione bez logowania interakcyjnego w wyniku tokenu sesji logowania jednokrotnego.After the validity period of the token has ended, the client must initiate a new authentication request, which will often be satisfied without interactive sign in as a result of the Single Sign On (SSO) Session token.

Wartość NotOnOrAfter można zmienić `AccessTokenLifetime` za pomocą `TokenLifetimePolicy`parametru w . Zostanie on ustawiony na okres istnienia skonfigurowany w zasadach, jeśli istnieje, plus współczynnik pochylenia zegara pięciu minut.

Należy zauważyć, że potwierdzenie tematu NotOnOrAfter określony w elemencie `<SubjectConfirmationData>` nie ma wpływu na konfigurację okresu istnienia tokenu. 

### <a name="refresh-tokens"></a>Odśwież tokeny

Gdy klient uzyskuje token dostępu, aby uzyskać dostęp do chronionego zasobu, klient otrzymuje również token odświeżania. Token odświeżania jest używany do uzyskiwania nowych par tokenów dostępu/odświeżania po wygaśnięciu bieżącego tokenu dostępu. Token odświeżania jest powiązany z kombinacją użytkownika i klienta. Token odświeżania można [odwołać w dowolnym momencie,](access-tokens.md#token-revocation)a ważność tokenu jest sprawdzana za każdym razem, gdy token jest używany.  Tokeny odświeżania nie są odwoływane, gdy są używane do pobierania nowych tokenów dostępu — najlepszym rozwiązaniem jest jednak bezpiecznie usunąć stary token podczas uzyskiwania nowego. 

Ważne jest, aby dokonać rozróżnienia między klientami poufnymi a klientami publicznymi, ponieważ ma to wpływ na czas, przez jaki można używać tokenów odświeżania. Aby uzyskać więcej informacji na temat różnych typów klientów, zobacz [RFC 6749](https://tools.ietf.org/html/rfc6749#section-2.1).

#### <a name="token-lifetimes-with-confidential-client-refresh-tokens"></a>Okresy istnienia tokenu z poufnymi tokenami odświeżania klienta
Klienci poufni to aplikacje, które mogą bezpiecznie przechowywać hasło klienta (tajne). Mogą udowodnić, że żądania pochodzą z zabezpieczonej aplikacji klienckiej, a nie od złośliwego aktora. Na przykład aplikacja sieci web jest klientem poufnym, ponieważ może przechowywać klucz tajny klienta na serwerze sieci web. Nie jest narażony. Ponieważ przepływy te są bezpieczniejsze, domyślne okresy istnienia `until-revoked`tokenów odświeżania wystawione dla tych przepływów jest , nie można zmienić przy użyciu zasad i nie zostaną odwołane w przypadku dobrowolnego resetowania hasła.

#### <a name="token-lifetimes-with-public-client-refresh-tokens"></a>Okresy istnienia tokenu z tokenami odświeżania klienta publicznego

Klienci publiczni nie mogą bezpiecznie przechowywać hasła klienta (klucz tajny). Na przykład aplikacja dla systemu iOS/Android nie może zaciemnić klucz tajny od właściciela zasobu, więc jest uważany za klienta publicznego. Można ustawić zasady dotyczące zasobów, aby uniemożliwić tokeny odświeżania od klientów publicznych starszych niż określony okres uzyskiwania nowej pary tokenów dostępu/odświeżania. (Aby to zrobić, należy użyć właściwości Refresh`MaxInactiveTime`Token Max Inactive Time ( ).) Można również użyć zasad, aby ustawić okres, po upływie którego tokeny odświeżania nie są już akceptowane. (Aby to zrobić, należy użyć właściwości Odśwież token Max Age.) Można dostosować okres istnienia tokenu odświeżania, aby kontrolować, kiedy i jak często użytkownik jest wymagany do ponownego wniesienia poświadczeń, zamiast dyskretnie ponownie uwierzytelniony, podczas korzystania z publicznej aplikacji klienckiej.

> [!NOTE]
> Właściwość Max Age jest czasem, przez jaki można użyć pojedynczego tokenu. 

### <a name="id-tokens"></a>Tokeny identyfikatorów
Tokeny identyfikatorów są przekazywane do witryn sieci Web i klientów natywnych. Tokeny identyfikatorów zawierają informacje o profilu użytkownika. Token identyfikatora jest powiązany z określoną kombinacją użytkownika i klienta. Tokeny identyfikatorów są uważane za ważne do czasu ich wygaśnięcia. Zazwyczaj aplikacja sieci web dopasowuje okres istnienia sesji użytkownika w aplikacji do okresu istnienia tokenu identyfikatora wystawionego dla użytkownika. Można dostosować okres istnienia tokenu identyfikatora, aby kontrolować, jak często aplikacja sieci web wygasa sesji aplikacji i jak często wymaga ponownego uwierzytelnienia użytkownika za pomocą usługi Azure AD (dyskretnie lub interaktywnie).

### <a name="single-sign-on-session-tokens"></a>Tokeny sesji logowania jednokrotnego
Gdy użytkownik uwierzytelnia się za pomocą usługi Azure AD, sesja logowania jednokrotnego (Logowanie jednokrotne) jest ustanawiana za pomocą przeglądarki użytkownika i usługi Azure AD. Token SSO w postaci pliku cookie reprezentuje tę sesję. Token sesji SSO nie jest powiązany z określoną aplikacją zasobu/kliencka. Tokeny sesji SSO można odwołać, a ich ważność jest sprawdzana za każdym razem, gdy są używane.

Usługa Azure AD używa dwóch rodzajów tokenów sesji SSO: trwałe i nietrwałe. Tokeny sesji trwałej są przechowywane jako trwałe pliki cookie przez przeglądarkę. Nietrwiące tokeny sesji są przechowywane jako pliki cookie sesji. (Pliki cookie sesji są niszczone po zamknięciu przeglądarki). Zazwyczaj token sesji nietrwuchowej jest przechowywany. Jednak gdy użytkownik zaznaczy pole wyboru **Zachowaj mnie zalogowany** podczas uwierzytelniania, token sesji trwałej jest przechowywany.

Tokeny sesji nietrwuchowe mają okres istnienia 24 godzin. Trwałe tokeny mają okres istnienia 180 dni. Za każdym razem, gdy token sesji jednokrotnego jest używany w okresie ważności, okres ważności jest przedłużany o kolejne 24 godziny lub 180 dni, w zależności od typu tokenu. Jeśli token sesji SZO nie jest używany w okresie ważności, jest uważany za wygasły i nie jest już akceptowany.

Można użyć zasad, aby ustawić czas po wystawieniu tokenu pierwszej sesji, po przekroju którego token sesji nie jest już akceptowany. (Aby to zrobić, należy użyć właściwości Maksymalna wiek tokenu sesji). Można dostosować okres istnienia tokenu sesji, aby kontrolować, kiedy i jak często użytkownik jest wymagany do ponownego wniesienia poświadczeń, zamiast być dyskretnie uwierzytelniony, podczas korzystania z aplikacji sieci web.

### <a name="token-lifetime-policy-properties"></a>Właściwości zasad okresu istnienia tokenu
Zasady okresu istnienia tokenu to typ obiektu zasad, który zawiera reguły okresu istnienia tokenu. Użyj właściwości zasad, aby kontrolować określone okresy istnienia tokenu. Jeśli żadna zasada nie jest ustawiona, system wymusza domyślną wartość okresu istnienia.

### <a name="configurable-token-lifetime-properties"></a>Konfigurowalne właściwości okresu istnienia tokenu
| Właściwość | Ciąg właściwości zasad | Wpływa | Domyślne | Minimalne | Maksimum |
| --- | --- | --- | --- | --- | --- |
| Okres istnienia tokenu dostępu |AccessTokenLifetime<sup>2</sup> |Tokeny dostępu, tokeny identyfikatorów, tokeny SAML2 |1 godzina |10 minut |1 dzień |
| Maksymalny czas nieaktywnego tokenu odświeżania |MaxInactiveTime (Czas działania maksymalnego) |Odśwież tokeny |90 dni |10 minut |90 dni |
| Token odświeżania jednoczynnie Maksymalny wiek |MaxAgeSingleFactor (MaxAgeSingleFactor) |Tokeny odświeżania (dla wszystkich użytkowników) |Do odwołania |10 minut |Do odwołania<sup>1</sup> |
| Wieloczynnikowy token odświeżania Maksymalny wiek |MaxAgeMultiFactor (MaxAgeMultiFactor) |Tokeny odświeżania (dla wszystkich użytkowników) |Do odwołania |10 minut |Do odwołania<sup>1</sup> |
| Maksymalny wiek tokenu sesyjnego z jednym czynnikiem |MaxAgeSessionSingleFactor (MaxAgeSessionSingleFactor) |Tokeny sesji (trwałe i nietrwałe) |Do odwołania |10 minut |Do odwołania<sup>1</sup> |
| Wiek maksymalny tokenu sesji wieloczynnikowej |MaxAgeSessionMultiFactor (MaxAgeSessionMultiFactor) |Tokeny sesji (trwałe i nietrwałe) |Do odwołania |10 minut |Do odwołania<sup>1</sup> |

* <sup>1</sup>365 dni to maksymalna jawna długość, którą można ustawić dla tych atrybutów.
* <sup>2.</sup> Aby upewnić się, że klient sieci Web usługi Microsoft Teams działa, zaleca się, aby program AccessTokenLifetime był dłuższy niż 15 minut dla usługi Microsoft Teams.

### <a name="exceptions"></a>Wyjątki
| Właściwość | Wpływa | Domyślne |
| --- | --- | --- |
| Odśwież token Max Age (wydany dla użytkowników federacyjnych, którzy nie mają wystarczających informacji o odwołaniu<sup>1)</sup> |Tokeny odświeżania (wydane dla użytkowników federacyjnych, którzy nie mają wystarczających informacji o odwołaniu<sup>1)</sup> |12 godz. |
| Odśwież token Max Nieaktywny czas (wydany dla klientów poufnych) |Tokeny odświeżania (wydane dla klientów poufnych) |90 dni |
| Odśwież token Max Age (wydany dla klientów poufnych) |Tokeny odświeżania (wydane dla klientów poufnych) |Do odwołania |

* <sup>1.</sup> Federated użytkowników, którzy mają niewystarczające informacje o odwołaniu obejmują wszystkich użytkowników, którzy nie mają "LastPasswordChangeTimestamp" atrybut zsynchronizowany. Ci użytkownicy otrzymują ten krótki Maksymalny wiek, ponieważ aad nie jest w stanie zweryfikować, kiedy odwołać tokeny, które są powiązane ze starymi poświadczeniami (na przykład hasło, które zostało zmienione) i należy zaewidencjonować częściej, aby upewnić się, że użytkownik i skojarzone tokeny są nadal w dobrej kondycji. Aby poprawić to doświadczenie, administratorzy dzierżawy muszą upewnić się, że są one synchronizacji "LastPasswordChangeTimestamp" atrybut (można to ustawić na obiekcie użytkownika za pomocą programu Powershell lub za pośrednictwem AADSync).

### <a name="policy-evaluation-and-prioritization"></a>Ocena polityki i ustalanie priorytetów
Można utworzyć, a następnie przypisać zasady okresu istnienia tokenu do określonej aplikacji, do organizacji i do podmiotów obsługujących usługi. Wiele zasad może mieć zastosowanie do określonej aplikacji. Zasady okresu istnienia tokenu, które wchodzą w życie, są zgodne z następującymi regułami:

* Jeśli zasada jest jawnie przypisana do jednostki usługi, jest wymuszana.
* Jeśli żadna zasada nie jest jawnie przypisana do jednostki usługi, zasady jawnie przypisane do nadrzędnej organizacji jednostki usługi jest wymuszana.
* Jeśli żadna zasada nie jest jawnie przypisana do jednostki usługi lub do organizacji, zasady przypisane do aplikacji są wymuszane.
* Jeśli do jednostki usługi, organizacji lub obiektu aplikacji nie przypisano żadnych zasad, wartości domyślne są wymuszane. (Zobacz tabelę we [właściwościach okresu istnienia tokenu konfigurowalny.)](#configurable-token-lifetime-properties)

Aby uzyskać więcej informacji na temat relacji między obiektami aplikacji a obiektami jednostki usługi, zobacz [Obiekty głównej aplikacji i usługi w usłudze Azure Active Directory](app-objects-and-service-principals.md).

Ważność tokenu jest oceniana w momencie użycia tokenu. Zasady o najwyższym priorytecie w aplikacji, do których dostęp jest, wchodzą w życie.

Wszystkie okienka czasu używane w tym miejscu są sformatowane zgodnie z C# [TimeSpan](/dotnet/api/system.timespan) obiektu - D.HH:MM:SS.  Więc 80 dni i 30 minut będzie `80.00:30:00`.  Wiodący D może zostać usunięty, jeśli zero, `00:90:00`więc 90 minut będzie .  

> [!NOTE]
> Oto przykładowy scenariusz.
>
> Użytkownik chce uzyskać dostęp do dwóch aplikacji sieci web: Aplikacji sieci Web A i aplikacji sieci Web B.
> 
> Czynników:
> * Obie aplikacje sieci web znajdują się w tej samej organizacji nadrzędnej.
> * Zasady okresu istnienia tokenu 1 z maksymalnym wiekiem tokenu sesji wynosi osiem godzin, są ustawione jako domyślne zasady organizacji nadrzędnej.
> * Aplikacja sieci Web A jest aplikacją sieci web regularnego użytku i nie jest połączona z żadnymi zasadami.
> * Aplikacja sieci Web B jest używana dla procesów bardzo wrażliwych. Jego jednostki usługi jest połączony z zasadą okresu istnienia tokenu 2, który ma wiek maksymalny tokenu sesji 30 minut.
>
> O godzinie 12:00 użytkownik rozpoczyna nową sesję przeglądarki i próbuje uzyskać dostęp do aplikacji sieci Web A. Użytkownik jest przekierowywany do usługi Azure AD i jest proszony o zalogowanie się. Spowoduje to utworzenie pliku cookie, który ma token sesji w przeglądarce. Użytkownik jest przekierowywane z powrotem do aplikacji sieci Web A z tokenem identyfikatora, który umożliwia użytkownikowi dostęp do aplikacji.
>
> O godzinie 12:15 użytkownik próbuje uzyskać dostęp do aplikacji sieci Web B. Przeglądarka przekierowuje do usługi Azure AD, która wykrywa plik cookie sesji. Jednostka usługi aplikacji sieci Web B jest powiązana z zasadami okresu istnienia tokenu 2, ale jest również częścią organizacji nadrzędnej, z domyślną zasadą okresu istnienia tokenu 1. Zasady okresu istnienia tokenu 2 są skuteczne, ponieważ zasady połączone z jednostkami usług mają wyższy priorytet niż domyślne zasady organizacji. Token sesji został pierwotnie wystawiony w ciągu ostatnich 30 minut, więc jest uważany za prawidłowy. Użytkownik jest przekierowywane z powrotem do aplikacji sieci Web B z tokenem identyfikatora, który udziela im dostępu.
>
> O godzinie 13:00 użytkownik próbuje uzyskać dostęp do aplikacji sieci Web A. Użytkownik jest przekierowywał do usługi Azure AD. Aplikacja sieci Web A nie jest połączona z żadnymi zasadami, ale ponieważ jest w organizacji z domyślną zasadą okresu istnienia tokenu 1, ta zasada staje się skuteczna. Zostanie wykryty plik cookie sesji, który został pierwotnie wydany w ciągu ostatnich ośmiu godzin. Użytkownik jest dyskretnie przekierowywane z powrotem do aplikacji sieci Web A z nowym tokenem identyfikatora. Użytkownik nie jest zobowiązany do uwierzytelniania.
>
> Natychmiast po tym użytkownik próbuje uzyskać dostęp do aplikacji sieci Web B. Użytkownik jest przekierowywał do usługi Azure AD. Tak jak poprzednio, zasady okresu istnienia tokenu 2 wchodzą w życie. Ponieważ token został wystawiony ponad 30 minut temu, użytkownik jest monitowany o ponowne wniesienie poświadczeń logowania. Wydaje się zupełnie nowy token sesji i token identyfikatora. Użytkownik może następnie uzyskać dostęp do aplikacji sieci Web B.
>
>

## <a name="configurable-policy-property-details"></a>Konfigurowalne szczegóły właściwości zasad
### <a name="access-token-lifetime"></a>Okres istnienia tokenu dostępu
**Ciąg:** AccessTokenLifetime

**Wpływa na:** Tokeny dostępu, tokeny identyfikatorów, tokeny SAML

**Krótki opis:** Ta zasada określa, jak długo tokeny dostępu i identyfikatora dla tego zasobu są uważane za prawidłowe. Zmniejszenie właściwości Lifetime tokenu dostępu zmniejsza ryzyko tokenu dostępu lub tokenu identyfikatora używanego przez złośliwego aktora przez dłuższy okres czasu. (Tych tokenów nie można odwołać). Kompromisem jest to, że wydajność jest niekorzystnie wpływa, ponieważ tokeny muszą być wymieniane częściej.

### <a name="refresh-token-max-inactive-time"></a>Maksymalny czas nieaktywnego tokenu odświeżania
**Ciąg:** MaxInactiveTime (Czas działania maksymalnego)

**Wpływa na:** Odśwież tokeny

**Krótki opis:** Ta zasada określa, ile lat może być token odświeżania, zanim klient nie będzie mógł go już używać do pobierania nowej pary tokenów dostępu/odświeżania podczas próby uzyskania dostępu do tego zasobu. Ponieważ nowy token odświeżania zwykle jest zwracany, gdy używany jest token odświeżania, ta zasada uniemożliwia dostęp, jeśli klient próbuje uzyskać dostęp do dowolnego zasobu przy użyciu bieżącego tokenu odświeżania w określonym czasie.

Ta zasada wymusza użytkowników, którzy nie byli aktywni na swoim kliencie, aby ponownie uwierzytelnić, aby pobrać nowy token odświeżania.

Właściwość Refresh Token Max Inactive Time musi być ustawiona na niższą wartość niż maksymalny wiek tokenu jednoczynno-factor i właściwości Maksymalny wiek tokenu odświeżania wielu czynników.

### <a name="single-factor-refresh-token-max-age"></a>Token odświeżania jednoczynnie Maksymalny wiek
**Ciąg:** MaxAgeSingleFactor (MaxAgeSingleFactor)

**Wpływa na:** Odśwież tokeny

**Krótki opis:** Ta zasada określa, jak długo użytkownik może używać tokenu odświeżania, aby uzyskać nową parę tokenów dostępu/odświeżania po zakończeniu pomyślnie uwierzytelnienia przy użyciu tylko jednego czynnika. Po uwierzytelnieniu użytkownika i odbiera nowy token odświeżania, użytkownik może użyć przepływu tokenu odświeżania przez określony czas. (Jest to prawdą, tak długo, jak bieżący token odświeżania nie jest odwołany i nie jest nieużywane przez dłuższy czas niż czas nieaktywny.) W tym momencie użytkownik jest zmuszony do ponownego uwierzytelnienia, aby otrzymać nowy token odświeżania.

Zmniejszenie maksymalnego wieku zmusza użytkowników do uwierzytelniania częściej. Ponieważ uwierzytelnianie jednoskładnikowe jest uważane za mniej bezpieczne niż uwierzytelnianie wieloskładnikowe, zaleca się ustawienie tej właściwości na wartość równą lub mniejszą niż właściwość Multi-Factor Refresh Token Max Age.

### <a name="multi-factor-refresh-token-max-age"></a>Wieloczynnikowy token odświeżania Maksymalny wiek
**Ciąg:** MaxAgeMultiFactor (MaxAgeMultiFactor)

**Wpływa na:** Odśwież tokeny

**Krótki opis:** Ta zasada określa, jak długo użytkownik może używać tokenu odświeżania, aby uzyskać nową parę tokenów dostępu/odświeżania po zakończeniu pomyślnie uwierzytelnienia przy użyciu wielu czynników. Po uwierzytelnieniu użytkownika i odbiera nowy token odświeżania, użytkownik może użyć przepływu tokenu odświeżania przez określony czas. (Jest to prawdą, tak długo, jak bieżący token odświeżania nie jest odwołany i nie jest nieużywane przez dłuższy czas niż czas nieaktywny.) W tym momencie użytkownicy są zmuszeni do ponownego uwierzytelnienia, aby otrzymać nowy token odświeżania.

Zmniejszenie maksymalnego wieku zmusza użytkowników do uwierzytelniania częściej. Ponieważ uwierzytelnianie jednoskładnikowe jest uważane za mniej bezpieczne niż uwierzytelnianie wieloskładnikowe, zaleca się ustawienie tej właściwości na wartość równą lub większą niż właściwość Max Age tokenu odświeżania jednoskładnikowego.

### <a name="single-factor-session-token-max-age"></a>Maksymalny wiek tokenu sesyjnego z jednym czynnikiem
**Ciąg:** MaxAgeSessionSingleFactor (MaxAgeSessionSingleFactor)

**Wpływa na:** Tokeny sesji (trwałe i nietrwałe)

**Krótki opis:** Ta zasada określa, jak długo użytkownik może używać tokenu sesji, aby uzyskać nowy identyfikator i token sesji po ostatnim uwierzytelnianiu pomyślnie przy użyciu tylko jednego czynnika. Po uwierzytelnieniu się użytkownika i odebraniu nowego tokenu sesji użytkownik może użyć przepływu tokenu sesji przez określony czas. (Jest to prawdą, tak długo, jak bieżący token sesji nie jest odwołany i nie wygasł.) Po upływie określonego czasu użytkownik jest zmuszony do ponownego uwierzytelnienia, aby otrzymać nowy token sesji.

Zmniejszenie maksymalnego wieku zmusza użytkowników do uwierzytelniania częściej. Ponieważ uwierzytelnianie jednoskładnikowe jest uważane za mniej bezpieczne niż uwierzytelnianie wieloskładnikowe, zaleca się ustawienie tej właściwości na wartość równą lub mniejszą niż właściwość Multi-Factor Session Token Max Age.

### <a name="multi-factor-session-token-max-age"></a>Wiek maksymalny tokenu sesji wieloczynnikowej
**Ciąg:** MaxAgeSessionMultiFactor (MaxAgeSessionMultiFactor)

**Wpływa na:** Tokeny sesji (trwałe i nietrwałe)

**Krótki opis:** Ta zasada określa, jak długo użytkownik może używać tokenu sesji, aby uzyskać nowy identyfikator i token sesji po ostatnim uwierzytelnianiu pomyślnie przy użyciu wielu czynników. Po uwierzytelnieniu się użytkownika i odebraniu nowego tokenu sesji użytkownik może użyć przepływu tokenu sesji przez określony czas. (Jest to prawdą, tak długo, jak bieżący token sesji nie jest odwołany i nie wygasł.) Po upływie określonego czasu użytkownik jest zmuszony do ponownego uwierzytelnienia, aby otrzymać nowy token sesji.

Zmniejszenie maksymalnego wieku zmusza użytkowników do uwierzytelniania częściej. Ponieważ uwierzytelnianie jednoskładnikowe jest uważane za mniej bezpieczne niż uwierzytelnianie wieloskładnikowe, zaleca się ustawienie tej właściwości na wartość równą lub większą niż właściwość Max Age tokenu sesji jednoskładnikowej.

## <a name="example-token-lifetime-policies"></a>Przykładowe zasady okresu istnienia tokenu
Wiele scenariuszy jest możliwe w usłudze Azure AD, gdy można tworzyć okresy istnienia tokenów i zarządzać nimi dla aplikacji, podmiotów korzystających z usług i ogólnej organizacji. W tej sekcji przechodzimy przez kilka typowych scenariuszy zasad, które mogą pomóc w narzuceniu nowych reguł dla:

* Czas życia tokenu
* Maksymalny czas nieaktywny tokenu
* Wiek tokenu Max

W przykładach możesz dowiedzieć się, jak:

* Zarządzanie domyślnymi zasadami organizacji
* Tworzenie zasad logowania do sieci Web
* Tworzenie zasad dla aplikacji natywnej, która wywołuje internetowy interfejs API
* Zarządzanie zaawansowanymi zasadami

### <a name="prerequisites"></a>Wymagania wstępne
W poniższych przykładach można utworzyć, zaktualizować, połączyć i usunąć zasady dla aplikacji, podmiotów usług i ogólnej organizacji. Jeśli jesteś nowy w usłudze Azure AD, zaleca się, aby dowiedzieć się, [jak uzyskać dzierżawy usługi Azure AD](quickstart-create-new-tenant.md) przed kontynuowaniem tych przykładów.  

Aby rozpocząć, wykonaj następujące czynności:

1. Pobierz najnowszą [wersję publicznej wersji programu Azure AD PowerShell Module Public Preview](https://www.powershellgallery.com/packages/AzureADPreview).
2. Uruchom `Connect` polecenie, aby zalogować się do konta administratora usługi Azure AD. Uruchom to polecenie przy każdym uruchomieniu nowej sesji.

    ```powershell
    Connect-AzureAD -Confirm
    ```

3. Aby wyświetlić wszystkie zasady utworzone w organizacji, uruchom następujące polecenie. Uruchom to polecenie po większości operacji w następujących scenariuszach. Uruchomienie polecenia pomaga również uzyskać ** ** zasad.

    ```powershell
    Get-AzureADPolicy
    ```

### <a name="example-manage-an-organizations-default-policy"></a>Przykład: zarządzanie domyślnymi zasadami organizacji
W tym przykładzie utworzysz zasadę, która umożliwia użytkownikom rzadziej logowanie się w całej organizacji. Aby to zrobić, należy utworzyć zasady okresu istnienia tokenu dla tokenów odświeżania jednoskładnikowego, które są stosowane w całej organizacji. Zasady są stosowane do każdej aplikacji w organizacji i do każdej jednostki usługi, która nie ma jeszcze zestawu zasad.

1. Tworzenie zasad okresu istnienia tokenu.

    1. Ustaw token odświeżania jednoskładnikowego na "dopóki nie zostanie odwołany". Token nie wygasa, dopóki dostęp nie zostanie odwołany. Utwórz następującą definicję zasad:

        ```powershell
        @('{
            "TokenLifetimePolicy":
            {
                "Version":1,
                "MaxAgeSingleFactor":"until-revoked"
            }
        }')
        ```

    1. Aby utworzyć zasadę, uruchom następujące polecenie:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1, "MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "OrganizationDefaultPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Aby usunąć wszystkie odstępy, uruchom następujące polecenie:

        ```powershell
        Get-AzureADPolicy -id | set-azureadpolicy -Definition @($((Get-AzureADPolicy -id ).Replace(" ","")))
        ```

    1. Aby wyświetlić nowe zasady i uzyskać **identyfikator objectid**zasad, uruchom następujące polecenie:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Zaktualizuj zasady.

    Możesz zdecydować, że pierwsza zasada ustawiona w tym przykładzie nie jest tak ścisła, jak wymaga tego usługa. Aby ustawić wygaśnięcie tokenu odświeżania jednoskładnikowego w ciągu dwóch dni, uruchom następujące polecenie:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName $policy.DisplayName -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"2.00:00:00"}}')
    ```

### <a name="example-create-a-policy-for-web-sign-in"></a>Przykład: tworzenie zasad logowania do sieci Web

W tym przykładzie utworzysz zasadę, która wymaga od użytkowników częstszego uwierzytelniania w aplikacji sieci web. Ta zasada ustawia okres istnienia tokenów dostępu/identyfikatora i maksymalny wiek tokenu sesji wieloczynnikowej na jednostkę usługi aplikacji sieci web.

1. Tworzenie zasad okresu istnienia tokenu.

    Ta zasada, dla logowania w sieci Web, ustawia okres istnienia tokenu dostępu/identyfikatora i maksymalny wiek tokenu sesji jednoskładnikowego na dwie godziny.

    1. Aby utworzyć zasadę, uruchom to polecenie:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00","MaxAgeSessionSingleFactor":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Aby wyświetlić nowe zasady i uzyskać zasadę **ObjectId**, uruchom następujące polecenie:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Przypisz zasady do jednostki usługi. Należy również uzyskać **ObjectId** jednostki usługi.

    1. Użyj polecenia cmdlet [Get-AzureADServicePrincipal,](/powershell/module/azuread/get-azureadserviceprincipal) aby wyświetlić wszystkie jednostki usługi organizacji lub jednego podmiotu usługi.
        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
        ```

    1. Jeśli masz jednostkę usługi, uruchom następujące polecenie:
        ```powershell
        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

### <a name="example-create-a-policy-for-a-native-app-that-calls-a-web-api"></a>Przykład: Tworzenie zasad dla aplikacji macierzystej, która wywołuje internetowy interfejs API
W tym przykładzie należy utworzyć zasadę, która wymaga od użytkowników uwierzytelniania rzadziej. Zasady wydłużają również czas, przez jaki użytkownik może być nieaktywny, zanim użytkownik będzie musiał ponownie uwierzytelnić. Zasady są stosowane do internetowego interfejsu API. Gdy natywna aplikacja żąda internetowego interfejsu API jako zasobu, ta zasada jest stosowana.

1. Tworzenie zasad okresu istnienia tokenu.

    1. Aby utworzyć ścisłe zasady dla internetowego interfejsu API, uruchom następujące polecenie:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"30.00:00:00","MaxAgeMultiFactor":"until-revoked","MaxAgeSingleFactor":"180.00:00:00"}}') -DisplayName "WebApiDefaultPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
        ```

    1. Aby wyświetlić nowe zasady, uruchom następujące polecenie:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Przypisz zasady do internetowego interfejsu API. Należy również uzyskać **ObjectId** aplikacji. Użyj polecenia cmdlet [Get-AzureADApplication,](/powershell/module/azuread/get-azureadapplication) aby znaleźć **identyfikator obiektu ObjectId**aplikacji lub użyć [portalu Azure.](https://portal.azure.com/)

    Pobierz **objectid** aplikacji i przypisać zasady:

    ```powershell
    # Get the application
    $app = Get-AzureADApplication -Filter "DisplayName eq 'Fourth Coffee Web API'"

    # Assign the policy to your web API.
    Add-AzureADApplicationPolicy -Id $app.ObjectId -RefObjectId $policy.Id
    ```

### <a name="example-manage-an-advanced-policy"></a>Przykład: Zarządzanie zaawansowanymi zasadami
W tym przykładzie utworzysz kilka zasad, aby dowiedzieć się, jak działa system priorytetu. Dowiesz się również, jak zarządzać wieloma zasadami, które są stosowane do kilku obiektów.

1. Tworzenie zasad okresu istnienia tokenu.

    1. Aby utworzyć domyślną zasadę organizacji, która ustawia okres istnienia tokenu odświeżania jednoskładnikowego na 30 dni, uruchom następujące polecenie:

        ```powershell
        $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"30.00:00:00"}}') -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
        ```

    1. Aby wyświetlić nowe zasady, uruchom następujące polecenie:

        ```powershell
        Get-AzureADPolicy -Id $policy.Id
        ```

1. Przypisz zasady do jednostki usługi.

    Teraz masz zasady, które mają zastosowanie do całej organizacji. Można zachować tę zasadę 30-dniową dla określonego podmiotu usługi, ale zmienić domyślną zasadę organizacji na górny limit "do odwołania".

    1. Aby wyświetlić wszystkie jednostki usługi organizacji, należy użyć polecenia cmdlet [Get-AzureADServicePrincipal.](/powershell/module/azuread/get-azureadserviceprincipal)

    1. Jeśli masz jednostkę usługi, uruchom następujące polecenie:

        ```powershell
        # Get ID of the service principal
        $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"

        # Assign policy to a service principal
        Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
        ```

1. Ustaw `IsOrganizationDefault` flagę na false:

    ```powershell
    Set-AzureADPolicy -Id $policy.Id -DisplayName "ComplexPolicyScenario" -IsOrganizationDefault $false
    ```

1. Utwórz nową zasadę domyślną organizacji:

    ```powershell
    New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxAgeSingleFactor":"until-revoked"}}') -DisplayName "ComplexPolicyScenarioTwo" -IsOrganizationDefault $true -Type "TokenLifetimePolicy"
    ```

    Masz teraz oryginalne zasady połączone z jednostką usługi, a nowa zasada jest ustawiona jako domyślna zasada organizacji. Należy pamiętać, że zasady stosowane do podmiotów nych usług mają pierwszeństwo przed domyślnymi zasadami organizacji.

## <a name="cmdlet-reference"></a>Dokumentacja poleceń cmdlet

### <a name="manage-policies"></a>Zarządzanie zasadami

Do zarządzania zasadami można używać następujących poleceń cmdlet.

#### <a name="new-azureadpolicy"></a>Nowa AzureAdPolicy

Tworzy nową zasadę.

```powershell
New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsOrganizationDefault <boolean> -Type <Policy Type>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Definition</code> |Tablica stringified JSON, który zawiera wszystkie reguły zasad. | `-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;DisplayName</code> |Ciąg nazwy zasad. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;IsOrganizationDefault</code> |Jeśli zasada true, ustawia zasady jako domyślne zasady organizacji. Jeśli jest fałszywy, nic nie robi. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code> |Typ zasad. W przypadku okresów istnienia tokenu należy zawsze używać funkcji "TokenLifetimePolicy". | `-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>[Opcjonalnie] |Ustawia alternatywny identyfikator dla zasad. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureAdPolicy
Pobiera wszystkie zasady usługi Azure AD lub określone zasady.

```powershell
Get-AzureADPolicy
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code>[Opcjonalnie] |**ObjectId (ID)** żądanej zasady. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject
Pobiera wszystkie aplikacje i podmioty usługi, które są połączone z zasadami.

```powershell
Get-AzureADPolicyAppliedObject -Id <ObjectId of Policy>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** żądanej zasady. |`-Id <ObjectId of Policy>` |

</br></br>

#### <a name="set-azureadpolicy"></a>Ustaw AzureADPolicy
Aktualizuje istniejące zasady.

```powershell
Set-AzureADPolicy -Id <ObjectId of Policy> -DisplayName <string>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** żądanej zasady. |`-Id <ObjectId of Policy>` |
| <code>&#8209;DisplayName</code> |Ciąg nazwy zasad. |`-DisplayName "MyTokenPolicy"` |
| <code>&#8209;Definition</code>[Opcjonalnie] |Tablica stringified JSON, który zawiera wszystkie reguły zasad. |`-Definition @('{"TokenLifetimePolicy":{"Version":1,"MaxInactiveTime":"20:00:00"}}')` |
| <code>&#8209;IsOrganizationDefault</code>[Opcjonalnie] |Jeśli zasada true, ustawia zasady jako domyślne zasady organizacji. Jeśli jest fałszywy, nic nie robi. |`-IsOrganizationDefault $true` |
| <code>&#8209;Type</code>[Opcjonalnie] |Typ zasad. W przypadku okresów istnienia tokenu należy zawsze używać funkcji "TokenLifetimePolicy". |`-Type "TokenLifetimePolicy"` |
| <code>&#8209;AlternativeIdentifier</code>[Opcjonalnie] |Ustawia alternatywny identyfikator dla zasad. |`-AlternativeIdentifier "myAltId"` |

</br></br>

#### <a name="remove-azureadpolicy"></a>Usuń-AzureADPolicy
Usuwa określone zasady.

```powershell
 Remove-AzureADPolicy -Id <ObjectId of Policy>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** żądanej zasady. | `-Id <ObjectId of Policy>` |

</br></br>

### <a name="application-policies"></a>Zasady aplikacji
W zasadach aplikacji można użyć następujących poleceń cmdlet.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>Add-AzureADPolicy aplikacji
Łączy określone zasady z aplikacją.

```powershell
Add-AzureADApplicationPolicy -Id <ObjectId of Application> -RefObjectId <ObjectId of Policy>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** aplikacji. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** zasad. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy
Pobiera zasady, która jest przypisana do aplikacji.

```powershell
Get-AzureADApplicationPolicy -Id <ObjectId of Application>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** aplikacji. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Usuń AzureADPolicy aplikacji
Usuwa zasady z aplikacji.

```powershell
Remove-AzureADApplicationPolicy -Id <ObjectId of Application> -PolicyId <ObjectId of Policy>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** aplikacji. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** zasad. | `-PolicyId <ObjectId of Policy>` |

</br></br>

### <a name="service-principal-policies"></a>Zasady jednostkowe usługi
Następujące polecenia cmdlet można użyć dla zasad jednostkowych usług.

#### <a name="add-azureadserviceprincipalpolicy"></a>Add-AzureADServicePrincipalPolicy
Łączy określone zasady z jednostką usługi.

```powershell
Add-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal> -RefObjectId <ObjectId of Policy>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** aplikacji. | `-Id <ObjectId of Application>` |
| <code>&#8209;RefObjectId</code> |**ObjectId** zasad. | `-RefObjectId <ObjectId of Policy>` |

</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureAdServicePrincipalPolicy
Pobiera wszelkie zasady połączone z określonego podmiotu usługi.

```powershell
Get-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** aplikacji. | `-Id <ObjectId of Application>` |

</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Usuń-AzureADServicePrincipalPolicy
Usuwa zasady z określonego podmiotu usługi.

```powershell
Remove-AzureADServicePrincipalPolicy -Id <ObjectId of ServicePrincipal>  -PolicyId <ObjectId of Policy>
```

| Parametry | Opis | Przykład |
| --- | --- | --- |
| <code>&#8209;Id</code> |**ObjectId (ID)** aplikacji. | `-Id <ObjectId of Application>` |
| <code>&#8209;PolicyId</code> |**ObjectId** zasad. | `-PolicyId <ObjectId of Policy>` |
