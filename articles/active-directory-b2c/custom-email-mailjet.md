---
title: Niestandardowa Weryfikacja poczty e-mail za pomocą Mailjet
titleSuffix: Azure AD B2C
description: Dowiedz się, jak zintegrować się z usługą Mailjet w celu dostosowania wiadomości e-mail do weryfikacji wysyłanej do klientów po zarejestrowaniu się w celu korzystania z aplikacji obsługujących Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/15/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 48e823b19c1c6d30e73a7a673cbeab82a4d007a9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489223"
---
# <a name="custom-email-verification-with-mailjet"></a>Niestandardowa Weryfikacja poczty e-mail za pomocą Mailjet

Użyj niestandardowych wiadomości e-mail w programie Azure Active Directory B2C (Azure AD B2C), aby wysyłać niestandardowe wiadomości e-mail do użytkowników, którzy logują się w celu korzystania z aplikacji. Korzystając z usługi [DisplayControls](display-controls.md) (obecnie w wersji zapoznawczej) i dostawcy poczty e-mail innej firmy Mailjet, można użyć własnego szablonu wiadomości e-mail oraz adresu *:* adres i temat, a także do obsługi lokalizacji i niestandardowych ustawień hasła jednorazowego (OTP).

Niestandardowa Weryfikacja poczty e-mail wymaga użycia dostawcy poczty e-mail innej firmy, takiego jak [Mailjet](https://Mailjet.com), [SendGrid](./custom-email-sendgrid.md)lub [SPARKPOST](https://sparkpost.com), niestandardowy interfejs API REST lub dowolny dostawca poczty e-mail oparty na protokole HTTP (w tym własny). W tym artykule opisano konfigurowanie rozwiązania korzystającego z Mailjet.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-mailjet-account"></a>Utwórz konto Mailjet

Jeśli jeszcze tego nie masz, Zacznij od skonfigurowania konta Mailjet (klienci platformy Azure mogą odblokować 6 000 wiadomości e-mail z limitem 200 wiadomości e-mail dziennie). 

1. Postępuj zgodnie z instrukcjami instalacji na stronie [Tworzenie konta Mailjet](https://www.mailjet.com/guides/azure-mailjet-developer-resource-user-guide/enabling-mailjet/).
1. Aby można było wysyłać wiadomości e-mail, [rejestrować i sprawdzać poprawność](https://www.mailjet.com/guides/azure-mailjet-developer-resource-user-guide/enabling-mailjet/#how-to-configure-mailjet-for-use) adresu e-mail lub domeny nadawcy.
2. Przejdź do [strony Zarządzanie kluczami interfejsu API](https://app.mailjet.com/account/api_keys). Zapisz **klucz interfejsu API** i **klucz tajny** do użycia w późniejszym kroku. Oba klucze są generowane automatycznie podczas tworzenia konta.  

## <a name="create-azure-ad-b2c-policy-key"></a>Utwórz klucz zasad Azure AD B2C

Następnie Zapisz klucz interfejsu API Mailjet w kluczu zasad Azure AD B2C, aby uzyskać informacje dotyczące zasad.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Upewnij się, że używasz katalogu zawierającego dzierżawcę Azure AD B2C. W górnym menu wybierz pozycję **katalog i subskrypcja** , a następnie wybierz katalog Azure AD B2C.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Na stronie **Przegląd** wybierz pozycję **Struktura środowiska tożsamości**.
1. Wybierz pozycję **klucze zasad**, a następnie wybierz pozycję **Dodaj**.
1. W obszarze **Opcje** wybierz pozycję **Ręczne**.
1. Wprowadź **nazwę** klucza zasad. Na przykład `MailjetApiKey`. Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
1. W **kluczu tajnym** wprowadź wcześniej zarejestrowany **klucz interfejsu API** Mailjet.
1. W obszarze **użycie klucza** wybierz pozycję **podpis**.
1. Wybierz przycisk **Utwórz**.
1. Wybierz pozycję **klucze zasad** , a następnie wybierz pozycję **Dodaj**.
1. W obszarze **Opcje** wybierz pozycję **Ręczne**.
1. Wprowadź **nazwę** klucza zasad. Na przykład `MailjetSecretKey`. Prefiks `B2C_1A_` jest automatycznie dodawany do nazwy klucza.
1. W **kluczu tajnym** wprowadź wcześniej zarejestrowany **klucz tajny** Mailjet.
1. W obszarze **użycie klucza** wybierz pozycję **podpis**.
1. Wybierz przycisk **Utwórz**.

## <a name="create-a-mailjet-template"></a>Tworzenie szablonu Mailjet

Po utworzeniu konta Mailjet i kluczu interfejsu API Mailjet przechowywanego w kluczu zasad Azure AD B2C Utwórz [dynamiczny szablon transakcyjny](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)Mailjet.

1. W witrynie Mailjet Otwórz stronę [Szablony transakcyjne](https://app.mailjet.com/templates/transactional) i wybierz pozycję **Utwórz nowy szablon**.
1. Wybierz **przez kodowanie w formacie HTML**, a następnie wybierz pozycję **kod od podstaw**.
1. Wprowadź unikatową nazwę szablonu `Verification email` , na przykład, a następnie wybierz pozycję **Utwórz**.
1. W edytorze HTML wklej następujący szablon HTML lub użyj własnych. `{{var:otp:""}}`Parametry i `{{var:email:""}}` zostaną zamienione dynamicznie z wartością hasła jednorazowego i adresem e-mail użytkownika.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
       <!-- Template B O365 -->
       <style>
           table td {border-collapse:collapse;margin:0;padding:0;}
       </style>
    </head>
    <body dir="ltr" lang="en">
       <table width="100%" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
            <tr>
               <td valign="top" width="50%"></td>
               <td valign="top">
                  <!-- Email Header -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en" style="border-left:1px solid #e3e3e3;border-right: 1px solid #e3e3e3;">
                   <tr style="background-color: #0072C6;">
                       <td width="1" style="background:#0072C6; border-top:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-top:1px solid #e3e3e3;border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="310" valign="middle" style="border-top:1px solid #e3e3e3; border-bottom:1px solid #e3e3e3;padding:12px 0;">
                           <h1 style="line-height:20pt;font-family:Segoe UI Light; font-size:18pt; color:#ffffff; font-weight:normal;">
                            <span id="HeaderPlaceholder_UserVerificationEmailHeader"><font color="#FFFFFF">Verify your email address</font></span>
                           </h1>
                       </td>
                       <td width="24" style="border-top: 1px solid #e3e3e3;border-bottom: 1px solid #e3e3e3;">&nbsp;</td>
                   </tr>
                  </table>
                  <!-- Email Content -->
                  <table width="640" cellpadding="0" cellspacing="0" border="0" dir="ltr" lang="en">
                   <tr>
                       <td width="1" style="background:#e3e3e3;"></td>
                       <td width="24">&nbsp;</td>
                       <td id="PageBody" width="640" valign="top" colspan="2" style="border-bottom:1px solid #e3e3e3;padding:10px 0 20px;border-bottom-style:hidden;">
                           <table cellpadding="0" cellspacing="0" border="0">
                               <tr>
                                   <td width="630" style="font-size:10pt; line-height:13pt; color:#000;">
                                       <table cellpadding="0" cellspacing="0" border="0" width="100%" style="" dir="ltr" lang="en">
                                           <tr>
                                               <td>

       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{var:email:""}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{var:otp:""}}</span>
       </div>
       <br>
       <br>

                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333;">
                                                   Sincerely,
                                                   </div>
                                                   <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; font-style:italic; color:#333;">
                                                       Contoso
                                                   </div>
                                               </td>
                                           </tr>
                                       </table>
                                   </td>
                               </tr>
                           </table>

                       </td>

                       <td width="1">&nbsp;</td>
                       <td width="1"></td>
                       <td width="1">&nbsp;</td>
                       <td width="1" valign="top"></td>
                       <td width="29">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3;"></td>
                   </tr>
                   <tr>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                       <td width="24" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td id="PageFooterContainer" width="585" valign="top" colspan="6" style="border-bottom:1px solid #e3e3e3;padding:0px;">


                       </td>

                       <td width="29" style="border-bottom:1px solid #e3e3e3;">&nbsp;</td>
                       <td width="1" style="background:#e3e3e3; border-bottom:1px solid #e3e3e3;"></td>
                   </tr>
                  </table>

               </td>
               <td valign="top" width="50%"></td>
           </tr>
       </table>
    <img src="https://mucp.api.account.microsoft.com/m/v2/v?d=AIAACWEPFYXYIUTJIJVV4ST7XLBHVI5MLLYBKJAVXHBDTBHUM5VBSVVPTTVRWDFIXJ5JQTHYOH5TUYIPO4ZAFRFK52UAMIS3UNIPPI7ZJNDZPRXD5VEJBN4H6RO3SPTBS6AJEEAJOUYL4APQX5RJUJOWGPKUABY&amp;i=AIAACL23GD2PFRFEY5YVM2XQLM5YYWMHFDZOCDXUI2B4LM7ETZQO473CVF22PT6WPGR5IIE6TCS6VGEKO5OZIONJWCDMRKWQQVNP5VBYAINF3S7STKYOVDJ4JF2XEW4QQVNHMAPQNHFV3KMR3V3BA4I36B6BO7L4VQUHQOI64EOWPLMG5RB3SIMEDEHPILXTF73ZYD3JT6MYOLAZJG7PJJCAXCZCQOEFVH5VCW2KBQOKRYISWQLRWAT7IINZ3EFGQI2CY2EMK3FQOXM7UI3R7CZ6D73IKDI" width="1" height="1"></body>
    </html>
    ```

1. Rozwiń pozycję **Edytuj temat** w lewym górnym rogu
    1. W polu **temat** wprowadź wartość domyślną podmiotu. Mailjet używa tej wartości, gdy interfejs API nie zawiera parametru podmiotu.
    1. W polu **Nazwa** wpisz nazwę firmy.
    1. W polu **adres** wybierz swój adres e-mail
    1. Wybierz pozycję **Zapisz**.
1. W prawym górnym rogu wybierz pozycję **zapisz & Opublikuj**, a następnie pozycję **tak, Publikuj zmiany**
1. Zapisz **Identyfikator szablonu** utworzonego do użycia w późniejszym kroku. Ten identyfikator należy określić podczas [dodawania transformacji oświadczeń](#add-the-claims-transformation).


## <a name="add-azure-ad-b2c-claim-types"></a>Dodawanie Azure AD B2C typów roszczeń

W zasadach Dodaj następujące typy roszczeń do `<ClaimsSchema>` elementu w elemencie `<BuildingBlocks>` .

Te typy oświadczeń są niezbędne do generowania i weryfikowania adresu e-mail przy użyciu kodu hasła jednorazowego (OTP).

```XML
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="Otp">
      <DisplayName>Secondary One-time password</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="emailRequestBody">
      <DisplayName>Mailjet request body</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="VerificationCode">
      <DisplayName>Secondary Verification Code</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Enter your email verification code</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks> -->
```

## <a name="add-the-claims-transformation"></a>Dodawanie transformacji oświadczeń

Następnie konieczne jest przekształcenie oświadczeń, aby uzyskać dane wyjściowe oświadczenia ciągu JSON, które będzie treścią żądania wysłanego do Mailjet.

Struktura obiektu JSON jest definiowana przez identyfikatory w notacji kropkowej obiektu InputParameters i TransformationClaimTypes InputClaims. Liczby w zapisie kropkowym oznaczają tablice. Wartości pochodzą z wartości InputClaims i właściwości InputParameters "". Aby uzyskać więcej informacji na temat transformacji oświadczeń JSON, zobacz [przekształcenia oświadczeń JSON](json-transformations.md).

Dodaj następującą transformację oświadczeń do `<ClaimsTransformations>` elementu w `<BuildingBlocks>` . Wprowadź następujące aktualizacje do przekształcenia XML oświadczeń:

* Zaktualizuj `Messages.0.TemplateID` wartość InputParameter z identyfikatorem szablonu transakcyjnego Mailjet utworzonego wcześniej w temacie [Tworzenie szablonu Mailjet](#create-a-mailjet-template).
* Zaktualizuj `Messages.0.From.Email` wartość adresu. Użyj prawidłowego adresu e-mail, aby pomóc w zapobieganiu oznaczania wiadomości e-mail weryfikacyjnej jako spamu.
* Zaktualizuj wartość `Messages.0.Subject` parametru wejściowego wiersza tematu z wierszem tematu odpowiednim dla Twojej organizacji.

```XML
<!-- 
<BuildingBlocks>
  <ClaimsTransformations> -->
    <ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.To.0.Email" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="Messages.0.Variables.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.Variables.email" />
      </InputClaims>
      <InputParameters>
        <!-- Update the template_id value with the ID of your Mailjet template. -->
        <InputParameter Id="Messages.0.TemplateID" DataType="int" Value="1234567"/>
        <InputParameter Id="Messages.0.TemplateLanguage" DataType="boolean" Value="true"/>

        <!-- Update with an email appropriate for your organization. -->
        <InputParameter Id="Messages.0.From.Email" DataType="string" Value="my_email@mydomain.com"/>

        <!-- Update with a subject line appropriate for your organization. -->
        <InputParameter Id="Messages.0.Subject" DataType="string" Value="Contoso account email verification code"/>
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
      </OutputClaims>
    </ClaimsTransformation>
  <!--
  </ClaimsTransformations>
</BuildingBlocks> -->
```

## <a name="add-datauri-content-definition"></a>Dodaj definicję zawartości DataUri

Poniżej przekształceń oświadczeń w programie `<BuildingBlocks>` Dodaj następujący [ContentDefinition](contentdefinitions.md) , aby odwołać się do identyfikatora URI danych w wersji 2.1.2:

```XML
<!--
<BuildingBlocks> -->
  <ContentDefinitions>
   <ContentDefinition Id="api.localaccountsignup">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
    <ContentDefinition Id="api.localaccountpasswordreset">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
    </ContentDefinition>
  </ContentDefinitions>
<!--
</BuildingBlocks> -->
```

## <a name="create-a-displaycontrol"></a>Tworzenie elementu DisplayControl

Kontrolka wyświetlania weryfikacji służy do weryfikowania adresu e-mail z kodem weryfikacyjnym, który jest wysyłany do użytkownika.

Ten przykładowy formant wyświetlania jest skonfigurowany do:

1. Zbierz `email` Typ zgłoszenia adresu od użytkownika.
1. Poczekaj, aż użytkownik poda `verificationCode` Typ zgłoszenia przy użyciu kodu wysłanego do użytkownika.
1. Zwróć `email` do samodzielnego profilu technicznego, który ma odwołanie do tej kontrolki wyświetlania.
1. Za pomocą `SendCode` akcji Wygeneruj kod OTP i Wyślij wiadomość e-mail z kodem OTP do użytkownika.

   ![Wyślij wiadomość e-mail z kodem weryfikacyjnym](media/custom-email-mailjet/display-control-verification-email-action-01.png)

W obszarze definicje zawartości nadal w ramach programu `<BuildingBlocks>` Dodaj do zasad następujący [formant DisplayControl](display-controls.md) typu [VerificationControl](display-control-verification.md) .

```XML
<!--
<BuildingBlocks> -->
  <DisplayControls>
    <DisplayControl Id="emailVerificationControl" UserInterfaceControlType="VerificationControl">
      <DisplayClaims>
        <DisplayClaim ClaimTypeReferenceId="email" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="verificationCode" ControlClaimType="VerificationCode" Required="true" />
      </DisplayClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" />
      </OutputClaims>
      <Actions>
        <Action Id="SendCode">
          <ValidationClaimsExchange>
            <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="GenerateOtp" />
            <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="SendOtp" />
          </ValidationClaimsExchange>
        </Action>
        <Action Id="VerifyCode">
          <ValidationClaimsExchange>
            <ValidationClaimsExchangeTechnicalProfile TechnicalProfileReferenceId="VerifyOtp" />
          </ValidationClaimsExchange>
        </Action>
      </Actions>
    </DisplayControl>
  </DisplayControls>
<!--
</BuildingBlocks> -->
```

## <a name="add-otp-technical-profiles"></a>Dodaj profile techniczne OTP

`GenerateOtp`Profil techniczny generuje kod dla adresu e-mail. `VerifyOtp`Profil techniczny weryfikuje kod skojarzony z adresem e-mail. Można zmienić konfigurację formatu i czas wygaśnięcia hasła jednorazowego. Więcej informacji o profilach technicznych OTP znajduje się w temacie [Definiowanie profilu technicznego hasła jednorazowego](one-time-password-technical-profile.md).

Dodaj następujące profile techniczne do `<ClaimsProviders>` elementu.

```XML
<!--
<ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>One time password technical profiles</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="GenerateOtp">
        <DisplayName>Generate one time password</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
          <Item Key="Operation">GenerateCode</Item>
          <Item Key="CodeExpirationInSeconds">1200</Item>
          <Item Key="CodeLength">6</Item>
          <Item Key="CharacterSet">0-9</Item>
          <Item Key="ReuseSameCode">true</Item>
          <Item Key="NumRetryAttempts">5</Item>
        </Metadata>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
        </InputClaims>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="otp" PartnerClaimType="otpGenerated" />
        </OutputClaims>
      </TechnicalProfile>

      <TechnicalProfile Id="VerifyOtp">
        <DisplayName>Verify one time password</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.OneTimePasswordProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
        <Metadata>
          <Item Key="Operation">VerifyCode</Item>
        </Metadata>
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="identifier" />
          <InputClaim ClaimTypeReferenceId="verificationCode" PartnerClaimType="otpToVerify" />
        </InputClaims>
      </TechnicalProfile>
     </TechnicalProfiles>
  </ClaimsProvider>
<!--
</ClaimsProviders> -->
```

## <a name="add-a-rest-api-technical-profile"></a>Dodawanie profilu technicznego interfejsu API REST

Profil techniczny interfejsu API REST generuje zawartość wiadomości e-mail (przy użyciu formatu Mailjet). Więcej informacji o profilach technicznych RESTful znajduje się w temacie [Definiowanie profilu technicznego RESTful](restful-technical-profile.md).

Podobnie jak w przypadku profilów technicznych OTP, do elementu należy dodać następujące profile techniczne `<ClaimsProviders>` .

```XML
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="sendOtp">
      <DisplayName>Use email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.mailjet.com/v3.1/send</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">emailRequestBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_MailjetApiKey" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_MailjetSecretKey" />
      </CryptographicKeys>
      <InputClaimsTransformations>
        <InputClaimsTransformation ReferenceId="GenerateEmailRequestBody" />
      </InputClaimsTransformations>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="emailRequestBody" />
      </InputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="make-a-reference-to-the-displaycontrol"></a>Utwórz odwołanie do elementu DisplayControl

W ostatnim kroku Dodaj odwołanie do utworzonego elementu DisplayControl. Zastąp istniejące `LocalAccountSignUpWithLogonEmail` i `LocalAccountDiscoveryUsingEmailAddress` samodzielne profile techniczne z następującymi tematami. Jeśli użyto wcześniejszej wersji zasad Azure AD B2C. Te profile techniczne są używane `DisplayClaims` wraz z odwołaniem do elementu DisplayControl..

Aby uzyskać więcej informacji, Zobacz Profil techniczny i [formant DisplayControl](display-controls.md)z [własnym potwierdzeniem](restful-technical-profile.md) .

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <Metadata>
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
        <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
        <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
      </DisplayClaims>
    </TechnicalProfile>
    <TechnicalProfile Id="LocalAccountDiscoveryUsingEmailAddress">
      <Metadata>
        <!--OTP validation error messages-->
        <Item Key="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</Item>
        <Item Key="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</Item>
        <Item Key="UserMessageIfInvalidCode">You have entered the wrong code.</Item>
        <Item Key="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</Item>
      </Metadata>
      <DisplayClaims>
        <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
      </DisplayClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="optional-localize-your-email"></a>Obowiązkowe Lokalizowanie poczty e-mail

Aby zlokalizować tę wiadomość e-mail, musisz wysłać zlokalizowane ciągi do Mailjet lub dostawcę poczty e-mail. Można na przykład zlokalizować temat wiadomości e-mail, treść, swój kod lub podpis wiadomości e-mail. W tym celu można użyć transformacji oświadczeń [GetLocalizedStringsTransformation](string-transformations.md) do kopiowania zlokalizowanych ciągów do typów oświadczeń. `GenerateEmailRequestBody`Transformacja oświadczeń, która generuje ładunek JSON, używa oświadczeń wejściowych, które zawierają zlokalizowane ciągi.

1. W zasadach Zdefiniuj następujące oświadczenia ciągu: subject, Message, codeIntro i Signature.
1. Zdefiniuj transformację oświadczeń [GetLocalizedStringsTransformation](string-transformations.md) , aby zastąpić zlokalizowane wartości ciągu do oświadczeń z kroku 1.
1. Zmień `GenerateEmailRequestBody` transformację oświadczeń, tak aby korzystała z oświadczeń wejściowych z poniższym fragmentem kodu XML.
1. Zaktualizuj szablon Mailjet, tak aby korzystał z parametrów dynamicznych zamiast wszystkich ciągów, które będą zlokalizowane przez Azure AD B2C.

    ```XML
    <ClaimsTransformation Id="GetLocalizedStringsForEmail" TransformationMethod="GetLocalizedStringsTransformation">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="subject" TransformationClaimType="email_subject" />
        <OutputClaim ClaimTypeReferenceId="message" TransformationClaimType="email_message" />
        <OutputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="email_code" />
        <OutputClaim ClaimTypeReferenceId="signature" TransformationClaimType="email_signature" />
      </OutputClaims>
    </ClaimsTransformation>
    <ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.To.0.Email" />
        <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="Messages.0.Subject" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="Messages.0.Variables.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="Messages.0.Variables.email" />
        <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="Messages.0.Variables.otpmessage" />
        <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="Messages.0.Variables.otpcodeIntro" />
        <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="Messages.0.Variables.otpsignature" />
      </InputClaims>
      <InputParameters>
        <!-- Update the template_id value with the ID of your Mailjet template. -->
        <InputParameter Id="Messages.0.TemplateID" DataType="int" Value="1234567"/>
        <InputParameter Id="Messages.0.TemplateLanguage" DataType="boolean" Value="true"/>

        <!-- Update with an email appropriate for your organization. -->
        <InputParameter Id="Messages.0.From.Email" DataType="string" Value="my_email@mydomain.com"/>
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
      </OutputClaims>
    </ClaimsTransformation>
    ```

1. Dodaj następujący element [lokalizacji](localization.md) .

    ```xml
    <!--
    <BuildingBlocks> -->
      <Localization Enabled="true">
        <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
          <SupportedLanguage>en</SupportedLanguage>
          <SupportedLanguage>es</SupportedLanguage>
        </SupportedLanguages>
        <LocalizedResources Id="api.custom-email.en">
          <LocalizedStrings>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for validating the account</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
          </LocalizedStrings>
          </LocalizedStrings>
        </LocalizedResources>
        <LocalizedResources Id="api.custom-email.es">
          <LocalizedStrings>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Código de verificación del correo electrónico de la cuenta de Contoso</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Gracias por comprobar la cuenta de </LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Su código es</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sinceramente</LocalizedString>
          </LocalizedStrings>
        </LocalizedResources>
      </Localization>
    <!--
    </BuildingBlocks> -->
    ```

1. Dodaj odwołania do elementów LocalizedResources przez zaktualizowanie elementu [ContentDefinitions](contentdefinitions.md) .

    ```xml
    <!--
    <BuildingBlocks> -->
      <ContentDefinitions>
        <ContentDefinition Id="api.localaccountsignup">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
          <LocalizedResourcesReferences MergeBehavior="Prepend">
            <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
            <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
          </LocalizedResourcesReferences>
        </ContentDefinition>
        <ContentDefinition Id="api.localaccountpasswordreset">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.2</DataUri>
          <LocalizedResourcesReferences MergeBehavior="Prepend">
            <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.custom-email.en" />
            <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.custom-email.es" />
          </LocalizedResourcesReferences>
        </ContentDefinition>
      </ContentDefinitions>
    <!--
    </BuildingBlocks> -->
    ```

1. Na koniec Dodaj następujące przekształcenia oświadczeń wejściowych do `LocalAccountSignUpWithLogonEmail` `LocalAccountDiscoveryUsingEmailAddress` profilów technicznych i.

    ```xml
    <InputClaimsTransformations>
      <InputClaimsTransformation ReferenceId="GetLocalizedStringsForEmail" />
    </InputClaimsTransformations>
    ```
    
## <a name="optional-localize-the-ui"></a>Obowiązkowe Lokalizowanie interfejsu użytkownika

Element lokalizacja umożliwia obsługę wielu ustawień regionalnych lub języków w zasadach dla podróży użytkownika. Obsługa lokalizacji w ramach zasad umożliwia dostarczenie ciągów specyficznych dla języka dla obu [elementów interfejsu użytkownika kontrolki wyświetlania weryfikacyjnych](localization-string-ids.md#verification-display-control-user-interface-elements)i [komunikatów o błędach o jednym czasie](localization-string-ids.md#one-time-password-error-messages). Dodaj następujący LocalizedString do LocalizedResources. 

```XML
<LocalizedResources Id="api.custom-email.en">
  <LocalizedStrings>
    ...
    <!-- Display control UI elements-->
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="intro_msg">Verification is necessary. Please click Send button.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_send_code_msg">Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_send_code_msg">We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="success_verify_code_msg">E-mail address verified. You can now continue.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="failure_verify_code_msg">We are having trouble verifying your email address. Please try again.</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_code">Send verification code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_verify_code">Verify code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_send_new_code">Send new code</LocalizedString>
    <LocalizedString ElementType="DisplayControl" ElementId="emailVerificationControl" StringId="but_change_claims">Change e-mail</LocalizedString>
    <!-- Claims-->
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="DisplayName">Verification Code</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="UserHelpText">Verification code received in the email.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="emailVerificationCode" StringId="AdminHelpText">Verification code received in the email.</LocalizedString>
    <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">Eamil</LocalizedString>
    <!-- Email validation error messages-->
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionDoesNotExist">You have exceeded the maximum time allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMaxRetryAttempted">You have exceeded the number of retries allowed.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidCode">You have entered the wrong code.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfSessionConflict">Cannot verify the code, please try again later.</LocalizedString>
    <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfVerificationFailedRetryAllowed">The verification has failed, please try again.</LocalizedString>
  </LocalizedStrings>
</LocalizedResources>
```

Po dodaniu zlokalizowanych ciągów usuń metadane komunikatów o błędach weryfikacji OTP z profilów technicznych LocalAccountSignUpWithLogonEmail i LocalAccountDiscoveryUsingEmailAddress.

## <a name="next-steps"></a>Następne kroki

Przykład niestandardowych zasad weryfikacji poczty e-mail można znaleźć w witrynie GitHub:

- [Niestandardowa Weryfikacja poczty e-mail — DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)
- Aby uzyskać informacje o korzystaniu z niestandardowego interfejsu API REST lub dowolnego dostawcy poczty e-mail opartego na protokole HTTP, zobacz [Definiowanie profilu technicznego RESTful w zasadach niestandardowych Azure AD B2C](restful-technical-profile.md).
