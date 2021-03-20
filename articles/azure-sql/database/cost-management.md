---
title: Planowanie i zarządzanie kosztami Azure SQL Database
description: Dowiedz się, jak planować i zarządzać kosztami Azure SQL Database przy użyciu analizy kosztów w Azure Portal.
author: stevestein
ms.author: sstein
ms.custom: subject-cost-optimization
ms.service: sql-database
ms.topic: how-to
ms.date: 01/15/2021
ms.openlocfilehash: 56cf30d89460df8ac50d258bd8b29cf4e7236690
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98734634"
---
# <a name="plan-and-manage-costs-for-azure-sql-database"></a>Planowanie i zarządzanie kosztami Azure SQL Database

W tym artykule opisano sposób planowania i zarządzania kosztami Azure SQL Database. Najpierw użyj kalkulatora cen platformy Azure, aby dodać zasoby platformy Azure, a następnie zapoznaj się z oszacowanymi kosztami. Po rozpoczęciu korzystania z Azure SQL Database zasobów należy używać funkcji Cost Management do ustawiania budżetów i monitorowania kosztów. Możesz również przejrzeć prognozowane koszty i zidentyfikować trendy wydatków, aby identyfikować obszary, w których może być konieczne działanie. Koszty Azure SQL Database są tylko częścią miesięcznych kosztów rachunku na korzystanie z platformy Azure. Chociaż w tym artykule wyjaśniono, jak planować i zarządzać kosztami Azure SQL Database, opłaty są naliczane za wszystkie usługi i zasoby platformy Azure używane w ramach subskrypcji platformy Azure, w tym także usługi innych firm.


## <a name="prerequisites"></a>Wymagania wstępne

