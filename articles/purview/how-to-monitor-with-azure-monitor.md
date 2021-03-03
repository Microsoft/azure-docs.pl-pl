---
title: Jak monitorować usługę Azure kontrolą
description: Informacje o konfigurowaniu metryk, alertów i ustawień diagnostycznych usługi Azure kontrolą za pomocą Azure Monitor.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 12/03/2020
ms.openlocfilehash: 4cb3965d359980856c238cd563ed8b761754660b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667734"
---
# <a name="azure-purview-metrics-in-azure-monitor"></a>Metryki usługi Azure kontrolą w Azure Monitor

W tym artykule opisano sposób konfigurowania metryk, alertów i ustawień diagnostycznych dla usługi Azure kontrolą przy użyciu Azure Monitor.

## <a name="monitor-azure-purview"></a>Monitorowanie usługi Azure kontrolą

Administratorzy usługi Azure kontrolą mogą używać Azure Monitor do śledzenia stanu operacyjnego konta kontrolą. Zbierane są metryki zapewniające punkty danych umożliwiające śledzenie potencjalnych problemów, rozwiązywanie problemów i zwiększanie niezawodności konta usługi kontrolą. Metryki są wysyłane do usługi Azure monitor w przypadku zdarzeń w usłudze Azure kontrolą.

## <a name="aggregated-metrics"></a>Zagregowane metryki

