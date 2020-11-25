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
ms.date: 10/26/2020
ms.openlocfilehash: a5db3935ae445ee7dcf8129eb1d4c75fcb64302f
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003909"
---
# <a name="apply-transformation-module"></a>Zastosuj moduł transformacji

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Ten moduł służy do modyfikowania wejściowego zestawu danych na podstawie wcześniej obliczonego przekształcenia. Ten moduł jest niezbędny w przypadku konieczności aktualizacji transformacji w potokach wnioskowania.

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
  
1. Aby zastosować transformację do nowego zestawu danych, Prześlij potok.  

> [!IMPORTANT]
> Aby upewnić się, że uaktualniona transformacja potoków szkoleniowych jest również wykonalna w potokach wnioskowania, należy wykonać poniższe kroki za każdym razem, gdy w potoku szkoleniowym jest aktualizowana transformacja:
> 1. W potoku szkoleniowym zarejestruj dane wyjściowe [przekształcenia wybierz kolumny](select-columns-transform.md) jako zestaw danych.
> ![Zarejestruj zestaw danych wyjściowych modułu](media/module/select-columns-transform-register-dataset.png)
> 1. W potoku wnioskowania Usuń element **TD-** module i zastąp go zarejestrowanym zestawem danych w poprzednim kroku.
> ![Zastąp moduł TD](media/module/replace-tranformation-directory.png)

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 