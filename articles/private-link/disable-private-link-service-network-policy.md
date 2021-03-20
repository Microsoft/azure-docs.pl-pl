---
title: 'Wyłącz zasady sieciowe dla źródłowego adresu IP usługi linku prywatnego platformy Azure '
description: Dowiedz się, jak wyłączyć zasady sieciowe dla prywatnego linku platformy Azure
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: 7528d12dcbfba09b4dae4ac07a5c683a7c11bd88
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99548805"
---
# <a name="disable-network-policies-for-private-link-service-source-ip"></a>Wyłącz zasady sieciowe dla źródłowego adresu IP usługi linku prywatnego

Aby można było wybrać źródłowy adres IP dla usługi linku prywatnego, `privateLinkServiceNetworkPolicies` w podsieci musi być wymagane jawne ustawienie Wyłącz. To ustawienie dotyczy tylko określonego prywatnego adresu IP, który został wybrany jako źródłowy adres IP usługi łącza prywatnego. W przypadku innych zasobów w podsieci dostęp jest kontrolowany na podstawie definicji reguł zabezpieczeń sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń). 
 
W przypadku tworzenia usługi linku prywatnego przy użyciu portalu to ustawienie jest automatycznie wyłączone w ramach procesu tworzenia. Wdrożenia przy użyciu dowolnego klienta platformy Azure (środowiska PowerShell, interfejsu wiersza polecenia lub szablonów) wymagają dodatkowego kroku, aby zmienić tę właściwość. Zasady można wyłączyć za pomocą usługi Cloud Shell z poziomu Azure Portal lub instalacji lokalnych Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub szablonów Azure Resource Manager.  
 
Wykonaj poniższe kroki, aby wyłączyć zasady sieci usługi link prywatny dla sieci wirtualnej o nazwie *myVirtualNetwork* z *domyślną* podsiecią hostowaną w grupie zasobów o nazwie Moja *resourceName*. 

## <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell
W tej sekcji opisano, jak wyłączyć zasady prywatnego punktu końcowego podsieci przy użyciu Azure PowerShell.
W kodzie Zastąp wartość "default" nazwą podsieci wirtualnej.

```azurepowershell
$virtualSubnetName = "default"
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq $virtualSubnetName} ).privateLinkServiceNetworkPolicies = "Disabled"  
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure
W tej sekcji opisano, jak wyłączyć zasady prywatnego punktu końcowego podsieci przy użyciu interfejsu wiersza polecenia platformy Azure.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-link-service-network-policies true 
```
## <a name="using-a-template"></a>Korzystanie z szablonu
W tej sekcji opisano, jak wyłączyć zasady prywatnego punktu końcowego podsieci przy użyciu szablonu Azure Resource Manager.
```json
{ 
    "name": "myVirtualNetwork", 
    "type": "Microsoft.Network/virtualNetworks", 
    "apiVersion": "2019-04-01", 
    "location": "WestUS", 
    "properties": { 
        "addressSpace": { 
            "addressPrefixes": [ 
                "10.0.0.0/16" 
             ] 
        }, 
        "subnets": [ 
               { 
                 "name": "default", 
                 "properties": { 
                        "addressPrefix": "10.0.0.0/24", 
                        "privateLinkServiceNetworkPolicies": "Disabled" 
                    } 
                } 
        ] 
    } 
} 
 
```
## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [prywatnym punkcie końcowym platformy Azure](private-endpoint-overview.md)
 
