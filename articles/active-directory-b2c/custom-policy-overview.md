---
title: Zasady niestandardowe usługi Azure Active Directory B2C | Dokumenty firmy Microsoft
description: Dowiedz się więcej o zasadach niestandardowych usługi Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f18f44208b97ab5bc8d9cd9ff01d604c62deb963
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678159"
---
# <a name="custom-policies-in-azure-active-directory-b2c"></a>Zasady niestandardowe w usłudze Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Zasady niestandardowe to pliki konfiguracyjne definiujące zachowanie dzierżawy usługi Azure Active Directory B2C (Azure AD B2C). Przepływy użytkowników są wstępnie zdefiniowane w portalu usługi Azure AD B2C dla najczęstszych zadań tożsamości. Zasady niestandardowe mogą być w pełni edytowane przez dewelopera tożsamości, aby wykonać wiele różnych zadań.

## <a name="comparing-user-flows-and-custom-policies"></a>Porównywanie przepływów użytkowników i zasad niestandardowych

| | Przepływy użytkowników | Zasady niestandardowe |
|-|-------------------|-----------------|
| Docelowi użytkownicy | Wszyscy deweloperzy aplikacji z wiedzą o tożsamości lub bez niej. | Profesjonaliści tożsamości, integratorzy systemów, konsultanci i waże zespoły tożsamości. Są wygodne z przepływów OpenID Connect i zrozumieć dostawców tożsamości i uwierzytelniania opartego na oświadczeniach. |
| Metoda konfiguracji | Portal Azure z przyjaznym dla użytkownika interfejsem użytkownika (UI). | Bezpośrednio edytuje pliki XML, a następnie przekazuje do witryny Azure portal. |
| Dostosowywanie interfejsu użytkownika | Pełne dostosowanie interfejsu użytkownika, w tym HTML, CSS i JavaScript.<br><br>Obsługa wielojęzycznego języka za pomocą ciągów niestandardowych. | Ta sama |
| Dostosowywanie atrybutów | Atrybuty standardowe i niestandardowe. | Ta sama |
| Zarządzanie tokenami i sesjami | Niestandardowy token i wiele opcji sesji. | Ta sama |
| Dostawcy tożsamości | Wstępnie zdefiniowany dostawca lokalny lub społeczny i większość dostawców tożsamości OIDC, takich jak federacja z dzierżawami usługi Azure Active Directory. | Oparte na standardach OIDC, OAUTH i SAML.  Uwierzytelnianie jest również możliwe przy użyciu integracji z interfejsami API REST. |
| Zadania tożsamości | Zarejestruj się lub zaloguj się za pomocą lokalnych lub wielu kont społecznościowych.<br><br>Samoobsługowe resetowanie hasła.<br><br>Edycja profilu.<br><br>Uwierzytelnianie wieloskładnikowe.<br><br>Dostosowywanie tokenów i sesji.<br><br>Przepływy tokenu dostępu. | Wykonaj te same zadania, co przepływy użytkowników przy użyciu niestandardowych dostawców tożsamości lub użyj zakresów niestandardowych.<br><br>Aprowizuj konto użytkownika w innym systemie w momencie rejestracji.<br><br>Wyślij powitalną wiadomość e-mail za pomocą własnego dostawcy usług poczty e-mail.<br><br>Użyj magazynu użytkowników poza usługą Azure AD B2C.<br><br>Sprawdzanie poprawności informacji dostarczonych przez użytkownika za pomocą zaufanego systemu przy użyciu interfejsu API. |

## <a name="policy-files"></a>Pliki zasad

Te trzy typy plików zasad są używane:

- **Plik podstawowy** - zawiera większość definicji. Zaleca się wprowadzenie minimalnej liczby zmian w tym pliku, aby ułatwić rozwiązywanie problemów i długoterminowe utrzymanie zasad.
- **Plik rozszerzeń** — przechowuje unikatowe zmiany konfiguracji dla dzierżawy.
- **Plik jednostki uzależniona (RP)** — pojedynczy plik skoncentrowany na zadaniach, który jest wywoływany bezpośrednio przez aplikację lub usługę (znany również jako jednostka uzależniona). Każde unikatowe zadanie wymaga własnego RP i w zależności od wymagań dotyczących znakowania liczba może być "całkowita liczba aplikacji x całkowita liczba przypadków użycia."

