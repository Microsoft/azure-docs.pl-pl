---
title: 'Praca zdalna: zagadnienia dotyczące sieciowego urządzenia wirtualnego (urządzenie WUS) dotyczące zdalnego działania | VPN Gateway platformy Azure'
description: Ten artykuł ułatwia zapoznanie się z zagadnieniami, które należy wziąć pod uwagę podczas pracy z wirtualnymi urządzeniami sieciowymi (urządzeń WUS) na platformie Azure w trakcie COVID-19 Pandemic.
services: vpn-gateway
author: scottnap
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: scottnap
ms.openlocfilehash: 4783016e472907392f2d379efa0fed2d90ed21bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89595363"
---
# <a name="working-remotely-network-virtual-appliance-nva-considerations-for-remote-work"></a>Praca zdalna: zagadnienia dotyczące sieciowego urządzenia wirtualnego (urządzenie WUS) na potrzeby pracy zdalnej

>[!NOTE]
>W tym artykule opisano, jak można korzystać z sieciowych urządzeń wirtualnych, platformy Azure, sieci firmy Microsoft i ekosystemu partnerów platformy Azure do zdalnego działania i rozwiązywania problemów z siecią, które są dostępne z powodu kryzysu COVID-19.
>

Niektórzy klienci platformy Azure wykorzystują wirtualne urządzenia sieciowe innych firm (urządzeń WUS) z witryny Azure Marketplace w celu zapewnienia krytycznych usług, takich jak sieć VPN typu punkt-lokacja dla swoich pracowników, którzy pracują z domu podczas epidemii COVID-19. W tym artykule opisano niektóre wskazówki wysokiego poziomu, które należy wziąć pod uwagę podczas korzystania z urządzeń WUS na platformie Azure w celu zapewnienia rozwiązań dostępu zdalnego.

## <a name="nva-performance-considerations"></a>Zagadnienia dotyczące wydajności urządzenie WUS

Wszyscy urządzenie WUS dostawcy w witrynie Azure Marketplace powinni mieć zalecenia dotyczące rozmiaru maszyny wirtualnej i liczby wystąpień używanych podczas wdrażania rozwiązań.  Niemal wszyscy dostawcy urządzenie WUS umożliwiają wybranie dowolnego rozmiaru, który jest dostępny dla Ciebie w danym regionie, dlatego bardzo ważne jest, aby postępować zgodnie z zaleceniami dostawców dla rozmiarów wystąpień maszyn wirtualnych platformy Azure, ponieważ te zalecenia to rozmiary maszyn wirtualnych wykonywane przez dostawcę w systemie Azure.  

### <a name="consider-the-following"></a>Weź pod uwagę następujące kwestie

