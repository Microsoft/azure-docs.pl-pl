---
title: Przykłady szablonów usługi Azure Resource Manager — usługa Event Grid | Microsoft Docs
description: Ten artykuł zawiera listę przykładów Azure Resource Manager szablonów dla Azure Event Grid w witrynie GitHub.
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: na
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 38d8db0bcc504760595fe51b63072f63e785577a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76720626"
---
# <a name="azure-resource-manager-templates-for-event-grid"></a>Szablony usługi Azure Resource Manager dla usługi Event Grid

Aby zapoznać się z właściwościami i składnią języka JSON, których należy użyć w szablonie, zobacz [Typy zasobów Microsoft.EventGrid](/azure/templates/microsoft.eventgrid/allversions). Poniższa tabela zawiera linki do szablonów usługi Azure Resource Manager dla usługi Event Grid.

| | |
|-|-|
|**Subskrypcje Event Grid**||
| [Niestandardowe tematy i subskrypcje z punktem końcowym elementu WebHook](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid)| Wdraża niestandardowy temat usługi Event Grid. Tworzy subskrypcję tego niestandardowego tematu, który używa punktu końcowego elementu WebHook. |
| [Subskrypcja niestandardowego tematu z punktem końcowym EventHub](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-event-hubs-handler)| Tworzy subskrypcję niestandardowego tematu dla usługi Event Grid. Subskrypcja używa Centrum zdarzeń dla punktu końcowego. |
| [Subskrypcja platformy Azure lub subskrypcja grupy zasobów](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-resource-events-to-webhook)| Subskrybuje zdarzenia dla grupy zasobów lub subskrypcji platformy Azure. Grupa zasobów, która został określona jako element docelowy podczas wdrażania, jest źródłem zdarzeń. Subskrypcja używa elementu WebHook dla punktu końcowego. |
| [Konto magazynu obiektów blob i subskrypcja](https://github.com/Azure/azure-quickstart-templates/tree/master/101-event-grid-subscription-and-storage)| Wdraża konto magazynu obiektów Blob platformy Azure i subskrybuje zdarzenia dla tego konta magazynu. |
| | |
