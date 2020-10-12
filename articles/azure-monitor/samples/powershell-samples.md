---
title: Przykłady programu Azure Monitor PowerShell
description: Użyj programu PowerShell, aby uzyskiwać dostęp do funkcji Azure Monitor, takich jak automatyczne skalowanie, alerty, elementy webhook i wyszukiwanie dzienników aktywności.
ms.subservice: ''
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 2/14/2018
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 74211df6f925aaa09a4c87a518056e8ef3206b87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89078405"
---
# <a name="azure-monitor-powershell-samples"></a>Przykłady programu Azure Monitor PowerShell
W tym artykule przedstawiono przykładowe polecenia programu PowerShell ułatwiające dostęp do funkcji Azure Monitor.

> [!NOTE]
> Azure Monitor to nowa nazwa o nazwie "Azure Insights" do września 25 2016. Jednak przestrzenie nazw i w ten sposób następujące polecenia nadal zawierają *informacje*o wyrazach.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="set-up-powershell"></a>Konfigurowanie programu PowerShell
Jeśli jeszcze tego nie zrobiono, skonfiguruj program PowerShell do uruchamiania na komputerze. Aby uzyskać więcej informacji, zobacz [jak zainstalować i skonfigurować program PowerShell](/powershell/azure/).

## <a name="examples-in-this-article"></a>Przykłady w tym artykule
Przykłady w tym artykule ilustrują, jak można użyć poleceń cmdlet Azure Monitor. Możesz również przejrzeć całą listę Azure Monitor poleceń cmdlet programu PowerShell w [Azure monitor polecenia cmdlet (Insights)](/powershell/module/az.applicationinsights).

## <a name="sign-in-and-use-subscriptions"></a>Logowanie i korzystanie z subskrypcji
Najpierw Zaloguj się do subskrypcji platformy Azure.

```powershell
Connect-AzAccount
```

Zobaczysz ekran logowania. Po zalogowaniu się na koncie zostanie wyświetlony TenantID i domyślny identyfikator subskrypcji. Wszystkie polecenia cmdlet platformy Azure działają w kontekście domyślnej subskrypcji. Aby wyświetlić listę subskrypcji, do których masz dostęp, użyj następującego polecenia:

```powershell
Get-AzSubscription
```

Aby wyświetlić kontekst roboczy (z subskrypcją których są uruchamiane polecenia), użyj następującego polecenia:

```powershell
Get-AzContext
```
Aby zmienić kontekst roboczy na inną subskrypcję, użyj następującego polecenia:

```powershell
Set-AzContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-activity-log"></a>Pobierz dziennik aktywności
Użyj polecenia cmdlet [Get-AzLog](/powershell/module/az.monitor/get-azlog) .  Poniżej przedstawiono kilka typowych przykładów. Dziennik aktywności obejmuje ostatnie 90 dni operacji. Użycie dat przed tym terminem spowoduje wyświetlenie komunikatu o błędzie.  

Sprawdź bieżącą datę/godzinę, aby sprawdzić, jakich godzin użyć w poniższych poleceniach:
```powershell
Get-Date
```

Pobierz wpisy dziennika od tej godziny/daty do prezentacji:

```powershell
Get-AzLog -StartTime 2019-03-01T10:30
```

Pobierz wpisy dziennika między zakresem czasu i daty:

```powershell
Get-AzLog -StartTime 2019-01-01T10:30 -EndTime 2015-01-01T11:30
```

Pobierz wpisy dziennika z określonej grupy zasobów:

```powershell
Get-AzLog -ResourceGroup 'myrg1'
```

Pobierz wpisy dziennika od określonego dostawcy zasobów między zakresem czasu i daty:

```powershell
Get-AzLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Pobierz wszystkie wpisy dziennika z określonym obiektem wywołującym:

```powershell
Get-AzLog -Caller 'myname@company.com'
```

Następujące polecenie pobiera ostatnie 1000 zdarzeń z dziennika aktywności:

```powershell
Get-AzLog -MaxRecord 10
```

`Get-AzLog` obsługuje wiele innych parametrów. Więcej informacji można znaleźć w `Get-AzLog` dokumentacji.

