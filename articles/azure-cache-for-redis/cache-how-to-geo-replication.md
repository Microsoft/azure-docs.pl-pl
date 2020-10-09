---
title: Jak skonfigurować replikację geograficzną dla usługi Azure cache for Redis | Microsoft Docs
description: Dowiedz się, jak replikować pamięć podręczną platformy Azure dla wystąpień Redis w różnych regionach geograficznych.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: yegu
ms.openlocfilehash: f6ac02f0bcd9becf5dd1ffcd600f78b848b47cda
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91839694"
---
# <a name="how-to-set-up-geo-replication-for-azure-cache-for-redis"></a>Jak skonfigurować replikację geograficzną dla usługi Azure cache for Redis

Replikacja geograficzna zapewnia mechanizm łączenia dwóch pamięci podręcznej platformy Azure w warstwie Premium dla wystąpień Redis. Jedna pamięć podręczna jest wybierana jako podstawowa połączona pamięć podręczna, a druga jako pomocnicza połączonej pamięci podręcznej. Pomocnicza połączonej pamięci podręcznej jest tylko do odczytu, a dane zapisywane w podstawowej pamięci podręcznej są replikowane do pomocniczej połączonej pamięci podręcznej. Transfer danych między podstawowym i pomocniczym wystąpieniem pamięci podręcznej jest zabezpieczony przy użyciu protokołu TLS. Replikacja geograficzna może służyć do konfigurowania pamięci podręcznej obejmującej dwa regiony platformy Azure. Ten artykuł zawiera Przewodnik konfigurowania replikacji geograficznej dla usługi Azure cache w warstwie Premium dla wystąpień Redis.

> [!NOTE]
> Replikacja geograficzna jest zaprojektowana jako rozwiązanie odzyskiwania po awarii. Domyślnie aplikacja zostanie zapisana w regionie podstawowym i odczytana z niej. Opcjonalnie można ją skonfigurować do odczytu z regionu pomocniczego. Replikacja geograficzna nie zapewnia automatycznej pracy awaryjnej ze względu na problemy z dodanym opóźnieniem sieci między regionami, jeśli pozostała część aplikacji pozostaje w regionie podstawowym. Należy zarządzać i inicjować tryb failover przez odłączenie pomocniczej pamięci podręcznej. Spowoduje to podwyższenie poziomu IT do nowego wystąpienia podstawowego.

## <a name="geo-replication-prerequisites"></a>Wymagania wstępne dotyczące replikacji geograficznej

Aby skonfigurować replikację geograficzną między dwiema pamięciami podręcznymi, należy spełnić następujące wymagania wstępne:

