---
title: Przygotuj model do wdrożenia
titleSuffix: ML Studio (classic) - Azure
description: Jak przygotować przeszkolony model do wdrożenia jako usługę sieci Web, konwertując eksperyment Machine Learning Studio (klasyczny) do eksperymentu predykcyjnego.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.date: 03/28/2017
ms.openlocfilehash: 061c340f8c4952d5a0f2a3873f7475e4f733c290
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79204515"
---
# <a name="how-to-prepare-your-model-for-deployment-in-azure-machine-learning-studio-classic"></a>Jak przygotować model do wdrożenia w Azure Machine Learning Studio (klasyczny)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Azure Machine Learning Studio (klasyczny) oferuje narzędzia potrzebne do opracowania modelu analizy predykcyjnej, a następnie operacjonalizować go przez wdrożenie go jako usługi sieci Web platformy Azure.

W tym celu należy użyć programu Studio (klasycznego) do utworzenia eksperymentu o nazwie *szkolenia* , w którym można wyszkolić, oceny i edytować model. Gdy skończysz, uzyskasz gotowy do wdrożenia model, konwertując eksperyment szkoleniowy na *eksperyment predykcyjny* , który jest skonfigurowany do oceny danych użytkownika.

Przykładowy proces można zobaczyć w [samouczku 1: przewidywanie ryzyka kredytowego](tutorial-part1-credit-risk.md).

Ten artykuł zawiera szczegółowe informacje o tym, jak eksperyment szkoleniowy jest konwertowany na eksperyment predykcyjny i jak jest wdrażany eksperyment predykcyjny. Opisując te szczegóły, można dowiedzieć się, jak skonfigurować wdrożony model, aby zwiększyć jego efektywność.



## <a name="overview"></a>Omówienie 

Proces konwersji eksperymentu szkoleniowego na eksperyment predykcyjny obejmuje trzy kroki:

1. Zastąp moduły algorytmu uczenia maszynowego modelem szkolonym.
2. Przytnij eksperyment tylko do modułów, które są konieczne do oceny. Eksperyment szkoleniowy obejmuje wiele modułów, które są niezbędne do uczenia się, ale nie są potrzebne, gdy model jest szkolony.
3. Zdefiniuj, w jaki sposób model akceptuje dane od użytkownika usługi sieci Web i jakie dane zostaną zwrócone.

> [!TIP]
> W doświadczeniu szkoleniowym Jesteś w trakcie szkoleń i oceniamy model przy użyciu własnych danych. Po wdrożeniu użytkownicy będą wysyłać nowe dane do modelu i zwracają wyniki prognozowania. Dlatego podczas konwertowania eksperymentu szkoleniowego na eksperyment predykcyjny, aby przygotować go do wdrożenia, należy pamiętać, jak model będzie używany przez inne osoby.
> 
> 

## <a name="set-up-web-service-button"></a>Przycisk usługi sieci Web
Po uruchomieniu eksperymentu (kliknij pozycję **Uruchom** u dołu kanwy eksperymentu) kliknij przycisk **Konfiguruj usługę sieci Web** (wybierz opcję **predykcyjnej usługi sieci Web** ). **Konfiguracja usługi sieci Web** wykonuje trzy kroki konwertowania eksperymentu szkoleniowego na eksperyment predykcyjny:

1. Model ten jest zapisywany w sekcji **przeszkolonych modeli** w palecie modułów (po lewej stronie kanwy eksperymentu). Spowoduje to zamienienie algorytmu uczenia maszynowego i [przeszkolenie modułów modelu][train-model] z zapisanym przeszkolonym modelem.
2. Analizuje eksperyment i usuwa moduły, które były jasno używane tylko do szkolenia i nie są już potrzebne.
3. Wstawia moduły danych wejściowych i _wyjściowych_ _usługi sieci Web_ do domyślnych lokalizacji w Twoim eksperymentie (te moduły akceptują i zwracają dane użytkownika).

Na przykład następujące eksperymenty pociąga za pomocą dwuklasowego modelu drzewa decyzyjnego przy użyciu przykładowych danych spisu:

![Eksperyment szkoleniowy](./media/convert-training-experiment-to-scoring-experiment/figure1.png)

Moduły w tym eksperymentie wykonują zasadniczo cztery różne funkcje:

![Funkcje modułu](./media/convert-training-experiment-to-scoring-experiment/figure2.png)

