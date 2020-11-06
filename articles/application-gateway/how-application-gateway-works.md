---
title: Jak działa Brama aplikacji
description: Ten artykuł zawiera informacje o tym, jak Brama aplikacji akceptuje żądania przychodzące i kieruje je do zaplecza.
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: absha
ms.openlocfilehash: 9166125fac28f43a93cbee2875b91bee986b1400
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397471"
---
# <a name="how-an-application-gateway-works"></a>Jak działa Brama aplikacji

W tym artykule wyjaśniono, jak Brama aplikacji akceptuje żądania przychodzące i kieruje je do zaplecza.

![Jak Brama aplikacji akceptuje żądanie](./media/how-application-gateway-works/how-application-gateway-works.png)

## <a name="how-an-application-gateway-accepts-a-request"></a>Jak Brama aplikacji akceptuje żądanie

1. Zanim klient wyśle żądanie do bramy aplikacji, rozpoznaje nazwę domeny bramy aplikacji przy użyciu serwera systemu nazw domen (DNS). System Azure kontroluje wpis DNS, ponieważ wszystkie bramy aplikacji znajdują się w domenie azure.com.

2. Azure DNS zwraca adres IP klienta, który jest adresem IP frontonu bramy aplikacji.

3. Brama aplikacji akceptuje ruch przychodzący na jednym lub większej liczbie odbiorników. Odbiornik jest jednostką logiczną, która sprawdza, czy są używane żądania połączeń. Jest on konfigurowany przy użyciu adresu IP frontonu, protokołu i numeru portu dla połączeń od klientów do bramy aplikacji.

4. Jeśli jest używana Zapora aplikacji sieci Web (WAF), Brama aplikacji sprawdza nagłówki żądań i treść, jeśli istnieje, względem reguł WAF. Ta akcja określa, czy żądanie jest prawidłowym żądaniem czy zagrożeniem bezpieczeństwa. Jeśli żądanie jest prawidłowe, jest kierowane do zaplecza. Jeśli żądanie jest nieprawidłowe i WAF jest w trybie zapobiegania, jest ono blokowane jako zagrożenie bezpieczeństwa. Jeśli jest w trybie wykrywania, żądanie jest oceniane i rejestrowane, ale nadal przesyłane do serwera wewnętrznej bazy danych.

Usługi Azure Application Gateway można używać jako wewnętrznego modułu równoważenia obciążenia aplikacji lub jako modułu równoważenia obciążenia aplikacji dostępnego z Internetu. Brama aplikacji mających dostęp do Internetu używa publicznych adresów IP. Nazwa DNS bramy aplikacji dostępnej z Internetu jest publicznie rozpoznawalna na swój publiczny adres IP. W związku z tym internetowe bramy aplikacji mogą kierować żądania klientów do Internetu.

Wewnętrzne bramy aplikacji używają tylko prywatnych adresów IP. Jeśli używasz niestandardowej lub [prywatna strefa DNS strefy](../dns/private-dns-overview.md), nazwa domeny powinna być wewnętrznie rozpoznawalna na prywatny adres IP Application Gateway. W związku z tym wewnętrzne moduły równoważenia obciążenia mogą kierować żądania tylko od klientów mających dostęp do sieci wirtualnej dla bramy aplikacji.

## <a name="how-an-application-gateway-routes-a-request"></a>Jak Brama aplikacji kieruje żądanie

Jeśli żądanie jest prawidłowe i nie zostało zablokowane przez WAF, Brama aplikacji oceni regułę routingu żądań skojarzoną z odbiornikiem. Ta akcja określa pulę zaplecza, do której mają być kierowane żądania.

Na podstawie reguły routingu żądań Brama aplikacji określa, czy wszystkie żądania dotyczące odbiornika mają być kierowane do określonej puli zaplecza, trasy żądań do różnych pul zaplecza na podstawie ścieżki URL lub przekierować żądania do innego portu lub zewnętrznej lokacji.
>[!NOTE]
>Reguły są przetwarzane w kolejności, w jakiej są wyświetlane w portalu dla jednostki SKU w wersji 1. 

