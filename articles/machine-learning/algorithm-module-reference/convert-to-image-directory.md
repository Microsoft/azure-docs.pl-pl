---
title: Konwertowanie na katalog obrazów
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak przekonwertować zestaw danych na format katalogu obrazów przy użyciu modułu Konwertuj na katalog obrazu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: b29b5fa1beb19bc055f94c56b064ae2c0ae175b5
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171146"
---
# <a name="convert-to-image-directory"></a>Konwertowanie na katalog obrazów

W tym artykule opisano sposób użycia modułu Konwertuj na katalog obrazu w celu ułatwienia konwersji zestawu danych obrazu na typ danych "Image Directory", który jest standardowym formatem danych w zadaniach związanych z obrazami, takimi jak Klasyfikacja obrazu w programie Azure Machine Learning Designer (wersja zapoznawcza).

## <a name="how-to-use-convert-to-image-directory"></a>Jak używać konwersji do katalogu obrazów  

1.  Dodaj do eksperymentu moduł **Konwertuj na katalog obrazu** . Ten moduł można znaleźć w kategorii "przetwarzanie obrazów/transformacje danych obrazu" na liście modułów. 

2.  [Zarejestruj zestaw danych obrazu](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) i połącz go z portem wejściowym modułu. Upewnij się, że w wejściowym zestawie danych znajduje się obraz. 
    Obsługiwane są następujące formaty zestawu danych:

    - Skompresowany plik w tych rozszerzeniach: "zip", ". tar", ". gz", ". bz2".
    - Folder zawierający obrazy. **Zdecydowanie zaleca się kompresowanie takiego folderu jako pierwszy, a następnie użycie skompresowanego pliku jako zestawu danych**.

    > [!WARNING]
    > **Nie można** użyć modułu **Import danych** w celu zaimportowania zestawu danych obrazu, ponieważ typ danych wyjściowych modułu **importowania danych** jest katalogiem Dataframe, który zawiera tylko ciąg ścieżki pliku.
    

    > [!NOTE]
    > Jeśli Użyj zestawu danych obrazu w nadzorowanej uczeniu, etykieta jest wymagana.
    > W przypadku zadania klasyfikacji obrazu można wygenerować etykietę jako "Kategoria" w danych wyjściowych modułu, jeśli ten zestaw danych obrazu jest zorganizowany w formacie torchvision ImageFolder. W przeciwnym razie tylko obrazy są zapisywane bez etykiety. Oto przykład sposobu organizowania zestawu danych obrazu w celu uzyskania etykiety. Użyj kategorii obrazu jako nazwy podfolderu. Aby uzyskać więcej informacji, zobacz [zestawy danych torchvision](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) .
    >
    > ```
    > root/dog/xxx.png
    > root/dog/xxy.png
    > root/dog/xxz.png
    >
    > root/cat/123.png
    > root/cat/nsdf3.png
    > root/cat/asd932_.png
    > ```

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
