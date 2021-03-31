---
title: Informacje o niejawnym przepływie OAuth2 w usłudze Azure AD | Microsoft Docs
description: Dowiedz się więcej o implementacji niejawnego przepływu OAuth2 przez Azure Active Directory oraz o tym, czy jest on odpowiedni dla Twojej aplikacji.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.topic: conceptual
ms.workload: identity
ms.date: 08/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: eaa3844bfbbef8cb71dbe8691cab894c921ce00a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "80154512"
---
# <a name="understanding-the-oauth2-implicit-grant-flow-in-azure-active-directory-ad"></a>Informacje o niejawnym przepływie OAuth2 w Azure Active Directory (AD)

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Niejawne przyznanie OAuth2 jest Notorious do przyznania z najdłuższym wykazem zagadnień związanych z bezpieczeństwem w specyfikacji OAuth2. Jeszcze jest to podejście zaimplementowane przez bibliotekę ADAL JS i jest to zalecane w przypadku pisania aplikacji SPA. Co daje? Jest to bardzo istotne znaczenie: i w miarę wyłączania, niejawne przyznanie jest najlepszym rozwiązaniem w przypadku aplikacji korzystających z internetowego interfejsu API za pośrednictwem języka JavaScript z przeglądarki.

## <a name="what-is-the-oauth2-implicit-grant"></a>Co to jest niejawne OAuth2?

[Przyznanie kodu autoryzacji Quintessential OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.1) jest przyznawanie autoryzacji, która używa dwóch oddzielnych punktów końcowych. Punkt końcowy autoryzacji jest używany dla fazy interakcji użytkownika, która powoduje kod autoryzacji. Punkt końcowy tokenu jest następnie używany przez klienta do wymiany kodu dla tokenu dostępu i często również tokenu odświeżania. Aplikacje sieci Web są wymagane do zaprezentowania własnych poświadczeń aplikacji do punktu końcowego tokenu, dzięki czemu serwer autoryzacji może uwierzytelniać klienta.

