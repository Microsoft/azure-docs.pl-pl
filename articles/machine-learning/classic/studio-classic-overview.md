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
ms.openlocfilehash: 24b7679c92b8f69b9406677ebe6355c0e1e51f55
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91349135"
---
# <a name="what-can-i-do-with-machine-learning-studio-classic"></a>Co mogę zrobić z Machine Learning Studio (klasyczny)?

**dotyczy:** ![ tak ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) ![ nie](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../compare-azure-ml-to-studio-classic.md)  

[!INCLUDE [Designer notice](../../../includes/designer-notice.md)]

Machine Learning Studio (klasyczny) to narzędzie typu "przeciągnij i upuść", które służy do kompilowania, testowania i wdrażania modeli uczenia maszynowego. Studio (klasyczny) publikuje modele jako usługi sieci Web, które mogą być łatwo wykorzystane przez aplikacje niestandardowe lub narzędzia analizy biznesowej, takie jak program Excel.


## <a name="studio-classic--interactive-workspace"></a>Studio (klasyczny) interaktywny obszar roboczy

Aby opracować model analizy predykcyjnej, zazwyczaj używane są dane z jednego lub większej liczby źródeł, przekształcania i analizowania tych danych za pośrednictwem różnych funkcji manipulowania danymi i funkcje statystyczne oraz generowanie zestawu wyników. Tworzenie modelu w ten sposób jest procesem iteracyjnym. Podczas modyfikowania różnych funkcji i ich parametrów wyniki stają się zbieżne, aż do uzyskania wytrenowanego, skutecznego modelu.

Machine Learning Studio (klasyczny) oferuje interaktywny, wizualny obszar roboczy, który umożliwia łatwe kompilowanie, testowanie i iterację modelu analizy predykcyjnej. Możesz przeciągać i upuszczać ***zestawy danych*** i ***moduły*** analizy na interaktywną kanwę, łącząc je ze sobą w celu utworzenia ***eksperymentu***, który jest uruchamiany w Machine Learning Studio (klasyczny). W celu wykonania iteracji projektu modelu należy przeprowadzić edycję eksperymentu, zapisując kopię w razie potrzeby, a następnie uruchomić eksperyment ponownie. Gdy nadejdzie odpowiedni moment, można przekształcić ***eksperyment uczenia*** w ***eksperyment predykcyjny***, a następnie opublikować go jako ***usługę sieci Web***, dzięki czemu model będzie dostępny dla innych osób.

Nie jest wymagane programowanie, wizualne łączenie zestawów danych i modułów w celu utworzenia modelu analizy predykcyjnej.

![Diagram Machine Learning Studio (klasyczny): Tworzenie eksperymentów, odczytywanie danych z wielu źródeł, pisanie danych oceny i pisanie modeli.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-ml-studio-classic-overview-diagram"></a>Pobierz diagram omówienia ML Studio (klasyczny)
Pobierz diagram **omówienia możliwości programu Microsoft ml Studio (klasyczny)** i uzyskaj ogólny widok możliwości Machine Learning Studio (klasyczny). Aby mieć ten diagram zawsze w pobliżu, wydrukuj go w rozmiarze tabloidu (11 cali x 17 cali).

**Pobierz diagram tutaj: [Przegląd](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf) ** 
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
[convert-to-arff]: https://msdn.microsoft.com/library/azure/62d2cece-d832-4a7a-a0bd-f01f03af0960/
[elementary-statistics]: https://msdn.microsoft.com/library/azure/3086b8d4-c895-45ba-8aa9-34f0c944d4d3/
[linear-regression]: https://msdn.microsoft.com/library/azure/31960a6f-789b-4cf7-88d6-2e1152c0bd1a/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
