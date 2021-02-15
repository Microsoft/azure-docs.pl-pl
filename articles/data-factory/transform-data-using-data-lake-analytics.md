---
title: Przekształcanie danych przy użyciu skryptu U-SQL
description: Dowiedz się, jak przetwarzać lub przekształcać dane, uruchamiając skrypty U-SQL w usłudze Azure Data Lake Analytics COMPUTE.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2018
ms.openlocfilehash: 94b584f0bea01754a4e955d1418a7a9a2e9aeace
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373916"
---
# <a name="process-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Przetwarzanie danych przez uruchamianie skryptów U-SQL na Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Wybierz używaną wersję usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-usql-activity.md)
> * [Bieżąca wersja](transform-data-using-data-lake-analytics.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Potok w usłudze Azure Data Factory przetwarza dane w połączonych usługach magazynu za pomocą połączonych usług obliczeniowych. Zawiera sekwencję działań, w których każde działanie wykonuje określoną operację przetwarzania. W tym artykule opisano **działanie programu Data Lake Analytics u-SQL** , które uruchamia skrypt **u-SQL** na połączonej usłudze **Azure Data Lake Analytics** COMPUTE. 

Utwórz konto Azure Data Lake Analytics przed utworzeniem potoku za pomocą Data Lake Analytics działania U-SQL. Aby dowiedzieć się więcej na temat Azure Data Lake Analytics, zobacz Rozpoczynanie [pracy z Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).


## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics połączona usługa
Tworzysz **Azure Data Lake Analytics** połączonej usługi, aby połączyć usługę obliczeniową Azure Data Lake Analytics z fabryką danych Azure. Działanie Data Lake Analytics U-SQL w potoku odwołuje się do tej połączonej usługi. 

Poniższa tabela zawiera opis właściwości ogólnych używanych w definicji JSON. 

| Właściwość                 | Opis                              | Wymagane                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **Wprowadź**                 | Właściwość Type powinna mieć wartość: **AzureDataLakeAnalytics**. | Tak                                      |
| **accountName**          | Nazwa konta Azure Data Lake Analytics.  | Tak                                      |
| **dataLakeAnalyticsUri** | Azure Data Lake Analytics identyfikator URI.           | Nie                                       |
| **Identyfikator**       | Identyfikator subskrypcji platformy Azure                    | Nie                                       |
| **resourceGroupName**    | Nazwa grupy zasobów platformy Azure                | Nie                                       |

### <a name="service-principal-authentication"></a>Uwierzytelnianie jednostki usługi
Połączona usługa Azure Data Lake Analytics wymaga uwierzytelniania jednostki usługi w celu nawiązania połączenia z usługą Azure Data Lake Analytics. Aby skorzystać z uwierzytelniania jednostki usługi, zarejestruj jednostkę aplikacji w Azure Active Directory (Azure AD) i Udziel jej dostępu zarówno do Data Lake Analytics, jak i Data Lake Store. Aby uzyskać szczegółowe instrukcje, zobacz [uwierzytelnianie między usługami](../data-lake-store/data-lake-store-service-to-service-authenticate-using-active-directory.md). Należy zwrócić uwagę na następujące wartości, których można użyć do zdefiniowania połączonej usługi:

* Identyfikator aplikacji
* Klucz aplikacji 
* Identyfikator dzierżawy

Udziel uprawnienia nazwy głównej usługi do Azure Data Lake anatlyics za pomocą [Kreatora dodawania użytkownika](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#add-a-new-user).

Użyj uwierzytelniania nazwy głównej usługi, określając następujące właściwości:

| Właściwość                | Opis                              | Wymagane |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Określ identyfikator klienta aplikacji.     | Tak      |
| **servicePrincipalKey** | Określ klucz aplikacji.           | Tak      |
| **tenant**              | Określ informacje o dzierżawie (nazwę domeny lub identyfikator dzierżawy), w których znajduje się Twoja aplikacja. Możesz ją pobrać, aktywując wskaźnik myszy w prawym górnym rogu Azure Portal. | Tak      |

**Przykład: Uwierzytelnianie jednostki usługi**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "<azure data lake analytics URI>",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "value": "<service principal key>",
                "type": "SecureString"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }       
    }
}
```

Aby dowiedzieć się więcej na temat połączonej usługi, zobacz [połączone usługi obliczeniowe](compute-linked-services.md).

## <a name="data-lake-analytics-u-sql-activity"></a>Działania języka U-SQL usługi Data Lake Analytics
Poniższy fragment kodu JSON definiuje potok z działaniem Data Lake Analytics U-SQL. Definicja działania zawiera odwołanie do utworzonej wcześniej połączonej usługi Azure Data Lake Analytics. Aby wykonać Data Lake Analytics skrypt U-SQL, Data Factory przesyła skrypt określony do Data Lake Analytics, a wymagane dane wejściowe i wyjściowe są zdefiniowane w skrypcie dla Data Lake Analytics do pobrania i wygenerowania danych wyjściowych. 

```json
{
    "name": "ADLA U-SQL Activity",
    "description": "description",
    "type": "DataLakeAnalyticsU-SQL",
    "linkedServiceName": {
        "referenceName": "<linked service name of Azure Data Lake Analytics>",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "<linked service name of Azure Data Lake Store or Azure Storage which contains the U-SQL script>",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
        "degreeOfParallelism": 3,
        "priority": 100,
        "parameters": {
            "in": "/datalake/input/SearchLog.tsv",
            "out": "/datalake/output/Result.tsv"
        }
    }   
}
```

W poniższej tabeli opisano nazwy i opisy właściwości, które są specyficzne dla tego działania. 

| Właściwość            | Opis                              | Wymagane |
| :------------------ | :--------------------------------------- | :------- |
| name                | Nazwa działania w potoku     | Tak      |
| description (opis)         | Tekst opisujący działanie działania.  | Nie       |
| typ                | W przypadku Data Lake Analytics działania U-SQL typ działania to  **DataLakeAnalyticsU-SQL**. | Tak      |
| linkedServiceName   | Połączona usługa do Azure Data Lake Analytics. Aby dowiedzieć się więcej o tej połączonej usłudze, zobacz artykuł dotyczący [połączonych usług obliczeniowych](compute-linked-services.md) .  |Tak       |
| scriptPath          | Ścieżka do folderu, który zawiera skrypt U-SQL. Nazwa pliku jest rozróżniana wielkość liter. | Tak      |
| Elementu scriptlinkedservice | Połączona usługa łącząca **Azure Data Lake Store** lub **Magazyn platformy Azure** , który zawiera skrypt do fabryki danych | Tak      |
| degreeOfParallelism | Maksymalna liczba węzłów jednocześnie używanych do uruchomienia zadania. | Nie       |
| priority            | Określa, które zadania z wszystkich znajdujących się w kolejce powinny zostać wybrane do uruchomienia jako pierwsze. Im niższa wartość, tym wyższy priorytet. | Nie       |
| parameters          | Parametry do przekazania do skryptu U-SQL.    | Nie       |
| runtimeVersion      | Wersja środowiska uruchomieniowego aparatu U-SQL do użycia. | Nie       |
| kompilacjamode     | <p>Tryb kompilacji języka U-SQL. Musi mieć jedną z następujących wartości: **semantyka:** wykonaj tylko testy semantyczne i niezbędne kontrole Sanity, **pełne:** wykonaj pełną kompilację, w tym sprawdzanie składni, optymalizację, generowanie kodu itp., **SingleBox:** wykonaj pełną kompilację z ustawieniem TargetType na SingleBox. Jeśli nie określisz wartości tej właściwości, serwer określi tryb optymalnej kompilacji. | Nie |

Zobacz [SearchLogProcessing.txt](#sample-u-sql-script) definicji skryptu. 

## <a name="sample-u-sql-script"></a>Przykładowy skrypt U-SQL

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int,
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

W powyższym przykładzie skryptu dane wejściowe i wyjściowe do skryptu są zdefiniowane w parametrze **\@ in** i **\@ out** . Wartości parametrów **\@ in** i **\@ out** w skrypcie U-SQL są przesyłane dynamicznie przez Data Factory przy użyciu sekcji "Parameters". 

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
    "in": "/datalake/input/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/data.tsv",
    "out": "/datalake/output/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/result.tsv"
}
```

W takim przypadku pliki wejściowe są nadal pobierane z folderu/datalake/Input, a pliki wyjściowe są generowane w folderze/datalake/Output. Nazwy plików są dynamiczne na podstawie czasu rozpoczęcia okna, gdy zostanie wyzwolone potoku.  

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z następującymi artykułami, które wyjaśniają sposób przekształcania danych w inny sposób: 

* [Działanie Hive](transform-data-using-hadoop-hive.md)
* [Aktywność trzody chlewnej](transform-data-using-hadoop-pig.md)
* [Działanie MapReduce](transform-data-using-hadoop-map-reduce.md)
* [Działanie przesyłania strumieniowego Hadoop](transform-data-using-hadoop-streaming.md)
* [Działanie platformy Spark](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie wykonywania wsadowego w Azure Machine Learning Studio (klasycznej)](transform-data-using-machine-learning.md)
* [Działanie procedury składowanej](transform-data-using-stored-procedure.md)