---
title: Azure Active Directory B2C zasad niestandardowych | Microsoft Docs
description: Dowiedz się więcej na temat Azure Active Directory B2C zasad niestandardowych.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 06/06/2019
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 043cf83d804803e1b5b47d3ac51bbccaa06e4e87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87116424"
---
# <a name="custom-policies-in-azure-active-directory-b2c"></a>Zasady niestandardowe w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Zasady niestandardowe to pliki konfiguracji, które definiują zachowanie dzierżawy Azure Active Directory B2C (Azure AD B2C). Przepływy użytkowników są wstępnie zdefiniowane w portalu Azure AD B2C dla najbardziej typowych zadań związanych z tożsamościami. Zasady niestandardowe mogą być w pełni edytowane przez dewelopera tożsamości w celu wykonywania wielu różnych zadań.

## <a name="comparing-user-flows-and-custom-policies"></a>Porównywanie przepływów użytkowników i zasad niestandardowych

| Kontekst | Przepływy użytkowników | Zasady niestandardowe |
|-|-------------------|-----------------|
| Użytkownicy docelowi | Wszyscy deweloperzy aplikacji z wiedzą lub bez wiedzy z zakresu tożsamości. | Specjaliści tożsamości, Integratory systemów, konsultanci i wewnętrzne zespoły tożsamości. Są one wygodne w połączeniu z usługą OpenID Connect Connect Flows i wiedzą dostawców tożsamości i uwierzytelniania opartego na oświadczeniach. |
| Metoda konfiguracji | Azure Portal z interfejsem użytkownika przyjaznym dla użytkownika (UI). | Bezpośrednie edytowanie plików XML i przekazywanie ich do Azure Portal. |
| Dostosowanie interfejsu użytkownika | Pełne dostosowanie interfejsu użytkownika, w tym HTML, CSS i JavaScript.<br><br>Obsługa wielu języków za pomocą ciągów niestandardowych. | Ta sama |
| Dostosowanie atrybutu | Atrybuty standardowe i niestandardowe. | Ta sama |
| Zarządzanie tokenami i sesjami | Token niestandardowy i wiele opcji sesji. | Ta sama |
| Dostawcy tożsamości | Wstępnie zdefiniowany dostawca usług lokalnych lub społecznościowych oraz większość OIDC dostawców tożsamości, takich jak Federacja Federacji z dzierżawami Azure Active Directory. | OIDC oparte na standardach, OAUTH i SAML.  Uwierzytelnianie jest również możliwe przy użyciu integracji z interfejsami API REST. |
| Zadania tożsamości | Utwórz konto lub Zaloguj się przy użyciu lokalnego lub wielu kont społecznościowych.<br><br>Samoobsługowe resetowanie hasła.<br><br>Edytowanie profilu.<br><br>Multi-Factor Authentication.<br><br>Dostosowywanie tokenów i sesji.<br><br>Dostęp do przepływów tokenów. | Wykonaj te same zadania co przepływy użytkownika przy użyciu niestandardowych dostawców tożsamości lub użyj zakresów niestandardowych.<br><br>Zainicjuj obsługę konta użytkownika w innym systemie w czasie rejestracji.<br><br>Wyślij powitalną wiadomość e-mail przy użyciu własnego dostawcy usługi poczty e-mail.<br><br>Użyj magazynu użytkownika poza Azure AD B2C.<br><br>Sprawdź poprawność dostarczonych przez użytkownika informacji z zaufanym systemem przy użyciu interfejsu API. |

## <a name="policy-files"></a>Pliki zasad

Są używane te trzy typy plików zasad:

- **Plik podstawowy** — zawiera większość definicji. Zaleca się dokonanie minimalnej liczby zmian w tym pliku, aby ułatwić rozwiązywanie problemów i długoterminową konserwację zasad.
- **Plik rozszerzeń** — zawiera unikatowe zmiany konfiguracji dla dzierżawy.
- **Plik jednostki uzależnionej (RP)** — pojedynczy plik skoncentrowany na zadaniach, który jest wywoływany bezpośrednio przez aplikację lub usługę (określaną również jako jednostka uzależniona). Każde unikatowe zadanie wymaga własnego RP i w zależności od wymagań dotyczących znakowania może to być liczba "całkowita liczba aplikacji x łącznie z liczbą przypadków użycia".

