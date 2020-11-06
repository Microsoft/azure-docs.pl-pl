---
title: Reguły wychodzące Azure Load Balancer
description: W tym artykule wyjaśniono, jak skonfigurować reguły ruchu wychodzącego w celu kontrolowania ruchu internetowego za pomocą Azure Load Balancer.
services: load-balancer
author: asudbring
ms.service: load-balancer
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/13/2020
ms.author: allensu
ms.openlocfilehash: 947ecaa2efbfb013f1f3e8203d1c4296b9ca329f
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422165"
---
# <a name="outbound-rules-azure-load-balancer"></a><a name="outboundrules"></a>Reguły wychodzące Azure Load Balancer

Reguły ruchu wychodzącego umożliwiają jawne zdefiniowanie elementu Resources (translator adresów sieciowych) dla publicznego standardowego modułu równoważenia obciążenia. Ta konfiguracja umożliwia korzystanie z publicznych adresów IP modułu równoważenia obciążenia w celu zapewnienia wychodzącej łączności z Internetem dla wystąpień zaplecza.

Ta konfiguracja umożliwia:

* Zamaskowane IP
* Uproszczenie list dozwolonych.
* Zmniejsza liczbę publicznych zasobów IP do wdrożenia.

Reguły ruchu wychodzącego mają pełną kontrolę deklaratywną w porównaniu z wychodzącymi połączeniami internetowymi. Reguły ruchu wychodzącego umożliwiają skalowanie i dostosowanie tej możliwości do konkretnych potrzeb. 

Reguły ruchu wychodzącego będą następować tylko wtedy, gdy maszyna wirtualna zaplecza nie ma publicznego adresu IP na poziomie wystąpienia (ILPIP).

