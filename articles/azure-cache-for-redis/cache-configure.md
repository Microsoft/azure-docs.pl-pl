---
title: Jak skonfigurować Azure Cache for Redis
description: Zapoznaj się z domyślną konfiguracją usługi Redis dla Azure Cache for Redis i dowiedz się, jak skonfigurować Azure Cache for Redis wystąpień
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: yegu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ffcaf7fc50a310cdd4773868c62bbbb801619e3c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833152"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Jak skonfigurować Azure Cache for Redis
W tym temacie opisano konfiguracje dostępne Azure Cache for Redis wystąpień. W tym temacie omykamy również domyślną konfigurację serwera Redis dla Azure Cache for Redis wystąpień.

> [!NOTE]
> Aby uzyskać więcej informacji na temat konfigurowania i używania funkcji pamięci podręcznej Premium, zobacz Jak skonfigurować [trwałość,](cache-how-to-premium-persistence.md)Jak skonfigurować [klastrowanie](cache-how-to-premium-clustering.md)i Jak skonfigurować Virtual Network pamięci [podręcznej.](cache-how-to-premium-vnet.md)
>
>

## <a name="configure-azure-cache-for-redis-settings"></a>Konfigurowanie Azure Cache for Redis ustawień
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Azure Cache for Redis są przeglądane i konfigurowane w bloku **Azure Cache for Redis** za pomocą **menu Zasobów.**

![Azure Cache for Redis ustawień](./media/cache-configure/redis-cache-settings.png)

Poniższe ustawienia można wyświetlić i skonfigurować przy użyciu **menu Zasobów**.

