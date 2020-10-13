---
title: Samouczek konfigurowania Azure Active Directory B2C przy użyciu Experian
titleSuffix: Azure AD B2C
description: Dowiedz się, jak zintegrować uwierzytelnianie Azure AD B2C z usługą Experian w celu weryfikacji tożsamości i sprawdzania na podstawie atrybutów użytkownika w celu zapobiegania oszustwom.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/22/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: a88894bb7462e9ac3afd16d69ae820dd98543a5f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91259377"
---
# <a name="tutorial-for-configuring-experian-with-azure-active-directory-b2c"></a>Samouczek dotyczący konfigurowania Experian z Azure Active Directory B2C

W tym przykładowym samouczku przedstawiono wskazówki dotyczące integrowania Azure AD B2C z usługą [Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration). Experian oferuje różne rozwiązania, które można znaleźć [tutaj](https://www.experian.com/).

W tym przykładzie jest używana zintegrowana tożsamość cyfrowa Experian i ryzyko oszustwa **CrossCore** . CrossCore jest usługą weryfikacji identyfikatora, która jest używana do weryfikacji tożsamości użytkownika. Jest to analiza ryzyka oparta na kilku informacjach dostarczonych przez użytkownika podczas przepływu rejestracji. CrossCore służy do określenia, czy użytkownik powinien być uprawniony do dalszej rejestracji. Następujące atrybuty mogą być używane w analizie ryzyka CrossCore:

- Poczta e-mail
- Adres IP
- Imię
- Drugie imię
- Nazwisko
- Ulica i numer
- City (Miasto)
- Województwo
- Postal Code
- Kraj/region
- Numer telefonu

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, musisz:

- Subskrypcja usługi Azure AD. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).

- [Dzierżawa Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant) , która jest połączona z subskrypcją platformy Azure.

## <a name="scenario-description"></a>Opis scenariusza

Integracja Experian obejmuje następujące składniki:

- Azure AD B2C — serwer autoryzacji odpowiedzialny za Weryfikowanie poświadczeń użytkownika, znanego również jako dostawca tożsamości

- Experian — usługa Experian pobiera dane wejściowe użytkownika i weryfikuje tożsamość użytkownika

- Niestandardowy interfejs API REST — ten interfejs API implementuje integrację między Azure AD B2C a usługą Experian.

Na poniższym diagramie architektury przedstawiono implementację.

![zrzut ekranu przedstawiający diagram Experian-Architecture](media/partner-experian/experian-architecture-diagram.png)

|Krok | Opis |
|:-----| :-----------|
| 1. | Użytkownik dotarł do strony logowania. Użytkownik wybierze opcję rejestracji, aby utworzyć nowe konto i wprowadzić informacje na stronie. Azure AD B2C zbiera atrybuty użytkownika.
| 2. | Azure AD B2C wywołuje interfejs API warstwy środkowej i przekazuje atrybuty użytkownika.
| 3. | Interfejs API warstwy środkowej zbiera atrybuty użytkownika i przekształca je w format, który może zużywać interfejs API Experian. Następnie program wysyła ją do Experian.
| 4. | Experian zużywa informacje i przetwarza je w celu weryfikacji tożsamości użytkowników na podstawie analizy ryzyka. Następnie zwraca wynik do interfejsu API warstwy środkowej.
| 5. | Interfejs API warstwy środkowej przetwarza informacje i wysyła odpowiednie informacje w prawidłowym formacie JSON do Azure AD B2C.
| 6. | Azure AD B2C otrzymuje informacje z z interfejsu API platformy środkowej. Jeśli zostanie wyświetlona odpowiedź na awarię, zostanie wyświetlony komunikat o błędzie dla użytkownika. Jeśli zostanie wyświetlona odpowiedź o powodzeniu, użytkownik zostanie uwierzytelniony i zapisany w katalogu.

## <a name="onboard-with-experian"></a>Dołączanie do Experian

