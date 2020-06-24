---
title: Planowanie i zarządzanie kosztami Azure Cosmos DB
description: Dowiedz się, jak planować i zarządzać kosztami Azure Cosmos DB przy użyciu analizy kosztów w programie Azure Portal.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/02/2020
ms.openlocfilehash: 42421f745759d9aee75b285c3fbc6ea7217ba5c0
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2020
ms.locfileid: "85112705"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Planowanie i zarządzanie kosztami Azure Cosmos DB

W tym artykule opisano sposób planowania kosztów Azure Cosmos DB i zarządzania nimi. Najpierw użyj kalkulatora pojemności Azure Cosmos DB, aby ułatwić planowanie kosztów przed dodaniem zasobów. Następnie podczas dodawania zasobów platformy Azure można przejrzeć szacowane koszty. Po rozpoczęciu korzystania z Azure Cosmos DB zasobów Skorzystaj z funkcji zarządzania kosztami, aby ustawić budżety i monitorować koszty. Możesz również przejrzeć przewidywane koszty i zidentyfikować trendy wydatków, aby identyfikować obszary, w których może być konieczne działanie.

Należy zrozumieć, że koszty Azure Cosmos DB są tylko częścią miesięcznych kosztów rachunku na korzystanie z platformy Azure. Jeśli używasz innych usług platformy Azure, opłaty są naliczane za wszystkie usługi i zasoby platformy Azure używane w ramach subskrypcji platformy Azure, w tym usługi innych firm. W tym artykule wyjaśniono, jak planować i zarządzać kosztami Azure Cosmos DB. Po zapoznaniu się z zarządzaniem kosztami Azure Cosmos DB możesz zastosować podobne metody, aby zarządzać kosztami wszystkich usług platformy Azure używanych w ramach subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

