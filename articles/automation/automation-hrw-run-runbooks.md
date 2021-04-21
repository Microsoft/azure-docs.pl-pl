---
title: Uruchamianie Azure Automation Runbook w hybrydowym procesu roboczego runbook
description: W tym artykule opisano sposób uruchamiania runbook na maszynach w lokalnym centrum danych lub u innego dostawcy usług w chmurze za pomocą hybrydowego procesu roboczego runbook.
services: automation
ms.subservice: process-automation
ms.date: 03/10/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6501fd26a5c7966c4cde596df40346b106c57cce
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834790"
---
# <a name="run-runbooks-on-a-hybrid-runbook-worker"></a>Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook

Za pomocą hybrydowych procesów roboczych [runbook](automation-hybrid-runbook-worker.md) zazwyczaj można zarządzać zasobami na komputerze lokalnym lub w środowisku lokalnym, w którym wdrożono proces roboczy. Zasoby runbook w Azure Automation zwykle zarządzają zasobami w chmurze platformy Azure. Mimo że są one używane inaczej, są one identyczne w strukturze Azure Automation i runbook, które działają w hybrydowym procesach roboczych runbook.

Podczas tworzenia i testowania hybrydowego procesu roboczego runbook należy go edytować i testować na maszynie, która hostuje proces roboczy. Maszyna hosta ma wszystkie moduły programu PowerShell i dostęp do sieci wymagany do zarządzania zasobami lokalnymi. Po przetestowaniu runbook na maszynie hybrydowego procesu roboczego runbook można przekazać go do środowiska Azure Automation, gdzie można go uruchomić w procesach roboczych.

## <a name="plan-runbook-job-behavior"></a>Planowanie zachowania zadania runbook

Azure Automation zadania dotyczące hybrydowych pracowników runbook różnią się od zadań uruchamianych w piaskownicach platformy Azure. Jeśli masz długotrwały podręcznik Runbook, upewnij się, że jest on odporny na możliwe ponowne uruchomienie. Aby uzyskać szczegółowe informacje o zachowaniu zadania, zobacz [Hybrid Runbook Worker jobs (Zadania hybrydowego procesu roboczego runbook).](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-jobs)

Zadania dla hybrydowych pracowników runbook są uruchamiane na lokalnym koncie **systemowym** w systemie Windows lub **na koncie nxautomation** w systemie Linux. W przypadku systemu Linux sprawdź, **czy konto nxautomation** ma dostęp do lokalizacji, w której są przechowywane moduły runbook. W przypadku korzystania z polecenia cmdlet [Install-Module](/powershell/module/powershellget/install-module) należy określić parametr AllUsers, aby upewnić się, że konto `Scope` **nxautomation** ma dostęp. Aby uzyskać więcej informacji na temat programu PowerShell w systemie Linux, zobacz Znane problemy dotyczące programu [PowerShell na platformach innych niż Windows.](/powershell/scripting/whats-new/what-s-new-in-powershell-70)

## <a name="configure-runbook-permissions"></a>Konfigurowanie uprawnień do runbook

Zdefiniuj uprawnienia dla swojego runbook do uruchamiania w hybrydowym procesu roboczego runbook w następujący sposób:

* Aby zapewnić własne uwierzytelnianie dla zasobów lokalnych, należy udostępnić go w programie Runbook.
* Skonfiguruj uwierzytelnianie przy [użyciu tożsamości zarządzanych dla zasobów platformy Azure.](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)
* Określ konto Uruchom jako, aby udostępnić kontekst użytkownika dla wszystkich runbook.

### <a name="use-runbook-authentication-to-local-resources"></a>Używanie uwierzytelniania runbook w zasobach lokalnych

