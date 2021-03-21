---
title: Konfigurowanie przepływu resetowania hasła w Azure AD B2C
titleSuffix: Azure AD B2C
description: Dowiedz się, jak skonfigurować przepływ resetowania hasła w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8b3224333a3915b7827242004d1dec0f4695f479
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581919"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Konfigurowanie przepływu resetowania hasła w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="password-reset-flow"></a>Przepływ resetowania hasła

Dzięki [temu użytkownicy](add-sign-up-and-sign-in-policy.md) mogą zresetować swoje hasła przy użyciu linku "nie **pamiętasz hasła?** ". Przepływ resetowania hasła obejmuje następujące kroki:

1. Na stronie Rejestracja i logowanie użytkownik klika łącze nie **pamiętasz hasła?** Azure AD B2C inicjuje przepływ resetowania hasła. 
2. Użytkownik udostępnia i weryfikuje swój adres e-mail za pomocą jednorazowego kodu dostępu.
3. Użytkownik może następnie wprowadzić nowe hasło.

![Przepływ resetowania hasła](./media/add-password-reset-policy/password-reset-flow.png)

Przepływ resetowania hasła dotyczy kont lokalnych w Azure AD B2C, które używają [adresu e-mail](identity-provider-local.md#email-sign-in) lub [nazwy użytkownika](identity-provider-local.md#username-sign-in) z hasłem do logowania.

Typowym postępowaniem po migracji użytkowników do Azure AD B2C z losowymi hasłami jest możliwość weryfikowania adresów e-mail przez użytkowników i resetowania ich haseł podczas pierwszego logowania. Często istnieje również możliwość wymuszenia resetowania hasła przez użytkownika po zmianie hasła przez administratora. Aby włączyć tę funkcję, zobacz [Wymuś Resetowanie hasła](force-password-reset.md) .

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="self-service-password-reset-recommended"></a>Samoobsługowe resetowanie hasła (zalecane)

Nowe środowisko resetowania hasła jest teraz częścią zasad rejestracji lub logowania. Gdy użytkownik wybierze link **zapomniane hasło?** , zostanie natychmiast wysłany do niezapomnianego hasła. Aplikacja nie musi już obsługiwać [kodu błędu AADB2C90118](#password-reset-policy-legacy)i nie jest potrzebna oddzielna zasada resetowania hasła.

::: zone pivot="b2c-user-flow"

Środowisko samoobsługowego resetowania hasła można skonfigurować na potrzeby **logowania (zalecane)** lub **rejestrowania i logowania (zalecane)** przepływów użytkowników. Jeśli nie masz takiego przepływu użytkownika, utwórz go [i](add-sign-up-and-sign-in-policy.md) Zarejestruj się. 

Aby włączyć funkcję samoobsługowego resetowania hasła dla przepływu użytkownika dotyczącego rejestracji lub logowania:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **przepływy użytkownika**.
1. Wybierz przepływ użytkownika do rejestracji lub logowania ( **zalecane** typy), który chcesz dostosować.
1. W obszarze **Ustawienia** w menu po lewej stronie wybierz pozycję **Właściwości**.
1. W obszarze **złożoność hasła** wybierz pozycję **Samoobsługowe resetowanie hasła**.
1. Wybierz pozycję **Zapisz**.
1. W obszarze **Dostosuj** w menu po lewej stronie wybierz pozycję **układy strony**.
1. W **wersji układu strony** wybierz polecenie **2.1.2 — bieżące** lub nowsze.
1. Wybierz pozycję **Zapisz**.

::: zone-end

::: zone pivot="b2c-custom-policy"

W poniższych sekcjach opisano, jak dodać środowisko hasła samoobsługowego do zasad niestandardowych. Przykład jest oparty na plikach zasad zawartych w [pakiecie początkowym zasad niestandardowych](./custom-policy-get-started.md). 

> [!TIP]
> Pełny przykład zasad "rejestrowanie lub logowanie przy użyciu resetowania hasła" można znaleźć w witrynie [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/embedded-password-reset).

### <a name="indicate-a-user-selected-the-forgot-your-password-link"></a>Wskazać, że użytkownik zaznaczył zapomniane hasło? powiązań

Aby wskazać zasady, dla których użytkownik zaznaczył **zapomniane hasło?** link, zdefiniuj wartość logiczną. To zastrzeżenie zostanie użyte do skierowania podróży użytkownika do profilu technicznego zapomnianego hasła. To zgłoszenie można także wystawić dla tokenu, aby aplikacja była świadoma, że użytkownik zalogował się za pośrednictwem zapomnianego przepływu haseł.

Oświadczenia deklaruje się w [schemacie oświadczeń](claimsschema.md). Otwórz plik rozszerzeń zasad. Na przykład <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Wyszukaj element [BuildingBlocks](buildingblocks.md) . Jeśli element nie istnieje, Dodaj go.
1. Znajdź element [ClaimsSchema](claimsschema.md) . Jeśli element nie istnieje, Dodaj go.
1. Dodaj następujące zastrzeżenie do elementu **ClaimsSchema** . 

```XML
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="isForgotPassword">
      <DisplayName>isForgotPassword</DisplayName>
      <DataType>boolean</DataType>
      <AdminHelpText>Whether the user has selected Forgot your Password</AdminHelpText>
    </ClaimType>
  <!--
  </ClaimsSchema>
</BuildingBlocks> -->
```

### <a name="upgrade-the-page-layout-version"></a>Uaktualnij wersję układu strony

[Wersja](contentdefinitions.md#migrating-to-page-layout) `2.1.2` układu strony jest wymagany do włączenia samoobsługowego przepływu resetowania hasła w trakcie rejestracji lub logowania.

1. Wyszukaj element [BuildingBlocks](buildingblocks.md) . Jeśli element nie istnieje, Dodaj go.
1. Znajdź element [ContentDefinitions](contentdefinitions.md) . Jeśli element nie istnieje, Dodaj go.
1. Zmodyfikuj element **DataURI** w elemencie **ContentDefinition** z identyfikatorem **API. signuporsignin** , jak pokazano poniżej.

```xml
<!-- 
<BuildingBlocks>
  <ContentDefinitions> -->
    <ContentDefinition Id="api.signuporsignin">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2</DataUri>
    </ContentDefinition>
  <!-- 
  </ContentDefinitions>
</BuildingBlocks> -->
```

Do zainicjowania `isForgotPassword` oświadczenia jest używany profil techniczny transformacji oświadczeń. Ten profil techniczny zostanie przywoływany później. Gdy zostanie wywołane, ustawi wartość tego `isForgotPassword` żądania `true` . Znajdź `ClaimsProviders` element. Jeśli element nie istnieje, Dodaj go. Następnie Dodaj następującego dostawcę oświadczeń:  

```xml
<!-- 
<ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>Local Account</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="ForgotPassword">
        <DisplayName>Forgot your password?</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="true" AlwaysUseDefaultValue="true"/>
        </OutputClaims>
      </TechnicalProfile>
      <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
        <Metadata>
          <Item Key="setting.forgotPasswordLinkOverride">ForgotPasswordExchange</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
<!-- 
</ClaimsProviders> -->
```

`SelfAsserted-LocalAccountSignin-Email`Profil techniczny `setting.forgotPasswordLinkOverride` definiuje, że wymiana oświadczeń resetowania hasła ma być wykonywana w podróży użytkownika. 

### <a name="add-the-password-reset-sub-journey"></a>Dodaj podróż podrzędną resetowania hasła

Twoja podróż obejmuje teraz możliwość logowania się, tworzenia konta i resetowania hasła. Aby lepiej zorganizować podróż użytkownika, można użyć [podpodróży](subjourneys.md) do obsługi przepływu resetowania hasła.

Podpodróż zostanie wywołana z podróży użytkownika i wykona konkretne kroki w celu dostarczenia użytkownikowi środowiska resetowania hasła. Użyj `Call` podjazdu typu, aby po zakończeniu podjazdu kontrola została zwrócona do kroku aranżacji, który zainicjował podróż sub.

Znajdź `SubJourneys` element. Jeśli element nie istnieje, Dodaj go po `User Journeys` elemencie. Następnie Dodaj następującą podróż podrzędną:

```xml
<!--
<SubJourneys>-->
  <SubJourney Id="PasswordReset" Type="Call">
    <OrchestrationSteps>
      <!-- Validate user's email address. -->
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="PasswordResetUsingEmailAddressExchange" TechnicalProfileReferenceId="LocalAccountDiscoveryUsingEmailAddress" />
        </ClaimsExchanges>
      </OrchestrationStep>

      <!-- Collect and persist a new password. -->
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
<!--
</SubJourneys>-->
```

### <a name="prepare-your-user-journey"></a>Przygotowywanie podróży użytkownika

Musisz połączyć **zapomniane hasło?** link do podjazdu zapomnianego hasła. W tym celu należy odwołać się do zapomnianego identyfikatora podjazdu hasła w elemencie **ClaimsProviderSelection** kroku **CombinedSignInAndSignUp** .

Jeśli nie masz własnej niestandardowej podróży użytkownika z krokiem **CombinedSignInAndSignUp** , użyj poniższej procedury, aby zduplikować istniejącą podróż lub zalogowanie użytkownika. W przeciwnym razie przejdź do następnej sekcji.

1. Otwórz plik *TrustFrameworkBase.xml* z pakietu początkowego.
2. Znajdź i Skopiuj całą zawartość elementu **UserJourney** , który zawiera `Id="SignUpOrSignIn"` .
3. Otwórz *TrustFrameworkExtensions.xml* i Znajdź element **UserJourneys** . Jeśli element nie istnieje, Dodaj go.
4. Utwórz element podrzędny elementu **UserJourneys** , wklejając całą zawartość elementu **UserJourney** skopiowanego w kroku 2.
5. Zmień nazwę identyfikatora podróży użytkownika. Na przykład `Id="CustomSignUpSignIn"`.

### <a name="connect-the-forgot-password-link-to-the-forgot-password-sub-journey"></a>Połącz link zapomnianego hasła z podjazdem zapomnianego hasła 

W trakcie podróży użytkownika można reprezentować niezapomniane podjazdę hasła jako **ClaimsProviderSelection**. Dodanie tego elementu łączy **zapomniane hasło?** link do podjazdu zapomnianego hasła.

1. W trakcie podróży użytkownika Znajdź element kroku aranżacji obejmujący `Type="CombinedSignInAndSignUp"` lub `Type="ClaimsProviderSelection"` . Zwykle jest to pierwszy krok aranżacji. Element **ClaimsProviderSelections** zawiera listę dostawców tożsamości, których użytkownik może używać do logowania. Dodaj następujący wiersz:
    
    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ForgotPasswordExchange" />
    ```

1. W następnym kroku aranżacji Dodaj element **ClaimsExchange** . Dodaj następujący wiersz:

    ```xml
    <ClaimsExchange Id="ForgotPasswordExchange" TechnicalProfileReferenceId="ForgotPassword" />
    ```
    
1. Dodaj następujący krok aranżacji między bieżącym krokiem i następnym krokiem. Nowy krok aranżacji, który zostanie dodany, sprawdza, czy `isForgotPassword` istnieje takie stwierdzenie. Jeśli takie stwierdzenie istnieje, wywołuje [podjazdę resetowania hasła](#add-the-password-reset-sub-journey). 

    ```xml
    <OrchestrationStep Order="3" Type="InvokeSubJourney">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>isForgotPassword</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <JourneyList>
        <Candidate SubJourneyReferenceId="PasswordReset" />
      </JourneyList>
    </OrchestrationStep>
    ```
    
1. Po dodaniu nowego kroku aranżacji ponownie przystąpi kolejne kroki bez pomijania wartości całkowitych z zakresu od 1 do N.

### <a name="set-the-user-journey-to-be-executed"></a>Ustawianie przebiegu użytkownika do wykonania

Po zmodyfikowaniu lub utworzeniu podróży użytkownika w sekcji jednostka **uzależniona** Określ, która Azure AD B2C będzie wykonywana dla tych zasad niestandardowych. W elemencie [RelyingParty](relyingparty.md) Znajdź element **DefaultUserJourney** . Zaktualizuj  **DefaultUserJourney ReferenceId** w taki sposób, aby odpowiadał identyfikatorowi podróży użytkownika, w którym dodano **ClaimsProviderSelections**.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="indicate-the-forgot-password-flow-to-your-app"></a>Wskaż niezapomniany przepływ hasła do aplikacji

Aplikacja może potrzebować wykryć, czy użytkownik jest zalogowany za pośrednictwem przepływu użytkownika zapomnianego hasła. **IsForgotPassworda** zawiera wartość logiczną, która wskazuje, że może być wystawiony w tokenie wysyłanym do aplikacji. W razie potrzeby Dodaj `isForgotPassword` do oświadczeń wyjściowych w sekcji jednostki **uzależnionej** . Aplikacja może sprawdzić, `isForgotPassword` czy w celu ustalenia, czy użytkownik resetuje swoje hasło.

```xml
<RelyingParty>
  <OutputClaims>
    ...
    <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="false" />
  </OutputClaims>
</RelyingParty>
```


### <a name="upload-the-custom-policy"></a>Przekazywanie zasad niestandardowych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. W obszarze **zasady** wybierz pozycję **platforma obsługi tożsamości**.
1. Wybierz pozycję **Przekaż zasady niestandardowe**, a następnie Przekaż dwa pliki zasad, które zostały zmienione w następującej kolejności:
   1. Zasady rozszerzenia, na przykład `TrustFrameworkExtensions.xml` .
   2. Na przykład zasady jednostki uzależnionej `SignUpSignIn.xml` .

::: zone-end

### <a name="test-the-password-reset-flow"></a>Testowanie przepływu resetowania hasła

1. Wybierz przepływ użytkowników do rejestracji lub logowania (zalecane typy), które chcesz przetestować.
1. Wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web o nazwie *webapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Wybierz pozycję **Uruchom przepływ użytkownika**.
1. Na stronie rejestracji lub logowania wybierz pozycję nie **pamiętasz hasła?**.
1. Sprawdź, czy adres e-mail konta, które zostało utworzone wcześniej, a następnie wybierz pozycję **Kontynuuj**.
1. Masz teraz możliwość zmiany hasła dla użytkownika. Zmień hasło i wybierz pozycję **Kontynuuj**. Token jest zwracany do `https://jwt.ms` i powinien być wyświetlony.
1. Sprawdź wartość żądania tokenu zwrotnego `isForgotPassword` . Jeśli istnieje i jest ustawiona na true (prawda), oznacza to, że użytkownik zresetował hasło.

## <a name="password-reset-policy-legacy"></a>Zasady resetowania hasła (starsza wersja)

Jeśli środowisko samoobsługowego [resetowania hasła](#self-service-password-reset-recommended) nie jest włączone, kliknięcie tego linku nie spowoduje automatycznego wyzwolenia przepływu użytkownika resetowania hasła. Zamiast tego kod błędu `AADB2C90118` jest zwracany do aplikacji. Aplikacja musi obsłużyć ten kod błędu przez ponowne zainicjowanie biblioteki uwierzytelniania w celu uwierzytelnienia przepływu użytkownika resetowania hasła Azure AD B2C.

Na poniższym diagramie:

1. W aplikacji użytkownik klika przycisk Zaloguj. Aplikacja inicjuje żądanie autoryzacji i pobiera użytkownika, aby Azure AD B2C zakończyć logowanie. Żądanie autoryzacji określa nazwę zasady rejestracji lub logowania, taką jak **B2C_1_signup_signin**.
1. Użytkownik wybierze link "nie **pamiętasz hasła?** ". Azure AD B2C zwraca do aplikacji kod błędu AADB2C90118.
1. Aplikacja obsługuje kod błędu i Inicjuje nowe żądanie autoryzacji. Żądanie autoryzacji określa nazwę zasad resetowania hasła, na przykład **B2C_1_pwd_reset**.

![Przepływ użytkownika resetowania starszego hasła](./media/add-password-reset-policy/password-reset-flow-legacy.png)

Aby zobaczyć przykład, zapoznaj się z [prostym](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)przykładem ASP.NET, który pokazuje łączenie przepływów użytkowników.

::: zone pivot="b2c-user-flow"

### <a name="create-a-password-reset-user-flow"></a>Tworzenie przepływu użytkownika dotyczącego resetowania haseł

Aby umożliwić użytkownikom aplikacji Resetowanie hasła, należy utworzyć przepływ użytkownika resetowania hasła.

1. W menu przegląd Azure AD B2C dzierżawy wybierz pozycję **przepływy użytkowników**, a następnie wybierz pozycję **Nowy przepływ użytkownika**.
1. Na stronie **Tworzenie przepływu użytkownika** wybierz przepływ użytkownika **resetowania hasła** . 
1. W obszarze **Wybierz wersję** wybierz pozycję **zalecane**, a następnie wybierz pozycję **Utwórz**.
1. Wprowadź **nazwę** przepływu użytkownika. Na przykład *passwordreset1*.
1. W przypadku **dostawców tożsamości** Włącz **Resetowanie hasła przy użyciu adresu e-mail**.
1. W obszarze **oświadczenia aplikacji** wybierz pozycję **Pokaż więcej** i wybierz oświadczenia, które mają być zwracane w tokenach autoryzacji wysyłanych z powrotem do aplikacji. Na przykład wybierz pozycję **Identyfikator obiektu użytkownika**.
1. Wybierz przycisk **OK**.
1. Wybierz pozycję **Utwórz** , aby dodać przepływ użytkownika. Prefiks *B2C_1* jest automatycznie dołączany do nazwy.

### <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Wybierz utworzony przepływ użytkownika, aby otworzyć jego stronę przeglądu, a następnie wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web o nazwie *webapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Kliknij pozycję **Uruchom przepływ użytkownika**, Sprawdź adres e-mail konta, które zostało utworzone wcześniej, a następnie wybierz pozycję **Kontynuuj**.
1. Teraz można zmienić hasło użytkownika. Zmień hasło i wybierz pozycję **Kontynuuj**. Token jest zwracany do `https://jwt.ms` i powinien być wyświetlony.

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="create-a-password-reset-policy"></a>Tworzenie zasad resetowania haseł

Zasady niestandardowe są zestawem plików XML przekazywanym do dzierżawy Azure AD B2C w celu zdefiniowania podróży użytkowników. Udostępniamy pakiety początkowe z kilkoma wstępnie utworzonymi zasadami, takimi jak: rejestrowanie i logowanie, resetowanie haseł i zasady edytowania profilu. Aby uzyskać więcej informacji, zobacz Wprowadzenie [do zasad niestandardowych w Azure AD B2C](custom-policy-get-started.md).

::: zone-end

## <a name="next-steps"></a>Następne kroki

Skonfiguruj [wymuszanie resetowania hasła](force-password-reset.md).
