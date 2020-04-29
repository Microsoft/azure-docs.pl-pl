---
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/24/2020
ms.author: tomfitz
ms.openlocfilehash: c883383d3c870689bb95f808f6f60c5185c165c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334676"
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

<sup>1</sup> Jeśli osiągnięto limit 800 wdrożeń dla każdej grupy zasobów, należy usunąć wdrożenia z historii, które nie są już potrzebne. Usunięcie wpisu z historii wdrożenia nie ma wpływu na wdrożone zasoby. Aby uzyskać więcej informacji, zobacz [Rozwiązywanie błędów, gdy liczba wdrożeń przekracza 800](../articles/azure-resource-manager/templates/deployment-quota-exceeded.md).

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
