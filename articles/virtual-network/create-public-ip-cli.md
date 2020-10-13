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
ms.openlocfilehash: d868a2d9bc88be7faea161779c35110f13e2b2ac
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2020
ms.locfileid: "91939073"
---
# <a name="quickstart-create-a-public-ip-address-using-azure-cli"></a>Szybki Start: Tworzenie publicznego adresu IP przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule pokazano, jak utworzyć zasób publicznego adresu IP przy użyciu interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji na temat zasobów, z którymi może być skojarzone, różnica między podstawową i standardową jednostką SKU i innymi powiązanymi informacjami, zobacz [publiczne adresy IP](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses).  Na potrzeby tego przykładu będziemy skupić się tylko na adresach IPv4. Aby uzyskać więcej informacji na temat adresów IPv6, zobacz [IPv6 dla sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-network/ipv6-overview).

## <a name="prerequisites"></a>Wymagania wstępne

- Interfejs wiersza polecenia platformy Azure został zainstalowany lokalnie lub Azure Cloud Shell

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten przewodnik Szybki Start będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Aby dowiedzieć się, jaka wersja jest używana, uruchom polecenie `az --version`. Jeśli konieczna będzie instalacja lub uaktualnienie interfejsu, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą funkcji [AZ Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) o nazwie Moja **resourceName** w lokalizacji **eastus2** .

```azurecli-interactive
  az group create \
    --name myResourceGroup \
    --location eastus2
```
---
# <a name="standard-sku---using-zones"></a>[**Standardowa jednostka SKU — używanie stref**](#tab/option-create-public-ip-standard-zones)

>[!NOTE]
>Następujące polecenie działa w przypadku interfejsu API w wersji 2020-08-01 lub nowszej.  Aby uzyskać więcej informacji na temat aktualnie używanej wersji interfejsu API, zapoznaj się z [dostawcami zasobów i typami](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

Użyj [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) , aby utworzyć strefę standardowa — nadmiarowy publiczny adres IP o nazwie **MyStandardZRPublicIP** w liście **zasobów**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardZRPublicIP \
    --sku Standard
    --zone 1,2,3
```
> [!IMPORTANT]
> W przypadku wersji interfejsu API starszej niż 2020-08-01 Uruchom polecenie powyżej bez określania parametru strefy, aby utworzyć nadmiarowy adres IP strefy. 
>

Aby utworzyć standardowy publiczny adres IP stref w Strefa 2 o nazwie **myStandardZonalPublicIP** w liście **zasobów**, użyj następującego polecenia:

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroupLB \
    --name myStandardZonalPublicIP \
    --sku Standard \
    --zone 2
```

Należy pamiętać, że powyższe opcje dla stref są tylko prawidłowymi opcjami w regionach, w których [strefy dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).

# <a name="standard-sku---no-zones"></a>[**Standardowa jednostka SKU — brak stref**](#tab/option-create-public-ip-standard)

>[!NOTE]
>Następujące polecenie działa w przypadku interfejsu API w wersji 2020-08-01 lub nowszej.  Aby uzyskać więcej informacji na temat aktualnie używanej wersji interfejsu API, zapoznaj się z [dostawcami zasobów i typami](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types).

Użyj [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) , aby utworzyć standardowy publiczny adres IP jako zasób niebędący strefą o nazwie **MyStandardPublicIP** w liście **zasobów**.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myStandardPublicIP \
    --sku Standard
```
Ten wybór jest prawidłowy we wszystkich regionach i jest domyślnym wyborem dla standardowych publicznych adresów IP w regionach bez [strefy dostępności](https://docs.microsoft.com/azure/availability-zones/az-overview?toc=/azure/virtual-network/toc.json#availability-zones).

# <a name="basic-sku"></a>[**Podstawowy SKU**](#tab/option-create-public-ip-basic)

Użyj [AZ Network Public-IP Create](https://docs.microsoft.com/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create) , aby utworzyć podstawowy statyczny publiczny adres IP o nazwie **MyBasicPublicIP** w liście **zasobów**.  Podstawowe publiczne adresy IP nie mają koncepcji dotyczących stref dostępności.

```azurecli-interactive
  az network public-ip create \
    --resource-group myResourceGroup \
    --name myBasicPublicIP \
    --sku Basic \
    --allocation-method Static
```
Jeśli jest akceptowalny do zmiany adresu IP z upływem czasu, można wybrać opcję **dynamicznego** przypisywania adresów IP, zmieniając metodę alokacji na "dynamiczna".

---

## <a name="additional-information"></a>Dodatkowe informacje 

Aby uzyskać więcej szczegółowych informacji na temat poszczególnych zmiennych wymienionych powyżej, zobacz [Zarządzanie publicznymi adresami IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address#create-a-public-ip-address).

## <a name="next-steps"></a>Następne kroki
- Skojarz [publiczny adres IP z maszyną wirtualną](https://docs.microsoft.com/azure/virtual-network/associate-public-ip-address-vm#azure-portal).
- Dowiedz się więcej o [publicznych adresach IP](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) na platformie Azure.
- Dowiedz się więcej na temat wszystkich [ustawień publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address).
