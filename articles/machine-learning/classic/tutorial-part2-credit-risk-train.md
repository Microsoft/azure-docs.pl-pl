---
title: 'ML Studio (klasyczny) — samouczek 2: uczenie modeli ryzyka kredytowego — Azure'
description: Szczegółowy Samouczek przedstawiający sposób tworzenia rozwiązania do analizy predykcyjnej w celu oceny ryzyka kredytowego w Azure Machine Learning Studio (klasyczny). Ten samouczek jest drugą częścią trzyczęściowej serii samouczków. Przedstawia on trenowanie i ewaluację modeli.
keywords: ryzyko kredytowe, rozwiązanie analizy predykcyjnej, ocena ryzyka
author: sdgilley
ms.author: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: tutorial
ms.date: 02/11/2019
ms.openlocfilehash: 677c5b791f475468fbcf15c81fbabdcdbb1972de
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517454"
---
# <a name="tutorial-2-train-credit-risk-models---azure-machine-learning-studio-classic"></a>Samouczek 2: uczenie modeli ryzyka kredytowego — Azure Machine Learning Studio (klasyczny)

**dotyczy:** ![ Jest to znacznik wyboru, co oznacza, że ten artykuł ma zastosowanie do Machine Learning Studio (klasyczne).  ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny)   ![ to jest X, co oznacza, że ten artykuł ma zastosowanie do Azure Machine Learning](../../../includes/media/aml-applies-to-skus/no.png)[ . Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

W tym samouczku szczegółowo przedstawiono proces opracowywania rozwiązania analizy predykcyjnej. Tworzysz prosty model w Machine Learning Studio (klasyczny).  Następnie wdrożysz model jako usługę internetową w ramach usługi Azure Machine Learning.  Wdrożony model może tworzyć przewidywania przy użyciu nowych danych. Ten samouczek jest **drugą częścią serii samouczków z trzema częściami**.

Załóżmy, że chcesz przewidzieć ryzyko kredytowe osoby na podstawie informacji przekazanych we wniosku kredytowym.  

Ocena ryzyka kredytowego to złożony problem, ale w tym samouczku zostanie on nieco uproszczony. Będziesz jej używać jako przykładu tworzenia rozwiązania do analizy predykcyjnej przy użyciu Microsoft Azure Machine Learning Studio (klasyczne). Dla tego rozwiązania będziesz używać Azure Machine Learning Studio (klasyczny) i Machine Learning usługi sieci Web.  

W tym trzyczęściowym samouczku zaczniesz od publicznie dostępnych danych ryzyka kredytowego.  Następnie wdrożysz i wytrenujesz model predykcyjny.  Na koniec wdrożysz model jako usługę internetową.

W [pierwszej części samouczka](tutorial-part1-credit-risk.md)utworzono obszar roboczy Machine Learning Studio (klasyczny), przekazane dane i utworzono eksperyment.

W tej części samouczka zostaną wykonane następujące czynności:
 
> [!div class="checklist"]
> * Trenowanie wielu modeli
> * Generowanie wyników dla modeli i ich ewaluacja


W [trzeciej części samouczka](tutorial-part3-credit-risk-deploy.md) wdrożysz model jako usługę internetową.

## <a name="prerequisites"></a>Wymagania wstępne

Wykonanie [pierwszej części samouczka](tutorial-part1-credit-risk.md).

## <a name="train-multiple-models"></a><a name="train"></a>Trenowanie wielu modeli

Jedną z zalet używania Azure Machine Learning Studio (klasycznego) do tworzenia modeli uczenia maszynowego jest możliwość wypróbowania więcej niż jednego typu modelu jednocześnie w jednym doświadczeniu i porównać wyniki. Taki eksperyment ułatwia znalezienie najlepszego rozwiązania problemu.

W ramach eksperymentu opracowywanego w tym samouczku utworzysz dwa różne typy modelu i porównasz wyniki ich oceny, aby określić, który algorytm ma zostać użyty w końcowym eksperymencie.  

Masz do wyboru różne modele. Aby wyświetlić dostępne modele, rozwiń węzeł **Machine Learning** na palecie modułów, a następnie rozwiń węzeł **Initialize Model** (Inicjuj model) i węzły znajdujące się poniżej. Na potrzeby tego eksperymentu wybierzesz moduły [Two-Class Support Vector Machine][two-class-support-vector-machine] — SVM (Dwuklasowa maszyna wektorów nośnych) i [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Dwuklasowe wzmocnione drzewo decyzyjne).

> [!TIP]
> Aby uzyskać pomoc w wyborze, który algorytm Machine Learning najlepiej odpowiada konkretnemu problemowi, który próbujesz rozwiązać, zobacz [jak wybrać algorytmy dla Microsoft Azure Machine Learning Studio (klasyczne)](../how-to-select-algorithms.md).
> 
> 

W tym eksperymencie dodasz zarówno moduł [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Dwuklasowe wzmocnione drzewo decyzyjne), jak i [Two-Class Support Vector Machine][two-class-support-vector-machine] (Dwuklasowa maszyna wektorów nośnych).

### <a name="two-class-boosted-decision-tree"></a>Two-Class Boosted Decision Tree (Dwuklasowe wzmocnione drzewo decyzyjne)

Najpierw skonfiguruj model wzmocnionego drzewa decyzyjnego.

1. Znajdź moduł [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Dwuklasowe wzmocnione drzewo decyzyjne) na palecie modułów i przeciągnij go na kanwę.

1. Znajdź moduł [Train Model][train-model] (Trenowanie modelu), przeciągnij go na kanwę, a następnie połącz wyjście modułu [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Dwuklasowe wzmocnione drzewo decyzyjne) z lewym portem wejściowym modułu [Train Model][train-model] (Trenowanie modelu).
   
   Moduł [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Dwuklasowe wzmocnione drzewo decyzyjne) inicjuje ogólny model, a moduł [Train Model][train-model] (Trenowanie modelu) używa danych trenowania do trenowania modelu. 

1. Połącz lewe wyjście lewego modułu [Execute R Script][execute-r-script] (Wykonanie skryptu R) z prawym portem wejściowym modułu [Train Model][train-model] (Trenowanie modelu). W tym samouczku [użyto do trenowania danych pochodzących z lewej strony](#train) modułu Split Data (Dzielenie danych).
   
   > [!TIP]
   > Nie potrzebujesz dwóch wejść i jednego z wyjść modułu [Execute R Script][execute-r-script] (Wykonanie skryptu R) do tego eksperymentu, więc możesz pozostawić je bez połączenia. 
   > 
   > 

Ta część eksperymentu teraz wygląda mniej więcej tak:  

![Trenowanie modelu](./media/tutorial-part2-credit-risk-train/experiment-with-train-model.png)

Teraz trzeba poinformować moduł [Train Model][train-model] (Trenowanie modelu) o tym, że ma on przewidywać wartość ryzyka kredytowego.

1. Wybierz moduł [Train Model][train-model] (Trenowanie modelu). W okienku **Properties** (Właściwości) kliknij pozycję **Launch column selector** (Uruchom selektor kolumn).

1. W oknie dialogowym **Select a single column** (Wybieranie pojedynczej kolumny) wpisz „credit risk” („ryzyko kredytowe”) w polu wyszukiwania w obszarze **Available Columns** (Dostępne kolumny), wybierz pozycję „Credit risk” („Ryzyko kredytowe”) poniżej i kliknij przycisk strzałki w prawo (**>**) aby przenieść pozycję „Credit risk” („Ryzyko kredytowe”) do pola **Selected Columns** (Wybrane kolumny). 

    ![Wybieranie kolumny Credit risk (Ryzyko kredytowe) dla modułu Train Model (Trenowanie modelu)](./media/tutorial-part2-credit-risk-train/train-model-select-column.png)

1. Kliknij znacznik wyboru **OK**.

### <a name="two-class-support-vector-machine"></a>Two-Class Support Vector Machine (Dwuklasowa maszyna wektorów nośnych)

Następnie skonfigurujesz model SVM.  

Najpierw krótko objaśnimy model SVM. Wzmocnione drzewa decyzyjne dobrze współpracują z cechami dowolnego typu. Jednak ponieważ moduł SVM generuje klasyfikator liniowy, wygenerowany przez niego model ma najlepszy błąd testu, gdy wszystkie cechy liczbowe mają tę samą skalę. Aby przekonwertować wszystkie cechy liczbowe do tej samej skali, użyjesz transformacji „Tanh”(z modułem [Normalize Data][normalize-data](Normalizacja danych)). W ten sposób liczby zostaną przetransformowane do zakresu [0,1]. Moduł SVM konwertuje cechy ciągu na cechy kategorialne, a następnie na binarne cechy 0/1, więc nie musisz ręcznie transformować cech ciągu. Ponadto nie należy transformować kolumny 21 Credit Risk (Ryzyko kredytowe) — jest to kolumna liczbowa, lecz zawiera także wartość, pod kątem przewidywania której moduł jest trenowany, dlatego należy pozostawić ją bez zmian.  

Aby skonfigurować model SVM, wykonaj następujące działania:

1. Znajdź moduł [Two-Class Support Vector Machine][two-class-support-vector-machine] (Dwuklasowa maszyna wektorów nośnych) na palecie modułów i przeciągnij go na kanwę.

1. Kliknij prawym przyciskiem myszy moduł [Train Model][train-model] (Trenowanie modelu), wybierz pozycję **Copy** (Kopiuj), a następnie kliknij prawym przyciskiem myszy kanwę i wybierz pozycję **Paste** (Wklej). Kopia modułu [Train Model][train-model] (Trenowanie modelu) ma wybrane te same kolumny co oryginał.

1. Połącz wyjście modułu [Two-Class Support Vector Machine][two-class-support-vector-machine] (Dwuklasowa maszyna wektorów nośnych) z lewym portem wejściowym drugiego modułu [Train Model][train-model] (Trenowanie modelu).

1. Znajdź moduł [Normalize Data][normalize-data] (Normalizacja danych) i przeciągnij go na kanwę.

1. Połącz lewe wyjście lewego modułu [Execute R Script][execute-r-script] (Wykonanie skryptu R) z wejściem tego modułu (zwróć uwagę na to, że port wyjściowy modułu może być połączony z wieloma modułami).

1. Połącz lewy port wyjściowy modułu [Normalize Data][normalize-data] (Normalizacja danych) z prawym portem wejściowym drugiego modułu [Train Model][train-model] (Trenowanie modelu).

Ta część eksperymentu powinna teraz wyglądać mniej więcej tak:  

![Trenowanie drugiego modelu](./media/tutorial-part2-credit-risk-train/svm-model-added.png)

Teraz skonfiguruj moduł [Normalize Data][normalize-data] (Normalizacja danych):

1. Kliknij, aby wybrać moduł [Normalize Data][normalize-data] (Normalizacja danych). W okienku **Properties** (Właściwości) wybierz wartość **Tanh** dla parametru **Transformation method** (Metoda transformacji).

1. Kliknij pozycję **Launch column selector** (Uruchom selektor kolumn), wybierz opcję „No columns” („Bez kolumn”) dla pozycji **Begin With** (Rozpocznij z), wybierz pozycję **Include** (Dołącz) na pierwszej liście rozwijanej, wybierz pozycję **column type** (typ kolumny) na drugiej liście rozwijanej i pozycję **Numeric** (Liczbowa) na trzeciej liście rozwijanej. W ten sposób określono, że wszystkie kolumny liczbowe (i tylko liczbowe) są przekształcane.

1. Kliknij znak plus (+) z prawej strony tego wiersza — spowoduje to utworzenie wiersza list rozwijanych. Wybierz pozycję **Exclude** (Wyklucz) na pierwszej liście rozwijanej, wybierz pozycję **column names** (nazwy kolumn) na drugiej liście rozwijanej, a następnie wpisz „Credit risk” („Ryzyko kredytowe”) w polu tekstowym. W ten sposób określono, że kolumna Credit Risk (Ryzyko kredytowe) ma być ignorowana (jest to konieczne, ponieważ jest to kolumna liczbowa i byłaby przekształcana, jeśli nie zostałaby wykluczona).

1. Kliknij znacznik wyboru **OK**.  

    ![Wybieranie kolumn dla modułu Normalize Data (Normalizacja danych)](./media/tutorial-part2-credit-risk-train/normalize-data-select-column.png)


Moduł [Normalize Data][normalize-data] (Normalizacja danych) będzie teraz wykonywał przekształcenie Tanh dla wszystkich kolumn liczbowych z wyjątkiem kolumny Credit Risk (Ryzyko kredytowe).  

## <a name="score-and-evaluate-the-models"></a>Generowanie wyników dla modeli i ich ewaluacja

Dane testowe wydzielone za pomocą modułu [Split Data][split] (Dzielenie danych) są używane do oceny trenowanych modeli. Następnie można porównać wyniki dwóch modeli, aby zobaczyć, który wygenerował lepsze wyniki.  

### <a name="add-the-score-model-modules"></a>Dodawanie modułów Score Model (Generowanie wyników dla modelu)

1. Znajdź moduł [Score Model][score-model] (Generowanie wyników dla modelu) i przeciągnij go na kanwę.

1. Połącz moduł [Train Model][train-model] (Trenowanie modelu) połączony z modułem [Two-Class Boosted Decision Tree][two-class-boosted-decision-tree] (Dwuklasowe wzmocnione drzewo decyzyjne) z lewym portem wejściowym modułu [Score Model][score-model] (Generowanie wyników dla modelu).

1. Połącz prawy moduł [Execute R Script][execute-r-script] (Wykonanie skryptu R), czyli dane testowe, z prawym portem wejściowym modułu [Score Model][score-model] (Generowanie wyników dla modelu).

    ![Połączony moduł Score Model (Generowanie wyników dla modelu)](./media/tutorial-part2-credit-risk-train/score-model-connected.png)

   
   Moduł [Score Model][score-model] (Generowanie wyników dla modelu) może teraz pobrać informacje kredytowe z danych testowych, przetworzyć je za pomocą modelu i porównać przewidywania wygenerowane przez model z kolumną rzeczywistego ryzyka kredytowego w danych testowych.

1. Skopiuj i wklej moduł [Score Model][score-model] (Generowanie wyników dla modelu), aby utworzyć drugą kopię.

1. Połącz wyjście modelu SVM (tzn. port wyjściowy modułu [Train Model][train-model] — Trenowanie modelu — połączonego z modułem [Two-Class Support Vector Machine][two-class-support-vector-machine] — Dwuklasowa maszyna wektorów nośnych) z portem wejściowym drugiego modułu [Score Model][score-model] (Generowanie wyników dla modelu).

1. W przypadku modelu SVM należy wykonać tę samą transformację danych testowych co w przypadku danych treningowych. W tym celu skopiuj i wklej moduł [Normalize Data][normalize-data] (Normalizacja danych), aby utworzyć drugą kopię, i połącz go z prawym modułem [Execute R Script][execute-r-script] (Wykonanie skryptu R).

1. Połącz lewe wyjście drugiego modułu [Normalize Data][normalize-data] (Normalizacja danych) z prawym portem wejściowym drugiego modułu [Score Model][score-model] (Generowanie wyników dla modelu).

    ![Połączone oba moduły Score Model (Generowanie wyników dla modelu)](./media/tutorial-part2-credit-risk-train/both-score-models-added.png)


### <a name="add-the-evaluate-model-module"></a>Dodawanie modułu Evaluate Model (Ewaluacja modelu)

Aby ewaluować dwa wyniki oceny i porównać je, należy użyć modułu [Evaluate Model][evaluate-model] (Ewaluacja modelu).  

1. Znajdź moduł [Evaluate Model][evaluate-model] (Ewaluacja modelu) i przeciągnij go na kanwę.

1. Połącz port wyjściowy modułu [Score Model][score-model] (Generowanie wyników dla modelu) skojarzonego z modelem wzmocnionego drzewa decyzyjnego z lewym portem wejściowym modułu [Evaluate Model][evaluate-model] (Ewaluacja modelu).

1. Połącz drugi moduł [Score Model][score-model] (Generowanie wyników dla modelu) z prawym portem wejściowym.  

    ![Połączony moduł Evaluate Model (Ewaluacja modelu)](./media/tutorial-part2-credit-risk-train/evaluate-model-added.png)


### <a name="run-the-experiment-and-check-the-results"></a>Uruchomienie eksperymentu i sprawdzenie wyników

Aby uruchomić eksperyment, kliknij przycisk **RUN** (URUCHOM) poniżej kanwy. Może to potrwać kilka minut. Obracający się wskaźnik na każdym module oznacza, że moduł działa, a następnie zielony znacznik wyboru oznacza, że moduł zakończył pracę. Gdy wszystkie moduły mają zielony znacznik wyboru, oznacza to, że działanie eksperymentu zostało zakończone.

Eksperyment powinien wyglądać teraz mniej więcej tak:  

![Ewaluacja obu modeli](./media/tutorial-part2-credit-risk-train/final-experiment.png)


Aby sprawdzić wyniki, kliknij port wyjściowy modułu [Evaluate Model][evaluate-model] (Ewaluacja modelu), a następnie wybierz pozycję **Visualize** (Wizualizuj).  

Moduł [Evaluate Model][evaluate-model] (Ewaluacja modelu) generuje parę krzywych i metryk, które umożliwiają porównanie wyników dwóch ocenionych modeli. Wyniki można wyświetlić w postaci krzywych operacyjno-charakterystycznych, Dokładność/Czułość lub Wzrost. Dodatkowe wyświetlane dane obejmują macierz błędów, skumulowane wartości dla obszaru pod krzywą i inne metryki. Wartość progu można zmienić, przesuwając suwak w lewo lub w prawo i obserwując, jak wpływa to na zestaw metryk.  

Po prawej stronie wykresu kliknij pozycję **Scored dataset** (Oceniony zestaw danych) lub **Scored dataset to compare** (Oceniony zestaw danych do porównania), aby wyróżnić skojarzoną krzywą i wyświetlić skojarzone metryki poniżej. W legendzie dla krzywych pozycja „Scored dataset” („Oceniony zestaw danych”) odnosi się do lewego portu wejściowego modułu [Evaluate Model][evaluate-model] (Ewaluacja modelu) — w naszym przypadku jest to model wzmocnionego drzewa decyzyjnego. Pozycja „Scored dataset to compare” („Oceniony zestaw danych do porównania”) odnosi się do prawego portu wejściowego — w naszym przypadku jest to model SVM. Po kliknięciu jednej z tych etykiet krzywa dla danego modelu zostanie wyróżniona, a odpowiadające metryki zostaną wyświetlone, jak pokazano na poniższym rysunku.  

![Krzywe operacyjno-charakterystyczne dla modeli](./media/tutorial-part2-credit-risk-train/roc-curves.png)

Badając te wartości, możesz zdecydować, który model daje wyniki najbardziej zbliżone do wyników, których szukasz. Możesz wrócić do eksperymentu i powtórzyć go, zmieniając wartości parametrów w różnych modelach. 

Nauka i sztuka interpretowania tych wyników i strojenie wydajności modelu wykracza poza zakres tego samouczka. Aby uzyskać dodatkową pomoc, możesz przeczytać następujące artykuły:
- [Jak oszacować wydajność modelu w Azure Machine Learning Studio (klasyczny)](evaluate-model-performance.md)
- [Wybierz parametry, aby zoptymalizować algorytmy w Azure Machine Learning Studio (klasyczny)](algorithm-parameters-optimize.md)
- [Interpretowanie wyników modelu w Azure Machine Learning Studio (klasyczny)](interpret-model-results.md)

> [!TIP]
> Każde uruchomienie eksperymentu powoduje zapisanie rekordu iteracji w historii uruchamiania. Te iteracje możesz wyświetlić i wrócić do dowolnej z nich, klikając pozycję **VIEW RUN HISTORY** (WYŚWIETL HISTORIĘ URUCHAMIANIA) poniżej kanwy. Możesz także kliknąć pozycję **Prior Run** (Poprzednie uruchomienie) w okienku **Properties** (Właściwości), aby wrócić do iteracji bezpośrednio poprzedzającej obecnie otwartą iterację.
> 
> Kopię dowolnej iteracji eksperymentu można utworzyć, klikając pozycję **SAVE AS** (ZAPISZ JAKO) poniżej kanwy. 
> Użyj właściwości **Summary** (Podsumowanie) i **Description** (Opis) eksperymentu, aby zanotować własne informacje dotyczące celów iteracji eksperymentu.
> 
> Aby uzyskać więcej informacji, zobacz [Zarządzanie iteracjami eksperymentów w Azure Machine Learning Studio (klasyczny)](manage-experiment-iterations.md).  
> 
> 

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [machine-learning-studio-clean-up](../../../includes/machine-learning-studio-clean-up.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku zostały wykonane następujące kroki: 
 
> [!div class="checklist"]
> * Tworzenie eksperymentu
> * Trenowanie wielu modeli
> * Generowanie wyników dla modeli i ich ewaluacja

Teraz możesz przystąpić do wdrażania modeli dla danych.

> [!div class="nextstepaction"]
> [Samouczek 3 — wdrażanie modeli](tutorial-part3-credit-risk-deploy.md)


<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[split]: /azure/machine-learning/studio-module-reference/split-data
[evaluate-model]: /azure/machine-learning/studio-module-reference/evaluate-model
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
[normalize-data]: /azure/machine-learning/studio-module-reference/normalize-data
[score-model]: /azure/machine-learning/studio-module-reference/score-model
[train-model]: /azure/machine-learning/studio-module-reference/train-model
[two-class-boosted-decision-tree]: /azure/machine-learning/studio-module-reference/two-class-boosted-decision-tree
[two-class-support-vector-machine]: /azure/machine-learning/studio-module-reference/two-class-support-vector-machine
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/