---
title: Monitorowanie usługi Azure łańcucha bloków (ABS)
description: Monitorowanie usługi Azure łańcucha bloków za pomocą Azure Monitor
ms.date: 01/08/2020
ms.topic: how-to
ms.reviewer: v-umha
ms.openlocfilehash: 05147f48c4cde4cc97bf6cc9cae5c8220a389ebd
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100594928"
---
# <a name="monitor-azure-blockchain-service-through-azure-monitor"></a>Monitoruj usługę Azure łańcucha bloków za pomocą Azure Monitor  

Klienci, którzy uruchamiają scenariusze łańcucha bloków klasy produkcyjnej w usłudze Azure łańcucha bloków Service (ABS), mają krytyczne znaczenie dla monitorowania zasobów pod kątem dostępności, wydajności i operacji. W tym artykule opisano dane monitorowania wygenerowane przez usługę Azure łańcucha bloków oraz sposób użycia różnych funkcji i integracji Azure Monitor do analizowania i wyzwalania alertów w programie w celu zarządzania środowiskami klasy produkcyjnej.  

## <a name="what-is-azure-monitor"></a>Co to jest Azure Monitor?

Usługa Azure łańcucha bloków tworzy dane monitorowania za pomocą Azure Monitor, który jest pełną usługą monitorowania stosu na platformie Azure, która oferuje pełny zestaw funkcji do monitorowania zasobów platformy Azure. Aby uzyskać więcej informacji na temat Azure Monitor, zobacz [monitorowanie zasobów platformy Azure przy użyciu Azure monitor](../../azure-monitor/essentials/monitor-azure-resource.md).
 

Poniższe sekcje dotyczą tego artykułu, opisując szczegółowe dane zebrane z usługi Azure łańcucha bloków Service i dostarczając przykłady konfigurowania zbierania danych i analizowania tych danych przy użyciu narzędzi platformy Azure.

## <a name="monitor-data-collected-from-azure-blockchain-service"></a>Monitoruj dane zebrane z usługi Azure łańcucha bloków Service  

