---
title: Wybór między usługami Azure Cost Management i Cloudyn
description: Ten artykuł pomaga określić, czy do zarządzania kosztami w Twoim przypadku lepsza jest usługa Azure Cost Management czy Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 03/20/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: a46e02bfa9590b5ef34affa45f3c468fba6999c2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684970"
---
# <a name="choose-between-azure-cost-management-and-cloudyn"></a>Wybór między usługami Azure Cost Management i Cloudyn

Usługa Cloudyn zostanie wycofana przed końcem 2020 r. Istniejące funkcje usługi Cloudyn są zintegrowane bezpośrednio z witryną Azure Portal, gdzie tylko jest to możliwe. Z wyjątkiem klientów korzystających z programu CSP nie można w tej chwili dołączać nowych klientów. Istniejący produkt pozostanie obsługiwany do momentu całkowitego wycofania.

Firma Microsoft nabyła rozwiązanie Cloudyn i migruje jego funkcje zarządzania kosztami z portalu Cloudyn do platformy Azure, aby działały w sposób natywny. Aby móc skorzystać z nowych funkcji, zaloguj się do witryny Azure Portal i przejdź do pozycji [Zarządzanie kosztami i rozliczenia](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) na liście usług platformy Azure. W porównaniu z rozwiązaniem Cloudyn natywne środowisko zapewnia lepszą wydajność i mniejsze opóźnienie danych wynoszące około ośmiu godzin.

Migracja najważniejszych funkcji do usługi Azure Cost Management dla umów Enterprise Agreement, płatności zgodnie z rzeczywistym użyciem i kategorii ofert MSDN została ukończona. Subskrypcje CSP są aktualnie migrowane do usługi Azure Cost Management.

W przypadku korzystania z kategorii ofert, która nie została jeszcze zmigrowana, należy nadal używać portalu Cloudyn. Wszystkie inne osoby mogą korzystać z usługi Azure Cost Management.

## <a name="recommended-services-by-offer"></a>Zalecane usługi według oferty

| Oferty platformy Microsoft Azure | Zalecana usługa zarządzania kosztami |
| --- | --- |
| Umowa Enterprise Agreement platformy Azure | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Web Direct (płatność zgodnie z rzeczywistym użyciem/MSDN) | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Government | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Umowa klienta firmy Microsoft | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)|
| Umowa klienta firmy Microsoft obsługiwana przez partnerów | [Azure Cost Management](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)|
| Azure CSP | [Cloudyn](https://azure.cloudyn.com) |


## <a name="available-cost-management-features"></a>Dostępne funkcje usługi Cost Management

Niektóre z następujących funkcji są dostępne w rozwiązaniu Cloudyn, ale wszystkie są teraz dostępne w usłudze Azure Cost Management.

- Interfejsy API
- Zalecenia dotyczące optymalizacji kosztów platformy Azure, w tym między innymi:
    - Określanie odpowiedniego rozmiaru wystąpienia platformy Azure i zalecenia dotyczące zamykania
    - Rekomendacje dotyczące rezerwacji na platformie Azure
- Budżety
- Analiza kosztów
- Eksportowanie danych na konto usługi Azure Storage
- Mniejsze opóźnienia
- Aplikacja szablonu usługi Power BI
- Obsługa tagów zasobów
- Obsługa analizy kosztów w różnych chmurach dla usługi AWS

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej na temat usługi [Azure Cost Management](../cost-management-billing-overview.md).