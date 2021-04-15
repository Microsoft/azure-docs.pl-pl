---
title: Translator źródłowych adresów sieciowych (SNAT) dla połączeń wychodzących
titleSuffix: Azure Load Balancer
description: Dowiedz się, Azure Load Balancer są używane do wychodzącej łączności internetowej (SNAT).
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 3b92ef3ce195a2eee9bce53e08d977593a9f1fc2
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107477710"
---
# <a name="using-source-network-address-translation-snat-for-outbound-connections"></a>Używanie translatora źródłowych adresów sieciowych (SNAT) dla połączeń wychodzących

Aby zapewnić łączność wychodzącą z Internetem dla wystąpień zaplecza, można użyć ip frontonia publicznego równoważenia obciążenia platformy Azure. Ta konfiguracja używa **translatora źródłowych adresów sieciowych (SNAT).** SNAT ponownie zapisuje adres IP zaplecza na publiczny adres IP usługi równoważenia obciążenia. 

Funkcja SNAT **umożliwia zaszycie adresów IP** wystąpienia zaplecza. To zaszycie uniemożliwia zewnętrznym źródłom bezpośredni adres do wystąpień zaplecza. Adres IP współużytkować między wystąpieniami zaplecza zmniejsza koszt statycznych publicznych adresów IP. Znany adres IP obsługuje scenariusze, takie jak upraszczanie listy zezwalania na adresy IP przy użyciu ruchu ze znanych publicznych adresów IP. 

>[!Note]
> W przypadku aplikacji wymagających dużej liczby połączeń wychodzących lub klientów korporacyjnych, którzy wymagają pojedynczego [](../virtual-network/nat-overview.md) zestawu ip do zastosowania z danej sieci wirtualnej, zalecanym rozwiązaniem jest Translator adresów sieciowych usługi Virtual Network sieci. Jest to alokacja dynamiczna umożliwiająca prostą konfigurację i najbardziej wydajne korzystanie z portów SNAT z każdego adresu IP. Dzięki temu wszystkie zasoby w sieci wirtualnej mogą współużytrowić zestaw adresów IP bez konieczności udostępniania przez nie usługi równoważenia obciążenia.

>[!Important]
> Nawet bez skonfigurowanej sieci SNAT dla ruchu wychodzącego konta usługi Azure Storage w tym samym regionie będą nadal dostępne, a zasoby zaplecza będą nadal mieć dostęp do usługi firmy Microsoft takich jak Aktualizacje systemu Windows.

>[!NOTE] 
>W tym artykule o Azure Resource Manager tylko wdrożenia. Przejrzyj [połączenia wychodzące (klasyczne) dla](/previous-versions/azure/load-balancer/load-balancer-outbound-connections-classic) wszystkich klasycznych scenariuszy wdrażania na platformie Azure.

## <a name="sharing-frontend-ip-address-across-backend-resources"></a><a name ="snat"></a> Udostępnianie adresu IP frontonie w zasobach zaplecza

Jeśli zasoby zaplecza usługi równoważenia obciążenia nie mają publicznych adresów IP na poziomie wystąpienia (ILPIP), ustanawiają łączność wychodzącą za pośrednictwem adresu IP frontonia publicznego równoważenia obciążenia. Porty służą do generowania unikatowych identyfikatorów używanych do obsługi odrębnych przepływów. W celu zapewnienia tego rozróżnienia Internet używa krotki z pięcioma krotami.

Krotka z pięcioma częściami składa się z:

* Docelowy adres IP
* Port docelowy
* Źródłowy adres IP
* Port źródłowy i protokół w celu zapewnienia tego rozróżnienia.

Jeśli port jest używany dla połączeń przychodzących, ma odbiornik **dla** żądań połączeń przychodzących na tym porcie. Tego portu nie można używać dla połączeń wychodzących. Aby nawiązać połączenie wychodzące, port **efemeralny** służy do zapewnienia lokalizacji docelowej portu, na którym będzie się komunikować i utrzymywać odrębny przepływ ruchu. Gdy te porty efemeracyjne są używane dla SNAT, są one nazywane **portami SNAT** 

