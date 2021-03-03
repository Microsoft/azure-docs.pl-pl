---
title: Użyj alertu, aby wyzwolić Azure Automation element Runbook
description: W tym artykule opisano sposób wyzwalania elementu Runbook do uruchomienia w przypadku zgłoszenia alertu platformy Azure.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2021
ms.topic: conceptual
ms.openlocfilehash: 0483b171ee65ac55d65261140738bc5c1838873f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101732296"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Użyj alertu, aby wyzwolić Azure Automation element Runbook

Za pomocą [Azure monitor](../azure-monitor/overview.md) można monitorować metryki i dzienniki na poziomie podstawowym dla większości usług platformy Azure. Można wywoływać Azure Automation elementów Runbook za pomocą [grup akcji](../azure-monitor/platform/action-groups.md) do automatyzowania zadań na podstawie alertów. W tym artykule opisano sposób konfigurowania i uruchamiania elementu Runbook przy użyciu alertów.

## <a name="alert-types"></a>Typy alertów

Można używać elementów Runbook usługi Automation z trzema typami alertów:

* Typowe alerty
* Alerty dotyczące dziennika aktywności
* Alerty metryk niemal w czasie rzeczywistym

> [!NOTE]
> Typowy schemat alertów umożliwia standaryzację użycia powiadomień o alertach na platformie Azure już dziś. W przeszłości trzy typy alertów na platformie Azure dzisiaj (metryki, dzienniki i dziennik aktywności) miały własne szablony wiadomości e-mail, schematy elementu webhook itp. Aby dowiedzieć się więcej, zobacz [Common alert Schema](../azure-monitor/alerts/alerts-common-schema.md)

Gdy alert wywołuje element Runbook, rzeczywiste wywołanie to żądanie HTTP POST do elementu webhook. Treść żądania POST zawiera obiekt w formacie JSON, który ma użyteczne właściwości, które są związane z alertem. W poniższej tabeli przedstawiono linki do schematu ładunku dla każdego typu alertu:

