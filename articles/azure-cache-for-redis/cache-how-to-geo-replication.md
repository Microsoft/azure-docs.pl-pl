---
title: Konfigurowanie replikacji geograficznej dla wystąpień Azure Cache for Redis Premium
description: Dowiedz się, jak replikować wystąpienia Azure Cache for Redis Premium w różnych regionach świadczenia usługi Azure
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: 0be2bb59b46dc827001d89f8e0f1be23f35a714d
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536089"
---
# <a name="configure-geo-replication-for-premium-azure-cache-for-redis-instances"></a>Konfigurowanie replikacji geograficznej dla wystąpień Azure Cache for Redis Premium

W tym artykule dowiesz się, jak skonfigurować usługę Azure Cache z replikacją geograficzną przy użyciu Azure Portal.

Replikacja geograficzna łączy dwa wystąpienia Azure Cache for Redis Premium i tworzy relację replikacji danych. Te wystąpienia pamięci podręcznej zwykle znajdują się w różnych regionach świadczenia usługi Azure, chociaż nie jest to wymagane. Jedno wystąpienie działa jako podstawowe, a drugie jako pomocnicze. Podstawowa obsługuje żądania odczytu i zapisu oraz propaguje zmiany do pomocniczej. Ten proces jest kontynuowany do momentu usunięcia połączenia między dwoma wystąpieniami.

> [!NOTE]
> Replikacja geograficzna została zaprojektowana jako rozwiązanie odzyskiwania po awarii.
>
>

## <a name="geo-replication-prerequisites"></a>Wymagania wstępne dotyczące replikacji geograficznej

Aby skonfigurować replikację geograficzną między dwiema pamięciami podręcznych, należy spełnić następujące wymagania wstępne:

