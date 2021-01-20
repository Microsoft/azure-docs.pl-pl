---
title: Zaplanuj zarządzanie kosztami usługi Azure Synapse Analytics
description: Dowiedz się, jak planować i zarządzać kosztami usługi Azure Synapse Analytics przy użyciu analizy kosztów w Azure Portal.
author: julieMSFT
ms.author: jrasnick
ms.custom: subject-cost-optimization
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 12/09/2020
ms.openlocfilehash: c7a0be6f1d402cc994532ab4bc5a5d0ea39bc8b7
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599039"
---
# <a name="plan-and-manage-costs-for-azure-synapse-analytics"></a>Planowanie i zarządzanie kosztami usługi Azure Synapse Analytics

W tym artykule opisano sposób planowania kosztów usługi Azure Synapse Analytics i zarządzania nimi. Najpierw możesz skorzystać z kalkulatora cen platformy Azure, aby zaplanować koszty usługi Azure Synapse przed dodaniem zasobów usługi do oszacowania kosztów. Następnie podczas dodawania zasobów usługi Azure Synapse Przejrzyj szacowane koszty.

Po rozpoczęciu korzystania z zasobów Synapse platformy Azure Użyj funkcji Cost Management, aby ustawić budżety i monitorować koszty. Możesz również przejrzeć prognozowane koszty i zidentyfikować trendy wydatków, aby identyfikować obszary, w których może być konieczne działanie. Koszty związane z usługą Azure Synapse to tylko część miesięcznych kosztów rachunku na korzystanie z platformy Azure. Chociaż w tym artykule wyjaśniono, jak planować i zarządzać kosztami usługi Azure Synapse, opłaty są naliczane za wszystkie usługi i zasoby platformy Azure używane w ramach subskrypcji platformy Azure, w tym usługi innych firm.

## <a name="prerequisites"></a>Wymagania wstępne

