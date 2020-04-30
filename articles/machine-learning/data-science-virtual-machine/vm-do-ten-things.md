---
title: Eksplorowanie danych i modelu w systemie Windows
titleSuffix: Azure Data Science Virtual Machine
description: Wykonuj zadania eksploracji i modelowania danych w Data Science Virtual Machine systemu Windows.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 6574ccd7616ac1442b1c8c69b6218431ad912ab3
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204223"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Dziesięć rzeczy, które możesz wykonać w Data Science Virtual Machine systemu Windows

Windows Data Science Virtual Machine (DSVM) to zaawansowane środowisko programistyczne do nauki o danych, w którym można wykonywać Eksplorowanie danych i zadania modelowania. Środowisko jest już skompilowane i jest powiązane z kilkoma popularnymi narzędziami do analizy danych, które ułatwiają rozpoczęcie pracy z analizą dla wdrożeń lokalnych, chmurowych lub hybrydowych. 

DSVM ściśle współpracuje z usługami platformy Azure. Może odczytywać i przetwarzać dane, które są już przechowywane na platformie Azure, w Azure SQL Data Warehouse, Azure Data Lake, Azure Storage lub Azure Cosmos DB. Może również korzystać z innych narzędzi analitycznych, takich jak Azure Machine Learning i Azure Data Factory.

W tym artykule dowiesz się, jak używać DSVM do wykonywania zadań analizy danych i korzystania z innych usług platformy Azure. Poniżej przedstawiono niektóre czynności, które można wykonać na DSVM:

- Eksplorowanie danych i projektowanie modeli lokalnie na DSVM przy użyciu Microsoft Machine Learning Server i języka Python.
- Użyj notesu Jupyter, aby eksperymentować z danymi w przeglądarce przy użyciu języka Python 2, Python 3 i Microsoft R. (Microsoft R to wersja systemu R przeznaczona dla przedsiębiorstw, która została zaprojektowana pod kątem wydajności).
- Wdrażaj modele utworzone za pomocą języka R i Python na Azure Machine Learning, aby aplikacje klienckie mogły uzyskiwać dostęp do Twoich modeli przy użyciu prostego interfejsu usługi sieci Web.
- Administruj zasobami platformy Azure przy użyciu Azure Portal lub programu PowerShell.
- Zwiększ ilość miejsca w magazynie i Udostępnij zestawy danych o dużej skali/kod w całym zespole, tworząc Azure Files udział jako instalowalny dysk w DSVM.
- Udostępnianie kodu zespołowi za pomocą usługi GitHub. Uzyskaj dostęp do repozytorium przy użyciu wstępnie zainstalowanych klientów git: narzędzia Git bash i interfejsu GUI systemu Git.
- Uzyskuj dostęp do usług Azure Data and Analytics, takich jak Azure Blob Storage, Azure Data Lake, Azure HDInsight (Hadoop), Azure Cosmos DB, Azure SQL Data Warehouse i Azure SQL Database.
- Twórz raporty i pulpit nawigacyjny przy użyciu wystąpienia Power BI Desktop, które jest wstępnie zainstalowane w DSVM i wdrażaj je w chmurze.
- Dynamiczne skalowanie DSVM, aby spełniały potrzeby Twojego projektu.
- Zainstaluj dodatkowe narzędzia na maszynie wirtualnej.   

