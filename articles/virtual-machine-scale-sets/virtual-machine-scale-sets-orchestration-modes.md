---
title: Tryby aranżacji dla zestawów skalowania maszyn wirtualnych na platformie Azure
description: Dowiedz się, jak korzystać z elastycznych i jednolitych trybów aranżacji dla zestawów skalowania maszyn wirtualnych na platformie Azure.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.date: 02/12/2021
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: 316f97480dfe7ee6481ef88faf265051a92024da
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105937264"
---
# <a name="preview-orchestration-modes-for-virtual-machine-scale-sets-in-azure"></a>Wersja zapoznawcza: tryby aranżacji dla zestawów skalowania maszyn wirtualnych na platformie Azure 

Zestawy skalowania Virtual Machines udostępniają logiczne grupowanie maszyn wirtualnych zarządzanych przez platformę. Zestawy skalowania umożliwiają utworzenie modelu konfiguracji maszyny wirtualnej, automatyczne dodanie lub usunięcie dodatkowych wystąpień na podstawie obciążenia procesora lub pamięci oraz automatyczne uaktualnienie do najnowszej wersji systemu operacyjnego. Tradycyjnie zestawy skalowania umożliwiają tworzenie maszyn wirtualnych przy użyciu modelu konfiguracji maszyny wirtualnej, który jest dostępny w momencie tworzenia zestawu skalowania, a zestaw skalowania może zarządzać maszynami wirtualnymi niejawnie utworzonymi na podstawie modelu konfiguracji. 

Tryby aranżacji zestawu skalowania umożliwiają większą kontrolę nad sposobem, w jaki wystąpienia maszyn wirtualnych są zarządzane przez zestaw skalowania. 

> [!IMPORTANT]
> Tryb aranżacji jest definiowany podczas tworzenia zestawu skalowania i nie można go zmienić ani zaktualizować później.


## <a name="scale-sets-with-uniform-orchestration"></a>Zestawy skalowania z jednolitą aranżacją
Optymalizacja pod kątem obciążeń bezstanowych na dużą skalę z identycznymi wystąpieniami.

Zestawy skalowania maszyn wirtualnych z jednolitą aranżacją używają profilu lub szablonu maszyny wirtualnej w celu skalowania do żądanej pojemności. Chociaż istnieje możliwość zarządzania lub dostosowywania poszczególnych wystąpień maszyn wirtualnych, funkcja Uniform używa identycznych wystąpień maszyn wirtualnych. Pojedyncze jednolite wystąpienia maszyn wirtualnych są udostępniane za pośrednictwem poleceń interfejsu API maszyny wirtualnej zestawu skalowania maszyn wirtualnych. Poszczególne wystąpienia nie są zgodne ze standardowymi poleceniami interfejsu API VM usługi Azure IaaS, funkcjami zarządzania platformy Azure, takimi jak Azure Resource Manager tagowanie zasobów, Azure Backup lub Azure Site Recovery. Jednolita aranżacja zapewnia gwarancje wysokiej dostępności domeny błędów w przypadku skonfigurowania mniej niż 100 wystąpień. Jednolita organizacja jest ogólnie dostępna i obsługuje pełną gamę zarządzania zestawami skalowania i aranżacji, w tym Skalowanie automatyczne, ochronę wystąpień i automatyczną modernizację systemu operacyjnego. 


## <a name="scale-sets-with-flexible-orchestration"></a>Zestawy skalowania z elastyczną aranżacją 
Uzyskaj wysoką dostępność na dużą skalę z identycznymi lub wieloma typami maszyn wirtualnych.

Dzięki elastycznej aranżacji platforma Azure zapewnia ujednolicone środowisko w ramach ekosystemu maszyn wirtualnych platformy Azure. Elastyczna aranżacja oferuje gwarancje wysokiej dostępności (do 1000 maszyn wirtualnych) przez rozproszenie maszyn wirtualnych między domenami błędów w regionie lub w strefie dostępności. Umożliwia to skalowanie aplikacji przy zachowaniu izolacji domeny błędów, która jest niezbędna do uruchamiania obciążeń opartych na kworum lub stanowych, w tym:
- Obciążenia oparte na kworum
- Bazy danych Open-Source
- Aplikacje stanowe
- Usługi wymagające wysokiej dostępności i dużej skali
- Usługi, które chcą mieszać typy maszyn wirtualnych lub korzystać ze wszystkich maszyn wirtualnych w miejscu i na żądanie
- Istniejące aplikacje zestawu dostępności  

