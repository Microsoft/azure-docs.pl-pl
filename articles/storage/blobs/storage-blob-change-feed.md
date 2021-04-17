---
title: Zestawienia zmian w Azure Blob Storage | Microsoft Docs
description: Dowiedz się więcej na temat dzienników zestawienia Azure Blob Storage i sposobu ich używania.
author: normesta
ms.author: normesta
ms.date: 02/08/2021
ms.topic: how-to
ms.service: storage
ms.subservice: blobs
ms.reviewer: sadodd
ms.openlocfilehash: 6da83ceb6d8ee51916d25949309d7ddfba0e4b30
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503618"
---
# <a name="change-feed-support-in-azure-blob-storage"></a>Obsługa zestawienia zmian w Azure Blob Storage

Celem zestawienia zmian jest dostarczenie dzienników transakcji wszystkich zmian, które zachodzą w obiektach blob, oraz metadanych obiektów blob na koncie magazynu. Źródło zmian zawiera **uporządkowany,** **gwarantowany,** **trwały,** **niezmienny** i tylko **do** odczytu dziennik tych zmian. Aplikacje klienckie mogą odczytywać te dzienniki w dowolnym momencie, w trybie przesyłania strumieniowego lub w trybie wsadowym. Zestawienia zmian umożliwia tworzenie wydajnych i skalowalnych rozwiązań, które przetwarzają zdarzenia zmiany, które występują Blob Storage konta użytkownika przy niskich kosztach.

[!INCLUDE [storage-data-lake-gen2-support](../../../includes/storage-data-lake-gen2-support.md)]

## <a name="how-the-change-feed-works"></a>Jak działa zestawienia zmian

