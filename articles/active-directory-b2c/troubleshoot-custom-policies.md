---
title: Rozwiązywanie problemów z zasadami niestandardowymi w Azure Active Directory B2C
description: Dowiedz się więcej na temat metod rozwiązywania problemów podczas pracy z zasadami niestandardowymi w programie Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bbb3bc0e34ad596c39aebb49124bb72d0b3efe6f
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103969"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies"></a>Rozwiązywanie problemów Azure AD B2C zasad niestandardowych

W przypadku używania [zasad niestandardowych](custom-policy-overview.md)Azure Active Directory B2C (Azure AD B2C) mogą wystąpić problemy z formatem XML języka zasad lub problemami w czasie wykonywania. W tym artykule opisano niektóre narzędzia i wskazówki, które mogą pomóc w wykrywaniu i rozwiązywaniu problemów. 

Ten artykuł koncentruje się na rozwiązywaniu problemów z konfiguracją zasad niestandardowych Azure AD B2C. Nie dotyczy to aplikacji jednostki uzależnionej ani jej biblioteki tożsamości.

## <a name="azure-ad-b2c-correlation-id-overview"></a>Azure AD B2C — Omówienie identyfikatora korelacji

Identyfikator korelacji Azure AD B2C jest unikatowym identyfikatorem, który jest dołączany do żądań autoryzacji. Przechodzi przez wszystkie kroki aranżacji, za pomocą których użytkownik jest zajęty. Identyfikator korelacji umożliwia:

- Zidentyfikuj aktywność logowania w aplikacji i śledź wydajność swoich zasad.
- Znajdź dzienniki usługi Azure Application Insights na żądanie logowania.
- Przekaż identyfikator korelacji do interfejsu API REST i użyj go do zidentyfikowania przepływu logowania. 

Identyfikator korelacji jest zmieniany za każdym razem, gdy zostanie ustanowiona Nowa sesja. Podczas debugowania zasad upewnij się, że istnieją karty Zamknij istniejące przeglądarki. Lub Otwórz nową przeglądarkę w trybie prywatnym.

### <a name="get-the-azure-ad-b2c-correlation-id"></a>Pobierz identyfikator korelacji Azure AD B2C

Identyfikator korelacji można znaleźć na stronie Azure AD B2C rejestracji lub logowania. W przeglądarce wybierz pozycję **Wyświetl źródło**. Korelacja pojawia się jako komentarz w górnej części strony.

![Zrzut ekranu przedstawiający Źródło widoku strony logowania Azure AD B2C.](./media/troubleshoot-custom-policies/find-azure-ad-b2c-correlation-id.png)

