---
title: 'Czyste brakujące dane: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak usunąć brakujące wartości przy użyciu modułu czyste brakujące dane w Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/11/2020
ms.openlocfilehash: 5b7943b2026d640ae7e5d119e165bd752ae2fe7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898821"
---
# <a name="clean-missing-data-module"></a>Wyczyść nieobecny moduł danych

W tym artykule opisano moduł w programie Azure Machine Learning Designer.

Ten moduł służy do usuwania, zastępowania lub wnioskowania brakujących wartości. 

Analityki danych często sprawdzają dane pod kątem brakujących wartości, a następnie wykonują różne operacje, aby naprawić dane lub wstawić nowe wartości. Celem takich operacji czyszczenia jest zapobieganie problemom spowodowanym brakiem danych, które mogą wystąpić podczas uczenia modelu. 

Ten moduł obsługuje wiele typów operacji czyszczenia brakujących wartości, takich jak:

+ Zastępowanie brakujących wartości symbolem zastępczym, średnim lub inną wartością
+ Całkowite usuwanie wierszy i kolumn, które mają brakujące wartości
+ Wnioskowanie wartości na podstawie metod statystycznych


Użycie tego modułu nie powoduje zmiany źródłowego zestawu danych. Zamiast tego tworzy nowy zestaw danych w obszarze roboczym, którego można użyć w kolejnym przepływie pracy. Można również zapisać nowy, oczyszczony zestaw danych do ponownego użycia.

Ten moduł również wyprowadza definicję przekształcenia użytego do oczyszczenia brakujących wartości. Można ponownie użyć tej transformacji na innych zestawach danych, które mają ten sam schemat, przy użyciu modułu [zastosowania przekształcenia](./apply-transformation.md) .  

## <a name="how-to-use-clean-missing-data"></a>Jak używać czystego brakujących danych

Ten moduł umożliwia zdefiniowanie operacji czyszczenia. Możesz również zapisać operację czyszczenia, aby później można było zastosować ją do nowych danych. Zapoznaj się z następującymi sekcjami, jak utworzyć i zapisać proces czyszczenia: 
 
