---
title: Uruchamianie Azure Automation elementów Runbook w hybrydowym procesie roboczym elementu Runbook
description: W tym artykule opisano sposób uruchamiania elementów Runbook na maszynach w lokalnym centrum danych lub innym dostawcy chmury przy użyciu hybrydowego procesu roboczego elementu Runbook.
services: automation
ms.subservice: process-automation
ms.date: 03/10/2021
ms.topic: conceptual
ms.openlocfilehash: 796ac876537aa06253ad6eeec99adaf48de61c79
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167264"
---
# <a name="run-runbooks-on-a-hybrid-runbook-worker"></a>Uruchamianie elementów Runbook w hybrydowym procesie roboczym elementu Runbook

Elementy Runbook działające w [hybrydowym procesie roboczym elementu Runbook](automation-hybrid-runbook-worker.md) zwykle zarządzają zasobami na komputerze lokalnym lub z zasobami w środowisku lokalnym, w którym jest wdrażany proces roboczy. Elementy Runbook w Azure Automation zazwyczaj zarządzają zasobami w chmurze platformy Azure. Mimo że są one używane inaczej, elementy Runbook działające w Azure Automation i elementy Runbook, które działają w hybrydowym procesie roboczym elementu Runbook, są identyczne w strukturze.

Podczas tworzenia elementu Runbook do uruchamiania w hybrydowym procesie roboczym elementu Runbook należy edytować i testować element Runbook na komputerze hostującym proces roboczy. Komputer hosta ma wszystkie moduły programu PowerShell i dostęp do sieci wymagany do zarządzania zasobami lokalnymi. Po przetestowaniu elementu Runbook na komputerze hybrydowego procesu roboczego elementu Runbook można przekazać go do środowiska Azure Automation, w którym można go uruchomić w procesie roboczym.

## <a name="plan-runbook-job-behavior"></a>Zachowanie zadania planu elementu Runbook

Azure Automation obsługuje zadania dla hybrydowych procesów roboczych elementu Runbook inaczej niż zadania uruchamiane w piaskownicach platformy Azure. Jeśli masz długotrwały element Runbook, upewnij się, że jest on odporny na możliwe ponowne uruchomienie. Aby uzyskać szczegółowe informacje o zachowaniu zadania, zobacz [hybrydowe zadania procesu roboczego elementu Runbook](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-jobs).

Zadania dla hybrydowych procesów roboczych elementu Runbook działają w ramach lokalnego konta **systemowego** w systemie Windows lub konta **Nxautomation** w systemie Linux. W przypadku systemu Linux Sprawdź, czy konto **nxautomation** ma dostęp do lokalizacji, w której są przechowywane moduły Runbook. W przypadku korzystania z polecenia cmdlet [Install-module](/powershell/module/powershellget/install-module) należy określić ALLUSERS dla `Scope` parametru, aby upewnić się, że konto **nxautomation** ma dostęp. Aby uzyskać więcej informacji na temat programu PowerShell w systemie Linux, zobacz [znane problemy dotyczące programu PowerShell na platformach innych niż Windows](/powershell/scripting/whats-new/what-s-new-in-powershell-70).

## <a name="configure-runbook-permissions"></a>Konfigurowanie uprawnień elementu Runbook

Zdefiniuj uprawnienia dla elementu Runbook do działania w hybrydowym procesie roboczym elementu Runbook w następujący sposób:

