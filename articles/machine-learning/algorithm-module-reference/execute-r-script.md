---
title: 'Wykonaj skrypt języka R: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak uruchomić kod języka R przy użyciu modułu uruchamiania skryptu języka R w Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: eb778c8d24639320b60927438de76a29de724ac2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81684712"
---
# <a name="execute-r-script"></a>Wykonywanie skryptu języka R

W tym artykule opisano, jak używać modułu **skryptu języka r** do uruchamiania kodu języka r w potoku programu Azure Machine Learning Designer (wersja zapoznawcza).

Za pomocą języka R można wykonywać zadania, które nie są obecnie obsługiwane przez istniejące moduły, takie jak: 
- Tworzenie niestandardowych transformacji danych
- Używanie własnych metryk do szacowania prognoz
- Tworzenie modeli przy użyciu algorytmów, które nie są zaimplementowane jako moduły autonomiczne w projektancie

## <a name="r-version-support"></a>Obsługa wersji języka R

Program Azure Machine Learning Designer używa dystrybucji CRAN (kompleksowa sieć R archiwum) języka R. Obecnie używana wersja to CRAN 3.5.1.

## <a name="supported-r-packages"></a>Obsługiwane pakiety języka R

Środowisko języka R jest wstępnie instalowane z ponad 100 pakietów. Aby uzyskać pełną listę, zapoznaj się z sekcją [wstępnie zainstalowanych pakietów języka R](#pre-installed-r-packages).

Możesz również dodać następujący kod do dowolnego modułu **skryptu języka R** i zobaczyć zainstalowane pakiety.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>Instalowanie pakietów języka R
Aby zainstalować dodatkowe pakiety języka R, użyj `install.packages()` metody. Pakiety są instalowane dla każdego modułu **wykonywania skryptu języka r** i nie są współużytkowane przez inne moduły **wykonywania skryptów języka r** .

> [!NOTE]
> Określ repozytorium CRAN podczas instalowania pakietów, takich jak`install.packages("zoo",repos = "http://cran.us.r-project.org")`

Ten przykład pokazuje, jak zainstalować system ZOO:
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
  > Sprawdź, czy pakiet już istnieje przed zainstalowaniem go, aby uniknąć powtarzania instalacji. Podobnie `  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")` jak w przypadku powyżej przykładowego kodu. Powtórzenie instalacji może spowodować przekroczenie limitu czasu żądania usługi sieci Web.     

## <a name="upload-files"></a>Przekazywanie plików
**Skrypt Execute języka r** obsługuje przekazywanie plików przy użyciu Azure Machine Learning języka r SDK.

Poniższy przykład pokazuje, jak przekazać plik obrazu w **skrypcie Execute języka R**:
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
> ![Przekazany — obraz](media/module/upload-image-in-r-script.png)

## <a name="how-to-configure-execute-r-script"></a>Jak skonfigurować skrypt wykonywania skryptu języka R

Moduł **wykonywania skryptu języka R** zawiera przykładowy kod, którego można użyć jako punktu wyjścia. Aby skonfigurować moduł **wykonywania skryptu języka R** , podaj zestaw wejść i kod do wykonania.

![R-module](media/module/execute-r-script.png)

Zestawy danych przechowywane w projektancie są automatycznie konwertowane na ramkę z danymi języka R po załadowaniu tego modułu.

1.  Dodaj moduł **wykonywania skryptu języka R** do potoku.

  

1. Połącz wszystkie dane wejściowe, które są potrzebne przez skrypt. Wejścia są opcjonalne i mogą zawierać dane oraz dodatkowy kod R.

    * **Pozycję DataSet1**: odwołanie do pierwszego danych wejściowych `dataframe1`jako. Wejściowy zestaw danych musi być sformatowany jako CSV, TSV, ARFF lub połączony z zestawem danych Azure Machine Learning.

    * **Dataset2**: odwołuje się do drugiego `dataframe2`danych wejściowych jako. Ten zestaw danych musi być również sformatowany jako plik CSV, TSV, ARFF lub jako zestaw danych Azure Machine Learning.

    * **Pakiet skryptu**: trzecia wejściowa akceptuje pliki zip. Spakowany plik może zawierać wiele plików i wiele typów plików.

1. W polu tekstowym **skrypt języka r** wpisz lub wklej prawidłowy skrypt języka r.

    > [!NOTE]
    > Należy zachować ostrożność podczas pisania skryptu i upewnić się, że nie występuje błąd składniowy, na przykład użycie niezadeklarowanej zmiennej lub zaimportowanego modułu lub funkcji. Zwróć również uwagę na wstępnie zainstalowaną listę pakietów na końcu tego dokumentu. Aby użyć pakietów, które nie są wymienione na liście, należy je zainstalować w skrypcie, np.`install.packages("zoo",repos = "http://cran.us.r-project.org")`
    
    > [!NOTE]
    > Funkcje zależne od biblioteki X11, takie jak "View", nie są obsługiwane, ponieważ biblioteka X11 nie jest wstępnie zainstalowana.
    
    Aby ułatwić rozpoczęcie pracy, pole tekstowe **skryptu języka R** jest wstępnie wypełnione z przykładowym kodem, który można edytować lub zamienić.
    
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

 * Skrypt musi zawierać funkcję o nazwie `azureml_main`, która jest punktem wejścia dla tego modułu.

 * Funkcja punktu wejścia musi mieć dwa argumenty wejściowe: `Param<dataframe1>` i `Param<dataframe2>`, nawet jeśli te dwa argumenty nie są używane w funkcji.

   > [!NOTE]
    > Dane przesłane do modułu **wykonywania skryptu języka R** są przywoływane `dataframe1` jako `dataframe2`i, które różnią się od programu Azure Machine Learning Designer (odwołują `dataset2`się do projektanta jako `dataset1`). Upewnij się, że dane wejściowe są poprawnie referneced w skrypcie.  
 
    > [!NOTE]
    >  Istniejący kod R może potrzebować drobnych zmian do uruchomienia w potoku projektanta. Na przykład dane wejściowe, które podano w formacie CSV, powinny być jawnie konwertowane na zestaw danych, zanim będzie można używać go w kodzie. Typy danych i kolumn używane w języku R również różnią się w zależności od typu danych i kolumn używanych w projektancie.

1.  **Losowy inicjator**: wpisz wartość, która ma być używana w środowisku języka R jako wartość losowego inicjatora. Ten parametr jest odpowiednikiem wywołania `set.seed(value)` w kodzie R.  

1. Prześlij potok.  

## <a name="results"></a>Wyniki

Moduły **Execute skryptu języka R** mogą zwracać wiele danych wyjściowych, ale muszą one być podane jako ramki z danymi języka r. Ramki danych są automatycznie konwertowane na zestawy DataSet w projektancie w celu zapewnienia zgodności z innymi modułami.

Standardowe komunikaty i błędy w języku R są zwracane do dziennika modułu.

Jeśli chcesz wydrukować wyniki w skrypcie języka R, możesz znaleźć wyniki wydrukowane w **70_driver_log** na karcie dane **wyjściowe i dzienniki** na prawym panelu modułu.

## <a name="sample-scripts"></a>Przykładowe skrypty

Istnieje wiele sposobów na rozbudowanie potoku za pomocą niestandardowego skryptu języka R.  Ta sekcja zawiera przykładowy kod dla typowych zadań.


### <a name="add-r-script-as-an-input"></a>Dodaj skrypt języka R jako dane wejściowe

Moduł **wykonywania skryptu języka r** obsługuje dowolne pliki skryptów języka r jako dane wejściowe. W tym celu należy przekazać je do obszaru roboczego jako część pliku ZIP.

1. Aby przekazać plik ZIP zawierający kod R do obszaru roboczego, przejdź do strony zasobów **zestawy** danych, kliknij pozycję **Utwórz zestaw danych**, a następnie wybierz pozycję **z pliku lokalnego** i opcję **Typ zestawu danych** .  

1. Sprawdź, czy spakowany plik jest dostępny na liście **Moje zestawy danych** w obszarze Kategorie **zestawy danych** w lewym drzewie modułu.

1.  Połącz zestaw danych z portem wejściowym **pakietu skryptu** .

1. Wszystkie pliki znajdujące się w pliku ZIP są dostępne podczas wykonywania potoku. 

    Jeśli plik pakietu skryptu zawierał strukturę katalogów, struktura jest zachowywana. Należy jednak zmienić kod, aby dołączyć do niego pakiet Directory **./Script** .

### <a name="process-data"></a>Przetwarzanie danych

Poniższy przykład pokazuje, jak skalować i znormalizować dane wejściowe:

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

### <a name="read-a-zip-file-as-input"></a>Odczytaj plik ZIP jako dane wejściowe

Ten przykład pokazuje, jak używać zestawu danych w pliku ZIP jako dane wejściowe do modułu **wykonywania skryptu języka R** .

1. Utwórz plik danych w formacie CSV, a następnie nadaj mu nazwę "plik. csv".
1. Utwórz plik ZIP i Dodaj plik CSV do archiwum.
1. Przekaż spakowany plik do obszaru roboczego Azure Machine Learning. 
1. Połącz zestaw danych z danymi wejściowymi z **ScriptBundleem** **wykonywania modułu skryptu języka R** .
1. Użycie poniższego kodu w celu odczytania danych CSV z pliku spakowanego.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Replikuj wiersze

Ten przykład pokazuje, jak replikować rekordy pozytywne w zestawie danych, aby zrównoważyć przykład:

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

### <a name="pass-r-objects-between-execute-r-script-modules"></a>Przekazywanie obiektów R między modułami wykonywania skryptu języka R

Można przekazać obiekty R między wystąpieniami modułu **wykonywania skryptu języka r** przy użyciu wewnętrznego mechanizmu serializacji. W tym przykładzie przyjęto założenie, że chcesz przenieść obiekt `A` R o nazwie między dwa moduły **wykonywania skryptu języka r** .

1. Dodaj do potoku pierwszy moduł **skryptu wykonania R** , a następnie wpisz następujący kod w polu tekstowym **skryptu języka r** , aby utworzyć serializowany obiekt `A` jako kolumnę w tabeli danych wyjściowych modułu:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    Jawna konwersja na typ Integer jest wykonywana, ponieważ funkcja serializacji wyprowadza dane w formacie R `Raw` , co nie jest obsługiwane przez projektanta.

1. Dodaj drugie wystąpienie modułu **wykonywania skryptu języka R** i połącz je z portem wyjściowym poprzedniego modułu.

1. Wpisz następujący kod w polu tekstowym **skryptu języka R** , aby wyodrębnić obiekt `A` z tabeli danych wejściowych. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Wstępnie zainstalowane pakiety języka R

Bieżąca lista wstępnie zainstalowanych pakietów języka R dostępnych do użycia:

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
| rozruch         | 1.3 — 22     | 
| broom        | 0.5.2      | 
| callr        | 3.2.0      | 
| caret        | 6.0 — 84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| class        | 7.3-15     | 
| interfejs wiersza polecenia          | 1.1.0      | 
| Program Cliper        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| codetools    | 0,2 – 16     | 
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
| foreign      | 0,8-71     | 
| systemu           | 1.3.1      | 
| gdata        | 2.18.0     | 
| typy ogólne     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0 – 18     | 
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
| lattice      | 0,20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| markdown     | 1          | 
| MASS         | 7.3 — 51.4   | 
| Matrix       | 1.2 – 17     | 
| methods      | 3.5.1      | 
| mgcv         | 1.8 — 28     | 
| mime         | 0.7        | 
| ModelMetrics | ppkt      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1 – 140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8 — 1.1 | 
| openssl      | 1.4        | 
| parallel     | 3.5.1      | 
| pillar       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| progress     | ppkt      | 
| iloczyn           | 1.3.0      | 
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
| survival     | 2.44 — 1.1   | 
| sys          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex      | 0.13       | 
| narzędzia        | 3.5.1      | 
| tseries      | 0.10-47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0,8        | 
| xml2         | 1.2.0      | 
| xts          | 0,11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoo          | 1.8-6      | 

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z [zestawem modułów dostępnych](module-reference.md) do Azure Machine Learning. 
