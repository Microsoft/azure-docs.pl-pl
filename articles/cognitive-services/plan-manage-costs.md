---
title: Zaplanuj zarządzanie kosztami dla usługi Azure Cognitive Services
description: Dowiedz się, jak planować i zarządzać kosztami usługi Azure Cognitive Services przy użyciu analizy kosztów w Azure Portal.
author: erhopf
ms.author: erhopf
ms.custom: subject-cost-optimization
ms.service: cognitive-services
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: db99fa5caff27a24aa04e4780b25ade3f7c25496
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101699933"
---
# <a name="plan-and-manage-costs-for-azure-cognitive-services"></a>Planowanie i zarządzanie kosztami Cognitive Services platformy Azure

W tym artykule opisano sposób planowania kosztów usługi Azure Cognitive Services i zarządzania nimi. Najpierw należy skorzystać z kalkulatora cen platformy Azure, aby pomóc w zaplanowaniu Cognitive Services kosztów przed dodaniem zasobów usługi do oszacowania kosztów. Następnie podczas dodawania zasobów platformy Azure Przejrzyj szacowane koszty. Po rozpoczęciu korzystania z zasobów Cognitive Services (na przykład Speech, przetwarzanie obrazów, LUIS, analiza tekstu, translator itp.) Używaj funkcji Cost Management do ustawiania budżetów i monitorowania kosztów. Możesz również przejrzeć prognozowane koszty i zidentyfikować trendy wydatków, aby identyfikować obszary, w których może być konieczne działanie. Koszty Cognitive Services są tylko częścią miesięcznych kosztów rachunku na korzystanie z platformy Azure. Chociaż w tym artykule wyjaśniono, jak planować i zarządzać kosztami Cognitive Services, opłaty są naliczane za wszystkie usługi i zasoby platformy Azure używane w ramach subskrypcji platformy Azure, w tym usługi innych firm.

## <a name="prerequisites"></a>Wymagania wstępne

Analiza kosztów w Cost Management obsługuje większość typów kont platformy Azure, ale nie wszystkie z nich. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Aby wyświetlić dane dotyczące kosztów, wymagany jest co najmniej dostęp do odczytu dla konta platformy Azure. Aby uzyskać informacje na temat przypisywania dostępu do danych usługi Azure Cost Management, zobacz [Przypisywanie dostępu do danych](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

<!--Note for Azure service writer: If you have other prerequisites for your service, insert them here -->

## <a name="estimate-costs-before-using-cognitive-services"></a>Oszacuj koszty przed użyciem Cognitive Services

Skorzystaj z [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) , aby oszacować koszty przed dodaniem Cognitive Services.

:::image type="content" source="media/cognitive-services-pricing-calculator.png" alt-text="Kalkulator cen platformy Azure dla Cognitive Services" border="true":::

Dodając nowe zasoby do obszaru roboczego, Wróć do tego kalkulatora i Dodaj tutaj ten sam zasób, aby zaktualizować oszacowania kosztów.

Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/).

## <a name="understand-the-full-billing-model-for-cognitive-services"></a>Zapoznaj się z pełnym modelem rozliczeń dla Cognitive Services

