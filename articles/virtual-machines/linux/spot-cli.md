---
title: Wdrażanie usługi Azure Spot przy użyciu interfejsu wiersza polecenia Virtual Machines
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia wdrożyć usługę Azure Spot Virtual Machines, aby zaoszczędzić koszty.
author: cynthn
ms.service: virtual-machines
ms.subservice: spot
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/22/2021
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 8e8bdaa7a812d8c7accfea59b58b75a58d50e21e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789613"
---
# <a name="deploy-azure-spot-virtual-machines-using-the-azure-cli"></a>Wdrażanie usługi Azure Spot Virtual Machines przy użyciu interfejsu wiersza polecenia platformy Azure

Korzystanie [z usługi Azure Spot Virtual Machines](../spot-vms.md) umożliwia wykorzystanie nieużywanej pojemności przy znacznych oszczędnościach kosztów. W dowolnym momencie, gdy platforma Azure potrzebuje pojemności z powrotem, infrastruktura platformy Azure eksmisja usługi Azure Spot Virtual Machines. W związku z tym usługa Azure Spot Virtual Machines doskonałe dla obciążeń, które mogą obsługiwać przerwy, takie jak zadania przetwarzania wsadowego, środowiska tworzenia i testowania, duże obciążenia obliczeniowe i nie tylko.

Cennik usługi Azure Spot Virtual Machines jest zmienny w zależności od regionu i sku. Aby uzyskać więcej informacji, zobacz Cennik maszyn wirtualnych dla [systemów Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) [i Windows.](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) 

Możesz ustawić maksymalną cenę, jaką chcesz płacić za godzinę dla maszyny wirtualnej. Maksymalną cenę maszyny wirtualnej usługi Azure Spot można ustawić w dolarach amerykańskich (USD) przy użyciu maksymalnie 5 miejsc dziesiętnych. Na przykład wartość będzie maksymalną ceną `0.98765` w wysokości 0,98765 USD za godzinę. Jeśli ustawisz maksymalną cenę na , maszyna wirtualna nie zostanie `-1` eksmitowana na podstawie ceny. Cena maszyny wirtualnej to bieżąca cena maszyny wirtualnej usługi Azure Spot lub standardowa maszyna wirtualna, która kiedykolwiek jest mniejsza, o ile dostępna jest pojemność i limit przydziału. Aby uzyskać więcej informacji na temat ustawiania maksymalnej ceny, zobacz [Azure Spot Virtual Machines — Cennik.](../spot-vms.md#pricing)

Proces tworzenia maszyny wirtualnej usługi Azure Spot przy użyciu interfejsu wiersza polecenia platformy Azure jest taki sam jak w artykule [Szybki start.](./quick-create-cli.md) Po prostu dodaj parametr "--priority Spot", ustaw parametr na cofniesz alokację (jest to wartość domyślna) lub , a następnie podaj maksymalną `--eviction-policy` `Delete` cenę lub `-1` . 


## <a name="install-azure-cli"></a>Zainstaluj interfejs wiersza polecenia platformy Azure

Aby utworzyć usługę Azure Spot Virtual Machines, musisz mieć uruchomiony interfejs wiersza polecenia platformy Azure w wersji 2.0.74 lub nowszej. Aby odnaleźć wersję, uruchom polecenie **az --version**. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). 

