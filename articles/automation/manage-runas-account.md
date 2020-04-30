---
title: Zarządzaj kontami Azure Automation Uruchom jako
description: W tym artykule opisano sposób zarządzania kontami Uruchom jako przy użyciu programu PowerShell lub portalu.
services: automation
ms.subservice: shared-capabilities
ms.date: 04/23/2020
ms.topic: conceptual
ms.openlocfilehash: 5cb3752e5a74f26936efcbb9dba5cdcda76e01f4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82113310"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Zarządzaj kontami Azure Automation Uruchom jako

Konta Uruchom jako w Azure Automation umożliwiają uwierzytelnianie w celu zarządzania zasobami na platformie Azure przy użyciu poleceń cmdlet platformy Azure. Podczas tworzenia konta Uruchom jako jest tworzony nowy użytkownik głównej nazwy usługi w Azure Active Directory (AD) i przypisuje rolę współautor do tego użytkownika na poziomie subskrypcji.

## <a name="types-of-run-as-accounts"></a>Typy kont Uruchom jako

Azure Automation używa dwóch typów kont Uruchom jako:

* Konto Uruchom jako platformy Azure
* Klasyczne konto Uruchom jako platformy Azure

>[!NOTE]
>Subskrypcje dostawcy rozwiązań w chmurze (CSP) platformy Azure obsługują tylko model Azure Resource Manager. Usługi inne niż Azure Resource Manager nie są dostępne w programie. W przypadku korzystania z subskrypcji programu CSP klasyczne konto Uruchom jako platformy Azure nie jest tworzone, ale zostało utworzone konto Uruchom jako platformy Azure. Aby dowiedzieć się więcej o subskrypcjach dostawcy usług kryptograficznych, zobacz [dostępne usługi w subskrypcjach programu CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).

Nazwa główna usługi dla konta Uruchom jako nie ma uprawnień do domyślnego odczytywania usługi Azure AD. Jeśli chcesz dodać uprawnienia do odczytu lub zarządzania usługą Azure AD, musisz udzielić uprawnień do nazwy głównej usługi w obszarze **uprawnienia interfejsu API**. Aby dowiedzieć się więcej, zobacz [Dodawanie uprawnień dostępu do interfejsów API sieci Web](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

### <a name="run-as-account"></a>Konto Uruchom jako

Konto Uruchom jako służy do zarządzania zasobami [modelu wdrażania Menedżer zasobów](../azure-resource-manager/management/deployment-models.md) . Wykonuje następujące zadania.

* Tworzy aplikację usługi Azure AD z certyfikatem z podpisem własnym, tworzy konto jednostki usługi dla aplikacji w usłudze Azure AD i przypisuje rolę Współautor dla konta w bieżącej subskrypcji. Można zmienić ustawienie certyfikatu na właściciela lub inną rolę. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu oparta na rolach w usłudze Azure Automation](automation-role-based-access-control.md).
  
* Tworzy zasób certyfikatu usługi Automation o `AzureRunAsCertificate` nazwie w określonym koncie usługi Automation. Zasób certyfikatu zawiera klucz prywatny certyfikatu, którego używa aplikacja usługi Azure AD.
  
* Tworzy zasób połączenia usługi Automation o `AzureRunAsConnection` nazwie w określonym koncie usługi Automation. Zasób połączenia zawiera identyfikator aplikacji, identyfikator dzierżawy, Identyfikator subskrypcji i odcisk palca certyfikatu.

### <a name="azure-classic-run-as-account"></a>Klasyczne konto Uruchom jako platformy Azure

Klasyczne konto Uruchom jako platformy Azure zarządza zasobami [klasycznego modelu wdrażania](../azure-resource-manager/management/deployment-models.md) . Aby utworzyć lub odnowić ten typ konta, musisz być współadministratorem w ramach subskrypcji.

Klasyczne konto Uruchom jako platformy Azure wykonuje następujące zadania.

  * Tworzy certyfikat zarządzania w subskrypcji.

  * Tworzy zasób certyfikatu usługi Automation o `AzureClassicRunAsCertificate` nazwie w określonym koncie usługi Automation. Zasób certyfikatu zawiera klucz prywatny certyfikatu używany przez certyfikat zarządzania.

  * Tworzy zasób połączenia usługi Automation o `AzureClassicRunAsConnection` nazwie w określonym koncie usługi Automation. Zasób połączenia zawiera nazwę subskrypcji, Identyfikator subskrypcji i nazwę zasobu certyfikatu.

