---
title: Przewodnik przejścia projektanta widoku Azure Monitor do skoroszytów
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 3179daec361aa287b79c5e9a468908d0ccdd2982
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92143961"
---
# <a name="azure-monitor-view-designer-to-workbooks-transition-guide"></a>Przewodnik przejścia projektanta widoku Azure Monitor do skoroszytów
[Projektant widoków](view-designer.md) jest funkcją Azure monitor, która umożliwia tworzenie niestandardowych widoków, które ułatwiają wizualizację danych w obszarze roboczym log Analytics, z wykresami, listami i osiami czasu. Zostały one przenoszone do skoroszytów, aby zapewnić elastyczną kanwę do analizy danych i tworzenia bogatych raportów wizualnych w Azure Portal. Ten artykuł pomaga w wprowadzeniu przejścia z projektanta widoku do skoroszytów. 


## <a name="workbooks-overview"></a>Przegląd skoroszytów
[Skoroszyty](../insights/vminsights-workbooks.md) łączą tekst, [kwerendy dzienników](/azure/data-explorer/kusto/query/), metryki i parametry w rozbudowanych raportach interaktywnych. Członkowie zespołu mający ten sam dostęp do zasobów platformy Azure mogą również edytować skoroszyty.

Skoroszyty są przydatne w scenariuszach takich jak:

-   Zbadaj użycie maszyny wirtualnej, gdy nie wiesz, jakie są interesujące Cię metryki: użycie procesora CPU, miejsce na dysku, pamięć, zależności sieci itp. W przeciwieństwie do innych narzędzi analitycznych użycia, skoroszyty umożliwiają łączenie wielu rodzajów wizualizacji i analiz, dzięki czemu są wspaniałe dla tego rodzaju eksploracji o dowolnej postaci.
-   Objaśnienie Twojego zespołu, jak ostatnio obsługiwana maszyna wirtualna jest wykonywana, pokazując metryki dla liczników kluczy i innych zdarzeń dzienników.
-   Udostępnianie wyników eksperymentu zmiany rozmiarów maszyny wirtualnej z innymi członkami zespołu. Można wyjaśnić cele eksperymentu z tekstem, a następnie pokazać wszystkie metryki użycia i zapytania analityczne używane do szacowania eksperymentu, a także wyraźne wywołania, dla których każda Metryka przekracza lub poniżej wartości docelowej.
-   Raportowanie wpływu awarii na korzystanie z maszyny wirtualnej, łączenie danych, wyjaśnienie tekstu i Omówienie następnych kroków, aby zapobiec awarii w przyszłości.


## <a name="why-convert-view-designer-dashboards-to-workbooks"></a>Dlaczego warto konwertować pulpity nawigacyjne projektanta widoków na skoroszyty?

Projektant widoków oferuje możliwość generowania różnych widoków i wizualizacji opartych na zapytaniach. Wiele dostosowań najwyższego poziomu pozostaje jednak ograniczonych, na przykład formatowanie układów siatek i kafelków czy wybieranie alternatywnej grafiki mającej reprezentować dane. Projektant widoków jest ograniczony do dziewięciu unikatowych kafelków, które reprezentują dane.

Skoroszyty to platforma, która uwalnia pełny potencjał danych. skoroszyty nie tylko zachowują wszystkie możliwości, ale również obsługują dodatkowe funkcje za pomocą tekstu, metryk, parametrów i wiele innych. Na przykład skoroszyty umożliwiają użytkownikom konsolidowanie gęstych siatek i dodawanie pasków wyszukiwania w celu łatwego filtrowania i analizowania danych. 

### <a name="advantages-of-using-workbooks-over-view-designer"></a>Zalety używania skoroszytów przez projektanta widoków

* Obsługuje zarówno dzienniki, jak i metryki.
* Zezwala na widoki osobiste dla poszczególnych widoków kontroli dostępu i skoroszytów udostępnionych.
* Niestandardowe opcje układu z kartami, rozmiarami i kontrolkami skalowania.
* Obsługa zapytań w wielu obszarach roboczych Log Analytics, aplikacjach Application Insights i subskrypcjach.
* Włącza parametry niestandardowe dynamicznie aktualizują skojarzone wykresy i wizualizacje.
* Obsługa galerii szablonów z publicznej usługi GitHub.

Chociaż ten przewodnik zawiera proste kroki umożliwiające bezpośrednie odtworzenie kilku często używanych widoków projektanta widoku, skoroszyty umożliwiają użytkownikom swobodne tworzenie i projektowanie własnych niestandardowych wizualizacji i metryk. Poniższy zrzut ekranu pochodzi z [szablonu użycie obszaru roboczego](https://go.microsoft.com/fwlink/?linkid=874159&resourceId=Azure%20Monitor&featureName=Workbooks&itemId=community-Workbooks%2FAzure%20Monitor%20-%20Workspaces%2FWorkspace%20Usage&workbookTemplateName=Workspace%20Usage&func=NavigateToPortalFeature&type=workbook) i pokazuje przykład, jakie skoroszyty mogą tworzyć:


![Przykład aplikacji ze skoroszytami](media/view-designer-conversion-overview/workbook-template-example.jpg)


## <a name="how-to-start-using-workbooks"></a>Jak zacząć używać skoroszytów
Otwarte skoroszyty ze skoroszytów są włączane w Log Analytics obszarach roboczych jako element na pasku nawigacyjnym po stronie bezpośrednio pod lokalizacją projektanta widoku.

![Nawigacja po skoroszytach](media/view-designer-conversion-overview/workbooks-nav.png)

Po wybraniu zostanie wyświetlona Galeria zawierająca listę wszystkich zapisanych skoroszytów i szablonów dla Twojego obszaru roboczego.

![Galeria skoroszytów](media/view-designer-conversion-overview/workbooks-gallery.png)

Aby rozpocząć nowy skoroszyt, możesz wybrać **pusty** szablon w obszarze **Szybki Start**lub **nową** ikonę na górnym pasku nawigacyjnym. Aby wyświetlić szablony lub powrócić do zapisanych skoroszytów, wybierz element z galerii lub Wyszukaj nazwę na pasku wyszukiwania.

Aby zapisać skoroszyt, należy zapisać raport z określonym tytułem, subskrypcją, grupą zasobów i lokalizacją.
Skoroszyt spowoduje wypełnienie tych samych ustawień co obszar roboczy LA, z tą samą subskrypcją, grupą zasobów, ale użytkownicy mogą zmienić te ustawienia raportu. Skoroszyty są domyślnie zapisywane do *Moje raporty*, dostępne tylko dla poszczególnych użytkowników. Można je także zapisywać bezpośrednio w udostępnionych raportach lub udostępniać je później.

![Zapisywanie skoroszytów](media/view-designer-conversion-overview/workbooks-save.png)

## <a name="next-steps"></a>Następne kroki

- [Opcje konwersji](view-designer-conversion-options.md)