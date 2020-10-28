---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/26/2020
ms.author: larryfr
ms.openlocfilehash: 1fde2947719079e411bc233bcce426feec8fa996
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92631051"
---
> [!IMPORTANT]
> Wystąpienie Cosmos DB jest tworzone w grupie zasobów zarządzanej przez firmę Microsoft w __ramach subskrypcji__ wraz z potrzebnymi zasobami. Oznacza to, że naliczanie opłat za to wystąpienie Cosmos DB. Zarządzana Grupa zasobów ma nazwę w formacie `<AML Workspace Resource Group Name><GUID>` . Jeśli obszar roboczy Azure Machine Learning używa prywatnego punktu końcowego, Sieć wirtualna jest również tworzona dla wystąpienia Cosmos DB. Ta sieć wirtualna jest używana do zabezpieczania komunikacji między Cosmos DB i Azure Machine Learning.
> 
> * __Nie usuwaj grupy zasobów__ zawierającej to wystąpienie Cosmos DB lub żadnego z zasobów utworzonych automatycznie w tej grupie. Jeśli musisz usunąć grupę zasobów, Cosmos DB wystąpienie itd., musisz usunąć obszar roboczy Azure Machine Learning, który go używa. Grupa zasobów, wystąpienie Cosmos DB i inne automatycznie tworzone zasoby zostaną usunięte po usunięciu skojarzonego obszaru roboczego.
> * Domyślne [__jednostki żądań__](../articles/cosmos-db/request-units.md) dla tego konta Cosmos DB są ustawione na __8000__ . __Zmiana tej wartości nie jest obsługiwana__ .
> * __Nie można udostępnić własnej sieci wirtualnej do użycia z tworzonym wystąpieniem Cosmos DB__ . __Nie można również modyfikować sieci wirtualnej__ . Na przykład nie można zmienić zakresu adresów IP, z którego korzysta.