> [!NOTE]
> Dodatkowe opłaty za użycie dotyczą wielu usług magazynu danych i analiz wymienionych w tym artykule. Aby uzyskać szczegółowe informacje, zobacz stronę z [cennikiem platformy Azure](https://azure.microsoft.com/pricing/) .
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne

* Potrzebna jest subskrypcja platformy Azure. Możesz [utworzyć konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
* Instrukcje dotyczące aprowizacji Data Science Virtual Machine na Azure Portal są dostępne podczas [tworzenia maszyny wirtualnej](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Eksplorowanie danych i opracowywanie modeli przy użyciu Microsoft Machine Learning Server
Możesz użyć języków takich jak R i Python, aby przeprowadzić analizę danych bezpośrednio na DSVM.

W przypadku języka R można użyć środowiska IDE, takiego jak RStudio, które można znaleźć w menu Start lub na pulpicie. Można też użyć R Tools for Visual Studio. Firma Microsoft udostępniła dodatkowe biblioteki na podstawie CRAN R typu "open source", aby umożliwić skalowalną analizę i możliwość analizowania danych większych niż rozmiar pamięci dozwolony w równoległej analizie fragmentarycznej. 

W przypadku języka Python można użyć środowiska IDE, takiego jak Visual Studio Community Edition, na którym jest wstępnie zainstalowane rozszerzenie Python Tools for Visual Studio (PTVS). Domyślnie tylko język Python 3,6, główne środowisko Conda jest skonfigurowany w PTVS. Aby włączyć Anaconda Python 2,7, wykonaj następujące czynności:

1. Twórz środowiska niestandardowe dla każdej wersji, przechodząc do **narzędzi** > **Python Tools** > **Python**Environments, a następnie wybierając pozycję **+ Custom** w programie Visual Studio Community Edition.
1. Podaj opis i Ustaw ścieżkę prefiksu środowiska jako **c:\anaconda\envs\python2** dla anaconda Python 2,7.
1. Wybierz pozycję **Autowykrywanie** > **Zastosuj** , aby zapisać środowisko.

Zapoznaj się z [dokumentacją PTVS](https://aka.ms/ptvsdocs) , aby uzyskać szczegółowe informacje na temat tworzenia środowisk języka Python.

Teraz można utworzyć nowy projekt w języku Python. Przejdź do pozycji **plik** > **Nowy** > **projekt** > **Python** i wybierz typ kompilowanej aplikacji języka Python. Możesz ustawić środowisko Python dla bieżącego projektu na żądaną wersję (Python 2,7 lub 3,6), klikając prawym przyciskiem myszy **środowiska Python** , a następnie wybierając polecenie **Dodaj/Usuń środowiska Python**. Więcej informacji na temat pracy z programem PTVS można znaleźć w [dokumentacji produktu](https://aka.ms/ptvsdocs).

## <a name="use-jupyter-notebooks"></a>Korzystanie z notesów programu Jupyter
Jupyter Notebook udostępnia środowisko IDE oparte na przeglądarce do eksploracji i modelowania danych. W notesie Jupyter można używać języka Python 2, Python 3 lub R (zarówno typu open source, jak i Microsoft R Server).

Aby uruchomić Jupyter Notebook, wybierz ikonę **Jupyter Notebook** w menu **Start** lub na pulpicie. W wierszu polecenia DSVM można także uruchomić polecenie ```jupyter notebook``` z katalogu, w którym znajdują się już notesy, lub w którym chcesz utworzyć nowe notesy.  

Po rozpoczęciu Jupyter powinien zostać wyświetlony katalog zawierający kilka przykładowych notesów, które są wstępnie spakowane w DSVM. Co możesz teraz zrobić:

* Wybierz Notes, aby wyświetlić kod.
* Uruchom każdą komórkę, wybierając SHIFT + ENTER.
* Uruchom cały Notes, wybierając pozycję **Cell** > **przebieg**komórki.
* Utwórz nowy Notes, wybierając ikonę Jupyter (w lewym górnym rogu), wybierając przycisk **Nowy** po prawej stronie, a następnie wybierając język notesu (nazywany także jądrami).   

> [!NOTE]
> Obecnie obsługiwane są jądra Python 2,7, Python 3,6, R, Julia i PySpark w Jupyter. Jądro języka R obsługuje programowanie zarówno w języku R, jak i w programie Microsoft R.   
> 
> 

Gdy jesteś w notesie, możesz eksplorować dane, kompilować model i testować model przy użyciu wybranych bibliotek.

## <a name="train-and-deploy-models-by-using-azure-machine-learning"></a>Uczenie i wdrażanie modeli przy użyciu Azure Machine Learning
Po skompilowaniu i zweryfikowaniu modelu, następnym krokiem jest zwykle wdrożenie go w środowisku produkcyjnym. Ten krok umożliwia aplikacjom klienckim wywoływanie prognoz modelu w czasie rzeczywistym lub w trybie wsadowym. Azure Machine Learning zapewnia mechanizm do operacjonalizować modelu wbudowanego w języku R lub Python.

Gdy operacjonalizować model w Azure Machine Learning, zostanie wyświetlona usługa sieci Web. Umożliwia klientom wykonywanie wywołań REST, które są przekazywane do parametrów wejściowych i odbiera przewidywania z modelu jako dane wyjściowe.

### <a name="build-and-operationalize-python-models"></a>Kompiluj i operacjonalizować modele języka Python
Oto fragment kodu opracowany w notesie Jupyter języka Python, który kompiluje prosty model przy użyciu biblioteki Scikit-Dowiedz się:

```python
# IRIS classification
from sklearn import datasets
from sklearn import svm
clf = svm.SVC()
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
```

Metoda, która jest używana do wdrażania modeli języka Python w celu Azure Machine Learning zawija prognozę modelu do funkcji i zdobi ją z atrybutami dostarczonymi przez wstępnie zainstalowaną Azure Machine Learning biblioteki języka Python. Atrybuty należy zauważyć, że Azure Machine Learning identyfikator obszaru roboczego, klucz interfejsu API i parametry wejściowe i zwrotne.  

```python
from azureml import services
@services.publish(workspaceid, auth_token)
@services.types(sep_l=float, sep_w=float, pet_l=float, pet_w=float)
@services.returns(int)  # 0, or 1, or 2
def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]


return clf.predict(inputArray)
```

Klient może teraz wykonywać wywołania do usługi sieci Web. Wygoda otoki konstruuje żądania interfejsu API REST. Oto przykładowy kod umożliwiający korzystanie z usługi sieci Web:

```python
# Consume through web service URL and keys
from azureml import services
@services.service(url, api_key)
@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
@services.returns(float)
def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
pass

IrisPredictor(3,2,3,4)
```

> [!NOTE]
> Obecnie Biblioteka Azure Machine Learning jest obsługiwana tylko w języku Python 2,7.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>Tworzenie i operacjonalizować modeli R
Można wdrażać modele języka R utworzone na Data Science Virtual Machine lub w innym miejscu Azure Machine Learning w taki sposób, aby były podobne do języka Python. Oto konkretne kroki:

1. Utwórz plik Settings. JSON, aby podać identyfikator obszaru roboczego i token uwierzytelniania. 
2. Napisz otokę dla funkcji przewidywania modelu.
3. Wywołaj ```publishWebService``` w bibliotece Azure Machine Learning, aby przekazać otokę funkcji.  

Użyj poniższej procedury i fragmentów kodu, aby skonfigurować, skompilować, opublikować i korzystać z modelu jako usługi sieci Web w Azure Machine Learning.

#### <a name="set-up"></a>Konfiguruj

Utwórz plik Settings. JSON w katalogu o nazwie ```.azureml``` w katalogu macierzystym. Wprowadź parametry w obszarze roboczym Azure Machine Learning.

Oto Struktura pliku Settings. JSON:

```json
{"workspace":{
"id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
"authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
}}
```

#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>Tworzenie modelu w języku R i publikowanie go w Azure Machine Learning

```r
library(AzureML)
ws <- workspace(config="~/.azureml/settings.json")

if(!require("lme4")) install.packages("lme4")
library(lme4)
set.seed(1)
train <- sleepstudy[sample(nrow(sleepstudy), 120),]
m <- lm(Reaction ~ Days + Subject, data = train)

# Define a prediction function to publish based on the model:
sleepyPredict <- function(newdata){
        predict(m, newdata=newdata)
}

ep <- publishWebService(ws, fun = sleepyPredict, name="sleepy lm", inputSchema = sleepstudy, data.frame=TRUE)
```

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Korzystaj z modelu wdrożonego w Azure Machine Learning
Aby korzystać z modelu z aplikacji klienckiej, użyj biblioteki Azure Machine Learning, aby wyszukać opublikowaną usługę sieci Web według nazwy. Użyj wywołania `services` interfejsu API, aby określić punkt końcowy. Następnie wystarczy wywołać `consume` funkcję i przekazać ją do przewidywania.

Użyj poniższego kodu, aby wykorzystać model opublikowany jako usługa sieci Web Azure Machine Learning:

```r
library(AzureML)
library(lme4)
ws <- workspace(config="~/.azureml/settings.json")

s <-  services(ws, name = "sleepy lm")
s <- tail(s, 1) # use the last published function, in case of duplicate function names

ep <- endpoints(ws, s)

# Try this out, and compare with raw data
ans = consume(ep, sleepstudy)$ans
```

Zobacz więcej informacji [na temat pakietów języka R w Machine Learning Studio](/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning).

## <a name="manage-azure-resources"></a>Zarządzanie zasobami platformy Azure
DSVM nie tylko umożliwia tworzenie rozwiązań analitycznych lokalnie na maszynie wirtualnej. Umożliwia również dostęp do usług na platformie Azure w chmurze. Platforma Azure oferuje kilka usług obliczeniowych, magazynowych, analitycznych danych i innych, które można administrować i uzyskiwać do nich dostęp z DSVM.

Do administrowania subskrypcją platformy Azure i zasobami w chmurze dostępne są dwie opcje:
+ Użyj przeglądarki i przejdź do [Azure Portal](https://portal.azure.com).

+ Użyj skryptów programu PowerShell. Uruchom Azure PowerShell ze skrótu na pulpicie lub z menu **Start** . Szczegółowe informacje znajdują się w [dokumentacji Microsoft Azure PowerShell](../../powershell-azure-resource-manager.md) . 

## <a name="extend-storage-by-using-shared-file-systems"></a>Zwiększanie magazynu przy użyciu udostępnionych systemów plików
Analityki danych mogą udostępniać duże zestawy, kod lub inne zasoby w zespole. DSVM zawiera około 45 GB dostępnego miejsca. Aby zwiększyć magazyn, można użyć Azure Files i zainstalować go na co najmniej jednym wystąpieniu usługi DSVM lub uzyskać do niego dostęp za pośrednictwem interfejsu API REST. Możesz również użyć [Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md) lub użyć [Azure PowerShell](../../virtual-machines/windows/attach-disk-ps.md) , aby dodać dodatkowe dedykowane dyski danych. 

> [!NOTE]
> Maksymalna ilość miejsca w udziale Azure Files wynosi 5 TB. Limit rozmiaru każdego pliku wynosi 1 TB. 

Możesz użyć tego skryptu w Azure PowerShell, aby utworzyć udział Azure Files:

```powershell
# Authenticate to Azure.
Connect-AzAccount
# Select your subscription
Get-AzSubscription –SubscriptionName "<your subscription name>" | Select-AzSubscription
# Create a new resource group.
New-AzResourceGroup -Name <dsvmdatarg>
# Create a new storage account. You can reuse existing storage account if you want.
New-AzStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
# Set your current working storage account
Set-AzCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

# Create an Azure Files share
$s = New-AzStorageShare <<teamsharename>>
# Create a directory under the file share. You can give it any name
New-AzStorageDirectory -Share $s -Path <directory name>
# List the share to confirm that everything worked
Get-AzStorageFile -Share $s
```

Po utworzeniu udziału Azure Files można go zainstalować na dowolnej maszynie wirtualnej na platformie Azure. Zalecamy umieszczenie maszyny wirtualnej w tym samym centrum danych platformy Azure jako konta magazynu, aby uniknąć opłat za opóźnienia i transfer danych. Poniżej przedstawiono Azure PowerShell polecenia służące do instalowania dysku na DSVM:

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

Teraz możesz uzyskać dostęp do tego dysku, tak jak w przypadku wszystkich normalnych dysków na maszynie wirtualnej.

## <a name="share-code-in-github"></a>Udostępnianie kodu w usłudze GitHub
GitHub to repozytorium kodu, w którym można znaleźć przykłady kodu i źródła dla różnych narzędzi, wykorzystując technologie udostępnione przez społeczność deweloperów. Używa narzędzia Git jako technologii do śledzenia i przechowywania wersji plików kodu. GitHub jest również platformą, w której można utworzyć własne repozytorium do przechowywania udostępnionego kodu zespołu i dokumentacji, zaimplementować kontrolę wersji i kontrolować, kto ma dostęp do wyświetlania i tworzenia kodu. 

Odwiedź [strony pomocy usługi GitHub](https://help.github.com/) , aby uzyskać więcej informacji na temat korzystania z usługi git. Usługi GitHub można używać jako jednego ze sposobów współpracy z zespołem, używania kodu opracowanego przez społeczność i współtworzenia kodu dla społeczności.

DSVM jest ładowany z narzędziami klienckimi w wierszu polecenia i na graficznym interfejsie użytkownika w celu uzyskania dostępu do repozytorium GitHub. Narzędzie wiersza polecenia, które współpracuje z usługami git i GitHub, nosi nazwę git bash. Program Visual Studio jest zainstalowany na DSVM i ma rozszerzenia git. Ikony tych narzędzi można znaleźć w menu **Start** i na pulpicie.

Aby pobrać kod z repozytorium GitHub, użyj ```git clone``` polecenia. Na przykład aby pobrać repozytorium analizy danych opublikowane przez firmę Microsoft w bieżącym katalogu, możesz uruchomić następujące polecenie w narzędziu git bash:

    git clone https://github.com/Azure/DataScienceVM.git

W programie Visual Studio można wykonać tę samą operację klonowania. Poniższy zrzut ekranu pokazuje, jak uzyskać dostęp do narzędzi git i GitHub w programie Visual Studio:

![Zrzut ekranu programu Visual Studio z wyświetlonym połączeniem GitHub](./media/vm-do-ten-things/VSGit.PNG)

Więcej informacji na temat używania usługi git do pracy z repozytorium GitHub można znaleźć z zasobów dostępnych w witrynie github.com. [Arkusz Ściągawka](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) jest przydatnym odwołaniem.

## <a name="access-azure-data-and-analytics-services"></a>Dostęp do usług Azure Data and Analytics
### <a name="azure-blob-storage"></a>Azure Blob Storage
Magazyn obiektów blob platformy Azure to niezawodna, ekonomiczna usługa magazynu w chmurze, w której dane są duże i małe. W tej sekcji opisano sposób przenoszenia danych do usługi BLOB Storage i uzyskiwania dostępu do danych przechowywanych w obiekcie blob platformy Azure.

#### <a name="prerequisites"></a>Wymagania wstępne

* Utwórz konto usługi Azure Blob Storage na podstawie [Azure Portal](https://portal.azure.com).

   ![Zrzut ekranu procesu tworzenia konta magazynu w Azure Portal](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Upewnij się, że narzędzie wiersza polecenia AzCopy jest wstępnie zainstalowane: ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```. Katalog zawierający plik AzCopy. exe znajduje się już w zmiennej środowiskowej PATH, dlatego można uniknąć wpisywania pełnej ścieżki polecenia podczas uruchamiania tego narzędzia. Więcej informacji o narzędziu AzCopy można znaleźć w [dokumentacji AzCopy](../../storage/common/storage-use-azcopy.md).
* Uruchom narzędzie Eksplorator usługi Azure Storage. Można go pobrać z [witryny sieci web Eksplorator usługi Storage](https://storageexplorer.com/). 

   ![Zrzut ekranu przedstawiający Eksplorator usługi Azure Storage uzyskiwania dostępu do konta magazynu](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>Przenoszenie danych z maszyny wirtualnej do obiektu blob platformy Azure: AzCopy

Aby przenieść dane między lokalnymi plikami i magazynem obiektów blob, można użyć AzCopy w wierszu polecenia lub w programie PowerShell:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Zastąp **C:\MyFolder** ścieżką, w której jest przechowywany plik, **mojekontomagazynu** z nazwą konta magazynu obiektów BLOB **, z** nazwą kontenera i **kluczem konta magazynu** za pomocą klucza dostępu do magazynu obiektów BLOB. Poświadczenia konta magazynu można znaleźć w [Azure Portal](https://portal.azure.com).

Uruchom polecenie AzCopy w programie PowerShell lub z poziomu wiersza polecenia. Oto przykładowe użycie polecenia AzCopy:

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

Po uruchomieniu polecenia AzCopy w celu skopiowania do obiektu blob platformy Azure plik zostanie wyświetlony w Eksplorator usługi Azure Storage.

![Zrzut ekranu przedstawiający konto magazynu, w którym jest wyświetlany przekazany plik CSV](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>Przenoszenie danych z maszyny wirtualnej do obiektu blob platformy Azure: Eksplorator usługi Azure Storage

Możesz również przekazać dane z lokalnego pliku na maszynę wirtualną przy użyciu Eksplorator usługi Azure Storage:

* Aby przekazać dane do kontenera, wybierz kontener docelowy i wybierz przycisk **Przekaż** . ![Zrzut ekranu przycisku przekazywania w Eksplorator usługi Azure Storage](./media/vm-do-ten-things/storage-accounts.png)
* Wybierz wielokropek (**...**) z prawej strony pola **pliki** , wybierz co najmniej jeden plik do przekazania z systemu plików, a następnie wybierz pozycję **Przekaż** , aby rozpocząć przekazywanie plików. ![Zrzut ekranu przedstawiający okno dialogowe przekazywanie plików](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-machine-learning-reader-module"></a>Odczytywanie danych z obiektu blob platformy Azure: Machine Learning modułu czytnika

W Azure Machine Learning Studio możesz użyć modułu import danych, aby odczytywać dane z obiektu BLOB.

![Zrzut ekranu modułu import danych w Machine Learning Studio](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Odczytywanie danych z obiektu blob platformy Azure: Język Python ODBC

Biblioteka BlobService umożliwia odczytywanie danych bezpośrednio z obiektu BLOB w notesie Jupyter lub w programie w języku Python.

Najpierw zaimportuj wymagane pakiety:

```python
import pandas as pd
from pandas import Series, DataFrame
import numpy as np
import matplotlib.pyplot as plt
from time import time
import pyodbc
import os
from azure.storage.blob import BlobService
import tables
import time
import zipfile
import random
```

Następnie podłącz poświadczenia konta usługi BLOB Storage i Odczytaj dane z obiektu BLOB:

```python
CONTAINERNAME = 'xxx'
STORAGEACCOUNTNAME = 'xxxx'
STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
localfilename = 'trip_data_1.csv'
LOCALDIRECTORY = os.getcwd()
LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

#download from blob
t1 = time.time()
blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
t2 = time.time()
print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

#unzip downloaded files if needed
#with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
#    z.extractall(LOCALDIRECTORY)

df1 = pd.read_csv(LOCALFILE, header=0)
df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
print 'the size of the data is: %d rows and  %d columns' % df1.shape
```

Dane są odczytywane jako ramka danych:

![Zrzut ekranu przedstawiający pierwszych 10 wierszy danych](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake Storage to repozytorium do skalowania danych Big Data, które jest zgodne z usługą Hadoop rozproszony system plików (HDFS). Współpracuje z usługami Hadoop, Spark i Azure Data Lake Analytics. W tej sekcji dowiesz się, jak przenosić dane do Azure Data Lake Storage i uruchamiać analizy przy użyciu Azure Data Lake Analytics.

#### <a name="prerequisites"></a>Wymagania wstępne

* Utwórz wystąpienie Azure Data Lake Analytics w [Azure Portal](https://portal.azure.com).

   ![Zrzut ekranu przedstawiający tworzenie wystąpienia Data Lake Analytics z Azure Portal](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* [Narzędzia Azure Data Lake i Stream Analytics Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) są już zainstalowane w programie Visual Studio Community Edition na maszynie wirtualnej. Po uruchomieniu programu Visual Studio i zalogowaniu się do subskrypcji platformy Azure powinno być widoczne Twoje konto i magazyn usługi Azure Data Analytics w lewym panelu programu Visual Studio.

   ![Zrzut ekranu przedstawiający wtyczkę dla narzędzi Data Lake w programie Visual Studio](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

#### <a name="move-data-from-a-vm-to-data-lake-azure-data-lake-explorer"></a>Przenoszenie danych z maszyny wirtualnej do Data Lake: Eksplorator Azure Data Lake

Za pomocą Eksploratora Azure Data Lake można [przekazać dane z plików lokalnych na maszynę wirtualną do Data Lake Storage](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

Możesz również utworzyć potok danych, aby operacjonalizować przenoszenie danych do lub z Azure Data Lake przy użyciu [Azure Data Factory](https://azure.microsoft.com/services/data-factory/). [Ten artykuł](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) przeprowadzi Cię przez kroki tworzenia potoków danych.

#### <a name="read-data-from-an-azure-blob-to-data-lake-u-sql"></a>Odczytywanie danych z obiektu blob platformy Azure do Data Lake: U-SQL

Jeśli Twoje dane znajdują się w usłudze Azure Blob Storage, możesz bezpośrednio odczytywać dane z obiektu blob platformy Azure w zapytaniu U-SQL. Przed złożeniem zapytania U-SQL upewnij się, że konto magazynu obiektów BLOB jest połączone z wystąpieniem Azure Data Lake. Przejdź do Azure Portal, Znajdź pulpit nawigacyjny Azure Data Lake Analytics, wybierz pozycję **Dodaj źródło danych**, wybierz typ magazynu **usługi Azure Storage**, a następnie wpisz nazwę i klucz konta usługi Azure Storage. Następnie możesz odwoływać się do danych przechowywanych na koncie magazynu.

![Zrzut ekranu przedstawiający okno dialogowe Dodawanie źródła danych](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

W programie Visual Studio można odczytywać dane z usługi BLOB Storage, manipulować danymi, funkcjami inżynierów i wysyłać dane uzyskane do Azure Data Lake lub magazynu obiektów blob platformy Azure. Podczas odwoływania się do danych w usłudze BLOB Storage Użyj **wasb://**. Podczas odwoływania się do danych w Azure Data Lake Użyj **swbhdfs://**.

W programie Visual Studio można używać następujących zapytań U-SQL:

```usql
@a =
    EXTRACT medallion string,
            hack_license string,
            vendor_id string,
            rate_code string,
            store_and_fwd_flag string,
            pickup_datetime string,
            dropoff_datetime string,
            passenger_count int,
            trip_time_in_secs double,
            trip_distance double,
            pickup_longitude string,
            pickup_latitude string,
            dropoff_longitude string,
            dropoff_latitude string

    FROM "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Input Data File Name>"
    USING Extractors.Csv();

@b =
    SELECT vendor_id,
    COUNT(medallion) AS cnt_medallion,
    SUM(passenger_count) AS cnt_passenger,
    AVG(trip_distance) AS avg_trip_dist,
    MIN(trip_distance) AS min_trip_dist,
    MAX(trip_distance) AS max_trip_dist,
    AVG(trip_time_in_secs) AS avg_trip_time
    FROM @a
    GROUP BY vendor_id;

OUTPUT @b   
TO "swebhdfs://<Azure Data Lake Storage Account Name>.azuredatalakestore.net/<Folder Name>/<Output Data File Name>"
USING Outputters.Csv();

OUTPUT @b   
TO "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Output Data File Name>"
USING Outputters.Csv();
```

Po przesłaniu zapytania do serwera na diagramie zostanie wyświetlony stan Twojego zadania.

![Zrzut ekranu przedstawiający Diagram stanu zadania](./media/vm-do-ten-things/USQL_Job_Status.PNG)

#### <a name="query-data-in-data-lake-u-sql"></a>Wykonywanie zapytań dotyczących danych w Data Lake: U-SQL

Po pozyskaniu zestawu danych w Azure Data Lake można użyć [języka U-SQL](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) do wykonywania zapytań i eksplorowania danych. Język U-SQL jest podobny do T-SQL, ale łączy niektóre funkcje języka C#, aby użytkownicy mogli pisać niestandardowe moduły i funkcje zdefiniowane przez użytkownika. Możesz użyć skryptów w poprzednim kroku.

Po przesłaniu zapytania do serwera tripdata_summary. Plik CSV jest wyświetlany w Eksploratorze Azure Data Lake. Możesz wyświetlić podgląd danych, klikając plik prawym przyciskiem myszy.

![Zrzut ekranu przedstawiający plik CSV w Eksploratorze Data Lake](./media/vm-do-ten-things/USQL_create_summary.png)

Zostaną wyświetlone informacje o pliku:

![Zrzut ekranu przedstawiający informacje podsumowujące pliku](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>Klastry usługi HDInsight Hadoop
Azure HDInsight to zarządzana usługa Apache Hadoop, Spark, HBase i burza w chmurze. Możesz łatwo współpracować z klastrami usługi Azure HDInsight z poziomu Data Science Virtual Machine.

#### <a name="prerequisites"></a>Wymagania wstępne

* Utwórz konto usługi Azure Blob Storage na podstawie [Azure Portal](https://portal.azure.com). To konto magazynu jest używane do przechowywania danych dla klastrów usługi HDInsight.

   ![Zrzut ekranu przedstawiający tworzenie konta magazynu na podstawie Azure Portal](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Dostosuj klastry Azure HDInsight Hadoop z [Azure Portal](../team-data-science-process/customize-hadoop-cluster.md).
  
   Połącz konto magazynu utworzone przy użyciu klastra usługi HDInsight podczas jego tworzenia. To konto magazynu służy do uzyskiwania dostępu do danych, które mogą być przetwarzane w klastrze.

   ![Wybory dotyczące łączenia konta magazynu utworzonego za pomocą klastra usługi HDInsight](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* Włącz Pulpit zdalny dostęp do węzła głównego klastra po jego utworzeniu. Zapamiętaj poświadczenia dostępu zdalnego określone w tym miejscu, ponieważ będą one potrzebne w dalszej procedurze.

   ![Przycisk Pulpit zdalny na potrzeby włączania dostępu zdalnego do klastra usługi HDInsight](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Utwórz obszar roboczy Azure Machine Learning. Eksperymenty Machine Learning są przechowywane w tym Machine Learning obszarze roboczym. Wybierz wyróżnione opcje w portalu, jak pokazano na poniższym zrzucie ekranu:

   ![Tworzenie obszaru roboczego Azure Machine Learning](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Wprowadź parametry dla obszaru roboczego.

   ![Wprowadź Machine Learning parametry obszaru roboczego](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Przekazywanie danych za pomocą notesu IPython. Zaimportuj wymagane pakiety, podłącz poświadczenia, Utwórz bazę danych na koncie magazynu, a następnie Załaduj dane do klastrów HDI.

```python
# Import required packages
import pyodbc
import time as time
import json
import os
import urllib
import urllib2
import warnings
import re
import pandas as pd
import matplotlib.pyplot as plt
from azure.storage.blob import BlobService
warnings.filterwarnings("ignore", category=UserWarning, module='urllib2')


# Create the connection to Hive by using ODBC
SERVER_NAME = 'xxx.azurehdinsight.net'
DATABASE_NAME = 'nyctaxidb'
USERID = 'xxx'
PASSWORD = 'xxxx'
DB_DRIVER = 'Microsoft Hive ODBC Driver'
driver = 'DRIVER={' + DB_DRIVER + '}'
server = 'Host=' + SERVER_NAME + ';Port=443'
database = 'Schema=' + DATABASE_NAME
hiveserv = 'HiveServerType=2'
auth = 'AuthMech=6'
uid = 'UID=' + USERID
pwd = 'PWD=' + PASSWORD
CONNECTION_STRING = ';'.join(
    [driver, server, database, hiveserv, auth, uid, pwd])
connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
cursor = connection.cursor()


# Create the Hive database and tables
queryString = "create database if not exists nyctaxidb;"
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.trip
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    rate_code string,
                    store_and_fwd_flag string,
                    pickup_datetime string,
                    dropoff_datetime string,
                    passenger_count int,
                    trip_time_in_secs double,
                    trip_distance double,
                    pickup_longitude double,
                    pickup_latitude double,
                    dropoff_longitude double,
                    dropoff_latitude double)  
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.fare
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    pickup_datetime string,
                    payment_type string,
                    fare_amount double,
                    surcharge double,
                    mta_tax double,
                    tip_amount double,
                    tolls_amount double,
                    total_amount double)
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)


# Upload data from Blob storage to an HDI cluster
for i in range(1, 13):
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
```

Możesz również wykonać czynności opisane w [tym instruktażu](../team-data-science-process/hive-walkthrough.md) , aby przekazać dane dotyczące NYC z taksówkami do klastra HDI. Główne kroki obejmują:
  
* Użyj AzCopy, aby pobrać spakowane CSV z publicznego obiektu BLOB do folderu lokalnego.
* Użyj AzCopy, aby przekazać niespakowane CSV z folderu lokalnego do klastra HDI.
* Zaloguj się do węzła głównego klastra usługi Hadoop i przygotuj się do analizy danych w postaci poznawczej.

Po załadowaniu danych do klastra HDI można sprawdzić dane w Eksplorator usługi Azure Storage. I baza danych nyctaxidb została utworzona w klastrze HDI.

#### <a name="data-exploration-hive-queries-in-python"></a>Eksploracja danych: zapytania programu Hive w języku Python

Ponieważ dane są w klastrze usługi Hadoop, można użyć pakietu moduł pyodbc do łączenia się z klastrami Hadoop i bazami danych zapytań za pomocą programu Hive w celu eksplorowania i tworzenia funkcji. Można wyświetlić istniejące tabele utworzone w kroku wymagań wstępnych.

```python
queryString = """
    show tables in nyctaxidb2;
    """
pd.read_sql(queryString, connection)
```

![Wyświetl istniejące tabele](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Przyjrzyjmy się liczbie rekordów w każdym miesiącu i częstotliwościach przerzucanych lub nieznajdujących się w tabeli podróży:

```python
queryString = """
    select month, count(*) from nyctaxidb.trip group by month;
    """
results = pd.read_sql(queryString,connection)

%matplotlib inline

results.columns = ['month', 'trip_count']
df = results.copy()
df.index = df['month']
df['trip_count'].plot(kind='bar')
```

![Wykres liczby rekordów w każdym miesiącu](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

```python
queryString = """
    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;
    """
results = pd.read_sql(queryString, connection)

results.columns = ['tipped', 'trip_count']
df = results.copy()
df.index = df['tipped']
df['trip_count'].plot(kind='bar')
```

![Wykres częstotliwości etykiet](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Możesz również obliczyć odległość między lokalizacją odbioru i lokalizacją docelową, a następnie porównać ją z odległością podróży.

```python
queryString = """
                select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
                    3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
                    *radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
                    *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
                    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
                    +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*
                    pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance
                    from nyctaxidb.trip
                    where month=1
                        and pickup_longitude between -90 and -30
                        and pickup_latitude between 30 and 90
                        and dropoff_longitude between -90 and -30
                        and dropoff_latitude between 30 and 90;
            """
results = pd.read_sql(queryString, connection)
results.head(5)
```

![Najważniejsze wiersze tabeli pobrania i listy rozwijanej](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

```python
results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                   'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
df = results.loc[results['trip_distance'] <= 100]  # remove outliers
df = df.loc[df['direct_distance'] <= 100]  # remove outliers
plt.scatter(df['direct_distance'], df['trip_distance'])
```

![Wykres odległości odbioru/upuszczania do drogi podróży](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Teraz Przygotujmy przepróbkowany (1 procent) zestaw danych do modelowania. Możesz użyć tych danych w module czytnika Machine Learning.

```python
queryString = """
create  table if not exists nyctaxi_downsampled_dataset_testNEW (
medallion string,
hack_license string,
vendor_id string,
rate_code string,
store_and_fwd_flag string,
pickup_datetime string,
dropoff_datetime string,
pickup_hour string,
pickup_week string,
weekday string,
passenger_count int,
trip_time_in_secs double,
trip_distance double,
pickup_longitude double,
pickup_latitude double,
dropoff_longitude double,
dropoff_latitude double,
direct_distance double,
payment_type string,
fare_amount double,
surcharge double,
mta_tax double,
tip_amount double,
tolls_amount double,
total_amount double,
tipped string,
tip_class string
)
row format delimited fields terminated by ','
lines terminated by '\\n'
stored as textfile;
"""
cursor.execute(queryString)
```

Teraz Wstaw zawartość sprzężenia do poprzedniej tabeli wewnętrznej.

```python
queryString = """
insert overwrite table nyctaxi_downsampled_dataset_testNEW
select
t.medallion,
t.hack_license,
t.vendor_id,
t.rate_code,
t.store_and_fwd_flag,
t.pickup_datetime,
t.dropoff_datetime,
hour(t.pickup_datetime) as pickup_hour,
weekofyear(t.pickup_datetime) as pickup_week,
from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
t.passenger_count,
t.trip_time_in_secs,
t.trip_distance,
t.pickup_longitude,
t.pickup_latitude,
t.dropoff_longitude,
t.dropoff_latitude,
t.direct_distance,
f.payment_type,
f.fare_amount,
f.surcharge,
f.mta_tax,
f.tip_amount,
f.tolls_amount,
f.total_amount,
if(tip_amount>0,1,0) as tipped,
if(tip_amount=0,0,
if(tip_amount>0 and tip_amount<=5,1,
if(tip_amount>5 and tip_amount<=10,2,
if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
from
(
select
medallion,
hack_license,
vendor_id,
rate_code,
store_and_fwd_flag,
pickup_datetime,
dropoff_datetime,
passenger_count,
trip_time_in_secs,
trip_distance,
pickup_longitude,
pickup_latitude,
dropoff_longitude,
dropoff_latitude,
3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
+cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance,
rand() as sample_key

from trip
where pickup_latitude between 30 and 90
    and pickup_longitude between -90 and -30
    and dropoff_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
)t
join
(
select
medallion,
hack_license,
vendor_id,
pickup_datetime,
payment_type,
fare_amount,
surcharge,
mta_tax,
tip_amount,
tolls_amount,
total_amount
from fare
)f
on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
where t.sample_key<=0.01
"""
cursor.execute(queryString)
```

Po czasie możesz zobaczyć, że dane zostały załadowane w klastrach Hadoop:

```python
queryString = """
    select * from nyctaxi_downsampled_dataset limit 10;
    """
cursor.execute(queryString)
pd.read_sql(queryString, connection)
```

![Najważniejsze wiersze danych z tabeli](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

### <a name="azure-sql-data-warehouse-and-databases"></a>Azure SQL Data Warehouse i bazy danych
Azure SQL Data Warehouse to elastyczny magazyn danych jako usługa z obsługą SQL Server klasy korporacyjnej.

Możesz zainicjować obsługę administracyjną usługi Azure SQL Data Warehouse, postępując zgodnie z instrukcjami w [tym artykule](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). Po zainicjowaniu obsługi administracyjnej usługi SQL Data Warehouse można użyć [tego przewodnika](../team-data-science-process/sqldw-walkthrough.md) do przekazywania danych, eksploracji i modelowania przy użyciu danych w usłudze SQL Data Warehouse.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB to baza danych NoSQL w chmurze. Można jej używać do pracy z dokumentami, takimi jak JSON, oraz do przechowywania i wykonywania zapytań dotyczących dokumentów.

Aby uzyskać dostęp do Azure Cosmos DB z DSVM, należy wykonać następujące czynności wstępne:

1. Azure Cosmos DB Python SDK jest już zainstalowany na DSVM. Aby go zaktualizować, uruchom ```pip install pydocumentdb --upgrade``` polecenie w wierszu polecenia.
2. Utwórz konto Azure Cosmos DB i bazę danych z [Azure Portal](https://portal.azure.com).
3. Pobierz narzędzie do migracji danych Azure Cosmos DB z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=53595) i Wyodrębnij je do wybranego katalogu.
4. Importuj dane JSON (Volcano Data) przechowywane w [publicznym obiekcie blob](https://https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json) do Azure Cosmos DB z następującymi parametrami polecenia do narzędzia migracji. (Użyj programu dtui. exe z katalogu, w którym zainstalowano narzędzie do migracji danych Azure Cosmos DB). Wprowadź lokalizację źródłową i docelową z następującymi parametrami:
   
    `/s:JsonFile /s.Files:https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

Po zaimportowaniu danych możesz przejść do Jupyter i otworzyć Notes zatytułowany *DocumentDBSample*. Zawiera kod języka Python umożliwiający dostęp do Azure Cosmos DB i wykonywanie podstawowych zapytań. Więcej informacji na temat Azure Cosmos DB można uzyskać, odwiedzając [stronę dokumentacji](https://docs.microsoft.com/azure/cosmos-db/)usługi.

## <a name="use-power-bi-reports-and-dashboards"></a>Korzystanie z Power BI raportów i pulpitów nawigacyjnych 
Możesz wizualizować plik JSON Volcano z powyższego przykładu Azure Cosmos DB w Power BI Desktop, aby uzyskać wizualny wgląd w dane. Szczegółowe kroki są dostępne w [artykule Power BI](../../cosmos-db/powerbi-visualize.md). Poniżej przedstawiono ogólne czynności:

1. Otwórz program Power BI Desktop i wybierz pozycję **Pobierz dane**. Określ adres URL jako: `https://cahandson.blob.core.windows.net/samples/volcano.json`.
2. Powinny zostać wyświetlone rekordy JSON zaimportowane jako lista. Przekonwertuj listę na tabelę, aby Power BI mogła z nią korzystać.
4. Rozwiń kolumny, wybierając ikonę rozwijania (strzałka).
5. Zauważ, że lokalizacja jest polem **rekordu** . Rozwiń rekord i wybierz tylko współrzędne. **Koordynuje** to kolumna listy.
6. Dodaj nową kolumnę, aby przekonwertować kolumnę współrzędnej listy na kolumnę **LatLong** rozdzieloną przecinkami. Połącz dwa elementy w polu listy współrzędnych przy użyciu formuły ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```.
7. Przekonwertuj kolumnę **podniesienia uprawnień** na wartość dziesiętną i wybierz przyciski **Zamknij** i **Zastosuj** .

Zamiast kroków poprzedzających, można wkleić poniższy kod. Skryptuje kroki używane w Edytor zaawansowany w Power BI, aby zapisać przekształcenia danych w języku zapytań.

```pqfl
let
    Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
    #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
    #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
    #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
    #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
in
    #"Changed Type"
```

Masz teraz dane w modelu danych Power BI. Wystąpienie Power BI Desktop powinno wyglądać następująco:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Możesz rozpocząć tworzenie raportów i wizualizacji przy użyciu modelu danych. Aby utworzyć raport, można wykonać kroki opisane w [tym artykule Power BI](../../cosmos-db/powerbi-visualize.md#build-the-reports) .

## <a name="scale-the-dsvm-dynamically"></a>Dynamiczne skalowanie DSVM 
Możesz skalować w górę i w dół, aby sprostać potrzebom projektu. Jeśli nie musisz używać maszyny wirtualnej w wieczór lub w weekendy, możesz wyłączyć maszynę wirtualną z poziomu [Azure Portal](https://portal.azure.com).

> [!NOTE]
> Opłaty za obliczenia są naliczane, jeśli dla systemu operacyjnego na maszynie wirtualnej jest używany tylko przycisk Zamknij.  
> 
> 

Może być konieczne obsłużenie pewnej analizy na dużą skalę i konieczności użycia procesora CPU, pamięci lub pojemności dysku. W takim przypadku można znaleźć wybrane rozmiary maszyn wirtualnych pod względem rdzeni procesora, wystąpień opartych na procesorze GPU do uczenia głębokiego, pojemności pamięci i typów dysków (w tym dysków SSD), które spełniają wymagania obliczeniowe i budżetowe. Pełna lista maszyn wirtualnych wraz z cenami obliczeń godzinowych jest dostępna na stronie [cennika usługi Azure Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/) .

Analogicznie, zapotrzebowanie na wydajność przetwarzania maszyn wirtualnych może się zmniejszyć. (Na przykład: przeniesiono duże obciążenie do klastra Hadoop lub Spark). Następnie można przeskalować klaster z [Azure Portal](https://portal.azure.com) i przejść do ustawień wystąpienia maszyny wirtualnej. 

## <a name="add-more-tools"></a>Dodaj więcej narzędzi
Narzędzia wbudowane w DSVM mogą dotyczyć wielu typowych potrzeb związanych z analizą danych. Pozwala to zaoszczędzić czas, ponieważ nie trzeba instalować i konfigurować swoich środowisk po jednym. Pozwala to również zaoszczędzić pieniądze, ponieważ płacisz tylko za zasoby, których używasz.

Aby ulepszyć środowisko analityczne, można użyć innych usług Azure Data and Analytics w tym artykule. W niektórych przypadkach mogą być potrzebne dodatkowe narzędzia, w tym niektóre własnościowe narzędzia partnerskie. Na maszynie wirtualnej masz pełny dostęp administracyjny, aby zainstalować potrzebne nowe narzędzia. Można także zainstalować dodatkowe pakiety w języku Python i języku R, które nie są wstępnie zainstalowane. W przypadku języka Python można użyć jednego ```conda``` lub ```pip```. W przypadku języka r możesz użyć ```install.packages()``` programu w konsoli języka r lub użyć środowiska IDE, a następnie wybrać pozycję **pakiety** > **Zainstaluj pakiety**.

## <a name="deep-learning"></a>Uczenie głębokie

Oprócz przykładów opartych na architekturze można uzyskać zestaw obszernych instruktaży, które zostały zweryfikowane w DSVM. Te przewodniki ułatwiają szybkie rozpoczęcie opracowywania aplikacji głębokiego uczenia w domenach, takich jak zrozumienie obrazu i tekstu/języka.   


- [Uruchamianie sieci neuronowych w różnych strukturach](https://github.com/ilkarman/DeepLearningFrameworks): w tym przewodniku pokazano, jak migrować kod z jednej struktury do innej. Przedstawiono w nim również sposób porównywania modeli i wydajności środowiska uruchomieniowego w różnych strukturach. 

- Przewodnik, w którym można [utworzyć kompleksowe rozwiązanie wykrywające produkty w obrazach](https://github.com/Azure/cortana-intelligence-product-detection-from-images): wykrywanie obrazów to technika, która umożliwia lokalizowanie i klasyfikowanie obiektów w obrazach. Ta technologia ma potencjalne znaczenie w wielu domenach firmowych w czasie rzeczywistym. Na przykład Detaliści mogą użyć tej techniki, aby określić, który produkt został pobrany przez klienta z półki. Te informacje z kolei ułatwiają przechowywanie spisu produktów. 

- [Głębokie uczenie audio](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): w tym samouczku pokazano, jak przeszkolić model głębokiego uczenia na potrzeby wykrywania zdarzeń audio w [zestawie danych "dźwięki miejskie](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html)". Zawiera również omówienie sposobu pracy z danymi audio.

- [Klasyfikacja dokumentów tekstowych](https://github.com/anargyri/lstm_han): w tym instruktażu pokazano, jak tworzyć i przeszkolić dwie architektury sieci neuronowych: hierarchiczną sieć uwagi i długą pamięć krótkoterminową (LSTM). Te sieci neuronowych używają interfejsu API Keras do uczenia głębokiego do klasyfikowania dokumentów tekstowych. Keras to fronton z trzema najpopularniejszymi platformami głębokiego uczenia: Microsoft Cognitive Toolkit, TensorFlow i Theano.

## <a name="summary"></a>Podsumowanie
W tym artykule opisano niektóre czynności, które można wykonać w Data Science Virtual Machine firmy Microsoft. Istnieje wiele zadań, które można wykonać, aby DSVM wydajne środowisko analityczne.

