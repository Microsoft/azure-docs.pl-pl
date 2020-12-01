---
title: Jak dodać strumieniowe źródła danych z różnych źródeł do klasyfikatora metryk
titleSuffix: Azure Cognitive Services
description: Dodawanie różnych strumieniowych źródeł danych do klasyfikatora metryk
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: conceptual
ms.date: 10/12/2020
ms.author: mbullwin
ms.openlocfilehash: c4d1d23da5fd9678cc5b9477ddeed0daf4f5ac36
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348623"
---
# <a name="add-data-feeds-from-different-data-sources-to-metrics-advisor"></a>Dodawanie strumieniowych źródeł danych z różnych źródeł danych do klasyfikatora metryk

Skorzystaj z tego artykułu, aby znaleźć ustawienia i wymagania dotyczące łączenia różnych typów źródeł danych z usługą Metric Advisor. Zapoznaj się z informacjami na temat sposobu dołączania [danych](how-tos/onboard-your-data.md) , aby dowiedzieć się więcej o najważniejszych pojęciach dotyczących używania danych za pomocą usługi Metric Advisor. 

## <a name="supported-authentication-types"></a>Obsługiwane typy uwierzytelniania

| Typy uwierzytelniania | Opis |
| ---------------------|-------------|
|**Podstawowe** | Musisz mieć możliwość udostępnienia podstawowych parametrów dostępu do źródeł danych. Na przykład parametry połączenia lub klucz. Administratorzy strumieniowego źródła danych mogą wyświetlać te poświadczenia. |
| **AzureManagedIdentity** | [Zarządzane tożsamości](../../active-directory/managed-identities-azure-resources/overview.md) dla zasobów platformy Azure to funkcja Azure Active Directory. Zapewnia usługi platformy Azure z automatyczną tożsamością zarządzaną w usłudze Azure AD. Tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie w usłudze Azure AD.|
| **AzureSQLConnectionString**| Przechowuj parametry połączenia AzureSQL jako **jednostkę poświadczeń** w usłudze Advisor metryk i używaj jej bezpośrednio za każdym razem podczas dołączania danych metryk. Tylko Administratorzy jednostki Credential mogą wyświetlać te poświadczenia, ale umożliwiają autoryzowanym podglądom tworzenie strumieniowych źródeł danych bez konieczności poznania szczegółowych informacji dotyczących poświadczeń. |
| **DataLakeGen2SharedKey**| Przechowuj klucz konta usługi Data Lake jako **jednostkę poświadczeń** w usłudze Metrics Advisor i używaj go bezpośrednio za każdym razem podczas dołączania danych metryk. Tylko Administratorzy jednostki Credential mogą wyświetlać te poświadczenia, ale umożliwiają autoryzowanym użytkownikom tworzenie strumieniowych źródeł danych bez znajomości szczegółowych informacji o poświadczeniu.|
| **Jednostka usługi**| Przechowuj swoją nazwę główną usługi jako **jednostkę poświadczeń** w usłudze Metrics Advisor i używaj jej bezpośrednio za każdym razem podczas dołączania danych metryk. Tylko Administratorzy jednostki poświadczeń mogą wyświetlać poświadczenia, ale umożliwiają autoryzowanym podglądom tworzenie strumieniowego źródła danych bez znajomości szczegółowych informacji o poświadczeniu.|
| **Nazwa główna usługi z magazynu kluczy**|Przechowuj swoją nazwę główną usługi w magazynie kluczy jako **jednostkę poświadczeń** w usłudze Metrics Advisor i używaj jej bezpośrednio za każdym razem podczas dołączania danych metryk. Tylko Administratorzy **jednostki poświadczeń** mogą wyświetlać poświadczenia, ale również pozostawiać podglądy mogące tworzyć strumieniowe źródła danych bez znajomości szczegółowych poświadczeń. |

## <a name="data-sources-supported-and-corresponding-authentication-types"></a>Obsługiwane źródła danych i odpowiednie typy uwierzytelniania


