---
title: Tryby orkiestracji dla zestawów skalowania maszyn wirtualnych na platformie Azure
description: Dowiedz się, jak używać elastycznych i jednolitych trybów aranżacji dla zestawów skalowania maszyn wirtualnych na platformie Azure.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.date: 02/12/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 72e36a942eeaea00699f346db99a7ca3503495da
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481654"
---
# <a name="preview-orchestration-modes-for-virtual-machine-scale-sets-in-azure"></a>Wersja zapoznawcza: tryby orkiestracji dla zestawów skalowania maszyn wirtualnych na platformie Azure 

Virtual Machines usługi Scale Sets zapewniają logiczne grupowanie maszyn wirtualnych zarządzanych przez platformę. Za pomocą zestawów skalowania tworzysz model konfiguracji maszyny wirtualnej, automatycznie dodajesz lub usuwasz dodatkowe wystąpienia na podstawie obciążenia procesora CPU lub pamięci oraz automatycznie uaktualniasz system operacyjny do najnowszej wersji. Tradycyjnie zestawy skalowania umożliwiają tworzenie maszyn wirtualnych przy użyciu modelu konfiguracji maszyny wirtualnej dostarczonego podczas tworzenia zestawu skalowania, a zestaw skalowania może zarządzać tylko maszynami wirtualnymi, które są niejawnie tworzone na podstawie modelu konfiguracji. 

Tryby orkiestracji zestawu skalowania umożliwiają większą kontrolę nad tym, jak wystąpienia maszyn wirtualnych są zarządzane przez zestaw skalowania. 

> [!IMPORTANT]
> Tryb orkiestracji jest definiowany podczas tworzenia zestawu skalowania i nie można go później zmienić ani zaktualizować.


## <a name="scale-sets-with-uniform-orchestration"></a>Zestawy skalowania z orkiestracji jednolitej
Zoptymalizowany pod kątem bez stanowych obciążeń na dużą skalę z identycznymi wystąpieniami.

Zestawy skalowania maszyn wirtualnych z ujednoliconą aranżacją używają profilu lub szablonu maszyny wirtualnej do skalowania w górę do żądanej pojemności. Chociaż istnieje możliwość dostosowywania poszczególnych wystąpień maszyn wirtualnych lub zarządzania nimi, uniform używa identycznych wystąpień maszyn wirtualnych. Poszczególne wystąpienia jednolitych maszyn wirtualnych są udostępniane za pośrednictwem poleceń interfejsu API zestawu skalowania maszyn wirtualnych. Pojedyncze wystąpienia nie są zgodne ze standardowymi poleceniami interfejsu API maszyn wirtualnych IaaS platformy Azure, funkcjami zarządzania platformy Azure, takimi jak Azure Resource Manager tagowanie zasobów RBAC, Azure Backup lub Azure Site Recovery. Ujednolicona aranżacja zapewnia gwarancje wysokiej dostępności domeny błędów w przypadku skonfigurowania z mniej niż 100 wystąpieniami. Ujednolicona aranżacja jest ogólnie dostępna i obsługuje pełny zakres zarządzania zestawami skalowania i aranżacji, w tym automatyczne skalowanie oparte na metrykach, ochronę wystąpień i automatyczne uaktualnienia systemu operacyjnego. 


## <a name="scale-sets-with-flexible-orchestration"></a>Zestawy skalowania z elastyczną aranżacją 
Uzyskaj wysoką dostępność na dużą skalę przy użyciu identycznych lub wielu typów maszyn wirtualnych.

Dzięki elastycznej orkiestracji platforma Azure zapewnia ujednolicone środowisko w ekosystemie maszyn wirtualnych platformy Azure. Elastyczna aranżacja oferuje gwarancje wysokiej dostępności (do 1000 maszyn wirtualnych) dzięki rozłożeniu maszyn wirtualnych między domeny błędów w regionie lub strefie dostępności. Umożliwia to skalowanie aplikacji w zewnątrz przy zachowaniu izolacji domeny błędów, która jest niezbędna do uruchamiania obciążeń opartych na kworum lub stanowych, w tym:
- Obciążenia oparte na kworum
- Open-Source baz danych
- Aplikacje stanowe
- Usługi, które wymagają wysokiej dostępności i dużej skali
- Usługi, które chcą mieszać typy maszyn wirtualnych lub wspólnie korzystać z maszyn wirtualnych typu spot i na żądanie
- Istniejące aplikacje zestawu dostępności  

