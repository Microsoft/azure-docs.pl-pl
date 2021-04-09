---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 09/01/2020
ms.author: tomfitz
ms.openlocfilehash: 543aa50d72de5a06a9a1c7ac88ac5ecae993bc9d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98697990"
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

Można przekroczyć niektóre limity szablonów przy użyciu zagnieżdżonego szablonu. Aby uzyskać więcej informacji, zobacz [Używanie połączonych szablonów podczas wdrażania zasobów platformy Azure](../articles/azure-resource-manager/templates/linked-templates.md). Aby zmniejszyć liczbę parametrów, zmiennych lub danych wyjściowych, można połączyć kilka wartości z obiektem. Aby uzyskać więcej informacji, zobacz [obiekty jako parametry](/azure/architecture/guide/azure-resource-manager/advanced-templates/objects-as-parameters).