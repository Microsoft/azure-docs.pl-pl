---
title: Uzyskiwanie tokenu w aplikacji systemu Android
titleSuffix: Azure AD B2C
description: Jak utworzyć aplikację dla systemu Android, która używa AppAuth z Azure Active Directory B2C, aby zarządzać tożsamościami użytkowników i uwierzytelniać użytkowników.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 31ad373b1544fc601a9c37e05e324a9c1dfb3f73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78183787"
---
# <a name="sign-in-using-an-android-application-in-azure-active-directory-b2c"></a>Logowanie przy użyciu aplikacji systemu Android w Azure Active Directory B2C

Platforma Microsoft Identity korzysta z otwartych standardów, takich jak OAuth2 i OpenID Connect. Te standardy umożliwiają korzystanie z dowolnej biblioteki, którą chcesz zintegrować z usługą Azure Active Directory B2C. Aby ułatwić korzystanie z innych bibliotek, można użyć przewodnika, takiego jak ten, aby zademonstrować sposób konfigurowania bibliotek innych firm do łączenia się z platformą tożsamości firmy Microsoft. Większość bibliotek implementujących [specyfikację RFC6749 OAuth2](https://tools.ietf.org/html/rfc6749) może łączyć się z platformą tożsamości firmy Microsoft.

> [!WARNING]
> Firma Microsoft nie udostępnia poprawek dla bibliotek innych firm i nie przeprowadzono przeglądu tych bibliotek. Ten przykład korzysta z biblioteki innej firmy o nazwie AppAuth, która została przetestowana pod kątem zgodności w podstawowych scenariuszach z Azure AD B2C. Problemy i żądania funkcji powinny być kierowane do projektu open-source biblioteki. Aby uzyskać więcej informacji, zobacz [ten artykuł](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-libraries) .
>
>

Jeśli dopiero rozpoczynasz korzystanie ze standardu OAuth2 lub OpenID Connect, spora część tej przykładowej konfiguracji może być dla Ciebie niezrozumiała. Zalecamy zapoznanie się z [krótkim omówieniem protokołu w tej dokumentacji](protocols-overview.md).

## <a name="get-an-azure-ad-b2c-directory"></a>Tworzenie katalogu usługi Azure AD B2C

Przed rozpoczęciem korzystania z usługi Azure AD B2C należy utworzyć katalog lub dzierżawę. Katalog jest kontenerem dla wszystkich użytkowników, aplikacji, grup i innych elementów. Jeśli jeszcze go nie masz, [utwórz katalog usługi B2C](tutorial-create-tenant.md), zanim przejdziesz dalej.

## <a name="create-an-application"></a>Tworzenie aplikacji

Następnie zarejestruj aplikację w dzierżawie Azure AD B2C. Dzięki temu usługa Azure AD musi uzyskać bezpieczne komunikowanie się z Twoją aplikacją.

[!INCLUDE [active-directory-b2c-appreg-native](../../includes/active-directory-b2c-appreg-native.md)]

Zapisz **Identyfikator aplikacji (klienta)** do użycia w późniejszym kroku.

Zapisz także niestandardowy identyfikator URI przekierowania do użycia w późniejszym kroku. Na przykład `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

## <a name="create-your-user-flows"></a>Tworzenie przepływów użytkownika

W Azure AD B2C każde środowisko użytkownika jest definiowane przez [przepływ użytkownika](user-flow-overview.md), czyli zestaw zasad kontrolujących zachowanie usługi Azure AD. Ta aplikacja wymaga przepływu użytkownika logowania i logowania. Podczas tworzenia przepływu użytkownika upewnij się, że:

* Wybierz **nazwę wyświetlaną** jako atrybut rejestracji w przepływie użytkownika.
* Wybierz **nazwę wyświetlaną** i **Identyfikator obiektu** oświadczenia aplikacji w każdym przepływie użytkownika. Można również wybrać inne oświadczenia.
* Skopiuj **nazwę** każdego przepływu użytkownika po jego utworzeniu. Powinny zawierać prefiks `b2c_1_`.  Nazwa przepływu użytkownika będzie potrzebna później.

Po utworzeniu przepływów użytkowników możesz utworzyć aplikację.

## <a name="download-the-sample-code"></a>Pobieranie przykładowego kodu

Udostępnimy przykład roboczy, który używa usługi AppAuth z usługą Azure AD B2C [w serwisie GitHub](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Możesz pobrać kod i uruchomić go. Możesz szybko rozpocząć pracę z własną aplikacją przy użyciu własnej konfiguracji Azure AD B2C, postępując zgodnie z instrukcjami podanymi w [README.MD](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md).

Przykładem jest modyfikacja przykładu dostarczonego przez [AppAuth](https://openid.github.io/AppAuth-Android/). Odwiedź stronę, aby dowiedzieć się więcej na temat AppAuth i jego funkcji.

## <a name="modifying-your-app-to-use-azure-ad-b2c-with-appauth"></a>Modyfikowanie aplikacji do użycia Azure AD B2C z AppAuth

> [!NOTE]
> AppAuth obsługuje interfejsy API systemu Android 16 (Jellybean) i nowsze. Zalecamy korzystanie z interfejsu API 23 lub nowszego.
>

### <a name="configuration"></a>Konfiguracja

Komunikację można skonfigurować przy użyciu Azure AD B2C, określając identyfikator URI odnajdowania lub określając zarówno punkt końcowy autoryzacji, jak i identyfikatory URI punktu końcowego tokenu. W obu przypadkach potrzebne są następujące informacje:

* Identyfikator dzierżawy (np. contoso.onmicrosoft.com)
* Nazwa przepływu użytkownika (np. B2C\_1\_)

W przypadku wybrania opcji automatycznego odnajdywania identyfikatorów URI punktów końcowych autoryzacji i tokenów należy pobrać informacje z identyfikatora URI odnajdywania. Identyfikator URI odnajdywania można wygenerować, zastępując identyfikator dzierżawy\_i nazwę zasady\_następującym adresem URL:

```java
String mDiscoveryURI = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/v2.0/.well-known/openid-configuration?p=<Policy_Name>";
```

Następnie można uzyskać autoryzację i identyfikatory URI punktu końcowego tokenu i utworzyć obiekt AuthorizationServiceConfiguration, uruchamiając następujące polecenie:

```java
final Uri issuerUri = Uri.parse(mDiscoveryURI);
AuthorizationServiceConfiguration config;

AuthorizationServiceConfiguration.fetchFromIssuer(
    issuerUri,
    new RetrieveConfigurationCallback() {
      @Override public void onFetchConfigurationCompleted(
          @Nullable AuthorizationServiceConfiguration serviceConfiguration,
          @Nullable AuthorizationException ex) {
        if (ex != null) {
            Log.w(TAG, "Failed to retrieve configuration for " + issuerUri, ex);
        } else {
            // service configuration retrieved, proceed to authorization...
        }
      }
  });
```

Zamiast odnajdywania w celu uzyskiwania adresów URI autoryzacji i tokenów, można je również jawnie określić, zastępując identyfikator dzierżawy\_i nazwę zasad\_w poniższym adresie URL:

```java
String mAuthEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/authorize?p=<Policy_Name>";

String mTokenEndpoint = "https://<Tenant_name>.b2clogin.com/<Tenant_ID>/oauth2/v2.0/token?p=<Policy_Name>";
```

Uruchom następujący kod, aby utworzyć obiekt AuthorizationServiceConfiguration:

```java
AuthorizationServiceConfiguration config =
        new AuthorizationServiceConfiguration(name, mAuthEndpoint, mTokenEndpoint);

// perform the auth request...
```

### <a name="authorizing"></a>Autoryzowanie

Po skonfigurowaniu lub pobraniu konfiguracji usługi autoryzacji można utworzyć żądanie autoryzacji. Aby można było utworzyć żądanie, potrzebne są następujące informacje:

* Identyfikator klienta (Identyfikator aplikacji), który został zarejestrowany wcześniej. Na przykład `00000000-0000-0000-0000-000000000000`.
* Niestandardowy identyfikator URI przekierowania, który został wcześniej zarejestrowany. Na przykład `com.onmicrosoft.contosob2c.exampleapp://oauth/redirect`.

Oba elementy powinny być zapisane podczas [rejestrowania aplikacji](#create-an-application).

```java
AuthorizationRequest req = new AuthorizationRequest.Builder(
    config,
    clientId,
    ResponseTypeValues.CODE,
    redirectUri)
    .build();
```

Zapoznaj się z [przewodnikiem AppAuth](https://openid.github.io/AppAuth-Android/) , aby dokończyć pozostałą część procesu. Jeśli musisz szybko rozpocząć pracę z działającą aplikacją, zapoznaj się z [naszym Przykładem](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c). Postępuj zgodnie z instrukcjami w [README.MD](https://github.com/Azure-Samples/active-directory-android-native-appauth-b2c/blob/master/README.md) , aby wprowadzić własną konfigurację Azure AD B2C.
