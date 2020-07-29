---
title: Azure front-drzwi — monitorowanie kondycji zaplecza | Microsoft Docs
description: Ten artykuł pomaga zrozumieć, w jaki sposób usługa Azure front-drzwi monitoruje kondycję zasobie
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: sharadag
ms.openlocfilehash: e2e656c395f1a31c1f5ebbd46d5a18a046f854f7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79471578"
---
# <a name="health-probes"></a>Sondy kondycji

Aby określić kondycję i bliskość każdego zaplecza z danego środowiska z przodu, każde środowisko tylne drzwi okresowo wysyła syntetyczne żądanie HTTP/HTTPS do każdego ze skonfigurowanych zaplecza. Usługa Front Door następnie używa odpowiedzi z tych sond, aby określić „najlepsze” zaplecza, do których powinna kierować rzeczywiste żądania klientów. 

> [!WARNING]
> Ze względu na to, że drzwi tylne mają wiele środowisk brzegowych, sonda kondycji żąda dużej ilości danych od 25 żądań co minutę do 1200 żądań na minutę, w zależności od skonfigurowanej częstotliwości sondowania kondycji. Domyślna częstotliwość sondy wynosząca 30 sekund, objętość sondy w zasobie powinna wynosić około 200 żądań na minutę.

## <a name="supported-protocols"></a>Obsługiwane protokoły

Drzwi tylne obsługują wysyłanie sond za pośrednictwem protokołów HTTP i HTTPS. Te sondy są wysyłane przy użyciu tych samych portów TCP co skonfigurowane na potrzeby routingu żądań klientów i nie można ich zmienić.

## <a name="supported-http-methods-for-health-probes"></a>Obsługiwane metody HTTP dla sond kondycji

Drzwi tylne obsługują następujące metody HTTP do wysyłania sond kondycji:

1. **Pobierz:** Metoda GET oznacza pobieranie dowolnych informacji (w formie jednostki) identyfikowanych przez identyfikator URI żądania.
2. **Nagłówek:** Metoda główna jest taka sama, aby można było uzyskać z tą różnicą, że serwer nie może zwrócić treści komunikatu w odpowiedzi. W przypadku nowych profilów z przodu, domyślnie Metoda sondowania jest ustawiana jako główna.

> [!NOTE]
> W celu obniżenia obciążenia i ponoszenia kosztów na potrzeby punktów końcowych zaleca się używanie żądań głównych dla sond kondycji.

## <a name="health-probe-responses"></a>Odpowiedzi sondy kondycji

| Odpowiedzi  | Opis | 
| ------------- | ------------- |
| Określanie kondycji  |  Kod stanu 200 OK wskazuje, że zaplecze jest w dobrej kondycji. Wszystko inne jest uznawane za niepowodzenie. Jeśli z jakiegoś powodu (w tym awarii sieci) nie odebrano prawidłowej odpowiedzi HTTP dla sondy, sonda jest traktowana jako błąd.|
| Pomiar opóźnienia  | Opóźnienie to czas zegara ściany mierzony od momentu natychmiast po wysłaniu żądania sondy do momentu otrzymania ostatniego bajtu odpowiedzi. Dla każdego żądania używane jest nowe połączenie TCP, więc pomiary nie są wliczane do zachodzących istniejących połączeń ciepłej.  |

## <a name="how-front-door-determines-backend-health"></a>Jak drzwi z przodu określają kondycję zaplecza

Drzwi frontonu platformy Azure używają tego samego dwuetapowego procesu dla wszystkich algorytmów w celu określenia kondycji.

1. Wyklucz wyłączone punkty końcowe.

2. Wyklucz nadkończenie, które mają Błędy sond kondycji:
    * Aby to zrobić, należy przejrzeć odpowiedzi z ostatnich _n_ sondy kondycji. Jeśli co najmniej _x_ jest w dobrej kondycji, zaplecze jest traktowana jako dobra kondycja.

    * _n_ jest skonfigurowany przez zmianę właściwości SampleSize w ustawieniach równoważenia obciążenia.

    * wartość _x_ jest konfigurowana przez zmianę właściwości SuccessfulSamplesRequired w ustawieniach równoważenia obciążenia.

3. Poza zestawem nieprawidłowych punktów końcowych w puli zaplecza z przodu są dodatkowo stosowane i utrzymywane opóźnienie (czas błądzenia) dla każdego zaplecza.


## <a name="complete-health-probe-failure"></a>Ukończ błąd sondy kondycji

Jeśli sondy kondycji dla każdego zaplecza w puli zaplecza nie powiedzie się, a następnie drzwi przede wszystkim są w dobrej kondycji i przekierowują ruch w ramach rozkładu okrężnego między wszystkimi nimi.

Gdy dowolna zaplecze powróci do stanu dobrej kondycji, następnie drzwi do przodu spowodują wznowienie normalnego algorytmu równoważenia obciążenia.

## <a name="disabling-health-probes"></a>Wyłączanie sond kondycji

Jeśli masz pojedyncze zaplecze w puli zaplecza, możesz wyłączyć sondy kondycji zmniejszające obciążenie zaplecze aplikacji. Nawet jeśli istnieje wiele zapleczy w puli zaplecza, ale tylko jeden z nich jest w stanie włączony, można wyłączyć sondy kondycji.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [utworzyć usługę Front Door](quickstart-create-front-door.md).
- Dowiedz się, [jak działa usługa Front Door](front-door-routing-architecture.md).
