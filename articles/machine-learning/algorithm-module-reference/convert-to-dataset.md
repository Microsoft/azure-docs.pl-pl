---
title: 'Konwertuj na zestaw danych: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu Konwertuj do zestawu danych w programie Azure Machine Learning Designer, aby przekonwertować dane wejściowe do formatu wewnętrznego zestawu danych.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 3ba69a6ddf38083e5665e20081f5f909b7100024
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102508490"
---
# <a name="convert-to-dataset"></a>Konwertowanie na zestaw danych

W tym artykule opisano, jak używać modułu Konwertuj do zestawu danych w programie Azure Machine Learning Designer do konwertowania wszelkich danych dla potoku w wewnętrznym formacie projektanta.
  
Konwersja nie jest wymagana w większości przypadków. Azure Machine Learning niejawnie konwertuje dane do formatu natywnego zestawu danych w przypadku wykonywania dowolnej operacji na danych. 

Zalecamy zapisanie danych w formacie zestawu danych, jeśli wykonano pewną normalizację lub czyszczenie zestawu danych i chcesz się upewnić, że zmiany są używane w innych potokach.  
  
> [!NOTE]
> Konwersja na zestaw danych zmienia tylko format danych. Nie zapisuje nowej kopii danych w obszarze roboczym. Aby zapisać zestaw danych, kliknij dwukrotnie port wyjściowy, wybierz pozycję **Zapisz jako zestaw danych** i wprowadź nową nazwę.  
  
## <a name="how-to-use-convert-to-dataset"></a>Jak używać konwersji do zestawu danych  

Zalecamy użycie modułu [Edytowanie metadanych](edit-metadata.md) w celu przygotowania zestawu danych przed użyciem konwersji do zestawu danych. Można dodawać lub zmieniać nazwy kolumn, dopasowywać typy danych i wprowadzać inne zmiany zgodnie z potrzebami.

1.  Dodaj moduł konwersji do zestawu danych do potoku. Ten moduł można znaleźć w kategorii **Przekształcanie danych** w projektancie. 

2. Połącz ją z dowolnym modułem, który wyprowadza zestaw danych.   

    Tak długo, jak dane są [tabelaryczne](/python/api/azureml-core/azureml.data.tabulardataset), można przekonwertować ją na zestaw danych. Obejmuje to dane załadowane przez [dane importu](import-data.md), dane utworzone za poorednictwem [wprowadzania danych ręcznie](enter-data-manually.md)lub zestawy danych przekształcane przez [zastosowanie transformacji](apply-transformation.md).

3.  Z listy rozwijanej **Akcja** wskaż, czy chcesz przeprowadzić oczyszczanie danych przed zapisaniem zestawu danych:  
  
    - **Brak**: Użyj danych jako.  
  
    - **SetMissingValue**: Ustaw określoną wartość na brakującą wartość w zestawie danych. Domyślny symbol zastępczy jest znakiem zapytania (?), ale można użyć niestandardowej opcji  **braku wartości** , aby wprowadzić inną wartość. Jeśli na przykład wprowadzisz **taksówkę** dla **niestandardowej brakującej wartości**, wszystkie **wystąpienia w zestawie** danych zostaną zmienione na brakującą wartość.
  
    - **ReplaceValues**: Użyj tej opcji, aby określić pojedynczą dokładną wartość, która zostanie zamieniona na inną dokładną wartość. Brakujące wartości lub wartości niestandardowe można zastąpić, ustawiając metodę **replace** :

      - **Brak**: Wybierz tę opcję, aby zastąpić brakujące wartości w wejściowym zestawie danych. Dla opcji **Nowa wartość** wprowadź wartość, aby zastąpić brakujące wartości wartościami.
      - **Niestandardowe**: Wybierz tę opcję, aby zastąpić wartości niestandardowe w wejściowym zestawie danych. W polu **wartość niestandardowa** wprowadź wartość, która ma zostać znaleziona. Na przykład, jeśli dane zawierają ciąg `obs` używany jako symbol zastępczy dla brakujących wartości, należy wprowadzić `obs` . Dla **nowej wartości** wprowadź nową wartość, aby zastąpić oryginalny ciąg ciągiem.
  
    Należy pamiętać, że operacja **ReplaceValues** ma zastosowanie tylko do dokładnych dopasowań. Na przykład nie wpłynie to na te ciągi: `obs.` , `obsolete` .  
 
  
5.  Prześlij potok.  

## <a name="results"></a>Wyniki

+  Aby zapisać wynikowy zestaw danych przy użyciu nowej nazwy, wybierz opcję **zarejestruj zestaw danych** na karcie dane **wyjściowe** w prawym panelu modułu.  
  
## <a name="technical-notes"></a>Uwagi techniczne  

-   Każdy moduł, który pobiera zestaw danych jako dane wejściowe, może również pobrać dane z pliku CSV lub TSV. Przed uruchomieniem kodu modułu, dane wejściowe są wstępnie przetwarzane. Przetwarzanie wstępne jest równoznaczne z uruchomieniem modułu konwersji na zestaw danych na wejściu.  
  
-   Nie można przeprowadzić konwersji z formatu SVMLight na zestaw danych.  
  
-   Podczas określania niestandardowej operacji zamiany operacja wyszukiwania i zamieniania dotyczy pełnych wartości. Częściowe dopasowania są niedozwolone. Na przykład można zastąpić 3 z-1 lub 33, ale nie można zastąpić 3 w liczbie dwucyfrowej, takiej jak 35.  
  
-   W przypadku operacji zastąpienia niestandardowego zastąpienie zostanie zakończone niepowodzeniem, jeśli używasz jako zamiennika dowolnego znaku, który nie jest zgodny z bieżącym typem danych kolumny.  

  
## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning.