---
title: Translator adresów sieciowych (Resources) dla połączeń wychodzących
titleSuffix: Azure Load Balancer
description: Dowiedz się, jak Azure Load Balancer jest używany do wychodzącego połączenia z Internetem.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperf-fy21q1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 99f15afdab917fe28e22df8cb0e372b6c30c8526
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105027333"
---
# <a name="using-source-network-address-translation-snat-for-outbound-connections"></a>Używanie translatora adresów sieciowych dla połączeń wychodzących

Adresy IP frontonu publicznego modułu równoważenia obciążenia platformy Azure mogą służyć do zapewnienia łączności wychodzącej z Internetem dla wystąpień zaplecza. Ta konfiguracja używa **translatora adresów sieciowych (Resources)**. Podzbiór danych ponownie zapisuje adres IP zaplecza na publicznym adresie IP modułu równoważenia obciążenia. 

Przydziały adresów IP włączają **podszywającanie** się w ramach wystąpienia zaplecza. Takie zamaskowane uniemożliwia ze źródeł zewnętrznych adresowanie do wystąpień zaplecza. Adres IP współużytkowany między wystąpieniami wewnętrznej bazy danych zmniejsza koszt statycznych publicznych adresów IP. Znany adres IP obsługuje takie scenariusze, jak uproszczenie dozwolonych IP z ruchem ze znanych publicznych adresów IP. 

>[!Note]
> W przypadku aplikacji wymagających dużej liczby połączeń wychodzących lub klientów korporacyjnych, którzy wymagają jednego zestawu adresów IP do użycia z danej sieci wirtualnej, zalecanym rozwiązaniem jest [Virtual Network translator adresów sieciowych](../virtual-network/nat-overview.md) . Alokacja dynamiczna pozwala na prostą konfigurację i najbardziej wydajne korzystanie z portów ze wszystkich adresów IP. Umożliwia ona wszystkim zasobom w sieci wirtualnej udostępnianie zestawu adresów IP bez potrzeby udostępniania modułu równoważenia obciążenia.

>[!Important]
> Nawet bez skonfigurowanego wychodzącego programu Resource kont usługi Azure Storage w tym samym regionie nadal będą dostępne, a zasoby zaplecza nadal będą mieć dostęp do usług firmy Microsoft, takich jak aktualizacje systemu Windows.

>[!NOTE] 
>W tym artykule omówiono tylko wdrożenia Azure Resource Manager. Przejrzyj [połączenia wychodzące (klasyczne)](/previous-versions/azure/load-balancer/load-balancer-outbound-connections-classic) dla wszystkich klasycznych scenariuszy wdrażania na platformie Azure.

## <a name="sharing-frontend-ip-address-across-backend-resources"></a><a name ="snat"></a> Udostępnianie adresu IP frontonu w ramach zasobów zaplecza

Jeśli zasoby zaplecza modułu równoważenia obciążenia nie mają publicznych adresów IP na poziomie wystąpienia (ILPIP), ustanawiają łączność wychodzącą za pośrednictwem adresu IP frontonu publicznego modułu równoważenia obciążenia. Porty są używane do generowania unikatowych identyfikatorów używanych do obsługi różnych przepływów. Internet używa pięciu krotek w celu zapewnienia tego rozróżnienia.

Pięć krotek składa się z:

* Docelowy adres IP
* Port docelowy
* Źródłowy adres IP
* Port źródłowy i protokół w celu zapewnienia tego rozróżnienia.

Jeśli port jest używany do połączeń przychodzących, ma **odbiornik** dla przychodzących żądań połączeń na tym porcie. Tego portu nie można używać dla połączeń wychodzących. Aby nawiązać połączenie wychodzące, **port tymczasowych** służy do zapewnienia miejsca docelowego z portem, na którym należy komunikować się i obsługiwać różne przepływy ruchu. Gdy te porty tymczasowe są używane dla elementu Reportal, są one nazywane **portami** przyłączonem do adresów. 

