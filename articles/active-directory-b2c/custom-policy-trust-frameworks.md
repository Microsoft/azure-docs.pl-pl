---
title: Przegląd zasad niestandardowych Azure AD B2C | Microsoft Docs
description: Temat dotyczący Azure Active Directory B2C zasad niestandardowych i struktury obsługi tożsamości.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cb33e11af26d5f5a2676f5b236ac142179bdb550
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99592844"
---
# <a name="azure-ad-b2c-custom-policy-overview"></a>Przegląd zasad niestandardowych Azure AD B2C

Zasady niestandardowe to pliki konfiguracji, które definiują zachowanie dzierżawy Azure Active Directory B2C (Azure AD B2C). Podczas gdy [przepływy użytkowników](user-flow-overview.md) są wstępnie zdefiniowane w portalu Azure AD B2C dla najbardziej typowych zadań związanych z tożsamościami, zasady niestandardowe mogą być w pełni edytowane przez dewelopera tożsamości w celu wykonywania wielu różnych zadań.

Zasada niestandardowa jest w pełni konfigurowana i oparta na zasadach. Zasady niestandardowe organizują relację zaufania między jednostkami w standardowych formatach protokołów, takimi jak OpenID Connect Connect, OAuth, SAML i kilka niestandardowymi, na przykład wymiany oświadczeń systemu opartego na interfejsie API REST. Struktura tworzy przyjazne dla użytkownika i białe środowisko.

Zasady niestandardowe są reprezentowane jako jeden lub więcej plików w formacie XML, które odnoszą się do siebie nawzajem w łańcuchu hierarchicznym. Elementy XML definiują bloki konstrukcyjne, interakcje z użytkownikiem, inne strony i logikę biznesową. 

## <a name="custom-policy-starter-pack"></a>Pakiet startowy zasad niestandardowych

