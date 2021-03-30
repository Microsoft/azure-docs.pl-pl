---
title: Używanie Azure Active Directory do uwierzytelniania rozwiązań do zarządzania partiami
description: Skorzystaj z Azure Active Directory do uwierzytelniania z poziomu aplikacji, które korzystają z biblioteki zarządzania usługą Batch dla platformy .NET.
ms.topic: how-to
ms.date: 04/27/2017
ms.custom: has-adal-ref, devx-track-csharp
ms.openlocfilehash: ff49d5e88df7c56ed4dee0685f09e45eb372aa5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88930217"
---
# <a name="authenticate-batch-management-solutions-with-active-directory"></a>Uwierzytelnianie rozwiązań do zarządzania partiami przy użyciu Active Directory

Aplikacje, które wywołują Azure Batch usługi zarządzania uwierzytelniają się za pomocą [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD). Azure AD to usługa zarządzania tożsamościami w chmurze z wieloma dzierżawcami firmy Microsoft. Sama platforma Azure korzysta z usługi Azure AD do uwierzytelniania klientów, administratorów usługi i użytkowników w organizacji.

Biblioteka platformy .NET zarządzania usługą Batch udostępnia typy do pracy z kontami usługi Batch, kluczami kont, aplikacjami i pakietami aplikacji. Biblioteka .NET Management jest klientem dostawcy zasobów platformy Azure i jest używana razem z [Azure Resource Manager](../azure-resource-manager/management/overview.md) do programu programistycznego zarządzania tymi zasobami. Usługa Azure AD jest wymagana do uwierzytelniania żądań wysyłanych za pomocą dowolnego klienta dostawcy zasobów platformy Azure, w tym biblioteki usługi Batch Management .NET, a także za pomocą Azure Resource Manager.

W tym artykule omówiono korzystanie z usługi Azure AD do uwierzytelniania z poziomu aplikacji, które korzystają z biblioteki zarządzania usługą Batch dla platformy .NET. Pokazujemy, jak używać usługi Azure AD do uwierzytelniania administratora lub współadministratora subskrypcji przy użyciu uwierzytelniania zintegrowanego. Korzystamy z przykładowego projektu [zarządzania kontem](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement) , dostępnego w witrynie GitHub, aby zapoznać się z użyciem usługi Azure AD z biblioteką zarządzania usługą Batch dla platformy .NET.

Aby dowiedzieć się więcej o korzystaniu z biblioteki .NET Management Library i przykładu zarządzania kontem, zobacz [Zarządzanie kontami i przydziałami usługi Batch za pomocą biblioteki klienta zarządzania usługą Batch dla platformy .NET](batch-management-dotnet.md).

## <a name="register-your-application-with-azure-ad"></a>Rejestrowanie aplikacji w usłudze Azure AD

[Biblioteka Azure Active Directory Authentication Library](../active-directory/azuread-dev/active-directory-authentication-libraries.md) (ADAL) udostępnia interfejs programistyczny usługi Azure AD do użycia w aplikacjach. Aby wywołać biblioteki ADAL z poziomu aplikacji, musisz zarejestrować aplikację w dzierżawie usługi Azure AD. Po zarejestrowaniu aplikacji dostarczasz usługę Azure AD z informacjami o swojej aplikacji, w tym jej nazwą w ramach dzierżawy usługi Azure AD. Następnie usługa Azure AD udostępnia identyfikator aplikacji, który służy do kojarzenia aplikacji z usługą Azure AD w czasie wykonywania. Aby dowiedzieć się więcej o IDENTYFIKATORze aplikacji, zobacz [obiekty główne aplikacji i usługi w Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md).

Aby zarejestrować przykładową aplikację zarządzania kontem, wykonaj kroki opisane w sekcji [Dodawanie aplikacji](../active-directory/develop/quickstart-register-app.md) w temacie [Integrowanie aplikacji z Azure Active Directory](../active-directory/develop/quickstart-register-app.md). Określ **natywną aplikację kliencką** dla typu aplikacji. Standardowy identyfikator URI protokołu OAuth 2,0 dla **identyfikatora URI przekierowania** to `urn:ietf:wg:oauth:2.0:oob` . Można jednak określić dowolny prawidłowy identyfikator URI (na przykład `http://myaccountmanagementsample` ) dla **identyfikatora URI przekierowania**, ponieważ nie musi to być rzeczywisty punkt końcowy.

![Dodawanie aplikacji](./media/batch-aad-auth-management/app-registration-management-plane.png)

Po zakończeniu procesu rejestracji zostanie wyświetlony Identyfikator aplikacji i identyfikator obiektu (nazwy głównej usługi) na liście aplikacji.

![Proces rejestracji zakończonych](./media/batch-aad-auth-management/app-registration-client-id.png)

## <a name="grant-the-azure-resource-manager-api-access-to-your-application"></a>Przyznaj Azure Resource Manager dostęp do aplikacji interfejsu API

Następnie musisz delegować dostęp do aplikacji do interfejsu API Azure Resource Manager. Identyfikator usługi Azure AD dla interfejsu API Menedżer zasobów to **Windows Azure interfejs API zarządzania usługami**.

Wykonaj następujące kroki w witrynie Azure Portal:

1. W okienku nawigacji po lewej stronie Azure Portal wybierz pozycję **wszystkie usługi**, kliknij pozycję **rejestracje aplikacji**, a następnie kliknij przycisk **Dodaj**.
2. Wyszukaj nazwę aplikacji na liście rejestracji aplikacji:

    ![Wyszukaj nazwę aplikacji](./media/batch-aad-auth-management/search-app-registration.png)

