---
title: Omówienie odwrotnego systemu DNS na platformie Azure — Azure DNS
description: W tej ścieżce szkoleniowej Rozpocznij uczenie się, jak działa odwrotny system DNS i jak można go używać na platformie Azure
services: dns
documentationcenter: na
author: rohinkoul
manager: KumuD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/29/2017
ms.author: rohink
ms.openlocfilehash: 8af9549efc3e8dab54f55dd404346d87201dee2c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94965616"
---
# <a name="overview-of-reverse-dns-and-support-in-azure"></a>Omówienie odwrotnego systemu DNS i pomocy technicznej na platformie Azure

Ten artykuł zawiera omówienie sposobu działania odwrotnego systemu DNS oraz scenariusze odwrotnej usługi DNS obsługiwane na platformie Azure.

## <a name="what-is-reverse-dns"></a>Co to jest odwrotny system DNS?

Konwencjonalne rekordy DNS umożliwiają mapowanie nazw DNS (takich jak "www.contoso.com") na adres IP (na przykład 64.4.6.100).  Odwrotny system DNS umożliwia tłumaczenie adresu IP (64.4.6.100) z powrotem na nazwę ("www.contoso.com").

Rekordy odwrotnego systemu DNS są używane w różnych sytuacjach. Na przykład rekordy odwrotnego systemu DNS są szeroko używane do walki ze spamem wiadomości e-mail przez zweryfikowanie nadawcy wiadomości e-mail.  Serwer poczty odebranej pobiera rekord odwrotnego systemu DNS adresu IP serwera wysyłającego i weryfikuje, czy ten host jest autoryzowany do wysyłania wiadomości e-mail z domeny źródłowej. 

## <a name="how-reverse-dns-works"></a>Jak działa odwrotny system DNS

Rekordy odwrotnego systemu DNS są hostowane w specjalnych strefach DNS, zwanych strefami "ARPA".  Te strefy tworzą oddzielną Hierarchię DNS równolegle z normalnymi domenami hostingu w hierarchii, takimi jak "contoso.com".

Na przykład rekord DNS "www.contoso.com" jest implementowany przy użyciu rekordu "A" DNS o nazwie "www" w strefie "contoso.com".  Ten rekord wskazuje na odpowiedni adres IP, w tym przypadku 64.4.6.100.  Wyszukiwanie wsteczne jest implementowane oddzielnie, przy użyciu rekordu "PTR" o nazwie "100" w strefie "6.4.64.in-addr. arpa" (należy zauważyć, że adresy IP są odwrócone w strefach programu ARPA).  Ten rekord PTR (jeśli został prawidłowo skonfigurowany) wskazuje na nazwę "www.contoso.com".

Gdy organizacja ma przypisany blok adresów IP, uzyska również prawo do zarządzania odpowiednią strefą programu ARPA. Strefy ARPA odpowiadające blokom adresów IP używanym przez platformę Azure są hostowane i zarządzane przez firmę Microsoft. Usługodawca internetowy może hostować strefę programu ARPA pod kątem własnych adresów IP lub może umożliwić hostowanie strefy ARPA w wybranym przez siebie usłudze DNS, takiej jak Azure DNS.

> [!NOTE]
> Wyszukiwania DNS i odwrotne wyszukiwania DNS są implementowane w oddzielnych, równoległych hierarchiach DNS. Wyszukiwanie wsteczne dla elementu "www.contoso.com" **nie** jest hostowane w strefie "contoso.com", a nie jest hostowane w strefie arpa dla odpowiedniego bloku adresów IP. Dla bloków adresów IPv4 i IPv6 są używane oddzielne strefy.

### <a name="ipv4"></a>Protokół IPv4

Nazwa strefy wyszukiwania wstecznego IPv4 powinna mieć następujący format: `<IPv4 network prefix in reverse order>.in-addr.arpa` .

Na przykład podczas tworzenia strefy odwrotnej w celu hostowania rekordów hostów z adresami IP, które znajdują się w prefiksie 192.0.2.0/24, nazwa strefy zostanie utworzona przez wyodrębnienie prefiksu sieci (192.0.2), a następnie odwrócenie kolejności (2.0.192) i dodanie sufiksu `.in-addr.arpa` .

|Klasa podsieci|Prefiks sieci  |Odwrócony prefiks sieci  |Sufiks standardowy  |Odwróć nazwę strefy |
|-------|----------------|------------|-----------------|---------------------------|
|Klasa A|203.0.0.0/8     | 203        | . in-addr. arpa   | `203.in-addr.arpa`        |
|Klasa B|198.51.0.0/16   | 51,198     | . in-addr. arpa   | `51.198.in-addr.arpa`     |
|Klasa C|192.0.2.0/24    | 2.0.192    | . in-addr. arpa   | `2.0.192.in-addr.arpa`    |

### <a name="classless-ipv4-delegation"></a>Delegowanie bezklasowe IPv4

W niektórych przypadkach zakres adresów IP przypisywany do organizacji jest mniejszy niż zakres klasy C (/24). W takim przypadku zakres adresów IP nie należy do granicy strefy w `.in-addr.arpa` hierarchii strefy, dlatego nie może być delegowany jako strefa podrzędna.

Zamiast tego inny mechanizm służy do transferu kontroli pojedynczych rekordów wyszukiwania wstecznego (PTR) do dedykowanej strefy DNS. Ten mechanizm deleguje strefę podrzędną dla każdego zakresu adresów IP, a następnie mapuje każdy adres IP z zakresu indywidualnie do tej strefy podrzędnej przy użyciu rekordów CNAME.

Załóżmy na przykład, że organizacja otrzymuje zakres adresów IP 192.0.2.128/26 przez jego usługodawcę internetowego. Reprezentuje to 64 adresów IP, od 192.0.2.128 do 192.0.2.191. Odwrotny serwer DNS dla tego zakresu jest implementowany w następujący sposób:
- Organizacja tworzy strefę wyszukiwania wstecznego o nazwie 128-26.2.0.192.in-addr. arpa. Prefiks "128-26" reprezentuje segment sieci przypisany do organizacji w zakresie klasy C (/24).
- Usługodawca internetowy tworzy rekordy NS w celu skonfigurowania delegowania DNS dla powyższej strefy z strefy nadrzędnej klasy C. Tworzy również rekordy CNAME w strefy wyszukiwania wstecznego nadrzędnego (Class C), mapując każdy adres IP w zakresie adresów IP na nową strefę utworzoną przez organizację:

```
$ORIGIN 2.0.192.in-addr.arpa
; Delegate child zone
128-26    NS       <name server 1 for 128-26.2.0.192.in-addr.arpa>
128-26    NS       <name server 2 for 128-26.2.0.192.in-addr.arpa>
; CNAME records for each IP address
129       CNAME    129.128-26.2.0.192.in-addr.arpa
130       CNAME    130.128-26.2.0.192.in-addr.arpa
131       CNAME    131.128-26.2.0.192.in-addr.arpa
; etc
```
- Organizacja następnie zarządza poszczególnymi rekordami PTR w ramach ich strefy podrzędnej.

```
$ORIGIN 128-26.2.0.192.in-addr.arpa
; PTR records for each UIP address. Names match CNAME targets in parent zone
129      PTR    www.contoso.com
130      PTR    mail.contoso.com
131      PTR    partners.contoso.com
; etc
```
Wyszukiwanie wsteczne dla zapytań adresu IP "192.0.2.129" dla rekordu PTR o nazwie "129.2.0.192.in-addr. arpa". To zapytanie jest rozpoznawane przez rekord CNAME w strefie nadrzędnej do rekordu PTR w strefie podrzędnej.

### <a name="ipv6"></a>Protokół IPv6

