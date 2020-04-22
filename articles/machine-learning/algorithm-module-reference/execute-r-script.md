---
title: 'Wykonanie skryptu R: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak użyć modułu Wykonywanie skryptu R w usłudze Azure Machine Learning do uruchamiania kodu języka R.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: eb778c8d24639320b60927438de76a29de724ac2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684712"
---
# <a name="execute-r-script"></a>Wykonywanie skryptu języka R

W tym artykule opisano, jak używać modułu **Wykonywanie skryptu języka R** do uruchamiania kodu języka R w potoku projektanta usługi Azure Machine Learning (w wersji zapoznawczej).

Za pomocą r można wykonywać zadania, które nie są obecnie obsługiwane przez istniejące moduły, takie jak: 
- Tworzenie niestandardowych przekształceń danych
- Użyj własnych wskaźników do oceny prognoz
- Tworzenie modeli przy użyciu algorytmów, które nie są implementowane jako autonomiczne moduły w projektancie

## <a name="r-version-support"></a>Obsługa wersji R

Projektant usługi Azure Machine Learning używa dystrybucji języka R (Comprehensive R Archive Network) w usłudze Cran (Comprehensive R Archive Network). Obecnie używana wersja to CRAN 3.5.1.

## <a name="supported-r-packages"></a>Obsługiwane pakiety języka R