- Obie pamięci podręczne to [pamięci podręczne w warstwie Premium.](cache-overview.md#service-tiers)
- Obie pamięci podręczne znajdują się w tej samej subskrypcji platformy Azure.
- Pomocnicza połączona pamięć podręczna ma ten sam rozmiar pamięci podręcznej lub większy rozmiar pamięci podręcznej niż podstawowa połączona pamięć podręczna.
- Obie pamięci podręczne są tworzone i w stanie uruchomienia.

Niektóre funkcje nie są obsługiwane w przypadku replikacji geograficznej:

- Trwałość nie jest obsługiwana w przypadku replikacji geograficznej.
- Klastrowanie jest obsługiwane, jeśli obie pamięci podręczne mają włączone klastrowanie i mają taką samą liczbę fragmentów.
- Pamięci podręczne w tej samej sieci wirtualnej są obsługiwane.
- Pamięci podręczne w różnych sieciach wirtualnych są obsługiwane z zastrzeżeniami. Aby uzyskać więcej informacji, zobacz Czy mogę używać replikacji geograficznej z [pamięciami podręcznych](#can-i-use-geo-replication-with-my-caches-in-a-vnet) w sieci wirtualnej?.

Po skonfigurowaniu replikacji geograficznej do pary połączonej pamięci podręcznej mają zastosowanie następujące ograniczenia:

- Pomocnicza połączona pamięć podręczna jest tylko do odczytu. Możesz go odczytać, ale nie możesz w nim zapisywać żadnych danych. Jeśli zdecydujesz się na odczyt z wystąpienia usługi Geo-Secondary, należy pamiętać, że za każdym razem, gdy odbywa się pełna synchronizacja danych między usługą Geo-Primary i usługą Geo-Secondary (ma to miejsce, gdy jest aktualizowany program Geo-Primary lub Geo-Secondary i w niektórych scenariuszach ponownego uruchamiania), wystąpienie usługi Geo-Secondary będzie zgłaszać błędy (z informacją, że pełna synchronizacja danych jest w toku) dla dowolnej operacji usługi Redis względem niego do czasu ukończenia pełnej synchronizacji danych między Geo-Primary i Geo-Secondary. Aplikacje odczytujące dane Geo-Seocndary powinny zostać sbudowaną tak, aby wracały do Geo-Primary zawsze, gdy Geo-Seocndary zgłasza takie błędy. 
- Wszystkie dane, które były w dodatkowej połączonej pamięci podręcznej przed dodaniu łącza, zostaną usunięte. Jeśli jednak replikacja geograficzna zostanie później usunięta, replikowane dane pozostają w dodatkowej połączonej pamięci podręcznej.
- Nie można skalować [obu pamięci](cache-how-to-scale.md) podręcznych, gdy pamięci podręczne są połączone.
- Nie można zmienić [liczby fragmentów,](cache-how-to-premium-clustering.md) jeśli pamięć podręczna ma włączone klastrowanie.
- Nie można włączyć trwałości w obu pamięciach podręcznych.
- Możesz [wyeksportować z](cache-how-to-import-export-data.md#export) jednej z tych pamięci podręcznych.
- Nie można zaimportować [do dodatkowej](cache-how-to-import-export-data.md#import) połączonej pamięci podręcznej.
- Nie można usunąć połączonej pamięci podręcznej ani grupy zasobów, która je zawiera, dopóki nie odłączysz pamięci podręcznych. Aby uzyskać więcej informacji, zobacz Dlaczego operacja nie powiodła się [podczas próby usunięcia połączonej pamięci podręcznej?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Jeśli pamięci podręczne znajdują się w różnych regionach, koszty ruchu wychodzącego sieci mają zastosowanie do danych przenoszonych między regionami. Aby uzyskać więcej informacji, zobacz Ile kosztuje replikowanie danych [między regionami świadczenia usługi Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Automatyczny tryb failover nie występuje między podstawową i pomocniczą powiązaną pamięcią podręczną. Aby uzyskać więcej informacji i informacji na temat sposobu pracy w trybie failover aplikacji klienckiej, zobacz Jak działa tryb failover w dodatkowej [połączonej pamięci podręcznej?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Dodawanie linku replikacji geograficznej

1. Aby połączyć dwie pamięci podręczne w celu  replikacji geograficznej, kliknij przycisk Replikacja geograficzna w menu Zasób pamięci podręcznej, która ma być podstawową połączoną pamięcią podręczną. Następnie kliknij link **Dodaj replikację pamięci podręcznej** w **bloku Replikacja geograficzna.**

    ![Dodaj link](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Kliknij nazwę zamierzonej pomocniczej pamięci podręcznej na liście **Zgodne pamięci podręczne.** Jeśli pomocnicza pamięć podręczna nie jest wyświetlana na liście, sprawdź, czy zostały spełnione wymagania wstępne replikacji geograficznej dla pomocniczej pamięci podręcznej. [](#geo-replication-prerequisites) Aby filtrować pamięci podręczne według regionu, kliknij region na mapie, aby wyświetlić tylko te pamięci podręczne na liście **Zgodne pamięci podręczne.**

    ![Pamięci podręczne zgodne z replikacją geograficzną](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Możesz również rozpocząć proces łączenia lub wyświetlić szczegółowe informacje o pomocniczej pamięci podręcznej przy użyciu menu kontekstowego.

    ![Menu kontekstowe replikacji geograficznej](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Kliknij **pozycję Połącz,** aby połączyć ze sobą dwie pamięci podręczne i rozpocząć proces replikacji.

    ![Pamięci podręczne linków](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Postęp procesu replikacji można wyświetlić w bloku **Replikacja geograficzna.**

    ![Stan łączenia](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Stan łączenia można również wyświetlić w bloku **Przegląd** dla podstawowej i pomocniczej pamięci podręcznej.

    ![Zrzut ekranu przedstawiający sposób wyświetlania stanu łączenia dla podstawowej i pomocniczej pamięci podręcznej.](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Po zakończeniu procesu replikacji stan **linku zmieni** się na **Powodzenie.**

    ![Stan pamięci podręcznej](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    Podstawowa połączona pamięć podręczna pozostaje dostępna do użycia podczas procesu łączenia. Pomocnicza połączona pamięć podręczna nie jest dostępna do momentu zakończenia procesu łączenia.

## <a name="remove-a-geo-replication-link"></a>Usuwanie linku replikacji geograficznej

1. Aby usunąć połączenie między dwiema pamięciami podręcznych i zatrzymać replikację geograficzną, kliknij pozycję Odłącz pamięci **podręczne** w bloku **Replikacja geograficzna.**
    
    ![Odłączanie pamięci podręcznych](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Po zakończeniu procesu odłączania pomocnicza pamięć podręczna jest dostępna zarówno dla odczytu, jak i zapisu.

>[!NOTE]
>Po usunięciu łącza replikacji geograficznej replikowane dane z podstawowej połączonej pamięci podręcznej pozostają w pomocniczej pamięci podręcznej.
>
>

## <a name="geo-replication-faq"></a>Replikacja geograficzna — często zadawane pytania

- [Czy można używać replikacji geograficznej z pamięcią podręczną w warstwie Standardowa lub Podstawowa?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [Czy moja pamięć podręczna jest dostępna do użycia podczas procesu łączenia lub odłączania?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Czy można połączyć ze sobą więcej niż dwie pamięci podręczne?](#can-i-link-more-than-two-caches-together)
- [Czy można połączyć dwie pamięci podręczne z różnych subskrypcji platformy Azure?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Czy można połączyć dwie pamięci podręczne o różnych rozmiarach?](#can-i-link-two-caches-with-different-sizes)
- [Czy można używać replikacji geograficznej z włączonym klastrem?](#can-i-use-geo-replication-with-clustering-enabled)
- [Czy można używać replikacji geograficznej z pamięciami podręcznych w sieci wirtualnej?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Jaki jest harmonogram replikacji dla replikacji geograficznej usługi Redis?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Jak długo trwa replikacja geograficzna?](#how-long-does-geo-replication-replication-take)
- [Czy punkt odzyskiwania replikacji jest gwarantowany?](#is-the-replication-recovery-point-guaranteed)
- [Czy można zarządzać replikacją geograficzną za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Ile kosztuje replikowanie danych między regionami świadczenia usługi Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Dlaczego operacja nie powiodła się podczas próby usunięcia połączonej pamięci podręcznej?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Jakiego regionu należy użyć dla dodatkowej połączonej pamięci podręcznej?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Jak działa tryb pracy w trybie pracy w dodatkowej połączonej pamięci podręcznej](#how-does-failing-over-to-the-secondary-linked-cache-work)
- [Czy można skonfigurować zaporę z replikacją geograficzną?](#can-i-configure-a-firewall-with-geo-replication)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Czy można używać replikacji geograficznej z pamięcią podręczną w warstwie Standardowa lub Podstawowa?

Nie, replikacja geograficzna jest dostępna tylko dla pamięci podręcznych w warstwie Premium.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>Czy moja pamięć podręczna jest dostępna do użycia podczas procesu łączenia lub odłączania?

- Podczas łączenia podstawowa połączona pamięć podręczna pozostaje dostępna po zakończeniu procesu łączenia.
- Podczas łączenia dodatkowa połączona pamięć podręczna nie jest dostępna, dopóki proces łączenia nie zostanie ukończony.
- Podczas odłączania obie pamięci podręczne pozostają dostępne po zakończeniu procesu odłączania.

### <a name="can-i-link-more-than-two-caches-together"></a>Czy mogę połączyć ze sobą więcej niż dwie pamięci podręczne?

Nie, można połączyć ze sobą tylko dwie pamięci podręczne.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Czy można połączyć dwie pamięci podręczne z różnych subskrypcji platformy Azure?

Nie, obie pamięci podręczne muszą znajdować się w tej samej subskrypcji platformy Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Czy można połączyć dwie pamięci podręczne o różnych rozmiarach?

Tak, o ile pomocnicza połączona pamięć podręczna jest większa niż podstawowa połączona pamięć podręczna.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Czy można używać replikacji geograficznej z włączonym klastrem?

Tak, o ile obie pamięci podręczne mają taką samą liczbę fragmentów.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Czy można używać replikacji geograficznej z pamięciami podręcznych w sieci wirtualnej?

Tak, replikacja geograficzna pamięci podręcznych w sieciach wirtualnych jest obsługiwana z zastrzeżeniami:

- Replikacja geograficzna między pamięciami podręcznych w tej samej sieci wirtualnej jest obsługiwana.
- Obsługiwana jest również replikacja geograficzna między pamięciami podręcznymi w różnych sieciach wirtualnych.
  - Jeśli sieci wirtualne znajdują się w tym samym [](../virtual-network/virtual-network-peering-overview.md) regionie, możesz połączyć je za pomocą komunikacji równorzędnej sieci wirtualnych lub połączenia VPN Gateway między sieciami [wirtualnymi.](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
  - Jeśli sieci wirtualne znajdują się w różnych regionach, replikacja geograficzna korzystająca z komunikacji równorzędnej sieci wirtualnej jest obsługiwana, ale maszyna wirtualna klienta w sieci VNET 1 (region 1) nie będzie mogła uzyskać dostępu do pamięci podręcznej w sieci wirtualnej 2 (region 2) za pośrednictwem nazwy DNS ze względu na ograniczenie dotyczące wewnętrznych usług równoważenia obciążenia w witrynie Podstawowa. Aby uzyskać więcej informacji na temat ograniczeń komunikacji równorzędnej sieci wirtualnych, [zobacz Virtual Network — Komunikacja równorzędna — wymagania i ograniczenia.](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) Zalecanym rozwiązaniem jest użycie połączenia VPN Gateway sieć wirtualna-sieć wirtualna.
  
Za [pomocą tego szablonu](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/)platformy Azure można szybko wdrożyć dwie pamięci podręczne z replikacją geograficzną w sieci wirtualnej połączonej za pomocą połączenia VPN Gateway sieć wirtualna-sieć wirtualna.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Jaki jest harmonogram replikacji dla replikacji geograficznej usługi Redis?

Replikacja jest ciągła i asynchroniczna i nie odbywa się zgodnie z określonym harmonogramem. Wszystkie operacje zapisu wykonywane w podstawowej są natychmiastowo i asynchronicznie replikowane w pomocniczym.

### <a name="how-long-does-geo-replication-replication-take"></a>Jak długo trwa replikacja geograficzna?

Replikacja jest przyrostowa, asynchroniczna i ciągła, a czas nie różni się znacznie od opóźnienia w różnych regionach. W pewnych okolicznościach dodatkowa pamięć podręczna może być wymagana do pełnej synchronizacji danych z podstawowej pamięci podręcznej. Czas replikacji w tym przypadku zależy od wielu czynników, takich jak obciążenie podstawowej pamięci podręcznej, dostępna przepustowość sieci i opóźnienie między regionami. Odkryliśmy, że czas replikacji pełnej pary z replikacją geograficzną o pojemności 53 GB może być w dowolnym miejscu od 5 do 10 minut.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Czy punkt odzyskiwania replikacji jest gwarantowany?

W przypadku pamięci podręcznych w trybie replikacji geograficznej trwałość jest wyłączona. Jeśli para z replikacją geograficzną jest odwiązywała połączenie, na przykład w przypadku zainicjowanego przez klienta trybu failover, pomocnicza połączona pamięć podręczna przechowuje zsynchronizowane dane do tego momentu. W takich sytuacjach żaden punkt odzyskiwania nie jest gwarantowany.

Aby uzyskać punkt odzyskiwania, [wyeksportuj z](cache-how-to-import-export-data.md#export) jednej z pamięci podręcznej. Później możesz [zaimportować dane](cache-how-to-import-export-data.md#import) do podstawowej połączonej pamięci podręcznej.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Czy można zarządzać replikacją geograficzną za pomocą programu PowerShell lub interfejsu wiersza polecenia platformy Azure?

Tak, replikacją geograficzną można zarządzać przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz dokumenty [dotyczące programu PowerShell lub](/powershell/module/az.rediscache/#redis_cache) interfejsu wiersza polecenia platformy [Azure](/cli/azure/redis/server-link).

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Ile kosztuje replikowanie danych między regionami świadczenia usługi Azure?

W przypadku korzystania z replikacji geograficznej dane z podstawowej połączonej pamięci podręcznej są replikowane do dodatkowej połączonej pamięci podręcznej. Transfer danych jest bezpłatny, jeśli dwie połączone pamięci podręczne znajdują się w tym samym regionie. Jeśli dwie połączone pamięci podręczne znajdują się w różnych regionach, opłata za transfer danych jest kosztem ruchu wychodzącego sieciowego danych przenoszących się przez oba regiony. Aby uzyskać więcej informacji, zobacz [Szczegóły cennika przepustowości.](https://azure.microsoft.com/pricing/details/bandwidth/)

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Dlaczego operacja nie powiodła się podczas próby usunięcia połączonej pamięci podręcznej?

Nie można usunąć zreplikowanych geograficznie pamięci podręcznych i ich grup zasobów, dopóki nie usuniesz linku replikacji geograficznej. Jeśli spróbujemy usunąć grupę zasobów zawierającą jedną lub obie połączone pamięci podręczne, pozostałe zasoby w grupie zasobów zostaną usunięte, ale grupa zasobów pozostanie w stanie i wszystkie połączone pamięci podręczne w grupie zasobów pozostaną w stanie `deleting` `running` . Aby całkowicie usunąć grupę zasobów i połączone pamięci podręczne w tej grupie, odłącz pamięci podręczne zgodnie z opisem w tece [Usuwanie linku replikacji geograficznej](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Jakiego regionu należy użyć dla dodatkowej połączonej pamięci podręcznej?

Ogólnie rzecz biorąc, zaleca się, aby pamięć podręczna istniała w tym samym regionie świadczenia usługi Azure, co aplikacja, która uzyskuje do niego dostęp. W przypadku aplikacji z oddzielnymi regionami podstawowymi i rezerwowych zaleca się, aby podstawowa i pomocnicza pamięć podręczna istniała w tych samych regionach. Aby uzyskać więcej informacji na temat sparowanych regionów, zobacz [Best Practices – Azure Paired regions (Najlepsze rozwiązania — regiony sparowane platformy Azure).](../best-practices-availability-paired-regions.md)

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Jak działa tryb pracy w trybie pracy w dodatkowej połączone

Automatyczne tryb failover między regionami świadczenia usługi Azure nie jest obsługiwane w przypadku pamięci podręcznych replikowanych geograficznie. W scenariuszu odzyskiwania po awarii klienci powinni w sposób skoordynowany tworzyć stos aplikacji w swoim regionie kopii zapasowych. Umożliwienie poszczególnym składnikom aplikacji decydowania o tym, kiedy samodzielnie przełączyć się na kopie zapasowe, może mieć negatywny wpływ na wydajność. Jedną z kluczowych zalet redis jest to, że jest to magazyn o bardzo małych opóźnieniach. Jeśli główna aplikacja klienta znajduje się w innym regionie niż jego pamięć podręczna, dodatkowy czas rundy będzie miał zauważalny wpływ na wydajność. Z tego powodu unikamy automatycznego awarii z powodu przejściowych problemów z dostępnością.

Aby rozpocząć tryb failover zainicjowany przez klienta, najpierw odłącz pamięci podręczne. Następnie zmień klienta usługi Redis, aby używać punktu końcowego połączenia pomocniczej pamięci podręcznej (wcześniej połączonej). Gdy dwie pamięci podręczne są odłączone, pomocnicza pamięć podręczna ponownie staje się zwykłą pamięcią podręczną odczytu i zapisu i akceptuje żądania bezpośrednio od klientów redis.

### <a name="can-i-configure-a-firewall-with-geo-replication"></a>Czy można skonfigurować zaporę z replikacją geograficzną?

Tak, można skonfigurować [zaporę z replikacją](./cache-configure.md#firewall) geograficzną. Aby replikacja geograficzna działała obok zapory, upewnij się, że adres IP pomocniczej pamięci podręcznej został dodany do reguł zapory podstawowej pamięci podręcznej.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o Azure Cache for Redis funkcji.

* [Azure Cache for Redis usług](cache-overview.md#service-tiers)
* [Wysoka dostępność dla Azure Cache for Redis](cache-high-availability.md)
