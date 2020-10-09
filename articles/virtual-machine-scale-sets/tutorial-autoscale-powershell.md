---
title: Samouczek — Skalowanie automatyczne zestawu skalowania przy użyciu Azure PowerShell
description: Dowiedz się, jak za pomocą programu Azure PowerShell automatycznie skalować zestaw skalowania maszyn wirtualnych w odpowiedzi na wzrosty i spadki zapotrzebowania na procesor CPU
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 03/27/2018
ms.reviewer: avverma
ms.custom: avverma, devx-track-azurepowershell
ms.openlocfilehash: 8ee124f866a5241620671ff84c24f3713f62efe1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89078473"
---
# <a name="tutorial-automatically-scale-a-virtual-machine-scale-set-with-azure-powershell"></a>Samouczek: skalowanie automatyczne zestawu skalowania maszyn wirtualnych przy użyciu programu Azure PowerShell

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

Podczas tworzenia zestawu skalowania musisz zdefiniować liczbę wystąpień maszyn wirtualnych, które chcesz uruchamiać. W odpowiedzi na zmieniające się zapotrzebowanie aplikacji możesz automatycznie zwiększać lub zmniejszać liczbę wystąpień maszyn wirtualnych. Skalowanie automatyczne pozwala spełniać potrzeby klientów lub reagować na zmiany wydajności aplikacji w całym cyklu jej życia. Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Używanie funkcji skalowania automatycznego z zestawem skalowania
> * Tworzenie reguł skalowania automatycznego i korzystanie z nich
> * Testy obciążeniowe wystąpień maszyn wirtualnych i wyzwalanie reguł skalowania automatycznego
> * Skalowanie automatyczne do wewnątrz po zmniejszeniu zapotrzebowania

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Jest to znany problem, który ma wpływ na moduł programu Azure PowerShell w wersji 6.8.1 lub nowszej, w tym bieżącą wersję programu Azure Cloud Shell. Ten samouczek można uruchamiać tylko przy użyciu modułu programu Azure PowerShell w wersji od 6.0.0 do 6.8.0. Uruchom polecenie `Get-Module -ListAvailable AzureRM`, aby dowiedzieć się, jaka wersja jest używana. Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Connect-AzureRmAccount`, aby utworzyć połączenie z platformą Azure.


## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania
Aby ułatwić tworzenie reguł skalowania automatycznego, zdefiniuj zmienne dla zestawu skalowania. W poniższym przykładzie zdefiniowano zmienne dla zestawu skalowania o nazwie *myScaleSet* w grupie zasobów o nazwie *myResourceGroup* w regionie *East US* (Wschodnie stany USA). Polecenie [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) umożliwia uzyskanie identyfikatora subskrypcji. Jeśli masz wiele subskrypcji skojarzonych z kontem, zwracana jest tylko pierwsza z nich. Dostosuj nazwy i identyfikator subskrypcji:

```azurepowershell-interactive
$mySubscriptionId = (Get-AzureRmSubscription)[0].Id
$myResourceGroup = "myResourceGroup"
$myScaleSet = "myScaleSet"
$myLocation = "East US"
```

Teraz utwórz zestaw skalowania maszyn wirtualnych przy użyciu polecenia [New-AzureRmVmss](/powershell/module/azurerm.compute/new-azurermvmss). Musisz również utworzyć moduł równoważenia obciążenia, który umożliwia kierowanie ruchu do poszczególnych wystąpień maszyn wirtualnych. Moduł równoważenia obciążenia zawiera reguły, które pozwalają kierować ruchem na porcie TCP 80 oraz korzystać z ruchu pulpitu zdalnego na porcie TCP 3389 i komunikacji zdalnej programu PowerShell na porcie TCP 5985. Po wyświetleniu monitu podaj własne odpowiednie poświadczenia administracyjne dla wystąpień maszyn wirtualnych w zestawie skalowania:

```azurepowershell-interactive
New-AzureRmVmss `
  -ResourceGroupName $myResourceGroup `
  -VMScaleSetName $myScaleSet `
  -Location $myLocation `
  -VirtualNetworkName "myVnet" `
  -SubnetName "mySubnet" `
  -PublicIpAddressName "myPublicIPAddress" `
  -LoadBalancerName "myLoadBalancer"
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych trwa kilka minut.

## <a name="create-a-rule-to-autoscale-out"></a>Tworzenie reguły skalowania automatycznego w poziomie
Wraz ze wzrostem zapotrzebowania aplikacji zwiększa się obciążenie wystąpień maszyn wirtualnych w zestawie skalowania. Jeśli wzrost obciążenia ma cechy stałego trendu, można skonfigurować reguły skalowania automatycznego umożliwiające zwiększenie liczby wystąpień maszyn wirtualnych w zestawie skalowania. Gdy aplikacje zostaną wdrożone, zestaw skalowania rozpoczyna kierowanie ruchu do nowo utworzonych wystąpień maszyn wirtualnych za pośrednictwem modułu równoważenia obciążenia. Możesz określić, jakie metryki mają być monitorowane, na przykład procesor CPU lub dysk, oraz jak długo obciążenie aplikacji musi przekraczać wartość progową, a także ile wystąpień maszyn wirtualnych ma zostać dodanych do zestawu skalowania.

Za pomocą polecenia [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) utworzymy regułę, która zwiększa liczbę wystąpień maszyn wirtualnych w zestawie skalowania, jeśli w okresie pięciu minut średnie obciążenie procesora CPU wzrośnie ponad 70%. Wyzwolenie reguły powoduje zwiększenie liczby wystąpień maszyn wirtualnych o trzy.

W tej regule są używane następujące parametry:

| Parametr               | Objaśnienie                                                                                                         | Wartość          |
|-------------------------|---------------------------------------------------------------------------------------------------------------------|----------------|
| *-Metricname*           | Metryka wydajności, która jest monitorowana i na której są stosowane akcje zestawu skalowania.                                                   | Procentowe użycie procesora CPU |
| *-TimeGrain*            | Częstotliwość zbierania metryk do analizy.                                                                   | 1 minuta       |
| *-MetricStatistic*      | Określa sposób agregacji metryk zebranych do celów analizy.                                                | Średnia        |
| *-TimeWindow*           | Przedział czasu monitorowania, po którym wartość metryki jest porównywana z wartością progową.                                   | 5 min      |
| *-— Operator*             | Operator używany do porównywania danych metryki z wartością progową.                                                     | Większe niż   |
| *-Próg*            | Wartość, która powoduje wyzwolenie akcji przez regułę skalowania automatycznego.                                                      | 70%            |
| *-ScaleActionDirection* | Określa, czy podczas stosowania reguły ma nastąpić skalowanie w górę czy w dół.                                             | Zwiększ       |
| *-ScaleActionScaleType* | Wskazuje, że liczba wystąpień maszyn wirtualnych powinna zostać zmieniona o określoną wartość.                                    | Liczba zmian   |
| *-ScaleActionValue*     | Wartość procentowa liczby wystąpień maszyn wirtualnych, która powinna zostać zmieniona po wyzwoleniu reguły.                                            | 3              |
| *-ScaleActionCooldown*  | Przedział czasu przed ponownym zastosowaniem reguły, który gwarantuje, że akcje skalowania automatycznego zaczną obowiązywać. | 5 min      |

W poniższym przykładzie utworzono obiekt o nazwie *myRuleScaleOut*, który przechowuje tę regułę skalowania w górę. Parametr *-MetricResourceId* używa zmiennych zdefiniowanych wcześniej dla identyfikatora subskrypcji, nazwy grupy zasobów i nazwy zestawu skalowania:

```azurepowershell-interactive
$myRuleScaleOut = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -TimeGrain 00:01:00 `
  -MetricStatistic "Average" `
  -TimeWindow 00:05:00 `
  -Operator "GreaterThan" `
  -Threshold 70 `
  -ScaleActionDirection "Increase" `
  -ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 3 `
  -ScaleActionCooldown 00:05:00
```


