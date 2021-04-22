---
title: Konfigurowanie statycznego wychodzącego adresu IP
description: Konfigurowanie zapory platformy Azure i tras zdefiniowanych przez użytkownika dla Azure Container Instances, które używają publicznego adresu IP zapory na potrzeby ruchu wychodzącego i ruchu wychodzącego
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: a03c59652b9409d54bbe63c63a31fdd2228ac34e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878690"
---
# <a name="configure-a-single-public-ip-address-for-outbound-and-inbound-traffic-to-a-container-group"></a>Konfigurowanie pojedynczego publicznego adresu IP dla ruchu wychodzącego i przychodzącego do grupy kontenerów

Skonfigurowanie grupy [kontenerów](container-instances-container-groups.md) przy użyciu zewnętrznego adresu IP umożliwia klientom zewnętrznym użycie adresu IP w celu uzyskania dostępu do kontenera w grupie. Na przykład przeglądarka może uzyskać dostęp do aplikacji internetowej uruchomionej w kontenerze. Jednak obecnie grupa kontenerów używa innego adresu IP dla ruchu wychodzącego. Ten adres IP ruchu wychodzącego nie jest ujmowany programowo, co sprawia, że monitorowanie i konfiguracja grup kontenerów reguł zapory klienta jest bardziej złożona.

Ten artykuł zawiera instrukcje konfigurowania grupy kontenerów w sieci [wirtualnej](container-instances-virtual-network-concepts.md) zintegrowanej z Azure Firewall [.](../firewall/overview.md) Przez skonfigurowanie trasy zdefiniowanej przez użytkownika do grupy kontenerów i reguł zapory można rozsyłać i identyfikować ruch do i z grupy kontenerów. Ruch przychodzący i wychodzący grupy kontenerów używają publicznego adresu IP zapory. Jeden adres IP ruchu wychodzącego może być używany przez wiele grup kontenerów wdrożonych w podsieci sieci wirtualnej delegowanych do Azure Container Instances.

W tym artykule utworzysz zasoby dla tego scenariusza przy użyciu interfejsu wiersza polecenia platformy Azure:

* Grupy kontenerów wdrożone w podsieci delegowane [w sieci wirtualnej](container-instances-vnet.md) 
* Zapora platformy Azure wdrożona w sieci ze statycznym publicznym adresem IP
* Trasa zdefiniowana przez użytkownika w podsieci grup kontenerów
* Reguła NAT dla reguły reguły zapory dla danych przychodzących i reguła aplikacji dla danych wychodzącego

Następnie zweryfikuje się ruch przychodzący i wychodzący z przykładowych grup kontenerów przez zaporę.

## <a name="deploy-aci-in-a-virtual-network"></a>Wdrażanie usługi ACI w sieci wirtualnej

W typowym przypadku być może masz już sieć wirtualną platformy Azure, w której ma zostać wdrożona grupa kontenerów. W celach demonstracyjnych następujące polecenia tworzą sieć wirtualną i podsieć podczas tworzenia grupy kontenerów. Podsieć jest delegowana do Azure Container Instances. 

Grupa kontenerów uruchamia małą aplikację internetową na `aci-helloworld` obrazie. Jak pokazano w innych artykułach w dokumentacji, ten obraz pakuje małą aplikację internetową napisaną w Node.js, która obsługuje statyczną stronę HTML.

Jeśli potrzebujesz tej grupy, najpierw utwórz grupę zasobów platformy Azure za pomocą [polecenia az group create.][az-group-create] Na przykład:

```azurecli
az group create --name myResourceGroup --location eastus
```

Aby uprościć poniższe przykłady poleceń, użyj zmiennej środowiskowej dla nazwy grupy zasobów:

```console
export RESOURCE_GROUP_NAME=myResourceGroup
```

Utwórz grupę kontenerów za pomocą [polecenia az container create:][az-container-create]

