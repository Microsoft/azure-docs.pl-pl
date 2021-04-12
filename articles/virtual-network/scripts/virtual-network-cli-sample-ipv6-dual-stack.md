---
title: Przykładowy skrypt interfejsu wiersza polecenia platformy Azure — Konfigurowanie frontonu protokołu IPv6
titlesuffix: Azure Virtual Network
description: Użyj przykładowego skryptu interfejsu wiersza polecenia platformy Azure w celu skonfigurowania punktów końcowych protokołu IPv6 i wdrożenia aplikacji podwójnego stosu (IPv4 + IPv6) na platformie Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 04/23/2019
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9058fb4b9b92719f7abcc534632f983cafe2aae8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99550814"
---
# <a name="configure-ipv6-endpoints-in-virtual-network-script-sample"></a>Przykład konfiguracji punktów końcowych protokołu IPv6 w skrypcie sieci wirtualnej

W tym artykule opisano sposób wdrażania aplikacji podwójnego stosu (IPv4 + IPv6) na platformie Azure, która obejmuje sieć wirtualną o podwójnej stercie z podsiecią podwójnego stosu, moduł równoważenia obciążenia z dwoma (IPv4 + IPv6) konfiguracjami frontonu, maszyny wirtualne z kartami sieciowymi z konfiguracją podwójnego adresu IP, podwójne reguły sieciowej grupy zabezpieczeń i dwa publiczne adresy IP.

