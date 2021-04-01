---
title: Planowanie i zarządzanie kosztami usługi Azure Cosmos DB
description: Dowiedz się, jak planować i zarządzać kosztami Azure Cosmos DB przy użyciu analizy kosztów w programie Azure Portal.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/19/2020
ms.openlocfilehash: 2bea2324817986654de6689a2be15d0cbf999b38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98602138"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Planowanie i zarządzanie kosztami usługi Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

W tym artykule opisano sposób planowania kosztów Azure Cosmos DB i zarządzania nimi. Najpierw użyj kalkulatora pojemności Azure Cosmos DB, aby oszacować koszt obciążenia przed utworzeniem zasobów. Później możesz sprawdzić szacowany koszt i rozpocząć tworzenie zasobów.

Po rozpoczęciu korzystania z Azure Cosmos DB zasobów Użyj funkcji Cost Management do ustawiania budżetów i monitorowania kosztów. Możesz również przejrzeć prognozowane koszty i zidentyfikować trendy wydatków, aby identyfikować obszary, w których może być konieczne działanie. Koszty Azure Cosmos DB są tylko częścią miesięcznych kosztów rachunku na korzystanie z platformy Azure. Chociaż w tym artykule wyjaśniono, jak planować i zarządzać kosztami Azure Cosmos DB, opłaty są naliczane za wszystkie usługi i zasoby platformy Azure używane w ramach subskrypcji platformy Azure, w tym usługi innych firm.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="provisioned-throughput-or-serverless"></a>Nieobsługiwana przepływność lub bezserwerowy

Azure Cosmos DB obsługuje dwa typy trybów pojemności: [zainicjowana przepływność](set-throughput.md) i [bezserwerowy](serverless.md). Sposób naliczania opłat za użycie Azure Cosmos DB różni się między tymi dwoma trybami, dlatego ważne jest, aby wybrać ten, który najlepiej sprawdza się w obciążeniu. Zapoznaj się z tematem [jak wybrać między niezależną przepływność i bezserwerowym](throughput-serverless.md) artykułem, aby uzyskać wskazówki i zalecenia dotyczące sposobu dokonania tego wyboru.

### <a name="cost-analysis"></a>Analiza kosztów

