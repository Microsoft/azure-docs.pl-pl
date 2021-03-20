---
title: Profilowanie aplikacji produkcyjnych na platformie Azure za pomocą Application Insights Profiler
description: Zidentyfikuj gorącą ścieżkę w kodzie serwera sieci Web za pomocą profilera niskiego poziomu.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 0d3074d58560df5cb5bd6bdc2c0437a4be828918
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86499396"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Profilowanie aplikacji produkcyjnych na platformie Azure za pomocą Application Insights
## <a name="enable-application-insights-profiler-for-your-application"></a>Włączanie Application Insights Profiler dla aplikacji

Usługa Azure Application Insights Profiler udostępnia dane śledzenia wydajności dla aplikacji działających w środowisku produkcyjnym na platformie Azure. Profiler automatycznie przechwytuje dane w skali bez negatywnego wpływu na użytkowników. Profiler ułatwia zidentyfikowanie ścieżki kodu "gorąca", która zajmuje najdłuższy czas, gdy obsługuje określone żądanie sieci Web. 

Profiler współpracuje z aplikacjami .NET, które są wdrożone w następujących usługach platformy Azure. Szczegółowe instrukcje dotyczące włączania profilera dla każdego typu usługi znajdują się w poniższych linkach.

* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [usług Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines i zestawy skalowania maszyn wirtualnych](profiler-vm.md?toc=/azure/azure-monitor/toc.json)
* [**Wersja zapoznawcza** ASP.NET Core Web Apps systemu Linux platformy Azure](profiler-aspnetcore-linux.md?toc=/azure/azure-monitor/toc.json) 

