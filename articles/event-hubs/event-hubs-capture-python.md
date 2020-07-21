---
title: 'Szybki Start: odczytywanie przechwyconych danych z aplikacji w języku Python — Event Hubs platformy Azure'
description: 'Szybki Start: skrypty używające zestawu Azure Python SDK do zademonstrowania funkcji przechwytywania Event Hubs.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 364ca789f560dc8fdae099b09c77946bc4ad5005
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86537228"
---
# <a name="quickstart-event-hubs-capture-walkthrough-python-azure-eventhub-version-1"></a>Przewodnik Szybki Start: Event Hubs Przechwytywanie: Python (Azure-eventhub wersja 1)

Przechwytywanie jest funkcją usługi Azure Event Hubs. Możesz użyć funkcji przechwytywania, aby automatycznie dostarczać dane przesyłane strumieniowo do centrum zdarzeń na wybranym przez siebie koncie usługi Azure Blob Storage. Ta funkcja ułatwia przetwarzanie wsadowe danych przesyłanych strumieniowo w czasie rzeczywistym. W tym artykule opisano sposób korzystania z funkcji przechwytywania Event Hubs w języku Python. Aby uzyskać więcej informacji na temat funkcji przechwytywania Event Hubs, zobacz [przechwytywanie zdarzeń za pomocą usługi Azure Event Hubs][Overview of Event Hubs Capture].