[Niejawne przyznanie OAuth2](https://tools.ietf.org/html/rfc6749#section-1.3.2) jest wariantem innych dotacji do autoryzacji. Umożliwia klientowi uzyskanie tokenu dostępu (i id_token, w przypadku korzystania z programu [OpenID Connect Connect](https://openid.net/specs/openid-connect-core-1_0.html)) bezpośrednio z punktu końcowego autoryzacji, bez kontaktowania się z punktem końcowym tokenu ani uwierzytelnianiem klienta. Ten wariant został zaprojektowany dla aplikacji opartych na języku JavaScript działających w przeglądarce sieci Web: w pierwotnej specyfikacji OAuth2 tokeny są zwracane w fragmencie identyfikatora URI. Powoduje to, że bity usługi BITS są dostępne dla kodu JavaScript w kliencie, ale gwarantuje, że nie zostaną uwzględnione w przekierowaniach skierowanych na serwer. W przypadku niejawnego udzielenia OAuth2 punkt końcowy autoryzacji wystawia tokeny dostępu bezpośrednio do klienta przy użyciu identyfikatora URI przekierowania, który został wcześniej dostarczony. Pozwala również wyeliminować wszelkie wymagania dotyczące wywołań między źródłami, które są niezbędne, jeśli aplikacja JavaScript jest wymagana do skontaktowania się z punktem końcowym tokenu.

Ważną cechą niejawnego udzielenia OAuth2 jest fakt, że takie przepływy nigdy nie zwracają tokenów odświeżania do klienta. W następnej sekcji pokazano, jak to nie jest konieczne i w rzeczywistości występuje problem z zabezpieczeniami.

## <a name="suitable-scenarios-for-the-oauth2-implicit-grant"></a>Odpowiednie scenariusze dla niejawnego udzielenia OAuth2

Specyfikacja OAuth2 deklaruje, że niejawny przydział został opracowany w celu włączenia aplikacji agenta użytkownika — to znaczy, że aplikacje JavaScript działają w przeglądarce. Definiowanie cech takich aplikacji polega na tym, że kod JavaScript jest używany do uzyskiwania dostępu do zasobów serwera (zazwyczaj internetowego interfejsu API) i odpowiednio do aktualizowania środowiska użytkownika aplikacji. Pomyśl o aplikacjach, takich jak Gmail lub Outlook Dostęp w sieci Web: po wybraniu wiadomości ze skrzynki odbiorczej tylko panel wizualizacji komunikatów zmieni się, aby wyświetlić nowy wybór, podczas gdy reszta strony pozostanie niezmodyfikowana. Ta cecha różni się od tradycyjnych aplikacji sieci Web opartych na przekierowaniu, w przypadku których każda interakcja użytkownika powoduje pełne ogłaszanie zwrotne i pełne strony nowej odpowiedzi serwera.

Aplikacje korzystające z podejścia opartego na języku JavaScript do jego skrajnych metod są nazywane jednostronicowymi aplikacjami lub aplikacji jednostronicowych. Pomysłem jest to, że te aplikacje obsługują tylko początkową stronę HTML i powiązane z nią JavaScript, a wszystkie kolejne interakcje są realizowane przez wywołania interfejsu API sieci Web wykonywane za pośrednictwem języka JavaScript. Jednak podejście hybrydowe, gdzie aplikacja jest oparta na odświeżeniu, ale wykonuje sporadyczne wywołania JS, nie są nietypowe — dyskusja dotycząca niejawnego użycia przepływu jest również istotna dla tych.

Aplikacje oparte na przekierowaniu zazwyczaj zabezpieczają swoje żądania za pośrednictwem plików cookie, jednak takie podejście nie działa prawidłowo w przypadku aplikacji JavaScript. Pliki cookie działają tylko względem domeny, dla której zostały wygenerowane, podczas gdy wywołania JavaScript mogą być kierowane do innych domen. W rzeczywistości często będzie to miało miejsce: należy wziąć pod uwagę aplikacje wywołujące Microsoft Graph interfejs API, interfejs API pakietu Office, interfejs API platformy Azure — wszystkie znajdujące się poza domeną, w której jest obsługiwana aplikacja. Rosnąca tendencja dla aplikacji języka JavaScript nie ma żadnego zaplecza, co polega na 100% od interfejsów API sieci Web innych firm w celu zaimplementowania funkcji firmy.

Obecnie preferowaną metodą ochrony wywołań interfejsu API sieci Web jest użycie metody tokena okaziciela OAuth2, gdzie każde wywołanie towarzyszy token dostępu OAuth2. Internetowy interfejs API bada token dostępu przychodzącego i, jeśli znajdzie w nim wymagane zakresy, udziela dostępu do wymaganej operacji. Niejawny przepływ zapewnia wygodny mechanizm dla aplikacji języka JavaScript do uzyskiwania tokenów dostępu dla internetowego interfejsu API, oferując wiele korzyści w odniesieniu do plików cookie:

* Tokeny mogą być niezawodnie uzyskiwane bez konieczności wykonywania wywołań między źródłami — obowiązkowa Rejestracja identyfikatora URI przekierowania, do którego tokeny zwracają gwarancje, że tokeny nie są umieszczane
* Aplikacje JavaScript mogą uzyskać dowolną liczbę tokenów dostępu dla tylu interfejsów API sieci Web, które są docelowe — bez ograniczeń dla domen
* Funkcje HTML5, takie jak sesja lub magazyn lokalny, zapewniają pełną kontrolę nad buforowaniem tokenów i zarządzaniem okresem istnienia, natomiast zarządzanie plikami cookie jest nieprzezroczyste dla aplikacji
* Tokeny dostępu nie są narażone na ataki podrabiania żądań między lokacjami (CSRF)

Niejawny przepływ wydawania nie wydaje tokenów odświeżania, głównie ze względów bezpieczeństwa. Token odświeżania nie jest tak wąski, jak tokeny dostępu, umożliwiając znacznie większą moc, co oznacza znacznie więcej uszkodzeń w przypadku, gdy jest on wycieka. W niejawnym przepływie tokeny są dostarczane w adresie URL, dlatego ryzyko przechwycenia jest większe niż w przypadku przyznania kodu autoryzacji.

Jednak aplikacja JavaScript ma inny mechanizm do usuwania tokenów dostępu bez wielokrotnego monitowania użytkownika o poświadczenia. Aplikacja może używać ukrytego elementu IFRAME do wykonywania nowych żądań tokenów względem punktu końcowego autoryzacji usługi Azure AD: o ile przeglądarka nadal ma aktywną sesję (odczyt: zawiera plik cookie sesji) z domeną usługi Azure AD, żądanie uwierzytelnienia może zostać wykonane pomyślnie bez konieczności interakcji z użytkownikiem.

Ten model przyznaje aplikacji JavaScript możliwość niezależnego odnawiania tokenów dostępu, a nawet pozyskiwania nowych dla nowego interfejsu API (pod warunkiem, że użytkownik wcześniej wyraził zgodę na ich). Pozwala to uniknąć dodawania, utrzymywania i ochrony artefaktu o wysokiej wartości, takiego jak token odświeżania. Artefakt, który sprawia, że możliwe jest odnowienie dyskretne, plik cookie sesji usługi Azure AD jest zarządzany poza aplikacją. Inną zaletą tego podejścia jest to, że użytkownik może wylogować się z usługi Azure AD przy użyciu dowolnych aplikacji zalogowanych do usługi Azure AD uruchomionych na dowolnej z kart przeglądarki. Spowoduje to usunięcie pliku cookie sesji usługi Azure AD, a aplikacja JavaScript automatycznie utraci możliwość odnowienia tokenów dla zalogowanego użytkownika.

## <a name="is-the-implicit-grant-suitable-for-my-app"></a>Czy niejawne przyznanie jest odpowiednie dla mojej aplikacji?

Niejawne przyznanie przedstawia większe ryzyko niż inne dotacje, a obszary, do których należy zwrócić uwagę, są dobrze udokumentowane (na przykład [niewłaściwe użycie tokenu dostępu do personifikacji właściciela zasobu w przepływie niejawnym][OAuth2-Spec-Implicit-Misuse] oraz [Model zagrożenia OAuth 2,0 i zagadnienia dotyczące zabezpieczeń][OAuth2-Threat-Model-And-Security-Implications]). Jednak profil wyższego ryzyka jest w dużym stopniu spowodowany tym, że ma to na celu umożliwienie aplikacjom wykonującym kod aktywny, obsługiwanym przez zasób zdalny w przeglądarce. Jeśli planujesz architekturę SPA, nie ma składników zaplecza ani nie ma potrzeby wywoływania internetowego interfejsu API za pośrednictwem języka JavaScript, zaleca się użycie niejawnego przepływu do pozyskiwania tokenów.

Jeśli aplikacja jest klientem macierzystym, niejawny przepływ nie jest doskonałym rozwiązaniem. Brak pliku cookie sesji usługi Azure AD w kontekście natywnego klienta pozbawia aplikację od środków w celu utrzymania długotrwałych sesji. Oznacza to, że aplikacja będzie kilkukrotnie monitować użytkownika o uzyskanie tokenów dostępu dla nowych zasobów.

Jeśli tworzysz aplikację sieci Web, która zawiera zaplecze i zużywa interfejs API z kodu wewnętrznej bazy danych, niejawny przepływ nie jest również dobrym dopasowaniem. Inne dotacje zapewniają znacznie większą moc. Na przykład przyznanie poświadczeń klienta OAuth2 umożliwia uzyskanie tokenów, które odzwierciedlają uprawnienia przypisane do samej aplikacji, w przeciwieństwie do delegowania użytkowników. Oznacza to, że klient ma możliwość utrzymania dostępu programowego do zasobów nawet wtedy, gdy użytkownik nie jest aktywnie zaangażowany w sesję i tak dalej. Nie tylko, ale takie dotacje dają wyższe gwarancje bezpieczeństwa. Na przykład tokeny dostępu nigdy nie są przesyłane przez przeglądarkę użytkownika, nie są one ryzykowne w historii przeglądarki i tak dalej. Aplikacja kliencka może także przeprowadzić silne uwierzytelnianie podczas żądania tokenu.

## <a name="next-steps"></a>Następne kroki

* Zobacz [Jak zintegrować aplikację z usługą Azure AD][ACOM-How-To-Integrate] , aby uzyskać dodatkową głębokość procesu integracji aplikacji.

<!--Image references-->

<!--Reference style links in use-->
[ACOM-How-And-Why-Apps-Added-To-AAD]: active-directory-how-applications-are-added.md
[ACOM-How-To-Integrate]: ../develop/active-directory-how-to-integrate.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json
[OAuth2-Spec-Implicit-Misuse]: https://tools.ietf.org/html/rfc6749#section-10.16
[OAuth2-Threat-Model-And-Security-Implications]: https://tools.ietf.org/html/rfc6819