Przepływy użytkownika w Azure AD B2C są zgodne z wzorcem pliku przedstawionym powyżej, ale deweloper widzi tylko plik RP, podczas gdy Azure Portal wprowadza zmiany w tle do pliku rozszerzeń.

Chociaż istnieją trzy typy plików zasad, nie można ograniczyć do trzech plików. Może istnieć wiele plików każdego typu plików. Na przykład, jeśli nie chcesz wprowadzać zmian w pliku rozszerzeń, możesz utworzyć plik Extensions2, aby rozszerzyć plik rozszerzeń.

## <a name="custom-policy-core-concepts"></a>Podstawowe pojęcia dotyczące zasad

Usługa zarządzania tożsamościami i dostępem klienta (CIAM) na platformie Azure obejmuje następujące:

- Katalog użytkownika, który jest dostępny przy użyciu Microsoft Graph i który zawiera dane użytkownika zarówno dla kont lokalnych, jak i kont federacyjnych.
- Dostęp do **struktury obsługi tożsamości** , która organizuje relacje zaufania między użytkownikami i jednostkami i przekazuje oświadczenia między nimi w celu ukończenia zadania zarządzania tożsamością lub dostępem.
- Usługa tokenu zabezpieczającego (STS), która wystawia tokeny identyfikatora, tokeny odświeżania i tokeny dostępu (oraz równoważne potwierdzenia SAML) i sprawdza ich poprawność, aby chronić zasoby.

Azure AD B2C współdziała z dostawcami tożsamości, użytkownikami, innymi systemami oraz z lokalnym katalogiem użytkownika w sekwencji w celu osiągnięcia zadania tożsamości. Na przykład Zaloguj użytkownika, Zarejestruj nowego użytkownika lub zresetuj hasło. Struktura środowiska tożsamości i zasady (nazywane również przeprowadzeniem przez użytkownika lub zasadami struktury zaufania) ustanawiają relację zaufania innej firmy i jawnie definiują aktorów, akcje, protokoły i sekwencję kroków do ukończenia.

Platforma obsługi tożsamości jest w pełni konfigurowalną, opartą na chmurze platformą platformy Azure, która organizuje relacje zaufania między jednostkami w standardowych formatach protokołów, takimi jak OpenID Connect Connect, OAuth, SAML i kilka niestandardowymi, na przykład wymianę oświadczeń systemu opartego na interfejsie API REST. Struktura tworzy przyjazne dla użytkownika środowisko, które obsługuje język HTML i CSS.

Niestandardowe zasady są reprezentowane jako jeden lub kilka plików w formacie XML, które odwołują się do siebie nawzajem zgodnie z łańcuchem hierarchii. Elementy XML definiują schemat oświadczeń, przekształcenia oświadczeń, definicje zawartości, dostawcy oświadczeń, profile techniczne oraz kroki aranżacji przechodzenia przez użytkownika między innymi elementami. Zasady niestandardowe są dostępne jako jeden lub kilka plików XML, które są wykonywane przez strukturę środowiska tożsamości w przypadku wywołania przez jednostkę uzależnioną. Deweloperzy konfigurujący zasady niestandardowe muszą definiować relacje zaufania w szczegółowy sposób, aby obejmowały punkty końcowe metadanych, dokładne definicje wymiany oświadczeń i konfigurować klucze tajne, klucze i certyfikaty, zgodnie z potrzebami każdego dostawcy tożsamości.

### <a name="inheritance-model"></a>Model dziedziczenia

Gdy aplikacja wywołuje plik zasad RP, struktura środowiska tożsamości w Azure AD B2C dodaje wszystkie elementy z pliku bazowego, z pliku rozszerzeń, a następnie z pliku zasad RP, aby utworzyć bieżące zasady.  Elementy tego samego typu i nazwy w pliku RP przesłonią te w rozszerzeniach, a rozszerzenia przesłaniają bazę.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do zasad niestandardowych](custom-policy-get-started.md)
