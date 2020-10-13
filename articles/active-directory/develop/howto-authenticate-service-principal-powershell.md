---
title: Tworzenie tożsamości aplikacji platformy Azure (PowerShell) | Azure
titleSuffix: Microsoft identity platform
description: Opis tworzenia aplikacji i jednostki usługi Azure Active Directory oraz przyznawania jej dostępu do zasobów za pośrednictwem kontroli dostępu opartej na rolach przy użyciu programu Azure PowerShell. Ten temat zawiera również informacje na temat uwierzytelniania aplikacji przy użyciu certyfikatu.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev , devx-track-azurepowershell
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.date: 06/26/2020
ms.author: ryanwi
ms.reviewer: tomfitz
ms.openlocfilehash: e034149372ba061ec958e0c1e22187d33009080c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91265837"
---
# <a name="how-to-use-azure-powershell-to-create-a-service-principal-with-a-certificate"></a>Instrukcje: używanie Azure PowerShell do tworzenia jednostki usługi przy użyciu certyfikatu

Jeśli masz aplikację lub skrypt, które potrzebują dostępu do zasobów, możesz skonfigurować tożsamość aplikacji i uwierzytelnić aplikację przy użyciu jej własnych poświadczeń. Ta tożsamość jest określana jako jednostka usługi. Takie podejście umożliwia:

* Przypisywanie uprawnień do tożsamości aplikacji — są to uprawnienia inne niż Twoje. Zazwyczaj te uprawnienia są ograniczone tylko do czynności, które aplikacja musi wykonywać.
* Używanie certyfikatu do uwierzytelnienia podczas wykonywania skryptu nienadzorowanego.

> [!IMPORTANT]
> Zamiast tworzyć jednostkę usługi, rozważ użycie zarządzanych tożsamości dla zasobów platformy Azure dla Twojej tożsamości aplikacji. Jeśli kod jest uruchamiany w usłudze, która obsługuje zarządzane tożsamości i uzyskuje dostęp do zasobów, które obsługują uwierzytelnianie Azure Active Directory (Azure AD), tożsamości zarządzane są dla Ciebie lepszym rozwiązaniem. Aby dowiedzieć się więcej na temat tożsamości zarządzanych dla zasobów platformy Azure, w tym usług, które są obecnie obsługiwane, zobacz temat [co to są tożsamości zarządzane dla zasobów platformy Azure?](../managed-identities-azure-resources/overview.md).

W tym artykule przedstawiono sposób tworzenia jednostki usługi uwierzytelnianej przy użyciu certyfikatu. Aby skonfigurować jednostkę przy użyciu hasła, zobacz [Tworzenie jednostki usługi platformy Azure za pomocą programu Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).

Aby wykonać czynności z tego artykułu, musisz mieć [najnowszą wersję](/powershell/azure/install-az-ps) programu PowerShell.

[!INCLUDE [az-powershell-update](../../../includes/updated-for-az.md)]

## <a name="required-permissions"></a>Wymagane uprawnienia

Aby ukończyć ten artykuł, musisz mieć wystarczające uprawnienia zarówno w usłudze Azure AD, jak i w ramach subskrypcji platformy Azure. W tym celu należy mieć możliwość tworzenia aplikacji w usłudze Azure AD i przypisywania jednostki usługi do roli.

