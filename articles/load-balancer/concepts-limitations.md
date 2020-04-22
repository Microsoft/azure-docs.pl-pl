---
title: Komponenty i ograniczenia
titleSuffix: Azure Load Balancer
description: Omówienie składników i ograniczeń modułu równoważenia obciążenia platformy Azure.
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
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768254"
---
# <a name="load-balancer-components-and-limitations"></a>Składniki i ograniczenia modułu równoważenia obciążenia
Moduł równoważenia obciążenia platformy Azure zawiera kilka kluczowych składników dla jego działania.  Te składniki można skonfigurować w ramach subskrypcji za pośrednictwem witryny Azure portal, interfejsu wiersza polecenia platformy Azure lub programu Azure PowerShell.  

## <a name="load-balancer-components"></a>Składniki modułu równoważenia obciążenia

* **Konfiguracje ip frontu:** Adres IP modułu równoważenia obciążenia. Jest to punkt kontaktowy dla klientów. Adresy te mogą być następujące: 

    - **[Publiczny adres IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address)**
    - **[Prywatny adres IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#private-ip-addresses)**

* **Pula wewnętrznej bazy**danych: Grupa maszyn wirtualnych lub wystąpień w zestawie skalowania maszyny wirtualnej, które będą obsługiwać żądanie przychodzące. Aby skalować się w sposób ekonomiczny, aby sprostać dużej ilości ruchu przychodzącego, wskazówki dotyczące obliczeń zalecają dodanie większej liczby wystąpień do puli wewnętrznej bazy danych. Moduł równoważenia obciążenia natychmiast zmienia konfigurację za pomocą automatycznej ponownej konfiguracji podczas skalowania wystąpień w górę lub w dół. Dodawanie lub usuwanie maszyn wirtualnych z puli wewnętrznej bazy danych ponownie konfiguruje moduł równoważenia obciążenia bez dodatkowych operacji. Zakres puli wewnętrznej bazy danych jest dowolną maszyną wirtualną w sieci wirtualnej. Pula wewnętrznej bazy danych może mieć maksymalnie 1000 wystąpień wewnętrznej bazy danych lub konfiguracji adresów IP.
Podstawowe moduły równoważenia obciążenia mają ograniczony zakres (zestaw dostępności) mogą skalować tylko do 300 konfiguracji IP. Aby uzyskać więcej informacji na temat limitów, zobacz [Limity modułu równoważenia obciążenia](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). Rozważając sposób projektowania puli wewnętrznej bazy danych, można zaprojektować dla najmniejszej liczby zasobów puli poszczególnych zaplecza, aby dodatkowo zoptymalizować czas trwania operacji zarządzania. Nie ma różnicy w wydajności płaszczyzny danych lub skali.
* **Sondy kondycji:** **[Sonda kondycji](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)** służy do określenia kondycji wystąpień w puli wewnętrznej bazy danych. Można zdefiniować próg złej kondycji dla sond kondycji. Jeśli sonda nie odpowiada, moduł równoważenia obciążenia zaprzestaje inicjowania nowych połączeń z wystąpieniami o złej kondycji. Błąd sondy nie wpływa na istniejące połączenia. 
    
    Połączenie będzie kontynuowane do momentu, gdy aplikacja: 
    - Kończy przepływ
    - Występuje limit czasu bezczynnego
    - Maszyna wirtualna wyłącza się

    Moduł równoważenia obciążenia udostępnia różne typy sond kondycji dla punktów końcowych:
    - TCP
    - HTTP
    - Https (sonda HTTP z otoką TLS (Transport Layer Security)
     
     Podstawowy moduł równoważenia obciążenia nie obsługuje sond HTTPS. Ponadto podstawowy moduł równoważenia obciążenia zakończy wszystkie połączenia TCP (w tym nawiązane połączenia). 
    Aby uzyskać więcej informacji, zobacz [Typy sondowania](load-balancer-custom-probe-overview.md#types).

* **Reguły równoważenia obciążenia:** Reguły równoważenia obciążenia to te, które informują moduł równoważenia obciążenia, co należy wykonać, gdy. 
* **Przychodzące reguły NAT:** Przychodząta reguła translatora adresów sieciowych przekazuje ruch z określonego portu określonego adresu IP frontendu do określonego portu określonego wystąpienia zaplecza wewnątrz sieci wirtualnej. **[Przekazywanie portów](https://docs.microsoft.com/azure/load-balancer/tutorial-load-balancer-port-forwarding-portal)** odbywa się za pomocą tej samej dystrybucji opartej na mieszaniu, co równoważenie obciążenia. Typowymi scenariuszami tej funkcji są sesje protokołu RDP (Remote Desktop Protocol) lub Secure Shell (Secure Shell) dla poszczególnych wystąpień maszyn wirtualnych w sieci wirtualnej platformy Azure. Można mapować wiele wewnętrznych punktów końcowych do portów na tym samym adresie IP front-end. Adresy IP front-end można używać do zdalnego administrowania maszynami wirtualnymi bez dodatkowego pola szybkiego dostępu.
* **Reguły ruchu wychodzącego:** **[Reguła wychodząca](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-rules-overview)** konfiguruje wychodzące translacji adresów sieciowych (NAT) dla wszystkich maszyn wirtualnych lub wystąpień zidentyfikowanych przez pulę wewnętrznej bazy danych standardowego modułu równoważenia obciążenia, które mają zostać przetłumaczone na fronton.

  Podstawowy moduł równoważenia obciążenia nie obsługuje reguł wychodzących.

  ![Azure Load Balancer](./media/load-balancer-overview/load-balancer-overview.png)
* **Protokoły transportu:** Moduł równoważenia obciążenia nie obsługuje protokołu ICMP; Przepychy ICMP do modułu równoważenia obciążenia skierowanego do publicznej strony przesunie limit czasu. Aby pingować moduł równoważenia obciążenia skierowanego do publicznej strony, użyj pingu TCP

## <a name="load-balancer-concepts"></a><a name = "load-balancer-concepts"></a>Koncepcje modułu równoważenia obciążenia

Moduł równoważenia obciążenia zapewnia następujące podstawowe możliwości na potrzeby aplikacji protokołu TCP i UDP:

* **Algorytm równoważenia obciążenia:** Za pomocą modułu Azure Load Balancer można utworzyć regułę równoważenia obciążenia, aby dystrybuować ruch docierający do frontendu do wystąpień puli wewnętrznej bazy. Moduł równoważenia obciążenia używa algorytmu mieszania do dystrybucji przepływów przychodzących (nie bajtów) i przepisuje nagłówki przepływów do wystąpień puli wewnętrznej bazy. Serwer jest dostępny do odbierania nowych przepływów, gdy sonda kondycji wskazuje zdrowy punkt końcowy zaplecza.
Domyślnie moduł równoważenia obciążenia używa skrótu 5 krotek. 

   Skrót zawiera: 

   - **Źródłowy adres IP**
   - **Port źródłowy**
   - **Docelowy adres IP**
   - **Port docelowy**
   - **Numer protokołu IP do mapowania przepływów do dostępnych serwerów** 

Koligacji do źródłowego adresu IP można utworzyć przy użyciu skrótu 2- lub 3-krotka dla danej reguły. Wszystkie pakiety z tego samego przepływu pakietów trafiają do tego samego wystąpienia w ramach frontonu z równoważeniem obciążenia. Gdy klient uruchamia nowy przepływ z tego samego źródłowego adresu IP, port źródłowy jest zmieniany. W rezultacie skrót 5 krotek może spowodować, że ruch przejdzie do innego punktu końcowego wewnętrznej bazy danych.
Aby uzyskać więcej informacji, zobacz [Konfigurowanie trybu dystrybucji dla modułu Równoważenia obciążenia platformy Azure](./load-balancer-distribution-mode.md). 

Na poniższej ilustracji przedstawiono dystrybucję opartą na skrótach:

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer-distribution.svg" width="512" title="Dystrybucja oparta na skrótach">
</p>

  *Rysunek: dystrybucja oparta na skrótach*

* **Niezależność i przezroczystość aplikacji:** Moduł równoważenia obciążenia nie wchodzi w bezpośrednią interakcję z TCP lub UDP ani warstwą aplikacji. Każdy scenariusz aplikacji TCP lub UDP może być obsługiwany. Moduł równoważenia obciążenia nie kończy przepływów ani nie pobiera przepływów, nie wchodzi w interakcję z ładunkiem przepływu ani nie udostępnia żadnej funkcji bramy warstwy aplikacji. Uzgadniania protokołu zawsze występują bezpośrednio między klientem a wystąpieniem puli zaplecza. Odpowiedź na przepływ przychodzący to zawsze odpowiedź z maszyny wirtualnej. Po dostarczeniu przepływu do maszyny wirtualnej oryginalny źródłowy adres IP również jest zachowywany.
  * Na każdy punkt końcowy odpowiada tylko maszyna wirtualna. Na przykład uzgadnianie TCP zawsze występuje między klientem a wybraną maszyną wirtualną zaplecza. Odpowiedź na żądanie do frontu jest odpowiedzią generowaną przez maszynę wirtualną zaplecza. Po pomyślnym sprawdzeniu poprawności łączności z frontonia, sprawdzasz łączność end-to-end z co najmniej jedną maszyną wirtualną zaplecza.
  * Ładunki aplikacji są przezroczyste dla modułu równoważenia obciążenia. Każda aplikacja UDP lub TCP może być obsługiwana.
  * Ponieważ moduł równoważenia obciążenia nie wchodzi w interakcję z ładunkiem TCP i zapewnia odciążanie TLS, można tworzyć scenariusze szyfrowane end-to-end. Za pomocą modułu równoważenia obciążenia zyskuje duże skalowanie w poziomie dla aplikacji TLS, kończąc połączenie TLS na samej maszynie wirtualnej. Na przykład pojemność kluczowania sesji TLS jest ograniczona tylko przez typ i liczbę maszyn wirtualnych dodawanych do puli zaplecza.

* **Połączenia wychodzące: Wszystkie przepływy wychodzące**z prywatnych adresów IP wewnątrz sieci wirtualnej do publicznych adresów IP w Internecie mogą być tłumaczone na adres IP frontonu modułu równoważenia obciążenia. Gdy publiczny frontołów jest powiązany z wewnętrznej bazy wirtualnej za pomocą reguły równoważenia obciążenia, platforma Azure tłumaczy połączenia wychodzące do adresu IP publicznego frontona. Ta konfiguracja ma następujące zalety:
  * Łatwe uaktualnianie i odzyskiwanie po awarii usług, ponieważ fronton może być dynamicznie mapowane do innego wystąpienia usługi.
  * Łatwiejsze zarządzanie listą kontroli dostępu (ACL). Listy ACL wyrażone jako front-end IP nie zmieniają się, ponieważ usługi skalują się w górę lub w dół lub są ponownie rozmieszczane. Tłumaczenie połączeń wychodzących na mniejszą liczbę adresów IP niż maszyny zmniejsza obciążenie związane z implementacjam list bezpiecznych adresatów.

  Standardowy moduł równoważenia obciążenia wykorzystuje [solidny, skalowalny i przewidywalny algorytm SNAT.](load-balancer-outbound-connections.md#snat) Są to kluczowe założenia, o których należy pamiętać podczas pracy ze standardowym modułem równoważenia obciążenia:

    - zasady równoważenia obciążenia wywnioskować, jak SNAT jest zaprogramowany. Reguły równoważenia obciążenia są specyficzne dla protokołu. SNAT jest specyficzne dla protokołu i konfiguracja powinna odzwierciedlać to, a nie tworzyć efekt uboczny.

    - **Wiele frontendów** Gdy dostępnych jest wiele frontonów, używane są wszystkie frontony, a każda fronton mnoży liczbę dostępnych portów SNAT. Jeśli chcesz więcej portów SNAT, ponieważ oczekujesz lub już występują duże zapotrzebowanie na połączenia wychodzące, można również dodać przyrostowe zapasów portów SNAT, konfigurując dodatkowe frontendy, reguły i pul zaplecza do tych samych zasobów maszyny wirtualnej.

    - **Sterowanie, która przednia jest używana dla ruchu wychodzącego** Można wybrać i kontrolować, jeśli nie chcesz, aby określona frontend była używana dla połączeń wychodzących. Jeśli chcesz ograniczyć połączenia wychodzące tylko pochodzić z określonego adresu IP frontendu, można opcjonalnie wyłączyć wychodzących SNAT na regułę, która wyraża mapowanie wychodzące.

    - **Steruj wychodzących scenariuszy połączeń** wychodzących są jawne i połączenia wychodzące nie istnieje, dopóki nie został określony. Standardowy moduł równoważenia obciążenia istnieje w kontekście sieci wirtualnej.  Sieć wirtualna jest izolowanym, prywatną siecią.  O ile skojarzenie z publicznym adresem IP nie istnieje, łączność publiczna jest niedozwolona.  Można dotrzeć do [punktów końcowych usługi sieci wirtualnej,](../virtual-network/virtual-network-service-endpoints-overview.md) ponieważ znajdują się one wewnątrz sieci wirtualnej i lokalne.  Jeśli chcesz ustanowić łączność wychodzącą z miejscem docelowym poza siecią wirtualną, masz dwie opcje:
        - przypisywanie standardowego publicznego adresu IP jednostki SKU jako publicznego adresu IP na poziomie wystąpienia do zasobu maszyny wirtualnej lub
        - umieść zasób maszyny wirtualnej w puli wewnętrznej bazy danych publicznego standardowego modułu równoważenia obciążenia.

        Oba umożliwiają łączność wychodzącą z sieci wirtualnej do sieci zewnętrznej sieci wirtualnej. 

        Jeśli z pulą zaplecza, w której znajduje się zasób maszyny wirtualnej, jest _skojarzony tylko_ wewnętrzny standardowy moduł równoważenia obciążenia, maszyna wirtualna może dotrzeć tylko do zasobów sieci wirtualnej i [punktów końcowych usługi sieci wirtualnej.](../virtual-network/virtual-network-service-endpoints-overview.md)  Można wykonać kroki opisane w poprzednim akapicie, aby utworzyć łączność wychodzącą.

        Łączność wychodząca zasobu maszyny wirtualnej niesłączona ze standardowymi jednostkami SKU pozostaje jak poprzednio.

        Przejrzyj [szczegółową dyskusję na temat Połączeń wychodzących](load-balancer-outbound-connections.md).

* **Strefy dostępności: Standardowy**moduł równoważenia obciążenia obsługuje dodatkowe możliwości w regionach, w których dostępne są strefy dostępności. Te funkcje są przyrostowe do wszystkich standardowych load balancer zapewnia.  Konfiguracje stref dostępności są dostępne dla obu typów, publicznego i wewnętrznego standardowego modułu równoważenia obciążenia.
 Nadmiarowy frontend strefy przetrwa awarię strefy i jest obsługiwany przez dedykowaną infrastrukturę we wszystkich strefach jednocześnie. 
Ponadto można zagwarantować frontend do określonej strefy. Zobona frontend dzieli los z odpowiednią strefą i jest obsługiwana tylko przez dedykowaną infrastrukturę w jednej strefie.
Równoważenie obciążenia między strefami jest dostępne dla puli wewnętrznej bazy danych, a każdy zasób maszyny wirtualnej w sieci wirtualnej może być częścią puli zaplecza.
Podstawowy moduł równoważenia obciążenia nie obsługuje stref.
Aby uzyskać więcej informacji, zapoznaj [się ze szczegółowymi omówieniami dotyczących zdolności i](load-balancer-standard-availability-zones.md) stref dostępności w [strefach dostępności.](../availability-zones/az-overview.md)

* **Porty wysokiej**klasy: Można skonfigurować reguły równoważenia obciążenia, aby skala aplikacji i być wysoce niezawodne. When you use an HA Ports load-balancing rule, Standard Load Balancer will provide per flow load balancing on every ephemeral port of an internal Standard Load Balancer's frontend IP address.  Funkcja jest przydatna w innych scenariuszach, w których określenie poszczególnych portów jest niepraktyczne lub niepożądane. Reguła równoważenia obciążenia portów wysokiej jakości umożliwia tworzenie scenariuszy active-passive lub active-active n+1 dla wirtualnych urządzeń sieciowych i dowolnej aplikacji, która wymaga dużych zakresów portów przychodzących.  Sonda kondycji może służyć do określenia, które zaplecze powinny odbierać nowe przepływy.  Sieciowej grupy zabezpieczeń można użyć do emulacji scenariusza zakresu portów. Podstawowy moduł równoważenia obciążenia nie obsługuje portów wysokiej obsługi.
Przegląd [szczegółowej dyskusji na temat portów wysokiej informacji](load-balancer-ha-ports-overview.md)
>[!IMPORTANT]
> Jeśli planujesz używać wirtualnego urządzenia sieciowego, skontaktuj się z dostawcą, aby uzyskać wskazówki, czy ich produkt został przetestowany za pomocą portów wysokiej jakości i postępuj zgodnie z ich konkretnymi wskazówkami dotyczącymi implementacji. 

* **Wiele frontendów:** Moduł równoważenia obciążenia obsługuje wiele reguł z wieloma frontendami.  Standardowy moduł równoważenia obciążenia rozszerza tę tę ofertę na scenariusze wychodzące.  Scenariusze wychodzące są zasadniczo odwrotnością reguły równoważenia obciążenia przychodzącego.  Reguła równoważenia obciążenia przychodzącego tworzy również skojarzenie dla połączeń wychodzących. Standardowy moduł równoważenia obciążenia używa wszystkich frontendów skojarzonych z zasobem maszyny wirtualnej za pomocą reguły równoważenia obciążenia.  Ponadto parametr w regule równoważenia obciążenia i pozwala pominąć regułę równoważenia obciążenia na potrzeby łączności wychodzącej, która umożliwia wybór określonych frontendów, w tym brak.

Dla porównania, podstawowy moduł równoważenia obciążenia wybiera losowo pojedynczą przód i nie ma możliwości kontrolowania, który z nich został wybrany.
## <a name="load-balancer-types"></a>Typy modułu równoważenia obciążenia

### <a name="public-load-balancer"></a><a name = "publicloadbalancer"></a>Publiczny moduł równoważenia obciążenia

Publiczny moduł równoważenia obciążenia mapuje publiczny adres IP i port ruchu przychodzącego na prywatny adres IP i port maszyny Wirtualnej. Moduł równoważenia obciążenia mapuje ruch w drugą stronę dla ruchu odpowiedzi z maszyny Wirtualnej. Można rozpowszechniać określone typy ruchu na wielu maszynach wirtualnych lub usługach, stosując reguły równoważenia obciążenia. Na przykład można rozłożyć obciążenie ruchu związanego z żądaniami internetowymi na wiele serwerów internetowych.

>[!NOTE]
>Można zaimplementować tylko jeden publiczny moduł równoważenia obciążenia i jeden wewnętrzny moduł równoważenia obciążenia na zestaw dostępności.

Na poniższej ilustracji przedstawiono punkt końcowy z równoważenia obciążenia dla ruchu internetowego, który jest współużytkował trzy maszyny wirtualne dla publicznego i portu TCP 80. Te trzy maszyny wirtualne znajdują się w zestawie z równoważeniem obciążenia.

<p align="center">
  <img src="./media/load-balancer-overview/load-balancer-http.svg" width="256" title="Publiczny moduł równoważenia obciążenia">
</p>

*Rysunek: Równoważenie ruchu internetowego przy użyciu publicznego modułu równoważenia obciążenia*

Klienci internetowi wysyłają żądania stron internetowych na publiczny adres IP aplikacji sieci web na porcie TCP 80. Moduł równoważenia obciążenia platformy Azure rozdziela żądania na trzy maszyny wirtualne w zestawie z równoważenia obciążenia. Aby uzyskać więcej informacji na temat algorytmów równoważenia obciążenia, zobacz [pojęcia modułu równoważenia obciążenia](concepts-limitations.md#load-balancer-concepts).

Moduł równoważenia obciążenia platformy Azure domyślnie rozdziela ruch sieciowy między wiele wystąpień maszyn wirtualnych. Można również skonfigurować koligację sesji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie trybu dystrybucji dla modułu Równoważenia obciążenia platformy Azure](load-balancer-distribution-mode.md).

### <a name="internal-load-balancer"></a><a name = "internalloadbalancer"></a> Wewnętrzny moduł równoważenie obciążenia

Wewnętrzny moduł równoważenia obciążenia kieruje ruch tylko do zasobów, które znajdują się wewnątrz sieci wirtualnej lub które korzystają z sieci VPN w celu uzyskania dostępu do infrastruktury platformy Azure, w przeciwieństwie do publicznego modułu równoważenia obciążenia. Infrastruktura platformy Azure ogranicza dostęp do adresów IP z równoważenia obciążenia sieci wirtualnej. Adresy IP frontonu i sieci wirtualne nigdy nie są bezpośrednio narażone na punkt końcowy internetu. Wewnętrzne aplikacje biznesowe są uruchomiane na platformie Azure i dostęp do nich jest uzyskiwany z poziomu platformy Azure lub zasobów lokalnych.

Wewnętrzny moduł równoważenia obciążenia pozwala na następujące typy równoważenia obciążenia:

* **W sieci wirtualnej:** Równoważenie obciążenia z maszyn wirtualnych w sieci wirtualnej do zestawu maszyn wirtualnych, które znajdują się w tej samej sieci wirtualnej.
* **W przypadku sieci wirtualnej międzylokacje:** Równoważenie obciążenia z komputerów lokalnych do zestawu maszyn wirtualnych, które znajdują się w tej samej sieci wirtualnej.
* **W przypadku aplikacji wielowarstwowych:** Równoważenie obciążenia dla aplikacji wielowarstwowych z internetem, w których warstwy zaplecza nie są oparte na Internecie. Warstwy zaplecza wymagają równoważenia obciążenia ruchu z warstwy internetowej. Zobacz następny rysunek.
* **Dla aplikacji biznesowych**: równoważenie obciążenia na potrzeby aplikacji biznesowych, które są hostowane na platformie Azure bez użycia dodatkowego sprzętu lub oprogramowania służącego do równoważenia obciążenia. Ten scenariusz obejmuje serwery lokalne, które znajdują się w zestawie komputerów, których ruch jest równoważenia obciążenia.


<p align="center">
  <img src="./media/load-balancer-overview/load-balancer.svg" width="256" title="Publiczny moduł równoważenia obciążenia">
</p>

*Rysunek: Równoważenie aplikacji wielowarstwowych przy użyciu publicznego i wewnętrznego modułu równoważenia obciążenia*

## <a name="load-balancer-sku-comparison"></a><a name="skus"></a> Porównanie jednostek SKU modułu równoważenia obciążenia

Moduł równoważenia obciążenia obsługuje zarówno jednostki SKU basic, jak i Standard. Te jednostki SKU różnią się skalą scenariusza, funkcjami i cenami. Każdy scenariusz, który jest możliwy za pomocą podstawowego modułu równoważenia obciążenia, można utworzyć za pomocą standardowego modułu równoważenia obciążenia. Interfejsy API dla obu jednostek SKU są podobne i są wywoływane za pomocą specyfikacji jednostki SKU. Interfejs API do obsługi jednostek SKU dla modułu równoważenia obciążenia i publicznego adresu IP jest dostępny, począwszy od `2017-08-01` interfejsu API. Oba jednostki SKU współużytkuje ten sam ogólny interfejs API i struktury.

Pełna konfiguracja scenariusza może się nieznacznie różnić w zależności od jednostki SKU. Dokumentacja modułu równoważenia obciążenia wywołuje, gdy artykuł dotyczy tylko określonej jednostki SKU. Aby porównać i poznać różnice, zapoznaj się z poniższą tabelą. Aby uzyskać więcej informacji, zobacz [omówienie standardowego modułu równoważenia obciążenia platformy Azure](load-balancer-standard-overview.md).

Aby uaktualnić podstawowy moduł równoważenia obciążenia do [standardu, zobacz Uaktualnianie podstawowego modułu równoważenia obciążenia](upgrade-basic-standard.md).

>[!NOTE]
> Firma Microsoft zaleca standardowy moduł równoważenia obciążenia.
Autonomiczne maszyny wirtualne, zestawy dostępności i zestawy skalowania maszyn wirtualnych można połączyć tylko jednej jednostki SKU — nigdy do obu. Moduł równoważenia obciążenia i publiczna jednostka SKU adresu IP muszą być zgodne podczas używania ich z publicznymi adresami IP. Moduł równoważenia obciążenia i publiczne jednostki SKU IP nie są modyfikowalne.

[!INCLUDE [comparison table](../../includes/load-balancer-comparison-table.md)]

Aby uzyskać więcej informacji, zobacz [Limity równoważenia obciążenia](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#load-balancer). Szczegółowe informacje dotyczące modułu równoważenia obciążenia w warstwie Standardowa zawiera [przegląd](load-balancer-standard-overview.md), [cennik](https://aka.ms/lbpricing) i [umowa dotycząca poziomu usług](https://aka.ms/lbsla).

## <a name="limitations"></a><a name = "limitations"></a>Ograniczenia

- Jednostki SKU nie są modyfikowalne. Nie można zmienić jednostki SKU istniejącego zasobu.
- Autonomiczny zasób maszyny wirtualnej, zasób zestawu zestawów dostępności lub zestaw skalowania maszyny wirtualnej może odwoływać się do jednej jednostki SKU, nigdy obu.
- Reguła równoważenia obciążenia nie może obejmować dwóch sieci wirtualnych.  Frontendy i ich powiązane wystąpienia wewnętrznej bazy danych muszą znajdować się w tej samej sieci wirtualnej.  
- [Operacje subskrypcji przenoszenia](../azure-resource-manager/management/move-resource-group-and-subscription.md) nie są obsługiwane dla standardowych zasobów LB i publicznych adresów IP.
- Role procesów roboczych sieci Web bez sieci wirtualnej i innych usług platformy firmy Microsoft mogą być dostępne z wystąpień za tylko wewnętrzny moduł równoważenia obciążenia standardowego. Nie możesz polegać na tym, ponieważ sama usługa lub platforma bazowa może ulec zmianie bez powiadomienia. Należy zawsze zakładać, że należy utworzyć [łączność wychodzącą](load-balancer-outbound-connections.md) jawnie w razie potrzeby podczas korzystania z wewnętrznego standardowego modułu równoważenia obciążenia tylko.

- Moduł równoważenia obciążenia i przekazywania portów dla określonych protokołów TCP lub UDP. Reguły równoważenia obciążenia i przychodzące reguły NAT obsługują protokół TCP i UDP, ale nie inne protokoły IP, w tym protokół ICMP.

  Moduł równoważenia obciążenia nie kończy, nie odpowiada ani w inny sposób nie wchodzi w interakcję z ładunkiem przepływu UDP lub TCP. To nie jest proxy. Pomyślne sprawdzenie poprawności łączności z frontomatem musi odbywać się w paśmie z tym samym protokołem, który jest używany w równoważeniu obciążenia lub przychodzącej regule NAT. Co najmniej jedna z maszyn wirtualnych musi wygenerować odpowiedź dla klienta, aby zobaczyć odpowiedź z frontu.

  Nie odbieranie odpowiedzi w paśmie z frontonia modułu równoważenia obciążenia wskazuje, że żadne maszyny wirtualne nie mogą odpowiedzieć. Nic nie może wchodzić w interakcje z frontonatu modułu równoważenia obciążenia bez maszyny wirtualnej w stanie odpowiedzieć. Ta zasada ma również zastosowanie do połączeń wychodzących, gdzie port maskarada SNAT jest obsługiwana tylko dla protokołu TCP i UDP. Wszelkie inne protokoły IP, w tym ICMP, nie powiodą się. Przypisz publiczny adres IP na poziomie wystąpienia, aby złagodzić ten problem. Aby uzyskać więcej informacji, zobacz [Opis SNAT i PAT](load-balancer-outbound-connections.md#snat).

- Wewnętrzne moduły równoważenia obciążenia nie tłumaczą połączeń wychodzących na frontołów wewnętrznego modułu równoważenia obciążenia, ponieważ oba znajdują się w prywatnej przestrzeni adresów IP. Publiczne moduły równoważenia obciążenia zapewniają [połączenia wychodzące](load-balancer-outbound-connections.md) z prywatnych adresów IP wewnątrz sieci wirtualnej do publicznych adresów IP. W przypadku wewnętrznych modułów równoważenia obciążenia takie podejście pozwala uniknąć potencjalnego wyczerpania portów SNAT w unikatowej wewnętrznej przestrzeni adresów IP, gdzie tłumaczenie nie jest wymagane.

  Efektem ubocznym jest to, że jeśli przepływ wychodzący z maszyny Wirtualnej w puli zaplecza próbuje przepływ do przodu wewnętrznego modułu równoważenia obciążenia w puli _i_ jest mapowany z powrotem do siebie, dwie nogi przepływu nie pasują do siebie. Ponieważ nie są zgodne, przepływ kończy się niepowodzeniem. Przepływ powiedzie się, jeśli przepływ nie mapuje z powrotem do tej samej maszyny Wirtualnej w puli zaplecza, który utworzył przepływ do frontu.

  Gdy przepływ mapuje z powrotem do siebie, przepływ wychodzący wydaje się pochodzić z maszyny Wirtualnej do frontu i odpowiedni przepływ przychodzący wydaje się pochodzić z maszyny Wirtualnej do siebie. Z punktu widzenia systemu operacyjnego gościa przychodzące i wychodzące części tego samego przepływu nie pasują do siebie wewnątrz maszyny wirtualnej. Stos TCP nie rozpoznaje tych połówek tego samego przepływu jako część tego samego przepływu. Źródło i miejsce docelowe nie są zgodne. Gdy przepływ mapuje do innych maszyn wirtualnych w puli zaplecza, połówki przepływu są zgodne i maszyna wirtualna może odpowiedzieć na przepływ.

  Symptom dla tego scenariusza jest przerywany limity czasu połączenia, gdy przepływ powraca do tego samego zaplecza, który pochodzi przepływu. Typowe obejścia obejmują wstawianie warstwy serwera proxy za wewnętrznym modułem równoważenia obciążenia i używanie reguł stylu Direct Server Return (DSR). Aby uzyskać więcej informacji, zobacz [wiele frontów dla modułu równoważenia obciążenia platformy Azure](load-balancer-multivip-overview.md).

  Wewnętrzny moduł równoważenia obciążenia można połączyć z dowolnym serwerem proxy innych firm lub użyć wewnętrznej [bramy aplikacji](../application-gateway/application-gateway-introduction.md) w scenariuszach serwera proxy z protokołem HTTP/HTTPS. Chociaż można użyć publicznego modułu równoważenia obciążenia, aby złagodzić ten problem, wynikowy scenariusz jest podatny na [wyczerpanie SNAT](load-balancer-outbound-connections.md#snat). Unikaj tego drugiego podejścia, chyba że starannie zarządzane.

- Ogólnie rzecz biorąc przekazywanie fragmentów adresów IP nie jest obsługiwane w regułach równoważenia obciążenia. Fragmentacja adresów IP pakietów UDP i TCP nie jest obsługiwana w regułach równoważenia obciążenia. Reguły równoważenia obciążenia portów o wysokiej dostępności mogą służyć do przekazywania istniejących fragmentów adresów IP. Aby uzyskać więcej informacji, zobacz [Omówienie portów o wysokiej dostępności](load-balancer-ha-ports-overview.md).

## <a name="next-steps"></a>Następne kroki

- Zobacz [Uaktualnianie podstawowego modułu równoważenia obciążenia,](upgrade-basic-standard.md) aby uaktualnić podstawowy moduł równoważenia obciążenia do standardowego modułu równoważenia obciążenia.
- Zobacz [Tworzenie publicznego standardowego modułu równoważenia obciążenia,](quickstart-load-balancer-standard-public-portal.md) aby rozpocząć korzystanie z modułu równoważenia obciążenia: utwórz jedną z nich, utwórz maszyny wirtualne z zainstalowanym niestandardowym rozszerzeniem usług IIS i równoważ aplikację sieci web między maszynami wirtualnymi.
- Dowiedz się więcej o [równoważącym obciążenia platformy Azure](load-balancer-overview.md).
- Dowiedz się więcej o [używaniu standardowego modułu równoważenia obciążenia i stref dostępności](load-balancer-standard-availability-zones.md).
- Dowiedz się więcej o [sondach kondycji](load-balancer-custom-probe-overview.md).
- Dowiedz się więcej o [standardowej diagnostyce równoważenia obciążenia](load-balancer-standard-diagnostics.md).
- Dowiedz się więcej o [używaniu modułu równoważenia obciążenia dla połączeń wychodzących](load-balancer-outbound-connections.md).
- Dowiedz się więcej [o regułach ruchu wychodzącego](load-balancer-outbound-rules-overview.md).
- Dowiedz się więcej o [resetowanie TCP na bezczynniu](load-balancer-tcp-reset.md).
- Dowiedz się więcej o [standardowym równoważeniu obciążenia z regułami równoważenia obciążenia portów WYSOKIEJ.](load-balancer-ha-ports-overview.md)
- Dowiedz się więcej o [używaniu modułu równoważenia obciążenia z wieloma frontendami](load-balancer-multivip-overview.md).
- Dowiedz się więcej o [sieciowych grupach zabezpieczeń](../virtual-network/security-overview.md).
