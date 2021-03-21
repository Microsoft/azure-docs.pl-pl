---
title: 'Model uczenia: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu **uczenie modelu** w Azure Machine Learning, aby szkolić model klasyfikacji lub regresji.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2021
ms.openlocfilehash: 77927472dae6c8e7e6fddacf9088b479636edd37
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103224342"
---
# <a name="train-model-module"></a>Moduł uczenia modelu

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Ten moduł służy do uczenia modelu klasyfikacji lub regresji. Szkolenia odbywają się po zdefiniowaniu modelu i ustawieniu jego parametrów i wymaganiu oznakowanych danych. Możesz również użyć **modelu uczenia** , aby ponownie szkolić istniejący model przy użyciu nowych danych. 

## <a name="how-the-training-process-works"></a>Jak działa proces uczenia

W Azure Machine Learning tworzenie i Używanie modelu uczenia maszynowego jest zwykle procesem dwuetapowym. 

1. Należy skonfigurować model, wybierając konkretny typ algorytmu i definiując jego parametry lub parametr. Wybierz dowolny z następujących typów modeli: 

    + Modele **klasyfikacji** w oparciu o sieci neuronowych, drzewa decyzyjne i lasy decyzyjne oraz inne algorytmy.
    + Modele **regresji** , które mogą uwzględniać standardowe regresję liniową lub które używają innych algorytmów, w tym sieci neuronowych i regresji bayesowskie.  

2. Podaj zestaw danych, który ma etykietę i zawiera dane zgodne z algorytmem. Połącz zarówno dane, jak i model, aby **szkolić model**.

    Co to jest konkretny format binarny, iLearner, który hermetyzuje wzorce statystyczne uzyskane z danych. Nie można bezpośrednio zmodyfikować ani odczytać tego formatu; Jednak inne moduły mogą korzystać z tego przeszkolonego modelu. 
    
    Możesz również wyświetlić właściwości modelu. Aby uzyskać więcej informacji, zobacz sekcję wyniki.

3. Po zakończeniu szkolenia Użyj modelu przeszkolonego z jednym z [modułów oceniania](./score-model.md), aby tworzyć przewidywania dotyczące nowych danych.

## <a name="how-to-use-train-model"></a>Jak używać modelu uczenia 
    
1. Dodaj moduł **uczenie modelu** do potoku.  Ten moduł można znaleźć pod kategorią **Machine Learning** . Rozwiń węzeł **uczenie**, a następnie przeciągnij moduł **uczenie modelu** do potoku.
  
1.  Z lewej strony Dołącz tryb niepociąg. Dołącz zestaw danych szkoleniowych do prawego wejścia **modelu uczenia**.

    Zestaw danych szkoleniowych musi zawierać kolumnę Label. Wszystkie wiersze bez etykiet są ignorowane.
  
1.  Dla **kolumny etykieta** kliknij pozycję **Edytuj kolumnę** w prawym panelu modułu, a następnie wybierz pojedynczą kolumnę zawierającą wyniki, które będą używane przez model do szkoleń.
  
    - W przypadku problemów z klasyfikacją kolumna Label musi zawierać wartości **kategorii** lub wartości **dyskretnych** . Niektóre przykłady mogą być klasyfikacją tak/bez, kodem klasyfikacji lub grupą przychodów.  W przypadku wybrania kolumny noncategorical moduł zwróci błąd podczas szkolenia.
  
    -   W przypadku problemów z regresją kolumna etykieta musi zawierać dane **liczbowe** reprezentujące zmienną odpowiedzi. Najlepiej, gdy dane liczbowe reprezentują ciągłą skalę. 
    
    Przykładami mogą być oceny ryzyka kredytowego, przewidywany czas do niepowodzenia na dysku twardym lub prognozowaną liczbę wywołań do centrum wywołania w danym dniu lub godzinie.  Jeśli nie wybierzesz kolumny liczbowej, może wystąpić błąd.
  
    -   Jeśli nie określisz, której kolumny etykiety użyć, Azure Machine Learning próbuje wnioskować, która jest odpowiednią kolumną etykiety, przy użyciu metadanych zestawu danych. W przypadku pobrania niewłaściwej kolumny Użyj selektora kolumn, aby je poprawić.
  
    > [!TIP] 
    > Jeśli masz problemy z użyciem selektora kolumn, zobacz artykuł [Wybieranie kolumn w zestawie danych](./select-columns-in-dataset.md) , aby uzyskać porady. Opisano niektóre typowe scenariusze i porady dotyczące korzystania **z reguł with** i **według nazwy** .
  
