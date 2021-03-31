---
title: Składniki usługi Application Gateway
description: Ten artykuł zawiera informacje dotyczące różnych składników w bramie aplikacji
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: surmb
ms.openlocfilehash: ebd06b0b78ee511dce535ff4220df03087fb6906
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88723320"
---
# <a name="application-gateway-components"></a>Składniki usługi Application Gateway

 Brama aplikacji służy jako pojedynczy punkt kontaktu dla klientów. Dystrybuuje ruch aplikacji przychodzących między wieloma pulami zaplecza, takich jak maszyny wirtualne platformy Azure, zestawy skalowania maszyn wirtualnych, Azure App Service i serwery lokalne/zewnętrzne. Aby dystrybuować ruch, Brama aplikacji używa kilku składników opisanych w tym artykule.

![Składniki używane w bramie aplikacji](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>Adresy IP frontonu

Adres IP frontonu to adres IP skojarzony z bramą aplikacji. Bramę aplikacji można skonfigurować tak, aby zawierała publiczny adres IP, prywatny adres IP lub oba te elementy. Brama aplikacji obsługuje jeden publiczny lub jeden prywatny adres IP. Twoja sieć wirtualna i publiczny adres IP muszą znajdować się w tej samej lokalizacji co Brama aplikacji. Po jego utworzeniu adres IP frontonu jest skojarzony z odbiornikiem.

### <a name="static-versus-dynamic-public-ip-address"></a>Statyczny i dynamiczny publiczny adres IP

Jednostka SKU platformy Azure Application Gateway v2 można skonfigurować do obsługi zarówno statycznego wewnętrznego adresu IP, jak i statycznego publicznego adresu IP, lub tylko statycznego publicznego adresu IP. Nie można go skonfigurować do obsługi tylko statycznego wewnętrznego adresu IP.

Jednostka SKU V1 można skonfigurować do obsługi statycznego lub dynamicznego wewnętrznego adresu IP oraz dynamicznego publicznego adresu IP. Dynamiczny adres IP Application Gateway nie zmienia się na działającej bramie. Można to zmienić tylko wtedy, gdy zatrzymasz lub uruchomisz bramę. Nie zmieniają się na awarie systemu, aktualizacje, aktualizacje hosta platformy Azure itd. 

Nazwa DNS skojarzona z bramą aplikacji nie zmienia cyklu życia bramy. W związku z tym należy użyć aliasu CNAME i wskazać go adres DNS bramy aplikacji.

## <a name="listeners"></a>Odbiorniki

Odbiornik jest jednostką logiczną, która sprawdza przychodzące żądania połączenia. Odbiornik akceptuje żądanie, jeśli protokół, port, nazwa hosta i adres IP skojarzony z żądaniem są zgodne z tymi samymi elementami, które są skojarzone z konfiguracją odbiornika.

Przed użyciem bramy aplikacji należy dodać co najmniej jeden odbiornik. Do bramy aplikacji może być przypisanych wiele odbiorników, które mogą być używane dla tego samego protokołu.

Gdy odbiornik wykryje przychodzące żądania od klientów, Brama aplikacji kieruje te żądania do członków w puli zaplecza skonfigurowanych w regule.

Odbiorniki obsługują następujące porty i protokoły.

### <a name="ports"></a>Porty

Port jest miejscem, w którym odbiornik nasłuchuje żądania klienta. Można skonfigurować porty z zakresu od 1 do 65502 dla jednostki SKU w wersji 1 i 1 do 65199 dla jednostki SKU w wersji 2.

### <a name="protocols"></a>Protokoły

Application Gateway obsługuje cztery protokoły: HTTP, HTTPS, HTTP/2 i WebSocket:
>[!NOTE]
>Obsługa protokołu HTTP/2 jest dostępna dla klientów nawiązujących połączenie z odbiornikami bramy aplikacji. Komunikacja z pulami serwerów zaplecza jest zawsze za pośrednictwem protokołu HTTP/1.1. Domyślnie obsługa protokołu HTTP/2 jest wyłączona. Możesz włączyć tę opcję.

- Określ między protokołami HTTP i HTTPS w konfiguracji odbiornika.
- Obsługa [protokołów WebSockets i http/2](features.md#websocket-and-http2-traffic) jest zapewniana w sposób natywny, a [Obsługa protokołu WebSocket](application-gateway-websocket.md) jest domyślnie włączona. Nie ma żadnych ustawień konfigurowanych przez użytkownika umożliwiających selektywne włączenie lub wyłączenie obsługi protokołu WebSocket. Użyj obiektów WebSockets z odbiornikami HTTP i HTTPS.

Użyj odbiornika HTTPS do zakończenia protokołu TLS. Odbiornik HTTPS odciąża szyfrowanie i odszyfrowanie do bramy aplikacji, dzięki czemu serwery sieci Web nie obciążają obciążenia.

### <a name="custom-error-pages"></a>Niestandardowe strony błędów

Application Gateway umożliwia tworzenie niestandardowych stron błędów zamiast wyświetlania domyślnych stron błędów. W przypadku niestandardowych stron błędów możesz użyć własnych oznakowań i układu. Application Gateway wyświetla niestandardową stronę błędu, gdy żądanie nie może nawiązać połączenia z zapleczem.

Aby uzyskać więcej informacji, zobacz [niestandardowe strony błędów dla bramy aplikacji](custom-error.md).

### <a name="types-of-listeners"></a>Typy odbiorników

Istnieją dwa typy odbiorników:

- **Podstawowa**. Ten typ odbiornika nasłuchuje w pojedynczej lokacji domeny, gdzie ma pojedyncze mapowanie DNS na adres IP bramy aplikacji. Ta konfiguracja odbiornika jest wymagana w przypadku hostowania pojedynczej lokacji za bramą aplikacji.

- **Wiele witryn**. Ta konfiguracja odbiornika jest wymagana, gdy chcesz skonfigurować Routing na podstawie nazwy hosta lub nazwy domeny dla więcej niż jednej aplikacji sieci Web w tej samej bramie aplikacji. Ta funkcja umożliwia skonfigurowanie bardziej wydajnej topologii dla wdrożeń przez dodanie nawet ponad 100 witryn internetowych do jednej bramy aplikacji. Każdą witrynę sieci Web można skierować do jej puli zaplecza. Na przykład trzy domeny — contoso.com, fabrikam.com i adatum.com — wskazują adres IP bramy aplikacji. Utworzysz trzy [odbiorniki z wieloma lokacjami](multiple-site-overview.md) i skonfigurujesz każdy odbiornik dla odpowiedniego ustawienia portu i protokołu. 

    Możesz również określić nazwy hosta z symbolami wieloznacznymi w odbiorniku obejmującym wiele witryn, z maksymalnie pięcioma nazwami hostów na odbiornik. Aby dowiedzieć się więcej, zobacz [symbole wieloznaczne nazw hostów w odbiorniku (wersja zapoznawcza)](multiple-site-overview.md#wildcard-host-names-in-listener-preview).

    Aby uzyskać więcej informacji na temat konfigurowania odbiornika z wieloma lokacjami, zobacz [hostowanie wielu witryn w Application Gateway przy użyciu Azure Portal](create-multiple-sites-portal.md).

Po utworzeniu odbiornika należy skojarzyć go z regułą routingu żądania. Ta reguła określa, w jaki sposób żądanie otrzymane na odbiorniku powinno być kierowane do zaplecza. Reguła routingu żądań zawiera również pulę zaplecza, do której należy się kierować, oraz ustawienie protokołu HTTP, w którym są wymienione port zaplecza, protokół itp.

## <a name="request-routing-rules"></a>Żądanie reguł rozsyłania

Reguła routingu żądań jest kluczem składnika bramy aplikacji, ponieważ określa sposób kierowania ruchu do odbiornika. Reguła wiąże odbiornik, pulę serwerów zaplecza i ustawienia protokołu HTTP zaplecza.

Gdy odbiornik akceptuje żądanie, reguła routingu żądań przekazuje żądanie do zaplecza lub przekierowuje ją w innym miejscu. Jeśli żądanie jest przekazywane do zaplecza, reguła routingu żądań określa, do której puli serwerów zaplecza ma zostać przekazana. Reguła routingu żądań określa również, czy nagłówki w żądaniu mają być ponownie zapisywane. Jeden odbiornik może być dołączony do jednej reguły.

Istnieją dwa typy reguł routingu żądań:

- **Podstawowa**. Wszystkie żądania na skojarzonym odbiorniku (na przykład blog.contoso.com/*) są przekazywane do skojarzonej puli zaplecza przy użyciu skojarzonego ustawienia protokołu HTTP.

- **Oparta na ścieżce**. Ta reguła routingu umożliwia kierowanie żądań na skojarzonym odbiorniku do określonej puli zaplecza na podstawie adresu URL w żądaniu. Jeśli ścieżka adresu URL w żądaniu pasuje do wzorca ścieżki w regule opartej na ścieżce, reguła kieruje to żądanie. Stosuje wzorzec ścieżki tylko do ścieżki URL, a nie do parametrów zapytania. Jeśli ścieżka URL w żądaniu odbiornika nie jest zgodna z żadną regułą opartą na ścieżce, kieruje żądanie do domyślnej puli zaplecza i ustawień protokołu HTTP.

Aby uzyskać więcej informacji, zobacz [routing oparty na adresach URL](url-route-overview.md).

### <a name="redirection-support"></a>Obsługa przekierowania

Reguła routingu żądań umożliwia również przekierowywanie ruchu na bramie aplikacji. Jest to mechanizm przekierowania ogólnego, dlatego można przekierować do i z dowolnego portu zdefiniowanego za pomocą reguł.

Możesz wybrać miejsce docelowe przekierowania jako inny odbiornik (co może pomóc włączyć automatyczne przekierowywanie protokołu HTTP do protokołu HTTPS) lub zewnętrznej lokacji. Możesz również wybrać opcję przekierowania na tymczasową lub trwałą lub dołączyć ścieżkę identyfikatora URI i ciąg zapytania do przekierowanego adresu URL.

Aby uzyskać więcej informacji, zobacz [przekierowywanie ruchu na bramie aplikacji](redirect-overview.md).

### <a name="rewrite-http-headers-and-url"></a>Ponowne zapisywanie nagłówków HTTP i adresów URL

Korzystając z reguł ponownego zapisywania, można dodawać, usuwać lub aktualizować żądania HTTP (S) i nagłówki odpowiedzi oraz ścieżkę URL i parametry ciągu zapytania jako pakiety żądań i odpowiedzi przenoszone między pulami klienta i zaplecza za pośrednictwem bramy aplikacji.

W nagłówkach i parametrach adresu URL można ustawić wartości statyczne lub inne nagłówki i zmienne serwera. Pomaga to z ważnymi przypadkami użycia, takimi jak wyodrębnianie adresów IP klientów, usuwanie poufnych informacji o zapleczu, dodawanie dodatkowych zabezpieczeń i tak dalej.

Aby uzyskać więcej informacji, zobacz [Zapisywanie nagłówków HTTP i adresów URL na bramie aplikacji](rewrite-http-headers-url.md).

## <a name="http-settings"></a>Ustawienia protokołu HTTP

Brama aplikacji kieruje ruch do serwerów zaplecza (określony w regule routingu żądań, która obejmuje ustawienia protokołu HTTP), używając numeru portu, protokołu i innych ustawień szczegółowych w tym składniku.

Port i protokół używany w ustawieniach protokołu HTTP określają, czy ruch między usługą Application Gateway i serwerami zaplecza jest szyfrowany (zapewnianie kompleksowego protokołu TLS) lub nieszyfrowany.

Ten składnik jest również używany do:

- Określ, czy sesja użytkownika ma być przechowywana na tym samym serwerze przy użyciu [koligacji sesji opartej na plikach cookie](features.md#session-affinity).

- Usuń bezpiecznie członków puli zaplecza przy użyciu [opróżniania połączeń](features.md#connection-draining).

- Skojarz niestandardową sondę, aby monitorować kondycję zaplecza, ustaw interwał limitu czasu żądania, zastąp nazwę hosta i ścieżkę w żądaniu, a następnie podaj prostotę, aby określić ustawienia dla App Service zaplecza.

## <a name="backend-pools"></a>Pule zaplecza

Pula zaplecza kieruje żądanie do serwerów zaplecza, które umożliwiają żądanie. Pule zaplecza mogą zawierać:

- Karty interfejsów sieciowych
- Zestawy skalowania maszyn wirtualnych
- Publiczne adresy IP
- Wewnętrzne adresy IP
- Nazwa FQDN
- Wielodostępne punkty końcowe (takie jak App Service)

Elementy członkowskie puli zaplecza Application Gateway nie są powiązane z zestawem dostępności. Brama aplikacji może komunikować się z wystąpieniami poza siecią wirtualną, w której się znajduje. W związku z tym elementy członkowskie pul zaplecza mogą znajdować się w różnych klastrach, w centrach danych lub poza platformą Azure, o ile jest to połączenie IP.

Jeśli używasz wewnętrznych adresów IP jako członków puli zaplecza, musisz użyć [komunikacji równorzędnej sieci wirtualnej](../virtual-network/virtual-network-peering-overview.md) lub [bramy sieci VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Komunikacja równorzędna sieci wirtualnych jest obsługiwana i korzystna dla ruchu z równoważeniem obciążenia w innych sieciach wirtualnych.

Brama aplikacji może również komunikować się z serwerami lokalnymi, jeśli są one połączone przez usługę Azure ExpressRoute lub tunele VPN, jeśli jest to dozwolone.

Dla różnych typów żądań można utworzyć różne pule zaplecza. Na przykład Utwórz jedną pulę zaplecza dla żądań ogólnych, a następnie inną pulę zaplecza dla żądań kierowanych do mikrousług dla aplikacji.

## <a name="health-probes"></a>Sondy kondycji

Domyślnie Brama aplikacji monitoruje kondycję wszystkich zasobów w puli zaplecza i automatycznie usuwa w złej kondycji. Następnie monitoruje wystąpienia w złej kondycji i dodaje je z powrotem do odpowiedniej puli zaplecza, gdy staną się dostępne i reagują na sondy kondycji.

Oprócz domyślnego monitorowania sondy kondycji można także dostosować sondę kondycji do wymagań aplikacji. Niestandardowe sondy umożliwiają dokładniejszą kontrolę nad monitorowaniem kondycji. W przypadku korzystania z sond niestandardowych można skonfigurować niestandardową nazwę hosta, ścieżkę adresu URL, interwał sondy oraz liczbę niepowodzeń odpowiedzi akceptowanych przed oznaczeniem wystąpienia puli zaplecza jako złej kondycji, niestandardowych kodów stanu i zgodności treści odpowiedzi itp. Zalecamy skonfigurowanie niestandardowych sond do monitorowania kondycji każdej puli zaplecza.

Aby uzyskać więcej informacji, zobacz [monitorowanie kondycji bramy aplikacji](../application-gateway/application-gateway-probe-overview.md).

## <a name="next-steps"></a>Następne kroki

Tworzenie bramy aplikacji:

* [W witrynie Azure Portal](quick-create-portal.md)
* [Za pomocą Azure PowerShell](quick-create-powershell.md)
* [Za pomocą interfejsu wiersza polecenia platformy Azure](quick-create-cli.md)
