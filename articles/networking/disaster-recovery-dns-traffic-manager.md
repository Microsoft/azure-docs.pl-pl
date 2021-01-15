---
title: Odzyskiwanie po awarii przy użyciu Azure DNS i Traffic Manager | Microsoft Docs
description: Przegląd rozwiązań odzyskiwania po awarii przy użyciu Azure DNS i Traffic Manager.
services: dns
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/08/2018
ms.author: kumud
ms.openlocfilehash: 8cb1a490ac8edf2630253b45d99c3394bbe721b8
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234158"
---
# <a name="disaster-recovery-using-azure-dns-and-traffic-manager"></a>Odzyskiwanie po awarii przy użyciu usług Azure DNS i Traffic Manager

Odzyskiwanie po awarii powinno być skoncentrowane na odzyskiwaniu po poważnej utracie funkcjonalności aplikacji. W celu wybrania rozwiązania do odzyskiwania po awarii, właściciele biznesowy i technologiczny muszą najpierw określić poziom funkcjonalności, który jest wymagany w przypadku awarii, na przykład-niedostępne, częściowo dostępne za pośrednictwem ograniczonej funkcjonalności lub opóźnionej dostępności lub w pełni dostępny.
Większość klientów korporacyjnych wybiera architekturę wieloregionową w celu odporności na tryb failover aplikacji lub poziomu infrastruktury. Klienci mogą wybrać kilka metod w wezwaniu, aby osiągnąć tryb failover i wysoką dostępność za pośrednictwem nadmiarowej architektury. Oto niektóre z popularnych metod:

- **Aktywny-pasywny z zimnem w stanie wstrzymania**: w tym rozwiązaniu trybu failover maszyny wirtualne i inne urządzenia działające w regionie wstrzymania nie są aktywne do momentu potrzeby przejścia w tryb failover. Jednak środowisko produkcyjne jest replikowane w formie kopii zapasowych, obrazów maszyn wirtualnych lub szablonów Menedżer zasobów w innym regionie. Ten mechanizm pracy awaryjnej jest ekonomiczny, ale trwa dłużej, aby przeprowadzić pełną pracę w trybie failover.
 
    ![Aktywne/pasywne z gotowością zimną](./media/disaster-recovery-dns-traffic-manager/active-passive-with-cold-standby.png)
    
    *Rysunek — aktywny/pasywny z konfiguracją odzyskiwania po awarii w stanie gotowości*

- **Aktywne/pasywne z lampą pilotażową**: w tym rozwiązaniu trybu failover środowisko jest skonfigurowane z konfiguracją minimalną. Instalator ma tylko wymagane usługi, aby obsługiwał tylko minimalny i krytyczny zestaw aplikacji. W jego postaci natywnej w tym scenariuszu można wykonać tylko minimalną funkcjonalność, ale można skalować w górę i zduplikować dodatkowe usługi, aby wykonać obciążenie produkcyjne w przypadku wystąpienia przejścia w tryb failover.
    
    ![Aktywne/pasywne z lampą pilotażową](./media/disaster-recovery-dns-traffic-manager/active-passive-with-pilot-light.png)
    
    *Rysunek: aktywny/pasywny z konfiguracją odzyskiwania po awarii pilotażu*

- **Aktywny/pasywny z rezerwą bezczynną**: w tym rozwiązaniu trybu failover region gotowości jest wstępnie rozgrzany i jest gotowy do założenia obciążenia podstawowego, automatyczne skalowanie jest włączone, a wszystkie wystąpienia są uruchomione. To rozwiązanie nie jest skalowane w celu przejęcia pełnego obciążenia produkcyjnego, ale jest funkcjonalne, a wszystkie usługi są uruchomione. To rozwiązanie jest rozszerzonym sposobem podejścia pilotażowego.
    
    ![Aktywny/pasywny ze stanem rezerwy ciepłej](./media/disaster-recovery-dns-traffic-manager/active-passive-with-warm-standby.png)
    
    *Rysunek: aktywne/pasywne z konfiguracją odzyskiwania po awarii w stanie rezerwowym*
    
Aby dowiedzieć się więcej o trybie failover i wysokiej dostępności, zobacz [odzyskiwanie po awarii dla aplikacji platformy Azure](/azure/architecture/resiliency/disaster-recovery-azure-applications).


## <a name="planning-your-disaster-recovery-architecture"></a>Planowanie architektury odzyskiwania po awarii

