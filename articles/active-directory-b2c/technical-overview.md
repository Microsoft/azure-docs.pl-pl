---
title: Omówienie techniczne i funkcje — Azure Active Directory B2C
description: Szczegółowe wprowadzenie do funkcji i technologii w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 519e2da902e1121220bcbb28c4cb18a68001293b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94840557"
---
# <a name="technical-and-feature-overview-of-azure-active-directory-b2c"></a>Omówienie techniczne i funkcje Azure Active Directory B2C

Pomocnik do [Azure Active Directory B2C](overview.md), ten artykuł zawiera bardziej szczegółowe wprowadzenie do usługi. Omówiono w nim podstawowe zasoby, z którymi pracują usługi, jej funkcje oraz sposoby zapewniania w pełni niestandardowej obsługi tożsamości dla klientów w aplikacjach.

## <a name="azure-ad-b2c-tenant"></a>Dzierżawa Azure AD B2C

W Azure Active Directory B2C (Azure AD B2C) *dzierżawca* reprezentuje organizację i jest katalogiem użytkowników. Każda dzierżawa usługi Azure AD B2C jest unikatowa i oddzielona od innych dzierżaw usługi Azure AD B2C. Dzierżawa Azure AD B2Ca jest inna niż dzierżawa Azure Active Directory, która może już istnieć.

Podstawowe zasoby, z którymi pracujesz w dzierżawie Azure AD B2C są następujące:

* **Katalog** — *katalog* jest miejscem, w którym Azure AD B2C przechowuje poświadczenia użytkowników i dane profilu, a także rejestracje aplikacji.
* **Rejestracje aplikacji** — rejestrujesz aplikacje internetowe, mobilne i natywne za pomocą Azure AD B2C, aby umożliwić zarządzanie tożsamościami. Ponadto wszystkie interfejsy API, które mają być chronione za pomocą Azure AD B2C.
* **Przepływy użytkowników** i **zasady niestandardowe** — wbudowana (przepływy użytkowników) i w pełni dostosowywalne (zasady niestandardowe) środowiska tożsamości dla aplikacji.
  * *Przepływy użytkowników* umożliwiają szybką konfigurację i obsługę typowych zadań związanych z tożsamościami, takich jak rejestrowanie, logowanie i edytowanie profilów.
  * Użyj *zasad niestandardowych* , aby włączyć środowisko użytkownika nie tylko dla typowych zadań związanych z tożsamościami, ale również w celu uzyskania pomocy technicznej dla złożonych przepływów pracy tożsamości, które są unikatowe dla Twojej organizacji, klientów, pracowników, partnerów i obywateli.
* **Dostawcy tożsamości** — ustawienia Federacji dla:
  * Dostawcy tożsamości *społecznościowej* , takich jak Facebook, LinkedIn lub Twitter, które mają być obsługiwane w aplikacjach.
  * *Zewnętrzni* dostawcy tożsamości, którzy obsługują standardowe protokoły tożsamości, takie jak OAuth 2,0, OpenID Connect Connect i inne.
  * Konta *lokalne* , które umożliwiają użytkownikom rejestrowanie się i logowanie przy użyciu nazwy użytkownika (lub adresu e-mail lub innego identyfikatora) i hasła.
* **Klucze** — Dodawanie kluczy szyfrowania do podpisywania i weryfikowania tokenów, kluczy tajnych klienta, certyfikatów i haseł oraz zarządzanie nimi.

Dzierżawa Azure AD B2C to pierwszy zasób, który należy utworzyć, aby rozpocząć pracę z Azure AD B2C. Dowiedz się [, jak w samouczku: Tworzenie dzierżawy Azure Active Directory B2C](tutorial-create-tenant.md).

## <a name="accounts-in-azure-ad-b2c"></a>Konta w Azure AD B2C

Azure AD B2C definiuje kilka typów kont użytkowników. Azure Active Directory, Azure Active Directory B2B i Azure Active Directory B2C udostępniać te typy kont.

