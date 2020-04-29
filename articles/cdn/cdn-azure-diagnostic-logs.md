---
title: Dzienniki diagnostyczne platformy Azure | Microsoft Docs
description: Klient może włączyć analizę dzienników dla Azure CDN.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/06/2018
ms.author: allensu
ms.openlocfilehash: 35d028a38e6ac19f270abcc8708a532b3749eb39
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81254805"
---
# <a name="azure-diagnostic-logs"></a>Dzienniki diagnostyczne platformy Azure

Korzystając z dzienników diagnostycznych platformy Azure, można wyświetlić podstawowe analizy i zapisać je w jednym lub większej liczbie miejsc docelowych, w tym:

 - Konto usługi Azure Storage
 - Azure Event Hubs
 - [Obszar roboczy usługi Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started)
 
Ta funkcja jest dostępna w punktach końcowych usługi CDN dla wszystkich warstw cenowych. 

Dzienniki diagnostyczne platformy Azure umożliwiają eksportowanie podstawowych metryk użycia z punktu końcowego sieci CDN do różnych źródeł, dzięki czemu można je wykorzystać w dostosowany sposób. Można na przykład wykonać następujące rodzaje eksportu danych:

- Eksportuj dane do magazynu obiektów blob, Eksportuj do woluminów CSV i Generuj wykresy w programie Excel.
- Eksportuj dane do Event Hubs i skorelowane z danymi z innych usług platformy Azure.
- Eksportuj dane do Azure Monitor dzienników i wyświetlaj dane we własnym Log Analytics obszarze roboczym

Na poniższym diagramie przedstawiono typowy widok podstawowych analiz usługi CDN dla danych.

![Portal — dzienniki diagnostyki](./media/cdn-diagnostics-log/01_OMS-workspace.png)

*Rysunek 1 — widok usługi CDN Core Analytics*

Aby uzyskać więcej informacji na temat dzienników diagnostycznych, zobacz [dzienniki diagnostyczne](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="enable-logging-with-the-azure-portal"></a>Włączanie rejestrowania w witrynie Azure Portal

Wykonaj następujące kroki, aby włączyć rejestrowanie w usłudze CDN Core Analytics:

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). Jeśli nie masz jeszcze włączonej usługi CDN dla przepływu pracy, przed kontynuowaniem [Utwórz profil Azure CDN i punkt końcowy](cdn-create-new-endpoint.md) .

1. W Azure Portal przejdź do **profilu CDN**.

2. W Azure Portal Wyszukaj profil usługi CDN lub wybierz go z pulpitu nawigacyjnego. Następnie wybierz punkt końcowy usługi CDN, dla którego chcesz włączyć dzienniki diagnostyczne.

    ![Portal — dzienniki diagnostyki](./media/cdn-diagnostics-log/02_Browse-to-Diagnostics-logs.png)

3. W sekcji monitorowanie wybierz pozycję **dzienniki diagnostyczne** .

   Zostanie wyświetlona strona **dzienniki diagnostyki** .

    ![Portal — dzienniki diagnostyki](./media/cdn-diagnostics-log/03_Diagnostics-logs-options.png)

### <a name="enable-logging-with-azure-storage"></a>Włączanie rejestrowania w usłudze Azure Storage

Aby użyć konta magazynu do przechowywania dzienników, wykonaj następujące czynności:
    
1. W obszarze **Nazwa**wprowadź nazwę ustawień dziennika diagnostycznego.
 
2. Wybierz pozycję **Archiwizuj na koncie magazynu**, a następnie wybierz pozycję **CoreAnalytics**. 

2. W polu **przechowywanie (dni)** wybierz liczbę dni przechowywania. Przechowywanie w dniach zero przechowuje dzienniki w nieskończoność. 

    ![Portal — dzienniki diagnostyki](./media/cdn-diagnostics-log/04_Diagnostics-logs-storage.png) 

3. Wybierz pozycję **konto magazynu**.

    Zostanie wyświetlona strona **Wybierz konto magazynu** .

