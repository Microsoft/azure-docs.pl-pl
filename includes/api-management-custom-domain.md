---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: 1858317d40efa59b188ce894534be93a1f11b287
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96027954"
---
## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>Jak serwer proxy usługi API Management odpowiada przy użyciu certyfikatów SSL podczas uzgadniania szyfrowania TLS

### <a name="clients-calling-with-sni-header"></a>Klienci wywołujący nagłówek SNI
Jeśli klient ma jedną lub wiele domen niestandardowych skonfigurowanych dla serwera proxy, APIM może odpowiedzieć na żądania HTTPS z domen niestandardowych (na przykład contoso.com), a także domenę domyślną (na przykład apim-service-name.azure-api.net). Na podstawie informacji w nagłówku Oznaczanie nazwy serwera (SNI) APIM odpowiada przy użyciu odpowiedniego certyfikatu serwera.

### <a name="clients-calling-without-sni-header"></a>Klienci wywołujący bez nagłówka SNI
Jeśli klient używa klienta programu, który nie wysyła nagłówka [SNI](https://tools.ietf.org/html/rfc6066#section-3) , APIM tworzy odpowiedzi na podstawie następujących logiki:

* Jeśli usługa ma tylko jedną domenę niestandardową skonfigurowaną dla serwera proxy, certyfikat domyślny jest certyfikatem wystawionym dla domeny niestandardowej serwera proxy.
* Jeśli usługa skonfigurowała wiele domen niestandardowych dla serwera proxy (obsługiwana w warstwie **deweloper** i **Premium** ), klient może wyznaczyć certyfikat domyślny. Aby ustawić certyfikat domyślny, właściwość [defaultSslBinding](/rest/api/apimanagement/2019-12-01/apimanagementservice/createorupdate#hostnameconfiguration) powinna mieć wartość true ("defaultSslBinding": "true"). Jeśli klient nie ustawi właściwości, domyślnym certyfikatem jest certyfikat wystawiony dla domyślnej domeny serwera proxy hostowanej w lokalizacji *. azure-api.net.

## <a name="support-for-putpost-request-with-large-payload"></a>Obsługa żądania PUT/POST z dużym ładunkiem

Serwer proxy APIM obsługuje żądanie z dużym ładunkiem w przypadku używania certyfikatów po stronie klienta w protokole HTTPS (na przykład ładunek > 40 KB). Aby zapobiec zablokowaniu żądania serwera, klienci mogą ustawić właściwość ["negotiateClientCertificate": "true"](/rest/api/apimanagement/2019-12-01/ApiManagementService/CreateOrUpdate#hostnameconfiguration) na nazwie hosta serwera proxy. Jeśli właściwość ma wartość true, certyfikat klienta jest wymagany w czasie połączenia SSL/TLS przed dowolnymi żądaniami protokołu HTTP. Ponieważ to ustawienie jest stosowane na poziomie **nazwy hosta serwera proxy** , wszystkie żądania połączeń żądają certyfikatu klienta. Klienci mogą skonfigurować maksymalnie 20 domen niestandardowych dla serwera proxy (obsługiwane tylko w warstwie **Premium** ) i obejść to ograniczenie.