Analiza kosztów w Cost Management obsługuje większość typów kont platformy Azure, ale nie wszystkie z nich. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Aby wyświetlić dane dotyczące kosztów, wymagany jest co najmniej dostęp do odczytu dla konta platformy Azure. Aby uzyskać informacje na temat przypisywania dostępu do danych usługi Azure Cost Management, zobacz [Przypisywanie dostępu do danych](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-synapse-analytics"></a>Oszacuj koszty przed rozpoczęciem korzystania z usługi Azure Synapse Analytics

Skorzystaj z [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) , aby oszacować koszty przed dodaniem usługi Azure Synapse Analytics.

Usługa Azure Synapse ma różne zasoby, które mają różne opłaty, jak w szacunku kosztów poniżej. 

![Przykład przedstawiający szacowany koszt w kalkulatorze cen platformy Azure](./media/plan-manage-costs/cost-estimate.png)

## <a name="understand-the-full-billing-model-for-azure-synapse-analytics"></a>Zapoznaj się z pełnym modelem rozliczeń dla usługi Azure Synapse Analytics

Usługa Azure Synapse działa w infrastrukturze platformy Azure, która narasta koszty wraz z usługą Azure Synapse podczas wdrażania nowego zasobu. Ważne jest, aby zrozumieć, że dodatkowa infrastruktura może naliczać koszt. Ten koszt należy zarządzać po wprowadzeniu zmian w wdrożonych zasobach. 

### <a name="costs-that-typically-accrue-with-azure-synapse-analytics"></a>Koszty, które zwykle naliczane za pomocą usługi Azure Synapse Analytics

Podczas tworzenia zasobów dla usługi Azure Synapse są również tworzone zasoby dla innych usług platformy Azure. Obejmują one:

- Data Lake Storage Gen2

 ### <a name="costs-might-accrue-after-resource-deletion"></a>Koszty mogą naliczane po usunięciu zasobów

Po usunięciu zasobów usługi Azure Synapse następujące zasoby mogą nadal istnieć. W dalszym ciągu naliczane są koszty, dopóki nie zostaną usunięte.

- Data Lake Storage Gen2

### <a name="using-azure-prepayment-credit-with-azure-synapse"></a>Korzystanie z przedpłat z płatnościami na platformie Azure w usłudze Azure Synapse 

Opłaty za usługę Azure Synapse można zapłacić za pomocą środków na korzystanie z platformy Azure (wcześniej nazywanych zobowiązaniami pieniężnymi). Nie można jednak używać środków zaliczkowych na platformie Azure, aby zapłacić za opłaty za produkty i usługi innych firm, w tym te z portalu Azure Marketplace.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Przeglądanie szacowanych kosztów w witrynie Azure Portal

Podczas tworzenia zasobów dla usługi Azure Synapse Analytics zobaczysz szacowane koszty. Obszar roboczy ma pulę SQL bezserwerową utworzoną za pomocą obszaru roboczego. Bezserwerowa Pula SQL nie będzie ponosić opłat do momentu uruchomienia zapytań. Inne zasoby, takie jak dedykowane pule SQL i pule Apache Spark bezserwerowe, będą musiały zostać utworzone w obszarze roboczym.

Aby utworzyć <ResourceName> i wyświetlić szacowaną cenę:

1. Przejdź do usługi w Azure Portal.
2. Utwórz zasób.
3. Zapoznaj się z szacowaną ceną wyświetlaną w podsumowaniu.
4. Zakończ tworzenie zasobu.

![Przykład przedstawia szacowany koszt podczas tworzenia zasobu](./media/plan-manage-costs/create-workspace-cost.png)


Jeśli subskrypcja platformy Azure ma limit wydatków, platforma Azure nie pozwala na korzystanie z wydatków w wysokości. Twoje środki są używane podczas tworzenia i używania zasobów platformy Azure. Po osiągnięciu limitu kredytu wdrożone zasoby są wyłączone dla pozostałej części okresu rozliczeniowego. Nie możesz zmienić limitu kredytowego, ale możesz go usunąć. Aby uzyskać więcej informacji na temat limitów wydatków, zobacz [limit wydatków platformy Azure](../cost-management-billing/manage/spending-limit.md).

## <a name="monitor-costs"></a>Monitorowanie kosztów

W przypadku używania zasobów usługi Azure Synapse są naliczane koszty. Koszty jednostkowe użycia zasobów platformy Azure różnią się w zależności od przedziałów czasu (sekund, minut, godzin i dni) lub według użycia jednostek (bajtów, megabajtów itd.). Po rozpoczęciu korzystania z zasobów w usłudze Azure Synapse są naliczane koszty, a koszty można zobaczyć w [analizie kosztów](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Korzystając z analizy kosztów, można wyświetlać koszty usługi Azure Synapse Analytics w wykresach i tabelach dla różnych interwałów czasu. Kilka przykładów jest dziennie, bieżącego i poprzedniego miesiąca oraz roku. Możesz również wyświetlić koszty związane z budżetami i prognozowanymi kosztami. Przełączenie na dłuższe widoki w czasie może pomóc w identyfikowaniu trendów wydatków. I zobaczysz, gdzie mogły wystąpić przespędzanie. Jeśli utworzono budżety, możesz również łatwo zobaczyć, gdzie są przekraczane.

Aby wyświetlić koszty usługi Azure Synapse w analizie kosztów:

1. Zaloguj się do witryny Azure Portal.
2. Otwórz zakres, subskrypcję lub grupę zasobów w Azure Portal i wybierz pozycję **Analiza kosztów** w menu. Na przykład przejdź do pozycji **subskrypcje**, wybierz subskrypcję z listy, a następnie wybierz pozycję  **Analiza kosztów** w menu. Wybierz **zakres** , aby przełączyć się do innego zakresu w analizie kosztów.
3. Domyślnie koszt usług jest pokazywany na pierwszym wykresie pierścieniowym. Wybierz obszar na wykresie z etykietą Azure Synapse.

Rzeczywiste koszty miesięczne są wyświetlane po pierwszym otwarciu analizy kosztów. Oto przykład pokazujący wszystkie miesięczne koszty użycia.

![Przykład przedstawiający skumulowane koszty subskrypcji](./media/plan-manage-costs/actual-monthly-costs.png)

- Aby zawęzić koszty pojedynczej usługi, np. Azure Synapse, wybierz pozycję **Dodaj filtr** , a następnie wybierz pozycję **nazwa usługi**. Następnie wybierz pozycję **Azure Synapse Analytics**.

Oto przykład przedstawiający koszty tylko usługi Azure Synapse.

![Przykład przedstawiający skumulowane koszty usługi ServiceName](./media/plan-manage-costs/filtered-monthly-costs.png)

W poprzednim przykładzie zobaczysz bieżący koszt usługi. Pokazywane są również koszty według regionów platformy Azure i kosztów usługi Azure Synapse według grupy zasobów. W tym miejscu możesz dowiedzieć się o kosztach własnych.

## <a name="create-budgets"></a>Tworzenie budżetów

W celu zarządzania kosztami można utworzyć [budżety](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oraz [alerty](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn), które automatycznie powiadamiają uczestników o anomaliach w wydatkach i o ryzyku nadmiernych wydatków. Alerty są oparte na wydatkach w porównaniu z budżetem i progami kosztów. Budżety i alerty są tworzone dla subskrypcji platformy Azure i grup zasobów, dzięki czemu są one przydatne jako część ogólnej strategii monitorowania kosztów. 

Budżety można utworzyć za pomocą filtrów dla określonych zasobów lub usług na platformie Azure, jeśli chcesz mieć większą szczegółowość w monitorowaniu. Filtry ułatwiają zagwarantowanie, że nie zostaną przypadkowo utworzone nowe zasoby, które będą kosztowały dodatkowe pieniądze. Aby uzyskać więcej informacji na temat opcji filtrowania podczas tworzenia budżetu, zobacz [Opcje grup i filtrów](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Eksportowanie danych kosztów

Możesz również [wyeksportować dane kosztu](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) do konta magazynu. Jest to przydatne, gdy potrzebujesz lub inne osoby mogą przeprowadzić dodatkową analizę danych pod kątem kosztów. Na przykład zespoły finansowe mogą analizować dane przy użyciu programu Excel lub Power BI. Możesz wyeksportować koszty codziennie, co tydzień lub miesięcznie i ustawić niestandardowy zakres dat. Eksportowanie danych o kosztach jest zalecanym sposobem na pobranie z nich zbiorów.


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-synapse"></a>Inne sposoby zarządzania i zmniejszania kosztów usługi Azure Synapse 

### <a name="serverless-sql-pool"></a>Bezserwerowa pula SQL

Aby dowiedzieć się więcej o kosztach puli SQL bezserwerowej, zobacz [Zarządzanie kosztami dla puli SQL bezserwerowej w usłudze Azure Synapse Analytics](./sql/data-processed.md)

### <a name="dedicated-sql-pool"></a>Dedykowana pula SQL

Możesz kontrolować koszty dedykowanej puli SQL, wstrzymując zasób, gdy nie jest on używany. Na przykład jeśli baza danych nie będzie używana w porze nocnej i w weekendy, możesz ją wstrzymać w tych godzinach i wznowić ją w ciągu dnia. Aby uzyskać więcej informacji [, zobacz Wstrzymywanie i wznawianie obliczeń w dedykowanej puli SQL za pomocą Azure Portal](./sql-data-warehouse/pause-and-resume-compute-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

<!-- ### Serverless Apache Spark pool -->

### <a name="data-integration---pipelines-and-data-flows"></a>Integracja danych — potoki i przepływy danych 

Aby dowiedzieć się więcej o kosztach integracji danych [, zobacz Planowanie i zarządzanie kosztami Azure Data Factory](../data-factory/plan-manage-costs.md)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak zoptymalizować inwestycję w chmurę za pomocą Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Dowiedz się więcej o zarządzaniu kosztami przy użyciu [analizy kosztów](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Dowiedz się więcej na temat [zapobiegania nieoczekiwanym kosztom](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Zapoznaj się z [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) kurs uczenia z przewodnikiem.
- Dowiedz się więcej o planowaniu i zarządzaniu kosztami [Azure Machine Learning](../machine-learning/concept-plan-manage-cost.md)