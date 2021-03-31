---
title: Przykłady szablonów usługi Azure Resource Manager — usługa Event Grid | Microsoft Docs
description: Ten artykuł zawiera listę przykładów Azure Resource Manager szablonów dla Azure Event Grid w witrynie GitHub.
ms.topic: sample
ms.date: 07/07/2020
ms.openlocfilehash: 910012adf2dc930e6f1a26f1a7fc41f5ed0580c9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86119059"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Szablony usługi Azure Resource Manager dla usługi Event Grid

Aby zapoznać się z właściwościami i składnią języka JSON, których należy użyć w szablonie, zobacz [Typy zasobów Microsoft.EventGrid](/azure/templates/microsoft.eventgrid/allversions). Poniższa tabela zawiera linki do szablonów usługi Azure Resource Manager dla usługi Event Grid.

## <a name="event-grid-subscriptions"></a>Subskrypcje Event Grid
- [Temat niestandardowy i subskrypcja z punktem końcowym elementu webhook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid) — służy do wdrażania niestandardowego tematu Event Grid. Tworzy subskrypcję tego niestandardowego tematu, który używa punktu końcowego elementu WebHook. 
- [Subskrypcja tematu niestandardowego z punktem końcowym usługi EventHub](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler) — tworzy subskrypcję Event Grid w temacie niestandardowym. Subskrypcja używa Centrum zdarzeń dla punktu końcowego. 
- Subskrypcja [platformy Azure lub subskrypcja grupy zasobów](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook) — subskrybuje zdarzenia dla grupy zasobów lub subskrypcji platformy Azure. Grupa zasobów, która został określona jako element docelowy podczas wdrażania, jest źródłem zdarzeń. Subskrypcja używa elementu WebHook dla punktu końcowego. 
- [Konto magazynu obiektów blob i subskrypcja](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage) — wdraża konto magazynu obiektów blob platformy Azure i subskrybuje zdarzenia dla tego konta magazynu. 

## <a name="next-steps"></a>Następne kroki
Zobacz następujące przykłady:

- [Przykłady programu PowerShell](powershell-samples.md)
- [Przykłady interfejsu wiersza polecenia](cli-samples.md)
