---
title: Korzystanie z języka R z Machine Learning Studio (klasyczny) — Azure
description: Skorzystaj z tego samouczka programowania w języku R, aby rozpocząć pracę z Azure Machine Learning Studio (klasyczną) w R, aby utworzyć rozwiązanie do prognozowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: how-to
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: c619b51f9323477bda4f1ec99aeeb1bfa01028fc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100517743"
---
# <a name="get-started-with-azure-machine-learning-studio-classic-in-r"></a>Wprowadzenie do Azure Machine Learning Studio (klasyczne) w języku R

**dotyczy:** ![ Jest to znacznik wyboru, co oznacza, że ten artykuł ma zastosowanie do Machine Learning Studio (klasyczne). ](../../../includes/media/aml-applies-to-skus/yes.png) Machine Learning Studio (klasyczny) ![ to jest X, co oznacza, że ten artykuł ma zastosowanie do Azure Machine Learning ](../../../includes/media/aml-applies-to-skus/no.png)[ . Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)  

<!-- Stephen F Elston, Ph.D. -->
W ramach tego samouczka nauczysz się używać Azure Machine Learning Studio (klasycznego) do tworzenia, testowania i wykonywania kodu w języku R. Na końcu będziesz mieć kompleksowe rozwiązanie do prognozowania.

> [!div class="checklist"]
> * Utwórz kod służący do czyszczenia i przekształcania danych.
> * Analizuj korelacje między kilkoma zmiennymi w naszym zestawie danych.
> * Utwórz model prognozowania szeregów czasowych dla produkcji mleka.


Machine Learning Studio (klasyczny) zawiera wiele zaawansowanych modułów uczenia maszynowego i manipulowania danymi. Korzystając z języka programowania R, ta kombinacja zapewnia skalowalność i łatwość wdrażania Machine Learning Studio (klasyczny) z elastyczną i dogłębną analizą języka R.

Prognozowanie to szeroko zaangażowane i przydatne metody analityczne. Typowym zastosowaniem jest zakres od przewidywania sprzedaży elementów sezonowych i określania optymalnych poziomów zapasów do przewidywania zmiennych makroekonomicznych. Prognozowanie jest zwykle wykonywane z użyciem modeli szeregów czasowych. Dane szeregów czasowych to dane, w których wartości mają indeks czasu. Indeks czasu może być regularny, na przykład co miesiąc lub co minutę. Indeks czasu może być również nieregularny. Model szeregów czasowych jest oparty na danych szeregów czasowych. Język programowania R zawiera elastyczną platformę i rozbudowaną analizę dla danych szeregów czasowych.

## <a name="get-the-data"></a>Pobieranie danych

W tym samouczku przedstawiono dane dotyczące produkcji mlecznej i cennika, w tym miesięczne informacje o produkcji kilku produktów mleczarskich i ceny tłuszczu mlekowego, który jest towarem porównawczym.

