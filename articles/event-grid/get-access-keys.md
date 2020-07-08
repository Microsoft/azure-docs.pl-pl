---
title: Uzyskaj klucz dostępu dla zasobu Event Grid
description: W tym artykule opisano, jak uzyskać klucz dostępu dla tematu Event Grid lub domeny
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: how-to
ms.date: 06/25/2020
ms.author: spelluru
ms.openlocfilehash: 75ab09de40a4305ebd215985f2f9f181bfd69a24
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85414892"
---
# <a name="get-access-keys-for-event-grid-resources-topics-or-domains"></a>Uzyskaj klucze dostępu dla zasobów Event Grid (tematy lub domeny)
Klucze dostępu są używane do uwierzytelniania zdarzeń publikowania aplikacji w celu Azure Event Grid zasobów (tematów i domen). Zalecamy regularne ponowne generowanie kluczy i przechowywanie ich w bezpiecznym miejscu. Są dostępne dwa klucze dostępu, dzięki czemu można zachować połączenia przy użyciu jednego klucza podczas ponownego generowania drugiego.

W tym artykule opisano sposób uzyskiwania kluczy dostępu dla zasobu Event Grid (temat lub domena) przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia. 

## <a name="azure-portal"></a>Azure Portal
W Azure Portal przejdź do karty **klucze dostępu** w **temacie Event Grid** lub na stronie **domeny Event Grid** dla tematu lub domeny.  

:::image type="content" source="./media/get-access-keys/azure-portal.png" alt-text="Strona kluczy dostępu":::

## <a name="azure-powershell"></a>Azure PowerShell
Użyj polecenia [Get-AzEventGridTopicKey](/powershell/module/az.eventgrid/get-azeventgridtopickey?view=azps-4.3.0) , aby uzyskać klucze dostępu dla tematów. 

```azurepowershell-interactive
Get-AzEventGridTopicKey -ResourceGroup <RESOURCE GROUP NAME> -Name <TOPIC NAME>
```

Użyj polecenia [Get-AzEventGridDomainKey](/powershell/module/az.eventgrid/get-azeventgriddomainkey?view=azps-4.3.0) , aby uzyskać klucze dostępu dla domen. 

```azurepowershell-interactive
Get-AzEventGridDomainKey -ResourceGroup <RESOURCE GROUP NAME> -Name <DOMAIN NAME>
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Użyj [listy kluczy tematu AZ eventgrid](/cli/azure/eventgrid/topic/key?view=azure-cli-latest#az-eventgrid-topic-key-list) , aby uzyskać dostęp do kluczy dla tematów. 

```azurecli-interactive
az eventgrid topic key list --resource-group <RESOURCE GROUP NAME> --name <TOPIC NAME>
```

Użyj [AZ eventgrid domena Key list](/cli/azure/eventgrid/domain/key?view=azure-cli-latest#az-eventgrid-domain-key-list) , aby uzyskać klucze dostępu dla domen. 

```azurecli-interactive
az eventgrid domain key list --resource-group <RESOURCE GROUP NAME> --name <DOMAIN NAME>
```

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującym artykułem: [uwierzytelnianie publikowania klientów](security-authenticate-publishing-clients.md). 
