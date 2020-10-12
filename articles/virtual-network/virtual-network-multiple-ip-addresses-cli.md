---
title: Maszyna wirtualna z wieloma adresami IP przy użyciu interfejsu wiersza polecenia platformy Azure
titlesuffix: Azure Virtual Network
description: Dowiedz się, jak przypisać wiele adresów IP do maszyny wirtualnej przy użyciu interfejsu wiersza polecenia (CLI) platformy Azure.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/17/2016
ms.author: allensu
ms.openlocfilehash: 8f3cdad8638f8a1f99942d03f3878d0626c3bdbf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87281246"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-cli"></a>Przypisywanie wielu adresów IP do maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure

[!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]

W tym artykule wyjaśniono, jak utworzyć maszynę wirtualną za pomocą modelu wdrażania Azure Resource Manager przy użyciu interfejsu wiersza polecenia platformy Azure. Nie można przypisać wielu adresów IP do zasobów utworzonych za pomocą klasycznego modelu wdrażania. Aby dowiedzieć się więcej na temat modeli wdrażania platformy Azure, zapoznaj się z artykułem [Omówienie modeli wdrażania](../resource-manager-deployment-model.md) .

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Tworzenie maszyny wirtualnej z wieloma adresami IP

Poniższe kroki wyjaśniają, jak utworzyć przykładową maszynę wirtualną z wieloma adresami IP, zgodnie z opisem w tym scenariuszu. Zmień wartości zmiennych w adresie "" i typy adresów IP, zgodnie z potrzebami, dla implementacji. 

1. Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) , jeśli go jeszcze nie zainstalowano.
2. Utwórz parę kluczy publicznych i prywatnych SSH dla maszyn wirtualnych z systemem Linux, wykonując kroki opisane w sekcji [Tworzenie pary kluczy publicznych i prywatnych protokołu SSH dla maszyn wirtualnych z systemem Linux](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
3. W powłoce poleceń Zaloguj się przy użyciu polecenia `az login` i wybierz subskrypcję, której używasz.
4. Utwórz maszynę wirtualną, wykonując skrypt następujący na komputerze z systemem Linux lub Mac. Skrypt tworzy grupę zasobów, jedną sieć wirtualną (VNet), jedną kartę sieciową z trzema konfiguracjami adresów IP oraz maszynę wirtualną z dołączonymi dwiema kartami sieciowymi. Wszystkie zasoby karty sieciowej, publicznego adresu IP, sieci wirtualnej i maszyny wirtualnej muszą znajdować się w tej samej lokalizacji i subskrypcji. Mimo że zasoby nie muszą znajdować się w tej samej grupie zasobów, w poniższym skrypcie.

```bash
    
#!/bin/sh
    
RgName="myResourceGroup"
Location="westcentralus"
az group create --name $RgName --location $Location
    
# Create a public IP address resource with a static IP address using the `--allocation-method Static` option. If you
# do not specify this option, the address is allocated dynamically. The address is assigned to the resource from a pool
# of IP addresses unique to each Azure region. Download and view the file from
# https://www.microsoft.com/en-us/download/details.aspx?id=41653 that lists the ranges for each region.

PipName="myPublicIP"

# This name must be unique within an Azure location.
DnsName="myDNSName"

az network public-ip create \
--name $PipName \
--resource-group $RgName \
--location $Location \
--dns-name $DnsName\
--allocation-method Static

# Create a virtual network with one subnet

VnetName="myVnet"
VnetPrefix="10.0.0.0/16"
VnetSubnetName="mySubnet"
VnetSubnetPrefix="10.0.0.0/24"

az network vnet create \
--name $VnetName \
--resource-group $RgName \
--location $Location \
--address-prefix $VnetPrefix \
--subnet-name $VnetSubnetName \
--subnet-prefix $VnetSubnetPrefix

# Create a network interface connected to the subnet and associate the public IP address to it. Azure will create the
# first IP configuration with a static private IP address and will associate the public IP address resource to it.

NicName="MyNic1"
az network nic create \
--name $NicName \
--resource-group $RgName \
--location $Location \
--subnet $VnetSubnet1Name \
--private-ip-address 10.0.0.4
--vnet-name $VnetName \
--public-ip-address $PipName
    
# Create a second public IP address, a second IP configuration, and associate it to the NIC. This configuration has a
# static public IP address and a static private IP address.

az network public-ip create \
--resource-group $RgName \
--location $Location \
--name myPublicIP2 \
--dns-name mypublicdns2 \
--allocation-method Static

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--name IPConfig-2 \
--private-ip-address 10.0.0.5 \
--public-ip-name myPublicIP2

# Create a third IP configuration, and associate it to the NIC. This configuration has  static private IP address and   # no public IP address.

az network nic ip-config create \
--resource-group $RgName \
--nic-name $NicName \
--private-ip-address 10.0.0.6 \
--name IPConfig-3

# Note: Though this article assigns all IP configurations to a single NIC, you can also assign multiple IP configurations
# to any NIC in a VM. To learn how to create a VM with multiple NICs, read the Create a VM with multiple NICs 
# article: https://docs.microsoft.com/azure/virtual-network/virtual-network-deploy-multinic-arm-cli.

# Create a VM and attach the NIC.

VmName="myVm"

# Replace the value for the following **VmSize** variable with a value from the
# https://docs.microsoft.com/azure/virtual-machines/sizes article. The script fails if the VM size
# is not supported in the location you select. Run the `azure vm sizes --location eastcentralus` command to get a full list
# of VMs in US West Central, for example.

VmSize="Standard_DS1"

# Replace the value for the OsImage variable value with a value for *urn* from the output returned by entering the
# `az vm image list` command.

OsImage="credativ:Debian:8:latest"

Username="adminuser"

# Replace the following value with the path to your public key file. If you're creating a Windows VM, remove the following
# line and you'll be prompted for the password you want to configure for the VM.

SshKeyValue="~/.ssh/id_rsa.pub"

az vm create \
--name $VmName \
--resource-group $RgName \
--image $OsImage \
--location $Location \
--size $VmSize \
--nics $NicName \
--admin-username $Username \
--ssh-key-value $SshKeyValue
```

Oprócz tworzenia maszyny wirtualnej z kartą sieciową z 3 konfiguracjami IP skrypt tworzy następujące polecenie:

- Pojedynczy dysk zarządzany w warstwie Premium domyślnie, ale dostępne są inne opcje dla typu dysku, który można utworzyć. Aby uzyskać szczegółowe informacje, przeczytaj artykuł [Tworzenie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure](../virtual-machines/linux/quick-create-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) .
- Sieć wirtualna z jedną podsiecią i dwoma publicznymi adresami IP. Alternatywnie możesz użyć *istniejącej* sieci wirtualnej, podsieci, karty sieciowej lub zasobów publicznego adresu IP. Aby dowiedzieć się, jak korzystać z istniejących zasobów sieciowych zamiast tworzyć dodatkowe zasoby, wprowadź `az vm create -h` .

Publiczne adresy IP mają opłata nominalną. Aby dowiedzieć się więcej o cenach adresów IP, zapoznaj się z informacjami na stronie [cennika adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Istnieje limit liczby publicznych adresów IP, które mogą być używane w ramach subskrypcji. Aby uzyskać więcej informacji o limitach, przeczytaj artykuł dotyczący [limitów platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

Po utworzeniu maszyny wirtualnej wprowadź `az network nic show --name MyNic1 --resource-group myResourceGroup` polecenie, aby wyświetlić konfigurację karty sieciowej. Wprowadź, `az network nic ip-config list --nic-name MyNic1 --resource-group myResourceGroup --output table` Aby wyświetlić listę konfiguracji protokołu IP skojarzonych z kartą sieciową.

Aby dodać prywatne adresy IP do systemu operacyjnego maszyny wirtualnej, wykonaj kroki dla systemu operacyjnego w sekcji [Dodawanie adresów IP do systemu operacyjnego maszyny wirtualnej](#os-config) w tym artykule.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>Dodawanie adresów IP do maszyny wirtualnej

Możesz dodać dodatkowe prywatne i publiczne adresy IP do istniejącego interfejsu sieciowego platformy Azure, wykonując poniższe kroki. Przykłady zostały skompilowane na podstawie [scenariusza](#scenario) opisanego w tym artykule.

1. Otwórz powłokę poleceń i wykonaj pozostałe kroki opisane w tej sekcji w ramach jednej sesji. Jeśli nie masz jeszcze zainstalowanego i skonfigurowanego interfejsu wiersza polecenia platformy Azure, wykonaj kroki opisane w artykule [instalacyjnym interfejsu wiersza polecenia platformy Azure](/cli/azure/install-az-cli2?toc=%2fazure%2fvirtual-network%2ftoc.json) i zaloguj się do konta platformy Azure przy użyciu `az-login` poleceń.

2. Wykonaj kroki opisane w jednej z poniższych sekcji, zgodnie z wymaganiami:

    **Dodawanie prywatnego adresu IP**
    
    Aby dodać prywatny adres IP do karty sieciowej, należy utworzyć konfigurację adresu IP za pomocą poniższego polecenia. Statyczny adres IP musi być nieużywanym adresem dla podsieci.

    ```azurecli
    az network nic ip-config create \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --private-ip-address 10.0.0.7 \
    --name IPConfig-4
    ```
    
    Utwórz tyle konfiguracji, ile potrzebujesz, przy użyciu unikatowych nazw konfiguracji i prywatnych adresów IP (dla konfiguracji ze statycznymi adresami IP).

    **Dodawanie publicznego adresu IP**
    
    Publiczny adres IP jest dodawany przez skojarzenie go z nową konfiguracją IP lub istniejącą konfiguracją IP. Wykonaj czynności opisane w jednej z poniższych sekcji, zgodnie z potrzebami.

    Publiczne adresy IP mają opłata nominalną. Aby dowiedzieć się więcej o cenach adresów IP, zapoznaj się z informacjami na stronie [cennika adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses) . Istnieje limit liczby publicznych adresów IP, które mogą być używane w ramach subskrypcji. Aby uzyskać więcej informacji o limitach, przeczytaj artykuł dotyczący [limitów platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

    - **Skojarz zasób z nową konfiguracją adresu IP**
    
        Za każdym razem, gdy dodasz publiczny adres IP w nowej konfiguracji protokołu IP, musisz również dodać prywatny adres IP, ponieważ wszystkie konfiguracje IP muszą mieć prywatny adres IP. Możesz dodać istniejący zasób publicznego adresu IP lub utworzyć nowy. Aby utworzyć nowy, wprowadź następujące polecenie:
    
        ```azurecli
        az network public-ip create \
        --resource-group myResourceGroup \
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3
        ```

        Aby utworzyć nową konfigurację adresu IP ze statycznym prywatnym adresem IP i skojarzonym z nim zasobem publicznego adresu IP *myPublicIP3* , wprowadź następujące polecenie:

        ```azurecli
        az network nic ip-config create \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-5 \
        --private-ip-address 10.0.0.8
        --public-ip-address myPublicIP3
        ```

    - **Skojarz zasób z istniejącą konfiguracją adresu IP** Zasób publicznego adresu IP może być skojarzony tylko z konfiguracją IP, która nie ma już skojarzonej. Aby określić, czy konfiguracja adresu IP ma skojarzony publiczny adres IP, wprowadź następujące polecenie:

        ```azurecli
        az network nic ip-config list \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --query "[?provisioningState=='Succeeded'].{ Name: name, PublicIpAddressId: publicIpAddress.id }" --output table
        ```

        Zwrócone dane wyjściowe:
    
        ```output
        Name        PublicIpAddressId
        
        ipconfig1   /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
        IPConfig-2  /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
        IPConfig-3
        ```

        Ponieważ kolumna **PublicIpAddressId** dla elementu *IpConfig-3* jest pusta w danych wyjściowych, nie jest obecnie skojarzony żaden zasób publicznego adresu IP. Możesz dodać istniejący zasób publicznego adresu IP do IpConfig-3 lub wprowadź następujące polecenie, aby je utworzyć:

        ```azurecli
        az network public-ip create \
        --resource-group  myResourceGroup
        --location westcentralus \
        --name myPublicIP3 \
        --dns-name mypublicdns3 \
        --allocation-method Static
        ```
    
        Wprowadź następujące polecenie, aby skojarzyć zasób publicznego adresu IP z istniejącą konfiguracją IP o nazwie *ipconfig-3*:
    
        ```azurecli
        az network nic ip-config update \
        --resource-group myResourceGroup \
        --nic-name myNic1 \
        --name IPConfig-3 \
        --public-ip myPublicIP3
        ```

3. Wyświetl prywatne adresy IP i identyfikatory zasobów publicznego adresu IP przypisane do karty sieciowej, wprowadzając następujące polecenie:

    ```azurecli
    az network nic ip-config list \
    --resource-group myResourceGroup \
    --nic-name myNic1 \
    --query "[?provisioningState=='Succeeded'].{ Name: name, PrivateIpAddress: privateIpAddress, PrivateIpAllocationMethod: privateIpAllocationMethod, PublicIpAddressId: publicIpAddress.id }" --output table
    ```

    Zwrócone dane wyjściowe: <br>

    ```output
    Name        PrivateIpAddress    PrivateIpAllocationMethod   PublicIpAddressId
    
    ipconfig1   10.0.0.4            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP1
    IPConfig-2  10.0.0.5            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP2
    IPConfig-3  10.0.0.6            Static                      /subscriptions/[Id]/resourceGroups/myResourceGroup/providers/Microsoft.Network/publicIPAddresses/myPublicIP3
    ```
    
4. Dodaj prywatne adresy IP dodane do karty sieciowej do systemu operacyjnego maszyny wirtualnej, postępując zgodnie z instrukcjami w sekcji [Dodawanie adresów IP do systemu operacyjnego maszyny wirtualnej](#os-config) w tym artykule. Nie należy dodawać publicznych adresów IP do systemu operacyjnego.

[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
