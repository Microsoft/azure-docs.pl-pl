---
title: Informacje o monitorowaniu sieci w dziennikach Azure Monitor | Microsoft Docs
description: Przegląd rozwiązań do monitorowania sieci, w tym NPM, do zarządzania sieciami w środowiskach w chmurze, lokalnych i hybrydowych.
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: agummadi
ms.openlocfilehash: 36e41db6e1a3cff13f31f0abf2e83e872dfd7c49
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91742336"
---
# <a name="network-monitoring-solutions"></a>Rozwiązania do monitorowania sieci 

Platforma Azure oferuje Host rozwiązań do monitorowania zasobów sieciowych. Platforma Azure oferuje rozwiązania i narzędzia służące do monitorowania łączności sieciowej, kondycji obwodów usługi ExpressRoute i analizowania ruchu sieciowego w chmurze.

## <a name="network-performance-monitor-npm"></a>Network Performance Monitor (NPM)

Network Performance Monitor (NPM) to zestaw możliwości, z których każdy jest kierowany do monitorowania kondycji sieci, łączności sieciowej z aplikacjami i zapewnia wgląd w wydajność sieci. NPM jest oparta na chmurze i udostępnia rozwiązanie do monitorowania sieci hybrydowej, które monitoruje łączność między:
 
* Wdrożenia w chmurze i lokalizacje lokalne
* Wiele centrów danych i oddziałów
* Najważniejsze aplikacje wielowarstwowe/mikrousługi
* Lokalizacje użytkowników i aplikacje oparte na sieci Web (HTTP/HTTPs) 

Monitor wydajności, monitor ExpressRoute i monitor łączności usług są funkcjami monitorowania w programie NPM i opisanymi poniżej.

## <a name="performance-monitor"></a>Monitorowanie wydajności

Monitor wydajności jest częścią NPM i jest monitorowaniem sieci dla środowisk chmurowych, hybrydowych i lokalnych. Możesz monitorować łączność sieciową w oddziałach zdalnych i biurach terenowych, lokalizacjach magazynu, centrach danych i chmurach. Możesz wykrywać problemy z siecią przed zazażaleniem użytkowników. Najważniejsze zalety:

* Monitorowanie strat i opóźnień w różnych podsieciach oraz Ustawianie alertów
* Monitoruj wszystkie ścieżki (w tym nadmiarowe ścieżki) w sieci
* Rozwiązywanie problemów z siecią przejściową i punktem w czasie, które trudno się replikować
* Określanie określonego segmentu w sieci, który jest odpowiedzialny za obniżoną wydajność
* Monitorowanie kondycji sieci bez konieczności stosowania protokołu SNMP

![Mapa topologii NPM](./media/network-monitoring-overview/npm-topology-map.png) 

Aby uzyskać więcej informacji, zobacz następujące artykuły:

