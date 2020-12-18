---
title: Monitorowanie i śledzenie użycia bezpłatnej usługi platformy Azure
description: Dowiedz się, jak sprawdzić użycie bezpłatnej usługi w witrynie Azure Portal. Za usługi dostępne w ramach bezpłatnego konta nie są naliczane opłaty, chyba że zostanie przekroczony limit usług.
author: amberbhargava
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 12/04/2020
ms.author: banders
ms.openlocfilehash: c7c28e64822a6aefa17e8baa4ef42a3b3fea8adb
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97589780"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Sprawdzanie użycia bezpłatnej usługi działającej w ramach bezpłatnego konta platformy Azure

Za korzystanie z bezpłatnych usług działających w ramach bezpłatnego konta platformy Azure nie są naliczane opłaty, chyba że przekroczono limity dotyczące tych usług. Aby nie przekraczać tych limitów, można śledzić użycie bezpłatnych usług za pomocą witryny Azure Portal.

## <a name="check-usage-in-the-azure-portal"></a>Sprawdzanie użycia w witrynie Azure Portal

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1.  Wyszukaj pozycję **Subskrypcje**.  
    ![Zrzut ekranu przedstawiający wyszukiwanie subskrypcji w portalu](./media/check-free-service-usage/billing-search-subscriptions.png)
1.  Wybierz subskrypcję utworzoną podczas tworzenia bezpłatnego konta platformy Azure.
1.  Przewiń w dół, aby wyświetlić tabelę zawierającą informacje o użyciu bezpłatnych usług.  
    ![Zrzut ekranu przedstawiający użycie bezpłatnych usług](./media/check-free-service-usage/subscription-usage-free-services.png)

Tabela zawiera następujące kolumny:

* **Miernik:** Określa jednostkę miary wykorzystywanej usługi.
* **Użycie/limit:** Użycie i limit dla miernika w bieżącym miesiącu.
* **Stan:** Stan użycia usługi. Na podstawie użycia jest określany jeden z następujących stanów:
  * **Nieużywany:** Nie użyto miernika lub użycie miernika nie dotarło do systemu rozliczeń.
  * **Przekroczono \<Date>:** Przekroczono limit miernika dnia \<Date>.
  * **Małe prawdopodobieństwo przekroczenia:** Przekroczenie limitu dla miernika jest mało prawdopodobne.
  * **Przekracza \<Date>:** Prawdopodobnie nastąpi przekroczenie limitu dla miernika dnia \<Date>.

> [!IMPORTANT]
>
> Bezpłatne usługi są dostępne tylko dla subskrypcji utworzonej podczas tworzenia bezpłatnego konta platformy Azure. Jeśli tabela bezpłatnych usług nie jest widoczna na stronie przeglądu subskrypcji, nie są one dostępne dla subskrypcji.

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami.

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki
- [Uaktualnienie bezpłatnego konta platformy Azure](upgrade-azure-subscription.md)