- **Pojemność i liczba współbieżnych użytkowników** — ta liczba jest szczególnie ważna w przypadku użytkowników sieci VPN typu punkt-lokacja, ponieważ każdy połączony użytkownik utworzy jeden tunel szyfrowany (IPSec lub SSL).  
- **Zagregowana przepływność** — co to jest przepustowość zagregowana, która będzie potrzebna do uwzględnienia liczby użytkowników, których potrzebujesz do zapewnienia dostępu zdalnego.
- **Wymagany rozmiar maszyny wirtualnej** — zawsze należy używać rozmiarów maszyn wirtualnych zalecanych przez dostawcę urządzenie WUS.  W przypadku połączeń sieci VPN typu punkt-lokacja, jeśli będziesz mieć dużą liczbę współbieżnych połączenia użytkownika, należy używać większych rozmiarów maszyn wirtualnych, takich jak maszyny wirtualne [z serii Dv2 i DSv2](https://docs.microsoft.com/azure/virtual-machines/dv2-dsv2-series "Seria Dv2 i Dsv2") . Te maszyny wirtualne mają więcej procesorów wirtualnych vCPU i mogą obsługiwać więcej współbieżnych sesji sieci VPN.  Oprócz większej liczby rdzeni wirtualnych, większe rozmiary maszyn wirtualnych na platformie Azure mają bardziej zagregowaną pojemność przepustowości niż mniejsze rozmiary maszyn wirtualnych.
    > **Ważne:** Każdy dostawca używa zasobów inaczej.  Jeśli nie wyczyścisz rozmiarów wystąpień, których należy użyć, aby obsłużyć szacowane obciążenie użytkownikami, skontaktuj się bezpośrednio z dostawcą oprogramowania i poproś o rekomendację.
- **Liczba wystąpień** — jeśli oczekujesz dużej liczby użytkowników i połączeń, istnieją ograniczenia skalowania rozmiaru wystąpienia urządzenie WUS.  Rozważ wdrożenie wielu wystąpień maszyn wirtualnych.
- **IPsec sieci** VPN i sieci VPN SSL — ogólne implementacje protokołu IPsec sieci VPN wykonują lepsze niż IMPLEMENTACJE protokołu SSL sieci VPN.  
- **Licencjonowanie** — upewnij się, że licencje na oprogramowanie zakupione w ramach rozwiązania urządzenie WUS będą obejmować nagły wzrost, który można napotkać podczas epidemii COVID-19.  Wiele programów licencjonowania urządzenie WUS ogranicza liczbę połączeń lub przepustowość, do których rozwiązanie jest możliwe.
- **Przyspieszona sieć** — Rozważ rozwiązanie urządzenie WUS, które obsługuje przyspieszone sieci.  Przyspieszona sieć umożliwia wirtualizację we/wy z jednym elementem głównym (SR-IOV) do maszyny wirtualnej, co znacznie zwiększa wydajność sieci. Ta ścieżka o wysokiej wydajności pomija hosta ze ścieżki danych, skracając czas opóźnienia, wahania i użycie procesora CPU w przypadku najbardziej wymagających obciążeń sieciowych na obsługiwanych typach maszyn wirtualnych. Przyspieszona sieć jest obsługiwana w większości ogólnego przeznaczenia i o rozmiarach wystąpień zoptymalizowanych pod kątem obliczeń przy użyciu co najmniej dwóch procesorów wirtualnych vCPU.

## <a name="monitoring-resources"></a>Monitorowanie zasobów

Każde rozwiązanie urządzenie WUS ma własne narzędzia i zasoby do monitorowania wydajności ich urządzenie WUS.  Zapoznaj się z dokumentacją dostawców, aby upewnić się, że rozumiesz ograniczenia wydajności i wykryjesz, gdy urządzenie WUS zbliża się lub osiągnie pojemność.  Oprócz tego można przyjrzeć się Azure Monitor usługi Network Insights i zobaczyć podstawowe informacje o wydajności urządzeń wirtualnych sieci, takich jak:

- Wykorzystanie procesora
- Sieć — wejście
- Sieć — wyjście
- Przepływy przychodzące
- Przepływy wychodzące

## <a name="next-steps"></a>Następne kroki

Większość głównych partnerów urządzenie WUS opublikowała wskazówki dotyczące skalowania pod kątem nagłego i nieoczekiwanego wzrostu w trakcie COVID-19. Poniżej przedstawiono kilka przydatnych linków do zasobów partnerskich.

[Barracuda umożliwia korzystanie z domu podczas zabezpieczania danych podczas COVID-19](https://www.barracuda.com/covid-19/work-from-home "Włącz pracy z domu podczas zabezpieczania danych w trakcie COVID-19")

[Zapoznaj się z bezpiecznym pracownikiem zdalnym podczas coronavirus](https://www.checkpoint.com/solutions/secure-remote-workforce-during-coronavirus/ "Zabezpiecz pracowników zdalnych podczas coronavirus")

[Implementacje programu Cisco AnyConnect i wydajność/skalowanie dla przygotowania COVID-19](https://www.cisco.com/c/en/us/support/docs/security/anyconnect-secure-mobility-client/215331-anyconnect-implementation-and-performanc.html "Implementacje programu Cisco AnyConnect i wydajność/skalowanie dla przygotowania COVID-19")

[Centrum pomocy technicznej Citrix COVID-19](https://www.citrix.com/support/covid-19-coronavirus.html "Centrum pomocy technicznej Citrix COVID-19")

[Wskazówki F5 umożliwiające znaczne zwiększenie liczby pracowników zdalnych](https://www.f5.com/business-continuity "Wskazówki F5 umożliwiające znaczne zwiększenie liczby pracowników zdalnych")

[Fortinet COVID-19 aktualizacje dla klientów i partnerów](https://www.fortinet.com/covid-19.html "Aktualizacje COVID-19 dla klientów i partnerów")

[Palo Alto Networks COVID-19 Response Center](https://live.paloaltonetworks.com/t5/COVID-19-Response-Center/ct-p/COVID-19_Response_Center "Palo Alto Networks COVID-19 Response Center")
