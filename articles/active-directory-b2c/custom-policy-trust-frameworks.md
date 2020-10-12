---
title: Struktury zaufania odwołań w Azure Active Directory B2C | Microsoft Docs
description: Temat dotyczący Azure Active Directory B2C zasad niestandardowych i struktury obsługi tożsamości.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 08/04/2017
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d3d29bd05f67d00047499dc256e5e1a82f98693a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388803"
---
# <a name="define-trust-frameworks-with-azure-ad-b2c-identity-experience-framework"></a>Definiowanie struktur zaufania za pomocą środowiska Azure AD B2C Identity Framework

Azure Active Directory B2C (Azure AD B2C) zasady niestandardowe, które używają struktury obsługi tożsamości, zapewniają organizacji scentralizowaną usługę. Ta usługa zmniejsza złożoność federacji tożsamości w dużej społeczności interesu. Złożoność jest zredukowana do jednej relacji zaufania i wymiany pojedynczej metadanych.

Azure AD B2C zasady niestandardowe wykorzystują platformę obsługi tożsamości, aby można było odpowiedzieć na następujące pytania:

- Jakie są zasady prawne, zabezpieczenia, prywatność i ochrony danych, które muszą być przestrzegane?
- Kim są kontakty i jakie są procesy, które mają być akredytowany uczestnik?
- Którzy są akredytowanymi dostawcami informacji o tożsamościach (nazywanymi także "dostawcami oświadczeń") i jakie są one oferowane?
- Którzy są akredytowanymi jednostkami uzależnionymi (i opcjonalnie, czego wymagają)?
- Jakie są techniczne wymagania dotyczące współdziałania dla uczestników?
- Jakie są reguły operacyjne środowiska uruchomieniowego, które należy wymusić w przypadku wymiany informacji o tożsamości cyfrowej?

Aby odpowiedzieć na wszystkie te pytania, Azure AD B2C zasad niestandardowych, które używają struktury obsługi tożsamości, użyj konstrukcji Trust Framework (TF). Rozważmy tę konstrukcję i jej zawartość.

## <a name="understand-the-trust-framework-and-federation-management-foundation"></a>Zrozumienie struktury zaufania i usługi federacyjnej Management Foundation

Struktura zaufania to zapisywana Specyfikacja zasad tożsamości, zabezpieczeń, ochrony prywatności i danych, do których uczestnicy społeczności interesy muszą być zgodni.

Tożsamość federacyjna stanowi podstawę do osiągnięcia zapewnienia tożsamości użytkowników końcowych na skalę internetową. Delegując Zarządzanie tożsamościami do stron trzecich, można użyć pojedynczej tożsamości cyfrowej dla użytkownika końcowego z wieloma jednostkami uzależnionymi.

Program Identity Assurance wymaga, aby dostawcy tożsamości (dostawców tożsamości) i dostawcy atrybutów (AtPs) przestrzegały określonych zasad i praktyk dotyczących zabezpieczeń, ochrony prywatności oraz działania.  Jeśli nie mogą wykonać inspekcji bezpośrednich, jednostki uzależnione (RPS pliku) muszą opracowywać relacje zaufania z dostawców tożsamości i AtPs, które zdecydują się z nimi pracować.

W miarę zwiększania się liczby klientów i dostawców informacji o tożsamości cyfrowej można nadal korzystać z zarządzania buforowaniem tych relacji zaufania, a nawet do wymiany metadanych technicznych wymaganych do łączności sieciowej.  Centra federacyjne osiągnęły tylko ograniczone sukcesy podczas rozwiązywania tych problemów.

### <a name="what-a-trust-framework-specification-defines"></a>Co definiuje Specyfikacja struktury zaufania
TFs to linchpins modelu struktury zaufania Open Identity Exchange (OIX), gdzie każda społeczność interesów podlega określonej specyfikacji TF. Taka Specyfikacja TF definiuje:

- **Metryki bezpieczeństwa i ochrony prywatności dla społeczności interesu z definicją:**
    - Poziom gwarancji (DOWANIU) oferowany/wymagany przez uczestników; na przykład uporządkowany zestaw ocen zaufania do autentyczności informacji o tożsamości cyfrowej.
    - Poziomy ochrony (LOP), które są oferowane/wymagane przez uczestników; na przykład uporządkowany zestaw klasyfikacji zaufania do ochrony informacji o tożsamości cyfrowej, które są obsługiwane przez uczestników w społeczności zainteresowania.

- **Opis informacji o tożsamości cyfrowej oferowanych/wymaganych przez uczestników**.

