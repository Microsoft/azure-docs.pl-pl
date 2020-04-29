---
title: 'Zastosuj transformację: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak za pomocą modułu Zastosuj transformację w Azure Machine Learning zmodyfikować wejściowy zestaw danych na podstawie wcześniej obliczonego przekształcenia.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/05/2020
ms.openlocfilehash: ccf9d0c3eef50c7dfd838f1929e52506e8984879
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78395259"
---
# <a name="apply-transformation-module"></a>Zastosuj moduł transformacji

W tym artykule opisano moduł w programie Azure Machine Learning Designer (wersja zapoznawcza).

Ten moduł służy do modyfikowania wejściowego zestawu danych na podstawie wcześniej obliczonego przekształcenia.

Na przykład jeśli zostały użyte wyniki z przeznaczeniem do normalizacji danych szkoleniowych przy użyciu modułu **normalizowanie danych** , warto użyć wartości z-Score, która została obliczona dla szkolenia w fazie oceniania. W Azure Machine Learning można zapisać metodę normalizacji jako transformację, a następnie za pomocą **zastosowania przekształcenia zastosować** wynikowe dane wejściowe przed oceną.

## <a name="how-to-save-transformations"></a>Jak zapisywać przekształcenia

Projektant umożliwia zapisywanie przekształceń danych jako **zestawów DataSet** , aby można było używać ich w innych potokach.

1. Wybierz moduł transformacji danych, który został pomyślnie uruchomiony.

1. Wybierz kartę dane **wyjściowe + dzienniki** .

1. Wybierz **ikonę Zapisz** , aby zapisać **transformację wyniku**.

## <a name="how-to-use-apply-transformation"></a>Jak używać transformacji zastosowania  
  
1. Dodaj moduł **przekształcenia Zastosuj** do potoku. Ten moduł można znaleźć w sekcji **obliczanie & oceny** w palecie modułów. 
  
1. Znajdź zapisaną transformację, która ma być używana w obszarze **DataSets** > **Moje zestawy danych** w palecie modułów.

1. Połącz dane wyjściowe zapisanej transformacji z lewym portem wejściowym modułu **Zastosuj transformację** .

    Zestaw danych powinien mieć dokładnie ten sam schemat (liczbę kolumn, nazwy kolumn, typy danych) jako zestaw danych, dla którego przekształcenie zostało po raz pierwszy zaprojektowane.  
  
1. Połącz dane wyjściowe zestawu danych z odpowiedniego modułu z właściwym portem wejściowym modułu **Zastosuj transformację** .
  
1. Aby zastosować transformację do nowego zestawu danych, uruchom potok.  

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 