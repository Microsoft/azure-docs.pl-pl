---
title: Słownik deweloperów platformy tożsamości firmy Microsoft | Azure
description: Lista terminów dla często używanych pojęć i funkcji dla deweloperów platformy tożsamości firmy Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jmprieur, saeeda, jesakowi, nacanuma
ms.openlocfilehash: 930341b60f785c2c618be4ee235225519a08aaa6
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530054"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>Słownik deweloperów platformy tożsamości firmy Microsoft

Ten artykuł zawiera definicje niektórych podstawowych pojęć i terminologii dla deweloperów, które są przydatne podczas nauki tworzenia aplikacji przy użyciu platformy tożsamości firmy Microsoft.

## <a name="access-token"></a>token dostępu

Typ tokenu [zabezpieczającego](#security-token) wystawionego przez serwer [](#client-application) [autoryzacji](#authorization-server)i używanego przez aplikację kliency w celu uzyskania dostępu do [chronionego serwera zasobów.](#resource-server) Zazwyczaj w postaci JSON Web Token [(JWT)][JWT]token zawiera autoryzację udzieloną klientowi przez właściciela zasobu [dla](#resource-owner)żądanego poziomu dostępu. Token zawiera wszystkie odpowiednie [oświadczenia dotyczące](#claim) podmiotu, umożliwiając aplikacji klienckiej używanie go jako formy poświadczeń podczas uzyskiwania dostępu do danego zasobu. Eliminuje to również konieczność uwidoczniania poświadczeń klientowi przez właściciela zasobu.

Tokeny dostępu są ważne tylko przez krótki czas i nie można ich odwołać. Serwer autoryzacji może również wystawiać [token odświeżania](#refresh-token) podczas wydawania tokenu dostępu. Tokeny odświeżania są zwykle dostarczane tylko poufnym aplikacjom klienckim.

Tokeny dostępu są czasami określane jako "Użytkownik+aplikacja" lub "Tylko aplikacja", w zależności od reprezentowanych poświadczeń. Na przykład gdy aplikacja kliency używa:

* [Udzielenie autoryzacji "Kod autoryzacji"](#authorization-grant), użytkownik końcowy najpierw uwierzytelnia się jako właściciel zasobu, delegowając klientowi autoryzację w celu uzyskania dostępu do zasobu. Klient uwierzytelnia się później podczas uzyskiwania tokenu dostępu. Token czasami można nazwać bardziej szczegółowo tokenem "Użytkownik+aplikacja", ponieważ reprezentuje on zarówno użytkownika, który autoryzował aplikację kliencyjną, jak i aplikację.
* [Przyznawanie](#authorization-grant)autoryzacji "Poświadczenia klienta" , klient zapewnia jedyne uwierzytelnianie, działające bez uwierzytelniania/autoryzacji właściciela zasobu, więc token może czasami być nazywany tokenem "Tylko aplikacja".

Aby uzyskać więcej informacji, zobacz [Microsoft identity platform Token Reference (Informacje][AAD-Tokens-Claims] o tokenach platformy tożsamości firmy Microsoft).

## <a name="application-id-client-id"></a>identyfikator aplikacji (identyfikator klienta)

Unikatowy identyfikator usługi Azure AD jest związany z rejestracją aplikacji, która identyfikuje określoną aplikację i skojarzone konfiguracje. Ten identyfikator aplikacji[(identyfikator klienta)](https://tools.ietf.org/html/rfc6749#page-15)jest używany podczas wykonywania żądań uwierzytelniania i jest dostarczany do bibliotek uwierzytelniania w czasie projektowania. Identyfikator aplikacji (identyfikator klienta) nie jest kluczem tajnym.

## <a name="application-manifest"></a>manifest aplikacji

Funkcja dostarczana przez Azure Portal [,][AZURE-portal]która tworzy reprezentację JSON konfiguracji tożsamości aplikacji, używaną jako mechanizm aktualizowania skojarzonych jednostek [Application][Graph-App-Resource] i [ServicePrincipal.][Graph-Sp-Resource] Aby [uzyskać więcej informacji, zobacz Understanding the Azure Active Directory application manifest (Opis manifestu][AAD-App-Manifest] aplikacji mobilnej).

## <a name="application-object"></a>obiekt aplikacji

Podczas rejestrowania/aktualizowania aplikacji w usłudze [Azure Portal][AZURE-portal]portal tworzy/aktualizuje zarówno obiekt aplikacji, jak i odpowiedni obiekt jednostki usługi [dla](#service-principal-object) tej dzierżawy. Obiekt aplikacji  definiuje globalnie konfigurację tożsamości aplikacji (we wszystkich dzierżawach, do których ma dostęp), udostępniając  szablon, z którego pochodzą odpowiednie obiekty jednostki usługi do użytku lokalnego w czasie działania (w określonej dzierżawie).

Aby uzyskać więcej informacji, zobacz [Application and Service Principal Objects (Obiekty aplikacji i jednostki usługi).][AAD-App-SP-Objects]

## <a name="application-registration"></a>rejestracja aplikacji

Aby umożliwić integrację aplikacji z usługą i delegowanie funkcji zarządzania tożsamościami i dostępem do usługi Azure AD, należy ją zarejestrować w dzierżawie [usługi](#tenant)Azure AD. Podczas rejestrowania aplikacji w usłudze Azure AD udostępniasz konfigurację tożsamości dla aplikacji, umożliwiając jej integrację z usługą Azure AD i korzystanie z funkcji, takich jak:

* Niezawodne zarządzanie usługą Single Sign-On przy użyciu usługi Azure AD Identity Management [i OpenID Connect][OpenIDConnect] implementacji protokołu
* Dostęp brokera do [chronionych zasobów przez](#resource-server) [aplikacje klienckie](#client-application)za pośrednictwem serwera autoryzacji OAuth 2.0 [](#authorization-server)
* [Platformę wyrażania](#consent) zgody na zarządzanie dostępem klientów do chronionych zasobów na podstawie autoryzacji właściciela zasobu.

Zobacz [Integrowanie aplikacji z Azure Active Directory,][AAD-Integrating-Apps] aby uzyskać więcej informacji.

## <a name="authentication"></a>uwierzytelnianie

Działanie wymuszące na stronie weryfikację poświadczeń, która stanowi podstawę utworzenia podmiotu zabezpieczeń, który będzie używany do zarządzania tożsamościami i kontrolą dostępu. Na przykład [podczas udzielania autoryzacji OAuth2](#authorization-grant) uwierzytelnianie strony [](#resource-owner) wypełnia rolę właściciela zasobu lub aplikacji klienckiej [,](#client-application)w zależności od użytego udzielenia.

## <a name="authorization"></a>autoryzacja

Udzielenie uwierzytelnioneowi podmiotu zabezpieczeń uprawnień do czegoś. Istnieją dwa główne przypadki użycia w modelu programowania w usłudze Azure AD:

* Podczas [przepływu przyznawania autoryzacji OAuth2:](#authorization-grant) gdy właściciel zasobu udziela autoryzacji aplikacji klienckiej [,](#client-application)co umożliwia klientowi dostęp do zasobów właściciela zasobu. [](#resource-owner)
* Podczas dostępu do zasobów przez klienta: zgodnie z [](#claim) wdrożeniem serwera zasobów [przy](#resource-server)użyciu wartości uwierzytelniania obecnych w [tokenie](#access-token) dostępu do podejmowania decyzji dotyczących kontroli dostępu na ich podstawie.

## <a name="authorization-code"></a>kod autoryzacji

Krótkotrwały "token" dostarczony [](#client-application) aplikacji klienckiej przez punkt końcowy autoryzacji [w](#authorization-endpoint)ramach przepływu "kod autoryzacji" jeden z czterech autoryzacji OAuth2 udziela [.](#authorization-grant) Kod jest zwracany do aplikacji klienckiej w odpowiedzi na uwierzytelnianie właściciela [zasobu,](#resource-owner)co wskazuje, że właściciel zasobu ma delegowaną autoryzację dostępu do żądanych zasobów. W ramach przepływu kod jest później zrealizowany na token [dostępu](#access-token).

## <a name="authorization-endpoint"></a>punkt końcowy autoryzacji

Jeden z punktów końcowych implementowany przez serwer autoryzacji [,](#authorization-server)używany do [](#authorization-grant) interakcji z właścicielem zasobu w celu udzielenia autoryzacji podczas przepływu autoryzacji OAuth2. [](#resource-owner) W zależności od używanego przepływu udzielania autoryzacji rzeczywiste przyznanie może się różnić, w tym kod [autoryzacji](#authorization-code) lub [token zabezpieczający](#security-token).

Aby uzyskać więcej informacji, [][OAuth2-AuthZ-Grant-Types] zobacz sekcje typy przyznawania autoryzacji i punkt końcowy autoryzacji specyfikacji OAuth2 oraz Specyfikacja [OpenIDConnect.][OpenIDConnect-AuthZ-Endpoint] [][OAuth2-AuthZ-Endpoint]

## <a name="authorization-grant"></a>autoryzacja

Poświadczenie [reprezentujące autoryzację właściciela](#resource-owner) zasobu do [uzyskiwania](#authorization) dostępu do jego chronionych zasobów, udzielone aplikacji [klienckiej](#client-application). Aplikacja kliency może użyć jednego z czterech typów udzielania zdefiniowanych przez platformę [autoryzacji OAuth2][OAuth2-AuthZ-Grant-Types] w celu uzyskania udzielenia, w zależności od typu/wymagań klienta: "autoryzacja udzielenia kodu", "przyznawanie poświadczeń klienta", "przyznawanie niejawne" i "przyznawanie poświadczeń hasła właściciela zasobu". Poświadczenia zwrócone do klienta to [token](#access-token)dostępu lub kod autoryzacji [(wymieniany](#authorization-code) później na token dostępu) w zależności od typu użytego udzielenia autoryzacji.

## <a name="authorization-server"></a>serwer autoryzacji

Zgodnie z definicją w ramach struktury autoryzacji [OAuth2][OAuth2-Role-Def]serwer [](#client-application) odpowiedzialny za wystawianie tokenów dostępu klientowi po pomyślnym uwierzytelnieniu właściciela zasobu i uzyskaniu jego autoryzacji. [](#resource-owner) Aplikacja [kliency](#client-application) współdziała z serwerem autoryzacji w czasie wykonywania za pośrednictwem punktów końcowych autoryzacji i [](#authorization-endpoint) [tokenu](#token-endpoint) zgodnie z uprawnieniami autoryzacji zdefiniowanymi przez standard OAuth2. [](#authorization-grant)

W przypadku integracji aplikacji platformy tożsamości firmy Microsoft platforma tożsamości firmy Microsoft implementuje rolę serwera autoryzacji dla aplikacji usługi Azure AD i interfejsów API usługi firmy Microsoft, na przykład w Microsoft Graph [API.][Microsoft-Graph]

## <a name="claim"></a>Roszczenia

Token [zabezpieczający zawiera](#security-token) oświadczenia, które zapewniają potwierdzenia dotyczące [](#client-application) jednej jednostki (na przykład aplikacji klienckiej lub właściciela zasobu [)](#resource-owner)innej jednostce (takiej jak serwer [zasobów).](#resource-server) Oświadczenia to pary nazwa/wartość przekazujące fakty dotyczące podmiotu tokenu (na przykład podmiot zabezpieczeń uwierzytelniony przez [serwer autoryzacji).](#authorization-server) Oświadczenia obecne w danym tokenie są zależne od kilku zmiennych, w tym typu tokenu, typu poświadczeń używanych do uwierzytelniania podmiotu, konfiguracji aplikacji itp.

Aby uzyskać więcej informacji, zobacz [microsoft identity platform token reference (Informacje][AAD-Tokens-Claims] o tokenach platformy tożsamości firmy Microsoft).

## <a name="client-application"></a>aplikacja kliency

Zgodnie z definicją w [ramach struktury autoryzacji OAuth2][OAuth2-Role-Def]aplikacja, która wykonuje żądania zasobów chronionych w imieniu [właściciela zasobu.](#resource-owner) Termin "klient" nie oznacza żadnych konkretnych cech implementacji sprzętu (na przykład tego, czy aplikacja jest wykonywana na serwerze, komputerze stacjonarnym czy innych urządzeniach).

Aplikacja [kliency](#authorization) żąda autoryzacji od właściciela zasobu w celu uczestnictwa w przepływie udzielania autoryzacji [OAuth2](#authorization-grant) i może uzyskać dostęp do interfejsów API/danych w imieniu właściciela zasobu. W ramach struktury autoryzacji OAuth2 zdefiniowano dwa typy klientów [—][OAuth2-Client-Types]"poufne" i "publiczne" — na podstawie możliwości zachowania poufności poświadczeń klienta. Aplikacje mogą implementować klienta internetowego [(poufnego),](#web-client) który działa na serwerze internetowym, natywnego klienta [(publicznego)](#native-client) zainstalowanego na urządzeniu lub klienta opartego na agencie użytkownika [(publicznego),](#user-agent-based-client) który jest uruchamiany w przeglądarce urządzenia.

## <a name="consent"></a>Zgody

Proces właściciela zasobu, [który udziela](#resource-owner) aplikacji klienckiej autoryzacji [w](#client-application)celu uzyskania dostępu do chronionych zasobów w ramach określonych uprawnień [w](#permissions)imieniu właściciela zasobu. W zależności od uprawnień żądanych przez klienta administrator lub użytkownik zostanie poproszony o zgodę, aby zezwolić na dostęp do danych organizacji/poszczególnych użytkowników. Należy pamiętać, że w scenariuszu z [](#service-principal-object) wieloma [dzierżawami](#multi-tenant-application) nazwa główna usługi aplikacji jest również rejestrowana w dzierżawie wyrażania zgody użytkownika.

Aby uzyskać [więcej informacji,](consent-framework.md) zobacz platformę wyrażania zgody.

## <a name="id-token"></a>Token identyfikatora

Token [OpenID Connect][OpenIDConnect-ID-Token] [dostarczony](#security-token) przez punkt [](#authorization-server) końcowy autoryzacji serwera autoryzacji [,](#authorization-endpoint)który zawiera oświadczenia dotyczące uwierzytelniania właściciela zasobu użytkownika [końcowego.](#resource-owner) [](#claim) Podobnie jak token dostępu, tokeny identyfikatorów są również reprezentowane jako cyfrowo podpisane [JSON Web Token (JWT).][JWT] Jednak w przeciwieństwie do tokenu dostępu oświadczenia tokenu identyfikatora nie są używane do celów związanych z dostępem do zasobów, a w szczególności z kontrolą dostępu.

Aby uzyskać więcej [informacji, zobacz microsoft identity platform token reference (Informacje][AAD-Tokens-Claims] o tokenach platformy tożsamości firmy Microsoft).

## <a name="microsoft-identity-platform"></a>Platforma tożsamości firmy Microsoft

Platforma tożsamości firmy Microsoft jest ewolucją platformy deweloperów Azure Active Directory (Azure AD). Dzięki niej deweloperzy mogą tworzyć aplikacje, które logują użytkowników przy użyciu wszystkich tożsamości firmy Microsoft i uzyskują tokeny do wywoływania programu Microsoft Graph, innych interfejsów API firmy Microsoft lub interfejsów API opracowanych przez deweloperów. Jest to w pełni polecana platforma, która składa się z usługi uwierzytelniania, bibliotek, rejestracji i konfiguracji aplikacji, pełnej dokumentacji dla deweloperów, przykładów kodu i innej zawartości dla deweloperów. Platforma tożsamości firmy Microsoft obsługuje standardowe protokoły branżowe, takie jak OAuth 2.0 i OpenID Connect.

## <a name="multi-tenant-application"></a>aplikacja wielodostępna

Klasa aplikacji, która umożliwia [](#consent) logowanie i wyrażanie zgody przez użytkowników aprowowanych w dowolnej dzierżawie usługi Azure [AD,](#tenant)w tym dzierżawach innych niż ta, w której klient jest zarejestrowany. [Natywne](#native-client) aplikacje klienckie są domyślnie [](#web-client) wielodostępne, natomiast aplikacje klienta internetowego i zasobu [internetowego/interfejsu API](#resource-server) mają możliwość wyboru między pojedynczą lub wielodostępną dzierżawą. Z kolei aplikacja internetowa zarejestrowana jako pojedyncza dzierżawa zezwala na logowanie tylko z kont użytkowników aprowowanych w tej samej dzierżawie co ta, w której zarejestrowano aplikację.

Aby uzyskać więcej informacji, zobacz How [to sign in any Azure AD user using the multi-tenant application pattern][AAD-Multi-Tenant-Overview] (Jak zalogować dowolnego użytkownika usługi Azure AD przy użyciu wzorca aplikacji wielodostępnych).

## <a name="native-client"></a>klient natywny

Typ aplikacji [klienckiej zainstalowanej](#client-application) natywnie na urządzeniu. Ponieważ cały kod jest wykonywany na urządzeniu, jest traktowany jako klient "publiczny", ponieważ nie może przechowywać poświadczeń prywatnie/poufne. Aby uzyskać więcej informacji, zobacz Typy i profile klientów [OAuth2.][OAuth2-Client-Types]

## <a name="permissions"></a>uprawnienia

Aplikacja [kliency](#client-application) uzyskuje dostęp do [serwera zasobów,](#resource-server) deklarując żądania uprawnień. Dostępne są dwa typy:

* Uprawnienia "Delegowane", [](#scopes) które określają dostęp oparty na zakresie przy użyciu autoryzacji delegowaowej od zalogowanego właściciela [zasobu,](#resource-owner)są przedstawiane zasobowi w czasie wykonywania jako oświadczenia ["scp"](#claim) w [tokenie](#access-token)dostępu klienta.
* Uprawnienia "Aplikacja", [](#roles) które określają dostęp oparty na rolach przy użyciu poświadczeń/tożsamości aplikacji klienckiej, są prezentowane zasobowi w czasie wykonywania jako oświadczenia ["role"](#claim) w tokenie dostępu klienta.

Są one również dostępne podczas [procesu wyrażania](#consent) zgody, co daje administratorowi lub właścicielowi zasobu możliwość przyznania/odmowy dostępu klienta do zasobów w dzierżawie.

Żądania uprawnień są konfigurowane na stronie uprawnień interfejsu **API** dla aplikacji w administracyjnym programie [Azure Portal][AZURE-portal]przez wybranie odpowiednich pozycji "Uprawnienia delegowane" i "Uprawnienia aplikacji" (ta ostatnia wymaga członkostwa w roli administratora globalnego). Ponieważ klient [publiczny nie](#client-application) może bezpiecznie utrzymywać poświadczeń, może żądać tylko delegowanych uprawnień, [podczas](#client-application) gdy klient poufny może żądać zarówno uprawnień delegowanych, jak i uprawnień aplikacji. Obiekt aplikacji klienta przechowuje [zadeklarowane](#application-object) uprawnienia we właściwości [requiredResourceAccess][Graph-App-Resource].

## <a name="refresh-token"></a>token odświeżania

Typ [tokenu zabezpieczającego](#security-token) wystawionego przez serwer [](#client-application) autoryzacji [i](#authorization-server)używanego przez aplikację kliency w celu żądania nowego [tokenu](#access-token) dostępu przed wygaśnięciem tokenu dostępu. Zwykle w postaci JSON Web Token [(JWT).][JWT]

W przeciwieństwie do tokenów dostępu tokeny odświeżania można odwołać. Jeśli aplikacja kliency spróbuje zażądać nowego tokenu dostępu przy użyciu tokenu odświeżania, który został odwołany, serwer [](#resource-server) autoryzacji odmówi żądania, a aplikacja kliencza nie będzie już mieć uprawnień dostępu do serwera zasobów w imieniu [właściciela zasobu.](#resource-owner)

## <a name="resource-owner"></a>właściciel zasobu

Zgodnie z definicją [w ramach struktury autoryzacji OAuth2][OAuth2-Role-Def]jednostka może udzielać dostępu do chronionego zasobu. Gdy właścicielem zasobu jest osoba, jest on określany jako użytkownik końcowy. Na przykład gdy aplikacja [kliency](#client-application) chce uzyskać dostęp do skrzynki pocztowej użytkownika za pośrednictwem interfejsu API Microsoft Graph , wymaga [to][Microsoft-Graph]uprawnień właściciela zasobu skrzynki pocztowej.

## <a name="resource-server"></a>serwer zasobów

Zgodnie z definicją w [ramach struktury autoryzacji OAuth2][OAuth2-Role-Def], serwer hostjący chronione [](#client-application) zasoby, który może akceptować żądania zasobów chronionych i odpowiadać na nie przez aplikacje klienckie, które prezentują [token dostępu.](#access-token) Nazywane również chronionym serwerem zasobów lub aplikacją zasobów.

Serwer zasobów uwidacznia interfejsy API i [](#scopes) wymusza dostęp do chronionych zasobów za pośrednictwem zakresów i ról [przy](#roles)użyciu struktury autoryzacji OAuth 2.0. Przykłady obejmują [interfejs API Microsoft Graph,][Microsoft-Graph] który zapewnia dostęp do danych dzierżawy usługi Azure AD, oraz interfejsy API Microsoft 365, które zapewniają dostęp do danych, takich jak poczta e-mail i kalendarz.

Podobnie jak w przypadku aplikacji klienckiej konfiguracja [](#application-registration) tożsamości aplikacji zasobów jest ustalana za pośrednictwem rejestracji w dzierżawie usługi Azure AD, zapewniając zarówno obiekt aplikacji, jak i jednostkę usługi. Niektóre interfejsy API dostarczane przez firmę Microsoft, takie jak interfejs API Microsoft Graph, mają wstępnie zarejestrowane jednostki usługi udostępnione we wszystkich dzierżawach podczas aprowizowania.

## <a name="roles"></a>role

Podobnie [jak zakresy](#scopes), role zapewniają serwerowi zasobów możliwość [zarządzania](#resource-server) dostępem do chronionych zasobów. Istnieją dwa typy: rola "użytkownik" implementuje kontrolę dostępu opartą na rolach dla użytkowników/grup, które wymagają [](#client-application) dostępu do zasobu, podczas gdy rola "aplikacja" implementuje to samo dla aplikacji klienckich, które wymagają dostępu.

Role są ciągami zdefiniowanymi przez zasoby (na przykład "Osoba zatwierdzająca wydatki", "Tylko do odczytu", "Directory.ReadWrite.All"), zarządzanymi w usłudze [Azure Portal][AZURE-portal] za pośrednictwem [manifestu](#application-manifest)aplikacji zasobu i przechowywanymi we właściwości [appRoles zasobu][Graph-Sp-Resource]. Interfejs Azure Portal jest również używany do przypisywania użytkowników do ról [](#permissions) "użytkownika" i konfigurowania uprawnień aplikacji klienckich w celu uzyskania dostępu do roli "aplikacja".

Aby uzyskać szczegółowe omówienie ról aplikacji udostępniane przez interfejs API Microsoft Graph, zobacz [interfejs Graph API Zakresy uprawnień.][Graph-Perm-Scopes] Aby uzyskać przykład implementacji krok po kroku, zobacz Dodawanie lub usuwanie przypisań ról platformy Azure przy [użyciu Azure Portal][AAD-RBAC].

## <a name="scopes"></a>scopes

Podobnie [jak role](#roles), zakresy zapewniają serwerowi zasobów możliwość zarządzania dostępem do chronionych zasobów. [](#resource-server) Zakresy są używane do [implementowanie][OAuth2-Access-Token-Scopes] kontroli [](#client-application) dostępu opartej na zakresach dla aplikacji klienckiej, która ma delegowany dostęp do zasobu przez jego właściciela.

Zakresy [to][AZURE-portal] ciągi zdefiniowane przez zasób (na przykład "Mail.Read", "Directory.ReadWrite.All"), zarządzane w usłudze Azure Portal za pośrednictwem [manifestu](#application-manifest)aplikacji zasobu i przechowywane we właściwości [oauth2Permissions][Graph-Sp-Resource]zasobu . Konfiguracja Azure Portal służy również do konfigurowania delegowanych uprawnień aplikacji [klienckiej w](#permissions) celu uzyskania dostępu do zakresu.

Najlepszą konwencją nazewnictwa jest użycie formatu "resource.operation.constraint". Aby uzyskać szczegółowe omówienie zakresów udostępniane przez interfejs API Microsoft Graph, zobacz [interfejs Graph API Zakresy uprawnień.][Graph-Perm-Scopes] Aby uzyskać zakresy udostępniane przez usługi Microsoft 365, zobacz Microsoft 365 API permissions reference (Dokumentacja uprawnień [interfejsu API).][O365-Perm-Ref]

## <a name="security-token"></a>token zabezpieczający

Podpisany dokument zawierający oświadczenia, takie jak token OAuth2 lub asercja SAML 2.0. W przypadku udzielenia [](#authorization-grant)autoryzacji OAuth2 , [token](#access-token) dostępu (OAuth2), token odświeżania i [token](#refresh-token) [identyfikatora](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) to typy tokenów zabezpieczających, z których wszystkie są implementowane jako tokeny JSON Web Token [(JWT).][JWT]

## <a name="service-principal-object"></a>obiekt jednostki usługi

Podczas rejestrowania/aktualizowania aplikacji w usłudze [Azure Portal][AZURE-portal]portal tworzy/aktualizuje zarówno obiekt aplikacji, jak i odpowiedni obiekt jednostki usługi dla tej dzierżawy. [](#application-object) Obiekt aplikacji  definiuje globalnie konfigurację tożsamości aplikacji (we wszystkich dzierżawach, do których skojarzona aplikacja ma udzielony  dostęp) i jest szablonem, na podstawie którego odpowiednie obiekty jednostki usługi są wyprowadzone do użytku lokalnego w czasie działania (w określonej dzierżawie).

Aby uzyskać więcej informacji, zobacz [Application and Service Principal Objects (Obiekty aplikacji i jednostki usługi).][AAD-App-SP-Objects]

## <a name="sign-in"></a>sign-in

Proces aplikacji [klienckiej](#client-application) inicjujący uwierzytelnianie użytkownika końcowego i przechwytujący powiązany stan w celu uzyskania [tokenu](#security-token) zabezpieczającego i ustawienia zakresu sesji aplikacji na ten stan. Stan może zawierać artefakty, takie jak informacje o profilu użytkownika i informacje pochodzące z oświadczeń tokenu.

Funkcja logowania aplikacji jest zwykle używana do implementować logowanie jednokrotne. Może być ona również poprzedzona funkcją "rejestracji", jako punkt wejścia dla użytkownika końcowego w celu uzyskania dostępu do aplikacji (po pierwszym zalogowaniu). Funkcja rejestracji służy do zbierania i utrwalania dodatkowego stanu specyficznego dla użytkownika i może wymagać [zgody użytkownika.](#consent)

## <a name="sign-out"></a>wylogowanie

Proces nieuwierzytania użytkownika końcowego, odłączania stanu [](#client-application) użytkownika skojarzonego z sesją aplikacji klienckiej podczas [logowania](#sign-in)

## <a name="tenant"></a>Dzierżawy

Wystąpienie katalogu usługi Azure AD jest określane jako dzierżawa usługi Azure AD. Udostępnia on kilka funkcji, w tym:

* usługa rejestru dla zintegrowanych aplikacji
* uwierzytelnianie kont użytkowników i zarejestrowanych aplikacji
* Punkty końcowe REST wymagane do obsługi różnych protokołów, [](#authorization-endpoint)w tym OAuth2 i SAML, w tym punkt końcowy autoryzacji, punkt końcowy [tokenu](#token-endpoint) i "wspólny" punkt końcowy używany przez aplikacje [wielodostępne.](#multi-tenant-application)

Dzierżawy usługi Azure AD są tworzone/kojarzone z platformą Azure Microsoft 365 subskrypcjami podczas tworzenia konta, zapewniając funkcje zarządzania dostępem & tożsamości dla subskrypcji. Administratorzy subskrypcji platformy Azure mogą również tworzyć dodatkowe dzierżawy usługi Azure AD za pośrednictwem Azure Portal. Zobacz [Jak uzyskać dzierżawę Azure Active Directory,][AAD-How-To-Tenant] aby uzyskać szczegółowe informacje na temat różnych sposobów uzyskiwania dostępu do dzierżawy. Zobacz Kojarzenie subskrypcji platformy Azure z dzierżawą usługi Azure Active Directory lub dodawanie [jej, aby][AAD-How-Subscriptions-Assoc] uzyskać szczegółowe informacje na temat relacji między subskrypcjami i dzierżawą usługi Azure AD oraz instrukcje dotyczące sposobu kojarzenia lub dodawania subskrypcji do dzierżawy usługi Azure AD.

## <a name="token-endpoint"></a>punkt końcowy tokenu

Jeden z punktów końcowych implementowany przez serwer [autoryzacji w](#authorization-server) celu obsługi autoryzacji OAuth2 [udziela uprawnień](#authorization-grant). W zależności od udzielenia można go użyć do uzyskania tokenu dostępu (i powiązanego [tokenu](#access-token) ["odświeżania")][OpenIDConnect] do klienta lub [](#client-application) [tokenu identyfikatora](#id-token) w przypadku korzystania z OpenID Connect protokołu.

## <a name="user-agent-based-client"></a>Klient oparty na agencie użytkownika

Typ aplikacji [klienckiej,](#client-application) która pobiera kod z serwera internetowego i jest wykonywana w obrębie agenta użytkownika (na przykład przeglądarki internetowej), takiego jak aplikacja jednostronicowa (SPA). Ponieważ cały kod jest wykonywany na urządzeniu, jest traktowany jako klient "publiczny", ponieważ nie może przechowywać poświadczeń prywatnie/poufne. Aby uzyskać więcej informacji, zobacz [Typy i profile klientów OAuth2.][OAuth2-Client-Types]

## <a name="user-principal"></a>nazwa główna użytkownika

Podobnie jak w przypadku obiektu jednostki usługi do reprezentowania wystąpienia aplikacji, obiekt podmiotu zabezpieczeń użytkownika jest innym typem podmiotu zabezpieczeń, który reprezentuje użytkownika. Typ Microsoft Graph [użytkownika][Graph-User-Resource] definiuje schemat obiektu użytkownika, w tym właściwości związane z użytkownikiem, takie jak imię i nazwisko, główna nazwa użytkownika, członkostwo w roli katalogu itp. Zapewnia to konfigurację tożsamości użytkownika dla usługi Azure AD w celu ustanowienia jednostki użytkownika w czasie uruchamiania. Nazwa główna użytkownika jest używana do reprezentowania uwierzytelnionego [](#consent) użytkownika na potrzeby logowania pojedynczego, rejestrowania delegowania zgody, podejmowania decyzji dotyczących kontroli dostępu itp.

## <a name="web-client"></a>klient internetowy

Typ aplikacji [klienckiej,](#client-application) która wykonuje cały kod na serwerze internetowym i może działać jako klient "poufny" przez bezpieczne przechowywanie poświadczeń na serwerze. Aby uzyskać więcej informacji, zobacz [Typy i profile klientów OAuth2.][OAuth2-Client-Types]

## <a name="next-steps"></a>Następne kroki

Przewodnik dewelopera platformy tożsamości firmy Microsoft to strona docelowa, której można używać we [][AAD-How-To-Integrate] wszystkich tematach związanych z tworzeniem platformy tożsamości firmy [Microsoft,][AAD-Dev-Guide] w tym omówienie integracji aplikacji oraz podstawowe informacje dotyczące uwierzytelniania platformy tożsamości firmy Microsoft i obsługiwanych scenariuszy [uwierzytelniania.][AAD-Auth-Scenarios] Przykłady kodu można również znaleźć w & samouczków na temat szybkiego uruchamiania aplikacji w [usłudze GitHub.](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=)

Skorzystaj z poniższej sekcji komentarzy, aby przekazać opinię i pomóc w uściślić i ukształtować tę zawartość, w tym żądania dotyczące nowych definicji lub aktualizowania istniejących.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[Graph-Perm-Scopes]: /graph/permissions-reference
[Graph-App-Resource]: /graph/api/resources/application
[Graph-Sp-Resource]: /graph/api/resources/serviceprincipal
[Graph-User-Resource]: /graph/api/resources/user
[AAD-How-Subscriptions-Assoc]:../fundamentals/active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]:quickstart-create-new-tenant.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Multi-Tenant-Overview]:howto-convert-app-to-be-multi-tenant.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AZURE-portal]: https://portal.azure.com
[AAD-RBAC]: ../../role-based-access-control/role-assignments-portal.md
[JWT]: https://tools.ietf.org/html/rfc7519
[Microsoft-Graph]: https://developer.microsoft.com/graph
[O365-Perm-Ref]: /graph/permissions-reference
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
