---
title: Samouczek — Konfigurowanie Azure Active Directory B2C przy użyciu rozwiązania Zscaler
titleSuffix: Azure AD B2C
description: Dowiedz się, jak zintegrować uwierzytelnianie Azure AD B2C z usługą rozwiązania Zscaler.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 254f8da74a187e88cfb973da7100fe5654c84bb6
ms.sourcegitcommit: a89a517622a3886b3a44ed42839d41a301c786e0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97732450"
---
# <a name="tutorial-configure-zscaler-private-access-with-azure-active-directory-b2c"></a>Samouczek: Konfigurowanie prywatnego dostępu rozwiązania Zscaler za pomocą Azure Active Directory B2C

W tym samouczku dowiesz się, jak zintegrować uwierzytelnianie Azure Active Directory B2C (Azure AD B2C) z [dostępem prywatnym rozwiązania Zscaler (ZPA)](https://www.zscaler.com/products/zscaler-private-access). ZPA zapewnia oparte na zasadach, bezpieczny dostęp do prywatnych aplikacji i zasobów bez konieczności ponoszenia kosztów, problemów lub ryzyka związanego z zabezpieczeniami wirtualnej sieci prywatnej (VPN). Oferta rozwiązania Zscaler bezpiecznego dostępu hybrydowego umożliwia przeprowadzenie zerowej powierzchni ataku dla aplikacji przeznaczonych dla klientów, gdy jest ona łączona z Azure AD B2C.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem należy:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).  
- [Dzierżawa Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) , która jest połączona z subskrypcją platformy Azure.  
- [Subskrypcja usługi ZPA](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview).

## <a name="scenario-description"></a>Opis scenariusza

Integracja ZPA obejmuje następujące składniki:

- **Azure AD B2C**: dostawca tożsamości (dostawcy tożsamości), który jest odpowiedzialny za Weryfikowanie poświadczeń użytkownika. Jest on również odpowiedzialny za rejestrację nowego użytkownika.  
- **ZPA**: usługa, która jest odpowiedzialna za zabezpieczanie aplikacji sieci Web przez wymuszanie [dostępu z zaufaniem równym zeru](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.).  
- **Aplikacja sieci Web**: hostuje usługę, do której użytkownik próbuje uzyskać dostęp.

Na poniższym diagramie przedstawiono sposób, w jaki ZPA integruje się z Azure AD B2C.

![Diagram architektury rozwiązania Zscaler, pokazujący, jak ZPA integrują się z Azure AD B2C.](media/partner-zscaler/zscaler-architecture-diagram.png)

Sekwencja jest opisana w poniższej tabeli:

|Krok | Opis |
| :-----:| :-----------|
| 1 | Użytkownik dociera do portalu użytkownika ZPA lub aplikacji dostępu do przeglądarki ZPA.
| 2 | ZPA wymaga informacji kontekstu użytkownika przed podjęciem decyzji o tym, czy zezwolić użytkownikowi na dostęp do aplikacji sieci Web. Aby uwierzytelnić użytkownika, ZPA wykonuje przekierowanie SAML do strony logowania Azure AD B2C.  
| 3 | Użytkownik dotarł do strony logowania do usługi Azure AB B2C. Nowi użytkownicy rejestrują się, aby utworzyć konto, a istniejący użytkownicy logują się przy użyciu istniejących poświadczeń. Azure AD B2C sprawdza tożsamość użytkownika.
| 4 | Po pomyślnym uwierzytelnieniu Azure AD B2C przekierowuje użytkownika z powrotem do ZPA wraz z potwierdzeniem SAML. ZPA weryfikuje potwierdzenie SAML i ustawia kontekst użytkownika.
| 5 | ZPA oblicza zasady dostępu dla użytkownika. Jeśli użytkownik może uzyskać dostęp do aplikacji sieci Web, połączenie może być przekazywane.

## <a name="onboard-to-zpa"></a>Dołączanie do ZPA

W tym samouczku założono, że masz już działającą konfigurację ZPA. Jeśli zaczynasz pracę z usługą ZPA, zapoznaj się z [przewodnikiem konfiguracji krok po kroku dla usługi ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa).

## <a name="integrate-zpa-with-azure-ad-b2c"></a>Integruj ZPA z Azure AD B2C

### <a name="step-1-configure-azure-ad-b2c-as-an-idp-on-zpa"></a>Krok 1. Konfigurowanie Azure AD B2C jako dostawcy tożsamości w ZPA

Aby skonfigurować Azure AD B2C jako [dostawcy tożsamości w ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign), wykonaj następujące czynności:

1. Zaloguj się do [portalu administracyjnego ZPA](https://admin.private.zscaler.com).

1. Przejdź do pozycji **Administracja**  >  **Konfiguracja dostawcy tożsamości**.

1. Wybierz pozycję **Dodaj konfigurację dostawcy tożsamości**.

   Zostanie otwarte okienko **Dodaj konfigurację dostawcy tożsamości** .

   ![Zrzut ekranu przedstawiający kartę "informacje o dostawcy tożsamości" w okienku "Dodawanie konfiguracji dostawcy tożsamości".](media/partner-zscaler/add-idp-configuration.png)

1. Wybierz kartę **informacje dostawcy tożsamości** , a następnie wykonaj następujące czynności:

   a. W polu **Nazwa** wprowadź **Azure AD B2C**.  
   b. W obszarze **Logowanie jednokrotne** wybierz pozycję **użytkownik**.  
   c. Z listy rozwijanej **domeny** wybierz domeny uwierzytelniania, które chcesz skojarzyć z tym dostawcy tożsamości.

1. Wybierz pozycję **Dalej**.

1. Wybierz kartę **metadane Sp** , a następnie wykonaj następujące czynności:

   a. W obszarze **adres URL dostawcy usług** Skopiuj lub Zanotuj wartość do późniejszego użycia.  
   b. W obszarze **Identyfikator jednostki dostawcy usług** Skopiuj lub Zanotuj wartość do późniejszego użycia.

   ![Zrzut ekranu karty "SP Metadata" w okienku "Dodawanie konfiguracji dostawcy tożsamości".](media/partner-zscaler/sp-metadata.png)

1. Wybierz pozycję **Wstrzymaj**.

Po skonfigurowaniu Azure AD B2C pozostałe wznowienie konfiguracji dostawcy tożsamości.

### <a name="step-2-configure-custom-policies-in-azure-ad-b2c"></a>Krok 2. Konfigurowanie zasad niestandardowych w Azure AD B2C

>[!Note]
>Ten krok jest wymagany tylko wtedy, gdy zasady niestandardowe nie zostały jeszcze skonfigurowane. Jeśli masz już co najmniej jedną zasadę niestandardową, możesz pominąć ten krok.

Aby skonfigurować zasady niestandardowe dla dzierżawy Azure AD B2C, zobacz Wprowadzenie [do zasad niestandardowych w programie Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started).

### <a name="step-3-register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>Krok 3. rejestrowanie ZPA jako aplikacji SAML w Azure AD B2C

Aby skonfigurować aplikację SAML w Azure AD B2C, zobacz [Rejestrowanie aplikacji SAML w Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers). 

W kroku ["3,2 przekazanie i przetestowanie metadanych zasad"](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#32-upload-and-test-your-policy-metadata)Skopiuj lub ZANOTUJ adres URL metadanych dostawcy tożsamości SAML używany przez Azure AD B2C. Będzie on potrzebny później.

Postępuj zgodnie z instrukcjami w kroku ["4,2. Zaktualizuj manifest aplikacji"](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#42-update-the-app-manifest). W kroku 4,2 zaktualizuj właściwości manifestu aplikacji w następujący sposób:

- Dla **identifierUris**: Użyj identyfikatora jednostki dostawcy usług, który został skopiowany lub zanotowany wcześniej w "krok 1.6. b".  
- Dla **samlMetadataUrl**: Pomiń tę właściwość, ponieważ ZPA nie obsługuje adresu URL metadanych SAML.  
- W przypadku **replyUrlsWithType**: Użyj adresu URL dostawcy usługi, który został skopiowany lub zanotowany wcześniej w sekcji "krok 1.6. a".  
- Dla **logoutUrl**: Pomiń tę właściwość, ponieważ ZPA nie obsługuje adresu URL wylogowywania.

Pozostałe kroki nie są istotne dla tego samouczka.

### <a name="step-4-extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>Krok 4. wyodrębnienie metadanych SAML dostawcy tożsamości z Azure AD B2C

Następnie musisz uzyskać adres URL metadanych SAML w następującym formacie:

```https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata```

Należy pamiętać, że `<tenant-name>` jest nazwą dzierżawy Azure AD B2C i `<policy-name>` jest nazwą niestandardowych zasad SAML utworzonych w poprzednim kroku.

Na przykład adres URL może być `https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml//Samlp/metadata` .

Otwórz przeglądarkę internetową i przejdź do adresu URL metadanych SAML. Kliknij prawym przyciskiem myszy w dowolnym miejscu na stronie, wybierz polecenie **Zapisz jako**, a następnie Zapisz plik na komputerze do użycia w następnym kroku.

### <a name="step-5-complete-the-idp-configuration-on-zpa"></a>Krok 5. dokończ konfigurację dostawcy tożsamości na ZPA

Ukończ [konfigurację dostawcy tożsamości w portalu administracyjnym ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign) , który został wcześniej skonfigurowany w sekcji "krok 1: Konfigurowanie Azure AD B2C jako dostawcy tożsamości na ZPA".

1. W [portalu administracyjnym ZPA](https://admin.private.zscaler.com)przejdź do pozycji **Administracja**  >  **dostawcy tożsamości konfiguracja**.

1. Wybierz dostawcy tożsamości, który został skonfigurowany w kroku 1, a następnie wybierz pozycję **Wznów**.

1. W okienku **Dodaj konfigurację dostawcy tożsamości** wybierz kartę **Tworzenie dostawcy tożsamości** , a następnie wykonaj następujące czynności:

   a. W obszarze **plik metadanych dostawcy tożsamości** Przekaż plik metadanych, który został zapisany wcześniej w "krok 4: Wyodrębnienie metadanych SAML dostawcy tożsamości z Azure AD B2C".  
   b. Sprawdź, czy **stan** konfiguracji dostawcy tożsamości jest **włączony**.  
   c. Wybierz pozycję **Zapisz**.

   ![Zrzut ekranu przedstawiający kartę "Tworzenie dostawcy tożsamości" w okienku "Dodawanie konfiguracji dostawcy tożsamości".](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>Testowanie rozwiązania

Przejdź do portalu użytkownika ZPA lub aplikacji dostępu do przeglądarki, a następnie przetestuj proces tworzenia konta lub logowania. Test powinien spowodować pomyślne uwierzytelnienie SAML.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zapoznaj się z następującymi artykułami:

- [Wprowadzenie do zasad niestandardowych w Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)
- [Rejestrowanie aplikacji SAML w Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers)
- [Przewodnik konfiguracji krok po kroku dla usługi ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)
- [Konfigurowanie dostawcy tożsamości na potrzeby logowania jednokrotnego](https://help.zscaler.com/zpa/configuring-idp-single-sign)
