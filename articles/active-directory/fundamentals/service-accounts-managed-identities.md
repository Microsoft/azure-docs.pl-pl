---
title: Zabezpieczanie tożsamości zarządzanych w Azure Active Directory
description: Wyjaśnienie sposobu znajdowania, oceniania i zwiększania bezpieczeństwa zarządzanych tożsamości.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 3/1/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4584b0a605bd63c1f71082014d3c0622ca7d2c37
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587835"
---
# <a name="securing-managed-identities"></a>Zabezpieczanie tożsamości zarządzanych

Deweloperzy są często wzywani do zarządzania wpisami tajnymi i poświadczeniami używanymi do zabezpieczania komunikacji między różnymi usługami. Tożsamości zarządzane są bezpiecznymi tożsamościami Azure Active Directory (Azure AD) utworzonymi w celu dostarczania tożsamości dla zasobów platformy Azure.

## <a name="benefits-of-using-managed-identities-for-azure-resources"></a>Zalety korzystania z tożsamości zarządzanych dla zasobów platformy Azure

Poniżej przedstawiono zalety korzystania z tożsamości zarządzanych:

* Nie musisz zarządzać poświadczeniami. Przy użyciu tożsamości zarządzanych poświadczenia są w pełni zarządzane, obracane i chronione przez platformę Azure. Tożsamość jest automatycznie dostarczana i usuwana z zasobami platformy Azure. Zarządzane tożsamości umożliwiają zasobom platformy Azure komunikowanie się ze wszystkimi usługami, które obsługują uwierzytelnianie w usłudze Azure AD.

* Nikt (łącznie z administratorem globalnym) ma dostęp do poświadczeń, więc nie można go przypadkowo wyciekać przez, na przykład w kodzie.

## <a name="when-to-use-managed-identities"></a>Kiedy używać tożsamości zarządzanych?

Tożsamości zarządzane są najlepiej używane do komunikacji między usługami, które obsługują uwierzytelnianie usługi Azure AD. 

System źródłowy żąda dostępu do usługi docelowej. Dowolny zasób platformy Azure może być systemem źródłowym. Na przykład maszyny wirtualne platformy Azure, wystąpienie funkcji platformy Azure i wystąpienia usługi Azure App Services obsługują tożsamości zarządzane.