W tym instruktażu do zademonstrowania funkcji przechwytywania jest wykorzystywany [zestaw Azure Python SDK](https://azure.microsoft.com/develop/python/) . Program *Sender.py* wysyła symulowane dane telemetryczne środowiska do Event Hubs w formacie JSON. Centrum zdarzeń używa funkcji przechwytywania do zapisywania tych danych w usłudze BLOB Storage w partiach. Aplikacja *capturereader.py* odczytuje te obiekty blob, tworzy plik dołączany dla każdego z urządzeń i zapisuje dane do plików *CSV* na każdym urządzeniu.

> [!WARNING]
> Ten przewodnik Szybki Start dotyczy wersji 1 zestawu SDK języka Python platformy Azure Event Hubs. Zalecamy [Migrowanie](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md) kodu do [wersji 5 zestawu SDK języka Python](get-started-capture-python-v2.md).

W tym instruktażu zawarto następujące instrukcje: 

> [!div class="checklist"]
> * Utwórz w Azure Portal konto magazynu obiektów blob platformy Azure i kontener.
> * Włącz funkcję przechwytywania Event Hubs i Przekieruj ją do konta magazynu.
> * Wyślij dane do centrum zdarzeń przy użyciu skryptu języka Python.
> * Odczytywanie i przetwarzanie plików z Event Hubs przechwytywania przy użyciu innego skryptu języka Python.

## <a name="prerequisites"></a>Wymagania wstępne

- Język Python 3,4 lub nowszy z `pip` zainstalowanym i zaktualizowanym programem.
  
- Subskrypcja platformy Azure. Jeśli go nie masz, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .
  
- Aktywna przestrzeń nazw Event Hubs i centrum zdarzeń utworzone zgodnie z instrukcjami podanymi w [przewodniku szybki start: tworzenie centrum zdarzeń przy użyciu Azure Portal](event-hubs-create.md). Zanotuj swoją przestrzeń nazw i nazwy centrów zdarzeń do użycia w dalszej części tego przewodnika. 
  
  > [!NOTE]
  > Jeśli masz już kontener magazynu do użycia, możesz włączyć funkcję przechwytywania i wybrać kontener magazynu podczas tworzenia centrum zdarzeń. 
  > 
  
- Nazwa klucza dostępu współdzielonego Event Hubs i wartość klucza podstawowego. Znajdź lub Utwórz te wartości w obszarze **zasady dostępu współdzielonego** na stronie Event Hubs. Domyślna nazwa klucza dostępu to **RootManageSharedAccessKey**. Skopiuj nazwę klucza dostępu i wartość klucza podstawowego do użycia w dalszej części tego przewodnika. 

## <a name="create-an-azure-blob-storage-account-and-container"></a>Tworzenie konta i kontenera usługi Azure Blob Storage

Utwórz konto magazynu i kontener, które mają być używane do przechwytywania. 

1. Zaloguj się w witrynie [Azure Portal][Azure portal].
2. W lewym okienku nawigacji wybierz pozycję **konta magazynu**, a następnie na ekranie **konta magazynu** wybierz pozycję **Dodaj**.
3. Na ekranie Tworzenie konta magazynu wybierz subskrypcję i grupę zasobów, a następnie podaj nazwę konta magazynu. Pozostałe wybory można pozostawić domyślnie. Wybierz pozycję **Przegląd + Utwórz**, przejrzyj ustawienia, a następnie wybierz pozycję **Utwórz**. 
   
   ![Tworzenie konta magazynu][1]
   
4. Po zakończeniu wdrażania wybierz pozycję **Przejdź do zasobu**, a następnie na ekranie **Przegląd** konta magazynu wybierz pozycję **kontenery**.
5. Na ekranie **kontenery** wybierz pozycję **+ kontener**. 
6. Na ekranie **nowy kontener** nadaj kontenerowi nazwę, a następnie wybierz przycisk **OK**. Zanotuj nazwę kontenera do użycia w dalszej części przewodnika. 
7. W lewym panelu nawigacyjnym ekranu **kontenery** wybierz pozycję **klucze dostępu**. Skopiuj **nazwę konta magazynu**i wartość **klucza** w obszarze **Klucz1**, aby użyć jej w dalszej części przewodnika.
 
## <a name="enable-event-hubs-capture"></a>Włącz przechwytywanie Event Hubs

1. W Azure Portal przejdź do centrum zdarzeń, wybierając jego przestrzeń nazw Event Hubs ze **wszystkich zasobów**, wybierając pozycję **Centra zdarzeń** w lewym okienku nawigacji, a następnie wybierając centrum zdarzeń. 
2. Na ekranie **Omówienie** centrum zdarzeń wybierz pozycję **Przechwyć zdarzenia**.
3. Na ekranie **przechwytywanie** wybierz pozycję **włączone**. Następnie w obszarze **kontener usługi Azure Storage**wybierz pozycję **Wybierz kontener**. 
4. Na ekranie **kontenery** wybierz kontener magazynu, którego chcesz użyć, a następnie wybierz pozycję **Wybierz**. 
5. Na ekranie **przechwytywanie** wybierz pozycję **Zapisz zmiany**. 

## <a name="create-a-python-script-to-send-events-to-event-hub"></a>Tworzenie skryptu w języku Python do wysyłania zdarzeń do centrum zdarzeń
Ten skrypt wysyła zdarzenia 200 do centrum zdarzeń. Zdarzenia to proste odczyty środowiska w formacie JSON.

1. Otwórz swój ulubiony Edytor Python, taki jak [Visual Studio Code][Visual Studio Code].
2. Utwórz nowy plik o nazwie *Sender.py*. 
3. Wklej poniższy kod do *Sender.py*. Zastąp własne wartości Event Hubs \<namespace> , \<AccessKeyName> , \<primary key value> i \<eventhub> .
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus.control_client import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='<namespace>', shared_access_key_name='<AccessKeyName>', shared_access_key_value='<primary key value>')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('<eventhub>', s)
       print(y)
   ```
4. Zapisz plik.

## <a name="create-a-python-script-to-read-capture-files"></a>Tworzenie skryptu w języku Python do odczytu plików przechwytywania

Ten skrypt odczytuje przechwycone pliki i tworzy plik dla każdego urządzenia, aby zapisać dane tylko dla tego urządzenia.

1. W edytorze języka Python Utwórz nowy plik o nazwie *capturereader.py*. 
2. Wklej poniższy kod do *capturereader.py*. Zastąp zapisane wartości dla \<storageaccount> , \<storage account access key> i \<storagecontainer> .
   
   ```python
   import os
   import string
   import json
   import avro.schema
   from avro.datafile import DataFileReader, DataFileWriter
   from avro.io import DatumReader, DatumWriter
   from azure.storage.blob import BlockBlobService
   
   def processBlob(filename):
       reader = DataFileReader(open(filename, 'rb'), DatumReader())
       dict = {}
       for reading in reader:
           parsed_json = json.loads(reading["Body"])
           if not 'id' in parsed_json:
               return
           if not parsed_json['id'] in dict:
               list = []
               dict[parsed_json['id']] = list
           else:
               list = dict[parsed_json['id']]
               list.append(parsed_json)
       reader.close()
       for device in dict.keys():
           deviceFile = open(device + '.csv', "a")
           for r in dict[device]:
               deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
   
   def startProcessing(accountName, key, container):
       print('Processor started using path: ' + os.getcwd())
       block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
       generator = block_blob_service.list_blobs(container)
       for blob in generator:
           #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
           if blob.properties.content_length > 508:
               print('Downloaded a non empty blob: ' + blob.name)
               cleanName = str.replace(blob.name, '/', '_')
               block_blob_service.get_blob_to_path(container, blob.name, cleanName)
               processBlob(cleanName)
               os.remove(cleanName)
           block_blob_service.delete_blob(container, blob.name)
   startProcessing('<storageaccount>', '<storage account access key>', '<storagecontainer>')
   ```

## <a name="run-the-python-scripts"></a>Uruchamianie skryptów języka Python

1. Otwórz wiersz polecenia z językiem Python w swojej ścieżce i uruchom następujące polecenia, aby zainstalować wstępnie wymagane pakiety języka Python:
   
   ```cmd
   pip install azure-storage
   pip install azure-servicebus
   pip install avro-python3
   ```
   
   Jeśli masz wcześniejszą wersję programu `azure-storage` lub `azure` , może być konieczne użycie tej `--upgrade` opcji.
   
   Może być również konieczne uruchomienie następującego polecenia. Uruchomienie tego polecenia nie jest konieczne w większości systemów. 
   
   ```cmd
   pip install cryptography
   ```
   
2. W katalogu, w którym zapisano *Sender.py* i *capturereader.py*, uruchom następujące polecenie:
   
   ```cmd
   start python sender.py
   ```
   
   Polecenie uruchamia nowy proces języka Python, aby uruchomić nadawcę.
   
3. Po zakończeniu przechwytywania Uruchom następujące polecenie:
   
   ```cmd
   python capturereader.py
   ```

   Procesor przechwytywania pobiera wszystkie niepuste obiekty blob z kontenera konta magazynu i zapisuje wyniki jako pliki *CSV* w katalogu lokalnym. 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Event Hubs, zobacz: 

* [Przegląd Event Hubs przechwytywania][Overview of Event Hubs Capture]
* [Przykładowe aplikacje korzystające z usługi Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Przegląd usługi Event Hubs][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: ./event-hubs-about.md
