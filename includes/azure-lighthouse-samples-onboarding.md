---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 07/07/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: d80786bbdfc4f53254dc98d79ac3badcf5dd3b24
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86111669"
---
Udostępniamy różne szablony dla określonych scenariuszy dołączania. Wybierz najlepiej działającą opcję i pamiętaj, aby zmodyfikować plik parametrów w celu odzwierciedlenia środowiska. Aby uzyskać więcej informacji o sposobach korzystania z tych plików we wdrożeniu, zobacz [Dołączanie klienta do usługi Azure Lighthouse](../articles/lighthouse/how-to/onboard-customer.md).

| **Szablon** | **Opis** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/delegated-resource-management) | Dołącz subskrypcję klienta do usługi Azure Lighthouse. Dla każdej subskrypcji należy wykonać oddzielne wdrożenie. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/rg-delegated-resource-management) | Dołącz co najmniej jedną grupę zasobów klienta do usługi Azure Lighthouse. Użyj właściwości **rgDelegatedResourceManagement** dla pojedynczej grupy zasobów lub właściwości **multipleRgDelegatedResourceManagement**, aby dołączyć wiele grup zasobów w tej samej subskrypcji. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/marketplace-delegated-resource-management) | Jeśli [opublikowano ofertę usługi zarządzanej w witrynie Azure Marketplace](../articles/lighthouse/how-to/publish-managed-services-offers.md), możesz opcjonalnie użyć tego szablonu do dołączenia zasobów dla klientów, którzy zaakceptowali ofertę. Wartości z witryny Marketplace w pliku parametrów muszą być zgodne z wartościami użytymi podczas publikowania oferty. |

Zazwyczaj potrzebne jest osobne wdrożenie dla każdej dołączanej subskrypcji, ale możesz także wdrożyć szablony w wielu subskrypcjach.

| **Szablon** | **Opis** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/cross-subscription-deployment) | Wdrażanie szablonów usługi Azure Resource Manager w wielu subskrypcjach. |
