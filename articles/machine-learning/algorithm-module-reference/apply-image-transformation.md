---
title: Stosowanie transformacji obrazów
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak zastosować transformację obrazu do katalogu obrazu przy użyciu modułu Zastosuj transformację obrazu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 05/26/2020
ms.openlocfilehash: a64d5cebfd8e70e2f54a66193a7041c47887c54a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90898916"
---
# <a name="apply-image-transformation"></a>Stosowanie transformacji obrazów 

W tym artykule opisano, jak za pomocą modułu Zastosuj transformację obrazu w programie Azure Machine Learning Designer zmodyfikować katalog obrazu wejściowego na podstawie wcześniej określonej transformacji obrazu.  

Aby określić transformację, należy podłączyć moduł [przekształcenia obrazu init](init-image-transformation.md) , a następnie można zastosować takie przekształcenia do katalogu obrazu wejściowego modułu Zastosuj transformację obrazu.

## <a name="how-to-use-apply-image-transformation"></a>Jak używać transformacji obrazu zastosowania  

1. Dodaj moduł **przekształcenia obrazu** do potoku. Ten moduł można znaleźć w kategorii *Przetwarzanie obrazów/obraz przekształcenia danych* . 

2. Połącz dane wyjściowe **przekształcenia obrazu init** z lewej strony wejściowej **przekształcenia Zastosuj obraz**.

     > [!NOTE]
     > Ten moduł akceptuje tylko przekształcenia obrazu wygenerowane przez moduł [przekształcenia obrazu init](init-image-transformation.md) . W przypadku innego rodzaju transformacji należy połączyć ją, aby [zastosować transformację](apply-transformation.md), w przeciwnym razie zostanie wygenerowany element "InvalidTransformationDirectoryError".


3. Podłącz katalog obrazu, który chcesz przekształcić.

4. W obszarze **tryb** Określ, w jaki sposób ma być używane Przekształcanie danych wejściowych: "dla szkolenia" lub "do wnioskowania". 

   W przypadku wybrania opcji **szkolenia** zostanie zastosowana cała transformacja określona w transformacji obrazu init.

   W przypadku wybrania **do wnioskowania** przekształcenia, takie jak tworzenie nowych próbek, zostaną wykluczone przed ich zastosowaniem. Wynika to z faktu, że operacje przekształcania w celu tworzenia nowych przykładów losowo takich jak "losowe Przerzucanie poziome" są używane do rozszerzania danych w szkole

   > [!NOTE]
   > Przekształcenia, które zostaną wykluczone w trybie **do wnioskowania** , to: losowej uprawy o zmienionym rozmiarze, kadrowanie losowe, losowe Przerzucanie w poziomie, losowe Przerzucanie pionowe, rotacja Losowa, losowo afinicznyma, losowo przeskaling, losowo, losowe wymazywanie

5. Aby zastosować transformację obrazu do nowego katalogu obrazu, Prześlij potok.  

### <a name="module-parameters"></a>Parametry modułu

| Nazwa | Zakres | Typ | Domyślne                   | Opis                              |
| ---- | ----- | ---- | ------------------------- | ---------------------------------------- |
| Tryb | Dowolne   | Tryb | (Wymaganie użytkownika do określenia) | Do czego służy transformacja danych wejściowych. Należy wykluczyć operacje transformacji "Random" w wnioskach, ale zachować je w szkoleniu |

### <a name="expected-inputs"></a>Oczekiwane dane wejściowe  

| Nazwa                       | Typ                    | Opis                       |
| -------------------------- | ----------------------- | --------------------------------- |
| Przekształcanie obrazu wejściowego | TransformationDirectory | Przekształcanie obrazu wejściowego        |
| Katalog obrazu wejściowego      | ImageDirectory          | Katalog obrazu do przekształcenia |

### <a name="outputs"></a>Dane wyjściowe  

| Nazwa                   | Typ           | Opis            |
| ---------------------- | -------------- | ---------------------- |
| Katalog obrazu wyjściowego | ImageDirectory | Katalog obrazu wyjściowego |

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
