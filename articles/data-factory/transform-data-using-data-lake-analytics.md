---
title: Przekształcanie danych przy użyciu skryptu U-SQL
description: Dowiedz się, jak przetwarzać lub przekształcać dane, uruchamiając skrypty U-SQL w usłudze obliczeniowej usługi Azure Data Lake Analytics.
services: data-factory
documentationcenter: ''
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2018
ms.openlocfilehash: 427b7fff7b8f76412d7bd9d63aeb64583637779c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418970"
---
# <a name="transform-data-by-running-u-sql-scripts-on-azure-data-lake-analytics"></a>Przekształcanie danych przez uruchamianie skryptów U-SQL w usłudze Azure Data Lake Analytics 
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](v1/data-factory-usql-activity.md)
> * [Bieżąca wersja](transform-data-using-data-lake-analytics.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Potok w fabryce danych platformy Azure przetwarza dane w połączonych usługach magazynu przy użyciu połączonych usług obliczeniowych. Zawiera sekwencję działań, w których każde działanie wykonuje określoną operację przetwarzania. W tym artykule opisano **działanie U-SQL analizy usługi Data Lake Analytics,** która uruchamia skrypt **U-SQL** w połączonej usłudze obliczeniowej **usługi Azure Data Lake Analytics.** 

Utwórz konto usługi Azure Data Lake Analytics przed utworzeniem potoku z aktywność U-SQL analizy usługi Data Lake Analytics. Aby dowiedzieć się więcej o usłudze Azure Data Lake Analytics, zobacz [Wprowadzenie do usługi Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).


## <a name="azure-data-lake-analytics-linked-service"></a>Usługa połączona z usługą Azure Data Lake Analytics
Utwórz usługę połączony **usługi Usługi Azure Data Lake Analytics,** aby połączyć usługę obliczeniową usługi Azure Data Lake Analytics z fabryką danych platformy Azure. Działanie U-SQL usługi Data Lake Analytics w potoku odnosi się do tej połączonej usługi. 

Poniższa tabela zawiera opisy właściwości ogólnych używanych w definicji JSON. 

| Właściwość                 | Opis                              | Wymagany                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| **Typu**                 | Właściwość typu powinna być ustawiona na: **AzureDataLakeAnalytics**. | Tak                                      |
| **Accountname**          | Nazwa konta usługi Azure Data Lake Analytics.  | Tak                                      |
| **dataLakeAnalyticsUri** | Identyfikator URI usługi Azure Data Lake Analytics.           | Nie                                       |
| **Subscriptionid**       | Identyfikator subskrypcji platformy Azure                    | Nie                                       |
| **nazwa grupy zasobów**    | Nazwa grupy zasobów platformy Azure                | Nie                                       |

### <a name="service-principal-authentication"></a>Uwierzytelnianie jednostki usługi
Usługa połączona usługi Azure Data Lake Analytics wymaga uwierzytelniania głównego usługi, aby połączyć się z usługą Azure Data Lake Analytics. Aby użyć uwierzytelniania jednostki usługi, zarejestruj jednostkę aplikacji w usłudze Azure Active Directory (Azure AD) i udziel jej dostępu zarówno do usługi Data Lake Analytics, jak i do używanego magazynu usługi Data Lake. Aby uzyskać szczegółowe kroki, zobacz [Uwierzytelnianie usługi do usługi](../data-lake-store/data-lake-store-authenticate-using-active-directory.md). Zanotuj następujące wartości, których używasz do definiowania połączonej usługi:

* Identyfikator aplikacji
* Klucz aplikacji 
* Identyfikator dzierżawy

Udziel uprawnień jednostki usługi do usługi Azure Data Lake Anatlyics za pomocą [Kreatora dodawania użytkownika.](../data-lake-analytics/data-lake-analytics-manage-use-portal.md#add-a-new-user)

Użyj uwierzytelniania głównego usługi, określając następujące właściwości:

| Właściwość                | Opis                              | Wymagany |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | Określ identyfikator klienta aplikacji.     | Tak      |
| **servicePrincipalKey** | Określ klucz aplikacji.           | Tak      |
| **Dzierżawy**              | Określ informacje o dzierżawie (nazwa domeny lub identyfikator dzierżawy), w którym znajduje się aplikacja. Można go pobrać, najeżdżając myszą w prawym górnym rogu witryny Azure portal. | Tak      |

**Przykład: uwierzytelnianie jednostki usługi**
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

Aby dowiedzieć się więcej o połączonej usłudze, zobacz [Obliczenie połączonych usług](compute-linked-services.md).

## <a name="data-lake-analytics-u-sql-activity"></a>Działania języka U-SQL usługi Data Lake Analytics
Poniższy fragment kodu JSON definiuje potok z działaniem U-SQL analizy usługi Data Lake Analytics. Definicja działania ma odwołanie do usługi połączonej usługi Usługi Azure Data Lake Analytics utworzonej wcześniej. Aby wykonać skrypt U-SQL usługi Data Lake Analytics, usługa Data Factory przesyła skrypt określony do usługi Data Lake Analytics, a wymagane dane wejściowe i wyjściowe są zdefiniowane w skrypcie dla usługi Data Lake Analytics w celu pobrania i danych wyjściowych. 

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

| Właściwość            | Opis                              | Wymagany |
| :------------------ | :--------------------------------------- | :------- |
| name                | Nazwa działania w potoku     | Tak      |
| description         | Tekst opisujący działanie.  | Nie       |
| type                | W przypadku aktywności U-SQL usługi Data Lake Analytics typem działania jest **DataLakeAnalyticsU-SQL**. | Tak      |
| linkedServiceName   | Usługa połączona z usługą Azure Data Lake Analytics. Aby dowiedzieć się więcej o tej połączonej usłudze, zobacz Artykuł [dotyczący powiązanych usług obliczeń.](compute-linked-services.md)  |Tak       |
| scriptPath          | Ścieżka do folderu zawierającego skrypt U-SQL. W nazwie pliku jest rozróżniana wielkość liter. | Tak      |
| scriptLinkedService | Połączona usługa łącząca **magazyn usługi Azure Data Lake Store** lub usługę Azure **Storage** zawierającą skrypt z fabryką danych | Tak      |
| stopieńParalelizm | Maksymalna liczba węzłów jednocześnie używanych do uruchamiania zadania. | Nie       |
| priority            | Określa, które zadania ze wszystkich, które są w kolejce, powinny być wybrane do uruchomienia jako pierwsze. Im mniejsza liczba, tym wyższy priorytet. | Nie       |
| parameters          | Parametry do przekazania do skryptu U-SQL.    | Nie       |
| wersja runtimeVersion      | Wersja runtime aparatu U-SQL do użycia. | Nie       |
| kompilacjaMode     | <p>Tryb kompilacji U-SQL. Musi być jedną z następujących wartości: **Semantyczne:** Tylko wykonać kontrole semantyczne i niezbędne testy poczytalności, **Pełna:** Wykonaj pełną kompilację, w tym sprawdzanie składni, optymalizacja, generowanie kodu, itp., **SingleBox:** Wykonaj pełną kompilację, z targettype ustawienie SingleBox. Jeśli nie określisz wartości dla tej właściwości, serwer określa tryb kompilacji optymalne. | Nie |

Definicję skryptu można znaleźć w [pliku SearchLogProcessing.txt.](#sample-u-sql-script) 

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

W powyższym przykładzie skryptu dane wejściowe i wyjściowe do skryptu jest zdefiniowany ** \@w** i ** \@obecnie** parametrów. Wartości parametrów ** \@in** i ** \@out** w skrypcie U-SQL są przekazywane dynamicznie przez usługę Data Factory przy użyciu sekcji "parametry". 

Można określić inne właściwości, takie jak stopieńParallelism i priorytet, a także w definicji potoku dla zadań, które są uruchamiane w usłudze Azure Data Lake Analytics.

## <a name="dynamic-parameters"></a>Parametry dynamiczne
W definicji potoku próbki parametry in i out są przypisywane z wartościami zakodowanych na czas. 

```json
"parameters": {
    "in": "/datalake/input/SearchLog.tsv",
    "out": "/datalake/output/Result.tsv"
}
```

Zamiast tego można użyć parametrów dynamicznych. Przykład: 

```json
"parameters": {
    "in": "/datalake/input/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/data.tsv",
    "out": "/datalake/output/@{formatDateTime(pipeline().parameters.WindowStart,'yyyy/MM/dd')}/result.tsv"
}
```

W takim przypadku pliki wejściowe są nadal pobierane z folderu /datalake/input, a pliki wyjściowe są generowane w folderze /datalake/output. Nazwy plików są dynamiczne na podstawie czasu rozpoczęcia okna przekazywane w momencie wyzwalania potoku.  

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły, które wyjaśniają, jak przekształcać dane w inny sposób: 

* [Aktywność gałęzi](transform-data-using-hadoop-hive.md)
* [Aktywność świń](transform-data-using-hadoop-pig.md)
* [Działanie mapreduce](transform-data-using-hadoop-map-reduce.md)
* [Aktywność w serwisie Hadoop Streaming](transform-data-using-hadoop-streaming.md)
* [Aktywność iskierki](transform-data-using-spark.md)
* [Niestandardowe działanie platformy .NET](transform-data-using-dotnet-custom-activity.md)
* [Działanie wsadowe uczenia maszynowego](transform-data-using-machine-learning.md)
* [Działanie procedury składowanej](transform-data-using-stored-procedure.md)
