---
title: Przekształcanie danych przy użyciu skryptu U-SQL — Azure
description: Dowiedz się, jak przetwarzać lub przekształcać dane, uruchamiając skrypty U-SQL w usłudze Azure Data Lake Analytics COMPUTE Service — wersja 1.
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/01/2017
author: nabhishek
ms.author: abnarain
ms.custom: devx-track-csharp
robots: noindex
ms.openlocfilehash: 5931cb28721e8658a771ceea1cd94624a0c09f7c
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392922"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Przekształcanie danych przez uruchamianie skryptów U-SQL w usłudze Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](data-factory-usql-activity.md)
> * [Wersja 2 (bieżąca wersja)](../transform-data-using-data-lake-analytics.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [działanie języka U-SQL w wersji 2](../transform-data-using-data-lake-analytics.md).

Potok w usłudze Azure Data Factory przetwarza dane w połączonych usługach magazynu za pomocą połączonych usług obliczeniowych. Zawiera sekwencję działań, w których każde działanie wykonuje określoną operację przetwarzania. W tym artykule opisano **działanie programu Data Lake Analytics u-SQL** , które uruchamia skrypt **u-SQL** na połączonej usłudze **Azure Data Lake Analytics** COMPUTE. 

Utwórz konto Azure Data Lake Analytics przed utworzeniem potoku za pomocą Data Lake Analytics działania U-SQL. Aby dowiedzieć się więcej na temat Azure Data Lake Analytics, zobacz Rozpoczynanie [pracy z Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

Zapoznaj się z [samouczkiem Tworzenie pierwszego potoku](data-factory-build-your-first-pipeline.md) , aby zapoznać się ze szczegółowymi instrukcjami tworzenia fabryki danych, połączonych usług, zestawów DataSet i potoku. Użyj fragmentów kodu JSON z edytorem Data Factory lub programem Visual Studio lub Azure PowerShell, aby utworzyć jednostki Data Factory.

## <a name="supported-authentication-types"></a>Obsługiwane typy uwierzytelniania
Działanie U-SQL obsługuje poniższe typy uwierzytelniania dla Data Lake Analytics:
* Uwierzytelnianie jednostki usługi
* Uwierzytelnianie poświadczeń użytkownika (OAuth) 

Zalecamy używanie uwierzytelniania nazwy głównej usługi, szczególnie w przypadku zaplanowanego wykonywania skryptu U-SQL. Zachowanie wygaśnięcia tokenu może wystąpić z uwierzytelnianiem poświadczeń użytkownika. Aby uzyskać szczegółowe informacje dotyczące konfiguracji, zobacz sekcję [Właściwości połączonej usługi](#azure-data-lake-analytics-linked-service) .

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics połączona usługa
Tworzysz **Azure Data Lake Analytics** połączonej usługi, aby połączyć usługę obliczeniową Azure Data Lake Analytics z fabryką danych Azure. Działanie Data Lake Analytics U-SQL w potoku odwołuje się do tej połączonej usługi. 

Poniższa tabela zawiera opis właściwości ogólnych używanych w definicji JSON. Możesz wybrać między jednostką usługi a uwierzytelnianiem poświadczeń użytkownika.

| Właściwość | Opis | Wymagane |
| --- | --- | --- |
| **Wprowadź** |Właściwość Type powinna mieć wartość: **AzureDataLakeAnalytics**. |Tak |
| **accountName** |Nazwa konta Azure Data Lake Analytics. |Tak |
| **dataLakeAnalyticsUri** |Azure Data Lake Analytics identyfikator URI. |Nie |
| **Identyfikator** |Identyfikator subskrypcji platformy Azure |Nie (jeśli nie zostanie określony, zostanie użyta subskrypcja fabryki danych). |
| **resourceGroupName** |Nazwa grupy zasobów platformy Azure |Nie (jeśli nie zostanie określony, zostanie użyta Grupa zasobów fabryki danych). |

### <a name="service-principal-authentication-recommended"></a>Uwierzytelnianie jednostki usługi (zalecane)
Aby użyć uwierzytelniania nazwy głównej usługi, zarejestruj jednostkę aplikacji w Azure Active Directory (Azure AD) i Udziel jej dostępu do Data Lake Store. Aby uzyskać szczegółowe instrukcje, zobacz [uwierzytelnianie między usługami](../../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md). Należy zwrócić uwagę na następujące wartości, których można użyć do zdefiniowania połączonej usługi:
* Identyfikator aplikacji
* Klucz aplikacji 
* Identyfikator dzierżawy

Użyj uwierzytelniania nazwy głównej usługi, określając następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| **servicePrincipalId** | Określ identyfikator klienta aplikacji. | Tak |
| **servicePrincipalKey** | Określ klucz aplikacji. | Tak |
| **tenant** | Określ informacje o dzierżawie (nazwę domeny lub identyfikator dzierżawy), w których znajduje się Twoja aplikacja. Możesz ją pobrać, aktywując wskaźnik myszy w prawym górnym rogu Azure Portal. | Tak |

**Przykład: Uwierzytelnianie jednostki usługi**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

### <a name="user-credential-authentication"></a>Uwierzytelnianie poświadczeń użytkownika
Alternatywnie można użyć uwierzytelniania poświadczeń użytkownika dla Data Lake Analytics, określając następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| **zgody** | Kliknij przycisk **Autoryzuj** w edytorze Data Factory i wprowadź poświadczenia, które przypisuje automatycznie wygenerowany adres URL autoryzacji do tej właściwości. | Tak |
| **sessionId** | Identyfikator sesji OAuth z sesji autoryzacji OAuth. Każdy identyfikator sesji jest unikatowy i może być używany tylko raz. To ustawienie jest generowane automatycznie, gdy jest używany Edytor Data Factory. | Tak |

**Przykład: uwierzytelnianie poświadczeń użytkownika**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>", 
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>Wygaśnięcie tokenu
Kod autoryzacji wygenerowany przy użyciu przycisku **Autoryzuj** wygaśnie po pewnym czasie. Zapoznaj się z poniższą tabelą czasów wygaśnięcia dla różnych typów kont użytkowników. Po **wygaśnięciu tokenu** uwierzytelniania może zostać wyświetlony następujący komunikat o błędzie: błąd operacji poświadczeń: INVALID_GRANT-AADSTS70002: błąd podczas walidacji poświadczeń. AADSTS70008: podany przydział dostępu wygasł lub został odwołany. Identyfikator śledzenia: identyfikator korelacji d18629e8-af88-43c5-88e3-d8419eb1fca1: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 sygnatura czasowa: 2015-12-15 21:09:31Z

| Typ użytkownika | Wygasa po |
|:--- |:--- |
| Konta użytkowników niezarządzane przez Azure Active Directory ( @hotmail.com , @live.com itp.) |12 godzin |
| Konta użytkowników zarządzane przez Azure Active Directory (AAD) |14 dni od ostatniego uruchomienia wycinka. <br/><br/>90 dni, jeśli wycinek oparty na połączonej usłudze opartej na protokole OAuth jest uruchamiany co najmniej raz na 14 dni. |

Aby uniknąć tego błędu/rozwiązać ten problem, ponownie Autoryzuj przy użyciu przycisku **Autoryzuj** po **wygaśnięciu tokenu** i ponownym wdrożeniu połączonej usługi. Możesz również generować wartości właściwości **SessionID** i **Authorization** programowo przy użyciu kodu w następujący sposób:

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

Aby uzyskać szczegółowe informacje o klasach Data Factory używanych w kodzie, zobacz [AzureDataLakeStoreLinkedService Class](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice), [AzureDataLakeAnalyticsLinkedService Class](/dotnet/api/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice)i [AuthorizationSessionGetResponse](/dotnet/api/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse) . Dodaj odwołanie do: Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll dla klasy WindowsFormsWebAuthenticationDialog. 

## <a name="data-lake-analytics-u-sql-activity"></a>Działania języka U-SQL usługi Data Lake Analytics
Poniższy fragment kodu JSON definiuje potok z działaniem Data Lake Analytics U-SQL. Definicja działania zawiera odwołanie do utworzonej wcześniej połączonej usługi Azure Data Lake Analytics.   

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        "activities": 
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs": 
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00Z",
        "end": "2015-08-08T01:00:00Z",
        "isPaused": false
    }
}
```

W poniższej tabeli opisano nazwy i opisy właściwości, które są specyficzne dla tego działania. 

| Właściwość            | Opis                              | Wymagane                                 |
| :------------------ | :--------------------------------------- | :--------------------------------------- |
| typ                | Właściwość Type musi być ustawiona na wartość **DataLakeAnalyticsU-SQL**. | Tak                                      |
| linkedServiceName   | Odwołanie do Azure Data Lake Analytics zarejestrowanego jako połączona usługa w Data Factory | Tak                                      |
| scriptPath          | Ścieżka do folderu, który zawiera skrypt U-SQL. Nazwa pliku jest rozróżniana wielkość liter. | Nie (Jeśli używasz skryptu)                   |
| Elementu scriptlinkedservice | Połączona usługa, która łączy magazyn zawierający skrypt z fabryką danych | Nie (Jeśli używasz skryptu)                   |
| skrypt              | Określ skrypt wbudowany zamiast określania scriptPath i elementu scriptlinkedservice. Na przykład: `"script": "CREATE DATABASE test"`. | Nie (Jeśli używasz scriptPath i elementu scriptlinkedservice) |
| degreeOfParallelism | Maksymalna liczba węzłów jednocześnie używanych do uruchomienia zadania. | Nie                                       |
| priority            | Określa, które zadania z wszystkich znajdujących się w kolejce powinny zostać wybrane do uruchomienia jako pierwsze. Im niższa wartość, tym wyższy priorytet. | Nie                                       |
| parameters          | Parametry skryptu U-SQL          | Nie                                       |
| runtimeVersion      | Wersja środowiska uruchomieniowego aparatu U-SQL do użycia | Nie                                       |
| kompilacjamode     | <p>Tryb kompilacji języka U-SQL. Musi mieć jedną z następujących wartości:</p> <ul><li>**Semantyka:** Przeprowadzaj wyłącznie kontrole semantyczne i niezbędne testy Sanity.</li><li>**Pełna:** Wykonaj pełną kompilację, w tym sprawdzanie składni, optymalizację, generowanie kodu itd.</li><li>**SingleBox:** Wykonaj pełną kompilację z ustawieniem TargetType na SingleBox.</li></ul><p>Jeśli nie określisz wartości tej właściwości, serwer określi tryb optymalnej kompilacji. </p> | Nie                                       |

Zobacz [SearchLogProcessing.txt definicję](#sample-u-sql-script) skryptu dla definicji skryptu. 

## <a name="sample-input-and-output-datasets"></a>Przykładowe wejściowe i wyjściowe zestawy danych
### <a name="input-dataset"></a>Wejściowy zestaw danych
W tym przykładzie dane wejściowe znajdują się w pliku Azure Data Lake Store (użyty searchlog. tsv w folderze datalake/Input). 

```json
{
    "name": "DataLakeTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}    
```

### <a name="output-dataset"></a>Wyjściowy zestaw danych
W tym przykładzie dane wyjściowe generowane przez skrypt U-SQL są przechowywane w Azure Data Lake Store (folder datalake/Output). 

```json
{
    "name": "EventsByRegionTable",
    "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="sample-data-lake-store-linked-service"></a>Przykładowa usługa połączona Data Lake Store
Poniżej znajduje się definicja przykładowej Azure Data Lake Store połączonej usługi używanej przez zestawy danych Input/Output. 

```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
        }
    }
}
```

Opisy właściwości JSON można znaleźć w artykule [przenoszenie danych do i z Azure Data Lake Store](data-factory-azure-datalake-connector.md) artykułu. 

## <a name="sample-u-sql-script"></a>Przykładowy skrypt U-SQL

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM @in
    USING Extractors.Tsv(nullEscape:"#NULL#");

@rs1 =
    SELECT Start, Region, Duration
    FROM @searchlog
WHERE Region == "en-gb";

@rs1 =
    SELECT Start, Region, Duration
    FROM @rs1
    WHERE Start <= DateTime.Parse("2012/02/19");

OUTPUT @rs1   
    TO @out
      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);
```

Wartości parametrów **\@ in** i **\@ out** w skrypcie U-SQL są przesyłane dynamicznie przez ADF przy użyciu sekcji "Parameters". Zobacz sekcję "Parameters" w definicji potoku.

Można określić inne właściwości, takie jak degreeOfParallelism i Priority, jak również w definicji potoku dla zadań uruchamianych w usłudze Azure Data Lake Analytics.

## <a name="dynamic-parameters"></a>Parametry dynamiczne
W definicji potoku przykładowego parametry in i out są przypisywane z ustalonymi wartościami. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

Zamiast tego można użyć parametrów dynamicznych. Na przykład: 

```json
"parameters": {
    "in": "$$Text.Format('/datalake/input/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)",
    "out": "$$Text.Format('/datalake/output/{0:yyyy-MM-dd HH:mm:ss}.tsv', SliceStart)"
}
```

W takim przypadku pliki wejściowe są nadal pobierane z folderu/datalake/Input, a pliki wyjściowe są generowane w folderze/datalake/Output. Nazwy plików są dynamiczne na podstawie czasu rozpoczęcia wycinka.