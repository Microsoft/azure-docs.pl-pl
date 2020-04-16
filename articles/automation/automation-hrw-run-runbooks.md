---
title: Elementy runbook w usłudze Azure Automation Hybrydowy proces roboczy systemu runbook
description: Ten artykuł zawiera informacje dotyczące uruchamiania ksiąg runbook na komputerach w lokalnym centrum danych lub dostawcy chmury z rolą Hybrydowy proces roboczy podręcznika runbook.
services: automation
ms.subservice: process-automation
ms.date: 01/29/2019
ms.topic: conceptual
ms.openlocfilehash: b65c72e0c65cf9aa84cb614478fbdf78258f3054
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405823"
---
# <a name="running-runbooks-on-a-hybrid-runbook-worker"></a>Uruchamianie obrażeń runbook w hybrydowym uzywce procesowej

Umownie przeznaczone dla hybrydowego pracownika uruchomieniu zespół runbook zazwyczaj zarządzają zasobami na komputerze lokalnym lub zasobami w środowisku lokalnym, w którym jest wdrażany proces roboczy. Elementy runbook w usłudze Azure Automation zazwyczaj zarządzają zasobami w chmurze platformy Azure. Mimo że są one używane inaczej, elementy runbook, które są uruchamiane w usłudze Azure Automation i elementy runbook, które są uruchamiane w hybrydowym przebiegu roboczego systemu runbook są identyczne w strukturze.

Podczas tworzenia śmiętu do uruchomienia w hybrydowym uzywolnienia uruchomieniu, należy edytować i testować runbook na komputerze, na którym znajduje się proces roboczy. Komputer-host ma wszystkie moduły programu PowerShell i dostęp do sieci wymagany do zarządzania zasobami lokalnymi i uzyskiwania do nich dostępu. Po przetestowaniu systemu runbook na komputerze roboczym hybrydowego systemu runbook można przekazać go do środowiska automatyzacji platformy Azure, gdzie można go uruchomić w środowisku roboczym. 

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Uprawnienia runbook dla hybrydowego procesu roboczego uruchomieniu

Ponieważ uzyskują dostęp do zasobów innych niż platforma Azure, zestawy runbook uruchomione w hybrydowym udziale w usłudze Runbook nie mogą używać mechanizmu uwierzytelniania zwykle używanego przez księgi runbook uwierzytelniające się w zasobach platformy Azure. System runbook udostępnia własne uwierzytelnianie zasobów lokalnych lub konfiguruje uwierzytelnianie przy użyciu [zarządzanych tożsamości zasobów platformy Azure.](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager) Można również określić uruchom jako konto, aby zapewnić kontekst użytkownika dla wszystkich elementów runbook.

### <a name="runbook-authentication"></a>Uwierzytelnianie uruchomieniu

Domyślnie programy runbook są uruchamiane na komputerze lokalnym. W przypadku systemu Windows są one uruchamiane w kontekście lokalnego konta **systemowego.** W przypadku linuksa działają one w kontekście specjalnego konta użytkownika **nxautomation**. W obu scenariuszach runbooks musi zapewnić własne uwierzytelnianie zasobów, do których uzyskują dostęp.

