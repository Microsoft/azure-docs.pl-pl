---
title: Jak skonfigurować usługę Azure cache for Redis
description: Zapoznaj się z domyślną konfiguracją Redis dla usługi Azure cache for Redis i Dowiedz się, jak skonfigurować pamięć podręczną platformy Azure dla wystąpień Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: yegu
ms.openlocfilehash: 2ff97dd30d9b993385f52ea531653a89197f8756
ms.sourcegitcommit: f7084d3d80c4bc8e69b9eb05dfd30e8e195994d8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97734627"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Jak skonfigurować usługę Azure cache for Redis
W tym temacie opisano konfiguracje dostępne dla usługi Azure cache dla wystąpień Redis. W tym temacie opisano również domyślną konfigurację serwera Redis dla usługi Azure cache dla wystąpień Redis.

> [!NOTE]
> Aby uzyskać więcej informacji na temat konfigurowania i korzystania z funkcji Premium pamięci podręcznej, zobacz [How to configure trwałości](cache-how-to-premium-persistence.md), [How to configure Clustering](cache-how-to-premium-clustering.md)i [How to configure Virtual Network Support](cache-how-to-premium-vnet.md).
>
>

## <a name="configure-azure-cache-for-redis-settings"></a>Konfigurowanie usługi Azure cache dla ustawień Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Ustawienia pamięci podręcznej platformy Azure dla ustawień Redis są wyświetlane i konfigurowane w bloku **Azure cache for Redis** przy użyciu **menu zasób**.

![Pamięć podręczna platformy Azure dla ustawień Redis](./media/cache-configure/redis-cache-settings.png)

Poniższe ustawienia można wyświetlać i konfigurować za pomocą **menu zasób**.