```azurecli
az container create \
  --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet aci-vnet \
  --vnet-address-prefix 10.0.0.0/16 \
  --subnet aci-subnet \
  --subnet-address-prefix 10.0.0.0/24
```

> [!TIP]
> Dostosuj wartość `--subnet address-prefix` dla przestrzeni adresów IP potrzebnej w podsieci. Najmniejsza obsługiwana podsieć to /29, która zapewnia osiem adresów IP. Niektóre adresy IP są zarezerwowane do użytku przez platformę Azure.

Aby użyć go w późniejszym kroku, uzyskaj prywatny adres IP grupy kontenerów, uruchamiając polecenie [az container show][az-container-show]:

```azurecli
ACI_PRIVATE_IP="$(az container show --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress.ip --output tsv)"
```

## <a name="deploy-azure-firewall-in-network"></a>Wdrażanie Azure Firewall w sieci

W poniższych sekcjach użyj interfejsu wiersza polecenia platformy Azure, aby wdrożyć zaporę platformy Azure w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Samouczek: wdrażanie i konfigurowanie Azure Firewall użyciu Azure Portal](../firewall/deploy-cli.md).

Najpierw użyj narzędzia [az network vnet subnet create,][az-network-vnet-subnet-create] aby dodać podsieć o nazwie AzureFirewallSubnet dla zapory. AzureFirewallSubnet jest *wymaganą* nazwą tej podsieci.

```azurecli
az network vnet subnet create \
  --name AzureFirewallSubnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet   \
  --address-prefix 10.0.1.0/26
```

Użyj następujących poleceń [interfejsu wiersza polecenia platformy Azure,](../firewall/deploy-cli.md) aby utworzyć zaporę w podsieci.

Jeśli nie zostało jeszcze zainstalowane, dodaj rozszerzenie zapory do interfejsu wiersza polecenia platformy Azure przy użyciu [polecenia az extension add:][az-extension-add]

```azurecli
az extension add --name azure-firewall
```

Utwórz zasoby zapory:

```azurecli
az network firewall create \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus

az network public-ip create \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --allocation-method static \
  --sku standard
    
az network firewall ip-config create \
  --firewall-name myFirewall \
  --name FW-config \
  --public-ip-address fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet
```

Zaktualizuj konfigurację zapory za pomocą [polecenia az network firewall update:][az-network-firewall-update]

```azurecli
az network firewall update \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME
```

Uzyskaj prywatny adres IP zapory za pomocą polecenia [az network firewall ip-config list.][az-network-firewall-ip-config-list] Ten prywatny adres IP jest używany w późniejszym poleceniu.


```azurecli
FW_PRIVATE_IP="$(az network firewall ip-config list \
  --resource-group $RESOURCE_GROUP_NAME \
  --firewall-name myFirewall \
  --query "[].privateIpAddress" --output tsv)"
```
Uzyskaj publiczny adres IP zapory za pomocą polecenia [az network public-ip show.][az-network-public-ip-show] Ten publiczny adres IP jest używany w późniejszym poleceniu.

```azurecli
FW_PUBLIC_IP="$(az network public-ip show \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress --output tsv)"
```

## <a name="define-user-defined-route-on-aci-subnet"></a>Definiowanie trasy zdefiniowanej przez użytkownika w podsieci usługi ACI

Zdefiniuj trasę zdefiniowaną przez użycie w podsieci ACI, aby skierować ruch do zapory platformy Azure. Aby uzyskać więcej informacji, zobacz Route network traffic ( [Przekieruj ruch sieciowy).](../virtual-network/tutorial-create-route-table-cli.md) 

### <a name="create-route-table"></a>Tworzenie tabeli tras

Najpierw uruchom następujące polecenie [az network route-table create,][az-network-route-table-create] aby utworzyć tabelę tras. Utwórz tabelę tras w tym samym regionie co sieć wirtualna.

