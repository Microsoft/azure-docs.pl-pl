---
title: Stosowanie operacji matematycznej
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak używać modułu operacji matematycznej w Azure Machine Learning, aby zastosować operację matematyczną do wartości kolumn w potoku.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/09/2019
ms.openlocfilehash: cc2afb2b29c69e9687dbd3b194b6a1626d4131b3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105625198"
---
# <a name="apply-math-operation"></a>Stosowanie operacji matematycznej

W tym artykule opisano moduł programu Azure Machine Learning Designer.

Użyj operacji stosowania matematycznego, aby utworzyć obliczenia, które są stosowane do kolumn liczbowych w wejściowym zestawie danych. 

Operacje matematyczne obejmują funkcje arytmetyczne, funkcje trygonometryczne, funkcje zaokrąglania i funkcje specjalne używane w nauce danych, takie jak gamma i funkcje błędów.  

Po zdefiniowaniu operacji i uruchomieniu potoku wartości są dodawane do zestawu danych. W zależności od sposobu konfigurowania modułu można:

+ Dołącz wyniki do zestawu danych (przydatne podczas weryfikowania wyniku operacji).
+ Zamień wartości kolumn na nowe, obliczone wartości.
+ Wygeneruj nową kolumnę dla wyników i nie wyświetlaj oryginalnych danych. 

Poszukaj potrzebnych operacji w następujących kategoriach:  

