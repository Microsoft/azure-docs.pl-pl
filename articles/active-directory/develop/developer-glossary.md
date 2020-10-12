---
title: Microsoft Identity platform Developer słownik | Azure
description: Lista warunków dotyczących często używanych koncepcji i funkcji dla deweloperów platformy Microsoft Identity.
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
ms.openlocfilehash: b02329d624eda440230fb99e02e08c841c5580f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90706000"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>Słownik oprogramowania Microsoft Identity platform dla deweloperów

Ten artykuł zawiera definicje niektórych podstawowych pojęć i terminologii dla deweloperów, które są przydatne podczas uczenia się o tworzeniu aplikacji przy użyciu platformy tożsamości firmy Microsoft.

## <a name="access-token"></a>token dostępu

Typ [tokenu zabezpieczającego](#security-token) wystawionego przez [serwer autoryzacji](#authorization-server)i używany przez [aplikację kliencką](#client-application) w celu uzyskania dostępu do [chronionego serwera zasobów](#resource-server). Zwykle w postaci [tokenu sieci Web JSON (JWT)][JWT]token uwzględnia autoryzację przydaną klientowi przez [właściciela zasobu](#resource-owner)dla żądanego poziomu dostępu. Token zawiera wszystkie odpowiednie [oświadczenia](#claim) dotyczące tematu, umożliwiając aplikacji klienckiej używanie jej jako poświadczeń podczas uzyskiwania dostępu do danego zasobu. Eliminuje to również potrzebę, aby właściciel zasobu mógł ujawnić poświadczenia klientowi.

Tokeny dostępu są prawidłowe tylko przez krótki czas i nie można ich odwołać. Po wystawieniu tokenu dostępu serwer autoryzacji może także wydać [token odświeżenia](#refresh-token) . Tokeny odświeżania są zazwyczaj udostępniane tylko dla poufnych aplikacji klienckich.

Tokeny dostępu są czasami określane jako "użytkownik + aplikacja" lub "tylko aplikacja", w zależności od reprezentowanego poświadczenia. Na przykład, gdy aplikacja kliencka używa programu:

* ["Kod autoryzacji"](#authorization-grant), użytkownik końcowy najpierw uwierzytelnia się jako właściciel zasobu, delegując autoryzację do klienta w celu uzyskania dostępu do zasobu. Klient jest uwierzytelniany w późniejszym czasie podczas uzyskiwania tokenu dostępu. Token może być czasami określany jako token "User + App", ponieważ reprezentuje zarówno użytkownika, który ma autoryzowaną aplikację kliencką, jak i aplikację.
* ["Poświadczenia klienta" udzielenie autoryzacji](#authorization-grant), klient zapewnia uwierzytelnianie pojedyncze, działające bez uwierzytelniania/autoryzacji właściciela zasobu, więc token może być nazywany tokenem "tylko aplikacja".

Aby uzyskać więcej informacji, zobacz [Informacje o tokenach platformy tożsamości firmy Microsoft][AAD-Tokens-Claims] .

## <a name="application-id-client-id"></a>Identyfikator aplikacji (identyfikator klienta)

Unikatowy identyfikator dotyczący rejestracji aplikacji, który identyfikuje konkretną aplikację i skojarzone konfiguracje, w usłudze Azure AD. Ten identyfikator aplikacji ([Identyfikator klienta](https://tools.ietf.org/html/rfc6749#page-15)) jest używany podczas wykonywania żądań uwierzytelniania i jest dostarczany do bibliotek uwierzytelniania w czasie projektowania. Identyfikator aplikacji (identyfikator klienta) nie jest wpisem tajnym.

## <a name="application-manifest"></a>manifest aplikacji

Funkcja udostępniona przez [Azure Portal][AZURE-portal], która tworzy reprezentację w formacie JSON konfiguracji tożsamości aplikacji, używaną jako mechanizm do aktualizowania skojarzonej z nią jednostek [aplikacji][Graph-App-Resource] i obiektów [serviceprincipal][Graph-Sp-Resource] . Aby uzyskać więcej informacji [, zobacz Opis manifestu aplikacji Azure Active Directory][AAD-App-Manifest] .

## <a name="application-object"></a>Obiekt aplikacji

Po zarejestrowaniu/zaktualizowaniu aplikacji w [Azure Portal][AZURE-portal]Portal tworzy/aktualizuje obiekt aplikacji i odpowiadający mu [obiekt głównej usługi](#service-principal-object) dla tej dzierżawy. Obiekt aplikacji *definiuje* konfigurację tożsamości aplikacji globalnie (w przypadku wszystkich dzierżawców, w których ma dostęp), dostarczając szablon, z którego odpowiadające mu obiekty główne usługi są *wyprowadzane* lokalnie w czasie wykonywania (w określonej dzierżawie).

Aby uzyskać więcej informacji, zobacz [obiekty główne aplikacji i usługi][AAD-App-SP-Objects].

## <a name="application-registration"></a>rejestracja aplikacji

Aby umożliwić aplikacji integrację z funkcjami zarządzania tożsamościami i dostępem do usługi Azure AD oraz ich delegowanie, należy ją zarejestrować w [dzierżawie](#tenant)usługi Azure AD. Po zarejestrowaniu aplikacji w usłudze Azure AD udostępniamy konfigurację tożsamości dla aplikacji, umożliwiając jej integrację z usługą Azure AD i używanie takich funkcji jak:

* Niezawodne zarządzanie pojedynczymi Sign-On przy użyciu usługi Azure AD Identity Management i implementacji protokołu [połączenia OpenID Connect][OpenIDConnect]
* Dostęp przez brokera do [chronionych zasobów](#resource-server) przez [aplikacje klienckie](#client-application)za pośrednictwem [serwera autoryzacji](#authorization-server) OAuth 2,0
* [Struktura wyrażania zgody](#consent) na zarządzanie dostępem klientów do chronionych zasobów w oparciu o autoryzację właściciela zasobu.

Aby uzyskać więcej informacji, zobacz [Integrowanie aplikacji z Azure Active Directory][AAD-Integrating-Apps] .

## <a name="authentication"></a>uwierzytelnianie

Czynność zaskarżenia strony dla uprawnionych poświadczeń, która stanowi podstawę do utworzenia podmiotu zabezpieczeń, który ma być używany na potrzeby tożsamości i kontroli dostępu. Podczas [udzielania autoryzacji OAuth2](#authorization-grant) na przykład uwierzytelnianie strony wypełnia rolę [właściciela zasobu](#resource-owner) lub [aplikacji klienckiej](#client-application), w zależności od używanej dotacji.

## <a name="authorization"></a>autoryzacja

Czynność udzielenia uprawnienia uwierzytelnionego podmiotu zabezpieczeń. Istnieją dwa główne przypadki użycia w modelu programowania w usłudze Azure AD:

* Podczas przepływu [przyznawania autoryzacji OAuth2](#authorization-grant) : gdy [właściciel zasobu](#resource-owner) przyznaje autoryzację [aplikacji klienckiej](#client-application), zezwolenie klientowi na dostęp do zasobów właściciela zasobu.
* Podczas uzyskiwania dostępu do zasobów przez klienta: zgodnie z implementacją [serwera zasobów](#resource-server), za pomocą wartości [roszczeń](#claim) zawartych w [tokenie dostępu](#access-token) w celu podejmowania decyzji dotyczących kontroli dostępu.

## <a name="authorization-code"></a>kod autoryzacji

Krótko mówiąc "token" dostarczony do [aplikacji klienckiej](#client-application) przez [punkt końcowy autoryzacji](#authorization-endpoint)w ramach przepływu "kod autoryzacji", jeden z czterech OAuth2 [autoryzacji](#authorization-grant). Kod jest zwracany do aplikacji klienckiej w odpowiedzi na uwierzytelnianie [właściciela zasobu](#resource-owner), wskazując właścicielowi zasobu delegowane autoryzację w celu uzyskania dostępu do żądanych zasobów. W ramach przepływu kod jest później zrealizowany dla [tokenu dostępu](#access-token).

## <a name="authorization-endpoint"></a>punkt końcowy autoryzacji

Jeden z punktów końcowych wdrożonych przez [serwer autoryzacji](#authorization-server)używany do współdziałania z [właścicielem zasobu](#resource-owner) , aby zapewnić [udzielenie autoryzacji](#authorization-grant) w ramach przepływu OAuth2a autoryzacji. W zależności od użytego przepływu przydzielenia uprawnień rzeczywisty przydzielony udział może się różnić, łącznie z [kodem autoryzacji](#authorization-code) lub [tokenem zabezpieczeń](#security-token).

Aby uzyskać więcej informacji, zobacz sekcję Określanie [typów autoryzacji][OAuth2-AuthZ-Grant-Types] i [punktów końcowych][OAuth2-AuthZ-Endpoint] autoryzacji specyfikacji OAuth2 oraz [specyfikację OpenIDConnect][OpenIDConnect-AuthZ-Endpoint] .

## <a name="authorization-grant"></a>przyznawanie autoryzacji

Poświadczenie reprezentujące [autoryzację](#authorization) [właściciela zasobu](#resource-owner) w celu uzyskania dostępu do chronionych zasobów przyznanych [aplikacji klienckiej](#client-application). Aplikacja kliencka może używać jednego z [czterech typów dotacji zdefiniowanych przez strukturę autoryzacji OAuth2][OAuth2-AuthZ-Grant-Types] w celu uzyskania dotacji, w zależności od typu klienta/wymagań: "przyznanie kodu autoryzacji", "przyznanie poświadczeń klienta", "niejawne przyznanie" i "przyznanie poświadczeń hasła właściciela zasobu". Poświadczenie zwrócone do klienta to [token dostępu](#access-token)lub [kod autoryzacji](#authorization-code) (wymieniany później dla tokenu dostępu), w zależności od typu używanego przyznanych autoryzacji.

## <a name="authorization-server"></a>serwer autoryzacji

Zgodnie z definicją w [strukturze autoryzacji OAuth2][OAuth2-Role-Def], serwer odpowiedzialny za wystawianie tokenów dostępu do [klienta](#client-application) po pomyślnym uwierzytelnieniu [właściciela zasobu](#resource-owner) i uzyskaniu jego autoryzacji. [Aplikacja kliencka](#client-application) współdziała z serwerem autoryzacji w środowisku uruchomieniowym za pośrednictwem punktów końcowych [autoryzacji](#authorization-endpoint) i [tokenów](#token-endpoint) zgodnie z OAuth2 określonymi [dotacjami autoryzacji](#authorization-grant).

W przypadku integracji aplikacji platformy tożsamości firmy Microsoft platforma Microsoft Identity implementuje rolę serwera autoryzacji dla aplikacji usługi Azure AD i interfejsów API usługi firmy Microsoft, na przykład [Microsoft Graph interfejsów API][Microsoft-Graph].

## <a name="claim"></a>rozpatrywan

[Token zabezpieczający](#security-token) zawiera oświadczenia, które zapewniają potwierdzenia dotyczące jednej jednostki (na przykład [aplikacji klienckiej](#client-application) lub [właściciela zasobu](#resource-owner)) do innej jednostki (na przykład [serwera zasobów](#resource-server)). Oświadczenia są parami nazw/wartości, które przekazują fakty dotyczące podmiotu tokenu (na przykład podmiot zabezpieczeń, który został uwierzytelniony przez [serwer autoryzacji](#authorization-server)). Oświadczenia obecne w danym tokenie są zależne od kilku zmiennych, w tym typu tokenu, typu poświadczenia używanego do uwierzytelniania podmiotu, konfiguracji aplikacji itp.

Aby uzyskać więcej informacji, zobacz [Informacje o tokenach platformy tożsamości firmy Microsoft][AAD-Tokens-Claims] .

## <a name="client-application"></a>Aplikacja kliencka

Zgodnie z definicją w [strukturze autoryzacji OAuth2][OAuth2-Role-Def], aplikacja, która udostępnia chronione żądania zasobów w imieniu [właściciela zasobu](#resource-owner). Termin "klient" nie oznacza żadnych konkretnych charakterystyk implementacji sprzętu (np. czy aplikacja jest uruchamiana na serwerze, na komputerze, na innym urządzeniu).

Aplikacja kliencka żąda [autoryzacji](#authorization) od właściciela zasobu do udziału w przepływie [autoryzacji OAuth2](#authorization-grant) i może uzyskać dostęp do interfejsów API/danych w imieniu właściciela zasobu. Platforma autoryzacji OAuth2 [definiuje dwa typy klientów][OAuth2-Client-Types], "poufne" i "publiczne", w oparciu o zdolność klienta do zachowania poufności poświadczeń. Aplikacje mogą implementować [klienta sieci Web (poufne)](#web-client) działającego na serwerze sieci Web, [klienta natywnego (publicznego)](#native-client) zainstalowanego na urządzeniu lub [klienta opartego na agencie użytkownika (publicznego)](#user-agent-based-client) , który działa w przeglądarce urządzenia.

## <a name="consent"></a>posiadacz

Proces [właściciel zasobu](#resource-owner) udzielający autoryzacji dla [aplikacji klienckiej](#client-application)w celu uzyskania dostępu do chronionych zasobów w ramach określonych [uprawnień](#permissions)w imieniu właściciela zasobu. W zależności od uprawnień wymaganych przez klienta administrator lub użytkownik zostanie poproszony o zgodę na zezwolenie na dostęp do ich organizacji/poszczególnych danych. Należy pamiętać, że w scenariuszu z [wieloma dzierżawcami](#multi-tenant-application) nazwa [główna usługi](#service-principal-object) aplikacji jest również rejestrowana w dzierżawie użytkownika, który wyraził zgodę.

Aby uzyskać więcej informacji, zobacz [platformę wyrażania zgody](consent-framework.md) .

## <a name="id-token"></a>Identyfikator tokenu

[Token zabezpieczający](#security-token) [OpenID Connect Connect][OpenIDConnect-ID-Token] dostarczony przez [punkt końcowy autoryzacji](#authorization-endpoint) [serwera autoryzacji](#authorization-server) , który zawiera [oświadczenia](#claim) dotyczące uwierzytelniania [właściciela zasobu](#resource-owner)użytkownika końcowego. Podobnie jak token dostępu, tokeny identyfikatora są również reprezentowane jako cyfrowy [token sieci Web JSON (JWT)][JWT]. W przeciwieństwie do tokenu dostępu, oświadczenia tokenu identyfikatora nie są używane do celów związanych z dostępem do zasobów i w specjalnej kontroli dostępu.

Aby uzyskać więcej informacji, zobacz [Informacje o tokenach platformy tożsamości firmy Microsoft][AAD-Tokens-Claims] .

## <a name="microsoft-identity-platform"></a>Platforma tożsamości firmy Microsoft

Platforma tożsamości firmy Microsoft to unowocześnienie platformy deweloperów i usługi tożsamości Azure Active Directory (Azure AD). Dzięki niej deweloperzy mogą tworzyć aplikacje, które logują użytkowników przy użyciu wszystkich tożsamości firmy Microsoft i uzyskują tokeny do wywoływania programu Microsoft Graph, innych interfejsów API firmy Microsoft lub interfejsów API opracowanych przez deweloperów. Jest to w pełni funkcjonalna platforma, która obejmuje usługę uwierzytelniania, biblioteki, rejestrację i konfigurację aplikacji, pełną dokumentację dla deweloperów, przykłady kodu i inną zawartość dla deweloperów. Platforma tożsamości firmy Microsoft obsługuje standardowe protokoły branżowe, takie jak OAuth 2.0 i OpenID Connect.

## <a name="multi-tenant-application"></a>Aplikacja wielodostępna

Klasa aplikacji, która umożliwia logowanie i [zgodę](#consent) użytkowników zainicjowanych w dowolnej [dzierżawie](#tenant)usługi Azure AD, w tym dzierżawców innych niż te, w których zarejestrowano klienta. [Natywne aplikacje klienckie](#native-client) mają domyślnie wiele dzierżawców, natomiast [Klient sieci Web](#web-client) i aplikacje [sieci Web/interfejsy API](#resource-server) umożliwiają wybranie jednej lub wielu dzierżawców. Z kolei aplikacja sieci Web zarejestrowana w ramach jednej dzierżawy zezwala tylko na logowanie z kont użytkowników, które są obsługiwane w tej samej dzierżawie, co w przypadku, gdy aplikacja jest zarejestrowana.

Aby uzyskać więcej informacji [, zobacz Jak zalogować się do dowolnego użytkownika usługi Azure AD za pomocą wzorca aplikacji wielodostępnych][AAD-Multi-Tenant-Overview] .

## <a name="native-client"></a>Klient natywny

Typ [aplikacji klienckiej](#client-application) zainstalowanej natywnie na urządzeniu. Ponieważ cały kod jest wykonywany na urządzeniu, jest uznawany za "publiczny" klient z powodu niezdolności do prywatnego przechowywania poświadczeń. Aby uzyskać więcej informacji [, zobacz Typy i profile klientów OAuth2][OAuth2-Client-Types] .

## <a name="permissions"></a>uprawnienia

[Aplikacja kliencka](#client-application) uzyskuje dostęp do [serwera zasobów](#resource-server) , deklarując żądania uprawnień. Dostępne są dwa typy:

* Uprawnienia "delegowane", które określają dostęp [oparty na zakresie](#scopes) przy użyciu delegowanej autoryzacji z zalogowanego [właściciela zasobu](#resource-owner), są przedstawiane zasobowi w czasie wykonywania jako [oświadczenie "SCP"](#claim) w [tokenie dostępu](#access-token)klienta.
* Uprawnienia "aplikacja", które określają dostęp [oparty na rolach](#roles) przy użyciu poświadczeń/tożsamości aplikacji klienta, są przedstawiane zasobowi w czasie wykonywania jako [oświadczenia "role"](#claim) w tokenie dostępu klienta.

Są one również używane podczas procesu [wyrażania zgody](#consent) , dzięki czemu administrator lub właściciel zasobu mogą udzielić/odmówić dostępu klientowi do zasobów w swojej dzierżawie.

Żądania uprawnień są konfigurowane na stronie **uprawnienia interfejsu API** dla aplikacji w [Azure Portal][AZURE-portal], wybierając odpowiednie "delegowane uprawnienia" i "uprawnienia aplikacji" (ta ostatnia wymaga członkostwa w roli administratora globalnego). Ponieważ [Klient publiczny](#client-application) nie może bezpiecznie zachować poświadczeń, może zażądać tylko uprawnień delegowanych, natomiast [Klient poufny](#client-application) ma możliwość żądania zarówno delegowania, jak i dostępu do aplikacji. [Obiekt aplikacji](#application-object) klienta przechowuje zadeklarowane uprawnienia we [Właściwości requiredResourceAccess][Graph-App-Resource].

## <a name="refresh-token"></a>Odśwież token

Typ [tokenu zabezpieczającego](#security-token) wystawionego przez [serwer autoryzacji](#authorization-server)i używany przez [aplikację kliencką](#client-application) do żądania nowego [tokenu dostępu](#access-token) przed wygaśnięciem tokenu dostępu. Zwykle w postaci [tokenu sieci Web JSON (JWT)][JWT].

W przeciwieństwie do tokenów dostępu można odwołać tokeny odświeżania. Jeśli aplikacja kliencka próbuje zażądać nowego tokenu dostępu przy użyciu tokenu odświeżenia, który został odwołany, serwer autoryzacji odmówi żądania, a aplikacja kliencka nie będzie już miała uprawnienia dostępu do [serwera zasobów](#resource-server) w imieniu [właściciela zasobu](#resource-owner).

## <a name="resource-owner"></a>Właściciel zasobu

Zgodnie z definicją w [ramach platformy autoryzacji OAuth2][OAuth2-Role-Def]jednostka może udzielić dostępu do chronionego zasobu. Gdy właścicielem zasobu jest osoba, jest ona określana jako użytkownik końcowy. Na przykład gdy [aplikacja kliencka](#client-application) chce uzyskać dostęp do skrzynki pocztowej użytkownika za pośrednictwem [interfejsu API Microsoft Graph][Microsoft-Graph], wymaga uprawnień od właściciela zasobu skrzynki pocztowej.

## <a name="resource-server"></a>serwer zasobów

Zgodnie z definicją w [strukturze autoryzacji OAuth2][OAuth2-Role-Def]serwer, który hostuje chronione zasoby, może akceptować i odpowiadać na żądania zasobów chronionych przez [aplikacje klienckie](#client-application) , które składają [token dostępu](#access-token). Znany również jako chroniony serwer zasobów lub aplikacja zasobów.

Serwer zasobów udostępnia interfejsy API i wymusza dostęp do zasobów chronionych przez [zakresy](#scopes) i [role](#roles)przy użyciu platformy autoryzacji OAuth 2,0. Przykłady obejmują [interfejs api Microsoft Graph][Microsoft-Graph] , który zapewnia dostęp do danych dzierżawy usługi Azure AD, oraz interfejsy API Microsoft 365, które zapewniają dostęp do danych, takich jak poczta i kalendarz.

Podobnie jak aplikacja kliencka, Konfiguracja tożsamości aplikacji zasobów jest ustanawiana za pośrednictwem [rejestracji](#application-registration) w dzierżawie usługi Azure AD, dostarczając zarówno obiekt główny aplikacji, jak i obiektu usługi. Niektóre interfejsy API udostępniane przez firmę Microsoft, takie jak interfejs API Microsoft Graph, mają wstępnie zarejestrowane jednostki usługi udostępniane we wszystkich dzierżawcach podczas aprowizacji.

## <a name="roles"></a>role

Podobnie jak [zakresy](#scopes), role umożliwiają [serwerowi zasobów](#resource-server) zarządzanie dostępem do chronionych zasobów. Istnieją dwa typy: rola "użytkownik" implementuje kontrolę dostępu opartą na rolach dla użytkowników/grup, które wymagają dostępu do zasobu, podczas gdy rola "aplikacja" implementuje te same dla [aplikacji klienckich](#client-application) , które wymagają dostępu.

Role są ciągami zdefiniowanymi przez zasób (na przykład "osoba zatwierdzająca wydatki", "tylko do odczytu", "katalog. ReadWrite. wszystkie"), zarządzane w [Azure Portal][AZURE-portal] za pośrednictwem [manifestu aplikacji](#application-manifest)zasobu i przechowywane we [Właściwości appRoles][Graph-Sp-Resource]zasobu. Azure Portal jest również używany do przypisywania użytkowników do ról "użytkownika" i konfigurowania [uprawnień aplikacji](#permissions) klienckich w celu uzyskania dostępu do roli "aplikacja".

Aby uzyskać szczegółową dyskusję na temat ról aplikacji udostępnianych przez interfejs API Microsoft Graph, zobacz [interfejs API programu Graph zakresów uprawnień][Graph-Perm-Scopes]. Aby zapoznać się z przykładem implementacji krok po kroku, zobacz [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu Azure Portal][AAD-RBAC].

## <a name="scopes"></a>scopes

Podobnie jak w przypadku [ról](#roles), zakresy zapewniają sposób, aby [serwer zasobów](#resource-server) zarządzał dostępem do chronionych zasobów. Zakresy są używane do implementowania kontroli dostępu [opartej na zakresie][OAuth2-Access-Token-Scopes] , dla [aplikacji klienckiej](#client-application) , która udzieliła delegowanego dostępu do zasobu przez jego właściciela.

Zakresy są ciągami zdefiniowanymi przez zasób (na przykład "mail. Read", "Directory. ReadWrite. All"), które są zarządzane w [Azure Portal][AZURE-portal] za pośrednictwem [manifestu aplikacji](#application-manifest)zasobu i przechowywane we [Właściwości oauth2Permissions][Graph-Sp-Resource]zasobu. Azure Portal jest również używany do konfigurowania [uprawnień delegowanych](#permissions) aplikacji klienckich w celu uzyskania dostępu do zakresu.

Najlepszym rozwiązaniem konwencji nazewnictwa jest użycie formatu "Resource. Operation. Constraint". Aby uzyskać szczegółowe omówienie zakresów udostępnianych przez interfejs Microsoft Graph API, zobacz [interfejs API programu Graph zakresów uprawnień][Graph-Perm-Scopes]. W przypadku zakresów udostępnianych przez Microsoft 365 usług zapoznaj się z tematem [Microsoft 365 informacje o uprawnieniach interfejsu API][O365-Perm-Ref].

## <a name="security-token"></a>token zabezpieczający

Podpisany dokument zawierający oświadczenia, takie jak token OAuth2 lub potwierdzenie SAML 2,0. W przypadku [przyznania autoryzacji](#authorization-grant)OAuth2 [token dostępu](#access-token) (OAuth2), [token odświeżania](#refresh-token)i [token identyfikatora](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) są typami tokenów zabezpieczających, które są implementowane jako [token sieci Web JSON (JWT)][JWT].

## <a name="service-principal-object"></a>Obiekt główny usługi

Po zarejestrowaniu/zaktualizowaniu aplikacji w [Azure Portal][AZURE-portal]Portal tworzy/aktualizuje [obiekt aplikacji](#application-object) i odpowiadający mu obiekt głównej usługi dla tej dzierżawy. Obiekt aplikacji *definiuje* konfigurację tożsamości aplikacji globalnie (dla wszystkich dzierżawców, do których skojarzona aplikacja została udzielona dostęp) i jest szablonem, z którego są *wyprowadzane* odpowiednie obiekty główne usługi do użytku lokalnego w czasie wykonywania (w określonej dzierżawie).

Aby uzyskać więcej informacji, zobacz [obiekty główne aplikacji i usługi][AAD-App-SP-Objects].

## <a name="sign-in"></a>sign-in

Proces [aplikacji klienckiej](#client-application) inicjującej uwierzytelnianie użytkowników końcowych i przechwytujący stan związany z uzyskaniem [tokenu zabezpieczającego](#security-token) i określanie zakresu sesji aplikacji do tego stanu. Stan może zawierać artefakty, takie jak informacje o profilu użytkownika i informacje pochodzące z oświadczeń tokenów.

Funkcja logowania aplikacji jest zwykle używana do implementowania logowania jednokrotnego (SSO). Może być również poprzedzona przez funkcję "rejestracji", jako punkt wejścia dla użytkownika końcowego, aby uzyskać dostęp do aplikacji (przy pierwszym logowaniu). Funkcja rejestracji służy do zbierania i utrwalania dodatkowego stanu specyficznego dla użytkownika i może wymagać [zgody użytkownika](#consent).

## <a name="sign-out"></a>wylogowanie

Proces uwierzytelniania użytkownika końcowego, odłączania stanu użytkownika skojarzonego z sesją [aplikacji klienta](#client-application) podczas [logowania](#sign-in)

## <a name="tenant"></a>dzierżaw

Wystąpienie katalogu usługi Azure AD nazywa się dzierżawą usługi Azure AD. Udostępnia ona kilka funkcji, w tym:

* Usługa rejestru dla zintegrowanych aplikacji
* uwierzytelnianie kont użytkowników i zarejestrowanych aplikacji
* Punkty końcowe REST są wymagane do obsługi różnych protokołów, w tym OAuth2 i SAML, w tym [punkt końcowy autoryzacji](#authorization-endpoint), [punkt końcowy tokenu](#token-endpoint) i punkt końcowy "Common" używany przez [aplikacje](#multi-tenant-application)wielodostępne.

Dzierżawy usługi Azure AD są tworzone/skojarzone z platformą Azure oraz Microsoft 365 subskrypcje podczas tworzenia konta, zapewniając funkcje zarządzania dostępem & do subskrypcji. Administratorzy subskrypcji platformy Azure mogą również tworzyć dodatkowe dzierżawy usługi Azure AD za pośrednictwem Azure Portal. Zobacz, [jak uzyskać dzierżawę Azure Active Directory][AAD-How-To-Tenant] , aby uzyskać szczegółowe informacje na temat różnych sposobów uzyskiwania dostępu do dzierżawy. Aby uzyskać szczegółowe informacje na temat relacji między subskrypcjami i dzierżawą usługi Azure AD, zobacz [kojarzenie lub Dodawanie subskrypcji platformy Azure do dzierżawy Azure Active Directory][AAD-How-Subscriptions-Assoc] . Aby uzyskać instrukcje dotyczące kojarzenia lub dodawania subskrypcji do dzierżawy usługi Azure AD.

## <a name="token-endpoint"></a>punkt końcowy tokenu

Jeden z punktów końcowych wdrożonych przez [serwer autoryzacji](#authorization-server) do obsługi OAuth2 [autoryzacji](#authorization-grant). W zależności od grantu można go użyć w celu uzyskania [tokenu dostępu](#access-token) (i powiązanego tokenu "Refresh") z [klientem](#client-application)lub [tokenem ID](#id-token) , gdy jest używany z protokołem [Connect OpenID Connect][OpenIDConnect] .

## <a name="user-agent-based-client"></a>Klient oparty na agencie

Typ [aplikacji klienckiej](#client-application) pobierającej kod z serwera sieci Web i wykonywany w ramach agenta użytkownika (na przykład przeglądarki sieci Web), takiego jak aplikacja jednostronicowa (Spa). Ponieważ cały kod jest wykonywany na urządzeniu, jest uznawany za "publiczny" klient z powodu niezdolności do prywatnego przechowywania poświadczeń. Aby uzyskać więcej informacji, zobacz [OAuth2 Client Types and Profile][OAuth2-Client-Types].

## <a name="user-principal"></a>Nazwa główna użytkownika

Podobnie jak w przypadku użycia obiektu jednostki usługi do reprezentowania wystąpienia aplikacji, obiekt główny użytkownika jest innym typem podmiotu zabezpieczeń, który reprezentuje użytkownika. [Typ zasobu użytkownika][Graph-User-Resource] Microsoft Graph definiuje schemat dla obiektu użytkownika, w tym właściwości związane z użytkownikiem, takie jak imię i nazwisko, główna nazwa użytkownika, członkostwo w roli katalogu itp. Zapewnia to konfigurację tożsamości użytkownika dla usługi Azure AD w celu ustanowienia podmiotu użytkownika w czasie wykonywania. Podmiot zabezpieczeń jest używany do reprezentowania uwierzytelnionego użytkownika do logowania jednokrotnego, rejestrowania delegowania [zgody](#consent) , podejmowania decyzji dotyczących kontroli dostępu itp.

## <a name="web-client"></a>Klient sieci Web

Typ [aplikacji klienckiej](#client-application) , która wykonuje cały kod na serwerze sieci Web i może pełnić funkcję "poufnego" klienta przez bezpieczne przechowywanie jego poświadczeń na serwerze. Aby uzyskać więcej informacji, zobacz [OAuth2 Client Types and Profile][OAuth2-Client-Types].

## <a name="next-steps"></a>Następne kroki

[Przewodnik dewelopera platformy tożsamości firmy Microsoft][AAD-Dev-Guide] jest stroną docelową dla wszystkich tematów związanych z programowaniem na platformie Microsoft Identity, w tym omówienie [integracji aplikacji][AAD-How-To-Integrate] oraz podstawy [uwierzytelniania platformy tożsamości firmy Microsoft i obsługiwanych scenariuszy uwierzytelniania][AAD-Auth-Scenarios]. Możesz również znaleźć przykłady kodu, & samouczki, jak szybko rozpocząć pracę w serwisie [GitHub](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=).

Poniższa sekcja komentarzy zawiera informacje zwrotne i pomoc w zakresie ulepszania i kształtowania zawartości, w tym żądania dotyczące nowych definicji lub aktualizowania istniejących.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[Graph-Perm-Scopes]: /graph/permissions-reference
[Graph-App-Resource]: /graph/api/resources/application
[Graph-Sp-Resource]: /graph/api/resources/serviceprincipal?view=graph-rest-beta
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
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
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