## <a name="create-a-rule-to-autoscale-in"></a>Tworzenie reguły skalowania automatycznego w pionie
Wieczorami lub w weekendy zapotrzebowanie aplikacji może być mniejsze. Jeśli spadek obciążenia ma cechy stałego trendu w danym okresie, można skonfigurować reguły skalowania automatycznego umożliwiające zmniejszenie liczby wystąpień maszyn wirtualnych w zestawie skalowania. Akcja skalowania w pionie ogranicza koszt używania zestawu skalowania, ponieważ jest uruchomionych tylko tyle wystąpień, ile jest wymaganych do zaspokojenia bieżącego zapotrzebowania.

Za pomocą polecenia [New-AzureRmAutoscaleRule](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleRule) utwórz inną regułę, która zmniejsza liczbę wystąpień maszyn wirtualnych w zestawie skalowania, jeśli w okresie pięciu minut średnie obciążenie procesora CPU spadnie poniżej 30%. Gdy reguła jest wyzwalana, liczba wystąpień maszyn wirtualnych zostanie zmniejszona o jeden. Poniższy przykład tworzy obiekt o nazwie *myRuleScaleDown* , który przechowuje tę regułę skalowania w dół. Parametr *-MetricResourceId* używa zmiennych zdefiniowanych wcześniej dla identyfikatora subskrypcji, nazwy grupy zasobów i nazwy zestawu skalowania:

```azurepowershell-interactive
$myRuleScaleIn = New-AzureRmAutoscaleRule `
  -MetricName "Percentage CPU" `
  -MetricResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -Operator "LessThan" `
  -MetricStatistic "Average" `
  -Threshold 30 `
  -TimeGrain 00:01:00 `
  -TimeWindow 00:05:00 `
  -ScaleActionCooldown 00:05:00 `
  -ScaleActionDirection "Decrease" `
  -ScaleActionScaleType "ChangeCount" `
  -ScaleActionValue 1
```


## <a name="define-an-autoscale-profile"></a>Definiowanie profilu skalowania automatycznego
Aby skojarzyć reguły skalowania automatycznego z zestawem skalowania, utwórz profil. Profil skalowania automatycznego umożliwia definiowanie domyślnej, minimalnej i maksymalnej pojemności zestawu skalowania oraz pozwala kojarzyć reguły skalowania automatycznego. Utwórz profil skalowania automatycznego za pomocą polecenia [New-AzureRmAutoscaleProfile](/powershell/module/AzureRM.Insights/New-AzureRmAutoscaleProfile). W poniższym przykładzie ustawiono domyślną, minimalną pojemność — *2* — oraz maksymalną pojemność — *10* wystąpień maszyn wirtualnych. Następnie są dołączane reguły skalowania w poziomie i w pionie utworzone w poprzednich krokach:

```azurepowershell-interactive
$myScaleProfile = New-AzureRmAutoscaleProfile `
  -DefaultCapacity 2  `
  -MaximumCapacity 10 `
  -MinimumCapacity 2 `
  -Rule $myRuleScaleOut,$myRuleScaleIn `
  -Name "autoprofile"
```


## <a name="apply-autoscale-profile-to-a-scale-set"></a>Zastosuj profil skalowania automatycznego do zestawu skalowania
Ostatni krok polega na zastosowaniu profilu skalowania automatycznego do zestawu skalowania. Od tej pory zestaw skalowania może automatycznie przeprowadzać skalowanie w pionie lub w poziomie na podstawie zapotrzebowania aplikacji. Zastosuj profil skalowania automatycznego za pomocą polecenia [Add-AzureRmAutoscaleSetting](/powershell/module/AzureRM.Insights/Add-AzureRmAutoscaleSetting):

```azurepowershell-interactive
Add-AzureRmAutoscaleSetting `
  -Location $myLocation `
  -Name "autosetting" `
  -ResourceGroup $myResourceGroup `
  -TargetResourceId /subscriptions/$mySubscriptionId/resourceGroups/$myResourceGroup/providers/Microsoft.Compute/virtualMachineScaleSets/$myScaleSet `
  -AutoscaleProfile $myScaleProfile
```


## <a name="generate-cpu-load-on-scale-set"></a>Generowanie obciążenia procesora CPU w zestawie skalowania
Przetestowanie reguł skalowania automatycznego wymaga wygenerowania obciążenia procesora CPU na wystąpieniach maszyn wirtualnych w zestawie skalowania. Symulowane obciążenie procesora CPU powoduje uruchomienie skalowania w poziomie przez reguły skalowania automatycznego, a w efekcie zwiększenie liczby wystąpień maszyn wirtualnych. Gdy symulowane obciążenie procesora CPU zmniejszy się, nastąpi automatyczne skalowanie w pionie i ograniczenie liczby wystąpień maszyn wirtualnych.

Aby wyświetlić listę portów NAT umożliwiających połączenie z wystąpieniami maszyn wirtualnych w zestawie skalowania, najpierw pobierz obiekt modułu równoważenia obciążenia za pomocą polecenia [Get-AzureRmLoadBalancer](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancer). Następnie Wyświetl reguły NAT dla ruchu przychodzącego za pomocą [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig):

