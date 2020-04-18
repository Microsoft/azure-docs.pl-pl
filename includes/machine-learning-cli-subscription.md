---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 428a3ad17c81b465635207de622398e814289d87
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81616830"
---
> [!TIP]
> Po zalogowaniu się zobaczysz listę subskrypcji skojarzonych z twoim kontem platformy Azure. Informacje o `isDefault: true` subskrypcji to aktualnie aktywowana subskrypcja poleceń interfejsu wiersza polecenia platformy Azure. Ta subskrypcja musi być taka sama, która zawiera obszar roboczy usługi Azure Machine Learning. Identyfikator subskrypcji można znaleźć w [witrynie Azure portal,](https://portal.azure.com) odwiedzając stronę przeglądu obszaru roboczego. Można również użyć SDK, aby uzyskać identyfikator subskrypcji z obiektu obszaru roboczego. Na przykład `Workspace.from_config().subscription_id`.
> 
> Aby wybrać inną subskrypcję, `az account set -s <subscription name or ID>` użyj polecenia i określ nazwę subskrypcji lub identyfikator, do którego chcesz się przełączyć. Aby uzyskać więcej informacji na temat wyboru subskrypcji, zobacz [Używanie wielu subskrypcji platformy Azure](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).