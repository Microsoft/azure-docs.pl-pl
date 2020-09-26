---
title: 'Szybki Start: diagnozowanie problemu z filtrem ruchu sieciowego maszyny wirtualnej — interfejs wiersza polecenia platformy Azure'
titleSuffix: Azure Network Watcher
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia platformy Azure zdiagnozować problem z filtrem ruchu sieciowego maszyny wirtualnej za pomocą funkcji weryfikacji przepływu adresu IP platformy Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose a virtual machine (VM) network traffic filter problem that prevents communication to and from a VM.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: kumud
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 871c4fc69daac9d5f515fdf3e4ec0ca1de6fbe08
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91295978"
---
# <a name="quickstart-diagnose-a-virtual-machine-network-traffic-filter-problem---azure-cli"></a>Szybki start: diagnozowanie problemu z filtrowaniem ruchu sieciowego maszyny wirtualnej — interfejs wiersza polecenia platformy Azure

W tym przewodniku Szybki start wdrożysz maszynę wirtualną, a następnie sprawdzisz komunikację z adresem IP i adresem URL oraz komunikację z adresu IP. Określisz przyczynę niepowodzenia komunikacji oraz sposób rozwiązania problemu.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia platformy Azure i korzystać z niego lokalnie, ten przewodnik Szybki Start będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja została zainstalowana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Po sprawdzeniu wersji interfejsu wiersza polecenia platformy Azure Uruchom polecenie, `az login`  Aby utworzyć połączenie z platformą Azure. Polecenie interfejsu wiersza polecenia platformy Azure w tym przewodniku Szybki Start jest sformatowane do uruchamiania w powłoce bash.

## <a name="create-a-vm"></a>Tworzenie maszyny wirtualnej

Przed utworzeniem maszyny wirtualnej musisz utworzyć grupę zasobów, która będzie zawierała maszynę wirtualną. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm). Jeśli klucze SSH nie istnieją jeszcze w domyślnej lokalizacji kluczy, to polecenie je utworzy. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`. Poniższy przykład tworzy maszynę wirtualną o nazwie *myVm*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Nie wykonuj pozostałych kroków do momentu utworzenia maszyny wirtualnej, a interfejs wiersza polecenia platformy Azure zwraca dane wyjściowe.

## <a name="test-network-communication"></a>Testowanie komunikacji sieciowej

Aby przetestować komunikację sieciową za pomocą usługi Network Watcher, należy najpierw włączyć usługę Network Watcher w regionie, w którym znajduje się maszyna wirtualna do przetestowania, a następnie użyć możliwości weryfikowania przepływu adresów IP dostępnej w usłudze Network Watcher.

### <a name="enable-network-watcher"></a>Włączanie usługi Network Watcher

Jeśli masz już włączoną usługę Network Watcher w regionie Wschodnie stany USA, przejdź do sekcji [Korzystanie z weryfikowania przepływu adresów IP](#use-ip-flow-verify). Za pomocą polecenia [az network watcher configure](/cli/azure/network/watcher#az-network-watcher-configure) utwórz usługę Network Watcher w regionie Wschodnie stany USA:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-ip-flow-verify"></a>Korzystanie z weryfikowania przepływu adresów IP

Podczas tworzenia maszyny wirtualnej platforma Azure domyślnie zezwala na i blokuje ruch sieciowy do i z maszyny wirtualnej. Domyślne ustawienia platformy Azure można później zastąpić, aby zezwalać lub nie zezwalać na dodatkowe typy ruchu. Aby sprawdzić, czy ruch do różnych miejsc docelowych i ze źródłowego adresu IP jest dozwolony, czy blokowany, użyj polecenia [az network watcher test-ip-flow](/cli/azure/network/watcher#az-network-watcher-test-ip-flow).

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

Aby dowiedzieć się, dlaczego reguły wymienione w sekcji [Korzystanie z weryfikowania przepływu adresów IP](#use-ip-flow-verify) zezwalają na lub blokują komunikację, przejrzyj obowiązujące reguły zabezpieczeń dla interfejsu sieciowego za pomocą polecenia [az network nic list-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg):

```azurecli-interactive
az network nic list-effective-nsg \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

Zwrócone dane wyjściowe obejmują następujący tekst dla reguły **AllowInternetOutbound**, która zezwalała na ruch wychodzący do witryny www.bing.com w sekcji [Korzystanie z weryfikowania przepływu adresów IP](#use-ip-flow-verify) w poprzednim kroku:

```
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

```
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

```
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

W tym przewodniku Szybki utworzono maszynę wirtualną i zdiagnozowano filtry przychodzącego i wychodzącego ruchu sieciowego. Wiesz już, że reguły sieciowej grupy zabezpieczeń zezwalają na lub blokują ruch do i z maszyny wirtualnej. Dowiedz się więcej o [regułach zabezpieczeń](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) oraz [tworzeniu reguł zabezpieczeń](../virtual-network/manage-network-security-group.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-security-rule).

Nawet przy zastosowaniu odpowiednich filtrów ruchu sieciowego komunikacja z maszyną wirtualną może nadal kończyć się niepowodzeniem z powodu konfiguracji routingu. Aby dowiedzieć się, jak diagnozować problemy z routingiem sieci maszyny wirtualnej, zobacz [Diagnozowanie problemów z routingiem maszyny wirtualnej](diagnose-vm-network-routing-problem-cli.md). Aby za pomocą jednego narzędzia diagnozować problemy z routingiem ruchu wychodzącego, opóźnieniem i filtrowaniem ruchu, zobacz [Rozwiązywanie problemów z połączeniami](network-watcher-connectivity-cli.md).
