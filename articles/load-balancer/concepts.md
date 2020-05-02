---
title: Koncepcje Azure Load Balancer
description: Przegląd pojęć dotyczących Azure Load Balancer
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2020
ms.author: allensu
ms.openlocfilehash: 1fc6721ede07100b4a6769eef8893857cbde119d
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82629994"
---
# <a name="azure-load-balancer-concepts"></a>Koncepcje Azure Load Balancer

Moduł równoważenia obciążenia oferuje kilka możliwości dla aplikacji UDP i TCP. 

## <a name="load-balancing-algorithm"></a>Algorytm równoważenia obciążenia
Można utworzyć regułę równoważenia obciążenia, aby dystrybuować ruch z frontonu do puli zaplecza. Azure Load Balancer używa algorytmu wyznaczania wartości skrótu do dystrybucji przepływów przychodzących (nie bajtów). Moduł równoważenia obciążenia ponownie zapisuje nagłówki przepływów w wystąpieniach puli zaplecza. Serwer jest dostępny do odbierania nowych przepływów, gdy sonda kondycji wskazuje dobry punkt końcowy zaplecza.

Domyślnie moduł równoważenia obciążenia używa skrótu z pięcioma kolekcjami.

Skrót obejmuje:

- **Źródłowy adres IP**
- **Port źródłowy**
- **Docelowy adres IP**
- **Port docelowy**
- **Numer protokołu IP do mapowania przepływów na dostępne serwery**

Koligacja ze źródłowym adresem IP jest tworzona przy użyciu dwóch lub trzech krotek wartości skrótu. Pakiety tego samego przepływu docierają do tego samego wystąpienia, w którym znajduje się fronton o zrównoważonym obciążeniu. 

Port źródłowy zmienia się, gdy klient uruchamia nowy przepływ z tego samego źródłowego adresu IP. W efekcie wartość skrótu z pięcioma krotką może spowodować, że ruch przejdzie do innego punktu końcowego zaplecza.
Aby uzyskać więcej informacji, zobacz [Konfigurowanie trybu dystrybucji dla Azure Load Balancer](./load-balancer-distribution-mode.md).

Na poniższej ilustracji przedstawiono dystrybucję opartą na skrótach:

  ![Dystrybucja oparta na skrótach](./media/load-balancer-overview/load-balancer-distribution.png)

  *Rysunek: dystrybucja oparta na skrótach*

## <a name="application-independence-and-transparency"></a>Niezależność i przezroczystość aplikacji

 Moduł równoważenia obciążenia nie współdziała bezpośrednio z protokołem TCP lub UDP ani warstwą aplikacji. Można obsługiwać dowolny scenariusz aplikacji TCP lub UDP. Moduł równoważenia obciążenia nie zamyka lub nie pochodzi z przepływów ani nie współdziała z ładunkiem przepływu. Moduł równoważenia obciążenia nie udostępnia funkcji bramy warstwy aplikacji. Uzgadnianie protokołu zawsze następuje bezpośrednio między klientem a wystąpieniem puli zaplecza. Odpowiedź na przepływ przychodzący to zawsze odpowiedź z maszyny wirtualnej. Po dostarczeniu przepływu do maszyny wirtualnej oryginalny źródłowy adres IP również jest zachowywany.

* Każdy punkt końcowy jest odpowiedzią na maszynę wirtualną. Na przykład uzgadnianie protokołu TCP następuje między klientem a wybraną maszyną wirtualną zaplecza. Odpowiedź na żądanie do frontonu jest odpowiedzią wygenerowaną przez maszynę wirtualną zaplecza. Po pomyślnym zweryfikowaniu łączności z frontonem sprawdzasz poprawność łączności w ramach co najmniej jednej maszyny wirtualnej zaplecza.
* Ładunki aplikacji są niewidoczne dla modułu równoważenia obciążenia. Można obsługiwać dowolną aplikację UDP lub TCP.
* Ponieważ moduł równoważenia obciążenia nie współdziała z ładunkiem TCP i zapewnia odciążanie protokołu TLS, można tworzyć kompleksowe zaszyfrowane scenariusze. Korzystanie z usługi równoważenia obciążenia uzyskuje duże skalowanie w poziomie dla aplikacji TLS przez zakończenie połączenia TLS na maszynie wirtualnej. Na przykład pojemność klucza sesji protokołu TLS jest ograniczona tylko przez typ i liczbę maszyn wirtualnych dodawanych do puli zaplecza.

