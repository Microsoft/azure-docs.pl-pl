---
title: Uwierzytelnianie aplikacji w celu uzyskania dostępu do zasobów usługi Azure Event Hubs
description: Ten artykuł zawiera informacje o uwierzytelnianiu aplikacji przy użyciu Azure Active Directory dostępu do zasobów Event Hubs platformy Azure
ms.topic: conceptual
ms.date: 10/21/2020
ms.openlocfilehash: 25ec5f11ca7b5e801e18155f1a3da6474c8e66e2
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92913317"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-event-hubs-resources"></a>Uwierzytelnianie aplikacji za pomocą Azure Active Directory w celu uzyskania dostępu do zasobów Event Hubs
Microsoft Azure zapewnia zintegrowane zarządzanie kontrolą dostępu dla zasobów i aplikacji na podstawie Azure Active Directory (Azure AD). Główną zaletą korzystania z usługi Azure AD z usługą Azure Event Hubs jest to, że nie musisz już przechowywać poświadczeń w kodzie. Zamiast tego można zażądać tokenu dostępu OAuth 2,0 z platformy tożsamości firmy Microsoft. Nazwa zasobu do żądania tokenu jest taka `https://eventhubs.azure.net/` sama dla wszystkich chmur/dzierżawców (dla klientów Kafka, zasób do żądania tokenu `https://<namespace>.servicebus.windows.net` ). Usługa Azure AD uwierzytelnia podmiot zabezpieczeń (użytkownika, grupę lub nazwę główną usługi), na którym działa aplikacja. W przypadku pomyślnego uwierzytelnienia usługa Azure AD zwraca token dostępu do aplikacji, a następnie może użyć tokenu dostępu do autoryzowania żądania do zasobów usługi Azure Event Hubs.

Gdy rola jest przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure przyznaje dostęp do tych zasobów dla tego podmiotu zabezpieczeń. Dostęp można ograniczyć do poziomu subskrypcji, grupy zasobów, przestrzeni nazw Event Hubs lub dowolnego zasobu w ramach tego elementu. Zabezpieczenia usługi Azure AD mogą przypisywać role do użytkownika, grupy, nazwy głównej usługi aplikacji lub [zarządzanej tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md). 

> [!NOTE]
> Definicja roli to kolekcja uprawnień. Kontrola dostępu oparta na rolach (Azure RBAC) kontroluje, jak te uprawnienia są wymuszane przez przypisanie roli. Przypisanie roli składa się z trzech elementów: podmiotu zabezpieczeń, definicji roli i zakresu. Aby uzyskać więcej informacji, zobacz [Omówienie różnych ról](../role-based-access-control/overview.md).

## <a name="built-in-roles-for-azure-event-hubs"></a>Wbudowane role dla Event Hubs platformy Azure
Platforma Azure udostępnia następujące wbudowane role platformy Azure do autoryzowania dostępu do danych Event Hubs przy użyciu usługi Azure AD i uwierzytelniania OAuth:

