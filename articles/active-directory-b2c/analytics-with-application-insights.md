---
title: Śledzenie zachowania użytkowników przy użyciu Application Insights
titleSuffix: Azure AD B2C
description: Dowiedz się, jak włączyć dzienniki zdarzeń w Application Insights z Azure AD B2C użytkowników przy użyciu zasad niestandardowych.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 04/05/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67ea7324419d86fa5b5c23a2f0aa5f8c057495d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85385981"
---
# <a name="track-user-behavior-in-azure-active-directory-b2c-using-application-insights"></a>Śledzenie zachowania użytkowników w Azure Active Directory B2C przy użyciu Application Insights

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Azure Active Directory B2C (Azure AD B2C) obsługuje wysyłanie danych zdarzeń bezpośrednio do [Application Insights](../azure-monitor/app/app-insights-overview.md) przy użyciu klucza Instrumentacji dostarczonego do Azure AD B2C.  Profil techniczny Application Insights umożliwia uzyskanie szczegółowych i dostosowanych dzienników zdarzeń dla użytkowników w celu:

* Uzyskaj wgląd w zachowania użytkowników.
* Rozwiązywanie problemów z własnymi zasadami w programowaniu lub w środowisku produkcyjnym.
* Mierzenie wydajności.
* Utwórz powiadomienia z Application Insights.

## <a name="how-it-works"></a>Jak to działa

Profil techniczny [Application Insights](application-insights-technical-profile.md) definiuje zdarzenie z Azure AD B2C. Profil określa nazwę zdarzenia, rejestrowane oświadczenia oraz klucz Instrumentacji. W celu opublikowania zdarzenia profil techniczny zostanie następnie dodany jako krok aranżacji w [podróży użytkownika](userjourneys.md).

Application Insights może ujednolicić zdarzenia, używając identyfikatora korelacji do rejestrowania sesji użytkownika. Application Insights powoduje, że zdarzenie i sesja są dostępne w ciągu kilku sekund i prezentuje wiele narzędzi do wizualizacji, eksportowania i analitycznych.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w temacie Wprowadzenie [do zasad niestandardowych](custom-policy-get-started.md). Należy mieć działającą niestandardową zasadę do rejestracji i logowania przy użyciu kont lokalnych.

## <a name="create-an-application-insights-resource"></a>Tworzenie zasobu usługi Application Insights

Jeśli używasz Application Insights z Azure AD B2C, wystarczy utworzyć zasób i uzyskać klucz Instrumentacji. Aby uzyskać więcej informacji, zobacz [Tworzenie zasobu Application Insights](../azure-monitor/app/create-new-resource.md)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Upewnij się, że używasz katalogu, który zawiera subskrypcję platformy Azure, wybierając filtr **katalog + subskrypcja** w górnym menu i wybierając katalog zawierający twoją subskrypcję. Ta dzierżawa nie jest dzierżawą Azure AD B2C.
3. Wybierz pozycję **Utwórz zasób** w lewym górnym rogu Azure Portal, a następnie wyszukaj i wybierz pozycję **Application Insights**.
4. Kliknij przycisk **Utwórz**.
5. Wprowadź **nazwę** zasobu.
6. W obszarze **Typ aplikacji**wybierz pozycję **aplikacja sieci Web ASP.NET**.
7. W obszarze **Grupa zasobów**wybierz istniejącą grupę lub wprowadź nazwę nowej grupy.
8. Kliknij przycisk **Utwórz**.
4. Po utworzeniu zasobu Application Insights Otwórz go, rozwiń węzeł **Essentials**i skopiuj klucz Instrumentacji.

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

Profile techniczne mogą być uznawane za funkcje w środowisku tożsamości Azure AD B2C. W tej tabeli opisano profile techniczne, które są używane do otwierania sesji i publikowania zdarzeń.

| Profil techniczny | Zadanie |
| ----------------- | -----|
| AppInsights-Common | Wspólny zestaw parametrów do uwzględnienia we wszystkich profilach technicznych usługi Azure Insights. |
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

Zapisz i Przekaż plik *TrustFrameworkExtensions.xml* . Następnie należy wywołać zasady jednostki uzależnionej z poziomu aplikacji lub użyć **Uruchom teraz** w Azure Portal. W ciągu kilku sekund Twoje zdarzenia są dostępne w Application Insights.

1. Otwórz zasób **Application Insights** w dzierżawie Azure Active Directory.
2. Wybierz **Usage**pozycję  >  **zdarzenia**użycia.
3. Ustawiaj w **ciągu** **ostatniej godziny** i **przez** maksymalnie **3 minuty**.  Może być konieczne wybranie opcji **Odśwież** , aby wyświetlić wyniki.

![Application Insights USAGE-Events Blase](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="optional-collect-more-data"></a>Obowiązkowe Zbieraj więcej danych

Dodawanie typów i zdarzeń roszczeń do podróży użytkownika w celu dopasowania do Twoich potrzeb. Można użyć [resolverów oświadczeń](claim-resolver-overview.md) lub dowolnego typu oświadczenia ciągu, dodać oświadczenia poprzez dodanie elementu **oświadczenia wejściowego** do zdarzenia Application Insights lub do AppInsights-Common profilu technicznego.

- **ClaimTypeReferenceId** jest odwołaniem do typu zgłoszenia.
- **PartnerClaimType** to nazwa właściwości, która pojawia się w usłudze Azure Insights. Użyj składni `{property:NAME}` , gdzie `NAME` jest dodawana właściwość do zdarzenia.
- **Właściwość DefaultValue** używa dowolnej wartości ciągu lub mechanizmu rozwiązywania konfliktów.

```xml
<InputClaim ClaimTypeReferenceId="app_session" PartnerClaimType="{property:app_session}" DefaultValue="{OAUTH-KV:app_session}" />
<InputClaim ClaimTypeReferenceId="loyalty_number" PartnerClaimType="{property:loyalty_number}" DefaultValue="{OAUTH-KV:loyalty_number}" />
<InputClaim ClaimTypeReferenceId="language" PartnerClaimType="{property:language}" DefaultValue="{Culture:RFC5646}" />
```

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o profilu technicznym [Application Insights](application-insights-technical-profile.md) w dokumentacji IEF. 