W przypadku przygotowywania typu runbook, który zapewnia własne uwierzytelnianie dla zasobów, użyj [zasobów](./shared-resources/credentials.md) poświadczeń [i](./shared-resources/certificates.md) certyfikatów w swoim runbook. Istnieje kilka polecenia cmdlet, które umożliwiają określenie poświadczeń, aby umożliwić uwierzytelnianie w różnych zasobach. W poniższym przykładzie przedstawiono część runbook, która uruchamia ponownie komputer. Pobiera poświadczenia z zasobu poświadczeń i nazwę komputera z zasobu zmiennej, a następnie używa tych wartości z `Restart-Computer` poleceniem cmdlet .

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Można również użyć działania [InlineScript.](automation-powershell-workflow.md#use-inlinescript) `InlineScript` Umożliwia uruchamianie bloków kodu na innym komputerze z poświadczeniami.

### <a name="use-runbook-authentication-with-managed-identities"></a><a name="runbook-auth-managed-identities"></a>Używanie uwierzytelniania runbook z tożsamościami zarządzanymi

Hybrydowe pracownicy runbook na maszynach wirtualnych platformy Azure mogą używać tożsamości zarządzanych do uwierzytelniania w zasobach platformy Azure. Używanie tożsamości zarządzanych dla zasobów platformy Azure zamiast kont Uruchom jako zapewnia korzyści, ponieważ nie trzeba:

* Wyeksportuj certyfikat Uruchom jako, a następnie zaimportuj go do hybrydowego procesu roboczego runbook.
* Odnów certyfikat używany przez konto Uruchom jako.
* Obsługa obiektu połączenia Uruchom jako w kodzie runbook.

Wykonaj następne kroki, aby użyć tożsamości zarządzanej dla zasobów platformy Azure w hybrydowym procesu roboczego runbook:

1. Tworzenie maszyny wirtualnej platformy Azure.
1. Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej. Zobacz [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)przy użyciu Azure Portal .
1. Nadaj maszynie wirtualnej dostęp do grupy zasobów w Resource Manager. Zapoznaj się [z tematem Używanie przypisanej przez system](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)tożsamości zarządzanej maszyny wirtualnej z systemem Windows do uzyskiwania dostępu Resource Manager .
1. Zainstaluj hybrydowy proces roboczy runbook na maszynie wirtualnej. Zobacz [Deploy a Windows Hybrid Runbook Worker (Wdrażanie hybrydowego procesu roboczego runbook systemu Windows)](automation-windows-hrw-install.md) lub Deploy a Linux Hybrid [Runbook Worker (Wdrażanie hybrydowego procesu roboczego runbook systemu Linux).](automation-linux-hrw-install.md)
1. Zaktualizuj ten podręcznik, aby używać polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) z `Identity` parametrem w celu uwierzytelniania w zasobach platformy Azure. Ta konfiguracja zmniejsza potrzebę używania konta Uruchom jako i wykonywania skojarzonego zarządzania kontami.

    ```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
    ```

    > [!NOTE]
    > `Connect-AzAccount -Identity` Działa w przypadku hybrydowego procesu roboczego runbook przy użyciu tożsamości przypisanej przez system i jednej tożsamości przypisanej przez użytkownika. Jeśli używasz wielu tożsamości przypisanych przez użytkownika w hybrydowym procesie roboczy elementów Runbook, twój podręcznik runbook musi określić parametr , aby wybrać konkretną tożsamość `AccountId` `Connect-AzAccount` przypisaną przez użytkownika.

### <a name="use-runbook-authentication-with-run-as-account"></a>Używanie uwierzytelniania runbook przy użyciu konta Uruchom jako

Zamiast zapewniać własne uwierzytelnianie dla zasobów lokalnych, możesz określić konto Uruchom jako dla grupy hybrydowych procesów roboczych runbook. Aby określić konto Uruchom jako, należy zdefiniować zasób [poświadczeń,](./shared-resources/credentials.md) który ma dostęp do zasobów lokalnych. Te zasoby obejmują magazyny certyfikatów i wszystkie podręczniki Runbook są uruchamiane w ramach tych poświadczeń w hybrydowym procesie roboczym runbook w grupie.

