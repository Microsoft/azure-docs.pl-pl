---
title: Definiowanie atrybutów niestandardowych w Azure Active Directory B2C | Microsoft Docs
description: Zdefiniuj atrybuty niestandardowe dla aplikacji w Azure Active Directory B2C, aby zbierać informacje o klientach.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 5552c93c1c65f08f70ed8929d81126035aa2a357
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98661208"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Definiowanie atrybutów niestandardowych w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

W artykule [Dodawanie oświadczeń i dostosowywanie danych wejściowych użytkownika przy użyciu zasad niestandardowych](configure-user-input.md) dowiesz się, jak używać wbudowanych [atrybutów profilu użytkownika](user-profile-attributes.md). W tym artykule opisano włączenie atrybutu niestandardowego w katalogu Azure Active Directory B2C (Azure AD B2C). Później można użyć nowego atrybutu jako niestandardowego żądania w [przepływach użytkownika](user-flow-overview.md) lub [niestandardowych zasadach](custom-policy-get-started.md) .

Katalog Azure AD B2C zawiera [Wbudowany zestaw atrybutów](user-profile-attributes.md). Jednak często trzeba utworzyć własne atrybuty do zarządzania określonym scenariuszem, na przykład:

* Aplikacja dołączona do klienta musi zachować atrybut **LoyaltyId** .
* Dostawca tożsamości ma unikatowy identyfikator użytkownika ( **uniqueUserGUID**), który musi zostać utrwalony.
* Niestandardowa podróż użytkownika wymaga utrzymania stanu użytkownika ( **migrationStatus**), w którym ma działać inna logika.

Azure AD B2C umożliwia rozbudowa zestawu atrybutów przechowywanych na poszczególnych kontach użytkowników. Można również odczytywać i zapisywać te atrybuty przy użyciu [interfejsu API Microsoft Graph](microsoft-graph-operations.md).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-custom-attribute"></a>Tworzenie atrybutu niestandardowego

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/) jako administrator globalny dzierżawy usługi Azure AD B2C.
1. Upewnij się, że używasz katalogu, który zawiera Twoją dzierżawę usługi Azure AD B2C, przełączając się na niego w prawym górnym rogu witryny Azure Portal. Wybierz informacje o subskrypcji, a następnie wybierz pozycję **Przełącz katalog**.
1. Wybierz pozycję **Wszystkie usługi** w lewym górnym rogu witryny Azure Portal, a następnie wyszukaj i wybierz usługę **Azure AD B2C**.
1. Wybierz pozycję **atrybuty użytkownika**, a następnie wybierz pozycję **Dodaj**.
1. Podaj **nazwę** dla atrybutu niestandardowego (na przykład "ShoeSize")
1. Wybierz **Typ danych**. Dostępne są tylko **ciągi**, **wartości logiczne** i **int** .
1. Opcjonalnie wprowadź **Opis** dla celów informacyjnych.
1. Kliknij pozycję **Utwórz**.

Atrybut niestandardowy jest teraz dostępny na liście **atrybutów użytkownika** i do użycia w przepływach użytkownika. Atrybut niestandardowy jest tworzony tylko za pierwszym razem, gdy jest używany w dowolnym przepływie użytkownika, a nie po dodaniu go do listy **atrybutów użytkownika**.

::: zone pivot="b2c-user-flow"

## <a name="use-a-custom-attribute-in-your-user-flow"></a>Używanie atrybutu niestandardowego w przepływie użytkownika

1. W dzierżawie Azure AD B2C wybierz pozycję **przepływy użytkownika**.
1. Wybierz zasady (na przykład "B2C_1_SignupSignin"), aby je otworzyć.
1. Wybierz pozycję **atrybuty użytkownika** , a następnie wybierz atrybut niestandardowy (na przykład "ShoeSize"). Kliknij pozycję **Zapisz**.
1. Wybierz pozycję **oświadczenia aplikacji** , a następnie wybierz atrybut niestandardowy.
1. Kliknij pozycję **Zapisz**.

