---
title: Omówienie monitorowania kondycji dla usługi Azure Application Gateway
description: Usługa Azure Application Gateway monitoruje kondycję wszystkich zasobów w puli zaplecza i automatycznie usuwa wszelkie zasoby uznawane za złej kondycji z puli.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: f0e5a153efe26640e54f386600f07c7b3d4711d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89649063"
---
# <a name="application-gateway-health-monitoring-overview"></a>Application Gateway — Omówienie monitorowania kondycji

Usługa Azure Application Gateway domyślnie monitoruje kondycję wszystkich zasobów w puli zaplecza i automatycznie usuwa wszelkie zasoby uznawane za złej kondycji z puli. Usługa Application Gateway w dalszym ciągu monitoruje wystąpienia w złej kondycji i dodaje je do puli zaplecza w dobrej kondycji, gdy staną się dostępne i zaczną odpowiadać na sondy kondycji. Domyślnie Brama aplikacji wysyła sondy kondycji z tym samym portem, który jest zdefiniowany w ustawieniach protokołu HTTP zaplecza. Niestandardowy port sondy można skonfigurować przy użyciu niestandardowej sondy kondycji.

Źródłowy adres IP Application Gateway używany przez sondy kondycji zależy od puli zaplecza:
 
- Jeśli adres serwera w puli zaplecza jest publicznym punktem końcowym, adres źródłowy jest publicznym adresem IP frontonu bramy aplikacji.
- Jeśli adres serwera w puli zaplecza jest prywatnym punktem końcowym, źródłowy adres IP pochodzi z prywatnej przestrzeni adresowej IP podsieci usługi Application Gateway.

![przykład sondowania bramy aplikacji][1]

Oprócz domyślnego monitorowania sondy kondycji można także dostosować sondę kondycji do wymagań aplikacji. W tym artykule są pokryte domyślne i niestandardowe sondy kondycji.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="default-health-probe"></a>Domyślna sonda kondycji

Brama aplikacji automatycznie konfiguruje domyślną sondę kondycji, gdy nie zostanie skonfigurowana żadna niestandardowa konfiguracja sondy. Zachowanie monitorowania działa przez wykonywanie żądania HTTP GET do adresów IP lub nazw FQDN skonfigurowanych w puli zaplecza. W przypadku domyślnych sond, jeśli ustawienia protokołu HTTP zaplecza są skonfigurowane dla protokołu HTTPS, sonda korzysta z protokołu HTTPS do testowania kondycji serwerów wewnętrznej bazy danych.