- Nazwa użytkownika poświadczenia musi mieć jeden z następujących formatów:

   * Domena\nazwa_użytkownika
   * username@domain
   * nazwa użytkownika (w przypadku kont lokalnych na komputerze lokalnym)

- Aby użyć runbook programu PowerShell **Export-RunAsCertificateToHybridWorker,** należy zainstalować moduły Az dla Azure Automation na komputerze lokalnym.

#### <a name="use-a-credential-asset-to-specify-a-run-as-account"></a>Używanie zasobu poświadczeń do określania konta Uruchom jako

Użyj poniższej procedury, aby określić konto Uruchom jako dla grupy hybrydowych procesów roboczych runbook:

1. Utwórz [zasób poświadczeń](./shared-resources/credentials.md) z dostępem do zasobów lokalnych.
1. Otwórz konto usługi Automation w Azure Portal.
1. Wybierz **pozycję Grupy hybrydowych procesów** roboczych, a następnie wybierz określoną grupę.
1. Wybierz **pozycję Wszystkie ustawienia,** a następnie **pozycję Ustawienia grupy hybrydowych procesów roboczych.**
1. Zmień wartość opcji **Uruchom jako z** Domyślne **na** **Niestandardowa.**
1. Wybierz poświadczenie i kliknij **przycisk Zapisz.**

## <a name="install-run-as-account-certificate"></a><a name="runas-script"></a>Instalowanie certyfikatu konta Uruchom jako

W ramach zautomatyzowanego procesu kompilacji w celu wdrażania zasobów na platformie Azure może być wymagany dostęp do systemów lokalnych w celu obsługi zadania lub zestawu kroków w sekwencji wdrażania. Aby zapewnić uwierzytelnianie na platformie Azure przy użyciu konta Uruchom jako, musisz zainstalować certyfikat konta Uruchom jako.

>[!NOTE]
>Ten runbook programu PowerShell nie jest obecnie uruchamiany na maszynach z systemem Linux. Działa tylko na maszynach z systemem Windows.
>

Następujący runbook programu PowerShell o nazwie **Export-RunAsCertificateToHybridWorker** eksportuje certyfikat Uruchom jako z Azure Automation konta. Ten program Runbook pobiera i importuje certyfikat do magazynu certyfikatów komputera lokalnego w hybrydowym procesie roboczy runbook połączonym z tym samym kontem. Po ukończeniu tego kroku runbook sprawdza, czy proces roboczy może pomyślnie uwierzytelnić się na platformie Azure przy użyciu konta Uruchom jako.

>[!NOTE]
>Ten element Runbook programu PowerShell nie jest przeznaczony do uruchamiania poza kontem usługi Automation jako skrypt na maszynie docelowej.
>

