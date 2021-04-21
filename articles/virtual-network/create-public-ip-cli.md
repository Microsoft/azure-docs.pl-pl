---
title: Tworzenie publicznego adresu IP — interfejs wiersza polecenia platformy Azure
description: Dowiedz się, jak utworzyć publiczny adres IP przy użyciu interfejsu wiersza polecenia platformy Azure
services: virtual-network
documentationcenter: na
author: blehr
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/28/2020
ms.author: blehr
ms.openlocfilehash: ff0dbf31f6f428b23e00f9366d55703416847b90
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767695"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-cli"></a>Szybki start: tworzenie publicznego adresu IP przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule pokazano, jak utworzyć zasób publicznego adresu IP przy użyciu interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji na temat zasobów, z którymi można to skojarzyć, różnicy między podstawową i standardową bazą danych SKU oraz innych powiązanych informacji, zobacz [Publiczne adresy IP](./public-ip-addresses.md).  W tym przykładzie skupimy się tylko na adresach IPv4; Aby uzyskać więcej informacji na temat adresów IPv6, zobacz [IPv6 for Azure VNet (Protokół IPv6 dla sieci wirtualnej platformy Azure).](./ipv6-overview.md)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą [az group create](/cli/azure/group#az_group_create) o nazwie **myResourceGroup** w **lokalizacji eastus2.**

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```

## <a name="create-public-ip"></a>Tworzenie publicznego adresu IP

---
# <a name="standard-sku---using-zones"></a>[**Standardowa sku — używanie stref**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>Następujące polecenie działa w przypadku interfejsu API w wersji 2020-08-01 lub nowszej.  Aby uzyskać więcej informacji na temat aktualnie używanej wersji interfejsu API, zapoznaj się z [tematem Resource Providers and Types (Dostawcy zasobów i typy).](../azure-resource-manager/management/resource-providers-and-types.md)

Użyj [az network public-ip create,](/cli/azure/network/public-ip#az_network_public_ip_create) aby utworzyć standardowy strefowo nadmiarowy publiczny adres IP o nazwie **myStandardZRPublicIP** w **grupie myResourceGroup.**

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --sku Standard
    --zone 1 2 3
```
> [!IMPORTANT]
> W przypadku wersji interfejsu API starszych niż 2020-08-01 uruchom powyższe polecenie bez określania parametru strefy, aby utworzyć strefowo nadmiarowy adres IP. 
>

Aby utworzyć standardowy strefowy publiczny adres IP w usłudze Strefa 2 o nazwie **myStandardZonalPublicIP** w grupie **zasobów myResourceGroup,** użyj następującego polecenia:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --sku Standard \
    --zone 2
```

Należy pamiętać, że powyższe opcje dla stref są prawidłowymi wyborami tylko w regionach z Strefy dostępności [.](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones)

# <a name="standard-sku---no-zones"></a>[**Standardowa SKU — brak stref**](#tab/option-create-public-ip-standard)

>[!NOTE]
>Następujące polecenie działa w przypadku interfejsu API w wersji 2020-08-01 lub nowszej.  Aby uzyskać więcej informacji na temat aktualnie używanej wersji interfejsu API, zapoznaj się z [tematem Resource Providers and Types (Dostawcy zasobów i typy).](../azure-resource-manager/management/resource-providers-and-types.md)

Użyj [az network public-ip create,](/cli/azure/network/public-ip#az_network_public_ip_create) aby utworzyć standardowy publiczny adres IP jako zasób nieosobowy o nazwie **myStandardPublicIP** w **grupie myResourceGroup.**

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --sku Standard
```
Ten wybór jest prawidłowy we wszystkich regionach i jest domyślnym wyborem dla publicznych adresów IP w standardowych regionach bez [Strefy dostępności](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#availability-zones).

# <a name="basic-sku"></a>[**Podstawowy SKU**](#tab/option-create-public-ip-basic)

Użyj [az network public-ip create,](/cli/azure/network/public-ip#az_network_public_ip_create) aby utworzyć podstawowy statyczny publiczny adres IP o nazwie **myBasicPublicIP** w **grupie myResourceGroup.**  Podstawowe publiczne ip nie mają koncepcji stref dostępności.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Basic \
    --allocation-method Static
```
Jeśli adres IP może zmieniać się w **czasie,** dynamiczne przypisanie adresu IP można wybrać, zmieniając metodę allocation-method na "Dynamic".

---

## <a name="additional-information"></a>Dodatkowe informacje 

Aby uzyskać więcej informacji na temat poszczególnych zmiennych wymienionych powyżej, zobacz [Manage public IP addresses (Zarządzanie publicznymi adresami IP).](./virtual-network-public-ip-address.md#create-a-public-ip-address)

## <a name="next-steps"></a>Następne kroki
- [Skojarz publiczny adres IP z maszyną wirtualną.](./associate-public-ip-address-vm.md#azure-portal)
- Dowiedz się więcej o [publicznych adresach IP na](./public-ip-addresses.md#public-ip-addresses) platformie Azure.
- Dowiedz się więcej o wszystkich [ustawieniach publicznego adresu IP.](virtual-network-public-ip-address.md#create-a-public-ip-address)