4. Wybierz konto magazynu z listy rozwijanej, a następnie wybierz przycisk **OK**.

    ![Portal — dzienniki diagnostyki](./media/cdn-diagnostics-log/cdn-select-storage-account.png)

5. Po zakończeniu wprowadzania ustawień dzienników diagnostycznych wybierz pozycję **Zapisz**.

### <a name="logging-with-azure-monitor"></a>Rejestrowanie przy użyciu Azure Monitor

Aby użyć Azure Monitor do przechowywania dzienników, wykonaj następujące kroki:

1. Na stronie **dzienniki diagnostyczne** wybierz pozycję **Wyślij do log Analytics**. 

    ![Portal — dzienniki diagnostyki](./media/cdn-diagnostics-log/05_Ready-to-Configure.png)    

2. Wybierz pozycję **Konfiguruj** , aby skonfigurować rejestrowanie Azure monitor. 

   Zostanie wyświetlona strona **obszary robocze log Analytics** .

    >[!NOTE] 
    >Obszary robocze OMS są teraz nazywane obszarami roboczymi usługi Log Analytics.

    ![Portal — dzienniki diagnostyki](./media/cdn-diagnostics-log/06_Choose-workspace.png)

3. Wybierz pozycję **Utwórz nowy obszar roboczy**.

    Zostanie wyświetlona strona **log Analytics obszaru roboczego** .

    >[!NOTE] 
    >Obszary robocze OMS są teraz nazywane obszarami roboczymi usługi Log Analytics.

    ![Portal — dzienniki diagnostyki](./media/cdn-diagnostics-log/07_Create-new.png)

4. W **obszarze obszar roboczy log Analytics**wprowadź nazwę log Analytics obszaru roboczego. Nazwa obszaru roboczego Log Analytics musi być unikatowa i zawierać tylko litery, cyfry i łączniki; spacje i znaki podkreślenia są niedozwolone. 

5. W obszarze **subskrypcja**wybierz istniejącą subskrypcję z listy rozwijanej. 

6. W obszarze **Grupa zasobów**Utwórz nową grupę zasobów lub wybierz istniejącą.

7. W polu **Lokalizacja**wybierz lokalizację z listy.

8. Wybierz pozycję **Przypnij do pulpitu nawigacyjnego** , jeśli chcesz zapisać konfigurację dziennika na pulpicie nawigacyjnym. 

9. Wybierz **przycisk OK** , aby zakończyć konfigurację.

10. Po utworzeniu obszaru roboczego nastąpi powrót do strony **dzienników diagnostycznych** . Potwierdź nazwę nowego obszaru roboczego Log Analytics.

    ![Portal — dzienniki diagnostyki](./media/cdn-diagnostics-log/09_Return-to-logging.png)

11. Wybierz pozycję **CoreAnalytics**, a następnie wybierz pozycję **Zapisz**.