[!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

### <a name="how-authentication-and-authorization-work"></a>Jak działa uwierzytelnianie i autoryzacja

Z tożsamościami zarządzanymi system źródłowy może uzyskać token z usługi Azure AD bez właściciela źródła, który ma zarządzać poświadczeniami. Platforma Azure zarządza poświadczeniami. Token uzyskany przez system źródłowy jest przedstawiany systemowi docelowemu do uwierzytelniania. 

System docelowy musi uwierzytelniać (identyfikować) i autoryzować system źródłowy przed zezwoleniem na dostęp. Gdy usługa docelowa obsługuje uwierzytelnianie oparte na usłudze Azure AD, akceptuje token dostępu wystawiony przez usługę Azure AD. 

Platforma Azure ma płaszczyznę kontroli i płaszczyznę danych. Na płaszczyźnie kontroli tworzysz zasoby i w płaszczyźnie danych, do których uzyskujesz dostęp. Na przykład utworzysz bazę danych Cosmos na płaszczyźnie kontroli, ale zbadamy ją w płaszczyźnie danych.

Gdy system docelowy zaakceptuje token uwierzytelniania, może obsługiwać różne mechanizmy autoryzacji dla jego płaszczyzny kontroli i płaszczyzny danych.

Wszystkie operacje płaszczyzny kontroli platformy Azure są zarządzane przez [Azure Resource Manager](../../azure-resource-manager/management/overview.md) i używają [Access Control opartych na rolach platformy Azure](../../role-based-access-control/overview.md). W płaszczyźnie danych każdy system docelowy ma własny mechanizm autoryzacji. Usługa Azure Storage obsługuje funkcję RBAC na platformie Azure w ramach płaszczyzny danych. Na przykład aplikacje korzystające z usługi Azure App Services mogą odczytywać dane z usługi Azure Storage, a aplikacje korzystające z usługi Azure Kubernetes mogą odczytywać wpisy tajne przechowywane w Azure Key Vault.

Aby uzyskać więcej informacji na temat kontroli i płaszczyzny danych, zobacz [działania płaszczyzny kontroli i płaszczyzny danych — Azure Resource Manager](../../azure-resource-manager/management/control-plane-and-data-plane.md).

Wszystkie usługi platformy Azure będą ostatecznie obsługiwać zarządzane tożsamości. Aby uzyskać więcej informacji, zobacz [usługi obsługujące tożsamości zarządzane dla zasobów platformy Azure](../managed-identities-azure-resources/services-support-managed-identities.md).

##  

## <a name="types-of-managed-identities"></a>Typy tożsamości zarządzanych

Istnieją dwa typy tożsamości zarządzanych — przypisane do systemu i przypisane przez użytkownika.

Tożsamość zarządzana przypisana przez system ma następujące właściwości:

* Mają one 1:1 relacje z zasobem platformy Azure. Na przykład istnieje unikatowa tożsamość zarządzana skojarzona z każdą maszyną wirtualną.

* Są one powiązane z cyklem życia zasobów platformy Azure. Po usunięciu zasobu zarządzana tożsamość skojarzona z nią jest automatycznie usuwana, eliminując ryzyko związane z oddzielonymi kontami. 

Tożsamości zarządzane przypisane przez użytkownika mają następujące właściwości:

* Cykl życia tych tożsamości jest niezależny od zasobu platformy Azure, a użytkownik musi zarządzać cyklem życia. Po usunięciu zasobu platformy Azure przypisana tożsamość zarządzana przypisana przez użytkownika nie jest automatycznie usuwana.

* Tożsamość zarządzana przypisana przez użytkownika może być przypisana do zera lub większej liczby zasobów platformy Azure.

* Mogą być tworzone wcześniej, a następnie przypisywane do zasobu.

## <a name="find-managed-identity-service-principals-in-azure-ad"></a>Znajdowanie podmiotów usługi zarządzania tożsamościami w usłudze Azure AD

Istnieje kilka sposobów, w których można znaleźć tożsamości zarządzane:

* Używanie strony aplikacje dla przedsiębiorstw w Azure Portal

* Używanie Microsoft Graph

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

1. W usłudze Azure AD wybierz pozycję Aplikacja dla przedsiębiorstw.

2. Wybierz filtr dla "tożsamości zarządzane" 

   ![Obraz ekranu wszystkie aplikacje z wyróżnioną listą rozwijaną typ aplikacji "tożsamości zarządzane".](./media/securing-service-accounts/service-accounts-managed-identities.png)

 

### <a name="using-microsoft-graph"></a>Używanie Microsoft Graph

Możesz uzyskać listę wszystkich zarządzanych tożsamości w dzierżawie przy użyciu następującego żądania GET, aby Microsoft Graph:

`https://graph.microsoft.com/v1.0/servicePrincipals?$filter=(servicePrincipalType eq 'ManagedIdentity') `

Można filtrować te żądania. Aby uzyskać więcej informacji, zobacz dokumentację wykresu dla [Get serviceprincipal](/graph/api/serviceprincipal-get?view=).

## <a name="assess-the-security-of-managed-identities"></a>Ocenianie zabezpieczeń tożsamości zarządzanych 

Zabezpieczenia zarządzanych tożsamości można ocenić w następujący sposób:

* Sprawdź uprawnienia i upewnij się, że wybrano najmniej uprzywilejowany model. Użyj następującego polecenia cmdlet programu PowerShell, aby uzyskać uprawnienia przypisane do zarządzanych tożsamości.

   ` Get-AzureADServicePrincipal | % { Get-AzureADServiceAppRoleAssignment -ObjectId $_ }`

 
* Upewnij się, że zarządzana tożsamość nie jest częścią żadnych uprzywilejowanych grup, takich jak Grupa Administratorzy.  
Można to zrobić przez Wyliczenie członków grup o wysokim poziomie uprawnień przy użyciu programu PowerShell.

   `Get-AzureADGroupMember -ObjectId <String> [-All <Boolean>] [-Top <Int32>] [<CommonParameters>]`

* [Upewnij się, że zasoby, do których uzyskuje dostęp tożsamość zarządzana](../../role-based-access-control/role-assignments-list-powershell.md).

## <a name="move-to-managed-identities"></a>Przenieś do zarządzanych tożsamości

Jeśli używasz nazwy głównej usługi lub konta użytkownika usługi Azure AD, Oceń, czy zamiast tego możesz użyć funkcji zarządzanej, aby wyeliminować konieczność ochrony i rotacji poświadczeń oraz zarządzania nimi. 

## <a name="next-steps"></a>Następne kroki

**Aby uzyskać informacje na temat tworzenia tożsamości zarządzanych, zobacz:** 

[Utwórz tożsamość zarządzaną przypisaną przez użytkownika](../managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 

[Włącz tożsamość zarządzaną przypisaną przez system podczas tworzenia zasobu](../managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

[Włącz tożsamość zarządzaną przypisaną przez system w istniejącym zasobie](../managed-identities-azure-resources/qs-configure-portal-windows-vm.md)

**Aby uzyskać więcej informacji o kontach usług, zobacz:**

[Wprowadzenie do Azure Active Directory kont usług](service-accounts-introduction-azure.md)

[Zabezpieczanie podmiotów usługi](service-accounts-principal.md)

[Zarządzanie kontami usług platformy Azure](service-accounts-governing-azure.md)

[Wprowadzenie do kont usług lokalnych](service-accounts-on-premises.md)

 

 