[Pakiet startowy](custom-policy-get-started.md#get-the-starter-pack) Azure AD B2C zasad niestandardowych zawiera kilka wstępnie utworzonych zasad pozwalających szybko rozpocząć pracę. Każdy z tych pakietów początkowych zawiera najmniejszą liczbę profilów technicznych i podróży użytkowników, które są konieczne do osiągnięcia opisanych scenariuszy:

- **LocalAccounts** — umożliwia korzystanie tylko z kont lokalnych.
- **SocialAccounts** — umożliwia korzystanie tylko z kont społecznościowych (lub federacyjnych).
- **SocialAndLocalAccounts** — umożliwia korzystanie z kont lokalnych i społecznościowych. Większość naszych przykładów odnosi się do tych zasad.
- **SocialAndLocalAccountsWithMFA** — umożliwia korzystanie z opcji uwierzytelniania społecznościowego, lokalnego i wieloskładnikowego.

## <a name="understanding-the-basics"></a>Zrozumienie podstaw 

### <a name="claims"></a>Oświadczenia

W trakcie wykonywania zasad Azure AD B2C, zgłoszenie zapewnia tymczasowy magazyn danych. Może on przechowywać informacje o użytkowniku, takie jak imię i nazwisko, nazwisko lub inne oświadczenie uzyskane od użytkownika lub innych systemów (wymiana oświadczeń). [Schemat oświadczeń](claimsschema.md) jest miejscem, w którym deklarujesz oświadczenia. 

Po uruchomieniu zasad Azure AD B2C wysyła i odbiera oświadczenia do i od wewnętrznych i zewnętrznych podmiotów, a następnie wysyła podzestaw tych oświadczeń do aplikacji jednostki uzależnionej jako część tokenu. Oświadczenia są używane w następujący sposób: 

- W obiekcie użytkownika katalogu zostaje zapisane lub odczytane lub zaktualizowane.
- Odebrano wniosek od zewnętrznego dostawcy tożsamości.
- Oświadczenia są wysyłane lub odbierane przy użyciu niestandardowej usługi interfejsu API REST.
- Dane są zbierane jako oświadczenia od użytkownika podczas rejestrowania lub edytowania przepływów profilów.

### <a name="manipulating-your-claims"></a>Manipulowanie oświadczeniami

[Przekształcenia oświadczeń](claimstransformations.md) są wstępnie zdefiniowanymi funkcjami, które mogą służyć do konwertowania danego oświadczenia na inne, szacowania oświadczenia lub ustawiania wartości oświadczenia. Na przykład dodanie elementu do kolekcji ciągów, zmiana wielkości liter ciągu lub oszacowanie zgłoszenia daty i godziny. Transformacja oświadczeń określa metodę transformacji. 

### <a name="customize-and-localize-your-ui"></a>Dostosowywanie i lokalizowanie interfejsu użytkownika

Jeśli chcesz zbierać informacje od użytkowników przez przedstawienie strony w przeglądarce sieci Web, użyj [profilu technicznego z własnym potwierdzeniem](self-asserted-technical-profile.md). Możesz edytować własny profil techniczny, aby [dodać oświadczenia i dostosować dane wprowadzane przez użytkownika](./configure-user-input.md).

Aby [dostosować interfejs użytkownika](customize-ui-with-html.md) dla własnego profilu technicznego, należy określić adres URL w elemencie [definicji zawartości](contentdefinitions.md) z dostosowanej zawartości HTML. W profilu technicznym z własnym potwierdzeniem możesz wskazać ten identyfikator definicji zawartości.

Aby dostosować ciągi charakterystyczne dla języka, użyj elementu [Lokalizacja](localization.md) . Definicja zawartości może zawierać odwołanie do [lokalizacji](localization.md) , która określa listę zlokalizowanych zasobów do załadowania. Usługa Azure AD B2C scala elementy interfejsu użytkownika z zawartością HTML ładowaną z adresu URL, a następnie wyświetla stronę użytkownikowi. 

## <a name="relying-party-policy-overview"></a>Przegląd zasad jednostki uzależnionej

Aplikacja jednostki uzależnionej, która w protokole SAML jest znana jako dostawca usługi, wywołuje [zasady jednostki uzależnionej](relyingparty.md) do wykonania określonej podróży użytkownika. Zasady jednostki uzależnionej określają podróż użytkownika do wykonania i listę oświadczeń, które zawiera token. 

![Diagram przedstawiający przepływ wykonywania zasad](./media/custom-policy-trust-frameworks/custom-policy-execution.png)

Wszystkie aplikacje jednostki uzależnionej, które korzystają z tych samych zasad, otrzymają te same oświadczenia tokenu, a użytkownik przechodzi przez tę samą podróż użytkownika.

### <a name="user-journeys"></a>Podróże użytkownika

[Podróże użytkowników](userjourneys.md) umożliwiają zdefiniowanie logiki biznesowej z ścieżką, za pomocą której użytkownik będzie mógł uzyskać dostęp do aplikacji. Użytkownik odbywa się przez podróż użytkownika, aby pobrać oświadczenia, które mają być prezentowane dla aplikacji. Podróż użytkownika jest tworzona na podstawie sekwencji [kroków aranżacji](userjourneys.md#orchestrationsteps). Użytkownik musi dotrzeć do ostatniego kroku, aby uzyskać token. 

W poniższych instrukcjach opisano sposób dodawania kroków aranżacji do zasad programu [początkowych dla konta społecznościowych i lokalnych](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts) . Oto przykład wywołania interfejsu API REST, które zostało dodane.

![dostosowana podróż użytkownika](media/custom-policy-trust-frameworks/user-journey-flow.png)


### <a name="orchestration-steps"></a>Kroki aranżacji

Krok aranżacji odwołuje się do metody implementującej jej zamierzony cel lub funkcjonalność. Ta metoda jest nazywana [profilem technicznym](technicalprofiles.md). Gdy użytkownik potrzebuje rozgałęziania w celu lepszego reprezentowania logiki biznesowej, krok aranżacji odwołuje się do [podróży podrzędnej](subjourneys.md). Podróż podrzędna zawiera swój własny zestaw kroków aranżacji.

Użytkownik musi dotrzeć do ostatniego kroku aranżacji w podróży użytkownika w celu uzyskania tokenu. Jednak użytkownicy mogą nie muszą podróżować przez wszystkie kroki aranżacji. Kroki aranżacji można warunkowo wykonać w oparciu o [warunki wstępne](userjourneys.md#preconditions) zdefiniowane w kroku aranżacji. 

Po zakończeniu kroku aranżacji Azure AD B2C przechowuje zgłoszone oświadczenia w **zbiorze oświadczeń**. Oświadczenia w zbiorze oświadczeń mogą być wykorzystywane przez kolejne kroki aranżacji w podróży użytkownika.

Na poniższym diagramie przedstawiono sposób, w jaki kroki aranżacji podróży użytkownika mogą uzyskać dostęp do zbioru oświadczeń.

![Azure AD B2C podróży użytkownika](media/custom-policy-trust-frameworks/user-journey-diagram.png)

### <a name="technical-profile"></a>Profil techniczny

Profil techniczny zapewnia interfejs do komunikowania się z różnymi typami stron. Podróż użytkownika polega na łączeniu profilów technicznych przez etapy aranżacji w celu zdefiniowania logiki biznesowej.

Wszystkie typy profilów technicznych mają takie same koncepcje. Wysyłasz oświadczenia wejściowe, uruchamiasz transformację oświadczeń i komunikują się ze skonfigurowanymi stronami. Po zakończeniu procesu profil techniczny zwróci oświadczenia wyjściowe do zbioru oświadczeń. Aby uzyskać więcej informacji, zobacz [Omówienie profilów technicznych](technicalprofiles.md).

### <a name="validation-technical-profile"></a>Profil techniczny weryfikacji

Gdy użytkownik współdziała z interfejsem użytkownika, może być konieczne zweryfikowanie zbieranych danych. Aby można było korzystać z użytkownika, musi być używany [profil techniczny z własnym potwierdzeniem](self-asserted-technical-profile.md) .

Aby sprawdzić poprawność danych wejściowych użytkownika, [profil techniczny weryfikacji](validation-technical-profile.md) jest wywoływany z profilu technicznego z własnym potwierdzeniem. Profil techniczny weryfikacji to metoda wywołująca dowolny nieinteraktywny profil techniczny. W takim przypadku profil techniczny może zwracać oświadczenia wyjściowe lub komunikat o błędzie. Komunikat o błędzie jest renderowany do użytkownika na ekranie, dzięki czemu użytkownik może ponowić próbę.

Na poniższym diagramie pokazano, w jaki sposób Azure AD B2C sprawdzać poprawność poświadczeń użytkowników przy użyciu profilu technicznego weryfikacji.

![Diagram profilu technicznego weryfikacji](media/custom-policy-trust-frameworks/validation-technical-profile.png)

## <a name="inheritance-model"></a>Model dziedziczenia

Każdy pakiet startowy zawiera następujące pliki:

- Plik **podstawowy** , który zawiera większość definicji. Aby ułatwić rozwiązywanie problemów i długoterminową konserwację zasad, spróbuj zminimalizować liczbę zmian wprowadzonych w tym pliku.
- Plik **rozszerzeń** , który przechowuje unikatowe zmiany konfiguracji dla Twojej dzierżawy. Ten plik zasad pochodzi od pliku podstawowego. Użyj tego pliku, aby dodać nowe funkcje lub zastąpić istniejące funkcje. Na przykład użyj tego pliku do sfederować z nowymi dostawcami tożsamości.
- Plik **jednostki uzależnionej (RP)** , który jest pojedynczym plikiem skoncentrowanym na zadaniach, który jest wywoływany bezpośrednio przez aplikację jednostki uzależnionej, taką jak aplikacje internetowe, mobilne lub klasyczne. Każde unikatowe zadanie, takie jak rejestrowanie, logowanie, Resetowanie hasła lub edytowanie profilu, wymaga własnego pliku zasad jednostki uzależnionej. Ten plik zasad pochodzi z pliku rozszerzeń.

Model dziedziczenia jest następujący:

- Zasady podrzędne na dowolnym poziomie mogą dziedziczyć z zasad nadrzędnych i zwiększać je, dodając nowe elementy.
- W przypadku bardziej złożonych scenariuszy można dodać więcej poziomów dziedziczenia (łącznie do 10).
- Można dodać więcej zasad jednostki uzależnionej. Na przykład Usuń moje konto, Zmień numer telefonu, zasady jednostek uzależnionych SAML i nie tylko.

Na poniższym diagramie przedstawiono relację między plikami zasad i aplikacjami jednostki uzależnionej.

![Diagram przedstawiający model dziedziczenia zasad struktury zaufania](media/custom-policy-trust-frameworks/policies.png)


## <a name="guidance-and-best-practices"></a>Wskazówki i najlepsze rozwiązania

### <a name="best-practices"></a>Najlepsze rozwiązania

W ramach niestandardowych zasad Azure AD B2C można zintegrować własną logikę biznesową w celu kompilowania potrzebnych środowisk użytkownika i zwiększania funkcjonalności usługi. Mamy zestaw najlepszych rozwiązań i zaleceń, które należy zacząć.

- Utwórz logikę w ramach **zasad rozszerzenia** lub **zasad jednostki uzależnionej**. Można dodać nowe elementy, które przesłonią zasady podstawowe przez odwołanie do tego samego identyfikatora. Pozwoli to na skalowanie projektu przy równoczesnym uaktualnieniu zasad podstawowych w przypadku wydania przez firmę Microsoft nowych pakietów startowych.
- W ramach **podstawowych zasad** zdecydowanie zalecamy uniknięcie wprowadzania jakichkolwiek zmian. W razie potrzeby wprowadź komentarze, w których wprowadzane są zmiany.
- Podczas zastępowania elementu, takiego jak metadane profilu technicznego, należy unikać kopiowania całego profilu technicznego z zasad podstawowych. Zamiast tego Skopiuj tylko wymaganą sekcję elementu. Zobacz temat [wyłączanie weryfikacji poczty e-mail](./disable-email-verification.md) , aby zapoznać się z przykładem sposobu wprowadzania zmiany.
- Aby zmniejszyć duplikowanie profilów technicznych, w których funkcje podstawowe są udostępniane, należy użyć funkcji [dołączania do profilu technicznego](technicalprofiles.md#include-technical-profile).
- Unikaj zapisywania do katalogu usługi Azure AD podczas logowania, co może prowadzić do ograniczania problemów.
- Jeśli zasady mają zależności zewnętrzne, takie jak interfejsy API REST, upewnij się, że są one wysoce dostępne.
- Aby lepiej korzystać z interfejsu użytkownika, upewnij się, że niestandardowe szablony HTML są wdrożone globalnie przy użyciu [dostarczania zawartości online](../cdn/index.yml). Usługa Azure Content Delivery Network (CDN) pozwala skrócić czas ładowania, zaoszczędzić przepustowość i zwiększyć szybkość reakcji.
- Jeśli chcesz wprowadzić zmianę w podróży użytkowników, Skopiuj całą podróż użytkownika z zasad podstawowych do zasad rozszerzenia. Podaj unikatowy identyfikator podróży użytkownika w przegranie użytkownika, który został skopiowany. Następnie w [zasadach jednostki uzależnionej](relyingparty.md)Zmień [domyślny element podróż użytkownika](relyingparty.md#defaultuserjourney) , tak aby wskazywał nową podróż użytkownika.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Podczas tworzenia przy użyciu zasad Azure AD B2C można napotkać błędy lub wyjątki podczas wykonywania podróży użytkownika. Można to sprawdzić przy użyciu Application Insights.

- Integruj Application Insights z Azure AD B2C, aby [zdiagnozować wyjątki](troubleshoot-with-application-insights.md).
- [Rozszerzenie Azure AD B2C dla Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) może pomóc w dostępie do [dzienników i wizualizowaniu ich](https://github.com/azure-ad-b2c/vscode-extension/blob/master/src/help/app-insights.md) w oparciu o nazwę i godzinę zasad.
- Najbardziej typowym błędem konfigurowania zasad niestandardowych jest nieprawidłowo sformatowana zawartość XML. Użyj [walidacji schematu XML](troubleshoot-custom-policies.md) , aby zidentyfikować błędy przed przekazaniem pliku XML.

## <a name="continuous-integration"></a>Ciągła integracja

Za pomocą potoku ciągłej integracji i dostarczania (CI/CD), który został skonfigurowany w Azure Pipelines, można [uwzględnić zasady niestandardowe Azure AD B2C do dostarczania oprogramowania](deploy-custom-policies-devops.md) i automatyzacji kontroli kodu. Podczas wdrażania do różnych środowisk Azure AD B2C, na przykład dev, test i Production, zalecamy usunięcie ręcznych procesów i przeprowadzenie testowania automatycznego przy użyciu Azure Pipelines.

## <a name="prepare-your-environment"></a>Przygotowywanie środowiska

Rozpoczynasz pracę z Azure AD B2C zasadami niestandardowymi:

1. [Tworzenie dzierżawy usługi Azure AD B2C](tutorial-create-tenant.md)
1. [Zarejestruj aplikację sieci Web](tutorial-register-applications.md) przy użyciu Azure Portal, aby umożliwić testowanie zasad.
1. Dodaj niezbędne [klucze zasad](custom-policy-get-started.md#add-signing-and-encryption-keys) i [zarejestruj aplikacje struktury obsługi tożsamości](custom-policy-get-started.md#register-identity-experience-framework-applications).
1. [Pobierz pakiet startowy zasad Azure AD B2C](custom-policy-get-started.md#get-the-starter-pack) i przekaż go do swojej dzierżawy. 
1. Po przekazaniu pakietu początkowego [Sprawdź zasady rejestracji lub logowania](custom-policy-get-started.md#test-the-custom-policy).
1. Zalecamy pobranie i zainstalowanie [Visual Studio Code](https://code.visualstudio.com/) (vs Code). Visual Studio Code to lekki, ale zaawansowany edytor kodu źródłowego, który działa na pulpicie i jest dostępny dla systemów Windows, macOS i Linux. Za pomocą VS Code można szybko nawigować po Azure AD B2C niestandardowych plikach XML zasad, instalując [rozszerzenie Azure AD B2C dla vs Code](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)
 
## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu i przetestowaniu zasad Azure AD B2C można rozpocząć Dostosowywanie zasad. Zapoznaj się z następującymi artykułami, aby dowiedzieć się, jak:

- [Dodawanie oświadczeń i dostosowywanie danych wejściowych użytkownika](./configure-user-input.md) przy użyciu zasad niestandardowych. Informacje o definiowaniu i dodawaniu roszczeń do interfejsu użytkownika przez dostosowanie niektórych profilów technicznych pakietu startowego.
- [Dostosuj interfejs użytkownika](customize-ui-with-html.md) aplikacji przy użyciu zasad niestandardowych. Dowiedz się, jak utworzyć własną zawartość HTML i dostosować definicję zawartości.
- [Lokalizowanie interfejsu użytkownika](./language-customization.md) aplikacji przy użyciu zasad niestandardowych. Dowiedz się, jak skonfigurować listę obsługiwanych języków i udostępnić etykiety specyficzne dla języka poprzez dodanie elementu zlokalizowane zasoby.
- Podczas tworzenia i testowania zasad można [wyłączyć weryfikację poczty e-mail](./disable-email-verification.md). Dowiedz się, jak zastąpić metadane profilu technicznego.
- [Skonfiguruj logowanie za pomocą konta Google](./identity-provider-google.md) przy użyciu zasad niestandardowych. Dowiedz się, jak utworzyć nowego dostawcę oświadczeń przy użyciu profilu technicznego OAuth2. Następnie dostosuj podróż użytkownika w celu uwzględnienia opcji logowania Google.
- Aby zdiagnozować problemy z zasadami niestandardowymi, można [zbierać Azure Active Directory B2C dzienników z Application Insights](troubleshoot-with-application-insights.md). Dowiedz się, jak dodać nowe profile techniczne i skonfigurować zasady jednostek uzależnionych.
