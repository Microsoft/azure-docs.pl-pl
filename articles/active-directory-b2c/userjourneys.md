---
title: UserJourneys | Microsoft Docs
description: Określ element UserJourneys zasad niestandardowych w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/04/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 05307fe2ad9e0a59fa11c30f2dc7154ba5076603
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102174669"
---
# <a name="userjourneys"></a>UserJourneys

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Podróże użytkowników określają jawne ścieżki, za pomocą których zasady umożliwiają aplikacji jednostki uzależnionej uzyskanie żądanych oświadczeń dla użytkownika. Użytkownik otrzymuje te ścieżki, aby pobrać oświadczenia, które mają być prezentowane dla jednostki uzależnionej. Innymi słowy, Podróże użytkowników definiują logikę biznesową, przez jaką użytkownik końcowy przechodzi przez użytkownika końcowego, jako że platforma obsługi tożsamości Azure AD B2C przetwarza żądanie.

Te podróże użytkowników mogą być uważane za szablony dostępne w celu spełnienia podstawowych potrzeb różnych jednostek uzależnionych zainteresowanych społeczności. Podróże użytkowników ułatwiają zdefiniowanie w ramach zasad jednostki uzależnionej. Zasady mogą definiować wiele podróży użytkownika. Każda podróż użytkownika to sekwencja kroków aranżacji.

Aby zdefiniować podróże użytkownika obsługiwane przez zasady, element **UserJourneys** jest dodawany do elementu najwyższego poziomu w pliku zasad.

Element **UserJourneys** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| UserJourney | 1: n | Podróż użytkownika, która definiuje wszystkie konstrukcje niezbędne do całkowitego przepływu użytkownika. |

Element **UserJourney** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Id | Tak | Identyfikator podróży użytkownika, który może służyć do odwoływania się do niego z innych elementów w zasadach. Element **DefaultUserJourney** [zasad jednostki uzależnionej](relyingparty.md) wskazuje na ten atrybut. |

Element **UserJourney** zawiera następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| AuthorizationTechnicalProfiles | 0:1 | Lista profilów technicznych autoryzacji. | 
| OrchestrationSteps | 1: n | Sekwencja aranżacji, która musi zostać wykonana w przypadku pomyślnej transakcji. Każda podróż użytkownika obejmuje uporządkowaną listę kroków aranżacji, które są wykonywane w kolejności. Jeśli którykolwiek z kroków zakończy się niepowodzeniem, transakcja nie powiedzie się. |

## <a name="authorizationtechnicalprofiles"></a>AuthorizationTechnicalProfiles

Załóżmy, że użytkownik zakończył UserJourney i uzyskał token dostępu lub identyfikatora. Do zarządzania dodatkowymi zasobami, takimi jak [punkt końcowy UserInfo](userinfo-endpoint.md), należy zidentyfikować użytkownika. Aby rozpocząć ten proces, użytkownik musi przedstawić token dostępu wystawiony wcześniej jako dowód, że został on pierwotnie uwierzytelniony przez prawidłowe zasady Azure AD B2C. Prawidłowy token dla użytkownika musi zawsze być obecny w trakcie tego procesu, aby upewnić się, że użytkownik może wykonać to żądanie. Profile techniczne autoryzacji weryfikują token przychodzący i wyodrębniają oświadczenia z tokenu.

Element **AuthorizationTechnicalProfiles** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| AuthorizationTechnicalProfile | 0:1 | Lista profilów technicznych autoryzacji. | 

Element **AuthorizationTechnicalProfile** zawiera następujący atrybut:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| TechnicalProfileReferenceId | Tak | Identyfikator profilu technicznego, który ma zostać wykonany. |

Poniższy przykład pokazuje element podróży użytkownika z profilami technicznymi autoryzacji:

```xml
<UserJourney Id="UserInfoJourney" DefaultCpimIssuerTechnicalProfileReferenceId="UserInfoIssuer">
  <Authorization>
    <AuthorizationTechnicalProfiles>
      <AuthorizationTechnicalProfile ReferenceId="UserInfoAuthorization" />
    </AuthorizationTechnicalProfiles>
  </Authorization>
  <OrchestrationSteps>
    <OrchestrationStep Order="1" Type="ClaimsExchange">
     ...
```

## <a name="orchestrationsteps"></a>OrchestrationSteps

