---
title: Uwierzytelnianie aplikacji w celu uzyskania dostępu do usługi Azure Signal Service
description: Ten artykuł zawiera informacje o uwierzytelnianiu aplikacji przy użyciu Azure Active Directory dostępu do usługi Azure sygnalizującej
author: terencefan
ms.author: tefa
ms.service: signalr
ms.topic: conceptual
ms.date: 08/03/2020
ms.openlocfilehash: 597b69c1180ea1fb2a6812d648f8b8ad37707d66
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101092608"
---
# <a name="authenticate-an-application-with-azure-active-directory-to-access-azure-signalr-service"></a>Uwierzytelnianie aplikacji za pomocą Azure Active Directory w celu uzyskania dostępu do usługi Azure Signal Service
Microsoft Azure zapewnia zintegrowane zarządzanie kontrolą dostępu dla zasobów i aplikacji na podstawie Azure Active Directory (Azure AD). Główną zaletą korzystania z usługi Azure AD z usługą Azure Signal Service jest brak konieczności przechowywania poświadczeń w kodzie. Zamiast tego można zażądać tokenu dostępu OAuth 2,0 z platformy tożsamości firmy Microsoft. Nazwa zasobu do żądania tokenu to `https://signalr.azure.com/` . Usługa Azure AD uwierzytelnia podmiot zabezpieczeń (aplikację, grupę zasobów lub jednostkę usługi) z uruchomioną aplikacją. Jeśli uwierzytelnianie powiedzie się, usługa Azure AD zwraca token dostępu do aplikacji, a następnie może użyć tokenu dostępu w celu autoryzowania żądania do zasobów usługi Azure Signal Service.

Gdy rola jest przypisana do podmiotu zabezpieczeń usługi Azure AD, platforma Azure przyznaje dostęp do tych zasobów dla tego podmiotu zabezpieczeń. Dostęp można ograniczyć do poziomu subskrypcji, grupy zasobów lub zasobu usługi Azure Signal. Zabezpieczenia usługi Azure AD mogą przypisywać role do użytkownika, grupy, nazwy głównej usługi aplikacji lub [zarządzanej tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md). 

> [!NOTE]
> Definicja roli to kolekcja uprawnień. Kontrola dostępu oparta na rolach (RBAC) kontroluje, jak te uprawnienia są wymuszane za pośrednictwem przypisywania ról. Przypisanie roli składa się z trzech elementów: podmiotu zabezpieczeń, definicji roli i zakresu. Aby uzyskać więcej informacji, zobacz [Omówienie różnych ról](../role-based-access-control/overview.md).

