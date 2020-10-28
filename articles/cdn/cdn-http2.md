---
title: Obsługa protokołu HTTP/2 w Azure CDN | Microsoft Docs
description: Usługa Azure Content Delivery Network obsługuje protokół HTTP/2, który ma zalety protokołu HTTP/1, takie jak multipleksowanie & współbieżności, kompresji nagłówka i zależności strumienia.
services: cdn
documentationcenter: ''
author: lichard
manager: erikre
editor: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2017
ms.author: ril
ms.openlocfilehash: d4ed04185da251d9042f6b54a192d0e49ef20ede
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92778538"
---
# <a name="http2-support-in-azure-cdn"></a>Obsługa protokołu HTTP/2 w Azure CDN

HTTP/2 to główna wersja protokołu HTTP/1.1 \. Zapewnia to szybszą wydajność sieci Web, krótszy czas odpowiedzi i ulepszone środowisko użytkownika przy zachowaniu znanych metod HTTP, kodów stanu i semantyki. Mimo że protokół HTTP/2 jest przeznaczony do pracy z protokołami HTTP i HTTPS, wiele przeglądarek sieci Web klienta obsługuje tylko protokół HTTP/2 za pośrednictwem protokołu TLS.

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

## <a name="enabling-http2-support-in-azure-cdn"></a>Włączanie obsługi protokołu HTTP/2 w Azure CDN

Obecnie obsługa protokołu HTTP/2 jest aktywna dla wszystkich profilów Azure CDN. Od klientów nie są wymagane żadne dalsze działania.

## <a name="next-steps"></a>Następne kroki

Aby zobaczyć zalety protokołu HTTP/2 w działaniu, zobacz [ten pokaz z Akamai](https://http2.akamai.com/demo).

Więcej informacji na temat protokołu HTTP/2 można znaleźć w następujących zasobach:

*   [Strona główna specyfikacji protokołu HTTP/2](https://http2.github.io/)
*   [Oficjalne pytania dotyczące protokołu HTTP/2](https://http2.github.io/faq/)
*   [Informacje dotyczące protokołu HTTP/2 Akamai](https://http2.akamai.com/)

Aby dowiedzieć się więcej o dostępnych funkcjach Azure CDN, zobacz [omówienie Azure CDN](./cdn-overview.md).