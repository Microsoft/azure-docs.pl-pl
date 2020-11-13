---
title: 'Wykonaj skrypt języka R: odwołanie do modułu'
titleSuffix: Azure Machine Learning
description: Dowiedz się, w jaki sposób używać modułu skryptu języka R w programie Azure Machine Learning Designer do uruchamiania niestandardowego kodu języka R.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/21/2020
ms.openlocfilehash: 1e71d3883b8dacefa9b501ee3a9a0533d5c7d515
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94592672"
---
# <a name="execute-r-script-module"></a>Wykonaj moduł skryptu języka R

W tym artykule opisano, jak używać modułu skryptu języka R do uruchamiania kodu języka R w potoku programu Azure Machine Learning Designer.

Za pomocą języka R można wykonywać zadania, które nie są obsługiwane przez istniejące moduły, takie jak: 
- Tworzenie niestandardowych transformacji danych
- Używanie własnych metryk do szacowania prognoz
- Tworzenie modeli przy użyciu algorytmów, które nie są zaimplementowane jako moduły autonomiczne w projektancie

## <a name="r-version-support"></a>Obsługa wersji języka R

Program Azure Machine Learning Designer używa dystrybucji CRAN (kompleksowa sieć R archiwum) języka R. Obecnie używana wersja to CRAN 3.5.1.

## <a name="supported-r-packages"></a>Obsługiwane pakiety języka R

