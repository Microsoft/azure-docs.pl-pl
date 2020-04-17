---
title: Zarządzanie administratorami serwerów w usługach Azure Analysis Services | Dokumenty firmy Microsoft
description: W tym artykule opisano sposób zarządzania administratorami serwerów dla serwera usług Azure Analysis Services przy użyciu interfejsu API azure portal, powershell lub REST.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 944a84405bd7e03b72b2610278f9f0e4d3cfaf38
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81454241"
---
# <a name="manage-server-administrators"></a>Zarządzanie administratorami serwerów

Administratorzy serwera muszą być prawidłowymi użytkownikami lub grupą zabezpieczeń w usłudze Azure Active Directory (Azure AD) dla dzierżawy, w której znajduje się serwer. **Administratorów usług Analysis Services** dla serwera można używać w witrynie Azure portal, Właściwości serwera w interfejsie SSMS, PowerShell lub REST API do zarządzania administratorami serwerów. 

Podczas dodawania **grupy** `obj:groupid@tenantid`zabezpieczeń użyj .

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Aby dodać administratorów serwera przy użyciu witryny Azure Portal

1. W portalu dla serwera kliknij pozycję **Administratorzy usług Analysis Services**.
2. W ** \<> nazwa serwera - Administratorzy usług Analysis Services**kliknij przycisk **Dodaj**.
3. W **obszarze Dodaj administratorzy serwera**wybierz konta użytkowników z usługi Azure AD lub zaproś użytkowników zewnętrznych według adresu e-mail.

    ![Administratorzy serwera w witrynie Azure portal](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Aby dodać administratorów serwera przy użyciu usługi SSMS

1. Kliknij prawym przyciskiem myszy serwer > **Właściwości**.
2. W **oknie Właściwości serwera analysis (Właściwości serwera analizy)** kliknij pozycję **Zabezpieczenia**.
3. Kliknij **pozycję Dodaj**, a następnie wprowadź adres e-mail użytkownika lub grupy w usłudze Azure AD.
   
    ![Dodawanie administratorów serwera w systemie SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Polecenie cmdlet [New-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/new-azanalysisservicesserver) umożliwia określenie parametru Administrator podczas tworzenia nowego serwera. <br>
Użyj polecenia cmdlet [Set-AzAnalysisServicesServer,](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver) aby zmodyfikować parametr Administrator dla istniejącego serwera.

## <a name="rest-api"></a>Interfejs API REST

Użyj [Create,](https://docs.microsoft.com/rest/api/analysisservices/servers/create) aby określić właściwość asAdministrator podczas tworzenia nowego serwera. <br>
Użyj [update,](https://docs.microsoft.com/rest/api/analysisservices/servers/update) aby określić właściwość asAdministrator podczas modyfikowania istniejącego serwera. <br>



## <a name="next-steps"></a>Następne kroki 

[Uwierzytelnianie i uprawnienia użytkownika](analysis-services-manage-users.md)  
[Zarządzanie rolami i użytkownikami bazy danych](analysis-services-database-users.md)  
[Kontrola dostępu oparta na rolach](../role-based-access-control/overview.md)  