> [!IMPORTANT]
> Zestawy skalowania maszyn wirtualnych w elastycznym trybie aranżacji są obecnie dostępne w publicznej wersji zapoznawczej. Aby korzystać z funkcji publicznej wersji zapoznawczej opisanej poniżej, wymagana jest procedura zgody.
> Ta wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="what-has-changed-with-flexible-orchestration-mode"></a>Co zmieniło się z elastycznym trybem aranżacji?
Jedną z głównych zalet elastycznej aranżacji jest to, że oferuje funkcje aranżacji w porównaniu ze standardowymi maszynami wirtualnymi IaaS platformy Azure, a nie podrzędnych maszyn wirtualnych w zestawie skalowania. Oznacza to, że można używać wszystkich standardowych interfejsów API maszyn wirtualnych podczas zarządzania elastycznymi wystąpieniami aranżacji, a nie z interfejsów API maszyny wirtualnej zestawu skalowania maszyn wirtualnych, które są używane z jednolitą aranżacją. W trakcie okresu zapoznawczego istnieje kilka różnic między zarządzaniem wystąpieniami w elastycznej aranżacji a jednolitą aranżacją. Ogólnie rzecz biorąc, zaleca się używanie standardowych interfejsów API usługi Azure IaaS VM, gdy jest to możliwe. W tej sekcji przedstawiono przykłady najlepszych rozwiązań związanych z zarządzaniem wystąpieniami maszyn wirtualnych przy użyciu elastycznej aranżacji.  

### <a name="assign-fault-domain-during-vm-creation"></a>Przypisywanie domeny błędów podczas tworzenia maszyny wirtualnej
Możesz wybrać liczbę domen błędów dla elastycznego zestawu skalowania aranżacji. Domyślnie po dodaniu maszyny wirtualnej do elastycznego zestawu skalowania platforma Azure równomiernie rozkłada wystąpienia w domenach błędów. Mimo że zaleca się, aby platforma Azure przypisała domenę błędów, w przypadku scenariuszy zaawansowanych lub rozwiązywania problemów można zastąpić to zachowanie domyślne i określić domenę błędów, w której będzie się pojawić wystąpienie.

```azurecli-interactive 
az vm create –vmss "myVMSS"  –-platform_fault_domain 1
```

### <a name="instance-naming"></a>Nazewnictwo wystąpień 
Podczas tworzenia maszyny wirtualnej i dodawania jej do elastycznego zestawu skalowania masz pełną kontrolę nad nazwami wystąpień w ramach reguł konwencji nazewnictwa platformy Azure. Gdy maszyny wirtualne są automatycznie dodawane do zestawu skalowania za pośrednictwem skalowania automatycznego, należy podać prefiks, a platforma Azure dołącza unikatowy numer do końca nazwy.

### <a name="query-instances-for-power-state"></a>Wystąpienia zapytań dla stanu zasilacza
Preferowaną metodą jest użycie grafu zasobów platformy Azure do wykonywania zapytań dotyczących wszystkich maszyn wirtualnych w zestawie skalowania maszyn wirtualnych. Usługa Azure Resource Graph oferuje wydajną obsługę zapytań dla zasobów platformy Azure na dużą skalę w różnych subskrypcjach. 

``` 
| where type =~ 'Microsoft.Compute/virtualMachines' 
| where properties.virtualMachineScaleSet contains "demo" 
| extend powerState = properties.extended.instanceView.powerState.code 
| project name, resourceGroup, location, powerState 
| order by resourceGroup desc, name desc 
```

