---
title: Dane klasyfikatora na wykresie zasobów platformy Azure
description: Tworzenie zapytań dotyczących danych klasyfikatora na wykresie zasobów platformy Azure
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: f8ad8fd450bc004d9caa2699922717f38d38b482
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502453"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>Zapytanie dotyczące danych klasyfikatora w Eksploratorze grafu zasobów (Wykres zasobów platformy Azure)

Zasoby klasyfikatora zostały teraz dołączone do [grafu zasobów platformy Azure](https://azure.microsoft.com/features/resource-graph/). Stanowi to podstawę do wielu scenariuszy klientów na potrzeby zaleceń usługi Advisor. Niektóre scenariusze, które nie były możliwe przed przeprowadzeniem skalowania i teraz można osiągnąć przy użyciu grafu zasobów, są następujące:
* Zapewnia możliwość wykonywania złożonej kwerendy dla wszystkich subskrypcji w Azure Portal
* Zalecenia podsumowywane według typów kategorii (takich jak wysoka dostępność, wydajność) i typy wpływu (wysoki, średni, niski)
* Wszystkie zalecenia dotyczące określonego typu zalecenia
* Liczba zasobów, których dotyczy problem według kategorii rekomendacji

![Usługa Advisor w Eksploratorze grafu zasobów Azure](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Typy zasobów klasyfikatora w programie Azure Graph

Dostępne typy zasobów klasyfikatora na [wykresie zasobów](https://docs.microsoft.com/azure/governance/resource-graph/): dostępne są 3 typy zasobów do wykonywania zapytań w ramach zasobów klasyfikatora. Poniżej znajduje się lista zasobów, które są teraz dostępne do wykonywania zapytań w grafie zasobów.
* Microsoft. Advisor/konfiguracje
* Microsoft. Advisor/zalecenia
* Microsoft. Advisor/tłumienie

Te typy zasobów są wymienione w nowej tabeli o nazwie AdvisorResources, którą można także badać w Eksploratorze grafu zasobów w Azure Portal.


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zaleceń klasyfikatora, zobacz:
* [Wprowadzenie do Azure Advisor](advisor-overview.md)
* [Wprowadzenie do usługi Advisor](advisor-get-started.md)
* [Zalecenia dotyczące kosztów usługi Advisor](advisor-cost-recommendations.md)
* [Zalecenia dotyczące wydajności usługi Advisor](advisor-performance-recommendations.md)
* [Zalecenia dotyczące zabezpieczeń usługi Advisor](advisor-security-recommendations.md)
* [Zalecenia dotyczące doskonałości operacyjnej klasyfikatora](advisor-operational-excellence-recommendations.md)
* [Interfejs API REST usługi Advisor](https://docs.microsoft.com/rest/api/advisor/)
