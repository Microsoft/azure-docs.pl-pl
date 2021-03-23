---
title: Użyj interfejsu wiersza polecenia, aby wdrożyć Virtual Machines platformy Azure
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia wdrożyć Virtual Machines platformy Azure w celu oszczędności kosztów.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 90ad35757834c14abdffb017ff31b3296074ca24
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802441"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>Wdrażanie Virtual Machines usługi Azure spot przy użyciu interfejsu wiersza polecenia platformy Azure

Korzystanie z [usługi Azure Spot Virtual Machines](../spot-vms.md) umożliwia korzystanie z nieużywanej pojemności przy znaczącym obciążeniu kosztów. W dowolnym momencie, gdy platforma Azure potrzebuje pojemności z powrotem, infrastruktura platformy Azure wyłączy Virtual Machines na platformie Azure. Z tego względu Virtual Machines na platformie Azure są doskonałe dla obciążeń, które mogą obsłużyć przerwy, takie jak zadania przetwarzania wsadowego, środowiska deweloperskie/testowe, duże obciążenia obliczeniowe i inne.

Cennik usługi Azure Virtual Machines w miejscu to zmienna, na podstawie regionu i jednostki SKU. Aby uzyskać więcej informacji, zobacz cennik maszyn wirtualnych dla [systemów](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) i Windows. 

