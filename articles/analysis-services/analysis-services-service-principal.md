---
title: Automatyzowanie Azure Analysis Services zadań przy użyciu jednostek usługi | Microsoft Docs
description: Dowiedz się, jak utworzyć jednostkę usługi do automatyzowania Azure Analysis Services zadań administracyjnych.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: dc163de9a7fb46d62f4bc2983e040e68bbf9231c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79266145"
---
# <a name="automation-with-service-principals"></a>Automatyzacja przy użyciu jednostek usługi

Jednostki usług to zasoby aplikacji usługi Azure Active Directory tworzone w ramach dzierżawy w celu przeprowadzania nienadzorowanych operacji na poziomie zasobu lub usługi. Są one unikatowym typem *tożsamości użytkownika* przy użyciu identyfikatora aplikacji i hasła lub certyfikatu. Jednostka usługi ma tylko te uprawnienia, które są niezbędne do wykonywania zadań zdefiniowanych przez role i uprawnienia, do których jest przypisany. 

W Analysis Services nazwy główne usług są używane z Azure Automation, nienadzorowanym trybem programu PowerShell, niestandardowymi aplikacjami klienckimi i aplikacjami sieci Web do automatyzowania typowych zadań. Na przykład inicjowanie obsługi serwerów, wdrażanie modeli, odświeżanie danych, skalowanie w górę/w dół, a wstrzymywanie/wznawianie może być zautomatyzowane przy użyciu jednostek usługi. Uprawnienia są przypisywane do jednostek usługi za pomocą przynależności do roli, podobnie jak zwykłe konta UPN usługi Azure AD.

Analysis Services obsługuje również operacje wykonywane przez zarządzane tożsamości przy użyciu jednostek usługi. Aby dowiedzieć się więcej, zobacz temat [zarządzane tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) i [usług platformy Azure, które obsługują uwierzytelnianie usługi Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-analysis-services).  

## <a name="create-service-principals"></a>Tworzenie jednostek usługi
 
Nazwy główne usługi można tworzyć w Azure Portal lub przy użyciu programu PowerShell. Aby dowiedzieć się więcej, zobacz:

[Tworzenie nazwy głównej usługi — Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md)   
[Tworzenie jednostki usługi — PowerShell](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

## <a name="store-credential-and-certificate-assets-in-azure-automation"></a>Przechowuj poświadczenia i zasoby certyfikatów w Azure Automation

Poświadczenia i certyfikaty jednostki usługi mogą być bezpiecznie przechowywane w Azure Automation na potrzeby operacji elementu Runbook. Aby dowiedzieć się więcej, zobacz:

[Zasoby poświadczeń w Azure Automation](../automation/automation-credentials.md)   
[Zasoby certyfikatów w usłudze Azure Automation](../automation/automation-certificates.md)

## <a name="add-service-principals-to-server-admin-role"></a>Dodawanie jednostek usługi do roli administratora serwera

Aby można było użyć nazwy głównej usługi dla operacji zarządzania serwerem Analysis Services, należy dodać ją do roli Administratorzy serwera. Aby dowiedzieć się więcej, zobacz [Dodawanie nazwy głównej usługi do roli administratora serwera](analysis-services-addservprinc-admins.md).

## <a name="service-principals-in-connection-strings"></a>Nazwy główne usług w ciągach połączeń

Identyfikator appID i hasło i certyfikat jednostki usługi mogą być używane w parametrach połączenia w taki sam sposób, jak nazwa UPN.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

#### <a name="using-azanalysisservices-module"></a><a name="azmodule" />Korzystanie z modułu AZ. AnalysisServices

W przypadku używania jednostki usługi do operacji zarządzania zasobami z modułem [AZ. AnalysisServices](/powershell/module/az.analysisservices) należy użyć `Connect-AzAccount` polecenia cmdlet. 

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

Podczas nawiązywania połączenia z aplikacjami klienckimi i aplikacjami sieci Web, [bibliotekami klienckimi biblioteki AMO i ADOMD](analysis-services-data-providers.md) w wersji 15.0.2 i wyższych, które mają być instalowalne, z poziomu `app:AppID` usług NuGet w `cert:thumbprint`parametrach połączenia przy użyciu następującej składni: i hasła lub. 

W poniższym przykładzie `appID` i `password` są używane do wykonywania operacji odświeżania bazy danych modelu:

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
[Zaloguj się przy użyciu Azure PowerShell](https://docs.microsoft.com/powershell/azure/authenticate-azureps)   
[Dodawanie jednostki usługi do roli administratora serwera](analysis-services-addservprinc-admins.md)   
