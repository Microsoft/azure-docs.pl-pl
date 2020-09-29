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
ms.date: 09/28/2020
ms.openlocfilehash: 9f5f4b2b069ebc65430fba4bc31a9891ed61fedf
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91450105"
---
# <a name="convert-to-image-directory"></a>Konwertowanie na katalog obrazów

W tym artykule opisano sposób użycia modułu Konwertuj do katalogu obrazów w celu ułatwienia konwersji zestawu danych obrazu na typ danych "Image Directory", który jest standardowym formatem danych w zadaniach związanych z obrazami, takimi jak Klasyfikacja obrazu w programie Azure Machine Learning Designer.

## <a name="how-to-use-convert-to-image-directory"></a>Jak używać konwersji do katalogu obrazów  

1.  Dodaj moduł **Konwertuj do katalogu obrazu** na kanwę. Ten moduł można znaleźć w kategorii "przetwarzanie obrazów/transformacje danych obrazu" na liście modułów. 

2.  Wejście **konwersji do modułu katalogu obrazu** musi być zestawem danych pliku. [Zarejestruj zestaw danych obrazu](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets) i połącz go z portem wejściowym modułu. Upewnij się, że w wejściowym zestawie danych znajduje się obraz. Obecnie projektant nie obsługuje zestawu danych obrazu wizualizacji.
 
    Obsługiwane są następujące formaty zestawu danych:

    - Skompresowany plik w tych rozszerzeniach: "zip", ". tar", ". gz", ". bz2".
    - Folder zawierający obrazy. **Zdecydowanie zaleca się kompresowanie takiego folderu jako pierwszy, a następnie użycie skompresowanego pliku jako zestawu danych**.

    > [!WARNING]
    > **Nie można** użyć modułu **Import danych** w celu zaimportowania zestawu danych obrazu, ponieważ typ danych wyjściowych modułu **importowania danych** jest katalogiem Dataframe, który zawiera tylko ciąg ścieżki pliku.
    

    > [!NOTE]
    > - Jeśli używasz zestawu danych obrazu w nadzorowanej uczenia, musisz określić etykietę zestawu danych szkoleniowych.
    > - W przypadku zadania klasyfikacji obrazu można wygenerować etykietę jako "Kategoria" w danych wyjściowych modułu, jeśli ten zestaw danych obrazu jest zorganizowany w formacie torchvision ImageFolder. W przeciwnym razie tylko obrazy są zapisywane bez etykiety. Poniżej przedstawiono przykład sposobu organizowania zestawu danych obrazu w celu uzyskania etykiety, używania kategorii obrazu jako nazwy podfolderu. 
    > - Nie musisz przekazywać tej samej liczby obrazów w każdym folderze kategorii.
    > - Obrazy z tymi rozszerzeniami (małymi literami) są obsługiwane: "jpg", ". jpeg", ". png", ". ppm", ". bmp", ". PGM", ". tif", ". TIFF", ". WEBP". W jednym folderze można także mieć wiele typów obrazów.    
    > - Aby uzyskać więcej informacji, zobacz [zestawy danych torchvision](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) .
    >
    > ```
    > Your_image_folder_name/Category_1/xxx.png
    > Your_image_folder_name/Category_1/xxy.jpg
    > Your_image_folder_name/Category_1/xxz.jpeg
    >
    > Your_image_folder_name/Category_2/123.png
    > Your_image_folder_name/Category_2/nsdf3.png
    > Your_image_folder_name/Category_2/asd932_.png
    > ```
    > - Jeśli użyjesz zestawu danych obrazu do oceniania, zestaw danych wejściowych plików tego modułu powinien zawierać obrazy niesklasyfikowane.
    
3.  Prześlij potok. Ten moduł można uruchomić na procesorze GPU lub procesorze CPU.

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
