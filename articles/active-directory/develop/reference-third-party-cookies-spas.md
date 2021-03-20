---
title: Jak obsługiwać usługę inteligentnego śledzenia (ITP) w przeglądarce Safari | Azure
titleSuffix: Microsoft identity platform
description: Uwierzytelnianie jednostronicowe aplikacji (SPA), gdy pliki cookie innych firm nie są już dozwolone.
services: active-directory
author: hpsin
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: hirsin
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: eed4e919684575bb2c63170d91517b661fac4acf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98753961"
---
# <a name="handle-itp-in-safari-and-other-browsers-where-third-party-cookies-are-blocked"></a>Obsługa ITP w przeglądarce Safari i innych przeglądarkach, w których są blokowane pliki cookie innych firm

Obecnie wiele przeglądarek blokuje pliki cookie innych firm — pliki cookie w żądaniach do domen, które nie są takie same jak te, które są wyświetlane na pasku przeglądarki. Powoduje to przerwanie przepływu niejawnego i wymaga nowych wzorców uwierzytelniania do pomyślnego logowania użytkowników. Na platformie tożsamości firmy Microsoft używamy przepływu autoryzacji z tokenami PKCE i Refresh, aby uniemożliwić użytkownikom logowanie się podczas blokowania plików cookie innych firm.

## <a name="what-is-intelligent-tracking-protection-itp"></a>Co to jest usługa Intelligent Tracking Protection (ITP)?

