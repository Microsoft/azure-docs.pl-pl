---
title: Konfigurowanie prywatnych adresów IP dla maszyn wirtualnych — interfejs wiersza polecenia platformy Azure
description: Dowiedz się, jak skonfigurować prywatne adresy IP dla maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: 40b03a1a-ea00-454c-b716-7574cea49ac0
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: allensu
ms.openlocfilehash: c34ab73422d8dd41feb9da542ed63fdba060fe3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "84708165"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-cli"></a>Konfigurowanie prywatnych adresów IP dla maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure


[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

> [!NOTE]
> Następujące przykłady poleceń interfejsu wiersza polecenia platformy Azure oczekują istniejącego środowiska prostego. Jeśli chcesz uruchomić polecenia w taki sposób, aby były wyświetlane w tym dokumencie, najpierw Skompiluj środowisko testowe opisane w temacie [Tworzenie sieci wirtualnej](quick-create-cli.md).

## <a name="specify-a-static-private-ip-address-when-creating-a-vm"></a>Określ statyczny prywatny adres IP podczas tworzenia maszyny wirtualnej

Aby utworzyć maszynę wirtualną o nazwie *DNS01* w podsieci *frontonu* sieci wirtualnej o nazwie *TestVNet* ze statycznym prywatnym adresem IP *192.168.1.101*, wykonaj następujące czynności:

1. Jeśli jeszcze tego nie zrobiono, zainstaluj i skonfiguruj najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) i zaloguj się na konto platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index).

