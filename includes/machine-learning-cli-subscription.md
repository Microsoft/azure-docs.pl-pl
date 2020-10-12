---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 428a3ad17c81b465635207de622398e814289d87
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81616830"
---
> [!TIP]
> Po zalogowaniu zostanie wyświetlona lista subskrypcji skojarzonych z Twoim kontem platformy Azure. Informacje o subskrypcji `isDefault: true` są aktualnie aktywowanymi subskrypcjami poleceń interfejsu wiersza polecenia platformy Azure. Ta subskrypcja musi być taka sama, która zawiera obszar roboczy Azure Machine Learning. Identyfikator subskrypcji można znaleźć na [Azure Portal](https://portal.azure.com) , odwiedzając stronę przeglądu obszaru roboczego. Możesz również użyć zestawu SDK, aby uzyskać identyfikator subskrypcji z obiektu obszaru roboczego. Na przykład `Workspace.from_config().subscription_id`.
> 
> Aby wybrać inną subskrypcję, użyj `az account set -s <subscription name or ID>` polecenia i określ nazwę lub Identyfikator subskrypcji do przełączenia. Aby uzyskać więcej informacji na temat wyboru subskrypcji, zobacz [Korzystanie z wielu subskrypcji platformy Azure](https://docs.microsoft.com/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).