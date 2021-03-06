---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 493c674fa161bf33436e560fdcbb9196410db931
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102209476"
---
> [!TIP]
> Po zalogowaniu zostanie wyświetlona lista subskrypcji skojarzonych z Twoim kontem platformy Azure. Informacje o subskrypcji `isDefault: true` są aktualnie aktywowanymi subskrypcjami poleceń interfejsu wiersza polecenia platformy Azure. Ta subskrypcja musi być taka sama, która zawiera obszar roboczy Azure Machine Learning. Identyfikator subskrypcji można znaleźć na [Azure Portal](https://portal.azure.com) , odwiedzając stronę przeglądu obszaru roboczego. Możesz również użyć zestawu SDK, aby uzyskać identyfikator subskrypcji z obiektu obszaru roboczego. Na przykład `Workspace.from_config().subscription_id`.
> 
> Aby wybrać inną subskrypcję, użyj `az account set -s <subscription name or ID>` polecenia i określ nazwę lub Identyfikator subskrypcji do przełączenia. Aby uzyskać więcej informacji na temat wyboru subskrypcji, zobacz [Korzystanie z wielu subskrypcji platformy Azure](/cli/azure/manage-azure-subscriptions-azure-cli).