```azurepowershell-interactive
<#PSScriptInfo
.VERSION 1.0
.GUID 3a796b9a-623d-499d-86c8-c249f10a6986
.AUTHOR Azure Automation Team
.COMPANYNAME Microsoft
.COPYRIGHT
.TAGS Azure Automation
.LICENSEURI
.PROJECTURI
.ICONURI
.EXTERNALMODULEDEPENDENCIES
.REQUIREDSCRIPTS
.EXTERNALSCRIPTDEPENDENCIES
.RELEASENOTES
#>

<#
.SYNOPSIS
Exports the Run As certificate from an Azure Automation account to a hybrid worker in that account.

.DESCRIPTION
This runbook exports the Run As certificate from an Azure Automation account to a hybrid worker in that account. Run this runbook on the hybrid worker where you want the certificate installed. This allows the use of the AzureRunAsConnection to authenticate to Azure and manage Azure resources from runbooks running on the hybrid worker.

.EXAMPLE
.\Export-RunAsCertificateToHybridWorker

.NOTES
LASTEDIT: 2016.10.13
#>

# Generate the password used for this certificate
Add-Type -AssemblyName System.Web -ErrorAction SilentlyContinue | Out-Null
$Password = [System.Web.Security.Membership]::GeneratePassword(25, 10)

# Stop on errors
$ErrorActionPreference = 'stop'

# Get the management certificate that will be used to make calls into Azure Service Management resources
$RunAsCert = Get-AutomationCertificate -Name "AzureRunAsCertificate"

# location to store temporary certificate in the Automation service host
$CertPath = Join-Path $env:temp  "AzureRunAsCertificate.pfx"

# Save the certificate
$Cert = $RunAsCert.Export("pfx",$Password)
Set-Content -Value $Cert -Path $CertPath -Force -Encoding Byte | Write-Verbose

Write-Output ("Importing certificate into $env:computername local machine root store from " + $CertPath)
$SecurePassword = ConvertTo-SecureString $Password -AsPlainText -Force
Import-PfxCertificate -FilePath $CertPath -CertStoreLocation Cert:\LocalMachine\My -Password $SecurePassword -Exportable | Write-Verbose

# Test to see if authentication to Azure Resource Manager is working
$RunAsConnection = Get-AutomationConnection -Name "AzureRunAsConnection"

Connect-AzAccount `
    -ServicePrincipal `
    -Tenant $RunAsConnection.TenantId `
    -ApplicationId $RunAsConnection.ApplicationId `
    -CertificateThumbprint $RunAsConnection.CertificateThumbprint | Write-Verbose

Set-AzContext -Subscription $RunAsConnection.SubscriptionID | Write-Verbose

# List automation accounts to confirm that Azure Resource Manager calls are working
Get-AzAutomationAccount | Select-Object AutomationAccountName
```

>[!NOTE]
>W przypadku podręczników Runbook programu PowerShell `Add-AzAccount` `Add-AzureRMAccount` są to aliasy dla `Connect-AzAccount` . Jeśli podczas wyszukiwania elementów biblioteki nie widzisz pozycji , możesz użyć funkcji lub zaktualizować `Connect-AzAccount` moduły na koncie usługi `Add-AzAccount` Automation.

Aby zakończyć przygotowywanie konta Uruchom jako:

1. Zapisz na **komputerze runbook Export-RunAsCertificateToHybridWorker** z rozszerzeniem **ps1.**
1. Zaimportuj go na konto usługi Automation.
1. Edytuj pozycję runbook, zmieniając wartość zmiennej `Password` na własne hasło.
1. Opublikuj runbook.
1. Uruchom ten runbook, przeznaczony dla grupy hybrydowych procesów roboczych runbook, która uruchamia i uwierzytelnia je przy użyciu konta Uruchom jako. 
1. Sprawdź strumień zadań, aby zobaczyć, że raportuje on próbę zaimportowania certyfikatu do magazynu komputera lokalnego, a po nim wiele wierszy. To zachowanie zależy od liczby kont usługi Automation, które zdefiniowano w subskrypcji, oraz od stopnia powodzenia uwierzytelniania.

## <a name="work-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Praca z podpisanymi podręcznikami runbook w hybrydowym procesie roboczy runbook systemu Windows

Hybrydowy proces roboczy runbook systemu Windows można skonfigurować tak, aby uruchamiał tylko podpisane książki runbook.

> [!IMPORTANT]
> Po skonfigurowaniu hybrydowego procesu roboczego runbook do uruchamiania tylko podpisanych runbook niepodpisane nie można wykonać niepodpisanych procesów roboczych.

### <a name="create-signing-certificate"></a>Tworzenie certyfikatu podpisywania

Poniższy przykład tworzy certyfikat z podpisem własnym, który może służyć do podpisywania elementy Runbook. Ten kod tworzy certyfikat i eksportuje go w celu późniejszego zaimportowania go przez hybrydowy proces roboczy runbook. Odcisk palca jest również zwracany do późniejszego użycia podczas odwoływania się do certyfikatu.

