---
title: Zasady komunikacji równorzędnej firmy Microsoft
titleSuffix: Azure
description: Zasady komunikacji równorzędnej firmy Microsoft
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 12/15/2020
ms.author: prmitiki
ms.openlocfilehash: bee41bb8e5beb4df3086ab50499cb185a83e4efe
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97592334"
---
# <a name="peering-policy"></a>Zasady komunikacji równorzędnej
Firma Microsoft utrzymuje wybiórcze zasady komunikacji równorzędnej, zaprojektowane w celu zapewnienia najlepszej możliwości obsługi klienta w ramach standardów branżowych i najlepszych rozwiązań, skalowanie na potrzeby przyszłych potrzeb i strategiczne rozmieszczenie komunikacji równorzędnej. W związku z tym firma Microsoft zastrzega sobie prawo do podejmowania wyjątków dla zasad zgodnie z potrzebami. Ogólne wymagania firmy Microsoft z sieci zostały omówione w poniższych sekcjach. Są one stosowane do bezpośrednich żądań komunikacji równorzędnej i komunikacji równorzędnej programu Exchange. 

## <a name="technical-requirements"></a>Wymagania techniczne

* W pełni nadmiarowa sieć o wystarczającej pojemności do wymiany ruchu bez przeciążenia.
* Element równorzędny będzie miał publicznie rutowany numer systemu autonomicznego (ASN).
* Obsługiwane są zarówno adresy IPv4, jak i IPv6, a firma Microsoft oczekuje na ustanawianie sesji obu typów w każdej lokalizacji komunikacji równorzędnej.
* MD5 nie jest obsługiwany.
* **Szczegóły ASN:**

    * Firma Microsoft zarządza AS8075 wraz z poniższymi WPW: AS8068, AS8069, AS12076. Aby uzyskać pełną listę numerów ASN z AS8075 komunikacji równorzędnej, należy zapoznać się z ZESTAWem MICROSOFT.
    * W żadnym przypadku Komunikacja równorzędna z firmą Microsoft nie akceptuje tras z usługi AS12076 (Express Route) i powinna odfiltrować AS12076 we wszystkich elementach równorzędnych.

* **Zasady routingu:**
    * Element równorzędny będzie miał co najmniej jedną publiczną Routing/24.
    * Firma Microsoft zastąpi otrzymane Rozróżniaczy wielowyjścia (MED).
    * Firma Microsoft preferuje otrzymywanie tagów społeczności BGP z elementów równorzędnych w celu wskazania pochodzenia trasy.
    * Zalecamy, aby elementy równorzędne ustawili maksymalnie prefiksy tras 2000 (IPv4) i 500 (IPv6) w ramach sesji komunikacji równorzędnej z firmą Microsoft.
    * O ile nie zostanie to wcześniej uzgodnione, elementy równorzędne powinny ogłaszać spójne trasy we wszystkich lokalizacjach, w których są one równorzędne z firmą Microsoft.
    * Ogólnie rzecz biorąc, sesje komunikacji równorzędnej z usługą AS8075 będą anonsować wszystkie trasy zgodne z firmą MICROSOFT. Firma Microsoft może zaanonsować niektóre specyficzne dla regionu.
    * Żadna ze stron nie ustanowi trasy statycznej, trasy ostatniego użycia ani przesyłania ruchu do drugiej strony dla trasy nieogłoszonej za pośrednictwem protokołu BGP.
    * Elementy równorzędne są wymagane do zarejestrowania ich tras w bazie danych usługi Public Routing Internet Registry (IRR) na potrzeby filtrowania i zapewnienia aktualności tych informacji.      
    * Elementy równorzędne będą zgodne ze standardami branżowymi MANRS w zakresie zabezpieczeń tras.  Według własnego uznania firma Microsoft może wybrać: 1.), aby nie ustanowić komunikacji równorzędnej z firmami, które nie mają podpisanych i zarejestrowanych tras. 2.) w celu usunięcia nieprawidłowych tras RPKI; 3.) nie akceptują tras od ustanowionych elementów równorzędnych, które nie są zarejestrowane i podpisane. 

