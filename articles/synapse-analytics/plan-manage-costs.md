---
title: Planowanie zarządzania kosztami dla Azure Synapse Analytics
description: Dowiedz się, jak planować koszty i zarządzać nimi Azure Synapse Analytics przy użyciu analizy kosztów w Azure Portal.
author: julieMSFT
ms.author: jrasnick
ms.custom: subject-cost-optimization
ms.service: synapse-analytics
ms.subservice: overview
ms.topic: how-to
ms.date: 12/09/2020
ms.openlocfilehash: 15ac6ce6a1a49bbbd15849adec373dd0fcd42c10
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568526"
---
# <a name="plan-and-manage-costs-for-azure-synapse-analytics"></a>Planowanie kosztów dla Azure Synapse Analytics

W tym artykule opisano sposób planowania kosztów dla Azure Synapse Analytics. Najpierw użyj kalkulatora cen platformy Azure, aby zaplanować koszty Azure Synapse przed dodaniem jakichkolwiek zasobów dla usługi w celu oszacowania kosztów. Następnie, podczas dodawania Azure Synapse zasobów, przejrzyj szacowane koszty.

Po zakończeniu korzystania z zasobów Azure Synapse użyj funkcji Cost Management, aby ustawić budżety i monitorować koszty. Możesz również przeglądać prognozowane koszty i identyfikować trendy wydatków, aby zidentyfikować obszary, w których możesz chcieć działać. Koszty dla Azure Synapse to tylko część miesięcznych kosztów na rachunku za korzystanie z platformy Azure. Mimo że w tym artykule wyjaśniono, jak planować koszty usługi Azure Synapse i zarządzać nimi, opłaty są naliczane za wszystkie usługi i zasoby platformy Azure używane w ramach subskrypcji platformy Azure, w tym usługi innych firm.

## <a name="prerequisites"></a>Wymagania wstępne

