---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: aa76f7b85302651f6874747610c3355f0572a7ee
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94885487"
---
<!-- List and set subscriptions -->

1. Pobierz listę swoich subskrypcji za pomocą polecenia [az account list](/cli/azure/account#az-account-list):

    ```
    az account list --output table
    ```

2. Użyj polecenia `az account set` z identyfikatorem subskrypcji lub nazwą subskrypcji, na którą chcesz się przełączyć.

    ```
    az account set --subscription "My Demos"
    ```
