---
title: Połączenia wychodzące
titleSuffix: Azure Load Balancer
description: W tym artykule opisano, jak platforma Azure umożliwia maszynom wirtualnym komunikowanie się z publicznymi usługami internetowymi.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: contperfq1
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/30/2020
ms.author: allensu
ms.openlocfilehash: d778b3ae0889ea0bf9cc38ca5813ac61fc5fcdbe
ms.sourcegitcommit: ffa7a269177ea3c9dcefd1dea18ccb6a87c03b70
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91595643"
---
# <a name="outbound-connections"></a>Połączenia wychodzące

Azure Load Balancer zapewnia łączność wychodzącą za pośrednictwem różnych mechanizmów. W tym artykule opisano scenariusze i sposoby zarządzania nimi. 


## <a name="scenarios"></a>Scenariusze

* Maszyna wirtualna z publicznym adresem IP.
* Maszyna wirtualna bez publicznego adresu IP.
* Maszyna wirtualna bez publicznego adresu IP i bez standardowej usługi równoważenia obciążenia.

### <a name="virtual-machine-with-public-ip"></a><a name="scenario1"></a>Maszyna wirtualna z publicznym adresem IP

| Związku | Metoda | Protokoły IP |
| ---------- | ------ | ------------ |
| Publiczny moduł równoważenia obciążenia lub autonomiczny | [Resourceer (translator adresów sieciowych)](#snat) </br> Niepoprawna [(obiekt maskujący)](#pat) nie jest używany. | TCP (Transmission Control Protocol) </br> UDP (User Datagram Protocol) </br> ICMP (Internet Control Message Protocol) </br> ESP (Hermetyzowanie ładunku zabezpieczeń) |

#### <a name="description"></a>Opis

Platforma Azure używa publicznego adresu IP przypisanego do konfiguracji protokołu IP karty sieciowej wystąpienia dla wszystkich przepływów wychodzących. Dla tego wystąpienia są dostępne wszystkie porty tymczasowe. Nie ma znaczenia, czy maszyna wirtualna ma Równoważenie obciążenia, czy nie. Ten scenariusz ma pierwszeństwo przed innymi. 

Publiczny adres IP przypisany do maszyny wirtualnej jest relacją 1:1 (a nie 1: wiele) i zaimplementowaną jako bezstanowe urządzenie NAT 1:1.

### <a name="virtual-machine-without-public-ip"></a><a name="scenario2"></a>Maszyna wirtualna bez publicznego adresu IP

| Związku | Metoda | Protokoły IP |
| ------------ | ------ | ------------ |
| Publiczny moduł równoważenia obciążenia | Korzystanie z frontonu modułu równoważenia obciążenia [dla](#snat) elementu [reportowego z magazynem (podszywającanie portów)](#pat).| TCP </br> UDP |

#### <a name="description"></a>Opis

Zasób modułu równoważenia obciążenia jest skonfigurowany z regułą modułu równoważenia obciążenia. Ta reguła służy do tworzenia linku między publicznym serwerem frontonu IP a pulą zaplecza. 

Jeśli ta konfiguracja reguły nie zostanie ukończona, zachowanie jest zgodne z opisem w scenariuszu 3. 

Reguła z odbiornikiem nie jest wymagana do pomyślnego przeprowadzenia sondy kondycji.

Gdy maszyna wirtualna tworzy przepływ wychodzący, platforma Azure tłumaczy źródłowy adres IP na publiczny adres IP frontonu modułu równoważenia obciążenia. To tłumaczenie odbywa [się za](#snat)pośrednictwem protokołu NAT. 

Porty tymczasowe publicznego adresu IP frontonu modułu równoważenia obciążenia są używane do rozróżniania poszczególnych przepływów pochodzących z maszyny wirtualnej. W przypadku tworzenia przepływów ruchu wychodzącego w ramach strumienia danych dynamicznie [przydzielono wstępnie przydzieloną porty](#preallocatedports) tymczasowe. 

W tym kontekście porty, które są używane do przystawcy adresów sieciowych, są nazywane portami. Porty przydziałów adresów sieciowych są wstępnie przydzielone zgodnie z opisem w [tabeli alokacji domyślnych portów](#snatporttable).

### <a name="virtual-machine-without-public-ip-and-without-standard-load-balancer"></a><a name="scenario3"></a>Maszyna wirtualna bez publicznego adresu IP i bez usługi równoważenia obciążenia w warstwie Standardowa

| Związku | Metoda | Protokoły IP |
| ------------ | ------ | ------------ |
|Brak </br> Podstawowa usługa równoważenia obciążenia | Reportcy [adresów sieciowych](#snat) z [zamaskowanem portów](#pat)| TCP </br> UDP | 

#### <a name="description"></a>Opis

Gdy maszyna wirtualna tworzy przepływ wychodzący, platforma Azure tłumaczy źródłowy adres IP na publiczny adres IP. Ten publiczny adres IP **nie jest konfigurowalny** i nie można go zarezerwować. Ten adres nie jest liczony pod względem limitu zasobów publicznego adresu IP subskrypcji. 

Publiczny adres IP zostanie wywnioskowany, a w przypadku ponownego wdrożenia: 

* Maszyna wirtualna
* Zestaw dostępności
* Zestaw skalowania maszyn wirtualnych  

Nie należy używać tego scenariusza do dodawania adresów IP do listy dozwolonych. Użyj scenariusza 1 lub 2, gdzie jawnie deklarujesz zachowanie wychodzące. Porty podrzędnego [translatora adresów sieciowych](#snat) są wstępnie przydzielone zgodnie z opisem w [tabeli alokacji domyślnych portów](#snatporttable).



## <a name="port-allocation-algorithm"></a><a name="preallocatedports"></a>Algorytm alokacji portów

Platforma Azure używa algorytmu w celu określenia liczby dostępnych wstępnie [przyznanych](#snat) portów. Algorytm opiera się na liczbie portów w rozmiarze puli zaplecza. 

Dla każdego publicznego adresu IP skojarzonego z modułem równoważenia obciążenia [64 000 porty są dostępne jako porty](#snat) . Ta sama liczba portów przydziałów [adresów sieciowych](#snat) jest wstępnie przydzielna dla protokołów UDP i TCP. Porty są używane niezależnie od protokołu IP. 

Użycie [portu przez](#snat) przychodzący ruch jest różny w zależności od tego, czy przepływ to UDP, czy TCP. 

Porty są używane dynamicznie do wstępnie przydzielonych limitów.  Porty są uwalniane po zamknięciu przepływu lub przekroczeniu limitu czasu bezczynności.

Aby uzyskać więcej informacji na temat limitów czasu bezczynności, zobacz [Rozwiązywanie połączeń wychodzących w Azure Load Balancer](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) 

Porty są używane tylko wtedy, gdy konieczne jest przeprowadzenie unikatowych przepływów.

### <a name="dynamic-snat-ports-preallocation"></a><a name="snatporttable"></a> Wstępne przydzielanie portów dynamicznego translatora adresów sieciowych

W poniższej tabeli przedstawiono alokacje wstępne portów [adresów sieciowych](#snat) dla warstw rozmiaru puli zaplecza:

| Rozmiar puli (wystąpienia maszyn wirtualnych) | Wstępnie przydzielony porty dla konfiguracji protokołu IP |
| --- | --- |
| 1-50 | 1 024 |
| 51-100 | 512 |
| 101-200 | 256 |
| 201-400 | 128 |
| 401-800 | 64 |
| 801-1000 | 32 |

Zmiana rozmiaru puli zaplecza może mieć wpływ na niektóre ustanowione przepływy:

- Rozmiar puli zaplecza zwiększa przejścia wyzwalacza do następnej warstwy. Połowa wstępnie przydzielonych [SNAT](#snat) portów podlegającego przydzieleniu zostanie ododzyskiwana podczas przejścia do następnej warstwy. 

- Przepływy skojarzone z przekroczeniem limitu czasu i zamknięcia portu połączonego strumienia [adresów](#snat) . Należy ponownie nawiązać te przepływy. Jeśli zostanie podjęta próba nowego przepływu, przepływ zakończy się pomyślnie, o ile dostępne są wstępnie przydzieloną porty.

- Jeśli rozmiar puli zaplecza obniży się i przechodzi do niższej warstwy, zwiększa się liczba [dostępnych portów](#snat) . Nie ma to wpływu na [istniejące porty i](#snat) ich odpowiednie przepływy.

## <a name="outbound-rules"></a><a name="outboundrules"></a>Reguły ruchu wychodzącego

 Reguły ruchu wychodzącego umożliwiają konfigurację wychodzącej translacji adresów sieciowych publicznego [modułu równoważenia obciążenia](load-balancer-standard-overview.md).  

> [!NOTE]
> **Usługa Azure Virtual Network translator adresów sieciowych** może zapewnić łączność wychodzącą dla maszyn wirtualnych w sieci wirtualnej.  Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Virtual Network translator adresów sieciowych?](../virtual-network/nat-overview.md)

Masz pełną kontrolę deklaratywną nad łącznością wychodzącą, która umożliwia skalowanie i dostosowanie tej możliwości do Twoich potrzeb.

![Reguły ruchu wychodzącego modułu równoważenia obciążenia](media/load-balancer-outbound-rules-overview/load-balancer-outbound-rules.png)

Przy użyciu reguł ruchu wychodzącego można użyć modułu równoważenia obciążenia, aby zdefiniować wychodzące NAT od podstaw. Możesz również skalować i dostrajać zachowanie istniejącego wychodzącego translatora adresów sieciowych.

Reguły ruchu wychodzącego umożliwiają sterowanie:

- Które maszyny wirtualne powinny być tłumaczone na które publiczne adresy IP.
- Jak należy określić porty wychodzącego ruchu [źródłowego](#snat) .
- Protokoły, dla których ma zostać przewidziane tłumaczenie wychodzące.
- Czas trwania okresu bezczynności połączenia wychodzącego (4-120 minut).
- Czy należy wysyłać Resetowanie protokołu TCP po upływie limitu czasu bezczynności
- Protokoły transportowe TCP i UDP z pojedynczą regułą

### <a name="outbound-rule-definition"></a>Definicja reguły ruchu wychodzącego

Reguły ruchu wychodzącego są zgodne z tą samą znajomą składnią jak **frontend**Równoważenie obciążenia i reguły NAT dla ruchu przychodzącego:  +  **parameters**  +  **baza danych**frontonu. Reguła ruchu wychodzącego konfiguruje wychodzące NAT dla _wszystkich maszyn wirtualnych identyfikowanych przez pulę zaplecza_ , które mają zostać przetłumaczone na _fronton_.  _Parametry_ zapewniają dodatkową kontrolę nad algorytmem NAT dla ruchu wychodzącego.

### <a name="scale-outbound-nat-with-multiple-ip-addresses"></a><a name="scale"></a> Skalowanie ruchu wychodzącego NAT z wieloma adresami IP

Każdy dodatkowy adres IP dostarczony przez fronton oferuje dodatkowe 64 000 portów tymczasowych dla modułu równoważenia obciążenia, które mogą być używane jako porty protokołu źródłowego. 

Użyj wielu adresów IP do zaplanowania scenariuszy o dużej skali. Użyj reguł ruchu wychodzącego, aby ograniczyć [wyczerpanie adresów](troubleshoot-outbound-connection.md#snatexhaust). 

Można również użyć [publicznego prefiksu adresu IP](https://aka.ms/lbpublicipprefix) bezpośrednio z regułą wychodzącą. 

Publiczny prefiks adresu IP zwiększa skalowanie wdrożenia. Prefiks można dodać do listy dozwolonych przepływów pochodzących z zasobów platformy Azure. Konfigurację adresu IP frontonu można skonfigurować w ramach modułu równoważenia obciążenia, aby odwoływać się do prefiksu publicznego adresu IP.  

Moduł równoważenia obciążenia ma kontrolę nad publicznym prefiksem adresu IP. Reguła ruchu wychodzącego automatycznie będzie używać wszystkich publicznych adresów IP zawartych w publicznym prefiksie adresu IP dla połączeń wychodzących. 

Każdy adres IP w ramach publicznego prefiksu IP zapewnia dodatkowe 64 000 portów tymczasowych na adres IP dla usługi równoważenia obciążenia, które mają być używane jako porty protokołu źródłowego.

### <a name="outbound-flow-idle-timeout-and-tcp-reset"></a><a name="idletimeout"></a> Limit czasu bezczynności przepływu ruchu wychodzącego i Resetowanie protokołu TCP

Reguły ruchu wychodzącego zapewniają parametr konfiguracji służący do kontrolowania limitu czasu bezczynności przepływu wychodzącego i dopasowania go do potrzeb aplikacji. Domyślnie wychodzące limity czasu bezczynności to 4 minuty. Aby uzyskać więcej informacji, zobacz [Konfigurowanie limitów czasu bezczynności](load-balancer-tcp-idle-timeout.md#tcp-idle-timeout). 

Domyślnym zachowaniem usługi równoważenia obciążenia jest odrzucanie przepływu w trybie dyskretnym, gdy osiągnięto limit czasu bezczynności dla ruchu wychodzącego. `enableTCPReset`Parametr umożliwia przewidywalną kontrolę i zachowanie aplikacji. Parametr określa, czy należy wysyłać dwukierunkowe Resetowanie TCP (TCP RST) przy limicie czasu limitu czasu bezczynności ruchu wychodzącego. 

Przejrzyj informacje [o limicie czasu bezczynności na potrzeby resetowania protokołu TCP](https://aka.ms/lbtcpreset) , co obejmuje dostępność regionów.

### <a name="preventing-outbound-connectivity"></a><a name="preventoutbound"></a>Zapobieganie łączności wychodzącej

Reguły równoważenia obciążenia zapewniają automatyczne Programowanie dla wychodzącego translatora adresów sieciowych. Niektóre scenariusze korzyści lub wymagają wyłączenia automatycznego programowania wychodzącego NAT przez regułę równoważenia obciążenia. Wyłączenie za pośrednictwem reguły pozwala kontrolować lub ograniczać zachowanie.  

Tego parametru można użyć na dwa sposoby:

1. Zapobieganie przychodzącemu adresowi IP dla wychodzącego odruchu. Wyłącz wychodzący magazyn danych w regule równoważenia obciążenia.
  
2. Dostosuj parametry wychodzącego ruchu [źródłowego](#snat) dla adresu IP używanego do ruchu przychodzącego i wychodzącego jednocześnie. Automatyczne wychodzące NAT musi być wyłączone, aby zezwolić regule wychodzącej na przejęcie kontroli. Aby zmienić przydzieloną portów adresów sieciowych dla ruchu przychodzącego, `disableOutboundSnat` należy także ustawić wartość true dla parametru. 

Operacja konfigurowania reguły wychodzącej zakończy się niepowodzeniem w przypadku próby ponownego zdefiniowania adresu IP używanego do obsługi ruchu przychodzącego.  Najpierw wyłącz wychodzące NAT reguły równoważenia obciążenia.

>[!IMPORTANT]
> Jeśli ustawisz ten parametr na wartość true, maszyna wirtualna nie będzie mogła mieć łączności wychodzącej, aby zdefiniować łączność wychodzącą.  Niektóre operacje związane z maszyną wirtualną lub aplikacją mogą zależeć od dostępnego połączenia wychodzącego. Zadbaj o to, aby zrozumieć zależności danego scenariusza i wziąć pod uwagę wpływ wprowadzania tej zmiany.

Czasami jest niepożądane, aby maszyna wirtualna mogła utworzyć przepływ wychodzący. Może istnieć wymóg, aby zarządzać miejscem, w którym znajdują się przepływy wychodzące, lub które miejsca docelowe zaczynają przepływy przychodzące. Użyj [sieciowych grup zabezpieczeń](../virtual-network/security-overview.md) , aby zarządzać miejscami docelowymi, do których dojdzie maszyna wirtualna. Użyj sieciowych grup zabezpieczeń, aby zarządzać, które publiczne miejsca docelowe zaczynają przepływy przychodzące.

Po zastosowaniu sieciowej grupy zabezpieczeń do maszyny wirtualnej z równoważeniem obciążenia należy zwrócić uwagę na [Tagi usługi](../virtual-network/security-overview.md#service-tags) i [domyślne reguły zabezpieczeń](../virtual-network/security-overview.md#default-security-rules). Upewnij się, że maszyna wirtualna może odbierać żądania sondowania kondycji z Azure Load Balancer.

Jeśli sieciowej grupy zabezpieczeń blokuje żądania sondy kondycji z domyślnego tagu AZURE_LOADBALANCER, sonda kondycji maszyny wirtualnej kończy się niepowodzeniem, a maszyna wirtualna zostanie oznaczona jako niedostępna. Load Balancer przestaje wysyłać Nowe przepływy do tej maszyny wirtualnej.

## <a name="scenarios-with-outbound-rules"></a>Scenariusze z regułami ruchu wychodzącego

### <a name="outbound-rules-scenarios"></a>Scenariusze reguł ruchu wychodzącego

* Skonfiguruj połączenia wychodzące do określonego zestawu publicznych adresów IP lub prefiksu.
* Modyfikowanie [alokacji](#snat) portów dla tego obiektu.
* Włącz tylko ruch wychodzący.
* Wychodzące NAT tylko dla maszyn wirtualnych (bez ruchu przychodzącego).
* Wychodzące NAT dla wewnętrznego modułu równoważenia obciążenia.
* Włącz protokoły UDP & protokołu TCP dla ruchu wychodzącego NAT przy użyciu publicznego standardowego modułu równoważenia obciążenia.

### <a name="configure-outbound-connections-to-a-specific-set-of-public-ips-or-prefix"></a><a name="scenario1out"></a>Konfigurowanie połączeń wychodzących do określonego zestawu publicznych adresów IP lub prefiksu

#### <a name="details"></a>Szczegóły

Ten scenariusz służy do dostosowywania połączeń wychodzących, które pochodzą z zestawu publicznych adresów IP. Dodaj publiczne adresy IP lub prefiksy do listy dozwolonych lub zablokowanych w oparciu o pochodzenie.

Ten publiczny adres IP lub prefiks może być taki sam, jak używany przez regułę równoważenia obciążenia. 

Aby użyć innego publicznego adresu IP lub prefiksu niż używany przez regułę równoważenia obciążenia:  

1. Utwórz publiczny prefiks adresu IP lub publiczny adres IP.
2. Tworzenie publicznego, standardowego modułu równoważenia obciążenia 
3. Utwórz fronton odwołujący się do publicznego prefiksu adresu IP lub publicznego adresu IP, którego chcesz użyć. 
4. Ponowne użycie puli zaplecza lub utworzenie puli zaplecza i umieszczenie maszyn wirtualnych w puli zaplecza publicznego modułu równoważenia obciążenia
5. Skonfiguruj regułę ruchu wychodzącego w publicznym module równoważenia obciążenia, aby włączyć wychodzące NAT dla maszyn wirtualnych przy użyciu frontonu. Jeśli nie chcesz, aby reguła równoważenia obciążenia była używana dla ruchu wychodzącego, wyłącz wychodzący program do odczytu z reguły równoważenia obciążenia.

### <a name="modify-snat-port-allocation"></a><a name="scenario2out"></a>Modyfikuj [przydział](#snat) portu dla współdzielonego

#### <a name="details"></a>Szczegóły

Korzystając z reguł ruchu wychodzącego, można dostroić [automatyczną alokację portu z użyciem puli zaplecza](load-balancer-outbound-connections.md#preallocatedports). 

Jeśli masz problemy z wyczerpaniem, Zwiększ liczbę portów przyciągania [adresów sieciowych](#snat) z uwzględnieniem wartości domyślnej 1024. 

Każdy publiczny adres IP współtworzy do 64 000 portów tymczasowych. Liczba maszyn wirtualnych w puli zaplecza określa liczbę portów dystrybuowanych do każdej maszyny wirtualnej. Jedna maszyna wirtualna w puli zaplecza ma dostęp do maksymalnie 64 000 portów. Dla dwóch maszyn wirtualnych można uzyskać [maksymalnie 32 000 portów](#snat) z użyciem reguły ruchu wychodzącego (2x 32 000 = 64 000). 

Korzystając z reguł ruchu wychodzącego, można dostrajać porty przydzielone domyślnie. Przydajesz więcej lub mniej niż jest to domyślna alokacja portu przydziałów [adresów sieciowych](#snat) . Każdy publiczny adres IP z frontonu reguły wychodzącej współużytkuje do 64 000 portów tymczasowych na potrzeby używania [jako portów](#snat) protokołu reportowego.  

Moduł równoważenia obciążenia oferuje porty podzbiorów [adresów](#snat) w wielokrotnościach 8. Jeśli podano wartość, która nie jest podzielna przez 8, operacja konfiguracji zostanie odrzucona. 

Jeśli podjęto próbę przyznania [więcej portów](#snat) protokołu reportowego, niż jest dostępne w oparciu o liczbę publicznych adresów IP, operacja konfiguracji zostanie odrzucona.

W przypadku nadania 10 000 portów na maszynę wirtualną i siedmiu maszyn wirtualnych w puli zaplecza mają jeden publiczny adres IP, konfiguracja zostanie odrzucona. Siedem pomnożone przez 10 000 przekracza limit portów 64 000. Dodaj więcej publicznych adresów IP do frontonu reguły ruchu wychodzącego, aby włączyć scenariusz. 

Przywróć [domyślną alokację portu](load-balancer-outbound-connections.md#preallocatedports) , określając wartość 0 dla liczby portów. Pierwsze wystąpienie maszyny wirtualnej 50 spowoduje uzyskanie portów 1024 51-100, a w przypadku wystąpienia maszyn wirtualnych zostanie 512 wyświetlonych co najwyżej Maksymalna liczba wystąpień.  Aby uzyskać więcej informacji na temat domyślnego przydzielania portów adresów sieciowych, zobacz [tabela alokacji portów przydziałów](#snatporttable).

### <a name="enable-outbound-only"></a><a name="scenario3out"></a>Włącz tylko wychodzące

#### <a name="details"></a>Szczegóły

Użyj publicznego, standardowego modułu równoważenia obciążenia, aby zapewnić wychodzące NAT dla grupy maszyn wirtualnych. W tym scenariuszu należy użyć reguły ruchu wychodzącego, bez konieczności stosowania dodatkowych reguł.

> [!NOTE]
> **Usługa Azure Virtual Network translator adresów sieciowych** może zapewnić łączność wychodzącą dla maszyn wirtualnych bez potrzeby modułu równoważenia obciążenia.  Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Virtual Network translator adresów sieciowych?](../virtual-network/nat-overview.md)

### <a name="outbound-nat-for-vms-only-no-inbound"></a><a name="scenario4out"></a>Wychodzące NAT tylko dla maszyn wirtualnych (bez ruchu przychodzącego)

> [!NOTE]
> **Usługa Azure Virtual Network translator adresów sieciowych** może zapewnić łączność wychodzącą dla maszyn wirtualnych bez potrzeby modułu równoważenia obciążenia.  Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Virtual Network translator adresów sieciowych?](../virtual-network/nat-overview.md)

#### <a name="details"></a>Szczegóły

W tym scenariuszu:

1. Utwórz publiczny adres IP lub prefiks.
2. Utwórz publiczny standardowy moduł równoważenia obciążenia. 
3. Utwórz fronton związany z publicznym adresem IP lub prefiksem dedykowanym dla ruchu wychodzącego.
4. Utwórz pulę zaplecza dla maszyn wirtualnych.
5. Umieść maszyny wirtualne w puli zaplecza.
6. Skonfiguruj regułę ruchu wychodzącego, aby włączyć wychodzące NAT.

Użyj prefiksu lub publicznego adresu IP do [skalowania](#snat) portów. Dodaj źródło połączeń wychodzących do listy dozwolonych lub zablokowanych.

### <a name="outbound-nat-for-internal-standard-load-balancer"></a><a name="scenario5out"></a>Wychodzące NAT dla wewnętrznej usługi równoważenia obciążenia w warstwie Standardowa

> [!NOTE]
> **Usługa Azure Virtual Network translator adresów sieciowych** może zapewnić łączność wychodzącą dla maszyn wirtualnych korzystających z wewnętrznego, standardowego modułu równoważenia obciążenia.  Aby uzyskać więcej informacji, zobacz [co to jest usługa Azure Virtual Network translator adresów sieciowych?](../virtual-network/nat-overview.md)

#### <a name="details"></a>Szczegóły

Łączność wychodząca jest niedostępna dla wewnętrznego modułu równoważenia obciążenia, dopóki nie zostanie jawnie zadeklarowana. 

Aby uzyskać więcej informacji, zobacz [Konfiguracja modułu równoważenia obciążenia tylko dla ruchu wychodzącego](https://docs.microsoft.com/azure/load-balancer/egress-only).


### <a name="enable-both-tcp--udp-protocols-for-outbound-nat-with-a-public-standard-load-balancer"></a><a name="scenario6out"></a>Włącz protokoły UDP & protokołu TCP dla ruchu wychodzącego NAT z publicznym standardowym modułem równoważenia obciążenia

#### <a name="details"></a>Szczegóły

W przypadku korzystania z publicznej standardowej usługi równoważenia obciążenia usługa automatycznego wychodzącego NAT jest zgodna z protokołem transportu reguły równoważenia obciążenia. 

1. Wyłącz [przychodzący](#snat) magazyn danych w regule równoważenia obciążenia. 
2. Skonfiguruj regułę ruchu wychodzącego w tym samym module równoważenia obciążenia.
3. Ponownie Użyj puli zaplecza już używanej przez maszyny wirtualne. 
4. Określ "Protokół": "All" jako część reguły ruchu wychodzącego. 

Gdy są używane tylko reguły NAT dla ruchu przychodzącego, nie jest dostarczany wychodzący translator adresów sieciowych. 

1. Umieść maszyny wirtualne w puli zaplecza.
2. Zdefiniuj co najmniej jedną konfigurację adresu IP frontonu z publicznymi adresami IP lub prefiksem publicznego adresu IP 
3. Skonfiguruj regułę ruchu wychodzącego w tym samym module równoważenia obciążenia. 
4. Określ "Protocol": "All" jako część reguły ruchu wychodzącego

## <a name="terminology"></a><a name="terms"></a> Terminologia

### <a name="source-network-address-translation"></a><a name="snat"></a>Translacja adresów sieci źródłowej

| Odpowiednie protokoły |
|------------------------|
| TCP </br> UDP          |

#### <a name="details"></a>Szczegóły

Wdrożenie na platformie Azure może komunikować się z punktami końcowymi poza platformą Azure w publicznej przestrzeni adresów IP.

Gdy wystąpienie uruchamia ruch wychodzący do miejsca docelowego z publicznym adresem IP, platforma Azure dynamicznie mapuje prywatny adres IP zasobu na publiczny adres IP. 

Po utworzeniu tego mapowania ruch powrotny dla tego wychodzącego przepływu zostanie osiągnięty jako prywatny adres IP, z którego pochodzi przepływ. 

Platforma Azure używa **translatora adresów sieciowych** w celu wykonania tej funkcji.

### <a name="port-masquerading-snat-pat"></a><a name="pat"></a>Przymaskowany port do podszywającego się

| Odpowiednie protokoły |
|------------------------|
| TCP </br> UDP          |

#### <a name="details"></a>Szczegóły

Gdy prywatne adresy IP znajdują się za pojedynczym publicznym adresem, platforma Azure korzysta z **translacji adresów portu (** w celu ukrycia prywatnych adresów IP). 

Porty tymczasowe są używane na potrzeby usługi i są [wstępnie przydzieloną](#preallocatedports) na podstawie rozmiaru puli. 

Gdy publiczny moduł równoważenia obciążenia jest skojarzony z maszynami wirtualnymi bez publicznych adresów IP, każde źródło połączenia wychodzącego jest ponownie zapisywane. 

Źródło jest ponownie zapisywane z prywatnego adresu IP sieci wirtualnej do publicznego adresu IP frontonu modułu równoważenia obciążenia. 

W publicznej przestrzeni adresów IP pięć krotek przepływu musi być unikatowa:

* Źródłowy adres IP
* Port źródłowy
* Protokół transportu IP
* Docelowy adres IP
* Port docelowy 

Za pomocą protokołów TCP lub UDP może być używany podszywający się port. Porty protokołu Resources są używane po przepisaniu prywatnego źródła adresu IP, ponieważ wiele przepływów pochodzi z jednego publicznego adresu IP. Algorytm podszywający się pod względem portów nie jest różny dla połączeń UDP i TCP.

### <a name="snat-ports-tcp"></a>Porty protokołu źródłowego (TCP)

| Odpowiednie protokoły |
|------------------------|
| TCP |

#### <a name="details"></a>Szczegóły

Porty IP to tymczasowe porty dostępne dla publicznego adresu IP. Jeden port strumienia adresów sieciowych jest używany w przepływie na pojedynczy docelowy adres IP i port. 

W przypadku wielu przepływów TCP na ten sam docelowy adres IP, port i protokół każdy przepływ TCP zużywa pojedynczy port. 

To zużycie gwarantuje, że przepływy są unikatowe, gdy pochodzą z tego samego publicznego adresu IP i podróżują do:

* Ten sam docelowy adres IP
* Port
* Protokół 

Wiele przepływów współużytkuje jeden port. 

Docelowy adres IP, port i protokół sprawiają, że przepływy są unikatowe bez konieczności stosowania dodatkowych portów źródłowych do rozróżnienia przepływów w publicznej przestrzeni adresów IP.


### <a name="snat-ports-udp"></a>Porty protokołu źródłowego (UDP)

| Odpowiednie protokoły |
|------------------------|
| UDP |

#### <a name="details"></a>Szczegóły

Porty przyłączone do protokołu UDP są zarządzane przez inny algorytm niż porty protokołu podlegającego TCP.  Moduł równoważenia obciążenia używa algorytmu o nazwie port z ograniczeniami NAT dla protokołu UDP.

Jeden port źródłowego adresu IP jest używany dla każdego docelowego i każdego z portów.


### <a name="exhaustion"></a><a name="exhaustion"></a>Wyczerpania

| Odpowiednie protokoły |
|------------------------|
| Nie dotyczy |

#### <a name="details"></a>Szczegóły

W przypadku wyczerpania zasobów portów współdziałania przepływy wychodzące kończą się niepowodzeniem, dopóki istniejące przepływy nie zwolnią portów. Moduł równoważenia obciążenia odzyskuje porty dla translatora adresów sieciowych podczas zamykania przepływu.

[Limit czasu bezczynności](../load-balancer/troubleshoot-outbound-connection.md#idletimeout) wynoszący 4 minuty jest używany przez moduł równoważenia obciążenia do odzyskiwania portów protokołu reportowego.

Porty protokołu przesyłania adresów sieciowych UDP zwykle są znacznie szybsze niż porty protokołu przesyłania adresów sieciowych TCP ze względu na różnice w używanym algorytmie. Testowanie projektu i skalowania z powodu tej różnicy.

### <a name="snat-port-release-behavior-tcp"></a>Zachowanie wersji portu protokołu reportowego (TCP)

| Odpowiednie protokoły |
|------------------------|
| TCP |

#### <a name="details"></a>Szczegóły

Gdy serwer lub klient wysyła FINACK, port dla tego elementu jest wydawany po 240 sekund. W przypadku zdarzenia o parametrze RST zostanie wystawiony port dla tego elementu po upływie 15 sekund. Jeśli limit czasu bezczynności został osiągnięty, port zostanie wystawiony.

### <a name="snat-port-release-behavior-udp"></a>Zachowanie wersji portu protokołu reportowego (UDP)

| Odpowiednie protokoły |
|------------------------|
| TCP |

#### <a name="details"></a>Szczegóły

Po osiągnięciu limitu czasu bezczynności port zostanie wystawiony.

### <a name="snat-port-reuse"></a>Ponowne użycie portu dla translatora adresów sieciowych

| Odpowiednie protokoły |
|------------------------|
| TCP </br> UDP |

#### <a name="details"></a>Szczegóły

Gdy port zostanie wystawiony, port jest dostępny do ponownego użycia. Porty usługi reportowe są sekwencją od najniższego do najwyższego dostępnego dla danego scenariusza, a pierwszy dostępny port jest używany dla nowych połączeń.

## <a name="limitations"></a>Ograniczenia

- Maksymalna liczba użytecznych portów tymczasowych na adres IP frontonu to 64 000.
- Zakres konfigurowalnego limitu czasu bezczynności wynosi od 4 do 120 minut (240 do 7200 sekund).
- Moduł równoważenia obciążenia nie obsługuje protokołu ICMP dla wychodzącego translatora adresów sieciowych.
- Reguły ruchu wychodzącego mogą być stosowane tylko do podstawowej konfiguracji adresu IP karty sieciowej.  Nie można utworzyć reguły ruchu wychodzącego dla pomocniczego adresu IP maszyny wirtualnej lub urządzenie WUS. Obsługiwane są wiele kart sieciowych.
- Role procesów roboczych sieci Web bez sieci wirtualnej i innych usług platformy firmy Microsoft mogą być dostępne, gdy jest używany wewnętrzny standardowy moduł równoważenia obciążenia. Ten dostęp jest spowodowany skutkami ubocznymi działania usług przedsieci VNet i innych usług platformy. Nie należy polegać na tym efekcie ubocznym, ponieważ odpowiednia sama usługa lub bazowa platforma mogą ulec zmianie bez powiadomienia. Zawsze zakładaj, że musisz utworzyć łączność wychodzącą jawnie, jeśli potrzebujesz, gdy używasz tylko wewnętrznego modułu równoważenia obciążenia. Scenariusz 3 opisany w tym artykule nie jest dostępny.

## <a name="next-steps"></a>Następne kroki

Jeśli występują problemy z łącznością wychodzącą za pośrednictwem Azure Load Balancer, zobacz [Przewodnik rozwiązywania problemów dla połączeń wychodzących](../load-balancer/troubleshoot-outbound-connection.md).

- Dowiedz się więcej na temat usługi [równoważenia obciążenia w warstwie Standardowa](load-balancer-standard-overview.md).
- Zapoznaj się z [często zadawanymi pytaniami dotyczącymi Azure Load Balancer](load-balancer-faqs.md).
- Dowiedz się więcej o [regułach ruchu wychodzącego](load-balancer-outbound-rules-overview.md) dla standardowego publicznego modułu równoważenia obciążenia.

