---
title: Planowanie i zarządzanie kosztami API Management platformy Azure
description: Dowiedz się, jak planować i zarządzać kosztami usługi Azure API Management przy użyciu analizy kosztów w Azure Portal.
author: dlepow
ms.author: apimpm
ms.custom: subject-cost-optimization
ms.service: api-management
ms.topic: how-to
ms.date: 12/15/2020
ms.openlocfilehash: 90449a29f58f31827e2a36b5f6c41563e88ac032
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2021
ms.locfileid: "98251356"
---
# <a name="plan-and-manage-costs-for-api-management"></a>Planowanie i zarządzanie kosztami API Management

W tym artykule opisano sposób planowania kosztów usługi Azure API Management i zarządzania nimi. Najpierw należy skorzystać z kalkulatora cen platformy Azure, aby pomóc w zaplanowaniu API Management kosztów przed dodaniem zasobów usługi do oszacowania kosztów. Po rozpoczęciu korzystania z API Management zasobów należy używać funkcji Cost Management do ustawiania budżetów i monitorowania kosztów. Możesz również przejrzeć prognozowane koszty i zidentyfikować trendy wydatków, aby identyfikować obszary, w których może być konieczne działanie. 

Koszty API Management są tylko częścią miesięcznych kosztów rachunku na korzystanie z platformy Azure. Chociaż w tym artykule wyjaśniono, jak planować i zarządzać kosztami API Management, opłaty są naliczane za wszystkie usługi i zasoby platformy Azure używane w ramach subskrypcji platformy Azure, w tym usługi innych firm.

## <a name="prerequisites"></a>Wymagania wstępne

