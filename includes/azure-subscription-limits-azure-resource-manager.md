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
ms.openlocfilehash: 97d80e999ac61a2c2f8f561dc19213419014beb8
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/15/2021
ms.locfileid: "103470995"
---
| Zasób | Limit |
| --- | --- |
| Subskrypcje [skojarzone z dzierżawą Azure Active Directory](../articles/active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) | Nieograniczona liczba |
| [Współadministratorzy](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) na subskrypcję |Nieograniczona liczba |
| [Grupy zasobów](../articles/azure-resource-manager/management/overview.md) na subskrypcję |980 |
| Rozmiar żądania interfejsu API Azure Resource Manager |4 194 304 bajty |
| Tagi na subskrypcję<sup>1</sup> |50 |
| Obliczenia unikatowych tagów na subskrypcję<sup>1</sup> | 80 000 |
| [Wdrożenia na poziomie subskrypcji](../articles/azure-resource-manager/templates/deploy-to-subscription.md) na lokalizację | 800<sup>2</sup> |

<sup>1</sup> Możesz zastosować do 50 tagów bezpośrednio do subskrypcji. Jednak subskrypcja może zawierać nieograniczoną liczbę tagów, które są stosowane do grup zasobów i zasobów w ramach subskrypcji. Liczba tagów dla zasobu lub grupy zasobów jest ograniczona do 50. Menedżer zasobów zwraca [listę unikatowych nazw tagów i wartości](/rest/api/resources/tags) w subskrypcji tylko wtedy, gdy liczba tagów wynosi 80 000 lub mniej. Nadal można znaleźć zasób według tagu, gdy liczba przekracza 80 000.

<sup>2</sup> Jeśli osiągnięto limit 800 wdrożeń, należy usunąć wdrożenia, które nie są już potrzebne z historii. Aby usunąć wdrożenia na poziomie subskrypcji, użyj polecenie [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) lub [AZ Deployment sub Delete](/cli/azure/deployment/sub#az-deployment-sub-delete).