- **Zasady techniczne dotyczące produkcji i zużycia informacji o tożsamości cyfrowej, a tym samym do mierzenia DOWANIU i LOP. Te zapisywane zasady zwykle obejmują następujące kategorie zasad:**
    - Zasady weryfikacji tożsamości, na przykład: *jak silna informacja o tożsamości osoby zbadane?*
    - Zasady zabezpieczeń, na przykład: *jak silnie są chronione integralność informacji i poufność?*
    - Zasady ochrony prywatności, na przykład: *jaką kontrolę ma użytkownik nad informacjami o danych osobowych*?
    - Zasady dotyczące bezczynności, na przykład: *Jeśli dostawca zaprzestanie operacji, jak zapewnia ciągłość i ochronę funkcji* samodzielnych?

- **Profile techniczne do produkcji i użycia informacji o tożsamości cyfrowej. Te profile obejmują:**
    - Interfejsy zakresu, dla których informacje o tożsamości cyfrowej są dostępne w określonym DOWANIU.
    - Wymagania techniczne dotyczące współdziałania z okablowaniem.

- **Opisy różnych ról, które uczestnicy społeczności mogą wykonywać, oraz kwalifikacji, które są wymagane do spełnienia tych ról.**

W ten sposób Specyfikacja TF reguluje sposób wymiany informacji o tożsamości między uczestnikami społeczności zainteresowania: jednostkami uzależnionymi, tożsamościami i dostawcami atrybutów oraz inspektorami atrybutów.

Specyfikacja TF to jeden lub wiele dokumentów, które stanowią odniesienie do zarządzania społecznością interesu, która reguluje potwierdzenie i użycie informacji o tożsamości cyfrowej w społeczności. Jest to udokumentowany zestaw zasad i procedur służących do ustanawiania relacji zaufania z tożsamościami cyfrowymi, które są używane dla transakcji online między członkami społeczności.

Innymi słowy, Specyfikacja TF definiuje reguły do tworzenia ekosystemu dla federacyjnego tożsamości federacyjnych dla społeczności.

Obecnie istnieje szeroka umowa na korzyść tego podejścia. Nie ma wątpliwości, że specyfikacje struktury zaufania ułatwiają rozwój ekosystemów tożsamości cyfrowych z zweryfikowanymi zabezpieczeniami, gwarancjami i ochroną prywatności, co oznacza, że mogą być ponownie używane w wielu społecznościach zainteresowania.

Z tego powodu Azure AD B2C zasad niestandardowych, które wykorzystują platformę obsługi tożsamości, wykorzystują specyfikację jako podstawę reprezentacji danych dla TF, aby ułatwić współdziałanie.

Azure AD B2C zasady niestandardowe, które wykorzystują platformę obsługi tożsamości, reprezentują specyfikacje TF jako mieszankę danych do odczytu ludzi i maszyn. Niektóre sekcje tego modelu (zwykle sekcje, które są bardziej zorientowane na zarządzanie) są reprezentowane jako odwołania do opublikowanych dokumentacji zasad zabezpieczeń i ochrony prywatności wraz z pokrewnymi procedurami (jeśli istnieją). Inne sekcje zawierają szczegółowe informacje na temat metadanych konfiguracji i reguł środowiska uruchomieniowego, które ułatwiają automatyzację operacyjną.

## <a name="understand-trust-framework-policies"></a>Omówienie zasad dotyczących struktury zaufania

W przypadku wdrożenia Specyfikacja TF zawiera zestaw zasad, które umożliwiają pełną kontrolę nad zachowaniami i środowiskami tożsamości.  Azure AD B2C zasady niestandardowe, które wykorzystują platformę obsługi tożsamości, umożliwiają tworzenie własnych narzędzi do tworzenia i konfigurowania ich za pomocą zasad deklaratywnych, które można definiować i konfigurować:

- Odwołanie do dokumentu lub odwołania, które definiują ekosystem tożsamości federacyjnej społeczności, która odnosi się do TF. Są to linki do dokumentacji TF. Reguły (wstępnie zdefiniowane) operacyjne "środowisko uruchomieniowe" lub użytkownika, które automatyzują i/lub kontrolują wymianę i użycie oświadczeń. Te podróże użytkowników są skojarzone z DOWANIU (i LOP). W związku z tym zasady mogą korzystać z różnych LOAs (i LOPs).

- Dostawcy tożsamości i atrybutów albo dostawcy oświadczeń w społeczności zainteresowania i obsługiwane przez nich profile techniczne wraz z (poza pasmem) akredytacji DOWANIU/LOP, które odnoszą się do nich.

- Integracja z inspektorami atrybutów lub dostawcami oświadczeń.

- Jednostki uzależnione w społeczności (przez wnioskowanie).

- Metadane służące do ustanawiania komunikacji sieciowej między uczestnikami. Te metadane, wraz z profilami technicznymi, są używane podczas transakcji do podłączania "współdziałania" w ramach interoperacyjności między jednostką uzależnioną i innymi uczestnikami społeczności.

- Konwersja protokołu (na przykład SAML 2,0, OAuth2, WS-Federation i OpenID Connect Connect).

- Wymagania dotyczące uwierzytelniania.

- Aranżacja wieloskładnikowego, jeśli istnieje.