Analiza kosztów w Cost Management obsługuje większość typów kont platformy Azure, ale nie wszystkie z nich. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn). Aby wyświetlić dane dotyczące kosztów, wymagany jest co najmniej dostęp do odczytu dla konta platformy Azure. Aby uzyskać informacje na temat przypisywania dostępu do danych usługi Azure Cost Management, zobacz [Przypisywanie dostępu do danych](../cost-management-billing/costs/assign-access-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="estimating-provisioned-throughput-costs-before-using-azure-cosmos-db"></a>Szacowanie zainicjowanych kosztów przepływności przed użyciem Azure Cosmos DB

Jeśli planujesz używać Azure Cosmos DB w trybie przepływności, użyj [kalkulatora pojemności Azure Cosmos DB](https://cosmos.azure.com/capacitycalculator/) , aby oszacować koszty przed utworzeniem zasobów na koncie usługi Azure Cosmos. Kalkulator pojemności służy do uzyskiwania szacunku wymaganej przepływności i kosztu obciążenia. Skonfigurowanie baz danych i kontenerów platformy Azure Cosmos z właściwą ilością przepływności lub [jednostek żądań (ru/s)](request-units.md)dla obciążenia jest niezbędne w celu zoptymalizowania kosztów i wydajności. Musisz wprowadzić szczegóły, takie jak typ interfejsu API, liczba regionów, rozmiar elementu, żądania odczytu/zapisu na sekundę, Łączna ilość przechowywanych danych w celu uzyskania oszacowania kosztów. Aby dowiedzieć się więcej na temat kalkulatora pojemności, zobacz artykuł dotyczący [szacowania](estimate-ru-with-capacity-planner.md) .

Poniższy zrzut ekranu przedstawia przepływność i oszacowanie kosztów za pomocą kalkulatora pojemności:

:::image type="content" source="./media/plan-manage-costs/capacity-calculator-cost-estimate.png" alt-text="Oszacowanie kosztów w Azure Cosmos DB Kalkulator pojemności":::

## <a name="estimating-serverless-costs-before-using-azure-cosmos-db"></a><a id="estimating-serverless-costs"></a> Szacowanie kosztów bezserwerowych przed użyciem Azure Cosmos DB

Jeśli planujesz używać Azure Cosmos DB w trybie bezserwerowym, należy oszacować, ile [jednostek żądań](request-units.md) i GB miejsca na magazyn może zużywać miesięcznie. Aby oszacować wymaganą liczbę jednostek żądań, można ocenić ilość operacji w bazie danych, które zostaną wystawione w miesiącu, i pomnożyć ich kwoty według odpowiednich kosztów RU. W poniższej tabeli wymieniono szacowane opłaty za usługę RU dla wspólnych operacji bazy danych:

| Operacja | Szacowany koszt | Uwagi |
| --- | --- | --- |
| Tworzenie elementu | 5 jednostek ru | Średni koszt dla elementu 1 KB o długości poniżej 5 właściwości do indeksowania |
| Aktualizowanie elementu | 10 jednostek RU | Średni koszt dla elementu 1 KB o długości poniżej 5 właściwości do indeksowania |
| Odczytaj poszczególne elementy według identyfikatora i klucza partycji (punkt-odczyt) | 1 RU | Średni koszt dla elementu 1 KB |
| Usuwanie elementu | 5 jednostek ru | |
| Wykonaj zapytanie | 10 jednostek RU | Średni koszt zapytania, które w pełni wykorzystuje [indeksowanie](index-overview.md) i zwraca 100 wyników lub mniej |

> [!IMPORTANT] 
> Zwróć uwagę na uwagi z powyższej tabeli. Aby uzyskać dokładniejsze oszacowanie rzeczywistych kosztów operacji, można użyć [emulatora Azure Cosmos DB](local-emulator.md) i [zmierzyć dokładny koszt operacji](find-request-unit-charge.md)na jednostkę ru. Chociaż emulator Azure Cosmos DB nie obsługuje bezserwerowego programu, raport jest naliczany przez standardowy RU dla operacji bazy danych i może służyć do tego oszacowania.

Po przeprowadzeniu obliczenia łącznej liczby jednostek żądań i GB miejsca w magazynie, które prawdopodobnie będą zużywane przez miesiąc, następująca formuła zwróci oszacowanie kosztów: **([liczba jednostek żądań]/1 000 000 * $0,25) + ([GB magazynu] * $0,25)**.

> [!NOTE]
> Koszty pokazane w poprzednim przykładzie służą wyłącznie do celów demonstracyjnych. Aby uzyskać najnowsze informacje o cenach, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/) .

## <a name="review-estimated-costs-in-the-azure-portal"></a>Przeglądanie szacowanych kosztów w witrynie Azure Portal

Po rozpoczęciu korzystania z Azure Cosmos DB zasobów z Azure Portal można zobaczyć szacowane koszty. Aby zapoznać się z oszacowaniem kosztów, wykonaj następujące kroki:

1. Zaloguj się do Azure Portal i przejdź do swojego konta usługi Azure Cosmos.
1. Przejdź do sekcji **Przegląd** .
1. Sprawdź wykres **kosztów** u dołu. Ten wykres pokazuje oszacowanie bieżącego kosztu w konfigurowalnym okresie:
1. Utwórz nowy kontener, taki jak kontener grafu.
1. Wprowadź przepływność wymaganą dla obciążenia, np. 400 RU/s. Po wprowadzeniu wartości przepływności można zobaczyć oszacowanie cen, jak pokazano na poniższym zrzucie ekranu:

   :::image type="content" source="./media/plan-manage-costs/cost-estimate-portal.png" alt-text="Szacowanie kosztów w Azure Portal":::

Jeśli subskrypcja platformy Azure ma limit wydatków, platforma Azure nie pozwala na korzystanie z wydatków w wysokości. Twoje środki są używane podczas tworzenia i używania zasobów platformy Azure. Po osiągnięciu limitu kredytu wdrożone zasoby są wyłączone dla pozostałej części okresu rozliczeniowego. Nie możesz zmienić limitu kredytowego, ale możesz go usunąć. Aby uzyskać więcej informacji na temat limitów wydatków, zobacz [limit wydatków platformy Azure](../cost-management-billing/manage/spending-limit.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

Opłaty za Azure Cosmos DB mogą być naliczane przy użyciu przedpłaty za korzystanie z platformy Azure (wcześniej zwanego zobowiązaniem pieniężnym). Nie można jednak używać środków zaliczkowych na platformie Azure, aby zapłacić za opłaty za produkty i usługi innych firm, w tym te z portalu Azure Marketplace.

## <a name="monitor-costs"></a>Monitorowanie kosztów

Gdy korzystasz z zasobów z Azure Cosmos DB, naliczane są koszty. Koszty jednostkowe użycia zasobów różnią się w zależności od przedziałów czasu (sekundy, minuty, godziny i dni) lub według użycia jednostki żądania. Po rozpoczęciu korzystania z Azure Cosmos DB koszty są naliczane i można je wyświetlić w okienku [Analiza kosztów](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) w Azure Portal.

Korzystając z analizy kosztów, można wyświetlić Azure Cosmos DB koszty w wykresach i tabelach dla różnych przedziałów czasu. Kilka przykładów jest dziennie, bieżących, poprzedniego miesiąca i roku. Możesz również wyświetlić koszty związane z budżetami i prognozowanymi kosztami. Przełączenie na dłuższe widoki w czasie może pomóc w identyfikowaniu trendów wydatków i sprawdzaniu, gdzie mogły wystąpić nadwyżki. Jeśli utworzono budżety, możesz również łatwo zobaczyć miejsce ich przekroczenia. 

Aby wyświetlić koszty Azure Cosmos DB w analizie kosztów:

1. Zaloguj się do [Azure Portal](https://portal.azure.com).

1. Otwórz zakres w Azure Portal a następnie wybierz pozycję **Analiza kosztów** w menu. Na przykład przejdź do pozycji **subskrypcje**, wybierz subskrypcję z listy, a następnie wybierz pozycję  **Analiza kosztów** w menu. Wybierz **zakres** , aby przełączyć się do innego zakresu w analizie kosztów.

1. Domyślnie koszt wszystkich usług jest pokazywany na pierwszym wykresie pierścieniowym. Wybierz obszar na wykresie z etykietą "Azure Cosmos DB".

1. Aby zawęzić koszty pojedynczej usługi, takiej jak Azure Cosmos DB, wybierz pozycję **Dodaj filtr** , a następnie wybierz pozycję **nazwa usługi**. Następnie wybierz **Azure Cosmos DB** z listy. Oto przykład przedstawiający koszty tylko Azure Cosmos DB:
 
   :::image type="content" source="./media/plan-manage-costs/cost-analysis-pane.png" alt-text="Monitorowanie kosztów za pomocą okienka analiza kosztów":::

W poprzednim przykładzie zobaczysz bieżący koszt dla Azure Cosmos DB w miesiącu lutego. Wykresy zawierają również koszty Azure Cosmos DB według lokalizacji i grupy zasobów.

## <a name="create-budgets"></a>Tworzenie budżetów

W celu zarządzania kosztami można utworzyć [budżety](../cost-management-billing/costs/tutorial-acm-create-budgets.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) oraz [alerty](../cost-management-billing/costs/cost-mgt-alerts-monitor-usage-spending.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn), które automatycznie powiadamiają uczestników o anomaliach w wydatkach i o ryzyku nadmiernych wydatków. Alerty są oparte na wydatkach w porównaniu z budżetem i progami kosztów. Budżety i alerty są tworzone dla subskrypcji platformy Azure i grup zasobów, dzięki czemu są one przydatne jako część ogólnej strategii monitorowania kosztów. 

Budżety można utworzyć za pomocą filtrów dla określonych zasobów lub usług na platformie Azure, jeśli chcesz mieć większą szczegółowość w monitorowaniu. Filtry ułatwiają zagwarantowanie, że nie zostaną przypadkowo utworzone nowe zasoby, które będą kosztowały dodatkowe pieniądze. Aby uzyskać więcej informacji na temat opcji filtrowania podczas tworzenia budżetu, zobacz [Opcje grup i filtrów](../cost-management-billing/costs/group-filter.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).

## <a name="export-cost-data"></a>Eksportowanie danych kosztów

Możesz również [wyeksportować dane kosztu](../cost-management-billing/costs/tutorial-export-acm-data.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) do konta magazynu. Jest to przydatne, gdy potrzebujesz lub inne osoby mogą przeprowadzić dodatkową analizę danych pod kątem kosztów. Na przykład zespoły finansowe mogą analizować dane przy użyciu programu Excel lub Power BI. Możesz wyeksportować koszty codziennie, co tydzień lub miesięcznie i ustawić niestandardowy zakres dat. Eksportowanie danych o kosztach jest zalecanym sposobem na pobranie z nich zbiorów.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej na temat sposobu działania cen w Azure Cosmos DB:

* [Model cen w usłudze Azure Cosmos DB](how-pricing-works.md)
* [Optymalizacja zaaprowizowanej przepływności w usłudze Azure Cosmos DB](optimize-cost-throughput.md)
* [Optymalizacja kosztów zapytania w usłudze Azure Cosmos DB](./optimize-cost-reads-writes.md)
* [Optymalizacja kosztów magazynu w usłudze Azure Cosmos DB](optimize-cost-storage.md)
* Dowiedz się [, jak zoptymalizować inwestycję w chmurę za pomocą Azure Cost Management](../cost-management-billing/costs/cost-mgt-best-practices.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
* Dowiedz się więcej o zarządzaniu kosztami przy użyciu [analizy kosztów](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
* Dowiedz się więcej na temat [zapobiegania nieoczekiwanym kosztom](../cost-management-billing/cost-management-billing-overview.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn).
* Zapoznaj się z [Cost Management](/learn/paths/control-spending-manage-bills?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn) kurs uczenia z przewodnikiem.