Skopiuj identyfikator korelacji, a następnie kontynuuj przepływ logowania. Użyj identyfikatora korelacji, aby obserwować zachowanie logowania. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie problemów z Application Insights](#troubleshooting-with-application-insights).

### <a name="echo-the-azure-ad-b2c-correlation-id"></a>Echo Azure AD B2C identyfikator korelacji

W tokenach Azure AD B2C można uwzględnić identyfikator korelacji. Aby dołączyć identyfikator korelacji:

1. Otwórz plik rozszerzeń zasad. Na przykład <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Wyszukaj element [BuildingBlocks](buildingblocks.md) . Jeśli element nie istnieje, Dodaj go.
1. Znajdź element [ClaimsSchema](claimsschema.md) . Jeśli element nie istnieje, Dodaj go.
1. Dodaj wierzytelność do elementu **ClaimsSchema** .  

    ```xml
    <!-- 
    <BuildingBlocks>
      <ClaimsSchema> -->
        <ClaimType Id="correlationId">
          <DisplayName>correlation ID</DisplayName>
          <DataType>string</DataType>
        </ClaimType>
      <!-- 
      </ClaimsSchema>
    </BuildingBlocks>-->
    ```

1. Otwórz plik jednostki uzależnionej zasad. Na przykład <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> plik. Zgłoszenie danych wyjściowych zostanie dodane do tokenu po pomyślnym przejściu użytkownika i wysłaniu do aplikacji. Zmodyfikuj element profil techniczny w sekcji jednostki uzależnionej, aby dodać miasto jako zgłoszenie wyjściowe.
 
    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          ...
          <OutputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```


## <a name="troubleshooting-with-application-insights"></a>Rozwiązywanie problemów z Application Insights

Aby zdiagnozować problemy z zasadami niestandardowymi, użyj [Application Insights](troubleshoot-with-application-insights.md). Application Insights śledzi aktywność niestandardowej podróży użytkownika zasad. Umożliwia ona diagnozowanie wyjątków i przestrzeganie wymiany oświadczeń między Azure AD B2C a różnymi dostawcami oświadczeń zdefiniowanymi przez profile techniczne, takich jak dostawcy tożsamości, usługi oparte na interfejsie API, katalog Azure AD B2C użytkowników i inne usługi.  

Zalecamy zainstalowanie [rozszerzenia Azure AD B2C](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) [vs Code](https://code.visualstudio.com/). Dzięki rozszerzeniu Azure AD B2C dzienniki są zorganizowane według nazwy zasad, identyfikatora korelacji (Application Insights przedstawia pierwszą cyfrę identyfikatora korelacji) oraz sygnaturę czasową dziennika. Ta funkcja ułatwia znalezienie odpowiedniego dziennika w oparciu o lokalną sygnaturę czasową i przeglądanie podróży użytkownika w sposób wykonywany przez Azure AD B2C. 

> [!NOTE]
> - Istnieje krótkie opóźnienie, zazwyczaj mniej niż pięć minut, zanim będzie można zobaczyć nowe dzienniki w Application Insights.
> - Społeczność opracowała rozszerzenie Visual Studio Code dla Azure AD B2C, aby pomóc deweloperom w zakresie tożsamości. Rozszerzenie nie jest obsługiwane przez firmę Microsoft i jest udostępniane w sposób niezgodny z oczekiwaniami.

Pojedynczy przepływ logowania może wydać więcej niż jeden ślad usługi Azure Application Insights. Na poniższym zrzucie ekranu zasady *B2C_1A_signup_signin* mają trzy dzienniki. Każdy dziennik reprezentuje część przepływu logowania.

Poniższy zrzut ekranu przedstawia Azure AD B2C rozszerzenia VS Code za pomocą Eksploratora śledzenia Azure Application Insights.

![Zrzut ekranu rozszerzenia Azure AD B2C dla VS Code z usługą Azure Application Insights Trace.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-trace.png)

### <a name="filter-the-trace-log"></a>Filtrowanie dziennika śledzenia

Mając fokus w Eksploratorze śledzenia Azure AD B2C, Zacznij od wpisania pierwszej cyfry identyfikatora korelacji lub czasu, który ma zostać znaleziony. Zobaczysz pole filtru w prawym górnym rogu Eksploratora śledzenia Azure AD B2C, który pokazuje, co zostało wpisane do tej pory, i że zostaną wyróżnione pasujące dzienniki śledzenia.  

![Zrzut ekranu rozszerzenia Azure AD B2C Azure AD B2C wyróżniania filtru Eksploratora śledzenia.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-highlight.png)

Umieszczenie kursora nad polem filtru i wybranie opcji **Włącz filtr dla typu** spowoduje wyświetlenie tylko pasujących dzienników śledzenia. Użyj **przycisku Wyczyść "X"** , aby wyczyścić filtr.

![Zrzut ekranu przedstawiający Azure AD B2C rozszerzenia Azure AD B2C filtr Eksploratora śledzenia.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-filter.png)

### <a name="application-insights-trace-log-details"></a>Szczegóły dziennika śledzenia Application Insights

Po wybraniu śledzenia Application Insights platformy Azure rozszerzenie otwiera okno **szczegóły Application Insights** z następującymi informacjami:

- **Application Insights** — ogólne informacje o dzienniku śledzenia, w tym nazwa zasad, identyfikator korelacji, identyfikator śledzenia usługi Azure Application Insights i śledzenie sygnatur czasowych.
- **Profile techniczne** — lista profilów technicznych, które pojawiają się w dzienniku śledzenia.
- **Oświadczenia** — Alfabetyczna lista oświadczeń, które pojawiają się w dzienniku śledzenia i ich wartości. Jeśli w dzienniku śledzenia występuje wiele razy więcej wartości, `=>` znak wyznacza najnowszą wartość. Możesz przejrzeć te oświadczenia, aby określić, czy oczekiwane wartości oświadczeń są ustawione prawidłowo. Na przykład jeśli masz warunek wstępny, który sprawdza wartość oświadczenia, sekcja oświadczenia może pomóc w ustaleniu, dlaczego oczekiwany przepływ działa inaczej.
- **Przekształcanie oświadczeń** — lista transformacji oświadczeń, które pojawiają się w dzienniku śledzenia. Każda transformacja oświadczeń zawiera oświadczenia wejściowe, parametry wejściowe i oświadczenia wyjściowe. Sekcja transformacja oświadczeń zawiera szczegółowe informacje o danych wysłanych i wynikach transformacji oświadczeń.
- **Tokeny** — lista tokenów, które pojawiają się w dzienniku śledzenia. Tokeny obejmują tokeny federacyjnego dostawcy tożsamości protokołu OAuth i OpenID Connect Connect. Token dostawcy tożsamości federacyjnych zawiera szczegółowe informacje dotyczące sposobu, w jaki dostawca tożsamości zwraca oświadczenia do Azure AD B2C, dzięki czemu można mapować oświadczenia dotyczące danych wyjściowych profilu technicznego dostawcy tożsamości. 
- **Wyjątki** — Lista wyjątków lub błędów krytycznych, które pojawiają się w dzienniku śledzenia.
- **Application INSIGHTS JSON** — dane pierwotne zwracane z Application Insights.

Poniższy zrzut ekranu przedstawia przykład okna Szczegóły dziennika śledzenia Application Insights.  

![Zrzut ekranu przedstawiający Azure AD B2C rozszerzenia Azure AD B2C raport śledzenia.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-report.png)

## <a name="troubleshoot-jwt-tokens"></a>Rozwiązywanie problemów z tokenami JWT

W celu sprawdzenia poprawności tokenu JWT i debugowania można zdekodować JWTs za pomocą witryny takiej jak [https://jwt.ms](https://jwt.ms) . Utwórz aplikację testową, która może przekierować do `https://jwt.ms` inspekcji tokenu. Jeśli jeszcze tego nie zrobiono, [zarejestruj aplikację sieci Web](tutorial-register-applications.md)i [Włącz niejawne przyznanie tokenu identyfikatora](tutorial-register-applications.md#enable-id-token-implicit-grant). 

![Zrzut ekranu przedstawiający token JWT w wersji zapoznawczej.](./media/troubleshoot-custom-policies/jwt-token-preview.png)

Używaj **teraz** i `https://jwt.ms` Przetestuj swoje zasady niezależnie od aplikacji sieci Web lub mobilnej. Ta witryna sieci Web działa jak aplikacja jednostki uzależnionej. Wyświetla zawartość tokenu sieci Web JSON (JWT) wygenerowanego przez zasady Azure AD B2C.

## <a name="troubleshoot-saml-protocol"></a>Rozwiązywanie problemów z protokołem SAML

Aby pomóc w konfigurowaniu i debugowaniu integracji z dostawcą usług, można użyć rozszerzenia przeglądarki dla protokołu SAML, na przykład [rozszerzenia SAML devtools](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) dla programu Chrome, języka [SAML-Tracer](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) dla przeglądarki Firefox lub [narzędzi programistycznych przeglądarki Edge lub IE](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

Poniższy zrzut ekranu pokazuje, jak rozszerzenie SAML DevTools przedstawia żądanie SAML Azure AD B2C wysyłane do dostawcy tożsamości i odpowiedzi SAML.

![Zrzut ekranu przedstawiający dziennik śledzenia protokołu SAML.](./media/troubleshoot-custom-policies/saml-protocol-trace.png)

Korzystając z tych narzędzi, można sprawdzić integrację aplikacji i Azure AD B2C. Na przykład:

- Sprawdź, czy żądanie SAML zawiera podpis i określ algorytm używany do logowania żądania autoryzacji.
- Sprawdź, czy Azure AD B2C zwraca komunikat o błędzie.
- Sprawdź, czy sekcja potwierdzenia jest zaszyfrowana.
- Pobierz nazwę oświadczeń zwracających dostawcę tożsamości.

Możesz również śledzić wymianę komunikatów między przeglądarką klienta i Azure AD B2C przy użyciu [programu Fiddler](https://www.telerik.com/fiddler). Może to pomóc w uzyskaniu informacji o tym, gdzie podróż użytkownika kończy się niepowodzeniem w ramach kroków aranżacji.

## <a name="troubleshoot-policy-validity"></a>Rozwiązywanie problemów z ważnością zasad

Po zakończeniu opracowywania zasad należy przekazać zasady do Azure AD B2C. mogą wystąpić problemy związane z zasadami. Użyj następujących metod, aby zapewnić integralność/ważność zasad.

Najbardziej typowym błędem konfigurowania zasad niestandardowych jest nieprawidłowo sformatowana zawartość XML. Dobry Edytor XML jest niemal istotny. Wyświetla natywnie XML, zawartość kodów kolorów, wstępnie wypełnień wspólnych warunków, zachowuje indeksowane elementy XML i może być zweryfikowane względem schematu XML.

Zalecamy używanie [Visual Studio Code](https://code.visualstudio.com/). Następnie zainstaluj rozszerzenie XML, takie jak [Obsługa języka XML przez Red Hat](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-xml). Rozszerzenie XML umożliwia sprawdzenie poprawności schematu XML przed przekazaniem pliku XML przy użyciu definicji schematu [XSD](https://raw.githubusercontent.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/master/TrustFrameworkPolicy_0.3.0.0.xsd) zasad niestandardowych.

Za pomocą strategii kojarzenia plików XML można powiązać plik XML ze schematem XSD, dodając następujące ustawienia do pliku VS Code `settings.json` . W tym celu:

1. W obszarze VS Code kliknij **Ustawienia**. Aby uzyskać więcej informacji, zobacz [Ustawienia użytkownika i obszaru roboczego](https://code.visualstudio.com/docs/getstarted/settings).
1. Wyszukaj pozycję **fileAssociations**, a następnie w obszarze **rozszerzenia** wybierz **plik XML**.
1. Wybierz pozycję **Edytuj w settings.jsna**.

    ![Zrzut ekranu przedstawiający VS Code walidacji schematu XML.](./media/troubleshoot-custom-policies/xml-validation.png)
1. W settings.jsna Dodaj następujący kod JSON:

    ```json
    "xml.fileAssociations": [
      {
        "pattern": "**.xml",
        "systemId": "https://raw.githubusercontent.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/master/TrustFrameworkPolicy_0.3.0.0.xsd"
      }
    ]
    ```

Poniższy przykład pokazuje błąd sprawdzania poprawności kodu XML. Gdy przesuwasz wskaźnik myszy nad nazwą elementu, rozszerzenie wyświetli oczekiwane elementy.

![Zrzut ekranu przedstawiający wskaźnik błędu walidacji schematu XML VS Code.](./media/troubleshoot-custom-policies/xml-validation-error.png)

W poniższym przypadku `DisplayName` element jest poprawny. Ale w niewłaściwej kolejności. `DisplayName`Powinien znajdować się przed `Protocol` elementem. Aby rozwiązać ten problem, przesuń wskaźnik myszy nad `DisplayName` element do odpowiedniej kolejności elementów.

![Zrzut ekranu przedstawiający błąd kolejności walidacji schematu XML VS Code.](./media/troubleshoot-custom-policies/xml-validation-order-error.png)

## <a name="upload-policies-and-policy-validation"></a>Przekazywanie zasad i sprawdzanie poprawności zasad

Sprawdzanie poprawności pliku zasad XML jest wykonywane automatycznie przy przekazywaniu. Większość błędów powoduje niepowodzenie przekazywania. Walidacja obejmuje plik zasad, który jest przekazywany. Obejmuje również łańcuch plików, do których odnosi się plik przekazywania (plik zasad jednostki uzależnionej, plik rozszerzeń i plik podstawowy).

> [!TIP]
> Azure AD B2C uruchamia dodatkowe sprawdzanie poprawności zasad jednostki uzależnionej. Gdy występuje problem z zasadami, nawet jeśli edytujesz tylko zasady rozszerzenia, dobrym sposobem jest również przekazanie zasad jednostki uzależnionej. 

Ta sekcja zawiera typowe błędy walidacji i prawdopodobne rozwiązania.

### <a name="schema-validation-error-found-has-invalid-child-element-name"></a>Znaleziono błąd walidacji schematu... ma nieprawidłowy element podrzędny "{name}"

Twoje zasady zawierają nieprawidłowy element XML lub element XML jest prawidłowy, ale wygląda na to w niewłaściwej kolejności. Aby naprawić ten typ błędu, zapoznaj się z sekcją [ważność zasad rozwiązywania problemów](#troubleshoot-policy-validity) .

### <a name="there-is-a-duplicate-key-sequence-number"></a>Istnieje zduplikowana sekwencja klucza "{number}" 

[Podróż](userjourneys.md) lub [podpodróż](subjourneys.md) użytkownika obejmuje uporządkowaną listę kroków aranżacji, które są wykonywane w kolejności. Po zmianie kursu należy ponownie ponownie wykonać kroki bez pomijania wartości całkowitych z zakresu od 1 do N.

> [!TIP]
> Możesz użyć [rozszerzenia Azure AD B2C](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) dla [vs Code](https://code.visualstudio.com/) `(Shift+Ctrl+r)` polecenia, aby ponownie przenumerować wszystkie czynności związane z aranżacją i podpodróży użytkownika w ramach zasad.

### <a name="was-expected-to-have-step-with-order-number-but-it-was-not-found"></a>... powinien mieć krok z kolejnością "{number}", ale nie został znaleziony...

Sprawdź poprzedni błąd.

### <a name="orchestration-step-order-number-in-user-journey-name-is-followed-by-a-claims-provider-selection-step-and-must-be-a-claims-exchange-but-it-is-of-type"></a>Kolejność etapów aranżacji "{number}" w podróży użytkownika "{name}"... następuje krok wyboru dostawcy oświadczeń i musi być wymianą oświadczeń, ale jest typu...

Typ kroków aranżacji `ClaimsProviderSelection` i `CombinedSignInAndSignUp` zawiera listę opcji, spośród których użytkownik może wybrać. Musi być zgodna z typem z `ClaimsExchange` co najmniej jedną wymianą oświadczeń.

Poniższe kroki aranżacji powodują wystąpienie tego typu lub błędu. Drugi krok aranżacji musi być typu, a `ClaimsExchange` nie `ClaimsProviderSelection` .

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
        <ClaimsProviderSelections>
          <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange"/>
          <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange"/>
        </ClaimsProviderSelections>
        <ClaimsExchanges>
          <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email"/>
        </ClaimsExchanges>
      </OrchestrationStep> 
      
      <OrchestrationStep Order="2" Type="ClaimsProviderSelection">
        ...
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="step-number-with-2-claims-exchanges-it-must-be-preceded-by-a-claims-provider-selection-in-order-to-determine-which-claims-exchange-can-be-used"></a>... Krok {Number} z 2 wymianą oświadczeń. Aby określić, który z nich może być używany, musi być poprzedzony wyborem dostawcy oświadczeń.

Typ kroku aranżacji `ClaimsExchange` musi mieć pojedynczy `ClaimsExchange` , chyba że poprzedni krok jest typu `ClaimsProviderSelection` lub `CombinedSignInAndSignUp` . Poniższe kroki aranżacji powodują wystąpienie tego typu błędu. Szósty krok zawiera dwie oświadczenia. 

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="5" Type="ClaimsExchange">
        ...
        <ClaimsExchanges>
          <ClaimsExchange Id="SelfAsserted-Social" TechnicalProfileReferenceId="SelfAsserted-Social"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="6" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-First-API" TechnicalProfileReferenceId="Call-REST-First-API"/>
          <ClaimsExchange Id="Call-REST-Second-API" TechnicalProfileReferenceId="Call-REST-Second-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

Aby naprawić ten typ błędu, należy użyć dwóch kroków aranżacji. Każdy krok aranżacji z jedną wymianą oświadczeń.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="5" Type="ClaimsExchange">
        ...
        <ClaimsExchanges>
          <ClaimsExchange Id="SelfAsserted-Social" TechnicalProfileReferenceId="SelfAsserted-Social"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="6" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-First-API" TechnicalProfileReferenceId="Call-REST-First-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-Second-API" TechnicalProfileReferenceId="Call-REST-Second-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="there-is-a-duplicate-key-sequence-name"></a>Istnieje zduplikowana sekwencja klucza "{name}"

Podróż ma wiele `ClaimsExchange` takich samych `Id` . Poniższe kroki powodują wystąpienie tego typu błędu. Identyfikator *AADUserWrite* występuje dwa razy w podróży użytkownika.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="AAD-UserWriteUsingAlternativeSecurityId"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="8" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="Call-REST-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

Aby naprawić ten typ błędu, należy zmienić jednokrotne czynności aranżacji "oświadczenia wymiany do unikatowej nazwy, takiej jak *call-REST-API*.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="AAD-UserWriteUsingAlternativeSecurityId"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="8" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-API" TechnicalProfileReferenceId="Call-REST-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="makes-a-reference-to-claimtype-with-id-claim-name-but-neither-the-policy-nor-any-of-its-base-policies-contain-such-an-element"></a>... tworzy odwołanie do elementu ClaimType o identyfikatorze "{oświadczenia Name}", ale ani zasady ani żadne zasady podstawowe nie zawierają takich elementów

Ten typ błędu występuje, gdy zasady dokonują odwołania do oświadczenia, które nie jest zadeklarowane w [schemacie oświadczeń](claimsschema.md). Oświadczenia muszą być zdefiniowane w co najmniej jednym z plików zasad. 

Na przykład profil techniczny z *schoolIdem* danych wyjściowych. Jednak *schoolId* oświadczenia wyjściowego nigdy nie jest zadeklarowany w zasadach ani w zasadach nadrzędnych.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="schoolId" />
  ...
</OutputClaims>
```

Aby naprawić ten typ błędu, sprawdź `ClaimTypeReferenceId` , czy wartość jest błędnie wpisana lub nie istnieje w schemacie. Jeśli w zasadach rozszerzeń jest zdefiniowany element Claim, ale jest on również używany w zasadach podstawowych. Upewnij się, że to zdarzenie jest zdefiniowane w zasadach, które są używane, lub w zasadach wyższego poziomu.

Dodanie oświadczenia do schematu oświadczeń rozwiązuje ten typ błędu.

```xml
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="schoolId">
      <DisplayName>School name</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Enter your school name</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks> -->
```

### <a name="makes-a-reference-to-a-claimstransformation-with-id"></a>... tworzy odwołanie do ClaimsTransformation z IDENTYFIKATORem...

Przyczyna tego błędu jest podobna do przyczyny błędu dla żądania. Sprawdź poprzedni błąd.

### <a name="user-is-currently-logged-as-a-user-of-yourtenantonmicrosoftcom-tenant"></a>Użytkownik jest obecnie zalogowany jako użytkownik dzierżawy "yourtenant.onmicrosoft.com"...

Zaloguj się przy użyciu konta z dzierżawy, która jest inna niż zasada, którą próbujesz przekazać. Na przykład po zalogowaniu się za pomocą admin@contoso.onmicrosoft.com usługi, podczas gdy zasady `TenantId` są ustawione na `fabrikam.onmicrosoft.com` .

```xml
<TrustFrameworkPolicy ...
  TenantId="fabrikam.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://fabrikam.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>fabrikam.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

- Sprawdź, czy `TenantId` wartość w `<TrustFrameworkPolicy\>` i `<BasePolicy\>` elementów jest zgodna z docelową dzierżawą Azure AD B2C.

### <a name="claim-type-name-is-the-output-claim-of-the-relying-partys-technical-profile-but-it-is-not-an-output-claim-in-any-of-the-steps-of-user-journey"></a>Typem zgłoszenia "{name}" jest wynikowe zgłoszenie profilu technicznego jednostki uzależnionej, ale nie jest to zgłoszenie wyjściowe w żadnym z etapów podróży użytkownika...

W zasadach jednostki uzależnionej dodano zgłoszenie wyjściowe, ale nie jest to zgłoszenie wyjściowe w żadnym z kroków podróży użytkownika. Azure AD B2C nie może odczytać wartości oświadczenia z zbioru oświadczeń.

W poniższym przykładzie, *schoolId* to wynikowe zgłoszenie profilu technicznego jednostki uzależnionej, ale nie jest to zgłoszenie wyjściowe w żadnym z kroków podróży użytkownika *SignUpOrSignIn* .

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="schoolId" />
      ...
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
``` 

Aby naprawić ten typ błędu, upewnij się, że oświadczenia wyjściowe są wyświetlane w co najmniej jednej kolekcji oświadczeń wyjściowych w profilu technicznym. Jeśli podróż użytkownika nie może wyprowadzić tego żądania, w profilu technicznym jednostki uzależnionej ustaw wartość domyślną, na przykład pusty ciąg.  

```xml
<OutputClaim ClaimTypeReferenceId="schoolId" DefaultValue="" />
```

### <a name="input-string-was-not-in-a-correct-format"></a>Ciąg wejściowy nie ma poprawnego formatu

Określono nieprawidłowy typ wartości dla żądania z innego typu. Na przykład można zdefiniować wierzytelność typu Integer.

```xml
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="age">
      <DisplayName>Age</DisplayName>
      <DataType>int</DataType>
    </ClaimType>
  <!--
  </ClaimsSchema>
</BuildingBlocks> -->
```

Następnie próbujesz ustawić wartość ciągu:

```xml
<OutputClaim ClaimTypeReferenceId="age" DefaultValue="ABCD" />
```

Aby naprawić ten typ błędu, upewnij się, że ustawiono poprawną wartość, na przykład `DefaultValue="0"` .


### <a name="tenant-name-already-has-a-policy-with-id-name-another-policy-with-the-same-id-cannot-be-stored"></a>Dzierżawca "{name}" ma już zasady o identyfikatorze "{name}". Nie można przechowywać innych zasad o takim samym identyfikatorze

Podjęto próbę przekazania zasad do dzierżawy, ale zasady o tej samej nazwie zostały już przekazane do dzierżawy. 

Aby naprawić ten typ błędu, podczas przekazywania zasad zaznacz pole wyboru **Zastąp zasady niestandardowe, jeśli już istnieje** .

![Zrzut ekranu, który pokazuje, jak zastąpić zasady niestandardowe, jeśli już istnieje.](./media/troubleshoot-custom-policies/overwrite-custom-policy-if-exists.png)


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [zbierać dzienniki Azure Active Directory B2C z Application Insights](troubleshoot-with-application-insights.md).

