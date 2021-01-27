---
title: Rejestrowanie analityki magazynu platformy Azure
description: Użyj analityka magazynu, aby rejestrować szczegółowe informacje o żądaniach usługi Azure Storage. Zobacz, jakie żądania są rejestrowane, jak są przechowywane dzienniki, jak włączyć rejestrowanie magazynu i nie tylko.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 07/23/2020
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: f0f9832a8128a447970535f18cceca3cd4dccc69
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880257"
---
# <a name="azure-storage-analytics-logging"></a>Rejestrowanie usługi Azure Storage Analytics

Usługa Storage Analytics rejestruje szczegółowe informacje dotyczące żądań do usługi magazynu zakończonych powodzeniem i niepowodzeniem. Tych informacji można używać na potrzeby monitorowania poszczególnych żądań i diagnozowania problemów z usługą magazynu. Żądania są rejestrowane na podstawie najlepszego wysiłku.

 Rejestrowanie w usłudze Storage Analytics nie jest domyślnie włączone dla Twojego konta magazynu. Można ją włączyć w [Azure Portal](https://portal.azure.com/); Aby uzyskać szczegółowe informacje, zobacz [Monitorowanie konta magazynu w Azure Portal](./storage-monitor-storage-account.md). Możesz również włączyć analityka magazynu programowo za pośrednictwem interfejsu API REST lub biblioteki klienta. Aby włączyć analityka magazynu dla każdej usługi, użyj [właściwości get BLOB Service](/rest/api/storageservices/Blob-Service-REST-API), [Pobierz właściwości usługi kolejkowania](/rest/api/storageservices/Get-Queue-Service-Properties)i [Pobierz właściwości usługi Table](/rest/api/storageservices/Get-Table-Service-Properties) Service.

 Wpisy dziennika są tworzone tylko wtedy, gdy istnieją żądania skierowane do punktu końcowego usługi. Na przykład jeśli konto magazynu ma aktywność w swoim punkcie końcowym obiektu BLOB, ale nie znajduje się w jego punktach końcowych tabeli lub kolejki, zostaną utworzone tylko dzienniki dotyczące Blob service.

> [!NOTE]
>  Rejestrowanie w usłudze Storage Analytics jest aktualnie dostępne tylko dla usług Blob Storage, Queue Storage i Table Storage. Analityka magazynu rejestrowanie jest również dostępne dla kont [BlockBlobStorage](../blobs/storage-blob-create-account-block-blob.md) z wydajnością Premium. Nie jest to jednak dostępne w przypadku kont ogólnego przeznaczenia w wersji 2 z wydajnością w warstwie Premium.

## <a name="requests-logged-in-logging"></a>Zarejestrowane żądania logowania
### <a name="logging-authenticated-requests"></a>Rejestrowanie żądań uwierzytelnionych

 Rejestrowane są następujące typy żądań uwierzytelnionych:

- Żądania zakończone powodzeniem
- Żądania zakończone niepowodzeniem, w tym błędy limitu czasu, ograniczania przepustowości, sieci, autoryzacji i inne błędy
- Żądania przy użyciu sygnatury dostępu współdzielonego (SAS) lub protokołu OAuth, w tym żądania zakończone niepowodzeniem i zakończone powodzeniem
- Żądania dotyczące danych analityki

  Żądania wykonywane przez analityka magazynu samego siebie, takie jak tworzenie lub usuwanie dziennika, nie są rejestrowane. Pełna lista zarejestrowanych danych jest udokumentowana w temacie [analityka magazynu zarejestrowane operacje i komunikaty o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) oraz informacje o [formacie dziennika analityka magazynu](/rest/api/storageservices/storage-analytics-log-format) .

### <a name="logging-anonymous-requests"></a>Rejestrowanie żądań anonimowych

 Rejestrowane są następujące typy żądań anonimowych:

- Żądania zakończone powodzeniem
- Błędy serwera
- Błędy przekroczenia limitu czasu dla klienta i serwera
- Żądania GET zakończone niepowodzeniem z kodem błędu 304 (Nie zmodyfikowano)

  Wszystkie inne Nieudane żądania anonimowe nie są rejestrowane. Pełna lista zarejestrowanych danych jest udokumentowana w temacie [analityka magazynu zarejestrowane operacje i komunikaty o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages) oraz informacje o [formacie dziennika analityka magazynu](/rest/api/storageservices/storage-analytics-log-format) .

## <a name="how-logs-are-stored"></a>Jak przechowywane są dzienniki

Wszystkie dzienniki są przechowywane w blokowych obiektach Blob w kontenerze o nazwie `$logs` , który jest tworzony automatycznie, gdy analityka magazynu jest włączona dla konta magazynu. Kontener znajduje się `$logs` w przestrzeni nazw obiektów BLOB na koncie magazynu, na przykład: `http://<accountname>.blob.core.windows.net/$logs` . Nie można usunąć tego kontenera po włączeniu analityka magazynu, chociaż jego zawartość może zostać usunięta. Jeśli używasz narzędzia do przeglądania magazynu do bezpośredniego przechodzenia do kontenera, zobaczysz wszystkie obiekty blob zawierające dane rejestrowania.

> [!NOTE]
>  `$logs`Kontener nie jest wyświetlany, gdy jest wykonywana operacja tworzenia listy kontenerów, taka jak operacja listy kontenerów. Dostęp do niego należy uzyskać bezpośrednio. Na przykład możesz użyć operacji list Blobs, aby uzyskać dostęp do obiektów BLOB w `$logs` kontenerze.

Gdy żądania są rejestrowane, analityka magazynu przekaże wyniki pośrednie jako bloki. Okresowo analityka magazynu zatwierdzi te bloki i udostępni je jako obiekt BLOB. Dane dziennika mogą pojawić się w obiektach Blob w kontenerze **$Logs** , ponieważ częstotliwość opróżniania przez usługę magazynu dzienników. Zduplikowane rekordy mogą istnieć dla dzienników utworzonych w tej samej godzinie. Można określić, czy rekord jest duplikatem, sprawdzając identyfikator **żądania** i numer **operacji** .

Jeśli masz dużą ilość danych dziennika z wieloma plikami dla każdej godziny, możesz użyć metadanych obiektu BLOB, aby określić, jakie dane zawiera dziennik, badając pola metadanych obiektów BLOB. Jest to również przydatne, ponieważ czasami może wystąpić opóźnienie, gdy dane są zapisywane w plikach dziennika: metadane obiektów BLOB zapewniają dokładniejsze wskazanie zawartości obiektu BLOB niż nazwa obiektu BLOB.

Większość narzędzi do przeglądania magazynu umożliwia przeglądanie metadanych obiektów BLOB; te informacje można również odczytać przy użyciu programu PowerShell lub programowo. Poniższy fragment kodu programu PowerShell to przykład filtrowania listy obiektów BLOB dziennika według nazwy w celu określenia czasu i metadanych w celu zidentyfikowania tylko tych dzienników, które zawierają operacje **zapisu** .  

 ```powershell
 Get-AzStorageBlob -Container '$logs' |  
 Where-Object {  
     $_.Name -match 'table/2014/05/21/05' -and   
     $_.ICloudBlob.Metadata.LogType -match 'write'  
 } |  
 ForEach-Object {  
     "{0}  {1}  {2}  {3}" –f $_.Name,   
     $_.ICloudBlob.Metadata.StartTime,   
     $_.ICloudBlob.Metadata.EndTime,   
     $_.ICloudBlob.Metadata.LogType  
 }  
 ```  

Aby uzyskać informacje na temat programistycznego tworzenia listy obiektów blob, zobacz [wyliczanie zasobów obiektów BLOB](/rest/api/storageservices/Enumerating-Blob-Resources) i [Ustawianie właściwości i metadanych dla zasobów obiektów BLOB](/rest/api/storageservices/Setting-and-Retrieving-Properties-and-Metadata-for-Blob-Resources).  

### <a name="log-naming-conventions"></a>Konwencje nazewnictwa dzienników

 Każdy dziennik zostanie zapisany w następującym formacie:

 `<service-name>/YYYY/MM/DD/hhmm/<counter>.log`

 W poniższej tabeli opisano każdy atrybut w nazwie dziennika:

|Atrybut|Opis|
|---------------|-----------------|
|`<service-name>`|Nazwa usługi magazynu. Na przykład: `blob` , `table` , lub `queue`|
|`YYYY`|Czterocyfrowy rok dla dziennika. Na przykład: `2011`|
|`MM`|Dwucyfrowy miesiąc dla dziennika. Na przykład: `07`|
|`DD`|Dzień dwa cyfry dla dziennika. Na przykład: `31`|
|`hh`|Godzina dwie cyfry, która wskazuje godzinę początkową dzienników w formacie 24-godzinnym. Na przykład: `18`|
|`mm`|Wartość dwóch cyfr wskazująca na minutę początkową dzienników. **Uwaga:**  Ta wartość nie jest obsługiwana w bieżącej wersji analityka magazynu i jej wartość będzie zawsze równa `00` .|
|`<counter>`|Licznik oparty na zero, zawierający sześć cyfr wskazujących liczbę obiektów BLOB dziennika wygenerowanych dla usługi magazynu w ciągu godziny. Ten licznik jest uruchamiany o `000000` . Na przykład: `000001`|

 Poniżej znajduje się pełna nazwa dziennika przykładowego, który łączy powyższe przykłady:

 `blob/2011/07/31/1800/000001.log`

 Poniżej znajduje się przykładowy identyfikator URI, którego można użyć w celu uzyskania dostępu do powyższego dziennika:

 `https://<accountname>.blob.core.windows.net/$logs/blob/2011/07/31/1800/000001.log`

 Gdy żądanie magazynu zostanie zarejestrowane, powstająca Nazwa dziennika jest skorelowana na godzinę, gdy żądana operacja została ukończona. Na przykład jeśli żądanie GetBlob zostało wykonane w dniu 6:30 na 7/31/2011, Dziennik zostanie zapisany z następującym prefiksem: `blob/2011/07/31/1800/`

### <a name="log-metadata"></a>Metadane dziennika

 Wszystkie obiekty blob dziennika są przechowywane z metadanymi, których można użyć do zidentyfikowania, jakie dane rejestrowania zawiera obiekt BLOB. W poniższej tabeli opisano każdy atrybut metadanych:

|Atrybut|Opis|
|---------------|-----------------|
|`LogType`|Opisuje, czy dziennik zawiera informacje dotyczące operacji odczytu, zapisu lub usuwania. Ta wartość może zawierać jeden typ lub kombinację wszystkich trzech, rozdzielonych przecinkami.<br /><br /> Przykład 1: `write`<br /><br /> Przykład 2: `read,write`<br /><br /> Przykład 3: `read,write,delete`|
|`StartTime`|Najwcześniejsza godzina wpisu w dzienniku w postaci `YYYY-MM-DDThh:mm:ssZ` . Na przykład: `2011-07-31T18:21:46Z`|
|`EndTime`|Najnowsza godzina wpisu w dzienniku w postaci `YYYY-MM-DDThh:mm:ssZ` . Na przykład: `2011-07-31T18:22:09Z`|
|`LogVersion`|Wersja formatu dziennika.|

 Na poniższej liście przedstawiono kompletne przykładowe metadane przy użyciu powyższych przykładów:

-   `LogType=write`
-   `StartTime=2011-07-31T18:21:46Z`
-   `EndTime=2011-07-31T18:22:09Z`
-   `LogVersion=1.0`

## <a name="enable-storage-logging"></a>Włącz rejestrowanie magazynu

Rejestrowanie magazynu można włączyć przy użyciu zestawów SDK Azure Portal, PowerShell i magazynu.

### <a name="enable-storage-logging-using-the-azure-portal"></a>Włącz rejestrowanie magazynu przy użyciu Azure Portal  

W Azure Portal użyj bloku **ustawień diagnostycznych (klasyczny)** , aby kontrolować Rejestrowanie magazynu, dostępne w sekcji **monitorowanie (klasyczne)** w **bloku menu** konta magazynu.

Możesz określić usługi magazynu, które mają być rejestrowane, oraz okres przechowywania (w dniach) dla zarejestrowanych danych.  

### <a name="enable-storage-logging-using-powershell"></a>Włączanie rejestrowania magazynu przy użyciu programu PowerShell  

 Aby skonfigurować rejestrowanie magazynu na koncie magazynu przy Azure PowerShell użyciu polecenia cmdlet **Get-AzStorageServiceLoggingProperty** w programie PowerShell, można użyć narzędzia do pobrania bieżących ustawień oraz polecenia cmdlet **Set-AzStorageServiceLoggingProperty** w celu zmiany bieżących ustawień.  

 Polecenia cmdlet kontrolujące Rejestrowanie magazynu używają **LoggingOperations** parametru, który jest ciągiem zawierającym rozdzielaną przecinkami listę typów żądań do rejestrowania. Trzy możliwe typy żądań to **Odczyt**, **zapis** i **usuwanie**. Aby wyłączyć rejestrowanie, użyj wartości **none** dla parametru **LoggingOperations** .  

 Następujące polecenie przełącza do rejestrowania żądań odczytu, zapisu i usuwania w usługa kolejki na domyślnym koncie magazynu z ustawionym okresem przechowywania na pięć dni:  

```powershell
Set-AzStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5  
```  

 Następujące polecenie wyłącza rejestrowanie dla usługi Table Service na domyślnym koncie magazynu:  

```powershell
Set-AzStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none  
```  

 Aby uzyskać informacje dotyczące sposobu konfigurowania Azure PowerShell poleceń cmdlet do pracy z subskrypcją platformy Azure i wybierania domyślnego konta magazynu do użycia, zobacz: [jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/).  

### <a name="enable-storage-logging-programmatically"></a>Programowo Włącz rejestrowanie magazynu  

 Oprócz używania Azure Portal lub Azure PowerShell poleceń cmdlet do kontrolowania rejestrowania magazynu, można również użyć jednego z interfejsów API usługi Azure Storage. Jeśli na przykład używasz języka .NET, możesz użyć biblioteki klienta usługi Storage.  

# <a name="net-v12-sdk"></a>[\.Zestaw SDK NET V12](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

# <a name="net-v11-sdk"></a>[\.Zestaw SDK NET v11](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
```  

---


 Aby uzyskać więcej informacji na temat konfigurowania rejestrowania magazynu przy użyciu języka .NET, zobacz [Dokumentacja biblioteki klienta usługi Storage](/previous-versions/azure/dn261237(v=azure.100)).  

 Aby uzyskać ogólne informacje o konfigurowaniu rejestrowania magazynu za pomocą interfejsu API REST, zobacz [Włączanie i konfigurowanie analityka magazynu](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).  

## <a name="download-storage-logging-log-data"></a>Pobieranie danych dziennika rejestrowania magazynu

 Aby wyświetlić i przeanalizować dane dziennika, należy pobrać obiekty blob zawierające dane dzienników, które interesują się na komputerze lokalnym. Wiele narzędzi do przeglądania magazynu umożliwia pobieranie obiektów blob z konta magazynu; do pobrania danych dzienników można również użyć zespołu usługi Azure Storage dostępnego w wierszu polecenia usługi Azure copy [AzCopy](storage-use-azcopy-v10.md) .  
 
>[!NOTE]
> `$logs`Kontener nie jest zintegrowany z Event Grid, więc nie będziesz otrzymywać powiadomień o zapisie plików dziennika. 

 Aby upewnić się, że pobierasz dane dziennika, które Cię interesują, i unikaj pobierania tych samych danych dziennika więcej niż raz:  

-   Użyj konwencji nazewnictwa daty i godziny dla obiektów BLOB zawierających dane dziennika, aby śledzić, które obiekty blob zostały już pobrane do analizy, aby uniknąć ponownego pobierania tych samych danych więcej niż raz.  

-   Użyj metadanych obiektów BLOB zawierających dane dziennika, aby określić konkretny okres, dla którego obiekt BLOB przechowuje dane dziennika w celu zidentyfikowania dokładnego obiektu BLOB, który należy pobrać.  

Aby rozpocząć pracę z usługą AzCopy, zobacz artykuł [Rozpoczynanie pracy z usługą AzCopy](storage-use-azcopy-v10.md) 

Poniższy przykład pokazuje, jak można pobrać dane dziennika dla usługi kolejki dla godzin, począwszy od 09 AM, 10 AM i 11 AM 20 maja, 2014.

```
azcopy copy 'https://mystorageaccount.blob.core.windows.net/$logs/queue' 'C:\Logs\Storage' --include-path '2014/05/20/09;2014/05/20/10;2014/05/20/11' --recursive
```

Aby dowiedzieć się więcej o pobieraniu określonych plików, zobacz [pobieranie określonych plików](./storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#transfer-data).

Po pobraniu danych dziennika można wyświetlić wpisy dziennika w plikach. Te pliki dzienników używają rozdzielanego formatu tekstu, który umożliwia przeanalizowanie wielu narzędzi do odczytywania dzienników (Aby uzyskać więcej informacji, zobacz [monitorowanie, diagnozowanie i rozwiązywanie problemów Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)). Różne narzędzia mają różne pomieszczenia do formatowania, filtrowania, sortowania, wyszukiwania w usłudze AD zawartości plików dziennika. Aby uzyskać więcej informacji na temat formatu i zawartości pliku dziennika rejestrowania magazynu, zobacz [analityka magazynu format dziennika](/rest/api/storageservices/storage-analytics-log-format) i [analityka magazynu zarejestrowanych operacji i komunikatów o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Następne kroki

* [analityka magazynu format dziennika](/rest/api/storageservices/storage-analytics-log-format)
* [analityka magazynu zarejestrowane operacje i komunikaty o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Metryki analityka magazynu (klasyczne)](storage-analytics-metrics.md)
