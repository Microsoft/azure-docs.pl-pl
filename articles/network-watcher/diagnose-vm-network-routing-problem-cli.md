---
title: Diagnozowanie problemu z routingiem sieciowym maszyny wirtualnej — interfejs wiersza polecenia platformy Azure
titleSuffix: Azure Network Watcher
description: W tym artykule dowiesz się, jak za pomocą interfejsu wiersza polecenia platformy Azure zdiagnozować problem z routingiem sieciowym maszyny wirtualnej przy użyciu funkcji następnego przeskoku usługi Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: 2ca7a3b25b1355e21782c1d9f736d20a14cbd4ac
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785455"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Diagnozowanie problemu z routingiem sieciowym maszyny wirtualnej — interfejs wiersza polecenia platformy Azure

W tym artykule wdrożysz maszynę wirtualną, a następnie sprawdzisz komunikację z adresem IP i adresem URL. Określisz przyczynę niepowodzenia komunikacji oraz sposób rozwiązania problemu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana. 

- Polecenia interfejsu wiersza polecenia platformy Azure w tym artykule są sformatowane do uruchamiania w powłoce Bash.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Przed utworzeniem maszyny wirtualnej musisz utworzyć grupę zasobów, która będzie zawierała maszynę wirtualną. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w *lokalizacji eastus:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az_vm_create). Jeśli klucze SSH nie istnieją jeszcze w domyślnej lokalizacji kluczy, to polecenie je utworzy. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`. Poniższy przykład tworzy maszynę wirtualną *o nazwie myVm:*

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Nie kontynuuj pozostałych kroków, dopóki maszyna wirtualna nie zostanie utworzona i interfejs wiersza polecenia platformy Azure nie zwróci danych wyjściowych.

## <a name="test-network-communication"></a>Testowanie komunikacji sieciowej

Aby przetestować komunikację sieciową z usługą Network Watcher, należy najpierw włączyć usługę Network Watcher w regionie, w którym znajduje się maszyna wirtualna, w której ma zostać przetestowana maszyna wirtualna, a następnie użyć możliwości następnego przeskoku usługi Network Watcher do przetestowania komunikacji.

### <a name="enable-network-watcher"></a>Włączanie usługi Network Watcher

Jeśli masz już włączoną usługę Network Watcher w regionie Wschodnie usa, przejdź do [obszaru Użyj następnego przeskoku](#use-next-hop). Użyj polecenia [az network watcher configure,](/cli/azure/network/watcher#az_network_watcher_configure) aby utworzyć usługę Network Watcher w regionie Wschodnie usa:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>Korzystanie z funkcji Następny przeskok

Na platformie Azure są automatycznie tworzone trasy do domyślnych miejsc docelowych. Możesz tworzyć trasy niestandardowe zastępujące domyślne trasy. Czasami użycie tras niestandardowych może spowodować niepowodzenie komunikacji. Aby przetestować routing z maszyny wirtualnej, użyj az [network watcher show-next-hop,](/cli/azure/network/watcher#az_network_watcher_show_next_hop) aby określić następny przeskok routingu, gdy ruch jest przeznaczony dla określonego adresu.

Przetestuj komunikację wychodzącą z maszyny wirtualnej do jednego z adresów IP domeny www.bing.com:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 13.107.21.200 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Po kilku sekundach dane wyjściowe informują, że **nextHopType** to **Internet,** a **routeTableId** to **System Route**. Ten wynik informuje o tym, że istnieje prawidłowa trasa do miejsca docelowego.

Przetestuj komunikację wychodzącą z maszyny wirtualnej do adresu 172.31.0.100:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 172.31.0.100 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Zwrócone dane wyjściowe informują  o tym, że typ None jest **typu nextHopType** i że **routeTableId** jest również **trasą systemową**. Ten wynik oznacza, że istnieje prawidłowa trasa systemowa do miejsca docelowego, ale nie ma następnego przeskoku umożliwiającego kierowanie ruchu do miejsca docelowego.

## <a name="view-details-of-a-route"></a>Wyświetlanie szczegółów trasy

Aby dokładniej przeanalizować routing, przejrzyj efektywne trasy dla interfejsu sieciowego za pomocą polecenia [az network nic show-effective-route-table:](/cli/azure/network/nic#az_network_nic_show_effective_route_table)

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

W zwracanych danych wyjściowych znajduje się następujący tekst:

```console
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
  },
  "addressPrefix": [
    "0.0.0.0/0"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "Internet",
  "source": "Default",
  "state": "Active"
},
```

W przypadku użycia polecenia do przetestowania komunikacji wychodzącej z adresem 13.107.21.200 w funkcji Użyj następnego przeskoku trasa z poprawką `az network watcher show-next-hop` **addressPrefix** 0.0.0.0/0** została użyta do przekierowania ruchu do adresu, ponieważ żadna inna trasa w danych wyjściowych nie zawiera adresu . [](#use-next-hop) Domyślnie wszystkie adresy, które nie zostały określone w prefiksie adresu innej trasy, są kierowane do Internetu.

Jednak podczas testowania komunikacji wychodzącej do 172.31.0.100 za pomocą polecenia wynik informował o tym, że nie ma typu następnego `az network watcher show-next-hop` przeskoku. W zwróconych danych wyjściowych jest również wyświetlony następujący tekst:

```console
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
      },
  "addressPrefix": [
    "172.16.0.0/12"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "None",
  "source": "Default",
  "state": "Active"
},
```

Jak widać w danych wyjściowych polecenia , chociaż istnieje trasa domyślna do prefiksu `az network watcher nic show-effective-route-table` 172.16.0.0/12, który zawiera adres 172.31.0.100, wartość **nextHopType** to **None**. Platforma Azure tworzy domyślną trasę dla zakresu adresów 172.16.0.0/12, ale nie określa typu następnego przeskoku, jeśli nie jest to wymagane. Jeśli na przykład zakres adresów 172.16.0.0/12 został dodany do przestrzeni adresowej sieci wirtualnej, platforma Azure zmieni wartość **nextHopType** na **Sieć** wirtualna dla trasy. Następnie sprawdzenie pokaże sieć **wirtualną** jako **typ nextHopType**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono maszynę wirtualną i zdiagnozowaliśmy routing sieciowy z maszyny wirtualnej. Uzyskano informacje o tworzeniu tras domyślnych na platformie Azure i przetestowano routing do dwóch różnych miejsc docelowych. Uzyskaj więcej informacji na temat [routingu na platformie Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) i dowiedz się, jak [tworzyć trasy niestandardowe](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

W przypadku wychodzących połączeń maszyny wirtualnej można również określić opóźnienie oraz dozwolony i zabroniony ruch [](network-watcher-connectivity-cli.md) sieciowy między maszyną wirtualną i punktem końcowym przy użyciu Network Watcher rozwiązywania problemów z połączeniem usługi Network Watcher. Możesz monitorować komunikację między maszyną wirtualną i punktem końcowym, takim jak adres IP lub adres URL, w czasie, korzystając z Network Watcher monitorowania połączenia. Aby dowiedzieć się, jak to zrobić, [zobacz Monitorowanie połączenia sieciowego.](connection-monitor.md)
