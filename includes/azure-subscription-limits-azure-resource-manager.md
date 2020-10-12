---
title: plik dołączany
description: plik dołączany
services: azure-resource-manager
author: tfitzmac
ms.service: cost-management-billing
ms.topic: include
ms.date: 03/26/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 94fd7e692be31ba247e3342246d3940ed08ef9b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80334797"
---
| Zasób | Limit |
| --- | --- |
| Subskrypcje na Azure Active Directory dzierżawcę | Ograniczona. |
| [Współadministratorzy](../articles/cost-management-billing/manage/add-change-subscription-administrator.md) na subskrypcję |Ograniczona. |
| [Grupy zasobów](../articles/azure-resource-manager/management/overview.md) na subskrypcję |980 |
| Rozmiar żądania interfejsu API Azure Resource Manager |4 194 304 bajtów. |
| Tagi na subskrypcję<sup>1</sup> |50 |
| Obliczenia unikatowych tagów na subskrypcję<sup>1</sup> | 10 000 |
| [Wdrożenia na poziomie subskrypcji](../articles/azure-resource-manager/templates/deploy-to-subscription.md) na lokalizację | 800<sup>2</sup> |

<sup>1</sup> Możesz zastosować do 50 tagów bezpośrednio do subskrypcji. Jednak subskrypcja może zawierać nieograniczoną liczbę tagów, które są stosowane do grup zasobów i zasobów w ramach subskrypcji. Liczba tagów dla zasobu lub grupy zasobów jest ograniczona do 50. Menedżer zasobów zwraca [listę unikatowych nazw tagów i wartości](/rest/api/resources/tags) w subskrypcji tylko wtedy, gdy liczba tagów wynosi 10 000 lub mniej. Nadal można znaleźć zasób według tagu, gdy liczba przekracza 10 000.  

<sup>2</sup> Jeśli osiągnięto limit 800 wdrożeń, Usuń wdrożenia z historii, które nie są już potrzebne. Aby usunąć wdrożenia na poziomie subskrypcji, użyj polecenie [Remove-AzDeployment](/powershell/module/az.resources/Remove-AzDeployment) lub [AZ Deployment sub Delete](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-delete).
