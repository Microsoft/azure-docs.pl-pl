---
title: Dzienniki diagnostyczne
titleSuffix: Azure Content Delivery Network
description: Dowiedz się, jak zapisywać podstawowe analizy przy użyciu dzienników diagnostycznych platformy Azure, co pozwala na eksportowanie metryk użycia z punktu końcowego usługi Azure Content Delivery Network.
services: cdn
author: asudbring
manager: KumudD
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: allensu
ms.openlocfilehash: 96e80de5b8b5ab0a046913ce40ca2d7254dd0133
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100573221"
---
# <a name="diagnostic-logs---azure-content-delivery-network"></a>Dzienniki diagnostyczne — Content Delivery Network platformy Azure

Korzystając z dzienników diagnostycznych platformy Azure, można wyświetlić podstawowe analizy i zapisać je w jednym lub większej liczbie miejsc docelowych, w tym:

* Konto usługi Azure Storage
* Obszar roboczy usługi Log Analytics
* Azure Event Hubs

Ta funkcja jest dostępna w punktach końcowych usługi CDN dla wszystkich warstw cenowych. 

Dzienniki diagnostyczne umożliwiają eksportowanie podstawowych metryk użycia z punktu końcowego usługi CDN do różnych źródeł, dzięki czemu można je wykorzystać w dostosowany sposób. Można wykonywać następujące rodzaje eksportu danych:

* Eksportuj dane do magazynu obiektów blob, Eksportuj do woluminów CSV i Generuj wykresy w programie Excel.
* Eksportuj dane do Event Hubs i skorelowane z danymi z innych usług platformy Azure.
* Eksportuj dane do Azure Monitor dzienników i wyświetlaj dane we własnym Log Analytics obszarze roboczym

Do wykonania poniższych kroków jest wymagany profil Azure CDN. Przed kontynuowaniem zapoznaj się z tematem [Tworzenie profilu Azure CDN i punktu końcowego](cdn-create-new-endpoint.md) .

## <a name="enable-logging-with-the-azure-portal"></a>Włączanie rejestrowania w witrynie Azure Portal

Wykonaj następujące kroki, aby włączyć rejestrowanie dla punktu końcowego Azure CDN:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

2. W Azure Portal przejdź do **wszystkich zasobów**  ->  **Sieć CDN — profil**

2. Wybierz punkt końcowy usługi CDN, dla którego chcesz włączyć dzienniki diagnostyczne:

    :::image type="content" source="./media/cdn-diagnostics-log/02_browse-to-diagnostics-logs.png" alt-text="Wybierz punkt końcowy usługi CDN." border="true":::

3. W sekcji **monitorowanie** wybierz pozycję **dzienniki diagnostyczne** :

    :::image type="content" source="./media/cdn-diagnostics-log/03_diagnostics-logs-options.png" alt-text="Wybierz pozycję dzienniki diagnostyczne." border="true":::

### <a name="enable-logging-with-azure-storage"></a>Włączanie rejestrowania w usłudze Azure Storage

Aby użyć konta magazynu do przechowywania dzienników, wykonaj następujące czynności:

 >[!NOTE] 
 >Aby wykonać te kroki, wymagane jest konto magazynu. Aby uzyskać więcej informacji, zobacz **[Tworzenie konta usługi Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal&toc=%2fazure%2fstorage%2fblobs%2ftoc.json)** .
    
1. W obszarze **Nazwa ustawienia diagnostycznego** wprowadź nazwę ustawień dziennika diagnostycznego.
 
2. Wybierz pozycję **Archiwizuj na koncie magazynu**, a następnie wybierz pozycję **CoreAnalytics**. 

3. W polu **przechowywanie (dni)** wybierz liczbę dni przechowywania. Przechowywanie w dniach zero przechowuje dzienniki w nieskończoność. 

4. Wybierz subskrypcję i konto magazynu dla dzienników.

    :::image type="content" source="./media/cdn-diagnostics-log/04_diagnostics-logs-storage.png" alt-text="Dzienniki diagnostyczne — magazyn." border="true":::