Dane używane w tym artykule wraz ze skryptami języka R można pobrać z [MachineLearningSamples-notesów/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples). Dane w pliku zostały pierwotnie przeszkolene `cadairydata.csv` z informacji dostępnych w [witrynie Wisconsin rynków mlecznych](https://dairymarkets.com)na Uniwersytecie.

## <a name="interact-with-r-language-in-machine-learning-studio-classic"></a><a id="mlstudio"></a>Korzystanie z języka R w Machine Learning Studio (klasyczny)

W tej części przedstawiono podstawowe informacje dotyczące współpracy z językiem programowania R w środowisku Machine Learning Studio (klasycznego). Język R zapewnia zaawansowane narzędzie do tworzenia niestandardowych modułów analizy i manipulowania danymi w środowisku Machine Learning Studio (klasycznym).

Będziemy używać RStudio do opracowywania, testowania i debugowania kodu języka R w niewielkiej skali. Ten kod jest następnie wycięty i wklejony do modułu [wykonywania skryptu języka R][execute-r-script] gotowego do uruchomienia w Machine Learning Studio (klasyczny).

### <a name="the-execute-r-script-module"></a>Moduł wykonywania skryptu języka R

W Machine Learning Studio (klasyczny) skrypty języka R są uruchamiane w module [wykonywania skryptu języka r][execute-r-script] . Przykładem modułu [wykonywania skryptu języka R][execute-r-script] w Machine Learning Studio (klasyczny) przedstawiono tutaj.

 ![Zrzut ekranu pokazujący język programowania języka R: moduł wykonywania skryptu języka R wybrany w Machine Learning Studio (klasyczny).](./media/r-quickstart/fig1.png)

Na powyższym obrazie przedstawiono niektóre najważniejsze części środowiska Machine Learning Studio (klasycznego) do pracy z modułem [wykonywania skryptu języka R][execute-r-script] :

* Moduły w tym eksperymentie są wyświetlane w środkowym okienku.
* Górna część okienka po prawej stronie zawiera okno, za pomocą którego można wyświetlać i edytować skrypty języka R.
* W dolnej części okienka po prawej stronie są wyświetlane pewne właściwości [skryptu Execute języka R][execute-r-script]. Możesz wyświetlić dziennik błędów i danych wyjściowych, wybierając odpowiednie obszary tego okienka.

Szczegółowo omówiono [wykonanie skryptu języka R][execute-r-script] w dalszej części tego artykułu.

Podczas pracy z złożonymi funkcjami języka R zalecamy edytowanie, testowanie i debugowanie w RStudio. Podobnie jak w przypadku tworzenia oprogramowania, Zwiększ swój kod przyrostowo i przetestuj go w małych, prostych przypadkach testowych. Następnie należy wyciąć i wkleić funkcje do okna skryptu języka R w module [wykonywania skryptu języka r][execute-r-script] . Takie podejście umożliwia korzystanie z RStudio zintegrowanego środowiska programistycznego (IDE) i możliwości Machine Learning Studio (klasyczne).

#### <a name="execute-r-code"></a>Wykonaj kod R

Każdy kod języka R w module [wykonywania skryptu języka r][execute-r-script] zostanie wykonany po uruchomieniu eksperymentu, wybierając przycisk **Run (Uruchom** ). Po zakończeniu wykonywania znacznik wyboru pojawia się na ikonie [Wykonaj skrypt języka R][execute-r-script] .

#### <a name="defensive-r-coding-for-machine-learning-studio-classic"></a>Obronne kodowanie języka R dla Machine Learning Studio (klasyczne)

Jeśli opracowujesz kod języka R dla programu, Załóżmy, że usługa sieci Web przy użyciu Machine Learning Studio (klasyczny), należy ostatecznie zaplanować, w jaki sposób kod będzie zajmował się nieoczekiwanym danymi wejściowymi i wyjątkami. Aby zachować przejrzystość, nie dodaliśmy części w sposób umożliwiający sprawdzanie lub obsługę wyjątków w większości przykładów kodu. W miarę jak będziemy udostępniać kilka przykładów funkcji przy użyciu funkcji obsługi wyjątków języka R.

Jeśli potrzebujesz bardziej kompletnej obróbki obsługi wyjątków języka R, przeczytaj odpowiednie sekcje książki według Wickham na liście w [dalszej](#appendixb)części.

#### <a name="debug-and-test-r-in-machine-learning-studio-classic"></a>Debugowanie i testowanie języka R w Machine Learning Studio (klasyczny)

Przetestuj i Debuguj kod języka R na małą skalę w RStudio. Istnieją również sytuacje, w których należy śledzić problemy związane z kodem R w samym [skrypcie Execute języka r][execute-r-script] . Ponadto dobrym sposobem jest sprawdzenie wyników w Machine Learning Studio (klasyczny).

Dane wyjściowe z wykonywania kodu języka R i na platformie Machine Learning Studio (klasycznej) są dostępne głównie w danych wyjściowych. log. Dodatkowe informacje znajdują się w dzienniku Error. log.

Jeśli wystąpi błąd w Machine Learning Studio (klasyczny) podczas uruchamiania kodu języka R, pierwszy przebieg akcji powinien mieć na celu wyszukanie błędu. log. Ten plik może zawierać przydatne komunikaty o błędach ułatwiające zrozumienie i poprawienie błędu. Aby wyświetlić błąd. log, wybierz pozycję **Wyświetl dziennik błędów** w okienku właściwości dla [skryptu Execute R][execute-r-script] , który zawiera błąd.

Na przykład został uruchomiony następujący kod R z niezdefiniowaną zmienną y w module [wykonywania skryptu języka r][execute-r-script] .

```r
x <- 1.0
z <- x + y
```

Nie można wykonać tego kodu, co spowoduje wystąpienie błędu. Wybranie opcji **Wyświetl dziennik błędów** w okienku właściwości powoduje wyświetlenie następującego ekranu.

  ![Zrzut ekranu, na którym jest wyświetlany komunikat o błędzie.](./media/r-quickstart/fig2.png)

Wygląda na to, że musimy przeszukać plik Output. log, aby wyświetlić komunikat o błędzie języka R. Wybierz moduł [wykonywania skryptu języka R][execute-r-script] , a następnie wybierz pozycję **Wyświetl dane wyjściowe. log** w okienku właściwości po prawej stronie. Zostanie otwarte nowe okno przeglądarki i zostanie wyświetlony następujący komunikat o błędzie.

```output
[Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
---------- Start of error message from R ----------
object 'y' not found


object 'y' not found
----------- End of error message from R -----------
```

Ten komunikat o błędzie nie zawiera żadnych niedożądanych i jasno identyfikuje problem.

Aby sprawdzić wartość dowolnego obiektu w języku R, można wydrukować te wartości w pliku Output. log. Reguły służące do badania wartości obiektów są zasadniczo takie same jak w interaktywnej sesji języka R. Na przykład, jeśli wprowadzisz nazwę zmiennej w wierszu, wartość obiektu zostanie wydrukowany do pliku wyjściowego. log.

#### <a name="packages-in-machine-learning-studio-classic"></a>Pakiety w Machine Learning Studio (klasyczne)

Machine Learning Studio (klasyczny) zawiera ponad 350 wstępnie zainstalowanych pakietów języka R. Aby pobrać listę wstępnie zainstalowanych pakietów, można użyć następującego kodu w module [wykonywania skryptu języka R][execute-r-script] .

```r
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Jeśli w tym momencie nie znasz ostatniego wiersza tego kodu, zapoznaj się z artykułem. W pozostałej części tego artykułu będziemy obszernie omówić korzystanie z języka R w środowisku Machine Learning Studio (klasycznym).

### <a name="introduction-to-rstudio"></a>Wprowadzenie do RStudio

RStudio to powszechnie używane środowisko IDE dla języka R. Będziemy używać RStudio do edytowania, testowania i debugowania niektórych kodów języka R użytych w tym przewodniku. Po przetestowaniu i przygotowaniu kodu języka R można wyciąć i wkleić z edytora RStudio do modułu [skryptu języka r][execute-r-script] Machine Learning Studio (klasycznego).

Jeśli nie masz zainstalowanego języka R programowania na komputerze stacjonarnym, zrób to teraz. Bezpłatne pobieranie języka R typu open source jest dostępne w [kompleksowej usłudze R Archive Network (Cran)](https://www.r-project.org/). Pliki do pobrania są dostępne dla systemów Windows, macOS i Linux/UNIX. Wybierz odbicie w pobliżu i postępuj zgodnie z instrukcjami pobierania. Ponadto CRAN zawiera wiele przydatnych pakietów analizy i manipulowania danymi.

Jeśli dopiero zaczynasz RStudio, należy pobrać i zainstalować wersję Desktop. Pliki do pobrania RStudio dla systemów Windows, macOS i Linux/UNIX można znaleźć w witrynie [RStudio](http://www.rstudio.com/products/RStudio/). Postępuj zgodnie z instrukcjami podanymi w celu zainstalowania RStudio na komputerze stacjonarnym.

Samouczek wprowadzenie do RStudio jest dostępny przy [użyciu środowiska IDE RStudio](https://support.rstudio.com/hc/sections/200107586-Using-RStudio).

Aby uzyskać więcej informacji na temat korzystania z programu RStudio, zobacz [Przewodnik po RStudio dokumentacji](#appendixa).

## <a name="get-data-in-and-out-of-the-execute-r-script-module"></a><a id="scriptmodule"></a>Pobieranie danych z modułu skryptu języka R i z niego

W tej sekcji omówiono sposób uzyskiwania danych do i z modułu [wykonywania skryptu języka R][execute-r-script] . Zapoznajmy się również z tematem, jak obsługiwać różne typy danych do i z modułu [wykonywania skryptów języka R][execute-r-script] .

Pełny kod dla tej sekcji znajduje się w [MachineLearningSamples — notesy/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="load-and-check-data"></a>Załaduj i sprawdź dane

#### <a name="load-the-dataset"></a><a id="loading"></a>Załaduj zestaw danych

Zaczniemy od załadowania pliku **csdairydata.csv** do Machine Learning Studio (klasyczne).

1. Uruchom środowisko Machine Learning Studio (klasyczne).
1. Wybierz pozycję **+ Nowy** w lewym dolnym rogu ekranu, a następnie wybierz pozycję **zestaw danych**.
1. Wybierz pozycję **z pliku lokalnego**, a następnie wybierz pozycję **Przeglądaj** , aby wybrać plik.
1. Upewnij się, że wybrano **ogólny plik CSV z nagłówkiem (CSV)** jako typ zestawu danych.
1. Zaznacz znacznik wyboru.
1. Po przekazaniu zestawu danych powinien zostać wyświetlony nowy zestaw danych po wybraniu karty **zestawy danych** .

#### <a name="create-an-experiment"></a>Tworzenie eksperymentu

Teraz, gdy mamy pewne dane w Machine Learning Studio (klasyczne), musimy utworzyć eksperyment, aby przeprowadzić analizę.  

1. Wybierz pozycję **+ Nowy** w lewym dolnym rogu ekranu, a następnie wybierz pozycję **eksperymentowanie**  >  **pustego eksperymentu**.
1. Nazwij ten eksperyment, wybierając i modyfikując **eksperyment utworzony na** tytule w górnej części strony. Na przykład zmień go na **przeanalizowanie mleczarni urzędu certyfikacji**.
1. Po lewej stronie eksperymentu wybierz pozycję **zapisane zestawy danych**  >  **MOJE ZESTAWY** danych. Powinien zostać wyświetlony wcześniej przekazany plik **cadairydata.csv** .
1. Przeciągnij **csdairydata.csv zestaw danych** na eksperyment.
1. W polu **Wyszukaj elementy eksperymentu** w górnej części okienka po lewej stronie wpisz polecenie [Wykonaj skrypt języka R][execute-r-script]. Moduł zostanie wyświetlony na liście wyszukiwania.
1. Przeciągnij moduł [wykonywania skryptu R][execute-r-script] na paletę.
1. Połącz dane wyjściowe **csdairydata.csv zestawu danych** z lewej strony wejściowej (**pozycję DataSet1**) [skryptu Execute języka R][execute-r-script].
1. Wybierz pozycję **Zapisz**.

W tym momencie eksperyment powinien wyglądać podobnie do tego przykładu.

![Diagram przedstawiający eksperyment analizy nabiał urzędu certyfikacji z zestawem danych i wykonywaniem modułu skryptu języka R.](./media/r-quickstart/fig3.png)


#### <a name="check-on-the-data"></a>Sprawdź dane

Spójrzmy na dane, które zostały załadowane do naszego eksperymentu. W eksperymentie wybierz dane wyjściowe **zestawu danychcadairydata.csv** i wybierz opcję **Wizualizuj**. Powinieneś zobaczyć coś takiego jak to podsumowanie.

![Zrzut ekranu pokazujący podsumowanie zestawu danych cadairydata.csv.](./media/r-quickstart/fig4.png)

Ten widok przedstawia wiele przydatnych informacji. Zobaczymy pierwsze kilka wierszy zestawu danych. W przypadku wybrania kolumny w sekcji **statystyki** zostanie wyświetlona więcej informacji o kolumnie. Na przykład wiersz **Typ funkcji** pokazuje, jakie typy danych Machine Learning Studio (klasyczne) są przypisane do kolumny. Przed rozpoczęciem pracy należy zaznaczyć ten widok.

### <a name="first-r-script"></a>Pierwszy skrypt języka R

Utwórzmy prosty pierwszy skrypt języka R, aby eksperymentować w Machine Learning Studio (klasyczny). Utworzyliśmy i przetestowano następujący skrypt w RStudio.

```r
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
str(cadairydata)
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

Teraz musimy przesłać ten skrypt do Machine Learning Studio (klasyczny). Można wyciąć i wkleić, ale w takim przypadku należy przenieść skrypt R za pośrednictwem pliku zip.

### <a name="data-input-to-the-execute-r-script-module"></a>Dane wejściowe do modułu wykonywania skryptu języka R

Przyjrzyjmy się danych wejściowych do modułu [wykonywania skryptu języka R][execute-r-script] . W tym przykładzie odczytamy dane mleczarskie Kalifornii do modułu [wykonywania skryptu języka R][execute-r-script] .

Istnieją trzy możliwe dane wejściowe modułu [skryptu Execute języka R][execute-r-script] . W zależności od aplikacji można użyć dowolnego lub wszystkich tych danych wejściowych. Możesz również użyć skryptu języka R, który nie ma żadnych danych wejściowych.

Przyjrzyjmy się każdemu z tych danych wejściowych, przechodząc od lewej do prawej. Nazwy poszczególnych danych wejściowych można zobaczyć, umieszczając kursor na wejściu i odczytując etykietkę narzędzia.

#### <a name="script-bundle"></a>Pakiet skryptu

Dane wejściowe pakietu skryptu umożliwiają przekazanie zawartości pliku zip do modułu [wykonywania skryptu języka R][execute-r-script] . Możesz użyć jednego z poniższych poleceń, aby odczytać zawartość pliku zip do kodu języka R.

```r
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> Machine Learning Studio (klasyczny) traktuje pliki w pliku zip tak, jakby znajdowały się w katalogu src/, dlatego należy prefiksować nazwy plików przy użyciu tej nazwy katalogu. Na przykład, jeśli plik zip zawiera pliki `yourfile.R` i `yourData.rdata` w katalogu głównym pliku zip, należy zająć się tymi plikami jako `src/yourfile.R` i w `src/yourData.rdata` przypadku korzystania `source` z programu i `load` .

Omawiamy już sposób ładowania zestawów [danych w załadowaniu elementu DataSet](#loading). Po utworzeniu i przetestowaniu skryptu języka R wyświetlanego w poprzedniej sekcji wykonaj następujące czynności.

1. Zapisz skrypt języka R w. Plik języka R. Simpleplot plik skryptu **. R**. Oto nowości w pliku:

   ```r
   ## Only one of the following two lines should be used
   ## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
   ## If in RStudio, use the second line with read.csv()
   cadairydata <- maml.mapInputPort(1)
   # cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
   str(cadairydata)
   pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata)
   ## The following line should be executed only when running in
   ## Azure Machine Learning Studio (classic)
   maml.mapOutputPort('cadairydata')
   ```

1. Utwórz plik zip i skopiuj skrypt do tego pliku zip. W systemie Windows można kliknąć plik prawym przyciskiem myszy i wybrać pozycję **Wyślij do**  >  **folderu skompresowanego**. Ta akcja powoduje utworzenie nowego pliku zip, który zawiera **simpleplot. Plik języka R** .

1. Dodaj plik do **zestawów danych** w Machine Learning Studio (klasyczny) i określ typ jako **zip**. Powinien być teraz widoczny plik zip w zestawach danych.

1. Przeciągnij plik zip z **zestawów danych** na **kanwę ml Studio (klasyczną)**.

1. Połącz dane wyjściowe ikony **zip danych** z **pakietem skryptu** dane wejściowe modułu [wykonywania skryptu języka R][execute-r-script] .

1. Wprowadź `source()` funkcję z nazwą pliku zip do okna kod dla modułu [wykonywania skryptu języka R][execute-r-script] . W takim przypadku wprowadzono `source("src/simpleplot.R")` .

1. Wybierz pozycję **Zapisz**.

Po zakończeniu tych kroków moduł [wykonywania skryptu języka][execute-r-script] r wykonuje skrypt języka r w pliku zip, gdy eksperyment zostanie uruchomiony. W tym momencie eksperyment powinien wyglądać podobnie do tego przykładu.

![Diagram przedstawiający eksperyment przy użyciu spakowanego skryptu języka R.](./media/r-quickstart/fig6.png)

#### <a name="dataset1"></a>Pozycję DataSet1

Można przekazać prostokątną tabelę danych do kodu języka R przy użyciu danych wejściowych pozycję DataSet1. W naszym prostym skrypcie `maml.mapInputPort(1)` Funkcja odczytuje dane z portu 1. Te dane są następnie przypisywane do nazwy zmiennej Dataframe w kodzie. W naszym prostym skrypcie pierwszy wiersz kodu wykonuje przypisanie.

```r
cadairydata <- maml.mapInputPort(1)
```

Wykonaj eksperyment, wybierając przycisk **Run (Uruchom** ). Po zakończeniu wykonywania wybierz moduł [wykonywania skryptu języka R][execute-r-script] , a następnie wybierz pozycję **Wyświetl dziennik wyjściowy** w okienku właściwości. W przeglądarce powinna zostać wyświetlona nowa strona wyświetlająca zawartość pliku wyjściowego. log. Gdy przewiniesz w dół, powinny pojawić się następujące dane wyjściowe.

```output
[ModuleOutput] InputDataStructure
[ModuleOutput]
[ModuleOutput] {
[ModuleOutput]  "InputName":Dataset1
[ModuleOutput]  "Rows":228
[ModuleOutput]  "Cols":9
[ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
[ModuleOutput] }
```

Poniżej znajduje się bardziej szczegółowe informacje na temat kolumn, które będą wyglądały podobnie jak następujące dane wyjściowe.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput]
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput]
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput]
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput]
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput]
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput]
[ModuleOutput]  $ Month            : chr  "Jan" "Feb" "Mar" "Apr" ...
[ModuleOutput]
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput]
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput]
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput]
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
```

Te wyniki są w większości zgodnie z oczekiwaniami, z 228 obserwacji i 9 kolumnami w ramce Dataframe. Możemy zobaczyć nazwy kolumn, typ danych języka R i próbkę każdej kolumny.

> [!NOTE]
> Te same wydruki są wygodnie dostępne w danych wyjściowych urządzenia R modułu [skryptu Execute r][execute-r-script] . Będziemy omawiać dane wyjściowe modułu [wykonywania skryptu języka R][execute-r-script] w następnej sekcji.  

#### <a name="dataset2"></a>Dataset2

Zachowanie danych wejściowych Dataset2 jest identyczne z pozycję DataSet1. Korzystając z tych danych wejściowych, można przekazać drugą prostokątną tabelę danych do kodu języka R. Funkcja `maml.mapInputPort(2)` z argumentem 2 służy do przekazywania tych danych.  

### <a name="execute-r-script-outputs"></a>Wykonaj dane wyjściowe skryptu języka R

#### <a name="output-a-dataframe"></a>Wyprowadzanie danych wyjściowych

Można wyprowadzić zawartość ramki danych języka R jako prostokątną tabelę za pośrednictwem portu pozycję DataSet1 wyniku przy użyciu `maml.mapOutputPort()` funkcji. W naszym prostym skrypcie języka R ta akcja jest wykonywana przez następujący wiersz.

```r
maml.mapOutputPort('cadairydata')
```

Po uruchomieniu eksperymentu wybierz port wyjściowy pozycję DataSet1 wynik, a następnie wybierz opcję **Wizualizuj**. Powinieneś zobaczyć coś takiego jak w tym przykładzie.

![Zrzut ekranu, który pokazuje wizualizację danych wyjściowych z danymi mleczarskimi Kalifornii.](./media/r-quickstart/fig7.png)

Dane wyjściowe wyglądają identycznie z danymi wejściowymi, dokładnie tak jak w przypadku oczekiwań.

### <a name="r-device-output"></a>Dane wyjściowe urządzenia R

Dane wyjściowe urządzenia modułu [wykonywania skryptu języka R][execute-r-script] zawierają komunikaty i dane wyjściowe grafiki. Do portu wyjściowego urządzenia R są wysyłane zarówno standardowe, jak i standardowe komunikaty o błędach z języka R.

Aby wyświetlić dane wyjściowe urządzenia R, wybierz port, a następnie wybierz opcję **Wizualizuj**. Tutaj widzimy standardowe wyjście i standardowy błąd w skrypcie języka R.

![Zrzut ekranu pokazujący standardowe wyjście i standardowy błąd z portu urządzenia R.](./media/r-quickstart/fig8.png)

Przewiń w dół, zobaczysz dane wyjściowe grafiki z naszego skryptu języka R.

![Zrzut ekranu pokazujący dane wyjściowe grafiki z portu urządzenia R.](./media/r-quickstart/fig9.png)

## <a name="data-filtering-and-transformation"></a><a id="filtering"></a>Filtrowanie i Przekształcanie danych

W tej sekcji wykonamy pewne podstawowe operacje filtrowania i przekształcania danych na danych mlecznych Kalifornii. Na końcu tej sekcji będziemy używać danych w formacie odpowiednim do tworzenia modelu analitycznego.

Dokładniej, w tej sekcji wykonamy kilka typowych zadań związanych z czyszczeniem i przekształcaniem danych: przekształcanie typów, filtrowanie danych ramek, dodawanie nowych kolumn obliczanych i przekształceń wartości. To tło powinno pomóc w zapoznaniu się z wieloma odmianami napotkanymi w rzeczywistych problemach.

Pełny kod R dla tej sekcji jest dostępny w [MachineLearningSamples — notesy/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="type-transformations"></a>Przekształcenia typów

Teraz, gdy możemy odczytywać dane mleczarskie Kalifornii do kodu R w module [wykonywania skryptu języka r][execute-r-script] , musimy upewnić się, że dane w kolumnach mają żądany typ i format.

R jest językiem z typem dynamicznym, co oznacza, że typy danych są przekształcane z jednego do drugiego zgodnie z potrzebami. Typy danych niepodzielnych w języku R obejmują wartości liczbowe, logiczne i znak. Typ czynnika służy do kompaktowania przechowywania danych kategorii. Aby uzyskać więcej informacji na temat typów danych, zobacz odwołania w [dalszej](#appendixb)części.

Gdy dane tabelaryczne są odczytywane w języku R z zewnętrznego źródła, zawsze dobrym pomysłem jest sprawdzenie typów wyników w kolumnach. Możesz potrzebować kolumny typu Character, ale w wielu przypadkach kolumna będzie wyświetlana jako współczynnik lub odwrotnie. W innych przypadkach kolumna, której zdaniem powinna być wartością liczbową, jest reprezentowana przez dane znakowe, na przykład "1,23", a nie 1,23 jako liczba zmiennoprzecinkowa.

Na szczęście można łatwo skonwertować jeden typ na inny, tak długo jak mapowanie jest możliwe. Na przykład nie można skonwertować "Nevada" na wartość liczbową, ale można ją przekonwertować na współczynnik (zmienną kategorii). Innym przykładem może być konwertowanie wartości liczbowej 1 na znak "1" lub "współczynnik".

Składnia dla którejkolwiek z tych konwersji jest prosta: `as.datatype()` . Te funkcje konwersji typów obejmują następujące funkcje:

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Przeglądając typy danych kolumn, które wprowadzamy w poprzedniej sekcji, wszystkie kolumny są typu liczbowego. Wyjątkiem jest kolumna o nazwie "Month", która jest typu Character. Konwertujmy ten typ na współczynnik i przetestuj wyniki.

Usunął linię, która utworzyła macierz wykresu punktowego i dodała linię do konwersji kolumny miesiąc na współczynnik. W tym doświadczeniu wytniemy kod R i wkleisz go w oknie kodu modułu [wykonywania skryptu języka r][execute-r-script] . Możesz również zaktualizować plik zip i przekazać go do Machine Learning Studio (klasyczny), ale ta opcja wykonuje kilka kroków.

```r
## Only one of the following two lines should be used
## If running in Machine Learning Studio (classic), use the first line with maml.mapInputPort()
## If in RStudio, use the second line with read.csv()
cadairydata <- maml.mapInputPort(1)
# cadairydata  <- read.csv("cadairydata.csv", header = TRUE, stringsAsFactors = FALSE)
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(cadairydata$Month)
str(cadairydata) # Check the result
## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('cadairydata')
```

Wykonajmy ten kod i przejrzyj dziennik wyjściowy dla skryptu języka R. Odpowiednie dane z dziennika są wyświetlane w tym miejscu.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 14 levels "Apr","April",..: 6 5 9 1 11 8 7 3 14 13 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

Typ miesiąca powinien teraz powiedzieć współczynnik z **/14 poziomów**. Ten typ jest problemem, ponieważ w roku występuje tylko 12 miesięcy. Możesz również sprawdzić, czy typ w **wizualizacji** portu zestawu danych wyników to **kategorii**.

Problem polega na tym, że kolumna month nie została systematycznie zakodowana. W niektórych przypadkach miesiąc jest określany jako Kwiecień, a w innych — skrócony. Możemy rozwiązać ten problem, przycinania ciągu do trzech znaków. Wiersz kodu wygląda teraz podobnie jak w poniższym przykładzie.

```r
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Uruchom eksperyment i Wyświetl dziennik danych wyjściowych. Oczekiwane wyniki są wyświetlane w tym miejscu.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Column 0         : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year.Month       : num  1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```


Nasza zmienna Factor ma teraz żądane 12 poziomów.

### <a name="basic-dataframe-filtering"></a>Podstawowe filtrowanie ramek Dataframe

Ramki danych języka R obsługują zaawansowane możliwości filtrowania. Zestawy danych mogą być podzbiorowe za pomocą filtrów logicznych w wierszach lub kolumnach. W wielu przypadkach wymagane są złożone kryteria filtrowania. Aby uzyskać obszerne przykłady filtrowania ramek danych, zobacz odwołania w [dalszej](#appendixb)części.

Istnieje jeden bit filtrowania, który powinienmy wykonać na naszym zestawie danych. Jeśli przeszukasz kolumny w ramce datacadairydata, zobaczysz dwie niepotrzebne kolumny. Pierwsza kolumna zawiera numer wiersza, który nie jest bardzo użyteczny. Druga kolumna, Year. month, zawiera nadmiarowe informacje. Można łatwo wykluczyć te kolumny przy użyciu następującego kodu R.

> [!NOTE]
> Od teraz w tej sekcji pokażemy dodatkowy kod, który dodajemy w module [wykonywania skryptu języka R][execute-r-script] . Każdy nowy wiersz zostanie dodany *przed* `str()` funkcją. Ta funkcja służy do weryfikowania wyników w Machine Learning Studio (klasyczne).

Dodajemy następujący wiersz do kodu języka R w module [wykonywania skryptu języka r][execute-r-script] .

```r
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Uruchom ten kod w eksperymentie i sprawdź wynik z dziennika danych wyjściowych. Te wyniki są wyświetlane w tym miejscu.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  7 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

Otrzymamy teraz oczekiwane wyniki.

### <a name="add-a-new-column"></a>Dodaj nową kolumnę

Aby utworzyć modele szeregów czasowych, wygodnie będzie mieć kolumnę zawierającą miesiące od początku szeregu czasowego. Utworzymy nową kolumnę month. Count.

Aby ułatwić organizowanie kodu, utworzysz pierwszą prostą funkcję, `num.month()` . Następnie zastosujemy tę funkcję, aby utworzyć nową kolumnę w ramce Dataframe. Nowy kod jest następujący:

```r
## Create a new column with the month count
## Function to find the number of months from the first
## month of the time series
num.month <- function(Year, Month) {
  ## Find the starting year
  min.year  <- min(Year)

  ## Compute the number of months from the start of the time series
  12 * (Year - min.year) + Month - 1
}

## Compute the new column for the dataframe
cadairydata$Month.Count <- num.month(cadairydata$Year, cadairydata$Month.Number)
```

Teraz można uruchomić zaktualizowany eksperyment i wyświetlić wyniki przy użyciu dziennika danych wyjściowych. Te wyniki są wyświetlane w tym miejscu.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  4.37 3.69 4.54 4.28 4.47 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  51.6 56.1 68.5 65.7 73.7 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  2.11 1.93 2.16 2.13 2.23 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  0.98 0.892 0.892 0.897 0.897 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```


Wygląda na to, że wszystko działa. Mamy nową kolumnę o oczekiwanych wartościach w naszej ramce Dataframe.

### <a name="value-transformations"></a>Przekształcenia wartości

W tej sekcji wykonamy niektóre proste przekształcenia dotyczące wartości w niektórych kolumnach ramki Dataframe. Język R obsługuje niemal dowolne przekształcenia wartości. Aby uzyskać więcej przykładów, zobacz odwołania w [dalszej](#appendixb)części.

Jeśli zobaczysz wartości w podsumowaniu ramki Dataframe, zobaczysz coś niewidocznego w tym miejscu. Czy jest więcej lodów niż mleko produkowane w Kalifornii? Nie. Problem polega na tym, że jednostki są różne. Cena jest w jednostkach funtów amerykańskich, a mleko jest w jednostkach 1 000 000 amerykańskich funtów, lody w odniesieniu do lodów jest w jednostkach 1 000 w USA, a ser Cottage jest w jednostkach 1 000 funtów amerykańskich. Przy założeniu, że lody odpada około 6,5 funtów na galon, możemy łatwo wykonać mnożenie, aby przekonwertować te wartości tak, aby znajdowały się one w równych jednostkach 1 000 funtów.

W naszym modelu prognozowania używamy modelu mnożenia na potrzeby trendu i sezonowego dostosowywania tych danych. Przekształcenie dziennika pozwala nam korzystać z modelu liniowego, co upraszcza ten proces. Możemy zastosować transformację dziennika w tej samej funkcji, w której jest stosowany mnożnik.

W poniższym kodzie definiujemy nową funkcję, `log.transform()` i zastosujemy ją do wierszy, które zawierają wartości liczbowe. Funkcja R `Map()` służy do zastosowania `log.transform()` funkcji do wybranych kolumn w ramce Dataframe. `Map()`Funkcja jest podobna do `apply()` , ale zezwala na więcej niż jedną listę argumentów funkcji. Należy zauważyć, że lista mnożników dostarcza drugi argument do `log.transform()` funkcji. `na.omit()`Funkcja jest używana jako bit oczyszczania, aby upewnić się, że nie mamy lub nie zdefiniowano wartości w ramce Dataframe.

```r
log.transform <- function(invec, multiplier = 1) {
  ## Function for the transformation, which is the log
  ## of the input value times a multiplier

  warningmessages <- c("ERROR: Non-numeric argument encountered in function log.transform",
                       "ERROR: Arguments to function log.transform must be greate than zero",
                       "ERROR: Aggurment multiplier to funcition log.transform must be a scaler",
                       "ERROR: Invalid time seies value encountered in function log.transform"
                       )

  ## Check the input arguments
  if(!is.numeric(invec) | !is.numeric(multiplier)) {warning(warningmessages[1]); return(NA)}  
  if(any(invec < 0.0) | any(multiplier < 0.0)) {warning(warningmessages[2]); return(NA)}
  if(length(multiplier) != 1) {{warning(warningmessages[3]); return(NA)}}

  ## Wrap the multiplication in tryCatch
  ## If there is an exception, print the warning message to
  ## standard error and return NA
  tryCatch(log(multiplier * invec),
           error = function(e){warning(warningmessages[4]); NA})
}


## Apply the transformation function to the 4 columns
## of the dataframe with production data
multipliers  <- list(1.0, 6.5, 1000.0, 1000.0)
cadairydata[, 4:7] <- Map(log.transform, cadairydata[, 4:7], multipliers)

## Get rid of any rows with NA values
cadairydata <- na.omit(cadairydata)  
```

W funkcji występuje zbyt wiele `log.transform()` . Większość tego kodu sprawdza potencjalne problemy z argumentami lub w odniesieniu do wyjątków, które nadal mogą wystąpić podczas obliczeń. Tylko kilka wierszy tego kodu wykona obliczenia.

Celem programowania obronnego jest uniknięcie awarii pojedynczej funkcji, która uniemożliwia kontynuowanie przetwarzania. Nieoczekiwana awaria długotrwałej analizy może być frustrujące dla użytkowników. Aby uniknąć tej sytuacji, należy wybrać domyślne wartości zwracane, które spowodują ograniczenie szkód do przetwarzania podrzędnego. Zostanie również wygenerowany komunikat informujący użytkowników o niepowodzeniu.

Jeśli nie są używane do obronnego programowania w języku R, cały ten kod może być przeciążony. Przejdźmy do najważniejszych kroków:

1. Zdefiniowano wektor czterech komunikatów. Komunikaty te służą do przekazywania informacji o niektórych możliwych błędach i wyjątkach, które mogą wystąpić w przypadku tego kodu.
1. Dla każdego przypadku zwracamy wartość "na". Istnieje wiele innych możliwości, które mogą mieć mniej efektów ubocznych. Możemy na przykład zwrócić wektor zer lub oryginalny wektor wejściowy.
1. Testy są uruchamiane na argumentach funkcji. W każdym przypadku, jeśli zostanie wykryty błąd, zwracana jest wartość domyślna, a komunikat jest generowany przez `warning()` funkcję. Używamy `warning()` zamiast `stop()` tego, ponieważ drugie zakończy wykonywanie, co jest potrzebne do uniknięcia. Ten kod jest pisany w stylu proceduralnym, ponieważ w tym przypadku podejście funkcjonalne zostało złożone i zasłonięte.
1. Obliczenia dzienników są opakowane w `tryCatch()` taki sposób, aby wyjątki nie powodowały nieoczekiwanego zatrzymania do przetwarzania. Bez `tryCatch()` , większość błędów wywoływanych przez funkcje języka R powoduje sygnał zatrzymania, co oznacza, że tylko.

Wykonaj ten kod R w eksperymentie i obejrzyj wydruk wyjściowy w pliku Output. log. Zobaczysz teraz przekształcone wartości czterech kolumn w dzienniku, jak pokazano poniżej.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput] [1] "Saving variable  cadairydata  ..."
[ModuleOutput] 
[ModuleOutput] [1] "Saving the following item(s):  .maml.oport1"
```

Zobaczymy, że wartości zostały przekształcone. Produkcja mleka jest teraz znacznie większa niż w przypadku wszystkich innych produkcji produktu mleczarskiego, co pozwala na przeszukanie na skalę dzienników.

W tym momencie nasze dane są czyszczone i jesteśmy gotowi do pewnej modelowania. Jeśli zobaczysz podsumowanie wizualizacji dla danych wyjściowych zestawu wyników wykonanych przez nasz moduł [skryptu języka R][execute-r-script] , zobaczysz, że kolumna month jest kategorii z 12 unikatowymi wartościami, które są równie odpowiednie.

## <a name="time-series-objects-and-correlation-analysis"></a><a id="timeseries"></a>Obiekty szeregów czasowych i analiza korelacji

W tej sekcji pokażemy kilka podstawowych obiektów szeregów czasowych R i przeanalizuje korelacje między niektórymi zmiennymi. Naszym celem jest wyjście ramki danych, która zawiera informacje o korelacji parowania w kilku spowolnienia.

Kompletny kod języka R dla tej sekcji znajduje się w [MachineLearningSamples — notesy/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="time-series-objects-in-r"></a>Obiekty szeregów czasowych w języku R

Jak już wspomniano, szeregi czasowe są serią wartości danych indeksowanych według czasu. Obiekty szeregów czasowych języka R są używane do tworzenia i zarządzania indeksem czasu. Istnieje kilka zalet używania obiektów szeregów czasowych. Obiekty szeregów czasowych uwalniają Cię od wielu szczegółów dotyczących zarządzania wartościami indeksów szeregów czasowych, które są hermetyzowane w obiekcie. Ponadto obiekty szeregów czasowych umożliwiają użycie różnych metod szeregów czasowych do wykreślania, drukowania, modelowania i tak dalej.

Klasa szeregów czasowych POSIXct jest często używana i jest stosunkowo prosta. Ta klasa szeregów czasowych mierzy czas od rozpoczęcia epoki, 1 stycznia 1970. W tym przykładzie będziemy używać POSIXct obiektów szeregów czasowych. Inne powszechnie używane klasy obiektów szeregów czasowych języka R to zoo i XTS (rozszerzalne serie czasowe).

### <a name="time-series-object-example"></a>Przykład obiektu szeregów czasowych

Zacznijmy od naszego przykładu. Przeciągnij nowy moduł [skryptu języka R][execute-r-script] do eksperymentu. Połącz port wyjściowy pozycję DataSet1 wyniku istniejącego modułu [wykonywania skryptu języka r][execute-r-script] z portem wejściowym pozycję DataSet1 nowego modułu [skryptu Execute języka r][execute-r-script] .

Tak jak w przypadku pierwszych przykładów, zajmiemy się tym przykładem. W niektórych punktach pokazywane są tylko przyrostowe dodatkowe wiersze kodu R w każdym kroku.

#### <a name="read-the-dataframe"></a>Odczytaj ramkę danych

Pierwszym krokiem jest odczytanie w ramce Dataframe i upewnienie się, że otrzymamy oczekiwane wyniki. Poniższy kod powinien wykonać zadanie.

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Teraz uruchom eksperyment. Dziennik nowego kształtu skryptu Execute R powinien wyglądać podobnie do tego przykładu.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  8 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
```

Te dane mają oczekiwane typy i format. Teraz kolumna month jest typu Factor i ma oczekiwaną liczbę poziomów.

#### <a name="create-a-time-series-object"></a>Tworzenie obiektu szeregów czasowych

Musimy dodać obiekt szeregów czasowych do naszej ramki danych. Zastąp bieżący kod następującym kodem, który dodaje nową kolumnę klasy POSIXct.

```r
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

Teraz sprawdź dziennik. Powinien on wyglądać podobnie do tego przykładu.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...
```

Możemy zobaczyć podsumowanie, że nowa kolumna jest w rzeczywistości klasy POSIXct.

### <a name="explore-and-transform-the-data"></a>Eksplorowanie i Przekształcanie danych

Zapoznajmy się z niektórymi zmiennymi w tym zestawie danych. Macierz wykresów punktowych to dobry sposób na utworzenie szybkiego wyglądu. Zamienimy `str()` funkcję w poprzednim kodzie R z następującym wierszem.

```r
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Uruchom ten kod i zobacz, co się dzieje. Wykres utworzony na porcie urządzenia R powinien wyglądać podobnie do tego przykładu.

![Zrzut ekranu, który pokazuje macierz wykresu punktowego dla wybranych zmiennych.](./media/r-quickstart/fig17.png)

Relacje między tymi zmiennymi mają nieparzystą strukturę. Być może Ta struktura wynika z trendów danych i od faktu, że zmienne nie zostały znormalizowane.

### <a name="correlation-analysis"></a>Analiza korelacji

W celu przeprowadzenia analizy korelacji musimy zarówno usunąć trend, jak i znormalizować zmienne. Można po prostu użyć funkcji języka R `scale()` , która w obu centrach i skaluje zmienne. Ta funkcja może być również szybsza. Przyjrzyjmy się przykładowi programowania obronnego w języku R.

`ts.detrend()`Poniższa funkcja wykonuje obie te operacje. Poniższe dwa wiersze kodu usuwają trendy danych, a następnie standaryzacją wartości.

```r
ts.detrend <- function(ts, Time, min.length = 3){
  ## Function to de-trend and standardize a time series

  ## Define some messages if they are NULL  
  messages <- c('ERROR: ts.detrend requires arguments ts and Time to have the same length',
                'ERROR: ts.detrend requires argument ts to be of type numeric',
                paste('WARNING: ts.detrend has encountered a time series with length less than', as.character(min.length)),
                'ERROR: ts.detrend has encountered a Time argument not of class POSIXct',
                'ERROR: Detrend regression has failed in ts.detrend',
                'ERROR: Exception occurred in ts.detrend while standardizing time series in function ts.detrend'
  )
  # Create a vector of zeros to return as a default in some cases
  zerovec  <- rep(length(ts), 0.0)

  # The input arguments are not of the same length, return ts and quit
  if(length(Time) != length(ts)) {warning(messages[1]); return(ts)}

  # If the ts is not numeric, just return a zero vector and quit
  if(!is.numeric(ts)) {warning(messages[2]); return(zerovec)}

  # If the ts is too short, just return it and quit
  if((ts.length <- length(ts)) < min.length) {warning(messages[3]); return(ts)}

  ## Check that the Time variable is of class POSIXct
  if(class(cadairydata$Time)[[1]] != "POSIXct") {warning(messages[4]); return(ts)}

  ## De-trend the time series by using a linear model
  ts.frame  <- data.frame(ts = ts, Time = Time)
  tryCatch({ts <- ts - fitted(lm(ts ~ Time, data = ts.frame))},
           error = function(e){warning(messages[5]); zerovec})

  tryCatch( {stdev <- sqrt(sum((ts - mean(ts))^2))/(ts.length - 1)
             ts <- ts/stdev},
            error = function(e){warning(messages[6]); zerovec})

  ts
}  
## Apply the detrend.ts function to the variables of interest
df.detrend <- data.frame(lapply(cadairydata[, 4:7], ts.detrend, cadairydata$Time))

## Plot the results to look at the relationships
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = df.detrend, main = "Pairwise Scatterplots of detrended standardized time series")
```

W funkcji występuje zbyt wiele `ts.detrend()` . Większość tego kodu sprawdza potencjalne problemy z argumentami lub w odniesieniu do wyjątków, które nadal mogą wystąpić podczas obliczeń. Tylko kilka wierszy tego kodu faktycznie przeprowadza obliczenia.

Omawiamy już przykład programowania obronnego w transformacjach wartości. Oba bloki obliczeń są opakowane w `tryCatch()` . Dla niektórych błędów warto zwrócić oryginalny wektor wejściowy. W innych przypadkach zwracamy wektor zer.

Należy zauważyć, że regresja liniowa używana na potrzeby detrendu jest regresją szeregów czasowych. Zmienna predykcyjna to obiekt szeregów czasowych.

Po `ts.detrend()` zdefiniowaniu, stosujemy go do zmiennych interesujących w naszej ramce Dataframe. Musimy przekształcić wynikową listę utworzoną przez program `lapply()` na dane w ramce Dataframe przy użyciu polecenia `as.data.frame()` . Ze względu na aspekty obronne `ts.detrend()` , Niepowodzenie przetworzenia jednej ze zmiennych nie uniemożliwi prawidłowego przetwarzania innych.

Ostatni wiersz kodu tworzy wykres punktowy parowania. Po uruchomieniu kodu języka R, wyniki wykresu punktowego są wyświetlane w tym miejscu.

![Zrzut ekranu pokazujący wykres punktowy parowania z nietrendową i ustandaryzowaną serią czasową.](./media/r-quickstart/fig18.png)

Wyniki te można porównać z pokazanymi w poprzednim przykładzie. Po usunięciu trendu i znormalizowanych zmiennych zostanie wyświetlona znacznie mniejsza struktura relacji między tymi zmiennymi.

Kod do obliczenia korelacji jako obiektów R CCF jest następujący.

```r
## A function to compute pairwise correlations from a
## list of time series value vectors
pair.cor <- function(pair.ind, ts.list, lag.max = 1, plot = FALSE){
  ccf(ts.list[[pair.ind[1]]], ts.list[[pair.ind[2]]], lag.max = lag.max, plot = plot)
}

## A list of the pairwise indices
corpairs <- list(c(1,2), c(1,3), c(1,4), c(2,3), c(2,4), c(3,4))

## Compute the list of ccf objects
cadairycorrelations <- lapply(corpairs, pair.cor, df.detrend)  

cadairycorrelations
```

Uruchomienie tego kodu spowoduje utworzenie dziennika wyświetlonego tutaj.

```output
[ModuleOutput] Loading objects:
[ModuleOutput]   port1
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] [[1]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]    -1     0     1 
[ModuleOutput] 0.148 0.358 0.317 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[2]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.395 -0.186 -0.238 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[3]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.059 -0.089 -0.127 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[4]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]    -1     0     1 
[ModuleOutput] 0.140 0.294 0.293 
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] [[5]]
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput] Autocorrelations of series 'X', by lag
[ModuleOutput] 
[ModuleOutput] 
[ModuleOutput]     -1      0      1 
[ModuleOutput] -0.002 -0.074 -0.124 
```

Dla każdego opóźnienia istnieje wartość korelacji. Żadna z tych wartości korelacji nie jest wystarczająco duża, aby była znacząca. Możemy zawrzeć, że możemy niezależnie modelować każdą zmienną.

### <a name="output-a-dataframe"></a>Wyprowadzanie danych wyjściowych
Przeliczyłeś korelacje parowania jako listę obiektów CCF R. Przedstawia to bit problemu, ponieważ port wyjściowy zestawu danych wynikowych rzeczywiście wymaga ramki Dataframe. Ponadto obiekt CCF jest samym listą i chcemy, aby tylko wartości z pierwszego elementu tej listy, korelacje w różnych spowolnieniaach.

Poniższy kod wyodrębnia wartości zwłok z listy obiektów CCF, które są same.

```r
df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
r.names  <- c("Corr Cot Cheese - Ice Cream",
              "Corr Cot Cheese - Milk Prod",
              "Corr Cot Cheese - Fat Price",
              "Corr Ice Cream - Mik Prod",
              "Corr Ice Cream - Fat Price",
              "Corr Milk Prod - Fat Price")

