---
title: Predykaty i PredicateValidations
titleSuffix: Azure AD B2C
description: Zapobiegaj dodawaniu nieprawidłowo sformułowanych danych do dzierżawy Azure AD B2C przy użyciu zasad niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b01f1edd4305c09a874b177e4bca373991c9162e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85203813"
---
# <a name="predicates-and-predicatevalidations"></a>Predykaty i PredicateValidations

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

**Predykaty** i elementy **PredicateValidations** umożliwiają wykonanie procesu walidacji w celu upewnienia się, że w dzierżawie Azure Active Directory B2C (Azure AD B2C) są wprowadzane tylko prawidłowo uformowane dane.

Na poniższym diagramie przedstawiono relację między elementami:

![Diagram przedstawiający predykaty i relacje walidacji predykatu](./media/predicates/predicates.png)

## <a name="predicates"></a>Predykaty

Element **predykatu** definiuje podstawową walidację, aby sprawdzić wartość typu i zwraca `true` lub `false` . Walidacja jest wykonywana przy użyciu określonego elementu **metody** i zestawu elementów **parametrów** odpowiednich dla metody. Na przykład predykat może sprawdzić, czy długość wartości żądania ciągu mieści się w zakresie minimalnym i maksymalnym określonym parametrem lub czy wartość żądania ciągu zawiera zestaw znaków. Element **UserHelpText** zawiera komunikat o błędzie dla użytkowników, jeśli sprawdzenie zakończy się niepowodzeniem. Wartość elementu **UserHelpText** można lokalizować przy użyciu [dostosowywania języka](localization.md).

Element **predykatów** musi występować bezpośrednio po elemencie **ClaimsSchema** w elemencie [BuildingBlocks](buildingblocks.md) .

Element **predykatów** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Predykatu | 1: n | Lista predykatów. |