Najłatwiejszym sposobem sprawdzenia, czy Twoje konto ma odpowiednie uprawnienia, jest skorzystanie z portalu. Zobacz [Sprawdzanie wymaganego uprawnienia](howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

## <a name="assign-the-application-to-a-role"></a>Przypisywanie aplikacji do roli
Aby uzyskać dostęp do zasobów w ramach subskrypcji, musisz przypisać aplikację do roli. Zdecyduj, która rola oferuje odpowiednie uprawnienia dla aplikacji. Aby dowiedzieć się więcej na temat dostępnych ról, zobacz [role wbudowane platformy Azure](../../role-based-access-control/built-in-roles.md).

Zakres można ustawić na poziomie subskrypcji, grupy zasobów lub zasobu. Uprawnienia są dziedziczone do niższych poziomów zakresu. Na przykład dodanie aplikacji do roli *czytelnik* dla grupy zasobów oznacza, że może ona odczytać grupę zasobów i wszystkie zawarte w niej zasoby. Aby umożliwić aplikacji wykonywanie akcji takich jak ponowny rozruch, uruchamianie i zatrzymywanie wystąpień, wybierz rolę *współautor* .

## <a name="create-service-principal-with-self-signed-certificate"></a>Tworzenie jednostki usługi z certyfikatem z podpisem własnym

Poniższy przykład przedstawia prosty scenariusz. Używa polecenie [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal) , aby utworzyć jednostkę usługi z certyfikatem z podpisem własnym, i przypisać rolę [czytnika](../../role-based-access-control/built-in-roles.md#reader) do jednostki usługi przy użyciu funkcji [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) . Zakres przypisania roli to aktualnie wybrana subskrypcja platformy Azure. Aby wybrać inną subskrypcję, użyj polecenie [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext).

> [!NOTE]
> Polecenie cmdlet New-SelfSignedCertificate i moduł PKI nie są obecnie obsługiwane w programie PowerShell Core. 

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" `
  -Subject "CN=exampleappScriptCert" `
  -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

$sp = New-AzADServicePrincipal -DisplayName exampleapp `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
Sleep 20
New-AzRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $sp.ApplicationId
```

Przykład uśpienia przez 20 sekund, aby umożliwić pewien czas propagacji nowej jednostki usługi w usłudze Azure AD. Jeśli skrypt nie będzie oczekiwać wystarczająco długo, pojawi się następujący komunikat o błędzie: „Identyfikator {ID} jednostki usługi nie istnieje w katalogu {DIR-ID}”. Aby rozwiązać ten problem, poczekaj chwilę, a następnie ponownie uruchom polecenie **New-AzRoleAssignment** .

Zakres przypisania roli do określonej grupy zasobów można określić za pomocą parametru **ResourceGroupName**. Zakres konkretnego zasobu można również określić za pomocą parametrów **ResourceType** i **ResourceName**. 

Jeśli nie masz **zainstalowanego systemu Windows 10 lub Windows Server 2016**, musisz pobrać [generator certyfikatu z podpisem własnym](https://gallery.technet.microsoft.com/scriptcenter/Self-signed-certificate-5920a7c6/) z witryny Microsoft Script Center. Wyodrębnij jego zawartość i zaimportuj potrzebne polecenie cmdlet.

```powershell
# Only run if you could not use New-SelfSignedCertificate
Import-Module -Name c:\ExtractedModule\New-SelfSignedCertificateEx.ps1
```

W skrypcie zastąp dwa poniższe wiersze w celu wygenerowania certyfikatu.

```powershell
New-SelfSignedCertificateEx -StoreLocation CurrentUser `
  -Subject "CN=exampleapp" `
  -KeySpec "Exchange" `
  -FriendlyName "exampleapp"
$cert = Get-ChildItem -path Cert:\CurrentUser\my | where {$PSitem.Subject -eq 'CN=exampleapp' }
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Udostępnianie certyfikatu za pośrednictwem zautomatyzowanego skryptu programu PowerShell

Za każdym razem, gdy logujesz się jako jednostka usługi, musisz podać identyfikator dzierżawy katalogu aplikacji usługi AD. Dzierżawca jest wystąpieniem usługi Azure AD.

```powershell
$TenantId = (Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
$ApplicationId = (Get-AzADApplication -DisplayNameStartWith exampleapp).ApplicationId

 $Thumbprint = (Get-ChildItem cert:\CurrentUser\My\ | Where-Object {$_.Subject -eq "CN=exampleappScriptCert" }).Thumbprint
 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

## <a name="create-service-principal-with-certificate-from-certificate-authority"></a>Tworzenie jednostki usługi z certyfikatem od urzędu certyfikacji

W poniższym przykładzie użyto certyfikatu wystawionego przez urząd certyfikacji w celu utworzenia jednostki usługi. Zakres przypisania obejmuje określoną subskrypcję platformy Azure. Dodaje nazwę główną usługi do roli [czytelnik](../../role-based-access-control/built-in-roles.md#reader) . Jeśli podczas przypisywania roli wystąpi błąd, zostanie ponownie podjęta próba przypisania.

```powershell
Param (
 [Parameter(Mandatory=$true)]
 [String] $ApplicationDisplayName,

 [Parameter(Mandatory=$true)]
 [String] $SubscriptionId,

 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword
 )

 Connect-AzAccount
 Import-Module Az.Resources
 Set-AzContext -Subscription $SubscriptionId
 
 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force

 $PFXCert = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 -ArgumentList @($CertPath, $CertPassword)
 $KeyValue = [System.Convert]::ToBase64String($PFXCert.GetRawCertData())

 $ServicePrincipal = New-AzADServicePrincipal -DisplayName $ApplicationDisplayName
 New-AzADSpCredential -ObjectId $ServicePrincipal.Id -CertValue $KeyValue -StartDate $PFXCert.NotBefore -EndDate $PFXCert.NotAfter
 Get-AzADServicePrincipal -ObjectId $ServicePrincipal.Id 

 $NewRole = $null
 $Retries = 0;
 While ($NewRole -eq $null -and $Retries -le 6)
 {
    # Sleep here for a few seconds to allow the service principal application to become active (should only take a couple of seconds normally)
    Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Reader -ServicePrincipalName $ServicePrincipal.ApplicationId | Write-Verbose -ErrorAction SilentlyContinue
    $NewRole = Get-AzRoleAssignment -ObjectId $ServicePrincipal.Id -ErrorAction SilentlyContinue
    $Retries++;
 }
 
 $NewRole
```

### <a name="provide-certificate-through-automated-powershell-script"></a>Udostępnianie certyfikatu za pośrednictwem zautomatyzowanego skryptu programu PowerShell
Za każdym razem, gdy logujesz się jako jednostka usługi, musisz podać identyfikator dzierżawy katalogu aplikacji usługi AD. Dzierżawca jest wystąpieniem usługi Azure AD.

```powershell
Param (
 
 [Parameter(Mandatory=$true)]
 [String] $CertPath,

 [Parameter(Mandatory=$true)]
 [String] $CertPlainPassword,
 
 [Parameter(Mandatory=$true)]
 [String] $ApplicationId,

 [Parameter(Mandatory=$true)]
 [String] $TenantId
 )

 $CertPassword = ConvertTo-SecureString $CertPlainPassword -AsPlainText -Force
 $PFXCert = New-Object `
  -TypeName System.Security.Cryptography.X509Certificates.X509Certificate2 `
  -ArgumentList @($CertPath, $CertPassword)
 $Thumbprint = $PFXCert.Thumbprint

 Connect-AzAccount -ServicePrincipal `
  -CertificateThumbprint $Thumbprint `
  -ApplicationId $ApplicationId `
  -TenantId $TenantId
```

W identyfikatorach aplikacji i dzierżawy nie jest uwzględniana wielkość liter, więc można je osadzać bezpośrednio w skrypcie. Jeśli musisz pobrać identyfikator dzierżawy, użyj polecenia:

```powershell
(Get-AzSubscription -SubscriptionName "Contoso Default").TenantId
```

Jeśli musisz pobrać identyfikator aplikacji, użyj polecenia:

```powershell
(Get-AzADApplication -DisplayNameStartWith {display-name}).ApplicationId
```

## <a name="change-credentials"></a>Zmienianie poświadczeń

Aby zmienić poświadczenia dla aplikacji usługi AD z powodu naruszenia zabezpieczeń lub wygaśnięcia poświadczeń, Użyj poleceń cmdlet [Remove-AzADAppCredential](/powershell/module/az.resources/remove-azadappcredential) i [New-AzADAppCredential](/powershell/module/az.resources/new-azadappcredential) .

Aby usunąć wszystkie poświadczenia aplikacji, należy użyć polecenia:

```powershell
Get-AzADApplication -DisplayName exampleapp | Remove-AzADAppCredential
```

Aby dodać wartość certyfikatu, należy utworzyć certyfikat z podpisem własnym, jak pokazano w tym artykule. Następnie należy użyć polecenia:

```powershell
Get-AzADApplication -DisplayName exampleapp | New-AzADAppCredential `
  -CertValue $keyValue `
  -EndDate $cert.NotAfter `
  -StartDate $cert.NotBefore
```

## <a name="debug"></a>Debugowanie

Podczas tworzenia jednostki usługi mogą wystąpić następujące błędy:

* **„Authentication_Unauthorized”** lub **„W kontekście nie znaleziono subskrypcji”**. — Ten błąd jest wyświetlany, gdy konto nie ma [wymaganych uprawnień](#required-permissions) do zarejestrowania aplikacji w usłudze Azure AD. Zazwyczaj ten błąd jest wyświetlany, gdy tylko użytkownicy administracyjni w Azure Active Directory mogą rejestrować aplikacje, a Twoje konto nie jest kontem administratora. Poproszenie administratora o przypisanie do roli administratora lub umożliwienie użytkownikom rejestrowania aplikacji.

* Twoje konto **"nie ma autoryzacji do wykonania akcji" Microsoft. Authorization/roleAssignments/Write "w zakresie"/subscriptions/{GUID} "."** — ten błąd jest wyświetlany, gdy konto nie ma wystarczających uprawnień do przypisania roli do tożsamości. Poproś administratora subskrypcji o dodanie Cię do roli Administrator dostępu użytkowników.

## <a name="next-steps"></a>Następne kroki

* Aby skonfigurować jednostkę przy użyciu hasła, zobacz [Tworzenie jednostki usługi platformy Azure za pomocą programu Azure PowerShell](/powershell/azure/create-azure-service-principal-azureps).
* Aby uzyskać bardziej szczegółowy opis aplikacji i jednostek usługi, zobacz [Application Objects and Service Principal Objects (Obiekty aplikacji i obiekty jednostki usługi)](app-objects-and-service-principals.md).
* Aby uzyskać więcej informacji na temat uwierzytelniania usługi Azure AD, zobacz [scenariusze uwierzytelniania dla usługi Azure AD](./authentication-vs-authorization.md).
* Aby uzyskać informacje na temat pracy z rejestracjami aplikacji przy użyciu **Microsoft Graph**, zobacz Dokumentacja interfejsu API [aplikacji](/graph/api/resources/application) .
