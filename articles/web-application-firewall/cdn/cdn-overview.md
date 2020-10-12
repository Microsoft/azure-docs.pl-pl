---
title: Co to jest Zapora aplikacji sieci Web platformy Azure na Azure CDN?
description: Dowiedz się, jak usługa Zapora aplikacji sieci Web platformy Azure w usłudze Azure CDN chroni aplikacje sieci Web przed złośliwymi atakami.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: victorh
ms.openlocfilehash: 6949c1e8f83ebf47878a3d449796ccc03920756a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89225156"
---
# <a name="azure-web-application-firewall-on-azure-content-delivery-network"></a>Zapora aplikacji sieci Web platformy Azure na platformie Azure Content Delivery Network

Zapora aplikacji sieci Web platformy Azure (WAF) w usłudze Azure Content Delivery Network (CDN) firmy Microsoft zapewnia scentralizowaną ochronę zawartości sieci Web. WAF obrony usług sieci Web przed typowymi programami wykorzystującymi luki w zabezpieczeniach. Zapewnia ona wysoką dostępność usługi dla użytkowników i pomaga spełnić wymagania dotyczące zgodności.

> [!IMPORTANT]
> WAF na Azure CDN firmy Microsoft jest obecnie w publicznej wersji zapoznawczej i jest udostępniana w ramach umowy dotyczącej poziomu usług w wersji zapoznawczej. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.  Aby uzyskać szczegółowe informacje, zobacz [Dodatkowe warunki użytkowania wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

WAF na Azure CDN to globalne i scentralizowane rozwiązanie. Jest ona wdrażana w lokalizacjach brzegowych sieci platformy Azure na całym świecie. WAF przestaje złośliwych ataków blisko źródeł ataku przed osiągnięciem pochodzenia. Na dużą skalę możesz uzyskać ochronę globalną, bez obniżania wydajności. 

Zasady WAF łatwo łączą się z dowolnym punktem końcowym usługi CDN w ramach subskrypcji. Nowe reguły można wdrożyć w ciągu kilku minut, dzięki czemu można szybko reagować na zmiany wzorców zagrożeń.

![Zapora aplikacji internetowej platformy Azure](../media/cdn-overview/waf-cdn-overview.png)

## <a name="waf-policy-and-rules"></a>Zasady i reguły WAF

Można skonfigurować zasady WAF i skojarzyć te zasady z co najmniej jednym punktem końcowym usługi CDN w celu ochrony. Zasady WAF obejmują dwa typy reguł zabezpieczeń:

- Reguły niestandardowe, które można utworzyć.

- zarządzane zestawy reguł, które są kolekcjami wstępnie skonfigurowanych reguł platformy Azure.

Gdy obie są obecne, reguły niestandardowe są przetwarzane przed przetworzeniem reguł w zarządzanym zestawie reguł. Reguła zawiera warunek dopasowania, priorytet i akcję. Obsługiwane typy akcji to: *Zezwalaj*, *Blokuj*, *Rejestruj*i *Przekieruj*. Można utworzyć w pełni dostosowane zasady spełniające określone wymagania dotyczące ochrony aplikacji przez połączenie reguł zarządzanych i niestandardowych.

Reguły w ramach zasad są przetwarzane w kolejności priorytetów. Priority to unikatowy numer, który definiuje kolejność reguł do przetworzenia. Mniejsza liczba jest wyższym priorytetem, a reguły są oceniane przed regułami o większej wartości. Po dopasowaniu reguły odpowiednia akcja zdefiniowana w regule zostanie zastosowana do żądania. Gdy takie dopasowanie zostanie przetworzone, reguły o niższych priorytetach nie są przetwarzane więcej.

Aplikacja sieci Web hostowana na Azure CDN może mieć tylko jedną zasadę WAF z niej skojarzoną. Można jednak mieć punkt końcowy usługi CDN bez skojarzonych z nim zasad WAF. Jeśli zasady WAF są obecne, są replikowane do wszystkich naszych lokalizacji brzegowych w celu zapewnienia spójnych zasad zabezpieczeń na całym świecie.

## <a name="waf-modes"></a>Tryby WAF

Zasady WAF można skonfigurować tak, aby były uruchamiane w następujących dwóch trybach:

- *Tryb wykrywania*: w przypadku uruchamiania w trybie wykrywania WAF nie przyjmuje żadnych innych akcji innych niż monitory i rejestruje żądanie i dopasowaną regułę WAF do dzienników WAF. Można włączyć diagnostykę rejestrowania dla sieci CDN. W przypadku korzystania z portalu przejdź do sekcji **Diagnostyka** .

- *Tryb zapobiegania*: w trybie zapobiegania, WAF wykonuje określoną akcję, jeśli żądanie jest zgodne z regułą. W przypadku znalezienia dopasowania nie są oceniane dalsze reguły o niższym priorytecie. Wszystkie dopasowane żądania są również rejestrowane w dziennikach WAF.

## <a name="waf-actions"></a>Akcje WAF

Gdy żądanie spełnia warunki reguły, można wybrać jedną z następujących akcji:

- *Zezwalaj*: żądanie przechodzi przez WAF i jest przekazywane do zaplecza. Żadne dalsze reguły o niższym priorytecie nie mogą blokować tego żądania.
- *Blokuj*: żądanie jest blokowane i WAF wysyła odpowiedź do klienta bez przesyłania dalej żądania do zaplecza.
- *Dziennik*: żądanie jest rejestrowane w dziennikach WAF, a WAF kontynuuje szacowanie reguł o niższym priorytecie.
- *Redirect*: WAF przekierowuje żądanie do określonego identyfikatora URI. Określony identyfikator URI jest ustawieniem poziomu zasad. Po skonfigurowaniu wszystkie żądania zgodne z akcją *przekierowania* są wysyłane do tego identyfikatora URI.

## <a name="waf-rules"></a>Reguły WAF

Zasady WAFymi mogą składać się z dwóch typów reguł zabezpieczeń:

- *reguły niestandardowe*: utworzone przez siebie reguły 
- *zarządzane zbiory reguł*: zarządzane wstępnie zestaw reguł na platformie Azure

### <a name="custom-rules"></a>Reguły niestandardowe

Reguły niestandardowe mogą mieć reguły zgodności i reguły kontroli szybkości.

Można skonfigurować następujące niestandardowe reguły dopasowania:

- *Lista dozwolonych adresów IP i lista zablokowanych*: można kontrolować dostęp do aplikacji sieci Web w oparciu o listę IP lub zakresy adresów IP klientów. Obsługiwane są zarówno typy adresów IPv4, jak i IPv6. Tę listę można skonfigurować do blokowania lub zezwalania na te żądania, w przypadku których źródłowy adres IP jest zgodny z adresem IP na liście.

- *Kontrola dostępu na podstawie geograficznej*: można kontrolować dostęp do aplikacji sieci Web w oparciu o kod kraju skojarzony z adresem IP klienta.

- *Kontrola dostępu oparta na parametrach protokołu HTTP*: reguły podstawowe dla dopasowania ciągu w parametrach żądania HTTP/HTTPS.  Na przykład ciągi zapytań, POST args, identyfikator URI żądania, nagłówek żądania i treść żądania.

- *Żądaj kontroli dostępu opartej na metodzie*: reguły podstawowe w metodzie żądania HTTP żądania. Na przykład GET, PUT lub szef.

- *Ograniczenie rozmiaru*: reguły można oprzeć na długości określonych części żądania, takich jak ciąg zapytania, identyfikator URI lub treść żądania.

Reguła kontroli częstotliwości ogranicza nietypowy ruch z dowolnego adresu IP klienta.

- *Reguły ograniczania szybkości*: można skonfigurować próg liczby żądań sieci Web dozwolonych przez adres IP klienta w ciągu jednej minuty. Ta reguła różni się od reguły typu "Zezwól/Blokuj" na podstawie listy adresów IP, która zezwala na wszystkie żądania z adresu IP klienta lub je blokuje. Limity szybkości można łączyć z dodatkowymi warunkami dopasowania, takimi jak parametry protokołu HTTP (S), aby uzyskać szczegółowy formant kontroli szybkości.

### <a name="azure-managed-rule-sets"></a>Zestawy reguł zarządzane przez platformę Azure

Zestawy reguł zarządzane przez platformę Azure zapewniają łatwy sposób wdrażania ochrony przed wspólnym zbiorem zagrożeń bezpieczeństwa. Ponieważ te zestawu reguł są zarządzane przez platformę Azure, reguły są aktualizowane w razie potrzeby w celu ochrony przed nowymi sygnaturami ataków. Zestaw reguł domyślnych zarządzanych przez platformę Azure zawiera reguły dotyczące następujących kategorii zagrożeń:

- Użycie skryptów między witrynami
- Ataki Java
- Włączenie lokalnego pliku
- Ataki w języku PHP
- Zdalne wykonywanie poleceń
- Dołączanie pliku zdalnego
- Utrwalanie sesji
- Ochrona przed atakami polegającymi na iniekcji SQL
- Atakujący protokołu

Numer wersji zestawu reguł domyślnych jest zwiększany, gdy do zestawu reguł są dodawane nowe podpisy ataków.
Domyślny zestaw reguł jest domyślnie włączony w trybie *wykrywania* w zasadach WAF. Można wyłączyć lub włączyć poszczególne reguły w ramach domyślnego zestawu reguł, aby spełniały wymagania aplikacji. Można również ustawić określone akcje (Zezwalaj/Blokuj/REDIRECT/LOG) dla każdej reguły. Domyślna akcja dla zarządzanego zestawu reguł domyślnych jest *blokowana*.

Reguły niestandardowe są zawsze stosowane przed oceną reguł w domyślnym zestawie reguł. Jeśli żądanie jest zgodne z regułą niestandardową, zostanie zastosowana odpowiednia akcja reguły. Żądanie jest blokowane lub przenoszone do zaplecza. Nie są przetwarzane żadne inne reguły niestandardowe lub reguły w domyślnym zestawie reguł. Można również usunąć domyślny zestaw reguł z zasad WAFymi.

## <a name="configuration"></a>Konfiguracja

Można skonfigurować i wdrożyć wszystkie typy reguł WAF za pomocą Azure Portal, interfejsów API REST, szablonów Azure Resource Manager i Azure PowerShell.

## <a name="monitoring"></a>Monitorowanie

Monitorowanie dla WAF za pomocą usługi CDN jest zintegrowane z Azure Monitor do śledzenia alertów i łatwego monitorowania trendów ruchu.

## <a name="next-steps"></a>Następne kroki

- [Samouczek: Tworzenie zasad WAFymi za pomocą Azure CDN przy użyciu Azure Portal](waf-cdn-create-portal.md)
