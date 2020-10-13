---
title: Włączanie i wyświetlanie dzienników diagnostycznych dla Azure Data Lake Analytics
description: Informacje na temat konfigurowania i uzyskiwania dostępu do dzienników diagnostycznych dla Azure Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 02/12/2018
ms.openlocfilehash: f1f4320f0bfb924883eb7ae4807dcb714cd89983
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91331934"
---
# <a name="accessing-diagnostic-logs-for-azure-data-lake-analytics"></a>Accessing diagnostic logs for Azure Data Lake Analytics (Dostęp do dzienników diagnostycznych usługi Azure Data Lake Analytics)

Rejestrowanie diagnostyczne umożliwia zbieranie dzienników inspekcji dostępu do danych. Te dzienniki zawierają informacje takie jak:

* Lista użytkowników, którzy uzyskują dostęp do danych.
* Jak często uzyskuje się dostęp do danych.
* Ilość danych przechowywanych na koncie.

## <a name="enable-logging"></a>Włącz rejestrowanie

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Otwórz konto Data Lake Analytics i wybierz pozycję **dzienniki diagnostyczne** z sekcji __monitorowanie__ . Następnie wybierz pozycję __Włącz diagnostykę__.

    ![Zrzut ekranu pokazujący wybraną akcję "dzienniki diagnostyczne" i "Włącz diagnostykę, aby zebrać następujące dzienniki".](./media/data-lake-analytics-diagnostic-logs/turn-on-logging.png)

3. W obszarze __Ustawienia diagnostyki__wprowadź __nazwę__ tej konfiguracji rejestrowania, a następnie wybierz pozycję Opcje rejestrowania.

    ![Włącz diagnostykę, aby zbierać dzienniki inspekcji i żądań](./media/data-lake-analytics-diagnostic-logs/enable-diagnostic-logs.png "Włączanie dzienników diagnostycznych")

   * Dane można przechowywać/przetwarzać na trzy różne sposoby.

     * Wybierz pozycję __Archiwizuj na koncie magazynu__ , aby przechowywać dzienniki na koncie usługi Azure Storage. Użyj tej opcji, jeśli chcesz zarchiwizować dane. W przypadku wybrania tej opcji należy podać konto usługi Azure Storage, na którym mają zostać zapisane dzienniki.

     * Wybierz pozycję **strumień do centrum zdarzeń** , aby przesłać dane dziennika do centrum zdarzeń platformy Azure. Użyj tej opcji, jeśli masz potok przetwarzania podrzędnego, który analizuje dzienniki przychodzące w czasie rzeczywistym. W przypadku wybrania tej opcji należy podać szczegóły centrum zdarzeń platformy Azure, którego chcesz użyć.

     * Wybierz pozycję __Wyślij do log Analytics__ , aby wysłać dane do usługi Azure monitor. Użyj tej opcji, jeśli chcesz użyć dzienników Azure Monitor do zbierania i analizowania dzienników.
   * Określ, czy chcesz pobrać dzienniki inspekcji, czy Dzienniki żądań czy oba te elementy.  Dziennik żądania przechwytuje każde żądanie interfejsu API. Dziennik inspekcji rejestruje wszystkie operacje wyzwalane przez to żądanie interfejsu API.

   * Dla __Archiwum na konto magazynu__Określ liczbę dni, przez które mają zostać zachowane dane.

   * Kliknij przycisk __Zapisz__.

        > [!NOTE]
        > Przed kliknięciem przycisku __Zapisz__ należy wybrać opcję __Archiwizuj na koncie magazynu__, __przesłać strumieniowo do centrum zdarzeń__ lub __wysłać do log Analytics__ .

### <a name="use-the-azure-storage-account-that-contains-log-data"></a>Użyj konta usługi Azure Storage, które zawiera dane dziennika