> [!IMPORTANT]
> Zestawy skalowania maszyn wirtualnych w trybie elastycznej aranżacji są obecnie dostępne w publicznej wersji zapoznawczej. Procedura zgody na korzystanie z funkcji publicznej wersji zapoznawczej opisana poniżej jest potrzebna.
> Ta wersja zapoznawcza jest dostarczana bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="what-has-changed-with-flexible-orchestration-mode"></a>Co zmieniło się w przypadku elastycznego trybu aranżacji?
Jedną z głównych zalet elastycznej aranżacji jest to, że zapewnia funkcje orkiestracji w standardowych maszynach wirtualnych IaaS platformy Azure, a nie na podrzędne maszyny wirtualne zestawu skalowania. Oznacza to, że podczas zarządzania wystąpieniami elastycznej aranżacji można używać wszystkich standardowych interfejsów API maszyn wirtualnych, a nie interfejsów API maszyn wirtualnych zestawu skalowania maszyn wirtualnych, których używasz z aranżacją Uniform. W okresie zapoznawczym istnieje kilka różnic między zarządzaniem wystąpieniami w ramach orkiestracji elastycznej a orkiestracji jednolitej. Ogólnie zaleca się używanie standardowych interfejsów API maszyn wirtualnych IaaS platformy Azure, gdy jest to możliwe. W tej sekcji przedstawiono przykłady najlepszych rozwiązań w zakresie zarządzania wystąpieniami maszyn wirtualnych za pomocą elastycznej aranżacji.  

### <a name="assign-fault-domain-during-vm-creation"></a>Przypisywanie domeny błędów podczas tworzenia maszyny wirtualnej
Dla zestawu skalowania elastycznej aranżacji można wybrać liczbę domen błędów. Domyślnie po dodaniu maszyny wirtualnej do elastycznego zestawu skalowania platforma Azure równomiernie rozkłada wystąpienia między domeny błędów. Chociaż zaleca się, aby platforma Azure przypisała domenę błędów, w przypadku scenariuszy zaawansowanych lub rozwiązywania problemów można zastąpić to domyślne zachowanie i określić domenę błędów, w której zostanie trafiane wystąpienie.

```azurecli-interactive 
az vm create â€“vmss "myVMSS"  â€“-platform_fault_domain 1
```

### <a name="instance-naming"></a>Nazewnictwo wystąpień 
Po utworzeniu maszyny wirtualnej i dodaniu jej do elastycznego zestawu skalowania masz pełną kontrolę nad nazwami wystąpień w ramach reguł konwencji nazewnictwa platformy Azure. Gdy maszyny wirtualne są automatycznie dodawane do zestawu skalowania za pomocą skalowania automatycznego, należy podać prefiks, a platforma Azure dołączy unikatową liczbę na końcu nazwy.

### <a name="query-instances-for-power-state"></a>Wykonywanie zapytań o wystąpienia dotyczące stanu zasilania
Preferowaną metodą jest użycie Azure Resource Graph do wykonywania zapytań o wszystkie maszyny wirtualne w zestawie skalowania maszyn wirtualnych. Azure Resource Graph zapewnia wydajne możliwości zapytań dla zasobów platformy Azure na dużą skalę w różnych subskrypcjach. 

``` 
|â€¯whereâ€¯typeâ€¯=~â€¯'Microsoft.Compute/virtualMachines' 
|â€¯whereâ€¯properties.virtualMachineScaleSetâ€¯containsâ€¯"demo" 
|â€¯extendâ€¯powerStateâ€¯=â€¯properties.extended.instanceView.powerState.code 
|â€¯projectâ€¯name,â€¯resourceGroup,â€¯location,â€¯powerState 
|â€¯orderâ€¯byâ€¯resourceGroupâ€¯desc,â€¯nameâ€¯desc 
```

