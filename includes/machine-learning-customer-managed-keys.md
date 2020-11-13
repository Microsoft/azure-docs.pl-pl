---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/26/2020
ms.author: larryfr
ms.openlocfilehash: 2bba53410834aadce5627a34a759e65aa0e11c28
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94574125"
---
> [!IMPORTANT]
> Wystąpienie Cosmos DB jest tworzone w grupie zasobów zarządzanej przez firmę Microsoft w __ramach subskrypcji__ wraz z potrzebnymi zasobami. Oznacza to, że naliczanie opłat za to wystąpienie Cosmos DB. Zarządzana Grupa zasobów ma nazwę w formacie `<AML Workspace Resource Group Name><GUID>` . Jeśli obszar roboczy Azure Machine Learning używa prywatnego punktu końcowego, Sieć wirtualna jest również tworzona dla wystąpienia Cosmos DB. Ta sieć wirtualna jest używana do zabezpieczania komunikacji między Cosmos DB i Azure Machine Learning.
> 
> * __Nie usuwaj grupy zasobów__ zawierającej to wystąpienie Cosmos DB lub żadnego z zasobów utworzonych automatycznie w tej grupie. Jeśli musisz usunąć grupę zasobów, Cosmos DB wystąpienie itd., musisz usunąć obszar roboczy Azure Machine Learning, który go używa. Grupa zasobów, wystąpienie Cosmos DB i inne automatycznie tworzone zasoby zostaną usunięte po usunięciu skojarzonego obszaru roboczego.
> * Domyślne [__jednostki żądań__](../articles/cosmos-db/request-units.md) dla tego konta Cosmos DB są ustawione na __8000__.
> * __Nie można udostępnić własnej sieci wirtualnej do użycia z tworzonym wystąpieniem Cosmos DB__ . __Nie można również modyfikować sieci wirtualnej__. Na przykład nie można zmienić zakresu adresów IP, z którego korzysta.