```azurepowershell-interactive
# Get the load balancer object
$lb = Get-AzureRmLoadBalancer -ResourceGroupName "myResourceGroup" -Name "myLoadBalancer"

# View the list of inbound NAT rules
Get-AzureRmLoadBalancerInboundNatRuleConfig -LoadBalancer $lb | Select-Object Name,Protocol,FrontEndPort,BackEndPort
```

Następujące przykładowe dane wyjściowe zawierają nazwę wystąpienia, publiczny adres IP modułu równoważenia obciążenia oraz numer portu, do którego reguły NAT kierują ruch:

```powershell
Name        Protocol FrontendPort BackendPort
----        -------- ------------ -----------
myRDPRule.0 Tcp             50001        3389
myRDPRule.1 Tcp             50002        3389
```

Nazwa reguły w kolumnie *Name* jest zgodna z nazwą wystąpienia maszyny wirtualnej wyświetloną w poprzednim poleceniu [Get-AzureRmVmssVM](/powershell/module/azurerm.compute/get-azurermvmssvm). Na przykład aby nawiązać połączenie z wystąpieniem maszyny wirtualnej *0*, użyj nazwy *myRDPRule.0* i portu *50001*. Aby nawiązać połączenie z wystąpieniem maszyny wirtualnej *1*, użyj wartości *myRDPRule.1* i portu *50002*.

Publiczny adres IP modułu równoważenia obciążenia możesz wyświetlić za pomocą polecenia [Get-AzureRmPublicIpAddress](/powershell/module/AzureRM.Network/Get-AzureRmPublicIpAddress):

```azurepowershell-interactive
Get-AzureRmPublicIpAddress -ResourceGroupName "myResourceGroup" -Name myPublicIP | Select IpAddress
```

Przykładowe dane wyjściowe:

```powershell
IpAddress
---------
52.168.121.216
```

Utwórz połączenie zdalne z pierwszym wystąpieniem maszyny wirtualnej. Podaj swój publiczny adres IP i numer portu wymaganego wystąpienia maszyny wirtualnej, jak pokazano w poprzednich poleceniach. Po wyświetleniu monitu wprowadź poświadczenia używane podczas tworzenia zestawu skalowania (domyślnie w przykładowych poleceniach są *azureuser* i *P \@ ssw0rd!*). Jeśli korzystasz z usługi Azure Cloud Shell, wykonaj ten krok przy użyciu wiersza polecenia lokalnego programu PowerShell lub klienta pulpitu zdalnego. W poniższym przykładzie nawiązano połączenie z wystąpieniem maszyny wirtualnej *0*:

```powershell
mstsc /v 52.168.121.216:50001
```

Po zalogowaniu otwórz program Internet Explorer z paska zadań.

- Wybierz przycisk **OK**, aby zaakceptować monit *Użyj zalecanych ustawień zabezpieczeń, prywatności i zgodności*.
- Wpisz *http://download.sysinternals.com/files/CPUSTRES.zip* na pasku adresu.
- Po włączeniu konfiguracji zwiększonych zabezpieczeń programu Internet Explorer wybierz pozycję **Dodaj**, aby dodać domenę *http://download.sysinternals.com* do listy zaufanych witryn.
- Po wyświetleniu monitu o pobranie pliku wybierz pozycję **Otwórz**, a następnie pozycję **Uruchom**, aby uruchomić narzędzie *CPUSTRES.EXE*.

Aby wygenerować obciążenie procesora CPU, zaznacz pola **Active** (Aktywny) dla dwóch wątków. Z menu rozwijanego **Activity** (Aktywność) dla obu wątków wybierz opcję *Maximum* (Maksymalna). Za pomocą Menedżera zadań możesz upewnić się, że obciążenie procesora CPU na maszynie wirtualnej wynosi 100%.

![Narzędzie CPU Stress generuje obciążenie na wystąpieniu maszyny wirtualnej](media/tutorial-autoscale-powershell/cpu-stress-load.PNG)

Pozostaw otwartą sesję połączenia pulpitu zdalnego, a następnie otwórz kolejną sesję połączenia pulpitu zdalnego. Połącz się z drugim wystąpieniem maszyny wirtualnej za pomocą numeru portu uzyskanego przy użyciu poprzedniego polecenia cmdlet [Get-AzureRmLoadBalancerInboundNatRuleConfig](/powershell/module/AzureRM.Network/Get-AzureRmLoadBalancerInboundNatRuleConfig):

```powershell
mstsc /v 52.168.121.216:50002
```