Środowisko R jest wstępnie zainstalowane z ponad 100 pakietami. Aby uzyskać pełną listę, zobacz sekcję [Wstępnie zainstalowane pakiety R](#pre-installed-r-packages).

Można również dodać następujący kod do dowolnego **modułu Wykonywanie skryptu R** i wyświetlić zainstalowane pakiety.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>Instalowanie pakietów R
Aby zainstalować dodatkowe pakiety `install.packages()` R, należy użyć metody. Pakiety są instalowane dla każdego **modułu skryptu wykonywanie języka R** i nie są współużytkowane przez inne moduły **skryptu wykonywanie języka R.**

> [!NOTE]
> Podczas instalowania pakietów, takich jak`install.packages("zoo",repos = "http://cran.us.r-project.org")`

W tym przykładzie pokazano, jak zainstalować Zoo:
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# Please note that functions dependant on X11 library
# such as "View" are not supported because X11 library
# is not pre-installed.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  
  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")
  library(zoo)
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```
 > [!NOTE]
  > Sprawdź, czy pakiet już istnieje przed zainstalowaniem go, aby uniknąć ponownej instalacji. Podobnie `  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")` jak w powyższym przykładowym kodzie. Ponowna instalacja może spowodować przesunienie limitu czasu żądania usługi sieci web.     

## <a name="upload-files"></a>Przekazywanie plików
**Skrypt Wykonywanie języka R** obsługuje przekazywanie plików przy użyciu narzędzia Azure Machine Learning RDK.

W poniższym przykładzie pokazano, jak przekazać plik obrazu w **skrypcie Wykonywanie języka R:**
```R

# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# Please note that functions dependant on X11 library
# such as "View" are not supported because X11 library
# is not pre-installed.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")

  # Generate a jpeg graph
  img_file_name <- "rect.jpg"
  jpeg(file=img_file_name)
  example(rect)
  dev.off()

  upload_files_to_run(names = list(file.path("graphic", img_file_name)), paths=list(img_file_name))


  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

Po zakończeniu przebiegu potoku można wyświetlić podgląd obrazu w prawym panelu modułu

> [!div class="mx-imgBorder"]
> ![Przesłany obraz](media/module/upload-image-in-r-script.png)

## <a name="how-to-configure-execute-r-script"></a>Jak skonfigurować skrypt wykonywania języka R

Moduł **Wykonaj skrypt R** zawiera przykładowy kod, którego można użyć jako punktu wyjścia. Aby skonfigurować moduł **Wykonywanie skryptu R,** podaj zestaw danych wejściowych i kodu do wykonania.

![Moduł R](media/module/execute-r-script.png)

Zestawy danych przechowywane w projektancie są automatycznie konwertowane na ramkę danych R po załadowaniu z tym modułem.

1.  Dodaj moduł **Wykonaj skrypt R** do potoku.

  

1. Połącz wszystkie dane wejściowe wymagane przez skrypt. Dane wejściowe są opcjonalne i mogą zawierać dane i dodatkowy kod R.

    * **Zestaw danych1**: Odwołanie `dataframe1`się do pierwszego wejścia jako . Wejściowy zestaw danych musi być sformatowany jako zestaw DANYCH CSV, TSV, ARFF lub można połączyć zestaw danych usługi Azure Machine Learning.

    * **Zestaw danych2**: Odwołanie `dataframe2`się do drugiego wejścia jako . Ten zestaw danych musi być również sformatowany jako plik CSV, TSV, ARFF lub jako zestaw danych usługi Azure Machine Learning.

    * **Pakiet skryptów:** Trzecie dane wejściowe akceptują pliki ZIP. Spakowany plik może zawierać wiele plików i wiele typów plików.

1. W polu tekstowym **skryptu R** wpisz lub wklej prawidłowy skrypt R.

    > [!NOTE]
    > Należy zachować szczególną ostrożność podczas pisania skryptu i upewnia się, że nie ma błędu składni, takich jak przy użyciu nie zadeklarowanej zmiennej lub niezaimportowanego modułu lub funkcji. Należy również zwrócić szczególną uwagę na wstępnie zainstalowaną listę pakietów na końcu tego dokumentu. Aby użyć pakietów, których nie ma na liście, należy zainstalować je w skrypcie, takich jak`install.packages("zoo",repos = "http://cran.us.r-project.org")`
    
    > [!NOTE]
    > Funkcje zależne od biblioteki X11, takie jak "Widok", nie są obsługiwane, ponieważ biblioteka X11 nie jest wstępnie zainstalowana.
    
    Aby ułatwić rozpoczęcie pracy, pole tekstowe **Skrypt R** jest wstępnie wypełnione przykładowym kodem, który można edytować lub zastąpić.
    
    ```R
    # R version: 3.5.1
    # The script MUST contain a function named azureml_main
    # which is the entry point for this module.

    # Please note that functions dependant on X11 library
    # such as "View" are not supported because X11 library
    # is not pre-installed.
    
    # The entry point function MUST have two input arguments.
    # If the input port is not connected, the corresponding
    # dataframe argument will be null.
    #   Param<dataframe1>: a R DataFrame
    #   Param<dataframe2>: a R DataFrame
    azureml_main <- function(dataframe1, dataframe2){
    print("R script run.")

    # If a zip file is connected to the third input port, it is
    # unzipped under "./Script Bundle". This directory is added
    # to sys.path.

    # Return datasets as a Named List
    return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

 * Skrypt musi zawierać funkcję `azureml_main`o nazwie , która jest punktem wejścia dla tego modułu.

 * Funkcja punktu wejścia musi mieć `Param<dataframe1>` dwa `Param<dataframe2>`argumenty wejściowe: i , nawet jeśli te dwa argumenty nie są używane w funkcji.

   > [!NOTE]
    > Dane przekazywane do modułu **Wykonywanie skryptu R** są odwoływane jako `dataframe1` i `dataframe2`, który `dataset1` `dataset2`różni się od projektanta usługi Azure Machine Learning (odwołanie projektanta jako , ). Sprawdź, czy dane wejściowe są poprawnie odnajdowane w skrypcie.  
 
    > [!NOTE]
    >  Istniejący kod R może wymagać drobnych zmian do uruchomienia w potoku projektanta. Na przykład dane wejściowe podane w formacie CSV powinny być jawnie konwertowane na zestaw danych, zanim będzie można go użyć w kodzie. Typy danych i kolumn używane w języku Języka Języka R również różnią się pod pewnymi względami od typów danych i kolumn używanych w projektancie.

1.  **Losowy materiał siewny:** Wpisz wartość do użycia w środowisku R jako losową wartość materiału siewnego. Ten parametr jest `set.seed(value)` odpowiednikiem wywołania w kodzie R.  

1. Prześlij potok.  

## <a name="results"></a>Wyniki

Moduły **Skryptu wykonywania języka R** mogą zwracać wiele wyjść, ale muszą być dostarczane jako ramki danych języka R. Ramki danych są automatycznie konwertowane na zestawy danych w projektancie w celu zapewnienia zgodności z innymi modułami.

Standardowe komunikaty i błędy z R są zwracane do dziennika modułu.

Jeśli chcesz wydrukować wyniki w skrypcie R, Można znaleźć wydrukowane wyniki w **70_driver_log** w obszarze **Wyjścia + dzienniki** kartę w prawym panelu modułu.

## <a name="sample-scripts"></a>Przykładowe skrypty

Istnieje wiele sposobów, które można rozszerzyć potoku przy użyciu niestandardowego skryptu Języka R.  Ta sekcja zawiera przykładowy kod dla typowych zadań.


### <a name="add-r-script-as-an-input"></a>Dodawanie skryptu R jako danych wejściowych

Moduł **Execute R Script** obsługuje dowolne pliki skryptów Języka R jako dane wejściowe. Aby to zrobić, należy je przekazać do obszaru roboczego jako część pliku ZIP.

1. Aby przekazać plik ZIP zawierający kod R do obszaru roboczego, przejdź do strony zasobów **Zestawów danych,** kliknij pozycję **Utwórz zestaw danych**, a następnie wybierz opcję **Z pliku lokalnego** i opcję Typ zestawu danych **Plik.**  

1. Sprawdź, czy spakowany plik jest dostępny na liście **Moje zestawy danych** w obszarze Zestawy **danych** w drzewie lewego modułu.

1.  Podłącz zestaw danych do portu wejściowego **pakietu skryptów.**

1. Wszystkie pliki, które są zawarte w pliku ZIP są dostępne w czasie wykonywania potoku. 

    Jeśli plik pakietu skryptów zawierał strukturę katalogów, struktura jest zachowywana. Należy jednak zmienić kod, aby dołączyć katalog **./Script Bundle** do ścieżki.

### <a name="process-data"></a>Przetwarzanie danych

W poniższym przykładzie pokazano, jak skalować i normalizować dane wejściowe:

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# Please note that functions dependant on X11 library
# such as "View" are not supported because X11 library
# is not pre-installed.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a zip file is connected to the third input port, it is
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # find the maximum and minimum values of width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>Odczytywanie pliku ZIP jako danych wejściowych

W tym przykładzie pokazano, jak używać zestawu danych w pliku ZIP jako dane wejściowe do modułu **Wykonywanie skryptu R.**

1. Utwórz plik danych w formacie CSV i nazwij go "mydatafile.csv".
1. Utwórz plik ZIP i dodaj plik CSV do archiwum.
1. Przekaż spakowany plik do obszaru roboczego usługi Azure Machine Learning. 
1. Połącz wynikowy zestaw danych z wejściem **ScriptBundle** modułu **Skryptu wykonywania języka R.**
1. Używanie następującego kodu do odczytu danych CSV z pliku spakowanym.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Replikowanie wierszy

W tym przykładzie pokazano, jak replikować rekordy dodatnie w zestawie danych, aby zrównoważyć próbkę:

```R
azureml_main <- function(dataframe1, dataframe2){
  data.set <- dataframe1[dataframe1[,1]==-1,]  
  # positions of the positive samples
  pos <- dataframe1[dataframe1[,1]==1,]
  # replicate the positive samples to balance the sample  
  for (i in 1:20) data.set <- rbind(data.set,pos)  
  row.names(data.set) <- NULL
  # Return datasets as a Named List
  return(list(dataset1=data.set, dataset2=dataframe2))
}
```

### <a name="pass-r-objects-between-execute-r-script-modules"></a>Przekazywanie obiektów R między modułami skryptu Wykonywanie języka R

Obiekty języka R można przekazać między wystąpieniami modułu **Wykonywanie skryptu R** przy użyciu wewnętrznego mechanizmu serializacji. W tym przykładzie przyjęto założenie, `A` że chcesz przenieść R obiektu o nazwie między dwoma **wykonać skrypt R** modułów.

1. Dodaj pierwszy moduł **Wykonaj skrypt R** do potoku i wpisz następujący kod w polu tekstowym Skrypt **R,** aby utworzyć obiekt `A` szeregowy jako kolumnę w wyjściowej tabeli danych modułu:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    Jawna konwersja do typu liczby całkowitej odbywa się, ponieważ `Raw` funkcja serializacji wyprowadza dane w formacie R, który nie jest obsługiwany przez projektanta.

1. Dodaj drugie wystąpienie modułu **Wykonaj skrypt R** i podłącz go do portu wyjściowego poprzedniego modułu.

1. Wpisz następujący kod w polu tekstowym `A` **Skrypt R,** aby wyodrębnić obiekt z wejściowej tabeli danych. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Wstępnie zainstalowane pakiety R

Aktualna lista wstępnie zainstalowanych pakietów R dostępnych do użycia:

|              |            | 
|--------------|------------| 
| Pakiet      | Wersja    | 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| base         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| rozruch         | 1.3-22     | 
| broom        | 0.5.2      | 
| callr        | 3.2.0      | 
| caret        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| class        | 7.3-15     | 
| interfejs wiersza polecenia          | 1.1.0      | 
| suwnik        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
| colorspace   | 1.4-1      | 
|  — kompilator     | 3.5.1      | 
| crayon       | 1.3.4      | 
| curl         | 3.3        | 
| data.table   | 1.12.2     | 
| zestawy danych     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| evaluate     | 0.14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| foreign      | 0.8-71     | 
| Fs           | 1.3.1      | 
| gdata        | 2.18.0     | 
| Generyczne     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
| glue         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| grafika     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| grid         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| haven        | 2.1.0      | 
| highr        | 0,8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0.9-9      | 
| iterators    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1,23       | 
| labeling     | 0.3        | 
| lattice      | 0.20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| markdown     | 1          | 
| MASS         | 7.3-51.4   | 
| Matrix       | 1.2-17     | 
| methods      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| mime         | 0.7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| openssl      | 1.4        | 
| parallel     | 3.5.1      | 
| pillar       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| progress     | 1.2.2      | 
| Ps           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1.5-7      | 
| quantmod     | 0.4-15     | 
| R6           | 2.4.0      | 
| randomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| readr        | 1.3.1      | 
| readxl       | 1.3.1      | 
| recipes      | 0.1.5      | 
| rematch      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1.12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1.13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0.1        | 
| rvest        | 0.3.4      | 
| scales       | 1.0.0      | 
| selectr      | 0.4-1      | 
| spatial      | 7.3-11     | 
| splines      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| stats        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| survival     | 2.44-1.1   | 
| sys          | 3,2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex ( tinytex )      | 0.13       | 
| narzędzia        | 3.5.1      | 
| tseries      | 0.10-47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun (1000         | 0,8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoo          | 1.8-6      | 

## <a name="next-steps"></a>Następne kroki

Zobacz [zestaw modułów dostępnych dla](module-reference.md) usługi Azure Machine Learning. 