* **Konto służbowe** — użytkownicy z kontami służbowymi mogą zarządzać zasobami w dzierżawie, a rola administratora może również zarządzać dzierżawcami. Użytkownicy z kontami służbowymi mogą tworzyć nowe konta konsumentów, resetować hasła, blokować/odblokować konta i ustawiać uprawnienia lub przypisywać konto do grupy zabezpieczeń.
* **Konto gościa** — Użytkownicy Zewnętrzni zaproszeni do Twojej dzierżawy jako Goście. Typowym scenariuszem zapraszania użytkownika-gościa do dzierżawy usługi Azure AD B2C jest udostępnianie obowiązków administracyjnych.
* **Konta konsumentów** — konta użytkowników są kontami utworzonymi w katalogu Azure AD B2C, gdy użytkownicy ukończyją podróż użytkownika podczas rejestracji w aplikacji zarejestrowanej w dzierżawie.

![Azure AD B2C strony zarządzania użytkownikami w Azure Portal](media/technical-overview/portal-01-users.png)<br/>*Rysunek: Katalog użytkownika w ramach dzierżawy Azure AD B2C w Azure Portal*

### <a name="consumer-accounts"></a>Konta konsumentów

Przy użyciu konta *odbiorcy* użytkownicy mogą logować się do aplikacji, które zostały zabezpieczone za pomocą Azure AD B2C. Użytkownicy z kontami konsumentów nie mogą jednak uzyskać dostępu do zasobów platformy Azure, na przykład Azure Portal.

Konto konsumenta może być skojarzone z tymi typami tożsamości:

* **Lokalna** tożsamość wraz z nazwą użytkownika i hasłem przechowywanym lokalnie w katalogu Azure AD B2C. Często odwołują się do tych tożsamości jako "konta lokalne".
* Tożsamości **społecznościowe** i **korporacyjne** , w których tożsamość użytkownika jest zarządzana przez federacyjnego dostawcę tożsamości, takiego jak Facebook, Microsoft, ADFS lub Salesforce.

Użytkownik z kontem konsumenta może zalogować się przy użyciu wielu tożsamości, na przykład username, email, ID pracownika, identyfikatora rządu i innych. Pojedyncze konto może mieć wiele tożsamości, zarówno lokalnych, jak i społecznościowych.

![Tożsamości konta odbiorcy](media/technical-overview/identities.png)<br/>*Rysunek: jedno konto konsumenta z wieloma tożsamościami w Azure AD B2C*

Azure AD B2C umożliwia zarządzanie typowymi atrybutami profilów kont odbiorców, takimi jak nazwa wyświetlana, nazwisko, imię i nazwisko, miasto i inne. Możesz również zwiększyć schemat usługi Azure AD w celu przechowywania dodatkowych informacji o użytkownikach. Na przykład ich kraj/region lub zamieszkanie, preferowany język i preferencje, takie jak zasubskrybowanie biuletynu lub włączenie uwierzytelniania wieloskładnikowego.

Dowiedz się więcej o typach kont użytkowników w Azure AD B2C w temacie [Omówienie kont użytkowników w programie Azure Active Directory B2C](user-overview.md).

## <a name="external-identity-providers"></a>Zewnętrzni dostawcy tożsamości

Można skonfigurować Azure AD B2C, aby umożliwić użytkownikom logowanie się do aplikacji przy użyciu poświadczeń od zewnętrznych dostawców tożsamości społecznościowych lub korporacyjnych (dostawcy tożsamości). Azure AD B2C obsługuje zewnętrznych dostawców tożsamości, takich jak Facebook, konto Microsoft, Google, Twitter i dowolny dostawca tożsamości, który obsługuje protokół OAuth 1,0, OAuth 2,0, OpenID Connect Connect i protokołu SAML.

![Zewnętrzni dostawcy tożsamości](media/technical-overview/external-idps.png)

Za pomocą Federacji zewnętrznego dostawcy tożsamości możesz zaoferować swoim użytkownikom możliwość zalogowania się przy użyciu istniejących kont społecznościowych lub firmowych bez konieczności tworzenia nowego konta tylko dla aplikacji.

