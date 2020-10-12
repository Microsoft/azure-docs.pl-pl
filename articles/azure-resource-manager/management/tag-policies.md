---
title: Zasady tagowania zasobów
description: Opisuje zasady platformy Azure, które można przypisać w celu zapewnienia zgodności tagów.
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: c6867bc01306ac3c08a9797ece0567a45e060af2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89293743"
---
# <a name="assign-policies-for-tag-compliance"></a>Przypisywanie zasad zgodności tagów

Użyj [Azure Policy](../../governance/policy/overview.md) , aby wymusić reguły tagowania i konwencje. Utworzenie zasad pozwala uniknąć scenariusza wdrażania zasobów w ramach subskrypcji, które nie mają oczekiwanych tagów w organizacji. Zamiast ręcznie stosować Tagi lub wyszukiwać zasoby, które nie są zgodne, należy utworzyć zasady, które automatycznie stosują potrzebne Tagi podczas wdrażania. Tagi mogą być również stosowane do istniejących zasobów przy użyciu nowego efektu [Modyfikuj](../../governance/policy/concepts/effects.md#modify) i [zadania korygowania](../../governance/policy/how-to/remediate-resources.md). W poniższej sekcji przedstawiono przykładowe zasady dla tagów.

## <a name="policies"></a>Zasady

[!INCLUDE [Tag policies](../../../includes/policy/reference/bycat/policies-tags.md)]

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o znakowaniu zasobów, zobacz [Używanie tagów do organizowania zasobów platformy Azure](tag-resources.md).
* Nie wszystkie typy zasobów obsługują Tagi. Aby określić, czy można zastosować tag do typu zasobu, zobacz [obsługa tagów dla zasobów platformy Azure](tag-support.md).
