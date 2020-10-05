---
title: Co to jest zespołowe przetwarzanie danych?
description: Zapewnia metodologię do nauki o danych, która umożliwia dostarczanie rozwiązań do analizy predykcyjnej i inteligentnych aplikacji.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: overview
ms.date: 1/10/2020
ms.author: tdsp
ms.custom: previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: fc9a5e604e6ac52eb0e853725123272ca924fa36
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91362235"
---
# <a name="what-is-the-team-data-science-process"></a>Co to jest zespołowe przetwarzanie danych?

Proces nauki o danych zespołowych (przetwarzania TDSP) to Agile, iteracyjna metodologia analityczna danych, która umożliwia wydajne dostarczanie rozwiązań do analizy predykcyjnej i inteligentnych aplikacji. PRZETWARZANIA TDSP pomaga ulepszyć współpracę zespołową i uczenie się, sugerując, w jaki sposób role zespołu najlepiej współpracują ze sobą. PRZETWARZANIA TDSP zawiera najlepsze rozwiązania i struktury od firmy Microsoft i innych liderów branżowych, aby pomóc w pomyślnym wdrożeniu inicjatyw dotyczących analizy danych. Ma ona na celu pomoc firmom w pełnym wykorzystaniu zalet programu analitycznego.

Ten artykuł zawiera omówienie przetwarzania TDSP i jego głównych składników. Udostępniamy ogólny opis procesu, który można zaimplementować przy użyciu różnych rodzajów narzędzi. Bardziej szczegółowy opis zadań projektu i ról związanych z cyklem życia procesu znajduje się w dodatkowych powiązanych tematach. Udostępnimy również wskazówki dotyczące implementacji przetwarzania TDSP przy użyciu określonego zestawu narzędzi i infrastruktury firmy Microsoft, które są używane do implementowania przetwarzania TDSP w naszych zespołach.

## <a name="key-components-of-the-tdsp"></a>Najważniejsze składniki przetwarzania TDSP

PRZETWARZANIA TDSP ma następujące kluczowe składniki:

- Definicja **cyklu życia analizy danych**
- **Standardowa struktura projektu**
- **Infrastruktura i zasoby** zalecane dla projektów analizy danych
- **Narzędzia i programy** zalecane do wykonania projektu


## <a name="data-science-lifecycle"></a>Cykl życia analizy danych

Zespół ds. analizy danych (przetwarzania TDSP) zapewnia cykl życia do tworzenia projektów analizy danych. Cykl życia przedstawia pełne kroki, które są wykonywane przez pomyślne projekty.

