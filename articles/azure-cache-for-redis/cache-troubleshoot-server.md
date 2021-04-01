---
title: Rozwiązywanie problemów z usługą Azure Cache for Redis po stronie serwera
description: Dowiedz się, jak rozwiązywać typowe problemy po stronie serwera w usłudze Azure cache for Redis, takie jak wykorzystanie pamięci, duże użycie procesora, długotrwałe polecenia lub ograniczenia przepustowości.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: 12d78846f5892e71388de6e6e76b868f9b14d4de
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88008920"
---
# <a name="troubleshoot-azure-cache-for-redis-server-side-issues"></a>Rozwiązywanie problemów z usługą Azure Cache for Redis po stronie serwera

W tej części omówiono Rozwiązywanie problemów występujących w przypadku wystąpienia w pamięci podręcznej platformy Azure dla Redis lub maszyn wirtualnych, które je obsługują.

- [Wykorzystanie pamięci na serwerze Redis](#memory-pressure-on-redis-server)
- [Duże użycie procesora CPU lub obciążenie serwera](#high-cpu-usage-or-server-load)
- [Długotrwałe polecenia](#long-running-commands)
- [Ograniczenie przepustowości po stronie serwera](#server-side-bandwidth-limitation)

> [!NOTE]
> Kilka kroków rozwiązywania problemów zawartych w tym przewodniku zawiera instrukcje dotyczące uruchamiania poleceń Redis i monitorowania różnych metryk wydajności. Aby uzyskać więcej informacji i instrukcje, zapoznaj się z artykułami w sekcji [Informacje dodatkowe](#additional-information) .
>

## <a name="memory-pressure-on-redis-server"></a>Wykorzystanie pamięci na serwerze Redis

Wykorzystanie pamięci po stronie serwera prowadzi do wszystkich rodzajów problemów z wydajnością, które mogą opóźniać przetwarzanie żądań. Po trafieniu pamięci system może być stroną danych na dysku. _Awaria tej strony_ powoduje znaczne spowolnienie działania systemu. Istnieje kilka możliwych przyczyn tego ciśnienia pamięci:

- Pamięć podręczna jest zapełniona danymi zbliżoną do maksymalnej pojemności.
- Redis widzi wysoką fragmentację pamięci. Ta fragmentacja jest najczęściej spowodowana przechowywaniem dużych obiektów, ponieważ Redis jest zoptymalizowany pod kątem małych obiektów.

Redis udostępnia dwie statystyki za pomocą polecenia [info](https://redis.io/commands/info) , które mogą pomóc w zidentyfikowaniu tego problemu: "used_memory" i "used_memory_rss". Możesz [wyświetlić te metryki](cache-how-to-monitor.md#view-metrics-with-azure-monitor) przy użyciu portalu.

Istnieje kilka możliwych zmian, które mogą pomóc w poprawnym użyciu pamięci:

- [Skonfiguruj zasady pamięci](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) i ustaw czasy wygaśnięcia kluczy. Te zasady mogą nie być wystarczające w przypadku fragmentacji.
- [Skonfiguruj wartość zarezerwowaną maxmemory](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) , która jest wystarczająco duża, aby zrekompensować fragmentację pamięci.
- Podziel duże obiekty w pamięci podręcznej na mniejsze obiekty pokrewne.
- [Utwórz alerty](cache-how-to-monitor.md#alerts) dla metryk, takich jak użyta pamięć, aby uzyskiwać wczesne powiadomienia o potencjalnym wpływie.
- [Skalowanie](cache-how-to-scale.md) do większego rozmiaru pamięci podręcznej o większej pojemności pamięci.

## <a name="high-cpu-usage-or-server-load"></a>Duże użycie procesora CPU lub obciążenie serwera

Duże obciążenie serwera lub użycie procesora CPU oznacza, że serwer nie może przetwarzać żądań w odpowiednim czasie. Serwer może reagować na odpowiedź i nie może utrzymywać się przy użyciu stawek żądania.

[Monitoruj metryki](cache-how-to-monitor.md#view-metrics-with-azure-monitor) , takie jak obciążenie procesora lub serwera. Obserwuj w przypadku szczytów użycia procesora, które są zgodne z limitami czasu.

Istnieje kilka zmian, które można zmniejszyć, aby zmniejszyć obciążenie serwera:

- Zbadaj, co powoduje, że procesor CPU przekracza, jak [długo uruchomione polecenia](#long-running-commands) zanotowano poniżej lub błąd strony z powodu wysokiego poziomu wykorzystania pamięci.
- [Utwórz alerty](cache-how-to-monitor.md#alerts) na podstawie metryk, takich jak obciążenie procesora CPU lub serwera, aby wcześniej otrzymywać powiadomienia o potencjalnym wpływie.
- [Skalowanie](cache-how-to-scale.md) do większego rozmiaru pamięci podręcznej o większej pojemności procesora CPU.

## <a name="long-running-commands"></a>Długotrwałe polecenia

Niektóre polecenia Redis są droższe do wykonania niż inne. [Dokumentacja poleceń Redis](https://redis.io/commands) pokazuje złożoność każdego polecenia. Ponieważ przetwarzanie polecenia Redis jest jednowątkowe, polecenie, które wymaga czasu uruchomienia, będzie blokować wszystkie inne, które są po nim. Należy przejrzeć polecenia, które są wystawiane na serwerze Redis, aby zrozumieć ich wpływ na wydajność. Na przykład [klucze](https://redis.io/commands/keys) polecenia są często używane bez znajomości, że jest to operacja o (N). Możesz uniknąć używania [skanowania](https://redis.io/commands/scan) , aby zmniejszyć liczbę procesorów CPU.

Za pomocą polecenia [SLOWLOG](https://redis.io/commands/slowlog) można mierzyć kosztowne polecenia wykonywane względem serwera.

## <a name="server-side-bandwidth-limitation"></a>Ograniczenie przepustowości po stronie serwera

Różne rozmiary pamięci podręcznej mają różne pojemności sieci. Jeśli serwer przekracza dostępną przepustowość, dane nie będą wysyłane do klienta jak najszybciej. Żądania klientów mogą przekroczyć limit czasu, ponieważ serwer nie może szybko wypychania danych do klienta.

Metryki "Odczyt pamięci podręcznej" i "Zapisywanie pamięci podręcznej" mogą służyć do sprawdzenia, ile przepustowości po stronie serwera jest używana. [Te metryki można wyświetlić](cache-how-to-monitor.md#view-metrics-with-azure-monitor) w portalu.

Aby wyeliminować sytuacje, w których wykorzystanie przepustowości sieci zbliża się do maksymalnej pojemności:

- Zmień zachowanie wywołania klienta, aby zmniejszyć zapotrzebowanie na sieć.
- [Utwórz alerty](cache-how-to-monitor.md#alerts) dla metryk, takich jak odczyt z pamięci podręcznej lub zapis w pamięci podręcznej, aby uzyskiwać wczesne powiadomienia o potencjalnym wpływie.
- [Skalowanie](cache-how-to-scale.md) do większego rozmiaru pamięci podręcznej o większej pojemności sieci.

## <a name="additional-information"></a>Dodatkowe informacje

- [Rozwiązywanie problemów z usługą Azure Cache for Redis po stronie klienta](cache-troubleshoot-client.md)
- [Wybór odpowiedniej warstwy](cache-overview.md#choosing-the-right-tier)
- [Jak można sprawdzić i przetestować wydajność mojej pamięci podręcznej?](cache-management-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Jak monitorować usługę Azure cache for Redis](cache-how-to-monitor.md)
- [Jak można uruchomić polecenia Redis?](cache-development-faq.md#how-can-i-run-redis-commands)
