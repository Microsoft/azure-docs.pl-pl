---
title: Zarządzanie administratorami serwera w Azure Analysis Services | Microsoft Docs
description: W tym artykule opisano sposób zarządzania administratorami serwera Azure Analysis Servicesm przy użyciu interfejsów API Azure Portal, PowerShell lub REST.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 9fdd6b6a195d0c6d4c4bf0489a037cb138a23a42
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96351728"
---
# <a name="manage-server-administrators"></a>Zarządzanie administratorami serwerów

Administratorzy serwera muszą być prawidłowymi użytkownikami, jednostkami usługi lub grupami zabezpieczeń w Azure Active Directory (Azure AD) dla dzierżawy, w której znajduje się serwer. Do zarządzania administratorami serwera można używać administratorów **Analysis Services** serwera w Azure Portal, właściwości serwera w programie SSMS, PowerShell lub interfejsie API REST. 

Podczas dodawania **grupy zabezpieczeń** Użyj `obj:groupid@tenantid` . Nazwy główne usług nie są obsługiwane w grupach zabezpieczeń dodanych do roli administratora serwera.

Jeśli Zapora serwera jest włączona, adresy IP komputera klienckiego administratora serwera muszą być zawarte w regule zapory. Aby dowiedzieć się więcej, zobacz [Konfigurowanie zapory serwera](analysis-services-qs-firewall.md).

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Aby dodać administratorów serwera przy użyciu Azure Portal

1. W portalu dla serwera kliknij pozycję **administratorzy Analysis Services**.
2. **\<servername> Administratorzy w Analysis Services** kliknij przycisk **Dodaj**.
3. W obszarze **Dodaj administratorów serwera** wybierz pozycję konta użytkowników z usługi Azure AD lub Zaproś użytkowników zewnętrznych według adresu e-mail.

    ![Administratorzy serwera w Azure Portal](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Aby dodać administratorów serwera przy użyciu programu SSMS

1. Kliknij prawym przyciskiem myszy serwer > **Właściwości**.
2. W oknie **właściwości Analysis Server** kliknij pozycję **zabezpieczenia**.
3. Kliknij przycisk **Dodaj**, a następnie wprowadź adres e-mail użytkownika lub grupy w usłudze Azure AD.
   
    ![Dodawanie administratorów serwera w programie SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Użyj polecenia cmdlet [New-AzAnalysisServicesServer](/powershell/module/az.analysisservices/new-azanalysisservicesserver) , aby określić parametr administratora podczas tworzenia nowego serwera. <br>
Użyj polecenia cmdlet [Set-AzAnalysisServicesServer](/powershell/module/az.analysisservices/set-azanalysisservicesserver) , aby zmodyfikować parametr administratora dla istniejącego serwera.

## <a name="rest-api"></a>Interfejs API REST

Użyj [Create](/rest/api/analysisservices/servers/create) , aby określić właściwość asAdministrator podczas tworzenia nowego serwera. <br>
Użyj [aktualizacji](/rest/api/analysisservices/servers/update) , aby określić właściwość asAdministrator podczas modyfikowania istniejącego serwera. <br>



## <a name="next-steps"></a>Następne kroki 

[Uwierzytelnianie i uprawnienia użytkownika](analysis-services-manage-users.md)  
[Zarządzanie rolami i użytkownikami bazy danych](analysis-services-database-users.md)  
[Kontrola dostępu na podstawie ról na platformie Azure (Azure RBAC)](../role-based-access-control/overview.md)
