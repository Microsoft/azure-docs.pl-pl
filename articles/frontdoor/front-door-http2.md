---
title: Azure Front-HTTP2 support | Microsoft Docs
description: Ten artykuł ułatwia zapoznanie się z obsługą protokołu HTTP/2 na platformie Azure front-drzwi
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 20d45f5966aca3df89e17e03aa6120a4ddf5c5b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91448701"
---
# <a name="http2-support-in-azure-front-door"></a>Obsługa protokołu HTTP/2 na platformie Azure — drzwi

Obecnie obsługa protokołu HTTP/2 jest aktywna w przypadku wszystkich konfiguracji z przodu platformy Azure. Od klientów nie są wymagane żadne dalsze działania.

HTTP/2 to główna wersja protokołu HTTP/1.1, która zapewnia szybsze działanie sieci Web przez skrócenie czasu odpowiedzi. Protokół HTTP/2 jest wykonywany przez utrzymywanie znanych metod HTTP, kodów stanu i semantyki w celu poprawy środowiska użytkownika. Mimo że protokół HTTP/2 jest przeznaczony do pracy z protokołami HTTP i HTTPS, wiele przeglądarek sieci Web klienta obsługuje tylko protokół HTTP/2 dla Transport Layer Security (TLS).

> [!NOTE]
> Obsługa protokołu HTTP/2 jest dostępna tylko dla żądań od klientów do przednich drzwi. Komunikacja z tylnych drzwi do zaplecza w puli zaplecza odbywa się za pośrednictwem protokołu HTTP/1.1. 

### <a name="http2-benefits"></a>Korzyści z protokołu HTTP/2

Zalety protokołu HTTP/2 obejmują:

*   **Multipleksowanie i współbieżność**

    Przy użyciu protokołu HTTP 1,1 wykonanie wielu żądań zasobów wymaga wielu połączeń TCP, a każde połączenie ma powiązane z nim obciążenie. Protokół HTTP/2 umożliwia żądanie wielu zasobów na jednym połączeniu TCP.

*   **Kompresowanie nagłówka**

    Dzięki kompresji nagłówków HTTP dla obsługiwanych zasobów czas w sieci jest zmniejszany znacznie.

*   **Zależności strumienia**

    Zależności strumienia umożliwiają klientowi wskazanie serwera, którego zasoby mają priorytet.


## <a name="http2-browser-support"></a>Obsługa przeglądarki HTTP/2

Wszystkie główne przeglądarki wdrożyły obsługę protokołu HTTP/2 w ich bieżących wersjach. Nieobsługiwane przeglądarki automatycznie powracają do protokołu HTTP/1.1.

|Przeglądarka|Minimalna wersja|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="next-steps"></a>Następne kroki

Więcej informacji na temat protokołu HTTP/2 można znaleźć w następujących zasobach:

- [Strona główna specyfikacji protokołu HTTP/2](https://http2.github.io/)
- [Oficjalne pytania dotyczące protokołu HTTP/2](https://http2.github.io/faq/)
- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
