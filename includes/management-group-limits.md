---
title: Plik dyrektywy include
description: Plik dyrektywy include
author: tfitzmac
ms.service: governance
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 53e3f37d14153f3a2d7b5886a49b08ca9052b128
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799954"
---
| Zasób | Limit |
| --- | --- |
| Grupy zarządzania na dzierżawę usługi Azure AD | 10 000 |
| Subskrypcje na grupę zarządzania | Nieograniczony. |
| Poziomy hierarchii grup zarządzania | Poziom główny plus 6 poziomów<sup>1</sup> |
| Bezpośrednia nadrzędna grupa zarządzania na grupę zarządzania | Jeden |
| [Wdrożenia na poziomie grupy zarządzania na](../articles/azure-resource-manager/templates/deploy-to-management-group.md) lokalizację | 800<sup>2</sup> |

<sup>1</sup> 6 poziomów nie obejmuje poziomu subskrypcji.

<sup>2</sup> Jeśli osiągniesz limit 800 wdrożeń, usuń wdrożenia z historii, które nie są już potrzebne. Aby usunąć wdrożenia na poziomie grupy zarządzania, użyj [remove-AzManagementGroupDeployment](/powershell/module/az.resources/Remove-AzManagementGroupDeployment) lub [az deployment mg delete](/cli/azure/deployment/mg#az_deployment_mg_delete).
