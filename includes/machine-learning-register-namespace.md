---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 12/01/2020
ms.author: larryfr
ms.openlocfilehash: e92d3ac9ed4330cc1680428a426e881538cb0e78
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102623277"
---
* Podczas tworzenia nowego obszaru roboczego można automatycznie tworzyć usługi potrzebne przez obszar roboczy lub korzystać z istniejących usług. Jeśli chcesz używać __istniejących usług z innej subskrypcji platformy Azure__ niż obszar roboczy, musisz zarejestrować przestrzeń nazw Azure Machine Learning w subskrypcji zawierającej te usługi. Na przykład podczas tworzenia obszaru roboczego w ramach subskrypcji A, który korzysta z konta magazynu z subskrypcji B, przestrzeń nazw Azure Machine Learning musi być zarejestrowana w subskrypcji B, aby można było używać konta magazynu z obszarem roboczym.

    Dostawcą zasobów dla Azure Machine Learning jest __Microsoft. MachineLearningServices__. Aby uzyskać informacje na temat sposobu sprawdzenia, czy jest on zarejestrowany i jak go zarejestrować, zobacz artykuł [dostawcy zasobów platformy Azure i typy](../articles/azure-resource-manager/management/resource-providers-and-types.md)  .

    > [!IMPORTANT]
    > Dotyczy to tylko zasobów udostępnianych podczas tworzenia obszaru roboczego; Konta usługi Azure Storage, usługa Azure Container Register, Azure Key Vault i Application Insights.
