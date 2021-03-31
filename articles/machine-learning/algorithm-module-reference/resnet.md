---
title: Model ResNet
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć model klasyfikacji obrazów w programie Azure Machine Learning Designer przy użyciu algorytmu ResNet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 88a820d0f1fa9515b4f2992a8305a2d1065e0987
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93421213"
---
# <a name="resnet"></a>Model ResNet

W tym artykule opisano, jak używać modułu **ResNet** w projektancie Azure Machine Learning, aby utworzyć model klasyfikacji obrazów przy użyciu algorytmu ResNet.  

Ten algorytm klasyfikacji to nadzorowana Metoda uczenia i wymaga zestawu danych z etykietą. 
> [!NOTE]
> Ten moduł nie obsługuje oznaczonego zestawem danych wygenerowanego na podstawie *etykietowania* w programie Studio, ale obsługuje tylko katalog obrazu z etykietami wygenerowany na podstawie [konwersji do modułu usługi Image Directory](convert-to-image-directory.md) . 

Możesz nauczyć model, dostarczając model i katalog obrazu z etykietą jako dane wejściowe do [uczenia modelu Pytorch](train-pytorch-model.md). Model przeszkolony może następnie służyć do przewidywania wartości nowych przykładów wejściowych przy użyciu [modelu obrazu oceny](score-image-model.md).

### <a name="more-about-resnet"></a>Więcej informacji na temat ResNet

Zapoznaj się z [tym dokumentem](https://pytorch.org/docs/stable/torchvision/models.html?highlight=resnext101_32x8d#torchvision.models.resnext101_32x8d) , aby uzyskać więcej informacji na temat ResNet.

## <a name="how-to-configure-resnet"></a>Jak skonfigurować ResNet

1.  Dodaj moduł **ResNet** do potoku w projektancie.  

2.  W przypadku **nazwy modelu** należy określić nazwę pewnej struktury ResNet, a można wybrać opcję z obsługiwanych ResNet: "resnet18", "resnet34", "resnet50", "resnet101", "resnet152", "resnet152", "resnext50 32x4d", "resnext101", " \_ \_ wide_resnet50 \_ 2", "wide_resnet101 \_ 2".

3.  W przypadku wstępnego **przeszkolenia** Określ, czy model ma być używany wstępnie przez ImageNet. W przypadku wybrania tej możliwości można dostosować model na podstawie wybranego wstępnie nauczonego modelu. w przypadku zaznaczenia tej opcji można nauczyć się od podstaw.

4.  Połącz dane wyjściowe modułu **DenseNet** module, szkoleń i walidacji zestawu danych obrazu z [modelem uczenie Pytorch](train-pytorch-model.md). 

5. Prześlij potok.

## <a name="results"></a>Wyniki

Po zakończeniu przebiegu potoku, aby użyć modelu do oceniania, Połącz [model uczenia Pytorch](train-pytorch-model.md) z [modelem obrazu oceny](score-image-model.md), aby przewidzieć wartości dla nowych przykładów wejściowych.

## <a name="technical-notes"></a>Uwagi techniczne  

###  <a name="module-parameters"></a>Parametry modułu  

| Nazwa       | Zakres | Typ    | Domyślne           | Opis                              |
| ---------- | ----- | ------- | ----------------- | ---------------------------------------- |
| Nazwa modelu | Dowolne   | Tryb    | resnext101 \_ 32x8d | Nazwa pewnej struktury ResNet       |
| Przeduczenie | Dowolne   | Boolean | Prawda              | Czy używać modelu wstępnie nauczonego na ImageNet |
|            |       |         |                   |                                          |

###  <a name="output"></a>Dane wyjściowe  

| Nazwa            | Typ                    | Opis                              |
| --------------- | ----------------------- | ---------------------------------------- |
| Nieuczenie modelu | UntrainedModelDirectory | Nieuczenie modelu ResNet, który może być połączony z modelem uczenia Pytorch. |

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 