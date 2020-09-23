---
title: Dzielenie katalogu obrazów
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu modelu obrazu wynikowego w Azure Machine Learning do generowania prognoz przy użyciu przeszkolonego modelu obrazu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: 63ae7115f905523a3aac131fd7e77b56eb695243
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90890269"
---
# <a name="split-image-directory"></a>Dzielenie katalogu obrazów

W tym temacie opisano, jak używać modułu Split Image Directory w programie Azure Machine Learning Designer do dzielenia obrazów katalogu obrazu na dwa różne zestawy.

Ten moduł jest szczególnie przydatny, gdy trzeba podzielić dane obrazu na zestawy szkoleniowe i testowe. 

## <a name="how-to-configure-split-image-directory"></a>Jak skonfigurować katalog obrazu dzielonego

1. Dodaj moduł **Split Image Directory** do potoku. Ten moduł można znaleźć w kategorii "przetwarzanie obrazów/transformacje danych obrazu".

2. Połącz go z modułem, którego dane wyjściowe to katalog obrazu.

3. Wprowadź **ułamek danych w pierwszym danych wyjściowych** , aby określić wartość procentową, która ma zostać umieszczona w lewym podziale, domyślnie 0,9.


## <a name="technical-notes"></a>Uwagi techniczne

### <a name="expected-inputs"></a>Oczekiwane dane wejściowe

| Nazwa                  | Typ           | Opis              |
| --------------------- | -------------- | ------------------------ |
| Katalog obrazu wejściowego | ImageDirectory | Katalog obrazu do podziału |

### <a name="module-parameters"></a>Parametry modułu

| Nazwa                                   | Typ  | Zakres | Opcjonalne | Opis                            | Domyślny |
| -------------------------------------- | ----- | ----- | -------- | -------------------------------------- | ------- |
| Ułamek obrazów w pierwszym elemencie wyjściowym | Float | 0-1   | Wymagane | Ułamek obrazów w pierwszym elemencie wyjściowym | 0,9     |

### <a name="outputs"></a>Dane wyjściowe

| Nazwa                    | Typ           | Opis                              |
| ----------------------- | -------------- | ---------------------------------------- |
| Directory1 obrazu wyjściowego | ImageDirectory | Katalog obrazu, który zawiera wybrane obrazy |
| Directory2 obrazu wyjściowego | ImageDirectory | Katalog obrazu, który zawiera wszystkie inne obrazy |

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 

