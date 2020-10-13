---
title: 'Samouczek: używanie języka R do tworzenia modelu uczenia maszynowego (wersja zapoznawcza)'
titleSuffix: Azure Machine Learning
description: W tym samouczku użyjesz Azure Machine Learning R SDK, aby utworzyć model regresji logistycznej, który przewiduje prawdopodobieństwo wystąpienia krytycznego poziomu się w przypadku awarii samochodu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.reviewer: sgilley
author: revodavid
ms.author: davidsmi
ms.date: 02/07/2020
ms.openlocfilehash: fe9b0e9d2e2e46c7c4d04549777a5c27df6bdf18
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940110"
---
# <a name="tutorial-use-r-to-create-a-machine-learning-model-preview"></a>Samouczek: używanie języka R do tworzenia modelu uczenia maszynowego (wersja zapoznawcza)


> [!IMPORTANT]
> Zestaw Azure Machine Learning R SDK jest obecnie w publicznej wersji zapoznawczej.
> Wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

W tym samouczku użyjesz Azure Machine Learning R SDK (wersja zapoznawcza), aby utworzyć model regresji logistycznej, który przewiduje prawdopodobieństwo wystąpienia krytycznej awarii samochodu. Zobaczysz, jak zasoby chmury Azure Machine Learning współpracują z językiem R, aby zapewnić skalowalne środowisko do szkolenia i wdrażania modelu.  