Przepływy użytkownika w usłudze Azure AD B2C są zgodne ze wzorcem pliku przedstawionym powyżej, ale deweloper widzi tylko plik RP, podczas gdy portal Azure wywrze zmiany w tle pliku rozszerzeń.

Chociaż istnieją trzy typy plików zasad, nie są ograniczone tylko do trzech plików. Może być wiele plików każdego typu pliku. Na przykład jeśli nie chcesz wprowadzać zmian w pliku Rozszerzeń, możesz utworzyć plik Rozszerzenia2, aby jeszcze bardziej rozszerzyć plik Rozszerzeń.

## <a name="custom-policy-core-concepts"></a>Podstawowe pojęcia dotyczące zasad niestandardowych

Usługa zarządzania tożsamością i dostępem klienta (CIAM) na platformie Azure obejmuje:

- Katalog użytkowników, który jest dostępny przy użyciu programu Microsoft Graph i który przechowuje dane użytkownika zarówno dla kont lokalnych, jak i kont federatywnych.
- Dostęp do **struktury środowiska tożsamości,** która organizuje zaufanie między użytkownikami i jednostkami i przekazuje oświadczenia między nimi, aby ukończyć zadanie zarządzania tożsamością lub dostępem.
- Usługa tokenu zabezpieczającego (STS), która wystawia tokeny identyfikatorów, odświeżanie tokenów i tokenów dostępu (i równoważne potwierdzenia SAML) i sprawdza ich poprawność w celu ochrony zasobów.

Usługa Azure AD B2C współdziała z dostawcami tożsamości, użytkownikami, innymi systemami i z lokalnym katalogiem użytkowników w sekwencji, aby osiągnąć zadanie tożsamości. Na przykład zaloguj się do użytkownika, zarejestruj nowego użytkownika lub zresetuj hasło. Struktura środowiska tożsamości i zasady (nazywane również podróżą użytkownika lub zasadami ramowymi zaufania) ustanawia zaufanie wielu stron i jawnie definiuje podmioty, akcje, protokoły i sekwencję kroków do wykonania.

Struktura środowiska tożsamości jest w pełni konfigurowalną, opartą na zasadach, opartą na chmurze platformą Azure, która organizuje zaufanie między jednostkami w standardowych formatach protokołu, takich jak OpenID Connect, OAuth, SAML i kilka niestandardowych, na przykład wymiana oświadczeń opartych na interfejsie REST interfejsu API. Struktura tworzy przyjazne dla użytkownika, białe etykiety środowisk, które obsługują HTML i CSS.

Niestandardowe zasady są reprezentowane jako jeden lub kilka plików w formacie XML, które odwołują się do siebie nawzajem zgodnie z łańcuchem hierarchii. Elementy XML definiują schemat oświadczeń, przekształcenia oświadczeń, definicje zawartości, dostawców oświadczeń, profile techniczne i kroki aranżacji podróży użytkownika, wśród innych elementów. Zasady niestandardowe są dostępne jako jeden lub kilka plików XML, które są wykonywane przez platformę środowiska tożsamości, gdy są wywoływane przez jednostkę uzależnioną. Deweloperzy konfigurujący zasady niestandardowe muszą dokładnie definiować zaufane relacje, aby uwzględnić punkty końcowe metadanych, dokładne definicje wymiany oświadczeń i skonfigurować wpisy tajne, klucze i certyfikaty zgodnie z potrzebami każdego dostawcy tożsamości.

### <a name="inheritance-model"></a>Model dziedziczenia

Gdy aplikacja wywołuje plik zasad RP, identity experience framework w usłudze Azure AD B2C dodaje wszystkie elementy z pliku podstawowego, z pliku rozszerzeń, a następnie z pliku zasad RP, aby zebrać bieżące zasady w życie.  Elementy tego samego typu i nazwy w pliku RP zastąpią te w rozszerzeniach, a rozszerzenia zastępują bazę.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do zasad niestandardowych](custom-policy-get-started.md)
