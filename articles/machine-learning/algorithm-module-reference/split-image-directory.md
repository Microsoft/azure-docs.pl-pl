---
title: Dzielenie katalogu obrazów
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu Split Image Directory w projektancie do dzielenia obrazów katalogu obrazu na dwa różne zestawy.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 3ee4dd9b2e344ecb3e1a6424ce7310270e7cd076
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93421196"
---
# <a name="split-image-directory"></a>Dzielenie katalogu obrazów

W tym temacie opisano, jak używać modułu Split Image Directory w programie Azure Machine Learning Designer do dzielenia obrazów katalogu obrazu na dwa różne zestawy.

Ten moduł jest szczególnie przydatny, gdy trzeba podzielić dane obrazu na zestawy szkoleniowe i testowe. 

## <a name="how-to-configure-split-image-directory"></a>Jak skonfigurować katalog obrazu dzielonego

1. Dodaj moduł **Split Image Directory** do potoku. Ten moduł można znaleźć w kategorii "przetwarzanie obrazów/transformacje danych obrazu".

2. Połącz go z modułem, którego dane wyjściowe to katalog obrazu.

3. Wprowadź **ułamek danych w pierwszym danych wyjściowych** , aby określić wartość procentową, która ma zostać umieszczona w lewym podziale, domyślnie 0,9. Jeśli wynik Ułamkowy nie jest liczbą całkowitą, moduł używa mniejszej liczby całkowitej.


## <a name="technical-notes"></a>Uwagi techniczne

### <a name="expected-inputs"></a>Oczekiwane dane wejściowe

| Nazwa                  | Typ           | Opis              |
| --------------------- | -------------- | ------------------------ |
| Katalog obrazu wejściowego | ImageDirectory | Katalog obrazu do podziału |

### <a name="module-parameters"></a>Parametry modułu

| Nazwa                                   | Typ  | Zakres | Opcjonalne | Opis                            | Domyślne |
| -------------------------------------- | ----- | ----- | -------- | -------------------------------------- | ------- |
| Ułamek obrazów w pierwszym elemencie wyjściowym | Float | 0-1   | Wymagane | Ułamek obrazów w pierwszym elemencie wyjściowym | 0,9     |

### <a name="outputs"></a>Dane wyjściowe

| Nazwa                    | Typ           | Opis                              |
| ----------------------- | -------------- | ---------------------------------------- |
| Directory1 obrazu wyjściowego | ImageDirectory | Katalog obrazu, który zawiera wybrane obrazy |
| Directory2 obrazu wyjściowego | ImageDirectory | Katalog obrazu, który zawiera wszystkie inne obrazy |

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 

