---
title: Reguły ruchu wychodzącego — Azure Load Balancer
description: Za pomocą tej ścieżki szkoleniowej Rozpocznij korzystanie z reguł ruchu wychodzącego w celu definiowania translacji adresów sieciowych dla ruchu wychodzącego.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 7/17/2019
ms.author: allensu
ms.openlocfilehash: d419c213b3bcfef3631d68eb9d4cb485291bed31
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78304195"
---
# <a name="load-balancer-outbound-rules"></a>Load Balancer reguły ruchu wychodzącego

Azure Load Balancer zapewnia łączność wychodzącą z sieci wirtualnej oprócz ruchu przychodzącego.  Reguły ruchu wychodzącego ułatwiają konfigurowanie wychodzącej translacji adresów sieciowych [Usługa Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md)publicznej.  Masz pełną kontrolę deklaratywną nad łącznością wychodzącą w celu skalowania i dostrajania tej możliwości do konkretnych potrzeb.

![Load Balancer reguły ruchu wychodzącego](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Load Balancer reguły ruchu wychodzącego umożliwiają: 
- Zdefiniuj wychodzące NAT od podstaw.
- skalowanie i dostrajanie zachowania istniejącego wychodzącego translatora adresów sieciowych. 

Reguły ruchu wychodzącego umożliwiają sterowanie:
- które maszyny wirtualne powinny być tłumaczone na które publiczne adresy IP. 
- sposób przydzielenia [portów wychodzącego ruchu źródłowego](load-balancer-outbound-connections.md#snat) .
- protokoły, dla których ma zostać przewidziane tłumaczenie wychodzące.
- czas trwania okresu bezczynności połączenia wychodzącego (4-120 minut).
- Czy należy wysyłać Resetowanie protokołu TCP po upływie limitu czasu bezczynności

Reguły ruchu wychodzącego rozszerzają [Scenariusz 2](load-balancer-outbound-connections.md#lb) opisany w artykule [połączenia wychodzące](load-balancer-outbound-connections.md) , a priorytet scenariusza pozostaje taki, jak jest.

## <a name="outbound-rule"></a>Reguła ruchu wychodzącego

Podobnie jak w przypadku wszystkich reguł Load Balancer, reguły wychodzące mają taką samą znaną składnię jak równoważenie obciążenia i reguły NAT dla ruchu przychodzącego:

**frontend** + **parameters** + **Pula zaplecza** parametrów frontonu

Reguła ruchu wychodzącego konfiguruje wychodzące NAT dla _wszystkich maszyn wirtualnych identyfikowanych przez pulę zaplecza_ , które mają zostać przetłumaczone na _fronton_.  I _Parametry_ zapewniają dodatkową kontrolę nad algorytmem NAT dla ruchu wychodzącego.

Wersja interfejsu API "2018-07-01" zezwala na strukturę definicji reguły ruchu wychodzącego w następujący sposób:

```json
      "outboundRules": [
        {
          "frontendIPConfigurations": [ list_of_frontend_ip_configuations ],
          "allocatedOutboundPorts": number_of_SNAT_ports,
          "idleTimeoutInMinutes": 4 through 66,
          "enableTcpReset": true | false,
          "protocol": "Tcp" | "Udp" | "All",
          "backendAddressPool": backend_pool_reference,
        }
      ]
```

>[!NOTE]
>Obowiązująca Konfiguracja NAT dla ruchu wychodzącego jest złożona ze wszystkich reguł ruchu wychodzącego i reguł równoważenia obciążenia. Reguły ruchu wychodzącego są przyrostowe w regułach równoważenia obciążenia. Zapoznaj się z tematem [wyłączanie wychodzącego NAT dla reguły równoważenia obciążenia](#disablesnat) , aby zarządzać efektywnym wychodzącym TRANSLACJą NAT w przypadku zastosowania wielu reguł do maszyny wirtualnej. Podczas definiowania reguły ruchu wychodzącego, która używa tego samego publicznego adresu IP jako reguły równoważenia obciążenia, należy [wyłączyć wychodzące](#disablesnat) obiektu zasad sieciowych.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a>Skalowanie ruchu wychodzącego NAT z wieloma adresami IP

Reguła ruchu wychodzącego może być używana z tylko jednym publicznym adresem IP, ale reguły ruchu wychodzącego ułatwiają skalowanie w ramach ruchu wychodzącego NAT. Można użyć wielu adresów IP do zaplanowania scenariuszy o dużej skali i można użyć reguł ruchu wychodzącego, aby wyeliminować wzorce podatności na ruch [wydechowy](load-balancer-outbound-connections.md#snatexhaust) .  

Każdy dodatkowy adres IP dostarczony przez fronton zapewnia 64 000 portów tymczasowych Load Balancer do użycia jako portów protokołu integracyjnego. Mimo że reguły równoważenia obciążenia lub ruchu przychodzącego NAT mają jedną fronton, reguła wychodząca rozszerza koncepcję frontonu i zezwala na wiele frontonów dla każdej reguły.  W przypadku wielu frontonów dla każdej reguły liczba dostępnych portów adresów IP jest mnożona przy użyciu każdego publicznego adresu i można obsługiwać duże scenariusze.

Ponadto można użyć [publicznego prefiksu adresu IP](https://aka.ms/lbpublicipprefix) bezpośrednio z regułą wychodzącą.  Używanie publicznego prefiksu adresu IP zapewnia łatwiejsze skalowanie i uproszczone Wyświetlanie listy przepływów pochodzących ze wdrożenia platformy Azure. Konfigurację adresu IP frontonu można skonfigurować w ramach zasobu Load Balancer, aby odwoływać się bezpośrednio do prefiksu publicznego adresu IP.  Pozwala to Load Balancer wyłącznej kontroli nad publicznym prefiksem adresu IP, a reguła wychodząca automatycznie będzie używać wszystkich publicznych adresów IP zawartych w publicznym prefiksie adresu IP dla połączeń wychodzących.  Każdy adres IP należący do zakresu publicznego prefiksu adresu IP zapewnia 64 000 portów tymczasowych na adres IP, aby Load Balancer używać jako portów podzestawów adresów.   

W przypadku korzystania z tej opcji nie można utworzyć pojedynczych zasobów publicznego adresu IP, które są tworzone na podstawie tego publicznego prefiksu IP, ponieważ reguła ruchu wychodzącego musi mieć pełną kontrolę nad publicznym prefiksem adresu IP.  Jeśli potrzebujesz bardziej precyzyjnej kontroli, możesz utworzyć pojedynczy zasób publicznego adresu IP na podstawie prefiksu publicznego adresu IP i przypisać wiele publicznych adresów IP pojedynczo do frontonu reguły wychodzącej.

### <a name="tune-snat-port-allocation"></a><a name="snatports"></a>Dostrajanie alokacji portów dla współdzielonego

Możesz użyć reguł ruchu wychodzącego, aby dostroić [automatyczne przydzielanie portów adresów sieciowych w oparciu o rozmiar puli zaplecza](load-balancer-outbound-connections.md#preallocatedports) i przydzielić więcej lub mniej niż jest to automatyczna alokacja portu.

Użyj następującego parametru, aby przydzielić porty protokołu IPSec 10 000 na maszynę wirtualną (Konfiguracja IP karty sieciowej).
 

          "allocatedOutboundPorts": 10000

Każdy publiczny adres IP ze wszystkich frontonów reguły wychodzącej współużytkuje do 64 000 tymczasowych portów do użycia jako porty protokołu reportowego.  Load Balancer przydziela porty przystawek adresów sieciowych w wielokrotnościach 8. Jeśli podano wartość, która nie jest podzielna przez 8, operacja konfiguracji zostanie odrzucona.  Jeśli podjęto próbę przydzielenia większej liczby portów ze standardem adresów IP, które są dostępne w oparciu o liczbę publicznych adresów, operacja konfiguracji zostanie odrzucona.  Na przykład w przypadku przydzielenia 10 000 portów na maszynę wirtualną i 7 maszyn wirtualnych w puli zaplecza współużytkują jeden publiczny 64 000 > 10 000 adres IP  Aby włączyć ten scenariusz, można dodać więcej publicznych adresów IP do frontonu reguły ruchu wychodzącego.

Można przywrócić [automatyczną alokację portu na podstawie rozmiaru puli zaplecza](load-balancer-outbound-connections.md#preallocatedports) , określając 0 dla liczby portów. W takim przypadku pierwsze wystąpienia maszyn wirtualnych 50 będą mieć 1024 portów, a 51-100 wystąpienia maszyn wirtualnych otrzymają 512 i tak dalej, zgodnie z tabelą.

### <a name="control-outbound-flow-idle-timeout"></a><a name="idletimeout"></a>Kontrola limitu czasu bezczynności przepływu wychodzącego

Reguły ruchu wychodzącego zapewniają parametr konfiguracji służący do kontrolowania limitu czasu bezczynności przepływu wychodzącego i dopasowania go do potrzeb aplikacji.  Domyślnie wychodzące limity czasu bezczynności to 4 minuty.  Parametr przyjmuje wartość od 4 do 120 do określonej liczby minut dla limitu czasu bezczynności dla przepływów pasujących do tej konkretnej reguły.

Użyj następującego parametru, aby ustawić limit czasu bezczynności dla ruchu wychodzącego na 1 godzinę:

          "idleTimeoutInMinutes": 60

### <a name="enable-tcp-reset-on-idle-timeout"></a><a name="tcprst"></a><a name="tcpreset"></a> Włącz Resetowanie limitu czasu bezczynności dla protokołu TCP

Domyślnym zachowaniem Load Balancer jest odrzucanie przepływu w trybie dyskretnym, gdy osiągnięto limit czasu bezczynności wychodzący.  Za pomocą parametru enableTCPReset można włączyć bardziej przewidywalne zachowanie aplikacji i kontrolować, czy należy wysyłać dwukierunkowe Resetowanie protokołu TCP (TCP RST) w czasie poza limit czasu bezczynności ruchu wychodzącego. 

Użyj następującego parametru, aby włączyć Resetowanie protokołu TCP dla reguły ruchu wychodzącego:

           "enableTcpReset": true

Przejrzyj informacje [o limicie czasu bezczynności na potrzeby resetowania protokołu TCP](https://aka.ms/lbtcpreset) , co obejmuje dostępność regionów.

### <a name="support-both-tcp-and-udp-transport-protocols-with-a-single-rule"></a><a name="proto"></a>Obsługa protokołów transportu TCP i UDP przy użyciu jednej reguły

Prawdopodobnie chcesz użyć "All" dla protokołu transportowego reguły ruchu wychodzącego, ale możesz również zastosować regułę ruchu wychodzącego do określonego protokołu transportowego, a także w razie potrzeby.

Użyj następującego parametru, aby ustawić protokół na TCP i UDP:

          "protocol": "All"

### <a name="disable-outbound-nat-for-a-load-balancing-rule"></a><a name="disablesnat"></a>Wyłącz wychodzące NAT dla reguły równoważenia obciążenia

Jak wspomniano wcześniej, reguły równoważenia obciążenia zapewniają automatyczne Programowanie dla wychodzącego translatora adresów sieciowych. Jednak niektóre scenariusze mogą korzystać z funkcji automatycznego programowania wychodzącego NAT przez regułę równoważenia obciążenia, co pozwala na kontrolowanie lub Uściślanie zachowania.  Reguły ruchu wychodzącego mają scenariusze, które są ważne, aby zatrzymać automatyczne wychodzące programowanie NAT.

Tego parametru można użyć na dwa sposoby:
- Opcjonalne pomijanie przy użyciu przychodzącego adresu IP dla wychodzącego translatora adresów sieciowych.  Reguły ruchu wychodzącego są przyrostowe do reguł równoważenia obciążenia i z tym zestawem parametrów Reguła ruchu wychodzącego jest w formancie.
  
- Dostosuj wychodzące parametry NAT adresu IP używanego do ruchu przychodzącego i wychodzącego jednocześnie.  Automatyczne wychodzące programowanie NAT musi być wyłączone, aby zezwolić regule wychodzącej na przejęcie kontroli.  Na przykład w celu zmiany alokacji portu podrzędnego adresu reportowego używany również dla ruchu przychodzącego ten parametr musi być ustawiony na wartość true.  Jeśli podjęto próbę użycia reguły wychodzącej w celu ponownego zdefiniowania parametrów adresu IP używanego do obsługi ruchu przychodzącego i nie wydano wychodzącego programowania NAT dla reguły równoważenia obciążenia, operacja konfigurowania reguły wychodzącej zakończy się niepowodzeniem.

>[!IMPORTANT]
> Jeśli ustawisz ten parametr na wartość true, maszyna wirtualna nie będzie mogła mieć łączności wychodzącej (lub [scenariusza publicznego adresu IP na poziomie wystąpienia](load-balancer-outbound-connections.md#ilpip) ), aby zdefiniować łączność wychodzącą.  Niektóre operacje związane z maszyną wirtualną lub aplikacją mogą zależeć od dostępnego połączenia wychodzącego. Zadbaj o to, aby zrozumieć zależności danego scenariusza i wziąć pod uwagę wpływ wprowadzania tej zmiany.

Dla reguły równoważenia obciążenia można wyłączyć ruch wychodzący z tego parametru konfiguracji:

```json
      "loadBalancingRules": [
        {
          "disableOutboundSnat": true
        }
      ]
```

Parametr disableOutboundSNAT ma wartość domyślną false, co oznacza, że reguła równoważenia **obciążenia zapewnia automatyczne** wychodzące NAT jako obraz lustrzany konfiguracji reguły równoważenia obciążenia.  

Jeśli ustawisz wartość disableOutboundSnat na true w regule równoważenia obciążenia, reguła równoważenia obciążenia zwalnia kontrolę nad automatycznym wychodzącym programowaniem NAT.  Wychodzące przychodzącego ruchu sieciowego w wyniku reguły równoważenia obciążenia jest wyłączone.

### <a name="reuse-existing-or-define-new-backend-pools"></a>Ponowne używanie istniejących lub Definiowanie nowych pul zaplecza

Reguły ruchu wychodzącego nie wprowadzają nowej koncepcji w celu zdefiniowania grupy maszyn wirtualnych, do których ma zostać zastosowana reguła.  Zamiast tego ponownie używają koncepcji puli zaplecza, która jest również używana w regułach równoważenia obciążenia. Można jej użyć do uproszczenia konfiguracji przez ponowne użycie istniejącej definicji puli zaplecza lub utworzenie jednej dla reguły wychodzącej.

## <a name="scenarios"></a>Scenariusze

### <a name="groom-outbound-connections-to-a-specific-set-of-public-ip-addresses"></a><a name="groom"></a>Oczyść połączenia wychodzące do określonego zestawu publicznych adresów IP

Możesz użyć reguły ruchu wychodzącego, aby przetworzyć połączenia wychodzące w taki sposób, aby były wyświetlane z określonego zestawu publicznych adresów IP w celu ułatwienia listy dozwolonych scenariuszy.  Ten źródłowy publiczny adres IP może być taki sam, jak używany przez regułę równoważenia obciążenia lub inny zestaw publicznych adresów IP, niż jest używany przez regułę równoważenia obciążenia.  

1. Tworzenie [publicznego prefiksu adresu IP](https://aka.ms/lbpublicipprefix) (lub publicznych adresów IP z prefiksu publicznego adresu IP)
2. Tworzenie publicznej usługi Load Balancer w warstwie Standardowa
3. Utwórz frontony odwołujące się do publicznego prefiksu IP (lub publicznych adresów IP), których chcesz użyć
4. Użyj ponownie puli zaplecza lub Utwórz pulę zaplecza i umieść maszyny wirtualne w puli zaplecza Load Balancer publicznej
5. Skonfiguruj regułę ruchu wychodzącego na Load Balancer publicznej w celu zaprogramowania wychodzącego translatora adresów sieciowych dla tych maszyn wirtualnych przy użyciu frontonów
   
Jeśli nie chcesz, aby reguła równoważenia obciążenia była używana dla ruchu wychodzącego, należy [wyłączyć wychodzące elementu reźródłowego](#disablesnat) dla reguły równoważenia obciążenia.

### <a name="modify-snat-port-allocation"></a><a name="modifysnat"></a>Modyfikuj przydział portu dla współdzielonego

Korzystając z reguł ruchu wychodzącego, można dostroić [automatyczną alokację portu z użyciem puli zaplecza](load-balancer-outbound-connections.md#preallocatedports).

Na przykład jeśli masz dwie maszyny wirtualne, które współużytkują jeden publiczny adres IP dla wychodzącego translatora adresów sieciowych, możesz zwiększyć liczbę portów przyznanych z domyślnych portów 1024, jeśli występuje wyczerpanie. Każdy publiczny adres IP może współtworzyć maksymalnie 64 000 portów tymczasowych.  W przypadku skonfigurowania reguły wychodzącej z jednym frontonem publicznego adresu IP można rozpowszechnić łącznie 64 000 portów do maszyn wirtualnych w puli zaplecza.  W przypadku dwóch maszyn wirtualnych maksymalnie 32 000 portów przydziałów adresów sieciowych można przydzielyć za pomocą reguły ruchu wychodzącego (2x 32 000 = 64 000).

Przejrzyj [połączenia wychodzące](load-balancer-outbound-connections.md) i szczegółowe informacje dotyczące sposobu przydzielania i używania portów podłączania [adresów sieciowych](load-balancer-outbound-connections.md#snat) .

### <a name="enable-outbound-only"></a><a name="outboundonly"></a>Włącz tylko wychodzące

Aby zapewnić wychodzące NAT dla grupy maszyn wirtualnych, można użyć usługa Load Balancer w warstwie Standardowa publicznej. W tym scenariuszu można użyć reguły wychodzącej sama przez siebie bez potrzeby stosowania dodatkowych reguł.

#### <a name="outbound-nat-for-vms-only-no-inbound"></a>Wychodzące NAT tylko dla maszyn wirtualnych (bez ruchu przychodzącego)

Zdefiniuj usługa Load Balancer w warstwie Standardowa publiczny, umieść maszyny wirtualne w puli zaplecza i skonfiguruj regułę ruchu wychodzącego w celu zaprogramowania wychodzących NAT i oczyść połączenia wychodzące z określonego publicznego adresu IP. Możesz również użyć publicznego prefiksu IP Uprość białe, aby wyświetlić listę źródeł połączeń wychodzących.

1. Utwórz publiczną usługa Load Balancer w warstwie Standardowa.
2. Utwórz pulę zaplecza i umieść maszyny wirtualne w puli zaplecza Load Balancer publicznej.
3. Skonfiguruj regułę ruchu wychodzącego na Load Balancer publicznej w celu zaprogramowania wychodzącego translatora adresów sieciowych dla tych maszyn wirtualnych.

#### <a name="outbound-nat-for-internal-standard-load-balancer-scenarios"></a>Wychodzące translatory adresów sieciowych dla wewnętrznych scenariuszy usługa Load Balancer w warstwie Standardowa

W przypadku korzystania z usługa Load Balancer w warstwie Standardowa wewnętrznego, wychodzący NAT nie jest dostępny do momentu jawnego zadeklarowania łączności wychodzącej. Można zdefiniować łączność wychodzącą przy użyciu reguły ruchu wychodzącego, aby utworzyć łączność wychodzącą dla maszyn wirtualnych za wewnętrzną usługa Load Balancer w warstwie Standardowa, wykonując następujące kroki:

1. Utwórz publiczną usługa Load Balancer w warstwie Standardowa.
2. Utwórz pulę zaplecza i umieść maszyny wirtualne w puli zaplecza Load Balancer publicznej oprócz wewnętrznego Load Balancer.
3. Skonfiguruj regułę ruchu wychodzącego na Load Balancer publicznej w celu zaprogramowania wychodzącego translatora adresów sieciowych dla tych maszyn wirtualnych.

#### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a>Włącz protokoły UDP & protokołu TCP dla ruchu wychodzącego NAT z usługa Load Balancer w warstwie Standardowa publiczny

- W przypadku korzystania z usługa Load Balancer w warstwie Standardowa publicznego automatyczne wychodzące programowanie NAT jest zgodne z protokołem transportu reguły równoważenia obciążenia.  

   1. Wyłącz przychodzący magazyn danych w regule równoważenia obciążenia.
   2. Skonfiguruj regułę wychodzącą na tym samym Load Balancer.
   3. Ponownie Użyj puli zaplecza już używanej przez maszyny wirtualne.
   4. Określ "Protokół": "All" jako część reguły ruchu wychodzącego.

- Gdy są używane tylko reguły NAT dla ruchu przychodzącego, nie jest dostarczany wychodzący translator adresów sieciowych.

   1. Umieść maszyny wirtualne w puli zaplecza.
   2. Zdefiniuj co najmniej jedną konfigurację adresu IP frontonu z publicznymi adresami IP lub prefiksem publicznego adresu IP.
   3. Skonfiguruj regułę wychodzącą na tym samym Load Balancer.
   4. Określ "Protocol": "All" jako część reguły ruchu wychodzącego

## <a name="limitations"></a>Ograniczenia

- Maksymalna liczba użytecznych portów tymczasowych na adres IP frontonu to 64 000.
- Zakres konfigurowalnego limitu czasu bezczynności wynosi od 4 do 120 minut (240 do 7200 sekund).
- Load Balancer nie obsługuje protokołu ICMP dla wychodzącego translatora adresów sieciowych.
- Reguły ruchu wychodzącego mogą być stosowane tylko do podstawowej konfiguracji adresu IP karty sieciowej.  Obsługiwane są wiele kart sieciowych.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat korzystania z [Load Balancer dla połączeń wychodzących](load-balancer-outbound-connections.md).
- Dowiedz się więcej na temat [Usługa Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md).
- Informacje o [dwukierunkowym resetowaniu protokołu TCP przy bezczynności](load-balancer-tcp-reset.md).
- [Skonfiguruj reguły ruchu wychodzącego za pomocą interfejsu wiersza polecenia platformy Azure 2,0](configure-load-balancer-outbound-cli.md).
