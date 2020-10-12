---
title: Sieć produkcyjna platformy Azure
description: Dowiedz się więcej o sieci produkcyjnej platformy Azure. Zobacz metody dostępu zabezpieczeń i mechanizmy ochrony w celu nawiązania połączenia z siecią.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 724c2ee2d74ca9cab11280e92faac71c2cb2218e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87543813"
---
# <a name="the-azure-production-network"></a>Sieć produkcyjna platformy Azure
Użytkownicy sieci produkcyjnej platformy Azure obejmują zarówno klientów zewnętrznych, którzy uzyskują dostęp do własnych aplikacji platformy Azure, jak i do wewnętrznego personelu pomocy technicznej platformy Azure, który zarządza siecią produkcyjną. W tym artykule omówiono metody dostępu zabezpieczeń i mechanizmy ochrony na potrzeby ustanawiania połączeń z siecią produkcyjną platformy Azure.

## <a name="internet-routing-and-fault-tolerance"></a>Routing internetowy i odporność na uszkodzenia
Globalnie nadmiarowa wewnętrzna i zewnętrzna infrastruktura usługi nazw domen Azure (DNS), w połączeniu z wieloma podstawowymi i pomocniczymi klastrami serwerów DNS, zapewnia odporność na uszkodzenia. W tym samym czasie dodatkowe kontrolki zabezpieczeń sieci platformy Azure, takie jak program do skalowania, służą do zapobiegania atakom typu "odmowa usługi" (DDoS) i ochrony integralności usług Azure DNS.

Serwery Azure DNS znajdują się w wielu centrach centrów danych. Implementacja Azure DNS obejmuje hierarchię serwerów pomocniczych i podstawowych DNS, aby publicznie rozwiązywać nazwy domen klientów platformy Azure. Nazwy domen zwykle są rozpoznawane jako adres CloudApp.net, który zawija wirtualny adres IP (VIP) dla usługi klienta. Unikatowa dla platformy Azure, adres VIP, który odpowiada wewnętrznemu dedykowanemu adresowi IP (DIP) tłumaczenia dzierżawy, jest realizowany przez moduły równoważenia obciążenia firmy Microsoft odpowiedzialne za ten adres VIP.

Platforma Azure jest hostowana w geograficznie rozproszonych centrach danych platformy Azure w Stanach Zjednoczonych i jest oparta na najnowocześniejszych platformach routingu, które implementują niezawodne i skalowalne standardy architektury. Wśród istotnych funkcji są:

- Wieloprotokołowa usługa do przełączania etykiet (MPLS), która zapewnia wydajne wykorzystanie linków i bezpieczne obniżenie wydajności w przypadku wystąpienia awarii.
- Sieci są implementowane przy użyciu "potrzebnych i jednej" (N + 1) architektur nadmiarowości.
- Zewnętrznie centra danych są obsługiwane przez dedykowane obwody o wysokiej przepustowości, które nadmiarowo łączą właściwości z ponad 1 200 dostawcami usług internetowych w wielu punktach komunikacji równorzędnej. To połączenie zapewnia ponad 2 000 gigabajtów na sekundę (GB/s) pojemności brzegowej.

Ze względu na to, że firma Microsoft jest właścicielem własnych obwodów sieciowych między centrami danych, te atrybuty pomagają platformie Azure uzyskać dostęp do sieci 99,9 + procent bez potrzeby tradycyjnych dostawców usług internetowych innych firm.

## <a name="connection-to-production-network-and-associated-firewalls"></a>Połączenie z siecią produkcyjną i skojarzonymi zaporami
Zasada przepływu ruchu internetowego w sieci platformy Azure kieruje ruch do sieci produkcyjnej platformy Azure, która znajduje się w najbliższym regionalnym centrum danych w Stanach Zjednoczonych. Ponieważ produkcyjne centra danych platformy Azure utrzymują spójną architekturę sieci i sprzęt, opis przepływu ruchu, który następuje, jest spójny ze wszystkimi centrami zasobów.

Po przekierowaniu ruchu internetowego z platformy Azure do najbliższego centrum danych zostanie nawiązane połączenie z routerami dostępu. Te routery dostępu służą do izolowania ruchu między węzłami platformy Azure i maszynami wirtualnymi utworzonymi przez klienta. Urządzenia infrastruktury sieciowej w lokalizacji dostępu i krawędzi są punktami granicznymi, w których stosowane są filtry ruchu przychodzącego i wychodzącego. Te routery są konfigurowane za pośrednictwem listy kontroli dostępu warstwowego (ACL) w celu filtrowania niechcianego ruchu sieciowego i stosowania limitów szybkości ruchu, w razie potrzeby. Ruch dozwolony przez listę ACL jest kierowany do modułów równoważenia obciążenia. Routery dystrybucji zostały zaprojektowane tak, aby zezwalać tylko na adresy IP zatwierdzone przez firmę Microsoft, zapewnić antysfałszowanie i ustanowić połączenia TCP korzystające z list ACL.

