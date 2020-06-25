---
title: Azure Service Bus — automatycznie Aktualizuj jednostki obsługi komunikatów
description: W tym artykule pokazano, jak za pomocą Azure Automation elementu Runbook automatycznie aktualizować jednostki obsługi komunikatów Service Bus przestrzeni nazw.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 52f5b13b482739bfa56ff606f684fd5a9c7d3b6e
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/24/2020
ms.locfileid: "85341500"
---
# <a name="automatically-update-messaging-units-of-an-azure-service-bus-namespace"></a>Automatycznie Aktualizuj jednostki obsługi komunikatów Azure Service Bus przestrzeni nazw 
W tym artykule pokazano, jak można automatycznie aktualizować [jednostki obsługi komunikatów](service-bus-premium-messaging.md) Service Bus przestrzeni nazw na podstawie użycia zasobów (procesora lub pamięci). 

W przykładzie w tym artykule pokazano, jak zwiększyć liczbę jednostek obsługi komunikatów dla przestrzeni nazw Service Bus, gdy użycie procesora przez przestrzeń nazw przekracza 75%. Ogólne czynności są następujące:

1. Utwórz element Runbook Azure Automation przy użyciu skryptu programu PowerShell, który skaluje (zwiększa) jednostki obsługi komunikatów dla Service Bus przestrzeni nazw. 
2. Utwórz alert użycia procesora CPU w Service Bus przestrzeni nazw, który wywoła skrypt programu PowerShell, gdy użycie procesora w przestrzeni nazw spadnie powyżej 75%. 

> [!IMPORTANT]
> Ten artykuł ma zastosowanie tylko do warstwy **premium** Azure Service Bus. 


