---
title: Planowanie zarządzania kosztami dla Azure Logic Apps
description: Dowiedz się, jak planować koszty dla Azure Logic Apps i zarządzać nimi przy użyciu analizy kosztów w Azure Portal
ms.service: logic-apps
ms.reviewer: estfan, logicappspm, azla
ms.topic: how-to
ms.custom: subject-cost-optimization
ms.date: 03/24/2021
ms.openlocfilehash: ec2e1098df4c21704ee7c17852b893630cd3fd27
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107761821"
---
# <a name="plan-and-manage-costs-for-azure-logic-apps"></a>Planowanie kosztów dla Azure Logic Apps

Ten artykuł ułatwia planowanie kosztów związanych z Azure Logic Apps. Przed utworzeniem lub dodaniu zasobów przy użyciu tej usługi ososzacowania kosztów przy użyciu kalkulatora cen platformy Azure. Po rozpoczęciu korzystania z Logic Apps zasobów można ustawić budżety i monitorować koszty przy użyciu [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Aby zidentyfikować obszary, w których możesz chcieć działać, możesz również przejrzeć prognozowane koszty i monitorować trendy wydatków.

Należy pamiętać, że koszty Logic Apps są tylko częścią miesięcznych kosztów na rachunku za platformę Azure. Mimo że w tym artykule wyjaśniono, jak oszacować koszty usługi Logic Apps i zarządzać nimi, opłaty są naliczane za wszystkie usługi i zasoby platformy Azure używane w ramach subskrypcji platformy Azure, w tym za usługi innych firm. Po zapoznaniu się z zarządzaniem kosztami usługi Logic Apps można zastosować podobne metody zarządzania kosztami dla wszystkich usług platformy Azure używanych w ramach subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

<!--Note for Azure service writer: This section covers prerequisites for the Cost Management's Cost Analysis feature. Add other prerequisites needed for your service after the Cost Management prerequisites. -->

[Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) obsługuje większość typów kont platformy Azure. Aby wyświetlić wszystkie obsługiwane typy kont, zobacz [Understand Cost Management data (Opis Cost Management danych).](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) Aby wyświetlić dane kosztów, potrzebujesz przynajmniej dostępu do odczytu dla Twojego konta platformy Azure.

Aby uzyskać informacje na temat przypisywania dostępu do danych usługi Azure Cost Management, zobacz [Przypisywanie dostępu do danych](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, add them here -->

<a name="understand-billing-model"></a>

## <a name="understand-the-billing-model"></a>Informacje o modelu rozliczeń

Azure Logic Apps działa w infrastrukturze platformy [Azure, która nalicza koszty](https://azure.microsoft.com/pricing/details/logic-apps/) podczas wdrażania nowych zasobów. Upewnij się, że rozumiesz model rozliczeń dla usługi [Logic Apps oraz](logic-apps-pricing.md)powiązane zasoby platformy Azure i zarządzasz kosztami spowodowanymi tymi zależnościami podczas zmieniania wdrożonych zasobów.

<a name="typical-costs"></a>

### <a name="costs-that-typically-accrue-with-azure-logic-apps"></a>Koszty, które zazwyczaj są naliczane w Azure Logic Apps

Usługa Logic Apps stosuje różne modele cen w zależności od zasobów, które tworzysz i używasz:

* Zasoby aplikacji logiki, które tworzysz i uruchamiasz w wielodostępnej usłudze Logic Apps używają [modelu cenowego użycia.](../logic-apps/logic-apps-pricing.md#consumption-pricing)

* Zasoby aplikacji logiki, które tworzysz i uruchamiasz w środowisku usługi integracji [(ISE),](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) korzystają ze [stałego modelu cenowego.](../logic-apps/logic-apps-pricing.md#fixed-pricing)

Poniżej znajdują się inne zasoby, które poniesieją koszty podczas tworzenia ich do użycia z aplikacjami logiki:

* Konto [integracji to](../logic-apps/logic-apps-pricing.md#integration-accounts) oddzielny zasób, który tworzysz i łączysz z aplikacjami logiki w celu tworzenia integracji B2B. Konta integracji używają [stałego modelu cenowego,](../logic-apps/logic-apps-pricing.md#integration-accounts) w którym stawka jest oparta na typie lub warstwie *konta* integracji, którego używasz.

* IsE [to](../logic-apps/logic-apps-pricing.md#fixed-pricing) oddzielny zasób, który można utworzyć jako lokalizację wdrożenia dla aplikacji logiki, które wymagają bezpośredniego dostępu do zasobów w sieci wirtualnej. W przypadku platform ISE jest oparty [na](../logic-apps/logic-apps-pricing.md#fixed-pricing) modelu cen stałym, w którym stawka jest oparta na tworzyć i innych ustawieniach.

* Łącznik [niestandardowy to](../logic-apps/logic-apps-pricing.md#consumption-pricing) oddzielny zasób utworzony dla interfejsu API REST, który nie ma wstępnie utworzonego łącznika do użycia w aplikacjach logiki. Wykonania łączników niestandardowych używają [modelu cenowego użycia,](../logic-apps/logic-apps-pricing.md#consumption-pricing) z wyjątkiem sytuacji, gdy są używani w programie ISE.

* W przypadku wielodostępnej usługi Logic Apps przechowywanie [danych](../logic-apps/logic-apps-pricing.md#data-retention) i użycie magazynu nalicza koszty przy użyciu modelu [cen stałych.](../logic-apps/logic-apps-pricing.md#fixed-pricing) Na przykład dane wejściowe i wyjściowe z historii uruchamiania są przechowywane w magazynie w tle, który różni się od zasobów magazynu, które są niezależnie tworzyć i zarządzać aplikacją logiki oraz do nich dostęp.

  W przypadku isE przechowywanie danych i zużycie magazynu nie wiąże się z kosztami.

<a name="costs-after-resource-deletion"></a>

### <a name="costs-might-accrue-after-resource-deletion"></a>Koszty mogą być naliczane po usunięciu zasobów

<!--Note to Azure service writer: You might need to sync with your product team to identify resources that continue to exist after those ones for your service are deleted. If you're certain that no resources can exist after those for your service are deleted, then omit this section. -->

Po usunięciu aplikacji logiki usługa Logic Apps nie będzie tworzyć ani uruchamiać nowych wystąpień przepływu pracy. Jednak wszystkie przebiegi w toku i oczekujące będą kontynuowane do momentu ich zakończenia. W zależności od liczby tych przebiegów ten proces może zająć trochę czasu. Aby uzyskać więcej informacji, zobacz [Zarządzanie aplikacjami logiki.](manage-logic-apps-with-azure-portal.md#delete-logic-apps)

Jeśli masz te zasoby po usunięciu aplikacji logiki, te zasoby nadal istnieją i naliczają koszty do momentu ich usunięcia:

* Zasoby platformy Azure, które tworzysz i zarządzasz niezależnie od aplikacji logiki łączącej się z tymi zasobami, na przykład aplikacje funkcji platformy Azure, centra zdarzeń, siatki zdarzeń i tak dalej

* Konta integracji

* Środowiska usług integracji (ISE)

  W przypadku [usunięcia środowiska ISE](ise-manage-integration-service-environment.md#delete-ise)skojarzona sieć wirtualna platformy Azure, podsieci i inne powiązane zasoby nadal istnieją. Po usunięciu środowiska ISE może być konieczne odczekanie określonej liczby godzin, zanim będzie można spróbować usunąć sieć wirtualną lub podsieci.

### <a name="using-monetary-credit-with-azure-logic-apps"></a>Używanie środków pieniężnych z Azure Logic Apps

Możesz płacić za Azure Logic Apps za pomocą środków na zobowiązanie pieniężne w ramach umowy EA. Nie można jednak użyć środków na zobowiązanie pieniężne w ramach umowy EA, aby płacić za opłaty za produkty i usługi innych firm, w tym te z Azure Marketplace.

<a name="estimate-costs"></a>

## <a name="estimate-costs"></a>Szacowanie kosztów

Przed utworzeniem zasobów za pomocą Azure Logic Apps ososzacowania kosztów przy użyciu [kalkulatora cen platformy Azure.](https://azure.microsoft.com/pricing/calculator/) Aby uzyskać więcej informacji, zapoznaj się z [tematem Model cen Azure Logic Apps](../logic-apps/logic-apps-pricing.md).

1. Na stronie [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/)z menu po lewej stronie wybierz pozycję **Integration**  >  **Azure Logic Apps**.

   ![Zrzut ekranu przedstawiający kalkulator cen platformy Azure z wybraną Azure Logic Apps "azure".](./media/plan-manage-costs/add-azure-logic-apps-pricing-calculator.png)

1. Przewiń stronę w dół do momentu wyświetlenia kalkulatora Azure Logic Apps cen. W różnych sekcjach dotyczących zasobów platformy Azure, które są bezpośrednio związane z usługą Azure Logic Apps, wprowadź liczbę zasobów, które mają być stosowane, oraz liczbę interwałów, w których można używać tych zasobów.

   Ten zrzut ekranu przedstawia przykładowe oszacowanie kosztów przy użyciu kalkulatora:

   ![Przykład przedstawiający szacowany koszt w kalkulatorze cen platformy Azure](./media/plan-manage-costs/example-logic-apps-pricing-calculator.png)

1. Aby zaktualizować szacowane koszty podczas tworzenia i używania nowych powiązanych zasobów, wróć do tego kalkulatora i zaktualizuj te zasoby tutaj.

<a name="create-budgets-alerts"></a>

## <a name="create-budgets-and-alerts"></a>Tworzenie budżetów i alertów

Aby ułatwić aktywne zarządzanie kosztami dla konta platformy [](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) Azure lub [](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) subskrypcji, możesz tworzyć budżety i alerty przy użyciu usługi Azure Cost Management and Billing [i](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) możliwości.  Budżety i alerty są tworzone dla subskrypcji platformy Azure i grup zasobów, więc są one przydatne w ramach ogólnej strategii monitorowania kosztów.

Na podstawie wydatków w porównaniu z progami budżetu i kosztów alerty automatycznie powiadamiają uczestników projektu o anomaliach wydatków i ryzykach związanych z przekroczeniem wydatków. Jeśli potrzebujesz większej szczegółowości monitorowania, możesz również utworzyć budżety, które używają filtrów dla określonych zasobów lub usług na platformie Azure. Filtry pomagają upewnić się, że nie utworzysz przypadkowo nowych zasobów, które kosztują dodatkowe pieniądze. Aby uzyskać więcej informacji na temat opcji filtrowania, zobacz [Opcje grupowania i filtrowania.](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<a name="monitor-costs"></a>

## <a name="monitor-costs"></a>Monitorowanie kosztów

Koszty jednostek użycia zasobów różnią się w zależności od przedziałów czasu, takich jak sekundy, minuty, godziny i dni, lub według użycia jednostek, takich jak bajty, megabajty i tak dalej. Niektóre przykłady to według dnia, bieżącego i poprzedniego miesiąca oraz roku. Przełączanie się do dłuższych widoków w czasie może ułatwić identyfikowanie trendów wydatków. Korzystając z funkcji analizy kosztów, można wyświetlać koszty jako wykresy i tabele w różnych przedziałach czasu. Jeśli utworzono budżety i prognozy kosztów, możesz również łatwo znaleźć miejsca, w których budżety zostały przekroczone i mogło wystąpić przekroczenie wydatków.

Po rozpoczęciu ponoszenia kosztów zasobów, które tworzą lub zaczynają używać na platformie Azure, można przeglądać i monitorować te koszty w następujące sposoby:

* [Monitorowanie wykonań aplikacji logiki i użycia magazynu](#monitor-billing-metrics) przy użyciu Azure Monitor

* Uruchamianie [analizy kosztów](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) przy użyciu [Azure Cost Management and Billing](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<a name="monitor-billing-metrics"></a>

### <a name="monitor-logic-app-executions-and-storage-consumption"></a>Monitorowanie wykonań aplikacji logiki i użycia magazynu

Za Azure Monitor można wyświetlić te metryki dla określonej aplikacji logiki:

* Rozliczane wykonania akcji
* Rozliczane wykonania wyzwalacza
* Rozliczanie użycia dla wykonań operacji natywnych
* Rozliczanie użycia dla wykonań łącznika standardowego
* Rozliczanie użycia magazynu
* Łączna liczba wykonań rozliczanych

<a name="execution-storage-metrics"></a>

#### <a name="view-execution-and-storage-consumption-metrics"></a>Wyświetlanie metryk użycia magazynu i wykonywania

1. W Azure Portal znajdź i otwórz aplikację logiki. W menu aplikacji logiki w obszarze **Monitorowanie** wybierz **pozycję Metryki**.

1. W okienku po prawej stronie w obszarze **Tytuł** wykresu na pasku metryki otwórz listę Metryka i wybierz odpowiednią metrykę. 

   > [!NOTE]
   > Użycie magazynu jest mierzone jako liczba jednostek magazynu (GB), które są używane i rozliczane przez aplikację logiki. Przebiegi, które używają mniej niż 500 MB w magazynie, mogą nie być wyświetlane w widoku monitorowania, ale nadal są rozliczane.

   ![Zrzut ekranu przedstawiający okienko Metryki z otwartą listą "Metryka".](./media/logic-apps-pricing/select-metric.png)

1. W prawym górnym rogu okienka wybierz odpowiedni okres.

1. Aby wyświetlić inne dane użycia magazynu, w szczególności rozmiary danych wejściowych i wyjściowych akcji w historii uruchamiania aplikacji logiki, [wykonaj następujące kroki.](#view-input-output-sizes)

<a name="view-input-output-sizes"></a>

#### <a name="view-action-input-and-output-sizes-in-run-history"></a>Wyświetlanie rozmiarów danych wejściowych i wyjściowych akcji w historii uruchamiania

1. W Azure Portal znajdź i otwórz aplikację logiki.

1. W menu aplikacji logiki wybierz pozycję **Przegląd.**

1. W okienku po prawej stronie w obszarze **Historia** przebiegów wybierz przebieg, który zawiera dane wejściowe i wyjściowe, które chcesz wyświetlić.

1. W **obszarze Uruchomienie aplikacji logiki** wybierz pozycję Uruchom **szczegóły.**

1. W **okienku Szczegóły** uruchomienia aplikacji logiki w tabeli akcji, która zawiera listę stanu i czasu trwania każdej akcji, wybierz akcję, którą chcesz wyświetlić.

1. W **okienku Akcja aplikacji** logiki znajdź rozmiary danych wejściowych i wyjściowych tej akcji. W **obszarze Link inputs** (Dane wejściowe) **i Outputs (Dane** wyjściowe) znajdź linki do tych danych wejściowych i wyjściowych.

   > [!NOTE]
   > W przypadku pętli tylko akcje najwyższego poziomu pokazują rozmiary danych wejściowych i wyjściowych. W przypadku akcji wewnątrz zagnieżdżonych pętli dane wejściowe i wyjściowe pokazują zero rozmiaru i brak linków.

<a name="run-cost-analysis"></a>

### <a name="run-cost-analysis-by-using-azure-cost-management-and-billing"></a>Uruchamianie analizy kosztów przy użyciu Azure Cost Management and Billing

Aby przejrzeć koszty usługi Logic Apps na podstawie określonego zakresu, na przykład subskrypcji platformy [](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) Azure, możesz użyć funkcji analizy kosztów w [usłudze Azure Cost Management and Billing](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

1. W Azure Portal otwórz zakres, taki jak subskrypcja platformy Azure. W menu po lewej stronie w **obszarze Cost Management** wybierz pozycję **Analiza kosztów.**

   Przy pierwszym otwarciu okienka analiza kosztów górny wykres przedstawia rzeczywiste i prognozowane koszty użycia dla wszystkich usług w subskrypcji dla bieżącego miesiąca.

   ![Zrzut ekranu przedstawiający Azure Portal i analizy kosztów z przykładem kosztów rzeczywistych i prognozowanych w subskrypcji.](./media/plan-manage-costs/cost-analysis-total-actual-forecasted-costs.png)

   > [!TIP]
   > Aby zmienić zakresy, w **okienku Analiza** kosztów na pasku filtrów wybierz **filtr** Zakres. W **okienku Wybierz** zakres przejdź do odpowiedniego zakresu.

   Poniżej wykresy pierścieniowe pokazują bieżące koszty według usług platformy Azure, regionu (lokalizacji) platformy Azure i grupy zasobów.

   ![Zrzut ekranu przedstawiający Azure Portal i analizy kosztów z przykładowymi wykresami pierścieniowymi dla usług, regionów i grup zasobów.](./media/plan-manage-costs/cost-analysis-donut-charts.png)

1. Aby odfiltrować wykres do określonego obszaru, takiego jak usługa lub zasób, na pasku filtrów wybierz **pozycję Dodaj filtr**.

1. Z listy po lewej stronie wybierz typ filtru, na przykład **Nazwa usługi**. Z listy po prawej stronie wybierz filtr, na przykład aplikacje **logiki**. Gdy wszystko będzie gotowe, wybierz zielony znacznik wyboru.

   ![Zrzut ekranu przedstawiający Azure Portal i analizy kosztów z wyborami filtrów.](./media/plan-manage-costs/cost-analysis-add-service-name-filter.png)

   Na przykład poniżej znajduje się wynik dla Logic Apps service:

   ![Zrzut ekranu przedstawiający Azure Portal i analizy kosztów z wynikami odfiltrowanych w "aplikacjach logiki".](./media/plan-manage-costs/cost-analysis-total-actual-costs-service.png)

### <a name="export-cost-data"></a>Eksportowanie danych kosztów

Gdy musisz wykonać więcej analizy danych dotyczących kosztów, możesz [wyeksportować dane dotyczące kosztów](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) na konto magazynu. Na przykład zespół finansowy może analizować te dane przy użyciu programu Excel lub Power BI. Koszty można eksportować zgodnie z dziennym, tygodniowym lub comiesięcznym harmonogramem i ustawić niestandardowy zakres dat. Eksportowanie danych kosztów jest zalecanym sposobem pobierania zestawów danych kosztów.

## <a name="other-ways-to-manage-and-reduce-costs"></a>Inne sposoby zarządzania kosztami i ich obniżenia

<!-- Note to Azure service writer: This section is optional. Other than using the Cost Management methods above, your service probably has other specific ways to minimize costs. For example, you might have best practice advice or specific ways to reduce costs that are specific to your service. If so, try to add that guidance here or at least summarize key points. Try to be as prescriptive as possible. If you have more comprehensive content, add links to your other published articles or sections here.

Add a statement that discusses any recommended settings for your service that might help keep the charges minimal if a service isn't being actively used by the customer. For example: Will turning off a VM help to get no charges for the specific VM resource?

Otherwise, if no other cost-saving recommendations or best practices exist to reduce costs, cut this section.
-->

Aby ułatwić obniżenie kosztów logiki i powiązanych zasobów, wypróbuj następujące opcje:

* Jeśli to możliwe, [użyj wbudowanych wyzwalaczy](../connectors/built-in.md)i akcji , które kosztują mniej do uruchomienia na wykonanie niż wyzwalacze i akcje [łącznika zarządzanego.](../connectors/managed.md)

  Na przykład możesz zmniejszyć koszty podczas uzyskiwania dostępu do innych zasobów przy użyciu akcji [HTTP](../connectors/connectors-native-http.md) lub wywołując funkcję utworzoną przy użyciu usługi [Azure Functions](../azure-functions/functions-overview.md) i używając wbudowanej akcji [Azure Functions](../logic-apps/logic-apps-azure-functions.md). Jednak użycie Azure Functions również wiąże się z kosztami, dlatego należy porównać opcje.

* [Określ dokładne warunki wyzwalacza](logic-apps-workflow-actions-triggers.md#trigger-conditions) do uruchamiania przepływu pracy.

  Można na przykład określić, że wyzwalacz jest wyzwalany tylko wtedy, gdy docelowa witryna internetowa zwraca wewnętrzny błąd serwera. W definicji JSON wyzwalacza użyj właściwości , aby określić warunek, który odwołuje się do `conditions` kodu stanu wyzwalacza.

* Jeśli wyzwalacz ma wersję sondowania i wersję webhook, wypróbuj wersję webhook, która czeka na uruchomienie określonego zdarzenia przed uruchomieniem, zamiast regularnie sprawdzać zdarzenie.

* Wywołaj aplikację logiki za pośrednictwem innej usługi, aby wyzwalacz był uruchamiany tylko wtedy, gdy przepływ pracy powinien zostać uruchomiony.

  Możesz na przykład wywołać aplikację logiki z funkcji, która została tworzyć i uruchamiać przy użyciu Azure Functions usługi. Na przykład zobacz [Wywoływanie lub wyzwalanie aplikacji logiki przy użyciu Azure Functions i Azure Service Bus](logic-apps-scenario-function-sb-trigger.md).

* [Wyłącz aplikacje logiki,](manage-logic-apps-with-azure-portal.md#disable-or-enable-logic-apps) które nie muszą być stale uruchamiane, lub usuń aplikacje [logiki,](manage-logic-apps-with-azure-portal.md#delete-logic-apps) które nie są już potrzebne. Jeśli to możliwe, wyłącz wszystkie inne zasoby, które nie są stale aktywne.

## <a name="next-steps"></a>Następne kroki

* [Optymalizowanie inwestycji w chmurę za pomocą usługi Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Zarządzanie kosztami przy użyciu analizy kosztów](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Zapobieganie powstawaniu nieoczekiwanych kosztów](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* Weź udział [w kursie Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) nauka z przewodnikiem