Istnieją dwa aspekty techniczne dotyczące konfigurowania architektury odzyskiwania po awarii:
-  Używanie mechanizmu wdrażania do replikowania wystąpień, danych i konfiguracji między środowiskami podstawowymi i w stanie gotowości. Ten typ odzyskiwania po awarii można przeprowadzić natywnie za pośrednictwem usługi Azure Site-Recovery za pośrednictwem Microsoft Azure urządzeń/usług partnerskich, takich jak Veritas lub NetApp. 
- Tworzenie rozwiązania do przekierowywania ruchu sieciowego/sieci Web z lokacji głównej do lokacji w stanie wstrzymania. Ten typ odzyskiwania po awarii można osiągnąć za pośrednictwem Azure DNS, Azure Traffic Manager (DNS) lub globalnych modułów równoważenia obciążenia innych firm.

Ten artykuł jest ograniczony do podejścia za pośrednictwem przekierowywania ruchu sieciowego i sieci Web. Aby uzyskać instrukcje dotyczące konfigurowania Azure Site Recovery, zobacz [dokumentację usługi Azure Site Recovery](../site-recovery/index.yml).
System DNS to jeden z najbardziej wydajnych mechanizmów służących do przekierowywania ruchu sieciowego, ponieważ usługa DNS jest często globalna i zewnętrzna w centrum danych i jest izolowana ze względu na awarie poziomu strefy regionalnej lub dostępności. Jeden z nich może korzystać z mechanizmu trybu failover opartego na systemie DNS i na platformie Azure, jednak dwie usługi DNS mogą wykonywać takie same w niektórych Azure DNS (autorytatywny serwer DNS) i platformie Azure Traffic Manager (usługa inteligentnego routingu opartego na systemie DNS). 

