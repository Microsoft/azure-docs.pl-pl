---
title: Model DenseNet
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć model klasyfikacji obrazów przy użyciu algorytmu DenseNet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 2351012738f4cf5697fb29891c9459e4cc86cd3a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91536634"
---
# <a name="densenet"></a>Model DenseNet

W tym artykule opisano, jak używać modułu **DenseNet** w projektancie Azure Machine Learning, aby utworzyć model klasyfikacji obrazów przy użyciu algorytmu DenseNet.  

Ten algorytm klasyfikacji to nadzorowana Metoda uczenia i wymaga katalogu obrazu z etykietą. 

> [!NOTE]
> Ten moduł nie obsługuje oznaczonego zestawem danych wygenerowanego na podstawie *etykietowania* w programie Studio, ale obsługuje tylko katalog obrazu z etykietami wygenerowany na podstawie [konwersji do modułu usługi Image Directory](convert-to-image-directory.md) . 

Możesz nauczyć model, dostarczając model i katalog obrazu z etykietą jako dane wejściowe do [uczenia modelu Pytorch](train-pytorch-model.md). Model przeszkolony może następnie służyć do przewidywania wartości nowych przykładów wejściowych przy użyciu [modelu obrazu oceny](score-image-model.md).

### <a name="more-about-densenet"></a>Więcej informacji na temat DenseNet

Aby uzyskać więcej informacji na temat DenseNet, zobacz dokument dotyczący badań, [gęsto podłączone sieci splotowych](https://arxiv.org/abs/1608.06993).

## <a name="how-to-configure-densenet"></a>Jak skonfigurować DenseNet

1.  Dodaj moduł **DenseNet** do potoku w projektancie.  

2.  W przypadku **nazwy modelu**należy określić nazwę pewnej struktury DenseNet, a można wybrać opcję z obsługiwanej DenseNet: "densenet121", "densenet161", "densenet169", "densenet201".

3.  W przypadku wstępnego **przeszkolenia**Określ, czy model ma być używany wstępnie przez ImageNet. W przypadku wybrania tej możliwości można dostosować model na podstawie wybranego wstępnie nauczonego modelu. w przypadku zaznaczenia tej opcji można nauczyć się od podstaw.

4.  W celu **zapewnienia wydajnej pamięci**należy określić, czy należy używać punktów kontrolnych, które są znacznie bardziej wydajne, ale wolniejsze. Aby uzyskać więcej informacji, zobacz dokument dotyczący badań, [wydajna implementacja DenseNets w pamięci](https://arxiv.org/pdf/1707.06990.pdf).

5.  Połącz dane wyjściowe modułu **DenseNet** module, szkoleń i walidacji zestawu danych obrazu z [modelem uczenie Pytorch](train-pytorch-model.md). 

6. Prześlij potok.


## <a name="results"></a>Wyniki

Po zakończeniu przebiegu potoku, aby użyć modelu do oceniania, Połącz [model uczenia Pytorch](train-pytorch-model.md) z [modelem obrazu oceny](score-image-model.md), aby przewidzieć wartości dla nowych przykładów wejściowych.

## <a name="technical-notes"></a>Uwagi techniczne  

###  <a name="module-parameters"></a>Parametry modułu  

| Nazwa             | Zakres | Type    | Domyślne     | Opis                              |
| ---------------- | ----- | ------- | ----------- | ---------------------------------------- |
| Nazwa modelu       | Dowolne   | Tryb    | densenet201 | Nazwa pewnej struktury DenseNet     |
| Przeduczenie       | Dowolne   | Boolean | Prawda        | Czy używać modelu wstępnie nauczonego na ImageNet |
| Wydajna pamięć | Dowolne   | Boolean (wartość logiczna) | Fałsz       | Czy należy używać punktów kontrolnych, co jest znacznie bardziej wydajne, ale wolniejsze |

###  <a name="output"></a>Dane wyjściowe  

| Nazwa            | Typ                    | Opis                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Nieuczenie modelu | UntrainedModelDirectory | Nieuczenie modelu DenseNet, który może być połączony z modelem uczenia Pytorch. |

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
