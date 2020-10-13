---
title: Eksplorowanie danych i modelu w systemie Windows
titleSuffix: Azure Data Science Virtual Machine
description: Wykonuj zadania eksploracji i modelowania danych w Data Science Virtual Machine systemu Windows.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: b570968a66a0cfd60ac4d6ce6dd7dc31a1003240
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89440460"
---
# <a name="data-science-with-a-windows-data-science-virtual-machine"></a>Nauka danych za pomocą Data Science Virtual Machine systemu Windows

Windows Data Science Virtual Machine (DSVM) to zaawansowane środowisko programistyczne do nauki o danych, w którym można wykonywać Eksplorowanie danych i zadania modelowania. Środowisko jest już skompilowane i jest powiązane z kilkoma popularnymi narzędziami do analizy danych, które ułatwiają rozpoczęcie pracy z analizą dla wdrożeń lokalnych, chmurowych lub hybrydowych. 

DSVM ściśle współpracuje z usługami platformy Azure. Może odczytywać i przetwarzać dane, które są już przechowywane na platformie Azure, w usłudze Azure Synapse (dawniej SQL DW), Azure Data Lake, Azure Storage lub Azure Cosmos DB. Może również korzystać z innych narzędzi analitycznych, takich jak Azure Machine Learning.

W tym artykule dowiesz się, jak używać DSVM do wykonywania zadań analizy danych i korzystania z innych usług platformy Azure. Poniżej przedstawiono niektóre czynności, które można wykonać na DSVM:

- Użyj notesu Jupyter, aby eksperymentować z danymi w przeglądarce przy użyciu języka Python 2, Python 3 i Microsoft R. (Microsoft R to wersja systemu R przeznaczona dla przedsiębiorstw, która została zaprojektowana pod kątem wydajności).
- Eksplorowanie danych i projektowanie modeli lokalnie na DSVM przy użyciu Microsoft Machine Learning Server i języka Python.
- Administruj zasobami platformy Azure przy użyciu Azure Portal lub programu PowerShell.
- Zwiększ ilość miejsca w magazynie i Udostępnij zestawy danych o dużej skali/kod w całym zespole, tworząc Azure Files udział jako instalowalny dysk w DSVM.
- Udostępnianie kodu zespołowi za pomocą usługi GitHub. Uzyskaj dostęp do repozytorium przy użyciu wstępnie zainstalowanych klientów git: narzędzia Git bash i interfejsu GUI systemu Git.
- Uzyskuj dostęp do usług Azure Data and Analytics, takich jak Azure Blob Storage, Azure Cosmos DB, Azure Synapse (dawniej SQL DW) i Azure SQL Database.
- Twórz raporty i pulpit nawigacyjny przy użyciu wystąpienia Power BI Desktop, które jest wstępnie zainstalowane w DSVM i wdrażaj je w chmurze.

- Zainstaluj dodatkowe narzędzia na maszynie wirtualnej.   