## Build a dataframe with the row names column and the
## correlation dataframe and assign the column names
outframe <- cbind(r.names, df.correlations)
colnames(outframe) <- c.names
outframe


## WARNING!
## The following line works only in Azure Machine Learning Studio (classic)
## When running in RStudio, this code will result in an error
#maml.mapOutputPort('outframe')
```

Pierwszy wiersz kodu jest niepewną bitową, a niektóre wyjaśnienia mogą pomóc je zrozumieć. Pracując od wewnątrz, mamy:

1. Operator **[[** z argumentem **1** wybiera wektor korelacji w spowolnienia z pierwszego elementu listy obiektów CCF.
1. `do.call()`Funkcja stosuje `rbind()` funkcję względem elementów listy zwraca przez `lapply()` .
1. `data.frame()`Funkcja przekształca wynik tworzony przez `do.call()` element na ramkę danych.

Należy zauważyć, że nazwy wierszy znajdują się w kolumnie ramki danych. Wykonanie tej operacji zachowuje nazwy wierszy, gdy są one wyprowadzane ze [skryptu Execute języka R][execute-r-script].

Uruchomienie kodu spowoduje utworzenie danych wyjściowych w tym miejscu, gdy wybierzemy **wizualizację** , aby wyświetlić dane wyjściowe w porcie zestawu danych wynikowych. Nazwy wierszy znajdują się w pierwszej kolumnie zgodnie z oczekiwaniami.

![Zrzut ekranu pokazujący wyniki analizy korelacji.](./media/r-quickstart/fig20.png)

## <a name="time-series-example-seasonal-forecasting"></a><a id="seasonalforecasting"></a>Przykład szeregów czasowych: prognozowanie sezonowe

Nasze dane znajdują się teraz w formie odpowiedniej do analizy, a firma Microsoft ustaliła, że nie ma znaczących korelacji między zmiennymi. Przyjrzyjmy się i utworzysz model prognozowania szeregów czasowych. Przy użyciu tego modelu będziemy prognozować produkcję mleka Kalifornii przez 12 miesięcy z 2013.

Nasz model prognozowania będzie miał dwa składniki, składnik trendu i składnik sezonowy. Pełną prognozą jest iloczyn tych dwóch składników. Ten typ modelu jest znany jako model mnożenia. Alternatywą jest model dodatków. Zastosowałeś już transformację dziennika do interesujących zmiennych, co sprawia, że ta analiza jest pożądana.

Kompletny kod języka R dla tej sekcji znajduje się w [MachineLearningSamples — notesy/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="create-the-dataframe-for-analysis"></a>Tworzenie ramki Dataframe do analizy

Zacznij od dodania nowego modułu [skryptu wykonania języka R][execute-r-script] do Twojego eksperymentu. Połącz dane wyjściowe zestawu danych wyników istniejącego modułu [wykonywania skryptu języka R][execute-r-script] z **pozycję DataSet1** wejściem nowego modułu. Wynik powinien wyglądać podobnie do tego przykładu.

![Diagram pokazujący eksperyment z nowym modułem wykonywania skryptu języka R.](./media/r-quickstart/fig21.png)

Podobnie jak w przypadku analizy korelacji, musimy dodać kolumnę z POSIXct obiektem szeregów czasowych. Poniższy kod doda kolumnę.

```r
# If running in Machine Learning Studio (classic), uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

