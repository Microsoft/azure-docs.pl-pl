---
title: Omówienie kluczy zasad — Azure Active Directory B2C
description: Dowiedz się więcej o typach kluczy zasad szyfrowania, które mogą być używane w programie Azure Active Directory B2C do podpisywania i uwierzytelniania tokenów, wpisów tajnych klienta, certyfikatów i haseł.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/19/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a41717e9be0918dead9f77a5f5472494d734b38a
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717535"
---
# <a name="overview-of-policy-keys-in-azure-active-directory-b2c"></a>Omówienie kluczy zasad w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure Active Directory B2C (Azure AD B2C) przechowuje wpisy tajne i certyfikaty w postaci kluczy zasad w celu ustanowienia zaufania z usługami, z których jest zintegrowana. Te relacje zaufania składają się z:

- Zewnętrzni dostawcy tożsamości
- Nawiązywanie połączenia z [usługami interfejsu API REST](restful-technical-profile.md)
- Podpisywanie i szyfrowanie tokenów

 W tym artykule omówiono, co należy wiedzieć o kluczach zasad używanych przez Azure AD B2C.

> [!NOTE]
> Obecnie konfiguracja kluczy zasad jest ograniczona tylko do [zasad niestandardowych.](./user-flow-overview.md)

Wpisy tajne i certyfikaty można skonfigurować w celu ustanowienia zaufania między usługami w Azure Portal w menu **Klucze zasad.** Klucze mogą być symetryczne lub asymetryczne. *Kryptografia* symetryczna lub kryptografia klucza prywatnego to miejsce, w którym wspólny klucz tajny jest używany do szyfrowania i odszyfrowywania danych. *Kryptografia* asymetryczna (kryptografia klucza publicznego) to system kryptograficzny, który używa par kluczy składających się z kluczy publicznych udostępnianych aplikacji jednostki zależnej i kluczy prywatnych, które są znane tylko Azure AD B2C.

## <a name="policy-keyset-and-keys"></a>Zestaw kluczy zasad i klucze

Zasób najwyższego poziomu dla kluczy zasad w Azure AD B2C to **kontener zestawu** kluczy. Każdy zestaw kluczy zawiera co najmniej jeden **klucz**. Klucz ma następujące atrybuty:

| Atrybut |  Wymagane | Uwagi |
| --- | --- |--- |
| `use` | Tak | Użycie: określa przeznaczenie klucza publicznego. Szyfrowanie danych `enc` lub weryfikowanie podpisu na danych `sig` .|
| `nbf`| Nie | Data i godzina aktywacji. |
| `exp`| Nie | Data i godzina wygaśnięcia. |

Zalecamy ustawienie wartości aktywacji klucza i wygaśnięcia zgodnie ze standardami PKI. Może być konieczne okresowe obracanie tych certyfikatów ze względów bezpieczeństwa lub zasad. Na przykład można mieć zasady rotacji wszystkich certyfikatów co rok.

Aby utworzyć klucz, możesz wybrać jedną z następujących metod:

- **Ręcznie** — utwórz klucz tajny z ciągiem, który zdefiniujesz. Klucz tajny jest kluczem symetrycznym. Można ustawić daty aktywacji i wygaśnięcia.
- **Wygenerowano** — automatyczne generowanie klucza. Można ustawić daty aktywacji i wygaśnięcia. Dostępne są dwie opcje:
  - **Klucz** tajny — generuje klucz symetryczny.
  - **RSA** — generuje parę kluczy (klucze asymetryczne).
- **Przekaż** — przekaż certyfikat lub klucz PKCS12. Certyfikat musi zawierać klucze prywatne i publiczne (klucze asymetryczne).

## <a name="key-rollover"></a>Przewłaczanie klucza

Ze względów bezpieczeństwa Azure AD B2C przewłacać klucze okresowo lub natychmiast w razie zagrożenia. Każda aplikacja, dostawca tożsamości lub interfejs API REST, który integruje się z usługą Azure AD B2C, powinna być przygotowana do obsługi zdarzenia przechowania kluczy, niezależnie od tego, jak często może to wystąpić. W przeciwnym razie, jeśli aplikacja lub Azure AD B2C spróbuje użyć wygasłego klucza do wykonania operacji kryptograficznych, żądanie logowania nie powiedzie się.