Na stronie rejestracji lub logowania Azure AD B2C przedstawia listę zewnętrznych dostawców tożsamości, które użytkownik może wybrać do logowania. Po wybraniu jednego z zewnętrznych dostawców tożsamości zostaną one pobrane (przekierowane) do witryny sieci Web wybranego dostawcy w celu ukończenia procesu logowania. Po pomyślnym zalogowaniu się użytkownika są one zwracane do Azure AD B2C w celu uwierzytelnienia konta w aplikacji.

![Przykład logowania mobilnego przy użyciu konta społecznościowego (Facebook)](media/technical-overview/external-idp.png)

Aby dowiedzieć się, jak dodać dostawców tożsamości w Azure AD B2C, zobacz [Samouczek: Dodawanie dostawców tożsamości do aplikacji w Azure Active Directory B2C](tutorial-add-identity-providers.md).

## <a name="identity-experiences-user-flows-or-custom-policies"></a>Środowisko tożsamości: przepływy użytkownika lub zasady niestandardowe

Rozszerzalna struktura zasad Azure AD B2C jest jej podstawową siłą. Zasady opisują środowiska tożsamości użytkowników, takie jak rejestrowanie, logowanie i edytowanie profilów.

W Azure AD B2C istnieją dwie ścieżki podstawowe, które można wykonać w celu zapewnienia obsługi tożsamości: przepływy użytkownika i zasady niestandardowe.

* **Przepływy użytkowników** są wstępnie zdefiniowanymi, wbudowanymi, konfigurowalnymi zasadami, które udostępniamy, aby można było tworzyć nowe środowiska tworzenia konta, logowania i edytowania zasad w ciągu kilku minut.

* **Zasady niestandardowe** umożliwiają tworzenie własnych podróży użytkowników w przypadku złożonych scenariuszy obsługi tożsamości.

Zarówno przepływy użytkownika, jak i zasady niestandardowe są obsługiwane przez *platformę obsługi tożsamości*, aparat aranżacji zasad usługi Azure AD B2C's.

### <a name="user-flow"></a>Przepływ użytkownika

Aby ułatwić szybkie konfigurowanie typowych zadań związanych z tożsamościami, Azure Portal obejmuje kilka wstępnie zdefiniowanych i konfigurowalnych zasad o nazwie *przepływy użytkownika*.

Można skonfigurować ustawienia przepływu użytkownika, takie jak te, aby kontrolować zachowania dotyczące środowiska tożsamości w aplikacjach:

* Typy kont używane do logowania, takie jak konta społecznościowe, takie jak Facebook lub konta lokalne używające adresu e-mail i hasła do logowania
* Atrybuty, które mają być zbierane od konsumenta, takie jak imię i nazwisko, kod pocztowy lub kraj/region miejsca zamieszkania
* Usługa Azure AD Multi-Factor Authentication (MFA)
* Dostosowanie interfejsu użytkownika
* Zestaw oświadczeń w tokenie odbieranych przez aplikację po zakończeniu przepływu użytkownika przez użytkownika
* Zarządzanie sesjami
* ... i nie tylko.

Najczęstsze scenariusze tożsamości dla większości aplikacji mobilnych, sieci Web i jednostronicowych mogą być definiowane i implementowane przy użyciu przepływów użytkowników. Zalecamy używanie wbudowanych przepływów użytkowników, chyba że masz skomplikowane scenariusze podróży użytkowników, które wymagają pełnej elastyczności zasad niestandardowych.

Dowiedz się więcej o przepływach użytkowników w [przepływach użytkowników w Azure Active Directory B2C](user-flow-overview.md).

### <a name="custom-policy"></a>Zasady niestandardowe

Zasady niestandardowe blokują dostęp do pełnego potencjału aparatu aranżacji programu Identity Experience Framework (IEF). Za pomocą zasad niestandardowych można korzystać z IEF w celu kompilowania niemal wszelkich możliwości uwierzytelniania, rejestracji użytkowników lub edytowania profilów, które można wyobrazić.

Platforma obsługi tożsamości umożliwia konstruowanie podróży użytkowników przy użyciu dowolnej kombinacji kroków. Przykład:

