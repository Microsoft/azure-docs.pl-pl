---
title: Planowanie i zarządzanie kosztami Azure Data Factory
description: W tym artykule opisano, jak można planować i zarządzać kosztami Azure Data Factory
author: shirleywangmsft
ms.author: shwang
ms.service: data-factory
ms.topic: conceptual
ms.date: 05/14/2020
ms.openlocfilehash: d89275acdb8e13a792ea3166d874fabf911bb686
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100372896"
---
# <a name="plan-and-manage-costs-for-azure-data-factory"></a>Planowanie i zarządzanie kosztami Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory to bezserwerowa i elastyczna usługa integracji danych skompilowana dla skalowania w chmurze.  Oznacza to, że nie ma obliczeń o stałym rozmiarze, które należy zaplanować dla obciążenia szczytowego; należy określić, ile zasobów ma zostać przydzielone na żądanie na potrzeby operacji, co umożliwia projektowanie procesów ETL w bardziej skalowalny sposób. Ponadto funkcja ADF jest rozliczana zgodnie z planem opartym na zużyciu, co oznacza, że płacisz tylko za to, czego używasz.

W tym artykule opisano sposób planowania kosztów Azure Data Factory i zarządzania nimi.

*   Najpierw na początku projektu ETL należy przeprowadzić weryfikację koncepcji i użyć kombinacji użycia poszczególnych potoków i kalkulatora cen do oszacowania kosztów.
*   Po wdrożeniu potoków w środowisku produkcyjnym należy użyć funkcji zarządzania kosztami do ustawiania budżetów i monitorowania kosztów. Możesz również przejrzeć przewidywane koszty i zidentyfikować trendy wydatków.
*   Ponadto można wyświetlić informacje o zużyciu potoku i użyciu poszczególnych działań, aby zrozumieć, które potoki i jakie działania są Costliest i zidentyfikować kandydatów do obniżenia kosztów.

## <a name="estimate-costs-using-pipeline-and-activity-run-consumption-and-pricing-calculator"></a>Szacowanie kosztów za pomocą użycia potoku i uruchomienia działania oraz kalkulatora cen

