---
title: Uwierzytelnianie na platformie tożsamości firmy Microsoft | Azure
description: Dowiedz się więcej o podstawach uwierzytelniania na platformie tożsamości firmy Microsoft (wersja 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 5252fdbbaf425662fc9725e618f8fc450b435722
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534656"
---
# <a name="authentication-basics"></a>Podstawowe informacje o uwierzytelnianiu

## <a name="what-is-authentication"></a>Co to jest uwierzytelnianie

W tym artykule omówiono wiele pojęć uwierzytelniania, które należy zrozumieć, aby utworzyć chronione aplikacje sieci web, internetowe interfejsy API lub aplikacje wywołujące chronione interfejsy API sieci Web. Jeśli widzisz termin, którego nie znasz, wypróbuj nasz [słowniczek](developer-glossary.md) lub nasze filmy z [platformy tożsamości firmy Microsoft,](identity-videos.md) które obejmują podstawowe pojęcia.

**Uwierzytelnianie** to proces udowadniania, że jesteś tym, kim mówisz, że jesteś. Uwierzytelnianie jest czasami określane terminem AuthN.

**Autoryzacja** jest aktem udzielenia uwierzytelnionej zgody na zrobienie czegoś. Określa, do jakich danych masz dostęp i co można zrobić z tymi danymi. Autoryzacja jest czasami określana terminem AuthZ.

Zamiast tworzyć aplikacje, które przechowują własne informacje o nazwie użytkownika i hasła, co wiąże się z dużym obciążeniem administracyjnym, gdy trzeba dodać lub usunąć użytkowników w wielu aplikacjach, aplikacje mogą delegować tę odpowiedzialność do scentralizowanego dostawcy tożsamości.

Usługa Azure Active Directory (Azure AD) jest scentralizowanym dostawcą tożsamości w chmurze. Delegowanie uwierzytelniania i autoryzacji do niego umożliwia scenariusze, takie jak zasady dostępu warunkowego, które wymagają, aby użytkownik był w określonej lokalizacji, korzystanie z uwierzytelniania wieloskładnikowego, a także umożliwiając użytkownikowi zalogowanie się raz, a następnie automatycznie zalogowany do wszystkich aplikacji sieci web, które współużytkują ten sam scentralizowany katalog. Ta funkcja jest określana jako logowanie jednokrotne(Logowanie jednokrotne).

Scentralizowany dostawca tożsamości jest jeszcze ważniejszy dla aplikacji, które mają użytkowników znajdujących się na całym świecie, którzy niekoniecznie logują się z sieci przedsiębiorstwa. Usługa Azure AD uwierzytelnia użytkowników i udostępnia tokeny dostępu. [Token dostępu](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#access-token) jest tokenem zabezpieczającym wystawionym przez serwer autoryzacji. Zawiera informacje o użytkowniku i aplikacji, dla której token jest przeznaczony; które mogą być używane do uzyskiwania dostępu do interfejsów API sieci web i innych chronionych zasobów.

Platforma tożsamości firmy Microsoft upraszcza uwierzytelnianie dla deweloperów aplikacji, zapewniając tożsamość jako usługę, obsługując protokoły standardowe branżowe, takie jak [OAuth 2.0](https://oauth.net/2/) i [OpenID Connect,](https://openid.net/connect/)a także biblioteki typu open source dla różnych platform, które ułatwią szybkie rozpoczęcie kodowania. Umożliwia deweloperom tworzenie aplikacji, które logują się we wszystkich tożsamościach firmy Microsoft, pobierz tokeny do wywołania [programu Microsoft Graph,](https://developer.microsoft.com/graph/)innych interfejsów API firmy Microsoft lub interfejsów API utworzonych przez deweloperów. Aby uzyskać więcej informacji, zobacz [Ewolucja platformy tożsamości firmy Microsoft](about-microsoft-identity-platform.md).

### <a name="tenants"></a>Dzierżawy

Dostawca tożsamości w chmurze obsługuje wiele organizacji. Aby użytkownicy z różnych organizacji były oddzielone, usługa Azure AD jest podzielona na jednostki dzierżawy z jedną dzierżawą na organizację.

Dzierżawcy śledzą użytkowników i powiązane z nimi aplikacje. Platforma tożsamości firmy Microsoft obsługuje również użytkowników logucha, którzy logują się za pomocą osobistych kont Microsoft.

Usługa Azure AD udostępnia również usługę Azure Active Directory B2C, dzięki czemu organizacje mogą logować się do użytkowników, zazwyczaj klientów, przy użyciu tożsamości społecznościowych, takich jak konto Google. Aby uzyskać więcej informacji, zobacz [dokumentację usługi Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c) .

### <a name="security-tokens"></a>Tokeny zabezpieczające

Tokeny zabezpieczające zawierają informacje o użytkownikach i aplikacjach. Usługa Azure AD używa tokenów opartych na JSON (JWTs), które zawierają oświadczenia.

Oświadczenie zawiera potwierdzenia dotyczące jednej jednostki, takiej jak [aplikacja kliencka](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application) lub [właściciel zasobu,](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-owner)innej encji, takiej jak [serwer zasobów.](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-server)

Oświadczenia to pary nazw i wartości, które przekazują fakty dotyczące tematu tokenu. Na przykład oświadczenie może zawierać fakty dotyczące podmiotu zabezpieczeń, który został uwierzytelniony przez [serwer autoryzacji](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#authorization-server). Oświadczenia obecne w danym tokenie zależą od wielu rzeczy, w tym typu tokenu, typu poświadczenia używanego do uwierzytelniania podmiotu, konfiguracji aplikacji i tak dalej.

Aplikacje mogą używać oświadczeń do różnych zadań, takich jak:

* Sprawdzanie poprawności tokenu
* Identyfikowanie dzierżawy podmiotu tokenu
* Wyświetlanie informacji o użytkowniku
* Określanie upoważnienia uczestnika

Oświadczenie składa się z par klucz-wartość, które zawierają informacje, takie jak:

* Serwer tokenów zabezpieczających, który wygenerował token
* Data wygenerowania tokenu
* Temat (np. użytkownik — z wyjątkiem demonów)
* Publiczność, czyli aplikacja, dla której token został wygenerowany
* Aplikacja (klient), który poprosił o token. W przypadku aplikacji internetowych może to być takie samo, jak

Aby uzyskać bardziej szczegółowe informacje o roszczeniach, zobacz [tokeny dostępu](access-tokens.md) i [tokeny identyfikatorów](id-tokens.md).

To zależy od aplikacji, dla której został wygenerowany token, aplikacji sieci web, która zalogowała się użytkownika lub wywoływanych w sieci Web interfejsu API, aby sprawdzić poprawność tokenu. Token jest podpisywane przez serwer tokenów zabezpieczających (STS) za pomocą klucza prywatnego. STS publikuje odpowiedni klucz publiczny. Aby sprawdzić poprawność tokenu, aplikacja weryfikuje podpis przy użyciu klucza publicznego STS, aby sprawdzić, czy podpis został utworzony przy użyciu klucza prywatnego.

Tokeny są ważne tylko przez ograniczony czas. Zazwyczaj STS udostępnia parę tokenów: token dostępu, aby uzyskać dostęp do aplikacji lub chronionego zasobu i token odświeżania używany do odświeżania tokenu dostępu, gdy token dostępu jest bliski wygaśnięcia.

Tokeny dostępu są przekazywane do internetowego interfejsu `Authorization` API jako token nośnika w nagłówku. Aplikacja może zapewnić token odświeżania do STS, a jeśli dostęp użytkownika do aplikacji nie został odwołany, zostanie ono odzyskać nowy token dostępu i nowy token odświeżania. W ten sposób obsługiwany jest scenariusz, w jaki osoba opuszczająca przedsiębiorstwo jest obsługiwana. Gdy STS odbiera token odświeżania, nie wystawi innego prawidłowego tokenu dostępu, jeśli użytkownik nie jest już autoryzowany.

### <a name="how-each-flow-emits-tokens-and-codes"></a>Jak każdy przepływ emituje tokeny i kody

W zależności od sposobu zbudowanego klienta można użyć jednego (lub kilku) przepływów uwierzytelniania obsługiwanych przez usługę Azure AD. Przepływy te mogą tworzyć różne tokeny (id_tokens, odświeżanie tokenów, tokeny dostępu), a także kody autoryzacji i wymagają różnych tokenów, aby je działały. Ten wykres zawiera omówienie:

|Ruch | Wymaga | id_token | token dostępu | token odświeżania | kod autoryzacyjny |
|-----|----------|----------|--------------|---------------|--------------------|
|[Przepływ kodu autoryzacji](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[Niejawny przepływ](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybrydowy przepływ OIDC](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Odświeżanie wykorzystania tokenu](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | token odświeżania | x | x | x| |
|[Przepływ „w imieniu”](v2-oauth2-on-behalf-of-flow.md) | token dostępu| x| x| x| |
|[Poświadczenia klienta](v2-oauth2-client-creds-grant-flow.md) | | | x (tylko aplikacja)| | |

Tokeny wystawione w trybie niejawnym mają ograniczenie długości ze względu na `response_mode` `query` przekazywanie z powrotem do przeglądarki za pośrednictwem adresu URL (gdzie jest lub `fragment`).  Niektóre przeglądarki mają limit rozmiaru adresu URL, który można umieścić na pasku przeglądarki i zakończyć się niepowodzeniem, gdy jest zbyt długi.  W związku z tym `groups` tokeny te nie mają lub `wids` roszczeń.

Teraz, gdy masz omówienie podstaw, przeczytaj dalej, aby zrozumieć model aplikacji tożsamości i interfejs API, dowiedz się, jak działa inicjowanie obsługi administracyjnej w usłudze Azure AD i uzyskaj łącza do szczegółowych informacji na temat typowych scenariuszy, które obsługuje usługa Azure AD.

## <a name="application-model"></a>Model aplikacji

Aplikacje mogą samodzielnie logować się do użytkowników lub delegować logowanie do dostawcy tożsamości. Zobacz [Przepływy uwierzytelniania i scenariusze aplikacji,](authentication-flows-app-scenarios.md) aby dowiedzieć się więcej o scenariuszach logowania obsługiwanych przez usługę Azure AD.

Aby dostawca tożsamości wiedział, że użytkownik ma dostęp do określonej aplikacji, zarówno użytkownik, jak i aplikacja muszą być zarejestrowane u dostawcy tożsamości. Podczas rejestrowania aplikacji w usłudze Azure AD udostępniasz konfigurację tożsamości dla aplikacji, która umożliwia jej integrację z usługą Azure AD. Rejestracja aplikacji pozwala również na:

* Dostosuj znakowanie aplikacji w oknie dialogowym logowania. Jest to ważne, ponieważ jest to pierwsze środowisko, które użytkownik będzie miał z aplikacją.
* Zdecyduj, czy chcesz umożliwić użytkownikom logowanie się tylko wtedy, gdy należą do Twojej organizacji. Jest to aplikacja pojedynczej dzierżawy. Możesz też zezwolić użytkownikom na logowanie się przy użyciu dowolnego konta służbowego. Jest to aplikacja wielodostępna. Możesz też zezwolić na osobiste konta Microsoft lub konto społecznościowe z LinkedIn, Google itd.
* Żądania uprawnień zakresu. Na przykład można zażądać zakresu "user.read", który udziela uprawnień do odczytu profilu zalogowanego użytkownika.
* Zdefiniuj zakresy definiujące dostęp do internetowego interfejsu API. Zazwyczaj, gdy aplikacja chce uzyskać dostęp do interfejsu API, będzie musiał poprosić o uprawnienia do zakresów, które definiujesz.
* Udostępnij klucz tajny za pomocą usługi Azure AD, który potwierdza tożsamość aplikacji w usłudze Azure AD.  Jest to istotne w przypadku, gdy aplikacja jest poufną aplikacją kliencką. Aplikacja klienta poufne jest aplikacją, która może przechowywać poświadczenia bezpiecznie. Wymagają one zaufanego serwera wewnętrznej bazy danych do przechowywania poświadczeń.

Po zarejestrowaniu aplikacja otrzyma unikatowy identyfikator, który aplikacja udostępnia usłudze Azure AD, gdy żąda tokenów. Jeśli aplikacja jest [aplikacją klienta poufnego,](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application)będzie również udostępniać klucz tajny lub klucz publiczny*-w zależności od tego, czy użyto certyfikatów lub wpisów tajnych.

Platforma tożsamości firmy Microsoft reprezentuje aplikacje przy użyciu modelu, który spełnia dwie główne funkcje:

* Identyfikowanie aplikacji za pomocą protokołów uwierzytelniania, które obsługuje
* Podaj wszystkie identyfikatory, adresy URL, wpisy tajne i powiązane informacje potrzebne do uwierzytelnienia

Platforma tożsamości firmy Microsoft:

* Przechowuje wszystkie dane wymagane do obsługi uwierzytelniania w czasie wykonywania
* Przechowuje wszystkie dane w celu podjęcia decyzji, jakie zasoby aplikacja może potrzebować uzyskać dostęp i w jakich okolicznościach dane żądanie powinno zostać spełnione
* Zapewnia infrastrukturę do implementowania inicjowania obsługi administracyjnej aplikacji w dzierżawie dewelopera aplikacji i do dowolnej innej dzierżawy usługi Azure AD
* Obsługuje zgodę użytkownika w czasie żądania tokenu i ułatwia dynamiczne aprowizacji aplikacji w dzierżawach

Zgoda to proces uzyskiwania przez właściciela zasobu autoryzacji dla aplikacji klienckiej w celu uzyskania dostępu do chronionych zasobów w ramach określonych uprawnień w imieniu właściciela zasobu. Platforma tożsamości firmy Microsoft:

* Umożliwia użytkownikom i administratorom dynamiczne wyrażanie zgody lub odmawianie zgody dla aplikacji na uzyskiwanie przez nią dostępu do zasobów w ich imieniu.
* Umożliwia administratorom ostateczne zdecydowanie, jakie działania mogą podejmować aplikacje, którzy użytkownicy mogą używać określonych aplikacji i w jaki sposób uzyskiwany jest dostęp do zasobów katalogu.

Na platformie tożsamości firmy Microsoft [obiekt aplikacji](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-object) opisuje aplikację. W czasie wdrażania platforma tożsamości firmy Microsoft używa obiektu aplikacji jako planu do utworzenia [jednostki usługi,](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#service-principal-object)która reprezentuje konkretne wystąpienie aplikacji w katalogu lub dzierżawie. Podmiot usługi definiuje, co aplikacja może faktycznie zrobić w określonym katalogu docelowym, kto może go używać, jakie zasoby ma dostęp do i tak dalej. Platforma tożsamości firmy Microsoft tworzy jednostkę usługi z obiektu aplikacji **za**zgodą.

Na poniższym diagramie przedstawiono uproszczony przepływ inicjowania obsługi administracyjnej platformy tożsamości firmy Microsoft, oparty na zgodzie. Pokazuje dwóch dzierżawców: A i B. Dzierżawca A jest właścicielem aplikacji. Dzierżawa B jest tworzenie wystąpienia aplikacji za pośrednictwem jednostki usługi.

![Uproszczony przepływ aprowizowania sterowany poprzez wyrażenie zgody](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

Ten przepływ aprowizowania składa się z następujących etapów:

1. Użytkownik z dzierżawy B próbuje zalogować się za pomocą aplikacji, punkt końcowy autoryzacji żąda tokenu dla aplikacji.
1. Poświadczenia użytkownika są pobierane i weryfikowane do uwierzytelniania.
1. Użytkownik jest monitowany o wyrażenie zgody na uzyskanie dostępu do dzierżawy B przez aplikację.
1. Platforma tożsamości firmy Microsoft używa obiektu aplikacji w dzierżawie A jako planu tworzenia jednostki usługi w dzierżawie B.
1. Użytkownik otrzymuje żądany token.

Można powtórzyć ten proces dla dodatkowych dzierżaw. Dzierżawa A zachowuje plan aplikacji (obiekt aplikacji). Użytkownicy i administratorzy wszystkich innych dzierżaw, gdzie aplikacja jest udzielana zgoda zachować kontrolę nad tym, co aplikacja może zrobić za pośrednictwem odpowiedniego obiektu jednostki usługi w każdej dzierżawy. Aby uzyskać więcej informacji, zobacz [Obiekty głównej aplikacji i usługi na platformie tożsamości firmy Microsoft](app-objects-and-service-principals.md).

## <a name="web-app-sign-in-flow-with-azure-ad"></a>Przepływ logowania aplikacji sieci Web za pomocą usługi Azure AD

Gdy użytkownik przechodzi w przeglądarce do aplikacji sieci web, dzieje się tak:

* Aplikacja internetowa określa, czy użytkownik jest uwierzytelniony.
* Jeśli użytkownik nie jest uwierzytelniony, aplikacja sieci web deleguje do usługi Azure AD, aby zalogować się do użytkownika. To logowanie będzie zgodne z zasadami organizacji, co może oznaczać prośbę użytkownika o wprowadzenie poświadczeń, przy użyciu uwierzytelniania wieloskładnikowego lub nieużywanie hasła w ogóle (na przykład przy użyciu funkcji Windows Hello).
* Użytkownik jest proszony o wyrażenie zgody na dostęp, który aplikacja kliencka potrzebuje. Dlatego aplikacje klienckie muszą być zarejestrowane w usłudze Azure AD, aby usługa Azure AD mogła dostarczać tokeny reprezentujące dostęp, na który użytkownik wyraził zgodę.

Po pomyślnym uwierzytelnieniu użytkownika:

* Usługa Azure AD wysyła token do aplikacji sieci web.
* Plik cookie jest zapisywany, skojarzony z domeną usługi Azure AD, który zawiera tożsamość użytkownika w pliku cookie przeglądarki. Następnym razem, gdy aplikacja używa przeglądarki, aby przejść do punktu końcowego autoryzacji usługi Azure AD, przeglądarka prezentuje plik cookie, dzięki czemu użytkownik nie musi ponownie się zalogować. Jest to również sposób, w jaki uzyskuje się sytą łatę. Plik cookie jest produkowany przez usługę Azure AD i może być rozumiany tylko przez usługę Azure AD.
* Aplikacja internetowa następnie sprawdza poprawność tokenu. Jeśli sprawdzanie poprawności zakończy się pomyślnie, aplikacja internetowa wyświetla chroniona strona i zapisuje plik cookie sesji w pliku cookie przeglądarki. Gdy użytkownik przechodzi do innej strony, aplikacja sieci web wie, że użytkownik jest uwierzytelniony na podstawie pliku cookie sesji.

Poniższy diagram sekwencji podsumowuje tę interakcję:

![proces uwierzytelniania aplikacji sieci Web](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Jak aplikacja internetowa określa, czy użytkownik jest uwierzytelniony

Deweloperzy aplikacji sieci Web mogą wskazać, czy wszystkie lub tylko niektóre strony wymagają uwierzytelniania. Na przykład w ASP.NET/ASP.NET Core odbywa się to `[Authorize]` przez dodanie atrybutu do akcji kontrolera.

Ten atrybut powoduje, ASP.NET, aby sprawdzić obecność pliku cookie sesji zawierającego tożsamość użytkownika. Jeśli plik cookie nie jest obecny, ASP.NET przekierowuje uwierzytelnianie do określonego dostawcy tożsamości. Jeśli dostawcą tożsamości jest usługa Azure AD, `https://login.microsoftonline.com`aplikacja sieci web przekierowuje uwierzytelnianie do , który wyświetla okno dialogowe logowania.

### <a name="how-a-web-app-delegates-sign-in-to-azure-ad-and-obtains-a-token"></a>Jak aplikacja sieci web deleguje logowanie do usługi Azure AD i uzyskuje token

Uwierzytelnianie użytkownika odbywa się za pośrednictwem przeglądarki. Protokół OpenID używa standardowych komunikatów protokołu HTTP.
* Aplikacja sieci web wysyła HTTP 302 (przekierowanie) do przeglądarki do korzystania z usługi Azure AD.
* Gdy użytkownik jest uwierzytelniony, usługa Azure AD wysyła token do aplikacji sieci web przy użyciu przekierowania za pośrednictwem przeglądarki.
* Przekierowanie jest dostarczane przez aplikację sieci web w postaci identyfikatora URI przekierowania. Ten identyfikator URI przekierowania jest zarejestrowany w obiekcie aplikacji usługi Azure AD. Może istnieć kilka identyfikatorów URL przekierowania, ponieważ aplikacja może zostać wdrożona przy kilku adresach URL. Dlatego aplikacja sieci web będzie również musiał określić identyfikator URI przekierowania do użycia.
* Usługa Azure AD sprawdza, czy identyfikator URI przekierowania wysyłany przez aplikację sieci web jest jednym z zarejestrowanych identyfikatorów URI przekierowania dla aplikacji.

## <a name="desktop-and-mobile-app-sign-in-flow-with-azure-ad"></a>Przepływ logowania do aplikacji komputerowej i mobilnej za pomocą usługi Azure AD

Opisany powyżej przepływ ma zastosowanie, z niewielkimi różnicami, do aplikacji komputerowych i mobilnych.

Aplikacje komputerowe i mobilne mogą używać wbudowanej kontrolki sieci Web lub przeglądarki systemowej do uwierzytelniania. Na poniższym diagramie pokazano, jak aplikacja komputerowa lub mobilna używa biblioteki uwierzytelniania firmy Microsoft (MSAL) do uzyskiwania tokenów dostępu i wywoływania interfejsów API sieci Web.

![Aplikacja komputerowa, jak to wygląda](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL używa przeglądarki, aby uzyskać tokeny. Podobnie jak w przypadku aplikacji sieci web, uwierzytelnianie jest delegowane do usługi Azure AD.

Ponieważ usługa Azure AD zapisuje ten sam plik cookie tożsamości w przeglądarce, jak w przypadku aplikacji sieci web, jeśli aplikacja natywna lub mobilna korzysta z przeglądarki systemowej, natychmiast pobierze jednocześnie z odpowiednią aplikacją sieci web.

Domyślnie msal używa przeglądarki systemowej. Wyjątkiem są aplikacje klasyczne platformy .NET Framework, w których wbudowany formant jest używany w celu zapewnienia bardziej zintegrowanego środowiska użytkownika.

## <a name="next-steps"></a>Następne kroki

* Zapoznaj się z [glosariuszem dewelopera platformy tożsamości firmy Microsoft,](developer-glossary.md) aby zapoznać się ze wspólnymi terminami.
* Zobacz [Przepływy uwierzytelniania i scenariusze aplikacji,](authentication-flows-app-scenarios.md) aby dowiedzieć się więcej o innych scenariuszach uwierzytelniania użytkowników obsługiwanych przez platformę tożsamości firmy Microsoft.
* Zobacz [biblioteki MSAL,](msal-overview.md) aby dowiedzieć się więcej o bibliotekach firmy Microsoft, które ułatwiają tworzenie aplikacji, które współpracują z kontami Microsoft, kontami usługi Azure AD i użytkownikami usługi Azure AD B2C w jednym, usprawnionym modelu programowania.
* Zobacz [Integrowanie usługi App Service z platformą tożsamości firmy Microsoft,](/azure/app-service/configure-authentication-provider-aad) aby dowiedzieć się, jak skonfigurować uwierzytelnianie aplikacji usługi App Service.
