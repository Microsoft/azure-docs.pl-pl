---
title: Konfigurowanie statycznego wychodzącego adresu IP
description: Skonfiguruj zaporę platformy Azure i trasy zdefiniowane przez użytkownika dla obciążeń Azure Container Instances, które używają publicznego adresu IP zapory na potrzeby ruchu przychodzącego i wychodzącego
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: 497645b9fe7f908cc9b8b4d7ed0ba5e201570160
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "89566572"
---
# <a name="configure-a-single-public-ip-address-for-outbound-and-inbound-traffic-to-a-container-group"></a>Skonfiguruj pojedynczy publiczny adres IP dla ruchu wychodzącego i przychodzącego do grupy kontenerów

Skonfigurowanie [grupy kontenerów](container-instances-container-groups.md) z zewnętrznym adresem IP umożliwia klientom zewnętrznym korzystanie z adresu IP w celu uzyskania dostępu do kontenera w grupie. Na przykład przeglądarka może uzyskać dostęp do aplikacji sieci Web działającej w kontenerze. Jednak obecnie Grupa kontenerów używa innego adresu IP dla ruchu wychodzącego. Ten adres IP ruchu wychodzącego nie jest programowo narażony, co sprawia, że monitorowanie i konfigurowanie reguł zapory klienta jest bardziej skomplikowane.

Ten artykuł zawiera instrukcje dotyczące konfigurowania grupy kontenerów w [sieci wirtualnej](container-instances-virtual-network-concepts.md) zintegrowanej z [zaporą platformy Azure](../firewall/overview.md). Przez skonfigurowanie zdefiniowanej przez użytkownika trasy do grupy kontenerów i reguł zapory, można kierować i identyfikować ruch do i z grupy kontenerów. Przychodzący i wychodzący grupy kontenerów używają publicznego adresu IP zapory. Pojedynczy adres IP ruchu wychodzącego może być używany przez wiele grup kontenerów wdrożonych w podsieci sieci wirtualnej delegowanej do Azure Container Instances.

W tym artykule opisano tworzenie zasobów dla tego scenariusza przy użyciu interfejsu wiersza polecenia platformy Azure:

* Grupy kontenerów wdrożone w delegowanej podsieci [w sieci wirtualnej](container-instances-vnet.md) 
* Zapora platformy Azure wdrożona w sieci ze statycznym publicznym adresem IP
* Zdefiniowana przez użytkownika trasa w podsieci grupy kontenerów
* Reguła NAT dotycząca komunikacji przychodzącej i reguły aplikacji dla ruchu wychodzącego

Następnie można sprawdzić poprawność ruchu przychodzącego i wychodzącego z przykładowych grup kontenerów za pośrednictwem zapory.

## <a name="deploy-aci-in-a-virtual-network"></a>Wdrażanie ACI w sieci wirtualnej

W typowym przypadku może już istnieć Sieć wirtualna platformy Azure, w której ma zostać wdrożona Grupa kontenerów. W celach demonstracyjnych następujące polecenia tworzą sieć wirtualną i podsieć podczas tworzenia grupy kontenerów. Podsieć jest delegowana do Azure Container Instances. 

Grupa kontenerów uruchamia małą aplikację internetową z `aci-helloworld` obrazu. Jak pokazano w innych artykułach w dokumentacji, ten obraz zawiera niewielką aplikację sieci Web zapisaną w Node.js, która obsługuje statyczną stronę HTML.

W razie potrzeby należy najpierw utworzyć grupę zasobów platformy Azure za pomocą polecenia [AZ Group Create][az-group-create] . Na przykład:

```azurecli
az group create --name myResourceGroup --location eastus
```

Aby uprościć następujące przykłady poleceń, użyj zmiennej środowiskowej dla nazwy grupy zasobów:

```console
export RESOURCE_GROUP_NAME=myResourceGroup
```

Utwórz grupę kontenerów za pomocą polecenia [AZ Container Create][az-container-create] :

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
> Dostosuj wartość w `--subnet address-prefix` polu przestrzeń adresów IP potrzebną w podsieci. Najmniejsza obsługiwana podsieć to/29, która zapewnia osiem adresów IP. Niektóre adresy IP są zarezerwowane do użytku przez platformę Azure.

Aby użyć w późniejszym kroku, uzyskaj prywatny adres IP grupy kontenerów, uruchamiając polecenie [AZ Container show] [AZ-Container-show]:

```azurecli
ACI_PRIVATE_IP="$(az container show --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress.ip --output tsv)"
```

## <a name="deploy-azure-firewall-in-network"></a>Wdróż zaporę platformy Azure w sieci

