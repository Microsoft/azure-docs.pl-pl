---
title: Monitorowanie metryk i nieprzetworzonych dzienników dla Azure CDN firmy Microsoft
description: W tym artykule opisano Azure CDN z metryk monitorowania firmy Microsoft i dzienników nieprzetworzonych.
services: cdn
author: asudbring
manager: KumudD
ms.service: azure-cdn
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/25/2020
ms.author: allensu
ms.openlocfilehash: c41bf8bc6e5aa3749786bc1189343dfdebdc1508
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91321153"
---
# <a name="monitoring-metrics-and-raw-logs-for-azure-cdn-from-microsoft"></a>Monitorowanie metryk i nieprzetworzonych dzienników dla Azure CDN firmy Microsoft
Dzięki Azure CDN firmy Microsoft można monitorować zasoby w następujący sposób, aby ułatwić rozwiązywanie problemów, śledzenie i debugowanie. 

* Dzienniki pierwotne zawierają bogate informacje dotyczące każdego żądania odbieranego przez usługę CDN. Dzienniki pierwotne różnią się od dzienników aktywności. Dzienniki aktywności zapewniają wgląd w operacje wykonywane w zasobach platformy Azure.
* Metryki, które wyświetlają cztery kluczowe metryki w sieci CDN, w tym Współczynnik trafień bajtów, liczbę żądań, rozmiar odpowiedzi i łączny czas oczekiwania. Zapewnia również różne wymiary, aby podzielić metryki.
* Alert, który umożliwia klientowi skonfigurowanie alertu dotyczącego kluczowych metryk
* Dodatkowe metryki, które umożliwiają klientom korzystanie z usługi Azure Log Analytics w celu włączenia dodatkowych metryk wartości. Udostępniamy również przykłady zapytań dla kilku innych metryk w ramach usługi Azure Log Analytics.

> [!IMPORTANT]
> Funkcja dzienników RAW protokołu HTTP jest dostępna dla Azure CDN firmy Microsoft.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="configuration---azure-portal"></a>Konfiguracja — Azure Portal

Aby skonfigurować dzienniki pierwotne dla Azure CDN z profilu Microsoft: 

1. Z menu Azure Portal wybierz pozycję **wszystkie zasoby**  >>  **\<your-CDN-profile>** .

2. W obszarze **Monitorowanie** wybierz pozycję **Ustawienia diagnostyki**.

3. Wybierz pozycję **+ Dodaj ustawienie diagnostyczne**.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-01.png" alt-text="Dodaj ustawienie diagnostyczne dla profilu CDN." border="true":::
    
    > [!IMPORTANT]
    > Dzienniki RAW są dostępne tylko na poziomie profilu, podczas gdy zagregowane dzienniki kodów stanu HTTP są dostępne na poziomie punktu końcowego.

4. W obszarze **Ustawienia diagnostyczne**wprowadź nazwę ustawienia diagnostycznego w obszarze **Nazwa ustawień diagnostycznych**.

5. Wybierz **AzureCdnAccessLog** i Ustaw czas przechowywania w dniach.

6. Wybierz **szczegóły lokalizacji docelowej**. Opcje miejsca docelowego to:
    * **Wysyłanie do usługi Log Analytics**
        * Wybierz **obszar roboczy** **subskrypcja** i log Analytics.
    * **Archiwizowanie na koncie magazynu**
        * Wybierz **subskrypcję** i **konto magazynu**.
    * **Przesyłanie strumieniowe do centrum zdarzeń**
        * Wybierz **subskrypcję**, **przestrzeń nazw centrum zdarzeń**, **nazwę centrum zdarzeń (opcjonalnie)** i **nazwę zasad centrum zdarzeń**.

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-02.png" alt-text="Skonfiguruj miejsce docelowe dla ustawień dziennika." border="true":::

7. Wybierz pozycję **Zapisz**.

## <a name="configuration---azure-powershell"></a>Konfiguracja — Azure PowerShell

Użyj [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting) , aby skonfigurować ustawienie diagnostyczne dla nieprzetworzonych dzienników.

Dane przechowywania są definiowane przez opcję **-RetentionInDays** w poleceniu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="enable-diagnostic-logs-in-a-storage-account"></a>Włączanie dzienników diagnostycznych na koncie magazynu

