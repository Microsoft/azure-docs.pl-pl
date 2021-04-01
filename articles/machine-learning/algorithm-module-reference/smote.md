---
title: SMOTE
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu SMOTE w Azure Machine Learning, aby zwiększyć liczbę przykładów o niskim poziomie w zestawie danych przy użyciu próbkowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/16/2019
ms.openlocfilehash: 501f3e8946023d28d67a33fbbfca661afbc6306d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "90898284"
---
# <a name="smote"></a>SMOTE

W tym artykule opisano, jak używać modułu SMOTE w programie Azure Machine Learning Designer do zwiększania liczby poddanych wystawianych przypadków w elemencie DataSet używanym do uczenia maszynowego. SMOTE to lepszy sposób zwiększenia liczby rzadkich przypadków niż zwykłe duplikowanie istniejących przypadków.  

Moduł SMOTE można połączyć z *niezrównoważonym* zestawem danych. Istnieje wiele powodów, dla których może być niezrównoważony zestaw danych. Na przykład kategoria, która ma być używana, może być rzadki w populacji lub dane mogą być trudne do zebrania. Zazwyczaj należy używać SMOTE, gdy *Klasa* , która ma zostać poddana analizie, jest niereprezentowana. 
  
Moduł zwraca zestaw danych, który zawiera pierwotne próbki. Zwraca również liczbę syntetycznych próbek mniejszości, w zależności od określonej wartości procentowej.  
  
## <a name="more-about-smote"></a>Więcej informacji na temat SMOTE

Technika syntetycznej odpróbkowania mniejszościowego (SMOTE) jest techniką statystyczną zwiększającą liczbę przypadków w zestawie danych w zrównoważony sposób. Moduł działa, generując nowe wystąpienia z istniejących przypadków mniejszości, które podano jako dane wejściowe. Ta implementacja SMOTE *nie zmienia liczby* większości przypadków.

Nowe wystąpienia nie są jedynie kopiami istniejących przypadków mniejszości. Zamiast tego algorytm pobiera próbki *przestrzeni funkcji* dla każdej klasy docelowej i jej najbliższych sąsiadów. Następnie algorytm generuje nowe przykłady, które łączą funkcje przypadku docelowego z funkcjami jego sąsiadów. Takie podejście zwiększa funkcje dostępne dla każdej klasy i sprawia, że przykłady są bardziej ogólne.
  
SMOTE pobiera cały zestaw danych jako dane wejściowe, ale zwiększa wartość procentową tylko przypadków mniejszościowych. Załóżmy na przykład, że masz niezrównoważony zestaw danych, gdzie tylko 1 procent przypadków ma wartość docelową A (Klasa mniejszości), a 99 procent przypadków ma wartość B. Aby zwiększyć procent przypadków mniejszości do dwóch poprzednich wartości procentowych, należy wprowadzić **200** dla **SMOTE procent** we właściwościach modułu.  
  
## <a name="examples"></a>Przykłady  

Zalecamy użycie SMOTE z małym zestawem danych, aby zobaczyć, jak to działa. W poniższym przykładzie zastosowano zestaw danych darowizny dla krwi dostępny w programie Azure Machine Learning Designer.
  
Jeśli dodasz zestaw danych do potoku i wybierzesz opcję **Wizualizuj** w danych wyjściowych zestawu danych, zobaczysz, że w zestawie danych znajdują się wiersze lub przypadki 748, 570 przypadki (76 procent) są klasy 0, a dla przypadków 178 (24 procent) są klasy 1. Chociaż ten wynik nie jest poważny, Klasa 1 reprezentuje osoby, które oddają krew, więc te wiersze zawierają *przestrzeń funkcji* , którą chcesz modelować.
 
Aby zwiększyć liczbę przypadków, można ustawić wartość **procentu SMOTE** przy użyciu wielokrotności 100 w następujący sposób:

||Klasa 0|Klasa 1|suma|  
|-|-------------|-------------|-----------|  
|Oryginalny zestaw danych<br /><br /> (odpowiednik **wartości procentowej SMOTE**  =  **0**)|570<br /><br /> 76%|178<br /><br /> codzienne|748|  
|**SMOTE procent**  =  **100**|570<br /><br /> 62%|356<br /><br /> 38%|926|  
|**SMOTE procent**  =  **200**|570<br /><br /> 52%|534<br /><br /> 48%|1 104|  
|**SMOTE procent**  =  **300**|570<br /><br /> 44%|712<br /><br /> 56%|1 282|  
  
> [!WARNING]
> Zwiększenie liczby przypadków przy użyciu SMOTE nie gwarantuje tworzenia dokładniejszych modeli. Wypróbuj przetwarzanie potokowe z różnymi wartościami procentowymi, różnymi zestawami funkcji i różnymi liczbami najbliższych sąsiadów, aby zobaczyć, jak dodawanie przypadków ma wpływ na model.  
  
## <a name="how-to-configure-smote"></a>Jak skonfigurować SMOTE
  
1.  Dodaj moduł SMOTE do potoku. Moduł można znaleźć w obszarze **moduły przekształcania danych** w kategorii **manipulowanie** .

