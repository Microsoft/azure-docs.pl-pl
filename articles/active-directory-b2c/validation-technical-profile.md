---
title: Zdefiniuj profil techniczny weryfikacji w zasadach niestandardowych
titleSuffix: Azure AD B2C
description: Sprawdź poprawność oświadczeń w ramach zasad niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2d4c538a9292698fecc8b44c055ab201748e292c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85202997"
---
# <a name="define-a-validation-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Zdefiniuj profil techniczny weryfikacji w zasadach niestandardowych Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Profil techniczny weryfikacji jest zwykłym profilem technicznym z dowolnego protokołu, takiego jak [Azure Active Directory](active-directory-technical-profile.md) lub [interfejs API REST](restful-technical-profile.md). Profil techniczny weryfikacji zwraca oświadczenia wyjściowe lub zwraca kod stanu HTTP 4xx z poniższymi danymi. Aby uzyskać więcej informacji, zobacz [zwracany komunikat o błędzie](restful-technical-profile.md#returning-validation-error-message)

```json
{
    "version": "1.0.0",
    "status": 409,
    "userMessage": "Your error message"
}
```

Zakres oświadczeń wyjściowych w profilu technicznym weryfikacji jest ograniczony do [profilu technicznego z własnym](self-asserted-technical-profile.md) potwierdzeniem, który wywołuje profil techniczny weryfikacji, oraz jego profile techniczne weryfikacji. Jeśli chcesz użyć oświadczeń wyjściowych w następnym kroku aranżacji, Dodaj dane wyjściowe oświadczeń do profilu technicznego z własnym potwierdzeniem, który wywoła profil techniczny weryfikacji.

Profile techniczne weryfikacji są wykonywane w kolejności, w której pojawiają się w elemencie **ValidationTechnicalProfiles** . Konfigurację można skonfigurować w profilu technicznym weryfikacji, niezależnie od tego, czy wykonanie kolejnych walidacji profilów technicznych powinno być kontynuowane, gdy weryfikacja profilu technicznego zgłasza błąd lub powiedzie się.

Profil techniczny weryfikacji można wykonać warunkowo na podstawie warunków wstępnych zdefiniowanych w elemencie **ValidationTechnicalProfile** . Można na przykład sprawdzić, czy określone zgłoszenie istnieje lub czy nie jest równe określonej wartości.

Profil techniczny z własnym potwierdzeniem może definiować profil techniczny weryfikacji, który będzie używany do sprawdzania poprawności niektórych lub wszystkich oświadczeń wyjściowych. Wszystkie oświadczenia wejściowe profilu technicznego, do którego istnieje odwołanie, muszą pojawić się w oświadczeniach wyjściowych profilu technicznego weryfikacji odwołania.

> [!NOTE]
> Tylko własne profile techniczne mogą korzystać z weryfikacji profilów technicznych. Aby sprawdzić poprawność danych wyjściowych z niepotwierdzonych profilów technicznych, należy rozważyć użycie dodatkowego kroku aranżacji w podróży użytkownika w celu uwzględnienia profilu technicznego odpowiedzialnego za weryfikację.

## <a name="validationtechnicalprofiles"></a>ValidationTechnicalProfiles

Element **ValidationTechnicalProfiles** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| ValidationTechnicalProfile | 1: n | Profil techniczny służący do sprawdzania poprawności niektórych lub wszystkich oświadczeń wyjściowych odwołującego się do profilu technicznego. |

Element **ValidationTechnicalProfile** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Identyfikator odwołania | Tak | Identyfikator profilu technicznego jest już zdefiniowany w zasadach lub zasadach nadrzędnych. |
|ContinueOnError|Nie| Wskazuje, czy sprawdzanie poprawności wszelkich kolejnych weryfikacji profilów technicznych powinno być kontynuowane, jeśli ten profil techniczny tego sprawdzania poprawności zgłosi błąd. Możliwe wartości: `true` lub `false` (domyślnie przetwarzanie dalszych profilów weryfikacji zostanie zatrzymane i zostanie zwrócony błąd). |
|ContinueOnSuccess | Nie | Wskazuje, czy sprawdzanie poprawności wszelkich kolejnych profilów weryfikacji powinno być kontynuowane, jeśli ten profil techniczny zostanie pomyślnie zakończony. Możliwe wartości: `true` lub `false` . Wartość domyślna to `true` , co oznacza, że przetwarzanie dalszych profilów weryfikacji będzie kontynuowane. |

