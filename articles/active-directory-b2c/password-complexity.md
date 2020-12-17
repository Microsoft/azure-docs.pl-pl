---
title: Skonfiguruj wymagania dotyczące złożoności hasła
titleSuffix: Azure AD B2C
description: Jak skonfigurować wymagania dotyczące złożoności dla haseł dostarczonych przez użytkowników w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 11a45adfda306b2ab843725b6aaa28a5e6c026a6
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97614255"
---
# <a name="configure-complexity-requirements-for-passwords-in-azure-active-directory-b2c"></a>Konfigurowanie wymagań dotyczących złożoności haseł w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure Active Directory B2C (Azure AD B2C) obsługuje zmianę wymagań dotyczących złożoności dla haseł dostarczonych przez użytkownika końcowego podczas tworzenia konta. Domyślnie Azure AD B2C używa **silnych** haseł. Azure AD B2C obsługuje również opcje konfiguracji w celu kontrolowania złożoności haseł, które mogą być używane przez klientów.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="password-rule-enforcement"></a>Wymuszanie reguły hasła

Podczas rejestrowania lub resetowania hasła użytkownik końcowy musi podać hasło spełniające reguły złożoności. Reguły złożoności haseł są wymuszane dla przepływu użytkownika. Istnieje możliwość, że jeden przepływ użytkownika wymaga czterech cyfrowego numeru PIN podczas rejestracji, gdy inny przepływ użytkownika wymaga ośmiu ciągów znaków podczas rejestracji. Można na przykład użyć przepływu użytkownika z inną złożonością hasła dla osób dorosłych niż w przypadku dzieci.

Złożoność hasła nigdy nie jest wymuszana podczas logowania. Przed zalogowaniem użytkownicy nigdy nie będą monitowani o zmianę hasła, ponieważ nie spełnia ono wymagań dotyczących bieżącego stopnia złożoności.

Złożoność hasła można skonfigurować w następujących typach przepływów użytkownika:

- Przepływ użytkownika rejestracji lub logowania
- Przepływ użytkownika resetowania hasła

Jeśli używasz zasad niestandardowych, możesz ([skonfigurować złożoność hasła w zasadach niestandardowych](password-complexity.md)).

## <a name="configure-password-complexity"></a>Konfigurowanie złożoności hasła

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
3. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
4. Wybierz pozycję **przepływy użytkownika**.
2. Wybierz przepływ użytkownika, a następnie kliknij pozycję **Właściwości**.
3. W obszarze **złożoność hasła** Zmień złożoność hasła dla tego przepływu użytkownika na **prostą**, **silną** lub **niestandardową**.

### <a name="comparison-chart"></a>Wykres porównawczy

| Złożoność | Opis |
| --- | --- |
| Prostota | Hasło, które ma co najmniej 8 do 64 znaków. |
| Silna | Hasło, które ma co najmniej 8 do 64 znaków. Wymaga 3 z 4 z małych i wielkich liter, cyfr lub symboli. |
| Niestandardowy | Ta opcja zapewnia największą kontrolę nad regułami złożoności haseł.  Umożliwia skonfigurowanie długości niestandardowej.  Umożliwia również akceptowanie haseł tylko do numerów (PIN). |

## <a name="custom-options"></a>Opcje niestandardowe

### <a name="character-set"></a>Zestaw znaków

Umożliwia akceptowanie tylko cyfr (kodów PIN) lub pełnego zestawu znaków.

- **Liczba** dopuszcza tylko cyfry (0-9) podczas wprowadzania hasła.
- **All** zezwala na dowolną literę, cyfrę lub symbol.

### <a name="length"></a>Długość

Pozwala kontrolować wymagania dotyczące długości hasła.

- **Minimalna długość** musi wynosić co najmniej 4.
- **Maksymalna długość** musi być większa lub równa długości minimalnej, a maksymalnie 64 znaków.

### <a name="character-classes"></a>Klasy znaków

Umożliwia sterowanie różnymi typami znaków używanymi w haśle.