1. Zaloguj się do Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```

2. Aby włączyć dzienniki diagnostyczne na koncie magazynu, wprowadź te polecenia. Zamień zmienne na wartości:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $storageacct = <your-storage-account-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $storage = Get-AzStorageAccount -ResourceGroupName $rsg -Name $storageacct

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -StorageAccountId $storage.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

### <a name="enable-diagnostics-logs-for-log-analytics-workspace"></a>Włączanie dzienników diagnostycznych dla Log Analytics obszaru roboczego

1. Zaloguj się do Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Aby włączyć dzienniki diagnostyczne dla obszaru roboczego Log Analytics, wprowadź te polecenia. Zamień zmienne na wartości:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $workspacename = <your-log-analytics-workspace-name>
    $diagname = <your-diagnostic-setting-name>
    $days = '30'

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $workspace = Get-AzOperationalInsightsWorkspace -ResourceGroupName $rsg -Name $workspacename

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -WorkspaceId $workspace.ResourceId -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```
### <a name="enable-diagnostics-logs-for-event-hub-namespace"></a>Włączanie dzienników diagnostycznych dla przestrzeni nazw centrum zdarzeń

1. Zaloguj się do Azure PowerShell:

    ```azurepowershell-interactive
    Connect-AzAccount 
    ```
2. Aby włączyć dzienniki diagnostyczne dla przestrzeni nazw centrum zdarzeń, wprowadź te polecenia. Zamień zmienne na wartości:

    ```azurepowershell-interactive
    ## Variables for the commands ##
    $rsg = <your-resource-group-name>
    $cdnprofile = <your-cdn-profile-name>
    $cdnendpoint = <your-cdn-endpoint-name>
    $evthubnamespace = <your-event-hub-namespace-name>
    $diagname = <your-diagnostic-setting-name>

    $cdn = Get-AzCdnEndpoint -ResourceGroupName $rsg -ProfileName $cdnprofile -EndpointName $cdnendpoint

    $eventhub = Get-AzEventHubNamespace -ResourceGroupName $rsg -Name $eventhubname

    Set-AzDiagnosticSetting -Name $diagname -ResourceId $cdn.id -EventHubName $eventhub.id -Enabled $true -Category AzureCdnAccessLog -RetentionEnabled 1 -RetentionInDays $days
    ```

## <a name="raw-logs-properties"></a>Właściwości dzienników nieprzetworzonych

Azure CDN z usługi firmy Microsoft obecnie udostępnia dzienniki pierwotne. Dzienniki RAW zapewniają pojedyncze żądania interfejsu API za pomocą każdego wpisu, który ma następujący schemat: 

| Właściwość              | Opis                                                                                                                                                                                          |
|-----------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| TrackingReference     | Unikatowy ciąg odwołania, który identyfikuje żądanie obsługiwane przez tylne drzwi, również wysyłany jako nagłówek X-Azure-ref do klienta. Wymagane do wyszukiwania szczegółowych informacji w dziennikach dostępu dla określonego żądania. |
| HttpMethod            | Metoda HTTP używana przez żądanie.                                                                                                                                                                     |
| HttpVersion           | Typ żądania lub połączenia.                                                                                                                                                                   |
| RequestUri            | Identyfikator URI odebranego żądania.                                                                                                                                                                         |
| RequestBytes          | Rozmiar komunikatu żądania HTTP w bajtach, w tym nagłówki żądań i treść żądania.                                                                                                   |
| ResponseBytes         | Bajty wysłane przez serwer wewnętrznej bazy danych jako odpowiedź.                                                                                                                                                    |
| UserAgent             | Typ przeglądarki używany przez klienta.                                                                                                                                                               |
| ClientIp              | Adres IP klienta, który wykonał żądanie.                                                                                                                                                  |
| TimeTaken             | Czas trwania akcji (w milisekundach).                                                                                                                                            |
| To elementu SecurityProtocol      | Wersja protokołu TLS/SSL używana przez żądanie lub wartość null, jeśli nie ma szyfrowania.                                                                                                                           |
| Punkt końcowy              | Host punktu końcowego usługi CDN został skonfigurowany w ramach nadrzędnego profilu CDN.                                                                                                                                   |
| Nazwa hosta zaplecza     | Nazwa hosta zaplecza lub źródła, w którym są wysyłane żądania.                                                                                                                                |
| Wysłane do osłony pochodzenia </br> (przestarzałe) * **zapoznaj się z uwagami dotyczącymi zaniechania poniżej.** | Jeśli wartość jest równa true, oznacza to, że żądanie zostało odebrane z pamięci podręcznej osłony pochodzenia, zamiast w punkcie obecności krawędzi. Tarcza źródła jest nadrzędną pamięcią podręczną służącą do usprawnienia współczynnika trafień pamięci podręcznej.                                       |
| isReceivedFromClient | Jeśli wartość jest równa true, oznacza to, że żądanie pochodzi od klienta. Jeśli wartość jest równa false, żądanie jest chybień w krawędzi (podrzędny) i jest odrzucane z osłony pochodzenia (element nadrzędny). 
| HttpStatusCode        | Kod stanu HTTP zwrócony z serwera proxy.                                                                                                                                                        |
| HttpStatusDetails     | Stan wynikający z żądania. Znaczenie tej wartości ciągu można znaleźć w tabeli odwołania do stanu.                                                                                              |
| Skakując                   | Punkt obecności, który odpowiedział na żądanie użytkownika. Punkty obecności "skróty" są kodami portów lotniczych odpowiednich linii metra.                                                                                   |
| Stan pamięci podręcznej          | Oznacza, czy obiekt został zwrócony z pamięci podręcznej lub pochodzi z źródła.                                                                                                             |
> [!NOTE]
> Dzienniki można wyświetlać w profilu Log Analytics, uruchamiając zapytanie. Przykładowe zapytanie będzie wyglądać następująco:
    ```
    AzureDiagnostics | where Category == "AzureCdnAccessLog"
    ```

