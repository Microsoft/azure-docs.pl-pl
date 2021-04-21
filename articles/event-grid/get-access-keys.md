---
title: Uzyskiwanie klucza dostępu dla zasobu Event Grid zasobów
description: W tym artykule opisano sposób uzyskiwania klucza dostępu dla Event Grid tematu lub domeny
ms.topic: how-to
ms.date: 07/07/2020
ms.openlocfilehash: cd60777b2e28b82d72f8f2bf93fe0be301e9e280
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775227"
---
# <a name="get-access-keys-for-event-grid-resources-topics-or-domains"></a>Uzyskiwanie kluczy dostępu dla Event Grid zasobów (tematów lub domen)
Klucze dostępu są używane do uwierzytelniania aplikacji publikujące zdarzenia w Azure Event Grid zasobów (tematów i domen). Zalecamy regularne ponowne generowanie kluczy i ich bezpieczne przechowywanie. Dostępne są dwa klucze dostępu, dzięki czemu można utrzymywać połączenia przy użyciu jednego klucza podczas ponownego generowania drugiego.

W tym artykule opisano sposób uzyskiwania kluczy dostępu dla zasobu Event Grid (tematu lub domeny) przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia. 

## <a name="azure-portal"></a>Azure Portal
W Azure Portal przejdź do karty **Klucze** dostępu na stronie **Event Grid** tematu lub domeny Event Grid **tematu** lub domeny.  

:::image type="content" source="./media/get-access-keys/azure-portal.png" alt-text="Strona Klucze dostępu":::

## <a name="azure-powershell"></a>Azure PowerShell
Użyj polecenia [Get-AzEventGridTopicKey,](/powershell/module/az.eventgrid/get-azeventgridtopickey) aby uzyskać klucze dostępu dla tematów. 

```azurepowershell-interactive
Get-AzEventGridTopicKey -ResourceGroup <RESOURCE GROUP NAME> -Name <TOPIC NAME>
```

Użyj [polecenia Get-AzEventGridDomainKey,](/powershell/module/az.eventgrid/get-azeventgriddomainkey) aby uzyskać klucze dostępu dla domen. 

```azurepowershell-interactive
Get-AzEventGridDomainKey -ResourceGroup <RESOURCE GROUP NAME> -Name <DOMAIN NAME>
```

## <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure
Użyj listy [kluczy tematu az eventgrid,](/cli/azure/eventgrid/topic/key#az_eventgrid_topic_key_list) aby uzyskać klucze dostępu dla tematów. 

```azurecli-interactive
az eventgrid topic key list --resource-group <RESOURCE GROUP NAME> --name <TOPIC NAME>
```

Użyj [az eventgrid domain key list,](/cli/azure/eventgrid/domain/key#az_eventgrid_domain_key_list) aby uzyskać klucze dostępu dla domen. 

```azurecli-interactive
az eventgrid domain key list --resource-group <RESOURCE GROUP NAME> --name <DOMAIN NAME>
```

## <a name="next-steps"></a>Następne kroki
Zobacz następujący artykuł: [Uwierzytelnianie klientów publikowania.](security-authenticate-publishing-clients.md) 