Cognitive Services działa w infrastrukturze platformy Azure, która [naliczy koszty](https://azure.microsoft.com/pricing/details/cognitive-services/) podczas wdrażania nowego zasobu. Ważne jest, aby zrozumieć, że dodatkowa infrastruktura może naliczać koszt. Ten koszt należy zarządzać po wprowadzeniu zmian w wdrożonych zasobach. 

### <a name="costs-that-typically-accrue-with-cognitive-services"></a>Koszty, które zwykle naliczane są przy użyciu Cognitive Services

Zwykle po wdrożeniu zasobu platformy Azure koszty są ustalane na podstawie warstwy cenowej, a interfejsy API wywołuje się w punkcie końcowym. Jeśli usługa, z której korzystasz, ma warstwę zobowiązania, przejęcie nad wyznaczonymi wywołaniami w warstwie może spowodować naliczenie opłaty za nadwyżkowe użycie.

W przypadku korzystania z tych usług mogą naliczane dodatkowe koszty:

#### <a name="qna-maker"></a>QnA Maker

Podczas tworzenia zasobów dla QnA Maker mogą być również tworzone zasoby dla innych usług platformy Azure. Obejmują one:

- [Azure App Service (dla środowiska uruchomieniowego)](https://azure.microsoft.com/pricing/details/app-service/)
- [Wyszukiwanie poznawcze platformy Azure (dla danych)](https://azure.microsoft.com/pricing/details/search/)
 
### <a name="costs-that-might-accrue-after-resource-deletion"></a>Koszty, które mogą naliczać po usunięciu zasobów

#### <a name="qna-maker"></a>QnA Maker

Po usunięciu zasobów QnA Maker następujące zasoby mogą nadal istnieć. W dalszym ciągu naliczane są koszty, dopóki nie zostaną usunięte.

- [Azure App Service (dla środowiska uruchomieniowego)](https://azure.microsoft.com/pricing/details/app-service/)
- [Wyszukiwanie poznawcze platformy Azure (dla danych)](https://azure.microsoft.com/pricing/details/search/)

### <a name="using-azure-prepayment-credit-with-cognitive-services"></a>Przy użyciu środków na korzystanie z systemu Azure w wysokości Cognitive Services

Opłaty za Cognitive Services mogą być naliczane przy użyciu przedpłaty za korzystanie z platformy Azure (wcześniej zwanego zobowiązaniem pieniężnym). Nie można jednak używać środków zaliczkowych na platformie Azure, aby zapłacić za opłaty za produkty i usługi innych firm, w tym te z portalu Azure Marketplace.

## <a name="monitor-costs"></a>Monitorowanie kosztów

<!-- Note to Azure service writer: Modify the following as needed for your service. Replace example screenshots with ones taken for your service. If you need assistance capturing screenshots, ask banders for help. -->

Gdy korzystasz z zasobów platformy Azure z Cognitive Services, naliczane są koszty. Koszty jednostkowe użycia zasobów platformy Azure różnią się w zależności od przedziałów czasu (sekund, minut, godzin i dni) lub według użycia jednostek (bajtów, megabajtów itd.). Po rozpoczęciu korzystania z usługi poznawczej (lub Cognitive Services) koszty są ponoszone, a koszty można zobaczyć w [analizie kosztów](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Korzystając z analizy kosztów, można wyświetlać Cognitive Services koszty na wykresach i tabelach dla różnych interwałów czasu. Kilka przykładów jest dziennie, bieżącego i poprzedniego miesiąca oraz roku. Możesz również wyświetlić koszty związane z budżetami i prognozowanymi kosztami. Przełączenie na dłuższe widoki w czasie może pomóc w identyfikowaniu trendów wydatków. I zobaczysz, gdzie mogły wystąpić przespędzanie. Jeśli utworzono budżety, możesz również łatwo zobaczyć, gdzie są przekraczane.

Aby wyświetlić koszty Cognitive Services w analizie kosztów:

1. Zaloguj się w witrynie Azure Portal.
2. Otwórz zakres w Azure Portal a następnie wybierz pozycję **Analiza kosztów** w menu. Na przykład przejdź do pozycji **subskrypcje**, wybierz subskrypcję z listy, a następnie wybierz pozycję  **Analiza kosztów** w menu. Wybierz **zakres** , aby przełączyć się do innego zakresu w analizie kosztów.
3. Domyślnie koszt usług jest pokazywany na pierwszym wykresie pierścieniowym. Wybierz obszar na wykresie z etykietą Cognitive Services.

Rzeczywiste koszty miesięczne są wyświetlane po pierwszym otwarciu analizy kosztów. Oto przykład pokazujący wszystkie miesięczne koszty użycia.

:::image type="content" source="./media/cost-management/all-costs.png" alt-text="Przykład przedstawiający skumulowane koszty subskrypcji":::

- Aby zawęzić koszty pojedynczej usługi, na przykład Cognitive Services, wybierz pozycję **Dodaj filtr** , a następnie wybierz pozycję **nazwa usługi**. Następnie wybierz pozycję **Cognitive Services**.

Oto przykład przedstawiający koszty tylko Cognitive Services.

:::image type="content" source="./media/cost-management/cognitive-services-costs.png" alt-text="Przykład przedstawiający skumulowane koszty Cognitive Services":::

W poprzednim przykładzie zobaczysz bieżący koszt usługi. Pokazywane są również koszty według regionów platformy Azure i kosztów Cognitive Services według grupy zasobów. W tym miejscu możesz dowiedzieć się o kosztach własnych.

## <a name="create-budgets"></a>Tworzenie budżetów

W celu zarządzania kosztami można utworzyć [budżety](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oraz [alerty](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn), które automatycznie powiadamiają uczestników o anomaliach w wydatkach i o ryzyku nadmiernych wydatków. Alerty są oparte na wydatkach w porównaniu z budżetem i progami kosztów. Budżety i alerty są tworzone dla subskrypcji platformy Azure i grup zasobów, dzięki czemu są one przydatne jako część ogólnej strategii monitorowania kosztów. 

Budżety można utworzyć za pomocą filtrów dla określonych zasobów lub usług na platformie Azure, jeśli chcesz mieć większą szczegółowość w monitorowaniu. Filtry ułatwiają zagwarantowanie, że nie zostaną przypadkowo utworzone nowe zasoby, które będą kosztowały dodatkowe pieniądze. Aby uzyskać więcej informacji na temat opcji filtrowania podczas tworzenia budżetu, zobacz [Opcje grup i filtrów](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Eksportowanie danych kosztów

Możesz również [wyeksportować dane kosztu](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) do konta magazynu. Jest to przydatne, gdy potrzebujesz lub inne osoby mogą przeprowadzić dodatkową analizę danych pod kątem kosztów. Na przykład zespoły finansowe mogą analizować dane przy użyciu programu Excel lub Power BI. Możesz wyeksportować koszty codziennie, co tydzień lub miesięcznie i ustawić niestandardowy zakres dat. Eksportowanie danych o kosztach jest zalecanym sposobem na pobranie z nich zbiorów.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak zoptymalizować inwestycję w chmurę za pomocą Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Dowiedz się więcej o zarządzaniu kosztami przy użyciu [analizy kosztów](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Dowiedz się więcej na temat [zapobiegania nieoczekiwanym kosztom](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Zapoznaj się z [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) kurs uczenia z przewodnikiem.