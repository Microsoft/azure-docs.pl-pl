---
title: Zasady tagowania zasobów
description: Opisuje zasady platformy Azure, które można przypisać w celu zapewnienia zgodności tagów.
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: e3eeb28ea23b18c3492f68d2fac294fc014420c5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82147864"
---
# <a name="assign-policies-for-tag-compliance"></a>Przypisywanie zasad zgodności tagów

Użyj [Azure Policy](../../governance/policy/overview.md) , aby wymusić reguły tagowania i konwencje. Utworzenie zasad pozwala uniknąć scenariusza wdrażania zasobów w ramach subskrypcji, które nie mają oczekiwanych tagów w organizacji. Zamiast ręcznie stosować Tagi lub wyszukiwać zasoby, które nie są zgodne, należy utworzyć zasady, które automatycznie stosują potrzebne Tagi podczas wdrażania. Tagi mogą być również stosowane do istniejących zasobów przy użyciu nowego efektu [Modyfikuj](../../governance/policy/concepts/effects.md#modify) i [zadania korygowania](../../governance/policy/how-to/remediate-resources.md). W poniższej sekcji przedstawiono przykładowe zasady dla tagów.

## <a name="policies"></a>Zasady

[!INCLUDE [Tag policies](../../../includes/policy/samples/bycat/policies-tags.md)]

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o znakowaniu zasobów, zobacz [Używanie tagów do organizowania zasobów platformy Azure](tag-resources.md).
* Nie wszystkie typy zasobów obsługują Tagi. Aby określić, czy można zastosować tag do typu zasobu, zobacz [obsługa tagów dla zasobów platformy Azure](tag-support.md).