```powershell
# Create a self-signed certificate that can be used for code signing
$SigningCert = New-SelfSignedCertificate -CertStoreLocation cert:\LocalMachine\my `
    -Subject "CN=contoso.com" `
    -KeyAlgorithm RSA `
    -KeyLength 2048 `
    -Provider "Microsoft Enhanced RSA and AES Cryptographic Provider" `
    -KeyExportPolicy Exportable `
    -KeyUsage DigitalSignature `
    -Type CodeSigningCert

# Export the certificate so that it can be imported to the hybrid workers
Export-Certificate -Cert $SigningCert -FilePath .\hybridworkersigningcertificate.cer

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Retrieve the thumbprint for later use
$SigningCert.Thumbprint
```

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Importowanie certyfikatu i konfigurowanie świadczeń workers na celu weryfikacji podpisu

Skopiuj utworzony certyfikat do każdego hybrydowego procesu roboczego runbook w grupie. Uruchom następujący skrypt, aby zaimportować certyfikat i skonfigurować pracowników do używania walidacji podpisu w elementy Runbook.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Podpisywanie swoich runbook przy użyciu certyfikatu

W przypadku hybrydowych procesów roboczych runbook skonfigurowanych do używania tylko podpisanych runbook należy podpisać te, które mają być używane w hybrydowym procesie roboczy runbook. Użyj następującego przykładowego kodu programu PowerShell, aby podpisać te elementy Runbook.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Po podpisaniu runbook należy zaimportować go na konto usługi Automation i opublikować z blokiem podpisu. Aby dowiedzieć się, jak importować podręczniki Runbook, zobacz [Importowanie runbook.](manage-runbooks.md#import-a-runbook)

## <a name="work-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Praca z podpisanymi podręcznikami runbook w hybrydowym procesie roboczy runbook systemu Linux

Aby można było pracować ze podpisanymi podręcznikami runbook, hybrydowy proces roboczy runbook systemu Linux musi mieć plik wykonywalny [GPG](https://gnupg.org/index.html) na komputerze lokalnym.

> [!IMPORTANT]
> Po skonfigurowaniu hybrydowego procesu roboczego runbook do uruchamiania tylko podpisanych runbook niepodpisane nie można wykonać niepodpisanych procesów roboczych.

Aby ukończyć tę konfigurację, wykonaj następujące kroki:

* Tworzenie gpg keyring i keypair
* Udostępnij ten klucz hybrydowemu procesowi roboczemu runbook
* Sprawdź, czy weryfikacja podpisu jest wł.
* Podpisywanie runbook

### <a name="create-a-gpg-keyring-and-keypair"></a>Tworzenie gpg keyring i keypair

Aby utworzyć gpg keyring i keypair, użyj konta [nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux)hybrydowego procesu roboczego runbook.

1. Użyj aplikacji sudo, aby zalogować się jako **konto nxautomation.**

    ```bash
    sudo su - nxautomation
    ```

1. Gdy używasz **nxautomation**, wygeneruj gpg keypair. Przewodnik POG przeprowadzi Cię przez kroki. Należy podać nazwę, adres e-mail, czas wygaśnięcia i hasło. Następnie zaczekasz, aż na maszynie będzie wystarczająca entropia, aby klucz został wygenerowany.

    ```bash
    sudo gpg --generate-key
    ```

1. Ponieważ katalog GPG został wygenerowany za pomocą polecenia sudo, należy zmienić jego właściciela na **nxautomation przy** użyciu następującego polecenia.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Udostępnij ten klucz hybrydowemu procesowi roboczemu runbook

Po utworzeniu keyringu udostępnij go hybrydowemu procesowi roboczemu runbook. Zmodyfikuj **plik ustawień home/nxautomation/state/worker.conf,** aby uwzględnić następujący przykładowy kod w sekcji pliku `[worker-optional]` .

```bash
gpg_public_keyring_path = /home/nxautomation/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Sprawdź, czy weryfikacja podpisu jest wł.