Wykonywanie zapytań dotyczących zasobów przy użyciu [usługi Azure Resource Graph](../governance/resource-graph/overview.md) to wygodny i wydajny sposób wykonywania zapytań dotyczących zasobów platformy Azure i minimalizowania wywołań interfejsu API do dostawcy zasobów. Wykres zasobów platformy Azure to ostatecznie spójna pamięć podręczna, w której nowe lub zaktualizowane zasoby mogą nie być odzwierciedlone przez maksymalnie 60 sekund. Oto co możesz zrobić:
- Wyświetlanie listy maszyn wirtualnych w grupie zasobów lub subskrypcji.
- Użyj opcji rozwiń, aby pobrać widok wystąpienia (przypisanie domeny błędów, Stany i stan aprowizacji) dla wszystkich maszyn wirtualnych w ramach subskrypcji.
- Użyj interfejsu API Get VM i poleceń, aby uzyskać model i widok wystąpienia dla jednego wystąpienia.

### <a name="scale-sets-vm-batch-operations"></a>Operacje wsadowe dla zestawów skalowania maszyn wirtualnych
Użyj standardowych poleceń maszyn wirtualnych do uruchamiania, zatrzymywania, ponownego uruchamiania, usuwania wystąpień, a nie interfejsów API maszyny wirtualnej zestawu skalowania maszyn wirtualnych. Operacje wsadowe maszyny wirtualnej zestawu skalowania maszyn wirtualnych (Rozpocznij wszystko, Zatrzymaj wszystkie, Odtwórz wszystkie, wszystkie z obrazu itp.) nie są używane z elastycznym trybem aranżacji. 

### <a name="monitor-application-health"></a>Monitorowanie kondycji aplikacji 
Monitorowanie kondycji aplikacji umożliwia aplikacji udostępnianie platformy Azure za pomocą pulsu w celu ustalenia, czy aplikacja jest w dobrej kondycji. Platforma Azure może automatycznie zastępować wystąpienia maszyn wirtualnych o złej kondycji. W przypadku elastycznych wystąpień zestawu skalowania należy zainstalować i skonfigurować rozszerzenie kondycji aplikacji na maszynie wirtualnej. W przypadku wystąpień z jednolitym zestawem skalowania można użyć rozszerzenia kondycji aplikacji lub zmierzyć kondycję za pomocą Azure Load Balancer niestandardowej sondy kondycji. 

### <a name="list-scale-sets-vm-api-changes"></a>Lista zestawów skalowania — zmiany interfejsu API maszyny wirtualnej 
Virtual Machine Scale Sets umożliwia wyświetlenie listy wystąpień należących do zestawu skalowania. Dzięki elastycznej aranżacji lista Virtual Machine Scale Sets VM polecenie zawiera listę identyfikatorów maszyn wirtualnych zestawów skalowania. Następnie można wywołać polecenia GET Virtual Machine Scale Sets VM, aby uzyskać więcej szczegółowych informacji na temat sposobu pracy zestawu skalowania z wystąpieniem maszyny wirtualnej. Aby uzyskać szczegółowe informacje na temat maszyny wirtualnej, użyj standardowych poleceń pobierania maszyn wirtualnych lub [grafu zasobów platformy Azure](../governance/resource-graph/overview.md). 

### <a name="retrieve-boot-diagnostics-data"></a>Pobieranie danych diagnostycznych rozruchu 
Użyj standardowych interfejsów API i poleceń maszyn wirtualnych, aby pobrać dane diagnostyczne i zrzuty ekranu wystąpienia. Virtual Machine Scale Sets interfejsów API diagnostyki rozruchowych maszyn wirtualnych i poleceń nie są używane z elastycznymi wystąpieniami trybu aranżacji.

### <a name="vm-extensions"></a>Rozszerzenia maszyn wirtualnych 
Używaj rozszerzeń przeznaczonych dla standardowych maszyn wirtualnych zamiast rozszerzeń przeznaczonych dla jednolite wystąpienia trybu aranżacji.


## <a name="a-comparison-of-flexible-uniform-and-availability-sets"></a>Porównanie zestawów elastycznych, jednolitych i dostępności 
W poniższej tabeli porównano elastyczny tryb aranżacji, jednolity tryb aranżacji i zestawy dostępności według ich funkcji.

