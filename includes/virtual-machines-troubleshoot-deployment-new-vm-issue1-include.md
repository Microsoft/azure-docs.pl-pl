---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 2ae72045ae18d84eac2a6d619d94e3a9e49415ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183229"
---
## <a name="issue-custom-image-provisioning-errors"></a>Problem: obraz niestandardowy; Błędy aprowizacji
Błędy aprowizacji powstają podczas przekazywania lub przechwytywania uogólnionego obrazu maszyny wirtualnej jako wyspecjalizowanego obrazu maszyny wirtualnej lub na odwrót. Pierwsza z nich spowoduje błąd limitu czasu aprowizacji, a drugie spowoduje niepowodzenie aprowizacji. Aby wdrożyć niestandardowy obraz bez błędów, należy się upewnić, że typ obrazu nie zmienia się podczas procesu przechwytywania.

W poniższej tabeli wymieniono możliwe kombinacje uogólnionych i wyspecjalizowanych obrazów, typ błędu, który należy wykonać i co należy zrobić, aby naprawić błędy.

