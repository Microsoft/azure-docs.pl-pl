---
title: Co mogę zrobić z Machine Learning Studio (klasyczny) — Azure
description: Machine Learning Studio (klasyczny) to narzędzie do przeciągania i upuszczania umożliwiające szybkie tworzenie modeli z biblioteki gotowych do użycia algorytmów i modułów.
services: machine-learning
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio
ms.topic: overview
ms.date: 08/19/2020
ms.openlocfilehash: 1aadf1fe59c5300a4f16ea96b1e1d7a5fbcbdd6d
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93325735"
---
# <a name="what-can-i-do-with-machine-learning-studio-classic"></a>Co mogę zrobić z Machine Learning Studio (klasyczny)?

**dotyczy:** ![ tak ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) ![ nie ](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Machine Learning Studio (klasyczny) to narzędzie typu "przeciągnij i upuść", które służy do kompilowania, testowania i wdrażania modeli uczenia maszynowego. Studio (klasyczny) publikuje modele jako usługi sieci Web, które mogą być łatwo wykorzystane przez aplikacje niestandardowe lub narzędzia analizy biznesowej, takie jak program Excel.


## <a name="studio-classic--interactive-workspace"></a>Studio (klasyczny) interaktywny obszar roboczy

Aby opracować model analizy predykcyjnej, zazwyczaj używane są dane z jednego lub większej liczby źródeł, przekształcania i analizowania tych danych za pośrednictwem różnych funkcji manipulowania danymi i funkcje statystyczne oraz generowanie zestawu wyników. Tworzenie modelu w ten sposób jest procesem iteracyjnym. Podczas modyfikowania różnych funkcji i ich parametrów wyniki stają się zbieżne, aż do uzyskania wytrenowanego, skutecznego modelu.

Machine Learning Studio (klasyczny) oferuje interaktywny, wizualny obszar roboczy, który umożliwia łatwe kompilowanie, testowanie i iterację modelu analizy predykcyjnej. Przeciąganie i upuszczanie obiektów * *_zestawów danych_* _ i _*_analiz na_*_ interaktywną kanwę, łącząc je ze sobą w celu utworzenia _*_eksperymentu_*_ , który jest uruchamiany w Machine Learning Studio (klasyczny). W celu wykonania iteracji projektu modelu należy przeprowadzić edycję eksperymentu, zapisując kopię w razie potrzeby, a następnie uruchomić eksperyment ponownie. Gdy wszystko będzie gotowe, możesz przekonwertować _*_eksperyment szkoleniowy_*_ na _*_eksperyment predykcyjny_*_ , a następnie opublikować go jako _*_usługę sieci Web_*_ , dzięki czemu model będzie dostępny dla innych osób.

Nie jest wymagane programowanie, wizualne łączenie zestawów danych i modułów w celu utworzenia modelu analizy predykcyjnej.

![Diagram Machine Learning Studio (klasyczny): Tworzenie eksperymentów, odczytywanie danych z wielu źródeł, pisanie danych oceny i pisanie modeli.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-ml-studio-classic-overview-diagram"></a>Pobierz diagram omówienia ML Studio (klasyczny)
Pobierz program _ *Microsoft ml Studio (klasyczny) przegląd funkcji* * diagram i uzyskaj ogólny widok możliwości Machine Learning Studio (klasyczny). Aby mieć ten diagram zawsze w pobliżu, wydrukuj go w rozmiarze tabloidu (11 cali x 17 cali).

**Pobierz diagram tutaj: [Przegląd](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)** 
 ![ możliwości programu Microsoft Machine Learning Studio (klasyczny) Przegląd możliwości programu Microsoft Machine Learning Studio (klasyczny)](./media/what-is-ml-studio/ml_studio_overview_v1.1.png)


## <a name="components-of-a-studio-classic--experiment"></a>Składniki eksperymentu programu Studio (klasycznego)
Eksperyment składa się z zestawów danych, które dostarczają dane do modułów analitycznych łączonych w celu utworzenia modelu analizy predykcyjnej. Prawidłowy eksperyment ma następujące cechy:

* Obejmuje co najmniej jeden zestaw danych i jeden moduł
* Zestawy danych mogą być połączone tylko z modułami
* Moduły mogą być połączone z zestawami danych lub innymi modułami
* Wszystkie porty wejściowe modułów muszą mieć jakieś połączenie z przepływem danych
* Konieczne jest ustawienie wszystkich wymaganych parametrów dla każdego modułu

Eksperymenty można tworzyć od podstaw albo przy użyciu przykładowego eksperymentu jako szablonu. Aby uzyskać więcej informacji, zobacz [Kopiowanie przykładowych eksperymentów w celu tworzenia nowych eksperymentów uczenia maszynowego](sample-experiments.md).

Aby zapoznać się z przykładem tworzenia eksperymentu, zobacz [Tworzenie eksperymentu w Machine Learning Studio (klasyczny)](create-experiment.md).

Aby uzyskać pełniejszy Przewodnik tworzenia rozwiązania do analizy predykcyjnej, zobacz Tworzenie [rozwiązania predykcyjnego za pomocą Machine Learning Studio (klasyczny)](tutorial-part1-credit-risk.md).

### <a name="datasets"></a>Zestawy danych
Zestaw danych to dane przekazane do Machine Learning Studio (klasyczne), dzięki czemu mogą być używane w procesie modelowania. Wiele przykładowych zestawów danych jest dołączonych do Machine Learning Studio (klasyczny), dzięki którym można eksperymentować z programem, a w miarę potrzeb można przekazać więcej zestawów danych. Oto przykładowe zestawy danych dostępne w Studio:

* **MPG data for various automobiles** (Dane MPG dotyczące różnych samochodów) — wartości przebiegu w milach na galon paliwa (MPG) dla samochodów zidentyfikowanych na podstawie liczby cylindrów, mocy itp.
* **Breast cancer data** (Dane dot. raka piersi) — dane z diagnoz raka piersi.
* **Forest fires data** (Dane dot. pożarów lasów) — rozmiary pożarów lasów w północno-wschodniej Portugalii.

Podczas tworzenia eksperymentu możesz wybrać z listy zestawów danych dostępnych po lewej stronie kanwy.

Aby zapoznać się z listą przykładowych zestawów danych zawartych w Machine Learning Studio (klasyczny), zobacz [Korzystanie z przykładowych zestawów dane w Machine Learning Studio (klasyczne)](use-sample-datasets.md).

### <a name="modules"></a>Moduły
Moduł jest algorytmem, który można wykonać na danych. Machine Learning Studio (klasyczny) zawiera szereg modułów z przedziału od funkcji transferu danych przychodzących na procesy szkoleniowe, oceniające i walidacji. Oto przykładowe dołączone moduły:

* [Convert to ARFF][convert-to-arff] (Konwertowanie na ARFF) — konwertuje serializowany zestaw danych .NET na plik formatu Attribute-Relation File Format (ARFF).
* [Compute Elementary Statistics][elementary-statistics] (Obliczanie statystyk podstawowych) — oblicza podstawowe statystyki, takie jak średnia, odchylenie standardowe itp.
* [Linear Regression][linear-regression] (Regresja liniowa) — tworzy model regresji liniowej online na podstawie spadku gradientu.
* [Score Model][score-model] (Ocena modelu) — ocenia nauczony model klasyfikacji lub regresji.

Podczas tworzenia eksperymentu możesz wybrać z listy modułów dostępnych po lewej stronie kanwy.

Moduł może zawierać zestaw parametrów, za pomocą których można konfigurować wewnętrzne algorytmy modułu. Po wybraniu modułu na kanwie parametry tego modułu są wyświetlane w okienku **Properties** (Właściwości) po prawej stronie kanwy. Te parametry można modyfikować w okienku, aby dostosowywać model.

Aby uzyskać pomoc dotyczącą przechodzenia przez dużą bibliotekę dostępnych algorytmów uczenia maszynowego, zobacz [jak wybrać algorytmy dla Microsoft Machine Learning Studio (klasyczny)](../how-to-select-algorithms.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Wdrażanie usługi sieci Web analizy predykcyjnej
Gdy model analizy predykcyjnej jest gotowy, możesz go wdrożyć jako usługę sieci Web bezpośrednio z poziomu Machine Learning Studio (klasyczne). Aby uzyskać więcej informacji na temat tego procesu, zobacz [wdrażanie usługi sieci web Azure Machine Learning](deploy-a-machine-learning-web-service.md).

## <a name="next-steps"></a>Następne kroki
Podstaw analizy predykcyjnej i uczenia maszynowego możesz się nauczyć, korzystając z [przewodnika Szybki start krok po kroku](create-experiment.md), a także [rozwijając przykłady](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: /azure/machine-learning/studio-module-reference/convert-to-arff
[elementary-statistics]: /azure/machine-learning/studio-module-reference/compute-elementary-statistics
[linear-regression]: /azure/machine-learning/studio-module-reference/linear-regression
[score-model]: /azure/machine-learning/studio-module-reference/score-model