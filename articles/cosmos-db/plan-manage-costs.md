---
title: Planowanie i zarządzanie kosztami Azure Cosmos DB
description: Dowiedz się, jak planować i zarządzać kosztami Azure Cosmos DB przy użyciu analizy kosztów w programie Azure Portal.
author: SnehaGunda
ms.author: sngun
ms.custom: subject-cost-optimization
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: 278603887fe7d47b4be52b04f9f0864be1a1b75b
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92482251"
---
# <a name="plan-and-manage-costs-for-azure-cosmos-db"></a>Planowanie i zarządzanie kosztami Azure Cosmos DB

W tym artykule opisano sposób planowania kosztów Azure Cosmos DB i zarządzania nimi:

- Oszacuj, co będzie miało koszt przed utworzeniem zasobów
- Przejrzyj szacowane koszty po rozpoczęciu korzystania z zasobów
- Korzystanie z funkcji zarządzania kosztami w celu ustawiania budżetów i monitorowania kosztów
- Zapoznaj się z prognozowanymi kosztami i zidentyfikuj trendy wydatków, aby ujawnić obszary, w których może być konieczne działanie

Należy zrozumieć, że koszty Azure Cosmos DB są tylko częścią miesięcznych kosztów rachunku na korzystanie z platformy Azure. Jeśli używasz innych usług platformy Azure, opłaty są naliczane za wszystkie usługi i zasoby platformy Azure używane w ramach subskrypcji platformy Azure, w tym usługi innych firm. W tym artykule wyjaśniono, jak planować i zarządzać kosztami Azure Cosmos DB. Po zapoznaniu się z zarządzaniem kosztami Azure Cosmos DB możesz zastosować podobne metody, aby zarządzać kosztami wszystkich usług platformy Azure używanych w ramach subskrypcji.

## <a name="prerequisites"></a>Wymagania wstępne

Analiza kosztów obsługuje różne typy kont platformy Azure. Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md). Aby wyświetlić dane kosztów, potrzebujesz przynajmniej dostępu do odczytu dla Twojego konta platformy Azure. Aby uzyskać informacje na temat przypisywania dostępu do danych usługi Azure Cost Management, zobacz [Przypisywanie dostępu do danych](../cost-management-billing/costs/assign-access-acm-data.md).

## <a name="provisioned-throughput-or-serverless"></a>Nieobsługiwana przepływność lub bezserwerowy

Azure Cosmos DB obsługuje dwa typy trybów pojemności: [zainicjowana przepływność](set-throughput.md) i [bezserwerowy](serverless.md). Sposób naliczania opłat za użycie Azure Cosmos DB różni się między tymi dwoma trybami, dlatego ważne jest, aby wybrać ten, który najlepiej sprawdza się w obciążeniu. Zapoznaj się z tematem [jak wybrać między niezależną przepływność i bezserwerowym](throughput-serverless.md) artykułem, aby uzyskać wskazówki i zalecenia dotyczące sposobu dokonania tego wyboru.

## <a name="estimating-provisioned-throughput-costs-with-capacity-calculator"></a>Szacowanie zainicjowanych kosztów przepływności za pomocą kalkulatora pojemności