Analiza kosztów w Cost Management obsługuje większość typów kont platformy Azure, ale nie wszystkie z nich. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Aby wyświetlić dane dotyczące kosztów, musisz mieć co najmniej dostęp do odczytu dla konta platformy Azure. Aby uzyskać informacje na temat przypisywania dostępu do danych usługi Azure Cost Management, zobacz [Przypisywanie dostępu do danych](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-azure-synapse-analytics"></a>Szacowanie kosztów przed użyciem Azure Synapse Analytics

Skorzystaj z [kalkulatora cen platformy Azure,](https://azure.microsoft.com/pricing/calculator/) aby oszacować koszty przed dodaniem Azure Synapse Analytics.

Azure Synapse zasoby, które mają różne opłaty, jak po stronie poniższego oszacowania kosztów. 

![Przykład przedstawiający szacowany koszt w kalkulatorze cen platformy Azure](./media/plan-manage-costs/cost-estimate.png)

## <a name="understand-the-full-billing-model-for-azure-synapse-analytics"></a>Opis pełnego modelu rozliczeń dla Azure Synapse Analytics

Azure Synapse działa w infrastrukturze platformy Azure, która nalicza koszty wraz Azure Synapse podczas wdrażania nowego zasobu. Ważne jest, aby zrozumieć, że dodatkowa infrastruktura może kosztować koszty. Musisz zarządzać tym kosztami, gdy wprowadzasz zmiany w wdrożonych zasobach. 

### <a name="costs-that-typically-accrue-with-azure-synapse-analytics"></a>Koszty, które zazwyczaj są naliczane w Azure Synapse Analytics

Podczas tworzenia zasobów dla Azure Synapse są również tworzone zasoby dla innych usług platformy Azure. Obejmują one:

- Data Lake Storage Gen2

 ### <a name="costs-might-accrue-after-resource-deletion"></a>Koszty mogą być naliczane po usunięciu zasobu

Po usunięciu Azure Synapse zasobów następujące zasoby mogą nadal istnieć. Koszty będą nadal naliczane do momentu ich usunięcia.

- Data Lake Storage Gen2

### <a name="using-azure-prepayment-credit-with-azure-synapse"></a>Używanie środków na przedpłatę za platformę Azure z Azure Synapse 

Opłaty za subskrypcje można Azure Synapse pomocą środków na przedpłatę za platformę Azure (wcześniej nazywanych zobowiązaniem pieniężnym). Nie można jednak użyć środków na przedpłatę za platformę Azure, aby płacić za opłaty za produkty i usługi innych firm, w tym te z Azure Marketplace.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Przeglądanie szacowanych kosztów w witrynie Azure Portal

Podczas tworzenia zasobów dla Azure Synapse Analytics koszty są szacowane. Obszar roboczy ma bez serwera pulę SQL utworzoną za pomocą tego obszaru roboczego. Bez serwera pula SQL nie spowoduje naliczenie opłat, dopóki nie zostaną uruchomione zapytania. Inne zasoby, takie jak dedykowane pule SQL i Apache Spark bez serwera, należy utworzyć w obszarze roboczym.

Aby utworzyć i <ResourceName> wyświetlić szacowaną cenę:

1. Przejdź do usługi w Azure Portal.
2. Utwórz zasób.
3. Przejrzyj szacowaną cenę wyświetlaną w podsumowaniu.
4. Zakończ tworzenie zasobu.

![Przykład przedstawiający szacowane koszty podczas tworzenia zasobu](./media/plan-manage-costs/create-workspace-cost.png)


Jeśli twoja subskrypcja platformy Azure ma limit wydatków, platforma Azure zapobiega wydatkom na kwotę środków. Podczas tworzenia i używania zasobów platformy Azure używane są środki. Po osiągnięciu limitu środków wdrożone zasoby zostaną wyłączone w pozostałej części tego okresu rozliczeniowego. Nie można zmienić limitu środków, ale można go usunąć. Aby uzyskać więcej informacji na temat limitów wydatków, zobacz [Limit wydatków platformy Azure.](../cost-management-billing/manage/spending-limit.md)

## <a name="monitor-costs"></a>Monitorowanie kosztów

Korzystanie z zasobów Azure Synapse wiąże się z kosztami. Koszty jednostek użycia zasobów platformy Azure różnią się w zależności od przedziałów czasu (sekund, minut, godzin i dni) lub użycia jednostek (bajtów, megabajtów i tak dalej). Od razu po rozpoczęciu korzystania z zasobów w Azure Synapse koszty są ponoszone i koszty są dostępne w [analizie kosztów.](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

W przypadku korzystania z analizy kosztów koszty są Azure Synapse Analytics wykresach i tabelach w różnych przedziałach czasu. Niektóre przykłady to według dnia, bieżącego i poprzedniego miesiąca oraz roku. Możesz również wyświetlać koszty względem budżetów i prognozowanych kosztów. Przełączanie się do dłuższych widoków w czasie może ułatwić identyfikowanie trendów wydatków. Zobaczysz też, gdzie mogło wystąpić przesłonienie. Jeśli utworzono budżety, możesz również łatwo sprawdzić, gdzie zostały przekroczone.

Aby wyświetlić Azure Synapse kosztów w analizie kosztów:

1. Zaloguj się w witrynie Azure Portal.
2. Otwórz zakres , subskrypcję lub grupę zasobów, w skrypcie Azure Portal wybierz **pozycję Analiza** kosztów w menu. Na przykład przejdź do pozycji **Subskrypcje,** wybierz subskrypcję z listy, a następnie wybierz pozycję  **Analiza kosztów** w menu. Wybierz **pozycję Zakres,** aby przełączyć się na inny zakres w analizie kosztów.
3. Domyślnie koszt usług jest wyświetlany na pierwszym wykresie pierścieniowym. Wybierz obszar na wykresie z etykietą Azure Synapse.

Rzeczywiste miesięczne koszty są wyświetlane podczas początkowego otwierania analizy kosztów. Oto przykład przedstawiający wszystkie miesięczne koszty użycia.

![Przykład przedstawiający skumulowane koszty dla subskrypcji](./media/plan-manage-costs/actual-monthly-costs.png)

- Aby zawęzić koszty dla jednej usługi, na przykład Azure Synapse, wybierz pozycję **Dodaj filtr,** a następnie wybierz **pozycję Nazwa usługi.** Następnie wybierz pozycję **Azure Synapse Analytics**.

Oto przykład przedstawiający koszty tylko dla Azure Synapse.

![Przykład przedstawiający skumulowane koszty dla nazwy usługi](./media/plan-manage-costs/filtered-monthly-costs.png)

W poprzednim przykładzie zobaczysz bieżący koszt usługi. Wyświetlane są również koszty według regionów (lokalizacji) platformy Azure i Azure Synapse według grupy zasobów. W tym miejscu możesz samodzielnie eksplorować koszty.

## <a name="create-budgets"></a>Tworzenie budżetów

W celu zarządzania kosztami można utworzyć [budżety](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oraz [alerty](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn), które automatycznie powiadamiają uczestników o anomaliach w wydatkach i o ryzyku nadmiernych wydatków. Alerty są oparte na wydatkach w porównaniu z budżetem i progami kosztów. Budżety i alerty są tworzone dla subskrypcji platformy Azure i grup zasobów, więc są one przydatne w ramach ogólnej strategii monitorowania kosztów. 

Budżety można tworzyć przy użyciu filtrów dla określonych zasobów lub usług na platformie Azure, jeśli chcesz mieć większy poziom szczegółowości w monitorowaniu. Filtry pomagają zagwarantować, że nie utworzysz przypadkowo nowych zasobów, które będą kosztować dodatkowe pieniądze. Aby uzyskać więcej informacji na temat opcji filtrowania podczas tworzenia budżetu, zobacz [Opcje grupowania i filtrowania](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Eksportowanie danych kosztów

Możesz również [wyeksportować dane dotyczące kosztów](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) na konto magazynu. Jest to przydatne, gdy potrzebujesz lub innych osób, aby wykonać dodatkową analizę danych dla kosztów. Na przykład zespoły finansowe mogą analizować dane przy użyciu programu Excel lub Power BI. Możesz wyeksportować koszty zgodnie z harmonogramem dziennym, tygodniowym lub miesięcznym i ustawić niestandardowy zakres dat. Eksportowanie danych kosztów jest zalecanym sposobem pobierania zestawów danych kosztów.


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-synapse"></a>Inne sposoby zarządzania kosztami dla Azure Synapse 

### <a name="serverless-sql-pool"></a>Bezserwerowa pula SQL

Aby dowiedzieć się więcej o kosztach bez serwera w puli SQL, zobacz [Zarządzanie kosztami](./sql/data-processed.md) dla bez serwera puli SQL w Azure Synapse Analytics

### <a name="dedicated-sql-pool"></a>Dedykowana pula SQL

Możesz kontrolować koszty dla dedykowanej puli SQL, ws czas wsyłania zasobu, gdy nie jest on w użyciu. Jeśli na przykład nie będziesz używać bazy danych w nocy i w weekendy, możesz ją wstrzymać w tych godzinach i wznowić ją w ciągu dnia. Aby uzyskać więcej informacji, zobacz [Wstrzymywanie i wznawianie obliczeń w dedykowanej puli SQL za pośrednictwem Azure Portal](./sql-data-warehouse/pause-and-resume-compute-portal.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

### <a name="serverless-apache-spark-pool"></a>Bez serwera Apache Spark pula danych

Aby kontrolować koszty dla puli Apache Spark bez serwera, włącz funkcję automatycznego wstrzymywania Apache Spark bez serwera i odpowiednio ustaw wartość limitu czasu.  W przypadku Synapse Studio deweloperskie studio wysyła komunikat keep alive w celu utrzymania aktywności sesji, co jest również konfigurowalne, więc ustaw wartość krótkiego limitu czasu na automatyczne wstrzymanie.  Gdy wszystko będzie gotowe, zamknij sesję, a pula Apache Spark zostanie automatycznie wstrzymana po osiągnięciu wartości limitu czasu.
 
Podczas opracowywania należy utworzyć wiele Apache Spark pul o różnych rozmiarach.  Tworzenie Apache Spark puli jest bezpłatne, a opłaty są naliczane tylko za użycie.  Apache Spark użycia w Azure Synapse są naliczane za godzinę rdzeni wirtualnych i proporcjonalnie do minuty.  Na przykład użyj małych rozmiarów puli do tworzenia i walidacji kodu, używając większych rozmiarów puli do testowania wydajnościowego.


### <a name="data-integration---pipelines-and-data-flows"></a>Integracja danych — potoki i przepływy danych 

Aby dowiedzieć się więcej o kosztach integracji danych, zobacz Planowanie kosztów i [zarządzanie nimi Azure Data Factory](../data-factory/plan-manage-costs.md)

## <a name="next-steps"></a>Następne kroki

- Dowiedz [się, jak zoptymalizować inwestycję w chmurę za pomocą Azure Cost Management.](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Dowiedz się więcej o zarządzaniu kosztami za pomocą [analizy kosztów.](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Dowiedz się, jak [zapobiegać nieoczekiwanym kosztom.](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
- Weź udział [w Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) z przewodnikiem.
- Dowiedz się więcej na temat planowania kosztów [dla](../machine-learning/concept-plan-manage-cost.md) Azure Machine Learning