Zasobów [poświadczeń](automation-credentials.md) i [certyfikatów](automation-certificates.md) w bieście runbooka z poleceniami cmdlet, które umożliwiają określenie poświadczeń, aby identyfikator runbook mógł uwierzytelniać się w różnych zasobach. W poniższym przykładzie przedstawiono część elementów runbook, która uruchamia ponownie komputer. Pobiera poświadczenia z zasobu poświadczeń i nazwę komputera z zasobu zmiennej, a następnie używa tych wartości z poleceniem `Restart-Computer` cmdlet.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Można również użyć działania [InlineScript.](automation-powershell-workflow.md#inlinescript) `InlineScript`umożliwia uruchamianie bloków kodu na innym komputerze z poświadczeniami określonymi przez [wspólny parametr PSCredential](/powershell/module/psworkflow/about/about_workflowcommonparameters).

### <a name="run-as-account"></a>Konto Uruchom jako

Zamiast mieć swój system runbook zapewnić własne uwierzytelnianie do zasobów lokalnych, można określić Uruchom jako konto dla grupy hybrydowego procesu roboczego egoisku. Aby to zrobić, należy zdefiniować [zasób poświadczeń,](automation-credentials.md) który ma dostęp do zasobów lokalnych. Zasoby te obejmują magazyny certyfikatów i wszystkie zestawy runbook uruchamiane w ramach tych poświadczeń w hybrydowym udziale roboczym niuszka w grupie.

Nazwa użytkownika poświadczeń musi być w jednym z następujących formatów:

* Domena\nazwa_użytkownika
* username@domain
* nazwa użytkownika (dla kont lokalnych na komputerze lokalnym)

Poniższa procedura służy do określania konta Uruchom jako dla grupy hybrydowego procesu roboczego elementów runbook.

1. Utwórz [zasób poświadczeń](automation-credentials.md) z dostępem do zasobów lokalnych.
2. Otwórz konto automatyzacji w witrynie Azure portal.
3. Wybierz kafelek **Hybrydowe grupy robocze,** a następnie wybierz grupę.
4. Wybierz **pozycję Wszystkie ustawienia,** a następnie **ustawienia hybrydowej grupy roboczej**.
5. Zmień wartość **Uruchom jako** **domyślne** na **niestandardowe**.
6. Zaznacz poświadczenie i kliknij przycisk **Zapisz**.

### <a name="managed-identities-for-azure-resources"></a><a name="managed-identities-for-azure-resources"></a>Tożsamości zarządzane dla zasobów platformy Azure

Hybrydowe środowiska Owebook Workers na maszynach wirtualnych platformy Azure mogą używać tożsamości zarządzanych dla zasobów platformy Azure do uwierzytelniania zasobów platformy Azure. Korzystanie z tożsamości zarządzanych dla zasobów platformy Azure zamiast kont Uruchom jako zapewnia korzyści, ponieważ nie trzeba:

* Wyeksportuj certyfikat Uruchom jako, a następnie zaimportuj go do hybrydowego procesu roboczego uruchomieniu.
* Odnów certyfikat używany przez konto Uruchom jako.
* Obsłużyć Run As obiekt połączenia w kodzie przewodniczącego.

Wykonaj następne kroki, aby użyć tożsamości zarządzanej dla zasobów platformy Azure w hybrydowym udziale roboczym systemu runbook.

1. Tworzenie maszyny Wirtualnej platformy Azure.
2. Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie Wirtualnej. Zobacz [Konfigurowanie tożsamości zarządzanych dla zasobów platformy Azure na maszynie wirtualnej przy użyciu witryny Azure portal.](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm)
3. Nadaj maszynie wirtualnej dostęp do grupy zasobów w Menedżerze zasobów. Aby [uzyskać dostęp do Menedżera zasobów, należy zapoznać się z punktem Użyj tożsamości zarządzanej przypisanej przez system Windows, aby uzyskać dostęp do Menedżera zasobów](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
4. Zainstaluj proces roboczy hybrydowego egojnika na maszynie wirtualnej. Zobacz [Wdrażanie hybrydowego procesu roboczego żyła systemu Windows](automation-windows-hrw-install.md).
5. Zaktualizuj projekt runbook, aby używać polecenia `Identity` cmdlet [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0) z parametrem do uwierzytelniania zasobów platformy Azure. Ta konfiguracja zmniejsza potrzebę używania konta Uruchom jako i wykonywania skojarzonego zarządzania kontami.

```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
```

> [!NOTE]
> `Connect-AzAccount -Identity`działa dla hybrydowego procesu roboczego życiówek przy użyciu tożsamości przypisanej przez system i tożsamości przypisanej przez jednego użytkownika. Jeśli w hybrydowym uzytce procesu roboczego systemu runbook jest `AccountId` używanych wiele tożsamości przypisanych przez użytkownika, należy określić `Connect-AzAccount` parametr, aby wybrać określoną tożsamość przypisaną użytkownikowi.

### <a name="automation-run-as-account"></a><a name="runas-script"></a>Automatyzacja uruchom jako konto

W ramach procesu automatycznej kompilacji do wdrażania zasobów na platformie Azure, może wymagać dostępu do systemów lokalnych do obsługi zadania lub zestaw kroków w sekwencji wdrażania. Aby zapewnić uwierzytelnianie na platformie Azure przy użyciu konta Uruchom jako, należy zainstalować certyfikat konta Uruchom jako.

Następujący projekt runbook programu PowerShell o nazwie **Export-RunAsCertificateToHybridWorker**eksportuje certyfikat Uruchom jako z konta usługi Azure Automation. Program runbook pobiera i importuje certyfikat do lokalnego magazynu certyfikatów komputera w hybrydowym udziale roboczym uruchomieniu, który jest połączony z tym samym kontem. Po zakończeniu tego kroku, runbook sprawdza, czy proces roboczy może pomyślnie uwierzytelnić się na platformie Azure przy użyciu uruchom jako konto.

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
>Dla uruchomieniu programu PowerShell `Add-AzAccount` i `Add-AzureRMAccount` są `Connect-AzAccount`aliasy dla . Podczas przeszukiwania elementów biblioteki, `Connect-AzAccount`jeśli nie `Add-AzAccount`widzisz , można użyć , lub można zaktualizować moduły na koncie automatyzacji.

Aby zakończyć przygotowywanie konta Uruchom jako:

1. Zapisz **runbook Export-RunAsCertificateToHybridWorker** na komputerze z rozszerzeniem **.ps1.**
2. Zaimportuj go do konta automatyzacji.
3. Edytuj runbook, zmieniając wartość `Password` zmiennej na własne hasło. 
4. Opublikuj program runbook.
5. Uruchom grupę runbook, kierując grupę Hybrydowy proces roboczy żyjącego, który uruchamia i uwierzytelnia programy runbook przy użyciu konta Uruchom jako. 
6. Sprawdź strumień zadań, aby zobaczyć, że raportuje próbę zaimportowania certyfikatu do lokalnego magazynu maszyn i następuje z wieloma wierszami. To zachowanie zależy od liczby kont automatyzacji zdefiniowanych w ramach subskrypcji i stopnia powodzenia uwierzytelniania.

## <a name="job-behavior-on-hybrid-runbook-workers"></a>Zachowanie zadania w hybrydowych robotach runbook

Usługa Azure Automation obsługuje zadania w hybrydowych procesach traktowych Procesów trakcyjnych nieco inaczej niż zadania uruchamiane w piaskownicach platformy Azure. Jedną z kluczowych różnic jest to, że nie ma limitu czasu trwania zadania w pracownikach uruchomieniu. y. Runbooki uruchamiane w piaskownicach platformy Azure są ograniczone do trzech godzin z powodu [sprawiedliwego udziału.](automation-runbook-execution.md#fair-share)

W przypadku długotrwałego śmiętu należy upewnić się, że jest odporny na możliwe ponowne uruchomienie, na przykład, jeśli komputer, na którym znajduje się proces roboczy, zostanie ponownie uruchomiony. Jeśli komputer hosta hybrydowego procesu roboczego roboczego zostanie ponownie uruchomiony, każde uruchomione zadanie roboczej zostanie ponownie uruchomione od początku lub z ostatniego punktu kontrolnego dla śreszt owych przepływów pracy programu PowerShell. Po ponownym uruchomieniu zadania runbook więcej niż trzy razy, jest zawieszony.

Należy pamiętać, że zadania dla hybrydowych procesów workers y żyła są uruchamiane na lokalnym koncie systemowym w systemie Windows lub na koncie **nxautomation** w systemie Linux. W przypadku systemu Linux należy upewnić się, że konto **nxautomation** ma dostęp do lokalizacji, w której są przechowywane moduły runbook. Korzystając z polecenia cmdlet [Install-Module,](/powershell/module/powershellget/install-module) należy określić `Scope` allusers dla parametru, aby upewnić się, że konto **nxautomation** ma dostęp. Aby uzyskać więcej informacji na temat programu PowerShell w systemie Linux, zobacz [Znane problemy dla programu PowerShell na platformach innych niż Windows](https://docs.microsoft.com/powershell/scripting/whats-new/known-issues-ps6?view=powershell-6#known-issues-for-powershell-on-non-windows-platforms).

## <a name="starting-a-runbook-on-a-hybrid-runbook-worker"></a>Uruchamianie egonika w hybrydowym uziemiacza uruchomieniu

[Uruchamianie uruchomieniu w usłudze Azure Automation](automation-starting-a-runbook.md) opisuje różne metody uruchamiania uruchomieniu. ład. Uruchamianie dla systemu runbook w hybrydowym uzywce procesowej używa opcji **Uruchom na,** która umożliwia określenie nazwy grupy hybrydowego procesu roboczego systemu runbook. Po określeniu grupy jeden z pracowników w tej grupie pobiera i uruchamia grupę runbook. Jeśli twój projekt runbook nie określa tej opcji, usługa Azure Automation uruchamia zestaw runbook w zwykły sposób.

Po uruchomieniu uruchomieniu w witrynie Azure portal, zostanie wyświetlony z **uruchom na** opcji, dla której można wybrać **azure** lub **hybrydowy proces roboczy.** W przypadku wybrania opcji **Proces hybrydowy**można wybrać grupę Hybrydowy proces roboczy z listy rozwijanej.

Użyj `RunOn` parametru z poleceniem cmdlet [Start-AzAutomationRunbook.](https://docs.microsoft.com/powershell/module/Az.Automation/Start-AzAutomationRunbook?view=azps-3.7.0) W poniższym przykładzie użyto programu Windows PowerShell do uruchomienia elementów runbook o nazwie **Test-Runbook** w grupie hybrydowego procesu roboczego życigożnicy o nazwie MyHybridGroup.

```azurepowershell-interactive
Start-AzAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "Test-Runbook" -RunOn "MyHybridGroup"
```

> [!NOTE]
> Należy [pobrać najnowszą wersję programu PowerShell,](https://azure.microsoft.com/downloads/) jeśli masz wcześniej zainstalowaną. Zainstaluj tę wersję tylko na stacji roboczej, na której uruchamiasz program Runbook z programu PowerShell. Nie trzeba go instalować na komputerze hybrydowego roboczego systemu runbook, chyba że zamierzasz uruchomić programy runbook z tego komputera.

## <a name="working-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Praca z podpisanymi systemami runbook w usłudze Windows Hybrid Runbook Worker

Hybrydowy proces roboczy żyła systemu Windows można skonfigurować do uruchamiania tylko podpisanych żyłaczek.

> [!IMPORTANT]
> Po skonfigurowaniu hybrydowego procesu roboczego niu gospodyń w celu uruchamiania tylko podpisanych śmięty wiązki śmi.

### <a name="create-signing-certificate"></a>Utwórz certyfikat podpisywania

Poniższy przykład tworzy certyfikat z podpisem własnym, który może służyć do podpisywania elementów runbook. Ten kod tworzy certyfikat i eksportuje go tak, aby hybrydowy proces roboczy uruchomieniu może zaimportować go później. Odcisk palca jest również zwracany do późniejszego użycia w odwoływaniu się do certyfikatu.

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

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Importowanie certyfikatów i konfigurowanie pracowników do sprawdzania poprawności podpisu

Skopiuj utworzony certyfikat do każdego hybrydowego procesu roboczego roboczej w grupie. Uruchom następujący skrypt, aby zaimportować certyfikat i skonfigurować pracowników do używania sprawdzania poprawności podpisu w elementów runbook.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Podpisywanie żyła nych ksiąg eks-owych przy użyciu certyfikatu

W przypadku hybrydowych procesów traktowych skonfigurowanych do używania tylko podpisanych śmigieł, należy podpisać zestawy runbook, które mają być używane w hybrydowym uzywianiu życznicy żytniczej. Użyj następującego przykładowego kodu programu PowerShell, aby podpisać te elementy runbook.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Po podpisaniu elementa runbook należy zaimportować go do konta automatyzacji i opublikować go za pomocą bloku podpisu. Aby dowiedzieć się, jak importować elementy runbook, zobacz [Importowanie uruchomieniu księgi ą z pliku do usługi Azure Automation](manage-runbooks.md#importing-a-runbook).

## <a name="working-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Praca z podpisanymi systemami runbook w uzywniu hybrydowego systemu Linux

Aby móc pracować z podpisanymi systemami runbook, hybrydowy proces roboczy systemu Linux musi mieć plik wykonywalny [GPG](https://gnupg.org/index.html) na komputerze lokalnym.

> [!IMPORTANT]
> Po skonfigurowaniu hybrydowego procesu roboczego niu gospodyń w celu uruchamiania tylko podpisanych śmięty wiązki śmi.

### <a name="create-a-gpg-keyring-and-keypair"></a>Tworzenie breloka gpg i klawiatury

Aby utworzyć brelok i klucze GPG, użyj konta **nxautomation** procesu biegania hybrydowego.

1. Użyj aplikacji sudo, aby zalogować się jako konto **nxautomation.**

    ```bash
    sudo su – nxautomation
    ```

2. Po użyciu **nxautomation**, wygenerować gpg keypair. GPG poprowadzi Cię przez kroki. Musisz podać nazwę, adres e-mail, czas wygaśnięcia i hasło. Następnie poczekaj, aż na komputerze pojawi się wystarczająca entropia, aby klucz został wygenerowany.

    ```bash
    sudo gpg --generate-key
    ```

3. Ponieważ katalog GPG został wygenerowany za pomocą sudo, należy zmienić jego właściciela na **nxautomation** za pomocą następującego polecenia.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Udostępnianie breloka do hybrydowego środowiska roboczego uruchomieniu

Po utworzeniu breloka udostępnij go procesowi roboczemu hybrydowego uruchomieniu. Ład. Zmodyfikuj plik ustawień **/var/opt/microsoft/omsagent/state/automationworker/diy/worker.conf,** aby `[worker-optional]`uwzględnić następujący przykładowy kod w sekcji pliku .

```bash
gpg_public_keyring_path = /var/opt/microsoft/omsagent/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Sprawdź, czy sprawdzanie poprawności podpisu jest włączone

Jeśli sprawdzanie poprawności podpisu zostało wyłączone na komputerze, należy go włączyć, uruchamiając następujące polecenie sudo. Zamień `<LogAnalyticsworkspaceId>` na identyfikator obszaru roboczego.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Podpisywanie eks-u

Po skonfigurowaniu sprawdzania poprawności podpisu użyj następującego polecenia GPG, aby podpisać projekt runbook.

```bash
gpg –-clear-sign <runbook name>
```

Podpisany projekt runbook nosi nazwę ** <runbook name>.asc**.

Teraz można przekazać podpisany projekt runbook do usługi Azure Automation i wykonać go jak zwykły projekt runbook.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat metod uruchamiania uruchomieniu księgi, zobacz [Uruchamianie uruchomieniu księgi uruchomieniu w usłudze Azure Automation](automation-starting-a-runbook.md).
* Aby dowiedzieć się, jak używać edytora tekstu do pracy z systemami runbook programu PowerShell w usłudze Azure Automation, zobacz [Edytowanie życiówek w usłudze Azure Automation.](automation-edit-textual-runbook.md)
* Jeśli programy runbook nie kończą się pomyślnie, zapoznaj się z przewodnikiem rozwiązywania problemów z [błędami wykonania wiązki.](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails)
* Aby uzyskać więcej informacji na temat programu PowerShell, w tym odwoływania się do języka i modułów szkoleniowych, zobacz [Dokumenty programu PowerShell](https://docs.microsoft.com/powershell/scripting/overview).
* Aby uzyskać odwołanie do polecenia polecenia cmdlet programu PowerShell, zobacz [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