Uruchom ten kod i sprawdź dziennik. Wynik powinien wyglądać podobnie do tego przykładu.

```output
[ModuleOutput] [1] "Loading variable port1..."
[ModuleOutput] 
[ModuleOutput] 'data.frame':    228 obs. of  9 variables:
[ModuleOutput] 
[ModuleOutput]  $ Month.Number     : int  1 2 3 4 5 6 7 8 9 10 ...
[ModuleOutput] 
[ModuleOutput]  $ Year             : int  1995 1995 1995 1995 1995 1995 1995 1995 1995 1995 ...
[ModuleOutput] 
[ModuleOutput]  $ Month            : Factor w/ 12 levels "Apr","Aug","Dec",..: 5 4 8 1 9 7 6 2 12 11 ...
[ModuleOutput] 
[ModuleOutput]  $ Cotagecheese.Prod: num  1.47 1.31 1.51 1.45 1.5 ...
[ModuleOutput] 
[ModuleOutput]  $ Icecream.Prod    : num  5.82 5.9 6.1 6.06 6.17 ...
[ModuleOutput] 
[ModuleOutput]  $ Milk.Prod        : num  7.66 7.57 7.68 7.66 7.71 ...
[ModuleOutput] 
[ModuleOutput]  $ N.CA.Fat.Price   : num  6.89 6.79 6.79 6.8 6.8 ...
[ModuleOutput] 
[ModuleOutput]  $ Month.Count      : num  0 1 2 3 4 5 6 7 8 9 ...
[ModuleOutput] 
[ModuleOutput]  $ Time             : POSIXct, format: "1995-01-01" "1995-02-01" ...
```