2. Połącz zestaw danych, który chcesz zwiększyć. Jeśli chcesz określić miejsce na potrzeby tworzenia nowych przypadków, używając tylko określonych kolumn lub wyłączając niektóre, użyj modułu [SELECT Columns in DataSet (Wybieranie kolumn w zestawie danych](select-columns-in-dataset.md) ). Następnie można wyizolować kolumny, których chcesz użyć przed użyciem SMOTE.
  
    W przeciwnym razie tworzenie nowych przypadków za pośrednictwem SMOTE jest zależne od *wszystkich* kolumn dostarczanych jako dane wejściowe. Co najmniej jedna kolumna kolumn funkcji jest wartością numeryczną.
  
3.  Upewnij się, że wybrano kolumnę, która zawiera etykietę lub klasę docelową. SMOTE akceptuje tylko etykiety binarne.
  
4.  Moduł SMOTE automatycznie identyfikuje klasę mniejszości w kolumnie etykieta, a następnie pobiera wszystkie przykłady dla klasy mniejszości. Wszystkie kolumny nie mogą mieć wartości NaN.
  
5.  W opcji **procent SMOTE** wprowadź liczbę całkowitą, która wskazuje docelowy procent przypadków mniejszościowych w wyjściowym zestawie danych. Na przykład:  
  
    - Wprowadzisz **wartość 0**. Moduł SMOTE zwraca dokładnie ten sam zestaw danych, który został dostarczony jako dane wejściowe. Nie dodaje żadnych nowych przypadków mniejszościowych. W tym zestawie danych proporcja klasy nie uległa zmianie.  
  
    - Wprowadzisz **100**. Moduł SMOTE generuje nowe przypadki mniejszościowe. Dodaje tę samą liczbę przypadków mniejszości, które znajdowały się w oryginalnym zestawie danych. Ponieważ SMOTE nie zwiększa liczby większości przypadków, część przypadków każdej klasy uległa zmianie.  
  
    - Wprowadzisz **200**. Moduł podwaja procent przypadków mniejszościowych w porównaniu z oryginalnym zestawem danych. Nie *powoduje to,* że występuje dwa razy więcej przypadków mniejszości. Zamiast tego rozmiar zestawu danych jest zwiększany w taki sposób, że liczba większości przypadków pozostaje taka sama. Liczba przypadków mniejszościowych zostaje zwiększona do momentu dopasowania do żądanej wartości procentowej.  
  
    > [!NOTE]
    > Użyj tylko wielokrotności 100 dla procentu SMOTE.

6.  Użyj opcji **Liczba najbliższych sąsiadów** , aby określić rozmiar przestrzeni funkcji używanej przez algorytm SMOTE w tworzeniu nowych przypadków. Najbliższy sąsiad jest wierszem danych (przypadku) podobnym do wielkości docelowej. Odległość między dwoma przypadkami jest mierzona przez połączenie wag wektorów wszystkich funkcji.  
  
    + Zwiększając liczbę najbliższych sąsiadów, otrzymujesz funkcje z większej liczby przypadków.
    + Utrzymując liczbę najbliższych sąsiednich sąsiadów, należy używać funkcji, które są bardziej podobne do tych w pierwotnym przykładzie.  
  
7. Wprowadź wartość w polu **losowe inicjatory** , jeśli chcesz zapewnić te same wyniki w przypadku przebiegów tego samego potoku, przy użyciu tych samych danych. W przeciwnym razie moduł generuje losowy inicjator na podstawie wartości zegara procesora podczas wdrażania potoku. Generowanie losowego inicjatora może spowodować nieco inne wyniki w przebiegu.

8. Prześlij potok.  
  
   Wyjście modułu jest zestawem danych, który zawiera pierwotne wiersze oraz liczbę dodanych wierszy z przypadkami mniejszości.  

## <a name="technical-notes"></a>Uwagi techniczne

+ Gdy publikujesz model, który używa modułu **SMOTE** , Usuń **SMOTE** z potoku predykcyjnego przed jego opublikowaniem jako usługą sieci Web. Przyczyną jest to, że SMOTE jest przeznaczona do ulepszania modelu podczas uczenia się, a nie do oceniania. Jeśli opublikowany potok predykcyjny zawiera moduł SMOTE, może wystąpić błąd.

+ Często można uzyskać lepsze wyniki w przypadku czyszczenia brakujących wartości lub zastosowania innych transformacji, aby naprawić dane przed zastosowaniem SMOTE. 

+ Niektórzy pracownicy naukowo zbadali, czy SMOTE jest skuteczna dla danych o dużej wymiarze czy rozrzedzonych, takich jak dane używane w klasyfikacji tekstu lub w zestawach DataSet. Ten dokument zawiera dobre Podsumowanie skutków i teoretyczną prawidłowość stosowania SMOTE w takich przypadkach: [Blagus i Lusa: SMOTE for High-wymiarowe dane z niezrównoważoną klasą](https://bmcbioinformatics.biomedcentral.com/articles/10.1186/1471-2105-14-106).

+ Jeśli SMOTE nie jest skuteczna w zestawie danych, inne podejście, które można rozważyć, to m.in.:
  + Metody umożliwiające Przepróbkowanie przypadków mniejszościowych lub Podpróbkowanie większości przypadków.
  + Metody kompletów, które pomagają Ci uczyć się bezpośrednio przy użyciu klastrów, tworzenia w worku lub dostosowywania adaptacyjnego.


## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 