Po zalogowaniu do drugiego wystąpienia maszyny wirtualnej powtórz poprzednie kroki, aby pobrać i uruchomić narzędzie *CPUSTRES.EXE*. Ponownie zaznacz pole **Active** (Aktywny) dla obu wątków i ustaw ich aktywność na opcję *Maximum* (Maksymalna).

Aby narzędzie**CPU Stress** mogło kontynuować działanie, pozostaw otwarte obie sesje połączenia pulpitu zdalnego.


## <a name="monitor-the-active-autoscale-rules"></a>Monitorowanie aktywnych reguł skalowania automatycznego
Do monitorowania liczby wystąpień maszyn wirtualnych w zestawie skalowania służy pętla **while**. Skalowanie automatyczne skalowanie jest trwa 5 minut, aby rozpocząć proces skalowania w poziomie w odpowiedzi na obciążenie procesora CPU wygenerowane przez **CPUStress** na poszczególnych WYSTĄPIENIACH maszyn wirtualnych:

```azurepowershell-interactive
while (1) {Get-AzureRmVmssVM `
    -ResourceGroupName $myResourceGroup `
    -VMScaleSetName $myScaleSet; sleep 10}
```

Po osiągnięciu progu procesora CPU reguły skalowania automatycznego zwiększają liczbę wystąpień maszyn wirtualnych w zestawie skalowania. Następujące dane wyjściowe zawierają trzy maszyny wirtualne tworzone w zestawie skalowania podczas automatycznego skalowania w poziomie:

```powershell
ResourceGroupName         Name Location          Sku Capacity InstanceID ProvisioningState
-----------------         ---- --------          --- -------- ---------- -----------------
MYRESOURCEGROUP   myScaleSet_2   eastus Standard_DS2                   2         Succeeded
MYRESOURCEGROUP   myScaleSet_3   eastus Standard_DS2                   3         Succeeded
MYRESOURCEGROUP   myScaleSet_4   eastus Standard_DS2                   4          Creating
MYRESOURCEGROUP   myScaleSet_5   eastus Standard_DS2                   5          Creating
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Creating
```

W sesjach połączeń pulpitu zdalnego z poszczególnymi wystąpieniami maszyn wirtualnych zamknij narzędzie **CPU Stress**. Średnie obciążenie procesora CPU w całym zestawie skalowania powróci do typowej wartości. Po kolejnych pięciu minutach reguły skalowania automatycznego inicjują skalowanie w pionie liczby wystąpień maszyn wirtualnych. Najpierw są usuwane wystąpienia maszyn wirtualnych o najwyższych identyfikatorach. Jeśli zestaw skalowania używa zestawów dostępności lub stref dostępności, akcje skalowania w pionie są dystrybuowane równomiernie na te wystąpienia maszyn wirtualnych. Następujące przykładowe dane wyjściowe przedstawiają usuwanie wystąpienia maszyny wirtualnej z zestawu skalowania podczas skalowania w pionie:

```powershell
MYRESOURCEGROUP   myScaleSet_6   eastus Standard_DS2                   6          Deleting
```

Zakończ działanie pętli *while* za pomocą klawiszy `Ctrl-c`. Co pięć minut zestaw skalowania będzie powtarzał operację skalowania w pionie, usuwając po jednym wystąpieniu maszyny wirtualnej aż do osiągnięcia minimalnej liczby wystąpień równej dwa.


## <a name="clean-up-resources"></a>Czyszczenie zasobów
Aby pozbyć się zestawu skalowania i dodatkowych zasobów, usuń grupę zasobów wraz z całą zawartością za pomocą polecenia [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup). Parametr `-Force` potwierdza, że chcesz usunąć zasoby bez wyświetlania dodatkowego monitu. Parametr `-AsJob` zwraca kontrolę do wiersza polecenia bez oczekiwania na zakończenie operacji.

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name "myResourceGroup" -Force -AsJob
```


## <a name="next-steps"></a>Następne kroki
W tym samouczku przedstawiono automatyczne skalowanie zestawu skalowania w pionie lub w poziomie za pomocą programu Azure PowerShell:

> [!div class="checklist"]
> * Używanie funkcji skalowania automatycznego z zestawem skalowania
> * Tworzenie reguł skalowania automatycznego i korzystanie z nich
> * Testy obciążeniowe wystąpień maszyn wirtualnych i wyzwalanie reguł skalowania automatycznego
> * Skalowanie automatyczne do wewnątrz po zmniejszeniu zapotrzebowania
