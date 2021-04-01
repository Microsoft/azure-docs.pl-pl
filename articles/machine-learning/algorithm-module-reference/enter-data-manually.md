---
title: 'Wprowadź dane ręcznie: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak za pomocą modułu wprowadź dane ręcznie w Azure Machine Learning utworzyć mały zestaw danych, wpisując wartości. Zestaw danych może mieć wiele kolumn.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 3bff9a91f06649487560faef3ab554b2a3d56af3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "90908077"
---
# <a name="enter-data-manually-module"></a>Wprowadź ręcznie moduł danych

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Użyj modułu **Wprowadź dane ręcznie** , aby utworzyć mały zestaw danych, wpisując wartości. Zestaw danych może mieć wiele kolumn.
  
Ten moduł może być przydatny w scenariuszach takich jak:  
  
- Generowanie małego zestawu wartości do testowania.  
- Tworzenie krótkiej listy etykiet.  
- Wpisywanie listy nazw kolumn do wstawienia do zestawu danych.

## <a name="create-a-dataset"></a>Utwórz zestaw danych 
  
1. Dodaj moduł [Wprowadź dane ręcznie](./enter-data-manually.md) do potoku. Ten moduł można znaleźć w kategorii dane **wejściowe i wyjściowe** w Azure Machine Learning. 
  
1. W polu **Format** danych wybierz jedną z następujących opcji. Te opcje określają sposób, w jaki podane dane powinny być analizowane. Wymagania dotyczące każdego formatu różnią się znacznie, dlatego pamiętaj, aby przeczytać tematy pokrewne.  
  
   - **ARFF**: Format pliku relacji atrybutu używany przez Weka.   
   - **CSV**: format wartości rozdzielanych przecinkami. Aby uzyskać więcej informacji, zobacz [konwertowanie do formatu CSV](./convert-to-csv.md).    
   - **SVMLight**: format używany przez Vowpal Wabbit i inne platformy uczenia maszynowego.    
   - **TSV**: format wartości rozdzielanych znakami tabulacji.

   Jeśli wybierzesz format i nie podasz danych zgodnych ze specyfikacją formatu, wystąpi błąd w czasie wykonywania.
  
1. Kliknij wewnątrz pola tekstowego **dane** , aby rozpocząć wprowadzanie danych. Następujące formaty wymagają szczególnej uwagi:  
  
   - **CSV**: Aby utworzyć wiele kolumn, Wklej w tekście rozdzielanym przecinkami lub wpisz wiele kolumn za pomocą przecinków między polami.
  
     W przypadku wybrania opcji **HasHeader** można użyć pierwszego wiersza wartości jako nagłówka kolumny.  
  
     W przypadku wybrania tej opcji nazwy kolumn (Kol1, Col2 i tak dalej) są używane. Nazwy kolumn można dodawać lub zmieniać później za pomocą polecenia [Edytuj metadane](./edit-metadata.md).  
  
   - **TSV**: Aby utworzyć wiele kolumn, wklej tekst rozdzielany znakami tabulacji lub wpisz wiele kolumn, używając tabulatorów między polami.  
  
     W przypadku wybrania opcji **HasHeader** można użyć pierwszego wiersza wartości jako nagłówka kolumny.  
  
     W przypadku wybrania tej opcji nazwy kolumn (Kol1, Col2 i tak dalej) są używane. Nazwy kolumn można dodawać lub zmieniać później za pomocą polecenia [Edytuj metadane](./edit-metadata.md).  
  
   - **ARFF**: Wklej w istniejącym pliku formatu ARFF. W przypadku wpisywania wartości bezpośrednio należy pamiętać o dodaniu opcjonalnego nagłówka i wymaganych pól atrybutów na początku danych. 

     Na przykład następujące wiersze nagłówka i atrybutu można dodać do prostej listy. Nagłówek kolumny `SampleText` . Należy zauważyć, że typ ciągu nie jest obsługiwany.
    
     ```text
     % Title: SampleText.ARFF  
     % Source: Enter Data module  
     @ATTRIBUTE SampleText NUMERIC  
     @DATA  
     \<type first data row here>  
     ```

   - **SVMLight**: wpisz lub Wklej wartości przy użyciu formatu SVMLight.  
  
     Na przykład poniższy przykład reprezentuje pierwsze kilka wierszy zestawu danych darowizny krwi w formacie SVMLight:  
  
     ```text  
     # features are [Recency], [Frequency], [Monetary], [Time]  
     1 1:2 2:50 3:12500 4:98   
     1 1:0 2:13 3:3250 4:28   
     ```  
  
     Po uruchomieniu modułu [wprowadzanie danych ręcznie](./enter-data-manually.md) te wiersze są konwertowane na zestaw danych kolumn i wartości indeksów w następujący sposób:  
  
     |Col1|Col2|Col3|Col4|Etykiety|  
     |-|-|-|-|-|  
     |0,00016|0,004|0,999961|0,00784|1|  
     |0|0,004|0,999955|0,008615|1|  
  
1. Wybierz klawisz Enter po każdym wierszu, aby rozpocząć nowy wiersz.      
     
   W przypadku wybrania opcji Wprowadź wiele razy, aby dodać wiele pustych wierszy końcowych, puste wiersze zostaną usunięte lub przycięte.  
  
   W przypadku tworzenia wierszy z brakującymi wartościami można zawsze odfiltrować je w późniejszym czasie.  
  
1. Połącz port wyjściowy z innymi modułami i uruchom potok.  
  
   Aby wyświetlić zestaw danych, kliknij prawym przyciskiem myszy moduł i wybierz polecenie **Wizualizuj**.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 