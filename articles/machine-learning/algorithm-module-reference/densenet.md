---
title: DenseNet
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć model klasyfikacji obrazów przy użyciu algorytmu densenet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: db0e2f90ee45d4e1c0173cbc037084793a66e149
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450647"
---
# <a name="densenet"></a>DenseNet

W tym artykule opisano, jak używać modułu **DenseNet** w programie Azure Machine Learning Designer (wersja zapoznawcza) w celu utworzenia modelu klasyfikacji obrazów przy użyciu algorytmu DenseNet.  

Ten algorytm klasyfikacji to nadzorowana Metoda uczenia i wymaga zestawu danych z etykietą. Zapoznaj się z tematem [konwertowanie do modułu usługi Image Directory](convert-to-image-directory.md) , aby uzyskać więcej instrukcji na temat pobierania katalogu obrazu z etykietą. Możesz nauczyć model, dostarczając model i katalog obrazu z etykietą jako dane wejściowe do [uczenia modelu Pytorch](train-pytorch-model.md). Model przeszkolony może następnie służyć do przewidywania wartości nowych przykładów wejściowych przy użyciu [modelu obrazu oceny](score-image-model.md).

### <a name="more-about-densenet"></a>Więcej informacji na temat DenseNet

Aby uzyskać więcej informacji, zobacz [gęsto połączone sieci splotowych](https://arxiv.org/abs/1608.06993) .

## <a name="how-to-configure-densenet"></a>Jak skonfigurować DenseNet

1.  Dodaj moduł **DenseNet** do potoku w projektancie.  

2.  W przypadku **nazwy modelu**należy określić nazwę pewnej struktury densenet, a można wybrać opcję z obsługiwanej densenet: "densenet121", "densenet161", "densenet169", "densenet201".

3.  W przypadku wstępnego **przeszkolenia**Określ, czy model ma być używany wstępnie przez ImageNet. W przypadku wybrania tej możliwości można dopasować model na podstawie wybranego wstępnie nauczonego modelu; w przypadku zaznaczenia tej opcji można nauczyć się od podstaw.

4.  W celu **zapewnienia wydajnej pamięci**należy określić, czy należy używać punktów kontrolnych, które są znacznie bardziej wydajne, ale wolniejsze. Aby uzyskać więcej informacji, zobacz https://arxiv.org/pdf/1707.06990.pdf.

5.  Połącz dane wyjściowe modułu **DenseNet** module, szkoleń i walidacji zestawu danych obrazu z [modelem uczenie Pytorch](train-pytorch-model.md). 

6. Prześlij potok.


## <a name="results"></a>Wyniki

Po zakończeniu przebiegu potoku, aby użyć modelu do oceniania, Połącz [model uczenia Pytorch](train-pytorch-model.md) z [modelem obrazu oceny](score-image-model.md), aby przewidzieć wartości dla nowych przykładów wejściowych.

## <a name="technical-notes"></a>Uwagi techniczne  

###  <a name="module-parameters"></a>Parametry modułu  

| Nazwa             | Zakres | Typ    | Domyślne     | Opis                              |
| ---------------- | ----- | ------- | ----------- | ---------------------------------------- |
| Nazwa modelu       | Dowolne   | Tryb    | densenet201 | Nazwa pewnej struktury densenet     |
| Przeduczenie       | Dowolne   | Boolean | Prawda        | Czy używać modelu wstępnie nauczonego na ImageNet |
| Wydajna pamięć | Dowolne   | Boolean | Fałsz       | Czy należy używać punktów kontrolnych, co jest znacznie bardziej wydajne, ale wolniejsze |

###  <a name="output"></a>Dane wyjściowe  

| Nazwa            | Typ                    | Opis                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Nieuczenie modelu | UntrainedModelDirectory | Nieuczenie modelu densenet, który może być połączony z modelem uczenia Pytorch. |

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