Analiza kosztów obsługuje większość typów kont platformy Azure, ale nie wszystkie z nich. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](../../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Aby wyświetlić dane dotyczące kosztów, wymagany jest co najmniej dostęp do odczytu dla konta platformy Azure. 

Aby uzyskać informacje na temat przypisywania dostępu do danych usługi Azure Cost Management, zobacz [Przypisywanie dostępu do danych](../../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).


## <a name="sql-database-initial-cost-considerations"></a>SQL Database początkowe zagadnienia dotyczące kosztów

Podczas pracy z Azure SQL Database istnieje kilka kosztów, które należy wziąć pod uwagę:


### <a name="vcore-or-dtu-purchasing-models"></a>Rdzeń wirtualny lub modele zakupu jednostek DTU 

Azure SQL Database obsługuje dwa modele zakupu: rdzeń wirtualny i DTU. Sposób naliczania opłat zależy od modeli zakupu, dlatego warto zrozumieć model, który najlepiej sprawdza się w przypadku obciążenia podczas planowania i rozważania kosztów. Aby uzyskać informacje na temat modeli zakupów rdzeń wirtualny i DTU, zobacz [Wybieranie między modelami zakupów rdzeń wirtualny i DTU](purchasing-models.md).


### <a name="provisioned-or-serverless"></a>Obsługa administracyjna lub bezserwerowa

W modelu zakupów rdzeń wirtualny Azure SQL Database również obsługiwane są dwa typy warstw obliczeniowych: zainicjowana przepływność i bezserwerowy. Sposób naliczania opłat za poszczególne warstwy obliczeniowe różnią się w zależności od tego, co najlepiej sprawdza się w przypadku obciążeń związanych z planowaniem i rozważaniem kosztów. Aby uzyskać szczegółowe informacje, zobacz [rdzeń wirtualny model — Omówienie — warstwy obliczeń](service-tiers-vcore.md#compute-tiers).

W warstwie obliczeniowej z zainicjowaną obsługą modelu zakupu opartego na rdzeń wirtualny można wymienić istniejące licencje na stawki z rabatami. Aby uzyskać szczegółowe informacje, zobacz [korzyść użycia hybrydowego platformy Azure (AHB)](../azure-hybrid-benefit.md).

### <a name="elastic-pools"></a>Pule elastyczne

W środowiskach z wieloma bazami danych, które mają różne i nieprzewidywalne wymagania dotyczące użycia, pule elastyczne mogą zapewnić oszczędności w porównaniu z obsługą tej samej ilości pojedynczych baz danych. Aby uzyskać szczegółowe informacje, zobacz [Pule elastyczne](elastic-pool-overview.md).

## <a name="estimate-azure-sql-database-costs"></a>Oszacuj koszty Azure SQL Database

Skorzystaj z [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) , aby oszacować koszty dla różnych konfiguracji Azure SQL Database. Informacje i Cennik na poniższej ilustracji są tylko na przykład:

:::image type="content" source="media/cost-management/pricing-calc.png" alt-text="Przykład Azure SQL Database kalkulatora cen":::

Możesz również oszacować sposób, w jaki różne opcje zasad przechowywania wpływają na koszt. Informacje i Cennik na poniższej ilustracji są tylko na przykład:

:::image type="content" source="media/cost-management/backup-storage.png" alt-text="Przykład Azure SQL Database kalkulatora cen dla magazynu":::


## <a name="understand-the-full-billing-model-for-azure-sql-database"></a>Zapoznaj się z pełnym modelem rozliczeń dla Azure SQL Database

Azure SQL Database działa w infrastrukturze platformy Azure, która narasta koszty wraz z Azure SQL Database podczas wdrażania nowego zasobu. Ważne jest, aby zrozumieć, że dodatkowa infrastruktura może naliczać koszt. Ten koszt należy zarządzać po wprowadzeniu zmian w wdrożonych zasobach. 


Azure SQL Database (z wyjątkiem bezserwerowych) jest rozliczana według przewidywalnej stawki godzinowej. Jeśli baza danych SQL jest aktywna przez czas krótszy niż jedna godzina, opłaty są naliczane za każdą godzinę, gdy baza danych istnieje przy użyciu najwyższej wybranej warstwy usług, magazynu i operacji we/wy zastosowanej w tej godzinie, niezależnie od użycia lub aktywności bazy danych krótszej niż godzina.


### <a name="using-monetary-credit-with-azure-sql-database"></a>Korzystanie z środków pieniężnych w Azure SQL Database

Opłaty za Azure SQL Database mogą być naliczane przy użyciu przedpłaty za korzystanie z platformy Azure (wcześniej zwanego zobowiązaniem pieniężnym). Nie można jednak używać środków zaliczkowych na platformie Azure, aby zapłacić za opłaty za produkty i usługi innych firm, w tym te z portalu Azure Marketplace.

## <a name="review-estimated-costs-in-the-azure-portal"></a>Przeglądanie szacowanych kosztów w witrynie Azure Portal

Po przejściu przez proces tworzenia Azure SQL Database można zobaczyć szacowane koszty podczas konfigurowania warstwy obliczeniowej. 

Aby uzyskać dostęp do tego ekranu, wybierz pozycję **Konfiguruj bazę danych** na karcie **podstawowe** na stronie **Tworzenie SQL Database** . Informacje i Cennik na poniższej ilustracji są tylko na przykład:

  :::image type="content" source="media/cost-management/cost-estimate.png" alt-text="Przykład przedstawiający oszacowanie kosztów w Azure Portal":::



Jeśli subskrypcja platformy Azure ma limit wydatków, platforma Azure nie pozwala na korzystanie z wydatków w wysokości. Twoje środki są używane podczas tworzenia i używania zasobów platformy Azure. Po osiągnięciu limitu kredytu wdrożone zasoby są wyłączone dla pozostałej części okresu rozliczeniowego. Nie możesz zmienić limitu kredytowego, ale możesz go usunąć. Aby uzyskać więcej informacji na temat limitów wydatków, zobacz [limit wydatków platformy Azure](../../cost-management-billing/manage/spending-limit.md).

## <a name="monitor-costs"></a>Monitorowanie kosztów

Po rozpoczęciu korzystania z Azure SQL Database można zobaczyć szacowane koszty w portalu. Aby zapoznać się z oszacowaniem kosztów, wykonaj następujące kroki:

1. Zaloguj się do Azure Portal i przejdź do grupy zasobów usługi Azure SQL Database. Grupę zasobów można zlokalizować, przechodząc do bazy danych programu, a następnie wybierając pozycję **Grupa zasobów** w sekcji **Przegląd** .
1. W menu wybierz pozycję **Analiza kosztów**.
1. Wyświetlanie **skumulowanych kosztów** i Ustawianie wykresu w dół do **nazwy usługi**. Ten wykres pokazuje oszacowanie bieżących kosztów SQL Database. Aby zawęzić koszty dla całej strony do Azure SQL Database, wybierz pozycję **Dodaj filtr** , a następnie wybierz pozycję **Azure SQL Database**. Informacje i Cennik na poniższej ilustracji są tylko na przykład:

   :::image type="content" source="media/cost-management/cost-analysis.png" alt-text="Przykład przedstawiający skumulowane koszty w Azure Portal":::

W tym miejscu możesz dowiedzieć się o kosztach własnych. Aby uzyskać więcej informacji na temat różnych ustawień analizy kosztów, zobacz [Rozpocznij analizowanie kosztów](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="create-budgets"></a>Tworzenie budżetów

<!-- Note to Azure service writer: Modify the following as needed for your service. -->

W celu zarządzania kosztami można utworzyć [budżety](../../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oraz [alerty](../../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn), które automatycznie powiadamiają uczestników o anomaliach w wydatkach i o ryzyku nadmiernych wydatków. Alerty są oparte na wydatkach w porównaniu z budżetem i progami kosztów. Budżety i alerty są tworzone dla subskrypcji platformy Azure i grup zasobów, dzięki czemu są one przydatne jako część ogólnej strategii monitorowania kosztów. 

Budżety można utworzyć za pomocą filtrów dla określonych zasobów lub usług na platformie Azure, jeśli chcesz mieć większą szczegółowość w monitorowaniu. Filtry ułatwiają zagwarantowanie, że nie zostaną przypadkowo utworzone nowe zasoby, które będą kosztowały dodatkowe pieniądze. Aby uzyskać więcej informacji na temat opcji filtrowania podczas tworzenia budżetu, zobacz [Opcje grup i filtrów](../../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Eksportowanie danych kosztów

Możesz również [wyeksportować dane kosztu](../../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) do konta magazynu. Jest to przydatne, gdy potrzebujesz lub inne osoby mogą przeprowadzić dodatkową analizę danych pod kątem kosztów. Na przykład zespoły finansowe mogą analizować dane przy użyciu programu Excel lub Power BI. Możesz wyeksportować koszty codziennie, co tydzień lub miesięcznie i ustawić niestandardowy zakres dat. Eksportowanie danych o kosztach jest zalecanym sposobem na pobranie z nich zbiorów.


## <a name="other-ways-to-manage-and-reduce-costs-for-azure-sql-database"></a>Inne sposoby zarządzania i obniżyć koszty Azure SQL Database

Azure SQL Database umożliwia również skalowanie zasobów w górę lub w dół w celu kontrolowania kosztów w zależności od potrzeb aplikacji. Aby uzyskać szczegółowe informacje, zobacz [dynamiczne skalowanie zasobów bazy danych](scale-resources.md).

Oszczędność pieniędzy dzięki zatwierdzeniu do rezerwacji zasobów obliczeniowych przez jeden do trzech lat. Aby uzyskać szczegółowe informacje, zobacz temat [oszczędność kosztów zasobów z zarezerwowaną pojemnością](reserved-capacity-overview.md).


## <a name="next-steps"></a>Następne kroki

- Dowiedz się [, jak zoptymalizować inwestycję w chmurę za pomocą Azure Cost Management](../../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Dowiedz się więcej o zarządzaniu kosztami przy użyciu [analizy kosztów](../../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Dowiedz się więcej na temat [zapobiegania nieoczekiwanym kosztom](../../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
- Zapoznaj się z [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) kurs uczenia z przewodnikiem.