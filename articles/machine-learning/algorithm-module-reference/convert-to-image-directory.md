---
title: Konwertuj na katalog obrazu
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak przekonwertować zestaw danych na format katalogu obrazów przy użyciu modułu Konwertuj na katalog obrazu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: dc40e0a644f692b397b1f2107b27b1d940d2b284
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84450633"
---
# <a name="convert-to-image-directory"></a>Konwertuj na katalog obrazu

W tym artykule opisano sposób użycia modułu Konwertuj na katalog obrazu w celu ułatwienia konwersji zestawu danych obrazu na typ danych "Image Directory", który jest standardowym formatem danych w zadaniach związanych z obrazami, takimi jak Klasyfikacja obrazu w programie Azure Machine Learning Designer (wersja zapoznawcza).

## <a name="how-to-use-convert-to-image-directory"></a>Jak używać konwersji do katalogu obrazów  

1.  Dodaj do eksperymentu moduł **Konwertuj na katalog obrazu** . Ten moduł można znaleźć w kategorii "przetwarzanie obrazów/transformacje danych obrazu" na liście modułów. 

2.  Połącz zestaw danych obrazu jako dane wejściowe. Upewnij się, że w wejściowym zestawie danych znajduje się obraz.
    Obsługiwane są następujące formaty zestawu danych:

    - Skompresowany plik w tych rozszerzeniach: "zip", ". tar", ". gz", ". bz2".
    - Folder zawierający 1 skompresowany plik w powyższych prawidłowych rozszerzeniach. 
    - Folder zawierający obrazy.

    > [!NOTE]
    > Kategoria obrazu może być rejestrowana w danych wyjściowych modułu, jeśli ten zestaw danych obrazu jest zorganizowany w formacie torchvision ImageFolder, zapoznaj się z tematem [torchvision DataSets](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) , aby uzyskać więcej informacji. W przeciwnym razie zapisywane są tylko obrazy.

3.  Prześlij potok.

## <a name="results"></a>Wyniki

Dane wyjściowe **konwersji do modułu usługi Image Directory** są w formacie katalogu obrazów i mogą być połączone z innymi modułami związanymi z obrazami, których format portu wejściowego jest również katalogiem obrazu.

## <a name="technical-notes"></a>Uwagi techniczne 

###  <a name="expected-inputs"></a>Oczekiwane dane wejściowe  

| Nazwa          | Typ                  | Opis   |
| ------------- | --------------------- | ------------- |
| Wejściowy zestaw danych | AnyDirectory, ZipFile | Wejściowy zestaw danych |

###  <a name="output"></a>Dane wyjściowe  

| Nazwa                   | Typ           | Opis            |
| ---------------------- | -------------- | ---------------------- |
| Katalog obrazu wyjściowego | ImageDirectory | Katalog obrazu wyjściowego |

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