## <a name="create-a-service-bus-namespace"></a>Tworzenie przestrzeni nazw usługi Service Bus
Utwórz warstwę Premium Service Bus przestrzeni nazw. Wykonaj kroki z sekcji [Tworzenie przestrzeni nazw w artykule Azure Portal,](service-bus-quickstart-portal.md#create-a-namespace-in-the-azure-portal) aby utworzyć obszar nazw. 

## <a name="create-an-azure-automation-account"></a>Tworzenie konta usługi Azure Automation
Utwórz konto Azure Automation, wykonując instrukcje podane w artykule [Tworzenie konta usługi Azure Automation](../automation/automation-quickstart-create-account.md) . 

## <a name="import-azservice-module-from-gallery"></a>Importuj AZ. Service Module z galerii
Importuj `Az.Accounts` i `Az.ServiceBus` moduły z galerii do konta usługi Automation. `Az.ServiceBus`Moduł jest zależny od `Az.Accounts` modułu, dlatego musi być zainstalowany jako pierwszy. 

Aby uzyskać instrukcje krok po kroku, zobacz [Importowanie modułu z galerii modułów](../automation/automation-runbook-gallery.md#import-a-module-from-the-module-gallery-with-the-azure-portal).

## <a name="create-and-publish-a-powershell-runbook"></a>Tworzenie i publikowanie elementu Runbook programu PowerShell

1. Utwórz element Runbook programu PowerShell, postępując zgodnie z instrukcjami zawartymi w artykule [Tworzenie elementu Runbook programu PowerShell](../automation/automation-quickstart-create-runbook.md) . 

    Oto przykładowy skrypt programu PowerShell, którego można użyć do zwiększenia jednostek obsługi komunikatów dla przestrzeni nazw Service Bus. Ten skrypt programu PowerShell w elemencie Runbook usługi Automation zwiększa MUs od 1 do 2, od 2 do 4 lub od 4 do 8. Dozwolone wartości tej właściwości to: 1, 2, 4 i 8. Można utworzyć inny element Runbook, aby zmniejszyć liczbę jednostek obsługi komunikatów.

    Parametry **NamespaceName** i **resourceGroupName** są używane do testowania skryptu niezależnie od scenariusza alertu. 
    
    Parametr **WebHookData** jest przeznaczony dla alertu do przekazywania informacji, takich jak nazwa grupy zasobów, nazwa zasobu itp. w czasie wykonywania. 

    ```powershell
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData,
    
        [Parameter (Mandatory = $false)]
        [String] $namespaceName,
    
        [Parameter (Mandatory = $false)]
        [String] $resourceGroupName
    )
    
    
    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    
        # Get the alert schema ID
        $schemaId = $WebhookBody.schemaId

        # If it's a metric alert
        if ($schemaId -eq "AzureMonitorMetricAlert") {

            # Get the resource group name from the alert context
            $resourceGroupName = $WebhookBody.resourceGroupName
            
            # Get the namespace name from the alert context
            $namespaceName = $WebhookBody.resourceName
        }
    }
    
    # Connect to Azure account
    $connection = Get-AutomationConnection -Name AzureRunAsConnection
    
    while(!($connectionResult) -And ($logonAttempt -le 10))
    {
        $LogonAttempt++
        # Logging in to Azure...
        $connectionResult =    Connect-AzAccount `
                                    -ServicePrincipal `
                                    -Tenant $connection.TenantID `
                                    -ApplicationId $connection.ApplicationID `
                                    -CertificateThumbprint $connection.CertificateThumbprint
    
        Start-Sleep -Seconds 30
    }
    
    # Get the current capacity (number of messaging units) of the namespace
    $sbusns=Get-AzServiceBusNamespace `
        -Name $namespaceName `
        -ResourceGroupName $resourceGroupName
    
    $currentCapacity = $sbusns.Sku.Capacity
    
    # Increase the capacity
    # Capacity can be one of these values: 1, 2, 4, 8
    if ($currentCapacity -eq 1) {
        $newMU = 2
    }
    elseif ($currentCapacity -eq 2) {
        $newMU = 4
    }
    elseif ($currentCapacity -eq 4) {
        $newMU = 8    
    }
    else {
    
    }
    
    # Update the capacity of the namespace
    Set-AzServiceBusNamespace `
            -Location eastus `
            -SkuName Premium `
            -Name $namespaceName `
            -SkuCapacity $newMU `
            -ResourceGroupName $resourceGroupName

    ```
2. [Przetestuj skoroszyt](../automation/manage-runbooks.md#test-a-runbook) , określając wartości dla parametrów **NamespaceName** i **resourceGroupName** . Upewnij się, że jednostki obsługi komunikatów w przestrzeni nazw zostały zaktualizowane. 
3. Po pomyślnym przetestowaniu [skoroszytu Opublikuj skoroszyt](..//automation/manage-runbooks.md#publish-a-runbook) , aby był dostępny do dodania jako akcja dla alertu w przestrzeni nazw później. 

## <a name="create-an-alert-on-the-namespace-to-trigger-the-runbook"></a>Utwórz alert w przestrzeni nazw, aby wyzwolić element Runbook
Aby wyzwolić utworzony element Runbook usługi Automation, zobacz temat [Używanie alertu do wyzwolenia artykułu Azure Automation elementu Runbook](../automation/automation-create-alert-triggered-runbook.md) w celu skonfigurowania alertu w przestrzeni nazw Service Bus. Na przykład można utworzyć alert dotyczący **użycia procesora CPU według przestrzeni nazw** lub **rozmiaru pamięci dla metryki przestrzeni nazw** oraz dodać akcję w celu wyzwolenia utworzonego elementu Runbook usługi Automation. Aby uzyskać szczegółowe informacje o tych metrykach, zobacz [metryki użycia zasobów](service-bus-metrics-azure-monitor.md#resource-usage-metrics).

Poniższa procedura przedstawia sposób tworzenia alertu, który wyzwala element Runbook usługi Automation, gdy **użycie procesora** przestrzeni nazw przekracza **75%**.

1. Na stronie **obszar nazw Service Bus** dla obszaru nazw wybierz pozycję **alerty** w menu po lewej stronie, a następnie wybierz pozycję **+ Nowa reguła alertu** na pasku narzędzi. 
    
    ![Strona alertów — przycisk Nowa reguła alertu](./media/automate-update-messaging-units/alerts-page.png)
2. Na stronie **Tworzenie reguły alertu** kliknij pozycję **Wybierz warunek**. 

    ![Strona tworzenia reguły alertu — wybór warunku](./media/automate-update-messaging-units/alert-rule-select-condition.png) 
3. Na stronie **Konfiguruj logikę sygnału** wybierz opcję **procesor CPU** dla sygnału. 

    ![Konfiguruj logikę sygnałów — wybierz procesor CPU](./media/automate-update-messaging-units/configure-signal-logic.png)
4. Wprowadź **wartość progową** (w tym przykładzie jest to **75%**), a następnie wybierz pozycję **gotowe**. 

    ![Konfigurowanie sygnału procesora](./media/automate-update-messaging-units/cpu-signal-configuration.png)
5. Teraz na **stronie Tworzenie alertu**kliknij pozycję **Wybierz grupę akcji**.
    
    ![Wybierz grupę akcji](./media/automate-update-messaging-units/select-action-group-button.png)
6. Na pasku narzędzi wybierz przycisk **Utwórz grupę akcji** . 

    ![Przycisk tworzenia grupy akcji](./media/automate-update-messaging-units/create-action-group-button.png)
7. Na stronie **Dodawanie grupy akcji** wykonaj następujące czynności:
    1. Wprowadź **nazwę** grupy akcji. 
    2. Wprowadź **krótką nazwę** grupy akcji.
    3. Wybierz **subskrypcję** , w której chcesz utworzyć tę grupę akcji.
    4. Wybierz **grupę zasobów**. 
    5. W sekcji **Akcje** wprowadź **nazwę akcji**i wybierz **element Runbook Automation** dla **typu akcji**. 

        ![Strona dodawania grupy akcji](./media/automate-update-messaging-units/add-action-group-page.png)
8. Na stronie **Konfigurowanie elementu Runbook** wykonaj następujące czynności:
    1. W obszarze **źródło elementu Runbook**wybierz pozycję **użytkownik**. 
    2. W polu **subskrypcja**wybierz **subskrypcję** platformy Azure, która zawiera konto usługi Automation. 
    3. W przypadku **konta usługi Automation**wybierz swoje **konto usługi Automation**.
    4. W obszarze **Runbook**wybierz element Runbook. 
    5. Na stronie **Konfigurowanie elementu Runbook** wybierz pozycję **OK** . 
        ![Konfigurowanie elementu Runbook](./media/automate-update-messaging-units/configure-runbook.png)
9. Na stronie **Dodaj grupę akcji** wybierz **przycisk OK** . 
5. Teraz na stronie **Tworzenie reguły alertu** wprowadź **nazwę reguły**, a następnie wybierz pozycję **Utwórz regułę alertu**. 
    ![Tworzenie reguły alertu](./media/automate-update-messaging-units/create-alert-rule.png)

    > [!NOTE]
    > Teraz, gdy użycie procesora CPU przestrzeni nazw przekracza 75, alert wyzwala element Runbook automatyzacji, który zwiększa jednostki obsługi komunikatów przestrzeni nazw Service Bus. Podobnie można utworzyć alert dla innego elementu Runbook usługi Automation, który zmniejsza liczbę jednostek obsługi komunikatów, jeśli użycie procesora w przestrzeni nazw spadnie poniżej 25. 

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o jednostkach obsługi komunikatów, zobacz [Obsługa komunikatów w warstwie Premium](service-bus-premium-messaging.md)
