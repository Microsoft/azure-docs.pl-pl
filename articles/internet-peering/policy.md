---
title: Zasady komunikacji równorzędnej firmy Microsoft
titleSuffix: Azure
description: Zasady komunikacji równorzędnej firmy Microsoft
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a683ad71f5e80c91728262dc7bbabf36e9d68deb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75775227"
---
# <a name="peering-policy"></a>Zasady komunikacji równorzędnej
Ogólne wymagania firmy Microsoft z sieci zostały omówione w poniższych sekcjach. Są one stosowane do bezpośrednich żądań komunikacji równorzędnej i komunikacji równorzędnej programu Exchange.

## <a name="technical-requirements"></a>Wymagania techniczne

* W pełni nadmiarowa sieć o wystarczającej pojemności do wymiany ruchu bez przeciążenia.
* Element równorzędny będzie miał publicznie rutowany numer systemu autonomicznego (ASN).
* Obsługiwane są zarówno adresy IPv4, jak i IPv6, a firma Microsoft oczekuje na ustanawianie sesji obu typów w każdej lokalizacji komunikacji równorzędnej.
* MD5 nie jest obsługiwany.
* **Szczegóły ASN:**
    * Firma Microsoft zarządza AS8075 wraz z poniższymi WPW: AS8068, AS8069, AS12076. Aby uzyskać pełną listę numerów ASN z AS8075 komunikacji równorzędnej, należy zapoznać się z ZESTAWem MICROSOFT.
    * Wszyscy użytkownicy z firmą Microsoft nie akceptują tras z usługi AS12076 (Express Route) w żadnym wypadku i powinni odfiltrować AS12076 dla wszystkich elementów równorzędnych.
