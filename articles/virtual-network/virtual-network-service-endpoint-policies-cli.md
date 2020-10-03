---
title: Ograniczanie eksfiltracji danych do usługi Azure Storage — interfejs wiersza polecenia platformy Azure
description: W tym artykule dowiesz się, jak ograniczyć i ograniczyć eksfiltracji danych sieci wirtualnej do zasobów usługi Azure Storage za pomocą zasad punktu końcowego usługi sieci wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: virtual-network
author: rdhillon
manager: ''
editor: ''
tags: azure-resource-manager
Customer intent: I want only specific Azure Storage account to be allowed access from a virtual network subnet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: 53f10996a7f15e32261f151600163f41df4e58ca
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91666793"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-the-azure-cli"></a>Zarządzanie eksfiltracjiami danych na kontach usługi Azure Storage za pomocą zasad punktu końcowego usług sieci wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure

Zasady punktu końcowego usługi sieci wirtualnej umożliwiają stosowanie kontroli dostępu do kont usługi Azure Storage z poziomu sieci wirtualnej za pośrednictwem punktów końcowych usługi. Jest to klucz służący do zabezpieczania obciążeń, zarządzania tym, jakie konta magazynu są dozwolone i gdzie eksfiltracji danych jest dozwolony.
W tym artykule omówiono sposób wykonywania następujących zadań:

