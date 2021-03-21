---
title: 'Azure VPN Gateway: Konfigurowanie alertów dotyczących zdarzeń dziennika zasobów diagnostycznych'
description: Dowiedz się, jak skonfigurować alerty na podstawie zdarzeń dzienników zasobów z usługi Azure VPN Gateway przy użyciu Azure Monitor Log Analytics.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 11/18/2020
ms.author: alzam
ms.openlocfilehash: 70cac6ef566ef52409cd9667708c2fc297e046f7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97656618"
---
# <a name="set-up-alerts-on-resource-log-events-from-vpn-gateway"></a>Konfigurowanie alertów dotyczących zdarzeń dzienników zasobów z VPN Gateway

Ten artykuł ułatwia Konfigurowanie alertów opartych na zdarzeniach dzienników zasobów z platformy Azure VPN Gateway przy użyciu Azure Monitor Log Analytics. 

Następujące dzienniki zasobów są dostępne na platformie Azure:

|***Nazwa** _ | _ *_Opis_** |
|--- | --- |
|GatewayDiagnosticLog | Zawiera dzienniki zasobów dla zdarzeń konfiguracji bramy, podstawowych zmian i zdarzeń konserwacji |
|TunnelDiagnosticLog | Zawiera zdarzenia zmiany stanu tunelu. Zdarzenia łączenia/rozłączania tunelu zawierają podsumowanie przyczyny zmiany stanu, jeśli ma to zastosowanie |
|RouteDiagnosticLog | Rejestruje zmiany tras statycznych i zdarzeń BGP występujących w bramie |
|IKEDiagnosticLog | Rejestruje komunikaty i zdarzenia kontroli IKE na bramie |
|P2SDiagnosticLog | Rejestruje komunikaty i zdarzenia kontroli punkt-lokacja w bramie. Informacje o źródle połączenia są dostępne tylko dla połączeń IKEv2 i OpenVPN |

## <a name="set-up-alerts-in-the-azure-portal"></a><a name="setup"></a>Konfigurowanie alertów w Azure Portal

W poniższym przykładzie przedstawiono tworzenie alertu dotyczącego zdarzenia rozłączenia obejmującego tunel VPN typu lokacja-lokacja:


1. W Azure Portal Wyszukaj **log Analytics** w obszarze **wszystkie usługi** i wybierz pozycję **log Analytics obszary robocze**.

   ![Wybory do przechodzenia do Log Analytics obszarów roboczych](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert0.png "Utwórz")

2. Wybierz pozycję **Utwórz** na stronie **log Analytics** .

   ![Strona Log Analyticsa z przyciskiem tworzenia](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert1.png  "Wybierz pozycję")

3. Wybierz pozycję **Utwórz nową** i wypełnij szczegóły.

   ![Szczegóły dotyczące tworzenia obszaru roboczego Log Analytics](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert2.png  "Wybierz pozycję")

4. Znajdź bramę sieci VPN w bloku **monitorowanie**  >  **ustawień diagnostycznych** .

   ![Opcje znajdowania bramy sieci VPN w ustawieniach diagnostycznych](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert3.png  "Wybierz pozycję")

5. Aby włączyć diagnostykę, kliknij dwukrotnie bramę, a następnie wybierz pozycję **Włącz diagnostykę**.

   ![Opcje włączania diagnostyki](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert4.png  "Wybierz pozycję")

6. Wypełnij szczegóły i upewnij się, że wybrano opcję **Wyślij do log Analytics** i **TunnelDiagnosticLog** . Wybierz obszar roboczy Log Analytics, który został utworzony w kroku 3.

   ![Zaznaczone pola wyboru](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert5.png  "Wybierz pozycję")

   > [!NOTE]
   > Początkowe wyświetlanie danych może potrwać kilka godzin.

