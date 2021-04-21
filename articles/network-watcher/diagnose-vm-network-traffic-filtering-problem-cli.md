---
title: 'Szybki start: diagnozowanie problemu z filtrowaniem ruchu sieciowego maszyny wirtualnej — interfejs wiersza polecenia platformy Azure'
titleSuffix: Azure Network Watcher
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia platformy Azure zdiagnozować problem z filtrowaniem ruchu sieciowego maszyny wirtualnej przy użyciu funkcji weryfikowania przepływu adresów IP usługi Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 01/07/2021
ms.author: kumud
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 701df4353e8d2e36baf0496bd6944c4a95395414
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763275"
---
# <a name="quickstart-diagnose-a-virtual-machine-network-traffic-filter-problem---azure-cli"></a>Szybki start: diagnozowanie problemu z filtrowaniem ruchu sieciowego maszyny wirtualnej — interfejs wiersza polecenia platformy Azure

W tym przewodniku Szybki start wdrożysz maszynę wirtualną, a następnie sprawdzisz komunikację z adresem IP i adresem URL oraz komunikację z adresu IP. Określisz przyczynę niepowodzenia komunikacji oraz sposób rozwiązania problemu.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten przewodnik Szybki start wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana. 

- Polecenia interfejsu wiersza polecenia platformy Azure w tym przewodniku Szybki start są sformatowane do uruchamiania w powłoce Bash.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Przed utworzeniem maszyny wirtualnej musisz utworzyć grupę zasobów, która będzie zawierała maszynę wirtualną. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o *nazwie myResourceGroup* w *lokalizacji eastus:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). Jeśli klucze SSH nie istnieją jeszcze w domyślnej lokalizacji kluczy, to polecenie je utworzy. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`. Poniższy przykład tworzy maszynę wirtualną *o nazwie myVm:*

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Nie kontynuuj pozostałych kroków, dopóki maszyna wirtualna nie zostanie utworzona, a interfejs wiersza polecenia platformy Azure nie zwróci danych wyjściowych.

## <a name="test-network-communication"></a>Testowanie komunikacji sieciowej

Aby przetestować komunikację sieciową za pomocą usługi Network Watcher, należy najpierw włączyć usługę Network Watcher w regionie, w którym znajduje się maszyna wirtualna do przetestowania, a następnie użyć możliwości weryfikowania przepływu adresów IP dostępnej w usłudze Network Watcher.

### <a name="enable-network-watcher"></a>Włączanie usługi Network Watcher

Jeśli masz już włączoną usługę Network Watcher w regionie Wschodnie stany USA, przejdź do sekcji [Korzystanie z weryfikowania przepływu adresów IP](#use-ip-flow-verify). Za pomocą polecenia [az network watcher configure](/cli/azure/network/watcher#az_network_watcher_configure) utwórz usługę Network Watcher w regionie Wschodnie stany USA:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-ip-flow-verify"></a>Korzystanie z weryfikowania przepływu adresów IP

Podczas tworzenia maszyny wirtualnej platforma Azure domyślnie zezwala na i blokuje ruch sieciowy do i z maszyny wirtualnej. Domyślne ustawienia platformy Azure można później zastąpić, aby zezwalać lub nie zezwalać na dodatkowe typy ruchu. Aby sprawdzić, czy ruch do różnych miejsc docelowych i ze źródłowego adresu IP jest dozwolony, czy blokowany, użyj polecenia [az network watcher test-ip-flow](/cli/azure/network/watcher#az_network_watcher_test_ip_flow).

Przetestuj komunikację wychodzącą z maszyny wirtualnej do jednego z adresów IP domeny www.bing.com:

```azurecli-interactive
az network watcher test-ip-flow \
  --direction outbound \
  --local 10.0.0.4:60000 \
  --protocol TCP \
  --remote 13.107.21.200:80 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

Wynik zwrócony po kilku sekundach informuje, że dostęp jest dozwolony dzięki regule zabezpieczeń o nazwie **AllowInternetOutbound**.

