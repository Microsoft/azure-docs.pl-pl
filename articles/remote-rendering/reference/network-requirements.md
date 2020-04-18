---
title: Wymagania dotyczące sieci
description: Wymagania sieciowe i najlepsze rozwiązania sieciowe dla optymalnego środowiska
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: reference
ms.openlocfilehash: 9bb8818d2bb91c14eafc272774e0b633c51e58e1
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617881"
---
# <a name="network-requirements"></a>Wymagania dotyczące sieci

Stabilne połączenie sieciowe o małym opóźnieniu z centrum danych platformy Azure ma kluczowe znaczenie dla dobrego środowiska użytkownika w renderowaniu zdalnym platformy Azure. Złe warunki w sieci mogą spowodować przerwanie połączeń, niestabilne, zdenerwowanie lub "skoki" hologramów i zauważalne opóźnienie podczas aktualizowania wykresu sceny po stronie serwera.

## <a name="guidelines-for-network-connectivity"></a>Wskazówki dotyczące łączności sieciowej

Dokładne wymagania sieciowe zależą od konkretnego przypadku użycia, takiego jak liczba i częstotliwość modyfikacji wykresu sceny zdalnej, a także złożoność renderowanego widoku, ale istnieje szereg wskazówek, aby zapewnić, że doświadczenie jest tak dobre, jak to możliwe:

* Łączność z Internetem musi obsługiwać co najmniej **50 Mb/s w dół i** **10 Mb/s w górę** konsekwentnie dla pojedynczej sesji użytkownika renderowania zdalnego platformy Azure, przy założeniu, że nie ma konkurencyjnego ruchu w sieci. Zalecamy wyższe stawki dla lepszych doświadczeń. Przy większej liczbie użytkowników w tej samej sieci te wymagania są odpowiednio skalowane w górę.
* Korzystanie z **pasma Wi-Fi 5 GHz** zwykle daje lepsze wyniki niż pasmo Wi-Fi 2,4 GHz, choć oba powinny działać.
* Jeśli w pobliżu znajdują się inne sieci Wi-Fi, unikaj używania kanałów Wi-Fi używanych przez te inne sieci. Za pomocą narzędzi do skanowania sieci, takich jak [WifiInfoView,](https://www.nirsoft.net/utils/wifi_information_view.html) można sprawdzić, czy kanały używane przez sieć Wi-Fi są wolne od konkurencyjnego ruchu.
* Należy **bezwzględnie unikać używania repeaterów Wi-Fi** lub przekazywania lan-over-powerline.
* **Unikaj konkurencyjnego ruchu o dużej przepustowości** , takiego jak przesyłanie strumieniowe wideo lub gier, w tej samej sieci Wi-Fi.
* Posiadanie **dobrej siły sygnału Wi-Fi** jest niezbędne. Jeśli to możliwe, trzymaj się blisko punktu dostępu Wi-Fi i unikaj przeszkód między urządzeniem klienckim a punktami dostępu.
* Upewnij się, że zawsze łączysz się z **najbliższym centrum danych platformy Azure** dla swojego [regionu](regions.md). Im bliżej centrum danych, tym mniejsze opóźnienie sieci, co ma ogromny wpływ na stabilność hologramu.

> [!NOTE]
> Przepustowość niższego rzędu jest najczęściej zużywana przez strumień wideo, który z kolei jest podzielony między informacje o kolorze i głębi (zarówno 60 Hz, stereo.

## <a name="network-performance-tests"></a>Testy wydajności sieci

Jeśli chcesz uzyskać wstępne zrozumienie, czy jakość łączności sieciowej jest wystarczająca do uruchomienia renderowania zdalnego platformy Azure, istnieją istniejące narzędzia online, których można użyć. Zdecydowanie zaleca się uruchamianie tych narzędzi online z rozsądnie wydajnego laptopa podłączonego do tej samej sieci Wi-Fi, co urządzenie, na które planujesz uruchomić aplikację klienta renderowania zdalnego platformy Azure. Wyniki uzyskane z uruchomienia testów na telefonie komórkowym lub HoloLens2 są zwykle mniej przydatne, ponieważ okazały się wykazywać znaczne różnice w urządzeniach o niskim pozycjowym punkcie końcowym. Lokalizacja, w której można umieścić komputer przenośny powinien znajdować się mniej więcej w tym samym miejscu, w którym można oczekiwać, aby użyć urządzenia, które uruchamia aplikację klienta renderowania zdalnego platformy Azure.

Oto kilka prostych kroków, aby szybko przetestować łączność sieciową:

1. **Uruchom narzędzie do testowania sieci, takie jak www.speedtest.net, aby uzyskać dane dotyczące ogólnego opóźnienia i przepustowości połączenia sieciowego w górę/w dół.**
Wybierz serwer najbliżej Ciebie i uruchom test. Chociaż serwer nie będzie centrum danych platformy Azure, z które będzie się łączyć zdalne renderowanie platformy Azure, wynikowe dane są nadal przydatne do zrozumienia wydajności połączenia internetowego i sieci Wi-Fi.
   * **Minimalne wymagania dotyczące** zdalnego renderowania platformy Azure: około 40 Mb/s w dół i 5 Mb/s w górę.
   * **Zalecane** dla renderowania zdalnego platformy Azure: około 100 Mb/s w dół i 10 Mb/s w górę.
Zalecamy wielokrotne uruchamianie testu i podejmowanie najgorszych wyników.
1. **Użyj narzędzia, takiego jak www.azurespeed.com, które mierzy opóźnienia w centrach danych platformy Azure.** Wybierz centrum danych platformy Azure obsługiwane przez zdalne renderowanie platformy Azure, które jest najbliżej Ciebie (zobacz [obsługiwane regiony)](regions.md)i uruchom **test opóźnienia**. Jeśli istnieją różnice w liczbach, które widzisz, daj wyniki trochę czasu na stabilizację.
   * **Minimalne wymagania dotyczące** zdalnego renderowania platformy Azure: opóźnienie powinno być stale mniejsze niż 100 ms.
   * **Zalecane** dla renderowania zdalnego platformy Azure: opóźnienie powinno być stale mniejsze niż 70 ms.

Podczas gdy małe opóźnienia nie jest gwarancją, że zdalne renderowanie platformy Azure będzie działać dobrze w sieci, zwykle widzieliśmy go wykonać poprawnie w sytuacjach, gdy te testy pomyślnie.
Jeśli podczas uruchamiania renderowania zdalnego platformy Azure występują artefakty, takie jak niestabilne, zdenerwowanie lub przeskakiwanie hologramów, zapoznaj się z [przewodnikiem rozwiązywania problemów](../resources/troubleshoot.md).

## <a name="next-steps"></a>Następne kroki

* [Szybki start: renderowanie modelu za pomocą unity](../quickstarts/render-model.md)
