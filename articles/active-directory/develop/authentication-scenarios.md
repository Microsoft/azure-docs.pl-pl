---
title: Uwierzytelnianie na platformie tożsamości firmy Microsoft | Azure
description: Poznaj podstawowe informacje o uwierzytelnianiu w usłudze Microsoft Identity platform (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: d979745d9b5bb65bd08f69db86801156de2a489d
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/27/2020
ms.locfileid: "82161745"
---
# <a name="authentication-basics"></a>Podstawowe informacje o uwierzytelnianiu

W tym artykule omówiono wiele pojęć związanych z uwierzytelnianiem, które należy zrozumieć w celu utworzenia chronionych aplikacji sieci Web, interfejsów API sieci Web lub aplikacji wywołujących chronione interfejsy API sieci Web. Jeśli zobaczysz termin, którego nie znasz, wypróbuj nasz [słownik](developer-glossary.md) lub nasze [wideo platformy Microsoft Identity platform](identity-videos.md) , które obejmują podstawowe pojęcia.

## <a name="authentication-vs-authorization"></a>Uwierzytelnianie a autoryzacja

**Uwierzytelnianie** to proces potwierdzania użytkownika. Uwierzytelnianie jest czasami określane terminem AuthN. Platforma tożsamości firmy Microsoft implementuje protokół [Connect OpenID Connect](https://openid.net/connect/) do obsługi uwierzytelniania.

**Autoryzacja** to czynność udzielenia uprawnienia uwierzytelnionej stronie. Określa dane, do których można uzyskać dostęp i co można zrobić z tymi danymi. Autoryzacja jest czasami określana terminem AuthZ. Platforma tożsamości firmy Microsoft implementuje protokół [OAuth 2,0](https://oauth.net/2/) do obsługi autoryzacji.

Zamiast tworzyć aplikacje, które zachowują własne informacje o nazwie użytkownika i hasła, co wiąże się z dużym obciążeniem administracyjnym, gdy trzeba dodać lub usunąć użytkowników w wielu aplikacjach, aplikacje mogą delegować tę odpowiedzialność do scentralizowanego dostawcy tożsamości.

Azure Active Directory (Azure AD) to scentralizowany dostawca tożsamości w chmurze. Delegowanie uwierzytelniania i autoryzacji pozwala na takie scenariusze, jak zasady dostępu warunkowego, które wymagają, aby użytkownik znajdował się w określonej lokalizacji, korzystać z uwierzytelniania wieloskładnikowego, a także umożliwić użytkownikowi logowanie się jednokrotne do wszystkich aplikacji sieci Web, które współużytkują ten sam katalog scentralizowany. Ta funkcja jest określana jako logowanie jednokrotne **(SSO)**.

Platforma tożsamości firmy Microsoft upraszcza uwierzytelnianie i autoryzację dla deweloperów aplikacji, zapewniając tożsamość jako usługę, z obsługą standardowych protokołów, takich jak OAuth 2,0 i OpenID Connect Connect, a także bibliotek typu "open source" dla różnych platform, które ułatwiają szybkie rozpoczęcie tworzenia kodu. Dzięki temu deweloperzy mogą tworzyć aplikacje, które logują się do wszystkich tożsamości firmy Microsoft, uzyskują tokeny, aby wywoływać [Microsoft Graph](https://developer.microsoft.com/graph/), inne interfejsy API firmy Microsoft lub interfejsy API zaprojektowane przez deweloperów. Aby uzyskać więcej informacji, zobacz [ewolucja platformy tożsamości firmy Microsoft](about-microsoft-identity-platform.md).

## <a name="security-tokens"></a>Tokeny zabezpieczające

Scentralizowany dostawca tożsamości jest szczególnie istotny dla aplikacji, które znajdują się na całym świecie, niekoniecznie logują się z sieci przedsiębiorstwa. Platforma tożsamości firmy Microsoft uwierzytelnia użytkowników i udostępnia tokeny zabezpieczające, takie jak [token dostępu](developer-glossary.md#access-token), [token odświeżania](developer-glossary.md#refresh-token)i [tokeny identyfikatorów](developer-glossary.md#id-token), które umożliwiają [aplikacji klienckiej](developer-glossary.md#client-application) dostęp do chronionych zasobów na [serwerze zasobów](developer-glossary.md#resource-server).

**Tokeny dostępu** to token zabezpieczający wystawiony przez serwer autoryzacji. Zawiera informacje o użytkowniku i aplikacji, dla których jest przeznaczony token; służy do uzyskiwania dostępu do interfejsów API sieci Web i innych chronionych zasobów. Aby dowiedzieć się więcej o sposobie, w jaki oprogramowanie Microsoft Identity platform wystawia tokeny dostępu, zobacz [tokeny dostępu](access-tokens.md).

Tokeny dostępu są prawidłowe tylko przez krótki czas, więc serwery autoryzacji czasami wydają **tokeny odświeżania** w tym samym czasie, gdy token dostępu zostanie wystawiony. Aplikacja kliencka może następnie w razie potrzeby wymienić ten token odświeżania dla nowego tokenu dostępu. Aby dowiedzieć się więcej na temat sposobu odwoływania uprawnień przez platformę Microsoft Identity, zobacz [odwoływanie tokenu](access-tokens.md#token-revocation).

**Tokeny identyfikatorów** są wysyłane do aplikacji klienckiej w ramach przepływu [połączenia OpenID Connect](v2-protocols-oidc.md) . Mogą być wysyłane po stronie lub zamiast tokenu dostępu i są używane przez klienta do uwierzytelniania użytkownika. Aby dowiedzieć się więcej o tym, jak program Microsoft Identity platform generuje tokeny identyfikatorów, zobacz [identyfikatory tokenów](id-tokens.md).

### <a name="validating-security-tokens"></a>Weryfikowanie tokenów zabezpieczających

Jest to aplikacja, dla której Wygenerowano token, aplikację sieci Web, która została zarejestrowana przez użytkownika lub wywoływany interfejs API sieci Web, w celu zweryfikowania tokenu. Token jest podpisany przez serwer tokenu zabezpieczającego (STS) z kluczem prywatnym. Usługa STS publikuje odpowiadający mu klucz publiczny. Aby sprawdzić poprawność tokenu, aplikacja weryfikuje podpis przy użyciu klucza publicznego STS, aby sprawdzić, czy sygnatura została utworzona przy użyciu klucza prywatnego.

Tokeny są prawidłowe tylko przez ograniczony czas. Zazwyczaj usługa STS oferuje parę tokenów:

* Token dostępu do uzyskiwania dostępu do aplikacji lub chronionego zasobu, a także
* Token odświeżania używany do odświeżania tokenu dostępu, gdy token dostępu zbliża się do wygaśnięcia.

Tokeny dostępu są przesyłane do internetowego interfejsu API jako token okaziciela w `Authorization` nagłówku. Aplikacja może dostarczyć token odświeżania do usługi STS, a jeśli użytkownik nie ma dostępu do aplikacji, zostanie przywrócony nowy token dostępu i nowy token odświeżania. Jest to sposób obsługi scenariusza, w którym ktoś opuszcza przedsiębiorstwo. Gdy usługa STS odbierze token odświeżenia, nie będzie wydawać innego prawidłowego tokenu dostępu, jeśli użytkownik nie jest już autoryzowany.

### <a name="json-web-tokens-jwts-and-claims"></a>Tokeny sieci Web JSON (JWTs) i oświadczenia

Platforma tożsamości firmy Microsoft implementuje tokeny zabezpieczające jako tokeny sieci Web JSON (JWTs), które zawierają oświadczenia.

[Twierdzenie](developer-glossary.md#claim) zawiera potwierdzenia dotyczące jednej jednostki, takiej jak aplikacja kliencka lub [właściciel zasobu](developer-glossary.md#resource-owner), do innej jednostki, takiej jak serwer zasobów.

Oświadczenia są parami nazw/wartości, które przekazują fakty dotyczące podmiotu tokenu. Na przykład zgłoszenie może zawierać fakty dotyczące podmiotu zabezpieczeń, który został uwierzytelniony przez [serwer autoryzacji](developer-glossary.md#authorization-server). Oświadczenia obecne w danym tokenie zależą od wielu elementów, w tym typu tokenu, typu poświadczenia używanego do uwierzytelniania podmiotu, konfiguracji aplikacji i tak dalej.

Aplikacje mogą używać oświadczeń dla różnych zadań, takich jak:

* Sprawdzanie poprawności tokenu
* Identyfikowanie dzierżawy podmiotu
* Wyświetlanie informacji o użytkowniku
* Określanie autoryzacji podmiotu

Element Claim składa się z par klucz-wartość, które zawierają informacje takie jak:

* Serwer tokenów zabezpieczających, który wygenerował token
* Data wygenerowania tokenu
* Podmiot (na przykład użytkownik — z wyjątkiem demonów)
* Odbiorcy, czyli aplikacja, dla której Wygenerowano token
* Aplikacja (klient), która poprosiła o token. W przypadku aplikacji sieci Web może to być takie samo jak odbiorcy

Aby dowiedzieć się więcej o tym, jak platforma tożsamości firmy Microsoft implementuje tokeny i informacje dotyczące roszczeń, zobacz [tokeny dostępu](access-tokens.md) i [tokeny identyfikatorów](id-tokens.md).

### <a name="how-each-flow-emits-tokens-and-codes"></a>Jak każdy przepływ emituje tokeny i kody

W zależności od sposobu skompilowania klienta można użyć jednego (lub kilku) przepływów uwierzytelniania obsługiwanych przez platformę tożsamości firmy Microsoft. Te przepływy mogą generować różne tokeny (id_tokens, tokeny odświeżenia, tokeny dostępu), a także kody autoryzacji i wymagać innych tokenów, aby działały. Ten wykres zawiera przegląd:

|Ruch | KONIECZN | id_token | token dostępu | Odśwież token | kod autoryzacji |
|-----|----------|----------|--------------|---------------|--------------------|
|[Przepływ kodu autoryzacji](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[Niejawny przepływ](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybrydowy przepływ OIDC](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Odświeżanie umorzenia tokenu](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | Odśwież token | x | x | x| |
|[Przepływ „w imieniu”](v2-oauth2-on-behalf-of-flow.md) | token dostępu| x| x| x| |
|[Poświadczenia klienta](v2-oauth2-client-creds-grant-flow.md) | | | x (tylko aplikacja)| | |

Tokeny wystawione za pośrednictwem trybu niejawnego mają ograniczenie długości z powodu przekazanie z powrotem do przeglądarki `response_mode` za `query` pośrednictwem adresu URL (gdzie is lub `fragment`).  Niektóre przeglądarki mają limit rozmiaru adresu URL, który może być umieszczony na pasku przeglądarki i niepowodzenie, gdy jest zbyt długi.  W rezultacie te tokeny nie mają `groups` ani `wids` oświadczenia.

## <a name="tenants"></a>Dzierżawy

Dostawca tożsamości w chmurze służy do zapewniania wielu organizacji. Aby zapewnić, że użytkownicy z różnych organizacji są oddzielni, usługa Azure AD jest dzielona na dzierżawców z jedną dzierżawą na organizację.

Dzierżawy śledzą użytkowników i ich skojarzone aplikacje. Platforma tożsamości firmy Microsoft obsługuje również użytkowników logujących się przy użyciu osobistych kont Microsoft.

Usługa Azure AD udostępnia również Azure Active Directory B2C, dzięki czemu organizacje mogą logować użytkowników, zazwyczaj klientów przy użyciu tożsamości społecznościowych, takich jak konto Google. Aby uzyskać więcej informacji, zobacz [dokumentację Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c) .

Teraz, gdy znasz już podstawowe informacje, Przeczytaj, jak poznać model i interfejs API aplikacji tożsamości, Dowiedz się, w jaki sposób Inicjowanie obsługi działa na platformie tożsamości firmy Microsoft i uzyskać linki do szczegółowych informacji na temat typowych scenariuszy obsługiwanych przez platformę tożsamości firmy Microsoft.

## <a name="application-model"></a>Model aplikacji

Aplikacje mogą logować się samodzielnie lub delegować użytkowników do dostawcy tożsamości. Zobacz [przepływy uwierzytelniania i scenariusze aplikacji](authentication-flows-app-scenarios.md) , aby poznać scenariusze logowania obsługiwane przez platformę tożsamości firmy Microsoft.

Aby dostawca tożsamości mógł wiedzieć, że użytkownik ma dostęp do określonej aplikacji, zarówno użytkownik, jak i aplikacja muszą być zarejestrowane przy użyciu dostawcy tożsamości. Po zarejestrowaniu aplikacji w usłudze Azure AD udostępniamy konfigurację tożsamości dla aplikacji, która umożliwia integrację z platformą tożsamości firmy Microsoft. Zarejestrowanie aplikacji umożliwia również:

* Dostosuj znakowanie aplikacji w oknie dialogowym logowania. Jest to ważne, ponieważ jest to pierwsze środowisko użytkownika z Twoją aplikacją.
* Zdecyduj, czy chcesz zezwolić użytkownikom na logowanie się tylko wtedy, gdy należą do organizacji. Jest to jedna aplikacja dzierżawy. Lub zezwól użytkownikom na logowanie się przy użyciu dowolnego konta służbowego. Jest to aplikacja wielodostępna. Możesz również zezwolić na osobiste konta Microsoft lub konto społecznościowe w serwisie LinkedIn, Google i tak dalej.
* Uprawnienia zakresu żądania. Na przykład można zażądać zakresu "User. Read", który przyznaje uprawnienia do odczytu profilu zalogowanego użytkownika.
* Zdefiniuj zakresy, które definiują dostęp do internetowego interfejsu API. Zwykle, gdy aplikacja chce uzyskać dostęp do interfejsu API, będzie musiała zażądać uprawnień do zdefiniowanych zakresów.
* Udostępnienie tajnej platformy tożsamości firmy Microsoft, która udowadnia tożsamość aplikacji.  Ma to zastosowanie w przypadku, gdy aplikacja jest poufną aplikacją kliencką. Poufna aplikacja kliencka to aplikacja, która umożliwia bezpieczne przechowywanie poświadczeń. Wymagają one zaufanego serwera wewnętrznej bazy danych do przechowywania poświadczeń.

Po zarejestrowaniu aplikacja będzie mieć unikatowy identyfikator, który jest udostępniany przez aplikację z platformą tożsamości firmy Microsoft podczas żądania tokenów. Jeśli aplikacja jest [poufną aplikacją kliencką](developer-glossary.md#client-application), będzie również współużytkować wpis tajny lub klucz publiczny * — w zależności od tego, czy zostały użyte certyfikaty lub wpisy tajne.

Platforma tożsamości firmy Microsoft reprezentuje aplikacje przy użyciu modelu, który spełnia dwie główne funkcje:

* Identyfikowanie aplikacji przez obsługiwane protokoły uwierzytelniania
* Podaj wszystkie identyfikatory, adresy URL, wpisy tajne i powiązane informacje, które są konieczne do uwierzytelnienia

Platforma tożsamości firmy Microsoft:

* Przechowuje wszystkie dane wymagane do obsługi uwierzytelniania w czasie wykonywania
* Program przechowuje wszystkie dane dotyczące podejmowania decyzji o zasobach, do których aplikacja może potrzebować, i w jakich okolicznościach należy spełnić dane żądanie.
* Zapewnia infrastrukturę do implementowania aprowizacji aplikacji w ramach dzierżawy dewelopera aplikacji oraz do dowolnej innej dzierżawy usługi Azure AD
* Obsługuje wyrażanie zgody użytkownika podczas żądania tokenu i ułatwia dynamiczne Inicjowanie obsługi aplikacji między dzierżawcami

Wyrażanie zgody polega na tym, że właściciel zasobu udziela autoryzacji dla aplikacji klienckiej w celu uzyskiwania dostępu do chronionych zasobów w ramach określonych uprawnień w imieniu właściciela zasobu. Platforma tożsamości firmy Microsoft:

* Umożliwia użytkownikom i administratorom dynamiczne wyrażanie zgody lub odmawianie zgody dla aplikacji na uzyskiwanie przez nią dostępu do zasobów w ich imieniu.
* Umożliwia administratorom ostateczne zdecydowanie, jakie działania mogą podejmować aplikacje, którzy użytkownicy mogą używać określonych aplikacji i w jaki sposób uzyskiwany jest dostęp do zasobów katalogu.

W programie Microsoft Identity platform [obiekt aplikacji](developer-glossary.md#application-object) opisuje aplikację. W czasie wdrażania platforma Microsoft Identity platform używa obiektu aplikacji jako planu do tworzenia jednostki [usługi](developer-glossary.md#service-principal-object), która reprezentuje konkretne wystąpienie aplikacji w katalogu lub dzierżawie. Jednostka usługi definiuje, co aplikacja może w rzeczywistości wykonać w konkretnym katalogu docelowym, kto może z niego korzystać, do jakich zasobów ma dostęp i tak dalej. Platforma tożsamości firmy Microsoft tworzy jednostkę usługi z obiektu aplikacji za pomocą **zgody**.

Na poniższym diagramie przedstawiono uproszczony przepływ aprowizacji platformy tożsamości firmy Microsoft, który jest oparty na wyrażaniu zgody. Pokazuje dwie dzierżawy *: a* i *B*.

* *Dzierżawca jest* właścicielem aplikacji.
* *Dzierżawca B* tworzy wystąpienie aplikacji za pośrednictwem nazwy głównej usługi.

![Uproszczony przepływ aprowizowania sterowany poprzez wyrażenie zgody](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

Ten przepływ aprowizowania składa się z następujących etapów:

1. Użytkownik z dzierżawy B próbuje zalogować się przy użyciu aplikacji, punkt końcowy autoryzacji żąda tokenu dla aplikacji.
1. Poświadczenia użytkownika są uzyskiwane i weryfikowane w celu uwierzytelnienia.
1. Użytkownik zostanie poproszony o podanie zgody dla aplikacji w celu uzyskania dostępu do dzierżawy B.
1. Platforma tożsamości firmy Microsoft używa obiektu aplikacji w dzierżawie A jako strategii tworzenia jednostki usługi w dzierżawie B.
1. Użytkownik otrzymuje żądany token.

Ten proces można powtórzyć w przypadku dodatkowych dzierżawców. Dzierżawca A zachowuje plan dla aplikacji (obiektu aplikacji). Użytkownicy i Administratorzy wszystkich innych dzierżawców, w których aplikacja otrzymuje zgodę, zachowują kontrolę nad tym, co aplikacja może wykonać za pośrednictwem odpowiedniego obiektu głównego usługi w każdej dzierżawie. Aby uzyskać więcej informacji, zobacz [obiekty główne aplikacji i usług na platformie tożsamości firmy Microsoft](app-objects-and-service-principals.md).

## <a name="web-app-sign-in-flow-with-microsoft-identity-platform"></a>Przepływ logowania aplikacji sieci Web przy użyciu platformy tożsamości firmy Microsoft

Gdy użytkownik nawiguje w przeglądarce do aplikacji sieci Web, następuje:

* Aplikacja sieci Web określa, czy użytkownik jest uwierzytelniany.
* Jeśli użytkownik nie jest uwierzytelniony, aplikacja internetowa deleguje do usługi Azure AD, aby zalogować użytkownika. Ten proces logowania będzie zgodny z zasadami organizacji, co może oznaczać, że użytkownik musi wprowadzić poświadczenia przy użyciu uwierzytelniania wieloskładnikowego lub nie używać w ogóle hasła (na przykład korzystania z usługi Windows Hello).
* Użytkownik zostanie poproszony o zgodę na dostęp wymagany przez aplikację kliencką. Dlatego aplikacje klienckie muszą być zarejestrowane w usłudze Azure AD, dzięki czemu platforma tożsamości firmy Microsoft może dostarczać tokeny reprezentujące dostęp, do którego użytkownik wyraził zgodę.

Po pomyślnym uwierzytelnieniu użytkownika:

* Platforma tożsamości firmy Microsoft wysyła token do aplikacji sieci Web.
* Plik cookie jest zapisywany, skojarzony z domeną usługi Azure AD, która zawiera tożsamość użytkownika w pliku JAR cookie w przeglądarce. Następnym razem, gdy aplikacja będzie korzystać z przeglądarki w celu przejścia do punktu końcowego autoryzacji platformy tożsamości firmy Microsoft, przeglądarka prezentuje plik cookie, dzięki czemu użytkownik nie musi zalogować się ponownie. Jest to również sposób, w jaki uzyskuje się Logowanie jednokrotne. Plik cookie jest tworzony przez usługę Azure AD i może być zrozumiały tylko dla usługi Azure AD.
* Następnie aplikacja internetowa sprawdza poprawność tokenu. Jeśli sprawdzanie poprawności zakończy się pomyślnie, aplikacja sieci Web wyświetli chronioną stronę i zapisze plik cookie sesji w pliku cookie w przeglądarce. Gdy użytkownik nawiguje do innej strony, aplikacja sieci Web wie, że użytkownik jest uwierzytelniany w oparciu o plik cookie sesji.

Poniższy diagram sekwencji podsumowuje tę interakcję:

![proces uwierzytelniania aplikacji sieci Web](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Jak aplikacja internetowa decyduje o tym, czy użytkownik jest uwierzytelniony

Deweloperzy aplikacji sieci Web mogą wskazać, czy wszystkie lub tylko niektóre strony wymagają uwierzytelniania. Na przykład w ASP.NET/ASP.NET rdzeń jest to wykonywane przez dodanie `[Authorize]` atrybutu do akcji kontrolera.

Ten atrybut powoduje, że ASP.NET sprawdza obecność pliku cookie sesji zawierającego tożsamość użytkownika. Jeśli plik cookie nie istnieje, ASP.NET przekierowuje uwierzytelnianie do określonego dostawcy tożsamości. Jeśli dostawca tożsamości to usługa Azure AD, aplikacja sieci Web przekierowuje uwierzytelnianie do `https://login.microsoftonline.com`programu, co spowoduje wyświetlenie okna dialogowego logowania.

### <a name="how-a-web-app-delegates-sign-in-to-microsoft-identity-platform-and-obtains-a-token"></a>Jak aplikacja sieci Web deleguje logowanie do platformy tożsamości firmy Microsoft i uzyskuje token

Uwierzytelnianie użytkowników odbywa się za pośrednictwem przeglądarki. Protokół OpenID Connect używa standardowych komunikatów protokołu HTTP.

* Aplikacja sieci Web wysyła HTTP 302 (przekierowanie) do przeglądarki w celu korzystania z platformy tożsamości firmy Microsoft.
* Po uwierzytelnieniu użytkownika platforma tożsamości firmy Microsoft wysyła token do aplikacji sieci Web przy użyciu przekierowania za pośrednictwem przeglądarki.
* Przekierowanie jest dostarczane przez aplikację internetową w formie identyfikatora URI przekierowania. Ten identyfikator URI przekierowania jest zarejestrowany w obiekcie aplikacji usługi Azure AD. Może istnieć kilka identyfikatorów URI przekierowania, ponieważ aplikacja może zostać wdrożona w kilku adresach URL. W związku z tym aplikacja sieci Web będzie również musiała określić identyfikator URI przekierowania, który ma być używany.
* Usługa Azure AD weryfikuje, czy identyfikator URI przekierowania Wysłany przez aplikację sieci Web jest jednym z zarejestrowanych identyfikatorów URI przekierowania dla aplikacji.

## <a name="desktop-and-mobile-app-sign-in-flow-with-microsoft-identity-platform"></a>Przepływ logowania aplikacji klasycznych i mobilnych przy użyciu platformy tożsamości firmy Microsoft

Opisany powyżej przepływ ma zastosowanie z niewielkimi różnicami w aplikacjach komputerowych i mobilnych.

Aplikacje klasyczne i mobilne mogą korzystać z osadzonego formantu sieci Web lub przeglądarki systemowej w celu uwierzytelniania. Na poniższym diagramie przedstawiono, w jaki sposób aplikacja klasyczna lub mobilna używa biblioteki uwierzytelniania firmy Microsoft (MSAL) w celu uzyskania tokenów dostępu i wywołania interfejsów API sieci Web.

![Aplikacja klasyczna, która wygląda](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL używa przeglądarki do uzyskiwania tokenów. Podobnie jak w przypadku aplikacji sieci Web, uwierzytelnianie jest delegowane do platformy tożsamości firmy Microsoft.

Ponieważ usługa Azure AD zapisuje ten sam plik cookie tożsamości w przeglądarce, jak w przypadku aplikacji sieci Web, jeśli aplikacja natywna lub mobilna korzysta z przeglądarki systemu, natychmiast otrzyma Logowanie jednokrotne przy użyciu odpowiedniej aplikacji sieci Web.

Domyślnie MSAL używa przeglądarki systemowej. Wyjątkiem są .NET Framework aplikacje pulpitu, w których osadzony formant służy do zapewnienia bardziej zintegrowanego środowiska użytkownika.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się ze [słownikiem Microsoft Identity platform Developer](developer-glossary.md) , aby zapoznać się z typowymi terminami.
* Zobacz [przepływy uwierzytelniania i scenariusze aplikacji](authentication-flows-app-scenarios.md) , aby dowiedzieć się więcej o innych scenariuszach uwierzytelniania użytkowników obsługiwanych przez platformę tożsamości firmy Microsoft.
* Zobacz [biblioteki MSAL](msal-overview.md) , aby dowiedzieć się więcej o bibliotekach firmy Microsoft, które ułatwiają tworzenie aplikacji współpracujących z kontami Microsoft, kontami usługi Azure AD i Azure AD B2C użytkowników w jednym, usprawnionym modelu programowania.
* Aby dowiedzieć się, jak skonfigurować uwierzytelnianie dla aplikacji App Service, zobacz [integrowanie App Service z platformą tożsamości firmy Microsoft](/azure/app-service/configure-authentication-provider-aad) .