W poniższych sekcjach Użyj interfejsu wiersza polecenia platformy Azure, aby wdrożyć zaporę platformy Azure w sieci wirtualnej. Aby uzyskać ogólne instrukcje, zobacz [Samouczek: wdrażanie i Konfigurowanie zapory platformy Azure przy użyciu Azure Portal](../firewall/deploy-cli.md).

Najpierw użyj [AZ Network VNET Subnet Create][az-network-vnet-subnet-create] , aby dodać podsieć o nazwie AzureFirewallSubnet dla zapory. AzureFirewallSubnet jest *wymaganą* nazwą tej podsieci.

```azurecli
az network vnet subnet create \
  --name AzureFirewallSubnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet   \
  --address-prefix 10.0.1.0/26
```

Użyj następujących [poleceń interfejsu wiersza polecenia platformy Azure](../firewall/deploy-cli.md) , aby utworzyć zaporę w podsieci.

Jeśli jeszcze nie zainstalowano, Dodaj rozszerzenie zapory do interfejsu wiersza polecenia platformy Azure przy użyciu opcji [AZ Extension Add][az-extension-add] :

```azurecli
az extension add --name azure-firewall
```

Tworzenie zasobów zapory:

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

Zaktualizuj konfigurację zapory za pomocą polecenia [AZ Network firewall Update][az-network-firewall-update] :

```azurecli
az network firewall update \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME
```

Uzyskaj prywatny adres IP zapory za pomocą polecenia [AZ Network firewall IP-config list][az-network-firewall-ip-config-list] . Ten prywatny adres IP jest używany w późniejszym poleceniu.


```azurecli
FW_PRIVATE_IP="$(az network firewall ip-config list \
  --resource-group $RESOURCE_GROUP_NAME \
  --firewall-name myFirewall \
  --query "[].privateIpAddress" --output tsv)"
```
Uzyskaj publiczny adres IP zapory za pomocą polecenia [AZ Network Public-IP show][az-network-public-ip-show] . Ten publiczny adres IP jest używany w późniejszym poleceniu.

```azurecli
FW_PUBLIC_IP="$(az network public-ip show \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress --output tsv)"
```

## <a name="define-user-defined-route-on-aci-subnet"></a>Definiowanie trasy zdefiniowanej przez użytkownika w podsieci ACI

Zdefiniuj zdefiniowaną w podsieci ACI trasę do przekierowywania ruchu do zapory platformy Azure. Aby uzyskać więcej informacji, zobacz [kierowanie ruchu sieciowego](../virtual-network/tutorial-create-route-table-cli.md). 

### <a name="create-route-table"></a>Tworzenie tabeli tras

Najpierw uruchom następujące polecenie [AZ Network Route-Table Create][az-network-route-table-create] , aby utworzyć tabelę tras. Utwórz tabelę tras w tym samym regionie, w którym znajduje się sieć wirtualna.

```azurecli
az network route-table create \
  --name Firewall-rt-table \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --disable-bgp-route-propagation true
```

### <a name="create-route"></a>Utwórz trasę

Uruchom [AZ Network-Route-Table Route Create][az-network-route-table-route-create] w celu utworzenia trasy w tabeli tras. Aby skierować ruch do zapory, ustaw typ następnego przeskoku na `VirtualAppliance` i przekaż prywatny adres IP zapory jako adres następnego przeskoku.

```azurecli
az network route-table route create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $FW_PRIVATE_IP
```

### <a name="associate-route-table-to-aci-subnet"></a>Skojarz tabelę tras z podsiecią ACI

Uruchom polecenie [AZ Network VNET Subnet Update][az-network-vnet-subnet-update] , aby skojarzyć tabelę tras z podsiecią delegowaną do Azure Container Instances.

```azurecli
az network vnet subnet update \
  --name aci-subnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet \
  --address-prefixes 10.0.0.0/24 \
  --route-table Firewall-rt-table
```

## <a name="configure-rules-on-firewall"></a>Konfigurowanie reguł zapory

Domyślnie Zapora platformy Azure nie odmówi (blokuje) ruchu przychodzącego i wychodzącego. 

### <a name="configure-nat-rule-on-firewall-to-aci-subnet"></a>Konfigurowanie reguły NAT na zaporze w podsieci ACI

Utwórz [regułę NAT](../firewall/rule-processing.md) na zaporze, aby przetłumaczyć i filtrować przychodzący ruch internetowy do kontenera aplikacji uruchomionego wcześniej w sieci. Aby uzyskać szczegółowe informacje, zobacz [filtrowanie przychodzącego ruchu internetowego za pomocą zapory platformy Azure DNAT](../firewall/tutorial-firewall-dnat.md)