```azurecli
az network route-table create \
  --name Firewall-rt-table \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --disable-bgp-route-propagation true
```

### <a name="create-route"></a>Tworzenie trasy

Uruchom [az network-route-table route create,][az-network-route-table-route-create] aby utworzyć trasę w tabeli tras. Aby przekierowyć ruch do zapory, ustaw typ następnego przeskoku na wartość i przekaż prywatny adres IP zapory `VirtualAppliance` jako adres następnego przeskoku.

```azurecli
az network route-table route create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $FW_PRIVATE_IP
```

### <a name="associate-route-table-to-aci-subnet"></a>Kojarzenie tabeli tras z podsiecią usługi ACI

Uruchom polecenie [az network vnet subnet update,][az-network-vnet-subnet-update] aby skojarzyć tabelę tras z podsiecią delegowaną do Azure Container Instances.

```azurecli
az network vnet subnet update \
  --name aci-subnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet \
  --address-prefixes 10.0.0.0/24 \
  --route-table Firewall-rt-table
```

## <a name="configure-rules-on-firewall"></a>Konfigurowanie reguł w zaporze

Domyślnie program Azure Firewall (bloki) ruchu przychodzącego i wychodzącego. 

### <a name="configure-nat-rule-on-firewall-to-aci-subnet"></a>Konfigurowanie reguły NAT w zaporze dla podsieci ACI

Utwórz [regułę NAT na](../firewall/rule-processing.md) zaporze, aby przetłumaczyć i przefiltrować przychodzący ruch internetowy do kontenera aplikacji, który był wcześniej uruchomiony w sieci. Aby uzyskać szczegółowe informacje, [zobacz Filtrowanie przychodzącego ruchu internetowego za pomocą Azure Firewall DNAT](../firewall/tutorial-firewall-dnat.md)

Utwórz regułę i kolekcję naT za pomocą [polecenia az network firewall nat-rule create:][az-network-firewall-nat-rule-create]

```azurecli
az network firewall nat-rule create \
  --firewall-name myFirewall \
  --collection-name myNATCollection \
  --action dnat \
  --name myRule \
  --protocols TCP \
  --source-addresses '*' \
  --destination-addresses $FW_PUBLIC_IP \
  --destination-ports 80 \
  --resource-group $RESOURCE_GROUP_NAME \
  --translated-address $ACI_PRIVATE_IP \
  --translated-port 80 \
  --priority 200
```

Dodaj reguły NAT zgodnie z potrzebami, aby filtrować ruch do innych adresów IP w podsieci. Na przykład inne grupy kontenerów w podsieci mogą uwidaczniać adresy IP dla ruchu przychodzącego lub inne wewnętrzne adresy IP można przypisać do grupy kontenerów po ponownym uruchomieniu.

### <a name="create-outbound-application-rule-on-the-firewall"></a>Tworzenie reguły aplikacji dla ruchu wychodzącego w zaporze

Uruchom następujące polecenie [az network firewall application-rule create,][az-network-firewall-application-rule-create] aby utworzyć regułę ruchu wychodzącego na zaporze. Ta przykładowa reguła zezwala na dostęp z podsieci delegowane do Azure Container Instances do `checkip.dyndns.org` FQDN. Dostęp HTTP do witryny zostanie użyty w późniejszym kroku w celu potwierdzenia adresu IP wychodzącego z Azure Container Instances.

```azurecli
az network firewall application-rule create \
  --collection-name myAppCollection \
  --firewall-name myFirewall \
  --name Allow-CheckIP \
  --protocols Http=80 Https=443 \
  --resource-group $RESOURCE_GROUP_NAME \
  --target-fqdns checkip.dyndns.org \
  --source-addresses 10.0.0.0/24 \
  --priority 200 \
  --action Allow
```

## <a name="test-container-group-access-through-the-firewall"></a>Testowanie dostępu do grupy kontenerów za pośrednictwem zapory

