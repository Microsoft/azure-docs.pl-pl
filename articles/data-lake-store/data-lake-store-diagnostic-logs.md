---
title: Wyświetlanie dzienników diagnostycznych dla Azure Data Lake Storage Gen1 | Microsoft Docs
description: 'Informacje na temat konfigurowania i uzyskiwania dostępu do dzienników diagnostycznych dla Azure Data Lake Storage Gen1 '
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: f6e75eb1-d0ae-47cf-bdb8-06684b7c0a94
ms.service: data-lake-store
ms.devlang: na
ms.topic: how-to
ms.date: 03/26/2018
ms.author: twooley
ms.openlocfilehash: aac0139e09866ce44d25989119b2eafb31e76961
ms.sourcegitcommit: 8a74ab1beba4522367aef8cb39c92c1147d5ec13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/20/2021
ms.locfileid: "98610458"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-storage-gen1"></a>Uzyskiwanie dostępu do dzienników diagnostycznych dla Azure Data Lake Storage Gen1
Dowiedz się, jak włączyć rejestrowanie diagnostyczne dla konta Azure Data Lake Storage Gen1 i jak wyświetlić dzienniki zebrane dla Twojego konta.

Organizacje mogą włączyć rejestrowanie diagnostyczne dla swojego konta Azure Data Lake Storage Gen1, aby zbierać dzienniki inspekcji dostępu do danych, które zawierają informacje takie jak lista użytkowników uzyskujących dostęp do danych, częstotliwość uzyskiwania dostępu do danych, ilość danych przechowywanych na koncie itp. Gdy ta opcja jest włączona, Diagnostyka i/lub żądania są rejestrowane na podstawie najlepszego wysiłku. Zarówno żądania, jak i wpisy dzienników diagnostycznych są tworzone tylko wtedy, gdy istnieją żądania skierowane do punktu końcowego usługi.

## <a name="prerequisites"></a>Wymagania wstępne
* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto Azure Data Lake Storage Gen1**. Postępuj zgodnie z instrukcjami w punkcie wprowadzenie [do Azure Data Lake Storage Gen1 przy użyciu Azure Portal](data-lake-store-get-started-portal.md).