3. Wybierz pozycję **Zapisz**.

### <a name="send-to-log-analytics"></a>Wysyłanie do usługi Log Analytics

Aby użyć Log Analytics dzienników, wykonaj następujące kroki:

>[!NOTE] 
>Do wykonania tych kroków jest wymagany obszar roboczy usługi log Analytics. Aby uzyskać więcej informacji, zapoznaj się z tematem: **[Tworzenie obszaru roboczego log Analytics w Azure Portal](../azure-monitor/logs/quick-create-workspace.md)** .
    
1. W obszarze **Nazwa ustawienia diagnostycznego** wprowadź nazwę ustawień dziennika diagnostycznego.

2. Wybierz pozycję **Wyślij do log Analytics**, a następnie wybierz pozycję **CoreAnalytics**. 

3. Wybierz obszar roboczy subskrypcji i Log Analytics dla dzienników.

   :::image type="content" source="./media/cdn-diagnostics-log/05-la-workspace.png" alt-text="Dzienniki diagnostyczne — Log Analytics." border="true":::

4. Wybierz pozycję **Zapisz**.

### <a name="stream-to-an-event-hub"></a>Przesyłaj strumieniowo do centrum zdarzeń

Aby użyć centrum zdarzeń dla dzienników, wykonaj następujące kroki:

>[!NOTE] 
>Do wykonania tych kroków jest wymagany centrum zdarzeń. Zapoznaj się z artykułem **[Szybki Start: tworzenie centrum zdarzeń przy użyciu Azure Portal](../event-hubs/event-hubs-create.md)** , aby uzyskać więcej informacji.
    
1. W obszarze **Nazwa ustawienia diagnostycznego** wprowadź nazwę ustawień dziennika diagnostycznego.

2. Wybierz pozycję **strumień do centrum zdarzeń**, a następnie wybierz pozycję **CoreAnalytics**. 

3. Wybierz przestrzeń nazw subskrypcji i centrum zdarzeń dla dzienników.

   :::image type="content" source="./media/cdn-diagnostics-log/06-eventhub-namespace.png" alt-text="Dzienniki diagnostyczne — centrum zdarzeń." border="true":::

4. Wybierz pozycję **Zapisz**.


## <a name="enable-logging-with-powershell"></a>Włączanie rejestrowania przy użyciu programu PowerShell

Poniższy przykład przedstawia sposób włączania dzienników diagnostycznych za pomocą poleceń cmdlet Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>Włączanie dzienników diagnostycznych na koncie magazynu

1. Zaloguj się do Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. Aby włączyć dzienniki diagnostyczne na koncie magazynu, wprowadź te polecenia. Zamień zmienne na wartości:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Categories CoreAnalytics
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Włączanie dzienników diagnostycznych dla Log Analytics obszaru roboczego

1. Zaloguj się do Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Aby włączyć dzienniki diagnostyczne dla obszaru roboczego Log Analytics, wprowadź te polecenia. Zamień zmienne na wartości:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Categories CoreAnalytics
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>Włączanie dzienników diagnostycznych dla przestrzeni nazw centrum zdarzeń

