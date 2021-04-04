---
title: Rozwiązywanie problemów z usługą Azure Cache for Redis po stronie klienta
description: Dowiedz się, jak rozwiązywać typowe problemy po stronie klienta w usłudze Azure cache for Redis, takie jak obciążenie pamięci klienta Redis, Seria ruchu, wysoki procesor CPU, ograniczona przepustowość, duże żądania lub duża wielkość odpowiedzi.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: 122c96c95aea794fbba9cab8a9a5b867f9f34b48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88008971"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Rozwiązywanie problemów z usługą Azure Cache for Redis po stronie klienta

W tej części omówiono Rozwiązywanie problemów występujących w przypadku wystąpienia klienta Redis używanego przez aplikację.

- [Wykorzystanie pamięci na kliencie Redis](#memory-pressure-on-redis-client)
- [Ruch na rozerwanie](#traffic-burst)
- [Duże użycie procesora CPU przez klienta](#high-client-cpu-usage)
- [Ograniczenie przepustowości po stronie klienta](#client-side-bandwidth-limitation)
- [Duże żądanie lub wielkość odpowiedzi](#large-request-or-response-size)

## <a name="memory-pressure-on-redis-client"></a>Wykorzystanie pamięci na kliencie Redis

Wykorzystanie pamięci na komputerze klienckim prowadzi do wszystkich rodzajów problemów z wydajnością, które mogą opóźniać przetwarzanie odpowiedzi z pamięci podręcznej. Po trafieniu pamięci system może być stroną danych na dysku. _Awaria tej strony_ powoduje znaczne spowolnienie działania systemu.

Aby wykryć wykorzystanie pamięci na kliencie:

- Monitoruj użycie pamięci na maszynie, aby upewnić się, że nie przekracza ona dostępnej pamięci.
- Monitoruj `Page Faults/Sec` licznik wydajności klienta. Podczas normalnej pracy większość systemów zawiera pewne błędy stron. Skoki błędów stron odpowiadających limitom czasu żądania mogą wskazywać na wykorzystanie pamięci.

Duże wykorzystanie pamięci na kliencie można ograniczyć na kilka sposobów:

- Dig się ze wzorcami użycia pamięci, aby zmniejszyć zużycie pamięci przez klienta.
- Uaktualnij maszynę wirtualną klienta do większego rozmiaru, używając większej ilości pamięci.

## <a name="traffic-burst"></a>Ruch na rozerwanie

Rozerwania ruchu połączonego z słabymi `ThreadPool` ustawieniami mogą spowodować opóźnienia w przetwarzaniu danych, które zostały już wysłane przez serwer Redis, ale nie zostały jeszcze wykorzystane po stronie klienta.

Monitoruj jak `ThreadPool` dane statystyczne zmieniają się w miarę upływu czasu przy użyciu [przykładu `ThreadPoolLogger` ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Możesz użyć  `TimeoutException` komunikatów z stackexchange. Redis, jak pokazano poniżej, aby dokładniej zbadać:

```output
    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)
```

W powyższym wyjątku występuje kilka interesujących problemów:

- Zwróć uwagę, że w `IOCP` sekcji i `WORKER` sekcji masz `Busy` wartość większą niż `Min` wartość. Różnica polega na tym, że Twoje `ThreadPool` Ustawienia wymagają dostosowania.
- Możesz również zobaczyć `in: 64221` . Ta wartość wskazuje, że 64 211 bajtów została odebrana w warstwie gniazda jądra klienta, ale nie została odczytana przez aplikację. Ta różnica zwykle oznacza, że aplikacja (na przykład StackExchange. Redis) nie odczytuje danych z sieci tak szybko, jak serwer wysyła go do Ciebie.

Można [skonfigurować `ThreadPool` Ustawienia](cache-management-faq.md#important-details-about-threadpool-growth) , aby upewnić się, że Pula wątków szybko skaluje się w ramach scenariuszy z serii.

## <a name="high-client-cpu-usage"></a>Duże użycie procesora CPU przez klienta

Wysokie użycie procesora CPU przez klienta wskazuje, że system nie może zachować pracy z pożądanymi zadaniami. Mimo że pamięć podręczna przesłała szybko odpowiedź, klient może nie przetwarzać odpowiedzi w odpowiednim czasie.

Monitoruj użycie procesora CPU w całym systemie przy użyciu metryk dostępnych w Azure Portal lub liczników wydajności na komputerze. Należy zachować ostrożność, aby nie monitorować procesora *procesów* , ponieważ pojedynczy proces może mieć niskie użycie procesora CPU, ale procesor CPU może być wysoki. Obserwuj w przypadku szczytów użycia procesora, które są zgodne z limitami czasu. Wysoki procesor CPU może również spowodować wysokie `in: XXX` wartości w `TimeoutException` komunikatach o błędach zgodnie z opisem w sekcji [Seria ruchu](#traffic-burst) .

> [!NOTE]
> StackExchange. Redis 1.1.603 i nowsze zawierają `local-cpu` metrykę w `TimeoutException` komunikatach o błędach. Upewnij się, że używasz najnowszej wersji [pakietu NuGet stackexchange. Redis](https://www.nuget.org/packages/StackExchange.Redis/). Błędy są ciągle naprawiane w kodzie, aby zwiększyć jego niezawodność, tak aby Najnowsza wersja była ważna.
>

Aby ograniczyć użycie procesora CPU przez klienta:

- Sprawdź, co jest przyczyną wzrostu procesora CPU.
- Uaktualnij klienta do większego rozmiaru maszyny wirtualnej o większej pojemności procesora.

## <a name="client-side-bandwidth-limitation"></a>Ograniczenie przepustowości po stronie klienta

W zależności od architektury komputerów klienckich mogą one mieć ograniczenia dotyczące liczby dostępnych przepustowości sieci. Jeśli klient przekroczy dostępną przepustowość przez przeładowanie pojemności sieci, dane nie są przetwarzane po stronie klienta tak szybko, jak serwer wysyła go. Ta sytuacja może prowadzić do przekroczenia limitu czasu.

Monitoruj sposób zmiany użycia przepustowości z biegiem czasu przy użyciu [przykładu `BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Ten kod może nie działać prawidłowo w niektórych środowiskach z ograniczonymi uprawnieniami (takimi jak witryny sieci Web systemu Azure).

Aby ograniczyć użycie przepustowości sieci lub zwiększyć rozmiar maszyny wirtualnej klienta do jednego z większą pojemnością sieci.

## <a name="large-request-or-response-size"></a>Duże żądanie lub wielkość odpowiedzi

Duże żądanie/odpowiedź może spowodować przekroczenie limitu czasu. Załóżmy na przykład, że wartość limitu czasu skonfigurowana na kliencie wynosi 1 s. Aplikacja żąda dwóch kluczy (na przykład "A" i "B") w tym samym czasie (przy użyciu tego samego fizycznego połączenia sieciowego). Większość klientów obsługuje żądanie "przetwarzanie potokowe", gdzie oba żądania "A" i "B" są wysyłane jeden po drugim bez oczekiwania na ich odpowiedzi. Serwer wysyła odpowiedzi z powrotem w tej samej kolejności. Jeśli odpowiedź "A" jest duża, może wzmocnić większość czasu na potrzeby kolejnych żądań.

W poniższym przykładzie żądanie "A" i "B" są wysyłane szybko do serwera. Serwer uruchamia szybkie wysyłanie odpowiedzi "A" i "B". Ze względu na czas transferu danych odpowiedź "B" musi odczekać przed czasem odpowiedzi "A", mimo że serwer szybko odpowiedział.

```console
|-------- 1 Second Timeout (A)----------|
|-Request A-|
     |-------- 1 Second Timeout (B) ----------|
     |-Request B-|
            |- Read Response A --------|
                                       |- Read Response B-| (**TIMEOUT**)
```

To żądanie/odpowiedź to trudne do pomiaru. Możesz instrumentować kod klienta, aby śledzić duże żądania i odpowiedzi.

Rozwiązania dla dużych rozmiarów odpowiedzi są różne, ale obejmują:

1. Zoptymalizuj aplikację pod kątem dużej liczby małych wartości, a nie kilku dużych wartości.
    - Preferowane rozwiązanie polega na rozdzieleniu danych na powiązane mniejsze wartości.
    - Zobacz, [co to jest zakres rozmiaru wartości idealny dla Redis? Czy 100 KB zbyt duże?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) Aby uzyskać szczegółowe informacje na temat tego, dlaczego są zalecane mniejsze wartości.
1. Zwiększenie rozmiaru maszyny wirtualnej w celu uzyskania większej przepustowości
    - Większa przepustowość na maszynie wirtualnej klienta lub na serwerze może skrócić czas transferu danych w celu uzyskania większych odpowiedzi.
    - Porównaj bieżące użycie sieci na obu maszynach z limitami bieżącego rozmiaru maszyny wirtualnej. Większa przepustowość tylko na serwerze lub tylko na kliencie może być za mała.
1. Zwiększ liczbę obiektów połączeń używanych przez aplikację.
    - Użyj podejścia okrężnego, aby wykonywać żądania w różnych obiektach połączenia.

## <a name="additional-information"></a>Dodatkowe informacje

- [Rozwiązywanie problemów z usługą Azure Cache for Redis po stronie serwera](cache-troubleshoot-server.md)
- [Jak można sprawdzić i przetestować wydajność mojej pamięci podręcznej?](cache-management-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
