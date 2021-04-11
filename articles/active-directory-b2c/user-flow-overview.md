---
title: Przepływy użytkownika i zasady niestandardowe w Azure Active Directory B2C | Microsoft Docs
titleSuffix: Azure AD B2C
description: Dowiedz się więcej na temat wbudowanych przepływów użytkowników i zasad niestandardowych Extensible Policy Framework programu Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2e4dbc5178bec3a5b1f0931267465879f604f36f
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226012"
---
# <a name="user-flows-and-custom-policies-overview"></a>Omówienie przepływów użytkowników i zasad niestandardowych

W Azure AD B2C można zdefiniować logikę biznesową, którą użytkownicy obserwują w celu uzyskania dostępu do aplikacji. Można na przykład określić sekwencję kroków, które użytkownicy wykonują podczas logowania, zarejestrować się, edytować profil lub zresetować hasło. Po zakończeniu sekwencji użytkownik uzyskuje token i uzyska dostęp do aplikacji. 

W Azure AD B2C istnieją dwa sposoby zapewnienia środowiska użytkownika tożsamości:

* **Przepływy użytkowników** są wstępnie zdefiniowanymi, wbudowanymi, konfigurowalnymi zasadami, które udostępniamy, aby można było tworzyć nowe środowiska tworzenia konta, logowania i edytowania zasad w ciągu kilku minut.

* **Zasady niestandardowe** umożliwiają tworzenie własnych podróży użytkowników w przypadku złożonych scenariuszy obsługi tożsamości.

Poniższy zrzut ekranu przedstawia interfejs użytkownika ustawień przepływu użytkowników, a nie niestandardowe pliki konfiguracji zasad.

![Zrzut ekranu przedstawia interfejs użytkownika ustawień przepływu użytkowników, a nie niestandardowe pliki konfiguracji zasad.](media/user-flow-overview/user-flow-vs-custom-policy.png)

Ten artykuł zawiera krótkie omówienie przepływów użytkowników i zasad niestandardowych oraz ułatwia wybór metody, która będzie Najlepsza dla potrzeb Twojej firmy.

## <a name="user-flows"></a>Przepływy użytkowników

Aby skonfigurować Najczęstsze zadania tożsamości, Azure Portal obejmuje kilka wstępnie zdefiniowanych i konfigurowalnych zasad o nazwie *przepływy użytkownika*.

Można skonfigurować ustawienia przepływu użytkownika, takie jak te, aby kontrolować zachowania dotyczące środowiska tożsamości w aplikacjach:

* Typy kont używane do logowania, takie jak konta społecznościowe, takie jak Facebook lub konta lokalne używające adresu e-mail i hasła do logowania
* Atrybuty, które mają być zbierane od konsumenta, takie jak imię i nazwisko, kod pocztowy lub kraj/region miejsca zamieszkania
* Usługa Azure AD Multi-Factor Authentication (MFA)
* Dostosowanie interfejsu użytkownika
* Zestaw oświadczeń w tokenie odbieranych przez aplikację po zakończeniu przepływu użytkownika przez użytkownika
* Zarządzanie sesjami
* ... i nie tylko

Większość typowych scenariuszy tożsamości dla aplikacji można definiować i implementować efektywnie z przepływami użytkowników. Zalecamy używanie wbudowanych przepływów użytkowników, chyba że masz skomplikowane scenariusze podróży użytkowników, które wymagają pełnej elastyczności zasad niestandardowych.

## <a name="custom-policies"></a>Zasady niestandardowe

Zasady niestandardowe to pliki konfiguracji definiujące zachowanie środowiska użytkownika dzierżawy Azure AD B2C. Podczas gdy przepływy użytkowników są wstępnie zdefiniowane w portalu Azure AD B2C dla najbardziej typowych zadań związanych z tożsamościami, zasady niestandardowe mogą być w pełni edytowane przez dewelopera tożsamości w celu wykonywania wielu różnych zadań.

Zasada niestandardowa jest w pełni konfigurowana i oparta na zasadach. Organizacja organizuje relacje zaufania między jednostkami w protokołach standardowych. Na przykład OpenID Connect Connect, OAuth, SAML i kilka niestandardowymi, na przykład wymiany oświadczeń system-to-based opartego na interfejsie API REST. Struktura tworzy przyjazne dla użytkownika i białe środowisko.

Zasady niestandardowe umożliwiają konstruowanie podróży użytkowników przy użyciu dowolnej kombinacji kroków. Na przykład:

* Sfederować z innymi dostawcami tożsamości
* Wyzwania uwierzytelniania wieloskładnikowego (MFA) w pierwszej kolejności i innej firmy
* Zbierz dane wprowadzane przez użytkownika
* Integrowanie z systemami zewnętrznymi przy użyciu komunikacji interfejsu API REST

Każda podróż użytkownika jest definiowana przez zasady. Można utworzyć dowolną liczbę lub kilka zasad, które będą potrzebne do zapewnienia najlepszego środowiska użytkownika w organizacji.

![Diagram przedstawiający przykład złożonej podróży użytkownika włączonej przez IEF](media/user-flow-overview/custom-policy-diagram.png)

Zasady niestandardowe są definiowane przez kilka plików XML, które odwołują się do siebie nawzajem w łańcuchu hierarchicznym. Elementy XML definiują schemat oświadczeń, przekształcenia oświadczeń, definicje zawartości, dostawcy oświadczeń, profile techniczne, etapy organizowania podróży użytkowników i inne aspekty środowiska tożsamości.

Zaawansowana elastyczność zasad niestandardowych jest najbardziej odpowiednia w przypadku konieczności tworzenia złożonych scenariuszy tożsamości. Deweloperzy konfigurujący zasady niestandardowe muszą definiować relacje zaufania w szczegółowy sposób, aby obejmowały punkty końcowe metadanych, dokładne definicje wymiany oświadczeń i konfigurować klucze tajne, klucze i certyfikaty, zgodnie z potrzebami każdego dostawcy tożsamości.