Dzięki temu jesteśmy gotowi do rozpoczęcia analizy.

### <a name="create-a-training-dataset"></a>Tworzenie zestawu danych szkoleniowych

Z konstruowaną ramką Dataframe musimy utworzyć zestaw danych szkoleniowych. Te dane obejmują wszystkie obserwacje z wyjątkiem ostatnich 12, roku 2013, który jest naszym testowym zestawem danych. Poniższy kod podpisuje ramkę Dataframe i tworzy wykresy produkcji mlecznej i zmiennych cenowych. Następnie tworzymy wykresy czterech zmiennych produkcyjnych i cen. Funkcja anonimowa służy do definiowania niektórych rozszerzeń dla wykresu, a następnie iteracji na liście pozostałych dwóch argumentów za pomocą `Map()` . Jeśli zastanawiasz się, że pętla for będzie działała prawidłowo, nastąpi poprawne rozwiązanie. Ale ponieważ R to język funkcjonalny, jest to podejście funkcjonalne.

```r
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

Uruchomienie kodu powoduje utworzenie serii wykresów czasowych z danych wyjściowych urządzenia R przedstawionych tutaj. Oś czasu jest w jednostkach dat, co jest świetną zaletą metody kreślenia szeregów czasowych.

![Pierwszy wykres szeregów czasowych Kalifornii produkcja mleczna i dane cen.](./media/r-quickstart/unnamed-chunk-161.png)

![Drugi wykres szeregów czasowych Kalifornii produkcja mleczna i dane cen.](./media/r-quickstart/unnamed-chunk-162.png)

![Trzeci wykres szeregów czasowych Kalifornii produkcja mleczna i dane cen.](./media/r-quickstart/unnamed-chunk-163.png).

![Czwarty wykres szeregów czasowych Kalifornii produkcja mleczna i dane cen](./media/r-quickstart/unnamed-chunk-164.png)

### <a name="a-trend-model"></a>Model trendu

Teraz, po utworzeniu obiektu szeregów czasowych i zapoznaniu się z danymi, przyjrzyjmy się modelowi trendu dla danych produkcyjnych z mleka California. Możemy użyć regresji szeregów czasowych. Jest jasne od wykresu, który potrzebuje więcej niż nachylenie i przechwycenie, aby dokładnie modelować obserwowany trend w danych szkoleniowych.

Mając na względzie małą skalę danych, utworzymy model trendu w RStudio, a następnie wytniesz i wkleisz model wynikający z Machine Learning Studio (klasyczny). RStudio zapewnia interaktywny środowisko dla tego typu interaktywnej analizy.

Przy pierwszej próbie wypróbuje regresję wielomianową z uprawnieniami do trzech. Istnieje realne niebezpieczeństwo zbyt dobrze dopasowanego rodzaju modeli. Najlepiej jest unikać warunków. `I()`Funkcja wstrzymuje interpretację zawartości (interpretuje zawartość jako) i umożliwia pisanie dosłownie interpretowanej funkcji w równaniu regresji.

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

Ta funkcja generuje następujące dane wyjściowe.

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3),
##     data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.12667 -0.02730  0.00236  0.02943  0.10586
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## (Intercept)       6.33e+00   1.45e-01   43.60   <2e-16 ***
## Time              1.63e-09   1.72e-10    9.47   <2e-16 ***
## I(Month.Count^2) -1.71e-06   4.89e-06   -0.35    0.726
## I(Month.Count^3) -3.24e-08   1.49e-08   -2.17    0.031 *  
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0418 on 212 degrees of freedom
## Multiple R-squared:  0.941,    Adjusted R-squared:  0.94
## F-statistic: 1.12e+03 on 3 and 212 DF,  p-value: <2e-16
```

