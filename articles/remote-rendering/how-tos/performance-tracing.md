---
title: Tworzenie śladów wydajności po stronie klienta
description: Najlepsze rozwiązania dotyczące profilowania wydajności po stronie klienta przy użyciu platformy WPF
author: florianborn71
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.openlocfilehash: 2a10558e76a6e9af7c7571dc4ba3d063ce3e2286
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84021164"
---
# <a name="create-client-side-performance-traces"></a>Tworzenie śladów wydajności po stronie klienta

Istnieje wiele powodów, dla których wydajność renderowania zdalnego na platformie Azure może być nieodpowiednia. Oprócz czystej wydajności renderowania na serwerze w chmurze, szczególnie jakość połączenia sieciowego ma znaczny wpływ na środowisko pracy. Aby profilować wydajność serwera, zapoznaj się z rozdziałem [zapytania wydajności po stronie serwera](../overview/features/performance-queries.md).

Ten rozdział koncentruje się na sposobach identyfikowania potencjalnych wąskich gardeł po stronie klienta w programie *:::no-loc text="performance traces":::* .

## <a name="getting-started"></a>Wprowadzenie

Jeśli :::no-loc text="performance tracing"::: dopiero zaczynasz korzystać z funkcji systemu Windows, w tej sekcji przedstawiono najbardziej podstawowe warunki i aplikacje umożliwiające rozpoczęcie pracy.

### <a name="installation"></a>Instalacja

