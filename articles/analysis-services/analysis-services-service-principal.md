---
title: Automatyzowanie Azure Analysis Services zadań przy użyciu jednostek usługi | Microsoft Docs
description: Dowiedz się, jak utworzyć jednostkę usługi do automatyzowania Azure Analysis Services zadań administracyjnych.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 5d114017d5e5dfecfe20afc69849763a87aadd41
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92015322"
---
# <a name="automation-with-service-principals"></a>Automatyzacja przy użyciu jednostek usługi

Jednostki usług to zasoby aplikacji usługi Azure Active Directory tworzone w ramach dzierżawy w celu przeprowadzania nienadzorowanych operacji na poziomie zasobu lub usługi. Są one unikatowym typem *tożsamości użytkownika* przy użyciu identyfikatora aplikacji i hasła lub certyfikatu. Jednostka usługi ma tylko te uprawnienia, które są niezbędne do wykonywania zadań zdefiniowanych przez role i uprawnienia, do których jest przypisany. 

W Analysis Services nazwy główne usług są używane z Azure Automation, nienadzorowanym trybem programu PowerShell, niestandardowymi aplikacjami klienckimi i aplikacjami sieci Web do automatyzowania typowych zadań. Na przykład inicjowanie obsługi serwerów, wdrażanie modeli, odświeżanie danych, skalowanie w górę/w dół, a wstrzymywanie/wznawianie może być zautomatyzowane przy użyciu jednostek usługi. Uprawnienia są przypisywane do jednostek usługi za pomocą przynależności do roli, podobnie jak zwykłe konta UPN usługi Azure AD.

Analysis Services obsługuje również operacje wykonywane przez zarządzane tożsamości przy użyciu jednostek usługi. Aby dowiedzieć się więcej, zobacz temat [zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) i [usług platformy Azure, które obsługują uwierzytelnianie usługi Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-analysis-services).    

## <a name="create-service-principals"></a>Tworzenie jednostek usługi
 
Nazwy główne usługi można tworzyć w Azure Portal lub przy użyciu programu PowerShell. Aby dowiedzieć się więcej, zobacz:

[Tworzenie jednostki usługi — Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Tworzenie jednostki usługi — PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Przechowuj poświadczenia i zasoby certyfikatów w Azure Automation

Poświadczenia i certyfikaty jednostki usługi mogą być bezpiecznie przechowywane w Azure Automation na potrzeby operacji elementu Runbook. Aby dowiedzieć się więcej, zobacz:

[Zasoby poświadczeń w Azure Automation](../automation/shared-resources/credentials.md)   
[Zasoby certyfikatów w usłudze Azure Automation](../automation/shared-resources/certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Dodawanie jednostek usługi do roli administratora serwera

Aby można było użyć nazwy głównej usługi dla operacji zarządzania serwerem Analysis Services, należy dodać ją do roli Administratorzy serwera. Nazwy główne usługi należy dodać bezpośrednio do roli administratora serwera. Dodanie nazwy głównej usługi do grupy zabezpieczeń, a następnie dodanie tej grupy zabezpieczeń do roli administratora serwera nie jest obsługiwane. Aby dowiedzieć się więcej, zobacz [Dodawanie nazwy głównej usługi do roli administratora serwera](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Nazwy główne usług w ciągach połączeń

Identyfikator appID i hasło i certyfikat jednostki usługi mogą być używane w parametrach połączenia w taki sam sposób, jak nazwa UPN.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="using-azanalysisservices-module"></a><a name="azmodule"></a>Korzystanie z modułu AZ. AnalysisServices

W przypadku używania jednostki usługi do operacji zarządzania zasobami z modułem [AZ. AnalysisServices](/powershell/module/az.analysisservices)  należy użyć `Connect-AzAccount` polecenia cmdlet. 

W poniższym przykładzie identyfikator appID i hasło są używane do wykonywania operacji płaszczyzny kontroli w celu synchronizacji z replikami tylko do odczytu i skalowania w górę/w poziomie:

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

W poniższym przykładzie identyfikator appID i hasło są używane do wykonywania operacji odświeżania bazy danych modelu:

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

Nawiązując połączenie z aplikacjami klienta i aplikacjami internetowymi instalowalne pakiety NuGet [bibliotek klienta AMO i ADOMD](/analysis-services/client-libraries?view=azure-analysis-services-current) w wersji 15.0.2 lub nowszej obsługują jednostki usługi w parametrach połączenia przy użyciu następującej składni: `app:AppID` i password lub `cert:thumbprint`. 

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
[Automatyzowanie Power BI Premium obszarów roboczych i zadań DataSet przy użyciu jednostek usługi](/power-bi/admin/service-premium-service-principal)