Środowisko języka R jest preinstalowane z ponad 100 pakietami. Aby uzyskać pełną listę, zapoznaj się z sekcją [preinstalowane pakiety języka R](#preinstalled-r-packages).

Aby wyświetlić zainstalowane pakiety, można również dodać do dowolnego modułu skryptu języka R następujący kod.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```
> [!NOTE]
> Jeśli potok zawiera wiele modułów skryptu wykonywania języka R, które wymagają pakietów, które nie znajdują się na liście wstępnie zainstalowanych, zainstaluj pakiety w każdym module. 

## <a name="installing-r-packages"></a>Instalowanie pakietów języka R
Aby zainstalować dodatkowe pakiety języka R, użyj `install.packages()` metody. Pakiety są zainstalowane dla każdego modułu wykonywania skryptu języka R. Nie są one współużytkowane przez inne moduły wykonywania skryptów języka R.

> [!NOTE]
> Określ repozytorium CRAN podczas instalowania pakietów, takich jak `install.packages("zoo",repos = "http://cran.us.r-project.org")` .

Ten przykład pokazuje, jak zainstalować system ZOO:
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Note that functions dependent on the X11 library,
# such as "View," are not supported because the X11 library
# is not preinstalled.

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
 > Przed zainstalowaniem pakietu Sprawdź, czy już istnieje, aby nie powtarzać instalacji. Instalacja powtarzania może spowodować przekroczenie limitu czasu żądań usługi sieci Web.     

## <a name="uploading-files"></a>Przekazywanie plików
Moduł wykonywania skryptu języka R obsługuje przekazywanie plików przy użyciu zestawu Azure Machine Learning R SDK.

Poniższy przykład pokazuje, jak przekazać plik obrazu w skrypcie Execute języka R:
```R

# R version: 3.5.1
# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Note that functions dependent on the X11 library,
# such as "View," are not supported because the X11 library
# is not preinstalled.

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

Po zakończeniu przebiegu potoku można wyświetlić podgląd obrazu w prawym panelu modułu.

> [!div class="mx-imgBorder"]
> ![Podgląd przekazanego obrazu](media/module/upload-image-in-r-script.png)

## <a name="access-to-registered-dataset"></a>Dostęp do zarejestrowanego zestawu danych

Aby uzyskać dostęp do [zarejestrowanych zestawów danych](../how-to-create-register-datasets.md) w obszarze roboczym, można odwołać się do następującego przykładowego kodu:

```R
    azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  run = get_current_run()
  ws = run$experiment$workspace
  dataset = azureml$core$dataset$Dataset$get_by_name(ws, "YOUR DATASET NAME")
  dataframe2 <- dataset$to_pandas_dataframe()
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="how-to-configure-execute-r-script"></a>Jak skonfigurować skrypt wykonywania skryptu języka R

Moduł wykonywania skryptu języka R zawiera przykładowy kod jako punkt wyjścia.

![Diagram danych wejściowych dla modułu języka R](media/module/execute-r-script.png)

Zestawy danych przechowywane w projektancie są automatycznie konwertowane na ramkę z danymi języka R po załadowaniu tego modułu.

1.  Dodaj moduł **wykonywania skryptu języka R** do potoku.  

1. Połącz wszystkie dane wejściowe wymagane przez skrypt. Wejścia są opcjonalne i mogą zawierać dane oraz dodatkowy kod R.

    * **Pozycję DataSet1** : odwołanie do pierwszego danych wejściowych jako `dataframe1` . Wejściowy zestaw danych musi być sformatowany jako plik CSV, TSV lub ARFF. Można też połączyć zestaw danych Azure Machine Learning.

    * **Dataset2** : odwołuje się do drugiego danych wejściowych jako `dataframe2` . Ten zestaw danych musi być również sformatowany jako plik CSV, TSV lub ARFF lub jako zestaw danych Azure Machine Learning.

    * **Pakiet skryptu** : trzecie wejście akceptuje pliki. zip. Plik spakowany może zawierać wiele plików i wiele typów plików.

1. W polu tekstowym **skrypt języka r** wpisz lub wklej prawidłowy skrypt języka r.

    > [!NOTE]
    > Należy zachować ostrożność podczas pisania skryptu. Upewnij się, że nie ma błędów składniowych, takich jak użycie niezadeklarowanych zmiennych lub nieimportowanych modułów lub funkcji. Zanotuj dodatkową uwagę na listę wstępnie zainstalowanych pakietów na końcu tego artykułu. Aby użyć pakietów, które nie znajdują się na liście, zainstaluj je w skrypcie. Może to być na przykład `install.packages("zoo",repos = "http://cran.us.r-project.org")`.
    
    Aby ułatwić rozpoczęcie pracy, w polu tekstowym **skryptu języka R** zostanie wstępnie wypełniony przykładowy kod, który można edytować lub zamienić.
    
    ```R
    # R version: 3.5.1
    # The script MUST contain a function named azureml_main,
    # which is the entry point for this module.

    # Note that functions dependent on the X11 library,
    # such as "View," are not supported because the X11 library
    # is not preinstalled.
    
    # The entry point function MUST have two input arguments.
    # If the input port is not connected, the corresponding
    # dataframe argument will be null.
    #   Param<dataframe1>: a R DataFrame
    #   Param<dataframe2>: a R DataFrame
    azureml_main <- function(dataframe1, dataframe2){
    print("R script run.")

    # If a .zip file is connected to the third input port, it's
    # unzipped under "./Script Bundle". This directory is added
    # to sys.path.

    # Return datasets as a Named List
    return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

    Funkcja punktu wejścia musi mieć argumenty wejściowe `Param<dataframe1>` i `Param<dataframe2>` , nawet jeśli te argumenty nie są używane w funkcji.

    > [!NOTE]
    > Dane przesłane do modułu wykonywania skryptu języka R są przywoływane jako `dataframe1` i `dataframe2` , które różnią się od programu Azure Machine Learning Designer (odwołują się do projektanta jako `dataset1` `dataset2` ). Upewnij się, że dane wejściowe są prawidłowo przywoływane w skrypcie.  
 
    > [!NOTE]
    > Istniejący kod języka R może wymagać drobnych zmian do uruchomienia w potoku projektanta. Na przykład dane wejściowe, które podano w formacie CSV, powinny być jawnie konwertowane na zestaw danych, zanim będzie można używać go w kodzie. Typy danych i kolumn używane w języku R również różnią się w zależności od typu danych i kolumn używanych w projektancie.

1. Jeśli skrypt ma rozmiar większy niż 16 KB, użyj portu **pakietu skryptu** , aby uniknąć błędów, takich jak *CommandLine, przekracza limit 16597 znaków*. 
    
    1. Pakiet skryptu i innych zasobów niestandardowych należy powiązać z plikiem zip.
    1. Przekaż plik zip jako **zestaw danych pliku** do Studio. 
    1. Przeciągnij moduł DataSet z listy *DataSets* w lewym okienku modułu na stronie Tworzenie projektanta. 
    1. Połącz moduł DataSet z portem **pakietu** **wykonywania skryptu języka R** .
    
    Poniżej znajduje się przykładowy kod służący do użycia skryptu w pakiecie skryptu:

    ```R
    azureml_main <- function(dataframe1, dataframe2){
    # Source the custom R script: my_script.R
    source("./Script Bundle/my_script.R")

    # Use the function that defined in my_script.R
    dataframe1 <- my_func(dataframe1)

    sample <- readLines("./Script Bundle/my_sample.txt")
    return (list(dataset1=dataframe1, dataset2=data.frame("Sample"=sample)))
    }
    ```

1.  W przypadku **losowego inicjatora** wprowadź wartość, która ma być używana w środowisku języka R jako wartość losowego inicjatora. Ten parametr jest odpowiednikiem wywołania `set.seed(value)` w kodzie R.  

1. Prześlij potok.  

## <a name="results"></a>Wyniki

Moduły wykonywania skryptu języka R mogą zwracać wiele danych wyjściowych, ale muszą być one dostarczane jako ramki z danymi języka R. Projektant automatycznie konwertuje ramki danych na zestawy DataSet, aby zapewnić zgodność z innymi modułami.

Standardowe komunikaty i błędy w języku R są zwracane do dziennika modułu.

Jeśli chcesz wydrukować wyniki w skrypcie języka R, możesz znaleźć wyniki wydrukowane w **70_driver_log** na karcie dane **wyjściowe i dzienniki** w prawym panelu modułu.

## <a name="sample-scripts"></a>Przykładowe skrypty

Istnieje wiele sposobów na rozbudowanie potoku przy użyciu niestandardowych skryptów języka R. Ta sekcja zawiera przykładowy kod dla typowych zadań.


### <a name="add-an-r-script-as-an-input"></a>Dodaj skrypt języka R jako dane wejściowe

Moduł wykonywania skryptu języka R obsługuje dowolne pliki skryptów języka R jako dane wejściowe. Aby ich używać, należy przekazać je do obszaru roboczego w ramach pliku zip.

1. Aby przekazać plik. zip zawierający kod R do obszaru roboczego, przejdź do strony zasobów **zestawy danych** . Wybierz pozycję **Utwórz zestaw danych** , a następnie wybierz pozycję **z pliku lokalnego** i opcję **Typ zestawu danych** .  

1. Sprawdź, czy spakowany plik jest wyświetlany w obszarze **Moje zestawy danych** w kategorii **zestawy danych** w lewym drzewie modułu.

1.  Połącz zestaw danych z portem wejściowym **pakietu skryptu** .

1. Wszystkie pliki w pliku zip są dostępne podczas wykonywania potoku. 

    Jeśli plik pakietu skryptu zawierał strukturę katalogów, struktura jest zachowywana. Ale należy zmienić kod, aby dołączyć do niego **pakiet Directory./Script** .

### <a name="process-data"></a>Przetwarzanie danych

Poniższy przykład pokazuje, jak skalować i znormalizować dane wejściowe:

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main,
# which is the entry point for this module.

# Note that functions dependent on the X11 library,
# such as "View," are not supported because the X11 library
# is not preinstalled.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a .zip file is connected to the third input port, it's
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # Find the maximum and minimum values of the width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # Calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # Apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>Odczytaj plik. zip jako dane wejściowe

Ten przykład pokazuje, jak używać zestawu danych w pliku zip jako dane wejściowe w module wykonywania skryptu języka R.

1. Utwórz plik danych w formacie CSV, a następnie nadaj mu nazwę **mydatafile.csv**.
1. Utwórz plik zip i Dodaj plik CSV do archiwum.
1. Przekaż spakowany plik do obszaru roboczego Azure Machine Learning. 
1. Połącz zestaw danych z danymi wejściowymi z **ScriptBundleem** **wykonywania modułu skryptu języka R** .
1. Użyj poniższego kodu, aby odczytać dane CSV z pliku spakowanego.

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

Można przekazać obiekty R między wystąpieniami modułu wykonywania skryptu języka R przy użyciu wewnętrznego mechanizmu serializacji. W tym przykładzie przyjęto założenie, że chcesz przenieść obiekt R o nazwie `A` między dwa moduły wykonywania skryptu języka r.

1. Dodaj moduł **uruchamiania pierwszego skryptu języka R** do potoku. Następnie wprowadź następujący kod w polu tekstowym **skryptu języka R** , aby utworzyć serializowany obiekt `A` jako kolumnę w tabeli danych wyjściowych modułu:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    Jawna konwersja na typ Integer jest wykonywana, ponieważ funkcja serializacji wyprowadza dane w formacie R `Raw` , który nie jest obsługiwany przez projektanta.

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

## <a name="preinstalled-r-packages"></a>Wstępnie zainstalowane pakiety języka R

Obecnie dostępne są następujące wstępnie zainstalowane pakiety języka R:

| Pakiet      | Wersja    | 
|--------------|------------| 
| askpass      | 1,1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| base         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| boot         | 1.3 — 22     | 
| broom        | 0.5.2      | 
| callr        | 3.2.0      | 
| daszek        | 6.0 — 84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| class        | 7.3-15     | 
| cli          | 1.1.0      | 
| Program Cliper        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| codetools    | 0,2 – 16     | 
| colorspace   | 1.4-1      | 
| compiler     | 3.5.1      | 
| crayon       | 1.3.4      | 
| curl         | 3.3        | 
| data.table   | 1.12.2     | 
| zestawy danych     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| evaluate     | 0,14       | 
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
| Macierz       | 1.2 – 17     | 
| methods      | 3.5.1      | 
| mgcv         | 1.8 — 28     | 
| mime         | 0,7        | 
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
| rstudioapi   | 0,1        | 
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
| tools        | 3.5.1      | 
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