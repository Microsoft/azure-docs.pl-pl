---
title: Zarządzanie administratorami serwera w Azure Analysis Services | Microsoft Docs
description: W tym artykule opisano sposób zarządzania administratorami serwera dla serwera Azure Analysis Services przy użyciu Azure Portal, programu PowerShell lub interfejsów API REST.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 2/4/2021
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c0986b8508a7e21dee7c7c87e535eb2726944de8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769197"
---
# <a name="manage-server-administrators"></a>Zarządzanie administratorami serwerów

Administratorzy serwera muszą być prawidłowymi użytkownikami, jednostkami usługi lub grupą zabezpieczeń w Azure Active Directory (Azure AD) dla dzierżawy, w której znajduje się serwer. Administratorów serwera **Analysis Services można** używać w programie Azure Portal, właściwości serwera w programie SSMS, programie PowerShell lub interfejsie API REST do zarządzania administratorami serwera. 

Podczas dodawania **grupy zabezpieczeń** `obj:groupid@tenantid` użyj . Jednostki usługi nie są obsługiwane w grupach zabezpieczeń dodanych do roli administratora serwera.

Aby dowiedzieć się więcej na temat dodawania jednostki usługi do roli administratora serwera, zobacz Dodawanie jednostki usługi [do roli administratora serwera](analysis-services-addservprinc-admins.md).

Jeśli zapora serwera jest włączona, adresy IP komputerów klienckich administratora serwera muszą być uwzględnione w regułę zapory. Aby dowiedzieć się więcej, zobacz [Konfigurowanie zapory serwera](analysis-services-qs-firewall.md).

## <a name="to-add-server-administrators-by-using-azure-portal"></a>Aby dodać administratorów serwera przy użyciu Azure Portal

1. W portalu dla serwera kliknij pozycję **Analysis Services Administratorzy.**
2. W **\<servername> programie Analysis Services Administratorzy** kliknij pozycję **Dodaj.**
3. W **witrynie Add Server Administrators (Dodawanie** administratorów serwera) wybierz konta użytkowników z usługi Azure AD lub zaproś użytkowników zewnętrznych za pomocą adresu e-mail.

    ![Administratorzy serwera w Azure Portal](./media/analysis-services-server-admins/aas-manage-users-admins.png)

## <a name="to-add-server-administrators-by-using-ssms"></a>Aby dodać administratorów serwera przy użyciu programu SSMS

1. Kliknij prawym przyciskiem myszy serwer i > **właściwości**.
2. W **Analysis Server właściwości** kliknij pozycję **Zabezpieczenia.**
3. Kliknij **pozycję** Dodaj, a następnie wprowadź adres e-mail użytkownika lub grupy w usłudze Azure AD.
   
    ![Dodawanie administratorów serwera w programie SSMS](./media/analysis-services-server-admins/aas-manage-users-ssms.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Użyj polecenia cmdlet [New-AzAnalysisServicesServer,](/powershell/module/az.analysisservices/new-azanalysisservicesserver) aby określić parametr Administrator podczas tworzenia nowego serwera. <br>
Użyj polecenia cmdlet [Set-AzAnalysisServicesServer,](/powershell/module/az.analysisservices/set-azanalysisservicesserver) aby zmodyfikować parametr Administrator dla istniejącego serwera.

## <a name="rest-api"></a>Interfejs API REST

Użyj [pozycji](/rest/api/analysisservices/servers/create) Utwórz, aby określić właściwość asAdministrator podczas tworzenia nowego serwera. <br>
Użyj [aktualizacji,](/rest/api/analysisservices/servers/update) aby określić właściwość asAdministrator podczas modyfikowania istniejącego serwera. <br>



## <a name="next-steps"></a>Następne kroki 

[Uwierzytelnianie i uprawnienia użytkownika](analysis-services-manage-users.md)  
[Zarządzanie rolami i użytkownikami bazy danych](analysis-services-database-users.md)  
[Kontrola dostępu na podstawie ról na platformie Azure (Azure RBAC)](../role-based-access-control/overview.md)