Ten samouczek obejmuje wykonanie następujących zadań:
> [!div class="checklist"]
> * Tworzenie obszaru roboczego usługi Azure Machine Learning
> * Otwórz RStudio z obszaru roboczego
> * Klonowanie https://github.com/Azure/azureml-sdk-for-r plików niezbędnych do uruchomienia tego samouczka w obszarze roboczym
> * Ładowanie danych i przygotowanie do szkolenia
> * Przekaż dane do magazynu danych, aby były dostępne do szkolenia zdalnego
> * Utwórz zasób obliczeniowy, aby zdalnie nauczyć model
> * Uczenie `caret` modelu do przewidywania prawdopodobieństwa krytycznego
> * Wdrażanie punktu końcowego przewidywania
> * Testowanie modelu przy użyciu języka R

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz bezpłatne konto. Wypróbuj [bezpłatną lub płatną wersję Azure Machine Learning](https://aka.ms/AMLFree) dzisiaj.


## <a name="create-a-workspace"></a>Tworzenie obszaru roboczego

Obszar roboczy Azure Machine Learning to podstawowe zasoby w chmurze, za pomocą których można eksperymentować, uczeniować i wdrażać modele uczenia maszynowego. Łączy ona Twoją subskrypcję i grupę zasobów platformy Azure z łatwym w użyciu obiektem w usłudze. 

Aby zarządzać zasobami platformy Azure, można utworzyć obszar roboczy za pośrednictwem Azure Portal konsoli internetowej. 

[!INCLUDE [aml-create-portal](../../includes/aml-create-in-portal.md)]

>[!IMPORTANT] 
> Zanotuj swój **obszar roboczy** i **subskrypcję**. Będą one potrzebne do utworzenia eksperymentu w odpowiednim miejscu. 


## <a name="open-rstudio"></a><a name="open"></a>Otwórz RStudio

W tym przykładzie używane jest wystąpienie obliczeniowe w obszarze roboczym na potrzeby instalacji bezpłatnej i wstępnie skonfigurowanego środowiska. Użyj [własnego środowiska](https://azure.github.io/azureml-sdk-for-r/articles/installation.html) , jeśli wolisz mieć kontrolę nad środowiskiem, pakietami i zależnościami na własnym komputerze.

Użyj RStudio w wystąpieniu obliczeniowym Azure ML, aby uruchomić ten samouczek.  

1. Wybierz pozycję **obliczenia** po lewej stronie.

1. Dodaj zasób obliczeniowy, jeśli jeszcze nie istnieje.

1. Po uruchomieniu obliczeń Użyj linku **RStudio** , aby otworzyć RStudio.


## <a name="clone-the-sample-vignettes"></a><a name="azure"></a>Klonowanie przykładowej vignettes 

Sklonuj https://github.com/Azure/azureml-sdk-for-r repozytorium GitHub, aby uzyskać kopię plików Vignette, które zostaną uruchomione w tym samouczku.

1. W programie RStudio przejdź do karty "Terminal" i CD do katalogu, w którym chcesz sklonować repozytorium.

1. Uruchom w terminalu "klonowanie git https://github.com/Azure/azureml-sdk-for-r ", aby sklonować repozytorium.

1. W RStudio przejdź do folderu *vignettes* w sklonowanym folderze *Azure-SDK-for-r* .  W obszarze *vignettes*wybierz *kolejno pozycje uczenie i wdrażanie-pierwszy-model. RMD* plik, aby znaleźć Vignette używany w tym samouczku. Dodatkowe pliki używane dla Vignette znajdują się w podfolderze *pociąg-and-Deploy-First-model* . Po otwarciu Vignette Ustaw katalog roboczy na lokalizację pliku za pośrednictwem **sesji > ustaw > katalog roboczy na lokalizację pliku źródłowego**. 

> [!Important]
> Pozostała część tego artykułu zawiera tę samą zawartość, która jest wyświetlana w  *modelu uczenie i wdrażanie-pierwszy-model. Plik RMD* . Jeśli masz doświadczenie z RMarkdown, możesz użyć kodu z tego pliku.  Można też skopiować/wkleić fragmenty kodu z tego miejsca lub z tego artykułu do skryptu języka R lub wiersza polecenia. 


## <a name="set-up-your-development-environment"></a>Konfigurowanie środowiska projektowego
Konfiguracja dla pracy programistycznej w tym samouczku obejmuje następujące działania:

* Instalowanie wymaganych pakietów
* Nawiązywanie połączenia z obszarem roboczym, dzięki czemu wystąpienie obliczeniowe może komunikować się z zasobami zdalnymi
* Utwórz eksperyment do śledzenia przebiegów
* Utwórz zdalne miejsce docelowe obliczeń do użycia na potrzeby szkolenia

### <a name="install-required-packages"></a>Instalowanie wymaganych pakietów
Wystąpienie obliczeniowe ma już najnowszą wersję zestawu R SDK z CRAN. Jeśli chcesz zainstalować wersję deweloperskią z usługi GitHub zamiast pobrać najnowsze poprawki błędów, uruchom następujące polecenie:
    
```R
remotes::install_github('https://github.com/Azure/azureml-sdk-for-r')
azuremlsdk::install_azureml()
```

> [!WARNING]
> W trakcie procesu instalacji, jeśli zostanie wyświetlony monit " `Would you like to install Miniconda? [Y/n]:` ", proszę odpowiedzieć na " `n` ", ponieważ wystąpienie obliczeniowe ma już zainstalowane Anaconda i instalacja Miniconda nie jest wymagana.

Teraz przejdź dalej i zaimportuj pakiet **azuremlsdk** .

```R
library(azuremlsdk)
```

Skrypty szkoleniowe i oceniające ( `accidents.R` i `accident_predict.R` ) mają pewne dodatkowe zależności. Jeśli planujesz uruchamiać te skrypty lokalnie, upewnij się, że masz również wymagane pakiety.

### <a name="load-your-workspace"></a>Załaduj obszar roboczy
Utwórz wystąpienie obiektu obszaru roboczego z istniejącego obszaru roboczego. Poniższy kod załaduje szczegóły obszaru roboczego z **config.js** pliku. Możesz również pobrać obszar roboczy przy użyciu [`get_workspace()`](https://azure.github.io/azureml-sdk-for-r/reference/get_workspace.html) .

```R
ws <- load_workspace_from_config()
```

### <a name="create-an-experiment"></a>Tworzenie eksperymentu
Eksperyment Azure ML śledzi grupowanie przebiegów, zazwyczaj z tego samego skryptu szkoleniowego. Utwórz eksperyment, aby śledzić przebiegi w celu uczenia modelu karetki na danych wypadków.

```R
experiment_name <- "accident-logreg"
exp <- experiment(ws, experiment_name)
```

### <a name="create-a-compute-target"></a>Tworzenie obiektu docelowego obliczeń
Za pomocą usługi zarządzanej Azure Machine Learning Compute (AmlCompute) analitycy danych mogą szkolić modele uczenia maszynowego w klastrach maszyn wirtualnych platformy Azure. Przykłady obejmują maszyny wirtualne z obsługą procesorów GPU. W tym samouczku utworzysz klaster AmlCompute z jednym węzłem jako środowisko szkoleniowe. Poniższy kod tworzy klaster obliczeniowy, jeśli jeszcze nie istnieje w obszarze roboczym.

Jeśli klaster obliczeniowy nie istnieje, może być konieczne odczekanie kilku minut.

```R
cluster_name <- "rcluster"
compute_target <- get_compute(ws, cluster_name = cluster_name)
if (is.null(compute_target)) {
  vm_size <- "STANDARD_D2_V2" 
  compute_target <- create_aml_compute(workspace = ws,
                                       cluster_name = cluster_name,
                                       vm_size = vm_size,
                                       max_nodes = 1)
}

wait_for_provisioning_completion(compute_target)
```

## <a name="prepare-data-for-training"></a>Przygotowywanie danych do szkolenia
W tym samouczku są stosowane dane z [administracji krajowej bezpieczeństwa ruchu](https://cdan.nhtsa.gov/tsftables/tsfar.htm) drogowego USA (z podziękowaniami dla [Mary C. Meyer i Tremika Finney](https://www.stat.colostate.edu/~meyer/airbags.htm)).
Ten zestaw danych zawiera dane z ponad 25 000 samochodów w Stanach Zjednoczonych ze zmiennymi, których można użyć do przewidywania prawdopodobieństwa krytyczne. Najpierw zaimportuj dane do języka R i Przekształć je w nową ramkę danych `accidents` na potrzeby analizy i wyeksportuj je do `Rdata` pliku.

```R
nassCDS <- read.csv("nassCDS.csv", 
                     colClasses=c("factor","numeric","factor",
                                  "factor","factor","numeric",
                                  "factor","numeric","numeric",
                                  "numeric","character","character",
                                  "numeric","numeric","character"))
accidents <- na.omit(nassCDS[,c("dead","dvcat","seatbelt","frontal","sex","ageOFocc","yearVeh","airbag","occRole")])
accidents$frontal <- factor(accidents$frontal, labels=c("notfrontal","frontal"))
accidents$occRole <- factor(accidents$occRole)
accidents$dvcat <- ordered(accidents$dvcat, 
                          levels=c("1-9km/h","10-24","25-39","40-54","55+"))

saveRDS(accidents, file="accidents.Rd")
```

### <a name="upload-data-to-the-datastore"></a>Przekaż dane do magazynu danych
Przekaż dane do chmury, aby uzyskać dostęp do zdalnego środowiska szkoleniowego. Każdy obszar roboczy Azure Machine Learning jest dostarczany z domyślnym magazynem danych, który przechowuje informacje o połączeniu do kontenera obiektów blob platformy Azure, który jest inicjowany w ramach konta magazynu dołączonego do obszaru roboczego. Poniższy kod przekaże dane wypadki utworzone powyżej do tego magazynu danych.

```R
ds <- get_default_datastore(ws)

target_path <- "accidentdata"
upload_files_to_datastore(ds,
                          list("./accidents.Rd"),
                          target_path = target_path,
                          overwrite = TRUE)
```


## <a name="train-a-model"></a>Szkolenie modelu

Na potrzeby tego samouczka Dopasuj model regresji logistycznej do przekazanych danych przy użyciu zdalnego klastra obliczeniowego. Aby przesłać zadanie, musisz:

* Przygotuj skrypt szkoleniowy
* Tworzenie narzędzia do szacowania
* Przesyłanie zadania

### <a name="prepare-the-training-script"></a>Przygotuj skrypt szkoleniowy
`accidents.R`W katalogu " *szkolenie i wdrażanie-pierwszy-model* " udostępniono skrypt szkoleniowy o nazwie. Zwróć uwagę na następujące szczegóły w **skrypcie szkoleniowym** , które zostały wykonane w celu wykorzystania Azure Machine Learning do szkolenia:

* Skrypt szkoleniowy przyjmuje argument `-d` , aby znaleźć katalog zawierający dane szkoleniowe. Po zdefiniowaniu i przesłaniu zadania później należy wskazać magazyn danych dla tego argumentu. Platforma Azure ML zainstaluje folder magazynu w klastrze zdalnym dla zadania szkoleniowego.
* Skrypt szkoleniowy rejestruje ostateczną dokładność jako metrykę do rekordu uruchomienia w usłudze Azure ML przy użyciu `log_metric_to_run()` . Zestaw SDK usługi Azure ML udostępnia zestaw interfejsów API rejestrowania do rejestrowania różnych metryk podczas przebiegów szkoleniowych. Te metryki są rejestrowane i utrwalane w rekordzie przebiegu eksperymentu. Dostęp do metryk można uzyskać w dowolnym momencie lub wyświetlić na stronie Szczegóły uruchamiania w programie [Studio](https://ml.azure.com). Zobacz [informacje](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-training-experimentation) dotyczące pełnego zestawu metod rejestrowania `log_*()` .
* Skrypt szkoleniowy zapisuje model w katalogu **o nazwie**Outputs. `./outputs`Folder otrzymuje specjalne traktowanie w usłudze Azure ml. Podczas szkolenia pliki zapisywane do `./outputs` są automatycznie przekazywane do rekordu uruchomienia przez usługę Azure ml i utrwalane jako artefakty. Zapisując przeszkolony model do `./outputs` , będziesz mieć dostęp do pliku modelu nawet po jego zakończeniu i nie masz już dostępu do zdalnego środowiska szkoleniowego.

### <a name="create-an-estimator"></a>Tworzenie narzędzia do szacowania

Usługa Azure ML szacowania hermetyzuje informacje o konfiguracji uruchamiania, które są zbędne do wykonania skryptu szkoleniowego na obiekcie docelowym obliczeń. Uruchomienia usługi Azure ML są uruchamiane jako zadania kontenerowe w określonym elemencie docelowym obliczeń. Domyślnie obraz platformy Docker utworzony dla zadania szkoleniowego obejmuje język R, zestaw SDK usługi Azure ML oraz zestaw powszechnie używanych pakietów języka R. Zapoznaj się z pełną listą pakietów domyślnych uwzględnionych w tym miejscu.

Aby utworzyć szacowania, zdefiniuj:

* Katalog zawierający skrypty, które są używane do uczenia ( `source_directory` ). Wszystkie pliki w tym katalogu są przekazywane do węzłów klastra w celu wykonania. Katalog musi zawierać Twój skrypt szkoleniowy i wymagane dodatkowe skrypty.
* Skrypt szkoleniowy, który zostanie wykonany ( `entry_script` ).
* Obiekt docelowy obliczeń ( `compute_target` ), w tym przypadku utworzony wcześniej klaster AmlCompute.
* Parametry wymagane ze skryptu szkoleniowego ( `script_params` ). Usługa Azure ML uruchomi skrypt szkoleniowy jako skrypt wiersza polecenia z `Rscript` . W tym samouczku określisz jeden argument do skryptu, punkt zamontowania katalogu danych, do którego możesz uzyskać dostęp `ds$path(target_path)` .
* Wszystkie zależności środowiska wymagane do uczenia się. Domyślny obraz platformy Docker skompilowany na potrzeby szkolenia zawiera już trzy pakiety ( `caret` , `e1071` i), które `optparse` są niezbędne w skrypcie szkoleniowym.  Nie musisz podawać dodatkowych informacji. Jeśli używasz pakietów języka R, które nie są uwzględnione domyślnie, użyj parametru szacowania, `cran_packages` Aby dodać dodatkowe pakiety Cran. Zobacz [`estimator()`](https://azure.github.io/azureml-sdk-for-r/reference/estimator.html) informacje dotyczące pełnego zestawu konfigurowalnych opcji.

```R
est <- estimator(source_directory = "train-and-deploy-first-model",
                 entry_script = "accidents.R",
                 script_params = list("--data_folder" = ds$path(target_path)),
                 compute_target = compute_target
                 )
```

### <a name="submit-the-job-on-the-remote-cluster"></a>Prześlij zadanie w klastrze zdalnym

Na koniec Prześlij zadanie do uruchomienia w klastrze. `submit_experiment()` zwraca obiekt Run, który następnie jest używany do interfejsu z przebiegiem. Łącznie pierwszy przebieg trwa **około 10 minut**. Jednak w przypadku późniejszego uruchomienia ten sam obraz platformy Docker jest ponownie używany, tak długo, jak zależności skryptu nie są zmieniane.  W takim przypadku obraz jest buforowany i czas uruchamiania kontenera jest znacznie szybszy.

```R
run <- submit_experiment(exp, est)
```

Szczegóły przebiegu można wyświetlić w przeglądarce RStudio. Kliknięcie linku "Widok sieci Web" spowoduje wyświetlenie Azure Machine Learning Studio, w którym można monitorować przebieg w interfejsie użytkownika.

```R
view_run_details(run)
```

Szkolenia modeli odbywają się w tle. Poczekaj na zakończenie uczenia modelu przed uruchomieniem dalszego kodu.

```R
wait_for_run_completion(run, show_output = TRUE)
```

Współpracownicy z dostępem do obszaru roboczego — mogą przesyłać wiele eksperymentów równolegle, a platforma Azure ML zajmie się planowaniem zadań w klastrze obliczeniowym. Można nawet skonfigurować klaster w taki sposób, aby automatycznie skalowany w górę w wielu węzłach, i skalować z powrotem, gdy w kolejce nie ma więcej zadań obliczeniowych. Ta konfiguracja stanowi ekonomiczne rozwiązanie dla zespołów do udostępniania zasobów obliczeniowych.

## <a name="retrieve-training-results"></a>Pobierz wyniki szkolenia
Gdy Twój model zakończy szkolenia, możesz uzyskać dostęp do artefaktów zadania, które zostały utrwalone w rekordzie uruchomienia, w tym zarejestrowane metryki i finalny, przeszkolony model.

### <a name="get-the-logged-metrics"></a>Pobieranie zarejestrowanych metryk
W skrypcie szkoleniowym `accidents.R` zarejestrowano metrykę z modelu: dokładność prognoz w danych szkoleniowych. Możesz wyświetlić metryki w [Studio](https://ml.azure.com)lub wyodrębnić je do sesji lokalnej jako listę R w następujący sposób:

```R
metrics <- get_run_metrics(run)
metrics
```

Jeśli uruchomiono wiele eksperymentów (np. przy użyciu różnych zmiennych, algorytmów lub parametrów), można użyć metryk z każdego przebiegu, aby porównać i wybrać model, który będzie używany w środowisku produkcyjnym.

### <a name="get-the-trained-model"></a>Uzyskaj przeszkolony model
Możesz pobrać szkolony model i przeglądać wyniki w lokalnej sesji języka R. Poniższy kod pobierze zawartość `./outputs` katalogu, który zawiera plik modelu.

```R
download_files_from_run(run, prefix="outputs/")
accident_model <- readRDS("outputs/model.rds")
summary(accident_model)
```

Zobaczysz pewne czynniki, które przyczyniają się do wzrostu szacowanego prawdopodobieństwa zgonu:

* wyższa szybkość wpływów 
* Sterownik męski
* starszy pasażer
* drogow

Zobaczysz mniejsze prawdopodobieństwa śmierci z:

* obecność toreb dla worków
* SEATBELTS obecności
* kolizja czołowa 

Rok produkcji pojazdu nie ma znaczącego skutku.

Przy użyciu tego modelu można tworzyć nowe przewidywania:

```R
newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=16,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

## predicted probability of death for these variables, as a percentage
as.numeric(predict(accident_model,newdata, type="response")*100)
```

## <a name="deploy-as-a-web-service"></a>Wdrażanie w postaci usługi internetowej

Dzięki modelowi można przewidzieć niebezpieczeństwo zgonu z kolizji. Użyj platformy Azure ML do wdrożenia modelu jako usługi predykcyjnej. W tym samouczku zostanie wdrożona usługa sieci Web w [Azure Container Instances](https://docs.microsoft.com/azure/container-instances/) (ACI).

### <a name="register-the-model"></a>Rejestrowanie modelu

Najpierw Zarejestruj pobrany model do obszaru roboczego za pomocą [`register_model()`](https://azure.github.io/azureml-sdk-for-r/reference/register_model.html) . Zarejestrowanym modelem może być dowolna Kolekcja plików, ale w tym przypadku obiekt modelu R jest wystarczający. Platforma Azure ML będzie używać zarejestrowanego modelu do wdrożenia.

```R
model <- register_model(ws, 
                        model_path = "outputs/model.rds", 
                        model_name = "accidents_model",
                        description = "Predict probablity of auto accident")
```

### <a name="define-the-inference-dependencies"></a>Zdefiniuj zależności wnioskowania
Aby utworzyć usługę sieci Web dla modelu, należy najpierw utworzyć skrypt oceniania ( `entry_script` ), skrypt języka R, który przyjmuje jako wartości zmiennych wejściowych (w formacie JSON) i wyprowadza prognozę z modelu. Na potrzeby tego samouczka Użyj dostarczonego pliku oceniania `accident_predict.R` . Skrypt oceniania musi zawierać `init()` metodę, która ładuje model i zwraca funkcję, która używa modelu do prognozowania na podstawie danych wejściowych. Zapoznaj się z [dokumentacją](https://azure.github.io/azureml-sdk-for-r/reference/inference_config.html#details) , aby uzyskać więcej szczegółów.

Następnie zdefiniuj **środowisko** Azure ml dla zależności pakietu skryptu. W środowisku należy określić pakiety języka R (z CRAN lub innych innych), które są potrzebne do uruchomienia skryptu. Możesz również podać wartości zmiennych środowiskowych, do których skrypt może się odwoływać, aby zmodyfikować jego zachowanie. Domyślnie platforma Azure ML kompiluje ten sam domyślny obraz platformy Docker używany z szacowania na potrzeby szkolenia. Ponieważ samouczek nie ma specjalnych wymagań, Utwórz środowisko bez atrybutów specjalnych.

```R
r_env <- r_environment(name = "basic_env")
```

Jeśli zamiast tego chcesz użyć własnego obrazu platformy Docker do wdrożenia, określ `custom_docker_image` parametr. Zobacz [`r_environment()`](https://azure.github.io/azureml-sdk-for-r/reference/r_environment.html) informacje dotyczące pełnego zestawu konfigurowalnych opcji definiowania środowiska.

Teraz masz wszystko, co musisz zrobić, aby utworzyć **konfigurację wnioskowania** do hermetyzacji skryptów oceniania i zależności środowiska.

```R
inference_config <- inference_config(
  entry_script = "accident_predict.R",
  source_directory = "train-and-deploy-first-model",
  environment = r_env)
```

### <a name="deploy-to-aci"></a>Wdrażanie w usłudze ACI
W tym samouczku zostanie wdrożona usługa do ACI. Ten kod Inicjuje obsługę jednego kontenera w celu reagowania na żądania przychodzące, które jest odpowiednie do testowania i obciążeń lekkich. Więcej [`aci_webservice_deployment_config()`](https://azure.github.io/azureml-sdk-for-r/reference/aci_webservice_deployment_config.html) konfigurowalnych opcji można znaleźć w temacie. (W przypadku wdrożeń na skalę produkcyjną można także [wdrożyć usługę Azure Kubernetes Service](https://azure.github.io/azureml-sdk-for-r/articles/deploy-to-aks.html)).

``` R
aci_config <- aci_webservice_deployment_config(cpu_cores = 1, memory_gb = 0.5)
```

Teraz można wdrożyć model jako usługę sieci Web. Wdrożenie **może potrwać kilka minut**. 

```R
aci_service <- deploy_model(ws, 
                            'accident-pred', 
                            list(model), 
                            inference_config, 
                            aci_config)

wait_for_deployment(aci_service, show_output = TRUE)
```

## <a name="test-the-deployed-service"></a>Testowanie wdrożonego modelu

Teraz, gdy model jest wdrażany jako usługa, można przetestować usługę z poziomu języka R przy użyciu [`invoke_webservice()`](https://azure.github.io/azureml-sdk-for-r/reference/invoke_webservice.html) .  Podaj nowy zestaw danych do przewidywania, Przekształć go w kod JSON i wyślij go do usługi.

```R
library(jsonlite)

newdata <- data.frame( # valid values shown below
 dvcat="10-24",        # "1-9km/h" "10-24"   "25-39"   "40-54"   "55+"  
 seatbelt="none",      # "none"   "belted"  
 frontal="frontal",    # "notfrontal" "frontal"
 sex="f",              # "f" "m"
 ageOFocc=22,          # age in years, 16-97
 yearVeh=2002,         # year of vehicle, 1955-2003
 airbag="none",        # "none"   "airbag"   
 occRole="pass"        # "driver" "pass"
 )

prob <- invoke_webservice(aci_service, toJSON(newdata))
prob
```

Możesz również uzyskać punkt końcowy HTTP usługi sieci Web, który akceptuje wywołania klienta REST. Ten punkt końcowy można udostępnić każdemu, kto chce przetestować usługę internetową lub zintegrować ją z aplikacją.

```R
aci_service$scoring_uri
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Usuń zasoby, gdy nie będą już potrzebne. Nie usuwaj zasobów, których planujesz nadal używać. 

Usuń usługę sieci Web:
```R
delete_webservice(aci_service)
```

Usuń zarejestrowany model:
```R
delete_model(model)
```

Usuń klaster obliczeniowy:
```R
delete_compute(compute)
```

### <a name="delete-everything"></a>Usuń wszystko

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Możesz też zachować grupę zasobów i usunąć jeden obszar roboczy. Wyświetl właściwości obszaru roboczego i wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

* Teraz, po ukończeniu pierwszego Azure Machine Learning eksperymentu w języku R, Dowiedz się więcej na temat [Azure Machine Learning SDK dla języka r](https://azure.github.io/azureml-sdk-for-r/index.html).

* Dowiedz się więcej o Azure Machine Learning z językiem R w przykładach w innych folderach *vignettes* .