- Udostępniony schemat dla wszystkich oświadczeń, które są dostępne i są przeznaczone dla uczestników społeczności zainteresowania.

- Wszelkie przekształcenia oświadczeń wraz z możliwym minimalizacją danych w tym kontekście, aby utrzymać wymianę i użycie oświadczeń.

- Powiązanie i szyfrowanie.

- Magazyn oświadczeń.

### <a name="understand-claims"></a>Omówienie oświadczeń

> [!NOTE]
> Odwołujemy się do wszystkich możliwych typów informacji o tożsamości, które mogą być wymieniane jako "oświadczenia": oświadczenia dotyczące poświadczeń uwierzytelniania użytkownika końcowego, tożsamości przed sprawdzeniem, urządzenia komunikacyjnego, lokalizacji fizycznej, atrybutów użytkownika i tak dalej.
>
> Stosujemy termin "oświadczenia" — a nie "atrybuty" — ponieważ w transakcjach online te artefakty danych nie są faktami, które mogą być bezpośrednio zweryfikowane przez jednostkę uzależnioną. Nie są to jednak potwierdzenia lub oświadczenia dotyczące faktów, dla których jednostka uzależniona musi opracować wystarczający poziom zaufania, aby przyznać żądaną transakcję użytkownika końcowego.
>
> Stosujemy również termin "oświadczenia", ponieważ Azure AD B2C zasady niestandardowe korzystające z platformy Identity Experience są zaprojektowane w celu uproszczenia wymiany wszystkich typów informacji o tożsamości cyfrowej w spójny sposób, niezależnie od tego, czy podstawowy protokół jest zdefiniowany do uwierzytelniania użytkownika czy pobierania atrybutów.  Podobnie w przypadku używania "dostawców oświadczeń" do zbiorczego odwoływania się do dostawców tożsamości, dostawców atrybutów i inspektorów atrybutów, gdy nie chcemy rozróżnić określonych funkcji.

W ten sposób określają sposób wymiany informacji o tożsamości między jednostką uzależnioną, tożsamością i dostawcami atrybutów oraz inspektorami atrybutów. Określają, które tożsamości i dostawcy atrybutów są wymagane do uwierzytelniania jednostki uzależnionej. Powinny one być traktowane jako język specyficzny dla domeny (DSL), czyli język komputerowy, który jest wyspecjalizowany dla konkretnej domeny aplikacji z dziedziczeniem, *Jeśli* instrukcje, polimorfizm.

Te zasady stanowią część do odczytu maszynowego konstrukcji TF w Azure AD B2C zasad niestandardowych wykorzystujących strukturę środowiska tożsamości. Obejmują one wszystkie szczegóły operacyjne, w tym metadane dostawców oświadczeń i profile techniczne, definicje schematów oświadczeń, funkcje przekształcania oświadczeń i przedziały użytkowników, które zostały wypełnione, aby ułatwić organizację operacyjną i automatyzację.

Przyjęto założenie, że *dokumenty są żywe* , ponieważ ich zawartość ulegnie zmianie z upływem czasu dla aktywnych uczestników zadeklarowanych w ramach zasad. Istnieje również możliwość, że warunki i postanowienia dotyczące uczestnika mogą ulec zmianie.

Konfiguracja i konserwacja Federacji są znacznie uproszczone przez osłony stron uzależnionych od trwającego zaufania i ponownej konfiguracji łączności jako różne dostawcy oświadczeń/osoby weryfikujące lub opuszczają (społeczność reprezentowana przez program) zestaw zasad.

Współdziałanie jest innym znaczącym wyzwaniem. Dodatkowi dostawcy oświadczeń/weryfikatory muszą być zintegrowane, ponieważ jednostki uzależnione mogą obsługiwać wszystkie wymagane protokoły. Azure AD B2C zasady niestandardowe rozwiązują ten problem przez obsługę protokołów standardowych w branży i przez zastosowanie konkretnych podróży użytkowników do transpozycji żądań, gdy jednostki uzależnione i dostawcy atrybutów nie obsługują tego samego protokołu.

Podróże użytkowników obejmują profile protokołów i metadane, które są używane do podłączania "współdziałania" w sieci i innych uczestników. Istnieją także reguły środowiska uruchomieniowego, które są stosowane do komunikatów żądania/odpowiedzi wymiany informacji o tożsamości w celu wymuszenia zgodności z opublikowanymi zasadami w ramach specyfikacji TF. Pomysłem podróży użytkowników jest kluczowe dostosowanie środowiska klienta. Powoduje również, że system działa na poziomie protokołu.

Na tej podstawie aplikacje i portale jednostki uzależnionej mogą, w zależności od ich kontekstu, wywoływać Azure AD B2C zasady niestandardowe, które wykorzystują platformę obsługi tożsamości, przekazując nazwę określonych zasad i uzyskują precyzyjne informacje o zachowaniu i wymianie informacji bez jakichkolwiek muss, Fuss lub ryzyka.
