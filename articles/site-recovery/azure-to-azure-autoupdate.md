---
title: Automatyczna aktualizacja usługi mobilności w Azure Site Recovery
description: Przegląd automatycznej aktualizacji usługi mobilności podczas replikowania maszyn wirtualnych platformy Azure przy użyciu Azure Site Recovery.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/02/2020
ms.author: sideeksh
ms.openlocfilehash: 42d3c74229ab7eeec0ac716073a9e631775fd002
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187344"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Automatyczna aktualizacja usługi mobilności w replikacji na platformie Azure na platformę Azure

Azure Site Recovery używa comiesięcznego wydania erze, aby rozwiązać wszelkie problemy i ulepszyć istniejące funkcje lub dodać nowe. Aby zachować aktualność w usłudze, należy zaplanować wdrożenie poprawek co miesiąc. Aby uniknąć narzutu związanego z każdym uaktualnieniem, można zezwolić Site Recovery na zarządzanie aktualizacjami składników.

Jak wspomniano w [architekturze odzyskiwania po awarii platformy Azure](azure-to-azure-architecture.md)na platformę Azure, usługa mobilności jest instalowana na wszystkich maszynach wirtualnych platformy Azure, które mają włączoną replikację z jednego regionu platformy Azure do innego. W przypadku korzystania z funkcji Aktualizacje automatyczne każda nowa wersja aktualizuje rozszerzenie usługi mobilności.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Jak działają aktualizacje automatyczne

Użycie Site Recovery do zarządzania aktualizacjami powoduje wdrożenie globalnego elementu Runbook (używanego przez usługi platformy Azure) za pośrednictwem konta usługi Automation utworzonego w tej samej subskrypcji co magazyn. Każdy magazyn używa jednego konta usługi Automation. Dla każdej maszyny wirtualnej w magazynie element Runbook sprawdza aktywne aktualizacje. Jeśli dostępna jest nowsza wersja rozszerzenia usługi mobilności, aktualizacja zostanie zainstalowana.

Domyślny harmonogram elementu Runbook występuje codziennie o godzinie 12:00 w strefie czasowej zreplikowanej maszyny wirtualnej. Harmonogram elementów Runbook można także zmienić za pomocą konta usługi Automation.

