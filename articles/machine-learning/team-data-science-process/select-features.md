---
title: Wybór funkcji w procesie nauki danych zespołu
description: Wyjaśnia cel wyboru funkcji i zawiera przykłady ich roli w procesie ulepszania danych uczenia maszynowego.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 940d4c465acb5e8478d2b204ab5b9b18c79254f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93321319"
---
# <a name="feature-selection-in-the-team-data-science-process-tdsp"></a>Wybór funkcji w zespołowym przetwarzaniu danych dla celów naukowych
W tym artykule wyjaśniono cele wyboru funkcji i przedstawiono przykłady jej roli w procesie rozszerzania danych uczenia maszynowego. Te przykłady są rysowane z Azure Machine Learning Studio.

Inżynieria i wybór funkcji to jedna część procesu nauki o danych zespołowych (przetwarzania TDSP), którą opisano w artykule [co to jest proces nauki o danych zespołowych?](overview.md) Inżynieria funkcji i wybór są częścią kroku **opracowywania funkcji** przetwarzania TDSP.

* **Inżynieria funkcji**: ten proces podejmuje próbę utworzenia dodatkowych odpowiednich funkcji z istniejących pierwotnych funkcji w danych i zwiększenia mocy predykcyjnej do algorytmu uczenia.
* **wybór funkcji**: ten proces wybiera podzestaw kluczy oryginalnych funkcji danych w próbie zmniejszenia liczby problemów szkoleniowych.

Zwykle **Funkcja inżynierii funkcji** jest stosowana najpierw w celu wygenerowania dodatkowych funkcji, a następnie krok **wyboru funkcji** jest wykonywany w celu wyeliminowania nieistotnych, nadmiarowych lub wysoce skorelowanych funkcji.

## <a name="filter-features-from-your-data---feature-selection"></a>Filtrowanie funkcji z poziomu wyboru funkcji danych
Wybór funkcji może być używany na potrzeby zadań klasyfikacji lub regresji. Celem jest wybranie podzestawu funkcji z oryginalnego zestawu danych, który zmniejsza jego wymiary przy użyciu minimalnego zestawu funkcji do reprezentowania maksymalnej ilości wariancji danych. Ten podzbiór funkcji jest używany do uczenia modelu. Wybór funkcji służy do dwóch głównych celów.

* Najpierw wybór funkcji często zwiększa dokładność klasyfikacji, eliminując nieistotne, nadmiarowe lub wysoce skorelowane funkcje.
* Po drugie, zmniejsza liczbę funkcji, co sprawia, że proces szkolenia modelu jest bardziej wydajny. Wydajność jest ważna dla zdobywających informacje, które są kosztowne do uczenia się, takich jak maszyny wektorów pomocy technicznej.

Mimo że funkcja wyboru funkcji pozwala na zmniejszenie liczby funkcji w zestawie danych używanym do uczenia modelu, nie jest ona określana przez termin "redukcja wymiarowego". Metody wyboru funkcji wyodrębniają podzestaw oryginalnych funkcji danych bez ich zmiany.  Metody redukcji wymiarowej wykorzystują funkcje, które umożliwiają przekształcanie oryginalnych funkcji, a tym samym ich modyfikowanie. Przykłady metod zmniejszania liczby wymiarów obejmują analizę składników głównych, analizę korelacji kanonicznej i wieloznacznej dekompozycję wartości.

Od innych, jedna szeroko stosowana Kategoria metod wyboru funkcji w kontekście nadzorowanym jest nazywana "wyborem funkcji opartym na filtrowaniu". Oceniając korelację między każdą funkcją a atrybutem Target, te metody stosują miarę statystyczną do przypisywania oceny do każdej funkcji. Funkcje są następnie klasyfikowane według wyniku, którego można użyć, aby określić próg utrzymywania lub eliminowania określonej funkcji. Przykłady środków statystycznych używanych w tych metodach obejmują korelację osób, wzajemne informacje oraz test chi kwadrat.

W Azure Machine Learning Studio są dostępne moduły do wyboru funkcji. Jak pokazano na poniższej ilustracji, te moduły obejmują [wybór funkcji oparty na filtrowaniu][filter-based-feature-selection] i [analizę liniową discriminant][fisher-linear-discriminant-analysis].

![Moduły wyboru funkcji](./media/select-features/feature-Selection.png)

Rozważmy na przykład użycie modułu [wyboru funkcji opartego na filtrze][filter-based-feature-selection] . Dla wygody należy nadal używać przykładu wyszukiwania tekstu. Załóżmy, że chcesz skompilować model regresji po utworzeniu zestawu funkcji 256 za pośrednictwem modułu [wyznaczania wartości skrótu funkcji][feature-hashing] oraz że zmienna odpowiedzi jest "Kol1", która zawiera klasyfikacje przeglądów z zakresu od 1 do 5. Ustawienie "Metoda oceniania cech" jako "korelacji Pearsona", "kolumna docelowa" ma wartość "Kol1" i "Liczba żądanych funkcji" do 50. Następnie [wybór funkcji oparty na filtrze][filter-based-feature-selection] modułu tworzy zestaw danych zawierający 50 funkcji wraz z atrybutem Target "Kol1". Na poniższej ilustracji przedstawiono przepływ tego eksperymentu i parametry wejściowe:

![Właściwości modułu wyboru funkcji Filter-Based](./media/select-features/feature-Selection1.png)

Na poniższej ilustracji przedstawiono zestawy danych, które są następujące:

![Powstały zestaw danych dla modułu wyboru funkcji opartego na filtrze](./media/select-features/feature-Selection2.png)

Poszczególne funkcje są oceniane na podstawie korelacji Pearsona między nią a docelowym atrybutem "Kol1". Funkcje z najważniejszymi wynikami są zachowywane.

Na poniższej ilustracji przedstawiono odpowiednie wyniki wybranych funkcji:

![Wyniki dla modułu wyboru funkcji opartego na filtrze](./media/select-features/feature-Selection3.png)

Po zastosowaniu tego modułu [wyboru funkcji opartego na filtrze][filter-based-feature-selection] są wybierane 50 z 256 funkcji, ponieważ mają one najbardziej skorelowane funkcje z zmienną docelową "Kol1" na podstawie metody oceniania "Pearson korelacji".

## <a name="conclusion"></a>Podsumowanie
Inżynieria funkcji i wybór funkcji są dwa powszechnie używane i wybrane funkcje zwiększają wydajność procesu szkolenia, który próbuje wyodrębnić informacje o kluczu zawarte w danych. Poprawiają one również możliwości tych modeli w celu dokładnego klasyfikowania danych wejściowych i przewidywania wyników zainteresowania bardziej niezawodnie. Inżynieria funkcji i wybór mogą również łączyć się, aby zwiększyć możliwości obliczeniowe. Robi to przez zwiększenie i zmniejszenie liczby funkcji wymaganych do kalibracji lub uczenia modelu. Matematycznie mówiąc, funkcje wybrane do uczenia modelu są minimalnym zestawem niezależnych zmiennych, które wyjaśniają wzorce w danych, a następnie przewidywania wyników powiodło się.

Nie zawsze jest konieczna funkcja inżynierii lub wybór funkcji. Czy jest to konieczne, czy nie zależy od zbieranych danych, wybranego algorytmu i celu eksperymentu.

<!-- Module References -->
[feature-hashing]: /azure/machine-learning/studio-module-reference/feature-hashing
[filter-based-feature-selection]: /previous-versions/azure/dn905854(v=azure.100)
[fisher-linear-discriminant-analysis]: /azure/machine-learning/studio-module-reference/fisher-linear-discriminant-analysis