Dostęp do metryk można uzyskać z Azure Portal dla konta kontrolą. Dostęp do metryk są kontrolowane przez przypisanie roli konta kontrolą. Aby wyświetlić metryki, użytkownicy muszą być częścią roli "monitorowanie czytnika" w usłudze Azure kontrolą. Sprawdź [uprawnienia roli czytelnik monitorowania](../azure-monitor/roles-permissions-security.md#built-in-monitoring-roles) , aby dowiedzieć się więcej na temat poziomów dostępu do ról.

Osoba, która utworzyła konto kontrolą, automatycznie uzyskuje uprawnienia do wyświetlania metryk. Jeśli ktoś inny chce zobaczyć metryki, Dodaj je do roli **czytelnik monitorowania** , wykonując następujące czynności:

### <a name="add-a-user-to-the-monitoring-reader-role"></a>Dodawanie użytkownika do roli czytelnik monitorowania

Aby dodać użytkownika do roli **czytelnik monitorowania** , właściciel konta kontrolą lub właściciela subskrypcji może wykonać następujące czynności:

1. Przejdź do [Azure Portal](https://portal.azure.com) i wyszukaj nazwę konta usługi Azure kontrolą.

2. Wybierz pozycję **Kontrola dostępu (IAM)** .

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/access-iam.png" alt-text="Zrzut ekranu przedstawiający sposób uzyskiwania dostępu do mapy IAM.":::

3. Wybierz pozycję **Dodaj przypisanie roli**.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-role-assignment.png" alt-text="Zrzut ekranu przedstawiający sposób dodawania przypisania roli.":::

4. Wybierz **czytnik monitorowania** roli i ustaw opcję Przypisz dostęp do **użytkownika, grupy lub nazwy głównej usługi Azure AD**. I przypisz konto usługi AAD, aby uzyskać dostęp do metryk.  

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/add-monitoring-reader.png" alt-text="Zrzut ekranu przedstawiający sposób dodawania roli czytelnik monitorowania.":::

## <a name="metrics-visualization"></a>Wizualizacja metryk

Użytkownicy w roli **czytelnik monitorowania** mogą zobaczyć zagregowane metryki i dzienniki diagnostyczne wysyłane do Azure monitor. Metryki są wymienione w Azure Portal dla odpowiedniego konta kontrolą. W Azure Portal wybierz sekcję metryki, aby wyświetlić listę wszystkich dostępnych metryk.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/purview-metrics.png" alt-text="Zrzut ekranu przedstawiający sekcję dostępne metryki kontrolą." lightbox="./media/how-to-monitor-with-azure-monitor/purview-metrics.png":::

Użytkownicy usługi Azure kontrolą mogą również uzyskiwać dostęp do strony metryki bezpośrednio z centrum zarządzania konta usługi Azure kontrolą. Wybierz pozycję Azure Monitor na stronie głównej centrum zarządzania kontrolą, aby uruchomić Azure Portal.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png" alt-text="Zrzut ekranu przedstawiający uruchamianie metryk kontrolą z poziomu centrum zarządzania." lightbox="./media/how-to-monitor-with-azure-monitor/launch-metrics-from-management.png":::

### <a name="available-metrics"></a>Dostępne metryki

Aby zapoznać się z sposobem korzystania z sekcji Metryka w Azure Portal, przeczytaj następujące dwa dokumenty. [Wprowadzenie do Eksploratora metryk](../azure-monitor/essentials/metrics-getting-started.md) i [zaawansowanych funkcji Eksploratora metryk](../azure-monitor/essentials/metrics-charts.md).

Poniższa tabela zawiera listę metryk dostępnych do zbadania w Azure Portal:

| Nazwa metryki | Przestrzeń nazw metryki | Typ agregacji | Opis |
| ------------------- | ------------------- | ------------------- | ----------------- |
| Skanowanie anulowane | Automatyczne skanowanie | Sum <br> Liczba | Agreguj skany anulowanych źródeł danych w okresie |
| Ukończono skanowanie | Automatyczne skanowanie | Sum <br> Liczba | Agregowanie ukończonych skanów źródła danych w okresie |
| Skanowanie nie powiodło się | Automatyczne skanowanie | Sum <br> Liczba | Agregowanie skanów źródła danych zakończonych niepowodzeniem w okresie |
| Czas skanowania | Automatyczne skanowanie | Min. <br> Maks. <br> Sum <br> Śr. | Agreguj łączny czas trwania skanowania w czasie |

## <a name="diagnostic-logs-to-azure-storage-account"></a>Dzienniki diagnostyczne do konta usługi Azure Storage

Zdarzenia nieprzetworzonych danych telemetrycznych są emitowane do Azure Monitor. Zdarzenia można rejestrować na koncie magazynu klienta wybranym do dalszej analizy. Eksportowanie dzienników odbywa się za pośrednictwem ustawień diagnostycznych dla konta kontrolą na Azure Portal.

Postępuj zgodnie z instrukcjami, aby utworzyć ustawienia diagnostyczne dla konta usługi Azure kontrolą.

1. Utwórz nowe ustawienie diagnostyczne, aby zbierać dzienniki platformy i metryki, wykonując następujące czynności: [Tworzenie ustawień diagnostycznych w celu wysyłania dzienników platformy i metryk do różnych miejsc docelowych](../azure-monitor/essentials/diagnostic-settings.md). Wybierz lokalizację docelową tylko jako konto usługi Azure Storage.

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png" alt-text="Zrzut ekranu przedstawiający tworzenie dziennika diagnostycznego." lightbox="./media/how-to-monitor-with-azure-monitor/step-one-diagnostic-setting.png":::

2. Rejestruj zdarzenia na koncie magazynu. Do archiwizowania dzienników diagnostycznych zaleca się dedykowane konto magazynu. Poniższy artykuł umożliwia [utworzenie konta magazynu](../storage/common/storage-account-create.md?tabs=azure-portal).

   :::image type="content" source="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png" alt-text="Zrzut ekranu przedstawiający przypisanie konta magazynu dla dziennika diagnostycznego." lightbox="./media/how-to-monitor-with-azure-monitor/step-two-diagnostic-setting.png":::

Odczekaj do 15 minut, aż zacznie otrzymywać dzienniki na nowo utworzonym koncie magazynu. [Zobacz przechowywanie danych i schemat dzienników zasobów na koncie usługi Azure Storage](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage). Po skonfigurowaniu dzienników diagnostycznych zdarzenia będą przepływać do konta magazynu.

### <a name="scanstatuslogevent"></a>ScanStatusLogEvent

Zdarzenie śledzi cykl życia skanowania. Operacja skanowania odbywa się zgodnie z postępem przez sekwencję Stanów, od kolejki, uruchomione i wreszcie stan terminalu zakończony powodzeniem | Niepowodzenie | Szkodliw. Zdarzenie jest rejestrowane dla każdego przejścia stanu, a schemat zdarzenia będzie miał następujące właściwości.

```JSON
{
  "time": "<The UTC time when the event occurred>",
  "properties": {
    "dataSourceName": "<Registered data source friendly name>",
    "dataSourceType": "<Registered data source type>",
    "scanName": "<Scan instance friendly name>",
    "assetsDiscovered": "<If the resultType is succeeded, count of assets discovered in scan run>",
    "assetsClassified": "<If the resultType is succeeded, count of assets classified in scan run>",
    "scanQueueTimeInSeconds": "<If the resultType is succeeded, total seconds the scan instance in queue>",
    "scanTotalRunTimeInSeconds": "<If the resultType is succeeded, total seconds the scan took to run>",
    "runType": "<How the scan is triggered>",
    "errorDetails": "<Scan failure error>",
    "scanResultId": "<Unique GUID for the scan instance>"
  },
  "resourceId": "<The azure resource identifier>",
  "category": "<The diagnostic log category>",
  "operationName": "<The operation that cause the event Possible values for ScanStatusLogEvent category are: 
                    |AdhocScanRun 
                    |TriggeredScanRun 
                    |StatusChangeNotification>",
  "resultType": "Queued – indicates a scan is queued. 
                 Running – indicates a scan entered a running state. 
                 Succeeded – indicates a scan completed successfully. 
                 Failed – indicates a scan failure event. 
                 Cancelled – indicates a scan was cancelled. ",
  "resultSignature": "<Not used for ScanStatusLogEvent category. >",
  "resultDescription": "<This will have an error message if the resultType is Failed. >",
  "durationMs": "<Not used for ScanStatusLogEvent category. >",
  "level": "<The log severity level. Possible values are:
            |Informational
            |Error >",
  "location": "<The location of the Azure Purview account>",
}
```

Przykładowy dziennik wystąpienia zdarzenia znajduje się w sekcji poniżej.

```JSON
{
  "time": "2020-11-24T20:25:13.022860553Z",
  "properties": {
    "dataSourceName": "AzureDataExplorer-swD",
    "dataSourceType": "AzureDataExplorer",
    "scanName": "Scan-Kzw-shoebox-test",
    "assetsDiscovered": "0",
    "assetsClassified": "0",
    "scanQueueTimeInSeconds": "0",
    "scanTotalRunTimeInSeconds": "0",
    "runType": "Manual",
    "errorDetails": "empty_value",
    "scanResultId": "0dc51a72-4156-40e3-8539-b5728394561f"
  },
  "resourceId": "/SUBSCRIPTIONS/111111111111-111-4EB2/RESOURCEGROUPS/FOOBAR-TEST-RG/PROVIDERS/MICROSOFT.PURVIEW/ACCOUNTS/FOOBAR-HEY-TEST-NEW-MANIFEST-EUS",
  "category": "ScanStatusLogEvent",
  "operationName": "TriggeredScanRun",
  "resultType": "Delayed",
  "resultSignature": "empty_value",
  "resultDescription": "empty_value",
  "durationMs": 0,
  "level": "Informational",
  "location": "eastus",
}
```

## <a name="next-steps"></a>Następne kroki

[Wyświetlanie szczegółowych informacji o zasobach](asset-insights.md)