Przetestuj komunikację wychodzącą z maszyny wirtualnej do adresu 172.31.0.100:

```azurecli-interactive
az network watcher test-ip-flow \
  --direction outbound \
  --local 10.0.0.4:60000 \
  --protocol TCP \
  --remote 172.31.0.100:80 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

Zwrócony wynik informuje o odmowie dostępu przez regułę zabezpieczeń o nazwie **DefaultOutboundDenyAll**.

Przetestuj komunikację przychodzącą do maszyny wirtualnej z adresu 172.31.0.100:

```azurecli-interactive
az network watcher test-ip-flow \
  --direction inbound \
  --local 10.0.0.4:80 \
  --protocol TCP \
  --remote 172.31.0.100:60000 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

Zwrócony wynik informuje o odmowie dostępu z powodu reguły zabezpieczeń o nazwie **DefaultInboundDenyAll**. Gdy już wiesz, które reguły zabezpieczeń zezwalają na ruch lub blokują ruch do lub z maszyny wirtualnej, możesz określić, jak rozwiązać problemy.

## <a name="view-details-of-a-security-rule"></a>Wyświetlanie szczegółów reguły zabezpieczeń

Aby dowiedzieć się, dlaczego reguły wymienione w sekcji [Korzystanie z weryfikowania przepływu adresów IP](#use-ip-flow-verify) zezwalają na lub blokują komunikację, przejrzyj obowiązujące reguły zabezpieczeń dla interfejsu sieciowego za pomocą polecenia [az network nic list-effective-nsg](/cli/azure/network/nic#az_network_nic_list_effective_nsg):

```azurecli-interactive
az network nic list-effective-nsg \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

Zwrócone dane wyjściowe obejmują następujący tekst dla reguły **AllowInternetOutbound**, która zezwalała na ruch wychodzący do witryny www.bing.com w sekcji [Korzystanie z weryfikowania przepływu adresów IP](#use-ip-flow-verify) w poprzednim kroku:

```console
{
 "access": "Allow",
 "additionalProperties": {},
 "destinationAddressPrefix": "Internet",
 "destinationAddressPrefixes": [
  "Internet"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Outbound",
 "expandedDestinationAddressPrefix": [
  "1.0.0.0/8",
  "2.0.0.0/7",
  "4.0.0.0/6",
  "8.0.0.0/7",
  "11.0.0.0/8",
  "12.0.0.0/6",
  ...
 ],
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/AllowInternetOutBound",
 "priority": 65001,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
},
```

W poprzednich danych wyjściowych widać, że właściwość **destinationAddressPrefix** jest określona jako **Internet**. Nie jest jednak jasne, jak adres 13.107.21.200 jest związany z **Internetem**. W parametrze **expandedDestinationAddressPrefix** znajduje się kilka prefiksów adresów. Jednym z prefiksów na liście jest prefiks **12.0.0.0/6**, który obejmuje zakres adresów IP 12.0.0.1-15.255.255.254. Ponieważ adres 13.107.21.200 mieści się w tym zakresie adresów, reguła **AllowInternetOutBound** zezwala na ruch wychodzący. Ponadto w poprzednich danych wyjściowych nie ma żadnych reguł o wyższym priorytecie (niższym numerze), które przesłaniają tę regułę. Aby blokować komunikację wychodzącą z adresem IP, możesz dodać regułę zabezpieczeń o wyższym priorytecie, która blokuje ruch wychodzący na porcie 80 do tego adresu IP.

Po uruchomieniu polecenia `az network watcher test-ip-flow` w celu przetestowania komunikacji wychodzącej na adres 172.131.0.100 w kroku [Korzystanie z weryfikowania przepływu adresów IP](#use-ip-flow-verify) dane wyjściowe zawierały informację, że reguła **DefaultOutboundDenyAll** blokuje komunikację. Reguła **DefaultOutboundDenyAll** odpowiada regule **DenyAllOutBound** wymienionej w następujących danych wyjściowych polecenia `az network nic list-effective-nsg`:

```console
{
 "access": "Deny",
 "additionalProperties": {},
 "destinationAddressPrefix": "0.0.0.0/0",
 "destinationAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Outbound",
 "expandedDestinationAddressPrefix": null,
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/DenyAllOutBound",
 "priority": 65500,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
}
```

Reguła wyświetla wartość **0.0.0.0/0** dla właściwości **destinationAddressPrefix**. Reguła blokuje komunikację wychodzącą z adresem 172.131.0.100, ponieważ ten adres nie mieści się w ramach prefiksu **destinationAddressPrefix** żadnej innej reguły ruchu wychodzącego w danych wyjściowych polecenia `az network nic list-effective-nsg`. Aby zezwolić na komunikację wychodzącą, możesz dodać regułę zabezpieczeń o wyższym priorytecie, która zezwala na ruch wychodzący na porcie 80 pod adresem 172.131.0.100.

Po uruchomieniu polecenia `az network watcher test-ip-flow` w celu przetestowania komunikacji przychodzącej z adresu 172.131.0.100 w kroku [Korzystanie z weryfikowania przepływu adresów IP](#use-ip-flow-verify) dane wyjściowe zawierały informację, że reguła **DefaultInboundDenyAll** blokuje komunikację. Reguła **DefaultInboundDenyAll** odpowiada regule **DenyAllInBound** wymienionej w następujących danych wyjściowych polecenia `az network nic list-effective-nsg`:

```console
{
 "access": "Deny",
 "additionalProperties": {},
 "destinationAddressPrefix": "0.0.0.0/0",
 "destinationAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Inbound",
 "expandedDestinationAddressPrefix": null,
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/DenyAllInBound",
 "priority": 65500,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
},
```

Reguła **DenyAllInBound** jest stosowana, ponieważ, jak pokazano w danych wyjściowych polecenia `az network nic list-effective-nsg`, nie istnieje żadna inna reguła o wyższym priorytecie, która zezwala na ruch przychodzący na porcie 80 do maszyny wirtualnej z adresu 172.131.0.100. Aby zezwolić na komunikację przychodzącą, możesz dodać regułę zabezpieczeń o wyższym priorytecie, która zezwala na ruch przychodzący na porcie 80 z adresu 172.131.0.100.

Za pomocą testów w tym przewodniku Szybki start przetestowano konfigurację platformy Azure. Jeśli testy zwróciły oczekiwane wyniki, ale nadal występują problemy z siecią, upewnij się, że między maszyną wirtualną a punktem końcowym, z którym się komunikujesz, nie znajduje się zapora oraz że system operacyjny maszyny wirtualnej nie ma zapory, która zezwala na komunikację lub ją blokuje.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie zawarte w niej zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete](/cli/azure/group).

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki utworzono maszynę wirtualną i zdiagnozowano filtry przychodzącego i wychodzącego ruchu sieciowego. Wiesz już, że reguły sieciowej grupy zabezpieczeń zezwalają na lub blokują ruch do i z maszyny wirtualnej. Dowiedz się więcej o [regułach zabezpieczeń](../virtual-network/network-security-groups-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) oraz [tworzeniu reguł zabezpieczeń](../virtual-network/manage-network-security-group.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-security-rule).

Nawet przy zastosowaniu odpowiednich filtrów ruchu sieciowego komunikacja z maszyną wirtualną może nadal kończyć się niepowodzeniem z powodu konfiguracji routingu. Aby dowiedzieć się, jak diagnozować problemy z routingiem sieci maszyny wirtualnej, zobacz [Diagnozowanie problemów z routingiem maszyny wirtualnej](diagnose-vm-network-routing-problem-cli.md). Aby za pomocą jednego narzędzia diagnozować problemy z routingiem ruchu wychodzącego, opóźnieniem i filtrowaniem ruchu, zobacz [Rozwiązywanie problemów z połączeniami](network-watcher-connectivity-cli.md).