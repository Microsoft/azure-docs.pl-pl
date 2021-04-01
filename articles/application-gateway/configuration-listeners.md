---
title: Konfiguracja usługi Azure Application Gateway Listener
description: W tym artykule opisano sposób konfigurowania odbiorników usługi Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: 15f68e8cbca65e7b970944f7ca5ef1952140cc6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397647"
---
# <a name="application-gateway-listener-configuration"></a>Konfiguracja odbiornika Application Gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Odbiornik jest jednostką logiczną, która sprawdza przychodzące żądania połączeń przy użyciu portu, protokołu, hosta i adresu IP. Podczas konfigurowania odbiornika należy wprowadzić wartości pasujące do odpowiednich wartości w żądaniu przychodzącym na bramie.

Podczas tworzenia bramy aplikacji przy użyciu Azure Portal należy również utworzyć odbiornik domyślny, wybierając protokół i port odbiornika. Możesz wybrać, czy włączyć obsługę HTTP2 na odbiorniku. Po utworzeniu bramy aplikacji można edytować ustawienia tego odbiornika domyślnego (*appGatewayHttpListener*) lub utworzyć nowe odbiorniki.

## <a name="listener-type"></a>Typ odbiornika

Podczas tworzenia nowego odbiornika należy wybrać jedną z [ *podstawowych* i *wiele lokacji*](./application-gateway-components.md#types-of-listeners).

- Jeśli chcesz, aby wszystkie żądania (dla dowolnej domeny) zostały zaakceptowane i przesłane dalej do pul zaplecza, wybierz pozycję podstawowa. Dowiedz się, [jak utworzyć bramę aplikacji z odbiornikiem podstawowym](./quick-create-portal.md).

- Jeśli chcesz przekazywać żądania do różnych pul zaplecza na podstawie nazwy nagłówka *hosta* lub hosta, wybierz odbiornik wielu witryn, w którym należy określić nazwę hosta zgodną z żądaniem przychodzącym. Jest to spowodowane tym, że Application Gateway opiera się na nagłówkach hosta HTTP 1,1 do hostowania więcej niż jednej witryny sieci Web na tym samym publicznym adresie IP i porcie. Aby dowiedzieć się więcej, zobacz [hostowanie wielu witryn przy użyciu Application Gateway](multiple-site-overview.md).

### <a name="order-of-processing-listeners"></a>Kolejność przetwarzania odbiorników

W przypadku jednostki SKU V1 żądania są dopasowywane zgodnie z kolejnością reguł i typem odbiornika. Jeśli reguła z odbiornikiem podstawowym jest najpierw w kolejności, zostanie przetworzona jako pierwsza i zaakceptuje wszystkie żądania dla tego portu i kombinacji adresów IP. Aby tego uniknąć, należy najpierw skonfigurować reguły z odbiornikami z obsługą kilku lokacji i wypchnąć regułę z odbiornikiem podstawowym do ostatniego z listy.

W przypadku jednostki SKU w wersji 2 odbiorniki z obsługą wiele lokacji są przetwarzane przed podstawowymi odbiornikami.

## <a name="front-end-ip-address"></a>Adres IP frontonu

Wybierz adres IP frontonu, który ma zostać skojarzony z tym odbiornikiem. Odbiornik nasłuchuje przychodzących żądań w tym adresie IP.

## <a name="front-end-port"></a>Port frontonu

Wybierz port frontonu. Wybierz istniejący port lub Utwórz nowy. Wybierz dowolną wartość z [dozwolonego zakresu portów](./application-gateway-components.md#ports). Można użyć nie tylko dobrze znanych portów, na przykład 80 i 443, ale dowolnego dozwolonego niestandardowego portu, który jest odpowiedni. Port może być używany na potrzeby odbiorników publicznych lub odbiorników prywatnych.

## <a name="protocol"></a>Protokół

Wybierz pozycję HTTP lub HTTPS:

- W przypadku wybrania protokołu HTTP ruch między klientem i bramą aplikacji jest niezaszyfrowany.

- Wybierz opcję HTTPS, jeśli chcesz, aby [protokół TLS](features.md#secure-sockets-layer-ssltls-termination) lub [kompleksowe szyfrowanie TLS](./ssl-overview.md). Ruch między klientem i bramą aplikacji jest szyfrowany. A połączenie TLS kończy się na bramie aplikacji. Aby kompleksowe szyfrowanie TLS było wymagane, należy wybrać opcję HTTPS i skonfigurować ustawienia **protokołu HTTP zaplecza** . Gwarantuje to, że ruch jest ponownie szyfrowany podczas podróży z bramy aplikacji do zaplecza.


Aby skonfigurować zakończenie protokołu TLS i kompleksowe szyfrowanie protokołu TLS, należy dodać certyfikat do odbiornika, aby umożliwić bramie aplikacji uzyskanie klucza symetrycznego. Jest to podyktowane specyfikacją protokołu TLS. Klucz symetryczny służy do szyfrowania i odszyfrowywania ruchu wysyłanego do bramy. Certyfikat bramy musi być w formacie wymiany informacji osobistych (PFX). Ten format umożliwia wyeksportowanie klucza prywatnego, który jest wykorzystywany przez bramę do szyfrowania i odszyfrowywania ruchu.

## <a name="supported-certificates"></a>Obsługiwane certyfikaty

Zobacz [Omówienie kończenia protokołu TLS i kompleksowej usługi TLS z usługą Application Gateway](ssl-overview.md#certificates-supported-for-tls-termination)

## <a name="additional-protocol-support"></a>Obsługa dodatkowych protokołów

### <a name="http2-support"></a>Obsługa HTTP2

Obsługa protokołu HTTP/2 jest dostępna dla klientów, którzy łączą się tylko z odbiornikami bramy aplikacji. Komunikacja z pulami serwerów zaplecza odbywa się za pośrednictwem protokołu HTTP/1.1. Domyślnie obsługa protokołu HTTP/2 jest wyłączona. Poniższy fragment kodu Azure PowerShell ilustruje, jak to włączyć:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

### <a name="websocket-support"></a>Obsługa protokołu WebSocket

Obsługa protokołu WebSocket jest domyślnie włączona. Nie istnieje ustawienie konfigurowalne przez użytkownika, które można włączyć lub wyłączyć. Można używać obiektów WebSockets z odbiornikami HTTP i HTTPS.

## <a name="custom-error-pages"></a>Niestandardowe strony błędów

Błąd niestandardowy można zdefiniować na poziomie globalnym lub na poziomie odbiornika. Jednak tworzenie niestandardowych stron błędów niestandardowych na poziomie globalnym na podstawie Azure Portal nie jest obecnie obsługiwane. Można skonfigurować niestandardową stronę błędów dla błędu zapory aplikacji sieci Web 403 lub strony obsługi 502 na poziomie odbiornika. Należy również określić publicznie dostępny adres URL obiektu BLOB dla danego kodu stanu błędu. Aby uzyskać więcej informacji, zobacz [Create Application Gateway custom error pages (Tworzenie niestandardowych stron błędów w usłudze Application Gateway)](./custom-error.md).

![Application Gateway kody błędów](/azure/application-gateway/media/custom-error/ag-error-codes.png)

Aby skonfigurować globalną stronę błędu niestandardowego, zobacz [konfiguracja Azure PowerShell](./custom-error.md#azure-powershell-configuration).

## <a name="tls-policy"></a>Zasady protokołu TLS

Można scentralizować zarządzanie certyfikatami TLS/SSL i zmniejszyć obciążenie odszyfrowywania dla farmy serwerów zaplecza. Scentralizowana obsługa protokołu TLS umożliwia również określenie centralnych zasad protokołu TLS, które są odpowiednie do wymagań dotyczących zabezpieczeń. Można wybrać *domyślne*, *wstępnie zdefiniowane* lub *niestandardowe* zasady protokołu TLS.

Zasady protokołu TLS można skonfigurować do kontroli wersji protokołu TLS. Bramę aplikacji można skonfigurować tak, aby korzystała z minimalnej wersji protokołu dla uzgadniania TLS z protokołów TLS 1.0, TLS 1.1 i TLS 1.2. Domyślnie protokoły SSL 2,0 i 3,0 są wyłączone i nie można ich konfigurować. Aby uzyskać więcej informacji, zobacz [Application Gateway zasad protokołu TLS](./application-gateway-ssl-policy-overview.md).

Po utworzeniu odbiornika należy skojarzyć go z regułą routingu żądania. Ta reguła określa, jak żądania odbierane w odbiorniku są kierowane do zaplecza.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej na temat reguł routingu żądań](configuration-request-routing-rules.md).