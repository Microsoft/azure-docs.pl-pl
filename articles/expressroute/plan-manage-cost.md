---
title: Planowanie zarządzania kosztami usługi Azure ExpressRoute
description: Dowiedz się, jak planować i zarządzać kosztami usługi Azure ExpressRoute za pomocą analizy kosztów w Azure Portal.
author: duongau
ms.author: duau
ms.custom: subject-cost-optimization
ms.service: expressroute
ms.topic: how-to
ms.date: 11/30/2020
ms.openlocfilehash: 9b74f6e82e39955554c13f6ce3490bc3c22c2b98
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98600449"
---
# <a name="plan-and-manage-costs-for-azure-expressroute"></a>Planowanie i zarządzanie kosztami usługi Azure ExpressRoute

W tym artykule opisano sposób planowania kosztów i zarządzania nimi w programie ExpressRoute. Najpierw należy skorzystać z kalkulatora cen platformy Azure, aby ułatwić planowanie kosztów ExpressRoute przed dodaniem zasobów usługi do oszacowania kosztów. Po dodaniu zasobów platformy Azure Przejrzyj szacowane koszty. 

Po rozpoczęciu korzystania z zasobów ExpressRoute Użyj funkcji Cost Management do ustawiania budżetów i monitorowania kosztów. Możesz również przejrzeć prognozowane koszty i zidentyfikować trendy wydatków, aby identyfikować obszary, w których może być konieczne działanie. 

Należy pamiętać, że koszty ExpressRoute są tylko częścią miesięcznych kosztów rachunku na korzystanie z platformy Azure. Chociaż w tym artykule wyjaśniono, jak planować koszty programu ExpressRoute i zarządzać nimi, opłaty są naliczane za wszystkie usługi i zasoby platformy Azure używane w ramach subskrypcji platformy Azure, w tym usługi innych firm.

## <a name="prerequisites"></a>Wymagania wstępne

Analiza kosztów w Cost Management obsługuje większość typów kont platformy Azure, ale nie wszystkie z nich. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Aby wyświetlić dane dotyczące kosztów, wymagany jest co najmniej dostęp do odczytu dla konta platformy Azure. 