Podczas konwertowania tego eksperymentu szkoleniowego na eksperyment predykcyjny niektóre z tych modułów nie są już potrzebne lub są teraz w innym celu:

* **Dane** — dane w tym przykładowym zestawie danych nie są używane podczas oceniania — użytkownik usługi sieci Web dostarczy dane do oceny. Jednak metadane z tego zestawu danych, takie jak typy danych, są używane przez szkolony model. Dlatego należy zachować zestaw danych w eksperymentie predykcyjnym, aby umożliwić mu dostarczenie tych metadanych.

* **Przygotowanie** — w zależności od danych użytkownika, które zostaną przesłane do oceniania, te moduły mogą lub nie muszą być wymagane do przetwarzania danych przychodzących. Przycisk **Konfiguruj usługę sieci Web** nie dotyka tych — musisz zdecydować, jak chcesz je obsłużyć.
  
    Na przykład, w tym przykładzie przykładowy zestaw danych może mieć brakujące wartości, więc dołączono do nich [nieoczyszczony moduł danych][clean-missing-data] . Ponadto przykładowy zestaw danych zawiera kolumny, które nie są konieczne do uczenia modelu. W związku z tym dodano [kolumny SELECT w module DataSet][select-columns] , aby wykluczyć te dodatkowe kolumny z przepływu danych. Jeśli wiesz, że dane, które zostaną przesłane na potrzeby oceniania za pomocą usługi sieci Web, nie mają brakujących wartości, możesz usunąć [nieczysty moduł danych][clean-missing-data] . Jednak ponieważ moduł [Wybierz kolumny w zestawie danych][select-columns] ułatwia definiowanie kolumn danych oczekiwanych przez szkolony model, ten moduł musi pozostać.

* **Uczenie** — te moduły są używane do uczenia modelu. Po kliknięciu przycisku **Konfiguruj usługę sieci Web**te moduły są zastępowane jednym modułem zawierającym przeszkolony model. Ten nowy moduł jest zapisywany w sekcji **przeszkolonych modeli** w palecie modułów.

* **Wynik** — w tym przykładzie moduł [Split Data][split] jest używany do dzielenia strumienia danych na dane testowe i dane szkoleniowe. W eksperymentie predykcyjnym nie jesteśmy już szkoleniami, więc można usunąć [dane podzielone][split] . Analogicznie, drugi moduł [modelu][score-model] oceny i moduł [oceny modelu][evaluate-model] są używane do porównywania wyników danych testowych, więc te moduły nie są konieczne w przypadku eksperymentu predykcyjnego. Moduł pozostały [model oceny][score-model] jest jednak wymagany do zwrócenia wyniku oceny przez usługę sieci Web.

Oto jak wygląda przykład po kliknięciu przycisku **Skonfiguruj usługę sieci Web**:

![Przekonwertowany eksperyment predykcyjny](./media/convert-training-experiment-to-scoring-experiment/figure3.png)

Działanie wykonywane przez **usługę sieci Web** może być wystarczające, aby przygotować eksperyment do wdrożenia jako usługę sieci Web. Jednak może być konieczne wykonanie pewnych dodatkowych czynności związanych z eksperymentem.

### <a name="adjust-input-and-output-modules"></a>Dostosowywanie modułów wejściowych i wyjściowych
W doświadczeniu szkoleniowym użyto zestawu danych szkoleniowych, a następnie przetworzysz dane w postaci, w której jest wymagany algorytm uczenia maszynowego. Jeśli dane, które mają zostać odebrane przez usługę sieci Web, nie będą potrzebne do tego przetwarzania, można je ominąć: Połącz dane wyjściowe **modułu danych wejściowych usługi sieci Web** z innym modułem w Twoim eksperymentie. Dane użytkownika zostaną teraz odebrane w modelu w tej lokalizacji.

Na przykład **Usługa sieci Web** domyślnie umieszcza moduł **wejściowy usługi sieci Web** w górnej części przepływu danych, jak pokazano na rysunku powyżej. Można jednak ręcznie ustawić dane **wejściowe usługi sieci Web** za pomocą modułów przetwarzania danych:

![Przeniesienie danych wejściowych usługi sieci Web](./media/convert-training-experiment-to-scoring-experiment/figure4.png)

Dane wejściowe dostarczane za pomocą usługi sieci Web będą teraz przekazywane bezpośrednio do modułu modelu wynikowego bez konieczności przetwarzania wstępnego.