Skrypt można wykonać z poziomu usługi Azure [Cloud Shell](https://shell.azure.com/bash) lub lokalnej instalacji wiersza polecenia platformy Azure. Jeśli używasz interfejsu wiersza polecenia lokalnie, ten skrypt wymaga uruchomienia wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja została zainstalowana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli). Jeśli używasz interfejsu wiersza polecenia lokalnie, musisz też uruchomić polecenie `az login`, aby utworzyć połączenie z platformą Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Aby użyć funkcji IPv6 dla usługi Azure Virtual Network, należy skonfigurować subskrypcję tylko raz w następujący sposób:

```azurecli
az feature register --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature register --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```

Ukończenie rejestracji funkcji może potrwać do 30 minut. Stan rejestracji można sprawdzić, uruchamiając następujące polecenie interfejsu wiersza polecenia platformy Azure:

```azurecli
az feature show --name AllowIPv6VirtualNetwork --namespace Microsoft.Network
az feature show --name AllowIPv6CAOnStandardLB --namespace Microsoft.Network
```

Po zakończeniu rejestracji Uruchom następujące polecenie:

```azurecli
az provider register --namespace Microsoft.Network
```

## <a name="sample-script"></a>Przykładowy skrypt


```azurecli
# Create a resource group
az group create \
--name DsResourceGroup01 \
--location eastus

# Create an IPV4 IP address
az network public-ip create \
--name dsPublicIP_v4  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

# Create an IPV6 IP address
az network public-ip create \
--name dsPublicIP_v6  \
--resource-group DsResourceGroup01  \
--location eastus \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv6

# Create public IP addresses for remote access to VMs
az network public-ip create \
--name dsVM0_remote_access  \
--resource-group DsResourceGroup01 \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4

# Create load balancer
az network public-ip create \
--name dsVM1_remote_access  \
--resource-group DsResourceGroup01  \
--location eastus  \
--sku BASIC  \
--allocation-method dynamic  \
--version IPv4
az network lb create \
--name dsLB  \
--resource-group DsResourceGroup01 \
--sku Basic \
--location eastus \
--frontend-ip-name dsLbFrontEnd_v4  \
--public-ip-address dsPublicIP_v4  \
--backend-pool-name dsLbBackEndPool_v4

# Create IPv6 front-end
az network lb frontend-ip create \
--lb-name dsLB  \
--name dsLbFrontEnd_v6  \
--resource-group DsResourceGroup01  \
--public-ip-address dsPublicIP_v6

# Configure IPv6 back-end address pool
az network lb address-pool create \
--lb-name dsLB  \
--name dsLbBackEndPool_v6  \
--resource-group DsResourceGroup01

# Create a load balancer rule

az network lb rule create \
--lb-name dsLB  \
--name dsLBrule_v4  \
--resource-group DsResourceGroup01  \
--frontend-ip-name dsLbFrontEnd_v4  \
--protocol Tcp  \
--frontend-port 80  \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v4


az network lb rule create 
--lb-name dsLB  \
--name dsLBrule_v6  \
--resource-group DsResourceGroup01 \
--frontend-ip-name dsLbFrontEnd_v6  \
--protocol Tcp  \
--frontend-port 80 \
--backend-port 80  \
--backend-pool-name dsLbBackEndPool_v6

# Create an availability set
az vm availability-set create \
--name dsAVset  \
--resource-group DsResourceGroup01  \
--location eastus \
--platform-fault-domain-count 2  \
--platform-update-domain-count 2

# Create network security group

az network nsg create \
--name dsNSG1  \
--resource-group DsResourceGroup01  \
--location eastus

# Create inbound rule for port 3389
az network nsg rule create \
--name allowRdpIn  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow Remote Desktop In"  \
--access Allow  \
--protocol "*"  \
--direction Inbound  \
--source-address-prefixes "*"  \
--source-port-ranges 3389  \
--destination-address-prefixes "*"  \
--destination-port-ranges 3389

# Create outbound rule

az network nsg rule create \
--name allowAllOut  \
--nsg-name dsNSG1  \
--resource-group DsResourceGroup01  \
--priority 100  \
--description "Allow All Out"  \
--access Allow  \
--protocol "*"  \
--direction Outbound  \
--source-address-prefixes "*"  \
--source-port-ranges "*"  \
--destination-address-prefixes "*"  \
--destination-port-ranges "*"

# Create the virtual network
az network vnet create \
--name dsVNET \
--resource-group DsResourceGroup01 \
--location eastus  \
--address-prefixes "10.0.0.0/16" "fd00:db8:deca::/48"

# Create a single dual stack subnet

az network vnet subnet create \
--name dsSubNET \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--address-prefix 10.0.0.0/24 \
--address-prefix "fd00:db8:deca:deed::/64" \
--network-security-group dsNSG1

# Create NICs
az network nic create \
--name dsNIC0  \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1  \
--vnet-name dsVNET  \
--subnet dsSubNet  \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4  \
--lb-name dsLB  \
--public-ip-address dsVM0_remote_access

az network nic create \
--name dsNIC1 \
--resource-group DsResourceGroup01 \
--network-security-group dsNSG1 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv4 \
--lb-address-pools dsLbBackEndPool_v4 \
--lb-name dsLB \
--public-ip-address dsVM1_remote_access

# Create IPV6 configurations for each NIC

az network nic ip-config create \
--name dsIp6Config_NIC0  \
--nic-name dsNIC0  \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name dsNIC1 \
--resource-group DsResourceGroup01 \
--vnet-name dsVNET \
--subnet dsSubNet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 
--lb-name dsLB

# Create virtual machines
 az vm create \
--name dsVM0 \
--resource-group DsResourceGroup01 \
--nics dsNIC0 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest  

az vm create \
--name dsVM1 \
--resource-group DsResourceGroup01 \
--nics dsNIC1 \
--size Standard_A2 \
--availability-set dsAVset \
--image MicrosoftWindowsServer:WindowsServer:2016-Datacenter:latest

```

## <a name="clean-up-deployment"></a>Czyszczenie wdrożenia

Uruchom następujące polecenie, aby usunąć grupę zasobów, maszynę wirtualną i wszystkie powiązane zasoby:

```azurecli
az group delete --name <resourcegroupname> --yes
```

## <a name="script-explanation"></a>Objaśnienia dla skryptu

Ten skrypt zawiera następujące polecenia służące do tworzenia grupy zasobów, maszyny wirtualnej, zestawu dostępności, modułu równoważenia obciążenia i wszystkich powiązanych zasobów. Każde polecenie w tabeli stanowi link do dokumentacji polecenia.

| Polecenie | Uwagi |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Tworzy grupę zasobów, w której są przechowywane wszystkie zasoby. |
| [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create) | Tworzy sieć wirtualną i podsieć platformy Azure. |
| [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) | Tworzy publiczny adres IP przy użyciu statycznego adresu IP i skojarzonej nazwy DNS. |
| [az network lb create](/cli/azure/network/lb#az-network-lb-create) | Tworzy moduł równoważenia obciążenia platformy Azure. |
| [az network lb probe create](/cli/azure/network/lb/probe#az-network-lb-probe-create) | Tworzy sondę modułu równoważenia obciążenia. Sonda modułu równoważenia obciążenia służy do monitorowania każdej maszyny wirtualnej w zestawie modułu równoważenia obciążenia. Jeśli maszyna wirtualna stanie się niedostępna, ruch nie będzie do niej kierowany. |
| [az network lb rule create](/cli/azure/network/lb/rule#az-network-lb-rule-create) | Tworzy regułę modułu równoważenia obciążenia. W tym przykładzie tworzona jest reguła dla portu 80. Ruch HTTP docierający do modułu równoważenia obciążenia jest kierowany do portu 80 jednej z maszyn wirtualnych w zestawie modułu równoważenia obciążenia. |
| [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule#az-network-lb-inbound-nat-rule-create) | Tworzy regułę translatora adresów sieciowych modułu równoważenia obciążenia.  Reguły translatora adresów sieciowych mapują port modułu równoważenia obciążenia na port na maszynie wirtualnej. W tym przykładzie tworzona jest reguła translatora adresów sieciowych dla ruchu SSH do każdej maszyny wirtualnej w zestawie modułu równoważenia obciążenia.  |
| [az network nsg create](/cli/azure/network/nsg#az-network-nsg-create) | Tworzy sieciową grupę zabezpieczeń, czyli granicę zabezpieczeń między Internetem i maszyną wirtualną. |
| [az network nsg rule create](/cli/azure/network/nsg/rule#az-network-nsg-rule-create) | Tworzy regułę sieciowej grupy zabezpieczeń zezwalającą na ruch przychodzący. W tym przykładzie port 22 jest otwierany dla ruchu protokołu SSH. |
| [az network nic create](/cli/azure/network/nic#az-network-nic-create) | Tworzy wirtualną kartę sieciową i dołącza ją do sieci wirtualnej, podsieci i sieciowej grupy zabezpieczeń. |
| [az vm availability-set create](/cli/azure/network/lb/rule#az-network-lb-rule-create) | Tworzy zestaw dostępności. Zestawy dostępności zapewniają działanie aplikacji bez przestojów dzięki rozmieszczeniu maszyn wirtualnych w ramach zasobów fizycznych tak, aby niepowodzenie nie wpływało na cały zestaw. |
| [az vm create](/cli/azure/vm#az-vm-create) | Tworzy maszynę wirtualną i łączy ją z kartą sieciową, siecią wirtualną, podsiecią i sieciową grupą zabezpieczeń. To polecenie określa również obraz maszyny wirtualnej do użycia oraz poświadczenia administracyjne.  |
| [az group delete](/cli/azure/vm/extension#az-vm-extension-set) | Usuwa grupę zasobów wraz ze wszystkimi zagnieżdżonymi zasobami. |

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat interfejsu wiersza polecenia platformy Azure, zobacz [dokumentację interfejsu wiersza polecenia platformy Azure](/cli/azure).

Dodatkowe przykłady skryptów interfejsu wiersza polecenia platformy Azure dla sieci można znaleźć w [dokumentacji sieci platformy Azure](../cli-samples.md).