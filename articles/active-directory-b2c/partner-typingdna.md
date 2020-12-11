---
title: TypingDNA z Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Dowiedz się, jak zintegrować uwierzytelnianie Azure AD B2C z usługą TypingDNA, aby ułatwić weryfikację tożsamości i sprawdzanie poprawności na podstawie wzorca wpisywania przez użytkownika, zapewnia rozwiązania weryfikujące identyfikatory, które wymuszają uwierzytelnianie wieloskładnikowe i pomagają zachować zgodność z wymaganiami SCA dotyczącymi usług płatniczych 2 (PSD2).
author: gargi-sinha
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 68617d86fda940c5d3752f2389088a8c729aebec
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108352"
---
# <a name="tutorial-for-configuring-typingdna-with-azure-active-directory-b2c"></a>Samouczek dotyczący konfigurowania TypingDNA z Azure Active Directory B2C

W tym instruktażu dowiesz się, jak zintegrować przykładową aplikację płatniczą online w Azure Active Directory B2C z aplikacją TypingDNA. Za pomocą Azure AD B2C aplikacji TypingDNA klienci mogą przestrzegać wymagań transakcji dyrektywy 2 (PSD2) dotyczącej [usług płatniczych](https://www.typingdna.com/use-cases/sca-strong-customer-authentication) za pośrednictwem systemu Dynamics naciśnięć klawiszy i silnego uwierzytelniania klientów. Więcej informacji na temat TypingDNA znajdziesz [tutaj](https://www.typingdna.com/).

 Azure AD B2C używa technologii TypingDNA do przechwytywania cech charakterystycznych dla użytkowników i ich rejestrowania i analizowania pod kątem znajomości poszczególnych uwierzytelnień. Spowoduje to dodanie warstwy ochrony związanej z riskiness uwierzytelniania i ocenę poziomów ryzyka. Azure AD B2C może wywoływać inne mechanizmy w celu zapewnienia dalszej pewności, że użytkownik, do którego się odwołuje, wywołuje usługę Azure AD MFA, wymusza weryfikację wiadomości e-mail lub inną logikę niestandardową dla Twojego scenariusza.

>[!NOTE]
> Te przykładowe zasady są oparte na [SocialAndLocalAccountsWithMfa](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) Starter Pack.

## <a name="scenario-description"></a>Opis scenariusza

![Zrzut ekranu przedstawiający diagram architektury TypingDNA](./media/partner-typingdna/typingdna-architecture-diagram.png)

### <a name="sign-up"></a>Utwórz konto

1. Azure AD B2C strony używają biblioteki języka JavaScript TypingDNA do rejestrowania wzorca wpisywania przez użytkownika. Na przykład nazwa użytkownika i hasło są rejestrowane podczas rejestracji wstępnej, a następnie na każdym zalogowaniu w celu weryfikacji.

2. Gdy użytkownik przesyła stronę, biblioteka TypingDNA będzie obliczać cechy charakterystyczne dla użytkownika. Następnie Wstaw informacje do ukrytego pola tekstowego, które Azure AD B2C renderowane. To pole jest ukryte w CSS.  

    [Przykład zawiera pliki HTML](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignUp.cshtml) z modyfikacjami JavaScript i CSS, do których odwołują się `api.selfasserted.tdnasignin` `api.selfasserted.tdnasignup` definicje zawartości. Zapoznaj się z tematem hostowanie [zawartości strony](./customize-ui-with-html.md#hosting-the-page-content) , aby obsługiwać pliki HTML.

3. Azure AD B2C ma teraz wzorzec wpisywania w zbiorze roszczeń, gdy użytkownik przesyła poświadczenia. Musi wywołać interfejs API (z), aby przekazać te dane do punktu końcowego interfejsu API REST TypingDNA. Ten interfejs API jest dołączony do [przykładu (typingDNA-API-Interface)](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface).
4. Interfejs API warstwy środkowej przekazuje następnie wpisywanie danych wzorca do interfejsu API REST TypingDNA. Przy rejestrowaniu jest wywoływany [punkt końcowy użytkownika](https://api.typingdna.com/index.html#api-API_Services-GetUser) , aby potwierdzić, że użytkownik nie istnieje, a następnie jest wywoływany punkt końcowy [wzorca zapisywania](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) , aby zapisać wzorzec pierwszego wpisywania przez użytkownika.

> [!NOTE]
> Wszystkie wywołania punktu końcowego interfejsu API REST usługi TypingDNA powodują wysłanie identyfikatora użytkownika. Musi to być wartość skrótu. Azure AD B2C używa `HashObjectIdWithEmail` transformacji oświadczeń do mieszania wiadomości e-mail z losową wartością soli i wpisu tajnego.  

Wywołania interfejsu API REST są modelowane `validationTechnicalProfiles` w ramach `LocalAccountSignUpWithLogonEmail-TDNA` :

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail-TDNA" />
    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="true"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser"/>

</ValidationTechnicalProfiles>

```

### <a name="sign-in"></a>Logowanie

Podczas kolejnego logowania wzorzec wpisywania użytkownika jest obliczany w taki sam sposób, jak w przypadku rejestracji przy użyciu [niestandardowego kodu HTML](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignIn.cshtml). Gdy wpisany profil znajduje się w zbiorze roszczeń Azure AD B2C, Azure AD B2C wywoła interfejs API, aby wywołać punkt końcowy interfejsu API REST TypingDNA. Punkt końcowy [użytkownika sprawdzania](https://api.typingdna.com/index.html#api-API_Services-GetUser) jest wywoływany, aby potwierdzić, że użytkownik istnieje. Następnie [Sprawdź, czy](https://api.typingdna.com/index.html#api-API_Services-verifyTypingPattern) punkt końcowy wzorca jest wywoływany, aby zwrócić `net_score` . `net_score`Jest to wskazanie, jak zamknąć wzorzec wpisywania podczas tworzenia konta.

Ten wzór pisania jest modelowany `validationTechnicalProfiles` w ramach `SelfAsserted-LocalAccountSignin-Email-TDNA` :

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="login-NonInteractive"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="false"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-VerifyUser"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser">

        <Preconditions>

            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>saveTypingPattern</Value>

            <Value>False</Value>

            <Action>SkipThisValidationTechnicalProfile</Action>

            </Precondition>

        </Preconditions>

    </ValidationTechnicalProfile>

</ValidationTechnicalProfiles>

```

 Jeśli użytkownik uzyskuje wzorzec wpisywania, który ma wysoką `net_score` , można go zapisać przy użyciu punktu końcowego [wzorca wpisywania tekstu](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) TypingDNA.  

Interfejs API musi zwrócić wartość  `saveTypingPattern` , jeśli chcesz, aby TypingDNA zapisu punkt końcowy wzorca wpisywania, który ma zostać wywołany przez Azure AD B2C (za pośrednictwem interfejsu API).

Przykład w repozytorium zawiera interfejs API (TypingDNA-API-Interface), który jest skonfigurowany przy użyciu następujących właściwości.

- Tryb szkolenia — Jeśli użytkownik ma mniej niż dwa zapisane wzorce, zawsze Monituj o uwierzytelnianie wieloskładnikowe.

- Jeśli użytkownik ma zapisane 2-5 wzorców, a `net_score` jest mniejszy niż 50, Monituj o usługę MFA.

- Jeśli użytkownik ma 5 i zapisano wzorce, a wartość `net_score` jest niższa niż 65, Monituj o usługę MFA.

Te progi należy skorygować w przypadku użycia.

- Po przeprowadzeniu przez interfejs API oceny należy `net_score` zwrócić do B2C wartość logiczną `promptMFA` .

- To `promptMFA` zdarzenie jest używane w ramach warunku wstępnego do warunkowego wykonania usługi Azure AD MFA.

```xml

<OrchestrationStep Order="3" Type="ClaimsExchange">

    <Preconditions>

        <Precondition Type="ClaimsExist" ExecuteActionsIf="true">

            <Value>isActiveMFASession</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>promptMFA</Value>

            <Value>False</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

    </Preconditions>

    <ClaimsExchanges>

        <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" />

    </ClaimsExchanges>

</OrchestrationStep>

```

## <a name="onboard-with-typingdna"></a>Dołączanie do TypingDNA

1. Utwórz konto w usłudze TypingDNA [tutaj](https://www.typingdna.com/)
2. Zaloguj się do pulpitu nawigacyjnego usługi TypingDNA i uzyskaj **klucz interfejsu API** i **wpis tajny interfejsu API**. Ta wartość będzie konieczna w instalacji interfejsu API później

## <a name="integrate-typingdna-with-azure-ad-b2c"></a>Integruj TypingDNA z Azure AD B2C

1. Hostowanie [interfejsu TypingDNA-API-Interface](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) w wybranym dostawcy hostingu
2. Zastąp wszystkie wystąpienia `apiKey` i `apiSecret` w rozwiązaniu [TypingDNA-API-Interface](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) przy użyciu poświadczeń z pulpitu nawigacyjnego usługi TypingDNA
3. Hostować pliki HTML w wybranym dostawcy, postępując zgodnie z wymaganiami CORS w [tym miejscu](./customize-ui-with-html.md#3-configure-cors)
4. Zastąp elementy LoadURI dla `api.selfasserted.tdnasignup` definicji i `api.selfasserted.tdnasignin` zawartości w `TrustFrameworkExtensions.xml` pliku odpowiednio identyfikatorem URI hostowanych plików HTML.
5. Utwórz klucz zasad B2C w obszarze struktura środowiska tożsamości w bloku Azure AD w **Azure Portal**. Użyj `Generate` opcji i Nazwij ten klucz `tdnaHashedId` .
6. Zastąp TenantId w plikach zasad
7. Zamień adresy URL we wszystkich profilach technicznych interfejsu API REST TypingDNA (REST-TDNA-VerifyUser, REST-TDNA-SaveUser, REST-TDNA-CheckUser) z punktem końcowym interfejsu API [interfejsu API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface).
8. Przekaż [pliki zasad](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/policy) do dzierżawy.

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Otwórz dzierżawcę B2C i wybierz pozycję platforma obsługi tożsamości.
2. Wybierz wcześniej utworzony **przepływ użytkownika**.
3. Wybierz pozycję **Uruchom** przepływ użytkownika

    a. **Aplikacja** — wybór zarejestrowanej aplikacji (przykład: JWT)

    b. **Adres URL odpowiedzi** — wybierz adres URL przekierowania

    c. Wybierz pozycję **Uruchom przepływ użytkownika**.
  
4. Przejdź przez przepływ rejestracji i Utwórz konto
5. Wyloguj się
6. Przejdź przez przepływ logowania
7. Wynikowe wyniki JWT spowodują wyświetlenie wyników TypingDNA

## <a name="live-version"></a>Wersja na żywo

• Funkcja MFA została wyłączona w tej wersji testowej, ale można sprawdzić, czy po uwierzytelnieniu zostanie wyświetlony monit z żądaniem usługi MFA `promptMFA` .

• Zarejestruj się [tutaj](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SU_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login) i zaloguj się [tutaj](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SI_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje, zapoznaj się z następującymi artykułami:

- [Zasady niestandardowe w AAD B2C](./custom-policy-overview.md)

- [Wprowadzenie do zasad niestandardowych w AAD B2C](./custom-policy-get-started.md?tabs=applications)