Od wartości P ( `Pr(>|t|)` ) w tych danych wyjściowych widać, że kwadratowy termin może nie być znaczący. Użyjemy funkcji, `update()` Aby zmodyfikować ten model poprzez upuszczenie kwadratowego okresu.

```r
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

Ta funkcja generuje następujące dane wyjściowe.

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.12597 -0.02659  0.00185  0.02963  0.10696
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## (Intercept)       6.38e+00   4.07e-02   156.6   <2e-16 ***
## Time              1.57e-09   4.32e-11    36.3   <2e-16 ***
## I(Month.Count^3) -3.76e-08   2.50e-09   -15.1   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0417 on 213 degrees of freedom
## Multiple R-squared:  0.941,  Adjusted R-squared:  0.94
## F-statistic: 1.69e+03 on 2 and 213 DF,  p-value: <2e-16
```

Dane wyjściowe wyglądają lepiej. Wszystkie warunki są istotne. Wartość 2e-16 jest wartością domyślną i nie należy jej robić zbyt poważnie.  

W ramach testu Sanity Przyjrzyjmy się wykreśleniem szeregów czasowych danych produkcyjnych w stanie Kalifornia z pokazaną krzywą trendu. Dodaliśmy następujący kod w Machine Learning Studio (klasyczny) model [skryptu][execute-r-script] RStudio (nie), aby utworzyć model i stworzyć wykres. Wynik jest przedstawiony w poniższym przykładzie.