1. Zaloguj się do Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Aby włączyć dzienniki diagnostyczne dla obszaru roboczego Log Analytics, wprowadź te polecenia. Zamień zmienne na wartości:

    ```azurepowershell-interactive
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $eventhubname = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhubname -Enabled $true -Categories CoreAnalytics
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Korzystanie z dzienników diagnostycznych z usługi Azure Storage
W tej sekcji opisano schemat analizy podstawowej sieci CDN, organizacja na koncie usługi Azure Storage i przedstawiono przykładowy kod służący do pobierania dzienników w pliku CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Używanie Eksplorator usługi Microsoft Azure Storage
Aby pobrać narzędzie, zobacz [Eksplorator usługi Azure Storage](https://storageexplorer.com/). Po pobraniu i zainstalowaniu oprogramowania skonfiguruj je tak, aby korzystało z tego samego konta usługi Azure Storage, które zostało skonfigurowane jako miejsce docelowe w dziennikach diagnostyki sieci CDN.

1.  Otwórz **Eksplorator usługi Microsoft Azure Storage**
2.  Lokalizowanie konta magazynu
3.  Rozwiń węzeł **kontenery obiektów BLOB** w ramach tego konta magazynu.
4.  Wybierz kontener o nazwie *Insights-Logs-coreanalytics*.
5.  Wyniki są wyświetlane w okienku po prawej stronie, rozpoczynając od pierwszego poziomu, jako *ResourceID =*. Kontynuuj Wybieranie każdego poziomu, dopóki nie znajdziesz pliku *PT1H.js*. Aby uzyskać wyjaśnienie ścieżki, zobacz [Format ścieżki obiektu BLOB](cdn-azure-diagnostic-logs.md#blob-path-format).
6.  Każdy obiekt BLOB *PT1H.jsw* pliku reprezentuje dzienniki analizy przez jedną godzinę dla określonego punktu końcowego usługi CDN lub jego domeny niestandardowej.
7.  Schemat zawartości tego pliku JSON został opisany w sekcji schemat podstawowych dzienników analitycznych.


#### <a name="blob-path-format"></a>Format ścieżki obiektu BLOB

Dzienniki analizy podstawowej są generowane co godzinę, a dane są zbierane i przechowywane w pojedynczym obiekcie blob platformy Azure jako ładunek JSON. Narzędzie Eksplorator usługi Storage interpretuje znak "/" jako separator katalogów i Wyświetla hierarchię. Ścieżka do obiektu blob platformy Azure jest wyświetlana, jeśli istnieje struktura hierarchiczna i reprezentuje nazwę obiektu BLOB. Nazwa obiektu BLOB jest zgodna z następującą konwencją nazewnictwa:    

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Opis pól:**

|Wartość|Opis|
|-------|---------|
|Identyfikator subskrypcji    |Identyfikator subskrypcji platformy Azure w formacie identyfikatora GUID.|
|Nazwa grupy zasobów |Nazwa grupy zasobów, do której należą zasoby sieci CDN.|
|Profile Name (Nazwa profilu) |Nazwa profilu CDN|
|Nazwa punktu końcowego |Nazwa punktu końcowego usługi CDN|
|Year (Rok)|  Czwarta reprezentacja roku, na przykład 2017|
|Month (Miesiąc)| Dwucyfrowa reprezentacja numeru miesiąca. 01 = styczeń... 12 = grudzień|
|Dzień|   Dwucyfrowa reprezentacja dnia miesiąca|
|PT1H.jsna| Rzeczywisty plik JSON, w którym są przechowywane dane analizy|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Eksportowanie podstawowych danych analitycznych do pliku CSV

Aby uzyskać dostęp do podstawowych analiz, podano przykładowy kod dla narzędzia. To narzędzie umożliwia pobranie plików JSON do formatu prostego pliku rozdzielonych przecinkami, który może służyć do tworzenia wykresów lub innych agregacji.

Oto jak można użyć narzędzia:

1.  Odwiedź link usługi GitHub: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Pobierz kod.
3.  Postępuj zgodnie z instrukcjami, aby skompilować i skonfigurować.
4.  Uruchom narzędzie.
5.  Utworzony plik CSV pokazuje dane analityczne w prostej płaskiej hierarchii.

## <a name="log-data-delays"></a>Opóźnienia danych dziennika

W poniższej tabeli przedstawiono opóźnienia danych dziennika dla **Azure CDN Standard od firmy Microsoft**, **Azure CDN Standard z Akamai** i **Azure CDN Standard/Premium z Verizon**.

Opóźnienia danych dziennika firmy Microsoft | Opóźnienia danych dziennika Verizon | Opóźnienia danych dziennika Akamai
--- | --- | ---
Opóźnione o 1 godzinę. | Opóźnione o 1 godzinę, co może potrwać do 2 godzin po zakończeniu propagacji punktu końcowego. | Opóźnione o 24 godziny; Jeśli utworzono więcej niż 24 godziny temu, rozpoczyna się to do 2 godzin. Jeśli został niedawno utworzony, uruchomienie dzienników może zająć do 25 godzin.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Typy dzienników diagnostycznych dla usługi CDN Core Analytics

Firma Microsoft obecnie oferuje tylko podstawowe dzienniki analityczne, które zawierają metryki przedstawiające statystyki odpowiedzi HTTP i statystyki danych wyjściowych, które są widoczne na podstawie punktów obecności/krawędzi sieci CDN.

### <a name="core-analytics-metrics-details"></a>Szczegóły metryk analizy podstawowej
W poniższej tabeli przedstawiono listę metryk dostępnych w głównych dziennikach analiz dla:

* **Azure CDN Standard od firmy Microsoft**
* **Azure CDN Standard z Akamai**
* **Azure CDN Standard/Premium z Verizon**

Nie wszystkie metryki są dostępne dla wszystkich dostawców, chociaż takie różnice są minimalne. W tabeli są również wyświetlane, czy dana metryka jest dostępna od dostawcy. Metryki są dostępne tylko dla tych punktów końcowych usługi CDN, które mają ruch na nich.


|Metric                     | Opis | Microsoft | Verizon | Akamai |
|---------------------------|-------------|-----------|---------|--------|
| RequestCountTotal         | Łączna liczba trafień żądań w tym okresie. | Tak | Tak |Tak |
| RequestCountHttpStatus2xx | Liczba wszystkich żądań, które spowodowały 2xx kod HTTP (na przykład 200, 202). | Tak | Tak |Tak |
| RequestCountHttpStatus3xx | Liczba wszystkich żądań, które spowodowały 3xx kod HTTP (na przykład 300, 302). | Tak | Tak |Tak |
| RequestCountHttpStatus4xx | Liczba wszystkich żądań, które spowodowały 4xx kod HTTP (na przykład 400, 404). | Tak | Tak |Tak |
| RequestCountHttpStatus5xx | Liczba wszystkich żądań, które spowodowały 5xx kod HTTP (na przykład 500, 504). | Tak | Tak |Tak |
| RequestCountHttpStatusOthers | Liczba pozostałych kodów HTTP (poza 2xx-5xx). | Tak | Tak |Tak |
| RequestCountHttpStatus200 | Liczba wszystkich żądań, które spowodowały odpowiedź na kod HTTP 200. | Tak | Nie  |Tak |
| RequestCountHttpStatus206 | Liczba wszystkich żądań, które spowodowały odpowiedź na kod HTTP 206. | Tak | Nie  |Tak |
| RequestCountHttpStatus302 | Liczba wszystkich żądań, które spowodowały odpowiedź na kod HTTP 302. | Tak | Nie  |Tak |
| RequestCountHttpStatus304 | Liczba wszystkich żądań, które spowodowały odpowiedź na kod HTTP 304. | Tak | Nie  |Tak |
| RequestCountHttpStatus404 | Liczba wszystkich żądań, które spowodowały odpowiedź na kod HTTP 404. | Tak | Nie  |Tak |
| RequestCountCacheHit | Liczba wszystkich żądań, które spowodowały trafienie pamięci podręcznej. Zasób został obsłużony bezpośrednio z punktu POP do klienta. | Tak | Tak | Nie  |
| RequestCountCacheMiss | Liczba wszystkich żądań, które spowodowały odrzucenie pamięci podręcznej. Chybienia w pamięci podręcznej oznacza, że zasób nie został odnaleziony w punkcie POP najbliżej klienta i został pobrany z lokalizacji źródłowej. | Tak | Tak | Nie |
| RequestCountCacheNoCache | Liczba wszystkich żądań do elementu zawartości, które nie są przechowywane w pamięci podręcznej z powodu konfiguracji użytkownika na krawędzi. | Tak | Tak | Nie |
| RequestCountCacheUncacheable | Liczba wszystkich żądań do zasobów, które nie są przechowywane w pamięci podręcznej przez Cache-Control i wygaśnięcia nagłówków zasobów. Ta liczba wskazuje, że nie powinna być buforowana w punkcie POP ani przez klienta HTTP. | Tak | Tak | Nie |
| RequestCountCacheOthers | Liczba wszystkich żądań ze stanem pamięci podręcznej, które nie zostały omówione powyżej. | Nie | Tak | Nie  |
| EgressTotal | Wychodzący transfer danych w GB | Tak |Tak |Tak |
| EgressHttpStatus2xx | Wychodzący transfer danych * dla odpowiedzi z kodami stanu HTTP 2xx w GB. | Tak | Tak | Nie  |
| EgressHttpStatus3xx | Wychodzący transfer danych dla odpowiedzi z kodami stanu HTTP 3xx w GB. | Tak | Tak | Nie  |
| EgressHttpStatus4xx | Wychodzący transfer danych dla odpowiedzi z kodami stanu HTTP 4xx w GB. | Tak | Tak | Nie  |
| EgressHttpStatus5xx | Wychodzący transfer danych dla odpowiedzi z kodami stanu HTTP 5xx w GB. | Tak | Tak | Nie |
| EgressHttpStatusOthers | Wychodzący transfer danych dla odpowiedzi z innymi kodami stanu HTTP w GB. | Tak | Tak | Nie  |
| EgressCacheHit | Wychodzący transfer danych dla odpowiedzi dostarczonych bezpośrednio z pamięci podręcznej usługi CDN w przypadku punktów obecności/krawędzi sieci CDN. | Tak | Tak | Nie |
| EgressCacheMiss. | Wychodzący transfer danych dla odpowiedzi, które nie zostały znalezione na najbliższym serwerze POP i pobierane z serwera pochodzenia. | Tak | Tak | Nie |
| EgressCacheNoCache | Wychodzący transfer danych dla zasobów, które nie są przechowywane w pamięci podręcznej z powodu konfiguracji użytkownika na krawędzi. | Tak | Tak | Nie |
| EgressCacheUncacheable | Wychodzący transfer danych dla zasobów, które nie są przechowywane w pamięci podręcznej przez Cache-Control i lub wygaśnięcia nagłówków elementu zawartości. Wskazuje, że nie powinna być buforowana w punkcie POP ani przez klienta HTTP. | Tak | Tak | Nie |
| EgressCacheOthers | Wychodzące transfery danych dla innych scenariuszy pamięci podręcznej. | Nie | Tak | Nie |

* Wychodzący transfer danych odnosi się do ruchu dostarczonego z serwerów POP usługi CDN do klienta.


### <a name="schema-of-the-core-analytics-logs"></a>Schemat najważniejszych dzienników analitycznych 

Wszystkie dzienniki są przechowywane w formacie JSON, a każdy wpis zawiera pola ciągów zgodnie z następującym schematem:

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

*Czas* , w którym przedstawia czas rozpoczęcia granicy godziny, dla której raportowane są statystyki. Metryka nieobsługiwana przez dostawcę sieci CDN zamiast wartości typu Double lub Integer powoduje, że wartość jest równa null. Ta wartość null wskazuje brak metryki i różni się od wartości 0. Jeden zestaw tych metryk na domenę jest skonfigurowany w punkcie końcowym.

Przykładowe właściwości:

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```

## <a name="additional-resources"></a>Dodatkowe zasoby

* [Dzienniki diagnostyczne platformy Azure](../azure-monitor/essentials/platform-logs-overview.md)
* [Analiza podstawowa za pośrednictwem Azure CDN Portal uzupełniający](./cdn-analyze-usage-patterns.md)
* [Dzienniki usługi Azure Monitor](../azure-monitor/logs/log-query-overview.md)
* [Interfejs API REST usługi Azure Log Analytics](/rest/api/loganalytics)