* Utwórz sieć wirtualną i Dodaj podsieć.
* Włącz punkt końcowy usługi Azure Storage.
* Utwórz dwa konta usługi Azure Storage i zezwól na dostęp sieciowy do niego z podsieci utworzonej powyżej.
* Utwórz zasady punktu końcowego usługi, aby zezwolić na dostęp tylko do jednego z kont magazynu.
* Wdróż maszynę wirtualną w podsieci.
* Potwierdź dostęp do dozwolonego konta magazynu z podsieci.
* Upewnij się, że dostęp do konta magazynu niedozwolonego z podsieci zostanie odrzucony.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki start będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli). 

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Przed utworzeniem sieci wirtualnej należy utworzyć grupę zasobów dla sieci wirtualnej i wszystkie inne zasoby utworzone w tym artykule. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład obejmuje tworzenie grupy zasobów o nazwie *myResourceGroup* w lokalizacji *eastus*.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Utwórz sieć wirtualną z jedną podsiecią za pomocą [AZ Network VNET Create](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Private \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Włączanie punktu końcowego usługi 

W tym przykładzie jest tworzony punkt końcowy usługi *Microsoft. Storage* dla podsieci *Private*: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.0.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Ograniczanie dostępu sieciowego dla podsieci

Utwórz sieciową grupę zabezpieczeń za pomocą [AZ Network sieciowej grupy zabezpieczeń Create](/cli/azure/network/nsg). Poniższy przykład tworzy sieciową grupę zabezpieczeń o nazwie *myNsgPrivate*.

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Skojarz sieciową grupę zabezpieczeń z podsiecią *prywatną* przy użyciu [AZ Network VNET Subnet Update](/cli/azure/network/vnet/subnet). Poniższy przykład kojarzy sieciową grupę zabezpieczeń *myNsgPrivate* z podsiecią *prywatną* :

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Utwórz reguły zabezpieczeń za pomocą [AZ Network sieciowej grupy zabezpieczeń Rule Create](/cli/azure/network/nsg/rule). Reguła, która następuje po umożliwieniu dostępu wychodzącego do publicznych adresów IP przypisanych do usługi Azure Storage: 

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-Storage-All \
  --access Allow \
  --protocol "*" \
  --direction Outbound \
  --priority 100 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Storage" \
  --destination-port-range "*"
```

Każda sieciowa Grupa zabezpieczeń zawiera kilka [domyślnych reguł zabezpieczeń](security-overview.md#default-security-rules). Reguła, która następuje, zastępuje domyślną regułę zabezpieczeń, która zezwala na dostęp wychodzący do wszystkich publicznych adresów IP. `destination-address-prefix "Internet"`Opcja powoduje odmowę dostępu wychodzącego do wszystkich publicznych adresów IP. Poprzednia reguła zastępuje tę regułę z powodu wyższego priorytetu, co umożliwia dostęp do publicznych adresów IP usługi Azure Storage.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Deny-Internet-All \
  --access Deny \
  --protocol "*" \
  --direction Outbound \
  --priority 110 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Internet" \
  --destination-port-range "*"
```

Poniższa reguła umożliwia ruch SSH ruchu przychodzącego do podsieci z dowolnego miejsca. Reguła zastępuje domyślną regułę zabezpieczeń, która zakazuje całego ruchu przychodzącego z Internetu. Protokół SSH jest dozwolony w podsieci, aby można było przetestować łączność w późniejszym kroku.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 120 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "VirtualNetwork" \
  --destination-port-range "22"
```

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Ograniczanie dostępu sieciowego do kont usługi Azure Storage

W tej sekcji przedstawiono procedurę ograniczenia dostępu do sieci dla konta usługi Azure Storage z danej podsieci w sieci wirtualnej za pośrednictwem punktu końcowego usługi.

### <a name="create-a-storage-account"></a>Tworzenie konta magazynu

Utwórz dwa konta usługi Azure Storage za pomocą [AZ Storage account Create](/cli/azure/storage/account).

```azurecli-interactive
storageAcctName1="allowedstorageacc"

az storage account create \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2

storageAcctName2="notallowedstorageacc"

az storage account create \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Po utworzeniu kont magazynu Pobierz parametry połączenia dla kont magazynu do zmiennej za pomocą [AZ Storage account show-Connection-String](/cli/azure/storage/account). Parametry połączenia są używane do tworzenia udziału plików w późniejszym kroku.

```azurecli-interactive
saConnectionString1=$(az storage account show-connection-string \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)

saConnectionString2=$(az storage account show-connection-string \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Wyświetl zawartość zmiennej i zwróć uwagę na wartość **AccountKey** zwracaną w danych wyjściowych, ponieważ jest ona używana w późniejszym kroku.

```azurecli-interactive
echo $saConnectionString1

echo $saConnectionString2
```

### <a name="create-a-file-share-in-the-storage-account"></a>Tworzenie udziału plików w ramach konta magazynu

Utwórz udział plików na koncie magazynu za pomocą [AZ Storage Share Create](/cli/azure/storage/share). W późniejszym kroku ten udział plików jest instalowany w celu potwierdzenia dostępu do sieci.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString1 > /dev/null

az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString2 > /dev/null
```

### <a name="deny-all-network-access-to-the-storage-account"></a>Odmowa dostępu przez sieć do konta magazynu

Domyślnie konta magazynu akceptują połączenia sieciowe od klientów w dowolnej sieci. Aby ograniczyć dostęp do wybranych sieci, Zmień domyślną akcję na *Odmów* przy użyciu [AZ Storage account Update](/cli/azure/storage/account). Kiedy dostęp sieciowy jest blokowany, konto magazynu nie jest dostępne z żadnej sieci.

```azurecli-interactive
az storage account update \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --default-action Deny

az storage account update \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-virtual-network-subnet"></a>Włącz dostęp sieciowy z podsieci sieci wirtualnej

Zezwalaj na dostęp sieciowy do konta magazynu z podsieci *prywatnej* za pomocą [AZ Storage account Network-Rule Add](/cli/azure/storage/account/network-rule).

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName1 \
  --vnet-name myVirtualNetwork \
  --subnet Private

az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName2 \
  --vnet-name myVirtualNetwork \
  --subnet Private
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Zastosuj zasady, aby zezwolić na dostęp do prawidłowego konta magazynu

Zasady punktu końcowego usługi platformy Azure są dostępne tylko dla usługi Azure Storage. Dlatego będziemy włączać punkt końcowy usługi dla *Microsoft. Storage* w tej podsieci na potrzeby tego przykładowego Instalatora.

Zasady punktu końcowego usługi są stosowane za pośrednictwem punktów końcowych usługi. Zaczniemy od utworzenia zasad punktu końcowego usługi. Następnie utworzymy definicje zasad w ramach tych zasad dla kont usługi Azure Storage, które mają zostać zatwierdzone dla tej podsieci

Tworzenie zasad punktu końcowego usługi

```azurecli-interactive
az network service-endpoint policy create \
  --resource-group myResourceGroup \
  --name mysepolicy \
  --location eastus
```

Zapisz identyfikator URI zasobu dla dozwolonego konta magazynu w zmiennej. Przed wykonaniem poniższego polecenia Zastąp *\<your-subscription-id>* wartością rzeczywistą identyfikatora subskrypcji.

```azurecli-interactive
$serviceResourceId="/subscriptions/<your-subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/allowedstorageacc"
```

Utwórz & dodać definicję zasad umożliwiającą powyższenie konta usługi Azure Storage do zasad punktu końcowego usług

```azurecli-interactive
az network service-endpoint policy-definition create \
  --resource-group myResourceGroup \
  --policy-name mysepolicy \
  --name mypolicydefinition \
  --service "Microsoft.Storage" \
  --service-resources $serviceResourceId
```

I zaktualizuj podsieć sieci wirtualnej, która ma zostać skojarzona z nią zasadami punktu końcowego usługi utworzonymi w poprzednim kroku

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --service-endpoints Microsoft.Storage \
  --service-endpoint-policy mysepolicy
```

## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Sprawdzanie poprawności ograniczenia dostępu do kont usługi Azure Storage

### <a name="create-the-virtual-machine"></a>Tworzenie maszyny wirtualnej

Aby przetestować dostęp sieciowy do konta magazynu, Wdróż maszynę wirtualną w podsieci.

Utwórz maszynę wirtualną w podsieci *prywatnej* przy użyciu [AZ VM Create](/cli/azure/vm). Jeśli klucze SSH nie istnieją jeszcze w domyślnej lokalizacji kluczy, to polecenie je utworzy. Aby użyć określonego zestawu kluczy, użyj opcji `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

W ciągu kilku minut zostanie utworzona maszyna wirtualna. Po utworzeniu należy zanotować **publicIpAddress** w zwróconym danych wyjściowych. Ten adres jest używany w celu uzyskania dostępu do maszyny wirtualnej z Internetu w późniejszym kroku.

### <a name="confirm-access-to-storage-account"></a>Potwierdzanie dostępu do konta magazynu

Użyj protokołu SSH do maszyny wirtualnej *myVmPrivate* . Zastąp *\<publicIpAddress>* wartość publicznym adresem IP maszyny wirtualnej *myVmPrivate* .

```bash 
ssh <publicIpAddress>
```

Utwórz folder dla punktu instalacji:

```bash
sudo mkdir /mnt/MyAzureFileShare1
```

Zainstaluj udział plików platformy Azure w utworzonym katalogu. Przed wykonaniem poniższego polecenia Zastąp *\<storage-account-key>* wartość *AccountKey* z **$saConnectionString 1**.

```bash
sudo mount --types cifs //allowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare1 --options vers=3.0,username=allowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Zostanie wyświetlony `user@myVmPrivate:~$` monit. Udział plików platformy Azure został pomyślnie zainstalowany do */mnt/MyAzureFileShare*.

### <a name="confirm-access-is-denied-to-storage-account"></a>Potwierdzanie odmowy dostępu do konta magazynu

Na tej samej maszynie wirtualnej *myVmPrivate*Utwórz katalog dla punktu instalacji:

```bash
sudo mkdir /mnt/MyAzureFileShare2
```

Podjęto próbę zainstalowania udziału plików platformy Azure z konta magazynu *notallowedstorageacc* w utworzonym katalogu. W tym artykule przyjęto założenie, że wdrożono najnowszą wersję programu Ubuntu. Jeśli używasz wcześniejszych wersji programu Ubuntu, zobacz [Instalowanie w systemie Linux](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , aby uzyskać dodatkowe instrukcje dotyczące instalowania udziałów plików. 

Przed wykonaniem poniższego polecenia Zastąp *\<storage-account-key>* wartość *AccountKey* z **$saConnectionString 2**.

```bash
sudo mount --types cifs //notallowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare2 --options vers=3.0,username=notallowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Odmowa dostępu i pojawienie się `mount error(13): Permission denied` błędu, ponieważ to konto magazynu nie znajduje się na liście dozwolonych zasad punktu końcowego usługi, które zostały zastosowane do podsieci. 

Wyjdź z sesji SSH na maszynę wirtualną *myVmPublic* .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, użyj [AZ Group Delete](/cli/azure) , aby usunąć grupę zasobów i wszystkie zawarte w niej zasoby.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Następne kroki

W tym artykule zastosowano zasady punktu końcowego usługi za pośrednictwem punktu końcowego usługi sieci wirtualnej platformy Azure do usługi Azure Storage. Konta usługi Azure Storage zostały utworzone i ograniczono dostęp do sieci tylko do określonych kont magazynu (i w ten sposób odmówiono innym) z podsieci sieci wirtualnej. Aby dowiedzieć się więcej na temat zasad punktu końcowego usługi, zobacz [Omówienie zasad punktów końcowych usługi](virtual-network-service-endpoint-policies-overview.md).