```r
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![Pokazuje dane produkcji z mleka Kalifornii z modelem trendu.](./media/r-quickstart/unnamed-chunk-18.png)

Wygląda na to, że model trendu pasuje do danych dość dobrze. Ponadto nie wydaje się, że jest dowodem nad nadmiernym dopasowaniem, na przykład nieparzystą wigglesą w krzywej modelu.

### <a name="seasonal-model"></a>Model sezonowy

Dzięki modelowi trendu musimy przeprowadzić wypychanie i uwzględnić efekty sezonowe. Użyjemy miesiąca roku jako fikcyjnej zmiennej w modelu liniowym, aby przechwycić wpływ na miesiąc. Po wprowadzeniu zmiennych współczynnika do modelu nie można obliczyć przechwycenia. Jeśli tego nie zrobisz, formuła jest powyżej określona, a R spowoduje porzucenie jednego z żądanych czynników, ale utrzymanie okresu przechwycenia.

Ze względu na to, że mamy zadowalający model trendu, możemy użyć `update()` funkcji, aby dodać nowe warunki do istniejącego modelu. Wartość-1 w formule aktualizacji opuszcza okres przechwycenia. W tej chwili nadal trwa RStudio:

```r
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

Ta funkcja generuje następujące dane wyjściowe.

```output
##
## Call:
## lm(formula = Milk.Prod ~ Time + I(Month.Count^3) + Month - 1,
##     data = cadairytrain)
##
## Residuals:
##      Min       1Q   Median       3Q      Max
## -0.06879 -0.01693  0.00346  0.01543  0.08726
##
## Coefficients:
##                   Estimate Std. Error t value Pr(>|t|)
## Time              1.57e-09   2.72e-11    57.7   <2e-16 ***
## I(Month.Count^3) -3.74e-08   1.57e-09   -23.8   <2e-16 ***
## MonthApr          6.40e+00   2.63e-02   243.3   <2e-16 ***
## MonthAug          6.38e+00   2.63e-02   242.2   <2e-16 ***
## MonthDec          6.38e+00   2.64e-02   241.9   <2e-16 ***
## MonthFeb          6.31e+00   2.63e-02   240.1   <2e-16 ***
## MonthJan          6.39e+00   2.63e-02   243.1   <2e-16 ***
## MonthJul          6.39e+00   2.63e-02   242.6   <2e-16 ***
## MonthJun          6.38e+00   2.63e-02   242.4   <2e-16 ***
## MonthMar          6.42e+00   2.63e-02   244.2   <2e-16 ***
## MonthMay          6.43e+00   2.63e-02   244.3   <2e-16 ***
## MonthNov          6.34e+00   2.63e-02   240.6   <2e-16 ***
## MonthOct          6.37e+00   2.63e-02   241.8   <2e-16 ***
## MonthSep          6.34e+00   2.63e-02   240.6   <2e-16 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
##
## Residual standard error: 0.0263 on 202 degrees of freedom
## Multiple R-squared:     1,    Adjusted R-squared:     1
## F-statistic: 1.42e+06 on 14 and 202 DF,  p-value: <2e-16
```

Widzimy, że model nie ma już okresu przechwycenia i ma 12 znaczących czynników. Ten wynik ma dokładne znaczenie.

Utwórzmy kolejny wykres szeregów czasowych danych produkcji Kalifornii, aby zobaczyć, jak dobrze działa model sezonowy. Dodaliśmy następujący kod w Machine Learning Studio (klasyczny) [Uruchom skrypt języka R][execute-r-script] w celu utworzenia modelu i utworzenia wykresu.

```r
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

Uruchomienie tego kodu w Machine Learning Studio (klasyczny) powoduje wyświetlenie wykresu w tym miejscu.

![Produkcja mleka Kalifornii z modelem, w tym skutki sezonowe.](./media/r-quickstart/unnamed-chunk-20.png)

Dopasowanie do danych pokazanych w tym przykładzie jest raczej zachęcane. Tendencja i skutek sezonowy (odmiana miesięczna) wyglądają rozsądnie.

Podobnie jak w przypadku innego sprawdzenia modelu, przyjrzyjmy się resztom. Poniższy kod oblicza przewidywane wartości z naszych dwóch modeli, oblicza reszty dla modelu sezonowego, a następnie wykreśla te reszty dla danych szkoleniowych.

```r
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

Wykres resztkowy jest pokazywany w tym miejscu.

![Pozostałości modelu sezonowego dla danych szkoleniowych.](./media/r-quickstart/unnamed-chunk-21.png)

Są one odpowiednie do wyszukania. Nie ma określonej struktury, z tą różnicą, że nie jest to wpływ na 2008-2009, który model nie jest szczególnie używany.

Wykres przedstawiony w tym przykładzie jest przydatny do wykrywania wszelkich wzorców zależnych od czasu w pozostałych. Jawne podejście do przetwarzania i wykreślania reszty, które zostały użyte, powoduje umieszczenie reszt w kolejności w czasie na tym rysunku. Jeśli wykreślono `milk.lm$residuals` , wykres nie był w kolejności w czasie.

Można również użyć `plot.lm()` do tworzenia serii wykresów diagnostycznych.

