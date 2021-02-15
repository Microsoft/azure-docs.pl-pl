---
title: Poznaj system Linux
titleSuffix: Azure Data Science Virtual Machine
description: Dowiedz się, jak wykonać kilka typowych zadań związanych z nauką o danych przy użyciu Data Science Virtual Machine systemu Linux.
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 09/17/2020
ms.openlocfilehash: 42136d0d58dbc318aab0e111fcef46f80751ca88
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100517675"
---
# <a name="data-science-with-an-ubuntu-data-science-virtual-machine-in-azure"></a>Nauka danych przy użyciu Data Science Virtual Machine Ubuntu na platformie Azure

W tym instruktażu pokazano, jak wykonać kilka typowych zadań naukowych dotyczących danych za pomocą Ubuntu Data Science Virtual Machine (DSVM). Ubuntu DSVM to obraz maszyny wirtualnej dostępny na platformie Azure, który jest preinstalowany z kolekcją narzędzi często używanych do analizy danych i uczenia maszynowego. Najważniejsze składniki oprogramowania są wyszczególnione w [Ubuntu Data Science Virtual Machine](./dsvm-ubuntu-intro.md). Obraz DSVM ułatwia rozpoczęcie pracy z analizą danych w kilka minut, bez konieczności instalowania i konfigurowania poszczególnych narzędzi osobno. Można łatwo skalować w górę DSVM, jeśli jest to konieczne, i można go zatrzymać, gdy nie jest używany. Zasób DSVM jest elastyczny i ekonomiczny.

