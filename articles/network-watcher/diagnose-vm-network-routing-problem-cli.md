---
title: Diagnozowanie problemu z routingiem sieciowym maszyny wirtualnej — interfejs wiersza polecenia platformy Azure
titleSuffix: Azure Network Watcher
description: W tym artykule dowiesz się, jak używać interfejsu wiersza polecenia platformy Azure do diagnozowania problemów z routingiem sieciowym maszyny wirtualnej przy użyciu funkcji następnego przeskoku Network Watcher platformy Azure.
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
ms.openlocfilehash: 415fcc72116cc36644b58b619404d96ff63b024d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065926"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Diagnozowanie problemu z routingiem sieciowym maszyny wirtualnej — interfejs wiersza polecenia platformy Azure

W tym artykule opisano wdrożenie maszyny wirtualnej, a następnie sprawdzenie komunikacji z adresem IP i adresem URL. Określisz przyczynę niepowodzenia komunikacji oraz sposób rozwiązania problemu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga wersji 2,0 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana. 

- Polecenie interfejsu wiersza polecenia platformy Azure w tym artykule jest sformatowane do uruchamiania w powłoce bash.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Przed utworzeniem maszyny wirtualnej musisz utworzyć grupę zasobów, która będzie zawierała maszynę wirtualną. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az-vm-create). Jeśli klucze SSH nie istnieją jeszcze w domyślnej lokalizacji kluczy, to polecenie je utworzy. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`. Poniższy przykład tworzy maszynę wirtualną o nazwie *myVm*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Nie wykonuj pozostałych kroków do momentu utworzenia maszyny wirtualnej, a interfejs wiersza polecenia platformy Azure zwraca dane wyjściowe.

## <a name="test-network-communication"></a>Testowanie komunikacji sieciowej

Aby przetestować komunikację sieciową z Network Watcher, należy najpierw włączyć obserwatora sieci w regionie, w którym ma zostać przetestowana maszyna wirtualna, a następnie użyć funkcji następnego przeskoku Network Watcher do testowania komunikacji.

### <a name="enable-network-watcher"></a>Włączanie usługi Network Watcher

Jeśli masz już włączony obserwator sieciowy w regionie Wschodnie stany USA, Pomiń, aby [użyć następnego skoku](#use-next-hop). Użyj polecenia [AZ Network obserwator Configure](/cli/azure/network/watcher#az-network-watcher-configure) , aby utworzyć obserwatora sieci w regionie Wschodnie stany USA:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>Korzystanie z funkcji Następny przeskok

Na platformie Azure są automatycznie tworzone trasy do domyślnych miejsc docelowych. Możesz tworzyć trasy niestandardowe zastępujące domyślne trasy. Czasami użycie tras niestandardowych może spowodować niepowodzenie komunikacji. Aby przetestować Routing z maszyny wirtualnej, użyj [AZ Network obserwator show-Next-przeskok](/cli/azure/network/watcher#az-network-watcher-show-next-hop) , aby określić następny przeskok routingu, gdy ruch jest przeznaczony dla określonego adresu.

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

Po kilku sekundach dane wyjściowe informuje o tym, że **nextHopType** jest **Internet** i że **routeTableId** jest **trasą systemową**. Dzięki temu wiadomo, że istnieje prawidłowa trasa do miejsca docelowego.

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

Zwrócone dane wyjściowe informuje o tym, że **żaden** z nich nie jest **NextHopType** i że **RouteTableId** jest również **trasą systemową**. Ten wynik oznacza, że istnieje prawidłowa trasa systemowa do miejsca docelowego, ale nie ma następnego przeskoku umożliwiającego kierowanie ruchu do miejsca docelowego.

## <a name="view-details-of-a-route"></a>Wyświetlanie szczegółów trasy

Aby dodatkowo analizować Routing, przejrzyj efektywne trasy dla interfejsu sieciowego za pomocą polecenia [AZ Network nic show-skuteczna-Route-Table](/cli/azure/network/nic#az-network-nic-show-effective-route-table) :

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

Następujący tekst jest zawarty w zwracanych danych wyjściowych:

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

Gdy użyto `az network watcher show-next-hop` polecenia do testowania komunikacji wychodzącej do 13.107.21.200 w [następnym przeskoku](#use-next-hop), trasa z **addressPrefix** 0.0.0.0/0 * * została użyta do kierowania ruchu do adresu, ponieważ żadna inna trasa w danych wyjściowych nie zawiera adresu. Domyślnie wszystkie adresy, które nie zostały określone w prefiksie adresu innej trasy, są kierowane do Internetu.

W przypadku użycia `az network watcher show-next-hop` polecenia do testowania komunikacji wychodzącej na 172.31.0.100 jednak wynik informuje o braku typu następnego przeskoku. W zwracanych danych wyjściowych zobaczysz również następujący tekst:

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

Jak widać w danych wyjściowych `az network watcher nic show-effective-route-table` polecenia, chociaż istnieje trasa domyślna do prefiksu 172.16.0.0/12, który zawiera adres 172.31.0.100, **NextHopType** ma **wartość None**. Platforma Azure tworzy domyślną trasę dla zakresu adresów 172.16.0.0/12, ale nie określa typu następnego przeskoku, jeśli nie jest to wymagane. Jeśli na przykład dodaliśmy zakres adresów 172.16.0.0/12 do przestrzeni adresowej sieci wirtualnej, platforma Azure zmieni **nextHopType** na **sieć wirtualną** dla trasy. W wyniku sprawdzenia zostanie wyświetlona **Sieć wirtualna** jako **nextHopType**.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete](/cli/azure/group#az-group-delete).

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Następne kroki

W tym artykule utworzono maszynę wirtualną i zdiagnozowano Routing sieciowy z maszyny wirtualnej. Uzyskano informacje o tworzeniu tras domyślnych na platformie Azure i przetestowano routing do dwóch różnych miejsc docelowych. Uzyskaj więcej informacji na temat [routingu na platformie Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) i dowiedz się, jak [tworzyć trasy niestandardowe](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

W przypadku wychodzących połączeń maszyn wirtualnych można również określić opóźnienia i dozwolony i zabroniony ruch sieciowy między maszyną wirtualną a punktem końcowym za pomocą funkcji [rozwiązywania problemów z połączeniem](network-watcher-connectivity-cli.md) Network Watcher. Można monitorować komunikację między maszyną wirtualną a punktem końcowym, takim jak adres IP lub adres URL, w czasie przy użyciu funkcji Network Watcher monitor połączeń. Aby dowiedzieć się, jak to zrobić, zobacz [monitorowanie połączenia sieciowego](connection-monitor.md).
