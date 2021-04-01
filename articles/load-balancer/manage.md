---
title: Ustawienia portalu usługi Azure Load Balancer
description: Wprowadzenie do uczenia Azure Load Balancer ustawień portalu
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/8/2020
ms.author: allensu
ms.openlocfilehash: e1080aea12e70f4312fbee07b063d5a5cfbd1201
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89596311"
---
# <a name="azure-load-balancer-portal-settings"></a>Ustawienia portalu usługi Azure Load Balancer

Podczas tworzenia Azure Load Balancer informacje przedstawione w tym artykule pomogą dowiedzieć się więcej o poszczególnych ustawieniach oraz o tym, czym jest odpowiednia konfiguracja.

## <a name="create-load-balancer"></a>Tworzenie modułu równoważenia obciążenia

Azure Load Balancer to moduł równoważenia obciążenia sieci, który dystrybuuje ruch między wystąpieniami maszyn wirtualnych w puli zaplecza.

### <a name="basics"></a>Podstawy

Na karcie **podstawy** na stronie portalu Tworzenie modułu równoważenia obciążenia zostaną wyświetlone następujące informacje:



| Ustawienie |  Szczegóły |
| ---------- | ---------- |
| Subskrypcja  | Wybierz subskrypcję. Wybór to subskrypcja, w której ma zostać wdrożony moduł równoważenia obciążenia. |
| Grupa zasobów | Wybierz pozycję **Utwórz nową** i wpisz nazwę grupy zasobów w polu tekstowym. Jeśli masz utworzoną istniejącą grupę zasobów, wybierz ją. |
| Nazwa | To ustawienie jest nazwą Azure Load Balancer. |
| Region (Region) | Wybierz region platformy Azure, w którym chcesz wdrożyć moduł równoważenia obciążenia. |
| Typ | Moduł równoważenia obciążenia ma dwa typy: </br> **Wewnętrzny (prywatny)** </br> **Public (zewnętrzne)**.</br> Wewnętrzny moduł równoważenia obciążenia (ILB) kieruje ruch do członków puli zaplecza za pomocą prywatnego adresu IP.</br> Publiczny moduł równoważenia obciążenia kieruje żądania od klientów przez Internet do puli zaplecza.</br> Dowiedz się więcej o [typach modułów równoważenia obciążenia](components.md#frontend-ip-configuration-).|
| SKU  | Wybierz pozycję **Standardowy**. </br> Moduł równoważenia obciążenia ma dwie jednostki SKU: **podstawowa** i **standardowa**. </br> Podstawowa ma ograniczoną funkcjonalność. </br> **Standard** jest zalecany w przypadku obciążeń produkcyjnych. </br> Dowiedz się więcej o jednostkach [SKU](skus.md). |

Jeśli wybierzesz opcję **publiczny** jako typ, zobaczysz następujące informacje:

| Ustawienie |  Szczegóły |
| ---------- | ---------- |
| Publiczny adres IP | Wybierz pozycję **Utwórz nowy** , aby utworzyć publiczny adres IP dla publicznego modułu równoważenia obciążenia. </br> Jeśli masz istniejący publiczny adres IP, wybierz pozycję **Użyj istniejącej**.  |
| Nazwa publicznego adresu IP | Nazwa publicznego adresu IP.|
| Jednostka SKU publicznego adresu IP | Publiczne adresy IP mają dwie jednostki SKU: **podstawowa** i **standardowa**. </br> Warstwa Podstawowa nie obsługuje atrybutów odporności i stref. </br> **Standard** jest zalecany w przypadku obciążeń produkcyjnych. </br> Jednostki SKU modułu równoważenia obciążenia i publicznego adresu IP **muszą być zgodne**. |
| Przypisanie | Dla warstwy Standardowa jest wybierana funkcja **static** . </br> Podstawowe publiczne adresy IP mają dwa typy: **dynamiczne** i **statyczne**. </br> Dynamiczne publiczne adresy IP nie są przypisywane do momentu utworzenia. </br> Adresy IP można utracić, jeśli zasób zostanie usunięty. </br> Zalecane są statyczne adresy IP. |
| Strefa dostępności | Wybierz opcję **strefa nadmiarowa** , aby utworzyć odporny moduł równoważenia obciążenia. </br> Aby utworzyć strefowy moduł równoważenia obciążenia, wybierz określoną strefę z **1**, **2** lub **3**. </br> Strefy obsługi usługi równoważenia obciążenia w warstwie Standardowa i publicznych adresów IP. </br> Dowiedz się więcej o [strefach równoważenia obciążenia i dostępności](load-balancer-standard-availability-zones.md). </br> Nie zobaczysz wyboru strefy dla warstwy Podstawowa. Podstawowa usługa równoważenia obciążenia nie obsługuje stref. |
| Dodaj publiczny adres IPv6 | Moduł równoważenia obciążenia obsługuje adresy **IPv6** dla frontonu. </br> Dowiedz się więcej o usłudze [równoważenia obciążenia i protokole IPv6](load-balancer-ipv6-overview.md)
| Preferencja routingu | Wybierz pozycję **Sieć firmy Microsoft**. </br> Sieć firmy Microsoft oznacza, że ruch jest kierowany za pośrednictwem sieci globalnej firmy Microsoft. </br> Internet oznacza, że ruch jest kierowany przez sieć usługodawcy internetowego. </br> Dowiedz się więcej o [preferencjach routingu](../virtual-network/routing-preference-overview.md)|

:::image type="content" source="./media/manage/create-public-load-balancer-basics.png" alt-text="Utwórz publiczny moduł równoważenia obciążenia." border="true":::

W przypadku wybrania opcji **wewnętrzna** w polu Typ zostaną wyświetlone następujące informacje:

| Ustawienie |  Szczegóły |
| ---------- | ---------- |
| Sieć wirtualna | Sieć wirtualna, z której ma nastąpić wewnętrzna usługa równoważenia obciążenia. </br> Prywatny adres IP frontonu wybrany dla wewnętrznego modułu równoważenia obciążenia będzie pochodzić z tej sieci wirtualnej. |
| Przypisanie adresu IP | Opcje są **statyczne** lub **dynamiczne**. </br> Wartość statyczna gwarantuje, że adres IP nie zmieni się. Można zmienić dynamiczny adres IP. |
| Strefa dostępności | Dostępne opcje to: </br> **Strefa nadmiarowa** </br> **Strefa 1** </br> **Strefa 2** </br> **Strefa 3** </br> Aby utworzyć moduł równoważenia obciążenia o wysokiej dostępności i odporny na awarie stref dostępności, wybierz **strefowo nadmiarowy** adres IP. |

:::image type="content" source="./media/manage/create-internal-load-balancer-basics.png" alt-text="Utwórz wewnętrzny moduł równoważenia obciążenia." border="true":::

## <a name="frontend-ip-configuration"></a>Konfiguracja adresu IP frontonu

Adres IP Azure Load Balancer. Jest to punkt kontaktu dla klientów. 

Można mieć jedną lub wiele konfiguracji adresu IP frontonu. W przypadku powyższej sekcji podstawowe informacje o tym, że utworzono już fronton dla modułu równoważenia obciążenia. 

Jeśli chcesz dodać konfigurację adresu IP frontonu do modułu równoważenia obciążenia, przejdź do modułu równoważenia obciążenia w Azure Portal wybierz pozycję **Konfiguracja adresu IP frontonu**, a następnie wybierz pozycję **+ Dodaj**.

| Ustawienie |  Szczegóły |
| ---------- | ---------- |
| Nazwa | Nazwa konfiguracji adresu IP frontonu. |
| Wersja protokołu IP | Adres IP, który ma być używany przez fronton. </br> Moduł równoważenia obciążenia obsługuje konfiguracje adresów IP frontonu IPv4 i IPv6. |
| Typ adresu IP | Typ IP określa, czy pojedynczy adres IP jest skojarzony z frontonem lub zakresem adresów IP przy użyciu prefiksu IP. </br> [Publiczny prefiks adresu IP](../virtual-network/public-ip-address-prefix.md) pomaga, gdy trzeba wielokrotnie łączyć się z tym samym punktem końcowym. Prefiks zapewnia wystarczającą liczbę portów, aby pomóc w rozwiązywaniu problemów z portem. |
| Publiczny adres IP (lub prefiks, jeśli wybrano prefiks powyżej) | Wybierz lub Utwórz nowy publiczny adres IP (lub prefiks) frontonu modułu równoważenia obciążenia. |

:::image type="content" source="./media/manage/frontend.png" alt-text="Utwórz stronę konfiguracji adresu IP frontonu." border="true":::

## <a name="backend-pools"></a>Pule zaplecza

Pula adresów zaplecza zawiera adresy IP interfejsów sieci wirtualnych w puli zaplecza. 

Jeśli chcesz dodać pulę zaplecza do modułu równoważenia obciążenia, przejdź do modułu równoważenia obciążenia w Azure Portal wybierz pozycję **Pule zaplecza**, a następnie wybierz pozycję **+ Dodaj**.

| Ustawienie | Szczegóły |
| ---------- |  ---------- |
| Nazwa | Nazwa puli zaplecza. |
| Sieć wirtualna | Sieć wirtualna, w której znajdują się wystąpienia zaplecza. |
| Wersja protokołu IP | Dostępne opcje to **IPv4** lub **IPv6**. |

Maszyny wirtualne lub zestawy skalowania maszyn wirtualnych można dodać do puli zaplecza Azure Load Balancer. Najpierw utwórz maszyny wirtualne lub zestawy skalowania maszyn wirtualnych. Następnie dodaj je do modułu równoważenia obciążenia w portalu.

:::image type="content" source="./media/manage/backend.png" alt-text="Utwórz stronę puli zaplecza." border="true":::

## <a name="health-probes"></a>Sondy kondycji

Sonda kondycji służy do monitorowania stanu maszyn wirtualnych lub wystąpień zaplecza. Stan sondy kondycji określa, kiedy nowe połączenia są wysyłane do wystąpienia na podstawie kontroli kondycji. 

Jeśli chcesz dodać sondę kondycji do modułu równoważenia obciążenia, przejdź do modułu równoważenia obciążenia w Azure Portal wybierz pozycję **sondy kondycji**, a następnie wybierz pozycję **+ Dodaj**.

| Ustawienie | Szczegóły |
| ---------- | ---------- |
| Nazwa | Nazwa sondy kondycji. |
| Protokół | Wybrany protokół określa typ sprawdzania używany do określenia, czy wystąpienia zaplecza są w dobrej kondycji. </br> Dostępne opcje to: </br> **TCP** </br> **Schemat** </br> **HTTP** </br> Upewnij się, że używasz odpowiedniego protokołu. Wybór ten będzie zależeć od charakteru aplikacji. </br> Konfiguracja sondy kondycji i odpowiedzi sondy określa, które wystąpienia puli zaplecza będą odbierać Nowe przepływy. </br> Sond kondycji można użyć do wykrywania awarii aplikacji w punkcie końcowym zaplecza. </br> Dowiedz się więcej o [sondach kondycji](load-balancer-custom-probe-overview.md). |
| Port | Port docelowy sondy kondycji. </br> To ustawienie jest portem w wystąpieniu zaplecza, którego będzie używać sonda kondycji w celu określenia kondycji wystąpienia. |
| Interwał | Liczba sekund między próbami sondowania. </br> Interwał określi, jak często sonda kondycji podejmie próbę nawiązania połączenia z wystąpieniem zaplecza. </br> W przypadku wybrania opcji 5 druga próba sondowania zostanie wykonana po 5 sekundach itd. |
| Próg złej kondycji | Liczba kolejnych niepowodzeń sondy, które muszą wystąpić, zanim maszyna wirtualna zostanie uznana za złą.</br> W przypadku wybrania 2 nie zostaną ustawione żadne nowe przepływy dla tego wystąpienia wewnętrznej bazy danych po dwóch kolejnych błędach. |

:::image type="content" source="./media/manage/health-probe.png" alt-text="Dodaj sondę kondycji." border="true":::

## <a name="load-balancing-rules"></a>Reguły równoważenia obciążenia

Definiuje sposób dystrybucji ruchu przychodzącego do wszystkich wystąpień w puli zaplecza. Reguła równoważenia obciążenia mapuje daną konfigurację i port IP frontonu na wiele adresów IP i portów zaplecza.

Jeśli chcesz dodać regułę modułu równoważenia obciążenia do modułu równoważenia obciążenia, przejdź do modułu równoważenia obciążenia w Azure Portal wybierz pozycję **reguły równoważenia obciążenia**, a następnie wybierz pozycję **+ Dodaj**.
    
| Ustawienie | Szczegóły |
| ---------- | ---------- |
| Nazwa | Nazwa reguły modułu równoważenia obciążenia. |
| Wersja protokołu IP | Dostępne opcje to **IPv4** lub **IPv6**.  |
| Adres IP frontonu | Wybierz adres IP frontonu. </br> Adres IP frontonu modułu równoważenia obciążenia, z którym ma być skojarzona reguła modułu równoważenia obciążenia.|
| Protokół | Azure Load Balancer to moduł równoważenia obciążenia sieci warstwy 4. </br> Dostępne są następujące opcje: **TCP** lub **UDP**. |
| Port | To ustawienie jest portem skojarzonym z adresem IP frontonu, który ma być dystrybuowany ruch na podstawie tej reguły równoważenia obciążenia. |
| Port zaplecza | To ustawienie jest portem wystąpień w puli zaplecza, do którego moduł równoważenia obciążenia ma wysyłać ruch. To ustawienie może być takie samo jak w przypadku portu frontonu lub innego, jeśli jest wymagana elastyczność aplikacji. |
| Pula zaplecza | Pula zaplecza, dla której ma zostać zastosowana ta reguła modułu równoważenia obciążenia. |
| Sonda kondycji | Utworzona sonda kondycji w celu sprawdzenia stanu wystąpień w puli zaplecza. </br> Tylko wystąpienia w dobrej kondycji będą odbierać nowy ruch. |
| Trwałość sesji |  Dostępne opcje to: </br> **Brak** </br> **Adres IP klienta** </br> **Adres IP i protokół klienta**</br> </br> Utrzymuje ruch z klienta do tej samej maszyny wirtualnej w puli zaplecza. Ten ruch będzie utrzymywany przez czas trwania sesji. </br> **Brak** określa, że kolejne żądania z tego samego klienta mogą być obsługiwane przez dowolną maszynę wirtualną. </br> Adres **IP klienta** określa, że kolejne żądania z tego samego adresu IP klienta będą obsługiwane przez tę samą maszynę wirtualną. </br> Adres **IP klienta i protokół** zapewniają, że kolejne żądania z tego samego adresu IP klienta i protokołu będą obsługiwane przez tę samą maszynę wirtualną. </br> Dowiedz się więcej na temat [trybów dystrybucji](load-balancer-distribution-mode.md). |
| Limit czasu bezczynności (minuty) | Pozostaw otwarte połączenie **TCP** lub **http** bez polegania na klientach do wysyłania komunikatów Keep-Alive |  
| Resetowanie protokołu TCP | Moduł równoważenia obciążenia może wysyłać **Resetowanie TCP** , aby pomóc w tworzeniu bardziej przewidywalnego zachowania aplikacji w przypadku, gdy połączenie jest w stanie bezczynności. </br> Dowiedz się więcej o [resetowaniu protokołu TCP](load-balancer-tcp-reset.md)|
| Pływający adres IP | Zmienny adres IP to terminologia platformy Azure dla części tego, co jest znane jako **bezpośrednie zwrócenie serwera (DSR)**. </br> DSR składa się z dwóch części: <br> 1. topologia przepływu </br> 2. schemat mapowania adresów IP na poziomie platformy. </br></br> Azure Load Balancer zawsze działa w topologii przepływu DSR, niezależnie od tego, czy jest włączony pływający adres IP. </br> Ta operacja oznacza, że wychodząca część przepływu jest zawsze poprawnie zapisywana do przepływu bezpośrednio z powrotem do źródła. </br> Bez zmiennoprzecinkowych adresów IP platforma Azure uwidacznia tradycyjny schemat mapowania adresów IP dla usługi równoważenia obciążenia, czyli adres IP wystąpień maszyn wirtualnych. </br> Włączenie pływającego adresu IP powoduje zmianę mapowania adresów IP na adres IP frontonu usługi równoważenia obciążenia w celu zapewnienia dodatkowej elastyczności. </br> Aby uzyskać więcej informacji, zobacz [wiele frontonów dla Azure Load Balancer](load-balancer-multivip-overview.md).|
| Utwórz niejawne reguły wychodzące | Wybierz pozycję **Nie**. </br> Wartość domyślna: **disableOutboundSnat = false**  </br> W tym przypadku wychodzące odbywa się za pośrednictwem tego samego adresu IP frontonu. </br></br> **disableOutboundSnat = true** </br>W takim przypadku reguły ruchu wychodzącego są zbędne dla ruchu wychodzącego. |

:::image type="content" source="./media/manage/load-balancing-rule.png" alt-text="Dodaj regułę równoważenia obciążenia." border="true":::

## <a name="inbound-nat-rules"></a>Reguły NAT dla ruchu przychodzącego

Przychodzące reguły NAT przesyłają ruch przychodzący do kombinacji portów i adresów IP frontonu. 

Ruch jest wysyłany do określonej maszyny wirtualnej lub wystąpienia w puli zaplecza. Przekazywanie portów odbywa się przy użyciu tego samego rozkładu opartego na wykorzystaniu skrótu co Równoważenie obciążenia.

Jeśli scenariusz wymaga sesji Remote Desktop Protocol (RDP) lub Secure Shell (SSH), aby oddzielić wystąpienia maszyn wirtualnych w puli zaplecza. Wiele wewnętrznych punktów końcowych można zamapować na porty przy użyciu tego samego adresu IP frontonu. 

Adresy IP frontonu mogą służyć do zdalnego administrowania maszynami wirtualnymi bez dodatkowego pola skoku.

Jeśli chcesz dodać regułę NAT dla ruchu przychodzącego do modułu równoważenia obciążenia, przejdź do modułu równoważenia obciążenia w Azure Portal wybierz pozycję **reguły NAT dla ruchu przychodzącego**, a następnie wybierz pozycję **+ Dodaj**.

| Ustawienie | Szczegóły |
| ---------- | ---------- |
| Nazwa | Nazwa reguły NAT dla ruchu przychodzącego |
| Adres IP frontonu | Wybierz adres IP frontonu. </br> Adres IP frontonu dla modułu równoważenia obciążenia, do którego ma być skojarzona Reguła ruchu przychodzącego NAT. |
| Wersja protokołu IP | Dostępne opcje to IPv4 i IPv6. |
| Usługa | Typ usługi, która będzie działać na Azure Load Balancer. </br> Wybór w tym miejscu spowoduje odpowiednie zaktualizowanie informacji o porcie. |
| Protokół | Azure Load Balancer to moduł równoważenia obciążenia sieci warstwy 4. </br> Dostępne są następujące opcje: TCP lub UDP. |
| Limit czasu bezczynności (minuty) | Pozostaw otwarte połączenie TCP lub HTTP bez polegania na klientach do wysyłania komunikatów Keep-Alive. |
| Resetowanie protokołu TCP | Load Balancer może wysyłać Resetowanie TCP, aby pomóc w tworzeniu bardziej przewidywalnego zachowania aplikacji w przypadku, gdy połączenie jest w stanie bezczynności. </br> Dowiedz się więcej o [resetowaniu protokołu TCP](load-balancer-tcp-reset.md) |
| Port | To ustawienie jest portem skojarzonym z adresem IP frontonu, który ma być dystrybuowany ruch na podstawie tej przychodzącej reguły NAT. |
| Docelowy maszyny wirtualnej | Część maszyny wirtualnej puli zaplecza, z którą ma zostać skojarzona ta reguła. |
| Mapowanie portów | To ustawienie może być domyślne lub niestandardowe na podstawie preferencji aplikacji. |

:::image type="content" source="./media/manage/inbound-nat-rule.png" alt-text="Dodaj regułę NAT dla ruchu przychodzącego." border="true":::

## <a name="outbound-rules"></a>Reguły ruchu wychodzącego

Reguły ruchu wychodzącego modułu równoważenia obciążenia Skonfiguruj wychodzące pliki zasad sieciowych dla maszyn wirtualnych w puli zaplecza.

Jeśli chcesz dodać regułę wychodzącą do modułu równoważenia obciążenia, przejdź do modułu równoważenia obciążenia w Azure Portal wybierz pozycję **reguły ruchu wychodzącego**, a następnie wybierz pozycję **+ Dodaj**.

| Ustawienie | Szczegóły |
| ------- | ------ |
| Nazwa | Nazwa reguły ruchu wychodzącego. |
| Adres IP frontonu | Wybierz adres IP frontonu. </br> Adres IP frontonu modułu równoważenia obciążenia, z którym ma zostać skojarzona Reguła ruchu wychodzącego. |
| Protokół | Azure Load Balancer to moduł równoważenia obciążenia sieci warstwy 4. </br> Dostępne są następujące opcje: **wszystkie**, **TCP** lub **UDP**. |
| Limit czasu bezczynności (minuty) | Pozostaw otwarte połączenie **TCP** lub **http** bez polegania na klientach do wysyłania komunikatów Keep-Alive. |
| Resetowanie protokołu TCP | Moduł równoważenia obciążenia może wysyłać **Resetowanie TCP** , aby pomóc w tworzeniu bardziej przewidywalnego zachowania aplikacji w przypadku, gdy połączenie jest w stanie bezczynności. </br> Dowiedz się więcej o [resetowaniu protokołu TCP](load-balancer-tcp-reset.md) |
| Pula zaplecza | Pula zaplecza, dla której ma zostać zastosowana ta reguła ruchu wychodzącego. |

### <a name="port-allocation"></a>Alokacja portu

| Ustawienie | Szczegóły |
| ------- | ------ |
| Alokacja portu | Zalecamy wybranie opcji **ręcznie wybierz liczbę portów wychodzących**.|

### <a name="outbound-ports"></a>Porty wychodzące

| Ustawienie | Szczegóły |
| ------- | ------ |
| Wybierz według | Wybierz **porty na wystąpienie** |
| Porty na wystąpienie | Wprowadź **10 000**. |

:::image type="content" source="./media/manage/outbound-rule.png" alt-text="Dodaj regułę ruchu przychodzącego." border="true":::

## <a name="next-steps"></a>Następne kroki

W tym artykule omówiono różne warunki i ustawienia w Azure Portal Azure Load Balancer.

* [Dowiedz się](./load-balancer-overview.md) więcej o Azure Load Balancer.
* [Często zadawane pytania](./load-balancer-faqs.md) dotyczące Azure Load Balancer.