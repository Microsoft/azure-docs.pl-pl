---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 9b2bf8763d175bca028be1fcceda921e87eb5b23
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80334631"
---
| Zasób | Limit |
| --- | --- |
| Grupy zarządzania na katalog | 10 000 |
| Subskrypcje na grupę zarządzania | Ograniczona. |
| Poziomy hierarchii grup zarządzania | Poziom główny plus 6 poziomów<sup>1</sup> |
| Bezpośrednia nadrzędna grupa zarządzania na grupę zarządzania | Jeden |
| [Wdrożenia na poziomie grupy zarządzania](../articles/azure-resource-manager/templates/deploy-to-management-group.md) na lokalizację | 800<sup>2</sup> |

<sup>1</sup> 6 poziomów nie obejmuje poziomu subskrypcji.

<sup>2</sup> Jeśli osiągnięto limit 800 wdrożeń, Usuń wdrożenia z historii, które nie są już potrzebne. Aby usunąć wdrożenia na poziomie grupy zarządzania, użyj polecenie [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) lub [AZ Deployment mg Delete](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-delete).
