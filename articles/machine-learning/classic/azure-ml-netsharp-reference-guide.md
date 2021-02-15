---
title: 'ML Studio (klasyczny): net # Custom neuronowych Networks — Azure'
description: 'Przewodnik dotyczący składni języka NET # neuronowych Network Specification. Dowiedz się, jak tworzyć niestandardowe modele sieci neuronowych w Azure Machine Learning Studio (klasyczne).'
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: reference
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2018
ms.openlocfilehash: 5137b633f66088efbee41b96ba715eb3b18961dc
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100519256"
---
# <a name="guide-to-net-neural-network-specification-language-for-machine-learning-studio-classic"></a>Przewodnik dotyczący języka specyfikacji sieci NET # neuronowych dla Machine Learning Studio (klasyczny)

**dotyczy:** ![ Dotyczy. ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) nie ma ![ zastosowania do.](../../../includes/media/aml-applies-to-skus/no.png)[ Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

NET # to język opracowany przez firmę Microsoft, który służy do definiowania złożonych architektur sieci neuronowych, takich jak głębokie sieci neuronowych lub convolutions z dowolnych wymiarów. Możesz użyć złożonych struktur, aby zwiększyć uczenie się na danych, takich jak obraz, wideo lub dźwięk.

W tych kontekstach można użyć specyfikacji architektury net #:

+ Wszystkie moduły sieciowe neuronowych w Microsoft Azure Machine Learning Studio (klasyczny): [wieloklasowe sieci neuronowych](/azure/machine-learning/studio-module-reference/multiclass-neural-network), [dwie klasy sieci neuronowych](/azure/machine-learning/studio-module-reference/two-class-neural-network)oraz [regresja sieci neuronowych](/azure/machine-learning/studio-module-reference/neural-network-regression)
+ Neuronowych funkcje sieciowe w programie Microsoft ML Server: [NeuralNet](/machine-learning-server/r-reference/microsoftml/neuralnet) i [RxNeuralNet](/machine-learning-server/r-reference/microsoftml/rxneuralnet)dla języka R, a [rx_neural_network](/machine-learning-server/python-reference/microsoftml/rx-neural-network) for Python.


W tym artykule opisano podstawowe pojęcia i składnię wymaganą do opracowania niestandardowej sieci neuronowych przy użyciu usługi net #:

+ Neuronowych wymagania sieciowe i jak definiować składniki podstawowe
+ Składnia i słowa kluczowe języka specyfikacji NET #
+ Przykłady niestandardowych sieci neuronowych utworzonych przy użyciu usługi NET #



## <a name="neural-network-basics"></a>Podstawowe informacje dotyczące sieci neuronowych

Struktura sieci neuronowych składa się z węzłów, które są zorganizowane w warstwach, oraz połączeń ważonych (lub krawędzi) między węzłami. Połączenia są kierunkowe, a każde połączenie ma węzeł źródłowy i węzeł docelowy.

Każda warstwa przeszkolna (ukryta lub warstwa wyjściowa) ma jeden lub więcej **pakietów połączeń**. Pakiet połączeń składa się z warstwy źródłowej i specyfikacji połączeń z tej warstwy źródłowej. Wszystkie połączenia w danym pakiecie współdzielą źródłową i docelową warstwę. W polu NET # pakiet połączenia jest traktowany jako należący do warstwy docelowej zbioru.

NET # obsługuje różne rodzaje pakietów połączeń, co pozwala dostosować sposób, w jaki dane wejściowe są mapowane na ukryte warstwy i zamapowane na dane wyjściowe.

Pakiet domyślny lub standardowy jest **pełnym pakietem**, w którym każdy węzeł w warstwie źródłowej jest połączony z każdym węzłem w warstwie docelowej.

Ponadto NET # obsługuje cztery następujące rodzaje zaawansowanych pakietów połączeń:

+ **Przefiltrowane pakiety**. Predykat można zdefiniować przy użyciu lokalizacji węzła warstwy źródłowej i węzła warstwy docelowej. Węzły są połączone za każdym razem, gdy predykat ma wartość true.

+ **Zbiory splotowych**. W warstwie źródłowej można definiować małe kluby węzłów. Każdy węzeł w warstwie docelowej jest połączony z jednym otoczeniem węzłów w warstwie źródłowej.

+ Pakiety **puli** i **pakiety normalizacji odpowiedzi**. Są one podobne do pakietów splotowych w tym, że użytkownik definiuje małe kluby węzłów w warstwie źródłowej. Różnica polega na tym, że wagi krawędzi w tych pakietach nie są obsługiwane. Zamiast tego, wstępnie zdefiniowana funkcja jest stosowana do wartości węzła źródłowego, aby określić wartość węzła docelowego.


## <a name="supported-customizations"></a>Obsługiwane dostosowania

Architektura modeli sieci neuronowych utworzonych w Azure Machine Learning Studio (klasyczny) może być szeroko dostosowywana przy użyciu usługi net #. Oto co możesz zrobić:

+ Utwórz ukryte warstwy i kontroluj liczbę węzłów w każdej warstwie.
+ Określ, w jaki sposób warstwy mają być połączone ze sobą.
+ Zdefiniuj specjalne struktury połączeń, takie jak convolutions i zestawy udostępniania wag.
+ Określ inne funkcje aktywacji.

Aby uzyskać szczegółowe informacje na temat składni języka specyfikacji, zobacz [Specyfikacja struktury](#structure-specifications).

Przykłady definiowania sieci neuronowych dla niektórych typowych zadań uczenia maszynowego — od jednostronnej do złożonej — zobacz [przykłady](#examples-of-net-usage).

## <a name="general-requirements"></a>Wymagania ogólne

+ Musi istnieć dokładnie jedna warstwa wyjściowa, co najmniej jedna warstwa wejściowa i zero lub więcej ukrytych warstw.
+ Każda warstwa ma stałą liczbę węzłów, pojęciowo uporządkowaną w prostokątną tablicę dowolnych wymiarów.
+ Warstwy wejściowe nie mają skojarzonych parametrów szkolonych i przedstawiają punkt, w którym dane wystąpienia wchodzą w interakcję z siecią.
+ Warstwy do uczenia (warstwy ukryte i wyjściowe) mają skojarzone parametry wyszkolone, znane jako wagi i odchylenia.
+ Węzły źródłowy i docelowy muszą znajdować się w osobnych warstwach.
+ Połączenia muszą być acykliczne; Innymi słowy, nie może istnieć łańcuch połączeń prowadzących do początkowego węzła źródłowego.
+ Warstwa wyjściowa nie może być warstwą źródłową pakietu połączenia.

## <a name="structure-specifications"></a>Specyfikacje struktury

Specyfikacja struktury sieci neuronowych składa się z trzech sekcji: **deklaracji stałej**, **deklaracji warstwy**, **deklaracji połączenia**. Istnieje również opcjonalna sekcja **deklaracji udostępniania** . Sekcje można określić w dowolnej kolejności.

## <a name="constant-declaration"></a>Stała deklaracja

Deklaracja stałej jest opcjonalna. Zapewnia to sposób definiowania wartości używanych w innym miejscu w definicji sieci neuronowych. Instrukcja deklaracji składa się z identyfikatora, po którym następuje znak równości i wyrażenie wartości.

Na przykład następująca instrukcja definiuje stałą `x` :

`Const X = 28;`

Aby zdefiniować dwie lub więcej stałych jednocześnie, należy ująć nazwy i wartości identyfikatorów w nawiasach klamrowych i oddzielić je średnikami. Na przykład:

`Const { X = 28; Y = 4; }`

Prawa strona każdego wyrażenia przypisania może być liczbą całkowitą, liczbą rzeczywistą, wartością logiczną (true lub false) lub wyrażeniem matematycznym. Na przykład:

`Const { X = 17 * 2; Y = true; }`

## <a name="layer-declaration"></a>Deklaracja warstwy

Deklaracja warstwy jest wymagana. Definiuje rozmiar i źródło warstwy, łącznie z pakietami połączeń i atrybutami. Instrukcja deklaracji zaczyna się od nazwy warstwy (dane wejściowe, ukryte lub wyjściowe), po której następuje wymiary warstwy (spójna kolekcja liczb całkowitych). Na przykład:

```Net#
input Data auto;
hidden Hidden[5,20] from Data all;
output Result[2] from Hidden all;
```

+ Iloczyn wymiarów to liczba węzłów w warstwie. W tym przykładzie istnieją dwa wymiary [5, 20], co oznacza, że w warstwie znajdują się węzły 100.
+ Warstwy można zadeklarować w dowolnej kolejności, z wyjątkiem jednego wyjątku: Jeśli zdefiniowano więcej niż jedną warstwę wejściową, kolejność, w jakiej są one deklarowane musi być zgodna z kolejnością funkcji w danych wejściowych.

Aby określić, że liczba węzłów w warstwie ma być określana automatycznie, użyj `auto` słowa kluczowego. `auto`Słowo kluczowe ma różne efekty, w zależności od warstwy:

+ W deklaracji warstwy wejściowej liczba węzłów jest liczbą funkcji w danych wejściowych.
+ W deklaracji warstwy ukrytej liczba węzłów jest liczbą określoną przez wartość parametru dla **liczby ukrytych węzłów**.
+ W deklaracji warstwy wyjściowej liczba węzłów wynosi 2 dla klasyfikacji dwóch klas, 1 dla regresji i równa liczbie węzłów wyjściowych klasyfikacji wieloklasowej.

Na przykład następująca definicja sieci umożliwia automatyczne określenie rozmiaru wszystkich warstw:

```Net#
input Data auto;
hidden Hidden auto from Data all;
output Result auto from Hidden all;
```

Deklaracja warstwy dla warstwy możliwej do przeprowadzenia (ukryte lub wyjściowe) może opcjonalnie zawierać funkcję wyjściową (nazywaną również funkcją aktywacji), która domyślnie jest **sigmoid** dla modeli klasyfikacji i **liniowy** dla modeli regresji. Nawet jeśli jest używana wartość domyślna, można jawnie użyć funkcji aktywacji, jeśli jest to potrzebne do przejrzystości.

Obsługiwane są następujące funkcje wyjściowe:

+ sigmoid
+ liniowej
+ softmax
+ rlinear
+ kwadratowe
+ sqrt
+ srlinear
+ ABS
+ TANH —
+ brlinear

Na przykład następująca deklaracja używa funkcji **softmax** :

`output Result [100] softmax from Hidden all;`

## <a name="connection-declaration"></a>Deklaracja połączenia

Natychmiast po zdefiniowaniu warstwy z przeszkoleniem należy zadeklarować połączenia między zdefiniowanymi warstwami. Deklaracja pakietu połączeń rozpoczyna się od słowa kluczowego `from` , po którym następuje nazwa warstwy źródłowej pakietu i rodzaj pakietu połączenia do utworzenia.

Obecnie obsługiwane są pięć rodzajów pakietów połączeń:

+ **Pełne** pakiety, wskazywane przez słowo kluczowe `all`
+ **Przefiltrowane** zbiory, wskazywane przez słowo kluczowe `where` , po którym następuje wyrażenie predykatu
+ Zbiory **splotowych** , wskazywane przez słowo kluczowe `convolve` , po których następuje atrybuty Convolution
+ Zbiory **pul** , wskazywane przez **maksymalną pulę** lub **średnią** pulę słów kluczowych
+ Pakiety **normalizacji odpowiedzi** wskazywane przez **normę odpowiedzi** słowa kluczowego

## <a name="full-bundles"></a>Pełne pakiety

Pakiet Full Connection zawiera połączenie z każdego węzła w warstwie źródłowej do każdego węzła w warstwie docelowej. Jest to domyślny typ połączenia sieciowego.

## <a name="filtered-bundles"></a>Przefiltrowane zbiory

Specyfikacja zbioru połączeń filtrowanych zawiera predykat, podobnie jak wyrażenie lambda języka C#. W poniższym przykładzie zdefiniowano dwa przefiltrowane pakiety:

```Net#
input Pixels [10, 20];
hidden ByRow[10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol[5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;
```

+ W predykacie dla `ByRow` , `s` jest parametrem reprezentującym indeks do prostokątnego tablicy węzłów warstwy wejściowej, `Pixels` i `d` jest parametrem reprezentującym indeks do tablicy węzłów ukrytych warstwy `ByRow` . Typ obu `s` i `d` jest krotką liczb całkowitych długości dwóch. Koncepcyjnie, `s` zakresy dla wszystkich par liczb całkowitych z `0 <= s[0] < 10` i i są `0 <= s[1] < 20` `d` przenoszone na wszystkie pary liczb całkowitych, z `0 <= d[0] < 10` i `0 <= d[1] < 12` .

+ Po prawej stronie wyrażenia predykatu istnieje warunek. W tym przykładzie dla każdej wartości i w `s` `d` taki sposób, że warunek ma wartość true, istnieje krawędź z węzła warstwy źródłowej z węzłem warstwy docelowej. W związku z tym wyrażenie filtru wskazuje, że pakiet zawiera połączenie z węzła zdefiniowanego przez `s` do węzła zdefiniowanego przez `d` we wszystkich przypadkach, gdzie s [0] jest równe d [0].

Opcjonalnie można określić zestaw wag dla odfiltrowanego pakietu. Wartość atrybutu **wagi** musi być krotką wartości zmiennoprzecinkowych o długości odpowiadającej liczbie połączeń zdefiniowanych przez ten pakiet. Domyślnie wagi są generowane losowo.

Wartości wag są pogrupowane według indeksu węzła docelowego. Oznacza to, że jeśli pierwszy węzeł docelowy jest połączony z węzłami źródłowymi K, pierwsze `K` elementy krotki **odważników** to wagi pierwszego węzła docelowego w kolejności indeksu źródła. To samo dotyczy pozostałego węzła docelowego.

Możliwe jest określenie wag bezpośrednio jako wartości stałych. Na przykład jeśli poznasz już wagi, można je określić jako stałe przy użyciu następującej składni:

`const Weights_1 = [0.0188045055, 0.130500451, ...]`

## <a name="convolutional-bundles"></a>Pakiety splotowych

Gdy dane szkoleniowe mają jednorodną strukturę, połączenia splotowych są często używane do uczenia funkcji wysokiego poziomu danych. Na przykład w przypadku danych obrazu, audio lub wideo, przestrzenne lub wymiarowe wymiary mogą być dość jednorodne.

Zbiory splotowych korzystają z prostokątnych **jądra** , które są Slid przez wymiary. Zasadniczo każde jądro definiuje zestaw wag stosowanych w lokalnych klubach, nazywanych **aplikacjami jądra**. Każda aplikacja jądra odnosi się do węzła w warstwie źródłowej, który jest określany jako **węzeł centralny**. Wagi jądra są współużytkowane przez wiele połączeń. W pakiecie splotowych każde jądro jest prostokątne i wszystkie aplikacje jądra mają ten sam rozmiar.

Pakiety splotowych obsługują następujące atrybuty:

**InputShape** definiuje wymiar warstwy źródłowej dla celów tego pakietu splotowych. Wartość musi być krotką dodatnich liczb całkowitych. Iloczyn liczb całkowitych musi być równy liczbie węzłów w warstwie źródłowej, ale w przeciwnym razie nie musi być zgodny z wymiarem zadeklarowanym dla warstwy źródłowej. Długość tej krotki zmieni się na wartość **liczbową** dla pakietu splotowych. Zazwyczaj liczba argumentów odnosi się do liczby argumentów lub argumentów operacji, które może wykonać funkcja.

Aby zdefiniować kształt i lokalizacje jądra, użyj atrybutów **KernelShape**, **krok**, **dopełnienie**, **LowerPad** i **UpperPad**:

+ **KernelShape**: (wymagane) definiuje wymiar każdego jądra dla pakietu splotowych. Wartość musi być krotką dodatnich liczb całkowitych o długości równej liczbie argumentów pakietu. Każdy składnik tej krotki nie może być większy niż odpowiedni składnik **InputShape**.

+ **Krok**: (opcjonalnie) definiuje rozmiary ruchomych etapów Convolution (jeden rozmiar kroku dla każdego wymiaru), czyli odległość między węzłami centralnymi. Wartość musi być krotką dodatnich liczb całkowitych o długości, która jest liczbą argumentów pakietu. Każdy składnik tej krotki nie może być większy niż odpowiedni składnik **KernelShape**. Wartość domyślna to krotka ze wszystkimi składnikami równą jeden.

+ **Udostępnianie**: (opcjonalnie) definiuje udostępnianie wagi dla każdego wymiaru Convolution. Wartość może być pojedynczą wartością logiczną lub krotką wartości logicznych o długości, która jest równa liczbie argumentów pakietu. Pojedyncza wartość logiczna jest rozszerzona tak, aby była krotką o prawidłowej długości ze wszystkimi składnikami równymi określonej wartości. Wartość domyślna to krotka, która składa się ze wszystkich wartości rzeczywistych.

+ **MapCount**: (opcjonalnie) określa liczbę map funkcji dla pakietu splotowych. Wartość może być pojedynczą dodatnią liczbą całkowitą lub krotką dodatnich liczb całkowitych o długości, która jest liczbą argumentów pakietu. Wartość pojedynczej liczby całkowitej jest rozszerzona tak, aby była krotką o prawidłowej długości przy pierwszym składniku równym określonej wartości, a wszystkie pozostałe składniki są równe jednej. Wartość domyślna to 1. Łączna liczba map funkcji jest iloczynem składników spójnej kolekcji. Współczynnik całkowitej liczby elementów w składnikach określa sposób grupowania wartości mapy funkcji w węzłach docelowych.

+ **Wagi**: (opcjonalnie) określa początkowe wagi dla pakietu. Wartość musi być krotką wartości zmiennoprzecinkowych o długości, która jest liczbą jądra pomnożoną przez liczbę obciążeń na jądro, zgodnie z definicją w dalszej części tego artykułu. Domyślne wagi są generowane losowo.

Istnieją dwa zestawy właściwości kontrolujących dopełnienie, które wzajemnie się wykluczają:

+ **Uzupełnienie**: (opcjonalnie) określa, czy dane wejściowe powinny być uzupełniane przy użyciu **domyślnego schematu uzupełniania**. Wartość może być pojedynczą wartością logiczną lub może być krotką wartości logicznych o długości, która jest równa liczbie argumentów z pakietu.

    Pojedyncza wartość logiczna jest rozszerzona tak, aby była krotką o prawidłowej długości ze wszystkimi składnikami równymi określonej wartości.

    Jeśli wartość wymiaru ma wartość true, źródło zostanie logicznie uzupełnione w tym wymiarze z komórkami o zerowej wartości, aby zapewnić obsługę dodatkowych aplikacji jądra, w taki sposób, że centralne węzły pierwszego i ostatniego jądra w tym wymiarze są pierwszym i ostatnim węzłem w tym wymiarze w warstwie źródłowej. W ten sposób liczba węzłów "fikcyjnych" w każdym wymiarze jest określana automatycznie, aby dopasować dokładnie `(InputShape[d] - 1) / Stride[d] + 1` jądra do uzupełnionej warstwy źródłowej.

    Jeśli wartość dla wymiaru ma wartość false, jądra są zdefiniowane tak, aby liczba węzłów na każdej stronie, które są pozostawione, jest taka sama (maksymalnie z różnicą 1). Wartość domyślna tego atrybutu jest krotką ze wszystkimi składnikami równymi false.

+ **UpperPad** i **LowerPad**: (opcjonalnie) zapewniają większą kontrolę nad ilością dopełnienia, która ma zostać użyta. **Ważne:** Te atrybuty można zdefiniować, jeśli i tylko wtedy, gdy właściwość **uzupełnienie** **_nie_ jest *zdefiniowana. Wartości powinny być krotkami o wartościach całkowitych o długościach, które są liczbami argumentów pakietu. Gdy te atrybuty są określone, węzły "fikcyjne" są dodawane do dolnego i górnego końca każdego wymiaru warstwy wejściowej. Liczba węzłów dodawanych do dolnych i górnych punktów końcowych w każdym wymiarze jest określana odpowiednio przez _* LowerPad**[i] i **UpperPad**[i].

    Aby zapewnić, że jądra są zgodne tylko z węzłami "Real", a nie z węzłami "fikcyjne", muszą zostać spełnione następujące warunki:
  - Każdy składnik elementu **LowerPad** musi być ściśle mniejszy niż `KernelShape[d]/2` .
  - Każdy składnik elementu **UpperPad** nie może być większy niż `KernelShape[d]/2` .
  - Wartością domyślną tych atrybutów jest krotka ze wszystkimi składnikami równą 0.

    **Uzupełnienie** ustawień = true zezwala na dopełnienie, gdy jest to konieczne, aby zachować "centrum" jądra wewnątrz "Real" danych wejściowych. Spowoduje to zmianę bitu matematycznego na Obliczanie rozmiaru wyjściowego. Ogólnie rzecz biorąc, rozmiar danych wyjściowych *D* jest obliczany jako `D = (I - K) / S + 1` , gdzie `I` jest rozmiarem wejściowym, `K` jest rozmiarem jądra, `S` jest to krok i `/` jest dzieleniem liczb całkowitych (w przybliżeniu równa zero). Jeśli ustawisz UpperPad = [1, 1], rozmiar wejściowy `I` jest efektywny 29, a tym samym `D = (29 - 5) / 2 + 1 = 13` . Jednakże, jeśli **uzupełnienie** = true, zasadniczo jest to `I` spowodowane przez `K - 1` ; w związku z tym `D = ((28 + 4) - 5) / 2 + 1 = 27 / 2 + 1 = 13 + 1 = 14` . Określenie wartości dla **UpperPad** i **LowerPad** zapewnia znacznie większą kontrolę nad uzupełnieniem niż w przypadku, gdy właśnie ustawiono **uzupełnienie** = true.

Aby uzyskać więcej informacji na temat sieci splotowych i ich aplikacji, zobacz następujące artykuły:

+ [http://d2l.ai/chapter_convolutional-neural-networks/lenet.html ](http://d2l.ai/chapter_convolutional-neural-networks/lenet.html )
+ [https://research.microsoft.com/pubs/68920/icdar03.pdf](https://research.microsoft.com/pubs/68920/icdar03.pdf)

## <a name="pooling-bundles"></a>Zbiory pul

**Pakiet puli** ma zastosowanie geometrii podobnej do łączności splotowych, ale używa wstępnie zdefiniowanych funkcji do wartości węzła źródłowego w celu uzyskania wartości węzła docelowego. W związku z tym zbiory buforowania nie mają stanu do uczenia (wagi lub odchylenia). Zbiory buforowania obsługują wszystkie atrybuty splotowych z wyjątkiem **udostępniania**, **MapCount** i **wag**.

Zwykle jądra podsumowywane przez sąsiednie jednostki puli nie nakładają się na siebie. Jeśli krok [d] jest równy KernelShape [d] w każdym wymiarze, uzyskana warstwa jest tradycyjną warstwą buforowania lokalnego, która jest często stosowana w sieciach splotowych neuronowych. Każdy węzeł docelowy oblicza maksymalną lub średnią aktywność jądra w warstwie źródłowej.

Poniższy przykład ilustruje zbiór puli:

```Net#
hidden P1 [5, 12, 12]
  from C1 max pool {
  InputShape  = [ 5, 24, 24];
   KernelShape = [ 1,  2,  2];
   Stride      = [ 1,  2,  2];
  }
```

+ Liczba argumentów pakietu wynosi 3: to, Długość krotek `InputShape` , `KernelShape` i `Stride` .
+ Liczba węzłów w warstwie źródłowej to `5 * 24 * 24 = 2880` .
+ Jest to tradycyjna warstwa puli lokalnej, ponieważ **KernelShape** i **krok** są równe.
+ Liczba węzłów w warstwie docelowej to `5 * 12 * 12 = 1440` .

Aby uzyskać więcej informacji o warstwach puli, zobacz następujące artykuły:

+ [https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf) (Sekcja 3,4)
+ [https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf](https://cs.nyu.edu/~koray/publis/lecun-iscas-10.pdf)
+ [https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf](https://cs.nyu.edu/~koray/publis/jarrett-iccv-09.pdf)

## <a name="response-normalization-bundles"></a>Pakiety normalizacji odpowiedzi

**Normalizacja odpowiedzi** to lokalny schemat normalizacji, który został po raz pierwszy wprowadzony przez Geoffrey Hinton, et al, w [klasyfikacji papierowej ImageNet z głębokiego splotowych neuronowych](https://www.cs.toronto.edu/~hinton/absps/imagenet.pdf).

Normalizacja odpowiedzi jest używana do uogólniania w sieci neuronowych. Gdy jeden neuron jest wyzwalany na wysokim poziomie aktywacji, warstwa normalizacji odpowiedzi lokalnej pomija poziom aktywacji otaczającego neurons. Jest to realizowane przy użyciu trzech parametrów ( `α` , `β` i `k` ) oraz struktury splotowych (lub kształtu klubu). Każdy neuron w warstwie docelowej **y** odpowiada neuron **x** w warstwie źródłowej. Poziom aktywacji **y** jest określony przez następującą formułę, gdzie `f` jest poziomem aktywacji neuron i `Nx` jest jądrem (lub zestawem zawierającym neurons w sąsiedztwie **x**), zgodnie z definicją w następującej strukturze splotowych:

![Formuła dla struktury splotowych](./media/azure-ml-netsharp-reference-guide/formula_large.png)

Pakiety normalizacji odpowiedzi obsługują wszystkie atrybuty splotowych z wyjątkiem **udostępniania**, **MapCount** i **wag**.

+ Jeśli jądro zawiera neurons w tej samej mapie jak ***x** _, schemat normalizacji jest określany jako _ * ta sama normalizacja mapy * *. Aby zdefiniować tę samą normalizację mapy, pierwsza Współrzędna w **InputShape** musi mieć wartość 1.

+ Jeśli jądro zawiera neurons w tym samym położeniu przestrzennym co ***x** _, ale neurons znajdują się w innych mapach, schemat normalizacji jest wywoływany _ * w przypadku normalizacji Maps * *. Ten typ normalizacji odpowiedzi implementuje formę procedury inhibicji bocznej, która jest inspirowana typem znalezionym w rzeczywistości neurons, tworząc konkurs dla dużych poziomów aktywacji między wynikami neuron obliczanymi w różnych mapach. Aby zdefiniować w obrębie normalizacji Maps, pierwsza Współrzędna musi być liczbą całkowitą większą niż 1 i nie większa niż liczba map

Ponieważ pakiety normalizacji odpowiedzi stosują wstępnie zdefiniowaną funkcję do wartości węzła źródłowego, aby określić wartość węzła docelowego, nie mają stanu do uczenia (wagi lub odchylenia).

> [!NOTE]
> Węzły w warstwie docelowej odpowiadają neurons, które są centralnymi węzłami jądra. Na przykład jeśli `KernelShape[d]` jest nieparzysta, `KernelShape[d]/2` odpowiada centralnemu węzłowi jądra. Jeśli `KernelShape[d]` jest nawet, węzeł Centralny ma wartość o `KernelShape[d]/2 - 1` . W związku z tym, jeśli `Padding[d]` ma wartość false, pierwszy i ostatni `KernelShape[d]/2` węzeł nie mają odpowiednich węzłów w warstwie docelowej. Aby uniknąć tej sytuacji, zdefiniuj **uzupełnienie** jako [true, true,..., true].

Oprócz czterech opisanych wcześniej atrybutów, pakiety normalizacji odpowiedzi obsługują również następujące atrybuty:

+ **Alpha**: (required) określa wartość zmiennoprzecinkową, która odpowiada `α` w poprzedniej formule.
+ **Beta**: (wymagane) określa wartość zmiennoprzecinkową, która odpowiada `β` w poprzedniej formule.
+ **Przesunięcie**: (opcjonalnie) określa wartość zmiennoprzecinkową, która odpowiada `k` w poprzedniej formule. Wartość domyślna to 1.

W poniższym przykładzie zdefiniowano pakiet normalizacji odpowiedzi przy użyciu następujących atrybutów:

```Net#
hidden RN1 [5, 10, 10]
from P1 response norm {
  InputShape  = [ 5, 12, 12];
  KernelShape = [ 1,  3,  3];
  Alpha = 0.001;
  Beta = 0.75;
  }
```

+ Warstwa źródłowa zawiera pięć map, z których każdy ma kopia zapasowa AOF wymiar 12x12, co umożliwia sumowanie w węzłach 1440.
+ Wartość **KernelShape** wskazuje, że jest to taka sama warstwa normalizacji mapy, gdzie klub jest prostokątem 3x3.
+ Wartość domyślna **dopełnienia** to false, w tym przypadku warstwa docelowa ma tylko 10 węzłów w każdym wymiarze. Aby dołączyć jeden węzeł do warstwy docelowej, który odnosi się do każdego węzła w warstwie źródłowej, Dodaj uzupełnienie = [true, true, true]; i Zmień rozmiar RN1 na [5, 12, 12].

## <a name="share-declaration"></a>Udostępnianie deklaracji

NET # opcjonalnie obsługuje definiowanie wielu pakietów z współdzielonymi wagami. Wagi każdego z dwóch pakietów można udostępnić, jeśli ich struktury są takie same. Poniższa składnia definiuje zbiory ze współdzielonymi wagami:

```Net#
share-declaration:
  share    {    layer-list    }
  share    {    bundle-list    }
  share    {    bias-list    }

  layer-list:
    layer-name    ,    layer-name
    layer-list    ,    layer-name

  bundle-list:
    bundle-spec    ,    bundle-spec
    bundle-list    ,    bundle-spec

  bundle-spec:
    layer-name    =>     layer-name

  bias-list:
    bias-spec    ,    bias-spec
    bias-list    ,    bias-spec

  bias-spec:
    1    =>    layer-name

  layer-name:
    identifier
```

Na przykład następująca deklaracja udziału określa nazwy warstw, wskazując, że obie wagi i bias powinny być współużytkowane:

```Net#
Const {
  InputSize = 37;
  HiddenSize = 50;
  }
input {
  Data1 [InputSize];
  Data2 [InputSize];
  }
hidden {
  H1 [HiddenSize] from Data1 all;
  H2 [HiddenSize] from Data2 all;
  }
output Result [2] {
  from H1 all;
  from H2 all;
  }
share { H1, H2 } // share both weights and biases
```

+ Funkcje wejściowe są podzielone na dwie warstwy wejściowe o równym rozmiarze.
+ Ukryte warstwy następnie obliczają funkcje wyższego poziomu na dwóch warstwach wejściowych.
+ Deklaracja udziału określa, że *H1* i *H2* muszą być obliczane w taki sam sposób, jak w odpowiednich danych wejściowych.

Alternatywnie można je określić przy użyciu dwóch oddzielnych deklaracji udziałów w następujący sposób:

```Net#
share { Data1 => H1, Data2 => H2 } // share weights
<!-- -->
    share { 1 => H1, 1 => H2 } // share biases
```

Możesz użyć krótkiej formy tylko wtedy, gdy warstwy zawierają jeden pakiet. Ogólnie udostępnianie jest możliwe tylko wtedy, gdy odpowiednia struktura jest taka sama, co oznacza, że mają ten sam rozmiar, tę samą geometrię splotowych i tak dalej.

## <a name="examples-of-net-usage"></a>Przykłady użycia NET #

W tej sekcji przedstawiono kilka przykładów użycia polecenia NET # do dodawania ukrytych warstw, zdefiniowanie sposobu, w jaki ukryte warstwy współdziałają z innymi warstwami i tworzą sieci splotowych.

### <a name="define-a-simple-custom-neural-network-hello-world-example"></a>Zdefiniuj prostą niestandardową sieć neuronowych: przykład "Hello world"

Ten prosty przykład pokazuje, jak utworzyć model sieci neuronowych, który ma pojedynczą ukrytą warstwę.

```Net#
input Data auto;
hidden H [200] from Data all;
output Out [10] sigmoid from H all;
```

Przykład ilustruje niektóre podstawowe polecenia w następujący sposób:

+ Pierwszy wiersz definiuje warstwę wejściową (o nazwie `Data` ). W przypadku użycia  `auto` słowa kluczowego sieć neuronowych automatycznie uwzględnia wszystkie kolumny funkcji w przykładach wejściowych.
+ Drugi wiersz tworzy ukrytą warstwę. Nazwa `H` jest przypisana do warstwy ukrytej, która ma węzły 200. Ta warstwa jest w pełni połączona z warstwą wejściową.
+ Trzeci wiersz definiuje warstwę wyjściową (o nazwie `Out` ), która zawiera 10 węzłów wyjściowych. Jeśli sieć neuronowych jest używana do klasyfikacji, istnieje jeden węzeł wyjściowy dla każdej klasy. Słowo kluczowe **sigmoid** wskazuje, że funkcja Output jest stosowana do warstwy wyjściowej.

### <a name="define-multiple-hidden-layers-computer-vision-example"></a>Definiowanie wielu ukrytych warstw: przykładowa obsługa komputera

Poniższy przykład ilustruje sposób definiowania nieco bardziej złożonej sieci neuronowych z wieloma ukrytymi warstwami niestandardowymi.

```Net#
// Define the input layers
input Pixels [10, 20];
input MetaData [7];

// Define the first two hidden layers, using data only from the Pixels input
hidden ByRow [10, 12] from Pixels where (s,d) => s[0] == d[0];
hidden ByCol [5, 20] from Pixels where (s,d) => abs(s[1] - d[1]) <= 1;

// Define the third hidden layer, which uses as source the hidden layers ByRow and ByCol
hidden Gather [100]
{
from ByRow all;
from ByCol all;
}

// Define the output layer and its sources
output Result [10]
{
from Gather all;
from MetaData all;
}
```

Ten przykład ilustruje kilka funkcji języka specyfikacji sieci neuronowych:

+ Struktura ma dwie warstwy wejściowe `Pixels` i `MetaData` .
+ `Pixels`Warstwa jest warstwą źródłową dla dwóch pakietów połączeń z warstwami docelowymi `ByRow` i `ByCol` .
+ Warstwy `Gather` i `Result` są warstwami docelowymi w wielu pakietach połączeń.
+ Warstwa wyjściowa, `Result` , jest warstwą docelową w dwóch pakietach połączeń; jeden z warstwą Ukryta drugiego poziomu `Gather` jako warstwa docelowa, a druga z warstwy wejściowej `MetaData` jako warstwę docelową.
+ Ukryte warstwy `ByRow` i `ByCol` określają przefiltrowane połączenia przy użyciu wyrażeń predykatu. Dokładniej, węzeł w `ByRow` lokalizacji [x, y] jest połączony z węzłami w `Pixels` , które mają pierwszy współrzędną indeksu równą pierwszej koordynacji węzła, x. Podobnie węzeł w `ByCol` lokalizacji [x, y] jest połączony z węzłami w `Pixels` , który ma drugą współrzędną indeksu w jednym z drugiej współrzędnej węzła, y.

### <a name="define-a-convolutional-network-for-multiclass-classification-digit-recognition-example"></a>Zdefiniuj sieć splotowych dla klasyfikacji wieloklasowej: przykład rozpoznawania cyfr

Definicja następującej sieci została zaprojektowana w celu rozpoznawania liczb i ilustruje kilka zaawansowanych technik dostosowywania sieci neuronowych.

```Net#
input Image [29, 29];
hidden Conv1 [5, 13, 13] from Image convolve
  {
  InputShape  = [29, 29];
  KernelShape = [ 5,  5];
  Stride      = [ 2,  2];
  MapCount    = 5;
  }
hidden Conv2 [50, 5, 5]
from Conv1 convolve
  {
  InputShape  = [ 5, 13, 13];
  KernelShape = [ 1,  5,  5];
  Stride      = [ 1,  2,  2];
  Sharing     = [false, true, true];
  MapCount    = 10;
  }
hidden Hid3 [100] from Conv2 all;
output Digit [10] from Hid3 all;
```

+ Struktura ma jedną warstwę wejściową `Image` .
+ Słowo kluczowe `convolve` wskazuje, że warstwy o nazwie `Conv1` i `Conv2` są warstwami splotowych. Po każdej z tych deklaracji warstwy następuje lista atrybutów Convolution.
+ Sieć ma trzecią ukrytą warstwę, `Hid3` która jest w pełni połączona z drugą warstwą ukryta, `Conv2` .
+ Warstwa wyjściowa, `Digit` ,,, jest połączona tylko z trzecią ukrytą warstwą `Hid3` . Słowo kluczowe `all` wskazuje, że warstwa wyjściowa jest w pełni połączona z `Hid3` .
+ Liczba argumentów Convolution to trzy: długość spójnych krotek `InputShape` ,, `KernelShape` `Stride` i `Sharing` .
+ Liczba wag na jądro `1 + KernelShape\[0] * KernelShape\[1] * KernelShape\[2] = 1 + 1 * 5 * 5 = 26` . Lub `26 * 50 = 1300` .
+ Węzły w każdej ukrytej warstwie można obliczyć w następujący sposób:

    `NodeCount\[0] = (5 - 1) / 1 + 1 = 5` `NodeCount\[1] = (13 - 5) / 2 + 1 = 5`
    `NodeCount\[2] = (13 - 5) / 2 + 1 = 5`

+ Łączna liczba węzłów można obliczyć przy użyciu deklarowanej wartości wymiaru warstwy [50, 5, 5] w następujący sposób: `MapCount * NodeCount\[0] * NodeCount\[1] * NodeCount\[2] = 10 * 5 * 5 * 5`
+ Ponieważ `Sharing[d]` ma wartość false tylko dla `d == 0` , liczba jądra jest równa `MapCount * NodeCount\[0] = 10 * 5 = 50` .

## <a name="acknowledgements"></a>Podziękowania

Język NET # do dostosowywania architektury sieci neuronowych został opracowany przez firmę Microsoft przez Shon Katzenberger (architekt, Machine Learning) i Alexey Kamenev (inżynier oprogramowania, Microsoft Research). Jest używany wewnętrznie w przypadku projektów i aplikacji usługi Machine Learning w zakresie od wykrywania obrazów do analizy tekstu. Aby uzyskać więcej informacji, zobacz [neuronowych sieci w Azure Machine Learning Studio — wprowadzenie do usługi NET #](/archive/blogs/machinelearning/neural-nets-in-azure-ml-introduction-to-net)