Poniższe sekcje sprawdzają, czy podsieć delegowana do Azure Container Instances jest prawidłowo skonfigurowana za zaporą platformy Azure. W poprzednich krokach ruch przychodzący do podsieci i ruch wychodzący z podsieci był przekierowyny przez zaporę.

### <a name="test-ingress-to-a-container-group"></a>Testowanie danych przychodzących do grupy kontenerów

Przetestuj dostęp przychodzący do *aplikacjikontainer* uruchomionej w sieci wirtualnej, przeglądając publiczny adres IP zapory. Wcześniej publiczny adres IP był przechowywany w zmiennej $FW_PUBLIC_IP:

```bash
echo $FW_PUBLIC_IP
```

Dane wyjściowe są podobne do następujących:

```console
52.142.18.133
```

Jeśli reguła NAT na zaporze jest prawidłowo skonfigurowana, po wprowadzeniu publicznego adresu IP zapory w przeglądarce zobaczysz następujące informacje:

:::image type="content" source="media/container-instances-egress-ip-address/aci-ingress-ip-address.png" alt-text="Przejdź do publicznego adresu IP zapory":::

### <a name="test-egress-from-a-container-group"></a>Testowanie danych wychodzących z grupy kontenerów


Wd wdrożenie następującego przykładowego kontenera w sieci wirtualnej. Po jego uruchamianiu wysyła jedno żądanie HTTP do usługi , która wyświetla adres IP nadawcy `http://checkip.dyndns.org` (adres IP wychodzący). Jeśli reguła aplikacji na zaporze jest prawidłowo skonfigurowana, zwracany jest publiczny adres IP zapory.

```azurecli
az container create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress \
  --image mcr.microsoft.com/azuredocs/aci-tutorial-sidecar \
  --command-line "curl -s http://checkip.dyndns.org" \
  --restart-policy OnFailure \
  --vnet aci-vnet \
  --subnet aci-subnet
```

Wyświetl dzienniki kontenera, aby potwierdzić, że adres IP jest taki sam jak publiczny adres IP zapory.

```azurecli
az container logs \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress 
```

Dane wyjściowe są podobne do następujących:

```console
<html><head><title>Current IP Check</title></head><body>Current IP Address: 52.142.18.133</body></html>
```

## <a name="next-steps"></a>Następne kroki

W tym artykule skonfigurujemy grupy kontenerów w sieci wirtualnej za zaporą platformy Azure. Skonfigurowano trasę zdefiniowaną przez użytkownika oraz reguły NAT i aplikacji w zaporze. Korzystając z tej konfiguracji, można skonfigurować jeden statyczny adres IP dla danych przychodzących i wychodzących z Azure Container Instances.

Aby uzyskać więcej informacji na temat zarządzania [](../firewall/index.yml) ruchem i ochrony zasobów platformy Azure, zobacz Azure Firewall dokumentacji.



[az-group-create]: /cli/azure/group#az_group_create
[az-container-create]: /cli/azure/container#az_container_create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-network-firewall-update]: /cli/azure/network/firewall#az_network_firewall_update
[az-network-public-ip-show]: /cli/azure/network/public-ip/#az_network_public_ip_show
[az-network-route-table-create]:/cli/azure/network/route-table/#az_network_route_table_create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az_network_route_table_route_create
[az-network-firewall-ip-config-list]: /cli/azure/network/firewall/ip-config#az_network_firewall_ip_config_list
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_update
[az-container-exec]: /cli/azure/container#az_container_exec
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-vm-open-port]: /cli/azure/vm#az_vm_open_port
[az-vm-list-ip-addresses]: /cli/azure/vm#az_vm_list_ip_addresses
[az-network-firewall-application-rule-create]: /cli/azure/network/firewall/application-rule#az_network_firewall_application_rule_create
[az-network-firewall-nat-rule-create]: /cli/azure/network/firewall/nat-rule#az_network_firewall_nat_rule_create