Gdy Brama aplikacji wybiera pulę zaplecza, wysyła żądanie do jednego z serwerów zaplecza w dobrej kondycji w puli (y. y. y. y). Kondycja serwera jest określana przez sondę kondycji. Jeśli pula zaplecza zawiera wiele serwerów, Brama aplikacji używa algorytmu okrężnego do kierowania żądań między serwerami w dobrej kondycji. To obciążenie równoważy żądania na serwerach.

Gdy Brama aplikacji określi serwer wewnętrznej bazy danych, zostanie otwarta nowa sesja TCP z serwerem zaplecza na podstawie ustawień HTTP. Ustawienia protokołu HTTP określają protokół, port i inne ustawienia związane z routingiem, które są wymagane do ustanowienia nowej sesji z serwerem wewnętrznej bazy danych.

Port i protokół używany w ustawieniach protokołu HTTP określają, czy ruch między usługą Application Gateway i serwerami zaplecza jest szyfrowany (w związku z tym kompletnym protokołem TLS) lub jest niezaszyfrowany.

Gdy Brama aplikacji wysyła oryginalne żądanie do serwera wewnętrznej bazy danych, zauważa każdą konfigurację niestandardową wykonaną w ustawieniach HTTP związanych z zastępowaniem nazwy hosta, ścieżki i protokołu. Ta akcja obsługuje koligację sesji opartą na plikach cookie, opróżnianie połączeń, wybór nazwy hosta z zaplecza i tak dalej.

 >[!NOTE]
>Jeśli pula zaplecza:
> - **To publiczny punkt końcowy**. Brama aplikacji używa publicznego adresu IP frontonu do uzyskiwania dostępu do serwera. Jeśli nie ma publicznego adresu IP frontonu, jeden zostanie przypisany do wychodzącej łączności zewnętrznej.
> - **Zawiera wewnętrznie rozpoznawalną nazwę FQDN lub prywatny adres IP** , Brama aplikacji kieruje żądanie do serwera wewnętrznej bazy danych za pomocą prywatnych adresów IP wystąpienia.
> - **Zawiera zewnętrzny punkt końcowy lub zewnętrznie rozpoznawalną nazwę FQDN** , Brama aplikacji kieruje żądanie do serwera wewnętrznej bazy danych przy użyciu publicznego adresu IP frontonu. Rozpoznawanie nazw DNS jest oparte na prywatnej strefie DNS lub niestandardowym serwerze DNS, jeśli jest skonfigurowany lub korzysta z domyślnej usługi DNS udostępnionej przez platformę Azure. Jeśli nie ma publicznego adresu IP frontonu, jeden zostanie przypisany do wychodzącej łączności zewnętrznej.

### <a name="modifications-to-the-request"></a>Modyfikacje żądania

Brama aplikacji wstawia cztery dodatkowe nagłówki do wszystkich żądań przed przekazaniem żądań do zaplecza. Te nagłówki to x-Forwarded-For, x-Forwarded-proto, x-Forward-port i x-Original-host. Format dla nagłówka x-Forward-for jest rozdzielaną przecinkami listą adresów IP: port.

Prawidłowe wartości x-Forwarded-Proto to HTTP lub HTTPS. X-forwardd-Port Określa port, do którego żądanie dotarło do bramy aplikacji. Nagłówek X-Original-host zawiera oryginalny nagłówek hosta, z którym nadeszło żądanie. Ten nagłówek jest przydatny w integracji z witryną sieci Web systemu Azure, w której nagłówek hosta przychodzącego jest modyfikowany przed skierowaniem ruchu do zaplecza. Jeśli koligacja sesji jest włączona jako opcja, dodaje plik cookie koligacji zarządzanej przez bramę.

Można skonfigurować bramę aplikacji w celu modyfikowania nagłówków żądań i odpowiedzi oraz adresów URL przy użyciu opcji [Zapisz ponownie nagłówki i adres URL usługi http](rewrite-http-headers-url.md) lub zmodyfikować ścieżkę URI przy użyciu ustawienia przesłonięcia ścieżki. Jednak dopóki nie zostaną skonfigurowane, wszystkie żądania przychodzące są przekazywane do zaplecza.

## <a name="next-steps"></a>Następne kroki

[Informacje o składnikach Application Gateway](application-gateway-components.md)