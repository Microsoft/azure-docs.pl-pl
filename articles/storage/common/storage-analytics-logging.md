---
title: Rejestrowanie analityki magazynu platformy Azure
description: Użyj analityka magazynu, aby rejestrować szczegółowe informacje o żądaniach usługi Azure Storage. Zobacz, jakie żądania są rejestrowane, jak są przechowywane dzienniki, jak włączyć rejestrowanie magazynu i nie tylko.
author: normesta
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: a5d1d6af68fcbd6a5822b2652ee79c464d02241f
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200759"
---
# <a name="azure-storage-analytics-logging"></a>Rejestrowanie usługi Azure Storage Analytics

Usługa Storage Analytics rejestruje szczegółowe informacje dotyczące żądań do usługi magazynu zakończonych powodzeniem i niepowodzeniem. Tych informacji można używać na potrzeby monitorowania poszczególnych żądań i diagnozowania problemów z usługą magazynu. Żądania są rejestrowane na podstawie najlepszego wysiłku. Oznacza to, że większość żądań spowoduje powstanie rekordu dziennika, ale kompletności i terminowość dzienników analityka magazynu nie są gwarantowane. 

> [!NOTE]
> Zalecamy używanie dzienników usługi Azure Storage w Azure Monitor zamiast dzienników analityka magazynu. Dzienniki usługi Azure Storage w Azure Monitor są w publicznej wersji zapoznawczej i są dostępne do testowania wersji zapoznawczej we wszystkich regionach chmury publicznej. Ta wersja zapoznawcza umożliwia korzystanie z dzienników dla obiektów BLOB (w tym Azure Data Lake Storage Gen2), plików, kolejek i tabel. Aby dowiedzieć się więcej, zobacz dowolny z następujących artykułów:
>
> - [Monitorowanie Blob Storage platformy Azure](../blobs/monitor-blob-storage.md)
> - [Azure Files monitorowania](../files/storage-files-monitoring.md)
> - [Monitorowanie Queue Storage platformy Azure](../queues/monitor-queue-storage.md)
> - [Monitorowanie usługi Azure Table Storage](../tables/monitor-table-storage.md)

 Rejestrowanie w usłudze Storage Analytics nie jest domyślnie włączone dla Twojego konta magazynu. Można ją włączyć w [Azure Portal](https://portal.azure.com/) lub przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Instrukcje krok po kroku znajdują się w temacie [Włączanie i zarządzanie dziennikami Azure Storage Analytics (wersja klasyczna)](manage-storage-analytics-logs.md). 

Możesz również włączyć obsługę dzienników analityka magazynu programowo za pośrednictwem interfejsu API REST lub biblioteki klienta. Aby włączyć analityka magazynu dla każdej usługi, użyj [właściwości get BLOB Service](/rest/api/storageservices/Blob-Service-REST-API), [Pobierz właściwości usługi kolejkowania](/rest/api/storageservices/Get-Queue-Service-Properties)i [Pobierz właściwości usługi Table](/rest/api/storageservices/Get-Table-Service-Properties) Service. Aby zobaczyć przykład, który umożliwia analityka magazynu dzienników przy użyciu platformy .NET, zobacz [Włączanie dzienników](manage-storage-analytics-logs.md)

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
     $_.Name -match 'blob/2014/05/21/05' -and   
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


## <a name="next-steps"></a>Następne kroki

* [Włączanie dzienników Azure Storage Analytics i zarządzanie nimi (klasyczne)](manage-storage-analytics-logs.md)
* [analityka magazynu format dziennika](/rest/api/storageservices/storage-analytics-log-format)
* [analityka magazynu zarejestrowane operacje i komunikaty o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)
* [Metryki analityka magazynu (klasyczne)](storage-analytics-metrics.md)
