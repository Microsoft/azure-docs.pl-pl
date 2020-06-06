---
title: ResNet
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć model klasyfikacji obrazów przy użyciu algorytmu ResNet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: ea8c13e134eceeb27bd064e794d46d711092a867
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450731"
---
# <a name="resnet"></a>ResNet

W tym artykule opisano, jak używać modułu **ResNet** w programie Azure Machine Learning Designer (wersja zapoznawcza) w celu utworzenia modelu klasyfikacji obrazów przy użyciu algorytmu ResNet.  

Ten algorytm klasyfikacji to nadzorowana Metoda uczenia i wymaga zestawu danych z etykietą. Aby uzyskać więcej informacji o tym, jak uzyskać katalog obrazu z etykietą, zapoznaj się z tematem [konwertowanie do modułu usługi Image Directory](convert-to-image-directory.md) . Możesz nauczyć model, dostarczając model i katalog obrazu z etykietą jako dane wejściowe do [uczenia modelu Pytorch](train-pytorch-model.md). Model przeszkolony może następnie służyć do przewidywania wartości nowych przykładów wejściowych przy użyciu [modelu obrazu oceny](score-image-model.md).

### <a name="more-about-resnet"></a>Więcej informacji na temat ResNet

Zapoznaj się z [tym dokumentem](https://pytorch.org/docs/stable/torchvision/models.html?highlight=resnext101_32x8d#torchvision.models.resnext101_32x8d) , aby uzyskać więcej informacji na temat ResNet.

## <a name="how-to-configure-resnet"></a>Jak skonfigurować ResNet

1.  Dodaj moduł **ResNet** do potoku w projektancie.  

2.  W przypadku **nazwy modelu**należy określić nazwę pewnej struktury ResNet, a można wybrać opcję z obsługiwanych ResNet: "resnet18", "resnet34", "resnet50", "resnet101", "resnet152", "resnet152", "resnext50 32x4d", "resnext101", " \_ \_ wide_resnet50 \_ 2", "wide_resnet101 \_ 2".

3.  W przypadku wstępnego **przeszkolenia**Określ, czy model ma być używany wstępnie przez ImageNet. W przypadku wybrania tej możliwości można dopasować model na podstawie wybranego wstępnie nauczonego modelu; w przypadku zaznaczenia tej opcji można nauczyć się od podstaw.

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