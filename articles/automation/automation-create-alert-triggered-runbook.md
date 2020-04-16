---
title: Wyzwalanie uruchomieniu księgi runbook usługi Azure Automation za pomocą alertu
description: Dowiedz się, jak wyzwolić element runbook do uruchomienia po wywołaniu alertu platformy Azure.
services: automation
ms.subservice: process-automation
ms.date: 04/29/2019
ms.topic: conceptual
ms.openlocfilehash: e8ddcaf6a5c9ab51147e540e2426ef8c4a1fdd3a
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392369"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Wyzwalanie uruchomieniu księgi runbook usługi Azure Automation za pomocą alertu

Usługi [Azure Monitor](../azure-monitor/overview.md?toc=%2fazure%2fautomation%2ftoc.json) umożliwiają monitorowanie metryk i dzienników na poziomie podstawowym dla większości usług na platformie Azure. Można wywołać elementy runbook usługi Azure Automation przy użyciu [grup akcji](../azure-monitor/platform/action-groups.md?toc=%2fazure%2fautomation%2ftoc.json) lub przy użyciu alertów klasycznych do automatyzacji zadań na podstawie alertów. W tym artykule pokazano, jak skonfigurować i uruchomić system runbook przy użyciu alertów.

>[!NOTE]
>Ten artykuł został zaktualizowany o korzystanie z nowego modułu Azure PowerShell Az. Nadal możesz używać modułu AzureRM, który będzie nadal otrzymywać poprawki błędów do co najmniej grudnia 2020 r. Aby dowiedzieć się więcej na temat nowego modułu Az i zgodności z modułem AzureRM, zobacz [Wprowadzenie do nowego modułu Az programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Aby uzyskać instrukcje instalacji modułu Az w hybrydowym usłudze Runbook Worker, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Dla konta automatyzacji można zaktualizować moduły do najnowszej wersji przy użyciu [jak zaktualizować moduły programu Azure PowerShell w usłudze Azure Automation.](automation-update-azure-modules.md)

## <a name="alert-types"></a>Typy alertów

Można użyć elementów runbook automatyzacji z trzema typami alertów:

* Typowe alerty
* Alerty dotyczące dziennika aktywności
* Alerty dotyczące danych w czasie zbliżonym do rzeczywistego

> [!NOTE]
> Wspólny schemat alertów standaryzuje środowisko zużycia powiadomień alertów na platformie Azure już dziś. Historycznie trzy typy alertów na platformie Azure dzisiaj (metryka, dziennik i dziennik aktywności) miały własne szablony wiadomości e-mail, schematy zestawów webhook itp. Aby dowiedzieć się więcej, zobacz [Wspólny schemat alertów](../azure-monitor/platform/alerts-common-schema.md)

Gdy alert wywołuje element runbook, rzeczywiste wywołanie jest żądaniem HTTP POST do elementu webhook. Treść żądania POST zawiera obiekt w formacie JSON, który ma przydatne właściwości, które są związane z alertem. Poniższa tabela zawiera łącza do schematu ładunku dla każdego typu alertu:

|Alerty  |Opis|Schemat ładunku  |
|---------|---------|---------|
|[Wspólny alert](../azure-monitor/platform/alerts-common-schema.md?toc=%2fazure%2fautomation%2ftoc.json)|Wspólny schemat alertów, który standaryzuje środowisko zużycia powiadomień alertów na platformie Azure już dziś.|Schemat wspólnego ładunku alertów|
|[Alert dziennika aktywności](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json)    |Wysyła powiadomienie, gdy każde nowe zdarzenie w dzienniku aktywności platformy Azure spełnia określone warunki. Na przykład, `Delete VM` gdy operacja występuje w **myProductionResourceGroup** lub gdy pojawi się nowe zdarzenie usługi Azure Service Health ze stanem Active.| [Schemat ładunku alertu dziennika aktywności](../azure-monitor/platform/activity-log-alerts-webhook.md)        |
|[Alert metryki w czasie zbliżonym do rzeczywistego](../azure-monitor/platform/alerts-metric-near-real-time.md?toc=%2fazure%2fautomation%2ftoc.json)    |Wysyła powiadomienie szybciej niż alerty metryki, gdy co najmniej jedna metryka na poziomie platformy spełnia określone warunki. Na przykład, gdy wartość **% procesora CPU** na maszynie Wirtualnej jest większa niż 90, a wartość **w sieci** jest większa niż 500 MB w ciągu ostatnich 5 minut.| [Schemat ładunku alertów alertów w czasie zbliżonym do czasu rzeczywistego](../azure-monitor/platform/alerts-webhooks.md#payload-schema)          |

Ponieważ dane, które są dostarczane przez każdy typ alertu jest inny, każdy typ alertu jest obsługiwany inaczej. W następnej sekcji dowiesz się, jak utworzyć projekt runbook do obsługi różnych typów alertów.

## <a name="create-a-runbook-to-handle-alerts"></a>Tworzenie śmiętu do obsługi alertów

Aby użyć automatyzacji z alertami, potrzebujesz systemu runbook, który ma logikę, która zarządza ładunkiem alertu JSON, który jest przekazywany do żyłaczki uruchomieniu. ścieliczkowych. Poniższy przykładowy element runbook musi być wywołany z alertu platformy Azure.

Zgodnie z opisem w poprzedniej sekcji każdy typ alertu ma inny schemat. Skrypt pobiera dane elementu webhook z `WebhookData` alertu w parametrze wejściowym elementu runbook. Następnie skrypt ocenia ładunek JSON, aby określić, który typ alertu jest używany.

W tym przykładzie użyto alertu z maszyny Wirtualnej. Pobiera dane maszyny Wirtualnej z ładunku, a następnie używa tych informacji, aby zatrzymać maszynę wirtualną. Połączenie musi być skonfigurowane na koncie automatyzacji, na którym jest uruchamiany system runbook. Podczas korzystania z alertów do wyzwalania śmięków runbook, ważne jest, aby sprawdzić stan alertu w uruchomieniu. ysu. Runbook wyzwala za każdym razem, gdy alert zmienia stan. Alerty mają wiele stanów, z dwoma najczęściej aktywowane i rozwiązane. Sprawdź stan w logice uruchomieniu, aby upewnić się, że projekt runbook nie jest uruchamiany więcej niż jeden raz. W przykładzie w tym artykule pokazano, jak szukać alertów ze stanem tylko aktywowane.

Grupa runbook używa `AzureRunAsConnection` [konta uruchom](automation-create-runas-account.md) jako konto połączenia do uwierzytelniania za pomocą platformy Azure w celu wykonania akcji zarządzania względem maszyny Wirtualnej.

W tym przykładzie można utworzyć projekt runbook o nazwie **Stop-AzureVmInResponsetoVMAlert**. Można zmodyfikować skrypt programu PowerShell i używać go z wieloma różnymi zasobami.

1. Przejdź do swojego konta usługi Azure Automation.
2. W obszarze **Automatyzacja procesów**wybierz pozycję **Runbooks**.
3. U góry listy śmięty wybierz **pozycję + Utwórz program runbook**.
4. Na stronie **Dodaj zestaw runbook** wprowadź **pozycję Stop-AzureVmInResponsetoVMAlert** dla nazwy zestawu runbook. Dla typu eks-u wybierz pozycję **PowerShell**. Następnie wybierz przycisk **Create** (Utwórz).  
5. Skopiuj następujący przykład programu PowerShell na stronę **Edycji.**

    ```powershell-interactive
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )
    $ErrorActionPreference = "stop"

    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)

        # Get the info needed to identify the VM (depends on the payload schema)
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "azureMonitorCommonAlertSchema") {
            # This is the common Metric Alert schema (released March 2019)
            $Essentials = [object] ($WebhookBody.data).essentials
            # Get the first target only as this script doesn't handle multiple
            $alertTargetIdArray = (($Essentials.alertTargetIds)[0]).Split("/")
            $SubId = ($alertTargetIdArray)[2]
            $ResourceGroupName = ($alertTargetIdArray)[4]
            $ResourceType = ($alertTargetIdArray)[6] + "/" + ($alertTargetIdArray)[7]
            $ResourceName = ($alertTargetIdArray)[-1]
            $status = $Essentials.monitorCondition
        }
        elseif ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # Schema not supported
            Write-Error "The alert data schema - $schemaId - is not supported."
        }

        Write-Verbose "status: $status" -Verbose
        if (($status -eq "Activated") -or ($status -eq "Fired"))
        {
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose

            # Determine code path depending on the resourceType
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is an Resource Manager VM
                Write-Verbose "This is an Resource Manager VM." -Verbose

                # Authenticate to Azure with service principal and certificate and set subscription
                Write-Verbose "Authenticating to Azure with service principal and certificate" -Verbose
                $ConnectionAssetName = "AzureRunAsConnection"
                Write-Verbose "Get connection asset: $ConnectionAssetName" -Verbose
                $Conn = Get-AutomationConnection -Name $ConnectionAssetName
                if ($Conn -eq $null)
                {
                    throw "Could not retrieve connection asset: $ConnectionAssetName. Check that this asset exists in the Automation account."
                }
                Write-Verbose "Authenticating to Azure with service principal." -Verbose
                Add-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint | Write-Verbose
                Write-Verbose "Setting subscription to work against: $SubId" -Verbose
                Set-AzContext -SubscriptionId $SubId -ErrorAction Stop | Write-Verbose

                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType not supported
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated' or 'Fired' so no action taken
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```

6. Wybierz **pozycję Publikuj,** aby zapisać i opublikować projekt runbook.

## <a name="create-the-alert"></a>Tworzenie alertu

Alerty używają grup akcji, które są kolekcjami akcji, które są wyzwalane przez alert. Podręczniki runbook to tylko jedna z wielu akcji, których można używać z grupami akcji.

1. Na koncie automatyzacji wybierz pozycję **Alerty** w obszarze **Monitorowanie**.
1. Wybierz pozycję **+ Nowa reguła alertu**.
1. Kliknij **pozycję Zaznacz** w obszarze **Zasób**. Na stronie **Wybierz zasób wybierz maszynę wirtualną,** aby otrzymywać alerty, a następnie kliknij przycisk **Gotowe**.
1. Kliknij **pozycję Dodaj warunek** w obszarze **Warunek**. Wybierz sygnał, którego chcesz użyć, na przykład **Procent procesora i** kliknij przycisk **Gotowe**.
1. Na stronie **Konfigurowanie logiki sygnału** wprowadź **wartość próg** w obszarze **Logika alertu**i kliknij przycisk **Gotowe**.
1. W obszarze **Grupy akcji**wybierz pozycję **Utwórz nowy**.
1. Na stronie **Dodawanie grupy akcji** nadaj grupie akcji nazwę i krótką nazwę.
1. Nadaj akcji nazwę. Dla typu akcji wybierz pozycję **System yklańnik automatyzacji**.
1. Wybierz **pozycję Edytuj szczegóły**. Na stronie **Konfigurowanie żyłajnika** w obszarze **Źródło księgi chyłkań**wybierz pozycję **Użytkownik**.  
1. Wybierz konto **subskrypcja** i **automatyzacja**, a następnie wybierz **stop-AzureVmInResponsetoVMAlert** runbook.  
1. Wybierz **pozycję Tak,** aby **włączyć wspólny schemat alertu**.
1. Aby utworzyć grupę akcji, wybierz przycisk **OK**.

    ![Dodawanie strony grupy akcji](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    Tej grupy akcji można użyć w [alertach dziennika aktywności](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json) i [alertach w czasie zbliżonym](../azure-monitor/platform/alerts-overview.md?toc=%2fazure%2fautomation%2ftoc.json) do rzeczywistego, które tworzysz.

1. W obszarze **Szczegóły alertu**dodaj nazwę i opis reguły alertu oraz kliknij pozycję **Utwórz regułę alertu**.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat uruchamiania elementu runbook automatyzacji przy użyciu elementu webhook, zobacz [Uruchamianie elementu runbook z elementu webhook](automation-webhooks.md).
* Aby uzyskać szczegółowe informacje na temat różnych sposobów uruchamiania śmiętu, zobacz [Uruchamianie ekstysu](automation-starting-a-runbook.md).
* Aby dowiedzieć się, jak utworzyć alert dziennika aktywności, zobacz [Tworzenie alertów dziennika aktywności](../azure-monitor/platform/activity-log-alerts.md?toc=%2fazure%2fautomation%2ftoc.json).
* Aby dowiedzieć się, jak utworzyć alert w czasie zbliżonym do rzeczywistego, zobacz [Tworzenie reguły alertów w witrynie Azure portal](../azure-monitor/platform/alerts-metric.md?toc=/azure/azure-monitor/toc.json).
* Aby uzyskać odwołanie do polecenia polecenia cmdlet programu PowerShell, zobacz [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
