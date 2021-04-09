---
title: Konfigurowanie przepływu logowania
titleSuffix: Azure Active Directory B2C
description: Dowiedz się, jak skonfigurować przepływ logowania w Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/04/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 2310bd39c39036b6d6ac919517fa5539d7b70779
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104581868"
---
# <a name="set-up-a-sign-in-flow-in-azure-active-directory-b2c"></a>Konfigurowanie przepływu logowania w Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="sign-in-flow-overview"></a>Omówienie przepływu logowania

Zasady logowania umożliwiają użytkownikom: 

* Użytkownicy mogą logować się przy użyciu konta lokalnego Azure AD B2C
* Rejestracja lub logowanie przy użyciu konta społecznościowego
* Resetowanie hasła
* Użytkownicy nie mogą zarejestrować się w celu uzyskania konta lokalnego Azure AD B2C. Aby utworzyć konto, administrator może używać [Azure Portal](manage-users-portal.md#create-a-consumer-user)lub [MS interfejs API programu Graph](microsoft-graph-operations.md).

![Przepływ edycji profilu](./media/add-sign-in-policy/sign-in-user-flow.png)

## <a name="prerequisites"></a>Wymagania wstępne

Jeśli jeszcze tego nie zrobiono, [zarejestruj aplikację sieci Web w Azure Active Directory B2C](tutorial-register-applications.md).

::: zone pivot="b2c-user-flow"

## <a name="create-a-sign-in-user-flow"></a>Tworzenie przepływu użytkownika logowania

Aby dodać zasady logowania:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Na pasku narzędzi portalu wybierz ikonę **katalog i subskrypcję** , a następnie wybierz katalog zawierający dzierżawę Azure AD B2C.
1. W Azure Portal Wyszukaj i wybierz pozycję **Azure AD B2C**.
1. W obszarze **zasady** wybierz pozycję **przepływy użytkowników**, a następnie wybierz pozycję **Nowy przepływ użytkownika**.
1. Na stronie **Tworzenie przepływu użytkownika** wybierz przepływ użytkownika **Logowanie** .
1. W obszarze **Wybierz wersję** wybierz pozycję **zalecane**, a następnie wybierz pozycję **Utwórz**. ([Dowiedz się więcej](user-flow-versions.md) o wersjach przepływu użytkowników).
1. Wprowadź **nazwę** przepływu użytkownika. Na przykład *signupsignin1*.
1. W przypadku **dostawców tożsamości** wybierz pozycję **Logowanie za pośrednictwem poczty e-mail**.
1. W przypadku **oświadczeń aplikacji** wybierz oświadczenia i atrybuty, które chcesz wysłać do aplikacji. Na przykład wybierz pozycję **Pokaż więcej**, a następnie wybierz atrybuty i oświadczenia dla **nazwy wyświetlanej** **, imię** i  **nazwisko** oraz **Identyfikator obiektu użytkownika**. Kliknij przycisk **OK**.
1. Kliknij przycisk **Utwórz** , aby dodać przepływ użytkownika. Prefiks *B2C_1* jest automatycznie dołączany do nazwy.

### <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Wybierz utworzony przepływ użytkownika, aby otworzyć jego stronę przeglądu, a następnie wybierz pozycję **Uruchom przepływ użytkownika**.
1. W przypadku **aplikacji** wybierz aplikację sieci Web o nazwie *webapp1* , która została wcześniej zarejestrowana. Powinien być pokazywany **adres URL odpowiedzi** `https://jwt.ms` .
1. Kliknij pozycję **Uruchom przepływ użytkownika**.
1. Użytkownik powinien mieć możliwość zalogowania się przy użyciu utworzonego konta (przy użyciu programu MS interfejs API programu Graph) bez linku do rejestracji. Zwrócony token zawiera wybrane oświadczenia.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="remove-the-sign-up-link"></a>Usuwanie linku do rejestracji

Profil techniczny **SelfAsserted-LocalAccountSignin-email** jest [automatycznie potwierdzony](self-asserted-technical-profile.md), który jest wywoływany podczas przepływu rejestracji lub logowania. Aby usunąć łącze do rejestracji, należy ustawić `setting.showSignupLink` metadane `false` . Zastąp profile techniczne SelfAsserted-LocalAccountSignin-email w pliku rozszerzenia. 

1. Otwórz plik rozszerzeń zasad. Na przykład _`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**_ .
1. Znajdź `ClaimsProviders` element. Jeśli element nie istnieje, Dodaj go.
1. Dodaj następującego dostawcę oświadczeń do `ClaimsProviders` elementu:

    ```xml
    <!--
    <ClaimsProviders> -->
      <ClaimsProvider>
        <DisplayName>Local Account</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
            <Metadata>
              <Item Key="setting.showSignupLink">false</Item>
            </Metadata>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    <!--
    </ClaimsProviders> -->
    ```

1. W obszarze `<BuildingBlocks>` elementu Dodaj następujący [ContentDefinition](contentdefinitions.md) , aby odwołać się do wersji 1.2.0 lub nowszego identyfikatora URI danych:

    ```XML
    <!-- 
    <BuildingBlocks> 
      <ContentDefinitions>-->
        <ContentDefinition Id="api.localaccountsignup">
          <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:1.2.0</DataUri>
        </ContentDefinition>
      <!--
      </ContentDefinitions>
    </BuildingBlocks> -->
    ```

## <a name="update-and-test-your-policy"></a>Aktualizowanie i testowanie zasad

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Upewnij się, że używasz katalogu, który zawiera dzierżawę usługi Azure AD, wybierając filtr **katalog + subskrypcja** w górnym menu i wybierając katalog zawierający dzierżawę usługi Azure AD.
1. Wybierz pozycję **wszystkie usługi** w lewym górnym rogu Azure Portal, a następnie wyszukaj i wybierz pozycję **rejestracje aplikacji**.
1. Wybierz pozycję **platforma obsługi tożsamości**.
1. Wybierz pozycję **Przekaż zasady niestandardowe**, a następnie Przekaż zmieniony plik zasad, *TrustFrameworkExtensions.xml*.
1. Wybierz przekazane zasady logowania, a następnie kliknij przycisk **Uruchom teraz** .
1. Użytkownik powinien mieć możliwość zalogowania się przy użyciu utworzonego konta (przy użyciu programu MS interfejs API programu Graph) bez linku do rejestracji.

::: zone-end

## <a name="next-steps"></a>Następne kroki

* Dodaj [Logowanie przy użyciu dostawcy tożsamości społecznościowej](add-identity-provider.md).
* Skonfiguruj [przepływ resetowania hasła](add-password-reset-policy.md).
