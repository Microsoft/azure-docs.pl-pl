---
title: Odporność w ramach najlepszych rozwiązań dla deweloperów korzystających z Azure AD B2C | Microsoft Docs
description: Odporność na korzystanie z najlepszych rozwiązań dla deweloperów w zakresie zarządzania tożsamościami i dostępem klientów przy użyciu Azure AD B2C
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecde474abf3c814b7c3afa4ae18d044868785cf5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "95920062"
---
# <a name="resilience-through-developer-best-practices"></a>Odporność na najlepsze rozwiązania dla deweloperów

W tym artykule udostępniamy pewne informacje, które opierają się na naszych doświadczeniach związanych z pracą z dużymi klientami. Te zalecenia można wziąć pod uwagę podczas projektowania i wdrażania usług.

![Obraz przedstawia składniki środowiska deweloperskiego](media/resilience-b2c-developer-best-practices/developer-best-practices-architecture.png)

## <a name="use-the-microsoft-authentication-library-msal"></a>Korzystanie z biblioteki uwierzytelniania firmy Microsoft (MSAL)

[Biblioteka Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview) i [Biblioteka Microsoft Identity Web Authentication Library for ASP.NET](https://docs.microsoft.com/azure/active-directory/develop/reference-v2-libraries) upraszczają uzyskiwanie, zarządzanie, buforowanie i odświeżanie tokenów wymaganych przez aplikację. Te biblioteki są zoptymalizowane pod kątem obsługi tożsamości firmy Microsoft, w tym funkcji, które zwiększają odporność aplikacji.

Deweloperzy powinni przyjąć najnowsze wersje programu MSAL i być na bieżąco. Zobacz [, jak zwiększyć odporność uwierzytelniania i autoryzacji](resilience-app-development-overview.md) w aplikacjach. Tam, gdzie to możliwe, unikaj wdrażania własnego stosu uwierzytelniania i używaj dobrze ustanowionych bibliotek.

## <a name="optimize-directory-reads-and-writes"></a>Optymalizuj operacje odczytu i zapisu w katalogu

Usługa katalogowa Microsoft Azure AD B2C obsługuje miliardy uwierzytelnień dziennie. Jest on przeznaczony dla dużej liczby odczytów na sekundę. Zoptymalizuj zapisy, aby zminimalizować zależności i zwiększyć odporność.

### <a name="how-to-optimize-directory-reads-and-writes"></a>Optymalizowanie odczytów i zapisów w katalogu

- **Unikaj zapisywania funkcji zapisu w katalogu podczas logowania**: nigdy nie wykonuj zapisu przy logowaniu bez warunku wstępnego (klauzula IF) w zasadach niestandardowych. Jeden przypadek użycia, który wymaga zapisu podczas logowania, to [migracja haseł użytkowników w czasie just in Time](https://github.com/azure-ad-b2c/user-migration/tree/master/seamless-account-migration). Unikaj jakiegokolwiek scenariusza, który wymaga zapisu przy każdym logowaniu.

  - [Warunki wstępne](https://docs.microsoft.com/azure/active-directory-b2c/userjourneys) w podróży użytkownika będą wyglądać następująco:

  ``
  <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 
  <Value>requiresMigration</Value>
  ...
  < Precondition/>
  ``
  - Odporność na boty sterowane [przez integrację z systemem CAPTCHA](https://github.com/azure-ad-b2c/samples/tree/master/policies/captcha-integration).

  - Użyj [przykładu testowania obciążenia](https://docs.microsoft.com/azure/active-directory-b2c/best-practices#testing) , aby zasymulować logowanie i zalogować się. 

- **Opis ograniczania przepustowości**: katalog implementuje zasady ograniczania poziomu aplikacji i dzierżawy. Istnieją dalsze limity szybkości operacji read/GET, Write/POST, Update/PUT i usuwania/usuwania, a każda operacja ma różne limity.

  - Zapis w momencie logowania będzie należeć do wpisu dla nowych użytkowników lub zostanie umieszczony dla istniejących użytkowników.

  - Zasada niestandardowa, która tworzy lub aktualizuje użytkownika przy każdym logowaniu, może potencjalnie spowodować ograniczenie liczby trafień na poziomie aplikacji lub. Te same limity są stosowane podczas aktualizacji obiektów katalogu za pośrednictwem usługi Azure AD lub Microsoft Graph. Podobnie należy przeanalizować odczyty, aby zachować liczbę odczytów przy każdym logowaniu do minimum.

  - Oszacuj szczytowe obciążenie, aby przewidzieć szybkość zapisu w katalogu i uniknąć ograniczania. Szacunkowe oszacowanie ruchu powinno obejmować oszacowania dotyczące akcji, takich jak rejestrowanie, logowanie i uwierzytelnianie wieloskładnikowe (MFA). Należy przetestować zarówno system Azure AD B2C, jak i aplikację na potrzeby szczytowego ruchu sieciowego. Możliwe jest, że Azure AD B2C może obsłużyć obciążenie bez ograniczania, gdy aplikacje podrzędne lub usługi nie będą używane.

  - Zrozumienie i planowanie osi czasu migracji. Planując migrację użytkowników do Azure AD B2C przy użyciu Microsoft Graph, należy wziąć pod uwagę limity aplikacji i dzierżawy, aby obliczyć czas wymagany do przeprowadzenia migracji użytkowników. W przypadku dzielenia zadania tworzenia użytkownika lub skryptu przy użyciu dwóch aplikacji można użyć limitu dla aplikacji. Nadal będzie trzeba pozostawać poniżej wartości progowej dla dzierżawy.

  - Zapoznaj się z efektami zadania migracji w innych aplikacjach. Weź pod uwagę ruch na żywo obsługiwany przez inne aplikacje zależne, aby upewnić się, że nie jest możliwe ograniczenie poziomu dzierżawy i zasobów dla aktywnej aplikacji. Aby uzyskać więcej informacji, zobacz [wskazówki dotyczące ograniczania Microsoft Graph](https://docs.microsoft.com/graph/throttling).
  
## <a name="extend-token-lifetimes"></a>Zwiększ okresy istnienia tokenów

W przypadku gdy usługa uwierzytelniania Azure AD B2C nie może zakończyć tworzenia nowych kont i logowania, w razie potrzeby nadal można zapewnić środki zaradcze dla użytkowników, którzy są zalogowani. Za pomocą opcji [Konfiguracja](https://docs.microsoft.com/azure/active-directory-b2c/configure-tokens)można zezwolić użytkownikom, którzy są już zalogowani, aby nadal korzystać z aplikacji bez żadnych odczuwanych zakłóceń do momentu wylogowania użytkownika z aplikacji lub przekroczenia limitu czasu [sesji](https://docs.microsoft.com/azure/active-directory-b2c/session-behavior) z powodu braku aktywności.

Twoje wymagania biznesowe i środowisko użytkownika końcowego będą dyktować częstotliwość odświeżania tokenu dla aplikacji sieci Web i jednostronicowych (aplikacji jednostronicowych).

### <a name="how-to-extend-token-lifetimes"></a>Jak rozciągnąć okresy istnienia tokenów

- **Aplikacje sieci Web**: w przypadku aplikacji sieci Web, w których token uwierzytelniania jest sprawdzany na początku logowania, aplikacja zależy od pliku cookie sesji, aby kontynuować przedłużenie okresu ważności sesji.

  - Umożliwienie użytkownikom zalogowania się przez zaimplementowanie czasów sesji kroczącej, które będą nadal odnawiać sesje w oparciu o aktywność użytkownika. W przypadku Nieprzerwania wystawiania tokenów długoterminowych te czasy sesji można dodatkowo zwiększyć jako konfigurację jednorazowej w aplikacji. Okres istnienia sesji może być maksymalny.

- **Aplikacji jednostronicowych**: Spa może zależeć od tokenów dostępu, aby wykonywać wywołania do interfejsów API. SPA tradycyjnie używa niejawnego przepływu, który nie powoduje tokenu odświeżania. SPA może używać ukrytego elementu IFRAME do wykonywania nowych żądań tokenów względem punktu końcowego autoryzacji, jeśli przeglądarka nadal ma aktywną sesję z Azure AD B2C. W przypadku aplikacji jednostronicowych dostępnych jest kilka opcji umożliwiających użytkownikowi kontynuowanie korzystania z aplikacji.

  - Przedłuż okres ważności tokenu dostępu, aby spełnić wymagania biznesowe.

  - Skompiluj aplikację, aby użyć bramy interfejsu API jako serwera proxy uwierzytelniania. W tej konfiguracji SPA ładuje się bez żadnego uwierzytelniania i wywołania interfejsu API są wykonywane do bramy interfejsu API. Brama interfejsu API wysyła użytkownika za pośrednictwem procesu logowania przy użyciu [przyznawania kodu autoryzacji](https://oauth.net/2/grant-types/authorization-code/) na podstawie zasad i uwierzytelnia użytkownika. Następnie sesja uwierzytelniania między bramą interfejsu API i klientem jest utrzymywana przy użyciu pliku cookie uwierzytelniania. Interfejsy API są poddawane obsłużeniu z bramy interfejsu API przy użyciu tokenu uzyskanego przez bramę interfejsu API lub innej metody bezpośredniego uwierzytelniania, takiej jak certyfikaty, poświadczenia klienta lub klucze interfejsu API.

  - [Przeprowadź migrację spa z niejawnego przydzielenia](https://developer.microsoft.com/identity/blogs/msal-js-2-0-supports-authorization-code-flow-is-now-generally-available/) do [kodu autoryzacji przepływ](https://docs.microsoft.com/azure/active-directory-b2c/implicit-flow-single-page-application) z użyciem klucza testowego dla wymiany kodu (PKCE) i obsługi udostępniania zasobów między źródłami (CORS). Przeprowadź migrację aplikacji z MSAL.js 1. x do MSAL.js 2. x, aby zrealizować odporność aplikacji sieci Web.

  - W przypadku aplikacji mobilnych zaleca się rozbudowa okresów istnienia tokenu odświeżania i dostępu.

- **Zaplecze lub aplikacje mikrousług**: ponieważ aplikacje zaplecza (demon) nie są interaktywne i nie znajdują się w kontekście użytkownika, znacznie zmniejsza się relacja kradzieży tokenu. Zalecenie polega na zrównoważeniu równowagi między zabezpieczeniami a okresem istnienia i ustawieniu długiego okresu istnienia tokenu.

## <a name="configure-single-sign-on"></a>Konfigurowanie logowania jednokrotnego

Logowanie jednokrotne [(SSO)](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)umożliwia użytkownikom logowanie jednokrotne przy użyciu jednego konta i uzyskanie dostępu do wielu aplikacji. Aplikacja może być siecią Web, komórką lub aplikacją jednostronicową (SPA), niezależnie od nazwy platformy lub domeny. Gdy użytkownik po raz pierwszy zaloguje się do aplikacji, Azure AD B2C utrzymuje [sesję opartą na plikach cookie](https://docs.microsoft.com/azure/active-directory-b2c/session-overview).

Po kolejnych żądaniach uwierzytelniania Azure AD B2C odczytuje i sprawdza poprawność sesji opartej na plikach cookie i wystawia token dostępu bez monitowania użytkownika o ponowne zalogowanie. Jeśli logowanie jednokrotne jest skonfigurowane z ograniczonym zakresem zasad lub aplikacji, dalsze dostęp do innych zasad i aplikacji będzie wymagało ponownego uwierzytelnienia.

### <a name="how-to-configure-sso"></a>Jak skonfigurować Logowanie jednokrotne

[Skonfiguruj Logowanie jednokrotne](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso-quick-start) do całego dzierżawy (domyślnie), aby umożliwić wielu aplikacjom i przepływom użytkowników w dzierżawie współużytkowanie tej samej sesji użytkownika. Konfiguracja obejmująca wiele dzierżawców zapewnia największą odporność na nowe uwierzytelnianie.  

## <a name="safe-deployment-practices"></a>Praktyki bezpiecznego wdrażania

Najczęstszymi zakłóceniami usługi są zmiany kodu i konfiguracji. Wdrażanie procesów ciągłej integracji i ciągłego dostarczania (CICD) i narzędzi ułatwia szybkie wdrożenie na dużą skalę i zmniejsza liczbę błędów ludzkich podczas testowania i wdrażania w środowisku produkcyjnym. Przyjmij CICD na zmniejszenie, wydajność i spójność błędów. [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/azure/cicd-data-overview) jest przykładem CICD.

## <a name="web-application-firewall"></a>Zapora aplikacji internetowej

Ochrona aplikacji przed znanymi lukami w zabezpieczeniach, takimi jak ataki rozproszone typu "odmowa usługi" (DDoS), iniekcje SQL, skrypty między lokacjami, zdalne wykonywanie kodu i wiele innych, zgodnie z opisem w [OWASP 10 pierwszych](https://owasp.org/www-project-top-ten/). Wdrażanie zapory aplikacji sieci Web (WAF) może być obroną przed typowymi programami wykorzystującymi luki w zabezpieczeniach.

- Korzystanie z usługi Azure [WAF](https://docs.microsoft.com/azure/web-application-firewall/overview), która zapewnia scentralizowaną ochronę przed atakami.

- Użyj WAF z usługą Azure AD [Identity Protection i dostępem warunkowym, aby zapewnić ochronę wielowarstwową](https://docs.microsoft.com/azure/active-directory-b2c/conditional-access-identity-protection-overview) przy użyciu Azure AD B2C.  

## <a name="secrets-rotation"></a>Rotacja wpisów tajnych

Azure AD B2C używa wpisów tajnych dla aplikacji, interfejsów API, zasad i szyfrowania. Klucze tajne bezpieczne uwierzytelnianie, interakcje zewnętrzne i magazyn. Narodowy Instytut standardów i technologii (NIST) wywołuje przedział czasu, w którym określony klucz jest autoryzowany do użycia przez uprawnione jednostki a cryptoperiod. Wybierz właściwą długość [cryptoperiod](https://csrc.nist.gov/publications/detail/sp/800-57-part-1/rev-5/final) , aby sprostać potrzebom biznesowym. Deweloperzy muszą ręcznie ustawić okres ważności i obrócić wpisy tajne z wyprzedzeniem.

### <a name="how-to-implement-secret-rotation"></a>Jak zaimplementować rotację kluczy tajnych

- Użyj [tożsamości zarządzanych](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) dla obsługiwanych zasobów do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie w usłudze Azure AD. W przypadku korzystania z tożsamości zarządzanych można automatycznie zarządzać zasobami, w tym rotacją poświadczeń.

- Zrób spis wszystkich [kluczy i certyfikatów skonfigurowanych](https://docs.microsoft.com/azure/active-directory-b2c/policy-keys-overview) w Azure AD B2C. Ta lista może zawierać klucze używane w zasadach niestandardowych, [interfejsy API](https://docs.microsoft.com/azure/active-directory-b2c/secure-rest-api), token identyfikatora podpisywania i certyfikaty dla protokołu SAML.

- Korzystając z CICD, Obróć wpisy tajne, które wkrótce wygasną w ciągu dwóch miesięcy od przewidywanej pory szczytu. Zalecana maksymalna cryptoperiod kluczy prywatnych skojarzonych z certyfikatem wynosi jeden rok.

- Proaktywne monitorowanie i obracanie poświadczeń dostępu do interfejsu API, takich jak hasła i certyfikaty.

## <a name="test-rest-apis"></a>Testowanie interfejsów API REST

W kontekście odporności testowanie interfejsów API REST musi obejmować weryfikację — kodów HTTP, ładunku odpowiedzi, nagłówków i wydajności. Testowanie nie powinno obejmować tylko udanych testów ścieżek, ale również sprawdzać, czy interfejs API obsługuje scenariusze problemów.

### <a name="how-to-test-apis"></a>Testowanie interfejsów API

Zalecamy, aby plan testu obejmował [kompleksowe testy interfejsu API](https://docs.microsoft.com/azure/active-directory-b2c/best-practices#testing). Jeśli planujesz nadchodzące przeskoki ze względu na promocję lub ruch świąteczny, musisz poprawić testowanie obciążenia przy użyciu nowych oszacowań. Przeprowadzaj testy obciążenia interfejsów API i Content Delivery Network (CDN) w środowisku deweloperskim, a nie w produkcji.

## <a name="next-steps"></a>Następne kroki

- [Zasoby dotyczące odporności dla deweloperów Azure AD B2C](resilience-b2c.md)
  - [Odporne środowisko użytkownika końcowego](resilient-end-user-experience.md)
  - [Odporne interfejsy z procesami zewnętrznymi](resilient-external-processes.md)
  - [Odporność dzięki monitorowaniu i analizie](resilience-with-monitoring-alerting.md)
- [Tworzenie odporności w infrastrukturze uwierzytelniania](resilience-in-infrastructure.md)
- [Zwiększanie odporności uwierzytelniania i autoryzacji w aplikacjach](resilience-app-development-overview.md)