Podróż użytkownika jest reprezentowana jako sekwencja aranżacji, która musi być stosowana w przypadku pomyślnej transakcji. Jeśli którykolwiek z kroków zakończy się niepowodzeniem, transakcja nie powiedzie się. Te kroki aranżacji odwołują się zarówno do bloków konstrukcyjnych, jak i dostawców oświadczeń, które są dozwolone w pliku zasad. Każdy krok aranżacji, który jest odpowiedzialny za pokazywanie lub renderowanie środowiska użytkownika, ma również odniesienie do odpowiadającego identyfikatora definicji zawartości.

Kroki aranżacji można wykonać warunkowo na podstawie warunków wstępnych zdefiniowanych w elemencie kroku aranżacji. Na przykład można zaznaczyć, aby wykonać krok aranżacji tylko wtedy, gdy istnieje określone zgłoszenie lub jeśli jest równe lub nie jest to podanej wartości.

Aby określić uporządkowaną listę kroków aranżacji, element **OrchestrationSteps** jest dodawany w ramach zasad. Ten element jest wymagany.

Element **OrchestrationSteps** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| OrchestrationStep | 1: n | Uporządkowany krok aranżacji. |

Element **OrchestrationStep** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| `Order` | Tak | Kolejność kroków aranżacji. |
| `Type` | Tak | Typ kroku aranżacji. Możliwe wartości: <ul><li>**ClaimsProviderSelection** — wskazuje, że krok aranżacji przedstawia różne dostawcy oświadczeń dla użytkownika w celu wybrania jednego z nich.</li><li>**CombinedSignInAndSignUp** — wskazuje, że krok aranżacji przedstawia łączną stronę logowania dostawcy społecznego i konta lokalnego.</li><li>**ClaimsExchange** — wskazuje, że krok aranżacji wymienia oświadczenia z dostawcą oświadczeń.</li><li>**Getclaims** — określa, że krok aranżacji powinien przetwarzać dane oświadczeń wysyłane do Azure AD B2C od jednostki uzależnionej za pośrednictwem jej `InputClaims` konfiguracji.</li><li>**InvokeSubJourney** — wskazuje, że krok aranżacji wymienia oświadczenia z [podróżą podrzędną](subjourneys.md) (w publicznej wersji zapoznawczej).</li><li>**SendClaims** — wskazuje, że krok aranżacji wysyła oświadczenia do jednostki uzależnionej przy użyciu tokenu wystawionego przez wystawcę oświadczeń.</li></ul> |
| ContentDefinitionReferenceId | Nie | Identyfikator [definicji zawartości](contentdefinitions.md) skojarzonej z tym krokiem aranżacji. Zazwyczaj identyfikator odwołania definicji zawartości jest zdefiniowany w profilu technicznym z własnym potwierdzeniem. Ale istnieją sytuacje, w których Azure AD B2C muszą wyświetlać coś bez profilu technicznego. Istnieją dwa przykłady — jeśli jest to jeden z następujących typów kroku aranżacji: `ClaimsProviderSelection` lub  `CombinedSignInAndSignUp` , Azure AD B2C musi wyświetlić wybór dostawcy tożsamości bez profilu technicznego. |
| CpimIssuerTechnicalProfileReferenceId | Nie | Typ kroku aranżacji to `SendClaims` . Ta właściwość określa identyfikator profilu technicznego dostawcy oświadczeń, który wystawia token dla jednostki uzależnionej.  Jeśli nie istnieje, nie zostanie utworzony token jednostki uzależnionej. |

Element **OrchestrationStep** może zawierać następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Warunki wstępne | 0: n | Lista warunków wstępnych, które muszą być spełnione, aby krok aranżacji został wykonany. |
| ClaimsProviderSelections | 0: n | Lista wybranych dostawców oświadczeń dla kroku aranżacji. |
| ClaimsExchanges | 0: n | Lista wymian oświadczeń dla kroku aranżacji. |
| JourneyList | 0:1 | Lista kandydatów podjazdu na krok aranżacji. |

### <a name="preconditions"></a>Warunki wstępne

Element **warunki wstępne** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Warunek wstępny | 1: n | W zależności od używanego profilu technicznego program przekierowuje klienta zgodnie z wyborem dostawcy oświadczeń lub wysyła do usługi Exchange oświadczenia serwera. |


#### <a name="precondition"></a>Warunek wstępny

