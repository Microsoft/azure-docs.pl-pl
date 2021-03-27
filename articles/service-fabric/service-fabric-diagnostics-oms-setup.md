---
title: Konfigurowanie monitorowania przy użyciu dzienników Azure Monitor
description: Dowiedz się, jak skonfigurować Azure Monitor dzienników do wizualizacji i analizowania zdarzeń do monitorowania klastrów Service Fabric platformy Azure.
ms.topic: conceptual
ms.date: 02/20/2019
ms.openlocfilehash: 79dbc38f9d177bc3decf11673a68ce434f9a83bc
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105625963"
---
# <a name="set-up-azure-monitor-logs-for-a-cluster"></a>Konfigurowanie dzienników Azure Monitor dla klastra

Dzienniki Azure Monitor to nasze zalecenie do monitorowania zdarzeń na poziomie klastra. Log Analytics obszar roboczy można skonfigurować za pomocą Azure Resource Manager, programu PowerShell lub witryny Azure Marketplace. Jeśli zachowasz zaktualizowany szablon Menedżer zasobów wdrożenia do użytku w przyszłości, użyj tego samego szablonu, aby skonfigurować środowisko dzienników Azure Monitor. Wdrażanie za pośrednictwem portalu Marketplace jest łatwiejsze, jeśli masz już wdrożony klaster z włączoną diagnostyką. Jeśli nie masz dostępu na poziomie subskrypcji na koncie, na które jest wdrażane, wdróż program przy użyciu programu PowerShell lub szablonu Menedżer zasobów.

> [!NOTE]
> Aby skonfigurować Azure Monitor dzienników do monitorowania klastra, musisz mieć włączoną diagnostykę do wyświetlania zdarzeń na poziomie klastra lub na poziomie platformy. Zapoznaj się z artykułem [jak skonfigurować diagnostykę w klastrach systemu Windows](service-fabric-diagnostics-event-aggregation-wad.md) i [jak skonfigurować diagnostykę w klastrach z systemem Linux](service-fabric-diagnostics-oms-syslog.md) , aby uzyskać więcej

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Wdrażanie obszaru roboczego Log Analytics przy użyciu witryny Azure Marketplace

Jeśli chcesz dodać obszar roboczy Log Analytics po wdrożeniu klastra, przejdź do witryny Azure Marketplace w portalu i Wyszukaj pozycję **Service Fabric Analytics**. Jest to rozwiązanie niestandardowe dla wdrożeń Service Fabric, które mają dane specyficzne dla Service Fabric. W tym procesie utworzysz rozwiązanie (pulpit nawigacyjny, aby wyświetlić szczegółowe informacje) i obszar roboczy (agregacja danych klastra bazowego).

1. Wybierz pozycję **Nowy** w menu nawigacji po lewej stronie. 

2. Wyszukaj **Service Fabric Analytics**. Wybierz wyświetlony zasób.