+ [Aby zastąpić brakujące wartości](#replace-missing-values)
  
+ [Aby zastosować transformację czyszczącą do nowych danych](#apply-a-saved-cleaning-operation-to-new-data)
 
> [!IMPORTANT]
> Metoda czyszcząca używana do obsługi brakujących wartości może znacząco wpłynąć na wyniki. Zalecamy, aby eksperymentować z różnymi metodami. Rozważ zarówno uzasadnienie użycia określonej metody, jak i jakość wyników.

### <a name="replace-missing-values"></a>Zamień brakujące wartości  

Za każdym razem, gdy moduł  [czystych danych](./clean-missing-data.md) nie zostanie zastosowany do zestawu danych, ta sama operacja czyszczenia jest stosowana do wszystkich zaznaczonych kolumn. W związku z tym, jeśli chcesz wyczyścić różne kolumny przy użyciu różnych metod, użyj oddzielnych wystąpień modułu.

1.  Dodaj [czysty moduł danych](./clean-missing-data.md) do potoku, a następnie połącz zestaw danych z brakującymi wartościami.  
  
2.  Aby **wyczyścić kolumny**, wybierz kolumny zawierające brakujące wartości, które chcesz zmienić. Można wybrać wiele kolumn, ale należy użyć tej samej metody zastępczej we wszystkich zaznaczonych kolumnach. W związku z tym zazwyczaj należy czyścić kolumny ciągów i kolumny liczbowe osobno.

    Na przykład, aby sprawdzić brakujące wartości we wszystkich kolumnach liczbowych:

    1. Wybierz **czysty moduł danych** , a następnie kliknij pozycję **Edytuj kolumnę** w prawym panelu modułu.

    3. W polu **Uwzględnij** wybierz opcję **typy kolumn** z listy rozwijanej, a następnie wybierz wartość **numeryczną**. 
  
    Wszystkie wybrane metody czyszczenia lub zamiany muszą być stosowane do **wszystkich** kolumn w zaznaczeniu. Jeśli dane w dowolnej kolumnie są niezgodne z określoną operacją, moduł zwróci błąd i zatrzyma potok.
  
3.  Aby uzyskać **minimalny współczynnik braku wartości**, Określ minimalną liczbę brakujących wartości wymaganych do wykonania operacji.  
  
    Tej opcji należy użyć w połączeniu z **maksymalnym współczynnikem braku wartości** , aby zdefiniować warunki, w których operacja czyszczenia jest wykonywana na zestawie danych. Jeśli istnieje zbyt wiele lub zbyt mało wierszy, w których brakuje wartości, nie można wykonać operacji. 
  
    Wprowadzony numer reprezentuje **stosunek** brakujących wartości do wszystkich wartości w kolumnie. Domyślnie właściwość **współczynnik braku wartości** jest równa 0. Oznacza to, że brakujące wartości są czyszczone nawet wtedy, gdy istnieje tylko jedna wartość. 

    > [!WARNING]
    > Ten warunek musi być spełniony przez każdą kolumnę, aby można było zastosować określoną operację. Załóżmy na przykład, że wybrano trzy kolumny, a następnie ustawisz minimalny współczynnik brakujących wartości .2 (20%), ale tylko jedna kolumna ma w rzeczywistości 20% brakujących wartości. W takim przypadku operacja oczyszczania będzie miała zastosowanie tylko do kolumny z ponad 20% brakującymi wartościami. W związku z tym inne kolumny byłyby niezmienione.
    > 
    > Jeśli masz jakiekolwiek wątpliwości dotyczące tego, czy brakujące wartości zostały zmienione, zaznacz opcję, **Generuj kolumnę wskaźnik braku wartości**. Kolumna jest dołączana do zestawu danych, aby wskazać, czy każda kolumna spełnia określone kryteria dla minimalnych i maksymalnych zakresów.  
  
4. W przypadku **maksymalnego współczynnika braku wartości** Określ maksymalną liczbę brakujących wartości, które mogą być obecne dla operacji do wykonania.   
  
    Na przykład może być konieczne wykonanie podstawienia braku wartości tylko wtedy, gdy 30% lub mniej wierszy zawiera brakujące wartości, ale pozostaw wartości jako-is, jeśli w więcej niż 30% wierszy brakuje wartości.  
  
    Należy zdefiniować liczbę jako stosunek brakujących wartości do wszystkich wartości w kolumnie. Domyślnie **maksymalny współczynnik braku wartości** jest ustawiony na 1. Oznacza to, że brakujące wartości są czyszczone nawet wtedy, gdy brakuje 100% wartości w kolumnie.  
  
   
  
5. W obszarze **Tryb czyszczenia** wybierz jedną z następujących opcji zastępowania lub usuwania brakujących wartości:  
  
  
    + **Niestandardowa wartość podstawienia**: Użyj tej opcji, aby określić wartość symbolu zastępczego (na przykład 0 lub na), która ma zastosowanie do wszystkich brakujących wartości. Wartość określona jako zamiennik musi być zgodna z typem danych kolumny.
  
    + **Zamień na wartość średnia**: oblicza średnią kolumny i używa średniej jako wartości zastępczej dla każdej brakującej wartości w kolumnie.  
  
        Dotyczy tylko kolumn, które mają typ danych Integer, Double lub Boolean.  
  
    + **Zamień na medianę**: oblicza wartość mediany kolumny i używa wartości mediana jako zamiennika dla dowolnej brakującej wartości w kolumnie.  
  
        Dotyczy tylko kolumn, które mają typy danych Integer lub Double. 
  
    + **Zamień na tryb**: oblicza tryb dla kolumny i używa trybu jako wartości zastępczej dla każdej brakującej wartości w kolumnie.  
  
        Dotyczy kolumn, które mają typy danych Integer, Double, Boolean lub kategorii. 
  
    + **Usuń cały wiersz**: całkowicie usuwa wszystkie wiersze w zestawie danych, które mają co najmniej jedną brakującą wartość. Jest to przydatne, jeśli brakująca wartość może być uważana za losową.  
  
    + **Usuń całą kolumnę**: całkowicie usuwa wszystkie kolumny w zestawie danych, które mają co najmniej jedną brakującą wartość.  
  
    
  
6. **Wartość zastępcza** opcji jest dostępna w przypadku wybrania opcji, **niestandardowej wartości podstawienia**. Wpisz nową wartość, która będzie używana jako wartość zastępcza dla wszystkich brakujących wartości w kolumnie.  
  
    Należy pamiętać, że można użyć tej opcji tylko w kolumnach, które mają liczbę całkowitą, podwójną, logiczną lub ciąg.
  
7. **Generuj kolumnę wskaźnika brakującej wartości**: zaznacz tę opcję, jeśli chcesz, aby dane wyjściowe wskazywały, czy wartości w kolumnie spełniały kryteria czyszczenia wartości brakujące. Ta opcja jest szczególnie przydatna podczas konfigurowania nowej operacji czyszczenia i upewnienia się, że działa ona zgodnie z założeniami.
  
8. Prześlij potok.

### <a name="results"></a>Wyniki

Moduł zwraca dwa dane wyjściowe:  

-   **Oczyszczony zestaw danych**: zestaw danych składający się z wybranych kolumn, z brakującymi wartościami obsługiwanymi jako określone, wraz z kolumną wskaźnika, jeśli wybrano tę opcję.  

    Kolumny niewybrane do czyszczenia są również "przenoszone przez".  
  
-  **Przekształcanie transformacji**: transformacja danych służąca do czyszczenia, która może zostać zapisana w obszarze roboczym i zastosowana do nowych danych później.

### <a name="apply-a-saved-cleaning-operation-to-new-data"></a>Stosowanie zapisanej operacji czyszczenia do nowych danych  

Jeśli trzeba często powtarzać operacje czyszczenia, zalecamy zapisanie przepisu do czyszczenia danych w formie *przekształcenia*, aby ponownie użyć tego samego zestawu danych. Zapisywanie transformacji czyszczącej jest szczególnie przydatne, jeśli konieczne jest częste ponowne importowanie, a następnie czyszczenie danych, które mają ten sam schemat.  
      
1.  Dodaj moduł [przekształcenia Zastosuj](./apply-transformation.md) do potoku.  
  
2.  Dodaj zestaw danych, który chcesz wyczyścić, i Połącz zestaw danych z portem wejściowym z prawej strony.  
  
3.  Rozwiń grupę **przekształcenia** w okienku po lewej stronie projektanta. Znajdź zapisaną transformację i przeciągnij ją do potoku.  

4.  Połącz zapisaną transformację z lewym portem wejściowym [zastosowania transformacji](./apply-transformation.md). 

    W przypadku zastosowania zapisanej transformacji nie można wybrać kolumn, do których zastosowano transformację. Wynika to z faktu, że transformacja została już zdefiniowana i automatycznie stosowana do kolumn określonych w pierwotnej operacji.

    Załóżmy jednak, że utworzono transformację dla podzestawu kolumn liczbowych. Można zastosować tę transformację do zestawu danych typów kolumn mieszanych bez zgłaszania błędu, ponieważ brakujące wartości są zmieniane tylko w pasujących kolumnach liczbowych.

6.  Prześlij potok.  

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 