### <a name="sent-to-origin-shield-deprecation"></a>Wysłane do przestarzałej osłony pochodzenia
Właściwość nieprzetworzonego dziennika **isSentToOriginShield** jest przestarzała i zastąpiona przez nowe pole **isReceivedFromClient**. Użyj nowego pola, jeśli używasz już przestarzałego pola. 

Dzienniki pierwotne obejmują dzienniki wygenerowane z obu krawędzi sieci CDN (podręczny) i osłony pochodzenia. Tarcza pierwotna odnosi się do węzłów nadrzędnych, które są strategicznie zlokalizowane na całym świecie. Te węzły komunikują się z serwerami pochodzenia i zmniejszają obciążenie ruchu podczas pochodzenia. 

Dla każdego żądania, które przechodzi do osłony pochodzenia, istnieją wpisy z 2 dziennikami:

* Jeden dla węzłów brzegowych
* Jeden dla osłony pochodzenia. 

Aby odróżnić ruch wychodzący lub odpowiedzi między węzłami krawędzi a osłoną pierwotną, możesz użyć pola **isReceivedFromClient** , aby uzyskać poprawne dane. 

Jeśli wartość jest równa false, oznacza to, że żądanie jest odpowiedzi z osłony pochodzenia do węzłów brzegowych. Takie podejście obowiązuje do porównania nieprzetworzonych dzienników z danymi dotyczącymi rozliczeń. Opłaty nie są naliczane dla ruchu wychodzącego z osłony pochodzenia do węzłów brzegowych. Opłaty są naliczane za ruch wychodzący z węzłów krawędzi do klientów. 

**Przykład zapytania Kusto do wykluczenia dzienników generowanych w ramach osłony pochodzenia w Log Analytics.**

```kusto
AzureDiagnostics 
| where OperationName == "Microsoft.Cdn/Profiles/AccessLog/Write" and Category == "AzureCdnAccessLog"  
| where isReceivedFromClient == true

```

> [!IMPORTANT]
> Funkcja dzienników RAW protokołu HTTP jest dostępna automatycznie dla wszystkich profilów utworzonych lub zaktualizowanych po **25 lutego 2020**. W przypadku utworzonych wcześniej profilów usługi CDN należy zaktualizować punkt końcowy usługi CDN po skonfigurowaniu rejestrowania. Na przykład jeden może przechodzić do filtrowania geograficznego w punktach końcowych usługi CDN i blokować każdy kraj/region, który nie ma znaczenia dla obciążenia i trafiać.


## <a name="metrics"></a>Metryki
Azure CDN firmy Microsoft jest zintegrowany z Azure Monitor i publikuje cztery metryki usługi CDN w celu ułatwienia śledzenia, rozwiązywania problemów i debugowania. 

Metryki są wyświetlane na wykresach i dostępne za pośrednictwem programu PowerShell, interfejsu wiersza polecenia i interfejsu API. Metryki usługi CDN są bezpłatne.

Azure CDN od miar firmy Microsoft i wysyła je do 60 sekund. Metryki mogą pojawić się w portalu dopiero po 3 minutach. 