Jeśli włączono Profiler, ale nie widzisz śladów, zapoznaj się [z naszym przewodnikiem rozwiązywania problemów](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

## <a name="view-profiler-data"></a>Wyświetl dane profilera

Aby Profiler mógł przekazać ślady, aplikacja musi aktywnie obsługiwać żądania. Jeśli wykonujesz eksperyment, możesz generować żądania do aplikacji sieci Web za pomocą [Application Insights testowania wydajnościowego](/vsts/load-test/app-service-web-app-performance-test). Jeśli wcześniej włączono Profiler, możesz uruchomić krótki test obciążenia. Gdy test obciążenia jest uruchomiony, wybierz przycisk **profil teraz** w [okienku **Ustawienia profilera**](profiler-settings.md). Gdy profiler jest uruchomiony, profiluje losowo około raz na godzinę i przez dwa minuty. Jeśli aplikacja obsługuje stały strumień żądań, operacje przekazywania profilera są śladami co godzinę.

Gdy aplikacja odbierze ruch, a program Profiler miał czas na przekazanie śladów, należy wyświetlić dane śledzenia. Ten proces może potrwać od 5 do 10 minut. Aby wyświetlić ślady, w okienku **wydajność** wybierz pozycję **Przejmij akcje**, a następnie wybierz przycisk **ślady profilera** .

![Dane śledzenia profilera Application Insights w okienku wydajności][performance-blade]

Wybierz próbkę, aby wyświetlić podział czasu poświęcony na wykonanie żądania.

![Eksplorator śledzenia Application Insights][trace-explorer]

Eksplorator śledzenia wyświetla następujące informacje:

* **Pokaż ścieżkę gorącą**: otwiera największy węzeł liścia lub co najmniej coś zamknięcia. W większości przypadków ten węzeł zbliża się do wąskiego gardła wydajności.
* **Etykieta**: nazwa funkcji lub zdarzenia. W drzewie jest wyświetlany mieszany kod i zdarzenia, które wystąpiły, takie jak zdarzenia SQL i HTTP. Zdarzenie Top reprezentuje ogólny czas trwania żądania.
* **Upłynęło**: przedział czasu między rozpoczęciem operacji a końcem operacji.
* **Kiedy**: czas, kiedy funkcja lub zdarzenie działały w odniesieniu do innych funkcji.

## <a name="how-to-read-performance-data"></a>Jak odczytać dane dotyczące wydajności

Program Microsoft Service Profiler używa kombinacji metod próbkowania i instrumentacji do analizowania wydajności aplikacji. Gdy Szczegółowa kolekcja jest w toku, profiler usługi próbuje wskaźnikiem instrukcji każdego procesora komputera co milisekundy. Każdy przykład przechwytuje kompletny stos wywołań wątku, który jest aktualnie wykonywany. Zawiera szczegółowe informacje o tym, co robi ten wątek, zarówno na wysokim poziomie, jak i na niskim poziomie abstrakcji. Profiler usługi zbiera również inne zdarzenia do śledzenia korelacji i przyczyn aktywności, w tym zdarzeń przełączania kontekstu, zdarzeń biblioteki zadań równoległych (TPL) i zdarzenia puli wątków.

Stos wywołań, który jest wyświetlany w widoku oś czasu jest wynikiem próbkowania i instrumentacji. Ponieważ każdy przykład przechwytuje cały stos wywołań wątku, zawiera kod z Microsoft .NET Framework oraz z innych struktur, do których się odwołuje.

### <a name="object-allocation-clrjit_new-or-clrjit_newarr1"></a><a id="jitnewobj"></a>Alokacja obiektu (CLR! Kompilator JIT \_ New lub CLR! \_NEWARR1 JIT)

**środowisko CLR! \_New** i CLR języka JIT **\_NEWARR1 JIT** to funkcje pomocnika w .NET Framework przydzielania pamięci z sterty zarządzanej. **środowisko CLR! \_Nowy JIT** jest wywoływany po przydzieleniu obiektu. **środowisko CLR! \_NEWARR1 JIT** jest wywoływana, gdy przydzielono tablicę obiektów. Te dwie funkcje są zwykle szybkie i stosunkowo małe. Jeśli **CLR! Kompilator JIT \_ New** lub **CLR! \_NEWARR1 JIT** zajmuje dużo czasu na osi czasu, kod może przydzielić wiele obiektów i zużywać znaczną ilość pamięci.

### <a name="loading-code-clrtheprestub"></a><a id="theprestub"></a>Ładowanie kodu (CLR! ThePreStub)

**środowisko CLR! ThePreStub** jest funkcją pomocnika w .NET Framework, która przygotowuje kod do wykonania po raz pierwszy. To wykonanie zwykle obejmuje, ale nie jest ograniczone do, kompilacja just-in-Time (JIT). Dla każdej metody C#, **CLR! ThePreStub** powinien być wywoływany co najwyżej raz podczas procesu.

Jeśli **CLR! ThePreStub** zajmuje dużo czasu na żądanie, żądanie jest pierwszym z nich, aby wykonać tę metodę. Czas ładowania pierwszej metody przez środowisko uruchomieniowe .NET Framework jest znaczący. Rozważ użycie procesu rozgrzewania, który wykonuje tę część kodu, zanim użytkownicy uzyskają do niego dostęp, lub Rozważ uruchomienie generatora obrazu natywnego (ngen.exe) w zestawach.

### <a name="lock-contention-clrjitutil_moncontention-or-clrjitutil_monenterworker"></a><a id="lockcontention"></a>Zablokuj rywalizację (CLR! JITutil \_ MonContention lub CLR! JITutil \_ MonEnterWorker)

**środowisko CLR! JITutil \_ MonContention** lub **CLR! JITutil \_ MonEnterWorker** wskazuje, że bieżący wątek oczekuje na zwolnienie blokady. Ten tekst jest często wyświetlany podczas wykonywania instrukcji **Lock** języka C#, wywoływanie metody **monitor. Enter** lub Invoke metody z atrybutem **MethodImplOptions. Synchronized** . Rywalizacja o blokadę zwykle występuje, gdy wątek _a_ uzyskuje blokadę, a wątek _B_ próbuje uzyskać tę samą blokadę przed _jej wydaniem przez wątek_ .

### <a name="loading-code-cold"></a><a id="ngencold"></a>Ładowanie kodu ([ZIMNy])

Jeśli nazwa metody zawiera **[zimn]**, np. **mscorlib.ni! [ ZIMNy] system. odbicie. CustomAttribute. IsDefined**, .NET Framework środowisko uruchomieniowe wykonuje kod po raz pierwszy, który nie jest zoptymalizowany pod kątem [optymalizacji opartej na profilach](/cpp/build/profile-guided-optimizations). Dla każdej metody powinna być wyświetlana najwyżej raz podczas procesu.

Jeśli ładowanie kodu zajmuje dużo czasu na żądanie, żądanie jest pierwszym z nich do wykonania niezoptymalizowanej części metody. Rozważ użycie procesu rozgrzewania, który wykonuje tę część kodu, zanim użytkownicy uzyskają do niej dostęp.

### <a name="send-http-request"></a><a id="httpclientsend"></a>Wyślij żądanie HTTP

Metody, takie jak **HttpClient. Send** wskazują, że kod oczekuje na ukończenie żądania HTTP.

### <a name="database-operation"></a><a id="sqlcommand"></a>Operacja bazy danych

Metody takie jak **SqlCommand.Exeurocze** wskazują, że kod oczekuje na zakończenie operacji bazy danych.

### <a name="waiting-await_time"></a><a id="await"></a>Oczekiwanie ( \_ czas oczekiwania)

**Oczekiwanie \_ CZAS** wskazuje, że kod oczekuje na zakończenie innego zadania. To opóźnienie zwykle odbywa się przy użyciu instrukcji języka C# **AWAIT** . Gdy kod wykonuje polecenie C# **AWAIT**, wątek cofa i zwraca kontrolę do puli wątków i nie istnieje wątek, który jest zablokowany oczekujący **na zakończenie** oczekiwania. Jednak logicznie wątek, **który został oczekiwał, jest zablokowany** , i oczekuje na zakończenie operacji. Instrukcja **AWAIT \_ Time** wskazuje zablokowany czas oczekiwania na zakończenie zadania.

### <a name="blocked-time"></a><a id="block"></a>Czas blokowania

**BLOCKED_TIME** wskazuje, że kod oczekuje na dostępność innego zasobu. Na przykład może być oczekiwanie na obiekt synchronizacji, do udostępnienia wątku lub do zakończenia żądania.

### <a name="unmanaged-async"></a>Niezarządzany asynchroniczny

Program .NET Framework emituje zdarzenia ETW i przekazuje identyfikatory aktywności między wątkami, aby wywołania asynchroniczne mogły być śledzone między wątkami. Kod niezarządzany (kod natywny) i niektóre starsze style kodu asynchronicznego nie mają tych zdarzeń i identyfikatorów aktywności, dlatego Profiler nie może określić wątku i jakie funkcje działają w wątku. W stosie wywołań jest oznaczona jako "niezarządzana asynchroniczna". Jeśli pobierasz plik ETW, możesz użyć [Narzędzia PerfView](https://github.com/Microsoft/perfview/blob/master/documentation/Downloading.md)  , aby uzyskać więcej informacji o tym, co się dzieje.

### <a name="cpu-time"></a><a id="cpu"></a>Czas procesora CPU

Procesor CPU jest zajęty wykonywaniem instrukcji.

### <a name="disk-time"></a><a id="disk"></a>Czas dysku

Aplikacja wykonuje operacje na dyskach.

### <a name="network-time"></a><a id="network"></a>Czas sieci

Aplikacja wykonuje operacje sieciowe.

### <a name="when-column"></a><a id="when"></a>Kolumna, gdy

Kolumna **when** to Wizualizacja, w jaki sposób zbierane próbki pobierane dla węzła różnią się w zależności od czasu. Łączny zakres żądania jest podzielony na 32 przedziałów czasu. Próbki włączne dla tego węzła są gromadzone w tych zasobnikach 32. Każdy zasobnik jest reprezentowany jako pasek. Wysokość paska reprezentuje przeskalowana wartość. W przypadku węzłów, które są oznaczone **CPU_TIME** lub **BLOCKED_TIME** lub jeśli istnieje oczywista relacja do zużywania zasobów (na przykład procesora, dysku lub wątku), pasek reprezentuje użycie jednego z zasobów w zasobniku. W przypadku tych metryk możliwe jest uzyskanie wartości większej niż 100 procent przez zużywanie wielu zasobów. Na przykład w przypadku korzystania z średnio dwóch procesorów CPU w interwale występuje 200 procent.

## <a name="limitations"></a>Ograniczenia

Domyślny okres przechowywania danych wynosi pięć dni. Maksymalne dane, które są pozyskiwane dziennie, to 10 GB.

Za korzystanie z usługi profilera nie są naliczane opłaty. Aby można było z niego korzystać, aplikacja sieci Web musi być hostowana w co najmniej warstwie Podstawowa Web Apps funkcji Azure App Service.

## <a name="overhead-and-sampling-algorithm"></a>Algorytm narzutu i próbkowania

Profiler losowo uruchamia dwie minuty co godzinę na każdej maszynie wirtualnej, która hostuje aplikację z włączonym profilerem do przechwytywania śladów. Gdy profiler jest uruchomiony, dodaje od 5 do 15 procent obciążenia procesora CPU do serwera.

## <a name="next-steps"></a>Następne kroki
Włącz Application Insights Profiler dla aplikacji platformy Azure. Zobacz również:
* [Usługi aplikacji](profiler.md?toc=/azure/azure-monitor/toc.json)
* [usług Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines i zestawy skalowania maszyn wirtualnych](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/profiler-overview/trace-explorer.png