Dla maszyny wirtualnej można ustawić maksymalną cenę, która ma być płacona za godzinę. Maksymalna cena maszyny wirtualnej usługi Azure Spot można ustawić w dolarach amerykańskich (USD) przy użyciu maksymalnie 5 miejsc dziesiętnych. Na przykład wartość będzie `0.98765` Cena maksymalna $0,98765 USD za godzinę. Jeśli ustawisz maksymalną cenę `-1` , maszyna wirtualna nie zostanie wykluczona na podstawie ceny. Cena maszyny wirtualnej to bieżąca cena za maszynę wirtualną platformy Azure lub cena standardowej maszyny wirtualnej, która kiedykolwiek jest mniejsza, pod warunkiem, że dostępna jest pojemność i przydział. Aby uzyskać więcej informacji na temat ustawiania ceny maksymalnej, zobacz [Virtual Machines platformy Azure — cennik](../spot-vms.md#pricing).

Proces tworzenia maszyny wirtualnej platformy Azure w miejscu przy użyciu interfejsu wiersza polecenia platformy Azure jest taki sam jak szczegółowy w [artykule szybki start](./quick-create-cli.md). Po prostu Dodaj parametr "--Priority", ustaw `--eviction-policy` opcję na "Cofnij alokację" (jest to ustawienie domyślne) lub `Delete` , a następnie podaj maksymalną cenę lub `-1` . 


## <a name="install-azure-cli"></a>Zainstaluj interfejs wiersza polecenia platformy Azure

Aby utworzyć Virtual Machines w miejscu na platformie Azure, musisz uruchomić interfejs wiersza polecenia platformy Azure w wersji 2.0.74 lub nowszej. Aby odnaleźć wersję, uruchom polecenie **az --version**. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

Zaloguj się do platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index#az-login).

```azurecli-interactive
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>Tworzenie maszyny wirtualnej platformy Azure w miejscu

Ten przykład pokazuje, jak wdrożyć maszynę wirtualną platformy Azure w systemie Linux, która nie zostanie wykluczona na podstawie ceny. Zasada wykluczenia jest ustawiona na cofnięcie alokacji maszyny wirtualnej, dzięki czemu można ją uruchomić ponownie w późniejszym czasie. Jeśli chcesz usunąć maszynę wirtualną i dysk podstawowy podczas wykluczania maszyny wirtualnej, ustaw na wartość `--eviction-policy` `Delete` .

```azurecli-interactive
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 \
    --eviction-policy Deallocate
```



Po utworzeniu maszyny wirtualnej można wykonać zapytanie, aby zobaczyć maksymalną cenę rozliczeniową dla wszystkich maszyn wirtualnych w grupie zasobów.

```azurecli-interactive
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>Symulowanie wykluczenia

Możesz symulować wykluczenie maszyny wirtualnej platformy Azure w środowisku REST, programie PowerShell lub interfejsie wiersza polecenia, aby sprawdzić, jak dobrze aplikacja reaguje na nagłe wykluczenie.

W większości przypadków należy użyć interfejsu API REST [Virtual Machines — Symuluj wykluczenie](/rest/api/compute/virtualmachines/simulateeviction) , aby pomóc w zautomatyzowanym testowaniu aplikacji. W przypadku protokołu REST `Response Code: 204` oznacza to, że symulowane wykluczenie zakończyło się pomyślnie. Można połączyć symulowane wykluczenia z [zaplanowaną usługą zdarzeń](scheduled-events.md), aby zautomatyzować, w jaki sposób aplikacja będzie odpowiadać po wykluczeniu maszyny wirtualnej.

Aby zobaczyć zaplanowane zdarzenia w działaniu, Obejrzyj [piątek z platformy Azure, korzystając z usługi azure Scheduled Events w celu przygotowania do konserwacji maszyn wirtualnych](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance).


### <a name="quick-test"></a>Szybki test

Aby szybko sprawdzić, jak działa symulowane wykluczenie, przejdźmy do zapytania dotyczącego usługi zdarzeń zaplanowanych, aby zobaczyć, jak wygląda, gdy symulujesz wykluczenie przy użyciu interfejsu wiersza polecenia platformy Azure.

Zaplanowana usługa zdarzeń jest włączona dla Twojej usługi podczas pierwszego żądania zdarzeń. 

Zdalnie z maszyną wirtualną, a następnie otwórz wiersz polecenia. 

W wierszu polecenia na maszynie wirtualnej wpisz:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Ta pierwsza odpowiedź może potrwać do 2 minut. Od teraz powinny być wyświetlane dane wyjściowe niemal natychmiast.

Na komputerze, na którym zainstalowano interfejs wiersza polecenia platformy Azure (na przykład na komputerze lokalnym), Symuluj wykluczenie przy użyciu polecenia [AZ VM symulacja — wykluczanie](https://docs.microsoft.com/cli/azure/vm#az_vm_simulate_eviction). Zastąp własną nazwę grupy zasobów i nazwę maszyny wirtualnej. 

```azurecli-interactive
az vm simulate-eviction --resource-group mySpotRG --name mySpot
```

Dane wyjściowe odpowiedzi będą mieć, `Status: Succeeded` Jeśli żądanie zostało wykonane pomyślnie.

Szybko Wróć do zdalnego połączenia z maszyną wirtualną, a następnie ponownie wykonaj zapytanie o punkt końcowy Scheduled Events. Powtórz następujące polecenie do momentu uzyskania danych wyjściowych, które zawierają więcej informacji:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Gdy usługa zaplanowanego zdarzenia otrzymuje powiadomienie o wykluczeniu, otrzymasz odpowiedź podobną do:

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

Można je zobaczyć `"EventType":"Preempt"` , a zasób jest zasobem maszyny wirtualnej `"Resources":["myspotvm"]` . 

Możesz również sprawdzić, kiedy maszyna wirtualna zostanie wykluczona, sprawdzając, `"NotBefore"` czy maszyna wirtualna nie zostanie wykluczona przed upływem określonego czasu, dzięki czemu jest to okno aplikacji do bezpiecznego zamknięcia.


## <a name="next-steps"></a>Następne kroki

Możesz również utworzyć maszynę wirtualną platformy Azure w sieci za pomocą [Azure PowerShell](../windows/spot-powershell.md), [portalu](../spot-portal.md)lub [szablonu](spot-template.md).

Zapoznaj się z bieżącymi informacjami o cenach przy użyciu [interfejsu API cen detalicznych platformy Azure](/rest/api/cost-management/retail-prices/azure-retail-prices) , aby uzyskać informacje o maszynie wirtualnej platformy Azure. Elementy `meterName` i `skuName` będą zawierać `Spot` .

Jeśli wystąpi błąd, zobacz [kody błędów](../error-codes-spot.md).
