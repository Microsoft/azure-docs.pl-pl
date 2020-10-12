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
ms.date: 06/05/2020
ms.openlocfilehash: 7573abbbee479bfb0d1710beba3b95d084a5e657
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90898872"
---
# <a name="apply-transformation-module"></a>Zastosuj moduł transformacji

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Ten moduł służy do modyfikowania wejściowego zestawu danych na podstawie wcześniej obliczonego przekształcenia.

Na przykład jeśli zostały użyte wyniki z przeznaczeniem do normalizacji danych szkoleniowych przy użyciu modułu **normalizowanie danych** , warto użyć wartości z-Score, która została obliczona dla szkolenia w fazie oceniania. W Azure Machine Learning można zapisać metodę normalizacji jako transformację, a następnie za pomocą **zastosowania przekształcenia zastosować** wynikowe dane wejściowe przed oceną.

## <a name="how-to-save-transformations"></a>Jak zapisywać przekształcenia

Projektant umożliwia zapisywanie przekształceń danych jako **zestawów DataSet** , aby można było używać ich w innych potokach.

1. Wybierz moduł transformacji danych, który został pomyślnie uruchomiony.

1. Wybierz kartę dane **wyjściowe + dzienniki** .

1. Znajdź dane wyjściowe transformacji i wybierz pozycję **zarejestruj zestaw danych** , aby go zapisać jako moduł w kategorii **zestawy danych** w palecie modułów.

## <a name="how-to-use-apply-transformation"></a>Jak używać transformacji zastosowania  
  
1. Dodaj moduł **przekształcenia Zastosuj** do potoku. Ten moduł można znaleźć w sekcji **obliczanie & oceny** w palecie modułów. 
  
1. Znajdź zapisaną transformację, która ma być używana w obszarze **zestawy danych** w palecie modułów.

1. Połącz dane wyjściowe zapisanej transformacji z lewym portem wejściowym modułu **Zastosuj transformację** .

    Zestaw danych powinien mieć dokładnie ten sam schemat (liczbę kolumn, nazwy kolumn, typy danych) jako zestaw danych, dla którego przekształcenie zostało po raz pierwszy zaprojektowane.  
  
1. Połącz dane wyjściowe zestawu danych z odpowiedniego modułu z właściwym portem wejściowym modułu **Zastosuj transformację** .
  
1. Aby zastosować transformację do nowego zestawu danych, uruchom potok.  

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 