Możesz użyć [kalkulatora cen ADF](https://azure.microsoft.com/pricing/calculator/?service=data-factory) , aby uzyskać szacunkowy koszt uruchamiania obciążenia ETL w Azure Data Factory.  Aby użyć kalkulatora, musisz wprowadzić szczegóły, takie jak liczba uruchomień działania, liczba godzin korzystania z jednostek integracji danych, typ obliczeń używany do przepływu danych, liczba rdzeni, liczba wystąpień, czas trwania wykonywania itd.

Jednym z często zadawanych pytań dotyczących kalkulatora cen jest to, jakie wartości powinny być używane jako dane wejściowe.  W fazie weryfikacji koncepcji można przeprowadzić przebiegi próbne za pomocą przykładowych zestawów danych, aby zrozumieć użycie różnych mierników ADF.  Następnie na podstawie zużycia dla przykładowego zestawu danych można wypróbować użycie dla pełnego zestawu danych i harmonogramu operacjonalizacji.

> [!NOTE]
> Ceny używane w poniższych przykładach są hipotetyczne i nie są przeznaczone do oznaczania rzeczywistych cen.

Załóżmy na przykład, że należy przenieść 1 TB danych codziennie od AWS S3 do Azure Data Lake Gen2.  Aby zmierzyć przepływność pozyskiwania danych i zrozumieć odpowiednie użycie rozliczeń, można przeprowadzić ZATWIERDZAnie 100 GB danych.

Poniżej znajduje się szczegóły przykładowego przebiegu działania kopiowania (rzeczywisty przebieg będzie różny w zależności od kształtu określonego zestawu danych, szybkości sieci, limitów ruchu wychodzącego na koncie S3, limitów przychodzących na ADLS Gen2 i innych czynników).

![Przebieg kopiowania S3](media/plan-manage-costs/s3-copy-run-details.png)

Korzystając z [monitorowania zużycia na poziomie uruchomienia potoku](#monitor-consumption-at-pipeline-run-level), można zobaczyć odpowiednie ilości zużycia liczników przenoszenia danych:

![Użycie potoku kopiowania S3](media/plan-manage-costs/s3-copy-pipeline-consumption.png)

W związku z tym łączną liczbę DIU godzin do przeniesienia 1 TB dziennie przez cały miesiąc:

1,2667 (DIU-godz.) * (1 TB/100 GB) * 30 (dni w miesiącu) = 380 DIU-hours

Teraz możesz podłączyć 30 uruchomień działań i 380 DIU godzin do kalkulatora cen ADF, aby uzyskać szacunkowe rozliczenia miesięczne:

![Kalkulator cen kopiowania S3](media/plan-manage-costs/s3-copy-pricing-calculator.png)

## <a name="use-budgets-and-cost-alerts"></a>Używanie alertów dotyczących budżetu i kosztów

W celu zarządzania kosztami można utworzyć [budżety](../cost-management-billing/costs/tutorial-acm-create-budgets.md) oraz alerty, które automatycznie powiadamiają uczestników o anomaliach w wydatkach i o ryzyku nadmiernych wydatków.  Alerty są oparte na wydatkach w porównaniu z budżetem i progami kosztów.  Podczas tworzenia budżetu można wykonać tę czynność na poziomie subskrypcji lub niższej szczegółowości przez dodanie dodatkowych filtrów, takich jak identyfikator zasobu i nazwa miernika.  Nie można jednak tworzyć budżetów dla poszczególnych potoków w ramach fabryki.

## <a name="monitor-costs-at-factory-level"></a>Monitoruj koszty na poziomie fabryki

Po rozpoczęciu korzystania z Azure Data Factory można zobaczyć koszty poniesione w okienku [Analiza kosztów](../cost-management-billing/costs/quick-acm-cost-analysis.md) w Azure Portal.

1. Aby wyświetlić [analizę kosztów](../cost-management-billing/costs/quick-acm-cost-analysis.md), otwórz okno **Cost Management + rozliczanie** , wybierz pozycję **Zarządzanie kosztami** z menu, a następnie wybierz pozycję **Otwórz analizę kosztów**.
2. Widok domyślny pokazuje skumulowane koszty bieżącego miesiąca.  Można zmienić zakres czasu i różne stopnia szczegółowości, na przykład codziennie lub co miesiąc.
3. Aby zawęzić koszty pojedynczej usługi, takiej jak Azure Data Factory, wybierz pozycję **Dodaj filtr** , a następnie wybierz pozycję **nazwa usługi**.  Następnie wybierz z listy pozycję **Azure Data Factory v2** .
4. Można dodać dodatkowe filtry w celu przeanalizowania kosztu określonego wystąpienia fabryki i konkretnego stopnia szczegółowości licznika ADF.

   ![Analiza kosztów](media/plan-manage-costs/cost-analysis.png)

## <a name="monitor-consumption-at-pipeline-run-level"></a>Monitorowanie zużycia na poziomie uruchomienia potoku

W zależności od typów działań w potoku, ilości danych, które są przenoszone i przekształcane, a złożoność transformacji, wykonanie potoku spowoduje przejście do różnych liczników rozliczeń w Azure Data Factory.

Możesz wyświetlić ilość użycia dla różnych liczników dla poszczególnych potoków w środowisku użytkownika Azure Data Factory. Aby otworzyć środowisko monitorowania, wybierz pozycję **monitoruj & Zarządzaj** kafelkiem w bloku fabryka danych [Azure Portal](https://portal.azure.com/). Jeśli jesteś już w środowisku APD ADF, kliknij ikonę **monitora** na lewym pasku bocznym. Domyślny widok monitorowania to lista uruchomień potoku.

Kliknięcie przycisku **zużycia** obok nazwy potoku spowoduje wyświetlenie okna podręcznego z informacją o zużyciu dla uruchomienia potoku w ramach wszystkich działań w potoku.

![Zużycie przebiegu potoku](media/plan-manage-costs/pipeline-run-consumption.png)

![Szczegóły zużycia potoku](media/plan-manage-costs/pipeline-consumption-details.png)

Widok zużycie przebiegu potoku przedstawia ilość zużytą dla każdego miernika ADF dla określonego uruchomienia potoku, ale nie pokazuje rzeczywistej opłaty, ponieważ kwota rozliczana jest zależna od typu konta platformy Azure i typu używanej waluty.  Aby wyświetlić pełną listę obsługiwanych typów kont, zobacz [Omówienie danych usługi Cost Management](../cost-management-billing/costs/understand-cost-mgt-data.md).

## <a name="monitor-consumption-at-activity-run-level"></a>Monitoruj użycie na poziomie uruchomienia działania
Po zrozumieniu zagregowanego zużycia na poziomie uruchomienia potoku istnieją scenariusze, w których należy dalej przechodzenie do szczegółów i zidentyfikować, która jest najbardziej kosztowną aktywnością w ramach potoku.

Aby wyświetlić zużycie na poziomie działania, przejdź do interfejsu użytkownika narzędzia Monitor fabryki danych **&** . Na karcie **monitor** , w której widzisz listę uruchomień potoku, kliknij link **Nazwa potoku** , aby uzyskać dostęp do listy uruchomień działania w ramach uruchomienia potoku.  Kliknij przycisk **dane wyjściowe** obok nazwy działania i Znajdź właściwość **billableDuration** w danych wyjściowych JSON:

Oto przykład z uruchomienia działania kopiowania:

![Kopiuj dane wyjściowe](media/plan-manage-costs/copy-output.png)

A Oto przykład z przebiegu działania przepływu danych mapowania:

![Przepływu danych dane wyjściowe](media/plan-manage-costs/dataflow-output.png)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej na temat sposobu działania cen w Azure Data Factory:

- [Strona cennika Azure Data Factory](https://azure.microsoft.com/pricing/details/data-factory/ssis/)
- [Informacje na temat Azure Data Factory za poorednictwem przykładów](./pricing-concepts.md)
- [Azure Data Factory Kalkulator cen](https://azure.microsoft.com/pricing/calculator/?service=data-factory)