7. Przejdź do omówienia zasobu bramy sieci wirtualnej i wybierz pozycję **alerty** na karcie **monitorowanie** . Następnie utwórz nową regułę alertu lub edytuj istniejącą regułę alertu.

   ![Wybory dotyczące tworzenia nowej reguły alertu](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Wybierz pozycję")

   ![punkt-lokacja](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert6.png  "Wybierz pozycję")
8. Wybierz obszar roboczy Log Analytics i zasób.

   ![Wybory dla obszaru roboczego i zasobu](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert7.png  "Wybierz pozycję")

9. Wybierz **niestandardowe przeszukiwanie dzienników** jako logikę sygnału w obszarze **Dodawanie warunku**.

   ![Wybory dla niestandardowego przeszukiwania dzienników](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert8.png  "Wybierz pozycję")

10. Wprowadź następujące zapytanie w polu tekstowym **zapytanie wyszukiwania** . Zastąp wartości w <> i TimeGenerated, zgodnie z potrzebami.

    ```
    AzureDiagnostics
    | where Category == "TunnelDiagnosticLog"
    | where _ResourceId == tolower("<RESOURCEID OF GATEWAY>")
    | where TimeGenerated > ago(5m) 
    | where remoteIP_s == "<REMOTE IP OF TUNNEL>"
    | where status_s == "Disconnected"
    | project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId 
    | sort by TimeGenerated asc
    ```

    Ustaw wartość progową na 0 i wybierz pozycję **gotowe**.

    ![Wprowadzanie zapytania i wybieranie progu](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert9.png  "Wybierz pozycję")

11. Na stronie **Tworzenie reguły** wybierz pozycję **Utwórz nowy** w sekcji **grupy akcji** . Wypełnij szczegóły i wybierz **przycisk OK**.

    ![Szczegóły nowej grupy akcji](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert10.png  "Wybierz pozycję")

12. Na stronie **Tworzenie reguły** Wypełnij pola szczegóły **akcji dostosowywania** i upewnij się, że poprawna nazwa jest wyświetlana w sekcji **Nazwa grupy akcji** . Wybierz pozycję **Utwórz regułę alertu** , aby utworzyć regułę.

    ![Wybory dla tworzenia reguły](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-log/log-alert11.png  "Wybierz pozycję")

## <a name="set-up-alerts-by-using-powershell"></a><a name="setuppowershell"></a>Konfigurowanie alertów przy użyciu programu PowerShell

W poniższym przykładzie przedstawiono procedurę tworzenia alertu dotyczącego zdarzenia rozłączenia obejmującego tunel VPN typu lokacja-lokacja.

1. Utwórz obszar roboczy Log Analytics:

   ```powershell
   $Location           = 'westus2'
   $ResourceGroupName  = 'TestRG1'
   $Sku                = 'pergb2018'
   $WorkspaceName      = 'LogAnalyticsWS123'

   New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku $Sku -ResourceGroupName $ResourceGroupName
   ```

2. Włącz diagnostykę dla bramy sieci VPN:

   ```powershell
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   Set-AzDiagnosticSetting `
       -Name 'VPN tunnel' `
       -ResourceId $VpnGateway.Id `
       -WorkspaceId $Workspace.ResourceId `
       -Enabled $true `
       -Category 'TunnelDiagnosticLog'
   ```

3. Utwórz grupę akcji.

   Ten kod tworzy grupę akcji, która wysyła wiadomość e-mail z powiadomieniem po wyzwoleniu alertu:

   ```powershell
   $ActionGroupName            = 'EmailAdmins'   # Max. 60 characters long
   $ActionGroupShortName       = 'EmailAdmins'   # Max. 12 characters long
   $ActionGroupReceiverName    = 'My receiver Name'
   $EmailAddress               = 'xyz@contoso.com'
   $ResourceGroupName          = 'TestRG1'

   $ActionGroupReceiver = New-AzActionGroupReceiver -Name $ActionGroupReceiverName -UseCommonAlertSchema -EmailReceiver -EmailAddress $EmailAddress

   Set-AzActionGroup `
      -ResourceGroupName $ResourceGroupName `
      -Name $ActionGroupName `
      -ShortName $ActionGroupShortName `
      -Receiver @($ActionGroupReceiver)
   ```

4. Utwórz regułę alertu opartą na niestandardowym przeszukiwaniu dzienników:

   ```powershell
   $ActionGroupName    = 'EmailAdmins'
   $EmailSubject       = 'Redmond VPN tunnel is disconnected'
   $Location           = 'westus2'
   $RemoteIp           = '104.42.209.46'
   $ResourceGroupName  = 'TestRG1'
   $VpnGatewayName     = 'VNet1GW'
   $WorkspaceName      = 'LogAnalyticsWS123'

   $VpnGateway         = Get-AzVirtualNetworkGateway -Name $VpnGatewayName -ResourceGroupName $ResourceGroupName
   $Workspace          = Get-AzOperationalInsightsWorkspace -Name $WorkspaceName -ResourceGroupName $ResourceGroupName

   $Query = @"
   AzureDiagnostics |
   where Category == "TunnelDiagnosticLog" |
   where TimeGenerated > ago(5m) |
   where _ResourceId == tolower("$($VpnGateway.id)") |
   where remoteIP_s == "$($RemoteIp)" |
   where status_s == "Disconnected" |
   project TimeGenerated, OperationName, instance_s, Resource, ResourceGroup, _ResourceId |
   sort by TimeGenerated asc
   "@

   $Source             = New-AzScheduledQueryRuleSource -Query $Query -DataSourceId $Workspace.ResourceId
   $Schedule           = New-AzScheduledQueryRuleSchedule -FrequencyInMinutes 5 -TimeWindowInMinutes 5
   $TriggerCondition   = New-AzScheduledQueryRuleTriggerCondition -ThresholdOperator 'GreaterThan' -Threshold 0

   $ActionGroup        = Get-AzActionGroup -ResourceGroupName $ResourceGroupName -Name $ActionGroupName
   $AznsActionGroup    = New-AzScheduledQueryRuleAznsActionGroup -ActionGroup $ActionGroup.Id -EmailSubject $EmailSubject
   $AlertingAction     = New-AzScheduledQueryRuleAlertingAction -AznsAction $AznsActionGroup -Severity '1' -Trigger $TriggerCondition

   New-AzScheduledQueryRule `
       -ResourceGroupName $ResourceGroupName `
       -Location $Location `
       -Action $AlertingAction `
       -Enabled $true `
       -Description 'The tunnel between Azure and Redmond with IP address 104.42.209.46 is disconnected' `
       -Schedule $Schedule `
       -Source $Source `
       -Name 'The Azure to Redmond tunnel is disconnected'
   ```

## <a name="next-steps"></a>Następne kroki

Aby skonfigurować alerty dotyczące metryk tuneli, zobacz [Konfigurowanie alertów na VPN Gateway metryki](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md).