Dowiedz się więcej na temat zasad niestandardowych w [zasadach niestandardowych w Azure Active Directory B2C](custom-policy-overview.md).

## <a name="comparing-user-flows-and-custom-policies"></a>Porównywanie przepływów użytkowników i zasad niestandardowych

Poniższa tabela zawiera szczegółowe porównanie scenariuszy, z którymi można Azure AD B2C przepływy użytkownika i zasady niestandardowe.

| Kontekst | Przepływy użytkowników | Zasady niestandardowe |
|-|-------------------|-----------------|
| Użytkownicy docelowi | Wszyscy deweloperzy aplikacji z wiedzą lub bez wiedzy z zakresu tożsamości. | Specjaliści tożsamości, Integratory systemów, konsultanci i wewnętrzne zespoły tożsamości. Są one wygodne w połączeniu z usługą OpenID Connect Connect Flows i wiedzą dostawców tożsamości i uwierzytelniania opartego na oświadczeniach. |
| Metoda konfiguracji | Azure Portal z interfejsem użytkownika przyjaznym dla użytkownika (UI). | Bezpośrednie edytowanie plików XML i przekazywanie ich do Azure Portal. |
| Dostosowanie interfejsu użytkownika | [Pełne dostosowanie interfejsu użytkownika](customize-ui-with-html.md) , w tym HTML, CSS i [JavaScript](javascript-and-page-layout.md).<br><br>[Obsługa wielu języków](language-customization.md) za pomocą ciągów niestandardowych. | Ta sama |
| Dostosowanie atrybutu | Atrybuty standardowe i niestandardowe. | Ta sama |
| Zarządzanie tokenami i sesjami | [Dostosuj zachowanie tokenów](configure-tokens.md) i [sesji](session-behavior.md). | Ta sama |
| Dostawcy tożsamości | [Wstępnie zdefiniowany dostawca lokalny](identity-provider-local.md) lub [społeczny](add-identity-provider.md), taki jak federacyjny z Azure Active Directory dzierżawcami. | OIDC oparte na standardach, OAUTH i SAML.  Uwierzytelnianie jest również możliwe przy użyciu integracji z interfejsami API REST. |
| Zadania tożsamości | [Utwórz konto lub Zaloguj](add-sign-up-and-sign-in-policy.md) się przy użyciu lokalnego lub wielu kont społecznościowych.<br><br>[Samoobsługowe resetowanie hasła](add-password-reset-policy.md).<br><br>[Edytowanie profilu](add-profile-editing-policy.md).<br><br>Multi-Factor Authentication.<br><br>Dostęp do przepływów tokenów. | Wykonaj te same zadania co przepływy użytkownika przy użyciu niestandardowych dostawców tożsamości lub użyj zakresów niestandardowych.<br><br>Zainicjuj obsługę konta użytkownika w innym systemie w czasie rejestracji.<br><br>Wyślij powitalną wiadomość e-mail przy użyciu własnego dostawcy usługi poczty e-mail.<br><br>Użyj magazynu użytkownika poza Azure AD B2C.<br><br>Sprawdź poprawność dostarczonych przez użytkownika informacji z zaufanym systemem przy użyciu interfejsu API. |

## <a name="application-integration"></a>Integracja aplikacji

W razie konieczności można utworzyć wiele przepływów użytkowników lub niestandardowych zasad różnych typów w dzierżawie. Zarówno przepływy użytkownika, jak i zasady niestandardowe mogą być ponownie używane między aplikacjami. Ta elastyczność umożliwia definiowanie i modyfikowanie środowisk tożsamości przy minimalnych lub niewielkich zmianach w kodzie. 

Gdy użytkownik chce zalogować się do aplikacji, inicjuje żądanie autoryzacji dla przepływu użytkownika lub niestandardowego punktu końcowego dostarczonego przez zasady. Przepływ użytkownika lub zasady niestandardowe definiują i kontrolują środowisko użytkownika. Po ukończeniu przepływu użytkownika Azure AD B2C generuje token, a następnie przekierowuje użytkownika z powrotem do aplikacji.

![Aplikacja mobilna ze strzałkami przedstawiająca przepływ między stroną logowania Azure AD B2C](media/user-flow-overview/app-integration.png)

Wiele aplikacji może korzystać z tego samego przepływu użytkownika lub zasad niestandardowych. Pojedyncza aplikacja może korzystać z wielu przepływów użytkowników lub zasad niestandardowych.

Na przykład, aby zalogować się do aplikacji, aplikacja używa przepływu *rejestracji lub logowania* . Po zalogowaniu się użytkownika mogą chcieć edytować swój profil. Aby edytować profil, aplikacja inicjuje inne żądanie autoryzacji, tym razem korzystając z *profilu Edytuj* przepływ użytkownika.

Aplikacja wyzwala przepływ użytkownika przy użyciu standardowego żądania uwierzytelniania HTTP, zawierającego przepływ użytkownika lub nazwę zasad niestandardowych. Jako odpowiedź otrzymano dostosowany [token](tokens-overview.md) .


## <a name="next-steps"></a>Następne kroki

- Aby utworzyć zalecane przepływy użytkowników, postępuj zgodnie z instrukcjami podanymi w [samouczku: tworzenie przepływu użytkownika](tutorial-create-user-flows.md).
- Dowiedz się więcej o [wersjach przepływu użytkownika w Azure AD B2C](user-flow-versions.md).
- Dowiedz się więcej o [zasadach niestandardowych Azure AD B2C](custom-policy-overview.md).