* Sfederować z innymi dostawcami tożsamości
* Wyzwania uwierzytelniania wieloskładnikowego (MFA) w pierwszej kolejności i innej firmy
* Zbierz dane wprowadzane przez użytkownika
* Integrowanie z systemami zewnętrznymi przy użyciu komunikacji interfejsu API REST

Każda taka podróż użytkownika jest definiowana przez zasady i można utworzyć dowolną liczbę lub kilka zasad potrzebnych do zapewnienia najlepszego środowiska użytkownika w organizacji.

![Diagram przedstawiający przykład złożonej podróży użytkownika włączonej przez IEF](media/technical-overview/custom-policy.png)

Zasady niestandardowe są definiowane przez kilka plików XML, które odwołują się do siebie nawzajem w łańcuchu hierarchicznym. Elementy XML definiują schemat oświadczeń, przekształcenia oświadczeń, definicje zawartości, dostawcy oświadczeń, profile techniczne, etapy organizowania podróży użytkowników i inne aspekty środowiska tożsamości.

Zaawansowana elastyczność zasad niestandardowych jest najbardziej odpowiednia w przypadku konieczności tworzenia złożonych scenariuszy tożsamości. Deweloperzy konfigurujący zasady niestandardowe muszą definiować relacje zaufania w szczegółowy sposób, aby obejmowały punkty końcowe metadanych, dokładne definicje wymiany oświadczeń i konfigurować klucze tajne, klucze i certyfikaty, zgodnie z potrzebami każdego dostawcy tożsamości.

Dowiedz się więcej na temat zasad niestandardowych w [zasadach niestandardowych w Azure Active Directory B2C](custom-policy-overview.md).

## <a name="protocols-and-tokens"></a>Protokoły i tokeny

- W przypadku aplikacji Azure AD B2C obsługuje protokoły [OAuth 2,0](protocols-overview.md), [OpenID Connect Connect](openid-connect.md)i [SAML](connect-with-saml-service-providers.md) na potrzeby podróży użytkownika. Aplikacja uruchamia pracę użytkownika, wydając żądania uwierzytelniania do Azure AD B2C. Wynikiem żądania Azure AD B2C jest token zabezpieczający, taki jak [token identyfikatora, token dostępu](tokens-overview.md)lub token SAML. Ten token zabezpieczający definiuje tożsamość użytkownika w aplikacji.

- W przypadku tożsamości zewnętrznych Azure AD B2C obsługuje Federacji z dowolnymi dostawcami OAuth 1,0, OAuth 2,0, OpenID Connect Connect i SAML Identity.

Na poniższym diagramie przedstawiono sposób, w jaki Azure AD B2C może komunikować się przy użyciu różnych protokołów w ramach tego samego przepływu uwierzytelniania:

![Diagram aplikacji klienckiej opartej na OIDC federowanie z użyciem protokołu SAML dostawcy tożsamości](media/technical-overview/protocols.png)

1. Aplikacja jednostki uzależnionej inicjuje żądanie autoryzacji, aby Azure AD B2C przy użyciu połączenia OpenID Connect.
1. Gdy użytkownik aplikacji zdecyduje się na zalogowanie się przy użyciu zewnętrznego dostawcy tożsamości korzystającego z protokołu SAML, Azure AD B2C wywołuje protokół SAML, aby komunikować się z tym dostawcą tożsamości.
1. Gdy użytkownik ukończy operację logowania z zewnętrznym dostawcą tożsamości, Azure AD B2C następnie zwróci token do aplikacji jednostki uzależnionej za pomocą OpenID Connect Connect.

## <a name="application-integration"></a>Integracja aplikacji

Gdy użytkownik chce zalogować się do aplikacji, niezależnie od tego, czy jest to aplikacja sieci Web, urządzenia przenośne, klasyczne czy jednostronicowe, aplikacja inicjuje żądanie autoryzacji do przepływu użytkownika lub niestandardowego punktu końcowego dostarczonego przez zasady. Przepływ użytkownika lub zasady niestandardowe definiują i kontrolują środowisko użytkownika. Po zakończeniu przepływu użytkownika, na przykład w przepływie *rejestracji lub zalogowaniu* , Azure AD B2C generuje token, a następnie przekierowuje użytkownika z powrotem do aplikacji.

