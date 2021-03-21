---
title: Skalowanie klastra Service Fabric lub wychodzącego
description: Skalowanie klastra Service Fabric w celu dopasowania do zapotrzebowania przez ustawienie reguł automatycznego skalowania dla każdego typu węzła/zestawu skalowania maszyn wirtualnych. Dodawanie lub usuwanie węzłów do klastra Service Fabric
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 6ee04c73b75d6b335e450ff816c51f0a3089b918
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94409964"
---
# <a name="scale-a-cluster-in-or-out"></a>Skalowanie klastra w poziomie lub w pionie

> [!WARNING]
> Przeczytaj tę sekcję przed skalowaniem

Skalowanie zasobów obliczeniowych do źródła obciążenia wymaga zamierzonego planowania, prawie zawsze trwa dłużej niż godzinę dla środowiska produkcyjnego i wymaga zrozumienia obciążenia i kontekstu biznesowego. Jeśli nie wykonano tego działania wcześniej, zaleca się rozpoczęcie od odczytu i zrozumienie [Service Fabric zagadnień związanych z planowaniem pojemności klastra](service-fabric-cluster-capacity.md)przed kontynuowaniem pozostałej części tego dokumentu. To zalecenie ma na celu uniknięcie niezamierzonych problemów z LiveSite i jest również zalecane pomyślne przetestowanie operacji podejmowanych w środowisku nieprodukcyjnym. W dowolnym momencie możesz [zgłosić problemy z produkcją lub zażądać płatnej pomocy technicznej dla platformy Azure](service-fabric-support.md#report-production-issues-or-request-paid-support-for-azure). W przypadku inżynierów przystosowanych do wykonywania tych operacji z odpowiednim kontekstem w tym artykule opisano operacje skalowania, ale należy zdecydować i zrozumieć, które operacje są odpowiednie dla przypadku użycia; takie jak zasoby do skalowania (procesor CPU, magazyn, pamięć), kierunek skalowania (w pionie lub w poziomie) oraz operacje do wykonania (Template deployment zasobów, portal, PowerShell/CLI).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules-or-manually"></a>Skalowanie klastra Service Fabric w lub na zewnątrz przy użyciu reguł skalowania automatycznego lub ręczne
Zestawy skalowania maszyn wirtualnych to zasób obliczeniowy platformy Azure, który służy do wdrażania kolekcji maszyn wirtualnych jako zestawu i zarządzania nią. Każdy typ węzła zdefiniowany w klastrze Service Fabric jest ustawiany jako oddzielny zestaw skalowania maszyn wirtualnych. Każdy typ węzła można następnie przeskalować lub wyróżnić niezależnie, mieć otwarte różne zestawy portów i może mieć różne metryki pojemności. Więcej informacji na ten temat znajduje się w dokumencie [Service Fabric typów węzłów](service-fabric-cluster-nodetypes.md) . Ponieważ typy węzłów Service Fabric w klastrze składają się z zestawów skalowania maszyn wirtualnych w zapleczu, należy skonfigurować reguły automatycznego skalowania dla każdego typu węzła/zestawu skalowania maszyn wirtualnych.

> [!NOTE]
> Twoja subskrypcja musi mieć wystarczającą liczbę rdzeni, aby można było dodać nowe maszyny wirtualne tworzące ten klaster. Nie ma obecnie weryfikacji modelu, więc w przypadku dowolnych limitów przydziału występuje błąd czasu wdrożenia. Ponadto typ pojedynczego węzła nie może po prostu przekraczać 100 węzłów na VMSS. Może być konieczne dodanie VMSS, aby osiągnąć dodaną skalę skalowania, i automatyczne skalowanie nie może automatycznie dodać VMSS. Dodanie miejscowego VMSS do klastra na żywo jest wyzwaniem i często powoduje, że użytkownicy będą aprowizacji nowe klastry przy użyciu odpowiednich typów węzłów, które są obsługiwane podczas tworzenia. [Zaplanuj odpowiednio pojemność klastra](./service-fabric-cluster-capacity.md) . 
> 
> 

## <a name="choose-the-node-typevirtual-machine-scale-set-to-scale"></a>Wybierz typ węzła/zestaw skalowania maszyn wirtualnych do skalowania
Obecnie nie można określić reguł automatycznego skalowania dla zestawów skalowania maszyn wirtualnych przy użyciu portalu, aby utworzyć klaster Service Fabric, dlatego należy użyć Azure PowerShell (1.0 +), aby wyświetlić listę typów węzłów, a następnie dodać do nich reguły automatycznego skalowania.

Aby uzyskać listę zestawu skalowania maszyn wirtualnych tworzącego klaster, uruchom następujące polecenia cmdlet:

```powershell
Get-AzResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzVmss -ResourceGroupName <RGname> -VMScaleSetName <virtual machine scale set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevirtual-machine-scale-set"></a>Ustawianie reguł automatycznego skalowania dla typu węzła/zestawu skalowania maszyn wirtualnych
Jeśli klaster ma wiele typów węzłów, Powtórz te czynności dla każdego typu węzła/zestawu skalowania maszyn wirtualnych, które mają być skalowane (w lub na zewnątrz). Przed skonfigurowaniem skalowania automatycznego należy wziąć pod uwagę potrzebną liczbę węzłów. Minimalna liczba węzłów wymagana dla typu węzła podstawowego zależy od wybranego poziomu niezawodności. Przeczytaj więcej na temat [poziomów niezawodności](service-fabric-cluster-capacity.md).

> [!NOTE]
> Skalowanie w typie węzła podstawowego do wartości mniejszej niż wartość minimalna spowoduje, że klaster stanie się niestabilny, a nawet nie zostanie wyłączony. Może to spowodować utratę danych aplikacji i usług systemowych.
> 
> 

Obecnie funkcja automatycznego skalowania nie jest obsługiwana przez obciążenia, które mogą być raportowane przez aplikacje do Service Fabric. W tej chwili uzyskiwane automatyczne skalowanie jest wyłącznie sterowane licznikami wydajności, które są emitowane przez poszczególne wystąpienia zestawu skalowania maszyn wirtualnych.  

Postępuj zgodnie [z tymi instrukcjami, aby skonfigurować automatyczne skalowanie dla każdego zestawu skalowania maszyn wirtualnych](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

> [!NOTE]
> W scenariuszu w skali, chyba że typ węzła ma [poziom trwałości][durability] Gold lub Silver, należy wywołać [polecenie cmdlet Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate) z odpowiednią nazwą węzła. W przypadku trwałości Bronze nie zaleca się skalowania w więcej niż jednym węźle jednocześnie.
> 

## <a name="manually-add-vms-to-a-node-typevirtual-machine-scale-set"></a>Ręcznie Dodaj maszyny wirtualne do typu węzła/zestawu skalowania maszyn wirtualnych

Skalowanie w poziomie polega na dodawaniu wystąpień maszyn wirtualnych do zestawu skalowania. Wystąpienia te stają się węzłami używanymi przez usługę Service Fabric. Usługa Service Fabric wykrywa zwiększenie liczby wystąpień (efekt skalowania w poziomie) i reaguje automatycznie. 

> [!NOTE]
> Dodawanie maszyn wirtualnych zabiera trochę czasu, dlatego nie oczekuje się, że dodatki będą chwilowo. Zaplanuj, aby zwiększyć wydajność z wyprzedzeniem, co pozwala przez ponad 10 minut, zanim pojemność maszyny wirtualnej będzie dostępna dla replik/wystąpień usługi, które mają zostać umieszczone.
> 

### <a name="add-vms-using-a-template"></a>Dodawanie maszyn wirtualnych przy użyciu szablonu
Postępuj zgodnie z instrukcjami wyświetlanymi w [galerii szablonów szybkiego startu](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) , aby zmienić liczbę maszyn wirtualnych w każdym typie węzła. 

### <a name="add-vms-using-powershell-or-cli-commands"></a>Dodawanie maszyn wirtualnych przy użyciu programu PowerShell lub poleceń interfejsu wiersza polecenia
Poniższy kod pobiera nazwę zestawu skalowania i zwiększa jego **pojemność** o 1.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity += 1

Update-AzVmss -ResourceGroupName $scaleset.ResourceGroupName -VMScaleSetName $scaleset.Name -VirtualMachineScaleSet $scaleset
```

Pojemność jest ustawiana na 6.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 6
```

## <a name="manually-remove-vms-from-a-node-typevirtual-machine-scale-set"></a>Ręcznie usuń maszyny wirtualne z typu węzła/zestawu skalowania maszyn wirtualnych
Skalowanie w typ węzła powoduje usunięcie wystąpień maszyn wirtualnych z zestawu skalowania. Jeśli typ węzła to poziom trwałości Bronze, Service Fabric jest nieświadomy, co się stało, i raporty, że brakuje węzła. Powoduje to zgłoszenie stanu złej kondycji klastra. Aby uniknąć tego nieprawidłowego stanu, należy jawnie usunąć węzeł z klastra i usunąć stan węzła.

Usługi systemowe usługi Service Fabric działają w typie węzła podstawowego w klastrze. Podczas skalowania w typie węzła podstawowego nigdy nie jest skalowane do liczby wystąpień mniejszych niż to, co gwarantuje [warstwa niezawodności](service-fabric-cluster-capacity.md) . 
 
W przypadku usługi stanowej potrzebna jest pewna liczba węzłów, aby zawsze zachować dostępność i zachować stan usługi. Co najmniej minimalnie potrzebna jest liczba węzłów równa liczbie docelowych zestawu replik partycji/usługi.

### <a name="remove-the-service-fabric-node"></a>Usuwanie węzła usługi Service Fabric

Procedura ręcznego usuwania stanu węzła ma zastosowanie tylko do typów węzłów z warstwą trwałości *Bronze* .  W przypadku warstwy trwałości *Silver* i *Gold* te kroki są wykonywane automatycznie przez platformę. Aby uzyskać więcej informacji o trwałości, zobacz [Planowanie pojemności klastra usługi Service Fabric][durability].

>[!NOTE]
> Należy zachować minimalną liczbę pięciu węzłów dla dowolnego zestawu skalowania maszyn wirtualnych z włączonym poziomem trwałości Gold lub Silver. W przypadku skalowania poniżej tego progu klaster przejdzie w stan błędu i konieczne będzie ręczne oczyszczenie usuniętych węzłów.

Aby zachować równe rozłożenie węzłów klastra w domenach uaktualniania i błędów, a tym samym umożliwić ich równomierne wykorzystywanie, najpierw należy usunąć ostatnio utworzony węzeł. Innymi słowy węzły należy usuwać w kolejności odwrotnej niż były tworzone. Ostatnio utworzony węzeł to ten, który ma największą wartość właściwości `virtual machine scale set InstanceId`. Poniższe przykłady kodu zwracają ostatnio utworzony węzeł.

```powershell
Get-ServiceFabricNode | Sort-Object NodeInstanceId -Descending | Select-Object -First 1
```

```shell
sfctl node list --query "sort_by(items[*], &name)[-1]"
```

Klaster usługi Service Fabric musi „wiedzieć”, że ten węzeł ma zostać usunięty. Musisz wykonać trzy kroki:

1. Wyłącz węzeł, aby zatrzymać replikację danych.  
PowerShell: `Disable-ServiceFabricNode`  
sfctl: `sfctl node disable`

2. Zatrzymaj węzeł, aby środowisko uruchomieniowe usługi Service Fabric zostało prawidłowo zamknięte, a aplikacja otrzymała żądanie przerwania.  
PowerShell: `Start-ServiceFabricNodeTransition -Stop`  
sfctl: `sfctl node transition --node-transition-type Stop`

2. Usuń węzeł z klastra.  
PowerShell: `Remove-ServiceFabricNodeState`  
sfctl: `sfctl node remove-state`

Po wykonaniu powyższych kroków węzeł może zostać usunięty z zestawu skalowania. Jeśli korzystasz z innej warstwy trwałości niż [brązowa][durability], kroki te są wykonywane automatycznie w przypadku usunięcia wystąpienia zestawu skalowania.

Poniższy blok kodu obejmuje pobranie ostatnio utworzonego węzła oraz jego wyłączenie, zatrzymanie i usunięcie z klastra.

```powershell
#### After you've connected.....
# Get the node that was created last
$node = Get-ServiceFabricNode | Sort-Object { $_.NodeName.Substring($_.NodeName.LastIndexOf('_') + 1) } -Descending | Select-Object -First 1

# Node details for the disable/stop process
$nodename = $node.NodeName
$nodeid = $node.NodeInstanceId

$loopTimeout = 10

# Run disable logic
Disable-ServiceFabricNode -NodeName $nodename -Intent RemoveNode -TimeoutSec 300 -Force

$state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus

while (($state -ne [System.Fabric.Query.NodeStatus]::Disabled) -and ($loopTimeout -ne 0))
{
    Start-Sleep 5
    $loopTimeout -= 1
    $state = Get-ServiceFabricNode | Where-Object NodeName -eq $nodename | Select-Object -ExpandProperty NodeStatus
    Write-Host "Checking state... $state found"
}

# Exit if the node was unable to be disabled
if ($state -ne [System.Fabric.Query.NodeStatus]::Disabled)
{
    Write-Error "Disable failed with state $state"
}
else
{
    # Stop node
    $stopid = New-Guid
    Start-ServiceFabricNodeTransition -Stop -OperationId $stopid -NodeName $nodename -NodeInstanceId $nodeid -StopDurationInSeconds 300

    $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
    $loopTimeout = 10

    # Watch the transaction
    while (($state -eq [System.Fabric.TestCommandProgressState]::Running) -and ($loopTimeout -ne 0))
    {
        Start-Sleep 5
        $state = (Get-ServiceFabricNodeTransitionProgress -OperationId $stopid).State
        Write-Host "Checking state... $state found"
    }

    if ($state -ne [System.Fabric.TestCommandProgressState]::Completed)
    {
        Write-Error "Stop transaction failed with $state"
    }
    else
    {
        # Remove the node from the cluster
        Remove-ServiceFabricNodeState -NodeName $nodename -TimeoutSec 300 -Force
    }
}
```

W poniższym kodzie przedstawiającym użycie polecenia **sfctl** następujące polecenie służy do pobrania wartości **node-name** ostatnio utworzonego węzła: `sfctl node list --query "sort_by(items[*], &name)[-1].name"`

```shell
# Inform the node that it is going to be removed
sfctl node disable --node-name _nt1vm_5 --deactivation-intent 4 -t 300

# Stop the node using a random guid as our operation id
sfctl node transition --node-instance-id 131541348482680775 --node-name _nt1vm_5 --node-transition-type Stop --operation-id c17bb4c5-9f6c-4eef-950f-3d03e1fef6fc --stop-duration-in-seconds 14400 -t 300

# Remove the node from the cluster
sfctl node remove-state --node-name _nt1vm_5
```

> [!TIP]
> Następujące zapytania **sfctl** pozwalają sprawdzić stan każdego kroku
>
> **Sprawdź stan dezaktywacji**
> `sfctl node list --query "sort_by(items[*], &name)[-1].nodeDeactivationInfo"`
>
> **Sprawdź stan zatrzymania**
> `sfctl node list --query "sort_by(items[*], &name)[-1].isStopped"`
>

### <a name="scale-in-the-scale-set"></a>Skalowanie zestawu w pionie

Po usunięciu węzła usługi Service Fabric z klastra skalę zestawu skalowania maszyn wirtualnych można zmniejszyć w poziomie. W poniższym przykładzie pojemność zestawu skalowania została zmniejszona o 1.

```powershell
$scaleset = Get-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm
$scaleset.Sku.Capacity -= 1

Update-AzVmss -ResourceGroupName SFCLUSTERTUTORIALGROUP -VMScaleSetName nt1vm -VirtualMachineScaleSet $scaleset
```

Pojemność jest ustawiana na 5.

```azurecli
# Get the name of the node with
az vmss list-instances -n nt1vm -g sfclustertutorialgroup --query [*].name

# Use the name to scale
az vmss scale -g sfclustertutorialgroup -n nt1vm --new-capacity 5
```

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Zachowania, które można obserwować w Service Fabric Explorer
Podczas skalowania klastra Service Fabric Explorer odzwierciedla liczbę węzłów (wystąpień zestawu skalowania maszyn wirtualnych), które są częścią klastra.  Jednak podczas skalowania klastra w programie zostanie wyświetlone usunięte wystąpienie węzła/maszyny wirtualnej w złej kondycji, chyba że zostanie wywołane [polecenie Remove-ServiceFabricNodeState cmd](/powershell/module/servicefabric/remove-servicefabricnodestate) z odpowiednią nazwą węzła.   

Oto wyjaśnienie tego zachowania.

Węzły wymienione w Service Fabric Explorer stanowią odbicie Service Fabric usług systemowych (FM) o liczbie węzłów, w których klaster ma/ma. W przypadku skalowania zestawu skalowania maszyn wirtualnych w programie maszyna wirtualna została usunięta, ale usługa systemu w systemie FM nadal uważa, że węzeł (zamapowany na maszynę wirtualną, który został usunięty) zostanie przywrócony. Dlatego Service Fabric Explorer nadal wyświetla ten węzeł (chociaż stan kondycji może być błąd lub nieznany).

Aby mieć pewność, że węzeł zostanie usunięty po usunięciu maszyny wirtualnej, dostępne są dwie opcje:

1. Wybierz poziom trwałości Gold lub Silver dla typów węzłów w klastrze, który zapewnia integrację infrastruktury. Po skalowaniu w poziomie program automatycznie usunie węzły z naszego stanu usług systemowych (FM).
Szczegóły dotyczące [poziomów trwałości](service-fabric-cluster-capacity.md) można znaleźć tutaj.

> [!NOTE]
> Należy zachować minimalną liczbę pięciu węzłów dla dowolnego zestawu skalowania maszyn wirtualnych z włączonym poziomem trwałości Gold lub Silver. W przypadku skalowania poniżej tego progu klaster przejdzie w stan błędu i konieczne będzie ręczne oczyszczenie usuniętych węzłów.

2. Po skalowaniu wystąpienia maszyny wirtualnej w programie należy wywołać [polecenie cmdlet Remove-ServiceFabricNodeState](/powershell/module/servicefabric/remove-servicefabricnodestate).

> [!NOTE]
> Klastry Service Fabric wymagają, aby pewna liczba węzłów była cały czas, aby zachować dostępność i zachować stan — nazywane "konserwacją kworum". W związku z tym zwykle niebezpieczne jest zamknięcie wszystkich maszyn w klastrze, chyba że zostanie wykonana [pełna kopia zapasowa stanu](service-fabric-reliable-services-backup-restore.md).
> 
> 

## <a name="next-steps"></a>Następne kroki
Przeczytaj poniższe informacje, aby dowiedzieć się więcej na temat planowania pojemności klastra, uaktualniania klastra i usług partycjonowania:

* [Planowanie pojemności klastra](service-fabric-cluster-capacity.md)
* [Uaktualnienia klastra](service-fabric-cluster-upgrade.md)
* [Partycjonowanie usług stanowych dla maksymalnej skali](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-in-out/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-in-out/ClusterResources.png

[durability]: service-fabric-cluster-capacity.md#durability-characteristics-of-the-cluster
