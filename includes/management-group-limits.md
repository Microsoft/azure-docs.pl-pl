---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: cdcf6215973755444da9e513761de7ac71e479d4
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98738811"
---
| Zasób | Limit |
| --- | --- |
| Grupy zarządzania dla dzierżawy usługi Azure AD | 10 000 |
| Subskrypcje na grupę zarządzania | Ograniczona. |
| Poziomy hierarchii grup zarządzania | Poziom główny plus 6 poziomów<sup>1</sup> |
| Bezpośrednia nadrzędna grupa zarządzania na grupę zarządzania | Jeden |
| [Wdrożenia na poziomie grupy zarządzania](../articles/azure-resource-manager/templates/deploy-to-management-group.md) na lokalizację | 800<sup>2</sup> |

<sup>1</sup> 6 poziomów nie obejmuje poziomu subskrypcji.

<sup>2</sup> Jeśli osiągnięto limit 800 wdrożeń, Usuń wdrożenia z historii, które nie są już potrzebne. Aby usunąć wdrożenia na poziomie grupy zarządzania, użyj polecenie [Remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) lub [AZ Deployment mg Delete](/cli/azure/deployment/mg#az-deployment-mg-delete).