![Aplikacja mobilna ze strzałkami przedstawiająca przepływ między stroną logowania Azure AD B2C](media/technical-overview/app-integration.png)

Wiele aplikacji może korzystać z tego samego przepływu użytkownika lub zasad niestandardowych. Pojedyncza aplikacja może korzystać z wielu przepływów użytkowników lub zasad niestandardowych.

Na przykład, aby zalogować się do aplikacji, aplikacja używa przepływu *rejestracji lub logowania* . Po zalogowaniu się użytkownika mogą chcieć edytować swój profil, więc aplikacja inicjuje inne żądanie autoryzacji, tym razem korzystając z *profilu Edytuj* przepływ użytkownika.

## <a name="seamless-user-experiences"></a>Bezproblemowe środowisko użytkownika

W Azure AD B2C możesz postawić swoją tożsamość użytkowników, tak aby strony, które są wyświetlane, bezproblemowo przechodzą z wyglądu i sposobu działania marki. Uzyskujesz prawie pełną kontrolę nad zawartością HTML i CSS prezentowaną użytkownikom podczas przechodzenia przez tożsamość aplikacji. Dzięki tej elastyczności można zachować spójność marki i wizualizacji między aplikacją i Azure AD B2C.

![Zrzuty ekranu przedstawiające stronę logowania dostosowanej przez markę](media/technical-overview/seamless-ux.png)

Informacje o dostosowywaniu interfejsu użytkownika znajdują [się w temacie Informacje o dostosowywaniu interfejsów użytkowników w Azure Active Directory B2C](customize-ui-overview.md).

## <a name="localization"></a>Lokalizacja

Dostosowanie języka w Azure AD B2C pozwala dostosowywać różne języki do potrzeb klientów. Firma Microsoft udostępnia tłumaczenia dla języków 36, ale można również udostępniać własne tłumaczenia w dowolnym języku. Nawet jeśli środowisko użytkownika jest dostarczane tylko dla jednego języka, można dostosować dowolny tekst na stronach.

![Trzy strony logowania do rejestracji przedstawiające tekst interfejsu użytkownika w różnych językach](media/technical-overview/localization.png)

Zobacz, jak lokalizacja działa w [dostosowywaniu języka w Azure Active Directory B2C](user-flow-language-customization.md).

## <a name="add-your-own-business-logic"></a>Dodawanie własnej logiki biznesowej

Jeśli zdecydujesz się używać zasad niestandardowych, możesz zintegrować z interfejsem API RESTful w podróży użytkowników, aby dodać własną logikę biznesową do kursu. Na przykład Azure AD B2C może wymieniać dane za pomocą usługi RESTful, aby:

* Wyświetlanie niestandardowych przyjaznych dla użytkownika komunikatów o błędach.
* Sprawdź poprawność danych wprowadzonych przez użytkownika, aby zapobiec utrwalaniu źle skonstruowanych danych w katalogu użytkownika. Na przykład można modyfikować dane wprowadzone przez użytkownika, takie jak pierwsze imię i nazwisko, jeśli wprowadzono je na małą literę.
* Wzbogacaj dane użytkowników przez dalsze integrację z firmową aplikacją biznesową.
* Za pomocą wywołań RESTful można wysyłać powiadomienia wypychane, aktualizować korporacyjne bazy danych, uruchamiać proces migracji użytkowników, zarządzać uprawnieniami, bazami danych inspekcji i nie tylko.

Programy lojalnościowe są w innym scenariuszu obsługiwane przez usługę Azure AD B2C's, która umożliwia wywoływanie interfejsów API REST. Na przykład usługa RESTful może odbierać adresy e-mail użytkownika, wysyłać zapytania do bazy danych klienta, a następnie zwracać numer lojalnościowy użytkownika w celu Azure AD B2C. Dane zwrotne mogą być przechowywane na koncie katalogu użytkownika w Azure AD B2C, następnie następnie oceniane w kolejnych krokach zasad lub zawarte w tokenie dostępu.