Na przykład: skonfigurujesz bramę aplikacji tak, aby korzystała z serwerów zaplecza A, B i C do odbierania ruchu sieciowego HTTP na porcie 80. Domyślne monitorowanie kondycji testuje trzy serwery co 30 sekund dla prawidłowej odpowiedzi HTTP z 30-sekundowym limitem czasu dla każdego żądania. W dobrej kondycji odpowiedź HTTP ma [kod stanu](https://msdn.microsoft.com/library/aa287675.aspx) z przedziału od 200 do 399. W takim przypadku żądanie HTTP GET dla sondy kondycji będzie wyglądać następująco http://127.0.0.1/ .

Jeśli domyślne sprawdzenie sondy zakończy się niepowodzeniem dla serwera A, Brama aplikacji zatrzymuje przekazywanie żądań do tego serwera. Domyślna sonda nadal kontynuuje wyszukiwanie serwera A co 30 sekund. Gdy serwer A odpowiada na jedno żądanie z domyślnej sondy kondycji, Brama aplikacji zacznie ponownie przesyłać żądania do serwera.

### <a name="default-health-probe-settings"></a>Domyślne ustawienia sondy kondycji

| Właściwość sondy | Wartość | Opis |
| --- | --- | --- |
| Adres URL sondy |\<protocol\>://127.0.0.1:\<port\>/ |Protokół i port są dziedziczone z ustawień protokołu HTTP zaplecza, do których jest skojarzona sonda |
| Interwał |30 |Czas oczekiwania (w sekundach) przed wysłaniem następnej sondy kondycji.|
| Limit czasu |30 |Czas w sekundach, przez który Brama aplikacji czeka na odpowiedź sondy przed oznaczeniem sondy jako złej kondycji. Jeśli sonda jest powracana w dobrej kondycji, odpowiednie zaplecze jest natychmiast oznaczone jako w dobrej kondycji.|
| Próg złej kondycji |3 |Decyduje o liczbie sond do wysłania w przypadku niepowodzenia regularnej sondy kondycji. W wersji 1 jednostki SKU te dodatkowe sondy kondycji są wysyłane w krótkim czasie, aby szybko określić kondycję zaplecza i nie czekać na interwał sondowania. W przypadku wersji 2 jednostki SKU sondy kondycji czekają interwał. Serwer zaplecza jest oznaczony jako wyłączony po kolejnej liczbie błędów sondy osiągnie próg złej kondycji. |

Sonda domyślna jest sprawdzana tylko pod adresem \<protocol\> : \/ /127.0.0.1: \<port\> w celu określenia stanu kondycji. Jeśli musisz skonfigurować sondę kondycji, aby przejść do niestandardowego adresu URL lub zmodyfikować inne ustawienia, musisz użyć niestandardowych sond. Aby uzyskać więcej informacji na temat sond protokołu HTTPS, zobacz [Omówienie kończenia protokołu TLS i kompleksowej usługi TLS z Application Gateway](ssl-overview.md#for-probe-traffic).

### <a name="probe-intervals"></a>Interwały sondowania

Wszystkie wystąpienia Application Gateway sondować zaplecze niezależne od siebie. Ta sama konfiguracja sondy ma zastosowanie do każdego wystąpienia Application Gateway. Na przykład jeśli konfiguracja sondy ma wysyłać sondy kondycji co 30 sekund, a Brama aplikacji ma dwa wystąpienia, to oba wystąpienia wysyłają sondę kondycji co 30 sekund.

Ponadto jeśli istnieje wiele detektorów, każdy odbiornik sonduje zaplecze niezależne od siebie. Na przykład, jeśli istnieją dwa odbiorniki wskazujące tę samą pulę zaplecza na dwóch różnych portach (skonfigurowane przez dwa ustawienia protokołu HTTP zaplecza), każdy odbiornik sonduje tego samego zaplecza niezależnie. W takim przypadku istnieją dwie sondy z każdego wystąpienia bramy aplikacji dla dwóch odbiorników. Jeśli w tym scenariuszu istnieją dwa wystąpienia bramy aplikacji, na maszynie wirtualnej zaplecza zobaczysz cztery sondy zgodnie ze skonfigurowanym interwałem sondowania.

## <a name="custom-health-probe"></a>Niestandardowa sonda kondycji

Niestandardowe sondy umożliwiają bardziej szczegółową kontrolę nad monitorowaniem kondycji. W przypadku korzystania z sond niestandardowych można skonfigurować niestandardową nazwę hosta, ścieżkę adresu URL, interwał sondowania oraz liczbę niepowodzeń odpowiedzi akceptowanych przed oznaczeniem wystąpienia puli zaplecza jako złej kondycji itd.

### <a name="custom-health-probe-settings"></a>Niestandardowe ustawienia sondy kondycji

Poniższa tabela zawiera definicje właściwości niestandardowej sondy kondycji.

| Właściwość sondy | Opis |
| --- | --- |
| Nazwa |Nazwa sondy. Ta nazwa służy do identyfikowania i odwoływania się do sondy w ustawieniach protokołu HTTP zaplecza. |
| Protokół |Protokół używany do wysyłania sondy. Ta wartość musi być zgodna z protokołem zdefiniowanym w ustawieniach HTTP zaplecza, z którym jest skojarzone|
| Host |Nazwa hosta, za pomocą którego ma zostać wysłana sonda. W wersji 1 jednostki SKU ta wartość zostanie użyta tylko dla nagłówka hosta żądania sondowania. W wersji 2 jednostki SKU zostanie ona użyta zarówno jako nagłówek hosta, jak i SNI |
| Ścieżka |Ścieżka względna sondy. Prawidłowa ścieżka zaczyna się od znaku "/" |
| Port |Jeśli jest zdefiniowany, jest używany jako port docelowy. W przeciwnym razie używa tego samego portu, który jest skojarzony z ustawieniami protokołu HTTP. Ta właściwość jest dostępna tylko w jednostce SKU v2
| Interwał |Interwał sondy (w sekundach). Ta wartość jest przedziałem czasu między dwoma kolejnymi sondami |
| Limit czasu |Limit czasu sondy w sekundach. Jeśli prawidłowa odpowiedź nie zostanie odebrana w tym okresie, sonda jest oznaczona jako nieudana  |
| Próg złej kondycji |Liczba ponownych prób sondowania. Serwer zaplecza jest oznaczony jako wyłączony po kolejnej liczbie błędów sondy osiągnie próg złej kondycji |

### <a name="probe-matching"></a>Dopasowanie sondy

Domyślnie odpowiedź HTTP (S) o kodzie stanu z zakresu od 200 do 399 jest traktowana w dobrej kondycji. Niestandardowe sondy kondycji dodatkowo obsługują dwa zgodne kryteria. Kryterium dopasowywania można użyć, aby opcjonalnie zmodyfikować domyślną interpretację co daje w wyniku dobrą odpowiedź.

Poniżej przedstawiono kryteria dopasowywania: 

- **Dopasowanie kodu stanu odpowiedzi HTTP** — kryterium dopasowywania sondy dla żądania kodu odpowiedzi HTTP określonego przez użytkownika lub zakresów kodu odpowiedzi. Obsługiwane są oddzielne kody stanu odpowiedzi oddzielone przecinkami lub zakres kodu stanu.
- **Dopasowanie treści odpowiedzi HTTP** — kryterium dopasowywania sondy, które przegląda treść odpowiedzi HTTP i dopasowuje do ciągu określonego przez użytkownika. Dopasowanie tylko szuka obecności ciągu określonego przez użytkownika w treści odpowiedzi i nie jest pełnym dopasowaniem wyrażenia regularnego.

Kryteria dopasowywania można określić przy użyciu `New-AzApplicationGatewayProbeHealthResponseMatch` polecenia cmdlet.

Na przykład:

```azurepowershell
$match = New-AzApplicationGatewayProbeHealthResponseMatch -StatusCode 200-399
$match = New-AzApplicationGatewayProbeHealthResponseMatch -Body "Healthy"
```
Po określeniu kryteriów dopasowania można je dołączyć do konfiguracji sondowania przy użyciu `-Match` parametru w programie PowerShell.

## <a name="nsg-considerations"></a>Zagadnienia dotyczące sieciowej grupy zabezpieczeń

Należy zezwolić na przychodzący ruch internetowy na portach TCP 65503-65534 dla jednostki SKU Application Gateway V1 i portów TCP 65200-65535 dla jednostki SKU v2 z podsiecią docelową jako **dowolny** i źródłowy jako tag usługi **gatewaymanager** . Ten zakres portów jest wymagany w przypadku komunikacji infrastruktury platformy Azure.

Ponadto nie można zablokować wychodzącej łączności z Internetem, a ruch przychodzący pochodzący ze znacznika **AzureLoadBalancer** musi być dozwolony.

Aby uzyskać więcej informacji, zobacz [Omówienie konfiguracji Application Gateway](configuration-infrastructure.md#network-security-groups).

## <a name="next-steps"></a>Następne kroki
Po rozpoczęciu uczenia się Application Gateway monitorowania kondycji można skonfigurować [niestandardową sondę kondycji](application-gateway-create-probe-portal.md) w Azure Portal lub [niestandardowej sondy kondycji](application-gateway-create-probe-ps.md) przy użyciu programu PowerShell i modelu wdrażania Azure Resource Manager.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png
