---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 02/19/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 34388fe975df359c695b9358cafcd3ae711af0cf
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98901113"
---
Jeśli do zasobów zostaną zastosowane tagi, za pomocą tych tagów można wyświetlić koszty zasobów. Aby w analizie kosztów było wyświetlane najnowsze użycie, musi upłynąć trochę czasu, dlatego koszty mogą nie być jeszcze widoczne. Gdy koszty będą już dostępne, można wyświetlić koszty zasobów między grupami zasobów w ramach subskrypcji. Aby móc zobaczyć koszty, użytkownicy muszą mieć [dostęp do informacji o rozliczeniach na poziomie subskrypcji](../articles/cost-management-billing/manage/manage-billing-access.md).

Aby w portalu wyświetlić koszty według tagów, wybierz subskrypcję, a następnie wybierz pozycję **Analiza kosztów**.

![Analiza kosztów](./media/resource-manager-governance-tags-billing/select-cost-analysis.png)

Następnie ustaw filtrowanie według wartości tagu i wybierz pozycję **Zastosuj**.

![Wyświetlanie kosztów według tagów](./media/resource-manager-governance-tags-billing/view-costs-by-tag.png)

Aby programowo wyświetlić koszty, można także użyć [interfejsu API użycia platformy Azure](../articles/cost-management-billing/manage/consumption-api-overview.md) .