![Integracja biznesowa w aplikacji mobilnej](media/technical-overview/lob-integration.png)

Wywołanie interfejsu API REST można dodać w dowolnym kroku w podróży użytkownika zdefiniowanej przez zasady niestandardowe. Można na przykład wywołać interfejs API REST:

* Podczas logowania, tuż przed Azure AD B2C sprawdza poprawność poświadczeń
* Natychmiast po zalogowaniu
* Przed Azure AD B2C tworzy nowe konto w katalogu
* Po Azure AD B2C zostanie utworzone nowe konto w katalogu
* Zanim Azure AD B2C wystawia token dostępu

Aby dowiedzieć się, jak używać zasad niestandardowych dla integracji z interfejsem API RESTful w Azure AD B2C, zobacz [integrowanie wymiany oświadczeń interfejsu API REST w Azure AD B2C niestandardowych zasadach](custom-policy-rest-api-intro.md).

## <a name="protect-customer-identities"></a>Ochrona tożsamości klientów

Azure AD B2C jest zgodna z zabezpieczeniami, ochroną prywatności i innymi zobowiązaniami opisanymi w [Centrum zaufania Microsoft Azure](https://www.microsoft.com/trustcenter/cloudservices/azure).

Sesje są modelowane jako zaszyfrowane dane przy użyciu klucza odszyfrowywania znanego tylko do usługi tokenu zabezpieczeń Azure AD B2C. Używany jest algorytm silnego szyfrowania AES-192. Wszystkie ścieżki komunikacyjne są chronione za pomocą protokołu TLS w celu poufności i integralności. Nasza usługa tokenu zabezpieczającego używa certyfikatu rozszerzonej weryfikacji (EV) dla protokołu TLS. Ogólnie rzecz biorąc, usługa tokenów zabezpieczających zmniejsza ataki z użyciem skryptów między lokacjami (XSS) przez nie renderowanie niezaufanych danych wejściowych.

![Diagram zabezpieczonych danych podczas przesyłania i przechowywania](media/technical-overview/user-data.png)

### <a name="access-to-user-data"></a>Dostęp do danych użytkownika

Azure AD B2C dzierżawy współużytkują wiele cech z dzierżawami przedsiębiorstwa Azure Active Directory używanymi przez pracowników i partnerów. Wspólne aspekty obejmują mechanizmy wyświetlania ról administracyjnych, przypisywania ról i działań związanych z inspekcją.

Można przypisać role do kontrolowania, kto może wykonywać pewne działania administracyjne w Azure AD B2C, w tym:

* Twórz wszystkie aspekty przepływów użytkowników i zarządzaj nimi
* Utwórz schemat atrybutów dostępny dla wszystkich przepływów użytkowników i Zarządzaj nim
* Konfigurowanie dostawców tożsamości do użycia w Federacji bezpośredniej
* Tworzenie zasad zaufania i zarządzanie nimi w strukturze środowiska tożsamości (zasady niestandardowe)
* Zarządzanie wpisami tajnymi dla Federacji i szyfrowania w strukturze środowiska tożsamości (zasady niestandardowe)

Aby uzyskać więcej informacji na temat ról usługi Azure AD, w tym Azure AD B2C obsługi ról administracyjnych, zobacz [uprawnienia roli administrator w programie Azure Active Directory](../active-directory/roles/permissions-reference.md).

### <a name="multi-factor-authentication-mfa"></a>Multi-Factor Authentication (MFA)

Azure AD B2C uwierzytelnianie wieloskładnikowe (MFA) pomaga w zabezpieczeniu dostępu do danych i aplikacji przy jednoczesnym zachowaniu prostoty dla użytkowników. Zapewnia dodatkowe zabezpieczenia, wymagając drugiej formy uwierzytelniania i zapewnia silne uwierzytelnianie, oferując wiele łatwych w użyciu metod uwierzytelniania. Użytkownicy mogą lub nie mogą zakwestionować usługi MFA na podstawie decyzji konfiguracyjnych, które można utworzyć jako administrator.

Zobacz jak włączyć uwierzytelnianie wieloskładnikowe w przepływach użytkowników w artykule [Włączanie uwierzytelniania wieloskładnikowego w Azure Active Directory B2C](custom-policy-multi-factor-authentication.md).

### <a name="smart-account-lockout"></a>Blokada konta inteligentnego

Aby zapobiec próbom odgadnięcia hasła, Azure AD B2C używa zaawansowanej strategii do blokowania kont na podstawie adresu IP żądania, wprowadzonych haseł i kilku innych czynników. Czas trwania blokady jest automatycznie zwiększany na podstawie ryzyka i liczby prób.

![Inteligentna blokada konta](media/technical-overview/smart-lockout1.png)

Więcej informacji o zarządzaniu ustawieniami ochrony hasłem znajduje się [w temacie Zarządzanie zagrożeniami do zasobów i danych w Azure Active Directory B2C](threat-management.md).

### <a name="password-complexity"></a>Złożoność hasła

Podczas rejestrowania lub resetowania hasła użytkownicy muszą podać hasło spełniające reguły złożoności. Domyślnie Azure AD B2C wymusza zasady silnego hasła. Azure AD B2C udostępnia również opcje konfiguracji służące do określania wymagań dotyczących złożoności haseł używanych przez klientów.

Wymagania dotyczące złożoności hasła można skonfigurować zarówno w przypadku [przepływów użytkowników](user-flow-password-complexity.md) , jak i [zasad niestandardowych](custom-policy-password-complexity.md).

## <a name="auditing-and-logs"></a>Inspekcja i dzienniki

Azure AD B2C emituje dzienniki inspekcji zawierające informacje o działaniu dotyczące jego zasobów, wystawionych tokenów i dostępu administratora. Za pomocą tych dzienników inspekcji można zrozumieć aktywność platformy i zdiagnozować problemy. Wpisy dziennika inspekcji są dostępne wkrótce po wykonaniu działania, które spowodowało wygenerowanie zdarzenia.

W dzienniku inspekcji, który jest dostępny dla dzierżawy Azure AD B2C lub dla określonego użytkownika, możesz znaleźć informacje, takie jak:

* Działania dotyczące autoryzacji użytkownika w celu uzyskania dostępu do zasobów B2C (na przykład administrator uzyskujący dostęp do listy zasad B2C)
* Działania związane z atrybutami katalogu pobrane, gdy administrator zaloguje się przy użyciu Azure Portal
* Tworzenie, odczytywanie, aktualizowanie i usuwanie (CRUD) operacji na aplikacjach B2C
* Operacje CRUD na kluczach przechowywanych w kontenerze kluczy B2C
* CRUD operacji na zasobach B2C (na przykład zasad i dostawców tożsamości)
* Weryfikowanie poświadczeń użytkownika i wystawianie tokenów

![Dziennik inspekcji poszczególnych użytkowników wyświetlany w Azure Portal](media/technical-overview/audit-log.png)

Aby uzyskać dodatkowe informacje na temat dzienników inspekcji, zobacz [dostęp do dzienników inspekcji Azure AD B2C](view-audit-logs.md).

### <a name="usage-insights"></a>Szczegółowe informacje o użyciu

Azure AD B2C umożliwia odnajdywanie podczas rejestrowania się lub logowania do aplikacji sieci Web, w której znajdują się użytkownicy, oraz używanych przeglądarek i systemów operacyjnych. Dzięki integracji Application Insights platformy Azure z usługą Azure AD B2C przy użyciu zasad niestandardowych można uzyskać wgląd w sposób tworzenia konta, logowania, resetowania hasła lub edytowania profilu. Z taką wiedzą można podejmować decyzje oparte na danych dla nadchodzących cykli programistycznych.

Dowiedz się więcej o analizie użycia w temacie [śledzenie zachowania użytkowników w Azure Active Directory B2C przy użyciu Application Insights](analytics-with-application-insights.md).

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz już funkcje i aspekty techniczne Azure Active Directory B2C, Rozpocznij pracę z naszym [samouczkiem dotyczącym tworzenia dzierżawy Azure Active Directory B2C](tutorial-create-tenant.md).