```r
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Ten kod tworzy serię wykresów diagnostycznych wyświetlanych w poniższych przykładach.

![Pierwszy wykres diagnostyczny dla modelu sezonowego.](./media/r-quickstart/unnamed-chunk-221.png)

![Drugi wykres diagnostyczny dla modelu sezonowego.](./media/r-quickstart/unnamed-chunk-222.png)

![Trzeci wykres diagnostyki dla modelu sezonowego.](./media/r-quickstart/unnamed-chunk-223.png)

![Czwarty wykres diagnostyczny dla modelu sezonowego.](./media/r-quickstart/unnamed-chunk-224.png)

W tych wykresach znajdują się kilka bardzo znaczących punktów, ale nic nie może mieć dużego znaczenia. Dodatkowo możemy zobaczyć, że na zwykłych wykresach Q-Q jest bliski rozkładu, co jest ważnym założeniami dla modeli liniowych.

### <a name="forecasting-and-model-evaluation"></a>Prognozowanie i Ocena modelu

Aby wykonać nasz przykład, należy wykonać tylko jedną czynność. Musimy obliczyć prognozy i zmierzyć błąd w odniesieniu do rzeczywistych danych. Nasza prognoza będzie wynosić 12 miesięcy od 2013. Możemy obliczyć miarę błędu dla tej prognozy na rzeczywiste dane, które nie są częścią naszego zestawu danych szkoleniowych. Ponadto możemy porównać wydajność z 18 lat danych szkoleniowych w ciągu 12 miesięcy od danych testowych.

Wiele metryk służy do mierzenia wydajności modeli szeregów czasowych. W naszym przypadku użyjemy błędu średnika głównego (RMS). Następująca funkcja oblicza błąd usługi RMS między dwiema seriami.

```r
RMS.error <- function(series1, series2, is.log = TRUE, min.length = 2){
  ## Function to compute the RMS error or difference between two
  ## series or vectors

  messages <- c("ERROR: Input arguments to function RMS.error of wrong type encountered",
                "ERROR: Input vector to function RMS.error is too short",
                "ERROR: Input vectors to function RMS.error must be of same length",
                "WARNING: Funtion rms.error has received invald input time series.")

  ## Check the arguments
  if(!is.numeric(series1) | !is.numeric(series2) | !is.logical(is.log) | !is.numeric(min.length)) {
    warning(messages[1])
    return(NA)}

  if(length(series1) < min.length) {
    warning(messages[2])
    return(NA)}

  if((length(series1) != length(series2))) {
       warning(messages[3])
    return(NA)}

  ## If is.log is TRUE exponentiate the values, else just copy
  if(is.log) {
    tryCatch( {
      temp1 <- exp(series1)
      temp2 <- exp(series2) },
      error = function(e){warning(messages[4]); NA}
    )
  } else {
    temp1 <- series1
    temp2 <- series2
  }

 ## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute the RMS error in a dataframe
  tryCatch( {
    sqrt(sum((temp1 - temp2)^2) / length(temp1))},
    error = function(e){warning(messages[4]); NA})
}
```

Podobnie jak w przypadku `log.transform()` funkcji omówionej w sekcji "przekształcenia wartości" w tej funkcji występuje wiele błędów sprawdzania i kodu odzyskiwania wyjątków. Zastosowane zasady są takie same. Prace odbywają się w dwóch miejscach opakowanych w `tryCatch()` . Najpierw seria czasowa to exponentiated, ponieważ pracujemy z dziennikami wartości. Po drugie jest obliczany rzeczywisty błąd usługi RMS.

Za pomocą funkcji służącej do mierzenia błędu usługi RMS można utworzyć i wyprowadzić ramkę danych zawierającą błędy usługi RMS. Będziemy zawierać warunki dla samego modelu trendu i cały model z sezonowymi czynnikami. Poniższy kod wykonuje zadanie przy użyciu dwóch modeli liniowych, które zostały zbudowane.

```r
## Compute the RMS error in a dataframe
## Include the row names in the first column so they will
## appear in the output of the Execute R Script
RMS.df  <-  data.frame(
rowNames = c("Trend Model", "Seasonal Model"),
  Traing = c(
  RMS.error(predict1[1:216], cadairydata$Milk.Prod[1:216]),
  RMS.error(predict2[1:216], cadairydata$Milk.Prod[1:216])),
  Forecast = c(
    RMS.error(predict1[217:228], cadairydata$Milk.Prod[217:228]),
    RMS.error(predict2[217:228], cadairydata$Milk.Prod[217:228]))
)
RMS.df

## The following line should be executed only when running in
## Azure Machine Learning Studio (classic)
maml.mapOutputPort('RMS.df')
```

Uruchomienie tego kodu spowoduje utworzenie danych wyjściowych przedstawionych w tym miejscu na porcie wyjściowym zestawu danych wynikowych.

![Zrzut ekranu pokazujący Porównanie błędów usługi RMS dla modeli.](./media/r-quickstart/fig26.png)

Z tych wyników widzimy, że dodanie wskaźników sezonowych do modelu zmniejsza istotny błąd usługi RMS. Nie za Surprisingly, błąd usługi RMS dla danych szkoleniowych jest mniejszy niż w przypadku prognozowania.

## <a name="guide-to-rstudio-documentation"></a><a id="appendixa"></a>Przewodnik dotyczący dokumentacji RStudio

RStudio jest dobrze udokumentowane. Oto kilka linków do najważniejszych sekcji dokumentacji RStudio, aby rozpocząć pracę.

* **Tworzenie projektów**: można organizować kod R i zarządzać nim w projektach za pomocą RStudio. Aby uzyskać więcej informacji, zobacz [using projects](https://support.rstudio.com/hc/articles/200526207-Using-Projects). Postępuj zgodnie z tymi instrukcjami i Utwórz projekt dla przykładów kodu języka R w tym artykule.
* **Edytuj i wykonaj kod r**: RStudio zapewnia zintegrowane środowisko do edycji i wykonywania kodu języka r. Aby uzyskać więcej informacji, zobacz [Edytowanie i wykonywanie kodu](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code).
* **Debugowanie**: RStudio obejmuje zaawansowane funkcje debugowania. Aby uzyskać więcej informacji o tych funkcjach, zobacz [debugowanie za pomocą RStudio](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio). Aby uzyskać informacje o funkcjach rozwiązywania problemów z punktem przerwania, zobacz temat [Rozwiązywanie problemów](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting)

## <a name="further-reading"></a><a id="appendixb"></a>Dalsze odczytywanie

Ten samouczek programowania w języku R obejmuje podstawowe informacje o tym, co jest potrzebne do korzystania z języka R z Machine Learning Studio (klasyczny). Jeśli nie znasz języka R, dostępne są dwa wprowadzenie w witrynie CRAN:

* Emmanuel Paradis jest dobrym [miejscem do rozpoczęcia](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf) pracy.
* [Wprowadzenie do języka R](https://cran.r-project.org/doc/manuals/R-intro.html) przez w. N. Venables et al. przechodzi do większej głębokości.

Istnieje wiele książek w języku R, które mogą pomóc Ci rozpocząć pracę:

* **Grafika programowania w języku r: Przewodnik po projekcie oprogramowania statystycznego** przez Normanowi Matloff jest doskonałym wprowadzeniem do programowania w języku R.
* Język **r Cookbook** przez Paul Teetor stanowi podejście do rozwiązania problemu z użyciem języka r.
* Język **R w działaniu** przez Robert Kabacoff jest kolejną przydatną książką wprowadzającą. [Szybka witryna sieci Web](https://www.statmethods.net/) w usłudze R to przydatne zasoby.
* Nagrania **r Inferno** wg Patryka to książka Surprisingly witryna zawiera humorystyczną, która zajmuje się szeregiem Lew i trudnymi tematami, które można napotkać podczas programowania w języku R. Książka jest dostępna bezpłatnie w [Inferno R](https://www.burns-stat.com/documents/books/the-r-inferno/).
* **Zaawansowane r** przez Hadley Wickham zawiera głębokie szczegółowe w zaawansowanych tematach w języku r. Wersja online tej książki jest dostępna bezpłatnie w [zaawansowanym języku R](http://adv-r.had.co.nz/).
* Wprowadzenie **szeregów czasowych przy użyciu języka r** przez Paul Cowpertwait i Andrew Metcalfe zapewnia wstęp do analizy szeregów czasowych przy użyciu języka r. Wiele bardziej teoretycznych tekstów zawiera przykłady języka R.

Oto kilka doskonałych zasobów internetowych:

* [Widok zadań Cran: analiza szeregów czasowych](https://cran.r-project.org/web/views/TimeSeries.html) zawiera katalog pakietów szeregów czasowych języka R. Aby uzyskać informacje o określonych pakietach obiektów szeregów czasowych, zapoznaj się z dokumentacją tego pakietu.
* [Wprowadzenie do języka r](https://www.datacamp.com/courses/introduction-to-r) to bezpłatny interaktywny kurs z firmy datacamp, który uczy o języku R w przeglądarce dzięki wykorzystaniu lekcji i ćwiczeń. Istnieją Interaktywne samouczki dotyczące najnowszych technik i pakietów języka R.
* [Poznaj programowanie w języku R, a ostateczny Przewodnik](https://www.datamentor.io/r-programming/) z datadoradcy.
* [Kod języka r](https://r-coder.com/) zawiera szczegółowe samouczki języka r oraz bezpłatny kurs r dla początkujących.
* [Samouczek języka R](https://www.cyclismo.org/tutorial/R/) , Kelly czerń z Clarkson University to szybki samouczek.
* [Najważniejsze zasoby języka R w celu poprawy umiejętności](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html) związanych z danymi zawierają więcej niż 60 zasobów r.

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
