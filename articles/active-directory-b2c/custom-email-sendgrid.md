---
title: Niestandardowa weryfikacja wiadomości e-mail przy użyciu usługi SendGrid
titleSuffix: Azure AD B2C
description: Dowiedz się, jak zintegrować usługę z usługą SendGrid w celu dostosowania weryfikacyjnej wiadomości e-mail wysyłanej do klientów podczas rejestracji w celu korzystania Azure AD B2C z obsługą aplikacji.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/16/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2c4dcaaa1deaa50d620e7c24dacbe56fa91c217f
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713448"
---
# <a name="custom-email-verification-with-sendgrid"></a>Niestandardowa weryfikacja wiadomości e-mail przy użyciu usługi SendGrid

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Użyj niestandardowej poczty e-Azure Active Directory B2C aplikacji (Azure AD B2C), aby wysyłać dostosowane wiadomości e-mail do użytkowników, którzy zarejestrują się w celu korzystania z aplikacji. Korzystając z usługi SendGrid dostawcy poczty e-mail innej  firmy, możesz użyć własnego szablonu wiadomości e-mail i adresu i tematu z adresu i tematu, a także obsługiwać ustawienia lokalizacji i niestandardowe hasła jednorazowego (OTP).

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Niestandardowa weryfikacja poczty e-mail wymaga użycia zewnętrznego dostawcy poczty e-mail, takiego jak [SendGrid,](https://sendgrid.com) [Mail email lub](https://Mailjet.com) [SparkPost,](https://sparkpost.com)niestandardowego interfejsu API REST lub dowolnego dostawcy poczty e-mail opartego na http (w tym Twojego własnego). W tym artykule opisano konfigurowanie rozwiązania, które używa usługi SendGrid.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-sendgrid-account"></a>Tworzenie konta sendGrid

Jeśli jeszcze jej nie masz, zacznij od skonfigurowania konta usługi SendGrid (klienci platformy Azure mogą odblokować 25 000 bezpłatnych wiadomości e-mail każdego miesiąca). Aby uzyskać instrukcje dotyczące konfiguracji, zobacz [sekcję Tworzenie konta SendGrid](../sendgrid-dotnet-how-to-send-email.md#create-a-sendgrid-account) w tesłudze jak wysyłać wiadomości e-mail [przy użyciu usługi SendGrid na platformie Azure.](../sendgrid-dotnet-how-to-send-email.md)

Pamiętaj, aby ukończyć sekcję, w której [tworzysz klucz interfejsu API usługi SendGrid.](../sendgrid-dotnet-how-to-send-email.md#to-find-your-sendgrid-api-key) Zanotuj klucz interfejsu API do użycia w późniejszym kroku.

> [!IMPORTANT]
> SendGrid oferuje klientom możliwość wysyłania wiadomości e-mail z udostępnionego adresu IP i [dedykowanych adresów IP.](https://sendgrid.com/docs/ui/account-and-settings/dedicated-ip-addresses/) W przypadku korzystania z dedykowanych adresów IP należy odpowiednio budować własną reputację dzięki rozgrzewce adresów IP. Aby uzyskać więcej informacji, zobacz [Warming Up An IP Address (Rozgrzewanie adresu IP).](https://sendgrid.com/docs/ui/sending-email/warming-up-an-ip-address/)

## <a name="create-azure-ad-b2c-policy-key"></a>Tworzenie Azure AD B2C zasad grupy

Następnie przechowuj klucz interfejsu API usługi SendGrid w kluczu zasad Azure AD B2C, do których mają się odwoływać zasady.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Upewnij się, że używasz katalogu zawierającego dzierżawę Azure AD B2C dzierżawy. Wybierz filtr **Katalog i subskrypcja** w górnym menu, a następnie wybierz Azure AD B2C katalog.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Na stronie Przegląd wybierz pozycję **Identity Experience Framework**.
1. Wybierz **pozycję Klucze zasad,** a następnie wybierz **pozycję Dodaj.**
1. W **przypadku opcji** wybierz opcję **Ręcznie.**
1. Wprowadź **nazwę** klucza zasad. Na przykład `SendGridSecret`. Prefiks `B2C_1A_` jest dodawany automatycznie do nazwy klucza.
1. W **kluczu** tajnym wprowadź wcześniej zarejestrowany klucz interfejsu API SendGrid.
1. W **przypadku opcji Użycie klucza** wybierz pozycję **Podpis**.
1. Wybierz przycisk **Utwórz**.

## <a name="create-sendgrid-template"></a>Tworzenie szablonu SendGrid

Po utworzeniu konta usługi SendGrid i kluczu interfejsu API usługi SendGrid przechowywanego w kluczu zasad Azure AD B2C utwórz dynamiczny szablon [transakcyjny](https://sendgrid.com/docs/ui/sending-email/how-to-send-an-email-with-dynamic-transactional-templates/)SendGrid.

1. W witrynie SendGrid otwórz stronę [szablonów transakcyjnych](https://sendgrid.com/dynamic_templates) i wybierz **pozycję Utwórz szablon**.
1. Wprowadź unikatową nazwę szablonu, na przykład `Verification email` , a następnie wybierz pozycję **Zapisz.**
1. Aby rozpocząć edytowanie nowego szablonu, wybierz **pozycję Dodaj wersję.**
1. Wybierz **pozycję Edytor kodu,** a następnie **pozycję Kontynuuj.**
1. W edytorze HTML wklej następujący szablon HTML lub użyj własnego. Parametry i zostaną zastąpione dynamicznie wartością hasła i adresem `{{otp}}` `{{email}}` e-mail użytkownika.

    ```HTML
    <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

    <html xmlns="http://www.w3.org/1999/xhtml" dir="ltr" lang="en"><head id="Head1">
    <meta http-equiv="Content-Type" content="text/html; charset=utf-8"><title>Contoso demo account email verification code</title><meta name="ROBOTS" content="NOINDEX, NOFOLLOW">
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
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence1">Thanks for verifying your {{email}} account!</span>
       </div>
       <br>
       <div style="font-family:'Segoe UI', Tahoma, sans-serif; font-size:14px; color:#333; font-weight: bold">
           <span id="BodyPlaceholder_UserVerificationEmailBodySentence2">Your code is: {{otp}}</span>
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

1. Rozwiń **pozycję Ustawienia** po lewej stronie, a w **przypadku tematu wiadomości e-mail** wprowadź `{{subject}}` .
1. Wybierz **pozycję Zapisz szablon.**
1. Wróć do **strony Szablony transakcyjne,** wybierając strzałkę wstecz.
1. **Zanotuj** identyfikator szablonu, który został utworzony do użycia w późniejszym kroku. Na przykład `d-989077fbba9746e89f3f6411f596fb96`. Ten identyfikator należy określić podczas [dodawania przekształcenia oświadczeń](#add-the-claims-transformation).

## <a name="add-azure-ad-b2c-claim-types"></a>Dodawanie Azure AD B2C typów oświadczenia

W zasadach dodaj następujące typy oświadczenia do `<ClaimsSchema>` elementu w elemencie `<BuildingBlocks>` .

Te typy oświadczeń są niezbędne do generowania i weryfikowania adresu e-mail przy użyciu kodu hasła jednorazowego (OTP).

```xml
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="Otp">
      <DisplayName>Secondary One-time password</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    <ClaimType Id="emailRequestBody">
      <DisplayName>SendGrid request body</DisplayName>
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

## <a name="add-the-claims-transformation"></a>Dodawanie przekształcenia oświadczeń

Następnie należy przekształcć oświadczenia w celu wyprowadzenia oświadczenia ciągu JSON, które będzie treścią żądania wysyłanego do sendGrid.

Struktura obiektu JSON jest definiowana przez identyfikatory w notacji kropki dla właściwości InputParameters i TransformationClaimTypes obiektu InputClaims. Liczby w notacji kropki oznaczają tablice. Wartości pochodzą z wartości właściwości InputClaims i "Value" właściwości InputParameters. Aby uzyskać więcej informacji na temat przekształceń oświadczeń JSON, zobacz [Przekształcenia oświadczeń JSON](json-transformations.md).

Dodaj następujące przekształcenie oświadczeń do `<ClaimsTransformations>` elementu w elemencie `<BuildingBlocks>` . W pliku XML przekształcania oświadczeń należy wprowadzić następujące aktualizacje:

* Zaktualizuj wartość InputParameter przy użyciu identyfikatora szablonu transakcyjnego SendGrid utworzonego wcześniej w `template_id` ramach tworzenia [szablonu SendGrid.](#create-sendgrid-template)
* Zaktualizuj `from.email` wartość adresu. Użyj prawidłowego adresu e-mail, aby zapobiec oznaczaniu weryfikacyjnej wiadomości e-mail jako spamu.
* Zaktualizuj wartość parametru `personalizations.0.dynamic_template_data.subject` wejściowego wiersza tematu przy użyciu wiersza tematu odpowiedniego dla Twojej organizacji.

```xml
<!-- 
<BuildingBlocks>
  <ClaimsTransformations> -->
    <ClaimsTransformation Id="GenerateEmailRequestBody" TransformationMethod="GenerateJson">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
      </InputClaims>
      <InputParameters>
        <!-- Update the template_id value with the ID of your SendGrid template. -->
        <InputParameter Id="template_id" DataType="string" Value="d-989077fbba9746e89f3f6411f596fb96"/>
        <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com"/>
        <!-- Update with a subject line appropriate for your organization. -->
        <InputParameter Id="personalizations.0.dynamic_template_data.subject" DataType="string" Value="Contoso account email verification code"/>
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim"/>
      </OutputClaims>
    </ClaimsTransformation>
  <!--
  </ClaimsTransformations>
</BuildingBlocks> -->
```

## <a name="add-datauri-content-definition"></a>Dodawanie definicji zawartości DataUri

Poniżej przekształceń oświadczeń w pliku dodaj następujący kod ContentDefinition, aby odwołać się do danych URI w wersji `<BuildingBlocks>` 2.1.2: [](contentdefinitions.md)

```xml
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

## <a name="create-a-displaycontrol"></a>Tworzenie kontrolera DisplayControl

Kontrolka wyświetlania weryfikacji służy do weryfikowania adresu e-mail przy użyciu kodu weryfikacyjnego wysłanego do użytkownika.

Ta przykładowa kontrolka wyświetlania jest skonfigurowana w taki sposób, aby:

1. Zbierz `email` typ oświadczenia adresu od użytkownika.
1. Poczekaj, aż użytkownik poda `verificationCode` typ oświadczenia wraz z kodem wysłanym do użytkownika.
1. Wróć do `email` samodzielnie potwierdzanych profilów technicznych, które mają odwołanie do tej kontrolki wyświetlania.
1. Za pomocą `SendCode` akcji wygeneruj kod OTP i wyślij wiadomość e-mail z kodem OTP do użytkownika.

![Akcja wyślij wiadomość e-mail z kodem weryfikacyjnym](media/custom-email-sendgrid/display-control-verification-email-action-01.png)

W obszarze definicji zawartości, nadal w `<BuildingBlocks>` programie , dodaj następujący kontroler [DisplayControl](display-controls.md) typu [VerificationControl](display-control-verification.md) do zasad.

```xml
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

## <a name="add-otp-technical-profiles"></a>Dodawanie profilów technicznych OTP

Profil `GenerateOtp` techniczny generuje kod dla adresu e-mail. Profil `VerifyOtp` techniczny weryfikuje kod skojarzony z adresem e-mail. Konfigurację formatu i wygaśnięcie hasła można zmienić. Aby uzyskać więcej informacji na temat profilów technicznych OTP, zobacz Define a one-time password technical profile (Definiowanie [profilu technicznego haseł jednorazowych).](one-time-password-technical-profile.md)

Dodaj następujące profile techniczne do `<ClaimsProviders>` elementu .

```xml
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

Ten profil techniczny interfejsu API REST generuje zawartość wiadomości e-mail (przy użyciu formatu SendGrid). Aby uzyskać więcej informacji na temat profilów technicznych RESTful, zobacz Define a RESTful technical profile (Definiowanie [profilu technicznego RESTful).](restful-technical-profile.md)

Podobnie jak w przypadku profilów technicznych OTP, dodaj następujące profile techniczne do `<ClaimsProviders>` elementu .

```xml
<ClaimsProvider>
  <DisplayName>RestfulProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SendOtp">
      <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="ClaimUsedForRequestPayload">emailRequestBody</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridSecret" />
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

## <a name="make-a-reference-to-the-displaycontrol"></a>Odwołanie do DisplayControl

W ostatnim kroku dodaj odwołanie do utworzonego kontrolera DisplayControl. Zastąp istniejące `LocalAccountSignUpWithLogonEmail` i `LocalAccountDiscoveryUsingEmailAddress` samodzielnie potwierdzane profile techniczne następującymi. Jeśli używasz starszej wersji zasad Azure AD B2C zasad. Te profile techniczne używają `DisplayClaims` z odwołaniem do displaycontrol.

Aby uzyskać więcej informacji, zobacz [Samodzielnie potwierdzany profil techniczny i](restful-technical-profile.md) [DisplayControl](display-controls.md).

```xml
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

## <a name="optional-localize-your-email"></a>[Opcjonalnie] Lokalizacja wiadomości e-mail

Aby zlokalizowane wiadomości e-mail, należy wysłać zlokalizowane ciągi do usługi SendGrid lub dostawcy poczty e-mail. Na przykład możesz zlokalizowane tematu, treści wiadomości e-mail, wiadomości kodowej lub podpisu wiadomości e-mail. W tym celu można użyć przekształcenia oświadczeń [GetLocalizedStringsTransformation,](string-transformations.md) aby skopiować zlokalizowane ciągi do typów oświadczeń. Przekształcenie `GenerateEmailRequestBody` oświadczeń, które generuje ładunek JSON, używa oświadczeń wejściowych, które zawierają zlokalizowane ciągi.

1. W zasadach zdefiniuj następujące oświadczenia ciągu: temat, komunikat, codeIntro i podpis.
1. [Zdefiniuj przekształcenie oświadczeń GetLocalizedStringsTransformation,](string-transformations.md) aby zastąpić zlokalizowane wartości ciągów oświadczeniami z kroku 1.
1. Zmień przekształcenie `GenerateEmailRequestBody` oświadczeń, aby użyć oświadczeń wejściowych z następującym fragmentem kodu XML.
1. Zaktualizuj szablon SendGrid, aby używać parametrów dynamicznych w miejsce wszystkich ciągów, które zostaną zlokalizowane przez Azure AD B2C.

    ```xml
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
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.to.0.email" />
        <InputClaim ClaimTypeReferenceId="subject" TransformationClaimType="personalizations.0.dynamic_template_data.subject" />
        <InputClaim ClaimTypeReferenceId="otp" TransformationClaimType="personalizations.0.dynamic_template_data.otp" />
        <InputClaim ClaimTypeReferenceId="email" TransformationClaimType="personalizations.0.dynamic_template_data.email" />
        <InputClaim ClaimTypeReferenceId="message" TransformationClaimType="personalizations.0.dynamic_template_data.message" />
        <InputClaim ClaimTypeReferenceId="codeIntro" TransformationClaimType="personalizations.0.dynamic_template_data.codeIntro" />
        <InputClaim ClaimTypeReferenceId="signature" TransformationClaimType="personalizations.0.dynamic_template_data.signature" />
      </InputClaims>
      <InputParameters>
        <InputParameter Id="template_id" DataType="string" Value="d-1234567890" />
        <InputParameter Id="from.email" DataType="string" Value="my_email@mydomain.com" />
      </InputParameters>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="emailRequestBody" TransformationClaimType="outputClaim" />
      </OutputClaims>
    </ClaimsTransformation>
    ```

1. Dodaj następujący [element lokalizacji.](localization.md)

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
            <!--Email template parameters-->
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_subject">Contoso account email verification code</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_message">Thanks for validating the account</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_code">Your code is</LocalizedString>
            <LocalizedString ElementType="GetLocalizedStringsTransformationClaimType" StringId="email_signature">Sincerely</LocalizedString>
          </LocalizedStrings>
        </LocalizedResources>
        <LocalizedResources Id="api.custom-email.es">
          <LocalizedStrings>
            <!--Email template parameters-->
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

1. Dodaj odwołania do elementów LocalizedResources, aktualizując element [ContentDefinitions.](contentdefinitions.md)

    ```XML
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

1. Na koniec dodaj następujące przekształcenia oświadczeń wejściowych do `LocalAccountSignUpWithLogonEmail` profilów technicznych `LocalAccountDiscoveryUsingEmailAddress` i .

    ```XML
    <InputClaimsTransformations>
      <InputClaimsTransformation ReferenceId="GetLocalizedStringsForEmail" />
    </InputClaimsTransformations>
    ```
    
## <a name="optional-localize-the-ui"></a>[Opcjonalnie] Lokalizacja interfejsu użytkownika

Element Lokalizacja umożliwia obsługę wielu lokalizacji regionalnych lub języków w zasadach dla podróży użytkownika. Obsługa lokalizacji w zasadach umożliwia zapewnienie ciągów specyficznych [](localization-string-ids.md#verification-display-control-user-interface-elements)dla języka dla elementów interfejsu użytkownika kontroli wyświetlania weryfikacji i komunikatów o błędach hasła [tylko raz.](localization-string-ids.md#one-time-password-error-messages) Dodaj następujący zlokalizowanyciąg do lokalizacji LocalizedResources. 

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

- [Niestandardowa weryfikacja wiadomości e-mail — DisplayControls](https://github.com/azure-ad-b2c/samples/tree/master/policies/custom-email-verifcation-displaycontrol)
- Aby uzyskać informacje na temat korzystania z niestandardowego interfejsu API REST lub dowolnego dostawcy poczty e-mail SMTP opartego na Azure AD B2C HTTP, zobacz Define a RESTful technical profile in an Azure AD B2C custom policy (Definiowanie profilu technicznego [RESTful w Azure AD B2C zasad niestandardowych).](restful-technical-profile.md)

::: zone-end