3. Wyświetl blok **Ustawienia** . W sekcji **dostęp do interfejsu API** wybierz pozycję **wymagane uprawnienia**.
4. Kliknij przycisk **Dodaj** , aby dodać nowe wymagane uprawnienie.
5. W kroku 1 wprowadź **interfejs API zarządzania usługami platformy Microsoft Azure**, wybierz ten interfejs API z listy wyników, a następnie kliknij przycisk **Wybierz** .
6. W kroku 2 zaznacz pole wyboru obok pozycji dostęp do **klasycznego modelu wdrażania platformy Azure jako użytkownicy organizacji**, a następnie kliknij przycisk **Wybierz** .
7. Kliknij przycisk **gotowe** .

Blok **uprawnienia wymagane** teraz pokazuje, że uprawnienia do aplikacji są udzielane zarówno dla interfejsów API ADAL, jak i Menedżer zasobów. Uprawnienia są domyślnie przyznawane ADAL w przypadku pierwszej rejestracji aplikacji w usłudze Azure AD.

![Delegowanie uprawnień do interfejsu API Azure Resource Manager](./media/batch-aad-auth-management/required-permissions-management-plane.png)

## <a name="azure-ad-endpoints"></a>Punkty końcowe usługi Azure AD

Aby można było uwierzytelniać rozwiązania do zarządzania usługą Batch za pomocą usługi Azure AD, potrzebne są dwa dobrze znane punkty końcowe.

- **Wspólny punkt końcowy usługi Azure AD** zapewnia ogólny interfejs zbierania poświadczeń, gdy nie zostanie podana określona dzierżawa, tak jak w przypadku uwierzytelniania zintegrowanego:

    `https://login.microsoftonline.com/common`

- **Punkt końcowy Azure Resource Manager** służy do uzyskiwania tokenu do uwierzytelniania żądań do usługi zarządzania usługą Batch:

    `https://management.core.windows.net/`

Przykładowa aplikacja zarządzania kontem definiuje stałe dla tych punktów końcowych. Pozostaw te stałe bez zmian:

```csharp
// Azure Active Directory "common" endpoint.
private const string AuthorityUri = "https://login.microsoftonline.com/common";
// Azure Resource Manager endpoint
private const string ResourceUri = "https://management.core.windows.net/";
```

## <a name="reference-your-application-id"></a>Odwoływanie się do identyfikatora aplikacji

Aplikacja kliencka używa identyfikatora aplikacji (zwanego również IDENTYFIKATORem klienta) w celu uzyskania dostępu do usługi Azure AD w czasie wykonywania. Po zarejestrowaniu aplikacji w Azure Portal Zaktualizuj swój kod, aby używał identyfikatora aplikacji dostarczonego przez usługę Azure AD dla zarejestrowanej aplikacji. W przykładowej aplikacji zarządzania kontem Skopiuj identyfikator aplikacji z Azure Portal do odpowiedniej stałej:

```csharp
// Specify the unique identifier (the "Client ID") for your application. This is required so that your
// native client application (i.e. this sample) can access the Microsoft Graph API. For information
// about registering an application in Azure Active Directory, please see "Register an application with the Microsoft identity platform" here:
// https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app
private const string ClientId = "<application-id>";
```
Skopiuj także identyfikator URI przekierowania określony podczas procesu rejestracji. Identyfikator URI przekierowania określony w kodzie musi być zgodny z identyfikatorem URI przekierowania, który został dostarczony podczas rejestrowania aplikacji.

```csharp
// The URI to which Azure AD will redirect in response to an OAuth 2.0 request. This value is
// specified by you when you register an application with AAD (see ClientId comment). It does not
// need to be a real endpoint, but must be a valid URI (e.g. https://accountmgmtsampleapp).
private const string RedirectUri = "http://myaccountmanagementsample";
```

## <a name="acquire-an-azure-ad-authentication-token"></a>Uzyskiwanie tokenu uwierzytelniania usługi Azure AD

Po zarejestrowaniu przykładu zarządzania kontem w dzierżawie usługi Azure AD i zaktualizowaniu przykładowego kodu źródłowego za pomocą wartości przykład jest gotowy do uwierzytelniania za pomocą usługi Azure AD. Po uruchomieniu przykładu ADAL próbuje uzyskać token uwierzytelniania. W tym kroku zostanie wyświetlony komunikat z prośbą o poświadczenia firmy Microsoft:

```csharp
// Obtain an access token using the "common" AAD resource. This allows the application
// to query AAD for information that lies outside the application's tenant (such as for
// querying subscription information in your Azure account).
AuthenticationContext authContext = new AuthenticationContext(AuthorityUri);
AuthenticationResult authResult = authContext.AcquireToken(ResourceUri,
                                                        ClientId,
                                                        new Uri(RedirectUri),
                                                        PromptBehavior.Auto);
```

Po podaniu poświadczeń Przykładowa aplikacja może w dalszym trakcie wydać uwierzytelnione żądania do usługi zarządzania usługą Batch.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać więcej informacji na temat uruchamiania [przykładowej aplikacji zarządzania kontem](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/AccountManagement), zobacz [Zarządzanie kontami i przydziałami usługi Batch za pomocą biblioteki klienta zarządzania usługą Batch dla platformy .NET](batch-management-dotnet.md).
- Aby dowiedzieć się więcej o usłudze Azure AD, zapoznaj się z [dokumentacją Azure Active Directory](../active-directory/index.yml).
- Szczegółowe przykłady pokazujące, jak używać biblioteki ADAL, są dostępne w bibliotece [przykładów kodu platformy Azure](https://azure.microsoft.com/resources/samples/?service=active-directory) .
- Aby uwierzytelnić aplikacje usługi Batch za pomocą usługi Azure AD, zobacz temat [uwierzytelnianie rozwiązań usługi Batch za pomocą Active Directory](batch-aad-auth.md).
