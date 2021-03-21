---
title: 'Odwołanie: Ubuntu Data Science Virtual Machine'
titleSuffix: Azure Data Science Virtual Machine
description: Szczegóły dotyczące narzędzi uwzględnionych w Ubuntu Data Science Virtual Machine
author: gvashishtha
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
ms.author: gopalv
ms.date: 09/11/2019
ms.topic: reference
ms.openlocfilehash: e9a55f72718d6ed5991f3d0f16323409bb0f699f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101661073"
---
# <a name="reference-ubuntu-linux-data-science-virtual-machine"></a>Reference: Ubuntu (Linux) Data Science Virtual Machine

Poniżej znajduje się lista dostępnych narzędzi Ubuntu Data Science Virtual Machine. 

## <a name="deep-learning-libraries"></a>Biblioteki uczenia głębokiego

### <a name="cntk"></a>CNTK

Microsoft Cognitive Toolkit to zestaw narzędzi do uczenia głębokiego "open source". Powiązania języka Python są dostępne w środowiskach głównych i py35 Conda. Ma także narzędzie wiersza polecenia (CNTK), które już znajduje się w ścieżce.

Przykładowe notesy w języku Python są dostępne w JupyterHub. Aby uruchomić podstawowy przykład w wierszu polecenia, uruchom następujące polecenia w powłoce:

```bash
cd /home/[USERNAME]/notebooks/CNTK/HelloWorld-LogisticRegression
cntk configFile=lr_bs.cntk makeMode=false command=Train
```