Tworzenie reguły NAT i kolekcji za pomocą polecenia [AZ Network firewall NAT-Rule Create][az-network-firewall-nat-rule-create] :

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

Dodaj reguły NAT zgodnie z wymaganiami, aby filtrować ruch do innych adresów IP w podsieci. Na przykład inne grupy kontenerów w podsieci mogą uwidaczniać adresy IP dla ruchu przychodzącego lub inne wewnętrzne adresy IP mogą być przypisane do grupy kontenerów po ponownym uruchomieniu.

### <a name="create-outbound-application-rule-on-the-firewall"></a>Tworzenie reguły aplikacji wychodzącej na zaporze

Uruchom następujące polecenie [AZ Network firewall Application-Rule Create][az-network-firewall-application-rule-create] , aby utworzyć regułę ruchu wychodzącego w zaporze. Ta przykładowa reguła umożliwia dostęp z podsieci delegowanej do Azure Container Instances nazwy FQDN `checkip.dyndns.org` . Dostęp za pośrednictwem protokołu HTTP do witryny jest używany w późniejszym kroku, aby potwierdzić adres IP ruchu wychodzącego z Azure Container Instances.

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

## <a name="test-container-group-access-through-the-firewall"></a>Testowanie dostępu do grupy kontenerów za pomocą zapory

Poniższe sekcje sprawdzają, czy podsieć delegowana do Azure Container Instances została prawidłowo skonfigurowana za zaporą platformy Azure. Poprzednie kroki skierowały ruch przychodzący do podsieci i ruch wychodzący z podsieci przez zaporę.

### <a name="test-ingress-to-a-container-group"></a>Testowanie ruchu przychodzącego do grupy kontenerów

Przetestuj dostęp przychodzący do *kontenera aplikacji* działającego w sieci wirtualnej, przechodząc do publicznego adresu IP zapory. Wcześniej publiczny adres IP został zapisany w zmiennej $FW _PUBLIC_IP:

```bash
echo $FW_PUBLIC_IP
```

Dane wyjściowe są podobne do następujących:

```console
52.142.18.133
```

Jeśli reguła NAT w zaporze jest skonfigurowana prawidłowo, podczas wprowadzania publicznego adresu IP zapory w przeglądarce zostanie wyświetlony następujący tekst:

:::image type="content" source="media/container-instances-egress-ip-address/aci-ingress-ip-address.png" alt-text="Przejdź do publicznego adresu IP zapory":::

### <a name="test-egress-from-a-container-group"></a>Testowanie ruchu wychodzącego z grupy kontenerów


Wdróż następujący przykładowy kontener w sieci wirtualnej. Po uruchomieniu wysyła ono pojedyncze żądanie HTTP do `http://checkip.dyndns.org` , który wyświetla adres IP nadawcy (adres IP ruchu wychodzącego). Jeśli reguła aplikacji w zaporze jest skonfigurowana prawidłowo, zostaje zwrócony publiczny adres IP zapory.

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

Wyświetl dzienniki kontenerów, aby potwierdzić, że adres IP jest taki sam jak publiczny adres IP zapory.

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

W tym artykule opisano konfigurowanie grup kontenerów w sieci wirtualnej za zaporą platformy Azure. Skonfigurowano w zaporze zdefiniowaną przez użytkownika trasę i reguły dotyczące translatora adresów sieciowych oraz aplikacji. Za pomocą tej konfiguracji należy skonfigurować jeden statyczny adres IP dla ruchu przychodzącego i wychodzącego z Azure Container Instances.

Aby uzyskać więcej informacji o zarządzaniu ruchem i ochronie zasobów platformy Azure, zobacz dokumentację dotyczącą [zapory platformy Azure](../firewall/index.yml) .



[az-group-create]: /cli/azure/group#az-group-create
[az-container-create]: /cli/azure/container#az-container-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-update]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-update
[az-network-public-ip-show]: /cli/azure/network/public-ip/#az-network-public-ip-show
[az-network-route-table-create]:/cli/azure/network/route-table/#az-network-route-table-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[az-network-firewall-ip-config-list]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-list
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-update
[az-container-exec]: /cli/azure/container#az-container-exec
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-open-port]: /cli/azure/vm#az-vm-open-port
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[az-network-firewall-application-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/application-rule#ext-azure-firewall-az-network-firewall-application-rule-create
[az-network-firewall-nat-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/nat-rule#ext-azure-firewall-az-network-firewall-nat-rule-create






