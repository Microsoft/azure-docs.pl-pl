---
title: Zarządzanie uruchamianiem automatyzacji platformy Azure jako kontami
description: W tym artykule opisano sposób zarządzania kontami Uruchom jako za pomocą programu PowerShell lub z portalu.
services: automation
ms.subservice: shared-capabilities
ms.date: 05/24/2019
ms.topic: conceptual
ms.openlocfilehash: 8d2c2f1e7ee10153108e54649ceba45b927b0cd2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676627"
---
# <a name="manage-azure-automation-run-as-accounts"></a>Zarządzanie uruchamianiem automatyzacji platformy Azure jako kontami

Uruchom jako konta w usłudze Azure Automation zapewniają uwierzytelnianie do zarządzania zasobami na platformie Azure przy użyciu poleceń cmdlet platformy Azure. Podczas tworzenia konta Uruchom jako, tworzy nowego użytkownika jednostki usługi w usłudze Azure Active Directory (AD) i przypisuje rolę współautora do tego użytkownika na poziomie subskrypcji. W przypadku kreśliń korzystających z hybrydowych procesów zarządzania życzna na [maszynach wirtualnych](automation-hrw-run-runbooks.md#managed-identities-for-azure-resources) platformy Azure można używać tożsamości zarządzanych dla zasobów platformy Azure zamiast kont Uruchom jako do uwierzytelniania zasobów platformy Azure.

Podmiot usługi dla uruchom jako konto nie ma uprawnień do odczytu usługi Azure AD domyślnie. Jeśli chcesz dodać uprawnienia do odczytu usługi Azure AD lub zarządzania nią, musisz udzielić uprawnień do jednostki usługi w obszarze **Uprawnienia interfejsu API.** Aby dowiedzieć się więcej, zobacz [Dodawanie uprawnień dostępu do internetowych interfejsów API](../active-directory/develop/quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis).

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="types-of-run-as-accounts"></a>Typy kont uruchom jako

Usługa Azure Automation używa dwóch typów kont Uruchom jako:

* Konto Uruchom jako platformy Azure
* Konto Azure Classic Run As

>[!NOTE]
>Subskrypcje dostawcy rozwiązań w chmurze platformy Azure (CSP) obsługują tylko model usługi Azure Resource Manager. Usługi usługi Usługi Resource Manager inne niż Azure nie są dostępne w programie. Podczas korzystania z subskrypcji CSP nie jest tworzone konto Azure Classic Run As, ale zostanie utworzone konto Azure Run As. Aby dowiedzieć się więcej o subskrypcjach usług CSP, zobacz [Dostępne usługi w subskrypcjach usług CSP](https://docs.microsoft.com/azure/cloud-solution-provider/overview/azure-csp-available-services).

### <a name="run-as-account"></a>Konto Uruchom jako

Konto Uruchom jako zarządza zasobami [modelu wdrażania Menedżera zasobów.](../azure-resource-manager/management/deployment-models.md) Wykonuje następujące zadania.

* Tworzy aplikację usługi Azure AD z certyfikatem z podpisem własnym, tworzy konto jednostki usługi dla aplikacji w usłudze Azure AD i przypisuje rolę Współautor dla konta w bieżącej subskrypcji. Można zmienić ustawienie certyfikatu na Właściciel lub dowolną inną rolę. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu oparta na rolach w usłudze Azure Automation](automation-role-based-access-control.md).
  
* Tworzy zasób `AzureRunAsCertificate` certyfikatu automatyzacji nazwany na określonym koncie automatyzacji. Zasób certyfikatu przechowuje klucz prywatny certyfikatu używany przez aplikację usługi Azure AD.
  
* Tworzy zasób `AzureRunAsConnection` połączenia automatyzacji o nazwie na określonym koncie automatyzacji. Zasób połączenia przechowuje identyfikator aplikacji, identyfikator dzierżawy, identyfikator subskrypcji i odcisk palca certyfikatu.

### <a name="azure-classic-run-as-account"></a>Klasyczne konto Uruchom jako platformy Azure

Konto Azure Classic Run As zarządza zasobami [modelu wdrażania klasycznego.](../azure-resource-manager/management/deployment-models.md) Aby utworzyć lub odnowić konto tego typu, musisz być współadministratorem w ramach subskrypcji.

Konto Azure Classic Run As wykonuje następujące zadania.

  * Tworzy certyfikat zarządzania w subskrypcji.

  * Tworzy zasób `AzureClassicRunAsCertificate` certyfikatu automatyzacji nazwany na określonym koncie automatyzacji. Zasób certyfikatu zawiera klucz prywatny certyfikatu używany przez certyfikat zarządzania.

  * Tworzy zasób `AzureClassicRunAsConnection` połączenia automatyzacji o nazwie na określonym koncie automatyzacji. Zasób połączenia zawiera nazwę subskrypcji, identyfikator subskrypcji i nazwę zasobu certyfikatu.

## <a name="run-as-account-permissions"></a><a name="permissions"></a>Uruchom jako uprawnienia do konta

W tej sekcji zdefiniowano uprawnienia zarówno dla zwykłych kont Uruchom jako, jak i kont Classic Run As.

### <a name="permissions-to-configure-run-as-accounts"></a>Uprawnienia do konfigurowania kont Uruchom jako

Aby utworzyć lub zaktualizować konto Uruchom jako, musisz mieć określone uprawnienia i uprawnienia. Administrator aplikacji w usłudze Azure Active Directory i właściciel w ramach subskrypcji mogą wykonywać wszystkie zadania. W sytuacji, gdy masz separacji obowiązków, w poniższej tabeli przedstawiono listę zadań, równoważne polecenie cmdlet i uprawnienia potrzebne:

|Zadanie|Polecenie cmdlet  |Minimalne uprawnienia  |Gdzie można ustawić uprawnienia|
|---|---------|---------|---|
|Tworzenie aplikacji usługi Azure AD|[Nowa-AzADZastosowanie](https://docs.microsoft.com/powershell/module/az.resources/new-azadapplication)     | Rola dewelopera aplikacji<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Rejestracje aplikacji > usługi Azure > > Azure |
|Dodaj poświadczenia do aplikacji.|[Nowy-AzADAppCredential](https://docs.microsoft.com/powershell/module/az.resources/new-azadappcredential)     | Administrator aplikacji lub administrator globalny<sup>1</sup>         |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Rejestracje aplikacji > usługi Azure > > Azure|
|Tworzenie i odbycie jednostki usługi Azure AD|[New-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/new-azadserviceprincipal)</br>[Get-AzADServicePrincipal](https://docs.microsoft.com/powershell/module/az.resources/get-azadserviceprincipal)     | Administrator aplikacji lub administrator globalny<sup>1</sup>        |[Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)</br>Rejestracje aplikacji > usługi Azure > > Azure|
|Przypisywanie lub uzyskanie roli RBAC dla określonego podmiotu zabezpieczeń|[New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)</br>[Get-AzRoleAssignment](https://docs.microsoft.com/powershell/module/Az.Resources/Get-AzRoleAssignment)      | Administrator lub właściciel dostępu użytkownika lub mieć następujące uprawnienia:</br></br><code>Microsoft.Authorization/Operations/read</br>Microsoft.Authorization/permissions/read</br>Microsoft.Authorization/roleDefinitions/read</br>Microsoft.Authorization/roleAssignments/write</br>Microsoft.Authorization/roleAssignments/read</br>Microsoft.Authorization/roleAssignments/delete</code></br></br> | [Subskrypcja](../role-based-access-control/role-assignments-portal.md)</br>Subskrypcje > > \<-\> Kontrola dostępu (IAM)|
|Tworzenie lub usuwanie certyfikatu automatyzacji|[Nowy certyfikat AzAutomationCertificate](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationCertificate)</br>[Usuń-AzAutomationCertyfikat](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationcertificate)     | Współautor w grupie zasobów         |Grupa zasobów kont automatyzacji|
|Tworzenie lub usuwanie połączenia automatyzacji|[Nowe połączenie AzAutomation](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection)</br>[Usuń-AzAutomationPołączenie](https://docs.microsoft.com/powershell/module/az.automation/remove-azautomationconnection)|Współautor w grupie zasobów |Grupa zasobów kont automatyzacji|

<sup>1</sup> Użytkownicy niebędący administratorami w dzierżawie usługi Azure AD mogą [rejestrować aplikacje usługi AD,](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions) jeśli opcja Użytkownicy dzierżawy usługi Azure AD **może rejestrować aplikacje** na stronie Ustawienia użytkownika jest **ustawiona**na Tak . Jeśli ustawienie rejestracji aplikacji to **Nie,** użytkownik wykonujący tę akcję musi być zdefiniowany w tej tabeli.

Jeśli nie jesteś członkiem wystąpienia usługi Active Directory subskrypcji przed dodaniem do roli administratora globalnego subskrypcji, zostaniesz dodany jako gość. W tej sytuacji otrzymasz `You do not have permissions to create…` ostrzeżenie na stronie Dodaj konto automatyzacji. 

Jeśli jesteś członkiem wystąpienia usługi Active Directory subskrypcji podczas przypisywania roli administratora `You do not have permissions to create…` globalnego, możesz również otrzymać ostrzeżenie na stronie Dodaj konto automatyzacji. W takim przypadku można zażądać usunięcia z wystąpienia usługi Active Directory subskrypcji, a następnie zażądać ponownego dodania, aby stać się pełnoprawnym użytkownikiem w usłudze Active Directory.

Aby sprawdzić, czy sytuacja powodująca komunikat o błędzie została naprawiona:

1. W okienku usługi Azure Active Directory w witrynie Azure portal wybierz **pozycję Użytkownicy i grupy**. 
2. Wybierz **pozycję Wszyscy użytkownicy**.
3. Wybierz swoje imię i nazwisko, a następnie wybierz **profil**. 
4. Upewnij się, że wartość atrybutu **Typu Użytkownika** w profilu użytkownika nie jest ustawiona na **Gość**.

### <a name="permissions-to-configure-classic-run-as-accounts"></a><a name="permissions-classic"></a>Uprawnienia do konfigurowania kont Classic Run As

Aby skonfigurować lub odnowić konta Klasyczne uruchamianie jako, musisz mieć rolę współadministratora na poziomie subskrypcji. Aby dowiedzieć się więcej o klasycznych uprawnieniach subskrypcji, zobacz [Administratorzy subskrypcji klasycznej platformy Azure](../role-based-access-control/classic-administrators.md#add-a-co-administrator).

## <a name="creating-a-run-as-account-in-azure-portal"></a>Tworzenie konta Uruchom jako w witrynie Azure portal

Wykonaj następujące kroki, aby zaktualizować swoje konto usługi Azure Automation w witrynie Azure portal. Utwórz konta Uruchom jako i Klasycznie jako konta indywidualnie. Jeśli nie trzeba zarządzać zasobami klasycznymi, można po prostu utworzyć konto Uruchom jako platformy Azure.

1. Zaloguj się do portalu Azure przy użyciu konta, które jest członkiem roli Administratorzy subskrypcji i współadministratorem subskrypcji.
2. Wyszukaj i wybierz **pozycję Konta automatyzacji**.
3. Na stronie Konta automatyzacji wybierz konto automatyzacji z listy.
4. W lewym okienku wybierz pozycję **Uruchom jako konta** w sekcji Ustawienia konta.
5. W zależności od tego, które konto jest wymagane, wybierz pozycję **Konto Uruchom jako platformy Azure** lub **Klasyczne konto Uruchom jako platformy Azure**. 
6. W zależności od interesującego konta użyj okienka **Dodaj usługę Azure Uruchom jako** lub Dodaj **klasyczne uruchamianie usługi Azure jako konto.** Po zapoznaniu się z informacjami o przeglądzie kliknij przycisk **Utwórz**.
6. W trakcie tworzenia konta Uruchom jako na platformie Azure postęp można śledzić po wybraniu z menu opcji **Powiadomienia**. Wyświetlany jest również baner informujący o utworzeniu konta. Proces może potrwać kilka minut.

## <a name="creating-a-run-as-account-using-powershell"></a>Tworzenie konta Uruchom jako przy użyciu programu PowerShell

Poniższa lista zawiera wymagania dotyczące tworzenia konta Uruchom jako w programie PowerShell. Te wymagania dotyczą obu typów kont Uruchom jako.

* Windows 10 lub Windows Server 2016 z modułami usługi Azure Resource Manager 3.4.1 lub nowszymi. Skrypt programu PowerShell nie obsługuje wcześniejszych wersji systemu Windows.
* Program Azure PowerShell 1.0 lub nowszy. Informacje o wersji PowerShell 1.0 można znaleźć w temacie [How to install and configure Azure PowerShell](/powershell/azureps-cmdlets-docs) (Jak zainstalować i skonfigurować program Azure PowerShell).
* Konto automatyzacji, do którego odwołuje się `AutomationAccountName` `ApplicationDisplayName` wartość i parametry.
* Uprawnienia równoważne tym wymienionym w [sekcji Wymagane uprawnienia do konfigurowania kont Uruchom jako](#permissions).

Aby uzyskać wartości `SubscriptionId` `ResourceGroupName`dla , i , który jest wymagany parametrów dla skryptu programu PowerShell, wykonaj następne kroki.

1. W witrynie Azure portal wybierz pozycję **Konta automatyzacji**.
1. Na stronie Konta automatyzacji wybierz konto automatyzacji.
1. W sekcji Ustawienia konta wybierz pozycję **Właściwości**.
1. Zanotuj wartości **name,** **subscription i** **resource group** na stronie Właściwości. Wartości te odpowiadają wartościom `AutomationAccountName` `SubscriptionId`odpowiednio `ResourceGroupName` dla parametrów skryptu programu , i programu PowerShell.

   ![Strona właściwości konta automatyzacji](media/manage-runas-account/automation-account-properties.png)

### <a name="powershell-script-to-create-a-run-as-account"></a>Skrypt programu PowerShell do utworzenia konta Uruchom jako

Ta sekcja zawiera skrypt programu PowerShell do utworzenia konta Uruchom jako. Skrypt zawiera obsługę kilku konfiguracji.

* Tworzenie konta Uruchom jako przy użyciu certyfikatu z podpisem własnym.
* Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu z podpisem własnym.
* Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu wystawionego przez urząd certyfikacji przedsiębiorstwa.
* Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu z podpisem własnym w chmurze usługi Azure Government.

Skrypt używa wielu poleceń cmdlet usługi Azure Resource Manager do tworzenia zasobów. Aby zapoznać się z poleceniami cmdlet i wymaganymi uprawnieniami, zobacz [Uprawnienia do konfigurowania kont Uruchom jako](#permissions-to-configure-run-as-accounts).

Zapisz skrypt na komputerze przy użyciu nazwy pliku **New-RunAsAccount.ps1**.

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
>`Add-AzAccount`i `Add-AzureRMAccount` są aliasy dla [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Można użyć tych poleceń cmdlet lub można [zaktualizować moduły](automation-update-azure-modules.md) na koncie automatyzacji do najnowszych wersji. Może być konieczne zaktualizowanie modułów, nawet jeśli właśnie utworzono nowe konto automatyzacji.

### <a name="execute-the-powershell-script"></a>Wykonywanie skryptu programu PowerShell

1. Na komputerze uruchom program **Windows PowerShell** z ekranu **Start** z podwyższonym poziomem uprawnień użytkownika.
1. Z podwyższonej osłupionej powłoki wiersza polecenia przejdź do folderu zawierającego skrypt.
1. Wykonaj skrypt przy użyciu wartości parametrów dla wymaganej konfiguracji.
1. W przypadku tworzenia konta Classic Run As po wykonaniu skryptu należy przekazać publiczny certyfikat (**.cer** filename extension) do magazynu zarządzania dla subskrypcji, w której utworzono konto Automatyzacja.

Po wykonaniu skryptu zostanie wyświetlony monit o uwierzytelnienie za pomocą platformy Azure. Zaloguj się przy za pomocą konta, które jest członkiem roli administratorów subskrypcji i współadministratorem subskrypcji.

#### <a name="create-a-run-as-account-by-using-a-self-signed-certificate"></a>Tworzenie konta Uruchom jako przy użyciu certyfikatu z podpisem własnym

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $false
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate"></a>Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu z podpisem własnym

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true
```

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-an-enterprise-certificate"></a>Tworzenie konta uruchom jako i klasycznego konta uruchom jako przy użyciu certyfikatu przedsiębiorstwa

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication>  -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true -EnterpriseCertPathForRunAsAccount <EnterpriseCertPfxPathForRunAsAccount> -EnterpriseCertPlainPasswordForRunAsAccount <StrongPassword> -EnterpriseCertPathForClassicRunAsAccount <EnterpriseCertPfxPathForClassicRunAsAccount> -EnterpriseCertPlainPasswordForClassicRunAsAccount <StrongPassword>
```

Jeśli utworzono konto Classic Run As z certyfikatem publicznym przedsiębiorstwa ( plik**cer),** użyj tego certyfikatu. Zobacz [Przekazywanie certyfikatu interfejsu API zarządzania do witryny Azure portal](../azure-api-management-certs.md).

#### <a name="create-a-run-as-account-and-a-classic-run-as-account-by-using-a-self-signed-certificate-in-the-azure-government-cloud"></a>Tworzenie konta Uruchom jako i klasycznego konta Uruchom jako przy użyciu certyfikatu z podpisem własnym w chmurze usługi Azure Government

```powershell
.\New-RunAsAccount.ps1 -ResourceGroup <ResourceGroupName> -AutomationAccountName <NameofAutomationAccount> -SubscriptionId <SubscriptionId> -ApplicationDisplayName <DisplayNameofAADApplication> -SelfSignedCertPlainPassword <StrongPassword> -CreateClassicRunAsAccount $true  -EnvironmentName AzureUSGovernment
```

Jeśli utworzono konto Classic Run As z certyfikatem publicznym z podpisem własnym ( plik**cer),** skrypt utworzy go i zapisze w folderze plików tymczasowych na komputerze. Można go znaleźć w `%USERPROFILE%\AppData\Local\Temp`profilu użytkownika , który został użyty do wykonania sesji programu PowerShell.

## <a name="deleting-a-run-as-or-classic-run-as-account"></a>Usuwanie konta Uruchom jako lub Klasyczne uruchom jako

W tej sekcji opisano sposób usuwania konta Uruchom jako lub Klasyczne uruchom jako. Konto usługi Automation jest zachowywane podczas wykonywania tej akcji. Po usunięciu konta można ponownie utworzyć je w witrynie Azure portal.

1. W witrynie Azure Portal otwórz konto usługi Automation.

2. W lewym okienku wybierz pozycję **Uruchom jako konta** w sekcji Ustawienia konta.

3. Na stronie właściwości Konta Uruchom jako wybierz konto Uruchom jako albo klasyczne konto Uruchom jako, które chcesz usunąć. 

4. W okienku Właściwości dla wybranego konta kliknij pozycję **Usuń**.

   ![Usuwanie konta Uruchom jako](media/manage-runas-account/automation-account-delete-runas.png)

5. W trakcie usuwania konta postęp można śledzić po wybraniu z menu opcji **Powiadomienia**.

6. Po usunięciu konta możesz je ponownie utworzyć na stronie właściwości Konta Uruchom jako, wybierając opcję tworzenia **Konto Uruchom jako platformy Azure**.

   ![Ponowne tworzenie konta Uruchom jako usługi Automation](media/manage-runas-account/automation-account-create-runas.png)

## <a name="renewing-a-self-signed-certificate"></a><a name="cert-renewal"></a>Odnawianie certyfikatu z podpisem własnym

Certyfikat z podpisem własnym utworzony dla konta Uruchom jako wygasa po upływie jednego roku od daty utworzenia. W pewnym momencie przed wygaśnięciem konta Uruchom jako należy odnowić certyfikat. Można go odnowić w dowolnym momencie przed jego wygaśnięciem. 

Po odnowieniu certyfikatu z podpisem własnym bieżący prawidłowy certyfikat jest zachowywany w celu zapewnienia, że wszystkie umowy runbook, które są w kolejce lub aktywnie uruchomione, a uwierzytelniają się za pomocą konta Uruchom jako, nie są negatywnie zagrożone. Certyfikat pozostanie ważny aż do daty wygaśnięcia.

>[!NOTE]
>Jeśli uważasz, że konto Uruchom jako zostało naruszone, możesz usunąć i ponownie utworzyć certyfikat z podpisem własnym.

>[!NOTE]
>Jeśli konto Uruchom jako zostało skonfigurowane do używania certyfikatu wystawionego przez urząd certyfikacji przedsiębiorstwa i użyjesz opcji odnowienia opcji certyfikatu z podpisem własnym, certyfikat przedsiębiorstwa zostanie zastąpiony certyfikatem z podpisem własnym.

Aby odnowić certyfikat z podpisem własnym, należy wykonać następujące kroki.

1. W witrynie Azure Portal otwórz konto usługi Automation.

1. Wybierz **pozycję Uruchom jako konta** w sekcji Ustawienia konta.

    ![Okienko właściwości konta usługi Automation](media/manage-runas-account/automation-account-properties-pane.png)

1. Na stronie Właściwości Uruchom jako konta wybierz konto Uruchom jako lub Konto Klasyczne Uruchom jako, dla którego ma zostać odnowiony certyfikat.

1. W okienku właściwości dla wybranego konta kliknij przycisk **Odnów certyfikat**.

    ![Odnawianie certyfikatu konta Uruchom jako](media/manage-runas-account/automation-account-renew-runas-certificate.png)

1. W trakcie odnawiania certyfikatu postęp można śledzić po wybraniu z menu opcji **Powiadomienia**.

## <a name="setting-up-automatic-certificate-renewal-with-an-automation-runbook"></a><a name="auto-cert-renewal"></a>Konfigurowanie automatycznego odnawiania certyfikatów za pomocą systemu runbook automatyzacji

Aby automatycznie odnawiać certyfikaty, można użyć systemu runbook automatyzacji. Ten skrypt w [usłudze GitHub](https://github.com/ikanni/PowerShellScripts/blob/master/AzureAutomation/RunAsAccount/GrantPermissionToRunAsAccountAADApplication-ToRenewCertificateItself-CreateSchedule.ps1) umożliwia tę funkcję na koncie automatyzacji.

>[!NOTE]
>Aby wykonać skrypt, musisz być administratorem globalnym lub administratorem firmy w usłudze Azure AD.

Ten skrypt tworzy tygodniowy harmonogram odnawiania certyfikatów konta Uruchom jako. Dodaje **update-AutomationRunAsCredential** runbook do konta automatyzacji. Kod runbooka można wyświetlić w witrynie GitHub w skrypcie [Update-AutomationRunAsCredential.ps1](https://github.com/azureautomation/runbooks/blob/master/Utility/ARM/Update-AutomationRunAsCredential.ps1). Kod programu PowerShell w pliku służy do ręcznego odnawiania certyfikatów, w razie potrzeby.

Aby natychmiast przetestować proces odnawiania, należy wykonać następujące czynności.

1. Edytuj system runbook **Update-AutomationRunAsCredential** i umieść znak komentarza (#) w wierszu 122 przed **poleceniem Exit(1).**

   ```powershell
   #Exit(1)
   ```

2. Opublikuj program runbook.
3. Uruchom program runbook.
4. Sprawdź pomyślne odnowienie za pomocą następującego kodu:

   ```powershell
   (Get-AzAutomationCertificate -AutomationAccountName TestAA
                                -Name AzureRunAsCertificate
                                -ResourceGroupName TestAutomation).ExpiryTime.DateTime
   ```
    Dane wyjściowe:

   ```Output
   Thursday, November 7, 2019 7:00:00 PM
   ```

5. Po zakończeniu testu edytuj element runbook i usuń znak komentarza dodany w kroku 1.
6. Opublikuj program runbook.

## <a name="limiting-run-as-account-permissions"></a><a name="limiting-run-as-account-permissions"></a>Ograniczanie uprawnień do uruchamiania jako konta

Aby kontrolować kierowanie automatyzacji względem zasobów na platformie Azure, można uruchomić [update-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug8) skryptu. Ten skrypt zmienia istniejącą jednostkę usługi Uruchom jako konto, aby utworzyć i używać niestandardowej definicji roli. Rola ma uprawnienia dla wszystkich zasobów z wyjątkiem [usługi Key Vault](https://docs.microsoft.com/azure/key-vault/).

>[!IMPORTANT]
>Po uruchomieniu skryptu **Update-AutomationRunAsAccountRoleAssignments.ps1** elementy runbook, które uzyskują dostęp do magazynu kluczy za pomocą kont Uruchom jako, nie działają. Przed uruchomieniem skryptu należy przejrzeć wzorce funkcjonowania na koncie pod kątem wywołań usługi Azure Key Vault. Aby włączyć dostęp do usługi Key Vault z umion ekscesów usługi Azure Automation, należy [dodać konto Uruchom jako do uprawnień usługi Key Vault.](#add-permissions-to-key-vault)

Jeśli trzeba ograniczyć, co można zrobić uruchom jako jednostki usługi, `NotActions` można dodać inne typy zasobów do elementu niestandardowej definicji roli. Poniższy przykład ogranicza `Microsoft.Compute/*`dostęp do . Jeśli ten typ zasobu zostanie dodasz dla `NotActions` definicji roli, rola nie będzie mogła uzyskać dostępu do dowolnego zasobu obliczeniowego. Aby dowiedzieć się więcej o definicjach ról, zobacz [Opis definicji ról dla zasobów platformy Azure.](../role-based-access-control/role-definitions.md)

```powershell
$roleDefinition = Get-AzRoleDefinition -Name 'Automation RunAs Contributor'
$roleDefinition.NotActions.Add("Microsoft.Compute/*")
$roleDefinition | Set-AzRoleDefinition
```

Można określić, czy jednostki usługi używane przez konto Uruchom jako jest w definicji roli współautora lub niestandardowe. 

1. Przejdź do swojego konta automatyzacji i wybierz **pozycję Uruchom jako konta** w sekcji Ustawienia konta.
2. Wybierz **pozycję Azure Uruchom jako konto**. 
3. Wybierz **opcję Rola,** aby zlokalizować używaną definicję roli.

[![](media/manage-runas-account/verify-role.png "Verify the Run As Account role")](media/manage-runas-account/verify-role-expanded.png#lightbox)

Można również określić definicję roli używaną przez konta Uruchom jako dla wielu subskrypcji lub kont automatyzacji. W tym celu należy użyć skryptu [Check-AutomationRunAsAccountRoleAssignments.ps1](https://aka.ms/AA5hug5) w galerii programu PowerShell.

### <a name="add-permissions-to-key-vault"></a>Dodawanie uprawnień do usługi Key Vault

Można zezwolić usługi Azure Automation, aby sprawdzić, czy usługa Key Vault i jednostki usługi Uruchom jako konto używają niestandardowej definicji roli. Musisz:

* Udziel uprawnień do magazynu kluczy.
* Ustaw zasady dostępu.

Skrypt [Extend-AutomationRunAsAccountRoleAssignmentToKeyVault.ps1](https://aka.ms/AA5hugb) można użyć w Galerii programu PowerShell, aby nadać przechowalnię konta Uruchom jako. Aby uzyskać więcej informacji na temat ustawiania uprawnień w magazynie kluczy, zobacz [Udzielanie aplikacjom dostępu do magazynu kluczy.](../key-vault/general/group-permissions-for-apps.md)

## <a name="resolving-misconfiguration-issues-for-run-as-accounts"></a>Rozwiązywanie problemów z nieprawidłową konfiguracją dla kont Uruchom jako

Niektóre elementy konfiguracji niezbędne dla konta Uruchom jako lub Klasyczne uruchom jako mogły zostać usunięte lub utworzone nieprawidłowo podczas wstępnej konfiguracji. Możliwe wystąpienia błędnej konfiguracji obejmują:

* Zasób certyfikatu
* Zasób połączenia
* Uruchom jako konto usunięte z roli współautora
* Nazwa główna usługi lub aplikacji w usłudze Azure AD

W przypadku takich wystąpień nieprawidłowej konfiguracji konto automatyzacji wykrywa `Incomplete` zmiany i wyświetla stan w okienku właściwości Uruchom jako konta dla konta.

![Stan Niekompletne dla konfiguracji konta Uruchom jako](media/manage-runas-account/automation-account-runas-incomplete-config.png)

Po wybraniu konta Uruchom jako w okienku właściwości konta zostanie wyświetlony następujący komunikat o błędzie:

```text
The Run As account is incomplete. Either one of these was deleted or not created - Azure Active Directory Application, Service Principal, Role, Automation Certificate asset, Automation Connect asset - or the Thumbprint is not identical between Certificate and Connection. Please delete and then re-create the Run As Account.
```

Te problemy związane z kontem Uruchom jako można szybko rozwiązać, usuwając konto i tworząc je ponownie.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat podmiotów zabezpieczeń usługi, zobacz [Obiekty aplikacji i obiekty głównej usługi](../active-directory/develop/app-objects-and-service-principals.md).
* Aby uzyskać więcej informacji na temat certyfikatów i usług platformy Azure, zobacz [Omówienie certyfikatów dla usług w chmurze Azure](../cloud-services/cloud-services-certs-create.md).