| Cecha | Obsługiwane przez elastyczną aranżację (wersja zapoznawcza) | Obsługiwane przez jednolitą aranżację (ogólna dostępność) | Obsługiwane przez AvSets (ogólna dostępność) |
|-|-|-|-|
|         Typ maszyny wirtualnej  | Standardowa maszyna wirtualna Azure IaaS (Microsoft. COMPUTE/virtualmachines)  | Maszyny wirtualne konkretnych zestawów skalowania (Microsoft. COMPUTE/virtualmachinescalesets/virtualmachines)  | Standardowa maszyna wirtualna Azure IaaS (Microsoft. COMPUTE/virtualmachines)  |
|         Obsługiwane jednostki SKU  |            Seria D, Seria E, Seria F, Seria, Seria B, Intel, AMD  |            Wszystkie jednostki SKU  |            Wszystkie jednostki SKU  |
|         Strefy dostępności  |            Opcjonalnie można określić wszystkie wystąpienia w pojedynczej strefie dostępności |            Określ wystąpienia w terenie 1, 2 lub 3 strefy dostępności  |            Nieobsługiwane  |
|         Pełna kontrola nad MASZYNami wirtualnymi, kartami sieciowymi, dyskami  |            Tak  |            Ograniczona kontrola za pomocą interfejsu API maszyny wirtualnej zestawów skalowania maszyn wirtualnych  |            Tak  |
|         Skalowanie automatyczne  |            Nie  |            Tak  |            Nie  |
|         Przypisywanie maszyny wirtualnej do określonej domeny błędów  |            Tak  |             Nie   |            Nie  |
|         Usuń karty sieciowe i dyski podczas usuwania wystąpień maszyn wirtualnych  |            Nie  |            Tak  |            Nie  |
|         Zasady uaktualniania (zestawy skalowania maszyn wirtualnych) |            Nie  |            Automatyczne, stopniowe, ręczne  |            Nie dotyczy  |
|         Automatyczne aktualizacje systemu operacyjnego (zestawy skalowania maszyn wirtualnych) |            Nie  |            Tak  |            Nie dotyczy  |
|         W przypadku stosowania poprawek zabezpieczeń gościa  |            Tak  |            Nie  |            Tak  |
|         Kończenie powiadomień (zestawy skalowania maszyn wirtualnych) |            Nie  |            Tak  |            Nie dotyczy  |
|         Naprawa wystąpienia (zestawy skalowania maszyn wirtualnych) |            Nie  |            Tak   |            Nie dotyczy  |
|         Wydajniejsze sieci  |            Tak  |            Tak  |            Tak  |
|         Wystąpienia i Cennik   |            Tak, możesz mieć zarówno wystąpienia, jak i normalne priorytety  |            Tak, wystąpienia muszą być całościowe lub wszystkie zwykłe  |            Nie, tylko wystąpienia regularnego priorytetu  |
|         Mieszane systemy operacyjne  |            Tak, systemy Linux i Windows mogą znajdować się w tym samym elastycznym zestawie skalowania |            Nie, wystąpienia są tego samego systemu operacyjnego  |               Tak, systemy Linux i Windows mogą znajdować się w tym samym elastycznym zestawie skalowania |
|         Monitorowanie kondycji aplikacji  |            Rozszerzenie kondycji aplikacji  |            Program rozszerzenia kondycji aplikacji lub sonda modułu równoważenia obciążenia platformy Azure  |            Rozszerzenie kondycji aplikacji  |
|         UltraSSD dyski   |            Tak  |            Tak, tylko dla wdrożeń strefowych  |            Nie  |
|         InfiniBand   |            Nie  |            Tak, tylko pojedyncza Grupa umieszczania  |            Tak  |
|         akcelerator zapisu   |            Nie  |            Tak  |            Tak  |
|         Grupy umieszczania zbliżeniowe   |            Tak  |            Tak  |            Tak  |
|         Dedykowane hosty platformy Azure   |            Nie  |            Tak  |            Tak  |
|         Podstawowy program do równoważenia   |            Nie  |            Tak  |            Tak  |
|         Azure Load Balancer standardowa jednostka SKU |            Tak  |            Tak  |            Tak  |
|         Application Gateway  |            Nie  |            Tak  |            Tak  |
|         Kontrola konserwacji   |            Nie  |            Tak  |            Tak  |
|         Wyświetlanie listy maszyn wirtualnych w zestawie  |            Tak  |            Tak  |            Tak, Utwórz listę maszyn wirtualnych w AvSet  |
|         Alerty platformy Azure  |            Nie  |            Tak  |            Tak  |
|         Szczegółowe informacje o maszynie wirtualnej  |            Nie  |            Tak  |            Tak  |
|         Azure Backup  |            Tak  |            Tak  |            Tak  |
|         Azure Site Recovery  |     Nie  |            Nie  |            Tak  |
|         Dodaj/Usuń istniejącą maszynę wirtualną do grupy  |            Nie  |            Nie  |            Nie  | 