Kroki aranżacji można warunkowo wykonać w oparciu o warunki wstępne zdefiniowane w kroku aranżacji. Istnieją dwa typy warunków wstępnych:
 
- **Oświadczenia istnieją** — określa, czy akcje mają być wykonywane, jeśli określone oświadczenia istnieją w bieżącym zbiorze oświadczeń użytkownika.
- Wartość jest **równa** -określa, czy akcje mają być wykonywane, jeśli istnieje określone zastrzeżenie, a jego wartości są równe określonej wartości. Sprawdzanie wykonuje rozróżnienie porządkowe wielkości liter. Podczas sprawdzania typu "Boolean", użyj `True` lub `False` .

Element **Conditional** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| `Type` | Tak | Typ sprawdzenia lub zapytania, które ma zostać wykonane dla tego warunku wstępnego. Wartość może być **ClaimsExist**, która określa, że akcje należy wykonać, jeśli określone oświadczenia istnieją w bieżącym zestawie oświadczeń użytkownika lub **ClaimEquals**, które określa, że akcje należy wykonać, jeśli istnieje określone oświadczenie, a jego wartość jest równa określonej wartości. |
| `ExecuteActionsIf` | Tak | Użyj `true` testu lub, `false` Aby określić, czy akcje w warunku wstępnym mają być wykonywane. |

Elementy **warunku wstępnego** zawierają następujące elementy:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Wartość | 1:2 | Identyfikator typu żądania. Oświadczenie jest już zdefiniowane w sekcji schematu oświadczeń w pliku zasad lub nadrzędnym pliku zasad. Gdy warunek wstępny jest typu `ClaimEquals` , drugi `Value` element zawiera wartość, która ma zostać sprawdzona. |
| Akcja | 1:1 | Akcja, która powinna zostać wykonana, jeśli w kroku aranżacji jest spełniony warunek wstępny. Jeśli wartość `Action` jest ustawiona na `SkipThisOrchestrationStep` , skojarzenie `OrchestrationStep` nie powinno być wykonywane. |

#### <a name="preconditions-examples"></a>Przykłady warunków wstępnych

Poniższe warunki wstępne sprawdzają, czy identyfikator obiektu użytkownika istnieje. W trakcie podróży użytkownika użytkownik zaznaczył zalogować się przy użyciu konta lokalnego. Jeśli objectId istnieje, Pomiń ten krok aranżacji.

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Poniższe warunki wstępne sprawdzają, czy użytkownik zalogował się przy użyciu konta społecznościowego. Podjęto próbę odnalezienia konta użytkownika w katalogu. Jeśli użytkownik loguje się lub loguje się przy użyciu konta lokalnego, Pomiń ten krok aranżacji.

```xml
<OrchestrationStep Order="3" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>authenticationSource</Value>
      <Value>localAccountAuthentication</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="AADUserReadUsingAlternativeSecurityId" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId-NoError" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Warunki wstępne mogą sprawdzić wiele warunków wstępnych. Poniższy przykład sprawdza, czy istnieje element "objectId" lub "email". Jeśli pierwszy warunek ma wartość true, podróż przejdzie do następnego kroku aranżacji.

```xml
<OrchestrationStep Order="4" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>email</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="SelfAsserted-SocialEmail" TechnicalProfileReferenceId="SelfAsserted-SocialEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claims-provider-selection"></a>Wybór dostawcy oświadczeń

Wybór dostawcy tożsamości umożliwia użytkownikom wybranie akcji z listy opcji. Wybór dostawcy tożsamości składa się z pary dwóch kroków aranżacji: 

1. **Przyciski** — zaczynają się od typu `ClaimsProviderSelection` lub, `CombinedSignInAndSignUp` który zawiera listę opcji, spośród których użytkownik może wybrać. Kolejność opcji wewnątrz `ClaimsProviderSelections` elementu określa kolejność przycisków prezentowanych użytkownikowi.
2. **Akcje** — po którym następuje typ `ClaimsExchange` . ClaimsExchange zawiera listę akcji. Akcja to odwołanie do profilu technicznego, takiego jak [OAuth2](oauth2-technical-profile.md), [OpenID Connect Connect](openid-connect-technical-profile.md), [przekształcenie oświadczeń](claims-transformation-technical-profile.md)lub [samodzielne](self-asserted-technical-profile.md). Gdy użytkownik kliknie jeden z przycisków, odpowiednia akcja jest wykonywana.