Nazwa strefy wyszukiwania wstecznego IPv6 powinna mieć następującą postać: `<IPv6 network prefix in reverse order>.ip6.arpa`

Na przykład. W przypadku tworzenia strefy odwrotnej w celu hostowania rekordów o adresach IP, które znajdują się w prefiksie 2001: db8:1000: ABDC::/64, nazwa strefy zostanie utworzona przez wyodrębnienie prefiksu sieci adresu (2001: db8: ABDC::). Następnie rozwiń prefiks sieci IPv6 w celu usunięcia [kompresji zerowej](/previous-versions/windows/it-pro/windows-server-2003/cc781672(v=ws.10)), jeśli został on użyty do skrócenia prefiksu adresu IPv6 (2001:0db8: ABDC: 0000::). Odwróć kolejność, używając okresu jako ogranicznika między każdą liczbą szesnastkową w prefiksie, aby skompilować odwrócony prefiks sieci ( `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2` ) i dodać sufiks `.ip6.arpa` .


|Prefiks sieci  |Rozwinięty i odwrócony prefiks sieci |Sufiks standardowy |Odwróć nazwę strefy  |
|---------|---------|---------|---------|
|2001: db8: ABDC::/64    | 0.0.0.0. c. d. b. a. 8. b. d. 0.1.0.0.2        | . IP6. arpa        | `0.0.0.0.c.d.b.a.8.b.d.0.1.0.0.2.ip6.arpa`       |
|2001: db8:1000:9102::/64    | 2.0.1.9.0.0.0.1.8. b. d. 0.1.0.0.2        | . IP6. arpa        | `2.0.1.9.0.0.0.1.8.b.d.0.1.0.0.2.ip6.arpa`        |


## <a name="azure-support-for-reverse-dns"></a>Pomoc techniczna platformy Azure dla odwrotnego systemu DNS

Platforma Azure obsługuje dwa oddzielne scenariusze dotyczące odwrotnego systemu DNS:

**Hostowanie strefy wyszukiwania wstecznego odpowiadającego blokowi adresów IP.**
Azure DNS może służyć do [hostowania stref wyszukiwania wstecznego i zarządzania rekordami PTR dla każdego wyszukiwania odwrotnego systemu DNS](dns-reverse-dns-hosting.md)dla protokołów IPv4 i IPv6.  Proces tworzenia strefy wyszukiwania wstecznego (ARPA), konfigurowania delegowania i konfigurowania rekordów PTR jest taki sam jak w przypadku zwykłych stref DNS.  Jedyne różnice polegają na tym, że delegowanie musi być skonfigurowane za pośrednictwem usługodawcy internetowego, a nie rejestratora DNS, i należy używać tylko typu rekordu PTR.

**Skonfiguruj rekord odwrotnego systemu DNS dla adresu IP przypisanego do usługi platformy Azure.** System Azure umożliwia [skonfigurowanie wyszukiwania wstecznego dla adresów IP przypisywanych do usługi platformy Azure](dns-reverse-dns-for-azure-services.md).  To wyszukiwanie wsteczne jest konfigurowane przez platformę Azure jako rekord PTR w odpowiedniej strefie ARPA.  Te strefy ARPA, odpowiadające wszystkim zakresom adresów IP używanym przez platformę Azure, są hostowane przez firmę Microsoft

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat odwrotnej usługi DNS, zobacz [odwrotne wyszukiwanie DNS w witrynie Wikipedia](https://en.wikipedia.org/wiki/Reverse_DNS_lookup).
<br>
Dowiedz się [, jak hostować strefę wyszukiwania wstecznego dla zakresu adresów IP przypisanych przez usługodawcę internetowego w Azure DNS](dns-reverse-dns-for-azure-services.md).
<br>
Dowiedz się, jak [zarządzać rekordami odwrotnego systemu DNS dla usług platformy Azure](dns-reverse-dns-for-azure-services.md).