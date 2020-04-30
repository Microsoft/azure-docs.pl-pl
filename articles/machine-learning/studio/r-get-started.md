---
title: Korzystanie z języka R z ML Studio (klasyczny) — Azure
description: Skorzystaj z tego samouczka programowania w języku R, aby rozpocząć pracę z Azure Machine Learning Studio (klasyczną) w R, aby utworzyć rozwiązanie do prognozowania.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/01/2019
ms.openlocfilehash: 1dcda3efe3872100100d6e85b68a36359b7eab84
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82209506"
---
# <a name="get-started-with-azure-machine-learning-studio-classic-in-r"></a>Wprowadzenie do Azure Machine Learning Studio (klasyczne) w języku R

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

<!-- Stephen F Elston, Ph.D. -->
Ten samouczek ułatwia rozpoczęcie rozszerzania Azure Machine Learning Studio (klasycznego) przy użyciu języka programowania R. Postępuj zgodnie z tym samouczkiem programowania w języku R, aby utworzyć, przetestować i wykonać kod R w programie Studio (klasyczny). Podczas pracy z samouczkiem utworzysz kompletne rozwiązanie do prognozowania przy użyciu języka R w programie Studio (klasycznego).  

Azure Machine Learning Studio (klasyczny) zawiera wiele zaawansowanych modułów uczenia maszynowego i manipulowania danymi. Zaawansowany język R został opisany jako lingua franca analizy. Happily, analiza i manipulowanie danymi w programie Studio (klasyczne) można rozszerzyć przy użyciu języka R. Ta kombinacja zapewnia skalowalność i łatwość wdrażania programu Studio (klasycznego) z elastyczną i głęboką analizą języka R.

### <a name="forecasting-and-the-dataset"></a>Prognozowanie i zestaw danych

Prognozowanie jest szeroko zaangażowaną i całkiem użyteczną metodą analityczną. Typowym zastosowaniem jest zakres od przewidywania sprzedaży elementów sezonowych, co pozwala ustalić optymalne poziomy spisu do przewidywania zmiennych makroekonomicznych. Prognozowanie jest zwykle wykonywane z użyciem modeli szeregów czasowych.

Dane szeregów czasowych to dane, w których wartości mają indeks czasu. Indeks czasu może być regularny, np. co miesiąc lub co minutę, lub nieregularnym. Model szeregów czasowych jest oparty na danych szeregów czasowych. Język programowania R zawiera elastyczną platformę i rozbudowaną analizę dla danych szeregów czasowych.

W tym przewodniku będziemy pracować z danymi o produkcji i cenach mlecznych. Te dane obejmują miesięczne informacje o produkcji kilku produktów mleczarskich i cenie tłuszczu mlekowego, giełdzie testów porównawczych.

