---
title: Korzystanie z biblioteki klienta Azure Batch dla języka JavaScript
description: Poznaj podstawowe koncepcje Azure Batch i Utwórz proste rozwiązanie przy użyciu języka JavaScript.
ms.topic: how-to
ms.date: 01/01/2021
ms.openlocfilehash: 1f53c793e4c676df319d46f30595ac330846dfa5
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231483"
---
# <a name="get-started-with-batch-sdk-for-javascript"></a>Wprowadzenie do zestawu SDK usługi Batch dla języka JavaScript

Poznaj podstawy tworzenia klienta usługi Batch w języku JavaScript przy użyciu [Azure Batch JavaScript SDK](https://github.com/Azure/azure-sdk-for-js/). Zajmiemy się podejściem krok po kroku opisującym scenariusz aplikacji usługi Batch, a następnie skonfigurowania jej przy użyciu języka JavaScript.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że masz praktyczną wiedzę na temat języka JavaScript i znajomość systemu Linux. Przyjęto również założenie, że masz skonfigurowane konto platformy Azure z prawami dostępu do tworzenia usług Batch i Storage.

Zalecane jest przeczytanie artykułu [Azure Batch Technical Overview](batch-technical-overview.md) (Omówienie techniczne usługi Azure Batch) przed wykonaniem instrukcji opisanych w tym artykule.

## <a name="understand-the-scenario"></a>Omówienie scenariusza

W tym miejscu mamy prosty skrypt zapisany w języku Python, który pobiera wszystkie pliki CSV z kontenera usługi Azure Blob Storage i konwertuje je na format JSON. Aby przetwarzać wiele kontenerów kont magazynów równolegle, można wdrożyć skrypt jako zadanie w ramach usługi Azure Batch.

## <a name="azure-batch-architecture"></a>Architektura Azure Batch

Na poniższym diagramie przedstawiono sposób skalowania skryptu w języku Python przy użyciu Azure Batch i klienta.

![Diagram przedstawiający architekturę scenariuszy.](./media/batch-js-get-started/batch-scenario.png)

Przykładowy kod JavaScript wdraża zadanie wsadowe przy użyciu zadania przygotowania (szczegółowo dokładniej) i zestawu zadań w zależności od liczby kontenerów na koncie magazynu. Skrypty można pobrać z repozytorium GitHub.

- [Przykładowy kod](https://github.com/Azure-Samples/azure-batch-samples/blob/master/JavaScript/Node.js/sample.js)
- [Przygotowanie skryptów powłoki zadań](https://github.com/Azure-Samples/azure-batch-samples/blob/master/JavaScript/Node.js/startup_prereq.sh)
- [Konwerter plików csv języka Python na format JSON](https://github.com/Azure-Samples/azure-batch-samples/blob/master/JavaScript/Node.js/processcsv.py)

> [!TIP]
> Przykład JavaScript w określonym łączu nie zawiera określonego kodu do wdrożenia jako aplikacja funkcji platformy Azure. Użyj następujących linków, aby zapoznać się z instrukcjami tworzenia kodu.
> - [Tworzenie aplikacji funkcji](../azure-functions/functions-get-started.md)
> - [Create timer trigger function](../azure-functions/functions-bindings-timer.md) (Tworzenie funkcji wyzwalanej czasomierzem)

## <a name="build-the-application"></a>Kompilowanie aplikacji

Teraz poinformuj nas o etapie procesu, aby skompilować klienta JavaScript:

### <a name="step-1-install-azure-batch-sdk"></a>Krok 1. Instalowanie zestawu SDK usługi Azure Batch

Azure Batch SDK dla języka JavaScript można zainstalować za pomocą polecenia install npm.

`npm install azure-batch`

To polecenie powoduje zainstalowanie najnowszej wersji usługi Azure-Batch SDK dla języka JavaScript.

>[!Tip]
> W aplikacji funkcji platformy Azure należy przejść do karty Ustawienia, a następnie do obszaru „Konsola Kudu”, aby uruchomić polecenia npm install. W takim przypadku należy zainstalować Azure Batch SDK dla języka JavaScript.

### <a name="step-2-create-an-azure-batch-account"></a>Krok 2. Tworzenie konta usługi Azure Batch

Można go utworzyć za pomocą [Azure Portal](batch-account-create-portal.md) lub z wiersza polecenia ([PowerShell](batch-powershell-cmdlets-get-started.md)  / [platformy Azure](/cli/azure)).

Poniżej przedstawiono polecenia, które umożliwiają utworzenie konta za pomocą interfejsu wiersza polecenia platformy Azure.

Utwórz grupę zasobów. Pomiń ten krok, jeśli masz już grupę, w której chcesz utworzyć konto usługi Azure Batch:

`az group create -n "<resource-group-name>" -l "<location>"`

Następnie utwórz konto usługi Azure Batch.

`az batch account create -l "<location>"  -g "<resource-group-name>" -n "<batch-account-name>"`

Każde konto usługi Batch ma odpowiadające mu klucze dostępu. Te klucze są wymagane do tworzenia dodatkowych zasobów na koncie usługi Azure Batch. Dobrym rozwiązaniem dla środowiska produkcyjnego jest użycie usługi Azure Key Vault do przechowywania tych kluczy. Następnie można utworzyć jednostkę usługi dla aplikacji. Przy użyciu tej jednostki usługi aplikacja może utworzyć token OAuth w celu uzyskania dostępu do kluczy z magazynu kluczy.

`az batch account keys list -g "<resource-group-name>" -n "<batch-account-name>"`

Skopiuj i zachowaj klucz, ponieważ będzie potrzebny w kolejnych krokach samouczka.

### <a name="step-3-create-an-azure-batch-service-client"></a>Krok 3. Tworzenie klienta usługi Azure Batch

Poniższy fragment kodu najpierw importuje moduł JavaScript Azure-Batch, a następnie tworzy klienta usługi Batch. Najpierw należy utworzyć obiekt SharedKeyCredentials za pomocą klucza konta usługi Batch skopiowanego w poprzednim kroku.

```javascript
// Initializing Azure Batch variables

var batch = require('azure-batch');

var accountName = '<azure-batch-account-name>';

var accountKey = '<account-key-downloaded>';

var accountUrl = '<account-url>'

// Create Batch credentials object using account name and account key

var credentials = new batch.SharedKeyCredentials(accountName,accountKey);

// Create Batch service client

var batch_client = new batch.ServiceClient(credentials,accountUrl);

```

Identyfikator URI usługi Azure Batch można znaleźć na karcie Przegląd witryny Azure Portal. Jego format to:

`https://accountname.location.batch.azure.com`

Przyjrzyj się zrzutowi ekranu:

![Identyfikator URI usługi Azure Batch](./media/batch-js-get-started/batch-uri.png)

### <a name="step-4-create-an-azure-batch-pool"></a>Krok 4. Tworzenie puli usługi Azure Batch

Pula usługi Azure Batch składa się z wielu maszyn wirtualnych (znanych także jako węzły usługi Batch). Usługa Azure Batch wdraża zadania podrzędne na tych węzłach i zarządza nimi. Dla puli można zdefiniować następujące parametry konfiguracji.

- Typ obrazu maszyny wirtualnej
- Rozmiar węzłów maszyny wirtualnej
- Liczba węzłów maszyny wirtualnej

> [!TIP]
> Rozmiar i liczba węzłów maszyny wirtualnej w dużej mierze zależy od liczby zadań podrzędnych wykonywanych równolegle, a także od ich rodzaju. Zaleca się przeprowadzenie testów w celu określenia odpowiedniej liczby i rozmiaru węzłów.

Poniższy fragment kodu tworzy obiekty parametru konfiguracji.

```javascript
// Creating Image reference configuration for Ubuntu Linux VM
var imgRef = {publisher:"Canonical",offer:"UbuntuServer",sku:"14.04.2-LTS",version:"latest"}

// Creating the VM configuration object with the SKUID
var vmconfig = {imageReference:imgRef,nodeAgentSKUId:"batch.node.ubuntu 14.04"}

// Setting the VM size to Standard F4
var vmSize = "STANDARD_F4"

//Setting number of VMs in the pool to 4
var numVMs = 4
```

> [!TIP]
> Lista obrazów maszyn wirtualnych systemu Linux dostępnych dla usługi Azure Batch oraz ich identyfikatorów jednostek SKU znajduje się w sekcji [Lista obrazów maszyn wirtualnych](batch-linux-nodes.md#list-of-virtual-machine-images).

Po zdefiniowaniu konfiguracji puli można przystąpić do tworzenia puli usługi Azure Batch. Polecenie dotyczące puli w ramach usługi Batch tworzy węzły maszyny wirtualnej platformy Azure i przygotowuje je do odbierania zadań podrzędnych do wykonania. Każda pula powinna mieć unikatowy identyfikator, który będzie potrzebny w kolejnych krokach samouczka.

Poniższy fragment kodu przedstawia tworzenie puli usługi Azure Batch.

```javascript
// Create a unique Azure Batch pool ID
var poolid = "pool" + customerDetails.customerid;
var poolConfig = {id:poolid, displayName:poolid,vmSize:vmSize,virtualMachineConfiguration:vmconfig,targetDedicatedComputeNodes:numVms,enableAutoScale:false };
// Creating the Pool for the specific customer
var pool = batch_client.pool.add(poolConfig,function(error,result){
    if(error!=null){console.log(error.response)};
});
```

Przed przesłaniem zadania do tej puli należy sprawdzić stan utworzonej puli, aby upewnić się, że jest „aktywny”.

```javascript
var cloudPool = batch_client.pool.get(poolid,function(error,result,request,response){
        if(error == null)
        {

            if(result.state == "active")
            {
                console.log("Pool is active");
            }
        }
        else
        {
            if(error.statusCode==404)
            {
                console.log("Pool not found yet returned 404...");

            }
            else
            {
                console.log("Error occurred while retrieving pool data");
            }
        }
        });
```

Poniżej przedstawiono przykładowy obiekt wyniku zwrócony przez funkcję pool.get.

```
{ id: 'processcsv_201721152',
  displayName: 'processcsv_201721152',
  url: 'https://<batch-account-name>.centralus.batch.azure.com/pools/processcsv_201721152',
  eTag: '<eTag>',
  lastModified: 2017-03-27T10:28:02.398Z,
  creationTime: 2017-03-27T10:28:02.398Z,
  state: 'active',
  stateTransitionTime: 2017-03-27T10:28:02.398Z,
  allocationState: 'resizing',
  allocationStateTransitionTime: 2017-03-27T10:28:02.398Z,
  vmSize: 'standard_a1',
  virtualMachineConfiguration:
   { imageReference:
      { publisher: 'Canonical',
        offer: 'UbuntuServer',
        sku: '14.04.2-LTS',
        version: 'latest' },
     nodeAgentSKUId: 'batch.node.ubuntu 14.04' },
  resizeTimeout:
   { [Number: 900000]
     _milliseconds: 900000,
     _days: 0,
     _months: 0,
     _data:
      { milliseconds: 0,
        seconds: 0,
        minutes: 15,
        hours: 0,
        days: 0,
        months: 0,
        years: 0 },
     _locale:
      Locale {
        _calendar: [Object],
        _longDateFormat: [Object],
        _invalidDate: 'Invalid date',
        ordinal: [Function: ordinal],
        _ordinalParse: /\d{1,2}(th|st|nd|rd)/,
        _relativeTime: [Object],
        _months: [Object],
        _monthsShort: [Object],
        _week: [Object],
        _weekdays: [Object],
        _weekdaysMin: [Object],
        _weekdaysShort: [Object],
        _meridiemParse: /[ap]\.?m?\.?/i,
        _abbr: 'en',
        _config: [Object],
        _ordinalParseLenient: /\d{1,2}(th|st|nd|rd)|\d{1,2}/ } },
  currentDedicated: 0,
  targetDedicated: 4,
  enableAutoScale: false,
  enableInterNodeCommunication: false,
  taskSlotsPerNode: 1,
  taskSchedulingPolicy: { nodeFillType: 'Spread' } }
```

### <a name="step-4-submit-an-azure-batch-job"></a>Krok 4. Przesyłanie zadania usługi Azure Batch

Zadanie usługi Azure Batch jest logiczną grupą podobnych zadań podrzędnych. W naszym scenariuszu jest to „Konwertowanie plików csv na format JSON”. Każde przedstawione tutaj zadanie podrzędne może przetwarzać pliki csv zawarte we wszystkich kontenerach usługi Azure Storage.

Dzięki usłudze Azure Batch zadania podrzędne będą wykonywane równolegle i wdrażane w wielu węzłach.

> [!TIP]
> Możesz użyć właściwości [taskSlotsPerNode](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add) , aby określić maksymalną liczbę zadań, które mogą być uruchamiane współbieżnie w jednym węźle.

#### <a name="preparation-task"></a>Zadanie podrzędne przygotowania

Utworzone węzły maszyny wirtualnej są pustymi węzłami systemu Ubuntu. Często konieczne jest zainstalowanie zestawu programów.
Zazwyczaj w przypadku węzłów systemu Linux można korzystać ze skryptu powłoki, który instaluje wstępnie wymagane oprogramowanie przed uruchomieniem jakichkolwiek zadań podrzędnych. Jednak może to być dowolny inny programowalny i wykonywalny skrypt.

[Skrypt powłoki](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/startup_prereq.sh) przedstawiony w tym przykładzie instaluje narzędzie pip języka Python oraz zestaw SDK usługi Azure Storage dla języka Python.

W celu uzyskania dostępu do skryptu można go przekazać na konto usługi Azure Storage i wygenerować identyfikator URI sygnatury dostępu współdzielonego. Ten proces można również zautomatyzować za pomocą zestawu SDK JavaScript usługi Azure Storage.

> [!TIP]
> Zadanie podrzędne przygotowania w ramach zadania działa tylko na węzłach tej maszyny wirtualnej, na której konkretne zadanie podrzędne musi zostać uruchomione. Jeśli chcesz zainstalować wstępnie wymagane oprogramowanie we wszystkich węzłach (niezależnie od rodzaju zadania podrzędnego, które będzie w nich uruchamiane), podczas dodawania puli użyj właściwości [startTask](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Pool.html#add). Poniżej przedstawiono definicję zadania podrzędnego przygotowania.

Zadanie podrzędne przygotowania jest określane podczas przesyłania zadania usługi Azure Batch. Poniżej przedstawiono parametry konfiguracji zadania podrzędnego przygotowania:

- **D**: unikatowy identyfikator zadania podrzędnego przygotowania
- **commandLine**: wiersz polecenia służący do wykonania wykonywalnego zadania podrzędnego
- **resourceFiles**: tablica obiektów zawierająca szczegółowe informacje o plikach, które należy pobrać w celu uruchomienia zadania podrzędnego.  Poniżej przedstawiono dostępne opcje
  - blobSource: identyfikator URI sygnatury dostępu współdzielonego danego pliku
  - filePath: ścieżka lokalna do pobrania i zapisania pliku
  - fileMode: dotyczy wyłącznie węzłów systemu Linux, opcja fileMode jest w formacie ósemkowym i domyślnie ma wartość 0770
- **waitForSuccess**: jeśli ma wartość „true”, zadanie podrzędne nie zostanie uruchomione w razie niepowodzenia zadania podrzędnego przygotowania
- **runElevated**: jeśli do uruchomienia zadania podrzędnego konieczne są podwyższone uprawnienia, należy ustawić wartość „true”.

Poniższy fragment kodu pokazuje przykładową konfigurację skryptu zadania podrzędnego przygotowania:

```javascript
var job_prep_task_config = {id:"installprereq",commandLine:"sudo sh startup_prereq.sh > startup.log",resourceFiles:[{'blobSource':'Blob SAS URI','filePath':'startup_prereq.sh'}],waitForSuccess:true,runElevated:true}
```

Jeśli nie ma konieczności instalowania żadnego wstępnie wymaganego oprogramowania w celu uruchomienia zadań podrzędnych, można pominąć zadania podrzędne przygotowania. Następujący kod tworzy zadanie o nazwie wyświetlanej „process csv files” (konwertowanie plików csv).

 ```javascript
 // Setting up Batch pool configuration
 var pool_config = {poolId:poolid}
 // Setting up Job configuration along with preparation task
 var jobId = "processcsvjob"
 var job_config = {id:jobId,displayName:"process csv files",jobPreparationTask:job_prep_task_config,poolInfo:pool_config}
 // Adding Azure batch job to the pool
 var job = batch_client.job.add(job_config,function(error,result){
     if(error != null)
     {
         console.log("Error submitting job : " + error.response);
     }});
```

### <a name="step-5-submit-azure-batch-tasks-for-a-job"></a>Krok 5. Przesyłanie zadań podrzędnych usługi Azure Batch do zadania

Po utworzeniu zadania konwertującego pliki csv można utworzyć dla niego zadania podrzędne. Przy założeniu, że mamy cztery kontenery, należy utworzyć cztery zadania podrzędne, po jednym dla każdego kontenera.

[Skrypt języka Python](https://github.com/shwetams/azure-batchclient-sample-nodejs/blob/master/processcsv.py) przyjmuje dwa parametry:

- nazwa kontenera: kontener magazynu, z którego pobierane są pliki
- wzorzec: opcjonalny parametr wzorca nazwy plików

Zakładamy, że mamy cztery kontenery: „con1”, „con2”, „con3” i „con4”. Następujący kod przedstawia przesyłanie zadań podrzędnych do utworzonego wcześniej zadania konwertującego pliki csv usługi Azure Batch.

```javascript
// storing container names in an array
var container_list = ["con1","con2","con3","con4"]
    container_list.forEach(function(val,index){

           var container_name = val;
           var taskID = container_name + "_process";
           var task_config = {id:taskID,displayName:'process csv in ' + container_name,commandLine:'python processcsv.py --container ' + container_name,resourceFiles:[{'blobSource':'<blob SAS URI>','filePath':'processcsv.py'}]}
           var task = batch_client.task.add(poolid,task_config,function(error,result){
                if(error != null)
                {
                    console.log(error.response);
                }
                else
                {
                    console.log("Task for container : " + container_name + "submitted successfully");
                }



           });

    });
```

Kod dodaje wiele zadań podrzędnych do puli. Poszczególne zadania podrzędne są wykonywane w węźle utworzonej puli maszyn wirtualnych. Jeśli liczba zadań przekracza liczbę maszyn wirtualnych w puli lub właściwość taskSlotsPerNode, zadania czekają na udostępnienie węzła. Takie ograniczenia są zapewniane przez usługę Azure Batch automatycznie.

W witrynie Azure Portal zamieszczono szczegółowe widoki stanów zadań i zadań podrzędnych. Możesz również użyć listy i pobrać funkcje w zestawie SDK JavaScript platformy Azure. Szczegółowe informacje znajdują się w dokumentacji, do której prowadzi ten [link](https://azure.github.io/azure-sdk-for-node/azure-batch/latest/Job.html).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [przepływie pracy usługi Batch i zasobach podstawowych](batch-service-workflow-features.md) , takich jak pule, węzły, zadania i zadania.
- Zobacz [Dokumentacja usługi Batch JavaScript](/javascript/api/overview/azure/batch) , aby poznać interfejs API usługi Batch.