Analiza kosztów obsługuje różne typy kont platformy Azure. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md). Aby wyświetlić dane kosztów, potrzebujesz przynajmniej dostępu do odczytu dla Twojego konta platformy Azure. Aby uzyskać informacje na temat przypisywania dostępu do danych usługi Azure Cost Management, zobacz [Przypisywanie dostępu do danych](../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="review-estimated-costs-with-capacity-calculator"></a>Przeglądanie szacowanych kosztów za pomocą kalkulatora pojemności

Skorzystaj z [kalkulatora pojemności Azure Cosmos DB](https://cosmos.azure.com/capacitycalculator/) , aby oszacować koszty przed utworzeniem zasobów na koncie usługi Azure Cosmos. Kalkulator pojemności służy do uzyskiwania szacunku wymaganej przepływności i kosztu obciążenia. Skonfigurowanie baz danych i kontenerów platformy Azure Cosmos z właściwą ilością przepływności lub [jednostek żądań (ru/s)](request-units.md)dla obciążenia jest niezbędne w celu zoptymalizowania kosztów i wydajności. Musisz wprowadzić szczegóły, takie jak typ interfejsu API, liczba regionów, rozmiar elementu, żądania odczytu/zapisu na sekundę, Łączna ilość przechowywanych danych w celu uzyskania oszacowania kosztów. Aby dowiedzieć się więcej na temat kalkulatora pojemności, zobacz artykuł dotyczący [szacowania](estimate-ru-with-capacity-planner.md) .

Poniższy zrzut ekranu przedstawia przepływność i oszacowanie kosztów za pomocą kalkulatora pojemności:

:::image type="content" source="./media/plan-manage-costs/capacity-calculator-cost-estimate.png" alt-text="Oszacowanie kosztów w Azure Cosmos DB Kalkulator pojemności":::

## <a name="review-estimated-costs-from-the-azure-portal"></a>Przejrzyj szacowane koszty z Azure Portal

Podczas tworzenia zasobów Azure Cosmos DB z Azure Portal można zobaczyć szacowane koszty. Aby zapoznać się z oszacowaniem kosztów, wykonaj następujące kroki:

1. Zaloguj się do Azure Portal i przejdź do swojego konta usługi Azure Cosmos.
1. Przejdź do **Eksplorator danych**.
1. Utwórz nowy kontener, taki jak kontener grafu.
1. Wprowadź przepływność wymaganą dla obciążenia, np. 400 RU/s. Po wprowadzeniu wartości przepływności można zobaczyć oszacowanie cen, jak pokazano na poniższym zrzucie ekranu:

   :::image type="content" source="./media/plan-manage-costs/cost-estimate-portal.png" alt-text="Szacowanie kosztów w Azure Portal":::

Jeśli subskrypcja platformy Azure ma limit wydatków, platforma Azure nie pozwala na korzystanie z wydatków w wysokości. Twoje środki są używane podczas tworzenia i używania zasobów platformy Azure. Po osiągnięciu limitu kredytowego wdrożone zasoby są wyłączone dla pozostałej części okresu rozliczeniowego. Nie możesz zmienić limitu kredytowego, ale możesz go usunąć. Aby uzyskać więcej informacji na temat limitów wydatków, zobacz [limit wydatków platformy Azure](../billing/billing-spending-limit.md).

## <a name="use-budgets-and-cost-alerts"></a>Używanie alertów dotyczących budżetu i kosztów

W celu zarządzania kosztami można utworzyć [budżety](../cost-management/tutorial-acm-create-budgets.md) oraz alerty, które automatycznie powiadamiają uczestników o anomaliach w wydatkach i o ryzyku nadmiernych wydatków. Alerty są oparte na wydatkach w porównaniu z budżetem i progami kosztów. Budżety i alerty są tworzone dla subskrypcji platformy Azure i grup zasobów, dzięki czemu są one przydatne jako część ogólnej strategii monitorowania kosztów. Mogą jednak mieć ograniczoną funkcjonalność do zarządzania poszczególnymi kosztami usług platformy Azure, takimi jak koszt Azure Cosmos DB, ponieważ są one przeznaczone do śledzenia kosztów na wyższym poziomie.

## <a name="monitor-costs"></a>Monitorowanie kosztów

Gdy korzystasz z zasobów z Azure Cosmos DB, naliczane są koszty. Koszty jednostkowe użycia zasobów różnią się w zależności od przedziałów czasu (sekundy, minuty, godziny i dni) lub według użycia jednostki żądania. Po rozpoczęciu korzystania z Azure Cosmos DB koszty są naliczane i można je wyświetlić w okienku [Analiza kosztów](../cost-management/quick-acm-cost-analysis.md) w Azure Portal.

Korzystając z analizy kosztów, można wyświetlić Azure Cosmos DB koszty w wykresach i tabelach dla różnych przedziałów czasu. Kilka przykładów jest dziennie, bieżących, poprzedniego miesiąca i roku. Możesz również wyświetlić koszty związane z budżetami i prognozowanymi kosztami. Przełączenie na dłuższe widoki w czasie może pomóc w identyfikowaniu trendów wydatków i sprawdzaniu, gdzie mogły wystąpić nadwyżki. Jeśli utworzono budżety, możesz również łatwo zobaczyć miejsce ich przekroczenia. Aby wyświetlić koszty Azure Cosmos DB w analizie kosztów:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Otwórz okno **Cost Management + rozliczanie** , wybierz pozycję **Zarządzanie kosztami** z menu, a następnie wybierz pozycję **Analiza kosztów**. Następnie można zmienić zakres dla określonej subskrypcji z listy rozwijanej **zakres** .

1. Domyślnie koszt wszystkich usług jest pokazywany na pierwszym wykresie pierścieniowym. Wybierz obszar na wykresie z etykietą "Azure Cosmos DB".

1. Aby zawęzić koszty pojedynczej usługi, takiej jak Azure Cosmos DB, wybierz pozycję **Dodaj filtr** , a następnie wybierz pozycję **nazwa usługi**. Następnie wybierz **Azure Cosmos DB** z listy. Oto przykład przedstawiający koszty tylko Azure Cosmos DB:
 
   :::image type="content" source="./media/plan-manage-costs/cost-analysis-pane.png" alt-text="Monitorowanie kosztów za pomocą okienka analiza kosztów":::

W poprzednim przykładzie zobaczysz bieżący koszt dla Azure Cosmos DB w miesiącu lutego. Wykresy zawierają również koszty Azure Cosmos DB według lokalizacji i grupy zasobów.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej na temat sposobu działania cen w Azure Cosmos DB:

* [Model cen w usłudze Azure Cosmos DB](how-pricing-works.md)
* [Optymalizacja zaaprowizowanej przepływności w usłudze Azure Cosmos DB](optimize-cost-throughput.md)
* [Optymalizacja kosztów zapytania w usłudze Azure Cosmos DB](optimize-cost-queries.md)
* [Optymalizuj koszt magazynu w Azure Cosmos DB](optimize-cost-storage.md)