## <a name="register-for-flexible-orchestration-mode"></a>Zarejestruj się, aby zarządzać elastycznym trybem aranżacji
Przed wdrożeniem zestawów skalowania maszyn wirtualnych w trybie elastycznej aranżacji należy najpierw zarejestrować swoją subskrypcję dla funkcji w wersji zapoznawczej. Rejestracja może potrwać kilka minut. Aby zarejestrować się, można użyć następujących Azure PowerShell lub poleceń interfejsu wiersza polecenia platformy Azure.

### <a name="azure-portal"></a>Azure Portal
Przejdź do strony szczegółów subskrypcji, dla której chcesz utworzyć zestaw skalowania w trybie elastycznej aranżacji, a następnie wybierz pozycję funkcje w wersji zapoznawczej z menu. Wybierz dwie funkcje programu Orchestrator, które mają być włączone: _VMOrchestratorSingleFD_ i _VMOrchestratorMultiFD_, a następnie naciśnij przycisk Zarejestruj. Rejestracja funkcji może potrwać do 15 minut.

![Rejestracja funkcji.](https://user-images.githubusercontent.com/157768/110361543-04d95880-7ff5-11eb-91a7-2e98f4112ae0.png)

Po zarejestrowaniu funkcji dla subskrypcji Zakończ proces wyboru, propagowanie zmiany do dostawcy zasobów obliczeniowych. Przejdź do karty dostawcy zasobów dla subskrypcji, wybierz pozycję Microsoft. COMPUTE, a następnie kliknij pozycję Zarejestruj ponownie.

![Zarejestruj ponownie](https://user-images.githubusercontent.com/157768/110362176-cd1ee080-7ff5-11eb-8cc8-36aa967e267a.png)


### <a name="azure-powershell"></a>Azure PowerShell 
Aby włączyć podgląd subskrypcji, użyj polecenia cmdlet [register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature) . 

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute `
Register-AzProviderFeature -FeatureName VMOrchestratorSingleFD -ProviderNamespace Microsoft.Compute  
```

Rejestracja funkcji może potrwać do 15 minut. Aby sprawdzić stan rejestracji: 

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName VMOrchestratorMultiFD -ProviderNamespace Microsoft.Compute 
```

Po zarejestrowaniu tej funkcji dla subskrypcji Zakończ proces wyboru, propagowanie zmiany do dostawcy zasobów obliczeniowych. 

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute 
```

### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0 
Użyj [AZ Feature Register](/cli/azure/feature#az-feature-register) , aby włączyć podgląd dla Twojej subskrypcji. 

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name VMOrchestratorMultiFD
az feature register --namespace microsoft.compute --name VMOrchestratorSingleFD 
```

Rejestracja funkcji może potrwać do 15 minut. Aby sprawdzić stan rejestracji: 

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name VMOrchestratorMultiFD 
```

Po zarejestrowaniu tej funkcji dla subskrypcji Zakończ proces wyboru, propagowanie zmiany do dostawcy zasobów obliczeniowych. 

```azurecli-interactive
az provider register --namespace Microsoft.Compute 
```


## <a name="get-started-with-flexible-orchestration-mode"></a>Wprowadzenie do elastycznego trybu aranżacji

Rozpocznij pracę z elastycznym trybem aranżacji dla swoich zestawów skalowania za pomocą Azure Portal, interfejsu wiersza polecenia platformy Azure, Terraform lub API REST.

### <a name="azure-portal"></a>Azure Portal

Utwórz zestaw skalowania maszyn wirtualnych w trybie elastycznej aranżacji za pomocą Azure Portal.

1. Zaloguj się do [Azure Portal](https://portal.azure.com).
1. Na pasku wyszukiwania Wyszukaj i wybierz pozycję **zestawy skalowania maszyn wirtualnych**. 
1. Wybierz pozycję **Utwórz** na stronie **zestawów skalowania maszyn wirtualnych** .
1. Na stronie **Tworzenie zestawu skalowania maszyn wirtualnych** Wyświetl sekcję **aranżacja** .
1. W przypadku **trybu aranżacji** wybierz opcję **elastyczne** .
1. Ustaw **liczbę domen błędów**.
1. Zakończ tworzenie zestawu skalowania. Aby uzyskać więcej informacji na temat tworzenia zestawu skalowania [, zobacz Tworzenie zestawu skalowania w Azure Portal](quick-create-portal.md#create-virtual-machine-scale-set) .

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/portal-create-orchestration-mode-flexible.png" alt-text="Tryb aranżacji w portalu podczas tworzenia zestawu skalowania":::

Następnie należy dodać maszynę wirtualną do zestawu skalowania w trybie elastycznej aranżacji.

1. Na pasku wyszukiwania Wyszukaj i wybierz pozycję **maszyny wirtualne**.
1. Na stronie **maszyny wirtualne** wybierz pozycję **Dodaj** .
1. Na karcie **podstawy** zapoznaj się z sekcją **szczegóły wystąpienia** .
1. Dodaj maszynę wirtualną do zestawu skalowania w trybie elastycznej aranżacji, wybierając zestaw skalowania w obszarze **Opcje dostępności**. Maszynę wirtualną można dodać do zestawu skalowania w tym samym regionie, strefie i grupie zasobów.
1. Zakończ tworzenie maszyny wirtualnej. 

:::image type="content" source="./media/virtual-machine-scale-sets-orchestration-modes/vm-portal-orchestration-mode-flexible.png" alt-text="Dodaj maszynę wirtualną do elastycznego zestawu skalowania trybu aranżacji":::


### <a name="azure-cli-20"></a>Interfejs wiersza polecenia platformy Azure 2.0
Utwórz elastyczny zestaw skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure. W poniższym przykładzie pokazano Tworzenie elastycznego zestawu skalowania, w którym liczba domen błędów jest ustawiona na 3, maszyna wirtualna zostanie utworzona, a następnie dodana do elastycznego zestawu skalowania. 

```azurecli-interactive
vmssflexname="my-vmss-vmssflex"  
vmname="myVM"  
rg="my-resource-group"  

az group create -n "$rg" -l $location  
az vmss create -n "$vmssflexname" -g "$rg" -l $location --orchestration-mode flexible --platform-fault-domain-count 3  
az vm create -n "$vmname" -g "$rg" -l $location --vmss $vmssflexname --image UbuntuLTS 
```

### <a name="terraform"></a>Terraform
Utwórz elastyczny zestaw skalowania maszyn wirtualnych z Terraform. Ten proces wymaga **Terraform Azurerm Provider v 2.15.0** lub nowszego. Zwróć uwagę na następujące parametry:
- Gdy strefa nie jest określona, `platform_fault_domain_count` może mieć wartość 1, 2 lub 3, w zależności od regionu.
- Gdy strefa jest określona, `the fault domain count` może mieć wartość 1.
- `single_placement_group` parametr musi być `false` dla elastycznych zestawów skalowania maszyn wirtualnych.
- W przypadku wdrażania regionalnego nie trzeba określać `zones` .

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
    - Interfejs API w wersji 2019-12-01 (lub nowszej) 
    - `false`Podczas tworzenia elastycznego zestawu skalowania musi być pojedyncza Grupa umieszczania

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
    1. Przypisz `virtualMachineScaleSet` Właściwość do zestawu skalowania, który został wcześniej utworzony. Musisz określić `virtualMachineScaleSet` Właściwość podczas tworzenia maszyny wirtualnej. 
    1. Za pomocą funkcji szablonu Azure Resource Manager **Kopiuj ()** można utworzyć wiele maszyn wirtualnych w tym samym czasie. Zobacz [iteracja zasobów](../azure-resource-manager/templates/copy-resources.md#iteration-for-a-child-resource) w szablonach Azure Resource Manager. 

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

Pełny przykład można znaleźć w artykule [Szybki Start platformy Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-vmss-flexible-orchestration-mode) .


## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Jak dużo skalowanie obsługuje elastyczność aranżacji?**

Do zestawu skalowania w elastycznym trybie aranżacji można dodać maksymalnie 1000 maszyn wirtualnych.

**Jak dostępność z elastyczną aranżacją jest porównywana z zestawami dostępności lub jednolitą aranżacją?**

|   | Elastyczna aranżacja  | Jednolita aranżacja  | Zestawy dostępności  |
|-|-|-|-|
| Wdrażanie w strefach dostępności  | Nie  | Tak  | Nie  |
| Gwarancje dostępności domeny błędów w regionie  | Tak, do 1000 wystąpień można rozłożyć na maksymalnie 3 domeny błędów w regionie. Maksymalna liczba domen błędów jest różna w zależności od regionu  | Tak, do 100 wystąpień  | Tak, do 200 wystąpień  |
| Grupy umieszczania  | Tryb elastyczny zawsze używa wielu grup umieszczania (singlePlacementGroup = false)  | Można wybrać pojedynczą grupę umieszczania lub wiele grup umieszczania | Nie dotyczy  |
| Domeny aktualizacji  | Brak, konserwacja lub aktualizacje hosta są gotowe domeny błędów przez domenę błędów  | Do 5 domen aktualizacji  | Do 20 domen aktualizacji  |


## <a name="troubleshoot-scale-sets-with-flexible-orchestration"></a>Rozwiązywanie problemów z zestawami skalowania przy użyciu elastycznej aranżacji
Znajdź odpowiednie rozwiązanie w scenariuszu rozwiązywania problemów. 

```
InvalidParameter. The value 'False' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: True
```

**Przyczyna:** Subskrypcja nie jest zarejestrowana dla elastycznego, publicznej wersji zapoznawczej trybu aranżacji. 

**Rozwiązanie:** Postępuj zgodnie z powyższymi instrukcjami, aby zarejestrować się w publicznej wersji zapoznawczej trybu aranżacji. 

```
InvalidParameter. The specified fault domain count 2 must fall in the range 1 to 1.
```

**Przyczyna:** `platformFaultDomainCount` Parametr jest nieprawidłowy dla wybranego regionu lub strefy. 

**Rozwiązanie:** Musisz wybrać prawidłową `platformFaultDomainCount` wartość. Dla wdrożeń strefowych `platformFaultDomainCount` wartość maksymalna to 1. W przypadku wdrożeń regionalnych, w których żadna strefa nie jest określona, wartość maksymalna `platformFaultDomainCount` różni się w zależności od regionu. Zobacz [Zarządzanie dostępnością maszyn wirtualnych pod kątem skryptów](../virtual-machines/availability.md) , aby określić maksymalną liczbę domen błędów na region. 

```
OperationNotAllowed. Deletion of Virtual Machine Scale Set is not allowed as it contains one or more VMs. Please delete or detach the VM(s) before deleting the Virtual Machine Scale Set.
```

**Przyczyna:** Podjęto próbę usunięcia zestawu skalowania w elastycznym trybie aranżacji, który jest skojarzony z co najmniej jedną maszyną wirtualną. 

**Rozwiązanie:** Usuń wszystkie maszyny wirtualne skojarzone z zestawem skalowania w trybie elastycznej aranżacji, a następnie usuń zestaw skalowania.

```
InvalidParameter. The value 'True' of parameter 'singlePlacementGroup' is not allowed. Allowed values are: False.
```
**Przyczyna:** Subskrypcja jest zarejestrowana dla elastycznego trybu aranżacji w wersji zapoznawczej; jednak `singlePlacementGroup` parametr ma *wartość true*. 

**Rozwiązanie:** Wartość `singlePlacementGroup` musi być równa *false*. 


## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Dowiedz się, jak wdrożyć aplikację w zestawie skalowania.](virtual-machine-scale-sets-deploy-app.md)