## <a name="register-your-application-with-an-azure-ad-tenant"></a>Rejestrowanie aplikacji w dzierżawie usługi Azure AD
Pierwszy krok w korzystaniu z usługi Azure AD do autoryzacji zasobów usługi sygnałów platformy Azure polega na zarejestrowaniu aplikacji w dzierżawie usługi Azure AD na podstawie [Azure Portal](https://portal.azure.com/). Po zarejestrowaniu aplikacji dostarczasz informacje o aplikacji do usługi AD. Następnie usługa Azure AD udostępnia identyfikator klienta (nazywany również IDENTYFIKATORem aplikacji), którego można użyć do skojarzenia aplikacji z usługą Azure AD Runtime. Aby dowiedzieć się więcej o IDENTYFIKATORze klienta, zobacz [obiekty główne aplikacji i usługi w Azure Active Directory](../active-directory/develop/app-objects-and-service-principals.md). 

Na poniższych ilustracjach przedstawiono procedurę rejestrowania aplikacji sieci Web:

![Rejestrowanie aplikacji](./media/authenticate/app-registrations-register.png)

> [!Note]
> Jeśli aplikacja jest zarejestrowana jako aplikacja natywna, można określić dowolny prawidłowy identyfikator URI dla identyfikatora URI przekierowania. W przypadku aplikacji natywnych ta wartość nie musi być rzeczywistym adresem URL. W przypadku aplikacji sieci Web identyfikator URI przekierowania musi być prawidłowym identyfikatorem URI, ponieważ określa adres URL, na który są udostępniane tokeny.

Po zarejestrowaniu aplikacji zobaczysz **Identyfikator aplikacji (klienta)** w obszarze **Ustawienia**:

![Identyfikator aplikacji zarejestrowanej aplikacji](./media/authenticate/application-id.png)

Aby uzyskać więcej informacji na temat rejestrowania aplikacji w usłudze Azure AD, zobacz [Integrowanie aplikacji z Azure Active Directory](../active-directory/develop/quickstart-register-app.md).


### <a name="create-a-client-secret"></a>Tworzenie klucza tajnego klienta   
Aplikacja wymaga klucza tajnego klienta, aby potwierdzić swoją tożsamość podczas żądania tokenu. Aby dodać klucz tajny klienta, wykonaj następujące kroki.

1. Przejdź do rejestracji aplikacji w Azure Portal.
1. Wybierz ustawienie **certyfikaty & wpisy tajne** .
1. W obszarze wpisy **tajne klienta** wybierz pozycję **Nowy wpis tajny klienta** , aby utworzyć nowy wpis tajny.
1. Podaj opis wpisu tajnego i wybierz żądany interwał ważności.
1. Natychmiast skopiuj wartość nowego wpisu tajnego do bezpiecznej lokalizacji. Wartość Fill jest wyświetlana tylko raz.

![Tworzenie klucza tajnego klienta](./media/authenticate/client-secret.png)

### <a name="upload-a-certificate"></a>Przekaż certyfikat

Możesz również przekazać certyfikat zamiast tworzyć klucz tajny klienta.

![Przekaż certyfikat](./media/authenticate/certification.png)

## <a name="assign-azure-roles-using-the-azure-portal"></a>Przypisywanie ról platformy Azure przy użyciu Azure Portal  
Aby dowiedzieć się więcej na temat zarządzania dostępem do zasobów platformy Azure przy użyciu usługi Azure RBAC i Azure Portal, zobacz [ten artykuł](..//role-based-access-control/role-assignments-portal.md). 

Po ustaleniu odpowiedniego zakresu przypisania roli przejdź do tego zasobu w Azure Portal. Wyświetl ustawienia kontroli dostępu (IAM) dla zasobu i postępuj zgodnie z tymi instrukcjami, aby zarządzać przypisaniami ról:

1. W [Azure Portal](https://portal.azure.com/)przejdź do zasobu sygnalizującego.
1. Wybierz pozycję **Access Control (IAM)** , aby wyświetlić ustawienia kontroli dostępu dla usługi Azure Signal. 
1. Wybierz kartę **przypisania ról** , aby wyświetlić listę przypisań ról. Na pasku narzędzi wybierz przycisk **Dodaj** , a następnie wybierz pozycję **Dodaj przypisanie roli**. 

    ![Przycisk Dodaj na pasku narzędzi](./media/authenticate/role-assignments-add-button.png)

1. Na stronie **Dodawanie przypisania roli** wykonaj następujące czynności:
    1. Wybierz **rolę usługi Azure Signal** , którą chcesz przypisać. 
    1. Wyszukaj w celu zlokalizowania **podmiotu zabezpieczeń** (użytkownika, grupy, nazwy głównej usługi), do którego ma zostać przypisana rola.
    1. Wybierz pozycję **Zapisz** , aby zapisać przypisanie roli. 

        ![Przypisywanie roli do aplikacji](./media/authenticate/assign-role-to-application.png)

    1. Tożsamość, do której przypisano rolę, jest wyświetlana na liście w ramach tej roli. Na przykład na poniższej ilustracji przedstawiono tę aplikację `signalr-dev` i `signalr-service` znajdują się one w roli serwera aplikacji sygnalizującego. 
        
        ![Lista przypisywania ról](./media/authenticate/role-assignment-list.png)

Możesz wykonać podobne kroki, aby przypisać rolę zakres do grupy zasobów lub subskrypcji. Po zdefiniowaniu roli i jej zakresu można przetestować to zachowanie z przykładami [w tej lokalizacji usługi GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac).

## <a name="sample-codes-while-configuring-your-app-server"></a>Przykładowe kody podczas konfigurowania serwera aplikacji.

Dodaj następujące opcje, gdy `AddAzureSignalR` :

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>";
});
```

Lub po prostu Skonfiguruj `ConnectionString` plik w `appsettings.json` pliku.

```json
{
"Azure": {
    "SignalR": {
      "Enabled": true,
      "ConnectionString": "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientSecret=<clientSecret>;tenantId=<tenantId>"
    }
  },
}
```

W przypadku korzystania z programu `certificate` Zmień w `clientSecret` następujący sposób `clientCert` :

```C#
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;clientId=<clientId>;clientCert=<clientCertFilepath>;tenantId=<tenantId>";
```

## <a name="next-steps"></a>Następne kroki
- Aby dowiedzieć się więcej na temat RBAC, zobacz [co to jest kontrola dostępu oparta na rolach (Azure RBAC)](../role-based-access-control/overview.md)?
- Aby dowiedzieć się, jak przypisywać przypisania ról platformy Azure i zarządzać nimi za pomocą Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub API REST, zobacz następujące artykuły:
    - [Zarządzanie kontrolą dostępu opartą na rolach (RBAC) przy użyciu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Zarządzanie kontrolą dostępu opartą na rolach (RBAC) przy użyciu interfejsu wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md)
    - [Zarządzanie kontrolą dostępu opartą na rolach (RBAC) za pomocą interfejsu API REST](../role-based-access-control/role-assignments-rest.md)
    - [Zarządzanie kontrolą dostępu opartą na rolach (RBAC) przy użyciu szablonów Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

Zapoznaj się z następującymi artykułami:
- [Uwierzytelnianie tożsamości zarządzanej za pomocą Azure Active Directory w celu uzyskania dostępu do usługi Azure Signal Service](authenticate-managed-identity.md)
- [Autoryzuj dostęp do usługi Azure sygnalizacyjnej przy użyciu Azure Active Directory](authorize-access-azure-active-directory.md)