## <a name="operational-requirements"></a>Wymagania operacyjne
* W pełni obsługiwane 24x7 sieciowe centrum operacji (NOC), które może pomóc w rozwiązaniu wszystkich problemów technicznych i wydajności, naruszeniach zabezpieczeń, atakach typu "odmowa usługi" lub wszelkich innych nadużyciach pochodzących z elementu równorzędnego lub klientów.
* Komputery równorzędne powinny mieć pełny i aktualny profil w witrynie [PeeringDB](https://www.peeringdb.com) , w tym 24x7 noc e-mail z firmowej domeny i numeru telefonu. Te informacje służą do weryfikowania szczegółów elementu równorzędnego, takich jak informacje NOC, informacje kontaktowe techniczne i ich obecność w urządzeniach komunikacji równorzędnej itp. Osobiste konta usługi Yahoo, Gmail i Hotmail nie są akceptowane.

## <a name="physical-connection-requirements"></a>Wymagania dotyczące połączenia fizycznego
* Lokalizacje, w których można nawiązać połączenie z firmą Microsoft w celu bezpośredniej komunikacji równorzędnej lub komunikacji równorzędnej programu Exchange, są wymienione w [PeeringDB](https://www.peeringdb.com/net/694)

* **Komunikacja równorzędna programu Exchange:**
    * Komputery równorzędne powinny mieć co najmniej 10 GB połączenia z programem Exchange.
    * Jeśli użycie szczytowe przekracza 50%, należy uaktualnić porty równorzędne.
    * Firma Microsoft zachęca do komunikacji równorzędnej w celu utrzymania różnorodnych połączeń z programem Exchange w celu obsługi scenariuszy trybu failover.

* **Bezpośrednia Komunikacja równorzędna:**
    * Połączenie musi znajdować się w przedziale Fiber-Trybowym przy użyciu światłowodów 100 GB/s.
    * Firma Microsoft ustanowi bezpośrednią komunikację równorzędną z usługodawcami internetowymi lub dostawcami usług sieciowych.
    * Komputery równorzędne powinny uaktualnić porty, gdy szczytowe wykorzystanie przekracza 50% i utrzymuje różne pojemności w każdej linii metra, w jednej lokalizacji lub w kilku lokalizacjach w linii metra.
    * Każda bezpośrednia Komunikacja równorzędna składa się z dwóch połączeń z dwoma routerami programu Microsoft Edge z routerów równorzędnych znajdujących się w sieci równorzędnej. Firma Microsoft wymaga dwóch sesji BGP w ramach tych połączeń. Element równorzędny może zrezygnować z wdrażania nadmiarowych urządzeń.


## <a name="traffic-requirements"></a>Wymagania dotyczące ruchu

* Elementy równorzędne za pośrednictwem komunikacji równorzędnej programu Exchange muszą mieć co najmniej 500 MB ruchu i mniej niż 2 GB. W przypadku ruchu przekraczającego 2 GB bezpośrednie Komunikacja równorzędna powinna zostać ustanowiona.
* Firma Microsoft wymaga co najmniej 2 GB do bezpośredniej komunikacji równorzędnej. Każda wzajemnie uzgodniona lokalizacja komunikacji równorzędnej musi obsługiwać tryb failover, który zapewnia, że Komunikacja równorzędna pozostanie zlokalizowana w scenariuszu pracy awaryjnej. 

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o krokach konfigurowania bezpośredniej komunikacji równorzędnej z firmą Microsoft, postępuj zgodnie ze wskazówkami dotyczącymi [komunikacji równorzędnej](walkthrough-direct-all.md)
* Aby poznać kroki konfigurowania komunikacji równorzędnej programu Exchange z firmą Microsoft, postępuj zgodnie ze wskazówkami dotyczącymi [komunikacji równorzędnej programu Exchange](walkthrough-exchange-all.md).
