---
title: Włączanie dzienników Azure Storage Analytics i zarządzanie nimi (klasyczne) | Microsoft Docs
description: Dowiedz się, jak monitorować konto magazynu na platformie Azure przy użyciu Azure Storage Analytics.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 01/29/2021
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: bc6632b55ba8fd90317a8b5046a3e84d863bf0ef
ms.sourcegitcommit: 54e1d4cdff28c2fd88eca949c2190da1b09dca91
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/31/2021
ms.locfileid: "99221720"
---
# <a name="enable-and-manage-azure-storage-analytics-logs-classic"></a>Włączanie dzienników Azure Storage Analytics i zarządzanie nimi (klasyczne)

[Azure Storage Analytics](storage-analytics.md) zawiera dzienniki dla obiektów blob, kolejek i tabel. Aby skonfigurować dzienniki dla Twojego konta, można użyć [Azure Portal](https://portal.azure.com) . W tym artykule przedstawiono sposób włączania dzienników i zarządzania nimi. Aby dowiedzieć się, jak włączyć metryki, zobacz temat [Włączanie i zarządzanie metrykami Azure Storage Analytics (klasycznymi)](storage-monitor-storage-account.md).  Istnieją koszty związane z badaniem i przechowywaniem danych monitorowania w Azure Portal. Aby uzyskać więcej informacji, zobacz [Usługa Storage Analytics](storage-analytics.md).

> [!NOTE]
> Zalecamy używanie dzienników usługi Azure Storage w Azure Monitor zamiast dzienników analityka magazynu. Dzienniki usługi Azure Storage w Azure Monitor są w publicznej wersji zapoznawczej i są dostępne do testowania wersji zapoznawczej we wszystkich regionach chmury publicznej. Ta wersja zapoznawcza umożliwia korzystanie z dzienników dla obiektów BLOB (w tym Azure Data Lake Storage Gen2), plików, kolejek i tabel. Aby dowiedzieć się więcej, zobacz dowolny z następujących artykułów:
>
> - [Monitorowanie Blob Storage platformy Azure](../blobs/monitor-blob-storage.md)
> - [Azure Files monitorowania](../files/storage-files-monitoring.md)
> - [Monitorowanie Queue Storage platformy Azure](../queues/monitor-queue-storage.md)
> - [Monitorowanie usługi Azure Table Storage](../tables/monitor-table-storage.md)

Aby uzyskać szczegółowy przewodnik dotyczący używania analityka magazynu i innych narzędzi do identyfikowania, diagnozowania i rozwiązywania problemów związanych z usługą Azure Storage, zobacz [monitorowanie, diagnozowanie i rozwiązywanie problemów z Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

<a id="configure-logging"></a>

## <a name="enable-logs"></a>Włączanie dzienników

Można nakazać usłudze Azure Storage zapisywanie dzienników diagnostycznych dla żądań odczytu, zapisu i usuwania dla usług BLOB, tabel i kolejek. Ustawione zasady przechowywania danych dotyczą również tych dzienników.

> [!NOTE]
> Azure Files obecnie obsługuje metryki analityka magazynu, ale nie obsługuje rejestrowania analityka magazynu.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **konta magazynu**, a następnie nazwę konta magazynu, aby otworzyć blok konto magazynu.

2. Wybierz pozycję **Ustawienia diagnostyczne (klasyczne)** w sekcji **monitorowanie (klasyczne)** w bloku menu.

    ![Element menu Diagnostyka w obszarze monitorowanie w Azure Portal.](./media/manage-storage-analytics-logs/storage-enable-metrics-00.png)

3. Upewnij się, że **stan** jest ustawiony na **włączone**, a następnie wybierz **usługi** , dla których chcesz włączyć rejestrowanie.

   > [!div class="mx-imgBorder"]
   > ![Skonfiguruj rejestrowanie w Azure Portal.](./media/manage-storage-analytics-logs/enable-diagnostics.png)    


4. Upewnij się, że pole wyboru **Usuń dane** jest zaznaczone.  Następnie ustaw liczbę dni przechowywania danych dziennika przez przeniesienie suwaka poniżej pola wyboru lub przez bezpośrednie zmodyfikowanie wartości, która pojawia się w polu tekstowym obok kontrolki suwak. Wartość domyślna dla nowych kont magazynu wynosi siedem dni. Jeśli nie chcesz ustawiać zasad przechowywania, wprowadź wartość zero. Jeśli nie ma zasad przechowywania, można usunąć dane dziennika.

   > [!WARNING]
   > Dzienniki są przechowywane jako dane na Twoim koncie. dane dzienników można zbierać na koncie z upływem czasu, co może zwiększyć koszt magazynu. Jeśli potrzebujesz danych dziennika tylko przez niewielki czas, możesz zmniejszyć koszty, modyfikując zasady przechowywania danych. Nieodświeżone dane dziennika (dane starsze niż zasady przechowywania) są usuwane przez system. Zalecamy ustawienie zasad przechowywania w zależności od tego, jak długo mają być przechowywane dane dziennika dla Twojego konta. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [rozliczeń za magazyn](storage-analytics-metrics.md#billing-on-storage-metrics) .

4. Kliknij pozycję **Zapisz**.

   Dzienniki diagnostyczne są zapisywane w kontenerze obiektów BLOB o nazwie *$Logs* na koncie magazynu. Dane dziennika można wyświetlić za pomocą Eksploratora usługi Storage, takiego jak [Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com), lub programowo przy użyciu biblioteki klienta usługi Storage lub programu PowerShell.

   Aby uzyskać informacje o uzyskiwaniu dostępu do kontenera $logs, zobacz [Rejestrowanie analizy magazynu](storage-analytics-logging.md).

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Otwórz okno poleceń programu Windows PowerShell.

2. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

   ```powershell
   Connect-AzAccount
   ```

3. Jeśli Twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Zastąp `<subscription-id>` wartość symbolu zastępczego identyfikatorem subskrypcji.

5. Pobierz kontekst konta magazynu, który definiuje konto magazynu, którego chcesz użyć.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Zastąp `<resource-group-name>` wartość symbolu zastępczego nazwą grupy zasobów.

   * Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu. 

6. Aby zmienić bieżące ustawienia dziennika, użyj **Set-AzStorageServiceLoggingProperty** . Polecenia cmdlet kontrolujące Rejestrowanie magazynu używają **LoggingOperations** parametru, który jest ciągiem zawierającym rozdzielaną przecinkami listę typów żądań do rejestrowania. Trzy możliwe typy żądań to **Odczyt**, **zapis** i **usuwanie**. Aby wyłączyć rejestrowanie, użyj wartości **none** dla parametru **LoggingOperations** .  

   Następujące polecenie przełącza do rejestrowania żądań odczytu, zapisu i usuwania w usługa kolejki na domyślnym koncie magazynu z ustawionym okresem przechowywania na pięć dni:  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Queue -LoggingOperations read,write,delete -RetentionDays 5 -Context $ctx
   ```  

   > [!WARNING]
   > Dzienniki są przechowywane jako dane na Twoim koncie. dane dzienników można zbierać na koncie z upływem czasu, co może zwiększyć koszt magazynu. Jeśli potrzebujesz danych dziennika tylko przez niewielki czas, możesz zmniejszyć koszty, modyfikując zasady przechowywania danych. Nieodświeżone dane dziennika (dane starsze niż zasady przechowywania) są usuwane przez system. Zalecamy ustawienie zasad przechowywania w zależności od tego, jak długo mają być przechowywane dane dziennika dla Twojego konta. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [rozliczeń za magazyn](storage-analytics-metrics.md#billing-on-storage-metrics) .
   
   Następujące polecenie wyłącza rejestrowanie dla usługi Table Service na domyślnym koncie magazynu:  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Table -LoggingOperations none -Context $ctx 
   ```  

   Aby uzyskać informacje dotyczące sposobu konfigurowania Azure PowerShell poleceń cmdlet do pracy z subskrypcją platformy Azure i wybierania domyślnego konta magazynu do użycia, zobacz: [jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/).  

### <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_EnableDiagnosticLogs":::

### <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);  
var queueClient = storageAccount.CreateCloudQueueClient();  
var serviceProperties = queueClient.GetServiceProperties();  

serviceProperties.Logging.LoggingOperations = LoggingOperations.All;  
serviceProperties.Logging.RetentionDays = 2;  

queueClient.SetServiceProperties(serviceProperties);  
``` 

---

<a id="modify-retention-policy"></a>

## <a name="modify-log-data-retention-period"></a>Modyfikowanie okresu przechowywania danych dziennika

Dane dzienników można zbierać na koncie z upływem czasu, co może zwiększyć koszt magazynu. Jeśli potrzebujesz danych dziennika tylko przez niewielki czas, możesz zmniejszyć koszty, modyfikując okres przechowywania danych dziennika. Na przykład jeśli potrzebujesz dzienników tylko przez trzy dni, Ustaw okres przechowywania danych dziennika na wartość `3` . Dzięki temu dzienniki zostaną automatycznie usunięte z konta po 3 dniach. W tej sekcji przedstawiono sposób wyświetlania bieżącego okresu przechowywania danych dziennika, a następnie aktualizowania tego okresu, jeśli tak, co chcesz zrobić.

> [!NOTE]
> Te kroki mają zastosowanie tylko w przypadku kont, które nie mają włączonej opcji **hierarchicznej przestrzeni nazw** . Jeśli to ustawienie zostało włączone na koncie, to ustawienie dni przechowywania nie jest jeszcze obsługiwane. Zamiast tego należy ręcznie usunąć dzienniki przy użyciu dowolnego obsługiwanego narzędzia, takiego jak Eksplorator usługi Azure Storage, REST lub zestaw SDK. Aby znaleźć te dzienniki na koncie magazynu, zobacz [jak są przechowywane dzienniki](storage-analytics-logging.md#how-logs-are-stored).

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **konta magazynu**, a następnie nazwę konta magazynu, aby otworzyć blok konto magazynu.
2. Wybierz pozycję **Ustawienia diagnostyczne (klasyczne)** w sekcji **monitorowanie (klasyczne)** w bloku menu.

    ![Element menu Diagnostyka objęty MONITOROWANIEm w Azure Portal](./media/manage-storage-analytics-logs/storage-enable-metrics-00.png)

3. Upewnij się, że pole wyboru **Usuń dane** jest zaznaczone.  Następnie ustaw liczbę dni przechowywania danych dziennika przez przeniesienie suwaka poniżej pola wyboru lub przez bezpośrednie zmodyfikowanie wartości, która pojawia się w polu tekstowym obok kontrolki suwak.

   > [!div class="mx-imgBorder"]
   > ![Zmodyfikuj okres przechowywania w Azure Portal](./media/manage-storage-analytics-logs/modify-retention-period.png)

   Domyślna liczba dni dla nowych kont magazynu wynosi siedem dni. Jeśli nie chcesz ustawiać zasad przechowywania, wprowadź wartość zero. Jeśli nie ma zasad przechowywania, można usunąć dane monitorowania.
   
4. Kliknij pozycję **Zapisz**.

   Dzienniki diagnostyczne są zapisywane w kontenerze obiektów BLOB o nazwie *$Logs* na koncie magazynu. Dane dziennika można wyświetlić za pomocą Eksploratora usługi Storage, takiego jak [Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com), lub programowo przy użyciu biblioteki klienta usługi Storage lub programu PowerShell.

   Aby uzyskać informacje o uzyskiwaniu dostępu do kontenera $logs, zobacz [Rejestrowanie analizy magazynu](storage-analytics-logging.md).

### <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Otwórz okno poleceń programu Windows PowerShell.

2. Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Connect-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

   ```powershell
   Connect-AzAccount
   ```

3. Jeśli Twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Zastąp `<subscription-id>` wartość symbolu zastępczego identyfikatorem subskrypcji.

5. Pobierz kontekst konta magazynu, który definiuje konto magazynu.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   $ctx = $storageAccount.Context
   ```

   * Zastąp `<resource-group-name>` wartość symbolu zastępczego nazwą grupy zasobów.

   * Zastąp wartość symbolu zastępczego `<storage-account-name>` nazwą konta magazynu. 

6. Użyj [Get-AzStorageServiceLoggingProperty](https://docs.microsoft.com/powershell/module/az.storage/get-azstorageserviceloggingproperty) , aby wyświetlić bieżące zasady przechowywania dzienników. Poniższy przykład drukuje do konsoli okres przechowywania dla usług BLOB i queue storage.

   ```powershell
   Get-AzStorageServiceLoggingProperty -ServiceType Blob, Queue -Context $ctx
   ```  

   W danych wyjściowych konsoli okres przechowywania jest wyświetlany poniżej `RetentionDays` nagłówka kolumny.

   > [!div class="mx-imgBorder"]
   > ![Zasady przechowywania w danych wyjściowych programu PowerShell](./media/manage-storage-analytics-logs/retention-period-powershell.png)

7. Aby zmienić okres przechowywania, użyj [Ustawienia Set-AzStorageServiceLoggingProperty](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageserviceloggingproperty) . Poniższy przykład zmienia okres przechowywania na 4 dni.  

   ```powershell
   Set-AzStorageServiceLoggingProperty -ServiceType Blob, Queue -RetentionDays 4 -Context $ctx
   ```  

   Aby uzyskać informacje dotyczące sposobu konfigurowania Azure PowerShell poleceń cmdlet do pracy z subskrypcją platformy Azure i wybierania domyślnego konta magazynu do użycia, zobacz: [jak zainstalować i skonfigurować Azure PowerShell](/powershell/azure/).  

### <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

Poniższy przykład drukuje do konsoli okres przechowywania dla usług BLOB i queue storage.

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_ViewRetentionPeriod":::

Poniższy przykład zmienia okres przechowywania na 4 dni. 

:::code language="csharp" source="~/azure-storage-snippets/queues/howto/dotnet/dotnet-v12/Monitoring.cs" id="snippet_ModifyRetentionPeriod":::

### <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Poniższy przykład drukuje do konsoli okres przechowywania dla usług BLOB i queue storage.

```csharp
var storageAccount = CloudStorageAccount.Parse(connectionString);

var blobClient = storageAccount.CreateCloudBlobClient();
var queueClient = storageAccount.CreateCloudQueueClient();

var blobserviceProperties = blobClient.GetServiceProperties();
var queueserviceProperties = queueClient.GetServiceProperties();

Console.WriteLine("Retention period for logs from the blob service is: " +
   blobserviceProperties.Logging.RetentionDays.ToString());

Console.WriteLine("Retention period for logs from the queue service is: " +
   queueserviceProperties.Logging.RetentionDays.ToString());
```

Poniższy przykład zmienia okres przechowywania dzienników dla usług BLOB i queue storage na 4 dni. 

```csharp

blobserviceProperties.Logging.RetentionDays = 4;
queueserviceProperties.Logging.RetentionDays = 4;

blobClient.SetServiceProperties(blobserviceProperties);
queueClient.SetServiceProperties(queueserviceProperties);  
``` 

---

### <a name="verify-that-log-data-is-being-deleted"></a>Sprawdź, czy dane dziennika są usuwane

Możesz sprawdzić, czy dzienniki są usuwane, wyświetlając zawartość `$logs` kontenera konta magazynu. Na poniższej ilustracji przedstawiono zawartość folderu znajdującego się w `$logs` kontenerze. Folder odpowiada styczeń 2021, a każdy folder zawiera dzienniki przez jeden dzień. Jeśli dzisiejszy dzień był 29 lutego 2021, a zasady przechowywania są ustawione na tylko jeden dzień, ten folder powinien zawierać dzienniki tylko dla jednego dnia.

> [!div class="mx-imgBorder"]
> ![Lista folderów dzienników w witrynie Azure Portal](./media/manage-storage-analytics-logs/verify-and-delete-logs.png)

<a id="download-storage-logging-log-data"></a>

## <a name="view-log-data"></a>Wyświetlanie danych dziennika

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

Aby dowiedzieć się więcej o pobieraniu określonych plików, zobacz [pobieranie obiektów blob z usługi Azure Blob Storage za pomocą AzCopy v10](./storage-use-azcopy-blobs-download.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

Po pobraniu danych dziennika można wyświetlić wpisy dziennika w plikach. Te pliki dzienników używają rozdzielanego formatu tekstu, który umożliwia przeanalizowanie wielu narzędzi do odczytywania dzienników (Aby uzyskać więcej informacji, zobacz [monitorowanie, diagnozowanie i rozwiązywanie problemów Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)). Różne narzędzia mają różne pomieszczenia do formatowania, filtrowania, sortowania, wyszukiwania w usłudze AD zawartości plików dziennika. Aby uzyskać więcej informacji na temat formatu i zawartości pliku dziennika rejestrowania magazynu, zobacz [analityka magazynu format dziennika](/rest/api/storageservices/storage-analytics-log-format) i [analityka magazynu zarejestrowanych operacji i komunikatów o stanie](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages).

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej na temat analityka magazynu, zobacz [analityka magazynu](storage-analytics.md) for analityka magazynu.
* [Skonfiguruj metryki analityka magazynu](storage-monitor-storage-account.md).
* Aby uzyskać więcej informacji na temat konfigurowania rejestrowania magazynu przy użyciu języka .NET, zobacz [Dokumentacja biblioteki klienta usługi Storage](/previous-versions/azure/dn261237(v=azure.100)). 
* Aby uzyskać ogólne informacje o konfigurowaniu rejestrowania magazynu za pomocą interfejsu API REST, zobacz [Włączanie i konfigurowanie analityka magazynu](/rest/api/storageservices/Enabling-and-Configuring-Storage-Analytics).
* Dowiedz się więcej o formacie dzienników analityka magazynu. Zobacz [format dziennika analityka magazynu](/rest/api/storageservices/storage-analytics-log-format).