Jeśli planujesz używać Azure Cosmos DB w trybie przepływności, użyj [kalkulatora pojemności Azure Cosmos DB](https://cosmos.azure.com/capacitycalculator/) , aby oszacować koszty przed utworzeniem zasobów na koncie usługi Azure Cosmos. Kalkulator pojemności służy do uzyskiwania szacunku wymaganej przepływności i kosztu obciążenia. Skonfigurowanie baz danych i kontenerów platformy Azure Cosmos z właściwą ilością przepływności lub [jednostek żądań (ru/s)](request-units.md)dla obciążenia jest niezbędne w celu zoptymalizowania kosztów i wydajności. Musisz wprowadzić szczegóły, takie jak typ interfejsu API, liczba regionów, rozmiar elementu, żądania odczytu/zapisu na sekundę, Łączna ilość przechowywanych danych w celu uzyskania oszacowania kosztów. Aby dowiedzieć się więcej na temat kalkulatora pojemności, zobacz artykuł dotyczący [szacowania](estimate-ru-with-capacity-planner.md) .

Poniższy zrzut ekranu przedstawia przepływność i oszacowanie kosztów za pomocą kalkulatora pojemności:

:::image type="content" source="./media/plan-manage-costs/capacity-calculator-cost-estimate.png" alt-text="Oszacowanie kosztów w Azure Cosmos DB Kalkulator pojemności":::

## <a name="estimating-serverless-costs"></a>Szacowanie kosztów bezserwerowych

Jeśli planujesz używać Azure Cosmos DB w trybie bezserwerowym, należy oszacować, ile [jednostek żądań](request-units.md) i GB miejsca na magazyn może zużywać miesięcznie. Aby oszacować wymaganą liczbę jednostek żądań, można ocenić ilość operacji w bazie danych, które zostaną wystawione w miesiącu, i pomnożyć ich kwoty według odpowiednich kosztów RU. W poniższej tabeli wymieniono szacowane opłaty za usługę RU dla wspólnych operacji bazy danych:

| Operacja | Szacowany koszt | Uwagi |
| --- | --- | --- |
| Tworzenie elementu | 5 jednostek ru | Średni koszt dla elementu 1 KB o długości poniżej 5 właściwości do indeksowania |
| Aktualizowanie elementu | 10 jednostek RU | Średni koszt dla elementu 1 KB o długości poniżej 5 właściwości do indeksowania |
| Odczytaj poszczególne elementy według identyfikatora i klucza partycji (punkt-odczyt) | 1 RU | Średni koszt dla elementu 1 KB |
| Usuwanie elementu | 5 jednostek ru | |
| Wykonaj zapytanie | 10 jednostek RU | Średni koszt zapytania, które w pełni wykorzystuje [indeksowanie](index-overview.md) i zwraca 100 wyników lub mniej |

> [!IMPORTANT] 
> Zwróć uwagę na uwagi z powyższej tabeli. Aby uzyskać dokładniejsze oszacowanie rzeczywistych kosztów operacji, możesz użyć [emulatora usługi Azure Cosmos](local-emulator.md) i [zmierzyć dokładny koszt operacji na ru](find-request-unit-charge.md). Chociaż emulator usługi Azure Cosmos nie obsługuje bezserwerowego, raport jest naliczany jako standardowy RU dla operacji bazy danych i może służyć do tego oszacowania.

Po przeprowadzeniu obliczenia łącznej liczby jednostek żądań i GB miejsca w magazynie, które prawdopodobnie będą zużywane przez miesiąc, następująca formuła zwróci oszacowanie kosztów: **([liczba jednostek żądań]/1 000 000 * $0,25) + ([GB magazynu] * $0,25)**.

> [!NOTE]
> Koszty pokazane w poprzednim przykładzie służą wyłącznie do celów demonstracyjnych. Aby uzyskać najnowsze informacje o cenach, zobacz [stronę z cennikiem](https://azure.microsoft.com/pricing/details/cosmos-db/) .

## <a name="review-estimated-costs-from-the-azure-portal"></a>Przejrzyj szacowane koszty z Azure Portal

Po rozpoczęciu korzystania z Azure Cosmos DB zasobów z Azure Portal można zobaczyć szacowane koszty. Aby zapoznać się z oszacowaniem kosztów, wykonaj następujące kroki:

1. Zaloguj się do Azure Portal i przejdź do swojego konta usługi Azure Cosmos.
1. Przejdź do sekcji **Przegląd** .
1. Sprawdź wykres **kosztów** u dołu. Ten wykres pokazuje oszacowanie bieżącego kosztu w konfigurowalnym okresie:
1. Utwórz nowy kontener, taki jak kontener grafu.
1. Wprowadź przepływność wymaganą dla obciążenia, np. 400 RU/s. Po wprowadzeniu wartości przepływności można zobaczyć oszacowanie cen, jak pokazano na poniższym zrzucie ekranu:

   :::image type="content" source="./media/plan-manage-costs/cost-estimate-portal.png" alt-text="Oszacowanie kosztów w Azure Cosmos DB Kalkulator pojemności":::

## <a name="use-budgets-and-cost-alerts"></a>Używanie alertów dotyczących budżetu i kosztów

W celu zarządzania kosztami można utworzyć [budżety](../cost-management-billing/costs/tutorial-acm-create-budgets.md) oraz alerty, które automatycznie powiadamiają uczestników o anomaliach w wydatkach i o ryzyku nadmiernych wydatków. Alerty są oparte na wydatkach w porównaniu z budżetem i progami kosztów. Budżety i alerty są tworzone dla subskrypcji platformy Azure i grup zasobów, dzięki czemu są one przydatne jako część ogólnej strategii monitorowania kosztów. Mogą jednak mieć ograniczoną funkcjonalność do zarządzania poszczególnymi kosztami usług platformy Azure, takimi jak koszt Azure Cosmos DB, ponieważ są one przeznaczone do śledzenia kosztów na wyższym poziomie.

Jeśli subskrypcja platformy Azure ma limit wydatków, platforma Azure nie pozwala na korzystanie z wydatków w wysokości. Twoje środki są używane podczas tworzenia i używania zasobów platformy Azure. Po osiągnięciu limitu kredytowego wdrożone zasoby są wyłączone dla pozostałej części okresu rozliczeniowego. Nie możesz zmienić limitu kredytowego, ale możesz go usunąć. Aby uzyskać więcej informacji na temat limitów wydatków, zobacz [limit wydatków platformy Azure](../cost-management-billing/manage/spending-limit.md).

## <a name="monitor-costs"></a>Monitorowanie kosztów

Gdy korzystasz z zasobów z Azure Cosmos DB, naliczane są koszty. Koszty jednostkowe użycia zasobów różnią się w zależności od przedziałów czasu (sekundy, minuty, godziny i dni) lub według użycia jednostki żądania. Po rozpoczęciu korzystania z Azure Cosmos DB koszty są naliczane i można je wyświetlić w okienku [Analiza kosztów](../cost-management-billing/costs/quick-acm-cost-analysis.md) w Azure Portal.

Korzystając z analizy kosztów, można wyświetlić Azure Cosmos DB koszty w wykresach i tabelach dla różnych przedziałów czasu. Kilka przykładów jest dziennie, bieżących, poprzedniego miesiąca i roku. Możesz również wyświetlić koszty związane z budżetami i prognozowanymi kosztami. Przełączenie na dłuższe widoki w czasie może pomóc w identyfikowaniu trendów wydatków i sprawdzaniu, gdzie mogły wystąpić nadwyżki. Jeśli utworzono budżety, możesz również łatwo zobaczyć miejsce ich przekroczenia. Aby wyświetlić koszty Azure Cosmos DB w analizie kosztów:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Otwórz okno **Cost Management + rozliczanie** , wybierz pozycję **Zarządzanie kosztami** z menu, a następnie wybierz pozycję **Analiza kosztów**. Następnie można zmienić zakres dla określonej subskrypcji z listy rozwijanej **zakres** .

1. Domyślnie koszt wszystkich usług jest pokazywany na pierwszym wykresie pierścieniowym. Wybierz obszar na wykresie z etykietą "Azure Cosmos DB".

1. Aby zawęzić koszty pojedynczej usługi, takiej jak Azure Cosmos DB, wybierz pozycję **Dodaj filtr** , a następnie wybierz pozycję **nazwa usługi**. Następnie wybierz **Azure Cosmos DB** z listy. Oto przykład przedstawiający koszty tylko Azure Cosmos DB:
 
   :::image type="content" source="./media/plan-manage-costs/cost-analysis-pane.png" alt-text="Oszacowanie kosztów w Azure Cosmos DB Kalkulator pojemności":::

W poprzednim przykładzie zobaczysz bieżący koszt dla Azure Cosmos DB w miesiącu lutego. Wykresy zawierają również koszty Azure Cosmos DB według lokalizacji i grupy zasobów.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej na temat sposobu działania cen w Azure Cosmos DB:

* [Model cen w usłudze Azure Cosmos DB](how-pricing-works.md)
* [Optymalizacja zaaprowizowanej przepływności w usłudze Azure Cosmos DB](optimize-cost-throughput.md)
* [Optymalizacja kosztów zapytania w usłudze Azure Cosmos DB](./optimize-cost-reads-writes.md)
* [Optymalizuj koszt magazynu w Azure Cosmos DB](optimize-cost-storage.md)