* Czy element Runbook zapewnia własne uwierzytelnianie do zasobów lokalnych.
* Skonfiguruj uwierzytelnianie przy użyciu [zarządzanych tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
* Określ konto Uruchom jako, aby podać kontekst użytkownika dla wszystkich elementów Runbook.

### <a name="use-runbook-authentication-to-local-resources"></a>Używanie uwierzytelniania elementu Runbook w zasobach lokalnych

W przypadku przygotowywania elementu Runbook, który zapewnia własne uwierzytelnianie dla zasobów, użyj zasobów [poświadczeń](./shared-resources/credentials.md) i [certyfikatów](./shared-resources/certificates.md) w elemencie Runbook. Istnieje kilka poleceń cmdlet, które umożliwiają określenie poświadczeń, aby element Runbook mógł być uwierzytelniany w różnych zasobach. Poniższy przykład przedstawia część elementu Runbook, który uruchamia ponownie komputer. Pobiera poświadczenia z zasobu poświadczeń i nazwy komputera z zasobu zmiennej, a następnie używa tych wartości z `Restart-Computer` poleceniem cmdlet.

```powershell
$Cred = Get-AutomationPSCredential -Name "MyCredential"
$Computer = Get-AutomationVariable -Name "ComputerName"

Restart-Computer -ComputerName $Computer -Credential $Cred
```

Można również użyć działania [InlineScript](automation-powershell-workflow.md#use-inlinescript) . `InlineScript` umożliwia uruchamianie bloków kodu na innym komputerze z poświadczeniami.

### <a name="use-runbook-authentication-with-managed-identities"></a><a name="runbook-auth-managed-identities"></a>Używanie uwierzytelniania elementu Runbook z tożsamościami zarządzanymi

Hybrydowe procesy robocze elementów Runbook w usłudze Azure Virtual Machines mogą używać tożsamości zarządzanych do uwierzytelniania w zasobach platformy Azure. Korzystanie z tożsamości zarządzanych dla zasobów platformy Azure zamiast kont Uruchom jako zapewnia korzyści, ponieważ nie ma potrzeby:

* Wyeksportuj certyfikat Uruchom jako, a następnie zaimportuj go do hybrydowego procesu roboczego elementu Runbook.
* Odnów certyfikat używany przez konto Uruchom jako.
* Obsługuj obiekt połączenia Uruchom jako w kodzie elementu Runbook.

Wykonaj kolejne kroki, aby użyć zarządzanej tożsamości dla zasobów platformy Azure w hybrydowym procesie roboczym elementu Runbook:

1. Utwórz maszynę wirtualną platformy Azure.
1. Skonfiguruj zarządzane tożsamości dla zasobów platformy Azure na maszynie wirtualnej. Zobacz [Konfigurowanie zarządzanych tożsamości dla zasobów platformy Azure na maszynie wirtualnej przy użyciu Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-vm).
1. Nadaj MASZYNom wirtualnym dostęp do grupy zasobów w Menedżer zasobów. Aby [uzyskać dostęp do Menedżer zasobów, Użyj tożsamości zarządzanej przypisanej przez system Windows VM](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager).
1. Zainstaluj hybrydowy proces roboczy elementu Runbook na maszynie wirtualnej. Zobacz [wdrażanie hybrydowego procesu roboczego elementu Runbook systemu Windows](automation-windows-hrw-install.md) lub [wdrażanie hybrydowego procesu roboczego elementu Runbook z systemem Linux](automation-linux-hrw-install.md).
1. Zaktualizuj element Runbook, aby użyć polecenia cmdlet [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) z `Identity` parametrem do uwierzytelniania w zasobach platformy Azure. Ta konfiguracja zmniejsza konieczność użycia konta Uruchom jako i umożliwia zarządzanie kontami skojarzonymi.

    ```powershell
    # Connect to Azure using the managed identities for Azure resources identity configured on the Azure VM that is hosting the hybrid runbook worker
    Connect-AzAccount -Identity

    # Get all VM names from the subscription
    Get-AzVM | Select Name
    ```

    > [!NOTE]
    > `Connect-AzAccount -Identity` działa dla hybrydowego procesu roboczego elementu Runbook przy użyciu tożsamości przypisanej do systemu i pojedynczej tożsamości przypisanej przez użytkownika. Jeśli używasz wielu tożsamości przypisanych przez użytkownika w hybrydowym procesie roboczym elementu Runbook, element Runbook musi określić `AccountId` parametr, `Connect-AzAccount` Aby wybrać określoną tożsamość przypisaną przez użytkownika.

### <a name="use-runbook-authentication-with-run-as-account"></a>Korzystanie z uwierzytelniania Runbook przy użyciu konta Uruchom jako

Zamiast używać elementu Runbook do udostępniania zasobów lokalnych, można określić konto Uruchom jako dla grupy hybrydowych procesów roboczych elementu Runbook. Aby określić konto Uruchom jako, należy zdefiniować [zasób poświadczeń](./shared-resources/credentials.md) , który ma dostęp do zasobów lokalnych. Te zasoby obejmują magazyny certyfikatów i wszystkie elementy Runbook są uruchamiane w ramach tych poświadczeń w hybrydowym procesie roboczym elementu Runbook w grupie.

- Nazwa użytkownika dla poświadczenia musi mieć jeden z następujących formatów:

   * użytkownika
   * username@domain
   * Nazwa użytkownika (dla kont lokalnych dla komputera lokalnego)

- Aby skorzystać z eksportu elementów Runbook programu PowerShell **— RunAsCertificateToHybridWorker**, należy zainstalować na komputerze lokalnym polecenie AZ module for Azure Automation.

#### <a name="use-a-credential-asset-to-specify-a-run-as-account"></a>Określ konto Uruchom jako za pomocą zasobu poświadczeń

Aby określić konto Uruchom jako dla grupy hybrydowych procesów roboczych elementu Runbook, wykonaj czynności opisane w poniższej procedurze:

1. Utwórz [zasób poświadczeń](./shared-resources/credentials.md) z dostępem do zasobów lokalnych.
1. Otwórz konto usługi Automation w Azure Portal.
1. Wybierz pozycję **grupy hybrydowych procesów roboczych**, a następnie wybierz konkretną grupę.
1. Wybierz **wszystkie ustawienia**, a następnie **Ustawienia grupy hybrydowych procesów roboczych**.
1. Zmień wartość parametru **Uruchom jako** **domyślną** na **niestandardowy**.
1. Wybierz poświadczenie, a następnie kliknij przycisk **Zapisz**.

## <a name="install-run-as-account-certificate"></a><a name="runas-script"></a>Instalowanie certyfikatu konta Uruchom jako

W ramach zautomatyzowanego procesu kompilacji na potrzeby wdrażania zasobów na platformie Azure może być konieczne uzyskanie dostępu do systemów lokalnych w celu obsługi zadania lub zestawu kroków w sekwencji wdrożenia. Aby zapewnić uwierzytelnianie na platformie Azure przy użyciu konta Uruchom jako, należy zainstalować certyfikat konta Uruchom jako.

>[!NOTE]
>Ten element Runbook programu PowerShell obecnie nie działa na maszynach z systemem Linux. Działa tylko na maszynach z systemem Windows.
>

Następujący element Runbook programu PowerShell o nazwie **Export-RunAsCertificateToHybridWorker** eksportuje certyfikat Uruchom jako z konta Azure Automation. Element Runbook pobiera i importuje certyfikat do magazynu certyfikatów na komputerze lokalnym w hybrydowym procesie roboczym elementu Runbook, który jest podłączony do tego samego konta. Po zakończeniu tego kroku element Runbook sprawdzi, czy proces roboczy może pomyślnie uwierzytelnić się na platformie Azure przy użyciu konta Uruchom jako.

>[!NOTE]
>Ten element Runbook programu PowerShell nie jest zaprojektowany ani przeznaczony do uruchamiania poza kontem usługi Automation jako skrypt na maszynie docelowej.
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
>Dla elementów Runbook programu PowerShell `Add-AzAccount` i `Add-AzureRMAccount` są to aliasy dla `Connect-AzAccount` . Jeśli przeszukiwanie elementów biblioteki nie jest widoczne, możesz `Connect-AzAccount` użyć programu `Add-AzAccount` lub zaktualizować moduły na koncie usługi Automation.

Aby zakończyć przygotowywanie konta Uruchom jako:

1. Zapisz element Runbook **Export-RunAsCertificateToHybridWorker** na komputerze z rozszerzeniem **. ps1** .
1. Zaimportuj ją na konto usługi Automation.
1. Edytuj element Runbook, zmieniając wartość `Password` zmiennej na własne hasło.
1. Opublikuj element Runbook.
1. Uruchom element Runbook, który jest przeznaczony dla grupy hybrydowych procesów roboczych elementu Runbook, która uruchamia i uwierzytelnia elementy Runbook przy użyciu konta Uruchom jako. 
1. Sprawdź strumień zadań, aby zobaczyć, że raport próbuje zaimportować certyfikat do lokalnego magazynu komputerowego, a następnie wiele wierszy. To zachowanie zależy od liczby kont usługi Automation zdefiniowanych w ramach subskrypcji i stopnia sukcesu uwierzytelniania.

## <a name="work-with-signed-runbooks-on-a-windows-hybrid-runbook-worker"></a>Pracuj z podpisanymi elementami Runbook w hybrydowym procesie roboczym elementu Runbook systemu Windows

Można skonfigurować hybrydowy proces roboczy elementu Runbook systemu Windows do uruchamiania tylko podpisanych elementów Runbook.

> [!IMPORTANT]
> Po skonfigurowaniu hybrydowego procesu roboczego elementu Runbook do uruchamiania tylko podpisanych elementów Runbook, niepodpisane elementy Runbook nie są wykonywane w procesie roboczym.

### <a name="create-signing-certificate"></a>Utwórz certyfikat podpisywania

Poniższy przykład tworzy certyfikat z podpisem własnym, który może służyć do podpisywania elementów Runbook. Ten kod tworzy certyfikat i eksportuje go tak, aby hybrydowy proces roboczy elementu Runbook mógł zaimportować go później. Odcisk palca jest również zwracany do późniejszego użycia w odniesieniu do certyfikatu.

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

### <a name="import-certificate-and-configure-workers-for-signature-validation"></a>Importowanie certyfikatu i konfigurowanie procesów roboczych do weryfikacji podpisu

Skopiuj utworzony certyfikat do każdego hybrydowego procesu roboczego elementu Runbook w grupie. Uruchom następujący skrypt, aby zaimportować certyfikat i skonfigurować procesy robocze do korzystania z walidacji podpisu w elementach Runbook.

```powershell
# Install the certificate into a location that will be used for validation.
New-Item -Path Cert:\LocalMachine\AutomationHybridStore
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\AutomationHybridStore

# Import the certificate into the trusted root store so the certificate chain can be validated
Import-Certificate -FilePath .\hybridworkersigningcertificate.cer -CertStoreLocation Cert:\LocalMachine\Root

# Configure the hybrid worker to use signature validation on runbooks.
Set-HybridRunbookWorkerSignatureValidation -Enable $true -TrustedCertStoreLocation "Cert:\LocalMachine\AutomationHybridStore"
```

### <a name="sign-your-runbooks-using-the-certificate"></a>Podpisywanie elementów Runbook przy użyciu certyfikatu

Hybrydowe procesy robocze elementu Runbook skonfigurowane do używania tylko podpisanych elementów Runbook muszą podpisywać elementy Runbook, które mają być używane w hybrydowym procesie roboczym elementu Runbook. Aby podpisać te elementy Runbook, użyj następującego przykładowego kodu programu PowerShell.

```powershell
$SigningCert = ( Get-ChildItem -Path cert:\LocalMachine\My\<CertificateThumbprint>)
Set-AuthenticodeSignature .\TestRunbook.ps1 -Certificate $SigningCert
```

Po podpisaniu elementu Runbook należy go zaimportować do konta usługi Automation i opublikować przy użyciu bloku podpisu. Aby dowiedzieć się, jak importować elementy Runbook, zobacz [Importowanie elementu Runbook](manage-runbooks.md#import-a-runbook).

## <a name="work-with-signed-runbooks-on-a-linux-hybrid-runbook-worker"></a>Pracuj z podpisanymi elementami Runbook w hybrydowym procesie roboczym elementu Runbook systemu Linux

Aby można było korzystać z podpisanych elementów Runbook, hybrydowy proces roboczy elementu Runbook systemu Linux musi mieć plik wykonywalny [GPG](https://gnupg.org/index.html) na komputerze lokalnym.

> [!IMPORTANT]
> Po skonfigurowaniu hybrydowego procesu roboczego elementu Runbook do uruchamiania tylko podpisanych elementów Runbook, niepodpisane elementy Runbook nie są wykonywane w procesie roboczym.

Wykonaj następujące kroki, aby ukończyć tę konfigurację:

* Tworzenie GPG i pary kluczy
* Udostępnienie dzwonka dla hybrydowego procesu roboczego elementu Runbook
* Sprawdź, czy sprawdzanie poprawności podpisu jest włączone
* Podpisywanie elementu Runbook

### <a name="create-a-gpg-keyring-and-keypair"></a>Tworzenie GPG i pary kluczy

Aby utworzyć GPG pęku kluczy i pary kluczy, użyj konta hybrydowego procesu roboczego elementu Runbook [nxautomation](automation-runbook-execution.md#log-analytics-agent-for-linux).

1. Użyj aplikacji sudo, aby zalogować się jako konto **nxautomation** .

    ```bash
    sudo su - nxautomation
    ```

1. Gdy korzystasz z programu **nxautomation**, wygeneruj parę kluczy GPG. GPG przeprowadzi Cię przez kroki. Musisz podać nazwę, adres e-mail, czas wygaśnięcia i hasło. Następnie poczekaj, aż na komputerze jest wystarczająca Entropia dla klucza do wygenerowania.

    ```bash
    sudo gpg --generate-key
    ```

1. Ponieważ katalog GPG został wygenerowany z sudo, należy zmienić jego właściciela na **nxautomation** przy użyciu następującego polecenia.

    ```bash
    sudo chown -R nxautomation ~/.gnupg
    ```

### <a name="make-the-keyring-available-to-the-hybrid-runbook-worker"></a>Udostępnienie dzwonka dla hybrydowego procesu roboczego elementu Runbook

Po utworzeniu dzwonka kluczy Udostępnij go dla hybrydowego procesu roboczego elementu Runbook. Zmodyfikuj plik Settings **/nxautomation/State/Worker. conf** , aby uwzględnić Poniższy przykładowy kod w sekcji pliku `[worker-optional]` .

```bash
gpg_public_keyring_path = /home/nxautomation/run/.gnupg/pubring.kbx
```

### <a name="verify-that-signature-validation-is-on"></a>Sprawdź, czy sprawdzanie poprawności podpisu jest włączone

Jeśli walidacja podpisu została wyłączona na komputerze, musisz ją włączyć, uruchamiając następujące polecenie sudo. Zamień `<LogAnalyticsworkspaceId>` na identyfikator obszaru roboczego.

```bash
sudo python /opt/microsoft/omsconfig/modules/nxOMSAutomationWorker/DSCResources/MSFT_nxOMSAutomationWorkerResource/automationworker/scripts/require_runbook_signature.py --true <LogAnalyticsworkspaceId>
```

### <a name="sign-a-runbook"></a>Podpisywanie elementu Runbook

Po skonfigurowaniu weryfikacji podpisu użyj następującego polecenia GPG, aby podpisać element Runbook.

```bash
gpg --clear-sign <runbook name>
```

Podpisany element Runbook ma nazwę **<runbook name> . asc**.

Możesz teraz przekazać podpisany element Runbook do Azure Automation i wykonać go jak zwykłego elementu Runbook.

## <a name="start-a-runbook-on-a-hybrid-runbook-worker"></a>Uruchamianie elementu Runbook w hybrydowym procesie roboczym elementu Runbook

[Uruchom element Runbook w Azure Automation](start-runbooks.md) opisuje różne metody uruchamiania elementu Runbook. Uruchomienie elementu Runbook w hybrydowym procesie roboczym elementu Runbook powoduje użycie opcji **Uruchom na** , która umożliwia określenie nazwy grupy hybrydowych procesów roboczych elementu Runbook. Po określeniu grupy jeden z procesów roboczych w tej grupie pobiera i uruchamia element Runbook. Jeśli element Runbook nie określa tej opcji, Azure Automation uruchamia element Runbook w zwykły sposób.

Po uruchomieniu elementu Runbook w Azure Portal zostanie wyświetlona opcja **Uruchom na** , dla której można wybrać **platformę Azure** lub **hybrydowy proces roboczy**. W przypadku wybrania opcji **hybrydowy proces roboczy** można wybrać grupę hybrydowych procesów roboczych elementu Runbook z listy rozwijanej.

Podczas uruchamiania elementu Runbook przy użyciu programu PowerShell należy użyć `RunOn` parametru z poleceniem cmdlet [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook) . Poniższy przykład używa środowiska Windows PowerShell, aby uruchomić element Runbook o nazwie **test-Runbook** w grupie hybrydowych procesów roboczych elementu Runbook o nazwie Moja hybrydowa.

```azurepowershell-interactive
Start-AzAutomationRunbook -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook" -RunOn "MyHybridGroup"
```

## <a name="logging"></a>Rejestrowanie

Aby ułatwić rozwiązywanie problemów z elementami Runbook uruchomionymi w hybrydowym procesie roboczym elementu Runbook, dzienniki są przechowywane lokalnie w następującej lokalizacji:

* W systemie Windows pod `C:\ProgramData\Microsoft\System Center\Orchestrator\<version>\SMA\Sandboxes` kątem szczegółowego rejestrowania procesów czasu wykonywania zadań. Zdarzenia stanu zadania elementu Runbook wysokiego poziomu są zapisywane w dzienniku zdarzeń **aplikacji i usług Logs\Microsoft-Automation\Operations** .

* W systemie Linux dzienniki hybrydowego procesu roboczego użytkownika znajdują się w lokalizacji `/home/nxautomation/run/worker.log` , a dzienniki procesów roboczych elementów Runbook można znaleźć pod adresem `/var/opt/microsoft/omsagent/run/automationworker/worker.log` .

## <a name="next-steps"></a>Następne kroki

* Jeśli elementy Runbook nie zakończą się pomyślnie, zapoznaj się z przewodnikiem rozwiązywania problemów z [błędami wykonywania elementu Runbook](troubleshoot/hybrid-runbook-worker.md#runbook-execution-fails).
* Aby uzyskać więcej informacji na temat programu PowerShell, w tym modułów dokumentacji i uczenia dotyczącej języka, zobacz dokumentację [programu PowerShell](/powershell/scripting/overview).
* Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz [AZ. Automation](/powershell/module/az.automation).
