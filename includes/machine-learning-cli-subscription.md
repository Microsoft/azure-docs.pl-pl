---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/26/2020
ms.author: larryfr
ms.openlocfilehash: 6147dff71ff8c93c8c46b5921b37db459ea73e4a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025747"
---
> [!TIP]
> Po zalogowaniu zostanie wyświetlona lista subskrypcji skojarzonych z Twoim kontem platformy Azure. Informacje o subskrypcji `isDefault: true` są aktualnie aktywowanymi subskrypcjami poleceń interfejsu wiersza polecenia platformy Azure. Ta subskrypcja musi być taka sama, która zawiera obszar roboczy Azure Machine Learning. Identyfikator subskrypcji można znaleźć na [Azure Portal](https://portal.azure.com) , odwiedzając stronę przeglądu obszaru roboczego. Możesz również użyć zestawu SDK, aby uzyskać identyfikator subskrypcji z obiektu obszaru roboczego. Na przykład `Workspace.from_config().subscription_id`.
> 
> Aby wybrać inną subskrypcję, użyj `az account set -s <subscription name or ID>` polecenia i określ nazwę lub Identyfikator subskrypcji do przełączenia. Aby uzyskać więcej informacji na temat wyboru subskrypcji, zobacz [Korzystanie z wielu subskrypcji platformy Azure](/cli/azure/manage-azure-subscriptions-azure-cli?view=azure-cli-latest).