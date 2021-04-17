---
title: Możliwości interpretowania modelu w Azure Machine Learning (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak & sposób, w jaki model uczenia maszynowego tworzy przewidywania podczas trenowania & wnioskowania przy użyciu zestawu SDK Azure Machine Learning Python.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 02/25/2021
ms.openlocfilehash: c517cf2fc8491d62cf2379c87acd2eaadde8fe15
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576435"
---
# <a name="model-interpretability-in-azure-machine-learning-preview"></a>Możliwości interpretowania modelu w Azure Machine Learning (wersja zapoznawcza)


## <a name="model-interpretability-overview"></a>Omówienie możliwości interpretacji modelu

Interpretacja modelu ma kluczowe znaczenie dla naukowców danych, audytorów i osób decyzyjnych w firmie w celu zapewnienia zgodności z zasadami firmy, standardami branżowymi i przepisami rządowymi:

+ Naukowcy zajmujący się danymi muszą mieć możliwość wyjaśnienia swoich modeli kierownictwu i uczestnikom projektu, aby zrozumieć wartość i dokładność swoich ustaleń. Wymagają one również możliwości interpretacji w celu debugowania modeli i podejmowania świadomych decyzji dotyczących sposobu ich ulepszania. 

+ Audytorzy prawna wymagają narzędzi do weryfikowania modeli pod względem zgodności z przepisami i monitorowania wpływu decyzji modeli na ludzi. 

+ Decydenci biznesowi potrzebują pokoju, mając możliwość zapewnienia przejrzystości dla użytkowników końcowych. Dzięki temu mogą zdobyć i utrzymać zaufanie.

Umożliwienie wyjaśnienia modelu uczenia maszynowego jest ważne w dwóch głównych fazach opracowywania modelu:

+ W fazie trenowania projektanci modeli i ewaluatorzy mogą używać danych wyjściowych możliwości interpretacji modelu do weryfikowania hipotez i budowania zaufania z uczestnikami projektu. Używają one również szczegółowych informacji o modelu do debugowania, sprawdzania zachowania modelu do ich celów i sprawdzania pod uwagę nad naduchamialnością modelu lub jej cechami.

+ W fazie wnioskowania, ponieważ przejrzystość wdrożonych modeli umożliwia kierownictwu zrozumienie "po wdrożeniu" sposobu działania modelu oraz sposobu traktowania i wpływu jego decyzji na ludzi w rzeczywistości. 

## <a name="interpretability-with-azure-machine-learning"></a>Możliwości interpretowania za pomocą Azure Machine Learning

Klasy możliwości interpretacji modelu są udostępniane za pośrednictwem następującego pakietu zestawu SDK: (Dowiedz się, jak instalować pakiety [zestawu SDK](/python/api/overview/azure/ml/install)dla Azure Machine Learning )

* `azureml.interpret`Zawiera funkcje obsługiwane przez firmę Microsoft.

Do `pip install azureml-interpret` użytku ogólnego.

## <a name="how-to-interpret-your-model"></a>Interpretowanie modelu

Korzystając z klas i metod w zestawie SDK, można:
+ Wyjaśnienie przewidywania modelu przez wygenerowanie wartości ważności cech dla całego modelu i/lub poszczególnych punktów danych. 
+ Osiągnięcie możliwości interpretacji modelu w rzeczywistych zestawach danych na dużą skalę podczas trenowania i wnioskowania.
+ Używanie interaktywnego pulpitu nawigacyjnego wizualizacji do odnajdywania wzorców w danych i objaśnień w czasie trenowania

W uczeniu **maszynowym funkcje** to pola danych używane do przewidywania docelowego punktu danych. Aby na przykład przewidzieć ryzyko kredytowe, można użyć pól danych dotyczących wieku, rozmiaru konta i wieku konta. W tym przypadku funkcje to wiek, rozmiar konta i wiek **konta.** Ważność funkcji informuje o tym, jak każde pole danych wpłynęło na przewidywania modelu. Na przykład wiek może być intensywnie używany w prognozie, podczas gdy rozmiar konta i wiek nie wpływają znacząco na wartości przewidywania. Ten proces umożliwia badaczom danych wyjaśnienie wynikowych przewidywań, dzięki czemu uczestnicy projektu mają wgląd w najważniejsze funkcje w modelu.

## <a name="supported-interpretability-techniques"></a>Obsługiwane techniki interpretacji

 `azureml-interpret` korzysta z technik możliwości interpretowania opracowanych w społeczności [Interpret-Community](https://github.com/interpretml/interpret-community/), open source python do trenowania modeli z możliwością interpretacji i pomagania w wyjaśnianiu systemów AI czarnej skrzynki. [Interpret-Community](https://github.com/interpretml/interpret-community/) służy jako host dla objaśnień obsługiwanych przez ten zestaw SDK i obecnie obsługuje następujące techniki możliwości interpretacji:

|Interpretability Technique|Opis|Typ|
|--|--|--------------------|
|Objaśnienia drzewa SHAP| [Objaśnienia drzewa SHAP,](https://github.com/slundberg/shap)które koncentrują się na algorytmie szybkiego szacowania wartości SHAP czasu wielomianowego specyficznym dla drzew **i zespołów drzew.**|Specyficzne dla modelu|
|Objaśnienia głębokie SHAP| Na podstawie wyjaśnienia algorytmu SHAP, Deep Explainer "to algorytm przybliżenia o dużej szybkości dla wartości SHAP w modelach uczenia głębokiego, który jest oparty na połączeniu z algorytmem Deep DEEP opisanym w dokumencie [SHAP NIPS](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). **Obsługiwane są modele TensorFlow** i **keras** korzystające z zaplecza TensorFlow (jest również wstępna obsługa PyTorch)".|Specyficzne dla modelu|
|Objaśnienia liniowe SHAP| Objaśnienia liniowe SHAP oblicza wartości SHAP dla modelu **liniowego,** opcjonalnie zlicza korelacje między cechami.|Specyficzne dla modelu|
|Objaśnienia jądra SHAP| Objaśnienia jądra SHAP wykorzystują lokalną regresję liniową ze specjalnymi wagami, aby oszacować wartości SHAP **dla dowolnego modelu.**|Niezależne od modelu|
|Mimic Explainer (Globalny surogat)| Objaśnienie mimiczne opiera się na koncepcji trenowania globalnych modeli zastępczych w [celu](https://christophm.github.io/interpretable-ml-book/global.html) naśladowania modeli czarnej skrzynki. Globalny model zastępczy jest wewnętrznie interpretowalnym modelem, który jest  trenowany w celu jak najdokładnego przybliżenie przewidywań dowolnego modelu czarnej skrzynki. Badacze danych mogą interpretować model zastępczy, aby wyciągać wnioski dotyczące modelu czarnej skrzynki. Jako modelu zastępczego można użyć jednego z następujących modeli zinterpretowalnych: LightGBM (LGBMExplainableModel), Linear Regression (LinearExplainableModel), Stochastic Gradient Descent explainable model (SGDExplainableModel) i Decision Tree (DecisionTreeExplainableModel).|Niezależne od modelu|
|Objaśnianie ważności funkcji permutacji (PFI)| Znaczenie funkcji permutacji to technika używana do wyjaśniania modeli klasyfikacji i regresji, która jest inspirowana publikacją Random Forests (lasy [losowe) Breimana](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (patrz sekcja 10). Na wysokim poziomie sposób jej działania to losowe bojazy danych po jednej funkcji na raz dla całego zestawu danych i obliczanie, o ile zmienia się metryka wydajności zainteresowania. Im większa zmiana, tym ważniejsza jest ta funkcja. Pfi może wyjaśnić ogólne zachowanie **dowolnego bazowego modelu,** ale nie wyjaśnia poszczególnych przewidywań. |Niezależne od modelu|

Oprócz opisanych powyżej technik interpretacji obsługujemy inny objaśnienia oparte na technologii SHAP, o nazwie `TabularExplainer` . W zależności od modelu program `TabularExplainer` używa jednego z obsługiwanych objaśnienia SHAP:

* TreeExplainer dla wszystkich modeli opartych na drzewie
* DeepExplainer dla modeli sieci DNN
* LinearExplainer dla modeli liniowych
* KernelExplainer dla wszystkich innych modeli

`TabularExplainer` Wprowadzono również znaczące ulepszenia funkcji i wydajności w bezpośrednich objaśnieniach SHAP:

* **Podsumowanie zestawu danych inicjowania**. W przypadkach, gdy szybkość wyjaśnienia jest najważniejsza, podsumowujemy zestaw danych inicjalizacji i generujemy mały zestaw reprezentatywnych próbek, co przyspiesza generowanie ogólnych i poszczególnych wartości ważności cech.
* **Próbkowanie zestawu danych oceny**. Jeśli użytkownik przekazuje duży zestaw próbek do oceny, ale w rzeczywistości nie wymaga oceny wszystkich z nich, parametr próbkowania można ustawić na wartość true, aby przyspieszyć obliczanie ogólnych wyjaśnień modelu.

Na poniższym diagramie przedstawiono bieżącą strukturę obsługiwanych objaśnienia.

[![Machine Learning Interpretability Architecture](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>Obsługiwane modele uczenia maszynowego

Pakiet `azureml.interpret` zestawu SDK obsługuje modele wytrenowane przy użyciu następujących formatów zestawu danych:
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

Funkcje wyjaśnienia akceptują zarówno modele, jak i potoki jako dane wejściowe. Jeśli podano model, musi on implementować funkcję przewidywania lub zgodną z `predict` `predict_proba` konwencją Scikit. Jeśli model nie obsługuje tej funkcji, możesz opakuć model w funkcję, która generuje ten sam wynik co w programie Scikit i użyć tej funkcji otoki z wybranym `predict` `predict_proba` objaśnieniami. Jeśli podano potok, funkcja wyjaśnienia zakłada, że uruchomiony skrypt potoku zwraca przewidywanie. Ta technika opakowywania może obsługiwać modele wytrenowane za pośrednictwem platform uczenia głębokiego `azureml.interpret` PyTorch, TensorFlow i Keras, a także klasyczne modele uczenia maszynowego.

## <a name="local-and-remote-compute-target"></a>Lokalny i zdalny docelowy obiekt obliczeniowy

Pakiet jest przeznaczony do pracy z lokalnymi i `azureml.interpret` zdalnymi celami obliczeniowymi. W przypadku uruchamiania lokalnego funkcje zestawu SDK nie będą kontaktować się z żadnymi usługami platformy Azure. 

Wyjaśnienie można uruchomić zdalnie w usłudze Azure Machine Learning Compute i zalogować informacje objaśnienia w usłudze Azure Machine Learning Run History Service. Po zalogowaniu tych informacji raporty i wizualizacje z wyjaśnienia są łatwo dostępne Azure Machine Learning studio analizy użytkownika.


## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z [how-to for](how-to-machine-learning-interpretability-aml.md) enabling interpretability for models training both locally and on Azure Machine Learning compute resources (Jak w przypadku modeli trenować lokalnie i na Azure Machine Learning zasobów obliczeniowych). 
- Dowiedz się, jak [włączyć możliwość interpretacji dla modeli zautomatyzowanego uczenia maszynowego.](how-to-machine-learning-interpretability-automl.md)
- Zobacz [przykładowe notesy, aby](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) uzyskać dodatkowe scenariusze. 
- Jeśli interesuje Cię możliwości interpretowania scenariuszy tekstowych, zobacz [Interpret-text](https://github.com/interpretml/interpret-text), a related open source to [Interpret-Community](https://github.com/interpretml/interpret-community/), for interpretability techniques for NLP (Interpretowanie tekstu , powiązane z open source społeczności interpretacyjnych), aby uzyskać informacje na temat technik interpretacji dla technologii NLP. `azureml.interpret`Pakiet nie obsługuje obecnie tych technik, ale możesz rozpocząć pracę z [przykładowym notesem na temat klasyfikacji tekstu.](https://github.com/interpretml/interpret-text/blob/master/notebooks/text_classification/text_classification_classical_text_explainer.ipynb)