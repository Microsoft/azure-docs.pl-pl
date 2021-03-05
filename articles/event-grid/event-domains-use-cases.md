---
title: Przypadki użycia w przypadku domen zdarzeń w Azure Event Grid
description: W tym artykule opisano kilka przypadków użycia dotyczących używania domen zdarzeń w programie Azure Event Grid.
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 00318fc78053ed55e3599c329746d89d2eee4f99
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204409"
---
# <a name="use-cases-for-event-domains-in-azure-event-grid"></a>Przypadki użycia w przypadku domen zdarzeń w Azure Event Grid
W tym artykule opisano kilka przypadków użycia dotyczących używania domen zdarzeń w programie Azure Event Grid. 

## <a name="use-case-1"></a>Przypadek użycia 1 
[!INCLUDE [event-grid-domain-example-use-case.md](../../includes/event-grid-domain-example-use-case.md)]

## <a name="use-case-2"></a>Przypadek użycia 2
W przypadku korzystania z tematów systemowych obowiązuje limit 500 subskrypcji zdarzeń. Jeśli potrzebujesz więcej niż 500 subskrypcji zdarzeń dla tematu systemowego, możesz użyć domen. 

Załóżmy, że utworzono temat systemowy dla Blob Storage platformy Azure i musisz utworzyć więcej niż 500 subskrypcji w temacie, ale nie jest to możliwe z powodu ograniczenia (500 subskrypcji na temat). W takim przypadku można użyć następującego rozwiązania korzystającego z domeny zdarzeń. 

1. Utwórz domenę, która może obsługiwać do 100 000 tematów, a każdy temat domeny może mieć 500 subskrypcje zdarzeń. Ten model zapewni 500 * 100 000 subskrypcji zdarzeń. 
1. Utwórz subskrypcję funkcji platformy Azure dla tematu systemu Azure Storage. Gdy funkcja otrzymuje zdarzenia z usługi Azure Storage, może wzbogacać i publikować zdarzenia do odpowiedniego tematu domeny. Na przykład funkcja może analizować nazwę pliku obiektu BLOB, aby określić temat domeny docelowej i opublikować zdarzenie w temacie domena. 

:::image type="content" source="./media/event-domains-use-cases/use-case-2.jpg" alt-text="Domeny siatki AzureEvent — przypadek użycia 2":::


## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej o konfigurowaniu domen zdarzeń, tworzeniu tematów, tworzeniu subskrypcji zdarzeń i publikowaniu zdarzeń, zobacz [Zarządzanie domenami zdarzeń](./how-to-event-domains.md).