- Obie pamięci podręczne są pamięcią podręczną [warstwy Premium](cache-overview.md#service-tiers) .
- Obie pamięci podręczne są w tej samej subskrypcji platformy Azure.
- Pomocnicza połączony bufor ma ten sam rozmiar pamięci podręcznej lub większy rozmiar pamięci podręcznej niż w przypadku podstawowej połączonej pamięci podręcznej.
- Obie pamięci podręczne są tworzone i w stanie uruchomienia.

Niektóre funkcje nie są obsługiwane w przypadku replikacji geograficznej:

- Trwałość nie jest obsługiwana w przypadku replikacji geograficznej.
- Klastrowanie jest obsługiwane, jeśli obie pamięci podręczne mają włączoną funkcję klastrowania i mają tę samą liczbę fragmentów.
- Obsługiwane są pamięci podręczne w tej samej sieci wirtualnej.
- Pamięć podręczna w różnych sieci wirtualnychach jest obsługiwana z zastrzeżeniem. Zobacz [, czy można użyć replikacji geograficznej z moją pamięcią podręczną w sieci wirtualnej?](#can-i-use-geo-replication-with-my-caches-in-a-vnet) Aby uzyskać więcej informacji.

Po skonfigurowaniu replikacji geograficznej, następujące ograniczenia mają zastosowanie do pary połączonej pamięci podręcznej:

- Pomocnicza połączonej pamięci podręcznej jest tylko do odczytu; można z niego odczytywać, ale nie można zapisać w nim żadnych danych. 
- Wszystkie dane znajdujące się w pomocniczej połączonej pamięci podręcznej przed dodaniem linku zostaną usunięte. W przypadku późniejszego usunięcia replikacji geograficznej zreplikowane dane pozostają w pomocniczej połączonej pamięci podręcznej.
- Nie można [skalować](cache-how-to-scale.md) żadnej pamięci podręcznej podczas łączenia pamięci podręcznych.
- Nie można [zmienić liczby fragmentów](cache-how-to-premium-clustering.md) , jeśli pamięć podręczna ma włączone klastrowanie.
- Nie można włączyć trwałości w żadnej pamięci podręcznej.
- Możesz [wyeksportować](cache-how-to-import-export-data.md#export) z jednej pamięci podręcznej.
- Nie można [zaimportować](cache-how-to-import-export-data.md#import) do pomocniczej połączonej pamięci podręcznej.
- Nie można usunąć połączonej pamięci podręcznej lub grupy zasobów, która je zawiera, do momentu odłączenia pamięci podręcznych. Aby uzyskać więcej informacji, zobacz [dlaczego operacja kończy się niepowodzeniem podczas próby usunięcia połączonej pamięci podręcznej?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- Jeśli pamięć podręczna znajduje się w różnych regionach, koszty ruchu wychodzącego w sieci są stosowane do danych przenoszonych między regionami. Aby uzyskać więcej informacji, zobacz, [jak wiele kosztów może replikować dane w regionach platformy Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- Automatyczne przełączanie do trybu failover nie występuje między podstawową i pomocniczą połączoną pamięcią podręczną. Aby uzyskać więcej informacji i dowiedzieć się, jak przełączyć się do trybu failover w aplikacji klienckiej, zobacz Jak działa przechodzenie awaryjne [do pomocniczej połączonej pamięci podręcznej?](#how-does-failing-over-to-the-secondary-linked-cache-work)

## <a name="add-a-geo-replication-link"></a>Dodawanie linku replikacji geograficznej

1. Aby połączyć dwie pamięci podręczne na potrzeby replikacji geograficznej, Fist kliknij pozycję **replikacja geograficzna** z menu zasób pamięci podręcznej, która ma być podstawową, połączoną pamięcią podręczną. Następnie kliknij **link Dodaj replikację pamięci podręcznej** z bloku **replikacja geograficzna** .

    ![Dodaj łącze](./media/cache-how-to-geo-replication/cache-geo-location-menu.png)

2. Kliknij nazwę zamierzonej pomocniczej pamięci podręcznej z listy **zgodnych pamięci podręcznych** . Jeśli pomocnicza pamięć podręczna nie jest wyświetlana na liście, sprawdź, czy zostały spełnione [wymagania wstępne dotyczące replikacji geograficznej](#geo-replication-prerequisites) dla pomocniczej pamięci podręcznej. Aby filtrować pamięci podręczne według regionów, kliknij region na mapie, aby wyświetlić tylko te pamięci podręczne na liście **zgodnych pamięci podręcznych** .

    ![Pamięci podręczne zgodne z replikacją geograficzną](./media/cache-how-to-geo-replication/cache-geo-location-select-link.png)
    
    Możesz również uruchomić proces łączenia lub wyświetlić szczegóły pomocniczej pamięci podręcznej za pomocą menu kontekstowego.

    ![Menu kontekstowe replikacji geograficznej](./media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png)

3. Kliknij **link** , aby połączyć dwie pamięci podręczne i rozpocząć proces replikacji.

    ![Buforuj linki](./media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png)

4. Postęp procesu replikacji można wyświetlić w bloku **replikacji geograficznej** .

    ![Stan konsolidacji](./media/cache-how-to-geo-replication/cache-geo-location-linking.png)

    Możesz również wyświetlić stan połączenia w bloku **Przegląd** dla podstawowych i pomocniczych pamięci podręcznych.

    ![Zrzut ekranu przedstawiający sposób wyświetlania stanu konsolidacji dla podstawowych i pomocniczych pamięci podręcznych.](./media/cache-how-to-geo-replication/cache-geo-location-link-status.png)

    Po zakończeniu procesu replikacji **Stan łącza** zmieni się na **powodzenie**.

    ![Stan pamięci podręcznej](./media/cache-how-to-geo-replication/cache-geo-location-link-successful.png)

    Podstawowa połączona pamięć podręczna pozostaje dostępna do użycia podczas procesu łączenia. Pomocnicza połączona pamięć podręczna jest niedostępna, dopóki proces łączenia nie zostanie ukończony.

## <a name="remove-a-geo-replication-link"></a>Usuwanie linku replikacji geograficznej

1. Aby usunąć łącze między dwiema pamięciami podręcznymi i zatrzymać replikację geograficzną, kliknij opcję **Odłącz pamięć podręczną** od bloku **replikacji geograficznej** .
    
    ![Odłącz pamięć podręczną](./media/cache-how-to-geo-replication/cache-geo-location-unlink.png)

    Po zakończeniu procesu odłączania pomocnicza pamięć podręczna jest dostępna zarówno do odczytu, jak i zapisu.

>[!NOTE]
>Po usunięciu łącza replikacji geograficznej zreplikowane dane z podstawowej połączonej pamięci podręcznej pozostają w pomocniczej pamięci podręcznej.
>
>

## <a name="geo-replication-faq"></a>Replikacja geograficzna — często zadawane pytania

- [Czy można używać replikacji geograficznej z pamięcią podręczną warstwy Standardowa lub podstawowa?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [Czy moja pamięć podręczna jest dostępna do użycia podczas procesu łączenia lub rozłączania?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [Czy mogę połączyć więcej niż dwie pamięć podręczną?](#can-i-link-more-than-two-caches-together)
- [Czy mogę połączyć dwie pamięci podręczne z różnych subskrypcji platformy Azure?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [Czy mogę połączyć dwie pamięci podręczne o różnych rozmiarach?](#can-i-link-two-caches-with-different-sizes)
- [Czy można używać replikacji geograficznej z włączoną obsługą klastrowania?](#can-i-use-geo-replication-with-clustering-enabled)
- [Czy można używać replikacji geograficznej z moją pamięcią podręczną w sieci wirtualnej?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Jaki jest harmonogram replikacji Redis georeplikacji?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [Jak długo trwa replikacja geograficzna?](#how-long-does-geo-replication-replication-take)
- [Czy punkt odzyskiwania replikacji jest gwarantowany?](#is-the-replication-recovery-point-guaranteed)
- [Czy można użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure do zarządzania replikacją geograficzną?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Ile kosztuje, aby replikować dane w regionach platformy Azure?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [Dlaczego operacja kończy się niepowodzeniem podczas próby usunięcia połączonej pamięci podręcznej?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [Jakiego regionu należy używać dla mojej pomocniczej połączonej pamięci podręcznej?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [Jak działa przechodzenie w tryb failover do pomocniczej połączonej pamięci podręcznej?](#how-does-failing-over-to-the-secondary-linked-cache-work)
- [Czy można skonfigurować zaporę z replikacją geograficzną?](#can-i-configure-a-firewall-with-geo-replication)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>Czy można używać replikacji geograficznej z pamięcią podręczną warstwy Standardowa lub podstawowa?

Nie, replikacja geograficzna jest dostępna tylko w przypadku pamięci podręcznych warstwy Premium.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>Czy moja pamięć podręczna jest dostępna do użycia podczas procesu łączenia lub rozłączania?

- Podczas łączenia, podstawowa połączona pamięć podręczna pozostaje dostępna podczas kończenia procesu łączenia.
- Podczas łączenia pomocnicza połączonej pamięci podręcznej nie jest dostępna do momentu zakończenia procesu łączenia.
- Podczas odłączania obie pamięci podręczne pozostają dostępne po zakończeniu procesu odłączania.

### <a name="can-i-link-more-than-two-caches-together"></a>Czy mogę połączyć więcej niż dwie pamięć podręczną?

Nie, można połączyć tylko dwie pamięci podręczne.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>Czy mogę połączyć dwie pamięci podręczne z różnych subskrypcji platformy Azure?

Nie, obie pamięci podręczne muszą znajdować się w tej samej subskrypcji platformy Azure.

### <a name="can-i-link-two-caches-with-different-sizes"></a>Czy mogę połączyć dwie pamięci podręczne o różnych rozmiarach?

Tak, pod warunkiem, że pomocnicza połączonej pamięci podręcznej jest większa niż podstawowa połączona pamięć podręczna.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>Czy można używać replikacji geograficznej z włączoną obsługą klastrowania?

Tak, o ile obie pamięci podręczne mają tę samą liczbę fragmentów.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>Czy można używać replikacji geograficznej z moją pamięcią podręczną w sieci wirtualnej?

Tak, replikacja geograficzna pamięci podręcznych w sieci wirtualnych jest obsługiwana z zastrzeżeniem:

- Replikacja geograficzna między pamięciami podręcznymi w tej samej sieci wirtualnej jest obsługiwana.
- Obsługiwane są również replikacje geograficzne między pamięciami podręcznymi w różnych sieci wirtualnych.
  - Jeśli sieci wirtualnych znajdują się w tym samym regionie, można połączyć je za pomocą [komunikacji równorzędnej sieci wirtualnej](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) lub [VPN Gateway połączenia](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#V2V)między siećmi wirtualnymi.
  - Jeśli sieci wirtualnych znajdują się w różnych regionach, jest obsługiwana replikacja geograficzna używająca komunikacji równorzędnej sieci wirtualnych, ale maszyna wirtualna w sieci wirtualnej 1 (region 1) nie będzie w stanie uzyskać dostępu do pamięci podręcznej w sieci wirtualnej 2 (region 2) za pomocą nazwy DNS z powodu ograniczenia dotyczącego podstawowych wewnętrznych modułów równoważenia obciążenia. Aby uzyskać więcej informacji na temat ograniczeń wirtualnych sieci równorzędnych, zobacz [Virtual Network-Komunikacja równorzędna — wymagania i ograniczenia](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering#requirements-and-constraints). Zalecanym rozwiązaniem jest użycie VPN Gateway połączenia między sieciami WIRTUALNYmi.
  
Korzystając z [tego szablonu platformy Azure](https://azure.microsoft.com/resources/templates/201-redis-vnet-geo-replication/), można szybko wdrożyć dwie buforowane geograficznie podsieci w sieci wirtualnej połączonej z VPN Gateway połączeniem między sieciami wirtualnymi.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Jaki jest harmonogram replikacji Redis georeplikacji?

Replikacja jest ciągła i asynchroniczna i nie odbywa się zgodnie z określonym harmonogramem. Wszystkie zapisy wykonywane do podstawowego są natychmiast i asynchroniczne zreplikowane na pomocniczej.

### <a name="how-long-does-geo-replication-replication-take"></a>Jak długo trwa replikacja geograficzna?

Replikacja jest przyrostowa, asynchroniczna i ciągła, a czas trwania nie jest znacznie różny od opóźnienia w różnych regionach. W pewnych okolicznościach pomocnicza pamięć podręczna może być wymagana do wykonania pełnej synchronizacji danych z podstawowego. Czas replikacji w tym przypadku zależy od liczby takich czynników, jak: obciążenia podstawowej pamięci podręcznej, dostępnej przepustowości sieci i opóźnienia między regionami. Znaleźliśmy czas replikacji dla pełnej pary replikowanych geograficznie 53 GB, która może znajdować się w dowolnym miejscu w zakresie od 5 do 10 minut.

### <a name="is-the-replication-recovery-point-guaranteed"></a>Czy punkt odzyskiwania replikacji jest gwarantowany?

W przypadku pamięci podręcznej w trybie replikowanym geograficznie trwałość jest wyłączona. Jeśli para z replikacją geograficzną jest odłączona, na przykład w trybie failover inicjowanym przez klienta, pomocnicza połączonej pamięci podręcznej utrzymuje zsynchronizowane dane do tego momentu. W takich sytuacjach nie jest gwarantowany żaden punkt odzyskiwania.

Aby uzyskać punkt odzyskiwania, należy [wyeksportować](cache-how-to-import-export-data.md#export) z jednej pamięci podręcznej. Później można [zaimportować](cache-how-to-import-export-data.md#import) do podstawowej połączonej pamięci podręcznej.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>Czy można użyć programu PowerShell lub interfejsu wiersza polecenia platformy Azure do zarządzania replikacją geograficzną?

Tak. replikację geograficzną można zarządzać przy użyciu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji, zobacz Dokumentacja programu [PowerShell](https://docs.microsoft.com/powershell/module/az.rediscache/?view=azps-1.4.0#redis_cache) lub [Dokumentacja interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/redis/server-link?view=azure-cli-latest).

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Ile kosztuje, aby replikować dane w regionach platformy Azure?

W przypadku korzystania z replikacji geograficznej dane z podstawowej połączonej pamięci podręcznej są replikowane do pomocniczej połączonej pamięci podręcznej. Nie jest naliczana opłata za transfer danych, jeśli dwie połączone pamięci podręczne znajdują się w tym samym regionie. Jeśli dwie połączone pamięci podręczne znajdują się w różnych regionach, opłata za transfer danych jest kosztem wychodzącym przez sieć danych przenoszonych w dowolnym regionie. Aby uzyskać więcej informacji, zobacz [szczegóły cennika](https://azure.microsoft.com/pricing/details/bandwidth/)dotyczącego przepustowości.

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>Dlaczego operacja kończy się niepowodzeniem podczas próby usunięcia połączonej pamięci podręcznej?

Nie można usunąć pamięci podręcznej replikowanych geograficznie i ich grup zasobów, dopóki nie zostanie usunięte łącze replikacji geograficznej. Jeśli spróbujesz usunąć grupę zasobów zawierającą jedną lub obie połączone pamięci podręczne, inne zasoby w grupie zasobów zostaną usunięte, ale Grupa zasobów pozostanie w `deleting` stanie i wszystkie połączone pamięci podręczne w grupie zasobów pozostaną w `running` stanie. Aby całkowicie usunąć grupę zasobów i połączone pamięci podręczne, należy odłączyć pamięci podręczne zgodnie z opisem w temacie [Usuwanie łącza replikacji geograficznej](#remove-a-geo-replication-link).

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>Jakiego regionu należy używać dla mojej pomocniczej połączonej pamięci podręcznej?

Ogólnie rzecz biorąc, zaleca się, aby pamięć podręczna istniała w tym samym regionie świadczenia usługi Azure co aplikacja, która uzyskuje do niej dostęp. W przypadku aplikacji z oddzielnymi regionami podstawowymi i rezerwowymi zaleca się używanie podstawowych i pomocniczych pamięci podręcznych w tych samych regionach. Aby uzyskać więcej informacji na temat par regionów, zobacz [najlepsze rozwiązania — regiony sparowane na platformie Azure](../best-practices-availability-paired-regions.md).

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>Jak działa przechodzenie w tryb failover do pomocniczej połączonej pamięci podręcznej?

Automatyczne przełączanie w tryb failover w regionach platformy Azure nie jest obsługiwane w przypadku pamięci podręcznych replikowanych geograficznie. W scenariuszu odzyskiwania po awarii klienci powinni utworzyć cały stos aplikacji w skoordynowany sposób w ich regionie tworzenia kopii zapasowych. Umożliwienie pojedynczym składnikom aplikacji decydowanie o tym, kiedy przełączyć się na ich kopie zapasowe, może mieć negatywny wpływ na wydajność. Jedną z najważniejszych zalet Redis jest to, że jest to bardzo niski czas przechowywania. Jeśli główna aplikacja klienta znajduje się w innym regionie niż jej pamięć podręczna, dodanie czasu rundy mogą mieć zauważalny wpływ na wydajność. Z tego powodu unikamy automatycznego przełączania awaryjnego z powodu przejściowych problemów z dostępnością.

Aby uruchomić tryb failover zainicjowany przez klienta, najpierw Odłącz pamięci podręczne. Następnie zmień klienta programu Redis tak, aby używał punktu końcowego połączenia (dawniej połączony) pomocniczej pamięci podręcznej. Po odłączeniu dwóch pamięci podręcznych pomocnicza pamięć podręczna jest ponownie regularna w pamięci podręcznej odczytu i zapisu oraz akceptuje żądania bezpośrednio od klientów Redis.

### <a name="can-i-configure-a-firewall-with-geo-replication"></a>Czy można skonfigurować zaporę z replikacją geograficzną?

Tak, można skonfigurować [zaporę](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-configure#firewall) z replikacją geograficzną. Aby można było obsłużyć replikację geograficzną obok zapory, należy się upewnić, że adres IP pomocniczej pamięci podręcznej jest dodawany do reguł zapory podstawowej pamięci podręcznej.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o funkcjach usługi Azure cache for Redis.

* [Pamięć podręczna platformy Azure dla warstw usług Redis](cache-overview.md#service-tiers)