Podobnie domyślnie **Konfiguracja usługi** sieci Web powoduje umieszczenie modułu wyjściowego usługi sieci Web u dołu przepływu danych. W tym przykładzie usługa sieci Web zwróci do użytkownika dane wyjściowe modułu [modelu wynikowego][score-model] , który zawiera kompletny wektor danych wejściowych i wyniki oceniania.
Jeśli jednak wolisz zwrócić coś innego, możesz dodać kolejne moduły przed modułem **wyjściowym usługi sieci Web** . 

Aby na przykład zwrócić tylko wyniki oceny, a nie cały wektor danych wejściowych, Dodaj do modułu DataSet ( [Wybieranie kolumn w zestawie danych][select-columns] ) wszystkie kolumny z wyjątkiem wyników oceniania. Następnie Przenieś moduł **wyjściowy usługi sieci Web** do danych wyjściowych modułu [SELECT Columns in DataSet][select-columns] . Eksperyment wygląda następująco:

![Przeniesienie danych wyjściowych usługi sieci Web](./media/convert-training-experiment-to-scoring-experiment/figure5.png)

### <a name="add-or-remove-additional-data-processing-modules"></a>Dodawanie lub usuwanie dodatkowych modułów przetwarzania danych
Jeśli eksperyment zawiera więcej modułów, które nie są potrzebne podczas oceniania, można je usunąć. Na przykład ze względu na to, że moduł danych **wejściowych usługi sieci Web** został przeniesiony do punktu po modułach przetwarzania danych, możemy usunąć [nieczysty moduł danych][clean-missing-data] z eksperymentu predykcyjnego.

Nasz eksperyment predykcyjny wygląda teraz następująco:

![Usuwanie dodatkowego modułu](./media/convert-training-experiment-to-scoring-experiment/figure6.png)


### <a name="add-optional-web-service-parameters"></a>Dodaj opcjonalne parametry usługi sieci Web
W niektórych przypadkach może być konieczne zezwolenie użytkownikowi usługi sieci Web na zmianę zachowania modułów podczas uzyskiwania dostępu do usługi. *Parametry usługi sieci Web* umożliwiają wykonanie tej czynności.

Typowym przykładem jest skonfigurowanie modułu [importowania danych][import-data] , dzięki czemu użytkownik wdrożonej usługi sieci Web może określić inne źródło danych podczas uzyskiwania dostępu do usługi sieci Web. Lub skonfiguruj moduł [eksportu danych][export-data] , aby można było określić inną lokalizację docelową.

Można definiować parametry usługi sieci Web i kojarzyć je z jednym lub wieloma parametrami modułu, a także określić, czy są one wymagane, czy opcjonalne. Użytkownik usługi sieci Web dostarcza wartości tych parametrów podczas uzyskiwania dostępu do usługi, a akcje modułu są odpowiednio modyfikowane.

Więcej informacji o parametrach usługi sieci Web i sposobach ich użycia można znaleźć w temacie [Using Azure Machine Learning Web Service Parameters][webserviceparameters].

[webserviceparameters]: web-service-parameters.md


## <a name="deploy-the-predictive-experiment-as-a-web-service"></a>Wdrożenie eksperymentu predykcyjnego jako usługi internetowej
Teraz, gdy eksperyment predykcyjny został wystarczająco przygotowany, możesz go wdrożyć jako usługę sieci Web platformy Azure. Korzystając z usługi sieci Web, użytkownicy mogą wysyłać dane do modelu, a model zwróci swoje przewidywania.

Aby uzyskać więcej informacji na temat kompletnego procesu wdrażania, zobacz [wdrażanie usługi sieci web Azure Machine Learning][deploy]

[deploy]: deploy-a-machine-learning-web-service.md

<!-- Module References -->
[clean-missing-data]: https://msdn.microsoft.com/library/azure/d2c5ca2f-7323-41a3-9b7e-da917c99f0c4/
[evaluate-model]: https://msdn.microsoft.com/library/azure/927d65ac-3b50-4694-9903-20f6c1672089/
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
[score-model]: https://msdn.microsoft.com/library/azure/401b4f92-e724-4d5a-be81-d5b0ff9bdb33/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
[train-model]: https://msdn.microsoft.com/library/azure/5cc7053e-aa30-450d-96c0-dae4be720977/
[export-data]: https://msdn.microsoft.com/library/azure/7a391181-b6a7-4ad4-b82d-e419c0d6522c/