Wykonywanie zapytań o zasoby [za pomocą Azure Resource Graph](../governance/resource-graph/overview.md) to wygodny i wydajny sposób wykonywania zapytań o zasoby platformy Azure oraz minimalizowanie wywołań interfejsu API do dostawcy zasobów. Azure Resource Graph to ostatecznie spójna pamięć podręczna, w której nowe lub zaktualizowane zasoby mogą nie być odzwierciedlane przez maksymalnie 60 sekund. Oto co możesz zrobić:
- Lista maszyn wirtualnych w grupie zasobów lub subskrypcji.
- Użyj opcji rozwiń, aby pobrać widok wystąpienia (przypisywanie domeny błędów, stany zasilania i aprowowania) dla wszystkich maszyn wirtualnych w subskrypcji.
- Użyj interfejsu API i poleceń Pobierz maszynę wirtualną, aby uzyskać widok modelu i wystąpienia dla pojedynczego wystąpienia.

### <a name="scale-sets-vm-batch-operations"></a>Operacje usługi Batch maszyn wirtualnych w zestawach skalowania
Użyj standardowych poleceń maszyny wirtualnej, aby uruchamiać, zatrzymywać, uruchamiać ponownie i usuwać wystąpienia zamiast interfejsów API maszyn wirtualnych zestawu skalowania maszyn wirtualnych. Operacje wsadowe zestawu skalowania maszyn wirtualnych (uruchamianie wszystkich, zatrzymywanie wszystkich, odtwarzanie wszystkich z obrazu itp.) nie są używane w trybie elastycznej aranżacji. 

### <a name="monitor-application-health"></a>Monitorowanie kondycji aplikacji 
Monitorowanie kondycji aplikacji umożliwia aplikacji dostarczenie usłudze Azure pulsu w celu określenia, czy aplikacja jest w dobrej kondycji, czy w złej kondycji. Platforma Azure może automatycznie zastąpić wystąpienia maszyn wirtualnych, które są w złej kondycji. W przypadku wystąpień elastycznego zestawu skalowania należy zainstalować i skonfigurować rozszerzenie kondycji aplikacji na maszynie wirtualnej. W przypadku wystąpień jednolitego zestawu skalowania można użyć rozszerzenia kondycji aplikacji lub zmierzyć kondycję za pomocą Azure Load Balancer niestandardowej sondy kondycji. 

### <a name="list-scale-sets-vm-api-changes"></a>Lista zmian interfejsu API maszyn wirtualnych w zestawach skalowania 
Virtual Machine Scale Sets umożliwia tworzenie listy wystąpień należących do zestawu skalowania. W przypadku elastycznej aranżacji lista Virtual Machine Scale Sets maszyny wirtualnej zawiera listę identyfikatorów maszyn wirtualnych zestawów skalowania. Następnie możesz wywołać polecenia GET Virtual Machine Scale Sets VM, aby uzyskać więcej szczegółowych informacji na temat sposobu działania zestawu skalowania z wystąpieniem maszyny wirtualnej. Aby uzyskać pełne szczegóły maszyny wirtualnej, użyj standardowych poleceń get maszyny wirtualnej lub polecenia [Azure Resource Graph](../governance/resource-graph/overview.md). 

### <a name="retrieve-boot-diagnostics-data"></a>Pobieranie danych diagnostycznych rozruchu 
Użyj standardowych interfejsów API i poleceń maszyny wirtualnej, aby pobrać dane diagnostyki rozruchu wystąpienia i zrzuty ekranu. Interfejsy VIRTUAL MACHINE SCALE SETS API i polecenia diagnostyki rozruchu maszyny wirtualnej nie są używane z wystąpieniami elastycznego trybu aranżacji.

### <a name="vm-extensions"></a>Rozszerzenia maszyn wirtualnych 
Używaj rozszerzeń docelowych dla standardowych maszyn wirtualnych, a nie rozszerzeń dla wystąpień jednolitego trybu aranżacji.


## <a name="a-comparison-of-flexible-uniform-and-availability-sets"></a>Porównanie zestawów elastycznych, jednolitych i dostępności 
W poniższej tabeli porównano elastyczny tryb aranżacji, jednolity tryb aranżacji i zestawy dostępności według ich funkcji.