3. Wybierz przycisk **Utwórz**.

    ![Service Fabric Analytics w witrynie Marketplace](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. W oknie Tworzenie Service Fabric Analytics wybierz **obszar roboczy** dla pola **obszaru roboczego pakietu OMS** , a następnie **Utwórz nowy obszar roboczy**. Wypełnij wymagane wpisy. Jedynym wymaganiem jest, aby subskrypcja klastra Service Fabric i obszaru roboczego była taka sama. Po sprawdzeniu poprawności wpisów w obszarze roboczym rozpocznie się wdrażanie. Wdrożenie trwa zaledwie kilka minut.

5. Po zakończeniu wybierz pozycję **Utwórz** ponownie u dołu okna tworzenia Service Fabric Analytics. Upewnij się, że nowy obszar roboczy jest widoczny w obszarze **roboczym pakietu OMS**. Ta akcja dodaje rozwiązanie do utworzonego obszaru roboczego.

W przypadku korzystania z systemu Windows wykonaj następujące kroki, aby połączyć dzienniki Azure Monitor z kontem magazynu, na którym są przechowywane zdarzenia klastra. 

>[!NOTE]
>Rozwiązanie Service Fabric Analytics jest obsługiwane tylko w przypadku klastrów systemu Windows. W przypadku klastrów systemu Linux zapoznaj się z artykułem dotyczącym [konfigurowania dzienników Azure monitor dla klastrów systemu Linux](service-fabric-diagnostics-oms-syslog.md).  

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Łączenie obszaru roboczego Log Analytics z klastrem 

1. Obszar roboczy musi być połączony z danymi diagnostycznymi pochodzącymi z klastra. Przejdź do grupy zasobów, w której utworzono rozwiązanie Service Fabric Analytics. Wybierz pozycję **servicefabric \<nameOfWorkspace\>** i przejdź do jej strony przegląd. W tym miejscu możesz zmienić ustawienia rozwiązania, ustawienia obszaru roboczego i uzyskać dostęp do obszaru roboczego Log Analytics.

2. W menu nawigacji po lewej stronie w obszarze **źródła danych obszaru roboczego** wybierz pozycję **dzienniki kont magazynu**.

3. Na stronie **dzienniki konta magazynu** wybierz pozycję **Dodaj** u góry, aby dodać dzienniki klastra do obszaru roboczego.

4. Wybierz pozycję **konto magazynu** , aby dodać odpowiednie konto utworzone w klastrze. Jeśli została użyta nazwa domyślna, konto magazynu to **sfdg \<resourceGroupName\>**. Można to również potwierdzić przy użyciu szablonu Azure Resource Manager używanego do wdrożenia klastra, sprawdzając wartość używaną dla **applicationDiagnosticsStorageAccountName**. Jeśli nazwa nie zostanie wyświetlona, przewiń w dół i wybierz pozycję **Załaduj więcej**. Wybierz nazwę konta magazynu.

5. Określ typ danych. Ustaw ją na **Service Fabric zdarzenia**.

6. Upewnij się, że źródło jest automatycznie ustawione na **WADServiceFabric \* zdarzenia**.

7. Wybierz **przycisk OK** , aby połączyć obszar roboczy z dziennikami klastra.

    ![Dodawanie dzienników kont magazynu do dzienników Azure Monitor](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

Konto jest teraz wyświetlane jako część dzienników konta magazynu w źródłach danych obszaru roboczego.

Dodano rozwiązanie Service Fabric Analytics w obszarze roboczym Log Analytics, który jest teraz poprawnie połączony z platformą i tabelą dzienników aplikacji klastra. W ten sam sposób można dodać dodatkowe źródła do obszaru roboczego.


## <a name="deploy-azure-monitor-logs-with-azure-resource-manager"></a>Wdrażanie dzienników Azure Monitor przy użyciu Azure Resource Manager

W przypadku wdrażania klastra przy użyciu szablonu Menedżer zasobów szablon tworzy nowy Log Analytics obszar roboczy, dodaje rozwiązanie Service Fabric do obszaru roboczego i konfiguruje go w celu odczytywania danych z odpowiednich tabel magazynu.

Możesz użyć i zmodyfikować [Ten przykładowy szablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) w celu spełnienia wymagań. Ten szablon wykonuje następujące czynności:

* Tworzy klaster z 5 węzłami Service Fabric
* Tworzy obszar roboczy Log Analytics i rozwiązanie Service Fabric
* Konfiguruje agenta Log Analytics do zbierania i wysyłania 2 przykładowych liczników wydajności do obszaru roboczego
* Konfiguruje funkcji wad, aby zbierać Service Fabric i wysyłać je do tabel usługi Azure Storage (WADServiceFabric * Eventing)
* Konfiguruje obszar roboczy Log Analytics do odczytywania zdarzeń z tych tabel


Szablon można wdrożyć jako uaktualnienie Menedżer zasobów do klastra za pomocą `New-AzResourceGroupDeployment` interfejsu API w module Azure PowerShell. Przykładowe polecenie:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

Azure Resource Manager wykryje, że to polecenie jest aktualizacją istniejącego zasobu. Przetwarza tylko zmiany między szablonem prowadzącym do istniejącego wdrożenia i udostępnionym nowym szablonem.

## <a name="deploy-azure-monitor-logs-with-azure-powershell"></a>Wdrażanie dzienników Azure Monitor przy użyciu Azure PowerShell

Możesz również wdrożyć zasób usługi log Analytics za pomocą programu PowerShell przy użyciu `New-AzOperationalInsightsWorkspace` polecenia. Aby użyć tej metody, upewnij się, że zainstalowano [Azure PowerShell](/powershell/azure/install-az-ps). Użyj tego skryptu, aby utworzyć nowy obszar roboczy Log Analytics i dodać do niego rozwiązanie Service Fabric: 

```powershell

$SubID = "<subscription ID>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<Log Analytics workspace name>"
$solution = "ServiceFabric"

# Sign in to Azure and access the correct subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

Gdy skończysz, wykonaj kroki opisane w poprzedniej sekcji, aby połączyć Azure Monitor dzienniki z odpowiednim kontem magazynu.

Możesz również dodawać inne rozwiązania lub wprowadzać inne modyfikacje w obszarze roboczym Log Analytics za pomocą programu PowerShell. Aby dowiedzieć się więcej, zobacz [Zarządzanie dziennikami Azure monitor przy użyciu programu PowerShell](../azure-monitor/logs/powershell-workspace-configuration.md).

## <a name="next-steps"></a>Następne kroki
* [Wdróż agenta log Analytics](service-fabric-diagnostics-oms-agent.md) w węzłach w celu zebrania liczników wydajności i zebrania statystyk i dzienników platformy Docker dla kontenerów
* Zapoznaj się z funkcjami [przeszukiwania dzienników i wykonywania zapytań](../azure-monitor/logs/log-query-overview.md) , które są oferowane w ramach dzienników Azure monitor
* [Używanie projektanta widoków do tworzenia widoków niestandardowych w dziennikach Azure Monitor](../azure-monitor/visualize/view-designer.md)