>[!NOTE]
>Klasyczne konto Uruchom jako platformy Azure nie jest domyślnie tworzone w momencie tworzenia konta usługi Automation. To konto jest tworzone osobno zgodnie z krokami opisanymi w dalszej części tego artykułu.

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Uprawnienia konta Uruchom jako

Ta sekcja definiuje uprawnienia zarówno do zwykłych kont Uruchom jako, jak i klasycznych kont Uruchom jako.

### <a name="permissions-to-configure-run-as-accounts"></a>Uprawnienia do konfigurowania kont Uruchom jako

W celu utworzenia lub zaktualizowania konta Uruchom jako wymagane są określone uprawnienia. Administrator aplikacji w Azure Active Directory i właściciel subskrypcji może ukończyć wszystkie zadania. W przypadku rozdzielenia obowiązków w poniższej tabeli przedstawiono listę zadań, równoważne polecenie cmdlet i potrzebne uprawnienia:

|Zadanie|Polecenie cmdlet  |Minimalne uprawnienia  |Ustawianie uprawnień|
|---|---------|---------|---|
|Tworzenie aplikacji usługi Azure AD|[New-AzADApplication](https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication)     | Rola dewelopera aplikacji<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Rejestracja aplikacji w domu > usługi Azure AD > |
|Dodaj poświadczenie do aplikacji.|[New-AzADAppCredential](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential)     | Administrator aplikacji lub Administrator globalny<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Rejestracja aplikacji w domu > usługi Azure AD >|
|Tworzenie i pobieranie jednostki usługi Azure AD|[New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal)     | Administrator aplikacji lub Administrator globalny<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Rejestracja aplikacji w domu > usługi Azure AD >|
|Przypisz lub uzyskaj rolę RBAC dla określonego podmiotu zabezpieczeń|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Administrator lub właściciel dostępu użytkowników albo mają następujące uprawnienia:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Subskrypcja](../role-based-access-control/role-assignments-portal.md)</br>Subskrypcje > domowej > \<nazwa\> subskrypcji — Access Control (IAM)|
|Utwórz lub Usuń certyfikat usługi Automation|[New-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Remove-AzAutomationCertificate](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate)     | Współautor w grupie zasobów         |Grupa zasobów konta usługi Automation|
|Tworzenie lub usuwanie połączenia automatyzacji|[New-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection)</br>[Remove-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection)|Współautor w grupie zasobów |Grupa zasobów konta usługi Automation|

<sup>1</sup> użytkownicy inni niż administratorzy w dzierżawie usługi Azure AD mogą [rejestrować aplikacje usługi AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) , jeśli opcja Użytkownicy dzierżawy usługi Azure AD **może rejestrować aplikacje** na stronie Ustawienia użytkownika jest ustawiona na **wartość tak**. Jeśli ustawienie rejestracji aplikacji ma wartość **nie**, użytkownik wykonujący tę akcję musi być zdefiniowany w tej tabeli.

Jeśli nie jesteś członkiem wystąpienia Active Directory subskrypcji przed dodaniem do roli administratora globalnego subskrypcji, zostanie on dodany jako gość. W takiej sytuacji pojawi się `You do not have permissions to create…` ostrzeżenie na stronie Dodawanie konta usługi Automation. 

Jeśli jesteś członkiem wystąpienia Active Directory subskrypcji po przypisaniu roli administratora globalnego, możesz również otrzymać `You do not have permissions to create…` ostrzeżenie na stronie Dodawanie konta usługi Automation. W takim przypadku można zażądać usunięcia z wystąpienia Active Directory subskrypcji, a następnie zażądać ponownego dodania, aby stał się pełnym użytkownikiem w Active Directory.

Aby sprawdzić, czy problem z wygenerowaniem komunikatu o błędzie został usunięty:

1. W okienku Azure Active Directory w Azure Portal wybierz pozycję **Użytkownicy i grupy**. 
2. Wybierz pozycję **Wszyscy użytkownicy**.
3. Wybierz swoją nazwę, a następnie wybierz pozycję **profil**. 
4. Upewnij się, że wartość atrybutu **typu użytkownika** w profilu użytkownika nie jest ustawiona na **gość**.

### <a name="permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>Uprawnienia do konfigurowania klasycznych kont Uruchom jako