1. Aby wyświetlić kontenery obiektów blob, które przechowują dane rejestrowania, Otwórz konto magazynu platformy Azure używane na Data Lake Analytics potrzeby rejestrowania, a następnie kliknij pozycję __obiekty blob__.

   * Kontener **Insights-Logs-Audits** zawiera dzienniki inspekcji.
   * Usługi Container **Insights — Dzienniki żądań** zawiera Dzienniki żądań.

2. W kontenerach dzienniki są przechowywane w następującej strukturze plików:

   ```text
   resourceId=/
     SUBSCRIPTIONS/
       <<SUBSCRIPTION_ID>>/
         RESOURCEGROUPS/
           <<RESOURCE_GRP_NAME>>/
             PROVIDERS/
               MICROSOFT.DATALAKEANALYTICS/
                 ACCOUNTS/
                   <DATA_LAKE_ANALYTICS_NAME>>/
                     y=####/
                       m=##/
                         d=##/
                           h=##/
                             m=00/
                               PT1H.json
   ```

   > [!NOTE]
   > `##`Wpisy w ścieżce zawierają rok, miesiąc, dzień i godzinę, w których został utworzony dziennik. Data Lake Analytics tworzy jeden plik co godzinę, więc `m=` zawsze zawiera wartość `00` .

    Na przykład Pełna ścieżka do dziennika inspekcji może być następująca:

    `https://adllogs.blob.core.windows.net/insights-logs-audit/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=04/m=00/PT1H.json`

    Podobnie pełna ścieżka do dziennika żądania może być:

    `https://adllogs.blob.core.windows.net/insights-logs-requests/resourceId=/SUBSCRIPTIONS/<sub-id>/RESOURCEGROUPS/myresourcegroup/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/mydatalakeanalytics/y=2016/m=07/d=18/h=14/m=00/PT1H.json`

## <a name="log-structure"></a>Struktura dziennika

Dzienniki inspekcji i żądań mają strukturalny format JSON.

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
         "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_analytics_account_name>",
         "category": "Requests",
         "operationName": "GetAggregatedJobHistory",
         "resultType": "200",
         "callerIpAddress": "::ffff:1.1.1.1",
         "correlationId": "4a11c709-05f5-417c-a98d-6e81b3e29c58",
         "identity": "1808bd5f-62af-45f4-89d8-03c5e81bac30",
         "properties": {
             "HttpMethod":"POST",
             "Path":"/JobAggregatedHistory",
             "RequestContentLength":122,
             "ClientRequestId":"3b7adbd9-3519-4f28-a61c-bd89506163b8",
             "StartTime":"2016-07-07T21:02:52.472Z",
             "EndTime":"2016-07-07T21:02:53.456Z"
             }
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
| resourceId |Ciąg |Identyfikator zasobu, w którym wykonano operację |
| category |Ciąg |Kategoria dziennika. Na przykład **żądania**. |
| operationName |Ciąg |Nazwa rejestrowanej operacji. Na przykład GetAggregatedJobHistory. |
| resultType |Ciąg |Stan operacji, na przykład 200. |
| callerIpAddress |Ciąg |Adres IP klienta wysyłającego żądanie |
| correlationId |Ciąg |Identyfikator dziennika. Ta wartość może służyć do grupowania zestawu powiązanych wpisów dziennika. |
| identity |Obiekt |Tożsamość, która wygenerowała dziennik. |
| properties |JSON |Aby uzyskać szczegółowe informacje, zobacz następną sekcję (schemat właściwości dziennika żądań) |

#### <a name="request-log-properties-schema"></a>Schemat właściwości dziennika żądań

| Nazwa | Typ | Opis |
| --- | --- | --- |
| HttpMethod |Ciąg |Metoda HTTP użyta dla operacji. Na przykład Pobierz. |
| Ścieżka |Ciąg |Ścieżka, na której wykonano operację |
| RequestContentLength |int |Długość zawartości żądania HTTP |
| Identyfikatorem żądania klienta |Ciąg |Identyfikator, który jednoznacznie identyfikuje to żądanie |
| StartTime |Ciąg |Godzina, o której serwer odebrał żądanie |
| EndTime |Ciąg |Godzina, o której serwer wysłał odpowiedź |