Z definicji każdy adres IP ma 65 535 portów. Każdy port może być używany dla połączeń przychodzących lub wychodzących dla protokołów TCP(Transmission Control Protocol) i UDP(User Datagram Protocol). 

Gdy publiczny adres IP jest dodawany jako adres IP frontonia do usługi równoważenia obciążenia, platforma Azure udostępnia 64 000 portów kwalifikujących się do SNAT.

>[!NOTE]
> Każdy port używany do równoważenia obciążenia lub reguły NAT dla ruchu przychodzącego będzie korzystać z zakresu ośmiu portów z tych 64 000 portów, co zmniejsza liczbę portów kwalifikujących się do korzystania z SNAT. Jeśli reguła równoważenia obciążenia lub reguły nat znajduje się w tym samym zakresie ośmiu co inna, nie będzie zużywać żadnych dodatkowych portów. 

Za [pomocą reguł ruchu](./outbound-rules.md) wychodzącego i reguł równoważenia obciążenia te porty SNAT można dystrybuować do wystąpień zaplecza, aby umożliwić im współużytkowanie publicznych ip usługi równoważenia obciążenia dla połączeń wychodzących.

Po [skonfigurowaniu scenariusza 2](#scenario2) poniżej host dla każdego wystąpienia zaplecza będzie miał pakiety SNAT, które są częścią połączenia wychodzącego. 

Podczas wykonywania SNAT w połączeniu wychodzącym z wystąpienia zaplecza host ponownie zapisuje źródłowy adres IP do jednego z adresów IP frontendu. 

Aby zachować unikatowe przepływy, host ponownie zapisuje port źródłowy każdego pakietu wychodzącego do portu SNAT w wystąpieniu zaplecza.

## <a name="outbound-connection-behavior-for-different-scenarios"></a>Zachowanie połączenia wychodzącego dla różnych scenariuszy
  * Maszyna wirtualna z publicznym adresem IP.
  * Maszyna wirtualna bez publicznego adresu IP.
  * Maszyna wirtualna bez publicznego adresu IP i standardowego usługi równoważenia obciążenia.
        
 ### <a name="scenario-1-virtual-machine-with-public-ip-either-with-or-without-a-load-balancer"></a><a name="scenario1"></a> Scenariusz 1. Maszyna wirtualna z publicznym adresem IP z usługą równoważenia obciążenia lub bez niego.

 | Stowarzyszenia | Metoda | Protokoły IP |
 | ---------- | ------ | ------------ |
 | Publiczny lub autonomiczny równoważenie obciążenia | [SNAT (translatora źródłowych adresów sieciowych)](#snat) </br> nie jest używany. | TCP (Transmission Control Protocol) </br> UDP (User Datagram Protocol) </br> ICMP (Internet Control Message Protocol) </br> ESP (Hermetyzowanie ładunku zabezpieczeń) |

 #### <a name="description"></a>Opis

 Cały ruch zostanie zwrócony do klienta żądającego z publicznego adresu IP maszyny wirtualnej (adres IP na poziomie wystąpienia).
 
 Platforma Azure używa publicznego adresu IP przypisanego do konfiguracji adresu IP karty sieciowej wystąpienia dla wszystkich przepływów wychodzących. Wystąpienie ma dostępne wszystkie porty efemeracyjne. Nie ma znaczenia, czy maszyna wirtualna ma zrównoważone obciążenie. Ten scenariusz ma pierwszeństwo przed innymi. 

 Publiczny adres IP przypisany do maszyny wirtualnej to relacja 1:1 (a nie 1: wiele) i zaimplementowana jako bez stanowy nat 1:1.

 ### <a name="scenario-2-virtual-machine-without-public-ip-and-behind-standard-public-load-balancer"></a><a name="scenario2"></a>Scenariusz 2. Maszyna wirtualna bez publicznego adresu IP i za standardową siecią publiczną Load Balancer

 | Stowarzyszenia | Metoda | Protokoły IP |
 | ------------ | ------ | ------------ |
 | Publiczny równoważenie obciążenia w standardowych | Użycie ip frontonia równoważenia obciążenia dla [SNAT.](#snat)| TCP </br> UDP |

 #### <a name="description"></a>Opis

 Zasób usługi równoważenia obciążenia jest konfigurowany przy użyciu reguły ruchu wychodzącego lub reguły równoważenia obciążenia, która włącza SNAT. Ta reguła służy do tworzenia połączenia między frontoną publicznego adresu IP a pulą zaplecza. 

 Jeśli ta konfiguracja reguły nie zostanie ukończona, zachowanie jest opisane w scenariuszu 3. 

 Reguła z odbiornikiem nie jest wymagana do powodzenia sondy kondycji.

 Gdy maszyna wirtualna tworzy przepływ wychodzący, platforma Azure tłumaczy źródłowy adres IP na publiczny adres IP frontonia publicznego równoważenia obciążenia. To tłumaczenie odbywa się za pośrednictwem [translatora SNAT.](#snat) 

 Porty efemeracyjne publicznego adresu IP frontonia usługi równoważenia obciążenia służą do odróżnienia poszczególnych przepływów pochodzących od maszyny wirtualnej. Podczas tworzenia przepływów [wychodzących](#preallocatedports) SNAT dynamicznie używane są wstępnie nieprzydzielone porty efemeracyjne. 

 W tym kontekście porty efemerów używane dla SNAT są nazywane portami SNAT. Zdecydowanie zaleca się jawne skonfigurowanie reguły ruchu wychodzącego. [](./outbound-rules.md) W przypadku używania domyślnej funkcji SNAT za pośrednictwem reguły równoważenia obciążenia porty SNAT są wstępnie przydzielane zgodnie z opisem w tabeli alokacji domyślnych portów [SNAT.](#snatporttable)

> [!NOTE]
> **Translator adresów sieciowych usługi Azure Virtual Network** może zapewnić łączność wychodzącą dla maszyn wirtualnych bez konieczności użycia usługi równoważenia obciążenia. Zobacz [Co to jest Translator adresów sieciowych usługi Azure Virtual Network?](../virtual-network/nat-overview.md) , aby uzyskać więcej informacji.

 ### <a name="scenario-3-virtual-machine-without-public-ip-and-behind-standard-internal-load-balancer"></a><a name="scenario3"></a>Scenariusz 3. Maszyna wirtualna bez publicznego adresu IP i za standardową siecią wewnętrzną Load Balancer

 | Stowarzyszenia | Metoda | Protokoły IP |
 | ------------ | ------ | ------------ |
 | Wewnętrzny równoważenie obciążenia w standardowych | Brak łączności z Internetem.| Brak |

 #### <a name="description"></a>Opis
 
W przypadku korzystania z wewnętrznego równoważenia obciążenia w standardowych usługach SNAT nie są wykorzystywane efemeralne adresy IP. Ta funkcja domyślnie obsługuje zabezpieczenia. Ta funkcja zapewnia, że wszystkie adresy IP używane przez zasoby można konfigurować i można je rezerwować. 

Aby osiągnąć łączność wychodzącą z Internetem w przypadku korzystania z wewnętrznego równoważenia obciążenia w standardowych, skonfiguruj publiczny adres IP na poziomie wystąpienia, aby postępować zgodnie z zachowaniem w [scenariuszu 1.](#scenario1) 

Inną opcją jest dodanie wystąpień zaplecza do publicznego równoważenia obciążenia w standardowych standardach ze skonfigurowaną regułą ruchu wychodzącego. Wystąpienia zaplecza są dodawane do wewnętrznego równoważenia obciążenia w celu wewnętrznego równoważenia obciążenia. To wdrożenie jest zachowaniem w [scenariuszu 2.](#scenario2) 

> [!NOTE]
> **Translator adresów sieciowych usługi Azure Virtual Network** może zapewnić łączność wychodzącą dla maszyn wirtualnych bez konieczności użycia usługi równoważenia obciążenia. Zobacz [Co to jest Translator adresów sieciowych usługi Azure Virtual Network? aby](../virtual-network/nat-overview.md) uzyskać więcej informacji.

 ### <a name="scenario-4-virtual-machine-without-public-ip-and-behind-basic-load-balancer"></a><a name="scenario4"></a>Scenariusz 4. Maszyna wirtualna bez publicznego adresu IP i za Load Balancer

 | Stowarzyszenia | Metoda | Protokoły IP |
 | ------------ | ------ | ------------ |
 |Brak </br> Podstawowy program równoważenia obciążenia | [SNAT](#snat) z dynamicznym adresem IP na poziomie wystąpienia| TCP </br> UDP | 

 #### <a name="description"></a>Opis

 Gdy maszyna wirtualna tworzy przepływ wychodzący, platforma Azure tłumaczy źródłowy adres IP na dynamicznie podany publiczny źródłowy adres IP. Tego publicznego adresu IP **nie można skonfigurować** i nie można go zarezerwować. Ten adres nie jest wliczony do limitu zasobów publicznego adresu IP subskrypcji. 

Publiczny adres IP zostanie wydany, a nowy publiczny adres IP będzie żądany w przypadku ponownego wdniania: 

 * Maszyna wirtualna
 * Zestaw dostępności
 * Zestaw skalowania maszyn wirtualnych 

 Nie używaj tego scenariusza do dodawania adresów IP do listy zezwalania. Użyj scenariusza 1 lub 2, w którym jawnie deklaruje się zachowanie ruchu wychodzącego. [Porty SNAT](#snat) są wstępnie nieprzydzielone zgodnie z opisem w tabeli alokacji domyślnych [portów SNAT.](#snatporttable)

## <a name="exhausting-ports"></a><a name="scenarios"></a> Wyczerpywujące się porty

Każde połączenie z tym samym docelowym adresem IP i portem docelowym będzie używać portu SNAT. To połączenie zachowuje odrębny **przepływ ruchu** z wystąpienia zaplecza lub **klienta** do **serwera**. Ten proces zapewnia serwerowi odrębny port, na którym ma być adresować ruch. Bez tego procesu komputer kliencki nie wie, do którego przepływu należy pakiet.

Wyobraź sobie, że masz wiele przeglądarek https://www.microsoft.com przechodzących do , czyli:

* Docelowy adres IP = 23.53.254.142
* Port docelowy = 443
* Protokół = TCP

Bez różnych portów docelowych dla ruchu powrotowego (portu SNAT użytego do nawiązania połączenia) klient nie będzie miał możliwości oddzielenia jednego wyniku zapytania od innego.

Połączenia wychodzące mogą się rozsyłać. Wystąpieniu zaplecza można przydzielić niewystarczające porty. Bez **włączonego ponownego** użycia połączenia zwiększa się ryzyko wyczerpania **portów** SNAT.

Nowe połączenia wychodzące do docelowego adresu IP nie powiodą się w przypadku wyczerpania portów. Połączenia powiedzie się, gdy port stanie się dostępny. To wyczerpanie występuje, gdy 64 000 portów z adresu IP jest rozłożonych zuniowo w wielu wystąpieniach zaplecza. Aby uzyskać wskazówki dotyczące ograniczania wyczerpania portów SNAT, zobacz [przewodnik rozwiązywania problemów](./troubleshoot-outbound-connection.md).  

W przypadku połączeń TCP równoważenie obciążenia będzie używać jednego portu SNAT dla każdego docelowego adresu IP i portu. To wieloużycie umożliwia wiele połączeń z tym samym docelowym adresem IP przy użyciu tego samego portu SNAT. To wieloużycie jest ograniczone, jeśli połączenie nie jest z różnymi portami docelowymi.

W przypadku połączeń UDP równoważenie obciążenia używa algorytmu **NAT** z ograniczeniami portów, który używa jednego portu SNAT na docelowy adres IP niezależnie od portu docelowego. 

Port jest ponownie wykorzystywany dla nieograniczonej liczby połączeń. Port jest ponownie wykorzystywany tylko wtedy, gdy docelowy adres IP lub port jest inny.

## <a name="default-port-allocation"></a><a name="preallocatedports"></a> Domyślna alokacja portów

Każdy publiczny adres IP przypisany jako adres IP frontonu usługi równoważenia obciążenia ma 64 000 portów SNAT dla składowych puli zaplecza. Portów nie można udostępniać członkom puli zaplecza. Zakres portów SNAT może być używany tylko przez pojedyncze wystąpienie zaplecza w celu zapewnienia prawidłowego rozsyłania pakietów zwrotnych. 

Zaleca się użycie jawnej reguły ruchu wychodzącego do skonfigurowania alokacji portów SNAT. Ta reguła maksymalizuje liczbę portów SNAT dostępnych dla połączeń wychodzących w każdym wystąpieniu zaplecza. 

Jeśli używasz automatycznej alokacji wychodzącej sieci SNAT za pośrednictwem reguły równoważenia obciążenia, tabela alokacji zdefiniuje alokację portów.

W <a name="snatporttable"></a> poniższej tabeli przedstawiono wstępne alokacje portów SNAT dla warstw rozmiarów puli zaplecza:

| Rozmiar puli (wystąpienia maszyn wirtualnych) | Wstępnie nieprzydzielone porty SNAT na konfigurację adresów IP |
| --- | --- |
| 1-50 | 1,024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1,000 | 32 | 

>[!NOTE]
> Jeśli masz pulę zaplecza o maksymalnym rozmiarze 10, każde wystąpienie może mieć 64 000/10 = 6400 portów w przypadku zdefiniowania jawnej reguły ruchu wychodzącego. Zgodnie z powyższymi tabelami każda z nich będzie mieć tylko 1024, jeśli wybierzesz automatyczną alokację.

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> Reguły i reguły ruchu Translator adresów sieciowych usługi Virtual Network

Azure Load Balancer reguły ruchu wychodzącego i Translator adresów sieciowych usługi Virtual Network są dostępne dla ruchu wychodzącego z sieci wirtualnej.

Aby uzyskać więcej informacji na temat reguł ruchu wychodzącego, zobacz [Reguły ruchu wychodzącego](outbound-rules.md).

Aby uzyskać więcej informacji na Translator adresów sieciowych usługi Azure Virtual Network, zobacz [Co to jest Translator adresów sieciowych usługi Azure Virtual Network](../virtual-network/nat-overview.md).

## <a name="constraints"></a>Ograniczenia

*   Gdy połączenie jest bezczynne bez wysłania nowych pakietów, porty zostaną zwolnione po 4–120 minutach.
  * Ten próg można skonfigurować za pomocą reguł ruchu wychodzącego.
*   Każdy adres IP udostępnia 64 000 portów, które mogą być używane dla SNAT.
*   Każdy port może być używany zarówno dla połączeń TCP, jak i UDP z docelowym adresem IP
  * Wymagany jest port SNAT protokołu UDP bez względu na to, czy port docelowy jest unikatowy, czy nie. Dla każdego połączenia UDP z docelowym adresem IP jest używany jeden port SNAT protokołu UDP.
  * Port SNAT protokołu TCP może służyć do wielu połączeń z tym samym docelowym adresem IP, pod warunkiem, że porty docelowe są różne.
*   Wyczerpanie SNAT występuje, gdy wystąpienie zaplecza wyczerpie się z podanych portów SNAT. W przypadku równoważenia obciążenia nadal mogą być używane nieużywane porty SNAT. Jeśli używane porty SNAT wystąpienia zaplecza przekraczają podane porty SNAT, nie będzie ono w stanie nawiązać nowych połączeń wychodzących.

## <a name="next-steps"></a>Następne kroki

*   [Rozwiązywanie problemów z połączeniami wychodzącymi z powodu wyczerpania SNAT](./troubleshoot-outbound-connection.md)
*   [Przejrzyj metryki SNAT](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) i zapoznaj się z prawidłowym sposobem filtrowania, dzielenia i wyświetlania ich.