Jeśli zestaw Azure AD B2C kluczy ma wiele kluczy, tylko jeden z kluczy jest aktywny w dowolnym momencie, na podstawie następujących kryteriów:

- Aktywacja klucza opiera się na **dacie aktywacji**.
  - Klucze są sortowane według daty aktywacji w kolejności rosnącej. Klucze z datami aktywacji w przyszłości będą wyświetlane niżej na liście. Klucze bez daty aktywacji znajdują się w dolnej części listy.
  - Jeśli bieżąca data i godzina jest większa niż data aktywacji klucza, program Azure AD B2C klucz i przestanie używać wcześniejszego aktywnego klucza.
- Gdy upłynie czas wygaśnięcia bieżącego klucza, a kontener  kluczy zawiera  nowy klucz o ważności nie wcześniej niż i w czasie wygaśnięcia, nowy klucz stanie się aktywny automatycznie.
- Jeśli upłynął czas wygaśnięcia bieżącego klucza, a kontener kluczy nie  zawiera  nowego klucza o ważności nie wcześniej niż i czasie wygaśnięcia, Azure AD B2C nie będzie mógł użyć wygasłego klucza.  Azure AD B2C zostanie wyświetlony komunikat o błędzie w zależności od składnika zasad niestandardowych. Aby uniknąć tego problemu, możesz utworzyć klucz domyślny bez aktywacji i dat wygaśnięcia jako sieć bezpieczeństwa.
- Punkt końcowy klucza (JWKS URI) dobrze znanego punktu końcowego konfiguracji OpenId Connect odzwierciedla klucze skonfigurowane w kontenerze kluczy, gdy klucz jest przywołyny w profilu technicznym [JwtIssuer.](./jwt-issuer-technical-profile.md) Aplikacja korzystająca z biblioteki OIDC automatycznie pobierze te metadane, aby upewnić się, że używa prawidłowych kluczy do weryfikowania tokenów. Aby uzyskać więcej informacji, dowiedz się, jak używać [biblioteki uwierzytelniania firmy Microsoft,](../active-directory/develop/msal-b2c-overview.md)która zawsze automatycznie pobiera najnowsze klucze podpisywania tokenu.

## <a name="policy-key-management"></a>Zarządzanie kluczami zasad

Aby uzyskać bieżący aktywny klucz w kontenerze kluczy, użyj punktu końcowego interfejsu API Microsoft Graph [getActiveKey.](/graph/api/trustframeworkkeyset-getactivekey)

Aby dodać lub usunąć klucze podpisywania i szyfrowania:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz **ikonę Katalog i subskrypcja** na pasku narzędzi portalu, a następnie wybierz katalog zawierający Azure AD B2C dzierżawy.
1. W Azure Portal wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Na stronie przeglądu w obszarze **Zasady** wybierz pozycję **Identity Experience Framework**.
1. Wybieranie **kluczy zasad** 
    1. Aby dodać nowy klucz, wybierz pozycję **Dodaj**.
    1. Aby usunąć nowy klucz, wybierz klucz, a następnie wybierz pozycję **Usuń.** Aby usunąć klucz, wpisz nazwę kontenera kluczy do usunięcia. Azure AD B2C usunie klucz i utworzy kopię klucza z sufiksem bak.

### <a name="replace-a-key"></a>Zastępowanie klucza

Kluczy w zestawie kluczy nie można zastąpić ani wymiennie. Jeśli musisz zmienić istniejący klucz:

- Zalecamy dodanie nowego klucza z **datą aktywacji ustawioną** na bieżącą datę i godzina. Azure AD B2C uaktywni nowy klucz i przestanie używać wcześniejszego aktywnego klucza.
- Alternatywnie możesz utworzyć nowy zestaw kluczy z prawidłowymi kluczami. Zaktualizuj zasady, aby używać nowego zestawu kluczy, a następnie usuń stary zestaw kluczy. 

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak używać Microsoft Graph do automatyzowania wdrażania [zestawu](microsoft-graph-operations.md#trust-framework-policy-keyset) kluczy [i kluczy](microsoft-graph-operations.md#trust-framework-policy-key) zasad.

::: zone-end
