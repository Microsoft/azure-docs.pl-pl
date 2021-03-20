---
title: Skalowalność i wydajność — Personalizowanie
titleSuffix: Azure Cognitive Services
description: 'W przypadku witryn sieci Web o wysokiej wydajności i dużych ruchu istnieją dwa główne czynniki, które należy wziąć pod uwagę przy użyciu programu Personalizacja w celu zapewnienia skalowalności i wydajności: opóźnienia i uczenie.'
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/24/2019
ms.openlocfilehash: 52d22fce64a26267e73cb9a1df8614129bb96c3e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87132691"
---
# <a name="scalability-and-performance"></a>Skalowalność i wydajność

W przypadku witryn sieci Web o wysokiej wydajności i dużych ruchu istnieją dwa główne czynniki, które należy wziąć pod uwagę w celu zapewnienia skalowalności i wydajności:

* Zachowywanie małych opóźnień podczas wykonywania wywołań interfejsu API rangi
* Zapewnianie, że przepływność szkoleń jest zachowywana przy użyciu danych wejściowych zdarzeń

Personalizacja może szybko zwracać rangę z większością czasu trwania wywołań, która jest przeznaczona do komunikacji za pomocą interfejsu API REST. Platforma Azure automatycznie skaluje możliwość szybkiego reagowania na żądania.

##  <a name="low-latency-scenarios"></a>Scenariusze o małym opóźnieniu

Niektóre aplikacje wymagają małych opóźnień podczas zwracania rangi. Wymagane są małe opóźnienia:

* Aby uniemożliwić użytkownikowi oczekiwanie na zauważalny czas przed wyświetleniem sklasyfikowanej zawartości.
* Aby ułatwić serwerowi, który ma wyższy ruch, unikaj nawiązywania nieprawidłowych czasu obliczeń i połączeń sieciowych.


## <a name="scalability-and-training-throughput"></a>Skalowalność i przepływność szkoleń

Personalizacja działa, aktualizując model, który jest ponownie przemieszczony na podstawie komunikatów wysyłanych asynchronicznie przez personalizację po rangi i nagrodach interfejsów API. Te komunikaty są wysyłane przy użyciu centrum zdarzeń platformy Azure dla aplikacji.

 Jest mało prawdopodobne, że większość aplikacji osiągnie maksymalną przepustowość i uczenie się dla personalizacji. Osiągnięcie tej wartości maksymalnej nie spowoduje spowolnienia działania aplikacji. spowoduje to, że kolejki centrum zdarzeń są wprowadzane wewnętrznie w trybie szybszym niż można je wyczyścić.

## <a name="how-to-estimate-your-throughput-requirements"></a>Jak oszacować wymagania dotyczące przepływności

* Oszacuj średnią liczbę bajtów na zdarzenie klasyfikacji, dodając długości dokumentów JSON kontekstu i akcji.
* Podziel baza na sekundę przez ten szacowany średnią liczbę bajtów.

Na przykład jeśli średni ładunek ma 500 funkcji, a każdy z nich jest szacowany 20 znaków, każde zdarzenie jest około 10 KB. W przypadku tych szacunków 20 000 000/10 000 = 2 000 Events/s, które dotyczą zdarzeń 173 000 000 Events/dzień. 

Jeśli zbliżasz się do tych limitów, skontaktuj się z naszym zespołem pomocy technicznej, aby uzyskać poradę dotyczącą architektury.

## <a name="next-steps"></a>Następne kroki

[Utwórz i skonfiguruj personalizację](how-to-settings.md).