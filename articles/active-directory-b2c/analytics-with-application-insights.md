---
title: Śledzenie zachowania użytkowników przy użyciu Application Insights
titleSuffix: Azure AD B2C
description: Dowiedz się, jak włączyć dzienniki zdarzeń w Application Insights z Azure AD B2C użytkowników.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 01/29/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ce80e3376482ef44b466757cf7e345c4bcf186ad
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99218557"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Śledzenie zachowania użytkowników w Azure Active Directory B2C przy użyciu Application Insights

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure Active Directory B2C (Azure AD B2C) obsługuje wysyłanie danych zdarzeń bezpośrednio do [Application Insights](../azure-monitor/app/app-insights-overview.md) przy użyciu klucza Instrumentacji dostarczonego do Azure AD B2C. Profil techniczny Application Insights umożliwia uzyskanie szczegółowych i dostosowanych dzienników zdarzeń dla użytkowników w celu:

* Uzyskaj wgląd w zachowania użytkowników.
* Rozwiązywanie problemów z własnymi zasadami w programowaniu lub w środowisku produkcyjnym.
* Mierzenie wydajności.
* Utwórz powiadomienia z Application Insights.

## <a name="overview"></a>Omówienie

Aby włączyć niestandardowe dzienniki zdarzeń, należy dodać profil techniczny Application Insights. W profilu technicznym należy zdefiniować Application Insights klucz instrumentacji, nazwę zdarzenia i oświadczenia do rejestrowania. W celu opublikowania zdarzenia profil techniczny zostanie następnie dodany jako krok aranżacji w [podróży użytkownika](userjourneys.md).

Korzystając z Application Insights, należy wziąć pod uwagę następujące kwestie:

- Istnieje krótkie opóźnienie, zazwyczaj mniej niż pięć minut, zanim nowe dzienniki będą dostępne w Application Insights.
- Azure AD B2C pozwala wybrać oświadczenia, które mają być rejestrowane. Nie dodawaj oświadczeń zawierających dane osobowe.
- Aby zarejestrować sesję użytkownika, zdarzenia mogą być ujednolicone przy użyciu identyfikatora korelacji. 
- Wywołaj Application Insights profil techniczny bezpośrednio od [podróży użytkownika](userjourneys.md) lub [podróży podrzędnych](subjourneys.md). Nie używaj Application Insights profilu technicznego jako [weryfikacji profilu technicznego](validation-technical-profile.md).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights

Jeśli używasz Application Insights z Azure AD B2C, wystarczy utworzyć zasób i uzyskać klucz Instrumentacji. Aby uzyskać więcej informacji, zobacz [Tworzenie zasobu Application Insights](../azure-monitor/app/create-new-resource.md)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu, który zawiera subskrypcję platformy Azure, wybierając filtr **katalog + subskrypcja** w górnym menu i wybierając katalog zawierający twoją subskrypcję. Ta dzierżawa nie jest dzierżawą Azure AD B2C.
3. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu Azure Portal, a następnie wyszukaj i wybierz pozycję **Application Insights**.
4. Kliknij pozycję **Utwórz**.
5. Wprowadź **nazwę** zasobu.
6. W obszarze **Typ aplikacji** wybierz pozycję **aplikacja sieci Web ASP.NET**.
7. W obszarze **Grupa zasobów** wybierz istniejącą grupę lub wprowadź nazwę nowej grupy.
8. Kliknij pozycję **Utwórz**.
4. Po utworzeniu zasobu Application Insights Otwórz go, rozwiń węzeł **Essentials** i skopiuj klucz Instrumentacji.