Usługa Azure łańcucha bloków gromadzi dane monitorowania tego samego rodzaju jak inne zasoby platformy Azure, które są opisane w temacie [monitorowanie danych](../../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data) z zasobów platformy Azure. Aby uzyskać szczegółowe informacje o dziennikach i metrykach utworzonych przez usługę Azure łańcucha bloków, zobacz artykuł [monitorowanie usługi Azure łańcucha bloków Data Reference](#monitor-azure-blockchain-service-data-reference) .

Strona przegląd w Azure Portal dla każdego zasobu elementu członkowskiego usługi Azure łańcucha bloków zawiera krótki widok transakcji, w tym żądania obsługiwane i przetworzone bloki. Niektóre z tych danych są zbierane automatycznie i dostępne do analizy po utworzeniu zasobu elementu członkowskiego usługi Azure łańcucha bloków, podczas gdy można włączyć dodatkowe zbieranie danych z dodatkową konfiguracją.

## <a name="diagnostic-settings"></a>Ustawienia diagnostyczne  

Metryki platformy i dziennik aktywności są zbierane automatycznie, ale należy utworzyć ustawienie diagnostyczne, aby zbierać dzienniki zasobów lub przesyłać je dalej poza Azure Monitor. Zobacz [Tworzenie ustawień diagnostycznych, aby zbierać dzienniki platformy i metryki na platformie Azure](../../azure-monitor/essentials/diagnostic-settings.md) w celu uzyskania szczegółowego procesu tworzenia ustawień diagnostycznych przy użyciu Azure Portal, interfejsu wiersza polecenia lub programu PowerShell.

Podczas tworzenia ustawienia diagnostycznego należy określić, które kategorie dzienników mają być zbierane. Poniżej wymieniono kategorie usługi Azure łańcucha bloków Service.

**Łańcucha bloków dzienniki proxy** — wybierz kategorię, jeśli chcesz monitorować dzienniki proxy NGNIX. Wszystkie szczegóły transakcji klienta są dostępne do celów inspekcji i debugowania.  

**Dzienniki aplikacji łańcucha bloków** — wybierz kategorię, aby pobrać dzienniki aplikacji łańcucha bloków hostowanej przez usługę zarządzaną. Na przykład dla elementu członkowskiego ABS-Quorum te dzienniki będą dziennikami ze kworum.  

**Żądania metryk**: wybierz opcję zbierania danych metryk z Azure Cosmos DB do miejsc docelowych w ustawieniu diagnostycznym, które są zbierane automatycznie w ramach metryk platformy Azure. Zbieraj dane metryk z dziennikami zasobów, aby analizować jednocześnie oba rodzaje danych i wysyłać dane metryk poza Azure Monitor.

## <a name="analyze-metric-data"></a>Analizowanie danych metryki  

Metryki dla usługi Azure łańcucha bloków Service można analizować za pomocą Eksploratora metryk, przejdź do karty metryki w obszarze monitorowanie w bloku zasoby ABS. Zobacz Rozpoczynanie [pracy z usługą Azure Eksplorator metryk](../../azure-monitor/essentials/metrics-getting-started.md) , aby uzyskać szczegółowe informacje na temat korzystania z narzędzia. Kompletne metryki dla usługi Azure łańcucha bloków są wymienione w obszarze nazw metryki usługi Azure łańcucha bloków Service w warstwie Standardowa.

Wymiaru **węzła** można użyć podczas dodawania filtru lub dzielenia metryk, co zasadniczo zapewnia wartości metryk dla węzłów transakcji i węzłów modułu sprawdzania poprawności elementu członkowskiego ABS.

## <a name="analyze-log-data"></a>Analizowanie danych dzienników

Poniżej przedstawiono niektóre zapytania, które można wprowadzić na pasku przeszukiwania dzienników, aby ułatwić monitorowanie członków usługi Azure łańcucha bloków. Te zapytania działają w [nowym języku](../../azure-monitor/logs/log-query-overview.md).

Aby zbadać warunki błędu w dziennikach aplikacji łańcucha bloków, użyj poniższego zapytania:

```
BlockchainApplicationLog | where BlockchainMessage contains "ERROR" or BlockchainMessage contains "fatal"

```

Aby zbadać warunki błędu w dziennikach serwera proxy łańcucha bloków, użyj poniższego zapytania  


```
BlockchainProxyLog
| filter Code != 200
| limit 500

```
Można użyć filtrów czasu dostępnych w dziennikach platformy Azure do filtrowania zapytania dla określonego przedziału czasu.

## <a name="monitor-azure-blockchain-service-data-reference"></a>Monitoruj odwołanie do danych usługi Azure łańcucha bloków Service  

Ten artykuł zawiera informacje dotyczące danych dzienników i metryk zbieranych w celu przeanalizowania wydajności i dostępności usługi Azure łańcucha bloków.  

### <a name="resource-logs"></a>Dzienniki zasobów

Wszystkie dzienniki zasobów udostępniają wspólny schemat najwyższego poziomu z kilkoma unikatowymi właściwościami specyficznymi dla usługi łańcucha bloków. Można zapoznać się z artykułem [schemat dzienników zasobów najwyższego poziomu](../../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema), szczegółowe informacje o określonych właściwościach usługi Azure łańcucha bloków.  

W poniższej tabeli wymieniono właściwości dzienników serwera proxy usługi Azure łańcucha bloków, które są zbierane w dziennikach Azure Monitor lub usłudze Azure Storage.  


| Nazwa właściwości  | Opis |
|:---|:---|
| time | Data i godzina (UTC), gdy wystąpiła operacja. |
| Identyfikator  | Zasób usługi Azure łańcucha bloków, dla którego są włączone dzienniki.  |
| category  |W przypadku usługi Azure łańcucha bloków dostępne wartości to **Proxylogs** i **Applicationlogs**. |
| operationName  | Nazwa operacji reprezentowanej przez to zdarzenie.   |
| Poziom dziennika  | Domyślnie usługa Azure łańcucha bloków włącza poziom dziennika **informacyjnego** .   |
| NodeLocation  | Region świadczenia usługi Azure, w którym jest wdrażany element członkowski łańcucha bloków.  |
| BlockchainNodeName  | Nazwa węzła elementu członkowskiego usługi Azure łańcucha bloków, w którym jest wykonywana operacja.   |
| EthMethod  | Metoda, która jest wywoływana przez podstawowy protokół łańcucha bloków w kworum, może być eth_sendTransactions, eth_getBlockByNumber itd.  |
| Agent  | Agent użytkownika działający w imieniu użytkownika, taki jak przeglądarka sieci Web Mozilla, Edge itd. Przykłady wartości to: "Mozilla/5.0 (linux x64) node.js/8.16.0 V8/6.2.414.77"  |
| Kod   | Kody błędów HTTP. Zazwyczaj 4XX i 5XX są warunkami błędów.  |
| NodeHost  | Nazwa DNS węzła.   |
| RequestMethodName | Wywoływana metoda HTTP, możliwe wartości w tym miejscu są UMIESZCZAne w celu utworzenia elementu członkowskiego, pobieranie do pobrania szczegółów istniejącego elementu członkowskiego, usuwanie elementu członkowskiego usuwania, poprawka do aktualizacji elementu członkowskiego.   |
| BlockchainMemberName  | Nazwa członka usługi łańcucha bloków platformy Azure podana przez użytkownika.  |
| Konsorcjum | Nazwa konsorcjum podana przez użytkownika.   |
| Zdalne  | Adres IP klienta, z którego pochodzi żądanie.  |
| RequestSize  | Rozmiar żądania w bajtach.  |
| RequestTime  | Czas trwania żądania (w milisekundach).|




W poniższej tabeli wymieniono właściwości dzienników aplikacji Azure łańcucha bloków.


| Nazwa właściwości  | Opis |
|:---|:---|
| time | Data i godzina (UTC), gdy wystąpiła operacja. |
| Identyfikator  | Zasób usługi Azure łańcucha bloków, dla którego są włączone dzienniki.|
| category  |W przypadku usługi Azure łańcucha bloków wartość możliwe jest **Proxylogs** i **Applicationlogs**.  |
| operationName  | Nazwa operacji reprezentowanej przez to zdarzenie.   |
| Poziom dziennika  | Domyślnie usługa Azure łańcucha bloków włącza poziom dziennika **informacyjnego** .   |
| NodeLocation  | Region świadczenia usługi Azure, w którym jest wdrażany element członkowski łańcucha bloków.  |
| BlockchainNodeName  | Nazwa węzła elementu członkowskiego usługi Azure łańcucha bloków, w którym jest wykonywana operacja.   |
| BlockchainMessage    | To pole będzie zawierać dziennik aplikacji łańcucha bloków, który jest dzienników zwykłych danych. W przypadku usługi ABS-kworum będą istnieć dzienniki kworum. Zawiera informacje o rodzaju wpisu dziennika, który ma charakter informacyjny, błąd, ostrzeżenie i ciąg, który zawiera więcej informacji na temat wykonywanej akcji.   |
| TenantID    | Dzierżawa specyficzna dla regionu usługi Azure łańcucha bloków. Format tego pola to https://westlake-rp-prod . <region> . cloudapp.azure.com WHERE region określa region świadczenia usługi Azure wdrożonego elementu członkowskiego.       |
| SourceSystem   | System wypełnia dzienniki, w tym przypadku jest to **platforma Azure**.    |



### <a name="metrics"></a>Metryki

W poniższych tabelach przedstawiono metryki platformy zebrane dla usługi Azure łańcucha bloków Service. Wszystkie metryki są przechowywane w obszarze nazw metryki **usługi Azure łańcucha bloków Service** w warstwie Standardowa.

Listę wszystkich Azure Monitor obsługiwanych metryk (w tym usługi Azure łańcucha bloków Service) można znaleźć w temacie [Azure monitor Supported Metrics](../../azure-monitor/essentials/metrics-supported.md).

### <a name="blockchain-metrics"></a>Metryki łańcucha bloków

Poniższa tabela zawiera listę metryk łańcucha bloków, które są zbierane dla zasobu elementu członkowskiego usługi Azure łańcucha bloków.


| Nazwa metryki | Jednostka  |  Typ agregacji| Opis   |
|---|---|---|---|
| Oczekujące transakcje   | Liczba  |  Średnia | Liczba transakcji, które oczekują na analizowane pod.   |
| Przetworzone bloki   | Liczba  | Sum  |  Liczba bloków przetworzonych w każdym przedziale czasu. Obecnie rozmiar bloku wynosi 5 sekund, dlatego w minucie każdy węzeł będzie przetwarzać 12 bloków i 60 bloków w ciągu 5 minut.   |
|Przetworzone transakcje    | Liczba  | Sum  | Liczba transakcji przetworzonych w bloku.    |
|Transakcje w kolejce    |  Liczba | Średnia  | Liczba transakcji, które nie mogą być natychmiast analizowane pod. Może to być spowodowane faktem, że nie dotarły one do kolejności, a przyszła transakcja oczekuje na nadejście poprzedniej transakcji. Lub może to być dwa transakcje mają tę samą wartość tylko raz (jednorazowy) i tą samą wartością gazu, dlatego drugi nie może być analizowane pod.   |

### <a name="connection-metrics"></a>Metryki połączeń  

W poniższej tabeli wymieniono różne metryki połączenia, które są zbierane dla zasobu elementu członkowskiego usługi Azure łańcucha bloków. Są to metryki serwera proxy NGINX.


| Nazwa metryki | Jednostka  |  Typ agregacji| Opis |
|---|---|---|---|
| Zaakceptowane połączenia   | Liczba  |  Sum | Całkowita liczba zaakceptowanych połączeń klientów.   |
| Aktywne połączenia  | Liczba  | Średnia  |  Bieżąca liczba aktywnych połączeń klienta, w tym połączeń oczekujących.    |
|Obsłużone połączenia    | Liczba  | Sum  | Całkowita liczba obsłużonych połączeń. Ogólnie rzecz biorąc, wartość parametru jest taka sama jak w przypadku akceptowanych połączeń, o ile nie osiągnięto limitów zasobów.     |
|Obsłużone żądania     |  Liczba | Sum  | Całkowita liczba żądań klientów.  |


### <a name="performance-metrics"></a>Metryki wydajności

W poniższej tabeli przedstawiono metryki wydajności, które są zbierane dla każdego węzła zasobu elementu członkowskiego usługi Azure łańcucha bloków.  


| Nazwa metryki | Jednostka  |  Typ agregacji| Opis   |
|---|---|---|---|
| Procent użycia procesora CPU   | Procent  |  Maks. | Procent użycia procesora CPU.     |
| Bajty odczytu we/wy   | Kilobajtach   | Sum  |  Suma bajtów odczytu we wszystkich węzłach zasobu elementu członkowskiego łańcucha bloków.      |
|Bajty zapisu we/wy     | Kilobajtach   | Sum  | Suma operacji we/wy zapisuje bajty we wszystkich węzłach zasobu elementu członkowskiego łańcucha bloków.     |
|Limit pamięci       |  Gigabajtach   | Średnia    | Maksymalna ilość pamięci dostępnej dla procesu łańcucha bloków na węzeł. |
|Użycie pamięci     | Gigabajtach  |  Średnia | Ilość używanej pamięci średnia dla wszystkich węzłów.  |
| Procent użycia pamięci     | Procent   | Średnia  |  Wartość procentowa używanej pamięci (średnia) we wszystkich węzłach.       |
|Użycie magazynu      | Gigabajtach   | Średnia  | Średnia GB miejsca do magazynowania używana we wszystkich węzłach.       |


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [łańcucha bloków Data Manager](./data-manager.md) do przechwytywania i przekształcania danych łańcucha bloków w Azure Event Grid.