Zewnętrzne urządzenia równoważenia obciążenia znajdują się za routerami dostępu w celu przeprowadzenia translacji adresów sieciowych (NAT) z adresów IP w ramach routingu internetowego do wewnętrznych adresów IP platformy Azure. Urządzenia również kierują pakiety do prawidłowych wewnętrznych adresów IP i portów produkcyjnych i działają jako mechanizm ochrony w celu ograniczenia ujawniania wewnętrznej przestrzeni adresowej sieci produkcyjnej.

Domyślnie firma Microsoft wymusza protokół Hypertext Transfer Protocol Secure (HTTPS) dla całego ruchu przesyłanego do przeglądarek sieci Web klientów, w tym logowania i całego ruchu. Użycie protokołu TLS v 1.2 umożliwia bezpieczny tunel do przepływu ruchu przez program. Listy kontroli dostępu i routery podstawowe zapewniają, że źródło ruchu jest zgodne z oczekiwaniami.

Istotną różnicą w tej architekturze, gdy jest ona porównywana z tradycyjną architekturą zabezpieczeń, jest to, że nie ma żadnych dedykowanych zapór sprzętowych, wyspecjalizowanych urządzeń do wykrywania lub zapobiegania włamaniom lub innych urządzeń zabezpieczających, które są zwykle oczekiwane przed połączeniami w środowisku produkcyjnym platformy Azure. Klienci zazwyczaj oczekują tych urządzeń zapory sprzętowej w sieci platformy Azure; nie są jednak stosowane na platformie Azure. Niemal wyłącznie te funkcje zabezpieczeń są wbudowane w oprogramowanie, które uruchamia środowisko platformy Azure w celu zapewnienia niezawodnego, wielowarstwowego mechanizmu zabezpieczeń, w tym funkcji zapory. Ponadto zakres granicy i skojarzone rozwojem krytycznych urządzeń zabezpieczeń są łatwiejsze do zarządzania i spisu, jak pokazano na poprzedniej ilustracji, ponieważ jest zarządzany przez oprogramowanie, na którym działa platforma Azure.

## <a name="core-security-and-firewall-features"></a>Podstawowe funkcje zabezpieczeń i zapory
Platforma Azure implementuje niezawodne funkcje zabezpieczeń oprogramowania i zapory na różnych poziomach w celu wymuszenia funkcji zabezpieczeń, które zwykle oczekują w tradycyjnych środowiskach, aby chronić granicę podstawowej autoryzacji zabezpieczeń.

### <a name="azure-security-features"></a>Funkcje zabezpieczeń platformy Azure
Platforma Azure implementuje Zapory programowe oparte na hoście w sieci produkcyjnej. Kilka podstawowych funkcji zabezpieczeń i zapory znajduje się w podstawowym środowisku platformy Azure. Te funkcje zabezpieczeń odzwierciedlają strategię obrony w środowisku platformy Azure. Dane klienta na platformie Azure są chronione przez następujące zapory:

**Zapora funkcji hypervisor (filtr pakietów)**: Ta zapora jest zaimplementowana w funkcji hypervisor i skonfigurowana przez agenta kontrolera sieci szkieletowej (FC). Ta Zapora chroni dzierżawcę, który działa w ramach maszyny wirtualnej przed nieautoryzowanym dostępem. Domyślnie po utworzeniu maszyny wirtualnej cały ruch jest blokowany, a następnie Agent FC dodaje reguły i wyjątki w filtrze w celu zezwolenia na ruch autoryzowany.

Dwie kategorie reguł są zaprogramowane tutaj:

- **Konfiguracja maszyny lub reguły infrastruktury**: domyślnie cała komunikacja jest blokowana. Istnieją wyjątki, które umożliwiają maszynie wirtualnej wysyłanie i odbieranie informacji o komunikacji Dynamic Host Configuration Protocol (DHCP) i DNS oraz wysyłanie ruchu do "publicznego" ruchu przychodzącego do innych maszyn wirtualnych w klastrze FC i na serwerze aktywacji systemu operacyjnego. Ponieważ lista dozwolonych miejsc docelowych maszyn wirtualnych nie obejmuje podsieci routera platformy Azure i innych właściwości firmy Microsoft, reguły działają jako warstwa obrony.
- **Reguły dotyczące pliku konfiguracji roli**: definiuje listy ACL ruchu przychodzącego na podstawie modelu usługi dzierżawców. Na przykład jeśli dzierżawa ma fronton sieci Web na porcie 80 dla określonej maszyny wirtualnej, port 80 jest otwarty dla wszystkich adresów IP. Jeśli maszyna wirtualna ma uruchomioną rolę procesu roboczego, rola proces roboczy jest otwierana tylko na maszynie wirtualnej w ramach tej samej dzierżawy.