## <a name="outbound-connections"></a>Połączenia wychodzące 
Przepływy z puli zaplecza do publicznych adresów IP są mapowane na fronton. Platforma Azure tłumaczy połączenia wychodzące z publicznego adresu IP frontonu za pośrednictwem reguły ruchu wychodzącego równoważenia obciążenia. 

Ta konfiguracja ma następujące zalety:

* Łatwe uaktualnienie i odzyskiwanie po awarii usług, ponieważ fronton może być dynamicznie mapowany do innego wystąpienia usługi.
* Łatwiejsze zarządzanie listami kontroli dostępu (ACL). Listy ACL wyrażone jako adresy IP frontonu nie zmieniają się jako usługi skalowanie w górę lub w dół lub do ponownego wdrożenia. Tłumaczenie połączeń wychodzących na mniejszą liczbę adresów IP niż maszyny zmniejsza obciążenie związane z wdrażaniem bezpiecznych list adresatów.

Aby dowiedzieć się więcej o translatorze adresów sieciowych (Resources) i Azure Load Balancer, zobacz podadresy [i Azure Load Balancer](load-balancer-outbound-connections.md#snat).

## <a name="availability-zones"></a>Strefy dostępności

Usługa równoważenia obciążenia w warstwie Standardowa obsługuje dodatkowe możliwości w regionach, w których Strefy dostępności są dostępne. Te funkcje są przyrostowo przeznaczone dla wszystkich dostępnych standardowych modułów równoważenia obciążenia.  Konfiguracje Strefy dostępności są dostępne dla obu typów usługi równoważenia obciążenia w warstwie Standardowa; publiczna i wewnętrzna.


Strefowo nadmiarowy fronton przeżyje awarię strefy przy użyciu dedykowanej infrastruktury we wszystkich strefach jednocześnie. Ponadto możesz zagwarantowanie frontonu dla określonej strefy. 

Strefa frontonu jest obsługiwana przez dedykowaną infrastrukturę w jednej strefie. Równoważenie obciążenia między strefami jest dostępne dla puli zaplecza. Każdy zasób maszyny wirtualnej w sieci wirtualnej może być częścią puli zaplecza.

Podstawowa usługa równoważenia obciążenia nie obsługuje stref.
Przejrzyj [szczegółowe omówienie strefy dostępności związanych z nimi możliwości](load-balancer-standard-availability-zones.md) i [strefy dostępności omówienia](../availability-zones/az-overview.md) , aby uzyskać więcej informacji.

## <a name="ha-ports"></a>Porty wysokiej dostępności

Można skonfigurować reguły równoważenia obciążenia portów HA, aby umożliwić skalowanie aplikacji i być wysoce niezawodny. 

Równoważenie obciążenia na przepływ na krótkoterminowych portach adresu IP frontonu wewnętrznego modułu równoważenia obciążenia jest obsługiwane przez te reguły.

Ta funkcja jest przydatna, gdy nie ma praktycznego lub niepożądanego określania poszczególnych portów. Reguła portów HA umożliwia tworzenie scenariuszy w trybie aktywny-pasywny lub aktywny-aktywny. Te scenariusze są przeznaczone dla sieciowych urządzeń wirtualnych i wszystkich aplikacji, które wymagają dużych zakresów portów przychodzących. Sondy kondycji można użyć do określenia, które zaplecza powinny otrzymywać Nowe przepływy.  Do emulowania scenariusza zakresu portów można użyć sieciowej grupy zabezpieczeń. 

Podstawowa usługa równoważenia obciążenia nie obsługuje portów HA. Przejrzyj [szczegółowe omówienie portów ha](load-balancer-ha-ports-overview.md)

>[!IMPORTANT]
> Jeśli planujesz używanie sieciowego urządzenia wirtualnego, skontaktuj się z dostawcą, aby uzyskać wskazówki dotyczące tego, czy ich produkt został przetestowany przy użyciu portów HA, i postępuj zgodnie ze szczegółowymi wskazówkami dotyczącymi implementacji. 

## <a name="multiple-frontends"></a>Wiele frontonów

Moduł równoważenia obciążenia obsługuje wiele reguł z wieloma frontonami.  Usługa Load Balancer w warstwie Standardowa rozszerza tę możliwość na scenariusze wychodzące. 

Reguły ruchu wychodzącego są odwrotnością reguły ruchu przychodzącego. Reguła ruchu wychodzącego tworzy skojarzenie dla połączeń wychodzących. W ramach usługi równoważenia obciążenia w warstwie Standardowa są wykorzystywane wszystkie frontony skojarzone z zasobem maszyny wirtualnej.

Ponadto parametr reguły równoważenia obciążenia pozwala pominąć regułę równoważenia obciążenia na potrzeby łączności wychodzącej, co pozwala na wybór konkretnych frontonów, w tym braku.

W przypadku porównania podstawowa usługa równoważenia obciążenia wybiera losowo jedną fronton. Nie istnieje możliwość kontrolowania, który fronton został wybrany.

## <a name="limitations"></a><a name = "limitations"></a>Ograniczenia

- Jednostki SKU nie są modyfikowalne. Nie można zmienić jednostki SKU istniejącego zasobu.
- Zasób autonomicznej maszyny wirtualnej, zasób zestawu dostępności lub zasób zestawu skalowania maszyn wirtualnych może odwoływać się do jednej jednostki SKU, nigdy nie obu.
- Reguła modułu równoważenia obciążenia nie może obejmować dwóch sieci wirtualnych.  Frontony i powiązane z nimi wystąpienia zaplecza muszą znajdować się w tej samej sieci wirtualnej.  
- [Operacje przenoszenia subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md) nie są obsługiwane w przypadku usługi równoważenia obciążenia w warstwie Standardowa i publicznych adresów IP.
- Role procesów roboczych sieci Web bez sieci wirtualnej i innych usług platformy firmy Microsoft mogą być dostępne z wystąpień wyłącznie w ramach wewnętrznego modułu równoważenia obciążenia. Nie należy polegać na tym ułatwień dostępu, ponieważ sama sama usługa lub bazowa platforma mogą ulec zmianie bez powiadomienia. Jeśli łączność wychodząca jest wymagana w przypadku korzystania ze standardowego wewnętrznego modułu równoważenia obciążenia, należy skonfigurować [łączność wychodzącą](load-balancer-outbound-connections.md) .
- Moduł równoważenia obciążenia zapewnia Równoważenie obciążenia i przekazywanie portów dla określonych protokołów TCP lub UDP. Reguły równoważenia obciążenia i reguły NAT dla ruchu przychodzącego obsługują protokoły TCP i UDP, ale nie są to adresy IP, w tym protokół ICMP.

  Moduł równoważenia obciążenia nie zamyka, nie odpowiada ani nie działa w inny sposób z ładunkiem przepływu UDP lub TCP. Moduł równoważenia obciążenia nie działa jako serwer proxy. 
  
  Musi zostać wykonane pomyślne połączenie z frontonem. To połączenie musi być tym samym portem używanym w ramach równoważenia obciążenia lub przychodzącej reguły NAT. Aby wyświetlić odpowiedź z frontonu, jedna maszyna wirtualna w puli zaplecza musi odpowiedzieć.

  Niepowodzenie odebrania odpowiedzi z frontonu wskazuje, że maszyny wirtualne nie mogą reagować. Korzystanie z frontonu modułu równoważenia obciążenia zakończy się niepowodzeniem bez odpowiedzi maszyny wirtualnej. 
  
  Ta zasada ma zastosowanie również do połączeń wychodzących, w których port podszywający jest obsługiwany tylko dla protokołów TCP i UDP. Wszystkie inne protokoły IP, w tym ICMP, kończą się niepowodzeniem. 
  
  Przypisz publiczny adres IP do zasobu, aby rozwiązać ten problem. Aby uzyskać więcej informacji, zobacz temat [Omówienie elementu reźródłowego i](load-balancer-outbound-connections.md#snat)elementu.

- Wewnętrzne moduły równoważenia obciążenia nie przekładają się na wychodzące połączenia przychodzące do frontonu wewnętrznego modułu równoważenia obciążenia, ponieważ oba znajdują się w prywatnej przestrzeni adresów IP. Publiczne usługi równoważenia obciążenia zapewniają [połączenia wychodzące](load-balancer-outbound-connections.md) z prywatnych adresów IP w sieci wirtualnej do publicznych adresów IP. W przypadku wewnętrznych modułów równoważenia obciążenia to podejście pozwala uniknąć potencjalnego wyczerpania portów w ramach unikatowej wewnętrznej przestrzeni adresów IP, w której tłumaczenie nie jest wymagane.

  Przepływ wychodzący z maszyny wirtualnej zaplecza do frontonu wewnętrznego modułu równoważenia obciążenia zakończy się niepowodzeniem. Błąd występuje, gdy przepływ jest mapowany do samego siebie. Dwa etapy przepływu nie są zgodne i przepływ zakończy się niepowodzeniem.
  
  Gdy przepływ zamapuje się z powrotem do samego siebie, przepływ wychodzący wydaje się pochodzić z maszyny wirtualnej do frontonu.

  Przepływ powiedzie się, jeśli nie zamapuje się z powrotem do tej samej maszyny wirtualnej w zapleczu, która utworzyła przepływ.
  
  Części ruchu przychodzącego i wychodzącego przepływu nie są zgodne z maszyną wirtualną. Stos TCP nie rozpoznaje tych połówów tego samego przepływu, co jest częścią tego samego przepływu. Źródło i miejsce docelowe nie są zgodne. Maszyna wirtualna może reagować, gdy przepływ jest mapowany na inną maszynę wirtualną w zapleczu. Połówy są zgodne z przepływem, a połączenie może być kontynuowane.

  Objawem tego scenariusza jest sporadyczne przekroczenie limitu czasu połączenia. Typowe obejścia obejmują Wstawianie warstwy proxy za wewnętrznym modułem równoważenia obciążenia i używanie reguł stylu bezpośredniego powrotu serwera (DSR). Aby uzyskać więcej informacji, zobacz [wiele frontonów dla Azure Load Balancer](load-balancer-multivip-overview.md).

  Wewnętrzny moduł równoważenia obciążenia można połączyć z dowolnym serwerem proxy innej firmy. Dostępna jest również funkcja wewnętrznego [Application Gateway](../application-gateway/application-gateway-introduction.md) dla scenariuszy serwera proxy z protokołem HTTP/HTTPS. Mimo że można użyć publicznego modułu równoważenia obciążenia w celu ograniczenia tego problemu, powstały scenariusz jest podatny na [wyczerpanie adresów](load-balancer-outbound-connections.md#snat). Należy unikać tego drugiego podejścia, chyba że jest to starannie zarządzane.

- Ogólnie rzecz biorąc, fragmenty adresów IP przekazywania nie są obsługiwane w regułach równoważenia obciążenia. Fragmentacja IP pakietów UDP i TCP nie jest obsługiwana w regułach równoważenia obciążenia. Reguły równoważenia obciążenia portów HA mogą służyć do przesyłania dalej istniejących fragmentów adresów IP. Aby uzyskać więcej informacji, zobacz [Omówienie portów wysokiej dostępności](load-balancer-ha-ports-overview.md).

## <a name="next-steps"></a>Następne kroki

- Zobacz [tworzenie usługa Load Balancer w warstwie Standardowa publicznej](quickstart-load-balancer-standard-public-portal.md) , aby rozpocząć korzystanie z usługi Load Balancer: Utwórz jedną, twórz maszyny wirtualne z zainstalowanym niestandardowym rozszerzeniem usług IIS i Zrównoważ obciążenie aplikacji sieci Web między maszynami wirtualnymi.
- Dowiedz się więcej o [Azure Load Balancer połączenia wychodzące](load-balancer-outbound-connections.md).
- Dowiedz się więcej o [Azure Load Balancer](load-balancer-overview.md).
- Dowiedz się więcej o [sondach kondycji](load-balancer-custom-probe-overview.md).
- Dowiedz się więcej na temat [diagnostyki usługa Load Balancer w warstwie Standardowa](load-balancer-standard-diagnostics.md).
- Dowiedz się więcej na temat [sieciowych grup zabezpieczeń](../virtual-network/security-overview.md).