1. Aby utworzyć konto Experian, skontaktuj się z [Experian](https://www.experian.com/decision-analytics/account-opening-fraud/microsoft-integration)

2. Po utworzeniu konta otrzymasz informacje potrzebne do konfiguracji interfejsu API. W poniższych sekcjach opisano proces.

## <a name="configure-azure-ad-b2c-with-experian"></a>Konfigurowanie Azure AD B2C przy użyciu Experian

### <a name="part-1---deploy-the-api"></a>Część 1 — wdrażanie interfejsu API

Wdróż podany [kod interfejsu API](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/Experian/CrossCoreIntegrationApi/CrossCoreIntegrationApi.sln) w usłudze platformy Azure. Kod można opublikować z programu Visual Studio, postępując zgodnie z tymi [instrukcjami](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).

>[!NOTE]
>Aby skonfigurować usługę Azure AD przy użyciu wymaganych ustawień, należy podać adres URL wdrożonej usługi.

### <a name="part-2---deploy-the-client-certificate"></a>Część 2 — Wdrażanie certyfikatu klienta

Wywołanie interfejsu API Experian jest chronione przez certyfikat klienta. Ten certyfikat klienta będzie dostarczany przez Experian. Postępując zgodnie z instrukcjami wymienionymi w tym [dokumencie](https://docs.microsoft.com/azure/app-service/environment/certificates#private-client-certificate), należy przekazać certyfikat do usługi Azure App Service. W przykładowych zasadach są używane te klucze kroków w procesie:

- Przekazywanie certyfikatu

- Ustaw `WEBSITE_LOAD_ROOT_CERTIFICATES` klucz za pomocą odcisku palca certyfikatu.

### <a name="part-3---configure-the-api"></a>Część 3 — Konfigurowanie interfejsu API

Ustawienia aplikacji można [skonfigurować w usłudze App Service na platformie Azure](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings). Za pomocą tej metody ustawienia można bezpiecznie skonfigurować bez sprawdzania ich w repozytorium. Należy podać następujące ustawienia w interfejsie API REST:

| Ustawienia aplikacji | Element źródłowy | Uwagi |
| :-------- | :------------| :-----------|
|CrossCoreConfig: TenantId | Konfiguracja konta Experian |     |
|CrossCoreConfig:OrgCode | Konfiguracja konta Experian |     |
|CrossCore:ApiEndpoint |Konfiguracja konta Experian|  |
|CrossCore:ClientReference | Konfiguracja konta Experian | |
| CrossCore:ModelCode |Konfiguracja konta Experian|
| CrossCore:OrgCode | Konfiguracja konta Experian |
| CrossCore:SignatureKey  | Konfiguracja konta Experian |
| CrossCore: TenantId  | Konfiguracja konta Experian |
| CrossCore: CertificateThumbprint | Certyfikat Experian |
| BasicAuth:ApiUsername | Zdefiniuj nazwę użytkownika dla interfejsu API | Używane w konfiguracji ExtId |
| BasicAuth:ApiPassword | Definiowanie hasła dla interfejsu API | Używane w konfiguracji ExtId

### <a name="part-4---create-api-policy-keys"></a>Część 4 — Tworzenie kluczy zasad interfejsu API

Zapoznaj się z tym [dokumentem](https://docs.microsoft.com/azure/active-directory-b2c/secure-rest-api#add-rest-api-username-and-password-policy-keys) i Utwórz dwa klucze zasad — jeden dla nazwy użytkownika interfejsu API, a drugi dla hasła interfejsu API zdefiniowanego powyżej dla uwierzytelniania HTTP Basic.

>[!NOTE]
>Klucze do konfigurowania zasad będą potrzebne później.

### <a name="part-5---replace-the-configuration-values"></a>Część 5 — zastępowanie wartości konfiguracyjnych

W podanych [zasadach niestandardowych](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Experian/policy)Znajdź następujące symbole zastępcze i zastąp je odpowiednimi wartościami z Twojego wystąpienia

|                      Symbol zastępczy                       |                                   Zamień na wartość                                 |                   Przykład                    |
| ------------------------------------------------------ | -------------------------------------------------------------------------------- | -------------------------------------------- |
| {your_tenant_name}                                     | Krótka nazwa dzierżawy                                                           | "yourtenant" z yourtenant.onmicrosoft.com |
| {your_trustframeworkbase_policy}                       | Nazwa Azure AD B2C zasad TrustFrameworkBase                  | B2C_1A_experian_TrustFrameworkBase           |
| {your_tenant_IdentityExperienceFramework_appid}        | Identyfikator aplikacji IdentityExperienceFramework skonfigurowanej w dzierżawie Azure AD B2C      | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_tenant_ ProxyIdentityExperienceFramework _appid} | Identyfikator aplikacji ProxyIdentityExperienceFramework skonfigurowanej w dzierżawie Azure AD B2C | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_tenant_extensions_appid}                         | Identyfikator aplikacji magazynu Twojej dzierżawy                                      | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_tenant_extensions_app_objectid}                  | Identyfikator obiektu aplikacji magazynu dzierżawy                                   | 01234567-89ab-cdef-0123-456789ABCDEF         |
| {your_api_username_key_name}                           | Nazwa klucza username utworzonego w [tym miejscu](#part-4---create-api-policy-keys)             | B2C \_ 1a \_ RestApiUsername                     |
| {your_api_password_key_name}                           | Nazwa klucza hasła utworzonego w [tym miejscu](#part-4---create-api-policy-keys)             | B2C \_ 1a \_ RestApiPassword                     |
| {your_app_service_URL}                                 | Adres URL skonfigurowanej usługi App Service                                             | `https://yourapp.azurewebsites.net`          |

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>Część 6 — Konfigurowanie zasad Azure AD B2C

Zapoznaj się z tym [dokumentem](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack) , aby uzyskać instrukcje dotyczące sposobu konfigurowania dzierżawy usługi Azure AD B2C i konfigurowania zasad.

>[!NOTE]
>Te przykładowe zasady bazują na [lokalnych kontach z pakietem startowym](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts).

>[!NOTE]
> Najlepszym rozwiązaniem jest, aby klienci mogli dodać powiadomienie o zgodzie na stronie kolekcji atrybutów. Powiadom użytkowników, że informacje będą wysyłane do usług innych firm w celu weryfikacji tożsamości.

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Otwórz dzierżawcę Azure AD B2C i w obszarze zasady wybierz pozycję **przepływy użytkownika**.

2. Wybierz wcześniej utworzony **przepływ użytkownika**.

3. Wybierz pozycję **Uruchom przepływ użytkownika** i wybierz ustawienia:

   a. **Aplikacja**: wybór zarejestrowanej aplikacji (przykład: JWT)

   b. **Adres URL odpowiedzi**: Wybierz **adres URL przekierowania**

   c. Wybierz pozycję **Uruchom przepływ użytkownika**.

4. Przejdź przez przepływ rejestracji i Utwórz konto

5. Wyloguj

6. Przejdź przez przepływ logowania  

7. CrossCore układanki zostanie wyświetlona po wprowadzeniu **dalej**.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje, zapoznaj się z następującymi artykułami:

- [Zasady niestandardowe w usłudze Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Wprowadzenie do zasad niestandardowych w Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
