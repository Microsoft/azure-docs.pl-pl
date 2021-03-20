---
title: Wyłączanie zasad sieciowych dla prywatnych punktów końcowych na platformie Azure
description: Dowiedz się, jak wyłączyć zasady sieciowe dla prywatnych punktów końcowych.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: how-to
ms.date: 09/16/2019
ms.author: allensu
ms.openlocfilehash: a4c04033f3fb5ff523f0d80bb6c978955f4c9ec2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84737516"
---
# <a name="disable-network-policies-for-private-endpoints"></a>Wyłączanie zasad sieciowych dla prywatnych punktów końcowych

Zasady sieciowe, takie jak sieciowe grupy zabezpieczeń (sieciowej grupy zabezpieczeń), nie są obsługiwane dla prywatnych punktów końcowych. Aby można było wdrożyć prywatny punkt końcowy w danej podsieci, w tej podsieci jest wymagane jawne ustawienie Disable. To ustawienie ma zastosowanie tylko do prywatnego punktu końcowego. W przypadku innych zasobów w podsieci dostęp jest kontrolowany na podstawie definicji reguł zabezpieczeń sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń). 
 
W przypadku tworzenia prywatnego punktu końcowego przy użyciu portalu to ustawienie jest automatycznie wyłączone w ramach procesu tworzenia. Wdrożenie przy użyciu innych klientów wymaga dodatkowego kroku, aby zmienić to ustawienie. Można wyłączyć ustawienie za pomocą usługi Cloud Shell z poziomu Azure Portal lub lokalnych instalacji Azure PowerShell, interfejsu wiersza polecenia platformy Azure lub użyć szablonów Azure Resource Manager.  
 
W poniższych przykładach opisano, jak wyłączyć `PrivateEndpointNetworkPolicies` sieć wirtualną o nazwie *myVirtualNetwork* z *domyślną* podsiecią hostowaną w grupie zasobów o nazwie Moja *resourceName*.

## <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell
W tej sekcji opisano, jak wyłączyć zasady prywatnego punktu końcowego podsieci przy użyciu Azure PowerShell.

```azurepowershell
$virtualNetwork= Get-AzVirtualNetwork `
  -Name "myVirtualNetwork" ` 
  -ResourceGroupName "myResourceGroup"  
   
($virtualNetwork | Select -ExpandProperty subnets | Where-Object  {$_.Name -eq 'default'} ).PrivateEndpointNetworkPolicies = "Disabled" 
 
$virtualNetwork | Set-AzVirtualNetwork 
```
## <a name="using-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure
W tej sekcji opisano, jak wyłączyć zasady prywatnego punktu końcowego podsieci przy użyciu interfejsu wiersza polecenia platformy Azure.
```azurecli
az network vnet subnet update \ 
  --name default \ 
  --resource-group myResourceGroup \ 
  --vnet-name myVirtualNetwork \ 
  --disable-private-endpoint-network-policies true
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
                                    "privateEndpointNetworkPolicies": "Disabled" 
                                 } 
                         } 
                  ] 
          } 
} 
```
## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [prywatnym punkcie końcowym platformy Azure](private-endpoint-overview.md)
 