- [Właściciel danych Event Hubs platformy Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-owner): Użyj tej roli, aby zapewnić pełen dostęp do zasobów Event Hubs.
- [Nadawca danych Event Hubs platformy Azure](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-sender): Użyj tej roli, aby udzielić dostępu do Event Hubs do zasobów.
- [Usługa Azure Event Hubs Data Receiver](../role-based-access-control/built-in-roles.md#azure-event-hubs-data-receiver): Ta rola umożliwia uzyskanie dostępu do Event Hubs zasobów.   

Aby uzyskać wbudowane role rejestru schematu, zobacz [role rejestru schematu](schema-registry-overview.md#azure-role-based-access-control).

> [!IMPORTANT]
> Nasza wersja zapoznawcza obsługuje dodawanie Event Hubs uprawnień dostępu do danych do roli właściciel lub współautor. Jednak uprawnienia dostępu do danych dla roli właściciela i współautora nie są już honorowane. Jeśli używasz roli właściciela lub współautora, przełącz się do korzystania z roli właściciela danych Event Hubs platformy Azure.


## <a name="authenticate-from-an-application"></a>Uwierzytelnianie przy użyciu aplikacji
Główną zaletą korzystania z usługi Azure AD z Event Hubs jest to, że Twoje poświadczenia nie muszą już być przechowywane w kodzie. Zamiast tego można zażądać tokenu dostępu OAuth 2,0 z platformy tożsamości firmy Microsoft. Usługa Azure AD uwierzytelnia podmiot zabezpieczeń (użytkownika, grupę lub nazwę główną usługi), na którym działa aplikacja. W przypadku pomyślnego uwierzytelnienia usługa Azure AD zwraca token dostępu do aplikacji, a następnie może użyć tokenu dostępu do autoryzowania żądań do usługi Azure Event Hubs.

W poniższych sekcjach pokazano, jak skonfigurować aplikację natywną lub aplikację sieci Web do uwierzytelniania za pomocą platformy Microsoft Identity platform 2,0. Aby uzyskać więcej informacji o platformie Microsoft Identity platform 2,0, zobacz [Microsoft Identity platform (v 2.0) — Omówienie](../active-directory/develop/v2-overview.md).

Aby zapoznać się z omówieniem przepływu przyznawania kodu OAuth 2,0, zobacz temat [Autoryzuj dostęp do aplikacji sieci web Azure Active Directory przy użyciu przepływu przyznawania kodu oauth 2,0](../active-directory/develop/v2-oauth2-auth-code-flow.md).

### <a name="register-your-application-with-an-azure-ad-tenant"></a>Rejestrowanie aplikacji w dzierżawie usługi Azure AD
Pierwszym krokiem korzystania z usługi Azure AD w celu autoryzowania Event Hubs zasobów jest zarejestrowanie aplikacji klienckiej przy użyciu dzierżawy usługi Azure AD z [Azure Portal](https://portal.azure.com/). Po zarejestrowaniu aplikacji klienckiej podaj informacje o aplikacji do usługi AD. Następnie usługa Azure AD udostępnia identyfikator klienta (nazywany również IDENTYFIKATORem aplikacji), którego można użyć do skojarzenia aplikacji z usługą Azure AD Runtime. Aby dowiedzieć się więcej o IDENTYFIKATORze klienta, zobacz [obiekty główne aplikacji i usługi w Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Na poniższych ilustracjach przedstawiono procedurę rejestrowania aplikacji sieci Web:

![Rejestrowanie aplikacji](./media/authenticate-application/app-registrations-register.png)

> [!Note]
> Jeśli aplikacja jest zarejestrowana jako aplikacja natywna, można określić dowolny prawidłowy identyfikator URI dla identyfikatora URI przekierowania. W przypadku aplikacji natywnych ta wartość nie musi być rzeczywistym adresem URL. W przypadku aplikacji sieci Web identyfikator URI przekierowania musi być prawidłowym identyfikatorem URI, ponieważ określa adres URL, na który są udostępniane tokeny.

Po zarejestrowaniu aplikacji zobaczysz **Identyfikator aplikacji (klienta)** w obszarze **Ustawienia** :

![Identyfikator aplikacji zarejestrowanej aplikacji](./media/authenticate-application/application-id.png)

Aby uzyskać więcej informacji na temat rejestrowania aplikacji w usłudze Azure AD, zobacz [Integrowanie aplikacji z Azure Active Directory](../active-directory/develop/quickstart-register-app.md).


### <a name="create-a-client-secret"></a>Tworzenie klucza tajnego klienta   
Aplikacja wymaga klucza tajnego klienta, aby potwierdzić swoją tożsamość podczas żądania tokenu. Aby dodać klucz tajny klienta, wykonaj następujące kroki.

1. Przejdź do rejestracji aplikacji w Azure Portal.
1. Wybierz ustawienie **certyfikaty & wpisy tajne** .
1. W obszarze wpisy **tajne klienta** wybierz pozycję **Nowy wpis tajny klienta** , aby utworzyć nowy wpis tajny.
1. Podaj opis wpisu tajnego i wybierz żądany interwał ważności.
1. Natychmiast skopiuj wartość nowego wpisu tajnego do bezpiecznej lokalizacji. Wartość Fill jest wyświetlana tylko raz.

    ![Klucz tajny klienta](./media/authenticate-application/client-secret.png)


## <a name="assign-azure-roles-using-the-azure-portal"></a>Przypisywanie ról platformy Azure przy użyciu Azure Portal  
Po zarejestrowaniu aplikacji należy przypisać nazwę główną usługi aplikacji do roli Event Hubs usługi Azure AD opisanej w sekcji [role kompilacji dla platformy Azure Event Hubs](#built-in-roles-for-azure-event-hubs) . 

1. W [Azure Portal](https://portal.azure.com/)przejdź do przestrzeni nazw Event Hubs.
2. Na stronie **Przegląd** wybierz centrum zdarzeń, do którego chcesz przypisać rolę.

    ![Wybierz centrum zdarzeń](./media/authenticate-application/select-event-hub.png)
1. Wybierz pozycję **Access Control (IAM)** , aby wyświetlić ustawienia kontroli dostępu dla centrum zdarzeń. 
1. Wybierz kartę **przypisania ról** , aby wyświetlić listę przypisań ról. Na pasku narzędzi wybierz przycisk **Dodaj** , a następnie wybierz pozycję **Dodaj przypisanie roli** . 

    ![Przycisk Dodaj na pasku narzędzi](./media/authenticate-application/role-assignments-add-button.png)
1. Na stronie **Dodawanie przypisania roli** wykonaj następujące czynności:
    1. Wybierz **rolę Event Hubs** , którą chcesz przypisać. 
    1. Wyszukaj w celu zlokalizowania **podmiotu zabezpieczeń** (użytkownika, grupy, nazwy głównej usługi), do którego ma zostać przypisana rola. Wybierz **zarejestrowaną aplikację** z listy. 
    1. Wybierz pozycję **Zapisz** , aby zapisać przypisanie roli. 

        ![Przypisywanie roli do użytkownika](./media/authenticate-application/assign-role-to-user.png)
    4. Przejdź do karty **przypisania ról** i Potwierdź przypisanie roli. Na przykład na poniższej ilustracji przedstawiono, że **mywebapp** znajduje się w roli **nadawcy danych Event Hubs platformy Azure** . 
        
        ![Użytkownik na liście](./media/authenticate-application/user-in-list.png)

Możesz wykonać podobne kroki, aby przypisać rolę zakres do Event Hubs przestrzeni nazw, grupy zasobów lub subskrypcji. Po zdefiniowaniu roli i jej zakresu można przetestować to zachowanie z przykładami [w tej lokalizacji usługi GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). Aby dowiedzieć się więcej na temat zarządzania dostępem do zasobów platformy Azure przy użyciu usługi Azure RBAC i Azure Portal, zobacz [ten artykuł](..//role-based-access-control/role-assignments-portal.md). 


### <a name="client-libraries-for-token-acquisition"></a>Biblioteki klienckie do pozyskiwania tokenów  
Po zarejestrowaniu aplikacji i udzieleniu uprawnień IT do wysyłania/odbierania danych w usłudze Azure Event Hubs możesz dodać kod do aplikacji, aby uwierzytelnić podmiot zabezpieczeń i uzyskać token OAuth 2,0. Aby uwierzytelnić i uzyskać token, można użyć jednej z [bibliotek uwierzytelniania platformy tożsamości firmy Microsoft](../active-directory/develop/reference-v2-libraries.md) lub innej biblioteki typu open source, która obsługuje OpenID Connect lub Connect 1,0. Aplikacja może następnie użyć tokenu dostępu, aby autoryzować żądanie do Event Hubs platformy Azure.

Aby zapoznać się z listą scenariuszy, w których są obsługiwane tokeny, zobacz sekcję [scenariusze](https://aka.ms/msal-net-scenarios) w [bibliotece Microsoft Authentication Library (MSAL) dla](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) repozytorium usługi .NET GitHub.

## <a name="samples"></a>Samples
- [Przykłady Microsoft. Azure. EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    Te przykłady używają starej biblioteki **Microsoft. Azure. EventHubs** , ale można ją łatwo zaktualizować do korzystania z najnowszej biblioteki **Azure. Messaging. EventHubs** . Aby przenieść przykład z używania starej biblioteki do nowej, zapoznaj się z [przewodnikiem migrowania z Microsoft. Azure. EventHubs do platformy Azure. Messaging. EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
- [Przykłady dla platformy Azure. Messaging. EventHubs](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)

    Ten przykład został zaktualizowany, aby można było użyć najnowszej biblioteki **Azure. Messaging. EventHubs** .

## <a name="next-steps"></a>Następne kroki
- Aby dowiedzieć się więcej na temat usługi Azure RBAC, zobacz [co to jest kontrola dostępu oparta na rolach (Azure RBAC)](../role-based-access-control/overview.md)?
- Aby dowiedzieć się, jak przypisywać przypisania ról platformy Azure i zarządzać nimi za pomocą Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub API REST, zobacz następujące artykuły:
    - [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu programu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md)
    - [Dodawanie lub usuwanie przypisań ról platformy Azure przy użyciu interfejsu API REST](../role-based-access-control/role-assignments-rest.md)
    - [Dodawanie przypisań ról platformy Azure przy użyciu szablonów Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

Zapoznaj się z następującymi artykułami:
- [Uwierzytelnianie zarządzanej tożsamości za pomocą Azure Active Directory w celu uzyskania dostępu do zasobów Event Hubs](authenticate-managed-identity.md)
- [Uwierzytelnianie żądań na platformie Azure Event Hubs przy użyciu sygnatur dostępu współdzielonego](authenticate-shared-access-signature.md)
- [Autoryzuj dostęp do zasobów Event Hubs przy użyciu Azure Active Directory](authorize-access-azure-active-directory.md)
- [Autoryzuj dostęp do zasobów Event Hubs przy użyciu sygnatur dostępu współdzielonego](authorize-access-shared-access-signature.md)