Element **ClaimsProviderSelections** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| ClaimsProviderSelection | 1: n | Zawiera listę dostawców oświadczeń, które można wybrać.|

Element **ClaimsProviderSelections** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| DisplayOption| Nie | Steruje zachowaniem przypadku, gdy dostępny jest pojedynczy wybór dostawcy oświadczeń. Możliwe wartości: `DoNotShowSingleProvider` (domyślnie) użytkownik zostanie natychmiast przekierowany do dostawcy tożsamości federacyjnych. Lub `ShowSingleProvider` Azure AD B2C przedstawia stronę logowania za pomocą wyboru dostawcy pojedynczej tożsamości. Aby można było użyć tego atrybutu, [wersja definicji zawartości](page-layout.md) musi być `urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0` i nowsza.|

Element **ClaimsProviderSelection** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| TargetClaimsExchangeId | Nie | Identyfikator wymiany oświadczeń, który jest wykonywany w następnym kroku aranżacji wybranego dostawcy oświadczeń. Ten atrybut lub atrybut ValidationClaimsExchangeId musi być określony, ale nie oba. |
| ValidationClaimsExchangeId | Nie | Identyfikator wymiany oświadczeń, który jest wykonywany w bieżącym kroku aranżacji w celu zweryfikowania wyboru dostawcy oświadczeń. Ten atrybut lub atrybut TargetClaimsExchangeId musi być określony, ale nie oba. |

### <a name="claims-provider-selection-example"></a>Przykład wyboru dostawcy oświadczeń

W poniższym kroku aranżacji użytkownik może zalogować się przy użyciu konta w serwisie Facebook, LinkedIn, Twitter, Google lub konto lokalne. Jeśli użytkownik wybierze jednego z dostawców tożsamości społecznościowych, zostanie wykonany drugi krok aranżacji z wybranym określonym w atrybucie wymianą `TargetClaimsExchangeId` . Drugi krok aranżacji przekierowuje użytkownika do dostawcy tożsamości społecznościowej w celu ukończenia procesu logowania. Jeśli użytkownik zdecyduje się na zalogowanie się przy użyciu konta lokalnego, Azure AD B2C pozostaje na tym samym kroku aranżacji (na tej samej stronie rejestracji lub na stronie logowania) i pomija drugi krok aranżacji.

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="LinkedInExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="TwitterExchange" />
    <ClaimsProviderSelection TargetClaimsExchangeId="GoogleExchange" />
    <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
  </ClaimsProviderSelections>
  <ClaimsExchanges>
  <ClaimsExchange Id="LocalAccountSigninEmailExchange"
        TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
  </ClaimsExchanges>
</OrchestrationStep>


<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
    <ClaimsExchange Id="GoogleExchange" TechnicalProfileReferenceId="Google-OAUTH" />
    <ClaimsExchange Id="LinkedInExchange" TechnicalProfileReferenceId="LinkedIn-OAUTH" />
    <ClaimsExchange Id="TwitterExchange" TechnicalProfileReferenceId="Twitter-OAUTH1" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="claimsexchanges"></a>ClaimsExchanges

Element **ClaimsExchanges** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| ClaimsExchange | 1: n | W zależności od używanego profilu technicznego program przekierowuje klienta zgodnie z wybraną ClaimsProviderSelection lub przetworzy połączenie serwera do oświadczeń programu Exchange. |

Element **ClaimsExchange** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| Id | Tak | Identyfikator kroku wymiany oświadczeń. Identyfikator jest używany do odwoływania się do wymiany oświadczeń z poziomu dostawcy oświadczeń w ramach zasad. |
| TechnicalProfileReferenceId | Tak | Identyfikator profilu technicznego, który ma zostać wykonany. |

## <a name="journeylist"></a>JourneyList

Element **JourneyList** zawiera następujący element:

| Element | Wystąpień | Opis |
| ------- | ----------- | ----------- |
| Osoba | 1:1 | Odwołanie do podpodróży, która ma zostać wywołana. |

### <a name="candidate"></a>Osoba

Element **kandydujący** zawiera następujące atrybuty:

| Atrybut | Wymagane | Opis |
| --------- | -------- | ----------- |
| SubJourneyReferenceId | Tak | Identyfikator [podpodróży](subjourneys.md) , która ma zostać wykonana. |