|Alerty  |Opis|Schemat ładunku  |
|---------|---------|---------|
|[Typowy alert](../azure-monitor/alerts/alerts-common-schema.md)|Typowy schemat alertów, który umożliwia współczesne korzystanie z powiadomień o alertach na platformie Azure.|Schemat ładunku wspólnego alertu|
|[Alert dziennika aktywności](../azure-monitor/alerts/activity-log-alerts.md)    |Wysyła powiadomienie, gdy dowolne nowe zdarzenie w dzienniku aktywności platformy Azure dopasowuje określone warunki. Na przykład po `Delete VM` wystąpieniu operacji w **myProductionResourceGroup** lub po wyświetleniu nowego zdarzenia Azure Service Health z aktywnym stanem.| [Schemat ładunku alertu dziennika aktywności](../azure-monitor/alerts/activity-log-alerts-webhook.md)        |
|[Alert dotyczący metryki niemal w czasie rzeczywistym](../azure-monitor/alerts/alerts-metric-near-real-time.md)    |Wysyła powiadomienie szybciej niż alerty metryk, gdy jedna lub więcej metryk na poziomie platformy spełnia określone warunki. Na przykład, gdy wartość dla **procesora CPU%** na maszynie wirtualnej przekracza 90, a wartość dla **sieci** jest większa niż 500 MB w ciągu ostatnich 5 minut.| [Schemat ładunku alertu metryki niemal w czasie rzeczywistym](../azure-monitor/alerts/alerts-webhooks.md#payload-schema)          |

Ponieważ dane dostarczane przez każdy typ alertu różnią się od siebie, każdy typ alertu jest obsługiwany inaczej. W następnej sekcji dowiesz się, jak utworzyć element Runbook do obsługi różnych typów alertów.

## <a name="create-a-runbook-to-handle-alerts"></a>Tworzenie elementu Runbook do obsługi alertów

Aby korzystać z automatyzacji z alertami, potrzebny jest element Runbook, który ma logikę, która zarządza ładunkiem JSON, który jest przesyłany do elementu Runbook. Poniższy przykładowy element Runbook musi zostać wywołany z poziomu alertu platformy Azure.

Zgodnie z opisem w poprzedniej sekcji każdy typ alertu ma inny schemat. Skrypt pobiera dane elementu webhook z alertu w `WebhookData` parametrze wejściowym elementu Runbook. Następnie skrypt oblicza ładunek JSON, aby określić, który typ alertu jest używany.

Ten przykład używa alertu z maszyny wirtualnej. Pobiera dane dotyczące maszyn wirtualnych z ładunku, a następnie używa tych informacji do zatrzymania maszyny wirtualnej. Połączenie musi zostać skonfigurowane na koncie usługi Automation, na którym jest uruchomiony element Runbook. W przypadku wyzwalania elementów Runbook przy użyciu alertów ważne jest sprawdzenie stanu alertu w elemencie Runbook, który zostanie wyzwolony. Element Runbook jest wyzwalany za każdym razem, gdy zmienia się stan alertu. Alerty mają wiele stanów, a dwa Najczęstsze są aktywowane i rozwiązane. Sprawdź stan w logice elementu Runbook, aby upewnić się, że element Runbook nie działa więcej niż raz. W przykładzie w tym artykule pokazano, jak wyszukiwać alerty z aktywowanym stanem.

Element Runbook używa `AzureRunAsConnection` [konta Uruchom jako](./automation-security-overview.md) zasobu połączenia do uwierzytelniania za pomocą platformy Azure, aby wykonać akcję zarządzania względem maszyny wirtualnej.

Użyj tego przykładu, aby utworzyć element Runbook o nazwie **stop-AzureVmInResponsetoVMAlert**. Możesz zmodyfikować skrypt programu PowerShell i używać go z wieloma różnymi zasobami.

1. Przejdź do swojego konta Azure Automation.
2. W obszarze **Automatyzacja procesów** wybierz pozycję **elementy Runbook**.
3. W górnej części listy elementów Runbook wybierz pozycję **+ Utwórz element Runbook**.
4. Na stronie **Dodawanie elementu Runbook** wprowadź polecenie **stop-AzureVmInResponsetoVMAlert** dla nazwy elementu Runbook. W polu Typ elementu Runbook wybierz pozycję **PowerShell**. Następnie wybierz przycisk **Utwórz**.  
5. Skopiuj poniższy przykład programu PowerShell do strony **Edycja** .

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

6. Wybierz pozycję **Publikuj** , aby zapisać i opublikować element Runbook.

## <a name="create-the-alert"></a>Tworzenie alertu

Alerty korzystają z grup akcji, które są kolekcjami akcji wyzwalanych przez alert. Elementy Runbook są tylko jedną z wielu akcji, których można używać z grupami akcji.

1. Na koncie usługi Automation wybierz pozycję **alerty** w obszarze **monitorowanie**.
1. Wybierz pozycję **+ Nowa reguła alertu**.
1. Kliknij pozycję **Wybierz** w obszarze **zasób**. Na stronie **Wybierz zasób** wybierz maszynę wirtualną do wygenerowania alertu, a następnie kliknij pozycję **gotowe**.
1. Kliknij pozycję **Dodaj warunek** pod **warunkiem**. Wybierz sygnał, którego chcesz użyć, na przykład **procentowy udział procesora** i kliknij przycisk **gotowe**.
1. Na stronie **Konfiguruj logikę sygnału** wprowadź **wartość progową** w obszarze **logika alertu**, a następnie kliknij pozycję **gotowe**.
1. W obszarze **grupy akcji** wybierz pozycję **Utwórz nową**.
1. Na stronie **Dodaj grupę akcji** nadaj grupie akcji nazwę i krótką nazwę.
1. Nadaj nazwę akcji. W polu Typ akcji wybierz pozycję **element Runbook usługi Automation**.
1. Wybierz pozycję **Edytuj szczegóły**. Na stronie **Konfigurowanie elementu Runbook** w obszarze **Runbook Source** wybierz pozycję **użytkownik**.  
1. Wybierz swoją **subskrypcję** i **konto usługi Automation**, a następnie wybierz element Runbook **stop-AzureVmInResponsetoVMAlert** .  
1. Wybierz opcję **tak** , aby **włączyć wspólny schemat alertów**.
1. Aby utworzyć grupę akcji, wybierz **przycisk OK**.

    ![Strona dodawania grupy akcji](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    Tej grupy akcji można użyć w [alertach dziennika aktywności](../azure-monitor/alerts/activity-log-alerts.md) i [alertach niemal](../azure-monitor/alerts/alerts-overview.md) w czasie rzeczywistym, które tworzysz.

1. W obszarze **szczegóły alertu** Dodaj nazwę i opis reguły alertu, a następnie kliknij przycisk **Utwórz regułę alertu**.

## <a name="next-steps"></a>Następne kroki

* Aby uruchomić element Runbook za pomocą elementu webhook, zobacz temat [Uruchamianie elementu Runbook z elementu webhook](automation-webhooks.md).
* Aby poznać różne sposoby uruchamiania elementu Runbook, zobacz temat [Uruchamianie elementu Runbook](./start-runbooks.md).
* Aby utworzyć alert dziennika aktywności, zobacz [tworzenie alertów dziennika aktywności](../azure-monitor/alerts/activity-log-alerts.md).
* Aby dowiedzieć się, jak utworzyć alert niemal w czasie rzeczywistym, zobacz [Tworzenie reguły alertu w Azure Portal](../azure-monitor/alerts/alerts-metric.md?toc=/azure/azure-monitor/toc.json).
* Aby uzyskać informacje dotyczące poleceń cmdlet programu PowerShell, zobacz [AZ. Automation](/powershell/module/az.automation).