**Natywna Zapora hosta**: usługa Azure Service Fabric i usługa Azure Storage działają w natywnym systemie operacyjnym, w którym nie ma funkcji hypervisor i dlatego Zapora systemu Windows jest skonfigurowana z poprzednimi dwoma zestawami reguł.

**Zapora**hosta: Zapora hosta chroni partycję hosta, w której jest uruchomiona funkcja hypervisor. Reguły są programowane, aby zezwalać tylko na pola FC i Przeskocz, aby komunikować się z partycją hosta na określonym porcie. Innymi wyjątkami są Zezwalanie na odpowiedzi DHCP i odpowiedzi DNS. Na platformie Azure jest używany plik konfiguracji komputera zawierający szablon reguł zapory dla partycji hosta. Istnieje również wyjątek zapory hosta, który umożliwia maszynom wirtualnym komunikowanie się ze składnikami hosta, serwerem sieci szkieletowej i serwerem metadanych przy użyciu określonych protokołów/portów.

**Zapora gościa**: Element Zapora systemu Windows systemu operacyjnego gościa, który można skonfigurować przez klientów na maszynach wirtualnych i magazynach klienta.

Dodatkowe funkcje zabezpieczeń wbudowane w funkcje platformy Azure obejmują:

- Składniki infrastruktury, które mają przypisane adresy IP z zakresu DIP. Osoba atakująca w Internecie nie może adresować ruchu do tych adresów, ponieważ nie dociera do firmy Microsoft. Routery bramy internetowej filtrują pakiety, które są rozkierowane wyłącznie do adresów wewnętrznych, dlatego nie będą mogły wejść do sieci produkcyjnej. Jedynymi składnikami, które akceptują ruch kierowany do adresów VIP, są usługi równoważenia obciążenia.
- Zapory wdrożone na wszystkich węzłach wewnętrznych mają trzy podstawowe zagadnienia dotyczące architektury zabezpieczeń dla dowolnego z tych scenariuszy:

   - Zapory są umieszczane za modułem równoważenia obciążenia i akceptują pakiety z dowolnego miejsca. Te pakiety mają być udostępniane zewnętrznie i będą odpowiadały otwartym portom w tradycyjnych zaporach obwodowych.
   - Zapory akceptują pakiety tylko z ograniczonym zestawem adresów. To zagadnienie jest częścią strategii z szczegółowymi informacjami na temat ataków DDoS. Takie połączenia są uwierzytelniane kryptograficznie.
   - Dostęp do zapór można uzyskać tylko z poziomu wybranych węzłów wewnętrznych. Akceptują one tylko pakiety z listy wyliczeniowych źródłowych adresów IP, z których wszystkie są odbierane w sieci platformy Azure. Na przykład atak w sieci firmowej może kierować żądania do tych adresów, ale ataki byłyby blokowane, chyba że adres źródłowy pakietu był jeden z listy Wyliczenie w sieci platformy Azure.
     - Router dostępu w obszarze obwodu blokuje pakiety wychodzące, które są adresowane do adresu znajdującego się w sieci platformy Azure z powodu skonfigurowanych tras statycznych.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat sposobu zabezpieczania infrastruktury platformy Azure przez firmę Microsoft, zobacz:

- [Funkcje platformy Azure, lokalne i zabezpieczenia fizyczne](physical-security.md)
- [Dostępność infrastruktury platformy Azure](infrastructure-availability.md)
- [Składniki i granice systemu informacji platformy Azure](infrastructure-components.md)
- [Architektura sieci platformy Azure](infrastructure-network.md)
- [Azure SQL Database funkcje zabezpieczeń](infrastructure-sql.md)
- [Operacje produkcyjne platformy Azure i zarządzanie nimi](infrastructure-operations.md)
- [Monitorowanie infrastruktury platformy Azure](infrastructure-monitoring.md)
- [Integralność infrastruktury platformy Azure](infrastructure-integrity.md)
- [Ochrona danych klienta platformy Azure](protection-customer-data.md)
