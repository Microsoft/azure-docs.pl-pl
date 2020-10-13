---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 01/09/2020
ms.author: larryfr
ms.openlocfilehash: 8fd774f8a3a73ceaffa7902b35e1b1dff12ef5af
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75893974"
---
Podczas tworzenia obszaru roboczego Azure Machine Learning lub zasobu używanego przez obszar roboczy może zostać wyświetlony komunikat o błędzie podobny do następującego:

* `No registered resource provider found for location {location}`
* `The subscription is not registered to use namespace {resource-provider-namespace}`

Większość dostawców zasobów jest automatycznie rejestrowana, ale nie wszystkie. Jeśli zostanie wyświetlony ten komunikat, należy zarejestrować wymienionego dostawcę.

Aby uzyskać informacje na temat rejestrowania dostawców zasobów, zobacz [Rozwiązywanie problemów dotyczących rejestracji dostawcy zasobów](../articles/azure-resource-manager/templates/error-register-resource-provider.md).