| Źródła danych | Typy uwierzytelniania |
|-------------| ---------------------|
|[**Azure Application Insights**](#appinsights)|  Podstawowy |
|[**Azure Blob Storage (JSON)**](#blob) | Podstawowy<br>ManagedIdentity|
|[**Azure Cosmos DB (SQL)**](#cosmosdb) | Podstawowy |
|[**Azure Data Explorer (Kusto)**](#kusto) | Podstawowy<br>ManagedIdentity|
|[**Usługa Azure Data Lake Storage 2. generacji**](#adl) | Podstawowy<br>DataLakeGen2SharedKey<br>Jednostka usługi<br>Nazwa główna usługi z magazynu kluczy<br> |
|[**Azure SQL Database/SQL Server**](#sql) | Podstawowy<br>ManagedIdentity<br>Jednostka usługi<br>Nazwa główna usługi z magazynu kluczy<br>AzureSQLConnectionString
|[**Azure Table Storage**](#table) | Podstawowy | 
|[**ElasticSearch**](#es) | Podstawowy |
|[**Żądanie http**](#http) | Podstawowy | 
|[**InfluxDB (InfluxQL)**](#influxdb) | Podstawowy |
|[**MongoDB**](#mongodb) | Podstawowy |
|[**MySQL**](#mysql) | Podstawowy |
|[**PostgreSQL**](#pgsql)| Podstawowy|

Utwórz **jednostkę poświadczeń** i użyj jej do uwierzytelniania w źródłach danych. W poniższych sekcjach określono parametry wymagane przez program do uwierzytelniania *podstawowego* . 

## <a name="span-idappinsightsazure-application-insightsspan"></a><span id="appinsights">Azure Application Insights</span>

* **Identyfikator aplikacji**: służy do identyfikowania tej aplikacji podczas korzystania z interfejsu API Application Insights. Aby uzyskać identyfikator aplikacji, wykonaj następujące czynności:

    1. W ramach zasobu Application Insights kliknij pozycję dostęp do interfejsu API.

    2. Skopiuj identyfikator aplikacji wygenerowany do pola **Identyfikator aplikacji** w klasyfikatorze metryk. 
    
    Aby uzyskać więcej informacji, zobacz [dokumentację Azure bot Service](/azure/bot-service/bot-service-resources-app-insights-keys#application-id) .

* **Klucz interfejsu API**: klucze interfejsu API są używane przez aplikacje poza przeglądarką, aby uzyskać dostęp do tego zasobu. Aby uzyskać klucz interfejsu API, wykonaj następujące czynności:

    1. W obszarze zasób Application Insights kliknij pozycję dostęp do interfejsu API.

    2. Kliknij przycisk Utwórz klucz interfejsu API.

    3. Wprowadź krótki opis, zaznacz opcję Odczytaj dane telemetryczne, a następnie kliknij przycisk Generuj klucz.

    4. Skopiuj klucz interfejsu API do pola **klucz interfejsu API** w usłudze Metrics Advisor.

* **Zapytanie**: dzienniki usługi Azure Application Insights są oparte na usłudze Azure Eksplorator danych, a Azure monitor zapytania dzienników używają wersji tego samego języka zapytań Kusto. [Dokumentacja języka zapytań Kusto](/azure/data-explorer/kusto/query/) zawiera wszystkie szczegóły dotyczące języka i powinna być podstawowym zasobem do pisania zapytania względem Application Insights. 


## <a name="span-idblobazure-blob-storage-jsonspan"></a><span id="blob">Azure Blob Storage (JSON)</span>

* **Parametry połączenia**: Zobacz artykuł dotyczący [parametrów połączenia](../../storage/common/storage-configure-connection-string.md#configure-a-connection-string-for-an-azure-storage-account) BLOB Storage platformy Azure, aby uzyskać informacje na temat pobierania tego ciągu.

* **Kontener**: Doradca metryki oczekuje, że dane szeregów czasowych są przechowywane jako pliki obiektów BLOB (jeden obiekt BLOB na sygnaturę czasową) w ramach jednego kontenera. Jest to pole nazwa kontenera.

* **Szablon obiektu BLOB**: jest to szablon nazw plików obiektów BLOB. Przykład: `/%Y/%m/X_%Y-%m-%d-%h-%M.json`. Obsługiwane są następujące parametry:
  * `%Y` jest rokiem sformatowanym jako `yyyy`
  * `%m` jest miesiącem sformatowanym jako `MM`
  * `%d` jest dniem sformatowanym jako `dd`
  * `%h` jest godziną sformatowaną jako `HH`
  * `%M` jest minutą sformatowaną jako `mm`

* **Wersja formatu JSON**: definiuje schemat danych w plikach JSON. Obecnie klasyfikator metryk obsługuje dwie wersje:
  
  * V1 (wartość domyślna)

      Tylko *Nazwa* metryki i *wartość* są akceptowane. Przykład:
    
      ``` JSON
      {"count":11, "revenue":1.23}
      ```

  * v2

      Są również akceptowane *Wymiary* metryk i *sygnatura czasowa* . Przykład:
      
      ``` JSON
      [
        {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
        {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
      ]
      ```

Dozwolony jest tylko jeden znacznik czasu na plik JSON. 

## <a name="span-idcosmosdbazure-cosmos-db-sqlspan"></a><span id="cosmosdb">Azure Cosmos DB (SQL)</span>

* **Parametry połączenia**: parametry połączenia, aby uzyskać dostęp do Azure Cosmos DB. Ten temat można znaleźć w zasobie Cosmos DB w **kluczach**. 
* **Baza danych**: baza danych, względem której należy wykonać zapytanie. Ten temat można znaleźć na stronie **przeglądanie** w obszarze **kontenery** .
* **Identyfikator kolekcji**: identyfikator kolekcji, względem której należy wykonać zapytanie. Ten temat można znaleźć na stronie **przeglądanie** w obszarze **kontenery** .
* **Zapytanie SQL**: zapytanie SQL umożliwiające pobieranie i formułowanie danych w wielowymiarowych danych szeregów czasowych. `@StartTime` `@EndTime` W zapytaniu można używać zmiennych i. Powinny być sformatowane: `yyyy-MM-dd HH:mm:ss` .

    Przykładowe zapytanie:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    Przykładowe zapytanie dla wycinka danych z 2019/12/12:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idkustoazure-data-explorer-kustospan"></a><span id="kusto">Azure Data Explorer (Kusto)</span>

* **Parametry połączenia**: Klasyfikator metryk obsługuje dostęp do usługi Azure Eksplorator danych (Kusto) przy użyciu uwierzytelniania aplikacji usługi Azure AD. Należy utworzyć i zarejestrować aplikację usługi Azure AD, a następnie autoryzować ją w celu uzyskania dostępu do bazy danych Azure Eksplorator danych Database. Aby uzyskać parametry połączenia, zobacz dokumentację [usługi Azure Eksplorator danych](/azure/data-explorer/provision-azure-ad-app) .

* **Zapytanie**: zobacz [język zapytań Kusto](/azure/data-explorer/kusto/query) , aby uzyskać i sformułować dane w wielowymiarowych danych szeregów czasowych. `@StartTime` `@EndTime` W zapytaniu można używać zmiennych i. Powinny być sformatowane: `yyyy-MM-dd HH:mm:ss` .

## <a name="span-idadlazure-data-lake-storage-gen2span"></a><span id="adl">Azure Data Lake Storage Gen2</span>

* **Nazwa konta**: nazwa konta Azure Data Lake Storage Gen2. Ten temat można znaleźć na liście **kluczy dostępu** dla zasobu konta usługi Azure Storage (Azure Data Lake Storage Gen2).

* **Klucz konta**: Podaj nazwę konta, aby uzyskać dostęp do Azure Data Lake Storage Gen2. Można to znaleźć w obszarze zasób konta usługi Azure Storage (Azure Data Lake Storage Gen2) w ustawieniu **klucze dostępu** .

* **Nazwa systemu plików (kontener)**: Klasyfikator metryk będzie oczekiwać, że dane szeregów czasowych są przechowywane jako pliki obiektów BLOB (jeden obiekt BLOB na sygnaturę czasową) w ramach jednego kontenera. Jest to pole nazwa kontenera. Można je znaleźć w wystąpieniu konta usługi Azure Storage (Azure Data Lake Storage Gen2), a następnie kliknąć pozycję "Containers" w sekcji "BLOB Service".

* **Szablon katalogu**: jest to szablon katalogu pliku obiektu BLOB. Na przykład: */%Y/%m/%d*. Obsługiwane są następujące parametry:
  * `%Y` jest rokiem sformatowanym jako `yyyy`
  * `%m` jest miesiącem sformatowanym jako `MM`
  * `%d` jest dniem sformatowanym jako `dd`
  * `%h` jest godziną sformatowaną jako `HH`
  * `%M` jest minutą sformatowaną jako `mm`

* **Szablon pliku**: jest to szablon pliku obiektu BLOB. Na przykład: *x_% Y-% m-% d-% h-% M.json*. Obsługiwane są następujące parametry:
  * `%Y` jest rokiem sformatowanym jako `yyyy`
  * `%m` jest miesiącem sformatowanym jako `MM`
  * `%d` jest dniem sformatowanym jako `dd`
  * `%h` jest godziną sformatowaną jako `HH`
  * `%M` jest minutą sformatowaną jako `mm`

Obecnie klasyfikator metryk obsługuje schemat danych w plikach JSON. Przykład:

``` JSON
[
  {"date": "2018-01-01T00:00:00Z", "market":"en-us", "count":11, "revenue":1.23},
  {"date": "2018-01-01T00:00:00Z", "market":"zh-cn", "count":22, "revenue":4.56}
]
```
<!--
## <span id="eventhubs">Azure Event Hubs</span>

* **Connection String**: This can be found in 'Shared access policies' in your Event Hubs instance. Also for the 'EntityPath', it could be found by clicking into your Event Hubs instance and clicking at 'Event Hubs' in 'Entities' blade. Items that listed can be input as EntityPath. 

* **Consumer Group**: A [consumer group](../../event-hubs/event-hubs-features.md#consumer-groups) is a view (state, position, or offset) of an entire event hub.
Event Hubs use the latest offset of a consumer group to consume (subscribe from) the data from data source. Therefore a dedicated consumer group should be created for one data feed in your Metrics Advisor instance.

* **Timestamp**: Metrics Advisor uses the Event Hubs timestamp as the event timestamp if the user data source does not contain a timestamp field.
The timestamp field must match one of these two formats:

    * "YYYY-MM-DDTHH:MM:SSZ" format;

    * Number of seconds or milliseconds from the epoch of 1970-01-01T00:00:00Z.

    No matter which timestamp field it left aligns to granularity.For example, if timestamp is "2019-01-01T00:03:00Z", granularity is 5 minutes, then Metrics Advisor aligns the timestamp to "2019-01-01T00:00:00Z". If the event timestamp is "2019-01-01T00:10:00Z",  Metrics Advisor uses the timestamp directly without any alignment.
-->
## <a name="span-idsqlazure-sql-database--sql-serverspan"></a><span id="sql">Azure SQL Database | SQL Server</span>

* **Parametry połączenia**: Klasyfikator metryk akceptuje [Parametry połączenia w stylu ADO.NET](/dotnet/framework/data/adonet/connection-string-syntax) dla źródła danych programu SQL Server.

    Przykładowe parametry połączenia:

    ```
    Data Source=db-server.database.windows.net:[port];initial catalog=[database];User ID=[username];Password=[password];Connection Timeout=10ms;
    ```

* **Zapytanie**: zapytanie SQL umożliwiające pobieranie i formułowanie danych w wielowymiarowych danych szeregów czasowych. Możesz użyć `@StartTime` zmiennej w zapytaniu, aby uzyskać pomoc przy pobieraniu oczekiwanej wartości metryk.

  * `@StartTime`: DateTime w formacie `yyyy-MM-dd HH:mm:ss`

    Przykładowe zapytanie:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = @StartTime
    ```
    
    Rzeczywiste zapytanie wykonane dla wycinka danych 2019/12/12:
    
    ``` mssql
    select StartDate, JobStatusId, COUNT(*) AS JobNumber from IngestionJobs WHERE and StartDate = '2019-12-12 00:00:00'
    ```

## <a name="span-idtableazure-table-storagespan"></a><span id="table">Azure Table Storage</span>

* **Parametry połączenia**: należy zapoznać się z tematem [Wyświetlanie i kopiowanie parametrów połączenia](../../storage/common/storage-account-keys-manage.md?tabs=azure-portal&toc=%2fazure%2fstorage%2ftables%2ftoc.json#view-account-access-keys) , aby uzyskać informacje na temat pobierania parametrów połączenia z usługi Azure Table Storage.

* **Nazwa tabeli**: Określ tabelę, względem której ma zostać wyszukiwane zapytanie. Ten temat można znaleźć w wystąpieniu konta usługi Azure Storage. Kliknij pozycję **tabele** w sekcji **usługi tabel** .

* **Zapytanie** Można użyć `@StartTime` w zapytaniu. `@StartTime` jest zastępowany ciągiem formatu RRRR-MM-DDTgg: mm: SS w skrypcie.

    ``` mssql
    let StartDateTime = datetime(@StartTime); let EndDateTime = StartDateTime + 1d; 
    SampleTable | where Timestamp >= StartDateTime and Timestamp < EndDateTime | project Timestamp, Market, RPM
    ```

## <a name="span-ideselasticsearchspan"></a><span id="es">Elasticsearch</span>

* **Host**: Określ główny host klastra Elasticsearch.
* **Port**: Określ port główny klastra Elasticsearch.
* **Nagłówek autoryzacji**: Określ wartość nagłówka autoryzacji klastra Elasticsearch.
* **Zapytanie**: Określ zapytanie, aby pobrać dane. Symbol zastępczy @StartTime jest obsługiwany. ( na przykład w przypadku, gdy dane z 2020-06-21T00:00:00Z są pozyskiwane, @StartTime = 2020-06-21T00:00:00)

## <a name="span-idhttphttp-requestspan"></a><span id="http">Żądanie HTTP</span>

* **Adres URL żądania**: adres URL http, który może zwracać kod JSON. Symbole zastępcze% Y,% m,% d,% h,% M są obsługiwane:% Y = rok w formacie RRRR,% m = miesiąc w formacie MM,% d = dzień w formacie DD,% h = godzina w formacie gg,% M = minuta w formacie mm. Przykład: `http://microsoft.com/ProjectA/%Y/%m/X_%Y-%m-%d-%h-%M`.
* **Metoda żądania HTTP**: Użyj metody get lub post.
* **Nagłówek żądania**: może dodać podstawowe uwierzytelnianie. 
* **Ładunek żądania**: obsługiwany jest tylko ładunek JSON. Symbol zastępczy @StartTime jest obsługiwany w ładunku. Odpowiedź powinna mieć następujący format JSON: [{"timestamp": "2018 r-01-01T00:00:00Z", "rynek": "en-us", "Count": 11 "przychód": 1,23}, {"timestamp": "2018 r-01-01T00:00:00Z", "rynek": "zh-CN", "Count": 22, "przychód": da liczbę 4,56}]. (np. w przypadku pozyskiwania danych z 2020-06-21T00:00:00Z, @StartTime = 2020-06-21T00:00:00.0000000 + 00:00)

## <a name="span-idinfluxdbinfluxdb-influxqlspan"></a><span id="influxdb">InfluxDB (InfluxQL)</span>

* **Parametry połączenia**: parametry połączenia w celu uzyskania dostępu do InfluxDB.
* **Baza danych**: baza danych, względem której należy wykonać zapytanie.
* **Zapytanie**: zapytanie umożliwiające pobieranie i formułowanie danych w wielowymiarowych danych szeregów czasowych na potrzeby pozyskiwania.
* **Nazwa użytkownika**: to jest opcjonalne w przypadku uwierzytelniania. 
* **Hasło**: to jest opcjonalne do uwierzytelniania. 

## <a name="span-idmongodbmongodbspan"></a><span id="mongodb">MongoDB</span>

* **Parametry połączenia**: parametry połączenia w celu uzyskania dostępu do MongoDB.
* **Baza danych**: baza danych, względem której należy wykonać zapytanie.
* **Polecenie**: polecenie, aby pobrać i sformułować dane do wielowymiarowych danych szeregów czasowych na potrzeby pozyskiwania.

## <a name="span-idmysqlmysqlspan"></a><span id="mysql">MySQL</span>

* **Parametry połączenia**: parametry połączenia w celu uzyskania dostępu do bazy danych MySQL.
* **Zapytanie**: zapytanie umożliwiające pobieranie i formułowanie danych w wielowymiarowych danych szeregów czasowych na potrzeby pozyskiwania.

## <a name="span-idpgsqlpostgresqlspan"></a><span id="pgsql">PostgreSQL</span>

* **Parametry połączenia**: parametry połączenia, aby uzyskać dostęp do usługi PostgreSQL DB.
* **Zapytanie**: zapytanie umożliwiające pobieranie i formułowanie danych w wielowymiarowych danych szeregów czasowych na potrzeby pozyskiwania.

## <a name="next-steps"></a>Następne kroki

* Podczas oczekiwania na pozyskanie danych metryk w systemie Przeczytaj o [sposobach zarządzania konfiguracjami strumieniowego źródła danych](how-tos/manage-data-feeds.md).
* Gdy dane metryk są pozyskiwane, można [skonfigurować metryki i dostosować konfigurację wykrywania](how-tos/configure-metrics.md).