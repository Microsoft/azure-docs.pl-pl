---
title: Azure Analysis Services skalowanie w | Microsoft Docs
description: Replikowanie Azure Analysis Services serwerów przy użyciu skalowania w zewnątrz. Zapytania klientów mogą być następnie dystrybuowane między wieloma replikami zapytań w puli zapytań skalowania w celu skalowania w zewnątrz.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a2385cb811e322bd44daefa03d821b2ae47e0652
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769269"
---
# <a name="azure-analysis-services-scale-out"></a>Skalowanie w poziomie usług Azure Analysis Services

Dzięki skalowania w poziomie zapytania klientów  mogą być dystrybuowane między wieloma replikami zapytań w puli *zapytań,* co skraca czas odpowiedzi podczas dużych obciążeń zapytaniami. Można również oddzielić przetwarzanie od puli zapytań, dzięki czemu operacje przetwarzania nie będą niekorzystnie wpływać na zapytania klientów. Skalowanie w zewnątrz można skonfigurować w Azure Portal lub przy użyciu Analysis Services API REST.

Skalowanie w poziomie jest dostępne dla serwerów w warstwie cenowej Standardowa. Każda replika zapytania jest rozliczana za tę samą stawkę co serwer. Wszystkie repliki zapytań są tworzone w tym samym regionie co serwer. Liczba replik zapytań, które można skonfigurować, jest ograniczona przez region, w którym znajduje się serwer. Aby dowiedzieć się więcej, zobacz [Dostępność według regionów.](analysis-services-overview.md#availability-by-region) Zwiększanie skali w zewnątrz nie zwiększa ilości dostępnej pamięci dla serwera. Aby zwiększyć ilość pamięci, należy uaktualnić plan. 

## <a name="why-scale-out"></a>Dlaczego warto skalować w celu skalowania w zewnątrz?

W typowym wdrożeniu serwera jeden serwer służy zarówno jako serwer przetwarzania, jak i serwer zapytań. Jeśli liczba zapytań klientów dotyczących modeli na serwerze przekracza liczbę jednostek przetwarzania zapytań (QPU) dla planu serwera lub przetwarzanie modelu odbywa się w tym samym czasie co duże obciążenia zapytań, wydajność może się zmniejszyć. 

Za pomocą skalowania w górę można utworzyć pulę zapytań z maksymalnie siedmiu dodatkowymi zasobami repliki zapytań (łącznie osiem, łącznie z *serwerem* podstawowym). Liczbę replik w puli zapytań można skalować w celu spełnienia wymagań QPU w krytycznym czasie. W dowolnym momencie można oddzielić serwer przetwarzania od puli zapytań. 

Niezależnie od liczby replik zapytań w puli zapytań obciążenia przetwarzania nie są dystrybuowane między replikami zapytań. Serwer podstawowy służy jako serwer przetwarzania. Repliki zapytań obsługują tylko zapytania względem baz danych modelu synchronizowanych między serwerem podstawowym i każdą repliką w puli zapytań. 

W przypadku skalowania w górę przyrostowe dodanie nowych replik zapytań do puli zapytań może potrwać do pięciu minut. Gdy wszystkie nowe repliki zapytań są uruchomione, nowe połączenia klienckie są równoważyć obciążenie zasobów w puli zapytań. Istniejące połączenia klienta nie są zmieniane z zasobu, z który są obecnie połączone. Podczas skalowania do in, wszystkie istniejące połączenia klienta z zasobem puli zapytań, który jest usuwany z puli zapytań, są przerywane. Klienci mogą ponownie połączyć się z pozostałym zasobem puli zapytań.

## <a name="how-it-works"></a>Jak to działa

Podczas konfigurowania skalowania w zewnątrz po raz pierwszy  modelowe bazy danych na serwerze podstawowym są automatycznie synchronizowane z nowymi replikami w nowej puli zapytań. Automatyczna synchronizacja odbywa się tylko raz. Podczas automatycznej synchronizacji pliki danych serwera podstawowego (zaszyfrowane w magazynie obiektów blob) są kopiowane do drugiej lokalizacji, również szyfrowane w magazynie obiektów blob. Repliki w puli zapytań są następnie *wytłaszowane* przy użyciu danych z drugiego zestawu plików. 

Automatyczna synchronizacja jest wykonywana tylko podczas skalowania serwera w celu skalowania w zewnątrz po raz pierwszy, ale można również przeprowadzić synchronizację ręczną. Synchronizacja zapewnia, że dane replik w puli zapytań są zgodne z danymi serwera podstawowego. Podczas przetwarzania (odświeżania) modeli na serwerze  podstawowym po zakończeniu operacji przetwarzania należy przeprowadzić synchronizację. Ta synchronizacja kopiuje zaktualizowane dane z plików serwera podstawowego w magazynie obiektów blob do drugiego zestawu plików. Repliki w puli zapytań są następnie replikowane przy użyciu zaktualizowanych danych z drugiego zestawu plików w magazynie obiektów blob. 

Podczas wykonywania kolejnej operacji skalowania w zewnątrz, na przykład zwiększania liczby replik w puli zapytań z dwóch do pięciu, nowe repliki są mierowane przy użyciu danych z drugiego zestawu plików w magazynie obiektów blob. Nie ma synchronizacji. Jeśli następnie wykonasz synchronizację po skalowaniu na zewnątrz, nowe repliki w puli zapytań zostaną dwukrotnie przesłoone — nadmiarowe przesłonienie. Podczas wykonywania kolejnej operacji skalowania w celu skalowania w zewnątrz należy pamiętać o:

* Wykonaj synchronizację *przed operacją skalowania w celu* uniknięcia nadmiarowego nawodnienia dodanych replik. Równoczesne operacje synchronizacji i skalowania w poziomie uruchomione w tym samym czasie są niedozwolone.

* W przypadku automatyzacji operacji przetwarzania i skalowania w zewnątrz ważne jest, aby najpierw przetworzyć dane na serwerze podstawowym, a następnie przeprowadzić synchronizację, a następnie wykonać operację skalowania w zewnątrz.  Ta sekwencja zapewnia minimalny wpływ na liczbę elementów QPU i zasobów pamięci.

* Podczas operacji skalowania w zewnątrz wszystkie serwery w puli zapytań, w tym serwer podstawowy, są tymczasowo w trybie offline.

* Synchronizacja jest dozwolona nawet wtedy, gdy w puli zapytań nie ma żadnych replik. W przypadku skalowania w poziomie z zera do co najmniej jednej repliki z nowymi danymi z operacji przetwarzania na serwerze podstawowym należy najpierw przeprowadzić synchronizację bez replik w puli zapytań, a następnie skalować w poziomie. Synchronizacja przed skalowaniem na zewnątrz pozwala uniknąć nadmiarowego nawodnienia nowo dodanych replik.

* Podczas usuwania modelowej bazy danych z serwera podstawowego nie jest ona automatycznie usuwana z replik w puli zapytań. Operację synchronizacji należy wykonać przy użyciu polecenia programu PowerShell [Sync-AzAnalysisServicesInstance,](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) które usuwa pliki dla tej bazy danych z lokalizacji magazynu współdzielonych obiektów blob repliki, a następnie usuwa modelową bazę danych z replik w puli zapytań. Aby ustalić, czy modelowa baza danych istnieje na replikach w  puli zapytań, ale nie na serwerze podstawowym, upewnij się, że ustawienie Oddziel serwer przetwarzania od puli zapytań ma ustawienie **Tak.** Następnie użyj programu SSMS, aby nawiązać połączenie z serwerem podstawowym przy użyciu kwalifikatora, `:rw` aby sprawdzić, czy baza danych istnieje. Następnie połącz się z replikami w puli zapytań, łącząc się bez kwalifikatora, aby sprawdzić, czy ta `:rw` sama baza danych również istnieje. Jeśli baza danych istnieje w replikach w puli zapytań, ale nie na serwerze podstawowym, uruchom operację synchronizacji.   

* W przypadku zmiany nazwy bazy danych na serwerze podstawowym istnieje dodatkowy krok niezbędny do zapewnienia prawidłowej synchronizacji bazy danych z dowolnymi replikami. Po zmianie nazwy wykonaj synchronizację przy użyciu polecenia [Sync-AzAnalysisServicesInstance,](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) określając parametr ze starą `-Database` nazwą bazy danych. Ta synchronizacja usuwa bazę danych i pliki o starej nazwie ze wszystkich replik. Następnie wykonaj kolejną synchronizację `-Database` określającą parametr z nową nazwą bazy danych. Druga synchronizacja kopiuje nowo nazwaną bazę danych do drugiego zestawu plików i kopiuje wszystkie repliki. Tych synchronizacji nie można wykonać za pomocą polecenia Synchronizuj model w portalu.

### <a name="synchronization-mode"></a>Tryb synchronizacji

Domyślnie repliki zapytań są ponownie w pełni, a nie przyrostowo. Ponowne nawodnienie odbywa się etapami. Są one odłączane i dołączane po dwie na raz (przy założeniu, że istnieją co najmniej trzy repliki), aby upewnić się, że co najmniej jedna replika jest przechowywana w trybie online dla zapytań w danym momencie. W niektórych przypadkach klienci mogą wymagać ponownego nawiązania połączenia z jedną z replik online w trakcie tego procesu. Za pomocą ustawienia **ReplicaSyncMode** można teraz określić, że synchronizacja repliki zapytań odbywa się równolegle. Synchronizacja równoległa zapewnia następujące korzyści: 

- Znaczne skrócenie czasu synchronizacji. 
- Istnieje większe prawdopodobieństwo, że dane w replikach będą spójne podczas procesu synchronizacji. 
- Ponieważ bazy danych są przechowywane w trybie online we wszystkich replikach w całym procesie synchronizacji, klienci nie muszą ponownie nawiązywać połączenia. 
- Pamięć podręczna w pamięci jest aktualizowana przyrostowo tylko przy użyciu zmienionych danych, co może być szybsze niż pełne ponowne nasypanie modelu. 

#### <a name="setting-replicasyncmode"></a>Ustawianie trybu ReplicaSyncMode

Użyj programu SSMS, aby ustawić tryb ReplicaSyncMode we właściwościach zaawansowanych. Możliwe wartości są następujące: 

- `1` (ustawienie domyślne): pełne ponowne napełnienie bazy danych repliki etapami (przyrostowe). 
- `2`: synchronizacja zoptymalizowana równolegle. 

![Ustawienie RelicaSyncMode](media/analysis-services-scale-out/aas-scale-out-sync-mode.png)

W przypadku **ustawienia wartości ReplicaSyncMode=2**, w zależności od ilości pamięci podręcznej, która ma zostać zaktualizowana, repliki zapytań mogą zużywać dodatkową pamięć. Aby zachować bazę danych w trybie online i być dostępna dla zapytań,  w zależności od tego, jaka część danych uległa zmianie, operacja może wymagać nawet dwukrotnego podwojenie pamięci repliki, ponieważ zarówno stary, jak i nowy segment są przechowywane w pamięci jednocześnie. Węzły repliki mają taką samą alokację pamięci jak węzeł podstawowy, a na węźle podstawowym zwykle znajduje się dodatkowa pamięć na operacje odświeżania, więc może być mało prawdopodobne, że w replikach zabraknie pamięci. Ponadto częstym scenariuszem jest przyrostowa aktualizacja bazy danych w węźle podstawowym, dlatego wymaganie dwukrotności pamięci powinno być nietypowe. Jeśli operacja synchronizacji napotka błąd braku pamięci, ponowi próbę przy użyciu domyślnej techniki (dołączanie/odłączanie po dwa na raz). 

### <a name="separate-processing-from-query-pool"></a>Oddzielanie przetwarzania od puli zapytań

Aby uzyskać maksymalną wydajność operacji przetwarzania i wykonywania zapytań, można oddzielić serwer przetwarzania od puli zapytań. Po rozdzieleniu nowe połączenia klienckie są przypisywane do replik zapytań tylko w puli zapytań. Jeśli przetwarzanie zajmuje tylko krótki czas, można oddzielić serwer przetwarzania od puli zapytań tylko na czas wykonywania operacji przetwarzania i synchronizacji, a następnie dołączyć go z powrotem do puli zapytań. Oddzielenie serwera przetwarzania od puli zapytań lub dodanie go z powrotem do puli zapytań może potrwać do pięciu minut, aż operacja zakończy się.

## <a name="monitor-qpu-usage"></a>Monitorowanie użycia QPU

Aby określić, czy skalowanie serwera w celu skalowania w zewnątrz jest konieczne, [monitoruj](analysis-services-monitor.md) serwer w Azure Portal użyciu metryk. Jeśli funkcja QPU regularnie przekracza limit, oznacza to, że liczba zapytań względem modeli przekracza limit QPU dla planu. Metryka Długość kolejki zadań puli zapytań zwiększa się również, gdy liczba zapytań w kolejce puli wątków zapytań przekracza dostępną wartość QPU. 

Inną dobrą metryką do obserwowania jest średnia wartość QPU według ServerResourceType. Ta metryka porównuje średnią wartość QPU dla serwera podstawowego z pulą zapytań. 

![Metryki skalowania zapytań w celu skalowania w zewnątrz](media/analysis-services-scale-out/aas-scale-out-monitor.png)

**Aby skonfigurować QPU przez ServerResourceType**

1. Na wykresie liniowym Metryki kliknij pozycję **Dodaj metrykę.** 
2. W **obszarze ZASÓB** wybierz serwer, a następnie w obszarze PRZESTRZEŃ NAZW METRYKI wybierz pozycję Analysis Services metryki, a następnie w obszarze METRYKA wybierz pozycję **QPU,** a następnie w obszarze **AGREGACJA** wybierz pozycję **Średnia**.  
3. Kliknij **pozycję Zastosuj podział.** 
4. W **wartości**, wybierz **ServerResourceType**.  

### <a name="detailed-diagnostic-logging"></a>Szczegółowe rejestrowanie diagnostyczne

Użyj Azure Monitor, aby uzyskać bardziej szczegółową diagnostykę skalowanych zasobów serwera. Za pomocą dzienników można użyć zapytań usługi Log Analytics, aby rozłamać QPU i pamięć według serwera i repliki. Aby dowiedzieć się więcej, zobacz przykładowe zapytania [w Analysis Services rejestrowania diagnostycznego](analysis-services-logging.md#example-queries).


## <a name="configure-scale-out"></a>Konfigurowanie zwiększania skali w poziomie

### <a name="in-azure-portal"></a>W Azure Portal

1. W portalu kliknij pozycję **Skaluj w zewnątrz.** Za pomocą suwaka wybierz liczbę serwerów repliki zapytania. Liczba wybieranych replik jest dodatkiem do istniejącego serwera.  

2. W **oddziel serwer przetwarzania od puli zapytań,** wybierz opcję tak, aby wykluczyć serwer przetwarzania z serwerów zapytań. Połączenia [klienckie](#connections) używające domyślnych parametrów połączenia (bez `:rw` ) są przekierowywani do replik w puli zapytań. 

   ![Suwak skalowania w zewnątrz](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Kliknij **przycisk Zapisz,** aby aprowizować nowe serwery repliki zapytań. 

Podczas pierwszego konfigurowania skalowania w celu korzystania z serwera modele na serwerze podstawowym są automatycznie synchronizowane z replikami w puli zapytań. Automatyczna synchronizacja występuje tylko raz podczas pierwszej konfiguracji skalowania w celu co najmniej jednej repliki. Kolejne zmiany liczby replik na tym samym serwerze nie *wyzwalają kolejnej automatycznej synchronizacji.* Automatyczna synchronizacja nie nastąpi ponownie, nawet jeśli ustawiono serwer na zero replik, a następnie ponownie skalowanie w poziomie do dowolnej liczby replik. 

## <a name="synchronize"></a>Synchronizowanie 

Operacje synchronizacji muszą być wykonywane ręcznie lub przy użyciu interfejsu API REST.

### <a name="in-azure-portal"></a>W Azure Portal

Na **stronie** Omówienie > modelu > **zsynchronizować model**.

![Ikona Synchronizuj](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>Interfejs API REST

Użyj operacji **synchronizacji.**

#### <a name="synchronize-a-model"></a>Synchronizowanie modelu   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Uzyskiwanie stanu synchronizacji  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Zwracane kody stanu:


|Kod  |Opis  |
|---------|---------|
|-1     |  Nieprawidłowy       |
|0     | Replikowanie        |
|1     |  Ponowne zasyłanie       |
|2     |   Ukończone       |
|3     |   Niepowodzenie      |
|4     |    Finalizacji     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Przed rozpoczęciem korzystania z programu PowerShell [zainstaluj lub zaktualizuj najnowszą wersję Azure PowerShell module](/powershell/azure/install-az-ps). 

Aby uruchomić synchronizację, użyj [programu Sync-AzAnalysisServicesInstance.](/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance)

Aby ustawić liczbę replik zapytań, użyj [set-AzAnalysisServicesServer.](/powershell/module/az.analysisservices/set-azanalysisservicesserver) Określ opcjonalny `-ReadonlyReplicaCount` parametr .

Aby oddzielić serwer przetwarzania od puli zapytań, użyj [set-AzAnalysisServicesServer.](/powershell/module/az.analysisservices/set-azanalysisservicesserver) Określ opcjonalny `-DefaultConnectionMode` parametr , aby użyć parametru `Readonly` .

Aby dowiedzieć się więcej, zobacz Using a service principal with the Az.AnalysisServices module (Używanie jednostki [usługi z modułem Az.AnalysisServices).](analysis-services-service-principal.md#azmodule)

## <a name="connections"></a>Połączenia

Na stronie Przegląd serwera znajdują się dwie nazwy serwerów. Jeśli skalowanie serwera nie zostało jeszcze skonfigurowane, obie nazwy serwerów działają tak samo. Po skonfigurowaniu skalowania serwera w celu skalowania na zewnątrz należy określić odpowiednią nazwę serwera w zależności od typu połączenia. 

W przypadku połączeń klienckich użytkownika końcowego, takich Power BI Desktop, Excel i aplikacje niestandardowe, użyj **nazwy serwera**. 

W przypadku programu SSMS, Visual Studio i parametry połączenia w programie PowerShell, aplikacjach funkcji platformy Azure i aplikacji AMO użyj **nazwy serwera zarządzania**. Nazwa serwera zarządzania zawiera specjalny kwalifikator `:rw` (do odczytu i zapisu). Wszystkie operacje przetwarzania są wykonywane na (podstawowym) serwerze zarządzania.

![Nazwy serwerów](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="scale-up-scale-down-vs-scale-out"></a>Skalowanie w górę, skalowanie w dół a skalowanie w dół

Warstwę cenową można zmienić na serwerze z wieloma replikami. Ta sama warstwa cenowa ma zastosowanie do wszystkich replik. Operacja skalowania najpierw spowoduje jednocześnie wszystkie repliki, a następnie wszystkie repliki w nowej warstwie cenowej.

## <a name="troubleshoot"></a>Rozwiązywanie problemów

**Problem:** Użytkownicy zobaczą **błąd Nie można odnaleźć wystąpienia serwera " w \<Name of the server> trybie połączenia "ReadOnly".**

**Rozwiązanie:** Po wybraniu opcji **Oddziel** serwer przetwarzania od puli zapytań połączenia klienckie używające domyślnych parametrów połączenia (bez ) są przekierowywani do `:rw` replik puli zapytań. Jeśli repliki w puli zapytań nie są jeszcze w trybie online, ponieważ synchronizacja nie została jeszcze ukończona, przekierowywany połączenia klienta mogą zakończyć się niepowodzeniem. Aby zapobiec nieudanym połączeniem, podczas wykonywania synchronizacji w puli zapytań muszą znajdować się co najmniej dwa serwery. Każdy serwer jest synchronizowany indywidualnie, podczas gdy inne pozostają w trybie online. Jeśli zdecydujesz się nie mieć serwera przetwarzania w puli zapytań podczas przetwarzania, możesz usunąć go z puli w celu przetwarzania, a następnie dodać go z powrotem do puli po zakończeniu przetwarzania, ale przed synchronizacją. Monitorowanie stanu synchronizacji za pomocą metryk pamięci i QPU.



## <a name="related-information"></a>Informacje pokrewne

[Monitorowanie metryk serwera](analysis-services-monitor.md)   
[Zarządzanie Azure Analysis Services](analysis-services-manage.md)