Ważne jest zrozumienie kilku koncepcji w systemie DNS, które są szeroko wykorzystywane do omówienia rozwiązań dostępnych w tym artykule:
- **Rekord a systemu DNS** — rekordy to wskaźniki wskazujące domenę na adres IPv4. 
- **Nazwa CNAME lub kanoniczna** — ten typ rekordu służy do wskazywania innego rekordu DNS. Rekord CNAME nie odpowiada adresowi IP, ale raczej wskaźnik do rekordu, który zawiera adres IP. 
- **Routing ważony** — jeden może zdecydować się na skojarzenie wag z punktami końcowymi usługi, a następnie rozpowszechnić ruch na podstawie przypisanych wag. Ta metoda routingu jest jednym z czterech mechanizmów routingu ruchu dostępnych w ramach Traffic Manager. Aby uzyskać więcej informacji, zobacz [ważone metody routingu](../traffic-manager/traffic-manager-routing-methods.md#weighted).
- **Routing priorytetu** — Routing priorytetów jest oparty na sprawdzaniu kondycji punktów końcowych. Domyślnie usługa Azure Traffic Manager wysyła cały ruch do punktu końcowego o najwyższym priorytecie i po awarii lub awarii Traffic Manager kieruje ruch do pomocniczego punktu końcowego. Aby uzyskać więcej informacji, zobacz [priorytetowa Metoda routingu](../traffic-manager/traffic-manager-routing-methods.md#priority-traffic-routing-method).

## <a name="manual-failover-using-azure-dns"></a>Ręczne przełączanie w tryb failover przy użyciu Azure DNS
Azure DNS ręczne rozwiązanie trybu failover do odzyskiwania po awarii używa standardowego mechanizmu DNS do przejścia w tryb failover do lokacji kopii zapasowej. Opcja ręczna Azure DNS działa najlepiej, gdy jest używana w połączeniu z zimną gotowość lub z podejściem do oświetlenia pilotażowego. 

![Ręczne przełączanie w tryb failover przy użyciu Azure DNS](./media/disaster-recovery-dns-traffic-manager/manual-failover-using-dns.png)

*Rysunek — ręczne przełączanie do trybu failover przy użyciu Azure DNS*

Założenia dotyczące rozwiązania są następujące:
- Podstawowy i pomocniczy punkt końcowy mają statyczne adresy IP, które często nie zmieniają się. Załóżmy, że dla lokacji głównej jest 100.168.124.44 IP, a adres IP dla lokacji dodatkowej to 100.168.124.43.
- Strefa Azure DNS istnieje zarówno dla lokacji głównej, jak i dodatkowej. Załóżmy, że dla lokacji głównej jest prod.contoso.com punkt końcowy, a dla lokacji kopii zapasowej jest dr.contoso.com. Istnieje również rekord DNS dla aplikacji głównej znanej jako \. contoso.com www.   
- Wartość czasu wygaśnięcia jest równa lub niższa od umowy SLA RTO ustawionej w organizacji. Na przykład, jeśli przedsiębiorstwo ustawi RTO odpowiedzi na awarię aplikacji na 60 minut, wartość TTL powinna być mniejsza niż 60 minut, najlepiej niższą. 
  Azure DNS ręcznego przełączania do trybu failover można skonfigurować w następujący sposób:
- Tworzenie strefy DNS
- Tworzenie rekordów strefy DNS
- Aktualizowanie rekordu CNAME

### <a name="step-1-create-a-dns"></a>Krok 1. Tworzenie systemu DNS
Utwórz strefę DNS (na przykład www \. contoso.com), jak pokazano poniżej:

![Tworzenie strefy DNS na platformie Azure](./media/disaster-recovery-dns-traffic-manager/create-dns-zone.png)

*Rysunek — tworzenie strefy DNS na platformie Azure*

### <a name="step-2-create-dns-zone-records"></a>Krok 2. tworzenie rekordów strefy DNS

W tej strefie Utwórz trzy rekordy (na przykład-www \. contoso.com, prod.contoso.com i Dr.consoto.com), jak pokazano poniżej.

![Tworzenie rekordów strefy DNS](./media/disaster-recovery-dns-traffic-manager/create-dns-zone-records.png)

*Rysunek — tworzenie rekordów strefy DNS na platformie Azure*

W tym scenariuszu witryna sieci Web \. contoso.com ma wartość TTL 30 minut, która jest również niższa od podanej RTO, i wskazuje na witrynę produkcyjną prod.contoso.com. Ta konfiguracja jest w trakcie normalnych operacji wykonywanych w firmie. Wartość parametru TTL prod.contoso.com i dr.contoso.com została ustawiona na 300 sekund lub 5 minut. Możesz użyć usługi monitorowania platformy Azure, takiej jak Azure Monitor lub Azure App Insights, a także rozwiązań do monitorowania partnerów, takich jak dynaTrace, można nawet korzystać z rozwiązań domowych, które mogą monitorować lub wykrywać awarie poziomu infrastruktury aplikacji lub sieci wirtualnej.

### <a name="step-3-update-the-cname-record"></a>Krok 3. aktualizowanie rekordu CNAME

Po wykryciu błędu Zmień wartość rekordu na dr.contoso.com, jak pokazano poniżej:
       
![Aktualizowanie rekordu CNAME](./media/disaster-recovery-dns-traffic-manager/update-cname-record.png)

*Ilustracja — aktualizowanie rekordu CNAME na platformie Azure*

W ciągu 30 minut, podczas którego większość resolverów odświeży plik strefy w pamięci podręcznej, wszystkie zapytania do sieci Web \. contoso.com zostaną przekierowane do Dr.contoso.com.
Możesz również uruchomić następujące polecenie interfejsu wiersza polecenia platformy Azure, aby zmienić wartość CNAME:
 ```azurecli
   az network dns record-set cname set-record \
   --resource-group 123 \
   --zone-name contoso.com \
   --record-set-name www \
   --cname dr.contoso.com
```
Ten krok można wykonać ręcznie lub za pośrednictwem automatyzacji. Można to zrobić ręcznie za pośrednictwem konsoli programu lub interfejsu wiersza polecenia platformy Azure. Zestaw Azure SDK i interfejs API mogą służyć do automatyzowania aktualizacji CNAME, aby nie była wymagana interwencja ręczna. Automatyzację można skompilować za pomocą usługi Azure Functions lub w aplikacji do monitorowania innej firmy, a nawet w środowisku lokalnym.

### <a name="how-manual-failover-works-using-azure-dns"></a>Jak działa ręczne przełączenie w tryb failover przy użyciu Azure DNS
Ponieważ serwer DNS znajduje się poza strefą trybu failover lub awarii, jest izolowany od wszelkich przestojów. Dzięki temu użytkownik może utworzyć architekt prostego scenariusza trybu failover, który jest oszczędny i będzie działał przez cały czas przy założeniu, że operator ma łączność sieciową w trakcie awarii i przerzuca. Jeśli rozwiązanie jest skryptowe, należy upewnić się, że serwer lub usługa, na których działa skrypt, powinien być izolowany względem problemu wpływającego na środowisko produkcyjne. Należy również pamiętać o niskim poziomie czasu wygaśnięcia ustawionym względem strefy, dzięki czemu żaden mechanizm rozwiązywania konfliktów na całym świecie nie zachowuje punktu końcowego w pamięci podręcznej, a klienci mogą uzyskać dostęp do witryny w ramach RTO. W przypadku stanu gotowości zimnej i pilotażowej, ponieważ może być wymagane pewne działanie administracyjne, a inne mogą zapewnić wystarczającą ilość czasu przed przerzuceniem.

## <a name="automatic-failover-using-azure-traffic-manager"></a>Automatyczne przełączanie do trybu failover przy użyciu usługi Azure Traffic Manager
Jeśli masz złożone architektury i wiele zestawów zasobów umożliwiających wykonywanie tej samej funkcji, możesz skonfigurować usługę Azure Traffic Manager (na podstawie systemu DNS), aby sprawdzić kondycję zasobów i skierować ruch z zasobów niezwiązanych z kondycją do zasobu w dobrej kondycji. W poniższym przykładzie zarówno region podstawowy, jak i region pomocniczy mają pełne wdrożenie. To wdrożenie obejmuje usługi Cloud Services i zsynchronizowaną bazę danych. 

![Automatyczne przełączanie do trybu failover przy użyciu usługi Azure Traffic Manager](./media/disaster-recovery-dns-traffic-manager/automatic-failover-using-traffic-manager.png)

*Rysunek — automatyczne przełączanie do trybu failover przy użyciu usługi Azure Traffic Manager*

Jednak tylko region podstawowy aktywnie obsługuje żądania sieciowe od użytkowników. Region pomocniczy stanie się aktywny tylko wtedy, gdy region podstawowy napotyka przerwę w działaniu usługi. W takim przypadku wszystkie nowe żądania sieciowe są kierowane do regionu pomocniczego. Ponieważ kopia zapasowa bazy danych jest blisko chwilowa, oba moduły równoważenia obciążenia mają adresy IP, które mogą być sprawdzone pod kątem kondycji, a wystąpienia są zawsze uruchomione, a Ta topologia udostępnia opcję przechodzenia do RTO i pracy awaryjnej bez żadnej ręcznej interwencji. Pomocniczy region trybu failover musi być gotowy do przejścia w tryb Live bezpośrednio po awarii regionu podstawowego.
Ten scenariusz jest idealnym rozwiązaniem w przypadku korzystania z usługi Azure Traffic Manager, która ma wbudowane sondy dla różnych typów kontroli kondycji, w tym http/https i TCP. Usługa Azure Traffic Manager ma także aparat reguł, który można skonfigurować do pracy w trybie failover, gdy wystąpi błąd opisany poniżej. Rozważmy następujące rozwiązanie przy użyciu Traffic Manager:
- Klient ma region #1 punkt końcowy znany jako prod.contoso.com ze statycznym adresem IP jako 100.168.124.44 i region #2 punkt końcowy znany jako dr.contoso.com ze statycznym adresem IP jako 100.168.124.43. 
-   Każdy z tych środowisk jest przedsunięty za pośrednictwem publicznej właściwości, takiej jak moduł równoważenia obciążenia. Moduł równoważenia obciążenia można skonfigurować tak, aby zawierał punkt końcowy oparty na systemie DNS lub w pełni kwalifikowaną nazwę domeny (FQDN), jak pokazano powyżej.
-   Wszystkie wystąpienia w regionie 2 są w trakcie niemal w czasie rzeczywistym replikacji z regionem 1. Ponadto obrazy maszyn są aktualne, a wszystkie dane oprogramowania/konfiguracji są poprawiane i są zgodne z regionem 1.  
-   Skalowanie automatyczne jest wstępnie skonfigurowane z wyprzedzeniem. 

Kroki wykonywane w celu skonfigurowania trybu failover za pomocą usługi Azure Traffic Manager są następujące:
1. Utwórz nowy profil usługi Azure Traffic Manager
2. Tworzenie punktów końcowych w ramach profilu Traffic Manager
3. Skonfiguruj Sprawdzanie kondycji i konfigurację trybu failover

### <a name="step-1-create-a-new-azure-traffic-manager-profile"></a>Krok 1. Tworzenie nowego profilu usługi Azure Traffic Manager
Utwórz nowy profil usługi Azure Traffic Manager o nazwie contoso123 i wybierz metodę routingu jako priorytet. Jeśli masz już istniejącą grupę zasobów, którą chcesz skojarzyć z usługą, możesz wybrać istniejącą grupę zasobów, w przeciwnym razie utworzyć nową grupę zasobów.

![Utwórz profil Traffic Manager](./media/disaster-recovery-dns-traffic-manager/create-traffic-manager-profile.png)

*Rysunek — Tworzenie profilu Traffic Manager*

### <a name="step-2-create-endpoints-within-the-traffic-manager-profile"></a>Krok 2. Tworzenie punktów końcowych w ramach profilu Traffic Manager

W tym kroku utworzysz punkty końcowe wskazujące na Lokacje odzyskiwania po awarii. W tym miejscu wybierz **Typ** jako zewnętrzny punkt końcowy, ale jeśli zasób jest hostowany na platformie Azure, możesz wybrać również **punkt końcowy platformy Azure** . Jeśli wybierzesz **punkt końcowy platformy Azure**, wybierz **zasób docelowy** , który jest **App Service** lub **publicznym adresem IP** przydzielonym przez platformę Azure. Priorytet jest ustawiony jako wartość **1** , ponieważ jest to podstawowa usługa dla regionu 1.
Podobnie należy również utworzyć punkt końcowy odzyskiwania po awarii w Traffic Manager.

![Tworzenie punktów końcowych odzyskiwania po awarii](./media/disaster-recovery-dns-traffic-manager/create-disaster-recovery-endpoint.png)

*Rysunek — Tworzenie punktów końcowych odzyskiwania po awarii*

### <a name="step-3-set-up-health-check-and-failover-configuration"></a>Krok 3. Konfigurowanie kontroli kondycji i konfiguracji trybu failover

W tym kroku ustawisz czas wygaśnięcia DNS na 10 sekund, który jest uznawany przez większość tłumaczeń cyklicznych dostępnych z Internetu. Ta konfiguracja oznacza, że żaden program rozpoznawania nazw DNS nie będzie buforować informacji przez więcej niż 10 sekund. W przypadku ustawień monitora punktu końcowego ścieżka jest ustawiona na wartość/lub root, ale można dostosować ustawienia punktu końcowego w celu oszacowania ścieżki, na przykład prod.contoso.com/index. W poniższym przykładzie pokazano **https** jako protokół sondowania. Można jednak wybrać również opcję **http** lub **TCP** . Wybór protokołu zależy od aplikacji końcowej. Interwał sondowania jest ustawiony na 10 sekund, co umożliwia szybkie sondowanie, a ponowna próba jest ustawiona na 3. W związku z tym Traffic Manager przejdzie w tryb failover do drugiego punktu końcowego, jeśli trzy kolejne interwały zarejestrują awarię. Poniższa formuła definiuje łączny czas automatycznej pracy awaryjnej: czas przejścia w tryb failover = TTL + ponów próbę * interwał sondowania i w tym przypadku wartość wynosi 10 + 3 * 10 = 40 sekund (max).
Jeśli ponowienie zostanie ustawione na wartość 1, a czas wygaśnięcia wynosi 10 sekund, czas pracy awaryjnej 10 + 1 * 10 = 20 s. Ustaw ponowną próbę na wartość większą niż **1** , aby wyeliminować szanse przejścia w tryb failover z powodu fałszywych wartości dodatnich lub wszelkich drobnych Blips sieciowych. 


![Konfigurowanie kontroli kondycji](./media/disaster-recovery-dns-traffic-manager/set-up-health-check.png)

*Rysunek — Konfigurowanie kontroli kondycji i konfiguracji trybu failover*

### <a name="how-automatic-failover-works-using-traffic-manager"></a>Jak działa automatyczne przejście w tryb failover przy użyciu Traffic Manager

W trakcie awarii podstawowy punkt końcowy zostaje przesondowany, a stan zmieni się na **obniżony** , a lokacja odzyskiwania po awarii pozostaje w **trybie online**. Domyślnie usługa Traffic Manager kieruje cały ruch do podstawowego punktu końcowego (punktu końcowego o najwyższym priorytecie). Jeśli podstawowy punkt końcowy ma obniżoną wydajność, Traffic Manager kieruje ruch do drugiego punktu końcowego, o ile pozostanie w dobrej kondycji. Jedną z opcji jest możliwość skonfigurowania większej liczby punktów końcowych w Traffic Manager, które mogą obsłużyć jako dodatkowe punkty końcowe trybu failover, lub, jako moduły równoważenia obciążenia, które udostępniają obciążenie między punktami końcowymi

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [usłudze Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md).
- Dowiedz się więcej o [Azure DNS](../dns/dns-overview.md).