Po utworzeniu nowego użytkownika przy użyciu przepływu użytkownika, który używa nowo utworzonego atrybutu niestandardowego, obiekt może być badany w [eksploratorze Microsoft Graph](https://developer.microsoft.com/graph/graph-explorer). Alternatywnie możesz użyć funkcji [Uruchom przepływ użytkownika](./tutorial-create-user-flows.md) w przepływie użytkownika, aby zweryfikować środowisko klienta. Na liście atrybutów zebranych podczas rejestracji można teraz zobaczyć **ShoeSize** i zobaczyć je w tokenie wysyłanym z powrotem do aplikacji.

::: zone-end

## <a name="azure-ad-b2c-extensions-app"></a>Aplikacja rozszerzeń Azure AD B2C

Atrybuty rozszerzenia mogą być rejestrowane tylko w obiekcie aplikacji, nawet jeśli mogą zawierać dane dla użytkownika. Atrybut rozszerzenia jest dołączany do aplikacji o nazwie B2C-Extensions-App. Nie należy modyfikować tej aplikacji, ponieważ jest ona używana przez Azure AD B2C do przechowywania danych użytkownika. Tę aplikację można znaleźć w obszarze Azure AD B2C, rejestracje aplikacji.

*Właściwość rozszerzenia* terminów, *atrybut niestandardowy* i *niestandardowa* , odwołują się do tego samego elementu w kontekście tego artykułu. Nazwa różni się w zależności od kontekstu, takiego jak aplikacja, obiekt lub zasady.

## <a name="get-the-application-properties"></a>Pobierz właściwości aplikacji

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W menu po lewej stronie wybierz pozycję **Azure AD B2C**. Lub wybierz pozycję **wszystkie usługi** i Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. Wybierz pozycję **rejestracje aplikacji**, a następnie wybierz pozycję **wszystkie aplikacje**.
1. Wybierz aplikację `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.`.
1. Skopiuj następujące identyfikatory do schowka i Zapisz je:
    * **Identyfikator aplikacji**. Przykład: `11111111-1111-1111-1111-111111111111`.
    * **Identyfikator obiektu**. Przykład: `22222222-2222-2222-2222-222222222222`.

## <a name="using-custom-attribute-with-ms-graph-api"></a>Używanie atrybutu niestandardowego z usługą MS interfejs API programu Graph

Interfejs API Microsoft Graph obsługuje tworzenie i aktualizowanie użytkownika z atrybutami rozszerzenia. Atrybuty rozszerzenia w interfejs API programu Graph są nazwane przy użyciu konwencji, w `extension_ApplicationClientID_attributename` której `ApplicationClientID` jest **Identyfikator aplikacji (klienta)** `b2c-extensions-app` . Należy zauważyć, że **Identyfikator aplikacji (klienta)** , która jest reprezentowana w nazwie atrybutu rozszerzenia, nie zawiera łączników. Przykład:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
``` 

::: zone pivot="b2c-custom-policy"

## <a name="modify-your-custom-policy"></a>Modyfikowanie zasad niestandardowych

Aby włączyć atrybuty niestandardowe w zasadach, podaj **Identyfikator aplikacji** i **Identyfikator obiektu** aplikacji w AAD-Common metadanych profilu technicznego. Profil techniczny usługi *AAD — wspólny* jest dostępny w podstawowym [Azure Active Directory](active-directory-technical-profile.md) profilu technicznym i zapewnia obsługę zarządzania użytkownikami w usłudze Azure AD. Inne profile techniczne usługi Azure AD obejmują AAD-Common, aby wykorzystać jego konfigurację. Zastąp profil techniczny AAD-Common w pliku rozszerzenia.

1. Otwórz plik rozszerzeń zasad. Na przykład <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Znajdź element ClaimsProviders. Dodaj nowy ClaimsProvider do elementu ClaimsProviders.
1. Zamień `ApplicationObjectId` na identyfikator obiektu, który został wcześniej zarejestrowany. Następnie zastąp ciąg `ClientId` identyfikatorem aplikacji, który został wcześniej zarejestrowany w poniższym fragmencie kodu.

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
2. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD, wybierając filtr **katalog + subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawcę Azure AD B2C.
3. Wybierz pozycję **wszystkie usługi** w lewym górnym rogu Azure Portal, a następnie wyszukaj i wybierz pozycję **rejestracje aplikacji**.
4. Wybierz pozycję **platforma obsługi tożsamości**.
5. Wybierz pozycję **Przekaż zasady niestandardowe**, a następnie Przekaż zmienione pliki zasad TrustFrameworkExtensions.xml.

> [!NOTE]
> Pierwszy raz, gdy profil techniczny usługi Azure AD utrzymuje to zgłoszenie do katalogu, sprawdza, czy istnieje atrybut niestandardowy. Jeśli nie, tworzy atrybut niestandardowy.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Tworzenie niestandardowego atrybutu za pomocą Azure Portal

Te same atrybuty rozszerzenia są współdzielone między wbudowanymi i niestandardowymi zasadami. Po dodaniu atrybutów niestandardowych za pośrednictwem środowiska portalu te atrybuty są rejestrowane przy użyciu **B2C-Extensions-App** , która istnieje w każdej dzierżawie B2C.

Te atrybuty można utworzyć przy użyciu interfejsu użytkownika portalu przed lub po ich użyciu w niestandardowych zasadach. Podczas tworzenia atrybutu **loyaltyId** w portalu należy odwołać się do niego w następujący sposób:

|Nazwa     |Używany w |
|---------|---------|
|`extension_loyaltyId`  | Zasady niestandardowe|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft Graph API](microsoft-graph-operations.md)|

Poniższy przykład demonstruje użycie atrybutów niestandardowych w Azure AD B2C definicji niestandardowego żądania zasad.

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

Poniższy przykład ilustruje użycie niestandardowego atrybutu w Azure AD B2C zasad niestandardowych w profilu technicznym, danych wejściowych, wyjściowych i utrwalonych oświadczeń.

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

::: zone-end

## <a name="next-steps"></a>Następne kroki

Postępuj zgodnie ze wskazówkami dotyczącymi [dodawania oświadczeń i dostosowywania danych wejściowych użytkownika przy użyciu zasad niestandardowych](configure-user-input.md). Ten przykład używa wbudowanego "miasto". Aby użyć atrybutu niestandardowego, Zastąp wartość "miasto" własnymi atrybutami niestandardowymi.