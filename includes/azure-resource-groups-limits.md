---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 09/01/2020
ms.author: tomfitz
ms.openlocfilehash: 949118214851c3eceffd8c1d638a4093bdf7f366
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89303962"
---
| Zasób | Limit |
| --- | --- |
| Zasoby na [grupę zasobów](../articles/azure-resource-manager/management/overview.md#resource-groups) | Zasoby nie są ograniczone przez grupę zasobów. Zamiast tego są one ograniczone przez typ zasobów w grupie zasobów. Zobacz następny wiersz. |
| Zasoby na grupę zasobów dla każdego typu zasobu |800 — niektóre typy zasobów mogą przekraczać limit 800. Zobacz [zasoby nieograniczone do 800 wystąpień na grupę zasobów](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Wdrożenia według grupy zasobów w historii wdrożenia |800<sup>1</sup> |
| Zasoby na wdrożenie |800 |
| Blokady zarządzania na unikatowy [zakres](../articles/azure-resource-manager/management/overview.md#understand-scope)  |20 |
| Liczba tagów dla zasobu lub grupy zasobów |50 |
| Długość klucza tagu |512 |
| Długość wartości tagu |256 |

<sup>1</sup> Wdrożenia są automatycznie usuwane z historii, jak zbliżasz się do limitu. Usunięcie wpisu z historii wdrożenia nie ma wpływu na wdrożone zasoby. Aby uzyskać więcej informacji, zobacz [Automatyczne usuwanie z historii wdrażania](../articles/azure-resource-manager/templates/deployment-history-deletions.md).

#### <a name="template-limits"></a>Limity szablonów

| Wartość | Limit |
| --- | --- |
| Parametry |256 |
| Zmienne |256 |
| Zasoby (w tym liczba kopii) |800 |
| Dane wyjściowe |64 |
| Wyrażenie szablonu |24 576 znaków |
| Zasoby w wyeksportowanych szablonach |200 |
| Rozmiar szablonu |4 MB |
| Rozmiar pliku parametru |64 KB |

Można przekroczyć niektóre limity szablonów przy użyciu zagnieżdżonego szablonu. Aby uzyskać więcej informacji, zobacz [Używanie połączonych szablonów podczas wdrażania zasobów platformy Azure](../articles/azure-resource-manager/templates/linked-templates.md). Aby zmniejszyć liczbę parametrów, zmiennych lub danych wyjściowych, można połączyć kilka wartości z obiektem. Aby uzyskać więcej informacji, zobacz [obiekty jako parametry](../articles/azure-resource-manager/resource-manager-objects-as-parameters.md).
