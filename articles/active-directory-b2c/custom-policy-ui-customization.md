---
title: Dostosowywanie interfejsu użytkownika aplikacji przy użyciu zasad niestandardowych
titleSuffix: Azure AD B2C
description: Dowiedz się więcej o dostosowywaniu interfejsu użytkownika przy użyciu zasad niestandardowych w programie Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/16/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cb833ff35dae4fe1c0c27204ec66fa6b4cdb82c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388888"
---
# <a name="customize-the-user-interface-of-your-application-using-a-custom-policy-in-azure-active-directory-b2c"></a>Dostosuj interfejs użytkownika aplikacji przy użyciu zasad niestandardowych w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Wykonanie kroków opisanych w tym artykule spowoduje utworzenie zasad tworzenia konta i logowania przy użyciu marki i wyglądu. Dzięki Azure Active Directory B2C (Azure AD B2C) uzyskujesz niemal pełną kontrolę nad zawartością HTML i CSS prezentowaną użytkownikom. W przypadku korzystania z zasad niestandardowych można skonfigurować Dostosowywanie interfejsu użytkownika w formacie XML zamiast korzystać z formantów w Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonaj kroki opisane w temacie Wprowadzenie [do zasad niestandardowych](custom-policy-get-started.md). Należy mieć działającą niestandardową zasadę do rejestracji i logowania przy użyciu kont lokalnych.

[!INCLUDE [active-directory-b2c-html-how-to](../../includes/active-directory-b2c-html-how-to.md)]

### <a name="4-modify-the-extensions-file"></a>4. Zmodyfikuj plik rozszerzeń.

Aby skonfigurować dostosowanie interfejsu użytkownika, skopiuj **ContentDefinition** i jego elementy podrzędne z pliku podstawowego do pliku rozszerzeń.

1. Otwórz podstawowy plik zasad. Na przykład <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em> . Ten plik podstawowy jest jednym z plików zasad zawartych w pakiecie startowym zasad niestandardowych, który powinien zostać uzyskany w wymaganiu wstępnym, [Rozpocznij od zasad niestandardowych](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom).
1. Wyszukaj i Skopiuj całą zawartość elementu **ContentDefinitions** .
1. Otwórz plik rozszerzenia. Na przykład *TrustFrameworkExtensions.xml*. Wyszukaj element **BuildingBlocks** . Jeśli element nie istnieje, Dodaj go.
1. Wklej całą zawartość elementu **ContentDefinitions** , który został skopiowany jako element podrzędny elementu **BuildingBlocks** .
1. Wyszukaj element **ContentDefinition** , który zawiera `Id="api.signuporsignin"` kod XML, który został skopiowany.
1. Zmień wartość **LoadUri** na adres URL pliku HTML, który został przekazany do magazynu. Na przykład `https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html`.

    Zasady niestandardowe powinny wyglądać podobnie do następującego fragmentu kodu:

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <LoadUri>https://your-storage-account.blob.core.windows.net/your-container/customize-ui.html</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.0.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

1. Zapisz plik rozszerzeń.

### <a name="5-upload-and-test-your-updated-custom-policy"></a>5. przekazanie i przetestowanie zaktualizowanych zasad niestandardowych

#### <a name="51-upload-the-custom-policy"></a>5,1 przekazywanie zasad niestandardowych

1. Upewnij się, że używasz katalogu, który zawiera dzierżawę Azure AD B2C, wybierając pozycję **katalog i subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę.
1. Wyszukaj i wybierz **Azure AD B2C**.
1. W obszarze **zasady**wybierz pozycję **platforma obsługi tożsamości**.
1. Wybierz pozycję **Przekaż zasady niestandardowe**.
1. Przekaż wcześniej zmieniony plik rozszerzeń.

#### <a name="52-test-the-custom-policy-by-using-run-now"></a>5,2 testowanie zasad niestandardowych za pomocą polecenia **Uruchom teraz**

1. Wybierz przekazane zasady, a następnie wybierz pozycję **Uruchom teraz**.
1. Należy mieć możliwość rejestrowania się przy użyciu adresu e-mail.

[!INCLUDE [active-directory-b2c-html-templates](../../includes/active-directory-b2c-html-templates.md)]

## <a name="configure-dynamic-custom-page-content-uri"></a>Skonfiguruj dynamiczny Identyfikator URI zawartości strony niestandardowej

Za pomocą Azure AD B2C zasad niestandardowych można wysłać parametr w ścieżce URL lub ciągu zapytania. Przekazanie parametru do punktu końcowego HTML pozwala na dynamiczną zmianę zawartość strony. Na podstawie parametru przekazywanego z aplikacji internetowej lub aplikacji mobilnej można na przykład zmienić obraz tła na stronie rejestracji lub logowania usługi Azure AD B2C. Parametr może być dowolnymi [mechanizmami rozwiązywania konfliktów](claim-resolver-overview.md), takimi jak identyfikator aplikacji, identyfikator języka lub niestandardowy parametr ciągu zapytania, taki jak `campaignId` .

### <a name="sending-query-string-parameters"></a>Wysyłanie parametrów ciągu zapytania

Aby wysłać parametry ciągu zapytania, w [zasadach jednostki uzależnionej](relyingparty.md)Dodaj `ContentDefinitionParameters` element, jak pokazano poniżej.

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <UserJourneyBehaviors>
    <ContentDefinitionParameters>
        <Parameter Name="campaignId">{OAUTH-KV:campaignId}</Parameter>
        <Parameter Name="lang">{Culture:LanguageName}</Parameter>
        <Parameter Name="appId">{OIDC:ClientId}</Parameter>
    </ContentDefinitionParameters>
    </UserJourneyBehaviors>
    ...
</RelyingParty>
```

W definicji zawartości Zmień wartość `LoadUri` na `https://<app_name>.azurewebsites.net/home/unified` . Zasady niestandardowe `ContentDefinition` powinny wyglądać podobnie do następującego fragmentu kodu:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://<app_name>.azurewebsites.net/home/unified</LoadUri>
  ...
</ContentDefinition>
```

Gdy Azure AD B2C ładuje stronę, wywołuje punkt końcowy serwera sieci Web:

```http
https://<app_name>.azurewebsites.net/home/unified?campaignId=123&lang=fr&appId=f893d6d3-3b6d-480d-a330-1707bf80ebea
```

### <a name="dynamic-page-content-uri"></a>Identyfikator URI zawartości strony dynamicznej

Zawartość można ściągnąć z różnych miejsc na podstawie używanych parametrów. W punkcie końcowym z obsługą mechanizmu CORS Skonfiguruj strukturę folderów, aby hostować zawartość. Można na przykład zorganizować zawartość w następującej strukturze. *Folder główny/folder na język/pliki HTML*. Na przykład identyfikator URI strony niestandardowej może wyglądać następująco:

```xml
<ContentDefinition Id="api.signuporsignin">
  <LoadUri>https://contoso.blob.core.windows.net/{Culture:LanguageName}/myHTML/unified.html</LoadUri>
  ...
</ContentDefinition>
```

Azure AD B2C wysyła dwuliterowy kod ISO dla języka w `fr` języku francuskim:

```http
https://contoso.blob.core.windows.net/fr/myHTML/unified.html
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat elementów interfejsu użytkownika, które można dostosować, zobacz [Przewodnik referencyjny dotyczący dostosowywania interfejsu użytkownika dla przepływów użytkowników](customize-ui-overview.md).
