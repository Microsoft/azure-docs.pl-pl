---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/24/2020
ms.author: tomfitz
ms.openlocfilehash: 33a63280f6973d2c5e29db29f7a6f3fc68c57c77
ms.sourcegitcommit: c052c99fd0ddd1171a08077388d221482026cd58
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84424755"
---
| Zasób | Limit |
| --- | --- |
| Zasoby na [grupę zasobów](../articles/azure-resource-manager/management/overview.md#resource-groups) | Zasoby nie są ograniczone przez grupę zasobów. Zamiast tego są one ograniczone przez typ zasobów w grupie zasobów. Zobacz następny wiersz. |
| Zasoby na grupę zasobów dla każdego typu zasobu |800 — niektóre typy zasobów mogą przekraczać limit 800. Zobacz [zasoby nieograniczone do 800 wystąpień na grupę zasobów](../articles/azure-resource-manager/management/resources-without-resource-group-limit.md). |
| Wdrożenia według grupy zasobów w historii wdrożenia |800<sup>1</sup> |
| Zasoby na wdrożenie |800 |
| Blokady zarządzania na unikatowy zakres |20 |
| Liczba tagów dla zasobu lub grupy zasobów |50 |
| Długość klucza tagu |512 |
| Długość wartości tagu |256 |

<sup>1</sup> Począwszy od czerwca 2020, wdrożenia będą automatycznie usuwane z historii, jak zbliżasz się do limitu. Usunięcie wpisu z historii wdrożenia nie ma wpływu na wdrożone zasoby. Aby uzyskać więcej informacji, zobacz [Automatyczne usuwanie z historii wdrażania](../articles/azure-resource-manager/templates/deployment-history-deletions.md).

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
