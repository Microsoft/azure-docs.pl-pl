---
title: Samouczek konfigurowania Azure Active Directory B2C przy użyciu rozwiązania Zscaler
titleSuffix: Azure AD B2C
description: Dowiedz się, jak zintegrować uwierzytelnianie Azure AD B2C za pomocą rozwiązania Zscaler
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/09/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: ff51c2a71dfcaec580733a92e265628ac816e229
ms.sourcegitcommit: 5db975ced62cd095be587d99da01949222fc69a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97096058"
---
# <a name="tutorial-to-configure-zscaler-private-access-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>Samouczek dotyczący konfigurowania dostępu prywatnego rozwiązania Zscaler z Azure Active Directory B2C na potrzeby bezpiecznego dostępu hybrydowego

W tym samouczku dowiesz się, jak zintegrować uwierzytelnianie Azure AD B2C z [dostępem prywatnym rozwiązania Zscaler (ZPA)](https://www.zscaler.com/products/zscaler-private-access). ZPA zapewnia oparte na zasadach, bezpieczny dostęp do prywatnych aplikacji i zasobów bez konieczności ponoszenia kosztów, problemów lub ryzyka związanego z zabezpieczeniami wirtualnej sieci prywatnej (VPN). Oferta bezpiecznego dostępu hybrydowego rozwiązania Zscaler umożliwia przeprowadzenie zerowej powierzchni ataku dla aplikacji przeznaczonych dla klientów w połączeniu z Azure AD B2C.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, musisz:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).

- [Dzierżawa Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) , która jest połączona z subskrypcją platformy Azure.

- [Subskrypcja ZPA](https://azuremarketplace.microsoft.com/marketplace/apps/aad.zscalerprivateaccess?tab=Overview)

## <a name="scenario-description"></a>Opis scenariusza

Integracja ZPA obejmuje następujące składniki:

- Azure AD B2C — dostawca tożsamości (dostawcy tożsamości) odpowiedzialny za Weryfikowanie poświadczeń użytkownika. Jest on również odpowiedzialny za rejestrację nowego użytkownika.

- ZPA — usługa odpowiedzialna za zabezpieczanie aplikacji sieci Web przez wymuszenie [dostępu bez zaufania](https://www.microsoft.com/security/blog/2018/12/17/zero-trust-part-1-identity-and-access-management/#:~:text=Azure%20Active%20Directory%20%28Azure%20AD%29%20provides%20the%20strong%2C,to%20express%20their%20access%20requirements%20in%20simple%20terms.).

- Aplikacja sieci Web — hostuje usługę, do której użytkownik próbuje uzyskać dostęp.

Na poniższym diagramie przedstawiono sposób, w jaki ZPA integruje się z Azure AD B2C.

![Obraz przedstawia diagram architektury rozwiązania Zscaler](media/partner-zscaler/zscaler-architecture-diagram.png)

|Krok | Opis |
|:-----| :-----------|
| 1. | Użytkownik dociera do aplikacji Portal użytkowników ZPA lub dostęp do przeglądarki ZPA.
| 2. | ZPA wymaga informacji kontekstu użytkownika przed podjęciem decyzji o tym, czy zezwolić użytkownikowi na dostęp do aplikacji sieci Web. Aby uwierzytelnić użytkownika, ZPA wykonuje przekierowanie SAML do strony logowania Azure AD B2C.  
| 3. | Użytkownik dotarł do strony logowania do usługi Azure AB B2C. Jeśli jest to nowy użytkownik, użytkownik zostanie zarejestrowani, aby utworzyć nowe konto. Istniejący użytkownik zaloguje się przy użyciu istniejących poświadczeń. Azure AD B2C sprawdza tożsamość użytkownika.
| 4. | Po pomyślnym uwierzytelnieniu Azure AD B2C przekierowuje użytkownika z powrotem do ZPA wraz z potwierdzeniem SAML. ZPA weryfikuje potwierdzenie SAML i ustawia kontekst użytkownika.
| 5. | ZPA oblicza zasady dostępu dla użytkownika. Jeśli użytkownik może uzyskać dostęp do aplikacji sieci Web, połączenie może być przekazywane.

## <a name="onboard-to-zpa"></a>Dołączanie do ZPA

W tym samouczku założono, że masz już działającą konfigurację ZPA. Jeśli zaczynasz pracę z usługą ZPA, zapoznaj się z [przewodnikiem konfiguracji krok po kroku dla usługi ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa).

## <a name="integrate-with-azure-ad-b2c"></a>Integracja z usługą Azure AD B2C

### <a name="part-1---configure-azure-ad-b2c-as-an-idp-on-zpa"></a>Część 1 — Konfigurowanie Azure AD B2C jako dostawcy tożsamości w ZPA

Aby skonfigurować Azure AD B2C jako [dostawcę tożsamości (dostawcy tożsamości) w usłudze ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign):

1. Zaloguj się do [portalu administracyjnego ZPA](https://admin.private.zscaler.com)

2. Przejdź do pozycji **Administracja**  >  **Konfiguracja dostawcy tożsamości**

3. Wybierz pozycję **Dodaj konfigurację dostawcy tożsamości**

4. W przypadku **1 dostawcy tożsamości informacji** wpisz następujące polecenie:

   a. **Nazwa**: Azure AD B2C

   b. **Logowanie jednokrotne**: Wybieranie użytkownika

   c. **Domeny**: wybierz domeny uwierzytelniania, które chcesz skojarzyć z tym dostawcy tożsamości, a następnie wybierz pozycję **gotowe** .

   ![Obraz przedstawia informacje o dostawcy tożsamości](media/partner-zscaler/add-idp-configuration.png)

5. Wybierz pozycję **Dalej**

6. Dla **2 metadanych Sp**:

   a. Skopiuj adres URL dostawcy usługi i zanotuj go do późniejszego użycia.

   b. Skopiuj identyfikator jednostki dostawcy usług i zanotuj go do późniejszego użycia.

   ![Obraz przedstawia informacje o metadanych Sp](media/partner-zscaler/sp-metadata.png)

7. Wybierz pozycję **Wstrzymaj**

Pozostała część konfiguracji dostawcy tożsamości zostanie wznowiona po skonfigurowaniu Azure AD B2C.

### <a name="part-2---configure-custom-policy-in-azure-ad-b2c"></a>Część 2 — Konfigurowanie zasad niestandardowych w Azure AD B2C

>[!Note]
>Ten krok jest wymagany tylko wtedy, gdy nie masz jeszcze zasad niestandardowych. Jeśli masz już co najmniej jedną zasadę niestandardową, możesz pominąć ten krok.

Artykuł Rozpoczynanie [pracy z zasadami niestandardowymi w programie Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started) zawiera instrukcje dotyczące konfigurowania zasad niestandardowych w dzierżawie Azure AD B2C.

### <a name="part-3---register-zpa-as-a-saml-application-in-azure-ad-b2c"></a>Część 3 — rejestrowanie ZPA jako aplikacji SAML w Azure AD B2C

Artykuł [Rejestrowanie aplikacji SAML w Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers) zawiera instrukcje dotyczące KONFIGUROWANIA aplikacji SAML w programie Azure AD B2C. W [kroku 3,2](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#32-upload-and-test-your-policy-metadata)otrzymasz adres URL metadanych dostawcy tożsamości SAML używany przez Azure AD B2C. Będzie to potrzebne do późniejszego użycia.

Postępuj zgodnie z instrukcjami w [kroku 4,2](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers#42-update-the-app-manifest). W kroku 4,2 instrukcji należy zaktualizować manifest aplikacji. Zaktualizuj właściwości w następujący sposób:

- **identifierUris**: Użyj identyfikatora jednostki dostawcy usług zanotowanego w **części 1, krok 6a**.

- **samlMetadataUrl**: Pomiń tę właściwość, ponieważ ZPA nie obsługuje adresu URL metadanych SAML.

- **replyUrlsWithType**: Użyj adresu URL dostawcy usług zanotowanego w **części 1, krok 6B**.

- **logoutUrl**: Pomiń tę właściwość, ponieważ ZPA nie obsługuje adresu URL wylogowywania.

Pozostałe kroki nie są istotne dla tego samouczka.

### <a name="part-4---extract-the-idp-saml-metadata-from-azure-ad-b2c"></a>Część 4 — wyodrębnienie metadanych SAML dostawcy tożsamości z Azure AD B2C

W poprzednim kroku należy uzyskać adres URL metadanych SAML w następującym formacie:

```https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/<policy-name>/Samlp/metadata```

gdzie `<tenant-name>` jest nazwą dzierżawy Azure AD B2C i `<policy-name>` jest nazwą niestandardowych zasad SAML utworzonych w ostatnim kroku.

Na przykład https://safemarch.b2clogin.com/safemarch.onmicrosoft.com/B2C_1A_signup_signin_saml//Samlp/metadata

Otwórz przeglądarkę internetową i przejdź do adresu URL metadanych SAML. Gdy strona zostanie załadowana, kliknij prawym przyciskiem myszy w dowolnym miejscu na stronie. Wybierz pozycję **Zapisz stronę jako** i Zapisz plik na komputerze. zostanie ona użyta w następnej części.

### <a name="part-5---complete-idp-configuration-on-zpa"></a>Część 5 — kompletna konfiguracja dostawcy tożsamości na ZPA

Ukończ [konfigurację dostawcy tożsamości w portalu administratora ZPA](https://help.zscaler.com/zpa/configuring-idp-single-sign) , który został częściowo skonfigurowany w części 1:

1. Zaloguj się do [portalu administracyjnego ZPA](https://admin.private.zscaler.com)

2. Przejdź do pozycji **Administracja**  >  **Konfiguracja dostawcy tożsamości**.

3. Wybierz dostawcy tożsamości, który został skonfigurowany w części 1, a następnie wybierz pozycję **Wznów**.

4. Dla **3 Utwórz dostawcy tożsamości**

   a. Przejdź do **pliku metadanych dostawcy tożsamości**  >  **Wybierz plik** i Przekaż plik metadanych zapisany w części 4.

   b. Sprawdź, czy **stan** konfiguracji dostawcy tożsamości jest **włączony**.

   c. Wybierz pozycję **Zapisz**.

      ![Obraz przedstawia informacje o tworzeniu dostawcy tożsamości](media/partner-zscaler/create-idp.png)

## <a name="test-the-solution"></a>Testowanie rozwiązania

Przejdź do portalu użytkownika ZPA lub aplikacji dostępu do przeglądarki, a następnie przetestuj proces tworzenia konta lub logowania. Powinno to spowodować pomyślne uwierzytelnienie SAML.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje, zapoznaj się z następującymi artykułami:

- [Wprowadzenie do zasad niestandardowych w Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)

- [Rejestrowanie aplikacji SAML w Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/connect-with-saml-service-providers)

- [Przewodnik konfiguracji krok po kroku dla usługi ZPA](https://help.zscaler.com/zpa/step-step-configuration-guide-zpa)

- [Konfigurowanie dostawcy tożsamości na potrzeby logowania jednokrotnego](https://help.zscaler.com/zpa/configuring-idp-single-sign)