Aby uzyskać więcej informacji, zobacz [Azure monitor metryki](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

**Metryki obsługiwane przez Azure CDN od firmy Microsoft**

| Metryki         | Opis                                                                                                      | Wymiar                                                                                   |
|-----------------|------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------|
| Współczynnik trafień bajtów * | Wartość procentowa danych wyjściowych z pamięci podręcznej usługi CDN obliczona względem łącznego ruchu wychodzącego.                                      | Punkt końcowy                                                                                    |
| RequestCount    | Liczba żądań klientów obsłużonych przez usługę CDN.                                                                     | Punkt końcowy </br> Kraj klienta. </br> Region klienta. </br> Stan HTTP. </br> Grupa stanu HTTP. |
| ResponseSize    | Liczba bajtów wysłanych jako odpowiedzi z usługi CDN Edge do klientów.                                                  |Punkt końcowy </br> Kraj klienta. </br> Region klienta. </br> Stan HTTP. </br> Grupa stanu HTTP.                                                                                          |
| TotalLatency    | Łączny czas od żądania klienta odebranego przez usługę CDN **do momentu wysłania ostatniego bajtu odpowiedzi z sieci CDN do klienta**. |Punkt końcowy </br> Kraj klienta. </br> Region klienta. </br> Stan HTTP. </br> Grupa stanu HTTP.                                                                                             |

***Liczba bajtów trafień = (ruch wychodzący z krawędzi od brzegu do punktu początkowego)/egress z krawędzi**

Scenariusze wykluczone ze współczynnika trafień w bajtach:

* Jawnie konfigurujesz Brak pamięci podręcznej za pomocą aparatu reguł lub buforowania ciągu zapytania.
* Należy jawnie skonfigurować dyrektywę kontroli pamięci podręcznej bez magazynu ani prywatnej pamięci podręcznej.

### <a name="metrics-configuration"></a>Konfiguracja metryk

1. Z menu Azure Portal wybierz pozycję **wszystkie zasoby**  >>  **\<your-CDN-profile>** .

2. W obszarze **monitorowanie**wybierz pozycję **metryki**:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-03.png" alt-text="Metryki dla profilu CDN." border="true":::

3. Wybierz pozycję **Dodaj metrykę**, wybierz metrykę do dodania:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-04.png" alt-text="Dodaj i wybierz metrykę dla profilu CDN." border="true":::

4. Wybierz pozycję **Dodaj filtr** , aby dodać filtr:
    
    :::image type="content" source="./media/cdn-raw-logs/raw-logs-05.png" alt-text="Zastosuj filtr do metryki." border="true":::

5. Wybierz pozycję **Zastosuj** podział, aby zobaczyć trend według różnych wymiarów:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-06.png" alt-text="Zastosuj podział do metryki." border="true":::

6. Wybierz pozycję **Nowy wykres** , aby dodać nowy wykres:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-07.png" alt-text="Dodaj nowy wykres do widoku metryki." border="true":::

### <a name="alerts"></a>Alerty

Alerty można skonfigurować w usłudze Microsoft CDN, wybierając pozycję **monitorowanie**  >>  **alertów**.

Wybierz **nową regułę alertu** dla metryk wymienionych w sekcji metryki:

:::image type="content" source="./media/cdn-raw-logs/raw-logs-08.png" alt-text="Skonfiguruj alerty dla punktu końcowego usługi CDN." border="true":::

Opłata zostanie naliczona na podstawie Azure Monitor. Aby uzyskać więcej informacji na temat alertów, zobacz [Azure monitor alertów](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-overview).

### <a name="additional-metrics"></a>Dodatkowe metryki
Dodatkowe metryki można włączyć za pomocą usługi Azure Log Analytics i dzienników pierwotnych w celu uzyskania dodatkowego kosztu.

1. Wykonaj kroki opisane powyżej w temacie Włączanie diagnostyki, aby wysłać nieprzetworzony dziennik do usługi log Analytics.

2. Wybierz utworzony obszar roboczy Log Analytics:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-09.png" alt-text="Wybieranie obszaru roboczego usługi log Analytics" border="true":::   

3. Wybierz pozycję **dzienniki** w obszarze **Ogólne** w obszarze roboczym usługi log Analytics.  Następnie wybierz **pozycję Wprowadzenie:**

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-10.png" alt-text="Obszar roboczy zasobów usługi log Analytics." border="true":::   
 
4. Wybierz pozycję **Profile sieci CDN**.  Wybierz przykładowe zapytanie do uruchomienia lub Zamknij Przykładowy ekran, aby wprowadzić zapytanie niestandardowe:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-11.png" alt-text="Przykładowy ekran zapytania." border="true":::   

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-12.png" alt-text="Wykonywanie zapytania." border="true":::   

4. Aby wyświetlić dane według wykresu, wybierz pozycję **Wykres**.  Wybierz pozycję **Przypnij do pulpitu nawigacyjnego** , aby przypiąć wykres do pulpitu nawigacyjnego platformy Azure:

    :::image type="content" source="./media/cdn-raw-logs/raw-logs-13.png" alt-text="Przypnij wykres do pulpitu nawigacyjnego." border="true"::: 

## <a name="next-steps"></a>Następne kroki
W tym artykule włączono dzienniki RAW protokołu HTTP dla usługi Microsoft CDN.

Aby uzyskać więcej informacji na temat Azure CDN i innych usług platformy Azure wymienionych w tym artykule, zobacz:

* [Analizuj](cdn-log-analysis.md) Azure CDN wzorców użytkowania.

* Dowiedz się więcej o [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview).

* Skonfiguruj [log Analytics w Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).