> [!NOTE]
> Począwszy od [pakietu zbiorczego aktualizacji 35](site-recovery-whats-new.md#updates-march-2019), można wybrać istniejące konto usługi Automation, które ma być używane na potrzeby aktualizacji. Przed aktualizacją pakietu zbiorczego 35, Site Recovery utworzyć konto usługi Automation domyślnie. Tę opcję można wybrać tylko po włączeniu replikacji dla maszyny wirtualnej. Nie jest dostępna dla maszyny wirtualnej, która ma już włączoną replikację. Wybrane ustawienie ma zastosowanie do wszystkich maszyn wirtualnych platformy Azure chronionych w tym samym magazynie.

Włączenie funkcji Aktualizacje automatyczne nie wymaga ponownego uruchomienia maszyn wirtualnych platformy Azure lub ma wpływ na trwającą replikację.

Rozliczenia zadania na koncie usługi Automation bazują na liczbie minut wykonywania zadań w miesiącu. Wykonanie zadania trwa kilka sekund do około minuty i jest objęte bezpłatną jednostką. Domyślnie 500 minut jest uwzględniana jako wolne jednostki dla konta usługi Automation, jak pokazano w poniższej tabeli:

| Bezpłatne jednostki (w każdym miesiącu) | Cena |
|---|---|
| Czas wykonywania zadania 500 minuty | ₹ 0.14/minutę

## <a name="enable-automatic-updates"></a>Włącz aktualizacje automatyczne

Istnieje kilka sposobów, Site Recovery mogą zarządzać aktualizacjami rozszerzeń:

- [Zarządzanie w ramach kroku włączania replikacji](#manage-as-part-of-the-enable-replication-step)
- [Przełącz ustawienia aktualizacji rozszerzenia w magazynie](#toggle-the-extension-update-settings-inside-the-vault)
- [Ręczne zarządzanie aktualizacjami](#manage-updates-manually)

### <a name="manage-as-part-of-the-enable-replication-step"></a>Zarządzanie w ramach kroku włączania replikacji

Po włączeniu replikacji dla maszyny wirtualnej, rozpoczynając [od widoku maszyny wirtualnej](azure-to-azure-quickstart.md) lub [z magazynu usługi Recovery Services](azure-to-azure-how-to-enable-replication.md), można zezwolić Site Recovery na zarządzanie aktualizacjami Site Recovery rozszerzenia lub zarządzać nimi ręcznie.

:::image type="content" source="./media/azure-to-azure-autoupdate/enable-rep.png" alt-text="Ustawienia rozszerzenia":::

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Przełącz ustawienia aktualizacji rozszerzenia w magazynie

1. W magazynie Recovery Services przejdź do pozycji **Zarządzaj**  >  **infrastrukturą Site Recovery**.
1. W obszarze **dla**  >  **opcji Ustawienia aktualizacji rozszerzenia** Virtual Machines Azure  >  **Zezwalaj Site Recovery na zarządzanie**, wybierz pozycję **włączone**.

   Aby zarządzać rozszerzeniem ręcznie, wybierz pozycję **wyłączone**.

1. Wybierz pozycję **Zapisz**.

:::image type="content" source="./media/azure-to-azure-autoupdate/vault-toggle.png" alt-text="Ustawienia aktualizacji rozszerzeń":::

> [!IMPORTANT]
> Po wybraniu opcji **zezwól Site Recovery na zarządzanie**, ustawienie jest stosowane do wszystkich maszyn wirtualnych w magazynie.

> [!NOTE]
> Każda opcja powiadamia użytkownika o koncie usługi Automation używanym do zarządzania aktualizacjami. Jeśli ta funkcja jest używana w magazynie po raz pierwszy, domyślnie tworzone jest nowe konto usługi Automation. Alternatywnie możesz dostosować ustawienie i wybrać istniejące konto usługi Automation. Po zdefiniowaniu wszystkie kolejne akcje umożliwiające replikację w tym samym magazynie będą używały wybranego konta usługi Automation. Obecnie menu rozwijane będzie wyświetlać tylko konta usługi Automation, które znajdują się w tej samej grupie zasobów co magazyn.

> [!IMPORTANT]
> Następujący skrypt musi zostać uruchomiony w kontekście konta usługi Automation.
W przypadku niestandardowego konta automatyzacji Użyj następującego skryptu:

```azurepowershell
param(
    [Parameter(Mandatory=$true)]
    [String] $VaultResourceId,
    [Parameter(Mandatory=$true)]
    [ValidateSet("Enabled",'Disabled')]
    [Alias("Enabled or Disabled")]
    [String] $AutoUpdateAction,
    [Parameter(Mandatory=$false)]
    [String] $AutomationAccountArmId
)
$SiteRecoveryRunbookName = "Modify-AutoUpdateForVaultForPatner"
$TaskId = [guid]::NewGuid().ToString()
$SubscriptionId = "00000000-0000-0000-0000-000000000000"
$AsrApiVersion = "2018-01-10"
$RunAsConnectionName = "AzureRunAsConnection"
$ArmEndPoint = "https://management.azure.com"
$AadAuthority = "https://login.windows.net/"
$AadAudience = "https://management.core.windows.net/"
$AzureEnvironment = "AzureCloud"
$Timeout = "160"
function Throw-TerminatingErrorMessage
{
        Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
        )
    throw ("Message: {0}, TaskId: {1}.") -f $Message, $TaskId
}
function Write-Tracing
{
        Param
    (
        [Parameter(Mandatory=$true)]
        [ValidateSet("Informational", "Warning", "ErrorLevel", "Succeeded", IgnoreCase = $true)]
                [String]
        $Level,
        [Parameter(Mandatory=$true)]
        [String]
        $Message,
            [Switch]
        $DisplayMessageToUser
        )
    Write-Output $Message
}
function Write-InformationTracing
{
        Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
        )
    Write-Tracing -Message $Message -Level Informational -DisplayMessageToUser
}
function ValidateInput()
{
    try
    {
        if(!$VaultResourceId.StartsWith("/subscriptions", [System.StringComparison]::OrdinalIgnoreCase))
        {
            $ErrorMessage = "The vault resource id should start with /subscriptions."
            throw $ErrorMessage
        }
        $Tokens = $VaultResourceId.SubString(1).Split("/")
        if(!($Tokens.Count % 2 -eq 0))
        {
            $ErrorMessage = ("Odd Number of tokens: {0}." -f $Tokens.Count)
            throw $ErrorMessage
        }
        if(!($Tokens.Count/2 -eq 4))
        {
            $ErrorMessage = ("Invalid number of resource in vault ARM id expected:4, actual:{0}." -f ($Tokens.Count/2))
            throw $ErrorMessage
        }
        if($AutoUpdateAction -ieq "Enabled" -and [string]::IsNullOrEmpty($AutomationAccountArmId))
        {
            $ErrorMessage = ("The automation account ARM id should not be null or empty when AutoUpdateAction is enabled.")
            throw $ErrorMessage
        }
    }
    catch
    {
        $ErrorMessage = ("ValidateInput failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}
function Initialize-SubscriptionId()
{
    try
    {
        $Tokens = $VaultResourceId.SubString(1).Split("/")
        $Count = 0
                $ArmResources = @{}
        while($Count -lt $Tokens.Count)
        {
            $ArmResources[$Tokens[$Count]] = $Tokens[$Count+1]
            $Count = $Count + 2
        }
                return $ArmResources["subscriptions"]
    }
    catch
    {
        Write-Tracing -Level ErrorLevel -Message ("Initialize-SubscriptionId: failed with [Exception: {0}]." -f $_.Exception) -DisplayMessageToUser
        throw
    }
}
function Invoke-InternalRestMethod($Uri, $Headers, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-RestMethod -Uri $Uri -Headers $Headers
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }
            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}
function Invoke-InternalWebRequest($Uri, $Headers, $Method, $Body, $ContentType, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-WebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -UseBasicParsing
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }
            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}
function Get-Header([ref]$Header, $AadAudience, $AadAuthority, $RunAsConnectionName){
    try
    {
        $RunAsConnection = Get-AutomationConnection -Name $RunAsConnectionName
        $TenantId = $RunAsConnection.TenantId
        $ApplicationId = $RunAsConnection.ApplicationId
        $CertificateThumbprint = $RunAsConnection.CertificateThumbprint
        $Path = "cert:\CurrentUser\My\{0}" -f $CertificateThumbprint
        $Secret = Get-ChildItem -Path $Path
        $ClientCredential = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.ClientAssertionCertificate(
                $ApplicationId,
                $Secret)
        # Trim the forward slash from the AadAuthority if it exist.
        $AadAuthority = $AadAuthority.TrimEnd("/")
        $AuthContext = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext(
            "{0}/{1}" -f $AadAuthority, $TenantId )
        $AuthenticationResult = $authContext.AcquireToken($AadAudience, $Clientcredential)
        $Header.Value['Content-Type'] = 'application\json'
        $Header.Value['Authorization'] = $AuthenticationResult.CreateAuthorizationHeader()
        $Header.Value["x-ms-client-request-id"] = $TaskId + "/" + (New-Guid).ToString() + "-" + (Get-Date).ToString("u")
    }
    catch
    {
        $ErrorMessage = ("Get-BearerToken: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}
function Get-ProtectionContainerToBeModified([ref] $ContainerMappingList)
{
    try
    {
        Write-InformationTracing ("Get protection container mappings : {0}." -f $VaultResourceId)
        $ContainerMappingListUrl = $ArmEndPoint + $VaultResourceId + "/replicationProtectionContainerMappings" + "?api-version=" + $AsrApiVersion
        Write-InformationTracing ("Getting the bearer token and the header.")
        Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
        $Result = @()
        Invoke-InternalRestMethod -Uri $ContainerMappingListUrl -Headers $header -Result ([ref]$Result)
        $ContainerMappings = $Result[0]
        Write-InformationTracing ("Total retrieved container mappings: {0}." -f $ContainerMappings.Value.Count)
        foreach($Mapping in $ContainerMappings.Value)
        {
            if(($Mapping.properties.providerSpecificDetails -eq $null) -or ($Mapping.properties.providerSpecificDetails.instanceType -ine "A2A"))
            {
                Write-InformationTracing ("Mapping properties: {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the provider does not match." -f ($Mapping.Id))
                continue;
            }
            if($Mapping.Properties.State -ine "Paired")
            {
                Write-InformationTracing ("Ignoring container mapping: {0} as the state is not paired." -f ($Mapping.Id))
                continue;
            }
            Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties.providerSpecificDetails))
            $MappingAutoUpdateStatus = $Mapping.properties.providerSpecificDetails.agentAutoUpdateStatus
            $MappingAutomationAccountArmId = $Mapping.properties.providerSpecificDetails.automationAccountArmId
            $MappingHealthErrorCount = $Mapping.properties.HealthErrorDetails.Count
            if($AutoUpdateAction -ieq "Enabled" -and
                ($MappingAutoUpdateStatus -ieq "Enabled") -and
                ($MappingAutomationAccountArmId -ieq $AutomationAccountArmId) -and
                ($MappingHealthErrorCount -eq 0))
            {
                Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the auto update is already enabled and is healthy." -f ($Mapping.Id))
                continue;
            }
            ($ContainerMappingList.Value).Add($Mapping.id)
        }
    }
    catch
    {
        $ErrorMessage = ("Get-ProtectionContainerToBeModified: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}
$OperationStartTime = Get-Date
$ContainerMappingList = New-Object System.Collections.Generic.List[System.String]
$JobsInProgressList = @()
$JobsCompletedSuccessList = @()
$JobsCompletedFailedList = @()
$JobsFailedToStart = 0
$JobsTimedOut = 0
$Header = @{}
$AzureRMProfile = Get-Module -ListAvailable -Name AzureRM.Profile | Select Name, Version, Path
$AzureRmProfileModulePath = Split-Path -Parent $AzureRMProfile.Path
Add-Type -Path (Join-Path $AzureRmProfileModulePath "Microsoft.IdentityModel.Clients.ActiveDirectory.dll")
$Inputs = ("Tracing inputs VaultResourceId: {0}, Timeout: {1}, AutoUpdateAction: {2}, AutomationAccountArmId: {3}." -f $VaultResourceId, $Timeout, $AutoUpdateAction, $AutomationAccountArmId)
Write-Tracing -Message $Inputs -Level Informational -DisplayMessageToUser
$CloudConfig = ("Tracing cloud configuration ArmEndPoint: {0}, AadAuthority: {1}, AadAudience: {2}." -f $ArmEndPoint, $AadAuthority, $AadAudience)
Write-Tracing -Message $CloudConfig -Level Informational -DisplayMessageToUser
$AutomationConfig = ("Tracing automation configuration RunAsConnectionName: {0}." -f $RunAsConnectionName)
Write-Tracing -Message $AutomationConfig -Level Informational -DisplayMessageToUser
ValidateInput
$SubscriptionId = Initialize-SubscriptionId
Get-ProtectionContainerToBeModified ([ref]$ContainerMappingList)
$Input = @{
  "properties"= @{
    "providerSpecificInput"= @{
        "instanceType" = "A2A"
        "agentAutoUpdateStatus" = $AutoUpdateAction
        "automationAccountArmId" = $AutomationAccountArmId
    }
  }
}
$InputJson = $Input |  ConvertTo-Json
if ($ContainerMappingList.Count -eq 0)
{
    Write-Tracing -Level Succeeded -Message ("Exiting as there are no container mappings to be modified.") -DisplayMessageToUser
    exit
}
Write-InformationTracing ("Container mappings to be updated has been retrieved with count: {0}." -f $ContainerMappingList.Count)
try
{
    Write-InformationTracing ("Start the modify container mapping jobs.")
    ForEach($Mapping in $ContainerMappingList)
    {
    try {
            $UpdateUrl = $ArmEndPoint + $Mapping + "?api-version=" + $AsrApiVersion
            Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
            $Result = @()
            Invoke-InternalWebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -Result ([ref]$Result)
            $Result = $Result[0]
            $JobAsyncUrl = $Result.Headers['Azure-AsyncOperation']
            Write-InformationTracing ("The modify container mapping job invoked with async url: {0}." -f $JobAsyncUrl)
            $JobsInProgressList += $JobAsyncUrl;
            # Rate controlling the set calls to maximum 60 calls per minute.
            # ASR throttling for set calls is 200 in 1 minute.
            Start-Sleep -Milliseconds 1000
        }
        catch{
            Write-InformationTracing ("The modify container mappings job creation failed for: {0}." -f $Ru)
            Write-InformationTracing $_
            $JobsFailedToStart++
        }
    }
    Write-InformationTracing ("Total modify container mappings has been initiated: {0}." -f $JobsInProgressList.Count)
}
catch
{
    $ErrorMessage = ("Modify container mapping jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
try
{
    while($JobsInProgressList.Count -ne 0)
    {
        Sleep -Seconds 30
        $JobsInProgressListInternal = @()
        ForEach($JobAsyncUrl in $JobsInProgressList)
        {
            try
            {
                Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
                $Result = Invoke-RestMethod -Uri $JobAsyncUrl -Headers $header
                $JobState = $Result.Status
                if($JobState -ieq "InProgress")
                {
                    $JobsInProgressListInternal += $JobAsyncUrl
                }
                elseif($JobState -ieq "Succeeded" -or `
                    $JobState -ieq "PartiallySucceeded" -or `
                    $JobState -ieq "CompletedWithInformation")
                {
                    Write-InformationTracing ("Jobs succeeded with state: {0}." -f $JobState)
                    $JobsCompletedSuccessList += $JobAsyncUrl
                }
                else
                {
                    Write-InformationTracing ("Jobs failed with state: {0}." -f $JobState)
                    $JobsCompletedFailedList += $JobAsyncUrl
                }
            }
            catch
            {
                Write-InformationTracing ("The get job failed with: {0}. Ignoring the exception and retrying the next job." -f $_.Exception)
                # The job on which the tracking failed, will be considered in progress and tried again later.
                $JobsInProgressListInternal += $JobAsyncUrl
            }
            # Rate controlling the get calls to maximum 120 calls each minute.
            # ASR throttling for get calls is 10000 in 60 minutes.
            Start-Sleep -Milliseconds 500
        }
        Write-InformationTracing ("Jobs remaining {0}." -f $JobsInProgressListInternal.Count)
        $CurrentTime = Get-Date
        if($CurrentTime -gt $OperationStartTime.AddMinutes($Timeout))
        {
            Write-InformationTracing ("Tracing modify cloud pairing jobs has timed out.")
            $JobsTimedOut = $JobsInProgressListInternal.Count
            $JobsInProgressListInternal = @()
        }
        $JobsInProgressList = $JobsInProgressListInternal
    }
}
catch
{
    $ErrorMessage = ("Tracking modify cloud pairing jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage  -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
Write-InformationTracing ("Tracking modify cloud pairing jobs completed.")
Write-InformationTracing ("Modify cloud pairing jobs success: {0}." -f $JobsCompletedSuccessList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed: {0}." -f $JobsCompletedFailedList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed to start: {0}." -f $JobsFailedToStart)
Write-InformationTracing ("Modify cloud pairing jobs timedout: {0}." -f $JobsTimedOut)
if($JobsTimedOut -gt  0)
{
    $ErrorMessage = "One or more modify cloud pairing jobs has timedout."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
elseif($JobsCompletedSuccessList.Count -ne $ContainerMappingList.Count)
{
    $ErrorMessage = "One or more modify cloud pairing jobs failed."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
Write-Tracing -Level Succeeded -Message ("Modify cloud pairing completed.") -DisplayMessageToUser
```

### <a name="manage-updates-manually"></a>Ręczne zarządzanie aktualizacjami

1. Jeśli na maszynach wirtualnych są zainstalowane nowe aktualizacje usługi mobilności, zobaczysz następujące powiadomienie: **dostępna jest nowa aktualizacja agenta Site Recovery replikacji. Kliknij, aby zainstalować.**

   :::image type="content" source="./media/vmware-azure-install-mobility-service/replicated-item-notif.png" alt-text="Okno zreplikowane elementy":::

1. Wybierz powiadomienie, aby otworzyć stronę wyboru maszyny wirtualnej.
1. Wybierz Maszyny wirtualne, które chcesz uaktualnić, a następnie wybierz przycisk **OK**. Usługa mobilności aktualizacji rozpocznie się dla każdej wybranej maszyny wirtualnej.

   :::image type="content" source="./media/vmware-azure-install-mobility-service/update-okpng.png" alt-text="Lista replikowanych elementów maszyn wirtualnych":::

## <a name="common-issues-and-troubleshooting"></a>Typowe problemy i rozwiązywanie problemów

Jeśli wystąpi problem z aktualizacjami automatycznymi, zobaczysz powiadomienie o błędzie w obszarze **problemy z konfiguracją** na pulpicie nawigacyjnym magazynu.

Jeśli nie możesz włączyć funkcji Aktualizacje automatyczne, zobacz następujące typowe błędy i zalecane akcje:

- **Błąd**: nie masz uprawnień do utworzenia konta Uruchom jako platformy Azure (nazwy głównej usługi) i Udziel roli współautor do jednostki usługi.

  **Zalecana akcja**: Upewnij się, że konto zalogowane jest przypisane jako współautor, i spróbuj ponownie. Więcej informacji o przypisywaniu uprawnień znajduje się w sekcji wymagane uprawnienia w temacie [How to: Use the Portal Service, aby utworzyć aplikację usługi Azure AD i nazwę główną usługi, które mogą uzyskiwać dostęp do zasobów](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

  Aby rozwiązać większość problemów po włączeniu funkcji Aktualizacje automatyczne, wybierz pozycję **napraw**. Jeśli przycisk Napraw nie jest dostępny, Sprawdź komunikat o błędzie wyświetlany w okienku Ustawienia aktualizacji rozszerzenia.

  :::image type="content" source="./media/azure-to-azure-autoupdate/repair.png" alt-text="Przycisk naprawy usługi Site Recovery w ustawieniach aktualizacji rozszerzenia":::

- **Błąd**: konto Uruchom jako nie ma uprawnień dostępu do zasobu usługi Recovery Services.

  **Zalecana akcja**: Usuń, a następnie [Utwórz ponownie konto Uruchom jako](../automation/manage-runas-account.md). Lub upewnij się, że aplikacja Azure Active Directory konta Uruchom jako usługi Automation ma dostęp do zasobu usługi Recovery Services.

- **Błąd**: nie można odnaleźć konta Uruchom jako. Jeden z tych elementów został usunięty lub nie został utworzony — aplikacja Azure Active Directory, nazwa główna usługi, rola, zasób certyfikatu usługi Automation, zasób połączenia usługi Automation lub odcisk palca nie są identyczne między certyfikatem a połączeniem.

  **Zalecana akcja**: Usuń, a następnie [Utwórz ponownie konto Uruchom jako](../automation/manage-runas-account.md).

- **Błąd**: certyfikat Uruchom jako platformy Azure używany przez konto usługi Automation wkrótce wygaśnie.

  Certyfikat z podpisem własnym utworzony dla konta Uruchom jako wygasa w jednym roku od daty utworzenia. Można go odnowić w dowolnym momencie przed wygaśnięciem jego ważności. Jeśli zarejestrowano się w celu otrzymywania powiadomień pocztą e-mail, otrzymasz również wiadomości e-mail, gdy wymagana jest akcja z Twojej strony. Ten błąd zostanie wyświetlony dwa miesiące przed datą wygaśnięcia i zmieni się na błąd krytyczny, jeśli certyfikat wygasł. Po wygaśnięciu certyfikatu funkcja autoaktualizacji nie będzie działać, dopóki nie odnowisz tego samego.

  **Zalecana akcja**: Aby rozwiązać ten problem, wybierz pozycję **napraw** , a następnie **odnów certyfikat**.

  :::image type="content" source="./media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG" alt-text="Odnów certyfikat":::

  > [!NOTE]
  > Po odnowieniu certyfikatu Odśwież stronę, aby wyświetlić bieżący stan.
