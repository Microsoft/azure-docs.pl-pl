---
title: Wyzwalanie alertu za pomocą Azure Automation Runbook
description: W tym artykule opisano, jak wyzwolić element Runbook w celu uruchomienia go po wyzwoleniu alertu platformy Azure.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 36f1881ddd10498e7736de2d117a42021075abdc
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834880"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>Wyzwalanie alertu za pomocą Azure Automation Runbook

Za pomocą [Azure Monitor](../azure-monitor/overview.md) można monitorować metryki i dzienniki na poziomie bazowym dla większości usług na platformie Azure. Możesz wywołać Azure Automation Runbook przy użyciu grup [akcji](../azure-monitor/alerts/action-groups.md) w celu zautomatyzowania zadań w oparciu o alerty. W tym artykule pokazano, jak skonfigurować i uruchomić runbook przy użyciu alertów.

## <a name="alert-types"></a>Typy alertów

Elementów Runbook automatyzacji można używać z trzema typami alertów:

* Typowe alerty
* Alerty dotyczące dziennika aktywności
* Alerty metryk niemal w czasie rzeczywistym

> [!NOTE]
> Wspólny schemat alertów standaryzuje środowisko użycia powiadomień o alertach na platformie Azure już dziś. W przeszłości trzy typy alertów na platformie Azure (metryki, dziennik i dziennik aktywności) miały własne szablony wiadomości e-mail, schematy elementów webhook itp. Aby dowiedzieć się więcej, zobacz [Common alert schema (Wspólny schemat alertów)](../azure-monitor/alerts/alerts-common-schema.md)

Gdy alert wywołuje element Runbook, rzeczywiste wywołanie to żądanie HTTP POST do tego element webhook. Treść żądania POST zawiera obiekt w formacie JSON, który ma przydatne właściwości powiązane z alertem. W poniższej tabeli wymieniono linki do schematu ładunku dla każdego typu alertu:

|Alerty  |Opis|Schemat ładunku  |
|---------|---------|---------|
|[Wspólny alert](../azure-monitor/alerts/alerts-common-schema.md)|Wspólny schemat alertów, który standaryzuje środowisko użycia powiadomień o alertach na platformie Azure już dziś.|Wspólny schemat ładunku alertu|
|[Alert dziennika aktywności](../azure-monitor/alerts/activity-log-alerts.md)    |Wysyła powiadomienie, gdy dowolne nowe zdarzenie w dzienniku aktywności platformy Azure pasuje do określonych warunków. Na przykład gdy operacja zostanie `Delete VM` wyświetlona w grupie **myProductionResourceGroup** lub gdy zostanie Azure Service Health nowe zdarzenie o stanie Aktywne.| [Schemat ładunku alertu dziennika aktywności](../azure-monitor/alerts/activity-log-alerts-webhook.md)        |
|[Alert metryki niemal w czasie rzeczywistym](../azure-monitor/alerts/alerts-metric-near-real-time.md)    |Wysyła powiadomienie szybciej niż alerty metryk, gdy co najmniej jedna metryka na poziomie platformy spełnia określone warunki. Na przykład gdy wartość % procesora **CPU** na maszynie wirtualnej jest większa niż 90, a wartość dla ustawienia **Sieć** w jest większa niż 500 MB w ciągu ostatnich 5 minut.| [Schemat ładunku alertu metryki niemal w czasie rzeczywistym](../azure-monitor/alerts/alerts-webhooks.md#payload-schema)          |

Ponieważ dane dostarczane przez każdy typ alertu są różne, każdy typ alertu jest obsługiwany inaczej. W następnej sekcji dowiesz się, jak utworzyć podręcznik Runbook do obsługi różnych typów alertów.

## <a name="create-a-runbook-to-handle-alerts"></a>Tworzenie runbook do obsługi alertów

Aby używać usługi Automation z alertami, potrzebny jest podręcznik Runbook z logiką, która zarządza ładunkiem JSON alertu przekazywanym do tego typu. Poniższy przykładowy element Runbook musi zostać wywołany z alertu platformy Azure.

Zgodnie z opisem w poprzedniej sekcji każdy typ alertu ma inny schemat. Skrypt pobiera dane element webhook z alertu w `WebhookData` parametrze wejściowym runbook. Następnie skrypt ocenia ładunek JSON, aby określić, który typ alertu jest używany.

W tym przykładzie jest używany alert z maszyny wirtualnej. Pobiera dane maszyny wirtualnej z ładunku, a następnie używa tych informacji do zatrzymania maszyny wirtualnej. Połączenie musi zostać ustawione na koncie usługi Automation, na którym jest uruchamiany ten runbook. W przypadku używania alertów do wyzwalania elementów Runbook należy sprawdzić stan alertu w wyzwalanym elementie Runbook. Runbook jest wyzwalany za każdym razem, gdy alert zmienia stan. Alerty mają wiele stanów, z których dwa najczęściej są aktywowane i rozwiązane. Sprawdź stan logiki runbook, aby upewnić się, że nie zostanie uruchomiony więcej niż raz. W przykładzie w tym artykule pokazano, jak szukać alertów ze stanem Tylko aktywowane.

Za pomocą konta Uruchom jako zasobu połączenia do uwierzytelniania na platformie Azure w celu wykonania akcji zarządzania względem maszyny wirtualnej jest używany `AzureRunAsConnection` [](./automation-security-overview.md) zasób połączenia.

Użyj tego przykładu, aby utworzyć runbook **o nazwie Stop-AzureVmInResponsetoVMAlert.** Skrypt programu PowerShell można zmodyfikować i używać go z wieloma różnymi zasobami.

1. Przejdź do swojego Azure Automation konta.
2. W **obszarze Automatyzacja procesów** wybierz pozycję **Runbook.**
3. W górnej części listy elementów Runbook wybierz pozycję **+ Utwórz runbook.**
4. Na stronie **Dodawanie runbook** wprowadź **wartość Stop-AzureVmInResponsetoVMAlert** jako nazwę runbook. Jako typ runbook wybierz program **PowerShell.** Następnie wybierz przycisk **Utwórz**.  
5. Skopiuj poniższy przykład z programu PowerShell na **stronę** Edycja.

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

6. Wybierz **pozycję Publikuj,** aby zapisać i opublikować runbook.

## <a name="create-the-alert"></a>Tworzenie alertu

Alerty używają grup akcji, które są kolekcjami akcji wyzwalanych przez alert. Podręczniki Runbook to tylko jedna z wielu akcji, których można używać z grupami akcji.

1. Na koncie usługi Automation wybierz pozycję **Alerty w** obszarze **Monitorowanie.**
1. Wybierz pozycję **+ Nowa reguła alertu**.
1. Kliknij **pozycję Wybierz** w obszarze **Zasób.** Na stronie **Wybierz zasób** wybierz maszynę wirtualną, dla których ma być wykonywane alert, a następnie kliknij przycisk **Gotowe.**
1. Kliknij **pozycję Dodaj warunek** w **obszarze Warunek**. Wybierz sygnał, którego chcesz użyć, na przykład **Procentowe użycie procesora CPU,** a następnie kliknij przycisk **Gotowe.**
1. Na stronie **Konfigurowanie logiki sygnału** wprowadź wartość **progową** w obszarze **Logika alertu** i kliknij przycisk **Gotowe.**
1. W **obszarze Grupy akcji** wybierz pozycję Utwórz **nową.**
1. Na stronie **Dodawanie grupy akcji** nadaj grupie akcji nazwę i krótką nazwę.
1. Nadaj akcji nazwę. Jako typ akcji wybierz pozycję **Automation Runbook**.
1. Wybierz **pozycję Edytuj szczegóły.** Na stronie **Konfigurowanie runbook** w obszarze **Źródło runbook** wybierz pozycję **Użytkownik.**  
1. Wybierz konto **subskrypcji i** **usługi Automation,** a następnie wybierz runbook **Stop-AzureVmInResponsetoVMAlert.**  
1. Wybierz **pozycję Tak** dla opcji Włącz wspólny schemat **alertów.**
1. Aby utworzyć grupę akcji, wybierz przycisk **OK.**

    ![Dodawanie strony grupy akcji](./media/automation-create-alert-triggered-runbook/add-action-group.png)

    Tej grupy akcji można używać w alertach [dziennika](../azure-monitor/alerts/activity-log-alerts.md) aktywności i alertach [niemal](../azure-monitor/alerts/alerts-overview.md) w czasie rzeczywistym, które tworzysz.

1. W **obszarze Szczegóły alertu** dodaj nazwę i opis reguły alertu, a następnie kliknij **pozycję Utwórz regułę alertu.**

## <a name="next-steps"></a>Następne kroki

* Aby uruchomić runbook przy użyciu urządzenia webhook, zobacz [Start a runbook from a webhook](automation-webhooks.md)(Uruchamianie runbook z tego urządzenia).
* Aby poznać różne sposoby uruchamiania runbook, zobacz [Uruchamianie runbook.](./start-runbooks.md)
* Aby utworzyć alert dziennika aktywności, zobacz [Tworzenie alertów dziennika aktywności.](../azure-monitor/alerts/activity-log-alerts.md)
* Aby dowiedzieć się, jak utworzyć alert niemal w czasie rzeczywistym, zobacz [Tworzenie reguły alertu](../azure-monitor/alerts/alerts-metric.md?toc=/azure/azure-monitor/toc.json)w Azure Portal .
* Aby uzyskać informacje na temat poleceń cmdlet programu PowerShell, zobacz [Az.Automation](/powershell/module/az.automation).