Aby skonfigurować lub odnowić klasyczne konta Uruchom jako, musisz mieć rolę współadministrator na poziomie subskrypcji. Aby dowiedzieć się więcej o uprawnieniach klasycznych subskrypcji, zobacz [klasycznej subskrypcji platformy Azure](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="creating-a-run-as-account-in-azure-portal"></a>Tworzenie konta Uruchom jako w Azure Portal

Wykonaj następujące kroki, aby zaktualizować konto Azure Automation w Azure Portal. Utwórz pojedynczo konta Uruchom jako i klasycznej jako. Jeśli nie trzeba zarządzać zasobami klasycznymi, można po prostu utworzyć konto Uruchom jako platformy Azure.

1. Zaloguj się do portalu Azure przy użyciu konta, które jest członkiem roli Administratorzy subskrypcji i współadministratorem subskrypcji.
2. Wyszukaj i wybierz pozycję **konta usługi Automation**.
3. Na stronie konta usługi Automation wybierz z listy konto usługi Automation.
4. W okienku po lewej stronie wybierz pozycję **konta Uruchom jako** w sekcji Ustawienia konta.
5. W zależności od tego, które konto jest wymagane, wybierz pozycję **Konto Uruchom jako platformy Azure** lub **Klasyczne konto Uruchom jako platformy Azure**. 
6. W zależności od konta zainteresowania użyj okienka Dodawanie konta Uruchom **jako platformy Azure** lub **Dodaj klasyczne konto Uruchom jako platformy Azure** . Po przejrzeniu informacji przeglądowych kliknij przycisk **Utwórz**.
6. W trakcie tworzenia konta Uruchom jako na platformie Azure postęp można śledzić po wybraniu z menu opcji **Powiadomienia**. Zostanie również wyświetlony transparent informujący o utworzeniu konta. Proces może potrwać kilka minut.

## <a name="creating-a-run-as-account-using-powershell"></a>Tworzenie konta Uruchom jako przy użyciu programu PowerShell

Poniższa lista zawiera wymagania dotyczące tworzenia konta Uruchom jako w programie PowerShell. Te wymagania dotyczą obu typów kont Uruchom jako.

* System Windows 10 lub Windows Server 2016 z Azure Resource Manager modules i nowszymi. Skrypt programu PowerShell nie obsługuje wcześniejszych wersji systemu Windows.
* Program Azure PowerShell 1.0 lub nowszy. Informacje o wersji PowerShell 1.0 można znaleźć w temacie [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) (Jak zainstalować i skonfigurować program Azure PowerShell).
* Konto usługi Automation, do którego odwołuje się wartość parametrów `AutomationAccountName` i. `ApplicationDisplayName`
* Uprawnienia równoważne z tymi wymienionymi w [wymaganych uprawnieniach do konfigurowania kont Uruchom jako](#permissions).

Aby uzyskać wartości dla `SubscriptionId`, `ResourceGroupName`i, które są wymaganymi parametrami dla skryptu programu PowerShell, wykonaj poniższe kroki.

1. W Azure Portal wybierz pozycję **konta usługi Automation**.
1. Na stronie konta usługi Automation wybierz swoje konto usługi Automation.
1. W sekcji Ustawienia konta wybierz pozycję **Właściwości**.
1. Zwróć uwagę na wartości **nazwy**, **identyfikatora subskrypcji**i **grupy zasobów** na stronie właściwości. Te wartości odpowiadają odpowiednio wartościom parametrów `AutomationAccountName`skryptu `SubscriptionId`, i `ResourceGroupName` programu PowerShell.

   ![Strona właściwości konta usługi Automation](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Skrypt programu PowerShell służący do tworzenia konta Uruchom jako

Ta sekcja zawiera skrypt programu PowerShell służący do tworzenia konta Uruchom jako. Skrypt zawiera obsługę kilku konfiguracji.

* Tworzenie konta Uruchom jako przy użyciu certyfikatu z podpisem własnym.
* Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu z podpisem własnym.
* Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu wystawionego przez urząd certyfikacji przedsiębiorstwa.
* Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu z podpisem własnym w chmurze usługi Azure Government.

Skrypt używa wielu Azure Resource Manager poleceń cmdlet do tworzenia zasobów. W przypadku poleceń cmdlet i wymaganych uprawnień należy zapoznać [się z tematem uprawnienia do konfigurowania kont Uruchom jako](#permissions-to-configure-run-as-accounts).

Zapisz skrypt na komputerze przy użyciu nazwy pliku **New-RunAsAccount. ps1**.

```powershell
#Requires -RunAsAdministrator
Param (
    [Parameter(Mandatory = $true)]
    [String] $ResourceGroup,

    [Parameter(Mandatory = $true)]
    [String] $AutomationAccountName,

    [Parameter(Mandatory = $true)]
    [String] $ApplicationDisplayName,

    [Parameter(Mandatory = $true)]
    [String] $SubscriptionId,

    [Parameter(Mandatory = $true)]
    [Boolean] $CreateClassicRunAsAccount,

    [Parameter(Mandatory = $true)]
    [String] $SelfSignedCertPlainPassword,

    [Parameter(Mandatory = $false)]
    [string] $EnterpriseCertPathForRunAsAccount,

    [Parameter(Mandatory = $false)]
    [String] $EnterpriseCertPlainPasswordForRunAsAccount,

    [Parameter(Mandatory = $false)]
    [String] $EnterpriseCertPathForClassicRunAsAccount,

    [Parameter(Mandatory = $false)]
    [String] $EnterpriseCertPlainPasswordForClassicRunAsAccount,

    [Parameter(Mandatory = $false)]
    [ValidateSet("AzureCloud", "AzureUSGovernment")]
    [string]$EnvironmentName = "AzureCloud",

    [Parameter(Mandatory = $false)]
    [int] $SelfSignedCertNoOfMonthsUntilExpired = 12
)

function CreateSelfSignedCertificate([string] $certificateName, [string] $selfSignedCertPlainPassword,
    [string] $certPath, [string] $certPathCer, [string] $selfSignedCertNoOfMonthsUntilExpired ) {
    $Cert = New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation cert:\LocalMachine\My `
        -KeyExportPolicy Exportable -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
        -NotAfter (Get-Date).AddMonths($selfSignedCertNoOfMonthsUntilExpired) -HashAlgorithm SHA256

    $CertPassword = ConvertTo-SecureString $selfSignedCertPlainPassword -AsPlainText -Force
    Export-PfxCertificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPath -Password $CertPassword -Force | Write-Verbose
    Export-Certificate -Cert ("Cert:\localmachine\my\" + $Cert.Thumbprint) -FilePath $certPathCer -Type CERT | Write-Verbose
}

function CreateServicePrincipal([System.Security.Cryptography.X509Certificates.X509Certificate2] $PfxCert, [string] $applicationDisplayName) {
    $keyValue = [System.Convert]::ToBase64String($PfxCert.GetRawCertData())
    $keyId = (New-Guid).Guid

    # Create an Azure AD application, AD App Credential, AD ServicePrincipal

    # Requires Application Developer Role, but works with Application administrator or GLOBAL ADMIN
    $Application = New-AzADApplication -DisplayName $ApplicationDisplayName -HomePage ("http://" + $applicationDisplayName) -IdentifierUris ("http://" + $keyId)
    # Requires Application administrator or GLOBAL ADMIN
    $ApplicationCredential = New-AzADAppCredential -ApplicationId $Application.ApplicationId -CertValue $keyValue -StartDate $PfxCert.NotBefore -EndDate $PfxCert.NotAfter
    # Requires Application administrator or GLOBAL ADMIN
    $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $Application.ApplicationId
    $GetServicePrincipal = Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id

    # Sleep here for a few seconds to allow the service principal application to become active (ordinarily takes a few seconds)
    Sleep -s 15
    # Requires User Access Administrator or Owner.
    $NewRole = New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
    $Retries = 0;
    While ($NewRole -eq $null -and $Retries -le 6) {
        Sleep -s 10
        New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $Application.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
        $NewRole = Get-AzRoleAssignment -ServicePrincipalName $Application.ApplicationId -ErrorAction SilentlyContinue
        $Retries++;
    }
    return $Application.ApplicationId.ToString();
}

function CreateAutomationCertificateAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $certifcateAssetName, [string] $certPath, [string] $certPlainPassword, [Boolean] $Exportable) {
    $CertPassword = ConvertTo-SecureString $certPlainPassword -AsPlainText -Force
    Remove-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $certifcateAssetName -ErrorAction SilentlyContinue
    New-AzAutomationCertificate -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Path $certPath -Name $certifcateAssetName -Password $CertPassword -Exportable:$Exportable  | write-verbose
}

function CreateAutomationConnectionAsset ([string] $resourceGroup, [string] $automationAccountName, [string] $connectionAssetName, [string] $connectionTypeName, [System.Collections.Hashtable] $connectionFieldValues ) {
    Remove-AzAutomationConnection -ResourceGroupName $resourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -Force -ErrorAction SilentlyContinue
    New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $automationAccountName -Name $connectionAssetName -ConnectionTypeName $connectionTypeName -ConnectionFieldValues $connectionFieldValues
}

Import-Module AzureRm.Profile
Import-Module AzureRm.Resources

$AureRmProfileVersion = (Get-Module AzureRm.Profile).Version
if (!(($AzureRmProfileVersion.Major -ge 3 -and $AzureRmProfileVersion.Minor -ge 4) -or ($AzureRmProfileVersion.Major -gt 3))) {
    Write-Error -Message "Please install the latest Azure PowerShell and retry. Relevant doc url : https://docs.microsoft.com/powershell/azureps-cmdlets-docs/ "
    return
}

# To use the new Az modules to create your Run As accounts, please uncomment the following lines and ensure you comment out the previous 8 lines that import the AzureRM modules to avoid any issues. To learn about about using Az modules in your Automation account see https://docs.microsoft.com/azure/automation/az-modules.

# Import-Module Az.Automation
# Enable-AzureRmAlias


Connect-AzAccount -Environment $EnvironmentName
$Subscription = Get-AzSubscription -SubscriptionId $SubscriptionId

# Create a Run As account by using a service principal
$CertifcateAssetName = "AzureRunAsCertificate"
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionTypeName = "AzureServicePrincipal"

if ($EnterpriseCertPathForRunAsAccount -and $EnterpriseCertPlainPasswordForRunAsAccount) {
    $PfxCertPathForRunAsAccount = $EnterpriseCertPathForRunAsAccount
    $PfxCertPlainPasswordForRunAsAccount = $EnterpriseCertPlainPasswordForRunAsAccount
}
else {
    $CertificateName = $AutomationAccountName + $CertifcateAssetName
    $PfxCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".pfx")
    $PfxCertPlainPasswordForRunAsAccount = $SelfSignedCertPlainPassword
    $CerCertPathForRunAsAccount = Join-Path $env:TEMP ($CertificateName + ".cer")
    CreateSelfSignedCertificate $CertificateName $PfxCertPlainPasswordForRunAsAccount $PfxCertPathForRunAsAccount $CerCertPathForRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
}

# Create a service principal
$PfxCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($PfxCertPathForRunAsAccount, $PfxCertPlainPasswordForRunAsAccount)
$ApplicationId = CreateServicePrincipal $PfxCert $ApplicationDisplayName

# Create the Automation certificate asset
CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $CertifcateAssetName $PfxCertPathForRunAsAccount $PfxCertPlainPasswordForRunAsAccount $true

# Populate the ConnectionFieldValues
$SubscriptionInfo = Get-AzSubscription -SubscriptionId $SubscriptionId
$TenantID = $SubscriptionInfo | Select TenantId -First 1
$Thumbprint = $PfxCert.Thumbprint
$ConnectionFieldValues = @{"ApplicationId" = $ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Thumbprint; "SubscriptionId" = $SubscriptionId}

# Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ConnectionAssetName $ConnectionTypeName $ConnectionFieldValues

if ($CreateClassicRunAsAccount) {
    # Create a Run As account by using a service principal
    $ClassicRunAsAccountCertifcateAssetName = "AzureClassicRunAsCertificate"
    $ClassicRunAsAccountConnectionAssetName = "AzureClassicRunAsConnection"
    $ClassicRunAsAccountConnectionTypeName = "AzureClassicCertificate "
    $UploadMessage = "Please upload the .cer format of #CERT# to the Management store by following the steps below." + [Environment]::NewLine +
    "Log in to the Microsoft Azure portal (https://portal.azure.com) and select Subscriptions -> Management Certificates." + [Environment]::NewLine +
    "Then click Upload and upload the .cer format of #CERT#"

    if ($EnterpriseCertPathForClassicRunAsAccount -and $EnterpriseCertPlainPasswordForClassicRunAsAccount ) {
        $PfxCertPathForClassicRunAsAccount = $EnterpriseCertPathForClassicRunAsAccount
        $PfxCertPlainPasswordForClassicRunAsAccount = $EnterpriseCertPlainPasswordForClassicRunAsAccount
        $UploadMessage = $UploadMessage.Replace("#CERT#", $PfxCertPathForClassicRunAsAccount)
    }
    else {
        $ClassicRunAsAccountCertificateName = $AutomationAccountName + $ClassicRunAsAccountCertifcateAssetName
        $PfxCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".pfx")
        $PfxCertPlainPasswordForClassicRunAsAccount = $SelfSignedCertPlainPassword
        $CerCertPathForClassicRunAsAccount = Join-Path $env:TEMP ($ClassicRunAsAccountCertificateName + ".cer")
        $UploadMessage = $UploadMessage.Replace("#CERT#", $CerCertPathForClassicRunAsAccount)
        CreateSelfSignedCertificate $ClassicRunAsAccountCertificateName $PfxCertPlainPasswordForClassicRunAsAccount $PfxCertPathForClassicRunAsAccount $CerCertPathForClassicRunAsAccount $SelfSignedCertNoOfMonthsUntilExpired
    }
    
    # Create the Automation certificate asset
    CreateAutomationCertificateAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountCertifcateAssetName $PfxCertPathForClassicRunAsAccount $PfxCertPlainPasswordForClassicRunAsAccount $false

    # Populate the ConnectionFieldValues
    $SubscriptionName = $subscription.Subscription.Name
    $ClassicRunAsAccountConnectionFieldValues = @{"SubscriptionName" = $SubscriptionName; "SubscriptionId" = $SubscriptionId; "CertificateAssetName" = $ClassicRunAsAccountCertifcateAssetName}

    # Create an Automation connection asset named AzureRunAsConnection in the Automation account. This connection uses the service principal.
    CreateAutomationConnectionAsset $ResourceGroup $AutomationAccountName $ClassicRunAsAccountConnectionAssetName $ClassicRunAsAccountConnectionTypeName   $ClassicRunAsAccountConnectionFieldValues

    Write-Host -ForegroundColor red       $UploadMessage
}
```

>[!NOTE]
>`Add-AzAccount`i `Add-AzureRMAccount` są aliasami dla programu [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Możesz użyć tych poleceń cmdlet lub [zaktualizować moduły](automation-update-azure-modules.md) na koncie usługi Automation do najnowszych wersji. Może być konieczne zaktualizowanie modułów, nawet jeśli utworzono nowe konto usługi Automation.

### <a name="execute-the-powershell-script"></a>Wykonywanie skryptu programu PowerShell

1. Na komputerze uruchom program **Windows PowerShell** z ekranu **Start** z podwyższonym poziomem uprawnień użytkownika.
1. W powłoce wiersza polecenia z podwyższonym poziomem uprawnień przejdź do folderu, który zawiera skrypt.
1. Wykonaj skrypt, używając wartości parametrów dla wymaganej konfiguracji.
1. W przypadku tworzenia klasycznego konta Uruchom jako po wykonaniu skryptu Przekaż certyfikat publiczny (rozszerzenie nazwy pliku **. cer** ) do magazynu zarządzania dla subskrypcji, w ramach której zostało utworzone konto usługi Automation.

Po wykonaniu skryptu zostanie wyświetlony monit o uwierzytelnienie na platformie Azure. Zaloguj się przy użyciu konta, które jest członkiem roli Administratorzy subskrypcji i współadministratorem subskrypcji.

#### <a name="create-a-run-as-account-by-using-a-self-signed-certificate"></a>Tworzenie konta Uruchom jako przy użyciu certyfikatu z podpisem własnym

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate"></a>Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu z podpisem własnym

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-an-enterprise-certificate"></a>Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu przedsiębiorstwa

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
```

Jeśli utworzono klasyczne konto Uruchom jako z certyfikatem publicznym przedsiębiorstwa (plik **. cer** ), użyj tego certyfikatu. Zobacz [przekazywanie certyfikatu interfejsu API zarządzania do Azure Portal](../azure-api-management-certs.md).

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate-in-the-azure-government-cloud"></a>Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu z podpisem własnym w chmurze usługi Azure Government

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
```

Jeśli utworzono klasyczne konto Uruchom jako z certyfikatem publicznym z podpisem własnym (plik **. cer** ), skrypt tworzy i zapisuje go w folderze plików tymczasowych na komputerze. Można go znaleźć w profilu `%USERPROFILE%\AppData\Local\Temp`użytkownika, który został użyty do wykonania sesji programu PowerShell.

## <a name="deleting-a-run-as-or-classic-run-as-account"></a>Usuwanie Uruchom jako lub klasycznego konta Uruchom jako

W tej sekcji opisano, jak usunąć konto Uruchom jako lub klasyczne. Konto usługi Automation jest zachowywane podczas wykonywania tej akcji. Po usunięciu konta możesz je ponownie utworzyć w Azure Portal.

1. W witrynie Azure Portal otwórz konto usługi Automation.

2. W okienku po lewej stronie wybierz pozycję **konta Uruchom jako** w sekcji Ustawienia konta.

3. Na stronie właściwości Konta Uruchom jako wybierz konto Uruchom jako albo klasyczne konto Uruchom jako, które chcesz usunąć. 

4. W okienku właściwości wybranego konta kliknij pozycję **Usuń**.

   ![Usuwanie konta Uruchom jako](media/manage-runas-account/automation-account-delete-runas.png)

5. W trakcie usuwania konta postęp można śledzić po wybraniu z menu opcji **Powiadomienia**.

6. Po usunięciu konta możesz je ponownie utworzyć na stronie właściwości Konta Uruchom jako, wybierając opcję tworzenia **Konto Uruchom jako platformy Azure**.

   ![Ponowne tworzenie konta Uruchom jako usługi Automation](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renewing-a-self-signed-certificate"></a><a name="cert-renewal"></a>Odnawianie certyfikatu z podpisem własnym

Certyfikat z podpisem własnym utworzony dla konta Uruchom jako wygasa w jednym roku od daty utworzenia. W pewnym momencie przed wygaśnięciem konta Uruchom jako należy odnowić certyfikat. Można je odnowić w dowolnym momencie przed wygaśnięciem. 

W przypadku odnowienia certyfikatu z podpisem własnym bieżący prawidłowy certyfikat jest zachowywany, aby upewnić się, że wszystkie elementy Runbook, które zostały dodane do kolejki lub aktywnie uruchomione, i które uwierzytelniają się przy użyciu konta Uruchom jako, nie mają negatywnego wpływu. Certyfikat pozostanie ważny aż do daty wygaśnięcia.

>[!NOTE]
>Jeśli uważasz, że zabezpieczenia konta Uruchom jako zostały naruszone, możesz usunąć i ponownie utworzyć certyfikat z podpisem własnym.

>[!NOTE]
>Jeśli konto Uruchom jako zostało skonfigurowane do użycia certyfikatu wystawionego przez urząd certyfikacji przedsiębiorstwa i używasz opcji odnawiania certyfikatu z podpisem własnym, certyfikat przedsiębiorstwa zostanie zastąpiony certyfikatem z podpisem własnym.

Aby odnowić certyfikat z podpisem własnym, wykonaj następujące czynności.

1. W witrynie Azure Portal otwórz konto usługi Automation.

1. Wybierz pozycję **konta Uruchom jako** w sekcji Ustawienia konta.

    ![Okienko właściwości konta usługi Automation](media/manage-runas-account/automation-account-properties-pane.png)

1. Na stronie właściwości konta Uruchom jako wybierz konto Uruchom jako lub klasyczne konto Uruchom jako, dla którego chcesz odnowić certyfikat.

1. W okienku właściwości wybranego konta kliknij pozycję **odnów certyfikat**.

    ![Odnawianie certyfikatu konta Uruchom jako](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. W trakcie odnawiania certyfikatu postęp można śledzić po wybraniu z menu opcji **Powiadomienia**.

## <a name="setting-up-automatic-certificate-renewal-with-an-automation-runbook"></a><a name="auto-cert-renewal"></a>Konfigurowanie automatycznego odnawiania certyfikatów przy użyciu elementu Runbook usługi Automation

Aby automatycznie odnowić certyfikaty, można użyć elementu Runbook usługi Automation. Ten skrypt w serwisie [GitHub](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) umożliwia korzystanie z tej funkcji na koncie usługi Automation.

>[!NOTE]
>Aby wykonać skrypt, musisz być administratorem globalnym lub administratorem firmy w usłudze Azure AD.

Ten skrypt tworzy tygodniowy harmonogram odnawiania certyfikatów konta Uruchom jako. Dodaje element Runbook **Update-AutomationRunAsCredential** do konta usługi Automation. Kod elementu Runbook można wyświetlić w witrynie GitHub w skrypcie [Update-AutomationRunAsCredential. ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1). W razie potrzeby można użyć kodu programu PowerShell w pliku do ręcznego odnowienia certyfikatów.

Wykonaj następujące kroki, aby natychmiast przetestować proces odnawiania.

1. Edytuj element Runbook **Update-AutomationRunAsCredential** i umieść znak komentarza (#) w wierszu 122 przed poleceniem **Exit (1)** .

   ```powershell
   #Exit(1)
   ```

2. Opublikuj element Runbook.
3. Uruchom element Runbook.
4. Zweryfikuj pomyślne odnowienie przy użyciu następującego kodu:

   ```powershell
   (Get-AzAutomationCertificate -AutomationAccountName TestAA
                                -Name AzureRunAsCertificate
                                -ResourceGroupName TestAutomation).ExpiryTime.DateTime
   ```
    Dane wyjściowe:

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. Po zakończeniu testu Edytuj element Runbook, a następnie Usuń znak komentarza dodany w kroku 1.
6. Opublikuj element Runbook.

## <a name="limiting-run-as-account-permissions"></a><a name="limiting-run-as-account-permissions"></a>Ograniczanie uprawnień konta Uruchom jako

Aby kontrolować Określanie wartości docelowej automatyzacji względem zasobów na platformie Azure, można uruchomić skrypt [Update-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug8) . Ten skrypt zmienia istniejącą nazwę główną usługi konta Uruchom jako, aby utworzyć niestandardową definicję roli i korzystać z niej. Rola ma uprawnienia do wszystkich zasobów, z wyjątkiem [Key Vault](https://docs.microsoft.com/azure/key-vault/).

>[!IMPORTANT]
>Po uruchomieniu skryptu **Update-AutomationRunAsAccountRoleAssignments. ps1** elementy Runbook, które uzyskują dostęp do Key Vault za pomocą kont Uruchom jako, nie będą już działać. Przed uruchomieniem skryptu należy przejrzeć elementy Runbook na koncie w celu wywołania Azure Key Vault. Aby umożliwić dostęp do Key Vault z Azure Automation elementów Runbook, należy [dodać konto Uruchom jako do uprawnień Key Vault](#add-permissions-to-key-vault).

Jeśli trzeba będzie ograniczyć, jak może to zrobić jednostka usługi Uruchom jako, możesz dodać inne typy zasobów do `NotActions` elementu definicji roli niestandardowej. Poniższy przykład ogranicza dostęp do `Microsoft.Compute/*`programu. W przypadku dodania tego typu zasobu do `NotActions` definicji roli nie będzie można uzyskać dostępu do żadnego zasobu obliczeniowego. Aby dowiedzieć się więcej na temat definicji ról, zobacz [Omówienie definicji ról dla zasobów platformy Azure](../role-based-access-control/role-definitions.md).

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Można określić, czy nazwa główna usługi używana przez konto Uruchom jako jest w definicji roli współautor, czy też niestandardowa. 

1. Przejdź do konta usługi Automation i wybierz pozycję **konta Uruchom jako** w sekcji Ustawienia konta.
2. Wybierz pozycję **konto Uruchom jako platformy Azure**. 
3. Wybierz **rolę** , aby zlokalizować używaną definicję roli.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

Można również określić definicję roli używaną przez konta Uruchom jako dla wielu subskrypcji lub kont usługi Automation. W tym celu należy użyć skryptu [check-AutomationRunAsAccountRoleAssignments. ps1](https://aka.ms/AA5hug5) w Galeria programu PowerShell.

### <a name="add-permissions-to-key-vault"></a>Dodawanie uprawnień do Key Vault

Możesz zezwolić Azure Automation, aby sprawdzić, czy Key Vault i nazwa główna usługi konta Uruchom jako używają niestandardowej definicji roli. Należy:

* Udziel uprawnień do Key Vault.
* Ustaw zasady dostępu.

Możesz użyć skryptu [extend-AutomationRunAsAccountRoleAssignmentToKeyVault. ps1](https://aka.ms/AA5hugb) w Galeria programu PowerShell, aby nadać kontu Uruchom jako uprawnienia do Key Vault. Aby uzyskać więcej informacji na temat ustawiania uprawnień dla Key Vault [, zobacz Udzielanie aplikacjom dostępu do magazynu kluczy](../key-vault/general/group-permissions-for-apps.md) .

## <a name="resolving-misconfiguration-issues-for-run-as-accounts"></a>Rozwiązywanie problemów z błędami konfiguracji dla kont Uruchom jako

Niektóre elementy konfiguracji niezbędne do uruchomienia jako lub klasycznego konta Uruchom jako mogły zostać usunięte lub utworzone nieprawidłowo podczas konfiguracji początkowej. Możliwe wystąpienia niepozostałej konfiguracji obejmują:

* Zasób certyfikatu
* Zasób połączenia
* Konto Uruchom jako zostało usunięte z roli współautor
* Nazwa główna usługi lub aplikacji w usłudze Azure AD

W przypadku takich nieprawidłowych wystąpień konfiguracji konto usługi Automation wykrywa zmiany i wyświetla stan `Incomplete` w okienku właściwości konta Uruchom jako dla konta.

![Stan Niekompletne dla konfiguracji konta Uruchom jako](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Po wybraniu konta Uruchom jako w okienku właściwości konta zostanie wyświetlony następujący komunikat o błędzie:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Te problemy związane z kontem Uruchom jako można szybko rozwiązać, usuwając konto i tworząc je ponownie.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat jednostek usługi, zobacz [obiekty aplikacji i obiekty główne usługi](../active-directory/develop/app-objects-and-service-principals.md).
* Aby uzyskać więcej informacji na temat certyfikatów i usług Azure, zobacz [Omówienie certyfikatów dla platformy Azure Cloud Services](../cloud-services/cloud-services-certs-create.md).