1.  Prześlij potok. W przypadku dużej ilości danych może to chwilę potrwać.

    > [!IMPORTANT] 
    > Jeśli masz kolumnę identyfikatora, która jest IDENTYFIKATORem każdego wiersza, lub kolumna tekstowa, która zawiera zbyt wiele unikatowych wartości, **model uczenia** może wystąpić błąd, na przykład "Liczba unikatowych wartości w kolumnie:" {column_name} "jest większa niż dozwolona.
    >
    > Wynika to z faktu, że kolumna osiągnęła próg unikatowych wartości i może spowodować brak pamięci. Możesz użyć opcji [Edytuj metadane](edit-metadata.md) , aby oznaczyć tę kolumnę jako **funkcję czyszczenia** i nie będzie ona używana w szkoleniu ani nie [Wyodrębnij funkcji N-gramowych z modułu tekstowego](extract-n-gram-features-from-text.md) w celu wstępnego przetworzenia kolumny tekstu. Aby uzyskać szczegółowe informacje o błędzie, zobacz [Kod błędu projektanta](././designer-error-codes.md) .

## <a name="model-interpretability"></a>Interpretacja modelu

Możliwość interpretowania modeli umożliwia comprehend modelu ML i przedstawia podstawową podstawę podejmowania decyzji w sposób zrozumiały dla ludzi.

Moduł aktualnie **pouczenie modelu** obsługuje [Używanie pakietu do interpretacji w celu wyjaśnienia modeli ml](https://docs.microsoft.com/azure/machine-learning/how-to-machine-learning-interpretability-aml#generate-feature-importance-values-via-remote-runs). Obsługiwane są następujące algorytmy wbudowane:

- Regresja liniowa
- Regresja sieci neuronowej
- Dwuklasowa regresja logistyczna
- Two-Class Support Vector Machine (Dwuklasowa maszyna wektorów nośnych)
- Las decyzyjny wieloklasowej

W celu wygenerowania wyjaśnień modelu można wybrać opcję **prawda** na liście rozwijanej **wyjaśnienie modelu** w module model uczenia. Domyślnie w module **uczenie modelu** jest ustawiona wartość false. Należy pamiętać, że generowanie wyjaśnienia wymaga dodatkowego kosztu obliczeniowego.

![Zrzut ekranu przedstawiający pole wyboru informacji o modelu](./media/module/train-model-explanation-checkbox.png)

Po zakończeniu przebiegu potoku można odwiedzić kartę **wyjaśnienia** w prawym okienku modułu **uczenie modelu** i zapoznać się z modelem wydajność, zestawem danych i znaczeniem funkcji.

![Zrzut ekranu przedstawiający wykresy wyjaśniające model](./media/module/train-model-explanations-tab.gif)

Aby dowiedzieć się więcej o używaniu wyjaśnień modelowania w Azure Machine Learning, zapoznaj się z artykułem z artykułu jak [interpretować modele ml](https://docs.microsoft.com/azure/machine-learning/how-to-machine-learning-interpretability-aml#generate-feature-importance-values-via-remote-runs).

## <a name="results"></a>Wyniki

Po przeszkoleniu modelu:


+ Aby użyć modelu w innych potokach, wybierz moduł i wybierz ikonę **zarejestruj zestaw danych** na karcie dane **wyjściowe** w prawym panelu. Dostęp do zapisanych modeli można uzyskać w palecie modułów w obszarze **zestawy danych**.

+ Aby użyć modelu w celu przewidywania nowych wartości, połącz go z modułem [modelu oceny](./score-model.md) wraz z nowymi danymi wejściowymi.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 