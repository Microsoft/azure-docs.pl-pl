---
title: Interpretowanie modeli w Azure Machine Learning (wersja zapoznawcza)
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak zrozumieć, & wyjaśnić, jak model uczenia maszynowego wykonuje przewidywania podczas szkolenia & inferencing przy użyciu zestawu SDK języka Python Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, responsible-ml
ms.author: mithigpe
author: minthigpen
ms.reviewer: Luis.Quintanilla
ms.date: 02/25/2021
ms.openlocfilehash: 44ccf6b6d2459b87040fcac7d9cdcd336cc7b82f
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102522040"
---
# <a name="model-interpretability-in-azure-machine-learning-preview"></a>Interpretowanie modeli w Azure Machine Learning (wersja zapoznawcza)


## <a name="model-interpretability-overview"></a>Przegląd interpretacji modelu

Interpretacja modeli jest niezwykle ważna dla analityków danych, audytorów i osób podejmujących decyzje biznesowe, które są podobne, aby zapewnić zgodność z zasadami firmy, standardami branżowymi i przepisami rządowymi:

+ Osoby odpowiedzialne za dane muszą wyjaśnić swoje modele do kierownictwa i udziałowców, aby mogli zrozumieć wartość i dokładność ich wyników. Wymagają one również interpretacji do debugowania modeli i podejmowania świadomych decyzji o sposobie ich udoskonalania. 

+ Biegli rewidenci wymagają narzędzi do weryfikowania modeli w odniesieniu do zgodności z przepisami i monitorowania sposobu, w jaki te decyzje mają wpływ na ludzi. 

+ Twórcy zajmujący się decyzjami biznesowymi muszą mieć możliwość zapewnienia przejrzystości użytkownikom końcowym. Pozwala to na otrzymywanie i zachowanie zaufania.

Umożliwienie wyjaśnienia modelu uczenia maszynowego jest ważne w przypadku dwóch głównych etapów tworzenia modeli:

+ W fazie uczenia się projektanci modeli i oceniający mogą wykorzystać dane wyjściowe z możliwością interpretacji modelu do zweryfikowania pożądanych elementów i utworzenia relacji zaufania z uczestnikami projektu. Wykorzystują one również wgląd w model do debugowania, sprawdzanie poprawności zachowania modelu dopasowuje się do ich celów i sprawdza, czy istnieją niesprawiedliwe funkcje modelu.

+ W fazie inferencing, tak jakby miał przezroczystość w porównaniu ze wdrożonymi modelami, pozwala kierownictwu zrozumieć "po wdrożeniu", jak działa model i jakie decyzje są traktowane i wpływają na użytkowników w czasie rzeczywistym. 

## <a name="interpretability-with-azure-machine-learning"></a>Interpretowanie przy użyciu Azure Machine Learning

Klasy z możliwością interpretacji modelu są udostępniane za pomocą następującego pakietu SDK: (Dowiedz się, jak [zainstalować pakiety SDK dla Azure Machine Learning](/python/api/overview/azure/ml/install))

* `azureml.interpret`Program zawiera funkcje obsługiwane przez firmę Microsoft.

Użyj `pip install azureml-interpret` do ogólnego użytku.

## <a name="how-to-interpret-your-model"></a>Jak interpretować model

Korzystając z klas i metod w zestawie SDK, można:
+ Wyjaśnij prognozowanie modelu, generując wartości ważności funkcji dla całego modelu i/lub poszczególnych punktów. 
+ Zapewnij spójność modelu w rzeczywistych zestawach danych na dużą skalę podczas szkoleń i wnioskowania.
+ Korzystanie z interaktywnego pulpitu nawigacyjnego wizualizacji w celu odnajdywania wzorców danych i wyjaśnień w czasie uczenia