Aplikacje używane do śledzenia z ARR są narzędziami ogólnego przeznaczenia, które mogą być używane do tworzenia wszystkich okien. Są one dostępne za pomocą [zestawu narzędzi wydajności systemu Windows](https://docs.microsoft.com/windows-hardware/test/wpt/). Aby uzyskać ten zestaw narzędzi, Pobierz [zestaw do oceny i wdrażania systemu Windows](https://docs.microsoft.com/windows-hardware/get-started/adk-install).

### <a name="terminology"></a>Terminologia

Podczas wyszukiwania informacji na temat śladów wydajności będzie można w sposób nieunikniony przedziałem czasu. Najważniejsze są następujące:

* `ETW`
* `ETL`
* `WPR`
* `WPA`

Na potrzeby **funkcji ETW** jest przygotowana dla usługi [ **E** **t** **W**indows](https://docs.microsoft.com/windows/win32/etw/about-event-tracing). Jest to po prostu Przełożonej nazwy dla wydajnej funkcji śledzenia poziomu jądra, która jest wbudowana w system Windows. Jest to tzw. śledzenie *zdarzeń* , ponieważ aplikacje obsługujące ETW będą emitować zdarzenia do rejestrowania akcji, które mogą pomóc w śledzeniu problemów z wydajnością. Domyślnie system operacyjny emituje zdarzenia dotyczące takich operacji jak dostęp do dysku, przełączenia zadań. Aplikacje takie jak ARR dodatkowo emitują zdarzenia niestandardowe, na przykład w przypadku gubienia ramek, opóźnienia sieci itp.

**W przypadku**usługi **ETL** odogginga na rasę **T** **1**. Oznacza to po prostu, że ślad został zebrany (zarejestrowany) i dlatego jest zazwyczaj używany jako rozszerzenie pliku dla plików, które przechowują dane śledzenia. W takim przypadku po wykonaniu śledzenia zazwyczaj będzie znajdował się \* plik. etl.

**WP** ma wartość [ **w**indows **P**Eksplorator **R**ecorder](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder) i jest nazwą aplikacji, która uruchamia i przerywa nagrywanie śladów zdarzeń. Żądanie WP pobiera plik profilu ( \* . wprp), który konfiguruje dokładne zdarzenia do zarejestrowania. Taki `wprp` plik jest dostarczany z zestawem SDK arr. Gdy wykonujesz ślady na komputerze stacjonarnym, możesz uruchomić żądanie WP bezpośrednio. Podczas wykonywania operacji śledzenia na serwerze HoloLens zazwyczaj zamiast tego można przejść przez interfejs sieci Web.

**WPA** oznacza w [ **W**indows **P**Eksplorator nalyzer **A**](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer) i jest nazwą aplikacji graficznego interfejsu użytkownika, która jest używana do otwierania \* plików ETL i przechodzenia przez dane w celu zidentyfikowania problemów z wydajnością. WPA pozwala sortować dane według różnych kryteriów, wyświetlać dane na kilka sposobów, DIG do szczegółów i skorelować informacje.

Śledzenie ETL można tworzyć na dowolnym urządzeniu z systemem Windows (na komputerze lokalnym, serwerze HoloLens, w chmurze itp.), które są zwykle zapisywane na dysku i analizowane przy użyciu protokołu WPA na komputerze stacjonarnym. Pliki ETL mogą być wysyłane do innych deweloperów, aby mieć do nich wygląd. Należy pamiętać, że informacje poufne, takie jak ścieżki plików i adresy IP, mogą być przechwytywane w śladach ETL. Funkcji ETW można używać na dwa sposoby: do rejestrowania śladów lub analizowania śladów. Rejestrowanie śledzenia jest proste i wymaga minimalnej konfiguracji. Analizowanie śladów z drugiej strony wymaga znośnego zrozumienie zarówno narzędzia WPA, jak i badanego problemu. Poniżej przedstawiono materiał ogólny do uczenia WPA, a także wskazówki dotyczące sposobu interpretacji śladów specyficznych dla ARR.

## <a name="recording-a-trace-on-a-local-pc"></a>Rejestrowanie śledzenia na komputerze lokalnym

W celu zidentyfikowania problemów z wydajnością klasy ARR należy wykonać śledzenie bezpośrednio na serwerze HoloLens, ponieważ jest to jedyny sposób, aby uzyskać migawkę prawdziwych cech wydajności. Jeśli jednak użytkownik chce wykonać ślad bez ograniczeń wydajności urządzeń HoloLens lub chcieć dowiedzieć się, jak korzystać z protokołu WPA i nie musi mieć realistycznego śledzenia, Oto jak to zrobić.

### <a name="wpr-configuration"></a>ŻĄDANIE konfiguracji

1. Uruchom [:::no-loc text="Windows Performance Recorder":::](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-recorder) z *menu Start*.
1. Rozwiń **więcej opcji**
1. Kliknij pozycję **Dodaj profile...**
1. Wybierz plik *AzureRemoteRenderingNetworkProfiling. wprp*. Ten plik można znaleźć w zestawie SDK ARR w obszarze *Narzędzia/ETLProfiles*.
   Profil będzie teraz wyświetlany w oknie żądanie w obszarze *miary niestandardowe*. Upewnij się, że jest jedynym włączonym profilem.
1. Rozwiń węzeł *pierwszego poziomu Klasyfikacja*:
    * Jeśli wszystko, co chcesz zrobić, przechwytuje szybkie śledzenie zdarzeń sieci o ARR, **Wyłącz** tę opcję.
    * Jeśli konieczne jest skorelowanie zdarzeń sieci z identyfikatorem ARR z innymi charakterystykami systemu, takimi jak użycie procesora CPU lub pamięci, **Włącz** tę opcję.
    * Jeśli włączysz tę opcję, śledzenie najprawdopodobniej będzie mieć wiele gigabajtów w rozmiarze i trwa długi czas na zapisanie i otwarcie w protokole WPA.

Następnie konfiguracja WP powinna wyglądać następująco:

![ŻĄDANIE konfiguracji](./media/wpr.png)

### <a name="recording"></a>Nagrywanie

Kliknij przycisk **Rozpocznij** , aby rozpocząć rejestrowanie śledzenia. Nagrywanie można uruchomić i zatrzymać w dowolnym momencie; przed wykonaniem tej czynności nie trzeba zamykać aplikacji. Jak widać, nie musisz określać aplikacji do śledzenia, ponieważ ETW zawsze rejestruje ślad dla całego systemu. `wprp`Plik określa typy zdarzeń do rejestrowania.

Kliknij przycisk **Zapisz** , aby zatrzymać nagrywanie i określić miejsce przechowywania pliku ETL.

Masz teraz plik ETL, który można otworzyć bezpośrednio w WPA lub wysłać do kogoś innego.

## <a name="recording-a-trace-on-a-hololens"></a>Rejestrowanie śledzenia na serwerze HoloLens

Aby zarejestrować ślad w urządzeniu HoloLens, należy przeprowadzić rozruch urządzenia i wprowadzić jego adres IP do przeglądarki w celu otworzenia *portalu urządzenia*.

![Portal urządzenia](./media/wpr-hl.png)

1. Po lewej stronie przejdź do *> wydajność śledzenie wydajności*.
1. Wybieranie **profilów niestandardowych**
1. Polecenie **:::no-loc text="Browse...":::**
1. Wybierz plik *AzureRemoteRenderingNetworkProfiling. wprp*. Ten plik można znaleźć w zestawie SDK ARR w obszarze *Narzędzia/ETLProfiles*.
1. Kliknij przycisk **Rozpocznij śledzenie**
1. Urządzenie HoloLens rejestruje teraz śledzenie. Upewnij się, że Wyzwalasz problemy z wydajnością, które chcesz zbadać. Następnie kliknij przycisk **Zatrzymaj śledzenie**.
1. Ślad zostanie wyświetlony w dolnej części strony sieci Web. Kliknij ikonę dysku znajdującą się po prawej stronie, aby pobrać plik ETL.

Masz teraz plik ETL, który można otworzyć bezpośrednio w WPA lub wysłać do kogoś innego.

## <a name="analyzing-traces-with-wpa"></a>Analizowanie śladów przy użyciu protokołu WPA

### <a name="wpa-basics"></a>Podstawowe informacje o WPA

Analizator wydajności systemu Windows jest standardowym narzędziem do otwierania plików ETL i inspekcji śladów. Wyjaśnienie, w jaki sposób program WPA działa poza zakresem tego artykułu. Aby rozpocząć, zapoznaj się z następującymi zasobami:

* Obejrzyj [wstępne wideo](https://docs.microsoft.com/windows-hardware/test/wpt/windows-performance-analyzer) , aby zapoznać się z pierwszym omówieniem.
* Sam protokół WPA zawiera kartę *wprowadzenie* , która objaśnia typowe kroki. Zapoznaj się z dostępnymi tematami. W szczególności w obszarze "Wyświetlanie danych" przedstawiono krótkie instrukcje tworzenia grafów dla określonych danych.
* [W tej witrynie sieci Web](https://randomascii.wordpress.com/2015/09/24/etw-central/)znajdują się doskonałe informacje, ale nie wszystkie są istotne dla początkujących.

### <a name="graphing-data"></a>Tworzenie grafów danych

Aby zacząć korzystać z śledzenia ARR, warto znać następujące elementy.

![Wykres wydajności](./media/wpa-graph.png)

Na powyższym obrazie przedstawiono tabelę danych śledzenia i reprezentację grafu tych samych danych.

W tabeli u dołu Zanotuj żółty (złot) pasek i niebieski pasek. Możesz przeciągnąć te słupki i umieścić je w dowolnym miejscu.

Wszystkie **kolumny po lewej stronie żółtego paska** są interpretowane jako **klucze**. Klucze są używane do struktury drzewa w górnym lewym oknie. Oto dwie kolumny *klucza* , "Nazwa dostawcy" i "Nazwa zadania". W związku z tym struktura drzewa w górnym lewym oknie ma dwa poziomy głębokości. Jeśli zmienisz kolejność kolumn lub dodasz lub usuniesz kolumny z obszaru klucza, struktura w widoku drzewa zmienia się.

**Kolumny z prawej strony niebieskiego paska** są używane na potrzeby **wyświetlania wykresu** w prawym górnym oknie. Większość czasu jest używana tylko w pierwszej kolumnie, ale niektóre tryby grafu wymagają wielu kolumn danych. Aby wykresy liniowe działały, należy ustawić *tryb agregacji* dla tej kolumny. Użyj wartości "AVG" lub "Max". Tryb agregacji służy do określania wartości grafu w danym pikselu, gdy piksel obejmuje zakres z wieloma zdarzeniami. Można to zaobserwować przez ustawienie agregacji na wartość "Sum", a następnie powiększanie i zmniejszenie.

Kolumny w środku nie mają specjalnego znaczenia.

![Widok zdarzeń](./media/wpa-event-view.png)

W *Edytorze widoku zdarzeń ogólnych* można skonfigurować wszystkie kolumny do wyświetlenia, tryb agregacji, sortowanie i kolumny, które są używane jako klucze lub dla wykresów. W powyższym przykładzie **pole 2** jest włączone, a pole 3-6 jest wyłączone. Pole 2 to zazwyczaj pierwsze pole *danych niestandardowych* zdarzenia ETW i w ten sposób dla zdarzeń o genotypie "FrameStatistics", które reprezentują pewną wartość opóźnienia sieci. Włącz inne kolumny "Field", aby zobaczyć dalsze wartości tego zdarzenia.

### <a name="presets"></a>Zestawy

Aby prawidłowo przeanalizować ślad, należy ustalić własny przepływ pracy i preferowany sposób wyświetlania danych. Jednak aby szybko zapoznać się ze zdarzeniami specyficznymi dla ARR, dołączymy Profil platformy ochrony oprogramowania systemu Windows i pliki ustawień predefiniowanych w folderze *Tools/ETLProfiles*. Aby załadować pełny profil, wybierz pozycję *profile > Zastosuj...* z paska menu WPA lub Otwórz panel *Moje ustawienia wstępne* (*okno > moje ustawienia wstępne*) i wybierz pozycję *Importuj*. Dawniej skonfiguruje kompletną konfigurację protokołu WPA, jak na poniższej ilustracji. Ta ostatnia spowoduje, że tylko ustawienia dla różnych dostępnych konfiguracji widoku i umożliwią szybkie otwieranie widoku w celu wyszukania określonego fragmentu danych zdarzeń ARR.

![Zestawy](./media/wpa-arr-trace.png)

Na powyższym obrazie przedstawiono widoki różnych zdarzeń specyficznych dla ARR oraz widok całkowitego użycia procesora CPU.

## <a name="next-steps"></a>Następne kroki

* [Zapytania wydajności po stronie serwera](../overview/features/performance-queries.md)