Zestawienia zmian są przechowywane jako [obiekty blob w](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) specjalnym kontenerze na koncie magazynu po standardowych cenach obiektów [blob.](https://azure.microsoft.com/pricing/details/storage/blobs/) Okres przechowywania tych plików można kontrolować na podstawie wymagań (zobacz [warunki](#conditions) bieżącej wersji). Zdarzenia zmiany są dołączane do zestawienia zmian jako rekordy w specyfikacji formatu [Apache Avro:](https://avro.apache.org/docs/1.8.2/spec.html) kompaktowy, szybki format binarny, który zapewnia rozbudowane struktury danych ze schematem w tekście. Ten format jest powszechnie używany w ekosystemie hadoop, Stream Analytics i Azure Data Factory.

Dzienniki te można przetwarzać asynchronicznie, przyrostowo lub w całości. Dowolna liczba aplikacji klienckich może niezależnie odczytywać źródło zmian, równolegle i we własnym tempie. Aplikacje analityczne, takie jak [Apache Drill](https://drill.apache.org/docs/querying-avro-files/) lub [Apache Spark,](https://spark.apache.org/docs/latest/sql-data-sources-avro.html) mogą korzystać z dzienników bezpośrednio jako pliki Avro, co pozwala przetwarzać je przy niskich kosztach, z wysoką przepustowością i bez konieczności pisania aplikacji niestandardowej.

Na poniższym diagramie pokazano, jak rekordy są dodawane do zestawienia zmian:

:::image type="content" source="media/storage-blob-change-feed/change-feed-diagram.png" alt-text="Diagram przedstawiający sposób działania zestawienia zmian w celu zapewnienia uporządkowanego dziennika zmian w obiektach blob":::

Obsługa zestawienia zmian jest odpowiednia dla scenariuszy, w których dane są przetwarzane na podstawie zmienionych obiektów. Na przykład aplikacje mogą:

  - Aktualizowanie indeksu pomocniczego, synchronizacja z pamięcią podręczną, wyszukiwarką lub innymi scenariuszami zarządzania zawartością.
  
  - Wyodrębnianie szczegółowych informacji i metryk analityki biznesowej na podstawie zmian zachodzących w obiektach, w trybie strumieniowym lub wsadowym.
  
  - Przechowywanie, inspekcja i analizowanie zmian w obiektach, w dowolnym okresie, pod kątem bezpieczeństwa, zgodności lub analizy dla zarządzania danymi w przedsiębiorstwie.

  - Twórz rozwiązania do tworzenia kopii zapasowych, dublowania lub replikowania stanu obiektów na swoim koncie w celu zarządzania awariami lub zapewnienia zgodności.

  - Twórz połączone potoki aplikacji, które reagują na zmiany zdarzeń lub planują wykonywanie na podstawie utworzonego lub zmienionego obiektu.
  
Źródło zmian to funkcja wymagań wstępnych dla replikacji [obiektów](object-replication-overview.md) i [przywracania do punktu w czasie dla blokowych obiektów blob.](point-in-time-restore-overview.md)

> [!NOTE]
> Źródło zmian zapewnia trwały, uporządkowany model dziennika zmian, które występują w obiekcie blob. Zmiany są zapisywane i udostępniane w dzienniku zestawienia zmian w ciągu kilku minut od zmiany. Jeśli twoja aplikacja musi reagować na zdarzenia znacznie szybciej niż to, rozważ użycie Blob Storage [zdarzeń.](storage-blob-event-overview.md) [Blob Storage Events](storage-blob-event-overview.md) udostępnia zdarzenia czasu rzeczywistego, które umożliwiają użytkownikom Azure Functions lub aplikacjom szybkie reagowanie na zmiany zachodzące w obiekcie blob. 

## <a name="enable-and-disable-the-change-feed"></a>Włączanie i wyłączanie zestawienia zmian

Musisz włączyć zestawienia zmian na koncie magazynu, aby rozpocząć przechwytywanie i rejestrowanie zmian. Wyłącz źródło zmian, aby zatrzymać przechwytywanie zmian. Zmiany można włączać i wyłączać przy użyciu szablonów Azure Resource Manager portalu lub programu PowerShell.

Oto kilka rzeczy, o których należy pamiętać po włączeniu zestawienia zmian.

- Istnieje tylko jeden kanał informacyjny zmian dla usługi blob na każdym koncie magazynu i jest przechowywany w **kontenerze $blobchangefeed** magazynu.

- Zmiany dotyczące tworzenia, aktualizacji i usuwania są przechwytywane tylko na poziomie usługi obiektów blob.

- Źródło zmian *przechwytuje* wszystkie zmiany dla wszystkich dostępnych zdarzeń, które występują na koncie. Aplikacje klienckie mogą filtrować typy zdarzeń zgodnie z potrzebami. (Zobacz [warunki](#conditions) bieżącej wersji).

- Tylko konta GPv2 i konta usługi Blob Storage mogą włączać zestawienia zmian. Konta BlockBlobStorage w wersji Premium i konta z włączoną hierarchiczną przestrzenią nazw nie są obecnie obsługiwane. Konta magazynu GPv1 nie są obsługiwane, ale można je uaktualnić do wersji GPv2 bez przestojów. Aby uzyskać więcej informacji, zobacz Uaktualnianie do [konta magazynu GPv2.](../common/storage-account-upgrade.md)

### <a name="portal"></a>[Portal](#tab/azure-portal)

Włącz zestawienia zmian na koncie magazynu przy użyciu Azure Portal:

1. W [Azure Portal](https://portal.azure.com/)wybierz konto magazynu.
1. Przejdź do opcji **Ochrona danych** w obszarze **Zarządzanie danymi**.
1. W **obszarze Śledzenie** wybierz pozycję Włącz źródło zmian obiektów **blob.**
1. Wybierz przycisk **Zapisz,** aby potwierdzić ustawienia ochrony danych.

    :::image type="content" source="media/storage-blob-change-feed/change-feed-enable-portal.png" alt-text="Zrzut ekranu przedstawiający sposób włączania zestawienia zmian w Azure Portal":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Włączanie zestawienia zmian przy użyciu programu PowerShell:

1. Zainstaluj najnowszą wersję polecenia PowershellGet.

   ```powershell
   Install-Module PowerShellGet –Repository PSGallery –Force
   ```

2. Zamknij, a następnie otwórz ponownie konsolę programu PowerShell.

3. Zainstaluj wersję 2.5.0 lub nowszą **modułu Az.Storage.**

   ```powershell
   Install-Module Az.Storage –Repository PSGallery -RequiredVersion 2.5.0 –AllowClobber –Force
   ```

4. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia i postępuj zgodnie z `Connect-AzAccount` instrukcjami na ekranie, aby się uwierzytelnić.

   ```powershell
   Connect-AzAccount
   ```

5. Włącz zestawienia zmian dla konta magazynu.

   ```powershell
   Update-AzStorageBlobServiceProperty -EnableChangeFeed $true
   ```

### <a name="template"></a>[Szablon](#tab/template)
Użyj szablonu Azure Resource Manager, aby włączyć kanał informacyjny zmian na istniejącym koncie magazynu za pośrednictwem Azure Portal:

1. W Azure Portal wybierz pozycję **Utwórz zasób.**

2. W **polu Wyszukaj w witrynie Marketplace** wpisz wdrożenie **szablonu,** a następnie naciśnij klawisz **ENTER.**

3. Wybierz **[pozycję Deploy a custom template (Wd](https://portal.azure.com/#create/Microsoft.Template)** wdrażaj szablon niestandardowy), a następnie wybierz pozycję Build your own template in the editor **(Skompilowanie własnego szablonu w edytorze).**

4. W edytorze szablonów wklej następujący kod JSON. Zastąp symbol zastępczy `<accountName>` nazwą konta magazynu.

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts/blobServices",
           "apiVersion": "2019-04-01",
           "name": "<accountName>/default",
           "properties": {
               "changeFeed": {
                   "enabled": true
               }
           } 
        }]
   }
   ```
    
5. Wybierz przycisk **Zapisz,** określ grupę zasobów konta, a następnie wybierz przycisk **Kup,** aby wdrożyć szablon i włączyć zestawienia zmian.

---

## <a name="consume-the-change-feed"></a>Wykorzystanie zestawienia zmian

Źródło zmian generuje kilka plików metadanych i dziennika. Te pliki znajdują się w **$blobchangefeed** kontenera konta magazynu. 

> [!NOTE]
> W bieżącej wersji kontener $blobchangefeed widoczny tylko w Azure Portal, ale nie jest widoczny w Eksplorator usługi Azure Storage. Obecnie kontener kontenera usługi $blobchangefeed nie jest wyświetlony podczas wywołania interfejsu API ListContainers, ale możesz wywołać interfejs API ListBlobs bezpośrednio w kontenerze, aby wyświetlić obiekty blob

Aplikacje klienckie mogą korzystać z zestawienia zmian przy użyciu biblioteki procesora zestawienia zmian obiektów blob dostarczonej z zestawem SDK procesora zestawienia zmian. 

Zobacz [Przetwarzanie dzienników zestawienia zmian w Azure Blob Storage](storage-blob-change-feed-how-to.md).

## <a name="understand-change-feed-organization"></a>Opis organizacji zestawienia zmian

<a id="segment-index"></a>

### <a name="segments"></a>Segmenty

Zestawienia zmian to dziennik zmian, które są zorganizowane w **segmenty**  godzinowe, ale dołączane do i aktualizowane co kilka minut. Te segmenty są tworzone tylko wtedy, gdy w ciągu tej godziny występują zdarzenia zmiany obiektu blob. Umożliwia to aplikacji klienckiej korzystanie ze zmian, które występują w określonych zakresach czasu, bez konieczności przeszukiwania całego dziennika. Aby dowiedzieć się więcej, zobacz [Specifications (Specyfikacje).](#specifications)

Dostępny godzinowy segment zestawienia zmian jest opisany w pliku manifestu, który określa ścieżki do plików zestawienia zmian dla tego segmentu. Lista katalogu `$blobchangefeed/idx/segments/` wirtualnego zawiera te segmenty uporządkowane według czasu. Ścieżka segmentu opisuje początek przedziału czasu godzinowego, który reprezentuje segment. Ta lista umożliwia filtrowanie segmentów dzienników, które Cię interesują.

```text
Name                                                                    Blob Type    Blob Tier      Length  Content Type    
----------------------------------------------------------------------  -----------  -----------  --------  ----------------
$blobchangefeed/idx/segments/1601/01/01/0000/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1810/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/22/1910/meta.json                  BlockBlob                      584  application/json
$blobchangefeed/idx/segments/2019/02/23/0110/meta.json                  BlockBlob                      584  application/json
```

> [!NOTE]
> Plik `$blobchangefeed/idx/segments/1601/01/01/0000/meta.json` jest tworzony automatycznie po włączeniu zestawienia zmian. Możesz bezpiecznie zignorować ten plik. Jest to zawsze pusty plik inicjalizacji. 

Plik manifestu segmentu ( `meta.json` ) pokazuje ścieżkę plików zestawienia zmian dla tego segmentu we właściwości `chunkFilePaths` . Oto przykład pliku manifestu segmentu.

```json
{
    "version": 0,
    "begin": "2019-02-22T18:10:00.000Z",
    "intervalSecs": 3600,
    "status": "Finalized",
    "config": {
        "version": 0,
        "configVersionEtag": "0x8d698f0fba563db",
        "numShards": 2,
        "recordsFormat": "avro",
        "formatSchemaVersion": 1,
        "shardDistFnVersion": 1
    },
    "chunkFilePaths": [
        "$blobchangefeed/log/00/2019/02/22/1810/",
        "$blobchangefeed/log/01/2019/02/22/1810/"
    ],
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-22T18:11:01.187Z",
        "data": {
            "aid": "55e507bf-8006-0000-00d9-ca346706b70c"
        }
    }
}
```

> [!NOTE]
> Kontener zostanie wyświetlony dopiero po włączeniu funkcji zestawienia zmian `$blobchangefeed` na koncie. Zanim będzie można wyświetlić listę obiektów blob w kontenerze, musisz poczekać kilka minut po włączeniu zestawienia zmian. 

<a id="log-files"></a>

### <a name="change-event-records"></a>Zmienianie rekordów zdarzeń

Pliki zestawienia zmian zawierają serię rekordów zdarzeń zmiany. Każdy rekord zdarzenia zmiany odpowiada jednej zmianie pojedynczego obiektu blob. Rekordy są serializowane i zapisywane w pliku przy użyciu specyfikacji [formatu Apache Avro.](https://avro.apache.org/docs/1.8.2/spec.html) Rekordy można odczytywać przy użyciu specyfikacji formatu pliku Avro. Dostępnych jest kilka bibliotek do przetwarzania plików w tym formacie.

Pliki zestawienia zmian są przechowywane w katalogu `$blobchangefeed/log/` wirtualnym jako [uzupełnialne obiekty blob](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs). Pierwszy plik zestawienia zmian w każdej ścieżce będzie miał `00000` nazwę pliku (na przykład `00000.avro` ). Nazwa każdego kolejnego pliku dziennika dodanego do tej ścieżki będzie zwiększana o 1 (na przykład: `00001.avro` ).

Następujące typy zdarzeń są przechwytywane w rekordach zestawienia zmian:
- Obiekt BlobCreated
- BlobDeleted
- BlobPropertiesUpdated
- BlobSnapshotCreated

Oto przykład zmiany rekordu zdarzenia z pliku zestawienia zmian przekonwertowanego na format Json.

```json
{
     "schemaVersion": 1,
     "topic": "/subscriptions/dd40261b-437d-43d0-86cf-ef222b78fd15/resourceGroups/sadodd/providers/Microsoft.Storage/storageAccounts/mytestaccount",
     "subject": "/blobServices/default/containers/mytestcontainer/blobs/mytestblob",
     "eventType": "BlobCreated",
     "eventTime": "2019-02-22T18:12:01.079Z",
     "id": "55e5531f-8006-0000-00da-ca3467000000",
     "data": {
         "api": "PutBlob",
         "clientRequestId": "edf598f4-e501-4750-a3ba-9752bb22df39",
         "requestId": "00000000-0000-0000-0000-000000000000",
         "etag": "0x8D698F13DCB47F6",
         "contentType": "application/octet-stream",
         "contentLength": 128,
         "blobType": "BlockBlob",
         "url": "",
         "sequencer": "000000000000000100000000000000060000000000006d8a",
         "storageDiagnostics": {
             "bid": "11cda41c-13d8-49c9-b7b6-bc55c41b3e75",
             "seq": "(6,5614,28042,28038)",
             "sid": "591651bd-8eb3-c864-1001-fcd187be3efd"
         }
  }
}
```

Aby uzyskać opis każdej właściwości, zobacz [Azure Event Grid schematu zdarzeń dla Blob Storage](../../event-grid/event-schema-blob-storage.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#event-properties). Zdarzenia BlobPropertiesUpdated i BlobSnapshotCreated są obecnie dostępne wyłącznie w kanale informacyjnym zmian i nie są jeszcze obsługiwane Blob Storage zdarzeń.

> [!NOTE]
> Pliki zestawienia zmian dla segmentu nie są natychmiast wyświetlane po utworzeniu segmentu. Długość opóźnienia wynosi w normalnym interwale publikowania opóźnienia zestawienia zmian, który jest w ciągu kilku minut od zmiany.

<a id="specifications"></a>

## <a name="specifications"></a>Specyfikacje

- Rekordy zdarzeń zmiany są dołączane tylko do zestawienia zmian. Po dołączyć te rekordy są niezmienne i pozycja rekordu jest stabilna. Aplikacje klienckie mogą utrzymywać własny punkt kontrolny na pozycji odczytu zestawienia zmian.

- Rekordy zdarzeń zmiany są dołączane w ciągu kilku minut od zmiany. Aplikacje klienckie mogą korzystać z rekordów, ponieważ są dołączane w celu uzyskania dostępu do przesyłania strumieniowego lub zbiorczo w dowolnym innym czasie.

- Rekordy zdarzeń zmiany są uporządkowane według kolejności modyfikacji **dla każdego obiektu blob**. Kolejność zmian między obiektami blob jest niezdefiniowana w Azure Blob Storage. Wszystkie zmiany w wcześniejszym segmencie są przed zmianami w kolejnych segmentach.

- Rekordy zdarzeń zmian są serializowane do pliku dziennika przy użyciu specyfikacji formatu [Apache Avro 1.8.2.](https://avro.apache.org/docs/1.8.2/spec.html)

- Zmień rekordy zdarzeń, w których wartość ma wartość , są wewnętrznymi rekordami systemu i nie odzwierciedlają `eventType` `Control` zmian w obiektach na Twoim koncie. Te rekordy można bezpiecznie zignorować.

- Wartości w torbie właściwości są przeznaczone tylko do użytku wewnętrznego i nie są przeznaczone `storageDiagnostics` do użytku przez aplikację. Twoje aplikacje nie powinny mieć zależności umownej od tych danych. Możesz bezpiecznie zignorować te właściwości.

- Czas reprezentowany przez segment jest **przybliżony** i wynosi 15 minut. Aby zapewnić zużycie wszystkich rekordów w określonym czasie, należy korzystać z kolejnego segmentu poprzedniej i następnej godziny.

- Każdy segment może mieć inną liczbę ze względu na wewnętrzne partycjonowanie strumienia `chunkFilePaths` dzienników w celu zarządzania przepływnością publikowania. Pliki dziennika w każdym z nich mają gwarancję, że zawierają wzajemnie wykluczające się obiekty blob, i mogą być używane i przetwarzane równolegle bez naruszania kolejności modyfikacji na obiekt blob podczas `chunkFilePath` iteracji.

- Stan segmentów zaczyna `Publishing` się od początku. Po zakończeniu dołączania rekordów do segmentu będzie to `Finalized` . Pliki dziennika w dowolnym segmencie, które są datowane po dacie właściwości w pliku, nie powinny być używane `LastConsumable` `$blobchangefeed/meta/Segments.json` przez aplikację. Oto przykład właściwości `LastConsumable` w `$blobchangefeed/meta/Segments.json` pliku:

```json
{
    "version": 0,
    "lastConsumable": "2019-02-23T01:10:00.000Z",
    "storageDiagnostics": {
        "version": 0,
        "lastModifiedTime": "2019-02-23T02:24:00.556Z",
        "data": {
            "aid": "55e551e3-8006-0000-00da-ca346706bfe4",
            "lfz": "2019-02-22T19:10:00.000Z"
        }
    }
}

```

<a id="conditions"></a>

## <a name="conditions-and-known-issues"></a>Warunki i znane problemy

W tej sekcji opisano znane problemy i warunki w bieżącej wersji zestawienia zmian. 

- Rekordy zdarzeń zmiany dla każdej pojedynczej zmiany mogą pojawiać się więcej niż raz w twoim kanale zmian.
- Nie można jeszcze zarządzać okresem istnienia plików dziennika zestawienia zmian, ustawiając dla nich zasady przechowywania na podstawie czasu i nie można usunąć obiektów blob.
- Właściwość `url` pliku dziennika jest obecnie zawsze pusta.
- Właściwość pliku segments.jsnie zawiera pierwszego segmentu, który finalizuje `LastConsumable` kanał zmian. Ten problem występuje dopiero po sfinalizowaniu pierwszego segmentu. Wszystkie kolejne segmenty po pierwszej godzinie są dokładnie przechwytywane we właściwości `LastConsumable` .
- Obecnie nie widzisz kontenera **$blobchangefeed** podczas wywołania interfejsu API ListContainers, a kontener nie jest Azure Portal ani Eksplorator usługi Storage. Zawartość można wyświetlić, wywołując interfejs API ListBlobs bezpośrednio $blobchangefeed kontenerze.
- Konta magazynu, które wcześniej zainicjowały trybu [failover konta,](../common/storage-disaster-recovery-guidance.md) mogą mieć problemy z tym, że plik dziennika nie jest wyświetlany. Wszelkie przyszłe trybu failover konta może również mieć wpływ na plik dziennika.

## <a name="faq"></a>Często zadawane pytania

### <a name="what-is-the-difference-between-change-feed-and-storage-analytics-logging"></a>Jaka jest różnica między kanałem informacyjnym zmian analityka magazynu rejestrowaniem zmian?
Dzienniki analizy mają rekordy wszystkich operacji odczytu, zapisu, listy i usuwania z żądaniami pomyślnych i nieudanych we wszystkich operacjach. Dzienniki analityczne są najbardziej nakładem pracy i nie ma gwarancji kolejności.

Źródło zmian to rozwiązanie, które udostępnia dziennik transakcji pomyślnych operacji lub zmian na koncie, takich jak tworzenie, modyfikowanie i usuwanie obiektów blob. Źródło zmian gwarantuje, że wszystkie zdarzenia mają być rejestrowane i wyświetlane w kolejności pomyślnych zmian na obiekt blob, dlatego nie trzeba odfiltrować szumu z ogromnej liczby operacji odczytu lub żądań, które zakończyły się niepowodzeniem. Źródło zmian zostało zaprojektowane i zoptymalizowane pod kątem tworzenia aplikacji, które wymagają pewnych gwarancji.

### <a name="should-i-use-change-feed-or-storage-events"></a>Czy należy używać zestawienia zmian lub zdarzeń magazynu?
Można korzystać z obu funkcji, ponieważ kanał zmian i zdarzenia usługi [Blob Storage](storage-blob-event-overview.md) zapewniają te same informacje z taką samą gwarancją niezawodności dostarczania, a główną różnicą jest opóźnienie, kolejność i przechowywanie rekordów zdarzeń. Źródło zmian publikuje rekordy w dzienniku w ciągu kilku minut od zmiany, a także gwarantuje kolejność operacji zmiany na obiekt blob. Zdarzenia magazynu są wypychane w czasie rzeczywistym i mogą nie być uporządkowane. Zdarzenia zestawienia zmian są trwale przechowywane wewnątrz konta magazynu jako stabilne dzienniki tylko do odczytu z własnym zdefiniowanym przechowywaniem, podczas gdy zdarzenia magazynu są przejściowe, które mogą być używane przez program obsługi zdarzeń, chyba że jawnie je przechowujesz. Dzięki zestawienia zmian dowolna liczba aplikacji może samodzielnie korzystać z dzienników przy użyciu interfejsów API obiektów blob lub zestawów SDK. 

## <a name="next-steps"></a>Następne kroki

- Zobacz przykład sposobu odczytywania zestawienia zmian przy użyciu aplikacji klienckiej .NET. Zobacz [Przetwarzanie dzienników zestawienia zmian w Azure Blob Storage](storage-blob-change-feed-how-to.md).
- Dowiedz się, jak reagować na zdarzenia w czasie rzeczywistym. Zobacz [Reagowanie na Blob Storage zdarzeń](storage-blob-event-overview.md)
- Dowiedz się więcej o szczegółowych informacjach dotyczących rejestrowania dla operacji pomyślnych i nieudanych dla wszystkich żądań. Zobacz [Rejestrowanie analityki usługi Azure Storage](../common/storage-analytics-logging.md)