Zaloguj się do platformy Azure przy użyciu [narzędzia az login](/cli/azure/reference-index#az_login).

```azurecli-interactive
az login
```

## <a name="create-an-azure-spot-virtual-machine"></a>Tworzenie maszyny wirtualnej usługi Azure Spot

W tym przykładzie pokazano, jak wdrożyć maszynę wirtualną usługi Azure Spot z systemem Linux, która nie zostanie eksmitowana na podstawie ceny. Zasady eksmisji są ustawione na cofanie alokacji maszyny wirtualnej, dzięki czemu można ją ponownie uruchomić w późniejszym czasie. Jeśli chcesz usunąć maszynę wirtualną i dysk podstawowy podczas eksmisji maszyny wirtualnej, ustaw `--eviction-policy` wartość `Delete` .

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



Po utworzeniu maszyny wirtualnej możesz utworzyć zapytanie, aby wyświetlić maksymalną cenę rozliczeniową dla wszystkich maszyn wirtualnych w grupie zasobów.

```azurecli-interactive
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>Symulowanie eksmisji

Możesz symulować eksmisję maszyny wirtualnej usługi Azure Spot przy użyciu interfejsu REST, programu PowerShell lub interfejsu wiersza polecenia, aby przetestować, jak dobrze aplikacja będzie reagować na nagłe eksmisje.

W większości przypadków należy użyć interfejsu API REST [Virtual Machines — symulowania eksmisji,](/rest/api/compute/virtualmachines/simulateeviction) aby ułatwić zautomatyzowane testowanie aplikacji. W przypadku rest oznacza, `Response Code: 204` że symulowane eksmisja powiodła się. Możesz połączyć symulowane eksmisje z usługą [Zaplanowane](scheduled-events.md)zdarzenia, aby zautomatyzować sposób reagowania aplikacji po eksmisji maszyny wirtualnej.

Aby zobaczyć zaplanowane zdarzenia w akcji, obejrzyj azure friday — korzystanie z usługi [Azure Scheduled Events w celu przygotowania do konserwacji maszyny wirtualnej.](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance)


### <a name="quick-test"></a>Szybki test

Aby przeprowadzić szybki test, aby pokazać, jak będzie działać symulowane eksmisja, przejdźmy przez wykonywanie zapytań do zaplanowanej usługi zdarzeń, aby sprawdzić, jak to wygląda podczas symulowania eksmisji przy użyciu interfejsu wiersza polecenia platformy Azure.

Usługa Zaplanowane zdarzenia jest włączona dla twojej usługi przy pierwszym żądaniu zdarzeń. 

Na zdalną maszynę wirtualną, a następnie otwórz wiersz polecenia. 

W wierszu polecenia na maszynie wirtualnej wpisz:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Ta pierwsza odpowiedź może potrwać do 2 minut. Od teraz powinny być wyświetlane dane wyjściowe niemal natychmiast.

Na komputerze z zainstalowanym interfejsem wiersza polecenia platformy Azure (na przykład na komputerze lokalnym) zasymuluj eksmisję za pomocą [polecenia az vm simulate-eviction](https://docs.microsoft.com/cli/azure/vm#az_vm_simulate_eviction). Zastąp nazwę grupy zasobów i nazwę maszyny wirtualnej własną nazwą. 

```azurecli-interactive
az vm simulate-eviction --resource-group mySpotRG --name mySpot
```

Dane wyjściowe odpowiedzi będą `Status: Succeeded` mieć, jeśli żądanie zostało wykonane pomyślnie.

Szybko wróć do połączenia zdalnego z maszyną wirtualną usługi Spot i ponownie Scheduled Events punkt końcowy. Powtarzaj następujące polecenie do momentu uzyskania danych wyjściowych, które zawierają więcej informacji:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2019-08-01
```

Gdy usługa Scheduled Event Service otrzyma powiadomienie o eksmisji, otrzymasz odpowiedź podobną do tej:

```output
{"DocumentIncarnation":1,"Events":[{"EventId":"A123BC45-1234-5678-AB90-ABCDEF123456","EventStatus":"Scheduled","EventType":"Preempt","ResourceType":"VirtualMachine","Resources":["myspotvm"],"NotBefore":"Tue, 16 Mar 2021 00:58:46 GMT","Description":"","EventSource":"Platform"}]}
```

Widać, że `"EventType":"Preempt"` , a zasób jest zasobem maszyny wirtualnej `"Resources":["myspotvm"]` . 

Możesz również sprawdzić, kiedy maszyna wirtualna zostanie eksmisja, sprawdzając , że maszyna wirtualna nie zostanie eksmitowana przed danym czasem, więc jest to okno, w którym aplikacja może bezpiecznie `"NotBefore"` zamknąć się.


## <a name="next-steps"></a>Następne kroki

Maszynę wirtualną usługi Azure Spot można również utworzyć przy [użyciu Azure PowerShell](../windows/spot-powershell.md), [portalu](../spot-portal.md)lub [szablonu](spot-template.md).

Wykonywanie zapytań dotyczących bieżących informacji o cenach przy użyciu [interfejsu API cen detalicznych platformy Azure,](/rest/api/cost-management/retail-prices/azure-retail-prices) aby uzyskać informacje o maszynie wirtualnej usługi Azure Spot. Elementy `meterName` i będą zawierać elementy `skuName` `Spot` .

Jeśli wystąpi błąd, zobacz [Kody błędów](../error-codes-spot.md).