| Cecha | Obsługiwane przez elastyczną aranżację (wersja zapoznawcza) | Obsługiwane przez aranżację Uniform (ogólna dostępność) | Obsługiwane przez avSets (ogólna dostępność) |
|-|-|-|-|
|         Typ maszyny wirtualnej  | Standardowa maszyna wirtualna IaaS platformy Azure (Microsoft.compute /virtualmachines)  | Maszyny wirtualne specyficzne dla zestawu skalowania (Microsoft.compute /virtualmachinescalesets/virtualmachines)  | Standardowa maszyna wirtualna IaaS platformy Azure (Microsoft.compute /virtualmachines)  |
|         Obsługiwane jednostki SKU  |            Seria D, seria E, seria F, seria A, seria B, Intel, AMD  |            Wszystkie jednostki SKU  |            Wszystkie jednostki SKU  |
|         Strefy dostępności  |            Opcjonalnie określ wszystkie wystąpienia w jednej strefie dostępności |            Określanie wystąpień w 1, 2 lub 3 strefach dostępności  |            Nieobsługiwane  |
|         Pełna kontrola nad maszyną wirtualną, karty sieciowe, dyski  |            Tak  |            Ograniczona kontrola za pomocą interfejsu API maszyn wirtualnych zestawów skalowania maszyn wirtualnych  |            Tak  |
|         Automatyczne skalowanie  |            Nie  |            Tak  |            Nie  |
|         Przypisywanie maszyny wirtualnej do określonej domeny błędów  |            Tak  |             Nie   |            Nie  |
|         Usuwanie karty sieciowej i dysków podczas usuwania wystąpień maszyn wirtualnych  |            Nie  |            Tak  |            Nie  |
|         Zasady uaktualniania (zestawy skalowania maszyn wirtualnych) |            Nie  |            Automatyczne, Stopniowe, Ręczne  |            Nie dotyczy  |
|         Automatyczne aktualizacje systemu operacyjnego (zestawy skalowania maszyn wirtualnych) |            Nie  |            Tak  |            Nie dotyczy  |
|         W poprawianiu zabezpieczeń gościa  |            Tak  |            Nie  |            Tak  |
|         Powiadomienia o zakończeniu (zestawy skalowania maszyn wirtualnych) |            Nie  |            Tak  |            Nie dotyczy  |
|         Naprawianie wystąpień (zestawy skalowania maszyn wirtualnych) |            Nie  |            Tak   |            Nie dotyczy  |
|         Wydajniejsze sieci  |            Tak  |            Tak  |            Tak  |
|         Spotâ€ âinstances and pricingâ€ â  |            Tak, możesz mieć wystąpienia o priorytecie Spot i Regular  |            Tak, wystąpienia muszą być wszystkie — spot lub wszystkie — regularne  |            Nie, tylko wystąpienia o zwykłym priorytecie  |
|         Mieszane systemy operacyjne  |            Tak, systemy Linux i Windows mogą znajdować się w tym samym elastycznym zestawie skalowania |            Nie, wystąpienia są tym samym systemem operacyjnym  |               Tak, systemy Linux i Windows mogą znajdować się w tym samym elastycznym zestawie skalowania |
|         Monitorowanie kondycji aplikacji  |            Rozszerzenie kondycji aplikacji  |            Rozszerzenie kondycji aplikacji lub sonda usługi Azure Load Balancer  |            Rozszerzenie kondycji aplikacji  |
|         UltraSSDâ€ âDisksâ€ â  |            Tak  |            Tak, tylko w przypadku wdrożeń strefowych  |            Nie  |
|         Infinibandâ€ â  |            Nie  |            Tak, tylko pojedyncza grupa umieszczania  |            Tak  |
|         Writeâ€ âAcceleratorâ€ â  |            Nie  |            Tak  |            Tak  |
|         Proximityâ€ âPlacement Groupsâ€ jako  |            Tak  |            Tak  |            Tak  |
|         Dedykowane hosty platformy Azureâ€ â  |            Nie  |            Tak  |            Tak  |
|         Podstawowa SLBâ€ â  |            Nie  |            Tak  |            Tak  |
|         Azure Load Balancer standardowa SKU |            Tak  |            Tak  |            Tak  |
|         Application Gateway  |            Nie  |            Tak  |            Tak  |
|         Maintenance Controlâ€ zajmij się konserwacją  |            Nie  |            Tak  |            Tak  |
|         Lista maszyn wirtualnych w zestawie  |            Tak  |            Tak  |            Tak, lista maszyn wirtualnych w programie AvSet  |
|         Alerty platformy Azure  |            Nie  |            Tak  |            Tak  |
|         Szczegółowe informacje o maszynie wirtualnej  |            Nie  |            Tak  |            Tak  |
|         Azure Backup  |            Tak  |            Tak  |            Tak  |
|         Azure Site Recovery  |     Nie  |            Nie  |            Tak  |
|         Dodawanie/usuwanie istniejącej maszyny wirtualnej do grupy  |            Nie  |            Nie  |            Nie  | 