Jeśli jest używany inny cykl życia analizy [danych, taki jak](https://wikipedia.org/wiki/Cross_Industry_Standard_Process_for_Data_Mining) [KDD,](https://wikipedia.org/wiki/Data_mining#Process) lub własny niestandardowy proces organizacji, można nadal używać przetwarzania TDSP opartego na zadaniach w kontekście tych cykli rozwoju. Na wysokim poziomie te różne metody mają wiele wspólnych metod. 

Ten cykl życia został zaprojektowany na potrzeby projektów analizy danych, które są dostarczane jako część inteligentnych aplikacji. Te aplikacje wdrażają Uczenie maszynowe lub sztuczne modele analiz do analizy predykcyjnej. Dzięki temu procesowi mogą korzystać projekty analizy danych w nauce lub projekty analiz Improvised. Jednak w takich przypadkach niektóre opisane czynności mogą nie być wymagane.    

Cykl życia przedstawia główne etapy, które są zwykle wykonywane przez projekty, często iteracyjnie:

* **Zrozumienie biznesowe**
* **Pozyskiwanie i zrozumienie danych**
* **Modelowanie**
* **Wdrożenie**

Poniżej przedstawiono wizualną reprezentację **cyklu życia zespołowego procesu nauki danych**. 

![Na diagramie przedstawiono cykl życia analizy danych, w tym zrozumienie biznesowe, pozyskiwanie i zrozumienie danych, modelowanie i wdrażanie.](./media/overview/tdsp-lifecycle2.png) 

Artefakty cele, zadania i dokumentacja dotyczące poszczególnych etapów cyklu życia w programie przetwarzania TDSP są opisane w temacie [cykl życia procesu nauki o danych zespołowych](lifecycle.md) . Te zadania i artefakty są skojarzone z rolami projektu:

- Architekt rozwiązań
- Menedżer projektu
- Inżynier danych
- Mistrz danych
- Deweloper aplikacji
- Lider projektu 

Na poniższym diagramie przedstawiono widok siatki zadań (niebieski) i artefakty (w kolorze zielonym) skojarzone z każdym etapem cyklu życia (na osi poziomej) dla tych ról (na osi pionowej). 

[![PRZETWARZANIA TDSP — role i zadania](./media/overview/tdsp-tasks-by-roles.png)](./media/overview/tdsp-tasks-by-roles.png#lightbox)

## <a name="standardized-project-structure"></a>Standardowa struktura projektu

Jeśli wszystkie projekty współdzielą strukturę katalogów i używają szablonów dokumentów projektu, ułatwiają członkom zespołu znalezienie informacji o ich projektach. Cały kod i dokumenty są przechowywane w systemie kontroli wersji (VCS), takim jak Git, TFS lub Subversion, aby umożliwić współpracę zespołową. Śledzenie zadań i funkcji w systemie monitorowania projektu Agile, takich jak JIRA, Rally i Azure DevOps, umożliwia bliższe śledzenie kodu dla poszczególnych funkcji. Takie śledzenie umożliwia również zespołom uzyskanie lepszych szacunków kosztów. PRZETWARZANIA TDSP zaleca utworzenie oddzielnego repozytorium dla każdego projektu w systemie VCS na potrzeby obsługi wersji, bezpieczeństwa informacji i współpracy. Struktura znormalizowana dla wszystkich projektów pomaga w tworzeniu instytucjonalnej wiedzy w całej organizacji.

Udostępniamy szablony dla struktury folderów i wymaganych dokumentów w standardowych lokalizacjach. Ta struktura folderów organizuje pliki, które zawierają kod służący do eksploracji danych i wyodrębniania funkcji oraz te iteracje modelu rekordu. Te szablony ułatwiają członkom zespołu zrozumienie pracy wykonywanej przez inne osoby i dodawanie nowych członków do zespołów. Można łatwo wyświetlać i aktualizować szablony dokumentów w formacie promocji. Użyj szablonów, aby podać listy kontrolne z kluczowymi pytaniami dla każdego projektu, aby upewnić się, że problem jest dobrze zdefiniowany i że dostarczane dane spełniają wymagania jakości. Przykłady:

- Karta projektu do udokumentowania problemu biznesowego i zakresu projektu
- Raporty danych umożliwiające udokumentowanie struktury i statystyk danych pierwotnych
- Modelowanie raportów w celu udokumentowania funkcji pochodnych
- modelowe metryki wydajności, takie jak krzywe ROC lub MSE


[![PRZETWARZANIA TDSP — katalogi](./media/overview/tdsp-dir-structure.png)](./media/overview/tdsp-dir-structure.png#lightbox)

Strukturę katalogów można sklonować z usługi [GitHub](https://github.com/Azure/Azure-TDSP-ProjectTemplate).

## <a name="infrastructure-and-resources-for-data-science-projects"></a>Infrastruktura i zasoby dla projektów analizy danych  

PRZETWARZANIA TDSP zawiera zalecenia dotyczące zarządzania udostępnioną infrastrukturą analizy i magazynu, na przykład:

- systemy plików w chmurze do przechowywania zestawów danych 
- bazy danych
- klastry Big Data (SQL lub Spark) 
- Usługa Machine Learning 

Infrastruktura analizy i magazynu, w której przechowywane są nieprzetworzone i przetworzone zestawy danych, może znajdować się w chmurze lub lokalnie. Ta infrastruktura umożliwia przepustą analizę. Pozwala również uniknąć duplikowania, co może prowadzić do niespójności i niepotrzebnych kosztów związanych z infrastrukturą. Dostępne są narzędzia do udostępniania udostępnionych zasobów, śledzenia ich i zezwalania poszczególnym członkom zespołu na bezpieczne łączenie z tymi zasobami. Dobrym praktyką jest również posiadanie przez członków projektu spójnego środowiska obliczeniowego. Różnych członków zespołu mogą następnie replikować i weryfikować eksperymenty.

Oto przykład zespołu pracującego nad wieloma projektami i udostępniając różne składniki infrastruktury analizy chmurowej.

[![PRZETWARZANIA TDSP — infrastruktura](./media/overview/tdsp-analytics-infra.png)](./media/overview/tdsp-analytics-infra.png#lightbox) 


## <a name="tools-and-utilities-for-project-execution"></a>Narzędzia i programy do wykonania projektu

Wprowadzenie procesów w większości organizacji jest trudne. Narzędzia udostępniane do wdrożenia procesu nauki o danych i cyklu życia obniżają bariery i zwiększają spójność ich wdrażania. PRZETWARZANIA TDSP zawiera początkowy zestaw narzędzi i skryptów umożliwiający szybkie rozpoczęcie wdrażania przetwarzania TDSP w zespole. Pomaga również zautomatyzować niektóre typowe zadania związane z analizą danych, takie jak Eksploracja danych i modelowanie linii bazowej. Istnieje dobrze zdefiniowana struktura, która umożliwia osobom Współtworzenie współużytkowanych narzędzi i narzędzi w repozytorium kodu udostępnionego zespołu. Te zasoby mogą być następnie wykorzystywane przez inne projekty w zespole lub w organizacji. PRZETWARZANIA TDSP również planuje, aby umożliwić wkładowanie narzędzi i narzędzi do całej społeczności. Narzędzia przetwarzania TDSP mogą być klonowane z usługi [GitHub](https://github.com/Azure/Azure-TDSP-Utilities).


## <a name="next-steps"></a>Następne kroki

[Proces nauki danych zespołu: role i zadania](https://github.com/Azure/Microsoft-TDSP/blob/master/Docs/roles-tasks.md) Przedstawia podstawowe role pracowników i powiązane z nimi zadania dla zespołu ds. analizy danych, które są standaryzacją tego procesu. 
