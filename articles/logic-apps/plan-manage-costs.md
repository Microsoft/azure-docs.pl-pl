---
title: Zaplanuj zarządzanie kosztami Azure Logic Apps
description: Dowiedz się, jak planować i zarządzać kosztami Azure Logic Apps przy użyciu analizy kosztów w Azure Portal
ms.service: logic-apps
ms.reviewer: estfan, logicappspm, azla
ms.topic: how-to
ms.custom: subject-cost-optimization
ms.date: 01/29/2021
ms.openlocfilehash: 44351497ed58c8d49404c094f6800b52186edabb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101709635"
---
# <a name="plan-and-manage-costs-for-azure-logic-apps"></a>Planowanie i zarządzanie kosztami Azure Logic Apps

Ten artykuł ułatwia planowanie kosztów Azure Logic Apps i zarządzanie nimi. Przed utworzeniem lub dodaniem zasobów przy użyciu tej usługi Oszacuj koszty przy użyciu kalkulatora cen platformy Azure. Po rozpoczęciu korzystania z Logic Apps zasobów można ustawić budżety i monitorować koszty przy użyciu [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Aby identyfikować obszary, w których można było podjąć działania, możesz również przejrzeć przewidywane koszty i monitorować trendy wydatków.

Należy pamiętać, że koszty Logic Apps są tylko częścią miesięcznych kosztów rachunku na korzystanie z platformy Azure. Chociaż w tym artykule wyjaśniono, jak oszacować koszty Logic Apps i zarządzać nimi, opłaty są naliczane za wszystkie usługi i zasoby platformy Azure używane w ramach subskrypcji platformy Azure, w tym wszystkie usługi innych firm. Po zapoznaniu się z zarządzaniem kosztami Logic Apps możesz zastosować podobne metody, aby zarządzać kosztami wszystkich usług platformy Azure używanych w ramach subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

<!--Note for Azure service writer: This section covers prerequisites for the Cost Management's Cost Analysis feature. Add other prerequisites needed for your service after the Cost Management prerequisites. -->

[Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) obsługuje większość typów kont platformy Azure. Aby wyświetlić wszystkie obsługiwane typy kont, zobacz [opis Cost Management danych](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Aby wyświetlić dane kosztów, potrzebujesz przynajmniej dostępu do odczytu dla Twojego konta platformy Azure.

Aby uzyskać informacje na temat przypisywania dostępu do danych usługi Azure Cost Management, zobacz [Przypisywanie dostępu do danych](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, add them here -->

<a name="understand-billing-model"></a>

## <a name="understand-the-billing-model"></a>Omówienie modelu rozliczeń

Azure Logic Apps działa w infrastrukturze platformy Azure, która [naliczy koszty](https://azure.microsoft.com/pricing/details/logic-apps/) podczas wdrażania nowych zasobów. Należy zapoznać się z [modelem rozliczeń dla usługi Logic Apps wraz z powiązanymi zasobami platformy Azure](logic-apps-pricing.md)i zarządzać kosztami związanymi z tymi zależnościami w przypadku wprowadzania zmian w wdrożonych zasobach.

<a name="typical-costs"></a>

### <a name="costs-that-typically-accrue-with-azure-logic-apps"></a>Koszty, które zwykle naliczane są przy użyciu Azure Logic Apps

Usługa Logic Apps stosuje różne modele cenowe w oparciu o zasoby, które tworzysz i używają:

* Zasoby aplikacji logiki tworzone i uruchamiane w ramach usługi Logic Apps z wieloma dzierżawcami używają [modelu cen zużycia](../logic-apps/logic-apps-pricing.md#consumption-pricing).

* Zasoby aplikacji logiki tworzone i uruchamiane w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) używają [stałego modelu cen](../logic-apps/logic-apps-pricing.md#fixed-pricing).

Poniżej znajdują się inne zasoby, które ponoszą koszty podczas tworzenia ich do użycia z usługą Logic Apps:

* [Konto integracji](../logic-apps/logic-apps-pricing.md#integration-accounts) to osobny zasób tworzony i połączony z usługą Logic Apps służącą do tworzenia integracji B2B. Konta integracji używają [stałego modelu cen](../logic-apps/logic-apps-pricing.md#integration-accounts) , w którym stawka jest oparta na typie konta integracji lub używanej *warstwie* .

* [ISE](../logic-apps/logic-apps-pricing.md#fixed-pricing) to osobny zasób tworzony jako lokalizacja wdrożenia dla aplikacji logiki, które wymagają bezpośredniego dostępu do zasobów w sieci wirtualnej. ISEs użyć [stałego modelu cen](../logic-apps/logic-apps-pricing.md#fixed-pricing) , gdzie stawka jest oparta na utworzonej jednostce SKU ISE i innych ustawieniach.

* [Łącznik niestandardowy](../logic-apps/logic-apps-pricing.md#consumption-pricing) jest osobnym zasobem utworzonym dla interfejsu API REST, który nie ma wstępnie skompilowanego łącznika do użycia w aplikacjach logiki. Niestandardowe wykonania łączników używają [modelu cen zużycia](../logic-apps/logic-apps-pricing.md#consumption-pricing) , z wyjątkiem sytuacji, gdy są używane w ISE.

* W przypadku usługi Logic Apps z wieloma dzierżawcami koszty związane z [przechowywaniem i przechowywaniem danych](../logic-apps/logic-apps-pricing.md#data-retention) są naliczane przy użyciu [stałego modelu cen](../logic-apps/logic-apps-pricing.md#fixed-pricing). Na przykład dane wejściowe i wyjściowe z historii uruchamiania są przechowywane w magazynie w tle, co różni się od zasobów magazynu, które można niezależnie tworzyć, zarządzać i uzyskiwać dostęp z aplikacji logiki.

  W ISE użycie magazynu i przechowywania danych nie wiąże się z kosztami.

<a name="costs-after-resource-deletion"></a>

### <a name="costs-might-accrue-after-resource-deletion"></a>Koszty mogą naliczane po usunięciu zasobów

<!--Note to Azure service writer: You might need to sync with your product team to identify resources that continue to exist after those ones for your service are deleted. If you're certain that no resources can exist after those for your service are deleted, then omit this section. -->

Po usunięciu aplikacji logiki usługa Logic Apps nie będzie tworzyć ani uruchamiać nowych wystąpień przepływu pracy. Jednak wszystkie w toku i oczekujące przebiegi są kontynuowane do czasu zakończenia. W zależności od liczby tych przebiegów proces ten może zająć trochę czasu. Aby uzyskać więcej informacji, zobacz [Zarządzanie aplikacjami logiki](manage-logic-apps-with-azure-portal.md#delete-logic-apps).

Jeśli masz te zasoby po usunięciu aplikacji logiki, te zasoby nadal istnieją i naliczanie kosztów do czasu ich usunięcia:

* Zasoby platformy Azure, które tworzysz i zarządzasz niezależnie od aplikacji logiki, która łączy się z tymi zasobami, na przykład aplikacje funkcji platformy Azure, Centra zdarzeń, sieci zdarzeń itd.

* Konta integracji

* Środowiska usługi integracji (ISEs)

  W przypadku [usunięcia ISE](ise-manage-integration-service-environment.md#delete-ise)skojarzonej sieci wirtualnej platformy Azure, podsieci i innych powiązanych zasobów nadal istnieją. Po usunięciu ISE może być konieczne odczekanie na określoną liczbę godzin, aby można było spróbować usunąć sieć wirtualną lub podsieci.

### <a name="using-monetary-credit-with-azure-logic-apps"></a>Korzystanie z środków pieniężnych w Azure Logic Apps

Opłaty za Azure Logic Apps są naliczane za zobowiązania pieniężne z tytułu umowy EA. Nie można jednak użyć kredytu zobowiązania pieniężnego w ramach umowy EA, aby uregulować opłaty za produkty i usługi innych firm, w tym te z portalu Azure Marketplace.

<a name="estimate-costs"></a>

## <a name="estimate-costs"></a>Szacowanie kosztów

Przed utworzeniem zasobów Azure Logic Apps, Oszacuj koszty przy użyciu [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/). Aby uzyskać więcej informacji, zapoznaj się z tematem [model cen dla Azure Logic Apps](../logic-apps/logic-apps-pricing.md).

1. Na [stronie Kalkulator cen platformy Azure](https://azure.microsoft.com/pricing/calculator/)w menu po lewej wybierz pozycję **integracja**  >  **Azure Logic Apps**.

   ![Zrzut ekranu przedstawiający Kalkulator cen platformy Azure z wybraną pozycją "Azure Logic Apps".](./media/plan-manage-costs/add-azure-logic-apps-pricing-calculator.png)

1. Przewiń w dół stronę do momentu wyświetlenia kalkulatora cen Azure Logic Apps. W różnych sekcjach dotyczących zasobów platformy Azure, które są bezpośrednio powiązane z Azure Logic Apps, wprowadź liczbę zasobów, które mają być używane, oraz liczbę przedziałów, w których można korzystać z tych zasobów.

   Ten zrzut ekranu przedstawia przykład oszacowania kosztów za pomocą kalkulatora:

   ![Przykład przedstawiający szacowany koszt w kalkulatorze cen platformy Azure](./media/plan-manage-costs/example-logic-apps-pricing-calculator.png)

1. Aby zaktualizować oszacowania kosztów podczas tworzenia i używania nowych powiązanych zasobów, Wróć do tego kalkulatora i zaktualizuj te zasoby w tym miejscu.

<a name="create-budgets-alerts"></a>

## <a name="create-budgets-and-alerts"></a>Tworzenie budżetów i alertów

Aby ułatwić aktywne zarządzanie kosztami Twojego konta lub subskrypcji platformy Azure, możesz tworzyć [budżety](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) i [alerty](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) za pomocą usług [Azure Cost Management i rozliczeń](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oraz możliwości.  Budżety i alerty są tworzone dla subskrypcji platformy Azure i grup zasobów, dzięki czemu są one przydatne jako część ogólnej strategii monitorowania kosztów.

W oparciu o wydatki w porównaniu z progami budżetu i kosztów, alerty automatycznie powiadamiają udziałowców o wykorzystaniu anomalii i poświęcają ryzyko. Jeśli chcesz zwiększyć poziom szczegółowości monitorowania, możesz również utworzyć budżety używające filtrów dla określonych zasobów lub usług na platformie Azure. Filtry pomagają upewnić się, że nie zostaną przypadkowo utworzone nowe zasoby, które będą kosztowały dodatkowe pieniądze. Aby uzyskać więcej informacji na temat opcji filtru, zobacz [Opcje grup i filtrów](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<a name="monitor-costs"></a>

## <a name="monitor-costs"></a>Monitorowanie kosztów

Koszty jednostkowe użycia zasobów różnią się w zależności od interwałów czasu, takich jak sekundy, minuty, godziny, dni lub użycie jednostek, takie jak bajty, megabajty i tak dalej. Kilka przykładów jest dziennie, bieżącego i poprzedniego miesiąca oraz roku. Przełączenie na dłuższe widoki w czasie może pomóc w identyfikowaniu trendów wydatków. Korzystając z funkcji analizy kosztów, można wyświetlać koszty jako wykresy i tabele w różnych przedziałach czasu. Jeśli utworzono budżety i prognozy kosztów, możesz również łatwo sprawdzić, gdzie budżety zostały przekroczone i przeznaczyć, że wystąpiły.

Po rozpoczęciu ponoszenia kosztów zasobów tworzonych lub rozpoczynających korzystanie z platformy Azure możesz przeglądać i monitorować te koszty w następujący sposób:

* [Monitoruj wykonywanie aplikacji logiki i użycie magazynu](#monitor-billing-metrics) za pomocą Azure monitor

* Uruchamianie [analizy kosztów](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) przy użyciu [Azure Cost Management i rozliczeń](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)

<a name="monitor-billing-metrics"></a>

### <a name="monitor-logic-app-executions-and-storage-consumption"></a>Monitoruj wykonywanie aplikacji logiki i użycie magazynu

Za pomocą Azure Monitor można wyświetlić te metryki dla określonej aplikacji logiki:

* Rozliczane wykonania akcji
* Rozliczane wykonania wyzwalacza
* Użycie rozliczeń dla natywnych wykonań operacji
* Użycie rozliczeń dla wykonywania łączników standardowych
* Użycie rozliczeń na potrzeby wykorzystania magazynu
* Łączna Liczba wykonań rozliczanych

<a name="execution-storage-metrics"></a>

#### <a name="view-execution-and-storage-consumption-metrics"></a>Wyświetl metryki użycia i wykonywania magazynu

1. W Azure Portal Znajdź i Otwórz aplikację logiki. W menu aplikacji logiki w obszarze **monitorowanie** wybierz pozycję **metryki**.

1. W okienku po prawej stronie w obszarze **tytuł wykresu** na pasku metryki Otwórz listę **metryk** i wybierz żądaną metrykę.

   > [!NOTE]
   > Użycie magazynu jest mierzone jako liczba jednostek magazynowych (GB) używanych przez aplikację logiki i jest rozliczana. Program korzysta z tego, że użycie mniej niż 500 MB w magazynie może nie być wyświetlane w widoku monitorowanie, ale nadal są naliczane opłaty.

   ![Zrzut ekranu przedstawiający okienko metryk z otwartą listą metryk.](./media/logic-apps-pricing/select-metric.png)

1. W prawym górnym rogu okienka wybierz żądany okres.

1. Aby wyświetlić inne dane dotyczące użycia magazynu, w tym rozmiary danych wejściowych i wyjściowych akcji w historii uruchamiania aplikacji logiki, [wykonaj następujące kroki](#view-input-output-sizes).

<a name="view-input-output-sizes"></a>

#### <a name="view-action-input-and-output-sizes-in-run-history"></a>Wyświetl rozmiary danych wejściowych i wyjściowych akcji w historii uruchamiania

1. W Azure Portal Znajdź i Otwórz aplikację logiki.

1. W menu aplikacji logiki wybierz pozycję **Przegląd**.

1. W okienku po prawej stronie w obszarze **historia uruchamiania** wybierz przebieg zawierający dane wejściowe i wyjściowe, które chcesz wyświetlić.

1. W obszarze **Uruchom aplikację logiki** wybierz pozycję **Uruchom szczegóły**.

1. W okienku **szczegółów przebiegu aplikacji logiki** w tabeli Actions (akcje), która zawiera listę stanów i czas trwania akcji, wybierz akcję, którą chcesz wyświetlić.

1. W okienku **Akcja aplikacji logiki** Znajdź rozmiary danych wejściowych i wyjściowych tej akcji. W obszarze łącze **linków i danych** **wyjściowych** Znajdź linki do tych danych wejściowych i wyjściowych.

   > [!NOTE]
   > W przypadku pętli tylko akcje najwyższego poziomu pokazują rozmiary dla danych wejściowych i wyjściowych. W przypadku akcji wewnątrz zagnieżdżonych pętli dane wejściowe i wyjściowe pokazują rozmiar zerowy i nie łączą.

<a name="run-cost-analysis"></a>

### <a name="run-cost-analysis-by-using-azure-cost-management-and-billing"></a>Uruchamianie analizy kosztów przy użyciu Azure Cost Management i rozliczeń

Aby przejrzeć koszty usługi Logic Apps w oparciu o konkretny zakres, na przykład subskrypcję platformy Azure, możesz użyć możliwości [analizy kosztów](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) w [Azure Cost Management i rozliczeniach](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

1. W Azure Portal otwórz żądany zakres, na przykład subskrypcję platformy Azure. W menu po lewej stronie w obszarze **Cost Management** wybierz pozycję **Analiza kosztów**.

   Po pierwszym otwarciu okienka analiza kosztów na górnym wykresie prezentowane są rzeczywiste i przewidywane koszty użycia dla wszystkich usług w subskrypcji dla bieżącego miesiąca.

   ![Zrzut ekranu przedstawiający okienko Azure Portal i analizy kosztów z przykładem rzeczywistych i prognozowanych kosztów w ramach subskrypcji.](./media/plan-manage-costs/cost-analysis-total-actual-forecasted-costs.png)

   > [!TIP]
   > Aby zmienić zakresy, w okienku **Analiza kosztów** na pasku filtry wybierz filtr **zakresu** . W okienku **Wybierz zakres** przejdź do żądanego zakresu.

   Wykresy pierścieniowe pokazują bieżące koszty według usług platformy Azure, regionu platformy Azure i grupy zasobów.

   ![Zrzut ekranu przedstawiający okienko Azure Portal i analizy kosztów z przykładowymi wykresami pierścieniowymi dla usług, regionów i grup zasobów.](./media/plan-manage-costs/cost-analysis-donut-charts.png)

1. Aby filtrować wykres do określonego obszaru, takiego jak usługa lub zasób, na pasku filtry wybierz pozycję **Dodaj filtr**.

1. Z listy po lewej stronie wybierz typ filtru, na przykład **nazwa usługi**. Z listy znajdującej się po prawej stronie wybierz filtr, na przykład **Aplikacje logiki**. Gdy wszystko będzie gotowe, zaznacz zielony znacznik wyboru.

   ![Zrzut ekranu przedstawiający okienko Azure Portal i analizy kosztów z opcjami filtrowania.](./media/plan-manage-costs/cost-analysis-add-service-name-filter.png)

   Na przykład poniżej przedstawiono wynik usługi Logic Apps:

   ![Zrzut ekranu przedstawiający okienko Azure Portal i analizy kosztów z wynikami filtrowanymi "Logic Apps".](./media/plan-manage-costs/cost-analysis-total-actual-costs-service.png)

### <a name="export-cost-data"></a>Eksportowanie danych kosztów

Jeśli potrzebujesz więcej informacji na temat kosztów, możesz [wyeksportować dane dotyczące kosztów](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) do konta magazynu. Na przykład zespół finansowy może analizować te dane przy użyciu programu Excel lub Power BI. Możesz wyeksportować koszty codziennie, co tydzień lub według harmonogramu miesięcznego i ustawić niestandardowy zakres dat. Eksportowanie danych o kosztach jest zalecanym sposobem na pobranie z nich zbiorów.

## <a name="other-ways-to-manage-and-reduce-costs"></a>Inne sposoby zarządzania i zmniejszania kosztów

<!-- Note to Azure service writer: This section is optional. Other than using the Cost Management methods above, your service probably has other specific ways to minimize costs. For example, you might have best practice advice or specific ways to reduce costs that are specific to your service. If so, try to add that guidance here or at least summarize key points. Try to be as prescriptive as possible. If you have more comprehensive content, add links to your other published articles or sections here.

Add a statement that discusses any recommended settings for your service that might help keep the charges minimal if a service isn't being actively used by the customer. For example: Will turning off a VM help to get no charges for the specific VM resource?

Otherwise, if no other cost-saving recommendations or best practices exist to reduce costs, cut this section.
-->

Aby pomóc w obniżeniu kosztów związanych z punktami i powiązanymi zasobami, wypróbuj następujące opcje:

* Jeśli to możliwe, użyj [wbudowanych wyzwalaczy i akcji](../connectors/apis-list.md#built-in), które są tańsze do uruchomienia przez [wyzwalacze łączników zarządzanych i akcje](../connectors/apis-list.md#managed-connectors).

  Na przykład może być możliwe obniżenie kosztów podczas uzyskiwania dostępu do innych zasobów przy użyciu [akcji http](../connectors/connectors-native-http.md) lub przez wywołanie funkcji utworzonej przy użyciu [usługi Azure Functions](../azure-functions/functions-overview.md) i użycie [wbudowanej akcji Azure Functions](../logic-apps/logic-apps-azure-functions.md). Jednak użycie Azure Functions również wiąże się z kosztami, dlatego należy porównać opcje.

* [Określ precyzyjne warunki wyzwalania](logic-apps-workflow-actions-triggers.md#trigger-conditions) dla uruchamiania przepływu pracy.

  Można na przykład określić, że wyzwalacz ma być uruchamiany tylko wtedy, gdy docelowa witryna sieci Web zwróci błąd wewnętrzny serwera. W definicji JSON wyzwalacza Użyj `conditions` właściwości, aby określić warunek, który odwołuje się do kodu stanu wyzwalacza.

* Jeśli wyzwalacz ma wersję sondowania i wersję elementu webhook, Wypróbuj wersję elementu webhook, która czeka na wystąpienie określonego zdarzenia przed jego wyzwoleniem, a nie regularnie sprawdzaj istnienie zdarzenia.

* Wywołaj aplikację logiki za pomocą innej usługi, aby wyzwalacz uruchamiał się tylko wtedy, gdy przepływ pracy powinien zostać uruchomiony.

  Można na przykład wywołać aplikację logiki z funkcji, która została utworzona i uruchomiona za pomocą usługi Azure Functions. Na przykład zapoznaj się z tematem [wywoływanie lub wyzwalanie aplikacji logiki przy użyciu Azure Functions i Azure Service Bus](logic-apps-scenario-function-sb-trigger.md).

* [Wyłącz aplikacje logiki](manage-logic-apps-with-azure-portal.md#disable-or-enable-logic-apps) , które nie muszą być stale uruchamiane, lub [Usuń Aplikacje logiki](manage-logic-apps-with-azure-portal.md#delete-logic-apps) , które nie są już potrzebne. Jeśli to możliwe, wyłącz wszystkie inne zasoby, które nie są stale aktywne.

## <a name="next-steps"></a>Następne kroki

* [Optymalizowanie inwestycji w chmurę za pomocą usługi Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Zarządzanie kosztami przy użyciu analizy kosztów](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* [Zapobieganie powstawaniu nieoczekiwanych kosztów](../cost-management-billing/understand/analyze-unexpected-charges.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)
* Wykonaj [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) kurs uczenia z przewodnikiem