Analiza kosztów w Cost Management obsługuje większość typów kont platformy Azure, ale nie wszystkie z nich. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Aby wyświetlić dane dotyczące kosztów, wymagany jest co najmniej dostęp do odczytu dla konta platformy Azure. Aby uzyskać informacje na temat przypisywania dostępu do danych usługi Azure Cost Management, zobacz [Przypisywanie dostępu do danych](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimate-costs-before-using-api-management"></a>Oszacuj koszty przed użyciem API Management

Skorzystaj z [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) , aby oszacować koszty przed dodaniem API Management. 

1. Wyszukaj *API Management* lub wybierz   >  **API Management** integracji.
1. Wybierz **Widok** , aby dodać domyślne oszacowanie kosztów dla wystąpienia usługi API Management.

> [!NOTE]
> Koszty pokazane w tym przykładzie służą wyłącznie do celów demonstracyjnych. Zobacz [cennik API Management](https://azure.microsoft.com/pricing/details/api-management/) , aby uzyskać najnowsze informacje o cenach.

:::image type="content" source="media/plan-manage-costs/pricing-calculator-developer-tier.png" alt-text="Szacowanie kosztów dla warstwy dewelopera":::

* Domyślne oszacowanie kosztów odbywa się na podstawie API Management wystąpienia usługi w [warstwie usług](api-management-features.md) dla **deweloperów** z 1 [jednostką pojemności](api-management-capacity.md). Warstwa Deweloper jest dla nieprodukcyjnych przypadków użycia i ocen. Nie jest ona obsługiwana przez umowę dotyczącą poziomu usług.

* Aby oszacować koszty dodatkowych jednostek pojemności lub innej warstwy usług, wybierz inne opcje z listy rozwijanej **jednostki** i **warstwy** .

* W zależności od dostępności funkcji i warstwy usług dodatkowe opłaty mogą być stosowane do korzystania z [bram samoobsługowych](self-hosted-gateway-overview.md).

Aby uzyskać dodatkowe informacje dotyczące cen i funkcji, zobacz:

* [Cennik API Management](https://azure.microsoft.com/pricing/details/api-management/)
* [Porównanie warstw API Management platformy Azure oparte na funkcjach](api-management-features.md)

### <a name="using-monetary-credit-with-api-management"></a>Korzystanie z środków pieniężnych w API Management

Opłaty za API Management są naliczane za zobowiązania pieniężne z tytułu umowy EA. Nie można jednak używać kredytu zobowiązania pieniężnego umowy EA do płacenia za opłaty za produkty i usługi innych firm, w tym te z portalu Azure Marketplace.

## <a name="monitor-costs"></a>Monitorowanie kosztów

Gdy korzystasz z zasobów platformy Azure z API Management, naliczane są koszty. Koszty jednostkowe użycia zasobów platformy Azure różnią się w zależności od przedziałów czasu (sekund, minut, godzin i dni) lub według użycia jednostek (bajtów, megabajtów itd.). Po rozpoczęciu API Management zostanie naliczone koszty i będzie można zobaczyć koszty w [analizie kosztów](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Korzystając z analizy kosztów, można wyświetlać API Management koszty na wykresach i tabelach dla różnych interwałów czasu. Kilka przykładów jest dziennie, bieżącego i poprzedniego miesiąca oraz roku. Możesz również wyświetlić koszty związane z budżetami i prognozowanymi kosztami. Przełączenie na dłuższe widoki w czasie może pomóc w identyfikowaniu trendów wydatków. I zobaczysz, gdzie mogły wystąpić przespędzanie. Jeśli utworzono budżety, możesz również łatwo zobaczyć, gdzie są przekraczane.

> [!NOTE]
> Koszty pokazane w tym przykładzie służą wyłącznie do celów demonstracyjnych. Twoje koszty różnią się w zależności od użycia zasobów i bieżących cen.

Aby wyświetlić koszty API Management w analizie kosztów:

1. Zaloguj się w witrynie [Azure Portal](https://azure.microsoft.com).
1. Otwórz okno **Cost Management + rozliczanie** , wybierz pozycję **Zarządzanie kosztami** z menu, a następnie wybierz **zakres rozliczeń**. Na przykład wybierz subskrypcję z listy.
1. Wybierz **Cost Management** z menu, a następnie wybierz pozycję **Analiza kosztów**.
1. Domyślnie miesięczne koszty wszystkich usług są wyświetlane na pierwszym wykresie pierścieniowym. 

    :::image type="content" source="media/plan-manage-costs/api-management-cost-analysis.png" alt-text="Miesięczne koszty subskrypcji":::

1. Aby zawęzić koszty pojedynczej usługi, takiej jak API Management, wybierz pozycję **Dodaj filtr** , a następnie wybierz pozycję **nazwa usługi**. Następnie wybierz pozycję **API Management**.

    :::image type="content" source="media/plan-manage-costs/api-management-apim-cost-analysis.png" alt-text="Przykład przedstawiający skumulowane koszty API Management":::

W poprzednim przykładzie zobaczysz bieżący koszt usługi. Pokazywane są również koszty według regionów platformy Azure i kosztów API Management według grupy zasobów. W tym miejscu możesz dowiedzieć się o kosztach własnych.

## <a name="create-budgets"></a>Tworzenie budżetów

W celu zarządzania kosztami można utworzyć [budżety](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oraz [alerty](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn), które automatycznie powiadamiają uczestników o anomaliach w wydatkach i o ryzyku nadmiernych wydatków. Alerty są oparte na wydatkach w porównaniu z budżetem i progami kosztów. Budżety i alerty są tworzone dla subskrypcji platformy Azure i grup zasobów, dzięki czemu są one przydatne jako część ogólnej strategii monitorowania kosztów. 

Budżety można utworzyć za pomocą filtrów dla określonych zasobów lub usług na platformie Azure, jeśli chcesz mieć większą szczegółowość w monitorowaniu. Filtry ułatwiają zagwarantowanie, że nie zostaną przypadkowo utworzone nowe zasoby, które będą kosztowały dodatkowe pieniądze. Aby uzyskać więcej informacji na temat opcji filtrowania podczas tworzenia budżetu, zobacz [Opcje grup i filtrów](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Eksportowanie danych kosztów

Możesz również [wyeksportować dane kosztu](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) do konta magazynu. Jest to przydatne, gdy potrzebne są inne osoby do przeprowadzenia dodatkowej analizy danych na potrzeby kosztów. Na przykład zespół finansowy może analizować dane przy użyciu programu Excel lub Power BI. Możesz wyeksportować koszty codziennie, co tydzień lub miesięcznie i ustawić niestandardowy zakres dat. Eksportowanie danych o kosztach jest zalecanym sposobem na pobranie z nich zbiorów.

## <a name="other-ways-to-manage-and-reduce-costs-for-api-management"></a>Inne sposoby zarządzania i obniżyć koszty API Management

### <a name="choose-tier"></a>Wybierz warstwę

Zapoznaj się z [porównaniem warstw API Management platformy Azure](api-management-features.md) , aby ułatwić podjęcie decyzji, która warstwa usług może być odpowiednia dla Twoich scenariuszy. Różne warstwy usług obsługują kombinację funkcji i możliwości, które są przeznaczone do różnych przypadków użycia z różnymi kosztami. [Uaktualnij](upgrade-and-scale.md) do innej warstwy usług w dowolnym momencie.

* Warstwa usługi **zużycie** zapewnia uproszczoną, bezserwerową opcję, która nie wiąże się ze stałymi kosztami. Opłaty są naliczane na podstawie liczby wywołań interfejsu API do usługi powyżej pewnej wartości progowej. Pojemność jest również skalowana automatycznie na podstawie obciążenia usługi.
* Inne API Management warstwy ponoszą miesięczne koszty i zapewniają większą przepływność i bogatsze zestawy funkcji dla obciążeń testowych i produkcyjnych.

### <a name="scale-using-capacity-units"></a>Skalowanie przy użyciu jednostek wydajności

Z wyjątkiem warstwy usług zużycia, API Management obsługuje skalowanie przez dodanie lub usunięcie [*jednostek pojemności*](api-management-capacity.md). W miarę wzrostu obciążenia w wystąpieniu API Management dodanie jednostek wydajności może być bardziej ekonomiczne niż uaktualnienie do wyższej warstwy usług. Maksymalna liczba jednostek zależy od warstwy usług.

Każda jednostka pojemności ma pewne możliwości przetwarzania żądań, które są zależne od warstwy usługi. Na przykład jednostka warstwy Podstawowa ma szacowaną maksymalną przepływność wynoszącą około 1 000 żądań na sekundę. 

W miarę dodawania lub usuwania jednostek, pojemności i skali kosztów proporcjonalnie. Na przykład dwie jednostki warstwy Standardowa zapewniają szacowaną przepływność wynoszącą około 2 000 żądań na sekundę. Rzeczywista przepływność może się różnić z powodu rozmiaru żądań lub odpowiedzi, wzorców połączeń, liczby klientów zgłaszających żądania oraz innych czynników.

[Monitoruj](api-management-howto-use-azure-monitor.md) metrykę pojemności dla wystąpienia API Management, aby pomóc w podejmowaniu decyzji, czy skalować lub uaktualnić wystąpienie API Management, aby obsłużyć więcej obciążeń.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak zoptymalizować inwestycję w chmurę za pomocą Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Dowiedz się więcej o zarządzaniu kosztami przy użyciu [analizy kosztów](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Dowiedz się więcej na temat [zapobiegania nieoczekiwanym kosztom](../cost-management-billing/manage/getting-started.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Zapoznaj się z [Cost Management](https://docs.microsoft.com/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) kurs uczenia z przewodnikiem.
- Dowiedz się więcej o [możliwościach](api-management-capacity.md)API Management.
- Zobacz kroki umożliwiające skalowanie i uaktualnianie API Management przy użyciu [Azure Portal](upgrade-and-scale.md)i Dowiedz się więcej na temat [skalowania](api-management-howto-autoscale.md)automatycznego.