---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 49c0a2a37fa72cd8c4750b39ff422a90721e3940
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93233999"
---
| Zasób | Limit |
| --- | --- |
| Subskrypcje na Azure Active Directory dzierżawcę | Nieograniczona liczba |
| [Współadministratorzy](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) na subskrypcję |Nieograniczona liczba |
| [Grupy zasobów](../articles/azure-resource-manager/management/overview.md) na subskrypcję |980 |
| Rozmiar żądania interfejsu API Azure Resource Manager |4 194 304 bajty |
| Tagi na subskrypcję<sup>1</sup> |50 |
| Obliczenia unikatowych tagów na subskrypcję<sup>1</sup> | 10 000 |
| [Wdrożenia na poziomie subskrypcji](../articles/azure-resource-manager/templates/deploy-to-subscription.md) na lokalizację | 800<sup>2</sup> |

<sup>1</sup> Możesz zastosować do 50 tagów bezpośrednio do subskrypcji. Jednak subskrypcja może zawierać nieograniczoną liczbę tagów, które są stosowane do grup zasobów i zasobów w ramach subskrypcji. Liczba tagów dla zasobu lub grupy zasobów jest ograniczona do 50. Menedżer zasobów zwraca [listę unikatowych nazw tagów i wartości](/rest/api/resources/tags) w subskrypcji tylko wtedy, gdy liczba tagów wynosi 10 000 lub mniej. Nadal można znaleźć zasób według tagu, gdy liczba przekracza 10 000.  

<sup>2</sup> Jeśli osiągnięto limit 800 wdrożeń, należy usunąć wdrożenia, które nie są już potrzebne z historii. Aby usunąć wdrożenia na poziomie subskrypcji, użyj polecenie [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) lub [AZ Deployment sub Delete](/cli/azure/deployment/sub#az-deployment-sub-delete).
