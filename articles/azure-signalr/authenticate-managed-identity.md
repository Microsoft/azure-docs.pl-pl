---
title: Uwierzytelnianie tożsamości zarządzanej przy użyciu Azure Active Directory
description: Ten artykuł zawiera informacje dotyczące uwierzytelniania tożsamości zarządzanej za pomocą Azure Active Directory dostępu do usługi Azure Signal Service
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: c561bb507a5178f4a838b370a3da8af9447829f4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101092554"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-signalr-resources"></a>Uwierzytelnianie tożsamości zarządzanej za pomocą Azure Active Directory w celu uzyskania dostępu do zasobów usługi Azure Signal
Usługa Azure Signal Service obsługuje uwierzytelnianie Azure Active Directory (Azure AD) z [tożsamościami zarządzanymi dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md). Zarządzane tożsamości dla zasobów platformy Azure mogą autoryzować dostęp do zasobów usługi sygnałów platformy Azure przy użyciu poświadczeń Azure AD z aplikacji działających na platformie Azure Virtual Machines (maszyny wirtualne), aplikacji funkcji, Virtual Machine Scale Sets i innych usług. Korzystając z tożsamości zarządzanych dla zasobów platformy Azure wraz z uwierzytelnianiem w usłudze Azure AD, można uniknąć zapisywania poświadczeń z aplikacjami uruchomionymi w chmurze.

W tym artykule pokazano, jak autoryzować dostęp do usługi Azure sygnalizacyjnej przy użyciu tożsamości zarządzanej z maszyny wirtualnej platformy Azure.

## <a name="enable-managed-identities-on-a-vm"></a>Włączanie tożsamości zarządzanych na maszynie wirtualnej
Aby można było używać zarządzanych tożsamości dla zasobów platformy Azure w celu autoryzowania zasobów usługi Azure SIgnal z maszyny wirtualnej, należy najpierw włączyć zarządzane tożsamości dla zasobów platformy Azure na maszynie wirtualnej. Aby dowiedzieć się, jak włączyć zarządzane tożsamości dla zasobów platformy Azure, zobacz jeden z następujących artykułów:

- [Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Interfejs wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Szablon usługi Azure Resource Manager](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager biblioteki klienckie](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Przyznawanie uprawnień do zarządzanej tożsamości w usłudze Azure AD
Aby autoryzować żądanie do usługi sygnalizującej platformę Azure z zarządzanej tożsamości w aplikacji, najpierw Skonfiguruj ustawienia kontroli dostępu opartej na rolach (RBAC) dla tej tożsamości zarządzanej. Usługa Azure Signal definiuje role RBAC, które obejmują uprawnienia do pozyskiwania `AccessKey` lub `ClientToken` . Gdy rola RBAC zostanie przypisana do tożsamości zarządzanej, zarządzana tożsamość uzyskuje dostęp do usługi Azure Signaler w odpowiednim zakresie.

Aby uzyskać więcej informacji na temat przypisywania ról RBAC, zobacz [uwierzytelnianie za pomocą Azure Active Directory w celu uzyskania dostępu do zasobów usługi Azure Signal](authorize-access-azure-active-directory.md).

## <a name="connect-to-azure-signalr-service-with-managed-identities"></a>Nawiązywanie połączenia z usługą Azure Signal Service z tożsamościami zarządzanymi
Aby nawiązać połączenie z usługą Azure sygnalizacyjnym z tożsamościami zarządzanymi, musisz przypisać tożsamość do roli i odpowiedniego zakresu. Procedura opisana w tej sekcji korzysta z prostej aplikacji, która działa w ramach zarządzanej tożsamości i uzyskuje dostęp do zasobów usługi Azure Signal Service.

Tutaj korzystamy z przykładowego zasobu maszyny wirtualnej platformy Azure.

1. Przejdź do pozycji **Ustawienia** i wybierz pozycję **tożsamość**. 
1. Wybierz **stan** , który ma być **włączony**. 
1. Wybierz pozycję **Zapisz**, aby zapisać ustawienie. 

    ![Tożsamość zarządzana dla maszyny wirtualnej](./media/authenticate/identity-virtual-machine.png)

Po włączeniu tego ustawienia zostanie utworzona nowa tożsamość usługi w Azure Active Directory (Azure AD) i skonfigurowana na hoście App Service.

Teraz Przypisz tę tożsamość usługi do roli w wymaganym zakresie zasobów usługi Azure Signal Service.

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

## <a name="samples-code-while-configuring-your-app-server"></a>Kod przykładów podczas konfigurowania serwera aplikacji

Dodaj następujące opcje, gdy `AddAzureSignalR` :

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;";
});
```

## <a name="next-steps"></a>Następne kroki
- Aby dowiedzieć się więcej na temat RBAC, zobacz [co to jest kontrola dostępu oparta na rolach (Azure RBAC)](../role-based-access-control/overview.md)?
- Aby dowiedzieć się, jak przypisywać przypisania ról platformy Azure i zarządzać nimi za pomocą Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub API REST, zobacz następujące artykuły:
    - [Zarządzanie kontrolą dostępu opartą na rolach (RBAC) przy użyciu Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)  
    - [Zarządzanie kontrolą dostępu opartą na rolach (RBAC) przy użyciu interfejsu wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md)
    - [Zarządzanie kontrolą dostępu opartą na rolach (RBAC) za pomocą interfejsu API REST](../role-based-access-control/role-assignments-rest.md)
    - [Zarządzanie kontrolą dostępu opartą na rolach (RBAC) przy użyciu szablonów Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

Zapoznaj się z następującymi artykułami:
- [Uwierzytelnianie aplikacji za pomocą Azure Active Directory w celu uzyskania dostępu do usługi Azure Signal Service](authenticate-application.md)
- [Autoryzuj dostęp do usługi Azure sygnalizacyjnej przy użyciu Azure Active Directory](authorize-access-azure-active-directory.md)