Aby uzyskać więcej informacji, zobacz sekcję CNTK w witrynie [GitHub](https://github.com/Microsoft/CNTK) i [CNTK wiki](https://github.com/Microsoft/CNTK/wiki).

### <a name="caffe"></a>Caffe

Caffe to platforma uczenia głębokiego w witrynie Berkeley Vision i Learning Center. Jest on dostępny w/opt/Caffe. Przykłady można znaleźć w/opt/Caffe/examples.

### <a name="caffe2"></a>Caffe2

Caffe2 to platforma uczenia głębokiego w serwisie Facebook, która jest oparta na Caffe. Jest on dostępny w języku Python 2,7 w środowisku głównym Conda. Aby go uaktywnić, uruchom następujące polecenie w powłoce:

```bash
source /anaconda/bin/activate root
```

Niektóre przykładowe notesy są dostępne w JupyterHub.

### <a name="h2o"></a>H2O

H2O to szybka, rozproszona platforma do uczenia maszynowego i analizy predykcyjnej. Pakiet języka Python jest instalowany w środowiskach głównych i py35 Anaconda. Pakiet języka R jest również instalowany. 

Aby otworzyć H2O z wiersza polecenia, uruchom polecenie `java -jar /dsvm/tools/h2o/current/h2o.jar` . Istnieją różne [Opcje wiersza polecenia](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/starting-h2o.html#from-the-command-line) , które można skonfigurować. Aby rozpocząć pracę, możesz uzyskać dostęp do interfejsu użytkownika sieci Web przepływu `http://localhost:54321` . Przykładowe notesy są również dostępne w JupyterHub.

### <a name="keras"></a>Keras

Keras to interfejs API sieci neuronowych wysokiego poziomu w języku Python. Może działać w oparciu o TensorFlow, Microsoft Cognitive Toolkit lub Theano. Jest on dostępny w środowiskach głównych i py35 języka Python.

### <a name="mxnet"></a>MXNet

MXNet to platforma uczenia głębokiego, zaprojektowana pod kątem wydajności i elastyczności. Ma powiązania R i Python zawarte w DSVM. Przykładowe notesy są zawarte w JupyterHub, a przykładowy kod jest dostępny w/dsvm/Samples/mxnet.

### <a name="nvidia-digits"></a>CYFRY NVIDIA

System uczenia procesora GPU o głębokiej technologii NVIDIA, znany jako cyfry, to system upraszczający typowe zadania uczenia głębokiego. Te zadania obejmują zarządzanie danymi, projektowanie i uczenie sieci neuronowych w systemach GPU oraz monitorowanie wydajności w czasie rzeczywistym dzięki zaawansowanej wizualizacji.

CYFRY są dostępne jako usługa o nazwie *cyfry*. Uruchom usługę i przejdź do, aby rozpocząć `http://localhost:5000` pracę.

CYFRY są również instalowane jako moduł języka Python w środowisku głównym Conda.

### <a name="tensorflow"></a>TensorFlow

TensorFlow to biblioteka usługi głębokiego uczenia firmy Google. Jest to Biblioteka oprogramowania Open Source służąca do obliczania liczbowego przy użyciu wykresów przepływu danych. TensorFlow jest dostępny w środowisku języka Python py35, a niektóre przykładowe notesy są zawarte w JupyterHub.

### <a name="theano"></a>Theano

Theano to biblioteka języka Python służąca do wydajnego obliczania liczbowego. Jest on dostępny w środowiskach głównych i py35 języka Python. 

### <a name="torch"></a>Torch

Torch to naukowa platforma obliczeniowa z szeroką obsługą algorytmów uczenia maszynowego. Jest on dostępny w/dsvm/Tools/Torch, a w wierszu polecenia dostępne są **te interakcyjne** sesje i Menedżer pakietów LuaRocks. Przykłady są dostępne w/dsvm/Samples/Torch.

PyTorch jest również dostępna w głównym środowisku Anaconda. Przykłady znajdują się w/dsvm/Samples/pytorch.

## <a name="microsoft-machine-learning-server"></a>Serwer Microsoft Machine Learning

Język R jest jednym z najpopularniejszych języków analizy danych i uczenia maszynowego. Jeśli chcesz użyć języka R do analizy, maszyna wirtualna ma Microsoft Machine Learning Server z biblioteką jądra Microsoft R Open i Math. Biblioteka jądra matematycznego optymalizuje operacje matematyczne wspólne w algorytmach analitycznych. Program Microsoft R Open ma 100 procent zgodny z CRAN R, a wszystkie biblioteki języka R opublikowane w CRAN można zainstalować na stronie Microsoft R Open. 

Machine Learning Server umożliwia skalowanie i operacjonalizacji modeli R w usługach sieci Web. Można edytować programy R w jednym z edytorów domyślnych, takich jak RStudio, VI lub Emacs:. Jeśli wolisz używać edytora Emacs:, został on wstępnie zainstalowany. Pakiet Emacs: ESS (Emacs: mówiący o statystyce) upraszcza pracę z plikami języka R w edytorze emacs:.

Aby otworzyć konsolę języka R, należy wprowadzić wartość **R** w powłoce. To polecenie umożliwia przejście do interaktywnego środowiska. Aby opracować program R, zazwyczaj używany jest Edytor, taki jak Emacs: lub VI, a następnie uruchamianie skryptów w języku R. Dzięki RStudio masz pełny graficzny IDE do tworzenia programu R.

Istnieje również skrypt języka R umożliwiający zainstalowanie [najwyżej 20 pakietów r](https://www.kdnuggets.com/2015/06/top-20-r-packages.html) , jeśli chcesz. Ten skrypt można uruchomić po zakończeniu pracy z interfejsem Interactive języka R. Jak wspomniano wcześniej, możesz otworzyć ten interfejs, wprowadzając polecenie **R** w powłoce.  

## <a name="python"></a>Python

Anaconda Python jest instalowany z środowiskami Python 2,7 i 3,5. Środowisko 2,7 jest nazywane _korzeniem_, a środowisko 3,5 jest nazywane _py35_. Ta dystrybucja zawiera podstawowy język Python wraz z około 300 z najpopularniejszych pakietów obliczeń matematycznych, inżynieryjnych i analitycznych.

Środowisko py35 jest ustawieniem domyślnym. Aby aktywować środowisko główne (2,7), użyj tego polecenia:

```bash
source activate root
```

Aby ponownie aktywować środowisko py35, użyj tego polecenia:

```bash
source activate py35
```

Aby wywołać interaktywną sesję języka Python, wprowadź w powłoce język **Python** . 

Zainstaluj dodatkowe biblioteki języka Python za pomocą Conda lub PIP. W przypadku programu PIP najpierw Aktywuj poprawne środowisko, jeśli nie chcesz go domyślnie:

```bash
source activate root
pip install <package>
```

Lub Określ pełną ścieżkę do PIP:

```bash
/anaconda/bin/pip install <package>
```

W przypadku Conda należy zawsze określić nazwę środowiska (py35 lub root):

```bash
conda install <package> -n py35
```

Jeśli korzystasz z interfejsu graficznego lub chcesz skonfigurować przekazywanie X11, możesz wprowadzić **platformy PyCharm itd** , aby otworzyć środowisko IDE platformy PyCharm itd Python. Można użyć domyślnych edytorów tekstu. Ponadto można użyć Spyder, środowiska IDE języka Python, które jest powiązane z dystrybucją języka Python Anaconda. Spyder potrzebuje graficznego pulpitu lub X11ego przesyłania dalej. Pulpit graficzny ma skrót do Spyder.

## <a name="jupyter-notebook"></a>Jupyter Notes

Dystrybucja Anaconda zawiera również Notes Jupyter, czyli środowisko do udostępniania kodu i analizy. Notes Jupyter jest dostępny za pomocą JupyterHub. Zaloguj się przy użyciu nazwy użytkownika i hasła lokalnego systemu Linux.

Serwer notesu Jupyter został wstępnie skonfigurowany przy użyciu jądra Python 2, Python 3 i języka R. Użyj ikony **Jupyter Notebook** pulpitu, aby otworzyć przeglądarkę i uzyskać dostęp do serwera notesu. Jeśli korzystasz z maszyny wirtualnej za pośrednictwem protokołu SSH lub klienta X2Go, możesz również uzyskać dostęp do serwera notesu Jupyter w systemie `https://localhost:8000/` .

> [!NOTE]
> Kontynuuj, jeśli otrzymasz ostrzeżenia dotyczące certyfikatów.

Można uzyskać dostęp do serwera notesu Jupyter z dowolnego hosta. Wprowadź **https:// \<VM DNS name or IP address\> : 8000/**.

> [!NOTE]
> Port 8000 jest domyślnie otwarty w zaporze po zainicjowaniu obsługi maszyny wirtualnej. 

Spakowane przykładowe notesy — jeden w języku Python i jeden w języku R. Po uwierzytelnieniu do notesu Jupyter za pomocą nazwy użytkownika i hasła lokalnego systemu Linux możesz zobaczyć link do przykładów na stronie głównej notesu. Nowy Notes można utworzyć, wybierając pozycję **Nowy**, a następnie wybierając odpowiednie jądro języka. Jeśli przycisk **Nowy** nie jest widoczny, wybierz ikonę **Jupyter** w lewym górnym rogu, aby przejść do strony głównej serwera notesu.

## <a name="apache-spark-standalone"></a>Autonomiczne Apache Spark

Autonomiczne wystąpienie Apache Spark jest preinstalowane na DSVM systemu Linux, aby ułatwić tworzenie aplikacji platformy Spark lokalnie przed przetestowaniem i wdrażaniem ich w dużych klastrach. 

Można uruchamiać programy PySpark za pomocą jądra Jupyter. Po otwarciu Jupyter wybierz przycisk **Nowy** i powinna zostać wyświetlona lista dostępnych jądra. **Spark-Python** to jądro PySpark, które umożliwia tworzenie aplikacji platformy Spark przy użyciu języka Python. Do skompilowania programu Spark można także użyć środowiska IDE języka Python, takiego jak platformy PyCharm itd lub Spyder. 

W tym wystąpieniu autonomicznym stos Spark działa w ramach wywołującego programu klienckiego. Ta funkcja przyspiesza i ułatwia rozwiązywanie problemów w porównaniu z tworzeniem w klastrze Spark.

Jupyter udostępnia przykładowy Notes PySpark. Można go znaleźć w katalogu SparkML w katalogu głównym Jupyter ($HOME/notebooks/SparkML/pySpark). 

W przypadku programowania w języku R dla platformy Spark można użyć Microsoft Machine Learning Server, Spark lub sparklyr. 

Przed uruchomieniem w kontekście platformy Spark w Microsoft Machine Learning Server należy wykonać jednorazowy krok konfiguracji w celu włączenia lokalnego jednego węzła usługi Hadoop HDFS i wystąpienia nici. Domyślnie usługi Hadoop są zainstalowane, ale wyłączone na DSVM. Aby je włączyć, należy uruchomić następujące polecenia jako główne po raz pierwszy:

```bash
echo -e 'y\n' | ssh-keygen -t rsa -P '' -f ~hadoop/.ssh/id_rsa
cat ~hadoop/.ssh/id_rsa.pub >> ~hadoop/.ssh/authorized_keys
chmod 0600 ~hadoop/.ssh/authorized_keys
chown hadoop:hadoop ~hadoop/.ssh/id_rsa
chown hadoop:hadoop ~hadoop/.ssh/id_rsa.pub
chown hadoop:hadoop ~hadoop/.ssh/authorized_keys
systemctl start hadoop-namenode hadoop-datanode hadoop-yarn
```

Usługi związane z usługą Hadoop można zatrzymać, gdy nie są potrzebne, uruchamiając program ```systemctl stop hadoop-namenode hadoop-datanode hadoop-yarn``` .

Katalog/dsvm/samples/MRS zawiera przykład demonstrujący sposób tworzenia i testowania Microsoft Machine Learning Server w zdalnym kontekście Spark (autonomiczne wystąpienie platformy Spark w DSVM).

## <a name="ides-and-editors"></a>Środowisk IDE i redaktorzy

Można wybrać kilka edytorów kodu, w tym VI/VIM, Emacs:, platformy PyCharm itd, RStudio i IntelliJ. 

Platformy PyCharm itd, RStudio i IntelliJ są edytorami graficznymi. Aby móc z nich korzystać, musisz się zalogować na pulpicie graficznym. Można je otworzyć przy użyciu skrótów menu programu Desktop i aplikacji.

Vim i Emacs: są edytorami tekstowymi. W systemie Emacs: dodatek ESS udostępnia pracę z językiem R łatwiejszym w edytorze emacs:. Więcej informacji można znaleźć w [witrynie sieci Web ESS](https://ess.r-project.org/).

Wartość lateks jest instalowana za pomocą pakietu TeXLive wraz z pakietem dodatku Emacs: o nazwie [AUCTeX](https://www.gnu.org/software/auctex/manual/auctex/auctex.html). Ten pakiet upraszcza tworzenie dokumentów lateks w Emacs:.  

## <a name="databases"></a>Bazy danych

### <a name="graphical-sql-client"></a>Graficzny klient SQL

SQuirrel SQL, graficzny klient SQL, może łączyć się z różnymi bazami danych (takimi jak Microsoft SQL Server i MySQL) i uruchamiać zapytania SQL. Program SQuirrel SQL można uruchomić z graficznej sesji pulpitu (na przykład za pośrednictwem klienta X2Go) przy użyciu ikony pulpitu. Można też uruchomić klienta programu za pomocą następującego polecenia w powłoce:

```bash
/usr/local/squirrel-sql-3.7/squirrel-sql.sh
```

Przed pierwszym użyciem Skonfiguruj sterowniki i aliasy baz danych. Sterowniki JDBC znajdują się w/usr/share/Java/jdbcdrivers.

Aby uzyskać więcej informacji, zobacz [Squirrel SQL](http://squirrel-sql.sourceforge.net/index.php?page=screenshots).

### <a name="command-line-tools-for-accessing-microsoft-sql-server"></a>Narzędzia wiersza polecenia do uzyskiwania dostępu Microsoft SQL Server

Pakiet sterowników ODBC dla SQL Server również zawiera dwa narzędzia wiersza polecenia:

- **BCP**: Narzędzie bcp zbiorczo kopiuje dane między wystąpieniem Microsoft SQL Server i plikiem danych w formacie określonym przez użytkownika. Narzędzia bcp można użyć do zaimportowania dużej liczby nowych wierszy do tabel SQL Server lub do eksportowania danych z tabel do plików danych. Aby zaimportować dane do tabeli, należy użyć pliku formatu utworzonego dla tej tabeli. Lub należy zrozumieć strukturę tabeli i typy danych, które są prawidłowe dla jego kolumn.

  Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia za pomocą narzędzia bcp](/sql/connect/odbc/linux-mac/connecting-with-bcp).

- **sqlcmd**: można wprowadzać instrukcje Transact-SQL za pomocą narzędzia sqlcmd. Możesz również wprowadzić procedury systemowe i pliki skryptów w wierszu polecenia. To narzędzie używa ODBC do uruchamiania partii Transact-SQL.

  Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia przy użyciu narzędzia sqlcmd](/sql/connect/odbc/linux-mac/connecting-with-sqlcmd).

  > [!NOTE]
  > Istnieją pewne różnice w tym narzędziu między platformami Linux i Windows. Zapoznaj się z dokumentacją, aby uzyskać szczegółowe informacje.

### <a name="database-access-libraries"></a>Biblioteki dostępu do bazy danych

Biblioteki są dostępne w językach R i Python na potrzeby dostępu do bazy danych:

* W języku R można użyć pakietu RODBC lub pakietu dplyr do wykonywania zapytań lub uruchamiania instrukcji SQL na serwerze bazy danych.
* W języku Python Biblioteka moduł pyodbc zapewnia dostęp do bazy danych za pomocą ODBC jako warstwy źródłowej.  

## <a name="azure-tools"></a>Narzędzia platformy Azure

Następujące narzędzia platformy Azure są zainstalowane na maszynie wirtualnej:

* Interfejs wiersza polecenia **platformy Azure**: za pomocą poleceń powłoki można tworzyć zasoby platformy Azure i zarządzać nimi za pomocą polecenia. Aby otworzyć narzędzia platformy Azure, wprowadź **Pomoc platformy Azure**. Aby uzyskać więcej informacji, zobacz [stronę dokumentacji interfejsu wiersza polecenia platformy Azure](/cli/azure/get-started-with-az-cli2).
* **Eksplorator usługi Azure Storage**: Eksplorator usługi Azure Storage to graficzne narzędzie umożliwiające przeglądanie obiektów przechowywanych na koncie usługi Azure Storage oraz przekazywanie i pobieranie danych do i z obiektów blob platformy Azure. Dostęp do Eksplorator usługi Storage można uzyskać za pomocą ikony skrótu pulpitu. Możesz również otworzyć ją z poziomu monitu powłoki, wprowadzając **StorageExplorer**. Użytkownik musi być zalogowany z poziomu klienta usługi X2Go lub mieć skonfigurowane przekazywanie X11.
* **Biblioteki platformy Azure**: poniżej przedstawiono niektóre z wstępnie zainstalowanych bibliotek.
  
  * **Python**: biblioteki związane z platformą Azure w języku Python to *Azure* *, Azure,* *pydocumentdb* i *moduł pyodbc*. Za pomocą pierwszych trzech bibliotek można uzyskać dostęp do usług Azure Storage, Azure Machine Learning i Azure Cosmos DB (baza danych NoSQL na platformie Azure). Czwarta Biblioteka moduł pyodbc (wraz ze sterownikiem Microsoft ODBC dla SQL Server) umożliwia dostęp do SQL Server, Azure SQL Database i Azure Synapse Analytics z poziomu języka Python przy użyciu interfejsu ODBC. Wprowadź **listę PIP** , aby wyświetlić wszystkie wymienione biblioteki. Upewnij się, że uruchamiasz to polecenie w środowiskach Python 2,7 i 3,5.
  * **R**: biblioteki związane z platformą Azure w języku R to Azure i RODBC.
  * **Java**: listę bibliotek Java platformy Azure można znaleźć w katalogu/dsvm/SDK/AzureSDKJava na maszynie wirtualnej. Biblioteki kluczy to usługi Azure Storage i interfejsy API zarządzania, Azure Cosmos DB i sterowniki JDBC dla SQL Server.  

Dostęp do [Azure Portal](https://portal.azure.com) można uzyskać z wstępnie zainstalowanej przeglądarki Firefox. Na Azure Portal można tworzyć i monitorować zasoby platformy Azure oraz zarządzać nimi.

## <a name="azure-machine-learning"></a>Azure Machine Learning

Azure Machine Learning to w pełni zarządzana usługa w chmurze, która umożliwia tworzenie, wdrażanie i udostępnianie rozwiązań do analizy predykcyjnej. Możesz tworzyć eksperymenty i modele w programie Azure Machine Learning Studio (wersja zapoznawcza). Możesz uzyskać do niego dostęp z przeglądarki sieci Web na Data Science Virtual Machine, odwiedzając [Microsoft Azure Machine Learning](https://ml.azure.com).

Po zalogowaniu się do Azure Machine Learning Studio można użyć kanwy eksperymentowania do utworzenia przepływu logicznego dla algorytmów uczenia maszynowego. Masz również dostęp do notesu Jupyter, który jest hostowany w Azure Machine Learning i może bezproblemowo współpracować z eksperymentami w programie Azure Machine Learning Studio. 

Operacjonalizować modele uczenia maszynowego, które zostały skompilowane, umieszczając je w interfejsie usługi sieci Web. Modele uczenia maszynowego operacjonalizowania umożliwiają klientom w dowolnym języku wywoływanie prognoz z tych modeli. Aby uzyskać więcej informacji, zobacz [dokumentację Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/).

Możesz również kompilować modele w języku R lub Python na maszynie wirtualnej, a następnie wdrożyć je w środowisku produkcyjnym na Azure Machine Learning. W celu włączenia tej funkcji zainstalowano biblioteki w języku R (**Azure**) i Python (**Azure**).

> [!NOTE]
> Te instrukcje zostały opracowane dla Data Science Virtual Machine wersji systemu Windows. Jednak informacje o wdrażaniu modeli do Azure Machine Learning mają zastosowanie do maszyny wirtualnej z systemem Linux.

## <a name="machine-learning-tools"></a>Narzędzia uczenia maszynowego

Maszyna wirtualna zawiera narzędzia i algorytmy uczenia maszynowego, które zostały wstępnie skompilowane i wstępnie zainstalowane lokalnie. Są one następujące:

* **Vowpal Wabbit**: Krótki algorytm uczenia w trybie online.
* **xgboost**: narzędzie, które zapewnia zoptymalizowane, podwyższające algorytmy drzewa.
* **Rattle**: narzędzie graficzne oparte na języku R ułatwiające eksplorowanie i modelowanie danych.
* **Python**: Anaconda Python jest powiązany z algorytmami uczenia maszynowego z bibliotekami, takimi jak Scikit — uczenie się. Inne biblioteki można zainstalować za pomocą `pip install` polecenia.
* **LightGBM**: szybka i rozproszona platforma do tworzenia gradientów o wysokiej wydajności oparta na algorytmach drzewa decyzyjnego.
* **R**: Zaawansowana biblioteka funkcji uczenia maszynowego jest dostępna dla języka R. wstępnie zainstalowane biblioteki to LM, GLM, randomForest i rpart. Aby zainstalować inne biblioteki, należy uruchomić następujące polecenie:

    ```r
    install.packages(<lib name>)
    ```

Poniżej znajdują się dodatkowe informacje na temat pierwszych trzech narzędzi uczenia maszynowego na liście.

### <a name="vowpal-wabbit"></a>Vowpal Wabbit

Vowpal Wabbit to system uczenia maszynowego, który używa takich technik jak online, Hashing, allreduce, redukcyjny, learning2search, Active i Interactive uczenie się.

Aby uruchomić narzędzie w podstawowym przykładzie, użyj następujących poleceń:

```bash
cp -r /dsvm/tools/VowpalWabbit/demo vwdemo
cd vwdemo
vw house_dataset
```

Istnieją inne, większe demonstracje w tym katalogu. Aby uzyskać więcej informacji na temat Vowpal Wabbit, zobacz  [sekcję dotyczącą witryny GitHub](https://github.com/JohnLangford/vowpal_wabbit) i [Vowpal Wabbit wiki](https://github.com/JohnLangford/vowpal_wabbit/wiki).

### <a name="xgboost"></a>xgboost

Biblioteka xgboost jest zaprojektowana i zoptymalizowana pod kątem podwyższania poziomu (drzewa). Celem tej biblioteki jest wypchnięcie ograniczeń obliczeniowych maszyn do skrajnych wymagań w celu zapewnienia skalowalności, przenośnej i dokładnej promocji drzewa o dużej skali.

Jest on dostarczany jako wiersz polecenia i biblioteka języka R. Aby użyć tej biblioteki w języku R, można uruchomić interaktywną sesję języka R (wprowadzając polecenie **R** w powłoce) i załadować bibliotekę.

Oto prosty przykład, który można uruchomić w wierszu polecenia języka R:

```R
library(xgboost)

data(agaricus.train, package='xgboost')
data(agaricus.test, package='xgboost')
train <- agaricus.train
test <- agaricus.test
bst <- xgboost(data = train$data, label = train$label, max.depth = 2,
                eta = 1, nthread = 2, nround = 2, objective = "binary:logistic")
pred <- predict(bst, test$data)
```

Aby uruchomić wiersz polecenia xgboost, poniżej przedstawiono polecenia do uruchomienia w powłoce:

```bash
cp -r /dsvm/tools/xgboost/demo/binary_classification/ xgboostdemo
cd xgboostdemo
xgboost mushroom.conf
```

Plik. model jest zapisywana w określonym katalogu. Informacje na temat tego przykładu demonstracyjnego można znaleźć [w witrynie GitHub](https://github.com/dmlc/xgboost/tree/master/demo/CLI/binary_classification).

Aby uzyskać więcej informacji na temat xgboost, zobacz [stronę dokumentacji xgboost](https://xgboost.readthedocs.org/en/latest/) i jej [repozytorium GitHub](https://github.com/dmlc/xgboost).

### <a name="rattle"></a>Rattle

Rattle ( **R** **Nalityczny** **t** OOL **t** o **L** zdobyć **E** asily) korzysta z eksplorowania i modelowania danych opartych na graficznym interfejsie użytkownika. Prezentuje statystyczne i wizualne podsumowania danych, przekształca dane, które można łatwo modelować, kompiluje zarówno nienadzorowane, jak i nadzorowane modele z danych, przedstawia wydajność modeli graficznie i ocenia nowe zestawy danych. Generuje również kod języka R, replikuje operacje w interfejsie użytkownika, które można uruchomić bezpośrednio w języku R lub użyć jako punktu wyjścia do dalszej analizy.

Aby uruchomić Rattle, musisz być w sesji logowania na pulpicie graficznym. Na terminalu wprowadź **r** , aby otworzyć środowisko języka r. W wierszu polecenia języka R wprowadź następujące polecenia:

```R
library(rattle)
rattle()
```

Teraz zostanie otwarty interfejs graficzny z zestawem kart. Poniższe kroki szybkiego startu w programie Rattle umożliwiają użycie przykładowego zestawu danych pogody i skompilowanie modelu. W niektórych krokach zostanie wyświetlony monit o automatyczne zainstalowanie i załadowanie niektórych wymaganych pakietów języka R, które nie znajdują się jeszcze w systemie.

> [!NOTE]
> Jeśli nie masz dostępu do instalacji pakietu w katalogu systemowym (domyślnie), w oknie konsoli języka R może zostać wyświetlony monit o zainstalowanie pakietów w bibliotece osobistej. Odpowiedź **y** , Jeśli zobaczysz te informacje.

1. Wybierz pozycję **Wykonaj**.
1. Zostanie wyświetlone okno dialogowe z pytaniem, czy chcesz użyć przykładowego zestawu danych pogody. Wybierz pozycję **tak** , aby załadować przykład.
1. Wybierz kartę **model** .
1. Wybierz pozycję **Execute (wykonaj** ), aby skompilować drzewo decyzyjne.
1. Wybierz pozycję **Rysuj** , aby wyświetlić drzewo decyzyjne.
1. Wybierz opcję **Las** i wybierz opcję **Execute (wykonaj** ), aby skompilować losowo Las.
1. Wybierz kartę **szacowanie** .
1. Wybierz opcję **ryzyko** , a następnie wybierz pozycję **Wykonaj** , aby wyświetlić wykresy z dwoma **ryzykiem (skumulowane)** .
1. Wybierz kartę **Dziennik** , aby wyświetlić wygenerowany kod R dla powyższych operacji.
   (Z powodu błędu w bieżącej wersji programu Rattle należy wstawić **#** znak przed **eksportem tego dziennika** do tekstu dziennika).
1. Wybierz przycisk **Eksportuj** , aby zapisać plik skryptu języka R o nazwie *weather_script. R* do folderu macierzystego.

Możesz wyjść z Rattle i R. Teraz można zmodyfikować wygenerowany skrypt języka R. Można również użyć skryptu w ten sposób i uruchomić go w dowolnym momencie, aby powtórzyć wszystko, co zostało zrobione w interfejsie użytkownika Rattle. Szczególnie w przypadku początkujących w języku R, jest to sposób, aby szybko przeprowadzić analizę i uczenie maszynowe w prostym interfejsie graficznym, podczas gdy automatycznie generuje kod w języku R, aby zmodyfikować lub poznać.

## <a name="next-steps"></a>Następne kroki

Masz dodatkowe pytania? Rozważ utworzenie [biletu pomocy technicznej](https://azure.microsoft.com/support/create-ticket/).