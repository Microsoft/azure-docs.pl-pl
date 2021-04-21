---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/15/2021
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: f02b7f0f80cfb875cc6207b542db90607b379b67
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800107"
---
| Zasób | Limit |
| --- | --- |
| Subskrypcje [skojarzone z dzierżawą Azure Active Directory dzierżawy](../articles/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) | Nieograniczona liczba |
| [Współadministratorzy na](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) subskrypcję |Nieograniczona liczba |
| [Grupy zasobów na](../articles/azure-resource-manager/management/overview.md) subskrypcję |980 |
| Azure Resource Manager żądania interfejsu API |4 194 304 bajty |
| Tagi na subskrypcję<sup>1</sup> |50 |
| Obliczenia unikatowych tagów na<sup>subskrypcję 1</sup> | 80 000 |
| [Wdrożenia na poziomie subskrypcji na](../articles/azure-resource-manager/templates/deploy-to-subscription.md) lokalizację | 800<sup>2</sup> |

<sup>1</sup> Do subskrypcji można zastosować maksymalnie 50 tagów. Subskrypcja może jednak zawierać nieograniczoną liczbę tagów, które są stosowane do grup zasobów i zasobów w ramach subskrypcji. Liczba tagów na zasób lub grupę zasobów jest ograniczona do 50. Resource Manager zwraca listę [unikatowych](/rest/api/resources/tags) nazw i wartości tagów w subskrypcji tylko wtedy, gdy liczba tagów wynosi 80 000 lub mniej. Nadal można znaleźć zasób według tagu, gdy liczba przekracza 80 000.

<sup>2.</sup> W przypadku osiągnięcia limitu 800 wdrożeń usuń z historii wdrożenia, które nie są już potrzebne. Aby usunąć wdrożenia na poziomie subskrypcji, użyj [remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) lub [az deployment sub delete](/cli/azure/deployment/sub#az_deployment_sub_delete).