> [!NOTE]
> `Get-AzLog` zawiera tylko 15 dni historii. Użycie parametru **-MaxRecords** umożliwia wykonywanie zapytań dotyczących ostatnich N zdarzeń, po upływie 15 dni. Aby uzyskać dostęp do zdarzeń starszych niż 15 dni, użyj interfejsu API REST lub zestawu SDK (przykładowego w języku C# przy użyciu zestawu SDK). Jeśli nie dołączysz wartości **StartTime**, wartość domyślna to **Endtime** minus jedna godzina. Jeśli nie dołączysz **Endtime**, wartość domyślna to bieżąca godzina. Wszystkie czasy są w formacie UTC.
> 
> 

## <a name="retrieve-alerts-history"></a>Pobierz historię alertów
Aby wyświetlić wszystkie zdarzenia alertów, można wykonać zapytanie dotyczące dzienników Azure Resource Manager przy użyciu poniższych przykładów.

```powershell
Get-AzLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Aby wyświetlić historię określonej reguły alertu, można użyć `Get-AzAlertHistory` polecenia cmdlet, przekazując identyfikator zasobu reguły alertu.

```powershell
Get-AzAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

`Get-AzAlertHistory`Polecenie cmdlet obsługuje różne parametry. Więcej informacji można znaleźć w temacie [Get-AlertHistory](/previous-versions/azure/mt282453(v=azure.100)).

## <a name="retrieve-information-on-alert-rules"></a>Pobierz informacje o regułach alertów
Wszystkie poniższe polecenia działają w grupie zasobów o nazwie "montest".

Wyświetl wszystkie właściwości reguły alertu:

```powershell
Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Pobierz wszystkie alerty w grupie zasobów:

```powershell
Get-AzAlertRule -ResourceGroup montest
```

Pobieranie wszystkich reguł alertów ustawionych dla zasobu docelowego. Na przykład wszystkie reguły alertów ustawione na maszynie wirtualnej.

```powershell
Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzAlertRule` obsługuje inne parametry. Aby uzyskać więcej informacji, zobacz [Get-AlertRule](/previous-versions/azure/mt282459(v=azure.100)) .

## <a name="create-metric-alerts"></a>Tworzenie alertów dotyczących metryk
Za pomocą `Add-AlertRule` polecenia cmdlet można tworzyć, aktualizować lub wyłączać regułę alertu.

Możesz tworzyć właściwości poczty e-mail i elementu webhook  `New-AzAlertRuleEmail` odpowiednio przy użyciu i `New-AzAlertRuleWebhook` . W oknie polecenia cmdlet reguły alertu Przypisz te właściwości jako akcje do właściwości **Actions** reguły alertu.

W poniższej tabeli opisano parametry i wartości używane do tworzenia alertu przy użyciu metryki.

| parametr | value |
| --- | --- |
| Nazwa |simpletestdiskwrite |
| Lokalizacja tej reguły alertu |East US |
| ResourceGroup |montest |
| Element targetresourceid |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| Wartość metryki alertu, który został utworzony |\PhysicalDisk (_Total) \Bajty zapisy/s. `Get-MetricDefinitions` Aby uzyskać dokładne nazwy metryk, zobacz polecenie cmdlet |
| operator |GreaterThan |
| Wartość progowa (licznik/s w dla tej metryki) |1 |
| WindowSize (hh: mm: SS) |00:05:00 |
| agregator (Statystyka metryki, która używa w tym przypadku średniej liczby) |Średnia |
| niestandardowe wiadomości e-mail (Tablica ciągów) |'foo@example.com','bar@example.com' |
| Wyślij wiadomość e-mail do właścicieli, współautorów i czytelników |-SendToServiceOwners |

Utwórz akcję poczty E-mail

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail myname@company.com
```

Utwórz akcję elementu webhook

```powershell
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Tworzenie reguły alertu na CPU% metryki na klasycznej maszynie wirtualnej

```powershell
Add-AzMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Action $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Pobieranie reguły alertu

```powershell
Get-AzAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

Polecenie cmdlet Add alertu aktualizuje także regułę, jeśli istnieje już reguła alertu dla danej właściwości. Aby wyłączyć regułę alertu, należy uwzględnić parametr **-DisableRule**.

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Pobierz listę dostępnych metryk dla alertów
Za pomocą `Get-AzMetricDefinition` polecenia cmdlet można wyświetlić listę wszystkich metryk dla określonego zasobu.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id>
```

Poniższy przykład generuje tabelę z nazwą metryki i jednostką dla niej.

```powershell
Get-AzMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Pełna lista dostępnych opcji dla programu `Get-AzMetricDefinition` jest dostępna w witrynie [Get-MetricDefinitions](/previous-versions/azure/mt282458(v=azure.100)).

## <a name="create-and-manage-activity-log-alerts"></a>Tworzenie alertów dziennika aktywności i zarządzanie nimi
Za pomocą `Set-AzActivityLogAlert` polecenia cmdlet można ustawić alert dziennika aktywności. Alert dziennika aktywności wymaga, aby najpierw zdefiniować warunki jako słownik warunków, a następnie utworzyć alert, który używa tych warunków.

```powershell

$condition1 = New-AzActivityLogAlertCondition -Field 'category' -Equal 'Administrative'
$condition2 = New-AzActivityLogAlertCondition -Field 'operationName' -Equal 'Microsoft.Compute/virtualMachines/write'
$additionalWebhookProperties = New-Object "System.Collections.Generic.Dictionary``2[System.String,System.String]"
$additionalWebhookProperties.Add('customProperty', 'someValue')
$actionGrp1 = New-AzActionGroup -ActionGroupId '/subscriptions/<subid>/providers/Microsoft.Insights/actiongr1' -WebhookProperty $additionalWebhookProperties
Set-AzActivityLogAlert -Location 'Global' -Name 'alert on VM create' -ResourceGroupName 'myResourceGroup' -Scope '/subscriptions/<subid>' -Action $actionGrp1 -Condition $condition1, $condition2

```

Dodatkowe właściwości elementu webhook są opcjonalne. Możesz odzyskać zawartość alertu dziennika aktywności przy użyciu `Get-AzActivityLogAlert` .

## <a name="create-and-manage-autoscale-settings"></a>Tworzenie ustawień automatycznego skalowania i zarządzanie nimi

> [!NOTE] 
> W przypadku Cloud Services (Microsoft. ClassicCompute) Skalowanie automatyczne obsługuje ziarno czasu 5 minut (PT5M). W przypadku innych usług Skalowanie automatyczne obsługuje ziarno czasu o wartości co najmniej 1 minuty (PT1M)

Dla zasobu (aplikacji sieci Web, maszyny wirtualnej, usługi w chmurze lub zestawu skalowania maszyn wirtualnych) można skonfigurować tylko jedno ustawienie skalowania automatycznego.
Jednak każde ustawienie automatycznego skalowania może mieć wiele profilów. Na przykład jeden dla profilu skalowania opartego na wydajności i drugi dla profilu opartego na harmonogramie. Dla każdego profilu można skonfigurować wiele reguł. Aby uzyskać więcej informacji na temat automatycznego skalowania, zobacz [jak automatycznie skalować aplikację](../../cloud-services/cloud-services-how-to-scale-portal.md).

Poniżej przedstawiono kroki, które należy wykonać:

1. Utwórz reguły.
2. Utwórz profil (y), które mapują reguły, które zostały wcześniej utworzone w profilach.
3. Opcjonalne: Utwórz powiadomienia dla skalowania automatycznego przez skonfigurowanie elementu webhook i właściwości wiadomości e-mail.
4. Utwórz ustawienie automatycznego skalowania z nazwą w zasobie docelowym, mapując profile i powiadomienia utworzone w poprzednich krokach.

W poniższych przykładach pokazano, jak utworzyć ustawienia automatycznego skalowania dla zestawu skalowania maszyn wirtualnych dla systemu operacyjnego Windows w oparciu o użycie metryki użycia procesora CPU.

Najpierw utwórz regułę umożliwiającą skalowanie w poziomie przy zwiększeniu liczby wystąpień.

```powershell
$rule1 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 60 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionValue 1
```        

Następnie utwórz regułę skalowania w górę i zmniejsz liczbę wystąpień.

```powershell
$rule2 = New-AzAutoscaleRule -MetricName "Percentage CPU" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 30 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionValue 1
```

Następnie utwórz profil dla reguł.

```powershell
$profile1 = New-AzAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Utwórz właściwość elementu webhook.

```powershell
$webhook_scale = New-AzAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Utwórz Właściwość powiadomienia dla ustawienia automatycznego skalowania, w tym wiadomości e-mail i element webhook, który został utworzony wcześniej.

```powershell
$notification1= New-AzAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Na koniec Utwórz ustawienie skalowania automatycznego, aby dodać utworzony wcześniej profil. 

```powershell
Add-AzAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Aby uzyskać więcej informacji na temat zarządzania ustawieniami automatycznego skalowania, zobacz [Get-AutoscaleSetting](/previous-versions/azure/mt282461(v=azure.100)).

## <a name="autoscale-history"></a>Historia automatycznego skalowania
Poniższy przykład pokazuje, jak można wyświetlić ostatnie zdarzenia automatycznego skalowania i alertów. Użyj przeszukiwania dzienników aktywności, aby wyświetlić historię skalowania automatycznego.

```powershell
Get-AzLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Za pomocą `Get-AzAutoScaleHistory` polecenia cmdlet można pobrać historię skalowania automatycznego.

```powershell
Get-AzAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Aby uzyskać więcej informacji, zobacz [Get-AutoscaleHistory](/previous-versions/azure/mt282464(v=azure.100)).

### <a name="view-details-for-an-autoscale-setting"></a>Wyświetl szczegóły ustawienia automatycznego skalowania
Za pomocą `Get-Autoscalesetting` polecenia cmdlet można pobrać więcej informacji na temat ustawienia skalowania automatycznego.

W poniższym przykładzie przedstawiono szczegółowe informacje o wszystkich ustawieniach automatycznego skalowania w grupie zasobów "myrg1".

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

W poniższym przykładzie przedstawiono szczegółowe informacje na temat wszystkich ustawień automatycznego skalowania w grupie zasobów "myrg1", a w tym ustawienia automatycznego skalowania o nazwie "MyScaleVMSSSetting".

```powershell
Get-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Usuń ustawienie automatycznego skalowania
`Remove-Autoscalesetting`Aby usunąć ustawienie automatycznego skalowania, można użyć polecenia cmdlet.

```powershell
Remove-AzAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Zarządzanie profilami dzienników dla dziennika aktywności
Możesz utworzyć *profil dziennika* i wyeksportować dane z dziennika aktywności do konta magazynu, aby można było skonfigurować dla niego przechowywanie danych. Opcjonalnie można również przesłać strumieniowo dane do centrum zdarzeń. Ta funkcja jest obecnie dostępna w wersji zapoznawczej i można utworzyć tylko jeden profil dziennika na subskrypcję. Do tworzenia profilów dzienników i zarządzania nimi można użyć następujących poleceń cmdlet z bieżącą subskrypcją. Możesz również wybrać określoną subskrypcję. Mimo że program PowerShell domyślnie jest bieżącą subskrypcją, można zawsze zmienić ten program za pomocą polecenia `Set-AzContext` . Dziennik aktywności można skonfigurować w taki sposób, aby dane były kierowane do dowolnego konta magazynu lub centrum zdarzeń w ramach danej subskrypcji. Dane są zapisywane jako pliki obiektów BLOB w formacie JSON.

### <a name="get-a-log-profile"></a>Pobierz profil dziennika
Aby pobrać istniejące profile dziennika, użyj `Get-AzLogProfile` polecenia cmdlet.

### <a name="add-a-log-profile-without-data-retention"></a>Dodawanie profilu dziennika bez przechowywania danych
```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Usuwanie profilu dziennika
```powershell
Remove-AzLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Dodawanie profilu dziennika z przechowywaniem danych
Można określić właściwość **-RetentionInDays** o liczbie dni jako dodatnią liczbę całkowitą, w której dane są zachowywane.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Dodawanie profilu dziennika z przechowywaniem i EventHub
Oprócz przesyłania danych do konta magazynu można również przesłać strumieniowo do centrum zdarzeń. W tej wersji zapoznawczej Konfiguracja konta magazynu jest obowiązkowa, ale konfiguracja centrum zdarzeń jest opcjonalna.

```powershell
Add-AzLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Location global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Konfigurowanie dzienników diagnostycznych
Wiele usług platformy Azure oferuje dodatkowe dzienniki i dane telemetryczne, które mogą wykonać jedną lub więcej z następujących czynności: 
 - skonfigurować do zapisywania danych na koncie usługi Azure Storage
 - wysłane do Event Hubs
 - wysłano do obszaru roboczego Log Analytics. 

Operację można wykonać tylko na poziomie zasobu. Konto magazynu lub centrum zdarzeń powinno znajdować się w tym samym regionie co zasób docelowy, w którym skonfigurowano ustawienie diagnostyki.

### <a name="get-diagnostic-setting"></a>Pobierz ustawienie diagnostyczne
```powershell
Get-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Wyłącz ustawienie diagnostyczne

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Włącz ustawienie diagnostyczne bez przechowywania

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Włącz ustawienie diagnostyczne z przechowywaniem

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Włącz ustawienie diagnostyczne z przechowywaniem dla określonej kategorii dzienników

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Włącz ustawienie diagnostyczne dla Event Hubs

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Enable $true
```

Włącz ustawienie diagnostyczne dla Log Analytics

```powershell
Set-AzDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -WorkspaceId /subscriptions/s1/resourceGroups/insights-integration/providers/providers/microsoft.operationalinsights/workspaces/myWorkspace -Enabled $true

```

Należy pamiętać, że właściwość identyfikator obszaru roboczego Pobiera *Identyfikator zasobu* obszaru roboczego. Identyfikator zasobu obszaru roboczego Log Analytics można uzyskać przy użyciu następującego polecenia:

```powershell
(Get-AzOperationalInsightsWorkspace).ResourceId

```

Te polecenia można łączyć w celu wysyłania danych do wielu miejsc docelowych.
