---
title: Dodaj nazwę główną usługi do roli administratora Azure Analysis Services | Microsoft Docs
description: Dowiedz się, jak dodać nazwę główną usługi Automation do roli administratora serwera Azure Analysis Services
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: fasttrack-edit
ms.openlocfilehash: b1d5f8ec628245756c53e4dfeeecdeb3a4bebc2e
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93100732"
---
# <a name="add-a-service-principal-to-the-server-administrator-role"></a>Dodawanie jednostki usługi do roli administratora serwera 

 Aby zautomatyzować nienadzorowane zadania programu PowerShell, jednostka usługi musi mieć uprawnienia **administratora serwera** na zarządzanym serwerze Analysis Services. W tym artykule opisano sposób dodawania nazwy głównej usługi do roli Administratorzy serwera na platformie Azure jako serwer. Można to zrobić przy użyciu SQL Server Management Studio lub szablonu Menedżer zasobów. 

> [!NOTE]
> Nazwy główne usługi należy dodać bezpośrednio do roli administratora serwera. Dodanie nazwy głównej usługi do grupy zabezpieczeń, a następnie dodanie tej grupy zabezpieczeń do roli administratora serwera nie jest obsługiwane. 

## <a name="before-you-begin"></a>Przed rozpoczęciem
Przed ukończeniem tego zadania musisz mieć nazwę główną usługi zarejestrowaną w Azure Active Directory.

[Tworzenie jednostki usługi — Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Tworzenie jednostki usługi — PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="using-sql-server-management-studio"></a>Korzystanie z programu SQL Server Management Studio

Administratorów serwera można skonfigurować przy użyciu SQL Server Management Studio (SSMS). Aby wykonać to zadanie, musisz mieć uprawnienia [administratora serwera](analysis-services-server-admins.md) na platformie Azure jako serwer. 

1. W programie SSMS Połącz się z platformą Azure jako serwerem.
2. W obszarze **Właściwości serwera**  >  **zabezpieczenia** kliknij przycisk **Dodaj** .
3. W obszarze **Wybierz użytkownika lub grupę** Wyszukaj zarejestrowaną aplikację według nazwy, wybierz pozycję, a następnie kliknij przycisk **Dodaj** .

    ![Wyszukaj konto jednostki usługi](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-picker.png)

4. Sprawdź identyfikator konta nazwy głównej usługi, a następnie kliknij przycisk **OK** .
    
    ![Zrzut ekranu pokazujący identyfikator konta jednostki usługi i wyróżniony przycisk OK.](./media/analysis-services-addservprinc-admins/aas-add-sp-ssms-add.png)

## <a name="using-a-resource-manager-template"></a>Używanie szablonu usługi Resource Manager

Administratorzy serwera można również skonfigurować, wdrażając serwer Analysis Services przy użyciu szablonu Azure Resource Manager. Tożsamość, w której działa wdrożenie, musi należeć do roli **współautor** dla zasobu w [kontroli dostępu opartej na ROLACH (Azure RBAC)](../role-based-access-control/overview.md).

> [!IMPORTANT]
> Nazwę główną usługi należy dodać przy użyciu formatu `app:{service-principal-client-id}@{azure-ad-tenant-id}` .

Poniższy szablon Menedżer zasobów wdraża serwer Analysis Services z określoną jednostką usługi dodaną do roli administratora Analysis Services:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "analysisServicesServerName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "analysisServicesSkuName": {
            "type": "string"
        },
        "analysisServicesCapacity": {
            "type": "int"
        },
        "servicePrincipalClientId": {
            "type": "string"
        },
        "servicePrincipalTenantId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('analysisServicesServerName')]",
            "type": "Microsoft.AnalysisServices/servers",
            "apiVersion": "2017-08-01",
            "location": "[parameters('location')]",
            "sku": {
                "name": "[parameters('analysisServicesSkuName')]",
                "capacity": "[parameters('analysisServicesCapacity')]"
            },
            "properties": {
                "asAdministrators": {
                    "members": [
                        "[concat('app:', parameters('servicePrincipalClientId'), '@', parameters('servicePrincipalTenantId'))]"
                    ]
                }
            }
        }
    ]
}
```

## <a name="using-managed-identities"></a>Korzystanie z tożsamości zarządzanych

Tożsamość zarządzaną można również dodać do listy Administratorzy Analysis Services. Przykładowo może istnieć [aplikacja logiki z tożsamością zarządzaną przez system](../logic-apps/create-managed-service-identity.md)i chcesz udzielić jej uprawnień do administrowania serwerem Analysis Services.

W większości części Azure Portal i interfejsów API tożsamości zarządzane są identyfikowane przy użyciu identyfikatora obiektu nazwy głównej usługi. Jednak Analysis Services wymaga ich zidentyfikowania przy użyciu identyfikatora klienta. Aby uzyskać identyfikator klienta dla jednostki usługi, możesz użyć interfejsu wiersza polecenia platformy Azure:

```bash
az ad sp show --id <ManagedIdentityServicePrincipalObjectId> --query appId -o tsv
```

Alternatywnie możesz użyć programu PowerShell:

```powershell
(Get-AzureADServicePrincipal -ObjectId <ManagedIdentityServicePrincipalObjectId>).AppId
```

Tego identyfikatora klienta można następnie użyć w połączeniu z IDENTYFIKATORem dzierżawy, aby dodać zarządzaną tożsamość do listy administratorów Analysis Services, zgodnie z powyższym opisem.

## <a name="related-information"></a>Informacje pokrewne

* [Pobierz moduł SQL Server PowerShell](/sql/ssms/download-sql-server-ps-module)   
* [Pobierz narzędzie SSMS](/sql/ssms/download-sql-server-management-studio-ssms)