2. Uruchom polecenie [AZ Network nic Create](/cli/azure/network/nic) , aby utworzyć kartę sieciową ze statycznym prywatnym adresem IP. Lista wyświetlana po danych wyjściowych zawiera opis używanych parametrów. 
   
    ```azurecli
    az network nic create \
    --resource-group TestRG \
    --name TestNIC \
    --location centralus \
    --subnet FrontEnd \
    --private-ip-address 192.168.1.101 \
    --vnet-name TestVNet
    ```

    Oczekiwane dane wyjściowe:
   
    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.101",
                "privateIPAllocationMethod": "Static",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "<guid>"
        }
    }
    ```
    
    Parametry:

    * `--private-ip-address`: Statyczny prywatny adres IP karty sieciowej.
    * `--vnet-name`: Nazwa sieci wirtualnej, w której ma zostać utworzona karta sieciowa.
    * `--subnet`: Nazwa podsieci, w której ma zostać utworzona karta sieciowa.

3. Uruchom polecenie [Azure VM Create](/cli/azure/vm/nic) , aby utworzyć maszynę wirtualną przy użyciu publicznego adresu IP i karty sieciowej utworzonej wcześniej. Lista wyświetlana po danych wyjściowych zawiera opis używanych parametrów.
   
    ```azurecli
    az vm create \
    --resource-group TestRG \
    --name DNS01 \
    --location centralus \
    --image Debian \
    --admin-username adminuser \
    --ssh-key-value ~/.ssh/id_rsa.pub \
    --nics TestNIC
    ```

    Oczekiwane dane wyjściowe:
   
    ```json
    {
        "fqdns": "",
        "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01",
        "location": "centralus",
        "macAddress": "00-0D-3A-92-C1-66",
        "powerState": "VM running",
        "privateIpAddress": "192.168.1.101",
        "publicIpAddress": "",
        "resourceGroup": "TestRG"
    }
    ```
   
   Parametry inne niż podstawowe polecenie [AZ VM Create](/cli/azure/vm) Parameters.

   * `--nics`: Nazwa karty sieciowej, do której jest dołączona maszyna wirtualna.
   
Zaleca się, aby nie przypisywać statycznie prywatnego adresu IP przypisanego do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej, o ile nie jest to konieczne, [na przykład podczas przypisywania wielu adresów IP do maszyny wirtualnej z systemem Windows](virtual-network-multiple-ip-addresses-cli.md). Jeśli ręcznie ustawisz prywatny adres IP w ramach systemu operacyjnego, upewnij się, że jest to ten sam adres, co prywatny adres IP przypisany do [interfejsu sieciowego](virtual-network-network-interface-addresses.md#change-ip-address-settings)platformy Azure, lub utracisz łączność z maszyną wirtualną. Dowiedz się więcej o ustawieniach [prywatnych adresów IP](virtual-network-network-interface-addresses.md#private) .

## <a name="retrieve-static-private-ip-address-information-for-a-vm"></a>Pobieranie informacji o statycznym prywatnym adresie IP dla maszyny wirtualnej

Uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure, aby obserwować wartości *IP Alloc-Metoda* i *prywatny adres IP*:

```azurecli
az vm show -g TestRG -n DNS01 --show-details --query 'privateIps'
```

Oczekiwane dane wyjściowe:

```json
"192.168.1.101"
```

Aby wyświetlić informacje o adresie IP karty sieciowej dla tej maszyny wirtualnej, należy wykonać zapytanie na kartę sieciową w specjalny sposób:

```azurecli
az network nic show \
-g testrg \
-n testnic \
--query 'ipConfigurations[0].{PrivateAddress:privateIpAddress,IPVer:privateIpAddressVersion,IpAllocMethod:p
rivateIpAllocationMethod,PublicAddress:publicIpAddress}'
```

Dane wyjściowe wyglądają mniej więcej tak:

```json
{
    "IPVer": "IPv4",
    "IpAllocMethod": "Static",
    "PrivateAddress": "192.168.1.101",
    "PublicAddress": null
}
```

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Usuwanie statycznego prywatnego adresu IP z maszyny wirtualnej

Nie można usunąć statycznego prywatnego adresu IP z karty sieciowej w interfejsie wiersza polecenia platformy Azure na potrzeby wdrożeń Azure Resource Manager. Należy:
- Tworzenie nowej karty sieciowej korzystającej z dynamicznego adresu IP
- Skonfiguruj kartę sieciową na maszynie wirtualnej, aby była nowo utworzona karta sieciowa. 

Aby zmienić kartę sieciową dla maszyny wirtualnej używanej w poprzednich poleceniach, wykonaj następujące czynności:

1. Uruchom polecenie **Azure Network nic Create** , aby utworzyć nową kartę sieciową przy użyciu dynamicznego przydzielania adresów IP przy użyciu nowego adresu IP. Ponieważ nie określono żadnego adresu IP, metoda alokacji jest **dynamiczna**.

    ```azurecli
    az network nic create     \
    --resource-group TestRG     \
    --name TestNIC2     \
    --location centralus     \
    --subnet FrontEnd    \
    --vnet-name TestVNet
    ```        
   
    Oczekiwane dane wyjściowe:

    ```json
    {
        "newNIC": {
            "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
            },
            "enableIPForwarding": false,
            "ipConfigurations": [
            {
                "etag": "W/\"<guid>\"",
                "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2/ipConfigurations/ipconfig1",
                "name": "ipconfig1",
                "properties": {
                "primary": true,
                "privateIPAddress": "192.168.1.4",
                "privateIPAllocationMethod": "Dynamic",
                "provisioningState": "Succeeded",
                "subnet": {
                    "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                    "resourceGroup": "TestRG"
                }
                },
                "resourceGroup": "TestRG"
            }
            ],
            "provisioningState": "Succeeded",
            "resourceGuid": "0808a61c-476f-4d08-98ee-0fa83671b010"
        }
    }
    ```

2. Uruchom polecenie **Azure VM Set** , aby zmienić kartę sieciową używaną przez maszynę wirtualną.
   
    ```azurecli
   az vm nic set --resource-group TestRG --vm-name DNS01 --nics TestNIC2
    ```

    Oczekiwane dane wyjściowe:
   
    ```json
    [
        {
            "id": "/subscriptions/0e220bf6-5caa-4e9f-8383-51f16b6c109f/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC3",
            "primary": true,
            "resourceGroup": "TestRG"
        }
    ]
    ```

    > [!NOTE]
    > Jeśli maszyna wirtualna jest wystarczająco duża, aby można było korzystać z więcej niż jednej karty sieciowej, należy uruchomić polecenie **Usuń sieć nic sieci platformy Azure** w celu usunięcia starej karty sieciowej.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zarządzania [ustawieniami adresów IP](virtual-network-network-interface-addresses.md).