- [Podstawowa](#basic-math-operations)  
  
     Funkcje w kategorii **Basic** mogą służyć do manipulowania pojedynczą wartością lub kolumną wartości. Na przykład możesz uzyskać wartość bezwzględną wszystkich liczb w kolumnie lub obliczyć pierwiastek kwadratowy dla każdej wartości w kolumnie.  
  
-   [Porównaj](#comparison-operations)  
  
      Funkcje w kategorii **porównywania** są używane do porównania: można wykonać porównanie par wartości w dwóch kolumnach lub porównać każdą wartość w kolumnie z określoną stałą. Na przykład można porównać kolumny, aby określić, czy wartości były takie same w dwóch zestawach danych. Lub można użyć stałej, na przykład maksymalnej dozwolonej wartości, aby znaleźć elementy odstające w kolumnie liczbowej.  
  
-   [Operacje](#arithmetic-operations)  
  
     Kategoria **operacje** zawiera podstawowe funkcje matematyczne: Dodawanie, odejmowanie, mnożenie i dzielenie. Można korzystać z kolumn lub stałych. Na przykład możesz dodać wartość w kolumnie A do wartości w kolumnie B. Lub można odjąć stałą, taką jak wcześniej obliczona średnia, z każdej wartości w kolumnie A.  
  
-   [Zaokrąglanie](#rounding-operations)  
  
     Kategoria **zaokrąglania** obejmuje wiele funkcji służących do wykonywania operacji, takich jak zaokrąglanie, sufity, piętro i obcinanie na różne poziomy dokładności. Można określić poziom dokładności dla liczby dziesiętnej i całkowitej.  
  
-   [Jako](#special-math-functions)  
  
     Kategoria **specjalna** obejmuje funkcje matematyczne, które są szczególnie stosowane w nauce danych, takie jak całki eliptyczna i funkcja błędów gaussowskie.  
  
-   [Trygonometrycznych](#trigonometric-functions)  
  
     Kategoria **trygonometryczna** zawiera wszystkie standardowe funkcje trygonometryczne. Na przykład można skonwertować radiany na stopnie lub funkcje obliczeniowe, takie jak tangens w radianach lub stopniach.
     Te funkcje są jednoargumentowe, co oznacza, że przyjmują jedną kolumnę wartości jako dane wejściowe, stosują funkcję trygonometryczną i zwracają kolumnę wartości jako wynik. Upewnij się, że kolumna wejściowa jest odpowiednim typem i zawiera poprawny typ wartości dla określonej operacji.   

## <a name="how-to-configure-apply-math-operation"></a>Jak skonfigurować stosowanie operacji matematycznych  

Moduł **operacji Zastosuj matematyczny** wymaga zestawu danych, który zawiera co najmniej jedną kolumnę zawierającą tylko liczby. Liczby mogą być dyskretne lub ciągłe, ale muszą mieć typ danych liczbowych, a nie ciąg.

Tę samą operację można zastosować do wielu kolumn liczbowych, ale wszystkie kolumny muszą znajdować się w tym samym zestawie danych. 

Każde wystąpienie tego modułu może wykonywać tylko jeden typ operacji jednocześnie. Aby wykonywać złożone operacje matematyczne, może być konieczne przetworzenie łańcucha w wielu wystąpieniach modułu **zastosowania operacji matematycznych** .  
  
1.  Dodaj moduł **operacji matematycznej** do potoku.

1. Połącz zestaw danych, który zawiera co najmniej jedną kolumnę liczbową.  

1.  Wybierz co najmniej jedną kolumnę źródłową, w której ma zostać wykonane obliczenie.   
  
    - Każda wybrana kolumna musi być typem danych liczbowych. 
    - Zakres danych musi być prawidłowy dla wybranej operacji matematycznej. W przeciwnym razie może wystąpić błąd lub NaN (nie liczba). Na przykład ln (-1,0) jest nieprawidłową operacją, a wynikiem jest wartość `NaN` .
  
1.  Wybierz **kategorię** , aby wybrać **Typ** operacji matematycznej do wykonania.
    
1. Wybierz określoną operację z listy w tej kategorii.
  
1.  Ustaw dodatkowe parametry wymagane przez poszczególne typy operacji.  
  
1.  Użyj opcji **Tryb danych wyjściowych** , aby wskazać, w jaki sposób ma zostać wygenerowana operacja matematyczna: 

    - **Dołącz**. Wszystkie kolumny używane jako dane wejściowe są uwzględniane w wyjściowym zestawie danych, a jedna dodatkowa kolumna jest dołączana, która zawiera wyniki operacji matematycznej.
    - W **miejscu**. Wartości w kolumnach używanych jako dane wejściowe są zamieniane na nowe obliczone wartości. 
    - **ResultOnly**. Zwracana jest pojedyncza kolumna zawierająca wyniki operacji matematycznej.
  
1.  Prześlij potok.  
  
## <a name="results"></a>Wyniki

W przypadku generowania wyników przy użyciu opcji **Dołącz** lub **ResultOnly** nagłówki kolumn zwracanego zestawu danych wskazują operację i używane kolumny. Na przykład w przypadku porównania dwóch kolumn za pomocą operatora **Equals** wyniki będą wyglądać następująco:  
  
-   **Równa się (Col2_Col1)**, co oznacza, że przetestowano Col2 z Kol1.  
-   **Równa się (Col2_ $10)**, wskazujący, że kolumna 2 została porównana z stałą 10.  

Nawet jeśli używasz opcji **w miejscu** , dane źródłowe nie zostaną usunięte ani zmienione; kolumna w oryginalnym zestawie danych jest nadal dostępna w projektancie. Aby wyświetlić oryginalne dane, można połączyć moduł [Dodaj kolumny](add-columns.md) i dołączyć go do danych wyjściowych **operacji Zastosuj matematyczne**.  
    
## <a name="basic-math-operations"></a>Podstawowe operacje matematyczne 

Funkcje w kategorii **podstawowa** zazwyczaj przyjmują jedną wartość z kolumny, wykonują wstępnie zdefiniowaną operację i zwracają pojedynczą wartość. W przypadku niektórych funkcji można określić stałą lub zestaw kolumn jako drugi argument.  
  
 Azure Machine Learning obsługuje następujące funkcje w kategorii **podstawowa** :  

### <a name="abs"></a>Abs

Zwraca wartość bezwzględną zaznaczonych kolumn.  
  
### <a name="atan2"></a>Atan2

Zwraca arcus tangens z czterema ćwiartkami.  

Wybierz kolumny zawierające współrzędne punktu. Dla drugiego argumentu, który odnosi się do współrzędnej x, można również określić stałą.  

Odnosi się do funkcji ATAN2 w programie MATLAB.  

### <a name="conj"></a>Conj

Zwraca wartość koniugat dla wartości z zaznaczonej kolumny.  

### <a name="cuberoot"></a>CubeRoot

Oblicza pierwiastek modułu dla wartości z zaznaczonej kolumny.  

### <a name="doublefactorial"></a>DoubleFactorial  
 Oblicza podwójną silnię dla wartości w zaznaczonej kolumnie. Podwójna silnia jest rozszerzeniem normalnej funkcji silni i jest oznaczona jako `x!!` .  

### <a name="eps"></a>Formacie

Zwraca rozmiar przerwy między bieżącą wartością a numerem następnego lub podwójnej precyzji. Odnosi się do funkcji EPS w programie MATLAB.  
  
### <a name="exp"></a>Exp

Zwraca liczbę e podniesioną do potęgi wartości z zaznaczonej kolumny. Ta funkcja jest taka sama jak funkcja EXP programu Excel.  

### <a name="exp2"></a>Exp2 —

Zwraca wartość typu wykładniczego 2 dla argumentów, co oznacza wartość y = x * 2<sup>t</sup> , gdzie t jest kolumną wartości zawierających wykładniki.  

W obszarze  **zestaw kolumn** wybierz kolumnę zawierającą wartości wykładnika t.

Dla **exp2 —** można określić drugi argument x, który może być stałą lub inną kolumną wartości. W **drugim typie argumentu** wskaż, czy jako stałą ma być udostępniany współczynnik x, czy wartość w kolumnie.  

Na przykład w przypadku wybrania kolumny z wartościami {0,1,2,3,4,5} dla mnożnika i wykładnika funkcja zwraca {0, 2, 8, 24, 64 160).  

### <a name="expminus1"></a>ExpMinus1 

Zwraca wykładnik ujemny dla wartości w zaznaczonej kolumnie.  

### <a name="factorial"></a>Silnia
Zwraca silnię dla wartości w zaznaczonej kolumnie.  

### <a name="hypotenuse"></a>Przeciwprostokątnej
Oblicza przeciwprostokątnej dla trójkąta, w którym długość jednej strony jest określona jako kolumna wartości, a długość drugiej strony jest określana jako stała lub dwie kolumny.  

### <a name="ln"></a>Logarytm naturalny

Zwraca logarytm naturalny dla wartości z zaznaczonej kolumny.  

### <a name="lnplus1"></a>LnPlus1

Zwraca logarytm naturalny plus jeden dla wartości w zaznaczonej kolumnie.  

### <a name="log"></a>Dziennik

Zwraca dziennik wartości z zaznaczonej kolumny, z uwzględnieniem określonej podstawy.  

Można określić wartość bazową (drugi argument) jako stałą lub wybierając inną kolumnę wartości.  

### <a name="log10"></a>Log10

Zwraca podstawowe wartości 10-logarytmu dla zaznaczonej kolumny.  

### <a name="log2"></a>Log2 —

Zwraca podstawowe 2-wartości logarytmu dla zaznaczonej kolumny.  

### <a name="nthroot"></a>NthRoot
Zwraca n-ty z wartości przy użyciu określonej n.  

Wybierz kolumny, dla których chcesz obliczyć katalog główny przy użyciu opcji **zestawem kolumn** .  

W **drugim typie argumentu** wybierz inną kolumnę, która zawiera element główny, lub określ stałą, która ma być używana jako element główny.  

Jeśli drugi argument jest kolumną, każda wartość w kolumnie jest używana jako wartość n dla odpowiadającego wiersza. Jeśli drugi argument jest stałą, wpisz wartość n w drugim polu tekstowym **argumentu** .
### <a name="pow"></a>Pow

Oblicza wartość X podniesioną do potęgi Y dla każdej wartości w zaznaczonej kolumnie.  

Najpierw wybierz kolumny zawierające **bazę**, która powinna być zmiennoprzecinkowa, przy użyciu opcji **zestawem kolumn** .  

W **drugim typie argumentu** wybierz kolumnę zawierającą wykładnik lub określ stałą, która ma być używana jako wykładnik.  

Jeśli drugi argument jest kolumną, każda wartość w kolumnie jest używana jako wykładnik odpowiadającego wiersza. Jeśli drugi argument jest stałą, wpisz wartość wykładnika w drugim polu tekstowym **argumentu** .  

### <a name="sqrt"></a>Sqrt

Zwraca pierwiastek kwadratowy wartości z zaznaczonej kolumny.  

### <a name="sqrtpi"></a>SqrtPi

Dla każdej wartości w zaznaczonej kolumnie mnoży wartość przez Pi, a następnie zwraca pierwiastek kwadratowy wyniku.  

### <a name="square"></a>Square

Kwadratuje wartości w zaznaczonej kolumnie.  

## <a name="comparison-operations"></a>Operacje porównania  

Użyj funkcji porównania w projektancie Azure Machine Learning w dowolnym momencie, gdy konieczne jest przetestowanie dwóch zestawów wartości względem siebie. Na przykład w potoku może być konieczne wykonanie następujących operacji porównania:  

- Oceń kolumnę modelu wyników prawdopodobieństwa względem wartości progowej.
- Ustal, czy dwa zestawy wyników są takie same. Dla każdego wiersza, który jest inny, należy dodać flagę FALSE, która może być używana do dalszej przetwarzania lub filtrowania.  

### <a name="equalto"></a>EqualTo

Zwraca wartość true, jeśli wartości są takie same.  

### <a name="greaterthan"></a>GreaterThan

Zwraca wartość true, jeśli wartości w **zestawie kolumn** są większe niż określona stała lub większa niż odpowiadające wartości w kolumnie porównania.  

### <a name="greaterthanorequalto"></a>GreaterThanOrEqualTo

Zwraca wartość true, jeśli wartości w **zestawie kolumn** są większe lub równe określonej stałej lub większe lub równe odpowiadających wartościom w kolumnie porównania.  

### <a name="lessthan"></a>LessThan

Zwraca wartość true, jeśli wartości w **zestawie kolumn** są mniejsze niż określona stała lub mniejsze niż odpowiednie wartości w kolumnie porównania.  
  
### <a name="lessthanorequalto"></a>LessThanOrEqualTo

Zwraca wartość true, jeśli wartości w **zestawie kolumn** są mniejsze lub równe określonej stałej lub mniejszej lub równej odpowiadającej wartości w kolumnie porównania.  

### <a name="notequalto"></a>NotEqualTo

Zwraca wartość true, jeśli wartości w **zestawie kolumn** nie są równe kolumnie stałej lub porównania, i zwraca wartość false, jeśli są równe.  

### <a name="pairmax"></a>PairMax

Zwraca wartość, która jest większa — wartość w **zestawie kolumn** lub wartość w kolumnie stała lub porównania.  

### <a name="pairmin"></a>PairMin

Zwraca wartość, która jest mniejsza — wartość w **zestawie kolumn** lub wartość w kolumnie stała lub kolumna porównania.  
  
##  <a name="arithmetic-operations"></a>Operacje arytmetyczne   

Obejmuje podstawowe operacje arytmetyczne: Dodawanie i odejmowanie, dzielenie i mnożenie.  Ponieważ większość operacji jest binarnych, co wymaga dwóch liczb, należy najpierw wybrać operację, a następnie wybrać kolumnę lub liczby do użycia w pierwszym i drugim argumentach.

Kolejność dzielenia i odejmowania jest następująca: 
- Odejmij (Arg1_Arg2) = arg1-ARG 2
- Dzielenie (Arg1_Arg2) = arg1/ARG 2

W poniższej tabeli przedstawiono kilka przykładów

Operacja|Num1|Num2|Kolumna wyników|Wartość wyniku|
----|----|----|----|----
|Znak dodawania|1|5|Dodaj (Num2_Num1)| 6|
|Znak mnożenia|1|5|Wiele (Num2_Num1)|5|
|Odejmowanie|5|1|Odejmij (Num2_Num1)|4|
|Odejmowanie|0|1|Odejmij (Num2_Num1)|-1|
|Dział|5|1|Podziel (Num2_Num1)|5|
|Dział|1|0|Podziel (Num2_Num1)|Nieskończoność|

### <a name="add"></a>Dodaj

Określ kolumny źródłowe za pomocą **zestawu kolumn**, a następnie Dodaj do tych wartości liczbę określoną w **drugim argumencie**.  

Aby dodać wartości w dwóch kolumnach, wybierz kolumnę lub kolumny przy użyciu **zestawu kolumn**, a następnie wybierz drugą kolumnę przy użyciu **drugiego argumentu**.  

### <a name="divide"></a>Dzielenie

Dzieli wartości w **kolumnie ustawionych** przez stałą lub przez wartości kolumn zdefiniowane w **drugim argumencie**.  Inaczej mówiąc, najpierw wybierasz dzielnik, a następnie dzielną. Wartość wyjściowa jest ilorazem.

### <a name="multiply"></a>Mnożenie

Mnoży wartości w **kolumnie ustawionych** przez określone wartości stałej lub kolumny.  

### <a name="subtract"></a>Odejmowanie

Określ kolumnę wartości, na które mają być wykonywane operacje ( *odjemna*), wybierając inną kolumnę przy użyciu opcji **zestawu kolumn** . Następnie określ liczbę, która ma zostać odjęta ( *odjemnik*) przy użyciu **drugiego** listy rozwijanej. Można wybrać stałą lub kolumnę wartości.

##  <a name="rounding-operations"></a>Operacje zaokrąglania 

Program Azure Machine Learning Designer obsługuje różne operacje zaokrąglania. W przypadku wielu operacji należy określić liczbę dokładności, która ma być używana podczas zaokrąglania. Można użyć poziomu dokładności statycznej, określonego jako stała lub można zastosować dynamiczną wartość precyzji uzyskaną z kolumny wartości.  

- Jeśli używasz stałej, ustaw dla opcji **Typ dokładności** wartość **stała** , a następnie wpisz liczbę cyfr jako liczbę całkowitą w polu tekstowym **dokładność stałej** . Jeśli wpiszesz wartość inną niż Integer, moduł nie zgłasza błędu, ale wyniki mogą być nieoczekiwane.  

- Aby użyć innej wartości dokładności dla każdego wiersza w zestawie danych, ustaw dla opcji **typ precyzji** wartość **zestawem kolumn**, a następnie wybierz kolumnę zawierającą odpowiednie wartości dokładności.  

### <a name="ceiling"></a>Ceiling

Zwraca górny limit wartości w **zestawie kolumn**.  

### <a name="ceilingpower2"></a>CeilingPower2

Zwraca kwadratowy pułap dla wartości w **zestawie kolumn**.  

### <a name="floor"></a>Floor

Zwraca piętro dla wartości w **zestawie kolumn** do określonej precyzji.  

### <a name="mod"></a>Mod

Zwraca część ułamkową wartości w **zestawie kolumn** do określonej precyzji.  

### <a name="quotient"></a>Iloraz

Zwraca część ułamkową wartości w **zestawie kolumn** do określonej precyzji.  

### <a name="remainder"></a>Reszta

Zwraca resztę dla wartości w **zestawie kolumn**.  

### <a name="rounddigits"></a>RoundDigits

Zwraca wartości w **zestawie kolumn** zaokrąglone według reguły 4/5 do określonej liczby cyfr.  

### <a name="rounddown"></a>RoundDown

Zwraca wartości w **zestawie kolumn** zaokrąglone w dół do określonej liczby cyfr.  

### <a name="roundup"></a>RoundUp

Zwraca wartości w **zestawie kolumn** zaokrąglone do określonej liczby cyfr.  

### <a name="toeven"></a>ToEven

Zwraca wartości w **zestawie kolumn** zaokrąglone do najbliższej całkowitej liczby parzystej.  

### <a name="toodd"></a>ToOdd

Zwraca wartości w **zestawie kolumn** zaokrąglone do najbliższej całkowitej, nieparzystej liczby.  

### <a name="truncate"></a>Truncate

Obcina wartości w **kolumnie ustawionych** przez usunięcie wszystkich cyfr niedozwolonych przez określoną precyzję.  
  
## <a name="special-math-functions"></a>Specjalne funkcje matematyczne

Ta kategoria obejmuje wyspecjalizowane funkcje matematyczne często używane w nauce danych. O ile nie zaznaczono inaczej, funkcja jest Jednoargumentowa i zwraca określone obliczenie dla każdej wartości w zaznaczonej kolumnie lub kolumnach.  

### <a name="beta"></a>Beta

Zwraca wartość funkcji beta Euler.  

### <a name="ellipticintegrale"></a>EllipticIntegralE
Zwraca wartość niekompletnej całki eliptycznej.  
  

### <a name="ellipticintegralk"></a>EllipticIntegralK

Zwraca wartość kompletnej całki eliptycznej (KB).  

### <a name="erf"></a>Funkcja

Zwraca wartość funkcji błędu.  

Funkcja Error (nazywana również funkcją błędu Gaussa) jest specjalną funkcją kształtu sigmoid, która jest używana w prawdopodobieństwu do opisywania dyfuzji.  

### <a name="erfc"></a>ERFC —

Zwraca wartość komplementarnej funkcji błędu.  

`Erfc` jest definiowana jako 1 – ERF (x).  

### <a name="erfscaled"></a>ErfScaled

Zwraca wartość funkcji skalowanego błędu.  

Przeskalowana wersja funkcji Error może służyć do uniknięcia niedopełnienia arytmetycznego.  

### <a name="erfinverse"></a>ErfInverse

Zwraca wartość funkcji odwrotnej `erf` .  

### <a name="exponentialintegralein"></a>ExponentialIntegralEin

Zwraca wartość wykładniczej wartości całkowitej EI.  

### <a name="gamma"></a>Gamma

Zwraca wartość funkcji gamma.  

### <a name="gammaln"></a>Rozkład

Zwraca logarytm naturalny funkcji gamma.  

### <a name="gammaregularizedp"></a>GammaRegularizedP

Zwraca wartość zwykłej niekompletnej funkcji gamma.  

Ta funkcja przyjmuje drugi argument, który może być dostarczony jako stała lub kolumna wartości.  

### <a name="gammaregularizedpinverse"></a>GammaRegularizedPInverse

Zwraca wartość odwrotnej, niekompletnej funkcji gamma.  

Ta funkcja przyjmuje drugi argument, który może być dostarczony jako stała lub kolumna wartości.  

### <a name="gammaregularizedq"></a>GammaRegularizedQ  

Zwraca wartość zwykłej niekompletnej funkcji gamma.  

Ta funkcja przyjmuje drugi argument, który może być dostarczony jako stała lub kolumna wartości.  

### <a name="gammaregularizedqinverse"></a>GammaRegularizedQInverse

Zwraca wartość funkcji Inverse uogólniona, niepełna funkcja gamma.

Ta funkcja przyjmuje drugi argument, który może być dostarczony jako stała lub kolumna wartości.  

### <a name="polygamma"></a>Funkcja gamma

Zwraca wartość funkcji gamma.  

Ta funkcja przyjmuje drugi argument, który może być dostarczony jako stała lub kolumna wartości.

##  <a name="trigonometric-functions"></a>Funkcje trygonometryczne 

Ta kategoria iIncludes większość ważnych funkcji trygonometrycznych i odwrotnych. Wszystkie funkcje trygonometryczne są jednoargumentowe i nie wymagają dodatkowych argumentów.  

### <a name="acos"></a>Acos

Oblicza arcus cosinus dla wartości kolumn.  

### <a name="acosdegree"></a>AcosDegree

Oblicza arcus cosinus wartości kolumn (w stopniach).  

### <a name="acosh"></a>ACOSH —

Oblicza arcus cosinus hiperboliczny wartości kolumn.  

### <a name="acot"></a>Acot

Oblicza cotangens wartości kolumn.  

### <a name="acotdegrees"></a>AcotDegrees

Oblicza cotangens wartości kolumn (w stopniach).  

### <a name="acoth"></a>Acoth

Oblicza cotangens hiperboliczny wartości kolumn.  

### <a name="acsc"></a>ACSC

Oblicza arccosecant wartości kolumn.  

### <a name="acscdegrees"></a>AcscDegrees

Oblicza arccosecant wartości kolumn (w stopniach).  
### <a name="asec"></a>Asec

Oblicza arcsecant wartości kolumn.  

### <a name="asecdegrees"></a>AsecDegrees

Oblicza arcsecant wartości kolumn (w stopniach).  

### <a name="asech"></a>Asech

Oblicza arcsecant hiperboliczny wartości kolumn.  

### <a name="asin"></a>Asin

Oblicza arcus sinus wartości kolumn.  

### <a name="asindegrees"></a>AsinDegrees

Oblicza arcus sinus wartości kolumn (w stopniach).  

### <a name="asinh"></a>ASINH —

Oblicza arcus sinus hiperboliczny dla wartości kolumn.  

### <a name="atan"></a>Atan

Oblicza arcus tangens wartości kolumn.  

### <a name="atandegrees"></a>AtanDegrees

Oblicza arcus tangens wartości kolumn (w stopniach).  

### <a name="atanh"></a>ATANH —

Oblicza arcus tangens hiperboliczny wartości kolumn.  

### <a name="cos"></a>Cos

Oblicza cosinus wartości kolumn.  

### <a name="cosdegrees"></a>CosDegrees

Oblicza cosinus wartości kolumn (w stopniach).  

### <a name="cosh"></a>Cosh

Oblicza cosinus hiperboliczny dla wartości kolumn.  

### <a name="cot"></a>Cot

Oblicza wartość cotangens dla wartości kolumn.  

### <a name="cotdegrees"></a>CotDegrees

Oblicza wartość cotangens dla wartości kolumn (w stopniach).  

### <a name="coth"></a>Coth
Oblicza cotangens hiperboliczny dla wartości kolumn.  

### <a name="csc"></a>Obsługi

Oblicza wartość cosecans dla wartości kolumn.  

### <a name="cscdegrees"></a>CscDegrees

Oblicza wartość cosecans dla wartości kolumn (w stopniach).  

### <a name="csch"></a>Csch

Oblicza wartość cosecans hiperboliczny dla wartości kolumn.  

### <a name="degreestoradians"></a>DegreesToRadians

Konwertuje stopnie na radiany.  

### <a name="sec"></a>S

Oblicza secans wartości kolumn.  

### <a name="asecdegrees"></a>aSecDegrees

Oblicza secans dla wartości kolumn (w stopniach).  

### <a name="asech"></a>aSech

Oblicza secans hiperboliczny wartości kolumn.  

### <a name="sign"></a>Znak

Zwraca znak wartości kolumny.  

### <a name="sin"></a>Sin

Oblicza sinus wartości kolumn.  

### <a name="sinc"></a>Sinc

Oblicza wartość cosinusa sinusa wartości kolumn.  

### <a name="sindegrees"></a>SinDegrees

Oblicza sinus wartości kolumn (w stopniach).  

### <a name="sinh"></a>Sinh

Oblicza sinus hiperboliczny wartości kolumn.  

### <a name="tan"></a>Tan

Oblicza tangens wartości kolumn.  

### <a name="tandegrees"></a>TanDegrees

Oblicza tangens argumentu (w stopniach).  

### <a name="tanh"></a>Tanh

Oblicza tangens hiperboliczny wartości kolumn.  
  
## <a name="technical-notes"></a>Uwagi techniczne

Należy zachować ostrożność w przypadku wybrania więcej niż jednej kolumny jako drugiego operatora. Wyniki są łatwe do zrozumienia, jeśli operacja jest prosta, na przykład dodawanie stałej do wszystkich kolumn. 

Załóżmy, że zestaw danych ma wiele kolumn, i dodasz zestaw danych do samego siebie. W wynikach każda kolumna jest dodawana do samej siebie w następujący sposób:  
  
|Num1|Num2|Num3|Dodaj (Num1_Num1)|Dodaj (Num2_Num2)|Dodaj (Num3_Num3)|
|----|----|----|----|----|----|
|1|5|2|2|10|4|
|2|3|-1|4|6|-2|
|0|1|-1|0|2|-2|

Jeśli trzeba wykonać bardziej skomplikowane obliczenia, można utworzyć łańcuch wielu wystąpień **operacji Apply**. Na przykład można dodać dwie kolumny przy użyciu jednego wystąpienia **operacji Zastosuj matematyczne**, a następnie użyć innego wystąpienia **operacji matematycznej** , aby podzielić sumę przez stałą w celu uzyskania średniej.  
  
Alternatywnie możesz użyć jednego z następujących modułów, aby wykonać wszystkie obliczenia jednocześnie przy użyciu skryptu SQL, R lub Python:
 
+ [Wykonywanie skryptu języka R](execute-r-script.md)
+ [Wykonywanie skryptu w języku Python](execute-python-script.md)
+ [Stosowanie przekształcenia SQL](apply-sql-transformation.md)   
  
## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