W uczeniu maszynowym **funkcje** są polami danych używanymi do przewidywania docelowego punktu danych. Na przykład w celu przewidywania ryzyka kredytowego mogą być używane pola danych dla wieku, rozmiaru konta i wieku konta. W tym przypadku wiek, rozmiar konta i wiek konta są **funkcjami**. Ważność funkcji informuje o tym, jak każde pole danych ma wpływ na przewidywania modelu. Na przykład wiek może być wielokrotnie używany w prognozie, podczas gdy rozmiar konta i wiek nie mają znaczącego wpływu na wartości prognoz. Ten proces umożliwia analitykom danych wyjaśnienie powstających prognoz, dzięki czemu zainteresowane strony mają wgląd w to, jakie funkcje są najważniejsze w modelu.

## <a name="supported-interpretability-techniques"></a>Obsługiwane techniki interpretacji

 `azureml-interpret` używa technik interpretacji opracowanych w ramach [interpretacji Community](https://github.com/interpretml/interpret-community/), pakietu języka Python Open Source na potrzeby uczenia modeli, które mogą być interpretowane i pomagają w wyjaśnieniu systemów Blackbox AI. [Interpretuj — społeczność](https://github.com/interpretml/interpret-community/) służy jako host dla obsługiwanych przez niego OBJAŚNIEŃ zestawu SDK i obecnie obsługuje następujące techniki interpretowania:

|Metoda interpretacji|Opis|Typ|
|--|--|--------------------|
|Objaśnienie drzewa kształtu| Klasyfikator drzewa [kształtu](https://github.com/slundberg/shap), który koncentruje się na algorytmie oceny wartości szybkiego kształtu czasu wielomianu, który jest specyficzny dla **drzew i ich kompletów drzew**.|Specyficzne dla modelu|
|Szczegółowy opis kształtu| W oparciu o objaśnienie z kształtu, szczegółowy opis "jest algorytmem zbliżeniowym o dużej szybkości, dla wartości kształtu w modelach uczenia głębokiego, które kompilują się w połączeniu z DeepLIFT opisanym w [dokumencie kształt NIPS](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). Obsługiwane są modele **TensorFlow** i modele **Keras** korzystające z zaplecza TensorFlow (istnieje również wstępna pomoc techniczna dla PyTorch) ".|Specyficzne dla modelu|
|Objaśnienie liniowe kształtu| Funkcja wyjaśniająca liniowy kształtu oblicza wartości kształtu dla **modelu liniowego**, opcjonalnie rozliczanie korelacji między funkcjami.|Specyficzne dla modelu|
|Wyjaśnienie jądra kształtu| Wyjaśnienie jądra kształtu używa specjalnie ważonej lokalnej regresji liniowej do oszacowania wartości kształtu dla **dowolnego modelu**.|Model — niezależny od|
|Objaśnienie śladowe (globalny Surogat)| Objaśnienie śladów opiera się na pomysłie na [globalne modele wieloskładnikowe](https://christophm.github.io/interpretable-ml-book/global.html) do naśladowania modeli blackbox. Globalny model zastępczy jest modelem, który jest interpretowany wewnętrznie, aby przybliżyć prognozy **dowolnego czarnego modelu** , jak to możliwe. Naukowcy danych mogą interpretować model zastępczy, aby rysować wnioski o modelu czarnego pudełka. Można użyć jednego z następujących modeli interpretowanych jako model zastępczy: LightGBM (LGBMExplainableModel), regresja liniowa (LinearExplainableModel), Gradient stochastycznego, który jest bardziej wyjaśniony model (SGDExplainableModel) i drzewo decyzyjne (DecisionTreeExplainableModel).|Model — niezależny od|
|Objaśnienie ważności funkcji permutacji (PFI)| Ważność funkcji permutacji jest techniką używaną do wyjaśnienia modeli klasyfikacji i regresji, które są [sponsorowane przez papier Breimanych lasów](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (patrz sekcja 10). Na wysokim poziomie, w jaki działa, jest to spowodowane losowo Shuffling danych jedną funkcją w danym czasie dla całego zestawu danych i obliczaniem, ile jest Metryka wydajności. Im większa zmiana, tym bardziej ważna jest funkcja. PFI może wyjaśnić ogólne zachowanie **dowolnego modelu bazowego** , ale nie wyjaśnia poszczególnych prognoz. |Model — niezależny od|

Oprócz opisanych powyżej technik interpretacji obsługujemy inne objaśnienie oparte na KSZTAŁTach, zwane `TabularExplainer` . W zależności od modelu program `TabularExplainer` używa jednego z obsługiwanych objaśnień kształtu:

* TreeExplainer dla wszystkich modeli opartych na drzewie
* DeepExplainer dla modeli DNN
* LinearExplainer dla modeli liniowych
* KernelExplainer dla wszystkich innych modeli

`TabularExplainer` Ponadto wprowadzono znaczące ulepszenia funkcji i wydajności w odniesieniu do wyjaśnień bezpośredniego kształtu:

* **Podsumowanie zestawu danych inicjalizacji**. W przypadkach, gdy najważniejsza jest szybkość wyjaśnienia, podsumowujemy zestaw danych inicjalizacji i generuje mały zestaw reprezentatywnych próbek, co przyspiesza generowanie ogólnych i indywidualnych wartości ważności funkcji.
* **Próbkowanie zestawu danych oceny**. Jeśli użytkownik przejdzie do dużego zestawu próbek ewaluacyjnych, ale nie potrzebuje wszystkich ich do oceny, parametr próbkowania może być ustawiony na wartość true, aby przyspieszyć Obliczanie ogólnych wyjaśnień modelu.

Na poniższym diagramie przedstawiono bieżącą strukturę obsługiwanych objaśnień.

[![Architektura Machine Learningj interpretacji](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>Obsługiwane modele uczenia maszynowego

`azureml.interpret`Pakiet zestawu SDK obsługuje modele przeszkolone przy użyciu następujących formatów zestawu danych:
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

Funkcje wyjaśniające akceptują zarówno modele, jak i potoki jako dane wejściowe. Jeśli jest dostarczany model, model musi implementować funkcję przewidywania `predict` lub `predict_proba` , która jest zgodna z Konwencją Scikit. Jeśli model nie obsługuje tego, możesz otoczyć model funkcją, która generuje taki sam wynik jak `predict` lub `predict_proba` w Scikit, i Użyj tej funkcji otoki z wybranym objaśnieniem. Jeśli podano potok, funkcja wyjaśnienie zakłada, że skrypt uruchomionego potoku zwraca prognozę. Dzięki tej metodzie zawijania `azureml.interpret` można obsługiwać modele przeszkolone przez PyTorch, TensorFlow i Kerase środowiska uczenia głębokiego, a także klasyczne modele uczenia maszynowego.

## <a name="local-and-remote-compute-target"></a>Lokalne i zdalne miejsce docelowe obliczeń

`azureml.interpret`Pakiet jest przeznaczony do pracy z lokalnymi i zdalnymi obiektami docelowymi obliczeniowymi. W przypadku uruchomienia lokalnego funkcje zestawu SDK nie będą kontaktować się z żadnymi usługami platformy Azure. 

Wyjaśnienie można uruchomić zdalnie na Azure Machine Learning COMPUTE i zalogować informacje o wyjaśnieniu do usługi historii Azure Machine Learning uruchomienia. Po zarejestrowaniu tych informacji raporty i wizualizacje z wyjaśnienia są łatwo dostępne w programie Azure Machine Learning Studio na potrzeby analizy użytkowników.


## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z artykułem jak włączyć funkcję interpretacji dla modeli — zarówno lokalnie, [jak](how-to-machine-learning-interpretability-aml.md) i na Azure Machine Learning zdalnych zasobów obliczeniowych. 
- Zobacz [przykładowe notesy](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) , aby uzyskać dodatkowe scenariusze. 
- Jeśli interesuje Cię interpretowanie scenariuszy tekstowych, zobacz [Interpretuj-Text](https://github.com/interpretml/interpret-text), powiązane repozytorium typu "open source" w celu [interpretacji społeczności](https://github.com/interpretml/interpret-community/)dla technik interpretowania dla NLP. `azureml.interpret` pakiet nie obsługuje obecnie tych technik, ale można rozpocząć pracę z [przykładowym notesem na klasyfikacji tekstu](https://github.com/interpretml/interpret-text/blob/master/notebooks/text_classification/text_classification_classical_text_explainer.ipynb).