Element **predykatu** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Id | Tak | Identyfikator, który jest używany dla predykatu. Inne elementy mogą używać tego identyfikatora w zasadach. |
| Metoda | Tak | Typ metody do użycia na potrzeby walidacji. Możliwe wartości: [IsLengthRange](#islengthrange), [MatchesRegex](#matchesregex), [IncludesCharacters](#includescharacters)lub [IsDateRange](#isdaterange).  |
| HelpText | Nie | Komunikat o błędzie dla użytkowników, jeśli sprawdzenie zakończy się niepowodzeniem. Ten ciąg może być lokalizowany przy użyciu [dostosowania języka](localization.md) |

Element **predykatu** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 | Przestarzałe Komunikat o błędzie dla użytkowników, jeśli sprawdzenie zakończy się niepowodzeniem. |
| Parametry | 1:1 | Parametry typu metody weryfikacji ciągu. |

Element **Parameters** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Parametr | 1: n | Parametry typu metody weryfikacji ciągu. |

Element **Parameter** zawiera następujące atrybuty:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Id | 1:1 | Identyfikator parametru. |

### <a name="predicate-methods"></a>Metody predykatu

#### <a name="islengthrange"></a>IsLengthRange

Metoda IsLengthRange sprawdza, czy długość wartości żądania ciągu mieści się w zakresie określonych parametrów minimum i maksimum. Element predykatu obsługuje następujące parametry:

| Parametr | Wymagane | Opis |
| ------- | ----------- | ----------- |
| Maksimum | Tak | Maksymalna liczba znaków, które można wprowadzić. |
| Minimum | Tak | Minimalna liczba znaków, które muszą zostać wprowadzone. |


Poniższy przykład przedstawia metodę IsLengthRange z parametrami, `Minimum` `Maximum` które określają zakres długości ciągu:

```xml
<Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
  <Parameters>
    <Parameter Id="Minimum">8</Parameter>
    <Parameter Id="Maximum">64</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="matchesregex"></a>MatchesRegex

Metoda MatchesRegex sprawdza, czy wartość żądania ciągu pasuje do wyrażenia regularnego. Element predykatu obsługuje następujące parametry:

| Parametr | Wymagane | Opis |
| ------- | ----------- | ----------- |
| RegularExpression | Tak | Wzorzec wyrażenia regularnego do dopasowania. |

Poniższy przykład przedstawia `MatchesRegex` metodę z parametrem `RegularExpression` , który określa wyrażenie regularne:

```xml
<Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
  <Parameters>
    <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="includescharacters"></a>IncludesCharacters

Metoda IncludesCharacters sprawdza, czy wartość żądania ciągu zawiera zestaw znaków. Element predykatu obsługuje następujące parametry:

| Parametr | Wymagane | Opis |
| ------- | ----------- | ----------- |
| CharacterSet | Tak | Zestaw znaków, które można wprowadzić. Na przykład małe litery  `a-z` , wielkie litery `A-Z` , cyfry `0-9` lub Lista symboli, takich jak `@#$%^&amp;*\-_+=[]{}|\\:',?/~"();!` . |

Poniższy przykład przedstawia `IncludesCharacters` metodę z parametrem `CharacterSet` , który określa zestaw znaków:

```xml
<Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
  <Parameters>
    <Parameter Id="CharacterSet">a-z</Parameter>
  </Parameters>
</Predicate>
```

#### <a name="isdaterange"></a>IsDateRange

Metoda IsDateRange sprawdza, czy wartość żądania daty należy do zakresu od określonych parametrów minimum i maksimum. Element predykatu obsługuje następujące parametry:

| Parametr | Wymagane | Opis |
| ------- | ----------- | ----------- |
| Maksimum | Tak | Największa możliwa data, którą można wprowadzić. Format daty jest zgodny z `yyyy-mm-dd` Konwencją lub `Today` . |
| Minimum | Tak | Najmniejsza możliwa data, którą można wprowadzić. Format daty jest zgodny z `yyyy-mm-dd` Konwencją lub `Today` .|

Poniższy przykład przedstawia `IsDateRange` metodę z parametrami, `Minimum` `Maximum` które określają zakres dat z formatem `yyyy-mm-dd` i `Today` .

```xml
<Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 1970-01-01 and today.">
  <Parameters>
    <Parameter Id="Minimum">1970-01-01</Parameter>
    <Parameter Id="Maximum">Today</Parameter>
  </Parameters>
</Predicate>
```

## <a name="predicatevalidations"></a>PredicateValidations

Podczas gdy predykaty definiują walidację w celu sprawdzenia typu, Grupa **PredicateValidations** zestawu predykatów do tworzenia walidacji danych wejściowych użytkownika, które można zastosować do typu zgłoszenia. Każdy element **PredicateValidation** zawiera zestaw elementów **predykatu** , które zawierają zestaw elementów **PredicateReference** , które wskazują **predykat**. Aby przekazać weryfikację, wartość żądania powinna przekazać wszystkie testy dowolnego predykatu w ramach całej grupy **predykatów** z ich zestawem elementów **PredicateReference** .

Element **PredicateValidations** musi występować bezpośrednio po elemencie **predykatów** w elemencie [BuildingBlocks](buildingblocks.md) .

```xml
<PredicateValidations>
  <PredicateValidation Id="">
    <PredicateGroups>
      <PredicateGroup Id="">
        <UserHelpText></UserHelpText>
        <PredicateReferences MatchAtLeast="">
          <PredicateReference Id="" />
          ...
        </PredicateReferences>
      </PredicateGroup>
      ...
    </PredicateGroups>
  </PredicateValidation>
...
</PredicateValidations>
```

Element **PredicateValidations** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| PredicateValidation | 1: n | Lista walidacji predykatu. |

Element **PredicateValidation** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Id | Tak | Identyfikator, który jest używany na potrzeby walidacji predykatu. Element **ClaimType** może używać tego identyfikatora w zasadach. |

Element **PredicateValidation** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| PredicateGroups | 1: n | Lista grup predykatów. |

Element **PredicateGroups** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Predykat | 1: n | Lista predykatów. |

Element grupy **predykatów** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Id | Tak | Identyfikator, który jest używany dla grupy predykatu.  |

Element grupy **predykatów** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| UserHelpText | 0:1 |  Opis predykatu, który może być przydatny dla użytkowników, aby wiedzieć, jaka wartość powinna być typu. |
| PredicateReferences | 1: n | Lista odwołań predykatu. |

Element **PredicateReferences** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| MatchAtLeast | Nie | Określa, że wartość musi być zgodna co najmniej z wieloma definicjami predykatów dla danych wejściowych, które mają zostać zaakceptowane. Jeśli nie zostanie określony, wartość musi być zgodna ze wszystkimi definicjami predykatu. |

Element **PredicateReferences** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| PredicateReference | 1: n | Odwołanie do predykatu. |

Element **PredicateReference** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Id | Tak | Identyfikator, który jest używany na potrzeby walidacji predykatu.  |


## <a name="configure-password-complexity"></a>Konfigurowanie złożoności hasła

Przy użyciu **predykatów** i **PredicateValidationsInput** można kontrolować wymagania dotyczące złożoności dla haseł dostarczonych przez użytkownika podczas tworzenia konta. Domyślnie Azure AD B2C używa silnych haseł. Azure AD B2C obsługuje również opcje konfiguracji w celu kontrolowania złożoności haseł, które mogą być używane przez klientów. Złożoność hasła można zdefiniować przy użyciu następujących elementów predykatu:

- **IsLengthBetween8And64** przy użyciu `IsLengthRange` metody, sprawdza, czy hasło musi zawierać od 8 do 64 znaków.
- **Małe** litery przy użyciu `IncludesCharacters` metody, sprawdza, czy hasło zawiera małą literę.
- **Wielkie litery** przy użyciu `IncludesCharacters` metody, sprawdza, czy hasło zawiera wielką literę.
- **Liczba** przy użyciu `IncludesCharacters` metody, sprawdza, czy hasło zawiera cyfrę.
- **Symbol** przy użyciu `IncludesCharacters` metody, sprawdza, czy hasło zawiera jeden z kilku znaków symbolicznych.
- **Przypnij** przy użyciu `MatchesRegex` metody, sprawdza, czy hasło zawiera tylko cyfry.
- **AllowedAADCharacters** przy użyciu `MatchesRegex` metody, sprawdza, czy podano tylko nieprawidłowy znak.
- **DisallowedWhitespace** przy użyciu `MatchesRegex` metody, sprawdza, czy hasło nie zaczyna się ani nie kończy znakiem odstępu.

```xml
<Predicates>
  <Predicate Id="IsLengthBetween8And64" Method="IsLengthRange" HelpText="The password must be between 8 and 64 characters.">
    <Parameters>
      <Parameter Id="Minimum">8</Parameter>
      <Parameter Id="Maximum">64</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Lowercase" Method="IncludesCharacters" HelpText="a lowercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">a-z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Uppercase" Method="IncludesCharacters" HelpText="an uppercase letter">
    <Parameters>
      <Parameter Id="CharacterSet">A-Z</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Number" Method="IncludesCharacters" HelpText="a digit">
    <Parameters>
      <Parameter Id="CharacterSet">0-9</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="Symbol" Method="IncludesCharacters" HelpText="a symbol">
    <Parameters>
      <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="PIN" Method="MatchesRegex" HelpText="The password must be numbers only.">
    <Parameters>
      <Parameter Id="RegularExpression">^[0-9]+$</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="AllowedAADCharacters" Method="MatchesRegex" HelpText="An invalid character was provided.">
    <Parameters>
      <Parameter Id="RegularExpression">(^([0-9A-Za-z\d@#$%^&amp;*\-_+=[\]{}|\\:',?/`~"();! ]|(\.(?!@)))+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>

  <Predicate Id="DisallowedWhitespace" Method="MatchesRegex" HelpText="The password must not begin or end with a whitespace character.">
    <Parameters>
      <Parameter Id="RegularExpression">(^\S.*\S$)|(^\S+$)|(^$)</Parameter>
    </Parameters>
  </Predicate>
```

Po zdefiniowaniu podstawowych walidacji można połączyć je ze sobą i utworzyć zestaw zasad haseł, których można użyć w ramach zasad:

- **SimplePassword** sprawdza poprawność DisallowedWhitespace, AllowedAADCharacters i IsLengthBetween8And64
- **Strongpassword** sprawdza poprawność DisallowedWhitespace, AllowedAADCharacters, IsLengthBetween8And64. Ostatnia Grupa `CharacterClasses` uruchamia dodatkowy zestaw predykatów z `MatchAtLeast` ustawioną na 3. Hasło użytkownika musi mieć długość od 8 do 16 znaków i trzy z następujących znaków: małe litery, wielkie litery, cyfry lub symbole.
- **CustomPassword** weryfikuje tylko DisallowedWhitespace, AllowedAADCharacters. W takim przypadku użytkownik może podać dowolne hasło o dowolnej długości, o ile znaki są prawidłowe.

```xml
<PredicateValidations>
  <PredicateValidation Id="SimplePassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="StrongPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
       </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="LengthGroup">
        <PredicateReferences>
          <PredicateReference Id="IsLengthBetween8And64" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="CharacterClasses">
        <UserHelpText>The password must have at least 3 of the following:</UserHelpText>
        <PredicateReferences MatchAtLeast="3">
          <PredicateReference Id="Lowercase" />
          <PredicateReference Id="Uppercase" />
          <PredicateReference Id="Number" />
          <PredicateReference Id="Symbol" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>

  <PredicateValidation Id="CustomPassword">
    <PredicateGroups>
      <PredicateGroup Id="DisallowedWhitespaceGroup">
        <PredicateReferences>
          <PredicateReference Id="DisallowedWhitespace" />
        </PredicateReferences>
      </PredicateGroup>
      <PredicateGroup Id="AllowedAADCharactersGroup">
        <PredicateReferences>
          <PredicateReference Id="AllowedAADCharacters" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

W polu Typ zgłoszenia Dodaj element **PredicateValidationReference** i określ identyfikator jako jedną z walidacji predykatów, takich jak SimplePassword, strongpassword lub CustomPassword.

```xml
<ClaimType Id="password">
  <DisplayName>Password</DisplayName>
  <DataType>string</DataType>
  <AdminHelpText>Enter password</AdminHelpText>
  <UserHelpText>Enter password</UserHelpText>
  <UserInputType>Password</UserInputType>
  <PredicateValidationReference Id="StrongPassword" />
</ClaimType>
```

Poniżej pokazano, jak elementy są zorganizowane, gdy Azure AD B2C wyświetla komunikat o błędzie:

![Diagram przykładu złożoności hasła predykatu i predykatu](./media/predicates/predicates-pass.png)

## <a name="configure-a-date-range"></a>Konfigurowanie zakresu dat

Za pomocą **predykatów** i elementów **PredicateValidations** można kontrolować minimalną i maksymalną wartość daty **UserInputType** przy użyciu `DateTimeDropdown` . W tym celu należy utworzyć **predykat** z `IsDateRange` metodą i podać parametry minimalne i maksymalne.

```xml
<Predicates>
  <Predicate Id="DateRange" Method="IsDateRange" HelpText="The date must be between 01-01-1980 and today.">
    <Parameters>
      <Parameter Id="Minimum">1980-01-01</Parameter>
      <Parameter Id="Maximum">Today</Parameter>
    </Parameters>
  </Predicate>
</Predicates>
```

Dodaj element **PredicateValidation** z odwołaniem do `DateRange` predykatu.

```xml
<PredicateValidations>
  <PredicateValidation Id="CustomDateRange">
    <PredicateGroups>
      <PredicateGroup Id="DateRangeGroup">
        <PredicateReferences>
          <PredicateReference Id="DateRange" />
        </PredicateReferences>
      </PredicateGroup>
    </PredicateGroups>
  </PredicateValidation>
</PredicateValidations>
```

W polu Typ zgłoszenia Dodaj element **PredicateValidationReference** i określ identyfikator jako `CustomDateRange` .

```xml
<ClaimType Id="dateOfBirth">
  <DisplayName>Date of Birth</DisplayName>
  <DataType>date</DataType>
  <AdminHelpText>The user's date of birth.</AdminHelpText>
  <UserHelpText>Your date of birth.</UserHelpText>
  <UserInputType>DateTimeDropdown</UserInputType>
  <PredicateValidationReference Id="CustomDateRange" />
</ClaimType>
 ```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [skonfigurować złożoność hasła przy użyciu zasad niestandardowych w Azure Active Directory B2C](custom-policy-password-complexity.md) przy użyciu walidacji predykatów.