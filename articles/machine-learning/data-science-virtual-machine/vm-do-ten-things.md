---
title: Eksplorowanie danych i modelu w systemie Windows
titleSuffix: Azure Data Science Virtual Machine
description: Wykonywanie zadań eksploracji i modelowania danych na maszynie wirtualnej nauki o danych systemu Windows.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 0f42f075f5d3be4486157334403bfa7d3f1aa80c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682877"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>Dziesięć czynności, które można wykonać na maszynie wirtualnej do nauki o danych systemu Windows

Maszyna wirtualna do nauki o danych systemu Windows (DSVM) to zaawansowane środowisko programistyczne do nauki o danych, w którym można wykonywać zadania eksploracji i modelowania danych. Środowisko jest już skonstowanym i powiązanym z kilkoma popularnymi narzędziami do analizy danych, które ułatwiają rozpoczęcie analizy dla wdrożeń lokalnych, chmurowych lub hybrydowych. 

DSVM ściśle współpracuje z usługami platformy Azure. Może odczytywać i przetwarzać dane, które są już przechowywane na platformie Azure, w usłudze Azure SQL Data Warehouse, usłudze Azure Data Lake, usłudze Azure Storage lub usłudze Azure Cosmos DB. Może również korzystać z innych narzędzi analitycznych, takich jak azure machine learning i usługa Azure Data Factory.

W tym artykule dowiesz się, jak używać dsvm do wykonywania zadań nauki o danych i interakcji z innymi usługami platformy Azure. Oto kilka rzeczy, które można zrobić na DSVM:

- Eksploruj dane i twórz modele lokalnie na maszynie DSVM przy użyciu programu Microsoft Machine Learning Server i Python.
- Użyj notesu Jupyter do eksperymentowania z danymi w przeglądarce przy użyciu języków Python 2, Python 3 i Microsoft R. (Microsoft R to wersja języka R przeznaczona dla przedsiębiorstwa przeznaczona do działania).
- Wdrażanie modeli utworzonych za pomocą języka R i Python w usłudze Azure Machine Learning, dzięki czemu aplikacje klienckie mogą uzyskiwać dostęp do modeli przy użyciu prostego interfejsu usługi sieci web.
- Administrowanie zasobami platformy Azure przy użyciu witryny Azure portal lub programu PowerShell.
- Rozszerz miejsce do magazynowania i udostępnij zestawy danych na dużą skalę/kod w całym zespole, tworząc udział usługi Azure Files jako dysk do instalacji na modelu DSVM.
- Udostępnij kod swojemu zespołowi za pomocą gitHub. Uzyskaj dostęp do repozytorium za pomocą wstępnie zainstalowanych klientów Git: Git Bash i Git GUI.
- Uzyskaj dostęp do usług danych i analiz platformy Azure, takich jak magazyn obiektów Blob platformy Azure, usługa Azure Data Lake, usługa Azure HDInsight (Hadoop), usługa Azure Cosmos DB, usługa Azure SQL Data Warehouse i baza danych SQL Azure.
- Twórz raporty i pulpit nawigacyjny przy użyciu wystąpienia programu Power BI Desktop, które jest wstępnie zainstalowane w systemie DSVM, i wdrażaj je w chmurze.
- Dynamicznie skaluj dsvm, aby spełnić potrzeby twojego projektu.
- Zainstaluj dodatkowe narzędzia na maszynie wirtualnej.   

