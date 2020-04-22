---
title: Dodawanie własnych atrybutów do zasad niestandardowych
titleSuffix: Azure AD B2C
description: Przewodnik dotyczący używania właściwości rozszerzenia i atrybutów niestandardowych i dołączania ich do interfejsu użytkownika.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b5990f79891a9cbc0d18c3499691a3d7ef309a73
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678270"
---
# <a name="azure-active-directory-b2c-enable-custom-attributes-in-a-custom-profile-policy"></a>Usługa Azure Active Directory B2C: włączanie atrybutów niestandardowych w zasadach profilu niestandardowego

W [artykule Dodaj oświadczenia i dostosuj dane wejściowe użytkownika przy użyciu zasad niestandardowych](custom-policy-configure-user-input.md) dowiesz się, jak używać [wbudowanych atrybutów profilu użytkownika](user-profile-attributes.md). W tym artykule włączysz atrybut niestandardowy w katalogu usługi Azure Active Directory B2C (Azure AD B2C). Później można użyć nowego atrybutu jako oświadczenia niestandardowego w [przepływach użytkowników](user-flow-overview.md) lub [zasadach niestandardowych](custom-policy-get-started.md) jednocześnie.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Postępuj zgodnie z instrukcjami z artykułu [Usługa Azure Active Directory B2C: Wprowadzenie do zasad niestandardowych](custom-policy-get-started.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers"></a>Zbieranie informacji o klientach za pomocą atrybutów niestandardowych 

Katalog usługi Azure AD B2C jest wyposażony [we wbudowany zestaw atrybutów.](user-profile-attributes.md) Jednak często trzeba utworzyć własne atrybuty, aby zarządzać określonym scenariuszem, na przykład gdy:

* Aplikacja skierowana do klienta musi utrwalić atrybut **LoyaltyId.**
* Dostawca tożsamości ma unikatowy identyfikator użytkownika, **uniqueUserGUID**, który musi zostać utrwalony.
* Niestandardowa podróż użytkownika musi utrwalić stan użytkownika, **migrationStatus**, aby inna logika działała na.

Usługa Azure AD B2C umożliwia rozszerzenie zestawu atrybutów przechowywanych na każdym koncie użytkownika. Te atrybuty można również odczytywać i zapisywać za pomocą [interfejsu API programu Microsoft Graph](manage-user-accounts-graph-api.md).

## <a name="azure-ad-b2c-extensions-app"></a>Aplikacja rozszerzenia usługi Azure AD B2C

Atrybuty rozszerzenia można zarejestrować tylko w obiekcie aplikacji, nawet jeśli mogą zawierać dane dla użytkownika. Atrybut rozszerzenia jest dołączony do aplikacji o nazwie b2c-extensions-app. Nie należy modyfikować tej aplikacji, ponieważ jest ona używana przez usługę Azure AD B2C do przechowywania danych użytkownika. Tę aplikację można znaleźć w obszarze Rejestracje aplikacji usługi Azure AD B2C.

*Właściwość rozszerzenia*terminów, *atrybut niestandardowy*i oświadczenie *niestandardowe* odnoszą się do tego samego w kontekście tego artykułu. Nazwa różni się w zależności od kontekstu, takich jak aplikacja, obiekt lub zasady.

## <a name="get-the-application-properties"></a>Pobierz właściwości aplikacji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **subskrypcja katalog +** w górnym menu, a następnie wybierz katalog zawierający dzierżawę usługi Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Możesz też wybrać **wszystkie usługi** i wyszukać i wybrać pozycję Azure **AD B2C**.
1. Wybierz **pozycję Rejestracje aplikacji (Wersja zapoznawcza),** a następnie wybierz pozycję **Wszystkie aplikacje**.
1. Wybierz `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` aplikację.
1. Skopiuj następujące identyfikatory do schowka i zapisz je:
    * **Identyfikator aplikacji**. Przykład: `11111111-1111-1111-1111-111111111111`.
    * **Identyfikator obiektu**. Przykład: `22222222-2222-2222-2222-222222222222`.

## <a name="modify-your-custom-policy"></a>Modyfikowanie zasad niestandardowych

Aby włączyć atrybuty niestandardowe w zasadach, **podaj identyfikator aplikacji** i identyfikator **obiektu** aplikacji w metadanych profilu technicznego AAD-Common. Profil techniczny *AAD-Common* znajduje się w podstawowym profilu [technicznym usługi Azure Active Directory](active-directory-technical-profile.md) i zapewnia obsługę zarządzania użytkownikami usługi Azure AD. Inne profile techniczne usługi Azure AD obejmują AAD-Common, aby wykorzystać jego konfigurację. Zastąp profil techniczny AAD-Common w pliku rozszerzenia.

1. Otwórz plik rozszerzeń zasad. Na przykład <em> `SocialAndLocalAccounts/` </em>.
1. Znajdź ClaimsProviders element. Dodaj nowy ClaimsProvider do ClaimsProviders elementu.
1. Zastąp `ApplicationObjectId` identyfikator obiektu, który został wcześniej zarejestrowany. Następnie `ClientId` zastąp identyfikator aplikacji, który został wcześniej zarejestrowany we wtorkowym urywku.

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-Common">
          <Metadata>
            <!--Insert b2c-extensions-app application ID here, for example: 11111111-1111-1111-1111-111111111111-->  
            <Item Key="ClientId"></Item>
            <!--Insert b2c-extensions-app application ObjectId here, for example: 22222222-2222-2222-2222-222222222222-->
            <Item Key="ApplicationObjectId"></Item>
          </Metadata>
        </TechnicalProfile>
      </TechnicalProfiles> 
    </ClaimsProvider>
    ```

## <a name="upload-your-custom-policy"></a>Przekazywanie zasad niestandardowych

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD, wybierając filtr **subskrypcja Katalog +** w górnym menu i wybierając katalog zawierający dzierżawę usługi Azure AD B2C.
3. Wybierz **pozycję Wszystkie usługi** w lewym górnym rogu witryny Azure portal, a następnie wyszukaj i wybierz pozycję **Rejestracje aplikacji.**
4. Wybierz **platformę środowiska tożsamości**.
5. Wybierz **pozycję Przekaż zasady niestandardowe**, a następnie przekaż zmienione pliki zasad TrustFrameworkExtensions.xml.

> [!NOTE]
> Po raz pierwszy profil techniczny usługi Azure AD powtarza oświadczenie do katalogu, sprawdza, czy atrybut niestandardowy istnieje. Jeśli nie, tworzy atrybut niestandardowy.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Tworzenie atrybutu niestandardowego za pośrednictwem witryny Azure Portal

Te same atrybuty rozszerzenia są współużytkowane przez zasady wbudowane i niestandardowe. Po dodaniu atrybutów niestandardowych za pośrednictwem środowiska portalu, te atrybuty są rejestrowane przy użyciu **b2c-extensions-app,** który istnieje w każdej dzierżawy B2C.

Te atrybuty można utworzyć przy użyciu interfejsu użytkownika portalu przed lub po użyciu ich w zasadach niestandardowych. Postępuj zgodnie ze wskazówkami dotyczącymi [definiowania atrybutów niestandardowych w usłudze Azure Active Directory B2C](user-flow-custom-attributes.md). Podczas tworzenia atrybut **loyaltyId** w portalu, należy odwołać się do niego w następujący sposób:

|Nazwa     |Stosowany w |
|---------|---------|
|`extension_loyaltyId`  | Zasady niestandardowe|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Interfejs API programu Microsoft Graph](manage-user-accounts-graph-api.md)|

W poniższym przykładzie pokazano użycie atrybutów niestandardowych w definicji oświadczeń zasad niestandardowych usługi Azure AD B2C.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="extension_loyaltyId">
      <DisplayName>Loyalty Identification</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your loyalty number from your membership card</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

W poniższym przykładzie pokazano użycie atrybutu niestandardowego w zasadach niestandardowych usługi Azure AD B2C w profilu technicznym, danych wejściowych, danych wyjściowych i utrwalonych oświadczeń.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="extension_loyaltyId"  />
</InputClaims>
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
</PersistedClaims>
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
</OutputClaims>
```

## <a name="use-a-custom-attribute-in-a-policy"></a>Używanie atrybutu niestandardowego w zasadach

Postępuj zgodnie ze wskazówkami dotyczącymi [dodawania oświadczeń i dostosowywania danych wejściowych użytkownika przy użyciu zasad niestandardowych](custom-policy-configure-user-input.md). W tym przykładzie użyto wbudowanego oświadczenia "miasto". Aby użyć atrybutu niestandardowego, zastąp "miasto" własnymi atrybutami niestandardowymi.


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usługach:

- [Atrybuty profilu użytkownika usługi Azure AD B2C](user-profile-attributes.md)
- [Definicja atrybutów rozszerzenia](user-profile-attributes.md#extension-attributes)
- [Zarządzanie kontami użytkowników usługi Azure AD B2C za pomocą programu Microsoft Graph](manage-user-accounts-graph-api.md)
