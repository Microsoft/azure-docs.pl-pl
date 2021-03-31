---
title: Usługa Azure cache for Redis — często zadawane pytania
description: Poznaj odpowiedzi na często zadawane pytania, które ułatwiają zarządzanie usługą Azure cache for Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/06/2020
ms.openlocfilehash: 15c7ed4ca9d04e4bb314eea8b92bef749d2369b1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92537664"
---
# <a name="azure-cache-for-redis-management-faqs"></a>Usługa Azure cache for Redis — często zadawane pytania
Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące zarządzania usługą Azure cache for Redis.

## <a name="common-questions-and-answers"></a>Typowe pytania i odpowiedzi
W tej sekcji omówiono następujące często zadawane pytania:

* [Kiedy należy włączyć port bez protokołu TLS/SSL na potrzeby łączenia się z usługą Redis?](#when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis)
* [Jakie są najlepsze rozwiązania w zakresie produkcji?](#what-are-some-production-best-practices)
* [Jakie kwestie należy wziąć pod uwagę podczas korzystania z typowych poleceń Redis?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Jak można sprawdzić i przetestować wydajność mojej pamięci podręcznej?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Ważne szczegóły dotyczące wzrostu puli wątków](#important-details-about-threadpool-growth)
* [Włącz serwer GC, aby uzyskać większą przepływność na kliencie przy użyciu StackExchange. Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Zagadnienia dotyczące wydajności dotyczące połączeń](#performance-considerations-around-connections)

### <a name="when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis"></a>Kiedy należy włączyć port bez protokołu TLS/SSL na potrzeby łączenia się z usługą Redis?
Serwer Redis nie obsługuje natywnie protokołu TLS, ale pamięć podręczna platformy Azure dla Redisów. Jeśli łączysz się z usługą Azure cache for Redis, a klient obsługuje protokół TLS, taki jak StackExchange. Redis, należy użyć protokołu TLS.

>[!NOTE]
>Port inny niż TLS jest domyślnie wyłączony dla nowej pamięci podręcznej platformy Azure dla wystąpień Redis. Jeśli klient nie obsługuje protokołu TLS, należy włączyć port bez protokołu TLS, postępując zgodnie z instrukcjami w sekcji [porty dostępu](cache-configure.md#access-ports) w artykule [Konfigurowanie pamięci podręcznej w usłudze Azure cache for Redis](cache-configure.md) .
>
>

Narzędzia Redis, takie jak `redis-cli` nie działają z portem TLS, ale można użyć narzędzia, takiego jak `stunnel` Aby bezpiecznie połączyć narzędzia z portem TLS, postępując zgodnie z instrukcjami wyświetlanymi w blogu [dostawca stanu sesji ASP.NET dla Redis wersji zapoznawczej](https://devblogs.microsoft.com/aspnet/announcing-asp-net-session-state-provider-for-redis-preview-release/) .

Instrukcje dotyczące pobierania narzędzi Redis można znaleźć w sekcji [jak uruchomić polecenia Redis?](cache-development-faq.md#how-can-i-run-redis-commands)

### <a name="what-are-some-production-best-practices"></a>Jakie są najlepsze rozwiązania w zakresie produkcji?
* [StackExchange. Redis — najlepsze rozwiązania](#stackexchangeredis-best-practices)
* [Konfiguracja i koncepcje](#configuration-and-concepts)
* [Testowanie wydajności](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>StackExchange. Redis — najlepsze rozwiązania
* Ustaw `AbortConnect` wartość false, a następnie pozwól, aby usługa ConnectionMultiplexer łączyła się automatycznie. [Zobacz tutaj, aby uzyskać szczegółowe informacje](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* Ponownie Użyj ConnectionMultiplexer — nie twórz nowego dla każdego żądania. `Lazy<ConnectionMultiplexer>` [Opisany tutaj](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) wzorzec jest zalecany.
* Redis najlepiej sprawdza się w przypadku mniejszych wartości, więc Rozważ powiększanie większych danych do wielu kluczy. W [tej dyskusji Redis](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)100 KB jest traktowane jako duże. Przeczytaj [ten artykuł](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) , aby zapoznać się z przykładowym problemem, który może być spowodowany przez duże wartości.
* Skonfiguruj [Ustawienia puli wątków](#important-details-about-threadpool-growth) , aby uniknąć przekroczeń limitu czasu.
* Użyj co najmniej wartości domyślnej connectTimeout wynoszącej 5 sekund. Ten interwał daje StackExchange. Redis wystarczająco dużo czasu, aby ponownie nawiązać połączenie w przypadku Blip sieci.
* Należy pamiętać o kosztach wydajności związanych z różnymi operacjami, które są uruchomione. Na przykład `KEYS` polecenie jest operacją o (n) i należy ją uniknąć. [Lokacja Redis.IO](https://redis.io/commands/) zawiera szczegółowe informacje o złożoności czasu dla każdej obsługiwanej operacji. Kliknij każde polecenie, aby zobaczyć złożoność każdej operacji.

#### <a name="configuration-and-concepts"></a>Konfiguracja i koncepcje
* Użyj warstwy Standardowa lub Premium dla systemów produkcyjnych. Warstwa Podstawowa to system z jednym węzłem bez replikacji danych i bez umowy SLA. Ponadto można użyć przynajmniej pamięci podręcznej C1. Pamięć podręczna C0 jest zwykle używana w przypadku prostych scenariuszy tworzenia i testowania.
* Należy pamiętać, że Redis jest magazynem danych **w pamięci** . Przeczytaj [ten artykuł](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) , aby poznać scenariusze, w których może wystąpić utrata danych.
* Opracowywanie systemu w taki sposób, aby mógł obsługiwać Blips połączeń [z powodu stosowania poprawek i trybu failover](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Testowanie wydajności
* Zacznij od użycia, `redis-benchmark.exe` Aby uzyskać dostęp do możliwej przepływności przed napisaniem własnych testów wydajności. Ponieważ `redis-benchmark` program nie obsługuje protokołu TLS, przed uruchomieniem testu należy [włączyć port bez protokołu tls przez Azure Portal](cache-configure.md#access-ports) . Aby zapoznać się z przykładami, zobacz [Jak sprawdzić i przetestować wydajność mojej pamięci podręcznej?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* Maszyna wirtualna klienta użyta do testowania powinna znajdować się w tym samym regionie co usługa Azure cache for Redis.
* Zalecamy używanie serii maszyn wirtualnych Dv2 dla klienta, ponieważ mają one lepszy sprzęt i powinny dawać najlepsze wyniki.
* Upewnij się, że wybrana maszyna wirtualna klienta ma co najmniej tyle możliwości obliczeniowych i przepustowości jak w przypadku testowanej pamięci podręcznej.
* Jeśli korzystasz z systemu Windows, Włącz opcję wirtualnego skalowania na komputerze klienckim. [Zobacz tutaj, aby uzyskać szczegółowe informacje](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn383582(v=ws.11)).
* Wystąpienia usługi Redis w warstwie Premium mają lepsze opóźnienia sieci i przepływność, ponieważ działają na lepszym sprzęcie dla obu procesorów i sieci.

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Jakie kwestie należy wziąć pod uwagę podczas korzystania z typowych poleceń Redis?

* Należy unikać używania niektórych poleceń Redis, których wykonanie trwa długo, chyba że w pełni zrozumiesz wpływ tych poleceń. Na przykład nie należy uruchamiać poleceń [Keys](https://redis.io/commands/keys) w środowisku produkcyjnym. W zależności od liczby kluczy powracanie może potrwać dużo czasu. Redis to jednowątkowy serwer i przetwarza polecenia pojedynczo. Jeśli masz inne polecenia wydane po KLUCZu, nie zostaną one przetworzone do momentu, gdy Redis przetwarza polecenie KEYS. [Lokacja Redis.IO](https://redis.io/commands/) zawiera szczegółowe informacje o złożoności czasu dla każdej obsługiwanej operacji. Kliknij każde polecenie, aby zobaczyć złożoność każdej operacji.
* Rozmiary kluczy — czy należy używać małych kluczy/wartości, czy też dużych wartości klucz/wartość? Jest to zależne od scenariusza. Jeśli w scenariuszu wymagane są większe klucze, można dostosować wartość parametru ConnectionTimeout, a następnie ponowić próbę i dostosować logikę ponowień. W perspektywie serwera Redis mniejsze wartości zapewniają lepszą wydajność.
* Te zagadnienia nie oznacza, że nie można przechowywać większych wartości w Redis; należy pamiętać o następujących kwestiach. Opóźnienia będą wyższe. Jeśli masz jeden zestaw danych, który jest większy i który jest mniejszy, możesz użyć wielu wystąpień ConnectionMultiplexer, z których każda została skonfigurowana z innym zestawem limitów czasu i ponownych prób, zgodnie z opisem w poprzedniej sekcji [Opcje konfiguracji stackexchange. Redis](cache-development-faq.md#what-do-the-stackexchangeredis-configuration-options-do) .

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Jak można sprawdzić i przetestować wydajność mojej pamięci podręcznej?
* [Włącz diagnostykę pamięci podręcznej](cache-how-to-monitor.md#enable-cache-diagnostics), aby móc [monitorować](cache-how-to-monitor.md) jej kondycję. Możesz wyświetlać metryki w Azure Portal, a także [pobierać i przeglądać](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) je przy użyciu wybranych przez siebie narzędzi.
* redis-benchmark.exe można użyć do załadowania testowego serwera Redis.
* Upewnij się, że klient testowania obciążenia i pamięć podręczna platformy Azure dla Redis znajdują się w tym samym regionie.
* Użyj redis-cli.exe i monitoruj pamięć podręczną za pomocą polecenia INFO.
* Jeśli obciążenie powoduje duże fragmentację pamięci, należy skalować w górę do większego rozmiaru pamięci podręcznej.
* Instrukcje dotyczące pobierania narzędzi Redis można znaleźć w sekcji [jak uruchomić polecenia Redis?](cache-development-faq.md#how-can-i-run-redis-commands)

Poniższe polecenia zapewniają przykład użycia redis-benchmark.exe. Aby uzyskać dokładne wyniki, Uruchom te polecenia z maszyny wirtualnej w tym samym regionie, w którym znajduje się pamięć podręczna.

* Testuj żądania zestawu potokowego przy użyciu ładunku 1K

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Testuj potok żądania GET przy użyciu ładunku o mocy 1K.

>[!NOTE]
> Uruchom najpierw określony test, aby wypełnić pamięć podręczną
>

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

### <a name="important-details-about-threadpool-growth"></a>Ważne szczegóły dotyczące wzrostu puli wątków
W wątkach CLR istnieją dwa typy wątków — wątki "Worker" i "Port zakończenia we/wy" (portu).

* Wątki robocze są używane dla elementów, takich jak przetwarzanie `Task.Run(…)` `ThreadPool.QueueUserWorkItem(…)` metod lub. Te wątki są również używane przez różne składniki w środowisku CLR, gdy działanie musi się zdarzyć w wątku w tle.
* Wątki portu są używane, gdy odbywa się asynchroniczne operacje we/wy, takie jak podczas odczytywania z sieci.

Pula wątków udostępnia nowe wątki procesów roboczych lub wątki zakończenia operacji we/wy na żądanie (bez ograniczania przepustowości), dopóki nie osiągnie ustawienia "minimalne" dla każdego typu wątku. Domyślnie minimalna liczba wątków jest ustawiona na liczbę procesorów w systemie.

Gdy liczba istniejących (zajętych) wątków trafi na "minimalną" liczbę wątków, zostanie ograniczona szybkość, z jaką wprowadza nowe wątki do jednego wątku na 500 milisekund. Zwykle, jeśli system pobiera szereg zadań potrzebnych do wątku portu, będzie on szybko przetwarzał te działania. Jeśli jednak obciążenie serii pracy jest większe niż skonfigurowane ustawienie "minimalne", nastąpi kilka opóźnień podczas przetwarzania części pracy, ponieważ jest ona zależna od jednego z dwóch rzeczy.

* Istniejący wątek będzie bezpłatny, aby przetworzyć prace.
* Żaden z istniejących wątków nie jest bezpłatny dla 500 MS, więc zostanie utworzony nowy wątek.

W zasadzie oznacza to, że gdy liczba zajętych wątków jest większa niż min, prawdopodobnie płacisz 500-ms opóźnienia przed przetworzeniem ruchu sieciowego przez aplikację. Należy również pamiętać, że gdy istniejący wątek pozostaje bezczynny przez dłużej niż 15 sekund (w zależności od tego, co zapamiętasz), zostanie oczyszczony i będzie można powtórzyć ten cykl wzrostu i zmniejszania.

Jeśli zobaczysz przykładowy komunikat o błędzie z StackExchange. Redis (kompilacja 1.0.450 lub nowsza), zobaczysz, że teraz jest drukowana Statystyka puli wątków (zobacz szczegóły dotyczące portu i procesu roboczego).

```
System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
IOCP: (Busy=6,Free=994,Min=4,Max=1000),
WORKER: (Busy=3,Free=997,Min=4,Max=1000)
```

W poprzednim przykładzie można zobaczyć, że dla wątku portu są sześć zajętych wątków, a system jest skonfigurowany tak, aby zezwalał na cztery minimalne wątki. W takim przypadku klient prawdopodobnie widzi opóźnienia 2 500-MS, ponieważ 6 > 4.

Należy pamiętać, że StackExchange. Redis może trafiać limitów czasu, jeśli wzrost liczby wątków portu lub roboczych zostanie ograniczony.

#### <a name="recommendation"></a>Zalecenie

Mając te informacje, zdecydowanie zalecamy, aby klienci ustawili minimalną wartość konfiguracji dla portu i wątków roboczych na wartość większą niż domyślna. Firma Microsoft nie może podać wskazówek o jednym rozmiarze, które dotyczą tej wartości, ponieważ odpowiednia wartość dla jednej aplikacji będzie prawdopodobnie zbyt wysoka lub niska dla innej aplikacji. To ustawienie może również mieć wpływ na wydajność innych części skomplikowanych aplikacji, więc każdy klient musi dostosować to ustawienie do swoich konkretnych potrzeb. Dobrym miejscem początkowym jest 200 lub 300, a następnie testowanie i dostosowywanie zgodnie z wymaganiami.

Jak skonfigurować to ustawienie:

* Zalecamy zmianę tego ustawienia programowo przy użyciu metody [puli wątków. SetMinThreads — (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) w programie `global.asax.cs` . Na przykład:

    ```csharp
    private readonly int minThreads = 200;
    void Application_Start(object sender, EventArgs e)
    {
        // Code that runs on application startup
        AreaRegistration.RegisterAllAreas();
        RouteConfig.RegisterRoutes(RouteTable.Routes);
        BundleConfig.RegisterBundles(BundleTable.Bundles);
        ThreadPool.SetMinThreads(minThreads, minThreads);
    }
    ```

    > [!NOTE]
    > Wartość określona przez tę metodę jest ustawieniem globalnym wpływającym na całą domenę aplikacji. Na przykład jeśli masz maszynę 4-rdzeniową i chcesz ustawić *MinWorkerThreads* i *minIoThreads* na 50 na procesor CPU podczas wykonywania, użyj **puli wątków. SetMinThreads — (200, 200)**.

* Można również określić ustawienie minimalnych wątków przy użyciu [Ustawienia konfiguracji *MinIoThreads* lub *MinWorkerThreads*](/previous-versions/dotnet/netframework-4.0/7w2sway1(v=vs.100)) w obszarze `<processModel>` elementu konfiguracji w programie `Machine.config` , zwykle znajdującego się pod adresem `%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\` . **Ustawienie minimalnej liczby wątków w ten sposób zwykle nie jest zalecane, ponieważ jest to ustawienie na poziomie całego systemu.**

  > [!NOTE]
  > Wartość określona w tym elemencie konfiguracji jest ustawieniem *na rdzeń* . Na przykład jeśli masz maszynę 4-rdzeniową i chcesz, aby ustawienie *minIoThreads* było 200 w czasie wykonywania, użyj `<processModel minIoThreads="50"/>` .
  >

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Włącz serwer GC, aby uzyskać większą przepływność na kliencie przy użyciu StackExchange. Redis
Włączenie serwera GC może zoptymalizować klienta i zapewnić lepszą wydajność i przepływność przy użyciu StackExchange. Redis. Aby uzyskać więcej informacji na temat serwera GC i sposobu jego włączania, zobacz następujące artykuły:

* [Aby włączyć serwer GC](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [Podstawy dotyczące odzyskiwania pamięci](/dotnet/standard/garbage-collection/fundamentals)
* [Odzyskiwanie pamięci i wydajność](/dotnet/standard/garbage-collection/performance)

### <a name="performance-considerations-around-connections"></a>Zagadnienia dotyczące wydajności dotyczące połączeń

Każda warstwa cenowa ma różne limity dla połączeń klientów, pamięci i przepustowości. Chociaż każdy rozmiar pamięci podręcznej pozwala *na* określoną liczbę połączeń, każde połączenie z usługą Redis ma powiązane z nim obciążenie. Przykładem takiego obciążenia będzie użycie procesora CPU i pamięci w wyniku szyfrowania TLS/SSL. Maksymalny limit połączeń dla danego rozmiaru pamięci podręcznej zakłada, że pamięć podręczna została załadowana w sposób jasny. Jeśli obciążenie związane z naliczeniem połączenia *i* obciążenie z operacji klienta przekroczy pojemność systemu, pamięć podręczna może mieć problemy z pojemnością, nawet jeśli nie przekroczono limitu połączenia dla bieżącego rozmiaru pamięci podręcznej.

Aby uzyskać więcej informacji na temat różnych limitów połączeń dla każdej warstwy, zobacz [Azure cache for Redis — Cennik](https://azure.microsoft.com/pricing/details/cache/). Aby uzyskać więcej informacji o połączeniach i innych konfiguracjach domyślnych, zobacz [domyślną konfigurację serwera Redis](cache-configure.md#default-redis-server-configuration).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usłudze [Azure cache for Redis — często zadawane pytania](cache-faq.md).