W tym instruktażu analizujemy zestaw danych [spambase](https://archive.ics.uci.edu/ml/datasets/spambase) . Spambase to zbiór wiadomości e-mail oznaczonych jako spam lub Ham (bez spamu). Spambase zawiera również pewne dane statystyczne dotyczące zawartości wiadomości e-mail. Więcej informacji na temat statystyk znajduje się w dalszej części przewodnika.

## <a name="prerequisites"></a>Wymagania wstępne

Przed użyciem DSVM systemu Linux należy spełnić następujące wymagania wstępne:

* **Subskrypcja platformy Azure**. Aby uzyskać subskrypcję platformy Azure, zobacz artykuł [Tworzenie bezpłatnego konta platformy Azure już dzisiaj](https://azure.microsoft.com/free/).

* [**Ubuntu Data Science Virtual Machine**](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804). Aby uzyskać informacje o aprowizacji maszyny wirtualnej, zobacz temat [udostępnianie Ubuntu Data Science Virtual Machine](./release-notes.md).
* [**X2go**](https://wiki.x2go.org/doku.php) zainstalowane na komputerze z otwartą sesją pulpit Xfce. Aby uzyskać więcej informacji, zobacz [Instalowanie i Konfigurowanie klienta x2go](dsvm-ubuntu-intro.md#x2go).
* Aby usprawnić przewijanie w przeglądarce sieci Web w programie DSVM, przełącz `gfx.xrender.enabled` flagę w `about:config` . [Dowiedz się więcej](https://www.reddit.com/r/firefox/comments/4nfmvp/ff_47_unbearable_slow_over_remote_x11/). Należy również rozważyć `mousewheel.enable_pixel_scrolling` ustawienie `False` . [Dowiedz się więcej](https://support.mozilla.org/questions/981140).

## <a name="download-the-spambase-dataset"></a>Pobierz zestaw danych spambase

[Spambase](https://archive.ics.uci.edu/ml/datasets/spambase) DataSet to stosunkowo mały zestaw danych, który zawiera 4 601 przykładów. Zestaw danych jest wygodnym rozmiarem do prezentowania niektórych kluczowych funkcji DSVM, ponieważ utrzymuje wymagania dotyczące zasobów.

> [!NOTE]
> Ten Instruktaż został utworzony przy użyciu D2 v2-size Linux DSVM (wersja Ubuntu 18,04). Aby wykonać procedury, które są przedstawione w tym instruktażu, można użyć DSVM tego rozmiaru.

Jeśli potrzebujesz więcej miejsca do magazynowania, możesz utworzyć dodatkowe dyski i dołączyć je do DSVM. Na dyskach jest używany trwały Magazyn Azure, więc ich dane są zachowywane nawet wtedy, gdy serwer jest ponownie inicjowany z powodu zmiany rozmiarów lub wyłączania. Aby dodać dysk i dołączyć go do DSVM, wykonaj kroki opisane w temacie [Dodawanie dysku do maszyny wirtualnej z systemem Linux](../../virtual-machines/linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Procedura dodawania dysku przy użyciu interfejsu wiersza polecenia platformy Azure, który jest już zainstalowany w DSVM. Możesz wykonać czynności całkowicie z DSVM samego siebie. Kolejną opcją zwiększenia magazynu jest użycie [Azure Files](../../storage/files/storage-how-to-use-files-linux.md).

Aby pobrać dane, Otwórz okno terminalu, a następnie uruchom następujące polecenie:

```bash
wget --no-check-certificate https://archive.ics.uci.edu/ml/machine-learning-databases/spambase/spambase.data
```

Pobrany plik nie ma wiersza nagłówka. Utwórzmy kolejny plik, który ma nagłówek. Uruchom to polecenie, aby utworzyć plik z odpowiednimi nagłówkami:

```bash
echo 'word_freq_make, word_freq_address, word_freq_all, word_freq_3d,word_freq_our, word_freq_over, word_freq_remove, word_freq_internet,word_freq_order, word_freq_mail, word_freq_receive, word_freq_will,word_freq_people, word_freq_report, word_freq_addresses, word_freq_free,word_freq_business, word_freq_email, word_freq_you, word_freq_credit,word_freq_your, word_freq_font, word_freq_000, word_freq_money,word_freq_hp, word_freq_hpl, word_freq_george, word_freq_650, word_freq_lab,word_freq_labs, word_freq_telnet, word_freq_857, word_freq_data,word_freq_415, word_freq_85, word_freq_technology, word_freq_1999,word_freq_parts, word_freq_pm, word_freq_direct, word_freq_cs, word_freq_meeting,word_freq_original, word_freq_project, word_freq_re, word_freq_edu,word_freq_table, word_freq_conference, char_freq_semicolon, char_freq_leftParen,char_freq_leftBracket, char_freq_exclamation, char_freq_dollar, char_freq_pound, capital_run_length_average,capital_run_length_longest, capital_run_length_total, spam' > headers
```

Następnie połącz dwa pliki razem:

```bash
cat spambase.data >> headers
mv headers spambaseHeaders.data
```

Zestaw danych zawiera kilka typów statystyk dla każdej wiadomości e-mail:

* Kolumny, takie jak **Word \_ FREQ \_ _Word_** , wskazują procent słów w wiadomości e-mail pasującej do *wyrazu*. Jeśli na przykład **słowo \_ FREQ \_** ma wartość **1**, *nastąpiło* 1% wszystkich wyrazów w wiadomości e-mail.
* Kolumny, takie jak **char \_ FREQ \_ _char_** wskazują procent wszystkich znaków w wiadomości e-mail, które są *znakami*.
* **\_ Długość uruchomienia \_ w \_ Wielkiej** litery jest najdłuższym długością sekwencji wielkich liter.
* **\_ \_ \_ średnia długość uruchomienia kapitału** to średnia długość wszystkich sekwencji wielkich liter.
* **\_ \_ \_ całkowita długość uruchomienia kapitału** to łączna długość wszystkich sekwencji wielkich liter.
* **spam** wskazuje, czy wiadomość e-mail została uznana za spam, czy nie (1 = spam, 0 = nie spam).

## <a name="explore-the-dataset-by-using-r-open"></a>Eksplorowanie zestawu danych przy użyciu języka R Open

Przeanalizujmy dane i wykonują podstawowe Uczenie maszynowe przy użyciu języka R. DSVM jest dostarczany ze wstępnie zainstalowanym [programem Microsoft R Open](https://mran.revolutionanalytics.com/open/) . Wielowątkowe biblioteki matematyczne w wstępnie zainstalowanej wersji języka R oferują lepszą wydajność niż wersje jednowątkowe. Otwarty język R umożliwia także odtwarzalność za pomocą migawki repozytorium pakietu CRAN.

Aby uzyskać kopie przykładów kodu, które są używane w tym instruktażu, użyj narzędzia Git, aby sklonować repozytorium Azure-Machine-Learning-Data-nauka. Narzędzie git jest preinstalowane na DSVM. W wierszu polecenia usługi git Uruchom polecenie:

```bash
git clone https://github.com/Azure/Azure-MachineLearning-DataScience.git
```

Otwórz okno terminalu i uruchom nową sesję języka R w konsoli języka R Interactive. Można również użyć RStudio, który jest preinstalowany na DSVM.

Aby zaimportować dane i skonfigurować środowisko:

```R
data <- read.csv("spambaseHeaders.data")
set.seed(123)
```

Aby wyświetlić statystyki podsumowania poszczególnych kolumn:

```R
summary(data)
```

Aby uzyskać inny widok danych:

```R
str(data)
```

Ten widok przedstawia typ każdej zmiennej i kilka pierwszych wartości w zestawie danych.

Kolumna **spamu** została odczytana jako liczba całkowita, ale w rzeczywistości jest to zmienna kategorii (lub współczynnik). Aby ustawić swój typ:

```R
data$spam <- as.factor(data$spam)
```

Aby przeprowadzić analizę naukową, Użyj pakietu [ggplot2](https://ggplot2.tidyverse.org/) , popularnej biblioteki grafów dla języka R, który jest wstępnie zainstalowany w DSVM. Na podstawie danych podsumowania wyświetlanych wcześniej podsumowanie zawiera podsumowanie dotyczące częstotliwości znaku wykrzyknika. Tutaj Umieść te częstotliwości, uruchamiając następujące polecenia:

```R
library(ggplot2)
ggplot(data) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Ponieważ pasek zero jest skośny, można go wyeliminować:

```R
email_with_exclamation = data[data$char_freq_exclamation > 0, ]
ggplot(email_with_exclamation) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Istnieje nieprosta gęstość powyżej 1, która wygląda interesująca. Przyjrzyjmy się tylko tym danym:

```R
ggplot(data[data$char_freq_exclamation > 1, ]) + geom_histogram(aes(x=char_freq_exclamation), binwidth=0.25)
```

Następnie podzielenie go przez spam a Ham:

```R
ggplot(data[data$char_freq_exclamation > 1, ], aes(x=char_freq_exclamation)) +
geom_density(lty=3) +
geom_density(aes(fill=spam, colour=spam), alpha=0.55) +
xlab("spam") +
ggtitle("Distribution of spam \nby frequency of !") +
labs(fill="spam", y="Density")
```

Te przykłady powinny ułatwić tworzenie podobnych wykresów i Eksplorowanie danych w innych kolumnach.

## <a name="train-and-test-a-machine-learning-model"></a>Uczenie i testowanie modelu uczenia maszynowego

Przekażmy kilka modeli uczenia maszynowego, aby sklasyfikować wiadomości e-mail w zestawie danych jako zawierające spam lub Ham. W tej sekcji pouczymy Model drzewa decyzyjnego i losowy model lasów. Następnie przetestujemy dokładność prognoz.

> [!NOTE]
> Pakiet *rpart* (drzewa partycjonowania i regresji) użyty w poniższym kodzie jest już zainstalowany na DSVM.

Najpierw Podziel zestaw danych na zestawy szkoleniowe i zestawy testów:

```R
rnd <- runif(dim(data)[1])
trainSet = subset(data, rnd <= 0.7)
testSet = subset(data, rnd > 0.7)
```

Następnie utwórz drzewo decyzyjne służące do klasyfikowania wiadomości e-mail:

```R
require(rpart)
model.rpart <- rpart(spam ~ ., method = "class", data = trainSet)
plot(model.rpart)
text(model.rpart)
```

Oto wynik:

![Diagram tworzonego drzewa decyzyjnego](./media/linux-dsvm-walkthrough/decision-tree.png)

Aby określić, jak dobrze działa w zestawie szkoleniowym, należy użyć następującego kodu:

```R
trainSetPred <- predict(model.rpart, newdata = trainSet, type = "class")
t <- table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

Aby określić, jak dobrze działa w zestawie testów:

```R
testSetPred <- predict(model.rpart, newdata = testSet, type = "class")
t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

Wypróbujmy również losowy model lasów. Lasy losowe przeszkolą wiele drzew decyzyjnych i wyprowadzają klasę, która jest trybem klasyfikacji ze wszystkich poszczególnych drzew decyzyjnych. Zapewniają one bardziej zaawansowane podejście do uczenia maszynowego, ponieważ są one poprawne dla tendencji modelu drzewa decyzyjnego, aby overfit zestaw danych szkoleniowych.

```R
require(randomForest)
trainVars <- setdiff(colnames(data), 'spam')
model.rf <- randomForest(x=trainSet[, trainVars], y=trainSet$spam)

trainSetPred <- predict(model.rf, newdata = trainSet[, trainVars], type = "class")
table(`Actual Class` = trainSet$spam, `Predicted Class` = trainSetPred)

testSetPred <- predict(model.rf, newdata = testSet[, trainVars], type = "class")
t <- table(`Actual Class` = testSet$spam, `Predicted Class` = testSetPred)
accuracy <- sum(diag(t))/sum(t)
accuracy
```

<a name="deep-learning"></a>

## <a name="deep-learning-tutorials-and-walkthroughs"></a>Samouczki i przewodniki dotyczące uczenia głębokiego

Oprócz przykładów opartych na architekturze udostępnia się również zestaw kompleksowych przewodników. Te instruktaże ułatwiają szybkie rozpoczęcie tworzenia aplikacji do uczenia głębokiego w domenach, takich jak zrozumienie obrazów i tekstu/języka.

- [Uruchamianie sieci neuronowych w różnych strukturach](https://github.com/ilkarman/DeepLearningFrameworks): kompleksowy przewodnik, który pokazuje, jak migrować kod z jednej struktury do innej. Pokazano również, jak porównać model i wydajność środowiska uruchomieniowego w różnych strukturach. 

- [Przewodnik dotyczący tworzenia kompleksowych rozwiązań w celu wykrywania produktów w obrazach](https://github.com/Azure/cortana-intelligence-product-detection-from-images): wykrywanie obrazów to technika, która umożliwia lokalizowanie i klasyfikowanie obiektów w obrazach. Technologia ta pozwala uzyskać ogromne korzyści w wielu domenach biznesowych w czasie rzeczywistym. Na przykład Detaliści mogą użyć tej techniki, aby określić, który produkt został pobrany przez klienta z półki. Te informacje z kolei ułatwiają przechowywanie spisu produktów. 

- [Głębokie uczenie audio](/archive/blogs/machinelearning/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure): w tym samouczku pokazano, jak szkolić model uczenia głębokiego na potrzeby wykrywania zdarzeń audio w [zestawie danych dotyczących dźwięków miejskich](https://urbansounddataset.weebly.com/). Samouczek zawiera omówienie sposobu pracy z danymi audio.

- [Klasyfikacja dokumentów tekstowych](https://github.com/anargyri/lstm_han): w tym instruktażu przedstawiono sposób tworzenia i uczenia dwóch różnych architektur sieci neuronowych: hierarchicznej sieci i długiej ilości pamięci (LSTM). Te sieci neuronowych używają interfejsu API Keras do uczenia głębokiego do klasyfikowania dokumentów tekstowych. Keras to przód do trzech najpopularniejszych platform uczenia głębokiego: Microsoft Cognitive Toolkit, TensorFlow i Theano.

## <a name="other-tools"></a>Inne narzędzia

Pozostałe sekcje pokazują, jak używać niektórych narzędzi zainstalowanych w systemie Linux DSVM. Omawiamy następujące narzędzia:

* XGBoost
* Python
* JupyterHub
* Rattle
* PostgreSQL i SQuirreL SQL
* Azure Synapse Analytics (dawniej SQL DW)

### <a name="xgboost"></a>XGBoost

[XGBoost](https://xgboost.readthedocs.org/en/latest/) zapewnia szybką i dokładną implementację podwyższania poziomu drzewa.

```R
require(xgboost)
data <- read.csv("spambaseHeaders.data")
set.seed(123)

rnd <- runif(dim(data)[1])
trainSet = subset(data, rnd <= 0.7)
testSet = subset(data, rnd > 0.7)

bst <- xgboost(data = data.matrix(trainSet[,0:57]), label = trainSet$spam, nthread = 2, nrounds = 2, objective = "binary:logistic")

pred <- predict(bst, data.matrix(testSet[, 0:57]))
accuracy <- 1.0 - mean(as.numeric(pred > 0.5) != testSet$spam)
print(paste("test accuracy = ", accuracy))
```

XGBoost może również wywołać z języka Python lub wiersza polecenia.

### <a name="python"></a>Python

W przypadku programowania w języku Python dystrybucje Anaconda języka Python 3,5 i 2,7 są zainstalowane na DSVM.

> [!NOTE]
> Anaconda dystrybucji obejmuje [Conda](https://conda.pydata.org/docs/index.html). Za pomocą Conda można tworzyć niestandardowe środowiska języka Python, w których zainstalowano różne wersje lub pakiety.

Przeczytajmy część zestawu danych spambase i Klasyfikowanie wiadomości e-mail przy użyciu maszyn wektorów pomocy technicznej w Scikit — uczenie się:

```Python
import pandas
from sklearn import svm
data = pandas.read_csv("spambaseHeaders.data", sep = ',\s*')
X = data.ix[:, 0:57]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

Aby dokonać prognoz:

```Python
clf.predict(X.ix[0:20, :])
```

Aby dowiedzieć się, jak opublikować punkt końcowy Azure Machine Learning, Utwórzmy bardziej podstawowy model. Będziemy używać trzech zmiennych, które zostały użyte po opublikowaniu modelu R wcześniej:

```Python
X = data[["char_freq_dollar", "word_freq_remove", "word_freq_hp"]]
y = data.ix[:, 57]
clf = svm.SVC()
clf.fit(X, y)
```

### <a name="jupyterhub"></a>JupyterHub

Rozkład Anaconda w DSVM zawiera Jupyter Notebook, czyli środowisko Międzyplatformowe do udostępniania kodu Python, R lub Julia i analizy. Dostęp do Jupyter Notebook można uzyskać za pomocą JupyterHub. Użytkownik loguje się przy użyciu nazwy użytkownika i hasła lokalnego systemu Linux pod adresem https:// \<DSVM DNS name or IP address\> : 8000/. Wszystkie pliki konfiguracji dla JupyterHub znajdują się w/etc/jupyterhub.

> [!NOTE]
> Aby użyć Menedżera pakietów języka Python (za pośrednictwem `pip` polecenia) z Jupyter notebook w bieżącym jądrze, użyj tego polecenia w komórce kodu:
>
>   ```Python
>    import sys
>    ! {sys.executable} -m pip install numpy -y
>   ```
> 
> Aby użyć Instalatora Conda (za pośrednictwem `conda` polecenia) z Jupyter notebook w bieżącym jądrze, użyj tego polecenia w komórce kodu:
>
>   ```Python
>    import sys
>    ! {sys.prefix}/bin/conda install --yes --prefix {sys.prefix} numpy
>   ```

Kilka przykładowych notesów jest już zainstalowanych na DSVM:

* Przykładowe notesy w języku Python:
  * [IntroToJupyterPython. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroToJupyterPython.ipynb)
* Przykładowy Notes języka R:
  * [IntroTutorialinR](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Data-Science-Virtual-Machine/Samples/Notebooks/IntroTutorialinR.ipynb) 

> [!NOTE]
> Język Julia jest również dostępny w wierszu polecenia w systemie Linux DSVM.

### <a name="rattle"></a>Rattle

[Rattle](https://cran.r-project.org/web/packages/rattle/index.html) (*R* *A* nalityczny *t* OOL *t* o *L* zdobyć *E* asily) to graficzne narzędzie języka r do wyszukiwania danych. Rattle ma intuicyjny interfejs, który ułatwia ładowanie, eksplorowanie i Przekształcanie danych oraz tworzenie i ocenę modeli. [Rattle: interfejs GUI wyszukiwania danych dla języka R](https://journal.r-project.org/archive/2009-2/RJournal_2009-2_Williams.pdf) zawiera Przewodnik pokazujący funkcje Rattle.

Zainstaluj i uruchom program Rattle, uruchamiając następujące polecenia:

```R
if(!require("rattle")) install.packages("rattle")
require(rattle)
rattle()
```

> [!NOTE]
> Nie musisz instalować Rattle na DSVM. Jednak po otwarciu Rattle może zostać wyświetlony monit o zainstalowanie dodatkowych pakietów.

Rattle używa interfejsu opartego na kartach. Większość kart odpowiada krokom w [zespołowym procesie nauki danych](../team-data-science-process/index.yml), takim jak ładowanie danych lub Eksplorowanie danych. Proces analizy danych jest przepływem od lewej do prawej za pośrednictwem kart. Ostatnia karta zawiera dziennik poleceń języka R, które zostały uruchomione przez Rattle.

Aby załadować i skonfigurować zestaw danych:

1. Aby załadować plik, wybierz kartę **dane** .
1. Wybierz selektor obok **nazwy pliku**, a następnie wybierz pozycję **spambaseHeaders. Data**.
1. Do załadowania pliku. Wybierz pozycję **Wykonaj**. Powinno zostać wyświetlone podsumowanie każdej kolumny, w tym określony typ danych. bez względu na to, czy jest to wejście, obiekt docelowy czy inny typ zmiennej; oraz liczbę unikatowych wartości.
1. Rattle poprawnie zidentyfikowała kolumnę **spamu** jako element docelowy. Wybierz kolumnę **spam** , a następnie ustaw **docelowy typ danych** na **categoric**.

Aby eksplorować dane:

1. Wybierz kartę **Eksploruj** .
1. Aby wyświetlić informacje o typach zmiennych i niektórych statystykach podsumowujących, wybierz opcję wykonywanie **podsumowania**  >  .
1. Aby wyświetlić inne typy statystyk dla każdej zmiennej, wybierz inne opcje, takie jak **opisywanie** lub **podstawy**.

Karta **Eksplorowanie** umożliwia również generowanie szczegółowych wykresów. Aby wykreślić histogram dotyczący danych:

1. Wybierz opcję **dystrybucje**.
1. W obszarze **word_freq_remove** i **Word_freq_you** wybierz pozycję **histogram**.
1. Wybierz pozycję **Wykonaj**. W jednym oknie wykresu powinny być widoczne oba wykresy gęstości, w _których jest jasne, że słowo pojawia_ się częściej _niż w_ wiadomościach e-mail.

Wykresy **korelacji** są również interesujące. Aby utworzyć wykres:

1. W obszarze **Typ** wybierz pozycję **korelacja**.
1. Wybierz pozycję **Wykonaj**.
1. Rattle ostrzega o tym, że zaleca się użycie maksymalnie 40 zmiennych. Wybierz pozycję **tak** , aby wyświetlić wykres.

Istnieje kilka interesujących korelacji: _technologia_ jest silnie skorelowana z _HP_ i _Labs_, na przykład. Jest również silnie skorelowane z _650_ , ponieważ kod obszaru dawców zestawu danych to 650.

Wartości liczbowe korelacji między wyrazami są dostępne w oknie **Eksplorowanie** . Warto zwrócić uwagę na przykład, że _technologia_ jest niekorzystnie _skorelowane z i_ _pieniędzy_.

Rattle może przekształcić zestaw danych, aby obsłużyć niektóre typowe problemy. Na przykład może zmieniać skalę funkcji, określać brakujące wartości, obsługiwać elementy odstające i usuwać zmienne lub obserwacje z brakującymi danymi. Rattle może również identyfikować reguły kojarzenia między obserwacjami a zmiennymi. Te karty nie zostały omówione w tym instruktażu wprowadzającym.

Rattle może również uruchamiać analizę klastra. Wykluczmy niektóre funkcje, aby ułatwić odczytywanie danych wyjściowych. Na karcie **dane** wybierz opcję **Ignoruj** obok każdej ze zmiennych, z wyjątkiem tych 10 elementów:

* word_freq_hp
* word_freq_technology
* word_freq_george
* word_freq_remove
* word_freq_your
* word_freq_dollar
* word_freq_money
* capital_run_length_longest
* word_freq_business
* spam

Wróć do karty **klaster** . Wybierz pozycję **KMeans**, a następnie ustaw **liczbę klastrów** na **4**. Wybierz pozycję **Wykonaj**. Wyniki są wyświetlane w oknie dane wyjściowe. Jeden klaster ma wysoką częstotliwość _George_ i _HP_ i prawdopodobnie jest uzasadnioną służbową pocztą e-mail.

Aby utworzyć podstawowy model uczenia maszynowego w strukturze:

1. Wybierz kartę **model** ,
1. W polu **Typ** wybierz pozycję **drzewo**.
1. Wybierz polecenie **Execute (wykonaj** ), aby wyświetlić drzewo w formularzu tekstowym w oknie danych wyjściowych.
1. Wybierz przycisk **rysowania** , aby wyświetlić wersję graficzną. Drzewo decyzyjne wygląda podobnie do drzewa uzyskanego wcześniej przy użyciu rpart.

Przydatna funkcja Rattle może uruchamiać kilka metod uczenia maszynowego i szybko je oceniać. Oto kroki:

1. Dla opcji **Typ** zaznacz **wszystko**.
1. Wybierz pozycję **Wykonaj**.
1. Po zakończeniu działania Rattle można wybrać dowolną wartość **typu** , na przykład **SVM**, i wyświetlić wyniki.
1. Możesz również porównać wydajność modeli w zestawie walidacji, korzystając z karty **szacowanie** . Na przykład wybór **macierzy błędów** pokazuje macierz pomyłek, ogólny błąd i średnią błąd klasy dla każdego modelu w zestawie walidacji. Można również wykreślić krzywe ROC, uruchamiać analizę czułości i wykonywać inne typy ocen modeli.

Po zakończeniu tworzenia modeli wybierz kartę **Dziennik** , aby wyświetlić kod języka R, który został uruchomiony przez Rattle podczas sesji. Możesz wybrać przycisk **Eksportuj** , aby go zapisać.

> [!NOTE]
> Bieżąca wersja programu Rattle zawiera usterkę. Aby zmodyfikować skrypt lub użyć go w celu późniejszego powtórzenia kroków, należy wstawić **#** znak przed poleceniem *Eksportuj ten dziennik...* w tekście dziennika.

### <a name="postgresql-and-squirrel-sql"></a>PostgreSQL i SQuirreL SQL

DSVM jest dostarczany z zainstalowaną PostgreSQL. PostgreSQL to rozbudowana, relacyjna baza danych typu open source. W tej sekcji przedstawiono sposób ładowania zestawu danych spambase do PostgreSQL, a następnie wykonywania zapytania.

Przed załadowaniem danych należy zezwolić na uwierzytelnianie hasła z hosta lokalnego. W wierszu polecenia uruchom następujące polecenie:

```Bash
sudo gedit /var/lib/pgsql/data/pg_hba.conf
```

W dolnej części pliku konfiguracji są kilka wierszy, które zawierają szczegółowe informacje o dozwolonych połączeniach:

```
# "local" is only for Unix domain socket connections:
local   all             all                                     trust
# IPv4 local connections:
host    all             all             127.0.0.1/32            ident
# IPv6 local connections:
host    all             all             ::1/128                 ident
```

Zmień linię **połączenia lokalnego IPv4** , aby używać **algorytmu MD5** zamiast **ident**, więc można zalogować się przy użyciu nazwy użytkownika i hasła:

```
# IPv4 local connections:
host    all             all             127.0.0.1/32            md5
```

Następnie uruchom ponownie usługę PostgreSQL:

```Bash
sudo systemctl restart postgresql
```

Aby uruchomić *PSQL* (interaktywny Terminal dla PostgreSQL) jako wbudowanego użytkownika Postgres, uruchom następujące polecenie:

```Bash
sudo -u postgres psql
```

Utwórz nowe konto użytkownika przy użyciu nazwy użytkownika konta systemu Linux użytego do zalogowania się. Utwórz hasło:

```Bash
CREATE USER <username> WITH CREATEDB;
CREATE DATABASE <username>;
ALTER USER <username> password '<password>';
\quit
```

Zaloguj się do PSQL:

```Bash
psql
```

Zaimportuj dane do nowej bazy danych:

```SQL
CREATE DATABASE spam;
\c spam
CREATE TABLE data (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer);
\copy data FROM /home/<username>/spambase.data DELIMITER ',' CSV;
\quit
```

Teraz eksplorujmy dane i uruchamiasz niektóre zapytania za pomocą SQuirreL SQL, graficznego narzędzia, którego możesz użyć do współdziałania z bazami danych za pośrednictwem sterownika JDBC.

Aby rozpocząć, w menu **aplikacje** Otwórz SQuirreL SQL. Aby skonfigurować sterownik:

1. Wybierz pozycję  >  **sterowniki widoku** systemu Windows.
1. Kliknij prawym przyciskiem myszy pozycję **PostgreSQL** i wybierz pozycję **Modyfikuj sterownik**.
1. Wybierz pozycję **dodatkowa ścieżka klasy**  >  **Dodaj**.
1. W obszarze **Nazwa pliku** wprowadź **/usr/share/Java/jdbcdrivers/PostgreSQL-9.4.1208.jre6.jar**.
1. Wybierz pozycję **Otwórz**.
1. Wybierz pozycję **Wyświetl sterowniki**. W obszarze **Nazwa klasy** wybierz pozycję **org. PostgreSQL. Driver**, a następnie wybierz przycisk **OK**.

Aby skonfigurować połączenie z serwerem lokalnym:

1. Wybierz pozycję **Windows**  >  **View aliasy.**
1. Wybierz **+** przycisk, aby utworzyć nowy alias. Dla nowej nazwy aliasu wprowadź **bazę danych spamu**. 
1. W obszarze **Sterownik** wybierz pozycję **PostgreSQL**.
1. Ustaw adres URL **JDBC: PostgreSQL://localhost/spam**.
1. Wprowadź nazwę użytkownika i hasło.
1. Wybierz przycisk **OK**.
1. Aby otworzyć okno **połączenia** , kliknij dwukrotnie alias **bazy danych spamu** .
1. Wybierz pozycję **Połącz**.

Aby uruchomić niektóre zapytania:

1. Wybierz kartę **SQL** .
1. W polu zapytania w górnej części karty **SQL** wprowadź zapytanie podstawowe, takie jak `SELECT * from data;` .
1. Naciśnij klawisze CTRL + ENTER, aby uruchomić zapytanie. Domyślnie SQuirreL SQL zwraca pierwsze 100 wierszy z zapytania.

Istnieje wiele zapytań, które można uruchomić, aby eksplorować te dane. Na przykład, w jaki sposób *częstotliwość wyrazów* różni się między spamem a Ham?

```SQL
SELECT avg(word_freq_make), spam from data group by spam;
```

Lub jakie są cechy poczty e-mail, które często zawierają *3D*?

```SQL
SELECT * from data order by word_freq_3d desc;
```

Większość wiadomości e-mail, które mają duże wystąpienie *3W* , jest prawdopodobnie spamem. Te informacje mogą być przydatne podczas tworzenia modelu predykcyjnego do klasyfikowania wiadomości e-mail.

Jeśli chcesz przeprowadzić Uczenie maszynowe przy użyciu danych przechowywanych w bazie danych PostgreSQL, rozważ użycie [MADlib](https://madlib.incubator.apache.org/).

### <a name="azure-synapse-analytics-formerly-sql-dw"></a>Azure Synapse Analytics (dawniej SQL DW)

Azure Synapse Analytics to oparta na chmurze baza danych skalowalna w poziomie, która może przetwarzać ogromne ilości danych, zarówno relacyjnych, jak i nierelacyjnych. Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Synapse Analytics?](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

Aby nawiązać połączenie z magazynem danych i utworzyć tabelę, uruchom następujące polecenie w wierszu polecenia:

```Bash
sqlcmd -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -I
```

W wierszu polecenia sqlcmd Uruchom następujące polecenie:

```SQL
CREATE TABLE spam (word_freq_make real, word_freq_address real, word_freq_all real, word_freq_3d real,word_freq_our real, word_freq_over real, word_freq_remove real, word_freq_internet real,word_freq_order real, word_freq_mail real, word_freq_receive real, word_freq_will real,word_freq_people real, word_freq_report real, word_freq_addresses real, word_freq_free real,word_freq_business real, word_freq_email real, word_freq_you real, word_freq_credit real,word_freq_your real, word_freq_font real, word_freq_000 real, word_freq_money real,word_freq_hp real, word_freq_hpl real, word_freq_george real, word_freq_650 real, word_freq_lab real,word_freq_labs real, word_freq_telnet real, word_freq_857 real, word_freq_data real,word_freq_415 real, word_freq_85 real, word_freq_technology real, word_freq_1999 real,word_freq_parts real, word_freq_pm real, word_freq_direct real, word_freq_cs real, word_freq_meeting real,word_freq_original real, word_freq_project real, word_freq_re real, word_freq_edu real,word_freq_table real, word_freq_conference real, char_freq_semicolon real, char_freq_leftParen real,char_freq_leftBracket real, char_freq_exclamation real, char_freq_dollar real, char_freq_pound real, capital_run_length_average real, capital_run_length_longest real, capital_run_length_total real, spam integer) WITH (CLUSTERED COLUMNSTORE INDEX, DISTRIBUTION = ROUND_ROBIN);
GO
```

Skopiuj dane przy użyciu narzędzia bcp:

```bash
bcp spam in spambaseHeaders.data -q -c -t  ',' -S <server-name>.database.windows.net -d <database-name> -U <username> -P <password> -F 1 -r "\r\n"
```

> [!NOTE]
> Pobrany plik zawiera końce linii stylu systemu Windows. Narzędzie bcp oczekuje końców linii w stylu systemu UNIX. Użyj flagi-r, aby powiedzieć bcp.

Następnie wykonaj zapytanie przy użyciu narzędzia sqlcmd:

```sql
select top 10 spam, char_freq_dollar from spam;
GO
```

Możesz również wysyłać zapytania przy użyciu języka SQL SQuirreL. Wykonaj czynności podobne do PostgreSQL przy użyciu sterownika JDBC SQL Server. Sterownik JDBC znajduje się w folderze/usr/share/Java/jdbcdrivers/sqljdbc42.jar.