![Load Balancer reguły ruchu wychodzącego](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Korzystając z reguł ruchu **wychodzącego** , można jawnie zdefiniować zachowanie przychodzącego ruchu zwrotnego.

Reguły ruchu wychodzącego umożliwiają sterowanie:

* **Które maszyny wirtualne są tłumaczone na które publiczne adresy IP.**
     * Dwie reguły stanowiły pulę zaplecza A i korzystają z adresów IP a i B, a w puli zaplecza B są stosowane adresy IP C i D.
* **Sposób przydzielenia portów wychodzącego ruchu źródłowego.**
     * Pula zaplecza B jest jedyną pulą służącą do nawiązywania połączeń wychodzących, nadając jej wszystkie porty i brak do puli zaplecza A.
* **Protokoły, dla których ma zostać przewidziane tłumaczenie wychodzące.**
     * Pula zaplecza B potrzebuje portów UDP dla ruchu wychodzącego. Pula zaplecza A wymaga protokołu TCP. Nadaj portom TCP portów i UDP do B.
* **Czas trwania okresu bezczynności połączenia wychodzącego (4-120 minut).**
     * W przypadku długotrwałych połączeń z utrzymywaniem aktywności należy zarezerwować bezczynne porty dla długotrwałych połączeń przez maksymalnie 120 minut. Przyjęto założenie, że stare połączenia są porzucane i zwalniane w ciągu 4 minut na potrzeby nowych połączeń 
* **Czy należy wysyłać Resetowanie protokołu TCP przy użyciu limitu czasu bezczynności.**
     * Po upływie limitu czasu bezczynnych połączeń wysyłamy do klienta i serwera protokół TCP, aby wiadomo, że przepływ został porzucony?

## <a name="outbound-rule-definition"></a>Definicja reguły ruchu wychodzącego

Reguły ruchu wychodzącego są zgodne z tą samą znajomą składnią jak **frontend** Równoważenie obciążenia i reguły NAT dla ruchu przychodzącego:  +  **parameters**  +  **baza danych** frontonu. 

Reguła ruchu wychodzącego konfiguruje wychodzące NAT dla _wszystkich maszyn wirtualnych identyfikowanych przez pulę zaplecza_ , które mają zostać przetłumaczone na _fronton_.  

_Parametry_ zapewniają dodatkową kontrolę nad algorytmem NAT dla ruchu wychodzącego.

## <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Skalowanie ruchu wychodzącego NAT z wieloma adresami IP

Każdy dodatkowy adres IP dostarczony przez fronton oferuje dodatkowe 64 000 portów tymczasowych dla modułu równoważenia obciążenia, które mogą być używane jako porty protokołu źródłowego. 

Użyj wielu adresów IP do zaplanowania scenariuszy o dużej skali. Użyj reguł ruchu wychodzącego, aby ograniczyć [wyczerpanie adresów](troubleshoot-outbound-connection.md#snatexhaust). 

Można również użyć [publicznego prefiksu adresu IP](https://aka.ms/lbpublicipprefix) bezpośrednio z regułą wychodzącą. 

Publiczny prefiks adresu IP zwiększa skalowanie wdrożenia. Prefiks można dodać do listy dozwolonych przepływów pochodzących z zasobów platformy Azure. Konfigurację adresu IP frontonu można skonfigurować w ramach modułu równoważenia obciążenia, aby odwoływać się do prefiksu publicznego adresu IP.  

Moduł równoważenia obciążenia ma kontrolę nad publicznym prefiksem adresu IP. Reguła ruchu wychodzącego automatycznie będzie używać wszystkich publicznych adresów IP zawartych w publicznym prefiksie adresu IP dla połączeń wychodzących. 

Każdy adres IP w ramach publicznego prefiksu IP zapewnia dodatkowe 64 000 portów tymczasowych na adres IP dla usługi równoważenia obciążenia, które mają być używane jako porty protokołu źródłowego.

## <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Limit czasu bezczynności przepływu ruchu wychodzącego i Resetowanie protokołu TCP

Reguły ruchu wychodzącego zapewniają parametr konfiguracji służący do kontrolowania limitu czasu bezczynności przepływu wychodzącego i dopasowania go do potrzeb aplikacji. Domyślnie wychodzące limity czasu bezczynności to 4 minuty. Aby uzyskać więcej informacji, zobacz [Konfigurowanie limitów czasu bezczynności](load-balancer-tcp-idle-timeout.md). 

Domyślnym zachowaniem usługi równoważenia obciążenia jest odrzucanie przepływu w trybie dyskretnym, gdy osiągnięto limit czasu bezczynności dla ruchu wychodzącego. `enableTCPReset`Parametr umożliwia przewidywalną kontrolę i zachowanie aplikacji. Parametr określa, czy należy wysyłać dwukierunkowe Resetowanie TCP (TCP RST) przy limicie czasu limitu czasu bezczynności ruchu wychodzącego. 

Przejrzyj informacje [o limicie czasu bezczynności na potrzeby resetowania protokołu TCP](https://aka.ms/lbtcpreset) , co obejmuje dostępność regionów.

## <a name="securing-and-controlling-outbound-connectivity-explicitly"></a><a name="preventoutbound"></a>Jawne Zabezpieczanie i kontrolowanie łączności wychodzącej

Reguły równoważenia obciążenia zapewniają automatyczne Programowanie dla wychodzącego translatora adresów sieciowych. Niektóre scenariusze korzyści lub wymagają wyłączenia automatycznego programowania wychodzącego NAT przez regułę równoważenia obciążenia. Wyłączenie za pośrednictwem reguły pozwala kontrolować lub ograniczać zachowanie.  

Tego parametru można użyć na dwa sposoby:

1. Zapobieganie przychodzącemu adresowi IP dla wychodzącego odruchu. Wyłącz wychodzący magazyn danych w regule równoważenia obciążenia.
  
2. Dostosuj parametry wychodzącego ruchu **źródłowego** dla adresu IP używanego do ruchu przychodzącego i wychodzącego jednocześnie. Automatyczne wychodzące NAT musi być wyłączone, aby zezwolić regule wychodzącej na przejęcie kontroli. Aby zmienić przydzieloną portów adresów sieciowych dla ruchu przychodzącego, `disableOutboundSnat` należy także ustawić wartość true dla parametru. 

Operacja konfigurowania reguły wychodzącej zakończy się niepowodzeniem w przypadku próby ponownego zdefiniowania adresu IP używanego do obsługi ruchu przychodzącego.  Najpierw wyłącz wychodzące NAT reguły równoważenia obciążenia.

>[!IMPORTANT]
> Jeśli ustawisz ten parametr na wartość true, maszyna wirtualna nie będzie mogła mieć łączności wychodzącej, aby zdefiniować łączność wychodzącą.  Niektóre operacje związane z maszyną wirtualną lub aplikacją mogą zależeć od dostępnego połączenia wychodzącego. Zadbaj o to, aby zrozumieć zależności danego scenariusza i wziąć pod uwagę wpływ wprowadzania tej zmiany.

Czasami jest niepożądane, aby maszyna wirtualna mogła utworzyć przepływ wychodzący. Może istnieć wymóg, aby zarządzać miejscem, w którym znajdują się przepływy wychodzące, lub które miejsca docelowe zaczynają przepływy przychodzące. Użyj [sieciowych grup zabezpieczeń](../virtual-network/security-overview.md) , aby zarządzać miejscami docelowymi, do których dojdzie maszyna wirtualna. Użyj sieciowych grup zabezpieczeń, aby zarządzać, które publiczne miejsca docelowe zaczynają przepływy przychodzące.

Po zastosowaniu sieciowej grupy zabezpieczeń do maszyny wirtualnej z równoważeniem obciążenia należy zwrócić uwagę na [Tagi usługi](../virtual-network/security-overview.md#service-tags) i [domyślne reguły zabezpieczeń](../virtual-network/security-overview.md#default-security-rules). 

Upewnij się, że maszyna wirtualna może odbierać żądania sondowania kondycji z Azure Load Balancer.

Jeśli sieciowej grupy zabezpieczeń blokuje żądania sondy kondycji z domyślnego tagu AZURE_LOADBALANCER, sonda kondycji maszyny wirtualnej kończy się niepowodzeniem, a maszyna wirtualna zostanie oznaczona jako niedostępna. Moduł równoważenia obciążenia przestaje wysyłać Nowe przepływy do tej maszyny wirtualnej.

## <a name="scenarios-with-outbound-rules"></a>Scenariusze z regułami ruchu wychodzącego
        

### <a name="outbound-rules-scenarios"></a>Scenariusze reguł ruchu wychodzącego


* Skonfiguruj połączenia wychodzące do określonego zestawu publicznych adresów IP lub prefiksu.
* Modyfikowanie [alokacji](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) portów dla tego obiektu.
* Włącz tylko ruch wychodzący.
* Wychodzące NAT tylko dla maszyn wirtualnych (bez ruchu przychodzącego).
* Wychodzące NAT dla wewnętrznego modułu równoważenia obciążenia.
* Włącz protokoły UDP & protokołu TCP dla ruchu wychodzącego NAT przy użyciu publicznego standardowego modułu równoważenia obciążenia.


### <a name="scenario-1-configure-outbound-connections-to-a-specific-set-of-public-ips-or-prefix"></a><a name="scenario1out"></a>Scenariusz 1: Konfigurowanie połączeń wychodzących do określonego zestawu publicznych adresów IP lub prefiksu


#### <a name="details"></a>Szczegóły


Ten scenariusz służy do dostosowywania połączeń wychodzących, które pochodzą z zestawu publicznych adresów IP. Dodaj publiczne adresy IP lub prefiksy do listy dozwolonych lub zablokowanych w oparciu o pochodzenie.


Ten publiczny adres IP lub prefiks może być taki sam, jak używany przez regułę równoważenia obciążenia. 


Aby użyć innego publicznego adresu IP lub prefiksu niż używany przez regułę równoważenia obciążenia: 


1. Utwórz publiczny prefiks adresu IP lub publiczny adres IP.
2. Tworzenie publicznego, standardowego modułu równoważenia obciążenia 
3. Utwórz fronton odwołujący się do publicznego prefiksu adresu IP lub publicznego adresu IP, którego chcesz użyć. 
4. Ponowne użycie puli zaplecza lub utworzenie puli zaplecza i umieszczenie maszyn wirtualnych w puli zaplecza publicznego modułu równoważenia obciążenia
5. Skonfiguruj regułę ruchu wychodzącego w publicznym module równoważenia obciążenia, aby włączyć wychodzące NAT dla maszyn wirtualnych przy użyciu frontonu. Nie zaleca się używania reguły równoważenia obciążenia dla ruchu wychodzącego, wyłączając wychodzący plik zasad sieciowych dla reguły równoważenia obciążenia.


### <a name="scenario-2-modify-snat-port-allocation"></a><a name="scenario2out"></a>Scenariusz 2: modyfikowanie alokacji portów podrzędnego kodu [źródłowego](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources)


#### <a name="details"></a>Szczegóły


Korzystając z reguł ruchu wychodzącego, można dostroić [automatyczną alokację portu z użyciem puli zaplecza](load-balancer-outbound-connections.md#preallocatedports). 


Jeśli masz problemy z wyczerpaniem, Zwiększ liczbę portów przyciągania [adresów sieciowych](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) z uwzględnieniem wartości domyślnej 1024. 


Każdy publiczny adres IP współtworzy do 64 000 portów tymczasowych. Liczba maszyn wirtualnych w puli zaplecza określa liczbę portów dystrybuowanych do każdej maszyny wirtualnej. Jedna maszyna wirtualna w puli zaplecza ma dostęp do maksymalnie 64 000 portów. Dla dwóch maszyn wirtualnych można uzyskać [maksymalnie 32 000 portów](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) z użyciem reguły ruchu wychodzącego (2x 32 000 = 64 000). 


Korzystając z reguł ruchu wychodzącego, można dostrajać porty przydzielone domyślnie. Przydajesz więcej lub mniej niż jest to domyślna alokacja portu przydziałów [adresów sieciowych](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) . Każdy publiczny adres IP z frontonu reguły wychodzącej współużytkuje do 64 000 portów tymczasowych na potrzeby używania [jako portów](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) protokołu reportowego. 


Moduł równoważenia obciążenia oferuje porty podzbiorów [adresów](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) w wielokrotnościach 8. Jeśli podano wartość, która nie jest podzielna przez 8, operacja konfiguracji zostanie odrzucona. Każda reguła równoważenia obciążenia i Reguła ruchu przychodzącego NAT będą korzystały z zakresu 8 portów. Jeśli reguła równoważenia obciążenia lub ruchu przychodzącego NAT ma taki sam zakres 8 jak inny, nie będą używane żadne dodatkowe porty.


Jeśli podjęto próbę przyznania [więcej portów](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) protokołu reportowego, niż jest dostępne w oparciu o liczbę publicznych adresów IP, operacja konfiguracji zostanie odrzucona. Na przykład w przypadku nadania 10 000 portów na maszynę wirtualną i siedmiu maszyn wirtualnych w puli zaplecza mają jeden publiczny adres IP, konfiguracja zostanie odrzucona. Siedem pomnożone przez 10 000 przekracza limit portów 64 000. Dodaj więcej publicznych adresów IP do frontonu reguły ruchu wychodzącego, aby włączyć scenariusz. 


Przywróć [domyślną alokację portu](load-balancer-outbound-connections.md#preallocatedports) , określając wartość 0 dla liczby portów. Pierwsze wystąpienie maszyny wirtualnej 50 spowoduje uzyskanie portów 1024 51-100, a w przypadku wystąpienia maszyn wirtualnych zostanie 512 wyświetlonych co najwyżej Maksymalna liczba wystąpień. Aby uzyskać więcej informacji na temat domyślnego przydzielania portów adresów sieciowych, zobacz [tabela alokacji portów przydziałów](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).


### <a name="scenario-3-enable-outbound-only"></a><a name="scenario3out"></a>Scenariusz 3: Włącz tylko wychodzące


#### <a name="details"></a>Szczegóły


Użyj publicznego, standardowego modułu równoważenia obciążenia, aby zapewnić wychodzące NAT dla grupy maszyn wirtualnych. W tym scenariuszu należy użyć reguły ruchu wychodzącego niezależnie od skonfigurowanych dodatkowych reguł.


> [!NOTE]
> **Usługa Azure Virtual Network translator adresów sieciowych** może zapewnić łączność wychodzącą dla maszyn wirtualnych bez potrzeby modułu równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Virtual Network translator adresów sieciowych?](../virtual-network/nat-overview.md)

### <a name="scenario-4-outbound-nat-for-vms-only-no-inbound"></a><a name="scenario4out"></a>Scenariusz 4: wychodzące NAT tylko dla maszyn wirtualnych (bez ruchu przychodzącego)


> [!NOTE]
> **Usługa Azure Virtual Network translator adresów sieciowych** może zapewnić łączność wychodzącą dla maszyn wirtualnych bez potrzeby modułu równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Virtual Network translator adresów sieciowych?](../virtual-network/nat-overview.md)

#### <a name="details"></a>Szczegóły


W tym scenariuszu: Azure Load Balancer reguły ruchu wychodzącego i Virtual Network translatora adresów sieciowych są opcjami dostępnymi dla danych wychodzących z sieci wirtualnej.


1. Utwórz publiczny adres IP lub prefiks.
2. Utwórz publiczny standardowy moduł równoważenia obciążenia. 
3. Utwórz fronton związany z publicznym adresem IP lub prefiksem dedykowanym dla ruchu wychodzącego.
4. Utwórz pulę zaplecza dla maszyn wirtualnych.
5. Umieść maszyny wirtualne w puli zaplecza.
6. Skonfiguruj regułę ruchu wychodzącego, aby włączyć wychodzące NAT.



Użyj prefiksu lub publicznego adresu IP do [skalowania](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) portów. Dodaj źródło połączeń wychodzących do listy dozwolonych lub zablokowanych.



### <a name="scenario-5-outbound-nat-for-internal-standard-load-balancer"></a><a name="scenario5out"></a>Scenariusz 5: wychodzące NAT dla wewnętrznej usługi równoważenia obciążenia w warstwie Standardowa


> [!NOTE]
> **Usługa Azure Virtual Network translator adresów sieciowych** może zapewnić łączność wychodzącą dla maszyn wirtualnych korzystających z wewnętrznego, standardowego modułu równoważenia obciążenia. Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Virtual Network translator adresów sieciowych?](../virtual-network/nat-overview.md)

#### <a name="details"></a>Szczegóły


Łączność wychodząca jest niedostępna dla wewnętrznego modułu równoważenia obciążenia, dopóki nie zostanie jawnie zadeklarowana za pośrednictwem publicznych adresów IP na poziomie wystąpienia lub Virtual Network translatora adresów sieciowych lub przez skojarzenie członków puli zaplecza z konfiguracją modułu równoważenia obciążenia obsługującego tylko wychodzące. 


Aby uzyskać więcej informacji, zobacz [Konfiguracja modułu równoważenia obciążenia tylko dla ruchu wychodzącego](https://docs.microsoft.com/azure/load-balancer/egress-only).




### <a name="scenario-6-enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a><a name="scenario6out"></a>Scenariusz 6. Włączanie protokołów UDP & TCP dla ruchu wychodzącego NAT za pomocą publicznego standardowego modułu równoważenia obciążenia


#### <a name="details"></a>Szczegóły


W przypadku korzystania z publicznej standardowej usługi równoważenia obciążenia usługa automatycznego wychodzącego NAT jest zgodna z protokołem transportu reguły równoważenia obciążenia. 


1. Wyłącz [przychodzący](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#-sharing-ports-across-resources) magazyn danych w regule równoważenia obciążenia. 
2. Skonfiguruj regułę ruchu wychodzącego w tym samym module równoważenia obciążenia.
3. Ponownie Użyj puli zaplecza już używanej przez maszyny wirtualne. 
4. Określ "Protokół": "All" jako część reguły ruchu wychodzącego. 


Gdy są używane tylko reguły NAT dla ruchu przychodzącego, nie jest dostarczany wychodzący translator adresów sieciowych. 


1. Umieść maszyny wirtualne w puli zaplecza.
2. Zdefiniuj co najmniej jedną konfigurację adresu IP frontonu z publicznymi adresami IP lub prefiksem publicznego adresu IP 
3. Skonfiguruj regułę ruchu wychodzącego w tym samym module równoważenia obciążenia. 
4. Określ "Protocol": "All" jako część reguły ruchu wychodzącego


## <a name="limitations"></a>Ograniczenia

- Maksymalna liczba użytecznych portów tymczasowych na adres IP frontonu to 64 000.
- Zakres konfigurowalnego limitu czasu bezczynności wynosi od 4 do 120 minut (240 do 7200 sekund).
- Moduł równoważenia obciążenia nie obsługuje protokołu ICMP dla wychodzącego translatora adresów sieciowych.
- Reguły ruchu wychodzącego mogą być stosowane tylko do podstawowej konfiguracji adresu IP karty sieciowej.  Nie można utworzyć reguły ruchu wychodzącego dla pomocniczego adresu IP maszyny wirtualnej lub urządzenie WUS. Obsługiwane są wiele kart sieciowych.
- Wszystkie maszyny wirtualne w ramach **zestawu dostępności** muszą zostać dodane do puli zaplecza na potrzeby łączności wychodzącej. 
- Wszystkie maszyny wirtualne w ramach **zestawu skalowania maszyn wirtualnych** należy dodać do puli zaplecza na potrzeby łączności wychodzącej.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [usłudze Azure usługa Load Balancer w warstwie Standardowa](load-balancer-overview.md)
- Zapoznaj się z [często zadawanymi pytaniami dotyczącymi Azure Load Balancer](load-balancer-faqs.md)