* **Zasady routingu:**
    * Element równorzędny będzie miał co najmniej jedną publiczną Routing/24.
    * Firma Microsoft zastąpi otrzymane Rozróżniaczy wielowyjścia (MED).
    * Firma Microsoft preferuje otrzymywanie tagów społeczności BGP z elementów równorzędnych w celu wskazania pochodzenia trasy.
    * Usługa Peer oczekuje, że trasy są rejestrowane w bazie danych publicznego rejestru routingu internetowego (IRR), na potrzeby filtrowania i będą podejmować wysiłki w dobrej wierze, aby zapewnić aktualność tych informacji.
    * Zalecamy, aby elementy równorzędne ustawili maksymalnie prefiksy tras 1000 (IPv4) i 100 (IPv6) w ramach sesji komunikacji równorzędnej z firmą Microsoft.
    * O ile nie zostanie to wcześniej uzgodnione, elementy równorzędne powinny ogłaszać spójne trasy we wszystkich lokalizacjach, w których są one równorzędne z firmą Microsoft.
    * Ogólnie rzecz biorąc, sesje komunikacji równorzędnej z usługą AS8075 będą anonsować wszystkie trasy zgodne z firmą MICROSOFT. Połączenia międzyAS8075owe w Afryce i Azji mogą być ograniczone do tras odpowiednich dla danego regionu.
    * Żadna ze stron nie ustanowi trasy statycznej, trasy ostatniego użycia ani przesyłania ruchu do drugiej strony dla trasy nieogłoszonej za pośrednictwem protokołu BGP.
    * Elementy równorzędne powinny być zgodne ze standardami branżowymi [MANRS](https://www.manrs.org/) w zakresie zabezpieczeń tras.

## <a name="operational-requirements"></a>Wymagania operacyjne
* W pełni obsługiwane 24x7 sieciowe centrum operacji (NOC), które może pomóc w rozwiązaniu wszystkich problemów technicznych i wydajności, naruszeniach zabezpieczeń, atakach typu "odmowa usługi" lub wszelkich innych nadużyciach pochodzących z elementu równorzędnego lub klientów.
* Komputery równorzędne powinny mieć pełny i aktualny profil w witrynie [PeeringDB](https://www.peeringdb.com) , w tym 24x7 noc e-mail z firmowej domeny i numeru telefonu. Te informacje służą do weryfikowania szczegółów elementu równorzędnego, takich jak informacje NOC, informacje kontaktowe techniczne i ich obecność w urządzeniach komunikacji równorzędnej itp. Osobiste konta usługi Yahoo, Gmail i Hotmail nie są akceptowane.

## <a name="physical-connection-requirements"></a>Wymagania dotyczące połączenia fizycznego
* Lokalizacje, w których można nawiązać połączenie z firmą Microsoft w celu bezpośredniej komunikacji równorzędnej lub komunikacji równorzędnej programu Exchange, są wymienione w [PeeringDB](https://www.peeringdb.com/net/694)
* **Komunikacja równorzędna programu Exchange:**
    * Połączenia muszą znajdować się w przedziale między włóknami jednoprocesorowymi przy użyciu odpowiednich optyk 10Gbps.
    * Jeśli użycie szczytowe przekracza 50%, należy uaktualnić porty równorzędne.
* **Bezpośrednia Komunikacja równorzędna:**
    * Połączenie musi znajdować się w trybie jednoprocesorowym przy użyciu odpowiednich światłowodów 10Gbps lub 100Gbps.
    * Firma Microsoft ustanowi bezpośrednią komunikację równorzędną z usługodawcami internetowymi lub dostawcami usług sieciowych.
    * Komputery równorzędne powinny uaktualnić porty, gdy szczytowe wykorzystanie przekracza 50% i utrzymuje różne pojemności w każdej linii metra, w jednej lokalizacji lub w kilku lokalizacjach w linii metra.
    * Każda bezpośrednia Komunikacja równorzędna składa się z dwóch połączeń z dwoma routerami programu Microsoft Edge z routerów równorzędnych znajdujących się w sieci równorzędnej. Firma Microsoft wymaga dwóch sesji BGP w ramach tych połączeń. Element równorzędny może zrezygnować z wdrażania nadmiarowych urządzeń.

## <a name="traffic-requirements"></a>Wymagania dotyczące ruchu
* Elementy równorzędne za pośrednictwem komunikacji równorzędnej programu Exchange muszą mieć co najmniej 200 MB ruchu i mniej niż 2 GB.  W przypadku ruchu przekraczającego 2 GB bezpośrednie Komunikacja równorzędna powinna zostać sprawdzona.
* W przypadku bezpośredniej komunikacji równorzędnej ruch z sieci do firmy Microsoft musi spełniać poniższe wymagania minimalne.

    | Obszar geograficzny                      | Minimalny ruch do firmy Microsoft   |
    | :----------------------- |:-------------------------------|
    | Afryka                   | 500 Mb/s                       |
    | Azja I Pacyfik (z wyjątkiem Indii)      |   2 Gb/s                       |
    | Azja I Pacyfik (tylko Indie)        | 500 Mb/s                       |
    | Europa                   |   2 Gb/s                       |
    | LATAM                    |   2 Gb/s                       |
    | Bliski Wschód              | 500 Mb/s                       |
    | Nie dotyczy                       |   2 Gb/s                       |

* **Różnorodności**
    * W przypadku, Europy, Azja I Pacyfik i LATAM, połączenie w co najmniej trzech geograficznie różnych lokalizacjach, jeśli jest to możliwe, i utrzymuje różne pojemności, aby umożliwić ruch do trybu failover w każdej linii metra.
    * W Afryce, Bliski Wschód i Indie, połączenie w możliwie największej lokalizacji. Musi zachować wystarczającą liczbę różnorodnych możliwości, aby zapewnić, że ruch pozostaje w regionie.

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o krokach konfigurowania bezpośredniej komunikacji równorzędnej z firmą Microsoft, postępuj zgodnie ze wskazówkami dotyczącymi [komunikacji równorzędnej](walkthrough-direct-all.md)
* Aby poznać kroki konfigurowania komunikacji równorzędnej programu Exchange z firmą Microsoft, postępuj zgodnie ze wskazówkami dotyczącymi [komunikacji równorzędnej programu Exchange](walkthrough-exchange-all.md).