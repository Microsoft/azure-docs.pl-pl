---
title: Omówienie zasad protokołu TLS dla Application Gateway platformy Azure
description: Dowiedz się, jak skonfigurować zasady protokołu TLS dla platformy Azure Application Gateway i zmniejszyć obciążenie szyfrowania i odszyfrowywania z farmy serwerów zaplecza.
services: application gateway
author: amsriva
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: amsriva
ms.openlocfilehash: 16c6dd28d47573c2ad5b0d5a331b0dc48e7aacef
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85253634"
---
# <a name="application-gateway-tls-policy-overview"></a>Omówienie zasad protokołu TLS Application Gateway

Usługi Azure Application Gateway można użyć do scentralizowanego zarządzania certyfikatami TLS/SSL i ograniczenia obciążenia szyfrowania i odszyfrowywania z farmy serwerów zaplecza. Ta Scentralizowana obsługa protokołu TLS umożliwia również określenie centralnych zasad protokołu TLS, które są odpowiednie dla wymagań bezpieczeństwa organizacji. Pomaga to spełnić wymagania dotyczące zgodności, a także wskazówki dotyczące zabezpieczeń i zalecane rozwiązania.

Zasady protokołu TLS obejmują kontrolę wersji protokołu TLS, a także mechanizmy szyfrowania i kolejność, w jakiej szyfry są używane podczas uzgadniania TLS. Application Gateway oferuje dwa mechanizmy kontrolowania zasad protokołu TLS. Można użyć wstępnie zdefiniowanych zasad lub zasad niestandardowych.

## <a name="predefined-tls-policy"></a>Wstępnie zdefiniowane zasady protokołu TLS

Application Gateway ma trzy wstępnie zdefiniowane zasady zabezpieczeń. Bramę można skonfigurować przy użyciu dowolnej z tych zasad, aby uzyskać odpowiedni poziom zabezpieczeń. Nazwy zasad są oznaczane przez rok i miesiąc, w których zostały skonfigurowane. Każda zasada oferuje różne wersje protokołu TLS i mechanizmy szyfrowania. Zalecamy używanie najnowszych zasad protokołu TLS w celu zapewnienia najlepszego zabezpieczenia protokołu TLS.

### <a name="appgwsslpolicy20150501"></a>AppGwSslPolicy20150501

|Właściwość  |Wartość  |
|---|---|
|Nazwa     | AppGwSslPolicy20150501        |
|MinProtocolVersion     | TLSv1_0        |
|Domyślne| True (jeśli nie określono żadnych wstępnie zdefiniowanych zasad) |
|CipherSuites     |TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_DHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_DHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_DHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA256<br>TLS_DHE_DSS_WITH_AES_256_CBC_SHA<br>TLS_DHE_DSS_WITH_AES_128_CBC_SHA<br>TLS_RSA_WITH_3DES_EDE_CBC_SHA<br>TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA |
  
### <a name="appgwsslpolicy20170401"></a>AppGwSslPolicy20170401
  
|Właściwość  |Wartość  |
|   ---      |  ---       |
|Nazwa     | AppGwSslPolicy20170401        |
|MinProtocolVersion     | TLSv1_1        |
|Domyślne| Fałsz |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA<br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA |
  
### <a name="appgwsslpolicy20170401s"></a>AppGwSslPolicy20170401S

|Właściwość  |Wartość  |
|---|---|
|Nazwa     | AppGwSslPolicy20170401S        |
|MinProtocolVersion     | TLSv1_2        |
|Domyślne| Fałsz |
|CipherSuites     |TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256 <br>    TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384 <br>    TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA <br>TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256<br>TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384<br>TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384<br>TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256<br>TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA<br>TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_256_GCM_SHA384<br>TLS_RSA_WITH_AES_128_GCM_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA256<br>TLS_RSA_WITH_AES_128_CBC_SHA256<br>TLS_RSA_WITH_AES_256_CBC_SHA<br>TLS_RSA_WITH_AES_128_CBC_SHA<br> |

## <a name="custom-tls-policy"></a>Niestandardowe zasady protokołu TLS

Jeśli konieczne jest skonfigurowanie wstępnie zdefiniowanych zasad TLS dla wymagań, należy zdefiniować własne niestandardowe zasady protokołu TLS. Przy użyciu niestandardowych zasad protokołu TLS masz pełną kontrolę nad minimalną wersją protokołu TLS do obsługi, a także obsługiwanymi mechanizmami szyfrowania i ich kolejności priorytetów.
 
### <a name="tlsssl-protocol-versions"></a>Wersje protokołu TLS/SSL

* Protokoły SSL 2,0 i 3,0 są domyślnie wyłączone dla wszystkich bram aplikacji. Te wersje protokołu nie są konfigurowalne.
* Niestandardowe zasady protokołu TLS umożliwiają wybranie jednego z trzech następujących protokołów jako minimalnej wersji protokołu TLS dla bramy: TLSv1_0, TLSv1_1 i TLSv1_2.
* Jeśli nie zdefiniowano żadnych zasad TLS, wszystkie trzy protokoły (TLSv1_0, TLSv1_1 i TLSv1_2) są włączone.

### <a name="cipher-suites"></a>Mechanizmy szyfrowania

Application Gateway obsługuje następujące mechanizmy szyfrowania, z których można wybrać zasady niestandardowe. Uporządkowanie mechanizmów szyfrowania określa kolejność priorytetów podczas negocjacji TLS.


- TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
- TLS_DHE_RSA_WITH_AES_256_GCM_SHA384
- TLS_DHE_RSA_WITH_AES_128_GCM_SHA256
- TLS_DHE_RSA_WITH_AES_256_CBC_SHA
- TLS_DHE_RSA_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_AES_256_GCM_SHA384
- TLS_RSA_WITH_AES_128_GCM_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA256
- TLS_RSA_WITH_AES_128_CBC_SHA256
- TLS_RSA_WITH_AES_256_CBC_SHA
- TLS_RSA_WITH_AES_128_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256
- TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA
- TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA256
- TLS_DHE_DSS_WITH_AES_256_CBC_SHA
- TLS_DHE_DSS_WITH_AES_128_CBC_SHA
- TLS_RSA_WITH_3DES_EDE_CBC_SHA
- TLS_DHE_DSS_WITH_3DES_EDE_CBC_SHA

> [!NOTE]
> Mechanizmy szyfrowania TLS używane na potrzeby połączenia są również oparte na typie używanego certyfikatu. W przypadku połączeń między klientem a bramą aplikacji używane mechanizmy szyfrowania są oparte na typie certyfikatów serwera w odbiorniku bramy aplikacji. W usłudze Application Gateway do połączeń puli zaplecza używane mechanizmy szyfrowania są oparte na typie certyfikatów serwera na serwerach puli zaplecza.

## <a name="known-issue"></a>Znany problem
Application Gateway v2 nie obsługuje obecnie następujących szyfrów:
- DHE-RSA-AES128-GCM-SHA256
- DHE-RSA-AES128-SHA
- DHE-RSA-AES256-GCM-SHA384
- DHE-RSA-AES256-SHA
- DHE-DSS-AES128-SHA256
- DHE-DSS-AES128-SHA
- DHE-DSS-AES256-SHA256
- DHE-DSS-AES256-SHA

## <a name="next-steps"></a>Następne kroki

Jeśli chcesz dowiedzieć się, jak skonfigurować zasady protokołu TLS, zobacz [Konfigurowanie wersji zasad TLS i mechanizmów szyfrowania na Application Gateway](application-gateway-configure-ssl-policy-powershell.md).
