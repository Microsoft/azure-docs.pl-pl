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
ms.date: 02/11/2020
ms.openlocfilehash: e3080836e8b9ed38e99c691c66e71a4620829c90
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90890205"
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
  
1.  W Azure Machine Learning Skonfiguruj model klasyfikacji lub model regresji.
    
2. Dodaj moduł **uczenie modelu** do potoku.  Ten moduł można znaleźć pod kategorią **Machine Learning** . Rozwiń węzeł **uczenie**, a następnie przeciągnij moduł **uczenie modelu** do potoku.
  
3.  Z lewej strony Dołącz tryb niepociąg. Dołącz zestaw danych szkoleniowych do prawego wejścia **modelu uczenia**.

    Zestaw danych szkoleniowych musi zawierać kolumnę Label. Wszystkie wiersze bez etykiet są ignorowane.
  
4.  Dla **kolumny etykieta**kliknij pozycję **Edytuj kolumnę** w prawym panelu modułu, a następnie wybierz pojedynczą kolumnę zawierającą wyniki, które będą używane przez model do szkoleń.
  
    - W przypadku problemów z klasyfikacją kolumna Label musi zawierać wartości **kategorii** lub wartości **dyskretnych** . Niektóre przykłady mogą być klasyfikacją tak/bez, kodem klasyfikacji lub grupą przychodów.  W przypadku wybrania kolumny noncategorical moduł zwróci błąd podczas szkolenia.
  
    -   W przypadku problemów z regresją kolumna etykieta musi zawierać dane **liczbowe** reprezentujące zmienną odpowiedzi. Najlepiej, gdy dane liczbowe reprezentują ciągłą skalę. 
    
    Przykładami mogą być oceny ryzyka kredytowego, przewidywany czas do niepowodzenia na dysku twardym lub prognozowaną liczbę wywołań do centrum wywołania w danym dniu lub godzinie.  Jeśli nie wybierzesz kolumny liczbowej, może wystąpić błąd.
  
    -   Jeśli nie określisz, której kolumny etykiety użyć, Azure Machine Learning próbuje wnioskować, która jest odpowiednią kolumną etykiety, przy użyciu metadanych zestawu danych. W przypadku pobrania niewłaściwej kolumny Użyj selektora kolumn, aby je poprawić.
  
    > [!TIP] 
    > Jeśli masz problemy z użyciem selektora kolumn, zobacz artykuł [Wybieranie kolumn w zestawie danych](./select-columns-in-dataset.md) , aby uzyskać porady. Opisano niektóre typowe scenariusze i porady dotyczące korzystania **z reguł with** i **według nazwy** .
  
5.  Prześlij potok. W przypadku dużej ilości danych może to chwilę potrwać.

## <a name="results"></a>Wyniki

Po przeszkoleniu modelu:


+ Aby użyć modelu w innych potokach, wybierz moduł i wybierz ikonę **zarejestruj zestaw danych** na karcie dane **wyjściowe** w prawym panelu. Dostęp do zapisanych modeli można uzyskać w palecie modułów w obszarze **zestawy danych**.

+ Aby użyć modelu w celu przewidywania nowych wartości, połącz go z modułem [modelu oceny](./score-model.md) wraz z nowymi danymi wejściowymi.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 