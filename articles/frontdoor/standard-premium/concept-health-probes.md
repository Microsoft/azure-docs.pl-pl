---
title: Monitorowanie sondy kondycji Standard/Premium systemu Azure (wersja zapoznawcza)
description: Ten artykuł pomaga zrozumieć, w jaki sposób platforma Azure front-drzwi monitoruje kondycję zaplecza.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 30a8208babab2991c9d9e86cc419ac50e1530d7b
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101100088"
---
# <a name="azure-front-door-standardpremium-preview-health-probe-monitoring"></a>Monitorowanie sondy kondycji Standard/Premium systemu Azure (wersja zapoznawcza)

> [!Note]
> Ta dokumentacja dotyczy platformy Azure — Standard/Premium (wersja zapoznawcza). Szukasz informacji na temat zewnętrznych drzwi platformy Azure? [Tutaj](../front-door-overview.md)Wyświetl.

Drzwi frontonu platformy Azure okresowo wysyłają żądanie HTTP lub HTTPS do każdego z zaplecza. Te żądania umożliwiają usłudze Azure Front drzwiczkom ustalenie kondycji każdego punktu końcowego w puli zaplecza. Przede wszystkim są używane te odpowiedzi z sondy w celu określenia "najlepszych" zasobów zaplecza, które umożliwiają kierowanie żądań klientów. 

> [!WARNING]
> Ze względu na to, że drzwi z przodu zawierają wiele środowisk brzegowych, ilość sondy kondycji dla zakończyeń może być dość duża od 25 żądań co minutę do 1200 żądań na minutę, w zależności od skonfigurowanej częstotliwości sondowania kondycji. Domyślna częstotliwość sondy wynosząca 30 sekund, objętość sondy w zasobie powinna wynosić około 200 żądań na minutę.

## <a name="supported-protocols"></a>Obsługiwane protokoły

Drzwi tylne obsługują wysyłanie sond za pośrednictwem protokołów HTTP i HTTPS. Te sondy są wysyłane przy użyciu tych samych portów TCP co skonfigurowane na potrzeby routingu żądań klientów i nie można ich zmienić.

## <a name="supported-http-methods-for-health-probes"></a>Obsługiwane metody HTTP dla sond kondycji

Drzwi tylne obsługują następujące metody HTTP do wysyłania sond kondycji:

* **Pobierz:** Metoda GET oznacza pobieranie dowolnych informacji (w formie jednostki) identyfikowanych przez identyfikator URI żądania.
* **Nagłówek:** Metoda główna jest taka sama, aby można było uzyskać z tą różnicą, że serwer nie może zwrócić treści komunikatu w odpowiedzi. W przypadku nowych profilów z przodu, domyślnie Metoda sondowania jest ustawiana jako główna.

> [!NOTE]
> W celu obniżenia obciążenia i ponoszenia kosztów na potrzeby punktów końcowych zaleca się używanie żądań głównych dla sond kondycji.

## <a name="health-probe-responses"></a>Odpowiedzi sondy kondycji

| Odpowiedzi  | Opis | 
| ------------- | ------------- |
| Określanie kondycji  |  Kod stanu 200 OK wskazuje, że zaplecze jest w dobrej kondycji. Wszystko inne jest uznawane za niepowodzenie. Jeśli z jakiegoś powodu (w tym awarii sieci) nie otrzymasz prawidłowej odpowiedzi HTTP dla sondy, sonda jest traktowana jako błąd.|
| Pomiar opóźnienia  | Opóźnienie to czas zegara ściany mierzony od momentu natychmiast po wysłaniu żądania sondy do momentu otrzymania ostatniego bajtu odpowiedzi. Korzystamy z nowego połączenia TCP dla każdego żądania, więc ta wartość nie jest rozliczania na koniec z istniejącymi połączeniami ciepłymi.  |

## <a name="how-front-door-determines-backend-health"></a>Jak drzwi z przodu określają kondycję zaplecza

Drzwi frontonu platformy Azure używają tego samego dwuetapowego procesu dla wszystkich algorytmów w celu określenia kondycji.

1. Wyklucz wyłączone punkty końcowe.

1. Wyklucz nadkończenie, które mają Błędy sond kondycji:

    * Aby to zrobić, należy przejrzeć odpowiedzi z ostatnich _n_ sondy kondycji. Jeśli co najmniej _x_ jest w dobrej kondycji, zaplecze jest traktowana jako dobra kondycja.

    * _n_ jest skonfigurowany przez zmianę właściwości SampleSize w ustawieniach równoważenia obciążenia.

    * wartość _x_ jest konfigurowana przez zmianę właściwości SuccessfulSamplesRequired w ustawieniach równoważenia obciążenia.

1. W przypadku zestawów w dobrej kondycji w puli zaplecza z przodu są dodatkowo stosowane i utrzymywane opóźnienie (czas błądzenia) dla każdego zaplecza.


## <a name="complete-health-probe-failure"></a>Ukończ błąd sondy kondycji

Jeśli sondy kondycji dla każdego zaplecza w puli zaplecza nie powiedzie się, a następnie drzwi przede wszystkim są w dobrej kondycji i przekierowują ruch w ramach rozkładu okrężnego między wszystkimi nimi.

Gdy dowolna zaplecze powróci do stanu dobrej kondycji, następnie drzwi do przodu spowodują wznowienie normalnego algorytmu równoważenia obciążenia.

## <a name="disabling-health-probes"></a>Wyłączanie sond kondycji

W przypadku posiadania pojedynczego zaplecza w puli zaplecza lub tylko jednego zaplecza w puli zaplecza można wyłączyć sondy kondycji. Dzięki temu można zmniejszyć obciążenie zaplecza aplikacji.

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak utworzyć warstwę przednią/standardową](create-front-door-portal.md).