### <a name="audit-logs"></a>Dzienniki inspekcji

Oto przykładowy wpis w dzienniku inspekcji w formacie JSON. Każdy obiekt BLOB ma jeden element główny o nazwie **Records** zawierający tablicę obiektów dziennika.

```json
{
"records":
  [
    {
         "time": "2016-07-28T19:15:16.245Z",
         "resourceId": "/SUBSCRIPTIONS/<subscription_id>/RESOURCEGROUPS/<resource_group_name>/PROVIDERS/MICROSOFT.DATALAKEANALYTICS/ACCOUNTS/<data_lake_ANALYTICS_account_name>",
         "category": "Audit",
         "operationName": "JobSubmitted",
         "identity": "user@somewhere.com",
         "properties": {
             "JobId":"D74B928F-5194-4E6C-971F-C27026C290E6",
             "JobName": "New Job",
             "JobRuntimeName": "default",
             "SubmitTime": "7/28/2016 7:14:57 PM"
             }
    }
  ]
}
```

#### <a name="audit-log-schema"></a>Schemat dziennika inspekcji

| Nazwa | Typ | Opis |
| --- | --- | --- |
| time |Ciąg |Sygnatura czasowa (w formacie UTC) dziennika |
| resourceId |Ciąg |Identyfikator zasobu, w którym wykonano operację |
| category |Ciąg |Kategoria dziennika. Na przykład **Inspekcja**. |
| operationName |Ciąg |Nazwa rejestrowanej operacji. Na przykład JobSubmitted. |
| resultType |Ciąg |Podstan zadania (OperationName). |
| resultSignature |Ciąg |Dodatkowe szczegóły dotyczące stanu zadania (OperationName). |
| identity |Ciąg |Użytkownik, który zażądał operacji. Na przykład susan@contoso.com. |
| properties |JSON |Aby uzyskać szczegółowe informacje, zobacz następną sekcję (schemat właściwości dziennika inspekcji) |

> [!NOTE]
> **Typ ResultType** i **resultSignature** zawierają informacje o wyniku operacji i zawierają wartość tylko wtedy, gdy operacja została ukończona. Na przykład zawierają one tylko wartość, gdy **OperationName** zawiera wartość **JobStarted** lub **JobEnded**.
>
>

#### <a name="audit-log-properties-schema"></a>Schemat właściwości dziennika inspekcji

| Nazwa | Typ | Opis |
| --- | --- | --- |
| JobId |Ciąg |Identyfikator przypisany do zadania |
| JobName |Ciąg |Nazwa podana dla zadania |
| JobRunTime |Ciąg |Środowisko uruchomieniowe używane do przetwarzania zadania |
| SubmitTime |Ciąg |Czas (UTC) przesłania zadania |
| StartTime |Ciąg |Czas uruchomienia zadania po jego przejściu (w formacie UTC) |
| EndTime |Ciąg |Godzina zakończenia zadania |
| Równoległości |Ciąg |Liczba jednostek Data Lake Analytics żądana dla tego zadania podczas przesłania |

> [!NOTE]
> **SubmitTime**, **StartTime**, **Endtime**i **Parallel** zapewniają informacje dotyczące operacji. Te wpisy zawierają wartość tylko wtedy, gdy ta operacja została rozpoczęta lub ukończona. Na przykład **SubmitTime** zawiera tylko wartość po **operacji** , która ma wartość **JobSubmitted**.

## <a name="process-the-log-data"></a>Przetwarzanie danych dziennika

Azure Data Lake Analytics zawiera przykład sposobu przetwarzania i analizowania danych dziennika. Przykład można znaleźć pod adresem [https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample](https://github.com/Azure/AzureDataLake/tree/master/Samples/AzureDiagnosticsSample) .

## <a name="next-steps"></a>Następne kroki

[Omówienie usługi Azure Data Lake Analytics](data-lake-analytics-overview.md)
