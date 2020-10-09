---
title: Konfigurowanie złożoności haseł przy użyciu zasad niestandardowych
titleSuffix: Azure AD B2C
description: Jak skonfigurować wymagania dotyczące złożoności hasła przy użyciu zasad niestandardowych w programie Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 4ab196e894fc53b1243ac363f9863d5c7d4e328f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85389007"
---
# <a name="configure-password-complexity-using-custom-policies-in-azure-active-directory-b2c"></a>Konfigurowanie złożoności haseł przy użyciu zasad niestandardowych w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

W Azure Active Directory B2C (Azure AD B2C) można skonfigurować wymagania dotyczące złożoności haseł dostarczonych przez użytkownika podczas tworzenia konta. Domyślnie Azure AD B2C używa **silnych** haseł. W tym artykule opisano sposób konfigurowania złożoności haseł w [zasadach niestandardowych](custom-policy-overview.md). Istnieje również możliwość skonfigurowania złożoności haseł w [przepływach użytkowników](user-flow-password-complexity.md).

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w temacie Wprowadzenie [do zasad niestandardowych](custom-policy-get-started.md). Należy mieć działającą niestandardową zasadę do rejestracji i logowania przy użyciu kont lokalnych.


## <a name="add-the-elements"></a>Dodaj elementy

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

1. Następujące profile techniczne są [Active Directory profile techniczne](active-directory-technical-profile.md), które odczytują i zapisują dane do Azure Active Directory. Zastąp te profile techniczne w pliku rozszerzenia. Służy `PersistedClaims` do wyłączania zasad silnych haseł. Znajdź element **ClaimsProviders** .  Dodaj następujących dostawców roszczeń w następujący sposób:

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

1. Zapisz plik zasad.

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
2. W przypadku **aplikacji**wybierz wcześniej zarejestrowaną aplikację. Aby wyświetlić token, należy wyświetlić **adres URL odpowiedzi** `https://jwt.ms` .
3. Kliknij pozycję **Uruchom teraz**.
4. Wybierz pozycję **zarejestruj się teraz**, wprowadź adres e-mail i wprowadź nowe hasło. Wskazówki przedstawiono w temacie ograniczenia dotyczące haseł. Zakończ wprowadzanie informacji o użytkowniku, a następnie kliknij przycisk **Utwórz**. Powinna zostać wyświetlona zawartość zwróconego tokenu.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [skonfigurować zmianę hasła przy użyciu zasad niestandardowych w Azure Active Directory B2C](custom-policy-password-change.md).
- Dowiedz się więcej o [predykatach](predicates.md) i elementach [PredicateValidations](predicates.md#predicatevalidations) w odwołaniu IEF.