> [!NOTE]
> W przypadku wielu usług przechowywania i analiz danych wymienionych w tym artykule obowiązują dodatkowe opłaty za użycie. Aby uzyskać szczegółowe informacje, zobacz stronę [cennika platformy Azure.](https://azure.microsoft.com/pricing/)
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne

* Potrzebujesz subskrypcji platformy Azure. Możesz [zapisać się na bezpłatną wersję próbną](https://azure.microsoft.com/free/).
* Instrukcje dotyczące inicjowania obsługi administracyjnej maszyny wirtualnej do nauki o danych w witrynie Azure portal są dostępne w [tworzeniu maszyny wirtualnej.](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Eksplorowanie danych i tworzenie modeli za pomocą serwera microsoft machine learning Server
Możesz używać języków takich jak R i Python, aby wykonać analizę danych bezpośrednio na dsvm.

W przypadku R można użyć IDE, takiego jak RStudio, który można znaleźć w menu Start lub na pulpicie. Można też użyć narzędzia języka R dla programu Visual Studio. Firma Microsoft dostarczyła dodatkowe biblioteki na szczycie open source CRAN R, aby umożliwić skalowalną analizę i możliwość analizowania danych większych niż rozmiar pamięci dozwolony w równoległej analizie fragmentarycznej. 

W języku Python można użyć ide, takich jak Visual Studio Community Edition, który ma wstępnie zainstalowane rozszerzenie Python Tools for Visual Studio (PTVS). Domyślnie tylko Python 3.6, główne środowisko Conda, jest skonfigurowany na PTVS. Aby włączyć Anaconda Python 2.7, należy wykonać następujące kroki:

1. Tworzenie środowisk niestandardowych dla każdej wersji, przechodząc do **Narzędzia** > **Python Tools** > **Python Środowiska**, a następnie wybierając + **Niestandardowe** w programie Visual Studio Community Edition.
1. Opis i ustaw ścieżkę prefiksu środowiska jako **c:\anaconda\envs\python2** dla Anaconda Python 2.7.
1. Wybierz **opcję Automatyczne wykrywanie** > **Zastosuj,** aby uratować środowisko.

Zobacz [dokumentację PTVS,](https://aka.ms/ptvsdocs) aby uzyskać więcej informacji na temat tworzenia środowisk języka Python.

Teraz masz skonfigurować tworzenie nowego projektu Pythona. Przejdź do **file** > **new** > **project** > **python** i wybierz typ aplikacji Python, którą budujesz. Środowisko Języka Python dla bieżącego projektu można ustawić na żądaną wersję (Python 2.7 lub 3.6), klikając prawym przyciskiem myszy **środowiska Języka Python,** a następnie wybierając **pozycję Dodaj/Usuń środowiska Pythona**. Więcej informacji na temat pracy z PTVS można znaleźć w [dokumentacji produktu](https://aka.ms/ptvsdocs).

## <a name="use-jupyter-notebooks"></a>Korzystanie z notesów programu Jupyter
Notes Jupyter udostępnia ide oparty na przeglądarce do eksploracji i modelowania danych. Można używać python 2, Python 3 lub R (zarówno open source i Microsoft R Server) w notesie Jupyter.

Aby uruchomić notes Jupyter, wybierz ikonę **Notesu Jupyter** w menu **Start** lub na pulpicie. W wierszu polecenia DSVM można również ```jupyter notebook``` uruchomić polecenie z katalogu, w którym znajdują się istniejące notesy lub w którym mają zostać utworzone nowe notesy.  

Po uruchomieniu programu Jupyter powinien zostać wyświetlony katalog zawierający kilka przykładowych notesów, które są wstępnie spakowane do dsvm. Co możesz teraz zrobić:

* Wybierz notes, aby wyświetlić kod.
* Uruchom każdą komórkę, zaznaczając klawisze Shift+Enter.
* Uruchom cały notes, wybierając pozycję **Cell** > **Run**.
* Utwórz nowy notes, wybierając ikonę Jupyter (lewy górny róg), wybierając przycisk **Nowy** po prawej stronie, a następnie wybierając język notesu (znany również jako jądra).   

> [!NOTE]
> Obecnie obsługiwane są jądra Python 2.7, Python 3.6, R, Julia i PySpark w Jupyter. Jądro R obsługuje programowanie zarówno w open source R, jak i Microsoft R.   
> 
> 

Gdy jesteś w notesie, możesz eksplorować dane, tworzyć model i testować model przy użyciu bibliotek.

## <a name="train-and-deploy-models-by-using-azure-machine-learning"></a>Szkolenie i wdrażanie modeli przy użyciu usługi Azure Machine Learning
Po smołowej i zweryfikowanej modelu następnym krokiem jest zwykle wdrożenie go w produkcji. Ten krok umożliwia aplikacjom klienckim wywoływanie prognoz modelu w czasie rzeczywistym lub w trybie wsadowym. Usługa Azure Machine Learning udostępnia mechanizm do operacjonalizacji modelu wbudowanego w języku R lub Python.

Podczas operacjonalizacji modelu w usłudze Azure Machine Learning usługa sieci web jest narażona. Umożliwia klientom wykonywanie wywołań REST, które przekazują parametry wejściowe i odbierają prognozy z modelu jako wyjścia.

### <a name="build-and-operationalize-python-models"></a>Tworzenie i operacjonalizacja modeli języka Python
Oto fragment kodu opracowany w notesie Python Jupyter, który tworzy prosty model przy użyciu biblioteki Scikit-learn:

```python
# IRIS classification
from sklearn import datasets
from sklearn import svm
clf = svm.SVC()
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
```

Metoda, która jest używana do wdrażania modeli języka Python do usługi Azure Machine Learning zawija przewidywanie modelu do funkcji i ozdabia go atrybutami dostarczonymi przez wstępnie zainstalowaną bibliotekę usługi Azure Machine Learning Python. Atrybuty oznaczają identyfikator obszaru roboczego usługi Azure Machine Learning, klucz interfejsu API oraz parametry danych wejściowych i zwracania.  

```python
from azureml import services
@services.publish(workspaceid, auth_token)
@services.types(sep_l=float, sep_w=float, pet_l=float, pet_w=float)
@services.returns(int)  # 0, or 1, or 2
def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]


return clf.predict(inputArray)
```

Klient może teraz nawiązywać połączenia z usługą sieci web. Otoki wygody konstruowania żądań interfejsu API REST. Oto przykładowy kod do korzystania z usługi sieci web:

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
> Obecnie biblioteka usługi Azure Machine Learning jest obsługiwana tylko w języku Python 2.7.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>Tworzenie i operacjonalizacja modeli R
Modele języka R utworzone na maszynie wirtualnej do nauki o danych lub w innym miejscu można wdrożyć w usłudze Azure Machine Learning w sposób podobny do tego, jak to się robi dla języka Python. Oto konkretne kroki:

1. Utwórz plik settings.json, aby podać identyfikator obszaru roboczego i token uwierzytelniania. 
2. Napisz otokę dla funkcji przewidywania modelu.
3. Wywołanie ```publishWebService``` w bibliotece usługi Azure Machine Learning, aby przekazać w otoce funkcji.  

Poniższa procedura i fragmenty kodu służy do konfigurowania, tworzenia, publikowania i używania modelu jako usługi sieci web w usłudze Azure Machine Learning.

#### <a name="set-up"></a>Konfiguruj

Utwórz plik settings.json w ```.azureml``` katalogu wywoływanym w katalogu domowym. Wprowadź parametry z obszaru roboczego usługi Azure Machine Learning.

Oto struktura pliku settings.json:

```json
{"workspace":{
"id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
"authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
}}
```

#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>Tworzenie modelu w językach R i publikowanie go w usłudze Azure Machine Learning

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

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Korzystanie z modelu wdrożonego w usłudze Azure Machine Learning
Aby korzystać z modelu z aplikacji klienckiej, użyj biblioteki usługi Azure Machine Learning, aby wyszukać opublikowaną usługę sieci web według nazwy. Użyj `services` wywołania interfejsu API, aby określić punkt końcowy. Następnie wystarczy wywołać `consume` funkcję i przekazać w ramce danych do przewidzenia.

Użyj następującego kodu, aby korzystać z modelu opublikowanego jako usługa sieci Web usługi Azure Machine Learning:

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

Zobacz więcej informacji o [pakietach języka R w umiań machine learningu](/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning).

## <a name="manage-azure-resources"></a>Zarządzanie zasobami platformy Azure
DSVM nie tylko pozwala na tworzenie rozwiązania analitycznego lokalnie na maszynie wirtualnej. Umożliwia również dostęp do usług na platformie Azure cloud. Platforma Azure oferuje kilka usług obliczeniowych, magazynowych, analizy danych i innych usług, które można administrować i uzyskiwać do nich dostęp z systemu DSVM.

Aby administrować subskrypcją platformy Azure i zasobami w chmurze, masz dwie opcje:
+ Użyj przeglądarki i przejdź do [witryny Azure portal](https://portal.azure.com).

+ Użyj skryptów programu PowerShell. Uruchom program Azure PowerShell ze skrótu na pulpicie lub z menu **Start.** Szczegółowe informacje można znaleźć w [dokumentacji programu Microsoft Azure PowerShell.](../../powershell-azure-resource-manager.md) 

## <a name="extend-storage-by-using-shared-file-systems"></a>Rozszerzanie pamięci masowej przy użyciu udostępnionych systemów plików
Analitycy danych mogą udostępniać duże zestawy danych, kod lub inne zasoby w zespole. DSVM ma około 45 GB wolnego miejsca. Aby rozszerzyć magazyn, można użyć usługi Azure Files i zainstalować go na jednym lub kilku wystąpień DSVM lub uzyskać do niego dostęp za pośrednictwem interfejsu API REST. Można również użyć [witryny Azure portal](../../virtual-machines/windows/attach-managed-disk-portal.md) lub użyj programu Azure [PowerShell,](../../virtual-machines/windows/attach-disk-ps.md) aby dodać dodatkowe dedykowane dyski danych. 

> [!NOTE]
> Maksymalna ilość miejsca w udziale usługi Azure Files wynosi 5 TB. Limit rozmiaru dla każdego pliku wynosi 1 TB. 

Tego skryptu w programie Azure PowerShell można użyć do utworzenia udziału usługi Azure Files:

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

Po utworzeniu udziału usługi Azure Files można go zainstalować na dowolnej maszynie wirtualnej na platformie Azure. Zaleca się umieszczenie maszyny Wirtualnej w tym samym centrum danych platformy Azure co konto magazynu, aby uniknąć opóźnień i opłat za transfer danych. Oto polecenia programu Azure PowerShell do zainstalowania dysku na dsvm:

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

Teraz można uzyskać dostęp do tego dysku, jak każdy normalny dysk na maszynie wirtualnej.

## <a name="share-code-in-github"></a>Udostępnij kod w usłudze GitHub
GitHub to repozytorium kodu, w którym można znaleźć przykłady kodu i źródła różnych narzędzi przy użyciu technologii udostępnionych przez społeczność deweloperów. Używa Git jako technologii do śledzenia i przechowywania wersji plików kodu. GitHub to również platforma, na której można utworzyć własne repozytorium do przechowywania udostępnionego kodu i dokumentacji zespołu, implementowania kontroli wersji i kontroli, kto ma dostęp do kodu widoku i współtworzenia. 

Odwiedź [strony pomocy GitHub, aby](https://help.github.com/) uzyskać więcej informacji na temat korzystania z git. GitHub można użyć jako jeden ze sposobów współpracy z zespołem, użyć kodu opracowanego przez społeczność i przyczynić się kod z powrotem do społeczności.

DSVM jest ładowany z narzędzi klienta w wierszu polecenia i w gui, aby uzyskać dostęp do repozytorium GitHub. Narzędzie wiersza polecenia, które współpracuje z Git i GitHub nazywa Git Bash. Visual Studio jest zainstalowany na DSVM i ma rozszerzenia Git. Ikony tych narzędzi można znaleźć w menu **Start** i na pulpicie.

Aby pobrać kod z repozytorium GitHub, należy użyć ```git clone``` polecenia. Na przykład, aby pobrać repozytorium nauki o danych opublikowane przez firmę Microsoft do bieżącego katalogu, można uruchomić następujące polecenie w Git Bash:

    git clone https://github.com/Azure/DataScienceVM.git

W programie Visual Studio można wykonać tę samą operację klonowania. Poniższy zrzut ekranu pokazuje, jak uzyskać dostęp do narzędzi Git i GitHub w programie Visual Studio:

![Zrzut ekranu przedstawiający program Visual Studio z wyświetlonym połączeniem GitHub](./media/vm-do-ten-things/VSGit.PNG)

Więcej informacji na temat korzystania z git do pracy z repozytorium GitHub można znaleźć na podstawie zasobów dostępnych w github.com. [Ściągawka](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) jest użytecznym punktem odniesienia.

## <a name="access-azure-data-and-analytics-services"></a>Dostęp do usług danych i analiz platformy Azure
### <a name="azure-blob-storage"></a>Azure Blob Storage
Usługa Azure Blob storage to niezawodna, ekonomiczna usługa magazynu w chmurze dla dużych i małych danych. W tej sekcji opisano, jak można przenieść dane do magazynu obiektów Blob i uzyskać dostęp do danych przechowywanych w obiekcie blob platformy Azure.

#### <a name="prerequisites"></a>Wymagania wstępne

* Utwórz konto magazynu obiektów Blob platformy Azure z [witryny Azure portal](https://portal.azure.com).

   ![Zrzut ekranu przedstawiający proces tworzenia konta magazynu w witrynie Azure portal](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Upewnij się, że narzędzie AzCopy wiersza ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```polecenia jest wstępnie zainstalowane: . Katalog zawierający plik azcopy.exe znajduje się już w zmiennej środowiskowej PATH, dzięki czemu można uniknąć wpisywania pełnej ścieżki polecenia podczas uruchamiania tego narzędzia. Aby uzyskać więcej informacji na temat narzędzia AzCopy, zobacz [dokumentację AzCopy](../../storage/common/storage-use-azcopy.md).
* Uruchom narzędzie Eksploratora usługi Azure Storage. Można go pobrać ze [strony internetowej Eksploratora magazynu](https://storageexplorer.com/). 

   ![Zrzut ekranu przedstawiający usługę Azure Storage Explorer uzyskującą dostęp do konta magazynu](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>Przenoszenie danych z maszyny Wirtualnej do obiektu blob platformy Azure: AzCopy

Aby przenieść dane między plikami lokalnymi a magazynem obiektów Blob, można użyć programu AzCopy w wierszu polecenia lub w programie PowerShell:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Zastąp **C:\myfolder** ścieżką, w której przechowywany jest plik, **konto mystorageaccount** nazwą konta magazynu obiektów Blob, **mycontainer** z nazwą kontenera i **kluczem konta magazynu** z kluczem dostępu do magazynu obiektów Blob. Poświadczenia konta magazynu można znaleźć w [witrynie Azure portal](https://portal.azure.com).

Uruchom polecenie AzCopy w programie PowerShell lub z wiersza polecenia. Oto przykładowe użycie polecenia AzCopy:

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

Po uruchomieniu polecenia AzCopy do skopiowania do obiektu blob platformy Azure plik pojawi się w Eksploratorze usługi Azure Storage.

![Zrzut ekranu przedstawiający konto magazynu, wyświetlający przekazany plik CSV](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>Przenoszenie danych z maszyny Wirtualnej do obiektu blob platformy Azure: Eksplorator usługi Azure Storage

Można również przekazać dane z pliku lokalnego na maszynie Wirtualnej przy użyciu Eksploratora usługi Azure Storage:

* Aby przekazać dane do kontenera, wybierz kontener docelowy i wybierz przycisk **Przekaż.** ![Zrzut ekranu przedstawiający przycisk przekazywania w Eksploratorze usługi Azure Storage](./media/vm-do-ten-things/storage-accounts.png)
* Wybierz wielokropek (**...**) po prawej stronie pola **Pliki,** wybierz jeden lub wiele plików do przesłania z systemu plików i wybierz **pozycję Przekaż,** aby rozpocząć przesyłanie plików. ![Zrzut ekranu przedstawiający okno dialogowe Przekazywanie plików](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-machine-learning-reader-module"></a>Odczytywanie danych z obiektu blob platformy Azure: moduł czytnika usługi Machine Learning

W usłudze Azure Machine Learning Studio można użyć modułu Importuj dane do odczytu danych z obiektu blob.

![Zrzut ekranu przedstawiający moduł Importuj dane w udiocieli Machine Learning](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Odczytywanie danych z obiektu blob platformy Azure: Python ODBC

Biblioteka blobService służy do odczytywania danych bezpośrednio z obiektu blob w notesie Jupyter lub w programie Python.

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

Następnie podłącz poświadczenia konta magazynu obiektów Blob i odczytuj dane z obiektu blob:

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

![Zrzut ekranu przedstawiający pierwsze 10 wierszy danych](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Usługa Azure Data Lake Storage to repozytorium hiperskali dla obciążeń analizy dużych zbiorów danych i jest zgodne z rozproszonym systemem plików (HDFS) firmy Hadoop. Współpracuje z usługami Hadoop, Spark i Azure Data Lake Analytics. W tej sekcji dowiesz się, jak przenieść dane do usługi Azure Data Lake Storage i uruchomić analizę przy użyciu usługi Azure Data Lake Analytics.

#### <a name="prerequisites"></a>Wymagania wstępne

* Utwórz wystąpienie usługi Azure Data Lake Analytics w [witrynie Azure portal](https://portal.azure.com).

   ![Zrzut ekranu przedstawiający tworzenie wystąpienia usługi Data Lake Analytics z witryny Azure portal](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* [Usługa Azure Data Lake i narzędzia do analizy strumienia dla programu Visual Studio są](https://www.microsoft.com/download/details.aspx?id=49504) już zainstalowane w programie Visual Studio Community Edition na maszynie wirtualnej. Po uruchomieniu programu Visual Studio i zalogowaniu się do subskrypcji platformy Azure powinno zostać wyświetlona konto usługi Azure Data Analytics i magazyn w lewym panelu programu Visual Studio.

   ![Zrzut ekranu przedstawiający wtyczkę do narzędzi usługi Data Lake w programie Visual Studio](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

#### <a name="move-data-from-a-vm-to-data-lake-azure-data-lake-explorer"></a>Przenoszenie danych z maszyny Wirtualnej do usługi Data Lake: Usługa Azure Data Lake Explorer

Za pomocą usługi Azure Data Lake Explorer można [przekazywać dane z plików lokalnych na maszynie wirtualnej do magazynu usługi Data Lake.](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)

Można również utworzyć potok danych, aby operacjonalizacji przenoszenia danych do lub z usługi Azure Data Lake przy użyciu [usługi Azure Data Factory.](https://azure.microsoft.com/services/data-factory/) [W tym artykule](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) poprowadzą Cię przez kroki do tworzenia potoków danych.

#### <a name="read-data-from-an-azure-blob-to-data-lake-u-sql"></a>Odczytywanie danych z obiektu blob platformy Azure do usługi Data Lake: U-SQL

Jeśli dane znajdują się w magazynie obiektów Blob platformy Azure, można bezpośrednio odczytać dane z obiektu blob platformy Azure w kwerendzie U-SQL. Przed skomponowaniem kwerendy U-SQL upewnij się, że konto magazynu obiektów Blob jest połączone z wystąpieniem usługi Azure Data Lake. Przejdź do witryny Azure Portal, znajdź pulpit nawigacyjny usługi Azure Data Lake Analytics, wybierz pozycję **Dodaj źródło danych**, wybierz typ magazynu usługi Azure **Storage**i podłącz nazwę i klucz konta magazynu platformy Azure. Następnie można odwołać się do danych przechowywanych na koncie magazynu.

![Zrzut ekranu przedstawiający okno dialogowe Dodawanie źródła danych](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

W programie Visual Studio można odczytywać dane z magazynu obiektów Blob, manipulować danymi, projektować funkcje i wysyłać wynikowe dane do usługi Azure Data Lake lub magazynu obiektów blob platformy Azure. Podczas odwoływania się do danych w magazynie obiektów Blob należy użyć **wasb://**. W przypadku odwoływania się do danych w usłudze Azure Data Lake należy użyć **swbhdfs://**.

W programie Visual Studio można użyć następujących zapytań U-SQL:

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

Po przesłaniu zapytania do serwera diagram pokazuje stan zadania.

![Zrzut ekranu przedstawiający diagram stanu zadania](./media/vm-do-ten-things/USQL_Job_Status.PNG)

#### <a name="query-data-in-data-lake-u-sql"></a>Dane kwerendy w umiasne: U-SQL

Po wynajęciu zestawu danych w usłudze Azure Data Lake można użyć [języka U-SQL](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) do wykonywania zapytań i eksplorowania danych. Język U-SQL jest podobny do języka T-SQL, ale łączy w sobie niektóre funkcje z języka C#, dzięki czemu użytkownicy mogą pisać dostosowane moduły i funkcje zdefiniowane przez użytkownika. Skryptów można używać w poprzednim kroku.

Po przesłaniu zapytania do serwera tripdata_summary. Plik CSV pojawia się w Eksploratorze usługi Azure Data Lake. Możesz wyświetlić podgląd danych, klikając plik prawym przyciskiem myszy.

![Zrzut ekranu przedstawiający plik CSV w eksploratorze usługi Data Lake](./media/vm-do-ten-things/USQL_create_summary.png)

Pojawią się informacje o pliku:

![Zrzut ekranu przedstawiający informacje podsumowujące plik](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>Klastry HDInsight Hadoop
Usługa Azure HDInsight to zarządzana usługa Apache Hadoop, Spark, HBase i Storm w chmurze. Możesz łatwo pracować z klastrami usługi Azure HDInsight z maszyny wirtualnej do nauki o danych.

#### <a name="prerequisites"></a>Wymagania wstępne

* Utwórz konto magazynu obiektów Blob platformy Azure z [witryny Azure portal](https://portal.azure.com). To konto magazynu służy do przechowywania danych dla klastrów HDInsight.

   ![Zrzut ekranu przedstawiający tworzenie konta magazynu w witrynie Azure portal](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Dostosuj klastry usługi Azure HDInsight Hadoop z [witryny Azure portal](../team-data-science-process/customize-hadoop-cluster.md).
  
   Połącz konto magazynu utworzone z klastrem HDInsight podczas jego tworzenia. To konto magazynu służy do uzyskiwania dostępu do danych, które mogą być przetwarzane w klastrze.

   ![Wybór do łączenia konta magazynu utworzonego za pomocą klastra HDInsight](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* Włącz dostęp pulpitu zdalnego do węzła głównego klastra po jego utworzeniu. Zapamiętaj poświadczenia dostępu zdalnego, które określisz w tym miejscu, ponieważ będą one potrzebne w kolejnej procedurze.

   ![Przycisk Pulpit zdalny umożliwiający zdalny dostęp do klastra USŁUGI HDInsight](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Tworzenie obszaru roboczego usługi Azure Machine Learning. Eksperymenty uczenia maszynowego są przechowywane w tym obszarze roboczym uczenia maszynowego. Wybierz wyróżnione opcje w portalu, jak pokazano na poniższym zrzucie ekranu:

   ![Tworzenie obszaru roboczego usługi Azure Machine Learning](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Wprowadź parametry obszaru roboczego.

   ![Wprowadzanie parametrów obszaru roboczego uczenia maszynowego](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Przekaż dane za pomocą notebooka IPython. Importuj wymagane pakiety, podłącz poświadczenia, utwórz bazę danych na koncie magazynu, a następnie załaduj dane do klastrów HDI.

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

Alternatywnie można wykonać [ten przewodnik,](../team-data-science-process/hive-walkthrough.md) aby przekazać dane taksówki NYC do klastra HDI. Główne kroki obejmują:
  
* Użyj AzCopy, aby pobrać spakowane csv z publicznego obiektu blob do folderu lokalnego.
* Użyj AzCopy, aby przesłać rozpakowane csv z folderu lokalnego do klastra HDI.
* Zaloguj się do węzła głównego klastra Hadoop i przygotuj się do analizy danych odkrywczych.

Po załadowaniu danych do klastra HDI można sprawdzić dane w Eksploratorze usługi Azure Storage. Baza danych nyctaxidb została utworzona w klastrze HDI.

#### <a name="data-exploration-hive-queries-in-python"></a>Eksploracja danych: zapytania gałęzi w pythonie

Ponieważ dane znajdują się w klastrze Hadoop, można użyć pakietu pyodbc do łączenia się z klastrami Hadoop i bazami danych zapytań przy użyciu bazy danych hive do eksploracji i inżynierii funkcji. Można wyświetlić istniejące tabele utworzone w kroku warunek wstępny.

```python
queryString = """
    show tables in nyctaxidb2;
    """
pd.read_sql(queryString, connection)
```

![Wyświetlanie istniejących tabel](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Przyjrzyjmy się liczbie rekordów w każdym miesiącu i częstotliwości przechylonych lub nie w tabeli podróży:

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

![Wykres częstotliwości końcówek](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Można również obliczyć odległość między lokalizacją odbioru a lokalizacją nadania, a następnie porównać ją z odległością podróży.

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

![Górne rzędy stołu odbioru i wysiadania](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

```python
results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                   'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
df = results.loc[results['trip_distance'] <= 100]  # remove outliers
df = df.loc[df['direct_distance'] <= 100]  # remove outliers
plt.scatter(df['direct_distance'], df['trip_distance'])
```

![Wykres odległości odbioru/nadania do odległości podróży](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Teraz przygotujmy zestaw danych do modelowania w dół (1 procent). Tych danych można użyć w module czytnika uczenia maszynowego.

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

Teraz wstaw zawartość sprzężenia do poprzedniej tabeli wewnętrznej.

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

Po chwili widać, że dane zostały załadowane w klastrach Hadoop:

```python
queryString = """
    select * from nyctaxi_downsampled_dataset limit 10;
    """
cursor.execute(queryString)
pd.read_sql(queryString, connection)
```

![Górne wiersze danych z tabeli](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

### <a name="azure-sql-data-warehouse-and-databases"></a>Usługa Azure SQL Data Warehouse i bazy danych
Usługa Azure SQL Data Warehouse to elastyczny magazyn danych jako usługa z systemem SQL Server klasy korporacyjnej.

Możesz aprowizować magazyn danych SQL platformy Azure, postępując zgodnie z instrukcjami zawartymi w [tym artykule.](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) Po aprowizowaniu magazynu danych SQL można użyć [tego przewodnika](../team-data-science-process/sqldw-walkthrough.md) do przekazywania, eksploracji i modelowania danych przy użyciu danych w magazynie danych SQL.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Usługa Azure Cosmos DB to baza danych NoSQL w chmurze. Można go używać do pracy z dokumentami, takimi jak JSON, oraz do przechowywania i przeszukiwania dokumentów.

Użyj następujących kroków wymagań wstępnych, aby uzyskać dostęp do usługi Azure Cosmos DB z dsvm:

1. Zestaw SDK języka Python usługi Azure Cosmos DB jest już zainstalowany w systemie DSVM. Aby go zaktualizować, uruchom ```pip install pydocumentdb --upgrade``` z wiersza polecenia.
2. Utwórz konto i bazę danych usługi Azure Cosmos DB z [witryny Azure portal](https://portal.azure.com).
3. Pobierz narzędzie do migracji danych usługi Azure Cosmos DB z [Centrum pobierania firmy Microsoft](https://www.microsoft.com/download/details.aspx?id=53595) i wyodrębnij je do wybranego katalogu.
4. Importuj dane JSON (dane wulkanu) przechowywane w [publicznym obiekcie blob](https://dotnet.microsoft.com/) do usługi Azure Cosmos DB z następującymi parametrami polecenia do narzędzia migracji. (Użyj dtui.exe z katalogu, w którym zainstalowano narzędzie migracji danych usługi Azure Cosmos DB). Wprowadź lokalizację źródłowej i docelowej z tymi parametrami:
   
    `/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

Po zaimportowaniu danych można przejść do programu Jupyter i otworzyć notes zatytułowany *DocumentDBSample*. Zawiera kod języka Python, aby uzyskać dostęp do usługi Azure Cosmos DB i wykonać kilka podstawowych zapytań. Więcej informacji o usłudze Azure Cosmos DB można znaleźć na [stronie dokumentacji](https://docs.microsoft.com/azure/cosmos-db/)usługi.

## <a name="use-power-bi-reports-and-dashboards"></a>Korzystanie z raportów i pulpitów nawigacyjnych usługi Power BI 
Można wizualizować plik JSON wulkanu z poprzedniego przykładu usługi Azure Cosmos DB w programie Power BI Desktop, aby uzyskać wgląd w dane wizualne. Szczegółowe kroki są dostępne w [artykule usługi Power BI](../../cosmos-db/powerbi-visualize.md). Oto kroki wysokiego poziomu:

1. Otwórz program Power BI Desktop i wybierz pozycję **Pobierz dane**. Określ adres `https://cahandson.blob.core.windows.net/samples/volcano.json`URL jako: .
2. Rekordy JSON powinny być importowane jako lista. Konwertuj listę na tabelę, aby usługa Power BI mogła z nią współpracować.
4. Rozwiń kolumny, zaznaczając ikonę rozwijania (strzałka).
5. Należy zauważyć, że lokalizacja jest **polem Rekord.** Rozwiń rekord i wybierz tylko współrzędne. **Współrzędna** jest kolumną listy.
6. Dodaj nową kolumnę, aby przekonwertować kolumnę współrzędnych listy na oddzieloną przecinkami kolumnę **LatLong.** Połącz dwa elementy w polu listy współrzędnych za pomocą formuły ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```.
7. Przekonwertuj kolumnę **Rzędna** na dziesiętne i wybierz przyciski **Zamknij** i **Zastosuj.**

Zamiast poprzednich kroków można wkleić następujący kod. Jest skryptem kroki używane w Edytorze zaawansowanym w usłudze Power BI do zapisywania przekształceń danych w języku kwerendy.

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

Dane są teraz dostępne w modelu danych usługi Power BI. Wystąpienie programu Power BI Desktop powinno być wyświetlane w następujący sposób:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Tworzenie raportów i wizualizacji można rozpocząć przy użyciu modelu danych. Aby utworzyć raport, wykonaj kroki opisane w tym artykule usługi [Power BI.](../../cosmos-db/powerbi-visualize.md#build-the-reports)

## <a name="scale-the-dsvm-dynamically"></a>Dynamiczne skalowanie dsvm 
Model DSVM można skalować w górę i w dół, aby spełnić potrzeby projektu. Jeśli nie musisz używać maszyny Wirtualnej wieczorem lub w weekendy, możesz zamknąć maszynę wirtualną z [witryny Azure portal.](https://portal.azure.com)

> [!NOTE]
> Opłaty obliczeniowe są pobierane, jeśli używasz tylko przycisku zamykania systemu operacyjnego na maszynie Wirtualnej.  
> 
> 

Może być konieczne obsłużenie niektórych analizy na dużą skalę i potrzebują więcej procesora CPU, pamięci lub pojemności dysku. Jeśli tak, można znaleźć wybór rozmiarów maszyn wirtualnych pod względem rdzeni procesora CPU, wystąpień opartych na procesorze GPU dla uczenia głębokiego, pojemności pamięci i typów dysków (w tym dysków PÓŁPRZEWODNIKOWYCH), które spełniają twoje potrzeby obliczeniowe i budżetowe. Pełna lista maszyn wirtualnych wraz z ich godzinowymi cenami obliczeniowymi jest dostępna na stronie [cenniku maszyn wirtualnych platformy Azure.](https://azure.microsoft.com/pricing/details/virtual-machines/)

Podobnie zapotrzebowanie na zdolność przetwarzania maszyn wirtualnych może zmniejszyć. (Na przykład: przeniesiono duże obciążenie do klastra Hadoop lub Spark). Następnie można skalować w dół klastra z [witryny Azure portal](https://portal.azure.com) i przejść do ustawień wystąpienia maszyny Wirtualnej. 

## <a name="add-more-tools"></a>Dodaj więcej narzędzi
Narzędzia wbudowane w dsvm można rozwiązać wiele typowych potrzeb analizy danych. Pozwala to zaoszczędzić czas, ponieważ nie trzeba instalować i konfigurować środowisk jeden po drugim. Pozwala to również zaoszczędzić pieniądze, ponieważ płacisz tylko za zasoby, których używasz.

Można użyć innych usług azure danych i analiz profilowane w tym artykule, aby ulepszyć środowiska analitycznego. W niektórych przypadkach mogą być potrzebne dodatkowe narzędzia, w tym niektóre autorskie narzędzia partnerskie. Masz pełny dostęp administracyjny na maszynie wirtualnej, aby zainstalować nowe narzędzia, które są potrzebne. Można również zainstalować dodatkowe pakiety w Pythonie i R, które nie są wstępnie zainstalowane. W przypadku języka Python ```conda``` można ```pip```użyć jednego lub . W przypadku R ```install.packages()``` można używać w konsoli R lub użyć IDE i wybierz pozycję **Pakiety** > **instalacyjne pakietów**.

## <a name="deep-learning"></a>Uczenie głębokie

Oprócz przykładów opartych na platformie można uzyskać zestaw kompleksowych instruktaży, które zostały sprawdzone na DSVM. Te wskazówki ułatwiają szybkie rozpoczęcie tworzenia aplikacji do głębokiego uczenia w domenach, takich jak rozumienie obrazu i tekstu/języka.   


- [Uruchamianie sieci neuronowych w różnych ramach:](https://github.com/ilkarman/DeepLearningFrameworks)W tym przewodniku pokazano, jak migrować kod z jednej struktury do drugiej. Pokazano również, jak porównać modele i wydajność środowiska uruchomieniowego w ramach. 

- [Przewodnik do tworzenia kompleksowego rozwiązania do wykrywania produktów w obrazach:](https://github.com/Azure/cortana-intelligence-product-detection-from-images)Wykrywanie obrazów to technika, która może zlokalizować i sklasyfikować obiekty w obrazach. Technologia ta może przynieść ogromne korzyści w wielu rzeczywistych dziedzinach biznesu. Na przykład sprzedawcy detaliczni mogą użyć tej techniki, aby określić, który produkt klient podniósł z półki. Te informacje z kolei pomagają sklepom zarządzać zapasami produktów. 

- [Głębokie uczenie dźwięku:](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/)Ten samouczek pokazuje, jak trenować model uczenia głębokiego do wykrywania zdarzeń audio w [miejskim zestawie danych dźwięków.](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html) Zawiera również omówienie sposobu pracy z danymi audio.

- [Klasyfikacja dokumentów tekstowych:](https://github.com/anargyri/lstm_han)W tym instruktażu pokazano, jak budować i szkolić dwie architektury sieci neuronowych: hierarchiczną sieć uwagi i sieć pamięci długoterminowej (LSTM). Te sieci neuronowe używają interfejsu API Keras do uczenia głębokiego do klasyfikowania dokumentów tekstowych. Keras jest frontonem trzech najpopularniejszych struktur głębokiego uczenia: Microsoft Cognitive Toolkit, TensorFlow i Theano.

## <a name="summary"></a>Podsumowanie
W tym artykule opisano niektóre czynności, które można wykonać na maszynie wirtualnej nauki o danych firmy Microsoft. Istnieje wiele innych rzeczy, które można zrobić, aby DSVM skuteczne środowisko analityczne.