Jeśli weryfikacja podpisu została wyłączona na maszynie, należy ją włączyć, uruchamiając następujące polecenie sudo. Zastąp `<LogAnalyticsworkspaceId>` identyfikatorem obszaru roboczego.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Podpisywanie runbook

Po skonfigurowaniu weryfikacji podpisu użyj następującego polecenia GPG, aby podpisać ten runbook.

```bash
gpg --clear-sign <runbook name>
```

Podpisany podręcznik Runbook nosi **<runbook name> nazwę asc**.

Teraz możesz przekazać podpisany plik Runbook do Azure Automation i wykonać go jak zwykły.

## <a name="start-a-runbook-on-a-hybrid-runbook-worker"></a>Uruchamianie runbook w hybrydowym procesu roboczego runbook

[Uruchamianie runbook w programie Azure Automation](start-runbooks.md) opisuje różne metody uruchamiania runbook. Uruchamianie runbook w hybrydowym procesu  roboczego runbook używa opcji Uruchom przy, która umożliwia określenie nazwy grupy hybrydowych procesów roboczych runbook. Po podano grupę, jeden z pracowników w tej grupie pobiera i uruchamia runbook. Jeśli ta opcja nie jest określana przez twój Azure Automation runbook, uruchom go w zwykły sposób.

Po uruchomieniu runbook w Azure Portal zostanie przedstawiona opcja  Uruchom przy, dla której można wybrać pozycję **Azure** lub **Hybrydowy proces roboczy.** W przypadku wybrania **opcji Hybrydowy proces roboczy** możesz wybrać grupę hybrydowych procesów roboczych runbook z listy rozwijanej.

Podczas uruchamiania runbook przy użyciu programu PowerShell użyj parametru z poleceniem `RunOn` cmdlet [Start-AzAutomationRunbook.](/powershell/module/Az.Automation/Start-AzAutomationRunbook) W poniższym przykładzie użyto Windows PowerShell runbook o nazwie **Test-Runbook** w grupie hybrydowego procesu roboczego runbook o nazwie MyHybridGroup.

```azurepowershell-interactive
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -RunOn "MyHybridGroup"
```

## <a name="logging"></a>Rejestrowanie

Aby ułatwić rozwiązywanie problemów z uruchomionymi w hybrydowym procesem roboczym runbook elementy Runbook, dzienniki są przechowywane lokalnie w następującej lokalizacji:

* W systemie Windows na `C:\ProgramData\Microsoft\System Center\Orchestrator\<version>\SMA\Sandboxes` stronie , aby uzyskać szczegółowe rejestrowanie procesu środowiska uruchomieniowego zadania. Zdarzenia stanu zadania runbook wysokiego poziomu są zapisywane w dzienniku zdarzeń Dzienniki aplikacji i **usług\Microsoft-Automation\Operations.**

* W systemie Linux dzienniki hybrydowego procesu roboczego użytkownika można znaleźć w chmurze , a dzienniki procesów roboczych systemowych runbook można `/home/nxautomation/run/worker.log` znaleźć pod . `/var/opt/microsoft/omsagent/run/automationworker/worker.log`

## <a name="next-steps"></a>Następne kroki

* Jeśli twoje podręczniki Runbook nie są ukończone pomyślnie, zapoznaj się z przewodnikiem rozwiązywania problemów w przypadku [niepowodzeń wykonywania.](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)
* Aby uzyskać więcej informacji na temat programu PowerShell, w tym informacje o języku i moduły szkoleniowe, zobacz [PowerShell Docs](/powershell/scripting/overview).
* Aby uzyskać informacje na temat poleceń cmdlet programu PowerShell, zobacz [Az.Automation](/powershell/module/az.automation).
