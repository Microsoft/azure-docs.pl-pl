---
title: 'Dołącz dane: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak za pomocą modułu sprzęgania danych w programie Azure Machine Learning Designer scalić dwa zestawy danych jednocześnie.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: c23dca40d50c5837bd9ff45bc3c3d7fb2581685b
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93420754"
---
# <a name="join-data"></a>Łączenie danych

W tym artykule opisano, jak używać modułu **Join Data** w programie Azure Machine Learning Designer do scalania dwóch zestawów danych przy użyciu operacji łączenia w stylu bazy danych.  

## <a name="how-to-configure-join-data"></a>Jak skonfigurować dane sprzężenia

Aby wykonać sprzężenie na dwóch zestawach danych, powinny one być powiązane z kolumną klucza. Obsługiwane są również klucze złożone używające wielu kolumn. 

1. Dodaj zestawy danych, które chcesz połączyć, a następnie przeciągnij moduł **Dołącz dane** do potoku. 

    Moduł można znaleźć w kategorii **Przekształcanie danych** w obszarze **manipulowanie**.

1. Połącz zestawy danych z modułem **Join Data** . 
 
1. Wybierz pozycję **Uruchom selektor kolumny** , aby wybrać kolumny klucza. Pamiętaj, aby wybrać kolumny dla wejść w lewo i w prawo.

    Dla pojedynczego klucza:

    Wybierz jedną kolumnę klucza dla obu danych wejściowych.
    
    Dla klucza złożonego:

    Zaznacz wszystkie kolumny kluczy od lewej i prawej strony w tej samej kolejności. Moduł **łączenia danych** zostanie dołączony do tabel, gdy wszystkie kolumny klucza są zgodne. Zaznacz opcję **Zezwalaj na duplikowanie i Zachowaj kolejność kolumn w zaznaczeniu** , jeśli kolejność kolumn nie jest taka sama jak oryginalna tabela. 

    ![Selektor kolumn](media/module/join-data-column-selector.png)


1. Wybierz opcję **Uwzględnij wielkość** liter, jeśli chcesz zachować czułość wielkości liter w sprzężeniu kolumny tekstowej. 
   
1. Użyj listy rozwijanej **Typ sprzężenia** , aby określić sposób łączenia zestawów danych.  
  
    * **Sprzężenie wewnętrzne** : *sprzężenie wewnętrzne* jest najpopularniejszą operacją Join. Zwraca połączone wiersze tylko wtedy, gdy wartości kolumn klucza są zgodne.  
  
    * **Lewe sprzężenie zewnętrzne** : *lewe sprzężenie zewnętrzne* zwraca dołączone wiersze dla wszystkich wierszy z tabeli po lewej. Gdy wiersz w lewej tabeli nie ma pasujących wierszy w prawej tabeli, zwracany wiersz zawiera brakujące wartości dla wszystkich kolumn, które pochodzą z prawej tabeli. Możesz również określić wartość zastępczą dla brakujące wartości.  
  
    * **Pełne sprzężenie zewnętrzne** : *pełne sprzężenie zewnętrzne* zwraca wszystkie wiersze z tabeli po lewej ( **Tabela1** ) i z prawej tabeli ( **tabela2** ).  
  
         W przypadku każdego z wierszy w tabeli, które nie mają pasujących wierszy w drugim, wynik zawiera wiersz zawierający brakujące wartości.  
  
    * **Lewe odłączenie** : *lewe odłączenie* zwraca tylko wartości z tabeli po lewej, gdy wartości kolumn klucza są zgodne.  

1. Dla opcji **Zachowaj kolumny klucza prawego w połączonej tabeli** :

    * Wybierz tę opcję, aby wyświetlić klucze z obu tabel wejściowych.
    * Usuń zaznaczenie, aby zwrócić tylko kolumny klucza z lewej strony.

1. Prześlij potok.

1. Aby wyświetlić wyniki, kliknij prawym przyciskiem myszy **dane sprzężenia** i wybierz polecenie **Wizualizuj**.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 