---
title: Omówienie kluczy zasad — Azure Active Directory B2C
description: Informacje o typach kluczy zasad szyfrowania, które mogą być używane w Azure Active Directory B2C do podpisywania i weryfikowania tokenów, kluczy tajnych klienta, certyfikatów i haseł.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 30348d7ca12ded2d1f4b0522a7cabeadf0553a07
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94953359"
---
# <a name="overview-of-policy-keys-in-azure-active-directory-b2c"></a>Omówienie kluczy zasad w Azure Active Directory B2C

Azure Active Directory B2C (Azure AD B2C) przechowuje wpisy tajne i certyfikaty w formie kluczy zasad, aby ustanowić relację zaufania z usługami, które integrują się z usługą. Te relacje zaufania składają się z:

- Zewnętrzni dostawcy tożsamości
- Łączenie z [usługami interfejsu API REST](restful-technical-profile.md)
- Podpisywanie i szyfrowanie tokenów

 W tym artykule omówiono, co należy wiedzieć o kluczach zasad, które są używane przez Azure AD B2C.

> [!NOTE]
> Obecnie konfiguracja kluczy zasad jest ograniczona tylko do [zasad niestandardowych](./custom-policy-get-started.md) .

Można skonfigurować wpisy tajne i certyfikaty do ustanawiania relacji zaufania między usługami w Azure Portal w menu **klucze zasad** . Klucze mogą być symetryczne lub asymetryczne. Kryptografia *symetryczne* lub Kryptografia klucza prywatnego polega na tym, że wspólny klucz tajny jest używany do szyfrowania i odszyfrowywania danych. Kryptografia *asymetryczne* lub Kryptografia klucza publicznego jest systemem kryptograficznym, który używa par kluczy, składających się z kluczy publicznych, które są współużytkowane z aplikacją jednostki uzależnionej i kluczami prywatnymi, które są znane tylko Azure AD B2C.

## <a name="policy-keyset-and-keys"></a>Klucze kluczy i kluczy zasad

Zasób najwyższego poziomu dla kluczy zasad w Azure AD B2C jest kontenerem **zestawu kluczy** . Każdy zestaw kluczy zawiera co najmniej jeden **klucz**. Klucz ma następujące atrybuty:

| Atrybut |  Wymagane | Uwagi |
| --- | --- |--- |
| `use` | Tak | Użycie: określa zamierzone użycie klucza publicznego. Szyfrowanie danych `enc` lub Weryfikowanie podpisu w danych `sig` .|
| `nbf`| Nie | Data i godzina aktywacji. |
| `exp`| Nie | Data i godzina wygaśnięcia. |

Zalecamy ustawienie aktywacji klucza i wartości wygaśnięcia zgodnie ze standardami infrastruktury kluczy publicznych. Konieczne może być okresowe obrócenie tych certyfikatów ze względów bezpieczeństwa lub zasad. Na przykład może istnieć zasada do rotacji wszystkich certyfikatów co roku.

Aby utworzyć klucz, możesz wybrać jedną z następujących metod:

- **Ręczne** — Utwórz wpis tajny z zdefiniowanym ciągiem. Wpis tajny jest kluczem symetrycznym. Można ustawić daty aktywacji i wygaśnięcia.
- **Wygenerowany** — automatycznie Generuj klucz. Można ustawić daty aktywacji i wygaśnięcia. Dostępne są dwie opcje:
  - **Secret** — generuje klucz symetryczny.
  - **RSA** — generuje parę kluczy (klucze asymetryczne).
- **Przekaż** — Przekaż certyfikat lub klucz PKCS12. Certyfikat musi zawierać klucze prywatne i publiczne (klucze asymetryczne).

## <a name="key-rollover"></a>Przerzucanie kluczy

Ze względów bezpieczeństwa Azure AD B2C może okresowo wycofać klucze lub natychmiast w przypadku awarii. Dowolna aplikacja, dostawca tożsamości lub interfejs API REST, który integruje się z Azure AD B2C powinien zostać przygotowany do obsługi zdarzenia przerzucania klucza niezależnie od tego, jak często mogą wystąpić. W przeciwnym razie, jeśli aplikacja lub Azure AD B2C próbuje użyć klucza wygasłego do wykonania operacji kryptograficznej, żądanie logowania zakończy się niepowodzeniem.