Aby uzyskać informacje na temat przypisywania dostępu do danych usługi Azure Cost Management, zobacz [Przypisywanie dostępu do danych](../cost-management/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="local-vs-standard-vs-premium"></a>Local a Standard a Premium

ExpressRoute ma trzy różne jednostki SKU obwodu: [*Local*](./expressroute-faqs.md#expressroute-local), *Standard* i [*Premium*](./expressroute-faqs.md#expressroute-premium). Sposób naliczania opłat za użycie ExpressRoute różni się między tymi trzema typami jednostek SKU. W przypadku lokalnej jednostki SKU opłata jest naliczana automatycznie przy użyciu nieograniczonego planu danych. W przypadku jednostki SKU w warstwach Standardowa i Premium można wybrać dowolny plan taryfowy lub nieograniczony. Wszystkie dane związane z transferem danych przychodzących są bezpłatne, chyba że jest używany dodatek Global Reach. Ważne jest, aby zrozumieć, które typy jednostek SKU i plan danych najlepiej sprawdzają się w obciążeniu, aby najlepiej zoptymalizować koszt i budżet.

## <a name="estimate-costs"></a>Szacowanie kosztów

Skorzystaj z [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) , aby oszacować koszty przed utworzeniem obwodu usługi ExpressRoute. 

1. Po lewej stronie wybierz pozycję **Sieć**, a następnie wybierz pozycję **Azure ExpressRoute** , aby rozpocząć. 

1. Wybierz odpowiednią *strefę* w zależności od lokalizacji komunikacji równorzędnej. Zapoznaj się z [dostawcami połączeń ExpressRoute](./expressroute-locations-providers.md#partners) , aby wybrać odpowiednią *strefę* z listy rozwijanej. 

1. Następnie wybierz *jednostkę SKU*, *szybkość obwodu* i *Plan danych* , dla którego chcesz oszacować. 

1. W *dodatkowym wychodzącym transferze danych* wprowadź oszacowanie w GB ilości danych wychodzących, które mogą być używane w ciągu miesiąca. 

1. Na koniec możesz dodać do oszacowania *Global REACH dodatek* .

Poniższy zrzut ekranu przedstawia oszacowanie kosztów za pomocą kalkulatora:

:::image type="content" source="media/plan-manage-cost/capacity-calculator-cost-estimate.png" alt-text="Szacowany koszt ExpressRoute w kalkulatorze platformy Azure":::

Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute/).

### <a name="expressroute-gateway-estimated-cost"></a>Szacowany koszt bramy ExpressRoute

Jeśli używasz bramy ExpressRoute do łączenia sieci wirtualnej z obwodem usługi ExpressRoute, wykonaj następujące kroki, aby oszacować koszt użycia bramy.

1. Po lewej stronie wybierz pozycję **Sieć**, a następnie wybierz pozycję **VPN Gateway** , aby rozpocząć. 

1. Wybierz *region* bramy, a następnie zmień *Typ* na **ExpressRoute Gateways**.

1. Wybierz z listy rozwijanej *Typ bramy* .

1. Wprowadź *godziny bramy*. (720 godz. = 30 dni)

## <a name="understand-the-full-billing-model-for-expressroute"></a>Poznaj cały model rozliczeń dla ExpressRoute

ExpressRoute działa w infrastrukturze platformy Azure, która narasta koszty wraz z ExpressRoute podczas wdrażania nowego zasobu. Ważne jest, aby zrozumieć, że dodatkowa infrastruktura może naliczać koszt. Ten koszt należy zarządzać po wprowadzeniu zmian w wdrożonych zasobach. 

### <a name="costs-that-typically-accrue-with-expressroute"></a>Koszty, które zwykle naliczane są przy użyciu ExpressRoute

Podczas tworzenia obwodu ExpressRoute możesz utworzyć bramę ExpressRoute, aby połączyć sieć wirtualną z obwodem. Opłaty za bramy są naliczane według stawki godzinowej plus koszt obwodu ExpressRoute. Zobacz [Cennik usługi ExpressRoute](https://azure.microsoft.com/en-us/pricing/details/expressroute) i wybierz pozycję bramy ExpressRoute, aby wyświetlić stawki dla różnych jednostek SKU bramy.
 
### <a name="costs-might-accrue-after-resource-deletion"></a>Koszty mogą naliczane po usunięciu zasobów

Jeśli masz bramę usługi ExpressRoute po usunięciu obwodu usługi ExpressRoute, nadal będzie naliczana opłata za koszt do momentu jego usunięcia.

### <a name="using-azure-prepayment-credit"></a>Przy użyciu środków na korzystanie z systemu Azure

Opłaty za ExpressRoute są naliczane za pomocą przedpłaty platformy Azure (wcześniej zwanego zobowiązaniem pieniężnym). Nie można jednak używać środków zaliczkowych na platformie Azure, aby zapłacić za opłaty za produkty i usługi innych firm, w tym te z portalu Azure Marketplace.

## <a name="monitor-costs"></a>Monitorowanie kosztów

W przypadku używania zasobów platformy Azure z usługą ExpressRoute koszty są naliczane. Koszty jednostkowe użycia zasobów platformy Azure różnią się w zależności od przedziałów czasu (sekund, minut, godzin i dni) lub według użycia jednostek (bajtów, megabajtów itd.). Po rozpoczęciu korzystania z ExpressRoute są naliczane koszty, a koszty można zobaczyć w [analizie kosztów](../cost-management/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Korzystając z analizy kosztów, można wyświetlać koszt obwodu usługi ExpressRoute w wykresach i tabelach dla różnych przedziałów czasu. Kilka przykładów jest dziennie, bieżącego i poprzedniego miesiąca oraz roku. Możesz również wyświetlić koszty związane z budżetami i prognozowanymi kosztami. Przełączenie na dłuższe widoki w czasie może pomóc w identyfikowaniu trendów wydatków. I zobaczysz, gdzie mogły wystąpić przespędzanie. Jeśli utworzono budżety, możesz również łatwo zobaczyć, gdzie są przekraczane.

Aby wyświetlić koszty ExpressRoute w analizie kosztów:

1. Zaloguj się do witryny Azure Portal.

1. Przejdź do pozycji **subskrypcje**, wybierz subskrypcję z listy, a następnie wybierz pozycję  **Analiza kosztów** w menu. Wybierz **zakres** , aby przełączyć się do innego zakresu w analizie kosztów. Domyślnie koszt usług jest pokazywany na pierwszym wykresie pierścieniowym.

    Rzeczywiste koszty miesięczne są wyświetlane po pierwszym otwarciu analizy kosztów. Oto przykład pokazujący wszystkie miesięczne koszty użycia.

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane.png" alt-text="Przykład przedstawiający skumulowane koszty subskrypcji":::
    

1.  Aby zawęzić koszty pojedynczej usługi, np. ExpressRoute, wybierz pozycję **Dodaj filtr** , a następnie wybierz pozycję **nazwa usługi**. Następnie wybierz pozycję **ExpressRoute**.

    Oto przykład przedstawiający koszty tylko ExpressRoute.

    :::image type="content" source="media/plan-manage-cost/cost-analysis-pane-expressroute.png" alt-text="Przykład przedstawiający skumulowane koszty dla ExpressRoute":::

W poprzednim przykładzie zobaczysz bieżący koszt usługi. Pokazywane są również koszty według regionów platformy Azure i kosztów ExpressRoute według grupy zasobów. W tym miejscu możesz dowiedzieć się o kosztach własnych.

## <a name="create-budgets-and-alerts"></a>Tworzenie budżetów i alertów

W celu zarządzania kosztami można utworzyć [budżety](../cost-management/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oraz [alerty](../cost-management/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn), które automatycznie powiadamiają uczestników o anomaliach w wydatkach i o ryzyku nadmiernych wydatków. Alerty są oparte na wydatkach w porównaniu z budżetem i progami kosztów. Budżety i alerty są tworzone dla subskrypcji platformy Azure i grup zasobów, dzięki czemu są one przydatne jako część ogólnej strategii monitorowania kosztów. 

Budżety można utworzyć za pomocą filtrów dla określonych zasobów lub usług na platformie Azure, jeśli chcesz mieć większą szczegółowość w monitorowaniu. Filtry ułatwiają zagwarantowanie, że nie zostaną przypadkowo utworzone nowe zasoby, które będą kosztowały dodatkowe pieniądze. Aby uzyskać więcej informacji na temat opcji filtrowania podczas tworzenia budżetu, zobacz [Opcje grup i filtrów](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Eksportowanie danych kosztów

Możesz również [wyeksportować dane kosztu](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) do konta magazynu. Jest to przydatne, gdy potrzebujesz lub inne osoby mogą przeprowadzić dodatkową analizę danych pod kątem kosztów. Na przykład zespół finansowy może analizować dane przy użyciu programu Excel lub Power BI. Możesz wyeksportować koszty codziennie, co tydzień lub miesięcznie i ustawić niestandardowy zakres dat. Eksportowanie danych o kosztach jest zalecanym sposobem na pobranie z nich zbiorów.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat sposobu działania cen przy użyciu usługi Azure ExpressRoute. Zobacz [Cennik usługi Azure ExpressRoute — omówienie](https://azure.microsoft.com/en-us/pricing/details/expressroute/).
- Dowiedz się [, jak zoptymalizować inwestycję w chmurę za pomocą Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Dowiedz się więcej o zarządzaniu kosztami przy użyciu [analizy kosztów](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Dowiedz się więcej na temat [zapobiegania nieoczekiwanym kosztom](../cost-management-billing/manage/getting-started.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Zapoznaj się z [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) kurs uczenia z przewodnikiem.
