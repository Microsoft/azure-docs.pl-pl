---
title: Automatyzowanie Azure Analysis Services za pomocą jednostki usługi | Microsoft Docs
description: Dowiedz się, jak utworzyć jednostkę usługi na Azure Analysis Services zadań administracyjnych.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 04bc6ecd6e0a32e9234d07e995a7e012b17e3bbe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769161"
---
# <a name="automation-with-service-principals"></a>Automatyzacja przy użyciu jednostek usługi

Jednostki usług to zasoby aplikacji usługi Azure Active Directory tworzone w ramach dzierżawy w celu przeprowadzania nienadzorowanych operacji na poziomie zasobu lub usługi. Jest to unikatowy typ  tożsamości użytkownika z identyfikatorem aplikacji i hasłem lub certyfikatem. Jednostkę usługi mają tylko te uprawnienia niezbędne do wykonywania zadań zdefiniowanych przez role i uprawnienia, do których jest przypisana. 

W Analysis Services jednostki usługi są używane z usługami Azure Automation, trybem nienadzorowany programu PowerShell, niestandardowymi aplikacjami klienckimi i aplikacjami internetowymi do automatyzowania typowych zadań. Na przykład aprowizowanie serwerów, wdrażanie modeli, odświeżanie danych, skalowanie w górę/w dół oraz wstrzymywanie/wznawianie można zautomatyzować przy użyciu jednostki usługi. Uprawnienia są przypisywane do jednostki usługi za pośrednictwem członkostwa w rolach, podobnie jak zwykłe konta upn usługi Azure AD.

Analysis Services obsługuje również operacje wykonywane przez tożsamości zarządzane przy użyciu jednostki usługi. Aby dowiedzieć się więcej, zobacz [Tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) i usług platformy [Azure, które obsługują uwierzytelnianie usługi Azure AD.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-analysis-services)    

## <a name="create-service-principals"></a>Tworzenie jednostek usługi
 
Jednostki usługi można tworzyć w Azure Portal lub przy użyciu programu PowerShell. Aby dowiedzieć się więcej, zobacz:

[Tworzenie jednostki usługi — Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Tworzenie jednostki usługi — PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Przechowywanie zasobów poświadczeń i certyfikatów w Azure Automation

Poświadczenia i certyfikaty jednostki usługi mogą być bezpiecznie przechowywane w Azure Automation dla operacji na runbook. Aby dowiedzieć się więcej, zobacz:

[Zasoby poświadczeń w Azure Automation](../automation/shared-resources/credentials.md)   
[Zasoby certyfikatów w usłudze Azure Automation](../automation/shared-resources/certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Dodawanie jednostki usługi do roli administratora serwera

Aby można było używać jednostki usługi do Analysis Services zarządzania serwerem, należy dodać ją do roli administratorów serwera. Jednostki usługi należy dodawać bezpośrednio do roli administratora serwera. Dodawanie jednostki usługi do grupy zabezpieczeń, a następnie dodawanie tej grupy zabezpieczeń do roli administratora serwera nie jest obsługiwane. Aby dowiedzieć się więcej, [zobacz Dodawanie jednostki usługi do roli administratora serwera](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Jednostki usługi w ciągach połączenia

W ciągach połączenia można używać nazwy appID i hasła lub certyfikatu jednostki usługi w taki sam sposób jak w przypadku nazwy UPN.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="using-azanalysisservices-module"></a><a name="azmodule"></a>Korzystanie z modułu Az.AnalysisServices

W przypadku używania jednostki usługi do operacji zarządzania zasobami za pomocą [modułu Az.AnalysisServices](/powershell/module/az.analysisservices)  użyj `Connect-AzAccount` polecenia cmdlet . 

W poniższym przykładzie appID i hasło są używane do wykonywania operacji płaszczyzny sterowania w celu synchronizacji z replikami tylko do odczytu i skalowania w górę/wy:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force
$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

# Connect using Az module
Connect-AzAccount -Credential $Credential -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx"

# Syncronize a database for query scale out
Sync-AzAnalysisServicesInstance -Instance "asazure://westus.asazure.windows.net/testsvr" -Database "testdb"

# Scale up the server to an S1, set 2 read-only replicas, and remove the primary from the query pool. The new replicas will hydrate from the synchronized data.
Set-AzAnalysisServicesServer -Name "testsvr" -ResourceGroupName "testRG" -Sku "S1" -ReadonlyReplicaCount 2 -DefaultConnectionMode Readonly
```

#### <a name="using-sqlserver-module"></a>Korzystanie z modułu SQLServer

W poniższym przykładzie appID i hasło są używane do wykonywania operacji odświeżania modelowej bazy danych:

```powershell
Param (
        [Parameter(Mandatory=$true)] [String] $AppId,
        [Parameter(Mandatory=$true)] [String] $PlainPWord,
        [Parameter(Mandatory=$true)] [String] $TenantId
       )
$PWord = ConvertTo-SecureString -String $PlainPWord -AsPlainText -Force

$Credential = New-Object -TypeName "System.Management.Automation.PSCredential" -ArgumentList $AppId, $PWord

Invoke-ProcessTable -Server "asazure://westcentralus.asazure.windows.net/myserver" -TableName "MyTable" -Database "MyDb" -RefreshType "Full" -ServicePrincipal -ApplicationId $AppId -TenantId $TenantId -Credential $Credential
```

### <a name="amo-and-adomd"></a>AMO i ADOMD 

Nawiązując połączenie z aplikacjami klienta i aplikacjami internetowymi instalowalne pakiety NuGet [bibliotek klienta AMO i ADOMD](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true) w wersji 15.0.2 lub nowszej obsługują jednostki usługi w parametrach połączenia przy użyciu następującej składni: `app:AppID` i password lub `cert:thumbprint`. 

W poniższym przykładzie parametry `appID` i `password` są używane do wykonania operacji odświeżania bazy danych modelu:

```csharp
string appId = "xxx";
string authKey = "yyy";
string connString = $"Provider=MSOLAP;Data Source=asazure://westus.asazure.windows.net/<servername>;User ID=app:{appId};Password={authKey};";
Server server = new Server();
server.Connect(connString);
Database db = server.Databases.FindByName("adventureworks");
Table tbl = db.Model.Tables.Find("DimDate");
tbl.RequestRefresh(RefreshType.Full);
db.Model.SaveChanges();
```

## <a name="next-steps"></a>Następne kroki
[Zaloguj się przy użyciu Azure PowerShell](/powershell/azure/authenticate-azureps)   
[Odświeżanie za pomocą usługi Logic Apps](analysis-services-refresh-logic-app.md)  
[Odświeżanie za pomocą usługi Azure Automation](analysis-services-refresh-azure-automation.md)  
[Dodawanie jednostki usługi do roli administratora serwera](analysis-services-addservprinc-admins.md)  
[Automatyzowanie zadań Power BI Premium i zestawu danych za pomocą jednostki usługi](/power-bi/admin/service-premium-service-principal)
