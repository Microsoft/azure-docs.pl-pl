---
title: Eksportowanie interfejsu API hostowanego na platformie Azure do usługi PowerApps i Microsoft Flow
description: Omówienie sposobu uwidaczniania interfejsu API hostowanego w App Service w usłudze PowerApps i Microsoft Flow
ms.topic: conceptual
ms.date: 04/28/2020
ms.reviewer: sunayv
ms.openlocfilehash: 359f19598d5f1dff6cda4b303d33ed721ed72d97
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92054801"
---
# <a name="exporting-an-azure-hosted-api-to-powerapps-and-microsoft-flow"></a>Eksportowanie interfejsu API hostowanego na platformie Azure do usługi PowerApps i Microsoft Flow

[Powerapps](https://powerapps.microsoft.com/guided-learning/learning-introducing-powerapps/) to usługa służąca do kompilowania i używania niestandardowych aplikacji firmowych, które łączą się z danymi i pracują na różnych platformach. [Automatyzacja](/learn/modules/get-started-with-flow/index) zapewnia łatwe Automatyzowanie przepływów pracy i procesów biznesowych między ulubionymi aplikacjami i usługami. W przypadku usług PowerApps i Microsoft Flow są dostępne różne wbudowane łączniki ze źródłami danych, takimi jak Office 365, Dynamics 365, Salesforce i nie tylko. W niektórych przypadkach konstruktory aplikacji i przepływu chcą również łączyć się ze źródłami danych i interfejsami API utworzonymi przez ich organizację.

Podobnie deweloperzy, którzy chcą uwidocznić interfejsy API w organizacji, mogą udostępnić interfejsy API aplikacji i konstruktorów przepływów. W tym artykule opisano sposób eksportowania interfejsu API skompilowanego za pomocą [Azure Functions](../azure-functions/functions-overview.md) lub [Azure App Service](../app-service/overview.md). Wyeksportowany interfejs API jest *łącznikiem niestandardowym*, który jest używany w usłudze PowerApps i Microsoft Flow podobnie jak wbudowany łącznik.

> [!IMPORTANT]
> Funkcja definicji interfejsu API wyświetlana w tym artykule jest obsługiwana tylko w przypadku [wersji 1. x środowiska uruchomieniowego Azure Functions](functions-versions.md#creating-1x-apps) i aplikacji App Services. Wersja 2. x funkcji integruje się z API Management, aby tworzyć i obsługiwać definicje OpenAPI. Aby dowiedzieć się więcej, zobacz [Tworzenie definicji openapi dla funkcji za pomocą usługi Azure API Management](functions-openapi-definition.md). 

## <a name="create-and-export-an-api-definition"></a>Tworzenie i eksportowanie definicji interfejsu API
Przed wyeksportowaniem interfejsu API należy opisać interfejs API przy użyciu definicji OpenAPI (wcześniej znanej jako plik [struktury Swagger](https://swagger.io/) ). Ta definicja zawiera informacje o operacjach dostępnych w interfejsie API i wymaganej strukturze danych żądań i odpowiedzi dla interfejsu API. Usługi PowerApps i Microsoft Flow mogą tworzyć łączniki niestandardowe dla dowolnej definicji OpenAPI 2,0. Azure Functions i Azure App Service mają wbudowaną obsługę tworzenia i obsługiwania definicji OpenAPI oraz zarządzania nimi. Aby uzyskać więcej informacji, zobacz [hostowanie interfejsu API RESTful z mechanizmem CORS w Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

> [!NOTE]
> Można również tworzyć łączniki niestandardowe w interfejsie użytkownika usługi PowerApps i Microsoft Flow bez użycia definicji OpenAPI. Aby uzyskać więcej informacji, zobacz [Rejestrowanie i używanie łącznika niestandardowego (powerapps)](https://powerapps.microsoft.com/tutorials/register-custom-api/) oraz [Rejestrowanie i używanie łącznika niestandardowego (Microsoft Flow)](/power-automate/developer/register-custom-api).

Aby wyeksportować definicję interfejsu API, wykonaj następujące kroki:

1. W [Azure Portal](https://portal.azure.com)przejdź do aplikacji funkcji lub aplikacji App Service.

    W menu po lewej stronie w obszarze **interfejs API**wybierz pozycję **definicja interfejsu API**.

    :::image type="content" source="media/app-service-export-api-to-powerapps-and-flow/api-definition-function.png" alt-text="Definicja interfejsu API Azure Functions":::

2. Przycisk **Eksportuj do usługi powerapps i Microsoft Flow** powinien być dostępny (jeśli nie, musisz najpierw utworzyć definicję openapi). Wybierz ten przycisk, aby rozpocząć proces eksportowania.

    ![Przycisk Eksportuj do usługi PowerApps i Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-apps-flow.png)

3. Wybierz **tryb eksportu**:

    Program **Express** umożliwia utworzenie łącznika niestandardowego na podstawie Azure Portal. Wymaga zalogowania się w usłudze PowerApps lub Microsoft Flow i ma uprawnienia do tworzenia łączników w środowisku docelowym. Takie podejście jest zalecane, jeśli można spełnić te dwa wymagania. W przypadku korzystania z tego trybu postępuj zgodnie z poniższymi instrukcjami dotyczącymi [eksportu](#express) .

    Opcja **Ręczna** umożliwia wyeksportowanie definicji interfejsu API, która zostanie następnie zaimportowana przy użyciu portali usługi PowerApps lub Microsoft Flow. Ta metoda jest zalecana w przypadku, gdy użytkownik platformy Azure i użytkownik z uprawnieniami do tworzenia łączników są różnymi osobami lub jeśli łącznik musi zostać utworzony w innej dzierżawie platformy Azure. W przypadku korzystania z tego trybu postępuj zgodnie z poniższymi instrukcjami dotyczącymi [eksportowania ręcznego](#manual) .

    ![Tryb eksportu](media/app-service-export-api-to-powerapps-and-flow/export-mode.png)

> [!NOTE]
> Łącznik niestandardowy używa *kopii* definicji interfejsu API, dzięki czemu usługa PowerApps i Microsoft Flow nie będą od razu wiedzieć, czy wprowadzisz zmiany w aplikacji i jej definicji interfejsu API. Jeśli wprowadzisz zmiany, powtórz kroki eksportowania dla nowej wersji.

<a name="express"></a>
## <a name="use-express-export"></a>Użyj eksportu ekspresowego

Aby zakończyć eksport w trybie **Express** , wykonaj następujące kroki:

1. Upewnij się, że zalogowano się do dzierżawy usługi PowerApps lub Microsoft Flow, do której chcesz wyeksportować. 

2. Użyj ustawień określonych w tabeli.

    |Ustawienie|Opis|
    |--------|------------|
    |**Środowisko**|Wybierz środowisko, w którym ma zostać zapisany łącznik niestandardowy. Aby uzyskać więcej informacji, zobacz [Environments overview](https://powerapps.microsoft.com/tutorials/environments-overview/) (Omówienie środowisk).|
    |**Nazwa niestandardowego interfejsu API**|Wprowadź nazwę, która będzie widoczna dla aplikacji PowerApps i Microsoft Flow builds na liście łączników.|
    |**Przygotuj konfigurację zabezpieczeń**|W razie potrzeby podaj szczegóły konfiguracji zabezpieczeń potrzebne do udzielenia użytkownikom dostępu do interfejsu API. Ten przykład przedstawia klucz interfejsu API. Aby uzyskać więcej informacji, zobacz [Określ typ uwierzytelniania](#auth) poniżej.|
 
    ![Eksportowanie do usługi PowerApps i Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-express.png)

3. Kliknij przycisk **OK**. Łącznik niestandardowy został utworzony i dodany do określonego środowiska.

<a name="manual"></a>
## <a name="use-manual-export"></a>Użyj eksportu ręcznego

Aby zakończyć eksport w trybie **ręcznym** , wykonaj następujące kroki:

1. Kliknij pozycję **Pobierz** i Zapisz plik lub kliknij przycisk Kopiuj, a następnie Zapisz adres URL. Podczas importowania zostanie użyty plik do pobrania lub adres URL.
 
    ![Ręczne eksportowanie do usługi PowerApps i Microsoft Flow](media/app-service-export-api-to-powerapps-and-flow/export-manual.png)
 
2. Jeśli definicja interfejsu API zawiera wszystkie definicje zabezpieczeń, te definicje są wywoływane w kroku #2. Podczas importowania usługi PowerApps i Microsoft Flow wykrywają te definicje i monitują o informacje o zabezpieczeniach. Zbierz poświadczenia związane z każdą definicją do użycia w następnej sekcji. Aby uzyskać więcej informacji, zobacz [Określ typ uwierzytelniania](#auth) poniżej.

    ![Zabezpieczenia eksportu ręcznego](media/app-service-export-api-to-powerapps-and-flow/export-manual-security.png)

    Ten przykład przedstawia definicję zabezpieczeń klucza interfejsu API, która została uwzględniona w definicji OpenAPI.

Teraz, po wyeksportowaniu definicji interfejsu API, należy zaimportować ją do tworzenia łącznika niestandardowego w usłudze PowerApps i Microsoft Flow. Łączniki niestandardowe są współużytkowane przez te dwie usługi, więc wystarczy zaimportować definicję tylko raz.

Aby zaimportować definicję interfejsu API do usługi PowerApps i Microsoft Flow, wykonaj następujące kroki:

1. Przejdź do strony [powerapps.com](https://web.powerapps.com) lub [flow.microsoft.com](https://flow.microsoft.com).

2. W prawym górnym rogu kliknij ikonę koła zębatego, a następnie kliknij pozycję **Łączniki niestandardowe**.

   ![Ikona koła zębatego w usłudze](media/app-service-export-api-to-powerapps-and-flow/icon-gear.png)

3. Kliknij pozycję **Utwórz łącznik niestandardowy**, a następnie kliknij pozycję **Importuj definicję openapi**.

   ![Tworzenie łącznika niestandardowego](media/app-service-export-api-to-powerapps-and-flow/flow-apps-create-connector.png)

4. Wprowadź nazwę łącznika niestandardowego, a następnie przejdź do wyeksportowanej definicji OpenAPI, a następnie kliknij przycisk **Kontynuuj**.

   ![Przekaż definicję OpenAPI](media/app-service-export-api-to-powerapps-and-flow/flow-apps-upload-definition.png)

4. Na karcie **Ogólne** Przejrzyj informacje, które pochodzą z definicji openapi.

5. Jeśli zostanie wyświetlony monit o podanie szczegółów uwierzytelniania, na karcie **zabezpieczenia** wprowadź odpowiednie wartości w polu Typ uwierzytelniania. Kliknij przycisk **Kontynuuj**.

    ![Karta Zabezpieczenia](media/app-service-export-api-to-powerapps-and-flow/tab-security.png)

    W tym przykładzie przedstawiono pola wymagane do uwierzytelniania za pomocą klucza interfejsu API. Pola różnią się w zależności od typu uwierzytelniania.

6. Na karcie **definicje** wszystkie operacje zdefiniowane w pliku openapi są wypełniane automatycznie. Jeśli wszystkie wymagane operacje są zdefiniowane, możesz przejść do następnego kroku. W przeciwnym razie można dodawać i modyfikować operacje w tym miejscu.

    ![Karta definicje](media/app-service-export-api-to-powerapps-and-flow/tab-definitions.png)

    Ten przykład ma jedną operację o nazwie `CalculateCosts` . Metadane, takie jak **Opis**, pochodzą z pliku openapi.

7. Kliknij pozycję **Utwórz łącznik** w górnej części strony.

Teraz możesz nawiązać połączenie z łącznikiem niestandardowym w usłudze PowerApps i Microsoft Flow. Aby uzyskać więcej informacji na temat tworzenia łączników w portalach usługi PowerApps i Microsoft Flow, zobacz [Rejestrowanie niestandardowego łącznika (powerapps)](https://powerapps.microsoft.com/tutorials/register-custom-api/#register-your-custom-connector) i [Rejestrowanie łącznika niestandardowego (Microsoft Flow)](/power-automate/get-started-flow-dev#create-a-custom-connector).

<a name="auth"></a>
## <a name="specify-authentication-type"></a>Określanie typu uwierzytelniania

Usługa PowerApps i Microsoft Flow obsługują kolekcję dostawców tożsamości, które zapewniają uwierzytelnianie łączników niestandardowych. Jeśli interfejs API wymaga uwierzytelniania, upewnij się, że jest przechwytywany jako _Definicja zabezpieczeń_ w dokumencie openapi, jak w poniższym przykładzie:

```json
"securityDefinitions": {
    "AAD": {
    "type": "oauth2",
    "flow": "accessCode",
    "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
    "scopes": {}
    }
}
``` 
Podczas eksportowania dostarcza się wartości konfiguracyjne zezwalające usłudze PowerApps i Microsoft Flow na uwierzytelnianie użytkowników.

W tej sekcji omówiono typy uwierzytelniania, które są obsługiwane w trybie **Express** : klucz interfejsu API, Azure Active Directory i ogólne uwierzytelnianie OAuth 2,0. Usługa PowerApps i usługa Microsoft Flow obsługują również uwierzytelnianie podstawowe i protokół OAuth 2,0 dla określonych usług, takich jak Dropbox, Facebook i SalesForce.

### <a name="api-key"></a>Klucz interfejsu API
Przy użyciu klucza interfejsu API Użytkownicy Twojego łącznika są monitowani o podanie klucza podczas tworzenia połączenia. Należy określić nazwę klucza interfejsu API, aby pomóc im zrozumieć, który klucz jest wymagany. W poprzednim przykładzie używamy nazwy, `API Key (contact meganb@contoso.com)` aby użytkownicy wiedzieli, gdzie uzyskać informacje o kluczu interfejsu API. W przypadku Azure Functions klucz jest zazwyczaj jednym z kluczy hosta, obejmujących kilka funkcji w aplikacji funkcji.

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)
W przypadku korzystania z usługi Azure AD wymagane są dwie rejestracje aplikacji usługi Azure AD: jeden dla samego interfejsu API i jeden dla łącznika niestandardowego:

- Aby skonfigurować rejestrację dla interfejsu API, użyj funkcji [uwierzytelniania App Service/autoryzacji](../app-service/configure-authentication-provider-aad.md) .

- Aby skonfigurować rejestrację łącznika, wykonaj kroki opisane w temacie [Dodawanie aplikacji usługi Azure AD](../active-directory/develop/quickstart-register-app.md). Rejestracja musi mieć delegowany dostęp do Twojego interfejsu API i adres URL odpowiedzi `https://msmanaged-na.consent.azure-apim.net/redirect` . 

Aby uzyskać więcej informacji, zobacz przykłady rejestracji usługi Azure AD dla usług [powerapps](https://powerapps.microsoft.com/tutorials/customapi-azure-resource-manager-tutorial/) i [Microsoft Flow](/connectors/custom-connectors/azure-active-directory-authentication). Te przykłady używają Azure Resource Manager jako interfejsu API; Zastąp interfejs API, jeśli wykonano kroki.

Wymagane są następujące wartości konfiguracji:
- **Identyfikator klienta** — identyfikator klienta rejestracji usługi Azure AD łącznika
- **Klucz tajny klienta** — klucz tajny klienta dla rejestracji usługi Azure AD na łączniku
- **Adres URL logowania** — podstawowy adres URL usługi Azure AD. Na platformie Azure zwykle `https://login.windows.net` .
- **Identyfikator dzierżawy** — identyfikator dzierżawy, który ma być używany podczas logowania. Ten identyfikator powinien mieć wartość "Common" lub identyfikator dzierżawy, w której utworzono łącznik.
- **Adres URL zasobu** — adres URL zasobu rejestracji usługi Azure AD dla interfejsu API

> [!IMPORTANT]
> Jeśli ktoś inny zaimportuje definicję interfejsu API do usługi PowerApps i Microsoft Flow w ramach przepływu ręcznego, należy podać je identyfikator klienta i klucz tajny klienta *rejestracji łącznika*, a także adres URL zasobu interfejsu API. Upewnij się, że te klucze tajne są bezpiecznie zarządzane. **Nie udostępniaj poświadczeń zabezpieczeń samego interfejsu API.**

### <a name="generic-oauth-20"></a>Ogólne uwierzytelnianie OAuth 2.0
W przypadku korzystania z ogólnego uwierzytelniania OAuth 2,0 można zintegrować z dowolnym dostawcą protokołu OAuth 2,0. Dzięki temu można współpracować z dostawcami niestandardowymi, które nie są natywnie obsługiwane.

Wymagane są następujące wartości konfiguracji:
- **Identyfikator klienta** — identyfikator klienta OAuth 2,0
- **Klucz tajny klienta** — wpis tajny klienta OAuth 2,0
- **Adres URL autoryzacji** — adres URL autoryzacji OAuth 2,0
- **Adres URL tokenu** — adres URL tokenu OAuth 2,0
- **Odśwież adres URL** — adres URL odświeżania protokołu OAuth 2,0