Dane używane w tym artykule wraz ze skryptami języka R można pobrać z [MachineLearningSamples-notesów/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples). Dane w pliku `cadairydata.csv` zostały pierwotnie przeszkolene z informacji dostępnych na Uniwersytecie Wisconsin o [https://dairymarkets.com](https://dairymarkets.com).

### <a name="organization"></a>Organizacja

Będziemy postępować zgodnie z kilkoma krokami, aby dowiedzieć się, jak tworzyć, testować i wykonywać kod R analizy i manipulowania danymi w środowisku Azure Machine Learning Studio (klasycznym).  

* Najpierw będziemy poznać podstawy korzystania z języka R w środowisku Azure Machine Learning Studio (klasycznym).
* Następnie będziemy postępować zgodnie z omówieniem różnych aspektów we/wy dla danych, kodu R i grafiki w środowisku Azure Machine Learning Studio (klasycznym).
* Następnie utworzymy pierwszą część naszego rozwiązania do prognozowania, tworząc kod służący do czyszczenia i przekształcania danych.
* Po przygotowaniu danych wykonamy analizę korelacji między kilkoma zmiennymi w naszym zestawie danych.
* Na koniec utworzymy model prognozowania szeregów czasowych dla produkcji mleka.

## <a name="interact-with-r-language-in-machine-learning-studio-classic"></a><a id="mlstudio"></a>Korzystanie z języka R w Machine Learning Studio (klasyczny)

W tej części przedstawiono podstawowe informacje dotyczące współpracy z językiem programowania R w środowisku Machine Learning Studio (klasycznego). Język R zapewnia zaawansowane narzędzie do tworzenia niestandardowych modułów analizy i manipulowania danymi w środowisku Azure Machine Learning Studio (klasycznym).

Będę używać RStudio, aby opracowywać, testować i debugować kod R na małą skalę. Ten kod jest następnie wycięty i wklejony do modułu [wykonywania skryptu języka R][execute-r-script] gotowego do uruchomienia w Azure Machine Learning Studio (klasyczny).  

### <a name="the-execute-r-script-module"></a>Moduł wykonywania skryptu języka R

W Machine Learning Studio (klasyczny) skrypty języka R są uruchamiane w module [wykonywania skryptu języka r][execute-r-script] . Przykład modułu [skryptu języka R][execute-r-script] w Machine Learning Studio (klasyczny) przedstawiono na rysunku 1.

 ![Język programowania r: moduł wykonywania skryptu języka R wybrany w Machine Learning Studio (klasyczny)](./media/r-quickstart/fig1.png)

*Rysunek 1. Środowisko Machine Learning Studio (klasyczne) pokazujące wybrany moduł skryptu języka R.*

Na rysunku 1 Przyjrzyjmy się jednemu z najważniejszych części środowiska Machine Learning Studio (klasycznego) do pracy z modułem [wykonywania skryptów języka R][execute-r-script] .

* Moduły w tym eksperymentie są wyświetlane w środkowym okienku.
* Górna część okienka po prawej stronie zawiera okno umożliwiające wyświetlanie i Edytowanie skryptów języka R.  
* W dolnej części okienka po prawej stronie są wyświetlane pewne właściwości [skryptu Execute języka R][execute-r-script]. Możesz wyświetlić dziennik błędów i danych wyjściowych, wybierając odpowiednie punkty w tym okienku.

Będziemy oczywiście przedyskutować szczegółowy [Opis wykonania skryptu języka R][execute-r-script] w dalszej części tego artykułu.

Podczas pracy z złożonymi funkcjami języka R zaleca się edytowanie, testowanie i debugowanie w RStudio. Podobnie jak w przypadku tworzenia oprogramowania, Zwiększ swój kod przyrostowo i przetestuj go w małych prostych przypadkach testowych. Następnie należy wyciąć i wkleić funkcje do okna skryptu języka R w module [wykonywania skryptu języka r][execute-r-script] . Takie podejście umożliwia korzystanie z RStudio zintegrowanego środowiska programistycznego (IDE) i możliwości Azure Machine Learning Studio (klasyczne).  

#### <a name="execute-r-code"></a>Wykonaj kod R

Każdy kod języka R w module [wykonywania skryptu języka r][execute-r-script] zostanie wykonany po uruchomieniu eksperymentu, wybierając przycisk **Run (Uruchom** ). Po zakończeniu wykonywania znacznik wyboru zostanie wyświetlony na ikonie [Wykonaj skrypt języka R][execute-r-script] .

#### <a name="defensive-r-coding-for-azure-machine-learning"></a>Obronne kodowanie języka R dla Azure Machine Learning

Jeśli opracowujesz kod w języku R dla programu, Załóżmy, że usługa sieci Web przy użyciu Azure Machine Learning Studio (klasyczny), należy ostatecznie zaplanować, w jaki sposób kod będzie zajmował się nieoczekiwanym danymi wejściowymi i wyjątkami. Aby zachować przejrzystość, nie zostały one jeszcze rozmieszczone w sposób umożliwiający sprawdzanie lub obsługę wyjątków w większości przykładów kodu. Jednak w miarę jak będziemy udostępniać kilka przykładów funkcji przy użyciu funkcji obsługi wyjątków języka R.  

Jeśli potrzebujesz bardziej kompletnej obróbki obsługi wyjątków języka R, zalecamy przeczytanie odpowiednich sekcji książki według Wickham wymienionych poniżej w [celu dalszej operacji odczytu](#appendixb).

#### <a name="debug-and-test-r-in-machine-learning-studio-classic"></a>Debugowanie i testowanie języka R w Machine Learning Studio (klasyczny)

Aby wykonać ponowną iterację, zalecamy testowanie i debugowanie kodu języka R w niewielkiej skali w RStudio. Istnieją jednak przypadki, w których trzeba będzie śledzić problemy związane z kodem R w samym [skrypcie Execute języka r][execute-r-script] . Ponadto dobrym sposobem jest sprawdzenie wyników w Machine Learning Studio (klasyczny).

Dane wyjściowe z wykonywania kodu języka R i na platformie Azure Machine Learning Studio (klasycznej) są dostępne głównie w danych wyjściowych. log. Niektóre dodatkowe informacje będą widoczne w dzienniku Error. log.  

Jeśli wystąpi błąd w Machine Learning Studio (klasyczny) podczas uruchamiania kodu języka R, należy najpierw sprawdzić, czy wystąpił błąd. log. Ten plik może zawierać przydatne komunikaty o błędach ułatwiające zrozumienie i poprawienie błędu. Aby wyświetlić błąd. log, wybierz pozycję **Wyświetl dziennik błędów** w **okienku właściwości** dla [skryptu Execute R][execute-r-script] zawierającego błąd.

Na przykład uruchomiono następujący kod R z niezdefiniowaną zmienną y w module [Execute skryptu języka r][execute-r-script] :

```R
x <- 1.0
z <- x + y
```

Wykonanie tego kodu nie powiodło się, co spowoduje wystąpienie błędu. Wybranie opcji **Wyświetl dziennik błędów** w **okienku właściwości** powoduje wyświetlenie ekranu wyświetlanego na rysunku 2.

  ![Wyskakujące okienko komunikatu o błędzie](./media/r-quickstart/fig2.png)

*Rysunek 2. Wyskakujące okienko komunikatu o błędzie.*

Wygląda na to, że musimy przeszukać plik Output. log, aby wyświetlić komunikat o błędzie języka R. Wybierz [skrypt Execute R][execute-r-script] , a następnie wybierz pozycję **Wyświetl dane wyjściowe. log** w **okienku właściwości** po prawej stronie. Zostanie otwarte nowe okno przeglądarki i zostanie wyświetlone następujące polecenie.

    [Critical]     Error: Error 0063: The following error occurred during evaluation of R script:
    ---------- Start of error message from R ----------
    object 'y' not found


    object 'y' not found
    ----------- End of error message from R -----------

Ten komunikat o błędzie nie zawiera żadnych niedożądanych i jasno identyfikuje problem.

Aby sprawdzić wartość dowolnego obiektu w języku R, można wydrukować te wartości w pliku Output. log. Reguły służące do badania wartości obiektów są zasadniczo takie same jak w interaktywnej sesji języka R. Na przykład, jeśli wpiszesz nazwę zmiennej w wierszu, wartość obiektu zostanie wydrukowany do pliku wyjściowego. log.  

#### <a name="packages-in-machine-learning-studio-classic"></a>Pakiety w Machine Learning Studio (klasyczne)

Program Studio jest dostarczany z ponad 350 wstępnie zainstalowanymi pakietami języka R. Aby pobrać listę wstępnie zainstalowanych pakietów, można użyć następującego kodu w module [wykonywania skryptu języka R][execute-r-script] .

```R
data.set <- data.frame(installed.packages())
maml.mapOutputPort("data.set")
```

Jeśli w tym momencie nie znasz ostatniego wiersza tego kodu, zapoznaj się z artykułem. W pozostałej części tego artykułu będziemy rozległie omówić korzystanie z języka R w środowisku Studio (klasycznego).

### <a name="introduction-to-rstudio"></a>Wprowadzenie do RStudio

RStudio to powszechnie używane środowisko IDE dla języka R. Będę używać RStudio do edycji, testowania i debugowania niektórych kodów języka R użytych w tym przewodniku. Gdy kod języka R zostanie przetestowany i gotowy, można po prostu wyciąć i wkleić z edytora RStudio do modułu [skryptu języka r][execute-r-script] Machine Learning Studio (klasycznego).  

Jeśli nie masz zainstalowanego języka R programowania na komputerze stacjonarnym, zalecamy wykonanie tej czynności teraz. Bezpłatne pobieranie języka R typu open source jest dostępne w kompleksowej sieci R archiwum CRAN [https://www.r-project.org/](https://www.r-project.org/). Dostępne są pliki do pobrania dla systemów Windows, Mac OS i Linux/UNIX. Wybierz odbicie w pobliżu i postępuj zgodnie z instrukcjami pobierania. Ponadto CRAN zawiera wiele przydatnych pakietów analizy i manipulowania danymi.

Jeśli dopiero zaczynasz RStudio, należy pobrać i zainstalować wersję Desktop. Pliki do pobrania RStudio dla systemu Windows, Mac OS i Linux/UNIX można znaleźć pod http://www.rstudio.com/products/RStudio/adresem. Postępuj zgodnie z instrukcjami podanymi w celu zainstalowania RStudio na komputerze stacjonarnym.  

Samouczek wprowadzenie do RStudio jest dostępny przy [użyciu środowiska IDE RStudio](https://support.rstudio.com/hc/sections/200107586-Using-RStudio).

Dostarczamy kilka dodatkowych informacji na temat używania RStudio w [przewodniku do RStudio dokumentacji](#appendixa) poniżej.  

## <a name="get-data-in-and-out-of-the-execute-r-script-module"></a><a id="scriptmodule"></a>Pobieranie danych z modułu skryptu języka R i z niego

W tej sekcji omówiono sposób pobierania danych do modułu [skryptu wykonywania języka R][execute-r-script] i z niego. Sprawdzimy, jak obsługiwać różne typy danych odczytywane do i z modułu [wykonywania skryptu języka R][execute-r-script] .

Pełny kod dla tej sekcji znajduje się w [MachineLearningSamples — notesy/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="load-and-check-data-in-machine-learning-studio-classic"></a>Załaduj i sprawdź dane w Machine Learning Studio (klasyczne)

#### <a name="load-the-dataset"></a><a id="loading"></a>Załaduj zestaw danych

Zaczniemy od ładowania pliku **csdairydata. csv** do Azure Machine Learning Studio (klasyczny).

1. Uruchom środowisko Azure Machine Learning Studio (klasyczne).
1. Wybierz pozycję **+ Nowy** w lewym dolnym rogu ekranu i wybierz pozycję **zestaw danych**.
1. Wybierz **z pliku lokalnego**, a następnie **Przeglądaj** , aby wybrać plik.
1. Upewnij się, że wybrano **ogólny plik CSV z nagłówkiem (CSV)** jako typ zestawu danych.
1. Zaznacz znacznik wyboru.
1. Po przekazaniu zestawu danych powinien zostać wyświetlony nowy zestaw danych, wybierając kartę **zestawy** danych.  

#### <a name="create-an-experiment"></a>Tworzenie eksperymentu

Teraz, gdy mamy pewne dane w Machine Learning Studio (klasyczne), musimy utworzyć eksperyment, aby przeprowadzić analizę.  

1. Wybierz pozycję **+ Nowy** w lewym dolnym rogu, a następnie wybierz pozycję **eksperymenty**, a następnie przycisk **pusty eksperyment**.
1. Możesz nazwać eksperyment poprzez wybranie i zmodyfikowanie **eksperymentu utworzonego w...** title w górnej części strony. Na przykład zmiana na **przeanalizowanie mleczarni urzędu certyfikacji**.
1. Po lewej stronie eksperymentu rozwiń pozycję **zapisane zestawy danych**, a następnie **Moje zestawy danych**. Powinien zostać wyświetlony wcześniej przekazany plik **cadairydata. csv** .
1. Przeciągnij i upuść **zestaw danych csdairydata. csv** na eksperyment.
1. W polu **Wyszukaj elementy eksperymentu** w górnej części okienka po lewej stronie wpisz polecenie [Execute R Script][execute-r-script]. Moduł zostanie wyświetlony na liście wyszukiwania.
1. Przeciągnij i upuść moduł [Wykonaj skrypt R][execute-r-script] na palecie.  
1. Połącz dane wyjściowe **zestawu danych csdairydata. csv** z lewej strony wejściowej (**pozycję DataSet1**) [skryptu Execute języka R][execute-r-script].
1. **Nie zapomnij wybrać opcji "Save" (Zapisz)!**  

W tym momencie eksperyment powinien wyglądać jak rysunek 3.

![Eksperyment analizy mleczarni urzędu certyfikacji z zestawem danych i modułem wykonywania skryptu języka R](./media/r-quickstart/fig3.png)

*Rysunek 3. Eksperyment analizy mleczarni urzędu certyfikacji z zestawem danych i modułem wykonywania skryptu języka R.*

#### <a name="check-on-the-data"></a>Sprawdź dane

Spójrzmy na dane, które zostały załadowane do naszego eksperymentu. W eksperymentie wybierz dane wyjściowe **zestawu danych cadairydata. csv** i wybierz opcję **Wizualizuj**. Powinien pojawić się rysunek 4.  

![Podsumowanie zestawu danych cadairydata. csv](./media/r-quickstart/fig4.png)

*Rysunek 4. Podsumowanie zestawu danych cadairydata. csv.*

W tym widoku widzimy wiele przydatnych informacji. Zobaczymy pierwsze kilka wierszy tego zestawu danych. W przypadku wybrania kolumny w sekcji statystyki zostanie wyświetlona więcej informacji o kolumnie. Na przykład wiersz typu funkcja pokazuje, jakie typy danych Azure Machine Learning Studio (klasyczne) są przypisane do kolumny. Zapoznaj się z dobrym Sanity przed rozpoczęciem wykonywania jakichkolwiek poważnych zadań.

### <a name="first-r-script"></a>Pierwszy skrypt języka R

Utwórzmy prosty pierwszy skrypt języka R, aby eksperymentować w Azure Machine Learning Studio (klasyczny). Utworzono i przetestowano następujący skrypt w RStudio.  

```R
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

Teraz chcę przenieść ten skrypt do Azure Machine Learning Studio (klasyczny). Mogę po prostu wyciąć i wkleić. Jednak w tym przypadku wyślę mój skrypt języka R za pośrednictwem pliku zip.

### <a name="data-input-to-the-execute-r-script-module"></a>Dane wejściowe do modułu wykonywania skryptu języka R

Przyjrzyjmy się danych wejściowych do modułu [wykonywania skryptu języka R][execute-r-script] . W tym przykładzie odczytamy dane mleczarskie Kalifornii do modułu [wykonywania skryptu języka R][execute-r-script] .  

Istnieją trzy możliwe dane wejściowe modułu [skryptu Execute języka R][execute-r-script] . W zależności od aplikacji możesz użyć jednego lub wszystkich tych danych wejściowych. Jest również doskonale uzasadnione używanie skryptu języka R, który nie ma żadnych danych wejściowych.  

Przyjrzyjmy się każdemu z tych danych wejściowych, przechodząc od lewej do prawej. Nazwy poszczególnych danych wejściowych można zobaczyć, umieszczając kursor na wejściu i odczytując etykietkę narzędzia.  

#### <a name="script-bundle"></a>Pakiet skryptu

Dane wejściowe pakietu skryptu umożliwiają przekazanie zawartości pliku zip do modułu [wykonywania skryptu języka R][execute-r-script] . Możesz użyć jednego z poniższych poleceń, aby odczytać zawartość pliku zip do kodu języka R.

```R
source("src/yourfile.R") # Reads a zipped R script
load("src/yourData.rdata") # Reads a zipped R data file
```

> [!NOTE]
> Azure Machine Learning Studio (klasyczny) traktuje pliki w pliku zip jako jeśli znajdują się w katalogu src/, dlatego należy prefiksować nazwy plików przy użyciu tej nazwy katalogu. Na przykład, `yourfile.R` Jeśli plik zip zawiera pliki i `yourData.rdata` w katalogu głównym zip, należy rozwiązać te działania jako `src/yourfile.R` i `src/yourData.rdata` w przypadku korzystania `source` z i. `load`

Omawiamy już sposób ładowania zestawów [danych w załadowaniu elementu DataSet](#loading). Po utworzeniu i przetestowaniu skryptu języka R wyświetlanego w poprzedniej sekcji wykonaj następujące czynności:

1. Zapisz skrypt języka R w. Plik języka R. Nazywam mi plik skryptu "simpleplot. R ". Oto zawartość.

   ```R
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

1. Utwórz plik zip i skopiuj skrypt do tego pliku zip. W systemie Windows można kliknąć prawym przyciskiem myszy plik i wybrać polecenie **Wyślij do**, a następnie **folder skompresowany**. Spowoduje to utworzenie nowego pliku zip zawierającego "simpleplot. Plik "R".

1. Dodaj plik do **zestawów danych** w Azure Machine Learning Studio (klasyczny), określając typ jako **zip**. Powinien być teraz widoczny plik zip w zestawach danych.

1. Przeciągnij i upuść plik zip z **zestawów danych** na **kanwę ml Studio (klasyczną)**.

1. Połącz dane wyjściowe ikony **zip danych** z **pakietem skryptu** dane wejściowe modułu [wykonywania skryptu języka R][execute-r-script] .

1. Wpisz `source()` funkcję z nazwą pliku zip w oknie kod dla modułu [wykonywania skryptu języka R][execute-r-script] . We wszystkich wpisanych `source("src/simpleplot.R")`przypadkach.  

1. Upewnij się, że wybrano pozycję **Zapisz**.

Po zakończeniu tych kroków moduł [wykonywania skryptu języka][execute-r-script] r wykona skrypt języka r w pliku zip, gdy eksperyment zostanie uruchomiony. W tym momencie eksperyment powinien wyglądać podobnie do rysunku 5.

![Eksperymentowanie przy użyciu spakowanego skryptu języka R](./media/r-quickstart/fig6.png)

*Rysunek 5. Eksperymentowanie przy użyciu spakowanego skryptu języka R.*

#### <a name="dataset1"></a>Pozycję DataSet1

Można przekazać prostokątną tabelę danych do kodu języka R przy użyciu danych wejściowych pozycję DataSet1. W naszym prostym skrypcie `maml.mapInputPort(1)` funkcja odczytuje dane z portu 1. Te dane są następnie przypisywane do nazwy zmiennej Dataframe w kodzie. W naszym prostym skrypcie pierwszy wiersz kodu wykonuje przypisanie.

```R
cadairydata <- maml.mapInputPort(1)
```

Wykonaj eksperyment, wybierając przycisk **Run (Uruchom** ). Po zakończeniu wykonywania wybierz moduł [wykonywania skryptu języka R][execute-r-script] , a następnie wybierz pozycję **Wyświetl dziennik wyjściowy** w okienku właściwości. W przeglądarce powinna zostać wyświetlona nowa strona wyświetlająca zawartość pliku wyjściowego. log. Po przewinięciu w dół powinien zostać wyświetlony komunikat podobny do poniższego.

    [ModuleOutput] InputDataStructure
    [ModuleOutput]
    [ModuleOutput] {
    [ModuleOutput]  "InputName":Dataset1
    [ModuleOutput]  "Rows":228
    [ModuleOutput]  "Cols":9
    [ModuleOutput]  "ColumnTypes":System.Int32,3,System.Double,5,System.String,1
    [ModuleOutput] }

Poniżej przedstawiono bardziej szczegółowe informacje na temat kolumn, które będą wyglądały jak poniżej.

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

Te wyniki są w większości zgodnie z oczekiwaniami, z 228 obserwacji i 9 kolumnami w ramce Dataframe. Możemy zobaczyć nazwy kolumn, typ danych R i próbkę każdej kolumny.

> [!NOTE]
> Te same wydruki są wygodnie dostępne w danych wyjściowych urządzenia R modułu [skryptu Execute r][execute-r-script] . Będziemy omawiać dane wyjściowe modułu [Uruchom skrypt języka R][execute-r-script] w następnej sekcji.  

#### <a name="dataset2"></a>Dataset2

Zachowanie danych wejściowych Dataset2 jest identyczne z pozycję DataSet1. Za pomocą tego danych wejściowych można przekazać drugą prostokątną tabelę danych do kodu języka R. Funkcja `maml.mapInputPort(2)`z argumentem 2 służy do przekazywania tych danych.  

### <a name="execute-r-script-outputs"></a>Wykonaj dane wyjściowe skryptu języka R

#### <a name="output-a-dataframe"></a>Wyprowadzanie danych wyjściowych

Można wyprowadzić zawartość ramki danych języka R jako prostokątną tabelę za pośrednictwem portu pozycję DataSet1 wyniku przy użyciu `maml.mapOutputPort()` funkcji. W naszym prostym skrypcie języka R jest to wykonywane w następującym wierszu.

```
maml.mapOutputPort('cadairydata')
```

Po uruchomieniu eksperymentu wybierz port wyjściowy pozycję DataSet1 wynik, a następnie wybierz polecenie **wizualizator**. Powinna zostać wyświetlona ilustracja 6.

![Wizualizacja danych wyjściowych z danymi mleczarskimi Kalifornii](./media/r-quickstart/fig7.png)

*Rysunek 6. Wizualizacja danych wyjściowych z danymi mleczarskimi Kalifornii.*

Dane wyjściowe wyglądają identycznie z danymi wejściowymi, dokładnie tak jak w przypadku oczekiwań.  

### <a name="r-device-output"></a>Dane wyjściowe urządzenia R

Dane wyjściowe urządzenia modułu [wykonywania skryptu języka R][execute-r-script] zawierają komunikaty i dane wyjściowe grafiki. Do portu wyjściowego urządzenia R są wysyłane zarówno standardowe, jak i standardowe komunikaty o błędach z języka R.  

Aby wyświetlić dane wyjściowe urządzenia R, wybierz port, a następnie w **wizualizacji**. Widzimy standardowe wyjście i standardowy błąd ze skryptu języka R na rysunku 7.

![Standardowe wyjście i standardowy błąd z portu urządzenia R](./media/r-quickstart/fig8.png)

*Rysunek 7. Standardowe wyjście i standardowy błąd z portu urządzenia R.*

Przewiń w dół zobaczysz dane wyjściowe grafiki z naszego skryptu języka R na rysunku 8.  

![Dane wyjściowe grafiki z portu urządzenia R](./media/r-quickstart/fig9.png)

*Rysunek 8. Dane wyjściowe grafiki z portu urządzenia R.*  

## <a name="data-filtering-and-transformation"></a><a id="filtering"></a>Filtrowanie i Przekształcanie danych

W tej sekcji wykonamy pewne podstawowe operacje filtrowania i przekształcania danych na danych mlecznych Kalifornii. Na koniec tej sekcji będziemy mieć dane w formacie odpowiednim do tworzenia modelu analitycznego.  

Dokładniej, w tej sekcji wykonamy kilka typowych zadań związanych z czyszczeniem i przekształcaniem danych: przekształcanie typów, filtrowanie danych ramek, dodawanie nowych kolumn obliczanych i przekształceń wartości. To tło powinno pomóc w zapoznaniu się z wieloma odmianami napotkanymi w rzeczywistych problemach.

Pełny kod R dla tej sekcji jest dostępny w [MachineLearningSamples — notesy/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="type-transformations"></a>Przekształcenia typów

Teraz, gdy możemy odczytywać dane mleczarskie Kalifornii do kodu R w module [wykonywania skryptu języka r][execute-r-script] , musimy upewnić się, że dane w kolumnach mają żądany typ i format.  

R jest językiem z typem dynamicznym, co oznacza, że typy danych są przekształcane z jednego do drugiego zgodnie z potrzebami. Typy danych niepodzielnych w języku R zawierają wartości liczbowe, logiczne i znak. Typ czynnika służy do kompaktowania przechowywania danych kategorii. Więcej informacji na temat typów danych w odwołaniach można znaleźć w [dalszej](#appendixb) części poniżej.

Gdy dane tabelaryczne są odczytywane w języku R z zewnętrznego źródła, zawsze dobrym pomysłem jest sprawdzenie typów wyników w kolumnach. Może być potrzebna kolumna typu Character, ale w wielu przypadkach będzie ona wyświetlana jako współczynnik lub na odwrót. W innych przypadkach kolumna, której zdaniem powinna być wartością liczbową, jest reprezentowana przez dane znakowe, np. ' 1,23 ' zamiast 1,23 jako liczba zmiennoprzecinkowa.  

Na szczęście można łatwo skonwertować jeden typ na inny, tak długo jak mapowanie jest możliwe. Na przykład nie można skonwertować "Nevada" na wartość liczbową, ale można ją przekonwertować na współczynnik (zmienną kategorii). Innym przykładem może być konwertowanie wartości liczbowej 1 na znak "1" lub "współczynnik".  

Składnia dla którejkolwiek z tych konwersji jest prosta: `as.datatype()`. Te funkcje konwersji typów obejmują następujące elementy:

* `as.numeric()`
* `as.character()`
* `as.logical()`
* `as.factor()`

Spojrzenie na typy danych kolumn, które wprowadzamy w poprzedniej sekcji: wszystkie kolumny są typu liczbowego, z wyjątkiem kolumny oznaczonej jako "Month", która jest typu Character. Konwertujmy ten element na współczynnik i przetestuj wyniki.  

Usunięto wiersz, który utworzył macierz scatterplot i dodał wiersz konwertujące kolumnę "Month" na współczynnik. W trakcie eksperymentu po prostu wytniemy i wkleisz kod R w oknie kodu modułu [wykonywania skryptu języka r][execute-r-script] . Możesz również zaktualizować plik zip i przekazać go do Azure Machine Learning Studio (klasyczny), ale zajmie to kilka kroków.  

```R
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

Wykonajmy ten kod i przejrzyj dziennik wyjściowy dla skryptu języka R. Odpowiednie dane z dziennika przedstawiono na rysunku 9.

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

*Rysunek 9. Podsumowanie ramki Dataframe z zmienną Factor.*

Typ miesiąca powinien teraz powiedzieć "Factor z **/14 poziomów**". Jest to problem, ponieważ w roku występuje tylko 12 miesięcy. Możesz również sprawdzić, czy typ w **wizualizacji** portu zestawu danych wynikowego to "**kategorii**".

Problem polega na tym, że kolumna "miesiąc" nie została systematycznie zakodowana. W niektórych przypadkach miesiąc jest określany jako Kwiecień, a w innych są skracane jako kwiecień. Możemy rozwiązać ten problem, przycinania ciągu do 3 znaków. Wiersz kodu wygląda teraz następująco:

```R
## Ensure the coding is consistent and convert column to a factor
cadairydata$Month <- as.factor(substr(cadairydata$Month, 1, 3))
```

Uruchom eksperyment i Wyświetl dziennik danych wyjściowych. Oczekiwane wyniki przedstawiono na rysunku 10.  

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

*Rysunek 10. Podsumowanie ramki danych o poprawnej liczbie poziomów czynników.*

Nasza zmienna Factor ma teraz żądane 12 poziomów.

### <a name="basic-data-frame-filtering"></a>Podstawowe filtrowanie ramek danych

Ramki danych języka R obsługują zaawansowane możliwości filtrowania. Zestawy danych mogą być podzbiorowe za pomocą filtrów logicznych w wierszach lub kolumnach. W wielu przypadkach wymagane są złożone kryteria filtrowania. Odwołania do [dalszych odczytów](#appendixb) poniżej zawierają obszerne przykłady filtrowania ramek danych.  

Istnieje jeden bit filtrowania, który powinienmy wykonać na naszym zestawie danych. Jeśli przeszukasz kolumny w ramce datacadairydata, zobaczysz dwie niepotrzebne kolumny. W pierwszej kolumnie znajduje się tylko numer wiersza, który nie jest bardzo przydatny. Druga kolumna, Year. month, zawiera nadmiarowe informacje. Można łatwo wykluczyć te kolumny przy użyciu następującego kodu R.

> [!NOTE]
> Od teraz w tej sekcji pokażę tylko dodatkowy kod dodawany w module [wykonywania skryptu języka R][execute-r-script] . Dodam każdy nowy wiersz **przed** `str()` funkcją. Używam tej funkcji do weryfikowania wyników w Azure Machine Learning Studio (klasyczny).

Dodaj następujący wiersz do mojego kodu języka R w module [wykonywania skryptu języka r][execute-r-script] .

```R
# Remove two columns we do not need
cadairydata <- cadairydata[, c(-1, -2)]
```

Uruchom ten kod w eksperymentie i sprawdź wynik z dziennika danych wyjściowych. Wyniki te przedstawiono na rysunku 11.

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

*Rysunek 11. Podsumowanie ramki Dataframe zawierającej dwie kolumny usunięte.*

Dobra wiadomość! Otrzymamy oczekiwane wyniki.

### <a name="add-a-new-column"></a>Dodaj nową kolumnę

Aby utworzyć modele szeregów czasowych, warto mieć kolumnę zawierającą miesiące od momentu rozpoczęcia szeregów czasowych. Utworzymy nową kolumnę "month. Count".

Aby pomóc w organizowaniu kodu, `num.month()`utworzymy pierwszą prostą funkcję. Następnie zastosujemy tę funkcję, aby utworzyć nową kolumnę w ramce Dataframe. Nowy kod jest następujący.

```R
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

Teraz można uruchomić zaktualizowany eksperyment i użyć dziennika wyjściowego w celu wyświetlenia wyników. Wyniki te przedstawiono na rysunku 12.

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

*Rysunek 12. Podsumowanie ramki danych z dodatkową kolumną.*

Wygląda na to, że wszystko działa. Mamy nową kolumnę o oczekiwanych wartościach w naszej ramce Dataframe.

### <a name="value-transformations"></a>Przekształcenia wartości

W tej sekcji wykonamy niektóre proste przekształcenia dotyczące wartości w niektórych kolumnach naszej ramki danych. Język R obsługuje niemal dowolne przekształcenia wartości. Odwołania do [dalszych odczytów](#appendixb) poniżej zawierają obszerne przykłady.

Jeśli zobaczysz wartości w podsumowaniu naszej ramki danych, zobaczysz coś niewidocznego w tym miejscu. Czy jest więcej lodów niż mleko produkowane w Kalifornii? Nie, oczywiście nie, ponieważ nie ma sensu, Sad, ponieważ ten fakt może być częścią Lovers lodów. Jednostki są różne. Cena jest w jednostkach funtów amerykańskich, mleko jest w jednostkach 1 M funtów amerykańskich, lody lodów jest w jednostkach 1 000 USD, a ser Cottage jest w jednostkach 1 000 funtów USD. Przy założeniu, że lody jest ważone o 6,5 funtów na galon, można łatwo wykonać mnożenie, aby przekonwertować te wartości, tak aby znajdowały się one w równych jednostkach 1 000 funtów.

Nasz model prognozowania używa modelu mnożenia na potrzeby trendu i sezonowego dostosowywania tych danych. Transformacja dzienników pozwala nam korzystać z modelu liniowego, upraszczając ten proces. Możemy zastosować transformację dziennika w tej samej funkcji, w której jest stosowany mnożnik.

W poniższym kodzie definiuję nową funkcję `log.transform()`i stosujemy ją do wierszy zawierających wartości liczbowe. Funkcja R `Map()` służy do zastosowania `log.transform()` funkcji do wybranych kolumn w ramce Dataframe. `Map()`jest podobna do `apply()` , ale zezwala na więcej niż jedną listę argumentów funkcji. Należy zauważyć, że lista mnożników dostarcza drugi argument do `log.transform()` funkcji. `na.omit()` Funkcja jest używana jako bit oczyszczania, aby upewnić się, że w ramce danych nie ma wartości brakujące lub niezdefiniowane.

```R
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
  ## If there is an exception, print the warningmessage to
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

W `log.transform()` funkcji występuje dość wiele. Większość tego kodu sprawdza potencjalne problemy z argumentami lub w odniesieniu do wyjątków, które nadal mogą wystąpić podczas obliczeń. Tylko kilka wierszy tego kodu faktycznie przeprowadza obliczenia.

Celem programowania obronnego jest uniknięcie awarii pojedynczej funkcji, która uniemożliwia kontynuowanie przetwarzania. Nieoczekiwana awaria długotrwałej analizy może być całkiem frustrujące dla użytkowników. Aby uniknąć tej sytuacji, należy wybrać domyślne wartości zwracane, które spowodują ograniczenie szkód do przetwarzania podrzędnego. Zostanie również wygenerowany komunikat informujący użytkowników o niepowodzeniu.

Jeśli nie są używane do obrony programowania w języku R, cały ten kod może wydawać się nieco przeciążać. Przeprowadzimy Cię przez główne kroki:

1. Zdefiniowano wektor czterech komunikatów. Komunikaty te służą do przekazywania informacji o niektórych możliwych błędach i wyjątkach, które mogą wystąpić w przypadku tego kodu.
2. Dla każdego przypadku zwracamy wartość "na". Istnieje wiele innych możliwości, które mogą mieć mniej efektów ubocznych. Można na przykład zwrócić wektor zer lub oryginalny wektor wejściowy.
3. Testy są uruchamiane na argumentach funkcji. W każdym przypadku, jeśli zostanie wykryty błąd, zwracana jest wartość domyślna, a komunikat jest generowany przez `warning()` funkcję. Używam `warning()` , a nie `stop()` w taki sposób, aby kończył wykonywanie, dokładnie to, co próbujemy uniknąć. Zwróć uwagę, że zapisałem ten kod w stylu proceduralnym, tak jak w tym przypadku podejście funkcjonalne zostało złożone i zasłonięte.
4. Obliczenia dzienników są opakowane w `tryCatch()` taki sposób, aby wyjątki nie powodowały nieoczekiwanego zatrzymania do przetwarzania. Bez `tryCatch()` większości błędów wywoływanych przez funkcje języka R, wynikiem jest sygnał zatrzymania, który to właśnie wszystko.

Wykonaj ten kod R w eksperymentie i obejrzyj wydruk wyjściowy w pliku Output. log. Zobaczysz teraz przekształcone wartości czterech kolumn w dzienniku, jak pokazano na rysunku 13.

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

*Rysunek 13. Podsumowanie przekształconych wartości w ramce Dataframe.*

Zobaczymy, że wartości zostały przekształcone. Produkcja mleka jest teraz znacznie większa niż w przypadku wszystkich innych produkcji produktu mleczarskiego, co pozwala na przeszukanie na skalę dzienników.

W tym momencie dane są czyszczone i gotowe do pewnej modelowania. Przeglądając podsumowanie wizualizacji dla danych wyjściowych zestawu wyników wykonanych w naszym module [skryptu języka R][execute-r-script] , zobaczysz, że kolumna "miesiąc" ma wartość "kategorii" z 12 unikatowymi wartościami.

## <a name="time-series-objects-and-correlation-analysis"></a><a id="timeseries"></a>Obiekty szeregów czasowych i analiza korelacji

W tej sekcji pokażemy kilka podstawowych obiektów szeregów czasowych R i przeanalizuje korelacje między niektórymi zmiennymi. Naszym celem jest wyjście ramki danych zawierającej informacje o korelacji parowania w kilku spowolnienia.

Kompletny kod języka R dla tej sekcji znajduje się w [MachineLearningSamples — notesy/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="time-series-objects-in-r"></a>Obiekty szeregów czasowych w języku R

Jak już wspomniano, szeregi czasowe są serią wartości danych indeksowanych według czasu. Obiekty szeregów czasowych języka R są używane do tworzenia i zarządzania indeksem czasu. Istnieje kilka zalet używania obiektów szeregów czasowych. Obiekty szeregów czasowych uwalniają Cię od wielu szczegółów dotyczących zarządzania wartościami indeksów szeregów czasowych, które są hermetyzowane w obiekcie. Ponadto obiekty szeregów czasowych umożliwiają użycie wielu metod szeregów czasowych do wykreślania, drukowania, modelowania itp.

Klasa szeregów czasowych POSIXct jest często używana i jest stosunkowo prosta. Ta klasa szeregów czasowych mierzy czas od rozpoczęcia epoki, 1 stycznia 1970. W tym przykładzie będziemy używać POSIXct obiektów szeregów czasowych. Inne powszechnie używane klasy obiektów szeregów czasowych języka R to zoo i XTS, rozszerzalne szeregy czasowe.

### <a name="time-series-object-example"></a>Przykład obiektu szeregów czasowych

Zacznijmy od naszego przykładu. Przeciągnij i upuść **Nowy** moduł [skryptu języka R][execute-r-script] do eksperymentu. Połącz port wyjściowy pozycję DataSet1 wyniku istniejącego modułu [wykonywania skryptu języka r][execute-r-script] z portem wejściowym pozycję DataSet1 nowego modułu [skryptu Execute języka r][execute-r-script] .

Tak jak w przypadku pierwszych przykładów, w miarę postępów w przykładzie, w niektórych przypadkach, w każdym kroku, pokażę tylko przyrostowe dodatkowe wiersze kodu R.  

#### <a name="reading-the-dataframe"></a>Odczytywanie ramki Dataframe

Pierwszym krokiem jest odczytanie w ramce Dataframe i upewnienie się, że otrzymamy oczekiwane wyniki. Poniższy kod powinien wykonać zadanie.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)
str(cadairydata) # Check the results
```

Teraz uruchom eksperyment. Dziennik nowego kształtu skryptu Execute R powinien wyglądać jak rysunek 14.

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

*Rysunek 14. Podsumowanie ramki danych w module wykonywania skryptu języka R.*

Te dane mają oczekiwane typy i format. Należy zauważyć, że kolumna "miesiąc" jest typu Factor i ma oczekiwaną liczbę poziomów.

#### <a name="creating-a-time-series-object"></a>Tworzenie obiektu szeregów czasowych

Musimy dodać obiekt szeregów czasowych do naszej ramki danych. Zastąp bieżący kod następującym, co spowoduje dodanie nowej kolumny klasy POSIXct.

```R
# Comment the following if using RStudio
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata) # Check the results
```

Teraz sprawdź dziennik. Powinien wyglądać jak rysunek 15.

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

*Rysunek 15. Podsumowanie ramki danych z obiektem szeregów czasowych.*

Możemy zobaczyć podsumowanie, że nowa kolumna jest w rzeczywistości klasy POSIXct.

### <a name="exploring-and-transforming-the-data"></a>Eksplorowanie i Przekształcanie danych

Zapoznajmy się z niektórymi zmiennymi w tym zestawie danych. Macierz scatterplot to dobry sposób na utworzenie szybkiego wyglądu. Zamieniam `str()` funkcję w poprzednim kodzie R o następujący wiersz.

```R
pairs(~ Cotagecheese.Prod + Icecream.Prod + Milk.Prod + N.CA.Fat.Price, data = cadairydata, main = "Pairwise Scatterplots of dairy time series")
```

Uruchom ten kod i zobacz, co się dzieje. Wykres utworzony na porcie urządzenia R powinien wyglądać jak rysunek 16.

![Scatterplot macierz wybranych zmiennych](./media/r-quickstart/fig17.png)

*Rysunek 16. Scatterplot macierz dla wybranych zmiennych.*

Relacje między tymi zmiennymi zawiera nieparzystą strukturę. Może to być spowodowane trendami danych i faktem, że nie zostały ustandaryzowane zmienne.

### <a name="correlation-analysis"></a>Analiza korelacji

Aby przeprowadzić analizę korelacji, musimy jednocześnie wycofać trendy i znormalizować zmienne. Można po prostu użyć funkcji języka `scale()` R, która w obu centrach i skaluje zmienne. Ta funkcja może być również szybsza. Jednak chcę pokazać przykład programów obronnych w języku R.

`ts.detrend()` Funkcja pokazana poniżej wykonuje obie te operacje. Poniższe dwa wiersze kodu usuwają trendy danych, a następnie standaryzacją wartości.

```R
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

W `ts.detrend()` funkcji występuje dość wiele. Większość tego kodu sprawdza potencjalne problemy z argumentami lub w odniesieniu do wyjątków, które nadal mogą wystąpić podczas obliczeń. Tylko kilka wierszy tego kodu faktycznie przeprowadza obliczenia.

Omawiamy już przykład programowania obronnego w transformacjach wartości. Oba bloki obliczeń są opakowane `tryCatch()`w. W przypadku niektórych błędów warto zwrócić oryginalny wektor wejściowy, a w innych przypadkach zwracamy wektor zer.  

Należy zauważyć, że regresja liniowa używana na potrzeby detrendu jest regresją szeregów czasowych. Zmienna predykcyjna to obiekt szeregów czasowych.  

Po `ts.detrend()` zdefiniowaniu, stosujemy go do zmiennych interesujących w naszej ramce danych. Należy wymusić wynikową listę utworzoną przez `lapply()` program do Dataframe danych przy `as.data.frame()`użyciu polecenia. Ze względu na aspekty `ts.detrend()`obronne, Niepowodzenie przetworzenia jednej ze zmiennych nie uniemożliwi prawidłowego przetwarzania innych elementów.  

Ostatni wiersz kodu tworzy scatterplot parowania. Po uruchomieniu kodu języka R wyniki scatterplot są pokazane na rysunku 17.

![Buforowanie scatterplot z nietrendową i ustandaryzowaną serią czasową](./media/r-quickstart/fig18.png)

*Rysunek 17. Buforowanie scatterplot z nietrendową i ustandaryzowaną serią czasową.*

Wyniki te można porównać do tych, które przedstawiono na rysunku 16. Po usunięciu trendu i znormalizowanych zmiennych zostanie wyświetlona znacznie mniejsza struktura relacji między tymi zmiennymi.

Kod do obliczenia korelacji jako obiektów R CCF jest następujący.

```R
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

Uruchomienie tego kodu spowoduje utworzenie dziennika pokazanego na rysunku 18.

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

*Rysunek 18. Lista obiektów CCF z analizy korelacji parowania.*

Dla każdego opóźnienia istnieje wartość korelacji. Żadna z tych wartości korelacji nie jest wystarczająco duża, aby była znacząca. W związku z tym można zawrzeć, że możemy modelować każdą zmienną niezależnie.

### <a name="output-a-dataframe"></a>Wyprowadzanie danych wyjściowych
Przeliczyłeś korelacje parowania jako listę obiektów CCF R. Przedstawia to bit problemu, ponieważ port wyjściowy zestawu danych wynikowych rzeczywiście wymaga ramki Dataframe. Ponadto obiekt CCF jest samym listą i chcemy tylko wartości z pierwszego elementu tej listy, korelacje w różnych spowolnienia.

Poniższy kod wyodrębnia wartości zwłok z listy obiektów CCF, które są same.

```R
df.correlations <- data.frame(do.call(rbind, lapply(cadairycorrelations, '[[', 1)))

c.names <- c("correlation pair", "-1 lag", "0 lag", "+1 lag")
r.names  <- c("Corr Cot Cheese - Ice Cream",
              "Corr Cot Cheese - Milk Prod",
              "Corr Cot Cheese - Fat Price",
              "Corr Ice Cream - Mik Prod",
              "Corr Ice Cream - Fat Price",
              "Corr Milk Prod - Fat Price")

## Build a dataframe with the row names column and the
## correlation data frame and assign the column names
outframe <- cbind(r.names, df.correlations)
colnames(outframe) <- c.names
outframe


## WARNING!
## The following line works only in Azure Machine Learning Studio (classic)
## When running in RStudio, this code will result in an error
#maml.mapOutputPort('outframe')
```

Pierwszy wiersz kodu jest lewę, a niektóre wyjaśnienia mogą pomóc w zrozumieniu. W tym celu należy wykonać następujące czynności:

1. Operator "**[[**" z argumentem "**1**" wybiera wektor korelacji w spowolnienia z pierwszego elementu listy obiektów CCF.
2. `do.call()` Funkcja stosuje `rbind()` funkcję względem elementów listy zwraca przez `lapply()`.
3. `data.frame()` Funkcja przekształca wynik tworzony przez `do.call()` element na ramkę danych.

Należy zauważyć, że nazwy wierszy znajdują się w kolumnie ramki danych. Wykonanie tej operacji zachowuje nazwy wierszy, gdy są one wyprowadzane ze [skryptu Execute języka R][execute-r-script].

Uruchomienie kodu generuje dane wyjściowe pokazane na rysunku 19 podczas **wizualizacji** danych wyjściowych w porcie zestawu danych wynikowych. Nazwy wierszy znajdują się w pierwszej kolumnie zgodnie z oczekiwaniami.

![Wyniki z analizy korelacji](./media/r-quickstart/fig20.png)

*Rysunek 19. Wyniki z analizy korelacji.*

## <a name="time-series-example-seasonal-forecasting"></a><a id="seasonalforecasting"></a>Przykład szeregów czasowych: prognozowanie sezonowe

Nasze dane znajdują się teraz w formie odpowiedniej do analizy, a firma Microsoft ustaliła, że nie ma znaczących korelacji między zmiennymi. Przyjrzyjmy się i utworzysz model prognozowania szeregów czasowych. Korzystając z tego modelu, firma Microsoft będzie prognozować produkcję mleka Kalifornii przez 12 miesięcy z 2013.

Nasz model prognozowania będzie miał dwa składniki, składnik trendu i składnik sezonowy. Pełną prognozą jest iloczyn tych dwóch składników. Ten typ modelu jest znany jako model mnożenia. Alternatywą jest model dodatków. Zastosowano już transformację dziennika do interesujących zmiennych, co sprawia, że ta analiza jest pożądana.

Kompletny kod języka R dla tej sekcji znajduje się w [MachineLearningSamples — notesy/Studio-Samples](https://github.com/Azure-Samples/MachineLearningSamples-Notebooks/tree/master/studio-samples).

### <a name="creating-the-dataframe-for-analysis"></a>Tworzenie ramki danych do analizy

Zacznij od dodania **nowego** modułu [skryptu wykonania języka R][execute-r-script] do Twojego eksperymentu. Połącz dane wyjściowe **zestawu danych wyników** istniejącego modułu [wykonywania skryptu języka R][execute-r-script] z **pozycję DataSet1** wejściem nowego modułu. Wynik powinien wyglądać podobnie do ilustracji 20.

![Eksperyment z nowym modułem wykonywania skryptu języka R](./media/r-quickstart/fig21.png)

*Rysunek 20. Eksperyment z nowym modułem wykonywania skryptu języka R.*

Podobnie jak w przypadku analizy korelacji, musimy dodać kolumnę z POSIXct obiektem szeregów czasowych. Poniższy kod wykona tylko te czynności.

```R
# If running in Machine Learning Studio (classic), uncomment the first line with maml.mapInputPort()
cadairydata <- maml.mapInputPort(1)

## Create a new column as a POSIXct object
Sys.setenv(TZ = "PST8PDT")
cadairydata$Time <- as.POSIXct(strptime(paste(as.character(cadairydata$Year), "-", as.character(cadairydata$Month.Number), "-01 00:00:00", sep = ""), "%Y-%m-%d %H:%M:%S"))

str(cadairydata)
```

Uruchom ten kod i obejrzyj dziennik. Wynik powinien wyglądać jak rysunek 21.

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

*Rysunek 21. Podsumowanie ramki Dataframe.*

Z tego powodu jesteśmy gotowi do rozpoczęcia analizy.

### <a name="create-a-training-dataset"></a>Tworzenie zestawu danych szkoleniowych

Z konstruowaną ramką Dataframe musimy utworzyć zestaw danych szkoleniowych. Te dane obejmują wszystkie obserwacje z wyjątkiem ostatnich 12, roku 2013, który jest naszym testowym zestawem danych. Poniższy kod podpisuje ramkę Dataframe i tworzy wykresy produkcji mlecznej i zmiennych cenowych. Następnie tworzymy wykresy czterech zmiennych produkcyjnych i cen. Funkcja anonimowa służy do definiowania niektórych rozszerzeń dla wykresu, a następnie iteracji na liście pozostałych dwóch argumentów za pomocą `Map()`. Jeśli zastanawiasz się, że pętla for będzie działała prawidłowo, musisz być poprawna. Ale ponieważ R to język funkcjonalny, przedstawiamy podejście funkcjonalne.

```R
cadairytrain <- cadairydata[1:216, ]

Ylabs  <- list("Log CA Cotage Cheese Production, 1000s lb",
               "Log CA Ice Cream Production, 1000s lb",
               "Log CA Milk Production 1000s lb",
               "Log North CA Milk Milk Fat Price per 1000 lb")

Map(function(y, Ylabs){plot(cadairytrain$Time, y, xlab = "Time", ylab = Ylabs, type = "l")}, cadairytrain[, 4:7], Ylabs)
```

Uruchomienie kodu powoduje utworzenie serii wykresów czasu z danych wyjściowych urządzenia R przedstawionych na rysunku 22. Należy zauważyć, że oś czasu jest w jednostkach dat, a całkiem korzystne dla metody kreślenia w szeregach czasowych.

![Pierwsze wykresy z serii czasowych Kalifornii produkcja mleczna i dane cen](./media/r-quickstart/unnamed-chunk-161.png)

![Drugie z serii czasowych — dane dotyczące produkcji i cen mlecznych](./media/r-quickstart/unnamed-chunk-162.png)

![Trzecie wykresy z serii czasowych Kalifornii produkcja mleczna i dane cen](./media/r-quickstart/unnamed-chunk-163.png)

![Czwarte wykresy serii czasu Kalifornii dane produkcyjne i ceny](./media/r-quickstart/unnamed-chunk-164.png)

*Rysunek 22. Wykresy szeregów czasowych.*

### <a name="a-trend-model"></a>Model trendu

Po utworzeniu obiektu szeregów czasowych i wyszukaniu danych Przyjrzyjmy się modelowi trendu dla danych produkcyjnych z mleka California. Można to zrobić przy użyciu regresji szeregów czasowych. Jednak jest jasne od wykresu, który potrzebuje więcej niż spadku i przechwycenia, aby dokładnie modelować obserwowany trend w danych szkoleniowych.

Uwzględniając małą skalę danych, utworzymy model dla trendu w RStudio, a następnie wytniesz i wkleisz model wynikający z Azure Machine Learning Studio (klasyczny). RStudio zapewnia interaktywny środowisko dla tego typu interaktywnej analizy.

Przy pierwszej próbie wypróbuje regresję wielomianową z uprawnieniami do 3. Istnieje realne niebezpieczeństwo zbyt dobrze dopasowanego rodzaju modeli. W związku z tym najlepszym rozwiązaniem jest uniknięcie wysokich warunków. `I()` Funkcja wstrzymuje interpretację zawartości (interpretuje zawartość jako ") i umożliwia pisanie dosłownie interpretowanej funkcji w równaniu regresji.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^2) + I(Month.Count^3), data = cadairytrain)
summary(milk.lm)
```

Spowoduje to wygenerowanie następujących danych.

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

Od wartości P (`Pr(>|t|)`) w tych danych wyjściowych widać, że kwadratowy termin nie może być znaczący. Użyję funkcji, `update()` aby zmodyfikować ten model poprzez upuszczenie kwadratowego okresu.

```R
milk.lm <- update(milk.lm, . ~ . - I(Month.Count^2))
summary(milk.lm)
```

Spowoduje to wygenerowanie następujących danych.

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

To wygląda lepiej. Wszystkie warunki są istotne. Jednak wartość 2e-16 jest wartością domyślną i nie powinna być pobierana zbyt poważnie.  

W ramach testu Sanity Przyjrzyjmy się wykreśleniem szeregów czasowych danych produkcyjnych w stanie Kalifornia z pokazaną krzywą trendu. Dodano następujący kod w Azure Machine Learning Studio (klasyczny) model [skryptu][execute-r-script] RStudio (nie), aby utworzyć model i stworzyć wykres. Wynik jest pokazywany na rysunku 23.

```R
milk.lm <- lm(Milk.Prod ~ Time + I(Month.Count^3), data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm, cadairytrain), lty = 2, col = 2)
```

![Pokazane dane produkcji z mleka Kalifornii z modelem trendu](./media/r-quickstart/unnamed-chunk-18.png)

*Rysunek 23. Pokazuje dane produkcji z mleka Kalifornii z modelem trendu.*

Wygląda na to, że model trendu pasuje do danych dość dobrze. Jeszcze nie wydaje się, że nie jest dowodem nad nadmiernym dopasowaniem, na przykład nieparzystą wigglesą w krzywej modelu.  

### <a name="seasonal-model"></a>Model sezonowy

Dzięki modelowi trendu musimy przeprowadzić wypychanie i uwzględnić efekty sezonowe. Użyjemy miesiąca roku jako fikcyjnej zmiennej w modelu liniowym, aby przechwycić wpływ na miesiąc. Należy pamiętać, że po wprowadzeniu zmiennych współczynnika do modelu nie można obliczyć przechwycenia. Jeśli to nie zrobisz, formuła jest przekroczenia, a R porzuca jeden z żądanych czynników, ale utrzymuje okres przechwycenia.

Ze względu na to, że mamy zadowalający model trendów, możemy użyć `update()` funkcji, aby dodać nowe warunki do istniejącego modelu. Wartość-1 w formule aktualizacji opuszcza okres przechwycenia. W tej chwili nadal trwa RStudio:

```R
milk.lm2 <- update(milk.lm, . ~ . + Month - 1)
summary(milk.lm2)
```

Spowoduje to wygenerowanie następujących danych.

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

Widzimy, że model nie ma już okresu przechwycenia i ma 12 znaczących czynników. To dokładnie to, czego potrzebujemy.

Utwórzmy kolejny wykres szeregów czasowych danych produkcji Kalifornii, aby zobaczyć, jak dobrze działa model sezonowy. Dodano następujący kod w Azure Machine Learning Studio (klasyczny) [Uruchom skrypt języka R][execute-r-script] w celu utworzenia modelu i utworzenia wykresu.

```R
milk.lm2 <- lm(Milk.Prod ~ Time + I(Month.Count^3) + Month - 1, data = cadairytrain)

plot(cadairytrain$Time, cadairytrain$Milk.Prod, xlab = "Time", ylab = "Log CA Milk Production 1000s lb", type = "l")
lines(cadairytrain$Time, predict(milk.lm2, cadairytrain), lty = 2, col = 2)
```

Uruchomienie tego kodu w Azure Machine Learning Studio (klasyczny) powoduje wyświetlenie wykresu na rysunku 24.

![Produkcja mleka Kalifornii z modelem obejmującym efekty sezonowe](./media/r-quickstart/unnamed-chunk-20.png)

*Rysunek 24. Produkcja mleka Kalifornii z modelem, w tym skutki sezonowe.*

Dopasowanie do danych pokazanych na rysunku 24 jest raczej zachęcane. Tendencja i skutek sezonowy (odmiana miesięczna) wyglądają rozsądnie.

Podobnie jak w przypadku innego sprawdzenia modelu, przyjrzyjmy się resztom. Poniższy kod oblicza przewidywane wartości z naszych dwóch modeli, oblicza reszty dla modelu sezonowego, a następnie wykreśla te reszty dla danych szkoleniowych.

```R
## Compute predictions from our models
predict1  <- predict(milk.lm, cadairydata)
predict2  <- predict(milk.lm2, cadairydata)

## Compute and plot the residuals
residuals <- cadairydata$Milk.Prod - predict2
plot(cadairytrain$Time, residuals[1:216], xlab = "Time", ylab ="Residuals of Seasonal Model")
```

Wykres resztkowy jest pokazywany na rysunku 25.

![Pozostałości modelu sezonowego dla danych szkoleniowych](./media/r-quickstart/unnamed-chunk-21.png)

*Rysunek 25. Pozostałości modelu sezonowego dla danych szkoleniowych.*

Są one odpowiednie do wyszukania. Nie ma określonej struktury, z tą różnicą, że nie jest to wpływ na 2008-2009, który model nie jest szczególnie używany.

Wykres przedstawiony na rysunku 25 jest przydatny do wykrywania wszelkich wzorców zależnych od czasu w pozostałych. Jawne podejście do obliczeń i wykreślania użytych reszty umieszcza reszty w kolejności czasu na powierzchni. Jeśli z drugiej strony wykreślono `milk.lm$residuals`, wykres nie był w kolejności w czasie.

Można również użyć `plot.lm()` do tworzenia serii wykresów diagnostycznych.

```R
## Show the diagnostic plots for the model
plot(milk.lm2, ask = FALSE)
```

Ten kod tworzy serię wykresów diagnostycznych przedstawionych na rysunku 26.

![Pierwszy z wykresów diagnostycznych dla modelu sezonowego](./media/r-quickstart/unnamed-chunk-221.png)

![Sekundę z wykresów diagnostycznych dla modelu sezonowego](./media/r-quickstart/unnamed-chunk-222.png)

![Trzecia część wykresów diagnostycznych dla modelu sezonowego](./media/r-quickstart/unnamed-chunk-223.png)

![Czwarta z wykresów diagnostycznych dla modelu sezonowego](./media/r-quickstart/unnamed-chunk-224.png)

*Rysunek 26. Wykresy diagnostyczne dla modelu sezonowego.*

W tych wykresach znajdują się kilka bardzo znaczących punktów, ale nic nie może mieć dużego znaczenia. Dodatkowo możemy zobaczyć, że na zwykłych wykresach Q-Q jest bliski rozkładu, ważne założenie dla modeli liniowych.

### <a name="forecasting-and-model-evaluation"></a>Prognozowanie i Ocena modelu

Aby ukończyć nasz przykład, należy wykonać tylko jedną czynność. Musimy obliczyć prognozy i zmierzyć błąd w odniesieniu do rzeczywistych danych. Nasza prognoza będzie wynosić 12 miesięcy od 2013. Możemy obliczyć miarę błędu dla tej prognozy na rzeczywiste dane, które nie są częścią naszego zestawu danych szkoleniowych. Ponadto możemy porównać wydajność z 18 lat danych szkoleniowych w ciągu 12 miesięcy od danych testowych.  

Wiele metryk służy do mierzenia wydajności modeli szeregów czasowych. W naszym przypadku zostanie użyty błąd średniego kwadratu (RMS). Następująca funkcja oblicza błąd usługi RMS między dwiema seriami.  

```R
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

Podobnie jak w `log.transform()` przypadku funkcji omówionej w sekcji "przekształcenia wartości" w tej funkcji występuje wiele błędów sprawdzania i kodu odzyskiwania wyjątków. Zastosowane zasady są takie same. Prace odbywają się w dwóch miejscach opakowanych `tryCatch()`w. Najpierw seria czasowa to exponentiated, ponieważ pracujemy z dziennikami wartości. Po drugie jest obliczany rzeczywisty błąd usługi RMS.  

Za pomocą funkcji służącej do mierzenia błędu usługi RMS można utworzyć i wypróbować ramkę danych zawierającą błędy usługi RMS. Zostaną uwzględnione warunki dla samego modelu trendu i kompletny model z sezonowymi czynnikami. Poniższy kod wykonuje zadanie przy użyciu dwóch modeli liniowych, które zostały zbudowane.

```R
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

Uruchomienie tego kodu powoduje wygenerowanie danych wyjściowych przedstawionych na rysunku 27 na porcie wyjściowym zestawu danych wynikowych.

![Porównanie błędów usługi RMS dla modeli](./media/r-quickstart/fig26.png)

*Rysunek 27. Porównanie błędów usługi RMS dla modeli.*

Z tych wyników widzimy, że dodanie wskaźników sezonowych do modelu zmniejsza istotny błąd usługi RMS. Nie za Surprisingly, błąd usługi RMS dla danych szkoleniowych jest mniejszy niż w przypadku prognozowania.

## <a name="guide-to-rstudio-documentation"></a><a id="appendixa"></a>Przewodnik dotyczący dokumentacji RStudio

RStudio jest całkiem dobrze udokumentowane. Oto kilka linków do najważniejszych sekcji dokumentacji RStudio, aby rozpocząć pracę.

* **Tworzenie projektów** — możesz organizować kod R i zarządzać nim w projektach za pomocą RStudio. Szczegóły można znaleźć w temacie [using projects](https://support.rstudio.com/hc/articles/200526207-Using-Projects) . Zalecamy wykonanie tych instrukcji i utworzenie projektu dla przykładów kodu języka R w tym artykule.  
* **Edytowanie i wykonywanie kodu języka r** — RStudio zapewnia zintegrowane środowisko do edycji i wykonywania kodu języka r. Zobacz [Edytowanie i wykonywanie kodu,](https://support.rstudio.com/hc/articles/200484448-Editing-and-Executing-Code) Aby uzyskać szczegółowe informacje.
* **Debugowanie** — RStudio obejmuje zaawansowane funkcje debugowania. Aby uzyskać więcej informacji o tych funkcjach, zobacz [debugowanie za pomocą RStudio](https://support.rstudio.com/hc/articles/200713843-Debugging-with-RStudio) . Aby uzyskać informacje o funkcjach rozwiązywania problemów z punktem przerwania, zobacz temat [Rozwiązywanie problemów](https://support.rstudio.com/hc/articles/200534337-Breakpoint-Troubleshooting)

## <a name="further-reading"></a><a id="appendixb"></a>Dodatkowe informacje

Ten samouczek programowania w języku R obejmuje podstawowe informacje o tym, co jest potrzebne do korzystania z języka R z Azure Machine Learning Studio (klasyczny). Jeśli nie znasz języka R, dostępne są dwa wprowadzenie w witrynie CRAN:

* Emmanuel Paradis jest dobrym [miejscem do rozpoczęcia](https://cran.r-project.org/doc/contrib/Paradis-rdebuts_en.pdf) pracy.  
* [Wprowadzenie do języka R](https://cran.r-project.org/doc/manuals/R-intro.html) przez w. N. Venables et. wsp. przechodzi do większej głębi.

Istnieje wiele książek w języku R, które mogą pomóc Ci rozpocząć pracę. Poniżej znajdują się informacje przydatne:

* **Ilustracja programowania w języku r: Przewodnik projektowania oprogramowania statystycznego** przez Normanowi Matloff jest doskonałym wprowadzeniem do programowania w języku R.  
* **Cookbook r** przez Paul Teetor zapewnia rozwiązanie problemu i rozwiązania do korzystania z języka r.  
* Język **R w działaniu** przez Robert Kabacoff jest kolejną przydatną książką wprowadzającą. [Szybka witryna sieci Web](https://www.statmethods.net/) w usłudze R to przydatne zasoby.
* **Inferno r** to Surprisingly witryna zawiera humorystyczną książka, która zajmuje się wielu Lew i trudnymi tematami, które można napotkać podczas programowania w języku R. Książka jest dostępna bezpłatnie w [Inferno R](https://www.burns-stat.com/documents/books/the-r-inferno/).
* Jeśli chcesz, aby głębokie szczegółowe zajrzeć do zaawansowanych tematów w języku R, zapoznaj się z książką **Advanced R** by Hadley Wickham. Wersja online tej książki jest dostępna bezpłatnie pod adresem [http://adv-r.had.co.nz/](http://adv-r.had.co.nz/).

Wykaz pakietów szeregów czasowych języka R można znaleźć w [widoku zadań Cran: analiza szeregów czasowych](https://cran.r-project.org/web/views/TimeSeries.html). Aby uzyskać informacje o określonych pakietach obiektów szeregów czasowych, należy zapoznać się z dokumentacją tego pakietu.

**Seria czasowa wprowadzająca** w książce z r przez Paul Cowpertwait i Andrew Metcalfe zawiera wprowadzenie do analizy szeregów czasowych. Wiele bardziej teoretycznych tekstów zawiera przykłady języka R.

Oto kilka doskonałych zasobów internetowych:

* Datacamp uczą się języka R w celu wygody swojej przeglądarki dzięki wykorzystaniu lekcji wideo i ćwiczeń programistycznych. Istnieją Interaktywne samouczki dotyczące najnowszych technik i pakietów języka R. Wykonaj bezpłatny [samouczek języka R](https://www.datacamp.com/courses/introduction-to-r)w trybie interaktywnym.
* [Poznaj Programowanie języka R, ostateczny Przewodnik](https://www.programiz.com/r-programming) od Programiz.
* Szybki [samouczek języka R](https://www.cyclismo.org/tutorial/R/) , Kelly czerń z Clarkson University.
* Istnieje ponad 60 zasobów R wymienionych w [najważniejszych zasobach języka r, aby zwiększyć swoje umiejętności związane z danymi](https://www.computerworld.com/article/2497464/business-intelligence-60-r-resources-to-improve-your-data-skills.html).

<!-- Module References -->
[execute-r-script]: /azure/machine-learning/studio-module-reference/execute-r-script
