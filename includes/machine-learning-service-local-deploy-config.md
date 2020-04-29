---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 60c754aeec3157abee02eb136dcf148c440d8da0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79477276"
---
Wpisy w `deploymentconfig.json` dokumencie są mapowane na parametry [LocalWebservice. deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.local.localwebservicedeploymentconfiguration?view=azure-ml-py). W poniższej tabeli opisano mapowanie między jednostkami w dokumencie JSON a parametrami metody:

| Jednostka JSON | Parametr metody | Opis |
| ----- | ----- | ----- |
| `computeType` | Nie dotyczy | Docelowy zasób obliczeniowy. Dla lokalnych obiektów docelowych wartość musi być `local`. |
| `port` | `port` | Port lokalny, na którym ma zostać ujawniony punkt końcowy HTTP usługi. |

Ten kod JSON jest przykładową konfiguracją wdrożenia do użycia z interfejsem wiersza polecenia:

```json
{
    "computeType": "local",
    "port": 32267
}
```