12. Aby wyświetlić nowy obszar roboczy Log Analytics, wybierz opcję **podstawowe analizy** ze strony punktu końcowego usługi CDN.

    ![Portal — dzienniki diagnostyki](./media/cdn-diagnostics-log/cdn-core-analytics-page.png) 

    Obszar roboczy Log Analytics jest teraz gotowy do rejestrowania danych. Aby można było korzystać z tych danych, należy użyć [rozwiązania Azure monitor dzienników](#consuming-diagnostics-logs-from-a-log-analytics-workspace), które opisano w dalszej części tego artykułu.

Aby uzyskać więcej informacji na temat opóźnień danych dziennika, zobacz [opóźnienia danych dziennika](#log-data-delays).

## <a name="enable-logging-with-powershell"></a>Włączanie rejestrowania przy użyciu programu PowerShell

Poniższy przykład przedstawia sposób włączania dzienników diagnostycznych za pomocą poleceń cmdlet Azure PowerShell.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enabling-diagnostic-logs-in-a-storage-account"></a>Włączanie dzienników diagnostycznych na koncie magazynu

1. Zaloguj się i wybierz subskrypcję:

    Connect-AzAccount 

    SELECT-AzureSubscription-Identyfikator subskrypcji 

2. Aby włączyć dzienniki diagnostyczne na koncie magazynu, wprowadź następujące polecenie:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
    ```

3. Aby włączyć dzienniki diagnostyki w obszarze roboczym Log Analytics, wprowadź następujące polecenie:

    ```powershell
    Set-AzDiagnosticSetting -ResourceId "/subscriptions/`{subscriptionId}<subscriptionId>
    .<subscriptionName>" -WorkspaceId "/subscriptions/<workspaceId>.<workspaceName>" -Enabled $true -Categories CoreAnalytics 
    ```

## <a name="consuming-diagnostics-logs-from-azure-storage"></a>Korzystanie z dzienników diagnostycznych z usługi Azure Storage
W tej sekcji opisano schemat analizy podstawowej sieci CDN, sposób jej organizowania w ramach konta usługi Azure Storage i przedstawiono przykładowy kod służący do pobierania dzienników w pliku CSV.

### <a name="using-microsoft-azure-storage-explorer"></a>Używanie Eksplorator usługi Microsoft Azure Storage
Aby uzyskać dostęp do podstawowych danych analitycznych z konta usługi Azure Storage, musisz najpierw uzyskać dostęp do zawartości na koncie magazynu. Chociaż na rynku jest dostępnych kilka narzędzi, zaleca się Eksplorator usługi Microsoft Azure Storage. Aby pobrać narzędzie, zobacz [Eksplorator usługi Azure Storage](https://storageexplorer.com/). Po pobraniu i zainstalowaniu oprogramowania skonfiguruj je tak, aby korzystało z tego samego konta usługi Azure Storage, które zostało skonfigurowane jako miejsce docelowe w dziennikach diagnostyki sieci CDN.

1.  Otwórz **Eksplorator usługi Microsoft Azure Storage**
2.  Lokalizowanie konta magazynu
3.  Rozwiń węzeł **kontenery obiektów BLOB** w ramach tego konta magazynu.
4.  Wybierz kontener o nazwie *Insights-Logs-coreanalytics*.
5.  Wyniki są wyświetlane w okienku po prawej stronie, rozpoczynając od pierwszego poziomu, jako *ResourceID =*. Kontynuuj Wybieranie każdego poziomu, aż znajdziesz plik *PT1H. JSON*. Aby uzyskać wyjaśnienie ścieżki, zobacz [Format ścieżki obiektu BLOB](cdn-azure-diagnostic-logs.md#blob-path-format).
6.  Każdy plik BLOB *PT1H. JSON* reprezentuje dzienniki analizy przez godzinę dla określonego punktu końcowego usługi CDN lub jego domeny niestandardowej.
7.  Schemat zawartości tego pliku JSON został opisany w sekcji schemat podstawowych dzienników analitycznych.


#### <a name="blob-path-format"></a>Format ścieżki obiektu BLOB

Dzienniki analizy podstawowej są generowane co godzinę, a dane są zbierane i przechowywane w pojedynczym obiekcie blob platformy Azure jako ładunek JSON. Ponieważ narzędzie Eksplorator magazynu interpretuje znak "/" jako separator katalogów i Wyświetla hierarchię, ścieżka do obiektu blob platformy Azure jest wyświetlana tak, jakby istnieje struktura hierarchiczna i reprezentuje nazwę obiektu BLOB. Nazwa obiektu BLOB jest zgodna z następującą konwencją nazewnictwa:   

```resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json```

**Opis pól:**

|Wartość|Opis|
|-------|---------|
|Identyfikator subskrypcji    |Identyfikator subskrypcji platformy Azure w formacie identyfikatora GUID.|
|Nazwa grupy zasobów |Nazwa grupy zasobów, do której należą zasoby sieci CDN.|
|Profile Name (Nazwa profilu) |Nazwa profilu CDN|
|Nazwa punktu końcowego |Nazwa punktu końcowego usługi CDN|
|Year|  Czwarta reprezentacja roku, na przykład 2017|
|Month| Dwucyfrowa reprezentacja numeru miesiąca. 01 = styczeń... 12 = grudzień|
|Day|   Dwucyfrowa reprezentacja dnia miesiąca|
|PT1H. JSON| Rzeczywisty plik JSON, w którym są przechowywane dane analizy|

### <a name="exporting-the-core-analytics-data-to-a-csv-file"></a>Eksportowanie podstawowych danych analitycznych do pliku CSV

Aby ułatwić dostęp do podstawowych analiz, podano przykładowy kod dla narzędzia. To narzędzie umożliwia pobranie plików JSON do formatu prostego pliku rozdzielonych przecinkami, który może służyć do tworzenia wykresów lub innych agregacji.

Oto jak można użyć narzędzia:

1.  Odwiedź link usługi GitHub:[https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv)
2.  Pobierz kod.
3.  Postępuj zgodnie z instrukcjami, aby skompilować i skonfigurować.
4.  Uruchom narzędzie.
5.  Utworzony plik CSV pokazuje dane analityczne w prostej płaskiej hierarchii.

## <a name="consuming-diagnostics-logs-from-a-log-analytics-workspace"></a>Korzystanie z dzienników diagnostycznych z obszaru roboczego usługi Log Analytics
Azure Monitor to usługa platformy Azure, która monitoruje środowiska chmurowe i lokalne w celu utrzymania ich dostępności i wydajności. Zbiera ona dane generowane przez zasoby w środowiskach chmurowych i lokalnych oraz inne narzędzia do monitorowania, aby przeprowadzać analizę na podstawie wielu źródeł. 

Aby użyć Azure Monitor, musisz [włączyć rejestrowanie](#enable-logging-with-azure-storage) w obszarze roboczym usługi Azure log Analytics, który został omówiony wcześniej w tym artykule.

### <a name="using-the-log-analytics-workspace"></a>Korzystanie z obszaru roboczego Log Analytics

 Na poniższym diagramie przedstawiono architekturę danych wejściowych i wyjściowych repozytorium:

![Obszar roboczy usługi Log Analytics](./media/cdn-diagnostics-log/12_Repo-overview.png)

*Rysunek 3 — repozytorium Log Analytics*

Możesz wyświetlić dane na różne sposoby przy użyciu rozwiązań do zarządzania. Rozwiązania do zarządzania można uzyskać z [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions).

Rozwiązania do monitorowania można instalować z poziomu portalu Azure Marketplace, wybierając link **Uzyskaj teraz** w dolnej części każdego rozwiązania.

### <a name="add-an-azure-monitor-cdn-monitoring-solution"></a>Dodawanie rozwiązania do monitorowania Azure Monitor CDN

Wykonaj następujące kroki, aby dodać rozwiązanie do monitorowania Azure Monitor:

1.   Zaloguj się do Azure Portal przy użyciu subskrypcji platformy Azure i przejdź do pulpitu nawigacyjnego.
    ![Pulpit nawigacyjny platformy Azure](./media/cdn-diagnostics-log/13_Azure-dashboard.png)

2. Na stronie **Nowy** w obszarze **Marketplace**wybierz pozycję **monitorowanie i zarządzanie**.

    ![Portal Marketplace](./media/cdn-diagnostics-log/14_Marketplace.png)

3. Na stronie **monitorowanie i zarządzanie** wybierz pozycję **Zobacz wszystko**.

    ![Zobacz wszystko](./media/cdn-diagnostics-log/15_See-all.png)

4. Wyszukaj usługę CDN w polu wyszukiwania.

    ![Zobacz wszystko](./media/cdn-diagnostics-log/16_Search-for.png)

5. Wybierz pozycję **Azure CDN Core Analytics**. 

    ![Zobacz wszystko](./media/cdn-diagnostics-log/17_Core-analytics.png)

6. Po wybraniu opcji **Utwórz**zostanie wyświetlony monit o utworzenie nowego obszaru roboczego log Analytics lub użycie istniejącego. 

    ![Zobacz wszystko](./media/cdn-diagnostics-log/18_Adding-solution.png)

7. Wybierz utworzony wcześniej obszar roboczy. Następnie należy dodać konto usługi Automation.

    ![Zobacz wszystko](./media/cdn-diagnostics-log/19_Add-automation.png)

8. Na poniższym ekranie przedstawiono formularz konta usługi Automation, który należy wypełnić. 

    ![Zobacz wszystko](./media/cdn-diagnostics-log/20_Automation.png)

9. Po utworzeniu konta usługi Automation możesz dodać swoje rozwiązanie. Wybierz przycisk **Utwórz**.

    ![Zobacz wszystko](./media/cdn-diagnostics-log/21_Ready.png)

10. Twoje rozwiązanie zostało już dodane do obszaru roboczego. Wróć do pulpitu nawigacyjnego Azure Portal.

    ![Zobacz wszystko](./media/cdn-diagnostics-log/22_Dashboard.png)

    Wybierz obszar roboczy Log Analytics, który został utworzony, aby przejść do obszaru roboczego. 

11. Wybierz kafelek **Portal pakietu OMS** , aby zobaczyć nowe rozwiązanie.

    ![Zobacz wszystko](./media/cdn-diagnostics-log/23_workspace.png)

12. Portal powinien teraz wyglądać tak, jak na poniższym ekranie:

    ![Zobacz wszystko](./media/cdn-diagnostics-log/24_OMS-solution.png)

    Wybierz jeden z kafelków, aby zobaczyć kilka widoków w danych.

    ![Zobacz wszystko](./media/cdn-diagnostics-log/25_Interior-view.png)

    Możesz przewijać w lewo lub w prawo, aby zobaczyć dalsze kafelki reprezentujące poszczególne widoki w danych. 

    Wybierz jeden z kafelków, aby zobaczyć więcej szczegółów na temat danych.

     ![Zobacz wszystko](./media/cdn-diagnostics-log/26_Further-detail.png)

### <a name="offers-and-pricing-tiers"></a>Oferty i warstwy cenowe

W [tym miejscu](https://docs.microsoft.com/azure/log-analytics/log-analytics-add-solutions)możesz zobaczyć oferty i warstwy cenowe dla rozwiązań do zarządzania.

### <a name="customizing-views"></a>Dostosowywanie widoków

Widok można dostosować do swoich danych przy użyciu **projektanta widoków**. Aby rozpocząć projektowanie, przejdź do obszaru roboczego Log Analytics i wybierz kafelek **Projektant widoków** .

![Projektant widoków](./media/cdn-diagnostics-log/27_Designer.png)

Przeciągnij i upuść typy wykresów i wypełnij szczegóły danych, które chcesz analizować.

![Projektant widoków](./media/cdn-diagnostics-log/28_Designer.png)

    
## <a name="log-data-delays"></a>Opóźnienia danych dziennika

W poniższej tabeli przedstawiono opóźnienia danych dziennika dla **Azure CDN Standard od firmy Microsoft**, **Azure CDN Standard z Akamai**i **Azure CDN Standard/Premium z Verizon**.

Opóźnienia danych dziennika firmy Microsoft | Opóźnienia danych dziennika Verizon | Opóźnienia danych dziennika Akamai
--- | --- | ---
Opóźnione o 1 godzinę. | Opóźnione o 1 godzinę, co może potrwać do 2 godzin po zakończeniu propagacji punktu końcowego. | Opóźnione o 24 godziny; Jeśli utworzono więcej niż 24 godziny temu, rozpoczyna się to do 2 godzin. Jeśli został niedawno utworzony, uruchomienie dzienników może zająć do 25 godzin.

## <a name="diagnostic-log-types-for-cdn-core-analytics"></a>Typy dzienników diagnostycznych dla usługi CDN Core Analytics

Firma Microsoft obecnie oferuje tylko podstawowe dzienniki analityczne, które zawierają metryki przedstawiające statystyki odpowiedzi HTTP i statystyki danych wyjściowych, które są widoczne na podstawie punktów obecności/krawędzi sieci CDN.

### <a name="core-analytics-metrics-details"></a>Szczegóły metryk analizy podstawowej
W poniższej tabeli przedstawiono listę metryk dostępnych w podstawowych dziennikach analizy dla **Azure CDN standard firmy Microsoft**, **Azure CDN Standard from Akamai**oraz **Azure CDN Standard/Premium z Verizon**. Nie wszystkie metryki są dostępne dla wszystkich dostawców, chociaż takie różnice są minimalne. W tabeli są również wyświetlane, czy dana metryka jest dostępna od dostawcy. Metryki są dostępne tylko dla tych punktów końcowych usługi CDN, które mają ruch na nich.


|Metryka                     | Opis | Microsoft | Verizon | Akamai |
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
| RequestCountCacheMiss | Liczba wszystkich żądań, które spowodowały odrzucenie pamięci podręcznej. Chybienia w pamięci podręcznej oznacza, że zasób nie został odnaleziony w punkcie POP najbliżej klienta i dlatego został pobrany z lokalizacji źródłowej. | Tak | Tak | Nie |
| RequestCountCacheNoCache | Liczba wszystkich żądań do elementu zawartości, które nie są buforowane z powodu konfiguracji użytkownika na krawędzi. | Tak | Tak | Nie |
| RequestCountCacheUncacheable | Liczba wszystkich żądań do zasobów, które nie są przechowywane w pamięci podręcznej przez kontrolę i nagłówki elementów zawartości, co wskazuje, że nie powinna być buforowana w punkcie POP ani przez klienta HTTP. | Tak | Tak | Nie |
| RequestCountCacheOthers | Liczba wszystkich żądań ze stanem pamięci podręcznej, które nie zostały omówione powyżej. | Nie | Tak | Nie  |
| EgressTotal | Wychodzący transfer danych w GB | Tak |Tak |Tak |
| EgressHttpStatus2xx | Wychodzący transfer danych * dla odpowiedzi z kodami stanu HTTP 2xx w GB. | Tak | Tak | Nie  |
| EgressHttpStatus3xx | Wychodzący transfer danych dla odpowiedzi z kodami stanu HTTP 3xx w GB. | Tak | Tak | Nie  |
| EgressHttpStatus4xx | Wychodzący transfer danych dla odpowiedzi z kodami stanu HTTP 4xx w GB. | Tak | Tak | Nie  |
| EgressHttpStatus5xx | Wychodzący transfer danych dla odpowiedzi z kodami stanu HTTP 5xx w GB. | Tak | Tak | Nie |
| EgressHttpStatusOthers | Wychodzący transfer danych dla odpowiedzi z innymi kodami stanu HTTP w GB. | Tak | Tak | Nie  |
| EgressCacheHit | Wychodzący transfer danych dla odpowiedzi dostarczonych bezpośrednio z pamięci podręcznej usługi CDN w przypadku punktów obecności/krawędzi sieci CDN. | Tak | Tak | Nie |
| EgressCacheMiss. | Wychodzący transfer danych dla odpowiedzi, które nie zostały odnalezione na najbliższym serwerze POP i pobierany z serwera pochodzenia. | Tak | Tak | Nie |
| EgressCacheNoCache | Wychodzący transfer danych dla zasobów, które nie są buforowane ze względu na konfigurację użytkownika na krawędzi. | Tak | Tak | Nie |
| EgressCacheUncacheable | Wychodzący transfer danych dla zasobów, które nie są przechowywane w pamięci podręcznej przez kontrolę i/lub nagłówki elementu zawartości. Wskazuje, że nie powinna być buforowana w punkcie POP ani przez klienta HTTP. | Tak | Tak | Nie |
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

*Czas* , w którym przedstawia czas rozpoczęcia granicy godziny, dla której raportowane są statystyki. Gdy dostawca sieci CDN nie obsługuje metryki, a nie wartości podwójnej lub całkowitej, istnieje wartość null. Ta wartość null wskazuje brak metryki i różni się od wartości 0. Istnieje jeden zestaw tych metryk dla domeny skonfigurowany w punkcie końcowym.

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

* [Dzienniki diagnostyczne platformy Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Analiza podstawowa za pośrednictwem Azure CDN Portal uzupełniający](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)
* [Dzienniki usługi Azure Monitor](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)
* [Interfejs API REST usługi Azure Log Analytics](https://docs.microsoft.com/rest/api/loganalytics)