Apple Safari ma dostęp do domyślnej funkcji ochrony prywatności o nazwie [Intelligent Tracking Protection](https://webkit.org/tracking-prevention-policy/)lub *itp*. ITP blokuje pliki cookie innych firm, pliki cookie na żądaniach, które są między domenami.

Wspólna forma śledzenia użytkowników odbywa się przez załadowanie elementu IFRAME do witryny innej firmy w tle i użycie plików cookie do skorelowania użytkownika przez Internet. Niestety, ten wzorzec jest również standardowym sposobem implementacji [niejawnego przepływu](v2-oauth2-implicit-grant-flow.md) w aplikacjach jednostronicowych (aplikacji jednostronicowych). Gdy przeglądarka blokuje pliki cookie innych firm, aby uniemożliwić śledzenie użytkowników, aplikacji jednostronicowych również są uszkodzone.

Przeglądarka Safari nie jest jedynym sposobem blokowania plików cookie innych firm w celu zwiększenia prywatności użytkowników. Brave domyślnie zablokował pliki cookie innych firm, a chrom (platforma w tle Google Chrome i Microsoft Edge) ogłosił, że w przyszłości przestaną obsługiwać obsługę plików cookie innych firm.

Rozwiązanie opisane w tym artykule działa we wszystkich tych przeglądarkach lub zablokowaniu plików cookie innych firm.

## <a name="overview-of-the-solution"></a>Przegląd rozwiązania

Aby kontynuować uwierzytelnianie użytkowników w usłudze aplikacji jednostronicowych, deweloperzy aplikacji muszą używać [przepływu kodu autoryzacji](v2-oauth2-auth-code-flow.md). W przepływie kodu uwierzytelniania dostawca tożsamości wystawia kod, a SPA używa kodu dla tokenu dostępu i tokenu odświeżania. Gdy aplikacja wymaga dodatkowych tokenów, może użyć [przepływu tokenu odświeżania](v2-oauth2-auth-code-flow.md#refresh-the-access-token) , aby uzyskać nowe tokeny. MSAL.js 2,0, Biblioteka Microsoft Identity platform Library for aplikacji jednostronicowych, implementuje przepływ kodu autoryzacji dla aplikacji jednostronicowych i, z drobnymi aktualizacjami, jest zamiennikiem dla MSAL.js 1. x.

W przypadku platformy tożsamości firmy Microsoft aplikacji jednostronicowych i natywnych klientów postępuj zgodnie z podobnymi wskazówkami dotyczącymi protokołów:

* Użycie [wyzwania kodu PKCE](https://tools.ietf.org/html/rfc7636)
    * PKCE jest *wymagany* do aplikacji jednostronicowych na platformie tożsamości firmy Microsoft. PKCE jest *zalecane* dla klientów natywnych i poufnych.
* Brak użycia klucza tajnego klienta

Aplikacji jednostronicowych mają dwa dodatkowe ograniczenia:

* [Identyfikator URI przekierowania musi być oznaczony jako `spa` Typ](v2-oauth2-auth-code-flow.md#redirect-uri-setup-required-for-single-page-apps) Aby włączyć funkcję CORS dla punktów końcowych logowania.
* Odświeżanie tokenów wystawionych za pomocą przepływu kodu autoryzacji w celu `spa` przekierowania identyfikatorów URI ma 24-godzinny okres istnienia, a nie okres istnienia 90 dni.

:::image type="content" source="media/v2-oauth-auth-code-spa/active-directory-oauth-code-spa.svg" alt-text="Diagram przedstawiający przepływ kodu autoryzacji OAuth 2 między aplikacją jednostronicową a punktem końcowym usługi tokenu zabezpieczającego." border="false":::

## <a name="performance-and-ux-implications"></a>Wpływ na wydajność i środowisko użytkownika

Niektóre aplikacje używające próby niejawnego przepływu nie przekierują się, otwierając element IFRAME logowania przy użyciu polecenia `prompt=none` . W większości przeglądarek to żądanie będzie odpowiadać tokenom aktualnie zalogowanego użytkownika (przy założeniu, że zgoda została już udzielona). Ten wzorzec przeznaczony dla aplikacji nie wymagał przekierowania pełnych stron w celu podpisania użytkownika w programie, poprawy wydajności i środowiska użytkownika — użytkownik odwiedza stronę internetową i jest już zalogowany. Ponieważ `prompt=none` w elemencie iframe nie jest już dostępna opcja w przypadku zablokowania plików cookie innych firm, aplikacje muszą odwiedzić stronę logowania w ramce najwyższego poziomu, aby otrzymać kod autoryzacji.

Istnieją dwa sposoby realizacji logowania:

* **Przekierowania pełnego strony**
    * Podczas pierwszego ładowania SPA Przekieruj użytkownika na stronę logowania, jeśli żadna sesja już nie istnieje (lub jeśli sesja wygasła). Przeglądarka użytkownika zostanie odwiedzana na stronie logowania, zaprezentowania plików cookie zawierających sesję użytkownika, a następnie przekierować z powrotem do aplikacji z kodem i tokenami w fragmencie.
    * Przekierowanie powoduje dwa razy załadowanie SPA. Postępuj zgodnie z najlepszymi rozwiązaniami dotyczącymi buforowania aplikacji jednostronicowych, aby aplikacja nie została pobrana w całości.
    * Rozważ przeprowadzenie sekwencji wstępnego ładowania w aplikacji, która sprawdza, czy sesja logowania i przekierowuje do strony logowania, zanim aplikacja w pełni rozpakuje i wykona ładunek JavaScript.
* **Okna podręczne**
    * Jeśli środowisko użytkownika (UX) przekierowania pełnego strony nie działa dla aplikacji, rozważ użycie menu podręcznego do obsługi uwierzytelniania.
    * Gdy menu podręczne zakończy przekierowywanie do aplikacji po uwierzytelnieniu, kod w programie obsługi przekierowania będzie przechowywał kod i tokeny w magazynie lokalnym do użycia przez aplikację. MSAL.js obsługuje okienka podręczne do uwierzytelniania, tak jak w przypadku większości bibliotek.
    * Przeglądarki zmniejszają obsługę okien podręcznych, dlatego mogą nie być najbardziej niezawodne. Interakcja użytkownika z SPA przed utworzeniem okna podręcznego może być wymagana w celu spełnienia wymagań przeglądarki.

>[!NOTE]
> Firma Apple [opisuje metodę podręczną](https://webkit.org/blog/8311/intelligent-tracking-prevention-2-0/) jako tymczasową poprawkę zgodności, aby zapewnić oryginalnemu programowi dostęp do plików cookie innych firm. Firma Apple może usunąć ten przekazanie uprawnień w przyszłości, nie będzie to miało wpływu na wskazówki. W tym miejscu zostanie wyskakujące okienko nawigacji pierwszej strony do strony logowania, aby znaleźć sesję i można było dostarczyć kod uwierzytelniania. Powinno to kontynuować pracę w przyszłości.

### <a name="a-note-on-iframe-apps"></a>Uwaga dotycząca aplikacji iframe

Typowym wzorcem w usłudze Web Apps jest użycie elementu IFRAME do osadzenia jednej aplikacji w innej. Ramka najwyższego poziomu obsługuje uwierzytelnianie użytkownika, a aplikacja hostowana w elemencie iframe może ufać, że użytkownik jest zalogowany, pobierając tokeny dyskretnie przy użyciu niejawnego przepływu. Pozyskiwanie tokenów dyskretnych nie działa już w przypadku blokowania plików cookie innych firm — aplikacja osadzona w elemencie iframe musi przełączać się do korzystania z okien podręcznych w celu uzyskania dostępu do sesji użytkownika, ponieważ nie można przejść do strony logowania.

## <a name="security-implications-of-refresh-tokens-in-the-browser"></a>Implikacje związane z bezpieczeństwem tokenów odświeżania w przeglądarce

Wystawianie tokenów odświeżania przeglądarki jest traktowane jako problem z zabezpieczeniami. Ataki na skrypty między lokacjami (XSS) lub naruszone pakiety JS mogą wykraść token odświeżania i korzystać z niego zdalnie, dopóki nie wygaśnie lub zostanie odwołany. Aby zminimalizować ryzyko kradzieży tokenów odświeżania, aplikacji jednostronicowych będą wystawiane tokeny, które są ważne tylko przez 24 godziny. Po 24 godzinach aplikacja musi uzyskać nowy kod autoryzacji za pośrednictwem ramki najwyższego poziomu na stronie logowania.

Ten wzorzec tokenów odświeżania o ograniczonym okresie istnienia został wybrany jako równowaga między zabezpieczeniami i zdegradowanym środowiskiem użytkownika. Bez tokenów odświeżania lub plików cookie innych firm przepływ kodu autoryzacji (zgodnie z zaleceniami zalecanymi przez [wersję roboczą najlepszych praktyk dotyczących zabezpieczeń OAuth](https://tools.ietf.org/html/draft-ietf-oauth-security-topics-14)) jest uciążliwy, gdy wymagane są nowe lub dodatkowe tokeny. Dla każdego pojedynczego tokenu jest wymagana przekierowanie lub wyskakujące okienko, za każdym razem, gdy token wygaśnie (co godzinę zazwyczaj dla tokenów platformy tożsamości firmy Microsoft).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [przepływie kodu autoryzacji](v2-oauth2-auth-code-flow.md).

Wypróbuj przepływ kodu autoryzacji za pomocą [ przewodnika Szybki Start dlaMSAL.js 2,0](quickstart-v2-javascript-auth-code.md).