Jeśli zestaw kluczy Azure AD B2C ma wiele kluczy, tylko jeden z kluczy jest aktywny w dowolnym momencie, na podstawie następujących kryteriów:

- Aktywacja klucza opiera się na **dacie aktywacji**.
  - Klucze są sortowane według daty aktywacji w kolejności rosnącej. Klucze z datami aktywacji w przyszłości pojawiają się na liście poniżej. Klucze bez daty aktywacji znajdują się u dołu listy.
  - Gdy bieżąca data i godzina jest późniejsza niż Data aktywacji klucza, Azure AD B2C uaktywni klucz i zaprzestanie korzystania z wcześniejszego aktywnego klucza.
- Po upływie czasu wygaśnięcia bieżącego klucza, gdy kontener kluczy zawiera nowy klucz z prawidłowym terminem *niewcześniejszym niż* czas *wygaśnięcia* , nowy klucz zostanie automatycznie uaktywniony.
- Gdy upłynął czas wygaśnięcia bieżącego klucza *i kontener kluczy nie zawiera* nowego klucza z prawidłowym terminem *nie wcześniejszy niż* i czas *wygaśnięcia* , Azure AD B2C nie będzie można użyć klucza wygasłego. Azure AD B2C zgłosi komunikat o błędzie w ramach zależnego składnika zasad niestandardowych. Aby uniknąć tego problemu, można utworzyć klucz domyślny bez aktywacji i daty ważności jako sieci bezpieczeństwa.
- Punkt końcowy klucza (identyfikator URI zestawu JWKS) OpenID Connect Connect dobrze znanego punktu końcowego konfiguracji odzwierciedla klucze skonfigurowane w kontenerze kluczy, gdy istnieje odwołanie do klucza w [profilu technicznym JwtIssuer](./jwt-issuer-technical-profile.md). Aplikacja korzystająca z biblioteki OIDC automatycznie pobierze te metadane, aby upewnić się, że do walidacji tokenów użyto prawidłowych kluczy. Aby uzyskać więcej informacji, zobacz Korzystanie z [biblioteki uwierzytelniania firmy Microsoft](../active-directory/develop/msal-b2c-overview.md), która zawsze automatycznie pobiera najnowsze klucze podpisywania tokenu.

## <a name="policy-key-management"></a>Zarządzanie kluczami zasad

Aby uzyskać bieżący aktywny klucz w kontenerze kluczy, użyj punktu końcowego Microsoft Graph API [getActiveKey](/graph/api/trustframeworkkeyset-getactivekey) .

Aby dodać lub usunąć klucze podpisywania i szyfrowania:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Na stronie Przegląd w obszarze **zasady** wybierz pozycję **platforma obsługi tożsamości**.
1. Wybierz **klucze zasad** 
    1. Aby dodać nowy klucz, wybierz pozycję **Dodaj**.
    1. Aby usunąć nowy klucz, wybierz klucz, a następnie wybierz pozycję **Usuń**. Aby usunąć klucz, wpisz nazwę kontenera kluczy do usunięcia. Azure AD B2C usunie klucz i utworzysz kopię klucza z sufiksem. bak.

### <a name="replace-a-key"></a>Zastąp klucz

Klucze w zestawie kluczy nie są wymienne ani usuwalne. Jeśli musisz zmienić istniejący klucz:

- Zalecamy dodanie nowego klucza z **datą aktywacji** ustawioną na bieżącą datę i godzinę. Azure AD B2C aktywuje nowy klucz i zatrzyma poprzedni klucz aktywny.
- Alternatywnie można utworzyć nowy zestaw kluczy z prawidłowymi kluczami. Zaktualizuj zasady tak, aby korzystały z nowego zestawu kluczy, a następnie usuń stary zestaw kluczy. 

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, w jaki sposób używać Microsoft Graph do automatyzowania wdrożenia [zestawu](microsoft-graph-operations.md#trust-framework-policy-keyset) [kluczy i zasad](microsoft-graph-operations.md#trust-framework-policy-key) .