![Przegląd Application Insights i klucz Instrumentacji](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>Definiowanie oświadczeń

W trakcie wykonywania zasad Azure AD B2C, zgłoszenie zapewnia tymczasowy magazyn danych. [Schemat oświadczeń](claimsschema.md) jest miejscem, w którym deklarujesz oświadczenia.

1. Otwórz plik rozszerzeń zasad. Na przykład <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Wyszukaj element [BuildingBlocks](buildingblocks.md) . Jeśli element nie istnieje, Dodaj go.
1. Znajdź element [ClaimsSchema](claimsschema.md) . Jeśli element nie istnieje, Dodaj go.
1. Dodaj następujące oświadczenia do elementu **ClaimsSchema** . 

```xml
<ClaimType Id="EventType">
  <DisplayName>Event type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="EventTimestamp">
  <DisplayName>Event timestamp</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="PolicyId">
  <DisplayName>Policy Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="Culture">
  <DisplayName>Culture ID</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="CorrelationId">
  <DisplayName>Correlation Id</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="federatedUser">
  <DisplayName>Federated user</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
<ClaimType Id="parsedDomain">
  <DisplayName>Domain name</DisplayName>
  <DataType>string</DataType>
  <UserHelpText>The domain portion of the email address.</UserHelpText>
</ClaimType>
<ClaimType Id="userInLocalDirectory">
  <DisplayName>userInLocalDirectory</DisplayName>
  <DataType>boolean</DataType>
</ClaimType>
```

## <a name="add-new-technical-profiles"></a>Dodaj nowe profile techniczne

Profile techniczne mogą być uznawane za funkcje w zasadach niestandardowych. W tej tabeli opisano profile techniczne, które są używane do otwierania sesji i publikowania zdarzeń. Rozwiązanie używa podejścia do [włączenia profilu technicznego](technicalprofiles.md#include-technical-profile) . Profil techniczny zawiera inny profil techniczny, aby zmienić ustawienia lub dodać nową funkcję.

| Profil techniczny | Zadanie |
| ----------------- | -----|
| AppInsights-Common | Typowy profil techniczny ze wspólnym zestawem konfiguracji. Dołączenie klucza Instrumentacji Application Insights, zbieranie oświadczeń do rejestrowania i tryb dewelopera. Następujące profile techniczne obejmują wspólny profil techniczny i dodają więcej oświadczeń, takich jak nazwa zdarzenia. |
| AppInsights-SignInRequest | Rejestruje `SignInRequest` zdarzenie z zestawem oświadczeń po odebraniu żądania logowania. |
| AppInsights-UserSignUp | Rejestruje `UserSignUp` zdarzenie, gdy użytkownik wyzwala opcję rejestracji w trakcie rejestracji/logowania. |
| AppInsights-SignInComplete | Rejestruje `SignInComplete` zdarzenie po pomyślnym zakończeniu uwierzytelniania, gdy token został wysłany do aplikacji jednostki uzależnionej. |

Dodaj profile do pliku *TrustFrameworkExtensions.xml* z pakietu początkowego. Dodaj te elementy do elementu **ClaimsProviders** :

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key which will be used for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="EventTimestamp" PartnerClaimType="{property:EventTimestamp}" DefaultValue="{Context:DateTimeInUtc}" />
        <InputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="{property:TenantId}" DefaultValue="{Policy:TrustFrameworkTenantId}" />
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-UserSignUp">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignUp" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
    
    <TechnicalProfile Id="AppInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
        <InputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="{property:IDP}" DefaultValue="Local" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> Zmień klucz Instrumentacji w `AppInsights-Common` profilu technicznym na identyfikator GUID, który zapewnia zasób Application Insights.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>Dodaj profile techniczne jako kroki aranżacji

Wywołaj `AppInsights-SignInRequest` jako aranżację krok 2, aby śledzić, że Odebrano żądanie logowania/rejestracji:

```xml
<!-- Track that we have received a sign in request -->
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Bezpośrednio *przed* `SendClaims` krokiem aranżacji Dodaj nowy krok, który wywołuje `AppInsights-UserSignup` . Jest wyzwalane, gdy użytkownik wybierze przycisk rejestracji w podróży/logowaniu.

```xml
<!-- Handles the user clicking the sign up link in the local account sign in page -->
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
      <Value>newUser</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
    <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
      <Value>newUser</Value>
      <Value>false</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AppInsights-UserSignup" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Natychmiast po `SendClaims` kroku aranżacji Wywołaj polecenie `AppInsights-SignInComplete` . Ten krok przedstawia pomyślne zakończenie podróży.

```xml
<!-- Track that we have successfully sent a token -->
<OrchestrationStep Order="10" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
  </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> Po dodaniu nowych kroków aranżacji ponownie przystąpi kolejne kroki bez pomijania wartości całkowitych z zakresu od 1 do N.


## <a name="upload-your-file-run-the-policy-and-view-events"></a>Przekaż plik, uruchom zasady i Wyświetl zdarzenia

Zapisz i Przekaż plik *TrustFrameworkExtensions.xml* . Następnie należy wywołać zasady jednostki uzależnionej z poziomu aplikacji lub użyć **Uruchom teraz** w Azure Portal. Poczekaj chwilę, a Twoje zdarzenia będą dostępne w Application Insights.

1. Otwórz zasób **Application Insights** w dzierżawie Azure Active Directory.
2. Wybierz pozycję **użycie**, a następnie wybierz pozycję **zdarzenia**.
3. Ustawiaj w **ciągu** **ostatniej godziny** i **przez** maksymalnie **3 minuty**.  Może być konieczne wybranie opcji **Odśwież** , aby wyświetlić wyniki.

![Application Insights USAGE-Events Blase](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="collect-more-data"></a>Zbieraj więcej danych

Aby dopasować się do potrzeb firmy, warto zarejestrować więcej oświadczeń. Aby dodać oświadczenie, najpierw [Zdefiniuj oświadczenie](#define-claims), a następnie Dodaj oświadczenie do kolekcji oświadczeń wejściowych. Oświadczenia dodawane do profilu technicznego *AppInsights-Common* są wyświetlane we wszystkich zdarzeniach. Oświadczenia dodawane do określonego profilu technicznego będą wyświetlane tylko w tym zdarzeniu. Element Claims input zawiera następujące atrybuty:

- **ClaimTypeReferenceId** — jest odwołaniem do typu zgłoszenia. 
- **PartnerClaimType** — to nazwa właściwości, która pojawia się w usłudze Azure Insights. Użyj składni `{property:NAME}` , gdzie `NAME` jest dodawana właściwość do zdarzenia.
- **DefaultValue** — zdefiniowana wartość, która ma zostać zarejestrowana, taka jak nazwa zdarzenia. Zgłoszenie, które jest używane w podróży użytkownika, takie jak nazwa dostawcy tożsamości. Jeśli to zgłoszenie jest puste, zostanie użyta wartość domyślna. Na przykład to zgłoszenie `identityProvider` jest ustawiane przez profile techniczne Federacji, takie jak Facebook. Jeśli to zgłoszenie jest puste, oznacza to, że użytkownik loguje się przy użyciu konta lokalnego. W ten sposób wartość domyślna to *Local*. Można też zarejestrować [resolvery roszczeń](claim-resolver-overview.md) z wartością kontekstową, taką jak identyfikator aplikacji lub adres IP użytkownika.

### <a name="manipulating-claims"></a>Manipulowanie oświadczeniami

Możesz użyć [przekształceń oświadczeń wejściowych](custom-policy-trust-frameworks.md#manipulating-your-claims) , aby zmodyfikować oświadczenia wejściowe lub wygenerować nowe przed wysłaniem do Application Insights. W poniższym przykładzie profil techniczny obejmuje transformację oświadczeń wejściowych *CheckIsAdmin* . 

```xml
<TechnicalProfile Id="AppInsights-SignInComplete">
  <InputClaimsTransformations>  
    <InputClaimsTransformation ReferenceId="CheckIsAdmin" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isAdmin" PartnerClaimType="{property:IsAdmin}"  />
    ...
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

### <a name="add-events"></a>Dodaj zdarzenia

Aby dodać zdarzenie, Utwórz nowy profil techniczny zawierający profil techniczny *AppInsights-Common* . Następnie Dodaj profil techniczny jako krok aranżacji do [podróży użytkownika](custom-policy-trust-frameworks.md#orchestration-steps). Użyj [warunku wstępnego](userjourneys.md#preconditions) , aby wyzwolić zdarzenie w razie potrzeby. Na przykład zgłoś zdarzenie tylko wtedy, gdy użytkownicy uruchamiają się za pomocą usługi MFA.

```xml
<TechnicalProfile Id="AppInsights-MFA-Completed">
  <InputClaims>
     <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="MFA-Completed" />
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

Teraz, gdy masz profil techniczny, Dodaj wydarzenie do podróży użytkownika. Następnie ponownie przenumerowane kroki bez pomijania wartości całkowitych z zakresu od 1 do N.

```xml
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
    <Value>isActiveMFASession</Value>
    <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserMfaCompleted" TechnicalProfileReferenceId="AppInsights-MFA-Completed" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="enable-developer-mode"></a>Włącz tryb dewelopera

Podczas definiowania zdarzeń przy użyciu Application Insights można wskazać, czy tryb dewelopera jest włączony. Tryb dewelopera kontroluje sposób buforowania zdarzeń. W środowisku programistycznym z minimalnym woluminem zdarzeń włączenie trybu dewelopera powoduje natychmiastowe wysyłanie zdarzeń do Application Insights. Wartość domyślna to `false`. Nie włączaj trybu dewelopera w środowiskach produkcyjnych.

Aby włączyć tryb dewelopera, w profilu technicznym *AppInsights-Common* Zmień `DeveloperMode` metadane na `true` : 

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DeveloperMode">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="disable-telemetry"></a>Wyłącz telemetrię

Aby wyłączyć dzienniki usługi Application Insights, w profilu technicznym *AppInsights-Common* Zmień `DisableTelemetry` metadane na `true` : 

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DisableTelemetry">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [tworzyć niestandardowe pulpity nawigacyjne wskaźników KPI przy użyciu usługi Azure Application Insights](../azure-monitor/learn/tutorial-app-dashboards.md). 

::: zone-end