* [Skonfiguruj rozwiązanie Network Performance Monitor w dziennikach Azure Monitor](../azure-monitor/insights/network-performance-monitor.md) 
* [Przypadki użycia](https://blogs.technet.microsoft.com/msoms/2016/08/30/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor/)
* Aktualizacje produktu:
  * [Luty 2017](https://blogs.technet.microsoft.com/msoms/2017/02/27/oms-network-performance-monitor-is-now-generally-available/)
  * [Sierpień 2017](https://blogs.technet.microsoft.com/msoms/2017/08/14/improvements-to-oms-network-performance-monitor/)

## <a name="expressroute-monitor"></a>Monitor ExpressRoute

NPM for ExpressRoute oferuje kompleksowe monitorowanie ExpressRoute dla prywatnej komunikacji równorzędnej Azure i połączeń komunikacji równorzędnej firmy Microsoft. Możesz monitorować łączność E2E i wydajność między oddziałami i platformą Azure przez ExpressRoute. Kluczowe możliwości:

* Autowykrywanie obwodów usługi ER skojarzonych z Twoją subskrypcją
* Wykrywanie topologii sieci z poziomu lokalnego w aplikacjach w chmurze
* Planowanie pojemności, analiza wykorzystania przepustowości
* Monitorowanie i generowanie alertów dla ścieżek podstawowych i pomocniczych
* Monitorowanie łączności z usługami platformy Azure, takimi jak Microsoft 365, Dynamics 365,... ponad ExpressRoute
* Wykryj spadek wydajności łączności do sieci wirtualnych

![Mapa geograficzna pokazująca ruch między regionami](./media/network-monitoring-overview/expressroute-topology-map.png) 

Aby uzyskać więcej informacji, zobacz następujące artykuły:

* [Konfigurowanie monitora wydajności sieci dla usługi ExpressRoute](../expressroute/how-to-npm.md)
* [Wpis w blogu](https://aka.ms/NPMExRmonitorGA)

## <a name="service-connectivity-monitor"></a>Monitorowanie łączności usług

Dzięki monitorowaniu łączności usług możesz teraz testować dostęp do aplikacji i wykrywać wąskie gardła wydajności w lokalnych, sieciach przewoźnikowych i w chmurze/prywatnych centrach danych.

* Monitorowanie kompleksowej łączności sieciowej z aplikacjami
* Skorelowanie dostarczania aplikacji z wydajnością sieci, wykrywanie precyzyjnej lokalizacji degradacji wzdłuż ścieżki między użytkownikiem a aplikacją
* Testowanie osiągalności aplikacji z wielu lokalizacji użytkowników na całym świecie
* Określanie opóźnienia sieci i utraty pakietów dla aplikacji biznesowych i SaaSych
* Określanie aktywnych miejsc w sieci, które mogą powodować niską wydajność aplikacji
* Monitoruj możliwości Microsoft 365 aplikacji przy użyciu wbudowanych testów dla Microsoft 365, Dynamics 365, Skype dla firm i innych usług firmy Microsoft

Aby uzyskać więcej informacji, zobacz następujące artykuły:

* [Konfigurowanie Network Performance Monitor dla punktów końcowych usługi monitorowania](../azure-monitor/insights/network-performance-monitor-service-connectivity.md#configuration)
* [Wpis w blogu](https://aka.ms/svcendptmonitor)

## <a name="traffic-analytics"></a>Analiza ruchu
Analiza ruchu to rozwiązanie oparte na chmurze, które zapewnia wgląd w aktywność użytkowników i aplikacji w sieciach w chmurze. Dzienniki przepływu sieciowej grupy zabezpieczeń są analizowane w celu zapewnienia wglądu w:

* Przepływy ruchu między sieciami między platformą Azure i Internetem, regionami chmury publicznej, sieci wirtualnych i podsieciami
* Aplikacje i protokoły w sieci bez potrzeby wykrywania lub dedykowanych urządzeń modułu zbierającego przepływy
* Najpopularniejsze rozmowy, aplikacje rozmawiające, konwersacje maszyn wirtualnych w chmurze, punkty połączenia z ruchem
* Źródła i miejsca docelowe ruchu między sieci wirtualnych, relacje między krytycznymi usługami i aplikacjami biznesowymi
* Zabezpieczenia — złośliwy ruch, porty otwarte dla Internetu, aplikacje lub maszyny wirtualne próbujące uzyskać dostęp do Internetu...
* Wykorzystanie pojemności — pomaga wyeliminować problemy dotyczące nadmiernej aprowizacji lub niedostatecznego wykorzystania przez monitorowanie trendów użycia bram sieci VPN i innych usług

Analiza ruchu zapewnia informacje umożliwiające podjęcie odpowiednich działań, które ułatwiają inspekcję aktywności sieciowej organizacji, zabezpieczanie aplikacji i danych, Optymalizowanie wydajności obciążeń i zachowanie zgodności.

![Mapa geograficzna pokazująca ruch w różnych regionach 2](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

Powiązane linki:
* [Wpis w blogu](https://aka.ms/trafficanalytics), [Dokumentacja](https://aka.ms/trafficanalyticsdocs), [często zadawane pytania](https://docs.microsoft.com/azure/network-watcher/traffic-analytics-faq)

## <a name="dns-analytics"></a>Analiza DNS
To rozwiązanie skompilowane dla administratorów DNS umożliwia zbieranie, analizowanie i skorelowanie dzienników DNS w celu zapewnienia bezpieczeństwa, operacji i szczegółowych informacji dotyczących wydajności.  Dostępne są następujące możliwości:

* Identyfikacja klientów, którzy spróbują rozwiązać się do złośliwych domen
* Identyfikacja starych rekordów zasobów
* Wgląd w często badane nazwy domen i próg klientów DNS
* Widoczność żądań dotyczących obciążenia na serwerach DNS
* Monitorowanie dynamicznych błędów rejestracji w usłudze DNS

![Pulpit nawigacyjny DNS Analytics](./media/network-monitoring-overview/dns-analytics-overview.png) 

Powiązane linki:
* [wpis w blogu](https://blogs.technet.microsoft.com/msoms/2017/04/19/introducing-oms-dns-analytics/), [dokumentacja](https://docs.microsoft.com/azure/log-analytics/log-analytics-dns)

## <a name="miscellaneous"></a>Różne

* [Nowe ceny](https://docs.microsoft.com/azure/log-analytics/log-analytics-network-performance-monitor-pricing-faq)