Według definicji każdy adres IP ma 65 535 portów. Każdy port może być używany dla połączeń przychodzących lub wychodzących dla protokołów TCP (Transmission Control Protocol) i UDP (User Datagram Protocol). 

Gdy publiczny adres IP zostanie dodany jako adres IP frontonu do modułu równoważenia obciążenia, platforma Azure udostępnia 64 000 portów kwalifikujących się do przydzielenia im.

>[!NOTE]
> Każdy port używany do równoważenia obciążenia lub przychodzącej reguły NAT będzie korzystał z szeregu ośmiu portów z tych 64 000 portów, co zmniejsza liczbę portów kwalifikujących się do przydzielenia. Jeśli zasada równoważenia obciążenia lub translatora adresów sieciowych znajduje się w tym samym zakresie ośmiu, co inny, nie będzie korzystać z dodatkowych portów. 

Za pomocą [reguł ruchu wychodzącego](./outbound-rules.md) i reguł równoważenia obciążenia te porty adresów IP mogą być dystrybuowane do wystąpień zaplecza, aby umożliwić im Udostępnianie publicznych adresów IP modułu równoważenia obciążenia dla połączeń wychodzących.

W przypadku skonfigurowania [scenariusza 2](#scenario2) poniżej zostanie skonfigurowany Host dla każdego wystąpienia zaplecza, który jest częścią połączenia wychodzącego. 

W przypadku połączenia wychodzącego z wystąpienia zaplecza, Host ponownie zapisuje źródłowy adres IP na jednym z adresów IP frontonu. 

Aby zapewnić obsługę unikatowych przepływów, Host ponownie zapisuje port źródłowy każdego pakietu wychodzącego do portu strumienia adresów sieciowych w wystąpieniu zaplecza.

## <a name="outbound-connection-behavior-for-different-scenarios"></a>Zachowanie połączenia wychodzącego dla różnych scenariuszy
  * Maszyna wirtualna z publicznym adresem IP.
  * Maszyna wirtualna bez publicznego adresu IP.
  * Maszyna wirtualna bez publicznego adresu IP i bez standardowej usługi równoważenia obciążenia.
        
 ### <a name="scenario-1-virtual-machine-with-public-ip"></a><a name="scenario1"></a> Scenariusz 1. maszyna wirtualna z publicznym adresem IP

 | Związku | Metoda | Protokoły IP |
 | ---------- | ------ | ------------ |
 | Publiczny moduł równoważenia obciążenia lub autonomiczny | [Resourceer (translator adresów sieciowych)](#snat) </br> nieużywane. | TCP (Transmission Control Protocol) </br> UDP (User Datagram Protocol) </br> ICMP (Internet Control Message Protocol) </br> ESP (Hermetyzowanie ładunku zabezpieczeń) |

 #### <a name="description"></a>Opis

 Platforma Azure używa publicznego adresu IP przypisanego do konfiguracji protokołu IP karty sieciowej wystąpienia dla wszystkich przepływów wychodzących. Dla tego wystąpienia są dostępne wszystkie porty tymczasowe. Nie ma znaczenia, czy maszyna wirtualna ma Równoważenie obciążenia, czy nie. Ten scenariusz ma pierwszeństwo przed innymi. 

 Publiczny adres IP przypisany do maszyny wirtualnej jest relacją 1:1 (a nie 1: wiele) i zaimplementowaną jako bezstanowe urządzenie NAT 1:1.

 ### <a name="scenario-2-virtual-machine-without-public-ip-and-behind-standard-public-load-balancer"></a><a name="scenario2"></a>Scenariusz 2. maszyna wirtualna bez publicznego adresu IP i za Load Balancer publiczną w warstwie Standardowa

 | Związku | Metoda | Protokoły IP |
 | ------------ | ------ | ------------ |
 | Standardowy publiczny moduł równoważenia obciążenia | Korzystanie z adresów IP frontonu usługi równoważenia obciążenia dla [tego elementu.](#snat)| TCP </br> UDP |

 #### <a name="description"></a>Opis

 Zasób modułu równoważenia obciążenia jest skonfigurowany z regułą wychodzącą lub z regułą równoważenia obciążenia, która umożliwia podwyższenie poziomu. Ta reguła służy do tworzenia linku między publicznym serwerem frontonu IP a pulą zaplecza. 

 Jeśli ta konfiguracja reguły nie zostanie ukończona, zachowanie jest zgodne z opisem w scenariuszu 3. 

 Reguła z odbiornikiem nie jest wymagana do pomyślnego przeprowadzenia sondy kondycji.

 Gdy maszyna wirtualna tworzy przepływ wychodzący, platforma Azure tłumaczy źródłowy adres IP na publiczny adres IP frontonu modułu równoważenia obciążenia. To tłumaczenie odbywa [się za](#snat)pośrednictwem protokołu NAT. 

 Porty tymczasowe publicznego adresu IP frontonu modułu równoważenia obciążenia są używane do rozróżniania poszczególnych przepływów pochodzących z maszyny wirtualnej. W przypadku tworzenia przepływów ruchu wychodzącego w ramach strumienia danych dynamicznie [przydzielono wstępnie przydzieloną porty](#preallocatedports) tymczasowe. 

 W tym kontekście porty, które są używane do przystawcy adresów sieciowych, są nazywane portami. Zdecydowanie zaleca się jawne skonfigurowanie [reguły ruchu wychodzącego](./outbound-rules.md) . W przypadku użycia domyślnego odnoszący się do niego za pośrednictwem reguły równoważenia obciążenia porty podrzędnego protokołu adresów sieciowych są wstępnie przydzielane zgodnie z opisem w [tabeli alokacji domyślnych portów](#snatporttable).

> [!NOTE]
> **Usługa Azure Virtual Network translator adresów sieciowych** może zapewnić łączność wychodzącą dla maszyn wirtualnych bez potrzeby modułu równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Virtual Network translator adresów sieciowych?](../virtual-network/nat-overview.md)

 ### <a name="scenario-3-virtual-machine-without-public-ip-and-behind-standard-internal-load-balancer"></a><a name="scenario3"></a>Scenariusz 3: maszyna wirtualna bez publicznego adresu IP i za Load Balancer wewnętrzny w warstwie Standardowa

 | Związku | Metoda | Protokoły IP |
 | ------------ | ------ | ------------ |
 | Wewnętrzny moduł równoważenia obciążenia w warstwie Standardowa | Brak łączności z Internetem.| Brak |

 #### <a name="description"></a>Opis
 
W przypadku korzystania ze standardowego wewnętrznego modułu równoważenia obciążenia nie są używane tymczasowe adresy IP dla tego elementu. Ta funkcja domyślnie obsługuje zabezpieczenia. Ta funkcja zapewnia, że wszystkie adresy IP używane przez zasoby są konfigurowalne i mogą być zarezerwowane. 

Aby zapewnić łączność wychodzącą z Internetem w przypadku korzystania ze standardowego wewnętrznego modułu równoważenia obciążenia, należy skonfigurować publiczny adres IP na poziomie wystąpienia, aby postępować zgodnie z zachowaniem w [scenariuszu 1](#scenario1). 

Innym rozwiązaniem jest dodanie wystąpień zaplecza do standardowego publicznego modułu równoważenia obciążenia przy użyciu skonfigurowanej reguły ruchu wychodzącego. Wystąpienia zaplecza są dodawane do wewnętrznego modułu równoważenia obciążenia na potrzeby wewnętrznego równoważenia obciążenia. To wdrożenie jest zgodne z zachowaniem w [scenariuszu 2](#scenario2). 

> [!NOTE]
> **Usługa Azure Virtual Network translator adresów sieciowych** może zapewnić łączność wychodzącą dla maszyn wirtualnych bez potrzeby modułu równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Virtual Network translator adresów sieciowych?](../virtual-network/nat-overview.md)

 ### <a name="scenario-4-virtual-machine-without-public-ip-and-behind-basic-load-balancer"></a><a name="scenario4"></a>Scenariusz 4: maszyna wirtualna bez publicznego adresu IP i za podstawową Load Balancer

 | Związku | Metoda | Protokoły IP |
 | ------------ | ------ | ------------ |
 |Brak </br> Podstawowa usługa równoważenia obciążenia | Dynamiczny adres [IP na poziomie](#snat) wystąpienia| TCP </br> UDP | 

 #### <a name="description"></a>Opis

 Gdy maszyna wirtualna tworzy przepływ wychodzący, platforma Azure tłumaczy źródłowy adres IP na dynamicznie określony publiczny źródłowy adres IP. Ten publiczny adres IP **nie jest konfigurowalny** i nie można go zarezerwować. Ten adres nie jest liczony pod względem limitu zasobów publicznego adresu IP subskrypcji. 

Publiczny adres IP zostanie wywnioskowany, a w przypadku ponownego wdrożenia: 

 * Maszyna wirtualna
 * Zestaw dostępności
 * Zestaw skalowania maszyn wirtualnych 

 Nie należy używać tego scenariusza do dodawania adresów IP do dozwolonych. Użyj scenariusza 1 lub 2, gdzie jawnie deklarujesz zachowanie wychodzące. Porty podrzędnego [translatora adresów sieciowych](#snat) są wstępnie przydzielone zgodnie z opisem w [tabeli alokacji domyślnych portów](#snatporttable).

## <a name="exhausting-ports"></a><a name="scenarios"></a> Porty wydechowe

Każde połączenie z tym samym docelowym adresem IP i portem docelowym będzie używać portu. To połączenie utrzymuje odrębny **przepływ ruchu** z wystąpienia zaplecza lub **klienta** do **serwera**. Ten proces nadaje serwerowi unikatowy port, na którym ma być adresowany ruch. Bez tego procesu komputer kliencki nie rozpoznaje tego, z którego przepływu jest częścią pakietu.

Wyobraź sobie, że ma wiele przeglądarek https://www.microsoft.com :

* Docelowy adres IP = 23.53.254.142
* Port docelowy = 443
* Protokół = TCP

Bez innych portów docelowych dla ruchu zwrotnego (port wiązania używanego do ustanowienia połączenia) klient nie będzie miał możliwości oddzielenia jednego wyniku zapytania od innego.

Połączenia wychodzące mogą być przełączane. Wystąpienie zaplecza może być przydzieloną za mało portów. Bez włączenia **ponownego użycia połączenia** jest zwiększane ryzyko **wyczerpania portów** .

Jeśli nastąpi wyczerpanie portów, nowe połączenia wychodzące z docelowym adresem IP będą kończyć się niepowodzeniem. Połączenia będą kończyły się powodzeniem, gdy port będzie dostępny. Ta wyczerpanie występuje, gdy porty 64 000 z adresu IP są rozkładane w postaci cienkiej w wielu wystąpieniach zaplecza. Aby uzyskać wskazówki dotyczące [rozwiązywania problemów](./troubleshoot-outbound-connection.md)z wyczerpaniem portów z poziomu adresów sieciowych  

W przypadku połączeń TCP moduł równoważenia obciążenia będzie używać jednego portu dla każdego docelowego adresu IP i portu. Ta Multiuse umożliwia wiele połączeń z tym samym docelowym adresem IP z tym samym portem. Ta Multiuse jest ograniczona, jeśli połączenie nie ma różnych portów docelowych.

W przypadku połączeń UDP moduł równoważenia obciążenia używa algorytmu **NAT z ograniczeniami portów** , który wykorzystuje jeden port dla docelowego adresu IP, niezależnie od portu docelowego. 

Port jest ponownie używany dla nieograniczonej liczby połączeń. Port jest używany ponownie tylko wtedy, gdy docelowy adres IP lub port jest inny.

## <a name="default-port-allocation"></a><a name="preallocatedports"></a> Domyślna alokacja portów

Każdy publiczny adres IP przypisany jako adres IP frontonu modułu równoważenia obciążenia ma 64 000 portów przystawcy adresów dla swoich członków puli zaplecza. Porty nie mogą być współużytkowane z członkami puli zaplecza. Zakres portów źródłowego przesyłania adresów sieciowych może być używany tylko przez pojedyncze wystąpienie wewnętrznej bazy danych, aby upewnić się, że pakiety powrotne są poprawnie kierowane. 

Zaleca się użycie jawnej reguły ruchu wychodzącego w celu skonfigurowania alokacji portów dla translatora adresów sieciowych. Ta zasada pozwoli zmaksymalizować liczbę portów dla wszystkich wystąpień zaplecza dostępnych dla połączeń wychodzących. 

W przypadku korzystania z automatycznej alokacji przychodzącego obiektu odruchowego za pomocą reguły równoważenia obciążenia tabela alokacji określi przydział portów.

W poniższej <a name="snatporttable"></a> tabeli przedstawiono alokacje wstępne portów adresów sieciowych dla warstw rozmiaru puli zaplecza:

| Rozmiar puli (wystąpienia maszyn wirtualnych) | Wstępnie przydzielony porty dla konfiguracji protokołu IP |
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 | 

>[!NOTE]
> Jeśli masz pulę zaplecza o maksymalnym rozmiarze 10, każde wystąpienie może mieć 64000/10 = 6 400 portów, jeśli zdefiniujesz jawną regułę wychodzącą. Zgodnie z powyższą tabelą każda z nich będzie miała 1 024 tylko w przypadku wybrania opcji alokacja automatyczna.

## <a name="outbound-rules-and-virtual-network-nat"></a><a name="outboundrules"></a> Reguły ruchu wychodzącego i translatora adresów sieciowych Virtual Network

Azure Load Balancer reguły ruchu wychodzącego i Virtual Network translatora adresów sieciowych są opcjami dostępnymi w sieci wirtualnej.

Aby uzyskać więcej informacji o regułach ruchu wychodzącego, zobacz [reguły ruchu wychodzącego](outbound-rules.md).

Aby uzyskać więcej informacji na temat usługi Azure Virtual Network translator adresów sieciowych, zobacz [co to jest usługa azure Virtual Network translator adresów sieciowych](../virtual-network/nat-overview.md).

## <a name="constraints"></a>Ograniczenia

*   Gdy połączenie jest bezczynne, a żadne nowe pakiety nie są wysyłane, porty zostaną wydane po 4 – 120 minutach.
  * Ten próg można skonfigurować za pośrednictwem reguł ruchu wychodzącego.
*   Każdy adres IP zapewnia 64 000 portów, które mogą być używane na potrzeby protokołu reportowego.
*   Każdy port może być używany zarówno dla połączeń TCP, jak i UDP z docelowym adresem IP
  * Port przychodzący protokołu UDP jest wymagany, czy port docelowy jest unikatowy. Dla każdego połączenia UDP z docelowym adresem IP jest używany jeden port protokołu IPSec.
  * W przypadku wielu połączeń z tym samym docelowym adresem IP można użyć portu protokołu IPSec.
*   Wyczerpanie elementu podrzędnego jest wykonywane, gdy wystąpienie wewnętrznej bazy danych jest uruchomione z danego portu. Moduł równoważenia obciążenia nadal może mieć nieużywane porty. Jeśli porty przystosowane do wystąpienia zaplecza przekroczą określone porty, nie będzie można nawiązać nowych połączeń wychodzących.

## <a name="next-steps"></a>Następne kroki

*   [Rozwiązywanie problemów z błędami połączenia wychodzącego z powodu wyczerpania elementu wiązania](./troubleshoot-outbound-connection.md)
*   [Przejrzyj metryki dotyczące translatora adresów sieciowych](./load-balancer-standard-diagnostics.md#how-do-i-check-my-snat-port-usage-and-allocation) i zapoznaj się z poprawnym sposobem filtrowania, dzielenia i wyświetlania.
