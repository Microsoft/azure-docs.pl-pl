---
title: 'Drzewo decyzyjnej grupy wieloklasowej: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać wieloklasowego modułu drzewa decyzyjnego w Azure Machine Learning do tworzenia klasyfikatora przy użyciu danych z etykietami.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/19/2020
ms.openlocfilehash: 1a1cb7661ae01dd89d45afe004978813ac90eaff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90905310"
---
# <a name="multiclass-boosted-decision-tree"></a>Wieloklasowe wzmocnione drzewo decyzyjne

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Ten moduł służy do tworzenia modelu uczenia maszynowego, który jest oparty na algorytmie drzew z podwyższaną decyzją.

Wzmocnione drzewo decyzyjne to metoda uczenia się, w której drugie drzewo jest zgodne z błędami pierwszego drzewa, trzecie drzewo jest odpowiednie dla błędów pierwszego i drugiego drzewa i tak dalej. Przewidywania opierają się na rozwiązaniu wszystkich drzew jednocześnie.

## <a name="how-to-configure"></a>Jak skonfigurować 

Ten moduł tworzy niepociąg model klasyfikacji. Ponieważ klasyfikacja jest metodą uczenia nadzorowanego, potrzebny jest *oznaczony zestaw danych* , który zawiera kolumnę etykiety z wartością dla wszystkich wierszy.

Ten typ modelu można przeszkolić przy użyciu [modelu uczenia](././train-model.md). 

1.  Dodaj moduł **drzewa decyzyjnej wieloklasowej** do potoku.

1.  Określ sposób, w jaki model ma być szkolony przez ustawienie opcji **tworzenia trybu Trainer** .

    + **Pojedynczy parametr**: Jeśli wiesz, jak chcesz skonfigurować model, możesz podać określony zestaw wartości jako argumenty.
    
    + **Zakres parametrów**: Wybierz tę opcję, jeśli nie masz pewności co do najlepszych parametrów i chcesz uruchomić odchylenia parametrów. Wybierz zakres wartości do iteracji, a [Parametry dostrojenia modelu](tune-model-hyperparameters.md) przechodzą na wszystkie możliwe kombinacje ustawień, które podano, aby określić parametry, które generują optymalne wyniki.  

1. **Maksymalna liczba liści na drzewo** ogranicza maksymalną liczbę węzłów terminalu (liści), które można utworzyć w dowolnym drzewie.
    
    Zwiększając tę wartość, można zwiększyć rozmiar drzewa i osiągnąć wyższą precyzję w przypadku ryzyka przekroczenia i dłuższego czasu uczenia się.
  
1. **Minimalna liczba próbek na węzeł liścia** wskazuje liczbę przypadków wymaganych do utworzenia dowolnego węzła terminalu w drzewie.  

    Zwiększenie tej wartości spowoduje zwiększenie wartości progowej tworzenia nowych reguł. Na przykład, z wartością domyślną 1, nawet pojedynczy przypadek może spowodować utworzenie nowej reguły. W przypadku zwiększenia wartości do 5 dane szkoleniowe muszą zawierać co najmniej pięć przypadków, które spełniają te same warunki.

1. **Szybkość uczenia** definiuje rozmiar kroku podczas uczenia się. Wprowadź liczbę z zakresu od 0 do 1.

    Szybkość uczenia określa, jak szybko lub wolno dowiedzieć się zbieżność z optymalnym rozwiązaniem. Jeśli rozmiar tego kroku jest zbyt duży, można przekroczyć optymalne rozwiązanie. Jeśli rozmiar tego kroku jest zbyt mały, szkolenie trwa dłużej niż w przypadku najlepszego rozwiązania.

1. **Liczba skonstruowanych drzew** wskazuje całkowitą liczbę drzew decyzyjnych do utworzenia w całości. Przez utworzenie większej liczby drzew decyzyjnych można potencjalnie uzyskać lepszy zakres, ale zwiększy się czas uczenia.

1. **Inicjator liczb losowych** opcjonalnie ustawia nieujemną liczbę całkowitą do użycia jako losową wartość inicjatora. Określenie inicjatora zapewnia odtwarzalność w przebiegach, które mają te same dane i parametry.  

    Losowy inicjator jest domyślnie ustawiony na 42. Kolejne uruchomienia przy użyciu różnych losowych nasion mogą mieć różne wyniki.

1. Uczenie modelu:

    + Jeśli ustawisz **tryb tworzenia Trainer** na **pojedynczy parametr**, Połącz znacznikowy zestaw danych i moduł [uczenie modelu](train-model.md) .  
  
    + Jeśli ustawisz **tryb Trainer** na **zakres parametrów**, Połącz znacznikowy zestaw danych i nauczysz model przy użyciu funkcji [dostrajania parametrów modelu](tune-model-hyperparameters.md).  
  
    > [!NOTE]
    > 
    > Jeśli przejdziesz do zakresu parametrów w celu [nauczenia modelu](train-model.md), zostanie użyta tylko wartość domyślna na liście jednego parametru.  
    > 
    > W przypadku przekazania jednego zestawu wartości parametrów do modułu [strojenia modelu](tune-model-hyperparameters.md) , gdy oczekuje on zakresu ustawień dla każdego parametru, zignoruje wartości i użyje wartości domyślnych dla dodanej informacji.  
    > 
    > Jeśli wybierzesz opcję **zakres parametrów** i wprowadzisz pojedynczą wartość dla każdego parametru, ta pojedyncza wartość zostanie użyta w całym wyczyszczeniu, nawet jeśli inne parametry zmienią się w zakres wartości.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