- **2 z 4: mała litera, Wielka litera, cyfra (0-9), symbol zapewnia,** że hasło zawiera co najmniej dwa typy znaków. Na przykład liczba i małe litery.
- **3 z 4: mała litera, Wielka litera, cyfra (0-9), symbol zapewnia,** że hasło zawiera co najmniej trzy typy znaków. Na przykład liczba, małe litery i wielkie litery.
- **4 z 4: mała litera, Wielka litera, cyfra (0-9), symbol** zapewnia, że hasło zawiera wszystkie typy znaków.

    > [!NOTE]
    > Wymaganie **4 z 4** może skutkować frustrację użytkownika końcowego. Niektóre badania wykazały, że to wymaganie nie poprawia entropii hasła. Zobacz [wytyczne dotyczące haseł NIST](https://pages.nist.gov/800-63-3/sp800-63b.html#appA)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="password-predicate-validation"></a>Walidacja predykatu hasła

Aby skonfigurować złożoność hasła, Zastąp `newPassword` `reenterPassword` [typy i typu roszczeń](claimsschema.md) odwołaniem do [walidacji predykatów](predicates.md#predicatevalidations). Element PredicateValidations grupuje zestaw predykatów do tworzenia walidacji danych wejściowych użytkownika, które można zastosować do typu zgłoszenia. Otwórz plik rozszerzeń zasad. Na przykład <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Wyszukaj element [BuildingBlocks](buildingblocks.md) . Jeśli element nie istnieje, Dodaj go.
1. Znajdź element [ClaimsSchema](claimsschema.md) . Jeśli element nie istnieje, Dodaj go.
1. Dodaj `newPassword` oświadczenia i `reenterPassword` do elementu **ClaimsSchema** .

    ```xml
    <ClaimType Id="newPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    <ClaimType Id="reenterPassword">
      <PredicateValidationReference Id="CustomPassword" />
    </ClaimType>
    ```

1. [Predykaty](predicates.md) definiują podstawową walidację w celu sprawdzenia wartości typu i zwraca wartość PRAWDA lub FAŁSZ. Walidacja jest wykonywana przy użyciu określonego elementu metody i zestawu parametrów związanych z tą metodą. Dodaj następujące predykaty do elementu **BuildingBlocks** bezpośrednio po zamknięciu `</ClaimsSchema>` elementu:

    ```xml
    <Predicates>
      <Predicate Id="LengthRange" Method="IsLengthRange">
        <UserHelpText>The password must be between 6 and 64 characters.</UserHelpText>
        <Parameters>
          <Parameter Id="Minimum">6</Parameter>
          <Parameter Id="Maximum">64</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Lowercase" Method="IncludesCharacters">
        <UserHelpText>a lowercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">a-z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Uppercase" Method="IncludesCharacters">
        <UserHelpText>an uppercase letter</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">A-Z</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Number" Method="IncludesCharacters">
        <UserHelpText>a digit</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">0-9</Parameter>
        </Parameters>
      </Predicate>
      <Predicate Id="Symbol" Method="IncludesCharacters">
        <UserHelpText>a symbol</UserHelpText>
        <Parameters>
          <Parameter Id="CharacterSet">@#$%^&amp;*\-_+=[]{}|\\:',.?/`~"();!</Parameter>
        </Parameters>
      </Predicate>
    </Predicates>
    ```

1. Dodaj następujące walidacje predykatu do elementu **BuildingBlocks** , bezpośrednio po zamknięciu `</Predicates>` elementu:

    ```xml
    <PredicateValidations>
      <PredicateValidation Id="CustomPassword">
        <PredicateGroups>
          <PredicateGroup Id="LengthGroup">
            <PredicateReferences MatchAtLeast="1">
              <PredicateReference Id="LengthRange" />
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
    </PredicateValidations>
    ```

## <a name="disable-strong-password"></a>Wyłącz silne hasło 

Następujące profile techniczne są [Active Directory profile techniczne](active-directory-technical-profile.md), które odczytują i zapisują dane do Azure Active Directory. Zastąp te profile techniczne w pliku rozszerzenia. Służy `PersistedClaims` do wyłączania zasad silnych haseł. Znajdź element **ClaimsProviders** .  Dodaj następujących dostawców roszczeń w następujący sposób:

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
      </PersistedClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="AAD-UserWritePasswordUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration, DisableStrongPassword"/>
      </PersistedClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

Zapisz plik zasad.

## <a name="test-your-policy"></a>Testowanie zasad

### <a name="upload-the-files"></a>Przekazywanie plików

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
4. Wybierz pozycję **platforma obsługi tożsamości**.
5. Na stronie zasady niestandardowe kliknij pozycję **Przekaż zasady**.
6. Wybierz opcję **Zastąp zasady, jeśli istnieje**, a następnie wyszukaj i wybierz plik *TrustFrameworkExtensions.xml* .
7. Kliknij pozycję **Przekaż**.

### <a name="run-the-policy"></a>Uruchamianie zasad

1. Otwórz zasady rejestracji lub logowania. Na przykład *B2C_1A_signup_signin*.
2. W przypadku **aplikacji** wybierz wcześniej zarejestrowaną aplikację. Aby wyświetlić token, należy wyświetlić **adres URL odpowiedzi** `https://jwt.ms` .
3. Kliknij pozycję **Uruchom teraz**.
4. Wybierz pozycję **zarejestruj się teraz**, wprowadź adres e-mail i wprowadź nowe hasło. Wskazówki przedstawiono w temacie ograniczenia dotyczące haseł. Zakończ wprowadzanie informacji o użytkowniku, a następnie kliknij przycisk **Utwórz**. Powinna zostać wyświetlona zawartość zwróconego tokenu.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [skonfigurować zmianę hasła w Azure Active Directory B2C](add-password-change-policy.md).
- Dowiedz się więcej o [predykatach](predicates.md) i elementach [PredicateValidations](predicates.md#predicatevalidations) w odwołaniu IEF.


::: zone-end