> [!NOTE]
> Dodatkowe opłaty za użycie dotyczą wielu usług magazynu danych i analiz wymienionych w tym artykule. Aby uzyskać szczegółowe informacje, zobacz stronę z [cennikiem platformy Azure](https://azure.microsoft.com/pricing/) .
> 
> 

## <a name="prerequisites"></a>Wymagania wstępne

* Potrzebna jest subskrypcja platformy Azure. Możesz [utworzyć konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free/).
* Instrukcje dotyczące aprowizacji Data Science Virtual Machine na Azure Portal są dostępne podczas [tworzenia maszyny wirtualnej](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="use-jupyter-notebooks"></a>Korzystanie z notesów programu Jupyter
Jupyter Notebook udostępnia środowisko IDE oparte na przeglądarce do eksploracji i modelowania danych. W notesie Jupyter można używać języka Python 2, Python 3 lub R (zarówno typu open source, jak i Microsoft R Server).

Aby uruchomić Jupyter Notebook, wybierz ikonę **Jupyter Notebook** w menu **Start** lub na pulpicie. W wierszu polecenia DSVM można także uruchomić polecenie ```jupyter notebook``` z katalogu, w którym znajdują się już notesy, lub w którym chcesz utworzyć nowe notesy.  

Po rozpoczęciu Jupyter przejdź do `/notebooks` katalogu, w którym znajdują się przykładowe notesy, które są wstępnie spakowane do DSVM. Co możesz teraz zrobić:

* Wybierz Notes, aby wyświetlić kod.
* Uruchom każdą komórkę, wybierając SHIFT + ENTER.
* Uruchom cały Notes, wybierając pozycję **Cell**  >  **przebieg**komórki.
* Utwórz nowy Notes, wybierając ikonę Jupyter (w lewym górnym rogu), wybierając przycisk **Nowy** po prawej stronie, a następnie wybierając język notesu (nazywany także jądrami).   

> [!NOTE]
> Obecnie obsługiwane są jądra Python 2,7, Python 3,6, R, Julia i PySpark w Jupyter. Jądro języka R obsługuje programowanie zarówno w języku R, jak i w programie Microsoft R.   
> 
> 

Gdy jesteś w notesie, możesz eksplorować dane, kompilować model i testować model przy użyciu wybranych bibliotek.

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Eksplorowanie danych i opracowywanie modeli przy użyciu Microsoft Machine Learning Server
Możesz użyć języków takich jak R i Python, aby przeprowadzić analizę danych bezpośrednio na DSVM.

W przypadku języka R można użyć środowiska IDE, takiego jak RStudio, które można znaleźć w menu Start lub na pulpicie. Można też użyć R Tools for Visual Studio. Firma Microsoft udostępniła dodatkowe biblioteki na podstawie CRAN R typu "open source", aby umożliwić skalowalną analizę i możliwość analizowania danych większych niż rozmiar pamięci dozwolony w równoległej analizie fragmentarycznej. 

W przypadku języka Python można użyć środowiska IDE, takiego jak Visual Studio Community Edition, na którym jest wstępnie zainstalowane rozszerzenie Python Tools for Visual Studio (PTVS). Domyślnie tylko język Python 3,6, główne środowisko Conda jest skonfigurowany w PTVS. Aby włączyć Anaconda Python 2,7, wykonaj następujące czynności:

1. Twórz środowiska niestandardowe dla każdej wersji, przechodząc do **narzędzi**  >  **Python Tools**  >  **Python**Environments, a następnie wybierając pozycję **+ Custom** w programie Visual Studio Community Edition.
1. Podaj opis i Ustaw ścieżkę prefiksu środowiska jako **c:\anaconda\envs\python2** dla anaconda Python 2,7.
1. Wybierz pozycję **Autowykrywanie**  >  **Zastosuj** , aby zapisać środowisko.

Zapoznaj się z [dokumentacją PTVS](https://aka.ms/ptvsdocs) , aby uzyskać szczegółowe informacje na temat tworzenia środowisk języka Python.

Teraz można utworzyć nowy projekt w języku Python. Przejdź do pozycji **plik**  >  **Nowy**  >  **projekt**  >  **Python** i wybierz typ kompilowanej aplikacji języka Python. Możesz ustawić środowisko Python dla bieżącego projektu na żądaną wersję (Python 2,7 lub 3,6), klikając prawym przyciskiem myszy **środowiska Python** , a następnie wybierając polecenie **Dodaj/Usuń środowiska Python**. Więcej informacji na temat pracy z programem PTVS można znaleźć w [dokumentacji produktu](https://aka.ms/ptvsdocs).



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

```bash
git clone https://github.com/Azure/DataScienceVM.git
```

W programie Visual Studio można wykonać tę samą operację klonowania. Poniższy zrzut ekranu pokazuje, jak uzyskać dostęp do narzędzi git i GitHub w programie Visual Studio:

![Zrzut ekranu programu Visual Studio z wyświetlonym połączeniem GitHub](./media/vm-do-ten-things/VSGit.png)

Więcej informacji na temat używania usługi git do pracy z repozytorium GitHub można znaleźć z zasobów dostępnych w witrynie github.com. [Arkusz Ściągawka](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) jest przydatnym odwołaniem.

## <a name="access-azure-data-and-analytics-services"></a>Dostęp do usług Azure Data and Analytics
### <a name="azure-blob-storage"></a>Azure Blob Storage
Magazyn obiektów blob platformy Azure to niezawodna, ekonomiczna usługa magazynu w chmurze, w której dane są duże i małe. W tej sekcji opisano sposób przenoszenia danych do usługi BLOB Storage i uzyskiwania dostępu do danych przechowywanych w obiekcie blob platformy Azure.

#### <a name="prerequisites"></a>Wymagania wstępne

* Utwórz konto usługi Azure Blob Storage na podstawie [Azure Portal](https://portal.azure.com).

   ![Zrzut ekranu procesu tworzenia konta magazynu w Azure Portal](./media/vm-do-ten-things/create-azure-blob.png)

* Upewnij się, że narzędzie wiersza polecenia AzCopy jest wstępnie zainstalowane: ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe``` . Katalog zawierający azcopy.exe znajduje się już w zmiennej środowiskowej PATH, dlatego można uniknąć wpisywania pełnej ścieżki polecenia podczas uruchamiania tego narzędzia. Więcej informacji o narzędziu AzCopy można znaleźć w [dokumentacji AzCopy](../../storage/common/storage-use-azcopy.md).
* Uruchom narzędzie Eksplorator usługi Azure Storage. Można go pobrać z  [witryny sieci web Eksplorator usługi Storage](https://storageexplorer.com/). 

   ![Zrzut ekranu przedstawiający Eksplorator usługi Azure Storage uzyskiwania dostępu do konta magazynu](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>Przenoszenie danych z maszyny wirtualnej do obiektu blob platformy Azure: AzCopy

Aby przenieść dane między lokalnymi plikami i magazynem obiektów blob, można użyć AzCopy w wierszu polecenia lub w programie PowerShell:

```powershell
AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt
```

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

* Aby przekazać dane do kontenera, wybierz kontener docelowy i wybierz przycisk **Przekaż** . ![ Zrzut ekranu przycisku przekazywania w Eksplorator usługi Azure Storage](./media/vm-do-ten-things/storage-accounts.png)
* Wybierz wielokropek (**...**) z prawej strony pola **pliki** , wybierz co najmniej jeden plik do przekazania z systemu plików, a następnie wybierz pozycję **Przekaż** , aby rozpocząć przekazywanie plików. ![ Zrzut ekranu przedstawiający okno dialogowe przekazywanie plików](./media/vm-do-ten-things/upload-files-to-blob.png)

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

![Zrzut ekranu przedstawiający pierwszych 10 wierszy danych](./media/vm-do-ten-things/IPNB_data_readin.png)


### <a name="azure-synapse-analytics-formerly-sql-dw-and-databases"></a>Azure Synapse Analytics (dawniej SQL DW) i bazy danych
Azure Synapse Analytics (dawniej SQL DW) to elastyczny magazyn danych, który jest usługą z SQL Server środowisku korporacyjnym.

Usługę Azure Synapse Analytics można zainicjować przy użyciu instrukcji przedstawionych w [tym artykule](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). Po udostępnieniu usługi Azure Synapse Analytics można użyć [tego przewodnika](../team-data-science-process/sqldw-walkthrough.md) do przekazywania danych, eksploracji i modelowania przy użyciu danych w usłudze Azure Synapse Analytics.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB to baza danych NoSQL w chmurze. Można jej używać do pracy z dokumentami, takimi jak JSON, oraz do przechowywania i wykonywania zapytań dotyczących dokumentów.

Aby uzyskać dostęp do Azure Cosmos DB z DSVM, należy wykonać następujące czynności wstępne:

1. Azure Cosmos DB Python SDK jest już zainstalowany na DSVM. Aby go zaktualizować, uruchom ```pip install pydocumentdb --upgrade``` polecenie w wierszu polecenia.
2. Utwórz konto Azure Cosmos DB i bazę danych z [Azure Portal](https://portal.azure.com).
3. Pobierz narzędzie do migracji danych Azure Cosmos DB z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=53595) i Wyodrębnij je do wybranego katalogu.
4. Importuj dane JSON (Volcano Data) przechowywane w [publicznym obiekcie blob](https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json) do Azure Cosmos DB z następującymi parametrami polecenia do narzędzia migracji. (Użyj dtui.exe z katalogu, w którym zainstalowano narzędzie do migracji danych Azure Cosmos DB). Wprowadź lokalizację źródłową i docelową z następującymi parametrami:
   
    `/s:JsonFile /s.Files:https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

Po zaimportowaniu danych możesz przejść do Jupyter i otworzyć Notes zatytułowany *DocumentDBSample*. Zawiera kod języka Python umożliwiający dostęp do Azure Cosmos DB i wykonywanie podstawowych zapytań. Więcej informacji na temat Azure Cosmos DB można uzyskać, odwiedzając [stronę dokumentacji](https://docs.microsoft.com/azure/cosmos-db/)usługi.

## <a name="use-power-bi-reports-and-dashboards"></a>Korzystanie z Power BI raportów i pulpitów nawigacyjnych 
Możesz wizualizować plik JSON Volcano z powyższego przykładu Azure Cosmos DB w Power BI Desktop, aby uzyskać wizualny wgląd w dane. Szczegółowe kroki są dostępne w [artykule Power BI](../../cosmos-db/powerbi-visualize.md). Poniżej przedstawiono ogólne czynności:

1. Otwórz program Power BI Desktop i wybierz pozycję **Pobierz dane**. Określ adres URL jako: `https://cahandson.blob.core.windows.net/samples/volcano.json` .
2. Powinny zostać wyświetlone rekordy JSON zaimportowane jako lista. Przekonwertuj listę na tabelę, aby Power BI mogła z nią korzystać.
4. Rozwiń kolumny, wybierając ikonę rozwijania (strzałka).
5. Zauważ, że lokalizacja jest polem **rekordu** . Rozwiń rekord i wybierz tylko współrzędne. **Koordynuje** to kolumna listy.
6. Dodaj nową kolumnę, aby przekonwertować kolumnę współrzędnej listy na kolumnę **LatLong** rozdzieloną przecinkami. Połącz dwa elementy w polu listy współrzędnych przy użyciu formuły ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})``` .
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
> Opłaty za obliczenia są naliczane, jeśli dla systemu operacyjnego na maszynie wirtualnej jest używany tylko przycisk Zamknij. Zamiast tego należy cofnąć alokację DSVM przy użyciu Azure Portal lub Cloud Shell.
> 
> 

Może być konieczne obsłużenie pewnej analizy na dużą skalę i konieczności użycia procesora CPU, pamięci lub pojemności dysku. W takim przypadku można znaleźć wybrane rozmiary maszyn wirtualnych pod względem rdzeni procesora, wystąpień opartych na procesorze GPU do uczenia głębokiego, pojemności pamięci i typów dysków (w tym dysków SSD), które spełniają wymagania obliczeniowe i budżetowe. Pełna lista maszyn wirtualnych wraz z cenami obliczeń godzinowych jest dostępna na stronie [cennika usługi Azure Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/) .


## <a name="add-more-tools"></a>Dodaj więcej narzędzi
Narzędzia wbudowane w DSVM mogą dotyczyć wielu typowych potrzeb związanych z analizą danych. Pozwala to zaoszczędzić czas, ponieważ nie trzeba instalować i konfigurować swoich środowisk po jednym. Pozwala to również zaoszczędzić pieniądze, ponieważ płacisz tylko za zasoby, których używasz.

Aby ulepszyć środowisko analityczne, można użyć innych usług Azure Data and Analytics w tym artykule. W niektórych przypadkach mogą być potrzebne dodatkowe narzędzia, w tym niektóre własnościowe narzędzia partnerskie. Na maszynie wirtualnej masz pełny dostęp administracyjny, aby zainstalować potrzebne nowe narzędzia. Można także zainstalować dodatkowe pakiety w języku Python i języku R, które nie są wstępnie zainstalowane. W przypadku języka Python można użyć jednego ```conda``` lub ```pip``` . W przypadku języka r możesz użyć ```install.packages()``` programu w konsoli języka r lub użyć środowiska IDE, a następnie wybrać pozycję **pakiety**  >  **Zainstaluj pakiety**.

## <a name="deep-learning"></a>Uczenie głębokie

Oprócz przykładów opartych na architekturze można uzyskać zestaw obszernych instruktaży, które zostały zweryfikowane w DSVM. Te przewodniki ułatwiają szybkie rozpoczęcie opracowywania aplikacji głębokiego uczenia w domenach, takich jak zrozumienie obrazu i tekstu/języka.   


- [Uruchamianie sieci neuronowych w różnych strukturach](https://github.com/ilkarman/DeepLearningFrameworks): w tym przewodniku pokazano, jak migrować kod z jednej struktury do innej. Przedstawiono w nim również sposób porównywania modeli i wydajności środowiska uruchomieniowego w różnych strukturach. 

- Przewodnik, w którym można [utworzyć kompleksowe rozwiązanie wykrywające produkty w obrazach](https://github.com/Azure/cortana-intelligence-product-detection-from-images): wykrywanie obrazów to technika, która umożliwia lokalizowanie i klasyfikowanie obiektów w obrazach. Ta technologia ma potencjalne znaczenie w wielu domenach firmowych w czasie rzeczywistym. Na przykład Detaliści mogą użyć tej techniki, aby określić, który produkt został pobrany przez klienta z półki. Te informacje z kolei ułatwiają przechowywanie spisu produktów. 

- [Głębokie uczenie audio](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): w tym samouczku pokazano, jak przeszkolić model głębokiego uczenia na potrzeby wykrywania zdarzeń audio w [zestawie danych "dźwięki miejskie](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html)". Zawiera również omówienie sposobu pracy z danymi audio.

- [Klasyfikacja dokumentów tekstowych](https://github.com/anargyri/lstm_han): w tym instruktażu pokazano, jak tworzyć i przeszkolić dwie architektury sieci neuronowych: hierarchiczną sieć uwagi i długą pamięć krótkoterminową (LSTM). Te sieci neuronowych używają interfejsu API Keras do uczenia głębokiego do klasyfikowania dokumentów tekstowych. 

## <a name="summary"></a>Podsumowanie
W tym artykule opisano niektóre czynności, które można wykonać w Data Science Virtual Machine firmy Microsoft. Istnieje wiele zadań, które można wykonać, aby DSVM wydajne środowisko analityczne.