## <a name="enable-diagnostic-logging-for-your-data-lake-storage-gen1-account"></a>Włączanie rejestrowania diagnostycznego dla konta usługi Data Lake Storage Gen1
1. Zaloguj się do nowego [Azure Portal](https://portal.azure.com).
2. Otwórz konto Data Lake Storage Gen1 i w bloku konta Data Lake Storage Gen1 kliknij pozycję **Ustawienia diagnostyczne**.
3. W bloku **Ustawienia diagnostyczne** kliknij pozycję **Włącz diagnostykę**.

    ![Zrzut ekranu przedstawiający konto Data Lake Storage Gen 1 z opcją ustawienia diagnostyczne i polecenie Włącz diagnostykę o nazwie out.](./media/data-lake-store-diagnostic-logs/turn-on-diagnostics.png "Włączanie dzienników diagnostycznych")

3. W bloku **Ustawienia diagnostyki** wprowadź następujące zmiany w celu skonfigurowania rejestrowania diagnostycznego.
   
    ![Zrzut ekranu przedstawiający sekcję Ustawienia diagnostyczne z polem tekstowym Nazwa i opcją Zapisz wywołana.](./media/data-lake-store-diagnostic-logs/enable-diagnostic-logs.png "Włączanie dzienników diagnostycznych")
   
   * W polu **Nazwa** wprowadź wartość w obszarze Konfiguracja dziennika diagnostycznego.
   * Można przechowywać/przetwarzać dane na różne sposoby.
     
        * Wybierz opcję **archiwizowania na koncie magazynu** w celu przechowywania dzienników na koncie usługi Azure Storage. Użyj tej opcji, jeśli chcesz zarchiwizować dane, które będą przetwarzane wsadowo w późniejszym czasie. W przypadku wybrania tej opcji należy podać konto usługi Azure Storage, do którego mają zostać zapisane dzienniki.
        
        * Wybierz opcję **przesyłania strumieniowego do centrum zdarzeń** w celu przesyłania strumieniowego danych dziennika do centrum zdarzeń platformy Azure. Najprawdopodobniej użyjesz tej opcji, jeśli masz potok przetwarzania podrzędnego do analizowania dzienników przychodzących w czasie rzeczywistym. W przypadku wybrania tej opcji należy podać szczegóły centrum zdarzeń platformy Azure, którego chcesz użyć.

        * Wybierz opcję **wysyłania do log Analytics** , aby użyć usługi Azure monitor do analizowania wygenerowanych danych dziennika. W przypadku wybrania tej opcji należy podać szczegóły dotyczące obszaru roboczego Log Analytics, w którym będzie używana Analiza dzienników. Aby uzyskać szczegółowe informacje na temat korzystania z dzienników Azure Monitor [, zobacz Wyświetlanie lub analizowanie danych zebranych za pomocą dzienników Azure monitor](../azure-monitor/log-query/log-analytics-tutorial.md) .
     
   * Określ, czy chcesz pobrać dzienniki inspekcji, czy Dzienniki żądań czy oba te elementy.
   * Określ liczbę dni, przez jaką dane muszą zostać zachowane. Przechowywanie jest stosowane tylko w przypadku korzystania z konta usługi Azure Storage do archiwizowania danych dziennika.
   * Kliknij pozycję **Zapisz**.

Po włączeniu ustawień diagnostycznych można obejrzeć dzienniki na karcie **dzienniki diagnostyczne** .

## <a name="view-diagnostic-logs-for-your-data-lake-storage-gen1-account"></a>Wyświetlanie dzienników diagnostycznych dla konta Data Lake Storage Gen1
Istnieją dwa sposoby wyświetlania danych dziennika dla konta Data Lake Storage Gen1.

* W widoku ustawień konta Data Lake Storage Gen1
* Z konta usługi Azure Storage, na którym są przechowywane dane

### <a name="using-the-data-lake-storage-gen1-settings-view"></a>Korzystanie z widoku ustawień Data Lake Storage Gen1
1. W bloku **Ustawienia** konta Data Lake Storage Gen1 kliknij pozycję **dzienniki diagnostyczne**.
   
    ![Wyświetlanie dzienników diagnostycznych](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs.png "Wyświetlanie dzienników diagnostycznych") 
2. W bloku **dzienniki diagnostyczne** powinny zostać wyświetlone dzienniki uporządkowane według **dzienników inspekcji** i **dzienników żądań**.
   
   * Dzienniki żądań przechwytują każde żądanie interfejsu API wykonane na koncie Data Lake Storage Gen1.
   * Dzienniki inspekcji są podobne do dzienników żądań, ale zapewniają znacznie bardziej szczegółowy podział operacji wykonywanych na koncie Data Lake Storage Gen1. Na przykład pojedyncze wywołanie interfejsu API przekazywania w dziennikach żądań może spowodować powstanie wielu operacji dołączania w dziennikach inspekcji.
3. Aby pobrać dzienniki, kliknij link **pobierania** dla każdego wpisu dziennika.

### <a name="from-the-azure-storage-account-that-contains-log-data"></a>Z konta usługi Azure Storage, które zawiera dane dziennika
1. Otwórz blok konto usługi Azure Storage skojarzony z Data Lake Storage Gen1 do rejestrowania, a następnie kliknij pozycję obiekty blob. W bloku **BLOB Service** są wyświetlane dwa kontenery.
   
    ![Zrzut ekranu przedstawiający blok Data Lake Storage Gen 1 opcja obiekty blob wybrana i blok usługi blogów z nazwami dwóch usług BLOB o nazwie.](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account.png "Wyświetlanie dzienników diagnostycznych")
   
   * Kontener **Insights-Logs-Audits** zawiera dzienniki inspekcji.
   * Usługi Container **Insights — Dzienniki żądań** zawiera Dzienniki żądań.
2. W tych kontenerach dzienniki są przechowywane w następującej strukturze.
   
    ![Zrzut ekranu przedstawiający strukturę dzienników w postaci, w jakiej jest przechowywany w kontenerze.](./media/data-lake-store-diagnostic-logs/view-diagnostic-logs-storage-account-structure.png "Wyświetlanie dzienników diagnostycznych")
   
    Na przykład Pełna ścieżka do dziennika inspekcji może być `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=04/m=00/PT1H.json`
   
    Podobnie pełna ścieżka do dziennika żądań może być `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/mydatalakestorage/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="understand-the-structure-of-the-log-data"></a>Zrozumienie struktury danych dziennika
Dzienniki inspekcji i żądań są w formacie JSON. W tej sekcji Przyjrzyjmy się strukturze JSON dla dzienników żądań i inspekcji.

### <a name="request-logs"></a>Dzienniki żądań
Oto przykładowy wpis w dzienniku żądań w formacie JSON. Każdy obiekt BLOB ma jeden element główny o nazwie **Records** zawierający tablicę obiektów dziennika.

```json
{
"records": 
  [        
    . . . .
    ,
    {
        "time": "2016-07-07T21:02:53.456Z",
        "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
        "category": "Requests",
        "operationName": "GETCustomerIngressEgress",
        "resultType": "200",
        "callerIpAddress": "::ffff:1.1.1.1",
        "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
        "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
        "properties": {"HttpMethod":"GET","Path":"/webhdfs/v1/Samples/Outputs/Drivers.csv","RequestContentLength":0,"ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8","StartTime":"2016-07-07T21:02:52.472Z","EndTime":"2016-07-07T21:02:53.456Z"}
    }
    ,
    . . . .
  ]
}
```

#### <a name="request-log-schema"></a>Schemat dziennika żądania
| Nazwa | Typ | Opis |
| --- | --- | --- |
| time |Ciąg |Sygnatura czasowa (w formacie UTC) dziennika |
| resourceId |Ciąg |Identyfikator zasobu, w którym miało miejsce operacja |
| category |Ciąg |Kategoria dziennika. Na przykład **żądania**. |
| operationName |Ciąg |Nazwa rejestrowanej operacji. Na przykład getfilestatus. |
| resultType |Ciąg |Stan operacji, na przykład 200. |
| callerIpAddress |Ciąg |Adres IP klienta wysyłającego żądanie |
| correlationId |Ciąg |Identyfikator dziennika, który może służyć do grupowania razem zestawu powiązanych wpisów dziennika |
| identity |Obiekt |Tożsamość, która wygenerowała dziennik. |
| properties |JSON |Szczegóły znajdują się poniżej. |

#### <a name="request-log-properties-schema"></a>Schemat właściwości dziennika żądań
| Nazwa | Typ | Opis |
| --- | --- | --- |
| HttpMethod |Ciąg |Metoda HTTP użyta dla operacji. Na przykład Pobierz. |
| Ścieżka |Ciąg |Ścieżka, na której wykonano operację |
| RequestContentLength |int |Długość zawartości żądania HTTP |
| Identyfikatorem żądania klienta |Ciąg |Identyfikator, który jednoznacznie identyfikuje to żądanie |
| StartTime |Ciąg |Godzina, o której serwer odebrał żądanie |
| EndTime |Ciąg |Godzina, o której serwer wysłał odpowiedź |
| StoreIngressSize |Długo |Rozmiar w bajtach ingressed do Data Lake Store |
| StoreEgressSize |Długo |Rozmiar w bajtach egressed od Data Lake Store |

### <a name="audit-logs"></a>Dzienniki inspekcji
Oto przykładowy wpis w dzienniku inspekcji w formacie JSON. Każdy obiekt BLOB ma jeden element główny o nazwie **Records** zawierający tablicę obiektów dziennika

```json
{
"records": 
  [        
    . . . .
    ,
    {
        "time": "2016-07-08T19:08:59.359Z",
        "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKESTORE/ACCOUNTS/<data_lake_storage_gen1_account_name>",
        "category": "Audit",
        "operationName": "SeOpenStream",
        "resultType": "0",
        "resultSignature": "0",
        "correlationId": "381110fc03534e1cb99ec52376ceebdf;Append_BrEKAmg;25.66.9.145",
        "identity": "A9DAFFAF-FFEE-4BB5-A4A0-1B6CBBF24355",
        "properties": {"StreamName":"adl://<data_lake_storage_gen1_account_name>.azuredatalakestore.net/logs.csv"}
    }
    ,
    . . . .
  ]
}
```

#### <a name="audit-log-schema"></a>Schemat dziennika inspekcji
| Nazwa | Typ | Opis |
| --- | --- | --- |
| time |Ciąg |Sygnatura czasowa (w formacie UTC) dziennika |
| resourceId |Ciąg |Identyfikator zasobu, w którym miało miejsce operacja |
| category |Ciąg |Kategoria dziennika. Na przykład **Inspekcja**. |
| operationName |Ciąg |Nazwa rejestrowanej operacji. Na przykład getfilestatus. |
| resultType |Ciąg |Stan operacji, na przykład 200. |
| resultSignature |Ciąg |Dodatkowe szczegóły dotyczące operacji. |
| correlationId |Ciąg |Identyfikator dziennika, który może służyć do grupowania razem zestawu powiązanych wpisów dziennika |
| identity |Obiekt |Tożsamość, która wygenerowała dziennik. |
| properties |JSON |Szczegóły znajdują się poniżej. |

#### <a name="audit-log-properties-schema"></a>Schemat właściwości dziennika inspekcji
| Nazwa | Typ | Opis |
| --- | --- | --- |
| StreamName |Ciąg |Ścieżka, na której wykonano operację |

## <a name="samples-to-process-the-log-data"></a>Przykłady do przetwarzania danych dziennika
W przypadku wysyłania dzienników z usługi Azure Data Lake Storage Gen1 do dzienników Azure Monitor (zobacz [Wyświetlanie lub analizowanie danych zebranych za pomocą dzienników Azure monitor wyszukiwanie](../azure-monitor/log-query/log-analytics-tutorial.md) szczegółowych informacji dotyczących korzystania z dzienników Azure monitor), następujące zapytanie zwróci tabelę zawierającą listę nazw wyświetlanych użytkowników, czas zdarzeń oraz liczbę zdarzeń w czasie zdarzenia wraz z wykresem wizualnym. Można łatwo zmodyfikować, aby pokazać identyfikator GUID użytkownika lub inne atrybuty:

```
search *
| where ( Type == "AzureDiagnostics" )
| summarize count(TimeGenerated) by identity_s, TimeGenerated
```


Azure Data Lake Storage Gen1 zawiera przykład sposobu przetwarzania i analizowania danych dziennika. Przykład można znaleźć pod adresem [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample) . 

## <a name="see-also"></a>Zobacz też
* [Omówienie Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)