## <a name="register-for-flexible-orchestration-mode"></a>Rejestrowanie w trybie elastycznej aranżacji
Przed wdrożeniem zestawów skalowania maszyn wirtualnych w trybie elastycznej aranżacji należy najpierw zarejestrować subskrypcję dla funkcji w wersji zapoznawczej. Rejestracja może potrwać kilka minut. Do zarejestrowania się możesz użyć Azure PowerShell interfejsu wiersza polecenia platformy Azure.

### <a name="azure-portal"></a>Azure Portal
Przejdź do strony szczegółów subskrypcji, dla których chcesz utworzyć zestaw skalowania w trybie elastycznej aranżacji, a następnie wybierz pozycję Funkcje w wersji zapoznawczej z menu. Wybierz dwie funkcje orkiestratora, które chcesz włączyć: _VMO nieschematytratorSingleFD_ i _VMO niesklasyfikatormultiFD,_ a następnie naciśnij przycisk Zarejestruj. Rejestracja funkcji może potrwać do 15 minut.

![Rejestracja funkcji.](https://user-images.githubusercontent.com/157768/110361543-04d95880-7ff5-11eb-91a7-2e98f4112ae0.png)

Po zarejestrowaniu funkcji dla subskrypcji ukończ proces zgody, propagując zmianę do dostawcy zasobów obliczeniowych. Przejdź do karty Dostawcy zasobów dla subskrypcji, wybierz pozycję Microsoft.compute, a następnie kliknij pozycję Zarejestruj ponownie.

![Ponowne rejestrowanie](https://user-images.githubusercontent.com/157768/110362176-cd1ee080-7ff5-11eb-8cc8-36aa967e267a.png)


### <a name="azure-powershell"></a>Azure PowerShell 
Użyj polecenia cmdlet [Register-AzProviderFeature,](/powershell/module/az.resources/register-azproviderfeature) aby włączyć podgląd subskrypcji. 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMOrchestratorSingleFD -ProviderNamespace Microsoft.Compute  
```

Rejestracja funkcji może potrwać do 15 minut. Aby sprawdzić stan rejestracji: 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute 
```

Po zarejestrowaniu funkcji dla subskrypcji ukończ proces zgody, propagując zmianę do dostawcy zasobów obliczeniowych. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0 
Użyj [az feature register,](/cli/azure/feature#az-feature-register) aby włączyć podgląd subskrypcji. 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD 
```

Rejestracja funkcji może potrwać do 15 minut. Aby sprawdzić stan rejestracji: 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD 
```

Po zarejestrowaniu funkcji dla subskrypcji ukończ proces zgody, propagując zmianę do dostawcy zasobów obliczeniowych. 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```


## <a name="get-started-with-flexible-orchestration-mode"></a>Wprowadzenie do elastycznego trybu aranżacji

Rozpoczynanie pracy z elastycznym trybem aranżacji zestawów skalowania za pośrednictwem interfejsu Azure Portal, interfejsu wiersza polecenia platformy Azure, narzędzia Terraform lub interfejsu API REST.

### <a name="azure-portal"></a>Azure Portal

Utwórz zestaw skalowania maszyn wirtualnych w trybie elastycznej aranżacji za pośrednictwem Azure Portal.

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
1. Na pasku wyszukiwania wyszukaj i wybierz pozycję **Zestawy skalowania maszyn wirtualnych.** 
1. Wybierz **pozycję Utwórz** na stronie **Zestawy skalowania maszyn** wirtualnych.
1. Na stronie **Tworzenie zestawu skalowania maszyn wirtualnych** wyświetl **sekcję Orchestration (Aranżacja).**
1. Dla opcji **Tryb aranżacji** wybierz opcję **Elastyczna.**
1. Ustaw liczbę **domen błędów**.
1. Zakończ tworzenie zestawu skalowania. Zobacz [tworzenie zestawu skalowania w Azure Portal,](quick-create-portal.md#create-virtual-machine-scale-set) aby uzyskać więcej informacji na temat sposobu tworzenia zestawu skalowania.

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/portal-create-orchestration-mode-flexible.png" alt-text="Tryb orkiestracji w portalu podczas tworzenia zestawu skalowania":::

Następnie dodaj maszynę wirtualną do zestawu skalowania w trybie elastycznej aranżacji.

1. Na pasku wyszukiwania wyszukaj i wybierz **pozycję Maszyny wirtualne.**
1. Wybierz **pozycję** Dodaj na **stronie Maszyny** wirtualne.
1. Na karcie **Podstawowe** wyświetl sekcję **Szczegóły** wystąpienia.
1. Dodaj maszynę wirtualną do zestawu skalowania w trybie elastycznej aranżacji, wybierając zestaw skalowania w **opcjach dostępności.** Maszynę wirtualną można dodać do zestawu skalowania w tym samym regionie, strefie i grupie zasobów.
1. Zakończ tworzenie maszyny wirtualnej. 

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/vm-portal-orchestration-mode-flexible.png" alt-text="Dodawanie maszyny wirtualnej do zestawu skalowania w trybie elastycznej aranżacji":::


### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
Tworzenie elastycznego zestawu skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure. W poniższym przykładzie pokazano tworzenie elastycznego zestawu skalowania, w którym liczba domen błędów jest ustawiona na 3, maszyna wirtualna jest tworzona, a następnie dodawana do elastycznego zestawu skalowania. 

```azurecli-interactive
vmssflexname="my-vmss-vmssflex"  
vmname="myVM"  
rg="my-resource-group"  

az group create -n "$rg" -l $location  
az vmss create -n "$vmssflexname" -g "$rg" -l $location --orchestration-mode flexible --platform-fault-domain-count 3  
az vm create -n "$vmname" -g "$rg" -l $location --vmss $vmssflexname --image UbuntuLTS 
```

### <a name="terraform"></a>Terraform
Tworzenie elastycznego zestawu skalowania maszyn wirtualnych za pomocą platformy Terraform. Ten proces wymaga dostawcy **Terraform Azurerm w wersji 2.15.0 lub** nowszej. Zwróć uwagę na następujące parametry:
- Jeśli nie określono strefy, `platform_fault_domain_count` może być 1, 2 lub 3 w zależności od regionu.
- Jeśli określono strefę, `the fault domain count` może być 1.
- `single_placement_group` Parametr musi być dla `false` elastycznych zestawów skalowania maszyn wirtualnych.
- W przypadku wdrażania regionalnego nie trzeba określać wartości `zones` .

```terraform
resource "azurerm orchestrated_virtual_machine_scale_set" "tf_vmssflex" {
name = "tf_vmssflex"
location = azurerm_resource_group.myterraformgroup.location
resource_group_name = azurerm_resource_group.myterraformgroup.name
platform_fault_domain_count = 1
single_placement_group = false
zones = ["1"]
}
```


### <a name="rest-api"></a>Interfejs API REST

1. Utwórz pusty zestaw skalowania. Wymagane są następujące parametry:
    - Interfejs API w wersji 2019-12-01 (lub nowsza) 
    - Podczas tworzenia elastycznego zestawu skalowania `false` musi znajdować się pojedyncza grupa umieszczania

    ```json
    {
    "type": "Microsoft.Compute/virtualMachineScaleSets",
    "name": "[parameters('virtualMachineScaleSetName')]",
    "apiVersion": "2019-12-01",
    "location": "[parameters('location')]",
    "properties": {
        "singlePlacementGroup": false,
        "platformFaultDomainCount": "[parameters('virtualMachineScaleSetPlatformFaultDomainCount')]"
        },
    "zones": "[variables('selectedZone')]"
    }
    ```

2. Dodaj maszyny wirtualne do zestawu skalowania.
    1. Przypisz `virtualMachineScaleSet` właściwość do wcześniej utworzonego zestawu skalowania. Musisz określić właściwość `virtualMachineScaleSet` w czasie tworzenia maszyny wirtualnej. 
    1. Możesz użyć funkcji **Azure Resource Manager copy()** w celu utworzenia wielu maszyn wirtualnych w tym samym czasie. Zobacz [Iteracja zasobów](../azure-resource-manager/templates/copy-resources.md#iteration-for-a-child-resource) w Azure Resource Manager szablonów. 

    ```json
    {
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[concat(parameters('virtualMachineNamePrefix'), copyIndex(1))]",
    "apiVersion": "2019-12-01",
    "location": "[parameters('location')]",
    "copy": {
        "name": "VMcopy",
        "count": "[parameters('virtualMachineCount')]"
        },
    "dependsOn": [
        "
        [resourceID('Microsoft.Compute/virtualMachineScaleSets', parameters('virtualMachineScaleSetName'))]",
        "
        [resourceID('Microsoft.Storage/storageAccounts', variables('diagnosticsStorageAccountName'))]",
        "
        [resourceID('Microsoft.Network/networkInterfaces', concat(parameters('virtualMachineNamePrefix'), copyIndex(1), '-NIC1'))]"
        ],
    "properties": {
        "virtualMachineScaleSet": {
            "id": "[resourceID('Microsoft.Compute/virtualMachineScaleSets', parameters('virtualMachineScaleSetName'))]"
        }
    }
    ```

Pełny [przykład można znaleźć w przewodniku Szybki](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-vmss-flexible-orchestration-mode) start platformy Azure.


## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Jaka skala obsługuje elastyczna aranżacja?**

Do zestawu skalowania można dodać maksymalnie 1000 maszyn wirtualnych w trybie elastycznej aranżacji.

**Jak dostępność z elastyczną aranżacją wypada w porównaniu z zestawami dostępności lub orkiestracji jednolitej?**

| Atrybut dostępności  | Elastyczna aranżacja  | Jednolite orkiestracji  | Zestawy dostępności  |
|-|-|-|-|
| Wdrażanie w różnych strefach dostępności  | Nie  | Tak  | Nie  |
| Gwarancje dostępności domeny błędów w obrębie regionu  | Tak, maksymalnie 1000 wystąpień można rozłożyć na maksymalnie 3 domeny błędów w regionie. Maksymalna liczba domen błędów różni się w zależności od regionu  | Tak, maksymalnie 100 wystąpień  | Tak, maksymalnie 200 wystąpień  |
| Grupy umieszczania  | Tryb elastyczny zawsze używa wielu grup umieszczania (singlePlacementGroup = false)  | Możesz wybrać pojedynczą grupę umieszczania lub wiele grup umieszczania | Nie dotyczy  |
| Domeny aktualizacji  | Brak, konserwacja lub aktualizacje hosta są wykonywane w domenie błędów według domeny błędów  | Maksymalnie 5 domen aktualizacji  | Maksymalnie 20 domen aktualizacji  |


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>Rozwiązywanie problemów z zestawami skalowania za pomocą elastycznej aranżacji
Znajdź odpowiednie rozwiązanie dla swojego scenariusza rozwiązywania problemów. 

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**Przyczyna:** Subskrypcja nie jest zarejestrowana w publicznej wersji zapoznawczej trybu elastycznej aranżacji. 

**Rozwiązanie:** Postępuj zgodnie z powyższymi instrukcjami, aby zarejestrować się w publicznej wersji zapoznawczej w trybie elastycznej aranżacji. 

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
```

**Przyczyna:** Parametr `platformFaultDomainCount` jest nieprawidłowy dla wybranego regionu lub strefy. 

**Rozwiązanie:** Należy wybrać prawidłową `platformFaultDomainCount` wartość. W przypadku wdrożeń strefowych wartość maksymalna `platformFaultDomainCount` wynosi 1. W przypadku wdrożeń regionalnych, w których nie określono strefy, wartość maksymalna `platformFaultDomainCount` różni się w zależności od regionu. Zobacz [Manage the availability of VMs for scripts](../virtual-machines/availability.md) to determine the maximum fault domain count per region (Zarządzanie dostępnością maszyn wirtualnych dla skryptów), aby określić maksymalną liczbę domen błędów na region. 

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**Przyczyna:** Próbuje usunąć zestaw skalowania w trybie elastycznej aranżacji skojarzonym z co najmniej jedną maszyną wirtualną. 

**Rozwiązanie:** Usuń wszystkie maszyny wirtualne skojarzone z zestawem skalowania w trybie elastycznej aranżacji, a następnie możesz usunąć zestaw skalowania.

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**Przyczyna:** Subskrypcja jest zarejestrowana w wersji zapoznawczej elastycznego trybu aranżacji. Jednak parametr `singlePlacementGroup` ma wartość *True*. 

**Rozwiązanie:** Dla `singlePlacementGroup` musi być ustawiona wartość *False*. 


## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Dowiedz się, jak wdrożyć aplikację w zestawie skalowania.](virtual-machine-scale-sets-deploy-app.md)
