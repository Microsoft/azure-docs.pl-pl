---
title: Składniki i ograniczenia
titleSuffix: Azure Load Balancer
description: Omówienie składników Azure Load Balancer i ograniczeń.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
Customer intent: As an IT administrator, I want to learn more about the Azure Load Balancer components and limitations and how it will affect my environment.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/14/2020
ms.author: allensu
ms.openlocfilehash: 7a7210915920ed9ab2a5ddc47a27c2587895a57a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81768254"
---
# <a name="load-balancer-components-and-limitations"></a>Load Balancer składników i ograniczeń
Azure Load Balancer zawiera kilka kluczowych składników dla operacji.  Te składniki można skonfigurować w ramach subskrypcji za pośrednictwem Azure Portal, interfejsu wiersza polecenia platformy Azure lub Azure PowerShell.  

## <a name="load-balancer-components"></a>Składniki Load Balancer

* **Konfiguracje adresów IP frontonu**: adres IP modułu równoważenia obciążenia. Jest to punkt kontaktu dla klientów. Mogą to być następujące adresy: 

    - **[Publiczny adres IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)**
    - **[Prywatny adres IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)**

* **Pula zaplecza**: Grupa maszyn wirtualnych lub wystąpień w zestawie skalowania maszyn wirtualnych, które będą obsługiwać przychodzące żądanie. Aby zapewnić oszczędne skalowanie w celu spełnienia dużych ilości ruchu przychodzącego, wytyczne obliczeniowe zwykle zaleca się dodanie większej liczby wystąpień do puli zaplecza. Load Balancer natychmiast ponownie konfiguruje siebie przy użyciu automatycznej zmiany konfiguracji podczas skalowania wystąpień w górę lub w dół. Dodawanie lub usuwanie maszyn wirtualnych z puli zaplecza ponownie konfiguruje Load Balancer bez dodatkowych operacji. Zakresem puli zaplecza jest dowolna maszyna wirtualna w sieci wirtualnej. Pula zaplecza może mieć do 1000 wystąpień zaplecza lub konfiguracji protokołu IP.
Podstawowe usługi równoważenia obciążenia mają ograniczony zakres (zestaw dostępności) można skalować maksymalnie do 300 konfiguracji adresów IP. Aby uzyskać więcej informacji na temat limitów, zobacz [limity Load Balancer](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). Biorąc pod uwagę sposób projektowania puli zaplecza, można projektować dla najmniejszej liczby zasobów puli zaplecza, aby dodatkowo zoptymalizować czas trwania operacji zarządzania. Nie ma różnicy w wydajności lub skali danych.
* **Sondy kondycji**: **[sonda kondycji](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** służy do określania kondycji wystąpień w puli zaplecza. Można zdefiniować próg złej kondycji dla sond kondycji. Jeśli sonda nie odpowiada, moduł równoważenia obciążenia zaprzestaje inicjowania nowych połączeń z wystąpieniami o złej kondycji. Awaria sondy nie ma wpływu na istniejące połączenia. 
    
    Połączenie jest kontynuowane do momentu, gdy aplikacja: 
    - Zamyka przepływ
    - Limit czasu bezczynności
    - Maszyna wirtualna jest zamykana

    Load Balancer oferuje różne typy sond kondycji dla punktów końcowych:
    - TCP
    - HTTP
    - HTTPS (sonda HTTP z otoką Transport Layer Security (TLS))
     
     Podstawowa Load Balancer nie obsługuje sond protokołu HTTPS. Ponadto podstawowa Load Balancer zakończy wszystkie połączenia TCP (łącznie z ustanowionymi połączeniami). 
    Aby uzyskać więcej informacji, zobacz [typy sond](load-balancer-custom-probe-overview.md#types).

* **Reguły równoważenia obciążenia**: reguły równoważenia obciążenia są tymi, które informują Load Balancer, co należy zrobić. 
* **Reguły NAT dla ruchu przychodzącego**: Reguła ruchu przychodzącego NAT przesyła dalej ruch z określonego portu określonego adresu IP frontonu do określonego portu określonego wystąpienia zaplecza w sieci wirtualnej. **[Przekazywanie portów](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** odbywa się przy użyciu tego samego rozkładu opartego na wykorzystaniu skrótu co Równoważenie obciążenia. Typowe scenariusze tej możliwości są Remote Desktop Protocol (RDP) lub Secure Shell (SSH) do poszczególnych wystąpień maszyn wirtualnych w ramach Virtual Network platformy Azure. Można mapować wiele wewnętrznych punktów końcowych na porty w ramach tego samego adresu IP frontonu. Korzystając z adresów IP frontonu, można zdalnie administrować maszynami wirtualnymi bez dodatkowego pola skoku.
* **Reguły ruchu wychodzącego**: **[Reguła ruchu](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)** wychodzącego konfiguruje wychodzące translację adresów sieciowych (NAT) dla wszystkich maszyn wirtualnych lub wystąpień identyfikowanych przez pulę zaplecza usługa Load Balancer w warstwie Standardowa, które mają zostać przetłumaczone na fronton.

  Podstawowa Load Balancer nie obsługuje reguł ruchu wychodzącego.

  ![Azure Load Balancer](./media/load-balancer-overview/load-balancer-overview.png)
* **Protokoły transportowe**: Load Balancer nie obsługuje protokołu ICMP. Przekroczenie limitu czasu polecenia ping protokołu ICMP do publicznego modułu równoważenia obciążenia. Aby wysłać polecenie ping do publicznego modułu równoważenia obciążenia, użyj polecenia ping protokołu TCP

## <a name="load-balancer-concepts"></a><a name = "load-balancer-concepts"></a>Koncepcje Load Balancer

Moduł równoważenia obciążenia zapewnia następujące podstawowe możliwości na potrzeby aplikacji protokołu TCP i UDP:

* **Algorytm równoważenia obciążenia**: w Azure Load Balancer można utworzyć regułę równoważenia obciążenia w celu dystrybucji ruchu przychodzącego do wystąpień puli zaplecza. Load Balancer używa algorytmu wyznaczania wartości skrótu do dystrybucji przepływów przychodzących (nie bajtów) i ponownie zapisuje nagłówki przepływów w wystąpieniach puli zaplecza. Serwer jest dostępny do odbierania nowych przepływów, gdy sonda kondycji wskazuje dobry punkt końcowy zaplecza.
Domyślnie Load Balancer używa skrótu 5-spójnej kolekcji. 

   Skrót obejmuje: 

   - **Źródłowy adres IP**
   - **Port źródłowy**
   - **Docelowy adres IP**
   - **Port docelowy**
   - **Numer protokołu IP do mapowania przepływów na dostępne serwery** 

Można utworzyć koligację ze źródłowym adresem IP przy użyciu algorytmu 2-lub 3-spójnej kolekcji dla danej reguły. Wszystkie pakiety z tego samego przepływu pakietów trafiają do tego samego wystąpienia w ramach frontonu z równoważeniem obciążenia. Gdy klient uruchamia nowy przepływ z tego samego źródłowego adresu IP, port źródłowy zostanie zmieniony. W związku z tym mieszanie 5-krotka może spowodować, że ruch przejdzie do innego punktu końcowego zaplecza.
Aby uzyskać więcej informacji, zobacz [Konfigurowanie trybu dystrybucji dla Azure Load Balancer](./load-balancer-distribution-mode.md). 

Na poniższej ilustracji przedstawiono dystrybucję opartą na skrótach:

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer-distribution.svg" width="512" title="Dystrybucja oparta na skrótach">
</p>

  *Rysunek: dystrybucja oparta na skrótach*

* **Niezależność i przezroczystość aplikacji**: Load Balancer nie współdziała bezpośrednio z protokołem TCP lub UDP ani warstwą aplikacji. Można obsługiwać dowolny scenariusz aplikacji TCP lub UDP. Load Balancer nie przerywa działania lub nie pochodzą przepływy, współdziałają z ładunkiem przepływu ani nie zapewniają żadnej funkcji bramy warstwy aplikacji. Uzgadnianie protokołu zawsze następuje bezpośrednio między klientem a wystąpieniem puli zaplecza. Odpowiedź na przepływ przychodzący to zawsze odpowiedź z maszyny wirtualnej. Po dostarczeniu przepływu do maszyny wirtualnej oryginalny źródłowy adres IP również jest zachowywany.
  * Na każdy punkt końcowy odpowiada tylko maszyna wirtualna. Na przykład uzgadnianie protokołu TCP zawsze występuje między klientem a wybraną maszyną wirtualną zaplecza. Odpowiedź na żądanie do frontonu jest odpowiedzią wygenerowaną przez maszynę wirtualną zaplecza. Po pomyślnym zweryfikowaniu łączności z frontonem jest sprawdzana jest weryfikacja kompleksowej łączności z co najmniej jedną maszyną wirtualną zaplecza.
  * Ładunki aplikacji są niewidoczne dla Load Balancer. Można obsługiwać dowolną aplikację UDP lub TCP.
  * Ponieważ Load Balancer nie współdziała z ładunkiem protokołu TCP i zapewnia odciążanie protokołu TLS, można utworzyć kompleksowe scenariusze z szyfrowaniem. Korzystanie z Load Balancer jest bardzo duże dla aplikacji TLS, kończąc połączenie TLS na samej maszynie wirtualnej. Na przykład pojemność klucza sesji protokołu TLS jest ograniczona tylko przez typ i liczbę maszyn wirtualnych dodawanych do puli zaplecza.

* **Połączenia wychodzące**: wszystkie przepływy wychodzące z prywatnych adresów IP w sieci wirtualnej do publicznych adresów IP w Internecie można przetłumaczyć na adres IP frontonu Load Balancer. Gdy publiczny fronton jest powiązany z maszyną wirtualną zaplecza za pomocą reguły równoważenia obciążenia, platforma Azure tłumaczy połączenia wychodzące na publiczny adres IP frontonu. Ta konfiguracja ma następujące zalety:
  * Łatwe uaktualnienie i odzyskiwanie po awarii usług, ponieważ fronton może być dynamicznie mapowany do innego wystąpienia usługi.
  * Łatwiejsze zarządzanie listami kontroli dostępu (ACL). Listy ACL wyrażone jako adresy IP frontonu nie zmieniają się jako usługi skalowanie w górę lub w dół lub do ponownego wdrożenia. Tłumaczenie połączeń wychodzących na mniejszą liczbę adresów IP niż maszyny zmniejsza obciążenie związane z wdrażaniem bezpiecznych list adresatów.

  Usługa Load Balancer w warstwie Standardowa wykorzystuje [niezawodny, skalowalny i przewidywalny algorytm przetranslatora adresów sieciowych](load-balancer-outbound-connections.md#snat). Oto kluczowe założenia podczas pracy z usługa Load Balancer w warstwie Standardowa:

    - reguły równoważenia obciążenia wnioskują o sposób zaprogramowania tego mechanizmu. Reguły równoważenia obciążenia są specyficzne dla protokołu. Protokół reportowy jest specyficzny dla protokołu, a konfiguracja powinna odzwierciedlać to, zamiast tworzyć efekt uboczny.

    - **Wiele frontonów** Gdy jest dostępnych wiele frontonów, wszystkie frontony są używane, a każdy fronton mnoży liczbę dostępnych portów. Jeśli potrzebujesz więcej portów, ponieważ oczekujesz lub że masz już duże zapotrzebowanie na połączenia wychodzące, możesz również dodać przyrostowy spis portów dla ruchu przychodzącego, konfigurując dodatkowe frontony, reguły i pule zaplecza do tych samych zasobów maszyny wirtualnej.

    - **Kontrolowanie, który fronton jest używany do wychodzącego** Możesz wybrać i kontrolować, czy nie chcesz, aby konkretny fronton był używany do połączeń wychodzących. Jeśli chcesz ograniczyć połączenia wychodzące wyłącznie z określonego adresu IP frontonu, możesz opcjonalnie wyłączyć wychodzący element wiążący dla reguły, która wyraża mapowanie wychodzące.

    - **Kontrolowane scenariusze łączności** wychodzącej są jawne, a łączność wychodząca nie istnieje, dopóki nie została określona. Usługa Load Balancer w warstwie Standardowa istnieje w kontekście sieci wirtualnej.  Sieć wirtualna jest izolowaną siecią prywatną.  Jeśli skojarzenie z publicznym adresem IP nie istnieje, łączność publiczna jest niedozwolona.  Możesz dotrzeć do [punktów końcowych usługi Virtual Network](../virtual-network/virtual-network-service-endpoints-overview.md) , ponieważ należą one do sieci wirtualnej i są lokalne.  Jeśli chcesz nawiązać łączność wychodzącą z miejscem docelowym poza siecią wirtualną, masz dwie opcje:
        - Przypisz standardowy publiczny adres IP jednostki SKU jako publiczny adres IP na poziomie wystąpienia do zasobu maszyny wirtualnej lub
        - Umieść zasób maszyny wirtualnej w puli zaplecza usługa Load Balancer w warstwie Standardowa publicznej.

        Oba będą zezwalały na połączenia wychodzące z sieci wirtualnej poza siecią wirtualną. 

        Jeśli istnieje _tylko_ wewnętrzna usługa Load Balancer w warstwie Standardowa skojarzona z pulą zaplecza, w której znajduje się zasób maszyny wirtualnej, maszyna wirtualna może uzyskać dostęp tylko do zasobów sieci wirtualnej i [Virtual Network punktów końcowych usługi](../virtual-network/virtual-network-service-endpoints-overview.md).  Aby utworzyć łączność wychodzącą, można wykonać kroki opisane w poprzednim akapicie.

        Łączność wychodząca zasobu maszyny wirtualnej nie skojarzona ze standardowymi jednostkami SKU pozostanie tak jak wcześniej.

        Przejrzyj [szczegółowe omówienie połączeń wychodzących](load-balancer-outbound-connections.md).

* **Strefy dostępności**: usługa Load Balancer w warstwie Standardowa obsługuje dodatkowe możliwości w regionach, w których dostępne są strefy dostępności. Te funkcje są stopniowo dostępne dla wszystkich usługa Load Balancer w warstwie Standardowa.  Konfiguracje Strefy dostępności są dostępne zarówno dla typów, jak i dla usługa Load Balancer w warstwie Standardowa publicznych i wewnętrznych.
 Strefa nadmiarowa przeżyje awarię strefy i jest obsługiwana przez dedykowaną infrastrukturę we wszystkich strefach jednocześnie. 
Ponadto możesz zagwarantowanie frontonu dla określonej strefy. Strefa frontonu współużytkuje losy z odpowiednią strefą i jest obsługiwana tylko przez dedykowaną infrastrukturę w jednej strefie.
Równoważenie obciążenia między strefami jest dostępne dla puli zaplecza, a każdy zasób maszyny wirtualnej w sieci wirtualnej może być częścią puli zaplecza.
Podstawowa Load Balancer nie obsługuje stref.
Przejrzyj [szczegółowe omówienie strefy dostępności związanych z nimi możliwości](load-balancer-standard-availability-zones.md) i [strefy dostępności omówienia](../availability-zones/az-overview.md) , aby uzyskać więcej informacji.

* **Porty ha**: można skonfigurować reguły równoważenia obciążenia, aby umożliwić skalowanie aplikacji i zapewnić wysoką niezawodność. W przypadku korzystania z reguły równoważenia obciążenia z portami HA usługa Load Balancer w warstwie Standardowa zapewnia Równoważenie obciążenia dla przepływu na każdy port tymczasowych usługa Load Balancer w warstwie Standardowa adres IP frontonu.  Ta funkcja jest przydatna w przypadku innych scenariuszy, w których nie można określić pojedynczych portów. Reguła równoważenia obciążenia z portami HA umożliwia tworzenie aktywnych lub aktywnych scenariuszy n + 1 dla sieciowych urządzeń wirtualnych i aplikacji, które wymagają dużych zakresów portów przychodzących.  Sondy kondycji można użyć do określenia, które punkty końcowe powinny otrzymywać Nowe przepływy.  Do emulowania scenariusza zakresu portów można użyć sieciowej grupy zabezpieczeń. Podstawowa Load Balancer nie obsługuje portów HA.
Przejrzyj [szczegółowe omówienie portów ha](load-balancer-ha-ports-overview.md)
>[!IMPORTANT]
> Jeśli planujesz używanie sieciowego urządzenia wirtualnego, skontaktuj się z dostawcą, aby uzyskać wskazówki dotyczące tego, czy ich produkt został przetestowany przy użyciu portów HA, i postępuj zgodnie ze szczegółowymi wskazówkami dotyczącymi implementacji. 

* **Wiele frontonów**: Load Balancer obsługuje wiele reguł z wieloma frontonami.  Usługa Load Balancer w warstwie Standardowa rozszerza to na scenariusze wychodzące.  Scenariusze ruchu wychodzącego są zasadniczo odwrotnością reguły równoważenia obciążenia przychodzącego.  Reguła równoważenia obciążenia przychodzącego tworzy również skojarzenie dla połączeń wychodzących. W usługa Load Balancer w warstwie Standardowa są wykorzystywane wszystkie frontony skojarzone z zasobem maszyny wirtualnej za pośrednictwem reguły równoważenia obciążenia.  Ponadto parametr reguły równoważenia obciążenia i pozwala pominąć regułę równoważenia obciążenia na potrzeby łączności wychodzącej, co pozwala na wybór konkretnych frontonów, w tym braku.

W przypadku porównania podstawowa Load Balancer wybiera jedną fronton losowo i nie ma możliwości kontrolowania, która z nich została wybrana.
## <a name="load-balancer-types"></a>Typy Load Balancer

### <a name="public-load-balancer"></a><a name = "publicloadbalancer"></a>Publiczny moduł równoważenia obciążenia

Load Balancer publiczny mapuje publiczny adres IP i port ruchu przychodzącego na prywatny adres IP i port maszyny wirtualnej. Load Balancer mapuje ruch w inny sposób wokół ruchu odpowiedzi z maszyny wirtualnej. Można dystrybuować określone typy ruchu między wieloma maszynami wirtualnymi lub usługami, stosując reguły równoważenia obciążenia. Na przykład można rozłożyć obciążenie ruchu związanego z żądaniami internetowymi na wiele serwerów internetowych.

>[!NOTE]
>Można zaimplementować tylko jedną Load Balancer publiczną i jeden wewnętrzny Load Balancer na zestaw dostępności.

Na poniższej ilustracji przedstawiono punkt końcowy ze zrównoważonym obciążeniem dla ruchu internetowego, który jest współużytkowany przez trzy maszyny wirtualne dla portu publicznego i TCP 80. Te trzy maszyny wirtualne znajdują się w zestawie z równoważeniem obciążenia.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer-http.svg" width="256" title="Publiczny moduł równoważenia obciążenia">
</p>

*Ilustracja: Równoważenie ruchu internetowego za pomocą publicznego modułu równoważenia obciążenia*

Klienci internetowi wysyłają żądania strony sieci Web na publiczny adres IP aplikacji sieci Web na porcie TCP 80. Azure Load Balancer dystrybuuje żądania między trzema maszynami wirtualnymi w zestawie o zrównoważonym obciążeniu. Aby uzyskać więcej informacji na temat algorytmów Load Balancer, zobacz temat [Load Balancer koncepcje](concepts-limitations.md#load-balancer-concepts).

Azure Load Balancer domyślnie dystrybuuje ruch sieciowy między wieloma wystąpieniami maszyn wirtualnych. Można również skonfigurować koligację sesji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie trybu dystrybucji dla Azure Load Balancer](load-balancer-distribution-mode.md).

### <a name="internal-load-balancer"></a><a name = "internalloadbalancer"></a>Load Balancer wewnętrzny

Wewnętrzny moduł równoważenia obciążenia kieruje ruch tylko do zasobów znajdujących się w sieci wirtualnej lub korzystających z sieci VPN w celu uzyskania dostępu do infrastruktury platformy Azure, w przeciwieństwie do publicznego modułu równoważenia obciążenia. Infrastruktura platformy Azure ogranicza dostęp do adresów IP frontonu z równoważeniem obciążenia sieci wirtualnej. Adresy IP frontonu i sieci wirtualne nigdy nie są bezpośrednio ujawniane w internetowym punkcie końcowym. Wewnętrzne aplikacje biznesowe są uruchomiane na platformie Azure i dostęp do nich jest uzyskiwany z poziomu platformy Azure lub zasobów lokalnych.

Wewnętrzny moduł równoważenia obciążenia pozwala na następujące typy równoważenia obciążenia:

* **W ramach sieci wirtualnej**: Równoważenie obciążenia z maszyn wirtualnych w sieci wirtualnej do zestawu maszyn wirtualnych znajdujących się w tej samej sieci wirtualnej.
* W **przypadku sieci wirtualnej obejmującej wiele**lokalizacji: Równoważenie obciążenia z komputerów lokalnych do zestawu maszyn wirtualnych znajdujących się w tej samej sieci wirtualnej.
* **W przypadku aplikacji wielowarstwowych**: Równoważenie obciążenia dla aplikacji wielowarstwowych dostępnych z Internetu, w których warstwy zaplecza nie są dostępne w Internecie. Warstwy zaplecza wymagają równoważenia obciążenia sieciowego z warstwy połączonej z Internetem. Zobacz następną ilustrację.
* **Dla aplikacji biznesowych**: równoważenie obciążenia na potrzeby aplikacji biznesowych, które są hostowane na platformie Azure bez użycia dodatkowego sprzętu lub oprogramowania służącego do równoważenia obciążenia. Ten scenariusz obejmuje serwery lokalne, które znajdują się w zestawie komputerów, których ruch jest zrównoważony.


<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" width="256" title="Publiczny moduł równoważenia obciążenia">
</p>

*Ilustracja: Równoważenie aplikacji wielowarstwowych przy użyciu Load Balancer publicznego i wewnętrznego*

## <a name="load-balancer-sku-comparison"></a><a name="skus"></a> Porównanie jednostek SKU modułu równoważenia obciążenia

Moduł równoważenia obciążenia obsługuje podstawowe i standardowe jednostki SKU. Te jednostki SKU różnią się w zależności od skali, funkcji i cen. Dowolny scenariusz, który jest możliwy w przypadku podstawowego Load Balancer można utworzyć przy użyciu usługa Load Balancer w warstwie Standardowa. Interfejsy API dla obu jednostek SKU są podobne i są wywoływane przez specyfikację jednostki SKU. Interfejs API obsługujący jednostki SKU dla usługi równoważenia obciążenia i publiczny adres IP jest dostępny od `2017-08-01` interfejsu API. Obie jednostki SKU korzystają z tego samego ogólnego interfejsu API i struktury.

Kompletna konfiguracja scenariusza może się nieco różnić w zależności od jednostki SKU. Dokumentacja usługi równoważenia obciążenia jest wywoływana, gdy artykuł ma zastosowanie tylko do określonej jednostki SKU. Aby porównać i poznać różnice, zapoznaj się z poniższą tabelą. Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure usługa Load Balancer w warstwie Standardowa](load-balancer-standard-overview.md).

Aby uaktualnić podstawowe Load Balancer do warstwy Standardowa, zobacz [uaktualnianie Load Balancer podstawowa](upgrade-basic-standard.md).

>[!NOTE]
> Firma Microsoft zaleca usługa Load Balancer w warstwie Standardowa.
Autonomiczne maszyny wirtualne, zestawy dostępności i zestawy skalowania maszyn wirtualnych można połączyć tylko jednej jednostki SKU — nigdy do obu. Load Balancer oraz jednostka SKU publicznego adresu IP muszą być zgodne, gdy są używane z publicznymi adresami IP. Jednostki SKU Load Balancer i publicznych adresów IP nie są modyfikowalne.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Aby uzyskać więcej informacji, zobacz [limity usługi równoważenia obciążenia](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). Szczegółowe informacje dotyczące modułu równoważenia obciążenia w warstwie Standardowa zawiera [przegląd](load-balancer-standard-overview.md), [cennik](https://aka.ms/lbpricing) i [umowa dotycząca poziomu usług](https://aka.ms/lbsla).

## <a name="limitations"></a><a name = "limitations"></a>Ograniczenia

- Jednostki SKU nie są modyfikowalne. Nie można zmienić jednostki SKU istniejącego zasobu.
- Zasób autonomicznej maszyny wirtualnej, zasób zestawu dostępności lub zasób zestawu skalowania maszyn wirtualnych może odwoływać się do jednej jednostki SKU, nigdy nie obu.
- Reguła Load Balancer nie może obejmować dwóch sieci wirtualnych.  Frontony i powiązane z nimi wystąpienia zaplecza muszą znajdować się w tej samej sieci wirtualnej.  
- [Operacje przenoszenia subskrypcji](../azure-resource-manager/management/move-resource-group-and-subscription.md) nie są obsługiwane w przypadku zasobów w warstwie Standardowa lb i publicznych adresów IP.
- Role procesów roboczych sieci Web bez sieci wirtualnej i innych usług platformy firmy Microsoft mogą być dostępne z wystąpień znajdujących się tylko w usługa Load Balancer w warstwie Standardowa wewnętrznym. Nie należy polegać na tym, ponieważ sama usługa lub podstawowa platforma może ulec zmianie bez powiadomienia. Zawsze należy założyć, że konieczne jest utworzenie [połączenia wychodzącego](load-balancer-outbound-connections.md) jawnie, jeśli jest to potrzebne, przy użyciu tylko wewnętrznego usługa Load Balancer w warstwie Standardowa.

- Load Balancer zapewnia Równoważenie obciążenia i przekazywanie portów dla określonych protokołów TCP lub UDP. Reguły równoważenia obciążenia i reguły NAT dla ruchu przychodzącego obsługują protokoły TCP i UDP, ale nie są to adresy IP, w tym protokół ICMP.

  Load Balancer nie przerywa działania, reaguje lub nie działa w inny sposób z ładunkiem przepływu UDP lub TCP. Nie jest to serwer proxy. Pomyślne sprawdzenie poprawności łączności z frontonem musi odbywać się w paśmie przy użyciu tego samego protokołu, który jest używany w ramach równoważenia obciążenia lub przychodzącej reguły NAT. Co najmniej jedna maszyna wirtualna musi generować odpowiedź dla klienta, aby zobaczyć odpowiedź z frontonu.

  Nie odebrano odpowiedzi w paśmie z frontonu Load Balancer wskazuje, że żadne maszyny wirtualne nie mogą reagować. Nic nie może współdziałać z Load Balancer frontonu bez możliwości reagowania maszyny wirtualnej. Ta zasada ma zastosowanie również do połączeń wychodzących, w których port podszywający jest obsługiwany tylko dla protokołów TCP i UDP. Wszystkie inne protokoły IP, w tym ICMP, kończą się niepowodzeniem. Przypisz publiczny adres IP na poziomie wystąpienia, aby rozwiązać ten problem. Aby uzyskać więcej informacji, zobacz temat [Omówienie elementu reźródłowego i](load-balancer-outbound-connections.md#snat)elementu.

- Wewnętrzne moduły równoważenia obciążenia nie przekładają się na wychodzące połączenia przychodzące do frontonu wewnętrznego Load Balancer, ponieważ obie są w prywatnej przestrzeni adresów IP. Publiczne usługi równoważenia obciążenia zapewniają [połączenia wychodzące](load-balancer-outbound-connections.md) z prywatnych adresów IP w sieci wirtualnej do publicznych adresów IP. W przypadku wewnętrznych modułów równoważenia obciążenia to podejście pozwala uniknąć potencjalnego wyczerpania portów w ramach unikatowej wewnętrznej przestrzeni adresów IP, w której tłumaczenie nie jest wymagane.

  Efekt uboczny polega na tym, że jeśli przepływ wychodzący z maszyny wirtualnej w puli zaplecza próbuje przepływ do frontonu Load Balancer wewnętrznej w swojej puli _i_ jest mapowany do samego siebie, dwa etapy przepływu nie są zgodne. Ponieważ nie są one zgodne, przepływ kończy się niepowodzeniem. Przepływ powiedzie się, jeśli przepływ nie został zmapowany na tę samą maszynę wirtualną w puli zaplecza, która utworzyła przepływ do frontonu.

  Gdy przepływ zamapuje się z powrotem do samego siebie, przepływ wychodzący wydaje się pochodzić z maszyny wirtualnej do frontonu, a odpowiedni przepływ przychodzący wydaje się pochodzić z maszyny wirtualnej do samej siebie. Z punktu widzenia systemu operacyjnego gościa elementy przychodzące i wychodzące tego samego przepływu nie pasują do maszyny wirtualnej. Stos TCP nie rozpoznaje tych połówów tego samego przepływu, co jest częścią tego samego przepływu. Źródło i miejsce docelowe nie są zgodne. Gdy przepływ jest mapowany na dowolną inną maszynę wirtualną w puli zaplecza, połowy przepływu są zgodne, a maszyna wirtualna może odpowiedzieć na przepływ.

  Objawem tego scenariusza jest sporadyczne przekroczenie limitu czasu połączenia, gdy przepływ powraca do tego samego zaplecza, który pochodzi z przepływu. Typowe obejścia obejmują Wstawianie warstwy proxy za wewnętrzną Load Balancer i używanie reguł stylu bezpośredniego powrotu serwera (DSR). Aby uzyskać więcej informacji, zobacz [wiele frontonów dla Azure Load Balancer](load-balancer-multivip-overview.md).

  Wewnętrzny Load Balancer można połączyć z dowolnym serwerem proxy innej firmy lub użyć wewnętrznych [Application Gateway](../application-gateway/application-gateway-introduction.md) dla scenariuszy serwera proxy przy użyciu protokołu HTTP/HTTPS. Mimo że można użyć Load Balancer publicznego, aby zmniejszyć ten problem, wynikający z nich scenariusz jest podatny na [wyczerpanie adresów](load-balancer-outbound-connections.md#snat). Należy unikać tego drugiego podejścia, chyba że jest to starannie zarządzane.

- Ogólnie rzecz biorąc, fragmenty adresów IP przekazywania nie są obsługiwane w regułach równoważenia obciążenia. Fragmentacja IP pakietów UDP i TCP nie jest obsługiwana w regułach równoważenia obciążenia. Reguły równoważenia obciążenia portów o wysokiej dostępności mogą służyć do przesyłania dalej istniejących fragmentów adresów IP. Aby uzyskać więcej informacji, zobacz [Omówienie portów wysokiej dostępności](load-balancer-ha-ports-overview.md).

## <a name="next-steps"></a>Następne kroki

- Zobacz [uaktualnianie podstawowego Load Balancer](upgrade-basic-standard.md) , aby uaktualnić podstawowe Load Balancer do usługa Load Balancer w warstwie Standardowa.
- Zobacz [tworzenie usługa Load Balancer w warstwie Standardowa publicznej](quickstart-load-balancer-standard-public-portal.md) , aby rozpocząć korzystanie z usługi Load Balancer: Utwórz jedną, twórz maszyny wirtualne z zainstalowanym niestandardowym rozszerzeniem usług IIS i Zrównoważ obciążenie aplikacji sieci Web między maszynami wirtualnymi.
- Dowiedz się więcej o [Azure Load Balancer](load-balancer-overview.md).
- Dowiedz się więcej o korzystaniu z [Usługa Load Balancer w warstwie Standardowa i strefy dostępności](load-balancer-standard-availability-zones.md).
- Dowiedz się więcej o [sondach kondycji](load-balancer-custom-probe-overview.md).
- Dowiedz się więcej na temat [diagnostyki usługa Load Balancer w warstwie Standardowa](load-balancer-standard-diagnostics.md).
- Dowiedz się więcej na temat korzystania z [Load Balancer dla połączeń wychodzących](load-balancer-outbound-connections.md).
- Poznaj [reguły ruchu wychodzącego](load-balancer-outbound-rules-overview.md).
- Więcej informacji [na temat resetowania protokołu TCP w trybie bezczynności](load-balancer-tcp-reset.md).
- Dowiedz się więcej o [Usługa Load Balancer w warstwie Standardowa z regułami równoważenia obciążenia dla portów ha](load-balancer-ha-ports-overview.md).
- Dowiedz się więcej o korzystaniu [z Load Balancer z wieloma frontonami](load-balancer-multivip-overview.md).
- Dowiedz się więcej na temat [sieciowych grup zabezpieczeń](../virtual-network/security-overview.md).
