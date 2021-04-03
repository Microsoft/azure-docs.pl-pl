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
ms.date: 11/12/2020
ms.openlocfilehash: 1489ce74da2ecff5212feb5a1a2e3c9151b73424
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "94555587"
---
# <a name="convert-to-image-directory"></a>Konwertowanie na katalog obrazów

W tym artykule opisano sposób użycia modułu Konwertuj do katalogu obrazów w celu ułatwienia konwersji zestawu danych obrazu na typ danych *katalogu obrazu* , który jest standardowym formatem danych w zadaniach związanych z obrazami, takimi jak Klasyfikacja obrazu w programie Azure Machine Learning Designer.

## <a name="how-to-use-convert-to-image-directory"></a>Jak używać konwersji do katalogu obrazów  

1. Najpierw Przygotuj zestaw danych obrazu. 

    W przypadku nadzorowanych szkoleń należy określić etykietę zestawu danych szkoleniowych. Plik zestawu danych obrazu powinien znajdować się w następującej strukturze:
    
    ```
    Your_image_folder_name/Category_1/xxx.png
    Your_image_folder_name/Category_1/xxy.jpg
    Your_image_folder_name/Category_1/xxz.jpeg
    
    Your_image_folder_name/Category_2/123.png
    Your_image_folder_name/Category_2/nsdf3.png
    Your_image_folder_name/Category_2/asd932_.png
    ```
    
    W folderze zestaw danych obrazu istnieje wiele podfolderów. Każdy podfolder zawiera odpowiednio obrazy jednej kategorii. Nazwy podfolderów są traktowane jako etykiety zadań, takich jak Klasyfikacja obrazu. Aby uzyskać więcej informacji, zobacz [zestawy danych torchvision](https://pytorch.org/docs/stable/torchvision/datasets.html#imagefolder) .

    > [!WARNING]
    > Aktualnie etykietowane zestawy danych, które zostały wyeksportowane z etykietowania, nie są obsługiwane w projektancie.

    Obrazy z tymi rozszerzeniami (małymi literami) są obsługiwane: "jpg", ". jpeg", ". png", ". ppm", ". bmp", ". PGM", ". tif", ". TIFF", ". WEBP". W jednym folderze można także mieć wiele typów obrazów. Nie trzeba zawierać tej samej liczby obrazów w każdym folderze kategorii.

    Możesz użyć folderu lub skompresowanego pliku z rozszerzeniem ". zip", ". tar", ". gz" i ". bz2". **Pliki skompresowane są zalecane w celu zapewnienia lepszej wydajności.** 
    
    ![Przykładowy zestaw danych obrazu](./media/module/image-sample-dataset.png)

    > [!NOTE]
    > W przypadku wnioskowania folder zestawu danych obrazu musi zawierać tylko obrazy niesklasyfikowane.

1. [Zarejestrowanie zestawu danych obrazu jako zestawu danych](../how-to-create-register-datasets.md) w obszarze roboczym, ponieważ dane wejściowe modułu Convert to Image Directory muszą być **plikami zestawu danych**.

1. Dodaj zarejestrowany zestaw danych obrazu do kanwy. Swój zarejestrowany zestaw danych można znaleźć w kategorii **zestawy** danych na liście modułów po lewej stronie kanwy. Obecnie projektant nie obsługuje zestawu danych obrazu wizualizacji.

    > [!WARNING]
    > **Nie można** użyć modułu **Import danych** w celu zaimportowania zestawu danych obrazu, ponieważ typ danych wyjściowych modułu **importowania danych** jest katalogiem Dataframe, który zawiera tylko ciąg ścieżki pliku.

1. Dodaj moduł **Konwertuj do katalogu obrazu** na kanwę. Ten moduł można znaleźć w kategorii "przetwarzanie obrazów/transformacje danych obrazu" na liście modułów. Połącz ją z zestawem danych obrazu.
    
3.  Prześlij potok. Ten moduł można uruchomić na procesorze GPU lub procesorze CPU.

## <a name="results"></a>Wyniki

Dane wyjściowe **konwersji do modułu usługi Image Directory** są w formacie **katalogu obrazów** i mogą być połączone z innymi modułami związanymi z obrazami, których format portu wejściowego również jest katalogiem obrazu.

![Konwertuj na dane wyjściowe katalogu obrazu](./media/module/convert-to-image-directory-output.png)

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