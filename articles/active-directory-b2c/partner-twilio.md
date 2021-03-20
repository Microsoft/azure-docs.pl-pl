---
title: Twilio Weryfikuj aplikację przy użyciu Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: Dowiedz się, jak zintegrować przykładową aplikację płatniczą online w Azure AD B2C z interfejsem API weryfikacji Twilio. Zgodność z wymaganiami transakcji z PSD2 (dyrektywami usług płatniczych 2) w ramach konsolidacji dynamicznej i silnego uwierzytelniania klientów.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 953653a758577ed3d48ca2d81403b4cb363ea294
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994047"
---
# <a name="integrating-twilio-verify-app-with-azure-active-directory-b2c"></a>Integrowanie Twilio Weryfikuj aplikację z Azure Active Directory B2C

W tym instruktażu dowiesz się, jak zintegrować przykładową aplikację płatniczą online w Azure Active Directory B2C (Azure AD B2C) za pomocą interfejsu API weryfikacji Twilio. Za pomocą narzędzia Twilio verify Azure AD B2C App klienci mogą przestrzegać wymagań transakcji z PSD2 (dyrektywy Payment Services w dyrektywie 2) za pośrednictwem konsolidacji dynamicznej i silnego uwierzytelniania klientów.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, musisz:

* Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).
* [Dzierżawa Azure AD B2C](tutorial-create-tenant.md) , która jest połączona z subskrypcją platformy Azure.
* [Konto wersji próbnej](https://www.twilio.com/try-twilio) pod adresem Twilio.

## <a name="scenario-description"></a>Opis scenariusza

Następujące składniki tworzą rozwiązanie Twilio:

- [Aplikacja internetowa demonstracyjna .NET PSD2](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App), która umożliwia logowanie się lub rejestrowanie i wykonywanie fikcyjnej transakcji o wysokim ryzyku.
- Azure AD B2C połączone [zasady logowania i rejestrowania](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy)się.
- Zasady Azure AD B2C zintegrowane z usługą Twilio weryfikują interfejs API przy użyciu polecenia `id_token_hint` .
- Aplikacja sieci Web platformy .NET, która hostuje `.well-known` punkt końcowy OpenIdConnect, aby umożliwić weryfikację `id_token_hint` .


    ![przepływ Twilio](media/partner-twilio/twilio-flow.png)

| Krok | Opis |
|------|------|
| 1     | Użytkownik inicjuje Logowanie lub zarejestrowanie się w aplikacji demonstracyjnej PSD2. Użytkownik jest uwierzytelniany za pośrednictwem Azure AD B2C połączonych zasad logowania i rejestrowania. Token jest zwracany do aplikacji. Przy rejestrowaniu numer telefonu użytkownika jest sprawdzany przy użyciu wiadomości SMS/Phone i rejestrowany na koncie Azure AD B2C.     |
| 2     | Użytkownik inicjuje transakcję wysokiego ryzyka, taką jak transfer $50,00. Bieżący token dostępu użytkownika jest oceniany dla PolicyId w celu ustalenia, czy użytkownik został już uwierzytelniony za pośrednictwem zasad niestandardowych Step-Up.     |
| 3     | Aplikacja rejestruje wartość transakcji i element odbiorcy, $50,00 i Jan Nowak i generuje token podpisany. Token ten jest nazywany `id_token_hint` i zawiera element Claims `amount:$500, payee:john doe` . `id_token_hint`Jest wysyłany wraz z żądaniem do Azure AD B2C zasad niestandardowych, które są zintegrowane z Twilio.     |
| 4     | Azure AD B2C sprawdza podpis id_token_hint, sprawdzając `/.well-known` punkt końcowy aplikacji OpenID Connect Connect. Po sprawdzeniu wyodrębnia oświadczenia z tego tokenu, w szczególności `amount` i `payee` . Użytkownik zobaczy stronę, aby zweryfikować swój numer telefonu komórkowego za pośrednictwem wiadomości SMS.     |
| 5     | Użytkownik żąda zweryfikowania numeru telefonu za pośrednictwem wiadomości SMS, a Azure AD B2C wykonuje żądanie interfejsu API REST w celu sprawdzenia punktu końcowego interfejsu API usługi Twilio. Wysyła również transakcję `amount` i `payee` jako część procesu PSD2 do generowania kodu dostępu jednorazowego (OTP). Twilio wysyła wiadomość SMS na zarejestrowany numer telefonu użytkownika.     |
| 6     |  Użytkownik wprowadza żądanie OTP otrzymane w wiadomości SMS i przesyła je do Azure AD B2C. Azure AD B2C wykonuje żądanie interfejsu API z tym uwierzytelnianiem OTP Twilio interfejs API weryfikacji, aby sprawdzić, czy uwierzytelnianie OTP jest poprawne. W końcu token jest wystawiony dla aplikacji, a nowy PolicyId oznacza, że użytkownik został poddany próbie uwierzytelnienia.    |
|      |      |

## <a name="onboard-with-twilio"></a>Dołączanie do Twilio

1. Uzyskaj [konto wersji próbnej](https://www.twilio.com/try-twilio) pod adresem Twilio.

2. Kup numer telefonu w Twilio, zgodnie z opisem w [tym artykule](https://support.twilio.com/hc/articles/223135247-How-to-Search-for-and-Buy-a-Twilio-Phone-Number-from-Console) .

3. Przejdź do okna [Sprawdź poprawność interfejsu API](https://www.twilio.com/console/verify/services) w konsoli Twilio i postępuj zgodnie z [instrukcjami](https://www.twilio.com/docs/verify/verifying-transactions-psd2) , aby utworzyć usługę i włączyć opcję PSD2.  

## <a name="configure-the-psd2-demo-app"></a>Konfigurowanie aplikacji demonstracyjnej PSD2

1. Otwórz rozwiązanie B2C-WebAPI-DotNet i zastąp następujące wartości własnymi wartościami specyficznymi dla dzierżawy w web.config:

    ```xml
   <add key="ida:Tenant" value="yourtenant.onmicrosoft.com" />
   <add key="ida:TenantId" value="d6f33888-0000-4c1f-9b50-1590f171fc70" />
   <add key="ida:ClientId" value="6bd98cc8-0000-446a-a05e-b5716ef2651b" />
   <add key="ida:ClientSecret" value="secret" />
   <add key="ida:AadInstance" value="https://yourtenant.b2clogin.com/tfp/{0}/{1}" />
   <add key="ida:RedirectUri" value="https://your hosted psd2 demo app url/" />
   ```

2. [Aplikacja sieci Web](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/source-code/PSD2%20Demo%20App) hostuje również generator podpowiedzi tokenu identyfikatora i punkt końcowy metadanych.
   - Utwórz certyfikat podpisywania zgodnie z opisem w tym [przykładowym opisie](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#creating-a-signing-certificate).
   - Zaktualizuj następujące wiersze na podstawie certyfikatu w web.config:
   
   ```xml
   <add key="ida:SigningCertThumbprint" value="4F39D6014818082CBB763E5BA5F230E545212E89" />
   <add key="ida:SigningCertAlgorithm" value="RS256" />
   ```

3. Przekaż aplikację demonstracyjną do wybranego dostawcy hostingu. Wskazówki dotyczące Azure App Service są podane w [tym przykładowym opisie](https://github.com/azure-ad-b2c/samples/tree/master/policies/invite#hosting-the-application-in-azure-app-service), w tym instrukcje dotyczące przekazywania certyfikatu.

4. Aby zaktualizować rejestrację aplikacji Azure AD B2C, Dodaj adres URL odpowiedzi równy adresowi URL, pod którym jest hostowana aplikacja.

5. Otwórz [pliki zasad](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Twilio-VerifyAPI/policy) i Zastąp wszystkie wystąpienia  `contoso` nazwą dzierżawy.

6. Znajdź profil techniczny interfejsu API REST Twilio **Custom-SMS — Rejestracja**. Zaktualizuj  `ServiceURL`   program za pomocą Twilio AccountSID i numeru od do zakupionego numeru telefonu.

7. Znajdź profile techniczne interfejsu API REST usługi Twilio, **TwilioRestAPI-verify-krok 1**   i **TwilioRestAPI-verify-step2** i zaktualizuj je  `ServiceURL`   za pomocą Twilio AccountSID.

## <a name="integrate-with-azure-ad-b2c"></a>Integracja z usługą Azure AD B2C

Dodaj pliki zasad do Azure AD B2C:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.

2. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.

3. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.

4. Przejdź do **Azure AD B2C**  >  kluczy zasad programu **Identity Experience Framework**  >  .

5. Dodaj nowy klucz o nazwie **B2cRestTwilioClientId**. Wybierz pozycję **Ręczne** i podaj wartość Twilio AccountSID.

6. Dodaj nowy klucz o nazwie **B2cRestTwilioClientSecret**. Wybierz pozycję **Ręczne** i podaj wartość tokenu uwierzytelniania Twilio.

7. Przekaż wszystkie pliki zasad do dzierżawy.

8. Dostosuj ciąg w przekształceniu oświadczeń GenerateOTPMessageEnrol dla tekstu wiadomości SMS służącego do rejestrowania.

## <a name="test-the-solution"></a>Testowanie rozwiązania

* Przejdź do aplikacji i przetestuj operacje logowania, rejestracji i wysyłania pieniędzy.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje, zapoznaj się z następującymi artykułami:

- [Przykłady kodu integracji](https://github.com/azure-ad-b2c/samples/tree/master/policies/twilio-mfa-psd2) usługi GitHub for Twilio  

- [Zasady niestandardowe w AAD B2C](custom-policy-overview.md)

- [Wprowadzenie do zasad niestandardowych w AAD B2C](custom-policy-get-started.md?tabs=applications)