* [Omówienie](#overview)
* [Dziennik aktywności](#activity-log)
* [Kontrola dostępu (IAM)](#access-control-iam)
* [Tabliczk](#tags)
* [Diagnozowanie i rozwiązywanie problemów](#diagnose-and-solve-problems)
* [Ustawienia](#settings)
    * [Klawisze dostępu](#access-keys)
    * [Ustawienia zaawansowane](#advanced-settings)
    * [Usługa Azure cache for Redis Advisor](#azure-cache-for-redis-advisor)
    * [Skalowanie](#scale)
    * [Rozmiar klastra](#cluster-size)
    * [Trwałość danych](#redis-data-persistence)
    * [Aktualizacje harmonogramu](#schedule-updates)
    * [Replikacja geograficzna](#geo-replication)
    * [Virtual Network](#virtual-network)
    * [Zapora](#firewall)
    * [Właściwości](#properties)
    * [Blokady](#locks)
    * [Skrypt usługi Automation](#automation-script)
* Administracja
    * [Importuj dane](#importexport)
    * [Eksportowanie danych](#importexport)
    * [Ponowne uruchamianie](#reboot)
* [Monitorowanie](#monitoring)
    * [Metryki Redis](#redis-metrics)
    * [Zasady alertów](#alert-rules)
    * [Diagnostyka](#diagnostics)
* Obsługa & ustawień rozwiązywania problemów
    * [Kondycja zasobu](#resource-health)
    * [Nowy wniosek o pomoc techniczną](#new-support-request)


## <a name="overview"></a>Omówienie

**Przegląd** zawiera podstawowe informacje o pamięci podręcznej, takie jak nazwa, porty, warstwa cenowa i wybrane metryki pamięci podręcznej.

### <a name="activity-log"></a>Dziennik aktywności

Kliknij pozycję **Dziennik aktywności** , aby wyświetlić akcje wykonywane w pamięci podręcznej. Możesz również użyć filtru, aby rozwinąć ten widok, aby uwzględnić inne zasoby. Aby uzyskać więcej informacji na temat pracy z dziennikami inspekcji, zobacz [operacje inspekcji przy użyciu Menedżer zasobów](../azure-resource-manager/management/view-activity-logs.md). Aby uzyskać więcej informacji o monitorowaniu pamięci podręcznej platformy Azure dla zdarzeń Redis, zobacz [operacje i alerty](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Kontrola dostępu (IAM)

Sekcja **Kontrola dostępu (IAM)** zapewnia obsługę kontroli dostępu opartej na rolach (Azure RBAC) w Azure Portal. Ta konfiguracja ułatwia organizacjom spełnienie wymagań związanych z zarządzaniem dostępem po prostu i precyzyjnie. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu oparta na rolach na platformie Azure w Azure Portal](../role-based-access-control/role-assignments-portal.md).

### <a name="tags"></a>Tagi

Sekcja **Tagi** pomaga organizować zasoby. Aby uzyskać więcej informacji, zobacz [Porządkowanie zasobów na platformie Azure za pomocą tagów](../azure-resource-manager/management/tag-resources.md).


### <a name="diagnose-and-solve-problems"></a>Diagnozowanie i rozwiązywanie problemów

Kliknij przycisk **Diagnozuj i rozwiąż problemy** , które mają być dostarczone z typowymi problemami i strategiami rozwiązywania tych problemów.



## <a name="settings"></a>Ustawienia
Sekcja **Ustawienia** umożliwia dostęp do i konfigurowanie następujących ustawień pamięci podręcznej.

* [Klawisze dostępu](#access-keys)
* [Ustawienia zaawansowane](#advanced-settings)
* [Usługa Azure cache for Redis Advisor](#azure-cache-for-redis-advisor)
* [Skalowanie](#scale)
* [Rozmiar klastra](#cluster-size)
* [Trwałość danych](#redis-data-persistence)
* [Aktualizacje harmonogramu](#schedule-updates)
* [Replikacja geograficzna](#geo-replication)
* [Virtual Network](#virtual-network)
* [Zapora](#firewall)
* [Właściwości](#properties)
* [Blokady](#locks)
* [Skrypt usługi Automation](#automation-script)



### <a name="access-keys"></a>Klawisze dostępu
Kliknij pozycję **klucze dostępu** , aby wyświetlić lub ponownie wygenerować klucze dostępu do pamięci podręcznej. Te klucze są używane przez klientów nawiązujących połączenie z pamięcią podręczną.

![Pamięć podręczna platformy Azure dla kluczy dostępu Redis](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Ustawienia zaawansowane
Następujące ustawienia są konfigurowane w bloku **Ustawienia zaawansowane** .

* [Porty dostępu](#access-ports)
* [Zasady pamięci](#memory-policies)
* [Powiadomienia o przestrzeni kluczy (Ustawienia zaawansowane)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Porty dostępu
Domyślnie dostęp z użyciem protokołu TLS/SSL jest wyłączony dla nowych pamięci podręcznych. Aby włączyć port inny niż TLS, kliknij przycisk **nie** , aby **zezwolić na dostęp tylko za pośrednictwem protokołu SSL** w bloku **Ustawienia zaawansowane** , a następnie kliknij przycisk **Zapisz**.

> [!NOTE]
> Dostęp TLS do usługi Azure cache for Redis obsługuje obecnie protokoły TLS 1,0, 1,1 i 1,2, ale wersje 1,0 i 1,1 są wycofywane wkrótce.  Aby uzyskać więcej informacji, Przeczytaj stronę dotyczącą [usuwania protokołu TLS 1,0 i 1,1](cache-remove-tls-10-11.md) .

![Pamięć podręczna platformy Azure dla portów dostępu Redis](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Zasady pamięci
W bloku **Ustawienia zaawansowane** **zasady maxmemory**, **zarezerwowane maxmemory** i **maxfragmentationmemory-zastrzeżone** konfiguruje zasady pamięci dla pamięci podręcznej.

![Usługa Azure cache dla zasad Redis maxmemory](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Zasady maxmemory** konfigurują Zasady wykluczania dla pamięci podręcznej i umożliwiają wybór spośród następujących zasad wykluczania:

* `volatile-lru` -Domyślne zasady wykluczania.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Aby uzyskać więcej informacji na temat `maxmemory` zasad, zobacz [Zasady wykluczania](https://redis.io/topics/lru-cache#eviction-policies).

Ustawienie **zarezerwowane maxmemory** konfiguruje ilość pamięci (w MB na wystąpienie w klastrze) zarezerwowaną dla operacji poza pamięcią podręczną, takich jak replikacja podczas pracy w trybie failover. Ustawienie tej wartości pozwala na bardziej spójne środowisko serwera Redis, gdy obciążenie jest różne. Ta wartość powinna być ustawiona na wyższą dla obciążeń, które są bardzo duże. Gdy pamięć jest zarezerwowana dla takich operacji, jest niedostępna w przypadku przechowywania danych w pamięci podręcznej.

Ustawienie **zastrzeżone maxfragmentationmemory** konfiguruje ilość pamięci w MB na wystąpienie w klastrze, zarezerwowaną dla fragmentacji pamięci. Ustawienie tej wartości pozwala na bardziej spójne środowisko serwera Redis, gdy pamięć podręczna jest pełna lub bliska pełnej wartości, a współczynnik fragmentacji jest wysoki. Gdy pamięć jest zarezerwowana dla takich operacji, jest niedostępna w przypadku przechowywania danych w pamięci podręcznej.

Należy wziąć pod uwagę podczas wybierania nowej wartości rezerwacji pamięci (**maxmemory-zastrzeżone** lub **maxfragmentationmemory-zastrzeżone**), ponieważ ta zmiana może mieć wpływ na pamięć podręczną, która jest już uruchomiona z dużymi ilościami danych. Na przykład jeśli masz pamięć podręczną 53 GB z 49 GB danych, a następnie zmień wartość rezerwacji na 8 GB, ta zmiana spowoduje spadek maksymalnej dostępnej pamięci dla systemu do 45 GB. Jeśli bieżące `used_memory` lub Twoje `used_memory_rss` wartości są wyższe niż nowy limit wynoszący 45 GB, system będzie musiał wykluczyć dane do obydwu `used_memory` i `used_memory_rss` poniżej 45 GB. Wykluczenie może zwiększyć obciążenie serwera i fragmentację pamięci. Aby uzyskać więcej informacji na temat metryk pamięci podręcznej, takich jak `used_memory` i `used_memory_rss` , zobacz [Dostępne metryki i interwały raportowania](cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

> [!IMPORTANT]
> Ustawienia **zarezerwowane** maxmemory i maxfragmentationmemory są dostępne tylko dla pamięci podręcznej w **warstwach** standardowa i Premium.
>
>

#### <a name="keyspace-notifications-advanced-settings"></a>Powiadomienia o przestrzeni kluczy (Ustawienia zaawansowane)
Powiadomienia o przestrzeni kluczy Redis są konfigurowane w bloku **Ustawienia zaawansowane** . Powiadomienia o przestrzeni kluczy umożliwiają klientom otrzymywanie powiadomień o wystąpieniu pewnych zdarzeń.

![Ustawienia zaawansowane usługi Azure cache for Redis](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Powiadomienia dotyczące miejsca na dysku i ustawienia **powiadamiania o przestrzeni** kluczy są dostępne tylko dla pamięci podręcznej w warstwach Standardowa i Premium.
>
>

Aby uzyskać więcej informacji, zobacz [Redis — powiadomienia dotyczące miejsca na dysku](https://redis.io/topics/notifications). Aby zapoznać się z przykładowym kodem, zobacz plik [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) w próbce [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) .


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Usługa Azure cache for Redis Advisor
W bloku **Azure cache for Redis Advisor** są wyświetlane zalecenia dotyczące pamięci podręcznej. W trakcie normalnych operacji nie są wyświetlane żadne zalecenia.

![Zrzut ekranu pokazujący, gdzie są wyświetlane zalecenia.](./media/cache-configure/redis-cache-no-recommendations.png)

Jeśli wystąpią jakiekolwiek warunki podczas operacji pamięci podręcznej, takie jak duże użycie pamięci, przepustowość sieci lub obciążenie serwera, w bloku **pamięci podręcznej platformy Azure dla Redis** zostanie wyświetlony alert.

![Zrzut ekranu pokazujący, gdzie są wyświetlane alerty w sekcji Azure cache for Redis.](./media/cache-configure/redis-cache-recommendations-alert.png)

Więcej informacji można znaleźć w bloku **zalecenia** .

![Zalecenia](./media/cache-configure/redis-cache-recommendations.png)

Te metryki można monitorować w sekcjach [wykresy monitorowania](cache-how-to-monitor.md#monitoring-charts) i [wykresy użycia](cache-how-to-monitor.md#usage-charts) w bloku **Azure cache for Redis** .

Każda warstwa cenowa ma różne limity dla połączeń klientów, pamięci i przepustowości. Jeśli pamięć podręczna zbliża się do maksymalnej pojemności dla tych metryk przez dłuższy czas, zostanie utworzone zalecenie. Więcej informacji o metrykach i limitach przeglądanych przez narzędzie **rekomendacje** znajduje się w poniższej tabeli:

| Pamięć podręczna Azure dla metryki Redis | Więcej informacji |
| --- | --- |
| Użycie przepustowości sieci |[Przepustowość pamięci podręcznej dostępna dla wydajności](cache-planning-faq.md#azure-cache-for-redis-performance) |
| Połączeni klienci |[Domyślna konfiguracja serwera Redis — MaxClients](#maxclients) |
| Obciążenie serwera |[Wykresy użycia — obciążenie serwera Redis](cache-how-to-monitor.md#usage-charts) |
| Użycie pamięci |[Wydajność pamięci podręcznej — rozmiar](cache-planning-faq.md#azure-cache-for-redis-performance) |

Aby uaktualnić pamięć podręczną, kliknij pozycję **Uaktualnij teraz** , aby zmienić warstwę cenową i [skalować](#scale) pamięć podręczną. Aby uzyskać więcej informacji na temat wybierania warstwy cenowej, zobacz [Wybieranie odpowiedniej warstwy](cache-overview.md#choosing-the-right-tier) .


### <a name="scale"></a>Skalowanie
Kliknij pozycję **skalowanie** , aby wyświetlić lub zmienić warstwę cenową pamięci podręcznej. Aby uzyskać więcej informacji na temat skalowania, zobacz [Jak skalować pamięć podręczną platformy Azure dla Redis](cache-how-to-scale.md).

![Pamięć podręczna platformy Azure dla warstwy cenowej Redis](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Rozmiar klastra Redis
Kliknij pozycję **rozmiar klastra** , aby zmienić rozmiar klastra dla uruchomionej pamięci podręcznej Premium z włączoną obsługą klastrowania.

![Rozmiar klastra](./media/cache-configure/redis-cache-redis-cluster-size.png)

Aby zmienić rozmiar klastra, użyj suwaka lub wpisz liczbę z zakresu od 1 do 10 w polu tekstowym **Liczba fragmentu** , a następnie kliknij przycisk **OK** , aby zapisać.

> [!IMPORTANT]
> Klastrowanie Redis jest dostępne tylko dla pamięci podręcznych w warstwie Premium. Aby uzyskać więcej informacji, zobacz [jak skonfigurować klastrowanie dla pamięci podręcznej Premium platformy Azure dla Redis](cache-how-to-premium-clustering.md).
>
>


### <a name="redis-data-persistence"></a>Trwałość danych Redis
Kliknij pozycję **trwałość danych** , aby włączyć, wyłączyć lub skonfigurować trwałość danych dla pamięci podręcznej Premium. Usługa Azure cache for Redis oferuje Trwałość Redis przy użyciu trwałości RDB lub trwałości kopia zapasowa AOF.

Aby uzyskać więcej informacji, zobacz [jak skonfigurować trwałość dla pamięci podręcznej Premium platformy Azure dla Redis](cache-how-to-premium-persistence.md).


> [!IMPORTANT]
> Trwałość danych Redis jest dostępna tylko dla pamięci podręcznych w warstwie Premium.
>
>

### <a name="schedule-updates"></a>Aktualizacje harmonogramu
Blok **harmonogram aktualizacji** umożliwia wyznaczenie okna obsługi dla aktualizacji serwera Redis w pamięci podręcznej.

> [!IMPORTANT]
> Okno obsługi ma zastosowanie tylko do aktualizacji serwera Redis, a nie do aktualizacji lub aktualizacji platformy Azure dla systemu operacyjnego maszyn wirtualnych, które obsługują pamięć podręczną.
>
>

![Aktualizacje harmonogramu](./media/cache-configure/redis-schedule-updates.png)

Aby określić okno obsługi, sprawdź wymagane dni i określ czas rozpoczęcia okna obsługi dla każdego dnia, a następnie kliknij przycisk **OK**. Czas okna obsługi jest w formacie UTC.

> [!IMPORTANT]
> Funkcja **Schedule Updates** jest dostępna tylko w przypadku pamięci podręcznych w warstwie Premium. Aby uzyskać więcej informacji i instrukcje, zobacz temat [usługa Azure cache for Redis Administration — Zaplanuj aktualizacje](cache-administration.md#schedule-updates).
>
>

### <a name="geo-replication"></a>Replikacja geograficzna

Blok **replikacji geograficznej** zawiera mechanizm łączenia dwóch pamięci podręcznej platformy Azure w warstwie Premium dla wystąpień Redis. Jedna pamięć podręczna jest wyznaczyna jako podstawowa połączona pamięć podręczna, a druga jako pomocnicza połączona pamięć podręczna. Pomocnicza połączonej pamięci podręcznej jest tylko do odczytu, a dane zapisywane w podstawowej pamięci podręcznej są replikowane do pomocniczej połączonej pamięci podręcznej. Tej funkcji można użyć do replikowania pamięci podręcznej w regionach platformy Azure.

> [!IMPORTANT]
> **Replikacja geograficzna** jest dostępna tylko w przypadku pamięci podręcznych warstwy Premium. Aby uzyskać więcej informacji i instrukcje, zobacz [jak skonfigurować replikację geograficzną dla usługi Azure cache for Redis](cache-how-to-geo-replication.md).
>
>

### <a name="virtual-network"></a>Virtual Network
Sekcja **Virtual Network** umożliwia skonfigurowanie ustawień sieci wirtualnej dla pamięci podręcznej. Aby uzyskać informacje na temat tworzenia pamięci podręcznej Premium z obsługą sieci wirtualnej i aktualizowania jej ustawień, zobacz [jak skonfigurować obsługę usługi Virtual Network w przypadku pamięci podręcznej systemu Azure w warstwie Premium dla Redis](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> Ustawienia sieci wirtualnej są dostępne tylko dla pamięci podręcznych w warstwie Premium, które zostały skonfigurowane za pomocą obsługi sieci wirtualnej podczas tworzenia pamięci podręcznej.
>
>

### <a name="firewall"></a>Firewall

Konfiguracja reguł zapory jest dostępna dla wszystkich warstw usługi Azure cache for Redis.

Kliknij pozycję **Zapora** , aby wyświetlić i skonfigurować reguły zapory dla pamięci podręcznej.

![Firewall](./media/cache-configure/redis-firewall-rules.png)

Można określić reguły zapory z zakresem adresów IP początkowy i końcowy. Po skonfigurowaniu reguł zapory tylko połączenia klienckie z określonych zakresów adresów IP mogą łączyć się z pamięcią podręczną. Po zapisaniu reguły zapory występuje krótkie opóźnienie, zanim reguła zacznie obowiązywać. To opóźnienie jest zazwyczaj mniejsze niż jedna minuta.

> [!IMPORTANT]
> Połączenia z usługi Azure cache dla systemów monitorowania Redis są zawsze dozwolone, nawet jeśli są skonfigurowane reguły zapory.
>
>

### <a name="properties"></a>Właściwości
Kliknij pozycję **Właściwości** , aby wyświetlić informacje o pamięci podręcznej, w tym punkt końcowy pamięci podręcznej i porty.

![Pamięć podręczna platformy Azure dla właściwości Redis](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Blokady
Sekcja **blokady** umożliwia zablokowanie subskrypcji, grupy zasobów lub zasobu, aby zapobiec przypadkowemu usunięciu lub zmodyfikowaniu zasobów krytycznych przez innych użytkowników w organizacji. Aby uzyskać więcej informacji, zobacz [Lock resources with Azure Resource Manager](../azure-resource-manager/management/lock-resources.md) (Blokowanie zasobów w usłudze Azure Resource Manager).

### <a name="automation-script"></a>Skrypt usługi Automation

Kliknij pozycję **skrypt automatyzacji** , aby skompilować i wyeksportować szablon wdrożonych zasobów dla przyszłych wdrożeń. Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [wdrażanie zasobów za pomocą szablonów Azure Resource Manager](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="administration-settings"></a>Ustawienia administracyjne
Ustawienia w sekcji **Administracja** umożliwiają wykonywanie następujących zadań administracyjnych dla pamięci podręcznej.

![Administracja](./media/cache-configure/redis-cache-administration.png)

* [Importuj dane](#importexport)
* [Eksportowanie danych](#importexport)
* [Ponowne uruchamianie](#reboot)


### <a name="importexport"></a>Import/Export
Import/Export to pamięć podręczna platformy Azure do Redis operacji zarządzania danymi, która umożliwia importowanie i eksportowanie danych w pamięci podręcznej przez zaimportowanie i wyeksportowanie bazy danych Azure cache for Redis Database (RDB) z pamięci podręcznej Premium do strony obiektu BLOB na koncie usługi Azure Storage. Usługa Import/Export umożliwia Migrowanie między różnymi pamięciami podręcznymi platformy Azure dla wystąpień Redis lub wypełnianie pamięci podręcznej danymi przed użyciem.

Import może służyć do przenoszenia zgodnych plików RDB Redis z dowolnego serwera Redis działającego w dowolnej chmurze lub środowisku, w tym Redis działających w systemie Linux, Windows lub dowolnym dostawcy chmury, takim jak Amazon Web Services i inne. Importowanie danych to prosty sposób tworzenia pamięci podręcznej z wstępnie wypełnionymi danymi. Podczas procesu importowania usługa Azure cache for Redis ładuje pliki RDB z usługi Azure Storage do pamięci, a następnie wstawia klucze do pamięci podręcznej.

Eksport umożliwia eksportowanie danych przechowywanych w usłudze Azure cache for Redis do Redis zgodnych plików RDB. Ta funkcja służy do przenoszenia danych z jednej pamięci podręcznej platformy Azure dla wystąpienia Redis do innego lub do innego serwera Redis. Podczas eksportowania plik tymczasowy jest tworzony na maszynie wirtualnej, która obsługuje wystąpienie serwera usługi Azure cache for Redis, i plik zostanie przekazany do wskazanego konta magazynu. Po zakończeniu operacji eksportowania ze stanem powodzenie lub niepowodzenie plik tymczasowy zostanie usunięty.

> [!IMPORTANT]
> Usługa Import/Export jest dostępna tylko w przypadku pamięci podręcznych warstwy Premium. Aby uzyskać więcej informacji i instrukcje, zobacz [Importowanie i eksportowanie danych w usłudze Azure cache for Redis](cache-how-to-import-export-data.md).
>
>

### <a name="reboot"></a>Ponowne uruchamianie
Blok **ponowne uruchomienie** umożliwia ponowne uruchomienie węzłów pamięci podręcznej. Ta funkcja ponownego uruchamiania umożliwia testowanie aplikacji pod kątem odporności w przypadku awarii węzła pamięci podręcznej.

![Ponowne uruchamianie](./media/cache-configure/redis-cache-reboot.png)

Jeśli masz pamięć podręczną Premium z włączoną obsługą klastrowania, możesz wybrać fragmentów pamięci podręcznej, aby przeprowadzić ponowny rozruch.

![Zrzut ekranu pokazujący, gdzie należy wybrać fragmentów pamięci podręcznej do ponownego uruchomienia.](./media/cache-configure/redis-cache-reboot-cluster.png)

Aby ponownie uruchomić co najmniej jeden węzeł pamięci podręcznej, wybierz odpowiednie węzły, a następnie kliknij przycisk **Uruchom ponownie**. Jeśli masz pamięć podręczną Premium z włączoną obsługą klastrowania, wybierz fragmentu do ponownego uruchomienia, a następnie kliknij przycisk **Uruchom ponownie**. Po kilku minutach wybrane węzły zostaną przełączone ponownie, a następnie ponownie w trybie online w ciągu kilku minut później.

> [!IMPORTANT]
> Dla wszystkich warstw cenowych jest teraz dostępny ponowny rozruch. Aby uzyskać więcej informacji i instrukcje, zobacz [pamięć podręczna platformy Azure dla Redis Administration — ponowny rozruch](cache-administration.md#reboot).
>
>


## <a name="monitoring"></a>Monitorowanie

Sekcja **monitorowanie** pozwala skonfigurować diagnostykę i monitorowanie dla swojej pamięci podręcznej platformy Azure dla Redis.
Aby uzyskać więcej informacji na temat pamięci podręcznej platformy Azure na potrzeby monitorowania i diagnostyki Redis, zobacz [Jak monitorować pamięć podręczną Azure dla Redis](cache-how-to-monitor.md).

![Diagnostyka](./media/cache-configure/redis-cache-diagnostics.png)

* [Metryki Redis](#redis-metrics)
* [Zasady alertów](#alert-rules)
* [Diagnostyka](#diagnostics)

### <a name="redis-metrics"></a>Metryki Redis
Kliknij pozycję **metryki Redis** , aby [wyświetlić metryki](cache-how-to-monitor.md#view-cache-metrics) pamięci podręcznej.

### <a name="alert-rules"></a>Zasady alertów

Kliknij pozycję **reguły alertów** , aby skonfigurować alerty oparte na usłudze Azure cache for Redis Metrics. Aby uzyskać więcej informacji, zobacz [Alerts](cache-how-to-monitor.md#alerts).

### <a name="diagnostics"></a>Diagnostyka

Domyślnie metryki pamięci podręcznej w Azure Monitor są [przechowywane przez 30 dni](../azure-monitor/platform/data-platform-metrics.md) , a następnie usuwane. Aby zachować metryki pamięci podręcznej przez dłużej niż 30 dni, kliknij pozycję **Diagnostyka** , aby [skonfigurować konto magazynu](cache-how-to-monitor.md#export-cache-metrics) używane do przechowywania diagnostyki pamięci podręcznej.

>[!NOTE]
>Oprócz archiwizowania metryk pamięci podręcznej do magazynu można także [przesyłać strumieniowo do centrum zdarzeń lub wysyłać je do dzienników Azure monitor](../azure-monitor/platform/stream-monitoring-data-event-hubs.md).
>
>

## <a name="support--troubleshooting-settings"></a>Obsługa & ustawień rozwiązywania problemów
Ustawienia w sekcji **Pomoc techniczna i rozwiązywanie problemów** zawierają opcje rozwiązywania problemów z pamięcią podręczną.

![Pomoc techniczna i rozwiązywanie problemów](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Kondycja zasobu](#resource-health)
* [Nowy wniosek o pomoc techniczną](#new-support-request)

### <a name="resource-health"></a>Kondycja zasobów
**Kondycja zasobu** obserwuje zasób i informuje o tym, że działa zgodnie z oczekiwaniami. Aby uzyskać więcej informacji o usłudze Azure Resource Health, zobacz [Omówienie usługi Azure Resource Health](../service-health/resource-health-overview.md).

> [!NOTE]
> Usługa Resource Health nie może obecnie zgłosić kondycji pamięci podręcznej platformy Azure dla wystąpień Redis hostowanych w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [czy wszystkie funkcje pamięci podręcznej działają w przypadku hostowania pamięci podręcznej w sieci wirtualnej?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network)
>
>

### <a name="new-support-request"></a>Nowy wniosek o pomoc techniczną
Kliknij pozycję **nowe żądanie obsługi** , aby otworzyć żądanie pomocy technicznej dla pamięci podręcznej.





## <a name="default-redis-server-configuration"></a>Domyślna konfiguracja serwera Redis
W nowej pamięci podręcznej platformy Azure dla wystąpień Redis są skonfigurowane następujące domyślne wartości konfiguracji Redis:

> [!NOTE]
> Ustawień w tej sekcji nie można zmienić za pomocą `StackExchange.Redis.IServer.ConfigSet` metody. Jeśli ta metoda jest wywoływana z jednym z poleceń w tej sekcji, zostanie zgłoszony wyjątek podobny do następującego przykładu:  
>
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>
> Wszystkie wartości, które można skonfigurować, takie jak **Max-Memory-Policy**, można konfigurować za pomocą Azure Portal lub narzędzi do zarządzania wiersza polecenia, takich jak Azure CLI lub PowerShell.
>
>

| Ustawienie | Wartość domyślna | Opis |
| --- | --- | --- |
| `databases` |16 |Domyślna liczba baz danych to 16, ale można skonfigurować inną liczbę na podstawie warstwy cenowej. <sup>1</sup> domyślna baza danych to baza danych 0, można wybrać inną dla poszczególnych połączeń, przy użyciu `connection.GetDatabase(dbid)` której `dbid` jest liczbą między `0` i `databases - 1` . |
| `maxclients` |Zależy od warstwy cenowej<sup>2</sup> |Ta wartość jest maksymalną liczbą połączonych klientów dozwolonych w tym samym czasie. Po osiągnięciu limitu Redis zamyka wszystkie nowe połączenia, zwracając błąd "Maksymalna liczba klientów osiągniętych". |
| `maxmemory-policy` |`volatile-lru` |Maxmemory Policy to ustawienie, w jaki sposób Redis wybiera elementy do usunięcia `maxmemory` , gdy zostanie osiągnięty rozmiar oferty pamięci podręcznej wybranej podczas tworzenia pamięci podręcznej. W przypadku usługi Azure cache for Redis ustawieniem domyślnym jest `volatile-lru` , co spowoduje usunięcie kluczy z ustawionym ustawieniem wygaśnięcia przy użyciu algorytmu LRU. To ustawienie można skonfigurować w Azure Portal. Aby uzyskać więcej informacji, zobacz [zasady pamięci](#memory-policies). |
| `maxmemory-samples` |3 |Aby zaoszczędzić pamięć, algorytmy LRU i minimalnego czasu wygaśnięcia są przybliżone algorytmy, a nie precyzyjne algorytmy. Domyślnie Redis sprawdza trzy klucze i wybiera te, które były ostatnio używane. |
| `lua-time-limit` |5000 |Maksymalny czas wykonywania skryptu Lua w milisekundach. W przypadku osiągnięcia maksymalnego czasu wykonania program Redis rejestruje, że skrypt nadal jest wykonywany po upływie maksymalnego dozwolonego czasu, i zaczyna odpowiadać na zapytania z błędem. |
| `lua-event-limit` |500 |Maksymalny rozmiar kolejki zdarzeń skryptu. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8 MB 60 |Limity buforów wyjściowych klienta mogą być używane w celu wymuszenia odłączenia klientów, którzy nie odczytują danych z serwera na tyle z powodów (częstą przyczyną jest to, że klient publikowania/sub nie może zużywać komunikatów tak szybko, jak Wydawca może je generować). Aby uzyskać więcej informacji, zobacz [https://redis.io/topics/clients](https://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup> Limit dla `databases` jest różny dla każdej usługi Azure cache dla warstwy cenowej Redis i można ją ustawić podczas tworzenia pamięci podręcznej. Jeśli `databases` ustawienie nie zostanie określone podczas tworzenia pamięci podręcznej, wartością domyślną jest 16.

* Pamięć podręczna podstawowa i Standardowa
  * Z pamięci podręcznej C0 (250 MB) — do 16 baz danych
  * Pamięć podręczna C1 (1 GB) — do 16 baz danych
  * Pamięć podręczna C2 (2,5 GB) — do 16 baz danych
  * Pamięć podręczna C3 (6 GB) — do 16 baz danych
  * Pamięć podręczna C4 (13 GB) — do 32 baz danych
  * Pamięć podręczna C5 (26 GB) — do 48 baz danych
  * Pamięć podręczna C6 (53 GB) — do 64 baz danych
* Pamięć podręczna Premium
  * P1 (6 GB-60 GB) — do 16 baz danych
  * P2 (13 GB-130 GB) — do 32 baz danych
  * P3 (26 GB-260 GB) — do 48 baz danych
  * P4 (53 GB – 530 GB) — do 64 baz danych
  * Wszystkie pamięć podręczna Premium z włączonym klastrem Redis — klaster Redis obsługuje tylko Korzystanie z bazy danych 0, dlatego `databases` limit dla dowolnej pamięci podręcznej Premium z włączonym klastrem Redis jest skuteczny 1, a polecenie [SELECT](https://redis.io/commands/select) jest niedozwolone. Aby uzyskać więcej informacji, zobacz [Czy muszę wprowadzić zmiany w aplikacji klienckiej w celu korzystania z klastrowania?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Aby uzyskać więcej informacji na temat baz danych, zobacz [co to są bazy danych Redis?](cache-development-faq.md#what-are-redis-databases)

> [!NOTE]
> `databases`Ustawienie można skonfigurować tylko podczas tworzenia pamięci podręcznej i tylko przy użyciu programu PowerShell, interfejsu wiersza polecenia lub innych klientów zarządzania. Przykład konfigurowania `databases` podczas tworzenia pamięci podręcznej przy użyciu programu PowerShell można znaleźć w temacie [New-AzRedisCache](cache-how-to-manage-redis-cache-powershell.md#databases).
>
>

<a name="maxclients"></a>
<sup>2</sup> `maxclients` jest różna dla każdej usługi Azure cache dla warstwy cenowej Redis.

* Pamięć podręczna podstawowa i Standardowa
  * Z pamięci podręcznej C0 (250 MB) — do 256 połączeń
  * Pamięć podręczna C1 (1 GB) — do 1 000 połączeń
  * Pamięć podręczna C2 (2,5 GB) — do 2 000 połączeń
  * Pamięć podręczna C3 (6 GB) — do 5 000 połączeń
  * Pamięć podręczna C4 (13 GB) — do 10 000 połączeń
  * W przypadku pamięci podręcznej C5 (26 GB) — do 15 000 połączeń
  * Pamięć podręczna C6 (53 GB) — do 20 000 połączeń
* Pamięć podręczna Premium
  * P1 (6 GB-60 GB) — do 7 500 połączeń
  * P2 (13 GB-130 GB) — do 15 000 połączeń
  * P3 (26 GB-260 GB) — do 30 000 połączeń
  * P4 (53 GB – 530 GB) — do 40 000 połączeń

> [!NOTE]
> Chociaż każdy rozmiar pamięci podręcznej pozwala *na* określoną liczbę połączeń, każde połączenie z usługą Redis ma powiązane z nim obciążenie. Przykładem takiego obciążenia będzie użycie procesora CPU i pamięci w wyniku szyfrowania TLS/SSL. Maksymalny limit połączeń dla danego rozmiaru pamięci podręcznej zakłada, że pamięć podręczna została załadowana w sposób jasny. Jeśli obciążenie związane z naliczeniem połączenia *i* obciążenie z operacji klienta przekroczy pojemność systemu, pamięć podręczna może mieć problemy z pojemnością, nawet jeśli nie przekroczono limitu połączenia dla bieżącego rozmiaru pamięci podręcznej.
>
>



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>Polecenia Redis nie są obsługiwane w usłudze Azure cache dla Redis
> [!IMPORTANT]
> Ponieważ konfiguracja i zarządzanie usługą Azure cache for Redis Instances jest zarządzana przez firmę Microsoft, następujące polecenia są wyłączone. Jeśli spróbujesz je wywołać, zostanie wyświetlony komunikat o błędzie podobny do `"(error) ERR unknown command"` .
>
> * BGREWRITEAOF
> * BGSAVE
> * SYGNATUR
> * DEBUGUJ
> * DOKONAĆ
> * ZAPISZ
> * WYŁĄCZONY
> * SLAVEOF
> * Polecenia zapisu klastra klastrów są wyłączone, ale dozwolone są polecenia klastra tylko do odczytu.
>
>

Aby uzyskać więcej informacji o poleceniach Redis, zobacz [https://redis.io/commands](https://redis.io/commands) .

## <a name="redis-console"></a>Konsola Redis
Możesz bezpiecznie wydać polecenia do pamięci podręcznej platformy Azure dla wystąpień Redis za pomocą **konsoli Redis**, która jest dostępna w Azure Portal dla wszystkich warstw pamięci podręcznej.

> [!IMPORTANT]
> - Konsola Redis nie współpracuje z siecią [wirtualną](cache-how-to-premium-vnet.md). Gdy pamięć podręczna jest częścią sieci wirtualnej, tylko klienci w sieci wirtualnej mogą uzyskać dostęp do pamięci podręcznej. Ponieważ konsola Redis działa w przeglądarce lokalnej, która znajduje się poza siecią wirtualną, nie może nawiązać połączenia z pamięcią podręczną.
> - Nie wszystkie polecenia Redis są obsługiwane w pamięci podręcznej platformy Azure dla Redis. Listę poleceń Redis, które są wyłączone dla usługi Azure cache for Redis, można znaleźć w sekcji poprzednie [polecenia Redis nieobsługiwane w usłudze Azure cache for Redis](#redis-commands-not-supported-in-azure-cache-for-redis) . Aby uzyskać więcej informacji o poleceniach Redis, zobacz [https://redis.io/commands](https://redis.io/commands) .
>
>

Aby uzyskać dostęp do konsoli Redis, kliknij pozycję **konsola** w bloku **Azure cache for Redis** .

![Zrzut ekranu, który podświetla przycisk konsoli.](./media/cache-configure/redis-console-menu.png)

Aby wydać polecenia względem wystąpienia pamięci podręcznej, wpisz odpowiednie polecenie w konsoli programu.

![Zrzut ekranu thas przedstawia konsolę Redis z poleceniem Input i wynikami.](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>Korzystanie z konsoli Redis z klastrowaną pamięcią podręczną Premium

Korzystając z konsoli Redis z klastrowaną pamięcią podręczną w warstwie Premium, można wydać polecenia do jednego fragmentu pamięci podręcznej. Aby wydać polecenie do określonego fragmentu, najpierw Połącz się z żądanym fragmentu, klikając go w selektorze fragmentu.

![Konsola Redis](./media/cache-configure/redis-console-premium-cluster.png)

W przypadku próby uzyskania dostępu do klucza, który jest przechowywany w innym fragmentu niż połączony fragmentu, zostanie wyświetlony komunikat o błędzie podobny do następującego:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

W poprzednim przykładzie fragmentu 1 jest wybranym fragmentu, ale znajduje się `myKey` w fragmentu 0, jak wskazano `(shard 0)` część komunikatu o błędzie. W tym przykładzie w celu uzyskania dostępu `myKey` Wybierz pozycję fragmentu 0 przy użyciu selektora fragmentu, a następnie wydaj odpowiednie polecenie.


## <a name="move-your-cache-to-a-new-subscription"></a>Przenoszenie pamięci podręcznej do nowej subskrypcji
Możesz przenieść pamięć podręczną do nowej subskrypcji, klikając przycisk **Przenieś**.

![Przenoszenie pamięci podręcznej platformy Azure dla usługi Redis](./media/cache-configure/redis-cache-move.png)

Aby uzyskać informacje na temat przenoszenia zasobów z jednej grupy zasobów do innej i z jednej subskrypcji do drugiej, zobacz [przenoszenie zasobów do nowej grupy zasobów lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać więcej informacji na temat pracy z poleceniami Redis, zobacz [jak można uruchomić polecenia Redis?](cache-development-faq.md#how-can-i-run-redis-commands)