* [Omówienie](#overview)
* [Dziennik aktywności](#activity-log)
* [Kontrola dostępu (IAM)](#access-control-iam)
* [Tagi](#tags)
* [Diagnozowanie i rozwiązywanie problemów](#diagnose-and-solve-problems)
* [Ustawienia](#settings)
    * [Klawisze dostępu](#access-keys)
    * [Ustawienia zaawansowane](#advanced-settings)
    * [Azure Cache for Redis Advisor](#azure-cache-for-redis-advisor)
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
    * [Importowanie danych](#importexport)
    * [Eksportowanie danych](#importexport)
    * [Ponowne uruchamianie](#reboot)
* [Monitorowanie](#monitoring)
    * [Metryki redis](#redis-metrics)
    * [Zasady alertów](#alert-rules)
    * [Diagnostyka](#diagnostics)
* Pomoc & rozwiązywania problemów z ustawieniami
    * [Kondycja zasobu](#resource-health)
    * [Nowy wniosek o pomoc techniczną](#new-support-request)


## <a name="overview"></a>Omówienie

**Omówienie** zawiera podstawowe informacje o pamięci podręcznej, takie jak nazwa, porty, warstwa cenowa i wybrane metryki pamięci podręcznej.

### <a name="activity-log"></a>Dziennik aktywności

Kliknij **pozycję Dziennik aktywności,** aby wyświetlić akcje wykonywane w pamięci podręcznej. Możesz również użyć filtrowania, aby rozwinąć ten widok, aby uwzględnić inne zasoby. Aby uzyskać więcej informacji na temat pracy z dziennikami inspekcji, zobacz [Audit operations with Resource Manager](../azure-resource-manager/management/view-activity-logs.md)(Operacje inspekcji za pomocą Resource Manager ). Aby uzyskać więcej informacji na temat monitorowania Azure Cache for Redis zdarzeń, zobacz [Operacje i alerty](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Kontrola dostępu (IAM)

Sekcja **Kontrola dostępu (IAM)** zapewnia obsługę kontroli dostępu na podstawie ról (RBAC) platformy Azure w Azure Portal. Ta konfiguracja pomaga organizacjom w prosty i precyzyjny sposób spełnić wymagania dotyczące zarządzania dostępem. Aby uzyskać więcej informacji, zobacz [Kontrola dostępu oparta na rolach na platformie Azure w Azure Portal](../role-based-access-control/role-assignments-portal.md).

### <a name="tags"></a>Tagi

Sekcja **Tagi** ułatwia organizowanie zasobów. Aby uzyskać więcej informacji, zobacz [Porządkowanie zasobów na platformie Azure za pomocą tagów](../azure-resource-manager/management/tag-resources.md).


### <a name="diagnose-and-solve-problems"></a>Diagnozowanie i rozwiązywanie problemów

Kliknij **pozycję Diagnozuj i rozwiąż problemy,** aby uzyskać informacje o typowych problemach i strategiach ich rozwiązywania.



## <a name="settings"></a>Ustawienia
Sekcja **Ustawienia** umożliwia dostęp do następujących ustawień pamięci podręcznej i skonfigurowanie ich.

* [Klawisze dostępu](#access-keys)
* [Ustawienia zaawansowane](#advanced-settings)
* [Azure Cache for Redis Advisor](#azure-cache-for-redis-advisor)
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
Kliknij **pozycję Klucze dostępu,** aby wyświetlić lub ponownie wygenerować klucze dostępu dla pamięci podręcznej. Te klucze są używane przez klientów łączących się z pamięcią podręczną.

![Azure Cache for Redis dostępu](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Ustawienia zaawansowane
Następujące ustawienia są konfigurowane w **bloku Ustawienia** zaawansowane.

* [Porty dostępu](#access-ports)
* [Zasady pamięci](#memory-policies)
* [Powiadomienia dotyczące przestrzeni kluczy (ustawienia zaawansowane)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Porty dostępu
Domyślnie dostęp bez protokołu TLS/SSL jest wyłączony dla nowych pamięci podręcznych. Aby włączyć port niebędący portem protokołu TLS, kliknij **pozycję** Nie dla ustawienia Zezwalaj na dostęp tylko za pośrednictwem protokołu **SSL** w bloku **Ustawienia** zaawansowane, a następnie kliknij przycisk **Zapisz.**

> [!NOTE]
> Dostęp TLS do usług Azure Cache for Redis obsługuje obecnie wersje TLS 1.0, 1.1 i 1.2, ale wersje 1.0 i 1.1 zostaną wkrótce wycofane.  Aby uzyskać więcej informacji, zapoznaj się ze stroną [Remove TLS 1.0 and 1.1 (Usuwanie z TLS 1.0 i 1.1).](cache-remove-tls-10-11.md)

![Azure Cache for Redis portów dostępu](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Zasady pamięci
Ustawienia **zasad Maxmemory,** **maxmemory-reserved** i **maxfragmentationmemory-reserved** w bloku Ustawienia zaawansowane konfigurują zasady pamięci dla pamięci podręcznej. 

![Azure Cache for Redis Maxmemory Policy](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Zasady maxmemory** konfigurują zasady eksmisji dla pamięci podręcznej i umożliwiają wybór spośród następujących zasad eksmisji:

* `volatile-lru` — są to domyślne zasady eksmisji.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Aby uzyskać więcej informacji na `maxmemory` temat zasad, zobacz [Zasady eksmisji](https://redis.io/topics/lru-cache#eviction-policies).

Ustawienie **maxmemory-reserved** konfiguruje ilość pamięci (w MB na wystąpienie w klastrze) zarezerwowaną dla operacji innych niż buforowanie, takich jak replikacja podczas pracy w trybie failover. Ustawienie tej wartości umożliwia bardziej spójne środowisko serwera Redis, gdy obciążenie będzie się zmieniać. Ta wartość powinna być wyższa dla obciążeń, które mają duże obciążenie zapisu. Gdy pamięć jest zarezerwowana dla takich operacji, nie jest ona dostępna do przechowywania danych w pamięci podręcznej.

Ustawienie **maxfragmentationmemory-reserved** konfiguruje ilość pamięci (w MB na wystąpienie w klastrze) zarezerwowaną do uwzględnienia fragmentacji pamięci. Ustawienie tej wartości umożliwia bardziej spójne środowisko serwera Redis, gdy pamięć podręczna jest pełna lub zbliży się do pełnej pamięci podręcznej, a współczynnik fragmentacji jest wysoki. Gdy pamięć jest zarezerwowana dla takich operacji, nie jest ona dostępna do przechowywania danych w pamięci podręcznej.

Jedną z rzeczy, które należy wziąć pod uwagę podczas wybierania nowej wartości rezerwacji pamięci **(maxmemory-reserved** lub **maxfragmentationmemory-reserved),** jest to, jak ta zmiana może wpłynąć na pamięć podręczną, która już działa z dużą ilością danych. Jeśli na przykład masz 53 GB pamięci podręcznej z 49 GB danych, zmień wartość rezerwacji na 8 GB, ta zmiana spowoduje spadek maksymalnej dostępnej pamięci dla systemu do 45 GB. Jeśli twoje bieżące lub twoje wartości są wyższe niż nowy limit 45 GB, system będzie musiał eksmitować dane, dopóki wartości i nie będą poniżej `used_memory` `used_memory_rss` `used_memory` `used_memory_rss` 45 GB. Eksmisja może zwiększyć obciążenie serwera i fragmentację pamięci. Aby uzyskać więcej informacji na temat metryk pamięci podręcznej, takich jak `used_memory` i , zobacz Dostępne `used_memory_rss` [metryki i interwały raportowania.](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)

> [!IMPORTANT]
> Ustawienia **maxmemory-reserved** i **maxfragmentationmemory-reserved** są dostępne tylko dla pamięci podręcznych w standardowych i Premium.
>
>

#### <a name="keyspace-notifications-advanced-settings"></a>Powiadomienia dotyczące przestrzeni kluczy (ustawienia zaawansowane)
Powiadomienia usługi Redis Keyspace są konfigurowane w **bloku Ustawienia** zaawansowane. Powiadomienia przestrzeni kluczy umożliwiają klientom otrzymywanie powiadomień w przypadku wystąpienia określonych zdarzeń.

![Azure Cache for Redis ustawienia zaawansowane](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Powiadomienia dotyczące przestrzeni kluczy i ustawienie **notify-keyspace-events** są dostępne tylko dla pamięci podręcznych w standardowych i Premium.
>
>

Aby uzyskać więcej informacji, zobacz [Redis Keyspace Notifications (Powiadomienia redis Keyspace).](https://redis.io/topics/notifications) Przykładowy kod można znaleźć w pliku [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) w [przykładzie Hello world.](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Azure Cache for Redis Advisor
W **Azure Cache for Redis Advisor są** wyświetlane zalecenia dotyczące pamięci podręcznej. Podczas normalnych operacji nie są wyświetlane żadne zalecenia.

![Zrzut ekranu przedstawiający miejsce wyświetlania zaleceń.](./media/cache-configure/redis-cache-no-recommendations.png)

Jeśli podczas operacji pamięci podręcznej wystąpią jakieś warunki, takie jak wysokie użycie pamięci, przepustowość sieci lub obciążenie serwera, w bloku Azure Cache for Redis sieciowym zostanie **wyświetlony alert.**

![Zrzut ekranu przedstawiający, gdzie są wyświetlane alerty w Azure Cache for Redis sekcji.](./media/cache-configure/redis-cache-recommendations-alert.png)

Więcej informacji można znaleźć w bloku **Zalecenia.**

![Zalecenia](./media/cache-configure/redis-cache-recommendations.png)

Możesz monitorować te metryki w sekcjach [Wykresy monitorowania](cache-how-to-monitor.md#monitoring-charts) i [Wykresy](cache-how-to-monitor.md#usage-charts) użycia w **bloku Azure Cache for Redis** danych.

Każda warstwa cenowa ma różne limity połączeń klienckich, pamięci i przepustowości. Jeśli pamięć podręczna zbliża się do maksymalnej pojemności tych metryk w dłuższym okresie, tworzone jest zalecenie. Aby uzyskać więcej informacji na temat metryk i limitów przeglądanych przez narzędzie **Zalecenia,** zobacz następującą tabelę:

| Azure Cache for Redis metryki | Więcej informacji |
| --- | --- |
| Użycie przepustowości sieci |[Wydajność pamięci podręcznej — dostępna przepustowość](cache-planning-faq.md#azure-cache-for-redis-performance) |
| Połączeni klienci |[Domyślna konfiguracja serwera Redis — maxclients](#maxclients) |
| Ładowanie serwera |[Wykresy użycia — ładowanie serwera Redis](cache-how-to-monitor.md#usage-charts) |
| Użycie pamięci |[Wydajność pamięci podręcznej — rozmiar](cache-planning-faq.md#azure-cache-for-redis-performance) |

Aby uaktualnić pamięć podręczną, kliknij pozycję **Uaktualnij teraz,** aby zmienić warstwę cenową i [skalować](#scale) pamięć podręczną. Aby uzyskać więcej informacji na temat wybierania warstwy cenowej, zobacz [Wybieranie właściwej warstwy](cache-overview.md#choosing-the-right-tier)


### <a name="scale"></a>Skalowanie
Kliknij **pozycję Skaluj,** aby wyświetlić lub zmienić warstwę cenową pamięci podręcznej. Aby uzyskać więcej informacji na temat skalowania, [zobacz Jak skalować Azure Cache for Redis](cache-how-to-scale.md).

![Azure Cache for Redis cenowa](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Rozmiar klastra Redis
Kliknij **pozycję Rozmiar klastra,** aby zmienić rozmiar klastra dla uruchomionej pamięci podręcznej Premium z włączonym klastrem.

![Rozmiar klastra](./media/cache-configure/redis-cache-redis-cluster-size.png)

Aby zmienić rozmiar klastra, użyj suwaka lub wpisz liczbę z wartości od 1 do 10 w polu tekstowym **Liczba** fragmentów i kliknij przycisk **OK,** aby zapisać.

> [!IMPORTANT]
> Klastrowanie Redis jest dostępne tylko dla pamięci podręcznych Premium. Aby uzyskać więcej informacji, [zobacz How to configure clustering for a Premium Azure Cache for Redis (Jak](cache-how-to-premium-clustering.md)skonfigurować klastrowanie dla usługi Premium Azure Cache for Redis).
>
>


### <a name="redis-data-persistence"></a>Trwałość danych Redis
Kliknij **pozycję Trwałość danych,** aby włączyć, wyłączyć lub skonfigurować trwałość danych dla pamięci podręcznej Premium. Azure Cache for Redis zapewnia trwałość redis przy użyciu trwałości RDB lub AOF.

Aby uzyskać więcej informacji, [zobacz Jak skonfigurować trwałość dla usługi Premium Azure Cache for Redis](cache-how-to-premium-persistence.md).


> [!IMPORTANT]
> Trwałość danych redis jest dostępna tylko w przypadku pamięci podręcznych Premium.
>
>

### <a name="schedule-updates"></a>Aktualizacje harmonogramu
Blok **Planowanie aktualizacji** umożliwia wyznaczenie okna obsługi aktualizacji serwera Redis dla pamięci podręcznej.

> [!IMPORTANT]
> Okno obsługi dotyczy tylko aktualizacji serwera Redis, a nie żadnych aktualizacji platformy Azure ani aktualizacji systemu operacyjnego maszyn wirtualnych hostowanych w pamięci podręcznej.
>
>

![Aktualizacje harmonogramu](./media/cache-configure/redis-schedule-updates.png)

Aby określić okno obsługi, sprawdź żądane dni i określ godzinę rozpoczęcia okna obsługi dla każdego dnia, a następnie kliknij przycisk **OK.** Czas okna obsługi jest w czasie UTC.

Aby uzyskać więcej informacji i instrukcji, zobacz [Azure Cache for Redis — Planowanie aktualizacji](cache-administration.md#schedule-updates)

### <a name="geo-replication"></a>Replikacja geograficzna

Blok **Replikacja geograficzna** udostępnia mechanizm łączenia dwóch wystąpień Azure Cache for Redis Premium. Jedna pamięć podręczna jest wyznaczona jako podstawowa połączona pamięć podręczna, a druga jako pomocnicza połączona pamięć podręczna. Pomocnicza połączona pamięć podręczna staje się tylko do odczytu, a dane zapisywane w podstawowej pamięci podręcznej są replikowane do pomocniczej połączonej pamięci podręcznej. Ta funkcja może służyć do replikowania pamięci podręcznej między regionami świadczenia usługi Azure.

> [!IMPORTANT]
> **Replikacja geograficzna** jest dostępna tylko dla pamięci podręcznych w warstwie Premium. Aby uzyskać więcej informacji i instrukcji, zobacz [Jak skonfigurować replikację geograficzną dla Azure Cache for Redis](cache-how-to-geo-replication.md).
>
>

### <a name="virtual-network"></a>Virtual Network
Sekcja **Virtual Network** umożliwia skonfigurowanie ustawień sieci wirtualnej dla pamięci podręcznej. Aby uzyskać informacje na temat tworzenia pamięci podręcznej Premium z obsługą sieci wirtualnej i aktualizowania jej ustawień, zobacz Jak skonfigurować Virtual Network dla sieci [Premium Azure Cache for Redis.](cache-how-to-premium-vnet.md)

> [!IMPORTANT]
> Ustawienia sieci wirtualnej są dostępne tylko dla pamięci podręcznych Premium, które zostały skonfigurowane z obsługą sieci wirtualnej podczas tworzenia pamięci podręcznej.
>
>

### <a name="firewall"></a>Firewall

Konfiguracja reguł zapory jest dostępna dla wszystkich Azure Cache for Redis sieciowych.

Kliknij **pozycję Zapora,** aby wyświetlić i skonfigurować reguły zapory dla pamięci podręcznej.

![Firewall](./media/cache-configure/redis-firewall-rules.png)

Można określić reguły zapory z zakresem adresów IP rozpoczęcia i zakończenia. Po skonfigurowaniu reguł zapory tylko połączenia klientów z określonych zakresów adresów IP mogą łączyć się z pamięcią podręczną. Po zapisaniu reguły zapory istnieje krótkie opóźnienie, zanim reguła będzie obowiązywać. To opóźnienie jest zwykle mniejsze niż jedna minuta.

> [!IMPORTANT]
> Połączenia z Azure Cache for Redis monitorowania sieci są zawsze dozwolone, nawet jeśli skonfigurowano reguły zapory.
>
>

### <a name="properties"></a>Właściwości
Kliknij **pozycję Właściwości,** aby wyświetlić informacje o pamięci podręcznej, w tym punkt końcowy i porty pamięci podręcznej.

![Azure Cache for Redis właściwości](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Blokady
Sekcja **Blokady** umożliwia zablokowanie subskrypcji, grupy zasobów lub zasobu, aby zapobiec przypadkowemu usunięciu lub zmodyfikowaniu krytycznych zasobów przez innych użytkowników w organizacji. Aby uzyskać więcej informacji, zobacz [Lock resources with Azure Resource Manager](../azure-resource-manager/management/lock-resources.md) (Blokowanie zasobów w usłudze Azure Resource Manager).

### <a name="automation-script"></a>Skrypt usługi Automation

Kliknij **pozycję Skrypt automatyzacji,** aby skompilować i wyeksportować szablon wdrożonych zasobów do przyszłych wdrożeń. Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [Deploy resources with Azure Resource Manager templates (Wdrażanie zasobów za pomocą Azure Resource Manager szablonów).](../azure-resource-manager/templates/deploy-powershell.md)

## <a name="administration-settings"></a>Ustawienia administracji
Ustawienia w sekcji **Administracja** umożliwiają wykonywanie następujących zadań administracyjnych dotyczących pamięci podręcznej.

![Administracja](./media/cache-configure/redis-cache-administration.png)

* [Importowanie danych](#importexport)
* [Eksportowanie danych](#importexport)
* [Ponowne uruchamianie](#reboot)


### <a name="importexport"></a>Import/Export
Import/Export to operacja zarządzania danymi usługi Azure Cache for Redis, która umożliwia importowanie i eksportowanie danych w pamięci podręcznej przez importowanie i eksportowanie migawki usługi Azure Cache for Redis Database (RDB) z pamięci podręcznej Premium do stronicowego obiektu blob na koncie usługi Azure Storage. Import/eksport umożliwia migrację między różnymi wystąpieniami Azure Cache for Redis lub wypełnienie pamięci podręcznej danymi przed użyciem.

Importowanie może służyć do importowania plików RDB zgodnych z usługą Redis z dowolnego serwera Redis działającego w dowolnej chmurze lub środowisku, w tym redis uruchomionego w systemie Linux, Windows lub dowolnym dostawcy usług w chmurze, takim jak Amazon Web Services i inni. Importowanie danych to prosty sposób tworzenia pamięci podręcznej ze wstępnie wypełnionymi danymi. Podczas importowania program Azure Cache for Redis pliki RDB z usługi Azure Storage do pamięci, a następnie wstawia klucze do pamięci podręcznej.

Eksport umożliwia eksportowanie danych przechowywanych w programie Azure Cache for Redis do plików RDB zgodnych z usługą Redis. Ta funkcja umożliwia przenoszenie danych z jednego Azure Cache for Redis do innego lub na inny serwer Redis. Podczas eksportowania na maszynie wirtualnej, która hostuje wystąpienie serwera Azure Cache for Redis, jest tworzony plik tymczasowy, a plik jest przekazywany do wskazanego konta magazynu. Po zakończeniu operacji eksportowania ze stanem powodzenia lub niepowodzenia plik tymczasowy zostanie usunięty.

> [!IMPORTANT]
> Import/eksport jest dostępny tylko dla pamięci podręcznych w warstwie Premium. Aby uzyskać więcej informacji i instrukcji, zobacz [Import and Export data in Azure Cache for Redis (Importowanie i eksportowanie danych w programie Azure Cache for Redis](cache-how-to-import-export-data.md)).
>
>

### <a name="reboot"></a>Ponowne uruchamianie
Blok **Ponowne** uruchamianie umożliwia ponowne uruchomienie węzłów pamięci podręcznej. Ta funkcja ponownego uruchamiania umożliwia przetestowanie aplikacji pod kątem odporności w przypadku awarii węzła pamięci podręcznej.

![Ponowne uruchamianie](./media/cache-configure/redis-cache-reboot.png)

Jeśli masz pamięć podręczną Premium z włączonym klastrem, możesz wybrać fragmenty pamięci podręcznej do ponownego uruchomienia.

![Zrzut ekranu przedstawiający, gdzie wybrać fragmenty pamięci podręcznej do ponownego uruchomienia.](./media/cache-configure/redis-cache-reboot-cluster.png)

Aby ponownie uruchomić co najmniej jeden węzeł pamięci podręcznej, wybierz żądane węzły i kliknij pozycję **Uruchom ponownie.** Jeśli masz pamięć podręczną Premium z włączonym klastrem, wybierz fragmenty do ponownego uruchomienia, a następnie kliknij przycisk **Uruchom ponownie.** Po kilku minutach zostanie uruchomiony ponownie wybrany węzeł i kilka minut powrócisz do trybu online.

> [!IMPORTANT]
> Ponowne uruchomienie jest teraz dostępne dla wszystkich warstw cenowych. Aby uzyskać więcej informacji i instrukcji, zobacz [Azure Cache for Redis administracji — ponowne uruchomienie](cache-administration.md#reboot).
>
>


## <a name="monitoring"></a>Monitorowanie

Sekcja **Monitorowanie** umożliwia skonfigurowanie diagnostyki i monitorowania Azure Cache for Redis.
Aby uzyskać więcej informacji na Azure Cache for Redis monitorowania i diagnostyki, zobacz [Jak monitorować Azure Cache for Redis](cache-how-to-monitor.md).

![Diagnostyka](./media/cache-configure/redis-cache-diagnostics.png)

* [Metryki redis](#redis-metrics)
* [Zasady alertów](#alert-rules)
* [Diagnostyka](#diagnostics)

### <a name="redis-metrics"></a>Metryki redis
Kliknij **pozycję Metryki usługi Redis,** [aby wyświetlić metryki](cache-how-to-monitor.md#view-cache-metrics) dla pamięci podręcznej.

### <a name="alert-rules"></a>Zasady alertów

Kliknij **pozycję Reguły alertów,** aby skonfigurować alerty na Azure Cache for Redis metryk. Aby uzyskać więcej informacji, zobacz [Alerty](cache-how-to-monitor.md#alerts).

### <a name="diagnostics"></a>Diagnostyka

Domyślnie metryki pamięci podręcznej w Azure Monitor [są przechowywane przez 30 dni, a](../azure-monitor/essentials/data-platform-metrics.md) następnie usuwane. Aby utrwalić metryki pamięci podręcznej  dłużej niż 30 dni, kliknij pozycję Diagnostyka, aby skonfigurować [konto](cache-how-to-monitor.md#export-cache-metrics) magazynu używane do przechowywania diagnostyki pamięci podręcznej.

>[!NOTE]
>Oprócz archiwizowania metryk pamięci podręcznej w magazynie można również przesyłać strumieniowo do centrum zdarzeń lub wysyłać je do Azure Monitor [danych.](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md)
>
>

## <a name="support--troubleshooting-settings"></a>Pomoc & rozwiązywania problemów z ustawieniami
Ustawienia w sekcji **Pomoc techniczna i rozwiązywanie problemów** zapewniają opcje rozwiązywania problemów z pamięcią podręczną.

![Pomoc techniczna i rozwiązywanie problemów](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Kondycja zasobu](#resource-health)
* [Nowy wniosek o pomoc techniczną](#new-support-request)

### <a name="resource-health"></a>Kondycja zasobów
**Kondycja zasobu** obserwuje zasób i informuje o tym, czy działa zgodnie z oczekiwaniami. Aby uzyskać więcej informacji na temat usługi Azure Resource Health, zobacz [Omówienie usługi Azure Resource Health.](../service-health/resource-health-overview.md)

> [!NOTE]
> Kondycja zasobu nie jest obecnie w stanie raportować kondycji wystąpień Azure Cache for Redis hostowanych w sieci wirtualnej. Aby uzyskać więcej informacji, zobacz [Czy wszystkie funkcje pamięci podręcznej działają podczas hostowania pamięci podręcznej w sieci wirtualnej?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network)
>
>

### <a name="new-support-request"></a>Nowy wniosek o pomoc techniczną
Kliknij **pozycję Nowy wniosek o** pomoc techniczną, aby otworzyć żądanie obsługi pamięci podręcznej.





## <a name="default-redis-server-configuration"></a>Domyślna konfiguracja serwera Redis
Nowe Azure Cache for Redis są konfigurowane przy użyciu następujących domyślnych wartości konfiguracji usługi Redis:

> [!NOTE]
> Ustawień w tej sekcji nie można zmienić przy użyciu `StackExchange.Redis.IServer.ConfigSet` metody . Jeśli ta metoda jest wywoływana za pomocą jednego z poleceń w tej sekcji, zgłaszany jest wyjątek podobny do następującego przykładu:  
>
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>
> Wszystkie wartości, które można konfigurować, takie jak **max-memory-policy,** można konfigurować za pomocą Azure Portal lub narzędzi do zarządzania wierszem polecenia, takich jak interfejs wiersza polecenia platformy Azure lub program PowerShell.
>
>

| Ustawienie | Wartość domyślna | Opis |
| --- | --- | --- |
| `databases` |16 |Domyślna liczba baz danych to 16, ale można skonfigurować inną liczbę na podstawie warstwy cenowej. <sup>1</sup> Domyślna baza danych to DB 0. Możesz wybrać inną bazę danych dla poszczególnych połączeń, używając wartości , gdzie jest liczbą między `connection.GetDatabase(dbid)` `dbid` i `0` `databases - 1` . |
| `maxclients` |Zależy od warstwy cenowej<sup>2</sup> |Ta wartość to maksymalna dozwolona liczba połączonych klientów w tym samym czasie. Po osiągnięciu limitu redis zamyka wszystkie nowe połączenia, zwracając błąd "osiągnięto maksymalną liczbę klientów". |
| `maxmemory-policy` |`volatile-lru` |Zasady Maxmemory to ustawienie sposobu wybierania przez usługę Redis, co należy usunąć po osiągnięciu rozmiaru oferty pamięci podręcznej wybranej podczas tworzenia `maxmemory` pamięci podręcznej. W Azure Cache for Redis ustawieniem domyślnym jest , co powoduje usunięcie kluczy z ustawionym wygaśnięciem `volatile-lru` przy użyciu algorytmu LRU. To ustawienie można skonfigurować w Azure Portal. Aby uzyskać więcej informacji, zobacz [Zasady pamięci](#memory-policies). |
| `maxmemory-samples` |3 |Aby zaoszczędzić pamięć, algorytmy LRU i minimalny czas wygaśnięcia są algorytmami przybliżonymi, a nie dokładnymi algorytmami. Domyślnie redis sprawdza trzy klucze i wybiera ten, który był używany rzadziej. |
| `lua-time-limit` |5000 |Maksymalny czas wykonywania skryptu Lua w milisekundach. Jeśli zostanie osiągnięty maksymalny czas wykonywania, redis rejestruje, że skrypt jest nadal w trakcie wykonywania po upływie maksymalnego dozwolonego czasu, i rozpoczyna odpowiadanie na zapytania z błędem. |
| `lua-event-limit` |500 |Maksymalny rozmiar kolejki zdarzeń skryptu. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8mb 60 |Limity buforu wyjściowego klienta mogą służyć do wymuszania rozłączania klientów, którzy nie odczytują danych z serwera wystarczająco szybko z jakiegoś powodu (częstą przyczyną jest to, że klient usługi Pub/Sub nie może używać komunikatów tak szybko, jak wydawca może je tworzyć). Aby uzyskać więcej informacji, zobacz [https://redis.io/topics/clients](https://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup> Limit dla jest inny dla każdej Azure Cache for Redis cenowej i `databases` można go ustawić podczas tworzenia pamięci podręcznej. Jeśli podczas `databases` tworzenia pamięci podręcznej nie określono żadnego ustawienia, wartość domyślna to 16.

* Podstawowa i Standardowa pamięć podręczna
  * Pamięć podręczna C0 (250 MB) — maksymalnie 16 baz danych
  * Pamięć podręczna C1 (1 GB) — maksymalnie 16 baz danych
  * Pamięć podręczna C2 (2,5 GB) — maksymalnie 16 baz danych
  * Pamięć podręczna C3 (6 GB) — maksymalnie 16 baz danych
  * Pamięć podręczna C4 (13 GB) — maksymalnie 32 bazy danych
  * Pamięć podręczna C5 (26 GB) — do 48 baz danych
  * Pamięć podręczna C6 (53 GB) — maksymalnie 64 bazy danych
* Pamięci podręczne Premium
  * P1 (6 GB–60 GB) — do 16 baz danych
  * P2 (13 GB–130 GB) — maksymalnie 32 bazy danych
  * P3 (26 GB–260 GB) — do 48 baz danych
  * P4 (53 GB–530 GB) — do 64 baz danych
  * Wszystkie pamięci podręczne w wersji Premium z włączonym klastrem Redis — klaster Redis obsługuje tylko użycie bazy danych 0, dlatego limit dla dowolnej pamięci podręcznej Premium z włączonym klastrem Redis jest efektywnie 1 i polecenie Select jest `databases` niedozwolone. [](https://redis.io/commands/select) Aby uzyskać więcej informacji, zobacz Czy muszę wprowadzić jakieś zmiany w aplikacji [klienckiej, aby korzystać z klastrowania?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

Aby uzyskać więcej informacji na temat baz danych, [zobacz Co to są bazy danych Redis?](cache-development-faq.md#what-are-redis-databases)

> [!NOTE]
> To ustawienie można skonfigurować tylko podczas tworzenia pamięci podręcznej i tylko przy użyciu programu PowerShell, interfejsu `databases` wiersza polecenia lub innych klientów zarządzania. Aby uzyskać przykład konfigurowania podczas tworzenia `databases` pamięci podręcznej przy użyciu programu PowerShell, zobacz [New-AzRedisCache](cache-how-to-manage-redis-cache-powershell.md#databases).
>
>

<a name="maxclients"></a>
<sup>2</sup> `maxclients` różni się dla każdej Azure Cache for Redis cenowej.

* Podstawowa i Standardowa pamięć podręczna
  * Pamięć podręczna C0 (250 MB) — do 256 połączeń
  * Pamięć podręczna C1 (1 GB) — do 1000 połączeń
  * Pamięć podręczna C2 (2,5 GB) — do 2000 połączeń
  * Pamięć podręczna C3 (6 GB) — do 5000 połączeń
  * Pamięć podręczna C4 (13 GB) — do 10 000 połączeń
  * Pamięć podręczna C5 (26 GB) — do 15 000 połączeń
  * Pamięć podręczna C6 (53 GB) — do 20 000 połączeń
* Pamięci podręczne Premium
  * P1 (6 GB–60 GB) — do 7500 połączeń
  * P2 (13 GB–130 GB) — do 15 000 połączeń
  * P3 (26 GB–260 GB) — do 30 000 połączeń
  * P4 (53 GB–530 GB) — do 40 000 połączeń

> [!NOTE]
> Każdy rozmiar pamięci podręcznej zezwala *na określoną* liczbę połączeń, ale z każdym połączeniem z usługą Redis jest skojarzony narzut. Przykładem takiego obciążenia może być użycie procesora i pamięci w wyniku szyfrowania TLS/SSL. Maksymalny limit połączeń dla danego rozmiaru pamięci podręcznej zakłada, że pamięć podręczna jest ładowana w sposób lekki. Jeśli obciążenie z narzutu na *połączenie oraz* obciążenie operacji klienta przekracza pojemność systemu, pamięć podręczna może wystąpić problemy z pojemnością, nawet jeśli nie przekroczono limitu połączeń dla bieżącego rozmiaru pamięci podręcznej.
>
>



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>Polecenia redis nie są obsługiwane w Azure Cache for Redis
> [!IMPORTANT]
> Ponieważ konfiguracja wystąpień Azure Cache for Redis i zarządzanie nimi jest zarządzane przez firmę Microsoft, następujące polecenia są wyłączone. Jeśli spróbujesz je wywołać, zostanie wyświetlony komunikat o błędzie podobny do `"(error) ERR unknown command"` .
>
> * BGRITEAOF
> * BGSAVE
> * Config
> * DEBUGUJ
> * Migracji
> * ZAPISZ
> * Zamknięcia
> * PODRZĘDNY
> * KLASTER — polecenia zapisu klastra są wyłączone, ale polecenia klastra tylko do odczytu są dozwolone.
>
>

Aby uzyskać więcej informacji na temat poleceń redis, zobacz [https://redis.io/commands](https://redis.io/commands) .

## <a name="redis-console"></a>Konsola Redis
Polecenia dla wystąpień pamięci podręcznej można bezpiecznie wydawać Azure Cache for Redis przy użyciu konsoli **Redis,** która jest dostępna w warstwie Azure Portal dla wszystkich warstw pamięci podręcznej.

> [!IMPORTANT]
> - Konsola Redis Console nie działa z [siecią wirtualną](cache-how-to-premium-vnet.md). Gdy pamięć podręczna jest częścią sieci wirtualnej, tylko klienci w sieci wirtualnej mogą uzyskać dostęp do tej pamięci podręcznej. Ponieważ konsola Redis Jest uruchamiana w lokalnej przeglądarce, która znajduje się poza siecią wirtualną, nie może nawiązać połączenia z pamięcią podręczną.
> - Nie wszystkie polecenia redis są obsługiwane w Azure Cache for Redis. Aby uzyskać listę poleceń redis, które są wyłączone dla Azure Cache for Redis, zobacz poprzednie polecenia [redis](#redis-commands-not-supported-in-azure-cache-for-redis) nie są obsługiwane w Azure Cache for Redis sekcji. Aby uzyskać więcej informacji na temat poleceń redis, zobacz [https://redis.io/commands](https://redis.io/commands) .
>
>

Aby uzyskać dostęp do konsoli Redis, kliknij **pozycję Konsola** w **Azure Cache for Redis** bloku.

![Zrzut ekranu przedstawiający przycisk Konsola.](./media/cache-configure/redis-console-menu.png)

Aby wydać polecenia dla wystąpienia pamięci podręcznej, wpisz odpowiednie polecenie w konsoli.

![Zrzut ekranu przedstawia konsolę Redis Console z poleceniem wejściowym i wynikami.](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>Korzystanie z konsoli Redis z klastrowana pamięcią podręczną Premium

W przypadku korzystania z konsoli Redis z klastrowanej pamięci podręcznej Premium można wydawać polecenia do pojedynczego fragmentu pamięci podręcznej. Aby wydać polecenie dla określonego fragmentu, najpierw połącz się z żądanym fragmentem, klikając go na s wyboru fragmentu.

![Konsola Redis](./media/cache-configure/redis-console-premium-cluster.png)

Jeśli spróbujemy uzyskać dostęp do klucza przechowywanego w innym hard niż połączony fragment, zostanie wyświetlony komunikat o błędzie podobny do następującego:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

W poprzednim przykładzie fragment 1 jest wybranym fragmentem, ale znajduje się w 0, jak wskazuje część `myKey` `(shard 0)` komunikatu o błędzie. W tym przykładzie w celu uzyskania dostępu wybierz fragment 0 przy użyciu selektora fragmentów, a następnie `myKey` wydaj odpowiednie polecenie.


## <a name="move-your-cache-to-a-new-subscription"></a>Przenoszenie pamięci podręcznej do nowej subskrypcji
Pamięć podręczną można przenieść do nowej subskrypcji, klikając pozycję **Przenieś.**

![Przenoszenie Azure Cache for Redis](./media/cache-configure/redis-cache-move.png)

Aby uzyskać informacje na temat przenoszenia zasobów z jednej grupy zasobów do innej oraz z jednej subskrypcji do innej, zobacz Przenoszenie zasobów do nowej grupy zasobów [lub subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md).

## <a name="next-steps"></a>Następne kroki
* Aby uzyskać więcej informacji na temat pracy z poleceniami redis, zobacz [Jak mogę uruchamiać polecenia redis?](cache-development-faq.md#how-can-i-run-redis-commands)
