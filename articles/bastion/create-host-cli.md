---
title: Tworzenie hosta bastionu przy użyciu interfejsu wiersza polecenia platformy Azure | Azure bastionu
description: W tym artykule dowiesz się, jak utworzyć i usunąć hosta bastionu
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: how-to
ms.date: 03/26/2020
ms.author: mialdrid
ms.openlocfilehash: e7f80bb7f9be2e01aa24090d7305b1a5d882da04
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85255519"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Tworzenie hosta usługi Azure bastionu przy użyciu interfejsu wiersza polecenia platformy Azure

W tym artykule opisano sposób tworzenia hosta usługi Azure bastionu przy użyciu interfejsu wiersza polecenia platformy Azure. Po udostępnieniu usługi Azure bastionu w sieci wirtualnej, bezproblemowe środowisko RDP/SSH jest dostępne dla wszystkich maszyn wirtualnych w tej samej sieci wirtualnej. Wdrożenie usługi Azure bastionu odbywa się na sieć wirtualną, a nie na subskrypcję/konto lub maszynę wirtualną.

Opcjonalnie można utworzyć hosta usługi Azure bastionu przy użyciu [Azure Portal](bastion-create-host-portal.md)lub [Azure PowerShell](bastion-create-host-powershell.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [cloudshell cli](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Tworzenie hosta bastionu

Ta sekcja ułatwia tworzenie nowego zasobu usługi Azure bastionu przy użyciu interfejsu wiersza polecenia platformy Azure.

1. Utwórz sieć wirtualną i podsieć usługi Azure bastionu. Należy utworzyć podsieć usługi Azure bastionu przy użyciu wartości Name **AzureBastionSubnet**. Ta wartość pozwala platformie Azure wiedzieć, która podsieć, do której mają zostać wdrożone zasoby bastionu. Jest to inna niż podsieć bramy. Należy użyć podsieci z co najmniej/27 lub większą podsiecią (/27,/26 itd.). Utwórz **AzureBastionSubnet** bez żadnych tabel tras ani delegowania. Jeśli używasz sieciowych grup zabezpieczeń w **AzureBastionSubnet**, zapoznaj się z artykułem [Pracuj z sieciowych grup zabezpieczeń](bastion-nsg.md) .

   ```azurecli-interactive
   az network vnet create -g MyResourceGroup -n MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet  --subnet-prefix 10.0.0.0/24
   ```

2. Utwórz publiczny adres IP dla usługi Azure bastionu. Publiczny adres IP jest publicznym adresem IP, do którego zostanie uzyskany dostęp do protokołu RDP/SSH (za pośrednictwem portu 443). Publiczny adres IP musi znajdować się w tym samym regionie co tworzony zasób bastionu.

   ```azurecli-interactive
   az network public-ip create -g MyResourceGroup -n MyIp --sku Standard
   ```

3. Utwórz nowy zasób platformy Azure bastionu w AzureBastionSubnet sieci wirtualnej. Utworzenie i wdrożenie zasobu bastionu trwa około 5 minut.

   ```azurecli-interactive
   az network bastion create --name $name--public-ip-address $publicip--resource-group $RgName --vnet-name $VNetName --location $location
                           
   ```

## <a name="next-steps"></a>Następne kroki

* Przeczytaj [często zadawane pytania](bastion-faq.md) dotyczące usługi bastionu, aby uzyskać dodatkowe informacje.

* Aby użyć sieciowych grup zabezpieczeń z podsiecią usługi Azure bastionu, zobacz [Work with sieciowych grup zabezpieczeń](bastion-nsg.md).