Element **ValidationTechnicalProfile** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Warunki wstępne | 0:1 | Lista warunków wstępnych, które muszą być spełnione, aby można było wykonać walidację profilu technicznego. |

Element **Conditional** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| `Type` | Tak | Typ sprawdzenia lub zapytania do wykonania dla warunku wstępnego. `ClaimsExist`Określono, że akcje mają być wykonywane, jeśli określone oświadczenia istnieją w bieżącym zestawie oświadczeń użytkownika lub jeśli określono, `ClaimEquals` że akcje należy wykonać, jeśli istnieje określone oświadczenie, a jego wartość jest równa określonej wartości. |
| `ExecuteActionsIf` | Tak | Wskazuje, czy akcje w warunku wstępnym mają być wykonywane, jeśli test ma wartość true lub false. |

Element **Conditional** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Wartość | 1: n | Dane, które są używane przez sprawdzanie. Jeśli typ tego sprawdzenia to `ClaimsExist` , to pole określa ClaimTypeReferenceId do zapytania. Jeśli typ sprawdzenia to `ClaimEquals` , to pole określa ClaimTypeReferenceId do zapytania. Gdy inny element wartości zawiera wartość, która ma zostać sprawdzona.|
| Akcja | 1:1 | Akcja, która powinna zostać podjęta, jeśli sprawdzanie warunków wstępnych w ramach kroku aranżacji ma wartość true. Wartość **akcji** jest ustawiona na `SkipThisValidationTechnicalProfile` . Określa, że skojarzony profil techniczny weryfikacji nie powinien być wykonywany. |

### <a name="example"></a>Przykład

W poniższym przykładzie są stosowane następujące profile techniczne weryfikacji:

1. Pierwszy zweryfikowany profil techniczny sprawdza poświadczenia użytkownika i nie kontynuuje działania w przypadku wystąpienia błędu, takiego jak Nieprawidłowa nazwa użytkownika lub nieprawidłowe hasło.
2. Następny weryfikacyjny profil techniczny nie jest wykonywany, jeśli nie istnieje, lub jeśli wartość parametru UserType jest równa `Partner` . Profil techniczny weryfikacji próbuje odczytać profil użytkownika z wewnętrznej bazy danych klienta i kontynuować w przypadku wystąpienia błędu, takiego jak usługa interfejsu API REST niedostępna lub wystąpił błąd wewnętrzny.
3. Ostatni zweryfikowany profil techniczny nie jest wykonywany, jeśli nie istnieje, lub jeśli wartość parametru UserType jest równa `Customer` . Profil techniczny weryfikacji próbuje odczytać profil użytkownika z wewnętrznej bazy danych partnera i kontynuuje działanie w przypadku wystąpienia błędu, takiego jak usługa interfejsu API REST niedostępna lub wystąpił błąd wewnętrzny.

```xml
<ValidationTechnicalProfiles>
  <ValidationTechnicalProfile ReferenceId="login-NonInteractive" ContinueOnError="false" />
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromCustomertsDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Partner</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
  <ValidationTechnicalProfile ReferenceId="REST-ReadProfileFromPartnersDatabase" ContinueOnError="true" >
    <Preconditions>
      <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
        <Value>userType</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
      <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
        <Value>userType</Value>
        <Value>Customer</Value>
        <Action>SkipThisValidationTechnicalProfile</Action>
      </Precondition>
    </Preconditions>
  </ValidationTechnicalProfile>
</ValidationTechnicalProfiles>
```
